# K2 Pro Version Chat 2.16 — Test Sonuçları

- Test zamanı: 2026-07-17 16:39:45 +03:00 (Europe/Istanbul)
- Uygulama: `k2-pro-version_chat-2.16.html`
- Sonuç: **Başarılı**
- Otomatik regresyon: **12/12 geçti**
- Gerçek tarayıcı konsolu: **0 hata, 0 uyarı**

## Kaynak ve çıktı bütünlüğü

| Dosya | Boyut | SHA-256 | Durum |
|---|---:|---|---|
| `k2-pro-version_chat-2.12.html` | 180.023 bayt | `2219F9A53A444326D79E24F19D2753B6FC49684633E4FFE4B5B0B511E9631A33` | Değişmedi |
| `k2-pro-version_chat-2.15.html` | 1.231.009 bayt | `7AA136F5EBB42ABB815892734D7BD22AB3A099AA12BC530215AAE67EB2671940` | Değişmedi |
| `2026  tarife.xlsx` | 16.587 bayt | `A0112C164B9BAD87077358C5817F507E6DFAFDCFA0B5AD31F410C7C9BEA96B43` | Değişmedi |
| `k2-pro-version_chat-2.16.html` | 211.475 bayt | `267F7913EB96F390745AD7E2FA61225F00FD0693673DFCC2D880432EA9A7637B` | Yeni çıktı |

2.16 tek HTML ve tek uygulama betiğinden oluşur. JavaScript sözdizimi kontrolü geçti. SheetJS/XLSX ve minify edilmiş kütüphane çıkarıldığı için dosya 2.15'e göre yaklaşık %82,8 küçüldü.

## Excel geliştirme doğrulaması

Çalışma kitabı yalnızca geliştirme aşamasında `artifact-tool` ile okundu ve görsel olarak render edildi.

- Sayfa: `Sayfa1`
- Kullanılan aralık: `A1:O45`
- Uygulamanın çalışma zamanında Excel okuma/yükleme yoktur.
- 12 dağıtım değeri Excel ile eşleşti:
  `1070.498`, `1668.345`, `1652.488`, `1374.008`, `1182.457`, `2081.065`, `2040.402`, `1710.785`, `1829.503`, `2479.368`, `2424.900`, `2037.247` TL/MWh.

## Otomatik regresyon testleri

| # | Grup | Senaryo | Sonuç |
|---:|---|---|---|
| 1 | Kaynak | Tek HTML, tek betik; kaldırılan tarife ürünleştirme kimlikleri ve fonksiyonları yok | Geçti |
| 2 | Tarifeler | 12 kanonik müşteri tipinin KDV, BTV ve dağıtım değerleri | Geçti |
| 3 | Tarifeler | Dağıtım `Hayır = 0`; `Evet = sabit müşteri tipi değeri` | Geçti |
| 4 | Ödeme planları | Sekiz hazır şablonun tamamı ve nakit hareketleri | Geçti |
| 5 | Özel ödeme | Özel %25 ödeme, açık alacak ve tamamlayıcı tahsilat | Geçti |
| 6 | Mutabakat | Eksik ödeme: açık bırak, sonraki faturaya taşı, otomatik tahsil et | Geçti |
| 7 | Mutabakat | Fazla ödeme: avansta tut, gün sonra iade, sözleşme sonunda iade | Geçti |
| 8 | Finans | Aktif enerji, teklif marjı, BTV/KDV matrahı, sözleşme gücü ve GES formülleri; 365 gün bazı | Geçti |
| 9 | Tüketim | MWh/kWh eşitliği ve 17/31 kısmi ay hesabı | Geçti |
| 10 | Takvim | İş günü düzeltmesi, EPİAŞ hafta sonu eşlemesi ve günlük nakit akışı | Geçti |
| 11 | Çıktılar | Finansal snapshot, CSV ve PDF/yazdırma raporu; tarife snapshot alanları yok | Geçti |
| 12 | Migration | 2.15 müşteri/tatil verisi ve finansal sonuç korunarak 2.16'ya taşındı; tarife/settings depoları oluşturulmadı | Geçti |

## Gerçek tarayıcı arayüz testleri

Yerel HTTP üzerinden gerçek tarayıcıda olaylar kullanıcı etkileşimiyle tetiklendi.

| Senaryo | Gözlenen sonuç | Sonuç |
|---|---|---|
| Başlık ve sabit tablo | 2.16 başlığı, 12 müşteri tipi seçeneği ve 12 referans satırı | Geçti |
| Kaldırılan arayüz | Planlanan tedarik, manuel override, gelecek politika, kapsama, import, sürüm ve satır editörü alanları yok | Geçti |
| Müşteri tipi değişimi | AG Sanayi `20 / 1 / 1829.503` → AG Mesken `10 / 5 / 2424.9`; teklif özeti ve ödeme önizlemesi değişti | Geçti |
| Dağıtım olayı | `Hayır → 0`, `Evet → 2424.9`; özet ve ödeme önizlemesi yenilendi | Geçti |
| Hazır plan olayı | “Kredi Kartı Taksitli - Taksitli Aktarım” uygulandı; satır ve `1/3 … 3/3` önizlemesi oluştu | Geçti |
| Mutabakat olayı | Pasifte ilgili kontroller kapandı ve açıklama değişti; aktifte yeniden açıldı | Geçti |
| GES olayı | `%10`, `5 gün`; alan etkinleşti, ayrı tahsilat açıklaması ve tarih örneği oluştu | Geçti |
| Kirli durum | Kayıtlı teklifte müşteri tipi değişince “Kaydedilmemiş değişiklik var” ve uyarı görünür oldu; değer geri alınınca temizlendi | Geçti |
| Konsol | Hata ve uyarı kaydı yok | Geçti |

## Korunan kritik matematik

- Aktif enerji maliyet tabanı = PTF + YEKDEM.
- Teklif marjı yalnızca aktif enerji tabanına uygulanır.
- BTV matrahı = aktif enerji satış bedeli.
- Sözleşme gücü BTV matrahına girmez; KDV matrahına girer.
- KDV matrahı = aktif enerji satış bedeli + dağıtım + sözleşme gücü + BTV.
- GES alacağı ödeme planı anaparasından ayrılır ve ayrı nakit girişi üretir.
- Finansman hesabı 365 gün bazında çalışır.

## Son karar

`k2-pro-version_chat-2.16.html` demo kapsamı için kabul testlerini geçti. Kaynak 2.12, kaynak 2.15 ve Excel dosyası değiştirilmedi.
