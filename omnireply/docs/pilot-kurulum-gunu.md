# OmniReply — Pilot Kurulum Günü

> Toplantıda açık tut, sırayla işaretle, süreleri not al.
> **Toplam: ~2 saat.** Çoğunu sen tek başına yapıyorsun; salonun dokunduğu yerler aşağıda.

---

## Salonun yapacağı şeylerin tamamı

| Ne | Ne zaman | Süre |
|---|---|---|
| Meta portföyü açıp seni yönetici eklemek | **Toplantıdan önce, evde** (aşağıdaki mesajla) | 5 dk |
| Protokolü imzalamak | Toplantı başında | 10 dk |
| Telefonunu vermek: yedek, hesap silme, doğrulama kodu | E adımında — **sen yanındasın** | 10 dk |
| Kartını Meta'ya girmek | F adımında | 3 dk |
| Kendi şifresini belirlemek | Sonda | 2 dk |

Geri kalan her şeyi sen yapıyorsun.

---

## BU HAFTA — Test kurulumu (zorunlu)

Ucuz bir hat + ikinci bir Facebook hesabıyla ("salon" rolü) B'den L'ye her şeyi bir kez yap.

| Soru | Cevap |
|---|---|
| Hesap silindikten kaç dk sonra numara Meta'ya eklenebildi? | |
| Görünen ad onayı ne kadar sürdü? Onaysızken mesaj gidiyor mu? | |
| Şablon onayı ne kadar sürdü? | |
| Yayınlanmamış uygulamaya **başka bir telefondan** yazılan mesaj geliyor mu? | |
| Toplam süre | |

---

## 2-3 GÜN ÖNCE — Salona gönder

> Merhaba, kurulumu hızlandırmak için toplantıdan önce 5 dakikalık bir şey rica edeceğim:
>
> 1. Bilgisayardan **business.facebook.com** adresine kendi Facebook hesabınızla girin.
>    Instagram reklamı veriyorsanız zaten bir işletme hesabınız vardır, onu kullanın.
>    Yoksa "Hesap oluştur" deyip salonunuzun adını ve e-postanızı yazın.
> 2. Sol menüden **Ayarlar → Kişiler → Kişi ekle**'ye tıklayın.
> 3. **deniz@EPOSTAN** adresini yazın, **Tam kontrol** seçip davet gönderin.
>
> Görüşmede hesabınızı ben kuracağım, sizden sadece telefonunuzla bir doğrulama kodu
> ve Meta'ya ekleyeceğiniz bir kart gerekecek. Bir de WhatsApp sohbet yedeğinizi alırsanız
> iyi olur: WhatsApp Business → Ayarlar → Sohbetler → Sohbet yedeği.

- [ ] Davet e-postası geldi mi? **Kabul et**, salonun portföyüne geçebildiğini gör
- [ ] Gelmediyse toplantıda ilk iş bu (salon 5 dk yapar)

## 1 GÜN ÖNCE — Hazırlık

- [ ] Hizmet listesi, saatler, kapalı günler geldi mi — bir notta topla (K adımında panele gireceksin)
- [ ] Protokol: 2 nüsha yazdır, kendi bilgilerini doldur
- [ ] Doğrulama token'ını not al:
```bash
grep '^META_VERIFY_TOKEN=' /opt/omnireply-api/.env | cut -d= -f2-
```
- [ ] Kartvizit, laptop şarjlı

---

## TOPLANTI GÜNÜ

### A. Protokol — 10 dk · 👥 SALON İMZALAR

İmzadan **önce** sözlü anlat:
- Numaranın WhatsApp Business hesabı **silinecek**, uygulamadaki sohbet geçmişi kaybolacak
- Hesap onların Meta hesabında, sen yöneticisin; pilot boyunca erişimi kaldırmayacaklar (m.3.2)
- Meta mesaj ücretleri kendi kartlarına, çok küçük tutarlar (m.3.3)
- Meta kuralları değişirse hesapları etkilenebilir (m.7.7)
- 14 gün, otomatik ücret yok

- [ ] Protokol, EK-1, EK-2 imzalı; her sayfa paraflı
- [ ] EK-2'de salonun başvuru e-postası/telefonu yazılı

### B–C. Portföy ve yetki — ✓ önceden yapıldı

- [ ] Salonun portföyüne geçebiliyorsun (sağ üst → işletme seçici)
- [ ] Yapılmadıysa şimdi: salon 5 dk (yukarıdaki mesajdaki adımlar)

