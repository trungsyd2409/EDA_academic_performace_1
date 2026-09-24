# Review bài EDA – Student Habits and Academic Performance

**File được chấm:** `EDA_final.ipynb` (54 cells, chạy hết đến §5.2)
**Bài giải mẫu đi kèm:** `EDA_solution_example.ipynb` (+ bản `.html` để đọc nhanh)

---

## 1. Kết quả tổng quan

| | |
|---|---|
| **Điểm ước tính** | **51 / 100** |
| **Grade band (UTS)** | **Pass (P)** – sát ngưỡng |
| **Nếu chỉ viết đủ các phần Conclusion còn trống** | ≈ 68–72 (Credit) |
| **Nếu làm theo bài giải mẫu** | ≈ 88–92 (High Distinction) |

**Nhận xét chung.** Phần code và phần kiểm tra dữ liệu của bạn **tốt hơn mức trung bình**: có kiểm tra hidden missing values, duplicate bỏ qua ID, IQR, dùng cả Pearson lẫn Spearman, Kruskal–Wallis kèm effect size ε², VIF. Đây là những thứ nhiều bạn ở Pass/Credit không làm. **Vấn đề lớn nhất là bài chưa xong**: 7 ô Conclusion vẫn là `??`, 5 research question đặt ra ở đầu bài không được trả lời, và không có phần tổng kết. Trong rubric EDA, phần *diễn giải (interpretation)* thường chiếm nhiều điểm nhất – vẽ biểu đồ đúng mà không nói biểu đồ đó nghĩa là gì thì giảng viên gần như không cho điểm phần đó.

> ⚠️ Lưu ý: rubric dưới đây do mình tự xây dựng theo cách chấm criteria-based phổ biến ở UTS (HD ≥ 85, D 75–84, C 65–74, P 50–64, Z < 50). Rubric chính thức của môn bạn có thể khác trọng số – hãy đối chiếu với file assessment brief trên Canvas.

---

## 2. Rubric và điểm từng tiêu chí

| # | Tiêu chí | Trọng số | Điểm | Lý do chính |
|---|---|---|---|---|
| 1 | Problem framing & research questions | 10 | **7** | Có objective + 5 RQ rõ ràng. Thiếu context (ai cần kết quả này, để làm gì), RQ3 "does it matter" còn mơ hồ, chưa ghi RQ nào trả lời ở phần nào. |
| 2 | Data understanding (overview, dictionary, summary) | 10 | **7** | Dictionary tốt. Một số valid range sai (mental_health ≥ 0 thay vì 1–10; exercise ≥ 0 thay vì 0–7). Kết luận "data is synthetic" đưa ra quá sớm, chưa có bằng chứng. |
| 3 | Data quality & cleaning (justified decisions) | 15 | **9** | Kiểm tra khá đủ. Nhưng: ghi sai "19 missing" (thực tế 91); §2.4 không có code; hiểu nhầm "91 extra spaces"; không kiểm tra missing có ngẫu nhiên không; không phát hiện **ceiling effect** (48 điểm = 100); "decision log" chỉ là code, không có bảng lý do. |
| 4 | Univariate analysis | 10 | **4** | Biểu đồ đẹp nhưng **cả 3 Conclusion đều trống**. Có 2 bug (legend "mean" cho median; typo `internat_quality`). |
| 5 | Bivariate & multivariate analysis | 15 | **7** | Phương pháp đúng (Pearson/Spearman, regplot, Kruskal + ε², heatmap, VIF). Nhưng **4/4 Conclusion trống**, không có mô hình đa biến (regression) dù đã import `smf`, boxplot ordinal không theo thứ tự. |
| 6 | Statistical rigour | 10 | **6** | Điểm cộng: non-parametric test + effect size. Thiếu p-value cho correlation, không nhắc multiple testing, không kiểm tra giả định. |
| 7 | Interpretation, insights & answering RQs | 15 | **2** | Chỉ có conclusion cho phần cleaning. Không trả lời RQ nào, không có insight, recommendation hay limitation. |
| 8 | Communication (writing, figures) | 10 | **5** | Biểu đồ có title, layout gọn. Nhiều lỗi chính tả/ngữ pháp (acacdemic, comlumn, Conlusion, consistancy, redundants, "All float columns is"…). Figure không đánh số, không có caption. |
| 9 | Reproducibility & code quality | 5 | **4** | Code sạch, chạy tuần tự. Trừ điểm: import thừa (`smf`), dòng thừa `len(num_cols)`, `print(len(cols))`, không lưu dữ liệu đã clean vào DataFrame riêng. |
| | **Tổng** | **100** | **51** | |

