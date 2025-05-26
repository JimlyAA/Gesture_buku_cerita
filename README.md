### 1.	Buat proyek flutter baru → praktikum_buku_cerita_gesture
![image](https://github.com/user-attachments/assets/acb4661b-db02-4ccc-ad63-0a7a2ab61577)

### 2. Di dalam lib buat folder pages di dalamnya terdapat 3 files:
![image](https://github.com/user-attachments/assets/445b0429-8225-4294-8038-21ec3fde84e0)
- a.	title_page.dart
- b.	scene_page.dart
- c.	end_page.dart

### 3.	Buka lib/main.dart dan ganti isinya dengan kode berikut untuk membuat struktur PageView:
![image](https://github.com/user-attachments/assets/01d84ded-65cd-44e3-8292-e88fef2e2d3e)

// lib/main.dart
import 'package:flutter/material.dart';
import 'package:storybook_practicum/pages/scene_page.dart';
import 'package:storybook_practicum/pages/title_page.dart';
import 'package:storybook_practicum/pages/end_page.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Interactive Storybook',
      theme: ThemeData(
        colorScheme: ColorScheme.fromSeed(seedColor: Colors.teal, brightness: Brightness.dark),
        useMaterial3: true,
      ),
      home: StorybookHome(),
      debugShowCheckedModeBanner: false,
    );
  }
}

class StorybookHome extends StatelessWidget {
  StorybookHome({super.key});

  final PageController _controller = PageController();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: PageView(
        controller: _controller,
        children: [
          TitlePage(onStartPressed: () => _controller.animateToPage(1, duration: const Duration(milliseconds: 400), curve: Curves.easeInOut)),
          InteractiveScenePage(onQuestComplete: () => _controller.animateToPage(2, duration: const Duration(milliseconds: 400), curve: Curves.easeInOut)),
          EndPage(onRestart: () => _controller.animateToPage(0, duration: const Duration(milliseconds: 400), curve: Curves.easeInOut)),
        ],
      ),
    );
  }
}

### 4.	Untuk mencegah error, isi setiap file di folder pages dengan StatelessWidget boilerplate sederhana. Contoh untuk title_page.dart
![image](https://github.com/user-attachments/assets/c977bbb5-1089-4e20-bac3-6e9508ffce0e)

// lib/pages/title_page.dart
import 'package:flutter/material.dart';

class TitlePage extends StatelessWidget {
  final VoidCallback onStartPressed;
  const TitlePage({super.key, required this.onStartPressed});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            const Text("Petualangan Antariksa", style: TextStyle(fontSize: 32)),
            const SizedBox(height: 20),
            ElevatedButton(onPressed: onStartPressed, child: const Text("Mulai")),
          ],
        ),
      ),
    );
  }
}

### 5.	Lakukan hal yang sama untuk scene_page.dart dan end_page.dart, cukup tampilkan teks judul halaman


### 6.	Fokus pada lib/pages/scene_page.dart → ganti isinya dengan kerangka StatefulWidget
![image](https://github.com/user-attachments/assets/bec48544-0b1f-482f-996b-6d2e0ccf1441)

// lib/pages/scene_page.dart
import 'package:flutter/material.dart';

class InteractiveScenePage extends StatefulWidget {
  final VoidCallback onQuestComplete;
  const InteractiveScenePage({super.key, required this.onQuestComplete});

  @override
  State<InteractiveScenePage> createState() => _InteractiveScenePageState();
}

class _InteractiveScenePageState extends State<InteractiveScenePage> {
  // Variabel state untuk posisi, dialog, dan status peti
  Offset _keyPosition = const Offset(50, 300);
  bool _isDialogueVisible = false;
  bool _isChestOpen = false;
  String _chestHint = "Terkunci...";

  // GlobalKeys untuk mendapatkan posisi dan ukuran widget
  final GlobalKey _chestKey = GlobalKey();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Stack(
        children: [
          // Latar Belakang
          Container(color: const Color(0xff303952), child: const Center(child: Text("🪐", style: TextStyle(fontSize: 200, color: Colors.white24)))),

          // Elemen-elemen adegan akan kita tambahkan di sini
        ],
      ),
    );
  }
}

