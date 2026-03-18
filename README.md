<img width="523" height="967" alt="image" src="https://github.com/user-attachments/assets/e7e41edb-5392-4af2-9ede-33fc49b91325" />Car Rental Service — Clean Architecture
Розроблений програмний продукт може бути використаний як веборієнтована інформаційна система для сервісів прокату автомобілів, що забезпечує оформлення замовлень, контроль повернення автомобілів та облік фінансових операцій.Обґрунтування вибору архітектури

Для розробки програмного продукту Car Rental Service було обрано архітектуру Clean Architecture, оскільки вона найкраще відповідає вимогам до побудови масштабованих, гнучких та легко підтримуваних інформаційних систем.

Саме тому використання Clean Architecture є доцільним з таких причин:

Розділення відповідальностей (Separation of Concerns):
Архітектура дозволяє чітко відокремити бізнес-логіку від інфраструктурного рівня (бази даних, веб-інтерфейс), що спрощує розробку та тестування.

Незалежність бізнес-логіки:
Основна логіка системи (наприклад, розрахунок вартості оренди чи перевірка доступності автомобіля) не залежить від зовнішніх технологій, що дозволяє легко змінювати, наприклад, СУБД або фронтенд без впливу на ядро системи.

Масштабованість:
У разі розширення функціоналу (додавання нових типів послуг, системи знижок тощо) архітектура дозволяє безболісно інтегрувати нові компоненти.

Зручність тестування:
Завдяки ізоляції бізнес-логіки можна легко писати юніт-тести без необхідності підключення до бази даних або інших зовнішніх сервісів.

Гнучкість у виборі технологій:
Можливість змінювати фреймворки, бібліотеки чи навіть тип інтерфейсу (наприклад, з веб-додатку на мобільний) без значних змін у структурі системи.

<img width="469" height="921" alt="image" src="https://github.com/user-attachments/assets/7a26897a-24ba-482e-8818-632e04558331" />
<img width="476" height="906" alt="image" src="https://github.com/user-attachments/assets/c83b123d-53fb-4a2a-a436-1ebefd92a8ee" />
Структура проєкту

📋 Розподіл відповідальностей у вашій архітектурі
1. Domain Logic (Ядро)
Де живе: папка Entity

Car, Client, DamageReport, Payment, RentalOrder, User, UserRole, UserSession

Це чисті класи, які описують бізнес-правила. Вони:

Не залежать від фреймворку
Не мають доступу до БД
Не знають про HTTP, контролери чи сервіси
Містять лише дані та логіку валідації

2. Use Cases / Application Logic
Де живе: папка Service

AuthenticationService, CarService, ClientService, DamageReportService, RentalOrderService

Це шар бізнес-логіки. Вони:

Оркеструють роботу зі сутностями (Entities)
Викликають Repository для отримання даних
Застосовують бізнес-правила
Повертають результати контролерам
Залежать від Ports (Repository interfaces)

3. Ports / Interfaces
Де живе: двома місцях:
В контролерах (Controller):

AuthenticationController, CarController, ClientController, RentalOrderController
Це порти для надходження вхідних даних
Визначають, як зовнішній світ (HTTP, UI) спілкується з додатком

В репозиторіях (Repository):

CarRepository, ClientRepository, PaymentRepository, RentalOrderRepository, UserRepository
Це порти для доступу до даних
Визначають контракт, як додаток читає/пишет дані

4. Adapters (Технічна реалізація)
Де живе: папка Repository + Web контролери
Приклади:

CarRepositoryImpl — адаптер для карів (реалізує CarRepository)
ClientRepositoryImpl — адаптер для клієнтів
Web-контролери (WebCarController, WebHomeController) — адаптери для HTTP

Вони:

Реалізують Interface/Port
Спілкуються з зовнішніми системами (БД, HTTP)
Перетворюють дані в потрібний формат

5. Технічна реалізація
Де живе: утиліти та конфіг

DatabaseConnection — керування зв'язком з БД
GlobalExceptionHandler — обробка помилок
CreateClientRequest, CreateDamageReportRequest — DTO для валідації вхідних даних


🎯 Приклад потоку запиту
Як потрапляє запит через всі шари:
HTTP POST /rent → WebRentalOrderController (адаптер)
                ↓
           RentalOrderService (use case)
                ↓
           RentalOrder (entity) — застосувати правила
                ↓
           RentalOrderRepository.save() (port)
                ↓
           RentalOrderRepositoryImpl (адаптер)
                ↓
           DatabaseConnection → SQL query → БД
Кожен шар робить своє:

Controller — розбирає HTTP
Service — вирішує, як обробити
Entity — перевіряє валідність
Repository — зберігає в БД

Щоб запустити проєкт треба активувати файл CourseProjectApplication
