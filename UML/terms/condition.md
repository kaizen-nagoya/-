# Q1 UMLの教科書に事後条件という用語がありました。事前条件と、普遍条件とともに、システム設計の基本的な考え方と具体的な記述言語を３つ以上、ソースコード例を示してください。
A1 by chatgpt

はい。ここでいう「事後条件」は、UMLそのものの図記法というより、**UMLモデルに対してOCL（Object Constraint Language）などで付加する制約**として理解すると整理しやすいです。OMGのOCL仕様でも、操作に対する `pre` / `post` と、モデル全体の整合性を表す `inv` が明確に定義されています。([OMG][1])

そして「普遍条件」という言葉は少し注意が必要です。ソフトウェア設計では通常 **不変条件（invariant）** と呼びます。「常に成立していなければならない条件」という意味です。

---

# 1. まず3つを一枚で理解する

例えば銀行口座の `withdraw(amount)` を考えます。

```text
             withdraw(amount)
                    │
        ┌───────────┴───────────┐
        │                       │
    事前条件                  事後条件
   Precondition             Postcondition
        │                       │
 amount > 0               balance' =
 amount <= balance        balance - amount
        │                       │
        └──────────┬────────────┘
                   │
             不変条件
              Invariant
                   │
             balance >= 0
```

意味は非常に単純です。

### 事前条件

> **この操作を実行してよい条件**

```text
amount > 0
amount <= balance
```

### 事後条件

> **事前条件を満たして実行したなら、実行後に成立すべき条件**

```text
balance_after = balance_before - amount
```

### 不変条件

> **システム／オブジェクトが正常な状態にある限り、常に成立していなければならない条件**

```text
balance >= 0
```

EiffelのDesign by Contractでは、事前条件は呼び出し側の義務、事後条件は呼び出された側の保証、不変条件はクラス全体に適用される条件、と整理されています。([eiffel.org][2])

---

# 2. これは「Design by Contract」という考え方

この3つをまとめた代表的な考え方が **Design by Contract（DbC）** です。

```text
             Caller                  Supplier
                │                       │
                │   Precondition        │
                │──────────────────────>│
                │                       │
                │      operation        │
                │──────────────────────>│
                │                       │
                │   Postcondition       │
                │<──────────────────────│
                │                       │
```

つまり、

> **「何を入力として受け入れるか」
> 「何を保証するか」
> 「そもそもどんな状態を正常とするか」**

を、プログラムの設計段階で明示する方法です。

Eiffelの公式資料でも、契約によってソフトウェアコンポーネントの抽象的性質を明示し、実行時チェックやドキュメントとして利用できると説明されています。([eiffel.org][3])

これは、以前お話しされていた**「要求事項よりも制約を明確にする」**という考え方ともかなり近いものがあります。

---

# 3. 具体的な記述言語① UML + OCL

UMLとの関係で最も直接的なのが **OCL** です。

OMGのOCL仕様には、まさに

```text
context C::op(...)
    pre: ...
    post: ...
```

という記法があります。また `inv` によって不変条件を記述できます。([OMG][1])

例えば、

```text
class BankAccount
-----------------
balance : Integer

withdraw(amount : Integer)
```

に対して、

```ocl
context BankAccount::withdraw(amount : Integer)

pre:
    amount > 0

pre:
    amount <= balance

post:
    balance = balance@pre - amount

inv:
    balance >= 0
```

と書けます。

ここで重要なのが、

```ocl
balance@pre
```

です。

これは**操作実行前の値**を表します。

したがって、

```ocl
balance = balance@pre - amount
```

は、

> 「withdraw実行後の残高は、実行前の残高からamountを引いた値でなければならない」

という非常に明確な仕様になります。

これは単なるコメント

```c
/* withdrawするとbalanceが減る */
```

よりもずっと強い仕様です。

---

# 4. OCLで「普遍条件」を書く

例えば口座を複数管理する `Bank` があって、

```text
Bank
 ├── Account
 ├── Account
 ├── Account
 └── ...
```

すべての口座の残高が0以上でなければならない、とします。

