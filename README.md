# 🌤️ Türkiye Hava Durumu Uygulaması

Vue.js ile geliştirilmiş modern ve kullanıcı dostu hava durumu uygulaması. Türkiye'deki şehirler için mevcut hava durumu, saatlik ve 5 günlük tahmin bilgilerini sunar.

## ✨ Özellikler

- 🌍 Türkiye'deki tüm şehirler için hava durumu bilgisi
- 🌡️ Mevcut sıcaklık ve hissedilen sıcaklık
- ⏰ 24 saatlik saatlik tahmin
- 📅 5 günlük detaylı tahmin
- 💨 Rüzgar hızı, nem ve basınç bilgileri
- 🎨 Modern ve responsive tasarım
- 🔍 Şehir arama özelliği
- 📱 Mobil uyumlu arayüz

## 🚀 Kurulum

1. Projeyi klonlayın:
```bash
git clone <repository-url>
cd hava-durumu
```

2. Bağımlılıkları yükleyin:
```bash
npm install
```

3. OpenWeatherMap API anahtarınızı alın:
   - [OpenWeatherMap](https://openweathermap.org/) sitesine gidin
   - Ücretsiz hesap oluşturun
   - API anahtarınızı alın

4. API anahtarınızı `src/App.vue` dosyasında güncelleyin:
```javascript
apiKey: 'YOUR_API_KEY' // Buraya API anahtarınızı yazın
```

5. Uygulamayı başlatın:
```bash
npm run dev
```

6. Tarayıcınızda `http://localhost:3000` adresini açın

## 🛠️ Teknolojiler

- **Vue.js 3** - Modern JavaScript framework
- **Vite** - Hızlı build tool
- **Axios** - HTTP client
- **OpenWeatherMap API** - Hava durumu verileri
- **CSS3** - Modern styling ve responsive design

## 📱 Kullanım

1. Uygulama açıldığında varsayılan olarak İstanbul'un hava durumu gösterilir
2. Arama kutusuna istediğiniz şehir adını yazın (örn: Ankara, İzmir, Bursa)
3. Enter tuşuna basın veya "Ara" butonuna tıklayın
4. Mevcut hava durumu, saatlik ve günlük tahminleri görüntüleyin

## 🎨 Tasarım Özellikleri

- Gradient arka plan
- Glassmorphism efektleri
- Responsive grid layout
- Modern tipografi (Inter font)
- Smooth animasyonlar
- Emoji hava durumu ikonları

## 📊 Veri Kaynakları

- **Mevcut Hava Durumu**: OpenWeatherMap Current Weather API
- **Tahmin Verileri**: OpenWeatherMap 5 Day Forecast API
- **Dil Desteği**: Türkçe
- **Birim**: Celsius (°C)

## 🔧 Geliştirme

```bash
# Geliştirme sunucusunu başlat
npm run dev

# Production build
npm run build

# Build önizleme
npm run preview
```

## 📝 Lisans

Bu proje MIT lisansı altında lisanslanmıştır.

## 🤝 Katkıda Bulunma

1. Fork yapın
2. Feature branch oluşturun (`git checkout -b feature/amazing-feature`)
3. Değişikliklerinizi commit edin (`git commit -m 'Add amazing feature'`)
4. Branch'inizi push edin (`git push origin feature/amazing-feature`)
5. Pull Request oluşturun

## 📞 İletişim

Herhangi bir sorunuz veya öneriniz için issue açabilirsiniz.

---

**Not**: Bu uygulama OpenWeatherMap API'sini kullanmaktadır. API kullanım limitleri için lütfen OpenWeatherMap'in kullanım şartlarını kontrol edin. 