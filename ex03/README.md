課題3 (2015/6/22)
================

次は，(新たな関数を書くのではなく) [課題2](../ex02) で作った関数に many-to-one マッチング
(college admission のケース) を扱う機能を加えましょう．

* 提出方法：課題2で作ったリポジトリに「プッシュ」する．
* 締め切り：~~6月27日 (土)~~ 7月4日 (土)


## 多対一マッチング

次のような状況に限定することにしましょう：

* 学生は高々一つの大学に入る．
* 大学にはそれぞれ受け入れ上限がある．
* 各大学は (グループではなく) 学生それぞれに対して選好を持つ．
* とりあえず，学生側から提案するということにしましょう．

### 入力データ

学生の数 `m`，大学の数 `n` として，

* `prop_prefs`: `m` x `(n+1)` の2次元配列

* `resp_prefs`: `n` x `(m+1)` の2次元配列

* `caps`: 長さ `n` の1次元配列  
  `caps[j]` は大学 `j` の受入上限

(`prop_prefs`, `resp_prefs` はいままでの `m_prefs`, `f_prefs` と同じ．)

関数は

```python
def deferred_acceptance(prop_prefs, resp_prefs, caps=None):
    ...
```

のように書いて，`deferred_acceptance(prop_prefs, resp_prefs)` のように
`caps` 引数なしで呼び出されたら一対一としていままでと全く同じように処理するようにする．

### 出力データ

とりあえず以下のようなデータ構造を採用することにしましょう．

`caps` が指定されなければいままで通り

* `prop_matched`: 長さ `m` の1次元配列

* `resp_matched`: 長さ `n` の1次元配列

を返す
(`prop_matched`, `resp_matched` はいままでの `m_matched`, `f_matched` と同じ)．

`caps` が指定されたら次の3つを返す：

* `prop_matched`: 長さ `m` の1次元配列

* `resp_matched`: 長さ `L` の1次元配列  
  大学側が受け入れる学生をすべて列挙する．

* `indptr`: 長さ `n+1` の1次元配列  
  `resp_matched[indptr[j]:indptr[j+1]]` が大学 `j` が受け入れる学生のリスト．

ただし，`L` = `sum(caps)` です．

`indptr` は `np.cumsum(caps)` で得られる長さ `n` の1次元配列の先頭に `0` を加えたものです．
たとえば，

```python
indptr = np.empty(n+1, dtype=int)
indptr[0] = 0
np.cumsum(caps, out=indptr[1:])
```

で作ることができます．

### 補足 (2015/7/4)

`caps` あるなしで `indptr` の定義を変える，あるいは `caps` あるなしで
return するもの変えるのは，たとえば次のようにするとできます．

```python
def deferred_acceptance(prop_prefs, resp_prefs, caps=None):
    ...

    if caps is None:
        indptr = np.arange(n+1)
    else:
        indptr = np.empty(n+1, dtype=int)
        indptr[0] = 0
        np.cumsum(caps, out=indptr[1:])

    ...

    if caps is None:
        return prop_matches, resp_matches
    else:
        return prop_matches, resp_matches, indptr
```

`caps` が `None` のときは `caps` を定義せずに `indptr` をメインのループで使うことにして，
最後の `if` 文で `caps` が `None` であるかの判定に使えるようにしています．

(ここで `n` は大学 (respondants) の数です．自分のコードでは適切な変数名に書きかえる．)

ちなみに，一番最後の `else` はなくてもよいです．

```python
    if caps is None:
        return prop_matches, resp_matches
    return prop_matches, resp_matches, indptr
```


## 単体テスト

[test_matching.py](https://github.com/oyamad/matching/blob/035ca753748f9358fe365f0a3c58c14508d89e1f/test_matching.py)
に多対一マッチング用のテストを加えました．
作業フォルダにダウンロード，これが通るようにコードを書いていく．

(test_matching.py を修正しました．2015/6/23)


## ランダム選好リスト (2015/6/29)

ランダムな `caps` も返すように
[matching_tools.py](https://github.com/oyamad/matching/blob/master/matching_tools.py)
の `random_prefs` を拡張しました．

* [使用例](http://nbviewer.ipython.org/github/oyamad/matching/blob/62f8a46bb23727095b8faa9a49ca3a1fad2ebbdb/random_prefs.ipynb)


## パフォーマンス比較の結果 (2015/7/6)

* [結果](http://nbviewer.ipython.org/github/OyamaZemi/exercises2015/blob/master/ex03/competition_many_to_one.ipynb)
