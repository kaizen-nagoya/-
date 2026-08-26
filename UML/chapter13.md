# text
・ビジネス系システム開発では、開発プロセスの開発工程（要求分析→分析→アーキテクチャ設計→詳細設計→実装→テスト）に沿って、モデルベースで開発を行うことが大切です。 

・要求分析では、ユーザ視点でユースケースモデルを作成することで、システムで実現すべき目的を明確にします。 

・分析では、開発者の視点でシステムを整理します。システムの静的な構造をクラス図を使って、動的な振る舞いをコミュニケーション図を使って整理します。 

・アーキテクチャ設計では、システム全体の設計方針を検討することで、機能要求、非機能要求を実現する仕組みを具体的に考えます。 

・詳細設計では、設計方針に従って個別の実現方法を計画し、分析モデルを設計モデルへと変換します。
# propose 既存のオープンソースから始める場合
１　オープンソースのアーキテクチャ、目的、目標がわかる図を３つ以上作成する

２　ソースコードから生成可能な状態遷移、シーケンス、タイミングを作成する

３　図とHAZOPの１１のGuideWordに基づいて分析する

４　ソースコードから１６種類の仕様書を作成する　ー＞細部の一部を理解する

５　アーキテクチャの図を３つ以上作成する。ー＞細部を理解してから全体の理解を図る。

# Q1 ここまでの作業をもとに、アーキテクチャのわかる図を３つ作成してください
a1 by chatgpt
はい。ここまでの **「HumHub → UML 3図 → HAZOP 11 Guide Words → 16制約 → 16仕様書」** を踏まえると、アーキテクチャを理解するためには、次の3枚が特に有効です。

1. **全体アーキテクチャ図**：HumHubを構成する層・主要コンポーネント
2. **制約・依存関係アーキテクチャ図**：C01～C16がどこに関係するか
3. **実行アーキテクチャ図**：Browser → Controller → Model → DB等の実行経路

---

# 1. HumHub全体アーキテクチャ

まず「HumHubは何からできているか」を俯瞰する図です。

```plantuml
@startuml
title HumHub 全体アーキテクチャ

skinparam componentStyle rectangle

actor "User" as User

package "Presentation Layer" {

    component "Browser" as Browser
    component "HumHub UI\n(View / Assets)" as UI
}

package "Application Layer" {

    component "Controller" as Controller
    component "Service / Component" as Service
    component "Event System" as Event
}

package "Domain Layer" {

    component "User" as UserDomain
    component "Space" as Space
    component "Content" as Content
    component "Permission" as Permission
    component "Notification" as Notification
}

package "Module Layer" {

    component "User Module" as UserModule
    component "Space Module" as SpaceModule
    component "Content Module" as ContentModule
    component "Post Module" as PostModule
    component "Notification Module" as NotificationModule
    component "Other Modules" as OtherModules
}

package "Infrastructure Layer" {

    component "Yii Framework" as Yii
    database "MariaDB / MySQL" as DB
    component "Cache / Session" as Cache
    component "Queue / Background Processing" as Queue
}

User --> Browser
Browser --> UI

UI --> Controller
Controller --> Service

Service --> UserDomain
Service --> Space
Service --> Content
Service --> Permission
Service --> Notification

Controller --> UserModule
Controller --> SpaceModule
Controller --> ContentModule
Controller --> PostModule

UserModule --> UserDomain
SpaceModule --> Space
ContentModule --> Content
PostModule --> Content
NotificationModule --> Notification

UserModule --> Permission
SpaceModule --> Permission
ContentModule --> Permission

Controller --> Yii
Service --> Yii

UserDomain --> DB
Space --> DB
Content --> DB
Notification --> DB

UserDomain --> Cache
Service --> Cache
Service --> Queue

@enduml
```
![arch](./arch.png)

### この図で重要なのは

HumHubを単純に

```text
PHP + MySQL
```

と考えないことです。

むしろ、

```text
UI
 ↓
Controller
 ↓
Service / Component
 ↓
Domain
 ↓
Module
 ↓
Infrastructure
```

という**複数の責務の層**として見るほうが、ソースコードを読むときに理解しやすくなります。

---

# 2. 16制約とアーキテクチャの関係

次は今回のHAZOP分析をアーキテクチャに重ねます。

