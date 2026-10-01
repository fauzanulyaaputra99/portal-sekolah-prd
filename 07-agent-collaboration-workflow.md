# 07 — AGENT COLLABORATION WORKFLOW

## SISTEM PORTAL SEKOLAH

**Dokumen:** `07-agent-collaboration-workflow.md`
**Status:** `FINAL` — lingkup *agent collaboration workflow* (PRD 01–06 tetap tidak diubah)
**Proyek:** Portal Sekolah — `D:\anti\portal-sekolah`
**Fungsi:** Pedoman alur kerja kolaborasi antar-agent (dan manusia) saat mengerjakan proyek secara bertahap
**Dependensi:** PRD `01`–`06` (dibaca dan dipahami, **tidak diubah**)

> **Catatan penting:** Dokumen ini adalah *turunan* dari PRD, bukan pengganti. PRD `01`–`06` tetap menjadi otoritas tunggal. Jika terjadi perbedaan antara dokumen ini dan PRD, maka **PRD yang menang**.
>
> Bagian **1–6, 12 (kecuali 12.1)** dan **11.4** bersumber langsung dari PRD/ADR. Bagian **7–10, 13–15**, **11.1–11.3**, **12.1**, serta **Shared Dependency Ownership** adalah *aturan governance baru* (ditandai `[BARU]` pada heading, atau dijelaskan pada bagiannya). Aturan governance **meluruskan cara membaca** aturan, tidak mengubah requirement canonical PRD.
>
> Bagian **Administration Workstream Roadmap** dan **Academic Workstream Roadmap** menurunkan item-nya **murni dari PRD 01/02/05/06** (tidak ada fitur karangan), tetapi kolom `Status` / `Repository Evidence` merupakan **hasil verifikasi repository** — bukan kutipan PRD.

---

## 1. Purpose

- Mendefinisikan cara kerja kolaborasi antar-agent saat mengerjakan Portal Sekolah secara bertahap dan aman.
- Mencegah pekerjaan yang tumpang tindih, konflik perubahan berkas, dan pelanggaran keputusan final PRD.
- Menetapkan aturan pemilihan tugas berikutnya, gerbang keamanan, keselamatan basis data, dan kondisi berhenti.
- Sumber: turunan dari PRD 05 §37 (Development Boundary) & §38 (Rencana Strategi Pengembangan).

## 2. Source of Truth

- Urutan otoritas dokumen: `05-consolidated-prd.md` (blueprint final) → `06-decision-resolution.md` (ADR 01–20) → `04` (keamanan & operasional) → `02` (arsitektur & basis data) → `03` (Google Drive) → `01` (kebutuhan produk).
- Metadata di **MySQL = Single Source of Truth** data; berkas fisik hanya tersimpan privat di Google Drive (PRD 03 §6; PRD 05 §39 butir 4).
- `teacher_duty_schedules` = single source of truth status Guru Piket (ADDENDUM §8; ADR 19).
- Status keputusan final (2026-09-27): ADR 01–03 `[OBSOLETE]`, ADR 04–20 `[FINAL]`, **tidak ada keputusan terbuka**.

## 3. Administration Ownership

- Pemilik area: role **Admin / Staff Tata Usaha (TU)**.
- Master data: tahun ajaran, tingkat pendidikan, rombel dinamis, data guru, data siswa, mata pelajaran (PRD 01 §6.2–§6.6).
- Penugasan mengajar (*teaching assignment*) & jadwal pelajaran mingguan (PRD 01 §6.5, §6.7).
- CRUD jadwal piket `teacher_duty_schedules` **beserta audit** (PRD 01 §6.11; ADDENDUM §8–§18).
- Manajemen akun + reset password manual TU (PRD 01 §6.8; ADR 10).
- Koreksi absensi siswa (wajib alasan ≥10 karakter + audit `SCHOOL_ATT_CORRECT`) (PRD 01 §6.9, §13.7; PRD 02 §14.2).
- Akses **eksklusif** panel audit log (PRD 01 §6.10; ADR 17). Laporan & ekspor CSV/XLSX (PRD 01 §16).

## 4. Academic Ownership

- Pemilik area: role **Guru** dan **Supervisor / Atasan**.
- Guru — repositori dokumen pribadi (RPP/ATP/PROTA/PROSEM), maks 10 MB (ADR 07), versi immutable v1→v2 (ADR 08) (PRD 01 §7.3).
- Guru — sesi absensi siswa per mata pelajaran hanya untuk kelas yang diampu; penutupan **manual** (ADR 14) (PRD 01 §7.5, §12).
- Guru — akses scanner absensi masuk/pulang **hanya bila terjadwal piket** pada tanggal tersebut; Guru Piket **bukan** role baru (PRD 01 §7.4, §13.8; ADDENDUM §8, §12).
- Supervisor — monitoring baca-saja atas **seluruh** guru (ADR 09), approve / minta revisi (catatan ≥10 karakter), revoke approval (ADR 05) (PRD 01 §8.2–§8.3).
- Supervisor **DILARANG**: mengedit berkas guru, membuka sesi absensi kelas, melihat audit log, mengubah jadwal piket, mengubah master data (PRD 01 §5.3, §8; ADR 17).

## 5. Shared Area

- **Data absensi sekolah:** ditulis Guru Piket (scan barcode), dikoreksi Admin/TU; jejak audit bersifat *append-only* (PRD 05 §7.1b; PRD 04 §9.2).
- **Dokumen:** Guru mengunggah ↔ Supervisor meninjau ↔ Admin memantau (PRD 05 §6, §15).
- **Jadwal piket:** Admin menulis ↔ Guru mengonsumsi secara *read-only* (ADDENDUM §8–§18).
- **Audit log:** ditulis oleh sistem, dibaca eksklusif Admin/TU (ADR 17).
- Aturan area bersama: hanya pemilik wewenang yang boleh mengubah; pihak lain bersifat baca-saja.

### Batas Tegas Administration vs Academic

- **Administration** memiliki **sistem/modul**: skema basis data, jadwal piket, koreksi absensi, audit log, master data, konfigurasi (bagian **3**).
- **Academic** memiliki **aktivitas pengajaran**: dokumen guru, review Supervisor, absensi siswa per mata pelajaran (bagian **4**).
- **Titik singgung — Scanner absensi masuk/pulang:** **membangun & mengelola = Administration** (ADJUSTMENT D); **mengoperasikan sebagai Guru Piket = Academic (aktor)**. Yang satu memiliki sistem, yang lain menjalankan peran pengguna.
- Tidak ada tumpang tindih kepemilikan: setiap pekerjaan hanya boleh diambil oleh pemiliknya (lihat **14.5 Batas Ownership**).

## 6. Adjustment Roadmap

- **Catatan istilah:** "Adjustment" adalah **penamaan kerja internal (workstream eksekusi)**, bukan istilah yang dipakai PRD. PRD tidak memakai istilah "Adjustment A–E"; roadmap resmi adalah **Fase 1–9** pada PRD 05 §38. Workstream "Adjustment" dipetakan ke fase-fase tersebut dan tidak boleh menggantikan atau menambah keputusan PRD/ADR.
- Fase 1 — Foundation & Authentication; Fase 2 — Master Data & Dynamic Class; Fase 3 — Teaching Assignment & Scheduling; Fase 4 — Student School Attendance & Duty Schedule (ADDENDUM); Fase 5 — Document Repository & Google Drive; Fase 6 — Document Review & Immutable Versioning; Fase 7 — Reporting, Analytics & Audit Logging; Fase 8 — Security Verification & Integration Testing; Fase 9 — Production Hardening & Deployment.
- Status blueprint: PRD 05 §37 — `APPROVED FOR DEVELOPMENT (FINAL — A1/B2/C1 2026-09-27)`; PRD sendiri belum menyatakan tahap koding/testing/deployment/rilis selesai, sehingga status per workstream dilacak di bawah ini.
- **Aturan ketergantungan:** pekerjaan bersifat berurutan (PROMPT 22 → 25, lalu ADJUSTMENT A → E). Workstream berikutnya tidak dimulai sebelum workstream sebelumnya selesai dan tervalidasi. **Cakupan aturan ini:** rantai fondasi PROMPT 22 → 25 dan ADJUSTMENT A → E (dalam ranah ADMINISTRATION / SHARED FOUNDATION). Aturan ini **bukan** phase-lock lintas role — lihat **14.12**.

### Ringkasan Workstream — PROMPT

