# 多言語対応実装ガイド

## ドキュメント情報

| 項目 | 内容 |
|------|------|
| ドキュメント名 | PC Parts Shop 多言語対応実装ガイド |
| バージョン | 1.0 |
| 作成日 | 2026-02-03 |
| 対象読者 | 開発者、翻訳者、QAエンジニア |

---

## 1. はじめに

本ドキュメントは、PC Parts Shopにおける多言語対応の実装方法を説明します。
`i18n-requirements.md`に定義された要件を実装する際の具体的な手順とベストプラクティスを提供します。

---

## 2. 開発環境のセットアップ

### 2.1 必要なパッケージ

すでに以下のパッケージがインストールされています：

```json
{
  "i18next": "^25.8.0",
  "i18next-browser-languagedetector": "^8.2.0",
  "react-i18next": "^16.5.4"
}
```

### 2.2 プロジェクト構造

```
src/
├── i18n/
│   └── index.js              # i18next設定
├── locales/
│   ├── ja/
│   │   └── translation.json  # 日本語翻訳
│   └── en/
│       └── translation.json  # 英語翻訳
├── components/
│   └── common/
│       └── LanguageSwitcher.jsx  # 言語切り替えコンポーネント
└── ...
```

---

## 3. 翻訳の追加方法

### 3.1 新しい翻訳キーの追加

#### ステップ1: 日本語翻訳を追加

`src/locales/ja/translation.json`を編集：

```json
{
  "myFeature": {
    "title": "新機能のタイトル",
    "description": "新機能の説明文",
    "button": "アクションボタン"
  }
}
```

#### ステップ2: 英語翻訳を追加

`src/locales/en/translation.json`を編集：

```json
{
  "myFeature": {
    "title": "New Feature Title",
    "description": "Description of the new feature",
    "button": "Action Button"
  }
}
```

### 3.2 コンポーネントでの使用

```jsx
import React from 'react';
import { useTranslation } from 'react-i18next';

const MyComponent = () => {
  const { t } = useTranslation();

  return (
    <div>
      <h1>{t('myFeature.title')}</h1>
      <p>{t('myFeature.description')}</p>
      <button>{t('myFeature.button')}</button>
    </div>
  );
};

export default MyComponent;
```

---

## 4. 高度な翻訳機能

### 4.1 変数の補間

翻訳に動的な値を埋め込む場合：

**翻訳ファイル:**
```json
{
  "welcome": "ようこそ、{{name}}さん！",
  "itemCount": "カート内に{{count}}個の商品があります"
}
```

**コンポーネント:**
```jsx
<p>{t('welcome', { name: userName })}</p>
<p>{t('itemCount', { count: cartItems.length })}</p>
```

### 4.2 複数形対応

英語の複数形に対応する場合：

**英語翻訳:**
```json
{
  "items_one": "{{count}} item",
  "items_other": "{{count}} items"
}
```

**日本語翻訳:**
```json
{
  "items": "{{count}}点"
}
```

**コンポーネント:**
```jsx
// 英語: "1 item" または "5 items"
// 日本語: "1点" または "5点"
<p>{t('items', { count: itemCount })}</p>
```

### 4.3 HTMLタグを含む翻訳

**翻訳ファイル:**
```json
{
  "termsAgreement": "利用規約に<1>同意します</1>"
}
```

**コンポーネント:**
```jsx
import { Trans } from 'react-i18next';

<Trans i18nKey="termsAgreement">
  利用規約に<a href="/terms">同意します</a>
</Trans>
```

### 4.4 日付・数値のフォーマット

```jsx
import { useTranslation } from 'react-i18next';

const MyComponent = () => {
  const { i18n } = useTranslation();
  
  // 日付フォーマット
  const formattedDate = new Intl.DateTimeFormat(i18n.language, {
    year: 'numeric',
    month: 'long',
    day: 'numeric'
  }).format(new Date());
  
  // 通貨フォーマット
  const formattedPrice = new Intl.NumberFormat(i18n.language, {
    style: 'currency',
    currency: 'JPY'
  }).format(89800);
  
  return (
    <div>
      <p>{formattedDate}</p>
      <p>{formattedPrice}</p>
    </div>
  );
};
```

