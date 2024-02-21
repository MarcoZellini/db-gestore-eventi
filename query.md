# Gestionale Eventi

### Selezionare tutti gli eventi gratis, cioè con prezzo nullo (19)
```sql 
SELECT *
FROM `events`
WHERE `price` IS NULL;
```

### Selezionare tutte le location in ordine alfabetico (82)
```sql
SELECT *
FROM `locations`
ORDER BY `name` ASC;
```

### Selezionare tutti gli eventi che costano meno di 20 euro e durano meno di 3 ore (38)
```sql
SELECT *
FROM `events`
WHERE `price` < 20.00
AND HOUR(`duration`) < 3;
```

### Selezionare tutti gli eventi di dicembre 2023 (25)
```sql
SELECT *
FROM `events`
WHERE YEAR(`start`) = 2023
AND MONTH(`start`) = 12;
```

### Selezionare tutti gli eventi con una durata maggiore alle 2 ore (823)
```sql
SELECT *
FROM `events`
WHERE HOUR(`duration`) > 2;
```

### Selezionare tutti gli eventi, mostrando nome, data di inizio, ora di inizio, ora di fine e durata totale (1040)
```sql
SELECT `name`,
DATE(`start`) AS `date`, TIME(`start`) AS `start` , ADDTIME(TIME(`start`), `duration`) AS `end` , `duration`
FROM `events`;
```

### Selezionare tutti gli eventi aggiunti da “Fabiano Lombardo” (id: 1202) (132)
```sql
SELECT *
FROM `events`
INNER JOIN `users`
ON `events`.`user_id` = `users`.`id`
WHERE `users`.`first_name` = 'Fabiano'
AND `users`.`last_name` = 'Lombardo';
```

### Selezionare il numero totale di eventi per ogni fascia di prezzo (81)
```sql
SELECT COUNT(`id`) AS `event_id`, `price`
FROM `events`
GROUP BY `price`;
```

### Selezionare tutti gli utenti admin ed editor (9)
```sql
SELECT *
FROM `roles`
INNER JOIN `users`
ON `users`.`role_id` = `roles`.`id`
WHERE `roles`.`name` = 'Admin'
OR `roles`.`name` = 'Editor';
```

### Selezionare tutti i concerti (eventi con il tag “concerti”) (72)
```sql
SELECT *
FROM `events`
INNER JOIN `event_tag`
ON `event_tag`.`event_id` = `events`.`id`
INNER JOIN `tags`
ON `tags`.`id` = `event_tag`.`tag_id`
WHERE `tags`.`name` = 'concerti';
```

### Selezionare tutti i tag e il prezzo medio degli eventi a loro collegati (11)
```sql
SELECT `tags`.`name`, AVG(`events`.`price`) AS `avg_price`
FROM `tags`
INNER JOIN `event_tag`
ON `tags`.`id` = `event_tag`.`tag_id`
INNER JOIN `events`
ON `event_tag`.`event_id` = `events`.`id`
GROUP BY `tags`.`id`;
```

### Selezionare tutte le location e mostrare quanti eventi si sono tenute in ognuna di esse (82)
```sql
SELECT `locations`.`name`, COUNT(`events`.`id`) AS `events_number`
FROM `locations`
INNER JOIN `events`
ON `events`.`location_id` = `locations`.`id`
GROUP BY `locations`.`id`;
```

### Selezionare tutti i partecipanti per l’evento “Concerto Classico Serale” (slug:concerto-classico-serale, id: 34) (30)
```sql
SELECT *
FROM `events`
INNER JOIN `bookings`
ON `events`.`id` = `bookings`.`event_id`
INNER JOIN `users`
ON `bookings`.`user_id` = `users`.`id`
WHERE `events`.`name` = 'Concerto Classico Serale';
```

### Selezionare tutti i partecipanti all’evento “Festival Jazz Estivo” (slug:festival-jazz-estivo, id: 2) specificando nome e cognome (13)
```sql
SELECT `users`.`first_name`, `users`.`last_name`
FROM `events`
INNER JOIN `bookings`
ON `events`.`id` = `bookings`.`event_id`
INNER JOIN `users`
ON `bookings`.`user_id` = `users`.`id`
WHERE `events`.`name` = 'Festival Jazz Estivo';
```

## Selezionare tutti gli eventi sold out (dove il totale delle prenotazioni è uguale ai biglietti totali per l’evento) (18)
```sql
SELECT DISTINCT `events`.`id`, `events`.`total_tickets`, (SELECT COUNT(*) FROM `bookings` WHERE `bookings`.`event_id` = `events`.`id`) AS `bookings_counter` FROM `events` INNER JOIN `bookings` ON `events`.`id` = `bookings`.`event_id` WHERE `events`.`total_tickets` = (SELECT COUNT(*) FROM `bookings` WHERE `bookings`.`event_id` = `events`.`id`);
```

### Selezionare tutte le location in ordine per chi ha ospitato più eventi (82)
```sql
SELECT `locations`.`name`, COUNT(`events`.`id`) AS `event_number`
FROM `locations`
INNER JOIN `events`
ON `locations`.`id` = `events`.`location_id`
GROUP BY `locations`.`id`
ORDER BY `event_number`;
```

### Selezionare tutti gli utenti che si sono prenotati a più di 70 eventi (74)
```sql
SELECT COUNT(`users`.`id`) AS `user_counter`
FROM `users`
INNER JOIN `bookings`
ON `users`.`id` = `bookings`.`user_id`
GROUP BY `users`.`id`
HAVING COUNT(`users`.`id`) > 70;
```

### Selezionare tutti gli eventi, mostrando il nome dell’evento, il nome della location, il numero di prenotazioni e il totale di biglietti ancora disponibili per l’evento (1040)
```sql
SELECT `events`.`name`, `locations`.`name`, COUNT(`bookings`.`id`) AS `bookings_counter`, (`events`.`total_tickets` - COUNT(`bookings`.`id`)) AS `remaning_tickets`
FROM `events`
INNER JOIN `bookings`
ON `events`.`id` = `bookings`.`event_id`
INNER JOIN `locations`
ON `events`.`location_id` = `locations`.`id`
GROUP BY `events`.`id`;
```