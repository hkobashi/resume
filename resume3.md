### プロジェクト概要
- デモ機貸出管理システム開発

### 使用技術
- 利用言語：PHP(サーバー側), Javascript(フロントエンド側), MySQL(8.0)
- フレームワーク：Laravel, jQuery
- インフラ：AWS(  
  EC2, RDS(MySQL), S3, VPN(Site-to-Site VPN),  
  CodePipeline, CodeDeploy,  
  IAM,  
  Config, SSM,  
  Chatbot  
  )

### チーム情報
- PdM 1名、PL 1名、開発者4名、インフラ1名  
→インフラ構築を担当

### 担当業務
- インフラ（AWS）構成の構築  
  ※上記「使用技術」で使用しているAWSサービスは全て自分で設定・構築している
- VPN構築  
  システムに対してインターネットではなくVPN（顧客管轄）経由でアクセスできるようにした。  
  また、顧客側で運用しているネットワーク構成（サブネット状況など）を踏まえ、  
  AWS側のVPC（サブネットのCIDR）を設計・構築した。
- CD実装（  
  GitHubへのPushを起点に所定のサーバーに自動でデプロイされるよう設定。  
  ※本番（商用）環境についてはPdM、PLによる承認プロセスを通過しないとデプロイされない。  
  ChatbotでCDの結果をSlackで通知するよう設定。  
  ）  
  →デプロイ作業の属人化の防止、開発チームの作業負荷を減らすため

### どんな課題があったのか
- 顧客が扱っている業務システムのデモ機の貸出状況の確認が困難になっていた。  
  ＜背景＞  
  当顧客はデモ機の貸出状況をExcelを使って管理していた。  
  運用していくうちに貸出状況の確認や検索がやりにくいことや人によっては貸出状況の書き方が変わったり、  
  そもそも更新する箇所を誤ってしまったりなど運用においてさまざまな課題が出ていた。

### どうやって課題を解決したのか
- デモ機の貸出状況をWebシステムで可視化、貸出状況をガントチャートやカンバンなど  
  様々な形式で可視化できるようにした。  
  また、他人が扱っているデモ機情報を誤って更新してしまう事態を防ぐためにデモ機の貸出イベントごとに  
  「誰が貸し出したのか」を設定することでその貸出イベントの作成者以外は貸出状況を更新することができないようにした。

### 工夫した点
- NATインスタンスの構築
  セキュリティ要件のためにWeb/APサーバーをプライベートサブネットに配置していたが、  
  ライブラリのインストールや更新などインターネットに接続する必要があった。  
  NATGatewayだと当プロジェクトの規模に対して料金が割高になってしまうため  
  最安でNATを実現できるNATインスタンスを構築した。  
- サーバー構築の自動化（オートスケーリング有効化）  
  ミドルウェアなど必要な環境を構築するスクリプトを作成してuserdata（EC2起動時実行スクリプト）を  
  使って環境構築を自動化した。
- ‘ログの可視化
  CloudWatchLogsを使って重要ログ（Laravel、nginx、MySQLのログ）をGUIで確認できるようにした。  
  ※Laravelのアプリログについては重要度の高いエラー（emergency、alert、critical）のログが  
  出た際はSlackで通知するよう設定。
  （CloudWatch→SNS→Chatbot）
