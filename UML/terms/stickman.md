# Q1 UML入門では人型のアイコンであるスティックマンというものを使っています。近年の呼称の平等化の視点から用語を変えるとしたらどう呼びますか。UML2.5.1にはスティックマンという用語はみあたりません。英語では最近はどうよばれていますか。
A1 by chatGPT

はい。調べてみると、**UML 2.5.1で正式な概念名は「Actor（アクター）」であり、「stick man」は概念名ではなく、Actorを表現する図記号の説明**と考えるのが適切です。

OMGのUML 2.5.1仕様自体は公開されていますが、検索した範囲では「stick man」を独立したUML要素として定義していません。OMGの仕様関連資料では、Actorには複数の表記方法があり、棒人間型の図形はその一つです。([OMG][1])

### 現在の英語での呼び方

実際には次のような呼称が使われています。

| 英語                 | 日本語      | コメント                 |
| ------------------ | -------- | -------------------- |
| **Actor**          | アクター     | **UMLの正式な要素名。最も推奨**  |
| **Actor symbol**   | アクター記号   | 教材では使いやすい            |
| **Actor icon**     | アクターアイコン | GUI・ツール寄り            |
| **stick figure**   | 棒人間／人型図形 | 図形そのものを指す            |
| **stick man**      | 棒人間      | 従来からある呼称             |
| **human actor**    | 人間アクター   | 「人間であるActor」を明示したい場合 |
| **external actor** | 外部アクター   | システム外部の役割を強調する場合     |

例えばUML解説では現在でも **“stick man”** や **“stick figure”** は普通に使われています。UML-Diagrams.orgも「standard UML icon for actor is "stick man"」としています。([UML Diagrams][2])

一方で重要なのは、**Actor ≠ 人間**だということです。

UMLではActorは、

> 外部のエンティティがシステムと相互作用するときに果たす役割

です。人間だけでなく、**別のシステム、ハードウェアなど**もActorになれます。([UML Diagrams][2])

---

## 「平等化」の観点なら、私は「人型アイコン」と呼ぶのがよいと思います

日本語の入門教材なら、

> **スティックマン（棒人間）**

を

> **アクター記号（人型アイコン）**

あるいは単に

> **アクター記号**

に置き換えるのが自然です。

特に今回のご質問では、**「スティックマン」という名前そのものを男女平等・多様性の観点から変更する**より、

> **そもそも人を表しているのではなく、Actorという「役割」を表す記号である**

と教える方が、UMLの意味論にも忠実です。

---

### 例えば入門教材なら

従来：

> スティックマンは、システムを利用する人を表します。

よりも、

> **アクター（Actor）は、システムの外部からシステムと相互作用する役割を表します。図では人型のアクター記号で表すことがあります。**

とするのがよいでしょう。

すると、次の3種類を自然に説明できます。

```text
       人間
        ○
       /|\
       / \
      User
        │
        │
        ▼
   ┌───────────┐
   │   System  │
   │           │
   │ (Use Case)│
   └───────────┘
```

```text
      «actor»
   ┌──────────┐
   │  Other   │
   │  System  │
   └──────────┘
          │
          ▼
   ┌───────────┐
   │   System  │
   └───────────┘
```

つまり、

**人型アイコン = Actorの一つの表記方法**

であって、

**Actor = 人間**

ではありません。

実際、UML 2.5系ではActorを**クラス型の長方形＋`«actor»`**で表す方法や、対象に応じた**カスタムアイコン**も認められています。UML 2.5の仕様図18.6～18.8も、棒人間、クラス表記、アイコン表記の3種類を示しています。([No Magic Documentation][3])

### 私なら授業ではこうします

**正式用語：**

> **Actor（アクター）**

**図形の説明：**

> **アクター記号（人型アイコン）**

**補足として一度だけ：**

> 従来「stick man」と呼ばれることがありますが、UMLの要素名はActorです。

これなら、古い教科書との対応も維持しながら、**「人間を前提とした記号」という誤解も避けられます。**

そして、これは以前お話しされていた**AUTOSARや組込みシステムのUML教育**にもかなり重要です。例えば「ECU」「センサ」「他ECU」「診断装置」「外部クラウド」は人間ではありませんが、Use CaseのActorになり得ます。したがって「棒人間＝利用者」と教えてしまうより、**「Actor＝外部との相互作用を担う役割」**と教えた方が、後のシステム設計につながります。 ([UML Diagrams][2])

[1]: https://www.omg.org/spec/UML/About-UML/?utm_source=chatgpt.com "About the Unified Modeling Language Specification Version 2.5.1"
[2]: https://www.uml-diagrams.org/use-case-actor.html/1000?utm_source=chatgpt.com "UML actor is a role played by a human user of the designed system, some other system or hardware that interacts with the subject by using services of the subject."
[3]: https://docs.nomagic.com/download/attachments/136711173/UML%20specification.pdf?api=v2&modificationDate=1695226406576&version=1&utm_source=chatgpt.com "Date: May 2011"
