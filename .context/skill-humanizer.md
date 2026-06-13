# Skill: Humanizer — Remove AI Writing Patterns

> Adapted for Indonesian academic thesis (skripsi) context.
> Based on Wikipedia's "Signs of AI writing" guide (WikiProject AI Cleanup).
>
> **Usage:** Apply to prose paragraphs in bab1–bab5. Do NOT apply to table cells,
> LaTeX commands, figure captions, or citation keys. Preserve formal Indonesian
> academic register — do not make informal.

---

## What This Skill Does

Identifies and removes AI-generated writing patterns from thesis prose, then
rewrites to sound like a human researcher who has actual opinions and has read
the data, not a language model predicting likely next tokens.

Avoiding AI patterns is only half the job. Sterile, voiceless prose is just
as obvious as slop. Good thesis writing has a researcher behind it.

---

## Patterns to Remove (Indonesian + English equivalents)

### 1. Significance Inflation
Words that puff up importance without adding information.

**Indonesian AI-isms:**
- "menegaskan prinsip penting bahwa..."
- "mencerminkan komitmen terhadap..."
- "menandakan pentingnya..."
- "memperlihatkan betapa krusialnya..."
- "merupakan tonggak penting dalam..."
- "berperan vital/krusial dalam..."
- "memberikan kontribusi signifikan terhadap..."

**Fix:** State the fact directly. Delete the inflation sentence entirely if it
adds nothing beyond what the data already shows.

**Before:**
> Temuan ini menegaskan prinsip penting dalam pemilihan model bahwa sebuah
> sistem rekomendasi harus dinilai dari kualitas pemeringkatannya.

**After:**
> Metrik *retrieval* menjadi acuan utama pemilihan model, bukan *match rate*.

---

### 2. Formulaic Openers (Filler Phrases)
Sentence starters that delay the actual point.

| Filler opener | Replacement |
|---------------|-------------|
| Berdasarkan hasil evaluasi terhadap... | Langsung ke kesimpulan |
| Untuk memastikan bahwa... dilakukan analisis | State what the analysis found |
| Secara keseluruhan, ... | Cut; start with the claim |
| Secara deskriptif, ... | Cut entirely |
| Untuk memahami X dari perspektif Y, dilakukan Z | Just state Z's result |
| Temuan ini menunjukkan bahwa... | Rewrite as a direct statement |
| Temuan yang menarik muncul pada... | "X menunjukkan inversi yang menarik:" |
| Hal ini terjadi karena... | "...karena..." (inline) |
| Dengan pertimbangan tersebut, ... | Cut; just state the decision |

---

### 3. Superficial -ing/-kan Chain (Indonesian: yang + verb + sehingga)

Pattern: `...yang menunjukkan bahwa X, sehingga Y, sehingga Z`

Each `sehingga` that chains more than one level deep is a smell.
Also watch: `mencerminkan`, `mengindikasikan`, `menegaskan`, `menandakan`,
`menggarisbawahi` when used to add fake analytical depth.

**Before:**
> Distribusi ini mencerminkan dinamika saturasi kapasitas, menunjukkan bahwa
> supervisor dengan kemiripan tertinggi sering kali memiliki kapasitas yang
> terisi bersamaan, sehingga pilihan alternatif terdekat sudah tidak tersedia.

**After:**
> Supervisor dengan kemiripan tertinggi cenderung penuh bersamaan, sehingga
> mahasiswa langsung jatuh ke peringkat 4 atau lebih.

---

### 4. Copula Avoidance
AI substitutes elaborate verbs for simple "adalah" / "merupakan" / "ada".

| AI version | Human version |
|------------|---------------|
| diimplementasikan dalam bentuk aplikasi berbasis web | adalah aplikasi web |
| dirancang untuk memfasilitasi EPC dalam melakukan | digunakan EPC untuk |
| berfungsi sebagai pusat pengelolaan | adalah pusat pengelolaan |
| berperan sebagai acuan utama | adalah acuan utama |

---

### 5. Rule of Three Overuse
AI forces ideas into triples to sound comprehensive.

**Before:**
> Konfigurasi ini menghasilkan MRR tertinggi (0,585), Hit@5 tertinggi (0,798),
> dan persentase mahasiswa yang mendapat peringkat pertama tertinggi (61,4%).

