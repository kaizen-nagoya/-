# Q1 UMLの状態遷移図、シーケンス図、タイミング図から、ソースコード生成する事例を３つづつ紹介してください。
A1 by chatgpt

はい。ここは「UMLからコード生成」という観点ではかなり面白いところです。

ただし、**3種類の図からのコード生成のしやすさには大きな差**があります。

| UML図       | コード生成との相性 | 主に生成できるもの                      |
| ---------- | --------- | ------------------------------ |
| **状態遷移図**  | ◎         | 状態機械、イベント処理、`switch`、Stateパターン |
| **シーケンス図** | ○         | メソッド呼出し、通信処理、テストコード            |
| **タイミング図** | △〜○       | 周期処理、タイマ設定、スケジューラ、テスト          |

以下、**実際にコード生成へ使われている代表例を3つずつ**挙げます。

---

# 1. 状態遷移図 → ソースコード

### ① MATLAB/Simulink Stateflow → C/C++

![Image](https://images.openai.com/static-rsc-4/k8PagBbn5JGPXpLy5hAqyfjmnwgdujDpzk4CHhz1czTVa32NyEGrVWNmMBGq-aocCmQxJm-oWh46M9i3UebEZEI5dAubq7Eh7_bN9LXimcfbVLyMIFQTGgez9RGGWrhpPSXhlMFlDN2ubOOYmr6J9c9weHT-GA6BVl4EUCbHSxpAtIcIvRkgDIRPFJo8Kn6B?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/JVFfEnrG2JNcXt7-0KzYSO_1ItKZlwnCqqo94xPVaPjifXxYjhjRBlp27jRBzZDD7dayd7mD8fe-2shChyX-XeG06GnMvNbHUU8uiF2V6GyDX2W-TzGI9yNxCyKEGXwsd7sFi-A2tMiJ0SYeZ2hCXtzyk7oU9SLr7-MzMBp-7W3CjY77xWIyqRRo58A2sYsg?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/0TisNEsA5-vsecKzlGxuScgFr_AqQPLpGVGEzMz8c8fzILMSP8ov7xzoGPUt2NaQJgW6iVBEXoy5B0T3Do-RsyV_P237yn6bkWS66-nzdL-86yt1JLJoVJgETXJMZHu9pvZdwMQS3su7-Lxqf6bUjmTm_KDtUf3H9RItVJAKss_R1LypYmqMDcI0SOG379Ll?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/Z_spwNBCrFkZR3bV1qMZ3F_OUHzWiTRNkJfmbl6JyOeZLaXM_FEyP4EBBBSItTjKwWhdlXIh6BvKgDzFKxURH7cUKHWQLRaYwcFDurywIktgIz1T8h2bwd-7TE-gndhllRodAzwwDDHGuOskYzjEfkIBJYmFKG7ABKYKfeVWNuIIhpt3FKObZtgzVqJrG9Df?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/6_25yKnsK18_gkhmh-CWjCfejrsdaIeo_Ns2qmXS-xKv9n9Yem0q_z4u29-RdKKH3IxoA-4YOEEvWmi3EQ5t49JJb_dcXzOnVyDfXK-XqOZTiGlqUsQ-RxLbQQxe_K9_5IncLhRg28Qi7oUAUg0n4KFaeQC93Bnb1FgDzwvcibSxTL8ZX8chvHOi14XR9kXi?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/tPQ44HvQ46zb3H1YFzc8iEIaot9JUGEgdUV4eEkW5aS5CdCYqXgRQRDzuighmUZrUhdxs4Zukv-QZf0dYhWl7aPFW66q1WgwUTgMVChF931H59h-DRyif_oVl_6u8IzRIvDS0TRHc0Rk41q2WCTtVbKrOi2TIJpG5IbMbrey2DmwT0Ulk0Y7jmGBG50fSGqs?purpose=fullsize)

Stateflowでは状態機械をグラフィカルに記述し、**Simulink Coder / Embedded Coder**を使ってC/C++コードを生成できます。

例えば、

```text
       start
         │
         ▼
     ┌───────┐
     │ STOP  │
     └───┬───┘
         │ start
         ▼
     ┌───────┐
     │ RUN   │
     └───┬───┘
         │ stop
         ▼
       STOP
```

から概念的には、

```c
switch (state) {
case STOP:
    if (start)
        state = RUN;
    break;

case RUN:
    if (stop)
        state = STOP;
    break;
}
```

のようなコードを生成できます。

**組込み制御では非常に典型的な利用方法**です。

---

### ② IBM Rhapsody → C/C++/Java

![Image](https://images.openai.com/static-rsc-4/RdiZ7r0JhdVByEBgzrtLsDLQQV-CMsOImH5oLfv2hugW3hnrkAsHSXMKhrPhP8KTTePvL4WIwx7QhzNzVMbNUqRFm5LdjEkEuPVsCZ5EWXlCWZL4hMxUe0_VrKQlHbjU0e-n6iNAupvTbSjBg37-Dk5FLAqJ4cykU-DPv7bwzVmIJ7Fqnvs7PtaAi2g0_7RY?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/oQWcmX-UXj6-7rEU0UFKKJ1nClKOa6-Sr9z7s9zj3FSz2uc3zzUo7Kd4_IcmMuGQq2N1Cz8D0uYpBMT73jpxfk7LWGA4q282vWfjl7LCecKkmoulsJ2wejAZU1YvWzHqG5NMrqLnjzbuupdWgT0v9HNrsGKB2DQ265UcBMg94TGLJU6_DIEOFMQohOtgrWzl?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/8MUL2_X948EkPhnNfGtWaNymX71Qali79d3UlD2mSFyJcKILjVUAo731jhwGblrDFIQKpoZAHMfH15NeQe7UO_ZpSlAThwH9dWsGUwINYDMsmTOmOSG3ELOvW5KGvNEPDGSFUXH1A_EwtAyAJi6D4ZDIqhEK05eW6VrYIcD9BbdXATW0MH5Nv-KERJfDYbpO?purpose=fullsize)

IBM Engineering Systems Design Rhapsodyでは、UML/SysMLモデルから実装コードを生成できます。

状態図：

```text
Idle
 │
 │ start
 ▼
Running
 │
 │ error
 ▼
Error
```

から、状態、イベント、遷移、アクションを実装したC++等を生成する方式です。

特徴は、

> **状態遷移図を「設計図」ではなく、実行可能なモデルとして扱う**

ところです。

---

### ③ Yakindu / itemis CREATE → C/C++/Java

![Image](https://images.openai.com/static-rsc-4/zpa_lnvi1atfcqK1XjGE2ODE2mg4QZkyl4-aNWaCGu-_Ef0JiQIAhG8V8ow2nMIpQPBPNAbWUnap_OQEKr7liBA4KADWM6xfTnt0d75d8qdR7WbWv783kDPO8pADFXTk_tOU9t2cbTIoYh2NJ5lFsS-J9KPpepoLD5ykGnMlyLceTiDG1JsN4E6MzkAQv1Oi?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/l5z15OnB4hHq4rjr967eRAuTshtXh65wEva14iq6MCJcXFAi84VkiWAabY0SvM-VwLvRsMy3K-jA8BCROJqvV8ElKTV9s268RfQzCkT2-m55RPQ6P-UrNba84X32hUx4e8sDkvluqXN9OJbFnVEuKRfZid4yNCw7AYJcg8H_IDNwnJoFDE1Y_DJhNTs1RZW2?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/4NOo4fLHinhH2dmc9p4sg5k3Negh2DhGKn9KBJ_SJSYLw-iVyuQLoCtl7ixSrHPzmHoQBEecRICsGmCSf1StgjwZsI1YQnkNYmWmiF3t7yhfhLlcPCAwZSuIolty4I3dPU9fv-wLFbArq7Zay8LI_XQgtT5yNeU_3BytDnMyMMIUYO-YgB5M7Z0UdEd4gVJF?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/2kTFJdsr5R_S86kvTYmpLe3K32Jnq8QryjDQ0RHJpXEO3-nu_eJLxRwJ0GKCtvTQLHzIjzO2r6GeMlhJMrB2StOrh5Oa9QCvufLwsz9kM0HrnPwY-33U0cRZOlSgZqB9HquPasn30Zk-cQP_CMotaBpTLxQxw5i-15iKH-p1FEpswOY-IQA1Z_FB8G7CeZ_P?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/unVNroJ416V8qoW3QuegGgJCIeRcU7rVtkwFNVXuYnlTeuKfSsrRZZTG_RYb4260hgOOztLp2cOExrEaCXr1c46JzxMmb5CdEXdme-fb6iZzDS1a50EHPicgDlct61aQf6_eC9pzDj7MHpSLQU3qi7cgjau69kzjJGLbTWylFIebbpIiw9-s42O9MQz9E5sV?purpose=fullsize)

旧Yakindu Statechart Tools、現在のitemis CREATE系では、状態機械からC/C++、Javaなどのコードを生成できます。

例えば、

```text
OFF
 │ on
 ▼
ON
 │ off
 ▼
OFF
```

というモデルから、状態を管理するコードを生成します。

これは**状態遷移 → コード生成**を比較的素直に体験できる教材にも向いています。

---

# 2. シーケンス図 → ソースコード

ここは状態遷移図より少し事情が違います。

シーケンス図は、

```text
A       B       C
│       │       │
│──────>│       │
│       │──────>│
│       │<──────│
│<──────│       │
```

という**相互作用のシナリオ**を表すので、コード生成では「クラスそのもの」より、

**メソッド呼出し・通信・テスト**

の生成に向いています。

---

### ① IBM Rhapsody → Sequence Diagram → コード

Rhapsodyでは、シーケンス図をモデルの相互作用として利用し、オブジェクト間の操作・メッセージをモデル化できます。

例えば、

```text
Controller       Motor
     │             │
     │ setSpeed()  │
     │────────────>│
     │             │
     │<────────────│
```

なら、

```cpp
controller.setSpeed();
```

のような操作呼出しとの対応を作れます。

ただし、

> **シーケンス図だけから完全なプログラムを生成する**

というより、**クラス・操作・状態機械などと組み合わせて生成する**のが普通です。

---

### ② Enterprise Architect → Sequence Diagram → コード

![Image](https://images.openai.com/static-rsc-4/8P7ZA-9iMi-1MjXAyIVCTRmo4MhT-twd67CT6FplMg3T-c2BRHE_7X9rr-vlSXOCDkK2s1LvZDSvzFyW6OGi5ghSfvxoxFKGdWds0o39LfemHNDrRA_4VYrT2HAfOdfQ5VzDeOJOIUxooysmO5HhqXs7HA8NbH3clRnGCcWW4fcK6ovCoX5SSc4c3gBweBUH?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/Wt3683p65P7LDQ_Y5G3n5MnfuU1KjajBZBdIy6TETZ8M0OR07NXrQBokkOrZuFPvsKVwR8x9cuCn7-d86lr3t4gC7WD8v58GEUC3Fl8cZ51mFK3NB_Ahr9vWmzgZMhloinEVwW2W1Qevcp7Wxn3-N1rmuT0ttHBsN8ZF8wzny_nfOFSOZo9BG5O7GxdqThAS?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/YcDbxhvAp8qgBnYDEpt1IUy55dFublu-Nd6wwPlKNB7sc0qHCKV8TqaSj5cWsxymrzS3WL10dPv22WCQu7El9xKMx-PQh-1O3_47tagAktnJufAqL4UnkFEYzhJAhMZ8HOPnurwsXhHR3EPCYnZAV8vmbF18XOYhI2hhdHwZSmYVFFHU-rVI_gO6XaPk6q92?purpose=fullsize)

Sparx SystemsのEnterprise Architectでは、UMLモデルから各種ソースコードを生成できます。

シーケンス図では、

```text
User
 │
 │ request()
 ▼
Controller
 │
 │ execute()
 ▼
Service
 │
 │ save()
 ▼
Database
```

のような**メッセージの流れ**を設計し、クラス図などと組み合わせて実装につなげます。

特に、

* 操作
* 引数
* 戻り値
* メッセージ
* オブジェクト

をモデル上で定義しておくことが重要です。

---

### ③ UMLシーケンス図 → テストコード生成

これは非常に重要な使い方です。

例えば、

```text
Client        Server
  │             │
  │ request()   │
  │────────────>│
  │             │
  │<────────────│ response()
```

というシーケンスから、

```cpp
TEST(ServerTest, RequestResponse)
{
    client.request();

    auto response = server.response();

    ASSERT_TRUE(response.valid());
}
```

のような**テストシナリオ**を生成する方式です。

実務では、こちらのほうが「シーケンス図 → ソースコード」の意味が明確です。

つまり、

> **シーケンス図 → 本番コード**

だけでなく、

> **シーケンス図 → テストコード**

という方向があります。

---

# 3. タイミング図 → ソースコード

これは少し特殊です。

UMLタイミング図は、

```text
time →
       0   10   20   30   40 ms

Sensor  ┌──┐    ┌──┐    ┌──┐
        │  │    │  │    │  │

CPU     ────┌───────┐────────
            │       │

Motor   ──────────┌────┐─────
                   │    │
```

のように**時間に沿った状態変化**を表します。

したがって、

**タイマ、周期処理、Deadline、サンプリング**

などとの相性が良いです。

---

### ① MATLAB/Simulink → 周期タスク等のCコード

![Image](https://images.openai.com/static-rsc-4/tPQ44HvQ46zb3H1YFzc8iEIaot9JUGEgdUV4eEkW5aS5CdCYqXgRQRDzuighmUZrUhdxs4Zukv-QZf0dYhWl7aPFW66q1WgwUTgMVChF931H59h-DRyif_oVl_6u8IzRIvDS0TRHc0Rk41q2WCTtVbKrOi2TIJpG5IbMbrey2DmwT0Ulk0Y7jmGBG50fSGqs?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/TwDKIBfBzGtXx8g3nl00ScrvBOcyUi1X383NzryuUkVHYAImSje5fy--V6lMKmxzPkEJhp2GEiaF7YaYucIUW_HEv9KYgGR-l4oJnaJdP6vudTeCvz-aOeGyx8Wbgg_6XqCswGY5nSk7L7Zk1AtaswQqT72GLWe8CDiVfluEBKTMA5W3_yrT1Z7KrU5zopY3?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/M7UJ11sBz88CGo6ae1qR0XRWtCzsD5zN7nmkrhst_7O_ue7_hIz8lsi4FCQO0seVKIxncYzziRDYxjgIf5S08lLWISmQw_AEXhcpa3W1swGlT4xyrcDrhbUx--P-8iJibqkFmsOCm9kFYsC1tG1nL6X8P8TOccJTvcsZzhU9mfsciF0DSBcw-97nA9MwoX7E?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/ZwXKRNIXxs-qSuZcMEMbm4qkZdqGJdVkjGuft7x6T-tBKSb-j-YEZLuugIqVBWhVvdQAc1Xs1TS1CsxmykTDnrJPZtFouVafvuExp3Elik8HM5XRcyz5I2NUTKC5qJRRsOah25fT2iUynPn9wBx6K2dsARKWrirgpRbwIsAtF5_FLRPwsnF40kH-9sxfYB82?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/Qt2qredvzQ9Z4_OCUKH1qo9o09tAPCS5Z-azy6N9AVPdCcZqwvdHEwcYYvwCIlTU0mz1j-Y0W6nezGAKMbN1rkzySj8fTlI0sacMkBcZ_KfjNrq41CJ79gzNG6qdgzZ_IgExVeqgEy7Cp8giM4X4LgnmOgtHlH6gytz_U0F_gSNdsTHI0l3H4LJGSJIHGxIz?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/UR5sv1H_GzRBfZ3xDNQpkeS3cco_kcbd_mTcmEIKikCXr0_QfVBil1jQAVGVdXK8Btn7AmB_S6cpUA38X6uwiwuIE4VYDmqJ0zEpfBhmdoOAe7ypKiGdMMjn8uNX3AkNu61OwtNxFe7WR4VRcmEN5vTYLHq7MYca-kJbjdXcFKgeZz51YzLTXpcYD6y9mE0L?purpose=fullsize)

Simulinkでは、

```text
Sensor
  │
  │ 10 ms
  ▼
Controller
  │
  │ 10 ms
  ▼
Actuator
```

のようなサンプル時間をモデルに設定し、それを実行スケジュールに反映してCコードを生成できます。

例えば、

```c
void step(void)
{
    read_sensor();
    control();
    write_actuator();
}
```

を10ms周期で実行する構成になります。

ここでは**UMLタイミング図そのものから直接Cを生成する**というより、

> タイミングを持ったモデル → 実行スケジュール → Cコード

という考え方です。

---

### ② AUTOSAR / EAST-ADL系モデル → Runnable / RTE

これはユーザーの専門分野にかなり近い例です。

例えば、

```text
time →

Sensor       |----10ms----|----10ms----|
              ↓             ↓
Controller   [ Runnable ]  [ Runnable ]
              │
              ↓
Motor         [Output]
```

という周期実行をモデル化すると、

```c
void Controller_Runnable(void)
{
    Sensor_Read();
    Controller_Calculate();
    Motor_Write();
}
```

のようなRunnable実装につなげられます。

さらにAUTOSARでは、

```text
Runnable
   │
   ├── TimingEvent
   │       │
   │       └── 10 ms
   │
   └── RTE
```

という形で**時間イベントとRunnableの関係**をモデル化できます。

ただし、ここも厳密には**UMLタイミング図単独からAUTOSARコードを生成する**というより、AUTOSARの実行モデルとコード生成環境を組み合わせたものです。

---

### ③ UMLタイミングモデル → SystemVerilog/HDL・検証コード

研究・ツール開発寄りの例として、

```text
Clock ──┐_┐_┐_┐_┐_┐_┐_

Input ─────┐______┌──────

Output ─────────┐______┌─
```

のような時間制約をモデル化し、

```systemverilog
always_ff @(posedge clk) begin
    ...
end
```

などのHDL実装や、

```systemverilog
assert property (...);
```

のような**時間的性質の検証コード**につなげる方法があります。

これは特に、

* FPGA
* ASIC
* ECU
* 通信プロトコル
* リアルタイム制御

との相性が良いです。

---

# 4. 3種類を並べると違いがよく分かります

私は、組込み教育なら次のように説明するのが一番分かりやすいと思います。

```text
        UMLモデル
           │
     ┌─────┼─────┐
     │     │     │
     ▼     ▼     ▼
   State Sequence Timing
   Machine Diagram Diagram
     │     │     │
     ▼     ▼     ▼
   状態    通信    時間
   制御    手順    制約
     │     │     │
     ▼     ▼     ▼
   ┌─────────────────┐
   │   Source Code   │
   └─────────────────┘
```

そしてコード生成の対応を考えると、

```text
状態遷移図
   ↓
state / event / guard / action
   ↓
switch / State Pattern / FSM
```

```text
シーケンス図
   ↓
message / operation / interaction
   ↓
method call / communication / test
```

```text
タイミング図
   ↓
period / deadline / timeout / latency
   ↓
timer / scheduler / periodic task / assertion
```

となります。

---

## 5. ここが「UML 3.0」にかなり重要です

先ほど議論したUML 3.0の構想につなげるなら、私はむしろ、

> **3種類の図を独立した絵として扱わず、同じ実行モデルの3つのProjection（投影）として扱う**

のが面白いと思います。

例えば、

```text
              Executable Model
                    │
        ┌───────────┼───────────┐
        ↓           ↓           ↓
     State       Sequence      Timing
     Model        Model        Model
        │           │           │
        └───────────┼───────────┘
                    ↓
             Formal Model
          Petri Net / CSP / ...
                    ↓
             Source / Test
                    ↓
             Runtime Trace
                    │
                    └────→ Model improvement
```

とすれば、

**状態遷移図＝何が起こるか**
**シーケンス図＝誰と誰がどう相互作用するか**
**タイミング図＝いつ起こるか**

を同じモデルから表現できます。

これはユーザーが以前検討されていた **「UML → CSP →形式検証 → C/C++ → ECU」** という流れとも非常にきれいにつながります。

特に次の一歩としては、**「状態遷移図＋シーケンス図＋タイミング図の3つを入力として、同じCプログラムを生成する具体例」**を1つ作ると、UML 3.0の原案をかなり具体化できます。


# Q2 具体的にC、python, rustのソースコードを生成してください。
A2

はい。前回の「状態遷移図・シーケンス図・タイミング図を共通の実行モデルから生成する」という考え方を、**実際に C / Python / Rust の3言語へ落とす**ところまで具体化できます。

まず小さな制御系を例にします。

### 例：モータ制御器

状態：

```text
          start
   OFF ------------> RUN
    ^                 |
    |                 | stop
    |                 v
    +-------------- FAULT
          error
```

もう少し正確には、

```text
OFF --start--> RUN
RUN --stop--> OFF
RUN --error--> FAULT
FAULT --reset--> OFF
```

このモデルから、

* **状態遷移図** → 状態機械
* **シーケンス図** → Controller と Motor の呼び出し
* **タイミング図** → 10 ms周期で制御
* **ソースコード** → C / Python / Rust

という関係を作ります。

---

## 1. C の生成例

組込み系を意識すると、もっとも直接的なのは `enum + switch` です。

```c
#include <stdio.h>

typedef enum {
    STATE_OFF,
    STATE_RUN,
    STATE_FAULT
} State;

typedef enum {
    EVENT_START,
    EVENT_STOP,
    EVENT_ERROR,
    EVENT_RESET
} Event;

typedef struct {
    State state;
} MotorController;

void motor_on(void)
{
    printf("Motor ON\n");
}

void motor_off(void)
{
    printf("Motor OFF\n");
}

void motor_fault(void)
{
    printf("Motor FAULT\n");
}

void controller_init(MotorController *ctrl)
{
    ctrl->state = STATE_OFF;
}

void controller_dispatch(MotorController *ctrl, Event event)
{
    switch (ctrl->state) {

    case STATE_OFF:
        if (event == EVENT_START) {
            motor_on();
            ctrl->state = STATE_RUN;
        }
        break;

    case STATE_RUN:
        if (event == EVENT_STOP) {
            motor_off();
            ctrl->state = STATE_OFF;
        }
        else if (event == EVENT_ERROR) {
            motor_fault();
            ctrl->state = STATE_FAULT;
        }
        break;

    case STATE_FAULT:
        if (event == EVENT_RESET) {
            motor_off();
            ctrl->state = STATE_OFF;
        }
        break;
    }
}

int main(void)
{
    MotorController ctrl;

    controller_init(&ctrl);

    controller_dispatch(&ctrl, EVENT_START);
    controller_dispatch(&ctrl, EVENT_STOP);
    controller_dispatch(&ctrl, EVENT_START);
    controller_dispatch(&ctrl, EVENT_ERROR);
    controller_dispatch(&ctrl, EVENT_RESET);

    return 0;
}
```

ここでは、

```text
STATE_OFF
STATE_RUN
STATE_FAULT
```

が状態遷移図から、

```text
EVENT_START
EVENT_STOP
EVENT_ERROR
EVENT_RESET
```

がイベントから、

```c
switch (ctrl->state)
```

が状態遷移の実装として生成できます。

これは**状態遷移図 → C**として非常に分かりやすい例です。

---

# 2. Python

同じモデルを Python で生成すると、例えば次のようになります。

```python
from enum import Enum, auto


class State(Enum):
    OFF = auto()
    RUN = auto()
    FAULT = auto()


class Event(Enum):
    START = auto()
    STOP = auto()
    ERROR = auto()
    RESET = auto()


class MotorController:

    def __init__(self):
        self.state = State.OFF

    def motor_on(self):
        print("Motor ON")

    def motor_off(self):
        print("Motor OFF")

    def motor_fault(self):
        print("Motor FAULT")

    def dispatch(self, event):

        if self.state == State.OFF:

            if event == Event.START:
                self.motor_on()
                self.state = State.RUN

        elif self.state == State.RUN:

            if event == Event.STOP:
                self.motor_off()
                self.state = State.OFF

            elif event == Event.ERROR:
                self.motor_fault()
                self.state = State.FAULT

        elif self.state == State.FAULT:

            if event == Event.RESET:
                self.motor_off()
                self.state = State.OFF


if __name__ == "__main__":

    controller = MotorController()

    controller.dispatch(Event.START)
    controller.dispatch(Event.STOP)
    controller.dispatch(Event.START)
    controller.dispatch(Event.ERROR)
    controller.dispatch(Event.RESET)
```

Cとの大きな違いはありますが、**モデルそのものは同じ**です。

つまり、

```text
                 UMLモデル
                    │
           ┌────────┴────────┐
           ↓                 ↓
           C              Python
```

と考えられます。

---

# 3. Rust

Rustなら、状態を `enum` としてかなり自然に表現できます。

```rust
#[derive(Debug)]
enum State {
    Off,
    Run,
    Fault,
}

enum Event {
    Start,
    Stop,
    Error,
    Reset,
}

struct MotorController {
    state: State,
}

impl MotorController {

    fn new() -> Self {
        Self {
            state: State::Off,
        }
    }

    fn motor_on(&self) {
        println!("Motor ON");
    }

    fn motor_off(&self) {
        println!("Motor OFF");
    }

    fn motor_fault(&self) {
        println!("Motor FAULT");
    }

    fn dispatch(&mut self, event: Event) {

        match self.state {

            State::Off => {
                if let Event::Start = event {
                    self.motor_on();
                    self.state = State::Run;
                }
            }

            State::Run => {
                match event {
                    Event::Stop => {
                        self.motor_off();
                        self.state = State::Off;
                    }

                    Event::Error => {
                        self.motor_fault();
                        self.state = State::Fault;
                    }

                    _ => {}
                }
            }

            State::Fault => {
                if let Event::Reset = event {
                    self.motor_off();
                    self.state = State::Off;
                }
            }
        }
    }
}

fn main() {

    let mut controller = MotorController::new();

    controller.dispatch(Event::Start);
    controller.dispatch(Event::Stop);
    controller.dispatch(Event::Start);
    controller.dispatch(Event::Error);
    controller.dispatch(Event::Reset);
}
```

Rustの場合、

```text
UML State
    ↓
Rust enum
```

という対応が非常にきれいです。

---

# 4. さらに「シーケンス図」をコードに反映する

ここが今回のテーマでは重要です。

例えばシーケンス図を、

```text
User        Controller       Motor
 |               |             |
 | start()       |             |
 |-------------->|             |
 |               | motor_on()  |
 |               |------------>|
 |               |             |
 |               |<------------|
 |<--------------|             |
```

とします。

これならモデル上では、

```text
User
  |
  | start
  v
Controller
  |
  | motor_on
  v
Motor
```

という**メッセージ送信**になります。

Cなら、

```c
void user_start(MotorController *ctrl)
{
    controller_dispatch(ctrl, EVENT_START);
}
```

そして Controller が、

```c
motor_on();
```

を呼びます。

Pythonなら、

```python
controller.dispatch(Event.START)
```

Rustなら、

```rust
controller.dispatch(Event::Start);
```

となります。

つまり、

**シーケンス図は「誰が誰に何を送るか」を生成する**

と考えることができます。

---

# 5. タイミング図まで入れる

例えば10 ms周期の制御なら、

```text
time       0    10    20    30    40 ms
           |     |     |     |     |
Controller *-----*-----*-----*-----*
           |     |     |     |     |
           ↓     ↓     ↓     ↓     ↓
          RUN   RUN   RUN   RUN   RUN
```

とします。

このモデルからCなら、

```c
#define CONTROL_PERIOD_MS 10

void control_task(void)
{
    controller_dispatch(&controller, EVENT_TICK);
}
```

RTOSなら、

```c
while (1) {
    control_task();
    delay_ms(CONTROL_PERIOD_MS);
}
```

のような実装になります。

Pythonでは実験用なら、

```python
import time

CONTROL_PERIOD = 0.010

while True:
    start = time.monotonic()

    controller.dispatch(Event.TICK)

    elapsed = time.monotonic() - start
    time.sleep(max(0, CONTROL_PERIOD - elapsed))
```

Rustなら、

```rust
use std::thread;
use std::time::{Duration, Instant};

let period = Duration::from_millis(10);

loop {
    let start = Instant::now();

    controller.dispatch(Event::Tick);

    let elapsed = start.elapsed();

    if elapsed < period {
        thread::sleep(period - elapsed);
    }
}
```

という形にできます。

---

# 6. すると3種類の図が一つにつながる

ここが、先ほど提案した **UML 3.0** のかなり重要なポイントになります。

```text
                    UML 3.0
                 Executable Model
                       │
          ┌────────────┼────────────┐
          │            │            │
          ↓            ↓            ↓
      State Model   Interaction   Timing
          │           Model        Model
          │            │            │
          ↓            ↓            ↓
       状態         メッセージ       時間
          │            │            │
          └────────────┼────────────┘
                       ↓
                 共通意味モデル
                       │
          ┌────────────┼────────────┐
          ↓            ↓            ↓
          C         Python         Rust
```

つまり、単純に

> 「UML図からプログラムを生成する」

ではなく、

> **「UML 3.0の共通実行モデルから、C/Python/Rustなどの異なる実装を生成する」**

という考え方です。

---

# 7. さらに一歩進めると

ユーザーが以前検討されていた

```text
UML
 ↓
CSP
 ↓
形式検証
 ↓
C/C++
 ↓
ECU
 ↓
Runtime Trace
```

とも自然につながります。

例えば、

```text
        State Diagram
             │
        Sequence Diagram
             │
        Timing Diagram
             │
             ↓
     ┌─────────────────┐
     │ Executable Model │
     └────────┬────────┘
              │
       ┌──────┴───────┐
       ↓              ↓
    Petri Net         CSP
       │              │
       ↓              ↓
   Reachability    Model Check
       │              │
       └──────┬───────┘
              ↓
       ┌──────────────┐
       │ Code Generator│
       └───────┬──────┘
          ┌────┼────┐
          ↓    ↓    ↓
          C  Python Rust
          │    │    │
          └────┬────┘
               ↓
         Test / Execution
               ↓
          Runtime Trace
               ↓
          Model Revision
```

となります。

これはかなり面白い方向性です。

**特にC/Python/Rustを同じモデルから生成する**ことで、「UMLが単なる設計図なのか、それとも実行可能なモデルなのか」を実際に検証できます。

次の段階では、これをさらに具体化して、**1枚のUML状態遷移図＋シーケンス図＋タイミング図から、C・Python・Rustを実際に自動生成する小さなコードジェネレータ**をPythonで作るのがよいと思います。そうすると、UML 3.0の原案が「思想」から「動くプロトタイプ」になります。
