# Python チートシート

Pythonは経験が浅いので、いろいろコピペ用に書きなぐり

## 基本構文


### import

ライブラリをインポートする。

#### 一般的な書き方

```Python
import datetime
```
[datetime](https://docs.python.org/ja/3/library/datetime.html)モジュールを使いたいときに書く。
この状態ならモジュールに属するすべてのオブジェクトが使える。

##### 使用例
```Python
year = datetime.MAXYEAR              # 定数へアクセスできる
days = datetime.timedelta(days=7)    # クラスへアクセスできる
```
datetimeモジュールには定義されていないが、グローバル関数があればそれも呼び出すことができる。

#### 識別子を絞り込む

```Python
from datetime import timedelta
```
datetimeモジュールに属する[timedelta](https://docs.python.org/ja/3/library/datetime.html#datetime.timedelta)オブジェクトを使いたいときに書く。


```Python
from datetime import timedelta, timezone
```

カンマ区切りで複数の識別子を指定することができる。

##### 使用例
```Python
days = timedelta(days=7)
```
記載した識別子以外は使えない代わりに、モジュール名がない分だけ短く書くことができる。

#### 別名

```Python
import datetime.timedelta as td
```
これもtimedeltaクラスが使えるが`td`という別名を付けている。

##### 使用例
```Python
days = td(days=7)
```
識別子の重複を避けたい場合や、コードを短く書きたいときに使用する。

#### おまけ

```Python
import datetime
days = datetime.timedelta(days=7)
```

```Python
from datetime import timedelta
days = timedelta(days=7)
```

```Python
import datetime.timedelta as td
days = td(days=7)
```
書き方は違うが、どれもほぼ同じ動作する。


## ライブラリー

### datetime

### SQLite3

### OpenPyXL

### Pandas

### Matplotlib