### D. Uygulama — 10 dk · 🧑‍💻 SEN

- [ ] **developers.facebook.com** → Uygulamalarım → **Uygulama oluştur**
- [ ] Kullanım durumu: **WhatsApp ile müşterilerle bağlantı kur**
- [ ] Ad: `OmniReply - <Salon>`, e-posta: info@omnireply.io
- [ ] İşletme portföyü: **SALONUNKİ** — en kritik seçim, kendi portföyünü seçme
- [ ] Oluştur
- [ ] Uygulama ayarları → **Temel** → **App secret → Göster** → bir kenara al (K'da girilecek)
- [ ] Aynı sayfada: Gizlilik URL `https://omnireply.io/gizlilik`, Koşullar URL `https://omnireply.io/kullanim-kosullari`

### E. Numara — 15-30 dk · 🧑‍💻 SEN + 📱 SALONUN TELEFONU

> Geri alınması zor tek adım. Salon hazır değilse burada dur.

1. [ ] Salonun telefonunda sohbet yedeği alındı mı? Alınmadıysa şimdi al, bitmesini bekle
2. [ ] **WhatsApp Manager** → Telefon numaraları → **Numara ekle**
3. [ ] Görünen ad: salonun gerçek adı · Kategori: Güzellik, Spa ve Salon
4. [ ] Numarayı gir → "mevcut bir WhatsApp hesabına kayıtlı" hatası gelecek
5. [ ] 📱 Salonun telefonunda: **WhatsApp Business → Ayarlar → Hesap → Hesabımı sil** → numara → Sil
6. [ ] **3 dk bekle**, tekrar ekle
7. [ ] 📱 Kod SMS ile gelir → salonun telefonundan oku, sen gir
8. [ ] `phone_number_id` ve `WABA_ID`'yi not et (WhatsApp → API Kurulumu)

### F. Ödeme — 3 dk · 💳 SALON

- [ ] WhatsApp Manager → **Ödeme yöntemleri → Ekle** → salon kartını kendisi girer
- [ ] Ülke ve para birimi doğru mu

### G. Token — 10 dk · 🧑‍💻 SEN

- [ ] İşletme ayarları → **Sistem kullanıcıları → Ekle** → `omnireply-api`, rol **Yönetici**
- [ ] **Varlık ata:** uygulama (tam kontrol) + WABA (tam kontrol)
- [ ] **Token oluştur** → süre **Hiçbir zaman** → `whatsapp_business_messaging` + `whatsapp_business_management`
- [ ] Kopyala — bir daha gösterilmez
- [ ] Numarayı Cloud API'ye kaydet (6 haneli PIN seç, bir yere yaz):
```bash
curl -s -X POST "https://graph.facebook.com/v21.0/PHONE_NUMBER_ID/register" \
  -H "Authorization: Bearer TOKEN" -H "Content-Type: application/json" \
  -d '{"messaging_product":"whatsapp","pin":"123456"}'
```
`{"success":true}` → devam. **Hata alırsan körlemesine tekrar deneme**: 10 hatalı denemede numara 72 saat kilitlenir.

### K. Sistem kaydı — 10 dk · 🧑‍💻 SEN

> Webhook'tan (H) **önce** yap: gizli anahtar veritabanında olmazsa mesajlar imza kontrolünde reddedilir.

Panel → Profil → **Şube ekle**:
- [ ] Ad, şehir, telefon, WhatsApp numarası
- [ ] **phone_number_id** ve **access_token** (G'deki token)
- [ ] Çalışma saatleri, hizmetler (süre + fiyat)
- [ ] **Personel** (salon personele göre randevu alıyorsa — görüşme formu 8. madde): şube yönetiminde
      her personeli ekle ve **verdiği bütün hizmetleri işaretle**. İşaretlenmeyen hizmet "personelden
      bağımsız" sayılır, o hizmette müşteri personel seçemez. Personel ayrı saat girilmezse salonun
      saatleriyle çalışır.
- [ ] Kullanıcı adı, geçici şifre (8+ karakter), **salonun e-postası**
- [ ] Kaydet

Sunucuda:
```bash
docker exec omnireply-db psql -U omnireply -d omnireply -c \
"UPDATE branches SET manager_phone='90XXXXXXXXXX', app_secret='APP_SECRET', status='active' WHERE branch_id='PHONE_NUMBER_ID';"
```
Tatiller:
```bash
docker exec omnireply-db psql -U omnireply -d omnireply -c \
"INSERT INTO branch_holidays (branch_id, holiday_date, description) VALUES ('PHONE_NUMBER_ID','2026-10-29','Cumhuriyet Bayramı');"
```

### H. Webhook — 5 dk · 🧑‍💻 SEN

- [ ] Uygulama → WhatsApp → **Yapılandırma** → Webhook → Düzenle
  - Callback URL: `https://app.omnireply.io/api/meta-webhook`
  - Verify token: `.env`'deki `META_VERIFY_TOKEN`
  - **Verify and save**
- [ ] Webhook alanları → **messages** → Abone ol
- [ ] Uygulamayı WABA'ya abone et:
```bash
curl -s -X POST "https://graph.facebook.com/v21.0/WABA_ID/subscribed_apps" -H "Authorization: Bearer TOKEN"
```

### J. Hatırlatma şablonu — 5 dk · 🧑‍💻 SEN

- [ ] WhatsApp Manager → Mesaj şablonları → **Oluştur** → Utility · `randevu_hatirlatma` · Türkçe
- [ ] Gövde: `Merhaba {{1}}, {{2}} tarihinde saat {{3}} için {{4}} randevunuzu hatırlatmak isteriz.`
- [ ] Örnekler: Ayşe · 5 Ekim · 14:00 · Saç Kesimi → Gönder
- [ ] Salona: onaylanana kadar hatırlatma gitmez, asistan çalışır

### L. Canlı test — 15 dk · 🧑‍💻 SEN

- [ ] **Başka bir telefondan** (senin kişisel numaran değil) salonun numarasına "Merhaba" → cevap
- [ ] "Fiyatlarınız nedir" → salonun gerçek listesi
- [ ] "Yarın müsait misiniz" → gerçek boş saatler
- [ ] Personel tanımlıysa: "<personel adı> Hanım'a randevu" → o kişinin saatleri, onayda adı geçiyor
- [ ] Randevu al → isim + KVKK onayı → oluştu, panelde görünüyor
- [ ] "Randevum ne zaman?" → listeliyor · "Yarına alalım" → taşıyor · "İptal edin" → onay isteyip iptal ediyor
- [ ] Panelden AI kapat → cevap gelmiyor · panelden yaz → düşüyor · AI aç
- [ ] Salon kullanıcısıyla giriş → **sadece kendi şubesi**, "Tüm Şubeler" yok
- [ ] `app.omnireply.io/book/PHONE_NUMBER_ID` açılıyor
- [ ] Log temiz: `journalctl -u omnireply-api -n 30 --no-pager | grep WEBHOOK`

> Cevap gelmiyorsa ve logda "GEÇERSİZ İMZA" yoksa: uygulamayı **yayınla** (Uygulama ayarları →
> Yayınla) ve tekrar dene. Demo uygulaması yayınsız çalışıyor, ama salonunki farklı davranırsa çözüm bu.

### M. Teslim — 10 dk · 👥 SALON

- [ ] Salon panele girer, **Profil → Şifremi değiştir** → kendi şifresi (senin verdiğin geçici şifre ölür)
- [ ] Telefona kısayol
- [ ] Göster: randevular, elle ekleme, asistanı durdurma, konuşmayı devralma, 24 saat kuralı
- [ ] Söyle: Meta'daki yönetici erişimini kaldırmayın · randevu notlarına sağlık bilgisi yazmayın
- [ ] Şifresini unutursa: giriş ekranında **Şifremi unuttum** — sana ulaşmasına gerek yok

---

## KURULUMDAN SONRA

**Aynı akşam:** `tail -20 /var/log/omnireply-watch.log` · numaraya bir mesaj daha
**Ertesi gün:** şablon ve görünen ad onaylandı mı · gece gelen mesajlara cevap gitmiş mi
**14 gün boyunca:** 2-3 günde bir konuşmaları oku
- **5. gün** — 15 dk görüşme: ilk izlenim, asistanın takıldığı sorular, düzeltmeler
- **10. gün** — 15 dk görüşme: ilk rakamlar (mesaj, mesai dışı oran, randevu), son ayarlar
- **14. gün** — değerlendirme: rakamlar görüşme formundaki "öncesi" ile karşılaştırılır; devam, uzatma ya da bitiş kararı

---

## Notlar

- Toplam süre: ______
- Salonun beklediği süre (senin tek başına çalıştığın): ______
- Hesap silme → numara ekleme arası: ______ dk
- Bir dahaki sefere önceden yapılabilecek: ______
