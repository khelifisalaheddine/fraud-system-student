# Smart Attendance - Student App (Flutter)

Application mobile Flutter pour les étudiants du système Smart Attendance.

## 🎯 Fonctionnalités

| Écran | Description |
|-------|-------------|
| **Login** | Connexion avec Student ID + Mot de passe |
| **Dashboard** | Taux de présence, prochaine séance, stats par matière |
| **QR Code** | Carte d'identité numérique avec QR code unique |
| **Mes Présences** | Vue d'ensemble + historique filtrable |
| **Sessions** | Séances à venir et terminées |
| **Profil** | Infos personnelles + déconnexion |
| **Notifications** | Alertes de présence, séances, exclusions |

## 📱 Flux d'identification (RFID)

```
Lecteur RFID (PC Prof) → Lit la carte étudiant
         ↓
  App Prof marque la présence dans le backend
         ↓
  App Étudiant reçoit une notification push
  et voit son statut mis à jour en temps réel
```

## 🚀 Installation

### Prérequis
- Flutter SDK ≥ 3.0.0
- Dart ≥ 3.0.0
- Android Studio / VS Code

### Étapes

```bash
# 1. Cloner le projet
git clone <votre-repo>
cd smart_attendance_student

# 2. Installer les dépendances
flutter pub get

# 3. Configurer l'URL du backend
# Modifier lib/utils/constants.dart :
static const String baseUrl = 'http://VOTRE_IP:3000/api';

# 4. Lancer l'application
flutter run
```

## 🔌 Configuration Backend

### URL de base
Dans `lib/utils/constants.dart` :
```dart
static const String baseUrl = 'http://192.168.1.100:3000/api';
```

### Endpoints attendus (Express.js)

```
POST /api/auth/student/login
     Body: { studentId, password }
     Response: { token, student }

GET  /api/students/me              → profil étudiant
GET  /api/students/me/dashboard    → stats dashboard
GET  /api/students/me/qrcode       → QR code dynamique
GET  /api/attendance/me            → historique présences
GET  /api/attendance/me/stats      → stats par matière
GET  /api/sessions/my?status=planned|closed
GET  /api/sessions/:id
GET  /api/modules/my
GET  /api/notifications
PATCH /api/notifications/read-all
```

### Réponse attendue `/api/auth/student/login`
```json
{
  "token": "eyJhbGciOiJIUzI1NiIs...",
  "student": {
    "_id": "ObjectId",
    "fullName": "Mahdi Alami",
    "email": "mahdi@email.com",
    "studentId": "ST1001",
    "rfidCode": "...",
    "qrCode": "ST1001-UNIQUE-CODE",
    "group": "2A",
    "year": "2",
    "speciality": "Informatique"
  }
}
```

### Réponse attendue `/api/students/me/dashboard`
```json
{
  "attendanceRate": 89.4,
  "totalPresent": 48,
  "totalAbsent": 6,
  "totalLate": 2,
  "totalSessions": 56,
  "nextSession": { ...Session },
  "moduleStats": [
    {
      "module": { "_id": "...", "name": "Web Development", ... },
      "totalSessions": 25,
      "present": 20,
      "absent": 3,
      "late": 2,
      "isExcluded": false,
      "attendanceRate": 88.0
    }
  ],
  "weeklyData": [...]
}
```

## 📐 Architecture

```
lib/
├── main.dart                 # Point d'entrée
├── models/
│   └── models.dart           # Student, Session, Attendance, Module...
├── providers/
│   ├── auth_provider.dart    # Gestion auth + token
│   └── attendance_provider.dart
├── screens/
│   ├── login_screen.dart
│   ├── main_nav_screen.dart  # Navigation principale
│   ├── home_screen.dart      # Dashboard
│   ├── attendance_screen.dart
│   ├── sessions_screen.dart
│   ├── session_detail_screen.dart
│   ├── qr_code_screen.dart
│   ├── profile_screen.dart
│   └── notifications_screen.dart
├── services/
│   └── api_service.dart      # Tous les appels API
├── utils/
│   └── constants.dart        # URLs, couleurs, thème
└── widgets/
    └── common_widgets.dart   # Composants réutilisables
```

## 🎨 Design System

- **Couleur principale**: Indigo `#4F46E5`
- **Police**: Poppins (Google Fonts)
- **Statuts**:
  - 🟢 Present: `#10B981`
  - 🔴 Absent: `#EF4444`
  - 🟡 Late: `#F59E0B`
  - 🟠 Excluded: `#F97316`

## 🔐 Sécurité

- Token JWT stocké dans `flutter_secure_storage` (chiffré)
- QR code unique par étudiant
- Authentification par Student ID + mot de passe

## 📦 Dépendances principales

| Package | Usage |
|---------|-------|
| `provider` | State management |
| `http` | Appels API |
| `flutter_secure_storage` | Stockage sécurisé du token |
| `qr_flutter` | Génération QR code |
| `google_fonts` | Police Poppins |
| `percent_indicator` | Indicateurs circulaires |
| `table_calendar` | Calendrier présences |
| `fl_chart` | Graphiques |
