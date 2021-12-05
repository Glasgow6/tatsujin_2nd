# 第7章  コーディング段階

## 概要

## 37  爬虫類脳からの声に耳を傾ける
**爬虫類脳 = 暗黙知・本能**
プログラマーとして経験を積み重ねることで、暗黙知（上手くいくこと・いかないことの判別）が蓄積される。
暗黙知が警鐘を鳴らしている、そのサインを見極めることが重要。コードをなかなか書き進められないときは、何かを間違えている可能性が大きい。
* 構造や設計が間違っている
* 誤った問題を解決しようとしている

自らの暗黙知との対話ステップ。
1. 作業を止め、キーボードから離れて他のことをする。
2. 問題を具体的な形に表現し、他者に説明してみる。
3. **プロトタイピング**などにより、状況を整理して不安を消す。

### プロトタイピング
既存のコードを相手に作業し、手こずっている場合は、似たようなもののプロトタイピングを行うことで考えを整理できる。プロトタイピングは以下のように始める。
1. ポストイットに"プロトタイピング中"と描いて画面の横に貼る。
2. **プロトタイピングは失敗するために行う**という意識を持ち、プロトタイプはいずれの場合でも捨て去る。
3. 実行したいこと・やりたいことをコメントとして1行にまとめる。
4. コーディングを始める。

コーディングの最中にぼんやりした不安が具体化したら、その問題に取り組む。
プロトタイピングが終了し、それでも不安が残るのなら、しばらくコーディングから離れて散歩。

### コードから学ぶ
他人のコードを読むことで、他人の”本能”（意思決定）を知ることができる。見知らぬ方法で実装されている箇所を見つけたらそれをメモする習慣を持ち、そこからパターンを見つけ出し、パターンの根拠を明らかにすることができれば、コードへの理解は一気に深まる。


## 38  偶発的プログラミング
**行き当たりばったりの偶発的なプログラミングではなく、慎重なプログラミングを行う。**
少しでも触ると動かなくなるのになぜか動いているコード、動いているように見えるコードが存在している。それでもコードに手を加えなければならないときは来てしまう。
* 動いているようで動いていない
* 前提の境界条件が偶発的で、異なる解像度やCPUでは動かない
* 次のアップデートでドキュメント化されていない動作が必要
* 不要な呼び出しが残っているので挙動が重い

他人の使うコードを書いているなら、**正しいモジュール化**の基本原則と**コンパクトかつ上手にドキュメント化されたインターフェイスの背後に実装を隠す**という基本原則に準じ、契約（DbC）を活用することも有用。
機能を呼び出すときは**ドキュメント化されている振る舞いのみ**を前提とする。

タイムゾーンが1時間ずれるから、+1して出力すればOKという偶発的対処は問題。基本的な欠陥は別。
欠陥は”ユーザーが日本語を話す”・”ディレクトリが書き込み可能”といったコンテキストが間違っている可能性もある。

要求確定からテストに至るまで、暗黙の仮定からなる偶然が蔓延している。そのような仮定はドキュメント化されず、開発者間で矛盾する場合もあり、有害である。


### 慎重なプログラミング
* 常に何をしているかを意識する
* コードの詳細を新人に説明できるか
* 完全に理解していない手法や馴染みのない技法を使わない
* 明確なプランが頭の中にあるか
* 信頼のおけるものごとのみを前提とする
* 仮定をドキュメント化する（DbC）
* コードのテストのみではなく、仮定のテストも行う（表明を使用）
* 作業に優先順位をつけ、重要な部分に時間を掛ける
* 既存コードのしがらみにとらわれない

## 39  アルゴリズムのスピード
**プロジェクト終了までの時間的見積もり以外に、プログラマーはアルゴリズムの時間消費やメモリなどのリソースの見積もりを行っている。**

O記法を使って計算量を評価する。

![picture 1](images/1638690893925.png)

ソートアルゴリズム自体を開発することはほぼないが、使用する機会が多いので計算量を意識することは非常に役立つ。

![picture 2](images/1638691033843.png)

https://twitter.com/tome_ura?ref_src=twsrc%5Egoogle%7Ctwcamp%5Eserp%7Ctwgr%5Eauthor

