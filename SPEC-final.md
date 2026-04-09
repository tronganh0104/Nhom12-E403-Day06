# SPEC — AI Product Hackathon

**Nhóm:** 12
**Track:** VinSchool
**Problem statement (1 câu):** *Phụ huynh học sinh gặp khó khăn trong việc tìm kiếm thông tin về học phí, quy chế tuyển sinh, chính sách ưu đãi và các quy định liên quan của trường. Hiện tại, phụ huynh phải tìm kiếm thông tin qua nhiều kênh khác nhau như website, fanpage, email, hoặc liên hệ trực tiếp với nhà trường, gây tốn thời gian và công sức. AI có thể giúp giải quyết vấn đề này bằng cách cung cấp một trợ lý ảo thông minh, có khả năng trả lời nhanh chóng và chính xác các câu hỏi của phụ huynh về các vấn đề này.*

---

## 1. AI Product Canvas

|   | Value | Trust | Feasibility |
|---|-------|-------|-------------|
| **Câu hỏi** | User nào? Pain gì? AI giải gì? | Khi AI sai thì sao? User sửa bằng cách nào? | Cost/latency bao nhiêu? Risk chính? |
| **Trả lời** | *Phụ huynh tìm hiểu tuyển sinh mất thời gian tra cứu phân tán - AI tổng hợp, tư vấn cá nhân hóa và điều hướng nhanh* | *AI sai quy chế/học phí → mất niềm tin. Chữa bằng: AI luôn trích xuất kèm link nguồn.* | *Chi phí tốn API tokens rất nhỏ/query, latency < 4s, risk chính: hallucinate thông tin tuyển sinh quan trọng.* |

**Automation hay augmentation?** ☐ Automation · ☑ Augmentation
Justify: *Augmentation — Chatbot chỉ hỗ trợ tư vấn ban đầu và điều hướng. Quyết định thuộc về phụ huynh và tư vấn viên. Cost of reject thấp.*

**Learning signal:**

1. User correction đi vào đâu? *Logs tương tác (thumbs up/down, câu hỏi follow-up, chuyển hướng tư vấn viên) được lưu để cải thiện RAG retrieval và Prompt.*
2. Product thu signal gì để biết tốt lên hay tệ đi? *Acceptance rate (nhấn vào CTA "xem học phí", "đăng ký") tăng, Tỷ lệ chuyển tư vấn viên (human handoff rate) giảm.*
3. Data thuộc loại nào? ☐ User-specific · ☑ Domain-specific · ☐ Real-time · ☑ Human-judgment · ☐ Khác: ___
   Có marginal value không? (Model đã biết cái này chưa?) *Có marginal value. Model gốc không nắm được học phí, quy chế chi tiết và nội bộ của Vinschool năm học 2026-2027.*

---

## 2. User Stories — 4 paths

Mỗi feature chính = 1 bảng. AI trả lời xong → chuyện gì xảy ra?

### Feature 1: Gợi ý chương trình và cơ sở học phù hợp (Program & Campus Matching)

**Trigger:** Phụ huynh nhập độ tuổi của con, hệ muốn học hoặc khu vực sinh sống.

| Path | Câu hỏi thiết kế | Mô tả |
|------|-------------------|-------|
| Happy — AI đúng, tự tin | User thấy gì? Flow kết thúc ra sao? | *AI phân tích đúng độ tuổi ra cấp học mầm non/tiểu học/THCS, match đúng cơ sở ở khu vực đó và hiển thị 1-2 tùy chọn tốt nhất. Phụ huynh nhấn nút xem tiếp học phí.* |
| Low-confidence — AI không chắc | System báo "không chắc" bằng cách nào? User quyết thế nào? | *Phụ huynh hỏi mập mờ ("Có trường nào cho con đi học không?"). AI báo "Dạ phụ huynh cho em xin thêm độ tuổi của bé và khu vực sống hiện tại" kèm quick replies để chọn.* |
| Failure — AI sai | User biết AI sai bằng cách nào? Recover ra sao? | *AI gợi ý hệ Nâng Cao Cambridge ở một cơ sở không có chương trình đó. Phụ huynh thấy lạ khi đối chiếu link trang chủ → nhấp vào nút "Chat với nhân viên".* |
| Correction — user sửa | User sửa bằng cách nào? Data đó đi vào đâu? | *Phụ huynh chỉnh lại yêu cầu hoặc chuyển tư vấn viên. Log phiên chat được lưu lại để admin kiểm tra lại logic matching của hệ thống (metadata).* |

