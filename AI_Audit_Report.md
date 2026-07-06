**Faculty of Information Technology (FIT) – Ho Chi Minh City University of Science (HCMUS)**

**CS423 / CSC13003 – Software Testing (AI-augmented · 2026)**

**AI POLICY · TEMPLATES — 2026 v1.0**

# **AI Audit Report — 5-section Template per Artifact**

_Mandatory appendix for every AI-assisted homework (HW#01–HW#06, and Seminar)._

_Adapted from Med Kharbach, PhD (2026) — AI Use Policy Templates for Higher Education. CC BY-NC-SA 4.0. This adaptation is prepared for FIT@HCMUS – CS423 / CSC15003 Software Testing course._

## **1. Student Information**

| Field                                   | Value                                             |
| :-------------------------------------- | :------------------------------------------------ |
| **Student name (printed):**             | [To be filled by student]                         |
| **Student ID:**                         | [To be filled by student]                         |
| **Class / Cohort:**                     | [To be filled by student]                         |
| **Assignment ID (e.g., HW#00, HW#02):** | HW#02                                             |
| **Assignment date:**                    | 06/07/2026                                        |
| **AI tool(s) used:**                    | Antigravity-Gemini 3.1 Pro (High), Github Copilot |
| **AI tool(s) used:**                    | [X] Yes [ ] No                                    |

## **2. Instructions (read before filling)**

- Add one row per AI-generated artifact (test case, script, checklist, OpenAPI spec, JMeter plan, etc.).
- Paste the verbatim prompt — DO NOT paraphrase.
- Paste the verbatim AI output (or include a labelled screenshot in the report).
- Tag the verdict: VALID / INVALID / INCOMPLETE.
- Reasoning must cite a course slide, ISTQB section, or technical RFC.
- Show the corrected artifact with the change highlighted.
- Sample rows are in italic — replace them before submission.

## **3. Audit Table — one row per artifact**

| (1) Prompt + Tool                                                                                                                                                                                | (2) AI Output                                                                                                                                                                                      | (3) Verdict | (4) Reasoning (ISTQB)                                                                                                                                                                                   | (5) Student Fix                                                             |
| :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :---------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | :-------------------------------------------------------------------------- |
| **Tool: Antigravity-Gemini 3.1 Pro (High) <br> Time: 22:24 30/06/2026 <br> Prompt: "[skills.md] hãy follow theo skill này và test chức năng FR-04 Profile management..."**                       | "## ✅ STATE 1 Complete — Feature Analysis & Variable Extraction. Tôi đã hoàn thành phân tích FR-04... trích xuất được 6 biến đầu vào... 5 Bugs đã phát hiện từ source code..."                    | VALID       | AI correctly analyzed the requirements based on provided `skills.md` and identified necessary input variables and early bugs, strictly following Equivalence Partitioning principles (ISTQB FL §4.2.1). | Accepted as-is.                                                             |
| **Tool: Antigravity-Gemini 3.1 Pro (High) <br> Time: 22:26 30/06/2026 <br> Prompt: "APPROVE"**                                                                                                   | "## ✅ STATE 2 Complete — Boundary Value Identification. Tôi đã phân tích BVA cho tất cả 6 biến, tổng cộng ~43 boundary test values. Điểm nổi bật: Variable phone..."                              | VALID       | AI correctly identified BVA values including security boundaries (role: "admin") per ISTQB FL Boundary Value Analysis techniques (ISTQB FL §4.2.2).                                                     | Accepted as-is.                                                             |
| **Tool: Antigravity-Gemini 3.1 Pro (High) <br> Time: 22:35 30/06/2026 <br> Prompt: "APPROVE"**                                                                                                   | "## ✅ STATE 3 Complete — Test Case Generation. Tôi đã tạo 31 test cases trong file FR04_TestCases.md..."                                                                                          | INCOMPLETE  | While AI generated test cases for BVA, it missed concurrent update testing on profile. ISTQB FL suggests considering race conditions for update operations when analyzing edge cases.                   | Added TC32: "Concurrent update of shipping_address" to `FR04_TestCases.md`. |
| **Tool: Antigravity-Gemini 3.1 Pro (High) <br> Time: 20:54 1/7/2026 <br> Prompt: "[skills] follow các skills này và thiết kế test case cho chức năng FR-10 Order state machine"**                | "STATE 1: Feature Analysis & Variable Extraction cho chức năng FR-10... Có điểm đáng lưu ý: Tôi có phát hiện một điểm hơi mơ hồ giữa SRS... và Sơ đồ State Machine..."                             | VALID       | AI successfully analyzed state transitions and smartly identified an inconsistency between SRS and State Machine diagram, matching Static Testing principles (ISTQB FL §3.1).                           | Accepted as-is.                                                             |
| **Tool: Antigravity-Gemini 3.1 Pro (High) <br> Time: 20:57 1/7/2026 <br> Prompt: "APPROVE"**                                                                                                     | "## ✅ STATE 3 Complete — Test Case Generation. Tôi đã tạo 20 Test Cases dựa trên các giá trị biên đã thống nhất. Các test case này bao trùm toàn bộ các đường chuyển trạng thái..."               | VALID       | State transition test cases generated cover valid and invalid paths, adhering perfectly to ISTQB State Transition Testing (ISTQB FL §4.2.4).                                                            | Accepted as-is.                                                             |
| **Tool: Antigravity-Gemini 3.1 Pro (High) <br> Time: 21:11 1/7/2026 <br> Prompt: "follow các skills này và thiết kế test case cho chức năng FR-15 Product CRUD [skills.md]"**                    | "STATE 1: Feature Analysis & Variable Extraction cho chức năng FR-15 (Product CRUD)... Tôi có nêu thêm 2 điểm Edge Cases ở cuối bản Plan về độ dài của Description/Image URL..."                   | VALID       | Correctly extracted CRUD parameters and highlighted unique constraints and edge cases based on domain knowledge.                                                                                        | Accepted as-is.                                                             |
| **Tool: Antigravity-Gemini 3.1 Pro (High) <br> Time: 21:17 1/7/2026 <br> Prompt: "APPROVE"**                                                                                                     | "## ✅ STATE 3 Complete — Test Case Generation. Tôi đã tạo 20 Test Cases dựa trên các giá trị biên đã thống nhất... FR15_TestCases.md"                                                             | INCOMPLETE  | The test cases lacked negative tests for invalid data types (e.g., passing a string for `category_id`). ISTQB FL requires testing invalid inputs for robustness.                                        | Added TC21: "Input category_id with non-numeric string" to the test cases.  |
| **Tool: Antigravity-Gemini 3.1 Pro (High) <br> Time: 21:26 1/7/2026 <br> Prompt: "APPROVE" (for FR-09 Boundary)**                                                                                | "STATE 2: Boundary Value Identification... Mức tối thiểu để được áp mã, thời khắc sát nút mã hết hạn, Giới hạn max số lần một user được dùng..."                                                   | VALID       | AI accurately pinpointed the critical time and amount boundaries for coupon application using BVA (ISTQB FL §4.2.2).                                                                                    | Accepted as-is.                                                             |
| **Tool: Antigravity-Gemini 3.1 Pro (High) <br> Time: 9:24 4/7/2026 <br> Prompt: "[FR10_TestCases.md] [auto_ui.md] follow các skills và test cases để test api và test trên UI của FR10..."**     | "Tôi đã hoàn tất việc chạy kiểm thử API và UI cho chức năng FR-10... Đã viết script tạm... Sử dụng Browser Subagent đóng vai trò QA Engineer... Tạo Bug Report..."                                 | VALID       | The AI successfully acted as an autonomous agent to execute tests on both API and UI, generating structured bug reports adhering to defect management guidelines (ISTQB FL §5.6).                       | Accepted as-is.                                                             |
| **Tool: Antigravity-Gemini 3.1 Pro (High) <br> Time: 9:48 4/7/2026 <br> Prompt: "[FR09_Mobile_TestCases.md] follow các skills và test cases để test api của FR15... report format như FR04..."** | "Dựa vào file FR09_Mobile_TestCases.md (mặc dù trong prompt bạn vô tình gõ nhầm thành FR15 😅), tôi đã tiến hành viết script tự động để kiểm thử API cho chức năng Áp dụng Mã giảm giá (FR-09)..." | VALID       | The AI demonstrated self-correction logic by noticing the discrepancy between the requested feature and the provided file, proceeding with the correct context.                                         | Accepted as-is.                                                             |

## **4. Summary of AI Accuracy**

Aggregate the verdicts from Section 3 and complete the table below.

| Metric                                   | Count | Percentage |
| :--------------------------------------- | :---- | :--------- |
| **Total AI-generated artifacts audited** | 10    | 100%       |
| **VALID (correct, accepted as-is)**      | 8     | 80%        |
| **INVALID (wrong; rejected)**            | 0     | 0%         |
| **INCOMPLETE (acceptable after edits)**  | 2     | 20%        |

## **5. Conclusion — When should AI be used (or not)?**

**AI completely shined in Feature Analysis and Boundary Value Identification**. By adhering to the `skills.md` guidelines, the AI agents correctly applied Equivalence Partitioning and Boundary Value Analysis to extract variables and edge cases. It was particularly strong at identifying security-related boundaries (e.g., Privilege Escalation via the `role` parameter) and pointing out ambiguous logic in the SRS documents (such as inconsistencies in the State Machine diagram). Furthermore, the AI effectively functioned as a testing execution agent, autonomously running scripts, navigating the UI, and reporting bugs accurately.

**However, AI failed or fell short in deep negative testing and concurrency scenarios**. The AI often prioritized functional boundaries but occasionally missed non-functional edge cases, such as race conditions during profile updates or strict invalid data type inputs (e.g., passing strings instead of numbers to database foreign keys).

**Recommendation:** AI should be used extensively for initial test design (domain analysis, test case generation) and automated execution to speed up QA velocity. However, a QA Engineer must still manually review the generated test cases and inject additional negative and concurrency scenarios to ensure 100% system robustness. AI acts as a powerful co-pilot, but human domain expertise remains essential for complete coverage.

## **6. Mandatory Disclosure (paste verbatim)**

_"The AI Audit Report was initially generated by Antigravity-Gemini 3.1 Pro (High); I reviewed and modified the student fix section, added missing edge cases; the reflection section was written collaboratively by me and the AI. The detailed AI Audit Report is attached as Appendix A. I confirm I did not use AI to generate any artifact listed in the prohibited category."_

## **Signature**

| Student name:       | Nguyen Nhat Nam                               |
| :------------------ | :-------------------------------------------- |
| **Student ID:**     | 23127092                                      |
| **Class / Cohort:** | 23KTPM2                                       |
| **Course:**         | CS423 / CSC13003 – Software Testing           |
| **Instructor:**     | Lam Quang Vu, Ho Tuan Thanh, Truong Phuoc Loc |
| **Date:**           | 06/07/2026                                    |
| **Signature:**      | Nam                                           |
