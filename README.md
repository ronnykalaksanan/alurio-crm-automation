# Alurio — CRM Automation System

**Alurio** adalah studi kasus fiktif sebuah agency/consultancy kecil yang bergerak di bidang AI automation. Repo ini adalah implementasi nyata dari sistem CRM yang dipakai Alurio untuk mengelola lead, pipeline klien, dan automation-nya sendiri — dibangun end-to-end sebagai portofolio teknis, bukan sekadar tutorial.

Setiap folder di repo ini mewakili satu "layer" kemampuan CRM, dibangun bertahap di atas akun HubSpot + n8n yang sama, bukan proyek terpisah-pisah. Tujuannya menunjukkan pemahaman CRM secara menyeluruh: dari data model dasar sampai governance, dengan automation (n8n + LLM) sebagai benang merah di setiap layer.

## Tech Stack

- **CRM**: HubSpot (Free CRM)
- **Automation**: n8n (Cloud)
- **AI/LLM**: Groq API (untuk enrichment data, ringkasan lead, dsb)
- **Bahasa**: JavaScript (Code nodes), JSON (workflow config)

## Struktur Layer

| Folder | Layer | Status |
|---|---|---|
| [`00-orchestration`](./00-orchestration) | Menyambungkan semua workflow (HSC-01 s/d HSC-04) jadi satu alur otomatis end-to-end | ✅ Selesai |
| [`01-data-model`](./01-data-model) | Contact, Company, Deal — struktur data dasar & custom properties | Belum dimulai |
| [`02-pipeline-lifecycle`](./02-pipeline-lifecycle) | Deal pipeline & lifecycle stage (lead → customer) | ✅ Selesai |
| [`03-segmentation`](./03-segmentation) | Active Lists & segmentasi kontak | ✅ Selesai |
| [`04-native-automation`](./04-native-automation) | Automation bawaan HubSpot (dalam batas plan Free) | ✅ Selesai |
| [`05-reporting`](./05-reporting) | Dashboard & reporting dasar | ✅ Selesai |
| [`06-integration-automation`](./06-integration-automation) | Integrasi n8n ↔ HubSpot + AI enrichment (Groq) | ✅ Selesai |
| [`07-governance`](./07-governance) | Data governance: dedup, permission | ✅ Selesai |
| [`08-real-world-extension`](./08-real-world-extension) | Landing page publik, bug fixes produksi, error handling | ✅ Selesai |

## Kenapa Alurio?

Alurio dirancang sebagai agency AI automation yang butuh CRM buat kelola lead kliennya sendiri — konteks ini dipakai konsisten di setiap layer supaya setiap fitur teknis (workflow, pipeline, dashboard) punya alasan bisnis yang jelas, bukan sekadar demo fitur.

## Standar Workflow

Semua workflow n8n di repo ini mengikuti SOP konsisten: naming convention (`[LayerCode]-[NN] - [Deskripsi] [Env]`), retry logic (3x, 1000ms) di setiap node yang panggil API eksternal, Sticky Note README di tiap workflow, dan credential selalu lewat n8n Credentials Manager (tidak pernah hardcoded).

## Status Project

Seluruh layer inti (2, 3, 4, 5, 6, 7) sudah selesai dibangun dan didokumentasikan, dan seluruh workflow individual (HSC-01 s/d HSC-04) sudah disambungkan menjadi sistem otomatis end-to-end lewat dua orchestrator di `00-orchestration` — bukan lagi kumpulan workflow yang harus dijalankan manual satu per satu. Sistem ini kemudian diuji dengan skenario dunia nyata (landing page publik sungguhan, lihat `08-real-world-extension`) yang menemukan dan memperbaiki dua bug produksi, plus penambahan error handling terpusat di semua workflow. Sepanjang proses, beberapa insight penting ditemukan dan didokumentasikan langsung di README masing-masing layer: dari limitasi paket HubSpot Free, native automation lifecycle stage yang meng-override input manual, jebakan logika filter "ALL vs ANY" di reporting, sampai keharusan mendefinisikan skema input eksplisit antar sub-workflow di n8n.
