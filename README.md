# Phoenix Dev experiment containers (No Ecto)

[![miolab](https://circleci.com/gh/miolab/phoenix_dev_containers_no_ecto.svg?style=svg)](https://github.com/miolab/phoenix_dev_containers_no_ecto)

Elixir製Webフレームワーク __Phoenix__ の `--no-ecto`版 __Docker__ 開発環境を構築します

---

## 実行環境・バージョン

- macOS
- Elixir 1.11.2 (compiled with Erlang/OTP 23)
- Phoenix 1.5.7

## ビルド〜Phoenixプロジェクト作成

```terminal
$ docker-compose build
```

```terminal
$ docker-compose run --rm app mix phx.new my_app --no-ecto

Creating network "phoenix_dev_containers_no_ecto_default" with the default driver
Creating phoenix_dev_containers_no_ecto_app_run ... done
* creating my_app/config/config.exs
* creating my_app/config/dev.exs
* creating my_app/config/prod.exs
* creating my_app/config/prod.secret.exs
* creating my_app/config/test.exs
* creating my_app/lib/my_app/application.ex
* creating my_app/lib/my_app.ex
* creating my_app/lib/my_app_web/channels/user_socket.ex
* creating my_app/lib/my_app_web/views/error_helpers.ex
* creating my_app/lib/my_app_web/views/error_view.ex
* creating my_app/lib/my_app_web/endpoint.ex
* creating my_app/lib/my_app_web/router.ex
* creating my_app/lib/my_app_web/telemetry.ex
* creating my_app/lib/my_app_web.ex
* creating my_app/mix.exs
* creating my_app/README.md
* creating my_app/.formatter.exs
* creating my_app/.gitignore
* creating my_app/test/support/channel_case.ex
* creating my_app/test/support/conn_case.ex
* creating my_app/test/test_helper.exs
* creating my_app/test/my_app_web/views/error_view_test.exs
* creating my_app/lib/my_app_web/controllers/page_controller.ex
* creating my_app/lib/my_app_web/templates/layout/app.html.eex
* creating my_app/lib/my_app_web/templates/page/index.html.eex
* creating my_app/lib/my_app_web/views/layout_view.ex
* creating my_app/lib/my_app_web/views/page_view.ex
* creating my_app/test/my_app_web/controllers/page_controller_test.exs
* creating my_app/test/my_app_web/views/layout_view_test.exs
* creating my_app/test/my_app_web/views/page_view_test.exs
* creating my_app/lib/my_app_web/gettext.ex
* creating my_app/priv/gettext/en/LC_MESSAGES/errors.po
* creating my_app/priv/gettext/errors.pot
* creating my_app/assets/webpack.config.js
* creating my_app/assets/.babelrc
* creating my_app/assets/js/app.js
* creating my_app/assets/css/app.scss
* creating my_app/assets/js/socket.js
* creating my_app/assets/package.json
* creating my_app/assets/static/favicon.ico
* creating my_app/assets/css/phoenix.css
* creating my_app/assets/static/images/phoenix.png
* creating my_app/assets/static/robots.txt

Fetch and install dependencies? [Yn]
* running mix deps.get
* running mix deps.compile
* running cd assets && npm install && node node_modules/webpack/bin/webpack.js --mode development

We are almost there! The following steps are missing:

    $ cd my_app

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
phoenix_dev_containers_no_ecto_app_1   sh -c cd my_app && mix phx ...   Up      0.0.0.0:4000->4000/tcp
```

### ブラウザ確認

- [http://localhost:4000/](http://localhost:4000/)

  <img alt="phx_init_page" src="https://user-images.githubusercontent.com/33124627/100324924-3c566300-300b-11eb-9f84-e5ff80c11a07.png" width="455px">

### フォーマッティングとテスト

```terminal
$ docker-compose exec app bash -c "cd my_app && mix format"
```

```terminal
$ docker-compose exec app bash -c "cd my_app && mix test"

==> gettext
Compiling 1 file (.erl)
Compiling 20 files (.ex)
Generated gettext app
===> Compiling ranch
===> Compiling telemetry
==> telemetry_metrics
Compiling 7 files (.ex)
Generated telemetry_metrics app
===> Compiling telemetry_poller
==> jason
Compiling 8 files (.ex)
Generated jason app
==> phoenix_pubsub
Compiling 11 files (.ex)
Generated phoenix_pubsub app
===> Compiling cowlib
===> Compiling cowboy
===> Compiling cowboy_telemetry
==> mime
Compiling 2 files (.ex)
Generated mime app
==> plug_crypto
Compiling 5 files (.ex)
Generated plug_crypto app
==> plug
Compiling 1 file (.erl)
Compiling 41 files (.ex)
warning: System.stacktrace/0 is deprecated, use __STACKTRACE__ instead
  lib/plug/conn/wrapper_error.ex:23

Generated plug app
==> phoenix_html
Compiling 8 files (.ex)
Generated phoenix_html app
==> plug_cowboy
Compiling 5 files (.ex)
Generated plug_cowboy app
==> phoenix
Compiling 66 files (.ex)
Generated phoenix app
==> phoenix_live_view
Compiling 22 files (.ex)
Generated phoenix_live_view app
==> phoenix_live_dashboard
Compiling 36 files (.ex)
Generated phoenix_live_dashboard app
==> my_app
Compiling 15 files (.ex)
Generated my_app app
...

Finished in 0.3 seconds
3 tests, 0 failures

Randomized with seed 213195
```

## 新規ページ追加

新規ページ `/watchme` を追加します

- ルーティング設定（アップデート）

  `app/my_app/lib/my_app_web/router.ex`

  ```elixir
  scope "/", MyAppWeb do
  pipe_through :browser

  get "/", PageController, :index
  get "/watchme", WatchmeController, :index    # --> add
  end
  ```

- コントローラー追加（新規作成）

  `app/my_app/lib/my_app_web/controllers/watchme_controller.ex`

  ```elixir
  defmodule MyAppWeb.WatchmeController do
    use MyAppWeb, :controller

    def index(conn, _params) do
      render(conn, "index.html")
    end
  end
  ```

- ビュー追加（新規作成）

  `app/my_app/lib/my_app_web/views/watchme_view.ex`

  ```elixir
  defmodule MyAppWeb.WatchmeView do
    use MyAppWeb, :view
  end
  ```

- テンプレート追加（新規作成）

  `app/my_app/lib/my_app_web/templates/watchme/index.html.eex`

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
  $ docker-compose exec app bash -c "cd my_app && mix phx.routes"

          page_path  GET  /                                      MyAppWeb.PageController :index
      watchme_path  GET  /watchme                               MyAppWeb.WatchmeController :index
  live_dashboard_path  GET  /dashboard                             Phoenix.LiveView.Plug :home
  live_dashboard_path  GET  /dashboard/:page                       Phoenix.LiveView.Plug :page
  live_dashboard_path  GET  /dashboard/:node/:page                 Phoenix.LiveView.Plug :page
          websocket  WS   /live/websocket                        Phoenix.LiveView.Socket
          longpoll  GET  /live/longpoll                         Phoenix.LiveView.Socket
          longpoll  POST  /live/longpoll                         Phoenix.LiveView.Socket
          websocket  WS   /socket/websocket                      MyAppWeb.UserSocket
  ```

### ブラウザ確認

- [http://localhost:4000/watchme](http://localhost:4000/watchme)

  <img src="https://user-images.githubusercontent.com/33124627/99958502-d2507a80-2dcc-11eb-8ba3-b89612fb1f60.png" width="455px">

---

## （参考） プロジェクト・コンテナ環境削除

```terminal
$ docker-compose down --rmi all --volumes --remove-orphans

$ rm -rf app/my_app
```
