# Layer 3 — Segmentation

Layer ini menyaring data mentah dari Layer 1/2 menjadi kelompok yang punya arti operasional — siapa yang butuh perhatian sekarang, bukan sekadar daftar lengkap semua contact.

## Active Lists (Segments)

Dua active list HubSpot, otomatis update sesuai kriteria (bukan static list yang di-isi manual):

### New Leads This Week (List ID: 12)
- Lifecycle Stage is any of **Lead**
- AND Create Date dalam 7 hari terakhir

Tujuan: memantau volume lead baru masuk tanpa perlu cek manual tiap hari.

### Stalled Opportunities (List ID: 13)
- Lifecycle Stage is any of **Opportunity**
- AND terasosiasi ke Deal yang: Deal Stage is any of Discovery Call/Proposal Sent
- AND Deal Create Date lebih dari 14 hari lalu

Tujuan: menemukan opportunity yang progress-nya macet, sebelum lead-nya "hilang" dari radar tim.

**Catatan teknis:** filter berbasis properti Deal (bukan properti Contact langsung) memerlukan opsi **"Association to: Deal"** di segment builder HubSpot — bukan filter Contact biasa.

## Workflow: HSC-04 — Stalled Opportunities Follow-up Summary

**Tujuan:** Mengambil member dari sebuah active list, lalu meminta Groq membuat ringkasan follow-up yang actionable untuk tim Alurio — menjembatani data tersegmentasi dengan keputusan operasional.

**Alur:** Set list ID → Ambil member list (ID saja) → Ambil detail contact by ID → Susun jadi teks → Groq buat ringkasan actionable → Log hasil.

**Catatan:** versi saat ini memproses satu list per eksekusi (list ID di-set manual di node `SetListId`). Rencana ke depan: gabungkan ke satu alur otomatis yang memproses kedua list sekaligus, sebagai bagian dari integrasi akhir semua layer.

File: [`HSC-04-Stalled-Opportunities-Followup.json`](./HSC-04-Stalled-Opportunities-Followup.json)

## Cara Import

1. Buka n8n → Workflows → Import from File → pilih file JSON.
2. Gunakan credential HubSpot yang sama seperti layer sebelumnya, pastikan scope-nya sudah termasuk `crm.lists.read`.
3. Set `listId` di node `SetListId` ke `13` (Stalled Opportunities) atau `12` (New Leads This Week) sebelum test.
