# n8n Automation Portfolio

Kumpulan workflow n8n yang saya bangun untuk klien nyata dan sebagai latihan skill automation, sebagai bagian dari persiapan menjadi AI Automation Engineer / n8n Developer remote.

## 1. AI Lead Qualification

Workflow yang mengintegrasikan **Google Gemini API** untuk menganalisis pesan calon klien secara otomatis dan mengklasifikasikan urgensi kebutuhan mereka — tanpa perlu screening manual.

**Use case:** Dibangun untuk konteks bisnis jasa konstruksi (CV Bintang Alta Wijaya), di mana pesan calon klien lewat WhatsApp perlu cepat dikategorikan berdasarkan seberapa mendesak kebutuhan mereka.

**Tech stack:**
- n8n (workflow engine)
- Google Gemini API (LLM integration)
- JavaScript (Code node untuk parsing response)


**Node flow:**

`Trigger` → `Input pesan` → `HTTP Request ke Gemini API` → `Parse JSON response`

**Kemampuan yang ditunjukkan:**
- Integrasi LLM API ke dalam automation workflow
- Prompt engineering — mendesain system prompt dengan kriteria eksplisit agar hasil klasifikasi konsisten
- Iterasi berdasarkan hasil testing (prompt di-refine setelah testing awal menunjukkan hasil ambigu di kategori "sedang")
- Response parsing dari struktur JSON API

**Hasil testing** — diuji dengan 4 skenario berbeda:
| Skenario | Contoh Pesan | Hasil |
|---|---|---|
| Urgensi tinggi | "rumah bocor parah, butuh diperbaiki secepatnya" | ✅ tinggi |
| Urgensi sedang | "mau mulai 2-3 bulan lagi, sudah ada rencana" | ✅ sedang |
| Minim konteks | "Ada promo?" | ✅ rendah, tanpa error |
| Informal + typo | "mnt tnya biaya gazebo urgent bgt minggu depan" | ✅ tinggi (tetap akurat meski typo) |

---
## 2. Laporan Mingguan Otomatis

Workflow terjadwal yang membaca data lead dari Google Sheets setiap minggu dan mengirim ringkasan performa langsung ke Telegram — tanpa perlu membuka spreadsheet secara manual.

**Use case:** Pemilik bisnis (CV Bintang Alta Wijaya) mendapat gambaran cepat performa lead mingguan tanpa harus cek data satu per satu.

**Tech stack:**
- n8n (Schedule Trigger + Google Sheets integration)
- JavaScript (Code node untuk agregasi data)
- Telegram Bot API (notifikasi)

**Node flow:**

`Schedule Trigger (tiap Senin 08:00)` → `Baca data Google Sheets` → `Agregasi & hitung metrik` → `Kirim ringkasan ke Telegram`

**Kemampuan yang ditunjukkan:**
- Time-based automation (scheduled trigger), bukan hanya event-driven (webhook)
- Data aggregation dan kalkulasi menggunakan JavaScript (Code node)
- Debugging nama field dengan whitespace tidak konsisten dari sumber data eksternal
- Integrasi multi-platform (Google Sheets → n8n → Telegram)

**Output contoh:**
```
📊 Laporan Mingguan - CV Bintang Alta Wijaya

Total lead minggu ini: 3
Lead Prioritas: 3
Lead Perlu Diedukasi: 0
Estimasi total nilai proyek: Rp 440.000.000
```
## 3. Lead Capture & Budget-Based Routing System

Workflow inti yang menjadi fondasi seluruh sistem — menangkap lead dari kalkulator web, memfilter berdasarkan budget, dan merutekan secara otomatis ke jalur notifikasi yang berbeda.

**Use case:** Kalkulator estimasi biaya di website klien mengirim data lead via webhook. Lead dengan estimasi di atas threshold budget tertentu langsung dikirim ke Telegram + Google Sheets sebagai prioritas; lead di bawah threshold tetap tercatat di Sheets untuk follow-up edukasi.

**Tech stack:**
- n8n (Webhook trigger + conditional branching)
- Telegram Bot API (notifikasi real-time)
- Google Sheets API (penyimpanan data terstruktur)

**Node flow:**

`Webhook (POST dari kalkulator)` → `Parsing data lead` → `IF (cek threshold budget)` → cabang `Prioritas`: kirim Telegram + simpan Sheets, cabang `Edukasi`: simpan Sheets saja

**Kemampuan yang ditunjukkan:**
- Menerima dan memproses data real-time via Webhook (bukan hanya scheduled/manual trigger)
- Conditional branching (IF node) untuk merutekan data berdasarkan kriteria bisnis
- Integrasi multi-cabang — satu trigger menghasilkan dua jalur eksekusi berbeda
- Live di production, menangani lead nyata sejak sistem diluncurkan

---
## 4. Modular Sub-Workflow Design

Refactor dari workflow AI Lead Qualification menjadi sub-workflow yang dapat dipanggil (reusable) dari workflow manapun — menerapkan prinsip modular design agar logic AI tidak perlu diduplikasi di setiap tempat yang membutuhkannya.

**Use case:** Alih-alih menyalin ulang node-node AI qualification setiap kali dibutuhkan di workflow berbeda, logic tersebut diisolasi menjadi satu sub-workflow yang bisa dipanggil dengan parameter input. Jika prompt AI perlu diperbarui, cukup diedit di satu tempat — semua workflow pemanggil otomatis mendapat versi terbaru.

**Tech stack:**
- n8n (Execute Workflow Trigger + Execute Sub-workflow node)
- Google Gemini API

**File terkait:**
- `sub-workflow-ai-qualification.json` — sub-workflow yang menerima parameter `pesan_lead` dan mengembalikan hasil klasifikasi
- `test-caller-workflow.json` — contoh workflow pemanggil (caller) untuk testing

**Node flow:**

Sub-workflow: `When Executed by Another Workflow (menerima parameter pesan_lead)` → `HTTP Request ke Gemini API` → `Parse JSON response`

Caller: `Manual Trigger` → `Set data test` → `Execute Sub-workflow (memanggil & memetakan parameter)`

**Kemampuan yang ditunjukkan:**
- Modular workflow architecture — memisahkan logic reusable dari implementasi spesifik
- Parameter passing antar workflow (Workflow Input Schema)
- Prinsip DRY (Don't Repeat Yourself) diterapkan dalam konteks low-code automation

---

*Repo ini akan terus ditambah dengan workflow lain seiring proses belajar.*
