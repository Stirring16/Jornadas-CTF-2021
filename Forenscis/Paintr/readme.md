# Paintr
[Paintr.zip](https://github.com/Stirring16/Jornadas-CTF-2021/files/7394641/Paintr.zip)

## Analyze

Unzip file được cho ta có được 2 bức ảnh
```
┌──(kali㉿kali)-[~/Desktop/JornathonCTF/paintr]
└─$ file Paintr.zip 
Paintr.zip: Zip archive data, at least v2.0 to extract
                                                                                                                    
┌──(kali㉿kali)-[~/Desktop/JornathonCTF/paintr]
└─$ unzip Paintr.zip    
Archive:  Paintr.zip
  inflating: abstract.png            
  inflating: nautilus.jpg           
```
![image](https://user-images.githubusercontent.com/62060867/138402262-4356f9fc-207c-4033-858f-e5d53d84e324.png)

> # abstract.png

```
┌──(kali㉿kali)-[~/Desktop/JornathonCTF/paintr]
└─$ exiftool abstract.png                                                                                       1 ⚙
ExifTool Version Number         : 12.30
File Name                       : abstract.png
Palette                         : (Binary data 48 bytes, use -b option to extract)
Background Color                : 6
Image Description               : What an interesting language
Resolution Unit                 : inches
Artist                          : Piet Mondriant
Y Cb Cr Positioning             : Centered
Exif Version                    : 0232
Components Configuration        : Y, Cb, Cr, -
User Comment                    : It may be helpful to understand what it is
Flashpix Version                : 0100
Owner Name                      : David Morgan-Mar
Image Size                      : 384x384
Megapixels                      : 0.147

```

Khi xem thông tin metadata ta thấy được 2 điểm chú ý: `User Comment: It may be helpful to understand what it is` và `Artist: Piet Mondriant`

Piet Mondrian là họa sĩ nổi tiếng người Hà Lan và từ đó mình tìm kiếm được thông tin [Piet Mondrian color code](https://www.dangermouse.net/esoteric/piet.html) để giải quyết bức hình này. Để giải quyết nhanh hơn mình tìm thấy được 1 tool có thể extract được message của bức hình https://www.bertnase.de/npiet/

```
┌──(kali㉿kali)-[~/Desktop/JornathonCTF/paintr]
└─$ npiet -w -e 220000 abstract.png
0
1
1
2
3
5
8
13
21
34
55
89
144
233
377
610
987

```
Mình nhận ra đây ra là dãy `fibonacci`. Tiếp tục:

> # nautilus.jpg       

```
──(kali㉿kali)-[~/Desktop/JornathonCTF/paintr]
└─$ exiftool nautilus.jpg                                                                                       1 ⚙
ExifTool Version Number         : 12.30
File Name                       : nautilus.jpg
File Size                       : 49 KiB
File Permissions                : -rw-r--r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
Exif Version                    : 0232
Components Configuration        : Y, Cb, Cr, -
User Comment                    : Denso Wave
Encoding Process                : Baseline DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
```
Check metadata ta có được `Denso Wave` (là 1 subsidiary  liên quan đến mã vạch, QR code)

Tới đây mất một thời gian suy nghĩ về dãy `fibonacci` và  việc giấu thông tin trong file png.

Mình đã dùng `steghide` password là `fibonacci`. Guess,it's work

```
┌──(kali㉿kali)-[~/Desktop/JornathonCTF/paintr]
└─$ steghide extract -sf nautilus.jpg -p "fibonacci"
wrote extracted data to "secret.txt".
                                                                                                                    
┌──(kali㉿kali)-[~/Desktop/JornathonCTF/paintr]
└─$ cat secret.txt 
1111111010001100101111111
1000001001111010101000001
1011101011101101001011101
1011101011110101101011101
1011101010001001101011101
1000001000101100001000001
1111111010101010101111111
0000000000110011100000000
1111001010100111010011101
1111000010111111111101000
0000111111110100001011000
0011000001100111111001100
0010111101111101101111110
0110010100001001101111011
0110101101001010010011010
1010110111010011010000000
0011011110111100111111100
0000000011010001100010001
1111111001100000101010111
1000001001001011100011000
1011101000100000111111011
1011101010110010101111011
1011101010011011101010010
1000001010101101100000100
1111111010000110001000111                                                                                                                    
```

Cool, 1 đống binary, làm gì tiếp đây.

À hint từ metadata  `Denso Wave`, ta có thể chuyển convert đống binary này thành một mã QRcode.

```
 from PIL import Image
 lines = open('secret.txt') readlines( )
 lines
['1111111010001100101111111\n',
'1000001001111010101000001\n',
'1011101011101101001011101\n',
'1011101011110101101011101\n',
'1011101010001001101011101\n',
'1000001000101100001000001\n',
'1111111010101010101111111\n',
'0000000000110011100000000\n',
'1111001010100111010011101\n',
'1111000010111111111101000\n',
'0000111111110100001011000\n',
'0011000001100111111001100\n',
'0010111101111101101111110\n',
'0110010100001001101111011\n',
'0110101101001010010011010\n',
'1010110111010011010000000\n',
'0011011110111100111111100\n',
'0000000011010001100010001\n',
'1111111001100000101010111\n',
'1000001001001011100011000\n',
'1011101000100000111111011\n',
'1011101010110010101111011\n',
'1011101010011011101010010\n',
'1000001010101101100000100\n',
'1111111010000110001000111']

 img = Image new('RGB', (100, 100)) 
 img - Image new('RGB', (25, 25))
 pixels = img load( )

pixels
<PixelAccess at 0x7fe78403f690>

for x in range(25):
    for y range(25): 25):
        if lines[y][×]
           pixels[y, x] = (0, 0, 0)
        else:
           pixels[y, x] (0xff, 0xff, 0xff)
           
img save('qr.png')
#delvinru

```

![image](https://user-images.githubusercontent.com/62060867/138409412-745d263c-5b58-42af-931a-7ab3cf2e4f70.png)

Scan it and get flag :D



