# AWSを使ってみよう

## **概要**

Amazonが提供しているクラウドコンピューティングサービスであるAWS(AmazonWebService)を利用してクラウドにwebサービスをデプロイしましょう！

## **要確認：AWSコンソール画面について**

AWSのコンソール画面は頻繁にアップデートされております。

これからのchapter内にある、設定時の用語など別の文言に変わっている可能性がございます。

もし変わっている部分がありましたら、運営へ共有いただければと思います。

## **AWSアカウント作成**

※アカウント作成の際にクレジットカード情報が必要なので準備しておきましょう(デビットカードも可)！

公式に丁寧な手順書があるので、これに従い作成してください！

完了後は画面に従いAWSマネジメントコンソール画面(以下**コンソール画面**)を開き、次の項目(ルートアカウントの注意点)に進んでください！

[AWS アカウント作成の流れ](https://aws.amazon.com/jp/register-flow/)

## **ルートアカウントの注意点**

アカウントを作成できたと思います！

これでAWSのクラウドサービスを利用できますが、今ログインしているアカウントは**ルートアカウント**というすべてのサービスの操作が可能であり、またアクセス権限を変更することができません。

そのため通常はこのルートアカウントは保護し、利用するサービスへの制限を定義したユーザーを作成してそのユーザーアカウントでAWSを利用していきます。

そのためのサービスが次で作業するIAMになります。

## **IAMユーザー作成**

IAMでユーザー作成するために、所属するグループの定義から始めます。

### **IAMグループ作成**

コンソール画面の上部にある検索フォームからIAMを検索して、IAMの画面へ移動します。

左メニュー内の「個のユーザーグループ」から「グループ作成」に進んでください。

下記内容で作成してください。

- グループ名：任意のものでOK
- アクセス許可ポリシーをアタッチ：検索窓で「AdministratorAccess」を検索しチェック

### **IAMユーザー作成**

上記と同じく、IAMのコンソール画面の左メニュー内の「ユーザー」から「ユーザーを追加」に進んでください。

下記内容でユーザを作成します。

- ユーザ名：任意のものでOK
- AWS 認証情報タイプを選択：パスワード - AWSマネジメントコンソールへのアクセス
- コンソールのパスワード：カスタムパスワード、任意のパスワードを入力してください
- パスワードリセットが必要：チェックなし
- アクセス権限：先ほど作成したIAMグループにチェック
- タグの追加：なし

作成が終わったら作成したIAMユーザーでコンソール画面にログインできるか確認してみましょう！

この段階で「rootアカウント」「作成したIAMユーザーアカウント」の2つがあるかと思います。

基本的にAWS上での作業はrootアカウントは利用せずに、新しく作成したIAMユーザーアカウントを利用して進めていきます。

## **budgets設定**

※budgets設定はAWSの権限上、rootアカウントを利用して進めます。

AWSは無料枠で利用できるクラウドサービスもありますが、基本的には有料のサービスになっております。

そのため意図しない課金が出ないように予算のアラート設定をしましょう。

コンソール画面の検索窓から「budget」と検索して「AWS Budgets」のサービスを開き、「予算を作成」に進みます。

下記の内容で作成していきます。

- 予算タイプを選択
    - 予算タイプ：コスト予算
- 詳細
    - 予算名：任意の名前
- 予算額を設定(ここは任意のものでも大丈夫です)
    - 間隔：月別
    - 予算有効日：定期予算
    - 開始月：現在の年月
    - 予算設定方法：固定
    - 予算額：10.00
    - 予算のスコープ：すべてデフォルト設定でokです
- アラートの設定
    - 閾値：任意の%
    - トリガー：実績
    - Eメールの受信者：受信するメールアドレス
- アクションのアタッチ
    - そのまま