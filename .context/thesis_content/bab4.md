# 4.2 Hasil

> **GUIDE (delete before submission):** Sections below are draft thesis prose in Bahasa Indonesia. Every `**[LENGKAPI: ...]**` marker is a placeholder you must fill with real data. The blockquote `> DATA NEEDED:` callouts explain where each number comes from. A consolidated checklist is at the bottom.

---

## 4.2.1 Evaluasi Model

Pada tahap ini dilakukan evaluasi terhadap **[LENGKAPI: jumlah model, mis. tiga]** model *embedding* yang menjadi kandidat untuk komponen perhitungan kemiripan semantik. Seluruh model dievaluasi menggunakan *pipeline*, dataset, serta konfigurasi kapasitas yang identik agar perbandingan bersifat adil; satu-satunya variabel yang diubah adalah model *embedding* yang digunakan. Untuk memastikan keterulangan (*reproducibility*), seluruh proses dibuat deterministik sehingga eksekusi berulang menghasilkan keluaran yang identik.

Evaluasi dilakukan terhadap **168 dari 171 mahasiswa**. Sebanyak 3 mahasiswa dikecualikan karena tidak memiliki nilai `current_supervisor_code` yang valid sebagai *ground truth*.

> **DATA NEEDED:**
> - Model count and names → fill `[LENGKAPI: jumlah model]` and the model-name placeholders in the table.
> - Supervisor count M and capacity settings (min/max caps) → add one sentence describing M and capacity if not already stated in your setup section.

Pengukuran dilakukan pada dua tingkat yang saling melengkapi:

- **Metrik *retrieval* (sebelum *solver*):** MRR, Hit@1, Hit@5, NDCG@5, NDCG@10, dan rata-rata peringkat (*avg_rank*). Metrik ini mengukur kualitas pemeringkatan dosen pembimbing sebelum proses alokasi kapasitas dijalankan.
- **Metrik *assignment* (setelah *solver*):** *Assignment Match Rate*. Metrik ini mengukur kesesuaian hasil penugasan akhir terhadap penugasan historis admin.

Hasil evaluasi seluruh model kandidat disajikan pada **Tabel 4.[LENGKAPI: nomor tabel]**.

**Tabel 4.[x] Perbandingan Metrik Evaluasi Antar Model**

| Model | MRR | Hit@1 | Hit@5 | NDCG@5 | NDCG@10 | Avg Rank | Match Rate |
|---|---|---|---|---|---|---|---|
| **[LENGKAPI: nama model A]** (Run 5) | 0,585 | 0,417 | 0,804 | 0,622 | 0,668 | 3,32 | 0,357 |
| **[LENGKAPI: nama model B]** (Run 6) | 0,474 | 0,298 | 0,708 | 0,505 | 0,576 | 4,20 | 0,357 |
| **[LENGKAPI: nama model C]** (Run 9) | 0,469 | 0,304 | 0,649 | 0,483 | 0,563 | 4,60 | 0,381 |
| *Random baseline* | ~0,23 | ~0,07 | ~0,36 | ~0,21 | — | ~7,5 | — |

> **DATA NEEDED — random baseline:** Computed from the number of supervisors M. Values above assume **M = 14** — **verify M and recompute if different.** Formulas: Hit@1 = 1/M; Hit@5 = 5/M; MRR = H_M / M (H_M = harmonic number); avg_rank = (M+1)/2; NDCG@5 = (1/M)·Σ_{r=1..5} 1/log2(r+1). Random *match_rate* ≈ 1/M ≈ 0,07 (omitted from table to avoid confusing it with retrieval Hit@5).

Secara deskriptif, **[nama model A]** memperoleh MRR sebesar 0,585 dengan dosen pembimbing sebenarnya berada pada peringkat rata-rata 3,32. **[nama model B]** memperoleh MRR 0,474 dengan rata-rata peringkat 4,20. **[nama model C]** memperoleh MRR 0,469 dengan rata-rata peringkat 4,60, namun memiliki *Assignment Match Rate* tertinggi sebesar 0,381. Seluruh model berada jauh di atas *random baseline*, yang menunjukkan bahwa pendekatan kemiripan semantik memberikan sinyal pencocokan yang nyata.

