# CLAUDE.md — IA#3 · Three answers to one brief (CSC13008)

Đây là hướng dẫn cho **cả người học lẫn Claude**. Claude đọc file này ở mỗi phiên làm việc.

- Rubric: `IA3-rubric-three-answers.pdf`
- Đề bài mà assistant đã được giao: `brief.md`
- Cách chạy, bảng *Done means* và 5 câu CSR/SSR: `README.md`
- Hạn nộp: **thứ Tư 04/11/2026, 22:00**
- Remote: `https://github.com/ThanhDu14/IA3-WAD`

---

## 1. Phân tích đề: bài này thực sự chấm cái gì

Có ba file `public/versions/a.js`, `b.js`, `c.js`. Đây là ba câu trả lời của AI cho cùng một `brief.md`. Bài này **không chấm khả năng tự viết code**, mà chấm bốn việc:

1. **Quan sát.** Bạn chạy đủ 9 row (1–8 và `empty`) trên cả 3 version, tức 27 dòng kết quả. Mỗi FAIL phải có ảnh chụp và dòng code gây lỗi.
2. **Diễn đạt.** Mỗi version có *một câu* đưa cho assistant, nêu đủ **row + bằng chứng đã thấy + ràng buộc phải giữ**.
3. **Kiểm chứng AI.** Assistant review code, rồi bạn kiểm tra từng nhận xét của nó bằng một row thật. Bạn ghi rõ chỗ nó bịa lỗi và chỗ nó bỏ sót lỗi.
4. **Kết quả cuối.** Sau khi sửa, cả 3 file qua mọi row **và các request `/api/stock/…` vẫn chạy song song**. Giảng viên chấm bằng script chạy trên `server.mjs` gốc.

| # | Hạng mục | Điểm | Bẫy thường gặp |
|---|---|---|---|
| 1 | VERDICTS.md + evidence/ | 30 | Bỏ sót row chỉ lộ ra ở một điều kiện (`slow`, `hang`, `empty`). Đánh FAIL cho row thực ra PASS. Câu cho assistant mơ hồ |
| 2 | Mọi version qua mọi row | 25 | Sửa cho tổng đúng nhưng biến các stock request thành chạy tuần tự (waterfall hình bậc thang) |
| 3 | ASSISTANT-REVIEW.md | 15 | Dán bản tóm tắt thay cho nguyên văn. Kiểm tra bằng cách đọc ("looks right") thay vì chạy row |
| 4 | CSR-SSR.md | 10 | Trả lời bằng định nghĩa thay vì bằng thứ đã thấy (View source, JS off, số request đếm được) |
| 5 | AI-LOG.md | 10 | Viết bù vào cuối. Phần Changed/Rejected để trống |
| 6 | Scope + commits | 10 | Starter không phải commit đầu tiên. Gộp nhiều fix vào một commit. Sửa file ngoài `public/versions/*.js` |

**Thứ tự commit bắt buộc** (giảng viên đọc `git log`):

```
1. starter                          ← chỉ các file gốc, chưa có gì khác
2. CLAUDE.md                        ← file hướng dẫn này (không phải file starter)
3. predictions                      ← VERDICTS.md mới có phần Prediction
4. assistant review                 ← ASSISTANT-REVIEW.md (nguyên văn)
5. results + evidence               ← 27 dòng + ảnh chụp
6. fix A / fix B / fix C            ← mỗi version một commit, diff chỉ chạm đúng 1 file
7. CSR-SSR, AI-LOG, self-assessment
```

---

## 2. Quy tắc cho Claude (luôn áp dụng)

