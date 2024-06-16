# First step of Platform  Engineering

[toc]

## Hype Cycles from Gartner

**先進テクノロジーのハイプサイクル:2023年**

現在ピーク期かつ5-10年以内にはクラウドネイティブがモダンな開発手法となっている

![image](./image/hypesycle_for_emerging_technologies_2023.png)

**ソフトウェアエンジニアリングのハイプサイクル:2023年**

プラットフォーム エンジニアリングが2-5年以内に定常的に採用されることが予想されている

![image](./image/hypesycle_for_software_engineering_2023.png)

---

# Cloud Native & DevOps

## Cloud Native

クラウドの**小さくはじめて・素早く・柔軟に**デプロイできる利点を活用したシステムの設計/構成

## DevOps

アプリケーションなどの**開発と運用を柔軟で迅速に実施し、かつイテレーティブに改善していく**こと

```
- 自動化
- 継続的インテグレーション(CI)
- 継続的デリバリー(CD)
- 計測とフィードバック
```

## What is Platform Engineering?

### From Gartner

**本文**

> Platform engineering improves developer experience and productivity by providing self-service capabilities with automated infrastructure operations. It is trending because of its promise to optimize the developer experience and accelerate product teams’ delivery of customer value.

**日本語訳**

> プラットフォーム エンジニアリングは、自動化されたインフラストラクチャ運用によるセルフサービス機能を提供することで、開発者のエクスペリエンスと生産性を向上させます。開発者のエクスペリエンスを最適化し、プロダクトチームによる顧客価値の提供を加速することが約束されているため、トレンドになっています。

### From platformengineering.org

**本文**

> Platform engineering is the discipline of designing and building toolchains and workflows that enable self-service capabilities for software engineering organizations in the cloud-native era. Platform engineers provide an integrated product most often referred to as an “Internal Developer Platform” covering the operational necessities of the entire lifecycle of an application.

**日本語訳**

> プラットフォーム エンジニアリングは、クラウド ネイティブ時代のソフトウェア エンジニアリング組織のセルフサービス機能を可能にするツールチェーンとワークフローを設計および構築する分野です。プラットフォーム エンジニアは、アプリケーションのライフサイクル全体の運用上の必要性をカバーする、「内部開発者プラットフォーム」と呼ばれる統合製品を提供します。

## ざっくりまとめると

- 【**開発者向けのセルフサービス型の共通基盤**をプロダクトとして運用すること】

- 【開発者向けの**セルフサービス型の共通基盤をプロダクトとして運用すること**】

## 更に雑にまとめると (※個人の解釈)

- 【**開発者向けの情シス担当**として共通基盤を運用していく取り組み】

---

## ①開発者向けセルフサービス型の共通基盤がなぜ必要か？

### クラウド＆DevOpsの登場

### ちなみに...

「時代はクラウドネイティブだ！」と言っても登場人物はこんだけいる

![image](./image/landscape.png)

### クラウドの登場 -> Devopsの推進 -> 認知負荷の増大化

![image](./image/cognitive_load.png)

### 認知負荷とは

- 「人間の認知アーキテクチャの能力と限界」を出発点とした、効果的な学習デザインを考案するための理論
- 人間の頭の中で一度に処理できる情報量には限界があることを前提としている
- 3種類の負荷が存在

> 認知負荷理論についての詳細は[こちら](https://zenn.dev/kangetsu_121/articles/6b31565dda6053#fn-433b-6)を参照

### 3種の認知負荷

- 課題内在的負荷 (Intrinsic cognitive load)
    - 学習対象そのものの難易度により生じる負荷
- 課題外在的負荷 (Extraneous cognitive load)
    - 学習以外(指導者や教材)の難易度により生じる負荷
- 学習関連負荷 (Germane cognitive load)
    - 学習目標の達成のための認知活動により生じる負荷
## ①のまとめ

## ②セルフサービス型の共通基盤をプロダクトで運用していくためには？

### Platform as a Service

### Internal Developer Platform

### Internal Developer Portal

### IDPの代表例

### 共通プラットフォームのアンチパターン

### 目指すべきもの

### どうやって目指していくのか？

#### ProductとProject

#### ProductとProjectの関係性

## まとめ