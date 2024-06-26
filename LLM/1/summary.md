# Editing Large Language Models: Problems, Methods, and Opportunities

Yunzhi Yaoらの論文

[Editing Large Language Models: Problems, Methods, and Opportunities](https://arxiv.org/abs/2305.13172 "arxiv")

## 要旨

- LLMの特定のドメイン内での動作を, 他のドメインでの性能に悪影響を与えずに変更する技術, LLMモデル編集(editing LLM)が流行り
- モデル編集に関連する問題点, 方法, 及び機会(?)について深く掘り下げることにする
- 特に, タスクの定義と関連した課題, そしてそれに対処する手法の実証分析を提供する
- 現状の手法における問題点を明らかにするためのベンチマークデータセットも作った

## 序論

- LLMのバグ(古い情報の提供も含む)を修正するのに学習のやり直しは面倒なので, 特定ドメイン内での動作のみ修正するモデル編集が提案されている
- 現状では新しいネットワークを作って繋げるか, パラメタを更新するかの二択で, 比較分析があんまりできてない
- 公平な比較のため, まず標準的な問題定義を確立し, また現在のものよりも包括的な評価手法を提案する

## 問題定義

編集前のモデルを$f_{\theta}$とする($\theta$はパラメタ). この時, $f_{\theta}(x_{e}) \neq y_{e}$と満たす入出力のペア($x_{e}, y_{e}$)(編集記述子, edit descriptor)について, $f_{\theta_{e}}(x_{e}) = y_{e}$を満たすパラメタ$\theta_{e}$が欲しい. ただし, 他のサンプルにおける動作に影響を与えてはならない.

$$
\begin{align}
f_{\theta_{e}}(x) = \left\{
    \begin{array}{cl}
    y_{e} & \mathrm{if} \hspace{1mm} x \in I(x_{e}, y_{e}) \\
    f_{\theta_{e}}(x) & \mathrm{if} \hspace{1mm} x \in O(x_{e}, y_{e})
    \end{array}
\right.
\end{align}
$$

ここで, $I(x_{e}, y_{e})$は編集範囲(editing scope)を示し, $(x_{e}, y_{e})$に関連した入出力ペアの集合で, $O(x_{e}, y_{e})$はそうでないものの集合.

編集後のモデル$f_{\theta_{e}}$は, 以下の3つの性質を満たす必要がある:

1. 信頼性(Reliability): $x'_{e}$が与えられた時, $y'_{e}$を返すか？

$$
\begin{align}
\mathbb{E}_{(x'_{e}, y'_{e}) \sim \{(x_{e}, y_{e})\}} \mathbb{1} \left\{ \mathrm{argmax}_{y} f_{\theta_{e}}(y | x'_{e}) = y'_{e} \right\}
\end{align}
$$

2. 汎化性能(Generalization): $(x_{e}, y_{e})$と等価な(言い換えただけ, など)入出力セット$(x"_{e}, y"_{e})$ではどうか？

$$
\begin{align}
\mathbb{E}_{(x"_{e}, y"_{e}) \sim N(x_{e}, y_{e})} \mathbb{1} \left\{ \mathrm{argmax}_{y} f_{\theta_{e}}(y | x"_{e}) = y"_{e} \right\}
\end{align}
$$

3. 局所性(Locality):  無関係な入出力セットは変わっていないか？

$$
\begin{align}
\mathbb{E}_{(x'_{e}, y'_{e}) \sim O(x_{e}, y_{e})} \mathbb{1} \left\{ f_{\theta_{e}}(y | x'_{e}) = f_{\theta}(y | x'_{e}) \right\}
\end{align}
$$

## 現在の手法

現在のLLMの編集手法は大きく分けて2つ.

1. モデルのパラメタを変更しない
    1. Memory Based：新しい事実を記憶しておいて, 与えられた入力が編集したドメイン内のものかを判断するモデルを付け足す(SERAC). あるいは, 文脈内学習を利用して元モデルへのプロンプティングを行う(IKE).
    2. Additional Parameters：学習可能なパラメタを追加する. 元のパラメタは固定のまま, 新しい事実を用いて学習を行う(T-Patcher, CaliNET).
2. モデルのパラメタを変更する
    1. Locate-Then-Edit：新しい事実に対処するパラメタを特定し, それを直接更新する(KN, ROME, MEMIT)
    2. Meta-learning：???(KE, MEND)

## 予備実験

- データセット：ZsREとCounterFact
- 編集元モデル：T5-XL(3B, エンコーダ-デコーダモデル)とGPT-J(6B, デコーダモデル)
- 評価する手法：SERAC, IKE, T-Patcher, CaliNET, KN, ROME, MEMIT, KE, MENDなど
- 結果：「これまでの評価指標では、モデル編集能力を十分に評価できない可能性がある」らしい。

## 包括的な評価指標の提案

- 可搬性(Portability, より頑健な汎化性能)：言い換え表現をより実践的なものにした
  - 主語の置き換え：関係の言い換えが多かったので、主語を同義語に置き換えるデータも増やした
  - 関係性の逆転：「富士山」と「日本一高い山」など、一対一の関係性を示した文の主語と述語を反転させる(?).
  - One-hop：仮に「日本一高い山は？」の答えを「富士山」から「天保山」に編集した場合、「日本一高い山はどこにある？」の答えを「静岡県と山梨県の県境」から「大阪府」に変更しなければならない

$$
\begin{align}
\mathbb{E}_{(x'_{e}, y'_{e}) \sim P(x_{e}, y_{e})} \mathbb{1} \left\{ \mathrm{argmax}_{y} f_{\theta_{e}}(y | x'_{e}) = y'_{e} \right\}
\end{align}
$$

- 局所性(Locality, より包括的な評価)：ZsREとCounterFactで異なる評価方法を統一した
  - Other Relations：更新された主題の他の属性は編集後も変更されないべきである(?).
  - Distract Neighbourhood：他の無関係な入力の前に編集対象の入力を連結すると、出力は編集事実に左右されるらしい. 良くないね.
  - Other Tasks：モデル編集が他のタスクの性能に悪影響を与える可能性がある.

- 効率性(Efficiency, 編集に要する時間とメモリ)

## 関連研究

- LLMの知識増強(knowledge augmentation)
- Lifelong Learning and Unlearning
- LLMのセキュリティとプライバシ
  - 有害な情報の削除

## 英単語

- impeccable 罪を犯さない 罪のない 欠点のない 申し分のない
- commendable ほめるに足る 立派な 感心な
- ramification 分枝 分岐 支流 (派生する)効果 結果
- sway 左右にゆする 左右する 支配する 統治する
- necessitate 必要とする 要する 必要とする 余儀なくさせる
- encompass 取り囲む 取り巻く 包囲する 包含する 含む もたらす
- paramount 最高の 主要な 最高権威を有する
