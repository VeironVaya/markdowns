# HACKATHON TEAM-4 🔥🔥🔥🔥🔥 [Nama - App]

## 1. fitur utama: Masukin data timbangan, dihitung, balikin poin

## 2. fitur penting:

### 2.1. Exchange poin dengan product

(User lihat product list -> pilih product -> Dapat bukti -> bahwa sudah membayar "poin" `Poin berkurang sesuai nominal` -> datang ke lokasi tukar barang -> perlihatkan bukti -> tempat pariwisata kasih barang).

### 2.2. Volunteering (2 Opsi)

#### 2.2.1. Opsi 1 (Sederhana) = (User lihat news/artikel yang bisa dislide biasa ke samping gitu, berarti bakal makai data dummy yang tersedia aja,READ ONLY)

#### 2.2.2. Opsi 2 (Interactive) = (User bisa CRUD atau RU aja volunteering)

1. User ke halaman volunteering -> lihat list of volunteering tersedia -> bisa join
2. User ke halaman volunteering -> nambahin open volunteer dia -> akan mengupdate list of volunteer
3. Kalau user merupakan pembuat volunteer bisa update dan delete. kalau bukan bisa lihat dan join aja.

NOTE: mungkin kalau mau disimplifikasi `nambahin volunteernya ga ada` tapi cuman bisa join berarti sistem Create dan Delete ga ada. mirip opsi 1 (sederhana) tapi lebih interaktif dikit dan secara tampilan lebih ngga boring cuman judul dan hyperlink win win solution.
Berarti bakal ada RU (updatenya cuman nambah jumlah peserta volunteer di database kalo user join) yang dihandle sama Firebase `TANPA GAMBAR` , Mungkin alternatif gambar bisa ke aset aplikasi `Image` sesuai dengan `kategori` misal kategorinya laut yaudah pake aset laut bawaan aja `lokal`. jadi isi dari setiap list of volunteer bakal ada :

```dart
{
	judul: String
	deskripsi: String
	jumAnggotaSekarang: integer
	lokasi: String/Longitude altitude
	periode: Date/String
	closeRegist: Date
	hyperlink?: (tambahan misal link ke ig dsb) String
    kategori?: String
}
```

## 3. fitur sampingan:

### 3.1. History

(User bisa lihat pernah volunteering apa aja `Yang pernah dia join` dan User bisa lihat apa aja product yang dia tuker )

### 3.2. Achievement

(Lebih ke dashboard misal dia membuang sampah 100 kg berarti `Clean Master` dll)

## 4. IMPORTANT NOTES (Titipan Vei)

1. **[General]** Gua merasa diapp ini gamification (Poin,exchange,bahkan achievement) ini penting walau diaplikasi lain biasanya ngga penting. Karena tujuan utama kita adalah meningkatkan keinginan masyarakat buat buang sampah pada tempatnya sehingga butuh reward,exchange dan achievement.
2. **[Buat MP]** Secara fitur buat MP bisa kita bagi lebih mudah karena product dan volunteering ngga berkaitan, sehingga buat volunteering yang ada Firebasenya mungkin bisa gua handle kalau mau, nanti Rahmah bisa garap yang bagian product exchange dan poin yang sebenernya kalau mau pake collection lain di firebase bisa bisa aja tapi feel free kalau mau disimplifikasi buat prototypenya pake lokal aja kayak kemarin motionshop gitu **[Further Discuss]**.

- Vei (Login,Logout,Volunterring,profiling)
- Rahmah (Home,Timbangan(poin),product exchange)

3. **[Buat UI/UX]** Karna mungkin MP-nya bagi kerja perfitur mungkin bisa disesuaikan buat desainnya juga perfitur **[Further Discuss]**.
