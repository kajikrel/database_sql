# 以下のデータを抽出するクエリを書いてください。

1. よく見られているエピソードを知りたいです。エピソード視聴数トップ3のエピソードタイトルと視聴数を取得してください
```sql
SELECT e.episode_title,ev.view_count
FROM episode_views AS ev
JOIN episodes AS e ON ev.episode_id = e.episode_id
ORDER BY view_count DESC 
LIMIT 3;
```
2.よく見られているエピソードの番組情報やシーズン情報も合わせて知りたいです。エピソード視聴数トップ3の番組タイトル、シーズン数、エピソード数、エピソードタイトル、視聴数を取得してください
```sql
SELECT p.title,e.season_number,e.episode_number,e.episode_title,ev.view_count
FROM episode_views AS ev
JOIN episodes AS e ON ev.episode_id = e.episode_id
JOIN programs AS p ON e.program_id = p.program_id
ORDER BY view_count DESC 
LIMIT 3;
```

3.本日の番組表を表示するために、本日、どのチャンネルの、何時から、何の番組が放送されるのかを知りたいです。本日放送される全ての番組に対して、チャンネル名、放送開始時刻(日付+時間)、放送終了時刻、シーズン数、エピソード数、エピソードタイトル、エピソード詳細を取得してください。なお、番組の開始時刻が本日のものを本日方法される番組とみなすものとします
```sql
SELECT c.channel_name,b.start_time,b.end_time,e.episode_number,e.episode_number,e.episode_title,e.episode_description
FROM broadcast_dates AS b
JOIN program_broadcast_dates AS pbd ON b.broadcast_id = pbd.broadcast_id
JOIN programs AS p ON pbd.program_id = p.program_id
JOIN episodes AS e ON p.program_id = e.program_id
JOIN channels AS c ON p.channel_id = c.channel_id
WHERE DATE(b.start_time) = '2023-11-10';
```
4.ドラマというチャンネルがあったとして、ドラマのチャンネルの番組表を表示するために、本日から一週間分、何日の何時から何の番組が放送されるのかを知りたいです。ドラマのチャンネルに対して、放送開始時刻、放送終了時刻、シーズン数、エピソード数、エピソードタイトル、エピソード詳細を本日から一週間分取得してください
```sql
SELECT b.start_time,b.end_time,e.episode_number,e.episode_number,e.episode_title,e.episode_description
FROM broadcast_dates AS b
JOIN program_broadcast_dates AS pbd ON b.broadcast_id = pbd.broadcast_id
JOIN programs AS p ON pbd.program_id = p.program_id
JOIN episodes AS e ON p.program_id = e.program_id
JOIN channels AS c ON p.channel_id = c.channel_id
WHERE c.channel_name = "ドラマ" 
AND  b.start_time >= CURDATE() 
AND b.start_time < CURDATE() + INTERVAL 7 DAY;
```
5.(advanced) 直近一週間で最も見られた番組が知りたいです。直近一週間に放送された番組の中で、エピソード視聴数合計トップ2の番組に対して、番組タイトル、視聴数を取得してください
```sql
SELECT DISTINCT p.title, ev.view_count
FROM episode_views AS ev
JOIN episodes AS e ON ev.episode_id = e.episode_id
JOIN programs AS p ON e.program_id = p.program_id
JOIN program_broadcast_dates AS pbd ON pbd.program_id = p.program_id
JOIN broadcast_dates AS bd ON pbd.broadcast_id = bd.broadcast_id
WHERE bd.start_time BETWEEN CURDATE() - INTERVAL 7 DAY AND CURDATE()
ORDER BY ev.view_count DESC
LIMIT 2;
```
6.(advanced) ジャンルごとの番組の視聴数ランキングを知りたいです。番組の視聴数ランキングはエピソードの平均視聴数ランキングとします。ジャンルごとに視聴数トップの番組に対して、ジャンル名、番組タイトル、エピソード平均視聴数を取得してください。
```sql
SELECT 
    g.genre_name,
    p.title AS program_title,
    max_avg.avg_view_count
FROM 
    genres g
INNER JOIN 
    program_genres pg ON g.genre_id = pg.genre_id
INNER JOIN 
    programs p ON pg.program_id = p.program_id
INNER JOIN 
    (SELECT 
        pg.genre_id, 
        e.program_id, 
        AVG(ev.view_count) AS avg_view_count
     FROM 
        episode_views ev
     INNER JOIN 
        episodes e ON ev.episode_id = e.episode_id
     INNER JOIN 
        program_genres pg ON e.program_id = pg.program_id
     GROUP BY 
        pg.genre_id, e.program_id
    ) AS max_avg ON p.program_id = max_avg.program_id AND pg.genre_id = max_avg.genre_id
INNER JOIN 
    (SELECT 
        genre_id, 
        MAX(avg_view_count) AS max_avg_view_count
     FROM 
        (SELECT 
            pg.genre_id, 
            e.program_id, 
            AVG(ev.view_count) AS avg_view_count
         FROM 
            episode_views ev
         INNER JOIN 
            episodes e ON ev.episode_id = e.episode_id
         INNER JOIN 
            program_genres pg ON e.program_id = pg.program_id
         GROUP BY 
            pg.genre_id, e.program_id
        ) AS sub
    GROUP BY 
        genre_id
    ) AS genre_max ON g.genre_id = genre_max.genre_id AND max_avg.avg_view_count = genre_max.max_avg_view_count
    ORDER BY 
    avg_view_count DESC; 

```


