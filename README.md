# PayTrack

Sistem penggajian sederhana dengan frontend HTML/CSS/JavaScript, backend Node.js tanpa dependency, dan database Supabase PostgreSQL.

## Menyiapkan Supabase

1. Buat project Supabase dan buka SQL Editor.
2. Jalankan seluruh isi `database/schema.sql`.
3. Ambil Project URL dan publishable key di pengaturan API project.
4. Untuk pemakaian pribadi, buka `frontend/index.html` dan masukkan kedua nilai lewat tombol pengaturan.
5. Agar semua pengguna langsung terhubung, isi `projectUrl` dan `publishableKey` di `frontend/config.js`, lalu host seluruh folder `frontend` pada URL yang bisa diakses pengguna. File `file:///` dan local storage tidak dibagikan antarperangkat.

Kredensial yang dimasukkan lewat pengaturan tersimpan di browser tersebut. Konfigurasi di `frontend/config.js` tersedia bagi semua pengunjung dan publishable key memang dirancang untuk penggunaan frontend; jangan pernah taruh `service_role` key di sana. Skema demo saat ini mengizinkan akses tulis untuk role `anon`, jadi sebelum mengekspos data payroll sungguhan di internet, aktifkan Supabase Auth dan batasi kebijakan RLS agar hanya pengguna berwenang yang dapat membaca atau mengubah data.

## Menjalankan server lokal (opsional)

```powershell
node backend/app.js
```

Buka `http://localhost:3000`. Server memakai Node.js bawaan dan menyediakan pemeriksaan status di `/api/health`. Frontend tetap terhubung langsung ke Supabase, jadi server tidak diperlukan bila membuka file HTML.

## ERD

- `positions` memiliki banyak `employees`.
- `employees` memiliki banyak `salary_components` dan catatan `attendance` per periode.
- `salary_components.component_type` membedakan `TUNJANGAN` dan `POTONGAN`; menu Komponen Gaji menampilkannya melalui tab filter.
- Absensi mencatat `working_days` dan `alpha_days`. Potongan alfa dihitung sebagai `gaji_pokok * alpha_days / working_days`, dibulatkan ke rupiah penuh.
- Estimasi gaji bersih memakai gaji pokok + tunjangan - potongan komponen - potongan absensi untuk karyawan aktif pada periode berjalan.

Jika database sebelumnya memakai tabel `allowances` dan `deductions`, jalankan `database/schema.sql` di SQL Editor project yang sama. Skrip memindahkan isinya ke `salary_components` dengan jenis yang sesuai, lalu menghapus tabel lama.

Kebijakan RLS pada skema dibuat terbuka untuk kebutuhan demo berbasis publishable key. Sebelum digunakan untuk data nyata, batasi akses dengan autentikasi dan kebijakan per peran.