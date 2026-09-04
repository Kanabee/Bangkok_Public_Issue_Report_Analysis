# Bangkok Public Issue Report Analysis

NLP and exploratory analysis of 20,730 citizen complaint reports submitted to
Bangkok's Traffy Fondue platform, turning free-text Thai complaints into
category, district and resolution signals a city team could act on.

**Data:** Traffy Fondue open data · 20,730 reports · 65 districts · 202 subdistricts
**Window:** 25 May – 12 June 2022 (19 days)
**Notebook:** `Bangkok_Public_Issue_Analysis.ipynb`

---

## Headline Findings

**1. Footpaths dominate, and by a wide margin.**
21.0% of reports mention footpath problems — roughly double the next category.

| Issue | Reports | Share |
|---|---|---|
| ทางเท้า (Footpath) | 4,351 | **21.0%** |
| จราจร (Traffic) | 2,257 | 10.9% |
| ขยะ/กลิ่น/น้ำเสีย (Waste, odour, wastewater) | 2,110 | 10.2% |
| น้ำท่วม/ระบายน้ำ (Flooding, drainage) | 2,091 | 10.1% |
| ความปลอดภัย (Safety) | 1,717 | 8.3% |
| แสงสว่าง (Lighting) | 1,560 | 7.5% |
| ถนน (Roads) | 1,514 | 7.3% |

Categories overlap by design — a report can mention several problems — so
shares sum to more than 100%.

**2. Volume and rate point at different districts.**
Chatuchak files the most reports in absolute terms (1,462). But Pom Prap Sattru
Phai, with 256 reports, devotes **42.6%** of them to footpaths — the highest
concentration in the city, and twice the citywide rate.

| District | Footpath reports | Footpath rate | All reports |
|---|---|---|---|
| ป้อมปราบศัตรูพ่าย | 109 | **42.6%** | 256 |
| พระนคร | 93 | 33.0% | 282 |
| วัฒนา | 166 | 30.2% | 550 |
| จตุจักร | 410 | 28.0% | **1,462** |

Volume indicates workload. Rate indicates a concentrated local problem. A
ranking built on either alone sends resources to the wrong place.

**3. Only 13.1% of reports carry a completion photo.**
2,708 of 20,730. Treated here as a signal worth investigating rather than a
performance measure — absence may reflect documentation practice rather than
inaction.

---

## Validating the Rules Instead of Trusting Them

The keyword dictionary matches **64.5%** of reports to at least one category.
That number alone says nothing about whether the matches are *correct*.

The dataset carries a `type` column — the category Traffy itself assigned —
which makes the rules measurable rather than merely plausible.

| Rule | Platform type | Precision | Recall | F1 |
|---|---|---|---|---|
| น้ำท่วม/ระบายน้ำ | น้ำท่วม | [XX] | [XX] | [XX] |
| ทางเท้า | ทางเท้า | [XX] | [XX] | [XX] |
| ถนน | ถนน | [XX] | [XX] | [XX] |
| แสงสว่าง | แสงสว่าง | [XX] | [XX] | [XX] |

*Run the notebook to populate.*

This is not a clean ground truth. Platform categories are single-label while
the rules are multi-label, and the two taxonomies were designed independently,
so low precision does not automatically mean error — a report about a flooded
footpath is filed once but should trigger two rules.

**Low recall is the actionable signal.** It marks vocabulary the dictionary is
missing, and the clustering section is where that vocabulary gets found.

---

## Topic Discovery: A First Attempt That Failed

The initial implementation vectorised comments as character n-grams (3–5,
`char_wb`). Character n-grams are appealing for Thai because they sidestep
word-boundary detection, which Thai requires and English does not.

**The result was unusable.**

| Cluster | Reports | Share | Defining n-grams |
|---|---|---|---|
| 2 | 5,782 | 27.9% | `ค่ะ`, `ที่`, `ไม่`, `แจ้ง` |
| 9 | 4,045 | 19.5% | `ที่`, `ให้`, `การ`, `เป็น` |
| 4 | 460 | 2.2% | `ลาดพร้าว` (a place name) |

