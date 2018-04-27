# API v3のリリースについて

LiftIgniterの/modelおよび/activityのエンドポイントにおいて、新バーションのリリースを行いました。この変更により、modelサーバへのリクエストやmodelサーバ内のトラフィック処理をより効率的に行うことが可能になります。現在これらのエンドポイントをご利用中の場合は、新バージョンへの移行をお勧めします。なお、これまでの旧バージョン（v2）のエンドポイントの提供は2018年6月1日をもって終了させていただきます。

このバージョンアップにおいて必要な変更は、URLパスにJavascriptキーとuserIdを含め、ヘッダにAPIキーを追加するだけです。リクエストのJSON構文における変更は、apiKeyとuserIdのパラメータを削除するのみで、その他に大きなものはありません。

新バージョンのドキュメントは下記URLをご参照ください。

### /modelエンドポイント
https://documenter.getpostman.com/view/2166502/liftigniter/7TFGvSV#9bdf75da-edd6-45ec-9c28-a0edefad1389

### /activityエンドポイント
https://documenter.getpostman.com/view/2166502/liftigniter/7TFGvSV#6b9059f8-ec56-46c3-a39b-517763fa110c

##URLパスの変更

### 旧バージョン:
POST    https://query.petametrics.com/v2/model
POST    https://query.petametrics.com/v2/activity

### 新バージョン（v3）:
POST    https://query.petametrics.com/v3/:jsk/:userId/model
POST     https://query.petametrics.com/v3/:jsk/:userId/activity

:jskは、お客様のJavascriptキーです。このキーはLiftIgniterのダッシュボード（Settings > Access Keys）にてご確認いただけます。このキーはAPIキーとは異なりますのでご注意ください。

:userIdは、LiftIgniterのシステム上でのユーザのユニークIDです。/activityエンドポイントにデータを送っている場合は、お客様側で指定されたユニークIDを割り当てていると想定していますが、そうでない場合は、このIDがLiftIgniterのCookieに保存されるユーザIDになります。

### ヘッダへの変更:
リクエストのヘッダに"x-api-key" : "Your API Key"を追加してください。

### JSONへの変更:
JSONの"apiKey"と"userId"はv3では使用しません。これらの値は削除してください。

その他詳細に関するご質問はサポート担当者までお問合わせください。
