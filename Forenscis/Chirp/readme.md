# Chirp

> We got sent a file that sounds a bird. There mút be something hidden... Can you help us out?

[Chirp.wav](https://drive.google.com/file/d/1lIN2bmHzo0h2n8i86xqm_bUP0Xjo6Me_/view?usp=sharing)

## Solution

Challenge này cho ta một file `wav`

Mở file bằng `Audacity` xem chúng ta có gì

![image](https://user-images.githubusercontent.com/62060867/138320055-c7baa772-9953-4e80-b7d8-13d58e7bb02e.png)

Khi chuyển sang `multi-view` ta thấy được đoạn mã `morse-code`

Decode chúng bằng https://morsecode.world/

![image](https://user-images.githubusercontent.com/62060867/138320780-a921f5f2-1c78-4768-8b85-a12010d0ba9e.png)

Ta có được 1 đường link với `SHORTURL.AT/KCGMX`

```
https://drive.google.com/file/d/1L7rPwKZA9w_Zy_GcCvAwbxUp1Pi__H8-/view
```
![image](https://user-images.githubusercontent.com/62060867/138321179-73735573-3983-4fb4-8651-a0b7512e79a9.png)

Check 2 file `corrupted.mp3` và `bird.jpg` thì mình phát hiện file corrupted.mp3 bị thiếu 1 phần offset

```
└─$ strings corrupted.mp3
0000000 fbff 64e0 0000 1308 c96d 6d15 02e0 908f
0000010 8047 28a0 3200 2f02 1949 80cd 1002 e145
0000020 00c3 00a0 5601 7bf3 3261 43d0 9054 ad3b
0000030 dc43 3cd6 230a 5e80 0234 28e3 3322 f372
0000040 2129 b131 1153 300d 5090 1910 cb69 5e96
0000050 0730 4080 0009 089a 2940 c800 0918 9143
0000060 ff60 f116 2e73 ef6c e6d5 a081 2874 3519
0000070 c07d d543 e3f3 692c 76f6 4cc3 76cb 92f1
....
```
![image](https://user-images.githubusercontent.com/62060867/138323229-a85afe33-72eb-4afe-ab2b-32a86fc79966.png)

Có thể thấy chúng bị mất `offset` từ `00179c0` -> `0017b90`. Vậy chỉ cần add phần còn thiếu vào, xóa đi các phần offset

![image](https://user-images.githubusercontent.com/62060867/138323520-7c6b266a-89d9-4644-b138-9de8c06d509d.png)

Tạo 1 file mới với các `hex-value` và cat flag 


```
└─$ strings corrupted.mp3 | grep flag
flag{4Udi0_f0r3n.s1cs_1s_p41n}

```
