# Oyun Geliştirme İçin Skill, Plugin ve Araç Rehberi

Tarih: 2026-10-09. Henüz oyun fikri yok; bu belge "ne kurayım, hangi stack'i seçeyim, fikri nasıl bulayım" sorularına cevap verir.

Araştırma 3 alt agent ile paralel yapıldı:

- Sonnet 5.5: açık kaynak skill / plugin / MCP taraması (GitHub, skills.sh)
- Sonnet 5.5: stack karşılaştırması (AI agent ile headless çalışma odaklı)
- Haiku 5.5: fikir bulma kiti, GDD şablonu, milestone planı
- Fable 5.1 (koordinatör): claude.ai plugin kataloğu taraması ve birleştirme

---

## 1. Özet öneri

| Durum | Öneri |
|---|---|
| Fikir yok, hızlı prototip | **Phaser 4 + TypeScript + Vite**, test için Vitest + Playwright (headless Chromium) |
| Fikir oturdu, Steam / mobil hedefi | **Godot 4 (GDScript)**, godot-mcp + GodotPrompter |
| 3D istiyorsan | Three.js (web) veya Godot 4 |
| Konsol / büyük asset ekosistemi şart | Unity (CI sürtünmesini kabul ederek) |

Kural: fikir bilinmiyorsa web'de başla. Fikir kanıtlandıysa ve Steam/mobile çıkacaksa Godot.

Neden web? Agent'ın kapalı geri besleme döngüsü (kod yaz, çalıştır, ekran görüntüsü al, düzelt) web'de neredeyse bedava. Proje dosyaları metin olduğu için agent her şeyi düzenleyebilir. Unity ve Bevy'de bu döngü en zor olan.

---

## 2. Hemen kurulabilecekler (claude.ai plugin kataloğu)

Bunlar hesabındaki katalogda zaten var, `/plugin` ile etkinleştirilebilir.

| Plugin | Yayıncı | Ne işe yarar | Ne zaman |
|---|---|---|---|
| **SpriteCook** | Topluluk (MCP) | Sprite, tileset, UI kit, kısa animasyon üretimi; Godot'a aktarma skill'i var | 2D oyun, çizim yapamıyorsan |
| **Unity** | Unity Technologies (resmi) | 31 skill: yeni proje, tilemap, sprite, UI, fizik, ses, web build, IAP, multiplayer | Unity seçersen |
| **Unreal Engine Skills** | Epic Games (resmi) | Unreal Editor'ü MCP ile kontrol: actor, blueprint, materyal, Niagara, test | Unreal seçersen |
| **parallax-threejs** | Topluluk | Three.js/GLSL hata ayıklama, görsel regresyon testi, Playwright + Chrome DevTools MCP | Three.js seçersen |
| **playwright** | Microsoft | Tarayıcıyı sürer; web oyununu Claude'un kendisi oynayıp ekran görüntüsü alır | Her web oyunu |
| **SoundWeaver** | Topluluk | 52 hazır UI ses efekti (WAV + base64) | Menü/buton sesleri |
| **CellCog** | Topluluk (MCP) | game-asset, müzik, ses, 3D model, görsel üretimi | Asset üretimi |
| **image-generation** | Topluluk | Text-to-image prompt rehberi (model bağımsız) | Konsept art / sprite promptları |
| **Applixir** | Topluluk | HTML5/WebGL/Unity Web oyunlara ödüllü video reklam | Monetizasyon (sonra) |
| **unity-perf** | Topluluk | Unity/C# performans, zero-GC, kod standartları | Unity optimizasyon |
| **Design** | Anthropic | Tasarım kritiği, erişilebilirlik, UX yazımı | Menü/HUD tasarımı |

Dikkat: katalogdaki **"phaser"** adlı plugin Phaser oyun motoru DEĞİL; OpenSpec tabanlı bir iş akışı aracı. Phaser skill'i için aşağıdaki açık kaynak pakete bak.

Bu oturumda zaten bağlı olan konektörler:

- **ElevenLabs**: ses efekti ve seslendirme üretimi
- **Supabase**: skor tablosu, kayıt, basit multiplayer backend
- **Vercel / Cloudflare / Hostinger**: web oyununu yayınlama
- **Canva**: itch.io kapak görseli, tanıtım materyali
- **GitHub**: repo yönetimi

---

## 3. Açık kaynak skill paketleri (GitHub / skills.sh)

Hepsi topluluk projesi. Kurmadan önce SKILL.md ve hook dosyalarını oku.

### 3.1 Çok motorlu paketler (başlangıç noktası)