| Workstream | Ruang Lingkup | Owner | Status |
|---|---|---|---|
| PROMPT 22 — Bootstrap | Inisialisasi fondasi proyek Laravel 13 + Tailwind/Vite | ADMINISTRATION | SELESAI |
| PROMPT 23 — Database Foundation | Fondasi basis data MySQL 8.0+ (konvensi & migrasi dasar) | ADMINISTRATION | SELESAI |
| PROMPT 24 — Authentication | Modul login/logout aman, rate limit, session timeout, recovery sandi | ADMINISTRATION | SELESAI |
| PROMPT 25 — Authorization / RBAC | Penegakan 3 peran (Admin/TU, Guru, Supervisor) server-side, anti-IDOR | ADMINISTRATION / SHARED SECURITY | SELESAI |

### Ringkasan Workstream — ADJUSTMENT

| Workstream | Ruang Lingkup | Owner | Status |
|---|---|---|---|
| ADJUSTMENT A — Database alignment / schema adjustment | Penyelarasan skema basis data agar sesuai PRD final | ADMINISTRATION | SELESAI |
| ADJUSTMENT B — Netralisasi / penghapusan legacy Teacher Attendance | Penghapusan fitur `teacher_attendances` (ADDENDUM §20 & §26; ADR 01–03 `[OBSOLETE]`) | ADMINISTRATION | SELESAI |
| ADJUSTMENT C — Teacher Duty Schedule / Guru Piket | `teacher_duty_schedules` CRUD + audit, status piket dari data (ADDENDUM §8–§18; ADR 19) | ADMINISTRATION | SELESAI |
| ADJUSTMENT D — Student School Attendance Scanner | Scanner barcode masuk/pulang, mode eksplisit MASUK/PULANG, PULANG tanpa MASUK ditolak (ADR 18; Keputusan A1) | ADMINISTRATION | SELESAI |
| ADJUSTMENT E — Configuration & housekeeping | Konfigurasi & housekeeping fondasi (rincian di bawah) | ADMINISTRATION / SHARED FOUNDATION | STATUS WAJIB DIVERIFIKASI ITEM PER ITEM |

### Adjustment E — Item Verification Table

> **STATUS WAJIB DIVERIFIKASI ITEM PER ITEM.** Tidak ada status global untuk Adjustment E.
> Agent **wajib memeriksa repository** sebelum menetapkan status tiap item — dan **jangan mengulang pekerjaan yang ternyata sudah selesai**.
> Status hanya boleh: `COMPLETED`, `PARTIAL`, `NOT_STARTED`, `BLOCKED`.

| E-ID | Item Adjustment E | Status Repository | Evidence | Owner |
|------|-------------------|-------------------|----------|-------|
| E-01 | Timezone `Asia/Jakarta` | COMPLETED | `config/app.php` baris 71: `'timezone' => 'Asia/Jakarta'` (dengan komentar eksplisit bahwa logika tanggal absensi MASUK/PULANG berjalan pada timezone ini) | ADMINISTRATION / SHARED FOUNDATION |
| E-02 | Konfigurasi MySQL (PRD 02 §8; PRD 04 §6) | PARTIAL | `.env` baris 23–26: `DB_CONNECTION=mysql`, `DB_HOST=127.0.0.1`, `DB_PORT=3306`, `DB_DATABASE=portal_sekolah`; `config/database.php` baris 56–61: `utf8mb4` / `utf8mb4_unicode_ci` / `strict=true`. **Belum tuntas:** `'engine' => null` sehingga InnoDB tidak dipatok eksplisit, dan `.env.example` baris 23 masih `DB_CONNECTION=sqlite` | ADMINISTRATION / SHARED FOUNDATION |
| E-03 | Housekeeping middleware pipeline (PRD 02 §6) | COMPLETED | `bootstrap/app.php` baris 18–45: alias `role` + `on.duty`; `EnsureUserIsActive` global **tepat satu kali** (duplikasi lama sudah dibersihkan — tercatat pada komentar baris 30–33); `SetSecurityHeaders` di-`prepend` agar berlaku global | ADMINISTRATION / SHARED FOUNDATION |
| E-04 | Housekeeping artefak default Laravel | NOT_STARTED | Artefak bawaan masih ada: `tests/Feature/ExampleTest.php`, `tests/Unit/ExampleTest.php`, `resources/views/welcome.blade.php`; `.env.example` masih `APP_NAME=Laravel` + `DB_CONNECTION=sqlite`. Belum ada keputusan → **tidak boleh dihapus** sebelum diputuskan | ADMINISTRATION / SHARED FOUNDATION |
| E-05 | AuditLog constants / compatibility (PRD 04 §9.1) | PARTIAL | `app/Models/AuditLog.php`: tersedia `LOGIN_SUCCESS`, `LOGIN_FAILED`, `LOGOUT`, `DUTY_SCHEDULE_CREATE/UPDATE/DELETE`, `SCHOOL_ATT_SCAN`, `SCHOOL_ATT_CORRECT`. **Selisih dengan PRD 04 §9.1:** PRD menulis `AUTH_LOGIN_SUCCESS` / `AUTH_LOGIN_FAILED` / `AUTH_LOGOUT`, repo memakai `LOGIN_*` / `LOGOUT` → **perlu keputusan manusia** mana yang canonical (agent tidak boleh mengarang). Event `USER_*`, `DOC_*`, `ATT_SESSION_*`, `ATT_STUDENT_CORRECT`, `ACADEMIC_YEAR_CHANGE`, `TEACHING_ASSIGN_EDIT` belum ada karena fiturnya belum dibangun | ADMINISTRATION / SHARED FOUNDATION |
| E-06 | Verification (`php artisan test` + gate keamanan) | NOT_STARTED | Suite test ada (8 feature test + 2 unit test), tetapi **verifikasi penutup Adjustment E belum dijalankan / belum dicatat** — belum ada commit Adjustment E | ADMINISTRATION / SHARED FOUNDATION |
| E-07 | Git checkpoint | NOT_STARTED | Working tree bersih pada commit `c71eeb0`; belum ada commit khusus Adjustment E | ADMINISTRATION / SHARED FOUNDATION |

### Catatan Ownership

- **Adjustment E BUKAN Academic.** Adjustment E berada pada ranah **ADMINISTRATION / SHARED FOUNDATION** (konfigurasi, konsistensi fondasi, housekeeping), bukan ranah Guru/Supervisor.
- **Academic adalah workstream terpisah** yang mengacu pada canonical PRD (repositori dokumen guru, versioning, review Supervisor, absensi siswa per mapel) — lihat bagian **4. Academic Ownership**.
- **Adjustment E tetap `Configuration & Housekeeping`** dan **BUKAN Academic**. Tidak ada penulisan ulang Adjustment E menjadi Academic di dokumen ini.
- Setelah seluruh Adjustment selesai, pekerjaan Administration dilanjutkan pada bagian berikutnya: **Administration Workstream Roadmap**.

---

## Administration Workstream Roadmap

> **Posisi:** melengkapi **6. Adjustment Roadmap**. Semua item di bawah adalah **ownership ADMINISTRATION** (role Admin / Staff TU) menurut PRD 01 §6, PRD 02 §6 & §11–§16, PRD 05 §5.2–§5.9 / §5.15–§5.17, dan ADR 10 / ADR 17. **Tidak ada fitur baru** — setiap baris harus dapat dilacak ke requirement PRD.
>
> **Status WAJIB diverifikasi dari repository aktual** (bukan dari dokumen/report lama). Snapshot di bawah diperiksa pada commit `c71eeb0` (21 migrasi, `php artisan route:list` = **19 rute aplikasi**).

