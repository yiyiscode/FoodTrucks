# 關 0：基準量測

> 預計 30 分鐘。這一關不改任何程式碼，只量數字。

## 為什麼要做

面試時說「我把映像縮小了」沒有說服力，說「從 1.15GB 降到 180MB、重建時間從 4 分鐘降到 10 秒」才有。
後面每一關的成果都要回來跟這張表比，所以先把起點記下來。

## 任務

在 `FoodTrucks/` 目錄下，用現在的 `Dockerfile`（`baseline` 標籤的狀態）量出四個數字，填進 [metrics.md](metrics.md) 的「基準」那一列。

| 要量的東西 | 指令 |
|---|---|
| 冷建置時間（無快取） | `Measure-Command { docker build --no-cache -t foodtrucks-web:baseline . }` |
| 熱重建時間（只改一行 `app.py` 後重建） | 在 `flask-app/app.py` 加一個空白行，再跑 `Measure-Command { docker build -t foodtrucks-web:baseline . }`，量完把空白行還原 |
| 映像大小 | `docker image ls foodtrucks-web:baseline` |
| 弱點數（Critical / High） | `docker scout quickview foodtrucks-web:baseline` |

再跑一次這個，把輸出看懂（不用記錄）：

```powershell
docker history foodtrucks-web:baseline
```

## 驗收

- [ ] `metrics.md` 的基準列四格都填了
- [ ] 能回答下面「想一想」的三題（寫在 `metrics.md` 下方的筆記區，一兩句話即可）

## 想一想

1. `docker history` 裡最大的三層各是哪一行 Dockerfile 指令造成的？
2. 你只改了 `app.py` 一行，為什麼 `npm install` 和 `pip3 install` 都重跑了？是哪一行指令讓快取失效？
3. 執行中的容器需要 Node.js 嗎？需要 `python3-dev`、`curl`、`gnupg` 嗎？

這三題的答案，就是關 1 和關 2 要解決的問題。

## 面試題

- Docker image 的 layer 是什麼？哪些 Dockerfile 指令會產生新的 layer？
- 建置快取是怎麼判斷某一層能不能重用的？`COPY`／`ADD` 和 `RUN` 的判斷方式有什麼不同？
- `ADD` 和 `COPY` 差在哪？為什麼一般建議用 `COPY`？
- 為什麼不建議把 `apt-get update` 和 `apt-get install` 寫成兩個 `RUN`？

## 完成後

把 `metrics.md` commit 起來，然後跟 Claude 說「關 0 完成」，我會看你的數字和三題筆記，再追問幾題。
