# 241-shichirin-sanma LEARNINGS

## 2026-09-16 コントロールパネル実装＋タイトル付与

- フォルダ名を `241-day065` から `241-shichirin-sanma` へ、エントリを `shichirin-sanma-battle.html` から `index.html` へ変更。
- **描画コストが致命的だった**。`antialias:true` ＋ `pixelRatio 2` で、ソフトウェア描画環境では 13 RAF/秒。`antialias:false` ＋ `pixelRatio 1.5` に変えるだけで 52 RAF/秒まで回復した（4倍）。235・237 が `antialias:false` を選んでいるのはこの理由。
- `shadowMap.enabled = true` だったが、**影を落とすライトが1つも無かった**。メッシュ側に `castShadow=true` を並べても、光源が `castShadow` でなければ影は1枚も出ない。無効化しても見た目は変わらない。
- **`setPointerCapture` が無く、うちわの長押しが指のズレで切れていた**。`pointerdown` で捕まえるだけで解決。共通方針が「61本しか入っていない・押しっぱなしバグの根因」と書いているとおりだった。
- ポーズは `dt=0` 方式にした。`now` を進めないので、残り時間だけでなく煙・炭の明滅・うちわの往復まで同時に止まる。状態を1箇所（時間の進み）で制御できる設計が効いた。
- 幕付きタイトル＋canvas の構成は harness のタップ判定で必ず FAIL する（台帳に2件記録あり）。今回は**幕を canvas より前へ移し、重なり順を z-index で明示**して解決した。DOM順に頼った重なりをやめれば、幕を前に置いても表示は崩れない。**偽陽性として見送らず実際に直せた3件目**。
- 検証: harness PASS ／ 操作盤の機能テスト **18/18 PASS**（ポーズで残り時間が止まる、Pキー・Escape、タブ非表示で自動ポーズ、リロード後もミュート保持、JSエラー0件）。
- 未検証: iPhone 実機の発熱・FPS。

## 2026-09-19 旧エントリURLの404を修復
- 症状: `https://titan11111.github.io/241-shichirin-sanma/shichirin-sanma-battle.html` が **404**。本体（`/241-shichirin-sanma/`）は 200 で生きていた
- 原因: エントリを `shichirin-sanma-battle.html` → `index.html` へ改名したため、**改名前に配ったリンクだけが死んだ**
- 対処: `shichirin-sanma-battle.html` を index.html へのリダイレクト専用ページとして復活（meta refresh ＋ `location.replace()`。`?query`・`#hash` も引き継ぐ）
- 検出元: `_tools/check-legacy-entry.sh`（245の同種事故を機に新設）。本番URLへcurlを撃って検出
- 鉄則8: エントリ名を変えたら旧名をリダイレクトで必ず残す（本体URLが200のままなので気づけない）
