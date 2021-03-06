# CustomElementsについて

CustomElements周りです。
ShadowDOMとかHTMLTemplateなどいろいろ連携することもあるので、ここは個別の何かのメモ書きで、総合的なのは一つ上にまとめる予定。

# 概要

CustomElemetsはHTMLタグを自作する機能だと思えばよいです。
Googleが初期実装したv0と今ブラウザで実装が進みつつあるv1があり、v0は消えていく定めなのでv1についてのみです。

# 大雑把な使い方

大雑把には以下のステップでタグを自作します。

* HTMLElementもしくは既存タグを継承したクラスを定義する。
* タグ名とクラスをセットにして登録する。

HTMLElementを継承したものは普通のCustomElementで、既存のタグを拡張したものは既存タグの拡張という位置付けになるようです。
個人的には手間ではあるもののいろいろな不都合があるので、拡張はやらずHTMLElementを頑張って使うほうが良いと思います。

実際に使う時には以下のような方法があります。

* HTML直書き
    * `<自作タグ名></自作タグ名>` のように閉じタグとセットで必要。
    * `<拡張元タグ is="自作タグ名"></拡張元タグ>` のように、`is` 属性が必要。
        * 拡張を使わないほうが良い理由。おそらくメリットとしては拡張元タグに基づくCSSも引き継がれる（というかこうしないと引き継げない）が、その代償として冗長なタグ名となり、正直自作タグの意味があるかと言われると不明。
        * 拡張は拡張をしたいときだけにすべきな気がする。
    * ソースだけでなくinnerHTMLでも同じように使用可能。
* JSでnew
    * 単なるクラスなので、newしてappendChildすればよし。拡張の方もnewなら無問題とか。

タグは自作して登録するまで不明なタグとして扱われるので、レンダリングまでに必ず準備しなければいけないとか、準備ができてから使わなければならないといったルールはありません。安心して作りましょう。

# 具体的な手順

## タグの自作

```
class MyTag extends HTMLElement
{
	constructor()
	{
		super();
	}
}
```

とりあえずHTMLElementを継承するパターンだけに絞ります。
このように継承するだけです。

`constructor` をオーバーライドする場合は必ず `super` を実行して下さい。

## タグの登録

```
customElements.define( 'my-tag', MyTag );
```

このように、`customElements.define` を使います。
第一引数にはタグの名前、第二引数にはクラスを与えます。

重要なのは名前です。
CustomElementsではブラウザが提供するタグとユーザーが定義したタグの境界線を引くため、自作のタグには必ず `-` を含む必要があります。

これでソースを見たときにもすぐCustomElementsの利用の有無がわかります。

# 実際にタグを作る

実際に作るには、例えばHTMLTemplateでベースとなるタグの中身を作り、ShadowDOMでCustomElementsの内部実装を隔離し、場合によっては読み込みにHTML Importsを使うとかそういう感じになるかと思います。

とりあえずそれらはそれらで他のページで扱うこととします。

## 個人的な実装

個人的には以下のようなテンプレートを使っています。

```

class MyTag extends HTMLElement
{
	public static init( tagname = 'my-tag' )
	{
		// 本来thisにはMyTagをいれるが、ここならthisでOK
		customElements.define( tagname, this );
	}

	constructor()
	{
		super();
	}
}
```

後はどこかで `MyTag.init();`を実行します。
もしくはソース末尾に以下の処理を入れて、JSを読み込むだけで追加できるようにします。

```
window.addEventListener( 'DOMContentLoaded', () => { MyTag.init(); } );
```

正直読み込むのはJS一つにまとめられる規模がいいなと言う個人的な思いで、JSですべて書く場合はこんな形にします。
もっと大規模で複雑ならちゃんとHTML Importsとか使うと思うんですが、それWebComponentsにする意味あるかなぁと言う気もするので。


