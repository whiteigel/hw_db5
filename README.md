# Домашняя работа к лекции «Группировки, выборки из нескольких таблиц»

1. Количество исполнителей в каждом жанре;

```
SELECT g.name, COUNT(g.id) from genre g
INNER JOIN genreartist ga on g.id = ga.genre_id
INNER JOIN artist a on ga.artist_id = a.id
GROUP BY g.id; 
```
```buildoutcfg
[('hiphop', 3), ('rock', 7), ('pop', 4)]
```

2. Количество треков, вошедших в альбомы 2019-2020 годов;

```
SELECT SUM(track.id) from track
JOIN album ON track.album_id = album.id
WHERE album.release_date BETWEEN 2019 AND 2020;
```
```buildoutcfg
[(21,)]
```


3. Средняя продолжительность треков по каждому альбому;

```
SELECT a.title, AVG(t.duration) from album a
INNER JOIN track t ON a.id = t.album_id
GROUP BY a.id;
```
```buildoutcfg
[('New Jersey', Decimal('279.1666666666666667')), ('Everything Was Beautiful, and Nothing Hurt', Decimal('273.3636363636363636')), ('Demi Masa', Decimal('220.8333333333333333')), ('St. Anger', Decimal('409.4545454545454545')), ('Long Ambients 2', Decimal('2170.0000000000000000'))]
```
4. Все исполнители, которые не выпустили альбомы в 2020 году;

```
SELECT name FROM artist a
INNER JOIN artistalbum b ON a.id = b.artist_id
INNER JOIN album c ON b.album_id = c.id
WHERE c.release_date != 2020
GROUP BY a.name;
```

```buildoutcfg
[('Depeche Mode',), ('Da Molotov',), ('Morgue Vanguard',), ('Eminem',), ('Moby',), ('Bon Jovi',), ('Океан Эльзи',), ('Metalica',), ('AC/DC',)]
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

```buildoutcfg
[('Kate Bush Remastered Part I,')]
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
```buildoutcfg
[('Con Todo Respeto',), ('Demi Masa',), ('Everything Was Beautiful, and Nothing Hurt',), ('Kamikaze',), ('Long Ambients 2',), ('Songs of Faith and Devotion',)
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
```buildoutcfg
[('Breakadawn', 'Demi Masa', None), ('Buckshot Funk feat. Mr.EP & Sarkasz', 'Demi Masa', None), ('CSDB FM feat. Iwa K', 'Demi Masa', None), ('Check Your People', 'Demi Masa', None), ('Check Your People (Remix)', 'Demi Masa', None), ('Demi Masa', 'Demi Masa', None), ('Di Hadapan Babylon', 'Demi Masa', None), ('Junta Titimangsa', 'Demi Masa', None), ('Rotasi Baja (Interlude)', 'Demi Masa', None), ('Sans Temps Mort', 'Demi Masa', None), ('Testamen', 'Demi Masa', None), ('Testamen [Novum Testamentum]', 'Demi Masa', None), ('A Dark Cloud Is Coming', 'Everything Was Beautiful, and Nothing Hurt', None), ('Falling Rain and Light', 'Everything Was Beautiful, and Nothing Hurt', None), ('Like a Motherless Child', 'Everything Was Beautiful, and Nothing Hurt', None), ('Mere Anarchy', 'Everything Was Beautiful, and Nothing Hurt', None), ('The Ceremony of Innocence', 'Everything Was Beautiful, and Nothing Hurt', None), ('The Last of Goodbyes', 'Everything Was Beautiful, and Nothing Hurt', None), ('The Middle Is Gone', 'Everything Was Beautiful, and Nothing Hurt', None), ('The Sorrow Tree', 'Everything Was Beautiful, and Nothing Hurt', None), ('The Tired and the Hurt', 'Everything Was Beautiful, and Nothing Hurt', None), ('This Wild Darkness', 'Everything Was Beautiful, and Nothing Hurt', None), ('Welcome to Hard Times', 'Everything Was Beautiful, and Nothing Hurt', None), ('LA12', 'Long Ambients 2', None), ('LA13', 'Long Ambients 2', None), ('LA14', 'Long Ambients 2', None), ('LA15', 'Long Ambients 2', None), ('LA16', 'Long Ambients 2', None), ('LA17', 'Long Ambients 2', None), ('99 in the Shade', 'New Jersey', None), ('Bad Medicine', 'New Jersey', None), ('Blood on Blood', 'New Jersey', None), ('Born to Be My Baby', 'New Jersey', None), ('Homebound Train', 'New Jersey', None), ('Ill Be There for You', 'New Jersey', None), ('Lay Your Hands on Me', 'New Jersey', None), ('Living in Sin', 'New Jersey', None), ('Love for Sale', 'New Jersey', None), ('Ride Cowboy Ride', 'New Jersey', None), ('Stick to Your Guns', 'New Jersey', None), ('Wild Is the Wind', 'New Jersey', None), ('All Within My Hands', 'St. Anger', None), ('Dirty Window', 'St. Anger', None), ('Frantic', 'St. Anger', None), ('Invisible Kid', 'St. Anger', None), ('My World', 'St. Anger', None), ('Purify', 'St. Anger', None), ('Shoot Me Again', 'St. Anger', None), ('Some Kind of Monster', 'St. Anger', None), ('St. Anger', 'St. Anger', None), ('Sweet Amber', 'St. Anger', None), ('The Unnamed Feeling', 'St. Anger', None)]

```

8. Исполнителя(-ей), написавшего самый короткий по продолжительности трек (теоретически таких треков может быть несколько);

```
SELECT a.name AS artist_name, t.title AS track_title, t.duration AS track_duration FROM artist a
INNER JOIN artistalbum aa ON a.id = aa.artist_id
INNER JOIN album al ON aa.album_id = aa.id
INNER JOIN track t ON aa.id = t.album_id
WHERE t.duration =(SELECT MIN(track.duration) FROM track) LIMIT 1;
```
```buildoutcfg
[('Morgue Vanguard', 'Rotasi Baja (Interlude)', 58)]
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

```buildoutcfg
[('Long Ambients 2',)]
```