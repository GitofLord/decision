# CLAUDE.md — Proje Çalışma Kuralları

Bu repo, ilaç sektörüne yönelik, güçlü tasarımlı, analitik ve oyunlaştırılmış web uygulamaları için kullanılır.
Yanıtları Türkçe ver; kod, değişken adları ve commit mesajları İngilizce olsun.

## Öncelikler (sırayla)
1. **Tasarım kalitesi**: Her ekran "premium ürün" hissi vermeli. Sıradan Bootstrap/şablon görünümü kabul edilmez.
2. **Analiz ve muhakeme**: Her metrik bir karara bağlanmalı (ne oldu → neden → ne yapmalı).
3. **Oyunlaştırma**: Puan, seviye, rozet, seri (streak), liderlik tablosu, ilerleme halkaları — anlamlı davranışları ödüllendir.
4. **İlaç sektörü doğruluğu**: Terminoloji (HCP, KOL, pazar payı, reçete/kutu, MAT, YTD, bölge/tüm Türkiye) doğru kullanılmalı.

## Varsayılan teknoloji yığını
- React + TypeScript + Vite
- Tailwind CSS + shadcn/ui (bileşen tabanı)
- Animasyon: Framer Motion (sayfa/geçiş/mikro etkileşim), gerekirse GSAP; 3D için react-three-fiber
- Grafikler: Recharts veya ECharts; özel görseller için D3
- Durum: Zustand; veri: TanStack Query
- Test: Vitest + Playwright (görsel kontrol için ekran görüntüsü al)

## Tasarım kuralları
- Renkleri `:root` üzerinde token olarak tanımla; açık/koyu tema zorunlu.
- Glassmorphism, yumuşak gölgeler, gradient vurgular ölçülü kullanılır; okunabilirlik önce gelir.
- Her etkileşimde geri bildirim: hover, press, loading skeleton, başarı animasyonu.
- `prefers-reduced-motion` desteklenmeli.
- Mobil genişlikte (375px) yatay kaydırma olmamalı.

## Çalışma şekli
- Büyük özelliklerde önce kısa plan yaz, sonra uygula.
- UI değişikliğinden sonra uygulamayı çalıştır ve ekran görüntüsüyle doğrula.
- Örnek veriler gerçekçi ama kurgusal olmalı; gerçek kişi/şirket verisi kullanma.
