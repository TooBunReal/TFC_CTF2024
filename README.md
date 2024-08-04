# TFC_CTF2024

- Lâu ngày không chơi nên gà quá, solve được vài câu mong ae hok chê =))

## 

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
