# 關 1：Multi-stage build

> 預計 1 小時。對應教材：Modern Docker → Multi-Stage Builds。

## 問題

現在的 `Dockerfile` 用一個 `ubuntu:22.04` 同時做三件事：

1. 用 Node.js 把 React 原始碼打包成 `static/build/main.js`
2. 安裝 Python 依賴
3. 執行 Flask

結果是執行時根本用不到的 Node.js、`node_modules`、編譯工具全都留在最終映像裡。

## 任務

改寫 `FoodTrucks/Dockerfile`，拆成兩個階段：

- **建置階段**：用 Node 映像跑 `npm install` 和 `npm run build`
- **執行階段**：用精簡的 Python 映像，只帶 Python 依賴、應用程式碼，以及從建置階段複製過來的打包結果

只改 `Dockerfile`，這一關先不要動 `docker-compose.yml` 和應用程式碼。

## 驗收

```powershell
docker build -t foodtrucks-web:lab1 .

# 1. 映像小於 250MB
docker image ls foodtrucks-web:lab1

# 2. 最終映像裡沒有 node（應該報錯找不到）
docker run --rm foodtrucks-web:lab1 which node

# 3. 打包結果有被複製進來（應該列出 main.js）
docker run --rm foodtrucks-web:lab1 ls static/build

# 4. 整個應用還能跑：開 http://localhost:5000 看得到地圖、搜尋有結果
docker compose up --build
```

- [ ] 四項都通過
- [ ] `metrics.md` 的「關 1」列填好
- [ ] commit，訊息說明你做了什麼、數字變化多少

## 會踩到的坑

驗收第 4 項用 compose 跑的時候，注意 `docker-compose.yml` 裡有這一行：

```yaml
volumes:
  - ./flask-app:/opt/flask-app
```

想一想：這個掛載對你映像裡的 `static/build/main.js` 會造成什麼影響？
如果你本機的 `flask-app/static/build/` 是空的，頁面會怎樣？（先觀察、想清楚原因就好，關 5 會正式處理它。）

## 提示

卡住超過 15 分鐘再打開，一次開一層。

<details>
<summary>提示 1：整體骨架</summary>

一個 Dockerfile 可以有多個 `FROM`，每個 `FROM` 開始一個新階段，可以用 `AS 名字` 命名。
後面的階段用 `COPY --from=名字 來源路徑 目的路徑` 把前面階段的檔案拿過來。
只有最後一個階段會成為最終映像。
</details>

<details>
<summary>提示 2：基底映像怎麼選</summary>

建置階段：`node:18-alpine`（這個專案用的 webpack 1 很舊，Node 版本先跟基準一致，不要升）。
執行階段：`python:3.12-slim`。如果 pip 安裝失敗，先看錯誤訊息是哪個套件，再決定要換 Python 版本還是調整套件版本。
</details>

<details>
<summary>提示 3：打包結果在哪</summary>

看 `flask-app/webpack.config.js` 的 `output.filename`：打包結果寫到工作目錄下的 `static/build/main.js`。
所以建置階段的工作目錄如果是 `/app`，要複製的就是 `/app/static/build`。
</details>

<details>
<summary>提示 4：建置階段需要哪些檔案</summary>

`npm install` 只需要 `package.json` 和 `package-lock.json`。
`npm run build` 另外需要 `webpack.config.js`、`.babelrc` 和 `static/src/`。
Python 的東西建置階段完全不需要。
</details>

## 面試題

- 什麼是 multi-stage build？它解決什麼問題？
- 沒有 multi-stage 之前，大家怎麼做到同樣的事？（關鍵字：builder pattern、兩個 Dockerfile 加一支 shell script）
- 映像變小除了省空間，還有哪些好處？（至少講出：拉取與部署速度、攻擊面、弱點掃描結果）
- `alpine`、`slim`、`distroless` 各是什麼？Python 專案用 `alpine` 可能會遇到什麼問題？（關鍵字：musl、wheel）
- `docker build --target` 是做什麼的？什麼情境會用到？
- **ML 追問**：如果某個 Python 套件安裝時需要 `gcc` 編譯，但執行時不需要，你會怎麼安排階段？

## 完成後

跟 Claude 說「關 1 完成」，我會 review 你的 Dockerfile，並從上面的面試題裡抽幾題請你口頭回答。
