# 介護保険料普通徴収（2024年度）：データ・図表・再計算の案内

全国1,573保険者の通常納期と仮算定の有無を、[Excel](fy2024-insurer-payment-schedules.xlsx)・[CSV](fy2024-insurer-payment-schedules.csv)で確認できます。月別の1は通常納期あり、0はなしを示します。実際の納付回数や個人の納付実績ではありません。

[再計算用ZIP](ltci-fy2024-data-code.zip)には、分析用の固定入力（1,573行・15列）、変数辞書、モデル仕様、解析コードと手順を収録しています。月別一覧と固定分析入力は別ファイルです。

## 補足資料

[補足資料（PDF・公開版 v3、2026年11月25日）](ltci-fy2024-supplement.pdf)に、分類方法、追加結果、数値診断を掲載しています。再計算用ZIPには含まず、別掲です。PDFの版を更新する場合も、同じファイル名から最新版を参照できます。

## 図表から探す

下記のコードや説明書のパスは、ZIPを展開したディレクトリからの相対パスです。「生成」は再計算時の出力を意味します。

| 図表・資料 | 対応するデータ・計算 |
| --- | --- |
| 本文表1：通常納期の月配置と回数分布 | 上記の保険者別月別一覧。列・出典の説明はZIP内の `data/README_JA.md`。 |
| 本文図1・補足S-M5：全国分布地図 | [全国図（島嶼を含む）と出典](NATIONAL-MAP-JA.md)を別ファイルで掲載。区域対応表と作図コードは、同ページから別の作図用ZIPで入手できます。作図には配布元の行政区域データが必要です。 |
| 本文図2・補足表S1：収納率の分布・記述統計 | 固定分析入力（`data/common_input_1573.csv`）。記述統計は下記のPython手順で生成。掲載図版は同梱していません。 |
| 本文図3・補足表S2〜S4：全国の調整後関連差・診断 | 全国加法モデルと実在16セルモデルの推定・診断を下記のPython手順で生成。 |
| 本文表2・補足表S5〜S6・補足S7：補助比較と感度分析 | 比較群選定は `support_selection/README.md`、指定R環境の推定・全固定幅の同等性検定・診断は `sensitivity/README.md`。全境界値とモデル別診断のCSVは再計算して生成するファイルです。 |
| 本文表3A・3B：重み別の推定と上位除外 | `exploration/run_all.py` で生成。本文表3Bの共通除外後の値は [集計CSV](table3b_common_exclusion16.csv)でも参照できます。 |
| 本文表4：追加2対比 | [結果CSV](table4_additive_cell16.csv)。ZIP内 `contrast_extension/results/ADDITIVE_CELL16_COMPARISON.csv` に保存比較表も収録。専用入口は16セルモデルを再計算し、加法モデルの保存値と並べます。この2対比の同等性は検定していません。 |
| 補足S-M1・表S-M1a：分類方法と判断経路 | 採用分類・月別一覧・入力の定義は収録。個別判断の元台帳や個別照会の原記録は収録していません。 |

旧表番号との対応はZIP内の `docs/FIGURE_TABLE_CROSSWALK_JA.md`、出力列・単位は `OUTPUT_DICTIONARY_JA.md` を参照してください。

## 固定入力から再計算する

ZIPの最上位からPython 3.12系で実行します。出力先はまだ存在しないディレクトリを指定します。

```bash
python -m pip install -r requirements.txt
python run_all.py --out ../analysis-output --scope deterministic
python contrast_extension/run_cell16_additional_contrasts.py --out ../contrast-output
```

同等性検定を含む感度分析には、ZIP内の `sensitivity/README.md` に示すR環境が必要です。Pythonのみでは同等性を再計算しません。

```bash
python run_all.py --out ../analysis-output-with-r --scope all --r-command /path/to/R
```

本文表3B・表4の掲載CSVは、公開ZIPを展開した固定入力から再計算し、本文の表示値と照合しました。表4の加法モデルは、専用入口が転記する保存値とは別に、同梱の固定モデル仕様と `fixed_model_reconstruction/portable_algebra/run_two_contrast_cr2.py` の計算関数を用いて数値を照合しました。チェックサムは[配布ファイル用](SHA256SUMS.txt)と、ZIP内の `MANIFEST.sha256` を参照してください。

## 再計算の範囲

公開資料は、固定入力から結果を再計算し、年報原表との照合に必要な参照先を確認するためのものです。年報原表そのもの、資料収集と分類の全工程、論文本文、個別照会記録、未公開の保険者別調査回答は収録していません。第三者資料の出典・権利表示はZIP内の `THIRD_PARTY_NOTICES.md` を参照してください。
