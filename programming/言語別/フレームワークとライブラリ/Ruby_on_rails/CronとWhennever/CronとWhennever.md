## CronとWhennever

### **Cron**

CronはUNIX系オペレーティングシステムで広く使用されている、時間ベースのジョブスケジューラーです。Cronを使用すると、スクリプトやコマンドを予定された時間や定期的な間隔で自動的に実行することができます。Cronジョブは、**`crontab`**（Cron table）ファイルに設定され、システムが定期的にこのファイルをチェックして、スケジュールに従ってジョブを実行します。

Cronの設定は、特定の分、時、日、月、曜日を指定する5つのフィールドで構成されます。例えば、毎日午前3時にスクリプトを実行するには、以下のようなCron設定を使用します：

```jsx

0 3 * * * /path/to/script.sh

```

### **Whenever**

Wheneverは、Rubyのgemで、Cronジョブを管理するためのより直感的な方法を提供します。Ruby on Railsアプリケーションでよく使用されますが、Rails以外のプロジェクトでも利用可能です。Wheneverを使用すると、Rubyの構文を使用してCronのスケジュールを定義できるため、Cron設定の記述がはるかに読みやすく、書きやすくなります。

Wheneverは内部でCronの構文に変換されるため、Cronがインストールされているシステムであればどこでも使用できます。Wheneverの設定は、**`config/schedule.rb`**ファイルに記述します。

例えば、毎日午前3時にRailsタスクを実行するWheneverの設定は、以下のようになります：

```ruby

every :day, at: '3:00 am' do
  rake "my_task:run"
end

```

Wheneverは、このような設定をCronの形式に変換し、システムのcrontabに自動的に追加します。

### **使用上の注意**

- Wheneverを使用するには、まずGemfileに**`whenever`**を追加し、**`bundle install`**を実行してインストールする必要があります。
- スケジュールを定義した後、**`whenever --update-crontab`**コマンドを実行して、Wheneverのスケジュールをシステムのcrontabに適用します。
- Wheneverは、スケジュールの管理を簡単にするためのツールですが、実際にジョブを実行するのはCronです。したがって、Wheneverを使用するシステムにはCronがインストールされている必要があります。

Wheneverは、Cronジョブの管理をより簡単かつ効率的にするための強力なツールです。Rubyの構文を使用して直感的にジョブをスケジュールできるため、RubyまたはRailsのプロジェクトでCronジョブを使用する場合に特に便利です。


whemeverのコマンド解説
wheneverでは次の4つをスケジューリングできます。


command: bashコマンド実行
rake: rakeタスク実行
runner: Rails内のメソッド実行
script: scriptの実行
crontabを実行
crontabを使ってcronに対して命令をします。


$ bundle exec whenever --update-crontab   # wheneverの設定更新

$ bundle exec whenever  # 設定内容にエラーが無いか確認

$ crontab -l  # 設定されているcronを確認

$ bundle exec whenever --clear-crontab  # crontabの設定を削除
