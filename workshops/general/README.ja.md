# ワークショップ: 総合

## 概要

| | |
|---|---|
| **フォーカス** | セキュリティトリアージ、モダナイゼーション、機能開発、テスト — Devinの機能を幅広く体験するハンズオンツアー |
| **所要時間** | フレキシブル（3トラック、各3ラボ） |
| **対象者** | 開発チーム、エンジニアリングリーダー、複数のユースケースでDevinを評価するプラットフォームチーム |
| **トラック** | **トラックA:** セキュリティ＆イシュートリアージ · **トラックB:** モダナイゼーション · **トラックC:** 機能開発＆テスト |

## ワークショップの流れ

このワークショップでは、エンジニアリング業務で最も一般的な3つのカテゴリをカバーします：システムの安全性と安定性の維持、レガシー技術のモダナイゼーション、品質を伴う新機能の構築。各トラックは独立しており、参加者は3つすべてを順番にこなして1日フルの体験とするか、短いセッションで1〜2トラックに集中することができます。

各トラックはDevinが異なる問題タイプに取り組む様子を示すように設計されています：
- **トラックA** はDevinをセキュリティおよび信頼性エージェントとして示します — スキャン、修復、調査、継続的なメンテナンスの自動化
- **トラックB** はDevinが大規模な構造変更を処理する様子を示します — リアーキテクチャ、アップグレード、コードベース全体の翻訳
- **トラックC** はDevinを日常的な開発パートナーとして示します — 機能構築、PRレビューによるバグ検出、テストカバレッジの追加、E2Eテスト失敗の修正

## このワークショップを最大限に活用するために

> **Devinは独自のマシン上で自律的に動作します。** プロンプトを貼り付けてセッションを開始すると、Devinは独立して実行されます — 監視する必要はありません。次のラボに進んだり、Ask Devinを探索したり、コーヒーを飲みに行ったりしてください。PRが開かれると通知が届きます。

ハンズオン時間を最大化するためのヒント：

- **セッションは早めに開始し、レビューは後で。** 各ラボには「Devinに貼り付け」ステップと「レビュー＆フィードバック」ステップがあります。まずセッションを開始し、待ち時間にAsk DevinリサーチやDeepWikiの閲覧を行いましょう — Devinはバックグラウンドで作業を続けます。
- **並列セッションを試してみましょう。** いくつかのラボでは複数のDevinセッションを同時に実行することを推奨しています。これは、Devinがリポジトリ全体の繰り返し作業を処理する実際のエンタープライズ利用を反映しています。
- **セッション作成前にAsk Devinで要件を練りましょう。** タスクの定義が明確であるほど、Devinの出力は向上します。Ask Devinを使って問題を事前に考え、やり取りを減らしてDevinが効率的に実行できるようにしましょう。
- **Devinのナレッジを徐々に蓄積しましょう。** セッション中にDevinがナレッジアイテムを提案したら、受け入れましょう — これがチームの共有コンテキストを構築し、Devinを時間とともにスマートにする方法です。プロジェクトの規約や標準について手動でナレッジを作成することもできます。
- **PRコメントでDevinを導きましょう。** DevinがPRを開くと、PRレビューエージェントとCIチェックが自動的なフィードバックループを提供します。PR上に直接コメントを残すと、Devinが起動してそれに対応します — これが本番環境でDevinと反復作業を行うコアワークフローです。

---

## 目次