Two clusters absorbed **48% of all reports**, defined by politeness particles
and function words. The model had grouped reports by how politely they were
written, not by what they were about. A third keyed on a neighbourhood name.
Cluster descriptions also came back as fragments like `างเท้` — unreadable to
any stakeholder.

The cause was simple: a Thai stopword list existed in the notebook but was
applied only to the word cloud, never to the vectoriser.

### What changed

Comments are now tokenised with PyThaiNLP's `newmm` engine, filtered through an
expanded stopword list covering function words *and* politeness particles
(`ครับ`, `ค่ะ`, `รบกวน`, `ขอบคุณ`), then vectorised as word unigrams and
bigrams.

This gives up the tokeniser-independence of character n-grams for two things
worth more: clusters driven by content rather than register, and labels made of
real words.

| | Before | After |
|---|---|---|
| Largest cluster | 27.9% | [XX]% |
| Cluster labels | n-gram fragments | words |

Each cluster now also reports **rule coverage** — the share of its reports the
keyword dictionary already catches. Clusters below 50% are the dictionary's
blind spots and the direct input to the next iteration of `ISSUE_KEYWORDS`.

---

## Method

```
Collection → Validation → Cleaning → EDA
    → Rule-based Thai classification → Validation against platform labels
    → District comparison (volume and rate)
    → Topic discovery → Resolution analysis → Recommendations
```

**Why transparent keyword rules rather than a classifier.** City staff need to
review and extend the categories themselves. A rule that reads
`น้ำท่วม|น้ำขัง|ระบายน้ำ|ท่ออุดตัน` can be corrected by the person who knows
the domain; a trained model cannot. The rules are then measured against
platform labels so that transparency does not become an excuse for being wrong.

**Why districts with under 100 reports are excluded from rate rankings.** A
district with 12 reports can show a 50% issue rate from six comments. Rate
rankings without a volume floor surface sampling noise at the top.

**Why counts are not normalised by population.** They should be, and are not,
because population and road-length data were not joined in. Report volume as
used here measures problems *and* willingness to report them, and those cannot
be separated in this extract.

---

## Limitations

- **The window is 19 days: 25 May – 12 June 2022.** This is the onset of the
  Bangkok rainy season, so the 10.1% flooding share almost certainly overstates
  the annual figure. No seasonal claim can be made from this data, and no trend
  either — 19 days cannot separate a structural problem from a single storm.
- **Reports represent platform users**, not Bangkok residents. District volume
  partly measures reporting propensity.
- **Keyword rules miss synonyms, misspellings, sarcasm and context.** Coverage
  is 64.5%; the validation section quantifies the error where a platform
  counterpart exists.
- **16.2% of comments repeat text seen elsewhere** in the data. Duplicate rows
  were removed, but these carry distinct ids and timestamps, so they survive
  deduplication. They may be genuine separate incidents or duplicate
  submissions.
- **Completion photos are a proxy for resolution, not a measure of it.**
- **Cluster labels describe term co-occurrence, not verified topics.**

## Next Steps

- A full-year extract, to separate seasonal effects from structural ones.
- Normalise report counts by district population and road length.
- Extend the dictionary from the low-coverage clusters, then re-score against
  platform labels to confirm recall improved.
- Measure time-to-resolution from timestamps rather than photo presence.
- Train a supervised classifier on the platform labels and compare it to the
  rules on accuracy *and* reviewability — the rules may lose on the first and
  still win overall.

---

## Tech Stack

Python · Pandas · NumPy · PyThaiNLP (newmm tokenisation) · Scikit-learn
(TF-IDF, MiniBatch K-Means) · Matplotlib · Seaborn · WordCloud · Google Colab

## Running It

```bash
pip install pythainlp wordcloud pandas numpy scikit-learn matplotlib seaborn
jupyter notebook Bangkok_Public_Issue_Analysis.ipynb
```