OCLなら、

```ocl
context Bank

inv:
    accounts->forAll(a | a.balance >= 0)
```

と書けます。

`forAll` がポイントです。

つまり、

> **すべてのAccountについてbalance >= 0**

という意味です。

ここから「普遍条件」という言葉との関係も見えてきます。

---

# 5. ② Eiffel

この考え方を**プログラミング言語そのものに組み込んだ代表例**が Eiffel です。

例えば、

```eiffel
class
    BANK_ACCOUNT

feature

    balance: INTEGER

    withdraw (amount: INTEGER)
        require
            positive_amount: amount > 0
            sufficient_balance: amount <= balance
        do
            balance := balance - amount
        ensure
            balance_updated:
                balance = old balance - amount
        end

invariant
    non_negative_balance:
        balance >= 0

end
```

非常に分かりやすいですね。

```eiffel
require
```

が事前条件、

```eiffel
ensure
```

が事後条件、

```eiffel
invariant
```

が不変条件です。

そして、

```eiffel
old balance
```

が実行前の `balance`。

Eiffel公式資料にもこの `require`、`ensure`、`invariant` の関係が明記されています。([eiffel.org][2])

---

# 6. ③ Ada / SPARK

組込み・安全系の観点では、**Ada/SPARK** が非常に興味深いです。

例えばAdaでは、

```ada
procedure Withdraw
  (Account : in out Bank_Account;
   Amount  : in Integer)
with
  Pre  => Amount > 0
          and then Amount <= Account.Balance,
  Post => Account.Balance =
          Account.Balance'Old - Amount;
```

という契約を書くことができます。

ここでも、

```ada
Pre  =>
```

が事前条件、

```ada
Post =>
```

が事後条件、

```ada
'Old
```

が実行前の値です。

さらに型・状態に対する制約を組み合わせることで、組込みシステムや安全性が要求されるソフトウェアで、仕様と実装の関係をより厳密に扱えます。

SPARK/Adaでは、このような契約を形式検証と組み合わせることができるのが大きな特徴です。

---

# 7. ④ Dafny

もう一つ非常に分かりやすい言語が **Dafny** です。

例えば、

```dafny
method Withdraw(balance: int, amount: int)
    returns (newBalance: int)
    requires amount > 0
    requires amount <= balance
    ensures newBalance == balance - amount
    ensures newBalance >= 0
{
    newBalance := balance - amount;
}
```

ここでは、

```dafny
requires
```

が事前条件、

```dafny
ensures
```

が事後条件です。

さらにループについては、

```dafny
invariant
```

を書くことができます。

例えば、

```dafny
method Sum(a: array<int>) returns (s: int)
    ensures s >= 0
{
    var i := 0;
    s := 0;

    while i < a.Length
        invariant 0 <= i <= a.Length
    {
        s := s + a[i];
        i := i + 1;
    }
}
```

のように、

> **ループの途中でも常に成立する条件**

を指定できます。

これはシステム設計上かなり重要な発想です。

---

# 8. 「不変条件」は3種類に分けて考えると分かりやすい

実際のシステム設計では、

```text
不変条件
│
├── オブジェクト不変条件
│     balance >= 0
│
├── システム不変条件
│     全センサのIDは一意
│
└── ループ不変条件
      0 <= i <= N
```

と考えると理解しやすいです。

例えば自動車の制御ソフトなら、

```text
vehicle_speed >= 0
```

だけでなく、

```text
brake_pressure >= 0
```

や、

```text
wheel_speed >= 0
```

なども不変条件候補になります。

さらに、

```text
車速 = 0
```

なのに、

```text
ギヤ = D
```

だからといって必ずしも異常とは限りません。

したがって、**不変条件は物理法則そのものではなく、「システムが正常であるために常に成立させるべき制約」**として設計する必要があります。

---

# 9. さらに重要なのが「事前条件 → 事後条件」の数学的関係

プログラム検証では、

```text
       P
       │
       ▼
     Program
       │
       ▼
       Q
```

として、

```text
{ P } Program { Q }
```

