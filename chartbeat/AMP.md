# AMPサイトへのChartbeatコード実装

## 前提条件

1. 既に通常のWebサイトにChartbeatのコード実装が完了していること

2. ChartbeatのUID番号を持っていること

3. WebサイトがAMP対応済み（[Validate AMP pages](https://www.ampproject.org/docs/fundamentals/validate)）であること

## コードの実装

1. AMPページの<head>部内に下記コードを追加する

'''<script async custom-element="amp-analytics" src="https://cdn.ampproject.org/v0/amp-analytics-0.1.js"></script>'''

2. AMPページの</body>タグ前に下記コードを追加する（uidとdomainの値は実装するサイトのUIDとドメイン名に置き換える、Section名やAuthor名も同様にページごとに適宜変更する。ここではJSONで表記する。）

'''<amp-analytics type="chartbeat">
    <script type="application/json">
        {
            "vars": {
                "uid": "12345",
                "domain": "yoursite.com",
                "sections": "section 1, section 2"
                // Author, etc.
            }
        }
    </script>
</amp-analytics>'''

## 追記

Chartbeatのドメイン追加とコードの確認は[https://chartbeat.com/publishing/setup/code](https://chartbeat.com/publishing/setup/code)から。

同じRootドメイン下でサブドメインごとに別サイトとして分析したい場合もここからサブドメインを追加してコードを取得する。