---

## 5. 言語切り替えの実装

### 5.1 既存の言語切り替えコンポーネント

`src/components/common/LanguageSwitcher.jsx`が既に実装されています：

```jsx
import React from 'react';
import { useTranslation } from 'react-i18next';

const LanguageSwitcher = () => {
  const { i18n } = useTranslation();

  const changeLanguage = (lng) => {
    i18n.changeLanguage(lng);
  };

  return (
    <div className="language-switcher">
      <button
        className={`lang-btn ${i18n.resolvedLanguage === 'ja' ? 'active' : ''}`}
        onClick={() => changeLanguage('ja')}
      >
        日本語
      </button>
      <button
        className={`lang-btn ${i18n.resolvedLanguage === 'en' ? 'active' : ''}`}
        onClick={() => changeLanguage('en')}
      >
        English
      </button>
    </div>
  );
};
```

### 5.2 プログラマティックな言語変更

```jsx
import { useTranslation } from 'react-i18next';

const MyComponent = () => {
  const { i18n } = useTranslation();
  
  const handleLanguageChange = (event) => {
    const newLang = event.target.value;
    i18n.changeLanguage(newLang);
  };
  
  return (
    <select value={i18n.language} onChange={handleLanguageChange}>
      <option value="ja">日本語</option>
      <option value="en">English</option>
    </select>
  );
};
```

---

## 6. 商品データの多言語化

### 6.1 現在の課題

現在、商品データは日本語のみで保存されています：

```json
{
  "id": "prod-001",
  "name": "Intellon Core X9-14900K",
  "description": "第14世代Intellon Coreプロセッサー..."
}
```

### 6.2 推奨される実装方法

#### オプションA: データ構造の変更（推奨）

**メリット**: 完全な多言語対応、保守性が高い
**デメリット**: データ構造の大幅な変更が必要

```json
{
  "id": "prod-001",
  "name": {
    "ja": "Intellon Core X9-14900K",
    "en": "Intellon Core X9-14900K"
  },
  "description": {
    "ja": "第14世代Intellon Coreプロセッサー。",
    "en": "14th generation Intellon Core processor."
  },
  "specifications": {
    "ja": {
      "コア数": "24 (8P+16E)"
    },
    "en": {
      "Core Count": "24 (8P+16E)"
    }
  }
}
```

**API実装例:**
```javascript
// server/controllers/productController.js
exports.getProducts = (req, res) => {
  const lang = req.query.lang || 'ja';
  const products = dataStore.getProducts();
  
  const localizedProducts = products.map(product => ({
    ...product,
    name: product.name[lang] || product.name.ja,
    description: product.description[lang] || product.description.ja,
    specifications: product.specifications[lang] || product.specifications.ja
  }));
  
  res.json(localizedProducts);
};
```

**フロントエンド実装例:**
```javascript
// src/services/productService.js
export const getProducts = async (lang) => {
  const response = await fetch(`/api/products?lang=${lang}`);
  return response.json();
};

// コンポーネント内
const { i18n } = useTranslation();
const products = await getProducts(i18n.language);
```

#### オプションB: 翻訳ファイルでの管理（簡易版）

**メリット**: データ構造変更不要、実装が簡単
**デメリット**: 拡張性が低い、商品数が増えると管理が困難

```json
// src/locales/ja/products.json
{
  "prod-001": {
    "name": "Intellon Core X9-14900K",
    "description": "第14世代Intellon Coreプロセッサー。"
  }
}

// src/locales/en/products.json
{
  "prod-001": {
    "name": "Intellon Core X9-14900K",
    "description": "14th generation Intellon Core processor."
  }
}
```

**使用例:**
```jsx
const { t } = useTranslation('products');
<h2>{t(`${product.id}.name`, product.name)}</h2>
```

---