| ID | Administration Workstream | Scope | Dependency | PRD Reference | Repository Evidence | Status |
|----|---------------------------|-------|------------|---------------|---------------------|--------|
| ADM-01 | Dashboard Admin | Metrik sekolah: total guru/siswa aktif, total rombel berjalan, total dokumen per status, status absensi masuk/pulang hari ini, Guru Piket hari ini, peringatan dokumen & absensi | PROMPT 25 ✅; metrik penuh butuh ADM-02/03/04/06 + Academic A-03 | PRD 01 §6.1 (ADM-DASH-001), PRD 05 §5.2, §17.1 | Rute `admin.dashboard` → `RoleDashboardController@admin()` → `dashboards/role.blade.php` (kerangka pemisahan area, **tanpa** metrik ADM-DASH-001) | PARTIAL |
| ADM-02 | Data Master Guru | CRUD + soft delete + pencarian guru; atribut NIP/NUPTK, nama, jenis kelamin, email, telepon/WA, alamat, status kepegawaian, status akun | PROMPT 23 ✅ (`teachers`), PROMPT 24 ✅ (`users`), ADM-08 *(ADM-USR-001: akun dibuat otomatis)* | PRD 01 §6.2 (ADM-GUR-001–002), PRD 05 §5.3, PRD 02 §6 `/admin/teachers` | Tabel `teachers` + model `Teacher` (`user_id` unique FK, `nip`, `full_name`, `gender`, `phone_number`, `employment_status`, `address`) tersedia & terisi seeder; **tidak ada** rute/controller/view `admin.teachers` | NOT_STARTED |
| ADM-03 | Data Master Siswa | CRUD, pindah status kelas, nonaktifkan siswa; atribut NIS/NISN/nama/L-P/tgl lahir/orang tua/kontak darurat/kelas/status; **import massal CSV/XLSX + validasi** | ADM-04 *(rombel & enrollment)* | PRD 01 §6.3 (ADM-SIS-001–003), PRD 05 §5.4, PRD 02 §6 `/admin/students` | Tabel `students` + `barcode_code` (ADJUSTMENT A) + model `Student` tersedia; **tidak ada** rute/controller; **tidak ada** fitur import CSV/XLSX | NOT_STARTED |
| ADM-04 | Kelas & Tingkat / Dynamic Class | Kelola jenjang (grade level), rombel dinamis per tahun ajaran, penetapan Wali Kelas, pendaftaran siswa (class enrollment) | PROMPT 23 ✅, ADM-02, ADM-06 | PRD 01 §6.4 (ADM-KLS-001–004), PRD 05 §5.5 & §8, PRD 02 §6 & §11 | Tabel `grade_levels`, `classes` (`uq_classes_year_name`, `homeroom_teacher_id` SET NULL), `class_enrollments` (`uq_class_enrollments_student_yr`) + model tersedia; **tidak ada** rute/controller | NOT_STARTED |
| ADM-05A | Data Master Mata Pelajaran | Kelola katalog mapel: kode mapel **unik**, nama mapel, kategori (UMUM / MULOK / PEMINATAN). Mapel yang sudah punya riwayat penugasan **tidak dapat dihapus sembarangan** | PROMPT 23 ✅ (`subjects`), PROMPT 25 ✅. **Tidak punya dependency Administration** (master data / leaf) | PRD 01 §6.5 (ADM-MPL-001), PRD 05 §5.7, PRD 02 §6 `/admin/subjects` | Tabel `subjects` (`code` unique, `name`, `category` default `UMUM`) + model `Subject` tersedia & diisi seeder; **tidak ada** rute/controller/view `admin.subjects`; **kolom beban jam/minggu belum ada** (lihat Catatan Temuan no.1) | NOT_STARTED |
| ADM-05B | Jadwal Pelajaran | Jadwal pelajaran mingguan **per penugasan mengajar**: hari (1–7), jam mulai, jam selesai; aturan `end_time > start_time`; penolakan tumpang tindih (*time conflict*) guru/ruang | **ADM-07 wajib selesai lebih dulu** (PRD 05 §5.8: penugasan = prasyarat mutlak sebelum jadwal dibuat); PROMPT 23 ✅ (`schedules`) | PRD 01 §6.5 (ADM-JDW-001), PRD 05 §5.9, PRD 02 §6 `/admin/schedules` & §12.2 | Tabel `schedules` (`teaching_assignment_id` FK cascade, `day_of_week`, `start_time`, `end_time`, `idx_schedules_lookup`) + model `Schedule` tersedia; **tidak ada** rute/controller; **tidak ada** validasi anti-tumpang-tindih | NOT_STARTED |
| ADM-06 | Tahun Akademik & Semester | Kelola tahun ajaran + semester Ganjil/Genap; tetapkan **tepat 1** periode AKTIF; penutupan periode (archiving/closure) agar histori terkunci | PROMPT 23 ✅ | PRD 01 §6.6 (ADM-TA-001–003), PRD 05 §5.6, PRD 02 §6 `/admin/academic-years` | Tabel `academic_years` (`name`, `semester`, `is_active`, `start_date`, `end_date`, `uq_academic_years_name_sem`) + model tersedia & diisi seeder; **tidak ada** rute/controller UI; **belum ada** enforcement "tepat 1 aktif" di level aplikasi maupun DB; **belum ada** mekanisme closure/archiving | NOT_STARTED |
| ADM-07 | Teaching Assignment (Penugasan Mengajar) | Relasi banyak-ke-banyak Guru × Mapel × Kelas per tahun ajaran; validasi anti-duplikasi penugasan (kecuali *team teaching*) | ADM-02, ADM-04, ADM-05A, ADM-06 | PRD 01 §6.7 (ADM-TAS-001–002), PRD 05 §5.8 & §9, PRD 02 §6 & §12.1 | Tabel `teaching_assignments` + `uq_teaching_assignments_unique` (FK `restrict`) + model `TeachingAssignment` + `TeachingAssignmentPolicy` tersedia; **tidak ada** rute/controller; opsi *team teaching* PRD 01 §6.7 belum punya representasi data | NOT_STARTED |
| ADM-08 | User Account & Security | Pembuatan akun otomatis saat data guru/staff dibuat; reset kata sandi manual (ADR 10); penguncian akun sementara akibat login gagal berulang (brute-force throttling) | PROMPT 24 ✅, PROMPT 25 ✅ | PRD 01 §6.8 (ADM-USR-001–003), PRD 05 §5.1, ADR 10; PRD 04 §3.1 | **Sebagian sudah ada:** `RateLimiter` maks 5 percobaan/60 detik + audit `LOGIN_FAILED` alasan `rate_limited`, `session()->regenerate()`/`regenerateToken()`, `users.is_active` + middleware global `EnsureUserIsActive` (semuanya terverifikasi di `LoginController` & `bootstrap/app.php`). **Belum ada:** CRUD akun admin, UI reset password manual TU (ADR 10), dan pembuatan akun otomatis dari modul guru/staff | PARTIAL |
| ADM-09 | Monitoring & Koreksi Absensi | ADM-ABS-001 monitoring absensi masuk/pulang harian; ADM-ABS-002 monitoring rekap kehadiran per kelas & per sesi mapel; ADM-ABS-003 koreksi dengan alasan ≥10 karakter + penanda `DIKOREKSI_ADMIN` | ADJUSTMENT A/C/D ✅ *(masuk/pulang)*; rekap mapel butuh Academic A-05 | PRD 01 §6.9 (ADM-ABS-001–003) & §13.7, PRD 05 §5.10, PRD 02 §6 & §14.2 | **Sudah ada & teruji:** rute `admin.school-attendances.index/correct/update` + `SchoolAttendanceController` (index/edit/update) + `AttendanceCorrectionService::correct()` + `SchoolAttendancePolicy::correct()` + view `index`/`correct` + audit `SCHOOL_ATT_CORRECT` + alasan wajib ≥10 karakter (kolom `is_corrected`, `corrected_by_user_id`, `correction_reason` tersedia). **Belum ada:** ADM-ABS-002 rekap kehadiran per kelas/mapel | PARTIAL |
| ADM-10 | Audit Trail & System Log | Pencatatan otomatis tindakan penting (termasuk perubahan jadwal piket); antarmuka pencarian & filter log per tanggal/pengguna/modul/tipe aksi; akses eksklusif Admin/TU | ADJUSTMENT C/D ✅; E-05 *(nama konstanta)* | PRD 01 §6.10 (ADM-LOG-001–002) & §16.3, PRD 05 §5.16 & §24, PRD 04 §9, ADR 17, PRD 02 §16 | **Sisi tulis berjalan:** tabel `audit_logs` (append-only, `old_values`/`new_values` JSON, `ip_address`, `user_agent`, index aktor & entitas) + model `AuditLog` (8 konstanta) + `AuditService` yang benar-benar dipakai modul ADJUSTMENT C & D. **Belum ada:** rute/view `/admin/audit-logs` + filter ADM-LOG-002; cakupan event PRD 04 §9.1 masih sebagian (lihat **E-05**) | PARTIAL |
| ADM-11 | Laporan & Ekspor Data | Rekap absensi masuk/pulang harian & bulanan (termasuk identitas Guru Piket operator), rekap kehadiran per mapel/kelas, riwayat jadwal piket, matriks kelengkapan dokumen; filter rentang tanggal/tahun ajaran/status; ekspor **CSV & XLSX (MUST)**; PDF ber-kop `[SHOULD HAVE]` | ADM-01 … ADM-10 termasuk ADM-05A & ADM-05B *(data sumber)* | PRD 01 §16.1–§16.2, PRD 05 §5.15 & §34, PRD 02 §6 `/admin/reports` | `route:list` **tidak** menampilkan rute report/export; pencarian `csv|xlsx|export` di `app/Http/Controllers` **0 hasil** | NOT_STARTED |

### Audit Dependency Administration (Siklus Ditemukan & Diperbaiki)