| Paket | Kurulum | İçerik | Güven |
|---|---|---|---|
| **awesome-gamedev-agent-skills** | `npx skills add gamedev-skills/awesome-gamedev-agent-skills` | 74 skill + router. 9 tür (platformer, roguelike, RPG, FPS, tower defense, kart, visual novel, survival, puzzle). Game-jam ve prototipleme iş akışı. Steam + itch.io yayınlama. Godot 16, Unity 8, Unreal 6, Phaser/Pixi/Three 6, Bevy/pygame/LÖVE/Roblox 10 | 1.4k yıldız, Apache-2.0 |
| **Claude-Code-Game-Studios** | `git clone https://github.com/Donchitos/Claude-Code-Game-Studios.git my-game && cd my-game && claude` sonra `/start` | 49 agent, ~72 slash komutu, hook'lar. Tasarım, GDD (`/design-system`), mimari, sprint, QA, release. Her adımı sen onaylarsın. | ~25.9k yıldız, MIT. Çok popüler, ağır süreç |
| HermeticOrmus/claude-code-game-development | https://github.com/HermeticOrmus/claude-code-game-development | 22 plugin + game-dev kılavuzu | Doğrulanmadı (sadece arama) |

### 3.2 Motora özel

| Paket | Kurulum | İçerik | Güven |
|---|---|---|---|
| **GodotPrompter** | `claude plugins marketplace add jame581/skillsmith` → `claude plugins install godot-prompter@skillsmith` | 56 Godot 4 skill: mimari, input, fizik, shader, ses, UI, multiplayer | 799 yıldız, MIT |
| GD-Agentic-Skills | https://github.com/thedivergentai/gd-agentic-skills (`.claude/skills/` içine kopyala) | 99 skill + 27 tür blueprint'i, tipli GDScript | Doğrulanmadı |
| godot-dev | https://github.com/ItsYUGAMES/godot-dev | Router skill + doğrulama kapıları + godot-ai MCP | Doğrulanmadı |
| Godot-Claude-Skills | https://github.com/Randroids-Dojo/Godot-Claude-Skills | GdUnit4 test + PlayGodot e2e | 45 yıldız; repo "deprecated, marketplace'e taşındı" diyor |
| cc-plugin-unity-gamedev | https://github.com/tjboudreaux/cc-plugin-unity-gamedev | 21 Unity skill: Addressables, Cinemachine, UniTask, VContainer | Doğrulanmadı |
| **threejs-skills** | https://github.com/CloudAI-X/threejs-skills (`skills/*` → `.claude/skills/`) | 10 Three.js API skill'i | 3.5k yıldız, MIT |
| threejs-game-skills | https://github.com/majidmanzarpour/threejs-game-skills | 9 skill, tarayıcı oyunu + AI asset | Doğrulanmadı |

Phaser ve Babylon.js için ayrı güçlü paket yok; awesome-gamedev içindeki Phaser skill'ini kullan.

### 3.3 MCP sunucuları

| MCP | Kurulum | Ne yapar | Güven |
|---|---|---|---|
| **godot-mcp** (Coding-Solo) | `claude mcp add godot -- npx @coding-solo/godot-mcp` (`-e GODOT_PATH=...` opsiyonel) | Godot projesini çalıştırır, debug çıktısı alır, sahne düzenler | 6.0k yıldız, MIT |
| **MCP for Unity** (CoplayDev) | Unity Package Manager → git URL `https://github.com/CoplayDev/unity-mcp.git?path=/MCPForUnity#main`; `uv` + Python 3.10+ gerekir | Unity Editor içinde asset/sahne/script yönetimi | 14.8k yıldız, v10.3.0 (2026-10-04), çok aktif |
| Unreal_mcp (ChiR24) | `npx -y unreal-engine-mcp-server@beta` + `McpAutomationBridge` plugin'i | Unreal editörünü C++ köprüyle kontrol | 910 yıldız, aktif |
| **blender-mcp** (ahujasid) | `claude mcp add blender uvx blender-mcp` + Blender addon | 3D modelleme, viewport screenshot, Poly Haven / Sketchfab asset indirme | ~16.9k yıldız |
| pixel-mcp (willibrandon) | Kaynaktan `make build`; Go 1.23+, Aseprite 1.3+ | Pixel art, animasyon, palet, spritesheet export | 150 yıldız |
| aseprite-mcp alternatifleri | MalloyTheDev/aseprite-mcp (156 araç), ayigityol/aseprite-mcp (43 araç) | Headless Aseprite | Doğrulanmadı |
| **Playwright MCP** (Microsoft) | `claude mcp add playwright npx @playwright/mcp@latest` | Gerçek tarayıcıda oyun testi | 37.9k yıldız |

