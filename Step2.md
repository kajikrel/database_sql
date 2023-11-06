# データベース構築からサンプルデータを入れるまでの手順

### 1. データベースを構築する
MySQLにログインするには、次のようなコマンドを使用します。

```
mysql -u username -p
```
ここで、username はあなたのMySQLユーザー名に置き換えます。

コマンドを実行すると、MySQLはユーザーのパスワードを要求します。
画面には表示されないので、入力後に Enter キーを押してログインします。

MySQLプロンプトで、次のコマンドを使用してデータベースを作成します：
```sql
CREATE DATABASE internet_tv;
```

作成したデータベースを使用するために、次のコマンドでデータベースを選択します：
```sql
USE internet_tv;
```
### ２. 設計したテーブルを作成します

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
    description TEXT
);

CREATE INDEX idx_program_title ON programs(title);

CREATE TABLE genres (
    genre_id INT AUTO_INCREMENT PRIMARY KEY,
    genre_name VARCHAR(100)
);

CREATE TABLE program_genres (
    program_id INT NOT NULL,
    genre_id INT NOT NULL,
    PRIMARY KEY(program_id, genre_id),
    FOREIGN KEY(program_id) REFERENCES programs(program_id),
    FOREIGN KEY(genre_id) REFERENCES genres(genre_id)
);

CREATE TABLE seasons (
    season_id INT AUTO_INCREMENT PRIMARY KEY,
    program_id INT NOT NULL,
    season_number INT,
    FOREIGN KEY(program_id) REFERENCES programs(program_id)
);

CREATE TABLE episodes (
    episode_id INT AUTO_INCREMENT PRIMARY KEY,
    season_id INT,
    episode_number INT,
    episode_title VARCHAR(255),
    episode_description TEXT,
    video_time TIME,
    FOREIGN KEY(season_id) REFERENCES seasons(season_id)
);

CREATE TABLE users (
    user_id INT AUTO_INCREMENT PRIMARY KEY,
    user_name VARCHAR(100) NOT NULL,
    email VARCHAR(100) NOT NULL UNIQUE,
    password VARCHAR(100) NOT NULL
);

CREATE TABLE episode_views (
    view_id INT AUTO_INCREMENT PRIMARY KEY,
    channel_id INT NOT NULL,
    episode_id INT NOT NULL,
    broadcast_date DATE,
    view_count INT DEFAULT 0,
    FOREIGN KEY(channel_id) REFERENCES channels(channel_id),
    FOREIGN KEY(episode_id) REFERENCES episodes(episode_id)
);

CREATE INDEX idx_episode_views_channel_id ON episode_views(channel_id);


```
実行した後、下記コマンドでテーブルが作成できているかを確認する。
```sql
SHOW TABLES;
```
下記のようになっていればOKです
```sql
mysql> SHOW TABLES;
+-----------------------+
| Tables_in_internet_tv |
+-----------------------+
| channels              |
| episode_views         |
| episodes              |
| genres                |
| program_genres        |
| programs              |
| seasons               |
| users                 |
+-----------------------+
8 rows in set (0.00 sec)
```

### 3. サンプルデータを入れます

下記のコードをコピペし実行する。

```sql
-- channels テーブルのサンプルデータ
INSERT INTO channels (channel_name) VALUES 
('チャンネルA'), 
('チャンネルB'), 
('チャンネルC'),
('チャンネルD'), 
('チャンネルE');

-- programs テーブルのサンプルデータ 
INSERT INTO programs (title, description) VALUES 
('自然の探求', '自然についての素晴らしいショーです。'),
('技術ドキュメンタリー', '技術に関するドキュメンタリーです。'),
('スリリングなドラマ', 'ワクワクするプロットのドラマシリーズです。'),
('歴史ミステリー', '歴史に隠されたミステリーを探ります。'),
('料理の時間', '料理のコツを紹介する番組です。');

-- seasons テーブルのサンプルデータ
INSERT INTO seasons (program_id, season_number) VALUES 
(1, 1), 
(1, 2), 
(2, 1), 
(3, 1),
(4, NULL); -- この番組は単発番組です。

-- episodes テーブルのサンプルデータ
INSERT INTO episodes (season_id, episode_number, episode_title, episode_description, video_time) VALUES
(1, 1, '第1話', '私たちの旅の始まり', '00:45:00'),
(1, 2, '第2話', '野生を発見', '00:47:00'),
(1, 3, '第3話', '目覚める森', '00:50:00'),
(2, 1, '第1話', '新しい技術の台頭', '00:40:00'),
(2, 2, '第2話', '革新の未来', '00:42:00'),
(2, 3, '第3話', 'テクノロジーリーダーへのインタビュー', '00:48:00'),
(3, 1, '第1話', '予期せぬ出来事', '01:00:00'),
(3, 2, '第2話', 'ストーリーのねじれ', '01:05:00'),
(3, 3, '最終話', 'フィナーレ', '01:10:00'),
(NULL, NULL, '歴史の謎', '歴史に隠された謎を解明する特別番組', '02:00:00'); -- 単発番組のエピソードです。

-- users テーブルのサンプルデータ
INSERT INTO users (user_name, email, password) VALUES 
('Alice', 'alice@example.com', 'pass123'), 
('Bob', 'bob@example.com', 'password456'), 
('Charlie', 'charlie@example.com', 'mysecurepwd');

-- episode_views テーブルのサンプルデータ
INSERT INTO episode_views (channel_id, episode_id, broadcast_date, view_count) VALUES
(1, 1, '2023-01-01', 150),
(1, 2, '2023-01-08', 200),
(2, 3, '2023-01-15', 300),
(2, 4, '2023-01-22', 250),
(3, 5, '2023-02-01', 500),
(3, 6, '2023-02-08', 450),
(4, 7, '2023-02-15', 300),
(4, 8, '2023-02-22', 350),
(5, 9, '2023-03-01', 400),
(5, 10, '2023-03-08', 420);

-- genres テーブルのサンプルデータ
INSERT INTO genres (genre_name) VALUES 
('ドキュメンタリー'), 
('ドラマ'), 
('ミステリー'),
('コメディ'), 
('料理');

-- program_genres テーブルのサンプルデータ
INSERT INTO program_genres (program_id, genre_id) VALUES 
(1, 1), -- '自然の探求'は 'ドキュメンタリー'
(2, 1), -- '技術ドキュメンタリー'も 'ドキュメンタリー'
(3, 2), -- 'スリリングなドラマ'は 'ドラマ'
(4, 3), -- '歴史ミステリー'は 'ミステリー'
(5, 5); -- '料理の時間'は '料理'

```