### Feature 2: Tra cứu tính toán học phí & chính sách ưu đãi (Tuition Fee & Promos Inquiry)

**Trigger:** Phụ huynh hỏi về học phí, đóng phí như thế nào, có ưu đãi gì cho anh chị em không.

| Path | Câu hỏi thiết kế | Mô tả |
|------|-------------------|-------|
| Happy — AI đúng, tự tin | User thấy gì? Flow kết thúc ra sao? | *AI trích xuất đúng biểu phí 2026-2027, tính toán chi phí theo kỳ/năm, tiền xe buýt, tiền ăn bán trú. Đính kèm nút CTA "Tải biểu phí PDF". Phụ huynh an tâm tải file và tiếp tục hành trình.* |
| Low-confidence — AI không chắc | System báo "không chắc" bằng cách nào? User quyết thế nào? | *Phụ huynh hỏi về "chính sách cho cư dân Vinhomes". AI không rõ do không có trong bảng giá → trả lời "Chính sách này có thể thay đổi, quý phụ huynh xin vui lòng liên hệ tư vấn viên để lấy giá chiết khấu ưu đãi nhất" kèm nút Call.* |
| Failure — AI sai | User biết AI sai bằng cách nào? Recover ra sao? | *AI ảo giác (hallucinate) ra 1 con số giảm 50% học phí không có thực. Phụ huynh kỳ vọng sai, liên hệ trường nhưng bị từ chối. Hậu quả lớn. Recover: AI LUÔN ĐÍNH KÈM link điều khoản gốc.* |
| Correction — user sửa | User sửa bằng cách nào? Data đó đi vào đâu? | *User vote Thumbs-down / Báo giá sai. Admin sửa lại tập Knowledge Base JSON (Bổ sung phần "Lưu ý về ưu đãi cư dân") để RAG trả lời chính xác trong tương lai.* |

### Feature 3: Hỏi đáp quy chế tuyển sinh & Thủ tục nhập học (Admissions FAQ)

**Trigger:** Phụ huynh hỏi "điều kiện đầu vào", "chính sách bảo lưu hồ sơ/ hoàn tiền", "xin nghỉ".

| Path | Câu hỏi thiết kế | Mô tả |
|------|-------------------|-------|
| Happy — AI đúng, tự tin | User thấy gì? Flow kết thúc ra sao? | *AI liệt kê các bước nộp hồ sơ, giấy tờ cần chuẩn bị và hạn nộp. Kèm theo link "Đăng ký trực tuyến tại VinschoolOne". User làm theo và nộp hồ sơ thành công.* |
| Low-confidence — AI không chắc | System báo "không chắc" bằng cách nào? User quyết thế nào? | *Trường hợp phụ huynh hỏi tình huống ngách (VD: "chuyển trường từ nước ngoài về"). AI báo không có thông tin chi tiết và khuyên phụ huynh gọi hotline phòng tuyển sinh.* |
| Failure — AI sai | User biết AI sai bằng cách nào? Recover ra sao? | *AI báo sai chính sách rút hồ sơ (được hoàn 100% học phí thay vì không hoàn). Phụ huynh khi làm thủ tục thấy sai sẽ rất phẫn nộ. Recover: Yêu cầu AI luôn thêm câu disclaimer: "Thông tin chỉ mang tính tham khảo nhanh, phụ huynh xem chi tiết tại link..."* |
| Correction — user sửa | User sửa bằng cách nào? Data đó đi vào đâu? | *Phụ huynh complain qua hotline khi AI báo sai. Đội vận hành kiểm tra log sinh từ AI, sau đó cập nhật lại tài liệu vào RAG index để khắc phục.* |