- **SEBELUM (cycle):** `ADM-05 ← ADM-07` **dan** `ADM-07 ← ADM-05` → **CIRCULAR DEPENDENCY FOUND**. Roadmap tidak dapat menghasilkan `NEXT ELIGIBLE TASK` yang pasti pada pasangan ini.
- **SESUDAH (dipisah berdasarkan PRD):**
  - `ADM-05A` (Data Master Mata Pelajaran) ← **tidak ada** pendahulunya di Administration → sesuai PRD 05 §5.7 (modul ini hanya merekam kode/nama/kategori mapel).
  - `ADM-07` (Teaching Assignment) ← `ADM-02`, `ADM-04`, `ADM-05A`, `ADM-06` → sesuai rumus PRD 02 §12.1 `Assignment = f(Teacher, Subject, Class, Academic Year)`.
  - `ADM-05B` (Jadwal Pelajaran) ← `ADM-07` → sesuai PRD 05 §5.8 (*penugasan adalah prasyarat mutlak sebelum jadwal dibuat*) & PRD 02 §12.2 (`schedules` menunjuk `teaching_assignment_id`).
  - **Tidak ada lagi dependensi dua arah. DEPENDENCY CYCLE: NONE.**
- **Koreksi dependency lama yang tidak didukung PRD/skema** (bukan dependency baru):
  - `ADM-02` **tidak lagi** bergantung `ADM-06` — tabel `teachers` tidak memiliki `academic_year_id` dan PRD 01 §6.2 tidak menyebut tahun ajaran; dependency resmi yang tersisa adalah ADM-08 (PRD 01 §6.8 ADM-USR-001: akun dibuat otomatis saat data guru dibuat).
  - `ADM-03` **tidak lagi** bergantung `ADM-02` — `students` tidak memiliki FK ke `teachers` dan PRD 01 §6.3 tidak merujuk guru; yang dibutuhkan hanya `ADM-04` (rombel + `class_enrollments`).
- **Definisi `BLOCKED` diperketat:** ketergantungan pada workstream Administration lain yang **belum dikerjakan** TIDAK membuat task jadi `BLOCKED`; ia hanya **belum eligible** dan ditentukan oleh Urutan Eksekusi Topologis di bawah. `BLOCKED` dipakai bila dependency berupa **struktur/layanan yang belum ada di repository** (mis. `GoogleDriveService`) atau **keputusan manusia yang belum keluar** (mis. E-05).

### Urutan Eksekusi Topologis (bebas siklus)

Urutan ini yang menentukan pekerjaan mana boleh dikerjakan lebih dahulu. **Nomor ID bukan urutan eksekusi** (karena itu `ADM-05B` berada setelah `ADM-07`).

| # | Workstream | Dependency | Eligible sekarang? |
|---|---|---|---|
| 1 | ADJUSTMENT E (E-02, E-04, E-05, E-06, E-07) | PROMPT + ADJUSTMENT A–D sudah SELESAI | **YA — prioritas tertinggi** (§14.11 langkah 2) |
| 2 | ADM-06 Tahun Akademik & Semester | PROMPT 23 ✅ | YA |
| 3 | ADM-05A Data Master Mata Pelajaran | PROMPT 23 ✅ | YA |
| 4 | ADM-08 User Account & Security | PROMPT 24 ✅, PROMPT 25 ✅ | YA (lanjutkan bagian yang belum ada) |
| 5 | ADM-10 Audit Trail & System Log | ADJUSTMENT C/D ✅; **sisa butuh keputusan E-05** | YA untuk UI baca log; kode aksi menunggu E-05 |
| 6 | ADM-02 Data Master Guru | butuh ADM-08 selesai (ADM-USR-001) | BELUM |
| 7 | ADM-04 Kelas & Tingkat | butuh ADM-02, ADM-06 | BELUM |
| 8 | ADM-03 Data Master Siswa | butuh ADM-04 | BELUM |
| 9 | ADM-07 Teaching Assignment | butuh ADM-02, ADM-04, ADM-05A, ADM-06 | BELUM |
| 10 | ADM-05B Jadwal Pelajaran | butuh ADM-07 | BELUM |
| 11 | ADM-09 Monitoring & Koreksi Absensi | masuk/pulang sudah ✅; **ADM-ABS-002 butuh Academic A-05** | SEBAGIAN |
| 12 | ADM-01 Dashboard Admin | metrik non-dokumen butuh ADM-02/03/04/06; metrik dokumen butuh A-03 | BELUM (sisa) |
| 13 | ADM-11 Laporan & Ekspor | butuh ADM-01 … ADM-10 (termasuk ADM-05A/05B) | BELUM |

### Aturan Status Administration

- **Status tidak boleh dipercaya hanya dari dokumen 07.** Agent wajib memverifikasi ulang terhadap repository aktual (`route:list`, migrasi, controller, service, policy, view, test) pada setiap sesi.
- **Jangan mengambil task `COMPLETED` atau `PARTIAL` yang sudah tuntas bagiannya** sebagai pekerjaan baru — lanjutkan hanya bagian yang belum ada (aturan "jangan mengulang pekerjaan yang sudah selesai").
- Task `BLOCKED` tidak boleh diusulkan sebagai `NEXT ELIGIBLE TASK`; laporkan sebagai halangan.
- **Batas:** seluruh ADM-01 … ADM-11 adalah **ADMINISTRATION**. Academic (A-01 … A-08) tidak termasuk dan **tidak boleh** diambil saat role `ADMINISTRATION`.
- `PARTIAL` = sebagian requirement sudah terbukti di repository; `NOT_STARTED` = requirement ada di PRD, dependency tersedia, implementasi belum dibuat sama sekali.

### Catatan Temuan Verifikasi Repository

Temuan nyata hasil pemeriksaan repository (bukan asumsi) — masing-masing perlu keputusan manusia sebelum diperlakukan sebagai requirement tambahan:

1. `subjects` **tidak memiliki** kolom beban jam/minggu, padahal PRD 01 §6.5 (ADM-MPL-001) mensyaratkannya.
2. `academic_years` **tidak memiliki** penegakan "tepat 1 AKTIF" (PRD 01 §6.6 ADM-TA-002) maupun status periode untuk closure/archiving (ADM-TA-003).
3. `students.status` memakai nilai `AKTIF, LULUS, MUTASI, KELUAR`, sedangkan PRD 01 §6.3 menuliskan Aktif/Lulus/**Pindah**/Keluar.
4. `teaching_assignments` **tidak memiliki** penanda *team teaching* yang disinggung PRD 01 §6.7 (ADM-TAS-002).
5. Nama konstanta audit repository (`LOGIN_*`, `LOGOUT`) berbeda dari prefix `AUTH_*` pada PRD 04 §9.1 & PRD 05 §24 — lihat **E-05**.

---

## Academic Workstream Roadmap

> **Posisi:** bagian ini melengkapi **Administration Workstream Roadmap** (yang merupakan workstream Administration pasca-Adjustment) dan **6. Adjustment Roadmap**. Semua item di bawah adalah **ownership ACADEMIC** (role Guru + Supervisor) menurut PRD 01 §7–§8 dan PRD 05 §5.11–§5.14. **Tidak ada fitur baru** yang ditambahkan — setiap baris harus dapat dilacak ke requirement PRD.
>
> Seluruh `Status` di bawah **diverifikasi langsung terhadap isi repository**, bukan asumsi.