---

## 3. Review chi tiết từng bước

Mỗi bước gồm: ✅ bạn đã làm tốt – ❌ vấn đề – 👉 nên làm gì – ✍️ gợi ý câu Conclusion (tiếng Anh, bạn có thể dùng/sửa lại).

### §0. Problem definition and Setup

✅ Có objective, có 5 research question đánh số – đây là cấu trúc tốt, nhiều bài bỏ qua.

❌
- Chưa có **context**: tại sao phân tích này quan trọng, ai dùng kết quả?
- RQ3 *"Does gender and parental education matter?"* – "matter" mơ hồ, và sai ngữ pháp (phải là *Do*). RQ3 cũng chỉ nhắc 2 biến trong khi bạn phân tích 6 biến categorical.
- Lỗi chính tả: *acacdemic, quesions, performace, redundants, infomation, "most strongest"*.

👉
- Thêm 2–3 câu context.
- Viết RQ ở dạng đo được: *"Do exam scores differ across gender, parental-education… groups?"*
- Làm **bảng RQ → section trả lời** để giảng viên dễ theo dõi (xem bài mẫu §0.1).
- Nêu mức ý nghĩa α = 0.05 và nói sẽ báo cáo effect size.

### §1.1 Dataset overview

✅ Conclusion đúng và gọn.

👉 Ghi thêm: *"Only `parental_education_level` has missing values (909 non-null)"* – bạn đã có thông tin này trong output `info()`, nên dẫn luôn sang §2.1.

### §1.2 Variable dictionary

✅ Rất tốt khi ghi rõ "no official data dictionary, these are assumptions".

❌ Valid range chưa chính xác:
- `mental_health_rating`: ghi ≥ 0 nhưng thực tế là thang **1–10**.
- `exercise_frequency`: nên là **0–7** (số ngày/tuần).
- `age`: ghi 16–24 – vừa khít với dữ liệu nên không còn là "kiểm tra" nữa. Nên đặt theo domain (vd 16–30) rồi so với observed (17–24).
- `diet_quality`, `internet_quality`, `parental_education_level` bản chất là **ordinal** (bạn ghi parental là nominal).

👉 Thêm cột **Observed range** tính từ dữ liệu để so với Assumed range → đây chính là bằng chứng cho §2.4.

### §1.3 / §1.4 Summary statistics

✅ Nhận xét mean ≈ median → phân phối đối xứng: đúng.

❌ Câu *"hard limits … suggests the data is synthetic"* chưa đủ bằng chứng – sleep 3.2–10 giờ là hoàn toàn thực tế. Kết luận "synthetic" là đúng, nhưng bằng chứng thật nằm ở chỗ khác (phân phối uniform, predictor không tương quan, spike ở 0 và 100) – nên để kết luận này ở §2.6 / §3.2 / §5.1.

👉 Thêm cột `skew` và `mean - median` vào bảng; nêu con số cụ thể (ví dụ *"students study 3.6 h/day on average"*); thêm 1 dòng cho categorical (`describe()` của cột text).

### §2.1 Missing values

✅ Kiểm tra cả hidden missing (`''`, `'?'`, `'NA'`) – rất tốt.

❌
- Conclusion ghi **"19 missing values"** – output là **91**. Lỗi này giảng viên sẽ thấy ngay.
- Chưa trả lời câu hỏi quan trọng nhất: **missing có ngẫu nhiên không?** Nếu những bạn thiếu thông tin có điểm thấp hơn thì fill "Unknown" sẽ gây bias.

👉 So sánh nhóm missing vs không missing (Mann–Whitney cho biến số, Chi-square cho biến phân loại). Kết quả thực tế: điểm trung bình 70.0 vs 69.6, p = 0.71 → **phù hợp với MCAR**.