- [トラックA: セキュリティ＆イシュートリアージ](#トラックa-セキュリティイシュートリアージ)
  - [ラボA1 — SASTスキャン＆脆弱性修復](#ラボa1--sastスキャン脆弱性修復)
  - [ラボA2 — バグ修正＆根本原因分析](#ラボa2--バグ修正根本原因分析)
  - [ラボA3 — スケジュール依存関係メンテナンス](#ラボa3--スケジュール依存関係メンテナンス)
- [トラックB: モダナイゼーション](#トラックb-モダナイゼーション)
  - [ラボB1 — モノリスからマイクロサービスへのリアーキテクチャ](#ラボb1--モノリスからマイクロサービスへのリアーキテクチャ)
  - [ラボB2 — EOLシステムのLTSバージョンへのアップグレード](#ラボb2--eolシステムのltsバージョンへのアップグレード)
  - [ラボB3 — 言語翻訳](#ラボb3--言語翻訳)
- [トラックC: 機能開発＆テスト](#トラックc-機能開発テスト)
  - [ラボC1 — 機能追加 + PRレビューフィードバック](#ラボc1--機能追加--prレビューフィードバック)
  - [ラボC2 — テストカバレッジの追加](#ラボc2--テストカバレッジの追加)
  - [ラボC3 — E2Eテスト実行＆問題修正](#ラボc3--e2eテスト実行問題修正)
- [追加チャレンジ](#追加チャレンジ)
- [推奨フォーマット](#推奨フォーマット)
- [必要なリポジトリ](#必要なリポジトリ)
- [コンテキスト](#コンテキスト)
- [Devin機能チェックリスト](#devin機能チェックリスト)

---

## トラックA: セキュリティ＆イシュートリアージ

トラックAでは、Devinをセキュリティおよび信頼性エージェントとして体験します。参加者はSASTスキャンを実行して脆弱性を修復し、根本原因分析でバグを調査・修正し、継続的な依存関係メンテナンスのための自動スケジュールセッションを設定します。

### ラボA1 — SASTスキャン＆脆弱性修復

- **モジュール:** [脆弱性の修復](../../modules/security/remediate-vulnerabilities.md) + [シフトレフトセキュリティ](../../modules/security/shift-left-security.md)
- **リポジトリ:**
  - [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance) — 既知のCVEとOWASP Dependency-Checkが事前設定されたSpring Boot 2.6.3
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — npm auditとTrivyスキャンを備えたNode.jsアプリ（代替）
- **目標:** SASTツールを実行して脆弱性を特定し、最も重大な発見を修復し、将来のPRが自動的にチェックされるようにCIパイプラインにセキュリティスキャンを追加する

このラボには2つのパートがあります：（1）既存の脆弱性を発見して修正する、（2）シフトレフトとしてCIにセキュリティスキャンを追加し、新しい脆弱性を自動的に検出する。

#### ステップ1: Devinに貼り付け（このプロンプトをDevinにコピー＆ペースト）

**並列セッション**として実行してください — 既存の脆弱性修正用と、CIスキャン追加用：

**セッションA — スキャン＆修復:**
```
Run `./gradlew dependencyCheckAnalyze` on uc-cve-remediation-regulatory-compliance to identify dependency CVEs. Remediate the top 5 most critical findings (CVSS >= 7.0) — start with Spring Boot 2.6.3, SnakeYAML 1.29, and sqlite-jdbc 3.36.0.3. Re-run the scan to verify the fixes. Create a `SECURITY_REMEDIATION.md` documenting the before/after results. Open a PR.
```

**セッションB — シフトレフト（CIパイプライン）:**
```
Create a GitHub Actions CI pipeline for uc-cve-remediation-regulatory-compliance that: builds with Gradle, runs `./gradlew dependencyCheckAnalyze`, fails the PR if any dependency has CVSS >= 7.0, generates an SBOM in CycloneDX format, and uploads the dependency check report as a build artifact. Open a PR with the workflow file.
```

#### ステップ2: Ask Devinでリサーチ

Devinがステップ1の作業中に、**Ask Devin**を開いて探索しましょう：
- *「uc-cve-remediation-regulatory-complianceの依存関係に既知のCVEは何がありますか？CRITICAL重大度のものはどれですか？」*
- *「Spring Boot 2.6.3の最も安全なアップグレードパスは何ですか — まず2.7.xに上げるべきですか、それとも直接3.xにジャンプすべきですか？」*
- *「Spring Bootアプリケーションで利用可能なSASTツールは何ですか？Trivy、Snyk、OWASP Dependency-Checkを比較してください。」*
- 分析結果を使って**2つ目のセッション**を開始してみましょう — SonarQubeスキャン（リポジトリには事前設定された`docker-compose.sonarqube.yml`があります）やシークレット検出用のpre-commitフックの追加を試みてください

#### ステップ3（オプション）: DeepWikiを読む

リポジトリの**DeepWiki**ページを開いて、コードベースのアーキテクチャと依存関係ツリーを理解しましょう。学んだことを使って異なるアプローチを試してみてください：
- Devinに**SBOM生成**（CycloneDX）をビルドに追加させる
- Devinに**シークレット検出用のpre-commitフック**（gitleaks）の追加を依頼する
- **SonarQubeスキャン**の追加を試みる — リポジトリには事前設定された`docker-compose.sonarqube.yml`があります
- CIスキャンがDevinにブランチ上の発見を自動修復させる**イベントドリブンSASTパイプライン**の実装をDevinに依頼する

#### ステップ4（オプション）: レビュー＆フィードバック

ステップ1からDevinがPRを開いたら、**修復の完全性**に焦点を当ててレビューしましょう：
- **スキャン結果:** DevinはCRITICALとHIGHの両方の発見に対処しましたか？見逃しはありますか？
- **CIワークフロー:** パイプラインは高重大度CVEで正しく失敗しますか？SBOMは生成されていますか？
- **検証:** Devinは修正が機能することを証明するためにスキャンを再実行しましたか？

**フィードバックコメント**を残してDevinの応答を観察しましょう：
- *「SnakeYAMLのバージョンにはまだCVE-2022-1471が残っています — 2.xにアップグレードしてください」*
- *「CRITICALなCVEでビルドを失敗させるCIワークフローを追加してください」*
- *「gitleaksシークレット検出用のpre-commitフック設定を生成してください」*

詳細なチャレンジ内容は[脆弱性の修復](../../modules/security/remediate-vulnerabilities.md)と[シフトレフトセキュリティ](../../modules/security/shift-left-security.md)を参照してください。

- **主な学び:**
  - **「スキャン → 修正 → 再スキャン」** — DevinはローカルSASTツールを実行し、CVEレポートを解釈し、発見を修復し、クローズドループで修正を検証します
  - **「シフトレフト」** — CIにセキュリティスキャンを追加することで、本番環境に到達する前に新しい脆弱性を検出します
  - **「エビデンスベースのコンプライアンス」** — SBOM、スキャンレポート、修復ドキュメントが監査証跡を提供します

- **目標成果（いずれか達成で可）:**
  - 重大CVEが修復されたOWASP Dependency-Checkレポート
  - ビフォー/アフターのエビデンスを含む`SECURITY_REMEDIATION.md`
  - 高重大度CVEで失敗するGitHub Actions CIワークフロー
  - CycloneDX形式で生成されたSBOM
  - レビューコメントとDevinの応答を含むPR

---

### ラボA2 — バグ修正＆根本原因分析

- **モジュール:** [ランタイムバグの修正](../../modules/application-development/fix-runtime-bug.md) + [クロスサービスバグ調査](../../modules/migration-modernization/cross-service-bug-investigation.md)
- **リポジトリ:**
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — React + Node.jsフルスタックアプリケーション
  - [quickapp-microservices](https://github.com/Cognition-Partner-Workshops/quickapp-microservices) — 仕込まれたクロスサービスバグを含む分解された.NETマイクロサービス（代替）
- **目標:** 実行中のアプリケーションでバグを発見・修正し、根本原因分析を実施し、サービス境界を越えて問題を追跡するDevinの能力を実証する

このラボではバグ調査の2つの側面を示します：（1）Devinが実行中のアプリでバグを発見・修正する探索的テスト、（2）あるサービスの症状が別のサービスに根本原因があるクロスサービスデバッグ。

#### ステップ1: Devinに貼り付け（このプロンプトをDevinにコピー＆ペースト）

1つまたは両方を選択：

**オプションA — 探索的バグハンティング（timesheet-app）:**
```
Start timesheet-app locally (backend: `cd backend && npm run dev`, frontend: `cd frontend && npm run dev`). Explore the application — create work entries, manage clients, try the reporting features. Find and document any bugs or unexpected behavior. Fix the most impactful bug you find. Take before/after screenshots. Write a `ROOT_CAUSE_ANALYSIS.md` explaining the bug, why it happened, and how you fixed it. Open a PR.
```

**オプションB — クロスサービスバグ調査（.NETマイクロサービス）:**
```
Order confirmation notification emails are showing wrong amounts after the microservice decomposition. A $149.99 order shows as $1.50 in the email preview. Investigate and fix this bug in quickapp-microservices. To reproduce: run the notification-service locally, POST to `http://localhost:5005/api/notification/events/order-placed` with `{"orderId": "11111111-1111-1111-1111-111111111111", "customerId": "22222222-2222-2222-2222-222222222222", "totalAmount": 149.99, "placedAt": "2026-03-17T12:00:00Z"}`, then open the preview URL — the total shows $1.50 instead of $149.99. Find the root cause, fix it, take before/after screenshots, and open a PR with your fix and root cause analysis.
```

#### ステップ2: Ask Devinでリサーチ

Devinがステップ1の作業中に、**Ask Devin**を開いて探索しましょう：
- *「Express + Reactアプリケーションで最も一般的なバグの種類は何ですか？何を探すべきですか？」*
- *「OrderPlacedEventが受信されてから通知メールがレンダリングされるまでのデータフローを追跡してください。金額はどこで変換されますか？」*
- *「OrderPlacedEvent.TotalAmountフィールドは何を表しますか — ドルですか、セントですか？共有コントラクトの定義を確認してください。」*
- 分析結果を使ってバグレポートを改善するか、アプリケーションの別の領域をターゲットとした**2つ目のセッション**を開始しましょう

#### ステップ3（オプション）: DeepWikiを読む

リポジトリの**DeepWiki**ページを開いてアプリケーションのアーキテクチャを理解しましょう：
- **timesheet-app** — データフローを理解し、バグがある可能性のあるコンポーネントを特定します（複雑なステート管理、APIエラーハンドリング、日付/時刻ロジック）
- **quickapp-microservices** — OrderサービスからNotificationサービスへのイベントフロー、`Shared.Contracts`ライブラリ、`NotificationRenderer`を理解します

異なるアプローチを試してみてください：
- Devinに発見したバグの**回帰テスト**を追加させる
- Devinにコードベースの他の場所に**同様のバグ**がないかチェックさせる
- Devinに症状から根本原因までを追跡する**デバッグナラティブ**を作成させる

#### ステップ4（オプション）: レビュー＆フィードバック

ステップ1からDevinがPRを開いたら、**根本原因分析**に焦点を当ててレビューしましょう：
- **根本原因:** 分析はバグが*なぜ*発生したかを説明していますか、それとも*何が*変更されたかだけですか？
- **修正の品質:** 修正は根本原因に対処していますか、それとも症状だけですか？
- **回帰防止:** このバグが再発した場合にキャッチするテストはありますか？

**フィードバックコメント**を残してDevinの応答を観察しましょう：
- *「このバグの回帰テストを追加してください」*
- *「コードベースの他の場所で同じ仮定をしている箇所はありますか？」*
- *「FormatCurrencyのユニットテストを追加して、$149.99が'$149.99'としてレンダリングされ'$1.50'にならないことを検証してください」*

詳細なチャレンジ内容は[ランタイムバグの修正](../../modules/application-development/fix-runtime-bug.md)と[クロスサービスバグ調査](../../modules/migration-modernization/cross-service-bug-investigation.md)を参照してください。

- **主な学び:**
  - **「Devinは開発者のようにデバッグする」** — ブラウザでバグを再現し、データフローを追跡し、ログを読み、根本原因を特定します
  - **「クロスサービストレーシング」** — Devinはサービス境界と共有コントラクトを越えてデータを追跡し、症状のサービスの外にある根本原因を見つけます
  - **「コメントを信用しない」** — クロスサービスバグには誤解を招くコードコメントが含まれています。Devinは実際のデータコントラクトに対して検証することを学びます
  - **「エビデンスとしてのスクリーンレコーディング」** — Devinのビフォー/アフターのスクリーンショットとレコーディングは修正が機能することの視覚的な証拠を提供します

- **目標成果（いずれか達成で可）:**
  - 再現手順付きでバグを特定
  - 根本原因分析のドキュメント化
  - ビフォー/アフターのエビデンス（スクリーンショットまたはスクリーンレコーディング）付きの修正実装
  - 回帰テストの追加
  - 修正の説明とレビューコメントへのDevinの応答を含むPR

---

### ラボA3 — スケジュール依存関係メンテナンス

- **モジュール:** [依存関係のアップグレード](../../modules/security/upgrade-dependencies.md)
- **リポジトリ:**
  - [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance) — GradleベースのSpring Bootアプリ
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — npmベースのNode.jsアプリ（代替）
- **目標:** 毎週のサイクルで自動的に依存関係を最新のマイナー/パッチバージョンにアップグレードするDevinスケジュールセッションを設定する — 常時稼働メンテナンスエージェントとしてのDevinを実証する

このラボでは**Devinスケジュールセッション**を紹介します — 人間の介入なしに実行される定期的な自動タスクです。依存関係のバージョンバンプは完璧なユースケースです：低リスク、大量、CIで簡単に検証可能。

#### ステップ1: Devinに貼り付け（このプロンプトをDevinにコピー＆ペースト）

```
Check all dependencies in uc-cve-remediation-regulatory-compliance for available minor and patch version updates. Upgrade each dependency to the latest minor version (do not jump major versions). Run `./gradlew build` and `./gradlew test` to verify the build still passes after each upgrade. If any upgrade breaks the build, revert that specific upgrade and document it. Open a PR with all successful upgrades and a `DEPENDENCY_UPDATES.md` listing what was upgraded, from which version to which version, and any upgrades that were skipped (with reasons). Title the PR "chore: weekly dependency version bump".
```

#### ステップ2: Ask Devinでリサーチ

Devinがステップ1の作業中に、**Ask Devin**を開いて探索しましょう：
- *「uc-cve-remediation-regulatory-complianceで最も古い依存関係は何ですか？マイナー/パッチアップデートが最も多いのはどれですか？」*
- *「リスクの観点からマイナーバージョンアップグレードとパッチバージョンアップグレードの違いは何ですか？自動アップグレードが安全な場合と人間のレビューが必要な場合はいつですか？」*
- *「ビルドを壊す依存関係アップグレードをチームはどのように処理すべきですか — 自動リバートしてフラグを立てるか、ブロックして通知するか？」*
- 依存関係アップグレードポリシーを記録した**Devinナレッジアイテム**の作成を検討しましょう（例：「パッチバージョンは常にアップグレード、テストが通ればマイナーバージョンをアップグレード、メジャーバージョンは自動アップグレードしない」）

#### ステップ3（オプション）: スケジュールセッションの設定

ステップ1の出力に満足したら、定期タスクに変換しましょう。新しいDevinセッションを開いてスケジュールの作成を依頼します：

```
Create a Devin scheduled session that runs weekly on Monday mornings against uc-cve-remediation-regulatory-compliance. The schedule should use this prompt: "Check all dependencies for available minor and patch version updates. Upgrade to the latest minor versions. Run the full test suite and build to verify nothing is broken. If any upgrade breaks the build, revert that specific upgrade and note it. Open a PR with the upgrades and a summary in DEPENDENCY_UPDATES.md."
```

これにより、Devinは人間の介入なしに毎週自動的に依存関係バンプPRを開きます。

#### ステップ4（オプション）: 複数リポジトリへの拡張

**timesheet-app**に対して同じパターンをnpmフレーバーのプロンプトで試してみましょう：

```
Check all npm dependencies in timesheet-app for available minor and patch version updates. Run `npm update` to upgrade to latest minor versions. Run `npm test` and `npm run build` to verify everything still works. Open a PR with the upgrades and a `DEPENDENCY_UPDATES.md`.
```

これは同じメンテナンスパターンが異なる技術スタックとリポジトリ間でどのようにスケールするかを実証します — 各スケジュールセッションは独自のVM上で独立して実行されます。

- **主な学び:**
  - **「退屈な作業を自動化する」** — 依存関係のアップグレードは反復的で低リスク、大量です。Devinは人間の介入なしに毎週処理します
  - **「スケジュールセッション = 常時稼働メンテナンス」** — チームはポリシーを一度設定し、Devinがサイクルで実行します。もう「次のスプリントでやろう」はありません
  - **「デフォルトで安全」** — プロンプトはDevinにビルドの検証、破壊的アップグレードのリバート、すべてのドキュメント化を指示します。PRにはマージに人間の承認が必要です
  - **「リポジトリと技術スタック間でスケール」** — 同じパターンがGradle、npm、pip、cargoなどで動作します。リポジトリごとに一度設定すれば後は忘れて大丈夫です

- **目標成果（いずれか達成で可）:**
  - 週次依存関係アップグレード用に設定されたDevinスケジュールセッション
  - 依存関係のアップグレードと`DEPENDENCY_UPDATES.md`を含むPR
  - チームの依存関係アップグレードポリシーを記録したナレッジアイテム
  - 複数のリポジトリ/技術スタックで同じスケジュールを複製

---

## トラックB: モダナイゼーション

トラックBでは、Devinがコードベースの大規模な構造変更を処理する様子を体験します。参加者はモノリスからマイクロサービスを抽出し、サポート終了フレームワークをLTSバージョンにアップグレードし、レガシーコードをモダンな言語に翻訳します。

### ラボB1 — モノリスからマイクロサービスへのリアーキテクチャ

- **モジュール:** [コンテナ化＆マイクロサービス抽出](../../modules/migration-modernization/containerization-microservice-extraction.md)
- **リポジトリ:**
  - [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction) — 3つの明確な境界付きコンテキスト（Articles、Users/Profiles、Comments）を持つSpring Boot 2.6.3モノリス
  - [petclinic-microservices](https://github.com/Cognition-Partner-Workshops/petclinic-microservices) — 比較用のリファレンスマイクロサービスアーキテクチャ（オプション）
- **目標:** モノリスのドメイン境界を分析し、境界付きコンテキストを独自のAPI、Dockerfile、データベースを持つスタンドアロンマイクロサービスとして抽出し、Docker Composeでサービスを接続する

#### ステップ1: Devinに貼り付け（このプロンプトをDevinにコピー＆ペースト）

```
Analyze the domain boundaries in uc-spring-boot-upgrade-microservice-extraction. This Spring Boot monolith has three bounded contexts: Articles (CRUD, feed, favorites, tags), Users/Profiles (registration, authentication, following), and Comments (CRUD linked to articles).

Extract the Comments domain into a standalone Spring Boot microservice with its own database, Dockerfile, and REST API. The monolith should communicate with the comments microservice via HTTP. Create a docker-compose.yml that runs both services. Add integration tests that verify the monolith and microservice communicate correctly. Open a PR.
```

#### ステップ2: Ask Devinでリサーチ

Devinがステップ1の作業中に、**Ask Devin**を開いて探索しましょう：
- *「uc-spring-boot-upgrade-microservice-extractionのドメイン境界は何ですか？どの境界付きコンテキストが最も抽出しやすく、どれが最も難しいですか？」*
- *「このモノリスからArticlesドメインを抽出する場合、どの共有コードとデータベーステーブルを処理する必要がありますか？どのコミュニケーションパターンを使うべきですか？」*
- *「モノリスからマイクロサービスを抽出する際に必要な結合テストは何ですか？2つのサービス間のHTTP通信をどのようにテストしますか？」*
- 改善された分析を使って**2つ目のセッション**を開始しましょう — 別の境界付きコンテキスト（ArticlesはCommentsより難しい）の抽出を試み、Devinのアプローチを比較してください

#### ステップ3（オプション）: DeepWikiを読む

リポジトリの**DeepWiki**ページを開いて、モジュール構造、ドメインモデル、依存関係グラフを理解しましょう：
1. どのような境界付きコンテキストが存在し、どの程度密結合かを特定する
2. テストカバレッジを確認 — 抽出中のセーフティネットとして最も良いテストを持つドメインはどれか？
3. **petclinic-microservices**と比較して、完全に分解されたアーキテクチャがどのようなものかを確認する

異なるアプローチを試してみてください：
- 別々のDevinセッションを使用して**2つの境界付きコンテキストを並行して**抽出する
- Devinにコード変更前に**ドメイン分解ドキュメント**を作成させる
- Docker設定に加えて**Kubernetesマニフェスト**をDevinに追加させる

#### ステップ4（オプション）: レビュー＆フィードバック

ステップ1からDevinがPRを開いたら、**抽出の品質**に焦点を当ててレビューしましょう：
- **クリーンな境界:** モノリスへの残留依存関係はありますか？マイクロサービスは本当にスタンドアロンですか？
- **通信:** サービス間のHTTP通信は正しく機能していますか？適切なエラーハンドリングとリトライパターンがありますか？
- **コンテナ化:** Dockerfileはマルチステージビルドを使用していますか？docker-composeは起動順序を処理していますか？

**フィードバックコメント**を残してDevinの応答を観察しましょう：
- *「Dockerfileはイメージサイズを削減するためにマルチステージビルドを使用すべきです」*
- *「両方のサービスにヘルスチェックエンドポイントを追加してください」*
- *「結合テストは接続性だけでなく、完全なリクエスト-レスポンスサイクルを検証すべきです」*

詳細なチャレンジ内容は[コンテナ化＆マイクロサービス抽出](../../modules/migration-modernization/containerization-microservice-extraction.md)を参照してください。

- **主な学び:**
  - **「Devinはドメイン境界を分析する」** — 抽出前にコードベースを読んで境界付きコンテキスト、共有コード、結合ポイントを特定します
  - **「抽出はコピー＆ペースト以上」** — マイクロサービスには独自のデータベース、APIコントラクト、Dockerfile、サービス間通信が必要です
  - **「Docker Composeで動作を証明」** — 結合テストと共に両方のサービスを実行することで、分解をエンドツーエンドで検証します
  - **「並列抽出でスケール」** — 複数の境界付きコンテキストを別々のDevinセッションで同時に抽出できます

- **目標成果（いずれか達成で可）:**
  - スタンドアロンのSpring Bootサービスとして抽出された1つの境界付きコンテキスト
  - 抽出されたサービスのマルチステージビルドDockerfile
  - 両方のサービスを実行するDocker Compose設定
  - サービス間通信を検証する結合テスト
  - 抽出ドキュメントとレビューコメントへのDevinの応答を含むPR

---

### ラボB2 — EOLシステムのLTSバージョンへのアップグレード

- **モジュール:** [フレームワークアップグレード](../../modules/migration-modernization/framework-upgrade.md) + [反復的フレームワークアップグレード](../../modules/migration-modernization/repetitive-framework-upgrades.md)
- **リポジトリ:**
  - [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction) — Spring Boot 2.6.3 / Java 11（EOL）→ Spring Boot 3.x / Java 17+（LTS）
  - [petclinic-angular](https://github.com/Cognition-Partner-Workshops/petclinic-angular) — Angularバージョンアップグレード
  - [ts-angular-realworld](https://github.com/Cognition-Partner-Workshops/ts-angular-realworld) — Angularバージョンアップグレード（並列比較用の2つ目のリポジトリ）
- **目標:** 複数のリポジトリ全体で並列Devinセッションを実行してフレームワークと言語バージョンをアップグレードする — エンタープライズスケールでの反復的アップグレードタスクのDevinの処理能力を実証する

#### ステップ1: Devinに貼り付け（このプロンプトをDevinにコピー＆ペースト）

同じアップグレードパターンがリポジトリ間でどのようにスケールするかを確認するため、**並列セッション**として実行してください：

**セッションA — Spring Boot + Javaアップグレード:**
```
Upgrade uc-spring-boot-upgrade-microservice-extraction from Java 11 + Spring Boot 2.6.3 to Java 17 + Spring Boot 3.2. Handle the javax to jakarta namespace migration, update Gradle build configuration, fix any deprecations, and ensure all tests pass. Document every breaking change and how you resolved it in the PR description. Open a PR.
```

**セッションB — Angularアップグレード（PetClinic）:**
```
Upgrade petclinic-angular to the latest Angular version. Handle any breaking changes from the Angular update guide, update all dependencies, fix deprecated APIs, and ensure the app builds successfully. Document every breaking change encountered. Open a PR.
```

**セッションC — Angularアップグレード（RealWorld、比較用オプション）:**
```
Upgrade ts-angular-realworld to the latest Angular version. Handle any breaking changes, update dependencies, fix deprecated APIs, and ensure the app builds and tests pass. Open a PR.
```

#### ステップ2: Ask Devinでリサーチ

Devinがアップグレード作業中に、**Ask Devin**を開いて探索しましょう：
- *「Spring Boot 2から3にアップグレードする際の最大のリスクは何ですか？どのjavaxからjakartaへの変更が最も壊れやすいですか？」*
- *「petclinic-angularの現在のAngularバージョンは何ですか？そのバージョンと最新版の間の破壊的変更は何ですか？」*
- *「petclinic-angularとts-angular-realworldのAngularアップグレードパスを比較してください — 同じ破壊的変更が予想されますか？」*
- 分析を使って、数十の類似サービスに適用できる**反復可能なアップグレードランブック**を計画しましょう
- 満足のいくランブックができたら、**Devinプレイブック**への変換を検討しましょう — プロンプトを再設計することなく、チームメンバーが将来のアップグレードをワンクリックでトリガーできる再利用可能な手順セット

#### ステップ3（オプション）: DeepWikiを読む

各リポジトリの**DeepWiki**ページを開いて、アップグレード前のコードベースを理解しましょう：
1. **uc-spring-boot-upgrade-microservice-extraction** — ビルド設定、Spring Security設定、依存関係グラフを理解します。これらはSpring Boot 2から3への移行で最も影響を受ける領域です。
2. **petclinic-angular** — コンポーネント階層とモジュール構造を理解します。非推奨のAngularパターン（NgModules vs. スタンドアロンコンポーネント）を特定します。
3. **ts-angular-realworld** — PetClinic Angularアプリと比較します。異なるコードベースでは、同じアップグレードでも異なる破壊的変更が発生する可能性があります。

異なるアプローチを試してみてください：
- 両方のAngularアップグレードを**並列で**実行し、アップグレードPRを並べて比較する
- Devinに両方のAngularアップグレード体験から**共有アップグレードチェックリスト**を生成させる
- Devinに**反復可能なアップグレードランブック**を作成させる — その後**プレイブック**として保存し、チームメンバーが再利用できるようにする
- スケジューリングについて考える：フレームワークのアップグレードは**Devinスケジュールセッション**の優れた候補です — 例えば、週次で依存関係バージョンバンプを実行して問題を早期に検出する

#### ステップ4（オプション）: レビュー＆フィードバック

並列セッションからDevinがPRを開いたら、アップグレードアプローチを比較しましょう：
- **Spring Boot PR:** javaxからjakartaへの移行は完了していますか？すべてのテストがグリーンでビルドは通りますか？
- **Angular PR:** 両方のアップグレードはAngularアップデートガイドに従いましたか？非推奨パターンは完全に削除されていますか？
- **PR間の比較:** Devinは両方のAngularリポジトリで同じ問題に遭遇しましたか？一貫して解決されましたか？

**フィードバックコメント**を残してDevinの応答を観察しましょう：
- *「まだjavax.servletを使用しています — jakarta.servletに更新してください」*
- *「NgModulesからスタンドアロンコンポーネントへの移行もできますか？」*
- *「遭遇したすべての破壊的変更とその解決方法を文書化したアップグレードレポートを生成してください」*

詳細なチャレンジ内容は[フレームワークアップグレード](../../modules/migration-modernization/framework-upgrade.md)と[反復的フレームワークアップグレード](../../modules/migration-modernization/repetitive-framework-upgrades.md)を参照してください。

- **主な学び:**
  - **「同じプロンプト、複数のリポジトリ」** — 同じアップグレードタスクが異なるサービスに一貫して適用され、エンタープライズスケールを実証します
  - **「並列セッションで暦日を節約」** — 順次実行すると数週間かかるアップグレードが、各VM上で干渉なく同時に実行できます
  - **「アップグレード間の一貫性」** — Devinはリポジトリ間で同じパターンを適用し、同じ破壊的変更をキャッチします
  - **「プレイブックが一回限りの作業を反復可能なプロセスに変える」** — アップグレードランブックをプレイブックとしてキャプチャし、次回のアップグレードをチームメンバーがワンクリックで操作できるようにする

- **目標成果（いずれか達成で可）:**
  - Java 17+ / Spring Boot 3.xでビルドとテストが通るSpring Bootアプリ
  - 最新バージョンにアップグレードされビルドが通るAngularアプリ
  - すべての破壊的変更と解決策を記載したアップグレードドキュメント
  - リポジトリ間のアップグレードPRの並列比較
  - 反復可能なアップグレードランブックをキャプチャしたプレイブック
  - レビューコメントとDevinの応答を含むPR

---

### ラボB3 — 言語翻訳

- **モジュール:** [言語翻訳](../../modules/migration-modernization/legacy-modernization-combined.md)
- **リポジトリ:**
  - [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction) — Spring Boot（Java）RealWorldアプリ — ソース言語
  - [ts-angular-realworld](https://github.com/Cognition-Partner-Workshops/ts-angular-realworld) — Angular（TypeScript）RealWorldアプリ — 代替ターゲットのリファレンス
- **目標:** Java Spring Bootのサービスレイヤーを同等のPython（Flask/FastAPI）アプリケーションに翻訳し、APIコントラクトを保持しパリティテストで機能的等価性を証明する。ソースとターゲットの両方がUbuntuでコンパイル・実行可能

#### ステップ1: Devinに貼り付け（このプロンプトをDevinにコピー＆ペースト）

```
Translate the Articles API from uc-spring-boot-upgrade-microservice-extraction (Java/Spring Boot) into a Python FastAPI application. The Python version should expose the same REST endpoints: GET /api/articles, GET /api/articles/:slug, POST /api/articles, PUT /api/articles/:slug, DELETE /api/articles/:slug, and GET /api/articles/feed. Use SQLAlchemy for persistence and Pydantic for request/response models. Preserve the same JSON response shape so the API is a drop-in replacement. Write pytest tests that verify the Python endpoints return identical responses to the Java version for the same inputs. Document the translation decisions in a `MIGRATION_NOTES.md`. Open a PR.
```

#### ステップ2: Ask Devinでリサーチ

Devinがステップ1の作業中に、**Ask Devin**を開いて探索しましょう：
- *「uc-spring-boot-upgrade-microservice-extractionの主なAPIエンドポイントは何ですか？各エンドポイントは何をし、リクエスト/レスポンスのシェイプは何ですか？」*
- *「Spring Boot REST APIを翻訳するのに最適なPython Webフレームワークは何ですか — Flask、FastAPI、Django REST Framework？トレードオフを比較してください。」*
- *「最もリスクの高いJavaからPythonへの翻訳パターンは何ですか — 型安全性、null処理、依存性注入、ORM の違い？」*
- 分析を使って**2つ目のセッション**を開始しましょう — 同じサービスを**TypeScript（Express/NestJS）**に翻訳して結果を比較してみてください

#### ステップ3（オプション）: DeepWikiを読む

リポジトリの**DeepWiki**ページを開いて、翻訳前のJavaアプリケーションアーキテクチャを理解しましょう：
1. レイヤードアーキテクチャを理解する：APIコントローラ → アプリケーションサービス → ドメインエンティティ → リポジトリ
2. 最も複雑なビジネスロジックを持つサービスレイヤーを特定する
3. Javaパターン（Spring DI、JPAリポジトリ、Bean Validation）をPython同等物（FastAPI依存性注入、SQLAlchemy、Pydantic）にマッピングする

| Javaコンポーネント | Python同等物 | 備考 |
|---------------|-------------------|-------|
| `@RestController` | FastAPIルーター | ルートデコレータ |
| `@Service` | プレーンクラスまたはFastAPI依存性 | ビジネスロジック層 |
| JPAリポジトリ | SQLAlchemyセッション | ORMクエリ |
| Bean Validation（`@Valid`） | Pydanticモデル | リクエストバリデーション |
| `@Transactional` | SQLAlchemyセッションコンテキストマネージャ | トランザクション管理 |
| MyBatis XMLマッパー | SQLAlchemy Coreクエリ | 複雑なクエリ |

異なるアプローチを試してみてください：
- 別々のDevinセッションを使用して**複数のAPIドメインを並列で**翻訳する（Articles、Users、Comments）
- Devinにコード作成前に**翻訳マッピングドキュメント**を作成させる
- **2つの異なるPythonフレームワーク**（FastAPI vs. Flask）をターゲットにして出力を比較する
- JavaとPythonの両方のサーバーにアクセスして同一のレスポンスを検証する**コントラクトテストスイート**をDevinに作成させる

#### ステップ4（オプション）: レビュー＆フィードバック

ステップ1からDevinがPRを開いたら、**翻訳の忠実性**に焦点を当ててレビューしましょう：
- **APIコントラクト:** Python版はJava版とまったく同じJSONシェイプを返しますか？
- **ビジネスロジック:** エッジケースは同じ方法で処理されていますか（例：slug生成、ページネーション、認証）？
- **パリティテスト:** pytestテストは同じ入力に対して同一の動作を検証していますか？

**フィードバックコメント**を残してDevinの応答を観察しましょう：
- *「slug生成ロジックが一致しません — JavaはString.toLowerCase().replaceAll()を使用していますが、Pythonは異なる正規表現を使用しています」*
- *「両方のサーバーを起動して同じリクエストに対するレスポンスを比較するコントラクトテストを追加してください」*
- *「エラーレスポンスが一致しません — Javaは{errors: {body: [...]}}を返しますが、Pythonはフラットな文字列を返します」*

詳細なチャレンジ内容は[言語翻訳](../../modules/migration-modernization/legacy-modernization-combined.md)を参照してください。

- **主な学び:**
  - **「同じAPI、異なる言語」** — Devinはビジネスロジックを翻訳しながらAPIコントラクトを保持し、新しいサービスがドロップイン置換となるようにします
  - **「パリティテストで正確性を証明」** — Python版は同じ入力に対してJava版と同一のレスポンスを返す必要があります。テストがその証明です。
  - **「両方がUbuntuでコンパイル可能」** — メインフレーム言語とは異なり、JavaとPythonの両方がDevinのマシン上でネイティブに実行されるため、Devinは両方のバージョンをエンドツーエンドでビルド、実行、テストできます
  - **「移行ノートが決定事項を記録」** — `MIGRATION_NOTES.md`がJavaパターンからPython同等物へのマッピング方法と行われたトレードオフを文書化します

- **目標成果（いずれか達成で可）:**
  - Javaソースと同じREST APIを実装するPython（FastAPI）アプリケーション
  - 機能的等価性を証明するpytestパリティテスト
  - 翻訳の決定事項とパターンマッピングを文書化した`MIGRATION_NOTES.md`
  - ソースとターゲットの両方がUbuntuでビルド・実行に成功
  - Pythonコード、テスト、レビューコメントへのDevinの応答を含むPR

---

## トラックC: 機能開発＆テスト

トラックCでは、Devinを日常的な開発パートナーとして体験します。参加者は新機能を構築し、PRレビューが自動的に潜在的な問題をフラグする様子を確認し、テストカバレッジを追加し、E2Eテストを実行して問題を発見・修正します。

### ラボC1 — 機能追加 + PRレビューフィードバック

- **モジュール:** [新機能開発](../../modules/application-development/new-feature-development.md)
- **リポジトリ:**
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — React + Node.jsフルスタックアプリケーション
  - [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction) — Spring Boot RealWorldアプリ（代替）
- **目標:** 既存のアプリケーションに新機能を構築し、PRレビューが実装の潜在的なバグや問題を自動的にフラグする様子を観察し、Devinにフィードバックに対応させる

#### ステップ1: Ask Devinから始める（推奨）

セッション作成前に、**Ask Devin**を使って機能のスコープを定めましょう。要件が具体的であるほどDevinの出力は向上します — Ask Devinはコードを書き始める前に詳細を考えるのに役立ちます。

例えば、こう質問してみましょう：*「timesheet-appはCRUD機能にどのような既存パターンを使用していますか？新しい'Projects'機能はどのようなデータモデル、API構造、Reactコンポーネント規約に従うべきですか？」*

学んだことを使って、Devinセッションに貼り付ける前にプロンプトを改善しましょう：

**オプションA — フルスタックCRUD機能（timesheet-app）:**
```
Add a "Projects" management feature to timesheet-app. Users should be able to create, view, edit, and delete projects. Each project has a name, description, client assignment, start date, and status (active/completed/on-hold). Add both the backend API endpoints and the frontend UI page. Follow the existing patterns in the codebase for the data model, API structure, and React components. Write tests for the backend endpoints. Open a PR.
```

**オプションB — API機能（Spring Boot RealWorldアプリ）:**
```
Add an "article statistics" feature to uc-spring-boot-upgrade-microservice-extraction. Create a new endpoint GET /api/articles/:slug/stats that returns: view count, favorite count, comment count, and days since published. Add a GET /api/stats/trending endpoint that returns the top 10 most-favorited articles in the last 7 days. Write tests for both endpoints. Open a PR.
```

#### ステップ2（オプション）: DeepWikiを読む

リポジトリの**DeepWiki**ページを開いて既存の機能パターンを理解しましょう。学んだことを使って異なるアプローチを試してみてください：
- Devinに新機能の**バリデーションルール**を追加させる（例：プロジェクトの日付は重複してはならない）
- 新しいUIコンポーネントの**フロントエンドテスト**（React Testing Library）をDevinに追加させる
- 新機能のすべてのCRUD操作に**監査ログ**を追加させる
- 新しいエンドポイントの**APIドキュメント**（Swagger/OpenAPI）をDevinに生成させる

#### ステップ3: PRレビュー＆PRレビューエージェントの観察

ステップ1からDevinがPRを開いたら、**PRレビューフィードバックループ**の出番です：
- **PRレビューが自動的にPRを分析**し、潜在的な問題をフラグします — バリデーションの欠落、エラーハンドリングのギャップ、セキュリティの懸念、ロジックバグ
- **PRレビューコメントを注意深く読みましょう** — 本番環境に到達してしまう実際の問題をしばしばキャッチします
- PRレビューコメントと並行して**自分のフィードバック**を残し、Devinが両方に対応する様子を観察しましょう：
  - *「PRレビューがプロジェクト名フィールドに長さバリデーションがないとフラグしました — 最大長チェックを追加してください」*
  - *「アクティブなプロジェクトがあるクライアントが削除された場合のエラーハンドリングを追加してください」*
  - *「ReactコンポーネントがAPI呼び出し中のローディング状態を処理していません — スピナーを追加してください」*

これは本番ワークフローを実証します：Devinがコードを書き、PRレビューが問題を検出し、Devinが修正し、あなたが承認する。

詳細なチャレンジ内容は[新機能開発](../../modules/application-development/new-feature-development.md)を参照してください。

- **主な学び:**
  - **「Devinは既存のパターンに従う」** — 実装前にコードベースの規約を分析し、既存のアーキテクチャに適合するコードを生成します
  - **「PRレビューが人間の見落としをキャッチ」** — 自動レビューエージェントがPRを確認する前にバリデーションのギャップ、エラーハンドリングの問題、潜在的なバグをフラグします
  - **「フィードバックループがワークフロー」** — Devinが書き、PRレビューがフラグし、あなたがコメントし、Devinが修正する。これがチームが毎日本番でDevinを使う方法です
  - **「ナレッジは時間と共に蓄積」** — このセッション中にDevinがプロジェクトの規約を発見したら、ナレッジアイテムとして保存しましょう。将来のセッションが自動的に恩恵を受けます

- **目標成果（いずれか達成で可）:**
  - 既存のコード規約に従って実装された新機能
  - 新機能のユニットテストおよび/または結合テスト
  - マイグレーションスクリプト付きのデータベーススキーマ変更
  - 新機能のフロントエンドUIコンポーネント
  - 潜在的な問題を特定するPRレビューコメント
  - PRレビューと人間のフィードバック両方へのDevinの応答
  - フィードバックループの動作を示すレビューイテレーション付きPR

---

### ラボC2 — テストカバレッジの追加

- **モジュール:** [ユニットテスト](../../modules/testing-qa/unit-testing.md) + [BDDテスト生成](../../modules/testing-qa/bdd-test-generation.md)
- **リポジトリ:**
  - [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction) — 既存のJUnitインフラストラクチャを持つSpring Bootアプリ
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — Jestテスト付きのReact + Node.jsアプリ（代替）
  - [uc-bdd-test-generation-cucumber](https://github.com/Cognition-Partner-Workshops/uc-bdd-test-generation-cucumber) — Spring Boot + Cucumber BDDフレームワーク（代替）
- **目標:** 既存のテストカバレッジを分析し、テスト不足のモジュールに意味のあるテストを生成し、オプションでREST API用のBDDテストシナリオを作成する

#### ステップ1: Devinに貼り付け（このプロンプトをDevinにコピー＆ペースト）

1つ選択するか、複数を並列で実行：

**オプションA — ユニットテストカバレッジ（Spring Boot）:**
```
Analyze the current test coverage of uc-spring-boot-upgrade-microservice-extraction. Identify the modules with the lowest test coverage. Write JUnit tests for the top 5 least-tested modules, following the existing test patterns and framework. Aim for at least 80% line coverage on each module. Generate a JaCoCo coverage report. Open a PR with the new tests.
```

**オプションB — ユニットテストカバレッジ（Node.js）:**
```
Analyze the current test coverage of timesheet-app. Add missing Jest unit tests for the backend API routes and service layer. Generate a coverage report and fix any failing tests. Aim for at least 80% coverage on each tested module. Open a PR with the new tests.
```

**オプションC — BDDテスト生成（Cucumber）:**
```
Review the uc-bdd-test-generation-cucumber codebase. This is a Spring Boot + Cucumber BDD framework with pre-built step definitions for REST API testing. Run `mvn test` to see the existing scenarios pass. Then add new Gherkin feature files that test edge cases for the existing Users API: creating a user with missing required fields (expect 400), creating a user with duplicate ID (expect 409 or appropriate error), pagination and sorting, and input validation. Also create a new `OrderController` with endpoints for managing orders and write corresponding Gherkin feature files. Open a PR.
```

#### ステップ2: Ask Devinでリサーチ

Devinがステップ1の作業中に、**Ask Devin**を開いて探索しましょう：
- *「パッケージ別の現在のテストカバレッジの内訳は？どのドメイン（articles、users、comments）のカバレッジが最も弱いですか？」*
- *「コードベースはどのようなテストパターンを使用していますか？JUnit with Mockito？TestContainers？新しいテストはどの規約に従うべきですか？」*
- *「REST APIテストのCucumberベストプラクティスは何ですか — シナリオは独立すべきですか、それとも状態を共有できますか？」*
- 分析を使って**2つ目のセッション**を開始しましょう — プロパティベーステスト、ミューテーションテスト、データドリブンCucumber Scenario Outlinesの生成を試みてください

#### ステップ3（オプション）: DeepWikiを読む

リポジトリの**DeepWiki**ページを開いて、最も複雑なロジックを処理するサービスメソッドを理解しましょう — これらが新しいテストの優先対象です。学んだことを使って異なるアプローチを試してみてください：
- Cucumber Scenario OutlinesとExamplesテーブルを使用して**データドリブンシナリオ**をDevinに生成させる
- 認証失敗とレート制限の**ネガティブテストシナリオ**をDevinに追加させる
- 既存のテストが実際にバグをキャッチすることを検証する**ミューテーションテスト**をDevinに追加させる
- テストとビジネス要件をマッピングする**テストカバレッジマトリックス**をDevinに作成させる

#### ステップ4（オプション）: レビュー＆フィードバック

ステップ1からDevinがPRを開いたら、**テスト品質**に焦点を当ててレビューしましょう：
- **意味のあるアサーション:** テストは動作を検証していますか、それとも些細なアサーションでカバレッジを水増ししていますか？
- **エッジケース:** テストはエラーケース、境界条件、null/空入力をカバーしていますか？
- **独立性:** テストは分離されていますか、それとも互いにまたはデータベース状態に依存していますか？
- **可読性:** Gherkinシナリオは非開発者にも読めますか？実装の詳細ではなくビジネスの振る舞いを記述していますか？

**フィードバックコメント**を残してDevinの応答を観察しましょう：
- *「無効な日付範囲と重複エントリのエッジケーステストを追加してください」*
- *「ステップ定義はより説明的なメソッド名を使用すべきです」*
- *「Cucumber Scenario OutlinesとExamplesテーブルを使用したデータドリブンシナリオを追加してください」*

詳細なチャレンジ内容は[ユニットテスト](../../modules/testing-qa/unit-testing.md)と[BDDテスト生成](../../modules/testing-qa/bdd-test-generation.md)を参照してください。

- **主な学び:**
  - **「Devinは意味のあるテストを書く」** — カバレッジの水増しではありません。コードベースを分析して何をテストすべきかを理解し、実際の動作を検証するテストを生成します
  - **「テンプレートではなくパターンからテスト生成」** — Devinは既存のテストフレームワークとAPIパターンを読んで、プロジェクトの規約に適合するテストを生成します
  - **「BDDテストが開発とビジネスを橋渡し」** — Gherkinシナリオは非開発者にも読めるため、テストツールであると同時にコミュニケーションツールにもなります
  - **「スケールでの品質エンジニアリング」** — テスト生成はDevinスケジュールセッションの優れた候補です：新しいコードが追加されるたびに、定期的なテスト生成実行をスケジュールしてカバレッジを維持します

- **目標成果（いずれか達成で可）:**
  - 意味のあるテストケースでテストカバレッジが向上
  - JaCoCoまたはIstanbulカバレッジレポートの生成
  - 既存または新規APIエンドポイントのGherkinフィーチャーファイル
  - Scenario Outlinesを使用したデータドリブンシナリオ
  - テストファイルとカバレッジエビデンスを含むPR

---

### ラボC3 — E2Eテスト実行＆問題修正

- **モジュール:** [エンドツーエンドテスト](../../modules/testing-qa/end-to-end-testing.md) + [ランタイムバグの修正](../../modules/application-development/fix-runtime-bug.md)
- **リポジトリ:**
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — React + Node.jsフルスタックアプリケーション
  - [uc-bdd-test-generation-cucumber](https://github.com/Cognition-Partner-Workshops/uc-bdd-test-generation-cucumber) — Spring Boot + Cucumber BDDフレームワーク（代替）
- **目標:** 実行中のアプリケーションに対してE2Eテストを作成・実行し、テストを通じて問題を発見し、修正する — テスト→発見→修正の完全なサイクルを実証する

このラボはテストの全体像を完成させます：ユニットテスト（ラボC2）を追加した後、アプリケーションをエンドツーエンドで実行し、完全なユーザーワークフローを実行するPlaywrightテストを作成し、テストを通じて問題を発見し、見つけたものを修正します。

#### ステップ1: Devinに貼り付け（このプロンプトをDevinにコピー＆ペースト）

**オプションA — Playwright E2Eテスト（timesheet-app）:**
```
Set up and run timesheet-app locally (backend on port 3001, frontend on port 5173). Write Playwright E2E tests for the core user workflows: (1) Login flow, (2) Create a client, (3) Create a work entry for that client, (4) Verify the work entry appears in the list, (5) Edit the work entry, (6) Delete the work entry, (7) Check the reports page shows correct totals. Run the tests and take a screen recording. If any tests fail because of application bugs, fix the bugs too. Open a PR with the test files and any bug fixes.
```

**オプションB — API E2Eテスト（BDDフレームワーク）:**
```
Review the uc-bdd-test-generation-cucumber codebase. Run `mvn test` to verify the existing 16 scenarios pass. Then write new end-to-end Gherkin scenarios that test the full user lifecycle: create a user, verify they appear in the list, update their details, verify the update, then delete them and verify they're gone. Also test cross-resource relationships — create a user, create orders for that user, verify the orders appear when querying by user. Run all tests and fix any failures. Open a PR.
```

#### ステップ2: Ask Devinでリサーチ

Devinがステップ1の作業中に、**Ask Devin**を開いて探索しましょう：
- *「timesheet-appでE2Eテストの恩恵を受ける主なユーザーワークフローは何ですか？」*
- *「Playwrightのベストプラクティスは何ですか — 適切なセレクタ、待機戦略、テスト分離？」*
- *「フレーキーなE2Eテストの最も一般的な原因は何で、どのように防止できますか？」*
- インサイトを使って追加のワークフロー（クライアント管理、レポーティング、CSV/PDFエクスポート）のテストを作成しましょう

#### ステップ3（オプション）: DeepWikiを読む

リポジトリの**DeepWiki**ページを開いて、フロントエンドのルート、コンポーネント、APIコントラクトを理解しましょう。学んだことを使って異なるアプローチを試してみてください：
- スクリーンショット比較による**ビジュアルリグレッションテスト**をDevinに追加させる
- **アクセシビリティテスト**（Playwrightとのaxe-core統合）をDevinに追加させる
- **パフォーマンスアサーション**（ページロード時間 < 2秒）をDevinに追加させる
- 各テストのスクリーンショットとタイミングを含む**テストレポート**をDevinに生成させる

#### ステップ4（オプション）: レビュー＆フィードバック

ステップ1からDevinがPRを開いたら、**テストの堅牢性とバグ修正**に焦点を当ててレビューしましょう：
- **テスト品質:** テストは堅牢ですか、それともフレーキーになりますか？適切なセレクタと待機戦略を使用していますか？
- **バグ修正:** テスト中にDevinがバグを発見・修正した場合、修正は根本原因に対処していますか？
- **カバレッジの完全性:** E2Eテストは重要なユーザーパスをカバーしていますか？不足しているワークフローはありますか？

**フィードバックコメント**を残してDevinの応答を観察しましょう：
- *「必須フィールドが欠落した状態でフォームを送信するテストを追加してください — バリデーションエラーが表示されるはずです」*
- *「テストが要素の待機ではなくsleep()を使用しています — フレーキーさを避けるために修正してください」*
- *「レポートページのCSVエクスポート機能のE2Eテストを追加してください」*

詳細なチャレンジ内容は[エンドツーエンドテスト](../../modules/testing-qa/end-to-end-testing.md)と[ランタイムバグの修正](../../modules/application-development/fix-runtime-bug.md)を参照してください。

- **主な学び:**
  - **「テスト→発見→修正」** — E2Eテストは既存の動作を検証するだけでなく、バグを発見します。Devinは同じセッションで見つけたものを修正します
  - **「Devinはブラウザを使う」** — 内蔵ブラウザを通じて実行中のアプリケーションとやり取りし、人間のテスターと同様にフローをクリックしていきます
  - **「エビデンスとしてのスクリーンレコーディング」** — Devinのレコーディングは何がテストされ何が発見されたかを正確に示し、ステークホルダーに視覚的な証拠を提供します
  - **「E2E + ユニットテスト = 完全カバレッジ」** — ラボC2（ユニットテスト）とラボC3（E2Eテスト）を組み合わせることで、コードレベルとユーザーインタラクションレベルの両方で包括的なカバレッジを実現します

- **目標成果（いずれか達成で可）:**
  - コアユーザーワークフローをカバーするPlaywright E2Eテストスイート
  - テスト実行のスクリーンレコーディング
  - E2Eテスト中に発見されたバグ修正
  - APIライフサイクルテスト用のGherkinエンドツーエンドシナリオ
  - テストファイル、バグ修正、スクリーンレコーディングエビデンスを含むPR

---

## 追加チャレンジ

早く終わった参加者やさらに探索したい参加者は、完全な[モジュールカタログ](../../modules/)からどのチャレンジにも挑戦できます。推奨される追加チャレンジ：

| チャレンジ | モジュール | リポジトリ | トラック | 難易度 |
|-----------|--------|------|-------|------------|
| データソースマイグレーション | [データソースマイグレーション](../../modules/data-engineering/data-source-migration.md) | uc-data-source-migration-jdbc-normalization | B | 中級 |
| イベントドリブンSASTパイプライン | [イベントドリブンSAST修復](../../modules/security/event-driven-sast-remediation.md) | uc-cve-remediation-regulatory-compliance | A | 上級 |
| モノリス分解（.NET） | [.NETモノリス分解](../../modules/migration-modernization/dotnet-monolith-decomposition.md) | modular-monolith-ddd | B | 上級 |
| コードリファクタリング＆技術的負債 | [コードリファクタリング](../../modules/architecture-design/code-refactoring-tech-debt.md) | 任意 | C | 中級 |
| APIドキュメント | [APIドキュメント](../../modules/technical-documentation/api-documentation.md) | 任意 | C | 初級 |

## 推奨フォーマット

| フォーマット | 推奨アプローチ |
|--------|---------------------|
| 1日 | 全3トラック：トラックA（午前）+ トラックB（午後前半）+ トラックC（午後後半） |
| 半日 | 2トラック：参加者の関心に基づいて任意の2トラックを選択 |
| 短時間セッション | 1トラック完全実施 + 別トラックから1ラボ |
| サンプラー | 各トラックから1ラボずつ選択（例：A1 + B2 + C1）で幅広く体験 |
| 単一ラボ | A1（セキュリティ）またはC1（機能開発）がインパクトの面で推奨 |

## 必要なリポジトリ

### トラックA（セキュリティ＆イシュートリアージ）
- [ ] uc-cve-remediation-regulatory-compliance
- [ ] timesheet-app
- [ ] quickapp-microservices（オプション、ラボA2オプションB用）

### トラックB（モダナイゼーション）
- [ ] uc-spring-boot-upgrade-microservice-extraction
- [ ] petclinic-angular
- [ ] ts-angular-realworld（ラボB3翻訳ターゲットリファレンス、並列比較にも有用）

### トラックC（機能開発＆テスト）
- [ ] timesheet-app
- [ ] uc-spring-boot-upgrade-microservice-extraction（オプション、ラボC1オプションB用）
- [ ] uc-bdd-test-generation-cucumber（オプション、ラボC2オプションC / ラボC3オプションB用）

## リポジトリ複製に関する注記

- `uc-spring-boot-upgrade-microservice-extraction`と`uc-cve-remediation-regulatory-compliance`はどちらも`spring-boot-realworld-example-app`に由来します（[カタログ](../../catalog/repos.md)のクラスターC1）。各ユースケースが独立した開始点を持つように意図的に別リポジトリになっています。

## コンテキスト

- **対象者:** 一般的なエンジニアリングチーム — このワークショップは技術スタックの専門性に関係なく関連するよう設計されています
- **フォーカス:** セキュリティ、モダナイゼーション、機能開発にわたる幅広い内容 — Devinの汎用性を実証
- **全体を通じて織り込まれたDevinの価値テーマ：**
  - 自律的、オフマシン実行 — セッションを開始して次に進む
  - リポジトリ全体の反復作業をスケールする並列セッション
  - セッション前の要件収集とタスクスコーピングにAsk Devinを使用
  - 再利用可能なチームコンテキストを構築するナレッジとプレイブック
  - 自動フィードバックループとしてのPRレビューエージェント + CIチェック
  - 継続的コードメンテナンスのためのスケジュールセッション（依存関係バンプ、テスト生成）
  - 長時間タスク処理 — 会議中もDevinが作業を続行

## Devin機能チェックリスト

セッション全体を通じて[Devin機能付録](../../modules/devin-features/README.md)の進捗を追跡するよう参加者に推奨してください。
