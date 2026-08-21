# ⭐ Build@Mercari Step0 — 実装ガイド (午後)



午前おつかれさまでした！ 自分の `for` と `if` で 🍎 りんごキャッチが動いた感覚を手に入れた (もう少しで届きそうな) はず。
午後はそのベースを **自分色のゲームに育てていく** 時間です。このページは **午後 (自由開発)** 向けの実装ガイドです。

---

## 午後のゴール

**「自分のゲームと呼べる何かを 1 つ作って、最後に見せ合う」**

- ベースは午前のりんごキャッチで OK。**1 個でも追加機能が動けば、最後の発表で胸を張れます**
- 「全部やる」より「**1 個ちゃんと動く** + **自分らしさが乗ってる**」方が嬉しい仕上がり
- 詰まったらメンターに気軽に聞いてください

---

## 午後の流れ

| 時間 | やること |
| --- | --- |
| 14:00〜16:30 | 自由開発 (好きな課題を選んでチャレンジ) |
| 16:30〜17:00 | 成果発表 |

---

## 進め方のおすすめ

- **全部やる必要は無いです**。1 個できたら次、迷ったら 2 つ並行でも OK
- **小さい成功 → 大きい挑戦** の順で進めると気持ちが続きます
	- 例: Easy を 2〜3 個サクッと → Medium を 1 個じっくり → 余ったら Hard
- 自分の好きなテーマ (魚 + 網、桜 + 花瓶、推しキャラ + …) に変えるのも大歓迎

下に難易度別の課題を並べてあります。気になるところから手をつけてみてください。

---

## 🌱 Easy — 数字を変えるだけ (10〜20 分)

ファイルの中の **数字を 1〜2 個** 変えるだけ。すぐ結果が見えるので、午前で動いた感覚を確かめるウォームアップにぴったり。

| 課題 | どこをいじる？ | 何が変わる？ |
| --- | --- | --- |
| りんごを増やす | `setup()` の `for (let i = 0; i < 5; i++)` の `5` | 15 にすると一気に忙しい |
| りんごの色を変える | `drawApples()` の `fill(220, 40, 40)` (RGB) | 例: `fill(120, 80, 220)` で紫りんご |
| 落下スピード | `makeApple()` の `random(2, 4)` | `random(5, 10)` で激ムズ、`random(1, 2)` でゆっくり |
| かごの大きさ | `drawBasket()` の `rect(..., 80, ...)` の `80` | 120 にすると取りやすい |
| 空の色 | `drawBackground()` の `background(180, 220, 255)` | 朝焼け、夜空、宇宙… |

---

## 🌿 Medium — 新しい状態や条件を足す (20〜40 分)

新しいグローバル変数を 1 つ持たせたり、`if` で動きを分岐させる練習。

### 🌟 スター (5 点) を混ぜる

**何が変わる？** たまに金色のスターが落ちてきて、取ると 5 点入る。

考える順番:

1. `makeApple()` が返すオブジェクトに `kind` (種類) というプロパティを追加。20% くらいの確率で "star" にする
2. `drawApples()` で kind が "star" なら金色、それ以外は赤で描く
3. `catchApples()` でスコア加算するとき、kind を見て点数を切り替える

**👀 コードのヒント** — まず 5〜10 分は自分で考えてから、必要なものだけ開いてみよう！

<details>
<summary>kind を確率で付ける</summary>

```javascript
function makeApple() {
  return {
    x: random(40, width - 40),
    y: random(-600, 0),
    speed: random(2, 4),
    kind: random() < 0.2 ? "star" : "apple",
  };
}
```
</details>

<details>
<summary>描画で色を切り替える</summary>

```javascript
for (let i = 0; i < apples.length; i++) {
  if (apples[i].kind === "star") {
    fill(255, 215, 0);  // 金色
  } else {
    fill(220, 40, 40);  // 赤
  }
  ellipse(apples[i].x, apples[i].y, 28, 28);
}
```
</details>

<details>
<summary>スコア加算を kind で分岐</summary>

```javascript
if (apples[i].kind === "star") {
  score = score + 5;
} else {
  score = score + 1;
}
apples[i] = makeApple();
```
</details>

### 💔 ライフ制 (3 回落としたら終了)

**何が変わる？** 落としすぎるとゲーム終了。緊張感が出る。

考える順番:

1. グローバル変数 `life` を 3 で用意
2. `moveApples()` で「画面外に出たら作り直す」のところに、`life` を 1 減らす処理を足す
3. `life` が 0 以下になったら `noLoop()` でゲームを止める
4. `drawScore()` で life も画面に出す

**👀 コードのヒント**

<details>
<summary>グローバルに life を用意</summary>

ファイルの上の方、`let basketX;` などと同じ場所に書く:

```javascript
let life = 3;
```
</details>

<details>
<summary>画面外で life を減らす</summary>

`moveApples()` の `if (apples[i].y > height) { ... }` の中:

```javascript
if (apples[i].y > height) {
  life = life - 1;
  apples[i] = makeApple();
}
```
</details>

<details>
<summary>life が 0 以下でゲーム停止</summary>

`draw()` のどこかに足す:

```javascript
if (life <= 0) {
  noLoop();
}
```
</details>

<details>
<summary>life を画面に表示</summary>

`drawScore()` の中に:

```javascript
text("Life: " + life, 16, 70);
```
</details>

### ✨ キャッチで背景フラッシュ

**何が変わる？** 取った瞬間にパッと画面が明るくなって気持ちいい。

考える順番:

1. グローバル変数 `flash` を 0 で用意
2. `catchApples()` でスコア加算するとき、`flash` を 10 にセット
3. `drawBackground()` の中で、`flash > 0` なら明るい色 + `flash--`、そうでなければ元の色

**👀 コードのヒント**

<details>
<summary>グローバルに flash を用意</summary>

```javascript
let flash = 0;
```
</details>

<details>
<summary>キャッチ時にセット</summary>

```javascript
score = score + 1;
flash = 10;
apples[i] = makeApple();
```
</details>

<details>
<summary>背景描画で分岐</summary>

```javascript
function drawBackground() {
  if (flash > 0) {
    background(255, 250, 200);
    flash = flash - 1;
  } else {
    background(180, 220, 255);
  }
}
```
</details>

---

## 🌳 Hard — ゲームらしさを足す (40 分〜1 時間)

ここを 1 個でも完成させると、見せたときの「ゲームっぽさ」が一気に上がります。

### 🎬 タイトル画面

**何が変わる？** ゲームの「始まり」ができて、Click to Start で開始できる。

考える順番:

1. グローバル変数 `scene` を "title" で用意
2. `draw()` の最初で、`scene` が "title" なら drawTitle() を呼んで return (= ここで終わり)
3. `mousePressed()` で title のときにクリックされたら scene を "playing" に切り替える
4. `drawTitle()` という新しい関数で文字を描く

**👀 コードのヒント**

<details>
<summary>scene グローバル変数</summary>

```javascript
let scene = "title";
```
</details>

<details>
<summary>draw 冒頭で分岐</summary>

```javascript
function draw() {
  if (scene === "title") {
    drawTitle();
    return;
  }
  // 以下は元の draw のまま
  moveBasket();
  moveApples();
  catchApples();
  drawBackground();
  drawBasket();
  drawApples();
  drawScore();
}
```
</details>

<details>
<summary>クリックでスタート</summary>

新しい関数として追加:

```javascript
function mousePressed() {
  if (scene === "title") {
    scene = "playing";
  }
}
```
</details>

<details>
<summary>drawTitle() の例</summary>

```javascript
function drawTitle() {
  background(180, 220, 255);
  fill(50);
  textSize(36);
  text("🍎 Apple Catch", 70, height / 2 - 20);
  textSize(20);
  text("Click to Start", 120, height / 2 + 30);
}
```
</details>

### ⏱️ 30 秒の時間制限 + GameOver

**何が変わる？** 終わりがあるから、スコアを比べ合えるようになる。タイトル画面と組み合わせると一番映えます。

考える順番:

1. `frameCount` (毎フレーム +1 される p5 の変数) で残り秒数を計算
2. 0 になったら scene を "gameover" に切り替え
3. `drawGameOver()` で「Score: XX  Click to Restart」を表示
4. リスタート時に score と apples をリセット

**👀 コードのヒント**

<details>
<summary>残り秒数を計算</summary>

`draw()` の中で:

```javascript
let remain = 30 - floor(frameCount / 60);
```

(60 fps なので `frameCount / 60` が経過秒数)
</details>

<details>
<summary>0 で scene 切り替え</summary>

```javascript
if (remain <= 0) {
  scene = "gameover";
}
```

`draw()` の最初の分岐に gameover も足す:

```javascript
if (scene === "gameover") {
  drawGameOver();
  return;
}
```
</details>

<details>
<summary>drawGameOver() の例</summary>

```javascript
function drawGameOver() {
  background(40, 50, 70);
  fill(255);
  textSize(36);
  text("Game Over", 100, height / 2 - 30);
  textSize(24);
  text("Score: " + score, 130, height / 2 + 10);
  textSize(18);
  text("Click to Restart", 110, height / 2 + 50);
}
```
</details>

<details>
<summary>リスタート処理</summary>

`mousePressed()` を拡張:

