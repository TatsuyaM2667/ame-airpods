# ame-airpods

AirPods の頭の動き・電池を Bluetooth(AAP)で読む小さなコマンド(Linux)。

[ame-shell](https://github.com/TatsuyaM2667/ame-shell)(Wayland のデスクトップシェル)の一部を、単独で使えるように切り出したものです。
ame-shell は画面の要らない仕事を、1 つのことをして標準入出力でやりとりする小さなコマンドに分けています。これはその 1 つです。

## 使い方

```text
ame-airpods: AirPods と Apple のアクセサリの通信(AAP、Bluetooth の L2CAP)で話す。Linux のみ。

  ame-airpods find                      接続中の AirPods の「番地 名前」(無ければ終了コード 1)
  ame-airpods battery [-a 番地]          左・右・ケースの電池 {"left":{"level","charging"},"right":…,"case":…}
  ame-airpods head [-a 番地] [--yaw 度] [--pitch 度] [--invert-yaw] [--invert-pitch]
                                        頭の動きを 1 行 1 つの JSON で流し続ける(止めるまで):
    {"event":"status","status":"searching|connecting|running|no_device|failed|rejected","name":"…"}
    {"event":"angles","pitch":度,"yaw":度}   (10 回/秒まで)
    {"event":"gesture","gesture":"right|left|up|down"}   頭を振って戻した・向いて戻した

番地を省くと、接続中の Bluetooth の機器から AAP の UUID(74ec2172-…)を持つものを探す(bluetoothctl)。
head は AirPods が外れても探し直してつなぎ直す。SIGTERM / SIGINT で頭の向きの送信を止めてから終わる。
パケットの読み方と動きの見つけ方は src/lib/aap.zig。ame-shell の AirPods 操作と接続のカードもこれを使う。
```

## ビルド

```sh
zig build -Doptimize=ReleaseSafe   # zig-out/bin/ame-airpods(Zig 0.13、libc だけ)
zig build test
```

実行に要るもの: `bluetoothctl`

## このリポジトリについて

正本は ame-shell の `src/cmd/ame-airpods.zig` と `src/lib/` です。このリポジトリは `scripts/split-repos.sh` が生成して同期しています。
直したいところがあれば ame-shell の方へお願いします。

ライセンス: GPL-3.0(ame-shell と同じ)
