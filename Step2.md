# データベース構築からサンプルデータを入れるまでの手順

## 1. データベースを構築する
MySQLにログインするためにターミナルで次のようなコマンドを使用します。

```
mysql -u username -p
```
username はあなたのMySQLユーザー名に置き換えます。

コマンドを実行すると、MySQLはパスワードを要求します。
画面には表示されないので、入力後に Enter キーを押してログインします。

ログイン後に次のコマンドを使用してデータベースを作成します：
```sql
CREATE DATABASE internet_tv;
```

作成したデータベースを使用するために、次のコマンドでデータベースを選択します：
```sql
USE internet_tv;
```
## ２. 設計したテーブルを作成します

CREATE TABLE...を使用してStep1で設計したテーブルを作成していきます。
下記のコードをコピペし、実行する。

```sql
CREATE TABLE channels (
    channel_id INT AUTO_INCREMENT PRIMARY KEY,
    channel_name VARCHAR(100) NOT NULL UNIQUE
);

CREATE TABLE programs (
    program_id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(100) NOT NULL,
    description TEXT,
    channel_id INT NOT NULL,
    FOREIGN KEY (channel_id) REFERENCES channels(channel_id)
);

CREATE TABLE broadcast_dates (
    broadcast_id INT AUTO_INCREMENT PRIMARY KEY,
    start_time DATETIME NOT NULL,
    end_time DATETIME NOT NULL
);

CREATE TABLE program_broadcast_dates (
    pbd_id INT AUTO_INCREMENT PRIMARY KEY,
    program_id INT NOT NULL,
    broadcast_id INT NOT NULL,
    FOREIGN KEY (program_id) REFERENCES programs(program_id),
    FOREIGN KEY (broadcast_id) REFERENCES broadcast_dates(broadcast_id)
);

CREATE TABLE genres (
    genre_id INT AUTO_INCREMENT PRIMARY KEY,
    genre_name VARCHAR(100)
);

CREATE TABLE program_genres (
    program_id INT NOT NULL,
    genre_id INT NOT NULL,
    PRIMARY KEY (program_id, genre_id),
    FOREIGN KEY (program_id) REFERENCES programs(program_id),
    FOREIGN KEY (genre_id) REFERENCES genres(genre_id)
);

CREATE TABLE episodes (
    episode_id INT AUTO_INCREMENT PRIMARY KEY,
    program_id INT NOT NULL,
    season_number INT,
    episode_number INT,
    episode_title VARCHAR(255) NOT NULL,
    episode_description TEXT,
    video_time TIME NOT NULL,
    FOREIGN KEY (program_id) REFERENCES programs(program_id)
);

CREATE TABLE episode_views (
    view_id INT AUTO_INCREMENT PRIMARY KEY,
    episode_id INT NOT NULL,
    pbd_id INT NOT NULL,
    view_count INT DEFAULT 0,
    FOREIGN KEY (episode_id) REFERENCES episodes(episode_id),
    FOREIGN KEY (pbd_id) REFERENCES program_broadcast_dates(pbd_id)
);

CREATE TABLE users (
    user_id INT AUTO_INCREMENT PRIMARY KEY,
    user_name VARCHAR(100) NOT NULL,
    email VARCHAR(100) NOT NULL UNIQUE,
    password VARCHAR(100) NOT NULL
);



```
実行した後、下記コマンドでテーブルが作成できているかを確認する。
```sql
SHOW TABLES;
```
下記のようになっていればOKです
```sql
mysql> SHOW TABLES;
+-------------------------+
| Tables_in_internet_tv   |
+-------------------------+
| broadcast_dates         |
| channels                |
| episode_views           |
| episodes                |
| genres                  |
| program_broadcast_dates |
| program_genres          |
| programs                |
| users                   |
+-------------------------+
```

## 3. サンプルデータを入れます

下記のコードをコピペし実行する。

channelsテーブル
```sql
INSERT INTO channels (channel_name) VALUES
('ドラマ'),
('アニメ'),
('スポーツ'),
('ペット'),
('釣り'),
('料理'),
('バラエティ');
```

