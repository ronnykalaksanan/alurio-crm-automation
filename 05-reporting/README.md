# Layer 5 — Reporting

Layer ini mengubah data mentah dari layer sebelumnya jadi gambaran bisnis yang bisa langsung dibaca — dashboard, bukan sekadar daftar record.

## Catatan Penting: Plan Free vs Trial

Sama seperti Layer 4, custom report dan dashboard **tidak tersedia di plan Free** HubSpot — hanya dashboard bawaan yang tidak bisa dikustomisasi. Layer ini dibangun memakai trial 14 hari Sales Hub Professional yang sama dengan Layer 4.

## Dashboard: Alurio - Business Overview

![Dashboard bagian 1](./dashboard-overview-1.png)
![Dashboard bagian 2](./dashboard-overview-2.png)

Tiga report:

### 1. Deal Stage Overview
Jumlah deal di tiap stage pipeline "Alurio Client Acquisition" — gambaran cepat kesehatan pipeline saat ini.

### 2. New Contacts Created
Jumlah contact baru yang masuk ke sistem — indikator momentum lead.

### 3. Alurio - Win Rate (custom report)
Perbandingan jumlah deal "Active Client" (menang) vs "Closed Lost" (kalah), dibangun dari nol lewat custom report builder karena tidak ada template ready-made yang pas untuk pipeline custom.

## Insight teknis: filter "ALL" vs "ANY"

Bagian tersulit dari layer ini bukan soal data, tapi soal logika filter. Report Win Rate awalnya kosong meski data deal-nya ada. Penyebabnya: filter multi-value ("Deal stage is any of Active Client OR Closed Lost") digabung dengan operator **ALL** (AND) terhadap filter lain — yang secara matematis menjadi kontradiksi ("deal yang stage-nya A DAN B sekaligus" tidak pernah ada). Solusinya: ganti kondisi filter grup dari **"ALL of the filters below"** menjadi **"ANY of the filters below"**.

Ini kesalahan umum saat menyusun report dengan filter multi-value di HubSpot (atau tool BI mana pun) — worth diingat sebagai lesson learned, bukan cuma cara memperbaikinya.

## Masalah lain yang ditemukan: report berbasis event vs property

Report ready-made seperti "Deal stage funnel with deal conversion rates" ternyata berbasis **event** (histori perpindahan stage), bukan **snapshot property** (kondisi saat ini). Karena deal-deal di project ini dibuat langsung di stage tertentu lewat API (bukan lewat perpindahan manual bertahap), report jenis ini selalu kosong. Solusinya: pilih report yang membaca property langsung (seperti "Deal stage overview"), bukan yang membaca histori event.
