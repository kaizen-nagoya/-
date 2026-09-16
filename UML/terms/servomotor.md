# Q1 サーボモータについて、機械設計、電磁気設計、制御理論、電力消費、制御回路、制御ソフトの観点でそれぞれ８つの課題と成果を示し、Wikipedia以外の参考文献のURLをつけてください。
A1 by chatgpt

はい。サーボモータは、**機械・電磁気・制御理論・エネルギー・パワーエレクトロニクス・ソフトウェアが一体になった典型的な複合システム**なので、先ほどの「AIの自律的改良と人間の意図的改良を競わせる」という考え方にも非常に向いています。

ここでは主として **PMSM（永久磁石同期モータ）＋インバータ＋エンコーダ＋位置・速度・電流制御**を想定します。
「成果」は特定企業の製品実績を断定するものではなく、研究・実用技術として達成されてきた代表的な成果です。

---

# 1. 機械設計

| # | 課題                  | 技術的成果                                        |
| - | ------------------- | -------------------------------------------- |
| 1 | **小型化と高トルク密度の両立**   | 磁気回路・ロータ・ステータを最適化し、同じ外形で高トルクを得る高トルク密度PMSMが実現 |
| 2 | **ロータの慣性を小さくする**    | ロータ質量・径を低減し、加減速応答を高速化                        |
| 3 | **振動・騒音を減らす**       | スロット・極数・磁石形状・スキューなどを最適化し、トルクリップルと振動を低減       |
| 4 | **コギングトルクを減らす**     | スキュー、磁石形状、スロット／極数の組合せ最適化などにより低減              |
| 5 | **軸・軸受の剛性を確保する**    | 高速回転時のたわみ、共振、軸受振動を考慮した機械設計                   |
| 6 | **熱変形への対応**         | モータ温度上昇による軸・ハウジング・磁石位置の変化を考慮した設計             |
| 7 | **エンコーダの取付精度を確保する** | 軸偏心・取付誤差を抑え、位置フィードバック精度を向上                   |
| 8 | **製造ばらつきに強い設計**     | 磁石寸法、ステータ形状などの公差を考慮したロバスト設計                  |

特にコギングトルクについては、スキューだけでも単純には済まず、**コギングトルク低減と軸方向力・平均トルク・トルクリップルのトレードオフ**があります。([IEEE Xplore][1])

参考文献：

