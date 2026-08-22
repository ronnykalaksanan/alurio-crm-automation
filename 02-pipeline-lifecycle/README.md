# Layer 2 — Pipeline & Lifecycle

Layer ini menangani progres "kesepakatan" (Deal) yang terpisah dari sekadar data Contact di Layer 1/6 — mensimulasikan proses akuisisi klien Alurio dari lead mentah sampai jadi klien aktif.

## Pipeline: Alurio Client Acquisition

5 stage, disesuaikan dari pipeline default HubSpot:

| Stage | Internal ID | Probability |
|---|---|---|
| Discovery Call | `appointmentscheduled` | 10% |
| Proposal Sent | `contractsent` | 30% |
| Contract Signed | `qualifiedtobuy` | 70% |
| Active Client | `closedwon` | 100% |
| Closed Lost | `closedlost` | 0% |

## Workflow: HSC-03 — Create Deal for New Lead

**Tujuan:** Membuat Deal baru di stage "Discovery Call" untuk lead yang sudah masuk (dari Layer 6), dan meng-associate-nya ke Contact yang sesuai.

**Insight penting yang ditemukan saat build ini:** HubSpot punya native automation — begitu sebuah Contact diasosiasikan ke Deal yang masih terbuka, lifecycle stage Contact tersebut **otomatis** berubah jadi "Opportunity" (definisi resmi HubSpot: "contact yang terasosiasi dengan deal aktif"), dan ini meng-override value apa pun yang di-set manual sesudahnya.

Desain awal workflow ini juga mencoba paksa-set lifecycle stage secara manual lewat API — setelah dites, keputusannya adalah **membiarkan native automation HubSpot yang bekerja** alih-alih melawannya. Node update manual dihapus dari versi final. Ini contoh konkret kenapa penting memahami behavior platform sebelum membangun automation di atasnya, bukan asal panggil API.

**Alur:** Trigger → Isi data test (ID contact + nama deal) → Create Deal (associate ke contact) → Log hasil.

File: [`HSC-03-Create-Deal-Update-Lifecycle.json`](./HSC-03-Create-Deal-Update-Lifecycle.json)

## Cara Import

1. Buka n8n → Workflows → Import from File → pilih file JSON.
2. Gunakan credential HubSpot yang sama seperti Layer 6, pastikan scope-nya sudah termasuk `crm.objects.deals.read` dan `crm.objects.deals.write`.
3. Isi `PrepareTestContactData` dengan ID contact asli sebelum test.
