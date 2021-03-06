# ファイルをアップロードする

作成日 2020/03/11

## 01. 前提

- ウェブサーバーは、[Flask](https://flask.palletsprojects.com/en/1.1.x/)を使っている
- JS フレームワークは、[Vue.js](https://jp.vuejs.org/v2/guide/index.html)を使っている
- CSS フレームワークは、[Bulma](https://bulma.io/)を使っている

## 02. ページの見た目

![screenshot1.png](https://imgur.com/JXX59k6.png)

![screenshot2.png](https://imgur.com/nT5VFyL.png)

![screenshot3.png](https://imgur.com/LvFtgcW.png)

## 03. HTML コード

```html
<div
  class="box"
  v-on:dragover.prevent="dragging(1)"
  v-on:dragleave.prevent="dragging(0)"
  v-on:drop.prevent="fileDropped"
  v-bind:class="{'has-background-grey-light': isDragging}"
>
  <h1 class="title">ファイルアップロードのテスト</h1>
  <div class="level">
    <div class="level-left">
      <div class="level-item">
        <div class="file has-name">
          <label class="file-label">
            <input
              class="file-input"
              type="file"
              name="file"
              v-on:change.prevent="fileSelected"
            />
            <span class="file-cta">
              <span class="file-icon">
                <i class="fas fa-upload"></i>
              </span>
              <span class="file-label">
                ファイルを選択
              </span>
            </span>
            <span class="file-name" style="width: 300px;">
              {{selectedFilename}}
            </span>
          </label>
        </div>
        <!-- /file -->
      </div>
      <div class="level-item">
        <button
          v-on:click="fileUpload"
          class="button is-primary"
          v-bind:disabled="button_disabled"
        >
          アップロード
        </button>
      </div>
    </div>
  </div>
  <!-- /level -->
</div>
<!-- /box -->
```

## 04. JS コード

```javascript
const app = new Vue({
  el: '#app',
  data: {
    selectedFilename: '',
    uploadFile: null,
    button_disabled: true,
    isDragging: false,
  },
  methods: {
    dragging: function(n) {
      if (n == 1) {
        this.isDragging = true;
      } else {
        this.isDragging = false;
      }
    },
    fileSelected: function(e) {
      this.uploadFile = e.target.files[0];
      this.selectedFilename = this.uploadFile.name;
      this.button_disabled = false;
    },
    fileDropped: function(e) {
      this.isDragging = false;
      this.uploadFile = e.dataTransfer.files[0];
      this.selectedFilename = this.uploadFile.name;
      this.button_disabled = false;
    },
    fileUpload: function() {
      let formData = new FormData();
      formData.append('uploadFile', this.uploadFile);
      let config = {
        headers: {
          'content-type': 'multipart/form-data',
        },
      };
      axios
        .post('/api/avocado/upload', formData, config)
        .then(() => {
          // 成功
        })
        .catch(error => {
          // 失敗
        });
    },
  },
});
```

### File オブジェクトとは

- ファイル選択から呼ばれた場合、`event.target.files`からファイルオブジェクトを取得できる
- ドラッグ&ドロップの場合、`event.dataTransfer.files`でファイルオブジェクトを取得することができる
- File オブジェクトには 3 つのプロパティが用意されている
  - name ... ファイル名 (読み取り専用)。このプロパティはファイル名のみを持ち、パスに関する情報は何も持ちあわせていない
  - size ... ファイルサイズ (読み取り専用)。64 ビット整数のバイトで表現される
  - type ... ファイルの MIME タイプ (読み取り専用)。MIME タイプが決定できないときは空文字列 ("") を返す

### FormData クラスとは

FormData インターフェイスは、`XMLHttpRequest.send()`メソッドを用いることで、簡単に送信が可能な、フォームフィールドおよびそれらの値から表現されるキーと値のペアのセットを簡単に構築する手段を提供する

これは、エンコーディングタイプを`multipart/form-data`に設定した場合にフォームが使用するものと同じ形式を使用する

## 05. サーバーサイドの Python コード

```python
from os import path

from flask import Blueprint, current_app, jsonify, request

avocado = Blueprint('avocado', __name__, url_prefix='/api/avocado')

@avocado.route('/upload', methods=['POST'])
def upload():
    current_app.logger.debug(f'/api/avocado/upload {request.method} access')
    if 'uploadFile' not in request.files:
        return jsonify({'success': False})
    file = request.files['uploadFile']
    file_name = file.filename
    if file_name == '':
        return jsonify({'success': False})
    saveFullPass = path.join('./temp', file_name)
    file.save(saveFullPass)
    return jsonify({'success': True})
```

- 送信されたファイルは、`request.files['uploadFile']`に入っている
