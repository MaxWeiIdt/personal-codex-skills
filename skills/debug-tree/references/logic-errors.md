# 邏輯錯誤

使用於程式未報錯，但輸出、畫面、資料庫或 GIS 結果不符合預期。

## 判斷流程

1. 先確認能否穩定重現。
2. 固定一組最小輸入資料。
3. 比對每個階段的中間結果。
4. 從資料來源開始查，不要只看 UI。

## 常見範圍

| 範圍 | 優先檢查 |
|---|---|
| 資料輸入正確但輸出錯誤 | 條件分支、排序、分組、四捨五入、時間窗。 |
| UI 顯示與後端不符 | API response、model binding、React state、Razor model。 |
| 資料庫資料不正確 | SQL `JOIN`、`WHERE`、transaction commit、重跑策略。 |
| GIS 空間結果異常 | SRID、投影轉換、geometry validity、空間單位。 |

## GIS 空間邏輯常見錯誤

- WGS84 `4326` 與 TWD97 `3826/3857` 混用。
- 在地理座標系用「度」做 buffer。
- Polygon 自我相交導致空間查詢失敗。
- 用 Envelope 取代 geometry 精準查詢造成範圍過大。
