# ЛР № 1, часть 2 — Модели данных

**МГТУ им. Н. Э. Баумана, кафедра ИУ5.** Предметная область: учебный процесс кафедры. СУБД — **SQLite**, среда работы — **DBeaver**.

## Материалы для сдачи

| Материал | Файл |
| --- | --- |
| Отчёт (Word) | [report/ЛР1.2_КорнеевГИ_ИУ5-34Б.docx](report/ЛР1.2_КорнеевГИ_ИУ5-34Б.docx) |
| Основная база | [database/LR1_part2.sqlite](database/LR1_part2.sqlite) |
| SQL-структура (DDL) | [database/LR1_part2_structure.sql](database/LR1_part2_structure.sql) |
| Инфологическая модель | [models/infological_model.pdf](models/infological_model.pdf) |
| ER-диаграмма | [models/ER.png](models/ER.png) |
| Задание и методические требования | [assignment/ЛР1_часть_2_МД_2026.pdf](assignment/ЛР1_часть_2_МД_2026.pdf) |

## Структура проекта

```text
Data-Models_Lab1.2/
├── README.md
├── .gitignore
├── assignment/                 # задание
├── database/                   # основная база SQLite и SQL-DDL
├── models/                     # инфологическая модель и ER-диаграмма
├── report/                     # итоговый отчёт Word
└── screenshots/
    ├── constraints/            # проверки NOT NULL, UNIQUE, CHECK, DEFAULT
    ├── verification/           # FOREIGN KEY, CASCADE и проверки целостности
    └── workflow/               # вспомогательные скриншоты из DBeaver
```

## Структура базы

В базе **8 таблиц**: `study_group`, `student`, `teacher`, `discipline`, `discipline_course`, `discipline_teacher`, `lesson`, `control_result`.

- Связь многие-ко-многим между дисциплинами и преподавателями представлена таблицей `discipline_teacher`.
- Объявлено **9 внешних ключей**; для всех используются `ON UPDATE CASCADE` и `ON DELETE CASCADE`.
- Присутствуют ограничения `PRIMARY KEY`, `NOT NULL`, `CHECK`, `UNIQUE`.
- Для `control_result.control_date` установлено `DEFAULT CURRENT_DATE`.

**Особенность схемы:** отдельные ссылки `discipline_course.discipline_id` и `discipline_course.teacher_id` гарантируют существование дисциплины и преподавателя, но не проверяют наличие их пары в `discipline_teacher`.

## Результаты проверок

- `NOT NULL`, `UNIQUE`, `CHECK`, `FOREIGN KEY` — вставки с нарушениями ограничений отклонялись.
- `DEFAULT CURRENT_DATE` — дата подставлялась автоматически при отсутствии `control_date` в `INSERT`.
- `ON UPDATE CASCADE` — изменение номера учебной группы обновляло связанные значения.
- `ON DELETE CASCADE` — удаление студента приводило к удалению связанного результата контроля.
- `PRAGMA foreign_keys;` возвращала `1` при включённом контроле для соединения.
- `PRAGMA foreign_key_check;` не выявила нарушений ссылочной целостности.
- `PRAGMA integrity_check;` вернула `ok`.

Проверки изменения и удаления записей выполнялись на **отдельной тестовой копии**, чтобы сохранить основную базу.

### Подтверждающие изображения

| Проверка | Файл |
| --- | --- |
| NOT NULL | [not_null.png](screenshots/constraints/not_null.png) |
| UNIQUE | [unique.png](screenshots/constraints/unique.png) |
| CHECK | [check.png](screenshots/constraints/check.png) |
| DEFAULT | [default.png](screenshots/constraints/default.png) |
| Ошибка FOREIGN KEY | [foreign_key_error.png](screenshots/verification/foreign_key_error.png) |
| Девять правил CASCADE | [cascade_rules.png](screenshots/verification/cascade_rules.png) |
| ON DELETE CASCADE | [on_delete_cascade.png](screenshots/verification/on_delete_cascade.png) |
| Перечень таблиц | [final_tables.png](screenshots/verification/final_tables.png) |
| Нарушения внешних ключей | [foreign_key_check.png](screenshots/verification/foreign_key_check.png) |
| Внутренняя целостность | [integrity_check.png](screenshots/verification/integrity_check.png) |

## Как открыть проект

1. Открыть файл `database/LR1_part2.sqlite` в DBeaver через SQLite-подключение.
2. Для просмотра DDL использовать `database/LR1_part2_structure.sql` (не выполнять `CREATE TABLE` поверх готовой базы).
3. Для схемы открыть `models/ER.png`.
4. Для сдачи использовать документ в папке `report/`.

**Важно:** в SQLite контроль внешних ключей задаётся на уровне соединения командой `PRAGMA foreign_keys = ON;`. При работе в новом SQL-соединении следует проверить `PRAGMA foreign_keys;`.