| ID | Academic Workstream | Scope | Dependency | PRD Reference | Repository Evidence | Status |
|----|---------------------|-------|------------|---------------|---------------------|--------|
| A-01 | Dashboard Guru | Widget jadwal mengajar hari ini; status Guru Piket hari ini (banner + tombol scanner); ringkasan status berkas ajar (Draft / Menunggu Review / Perlu Revisi / Disetujui) | PROMPT 24 ✅, PROMPT 25 ✅, ADJUSTMENT C ✅ (`teacher_duty_schedules`) | PRD 01 §7.1 (GUR-DASH-001–003) | `routes/web.php` `guru.dashboard` → `RoleDashboardController::guru()` → `resources/views/dashboards/role.blade.php` (kerangka minimal — **tanpa** widget GUR-DASH) | PARTIAL |
| A-02 | Profil Guru | Biodata pribadi + riwayat penugasan mengajar; ubah kata sandi mandiri + nomor kontak | PROMPT 24 ✅, PROMPT 25 ✅ | PRD 01 §7.2 (GUR-PRF-001–002) | Tidak ada rute/controller/view profil pada grup `guru` di `routes/web.php` | NOT_STARTED |
| A-03 | Repositori Dokumen Guru | Repositori per Tahun Ajaran & Kategori (RPP/ATP/PROTA/PROSEM/Lainnya); unggah berkas baru (PDF/DOCX/XLSX, maks 10 MB, `DRAFT`); submit for review; lihat feedback & riwayat versi | GoogleDriveService (PRD 03 / Fase 5) — **belum ada** | PRD 01 §7.3 (GUR-DOC-001–004), PRD 05 §5.12 | Model `Document`/`DocumentVersion`/`DocumentReview`/`DocumentComment` + `DocumentPolicy` ada; `app/Services/` **tidak memuat** `GoogleDriveService`; tidak ada rute dokumen | BLOCKED |
| A-04 | Document Versioning & Permanent Retention | Unggah revisi → versi baru otomatis (v+1) tanpa menimpa versi lama; retensi permanen di Drive | A-03 | PRD 01 §7.3 (GUR-DOC-005), PRD 05 §5.13 (ADR 08) | Kolom + `UNIQUE(document_id, version_number)` ada di migrasi `2026_09_22_000010_create_documents_tables.php`; belum ada service/alur versi | BLOCKED |
| A-05 | Absensi Siswa per Mata Pelajaran | Buka sesi (`OPEN`) untuk kelas/mapel yang diampu; pre-fill seluruh siswa `HADIR`; status H/I/S/A + catatan; simpan & kunci (`CLOSED`); read-only pasca `CLOSED`; manual close | `teaching_assignments` ✅, `students` ✅, `class_enrollments` ✅, `student_attendance_sessions` ✅, `student_attendance_records` ✅ | PRD 01 §7.5 (GUR-ATT-001–004), PRD 05 §5.11 (ADR 14) | Model `StudentAttendanceSession`/`StudentAttendanceRecord` + `StudentAttendanceSessionPolicy` + `TeachingAssignmentPolicy` ada; **tidak ada** rute/controller/service | NOT_STARTED |
| A-06 | Dashboard Supervisor | Ringkasan progres pengumpulan dokumen seluruh guru; antrean `DIKIRIM`/`REVISI`; distribusi status dokumen; filter rumpun mapel / tingkat kelas | PROMPT 24 ✅, PROMPT 25 ✅; A-03 *(untuk metrik nyata)* | PRD 01 §8.1 (SUP-DASH-001–002) | `routes/web.php` `supervisor.dashboard` → `RoleDashboardController::supervisor()` → `dashboards/role.blade.php` (minimal) | PARTIAL |
| A-07 | Monitoring Repositori Guru | Daftar seluruh guru + buka repositori masing-masing; matriks kepatuhan dokumen per guru | A-03 | PRD 01 §8.2 (SUP-REP-001–002) | Tidak ada rute/controller monitoring repositori guru | BLOCKED |
| A-08 | Review, Evaluasi & Approval Dokumen | Preview/unduh berkas guru; jaminan Supervisor tidak dapat mengubah berkas guru; `SETUJUI` atau `MINTA REVISI` (catatan wajib ≥10 karakter); version tracking; revoke approval | A-03 | PRD 01 §8.3 (SUP-REV-001–004), PRD 05 §5.14 (ADR 05) | `DocumentPolicy::review()` sudah menegakkan supervisor-only (defense in depth), tetapi **tidak ada** rute/controller alur review | BLOCKED |

### Aturan Kepercayaan Status Academic

- **Status Academic Workstream tidak boleh dipercaya hanya dari dokumen 07.** Agent **wajib memverifikasinya terhadap repository aktual** pada setiap sesi.
- Status yang tertulis di dokumen ini adalah **snapshot**; bila repository berbeda, **repository yang berlaku** dan dokumen harus dikoreksi.
- Tidak ada status `COMPLETED` untuk Academic tanpa bukti rute + controller + view/test di repository.

### Legend Status (berlaku untuk Adjustment E, Administration Workstream Roadmap, dan Academic Workstream Roadmap)

- `COMPLETED` — implementasi **terbukti di repository** (rute + controller + view/test).
- `PARTIAL` — implementasi **parsial** (baru kerangka/fondasi area).
- `NOT_STARTED` — belum dibuat; **dependency tersedia**.
- `BLOCKED` — **dependency belum tersedia**, termasuk dependency berupa **shared dependency yang belum punya owner** (lihat **Shared Dependency Ownership**, aturan SD-O2).

### Aturan Dependency untuk Memilih Task Academic

- Academic task baru **boleh dipilih** hanya jika **dependency-nya benar-benar tersedia** (diverifikasi di repository, bukan diasumsikan dari dokumen).
- Sebelum memilih task Academic, agent wajib memeriksa ketersediaan dependency berikut bila relevan:

| Dependency | Wujud yang wajib diperiksa | Tersedia? |
|---|---|---|
| `students` | tabel + model `Student` | ✅ |
| `teachers` | tabel + model `Teacher` | ✅ |
| `classes` | tabel + model `ClassRoom` | ✅ |
| `subjects` | tabel + model `Subject` | ✅ |
| `class_enrollments` | tabel + model `ClassEnrollment` | ✅ |
| `teaching_assignments` | tabel + model `TeachingAssignment` + `TeachingAssignmentPolicy` | ✅ |
| `student_attendance_sessions` | tabel + model `StudentAttendanceSession` + `StudentAttendanceSessionPolicy` | ✅ |
| `student_attendance_records` | tabel + model `StudentAttendanceRecord` | ✅ |
| *(tambahan)* GoogleDriveService | `app/Services/GoogleDriveService.php` (PRD 03) | ❌ **belum ada** |

- Jika dependency relevan **belum tersedia** → task berstatus `BLOCKED` dan **tidak boleh** diusulkan sebagai `NEXT ELIGIBLE TASK`.

### Batas Academic — Yang DILARANG Diambil Academic

Academic **dilarang** mengambil ownership:

- Teacher Duty Schedule (milik ADMINISTRATION — ADJUSTMENT C)
- School Attendance Scanner (milik ADMINISTRATION — ADJUSTMENT D)
- School Attendance Monitoring / Correction (milik ADMINISTRATION)
- Administration-only feature (master data, panel audit log, konfigurasi, dan modul Admin/TU lainnya)

> **Catatan ketergantungan kritis:** mayoritas workstream Academic (A-03, A-04, A-07, A-08) terhalang **GoogleDriveService** — integrasi Google Drive (PRD 03 / Fase 5) — yang **belum dibangun dan belum dimiliki workstream mana pun** pada roadmap saat ini. Status `BLOCKED` di atas adalah kondisi nyata repository, bukan asumsi. Ownership integrasi Google Drive perlu ditetapkan oleh manusia (lihat bagian **13. Stop Conditions** dan **Shared Dependency Ownership** di bawah).

## Shared Dependency Ownership

> **Posisi:** aturan ini berada setelah kedua roadmap karena objeknya adalah **dependency yang dipakai silang workstream**. Aturan ini **tidak mengubah** requirement canonical PRD 01–06, roadmap **ADJUSTMENT A–E**, roadmap **ADM-01 ... ADM-11**, maupun roadmap **A-01 ... A-08**; yang diatur di sini **hanya penetapan pemilik (ownership)** dan konsekuensi statusnya.

### Aturan

- **SD-O1 — Wajib punya owner.** Shared dependency yang digunakan oleh **lebih dari satu workstream** wajib mempunyai **satu owner** yang ditetapkan secara eksplisit.
- **SD-O2 — Tanpa owner = `BLOCKED`.** Bila owner belum ditetapkan, seluruh workstream yang bergantung padanya berstatus **`BLOCKED`** (bukan `NOT_STARTED`) dan **tidak boleh** diusulkan sebagai `NEXT ELIGIBLE TASK`.
- **SD-O3 — AI dilarang memilih owner sendiri.** Penetapan owner adalah **keputusan manusia**. AI dilarang menyimpulkan owner dari nama modul, peran, letak direktori, atau roadmap.
- **SD-O4 — Wajib meminta keputusan.** Bila ditemukan shared dependency tanpa owner → **STOP + HUMAN DECISION REQUIRED / OWNER ASSIGNMENT** (lihat **13.2**) dan tulis pada field **HUMAN DECISION** (§15.2).
- **SD-O5 — Owner = implementer tunggal.** Setelah owner ditetapkan, **hanya owner tersebut** satu-satunya role yang boleh mengimplementasikan / mengubah shared dependency itu.
- **SD-O6 — Role lain hanya mengonsumsi.** Role lain **boleh memakai** dependency tersebut (memanggil layanan, membaca hasil) tetapi **tidak boleh mengubah implementasinya** tanpa mengikuti **Aturan Shared-Change** di bawah.
- **SD-O7 — Perubahan wajib cek dampak.** Setiap perubahan pada shared dependency **wajib** diperiksa dampaknya terhadap **seluruh consumer** yang terdaftar, dan hasilnya dilaporkan **sebelum** perubahan dijalankan (selaras dengan **14.6 Shared File**, **9. Anti Conflict**, **14.12**).

