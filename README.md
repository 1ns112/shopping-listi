Shopping List App на Flutter

Приложение для управления списком покупок с поддержкой светлой/тёмной темы и сохранением данных между запусками.

Функционал:

Добавление и удаление товаров

Изменение количества каждого товара

Отметка о покупке (зачёркивание названия)

Переключение светлой/тёмной темы

Смена языка интерфейса (русский/английский)

Сохранение списка и настроек в локальном хранилище

Material Design 3 UI

Технологии:

Flutter + Dart

Riverpod — управление состоянием

SharedPreferences — локальное key-value хранилище

Material 3 — дизайн-система

Ручная JSON-сериализация

Data layer Модель ShoppingItem — это обычный класс с полями name, quantity и purchased. Он содержит методы toJson() и fromJson() для сохранения/загрузки в JSON.

Сервис StorageService отвечает за работу с SharedPreferences. Здесь три группы методов:

loadItems() / saveItems() — сериализуют список покупок в JSON-строку и обратно.

loadTheme() / saveTheme() — сохраняют булевый флаг тёмной темы.

loadLanguage() / saveLanguage() — сохраняют текущий язык интерфейса ("ru" или "en").

Все методы асинхронные, потому что SharedPreferences работает с Future.

Domain / Управление состоянием Я выбрал Riverpod (пакет flutter_riverpod) как основной инструмент управления состоянием. Созданы два StateNotifier’а и соответствующие провайдеры.

ShoppingNotifier управляет списком товаров. При создании он загружает сохранённые данные из StorageService. Основные методы:

addItem() — добавляет товар с количеством 1

deleteItem() — удаляет по индексу

togglePurchased() — переключает статус куплен/не куплен

changeQuantity() — изменяет количество (не меньше 1)

Чтобы UI перерисовывался, каждый раз создаётся новая ссылка на список (state = [...list]), хотя сами объекты ShoppingItem изменяются на месте. Это компромисс ради простоты.

SettingsNotifier работает с иммутабельным объектом SettingsState (поля darkTheme и language). Любое изменение возвращает новый экземпляр через copyWith(). Методы:

toggleTheme(bool) — переключает тему и сохраняет выбор

setLanguage(String) — меняет язык и сохраняет

Провайдеры shoppingProvider и settingsProvider создаются как StateNotifierProvider и доступны во всём дереве виджетов через ProviderScope.

Presentation (UI) ShoppingApp — корневой виджет-ConsumerWidget. Он читает settingsProvider и в зависимости от значения darkTheme задаёт ThemeMode.light или ThemeMode.dark для MaterialApp. Тема строится на Material 3 с базовым синим цветом.

ShoppingScreen — основной экран. Состоит из:

AppBar с динамическим заголовком (меняется в зависимости от языка) и кнопкой настроек

ListView.builder с элементами списка. Каждый элемент — ListTile, где:

Checkbox переключает статус покупки

Название товара зачёркивается, если purchased == true

Подзаголовок показывает количество (текст адаптирован под язык)

Кнопки -/+ для изменения количества

Кнопка удаления

FloatingActionButton открывает диалог _showAddDialog() для добавления товара

Локализация сделана простыми тернарными выражениями (isRu ? "Добавить" : "Add"), что подходит для двух языков без подключения пакета intl.

SettingsScreen — экран с настройками. Содержит:

SwitchListTile для тёмной темы

DropdownButton для выбора языка (русский/английский)

Навигация между экранами — классический Navigator.push.

Ссылка на проект и видеопоказ кода - https://drive.google.com/drive/folders/10cTcRruOFgq_81XbJVR7zejxIFVN8l7W?usp=sharing

Показ самого приложения на гитхабе
