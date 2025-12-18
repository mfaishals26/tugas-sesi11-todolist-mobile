<<<<<<< HEAD
# tugas-sesi11-todolist-mobile
=======
<h1 align="center">React Native Todo App (SQLite)</h1>

<p align="center">
  <img src="https://img.shields.io/badge/Status-Active-brightgreen" />
  <img src="https://img.shields.io/badge/React%20Native-0.82-61DAFB" />
  <img src="https://img.shields.io/badge/Expo-~51-000020" />
  <img src="https://img.shields.io/badge/SQLite-expo--sqlite-blue" />
  <img src="https://img.shields.io/badge/TypeScript-5.x-3178C6" />
  <img src="https://img.shields.io/badge/License-MIT-orange" />
</p>

## Ringkasan

Aplikasi Todo berbasis React Native (Expo) yang menggunakan SQLite (`expo-sqlite`) untuk penyimpanan lokal. Aplikasi ini dirancang untuk tugas Pemrograman Mobile dan mengutamakan stabilitas, kemudahan migrasi skema, dan antarmuka sederhana namun fungsional.

---

## Fitur Utama

* Operasi CRUD pada entitas Todo (Create, Read, Update, Delete).
* Toggle status dengan checkbox (done / undone).
* Kolom `finished_at` yang otomatis diisi saat todo ditandai selesai.
* Filter daftar berdasarkan status: `all`, `done`, `undone`.
* Migrasi skema otomatis: menambahkan kolom `finished_at` jika belum ada.
* Fungsi utilitas: pencarian teks (`searchTodos`), query rentang tanggal (`getTodosByDateRange`), dan debug print (`debugPrintTodos`).
* UI menggunakan `FlatList`, input teks, tombol edit/hapus, dan komponen checkbox kustom.

---

## Preview Fitur Filter

Tabel berikut menampilkan tiga mode filter yang tersedia pada aplikasi Todo: All, Done, dan Undone.

<table align="center" style="border-collapse: collapse;"> <tr> <th style="padding: 8px; border-bottom: 1px solid #ccc;">No</th> <th style="padding: 8px; border-bottom: 1px solid #ccc;">Fitur</th> <th style="padding: 8px; border-bottom: 1px solid #ccc;">Preview</th> </tr> <tr> <td style="padding: 8px; text-align: center;">1</td> <td style="padding: 8px; text-align: center;">All</td> <td style="padding: 8px; text-align: center;"> <img src="https://github.com/Ranggis/Api-Image/blob/main/all.jpg" width="230" /> </td> </tr> <tr> <td style="padding: 8px; text-align: center;">2</td> <td style="padding: 8px; text-align: center;">Done</td> <td style="padding: 8px; text-align: center;"> <img src="https://github.com/Ranggis/Api-Image/blob/main/done.jpg" width="230" /> </td> </tr> <tr> <td style="padding: 8px; text-align: center;">3</td> <td style="padding: 8px; text-align: center;">Undone</td> <td style="padding: 8px; text-align: center;"> <img src="https://github.com/Ranggis/Api-Image/blob/main/undone.jpg" width="230" /> </td> </tr> </table>

---

## Teknologi

* React Native
* Expo
* TypeScript
* `expo-sqlite`
* `react-native-safe-area-context`
* `@expo/vector-icons` (opsional)

---

## Struktur Proyek (ringkasan)

```
app/
├─ components/
│  └─ TodoList.tsx        # Komponen layar utama todo
├─ services/
│  └─ todoService.ts      # Semua fungsi database dan utilitas
├─ assets/
│  └─ images/
├─ app.json
├─ package.json
└─ tsconfig.json
```

---

## Skema Tabel (SQLite)

Tabel `todos` dibuat dengan kolom berikut:

```sql
CREATE TABLE IF NOT EXISTS todos (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  text TEXT NOT NULL,
  done INTEGER NOT NULL DEFAULT 0,
  created_at DATETIME DEFAULT (datetime('now')),
  finished_at DATETIME
);
```

Kolom `finished_at` bersifat nullable; jika `done = 1` maka `finished_at` diset menjadi `datetime('now')` — jika `done = 0` maka `finished_at` dikosongkan.

---

## API Service (ringkasan fungsi utama di `todoService.ts`)

* `initDB(): Promise<void>`

  * Membuat tabel `todos` bila belum ada.
  * Menjalankan migrasi ringan (menambahkan `finished_at` jika belum ada).
  * Membuat index `idx_todos_finished_at` untuk mempercepat query rentang tanggal.

* `getTodos(status?: 'all' | 'done' | 'undone'): Promise<Todo[]>`

  * Mengambil daftar todo sesuai status yang diminta.

* `getTodoById(id: number): Promise<Todo | null>`

  * Mengambil satu todo berdasarkan `id`.

* `addTodo(text: string, done?: number): Promise<number>`

  * Menambahkan todo baru; jika `done = 1` akan mengisi `finished_at`.

* `updateTodo(id: number, fields: { text?: string; done?: number }): Promise<void>`

  * Memperbarui teks atau status; secara otomatis set/clear `finished_at`.

* `deleteTodo(id: number): Promise<void>`

  * Menghapus todo berdasarkan `id`.

* `getTodosByDateRange(startISO, endISO, opts?): Promise<Todo[]>`

  * Mengambil todo berdasarkan rentang `finished_at` (atau `created_at` jika diatur).

* `searchTodos(q: string): Promise<Todo[]>`

  * Pencarian sederhana menggunakan `LIKE`.

* `debugPrintTodos(): Promise<void>`

  * Helper untuk menampilkan semua baris tabel pada console (untuk debug).

---

## Contoh Penggunaan (Quick Start)

1. Install dependency

```bash
npm install
```

2. Jalankan Metro / Expo

```bash
npx expo start
```

3. Inisialisasi DB (biasanya dipanggil saat app start)

```ts
import { initDB } from './app/services/todoService';
await initDB();
```

4. Operasi dasar

```ts
import { addTodo, getTodos, updateTodo, deleteTodo } from './app/services/todoService';

await addTodo('Beli kopi');
const all = await getTodos('all');
await updateTodo(1, { done: 1 });
await deleteTodo(2);
```

---

## Debugging & Verifikasi

* Gunakan `debugPrintTodos()` untuk melihat isi tabel pada console.
* Setelah toggle status, gunakan `getTodoById(id)` untuk memverifikasi `finished_at` terisi / bersih.
* Pastikan memanggil `initDB()` sekali di awal aplikasi (mis. di root `useEffect`).

---

## Praktik Terbaik & Catatan

* Pastikan `skipLibCheck: true` di `tsconfig.json` jika editor menunjukkan peringatan terkait `expo-module-scripts`.
* Untuk produksi, pertimbangkan enkripsi atau penyimpanan aman jika menyimpan data sensitif.
* Index pada `finished_at` membantu untuk query rentang tanggal besar.

---

## Penulis

Ranggis
>>>>>>> abecc21 (Initial commit)
