# Домашняя работа к лекции «Группировки, выборки из нескольких таблиц»

1. Количество исполнителей в каждом жанре;

```
SELECT g.name, COUNT(g.id) from genre g
INNER JOIN genreartist ga on g.id = ga.genre_id
INNER JOIN artist a on ga.artist_id = a.id
GROUP BY g.id; 
```
2. Количество треков, вошедших в альбомы 2019-2020 годов;

```
SELECT SUM(track.id) from track
JOIN album ON track.album_id = album.id
WHERE album.release_date BETWEEN 2019 AND 2020;
```

3. Средняя продолжительность треков по каждому альбому;

```
SELECT a.title, AVG(t.duration) from album a
INNER JOIN track t ON a.id = t.album_id
GROUP BY a.id;
```

4. Все исполнители, которые не выпустили альбомы в 2020 году;

```
SELECT name FROM artist a
INNER JOIN artistalbum b ON a.id = b.artist_id
INNER JOIN album c ON b.album_id = c.id
WHERE c.release_date != 2020
GROUP BY a.name;
```

5. Названия сборников, в которых присутствует конкретный исполнитель (выберите сами);

```
SELECT c.title as comp_title FROM compilation c
LEFT JOIN trackcompilation tc ON c.id = tc.compilation_id
LEFT JOIN track t ON tc.track_id = t.id
LEFT JOIN album a ON t.album_id = a.id
LEFT JOIN artistalbum aa ON a.id = aa.album_id
LEFT JOIN artist ar ON aa.artist_id = ar.id
WHERE ar.name LIKE '%%Kate%%'
ORDER BY c.title;
```

6. Название альбомов, в которых присутствуют исполнители более 1 жанра;

```
SELECT a.title as album_title FROM album a
LEFT JOIN artistalbum aa ON a.id = aa.album_id
LEFT JOIN artist ar ON aa.artist_id = ar.id
LEFT JOIN genreartist ga ON ar.id = ga.artist_id
LEFT JOIN genre g ON ga.genre_id = g.id
GROUP BY a.title
HAVING COUNT (DISTINCT g.name)>1
ORDER BY a.title;
```

7. Наименование треков, которые не входят в сборники;

```
SELECT t.title as track_title, a.title as album_title, c.title as compilation_title FROM track t
LEFT JOIN album a ON t.album_id = a.id
LEFT JOIN trackcompilation tc ON t.id = tc.track_id
LEFT JOIN compilation c ON tc.compilation_id = c.id
WHERE c.title IS NULL
GROUP BY a.title, t.title, c.title;
```

8. Исполнителя(-ей), написавшего самый короткий по продолжительности трек (теоретически таких треков может быть несколько);

```
SELECT a.name AS artist_name, t.title AS track_title, t.duration AS track_duration FROM artist a
INNER JOIN artistalbum aa ON a.id = aa.artist_id
INNER JOIN album al ON aa.album_id = aa.id
INNER JOIN track t ON aa.id = t.album_id
WHERE t.duration =(SELECT MIN(track.duration) FROM track) LIMIT 1;
```

9. Название альбомов, содержащих наименьшее количество треков.

```
SELECT DISTINCT a.title AS album_title
FROM album a
LEFT JOIN track t ON t.album_id = a.id
WHERE t.album_id IN(SELECT album_id FROM track GROUP BY album_id HAVING COUNT(id) = (
SELECT COUNT(id) FROM track GROUP BY album_id ORDER BY COUNT LIMIT 1))
ORDER BY a.title;
```