# I C More Packets


![Estadio_de_Leiria](https://user-images.githubusercontent.com/62060867/138410805-c43cb1a8-0b94-44bc-bfe1-96778d9395b6.jpg)


# Anylyze

Sau khi một time mổ xẻ từng pixel trong hình từ strings, binwalk, foremost, outguess đến metadata mà vẫn không có bất cứ thông tin gì. 
Mình đã dò wordlist xem có password nào có thể extract file không

```
┌──(kali㉿kali)-[~/Desktop/JornathonCTF/lastFore]
└─$ time stegseek Estadio_de_Leiria.jpg rockyou.txt
StegSeek version 0.5
Progress: 83.59% (116964628 bytes)           

[i] --> Found passphrase: "pompump&mommam17"
[i] Original filename: "hidden"
[i] Extracting to "Estadio_de_Leiria.jpg.out"
stegseek Estadio_de_Leiria.jpg rockyou.txt  11.16s user 4.54s system 200% cpu 7.837 total

```
Cool, ta có được passpharase `pompump&mommam17`

```
┌──(kali㉿kali)-[~/Desktop/JornathonCTF/lastFore]
└─$ steghide extract -sf Estadio_de_Leiria.jpg -p "pompump&mommam17" 
wrote extracted data to "hidden".
                                                                                                                    
┌──(kali㉿kali)-[~/Desktop/JornathonCTF/lastFore]
└─$ file hidden          
hidden: ELF invalid byte order (SYSV), unknown class 20
```
Ta có được 1 file `hidden` nhưng có vẻ file này bị thay đổi header

`strings` thử xem

```
┌──(kali㉿kali)-[~/Desktop/JornathonCTF/lastFore]
└─$ strings hidden                                  
1pcap_test_1c.pcapngUT
eaux
JmRZi
.zEdA
UJ:]
*Ped
41MN;3
(I_*.
w;'P
B)W%
....
0?9WtK
OMuZx

 W-o
1pcap_test_1c.pcapngUT
eaux
wcLS.*3
flag.jpgUT
eaux

```

Ok có vẻ file `hidden` này là 1 file `zip` mà author đã thay đổi header thành `ELF`.

hexeditor `7F 45 4C 46` thành `50 4b 03 04` ta được 1 file zip

```
                                                                                                                    
┌──(kali㉿kali)-[~/Desktop/JornathonCTF/lastFore]
└─$ unzip hidden    
Archive:  hidden
  inflating: 1pcap_test_1c.pcapng    
  inflating: flag.jpg                

```
oh flag đây rồi, cat nó thôi 

![image](https://user-images.githubusercontent.com/62060867/138416661-602ac1a4-1214-48c6-82b8-afb062cbaaea.png)

Cool, chuẩn flag theo nghĩa đen. Ok còn file `pcap`
```
┌──(kali㉿kali)-[~/Desktop/JornathonCTF/lastFore]
└─$ wireshark 1pcap_test_1c.pcapng                                                                              1 ⚙
```
![image](https://user-images.githubusercontent.com/62060867/138417205-0ab216ae-814a-4c28-81cc-8eb09d775f43.png)

Một bài `Internet Control Message Protocol- ICMP`

Filter `icmp` ta thấy các giá trị TTL thay đổi theo từng gói và nhìn vào các kí tự đầu tiên ta có được flag 

![image](https://user-images.githubusercontent.com/62060867/138418344-e4bdfaf4-7662-4cfb-b6d0-1846df39c363.png)

Để dễ dàng nhìn hơn mình dùng `tskark` để chuyển các giá trị đó vào `raw_data`

```
└─$ tshark -r 1pcap_test_1c.pcapng -Y "icmp.type == 8" -T fields -e data > raw_data
```
Sau đó mình có một đoạn script để decode hex

```                                              
f = open('raw_data', 'r')
lines = f.read().splitlines()
output = []
output2=[]
for l in lines:
    try:
        val = l.decode('hex')
        if val not in output:
            output.append(val)
    except:
        print "In Exception" + l

w = open('flag', 'wb')
for i in output:
        w.write(i)
w.close()
```
Cat flag nào và submit thôi

```
┌──(kali㉿kali)-[~/Desktop/JornathonCTF/lastFore]
└─$ cat flag                                                                                       1 ⚙
flag{17_15n'7_7h15_bu7_U53fuLL}

```
> Yeah, `submission failed`

Bullshit, fake flag ;))

Đứng hình 5s, quay lại `flag.jpg`
Test password là fake flag kia xem sao

```
┌──(kali㉿kali)-[~/Desktop/JornathonCTF/lastFore]
└─$ steghide extract -sf flag.jpg -p "flag{17_15n'7_7h15_bu7_U53fuLL}"                                       1 ⨯ 1 ⚙
wrote extracted data to "flag.txt".
                                                                                                                     
┌──(kali㉿kali)-[~/Desktop/JornathonCTF/lastFore]
└─$ cat flag.txt                                                                                                 1 ⚙
flag{C0ng&4t5_H4\/3_4_C00#13}
                              

```

## Thank God You're Here


