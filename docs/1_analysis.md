# РОЗДІЛ 1. АНАЛІЗ ПРЕДМЕТНОЇ ОБЛАСТІ ТА ВИМОГ

## Аналіз предметної області та аналогів

## 1.2 Аналіз існуючих аналогів

**Дата перевірки відкритих джерел:** 11 листопада 2026 року. **Предмет зіставлення:** програмні засоби для конструювання, виконання й перевірки сценаріїв REST API. Для порівняння обрано Postman, Insomnia та Bruno як реальні аналоги з документованими механізмами HTTP-запитів, тестових сценаріїв і автоматизації. Власна система перебуває **на етапі проєктування**; відомості про її можливості нижче означають *передбачено проєктом*, а не виконану реалізацію.

**Правило доказовості.** Твердження про можливості клієнта, формат файлів або API/CLI спираються на документацію виробника чи відкритий репозиторій. Внутрішні сервіси, СУБД, способи реплікації, гарантовані RPS, Latency та Fault Tolerance не виводяться з назви продукту або з наявності клієнтської функції. Відсутність відкритих характеристик позначається як невизначеність джерел, а не як дефект аналога.

### 1.2.1 Postman

**Призначення й архітектура.** Postman надає клієнтське середовище для формування API-запитів, організації їх у колекції та запуску перевірок. Документовані desktop-клієнт і хмарні робочі простори, а також локальний *lightweight API client*, який працює без входу до облікового запису та не синхронізує власні дані з Postman Cloud. Таке розділення дає вибір між локальною роботою та спільним хмарним контуром. Конкретну внутрішню серверну архітектуру, тип промислової СУБД та схему консистентності даних Postman Cloud цими матеріалами **не підтверджено**. Джерела: [P1](https://learning.postman.com/docs/getting-started/basics/using-api-client), [P5](https://learning.postman.com/docs/getting-started/basics/syncing).

**Протоколи та HTTP-взаємодія.** Для сценарного тестування доступні HTTP-запити; Collection Runner також документує запуск GraphQL і gRPC. У Postman CLI для GraphQL/gRPC зазначено обмеження на unary-виклики, а запуск таких колекцій залежить від тарифного плану. Це можливості тестового клієнта, а не докази використання відповідних протоколів у внутрішніх сервісах Postman. Джерела: [P2](https://learning.postman.com/docs/tests-and-scripts/running-collections/intro-to-collection-runs), [P6](https://learning.postman.com/docs/postman-cli/postman-cli-run-collection).

**Сценарне виконання та перевірки.** Collection Runner запускає впорядковані запити, реєструє результати тестів та виконує JavaScript-скрипти перед/після запиту. Змінні середовища й колекції та post-response scripts дають змогу переносити значення відповіді між кроками; через `pm.test` і `pm.expect` перевіряються HTTP-статуси, JSON-дані та тривалість відповіді. Postman CLI надає командне виконання колекцій для CI/CD. Джерела: [P2](https://learning.postman.com/docs/tests-and-scripts/running-collections/intro-to-collection-runs), [P3](https://learning.postman.com/docs/tests-and-scripts/write-scripts/test-scripts), [P4](https://learning.postman.com/docs/tests-and-scripts/write-scripts/test-examples/), [P6](https://learning.postman.com/docs/postman-cli/postman-cli-run-collection).

**Дані та результати.** Дані lightweight-клієнта залишаються локальними; робочі ресурси звичайного Postman можуть синхронізуватися з Postman Cloud. Офіційно описано керування конфліктами синхронізації. Collection Runner формує результати запуску, однак збереження повних тіл запитів/відповідей має обмеження сесії та розміру; самі журнали запусків не слід ототожнювати з внутрішньою СУБД хмарного сервісу. Джерела: [P1](https://learning.postman.com/docs/getting-started/basics/using-api-client), [P2](https://learning.postman.com/docs/tests-and-scripts/running-collections/intro-to-collection-runs), [P5](https://learning.postman.com/docs/getting-started/basics/syncing).

**Безпека й надійність.** Postman Vault підтримує секрети для запитів та скриптів. Local Vault зберігає секрети локально; Shared Vault має окрему модель доступу й застосовується в підтримуваних хмарних сценаріях. Документація також розрізняє умови доступності секретів у ручних запусках і CLI/запланованих запусках. Це підтверджує наявність механізму секретів, але не дає підстав оцінювати загальний рівень захищеності продукту числом. Джерела: [P7](https://learning.postman.com/docs/use/postman-vault/use-vault-secrets), [P6](https://learning.postman.com/docs/postman-cli/postman-cli-run-collection).

**Обмеження та компроміс.** У Collection Runner збереження великих тіл відповідей залежить від налаштувань і ресурсів локального середовища; за умов великих запусків офіційна інструкція рекомендує зменшення обсягу накопичуваних даних. Для окремих типів колекцій CLI діють протокольні обмеження. Взаємодія із хмарними функціями створює залежність від обраного режиму синхронізації, тоді як lightweight-клієнт усуває її ціною відсутності такої синхронізації. Джерела: [P2](https://learning.postman.com/docs/tests-and-scripts/running-collections/intro-to-collection-runs), [P6](https://learning.postman.com/docs/postman-cli/postman-cli-run-collection), [P1](https://learning.postman.com/docs/getting-started/basics/using-api-client).

### 1.2.2 Insomnia

**Призначення й архітектура.** Insomnia — відкритий кросплатформний API-клієнт. У документі розробників зазначено desktop-архітектуру на Electron/React з мережевими операціями через libcurl; це свідчення щодо реалізації **клієнта**, а не доказ мікросервісної чи монолітної організації сторонньої хмари. Описані режими Local Vault, Scratch Pad, Cloud Sync та Git Sync. Файл `DEVELOPMENT.md` містить історичні відомості про локальний NeDB, проте відповідність цієї деталі **всім актуальним збіркам** окремо не встановлено; тому NeDB не визначається тут як гарантована поточна СУБД продукту. Джерела: [I1](https://github.com/kong/insomnia), [I2](https://github.com/Kong/insomnia/blob/develop/DEVELOPMENT.md), [I3](https://developer.konghq.com/insomnia/storage/).

**Протоколи та HTTP-взаємодія.** Офіційний репозиторій вказує на REST/HTTP, GraphQL, gRPC, WebSocket та Server-Sent Events. Наявність цих клієнтських режимів не визначає протоколи, якими обмінюються внутрішні компоненти хмарного сервісу. Джерело: [I1](https://github.com/kong/insomnia).

**Сценарне виконання та перевірки.** Collection Runner виконує вибрані запити у визначеному порядку, підтримує ітерації. Pre-request та after-response scripts змінюють змінні, зчитують JSON/статус/час відповіді й формують перевірки; документовано response chaining. Для автоматизованого запуску колекцій передбачено Inso CLI з параметрами середовища, даних і таймауту. Джерела: [I6](https://developer.konghq.com/insomnia/scripts/), [I7](https://developer.konghq.com/insomnia/test/), [I8](https://developer.konghq.com/inso-cli/reference/run_collection/).

**Дані та результати.** Local Vault та Scratch Pad дозволяють локальну роботу; Cloud Sync забезпечує синхронізацію, а Git Sync зберігає ресурси у YAML-файлах Git-репозиторію, дотримуючись звичайних процедур комітів і конфліктів. Тип СУБД, модель транзакцій і протоколи внутрішньої хмарної інфраструктури в цих документах **не розкриті в обсязі, достатньому для технічного зіставлення**. Джерела: [I3](https://developer.konghq.com/insomnia/storage/), [I4](https://developer.konghq.com/insomnia/git-sync/).

**Безпека й надійність.** У приватних глобальних підсередовищах підтримуються зашифровані *secret variables* з ключем сховища. Водночас позначення звичайної змінної як *confidential* не означає її шифрування при експорті: документація прямо зазначає, що такі значення експортуються у відкритому вигляді. У Inso CLI можна встановити таймаут запиту. Джерела: [I5](https://developer.konghq.com/insomnia/environments/), [I8](https://developer.konghq.com/inso-cli/reference/run_collection/).

**Обмеження та компроміс.** За використання Git Sync зміни й конфлікти керуються Git, а під час деяких операцій у репозиторії може з’являтися додаткова службова метаінформація. Ризик розкриття *confidential values* під час експорту потребує застосування саме *secret variables*, а не лише відповідної позначки. Це перевірювані умови експлуатації, але не свідчення загальної небезпечності Insomnia. Джерела: [I4](https://developer.konghq.com/insomnia/git-sync/), [I5](https://developer.konghq.com/insomnia/environments/).

### 1.2.3 Bruno

**Призначення й архітектура.** Bruno — відкритий локальний API-клієнт з desktop-застосунком на Electron/React і CLI, що випливає з офіційної структури відкритого репозиторію. Колекції розміщуються у файловій системі з можливістю Git-версіювання. В актуальних версіях Bruno для нових колекцій за замовчуванням використовується формат OpenCollection YAML; формат `.bru` також підтримується. Існуючі колекції `.bru` можна продовжувати використовувати або окремо мігрувати до YAML. Джерела: [B1](https://github.com/usebruno/bruno), [B2](https://github.com/usebruno/bruno/blob/main/contributing.md), [B3](https://www.usebruno.com/opencollection), [B8](https://www.usebruno.com/changelog), [B9](https://blog.usebruno.com/making-yaml-the-default-in-bruno-v3.1).

**Протоколи та HTTP-взаємодія.** Документовано клієнтську підтримку REST/HTTP, GraphQL, gRPC та WebSocket. Для теми дипломного проєкту релевантним піднабором є HTTP-запити до REST API. Джерело: [B7](https://www.usebruno.com/product/api-client).

**Сценарне виконання та перевірки.** Bruno підтримує змінні середовища, скрипти та твердження (assertions). Через JavaScript API доступні дані відповіді й встановлення змінних наступних запитів. Команда `bru run` запускає колекції/папки, а CLI може формувати файли результатів JSON, JUnit та HTML. Наявність цих форматів засвідчує можливість експорту результатів, але не автоматично наявність централізованої історії запусків у СУБД. Джерела: [B4](https://github.com/usebruno/bruno/blob/main/packages/bruno-cli/readme.md), [B5](https://github.com/usebruno/bruno-docs/blob/main/testing/script/javascript-reference.mdx), [B7](https://www.usebruno.com/product/api-client).

**Дані та результати.** Файлова модель і Git-інтеграція роблять колекції доступними для diff і code review; Bruno не позиціонує хмарну синхронізацію як власну функцію. Відкритий репозиторій містить окремий пакет, названий `bruno-sqlite`, однак саме ця назва **не доводить**, що колекції або всі результати виконання зберігаються у SQLite. Документована модель зберігання колекцій — файлові формати OpenCollection YAML/`.bru`. Джерела: [B1](https://github.com/usebruno/bruno), [B2](https://github.com/usebruno/bruno/blob/main/contributing.md), [B3](https://www.usebruno.com/opencollection).

**Безпека й надійність.** Секретні змінні зберігаються локально, не потрапляють до файлів середовища; офіційно описано шифрування засобами операційної системи або резервний AES-256. CLI підтримує зупинення при збої (`--bail`) та опції перевірки сертифікатів. Це окремі механізми контролю, а не підтвердження певного значення Fault Tolerance або гарантій безпеки в умовах довільних навантажень. Джерела: [B6](https://github.com/usebruno/bruno-docs/blob/main/secrets-management/secret-variables.mdx), [B4](https://github.com/usebruno/bruno/blob/main/packages/bruno-cli/readme.md).

**Обмеження та компроміс.** Відсутність власної хмарної синхронізації є **свідомим вибором моделі**; для командного обміну артефактами необхідний Git або інша зовнішня система. Файлове представлення підтримує простежуваність змін колекцій, проте не є готовою прикладною моделлю збереження історії тестових запусків і запитів вебзастосунку. Друге твердження є **інженерним висновком про відмінність цілей продуктів**, а не виміряним дефектом Bruno. Джерела: [B1](https://github.com/usebruno/bruno), [B3](https://www.usebruno.com/opencollection), [B4](https://github.com/usebruno/bruno/blob/main/packages/bruno-cli/readme.md).

### 1.2.4 Порівняльна інженерна матриця

**Таблиця 1.2 — Порівняння технічних характеристик Postman, Insomnia, Bruno та проєктованої системи**

| Технічний критерій | Postman | Insomnia | Bruno | Власна система (передбачено проєктом) |
| :--- | :--- | :--- | :--- | :--- |
| Архітектурний контур | Desktop-клієнт; локальний lightweight-режим і Cloud; серверна архітектура не підтверджена | Desktop Electron/React; Local/Cloud/Git; серверна архітектура не підтверджена | Desktop Electron/React + CLI; локальні файли, Git; без власного Cloud Sync | Вебзастосунок Blazor + ASP.NET Core; модульний моноліт |
| Клієнтські API-протоколи | HTTP, GraphQL, gRPC (окремі режими/обмеження) | REST/HTTP, GraphQL, gRPC, WebSocket, SSE | REST/HTTP, GraphQL, gRPC, WebSocket | REST поверх HTTP; GET, POST, PUT, PATCH, DELETE |
| Сценарії та передавання значень | Collection Runner; скрипти та змінні між запитами | Collection Runner; response chaining, скрипти, змінні | Колекції `bru run`; скрипти і змінні | Послідовні HTTP-кроки; іменовані змінні з простих JSON-шляхів |
| Перевірки відповідей | `pm.test`/`pm.expect`: статус, JSON, час | After-response scripts: статус, JSON, час | Assertions/тести: статус, тіло, час | Статус HTTP, вибрані поля JSON, час відповіді |
| Зберігання колекцій і результатів | Локальний режим/Cloud; результати Runner, умови збереження тіл | Local Vault/Scratch Pad/Cloud/Git YAML; виконання через Inso | OpenCollection YAML/`.bru` + Git; CLI-звіти JSON/JUnit/HTML | PostgreSQL + Entity Framework Core; історія запусків та результати кроків |
| Секрети та контроль помилок | Vault; параметри виконання колекцій | Secret variables у приватному сховищі; CLI-таймаут | Локальні secret variables; CLI `--bail` | Таймаути, контроль помилок, обмеження адрес/розміру відповіді, приховування секретів |

**Інтерпретація та межі матриці.** Порівнюються документовані механізми продуктів і попередня специфікація власної системи, а не результати єдиного експерименту. Матриця **не** присвоює переваг за Latency, RPS, Fault Tolerance, горизонтальним масштабуванням, реальною кількістю інцидентів безпеки або внутрішньою СУБД хмарних сервісів: зіставних перевірених вимірювань і відомостей для всіх об’єктів не отримано. Відсутність таких даних не означає, що продукт не має відповідного механізму. Джерельні підстави рядків: архітектура — [P1](https://learning.postman.com/docs/getting-started/basics/using-api-client), [I2](https://github.com/Kong/insomnia/blob/develop/DEVELOPMENT.md), [I3](https://developer.konghq.com/insomnia/storage/), [B2](https://github.com/usebruno/bruno/blob/main/contributing.md), [B3](https://www.usebruno.com/opencollection); протоколи — [P2](https://learning.postman.com/docs/tests-and-scripts/running-collections/intro-to-collection-runs), [I1](https://github.com/kong/insomnia), [B7](https://www.usebruno.com/product/api-client); сценарії й перевірки — [P2](https://learning.postman.com/docs/tests-and-scripts/running-collections/intro-to-collection-runs), [P3](https://learning.postman.com/docs/tests-and-scripts/write-scripts/test-scripts), [P4](https://learning.postman.com/docs/tests-and-scripts/write-scripts/test-examples/), [I6](https://developer.konghq.com/insomnia/scripts/), [I7](https://developer.konghq.com/insomnia/test/), [I8](https://developer.konghq.com/inso-cli/reference/run_collection/), [B4](https://github.com/usebruno/bruno/blob/main/packages/bruno-cli/readme.md), [B5](https://github.com/usebruno/bruno-docs/blob/main/testing/script/javascript-reference.mdx); зберігання — [P5](https://learning.postman.com/docs/getting-started/basics/syncing), [I3](https://developer.konghq.com/insomnia/storage/), [I4](https://developer.konghq.com/insomnia/git-sync/), [B3](https://www.usebruno.com/opencollection), [B4](https://github.com/usebruno/bruno/blob/main/packages/bruno-cli/readme.md); безпека — [P7](https://learning.postman.com/docs/use/postman-vault/use-vault-secrets), [I5](https://developer.konghq.com/insomnia/environments/), [B6](https://github.com/usebruno/bruno-docs/blob/main/secrets-management/secret-variables.mdx).

### 1.2.5 Аналітичний висновок підрозділу 1.2

Проведене порівняння показує, що досліджені аналоги застосовують різні способи організації тестових артефактів і сценарного виконання. Postman поєднує колекції зі сценарними скриптами та локальними/хмарними режимами; Insomnia підтримує виконання колекцій поряд із вибором локального, Git- або хмарного зберігання; Bruno використовує Git-орієнтовану файлову модель з окремим CLI. Виявлені компроміси стосуються, зокрема, меж синхронізації, експорту секретів і збереження результатів. Документовані можливості цих продуктів підтверджують доцільність моделі впорядкованих HTTP-кроків, спільного контексту змінних та явно сформульованих правил перевірки; вони не підтверджують універсальної переваги будь-якого з рішень за швидкодією чи захищеністю.

Для кваліфікаційного проєкту обрано C#/.NET, ASP.NET Core, Blazor, Entity Framework Core та PostgreSQL із модульною монолітною архітектурою. За визначеного обсягу реалізації цей підхід дозволяє розмежувати керування сценаріями, HTTP-виконання, перевірки відповідей та збереження історії в межах одного застосунку без додаткових мережевих меж між модулями. `HttpClient` і `async/await` застосовуватимуться для контрольованих HTTP-операцій, а PostgreSQL — для відтворюваної прив'язки результатів до запусків, кроків та правил перевірки. Така архітектура є **обґрунтовано достатньою для сформульованих вимог**, але її ефективність і безпека мають підтверджуватися майбутніми тестами, а не припускатися апріорі.

Інженерну цінність запланованого рішення визначає не розширення переліку протоколів або повторення універсального API-клієнта, а керований механізм виконання залежних REST API-запитів за схемою «запит → отримання значення JSON → підстановка у наступний крок → перевірки → збереження результатів → звіт». Навмисне обмеження простими шляхами до JSON-властивостей, послідовним порядком та фіксованими видами перевірок зменшує складність реалізації й полегшує перевірку коректності. Наступний розділ проєктування має визначити межі програмних модулів, модель даних сценарію й запуску та алгоритм передавання значень між кроками без розширення п'яти затверджених Core Features.

### 1.2.6 Перевірені джерела

Первинні офіційні документи й відкриті репозиторії; дата звернення до кожного джерела — **11.11.2026**. Посилання на актуальні вебсторінки не є гарантією незмінності їх змісту в майбутньому. Згадування NeDB у документації розробників Insomnia залишено тільки як приклад технічної інформації, актуальність якої для кожної версії не встановлювалася.

**Postman**

- [P1] https://learning.postman.com/docs/getting-started/basics/using-api-client
- [P2] https://learning.postman.com/docs/tests-and-scripts/running-collections/intro-to-collection-runs
- [P3] https://learning.postman.com/docs/tests-and-scripts/write-scripts/test-scripts
- [P4] https://learning.postman.com/docs/tests-and-scripts/write-scripts/test-examples/
- [P5] https://learning.postman.com/docs/getting-started/basics/syncing
- [P6] https://learning.postman.com/docs/postman-cli/postman-cli-run-collection
- [P7] https://learning.postman.com/docs/use/postman-vault/use-vault-secrets

**Insomnia**

- [I1] https://github.com/kong/insomnia
- [I2] https://github.com/Kong/insomnia/blob/develop/DEVELOPMENT.md
- [I3] https://developer.konghq.com/insomnia/storage/
- [I4] https://developer.konghq.com/insomnia/git-sync/
- [I5] https://developer.konghq.com/insomnia/environments/
- [I6] https://developer.konghq.com/insomnia/scripts/
- [I7] https://developer.konghq.com/insomnia/test/
- [I8] https://developer.konghq.com/inso-cli/reference/run_collection/

**Bruno**

- [B1] https://github.com/usebruno/bruno
- [B2] https://github.com/usebruno/bruno/blob/main/contributing.md
- [B3] https://www.usebruno.com/opencollection
- [B4] https://github.com/usebruno/bruno/blob/main/packages/bruno-cli/readme.md
- [B5] https://github.com/usebruno/bruno-docs/blob/main/testing/script/javascript-reference.mdx
- [B6] https://github.com/usebruno/bruno-docs/blob/main/secrets-management/secret-variables.mdx
- [B7] https://www.usebruno.com/product/api-client
- [B8] https://www.usebruno.com/changelog
- [B9] https://blog.usebruno.com/making-yaml-the-default-in-bruno-v3.1

**Власна система:** `docs/proposal.md` (фінальний файл ЛР №1, локальний первинний документ; не зовнішнє вебджерело).

## Постановка завдання та вимоги до системи
