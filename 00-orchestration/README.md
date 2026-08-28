# 00 — Orchestration

Layer penghubung yang menyambungkan semua workflow individual (HSC-01 sampai HSC-04) menjadi satu sistem yang berjalan otomatis end-to-end, tanpa perlu dites manual satu per satu lagi.

## Kenapa dipisah dari awal, baru disambungkan di akhir

Sejak HSC-01, setiap workflow sengaja dibangun **berdiri sendiri dan bisa ditest terisolasi** — supaya kalau ada error, mudah dilacak persis di layer mana masalahnya, dan supaya prinsip *single responsibility* dari SOP n8n pribadi tetap dipegang ("Do not create monolithic workflows. Extract single-responsibility processes into separate sub-workflows using the Execute Sub-workflow node"). Setelah semua bagian individual terbukti jalan benar, baru disambungkan lewat dua workflow orchestrator ini.

## HSC-00A — New Lead to Deal Pipeline

**Jadwal:** tiap 5 menit

**Alur:**
1. `MasterSchedule` memicu
2. `CallHSC02` menjalankan HSC-02 sebagai sub-workflow — poll contact baru di HubSpot, enrich pakai Groq
3. `HasNewLeads` cek apakah ada lead baru dari hasil polling
4. Jika ada, `CallHSC03` dijalankan per lead — membuat Deal baru dan meng-asosiasikannya ke contact
5. Hasil dicatat di `LogOrchestrationResult`

Ini yang membuat alur "lead baru masuk → langsung punya Deal di pipeline" jadi sepenuhnya otomatis, tanpa campur tangan manual.

## HSC-00B — Daily Segment Follow-up

**Jadwal:** 1x sehari, jam 8 pagi

**Alur:**
1. `DailySchedule` memicu
2. `SetBothListIds` menghasilkan 2 item (list ID 12 dan 13)
3. `CallHSC04` dijalankan otomatis 2 kali (sekali per item) — sekali untuk "New Leads This Week", sekali untuk "Stalled Opportunities" — masing-masing menghasilkan ringkasan follow-up dari Groq

## Cara kerja Execute Sub-workflow

Setiap workflow yang dipanggil (HSC-02, HSC-03, HSC-04) diberi node tambahan **"When Executed by Another Workflow"**, berdampingan dengan trigger aslinya (Manual Trigger / Schedule Trigger) yang tetap dipertahankan untuk keperluan testing manual. n8n mengizinkan satu workflow memiliki lebih dari satu trigger node sekaligus — siapa pun yang terpicu duluan yang menjalankan eksekusi.

## Cara Import

1. Pastikan HSC-02, HSC-03, HSC-04 sudah punya node "When Executed by Another Workflow" yang tersambung ke node kedua masing-masing alur.
2. Import kedua file JSON di folder ini.
3. Di setiap node "Call HSC-0X", pilih ulang sub-workflow yang dimaksud dari dropdown Workflow ID (tidak ikut ter-import karena ID unik per instance n8n).
4. Test HSC-00A dan HSC-00B secara manual dulu sebelum diaktifkan jadwalnya.