コードの実行時間やメモリ使用量の目安がわからない場合は、影響する数値を変更した結果をグラフで可視化して確認すればイメージがつくようになる。
常に現実的な視点でアルゴリズムを選択する。

## 40  リファクタリング
**ソフトウェアは建築よりもむしろガーデニング。**
常に状態を監視しながら、すこしずつ変えていくもの。建築のように科学的・繰り返し可能・指示報告が厳格というものではない。

Martin Fowlerのリファクタリング（リファクタリング  第2版：読んでる途中で忘れてたｗ）の定義。
* このアクティビティーは規律あるものであり、勝手気ままにすすめるものではない。
* 外部から見た振る舞いを変えることは許されていない。リファクタリングは機能追加をする場所ではない。

### リファクタリングを行うべきタイミング
* 二重化しているものを発見したとき
* 直行していない設計があるとき
* 時代遅れの知識が出てきたとき
* ユーザーによる使用方法が変わったとき
* パフォーマンス向上が必要なとき
* テストが通っているとき

リファクタリングをするべき箇所を放置しておくと、あとで大量の時間投資が必要になる。他の部分を巻き添えにして被害が拡大する。不十分なコードを**腫瘍**と捉え、早めにこまめにリファクタリングを行うべし。

### リファクタリングの方法
* リファクタリングと機能追加を同時に行わない
* リファクタリングの前にテストを追加し、頻繁にテストを行う。
* メソッド分割や変数リネームなど小さな単位で少しずつ行う。

## 41  コードのためのテスト
**テストのメリットはテストについて考え、テストを記述している時そのもの**

テストを書くことによって、関数の構造が洗練されていく。（以下、例）
* テストデータを渡せるような引数を作ろう
* テストデータの作成方法を検討することで、手持ちデータでテストが行えるような構造にでき、変更が容易になる。
* テストの境界条件と機能を事前に検討できる。

事前にテストを考え、さらに書きあげてからコードを書くのが**TDD**。
TDDの落とし穴。
* テストのカバレッジを常に100にしようとしてむだに時間を費やす。
* 冗長なクラスを定義しすぎる。
* 設計がボトムアップなもの（どうやって利用されるかを考えない）になりがちになる。

とにかくTDDを実践すること。
ボトムアップ・トップダウン両輪のエンドツーエンドで構築していく。
全体的な展望を見失わず、実際のソリューションに近づかない無意味なテストをパスして満足してはいけない。

### 契約に対するテスト
ユニットテストを”契約に対するテスト”と捉えてみる。
平方根を計算する機能の場合、契約は単純。
* 引数は負ではない（0もしくは正の数）
* 結果の2乗と引数の差は計算機イプシロンより小さい値になる

これによって事前条件と事後条件をチェックしていると仮定できるため、平方根機能をテストするための基本的なテストスクリプトを記述できる。

しかし、現実ではさまざまなモジュールに依存している組合わせテストをどう行うのか。
その場合はそれぞれのモジュールの契約のテストを行い、そのあとで直接明示されない他の契約に依存するメインコンポーネントの契約をテストすればよい。
（AでB, Cを利用する時、B, Cのテストで問題がないなら、Aのそれらの使用方法に問題がある。）

本番環境ではテストできないので、ログを残したりホットシーケンスやマジックURLを使って状態を確認できるようにする手段もある。

### テスト文化
最初にテスト→開発中にテスト→あとにテスト  という順で良い策。
時間あったらテストするわ～（笑）

テストコードは本番コードと同じくらい慎重に扱い、結合度を低くクリーンかつ堅牢にしておくこと。偶発的プログラミングをしないこと。

## 42  プロパティーベースのテスト
**テストはコード作成者の認識に基づいて作成されるので、コードはテストをパスする。
あなたの抱く予想を共有していないコンピューターがテストを実施すれば良い。**

