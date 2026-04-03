Brooklyn Nine Nine
---

<img width="1455" height="837" alt="image" src="https://github.com/user-attachments/assets/89a0da8d-afd8-492f-9ca2-1d7116817bc6" />

Source Page :
---
<img width="484" height="210" alt="image" src="https://github.com/user-attachments/assets/34efb74e-60ec-4931-b9f8-adf03d2f15af" />

There is steganography !
---
<img width="698" height="783" alt="image" src="https://github.com/user-attachments/assets/32fdab7f-35ef-4c4d-ad89-27129892b1bc" />

anonymous login in ftp is allowed

---

Analyzing the image with the commandlien: 
```
stegcracker image.png
```

the result :
---
<img width="705" height="299" alt="image" src="https://github.com/user-attachments/assets/3c42bed6-4da6-4e10-92b8-91613043e7e4" />

so -> admin is the password used to lock data in the image 

and then we have a another file created 
---
<img width="705" height="127" alt="image" src="https://github.com/user-attachments/assets/cd208776-139a-4a85-a079-d1f651a2e4cf" />

and when we cat it 
---
<img width="705" height="127" alt="image" src="https://github.com/user-attachments/assets/8e6724b6-0c87-4744-a1b4-d032fa5f7ff0" />

---

In the other side we try to connect with ftp :
```
lftp ftp:/10.10.10.10
```
<img width="705" height="224" alt="image" src="https://github.com/user-attachments/assets/b21b8a66-1900-4250-b068-0eebd0fdd240" />
---
Bingo, we have holt's password :
```
ssh holt@10.130.157.106   
```
<img width="705" height="298" alt="image" src="https://github.com/user-attachments/assets/94f5e7b3-9925-4967-b792-301a3ba9a4cb" />


