Не получится сделать в один момент, так как много ссылающихся таблиц
Для начала нужно создать запись с другим id,на нее перенести все связи, поменять в основной, и вернуть все связи назад
Сделать копию записи из бд
`insert into broadcast_broadcastgroup  (id, created_at, updated_at, name, color) select 2, created_at, updated_at, name, color from broadcast_broadcastgroup where id = 1;`

Communal
`BEGIN; UPDATE communal_communal SET group_id = 1 WHERE group_id = 5; UPDATE communal_communal SET group_level_two_id = 1 WHERE group_level_two_id = 5; COMMIT;`

communal_communalpreview
`BEGIN; UPDATE communal_communalpreview SET group_id = 1 WHERE group_id = 5; UPDATE communal_communalpreview SET group_level_two_id = 1 WHERE group_level_two_id = 5; COMMIT;`

broadcast_broadcastsubscription_groups +
`BEGIN; UPDATE broadcast_broadcastsubscription_groups SET broadcastgroup_id = 1 WHERE broadcastgroup_id = 5; COMMIT;`

broadcast_broadcastpreview +
`UPDATE broadcast_broadcastpreview SET group_id = 1 WHERE group_id = 5;`

broadcast_broadcastoperatorprofile_groups +
`UPDATE broadcast_broadcastoperatorprofile_groups SET broadcastgroup_id = 1 WHERE broadcastgroup_id = 5;`

broadcast_broadcastgroup +
`BEGIN; UPDATE broadcast_broadcastgroup SET id = 1 WHERE id = 5; UPDATE broadcast_broadcastgroup SET parent_id = 1 WHERE parent_id = 5; COMMIT;`

broadcast_broadcast +
`BEGIN; UPDATE broadcast_broadcast SET group_id = 1 WHERE group_id = 5; UPDATE broadcast_broadcast SET group_level_two_id = 1 WHERE group_level_two_id = 5; COMMIT;`


Запрос на изменение id категорий за 1 скрипт:
- Создание временной бд с временным значениями
- Создание копии нужной записи
- Перенос всех связей на временную запись
- Изменение основной записи
- Перенос всех связей на нужную модель
- Удаление временных данных(БД, значения, скопированная запись)

```
create temp table temp_values ( old_id INT, temp_id INT, new_id INT);

insert into temp_values (old_id, temp_id, new_id) VALUES (2, 1000, 1);

  
begin;

INSERT INTO broadcast_broadcastgroup (id, created_at, updated_at, name, color)

SELECT (select temp_id from temp_values), created_at, updated_at, name, color

FROM broadcast_broadcastgroup

WHERE id = (SELECT old_id FROM temp_values);

UPDATE communal_communal SET group_id = (select temp_id from temp_values) WHERE group_id = (select old_id from temp_values);

update

communal_communal

set

group_level_two_id = (select temp_id from temp_values)

where

group_level_two_id = (select old_id from temp_values);

UPDATE communal_communalpreview SET group_id = (select temp_id from temp_values) WHERE group_id = (select old_id from temp_values);

update

communal_communalpreview

set

group_level_two_id = (select temp_id from temp_values)

where

group_level_two_id = (select old_id from temp_values);

UPDATE broadcast_broadcastsubscription_groups SET broadcastgroup_id = (select temp_id from temp_values) WHERE broadcastgroup_id = (select old_id from temp_values);

UPDATE broadcast_broadcastpreview SET group_id = (select temp_id from temp_values) WHERE group_id = (select old_id from temp_values);

UPDATE broadcast_broadcastoperatorprofile_groups SET broadcastgroup_id = (select temp_id from temp_values) WHERE broadcastgroup_id = (select old_id from temp_values);

UPDATE broadcast_broadcast SET group_id = (select temp_id from temp_values) WHERE group_id = (select old_id from temp_values); UPDATE broadcast_broadcast SET group_level_two_id = (select temp_id from temp_values) WHERE group_level_two_id = (select old_id from temp_values);

UPDATE broadcast_broadcastgroup SET parent_id = (select temp_id from temp_values) WHERE parent_id = (select old_id from temp_values);

UPDATE broadcast_broadcastgroup SET id = (select new_id from temp_values) WHERE id = (select old_id from temp_values);

commit;


begin;

UPDATE communal_communal SET group_id = (select new_id from temp_values) WHERE group_id = (select temp_id from temp_values);

update

communal_communal

set

group_level_two_id = (select new_id from temp_values)

where

group_level_two_id = (select temp_id from temp_values);

UPDATE communal_communalpreview SET group_id = (select new_id from temp_values) WHERE group_id = (select temp_id from temp_values);

update

communal_communalpreview

set

group_level_two_id = (select new_id from temp_values)

where

group_level_two_id = (select temp_id from temp_values);

UPDATE broadcast_broadcastsubscription_groups SET broadcastgroup_id = (select new_id from temp_values) WHERE broadcastgroup_id = (select temp_id from temp_values);

UPDATE broadcast_broadcastpreview SET group_id = (select new_id from temp_values) WHERE group_id = (select temp_id from temp_values);

UPDATE broadcast_broadcastoperatorprofile_groups SET broadcastgroup_id = (select new_id from temp_values) WHERE broadcastgroup_id = (select temp_id from temp_values);

UPDATE broadcast_broadcast SET group_id = (select new_id from temp_values) WHERE group_id = (select temp_id from temp_values); UPDATE broadcast_broadcast SET group_level_two_id = (select new_id from temp_values) WHERE group_level_two_id = (select temp_id from temp_values);

  

UPDATE broadcast_broadcastgroup SET parent_id = (select new_id from temp_values) WHERE parent_id = (select temp_id from temp_values);

delete from broadcast_broadcastgroup where id = (select temp_id from temp_values);

DELETE FROM temp_values;

drop table temp_values;

commit;
```