# docker-recorder

Dockerfile for EPGStation + FFmpeg + JoinLogoScpTrialSet

## イメージについて

このイメージは、ベースとなっているEPGStationやFFmpegのイメージに更新があるたびに自動でビルドされます。
その影響で、latestタグのみしか提供することが出来ません。
latestタグの更新はDocker Hub次第です。

## 動作環境

動作確認は以下の環境で行っています(ただしバージョンは適当です)。

似ている環境なら恐らく動作しますが、動作を保証するものではありません。
環境が多少違ってもIssue及びPRは受け付けます。

### ソフト

* Ubuntu Server 20.04.2 (on ESXi 6.7)
* Docker CE 20.10.5
* docker-compose 1.28.4

## docker-compose.ymlについて

動作確認用です。実運用では自前のdocker-compose.ymlを作成することを推奨します。
