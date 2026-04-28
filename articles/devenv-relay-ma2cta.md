---
title: "Claude Code の「1.ラーメン」を物理ボタンで殴る"
emoji: "🎛️"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [claudecode, streamdeck, python]
published: true
publication_name: "smartcamp"
---

:::message
この記事は [SMARTCAMP Engineer Blog](https://zenn.dev/p/smartcamp) のリレー記事企画：[「開発環境」のリレー記事、始めます](https://zenn.dev/smartcamp/articles/22d5fa14a09212) の 3 本目の記事です。
:::

みなさんこんにちは！今日も元気に Vibe Coding！スマートキャンプ株式会社の ma2Cta です。開発環境リレー記事ということで、私からは一つ小ネタを投稿しようと思います。

# Claude Code は承認を求めてくる

Claude Code を使ったコーディング、みなさん行なっていますよね。私も、公私共に Claude Code をヘビーユースしています。

そんな Claude Code ですが、よく我々人間に承認や確認を求めてきます。Bash コマンドの実行、ファイルの編集、MCP ツール経由の外部操作など、副作用のあるアクションを行う直前で承認プロンプトが出てきます。オートで突き進むモードに切り替えることもできますが、ある程度は人間が監視・承認しながら進めたいシーンも多いはずです。そういった場合、いかにこのプロンプトを素早く捌くかが Claude Code 体験を左右します。

![](/images/devenv-relay/confirm_claude.png =500x)

焼肉が食べたい！という気分のときは、5 の Type something を選択して自分で入力するしかありません。しかし、私はラーメンが大好きなので 1 のラーメンを選択したいです。このとき、たくさんあるキーの中から 1 を探して押すのも面倒な時ってありますよね。

そんなときは、Stream Deck を使いましょう。

# Stream Deck とは

[公式ページ](https://www.elgato.com/jp/ja/p/stream-deck) を見ていただいた方が早いですが、端的にいうと物理ボタンがたくさん存在するデバイスです。各ボタンに様々なアクションを割り当てることができます。Stream という名前を冠するように、配信といった画面からあまり目を離せない作業を行なっている人が、定型的なアクションをワンボタンで実行する場合に利用する用途が多いようです。サイズ違いなど様々なラインナップがありますが、私はボタン数の多い Stream Deck XL を利用しています。

このデバイスを利用して、Claude Code に指示を出しましょう。

# 私の設定

![](/images/devenv-relay/config_top.png =500x)

上記の画像のような設定アプリケーションから、各ボタンにアクションを割り当てます。設定を順番に説明します。

![](/images/devenv-relay/config_iterm2.png =500x)

私はターミナルとして iTerm2 を利用しているので、最上段の列は iTerm2 のタブを切り替えるためのボタンを並べています。

![](/images/devenv-relay/config_iterm2_detail.png =500x)

複数のアクションを順番に実行できる「マルチアクション」を利用しています。まず、iTerm2 にフォーカスを持っていき、Cmd+1 などを入力してタブを表示します。

![](/images/devenv-relay/config_option.png =500x)

アルファベット、数字、yes や no を入力するためのボタンです。

![](/images/devenv-relay/config_option_detail.png =500x)

「テキスト」アクションを利用して、文字列を入力させています。承認プロンプトが表示されている場合は必要がないケースが多いですが、「メッセージの後に"Enter"キーを押す」にもチェックを入れています。オープンクエスチョン的に Claude Code が尋ねてきた場合には、効きます。

![](/images/devenv-relay/config_text.png =500x)

Skills を起動したり、まれに止まる Claude 君に続きを促すための定型文を入力するためのボタンです。こちらも同様に「メッセージの後に"Enter"キーを押す」にもチェックを入れています。

# ボタン画像の作り方

ここまで紹介したボタンですが、最初は Stream Deck の標準アイコンを使っていました。ただ、テキスト入力アクションのアイコンは「T」マークで全部同じ見た目になっていて、ボタンが増えてくると一目でどれがどれだか判別できなくなってしまいました。というわけで、Claude Code に作らせました。

最初に投げた依頼はこんな感じです。

> Stream Deck で Claude Code 用のテキスト入力を設定しています。アイコンの「T」が視認性が悪いので入力するテキスト用のアイコンを用意したい。

そこから対話で要件を詰めていきました。

- 色をカテゴリ別に分けたい（英字 / 数字 / 日本語 / yes / no）
- 右下に小さく >\_ のプロンプト風インジケータがあると嬉しい
- ラベル一覧: A / B / C / D / 1 / 2 / 3 / レビュー / 続けて / yes / no

途中でアイコンサイズの話になったので、Stream Deck のキー画像の推奨サイズも一緒に調べてくれました。

| 機種                        | キーアイコン推奨サイズ |
| --------------------------- | ---------------------- |
| Stream Deck Original / MK.2 | 72×72 px               |
| Stream Deck XL              | 96×96 px               |
| Stream Deck +               | 120×120 px             |

機種ごとに違うのですが、Stream Deck 側で自動的に縮小してくれるとのことだったので、最大の 120×120 px よりさらに大きい 144×144 px で出力させることにしました。これなら、後で機種を変えてもそのまま使い回せるはずです。

中身を眺めてみたところ、Python + Pillow で書いた、ざっくり以下のようなスクリプトでした。

```python
ICONS = [
    ("A", "text_a", "#1E3A8A", "#FFFFFF"),
    ("B", "text_b", "#1E40AF", "#FFFFFF"),
    # ...
    ("yes", "text_yes", "#15803D", "#FFFFFF"),
    ("no",  "text_no",  "#B91C1C", "#FFFFFF"),
]

def pick_font_size(label, font_path, max_w, max_h):
    """ラベルが max_w × max_h に収まる最大フォントサイズを二分探索."""
    low, high = 16, 120
    best = ImageFont.truetype(font_path, low)
    while low <= high:
        mid = (low + high) // 2
        font = ImageFont.truetype(font_path, mid)
        bbox = font.getbbox(label)
        w, h = bbox[2] - bbox[0], bbox[3] - bbox[1]
        if w <= max_w and h <= max_h:
            best, low = font, mid + 1
        else:
            high = mid - 1
    return best
```

ちなみに、画像を各ボタンに設定するところまで Claude Code にやらせたかったのですが、Stream Deck の設定アプリが管理するディレクトリを直接触ることになりそうだったため、あきらめて設定アプリを経由して手動で設定しました。

# どんな風に使っているか

![](/images/devenv-relay/picture.jpg =500x)

分割キーボードの間に置いて使っています。承認プロンプトが出るまでは、ブラウザで他の作業をしていたりもするので、キーボードから手を離していることも多いです。そういった際に、「Claude Code に承認を求められたら、まず Stream Deck を触ればよい」というように思考の流れをパターン化できるのは意外と快適です。おそらく、1 日で 100 回以上は Stream Deck のボタンを押していると思います。

みなさんも是非 Stream Deck を購入して Claude Code を操ってみてください。

# 余談

完全に余談ですが、Stream Deck は MCP に対応しているらしいです。
https://www.theverge.com/tech/905021/elgato-stream-deck-mcp-ai-agent-update

- MCP や CLI を提供していないサービスだが Stream Deck にはプラグインを提供している
- 人間がメインで行う定型アクションだがたまに AI にも実行させたい

といった場合に、もしかしたら役にたつかもしれません。

Stream Deck の本業である配信では、「get my stream ready」と話しかけると OBS の起動・シーン切り替え・照明 ON・音楽再生までを Stream Deck 経由で連鎖実行する、といった事例が紹介されています。
https://www.elgato.com/us/en/explorer/products/stream-deck/sd-mcp-setup/

開発寄りの活用例として面白かったのは、Stream Deck そのものを AI に「設計」させる方向の OSS です。[verygoodplugins/streamdeck-mcp](https://github.com/verygoodplugins/streamdeck-mcp) という MCP サーバーは、「このリポジトリ用の dev deck を Nordic カラーで作って」のような自然言語プロンプトを投げると、配色プランニング・アイコン生成・ボタンへのアクション割り当てまでを Claude が一気通貫で行なってくれるそうです。今回の記事では Claude Code に画像生成しか任せられませんでしたが、こちらを使えばボタン設定そのものを AI に丸投げできるので、ぜひ次に試してみたいなと思っています。
