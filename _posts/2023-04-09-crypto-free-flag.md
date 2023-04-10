---
title:  WreckIT.04 CTF - CRYPTO - Free Flag
updated: 2023-04-09 22:23:53
tags: [writeup,crypto,ctf]
author: iluv
---

- [SOAL](#CRY1337)
   - [FLAG](#flag1337)
   - [REFERENSI](#refe1337)



<a id="CRY1337"></a>

# Soal

Diberikan sebuah soal tentang cripto

[soal register](../writeup/criptofree/1.png)

yang berisi file download dengan nama file yaitu `soal.secret`

ketika mencoba membuka file tersebut isinya seperti ini 

```
00110100011000010011010001100001001101000011001100110101001101100011010000110101001101010011010100110100001100110011010100111001001101000110001000110101011000010011010000110100001101010011010000110100001110010011010001100100001101000011010000110011001100110011010001100010001101000011001000110100001100110011010100110101001101010011100100110101001100010011001100110010001101000011100000110100001110010011010001100001001101010011000000110101001101110011010001100110001101010011100100110101001101000011010101100001001101000110010000110100011000010011010100110001001101010011100000110100001110010011001100110101001101010011010000110100001100100011010001100011001100110011010100110101001110000011010000111000001101010011000100110011001100100011010000110011001100110011011100110100011001100011010000110110001101010011001000110100001101110011010000110011001100110011010100110100001100100011010000110010001101000011010100110100001101010011010100110111001101000011011000110011001101100011001100110110001101000011010000110100001110010011010001100100001101010011011000110101001110000011010000110111001101000011001100110011001101010011010000110011001101000011010000110100011001100011010001100001001100110011010100110100001101110011001100110100001101010011100100110100011000110011010001100110001101000110010000110101011000010011010100111000001101000011011100110100001100110011010100111000001100110011001100110101001101100011010001100110001101000110000100110011001100100011010100111000001101000011010100110011001101010011010001100011001101010011001100110101001100000011010100110101001100110110010000110011011001000011001101100100001100110110010000110011011001000011001101100100
```
> __Note__
> Seorang NPC pergi ke Lawang Sewu dan mendapati suatu pintu dengan tulisan seperti password. Ada palang bertuliskan Bi UNTUK BINERRRRR!!! password is `BiHB32R13`

mari kita titik fokuskan pada pernyataan `Bi UNTUK BINERRRRR!!!` dan password is `BiHB32R13`

kalau dilihat dari pola password disini saya asumsikan begini : 

- Bi adalah `BINER`
- H adalah `HEXA`
- B32 adalah `BASE32`
- R13 adalah `ROT13`


jadi kode awal yang diberikan adalah biner, disini kita akan merubah dari `biner` ke `hexa`

```python
kode_biner = 'MASUKAN ANGKA BINER DISINI'

# Bagi string biner menjadi potongan-potongan 8 bit dan konversi setiap potongan menjadi nilai desimalnya.
list_desimal = [int(kode_biner[i:i+8], 2) for i in range(0, len(kode_biner), 8)]

print(list_desimal)
```
hasilnya :

```
[52, 97, 52, 97, 52, 51, 53, 54, 52, 53, 53, 53, 52, 51, 53, 57, 52, 98, 53, 97, 52, 52, 53, 52, 52, 57, 52, 100, 52, 52, 51, 51, 52, 98, 52, 50, 52, 51, 53, 53, 53, 57, 53, 49, 51, 50, 52, 56, 52, 57, 52, 97, 53, 48, 53, 55, 52, 102, 53, 57, 53, 52, 53, 97, 52, 100, 52, 97, 53, 49, 53, 56, 52, 57, 51, 53, 53, 52, 52, 50, 52, 99, 51, 53, 53, 56, 52, 56, 53, 49, 51, 50, 52, 51, 51, 55, 52, 102, 52, 54, 53, 50, 52, 55, 52, 51, 51, 53, 52, 50, 52, 50, 52, 53, 52, 53, 53, 55, 52, 54, 51, 54, 51, 54, 52, 52, 52, 57, 52, 100, 53, 54, 53, 56, 52, 55, 52, 51, 51, 53, 52, 51, 52, 52, 52, 102, 52, 97, 51, 53, 52, 55, 51, 52, 53, 57, 52, 99, 52, 102, 52, 100, 53, 97, 53, 56, 52, 55, 52, 51, 53, 56, 51, 51, 53, 54, 52, 102, 52, 97, 51, 50, 53, 56, 52, 53, 51, 53, 52, 99, 53, 51, 53, 48, 53, 53, 51, 100, 51, 100, 51, 100, 51, 100, 51, 100, 51, 100]
```
sekarang kita rubah dari `decimal` ke `ASCII` character :

```python
kode_biner = 'MASUKAN ANGKA BINER DISINI'

# Bagi string biner menjadi potongan-potongan 8 bit dan konversi setiap potongan menjadi nilai desimalnya.
list_desimal = [int(kode_biner[i:i+8], 2) for i in range(0, len(kode_biner), 8)]

# Konversi setiap nilai desimal ke karakter ASCII yang sesuai
text = ''.join(chr(decimal) for decimal in list_desimal)

print(text)
```

hasilnya : 

```
4a4a4356455543594b5a4454494d44334b42435559513248494a50574f59545a4d4a5158493554424c355848513243374f4652474335424245455746363644494d565847433543444f4a354734594c4f4d5a5847435833564f4a325845354c5350553d3d3d3d3d3d
```

sekarang kita melakukan decode pada hexa diatas dalam base32 decode

```python
import base64

kode_biner = 'MASUKAN ANGKA BINER DISINI'

# Bagi string biner menjadi potongan-potongan 8 bit dan konversi setiap potongan menjadi nilai desimalnya.
list_desimal = [int(kode_biner[i:i+8], 2) for i in range(0, len(kode_biner), 8)]

# Konversi setiap nilai desimal ke karakter ASCII yang sesuai
text = ''.join(chr(decimal) for decimal in list_desimal)

decoded_hexa = bytes.fromhex(text).decode('utf-8')
print(decoded_hexa)
```

hasilnya : 

```bash
JJCVEUCYKZDTIMD3KBCUYQ2HIJPWOYTZMJQXI5TBL5XHQ2C7OFRGC5BBEEWF66DIMVXGC5CDOJ5G4YLOMZXGCX3VOJ2XE5LSPU======
```

lah ini kode apaan? apakah ini kode base64? ayo kita decode 

```bash
echo 'JJCVEUCYKZDTIMD3KBCUYQ2HIJPWOYTZMJQXI5TBL5XHQ2C7OFRGC5BBEEWF66DIMVXGC5CDOJ5G4YLOMZXGCX3VOJ2XE5LSPU======' | base64 -d
```

hasilnya : 

```
? ??9??0?#??/??C`?8TF
                     ?AE???1U?
                              ??8?F??1??	}?8????=% 
```

hhhhhmmmm...

Berdasarkan informasi soal `Bi UNTUK BINERRRRR!!!` dan password is `BiHB32R13` yang dimana BiH`B32`R13 adalah base32, tetapi untuk memastikan mari kita coba mengidentifikasi kode tersebut apakah benar itu base32 

```python
import base64

def is_base32(s):
    try:
        base64.b32decode(s)
        return True
    except base64.binascii.Error:
        return False

def is_base64(s):
    try:
        base64.b64decode(s)
        return True
    except base64.binascii.Error:
        return False

s = 'JJCVEUCYKZDTIMD3KBCUYQ2HIJPWOYTZMJQXI5TBL5XHQ2C7OFRGC5BBEEWF66DIMVXGC5CDOJ5G4YLOMZXGCX3VOJ2XE5LSPU======'

if is_base32(s):
    print('Kode tersebut adalah base32')
elif is_base64(s):
    print('Kode tersebut adalah base64')
else:
    print('Kode tersebut bukan base32 atau base64')
```

hasilnya : `Kode tersebut adalah base32` 

niceeeeee... kita lanjut untuk melakukan decode base32nya 

```python

import base64

kode_biner = 'MASUKAN ANGKA BINER DISINI'

# Bagi string biner menjadi potongan-potongan 8 bit dan konversi setiap potongan menjadi nilai desimalnya.
list_desimal = [int(kode_biner[i:i+8], 2) for i in range(0, len(kode_biner), 8)]

# Konversi setiap nilai desimal ke karakter ASCII yang sesuai
text = ''.join(chr(decimal) for decimal in list_desimal)

decoded_hexa = bytes.fromhex(text).decode('utf-8')

decoded_b32 = base64.b32decode(decoded_hexa)
decoded_teks = decoded_b32.decode('utf-8')

print(decoded_teks)

```

hasilnya : `JERPXVG40{PELCGB_gbybatva_nxh_qbat!!,_xhenatCrznanfna_ururur}`

Dari hasil yang didapat merupakan kode acak, tetapi karena cluenya sudah ketahuan yaitu `ROT13`

> __ROT13__
>
> ROT13 adalah metode enkripsi sederhana yang menggeser setiap huruf dalam teks sejauh 13 posisi dalam alfabet. Jadi misalnya huruf A akan digantikan dengan huruf N, huruf B akan digantikan dengan huruf O, dan seterusnya hingga huruf Z digantikan dengan huruf M.
>
> Contohnya, teks "HELLO" akan diubah menjadi "URYYB" karena H digantikan dengan U (H adalah huruf ke-8 dalam alfabet dan dirotasi sebanyak 13 posisi menjadi huruf ke-21, yaitu U) dan seterusnya.

kita tambahkan ROT13 didalam kode python yang kira kira seperti ini : 

```python
def rot13(text):
    rot13_trans = str.maketrans(string.ascii_lowercase + string.ascii_uppercase,
                               string.ascii_lowercase[13:] + string.ascii_lowercase[:13] +
                               string.ascii_uppercase[13:] + string.ascii_uppercase[:13])
    return text.translate(rot13_trans)
```

Kode lengkapnya seperti ini : 

```python

import base64

kode_biner = 'MASUKAN ANGKA BINER DISINI'

# Bagi string biner menjadi potongan-potongan 8 bit dan konversi setiap potongan menjadi nilai desimalnya.
list_desimal = [int(kode_biner[i:i+8], 2) for i in range(0, len(kode_biner), 8)]

# Konversi setiap nilai desimal ke karakter ASCII yang sesuai
text = ''.join(chr(decimal) for decimal in list_desimal)

decoded_hexa = bytes.fromhex(text).decode('utf-8')

decoded_b32 = base64.b32decode(decoded_hexa)
decoded_teks = decoded_b32.decode('utf-8')

def rot13(text):
    rot13_trans = str.maketrans(string.ascii_lowercase + string.ascii_uppercase,
                               string.ascii_lowercase[13:] + string.ascii_lowercase[:13] +
                               string.ascii_uppercase[13:] + string.ascii_uppercase[:13])
    return text.translate(rot13_trans)

    
decoded_flag = rot13(decoded_teks)

print(decoded_flag)

```
<a id="flag1337"></a>

## FLAG

FLAG : `WRECKIT40{CRYPTO_tolongin_aku_dong!!,_kurangPemanasan_hehehe}`


<a id="refe1337"></a>

## Referensi

-  [BINER](https://id.wikipedia.org/wiki/Sistem_bilangan_biner)
-  [DECIMAL](https://id.wikipedia.org/wiki/Sistem_bilangan_desimal)
-  [HEXA](https://kb.iu.edu/d/afdl)
-  [BASE32](https://id.wikipedia.org/wiki/Base32)
-  [BASE64](https://en.wikipedia.org/wiki/Base64)
-  [ROT13](https://en.wikipedia.org/wiki/Base64)








