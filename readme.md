# ATM or ATP or FTP ? 🤔 

> <p>Pradityo mencoba mengembangkan server ftp, tetapi seseorang mencoba melakukan bruteforce login, bisakah Anda menganalisis apa yang terjadi? </p>
> <p><a href="attachments/ftp.pcap">ftp.pcap</a></p>

## Path to Flag

Judul soal memberi petunjuk bahwa soal ini berkaitan dengan ftp, sehingga saya langsung display filter `ftp`. Kemudian melakukan sortir pada column time, dengan asumsi attempt terakhir merupakan attempt yang berhasil

<img src="attachments/1-1.png"><br>

Terlihat tulisan login successful dan saya langsung follow tcp streamnya

```
220 Welcome Alpine ftp server https://hub.docker.com/r/delfer/alpine-ftp-server/
USER adminJarkom
331 Please specify the password.
PASS m4y_th3_Kn!fe_ch1p_&_sh4tter
230 Login successful.
```

`adminJarkom:m4y_th3_Kn!fe_ch1p_&_sh4tter`

<img src="attachments/1-2.png"><br>

`Flag: JARKOM2024{Brut3f0rce_FtP_uhCCXOntg1Jt8A9}`


# evidence 

> <p>Perusahaan nanomate baru saja kebobolan. Mereka menyewamu untuk mencari tahu bagaimana caranya pelaku bisa masuk.</p>
> <p><a href="attachments/challenge.pcapng">challenge.pcapng</a></p>

## Path to Flag

Berdasarkan validator jawaban yang diberikan, kita diharuskan untuk mencari domain milik korban, sehingga kita mencoba untuk filter http terlebih dahulu. Kita mencoba untuk melihat dari atas dan menemukan sebuah domain

<img src="attachments/2-1.png"><br>

`nanomate-solutions.com`

Selanjutnya adalah mencari web server dari domain tersebut, sehingga kita mencoba untuk follow http stream dari packet yang terdapat domain target

<img src="attachments/2-2.png"><br>

`Apache/2.4.56`

Selanjutnya adalah mencari endpoint yang digunakan untuk login, terdapat 2 packet yang menarik jika dilihat secara langsung, yaitu `/app/includes/process_login.php` dan  `app/login.php`, tetapi jika kita follow http stream salah satu dari mereka, dapat dilihat bahwa `/app/login.php` merupakan referer sehingga dapat dipastikan `/app/includes/process_login.php` merupakan endpointnya.

<img src="attachments/2-3.png"><br>

Terakhir, untuk menemukan kredensial yang dipakai, kita menggunakan display filter `http and http.request.uri contains "/app/includes/process_login.php"`.

<img src="attachments/2-4.png"><br>

2 packets paling akhir memiliki nomor packet yang jauh lebih besar (anomali), sehingga kita mencoba packet no. 7025.

<img src="attachments/2-5.png"><br>

`tareq@gmail.com:tareq@nanomate`

<img src="attachments/2-6.png"><br>

`Flag : JARKOM2024{m4innya_h3bat_9T8CY79fQzJol8t}`

# How Many packets?

> <p>Sebagai kewajiban untuk laporan, aku diminta untuk mencari tahu berapa kali attempt login yang dilakukan oleh hacker. Dapatkah kamu membantuku untuk menganalisanya?</p>
> <p><a href="attachments/ftp.pcap">ftp.pcap</a></p>

## Path to Flag

# trace him 

> <p>Selain menghitung jumlah packet, coba lacak juga ip penyerang tersebut!</p>
> <p><a href="attachments/ftp.pcap">ftp.pcap</a></p>

## Path to Flag

# creds 

> <p>Attacker menyadari jika dia bisa membuat clone ftp server dari target, temukan kredensialn dari server ftp yang dibuat oleh attacker</p>
> <p><a href="attachments/evidence.pcap">evidence.pcap</a></p>

## Path to Flag

Deskripsi soal mengarah kepada ftp connection, jadi kita langsung display filter ftp, sekaligus mendapatkan sebuah credential yang valid

<img src="attachments/5-1.png">

`h3ngk3rTzy:S!l3ncE`

<img src="attachments/5-2.png"><br>

`Flag: JARKOM2024{s3curE_uR_FtP_xJ8lRzAtgRVoCrB}`

# malwleowleo

> <p>Dapatkah kamu menemukan file malware yang dikirim oleh attacker melalui ftp?</p>
> <p><a href="attachments/evidence.pcap">evidence.pcap</a></p>

## Path to Flag

Melanjutkan soal creds, kita langsung connect dengan server ftp dengan host 10.15.40.20 menggunakan command `ftp 10.15.40.20` dan input username dan password yang sudah ditemukan sebelumnya.

<img src="attachments/6-1.png"><br>

Kita juga melakukan `ls` untuk mengetahui file apa saja yang berada di dalam server ftp tersebut.

<img src="attachments/6-2.png"><br>

`Flag: JARKOM2024{beC4refUl_0f_m4lwAr3_I6CCXzAtyzVHkAY}`

# fuzz

> <p>My website got hacked. Can you analyze this network traffic to help me track the attacker?</p>
> <p><a href="attachments/capture.pcap">capture.pcap</a></p>

## Path to Flag

Pertama, kita diminta untuk mencari IP address milik attacker.

<img src="attachments/7-1.png"><br>

Secara langsung, kita berasumsi bahwa IP address attacker adalah `10.33.1.154` karena IP tersebut yang sering mengirim packet SYN, sedangkan `127.20.0.2` hanya acknowledge packet SYN tersebut diketahui dari packet SYN ACK dari IP tersebut sehingga dapat diasumsikan IP tersebut merupakan IP korban.

Berdasarkan network traffic tersebut, attacker melakukan koneksi ke port 80 dari korban

Selanjutnya, kita mencari endpoint dari login, jadi kita langsung mencoba untuk follow stream dan mendapatkan path root

<img src="attachments/7-2.png"><br>

Terlebih lagi, dapat ditemukan sesuatu yang mencurigakan yaitu, tools yang digunakan untuk melakukan fuzzing yaitu `Fuzz Faster U Fool v2.0.0-dev`. Setelah melakukan sanity check, kita mengetahui bahwa yang diperlukan adalah singkatannya, sehingga tools yang dipakai attacker adalah `ffuf-v2.0.0-dev`

Terakhir, kita diminta untuk mencari username dan password yang berhasil digunakan oleh attacker.
Menggunakan display filter, kita mencoba untuk melakukan screening dan menemukan sesuatu yang unique.

<img src="attachments/7-3.png"><br>

Kita langsung follow stream dan mendapatkan username dan password yang berhasil digunakan oleh attacker.

<img src="attachments/7-4.png"><br>

`admin:sUp3rSecretp@ssw0rd`

<img src="attachments/7-5.png"><br>

`Flag: JARKOM2024{s3m4ng4t_ya_<3_chfCXcAjg6ksR8B}`