# 第4章　妄想の達人

## 概要
完璧なソフトウェアを作ることはできない。
その事実に基づいて、石橋を叩いて渡りましょう。
## 23　契約による設計 (DbC)
### 契約による設計とは
ソフトウェアモジュールの権利と責務を文書化し、プログラムの正しさを保証する技法。
* 事前条件が保証されていなければ機能呼び出しを拒否
* 事後条件によって、機能終了後の状態を保証（無限ループは存在できない）
* クラス不変表明により、呼び出し側に対して常に真となることを保証する条件

**事前条件が満足されたとき、処理完了時点で事後条件と不変表明は満足される。**という契約が成立し、満たされない場合は例外処理を行う。

たとえばPythonでは以下のように書ける（[参考記事リンク](https://qiita.com/usop4/items/7d0a039616902d644f82)）
一部は型アノテーションで代用が可能。
```python
from contracts import contract

# pattern 1
@contract(a='int,>0', b='int,>0', returns='int')
def my_add(a, b):
    return a+b

print(my_add(1,1)) # 普通に実行される
print(my_add(1,1.1)) # ちゃんとクラッシュする

# pattern 2
@contract
def my_add2(a : 'int,>0', b : 'int,>0') -> 'int':
    return a+b

print(my_add2(1,1))
print(my_add2(1,1.1))

# pattern 3
@contract
def my_add3(a, b):
    """ Function description.
        :type a: int,>0
        :type b: int,>0
        :rtype: int
    """
    return a+b
print(my_add3(1,1))
print(my_add3(1,1.1))

```

DbCとテストはプログラムの正確性という幅広い対象に向かう共通点があるが、そのアプローチが異なる。TDDはテスト中におけるコードの普遍性チェックには重きを置いていない。

### 早めのクラッシュ
DbCによる事前条件や事後条件でクラッシュを早期に検知することで、異常箇所の特定が素早く行える。NaNが算出されたまま後続の処理を続けていくと、摩訶不思議な結果が生み出され、特定が難しくなる。
また、障害によるダメージを最小限に抑えることもできる。

### セマンティック不変表明
ものごとの意味の中心にある大前提。変更される可能性のある単なるポリシーではない。重要な要求があるのであれば、ドキュメントの核心として書く。それはシステムを使用するすべてのユーザーと我々の行動を保証するための契約となる。

### チャレンジ
DbCはなぜ広く使われないのか。

## 24　死んだプログラムは嘘をつかない
エラーは情報を与えてくれる。エラーがあれば確実に読む。
### キャッチ＆リリース
例外をすべてキャッチして処理を書かない。理由は以下。
* 本来のコードがエラーハンドリングに埋もれる
* コードの結合度を下げる。

# 25　表現を用いたプログラミング
ありえないなんてことはありえない。の気持ちを持つ。起こらないなら表現によって保証する。
 ```assert``` を使うのだ。起こり得ないことをチェックするので、エラーハンドリングのために使ってはならない。（正しい動作に必要な処理を実行するために使ってはならない）


```Python
# これはVery bad
puts("Enter 'Y' or 'N': ")
ans = gets[0]
assert((ch == 'Y') || (ch == 'N'))
```

また、表明を使う場合にはデバッグ対象システムの振る舞いを変えてはならない。（for文の繰り返しが進むなど）

### 表明を有効化したままにしておく
本番環境において、考えられうるエラーを第一防衛ラインでチェック、第二防衛ラインが表明となる。デプロイ時にも表明はオフにするべきではない。

**→表明はテストとは違うらしい。**
わかんねえのでt-wadaのスライド召喚！！[デデーン](https://speakerdeck.com/twada/php-conference-2016?slide=68)

## 26　リソースのバランス方法
コーディングの際はリソースの管理を必ず行う。始めたら終わらせる。

```Ruby
def read_customer
    @customer_file = File.open(@name + ".rec", "r+")
    @balance = BigDecimal(@customer_file.gets)
end

def write_customer
    @customer_file.rewind
    @customer_file.puts @balance.to_s
    @customer_file.close
end

# もとのバージョン
def update_customer(transaction_amount)
    read_customer
    @balance = @balance.add(transaction_amount, 2)
    write_customer
end

# 悪い変更。write_customerが呼び出されずにcloseしないケースがある。
def update_customer(transaction_amount)
    read_customer
    if (transaction_amount >= 0.00)
        @balance = @balance.add(transaction_amount, 2)
        write_customer
    end
end
```

上記のコードを変更する場合、特殊なケースを扱うような変更をしてはならない。問題自体は修正されるが、コードの状態の管理などが面倒になり、コードの質が低下することになる。変えるなら以下のように変える。

```Ruby
def read_customer(file)
    @balance = BigDecimal(file.gets)
end

def write_customer(file)
    file.rewind
    file.puts @balance.to_s
end

def update_customer(transaction_amount)
    file = File.open(@name + ".rec", "r")
    read_customer(file)
    @balance = @balance.add(transaction_amount, 2)
    write_customer(file)
    file.close
end
```

ファイル参照が保持されるのではなく、パラメーターとして扱われるため、ファイル操作が集約されている。
また、以下のように局所的に変数のスコープを制限することも可能。自動的にリソースが開放される。

```Ruby
def update_customer(transaction_amount)
    file = File.open(@name + ".rec", "r") do |file|
        read_customer(file)
        @balance = @balance.add(transaction_amount, 2)
        write_customer(file)
        # file.close ←クローズしなくて良い。
    end
end
```

### 割当のネスト
* リソースは割り当てた順に開放。
* コードの異なる箇所で同じリソースの組を割り当てるなら、同じ順で。（デッドロック回避）

### 例外
* オブジェクト指向では、特定のリソースが必要になるたびにオブジェクトを生成・デストラクターでスコープから除外を繰り返す。
* 例外処理が行われる前にリソースを開放する。
  * 変数のスコープ
  * try, catch, finallyを使って、finallyでクリーンアップ

### リソースをバランスさせられない時
あるルーチンで割り当てたメモリ領域を、当分使用する大きな構造にリンクさせる場合がそれにあたる。そこでは、メモリー割当に対するセマンティック不変性を確立する。誰が構造中のデータに対して責任を持つのかを決定しておく。
たとえばトップレベルの構造を開放した際は、以下のことが起こると考えられる。
* トップレベルの構造は自らが保持している部分構造の開放について責任を持ち、順次再帰的に開放を行っていく。
* トップレベルの構造のみを開放する。トップレベルの構造が保持する構造は参照されなくなるが、存在しているので孤児となる。
* トップレベルの構造は保持する構造がある限り開放を拒否する。

リソースが開放されているかを確認するコードを書くのもよい。

### チャレンジ
本書では主要なデータ構造に対するセマンティック不変表明を確立してメモリーの解放指針を規定した。DbCをどのように活用すれば、このアイディアを磨けるか。

# 27　ヘッドライトを追い越そうとしない
ヘッドライトを追い越さないように、常に小さな歩幅で少しずつ前進すること。
フィードバックのペースをを速度制限と捉える。思わぬところで予見できないことが発生する。
