## Prettier

作成日 2020/02/28

## 01. Prettier とは

Prettier は、必須の拡張機能\
JavaScript を自動で修正・整形してくれる便利ツール

[Prettier \- Code formatter \- Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)

## 02. Prettier を設定する

ときに整形ルールを変更したいときもある

`.prettierrc`ファイル

```json
{
  "trailingComma": "es5",
  "tabWidth": 4,
  "semi": true,
  "singleQuote": true
}
```

### 設定の上書き

Markdown ファイルも Prettier が整形してくれるのは助かっているが、\
タブ幅がリストインデントに影響しているに困っていた\
ある条件の場合のみ、設定が上書きされる方法を発見した

[Configuration File · Prettier](https://prettier.io/docs/en/configuration.html)

```json
{
  "trailingComma": "es5",
  "tabWidth": 4,
  "semi": true,
  "singleQuote": true,
  "overrides": [
    {
      "files": "*.md",
      "options": {
        "tabWidth": 2
      }
    }
  ]
}
```