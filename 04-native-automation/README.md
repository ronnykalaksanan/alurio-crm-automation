# Layer 4 — Native Automation

Layer ini menggunakan fitur Workflows bawaan HubSpot (bukan n8n) — mensimulasikan automation yang perlu bereaksi cepat/real-time terhadap perubahan data, sebagai pelengkap automation berbasis analisis di Layer 3/6.

## Catatan Penting: Plan Free vs Trial

Workflows multi-step di HubSpot **tidak tersedia sama sekali di plan Free** — langsung diarahkan ke halaman upsell Sales Hub Professional begitu menu Workflows dibuka. Untuk membangun dan mendokumentasikan layer ini, digunakan **trial 14 hari Sales Hub Professional** (tanpa kartu kredit). Ini konsisten dengan pendekatan project: mendokumentasikan batasan platform secara jujur (seperti insight lifecycle stage di Layer 2), bukan berpura-pura semuanya tersedia gratis.

## Workflow 1: Alurio - New Lead Internal Notification

**Tipe:** Contact-based
**Trigger:** Property value changed — Lifecycle stage is any of "Lead"
**Action:** Create task (type: Call), assigned ke diri sendiri, due 1 hari setelah enrollment, priority High

**Tujuan:** Begitu contact baru masuk sebagai Lead (dari Layer 1/6), langsung ada reminder actionable tanpa nunggu proses batch/polling — automation reaktif yang instan.

**Status:** Ditest dan berhasil — task "Follow up new lead: [nama]" otomatis terbuat begitu lifecycle stage contact diubah jadi Lead.

## Workflow 2: Alurio - Discovery Call Reminder

**Tipe:** Deal-based
**Trigger:** Property value changed — Deal stage is any of "Discovery Call" (pipeline Alurio Client Acquisition)
**Alur:** Delay 14 hari (bukan business days, disesuaikan dengan kriteria Stalled Opportunities di Layer 3) → Branch berdasarkan Deal Stage saat ini → hanya di cabang "Discovery Call" ditambahkan Create task reminder (type: Call, priority High, assigned ke diri sendiri)

**Tujuan:** Deal yang macet di stage awal selama 14 hari otomatis dapat reminder — early warning sebelum deal itu masuk kriteria "Stalled Opportunities" yang baru terdeteksi lewat HSC-04.

**Catatan desain:** Branch diperlukan supaya reminder hanya muncul kalau deal MASIH di Discovery Call setelah delay — bukan reminder buta yang tetap muncul walau deal sudah progress ke stage lain.

**Status:** Sudah disusun dan diaktifkan, belum bisa diverifikasi langsung karena delay 14 hari (berbeda dari Workflow 1 yang instan).

## Kenapa dua layer automation (native + n8n) berdampingan

Native Workflows: bagus untuk aksi instan/real-time berbasis satu event.
n8n (Layer 6/HSC-04): bagus untuk analisis batch, enrichment AI, dan logika yang lebih kompleks lintas beberapa data.

Kombinasi keduanya adalah pola yang realistis untuk RevOps/Customer Systems sungguhan — bukan semua automation harus dibangun custom.
