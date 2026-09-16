import SwiftUI

// MARK: - Model
struct Ayah: Identifiable, Codable {
    var id = UUID()
    let surahNumber: Int
    let ayahNumber: Int
    let textArabic: String
    let translation: String
    var isArchived: Bool = false
}

struct SurahProgress: Identifiable, Codable {
    var id: Int { surahNumber }
    let surahNumber: Int
    let surahName: String
    var isCompleted: Bool
}

// MARK: - Repository & State Management
class QuranRepository: ObservableObject {
    @Published var activeAyahs: [Ayah] = []
    @Published var surahs: [SurahProgress] = []
    @Published var searchText: String = ""
    
    init() {
        loadMockData()
    }
    
    func loadMockData() {
        activeAyahs = [
            Ayah(surahNumber: 1, ayahNumber: 1, textArabic: "بِسْمِ اللَّهِ الرَّحْمَٰنِ الرَّحِيمِ", translation: "Mərhəmətli və Rəhmli Allahın adı ilə.", isArchived: false),
            Ayah(surahNumber: 1, ayahNumber: 2, textArabic: "الْحَمْدُ لِلَّهِ رَبِّ الْعَالَمِينَ", translation: "Həmd olsun Allaha, aləmlərin Rəbbinə.", isArchived: false)
        ]
        
        surahs = [
            SurahProgress(surahNumber: 1, surahName: "əl-Fatihə", isCompleted: true),
            SurahProgress(surahNumber: 2, surahName: "əl-Bəqərə", isCompleted: false)
        ]
    }
    
    func addAyah(surah: Int, number: Int, arabic: String, translation: String) {
        let newAyah = Ayah(surahNumber: surah, ayahNumber: number, textArabic: arabic, translation: translation)
        activeAyahs.append(newAyah)
    }
    
    func duplicateAyah(id: UUID) {
        if let ayah = activeAyahs.first(where: { $0.id == id }) {
            let duplicated = Ayah(surahNumber: ayah.surahNumber, ayahNumber: ayah.ayahNumber, textArabic: ayah.textArabic, translation: ayah.translation, isArchived: ayah.isArchived)
            activeAyahs.append(duplicated)
        }
    }
    
    func deleteAyah(id: UUID) {
        activeAyahs.removeAll { $0.id == id }
    }
    
    func copyAyahContent(id: UUID) -> String {
        guard let ayah = activeAyahs.first(where: { $0.id == id }) else { return "" }
        return "[\(ayah.surahNumber):\(ayah.ayahNumber)] \(ayah.textArabic)\n\(ayah.translation)"
    }
    
    func archiveAyah(id: UUID) {
        if let index = activeAyahs.firstIndex(where: { $0.id == id }) {
            activeAyahs[index].isArchived.toggle()
        }
    }
    
    func restoreArchivedAyahs() {
        for index in activeAyahs.indices {
            activeAyahs[index].isArchived = false
        }
    }
    
    var filteredAyahs: [Ayah] {
        if searchText.isEmpty {
            return activeAyahs
        } else {
            return activeAyahs.filter { 
                String($0.ayahNumber).contains(searchText) || 
                String($0.surahNumber).contains(searchText) 
            }
        }
    }
}

// MARK: - Main View
struct QuranReaderView: View {
    @StateObject private var vm = QuranRepository()
    @State private var showAddSheet = false
    
    var backgroundColor: Color {
        Color(red: 0.97, green: 0.95, blue: 0.91)
    }
    
    var textColor: Color {
        Color(red: 0.15, green: 0.12, blue: 0.08)
    }
    
