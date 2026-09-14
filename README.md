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
*Repo ini akan terus ditambah dengan workflow lain seiring proses belajar.*
