## *Linux Tips and Tricks*

##### 1. IPアドレスから地域を特定する

```bash
$ ip="142.250.185.164"
$ curl -s ipinfo.io/${ip} | jq
{
  "ip": "142.250.185.164",
  "hostname": "fra16s51-in-f4.1e100.net",
  "city": "Mountain View",
  "region": "California",
  "country": "US",
  "loc": "38.0088,-122.1175",
  "org": "AS15169 Google LLC",
  "postal": "94043",
  "timezone": "America/Los_Angeles",
  "readme": "https://ipinfo.io/missingauth"
}

$ curl -s ip-api.com/json/${ip} | jq
{
  "status": "success",
  "country": "Germany",
  "countryCode": "DE",
  "region": "HE",
  "regionName": "Hesse",
  "city": "Frankfurt am Main",
  "zip": "60313",
  "lat": 50.1109,
  "lon": 8.68213,
  "timezone": "Europe/Berlin",
  "isp": "Google LLC",
  "org": "Google LLC",
  "as": "AS15169 Google LLC",
  "query": "142.250.185.164"
}
```
---
 
##### 2. 特定のポートをリッスンする

```bash
### 2.7
$ python -m SimpleHTTPServer 8000

### 3.x
$ python -m http.server 8000
```

## *Shell Script Tips and Tricks*

##### 1. エラーハンドリング

```bash
set -eu -o pipefail
IFS=$'\n\t'

### "||"を使う場合
set +e
command1 || command2
set -e
```

```bash
### 標準出力
echo "stdout" >&1
### 標準エラー出力
echo "stderr" >&2

### 標準エラー出力のみファイルにリダイレクト
$ command 2> file

### 標準出力 & 標準エラー出力を出力しない
$ command >& /dev/null
$ command &> /dev/null
$ command > /dev/null 2>&1
$ command 1> /dev/null 2> /dev/null
```

#### 2. 変数

```bash
### デフォルト値
###
$ echo ${HOGE:-HELLO}

###
$ echo ${HOGE:+HELLO}

### 置換
$ HOGE=abcdabcd
$ echo ${HOGE}
abcdabcd

### 最初のマッチのみ置換
$ echo ${HOGE/bc/xx}
axxdabcd

### 全てのマッチを置換
$ echo ${HOGE//bc/xx}
axxdaxxd

### 大文字に変換
$ HOGE=abcdABCD
$ echo ${HOGE^^}
ABCDABCD

### 小文字に変換
$ echo ${HOGE,,}
abcdabcd

### 変数のパターン一致による除外
### 前から検索
$ HOGE=/home/user/hoge
$ echo ${HOGE}
/home/user/hoge

### 最短除外
$ echo ${HOGE#*/}
home/user/hoge

#### 最長除外
$ echo ${HOGE##*/}
hoge

### 後ろから検索
$ HOGE=hoge.tar.bz2
$ echo ${HOGE}
hoge.tar.bz2

### 最短除外
$ echo ${HOGE%.*}
hoge.tar

### 最長除外
$ echo ${HOGE%%.*}
hoge
```

#### 3. 繰り返し処理

```bash
### -r: バックスラッシュ(\)によるクォートを抑止する
$ cat text.file | while IFS=":" read -r x y z
do
  echo "x: ${x}, y: ${y}, z: ${z}"
done
```

#### 4. ファイル検索・操作

```bash
### "uid"と"gid"を指定したファイル検索
$ find . \( -uid <uid> -o -gid <gid> \)

### 検索したファイルの所有者とグループを変更する
$ find <path> -user <uid> -exec chown ${user}:${group} {} \;
```

#### 5. awk

```bash
### 置換
$ cat /etc/shells 
/bin/sh
/bin/bash
/usr/bin/sh
/usr/bin/bash

### 最初のマッチのみ置換
$ cat /etc/shells | awk -F"/" /usr/'{sub("s", "S"); print}'
/uSr/bin/sh
/uSr/bin/bash

### すべてのマッチを置換
$ cat /etc/shells | awk -F"/" /usr/'{gsub("s", "S"); print}'
/uSr/bin/Sh
/uSr/bin/baSh

### 特定の文字を切り出す
$ cat /etc/shells | awk -F"/" /usr/'{str=substr($0, index($0, "bin"), 3); print str}'
bin
bin

### 特定の行以降を表示
$ cat /etc/shells | awk 'NR>3{print $0}'
/usr/bin/bash
```

#### 6. sed

```bash
### 特定の行（２行目）に追加する
$ cat /etc/shells | sed -e '2i /usr/local/bin'
/bin/sh
/usr/local/bin
/bin/bash
/usr/bin/sh
/usr/bin/bash

### 特定の行（２行目の下）に追加する
$ cat /etc/shells | sed -e '2a /usr/local/bin'
/bin/sh
/bin/bash
/usr/local/bin
/usr/bin/sh
/usr/bin/bash

### 特定の行（"/usr/bin/sh"の下）に追加する
$ cat /etc/shells | sed -e '/\/usr\/bin\/sh/a /usr/local/bin'
/bin/sh
/bin/bash
/usr/bin/sh
/usr/local/bin
/usr/bin/bash

### 特定の行（"/usr/bin/sh"の下）に複数行追加する
cat <<EOT | sed -e '/\/usr\/bin\/sh/r /dev/stdin' /etc/shells
/usr/local/bin
/root/.go/bin
EOT
/bin/sh
/bin/bash
/usr/bin/sh
/usr/local/bin
/root/.go/bin
/usr/bin/bash
```

## *Linux Performance Checklist*

[> システムのパフォーマンス分析方法](https://esa-pages.io/p/sharing/14387/posts/20/dfb77c259d31542e4177.html "")

## *Git Tips and Tricks*

[> Gitの使い方](https://esa-pages.io/p/sharing/14387/posts/6/95c231dd3af023267589.html "")
