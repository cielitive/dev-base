## *- cheat sheet -*

### パターンスペースを表示する

#### 1. 行番号で表示範囲を指定し表示する

```bash
$ sed -n '1p' source.txt 
# aaa

$ sed -n '1,2p' source.txt 
# aaa
aaa01,aaa02,aaa03
```

```bash
### テストデータ
$ cat source.txt
# aaa
aaa01,aaa02,aaa03

# bbb
bbb01,bbb02,bbb03

# ccc
ccc01,ccc02,ccc03
```

#### 2. 特定の文字列を含む行を表示する

```bash
$ sed -n '/aaa/p' source.txt 
# aaa
aaa01,aaa02,aaa03

$ grep 'aaa' source.txt 
# aaa
aaa01,aaa02,aaa03
```

```bash
### 検索文字列を含む行+n行表示する場合
$ sed '/aaa/,+1p' source.txt

$ grep -A1 'aaa' source.txt
```

#### 3. 開始文字列から終了文字列までの行を表示する

```bash
### 開始文字列が含まれる最初の行から、終了文字列が含まれる最初の行まで
$ sed -n '/aaa/,/bbb/p' source.txt 
# aaa
aaa01,aaa02,aaa03

# bbb
```

#### 4. 処理対象行の行番号を表示する

```bash
$ sed -n '/aaa/=' source.txt 
1
2
```

### パターンスペースを削除する

#### 1. 行番号で削除範囲を指定する

```bash
### 1行目を削除
$ sed '1d' source.txt
aaa01,aaa02,aaa03

# bbb
bbb01,bbb02,bbb03

# ccc
ccc01,ccc02,ccc03
```

```bash
### 4~6行目を削除
$ sed '4,6d' source.txt
# aaa
aaa01,aaa02,aaa03

# ccc
ccc01,ccc02,ccc03
```

#### 2. 最終行を削除する

```bash
$ sed '$d' source.txt 
# aaa
aaa01,aaa02,aaa03

# bbb
bbb01,bbb02,bbb03

# ccc
```

#### 3. 特定の文字列を含む行を削除する

```bash
### "aaa"を含む行を削除
### -e: コマンドに追加するコマンド(=置換条件など), 省略可能
$ sed '/aaa/d' source.txt

# bbb
bbb01,bbb02,bbb03

# ccc
ccc01,ccc02,ccc03
```

```bash
### "#"で始まるコメント行を削除
$ sed '/^#/d' source.txt
aaa01,aaa02,aaa03

bbb01,bbb02,bbb03

ccc01,ccc02,ccc03
```

```bash
### 空行を削除する
$ sed '/^$/d' source.txt 
# aaa
aaa01,aaa02,aaa03
# bbb
bbb01,bbb02,bbb03
# ccc
ccc01,ccc02,ccc03
```

```bash
### コメント行と空行を削除する
### -E: 拡張正規表現
$ sed -E '/(^$|^#)/d' source.txt
aaa01,aaa02,aaa03
bbb01,bbb02,bbb03
ccc01,ccc02,ccc03

### 行頭にスペースがある場合
$ sed -E '/^(#|[ \t]*#|$)/d' source.txt
```

### パターンスペースで条件に合った文字列を置換する

#### 1. 特定の文字列を置換する

```bash
### "aaa"を"xxx"に置換
### (s): 置換, (g): すべてを対象
$ sed 's/aaa/xxx/g' source.txt 
# xxx
xxx01,xxx02,xxx03

# bbb
bbb01,bbb02,bbb03

# ccc
ccc01,ccc02,ccc03

### (i): 大文字小文字をくべつしない
$ sed 's/aaa/xxx/gi' source.txt

### -n: 置換した行のみ表示
sed -n 's/aaa/xxx/g' source.txt
```

```bash
### 複数の対象文字列を置換
 sed -E 's/01|02|03/04/g' source.txt
# aaa
aaa04,aaa04,aaa04

# bbb
bbb04,bbb04,bbb04

# ccc
ccc04,ccc04,ccc04
```

