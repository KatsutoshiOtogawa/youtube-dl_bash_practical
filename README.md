# youtube-dl_practical

## pyhton
```
# debian系の場合はあらかじめ下のコマンドのインストール、aliasの実行をしておくこと。
# これを行うことによりmacと同じ感覚でクリップボード↔️標準入出力と操作できる様になります。
sudo apt install -y xsel
echo "alias pbcopy='xsel --clipboard --input'" >> $HOME/.bash_profile
echo "alias pbpaste='xsel --clipboard --output'" >> $HOME/.bash_profile

# pythonからクリップボードの中身を操作するために
# pyperclipをインストール
pip3 install pyperclip

# クリックボードからurlだけを処理。
waittime=10
python3 << END | sed '/^$/d' | xargs -n 1 -I {} bash -c "sleep $waittime && youtube-dl {}"
# this pyhton script is filtering url.
from urllib.parse import urlparse
import pyperclip
# クリップボードの中身を取得
for line in pyperclip.paste():
    url = line
    result = urlparse(url)
    # if url validate error, these valiables are empty.
    if result.scheme != '' and  result.netloc != '':
        print(url)
END
```

ファイルから読み取る場合
```
waittime=10
python3 << END | sed '/^$/d' | xargs -n 1 -I {} bash -c "sleep $waittime && youtube-dl {}"
# this pyhton script is filtering url.
from urllib.parse import urlparse
# list.txtがurlのリスト
with open('list.txt') as f:
    for line in f.readlines():
        url = line
        result = urlparse(url)
        # if url validate error, these valiables are empty.
        if result.scheme != '' and  result.netloc != '':
            print(url)
END
```
## nodejs

ファイルから読み取る場合
```
waittime=10
node << END | xargs -n 1 -I {} bash -c "sleep $waittime && youtube-dl {}"
const fs = require("fs");
const readline = require("readline");
const URL = require("url").URL;
  
const stream = fs.createReadStream("list.txt", {
                  encoding: "utf8",         // 文字コード
                  highWaterMark: 1024       // 一度に取得するbyte数
             });

const reader = readline.createInterface({ input: stream });

reader.on("line", (row) => {

    try {
        new URL(row);
        console.log(row);
    } catch (err) {
        // URLインスタンス失敗の時はurlとしての形式が間違っているので出力しない。
    }
});

END
```

## groovy
ファイルから読み取る場合
```
# コマンドラインからヒアドキュメントで実行できる様になって欲しいな😇
waittime=10
groovy -e "
new File('list.txt').eachLine('UTF-8') { 
    try {
    
        new URL(it).openConnection().connect()
        println(it)
    } catch (MalformedURLException e) {
            // the URL is not in a valid form. this
    } catch (IOException e) {
            // the connection couldn't be established
    }
}
" | xargs -n 1 -I {} bash -c "sleep $waittime && youtube-dl {}"
```
## 結論
以外にもpythonが煩雑になり、groovyはネット上に例が少ないがシンプルで見やすくなかなか良い書き方。
