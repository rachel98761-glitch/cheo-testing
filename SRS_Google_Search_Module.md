# Software Requirements Specification (SRS Test)
# Module: Search — Google.com

| Thuộc tính | Giá trị |
|---|---|
| Hệ thống | Google Search (https://www.google.com) |
| Module | Search (Tìm kiếm) |
| Phiên bản tài liệu | 1.0 |
| Ngày lập | 31/10/2026 |
| Người lập | rachel |
| Phương pháp | Reverse-engineering từ giao diện thực tế (khảo sát trực tiếp trên trình duyệt) |
| Trạng thái | Draft |

---

## 1. Giới thiệu

### 1.1 Mục đích
Tài liệu mô tả các yêu cầu chức năng và phi chức năng của module **Search** trên google.com, làm cơ sở cho việc thiết kế test case, test scenario và kiểm thử (manual/automation).

### 1.2 Phạm vi
Bao gồm:
- Ô tìm kiếm trên trang chủ và trang kết quả (SERP)
- Gợi ý tìm kiếm (Autocomplete / Search suggestions)
- Nút **Google Search** và **I'm Feeling Lucky**
- Tìm kiếm bằng giọng nói (Voice search) và bằng hình ảnh (Search by image / Google Lens) — ở mức điểm vào (entry point)
- Nút **AI Mode** và khối **AI Overview**
- Trang kết quả tìm kiếm: danh sách kết quả, sửa chính tả, tab phân loại, bộ lọc Tools, "People also ask", "People also search for", phân trang

Ngoài phạm vi:
- Thuật toán xếp hạng (ranking) nội bộ
- Đăng nhập / quản lý tài khoản Google
- Chi tiết bên trong các sản phẩm khác (Images, Shopping, Videos, Gmail…)
- Quảng cáo (Ads) và hệ thống tính phí

### 1.3 Định nghĩa & viết tắt
| Thuật ngữ | Ý nghĩa |
|---|---|
| SERP | Search Engine Results Page — trang kết quả tìm kiếm |
| Query / Từ khóa | Chuỗi người dùng nhập để tìm kiếm |
| Autocomplete | Danh sách gợi ý hiển thị khi người dùng đang gõ |
| AI Overview | Khối tóm tắt do AI sinh ra ở đầu SERP |
| AI Mode | Chế độ tìm kiếm hội thoại bằng AI |
| PAA | People also ask — khối câu hỏi liên quan |
| FR / NFR | Functional / Non-functional Requirement |

### 1.4 Tài liệu tham khảo
- Giao diện https://www.google.com (khảo sát ngày 31/10/2026)
- How Search works: https://google.com/search/howsearchworks/
- IEEE 830 / ISO/IEC/IEEE 29148 (cấu trúc SRS)

---

## 2. Mô tả tổng quan

### 2.1 Bối cảnh sản phẩm
Search là chức năng cốt lõi của google.com. Người dùng nhập truy vấn (văn bản, giọng nói, hình ảnh) và nhận về danh sách kết quả liên quan từ web cùng các khối thông tin bổ trợ (AI Overview, PAA, video, thảo luận…).

### 2.2 Đối tượng người dùng
| Nhóm | Mô tả |
|---|---|
| Khách (Guest) | Chưa đăng nhập, dùng đầy đủ tính năng tìm kiếm cơ bản |
| Người dùng đã đăng nhập | Có thêm cá nhân hóa (lịch sử, gợi ý theo lịch sử) |
| Người dùng thiết bị di động | Truy cập qua trình duyệt mobile, giao diện responsive |
| Người dùng hỗ trợ tiếp cận | Dùng screen reader / bàn phím |

### 2.3 Môi trường vận hành
- Trình duyệt: Chrome, Firefox, Safari, Edge (2 phiên bản mới nhất)
- Thiết bị: Desktop, Tablet, Mobile
- Kết nối Internet bắt buộc

### 2.4 Giả định & ràng buộc
- Kết quả tìm kiếm phụ thuộc vị trí (quốc gia/IP), ngôn ngữ và trạng thái đăng nhập.
- Nội dung gợi ý, AI Overview và các khối động thay đổi theo thời gian — test cần kiểm tra **cấu trúc/hành vi**, không assert nội dung cố định.
- Google có cơ chế chống bot (CAPTCHA "unusual traffic") khi truy vấn tự động với tần suất cao.

---

## 3. Yêu cầu chức năng

### 3.1 Trang chủ tìm kiếm (Home)

| ID | Yêu cầu | Độ ưu tiên |
|---|---|---|
| FR-HOME-01 | Trang chủ hiển thị logo Google (hoặc Doodle theo dịp, ví dụ Halloween) ở trung tâm. | Medium |
| FR-HOME-02 | Hiển thị ô tìm kiếm (combobox, accessible name "Search") ở giữa trang. | High |
| FR-HOME-03 | Ô tìm kiếm có các thành phần: nút "Add files and tools" (+), nút **Search by voice** (micro), nút **Search by image** (camera), nút **AI Mode**. | High |
| FR-HOME-04 | Dưới ô tìm kiếm có 2 nút: **Google Search** (submit) và **I'm Feeling Lucky**. | High |
| FR-HOME-05 | Header hiển thị: About, Store, Gmail, Images, Google apps, Sign in. | Low |
| FR-HOME-06 | Footer hiển thị: Advertising, Business, How Search works, Privacy, Terms, Settings. | Low |

### 3.2 Nhập truy vấn

| ID | Yêu cầu | Độ ưu tiên |
|---|---|---|
| FR-INPUT-01 | Người dùng có thể click/focus vào ô tìm kiếm và nhập văn bản. | High |
| FR-INPUT-02 | Ô tìm kiếm hỗ trợ ký tự Unicode (tiếng Việt có dấu, CJK, emoji, ký tự đặc biệt). | High |
| FR-INPUT-03 | Khi ô có nội dung, hiển thị nút **Clear (X)**; click X xóa toàn bộ nội dung và giữ focus trong ô. | Medium |
| FR-INPUT-04 | Hỗ trợ phím tắt văn bản tiêu chuẩn (Ctrl/Cmd+A, copy, paste, undo). | Medium |
| FR-INPUT-05 | Hỗ trợ cú pháp tìm kiếm nâng cao: `"cụm từ chính xác"`, `-loại trừ`, `site:`, `filetype:`, `OR`. | Medium |
| FR-INPUT-06 | Truy vấn rất dài (ví dụ > 2048 ký tự) phải được xử lý an toàn (cắt bớt hoặc báo lỗi), không làm vỡ giao diện. | Medium |

### 3.3 Gợi ý tìm kiếm (Autocomplete)

| ID | Yêu cầu | Độ ưu tiên |
|---|---|---|
| FR-AC-01 | Khi người dùng gõ, hiển thị dropdown gợi ý ngay dưới ô tìm kiếm. | High |
| FR-AC-02 | Hiển thị tối đa **10** gợi ý, mỗi dòng có icon kính lúp. | Medium |
| FR-AC-03 | Phần ký tự hoàn thành thêm (không do người dùng gõ) được **in đậm**. | Low |
| FR-AC-04 | Gợi ý cập nhật theo thời gian thực sau mỗi lần thay đổi ký tự. | High |
| FR-AC-05 | Dùng phím ↑/↓ để di chuyển giữa các gợi ý; nội dung ô cập nhật theo gợi ý đang chọn. Enter để tìm theo gợi ý đó. | High |
| FR-AC-06 | Click vào một gợi ý thực hiện tìm kiếm ngay với gợi ý đó. | High |
| FR-AC-07 | Nhấn Esc hoặc click ra ngoài đóng dropdown. | Medium |
| FR-AC-08 | Dropdown hiển thị nút Google Search, I'm Feeling Lucky và link **"Report inappropriate predictions"**. | Low |
| FR-AC-09 | Khi đã đăng nhập, gợi ý từ lịch sử tìm kiếm có icon đồng hồ và có thể xóa ("Remove"). | Low |
| FR-AC-10 | Khi đang có nội dung trong ô, nút AI Mode chuyển sang dạng "AI Mode →". | Low |

### 3.4 Thực hiện tìm kiếm

| ID | Yêu cầu | Độ ưu tiên |
|---|---|---|
| FR-SEARCH-01 | Nhấn **Enter** hoặc click **Google Search** điều hướng tới `/search?q=<query>` và hiển thị SERP. | High |
| FR-SEARCH-02 | Truy vấn được URL-encode đúng trong tham số `q` (khoảng trắng → `+`, ký tự đặc biệt → `%XX`). | High |
| FR-SEARCH-03 | Truy cập trực tiếp URL `https://www.google.com/search?q=<query>` cho cùng kết quả như tìm từ ô tìm kiếm. | High |
| FR-SEARCH-04 | Truy vấn **rỗng** (hoặc chỉ có khoảng trắng) không thực hiện tìm kiếm; người dùng vẫn ở trang chủ. *(Đã xác nhận khi khảo sát.)* | High |
| FR-SEARCH-05 | **I'm Feeling Lucky** với truy vấn hợp lệ điều hướng thẳng tới kết quả đầu tiên; với truy vấn rỗng điều hướng tới trang Doodles/trang đặc biệt. | Medium |
| FR-SEARCH-06 | Tiêu đề tab trình duyệt có dạng `<query> - Google Search`. | Low |
| FR-SEARCH-07 | Ô tìm kiếm trên SERP giữ nguyên truy vấn đã tìm và có thể chỉnh sửa để tìm lại. | High |

### 3.5 Tìm kiếm bằng giọng nói & hình ảnh

| ID | Yêu cầu | Độ ưu tiên |
|---|---|---|
| FR-VOICE-01 | Click micro yêu cầu quyền microphone của trình duyệt; nếu được cấp, hiển thị giao diện nghe ("Listening…"). | Medium |
| FR-VOICE-02 | Nội dung nhận dạng được chuyển thành truy vấn và tự động tìm kiếm. | Medium |
| FR-VOICE-03 | Nếu từ chối quyền micro, hiển thị thông báo hướng dẫn bật quyền. | Medium |
| FR-IMG-01 | Click camera mở hộp thoại Google Lens: kéo-thả ảnh, tải ảnh lên, hoặc dán link ảnh. | Medium |
| FR-IMG-02 | Định dạng ảnh không hỗ trợ hoặc link không hợp lệ phải có thông báo lỗi. | Medium |

### 3.6 Trang kết quả tìm kiếm (SERP)

| ID | Yêu cầu | Độ ưu tiên |
|---|---|---|
| FR-SERP-01 | Header SERP gồm: logo Google (click về trang chủ), ô tìm kiếm, nút Clear, micro, camera, Settings, Share, Google apps, Sign in. | High |
| FR-SERP-02 | Thanh tab phân loại: **AI Mode, All, Images, Shopping, Videos, Forums, Short videos, More, Tools**. Tab đang chọn được highlight (mặc định "All"). | High |
| FR-SERP-03 | Mỗi kết quả web hiển thị: favicon, tên site, URL dạng breadcrumb (`domain › path › ...`), tiêu đề (link xanh), ngày đăng (nếu có), đoạn mô tả (snippet) có in đậm từ khóa, "Read more". | High |
| FR-SERP-04 | Một số kết quả có **sitelinks** (link con) hoặc các chip liên kết bên dưới. | Low |
| FR-SERP-05 | Mỗi kết quả có menu ⋮ ("About this result"). | Low |
| FR-SERP-06 | Click tiêu đề kết quả điều hướng tới trang đích. | High |
| FR-SERP-07 | Trang có thể hiển thị các khối đặc biệt: **AI Overview**, **People also ask**, **What people are saying** (thảo luận/mạng xã hội), video, **People also search for**. | Medium |
| FR-SERP-08 | Kết quả trùng lặp từ cùng domain được gom nhóm hợp lý (không lặp quá nhiều liên tiếp). | Low |

### 3.7 Sửa chính tả (Spell correction)

| ID | Yêu cầu | Độ ưu tiên |
|---|---|---|
| FR-SPELL-01 | Khi truy vấn có khả năng sai chính tả, SERP hiển thị "**These are results for** <truy vấn đã sửa>" và link "**Search instead for** <truy vấn gốc>". *(Đã quan sát khi khảo sát.)* | Medium |
| FR-SPELL-02 | Click "Search instead for" tìm lại chính xác truy vấn gốc. | Medium |
| FR-SPELL-03 | Với lỗi nhỏ có độ tin cậy thấp, hiển thị "**Did you mean:** <gợi ý>". | Medium |

### 3.8 AI Overview & AI Mode

| ID | Yêu cầu | Độ ưu tiên |
|---|---|---|
| FR-AI-01 | Với các truy vấn phù hợp, đầu SERP hiển thị khối **AI Overview** gồm đoạn tóm tắt và chip nguồn tham khảo (ví dụ "GeeksforGeeks +1"). | Medium |
| FR-AI-02 | AI Overview dài được thu gọn với nút **Show more** để mở rộng. | Low |
| FR-AI-03 | Tab/nút **AI Mode** mở giao diện tìm kiếm hội thoại với truy vấn hiện tại. | Medium |
| FR-AI-04 | Một số câu hỏi trong PAA có link chuyển sang AI Mode ("This link will navigate to AI Mode"). | Low |

### 3.9 People also ask (PAA)

| ID | Yêu cầu | Độ ưu tiên |
|---|---|---|
| FR-PAA-01 | Hiển thị danh sách câu hỏi liên quan dạng accordion (mặc định ~4 câu). | Medium |
| FR-PAA-02 | Click một câu hỏi mở rộng câu trả lời và có thể nạp thêm câu hỏi mới vào danh sách. | Medium |
| FR-PAA-03 | Click lại câu hỏi đang mở sẽ thu gọn. | Low |

### 3.10 Bộ lọc Tools

| ID | Yêu cầu | Độ ưu tiên |
|---|---|---|
| FR-TOOLS-01 | Click **Tools** mở menu gồm: **Any time**, **All results**, **Advanced Search**. *(Đã quan sát.)* | Medium |
| FR-TOOLS-02 | Menu hiển thị số lượng kết quả ước tính và thời gian xử lý (ví dụ "About 2,000,000,000 results (0.30s)"). | Low |
| FR-TOOLS-03 | **Any time** cho phép lọc theo: Past hour, Past 24 hours, Past week, Past month, Past year, Custom range. Kết quả chỉ chứa nội dung trong khoảng thời gian đã chọn. | Medium |
| FR-TOOLS-04 | **All results** cho phép chuyển sang **Verbatim** (tìm chính xác, không sửa/mở rộng từ khóa). | Medium |
| FR-TOOLS-05 | **Advanced Search** điều hướng tới trang tìm kiếm nâng cao. | Low |
| FR-TOOLS-06 | Có tùy chọn **Clear** để bỏ toàn bộ bộ lọc đang áp dụng. | Low |

### 3.11 Tìm kiếm liên quan & phân trang

| ID | Yêu cầu | Độ ưu tiên |
|---|---|---|
| FR-REL-01 | Cuối SERP hiển thị khối **People also search for** gồm ~8 truy vấn liên quan (phần khác biệt được in đậm). Click một mục tìm kiếm truy vấn đó. | Medium |
| FR-PAGE-01 | Cuối SERP có thanh phân trang "Gooooooooogle" với số trang **1–10** và nút **Next**; trang hiện tại không phải là link. | High |
| FR-PAGE-02 | Từ trang 2 trở đi hiển thị nút **Previous**. | High |
| FR-PAGE-03 | Chuyển trang cập nhật tham số `start` trên URL (trang 2 → `start=10`, …). | Medium |
| FR-PAGE-04 | Một số giao diện/thiết bị dùng **cuộn vô hạn** hoặc nút "More results" thay cho phân trang. | Low |

### 3.12 Footer SERP

| ID | Yêu cầu | Độ ưu tiên |
|---|---|---|
| FR-FOOT-01 | Hiển thị vị trí hiện tại (ví dụ "United States · From your IP address · Update location"). | Low |
| FR-FOOT-02 | Hiển thị các link: Help, Send feedback, Privacy, Terms. | Low |

### 3.13 Xử lý ngoại lệ

| ID | Yêu cầu | Độ ưu tiên |
|---|---|---|
| FR-ERR-01 | Không có kết quả phù hợp: hiển thị thông báo "Your search - <query> - did not match any documents." kèm gợi ý (kiểm tra chính tả, dùng từ khóa khác/tổng quát hơn). | High |
| FR-ERR-02 | Truy vấn chứa mã HTML/JS (ví dụ `<script>alert(1)</script>`) được escape, không thực thi (chống XSS). | High |
| FR-ERR-03 | Mất kết nối mạng: trình duyệt hiển thị trang lỗi, không mất truy vấn đã nhập khi tải lại. | Medium |
| FR-ERR-04 | Phát hiện lưu lượng bất thường: hiển thị trang "unusual traffic" kèm CAPTCHA. | Medium |

---

## 4. Yêu cầu phi chức năng

| ID | Loại | Yêu cầu |
|---|---|---|
| NFR-PERF-01 | Hiệu năng | Gợi ý autocomplete xuất hiện trong ≤ 200 ms sau khi gõ (mạng ổn định). |
| NFR-PERF-02 | Hiệu năng | SERP tải nội dung chính (LCP) trong ≤ 2.5 s; thời gian xử lý phía server hiển thị ~0.3 s. |
| NFR-AVAIL-01 | Sẵn sàng | Dịch vụ sẵn sàng ≥ 99.9%. |
| NFR-USA-01 | Khả dụng | Toàn bộ luồng tìm kiếm thao tác được chỉ bằng bàn phím (Tab, Enter, ↑/↓, Esc). |
| NFR-ACC-01 | Tiếp cận | Tuân thủ WCAG 2.1 AA: ô tìm kiếm có role `combobox` và label "Search"; các nút icon có accessible name (Search by voice, Search by image…). |
| NFR-RESP-01 | Responsive | Giao diện hiển thị đúng trên desktop (≥ 1024px), tablet (768px), mobile (375px), không có thanh cuộn ngang. |
| NFR-COMPAT-01 | Tương thích | Hoạt động trên Chrome, Firefox, Safari, Edge (2 phiên bản mới nhất). |
| NFR-I18N-01 | Đa ngôn ngữ | Giao diện theo ngôn ngữ người dùng/vùng; hỗ trợ truy vấn đa ngôn ngữ và RTL. |
| NFR-SEC-01 | Bảo mật | Toàn bộ trang dùng HTTPS; dữ liệu nhạy cảm được ẩn trong URL (tham số `sxsrf`, `ei`… là token). |
| NFR-SEC-02 | Bảo mật | Chống XSS/injection trên tham số `q`. |
| NFR-PRIV-01 | Riêng tư | Có banner/cài đặt cookie theo luật địa phương; người dùng có thể tắt lịch sử tìm kiếm và SafeSearch trong Settings. |

---

## 5. Use case chính

### UC-01: Tìm kiếm bằng văn bản
- **Actor:** Người dùng
- **Tiền điều kiện:** Đang ở trang chủ google.com
- **Luồng chính:**
  1. Người dùng click vào ô tìm kiếm.
  2. Người dùng nhập từ khóa → hệ thống hiển thị gợi ý.
  3. Người dùng nhấn Enter / click "Google Search".
  4. Hệ thống điều hướng tới `/search?q=...` và hiển thị SERP.
- **Luồng thay thế:**
  - 2a. Người dùng chọn một gợi ý → hệ thống tìm theo gợi ý.
  - 3a. Từ khóa rỗng → hệ thống không làm gì.
  - 4a. Từ khóa sai chính tả → SERP hiển thị kết quả cho từ đã sửa + link "Search instead for".
  - 4b. Không có kết quả → hiển thị thông báo không tìm thấy.
- **Hậu điều kiện:** SERP hiển thị, ô tìm kiếm giữ từ khóa.

### UC-02: Lọc kết quả theo thời gian
1. Ở SERP, click **Tools** → **Any time** → chọn "Past week".
2. Hệ thống tải lại SERP chỉ với kết quả trong 7 ngày gần nhất; nhãn bộ lọc hiển thị "Past week".

### UC-03: Chuyển trang kết quả
1. Cuộn xuống cuối SERP, click **Next** hoặc số trang.
2. Hệ thống hiển thị trang kết quả tương ứng, URL cập nhật tham số `start`.

### UC-04: Tìm kiếm bằng giọng nói
1. Click biểu tượng micro → cấp quyền microphone.
2. Nói từ khóa → hệ thống chuyển thành văn bản và hiển thị SERP.

---

## 6. Gợi ý kịch bản kiểm thử (Test scenarios)

| # | Kịch bản | Yêu cầu liên quan |
|---|---|---|
| TS-01 | Tìm kiếm với từ khóa hợp lệ bằng Enter | FR-SEARCH-01 |
| TS-02 | Tìm kiếm bằng nút Google Search | FR-SEARCH-01 |
| TS-03 | Submit từ khóa rỗng / chỉ khoảng trắng | FR-SEARCH-04 |
| TS-04 | Từ khóa tiếng Việt có dấu, emoji, ký tự đặc biệt | FR-INPUT-02, FR-SEARCH-02 |
| TS-05 | Từ khóa rất dài (> 2048 ký tự) | FR-INPUT-06 |
| TS-06 | Autocomplete: số lượng gợi ý ≤ 10, chọn bằng phím ↑/↓ và chuột | FR-AC-01…06 |
| TS-07 | Đóng autocomplete bằng Esc / click ra ngoài | FR-AC-07 |
| TS-08 | Nút Clear (X) xóa nội dung | FR-INPUT-03 |
| TS-09 | Từ khóa sai chính tả → "These are results for / Search instead for" | FR-SPELL-01, 02 |
| TS-10 | Truy vấn có toán tử `"..."`, `-`, `site:` | FR-INPUT-05 |
| TS-11 | I'm Feeling Lucky với từ khóa hợp lệ / rỗng | FR-SEARCH-05 |
| TS-12 | Chuyển tab Images/Videos/News và quay lại All | FR-SERP-02 |
| TS-13 | Tools → Any time → Past 24 hours | FR-TOOLS-03 |
| TS-14 | Tools → All results → Verbatim | FR-TOOLS-04 |
| TS-15 | Phân trang: Next, Previous, số trang, tham số `start` | FR-PAGE-01…03 |
| TS-16 | Mở/đóng câu hỏi PAA | FR-PAA-01…03 |
| TS-17 | Click "People also search for" | FR-REL-01 |
| TS-18 | XSS payload trong ô tìm kiếm và trong tham số `q` | FR-ERR-02, NFR-SEC-02 |
| TS-19 | Từ chối quyền micro khi dùng voice search | FR-VOICE-03 |
| TS-20 | Upload ảnh sai định dạng khi dùng Search by image | FR-IMG-02 |
| TS-21 | Điều hướng toàn bộ bằng bàn phím + screen reader | NFR-USA-01, NFR-ACC-01 |
| TS-22 | Hiển thị responsive trên mobile 375px | NFR-RESP-01 |
| TS-23 | Back/Forward trình duyệt giữ đúng truy vấn và trang | FR-SEARCH-07 |

---

## 7. Ma trận truy vết (rút gọn)

| Nhóm yêu cầu | Số lượng FR | Test scenarios |
|---|---|---|
| Home | 6 | — (kiểm tra UI) |
| Input | 6 | TS-04, 05, 08, 10 |
| Autocomplete | 10 | TS-06, 07 |
| Search | 7 | TS-01, 02, 03, 11, 23 |
| Voice/Image | 5 | TS-19, 20 |
| SERP | 8 | TS-12 |
| Spell | 3 | TS-09 |
| AI | 4 | — |
| PAA | 3 | TS-16 |
| Tools | 6 | TS-13, 14 |
| Related/Paging | 5 | TS-15, 17 |
| Error | 4 | TS-18 |

---

## 8. Ghi chú khảo sát
- Khảo sát thực hiện ngày 31/10/2026 trên trình duyệt desktop, chưa đăng nhập, vị trí United States.
- Trang chủ hiển thị Doodle Halloween và link "🎃 Happy Halloween!".
- Đã xác nhận trực tiếp: autocomplete 10 gợi ý, sửa chính tả ("These are results for… / Search instead for…"), AI Overview, menu Tools (Any time / All results / Advanced Search, ~0.30s), People also search for, phân trang 1–10 + Next, submit rỗng không điều hướng.
- Các yêu cầu còn lại (voice, image, Verbatim, thông báo không có kết quả, CAPTCHA…) dựa trên hành vi đã biết của Google, cần xác minh lại khi viết test case chi tiết.
- Truy vấn vô nghĩa dạng chuỗi ngẫu nhiên vẫn có thể trả về kết quả gần đúng, nên khi test FR-ERR-01 cần dùng truy vấn kết hợp toán tử `"..."` để đảm bảo không có kết quả.
