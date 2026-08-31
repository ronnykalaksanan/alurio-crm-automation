# 08 — Real-World Extension & Production Hardening

Setelah 7 layer inti selesai, project ini diuji dengan skenario yang lebih mendekati dunia nyata: lead datang dari **form publik beneran** (bukan diketik manual di n8n), dan seluruh alur diverifikasi end-to-end sampai ketemu serta diperbaiki bug produksi.

## Landing Page

Form lead-capture untuk Alurio, 6 field yang dirancang untuk kualifikasi bisnis (bukan sekadar nama+email): Nama, Email, Nama Perusahaan, Ukuran Tim, Proses yang ingin diotomatisasi, Tools yang dipakai, dan Urgensi. Dibangun sebagai single-file HTML dengan elemen visual signature — garis alur yang "mengalir" dari titik-titik berantakan jadi satu jalur teratur, merepresentasikan identitas nama "Alurio" (dari kata "alur").

File: [`alurio-landing/index.html`](./alurio-landing/index.html)
Live: alurio-landing.vercel.app

## Upgrade HSC-01: dari testing manual ke webhook publik

HSC-01 ditambah node **Webhook** (berdampingan dengan Manual Trigger yang tetap ada untuk testing), menerima submission langsung dari landing page. Empat custom Contact property baru dibuat di HubSpot untuk menampung jawaban form: `proses_yang_ingin_diotomatisasi`, `tools_digunakan`, `ukuran_tim`, `urgensi`.

## Dua Bug Produksi yang Ditemukan dan Diperbaiki

### Bug 1: Data contact hilang setelah proses AI enrichment

Node `LogEnrichedLead` di HSC-02 hanya menyimpan `status`, `summary`, dan `loggedAt` — field `hubspotContactId`, `firstname`, `lastname`, `email` ikut "tertimpa" begitu data melewati node pemanggilan Groq (karena `$json` berubah isi jadi response API, bukan data contact lagi). Akibatnya, saat HSC-00A memanggil HSC-03 untuk membuat Deal, `hubspotContactId` yang diterima kosong.

**Perbaikan:** `LogEnrichedLead` diubah untuk membawa kembali keempat field itu, diambil ulang dari node `ExtractContactData` menggunakan referensi node eksplisit (`$('ExtractContactData').item.json...`), bukan hanya dari `$json` di titik itu.

### Bug 2: Node data-test menimpa data asli dari orchestrator

HSC-03 dan HSC-04 awalnya menyambungkan trigger `When Executed by Another Workflow` ke node data-test (`PrepareTestContactData`, `SetListId`) yang isinya nilai hardcoded untuk keperluan testing manual. Saat dipanggil dari orchestrator dengan data asli, nilai hardcoded itu **menimpa** data yang seharusnya diterima dari pemanggil.

**Perbaikan:** koneksi trigger sub-workflow diarahkan langsung ke node eksekusi utama (`CreateDeal`, `GetListMembers`), melewati node data-test sepenuhnya. Node data-test tetap dipertahankan untuk jalur testing manual (Manual Trigger) yang terpisah.

### Insight tambahan: skema input harus didefinisikan eksplisit

n8n mengharuskan skema input didefinisikan secara eksplisit di trigger `When Executed by Another Workflow` (nama field + tipe data) sebelum node pemanggil (`Execute Sub-workflow`) bisa memetakan nilai ke field tersebut dengan benar. Tanpa ini, field yang dikirim dari orchestrator tidak sampai dengan benar meski secara sintaks expression-nya terlihat benar.

## Error Handling

Workflow `ErrorHandler [Global]` (dibangun sebelumnya di luar project ini) dihubungkan ke keenam workflow n8n (HSC-01, HSC-02, HSC-03, HSC-04, HSC-00A, HSC-00B) lewat pengaturan **Error Workflow** di masing-masing Settings. Ini memastikan kegagalan eksekusi yang tidak tertangani (bukan kegagalan API yang sudah di-handle lewat `onError: continueErrorOutput`) tetap tercatat dan bisa direspons.

## Hasil Verifikasi End-to-End

Submit form di landing page publik berhasil menghasilkan: Contact baru dengan seluruh field kualifikasi terisi → terdeteksi oleh polling HSC-00A dalam 5 menit → Deal baru terbuat dengan nama yang benar (`Alurio x [Nama] - Automation Project`) di stage Discovery Call → lifecycle stage Contact otomatis berubah menjadi Opportunity → task follow-up otomatis dibuat oleh native automation Layer 4 — seluruhnya tanpa campur tangan manual.