---

## 3. Eval metrics + threshold

**Optimize precision hay recall?** ☑ Precision · ☐ Recall
Tại sao? *Vì nếu chatbot cung cấp thông tin tuyển sinh sai (sai chương trình, sai học phí), điều này sẽ gây hiểu lầm nghiêm trọng và làm mất niềm tin của phụ huynh. Rủi ro của false positive (trả lời sai) lớn hơn nhiều so với false negative (không trả lời được). Do đó, thà AI từ chối trả lời hoặc yêu cầu cung cấp thêm thông tin còn hơn đưa ra thông tin sai lệch.*
Nếu sai ngược lại thì chuyện gì xảy ra? *Nếu optimize recall nhưng low precision → Chatbot trả lời lan man, có thể lấy râu ông nọ cắm cằm bà kia (nhầm lẫn học phí giữa các hệ hoặc các cấp), phụ huynh bực bội do tốn thời gian lọc thông tin và chốt bực mình.*

| Metric | Threshold | Red flag (dừng khi) |
|--------|-----------|---------------------|
| *Recommendation precision (Gợi ý đúng cấp học/cơ sở)* | *≥ 85%* | *< 70% trong 1 tuần (thường xuyên gợi ý sai)* |
| *Acceptance rate (User click CTA tiếp tục follow flow)* | *≥ 30%* | *< 10% (Drop-off quá nhiều do luồng nghẽn)* |
| *Correction rate (User phải sửa lại prompt)* | *≤ 20%* | *> 40% (AI hiểu sai intent người dùng)* |
| *Latency (Thời gian phản hồi)* | *< 4s* | *> 8s (Gây ức chế trong trải nghiệm chat)* |

---

## 4. Top 3 failure modes

*Liệt kê cách product có thể fail — không phải list features.*
*"Failure mode nào user KHÔNG BIẾT bị sai? Đó là cái nguy hiểm nhất."*

| # | Trigger | Hậu quả | Mitigation |
|---|---------|---------|------------|
| 1 | *Phụ huynh nhập câu hỏi rất mơ hồ (VD: "Trường có tốt không?", "Trường nào phù hợp cho con mình?")* | *AI trả lời chung chung, không có call-to-action (CTA), khiến user bị rơi vào ngõ cụt và bỏ đi.* | *Thiết lập prompt bắt buộc AI phải hỏi lại để làm rõ (Clarification: xin độ tuổi con, khu vực sống) + cung cấp các nút Quick Replies.* |
| 2 | *AI tìm sai (hallucinate) dữ liệu học phí hoặc chính sách chiết khấu (đây là mảng dữ liệu nhạy cảm thiết kế dưới dạng bảng phức tạp).* | *Phụ huynh kỳ vọng một mức chi phí ưu đãi không có thực, dễ dẫn đến khiếu nại, ảnh hưởng hình ảnh trường.* | *Buộc AI LUÔN HIỂN THỊ LINK/NÚT ĐÍNH KÈM FILE PDF gốc. Cấm tự ý tính toán con số không có trong data.* |
| 3 | *AI đưa ra câu trả lời đúng, dài dòng nhưng quên điều hướng hướng đi tiếp theo cho người dùng.* | *Phụ huynh nhận được thông tin xong thì không biết bước tiếp theo phải làm thủ tục gì hay vào đâu đăng ký, làm giảm tỷ lệ chuyển đổi đăng ký hụt.* | *Quy định Prompt luôn kết thúc câu trả lời bằng một câu khơi gợi hoặc hành động cụ thể. (VD: "Anh/chị có muốn xem thêm chi tiết biểu phí hoặc hướng dẫn nộp hồ sơ không ạ?").* |

