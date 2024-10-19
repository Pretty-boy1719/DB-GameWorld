
# Проект: База данных для игрового мира

## Описание
Этот проект представляет собой разработку базы данных для игрового мира, включающую таблицы, связанные с игроками, достижениями, героями, гильдиями, характеристиками, навыками, заданиями, предметами и магазинами. Все таблицы взаимосвязаны через внешние ключи, что позволяет эффективно управлять данными и выполнять сложные запросы для анализа игровой информации.

## Структура базы данных

### Таблицы
- **Player**: хранит информацию о игроках (ID, имя, пол, дата рождения, электронная почта).
- **Achievement**: отслеживает достижения игроков.
- **Hero**: содержит информацию о героях игроков.
- **Guild**: хранит информацию о гильдиях.
- **Hero_Guild**: связывает героев с гильдиями.
- **Characteristic_Description** и **Characteristic_Value**: хранят описание характеристик героев и их значения.
- **Skill**: содержит информацию о навыках героев.
- **Task**: содержит информацию о заданиях героев.
- **Item**: хранит информацию о предметах.
- **Hero_Item**: связывает героев с предметами.
- **Shop**: содержит информацию о магазинах.
- **Item_Shop**: связывает предметы с магазинами.

## Содержание

- [Введение](#введение)
- [Даталогическая модель](#даталогическая-модель)
- [Процедуры](#процедуры)
- [Функции](#функции)
- [Триггеры](#триггеры)
- [Роли](#роли)

## Введение
Этот проект описывает структуру базы данных для игрового мира. Он включает в себя элементы, такие как игроки, герои, гильдии, достижения, навыки, предметы и магазины, которые взаимодействуют друг с другом с помощью таблиц, связанных через внешние ключи.

### 1. Создание и заполнение таблиц

```sql
SET foreign_key_checks = 0;
-- Удаление старых таблиц
DROP TABLE IF EXISTS player, achievement, hero, guild, hero_guild, characteristic_description, characteristic_value, skill, task, item, hero_item, shop, item_shop;
SET foreign_key_checks = 1;

-- Создание таблиц
CREATE TABLE player (...);
CREATE TABLE achievement (...);
CREATE TABLE hero (...);
CREATE TABLE guild (...);
CREATE TABLE hero_guild (...);
CREATE TABLE characteristic_description (...);
CREATE TABLE characteristic_value (...);
CREATE TABLE skill (...);
CREATE TABLE task (...);
CREATE TABLE item (...);
CREATE TABLE hero_item (...);
CREATE TABLE shop (...);
CREATE TABLE item_shop (...);

-- Заполнение таблиц данными
INSERT INTO player (name, gender, birthday, email) VALUES ('John Doe', 'Male', '1990-05-15', 'johndoe@example.com');
-- Добавьте сюда другие вставки
```

### 2. Создание процедур

Пример процедуры для получения информации о игроках с определённым достижением:

```sql
DELIMITER // 
CREATE PROCEDURE GetPlayersWithAchievement(IN achievementName VARCHAR(100)) 
BEGIN 
    SELECT p.name, p.email, a.nname AS achievement_name, a.date_receipt 
    FROM player p 
    JOIN achievement a ON p.id_player = a.id_player 
    WHERE a.nname = achievementName; 
END // 
DELIMITER ;
```

Для вызова процедуры используйте:
```sql
CALL GetPlayersWithAchievement('First Achievement');
```

## Функции

### 1. Функция для поиска магазина по названию предмета:

```sql
DELIMITER $$ 
CREATE FUNCTION FindItemShop(item_name VARCHAR(100)) RETURNS VARCHAR(100) DETERMINISTIC 
BEGIN 
    DECLARE shop_name VARCHAR(100); 
    SELECT shop.nname INTO shop_name 
    FROM item 
    JOIN item_shop ON item.id_item = item_shop.id_item 
    JOIN shop ON item_shop.id_shop = shop.id_shop 
    WHERE item.nname = item_name; 
    RETURN shop_name; 
END$$ 
DELIMITER ;
```

Для вызова:
```sql
SELECT FindItemShop('Sword of Power') AS ShopName;
```

## Триггеры

### 1. Триггер для автоматического обновления возраста игрока:

```sql
DELIMITER $$ 
CREATE TRIGGER age_check 
AFTER UPDATE ON player 
FOR EACH ROW 
BEGIN 
    IF (YEAR(CURDATE()) - YEAR(NEW.birthday)) >= 18 THEN 
        UPDATE player SET birthday = DATE_ADD(birthday, INTERVAL 1 DAY); 
    END IF; 
END$$ 
DELIMITER ;
```

### 2. Триггер для автоматического уровня героя при получении достижения:

```sql
DELIMITER $$ 
CREATE TRIGGER level_up 
AFTER INSERT ON achievement 
FOR EACH ROW 
BEGIN 
    UPDATE hero SET llevel = llevel + 1 WHERE id_hero = NEW.id_player; 
END$$ 
DELIMITER ;
```

## Роли

### 1. Создание роли `Admin`:

```sql
CREATE ROLE 'Admin'; 
GRANT ALL PRIVILEGES ON *.* TO 'Admin';
```

### 2. Роль "Чтение" для всех таблиц:

```sql
CREATE ROLE read_only; 
GRANT SELECT ON *.* TO 'read_only';
```

## Установка и запуск

1. Настройте MySQL сервер на вашем локальном компьютере или используйте существующую установку.
2. Импортируйте SQL-скрипты для создания таблиц, процедур, функций и триггеров.
3. Заполните базу данных данными с помощью запросов `INSERT`.
4. Выполняйте запросы к базе данных для тестирования функциональности.

## Лицензия

Этот проект распространяется под лицензией MIT. См. файл [LICENSE](LICENSE) для подробной информации.