## 7. テストガイド

### 7.1 翻訳の存在確認

開発環境では、翻訳漏れを検出するために`debug: true`に設定：

```javascript
// src/i18n/index.js
i18n.init({
  debug: process.env.NODE_ENV === 'development',
  // ...
});
```

### 7.2 手動テストチェックリスト

各言語で以下を確認：

- [ ] すべてのページで翻訳が表示される
- [ ] 長いテキストでもレイアウトが崩れない
- [ ] ボタンやラベルが適切に翻訳されている
- [ ] エラーメッセージが翻訳されている
- [ ] 日付・数値が適切にフォーマットされている
- [ ] 言語切り替えが即座に反映される
- [ ] ページリロード後も言語設定が保持される

### 7.3 翻訳カバレッジの確認

未使用の翻訳キーや翻訳漏れを確認する方法：

```bash
# すべての翻訳キーをリストアップ
grep -r "t('" src/ | sed "s/.*t('\\([^']*\\)'.*/\\1/" | sort | uniq

# 翻訳ファイルのキーをリストアップ
cat src/locales/ja/translation.json | jq -r 'paths(scalars) | join(".")'
```

---

## 8. ベストプラクティス

### 8.1 翻訳キーの命名

✅ **良い例:**
```javascript
t('productDetail.addToCart')
t('cart.empty.message')
t('checkout.shippingInfo')
```

❌ **悪い例:**
```javascript
t('button1')
t('msg')
t('text')
```

### 8.2 階層構造の使用

翻訳は画面または機能ごとにグループ化：

```json
{
  "productList": {
    "title": "商品一覧",
    "filter": {
      "category": "カテゴリ",
      "search": "検索"
    }
  },
  "productDetail": {
    "title": "商品詳細",
    "addToCart": "カートに追加"
  }
}
```

### 8.3 デフォルト値の提供

翻訳が見つからない場合のフォールバック：

```jsx
// 翻訳がない場合はデフォルト値を表示
{t('newFeature.title', 'Default Title')}
```

### 8.4 翻訳のコンテキスト

同じ単語でも文脈によって翻訳が異なる場合：

```json
{
  "header.close": "閉じる",
  "shop.close": "閉店"
}
```

### 8.5 文字列の連結を避ける

❌ **悪い例:**
```jsx
{t('hello')} + ' ' + userName + ' ' + t('welcome')
```

✅ **良い例:**
```jsx
{t('helloUser', { name: userName })}
```

翻訳ファイル:
```json
{
  "helloUser": "こんにちは、{{name}}さん。ようこそ！"
}
```

---

## 9. パフォーマンス最適化

### 9.1 翻訳ファイルの分割

大規模なアプリケーションでは、名前空間ごとに翻訳ファイルを分割：

```
locales/
  ja/
    common.json       # 共通UI要素
    product.json      # 商品関連
    cart.json         # カート関連
    checkout.json     # 注文関連
```

```jsx
// 使用例
const { t } = useTranslation(['common', 'product']);
<p>{t('product:detail.title')}</p>
```

### 9.2 遅延ローディング

必要な翻訳のみをロード：

```javascript
// src/i18n/index.js
i18n.init({
  // ...
  ns: ['common'],
  defaultNS: 'common',
  partialBundledLanguages: true,
});
```

---

## 10. トラブルシューティング

### 10.1 翻訳が表示されない

**原因1**: 翻訳キーが間違っている
```javascript
// 確認方法
console.log(t('yourKey', { returnObjects: true }));
```

**原因2**: 翻訳ファイルが読み込まれていない
```javascript
// 確認方法
console.log(i18n.store.data);
```

**原因3**: JSONの構文エラー
```bash
# JSONの検証
cat src/locales/ja/translation.json | jq
```

### 10.2 言語が切り替わらない

**原因1**: LocalStorageがブロックされている
```javascript
// 確認方法
localStorage.setItem('i18nextLng', 'en');
console.log(localStorage.getItem('i18nextLng'));
```

