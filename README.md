# DS108 — Dự đoán Doanh thu Phim

**Môn học:** DS108 — Pre-processing & Constructing Dataset  
**Nhóm:** 24521758 · 24521777  
**Dataset:** IMDB + TMDB (2020–2025), ~6 500 phim tiếng Anh

---

## Mô tả đề tài

Xây dựng pipeline tiền xử lý dữ liệu và huấn luyện mô hình dự đoán **doanh thu phim** (`revenue`) dựa trên các thông tin có thể biết trước ngày phát hành: ngân sách, thể loại, đạo diễn, ngày ra mắt, thời lượng.

---

## Cấu trúc thư mục

```
.
├── data/
│   ├── raw/                        # Dữ liệu thô gốc (KHÔNG chỉnh sửa tay)
│   │   ├── imdb_master_dataset_2020_2025.csv
│   │   └── movie1.csv
│   └── processed/                  # Dữ liệu đã làm sạch và feature engineering
│       ├── movie_final.csv
│       ├── train_movie.csv / test_movie.csv
│       ├── train_missing_handled.csv / test_missing_handled.csv
│       ├── train_fe.csv / test_fe.csv
│       └── lightgbm_*.csv / *.pkl
├── notebook/
│   ├── 0_Gộp_data.ipynb                # Gộp IMDB + TMDB, chuẩn hóa
│   ├── 1_EDA_film.ipynb                # Phân tích khám phá dữ liệu
│   ├── 2_DetectMissingValue.ipynb      # Kiểm định MCAR / MAR / MNAR
│   ├── 3_Cleaning_+_Split.ipynb        # Làm sạch, chia train/test
│   ├── 4_MissingValue.ipynb            # Xử lý missing value
│   ├── 5_Log_+_Feature_Engineering.ipynb  # Feature engineering
│   └── 6_TrainModel_LightGBM.ipynb        # Huấn luyện & đánh giá mô hình
├── requirements.txt
└── README.md
```

---

## Cài đặt môi trường

```bash
pip install -r requirements.txt
```

Python >= 3.10 được khuyến nghị.

---

## Cách chạy

Chạy các notebook theo thứ tự từ 0 đến 6 từ thư mục `notebook/`:

| Notebook | Đầu vào | Đầu ra |
|---|---|---|
| `0_Gộp_data` | `data/raw/*.csv` | `data/processed/movie_final.csv` |
| `1_EDA_film` | `movie_final.csv` | *(chỉ phân tích)* |
| `2_DetectMissingValue` | `movie_final.csv` | *(chỉ phân tích)* |
| `3_Cleaning_+_Split` | `movie_final.csv` | `train_movie.csv`, `test_movie.csv` |
| `4_MissingValue` | `train/test_movie.csv` | `train/test_missing_handled.csv` |
| `5_Log_+_Feature_Engineering` | `train/test_missing_handled.csv` | `train/test_fe.csv` |
| `6_TrainModel_LightGBM` | `train/test_fe.csv` | `lightgbm_*.csv`, `*.pkl` |

---

## Kết quả mô hình

| Mô hình | R² raw | RMSE (USD) | MAE (USD) |
|---|---|---|---|
| Dummy baseline | -0.0002 | 127.3M | 42.4M |
| log_model | 0.503 | 89.7M | 17.5M |
| raw_model | 0.628 | 77.6M | 17.8M |
| sqrt_model | 0.645 | 75.8M | 15.3M |
| **blend_model** | **0.648** | **75.5M** | **15.7M** |

---

## Dataset công khai

Dataset đã được publish trên Kaggle:  
**[Movie Revenue Prediction Dataset (IMDB + TMDB 2020–2025)](https://www.kaggle.com/datasets/nguyentienviet1105/movie-revenue-prediction-dataset)**

---

## Nguồn dữ liệu

- **IMDB**: scraping thủ công qua IMDb API (2020–2025)
- **TMDB**: The Movie Database public dataset
