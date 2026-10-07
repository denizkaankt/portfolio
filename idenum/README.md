# IDENUM

**Zihinsel yükü azaltan bilişsel asistan.** Kullanıcı aklındakini sesle söyler ("yarın Ahmet'i ara", "saat 3'te toplantı var"); IDENUM bunu açık bir döngü olarak kaydeder ve doğru zamanda, gerekirse yol süresini hesaba katarak hatırlatır.

**Durum:** Ekim 2026'da kapatıldı.

## Mimari yaklaşım

- **Önce deterministik.** MVP'deki yakalama motoru LLM kullanmıyor; tamamen kural ve regex tabanlı. Daha ucuz, daha öngörülebilir ve test edilebilir. LLM katmanı, sağlayıcıdan bağımsız olacak şekilde sonraya bırakıldı.
- **Davranış hiyerarşisi.** Sistem her olay için en az müdahaleci davranışı seçer: yok say → sessizce izle → hatırlat → öner → gerekçeli tavsiye.
- **Güven ve izin kontrolleri**, tekrar eden müdahaleyi önleme ve bekleme süreleri.
- **27 mimari karar kaydı (ADR)**: PWA seçimi, event sourcing'in reddedilmesi, Web Push, yalnızca yakalama anında konum gibi kararlar gerekçeleriyle belgelendi.

## Teknoloji

TypeScript backend · PostgreSQL · PWA · Web Push (VAPID) · Web Speech API · Mapbox

## Belgeler

| Dosya | İçerik |
|---|---|
| [docs/engineering.md](docs/engineering.md) | Mimari, davranış motoru, veri modeli ve mühendislik kararları |
| [docs/kvkk-rehberi.md](docs/kvkk-rehberi.md) | IDENUM için KVKK aydınlatma ve açık rıza rehberi |
| [app/index.html](app/index.html) | Uygulamanın ön yüzü |

## Neden kapattım

Kişisel asistan alanı büyük teknoloji şirketlerinin ürünleriyle dolu. Tek başına ayrışmanın gerçekçi bir yolunu göremedim.
