#include <iostream>
#include <vector>
#include <cstdlib>
#include <ctime>
#include <string>
#include <list>
#include <set>
#include <fstream> // Untuk file handling
using namespace std;

// Struktur untuk menyimpan data permainan
struct GameRecord {
    string playerName;
    string category;
    string secretWord;
    int attempts;
    bool won;
    string timestamp;
};

// Fungsi untuk mendapatkan waktu saat ini
string getCurrentTimestamp() {
    time_t now = time(0);
    string timestamp = ctime(&now);
    timestamp.pop_back(); // Menghapus newline di akhir
    return timestamp;
}

// Fungsi untuk menyimpan data permainan ke CSV
void saveGameToCSV(const GameRecord& record) {
    ofstream file("game_history.csv", ios::app); // append mode
    if (file.is_open()) {
        // Jika file baru, tambahkan header
        file.seekp(0, ios::end);
        if (file.tellp() == 0) {
            file << "Nama Pemain,Kategori,Kata Rahasia,Jumlah Tebakan,Hasil,Waktu\n";
        }
        
        // Tulis record ke file
        file << record.playerName << ","
             << record.category << ","
             << record.secretWord << ","
             << record.attempts << ","
             << (record.won ? "Menang" : "Kalah") << ","
             << record.timestamp << "\n";
        
        file.close();
        cout << "Data permainan berhasil disimpan!\n";
    } else {
        cout << "Error: Tidak dapat membuka file!\n";
    }
}

[... kode sebelumnya tetap sama sampai main() ...]

int main() {
    string playerName;
    cout << "Masukkan nama pemain: ";
    getline(cin, playerName);

    // Kategori kata
    vector<string> fruits = {"apel", "pisang", "markisa", "manggis", "blewah", "melon", "kiwi"};
    vector<string> animals = {"harimau", "kuda nil", "jerapah", "buaya", "panda", "zebra", "koala"};
    vector<string> colors = {"merah", "biru", "hijau", "kuning", "coklat", "biru dongker", "pink"};
    vector<string> all;
    all.insert(all.end(), fruits.begin(), fruits.end());
    all.insert(all.end(), animals.begin(), animals.end());
    all.insert(all.end(), colors.begin(), colors.end());

    // Pilih kategori
    int categoryChoice = chooseCategory();
    vector<string> selectedCategory;
    string categoryName;

    // Menentukan kategori berdasarkan pilihan pemain
    switch (categoryChoice) {
        case 1:
            selectedCategory = fruits;
            categoryName = "Buah";
            cout << "Kategori yang kamu pilih: Buah\n";
            break;
        case 2:
            selectedCategory = animals;
            categoryName = "Hewan";
            cout << "Kategori yang kamu pilih: Hewan\n";
            break;
        case 3:
            selectedCategory = colors;
            categoryName = "Warna";
            cout << "Kategori yang kamu pilih: Warna\n";
            break;
        default:
            cout << "oh no! u choose the hard mode \n";
            selectedCategory = all;
            categoryName = "Campuran";
    }

    // Memilih kata rahasia secara acak
    string secretWord = pickRandomWord(selectedCategory);
    
    string guess;
    int attempts = 0;
    const int MAX_ATTEMPTS = 5;
    int clueCount = 0;
    bool won = false;
    
    cout << "Kata ini memiliki " << secretWord.size() << " huruf.\n";
    cout << "Kamu memiliki " << MAX_ATTEMPTS << " kesempatan untuk menebak kata." << endl;

    while (attempts < MAX_ATTEMPTS) {
        cout << "Masukkan tebakan kata: ";
        cin >> guess;
        attempts++;

        // Menampilkan hasil
        if (guess == secretWord) {
            cout << "Selamat! Kamu menebak kata dengan benar!" << endl;
            won = true;
            break;
        } else {
            if (clueCount < 2) {
                giveClue(secretWord);
                clueCount++;
            } else {
                cout << "Kamu sudah menggunakan semua petunjuk yang tersedia." << endl;
            }
            cout << "Tebakan salah. Coba lagi!" << endl;
            cout << "Kamu punya " << (MAX_ATTEMPTS - attempts) << " kesempatan lagi.\n";
        }

        if (attempts == MAX_ATTEMPTS) {
            cout << "Sayang sekali, kamu kehabisan kesempatan. Kata yang benar adalah: " << secretWord << endl;
        }
    }

    // Menyimpan data permainan
    GameRecord record = {
        playerName,
        categoryName,
        secretWord,
        attempts,
        won,
        getCurrentTimestamp()
    };
    saveGameToCSV(record);

    return 0;
}
