# H1D023029 TUGAS 7

Tugas 6: Implementasi routes, side menu, login, dan local storage

Nama: Reva Septia Wulandari

NIM: H1D023029

Shift Baru: F

## Deskripsi Aplikasi

Aplikasi Flutter ini merupakan Student Task Tracker sederhana yang memungkinkan pengguna untuk:

- Menambahkan tugas baru beserta catatan opsional.

- Menandai tugas selesai atau belum selesai.

- Menghapus tugas.

- Menyimpan data tugas secara lokal menggunakan SharedPreferences.

- Mengatur tema aplikasi (system/light/dark).

Aplikasi ini terdiri dari beberapa halaman utama:

- startup_page.dart: Halaman loading awal untuk mengecek login dan tema.

- login_page.dart: Halaman login sederhana.

- home_page.dart: Halaman utama dengan SideMenu dan dashboard.

- task_list_page.dart: Halaman untuk melihat dan menambahkan tugas.

- settings_page.dart: Halaman pengaturan tema.

- profile_page.dart: Halaman profil pengguna.
- 
## Penjelasan Kode

### 1. Model Task

📁 **`lib/models/task_model.dart`**
TaskModel adalah class untuk menyimpan data tugas:
```dart
class TaskModel {
  String id;
  String title;
  String? note;
  bool done;
  DateTime createdAt;

  TaskModel({
    required this.id,
    required this.title,
    this.note,
    this.done = false,
    DateTime? createdAt,
  }) : createdAt = createdAt ?? DateTime.now();

  factory TaskModel.fromMap(Map<String, dynamic> map) => TaskModel(
        id: map['id'] ?? '',
        title: map['title'] ?? '',
        note: map['note'],
        done: map['done'] ?? false,
        createdAt: map['createdAt'] != null
            ? DateTime.parse(map['createdAt'])
            : DateTime.now(),
      );

  Map<String, dynamic> toMap() => {
        'id': id,
        'title': title,
        'note': note,
        'done': done,
        'createdAt': createdAt.toIso8601String(),
      };
}

```
Penjelasan:

- id: ID unik tugas.

- title: Judul tugas.

- note: Catatan tambahan.

- done: Status selesai/tidak.

- createdAt: Waktu dibuat.

- fromMap() dan toMap() digunakan untuk serialisasi ke JSON agar dapat disimpan di SharedPreferences.

### 2. Local Storage Service
📁 **`lib/services/local_storage_service.dart`**
Menyimpan data login, tema, dan tugas ke SharedPreferences:
```dart
static Future<List<TaskModel>> loadTasks() async { ... }
static Future<void> saveTasks(List<TaskModel> tasks) async { ... }
static Future<bool> isLoggedIn() async { ... }
static Future<String?> username() async { ... }
static Future<void> saveTheme(String mode) async { ... }
static Future<String> loadTheme() async { ... }

```
Penjelasan:

- Semua data tersimpan secara lokal sehingga tidak hilang saat aplikasi ditutup.

- loadTasks() memuat daftar tugas.

- saveTasks() menyimpan daftar tugas.

- saveTheme() dan loadTheme() mengatur mode tema.

### 3. Task List Page
📁 **`lib/pages/task_list_page.dart`**

Halaman ini digunakan untuk menampilkan daftar tugas, menambahkan tugas baru, menandai selesai, dan menghapus tugas.

```dart
final _titleC = TextEditingController();
final _noteC = TextEditingController();
List<TaskModel> tasks = [];
```


### Tampilan Aplikasi

### Halaman Form (form_data.dart)


### Halaman Tampil Data (tampil_data.dart)
