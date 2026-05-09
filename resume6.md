### プロジェクト概要
- 自社サービスのコンテナ基盤（AWS ECS / EKS）の運用・改善および開発者向けCI/CDプラットフォームの整備を担当。小規模チームにおいて、開発チームがインフラを意識せずにデプロイ・運用できる仕組みの構築を主導した。

### 使用技術
- `インフラ`：
  AWS（ECS, EKS, ECR, ALB, Route53, IAM, VPC, CloudWatch）
- `コンテナオーケストレーション`：
  Kubernetes（EKS）
- `マニフェスト管理`：
  Helm / Kustomize
- `GitOps`：
  ArgoCD
- `CI/CD`：
  GitHub Actions
- `オートスケーリング`：
  HPA / Cluster Autoscaler
- `監視・可視化`：
  Prometheus / Grafana（セルフホスト）
- `ネットワーク`：
  Istio Ambient Mode / Ingress Controller / Network Policy

### チーム情報
- エンジニア10名以下の小規模チーム
  →SRE / インフラエンジニアとして担当

### 担当業務
- `GitOps基盤の構築（ArgoCD）`
  ArgoCDを導入し、Gitリポジトリをシングルソースオブトゥルースとしたデプロイフローを確立した。  
  →手動デプロイによる人的ミスを排除し、デプロイ履歴の可視化と環境間の差分管理を実現した。
- `CI/CDパイプラインの整備（GitHub Actions）`
  ECS / EKSへの自動デプロイパイプラインをGitHub Actionsで整備した。  
  コンテナイメージのビルド・プッシュからArgoCDへの同期トリガーまでを一貫して自動化した。
- `Kubernetesマニフェスト管理の標準化（Helm / Kustomize）`
  Helm / Kustomizeを用いてマニフェストをDRYに管理できる構成を整備し、  
  開発チームが環境差分を意識せずデプロイできる仕組みを構築した。
- `オートスケーリング設計・実装（HPA / Cluster Autoscaler）`
  負荷特性に応じてHPAによるPodスケーリングとCluster Autoscalerによるノードスケーリングを  
  組み合わせた構成を設計・実装した。  
  スパイクアクセスのパターンに合わせてスケールアウト・インのタイミングをチューニングした。
- `監視・オブザーバビリティ基盤の構築（Prometheus / Grafana）`
  Prometheus + GrafanaをEKS上にセルフホストで構築し、  
  アプリケーション・インフラ両面のメトリクス可視化を実現した。
- `サービスメッシュ導入・運用（Istio Ambient Mode）`
  Istio Ambient Mode（サイドカーレス構成）を採用し、サービス間通信のmTLS化と  
  Network Policyによるゼロトラストなネットワーク制御を実現した。  
  従来のサイドカー方式に比べてリソースオーバーヘッドを削減しつつ、  
  セキュアなサービス間通信基盤を構築した。

### どんな課題があったのか
- `デプロイパイプラインがインフラ管轄のAWS CodeSeriesに依存していたこと。`
  ＜背景＞
  デプロイパイプラインがインフラ管轄のAWS CodeSeries（CodePipeline / CodeBuild / CodeDeploy）に
  依存しており、開発チームがリリースのたびにインフラ担当を介する必要があった。
  また、EKS上の複数サービスのマニフェスト管理が統一されておらず、環境間の差分把握が困難な状態だった。

### どうやって課題を解決したのか
- GitHub ActionsによるCI/CDパイプラインとArgoCDによるGitOps化を導入し、  
  AWS CodeSeriesへの依存を解消した。  
  開発チームがGitへのマージだけでデプロイを完結できるセルフサービス型のリリースフローを実現した。  
  合わせてHelm / Kustomizeでマニフェストを標準化し、環境間の差分管理を整備した。

### 工夫した点
- `GitOpsによるデプロイのセルフサービス化`
  AWS CodeSeriesへの依存を解消し、ArgoCDを中心としたGitOpsフローを整備した。  
  開発チームがGitへのマージだけでリリースを完結できる仕組みを構築し、  
  インフラ担当へのデプロイ依頼をゼロにした。
- `Istio Ambient Modeによるゼロトラストネットワーク基盤の構築`
  サイドカーレス構成のIstio Ambient Modeを採用し、mTLSによるサービス間通信の暗号化と  
  Network Policyによるゼロトラストなネットワーク制御を実現した。  
  サイドカー方式に比べてリソースオーバーヘッドを抑えながらセキュリティレベルを向上させた。
- `コスト効率を考慮したEKSノード設計`
  Spot Instanceを活用したノードグループ設計を採用し、  
  本番環境の安定性を担保しつつインフラコストを最適化した。  
  スケールアウト・インのタイミングをスパイクアクセスのパターンに合わせてチューニングし、  
  過剰なスケールアウトによる無駄なコストを抑制した。