- **Không bao giờ sửa** `server.mjs`, `public/panel.js`, `public/index.html`, `public/style.css`, `jsconfig.json`, `README.md`, `brief.md`. Code chỉ được sửa trong `public/versions/a.js|b.js|c.js`.
- **Mỗi lần fix chỉ sửa một version file.** Fix xong phải chạy lại cả 9 row cho version đó, kèm kiểm tra song song, rồi mới commit, và commit chỉ chứa file đó.
- Khi sửa phần stock value: **giữ các request `/api/stock/:id` chạy song song** (bắn hết rồi mới chờ, ví dụ `Promise.all`). Không dùng `await` trong vòng lặp.
- Dữ liệu từ API luôn được gán bằng `textContent`, không dùng `innerHTML`. Không dùng `console.error` cho các lỗi được phép xảy ra.
- **Không tiết lộ verdict.** Trước khi commit `predictions`, Claude không nói version nào pass hay fail row nào. Claude chỉ được giải thích code làm gì khi được hỏi.
- **Phiên review (bước 4)** phải là phiên mới (`/clear`). Trong phiên đó Claude **không được mở** `VERDICTS.md`, `AI-LOG.md`, `CSR-SSR.md` hay bất kỳ ghi chú nào của người học.
- **Không bịa quan sát.** Nội dung "What I saw", ảnh chụp và câu trả lời CSR/SSR đến từ lần chạy của người học. Claude chỉ định dạng lại hoặc tìm dòng code tương ứng.
- Script kiểm tra tự động (headless Chrome, đo thời điểm request) đặt **ngoài repo**, ở `../IA3-tools/`. Không thêm file lạ vào repo.
- Mỗi lần dùng assistant thì thêm một entry vào `AI-LOG.md` **ngay lúc đó**, không viết bù.
- Commit message **không** có dòng `Co-Authored-By` hay bất kỳ dòng attribution nào.

Lệnh hữu ích:

```bash
node server.mjs                                   # http://localhost:5050 , /ssr
curl -s -X POST localhost:5050/__mode -H 'Content-Type: application/json' -d '{"mode":"slow"}'
npx -y -p typescript@5 tsc -p jsconfig.json --noEmit --checkJs   # row 8, giống cách chấm
git diff --stat <starter-hash>                    # chỉ được thấy public/versions/*.js + các file .md/evidence mới
```

---

## 3. Các bước làm, kèm prompt dùng sẵn

> Prompt viết bằng tiếng Anh để khớp với ngôn ngữ của brief, README và rubric. Bạn copy nguyên văn, chỉ thay phần `<...>`.

### Bước 0 — Dọn thư mục và tạo commit starter (Claude làm, bạn kiểm tra)

Thư mục hiện tại có ba vấn đề: `public/` đang nằm trong `public-20261101T042433Z-1-001/`, `AI-LOG.md` là file **cũ của IA#2**, và hai file PDF không thuộc starter. Commit đầu tiên chỉ được chứa đúng các file gốc.

**Prompt:**
```
Set up this folder for IA#3. Do not edit the content of any starter file.
1. Move public-20261101T042433Z-1-001/public to ./public and delete the empty wrapper folder.
2. Move AI-LOG.md (it is from IA#2) and both PDFs into ../IA3-ref/ so they are not committed.
3. git init -b main, then commit ONLY README.md brief.md jsconfig.json server.mjs public/ with message "starter".
4. Commit CLAUDE.md separately with message "Add CLAUDE.md (working guide)".
5. Add remote origin https://github.com/ThanhDu14/IA3-WAD.git but do not push yet.
Show me `git log --stat` at the end.
```
✅ Kiểm tra: commit #1 tên `starter` chỉ có 9 file gốc. Chạy `node server.mjs`, mở `http://localhost:5050/?v=a` thấy trang hiện lên.
⚠️ Repo `IA3-WAD` đang **public**. Nên chuyển sang private (`gh repo edit ThanhDu14/IA3-WAD --visibility private --accept-visibility-change-consequences`) để không ai chép được VERDICTS của bạn.

### Bước 1 — Dự đoán (BẠN tự làm, chỉ đọc code)

Đặt `brief.md` cạnh từng file version. Với mỗi yêu cầu trong brief (1–6 và Constraints), tìm dòng code thực hiện nó. Ghi dự đoán của bạn vào `VERDICTS.md`. Dự đoán sai **không bị trừ điểm**; rubric còn muốn bạn ghi lại chỗ đoán sai.

Nếu có đoạn code nào không hiểu, dùng prompt này (nó không làm lộ đáp án):
```
Explain what lines <X–Y> of public/versions/<a>.js do, step by step, in plain words.
Do NOT tell me whether it meets brief.md or which Done-means rows it passes or fails.
```

