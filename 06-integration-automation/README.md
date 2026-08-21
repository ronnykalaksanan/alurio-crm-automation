# Layer 6 — Integration & External Automation

Layer ini menghubungkan CRM Alurio (HubSpot) dengan automation eksternal lewat n8n, ditambah AI enrichment pakai Groq LLM. Ini layer pertama yang dikerjakan karena jadi kekuatan utama automation engineer — bagian yang biasanya tidak bisa dikerjakan oleh CRM admin murni.

## Workflow 1: HSC-01 — Push Lead to HubSpot

**Tujuan:** Mensimulasikan lead baru (dari sumber eksternal apa pun — form, sistem lain, dsb) yang otomatis masuk ke HubSpot sebagai Contact.

**Alur:** Trigger → Validasi schema → Push ke HubSpot Contacts API → Log hasil (sukses/gagal terpisah).

**Poin teknis:** Retry logic 3x dengan jeda 1 detik di setiap panggilan API, validasi field sebelum kirim data (mencegah data kotor masuk ke CRM), error handling terpisah dari jalur sukses.

File: [`HSC-01-Push-Lead-to-HubSpot.json`](./HSC-01-Push-Lead-to-HubSpot.json)

## Workflow 2: HSC-02 — HubSpot Contact Polling + AI Enrichment

**Tujuan:** Mendeteksi contact baru di HubSpot secara berkala, lalu menghasilkan ringkasan lead otomatis pakai LLM (Groq) — mensimulasikan automation yang membantu sales/CS tim Alurio memahami lead baru tanpa harus baca satu-satu.

**Kenapa polling, bukan webhook:** Action "Webhook" di HubSpot Workflows hanya tersedia di paket Operations Hub Professional ke atas, tidak ada di plan Free. Sebagai gantinya, workflow ini polling HubSpot Search API setiap 5 menit, dengan static data menyimpan timestamp polling terakhir supaya tidak memproses ulang contact yang sama.

**Alur:** Schedule Trigger (5 menit) → Ambil waktu polling terakhir → Search contact baru di HubSpot → Simpan waktu polling baru → Cek ada data baru atau tidak → (jika ada) pecah per-contact → Generate ringkasan via Groq → Log hasil.

**Poin teknis:** Static data workflow dipakai sebagai state sederhana (bukan database eksternal), model LLM yang dipakai `openai/gpt-oss-20b` (pengganti resmi `llama-3.1-8b-instant` yang di-deprecate Groq per Agustus 2026).

File: [`HSC-02-HubSpot-Contact-Polling.json`](./HSC-02-HubSpot-Contact-Polling.json)

## Cara Import

1. Buka n8n → Workflows → Import from File → pilih file JSON yang relevan.
2. Buat credential Header Auth: `Authorization: Bearer <token>` untuk HubSpot Private App/Service Key dan untuk Groq API Key.
3. Jalankan "Test workflow" untuk memverifikasi sebelum mengaktifkan jadwal otomatis.
