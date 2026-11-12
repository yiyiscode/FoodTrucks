# RESUME：Docker 專案式學習進度

最後更新：2026-11-12

## 目標

補完 docker-curriculum.com 的 Modern Docker 章節，並能在資料／ML 工程師面試中回答 Docker 題。
做法：把這個 FoodTrucks 專案逐關改造成 modern 版，使用者動手，Claude 出任務卡、review、追問面試題。

## 目前進度

| 關 | 主題 | 任務卡 | 狀態 |
|---|---|---|---|
| 0 | 基準量測 | [labs/00-baseline.md](labs/00-baseline.md) | 待做 |
| 1 | Multi-stage build | [labs/01-multi-stage.md](labs/01-multi-stage.md) | 待做 |
| 2 | BuildKit cache mount、layer 順序 | 未寫 | |
| 3 | ML 服務容器化（scikit-learn） | 未寫 | |
| 4 | Healthcheck 與啟動順序 | 未寫 | |
| 5 | Compose Watch | 未寫 | |
| 6 | 容器安全（非 root、.dockerignore、Scout） | 未寫 | |
| 7 | 多平台與 CI/CD（GitHub Actions → GHCR） | 未寫 | |
| 8 | 模擬面試 | 未寫 | |
| 選修 | GPU 容器（GTX 1650 Ti） | 未寫 | |

成果數字記在 [labs/metrics.md](labs/metrics.md)。

## 下一步

1. 使用者做關 0，填 `labs/metrics.md`。
2. 使用者做關 1。
3. Claude review 關 1 後，寫關 2、關 3 的任務卡（關 3 需要 Claude 先提供 ML 服務的應用程式碼）。

## 關鍵決策

- 分支：`modern-docker`；`baseline` 標籤是改造前（第 3 章結束時）的狀態。
- 遠端：`origin` = yiyiscode/FoodTrucks（fork），`upstream` = prakhar1989/FoodTrucks。
- Claude 不預先寫解答，任務卡只給分層提示；使用者寫完才 review。
- 任務卡後續關卡是在前一關 review 後才寫，依實際卡關狀況調整。
- 底層原理（namespace、cgroup、overlayfs）只放在面試題，不做實作關。
- `opensearch-*.json`、`primary-container.json` 含 AWS 帳號 ID 與密碼，已加入 `.gitignore`，不可 commit。
