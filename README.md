# 🤝 SkillSwap — Волонтёрское приложение для обмена навыками

Полноценное Android-приложение с авторизацией, реальным чатом и загрузкой фото.

---

## ✅ Что реализовано

### 🔐 Регистрация и вход
- **Email/пароль** с валидацией и индикатором сложности пароля
- **Google Sign-In** в один клик
- **Сброс пароля** по email
- **Email-верификация** при регистрации
- Автоматический маппинг ошибок на русский язык

### 🛡️ Безопасность
- **Firestore Security Rules** — каждый пользователь может редактировать только свой профиль
- **Storage Rules** — лимит 5 МБ, только изображения, только свой аватар
- **Chat Rules** — только участники видят переписку
- Пароль никогда не хранится локально
- HTTPS-only (network_security_config)

### 📸 Фото профиля
- Загрузка из **галереи** (image picker)
- Автоматическое **сжатие** (макс. 1024px, JPEG 80%)
- Хранение в **Firebase Storage**
- Fallback на инициалы если фото нет

### 💬 Чат в реальном времени
- **Firestore real-time listeners** — мгновенная доставка
- Счётчик **непрочитанных** сообщений
- Автоматическая прокрутка к новым сообщениям
- Аватарки собеседников в чате
- Превью последнего сообщения в списке чатов

---

## 🛠️ Настройка Firebase (ОБЯЗАТЕЛЬНО)

### Шаг 1: Создайте проект Firebase
1. Откройте [Firebase Console](https://console.firebase.google.com/)
2. Нажмите **«Создать проект»** → назовите «SkillSwap»
3. Отключите Google Analytics (опционально)

### Шаг 2: Добавьте Android-приложение
1. В Firebase Console → **«Добавить приложение»** → Android
2. Package name: `com.skillswap.app`
3. Скачайте файл **`google-services.json`**
4. Положите его в папку `app/` (рядом с `build.gradle.kts`)

### Шаг 3: Включите Authentication
1. Firebase Console → **Authentication** → **Sign-in method**
2. Включите **Email/Password**
3. Включите **Google**:
   - Скопируйте **Web client ID**
   - Вставьте его в `app/src/main/res/values/strings.xml`:
     ```xml
     <string name="default_web_client_id">YOUR_WEB_CLIENT_ID_HERE</string>
     ```

### Шаг 4: Настройте Firestore
1. Firebase Console → **Firestore Database** → **Create database**
2. Выберите регион (eur3 для Европы или asia-south1)
3. Начните в **test mode**
4. Затем скопируйте правила из файла `firestore.rules` в **Rules** вкладку

### Шаг 5: Настройте Storage
1. Firebase Console → **Storage** → **Get started**
2. Скопируйте правила из файла `storage.rules` в **Rules** вкладку

### Шаг 6: Создайте индексы Firestore
Firebase Console → Firestore → **Indexes** → создайте:

| Collection | Fields | Order |
|---|---|---|
| `users` | `category` ASC, `rating` DESC | Composite |
| `chats` | `participants` ARRAY, `lastMessageTime` DESC | Composite |
| `swap_requests` | `toUserId` ASC, `status` ASC, `createdAt` DESC | Composite |
| `reviews` | `toUserId` ASC, `createdAt` DESC | Composite |

> Firebase обычно предлагает создать индексы автоматически при первом запуске — просто нажмите на ссылку в логах.

---

## 📂 Структура проекта

```
app/src/main/java/com/skillswap/app/
├── MainActivity.kt
├── SkillSwapApplication.kt
├── data/
│   ├── model/Models.kt              ← Firestore-модели данных
│   └── firebase/
│       ├── AuthService.kt           ← Email + Google Auth
│       ├── FirestoreService.kt      ← CRUD + Real-time listeners
│       └── StorageService.kt        ← Загрузка и сжатие фото
├── viewmodel/
│   ├── AuthViewModel.kt             ← Регистрация, вход, профиль
│   └── MainViewModel.kt             ← Поиск, чат, обмены
└── ui/
    ├── theme/Theme.kt               ← Material 3 тёмная тема
    ├── components/Components.kt     ← Avatar, SkillTag, MessageBubble...
    ├── screens/
    │   ├── AuthScreen.kt            ← Вход + Регистрация + Сброс пароля
    │   ├── HomeScreen.kt            ← Поиск и список волонтёров
    │   ├── ChatScreen.kt            ← Реальный чат + список чатов
    │   ├── ProfileScreen.kt         ← Мой профиль + загрузка фото
    │   └── UserProfileScreen.kt     ← Профиль другого пользователя
    └── navigation/Navigation.kt     ← Auth routing + Tab navigation

firestore.rules                      ← Правила безопасности Firestore
storage.rules                        ← Правила безопасности Storage
```

---

## 🚀 Запуск

1. Откройте проект в **Android Studio Hedgehog+**
2. Выполните шаги Firebase выше (google-services.json!)
3. **Sync Gradle**
4. Run на эмуляторе/устройстве (API 26+)

---

## 🔮 Идеи для развития

- [ ] Push-уведомления (Firebase Cloud Messaging)
- [ ] Видеозвонки (WebRTC / Agora)
- [ ] Геолокация и поиск по близости
- [ ] Отправка фото в чате
- [ ] Система модерации / верификации
- [ ] Мультиязычность (KZ/RU/EN)
- [ ] Тёмная/светлая тема