と書くことがあります。

これは **Hoare logic** の基本形です。

例えば、

```text
{ x >= 0 }

x := x + 1

{ x >= 1 }
```

です。

つまり、

> 事前条件 `P` が成立していてプログラムを正しく実行すれば、事後条件 `Q` が成立する

という考え方です。

これはUML/OCLだけの特殊な考え方ではありません。

---

# 10. したがって、3つの概念を比較すると

| 概念                         | 意味             | 典型的記述                          |
| -------------------------- | -------------- | ------------------------------ |
| **事前条件** Precondition      | 実行前に満たすべき条件    | `requires` / `pre` / `require` |
| **事後条件** Postcondition     | 正常終了後に保証される条件  | `ensures` / `post` / `ensure`  |
| **不変条件** Invariant         | 正常状態なら常に成立する条件 | `invariant` / `inv`            |
| **ループ不変条件** Loop invariant | ループ中ずっと成立する条件  | `invariant`                    |

対応関係は、

| モデル／言語          | 事前         | 事後        | 不変             |
| --------------- | ---------- | --------- | -------------- |
| **UML + OCL**   | `pre`      | `post`    | `inv`          |
| **Eiffel**      | `require`  | `ensure`  | `invariant`    |
| **Ada/SPARK**   | `Pre`      | `Post`    | `Invariant` 等  |
| **Dafny**       | `requires` | `ensures` | `invariant`    |
| **Hoare Logic** | `P`        | `Q`       | loop invariant |

という対応になります。UML/OCLについてはOMGの仕様が一次資料になります。([OMG][1])

---

# 11. サーボモータに適用すると非常に面白い

先ほどのサーボモータの話につなげると、例えば `MoveTo(position)` という操作を考えられます。

### 事前条件

```text
target_position is within permitted range
servo_enabled = true
emergency_stop = false
```

### 事後条件

```text
|actual_position - target_position| <= tolerance
```

### 不変条件

```text
motor_current <= maximum_current
temperature <= maximum_temperature
emergency_stop = true → motor_torque = 0
```

OCL風に書けば、

```ocl
context ServoMotor::moveTo(target : Real)

pre:
    target >= minPosition

pre:
    target <= maxPosition

pre:
    enabled = true

post:
    abs(position - target) <= positionTolerance

inv:
    abs(current) <= maximumCurrent

inv:
    temperature <= maximumTemperature

inv:
    emergencyStop implies torque = 0
```

となります。

ここまでくると、**UMLのクラス図・状態機械図・シーケンス図と、形式的な制約条件がつながってきます。**

---

# 12. そして生成AIとの組み合わせがかなり面白い

先ほどの話ともつながります。

例えば、

```text
要求仕様
   ↓
生成AI
   ↓
UML
   ↓
OCL
   ↓
C/C++コード
   ↓
テスト
   ↓
結果
   ↓
AIによる改良
```

という流れを作れます。

そして各段階で、

```text
Precondition
Postcondition
Invariant
```

を**消さずに保存する**。

例えばAIが、

```text
pre: speed >= 0
```

を

```text
pre: speed >= 0 && speed <= MAX_SPEED
```

に変更したら、

> なぜAIは制約を追加したのか？

を記録できます。

逆に人間が、

```text
invariant:
    emergencyStop implies torque = 0
```

を追加した場合、

> **人間がAIにはなかった安全上の制約を意図的に追加した**

ということが明確になります。

これは、先ほどのご提案だった**「生成AIの自律的改良と、人間の意図的改良を競わせる」**という実験を、かなり明確な形で実施できます。

特に **UML → OCL → C/C++ → テスト**という流れなら、単なる生成AIの文章比較ではなく、**「制約がどのように変化し、それが実行結果にどう影響したか」**まで追跡できます。

### 参考にする一次資料