### Aturan Shared-Change (syarat SD-O6 & SD-O7)

Perubahan shared dependency **oleh non-owner** hanya boleh dikerjakan bila **seluruh** langkah berikut terpenuhi:

1. Owner perubahan sudah **dikonfirmasi oleh manusia** (atau perubahan memang dikerjakan oleh owner yang telah ditetapkan);
2. Daftar **consumer** terdampak sudah **diperiksa pada repository aktual** dan dilaporkan;
3. Perubahan **tidak memutus kontrak** yang dipakai consumer; bila kontrak berubah, seluruh consumer terdampak diperbarui dalam task yang sama atau ditandai `BLOCKED`;
4. Test/regression terdampak **dijalankan dan hijau**, serta gerbang keamanan §11 tetap terpenuhi.

Bila salah satu langkah tidak terpenuhi → **STOP + HUMAN DECISION REQUIRED**.

### Daftar Shared Dependency

| Shared Dependency | Owner | Status | Consumers | Dasar PRD | Bukti Repository |
|---|---|---|---|---|---|
| Google Drive Integration / `GoogleDriveService` | **HUMAN DECISION REQUIRED** | **BLOCKED** | A-03, A-04, A-07, A-08 | PRD 03 (integrasi Google Drive), PRD 05 Fase 5 — Document Repository & Google Drive | `app/Services/` **tidak memuat** `GoogleDriveService`; tidak ada rute dokumen; consumer tercantum pada roadmap Academic |

### Google Drive Integration / `GoogleDriveService`

- **Owner:** `HUMAN DECISION REQUIRED` — belum ditetapkan. **Tidak boleh** diisi `ADMINISTRATION` maupun `ACADEMIC` secara otomatis oleh AI (SD-O3).
- **Status:** `BLOCKED` (SD-O2).
- **Consumers:**
  - **A-03** — Repositori Dokumen Guru
  - **A-04** — Document Versioning & Permanent Retention
  - **A-07** — Monitoring Repositori Guru
  - **A-08** — Review, Evaluasi & Approval Dokumen
- **Konsekuensi:** selama owner belum ditetapkan, keempat consumer di atas **tetap `BLOCKED`** dan tidak boleh diambil oleh role mana pun. Task yang **tidak** bergantung pada `GoogleDriveService` tetap dapat berjalan (lihat **14.12**).
- **Yang dibutuhkan manusia:** (a) menetapkan owner `GoogleDriveService`, (b) mengonfirmasi bahwa penetapan tersebut tidak mengubah keputusan PRD 03 / Fase 5.

---

## 7. Progress Detection `[BARU]`

- Deteksi progres per fase: implementasi selesai + test lulus + laporan konsisten dengan PRD.
- Sumber bukti: riwayat **git log/status** + hasil test + artefak laporan; tidak ada progres resmi tanpa bukti (commit + test).
- Usulan: tabel status per fase (Belum / Berjalan / Selesai) sebagai papan pemantauan tunggal.

## 8. Next Task Selection `[BARU]`

- Prinsip utama: **selesaikan pekerjaan yang belum tuntas sebelum memulai yang baru**.
- Urutan pemilihan tugas mengikuti Fase 1 → 9 (PRD 05 §38), kecuali ada arahan eksplisit pengguna. Urutan fase ini adalah urutan **implementasi kanonik**, bukan larangan berjalan paralel antar role; pemblokiran lintas role hanya terjadi bila ada **dependency nyata** yang belum tersedia (lihat **14.12**).
- Tugas baru wajib berada dalam **In Scope** (PRD 05 §3.1) dan tidak melanggar **Out of Scope** (PRD 05 §3.2).
- Satu sesi = satu tugas jelas dengan kriteria selesai yang dapat diverifikasi.

## 9. Anti Conflict `[BARU]`

- Satu berkas fisik = satu pemilik pada satu waktu; hindari pengeditan paralel.
- Hormati kepemilikan area pada bagian 3, 4, 5; jangan menyentuh area milik role lain.
- Dilarang mengubah keputusan final PRD/ADR tanpa persetujuan pengguna; dilarang menghidupkan fitur yang sudah dihapus (mis. Teacher Attendance / `teacher_attendances`).
- Larangan keras PRD (ADDENDUM §8/§20/§26; PRD 05 §3.2): bukan role "piket", bukan `users.is_piket`, tanpa RFID/NFC/chip/GPS, tanpa React/Vue, tanpa Redis/WebSocket/queue/microservice.

## 10. Git Workflow `[BARU]`

- Commit kecil & atomik dengan pesan deskriptif dan berawalan tipe (`docs:`, `feat:`, `fix:`, `test:`).
- Sebelum commit: `git status`, `git diff --stat`, `git diff --check` bersih; test lulus.
- Tidak melakukan commit bila test merah atau working tree memuat perubahan tak terkait.
- Dilarang rewrite history / force-push tanpa izin; dilarang commit `.env` atau kredensial (PRD 03 §18.1; PRD 04 §4).
- Verifikasi commit terakhir sebelum menutup sesi.

## 11. Security Development Gate

### 11.1 Sumber Requirement Keamanan (Wajib Dibaca)

**SETIAP development wajib memeriksa requirement dari:**

- `04-security-operations.md` — keamanan & operasional
- `06-decision-resolution.md` — keputusan final / ADR

### 11.2 Checklist Requirement Minimum per Perubahan

Minimal wajib diperiksa:

- [ ] authentication
- [ ] authorization
- [ ] role boundary
- [ ] IDOR
- [ ] validation
- [ ] CSRF
- [ ] input whitelist
- [ ] database integrity
- [ ] audit
- [ ] sensitive data exposure
- [ ] error handling
- [ ] debug exposure
- [ ] session security
- [ ] security headers
- [ ] race condition *(jika relevan)*
- [ ] upload security *(jika relevan)*

### 11.3 Larangan pada Security Gate

- **AI tidak boleh melemahkan security hanya agar fitur berhasil.**
- **AI tidak boleh mengarang requirement final yang belum diputuskan.**
- Jika ada keputusan yang **belum final** → **STOP + HUMAN DECISION REQUIRED**.

### 11.4 Gate Teknis (Rujukan PRD 04)

- Wajib lulus sebelum rilis: pengujian keamanan PRD 04 §16 — rate limit (429 setelah 5 kegagalan), regenerasi sesi, anti-IDOR (403), upload (422/magic bytes), CSRF (419), XSS escaping, SQLi pada filter.
- Wajib: **suite scanner & jadwal piket** (PRD 04 §16 butir 5).
- Pemetaan OWASP **ASVS L1** (PRD 04 §17) dan **OWASP Top 10** (PRD 04 §18).
- Checklist kesiapan produksi A–D (PRD 04 §19) + pemisahan kredensial dev/prod (PRD 04 §20).
- Header keamanan sesuai PRD 04 §5.3: `Permissions-Policy: camera=(self)`, `microphone=()`, `geolocation=()` (ADR 20 / Keputusan B2); **HSTS = kontrol produksi HTTPS** (diverifikasi/didokumentasikan sebagai kontrol production, bukan disimpulkan dari uji HTTP lokal).

## 12. Database Safety

- MySQL 8.0+ engine InnoDB; PK `BIGINT UNSIGNED AUTO_INCREMENT` (ADR 15).
- Dilarang keras `migrate:fresh`; dilarang menjalankan operasi destruktif tanpa izin eksplisit pengguna.
- Mengubah skema **diperbolehkan** bila berdasar requirement PRD/ADR final, dependency & dampak terverifikasi, migration aman, dan test/regression disiapkan — **syarat lengkapnya pada 12.1**. Yang dilarang adalah perubahan skema **tanpa dasar** atau yang **mengubah keputusan final**.
- UNIQUE penting: `student_school_attendances (student_id, attendance_date)`; `teacher_duty_schedules (schedule_date, teacher_id)`; `document_versions (document_id, version_number)` (PRD 02 §17.1).
- `audit_logs` bersifat *append-only*; riwayat absensi historis tidak berubah saat jadwal piket diubah (ADDENDUM §15–§16; PRD 04 §9.2).
- Backup `mysqldump` dengan RPO 24 jam / RTO 8 jam (ADR 12) dan arsip permanen semester (ADR 16).

### 12.1 Perubahan Skema Basis Data — AI BOLEH vs WAJIB STOP `[BARU]`

Aturan lama ("berhenti bila pekerjaan akan mengubah skema basis data") **terlalu luas** dan membuat agent macet. Maknanya diluruskan: perubahan skema adalah pekerjaan biasa **bila** berdasar PRD/ADR dan sudah diverifikasi; yang wajib dihentikan adalah perubahan yang **tidak berdasar, merusak, atau belum jelas dampaknya**.