**After:**
> Konfigurasi ini unggul pada seluruh metrik kepuasan mahasiswa: MRR 0,585,
> Hit@5 0,798, dan 61,4% mahasiswa mendapat supervisor rekomendasi pertama.

(The fix here is cutting the triple repetition of "tertinggi", not removing the data.)

---

### 6. Negative Parallelism
"Bukan hanya X, tetapi juga Y" / "Tidak hanya X, melainkan juga Y"

**Before:**
> Keunggulan ini konsisten di setiap metrik, bukan hanya pada satu indikator,
> sehingga menunjukkan kualitas pemeringkatan yang lebih baik secara menyeluruh.

**After:**
> Keunggulan bge-m3 konsisten di seluruh metrik, bukan hanya satu.

---

### 7. AI Vocabulary (Indonesian equivalents)
Overused in post-2023 AI text — use sparingly or replace:

- `mengindikasikan` → `menunjukkan` or just state it
- `secara komprehensif` → cut or be specific
- `holistik` → cut or be specific
- `optimal` (when vague) → specify what was optimized
- `relevan` (when vague) → say what it's relevant to
- `signifikan` → use the actual number or effect size
- `landscape` → be specific about what field/area

---

### 8. Em Dash Overuse
LaTeX `---` used mid-sentence to add fake punch.

**Before:**
> sehingga metrik \textit{retrieval} menjadi acuan utama --- yaitu kemampuan
> menempatkan dosen yang tepat pada posisi teratas --- dalam pemilihan model.

**After:**
> sehingga metrik \textit{retrieval} menjadi acuan utama pemilihan model.

---

### 9. Vague Positive Conclusions
Generic upbeat endings that say nothing.

**Before:**
> sehingga angka yang dilaporkan dapat dipertanggungjawabkan.

**Before:**
> sistem ini berpotensi meningkatkan efisiensi secara signifikan.

**Fix:** State the specific claim or cut entirely.

---

### 10. Overuse of Boldface
Only bold: model names in comparison sentences, the single most important
finding per subsection. Not: every concept being introduced.

---

## Signs of Soulless Prose (Even If Technically Clean)

Even after removing AI patterns, watch for:

- Every sentence is ~20 words, same structure
- No acknowledgment that something is surprising or unexpected
- No hedging where genuine uncertainty exists
- Reads like a translated Wikipedia article

**Add back:**
- Direct reactions to surprising data ("Temuan ini berlawanan dengan intuisi awal...")
- Honest uncertainty ("Penyebab pasti belum dapat dikonfirmasi tanpa inspeksi kode lebih lanjut")
- Short punchy sentences after longer explanatory ones

---

## Process

1. Read the paragraph
2. Identify patterns from the list above
3. Rewrite: cut filler, state the claim directly, use numbers
4. Read aloud — if it sounds like a robot, keep editing
5. Do NOT apply to: table cells, LaTeX commands, figure captions, section headings, citation keys

## Output

Provide the rewritten paragraph(s), then a one-line note on what was cut.

---

## Example (Indonesian Thesis)

**Before:**
> Berdasarkan hasil evaluasi terhadap 18 eksperimen, konfigurasi terbaik yang
> dipilih adalah BAAI/bge-m3 dengan extra_docs=True dan group_bonus=False
> (Run 8). Konfigurasi ini menghasilkan MRR tertinggi (0,585), Hit@5 tertinggi
> (0,798), dan persentase mahasiswa yang mendapat peringkat pertama tertinggi
> (61,4%). Temuan ini menegaskan prinsip penting bahwa pemilihan konfigurasi
> yang tepat berkontribusi secara signifikan terhadap kualitas rekomendasi.

**After:**
> Konfigurasi terbaik adalah BAAI/bge-m3 dengan \texttt{extra\_docs=True} dan
> \texttt{group\_bonus=False} (Run 8): MRR 0,585, Hit@5 0,798, dengan 61,4\%
> mahasiswa mendapat supervisor rekomendasi pertama. Konfigurasi ini unggul
> pada seluruh metrik kepuasan dibanding 17 konfigurasi lainnya.

**Cut:** Formulaic opener "Berdasarkan hasil evaluasi terhadap", triple
repetition of "tertinggi", significance inflation in closing sentence.