Untuk keperluan analisis lebih lanjut, nilai rata-rata skor kemiripan disajikan pada **Tabel 4.[LENGKAPI: nomor tabel]**.

**Tabel 4.[y] Rata-rata Skor Kemiripan Antar Model**

| Model | Avg Similarity @1 | Avg True Similarity | Selisih (sebaran) |
|---|---|---|---|
| **[nama model A]** (Run 5) | 0,619 | 0,596 | 0,023 |
| **[nama model B]** (Run 6) | 0,692 | 0,624 | 0,068 |
| **[nama model C]** (Run 9) | 0,871 | 0,857 | 0,014 |

---

## 4.2.2 Hasil Analisis Model dan Pemilihan Model Akhir

Berdasarkan hasil pada Tabel 4.[x], **[nama model A]** unggul pada seluruh metrik *retrieval*: MRR, Hit@1, Hit@5, NDCG@5, NDCG@10, dan rata-rata peringkat terbaik (3,32). Keunggulan ini konsisten di setiap metrik, bukan hanya pada satu indikator, sehingga menunjukkan kualitas pemeringkatan yang lebih baik secara menyeluruh.

Temuan yang menarik muncul pada **[nama model C]**. Model ini memiliki *Assignment Match Rate* tertinggi (0,381), namun justru memperoleh metrik *retrieval* terburuk di antara seluruh kandidat. Anomali ini dapat dijelaskan melalui Tabel 4.[y]: **[nama model C]** menghasilkan skor kemiripan yang sangat tinggi namun nyaris seragam (rata-rata skor peringkat-1 sebesar 0,871 dan skor dosen sebenarnya 0,857, dengan sebaran hanya 0,014). Artinya, model ini menilai hampir seluruh pasangan mahasiswa–dosen sebagai "sangat mirip" sehingga kehilangan kemampuan untuk membedakan satu dosen dari dosen lainnya. Tingginya *match rate* pada model ini bukan berasal dari kualitas pemeringkatan, melainkan merupakan efek dari proses alokasi berbasis kapasitas yang kebetulan sejalan dengan beberapa penugasan historis pada skor yang nyaris datar.

Temuan ini menegaskan prinsip penting dalam pemilihan model: **sebuah sistem rekomendasi dinilai dari kualitas pemeringkatannya** — yaitu kemampuan menempatkan dosen yang tepat pada posisi teratas — sehingga metrik *retrieval* menjadi acuan utama. *Assignment Match Rate* merupakan metrik hasil akhir yang bersifat melengkapi, namun apabila digunakan sendirian dapat menyesatkan, sebagaimana ditunjukkan oleh kasus **[nama model C]**.

> **DATA NEEDED:** This paragraph leans on the avg-similarity spread (Table 4.y). Make sure those numbers are filled before finalizing the argument.

Dengan pertimbangan tersebut, **[nama model A]** dipilih sebagai model akhir yang digunakan dalam sistem. Pemilihan ini didasarkan pada keunggulan menyeluruh pada metrik *retrieval*, yang merupakan indikator paling representatif terhadap tujuan utama sistem, yaitu memberikan rekomendasi dosen pembimbing yang relevan. Selain itu, hasil model ini telah diverifikasi bersifat deterministik dan dapat direproduksi secara identik pada eksekusi berulang, sehingga angka yang dilaporkan dapat dipertanggungjawabkan.

> **OPTIONAL — strengthen the selection:** if you want, add one closing sentence noting that the absolute Hit@1 ceiling (~0,42) is partly bounded by data quality (a cluster of students with only the `track` field populated produces identical embeddings). This connects to your Limitations section. Only add if you've decided to include the data-sufficiency analysis.

---

## ✅ Data Checklist (fill these, then delete this block)

1. **Number of candidate models** + **names of Model A / B / C** (which embedding model each Run used).
2. **Supervisor count M** → recompute the random baseline row if M ≠ 14.
3. **Table/figure numbers** (4.x, 4.y) to match your thesis numbering.
4. **(Optional)** capacity settings (min/max caps) — one sentence in setup if not already there.
5. **(Optional)** data-sufficiency note in 4.2.2 closing — only if including the subset analysis.

> Numbers already filled from your runs (no action needed): all retrieval metrics, match rates, and avg-similarity values for the three models.