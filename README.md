# AppInsightsQuery-TraceLogs
Application Insightsの分析で使えるサンプルクエリ集です。ここで、紹介するのはログトレースの際に使うことが想定されるクエリです。

# はじめに
Application Insightsが取得したボットのメッセージ(ユーザとボットのやり取り)は、JSON形式で保存されます。

<a href="https://imgur.com/Yxbz223"><img src="https://i.imgur.com/Yxbz223.png" title="source: imgur.com" /></a>

それぞれのデータにはキーが紐づいています。また、Application Insights側で用意されているキーを画面左側で確認できます。

<a href="https://imgur.com/XxQo978"><img src="https://i.imgur.com/XxQo978.png" title="source: imgur.com" /></a>

これらを用いて必要な情報を取り出し、表やグラフを作成します。

# 時刻・クライアント名・ユーザー名・メッセージの表

以下のクエリを実行すると、時刻・クライアント名・ユーザー名・メッセージの表を作成することが出来ます。

```
traces
| where severityLevel == 1 and client_City != "Des Moines" 
| extend Dialog = parse_json(message) 
| extend User = parse_json(Dialog.from) 
| project timestamp, Dialog.channelId, User.name, Dialog.text
```
※Application Insightsでトレースされるメッセージの中には、警告メッセージなど、分析に不要なものも含まれてしまうため、where文でフィルタをかけています。

※projectの行に書いてあるキーを並び替えることで、表の列の並びを変えることが出来ます。

<a href="https://imgur.com/KNv7gXQ"><img src="https://i.imgur.com/KNv7gXQ.png" title="source: imgur.com" /></a>

※Application Insightsにおいて、デフォルトで表示される時刻は協定世界時(UTC)になっているため、ローカルタイム(日本時間)に変換したい場合には、**Display Time**を変更してください。また、時間の並びの昇べきor降べきを変更する際には、列の先頭にある**timestamp**を押してください。

<a href="https://imgur.com/6xaMKJI"><img src="https://i.imgur.com/6xaMKJI.png" title="source: imgur.com" /></a>

# ログトレースの期間

〇10日間分のログを取得する際には、以下のようなクエリを追加します。

```
| where  timestamp > ago(10d) 
```

※( )の中には、(10h)など時間を挿入することもできます。

〇今週1週間分のログを取得する際には、以下のようなクエリを追加します。

```
| where timestamp > startofweek(now())
```

〇日付の期間を設定してログを取得する際には、以下のようなクエリを追加します。

```
| where timestamp > startofday(datetime("2018-08-01")) and timestamp < endofday(datetime("2018-08-18"))
```

〇日付+時間の期間を設定してログを取得する際には、以下のようなクエリを追加します。

```
| where  timestamp > datetime(2018-08-11 00:00:00) and timestamp <= datetime(2018-08-15 00:00:00)
```

# チャート・グラフ

〇ユーザによって使われたクライアントを円グラフで表示したい場合、以下のようなクエリを追加します。

```
traces
 | where severityLevel == 1 and client_City != "Des Moines" 
 | extend Dialog = parse_json(message)
 | summarize count() by tostring(Dialog.channelId)
 | top 10 by count_ 
 | render piechart
```

<a href="https://imgur.com/9gMNOz6"><img src="https://i.imgur.com/9gMNOz6.png" title="source: imgur.com" /></a>

※グラフの種類は、GUIで変更することも可能です(Pieと書かれているボタン)