✍️ *"Only `parental_education_level` has missing values (91 rows, 9.1%). Students with and without this information have almost the same mean exam score (70.0 vs 69.6, p = 0.71), and no other variable differs between the groups after Holm correction. This is consistent with data missing completely at random, so we recode the missing values as 'Unknown' instead of deleting 9% of the data."*

### §2.2 Duplicates

✅ Kiểm tra cả khi bỏ `student_id` – đúng cách.

❌ Conclusion nói *"every student id is unique"* nhưng code chưa kiểm tra riêng `student_id.duplicated()`. Lỗi chính tả "Conlusion".

### §2.3 Data types and format

❌ Hàm `n_long_decimals` kiểm tra "> 2 chữ số thập phân" – chưa rõ mục đích, và không kiểm tra cột integer.

👉 Kiểm tra: cột đếm có phải số nguyên không, cột liên tục có bao nhiêu chữ số thập phân (thực tế: tối đa 1). Viết conclusion có nghĩa: *"all continuous variables are recorded with one decimal place, so the format is consistent."* Sửa ngữ pháp: "All float columns **are** consistent".

### §2.4 Invalid values (domain rules)

❌ **Không có code**, chỉ có một câu kết luận. Giảng viên sẽ coi như phần này chưa làm.

👉 Viết rule cho từng biến (dựa trên dictionary §1.2) và đếm số vi phạm. Thêm **rule chéo giữa các biến**: `study + sleep + social_media + netflix ≤ 24 giờ` (thực tế max = 21.5 h, 0 vi phạm). Đây là loại kiểm tra thể hiện tư duy "domain knowledge" mà rubric rất thích.

### §2.5 Categorical consistency

✅ Kiểm tra unique + khoảng trắng.

❌ Output báo **"extra spaces: 91"** cho `parental_education_level`. Đây **không phải** khoảng trắng: `NaN != NaN` luôn trả về `True`, nên 91 giá trị missing bị đếm thành "space". Conclusion của bạn né vấn đề thay vì giải thích.

👉 Dùng `.dropna()` trước khi so sánh, kiểm tra thêm case variants (`male` vs `Male`), và ghi 1 câu giải thích bug này → cho thấy bạn hiểu code của mình.

### §2.6 Outliers

✅ IQR + boxplot đúng.

❌
- Conclusion *"some little outliers, keep its because it is minor"* – lý do chưa đủ. "Minor" không phải lý do; lý do đúng là *các giá trị này hợp lý (plausible) và nằm trong domain rule*.
- **Bỏ sót phát hiện quan trọng nhất bài:** có **48 học sinh đạt đúng 100 điểm** (bạn đã in ra ở §3.1 nhưng không bình luận), **66 học sinh attendance = 100%**, 59 học sinh netflix = 0. Đây là **ceiling/floor effect** – dấu hiệu dữ liệu bị "clip" (cắt) ở biên, và là bằng chứng mạnh nhất cho việc dữ liệu là synthetic.

👉 Đếm số giá trị nằm đúng ở min/max; vẽ histogram nhiều bin để thấy "spike".

✍️ *"The IQR rule flags only 2–7 points per variable (< 1%). These values are plausible and within the domain rules, so they are kept. More importantly, 48 students (4.8%) score exactly 100 and 66 have 100% attendance: the values appear to be capped, which creates a ceiling effect. This will be considered when interpreting relationships and building models."*

### §2.7 Cleaning decision log

❌ Tiêu đề là "decision log" nhưng chỉ có code, không có log. Đây là chỗ dễ lấy điểm nhất.

👉 Làm bảng: **Issue | Evidence | Decision | Justification** (xem bài mẫu §2.7). Thêm 1 dòng kết luận trả lời **RQ1**. Nên tạo `df = data.copy()` để giữ dữ liệu gốc.

### §3.1 Target variable

❌
- Conclusion trống.
- **Bug:** đường median có label `"mean = 70.5"` → phải là `"median = …"`.
- Output `score = 100: 48` được in ra nhưng không giải thích.

👉 Thêm Q–Q plot để đánh giá normality.