* [IEEE: Skewing Technology for PMSMs](https://ieeexplore.ieee.org/document/10742395?utm_source=chatgpt.com)
* [J-STAGE: Cogging Torque Reduction in Permanent Magnet Brushless Motors](https://www.jstage.jst.go.jp/article/ieejias1987/122/4/122_4_338/_article?utm_source=chatgpt.com)
* [IET: Robust design for reducing cogging torque considering tolerances](https://ietresearch.onlinelibrary.wiley.com/doi/10.1049/iet-epa.2018.5634?utm_source=chatgpt.com)
* [J-STAGE: Encoder resolution based on control requirements](https://www.jstage.jst.go.jp/article/jjspe1986/65/7/65_7_1026/_article/-char/en?utm_source=chatgpt.com)

---

# 2. 電磁気設計

| # | 課題            | 技術的成果                           |
| - | ------------- | ------------------------------- |
| 1 | **高トルク化**     | 永久磁石磁束とリラクタンストルクを利用したIPMSMなどを実現 |
| 2 | **磁気飽和を抑える**  | 磁極・ヨーク・歯部の磁束密度をFEAで最適化          |
| 3 | **コギングトルク低減** | スロット／極数、磁石形状、スキューを最適化           |
| 4 | **トルクリップル低減** | 磁石形状・巻線分布・電流波形を最適化              |
| 5 | **鉄損低減**      | 電磁鋼板・磁束分布・PWM条件を最適化             |
| 6 | **銅損低減**      | 巻線設計、電流密度、導体断面積を最適化             |
| 7 | **高速回転対応**    | 弱め磁束制御と磁石・ロータ構造を組み合わせ、広い速度範囲を実現 |
| 8 | **設計期間短縮**    | 2D/3D FEA、最適化、機械学習を組み合わせたモータ設計  |

例えば最近の研究では、**2D FEAを大量に使い、3D FEAのデータを少量組み合わせるtransfer learningによって、コギングトルク設計の計算量を削減**するアプローチも報告されています。これは、今回の「AIによる自律的改良」というテーマともかなり相性がよいです。([IEEE Xplore][2])

参考文献：

* [IEEE: Transfer Learning-Based Cogging Torque Reduction](https://ieeexplore.ieee.org/document/10184449?utm_source=chatgpt.com)
* [IET: Permanent Magnet Shaping for Cogging Torque Reduction](https://www.sciencedirect.com/org/science/article/pii/S033216491800166X?utm_source=chatgpt.com)
* [IEEE: Advanced Electrical Motors and Control Strategies for Servo Systems](https://ieeexplore.ieee.org/document/10490170/?utm_source=chatgpt.com)

---

# 3. 制御理論

| # | 課題               | 技術的成果                       |
| - | ---------------- | --------------------------- |
| 1 | **位置決め誤差を小さくする** | 位置フィードバックを利用した閉ループ位置制御      |
| 2 | **速度変動を抑える**     | 速度ループを追加したカスケード制御           |
| 3 | **トルクを高速制御する**   | 電流・トルクループを最内側に配置            |
| 4 | **非線形性に対応する**    | 非線形モデル、フィードフォワード、適応制御などを導入  |
| 5 | **負荷変動に対応する**    | 外乱抑制・ロバスト制御により負荷変化への追従性を向上  |
| 6 | **高速域まで制御する**    | 弱め磁束制御によりベース速度以上を制御         |
| 7 | **パラメータ変動に対応する** | オブザーバ、適応制御、MPCなどを利用         |
| 8 | **多目的最適化**       | 位置精度、応答速度、振動、電力消費を同時に考慮する制御 |

現在のサーボ制御では典型的に、

**位置ループ → 速度ループ → 電流／トルクループ**

というカスケード構造が使われます。電流ループが最も高速です。([Texas Instruments][3])

FOCではClarke変換、Park変換によって電流をd-q軸に変換し、磁束とトルクを分離して制御します。([MathWorks][4])

参考文献：

* [MathWorks: Field-Oriented Control](https://www.mathworks.com/discovery/field-oriented-control.html?utm_source=chatgpt.com)
* [MathWorks: Design Field-Oriented Control Algorithm](https://www.mathworks.com/help/mcb/ug/design-field-oriented-control-algorithm.html?utm_source=chatgpt.com)
* [TI: Servo Motor Control Loop Technology](https://www.ti.com/document-viewer/lit/html/SNOAAB3?utm_source=chatgpt.com)
* [IEEE: Advanced Electrical Motors and Control Strategies](https://ieeexplore.ieee.org/document/10490170/?utm_source=chatgpt.com)

---

# 4. 電力消費・エネルギー

| # | 課題                  | 技術的成果                     |
| - | ------------------- | ------------------------- |
| 1 | **銅損を減らす**          | 電流を最適化し、I²R損失を低減          |
| 2 | **鉄損を減らす**          | 磁束密度・PWM周波数・材料を最適化        |
| 3 | **インバータ損失を減らす**     | SiC/GaNなどの高速・低損失半導体を利用    |
| 4 | **待機時消費電力を減らす**     | 必要なトルク・電流だけを供給            |
| 5 | **加減速エネルギーを減らす**    | 軌道生成そのものをエネルギー最適化         |
| 6 | **回生エネルギーを利用する**    | 減速時の機械エネルギーを電気エネルギーとして回収  |
| 7 | **モータ＋減速機を総合最適化する** | モータ単体ではなくギヤ比まで含めて最適化      |
| 8 | **実際の損失マップを使う**     | 銅損だけでなく鉄損・機械損まで含めて最適軌道を生成 |

ここは非常に重要です。従来は「モータ効率を上げる」と考えがちですが、研究では**モータ、カップリング、ギヤボックスを含むシステム全体のエネルギー最適化**が扱われています。([IDEAS/RePEc][5])

さらに2025年発表の研究では、実測したモータ効率マップを使い、銅損だけではなく鉄損・機械損も含めたエネルギー最適軌道を求めています。([IEEE Xplore][6])

参考文献：

* [Renewable and Sustainable Energy Reviews: Energy optimal design of servo-actuated systems](https://ideas.repec.org/a/eee/rensus/v156y2022ics1364032121011886.html?utm_source=chatgpt.com)
* [IEEE: Energy-Optimal Motion Trajectory for a Servo Motor Drive](https://ieeexplore.ieee.org/document/11122907/?utm_source=chatgpt.com)
* [Springer: Energy-optimal motion laws for servo-actuated systems](https://link.springer.com/article/10.1007/s40435-025-01900-6?utm_source=chatgpt.com)

---

# 5. 制御回路・パワーエレクトロニクス

| # | 課題             | 技術的成果                      |
| - | -------------- | -------------------------- |
| 1 | **高速PWM制御**    | 数十kHz以上のスイッチングによる高速電流制御    |
| 2 | **スイッチング損失低減** | GaN/SiCなどによる低損失インバータ       |
| 3 | **発熱低減**       | 高効率化と放熱設計によってヒートシンクを小型化    |
| 4 | **電流検出精度向上**   | シャント、絶縁ΔΣ変調器などによる高精度電流検出   |
| 5 | **EMI低減**      | PCBレイアウト、ゲート制御、スイッチング速度最適化 |
| 6 | **過電流保護**      | ハードウェアによる高速過電流検出・遮断        |
| 7 | **過熱保護**       | 温度監視とディレーティング／停止制御         |
| 8 | **ドライブ一体化**    | インバータをモータ側に組み込み、小型化・配線短縮   |

GaNの実用例では、TIの1.25 kW三相インバータで**50℃環境で1.25 kW、85℃で550 W、ピーク効率99%以上**というリファレンス設計が公開されています。([Texas Instruments][7])

また、2 kW級のGaNインバータでは**16 kHz PWM、320 V DCリンクでピーク99.4%**という設計例があります。([Texas Instruments][8])

参考文献：

* [TI: TIDA-00915 1.25-kW GaN Servo Inverter](https://www.ti.com/tool/TIDA-00915?utm_source=chatgpt.com)
* [TI: TIDA-010255 2-kW GaN Servo Inverter](https://training-dev.ti.com/tool/TIDA-010255?utm_source=chatgpt.com)
* [TI: GaN Motor Drivers](https://training-dev.ti.com/product-category/motor-drivers/gallium-nitride/overview.html?utm_source=chatgpt.com)
* [TI: TIDA-010936 48-V/16-A GaN Motor Drive](https://www.ti.com/tool/TIDA-010936?utm_source=chatgpt.com)

---

# 6. 制御ソフトウェア

| # | 課題           | 技術的成果                               |
| - | ------------ | ----------------------------------- |
| 1 | **リアルタイム性**  | 電流・速度・位置ループを決められた周期で確実に実行           |
| 2 | **CPU負荷低減**  | Clarke/Park変換などを高速・効率的に実装           |
| 3 | **エンコーダ処理**  | QEPなどで位置・速度をリアルタイム取得                |
| 4 | **固定小数点化**   | MCU/FPGA上で高速・低コストに演算                |
| 5 | **パラメータ調整**  | 電流・速度・位置PIゲインを体系的にチューニング            |
| 6 | **異常処理**     | 過電流、過電圧、過熱、エンコーダ異常などを処理             |
| 7 | **通信との統合**   | EtherCAT等とモータ制御をリアルタイム統合            |
| 8 | **検証・コード生成** | モデル→シミュレーション→PIL/HIL→組込みCコードという開発体系 |

例えばTIの分散型多軸サーボリファレンス設計では、各軸のリアルタイムコントローラが電流制御を担当し、別のコントローラが位置・速度制御とEtherCAT通信などを担当する構成が示されています。([Texas Instruments][9])

MathWorksでも、FOCからCコード／HDL生成、PIL、HILに至る開発フローが用意されています。([MathWorks][4])

参考文献：

* [TI: Distributed Multi-Axis Servo Drive Reference Design](https://www.ti.com/tool/TIDM-02006?utm_source=chatgpt.com)
* [MathWorks: Field-Oriented Control](https://www.mathworks.com/discovery/field-oriented-control.html?utm_source=chatgpt.com)
* [MathWorks: Vector Control](https://www.mathworks.com/help/mcb/vector-control-foc-and-dtc.html?utm_source=chatgpt.com)
* [J-STAGE: Encoder Resolution and Software Servo Systems](https://www.jstage.jst.go.jp/article/jjspe1986/65/7/65_7_1026/_article/-char/en?utm_source=chatgpt.com)

---

# 7. さらに高度な制御・AIとの融合

ここは今回のご質問と特に関係があります。

| # | 課題                   | 成果・方向性                       |
| - | -------------------- | ---------------------------- |
| 1 | **モータパラメータを正確に推定する** | 実機データから抵抗・インダクタンス等を推定        |
| 2 | **負荷を推定する**          | オブザーバやAIによる負荷トルク推定           |
| 3 | **異常を検出する**          | 電流・振動・温度等から異常兆候を検出           |
| 4 | **制御ゲインを自動調整する**     | 自動チューニング・最適化                 |
| 5 | **エネルギー最適制御**        | モーション軌道とモータ効率を同時最適化          |
| 6 | **MPCを利用する**         | 将来状態を予測して電圧ベクトルを選択           |
| 7 | **FEA＋機械学習**         | モータ設計探索の計算量を削減               |
| 8 | **AIによる制御ソフト改良**     | シミュレーション結果や故障ログから制御アルゴリズムを改善 |

MPCについては、現在も**計算量、遅延補償、モデル誤差、重み係数、トルクリップル**などが主要課題として研究されています。([IET][10])

一方、モータ設計側では、先ほどのように**FEA＋transfer learning**という組合せも登場しています。([IEEE Xplore][2])

参考文献：

* [Wiley/IET: Model Predictive Control for Energy Efficient AC Motor Drives](https://ietresearch.onlinelibrary.wiley.com/doi/10.1049/elp2.12517?utm_source=chatgpt.com)
* [IEEE: Transfer Learning-Based PMSM Design](https://ieeexplore.ieee.org/document/10184449?utm_source=chatgpt.com)
* [IEEE: Energy-Optimal Motion Trajectory](https://ieeexplore.ieee.org/document/11122907/?utm_source=chatgpt.com)

---

# 8. システム統合・信頼性

| # | 課題                   | 成果                      |
| - | -------------------- | ----------------------- |
| 1 | **モータ・インバータ・制御器の協調** | 個別最適からシステム全体最適へ         |
| 2 | **センサ故障**            | センサ異常検出・推定値への切替         |
| 3 | **インバータ故障**          | 過電流・短絡・過熱保護             |
| 4 | **通信遅延・ジッタ**         | リアルタイム通信と制御周期を協調        |
| 5 | **多軸干渉**             | 複数軸を協調制御                |
| 6 | **安全停止**             | 異常時の安全な停止シーケンス          |
| 7 | **温度・寿命**            | 熱ストレスを考慮した寿命・ディレーティング設計 |
| 8 | **システムレベル最適化**       | 機械・電磁気・電力・制御・ソフトを同時に最適化 |

多軸サーボでは、各軸の電流制御と上位の位置・速度制御を分散配置するアーキテクチャが実際に提案されています。([Texas Instruments][9])

---

## 8分野を横断すると非常に面白い構造になる

整理すると、サーボモータは次のように見ることができます。

```text
                  ┌──────────────┐
                  │   機械設計    │
                  │ 慣性・剛性・振動│
                  └──────┬───────┘
                         ↓
┌──────────────┐   ┌──────────────┐
│  電磁気設計   │ → │ サーボモータ  │
│ 磁石・巻線・磁束│   │              │
└──────────────┘   └──────┬───────┘
                           ↓
                  ┌──────────────┐
                  │  パワー回路   │
                  │ inverter/GaN │
                  └──────┬───────┘
                         ↓
                  ┌──────────────┐
                  │  制御理論     │
                  │ P/PI/FOC/MPC │
                  └──────┬───────┘
                         ↓
                  ┌──────────────┐
                  │  制御ソフト   │
                  │ MCU/RTOS/通信 │
                  └──────┬───────┘
                         ↓
                  ┌──────────────┐
                  │ エネルギー    │
                  │ 効率・回生    │
                  └──────────────┘
```

そして、先ほどの**「AIの自律的改良 vs 人間の意図的改良」**という考え方をここに適用すると、かなり興味深い研究テーマになります。

例えば、

> **「サーボモータ設計におけるAI自律最適化と人間主導最適化の比較」**

として、

```text
同じ要求仕様
      │
      ├── AIルート
      │     ↓
      │   モータ設計
      │     ↓
      │   FEA
      │     ↓
      │   制御設計
      │     ↓
      │   シミュレーション
      │     ↓
      │   自律改善
      │
      └── 人間ルート
            ↓
          設計者の仮説
            ↓
          FEA
            ↓
          制御設計
            ↓
          実験
            ↓
          意図的改善

              ↓
       ┌─────────────┐
       │ 同じ評価指標 │
       ├─────────────┤
       │ トルク       │
       │ 位置精度     │
       │ 応答時間     │
       │ 振動         │
       │ 消費電力     │
       │ 温度         │
       │ コスト       │
       └─────────────┘
```

とすれば、**「AIが作った最終結果」だけでなく、AIがどんな試行錯誤をしたか、人間がどこでAIとは異なる判断をしたか**を比較できます。

これは特に、**FEA → 制御シミュレーション → Cコード生成 → HIL → 実機試験**までつなげれば、かなり実践的な「生成AI時代のエンジニアリング方法論」になりそうです。MathWorksも、モータモデル、インバータ、FOC、保護、コード生成までを一連の開発対象として扱っています。([MathWorks][4])

また、サーボモータでは**「機械設計の変更が電磁気特性を変え、それが制御性能と電力消費を変える」**という相互作用があるため、単純な分野別最適化よりも、**6分野を一つの評価ループにした方が研究として面白い**と思います。

[1]: https://ieeexplore.ieee.org/document/10742395/?utm_source=chatgpt.com "Skewing Technology for Permanent Magnet Synchronous Motors: A Comprehensive Review and Recent Trends | IEEE Journals & Magazine | IEEE Xplore"
[2]: https://ieeexplore.ieee.org/document/10184449/?utm_source=chatgpt.com "Transfer Learning-Based Design Method for Cogging Torque Reduction in PMSM With Step-Skew Considering 3-D Leakage Flux | IEEE Journals & Magazine | IEEE Xplore"
[3]: https://www.ti.com/document-viewer/lit/html/SNOAAB3/GUID-DB8BE9CF-C2DF-4E85-8740-17D714772D8C?utm_source=chatgpt.com "SNOAAB3 Application brief | TI.com"
[4]: https://www.mathworks.com/discovery/field-oriented-control.html?utm_source=chatgpt.com "Field-Oriented Control - MATLAB & Simulink"
[5]: https://ideas.repec.org/a/eee/rensus/v156y2022ics1364032121011886.html?utm_source=chatgpt.com "Energy optimal design of servo-actuated systems: A concurrent approach based on scaling rules"
[6]: https://ieeexplore.ieee.org/document/11122907/?utm_source=chatgpt.com "Energy-Optimal Motion Trajectory Accounting for Machine Efficiency for a Servo Motor Drive | IEEE Journals & Magazine | IEEE Xplore"
[7]: https://www.ti.com/tool/TIDA-00915?utm_source=chatgpt.com "TIDA-00915 reference design | TI.com"
[8]: https://training-dev.ti.com/tool/TIDA-010255?utm_source=chatgpt.com "TIDA-010255 reference design | TI.com"
[9]: https://www.ti.com/tool/TIDM-02006?utm_source=chatgpt.com "TIDM-02006 reference design | TI.com"
[10]: https://ietresearch.onlinelibrary.wiley.com/doi/10.1049/elp2.12517?utm_source=chatgpt.com "Model predictive control for energy efficient AC motor drives: An overview - Shahid - 2024 - IET Electric Power Applications - Wiley Online Library"

# Q2 サーボモータとそれ以外のモータの違いはなんですか。
A2

「サーボモータ」は、**モータの種類そのものというより、「位置・速度・トルクなどをフィードバックして、目標値に追従させる制御方式を組み込んだモータシステム」**と考えると分かりやすいです。

つまり、

> **サーボモータ ≒ モータ + センサ + フィードバック制御 + ドライバ**

です。

一方、「それ以外のモータ」は、必ずしもフィードバック制御を前提としません。

---

## 1. 一番大きな違い

例えばモータを1000 rpmで回したいとします。

### 通常のモータ

```text
       電圧・電流
          ↓
       ┌─────┐
       │Motor│
       └──┬──┘
          ↓
        回転
```

「この電圧を加えれば、おおよそ1000 rpmになる」という**オープンループ**でも動かせます。

負荷が変化すると、

```text
負荷 ↑
 ↓
回転速度 ↓
```

となっても、制御装置がそれを知らない場合があります。

---

### サーボ

```text
目標速度 1000 rpm
       │
       ↓
    ┌───────┐
    │ 制御器 │
    └───┬───┘
        ↓
      Motor
        ↓
      回転
        │
        ↓
    Encoder
        │
        └────────→ 制御器
                    ↑
                 フィードバック
```

実際の回転速度をエンコーダなどで測定して、

```text
目標値 - 実測値
      ↓
    誤差
      ↓
    制御器
      ↓
    モータ
```

という閉ループを作ります。

---

# 2. 「サーボ」という言葉の本質

例えば、

```text
目標位置 = 90°
実位置   = 80°
```

なら、

```text
誤差 = 90 - 80 = 10°
```

なのでモータを動かします。

```text
             ┌──────────────┐
目標位置 ──→ │   制御器      │
             └──────┬───────┘
                    ↓
                  Motor
                    ↓
                 機械系
                    ↓
                 Encoder
                    │
                    └──────→ 実位置
```

これがサーボ制御です。

したがって、

**「モータを回す」のではなく、「モータを使って目的の状態を実現する」**

のがサーボの重要な考え方です。

---

# 3. では「普通のモータ」と何が違うのか

実はここが少しややこしいところです。

「サーボモータ」という分類と、

* DCモータ
* ACモータ
* ブラシレスDCモータ
* PMSM
* ステッピングモータ
* 誘導モータ

という分類は**別の軸**です。

例えば、

```text
                    モータの種類
                        │
        ┌───────────────┼───────────────┐
        ↓               ↓               ↓
       DC             AC/PMSM       Stepper
```

という分類があります。

一方、

```text
                 制御方式
                    │
             ┌──────┴──────┐
             ↓             ↓
        Open Loop      Closed Loop
                            │
                         Servo
```

という分類があります。

だから、

> **「PMSMだからサーボではない」**

とは限りません。

**PMSMをサーボモータとして使う**ことも普通にあります。

---

# 4. ステッピングモータとの比較が分かりやすい

例えばステッピングモータなら、

```text
指令
 ↓
パルス
 ↓
┌────────────┐
│ステッピング │
│   モータ    │
└────────────┘
```

という使い方があります。

例えば、

```text
100パルス → 100ステップ回転
```

と考えて制御できます。

ところが、実際には、

* 過大な負荷
* 急加速
* 共振
* 電源条件

などによって脱調する可能性があります。

オープンループなら、

```text
指令した位置 ≠ 実際の位置
```

になっても、それを検出できません。

---

## 5. サーボなら

エンコーダを付ければ、

```text
指令位置
   ↓
 [制御器]
   ↓
 モータ
   ↓
 実位置
   │
   └──── Encoder
             │
             └──→ [制御器]
```

となり、

```text
指令 100°
実際  95°
      ↓
誤差 +5°
      ↓
さらに回す
```

という制御ができます。

---

# 6. サーボには「位置・速度・電流」の階層があります

ここは制御工学として面白いところです。

典型的なサーボ制御は、

```text
        位置指令
           ↓
      ┌─────────┐
      │位置制御器 │
      └────┬────┘
           ↓
        速度指令
           ↓
      ┌─────────┐
      │速度制御器 │
      └────┬────┘
           ↓
        電流指令
           ↓
      ┌─────────┐
      │電流制御器 │
      └────┬────┘
           ↓
          Motor
```

という**カスケード制御**になっています。

さらに、

```text
位置
 ↑
 │
速度
 ↑
 │
電流
 ↑
 │
モータ
```

という階層になります。

---

# 7. ここは「状態遷移図・タイミング図」と非常に相性がいい

先ほどのUMLの話につなげると、サーボモータはかなり良い題材です。

例えば状態遷移図：

```text
             start
        ┌────────────→ READY
        │                  │
      [OFF]                │ enable
        ↑                   ↓
        │                 SERVO
        │                   │
        │                   │ error
        │                   ↓
        └─────────────── FAULT
```

さらにシーケンス図：

```text
Controller       Servo       Encoder
    │              │            │
    │ position     │            │
    │─────────────>│            │
    │              │            │
    │              │ read       │
    │              │───────────>│
    │              │<───────────│
    │              │            │
    │              │ control    │
    │              │            │
```

さらにタイミング図：

```text
time       0    1    2    3    4 ms
           |    |    |    |    |

Encoder    *----*----*----*----*

Current    *--*--*--*--*--*--*--

Velocity   *---------*---------*

Position   *-------------------->
```

となります。

つまりサーボは、

**状態 × シーケンス × 時間**

を一緒に扱う必要があります。

これはまさに、先ほど話していた**UML 3.0で「Timeを第一級にする」**という考え方の具体例になります。

---

## 8. まとめると

| 項目       | 一般的なモータ利用 | サーボモータシステム |
| -------- | --------- | ---------- |
| 主目的      | 回転・駆動     | 目標値への追従    |
| フィードバック  | なくてもよい    | 基本的に利用     |
| センサ      | 不要な場合あり   | エンコーダ等を使用  |
| 位置制御     | 難しい/限定的   | 得意         |
| 速度制御     | 可能        | 高精度        |
| 負荷変動への対応 | 制御方式による   | フィードバックで補正 |
| リアルタイム性  | 用途による     | 重要         |
| 状態遷移     | 必ずしも重要でない | 重要         |
| タイミング    | 用途による     | 非常に重要      |

そして最も大事なのは、

> **サーボモータと「それ以外のモータ」は、モータの物理構造だけで分けるものではない**

という点です。

**DCモータをサーボにすることも、PMSMをサーボにすることもできます。**

この観点から見ると、ユーザーが以前検討されていた **「状態遷移図 → C/Python/Rust」**の教材としては、サーボモータは非常に良い例です。状態・イベント・周期・デッドライン・エンコーダ・制御周期まで一つのモデルに入れられるからです。
