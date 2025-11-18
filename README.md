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
TaskModel menyimpan data tugas dengan atribut id, title, note, done, dan createdAt. Class ini juga memiliki metode fromMap() dan toMap() untuk serialisasi ke JSON agar bisa disimpan di SharedPreferences.

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
LocalStorageService mengatur penyimpanan data lokal, termasuk daftar tugas, status login, username, dan tema. Fungsi loadTasks() dan saveTasks() memuat dan menyimpan daftar tugas, sedangkan saveTheme() dan loadTheme() mengatur mode tema aplikasi.

### 3. Task List Page
📁 **`lib/pages/task_list_page.dart`**

Halaman ini digunakan untuk menampilkan daftar tugas, menambahkan tugas baru, menandai selesai, dan menghapus tugas.

```dart
final _titleC = TextEditingController();
final _noteC = TextEditingController();
List<TaskModel> tasks = [];
```
Penjelasan
`_titleC` dan `_noteC` digunakan untuk menangkap input pengguna ketika menambahkan tugas baru, sedangkan `tasks` menyimpan semua tugas yang sedang ditampilkan pada halaman ini.

**Menambahkan tugas baru:**
```dart
Future<void> _addTask() async {
  final t = TaskModel(
    id: DateTime.now().millisecondsSinceEpoch.toString(),
    title: _titleC.text.trim(),
    note: _noteC.text.trim(),
  );
  tasks.insert(0, t);
  await LocalStorageService.saveTasks(tasks);
  setState(() {});
}
```
Penjelasan
Fungsi `_addTask()` membuat objek `TaskModel` baru berdasarkan input pengguna, menambahkannya ke daftar `tasks`, menyimpannya ke SharedPreferences, dan memperbarui tampilan UI menggunakan `setState()`.

**Menampilkan tugas:**
```dart
ListView.separated(
  itemCount: tasks.length,
  separatorBuilder: (_, __) => const SizedBox(height: 8),
  itemBuilder: (ctx, i) {
    final t = tasks[i];
    return ListTile(
      leading: Checkbox(
        value: t.done,
        onChanged: (_) => _toggleDone(i),
      ),
      title: Text(t.title,
          style: TextStyle(
              decoration: t.done ? TextDecoration.lineThrough : null)),
      subtitle: t.note != null && t.note!.isNotEmpty ? Text(t.note!) : null,
      trailing: IconButton(
        icon: const Icon(Icons.delete_outline),
        onPressed: () => _removeTask(i),
      ),
    );
  },
)
```
Penjelasan
Setiap tugas ditampilkan menggunakan `ListTile` dengan `Checkbox` untuk menandai selesai atau belum. Jika tugas selesai, judul akan dicoret menggunakan `TextDecoration.lineThrough`. `IconButton` digunakan untuk menghapus tugas dari daftar, dan daftar tugas ditampilkan secara vertikal dengan jarak antar item menggunakan `ListView.separated`.


### 4. Settings Page
📁 **`lib/pages/settings_page.dart`**
```dart
RadioListTile<String>(
  value: 'light',
  groupValue: _theme,
  title: const Text('Terang'),
  onChanged: (v) => _setTheme(v!),
),
```
Penjelasan:
SettingsPage memungkinkan pengguna mengubah tema aplikasi antara system, light, dan dark. Pilihan tema disimpan di SharedPreferences dan langsung diterapkan ke aplikasi melalui MyApp.


### 5. Startup Page
📁 **`lib/pages/startup_page.dart`**
```dart
final loggedIn = await LocalStorageService.isLoggedIn();
final theme = await LocalStorageService.loadTheme();

if (loggedIn) {
  Navigator.pushReplacementNamed(context, AppRoutes.home);
} else {
  Navigator.pushReplacementNamed(context, AppRoutes.login);
}
```
Penjelasan
StartupPage adalah halaman loading awal. Halaman ini mengecek apakah pengguna sudah login dan memuat tema yang tersimpan. Setelah pengecekan, halaman ini akan menavigasi ke HomePage jika sudah login, atau ke LoginPage jika belum.

### 6. Side Menu
📁 **`lib/widgets/side_menu.dart`**
```dart
ListTile(
  leading: const Icon(Icons.dashboard_outlined),
  title: const Text('Dashboard'),
  onTap: () => Navigator.pushReplacementNamed(context, AppRoutes.home),
),
```
Penjelasan
SideMenu adalah menu navigasi samping. Menu ini menampilkan avatar pengguna, username, versi aplikasi, dan beberapa opsi navigasi seperti Dashboard, Task List, Profile, Settings, serta tombol Logout.


### Tampilan Aplikasi


### Halaman Form (form_data.dart)


### Halaman Tampil Data (tampil_data.dart)