* [OMG Object Constraint Language (OCL) 2.4](https://www.omg.org/spec/OCL/2.4/PDF?utm_source=chatgpt.com) — UMLモデルの `pre`、`post`、`inv` の正式仕様。([OMG][4])
* [OMG OCL 2.2 Specification](https://www.omg.org/spec/OCL/2.2/PDF?utm_source=chatgpt.com) — pre/post/invariantの形式的な定義。([OMG][1])
* [Eiffel: Design by Contract and Assertions](https://www.eiffel.org/doc/eiffelstudio/I2E-_Design_by_Contract_and_Assertions?utm_source=chatgpt.com) — `require`、`ensure`、`invariant` の考え方。([eiffel.org][5])
* [Eiffel: Design by Contract, Assertions and Exceptions](https://www.eiffel.org/doc/eiffel/ET-_Design_by_Contract_%28tm%29%2C_Assertions_and_Exceptions?utm_source=chatgpt.com) — 契約、事前条件、事後条件、不変条件の詳細。([eiffel.org][2])

**特に教育教材としては、UMLのクラス図 → OCLの `pre/post/inv` → Eiffel/Dafnyの実装 → C/C++のテスト、という4段階にすると、「UMLが単なる絵ではなく、実行可能な仕様へ近づいていく」ということを学生に実感してもらえると思います。**

[1]: https://www.omg.org/spec/OCL/2.2/PDF?utm_source=chatgpt.com "OCL2.book"
[2]: https://www.eiffel.org/doc/eiffel/ET-_Design_by_Contract_%28tm%29%2C_Assertions_and_Exceptions?utm_source=chatgpt.com "ET: Design by Contract (tm), Assertions and Exceptions"
[3]: https://www.eiffel.org/doc/eiffel/ET-_General_Properties?utm_source=chatgpt.com "ET: General Properties"
[4]: https://www.omg.org/spec/OCL/2.4/PDF?utm_source=chatgpt.com "OCL.book"
[5]: https://www.eiffel.org/doc/eiffelstudio/I2E-_Design_by_Contract_and_Assertions?utm_source=chatgpt.com "I2E: Design by Contract and Assertions"

# Q2 CSPで具体例を８つ、論文URLとともにお示しください。
A2 

はい。CSP（Communicating Sequential Processes）は、**「複数のプロセスが独立に動き、通信イベントで同期する」**という考え方なので、UMLの状態遷移図・シーケンス図や、組込み・通信・安全系との相性がかなり良いです。

特に、事前条件・事後条件・不変条件の話につなげるなら、CSPではさらに **「プロセス間の通信について、何が起こってよいか／起こってはいけないか」**を形式化できます。

以下、実際の研究論文・論文集に対応した具体例を8種類挙げます。

| # | 具体例                    | CSPで何を記述・検証するか              | 代表的な論文                                                      |
| - | ---------------------- | --------------------------- | ----------------------------------------------------------- |
| 1 | **通信プロトコル X.25/LAPB**  | 送受信順序、ACK、再送、プロトコル状態        | X.25をCSP/OCCAMで仕様化・実装 ([サイエンスダイレクト][1])                     |
| 2 | **フォールトトレラント・システム**    | 故障、冗長系、復旧、故障時の安全性           | CSPによる耐故障ソフトウェア設計・検証 ([サイエンスダイレクト][2])                      |
| 3 | **分散ジョブショップ・スケジューリング** | 複数工程・機械の並行実行と資源競合           | Cspconsによる分散制約充足・Job-shop ([サイエンスダイレクト][3])                 |
| 4 | **リアルタイム通信ネットワーク**     | 同期、通信タイミング、ACK、遅延           | CSPによる通信プロトコルとリアルタイム処理 ([サイエンスダイレクト][4])                    |
| 5 | **ハードウェア／FPGA**        | 並列回路、Handshake、通信回路         | CSP/occamとTransputer、Handshake Components ([シュプリンガーリンク][5]) |
| 6 | **組込みマルチプロセッサ**        | CPU間通信、共有メモリ競合の回避           | Java CMP上でCSP通信をハードウェア支援 ([サイエンスダイレクト][6])                  |
| 7 | **セキュリティプロトコル**        | 認証、秘密情報、攻撃者との通信、情報漏洩        | CSPによるSecurity Protocol Verification ([シュプリンガーリンク][5])      |
| 8 | **デッドロックのない並行システム**    | プロセス間待ち合わせ、deadlock freedom | CSPのdeadlock-free性検証 ([Wiley Online Library][7])            |

### 1. 通信プロトコル

これはCSPの非常に典型的な用途です。

例えば、

```text
Sender  ── DATA ──> Receiver
Sender  <── ACK ─── Receiver
```

を、

```csp
SENDER =
    send.data ->
    receive.ack ->
    SENDER
```

のようなプロセスとして表現できます。

さらにReceiverを、

```csp
RECEIVER =
    receive.data ->
    send.ack ->
    RECEIVER
```

とします。

すると、

```csp
SYSTEM = SENDER || RECEIVER
```

として、**送信側と受信側が同時に動くシステム**を表現できます。

実際にX.25 LAPBをCSPで仕様化し、OCCAMで実装した研究があります。これは通信プロトコルをCSPで扱う実例として非常に分かりやすいです。([サイエンスダイレクト][1])

[On the specification and implementation of X.25 using CSP and OCCAM](https://doi.org/10.1016/0165-6074%2891%2990430-2?utm_source=chatgpt.com)

---

### 2. フォールトトレラント・システム

これは、**安全工学との接続が非常に面白い例**です。

例えば、

```text
Sensor A ──┐
           ├── Voter ── Controller
Sensor B ──┤
Sensor C ──┘
```

という3重化システムを考えます。

CSPでは、

```text
SENSOR_A
SENSOR_B
SENSOR_C
VOTER
CONTROLLER
```

をそれぞれ独立したプロセスとして扱えます。

そして、

```text
正常
 ↓
Sensor A failure
 ↓
Voter detects failure
 ↓
B,Cによる制御継続
```

という**故障を含む振る舞い**を検証できます。

実際に、CSPとmodel checkingを使って耐故障ソフトウェアを設計・検証した論文があります。マイクロプロセッサを使った装置を例に、通常設計、耐故障設計、ソフトウェア設計故障への耐性、dual-processor構成まで扱っています。([サイエンスダイレクト][2])

[Formal verification of fault-tolerant software design: the CSP approach](https://doi.org/10.1016/j.micpro.2004.07.005?utm_source=chatgpt.com)

これは、以前お話しされていた**「安全工学では、規則を満たしただけでなく、危険な状態に対してどう振る舞うかを見る」**という考え方ともかなり近いです。

---

### 3. 工場のジョブショップ・スケジューリング

例えば、

```text
Job A: Machine 1 → Machine 2 → Machine 3
Job B: Machine 2 → Machine 3 → Machine 1
Job C: Machine 3 → Machine 1 → Machine 2
```

という製造工程を考えます。

各機械を

```text
M1
M2
M3
```

というCSPプロセスにし、各Jobを別プロセスにできます。

重要なのは、

> **同じ機械を2つのJobが同時に使用してはいけない**

という制約です。

これは組込みシステムで言えば、

```text
CPU
CAN
SPI
DMA
Memory
ADC
```

などの共有資源競合にも対応する考え方です。

実際にCSPをベースにしたCspconsで、**distributed job-shop scheduling**を実装した研究があります。([サイエンスダイレクト][3])

[Communicating sequential processes for distributed constraint satisfaction](https://doi.org/10.1016/j.ins.2004.11.007?utm_source=chatgpt.com)

---

### 4. リアルタイム通信

CSPを単なる「並列プログラム」ではなく、**時間を含む通信システム**として利用する例です。

例えば、

```text
ECU A                    ECU B
 |                         |
 |------ CAN DATA -------->|
 |                         |
 |<----- ACK --------------|
 |                         |
```

について、

```text
DATA送信
 ↓
ACK待ち
 ↓
timeout
 ↓
再送
```

という時間的振る舞いをモデル化できます。

CSPを通信プロトコルに適用し、同期・リアルタイム処理を扱った研究があります。([サイエンスダイレクト][4])

[On communicating sequential processes](https://doi.org/10.1016/0169-7552%2894%2900016-M?utm_source=chatgpt.com)

これは**AUTOSAR、CAN、Ethernet、車載ECU間通信**をCSPで考える際の基本モデルとして応用できます。

---

### 5. ハードウェア／FPGA

CSPはソフトウェアだけではありません。

CSPの、

```text
Process A
    |
  channel
    |
Process B
```

という構造は、そのまま

```text
回路A
 │
Handshake
 │
回路B
```

というハードウェア構造にも対応できます。

CSPの25周年記念論文集には、

* CSP, occam and Transputers
* Models for Data-Flow Sequential Processes
* Implementation of Handshake Components

など、**CSP → 並列ハードウェア**に関する研究がまとまっています。([シュプリンガーリンク][5])

[Communicating Sequential Processes: The First 25 Years](https://doi.org/10.1007/b136154?utm_source=chatgpt.com)

さらに近年では、CSP的な並行プロセスモデルからFPGA/RTLを生成する研究もあります。([arXiv][8])

[Rule-based High-level Hardware-RTL Synthesis ... based on Concurrent Communicating Sequential Processes](https://arxiv.org/abs/2302.02959?utm_source=chatgpt.com)

これは以前お話しした**「UML状態遷移図 → Verilog HDL」**というテーマともかなり直接的につながります。

---

### 6. 組込みマルチプロセッサ

共有メモリ方式では、

```text
CPU1 ──┐
       ├── Shared Memory
CPU2 ──┤
       │
CPU3 ──┘
```

という構成になります。

一方CSPでは、

```text
CPU1 ── Channel ── CPU2
```

のように**メッセージ通信**を基本にできます。

この考え方をJavaの組込みチップマルチプロセッサに適用し、オンチップ通信チャネルをring-based NoCとして実装する研究があります。([サイエンスダイレクト][6])

[Hardware support for CSP on a Java chip multiprocessor](https://doi.org/10.1016/j.micpro.2012.08.004?utm_source=chatgpt.com)

組込みOSやAUTOSARを考える場合、

```text
Task A
  ↓
Message
  ↓
Task B
```

という構造をCSPとして抽象化できます。

---

### 7. セキュリティプロトコル

CSPは**通信そのものだけでなく、通信してはいけない相手との相互作用**も扱えます。

例えば、

```text
Client → Server : login
Server → Client : challenge
Client → Server : response
Server → Client : accept
```

という認証プロトコルをモデル化します。

さらに攻撃者

```text
Attacker
   ↕
Client / Server
```

をプロセスとしてモデルに加えます。

すると、

> 攻撃者が通信を傍受・再送しても秘密情報が漏れないか

などを形式検証できます。

CSPの25周年記念論文集にも、**“Verifying Security Protocols: An Application of CSP”**という章があります。([シュプリンガーリンク][5])

これは車載セキュリティ、AUTOSAR Security、IDSなどにも考え方を展開しやすい領域です。

---

### 8. デッドロック検証

CSPが特に得意なのがこれです。

例えば、

```text
Process A        Process B

lock(A)          lock(B)
   ↓                ↓
lock(B)          lock(A)
```

となったら、

```text
A → Bを待つ
B → Aを待つ
```

ので永久に進みません。

CSPではプロセス間通信を明示的にモデル化するため、

```text
P || Q
```

のような並列システムについて、

**deadlockが存在するか**

を形式的に調べられます。

名古屋大学の研究者による1987年の論文にも、CSPのdeadlock-free性を検証するための推論システムが報告されています。([Wiley Online Library][7])

[Verification system for freedom from deadlock of communicating sequential processes](https://doi.org/10.1002/scj.4690180401?utm_source=chatgpt.com)

---

## 8例を一つの体系として見ると

面白いのは、この8例が単なるバラバラの応用ではないことです。

```text
                    CSP
                     │
          ┌──────────┼──────────┐
          ↓          ↓          ↓
       Process     Channel    Parallel
          │          │          │
          └──────────┼──────────┘
                     ↓
              システムの振舞い
                     │
       ┌─────────────┼──────────────┐
       ↓             ↓              ↓
   通信プロトコル   組込み制御      ハードウェア
       ↓             ↓              ↓
     X.25          ECU/RTOS        FPGA
       │             │              │
       └─────────────┼──────────────┘
                     ↓
                形式検証
              /     |      \
          Deadlock  Failure  Security
```

そして、先ほどの**事前条件・事後条件・不変条件**との対応を考えると、かなりきれいです。

| DbC              | CSPで対応する考え方                           |   |    |
| ---------------- | ------------------------------------- | - | -- |
| 事前条件 `Pre`       | プロセスがイベントを受け付ける条件                     |   |    |
| 事後条件 `Post`      | イベント後に成立すべき状態・トレース                    |   |    |
| 不変条件 `Invariant` | 常に守られるシステム性質                          |   |    |
| 契約               | プロセス間の通信インターフェース                      |   |    |
| 例外・故障            | failure / divergence / abnormal event |   |    |
| 並行実行             | `P                                    |   | Q` |
| 通信               | `channel` / event                     |   |    |
| 検証               | refinement、deadlock、trace等            |   |    |

特に重要なのは、**CSPでは「値」だけでなく「イベントの順序・通信・並行性」を仕様にできる**ことです。

そのため、ユーザーが取り組まれている

**UML状態遷移図 → CSP → 形式検証 → C/C++ → テスト**

という教材構成はかなり自然です。

なお、CSPの原典はHoareの1978年論文です。ここで「入力・出力を基本プリミティブとし、通信する逐次プロセスの並列合成をプログラム構造化の基本とする」という考え方が示されています。([CiNii][9])

[C. A. R. Hoare, “Communicating Sequential Processes” (1978)](https://doi.org/10.1145/359576.359585?utm_source=chatgpt.com)

**特に次に面白いのは、同じ小さな制御システムを「UML状態遷移図 → OCLの事前・事後・不変条件 → CSP → C/C++」の4種類で書き比べること**です。これをやると、それぞれの形式手法が何を表現でき、何が表現しにくいのかが非常にはっきりします。

[1]: https://www.sciencedirect.com/science/article/pii/0165607491904302?utm_source=chatgpt.com "On the specification and implementation of X.25 using CSP and OCCAM - ScienceDirect"
[2]: https://www.sciencedirect.com/science/article/abs/pii/S0141933104001036?utm_source=chatgpt.com "Formal verification of fault-tolerant software design: the CSP approach - ScienceDirect"
[3]: https://www.sciencedirect.com/science/article/abs/pii/S0020025504003421?utm_source=chatgpt.com "Communicating sequential processes for distributed constraint satisfaction - ScienceDirect"
[4]: https://www.sciencedirect.com/science/article/abs/pii/016975529400016M?utm_source=chatgpt.com "On communicating sequential processes - ScienceDirect"
[5]: https://link.springer.com/book/10.1007/b136154?page=1&utm_source=chatgpt.com "Communicating Sequential Processes. The First 25 Years: Symposium on the Occasion of 25 Years of CSP, London, UK, July 7-8, 2004. Revised Invited Papers | Springer Nature Link"
[6]: https://www.sciencedirect.com/science/article/abs/pii/S0141933112001585?utm_source=chatgpt.com "Hardware support for CSP on a Java chip multiprocessor - ScienceDirect"
[7]: https://onlinelibrary.wiley.com/doi/10.1002/scj.4690180401?utm_source=chatgpt.com "Verification system for freedom from deadlock of communicating sequential processes - Murakami - 1987 - Systems and Computers in Japan - Wiley Online Library"
[8]: https://arxiv.org/abs/2302.02959?utm_source=chatgpt.com "Rule-based High-level Hardware-RTL Synthesis of Algorithms, Virtualizing Machines, and Communication Protocols with FPGAs based on Concurrent Communicating Sequential Processes and the ConPro Synthesis Framework"
[9]: https://cir.nii.ac.jp/crid/1362544420358829952?utm_source=chatgpt.com "Communicating sequential processes | CiNii Research"

