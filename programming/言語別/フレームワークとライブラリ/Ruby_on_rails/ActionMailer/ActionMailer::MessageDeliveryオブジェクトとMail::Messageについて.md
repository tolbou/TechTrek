## ActionMailer::MessageDeliveryオブジェクトとMail::Messageについて

Ruby on Railsにおける**`ActionMailer`**は、アプリケーションからメールを送信するためのフレームワークです。**`ActionMailer`**を使用してメールを送信するとき、主に関わってくるのが**`ActionMailer::MessageDelivery`**オブジェクトと**`Mail::Message`**オブジェクトです。

### **ActionMailer::MessageDeliveryオブジェクト**

- **概要**: **`ActionMailer::MessageDelivery`**は、**`ActionMailer`**からメールを送信するためのメソッド(**`deliver_now`**、**`deliver_later`**など)を提供するラッパーオブジェクトです。**`ActionMailer`**のメーラークラスでメール送信のためのメソッドを呼び出した時（例えば**`UserMailer.welcome_email(user)`**）、実際にはこの**`ActionMailer::MessageDelivery`**オブジェクトが返されます。このオブジェクトを介して、メールの送信時にどのように送信するか（即時送信するか、バックグラウンドで送信するかなど）を選択できます。
- **主な使い方**: **`deliver_now`**メソッドや**`deliver_later`**メソッドを呼び出してメールを送信します。

### **Mail::Messageオブジェクト**

- **概要**: **`Mail::Message`**は、メールそのものを表すオブジェクトで、メールの内容（宛先、件名、本文など）を保持します。**`ActionMailer::MessageDelivery`**オブジェクトから**`message`**メソッドを呼び出すことでアクセス可能です。**`Mail::Message`**オブジェクトを直接操作することで、メールの詳細な設定やカスタマイズを行うことができます。
- **主な使い方**: 通常は**`ActionMailer`**のビューやメーラークラス内でメールの内容を定義しますが、特定の条件下でメールのヘッダーを動的に変更するなどのカスタマイズが必要な場合に直接操作します。

### **使い分け**

- **`ActionMailer::MessageDelivery`オブジェクト**: メール送信の流れを制御するために使用します。具体的には、メールを即時送信するか、後で送信するか（非同期処理）、送信をキャンセルするかといった、メール送信の「いつ」「どのように」に関する操作に使います。
- **`Mail::Message`オブジェクト**: メールの具体的な内容（宛先、件名、本文など）や、メールのヘッダーなど、メールそのものの詳細な設定に関わる操作に使います。**`ActionMailer`**のテンプレートやメーラークラスで設定できないような特殊なカスタマイズが必要な場合に直接操作することがあります。

### **実際の使用例**

```ruby
rubyCopy code
# MessageDeliveryオブジェクトを取得し、即時にメールを送信
mail_delivery = UserMailer.welcome_email(user)
mail_delivery.deliver_now

# MessageDeliveryオブジェクトからMail::Messageオブジェクトにアクセス
mail_message = mail_delivery.message
puts mail_message.subject # メールの件名を出力

```

このように、**`ActionMailer::MessageDelivery`**オブジェクトと**`Mail::Message`**オブジェクトを理解し、適切に使い分けることで、Railsアプリケーションからのメール送信を柔軟に制御できるようになります。