**原因2**: コンポーネントが再レンダリングされていない
```javascript
// useTranslation フックを使用しているか確認
const { t, i18n } = useTranslation();
```

### 10.3 文字化けが発生する

- HTMLファイルの`<meta charset="UTF-8">`を確認
- 翻訳ファイルがUTF-8で保存されているか確認
- サーバーのレスポンスヘッダーを確認

---

## 11. 翻訳者向けガイド

### 11.1 翻訳ファイルの編集方法

1. `src/locales/[言語コード]/translation.json`を開く
2. JSONの構造を保ちながら、値のみを翻訳
3. 変数（`{{変数名}}`）は翻訳しない
4. HTMLタグ（`<0>`, `<1>`）は翻訳しない

### 11.2 翻訳時の注意点

- **文脈を理解する**: キーの階層から文脈を推測
- **一貫性を保つ**: 同じ用語は常に同じ翻訳を使用
- **長さを考慮**: ボタンのラベルは短く簡潔に
- **文化的配慮**: 文化的に不適切な表現を避ける
- **専門用語**: PC部品の専門用語は業界標準の訳を使用

### 11.3 翻訳例

```json
{
  "productDetail": {
    "addToCart": "カートに追加",           // ボタンラベル: 短く
    "addedToCart": "{{name}}をカートに追加しました",  // メッセージ: 詳細に
    "quantity": "数量",                   // ラベル: 簡潔に
    "inStock": "在庫あり ({{count}}個)"    // 状態表示: 情報を含める
  }
}
```

---

## 12. よくある質問（FAQ）

### Q1: 新しい言語を追加するには？

A: 以下の手順で追加できます：

1. `src/locales/[新言語コード]/translation.json`を作成
2. `src/i18n/index.js`にインポートを追加
3. `supportedLngs`配列に言語コードを追加
4. `LanguageSwitcher.jsx`にボタンを追加

### Q2: 翻訳をプログラムから変更できますか？

A: はい、`i18n.addResourceBundle()`を使用できます：

```javascript
i18n.addResourceBundle('ja', 'translation', {
  newKey: '新しい翻訳'
}, true, true);
```

### Q3: サーバーサイドレンダリング（SSR）に対応していますか？

A: 現在のSPAアーキテクチャではSSRを使用していませんが、Next.jsに移行する場合は`next-i18next`の使用を検討してください。

### Q4: 翻訳ファイルをAPIから動的にロードできますか？

A: はい、i18nextのbackendプラグインを使用できます：

```javascript
import Backend from 'i18next-http-backend';

i18n
  .use(Backend)
  .init({
    backend: {
      loadPath: '/api/locales/{{lng}}/{{ns}}.json'
    }
  });
```

---

## 13. 参考リソース

### 公式ドキュメント

- [i18next Documentation](https://www.i18next.com/)
- [React-i18next Documentation](https://react.i18next.com/)
- [i18next-browser-languagedetector](https://github.com/i18next/i18next-browser-languageDetector)

### ツール

- [i18next-parser](https://github.com/i18next/i18next-parser) - 翻訳キーの自動抽出
- [jsonlint](https://jsonlint.com/) - JSONファイルの検証
- [Locize](https://locize.com/) - 翻訳管理プラットフォーム

### スタイルガイド

- [Microsoft Localization Style Guides](https://www.microsoft.com/en-us/language/styleguides)
- [Apple Style Guide](https://support.apple.com/ja-jp/guide/applestyleguide/welcome/web)
- [Google Developer Documentation Style Guide](https://developers.google.com/style)

---

## 14. まとめ

本ガイドでは、PC Parts Shopにおける多言語対応の実装方法を説明しました。

**重要ポイント:**
- 翻訳はファイルベースで管理
- `useTranslation`フックを使用
- 翻訳キーは階層構造で命名
- 商品データの多言語化は計画的に実施
- テストと品質管理を徹底

実装に関する質問や問題がある場合は、プロジェクトのIssueで報告してください。

---

**最終更新**: 2026-02-03
