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

| カラム名       | データ型        | NULL | キー     | 初期値 | AUTO INCREMENT |
| -------------- | --------------- | ---- | -------- | ------ | -------------- |
| channel_id     | int             |      | PRIMARY  |        | YES            |
| channel_name   | varchar(100)    |      |          |        |                |


* ユニークキー制約：channel_nameカラムに設定

# programs
| カラム名 | データ型 | NULL | キー | 初期値 | AUTO INCREMENT |
|----------|----------|------|------|--------|----------------|
| program_id | int | | PRIMARY | | YES |
| title | varchar(100) | | INDEX | | |
| description | text | YES | | | |

# genres
| カラム名 | データ型 | NULL | キー | 初期値 | AUTO INCREMENT |
|----------|----------|------|------|--------|----------------|
| genre_id | int | | PRIMARY | | YES |
| genre_name | varchar(100) | YES | | | |

# program_genres
| カラム名 | データ型 | NULL | キー | 初期値 | AUTO INCREMENT |
|----------|----------|------|------|--------|----------------|
| program_id | int | |PRIMARY | | |
| genre_id | int | |PRIMARY | | |
###### 外部キー制約:
- program_idに対して、programsテーブルのprogram_idから設定
- genre_idに対して、genresテーブルのgenre_idから設定

# seasons
| カラム名 | データ型 | NULL | キー | 初期値 | AUTO INCREMENT |
|----------|----------|------|------|--------|----------------|
| season_id | int | | PRIMARY | | YES |
| program_id | int | | | | |
| season_number | int | YES | | | |

###### 外部キー制約：
- program_idに対して、programsテーブルのprogram_idカラムから設定

# episodes
| カラム名 | データ型 | NULL | キー | 初期値 | AUTO INCREMENT |
|----------|----------|------|------|--------|----------------|
| episode_id | int | | PRIMARY | | YES |
| season_id | int | YES | | | |
| episode_number | int | YES | | | |
| episode_title | varchar(255) | | | | |
| episode_description | text | YES | | | |
| video_time | time | | | | |

###### 外部キー制約：
- season_idに対して、seasonsテーブルのseason_idカラムから設定

# Usersテーブル
| カラム名 | データ型 | NULL | キー | 初期値 | AUTO INCREMENT |
|----------|----------|------|------|--------|----------------|
| user_id | int | | PRIMARY | | YES |
| user_name | varchar(100) | | | | |
| email | varchar(100) | | | | |
| password | varchar(100) | | | | |
* ユニークキー制約：emailカラムに設定

# episode_views
| カラム名 | データ型 | NULL | キー | 初期値 | AUTO INCREMENT |
|----------|----------|------|------|--------|----------------|
| view_id | int | | PRIMARY | | YES |
| channel_id | int | | INDEX | | |
| episode_id | int | | | | |
| broadcast_date | date | | | | |
| view_count | int | | | 0 | |
###### 外部キー制約：
- channel_idに対して、channelsテーブルのchannel_idカラムから設定
- episode_idに対して、episodesテーブルのepisode_idカラムから設定