Data loads from a public URL; no credentials required. Run all cells in order.
A Thai font is downloaded automatically for the charts.

---

# 日本語概要

## バンコク市民通報データの分析

バンコク市の市民通報プラットフォーム「Traffy Fondue」に寄せられた
**20,730件のタイ語自由記述**を対象に、自然言語処理と探索的分析を行い、
カテゴリ・地区・対応状況という行政が実際に使える形に変換したプロジェクトです。

**対象期間：** 2022年5月25日〜6月12日（19日間）／65区・202地区

### 主な発見

**1. 歩道に関する通報が突出して多い**
全体の **21.0%**（4,351件）が歩道の問題に言及しており、第2位の交通渋滞
（10.9%）の約2倍でした。

**2. 「件数」と「比率」は異なる区を指す**
通報件数が最多なのはチャトゥチャック区（1,462件）ですが、区内通報に占める
歩道問題の比率が最も高いのはポムプラープサットゥルーパイ区で **42.6%**
（256件中109件）と、市全体平均の約2倍でした。

件数は業務量を、比率は地域固有の問題の集中度を示します。どちらか一方だけで
優先順位を決めると、資源配分の対象を誤ります。

**3. 完了写真が添付されているのは全体の 13.1%**
ただしこれは対応状況の記録慣行の差を反映している可能性があり、対応実績の指標
ではなく「確認すべき兆候」として扱っています。

### 手法上の工夫

**1. ルールを「信頼する」のではなく「測定する」**
キーワード辞書による分類は全体の 64.5% をカバーしましたが、この数値は分類が
「正しいか」を何も語りません。本データには Traffy 側が付与した `type` 列が
存在するため、これを参照点として各ルールの適合率・再現率を算出しました。

単一ラベル対複数ラベルという設計の違いがあるため完全な正解データではありま
せんが、独立した第三者の判断であり、検証コストはほぼゼロです。特に**再現率の
低さは、辞書に不足している語彙を直接指し示します。**

**2. トピック抽出の失敗と、その修正**
当初は文字n-gram（char_wb, 3〜5）を使用しました。タイ語は単語境界がないため、
形態素解析に依存しない文字n-gramは合理的な選択に見えます。

しかし結果は使用に耐えないものでした。**2つのクラスタが全体の48%を占め、その
特徴語は「ค่ะ」「ครับ」「ที่」「ให้」といった文末表現・機能語**でした。
モデルは「何について書かれているか」ではなく「どれだけ丁寧に書かれているか」で
分類していたことになります。さらに別のクラスタは「ラートプラーオ」という地名
に反応していました。

原因は明快で、ノートブック内にタイ語ストップワードは定義されていたものの、
ワードクラウドにのみ適用され、ベクトル化には使われていませんでした。

修正として、PyThaiNLP の `newmm` による形態素解析を導入し、機能語に加えて
**文末表現・依頼表現（ครับ／ค่ะ／รบกวน／ขอบคุณ）を含むストップワードリスト**を
適用した上で、単語unigram・bigramでベクトル化しました。文字n-gramの持つ
「解析器非依存」という利点は失いますが、内容に基づくクラスタリングと、
実際の単語による解釈可能なラベルが得られます。

**3. 分析を次の改善につなげる構造**
各クラスタについて、キーワード辞書がどの程度カバーできているかを算出しました。
カバー率が低いクラスタは辞書の盲点であり、そこから抽出した語彙を辞書に追加し、
再度 `type` 列に対して再現率を測り直すという改善サイクルを組み込んでいます。

### 限界の明示

**対象期間は19日間のみであり、バンコクの雨季開始時期と重なります。** そのため
浸水関連の 10.1% という比率は年間平均を大きく上回っている可能性が高く、本データ
から季節性やトレンドを論じることはできません。また、通報件数はプラットフォーム
利用者の分布を反映しており、問題の実際の発生量と「通報する意欲」を分離できて
いません。人口・道路延長による正規化は今後の課題として明記しています。
