# Phoenix Dev experiment containers (No Ecto)

[![miolab](https://circleci.com/gh/miolab/phoenix_dev_containers_no_ecto.svg?style=svg)](https://github.com/miolab/phoenix_dev_containers_no_ecto)

Elixir製Webフレームワーク __Phoenix__ の `--no-ecto`版 __Docker__ 開発環境を構築します

## 実行環境・バージョン

- macOS
- Elixir 1.11.3 (compiled with Erlang/OTP 23)
- Phoenix 1.5.7
- Node 14.15.4

## 確認URL

- テスト環境 http://0.0.0.0:4000

---

## ビルド〜Phoenixプロジェクト作成

```terminal
$ docker-compose build
```

```terminal
$ docker-compose run --rm app mix phx.new . --app my_app --no-ecto

Creating phoenix_dev_containers_no_ecto_app_run ... done
The directory /usr/src/app already exists. Are you sure you want to continue? [Yn]
* creating config/config.exs
* creating config/dev.exs
* creating config/prod.exs
* creating config/prod.secret.exs
* creating config/test.exs
* creating lib/my_app/application.ex
* creating lib/my_app.ex
* creating lib/my_app_web/channels/user_socket.ex
* creating lib/my_app_web/views/error_helpers.ex
* creating lib/my_app_web/views/error_view.ex
* creating lib/my_app_web/endpoint.ex
* creating lib/my_app_web/router.ex
* creating lib/my_app_web/telemetry.ex
* creating lib/my_app_web.ex
* creating mix.exs
* creating README.md
* creating .formatter.exs
* creating .gitignore
* creating test/support/channel_case.ex
* creating test/support/conn_case.ex
* creating test/test_helper.exs
* creating test/my_app_web/views/error_view_test.exs
* creating lib/my_app_web/controllers/page_controller.ex
* creating lib/my_app_web/templates/layout/app.html.eex
* creating lib/my_app_web/templates/page/index.html.eex
* creating lib/my_app_web/views/layout_view.ex
* creating lib/my_app_web/views/page_view.ex
* creating test/my_app_web/controllers/page_controller_test.exs
* creating test/my_app_web/views/layout_view_test.exs
* creating test/my_app_web/views/page_view_test.exs
* creating lib/my_app_web/gettext.ex
* creating priv/gettext/en/LC_MESSAGES/errors.po
* creating priv/gettext/errors.pot
* creating assets/webpack.config.js
* creating assets/.babelrc
* creating assets/js/app.js
* creating assets/css/app.scss
* creating assets/js/socket.js
* creating assets/package.json
* creating assets/static/favicon.ico
* creating assets/css/phoenix.css
* creating assets/static/images/phoenix.png
* creating assets/static/robots.txt

Fetch and install dependencies? [Yn]
* running mix deps.get
* running mix deps.compile
* running cd assets && npm install && node node_modules/webpack/bin/webpack.js --mode development

We are almost there! The following steps are missing:

    $ cd app

Start your Phoenix app with:

    $ mix phx.server

You can also run your app inside IEx (Interactive Elixir) as:

    $ iex -S mix phx.server
```

```terminal
$ docker-compose up -d
```

```terminal
$ docker-compose ps

                Name                              Command               State           Ports
------------------------------------------------------------------------------------------------------
phoenix_dev_containers_no_ecto_app_1   sh -c mix phx.server --no-halt   Up      0.0.0.0:4000->4000/tcp
```

### ブラウザ確認

- [http://localhost:4000/](http://localhost:4000/)

  <img alt="phx_init_page" src="https://user-images.githubusercontent.com/33124627/100324924-3c566300-300b-11eb-9f84-e5ff80c11a07.png" width="455px">

### フォーマッティングとテスト

```terminal
$ docker-compose exec app bash -c "mix format"
```

```terminal
$ docker-compose exec app bash -c "mix test"

...

Finished in 0.3 seconds
3 tests, 0 failures

Randomized with seed 213195
```

## 新規ページ追加

新規ページ `/watchme` を追加します

- ルーティング設定（アップデート）

  `app/lib/my_app_web/router.ex`

  ```elixir
  scope "/", MyAppWeb do
  pipe_through :browser

  get "/", PageController, :index
  get "/watchme", WatchmeController, :index    # --> add
  end
  ```

- コントローラー追加（新規作成）

  `app/lib/my_app_web/controllers/watchme_controller.ex`

  ```elixir
  defmodule MyAppWeb.WatchmeController do
    use MyAppWeb, :controller

    def index(conn, _params) do
      render(conn, "index.html")
    end
  end
  ```

- ビュー追加（新規作成）

  `app/lib/my_app_web/views/watchme_view.ex`

  ```elixir
  defmodule MyAppWeb.WatchmeView do
    use MyAppWeb, :view
  end
  ```

- テンプレート追加（新規作成）

  `app/lib/my_app_web/templates/watchme/index.html.eex`

  ```html
  <section class="phx-hero">
    <h1>オレオレコンテナをぜひ見てくれ</h1>
  </section>
  <section class="row">
    <p>メリークリスマス！ 2020！<br>
      プレゼンテッド・バイ im（あいえむ）</p>
  </section>
  ```

- コンテナを再起動して、ルーティングを確認

  ```
  $ docker-compose restart app
  ```

  ```terminal
  $ docker-compose exec app mix phx.routes

            page_path  GET  /                                      MyAppWeb.  PageController :index
         watchme_path  GET  /watchme                               MyAppWeb.  WatchmeController :index
  live_dashboard_path  GET  /dashboard                             Phoenix.LiveView.Plug  :home
  live_dashboard_path  GET  /dashboard/:page                       Phoenix.LiveView.Plug  :page
  live_dashboard_path  GET  /dashboard/:node/:page                 Phoenix.LiveView.Plug  :page
            websocket  WS   /live/websocket                        Phoenix.LiveView.  Socket
             longpoll  GET  /live/longpoll                         Phoenix.LiveView.  Socket
             longpoll  POST  /live/longpoll                         Phoenix.LiveView. Socket
            websocket  WS   /socket/websocket                      MyAppWeb.UserSocket
  ```

### ブラウザ確認

- [http://localhost:4000/watchme](http://localhost:4000/watchme)

  <img src="https://user-images.githubusercontent.com/33124627/99958502-d2507a80-2dcc-11eb-8ba3-b89612fb1f60.png" width="455px">

---

## （補足） プロジェクト・コンテナ環境削除

```terminal
$ docker-compose down --rmi all --volumes --remove-orphans

$ rm -rf app/my_app
```

---

## 参考情報

- Credo
  - Docs https://hexdocs.pm/credo/overview.html
  - GitHub https://github.com/rrrene/credo
