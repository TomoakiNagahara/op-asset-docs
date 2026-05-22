# Frontend Asset Authoring

## 目的

この文書は、ONEPIECE Framework における JavaScript / CSS file の書き方の rule と注意点を定義します。

application、layout、unit、module、framework-provided asset package で frontend asset を追加または変更する場合、この rule を適用します。

## WebPack Grouping Model

ONEPIECE Framework の WebPack は、拡張子ごとに複数の file をまとめ、各拡張子の group を 1 回の request で送信する framework-native な system です。

これは Node.js 製の webpack ではありません。

同じ拡張子の file は 1 つの response に連結される場合があるため、各 frontend asset file は他の file と同じ output stream を共有しても安全な書き方にする必要があります。

WebPack は、拡張子ごとに packing した extension group ごとの hash も生成し、その hash を grouped asset URL に含めます。

ONEPIECE Framework の概念は、hash を packed bytes、content value、registered file list、または別の cache identity のどれから導出するかを強制しません。その選択は `op-unit-webpack` の責任です。

frontend asset を debug する場合は、URL hash を、どの packed content version が request されているか、cached packed output がまだ返っていないかを確認する手がかりとして使います。

## JavaScript Rules

JavaScript file は grouped delivery の影響を受けやすいです。複数 file が結合されると、top-level declaration が衝突する可能性があります。

WebPack-managed な各 JavaScript file は、code を closure で包んでください。

```javascript
(function(){
	'use strict';

	// File-local code goes here.
})();
```

この closure style は次に使います。

- page JavaScript
- layout JavaScript
- unit または module の JavaScript
- framework-provided standard JavaScript asset

他の script に提供する public API を意図している場合だけ、`window` やその他の shared object に名前を公開します。

public API を公開する必要がある場合は、export する surface を小さくし、名前から ownership が分かるようにしてください。

## CSS Rules

CSS は JavaScript より grouped delivery の影響を受けにくいですが、grouped response 全体に cascade は適用されます。

CSS を書く場合は次に注意します。

- file の ownership boundary に合う selector を使う
- layout、page、unit、module の scope が使える場合は、広すぎる selector を避ける
- 同じ拡張子 group の他の CSS file と一緒に送信される可能性を前提にする
- WebPack registration で順序を意図的に制御していない限り、偶然の file order に依存しない

## Registration Awareness

file が `js` directory や `css` directory に存在するだけで pack されるとは考えないでください。

file は、application、layout、unit、module が使う WebPack flow を通じて明示登録された場合に含まれます。

frontend asset が browser に出ない場合は、次を確認します。

- file が登録されているか
- 正しい extension group が request されているか
- grouped delivery request が意図した app-owned、layout-owned、unit-owned、module-owned directory を使っているか
- cache や hash state により最新の registration result が隠れていないか

## 関連文書

- `../webpack.ja.md`
- `../module/webpack.ja.md`
- `responsibility-boundaries.ja.md`