**AI BOLEH mengubah skema (membuat + menjalankan migration) jika SEMUA syarat berikut terpenuhi:**

- [ ] perubahan **memang diperlukan oleh requirement canonical PRD** (PRD 01–06, dirujuk eksplisit pada task) **atau** sudah ada **decision/ADR berstatus final** yang mendukung;
- [ ] **dependency dan dampak sudah diverifikasi** pada repository aktual — tabel/model terdampak, FK/index/UNIQUE yang terhubung, controller/service/policy, seeder, `route:list`, serta test yang ada (bukan asumsi dari dokumen);
- [ ] **migration aman**: `up()`/`down()` benar dan dapat di-rollback, bersifat *additive* atau disertai rencana migrasi data, tidak menghapus kolom/tabel/data, tidak memutus FK yang sudah dipakai, dan dijalankan dengan `php artisan migrate` biasa (**bukan** `migrate:fresh`);
- [ ] **test/regression disiapkan** — test baru yang membuktikan aturan skema baru, plus suite lama tetap hijau sebelum laporan/commit.

**AI WAJIB STOP + HUMAN DECISION REQUIRED jika:**

- schema change **tidak punya dasar PRD/ADR** (tidak dapat dilacak ke requirement atau keputusan final mana pun);
- perubahan **mengubah/membatalkan keputusan final** PRD/ADR yang sudah berstatus final;
- diperlukan **destructive operation** (`DROP TABLE`/`DROP COLUMN`, `TRUNCATE`, `migrate:fresh`, penghapusan/pembersihan data, atau perubahan yang menghilangkan histori);
- **dampaknya belum jelas** (belum dapat dipastikan fitur/telemetri apa yang terpengaruh, atau belum ada rencana test/regression).

**Tetap berlaku tanpa pengecualian (tidak dilonggarkan oleh bagian ini):**

- **Dilarang keras `migrate:fresh`.**
- **Dilarang operasi destruktif tanpa izin eksplisit pengguna.**
- Seluruh butir **Database Safety** §12 yang lain tetap mengikat.
- **Verifikasi FK / UNIQUE / index / constraint** wajib dilakukan pada **hasil akhir skema** (`migrate:status` + pembacaan file migrasi dan/atau `SHOW CREATE TABLE`), bukan hanya diasumsikan dari kode sumber; setiap selisih dengan PRD 02 dilaporkan sebagai temuan, tidak diperbaiki diam-diam.

## 13. Stop Conditions `[BARU]`

### 13.1 Berhenti & Minta Konfirmasi Manusia

- **Berhenti & minta konfirmasi** bila pekerjaan akan: menyentuh/mengubah **keputusan final** PRD/ADR, **menghapus data**, atau melakukan **perubahan skema yang belum memenuhi seluruh syarat 12.1** (dasar PRD/ADR, verifikasi dependency & dampak, migration aman, test/regression). Perubahan skema yang **sudah** memenuhi seluruh syarat **12.1** boleh dikerjakan tanpa menghentikan task.
- Berhenti bila test merah atau ditemukan perubahan tak terduga pada working tree.
- Berhenti bila tugas di luar In Scope atau melanggar larangan keras PRD.
- Dilarang melanjutkan ke fase berikutnya sebelum gerbang (gate) fase saat ini terpenuhi. **Cakupan:** gate berlaku pada rantai fase/workstream tempat task berada (Fase 1 → 9, PROMPT 22 → 25, ADJUSTMENT A → E) dan pada kriteria selesai task itu sendiri. Gerbang fase **bukan** alasan menunggu role lain — pemblokiran lintas role hanya bila ada **dependency nyata** yang belum tersedia (lihat **14.12**).
- Dilarang menjalankan operasi destruktif (`DROP`, `TRUNCATE`, `migrate:fresh`) tanpa izin eksplisit pengguna.

### 13.2 STOP + HUMAN DECISION REQUIRED

- **AI tidak boleh mengarang requirement final yang belum diputuskan.**
- Jika ada keputusan yang **belum final** (ADR belum `[FINAL]`, atau requirement tidak ditemukan di PRD) → **STOP + HUMAN DECISION REQUIRED**.
- Jika requirement tidak jelas, ambigu, atau bertentangan antar dokumen → **STOP + HUMAN DECISION REQUIRED**.
- AI **dilarang** mengambil keputusan arsitektur/keamanan atas nama manusia, dan **dilarang** mengubah PRD canonical agar cocok dengan source code.
- **AI tidak boleh melemahkan security hanya agar fitur berhasil.**
- Bila ada **shared dependency tanpa owner** (dipakai lebih dari satu workstream, pemilik belum ditetapkan) → **STOP + HUMAN DECISION REQUIRED / OWNER ASSIGNMENT**. AI **dilarang** menetapkan owner sendiri, walaupun kepemilikan tampak "jelas" dari nama modul atau roadmap (lihat **Shared Dependency Ownership** SD-O3/SD-O4).

## 14. Agent Session Workflow `[BARU]`

### 14.1 Langkah 0 — Wajib Menanyakan Ownership

- **Setiap awal sesi coding, AI WAJIB bertanya terlebih dahulu:**
  > "Anda sedang mengerjakan ADMINISTRATION atau ACADEMIC?"
- Nilai sah: **`ADMINISTRATION`** atau **`ACADEMIC`**.
- **AI tidak boleh menebak role.** Bila jawaban tidak jelas atau di luar dua nilai tersebut → **STOP** dan minta keputusan manusia.
- Ownership menentukan area kerja, berkas yang boleh disentuh, dan task yang boleh diambil.

### 14.2 Langkah 1 — Reconnaissance (Wajib Setelah Role Diketahui)

Setelah role diketahui, AI wajib memeriksa secara berurutan:

1. baca canonical PRD (`05-consolidated-prd.md`)
2. baca decision / ADR (`06-decision-resolution.md`)
3. cek `git status`
4. cek branch
5. cek `git log`
6. cek migration
7. cek route
8. cek source code
9. cek test
10. tentukan progres aktual

> Reconnaissance bersifat **baca-saja**. Tidak ada perubahan berkas pada langkah ini.

### 14.3 Langkah 2 — Klasifikasi Status

Setelah reconnaissance, AI menentukan salah satu:

- **`COMPLETED`** — sudah selesai dan terverifikasi
- **`UNFINISHED`** — belum selesai / belum lengkap
- **`BLOCKED`** — terhalang dependency, keputusan, atau masalah lain
- **`NEXT ELIGIBLE TASK`** — tugas berikutnya yang boleh diambil

### 14.4 Syarat "NEXT ELIGIBLE TASK"

Sebuah task hanya boleh diambil bila **seluruh** syarat berikut terpenuhi:

- task harus **sesuai ownership** user
- **dependency** harus siap
- **PRD harus final** (tidak ada keputusan terbuka; ADR berstatus `[FINAL]`)
- task **belum selesai**
- **tidak boleh conflict** dengan pekerjaan atau perubahan lain
- **security** harus memenuhi requirement (lihat bagian **11. Security Development Gate**)
- dependency berupa **shared dependency wajib sudah punya owner** yang ditetapkan manusia; bila owner belum ditetapkan → task **tidak eligible** dan dilaporkan sebagai `BLOCKED` (lihat **Shared Dependency Ownership**)

### 14.5 Batas Ownership

- **ADMINISTRATION hanya mengambil pekerjaan Administration.**
- **ACADEMIC hanya mengambil pekerjaan Academic.**
- AI dilarang mengambil task di luar ownership yang telah dikonfirmasi.
- **Catatan:** larangan ini membatasi **ownership**, bukan berarti satu role wajib menunggu role lain selesai. Selama dependency nyata sebuah task terpenuhi, task tersebut boleh dikerjakan meskipun workstream role lain masih berjalan (lihat **14.12 Paralelisme Administration & Academic**).

### 14.6 Shared File

- Jika suatu perubahan **menyentuh shared file**, AI **harus memeriksa dampaknya** terhadap seluruh pemakai berkas tersebut (lihat bagian **5. Shared Area**) dan melaporkannya sebelum melanjutkan.
- Jika objek yang diubah adalah **shared dependency** (layanan/struktur yang dipakai lebih dari satu workstream), berlaku **Aturan Shared-Change** pada bagian **Shared Dependency Ownership**: hanya owner yang boleh mengubah; role lain hanya mengonsumsi; perubahan wajib dicek dampaknya ke semua consumer.

### 14.7 Perlindungan Perubahan Milik Orang Lain

- Jika ditemukan **perubahan lokal milik orang lain** (uncommitted / tidak dikenali):
  - **JANGAN** `reset`, `restore`, `checkout`, `clean`, atau **menghapus**.
  - **STOP** dan **laporkan**.