✍️ *"Exam scores are approximately normal and centred around 70 (mean 69.6, median 70.5), with a slight left skew (−0.16) and a tail of low performers down to 18.4. There is a clear spike at 100 (48 students), so the distribution is cut off at the top (ceiling effect). A linear model is a reasonable starting point, but predictions near 100 may be biased."*

### §3.2 Numerical features

❌ Conclusion trống; dòng `len(num_cols)` thừa.

👉 Nhận xét theo **nhóm hình dạng** thay vì từng biến một.

✍️ *"Study, social-media, Netflix and sleep hours and attendance are bell-shaped, while age, exercise frequency and mental-health rating are almost perfectly uniform – every value appears equally often, which is unusual for real survey data and suggests the data is simulated. All |skew| ≤ 0.24, so no transformation is needed. Spikes at 100 (attendance) and at 0 (Netflix) confirm the capping seen in §2.6."*

### §3.3 Categorical features

❌
- Conclusion trống.
- **Bug:** `elif col == 'internat_quality'` (sai chính tả) → `internet_quality` không được sắp theo thứ tự Poor → Average → Good. Nhìn biểu đồ của bạn: Good, Average, Poor (theo tần suất).

✍️ *"Gender is balanced between Female (48.1%) and Male (47.7%), but 'Other' has only 42 students (4.2%), so estimates for this group are imprecise. Most students have no part-time job (78.5%) and no extracurricular activities (68.2%). 'Fair' diet (43.7%) and 'Good' internet (44.7%) are the most common levels. No level is rare enough to require merging, except that the 'Other' gender group should be interpreted with caution."*

### §4.1 Numerical features vs target

✅ Pearson + Spearman song song, sắp theo |ρ| – rất tốt.

❌
- **Không có ô Conclusion** cho phần này.
- Không có p-value.
- Chưa phân loại mức độ mạnh/yếu.

👉 Thêm p-value (có Holm correction), cột "strength", và 1 bảng chia study hours thành các khoảng để thấy mỗi giờ học thêm ≈ +10 điểm.

✍️ *"Study time is by far the strongest factor (ρ = 0.81, very strong): each extra hour of daily study is linked to about 10 more points. Mental-health rating has a moderate positive association (ρ = 0.32). Social media and Netflix hours have weak negative associations (ρ ≈ −0.17), and exercise, sleep and attendance weak positive ones (ρ = 0.09–0.15). Age, diet and internet quality show no association. Pearson ≈ Spearman for every feature, so the relationships are approximately linear, apart from the ceiling at 100 for students who study 6+ hours."*

### §4.2 Categorical features vs target

✅ Kruskal–Wallis + ε² là lựa chọn rất đúng (không cần giả định normal, chịu được ceiling). Đây là điểm mạnh của bài.

❌
- Conclusion trống.
- Boxplot `diet_quality` và `internet_quality` không theo thứ tự ordinal (Fair, Good, Poor).
- Chạy 6 test nhưng không nhắc tới multiple testing.

✍️ *"None of the six categorical variables is significantly associated with exam score (all p ≥ 0.20) and all effect sizes are negligible (ε² ≤ 0.003). The largest difference in medians is only about 5 points (parental education: Master 66.8 vs Bachelor 71.7), less than one-third of a standard deviation. In this dataset, performance depends on what students do (habits), not on who they are (background)."*

### §5.1 Correlation matrix

❌ Conclusion trống. Heatmap đầy đủ cả 2 tam giác (thừa).

✍️ *"The largest correlation between any two predictors is only |ρ| = 0.06: the predictors are practically independent. This is unexpected for real behaviour data (e.g. more screen time would normally mean less sleep or study) and is further evidence that the data is simulated. The only strong correlations are with the target."*

### §5.2 VIF

✅ Tính VIF đúng cách (có constant, drop const).

❌ Conclusion trống → RQ4 không được trả lời.

✍️ *"All VIF values are between 1.00 and 1.01, far below the usual threshold of 5. There is no multicollinearity, so no variable is redundant (RQ4) and each regression coefficient can be interpreted on its own."*

### Phần còn thiếu hoàn toàn