Khung `VERDICTS.md` (Claude có thể tạo khung trống cho bạn):
```markdown
# VERDICTS — IA#3

## Version A
### Prediction (from reading alone, before running)
- I expect rows … to fail because line … does …
### Results
| Row | PASS/FAIL | What I saw (screen / Network / Console / terminal) | Evidence |
|---|---|---|---|
| 1 | | | |
| 2 | | | |
| 3 | | | |
| 4 | | | |
| 5 | | | |
| 6 | | | |
| 7 | | | |
| 8 | | | |
| empty | | | |
- Line(s) responsible: `a.js:NN` — …
- Verdict: keep / fix / reject — decided by row …
- Prediction vs result: …
- The one sentence for the assistant: …
(repeat for B, C)
```
**Commit:** `git add VERDICTS.md && git commit -m "Predictions from reading, before running"`

### Bước 2 — Assistant review, trong một phiên MỚI (trước khi chạy bảng)

Rubric yêu cầu bước này diễn ra *trước khi bạn cho assistant biết verdict*. Gõ `/clear` rồi dùng prompt:
```
Review public/versions/a.js, b.js and c.js against brief.md and the "Done means" table in README.md.
Read ONLY brief.md, README.md and the three version files — do not open VERDICTS.md or any other notes.
For each version list every problem you claim, with: the line number, the brief item or Done-means row
it breaks, and what a user would see. Say "no problem found" for anything you checked that is fine.
Do not change any file.
```
Lưu câu trả lời **nguyên văn**: gõ `/export` để xuất hội thoại ra file, rồi copy nguyên phần trả lời vào `ASSISTANT-REVIEW.md` dưới các mục *Tool & model*, *Prompt* và *Answer (unedited)*. Bảng kiểm tra sẽ điền ở Bước 4.
**Commit:** `Assistant review of the original files (unedited)`
**AI-LOG:** thêm entry #1.

### Bước 3 — Chạy bảng Done means (BẠN chạy và chụp ảnh)

Làm cho từng version A, B, C: mở DevTools, tick **Disable cache**, mở cả Network và Console, để terminal chạy server ở bên cạnh.

| Row | Điều kiện | Nhìn vào đâu |
|---|---|---|
| 1, 2, 3 | `normal`, reload | 8 thẻ đúng thứ tự, giá `180.000 đ`, stock value `12.360.000 đ`, thẻ cuối `Scarf <i>limited</i>` |
| 4 | `slow`, reload | `Loading products…` khoảng 3 giây, sau đó dòng status biến mất |
| 5 | `error`, reload → chuyển `normal` → bấm Retry | Thông báo lỗi + nút Retry, không có thẻ. Sau Retry: 8 thẻ, thông báo và nút biến mất |
| 6 | `hang`, reload, đếm đến 7 | Sau khoảng 5 giây hiện `Could not load products.` và Retry |
| 7 | Console trong lúc chạy row 1–6 | Không có `Uncaught …`, không có dòng đỏ nào do version tự in |
| 8 | Lệnh `tsc` ở mục 2, hoặc thêm `// @ts-check` trong VS Code | Không có lỗi nào trỏ vào version đó |
| empty | `empty`, reload | `No products yet.` và `0 đ` |
| (song song) | `normal`, Network waterfall | Các `/api/stock/*` bắt đầu cùng lúc, không xếp bậc thang |

Mỗi FAIL cần một ảnh `evidence/<version>-row<N>.png`, ví dụ `evidence/b-row6.png` hoặc `evidence/c-rowempty.png`. Ảnh phải thấy **cả trang lẫn DevTools hoặc terminal** chứng minh lỗi. Ghi con số cụ thể bạn thấy, ví dụ "stock value hiện `X đ`" hay "vẫn Loading ở giây thứ 15".

Bạn đọc ghi chú thô cho Claude, rồi dùng prompt:
```
Here are my raw observations for version <A>: <paste notes>.
Fill the Results table in VERDICTS.md with exactly what I observed (do not add anything I did not see).
For every FAIL, find the line(s) in public/versions/<a>.js responsible and explain why in one line.
Do not change any version file.
```
Sau đó **bạn tự viết** verdict và "one sentence" theo mẫu README: *row + bằng chứng + ràng buộc*. Nhờ Claude soát câu đó:
```
Here is my one sentence for version <A>: "<sentence>". Could an assistant misread it?
Check it names the row, what I saw, and the constraint to keep (e.g. stock requests stay parallel,
textContent only, no console.error). Suggest a sharper wording but keep my meaning.
```
**Commit:** `Results of the Done-means table + evidence`

