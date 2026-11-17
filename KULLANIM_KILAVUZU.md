# Kripto Gözcü PRO – Kullanım Kılavuzu

Dosya: `KriptoGozcuPRO.pine` (Pine Script v5, overlay indikatör)

## 1. Kurulum
1. TradingView → alttaki **Pine Editor** → **Open → New blank indicator**.
2. İçindekileri silin, `KriptoGozcuPRO.pine` içeriğini yapıştırın → **Save** → **Add to chart**.
3. Önerilen zaman dilimleri: **15dk – 1S – 4S**. HTF bölgesi (varsayılan 4S) sadece grafik zaman dilimi 4S'ten **küçükse** çizilir.

## 2. Grafikte ne görürsünüz?

| Öğe | Anlamı |
|---|---|
| **LONG ★x/6** (yeşil, mumun altında) | Long giriş sinyali. Yıldızlı sayı = sinyal kalite skoru. Etiketin üzerine gelince Giriş/SL/TP1/TP2 ve risk % görünür. |
| **SHORT ★x/6** (kırmızı, mumun üstünde) | Short giriş sinyali. |
| **TP1 ✓** | TP1 seviyesine ulaşıldı (BE açıksa stop girişe çekildi). |
| **TP2 +3R / SL -1R / BE +0.75R / QQE +0.4R / ST ...** | Pozisyon kapandı; neden ve R cinsinden sonuç (TP1 kısmi kârı dahil). |
| Gri / kırmızı / turkuaz / yeşil çizgiler | Giriş, SL, TP1, TP2 (sadece pozisyon açıkken). Son barda fiyat etiketleri. |
| Beyaz / turuncu / mavi | SMA 34 / 5 / 11 |
| Yeşil / kırmızı ince çizgi | SuperTrend (iz süren stop olarak da kullanılabilir) |
| Kesikli yeşil/kırmızı + bant | Önceki kapanmış 4S mumunun High/Low'u, gri nokta Mid. Üst yarı = **Premium** (pahalı), alt yarı = **Discount** (ucuz). |
| R1/R2 – S1/S2 | En çok temas alan direnç/destek kümeleri ("x temas"). Fiyatın **üstündekiler direnç, altındakiler destek** (kırılan direnç desteğe döner). |
| Sağ üst panel | Trend, QQE, ADX, hacim, HTF, SuperTrend, skor, pozisyon, anlık K/Z (R), SL/TP ve geçmiş istatistik. |

## 3. Sinyal mantığı

**LONG şartları (hepsi aynı anda):**
1. Fiyat > SMA34 **ve** SMA5 > SMA11 (trend yukarı)
2. QQE histogramı son `catchBars` bar içinde eşiğin üstüne çıktı ve hâlâ üstünde (momentum)
3. Açık filtreler: ADX ≥ min, hacim ≥ ortalama × çarpan, fiyat HTF EMA'nın üstünde, SuperTrend yukarı
4. Cooldown doldu ve zaten long değilsiniz

SHORT bunun tam tersi.

**Skor (★0-6):** Trend, QQE, ADX, hacim, HTF EMA, SuperTrend'in kaçı sinyal yönünü destekliyor. Filtre kapalı olsa da sayılır.
- ★5-6: güçlü, tam pozisyon
- ★4: normal
- ★3 ve altı: zayıf; yarım pozisyon veya pas

**Çıkış öncelik sırası (her kapanmış mumda):**
1. **SL** (aynı mumda hem SL hem TP görüldüyse muhafazakâr olarak SL sayılır)
2. **TP1** → kısmi kâr; `BE` açıksa stop girişe çekilir
3. **TP2** → pozisyonun kalanı kapanır
4. **ST** – SuperTrend dönüşü (açıksa)
5. **QQE** – momentum dönüşü: *RSI 50 (klasik)* veya *QQE Hızlı Çizgi (erken)*
6. **TERS** – karşı yön sinyali geldi, pozisyon çevrildi (açıksa)

## 4. Ayarlar – önerilen profiller

| Profil | Değişiklik |
|---|---|
| **Dengeli (varsayılan)** | ADX filtresi açık (18), ATR stop 1.5, TP1 1.5R / TP2 3R, BE açık |
| **Az ama kaliteli sinyal** | + HTF EMA filtresi açık, + Hacim filtresi (1.2x), ADX 22 |
| **Trend sürücü** | QQE çıkışı kapalı, SuperTrend çıkışı açık, TP2 5R |
| **Scalp (5-15dk)** | Swing stop, TP1 1R / TP2 2R, QQE çıkış tipi "erken", cooldown 2 |
| **Eski davranış** | ADX filtresi kapalı, TP/SL takibi kapalı, QQE çıkışı açık |

Tüm çıkışları kapatıp "Ters sinyalde çevir"i açık bırakırsanız sistem her zaman pozisyonda olan dur-ve-çevir sistemine dönüşür. Hepsini kapatırsanız pozisyon hiç kapanmaz.

## 5. Alarm kurulumu
- **Basit:** Alarm oluştur → Koşul: *KG PRO* → `LONG Giriş`, `SHORT Giriş`, `Herhangi Giriş`, `TP1 Gerçekleşti` veya `Pozisyon Kapandı`.
- **Detaylı (önerilen):** Koşul: *KG PRO* → **"Any alert() function call"**. Mesajda Giriş/SL/TP1/TP2, skor, çıkış nedeni ve R sonucu hazır gelir (Telegram/webhook botları için ideal).
- "Sadece mum kapanışında sinyal" açıkken alarmlar mum kapanışında bir kez gelir ve **repaint olmaz**.

## 6. Pratik kullanım akışı
1. **Önce bağlam:** Panelde HTF EMA ve 4S bölgesine bakın. Long için fiyatın Discount bölgesinde veya bir **S1/S2** desteğine yakın olması, short için Premium/R1-R2 yakını idealdir.
2. **Sinyal gelince:** Skor ≥4 mü? TP1 yolunda yakın bir R/S seviyesi var mı? Varsa TP1'i o seviyenin hemen önüne çekmeyi düşünün.
3. **Emir:** Etiketteki SL'yi borsaya mutlaka girin. Risk/işlem: sermayenin %0.5-%1'i. Pozisyon büyüklüğü = (Sermaye × Risk%) / (Giriş − SL).
4. **Yönetim:** TP1'de %50 kapatın, stopu girişe çekin, kalanı TP2 veya çıkış sinyaline bırakın.
5. **Sinyal yoksa işlem yok.** ADX "yatay" yazıyorsa sinyal çıkmaması normaldir.

## 7. Önemli notlar
- Paneldeki "Geçmiş (yaklaşık)" istatistiği kapanış fiyatlarıyla yapılan basit bir simülasyondur; komisyon/kayma içermez. Gerçek backtest yerine geçmez, sadece ayarları kıyaslamak içindir.
- "Sadece mum kapanışında sinyal" kapatılırsa sinyaller mum içinde görünüp kaybolabilir (repaint).
- Düşük fiyatlı coinlerde (SHIB, PEPE vb.) fiyatlar sembolün tick hassasiyetiyle yazılır.
- Bu indikatör yatırım tavsiyesi değildir. Her zaman stop kullanın.