programsテーブル
```sql
INSERT INTO programs (title, description, channel_id) VALUES
('ドラマの世界', 'ドラマチックな展開が楽しめる番組です。', 1),
('アニメヒーローズ', '勇気と冒険に満ちたアニメシリーズ。', 2),
('スポーツバトル', '最新のスポーツ試合と分析。', 3),
('ペットの楽園', 'かわいいペットたちの日常を紹介。', 4),
('釣りバカ日誌', '釣りの魅力を伝えるリアリティショー。', 5),
('シェフの挑戦', 'プロのシェフが腕を競う料理番組。', 6),
('バラエティナイト', '笑いあり涙ありのバラエティ番組。', 7),
('ミステリー劇場', '毎週異なるミステリーを解き明かす。', 1),
('アニメ映画館', '人気のアニメ映画を毎週放送。', 2),
('スポーツドキュメンタリー', 'スポーツ選手のドキュメンタリー。', 3);
```
genresテーブル
```sql
INSERT INTO genres (genre_name) VALUES
('アニメ'),
('映画'),
('ドラマ'),
('笑い'),
('キュート'),
('白熱');
```
program_genresテーブル
```sql
INSERT INTO program_genres (program_id, genre_id) VALUES
(1, 3),  
(2, 1),
(3, 6),  
(4, 5),
(5, 5), 
(6, 6),  
(7, 4),
(8, 2),
(9, 1), 
(10, 6),
(2, 6),
(3, 4);
```
broadcast_datesテーブル
```sql
INSERT INTO broadcast_dates (start_time, end_time) VALUES
('2023-11-02 19:00:00', '2023-11-02 20:00:00'),
('2023-11-02 21:00:00', '2023-11-02 22:00:00'),
('2023-11-03 19:00:00', '2023-11-03 20:00:00'),
('2023-11-03 21:00:00', '2023-11-03 22:00:00'),
('2023-11-04 19:00:00', '2023-11-04 20:00:00'),
('2023-11-04 21:00:00', '2023-11-04 22:00:00'),
('2023-11-05 19:00:00', '2023-11-05 20:00:00'),
('2023-11-05 21:00:00', '2023-11-05 22:00:00'),
('2023-11-06 19:00:00', '2023-11-06 20:00:00'),
('2023-11-06 21:00:00', '2023-11-06 22:00:00'),
('2023-11-07 19:00:00', '2023-11-07 20:00:00'),
('2023-11-07 21:00:00', '2023-11-07 22:00:00'),
('2023-11-08 19:00:00', '2023-11-08 20:00:00'),
('2023-11-08 21:00:00', '2023-11-08 22:00:00'),
('2023-11-09 19:00:00', '2023-11-09 20:00:00'),
('2023-11-09 21:00:00', '2023-11-09 22:00:00'),
('2023-11-10 19:00:00', '2023-11-10 20:00:00'),
('2023-11-10 21:00:00', '2023-11-10 22:00:00'),
('2023-11-11 19:00:00', '2023-11-11 20:00:00'),
('2023-11-11 21:00:00', '2023-11-11 22:00:00'),
('2023-11-12 19:00:00', '2023-11-12 20:00:00'),
('2023-11-12 21:00:00', '2023-11-12 22:00:00'),
('2023-11-13 19:00:00', '2023-11-13 20:00:00'),
('2023-11-13 21:00:00', '2023-11-13 22:00:00'),
('2023-11-14 19:00:00', '2023-11-14 20:00:00'),
('2023-11-14 21:00:00', '2023-11-14 22:00:00'),
('2023-11-15 19:00:00', '2023-11-15 20:00:00'),
('2023-11-15 21:00:00', '2023-11-15 22:00:00'),
('2023-11-16 19:00:00', '2023-11-16 20:00:00'),
('2023-11-16 21:00:00', '2023-11-16 22:00:00');
```
usersテーブル
```sql
INSERT INTO users (user_name, email, password) VALUES
('山田太郎', 'taro.yamada@example.com', 'taro123'),
('鈴木花子', 'hanako.suzuki@example.com', 'hanako456'),
('佐藤健', 'ken.sato@example.com', 'ken789');
```
episodesテーブル
```sql
INSERT INTO episodes (program_id, season_number, episode_number, episode_title, episode_description, video_time) VALUES
(1, 1, 1, 'ドラマの世界 第1話', 'ドラマチックな展開が始まる第1話。', '00:45:00'),
(1, 1, 2, 'ドラマの世界 第2話', '感動の第2話。', '00:45:00'),
(2, 1, 1, 'アニメヒーローズ 第1話', '勇気と冒険の始まり。', '00:30:00'),
(2, 1, 2, 'アニメヒーローズ 第2話', 'ヒーローたちの新たな挑戦。', '00:30:00'),
(3, 1, 1, 'スポーツバトル 第1話', '熱戦が繰り広げられる。', '01:00:00'),
(3, 1, 2, 'スポーツバトル 第2話', 'スポーツの楽しさを再発見。', '01:00:00'),
(4, 1, 1, 'ペットの楽園 第1話', 'ペットたちのかわいい瞬間。', '00:30:00'),
(5, NULL, NULL, '釣りバカ日誌 特別編', '釣りの魅力に迫る特別編。', '00:45:00'),
(6, NULL, NULL, 'シェフの挑戦 特別編', 'プロのシェフが腕を競う特別編。', '00:50:00'),
(7, NULL, NULL, 'バラエティナイト 特別編', '笑いあり涙ありの特別編。', '00:55:00'),
(8, 1, 1, 'ミステリー劇場 第1話', '毎週異なるミステリーを解き明かす第1話。', '00:50:00'),
(9, NULL, NULL, 'アニメ映画館 特別編', '心温まるアニメの物語特別編。', '01:20:00'),
(10, NULL, NULL, 'スポーツドキュメンタリー 特別編', 'スポーツ選手のドキュメンタリー特別編。', '00:45:00');
```
episide_viewsテーブル
```sql
INSERT INTO episode_views (episode_id, pbd_id, view_count) VALUES
(1, 1, 150),
(2, 11, 200),
(3, 12, 275),
(4, 2, 125),
(5, 13, 350),
(6, 3, 420),
(7, 14, 160),
(8, 15, 500),
(9, 16, 240),
(10, 17, 310),
(11, 18, 280),
(12, 19, 490),
(13, 20, 360);
```
