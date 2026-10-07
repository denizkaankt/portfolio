# OmniReply

**Güzellik salonları için AI resepsiyonist.** WhatsApp ve Instagram'dan gelen mesajlara saniyeler içinde cevap veren, müsait saatleri kontrol edip randevu oluşturan, değiştiren ve iptal eden bir sistem.

**Durum:** Ekim 2026'da kapatıldı. Altyapı silindi; bu klasör projenin belgelerini içerir.

## Problem

Küçük salonlar Instagram ve WhatsApp'tan gelen mesajlara saatler sonra, bazen ertesi gün cevap veriyor. Bu arada müşteri başka salonlara da yazıyor ve ilk cevap verene gidiyor.

## Ne yaptım

- **WhatsApp Cloud API ve Instagram DM entegrasyonu.** Salonun kendi Meta işletme hesabı üzerinden, App Review beklemeden çalışan bir köprü kurulumu.
- **AI asistan.** Fiyat sorularını salonun gerçek listesinden cevaplıyor, boş saatleri gösteriyor, personele göre randevu alıyor, "yarına alalım" / "iptal edin" gibi talepleri işliyor.
- **Yönetim paneli.** Randevular, elle ekleme, AI'ı kapatıp konuşmayı devralma, şube bazlı yetkilendirme.
- **Web randevu sayfası.** Her şubeye özel link.
- **Hatırlatmalar.** Onaylı WhatsApp şablonuyla randevu hatırlatma.
- **KVKK uyumu.** İlk mesajda AI bilgilendirmesi, randevu öncesi açık rıza, yurt dışı aktarım onayı, veri saklama süreleri.

## Teknoloji

| Katman | Araç |
|---|---|
| Otomasyon | n8n (19 workflow) |
| API servisi | Özel `omnireply-api` servisi (systemd) |
| AI | OpenAI GPT-4.1 mini |
| Veritabanı | PostgreSQL 15 (Docker) |
| Geçici durum / hatırlatma | Upstash Redis |
| Sunucu | Hetzner VPS |
| Ağ / site | Cloudflare (DNS + Pages) |
| Mesajlaşma | Meta WhatsApp Cloud API, Instagram Messaging API |

## İş modeli

- 14 gün ücretsiz deneme, kurulum ücretsiz.
- Kanal başına (bir WhatsApp numarası ya da bir Instagram hesabı) aylık 2.490 TL + KDV; 3 aylık ve yıllık planlar.
- Cevap süresi garantisi: AI'ın aylık ortalama cevap süresi 20 saniyeyi geçerse o ayın ücreti iade.

## Belgeler

| Dosya | İçerik |
|---|---|
| [docs/sozlesme-paketi-v3.md](docs/sozlesme-paketi-v3.md) | Hizmet sözleşmesi taslağı, veri işleme sözleşmesi, aydınlatma ve açık rıza metinleri |
| [docs/hizmet-sozlesmesi.pdf](docs/hizmet-sozlesmesi.pdf) | Hizmet sözleşmesinin son hâli |
| [docs/pilot-protokolu.md](docs/pilot-protokolu.md) | Ücretsiz pilot protokolü |
| [docs/pilot-kurulum-gunu.md](docs/pilot-kurulum-gunu.md) | Salon kurulum günü kontrol listesi (Meta, numara, token, webhook, test) |
| [docs/web-sitesi-sozlesmesi.pdf](docs/web-sitesi-sozlesmesi.pdf) | Salonlara web sitesi hizmeti için sözleşme şablonu |
| [sales/satis-scripti.html](sales/satis-scripti.html) | Telefonla satış senaryosu |
| [sales/demo-senaryosu.html](sales/demo-senaryosu.html) | Canlı demo akışı |

## Neden kapattım

- **Pazar kalabalık.** Salonlara hizmet veren dijital ajanslar aynı hizmeti zaten paket içinde, ayda ~30.000 TL'ye satıyor.
- **Teknoloji bir hendek değil.** Ajanslara white-label tedarikçi olmayı denedim; cevap "bunu salon başına ayda 1–2 saatte kendimiz kurarız" oldu.
- **Dağıtım yavaştı.** Yaklaşık bir yılda ~15 salonla görüştüm, 1 demo ve 1 pilot çıktı; pilot salon da sonradan ulaşılamaz oldu.
- **Kalan tek yol**, ajansların gitmediği küçük salonlara emek yoğun bir hizmet işi kurmaktı. Bu benim yapmak istediğim iş değildi.

## Öğrendiklerim

- Meta Business, WhatsApp Cloud API ve Instagram API'nin gerçek dünyadaki kısıtları (App Review, sistem kullanıcıları, token yönetimi, şablon onayları).
- Tek başına üretim ortamı işletmek: Docker, Postgres, yedekleme, firewall, izleme.
- KVKK'yı ürün tasarımına baştan dahil etmek.
- En önemlisi: **ürünü kurmadan önce pazarı test etmek.**