### 3.4 Asset, tasarım, yayınlama

| Araç | Kurulum | Ne yapar |
|---|---|---|
| ai-pixel-art-image-generation | `git clone https://github.com/ianlintner/ai-pixel-art-image-generation.git ~/.claude/skills/...` + pip bağımlılıkları; OpenAI veya Gemini anahtarı ister | Sprite, tileset, spritesheet; Tiled uyumlu (TSX/TMJ) çıktı |
| itch-publish | awesome-gamedev paketinde | `butler push` ile itch.io'ya yükleme |
| game-design-document | `npx -y skills add ityes22/game-design-document --skill game-design-document --agent claude-code` | Tek başına GDD skill'i (küçük, doğrulanmadı) |

sfxr, Tiled/LDtk ve shader için özel skill bulunamadı; awesome-gamedev'in ses ve shader skill'leri en yakını.

---

## 4. Stack karşılaştırması (AI agent gözüyle)

Sürüm notu: Phaser şu an **v4.x** (v4.0 Nisan 2026). Yeni iş için v4 kullan. Godot 4.7 tarihi üçüncü taraf kaynaklardan; godotengine.org'dan doğrula.

| Stack | Headless build/run/test | Asset | Dağıtım | Öğrenme | 2D/3D | Maliyet |
|---|---|---|---|---|---|---|
| **Phaser (TS)** | Mükemmel. npm/Vite, Vitest, Playwright + headless Chromium. CI basit | Kolay (PNG/JSON/Tiled) | Web, itch.io doğal. Mobil: Capacitor. Steam: Electron/Tauri | Düşük | Güçlü 2D | Ücretsiz (MIT) |
| **Three.js / Babylon** | Mükemmel; headless WebGL daha yavaş, screenshot deterministik değil | glTF iyi, 3D art asıl darboğaz | Web ile aynı | Orta | Sadece 3D | Ücretsiz |
| **Canvas / PixiJS** | Mükemmel, en az parça. Fizik/sahne/input'u kendin yazarsın | Kolay | Web ile aynı | Düşük | 2D | Ücretsiz |
| **Godot 4** | Çok iyi. `--headless`, GUT/gdUnit4, `--export-release` CI'da çalışır. Görsel kontrol için xvfb. `.tscn` metin, diff'lenir | İyi (importer, tilemap, animasyon) | En geniş: web, masaüstü, Android/iOS, Steam, itch.io | Düşük-orta | Üst düzey 2D, iyi 3D | Ücretsiz, telifsiz |
| **Unity** | Zayıf-orta. Batchmode lisans + GB'larca kurulum ister. Sahneler gürültülü YAML | Mükemmel ekosistem ama editöre bağlı | Her şey, konsol dahil | Orta-yüksek | Güçlü 2D+3D | Ücretsiz katman, lisans sık değişiyor |
| **Bevy (Rust)** | Mantık için iyi (headless ECS); derleme yavaş, API her sürümde kırılıyor, agent eski kod yazıyor | Zayıf, editör yok | Masaüstü, WASM | Yüksek | İkisi de genç | Ücretsiz |
| **Pygame** | İyi. `SDL_VIDEODRIVER=dummy` | Kolay | PyInstaller; web (pygbag) kırılgan; mobil yok | Çok düşük | 2D, performans sınırlı | Ücretsiz |
| **Love2D** | Orta. Headless için xvfb/stub gerekir. Lua agent için daha az tip güvenli | Kolay | Masaüstü, itch.io; web (love.js) zor | Çok düşük | 2D | Ücretsiz |

---

## 5. Fikir bulma kiti

### 5.1 Solo + AI agent ile 1-2 haftada prototiplenebilecek 10 tür

| Tür | Çekirdek döngü | Kapsam riski |
|---|---|---|
| Puzzle (kaydırma/mantık/match) | Tahtayı oku, kuralı uygula, çöz | Düşük |
| Idle / incremental | Üret, yükselt, sayılar katlanır | Düşük |
| Endless runner | Otomatik koş, kaç/zıpla, hız artar | Düşük |
| Yazma oyunu | Kelimeler düşer, yazarak yok et | Düşük |
| Text adventure | Oku, seç, durum değişir, yeni dal | Orta (yazı hacmi) |
| Roguelike | Kat, dövüş/loot, öl, kalıcı açılımlarla tekrar | Orta (procgen, denge) |
| Tower defense | Yola kule koy, dalgalar büyür, para harca | Orta (pathing, ayar) |
| Fizik sandbox | Nesne koy, simüle et, sonucu izle | Orta (fizik kararsızlığı) |
| Deck-builder | Çek, oyna, dövüşler arası deste kur | Yüksek (kart dengesi, içerik) |
| Ritim | Notalar akar, vuruşta bas, kombo | Yüksek (ses senkronu, beatmap) |

