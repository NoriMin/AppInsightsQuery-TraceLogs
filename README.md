# AppInsightsQuery-TraceLogs
Application Insightsの分析で使えるサンプルクエリ集です。ここで、紹介するのはログトレースの際に使うことが想定されるクエリです。

# はじめに
Application Insightsが取得したボットのメッセージ(ユーザとボットのやり取り)は、JSON形式で保存されます。

<a href="https://imgur.com/Yxbz223"><img src="https://i.imgur.com/Yxbz223.png" title="source: imgur.com" /></a>

それぞれのデータにはキーが紐づいています。また、Application Insights側で用意されているキーを画面左側で確認できます。

<a href="https://imgur.com/XzYua5y"><img src="https://i.imgur.com/XzYua5y.png" title="source: imgur.com" /></a>

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