    var body: some View {
        NavigationView {
            ZStack {
                backgroundColor.ignoresSafeArea()
                
                VStack(spacing: 0) {
                    // Rəqəmlə axtarış sətri
                    HStack {
                        Image(systemName: "magnifyingglass")
                            .foregroundColor(textColor.opacity(0.5))
                        TextField("Rəqəmlə axtar (Məs: 1, 2...)", text: $vm.searchText)
                            .textFieldStyle(PlainTextFieldStyle())
                    }
                    .padding(12)
                    .background(Color.white.opacity(0.6))
                    .cornerRadius(10)
                    .padding(.horizontal, 20)
                    .padding(.top, 10)
                    
                    // Surə Statusları (Hazır / İçi Boş)
                    ScrollView(.horizontal, showsIndicators: false) {
                        HStack(spacing: 12) {
                            ForEach(vm.surahs) { surah in
                                HStack(spacing: 6) {
                                    Text(surah.surahName)
                                        .font(.subheadline)
                                        .fontWeight(.semibold)
                                    
                                    Circle()
                                        .fill(surah.isCompleted ? Color.green : Color.orange)
                                        .frame(width: 8, height: 8)
                                    
                                    Text(surah.isCompleted ? "Hazır" : "İçi Boş")
                                        .font(.caption2)
                                        .foregroundColor(textColor.opacity(0.6))
                                }
                                .padding(.horizontal, 12)
                                .padding(.vertical, 8)
                                .background(Color.white.opacity(0.4))
                                .cornerRadius(8)
                            }
                        }
                        .padding(.horizontal, 20)
                        .padding(.vertical, 12)
                    }
                    
                    // Ayələr siyahısı
                    ScrollView {
                        VStack(spacing: 28) {
                            ForEach(vm.filteredAyahs) { ayah in
                                AyahRowView(
                                    ayah: ayah,
                                    textColor: textColor,
                                    onCopy: { 
                                        UIPasteboard.general.string = vm.copyAyahContent(id: ayah.id) 
                                    },
                                    onDuplicate: {
                                        vm.duplicateAyah(id: ayah.id)
                                    },
                                    onDelete: { 
                                        vm.deleteAyah(id: ayah.id) 
                                    },
                                    onArchive: { 
                                        vm.archiveAyah(id: ayah.id) 
                                    }
                                )
                            }
                            
                            // Müəllif
                            VStack(spacing: 4) {
                                Text("Müəllif: Ağa Babayev")
                                    .font(.caption)
                                    .fontWeight(.medium)
                                Text("Demo Versiya")
                                    .font(.caption2)
                            }
                            .foregroundColor(textColor.opacity(0.5))
                            .padding(.top, 30)
                            .padding(.bottom, 20)
                        }
                        .padding(.horizontal, 20)
                        .padding(.vertical, 10)
                    }
                }
            }
            .navigationTitle("Quran Proqramı")
            .navigationBarTitleDisplayMode(.inline)
            .toolbar {
                ToolbarItem(placement: .navigationBarTrailing) {
                    Menu {
                        Button("Arxivdən Bərpa Et") {
                            vm.restoreArchivedAyahs()
                        }
                        Button("Yeni Ayə Əlavə Et") {
                            showAddSheet = true
                        }
                    } label: {
                        Image(systemName: "ellipsis.circle")
                            .foregroundColor(textColor)
                    }
                }
            }
        }
    }
}

// MARK: - Ayah Row Component
struct AyahRowView: View {
    let ayah: Ayah
    let textColor: Color
    var onCopy: () -> Void
    var onDuplicate: () -> Void
    var onDelete: () -> Void
    var onArchive: () -> Void
    
    var body: some View {
        VStack(alignment: .trailing, spacing: 16) {
            Text(ayah.textArabic)
                .font(.system(size: 24, weight: .medium))
                .foregroundColor(textColor)
                .multilineTextAlignment(.trailing)
                .lineSpacing(10)
            
            Text(ayah.translation)
                .font(.body)
                .foregroundColor(textColor.opacity(0.85))
                .multilineTextAlignment(.leading)
                .frame(maxWidth: .infinity, alignment: .leading)
            
            HStack(spacing: 16) {
                Button(action: onArchive) {
                    Image(systemName: ayah.isArchived ? "archivebox.fill" : "archivebox")
                        .font(.caption)
                }
                Button(action: onCopy) {
                    Image(systemName: "doc.on.doc")
                        .font(.caption)
                }
                Button(action: onDuplicate) {
                    Image(systemName: "plus.square.on.square")
                        .font(.caption)
                }
                Button(action: onDelete) {
                    Image(systemName: "trash")
                        .font(.caption)
                        .foregroundColor(.red)
                }
                Spacer()
                
                Text(ayah.isArchived ? "[Arxivdə]" : "")
                    .font(.caption2)
                    .foregroundColor(.orange)
                
                Text("[\(ayah.surahNumber):\(ayah.ayahNumber)]")
                    .font(.caption2)
                    .fontWeight(.bold)
                    .opacity(0.6)
            }
            .foregroundColor(textColor)
            Divider().background(textColor.opacity(0.15))
        }
        .padding(14)
        .background(Color.white.opacity(0.25))
        .cornerRadius(12)
    }
}
