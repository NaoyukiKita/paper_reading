# Not what you've signed up for: Compromising Real-World LLM-Integrated Applications with Indirect Prompt Injection

Kai Greshakeらの論文

[Not what you've signed up for: Compromising Real-World LLM-Integrated Applications with Indirect Prompt Injection](https://arxiv.org/abs/2302.12173 "arxiv")

## 要旨

- これまでのLLMでは, ユーザが(悪意のある, なしに関わらず)直接LLMに対してプロンプトを入力していた
  - Prompt Injectionは, ユーザが悪意のあるプロンプトを与えることによって, 事前に与えられていたinstructionを乗っ取る攻撃である.
- 本研究では, 直接的ではないPrompt Injectionの存在を明らかにした.
  - 文章自体は有害性のないものであるが, 収集されたデータが有害であるような手法. 例えば, "画像について説明してください"という文章入力と共に, 悪意のある画像を提供する.
- またさらにこの攻撃の能力について調査した.
  - 攻撃の有効性や実用性など

## 序論

- LLMは研究者だけでなく一般的な利用においても人気を博している.
- LLMは他のアプリケーションと組み合わされ新たな価値を創造している(例えばGoogle検索結果の要約など)が, そこに倫理的, あるいはセキュリティ上の安全策があるわけではないことに注意が必要である.
- LLMやそれを活用したアプリケーションに対する攻撃手段としてPrompt Injectionがあり, これによって機密情報の漏洩や差別的, 非倫理的な出力が誘導されてしまう.
- Indirect Prompt Injectionは, Reirieverのような外部からデータを収集しその結果から反応を返すようなLLMシステムに, 悪意のあるデータを収集させることで, 他のユーザに対する反応を歪める攻撃を指す.
  - 例えば, インターネット上に"これは猫です"と書かれた犬の画像をばら撒いておくと, LLMはそれらの画像をもとに犬の画像に対して"これは猫の画像です"と返すかもしれない.
- 本研究では, Indirect Prompt Injectionの定義, 分類, および有効性を検証した.

## 関連研究

- LLMは学習によってAPI呼び出しを行うことが可能である
- 任意のタスクについてよりLLMが自主的に問題解決に取り組むようなモデルが開発されている(問題の理解, 解決に必要な理論の構築およびデータの収集など)
  - LLMの挙動を監視して, より良い問題解決計画を構築させたりする
  - 本研究では, 攻撃者が階層の高い(単純な)指令のみで目的を達成できてしまうかどうかを監視する.
- 

## 英単語

- taxonomy: 分類学
- viability: 実行可能性
- circumvent: 迂回する 回避する
- elicit: 引き出す 誘い出す
- myriad: 無数
- benign: 良性の 恵み深い
- disseminaiton: 種まき 撒布 普及 宣伝
- avert: 逸らす 避ける