### 5.2 "Oyununu bul" anketi (7 soru)

1. **Platform:** Her gün hangi cihazda test edeceksin: tarayıcı, telefon, PC, konsol?
2. **Oturum süresi:** 2 dk, 10-15 dk, 30+ dk?
3. **Oyuncu sayısı:** Tek, aynı ekran, online? (Online 2 haftalık prototip için yüksek risk; atla.)
4. **Sanat:** Çizebiliyor musun, pixel art, asset paketi, yoksa sadece şekil ve yazı mı?
5. **Ton:** Sakin, gergin, komik, cozy, karanlık, absürt?
6. **Favoriler:** 3 oyun ve her birinde tekrar tekrar oynadığın o tek an.
7. **Zaman:** Haftada kaç saat, toplam kaç hafta?

Türü 2 ve 6'ya göre seç. 7'nin cevabı haftada 10 saatin altındaysa düşük/orta riske in.

### 5.3 Tek sayfalık GDD şablonu

- **Başlık ve pitch:** Oyunu satan tek cümle.
- **Tür, platform, oturum süresi:** Kesin seçimler, dilek listesi değil.
- **Oyuncu fantezisi:** Oyuncu ne hisseder, neye dönüşür.
- **Çekirdek döngü:** Tekrar eden 3-5 adım. Oyun budur.
- **Kontroller:** Her input için bir satır.
- **Kazanma ve kaybetme:** Oturum nasıl başlar, biter, başarılır, başarısız olur.
- **İlerleme:** Zamanla ne değişir (açılımlar, zorluk eğrisi).
- **İçerik kapsamı:** Kesin sayılar (level, düşman, eşya).
- **Sanat ve ses:** Üç sıfat, palet, placeholder planı.
- **Teknoloji:** Motor/stack, hedef FPS, agent'ın ilk yapacağı şey.
- **Kapsam dışı:** Yapmayacağın özelliklerin adı.
- **Riskler ve açık sorular:** Her biri için kes-ya-da-tut karar tarihi.

### 5.4 Milestone planı (önce dikey dilim)

1. **M0 Temel (gün 1-2):** Repo tek komutla çalışır; bir input bir placeholder şekli hareket ettirir. *Bitti: hedef platformda çalışıyor.*
2. **M1 Çirkin çekirdek döngü (gün 3-4):** Dikdörtgen ve yazıyla döngü çalışıyor, menü yok. *Bitti: baştan sona bir tur oynanabiliyor.*
3. **M2 Kurallar ve geri bildirim (gün 5-6):** Kazan, kaybet, skor, yeniden başlat, basit HUD. *Bitti: sayfa yenilemeden tekrar başlıyor.*
4. **M3 Dikey dilim (gün 7-9):** Gerçek art, ses ve vuruş hissi (flash, shake, particle) ile tam bir level/run. *Bitti: yabancı biri açıklama olmadan 5 dk oynuyor.*
5. **M4 Playtest ve kes (gün 10-14):** 3-5 dış playtest, eğlenceli olmayanı kes, kapsamı kilitle. *Bitti: ayarlanmış döngü + yazılı sonraki adımlar.*

Kural: M3'teki dilim eğlenceli olana kadar ikinci level, düşman veya sistem ekleme.

---

## 6. Önerilen kurulum sırası (fikir yokken)

1. `npx skills add gamedev-skills/awesome-gamedev-agent-skills` (prototipleme + tür skill'leri + itch publish)
2. Katalogdan **playwright** ve **SpriteCook** plugin'lerini etkinleştir
3. Phaser 4 + TypeScript + Vite iskeleti; oyun mantığını render'dan ayrı saf TS modüllerinde tut (Vitest ile tarayıcısız test)
4. 5. bölümdeki anketi cevapla, bir tür seç, tek sayfalık GDD yaz
5. M0-M3'ü sırayla geç; itch.io veya Vercel'e link at
6. Fikir tuttuysa ve Steam/mobil hedefleniyorsa: **godot-mcp + GodotPrompter** ile Godot'a geç