1. **§5.3 Multiple regression** – bạn đã import `statsmodels.formula.api` nhưng không dùng. Chỉ một mô hình OLS đơn giản cho ra kết quả rất mạnh:
   - Chỉ study hours: R² = 0.68
   - 7 biến thói quen: **R² = 0.90**, RMSE ≈ 5.3 điểm
   - Thêm tất cả biến categorical: không cải thiện (F-test p = 0.81)
   - **Insight hay:** social media, sleep, exercise trông "yếu" khi xét riêng (|ρ| ≈ 0.15) nhưng lại quan trọng khi kiểm soát study hours.
2. **§5.4 Residual plot** – thấy rõ mô hình dự đoán > 100 cho 37 học sinh → ceiling effect.
3. **§6 Summary** – bảng trả lời RQ1–RQ5, key insights, recommendation cho bước modelling, **limitations** (synthetic data, correlation ≠ causation, self-reported, ceiling, nhóm "Other" nhỏ).

Tất cả có trong `EDA_solution_example.ipynb`.

---

## 4. Danh sách lỗi cần sửa ngay (quick fixes)

| # | Vị trí | Lỗi | Sửa |
|---|---|---|---|
| 1 | Cell 15 (§2.1) | "19 missing values" | → **91** |
| 2 | Cell 34 (§3.1) | label median là `mean = …` | → `median = …` |
| 3 | Cell 40 (§3.3) | `'internat_quality'` | → `'internet_quality'` |
| 4 | Cell 25 (§2.5) | "extra spaces: 91" thực ra là NaN | thêm `.dropna()` |
| 5 | §2.4 | không có code | thêm domain rule check |
| 6 | Cell 46 (§4.2) | boxplot ordinal không theo thứ tự | truyền `order=` |
| 7 | Cell 2 | import `smf` không dùng | dùng cho regression hoặc xoá |
| 8 | Cell 29, 37, 40 | `print(len(cols))`, `len(num_cols)` thừa | xoá |
| 9 | 7 ô Conclusion | `??` | viết conclusion (gợi ý ở mục 3) |

---

## 5. Công thức viết một Conclusion tốt (áp dụng cho mọi bài EDA)

Mỗi Conclusion nên có **3 phần** – "What → So what → Now what":

1. **What (quan sát + con số):** *"Study hours has a very strong positive correlation with exam score (ρ = 0.81)."*
2. **So what (ý nghĩa):** *"It is the main driver of performance in this dataset."*
3. **Now what (hành động tiếp theo):** *"It will be the key feature in the model; we check whether it hides the effects of other habits in §5.3."*

**Mẫu câu tiếng Anh hay dùng:**
- Mô tả: *"X is approximately normal / right-skewed / uniform…"*, *"The majority of students (78.5%) …"*
- Quan hệ: *"X shows a strong / moderate / weak positive / negative association with Y (ρ = …)."*
- Không có ý nghĩa: *"There is no evidence that Y differs across X groups (p = …, ε² = …)."*
- Quyết định: *"We decided to … because …"* (luôn có **because**)
- Thận trọng: *"This suggests…"*, *"This is consistent with…"*, *"…should be interpreted with caution because…"*
- Tránh: *"X causes Y"* → dùng *"X is associated with / linked to Y"* (dữ liệu quan sát không chứng minh nhân quả).

**Lỗi ngữ pháp hay gặp trong bài bạn:**
- Chủ ngữ số nhiều + động từ số ít: *"All float columns **is**"* → **are**; *"categorical values **is**"* → **are**.
- *"most strongest"* → **strongest** (không dùng "most" với so sánh nhất -est).
- *"keep its"* → **keep them**.
- Nên bật spell-check trong VS Code/Jupyter (extension *Code Spell Checker*) trước khi nộp.

---

## 6. Thứ tự ưu tiên nếu bạn chỉ có ít thời gian

1. Viết **7 ô Conclusion còn trống** + sửa 4 bug ở mục 4 → tăng khoảng +15–20 điểm.
2. Thêm **§6 Summary** trả lời RQ1–RQ5 + limitations → +5–8 điểm.
3. Thêm bảng **decision log**, code **domain rule**, test **MCAR** → +3–5 điểm.
4. Thêm **regression + residual plot** (§5.3–5.4) → +3–5 điểm, đây là phần giúp lên HD.
