### プロジェクト概要
企業が顧客の本音（購買体験・利用シーン）を効率的に収集・分析するためのリサーチプラットフォーム。ゲーミフィケーションにより回答の質・量を最大化し、心理データと購買データを組み合わせた顧客理解の深化を支援する。食品・飲食・SaaSなど幅広い業界向けにSaaSとして提供。

### 使用技術
- `インフラ`：
  AWS（ECS on Fargate、ALB、RDS(MySQL)、S3、Route53、CloudFront、OpenSearch）、  
  Google Cloud（VertexAI）
- `CI/CD`：
  GitHub Actions、ecspresso

### チーム情報
- PdM 1名、エンジニア4名、デザイナー2名、インフラ1名  
→インフラ設計・構築・運用を担当

### 担当業務
- `インフラ設計・構築（AWS）`  
  ECS on Fargateを用いたコンテナ基盤の設計・構築。  
  ALB、RDS(MySQL)、S3、Route53、CloudFront、OpenSearchを含むアーキテクチャ全体を担当した。
- `Google Cloud VertexAI連携インフラ整備`  
  レシート画像解析のためのVertexAI連携に必要なインフラ・権限設計を担当した。
- `CI/CDパイプライン整備（AWS CodeSeries → ecspresso + GitHub Actions）`  
  初期はAWS CodeSeriesでCI/CDを構築。運用フェーズ移行後にecspresso + GitHub Actionsへ移行し、  
  デプロイ速度の改善とTerraformコードの管理コスト削減を実現した。

### どんな課題があったのか
- `ECS初期構築およびデプロイパイプラインの整備`  
  ECSを初めて初期構築する中で、デプロイパイプラインの整備が課題だった。  
  AWS CodeSeriesを用いたCI/CD構築はTerraformコードの管理コストが高く、  
  またCodeBuildの実行時間の長さからデプロイ1回あたり約10分かかっていた。

### どうやって課題を解決したのか
ecspresso + GitHub Actionsへ移行し、イメージビルドとデプロイを分離。  
CodeSeriesのTerraformコードを廃止することで管理コストを削減するとともに、  
デプロイ所要時間を約10分→2〜3分に短縮した。

### 工夫した点
- `デプロイパイプラインのecspresso移行によるTerraformコード管理の廃止`  
  ecspressoへの移行によりCodeSeriesのTerraformコード管理を廃止し、インフラ管理コストを削減した。
- `GitHub Actions + ecspressoの組み合わせによるデプロイ時間の短縮`  
  GitHub Actionsによるイメージビルドとecspressoによるデプロイを組み合わせることで、  
  デプロイ時間を約10分→2〜3分（約1/5）に短縮した。