---

## 5. ROI 3 kịch bản

|   | Conservative | Realistic | Optimistic |
|---|-------------|-----------|------------|
| **Assumption** | *50 phiên chat/ngày, 60% user hài lòng* | *200 phiên chat/ngày, 75% user hài lòng* | *500+ phiên chat/ngày, 85% user hài lòng* |
| **Cost** | *$10/tháng (API & Hosting)* | *$30/tháng* | *$80/tháng* |
| **Benefit** | *Tiết kiệm 1h thao tác tư vấn Hotline/ngày* | *Tiết kiệm 5h/ngày cho đội Telesales/Admin* | *Tiết kiệm 12h/ngày, tăng 5% tỷ lệ ghi danh* |
| **Net** | *Lãi nhẹ về tài nguyên con người* | *Giảm tải CSKH đáng kể* | *Chuyển đổi số toàn diện mảng Tư Vấn TS* |

**Kill criteria:** *Kill tính năng nếu tỷ lệ báo cáo Chatbot trả lời sai học phí vượt quá 3 report/tuần, hoặc Acceptance rate (Tỷ lệ tương tác thành công sau trả lời của AI) không đạt > 10% trong vòng trọn 1 tháng triển khai.*

---

## 6. Mini AI spec (1 trang)

**Tên hệ thống:** Trợ lý Ảo Tuyển Sinh Vinschool (AI Admissions Copilot)

**Vấn đề & Người dùng (Who & Why):**
Phụ huynh tại Việt Nam thường thấy bối rối trước lượng thông tin khổng lồ khi tra cứu tuyển sinh cho con (thông tin về cấp học, hệ Cambridge nâng cao/chuẩn, học phí theo từng trường, khu vực). Thông tin này nằm rải rác trên website làm mất thời gian đối chiếu.

**Giải pháp (What):**
Xây dựng một chatbot RAG chuyên môn hóa (Agumentation Tool) cho mảng tư vấn tuyển sinh Vinschool. Chatbot giúp:
1. Hỏi ngữ cảnh để nắm "Độ tuổi" và "Khu vực" sống của học sinh.
2. Từ đó khoanh vùng chính xác Cấp học, Hệ đào tạo phù hợp và cơ sở Vinschool gần nhà.
3. Kịp thời cung cấp Biểu phí của khu vực trực tiếp kèm theo link gốc.
4. Trả lời linh hoạt các câu hỏi dạng FAQ về thủ tục.

**Performance (How well):**
- Hệ thống ưu tiên **Precision** > Recall. Cần đảm bảo rằng các chính sách học phí hay chương trình học được cung cấp đúng 100%. Nhận diện đúng điều người dùng muốn hỏi (Intent accuracy) ≥ 85%.
- Response time < 4 giây.

**Rủi ro trọng tâm (Risks):**
- System rủi ro nhất khi AI tạo ra ảo giác (Hallucination) về học phí hoặc điều kiện nhập học chưa từng có. 
- Mitigation: Luôn bổ sung Disclaimer ("Đây là tư vấn AI để tham khảo..."), chỉ trích xuất từ dữ liệu chuẩn xác, kèm Link tài liệu PDF cho bất kỳ thông tin liên quan tới con số (Tiền tệ/thời điểm).

**Data Flywheel:**
Lưu trữ log của những từ chối trả lời (fallback) hay phiên người dùng bấm phẫn nộ (thumbs-down), hoặc lúc họ chọn phím "Chuyển tư vấn viên". Admin hệ thống sẽ review lại những "Gaps" dữ liệu này để đưa thông tin mới vào JSON/Markdown RAG docs. Qua đó, Copilot tự động "khôn" lên sau từng đợt công bố quy chế mới của nhà trường.