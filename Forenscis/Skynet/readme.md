# Skynet

  ![skynet](https://user-images.githubusercontent.com/62060867/138424318-337b5992-2fa1-4ef8-9025-a83abab75e71.jpeg)

## Autobiography
Một bài Crypto nhưng mình thấy nó giống Forensics hơn :D

Hơi tiếc bài này một chút vì sau giải khoảng 10' mình mới giải ra 

## Analyze
Bài này cho ta `qrcode`, scan trước:

```
┌──(kali㉿kali)-[~/Desktop/JornathonCTF/skynet]
└─$ zbarimg skynet.jpeg           
QR-Code: otpauth://totp/email:sarah.connor@skynet.net?secret=GRIUSTKIJFNDMNSIKBDDKSCCJQ&issuer=SKYNET

```
Ta được một link gì đó, tạm thời bỏ qua

Check metadata xem có gì

```
┌──(kali㉿kali)-[~/Desktop/JornathonCTF/skynet]
└─$ exiftool skynet.jpeg          
ExifTool Version Number         : 12.30
File Name                       : skynet.jpeg
File Type Extension             : jpg
Thumbnail Offset                : 202
Thumbnail Length                : 20863
XMP Toolkit                     : Image::ExifTool 11.88
Description                     : https://simplydailypuzzles.com/daily-cryptic/index.html?puzz=dc1-2021-08-26 (across=13)
Comment                         : bm90IGhlcmUgZHVkZS4ga2VlcCBzZWFyY2hpbmcuLi4gSSBob3BlIHRoYXQgeW91IGFyZSBnb29kIHdpdGggY3Jvc3N3b3Jkcw==
Image Size                      : 490x490
Megapixels                      : 0.240
Thumbnail Image                 : (Binary data 20863 bytes, use -b option to extract)
```
Ta thấy có Description (link) và Comment (base64), decode base64 trước xem

```
┌──(kali㉿kali)-[~/Desktop/JornathonCTF/skynet]
└─$ echo "bm90IGhlcmUgZHVkZS4ga2VlcCBzZWFyY2hpbmcuLi4gSSBob3BlIHRoYXQgeW91IGFyZSBnb29kIHdpdGggY3Jvc3N3b3Jkcw==" | base64 -d
 not here dude. keep searching... I hope that you are good with crosswords`
```
hmmm `good Crossword` 

![image](https://user-images.githubusercontent.com/62060867/138428743-f8b2a01b-4a4c-42bb-b576-ee73c7035a7e.png)

Cool một trò chơi giải quyết các ô chữ, để ý sau đường link có `accross=13`, nhấn vào ô 13 đường ngang sẽ có câu hỏi được trỏ tới `English woman almost make cakes`

Rãnh thì search google, không thì check đáp án thôi kkk

![image](https://user-images.githubusercontent.com/62060867/138429716-79f77068-95e1-4c64-9173-6a36e02f502e.png)

Ta được `ECLAIR`, có vẻ là password

Steghide ảnh với pass vừa kiếm 

```
┌──(kali㉿kali)-[~/Desktop/JornathonCTF/skynet]
└─$ steghide extract -sf skynet.jpeg -p "eclair"                                                                                1 ⨯
wrote extracted data to "creds.txt".
                                                                                                                                    
┌──(kali㉿kali)-[~/Desktop/JornathonCTF/skynet]
└─$ cat creds.txt 
uffw: 1852pyp40000-8bp8-8861-s302-55360430=yna?/obycbrbwne/ui.cvve.obycbrbwne//:riuuk
xrbo: 61 64 6y 69 6b 
iprrdfoy: Y49K2VT6?6886V
```

Hmm có vẻ như bị reverse  

```
┌──(kali㉿kali)-[~/Desktop/JornathonCTF/skynet]
└─$ cat creds.txt | rev                                                                                                         1 ⨯
kuuir://enwbrbcybo.evvc.iu/enwbrbcybo/?any=03406355-203s-1688-8pb8-00004pyp2581 :wffu
 b6 96 y6 46 16 :obrx
V6886?6TV2K94Y :yofdrrpi                                                                                                                                    

```
We see: `kuuir://` -> `https://`. Chúng bị encrypt bởi loại mã hóa nào đó

Để biết bị mã hóa cipher gì, ta có thể thử với https://www.dcode.fr/cipher-identifier

![image](https://user-images.githubusercontent.com/62060867/138434485-534d5fc2-85db-470a-95e6-afd54e7fa771.png)

Hmm, khả năng cao cho thấy là `Affine Cipher`

![image](https://user-images.githubusercontent.com/62060867/138434852-f8679d0d-2e72-4a88-a288-230aacb44a76.png)

Yeah, kết quả đầu tiên đúng với những gì mình mong đợi, 1 đường link truy cập đúng và có vẻ như cần phải reverse lại lần nữa

```
https://filesender.fccn.pt/filesender/?vid=03406355-203b-1688-8ae8-00004ada2581 :loot
e6 96 d6 46 16 :resu
C6886?6KC2H94D :drowssap
```

https://filesender.fccn.pt/filesender/?vid=03406355-203b-1688-8ae8-00004ada2581

![image](https://user-images.githubusercontent.com/62060867/138435062-15064518-3415-4556-a13b-c16f68dbb2b2.png)

```
┌──(kali㉿kali)-[~/Desktop/JornathonCTF/skynet]
└─$ cat decode_creds.txt | rev
tool: 1852ada40000-8ea8-8861-b302-55360430=div?/redneselif/tp.nccf.redneselif//:sptth
user: 61 64 6d 69 6e
password: D49H2CK6?6886C
```
We have user and password are encrypted

> ### User: admin

```
>>> bytes.fromhex('61 64 6d 69 6e').decode('utf-8')
'admin'

```

> ### Password: schwarzenegger

![image](https://user-images.githubusercontent.com/62060867/138436739-17bb1d67-237d-4b02-81d0-cc958b9e2f88.png)

- User có, password có, download file từ link thôi

```
                                                                                                                                    
┌──(kali㉿kali)-[~/Desktop/JornathonCTF/skynet]
└─$ wget https://filesender.fccn.pt/filesender/download.php?vid=03406355-203b-1688-8ae8-00004ada2581
--2021-10-22 06:15:57--  https://filesender.fccn.pt/filesender/download.php?vid=03406355-203b-1688-8ae8-00004ada2581
Resolving filesender.fccn.pt (filesender.fccn.pt)... 193.136.192.125, 2001:690:a00:4001::125
Connecting to filesender.fccn.pt (filesender.fccn.pt)|193.136.192.125|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 404751 (395K) [application/octet-stream]
Saving to: ‘download.php?vid=03406355-203b-1688-8ae8-00004ada2581.1’

download.php?vid=03406355-203b-1 100%[==========================================================>] 395.26K   299KB/s    in 1.3s    

2021-10-22 06:16:00 (299 KB/s) - ‘download.php?vid=03406355-203b-1688-8ae8-00004ada2581.1’ saved [404751/404751]

                                                                                                                                    
┌──(kali㉿kali)-[~/Desktop/JornathonCTF/skynet]
└─$ mv download.php\?vid=03406355-203b-1688-8ae8-00004ada2581 skynetbackdoor.zip
                                                                                                                                    
┌──(kali㉿kali)-[~/Desktop/JornathonCTF/skynet]
└─$ unzip skynetbackdoor.zip 
Archive:  skynetbackdoor.zip
   skipping: skynetbackdoor/pytransform/__init__.py  need PK compat. v5.1 (can do v4.6)
   skipping: skynetbackdoor/pytransform/__pycache__/__init__.cpython-39.pyc  need PK compat. v5.1 (can do v4.6)
   skipping: skynetbackdoor/pytransform/_pytransform.so  need PK compat. v5.1 (can do v4.6)
   skipping: skynetbackdoor/skynet_access.py  need PK compat. v5.1 (can do v4.6)

```
Hmmm skiping, we need password :( Where? Crack it 

```
┌──(kali㉿kali)-[~/Desktop/JornathonCTF/skynet]
└─$ zip2john skynetbackdoor.zip > skynetbackdoor.hash
skynetbackdoor.zip/skynetbackdoor/ is not encrypted!
skynetbackdoor.zip/skynetbackdoor/pytransform/ is not encrypted!
ver 78.8 skynetbackdoor.zip/skynetbackdoor/ is not encrypted, or stored with non-handled compression type
ver 78.8 skynetbackdoor.zip/skynetbackdoor/pytransform/ is not encrypted, or stored with non-handled compression type
ver 81.9 skynetbackdoor.zip/skynetbackdoor/pytransform/__init__.py is not encrypted, or stored with non-handled compression type
ver 78.8 skynetbackdoor.zip/skynetbackdoor/pytransform/__pycache__/ is not encrypted, or stored with non-handled compression type
ver 81.9 skynetbackdoor.zip/skynetbackdoor/pytransform/__pycache__/__init__.cpython-39.pyc is not encrypted, or stored with non-handled compression type
ver 81.9 skynetbackdoor.zip/skynetbackdoor/pytransform/_pytransform.so is not encrypted, or stored with non-handled compression type
ver 81.9 skynetbackdoor.zip/skynetbackdoor/skynet_access.py is not encrypted, or stored with non-handled compression type
                                                                                                                                    
┌──(kali㉿kali)-[~/Desktop/JornathonCTF/skynet]
└─$ john --wordlist=rockyou.txt skynetbackdoor.hash 
Using default input encoding: UTF-8
Loaded 1 password hash (ZIP, WinZip [PBKDF2-SHA1 128/128 AVX 4x])
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
zxcasdqwe123     (skynetbackdoor.zip/skynetbackdoor/pytransform/__init__.py)
1g 0:00:00:05 DONE (2021-10-22 06:23) 0.1828g/s 32947p/s 32947c/s 32947C/s 31122530..sandriux
Use the "--show" option to display all of the cracked passwords reliably
Session completed
```

Cool we have password is: `zxcasdqwe123`

```
┌──(kali㉿kali)-[~/Desktop/JornathonCTF/skynet]
└─$ unzip skynetbackdoor.zip -P zxcasdqwe123  
Archive:  skynetbackdoor.zip
┌──(kali㉿kali)-[~/Desktop/JornathonCTF/skynet]
└─$ cd skynetbackdoor         
                                                                                                                                    
┌──(kali㉿kali)-[~/Desktop/JornathonCTF/skynet/skynetbackdoor]
└─$ tree                                                                 
.
├── pytransform
│   ├── __init__.py
│   ├── __pycache__
│   │   └── __init__.cpython-39.pyc
│   └── _pytransform.so
└── skynet_access.py

```
Ta có 1 file python, run nó xem

```
┌──(kali㉿kali)-[~/Desktop/JornathonCTF/skynet/skynetbackdoor]
└─$ python3 skynet_access.py
usage: skynet_access.py [-h] -u U -p P -t T
skynet_access.py: error: the following arguments are required: -u, -p, -t
                                                                                                                                    
┌──(kali㉿kali)-[~/Desktop/JornathonCTF/skynet/skynetbackdoor]
└─$ python3 skynet_access.py -h                                                                                                 2 ⨯
usage: skynet_access.py [-h] -u U -p P -t T

optional arguments:
  -h, --help  show this help message and exit
  -u U        Username to access skynet backdoor
  -p P        Password o access skynet backdoor
  -t T        Token o access skynet backdoor

```
Ok để chạy được file này ta cần User, Password và Token

Hmmm ta đã có user và password. Còn token ở đâu?

kaka Token nằm ở Mochi :D 

![image](https://user-images.githubusercontent.com/62060867/138440504-0aaad3c8-4026-429c-83e4-52b1b836e96f.png)

Yeah scan QR code đề cho bằng `google authenticator qr code`

Vì mã do Google Authenticator cung cấp tại thời điểm quét chỉ tồn tại trong 30s nên mình cần scan lại để lấy mã mới

```
──(kali㉿kali)-[~/Desktop/JornathonCTF/skynet/skynetbackdoor]
└─$ python3 skynet_access.py  -u admin -p schwarzenegger -t 738428
INFO: Initializing.
INFO: Initializing..
INFO: Initializing...
 20%|███████████████████▏                                                                            | 2/10 [00:06<00:24,  3.01s/it]

Accessing backdoor...
Access granted. 
                                                                                                                                    
 50%|████████████████████████████████████████████████                                                | 5/10 [00:18<00:17,  3.46s/it]

Checking provided authentication details...
User creds were successfuly authenticated. 
                                                                                                                                    
 80%|████████████████████████████████████████████████████████████████████████████▊                   | 8/10 [00:30<00:07,  3.60s/it]

Checking token authenticity...
Token is 100% valid. 
                                                                                                                                    
100%|███████████████████████████████████████████████████████████████████████████████████████████████| 10/10 [00:40<00:00,  4.01s/it]
All systems  will go offline.


++++++++++[>+>+++>+++++++>++++++++++<<<<-]>>>>++.++++++.-----------.++++++.++++++++++++++++++++.<-------------------.>----------------.<--.>+++.---------.-----------------.<---.+++.>+++++++++++++++++++++++++++++++.<---.>.------------------.+++++.-.<.++++++.------.>+++++++++.<++.>+++++++++.++++++.
```
Great, we have `brainfuck-language`

Decode chúng bằng https://www.dcode.fr/brainfuck-language 

![image](https://user-images.githubusercontent.com/62060867/138441997-eb571e60-acd1-4947-89f5-7deb2425ed58.png)

# Thank God You're Here






