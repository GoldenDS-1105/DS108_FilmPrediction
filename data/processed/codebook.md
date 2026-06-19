# Code Book — Movie Revenue Dataset

## 1. movie_final.csv (dataset gốc sau khi gộp)

| Cột | Kiểu | Miền giá trị | Ý nghĩa |
|---|---|---|---|
| `id` | int | > 0 | ID phim (IMDB hoặc TMDB) |
| `title` | str | — | Tên phim (chữ thường) |
| `budget` | float | ≥ 0, có thể NaN | Ngân sách sản xuất (USD) |
| `revenue` | float | > 0, có thể NaN | Doanh thu phòng vé (USD) |
| `release_date` | str (YYYY-MM-DD) | 2020–2026 | Ngày phát hành |
| `genres` | str | ký tự `\|` ngăn cách | Thể loại phim (multi-label) |
| `vote_average` | float | 0–10 | Điểm trung bình từ khán giả |
| `vote_count` | int | ≥ 0 | Số lượt bình chọn |
| `runtime` | float | > 0, có thể NaN | Thời lượng phim (phút) |
| `original_language` | str | `en` | Ngôn ngữ gốc (chỉ giữ tiếng Anh) |
| `director` | str | có thể NaN | Tên đạo diễn chính |

---

## 2. train_movie.csv / test_movie.csv (sau NB3)

Giống `movie_final.csv` nhưng đã:
- Xóa dòng `revenue` null
- Bổ sung tên đạo diễn từ `director_fix.csv`
- Drop: `id`, `title`, `original_language`, `vote_average`, `vote_count`
- Giá trị `budget ≤ 0` và `runtime ≤ 0` chuyển thành NaN
- Chia 80/20 (random_state=42)

---

## 3. train_missing_handled.csv / test_missing_handled.csv (sau NB4)

Thêm 2 cột flag và đã impute:

| Cột | Kiểu | Ý nghĩa |
|---|---|---|
| `has_budget` | int (0/1) | 1 = budget ban đầu bị missing, 0 = có dữ liệu gốc |
| `director_missing` | int (0/1) | 1 = director ban đầu bị missing |

Xử lý:
- `release_date` null → xóa dòng
- `genres` null → điền `"Unknown"`
- `runtime` null → median theo director (fallback: global median, tính từ train)
- `budget` null → IterativeImputer + BayesianRidge (fit trên train, transform test)
- `director` null → điền `"Unknown"`

---

## 4. train_fe.csv / test_fe.csv (sau NB5)

Bộ feature đầy đủ cho mô hình. Tổng 33 cột:

| Cột | Kiểu | Ý nghĩa |
|---|---|---|
| `revenue` | float | Target (USD) |
| `runtime` | float | Thời lượng đã Winsorize về [15.5, 163.5] phút |
| `has_budget` | int (0/1) | Flag ngân sách bị missing |
| `director_missing` | int (0/1) | Flag đạo diễn bị missing |
| `log_budget` | float | log1p(budget) |
| `release_year` | int | Năm phát hành |
| `is_weekend` | int (0/1) | 1 = phát hành thứ 6 / thứ 7 / chủ nhật |
| `month_sin` | float | sin(2π × tháng / 12) — cyclical encoding |
| `month_cos` | float | cos(2π × tháng / 12) — cyclical encoding |
| `genre_action` … `genre_western` | int (0/1) | Multi-label binarization, 23 thể loại |
| `director_encoded` | float | Smoothed target encoding: (n·μ_dir + k·μ_global) / (n+k), k=10 |

---

## 5. Annotation files (data/annotation/)

| File | Nội dung |
|---|---|
| `director_fix.csv` | Lookup table: index phim → tên đạo diễn đúng (tra thủ công từ IMDb/Wikipedia) |
| `null_director_index.csv` | Danh sách index phim bị thiếu tên đạo diễn |
| `null_director_titles.csv` | Tên phim tương ứng cần tra đạo diễn |
| `null_genres_index.csv` | Danh sách index phim bị thiếu thể loại |
| `null_genres_titles.csv` | Tên phim tương ứng cần tra thể loại |
