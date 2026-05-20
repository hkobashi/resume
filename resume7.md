### プロジェクト概要
- **VoC分析・インサイト可視化サービス**：顧客から収集したVoC（Voice of Customer）データをAIによってサマライズ・カテゴライズし、インサイトを可視化するサービス。
- **顧客施策管理サービス**：ロイヤル顧客に継続的な顧客体験を提供するためのキャンペーンなどの施策をノーコードで実現するサービス。

### 使用技術
- `言語`：Ruby
- `フレームワーク`：Ruby on Rails
- `インフラ`：
  AWS（EKS on EC2、ALB、RDS(MySQL)、S3、Route53）
- `Ingress`：
  Istio Ingress / aws-loadbalancer-controller
- `k8s`：
  Argo-CD、Argo-Workflow、Cert-Manager（Helm）、OpenSearch（Helm）
- `監視`：
  Grafana、Prometheus、Prometheus Railsクライアント
- `AI`：
  OpenAI API（Embeddings API、Batch API）

### チーム情報
- PdM 2名、アプリ開発者3名、インフラ1名  
→インフラ設計・構築・運用、監視基盤構築、AIバッチ開発を担当

### 担当業務
- `インフラ設計・構築（AWS）`  
  EKS on EC2を用いたコンテナ基盤の設計・構築。  
  ALB、RDS、S3、Route53を含むアーキテクチャ全体を担当した。
- `Argo-CDによるGitOpsデプロイ基盤の構築`  
  当初FluxCDを使用していたが、デプロイ状況をGUIで確認できる運用上の利点からArgo-CDへ移行した。
- `Argo-Workflowを用いた実行基盤の整備`  
  rakeタスクやdb:migrateなどの実行基盤としてArgo-Workflowを導入・整備した。
- `Grafana on EKSによるプロダクト監視基盤の構築`  
  ALBメトリクス・アクセス数・4xx/5xxエラー・スロークエリ・RailsControllerごとの処理時間を可視化。  
  1ヶ月間メトリクスを収集した実績をもとにアラート閾値を設計した。
- `VoC AI処理バッチの開発`  
  Argo-WorkflowのWorkflowTemplate CRDを定義し、OpenAI API（Embeddings API・Batch API）を  
  呼び出すアプリコードを含む全体を担当。  
  VoCデータ（CSV）のカテゴライズ・傾向分析・改善アクション提案をJSON形式で生成し、  
  OpenSearchで検索可能な状態に整備した。

### どんな課題があったのか
- `k8s初導入に伴う概念習得とCI/CDの整備`  
  本プロジェクトがk8sを用いたプロダクトデプロイの初体験であり、特にCRDの概念習得に時間を要した。  
  また既存プロダクトではデプロイ起因のサービス断が頻発しており、  
  外形監視とリソース使用率のみという最低限の監視体制が課題だった。

### どうやって課題を解決したのか
- ECSとの共通概念を活用しながらk8sをキャッチアップ。  
  FluxCDからArgo-CDへ移行することでGUIによるデプロイ状況の可視化を実現した。
- Grafana on EKSで監視基盤を構築し、ALBメトリクス・スロークエリ・RailsControllerごとの処理時間など  
  多角的な可視化を実現。1ヶ月の実績データをもとにアラート閾値を設計することで、  
  過検知・検知漏れを抑制した。

### 工夫した点
- `FluxCD → Argo-CDへの移行による運用効率の向上`  
  デプロイ状況をGUIで可視化できるArgo-CDへ移行し、運用の視認性と対応速度を改善した。
- `実績ベースのアラート閾値設計`  
  1ヶ月間メトリクスを収集してから閾値を設計することで、実態に即したアラート体制を構築した。
- `VoC AI処理バッチの全体設計・実装`  
  Argo-WorkflowのWorkflowTemplate CRDの定義からOpenAI API連携のアプリコードまで一貫して担当し、  
  VoCデータをOpenSearchで検索可能な資産として整備した。
- `セキュリティ対策`  
  Istio Ambient Modeを使ってクラスター内のネットワーク通信を制御し、コンテナ乗っ取りのリスクを最小化した。  
  またIstio ServiceEntryを使ってコンテナからk8sクラスター外への通信先を制限することで、  
  万一コンテナが乗っ取られた場合の外部への情報漏洩リスクを低減した。
- `Argo-CD Image Updaterによるデプロイフローの汎用化`  
  当初はGitHub Actionsで`/bin/sed`を使いDeploymentのイメージタグを書き換えていたが、  
  Argo-CD Image Updaterへ移行することでイメージタグ更新処理を汎用化し、  
  サービスごとの個別対応を不要にした。
