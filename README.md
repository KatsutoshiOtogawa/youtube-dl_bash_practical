# youtube-dl_practical


```
# debian系の場合はあらかじめ下のコマンドのインストール、aliasの実行をしておくこと。
# これを行うことによりmacと同じ感覚でクリップボード↔️標準入出力と操作できる様になります。
sudo apt install -y xsel
echo "alias pbcopy='xsel --clipboard --input'" >> $HOME/.bash_profile
echo "alias pbpaste='xsel --clipboard --output'" >> $HOME/.bash_profile

# クリックボードからurlだけを処理。
pbpaste | python3 << END | youtube-dl
# this pyhton script is filtering url.
from urllib.parse import urlparse
import sys
for line in sys.stdin:
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
import sys
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
