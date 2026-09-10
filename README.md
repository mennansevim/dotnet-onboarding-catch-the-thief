# BootCamp Catch The Thief 🔎

**Kaşıkçı Elması kayıp. Hırsızı SQL ile bulabilir misin?**

28 Temmuz 2024’te **Topkapı Sarayı**’nda Kaşıkçı Elması çalındı. Yetkililer, hırsızın olaydan kısa süre sonra **Divan Yolu Caddesi** çevresinde şüpheli bir işlem yaptığını ve sonrasında İstanbul’dan ayrıldığını düşünüyor.

Elindeki kayıtları ilişkilendir, tanıkları dinle ve kaçışın izini sür.

## Görevin

Yalnızca verilen SQLite veritabanını ve **SQL `SELECT` sorgularını** kullanarak şu üç soruyu yanıtla:

1. **Hırsız kim?**
2. **Hangi şehre kaçtı?**
3. **Ona yardım eden suç ortağı kim?**

Her cevabı veritabanındaki kanıtlarla destekle. Sadece isimleri tahmin etmek yeterli değil.

## Veritabanını indir

### [⬇ istanbul_heist.db — doğrudan indir](https://github.com/mennansevim/dotnet-onboarding-catch-the-thief/releases/download/v1.0.0/istanbul_heist.db)

Alternatif: [Depodaki veritabanı](./istanbul_heist.db) sayfasını açıp **Download raw file** düğmesini kullan.

Veritabanı hazırdır; tablo oluşturman veya veri eklemen gerekmez. SQLite destekleyen bir uygulamada açıp sorgularını çalıştırabilirsin. Komut satırında SQLite kullanıyorsan:

```sh
sqlite3 -readonly istanbul_heist.db
```

İlk olarak hangi tabloların bulunduğunu ve şemalarını inceleyebilirsin:

```sql
SELECT name, sql
FROM sqlite_master
WHERE type = 'table'
ORDER BY name;
```

## Elindeki kayıtlar

| Tablo | İçerik |
| --- | --- |
| `crime_scene_reports` | Olay tarihi, konumu ve açıklaması |
| `interviews` | Tanık isimleri, ifade tarihleri ve ifadeler |
| `topkapi_security_logs` | Topkapı otoparkı giriş/çıkış saatleri ve plakalar |
| `airports` | Havalimanı kodları, isimleri ve şehirleri |
| `flights` | Kalkış/varış havalimanları, tarih ve saat |
| `passengers` | Uçuş, pasaport numarası ve koltuk |
| `people` | İsim, telefon, pasaport numarası ve plaka |
| `phone_calls` | Arayan, aranan, tarih ve saniye cinsinden süre |
| `bank_accounts` | Hesap numarası ve hesap sahibi |
| `atm_transactions` | Hesap, tarih, ATM konumu, işlem türü ve tutar |

Telefonlar `+905321234567` gibi boşluksuz saklanır. Güvenlik hareketleri `giriş` / `çıkış`; ATM işlem türleri `withdraw` (para çekme) / `deposit` (para yatırma) şeklindedir. Tarihler ayrı `year`, `month`, `day` sütunlarındadır. Kayıtlar eğitim amaçlı kurgusaldır; uçuş ve yolcu listeleri gerçek veya eksiksiz operasyon kayıtları değildir.

## Başlangıç ipucu

`crime_scene_reports` tablosunda **28 Temmuz 2024**, **Topkapı Sarayı** ile ilgili kaydı bul. Sonraki adımlar için olay raporunu ve tanık ifadelerini takip et. Kayıt ID’lerini önceden varsayma.

<details>
<summary>Takılırsan: ek ipuçları</summary>

- 28 Temmuz’da **10:15–10:25** arasındaki Topkapı **çıkışlarını** incele; sınır saatleri dahil.
- Aynı gün **Divan Yolu Caddesi** ATM’sinden **para çekenleri** bul.
- Şüpheli kümelerini kesiştir.
- Şüphelinin aynı gün yaptığı **60 saniye veya daha kısa** aramaların alıcılarını araştır.
- **29 Temmuz** İstanbul kalkışlı uçuşları saat sırasına koy; İstanbul’daki tüm havalimanlarını dikkate al.
- En erken uçuşun yolcu ve koltuk kayıtlarıyla bulgularını doğrula.