### 7.	Tambahkan Astronaut & Balon Dialog (Double Tap). Di dalam Stack, tambahkan Positioned untuk astronaut
![image](https://github.com/user-attachments/assets/dc907281-6bb9-48da-958a-f95a44b4b1b6)

// Di dalam children dari Stack:
// Astronaut (Double Tap)
Positioned(
  bottom: 50,
  left: 60,
  child: GestureDetector(
    onDoubleTap: () {
      setState(() {
        _isDialogueVisible = !_isDialogueVisible;
      });
    },
    child: const Text("👩‍🚀", style: TextStyle(fontSize: 80)),
  ),
),
// Balon Dialog
if (_isDialogueVisible)
  Positioned(
    bottom: 140,
    left: 30,
    child: Container(
      padding: const EdgeInsets.all(12),
      decoration: BoxDecoration(color: Colors.white, borderRadius: BorderRadius.circular(10)),
      child: const Text("Aku harus buka peti itu!", style: TextStyle(color: Colors.black)),
    ),
  ),

### 8.	Tambahkan Peti & Petunjuk (Long Press). Di dalam Stack, tambahkan Positioned untuk peti.
![image](https://github.com/user-attachments/assets/cb13d4c4-a8bd-464b-96aa-6feed5f90217)

// Di dalam children dari Stack:
// Peti (Long Press)
Positioned(
  key: _chestKey, // Memberi kunci global pada peti
  bottom: 50,
  right: 50,
  child: GestureDetector(
    onLongPress: () {
      if (_isChestOpen) return;
      setState(() {
        _chestHint = "Sepertinya butuh kunci...";
      });
      // Menghilangkan petunjuk setelah beberapa detik
      Future.delayed(const Duration(seconds: 2), () => setState(() => _chestHint = "Terkunci..."));
    },
    child: Text(_isChestOpen ? "🔓" : "📦", style: TextStyle(fontSize: 80)),
  ),
),
// Teks Petunjuk Peti
Positioned(
  bottom: 140,
  right: 20,
  child: Text(_chestHint, style: const TextStyle(color: Colors.white, fontStyle: FontStyle.italic)),
),

### 9.	Tambahkan Kunci & Logika Drag-and-Drop. Di dalam Stack, tambahkan Positioned untuk kunci. Ini bagian paling kompleks
![image](https://github.com/user-attachments/assets/aa72f8e7-72a5-40e8-b828-f6a1403fa1b3)

// Di dalam children dari Stack:
// Kunci (Drag & Drop)
Positioned(
  left: _keyPosition.dx,
  top: _keyPosition.dy,
  child: GestureDetector(
    onPanUpdate: (details) {
      if (_isChestOpen) return; // Kunci tidak bisa digerakkan jika peti sudah terbuka
      setState(() {
        _keyPosition += details.delta;
      });
    },
    onPanEnd: (details) {
      if (_isChestOpen) return;

      // Cek apakah kunci di-drop di atas peti
      final RenderBox chestBox = _chestKey.currentContext!.findRenderObject() as RenderBox;
      final chestPosition = chestBox.localToGlobal(Offset.zero);
      final chestRect = chestPosition & chestBox.size;

      if (chestRect.contains(_keyPosition)) {
        setState(() {
          _isChestOpen = true;
          _chestHint = "Terbuka!";
          // Sembunyikan kunci
          _keyPosition = const Offset(-100, -100); 
        });
        // Pindah ke halaman akhir setelah quest selesai
        Future.delayed(const Duration(seconds: 2), widget.onQuestComplete);
      }
    },
    child: const Text("🔑", style: TextStyle(fontSize: 50)),
  ),
),


### 10.	Efek Visual (InkWell) → Buka lib/pages/title_page.dart Bungkus ElevatedButton dengan Card dan InkWell untuk efek yang lebih bagus. Ganti ElevatedButton dengan kode berikut:
![image](https://github.com/user-attachments/assets/e453b6ff-ced4-4abc-8cb9-9e3bf6d9eb30)

// Di dalam title_page.dart, ganti ElevatedButton dengan ini:
Card(
  elevation: 8,
  child: InkWell(
    onTap: onStartPressed,
    splashColor: Colors.teal.withAlpha(40),
    child: const Padding(
      padding: EdgeInsets.symmetric(horizontal: 32.0, vertical: 16.0),
      child: Text("Mulai Petualangan", style: TextStyle(fontSize: 20)),
    ),
  ),
),

### 11.	Kita akan membuat latar belakang bisa digeser sedikit secara horizontal atau vertikal (seperti melihat lewat teleskop), tapi tidak keduanya sekaligus.
### 12.	Kembali ke lib/pages/scene_page.dart
### 13.	Kita modifikasi Container latar belakang. Pertama, tambahkan Offset untuk posisi background di State
![image](https://github.com/user-attachments/assets/c5faa5e1-380d-4339-8f44-5a6e2cde5d05)

// Tambahkan variabel state baru
Offset _backgroundOffset = Offset.zero;

### 14.	Bungkus Container latar belakang dengan RawGestureDetector dan Transform
![image](https://github.com/user-attachments/assets/7266a605-47e7-477a-aaf3-07482fa43c3e)

// Ganti Container latar belakang dengan kode ini:
Transform.translate(
              offset: _backgroundOffset,
              child: RawGestureDetector(
                // Mendaftarkan dua recognizer yang bersaing: Horizontal & Vertikal
                gestures: <Type, GestureRecognizerFactory>{
                  HorizontalDragGestureRecognizer: GestureRecognizerFactoryWithHandlers<HorizontalDragGestureRecognizer>(
                        () => HorizontalDragGestureRecognizer(), (instance) {
                    instance.onUpdate = (details) => setState(() => _backgroundOffset += Offset(details.delta.dx, 0));
                  },
                  ),
                  VerticalDragGestureRecognizer: GestureRecognizerFactoryWithHandlers<VerticalDragGestureRecognizer>(
                        () => VerticalDragGestureRecognizer(), (instance) {
                    instance.onUpdate = (details) => setState(() => _backgroundOffset += Offset(0, details.delta.dy));
                  },
                  ),
                },
                child: Container(
                  width: MediaQuery.of(context).size.width * 1.5,
                  height: MediaQuery.of(context).size.height * 1.5,
                  decoration: const BoxDecoration(
                    image: DecorationImage(
                      image: NetworkImage("https://www.nasa.gov/wp-content/uploads/2023/11/iss069e005527-1.jpg"),
                      fit: BoxFit.cover,
                    ),
                  ),
                ),
              ),
            ),


### 15. Di lib/pages/scene_page.dart, bungkus seluruh Stack dengan widget InteractiveViewer
![image](https://github.com/user-attachments/assets/0e1d3067-dc1a-4a07-a2a2-ab2b58cdae68)

// Di build method scene_page.dart
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      // InteractiveViewer untuk mengaktifkan fungsionalitas Pinch-to-Zoom
      body: InteractiveViewer(
        boundaryMargin: const EdgeInsets.all(double.infinity),
        minScale: 0.5,
        maxScale: 3.0,
        child: Stack(
          children: [
          // ... semua widget di dalam Stack tetap sama ...
        ],
      ),
    ),
  );
}

### 21.	Hot Reload dan seluruh adegan interaktif bisa di-zoom dan di-pan dengan mudah.
![Screenshot_20250526_234651](https://github.com/user-attachments/assets/84ef316d-bfd7-4eb0-abe8-cd17ee370fd3)
![Screenshot_20250526_234710](https://github.com/user-attachments/assets/15209cdf-68df-4e15-bdf1-62080c498a7e)
![Screenshot_20250526_234716](https://github.com/user-attachments/assets/84705eab-da70-48f6-bf79-7006fbc13729)
![Screenshot_20250526_234720](https://github.com/user-attachments/assets/27d2092f-f619-46f2-bd2f-503a2cf11cea)
