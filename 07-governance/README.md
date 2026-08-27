# Layer 7 — Governance

Layer terakhir ini menjaga kualitas dan keamanan data — bukan fitur yang terlihat mencolok, tapi krusial begitu sebuah CRM dipakai oleh lebih dari satu orang atau diisi dari banyak sumber otomatis (seperti HSC-01/02 di project ini).

## Deduplikasi

### Ditemukan lebih awal dari yang disadari

Insight pertama soal governance sebenarnya sudah muncul jauh sebelum Layer 7 ini resmi dikerjakan: saat HSC-01 di-run ulang dengan email contact yang sama, HubSpot menolak request dengan status **409 Conflict**. Itu adalah **deduplikasi otomatis** HubSpot yang bekerja di background pada semua plan (termasuk Free) — mencegah dua contact dengan email yang sama, dan dua company dengan domain yang sama, tercipta secara duplikat.

### Tool "Manage Duplicates"

Di luar dedup otomatis dasar, HubSpot punya tool **Manage Duplicates** (Professional/Enterprise) yang membandingkan record berdasarkan kombinasi properti (First Name, Last Name, Email, Phone, dll untuk Contact) dan menyarankan merge untuk kandidat duplikat yang mirip tapi tidak identik persis (misalnya typo nama, format nomor telepon beda). Hasil scan pada data Alurio: **0 duplicate issues ditemukan** — hasil ini sendiri adalah bukti bahwa dedup otomatis dari awal sudah bekerja dengan baik, bukan hasil kosong yang tidak berarti.

**Catatan:** fitur custom dedup rule (kriteria dedup di luar model default HubSpot) ternyata masuk paket terpisah lagi, **Data Hub Professional** — bukan bagian dari Sales Hub Professional yang dipakai di Layer 4/5. Pola serupa yang sudah beberapa kali ditemukan di project ini: setiap kemampuan "lanjutan" cenderung berada di paket produk yang berbeda-beda, bukan satu paket yang mencakup semuanya.

## Permission & Access Control (konseptual)

Alurio hanya punya satu user (pemilik akun), sehingga permission granular tidak bisa didemonstrasikan secara nyata dalam project ini. Namun konsepnya tetap relevan untuk dipahami:

HubSpot 2026 menggunakan model **seat-based**: setiap user yang perlu membuat/mengedit data butuh **Core, Sales, atau Service seat** (berbayar per-seat), sementara **View-Only seat** gratis dan unlimited untuk orang yang hanya perlu melihat data (misal eksekutif atau partner eksternal). Di atas seat, ada **permission per-role** yang mengatur granular apa yang boleh dilihat/diedit — misalnya seorang sales rep yang di-restrict hanya bisa melihat contact miliknya sendiri, bukan milik rep lain.

Untuk bisnis seperti Alurio yang berkembang dari solo-founder jadi tim, governance ini yang akan jadi pertimbangan pertama saat menambah orang kedua — bukan sekadar "kasih akses", tapi merancang siapa boleh apa dari awal.

## Kesimpulan project 7-layer

Dengan selesainya layer ini, seluruh 7 layer CRM (Data Model, Pipeline & Lifecycle, Segmentation, Native Automation, Reporting, Integration & External Automation, Governance) telah dibangun di atas satu akun HubSpot + n8n yang sama, dengan narasi bisnis konsisten (Alurio) dari awal sampai akhir.