契約と不変性をまとめてプロパティとし、それらを使ってテストを自動化する。
```python
# hypothesisは生成するデータを描写するためのミニ言語を持つ
# 可読性を上げるため、someとしてstrategies内のモジュールを呼び出していく
from hypothesis imort given
import hypothesis.strategies as some

# givenのデコレータによって、テスト関数が複数回実行される。
# @given(some.integers(min_value=5, max_value=10).map(lambda x x * 2))
# とすると、10~20の間の偶数を渡すようになる

# リストの大きさが変わらないこと
@given(some.lists(some.integers()))
def test_list_size_is_invariant_across_sorting(a_list):
    original_length = len(a_list)
    a_list.sort()
    assert len(a_list) == original_length

# リストが正しくソートされていること
@given(some.lists(some.texts()))
def test_sorted_result_is_orderd(a_list):
    a_list.sort()
    for i in range(len(a_list) -1):
        assert a_list[i] <= a_list[i + 1]

```
### まずい仮定を見つけ出す
かんたんな注文処理と在庫管理のシステムがテーマ。
```python
from   hypothesis import given
import hypothesis.strategies as some

class Warehouse:
    def __init__(self, stock):
        self.stock = stock

    def in_stock(self, item_name):
        return (item_name in self.stock) and (self.stock[item_name] > 0)

    def take_from_stock(self, item_name, quantity):
        if quantity <= self.stock[item_name]:
            self.stock[item_name] -= quantity
        else:
          raise Exception("Oversold {}".format(item_name))

    def stock_count(self, item_name):
        return self.stock[item_name]

def test_warehouse():
    wh = Warehouse({"shoes": 10, "hats": 2, "umbrellas": 0})
    assert wh.in_stock("shoes")
    assert wh.in_stock("hats")
    assert not wh.in_stock("umbrellas")

    wh.take_from_stock("shoes", 2)
    assert wh.in_stock("shoes")

    wh.take_from_stock("hats", 2)
    assert not wh.in_stock("hats")
```

倉庫から注文を取得するための要求を処理する関数。
```python
def order(warehouse, item, quantity):
    if warehouse.in_stock(item):
        warehouse.take_from_stock(item, quantity)
        return ( "ok", item, quantity )
    else:
        return ( "not available", item, quantity )

def test_order_in_stock():
    wh = Warehouse({"shoes": 10, "hats": 2, "umbrellas": 0})
    status, item, quantity = order(wh, "hats", 1)
    assert status   == "ok"
    assert item     == "hats"
    assert quantity == 1
    assert wh.stock_count("hats") == 1

def test_order_not_in_stock():
    wh = Warehouse({"shoes": 10, "hats": 2, "umbrellas": 0})
    status, item, quantity = order(wh, "umbrellas", 1)
    assert status   == "not available"
    assert item     == "umbrellas"
    assert quantity == 1
    assert wh.stock_count("umbrellas") == 0

def test_order_unknown_item():
    wh = Warehouse({"shoes": 10, "hats": 2, "umbrellas": 0})
    status, item, quantity = order(wh, "bagel", 1)
    assert status   == "not available"
    assert item     == "bagel"
    assert quantity == 1
```

ここまで表面上は問題ないが、在庫はトランザクションをまたがって出現したり消失したりしないというプロパティーテストを追加。
```python

@given(item     = some.sampled_from(["shoes", "hats"]),
       quantity = some.integers(min_value=1, max_value=4))
def test_stock_level_plus_quantity_equals_original_stock_level(item, quantity):
    wh = Warehouse({"shoes": 10, "hats": 2, "umbrellas": 0})
    initial_stock_level = wh.stock_count(item)
    (status, item, quantity) = order(wh, item, quantity)
    if status == "ok":
        assert wh.stock_count(item) + quantity == initial_stock_level

```

これは失敗。（take_from_stockでExceptionがスローされる。）
本来は少なくとも在庫がひとつ、ではなく、在庫が注文より多いということを確認する必要がある。

プロパティーテストはランダムな値が使用されるので、プロパティーテストが失敗した場合、引き渡されたパラメーターを調べ、それに対して一般的なユニットテストを個別に作成することが失敗の原因を特定するヒントになる。

プロパティーベースのテストは不変性と契約という観点からコードについて考えさせてくれる。変更してはならないもの、本当のものは何かを考えさせてくれるので、境界条件の曖昧さがなくなり、整合性のない状態にデータを多く機能に光が当たるようになる。

プロパティーベースのテストは、ユニットテストを補完するものであると信じている。

## 43  実世界の外敵から身を守る