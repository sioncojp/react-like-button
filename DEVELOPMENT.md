## 下準備

```
mkdir react-like-button
cd react-like-button
npm init

npm install --save-dev webpack babel-loader babel-core babel-preset-react babel-preset-es2015 babel-preset-stage-0
```

```
$ touch webpack.config.js

module.exports = {
  entry: __dirname + "/src/main.js",
  output: {
    path: __dirname + "/dist",
    filename: "like-button.js"
  },
  module: {
    loaders: [
      {test: /\.js$/, loader: "babel-loader"}
    ]
  }
};
```

```
mkdir src dist
touch ./src/main.js
vim ./src/main.js

console.log("OK");
```

```
$ touch dist/index.html

<!doctype html>
<html>
<head>
  <meta charset="UTF-8">
  <title>Reactで作った「いいねボタン」のデモ (Flux不使用)</title>
</head>
<body>
  <h1>Reactで作った「いいねボタン」のデモ (Flux不使用)</h1>
  <script src="like-button.js"></script>
</body>
</html>
```

```
$ vim package.json

{
...
  "scripts": {
    ...
    "build": "rm -rf dist/*.js && webpack",
    "watch": "rm -rf dist/*.js && webpack -w"
  },
...
}
```

```
npm run build
```

## reactインストール

```
npm install --save react react-dom

$ vim dist/index.html
<h1>Reactで作った「いいねボタン」のデモ (Flux不使用)</h1>
<div id="like-button"></div><!-- ここにコンポーネントをマウント -->
<script src="like-button.js"></script>
```

```
$ vim .src/main.js

import React from "react";
import ReactDom from "react-dom";

class LikeButton extends React.Component {
  render() {
    return (
      <span>いいねボタン予定地</span>
    );
  }
}

ReactDom.render(
  <LikeButton />,
  document.getElementById("like-button")
);
```

```
$ vim webpack.config.js

module.exports = {
  entry: __dirname + "/src/main.js",
  output: {
    path: __dirname + "/dist",
    filename: "like-button.js"
  },
  module: {
    loaders: [
      {
        test: /\.js$/,
        loader: "babel-loader",
        query:{ presets:["es2015", "stage-0", "react"] }
      }
    ]
  }
};
```

```
npm run watch
```


### ボタンデザインを作り込む

```

class LikeButton extends React.Component {
  styles() {
    return {
      container: {
        fontFamily: "helvetica, arial, 'hiragino kaku gothic pro', meiryo, 'ms pgothic', sans-serif",
        fontSize: 11
      },
      like: {
        display: "inline-block",
        background: "#3b5998",
        padding: "0px 5px",
        borderRadius: 2,
        color: "#ffffff",
        cursor: "pointer",
        float: "left",
        height: 20,
        lineHeight: "20px"
      },
      likeHover: {
        background: "#444"
      },
      counterBefore: {
        display: "block",
        float: "left",
        width: 6,
        height: 6,
        background: "#fafafa",
        marginLeft: "-12px",
        borderRight: 10,
        transform: "rotate(45deg)",
        WebkitTransform: "rotate(45deg)",
        marginTop: 6,
        borderLeft: "1px solid #aaa",
        borderBottom: "1px solid #aaa"
      },
      counter: {
        display: "block",
        background: "#fafafa",
        boxSizing: "border-box",
        border: "1px solid #aaa",
        float: "left",
        padding: "0px 8px",
        borderRadius: 2,
        marginLeft: 8,
        height: 20,
        lineHeight: "20px"
      }
    };
  }

  render() {
    const styles = this.styles();
    return (
      <span style={styles.container}>
        <span style={styles.like}>いいね！</span>
        <span style={styles.counter}>
          <span style={styles.counterBefore}>{" "}</span>999
        </span>
      </span>
    );
  }
}
```

```
npm run watch
```

## ボタンが乗ったら色が変わる

```
class LikeButton extends React.Component {
  constructor(props) {
    super(props);
    // コンポーネントにカーソルが乗ているかどうかの状態を持たせる
    this.state = {
      hovered: false
    }
  }

  styles() { ... }

  // カーソルが乗った時に状態を変更するイベントハンドラ
  onMouseEnter() {
    this.setState({hovered: true});
  }

  // カーソルが外れた時に状態を変更するイベントハンドラ
  onMouseLeave() {
    this.setState({hovered: false});
  }

  render() {
    const styles = this.styles();
    // 状態に応じてスタイルを変更する
    const likeStyle = this.state.hovered ? {...styles.like, ...styles.likeHover} : styles.like;
    return (
      <span style={styles.container}>
        <span
          style={likeStyle}
          onMouseEnter={::this.onMouseEnter}
          onMouseLeave={::this.onMouseLeave}>いいね！</span>
        <span style={styles.counter}>
          <span style={styles.counterBefore}>{" "}</span>999
        </span>
      </span>
    );
  }
}
```

## 数値を上げ下げ
```
constructor(props) {
  super(props);
  this.state = {
    hovered: false,
    count: 999,   // カウント数の状態を追加
    liked: false  // 押したかどうかの状態を追加
  }
}

// クリックしたときのイベントハンドラ
onClick() {
  this.setState({
    count: this.state.count + (this.state.liked ? -1 : 1),
    liked: !this.state.liked
  });
}

// this.state.likedとthis.state.countを追加し、
// onClickイベントハンドラを紐付ける
return (
  <span style={styles.container}>
    <span
      style={likeStyle}
      onMouseEnter={::this.onMouseEnter}
      onMouseLeave={::this.onMouseLeave}
      onClick={::this.onClick}>{this.state.liked ? "✔ " : ""}いいね！</span>
    <span style={styles.counter}>
      <span style={styles.counterBefore}>{" "}</span>
      {this.state.count}
    </span>
  </span>
);
}
```