### Bước 4 — Đối chiếu review với bảng kết quả

```
Compare the claims in ASSISTANT-REVIEW.md with my Results in VERDICTS.md.
Under the unedited answer, add a table: | Claim | Version:line | Row/run that checked it | Confirmed / Not real |
then a second table of failing rows my table found that the review MISSED.
Do not edit the pasted answer. Where a claim was never tested by a row, say so — I will run it.
```
Claim nào chưa có row nào kiểm chứng thì **bạn tự chạy thêm**, không được kiểm tra chỉ bằng cách đọc code.

### Bước 5 — Sửa từng version (mỗi version một phiên, một commit)

Chỉ làm cho version mang verdict **fix** hoặc **reject**. Version **keep** để nguyên, miễn là nó qua hết các row.

**Fix:**
```
<your one sentence from VERDICTS.md>
Change ONLY public/versions/<a>.js. Keep: stock requests in parallel, textContent for API data,
no console.error for expected failures, the 5-second timeout, and the element ids from brief.md.
Then rerun every Done-means row (1–8 + empty) for this version and the parallel check
(record when each /api/stock request starts) and show me the results. Do not commit yet.
```
**Reject:** viết rõ bạn muốn yêu cầu gì thay thế (*"what I would ask for instead"*), sau đó:
```
Rewrite public/versions/<c>.js from scratch to meet brief.md. <what I would ask for instead>.
Same constraints as above. Rerun every row + the parallel check and show me the results.
```
Sau đó **bạn tự chạy lại cả bảng trong browser**. Nếu bản sửa làm hỏng row khác, ghi vào AI-LOG mục *Changed/Rejected*. Đây chính là thứ rubric muốn thấy.
**Commit (chỉ 1 file):** `git add public/versions/<a>.js && git commit -m "Fix A: <row> — <what changed>"`
**AI-LOG:** mỗi lần fix là một entry.

### Bước 6 — CSR vs SSR (BẠN quan sát)

Mở `/?v=a` và `/ssr`. Với mỗi trang, làm lần lượt: View source (`Ctrl+U`), tắt JavaScript rồi reload, đếm request trong tab Network. Ghi lại con số và những gì thấy. Sau đó dùng prompt:
```
Here are my notes for the five CSR vs SSR questions in README.md: <notes>.
Write CSR-SSR.md answering each question from these observations only — quote what I saw and the
request counts. For Q2 explain why /ssr shows &lt;i&gt; and what that shares with textContent.
```

### Bước 7 — Kiểm tra cuối, tự chấm, nộp bài

```
Final check before submission:
1. git diff --stat <starter-commit> -- . ':!*.md' ':!evidence'  → must show only public/versions/*.js
2. Each fix commit touches exactly one version file (git log --stat).
3. Run all rows + parallel check on the final a.js, b.js, c.js under all five modes, and tsc for row 8.
4. Every FAIL in VERDICTS.md has its evidence/<v>-row<N>.png.
Report anything that would lose marks against IA3-rubric-three-answers.pdf. Do not fix anything yet.
```
Viết `SELF_ASSESSMENT_REPORT.md` theo template: bảng 6 tiêu chí × (Max, I claim, Evidence trỏ tới file/row/ảnh/commit), thêm hai mục *What I did not manage* và *What I would do differently*. Hãy chấm **trung thực**: lệch quá ±10 so với điểm thật sẽ bị trừ.

Nén file zip ở thư mục cha, nhớ giữ `.git`:
```bash
cd .. && zip -r <MSSV>_<total>.zip IA3 -x 'IA3/node_modules/*'
```
Nộp bằng tài khoản `<MSSV>@student.hcmus.edu.vn`.

---

## 4. Mẫu entry cho AI-LOG.md (file mới cho IA#3, không dùng lại file IA#2)

```markdown
## 2026-11-0X — <Review | Fix A | Rewrite C>
Tool: Claude Code (Claude Opus 5.5)
Asked for: <prompt, or "the one sentence for A" verbatim>
Kept: …
Changed: …   ← assistant làm sai chỗ nào, row nào phát hiện ra
Rejected: …  ← lỗi bịa ra / bản fix làm hỏng row khác
Checked: rows 1–8 + empty + parallel — <result>, by me in the browser
Commit: "<message>"
```