```bash
### 複数回の置換
$ sed -e 's/aaa/bbb/g' -e 's/bbb/xxx/g' source.txt
or
$ sed 's/aaa/bbb/g;s/bbb/xxx/g' source.txt 
# xxx
xxx01,xxx02,xxx03

# xxx
xxx01,xxx02,xxx03

# ccc
ccc01,ccc02,ccc03
```

#### 2. n回目の置換対象文字列のみ置換する

```bash
### (行の)2回目の対象文字列を置換
$ sed 's/aaa/xxx/2' source.txt
# aaa
aaa01,xxx02,aaa03

# bbb
bbb01,bbb02,bbb03

# ccc
ccc01,ccc02,ccc03
```

#### 3. 特定の文字列を含む行のみを対象とし置換する

```bash
### "aaa"を含む行のみ"01"を"04"に置換
$ sed '/aaa/s/01/04/g' source.txt 
# aaa
aaa04,aaa02,aaa03

# bbb
bbb01,bbb02,bbb03

# ccc
ccc01,ccc02,ccc03
```

```bash
### 特定の文字列を含まない行のみ置換
$ sed '/aaa/!s/01/04/g' source.txt
# aaa
aaa01,aaa02,aaa03

# bbb
bbb04,bbb02,bbb03

# ccc
ccc04,ccc02,ccc03
```

#### 4. 置換対象の行(範囲)を行番号で指定して置換する

```bash
### 指定行の間で置換
$ sed '4,6 s/bbb/zzz/g' source.txt 
# aaa
aaa01,aaa02,aaa03

# zzz
zzz01,zzz02,zzz03

# ccc
ccc01,ccc02,ccc03
```

#### 5. 置換対象の行(範囲)を文字列で指定して置換する

```bash
### 指定した開始、終了行も置換対象
$ sed '/# bbb/,/# ccc/ s/01/04/g' source.txt
# aaa
aaa01,aaa02,aaa03

# bbb
bbb04,bbb02,bbb03

# ccc
ccc01,ccc02,ccc03
```

#### 6. 対象文字列を大文字・小文字に置換する

```bash
### 大文字に置換
$ sed -E 's/.+/\U\0/g' source.txt

### 小文字に置換
$ sed -E 's/.+/\L\0/g' source.txt
```

#### 7. 空行・空白(タブ、スペース)を置換する

```bash
### タブをスペースに変換
$ sed -e 's/<tab>/<space>/g' source.txt
```

```bash
### 複数のスペースを1つのスペースに変換
$ sed -e 's/<space><space>*/<space>/g' source.txt

### すべてのホワイトスペースを1つのスペースに変換
$ sed -e 's/[<space><tab>][<space><tab>]*/<space>/g' source.txt
```

#### . キーと値を入れ替える

```bash
$ cat source.yml 
key01: val01
key02: val02
key03: val03

$ sed -E 's/^(.*): (.*)/\2: \1/g' source.yml 
val01: key01
val02: key02
val03: key03
```

### テキスト(行)を追加する

#### 1. 特定の行（２行目）に追加する

```bash
$ sed -e '2i /usr/local/bin' /etc/shells
/bin/sh
/usr/local/bin
/bin/bash
/usr/bin/sh
/usr/bin/bash
```

#### 2. 特定の行（２行目の下）に追加する

```bash
$ sed -e '2a /usr/local/bin' /etc/shells
/bin/sh
/bin/bash
/usr/local/bin
/usr/bin/sh
/usr/bin/bash
```

#### 3. 特定の行（"/usr/bin/sh"の下）に追加する

```bash
$ sed -e '/\/usr\/bin\/sh/a /usr/local/bin' /etc/shells
/bin/sh
/bin/bash
/usr/bin/sh
/usr/local/bin
/usr/bin/bash
```

#### 4. 特定の行（"/usr/bin/sh"の下）に複数行追加する

```bash
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

## *- tips -*

#### 1. TOMLファイルのテーブル配下の値を表示する

```bash
$ table=aaa; cat source.toml | sed -n -E "/\[$table\]/,/^$|^\[/p" | sed '1d;$d'
aaa01
aaa02
aaa03
```

```bash
### テストデータ
$ cat source.toml 
[aaa]
aaa01
aaa02
aaa03

[bbb]
bbb01
bbb02
bbb03

[ccc]
ccc01
ccc02
ccc03
```
