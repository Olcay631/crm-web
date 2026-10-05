# CRM Web Frontend

Bu repository, ayrı bir ASP.NET Core 10 Web API kullanan CRM uygulamasının yalnızca web frontend'ini içerir. Mobil istemci ayrı bir Flutter projesidir.

## Teknolojiler

- React ve TypeScript
- Vite
- React Router
- Axios
- TanStack Query
- Zustand
- React Hook Form ve Zod
- ESLint

Her araç tek bir sorumluluk için kullanılır: backend'den gelen server state TanStack Query, gerekli global istemci state'i Zustand, formlar React Hook Form ve doğrulama Zod ile yönetilir.

## Mimari

Proje feature-based architecture kullanır. İş kabiliyetleri `features` altında kendi API, bileşen, hook, şema ve tipleriyle birlikte bulunur. Route seviyesindeki `pages` dosyaları bu feature'ları birleştirir; ortak ve feature'dan bağımsız parçalar `shared` altında tutulur.

```text
src/
├── api/                 # Merkezi Axios istemcisi ve API hata modeli
├── app/
│   ├── providers/       # Global provider'lar
│   ├── router/          # Route konfigürasyonu
│   └── styles/          # Global temel stiller
├── features/
│   ├── auth/            # Login API, form, hook, schema ve tipleri
│   └── users/           # Users API, tablo, query hook'ları, schema ve tipleri
├── pages/               # Route seviyesindeki sayfalar
├── shared/              # Ortak bileşen, sabit ve yardımcılar
├── store/               # Zustand global client state
└── main.tsx             # Uygulama giriş noktası
```

API erişim akışı örneği:

```text
UsersPage → useUsers → usersApi → apiClient → ASP.NET Core API
```

Component'ler doğrudan Axios çağrısı yapmaz. Backend DTO'ları API katmanında frontend modellerine dönüştürülür.

## Kurulum

Gereksinimler:

- Node.js 22.12 veya üzeri
- npm 10 veya üzeri

Bağımlılıkları yükleyin:

```bash
npm install
```

Development sunucusunu başlatın:

```bash
npm run dev
```

Vite'ın terminalde gösterdiği adresi tarayıcıda açın.

## Environment ayarları

Uygulamanın backend adresi `VITE_API_URL` değişkeninden okunur. Development için `.env.development` dosyası hazırdır:

```dotenv
VITE_API_URL=https://localhost:7001/api/v1
```

Farklı bir backend kullanmak için bu değeri değiştirin. Kişisel yerel ayarlar için Git'e eklenmeyen `.env.local` dosyası da kullanılabilir. `VITE_` ile başlayan tüm değerlerin browser tarafından görülebildiğini unutmayın; API secret, veritabanı parolası veya JWT imzalama anahtarı gibi hassas değerleri frontend environment dosyalarına koymayın.

## Komutlar

```bash
npm run dev        # Development sunucusu
npm run lint       # ESLint kontrolü
npm run type-check # TypeScript kontrolü
npm run build      # TypeScript kontrolü ve production build
npm run preview    # Production çıktısını yerelde önizleme
```

Production çıktısı `dist/` klasörüne oluşturulur.

## Authentication notu

Başlangıç yapısı login isteğini `/auth/login` endpoint'ine gönderir ve Axios isteklerinde cookie kullanımını destekler. Token'lar `localStorage` içine yazılmaz. Zustand store yalnızca sayfa yaşam döngüsündeki frontend oturum durumunu tutar; backend sözleşmesi kesinleştiğinde HttpOnly cookie, refresh ve oturum yenileme akışı API katmanına eklenebilir.
