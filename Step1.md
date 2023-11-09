# テーブル設計
> 仕様について

好きな時間に好きな場所で話題の動画を無料で楽しめる「インターネットTVサービス」を新規に作成することになりました。データベース設計をした上で、データ取得する SQL を作成してください。

仕様は次の通りです。サービスのイメージとしては ABEMA を頭に思い浮かべてください。

- ドラマ1、ドラマ2、アニメ1、アニメ2、スポーツ、ペットなど、複数のチャンネルがある

- 各チャンネルの下では時間帯ごとに番組枠が1つ設定されており、番組が放映される
番組はシリーズになっているものと単発ものがある。シリーズになっているものはシーズンが1つものと、シーズン1、シーズン2のように複数シーズンのものがある。各シーズンの下では各エピソードが設定されている

- 再放送もあるため、ある番組が複数チャンネルの異なる番組枠で放映されることはある

- 番組の情報として、タイトル、番組詳細、ジャンルが画面上に表示される

- 各エピソードの情報として、シーズン数、エピソード数、タイトル、エピソード詳細、動画時間、公開日、視聴数が画面上に表示される。単発のエピソードの場合はシーズン数、エピソード数は表示されない

- ジャンルとしてアニメ、映画、ドラマ、ニュースなどがある。各番組は1つ以上のジャンルに属する

- KPIとして、チャンネルの番組枠のエピソードごとに視聴数を記録する。なお、一つのエピソードは複数の異なるチャンネル及び番組枠で放送されることがあるので、属するチャンネルの番組枠ごとに視聴数がどうだったかも追えるようにする

番組、シーズン、エピソードの関係について、以下のようなイメージです(シリーズになっているものの例)。

番組：鬼滅の刃
シーズン：1
エピソード：1話、2話、...、26話


# channels
| カラム名      | データ型    | NULL | キー     | 初期値 | AUTO INCREMENT |
|--------------|------------|------|----------|--------|----------------|
| channel_id   | INT        | NO   | PRIMARY  |        | YES            |
| channel_name | VARCHAR(100)| NO  | UNIQUE   |        |                |

# programs
| カラム名      | データ型    | NULL | キー     | 初期値 | AUTO INCREMENT |
|--------------|------------|------|----------|--------|----------------|
| program_id   | INT        | NO   | PRIMARY  |        | YES            |
| title        | VARCHAR(100)| NO  |          |        |                |
| description  | TEXT       | YES  |          |        |                |
| channel_id   | INT        | NO   | FOREIGN  |        |                |

# broadcast_dates
| カラム名      | データ型    | NULL | キー     | 初期値 | AUTO INCREMENT |
|--------------|------------|------|----------|--------|----------------|
| broadcast_id | INT        | NO   | PRIMARY  |        | YES            |
| start_time   | DATETIME   | NO   |          |        |                |
| end_time     | DATETIME   | NO   |          |        |                |

# program_broadcast_dates
| カラム名      | データ型    | NULL | キー     | 初期値 | AUTO INCREMENT |
|--------------|------------|------|----------|--------|----------------|
| pbd_id       | INT        | NO   | PRIMARY  |        | YES            |
| program_id   | INT        | NO   | FOREIGN  |        |                |
| broadcast_id | INT        | NO   | FOREIGN  |        |                |

# genres
| カラム名      | データ型    | NULL | キー     | 初期値 | AUTO INCREMENT |
|--------------|------------|------|----------|--------|----------------|
| genre_id     | INT        | NO   | PRIMARY  |        | YES            |
| genre_name   | VARCHAR(100)| YES |          |        |                |

# program_genres
| カラム名      | データ型    | NULL | キー     | 初期値 | AUTO INCREMENT |
|--------------|------------|------|----------|--------|----------------|
| program_id   | INT        | NO   | PRIMARY  |        |                |
| genre_id     | INT        | NO   | PRIMARY  |        |                |


# episodes
| カラム名            | データ型      | NULL | キー     | 初期値 | AUTO INCREMENT |
|--------------------|--------------|------|----------|--------|----------------|
| episode_id         | INT          | NO   | PRIMARY  |        | YES            |
| program_id        | INT          | NO | FOREIGN  |        |                |
| season_number       | INT          | YES  | FOREIGN  |        |                |
| episode_number     | INT          | YES  |          |        |                |
| episode_title      | VARCHAR(255) | NO  |          |        |                |
| episode_description| TEXT         | YES  |          |        |                |
| video_time         | TIME         | NO  |          |        |                |

# episode_views
| カラム名      | データ型    | NULL | キー     | 初期値 | AUTO INCREMENT |
|--------------|------------|------|----------|--------|----------------|
| view_id      | INT        | NO   | PRIMARY  |        | YES            |
| episode_id   | INT        | NO   | FOREIGN  |        |                |
| pbd_id       | INT        | NO   | FOREIGN  |        |                |
| view_count   | INT        | YES  |          | 0      |                |

# users
| カラム名      | データ型    | NULL | キー     | 初期値 | AUTO INCREMENT |
|--------------|------------|------|----------|--------|----------------|
| user_id      | INT        | NO   | PRIMARY  |        | YES            |
| user_name    | VARCHAR(100)| NO  |          |        |                |
| email        | VARCHAR(100)| NO  | INDEX    |        |                |
| password     | VARCHAR(100)| NO  |          |        |                |