</details>

## Cevaplarını nasıl teslim edeceksin?

Herkes bu projenin **`cevaplar/` klasörü altında kendi ad-soyad klasörünü** oluşturmalı ve şu iki dosyayı yüklemeli:

```text
cevaplar/
└── Ad-Soyad/
    ├── log.sql
    └── answers.txt
```

Klasör adına kendi adını ve soyadını yaz (örneğin `Ada-Kaya`). Aynı adla bir klasör varsa sonuna GitHub kullanıcı adını ekle: `Ada-Kaya-kullaniciadi`.

- **`log.sql`:** Çalıştırdığın sorguları araştırma sırasıyla kaydet. Her sorgunun üstüne `--` ile **neden yazdığını** ve bulduğun sonucun seni hangi adıma götürdüğünü açıkla.
- **`answers.txt`:** Aşağıdaki alanları doldur:

```text
Thief:
City:
Accomplice:
```

Başlangıç dosyaları [sablon/](./sablon/) klasöründe bulunur; bunları kendi cevap klasörüne kopyala.

### GitHub üzerinden yükleme

1. GitHub hesabınla giriş yap ve bu deponun sağ üstündeki **Fork** düğmesiyle kendi kopyanı oluştur.
2. Bilgisayarında ad-soyad klasörünü oluştur; içine `log.sql` ve `answers.txt` dosyalarını koy.
3. Kendi fork’unda **`cevaplar/`** klasörünü aç. **Add file → Upload files** ile ad-soyad klasörünü içindeki dosyalarla birlikte yükle ve **Commit changes** ile kaydet.
4. Fork’unda **Contribute → Open pull request** yoluyla asıl projeye bir **Pull Request** gönder. Hedef `mennansevim/dotnet-onboarding-catch-the-thief` deposunun `main` dalı olmalı.
5. Başlığı **`Cevap: Ad Soyad`** yap. Açıklamada sorgularınla üç soruyu yanıtladığını belirt ve Pull Request’i gönder.

Herkese açık olması depoya doğrudan yazma yetkisi vermez; cevaplar **Fork → Pull Request** yoluyla teslim edilir. Proje sahibi birleştirdiğinde klasörün bu projede görünür. Pull Request’in ve gönderdiğin cevaplar herkese açık olur.

Yalnızca kendi cevap klasörünü ekle veya güncelle. Veritabanını, soru metnini ve diğer katılımcıların dosyalarını değiştirme. Veritabanının kopyasını cevap klasörüne yükleme. Kendi çözümünü tamamlayana kadar diğer cevapları incelememeni öneririz.

## Teslim kontrolü

- [ ] Klasörüm `cevaplar/Ad-Soyad/` biçiminde.
- [ ] `log.sql` içindeki sorgular yalnızca veri okuyor ve SQLite üzerinde çalışıyor.
- [ ] Her sorgunun nedenini yorum satırlarıyla açıkladım.
- [ ] `answers.txt` içindeki üç alanı doldurdum.
- [ ] Sonuçlarımı tanık, güvenlik, ATM, telefon ve uçuş kayıtlarıyla destekledim.
- [ ] Yalnızca kendi cevap dosyalarımı içeren bir Pull Request açtım.

## Veritabanı doğrulaması

Yayımlanan başlangıç veritabanında **10 tablo ve 927 kayıt** bulunur. SQLite bütünlük ve yabancı anahtar kontrolleri yapılmış, ipucu zinciri salt okunur bağlantıda `SELECT` sorgularıyla çalıştırılmış ve tek bir çözüme ulaştığı doğrulanmıştır.

Bu bulmaca, CS50’deki SQL dedektiflik alıştırmalarından esinlenilmiş bağımsız bir eğitim çalışmasıdır; CS50’nin resmî içeriği değildir.