### 14.8 Requirement Tidak Jelas

- Jika requirement tidak jelas → **STOP** dan minta keputusan manusia.
- **Jangan mengubah PRD canonical agar cocok dengan source code.** Source code yang menyesuaikan PRD — bukan sebaliknya.

### 14.9 Aturan Ringkas — Checklist Pengambilan Task

- [ ] Ownership user sudah dikonfirmasi (ADMINISTRATION / ACADEMIC)
- [ ] Task sesuai ownership user
- [ ] Dependency sudah siap
- [ ] PRD sudah final
- [ ] Task belum selesai
- [ ] Tidak ada konflik
- [ ] Pemblokiran (bila ada) berbasis **dependency nyata**, bukan fase/role yang berbeda (lihat **14.12**)
- [ ] Shared dependency yang dipakai task sudah **punya owner** (bila tidak → minta OWNER ASSIGNMENT, lihat **Shared Dependency Ownership**)
- [ ] Security memenuhi requirement

### 14.10 Penutupan Sesi

- Alur lengkap: konfirmasi ownership → reconnaissance (baca-saja) → klasifikasi status → usulkan `NEXT ELIGIBLE TASK` → **tunggu persetujuan** → implementasi → test → laporan → commit (bila disetujui).
- Tidak mengulang dari awal; lanjutkan dari kondisi working tree saat ini dan jangan reset/checkout pekerjaan benar yang sudah ada.
- Jaga working tree tetap bersih dan perubahan tetap minimal serta terfokus.
- Tutup sesi dengan ringkasan: apa yang diubah, hasil test, status, dan langkah berikutnya.

### 14.11 Pemilihan NEXT ELIGIBLE TASK Berdasarkan Role

- **Jika user memilih `ACADEMIC`:** AI **harus** mencari `NEXT ELIGIBLE TASK` dari bagian **Academic Workstream Roadmap** — berdasarkan **dependency** dan **status repository** (bukan asumsi), bukan dari roadmap PROMPT/ADJUSTMENT.
- **Jika user memilih `ADMINISTRATION`:** AI wajib berjalan berurutan:
  1. **verifikasi Adjustment A–E** terhadap repository aktual (bukan dari dokumen/report lama);
  2. **selesaikan item Adjustment yang masih eligible** (termasuk item Adjustment E yang berstatus `PARTIAL`/`NOT_STARTED`);
  3. **setelah seluruh Adjustment selesai**, gunakan **Administration Workstream Roadmap** (ADM-01 … ADM-11, dengan ADM-05 terbagi menjadi **ADM-05A** Data Master Mata Pelajaran dan **ADM-05B** Jadwal Pelajaran);
  4. **pilih `NEXT ELIGIBLE TASK`** berdasarkan **Urutan Eksekusi Topologis** (bagian *Audit Dependency Administration*) **dan status repository**. Nomor ID **bukan** urutan pengerjaan: `ADM-05B` (Jadwal Pelajaran) baru eligible setelah `ADM-07` (Teaching Assignment) selesai;
  5. **jangan mengambil task Academic** (A-01 … A-08).
- Roadmap **harus tetap bebas siklus**. Bila penambahan workstream atau dependency menimbulkan dependensi dua arah (contoh historis: satu baris `ADM-05` yang saling membutuhkan dengan `ADM-07`), AI **STOP** dan laporkan sebagai konflik — jangan memotong atau mengarang dependency secara sepihak.
- Task yang pendahulunya masih berstatus `PARTIAL`/`NOT_STARTED` berarti **belum eligible** (bukan `BLOCKED`); `BLOCKED` hanya dipakai untuk struktur repository yang belum ada atau keputusan manusia yang masih tertunda.
- Task `BLOCKED` (dependency belum tersedia) **tidak boleh** diusulkan sebagai `NEXT ELIGIBLE TASK`; AI wajib mengusulkan task dengan dependency yang sudah siap dan **melaporkan** yang terhalang.
- Jika pada roadmap role tersebut **tidak ada** task yang eligible → AI **STOP** dan laporkan (lihat bagian **13. Stop Conditions**).
- Status task wajib **diverifikasi ulang terhadap repository** pada setiap sesi; status di dokumen tidak boleh dianggap final tanpa pengecekan.

### 14.12 Paralelisme Administration & Academic (Anti Phase-Lock) `[BARU]`

**Prinsip:** yang memblokir pekerjaan adalah **dependency nyata**, bukan nomor fase, bukan nama workstream, dan bukan fakta bahwa role lain sedang berjalan.

- **Shared foundation** yang belum selesai boleh memblokir role lain **HANYA bila** role tersebut **benar-benar bergantung** padanya (struktur/tabel/layanan/keputusan yang dibutuhkannya belum ada di repository).
- Task **Administration** memblokir Academic **hanya** bila ada dependency nyata yang belum tersedia.
- Task **Academic** memblokir Administration **hanya** bila ada dependency nyata yang belum tersedia (mis. metrik dashboard/ekspor yang membaca hasil Academic).
- **Workstream yang seluruh dependency-nya sudah siap boleh dikerjakan**, meskipun workstream milik role lain masih berjalan.
- Tetap **dilarang mengambil ownership role lain** (lihat **14.5**) dan **berkas/layanan bersama wajib diperiksa dampaknya** (lihat **14.6** dan **5. Shared Area**).
- **Kecuali:** shared dependency yang **belum punya owner** memblokir **semua** konsumennya sampai manusia menetapkan owner — ini pemblokiran nyata (SD-O2), bukan phase-lock (lihat **Shared Dependency Ownership**).
- Paralel dianggap aman bila berkas yang disentuh **tidak tumpang tindih**; bila tumpang tindih, berlaku aturan satu berkas = satu pemilik pada satu waktu (**9. Anti Conflict**).
- Aturan ini **tidak mengubah** requirement canonical PRD, roadmap ADM-01 ... ADM-11, roadmap A-01 ... A-08, maupun Adjustment A–E; aturan ini hanya meluruskan **cara membaca** aturan ketergantungan (§6) agar tidak menjadi phase-lock lintas role.

**Contoh (kondisi nyata repository):**

| Situasi | Boleh berjalan? | Alasan |
|---|---|---|
| ADMINISTRATION sedang mengerjakan **ADJUSTMENT E** (E-02, E-04, E-05, E-06, E-07) | YA | pekerjaan Administration / shared foundation |
| ACADEMIC memiliki task yang **seluruh dependency-nya sudah tersedia** di repository | **BOLEH** | tidak ada dependency nyata pada item E yang sedang berjalan; beda fase bukan pemblokiran |
| ACADEMIC task yang **membutuhkan shared foundation yang belum tersedia** (mis. `GoogleDriveService` pada A-03/A-04/A-07/A-08) | **BLOCKED** | dependency nyata belum ada → laporkan, jangan diusulkan sebagai `NEXT ELIGIBLE TASK` |
| ACADEMIC ingin menyentuh modul Admin/TU (master data, panel audit log, konfigurasi) | DILARANG | di luar ownership (**14.5**, **Batas Academic**) |

> Kesimpulan: **ADMINISTRATION dan ACADEMIC dapat berjalan paralel secara aman** selama tiap task lolos uji dependency nyata, ownership, dampak berkas bersama, dan gerbang keamanan (§11).

## 15. Completion Checklist `[BARU]`

### 15.1 Checklist Operasional

- [ ] requirement sesuai PRD
- [ ] ownership sesuai
- [ ] dependency benar
- [ ] security diperiksa
- [ ] test lulus
- [ ] migration aman
- [ ] diff diperiksa
- [ ] tidak ada conflict
- [ ] commit jelas
- [ ] progress berikutnya dicatat

### 15.2 Pelaporan Setelah Task Selesai

Setelah task selesai, AI **wajib menuliskan** (tidak boleh dikosongkan tanpa alasan):

- **NEXT ADMINISTRATION TASK** — usulan tugas Administrasi berikutnya (atau `—` bila tidak ada)
- **NEXT ACADEMIC TASK** — usulan tugas Academic berikutnya (atau `—` bila tidak ada)
- **BLOCKED** — daftar halangan yang ditemukan (atau `—` bila tidak ada)
- **HUMAN DECISION** — keputusan yang menunggu manusia, termasuk **OWNER ASSIGNMENT** untuk shared dependency yang belum punya pemilik (lihat **Shared Dependency Ownership**) — atau `—` bila tidak ada

---

*Dokumen ini berstatus **FINAL** untuk lingkup agent collaboration workflow. PRD 01–06 tidak diubah oleh dokumen ini dan tetap menjadi otoritas tunggal.*
