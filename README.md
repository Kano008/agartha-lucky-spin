# Agartha — Lucky Spin (yeni tasarım)

`agartha.pub/game` sayfasındaki slot makinesinin yeniden tasarlanmış hâli.
İki değişiklik var: **sütun başına üç sembol** ve **çevir düğmesi yerine bir kol**.

Bu depo yalnızca oyunu içerir. Sitenin geri kalanı bu depoda yok.

## Denemek için

```
prototip/index.html
```

Tarayıcıda aç, hepsi bu. Derleme, kurulum, bağımlılık yok.
(Sesler için sayfaya bir kez tıklamak gerekebilir — tarayıcı otomatik ses çalmayı engelliyor.)

Kolu aşağı sürükleyebilir, üstüne dokunabilir veya klavyeyle Enter'a basabilirsin.

## Ne değişti

### 1. Sütunda üç sembol

Her sütun artık tek bir kare değil, tamburun üzerine açılan bir pencere.
Kazanma hattının bir üstü ve bir altı görünür ama geri çekilmiş — soluk, renksiz,
küçük — böylece ortadaki sıranın sayılan sıra olduğu tartışmasız.
Hattı iki altın hairline ve kenardaki çentikler işaretliyor.

Teknik not: yayındaki oyun her makarayı şeritten **rastgele bir eleman** seçerek
dolduruyor, dolayısıyla sembolün şerit üzerinde komşusu yok. Üstünü ve altını
gösterebilmek için şerit **konum numarasıyla** okunacak şekilde çevrildi
(`pos` bir ondalık sayı, merkez `strip[round(pos)]`).

**Kazanma oranları değişmedi.** Şeritler sitenin kendi 32'lik dizileri, aynen
kopyalandı. Şerit konumundan uniform seçmek, elemandan uniform seçmekle özdeş.

Duruş zamanlaması da aynı bırakıldı: 800ms / 1200ms / 2000ms, üç logo gelirse
üçüncü makara +3000ms asılı kalıyor.

### 2. Kol

Tam genişlikteki *Çevir* düğmesi tamamen kalktı. Kol kabinin yan tarafına monte.
Dönerken kısalıyor, bırakınca merkezi bir miktar geçip yerine oturuyor.
Hak varken topuz altın rengiyle nefes alıyor, makaralar dönerken susuyor,
hak bitince griye düşüyor.

Kol hâlâ bir `<button>` — klavye ve ekran okuyucu çalışmaya devam ediyor.

### 3. Site tasarım sistemine geri dönüş

Oyun sayfası agartha.pub'ın kendi sisteminden kaymıştı. Düzeltilenler:

| | Önce | Sonra |
|---|---|---|
| Köşe yarıçapı | 12px / 8px / 6px | **4px** (sitenin her yerinde bu) |
| Çevir düğmesi | dolu altın zemin, 600 ağırlık, cümle düzeni | kol (düğme yok) |
| Altın kullanımı | dolgu | **çizgi** — sitede dolu altın yalnızca hero CTA'sında |
| Küçük etiketler | harf aralığı yok | büyük harf + `.19em` harf aralığı |
| Geçişler | `.2s` linear | `cubic-bezier(.22,1,.36,1)` |

Renkler ve yazı tipleri siteden birebir alındı: `#c9a46a` altın, `#8a5a2b` kehribar,
`#e7e1d6` metin, `#8d8579` soluk, `#0d0d0d`/`#151515` zeminler, Cormorant Garamond
+ Inter.

## Siteye entegre ederken

Değişiklik `src/routes/game/+page.svelte` içinde kalıyor. Dokunulmayan yerler:
IndexedDB ödül akışı, QR modal'ı, spin limiti (`agartha_spin_window`),
ses dosyaları ve `?s1=&s2=&s3=` / `?win=` ile zorlama sonuç mekanizması.

Ana fark, makara durumunun ne tuttuğu:

- **Önce:** her makara bir sembol id'si tutuyordu (`['cocktail','beer','pizza']`),
  dönerken 80ms'de bir rastgele başkasıyla değişiyordu.
- **Sonra:** her makara şerit üzerinde bir **konum** tutuyor (ondalık). Görünen
  üç sembol bu konumdan türüyor: `strip[i-1]`, `strip[i]`, `strip[i+1]`.
  Animasyon `requestAnimationFrame` ile konumu ilerletiyor.

Çalışan hâli `prototip/index.html` içindeki `<script>` bloğunda; `reels`, `measure`,
`paint` ve `spin` fonksiyonları doğrudan taşınabilir. CSS'te makara ve kol bölümleri
`--agartha-*` yerine yerel değişken adları kullanıyor, isim değişimi dışında aynı.

## `prototip/drinks` ve `prototip/sounds`

Prototipin tek başına çalışabilmesi için siteden kopyalandı. Sitede zaten
`static/game/drinks/` ve `static/sounds/` altında duruyorlar — entegrasyonda
bu klasörlere ihtiyaç yok.