```plantuml
@startuml
title HumHub アーキテクチャと16制約

skinparam componentStyle rectangle

package "Authentication / Session" {
    component "AuthController" as Auth
    component "User" as User
    component "Session" as Session

    component "C01\n認証状態" as C01
    component "C02\nSession増殖" as C02
    component "C06\n逆方向遷移" as C06
    component "C07\n認証主体" as C07
    component "C11\nLogout状態" as C11
    component "C16\n旧Session禁止" as C16
}

package "Authorization" {
    component "Permission" as Permission

    component "C12\n認証先行" as C12
    component "C14\n権限情報完全性" as C14
}

package "Space / Content" {
    component "Space" as Space
    component "Content" as Content
    component "Post" as Post

    component "C03\nSpaceアクセス" as C03
    component "C05\n部分情報" as C05
    component "C08\n投稿確定" as C08
    component "C10\nSpace確定前投稿禁止" as C10
    component "C15\n永続化先行" as C15
}

package "Application / Performance" {
    component "Controller / Service" as App
    component "Database" as DB
    component "Cache / Queue" as Infra

    component "C04\n不要状態遷移" as C04
    component "C09\n応答遅延" as C09
    component "C13\nDBアクセス量" as C13
}

Auth --> User
Auth --> Session

C01 --> Auth
C02 --> Session
C06 --> Session
C07 --> User
C11 --> Session
C16 --> Session

C12 --> Auth
C12 --> Permission
C14 --> Permission

Permission --> Space
Permission --> Content

C03 --> Space
C05 --> User
C05 --> Space
C05 --> Content
C08 --> Content
C10 --> Space
C10 --> Post
C15 --> Content

App --> DB
App --> Infra

C04 --> App
C09 --> App
C09 --> Infra
C13 --> DB

Auth --> C12
C12 --> C03
C03 --> C10
C10 --> C08
C08 --> C15

@enduml
```

![const](./const.png)


これは今回の作業の中では、かなり重要な図です。

16制約を単なる一覧ではなく、

```text
認証
 ↓
認可
 ↓
Space
 ↓
Content
 ↓
永続化
```

という**アーキテクチャ上の依存関係**として見ることができます。

---

# 3. 実行時アーキテクチャ

最後は、実際に利用者が操作したとき、システム内部で何が起こるかです。

```plantuml
@startuml
title HumHub 実行時アーキテクチャ

actor "User" as User

node "Client" {
    component "Web Browser" as Browser
}

node "HumHub Application Server" {

    component "Web Server" as Web

    package "Yii / HumHub" {

        component "Routing" as Routing
        component "Controller" as Controller
        component "Authentication" as Auth
        component "Authorization" as Authorization

        component "User Model" as UserModel
        component "Space Model" as SpaceModel
        component "Content Model" as ContentModel
        component "Post Module" as Post

        component "Notification" as Notification
    }
}

node "Infrastructure" {

    database "MariaDB / MySQL" as DB

    component "Session" as Session
    component "Cache" as Cache
    component "Queue" as Queue
}

User --> Browser : 操作

Browser --> Web : HTTP/HTTPS

Web --> Routing
Routing --> Controller

Controller --> Auth
Auth --> UserModel
Auth --> Session

Controller --> Authorization
Authorization --> UserModel
Authorization --> SpaceModel

Controller --> SpaceModel
SpaceModel --> DB

Controller --> ContentModel
ContentModel --> DB

Post --> ContentModel

ContentModel --> Notification
Notification --> Queue

UserModel --> DB
UserModel --> Cache

SpaceModel --> Cache

Controller --> Session

@enduml
```

---

# 3枚を並べると

今回の分析では、この3枚を**ズームレベルの違うアーキテクチャ図**として扱うと分かりやすいです。

```text
                 ┌─────────────────────┐
                 │ ① 全体アーキテクチャ │
                 │                     │
                 │ UI                  │
                 │ Application        │
                 │ Domain             │
                 │ Module             │
                 │ Infrastructure      │
                 └─────────┬───────────┘
                           │
                           ▼
                 ┌─────────────────────┐
                 │ ② 制約アーキテクチャ │
                 │                     │
                 │ C01 ～ C16         │
                 │                     │
                 │ Auth               │
                 │ Permission         │
                 │ Space              │
                 │ Content            │
                 └─────────┬───────────┘
                           │
                           ▼
                 ┌─────────────────────┐
                 │ ③ 実行アーキテクチャ │
                 │                     │
                 │ Browser            │
                 │ Controller         │
                 │ Model              │
                 │ DB                 │
                 │ Session            │
                 └─────────────────────┘
```

そして、今回の分析を一枚の流れにすると、

```text
利用者の問題
     ↓
  HAZOP
11 Guide Words
     ↓
16 Constraints
     ↓
16 Specifications
     ↓
Architecture
     ↓
Source Code
     ↓
UML
```

となります。

**この構造はかなり良いです。**

特に次の段階では、今回の3枚をさらに一歩進めて、**「HumHubの実ソースコードのパッケージ／クラスをPlantUMLに落とし、C01～C16の制約を各クラス・メソッドにトレーサビリティリンクする」**と、HumHubのリバースエンジニアリング用の本格的なアーキテクチャモデルになります。

