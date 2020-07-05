# Mastodon
Mastodonは[ActivityPub](https://www.w3.org/TR/activitypub/)の[Server to Server Interactions](https://www.w3.org/TR/activitypub/#server-to-server-interactions)に準拠した分散ソーシャルネットワークサービスのひとつです。分散ソーシャルネットワークサービスでは、ユーザーを代理して、ユーザーがログインしていない場面で、サーバどうしが投稿をやりとりしますので、[投稿内容を検証しなりすましを防ぐ必要](https://www.w3.org/TR/activitypub/#obj)があります。Mastodonの詳細については、[紹介サイト](https://joinmastodon.org/)(右上のメニューから日本語にできます)を参照してください。

## HTTP署名した投稿の配送
Mastodonでは、アカウントの作成時にOpenPGP鍵を生成しておき、他のサーバに投稿を配達する際に投稿元のアカウントの私有鍵で投稿内容に電子署名します。このHTTP署名については、[IETFで標準化に向けて議論が続けられています](https://www.ietf.org/archive/id/draft-cavage-http-signatures-12.txt)。配達先のサーバは、投稿元のアカウントの公開鍵を投稿元のサーバから取得することで、投稿内容の正当性を検証します。

## ダイレクトメッセージのエンドツーエンド暗号化
開発版のMastodonでは[ダイレクトメッセージをエンドツーエンド暗号化するAPI](https://github.com/tootsuite/mastodon/pull/13820)が利用可能です。このAPIは[Matrixのエンドツーエンド暗号化](https://matrix.org/docs/guides/end-to-end-encryption-implementation-guide)に似た手順エンドツーエンド暗号化を実現するもので、クライアントデバイスは下記の手順でエンドツーエンド暗号したダイレクトメッセージを送信します。サーバに知られることなく共通鍵を算出することで、サーバにダイレクトメッセージの内容を知られることなく、クライアントデバイスどうしでのダイレクトメッセージをやりとりします。

1. クライアントデバイスで電子署名用と共通鍵生成用の2ペアの鍵対を生成し2つの公開鍵をサーバにアップロードしておく。また、いくつかのワンタイム鍵対を生成し公開鍵をサーバにアップロードしておく
1. ダイレクトメッセージの送信先のユーザーのクライアントデバイスのリストを公開鍵と共に取得する
1. 今回のセッションのためにダイレクトメッセージの送信先のクライアントデバイスのワンタイム鍵を取得する
1. 今回のセッションのためにワンタイム鍵を生成する
1. 取得した送信先のワンタイム鍵・手元で生成したワンタイム鍵・送信先と手元の共通鍵生成用から、今回のセッションのために共通鍵を算出する
1. 生成した共通鍵で暗号化したダイレクトメッセージをサーバ経由で送付する