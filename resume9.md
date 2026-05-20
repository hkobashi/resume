### プロジェクト概要
自社プロダクト群のインフラ基盤整備・開発生産性向上・セキュリティ強化を横断的に担当した。開発者がインフラを意識せず安全に開発・運用できる仕組みを構築することを主眼に置いた。

### 使用技術
- `インフラ管理`：Terraform、AWS IAM Identity Center、FluxCD
- `開発環境`：Kubernetes（Namespace分離）、Argo-CD、Argo-CD Image Updater、TiDB、TiDB-DM
- `セキュリティ`：gitleaks、pre-commit、HashiCorp Boundary、AWS GuardDuty MalwareProtection
- `イベント処理`：AWS EventBridge、AWS Lambda（Python）
- `社内ツール`：Redash、HashiCorp Boundary、Grafana

### 担当業務
- `PRごとの開発環境並列展開基盤の構築`  
  PRに`preview`ラベルを付与したことをトリガーにGitHub ActionsがArgo-CDのApplicationCRと  
  Image UpdaterCRをapplyし、PRごとに独立したk8s Namespaceの環境を自動構築する仕組みを実現した。  
  TiDB-DMを使ってMySQLのデータをTiDBClusterにリアルタイム同期することで、  
  ステートフルなリソースを含む本番同等の開発環境を独立して提供した。
- `Terraformリポジトリのリプレース`  
  ネストが深く複雑化した社内カスタムTerraform moduleを廃止し、  
  シンプルなresourceとコミュニティ公開のmoduleへ移行した。  
  AIによるTerraformコード作成コストの低下を背景に、カスタムmoduleの必要性が薄れたと判断した。  
  これによりインフラ・SRE以外のアプリ開発者もTerraformをメンテナンスできる体制を構築した。
- `AWS・GCP IAM設計および整備`  
  過剰付与・属人化していたIAMを最小権限原則に基づき再設計した。  
  「有事の際に開発者が責任を持てる範囲」を基準に権限を定義し、  
  CloudWatchログ閲覧・ECSタスク再起動・S3オブジェクト参照を付与する一方、  
  SecretsManagerやS3削除権限は外した。  
  AWS IAMはIAM Identity Centerへ移行しIaC化。  
  GCP IAMはGoogle WorkspaceのメーリングリストをIAMプリンシパルとして活用し、  
  入退場管理コストを最適化した。
- `HashiCorp BoundaryによるセキュアなDB直接アクセス基盤の構築`  
  従来はECS-Execでコンテナシェルを取得してMySQLクライアントを実行していたが、  
  コンテナにはS3オブジェクト操作・OpenSearch操作など開発者個人のIAMには付与していない権限が付与されており、  
  シェル経由でそれらの権限が濫用されるリスクがあった。  
  昨今のAIツールの普及によりこのリスクはより現実的になっていると判断し、  
  HashiCorp BoundaryをTerraformで管理することで、  
  環境変数へのアクセスや権限の濫用なしにDBへセキュアに接続できる基盤を構築した。  
  またDBアクセス可能なメンバーをTerraformで適切に管理できる体制を確立した。
- `セキュリティ対策`  
  gitleaksとpre-commitを組み合わせ、CredentialのGitHubリポジトリへのPushを防止する仕組みを導入した。  
  S3へのファイルアップロード時のマルウェア検出をGuardDuty MalwareProtectionで実現し、  
  検出時はEventBridge + Lambda（Python）で隔離用S3バケットへ自動移送する仕組みを構築した。

### どんな課題があったのか
- `開発環境の共有によるコンフリクトとフィードバックの遅延`  
  従来はstagingなどの共有環境でしか動作確認できず、  
  複数PRが競合するとフィードバックループが遅延していた。
- `IAMの過剰権限と属人的管理`  
  IAMがIaC化されておらず担当者依存の管理になっており、  
  セキュリティリスクと引き継ぎコストが高い状態だった。
- `ECS-Exec経由のDB接続によるコンテナ権限の濫用リスク`  
  ECS-Execでコンテナシェルを取得してDBに接続する運用では、  
  コンテナに付与されたS3・OpenSearchなどの操作権限が開発者によって濫用されるリスクがあった。  
  また環境変数を通じた機密情報への不正アクセスも懸念された。

### どうやって課題を解決したのか
- PRラベルをトリガーにした自動環境構築とTiDB-DMによるデータ同期で、  
  PRごとに独立した開発環境を提供し並走開発を実現した。
- IAMをTerraformでIaC化し、最小権限原則に基づく権限設計と入退場管理の自動化を実現した。
- HashiCorp Boundaryを導入することでシェルアクセスなしのセキュアなDB接続基盤を構築し、  
  コンテナ権限の濫用リスクと環境変数漏洩リスクを排除した。

### 工夫した点
- `PR単位での独立開発環境の自動構築`  
  previewラベルのみで本番同等のデータを持つ独立環境が起動する仕組みを実現し、  
  開発者のフィードバックループを大幅に短縮した。  
  TiDB検証（MySQLの書き込みスケーリング課題への解決策検討）も兼ねた設計とした。  
  また、PRのマージ・クローズ・previewラベル削除をトリガーにGitHub ActionsがApplicationCRと  
  ImageUpdaterCRを自動削除する仕組みを実装した。  
  不要になった環境を即時削除することでコストを最適化しつつ、  
  PRがオープンである間は環境を維持することで長期QAにも対応できる設計とした。
- `「インシデント対応責任範囲」を基準にしたIAM設計`  
  権限付与の基準を「開発者が有事の際に責任を持てる操作範囲」と定義することで、  
  最小権限と開発効率のバランスを取った設計を実現した。
- `gitleaksのmise連携によるリポジトリ単位のチューニング`  
  パッケージ管理ツールmiseを使い、gitleaksの設定をリポジトリごとにチューニングできる仕組みを整備した。
- `Terraformリポジトリのシンプル化による開発者へのオーナーシップ移譲`  
  カスタムmoduleを廃止しシンプルな構成に移行することで、  
  インフラ担当以外の開発者もTerraformコードをメンテナンスできる体制を構築した。