```javascript
function mousePressed() {
  if (scene === "title") {
    scene = "playing";
  } else if (scene === "gameover") {
    score = 0;
    apples = [];
    for (let i = 0; i < 5; i++) {
      apples.push(makeApple());
    }
    scene = "playing";
  }
}
```

(注: 残り秒数を `frameCount` ベースで計算してると、リスタートしても 0 のままになります。グローバルに `let timeLeft = 30 * 60;` を持って毎フレーム `timeLeft--`、リスタート時に `timeLeft = 30 * 60;` に戻す方が素直)
</details>

### ⌨️ キーボードでも動かす

**何が変わる？** マウスとキーボード両方で操作できる。好みで選べる。

考える順番:

1. p5.js には `keyIsDown(LEFT_ARROW)` という関数がある (キーが押されてる間だけ true)
2. `moveBasket()` の中で、左キーが押されてたら `basketX` を減らす、右キーなら増やす

**👀 コードのヒント**

<details>
<summary>キーで basketX を動かす</summary>

```javascript
function moveBasket() {
  basketX = mouseX;
  if (keyIsDown(LEFT_ARROW)) {
    basketX = basketX - 6;
  }
  if (keyIsDown(RIGHT_ARROW)) {
    basketX = basketX + 6;
  }
}
```

(マウスの行を消せばキーボード専用にもできる)
</details>

### 🏆 ハイスコアを保存する

**何が変わる？** リロードしてもベストスコアが残る。何度も挑戦したくなる。

考える順番:

1. グローバル変数 `highScore` を 0 で用意
2. `setup()` で `localStorage` から前回のベストを読む
3. ゲーム終了時に、現在のスコアがベストを超えてたら保存
4. `drawScore()` でベストも表示

**👀 コードのヒント**

<details>
<summary>setup でベストを読む</summary>

```javascript
let highScore = 0;

function setup() {
  createCanvas(400, 600);
  basketX = width / 2;
  highScore = Number(localStorage.getItem("appleHighScore") || 0);
  for (let i = 0; i < 5; i++) {
    apples.push(makeApple());
  }
}
```
</details>

<details>
<summary>ベスト更新時に保存</summary>

ゲーム終了時 (時間切れ や `life <= 0` のところ) に:

```javascript
if (score > highScore) {
  highScore = score;
  localStorage.setItem("appleHighScore", String(score));
}
```
</details>

<details>
<summary>ベストを画面に表示</summary>

```javascript
text("Best: " + highScore, 16, 70);
```
</details>

---

## 🌟 Bonus — 自分色に作り変える

完全に自分のオリジナルにする系。メンターと一緒に進めると早いです。

- **🔊 効果音をつける** — p5.sound ライブラリの追加が必要。メンターに「p5.sound 使いたい」と相談
- **🖼️ 画像で見た目を変える** — `preload()` で `loadImage()` し、`drawApples()` の `ellipse(...)` を `image(img, x, y, w, h)` に置き換え
- **🎨 自分のテーマに変える** — 魚 + 網、風船 + ピン、雪だるま + 雪、桜 + 花瓶、推しキャラ + … 何でも OK。絵文字 (`text("🐟", x, y)`) を使うのも楽
- **👯 2 人プレイ** — `basketX2` を追加して A/D キーで動かす 2 つ目のかご

---

## 詰まったとき

「動かない！」となったら、まず赤いエラーメッセージを確認してみましょう。

### よく出るエラー

| エラー文 | だいたいの原因 |
| --- | --- |
| `SyntaxError: Unexpected token` | 括弧 `{ } ( )` の数が合ってない、`;` の付け忘れ |
| `XXX is not defined` | 変数名のタイポ、`let` の付け忘れ |
| `Cannot read property 'y' of undefined` | 配列の範囲を超えた (`for` の条件が `<=` になってる など) |
| 画面が白いまま | `setup` `draw` `mousePressed` のスペルミス (大文字小文字含む) |

### 元に戻したくなったら

**こまめにセーブ** しておくと安心。履歴から復元できます。

### それでもダメなときは

遠慮せずメンターに聞いてみましょう！

> メンターに聞くときのコツ: 「何をしたら」「どうなった」「どう動いてほしかった」を順に話すと、原因にたどり着きやすいです。
> 例: 「スターを追加したら、画面が真っ暗になった。本当は金色のスターが落ちてほしかった」

---

## 16:30〜 成果発表

何グループか合同で、作品を見せ合います。

- **コードの解説より、何を作ったか・どこが気に入ってるか** を話してもらえると場が盛り上がります
- 「**ここで詰まったけどメンターと一緒に乗り越えた**」みたいな話も大歓迎
- 完成度より、「自分の手で動かした感覚」が伝わるのが一番大事

---

午後も楽しんでいってください 🎉
