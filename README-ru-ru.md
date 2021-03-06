#Custom service registration
### DevChallenge 2016 fall, back-end qualification round

## Запуск приложения через загрузку образа с docker hub
> - **docker run -p 8080:5000 devchallenge/custom-registration-service**
> - Запустите localhost:8080 в браузере, если порт занят, замените в docker run 8080 на любой другой порт и запустите команду еще раз.

## Альтернативный путь сборки и запуска через docker
> - `cd src\CustomServiceRegistration`
> - `bower install`
> - `cd ..\..\`
> - `docker build -t app .`
> - `docker run -p 8080:5000 -t app`

## Описание проекта
Проект являет собой сервис единой регистрации, позволяющую третьим лицам и приложениям доступ к данным пользователей.
Используемые технологии:
- **Серверная часть:** ASP.NET Core, Entity Framework Core, MS SQL, Swagger (для авто документации API), XUnit, Moq.
- **Клиент:** AngularJS, Angular Material (для красивого отображения в стиле Material design).


## Структура проекта
Проект выполнен с использованием технологии ASP.NET Core, так как .NET предполагает собой решения уровня Enterprise, приложение было декомпозировано на 4 проекта:
- **CustomServiceRegistration** - так как нужно было запускать через один docker контейнер. API и клиентская часть содержится на одном хосте. В папке **Controllers** содержатся два API контроллера, документацию и подробности можно посмотреть с помощью автосгенерированной документации по адресу **/swagger/ui**, или по XML комментариями над методами в самих контроллерах. Так же присутствует Home контроллер просто для того, что бы вернуть вьюху и на клиенте уже использовать AngularJS. В файле *Startup.cs* по гайдлайнам от ASP.NET Core есть метод *ConfigureServices*, который отвечает за DI контейнер, на объекте IServiceCollection мы регистрируем интерфейс и конкретную реализацию сервисов и репозиториев. Так с помощью DI мы позволяем в будущем более расширить наше приложение и не заботиться о замене конкретных реализаций в разных частях приложения. В папке **Services** содержатся интерфейсы и реализация сервисов, в них была вынесена вся основная логика работы над запросами с контроллеров для того, чтобы не захламлять сами контроллеры. В папке **wwwroot/app** клиентское приложение с использованием AngularJS вы можете просмотреть его сразу после старта приложения.
- **CustomServiceRegistration.Domain** - так как в проекте применяется ORM Entity Framework Core, работа с данными была вынесена в отдельный проект, чтобы разграничить ответственность. Был применен паттерн Repository, интерфейс и реализацию вы можете увидеть в папке **Infrastructure**. Были применены миграции для расширения проекта и постоянного апдейта структуры базы данных через новые миграции, в папке **Migrations** находятся автосгенерированые классы после обновления моделей.
- **CustomServiceRegistration.Tests** - в папке **UnitTests** содержатся тесты, отвечающие за апрув основной базовой логики контроллеров. В папке **IntegrationTests** содержатся тесты, которые отправляют запросы на мок-сервер, аналогичный по конфигурации тому, который содержится в главном приложении. В **UserControllerTest** находятся два теста на проверку отправленного запроса на взятие данных пользователя с токеном и без него.
- **CustomServiceRegistration.TokenProvider** - проект, отвечающий за верификацию и генерацию OAuth2 токенов юзерам и приложениям, зарегистрированным в сервисе.

## Зависимости в проекте
- **CustomServiceRegistration** -> CustomServiceRegistration.Domain, CustomServiceRegistration.TokenProvider
- **CustomServiceRegistration.Tests** ->  CustomServiceRegistration
- **CustomServiceRegistration.TokenProvider** -> CustomServiceRegistration.Domain