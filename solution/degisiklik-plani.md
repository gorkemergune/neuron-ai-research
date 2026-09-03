# Analizler Sonrası Değişmesi Gerekenler

> **Kaynak:** 21 sitenin (`sites/01–21`) 4 turlu inceleme notları + `eksikler-ve-cozumler.md` + `ai-ozellik-ve-dunya-ornekleri.md`
> **Amaç:** Bulunan sorunları ve her birinin doğrudan çözümünü tek dokümanda toplamak
> **Tarih:** Eylül 2026

---

## Tek cümle

21 site bugün **veri topluyor ama işlemiyor, rakam üretiyor ama açıklamıyor, formu gösteriyor ama randevuya çevirmiyor** — aşağıdaki her madde bu üç boşluktan birini kapatıyor.

Ve en önemlisi: bunlar 21 ayrı sitenin 21 ayrı sorunu değil. **Hepsi aynı platformun aynı varsayılanı.** Bir kez düzeltilir, 21 kez açılır.

---

## Öncelik özeti

| # | Değişiklik | Kanıt gücü | AI gerekli mi? | Süre | Faz |
|---|---|---|---|---|---|
| 3 | Randevu / değerleme butonları | **Çok güçlü** (kırık form + 9 site) | Hayır | 1-3 hafta | **Faz 1** |
| A | Sahte "100% MATCH" rozeti | **Çok güçlü** (6 site, sayısal) | Hayır | 1 hafta | **Faz 1** |
| C | Hukuki/coğrafi hata | **Çok güçlü** (SDLT/LBTT) | Hayır | 1 hafta | **Faz 1** |
| 6 | Varsayılanlar + tema hataları | Güçlü | Hayır | 1-2 hafta | **Faz 1** |
| 1 | Zillow tarzı filtreleme | **Çok güçlü** (21/21) | Kısmen | 3-6 hafta | **Faz 2** |
| 2 | Haritada konum + bölge verisi | Güçlü | Hayır (özetleme hariç) | 2-8 hafta | **Faz 2** |
| B | Değerleme açıklaması | **Çok güçlü** (8 site) | Evet | 4-6 hafta | **Faz 3** |
| 5 | Chatbot (RAG) | **Çok güçlü** (21/21) | Evet | 6-10 hafta | **Faz 3** |
| D | Toplanan verinin kullanılması | Güçlü (21/21 form) | Evet | 8-12 hafta | **Faz 4** |
| 4 | Marka metni / About us | Orta (aşağıda not) | Evet | 4-6 hafta | **Faz 4** |

**Neden bu sıra:** Faz 1'in tamamı AI değil — bug ve config işi. Sahte rozet dururken üstüne AI eklemek, güven kırıldığında AI'ı da beraberinde götürür.

---

# BÖLÜM 1 — Ekibin belirlediği 6 madde

## 1 · Zillow tarzı filtreleme gelmeli

**Bugün ne var:** 21 sitenin tamamında aynı filtre seti — fiyat aralığı + min/max yatak odası + konum yarıçapı (0–40 mil). Bitti.

**Ne yok:**
- Mülk tipi filtresi yok (01, 02, 03, 04, 05, 07 — hepsi)
- Anahtar kelime yok, serbest metin yok
- Harita tabanlı arama yok (Grup A'nın tamamı "sadece liste görünümü")
- Yazım toleransı yok: `"Paultn"` → **"Paul Tann Ltd"** (18 Allen, 16 Beercocks — Paulton kasabası yerine firma adı). `"Sheilds"` → **"SHEilds Ltd"** (19 Browns — South Shields yerine eğitim firması)
- Doğal dil bloke: `"3 bed with garage under £400k in Rainham"` (08), `"2 bed flat near Gilberd School under £200k"` (09), `"4 bed eco home near Castle Cary"` (10) → hepsi **"Couldn't find the address"**

**Kötü varsayılanlar da sorunun parçası:**
- 11 Cope & Co ve 21 Courtyard: varsayılan yarıçap **0.25 mil** → kasaba araması neredeyse boş sayfa, uyarı yok
- 12 Price: varsayılan sıralama **"Highest Price"** → ilk kez ev alan kullanıcı en pahalı evle karşılaşıyor
- 11 Cope & Co: SSTC / Let Agreed ilanlar varsayılan olarak sonuçlara dahil

**Çözüm — iki katman:**

**Katman 1 (AI değil, hafta 1-3):** Klasik filtre setini genişlet.
- Mülk tipi, tenure, mülk stili, anahtar kelime, harita alanı, ulaşım süresi
- **Önemli bulgu:** 14 Nicholsons'ta şema **zaten telde** — `property_types`, `property_styles`, `tenure_types`. Yani backend hazır, arayüzde açık değil. Bu bir geliştirme değil, bir config işi.
- Sensible defaults: yarıçap 3 mil, sıralama "Most Relevant", SSTC hariç

**Katman 2 (AI, hafta 3-6):** Üstüne doğal dil katmanı.
- **Ne:** LLM tool-calling + structured JSON output
- **Nasıl:** Küçük hızlı model (Haiku / Gemini Flash sınıfı). `"okula yakın 3 yatak £300k altı"` → `{beds: 3, max_price: 300000, tags: ["school"]}` → mevcut filtre API'sine geçir
- **Neden güvenli:** Model bilgi üretmiyor, sadece cümleyi API parametresine çeviriyor. Halüsinasyon riski minimum, JSON çıktısı regresyon testiyle ölçülebilir
- **Fuzzy katmanı:** Elasticsearch/Typesense fuzzy matching + öncelik sırası (yer > firma > kişi) + "şunu mu demek istediniz?" önerisi

**Dünya örneği:** Zillow 2023'te doğal dil aramasını açtı, 2024'te genişletti. Rightmove **AI Keyword Sort** ilan görselleri ve metnindeki eşleşmeleri sıralamaya katıyor. Zoopla **smart tags** ile klasik filtrelerde olmayan özellikleri aramaya ekliyor.

---

## 2 · Haritada konum gösterme

**Bugün ne var:** Karışık ve tutarsız.
- Arama sonuçlarında harita **hiçbir sitede yok** — sadece liste görünümü
- İlan sayfasında harita bazı sitelerde var (02, 03, 06), ama gösterilen noktalar **Google'ın kendi POI verisi** — okul, ulaşım, suç, demografi yorumu yok
- 06 Property Connections: iletişim haritası **600 km yanlış** — Bathgate (İskoçya) yerine Folkestone/Kent gösteriyor

**Bölge bilgisi elle yazılıyor:**
- 10 Roderick Thomas: her ilana tek tek *"Glastonbury 8 miles, Paddington 1.5 hours, A303 3.5 miles (approximate)"*
- 13 Keys and Lee: *"0.8 miles to Clockhouse primary school"* elle
- 18 Allen: Bristol/Bath ulaşım süreleri ilan metnine gömülü
- 04 Lloyds (Londra): yarıçap filtresi kullanıyor — ama Londralı alıcı "Central line'a yürüme mesafesi" diye düşünüyor, "2 mil yarıçap" diye değil

**Çözüm:**

**a) Arama sonuçlarına harita görünümü (hafta 2-4)** — liste ↔ harita geçişi, haritada alan çizerek arama.

**b) İlan sayfasına otomatik bölge katmanı (hafta 4-8):** Postcode → API katmanı:

| Veri | Kaynak |
|---|---|
| Ulaşım süresi | Google Maps / TfL API |
| Okul | Ofsted API (İngiltere) + Education Scotland |
| Sel riski | Environment Agency flood risk API |
| Broadband | Ofcom |
| Çevre (market, park, restoran) | Google Places |

LLM burada **veri üretmiyor, sadece özetliyor**: *"Bu mülk Liverpool Street'e yaklaşık 35 dakika. Yakın çevrede üç ilkokul ve 10 dakika yürüme mesafesinde iki market var."*

**c) Build sırasında geocode doğrulaması** — Bathgate koordinatı Kent'te olamaz. Bu tek satırlık bir kontrol, 06'daki hatayı bir daha yaşatmaz.

**d) Londra tenant'ları için ulaşım süresi filtresi** (yarıçap yerine) — TfL API ile.

---

## 3 · "Book a valuation / Randevu al" butonları çalışmıyor

**Bu maddeyi listenin başına almanı öneriyorum. Kanıtı en net, maliyeti en düşük, etkisi doğrudan gelir.**

### 3a · Gerçekten kırık form — 09 Harris + Wood

Canlı test edildi (`CO4 6AZ` → 13 Cordelia Drive). Değerleme akışı son adımda **tamamen kırık:**

```
Uncaught (in promise) SyntaxError: Failed to execute 'querySelector'
on 'Document': '#2' is not a valid selector
(DGx-v1bZ.js:4:13837, scrollBehavior)
```

URL hash'i (`#2`) CSS selector olarak kullanılıyor. CSS'te ID rakamla başlayamayacağı için hata düşüyor ve **gönderim zinciri kesiliyor.** Geçerli UK cep numaraları defalarca denendi, hepsinde *"Please enter a valid phone number"* hatası alındı.

Sonuç: *"Takes about 60 seconds. No obligation."* diyen araç, sonucu göstermeden önce ad/soyad/e-posta/telefon zorunlu tutuyor — **ve o formu gönderemiyor.** Değerleme sayfasının tek amacı lead toplamak; lead formu çalışmıyor. Çalışmadaki **en yüksek maliyetli tek hata**.

### 3b · Çalışıyor ama randevu değil — 8 site

Şu 8 sitede randevu modalı açıkça yazıyor: ***"This does not confirm the viewing"***
`02, 03, 04, 08, 09, 10, 13, 16`

Yani kullanıcı tarih ve saat seçiyor, buton basıyor, **hiçbir şey rezerve olmuyor.** Bu bir randevu sistemi değil, süslenmiş bir talep formu. 08 Jacksons'ta metin daha da açık: *"We'll confirm availability for you. (This does not confirm the viewing)"*

**Kritik ayrım — değerleme randevusu ≠ görüntüleme randevusu:**

| Site | Değerleme randevusu | Görüntüleme randevusu |
|---|---|---|
| 03 Harrisons | ✅ **Gerçek** (Cal.com, danışman bazlı, 1 saatlik slot, gerçek müsaitlik) | ❌ *"does not confirm"* |
| 14 Nicholsons | ✅ **Gerçek** (Cal.com takvimi) | ❌ AI/otomatik değil |
| Diğer 19 site | ❌ Form | ❌ Form |

Yani **çalışan altyapı tek bir akışta var (değerleme), diğerine hiç bağlanmamış.** 03'te aynı Cal.com entegrasyonu görüntüleme randevusuna da bağlanabilirdi — bağlanmamış. Bu bir geliştirme değil, bir bağlantı işi.

**05 Property Cloud** ayrı bir varyant: *"does not confirm"* yazısı bile yok — "Book a viewing" ve değerleme formları **hiç müsaitlik göstermiyor**, doğrudan talep gönderiyor.

**Diğer varyantlar:**
- **12 Price:** gün/saat **serbest metinle yazılıyor** — takvim yok, müsaitlik yok
- **16 Beercocks:** müsaitlik hiç göstermiyor, form-only
- **17 Grey & Co:** takvim var, teyitli müsaitlik yok
- **11 Cope & Co:** takvim gerçek müsaitliği göstermiyor + iletişim formu 7+ alan istiyor
- **20 By Design:** `InstantValuation` bileşeni kaynakta yüklü, **kapatılmış**
- **15 No.86:** Virtual Valuation fotoğraf + tadilat + satış noktası topluyor → sonra **bir insan 24 saat içinde elle rapor yazıyor**

### 3c · Çalışan iki örnek — kopyalanacak şablon bu

**Sadece 03 Harrisons ve 14 Nicholsons'ta gerçek rezervasyon çalışıyor — ve sadece değerleme randevusunda.** Cal.com entegrasyonu, canlı doğrulandı: *"Sales market appraisal - Chris," 25 Ağustos 2026, 15:00–16:00, 1 saat, yüz yüze, danışman bazlı gerçek müsaitlik.*

**19 Browns'ta** 24/7 self-service booking var (rule-based) — altyapı olarak en ileri nokta, sadece önceliklendirme yok.

**Çözüm:**
1. **09'daki JS hatasını acil düzelt** — bu bir bug değil, doğrudan gelir kaybı. Süre: gün.
2. **03/14'teki çalışan Cal.com entegrasyonunu platform şablonu yap** → 21 tenant'a aç. AI gerektirmiyor. Süre: 1-3 hafta.
3. Onay maili + takvim daveti + danışman bildirimi.
4. **Sonra** tool-calling ile AI asistanın çağırabileceği fonksiyon haline getir: `book_viewing(property_id, slot)` → chatbot sadece konuşmasın, gerçekten rezervasyon yapsın.

---

## 4 · About us kısımları ilgi çekici değil

**Dürüst not:** Site analizlerinde "about us kötü" diye bir bulgu yok — tersine marka ve güven tarafı çoğu sitede **"İyi"** olarak değerlendirilmiş: 02 Town and City'de maskot + gerçek ekip (Nigel) + Google yorumları + TPO/Money Shield rozetleri; 08 Jacksons'ta 15+ yorum + isimli ekip + Propertymark/TPO; 10 Roderick Thomas'ta isimli ekip (Bridget/Lizzie/Harry) + şirket kaydı.

**Ama bulunan gerçek sorun şu — ve bu daha savunulabilir bir argüman:** metinler şablon.

- **08 Jacksons** ana sayfasındaki *"A Different Kind of Estate Agency"* ifadesi, **06 Property Connections ile birebir aynı şablon metni.** İki farklı acente, iki farklı şehir, tek cümle. "Farklı bir emlak acentesi" diyen cümlenin kendisi kopya.
- **19 Browns:** ana sayfada *"3D Digital Twin / Matterport"* iddiası var, **ilan sayfalarında hiç görünmüyor** — doğrulanmamış iddia.
- **03 Harrisons:** ana sayfada güçlü istatistikler var, **ilan sayfalarına hiç yansımıyor.**
- **01 Kinetic + 07 Keystone:** footer iki kez render ediliyor, ikinci blokta *"Company Number: NaN"*.

**Çözüm:**
- **Ne:** Tenant başına farklılaştırılmış marka metni — LLM + **zorunlu danışman onay paneli**
- **Nasıl:** Tek girdi (acentenin gerçek verileri: kuruluş yılı, şube, satış sayısı, ekip) → çıktı: about metni + ilan sayfası güven bloğu + sosyal bio. Aynı altyapı içerik stüdyosuyla ortak (madde G14).
- **Yayın öncesi iddia denetimi:** "Matterport" diyorsan gerçekten var mı? Kural motoru kontrol etsin.
- **Ana sayfa istatistiklerini ilan sayfasına taşı** — güven ilanı görürken lazım, hakkımızda sayfasında değil.

**Toplantı tavsiyesi:** Bunu "about us güzelleştirelim" diye sunma — kanıtın yok, karşı taraf "bizimki iyi zaten" der. **"Şablon aynılığı ve doğrulanmamış iddia"** diye sun — iki somut kanıtın var.

---

## 5 · Chatbot — NLP

**Bugün ne var: 21/21 sitede çalışan chatbot yok.** Mesai dışı hiçbir yanıt yok.

**Ama bileşen zaten platformda:**
- `whatsapp-chatbot` / `msgboxx` bundle **kaynakta yüklü, DOM'da yok**: 01, 04, 07, 08, 11, 12, 17, 18
- **20 By Design:** Chatbot + InstantValuation + StampDutyCalculator **üçü birden pasif** — platform tezinin en net kanıtı
- **07 Keystone:** *"24/7 buradayız"* vaat ediyor, çalışma saatleri 9-17 ve Pazar kapalı

**Çözüm — RAG, fine-tune değil:**

**Neden fine-tune değil:** İlan verisi sürekli değişiyor. Model ağırlığına gömersen bayatlar, üstelik kaynak gösteremezsin. Yanlış bilgi = **misdescription** = hukuki risk.

**Grounding kuralı (mimari zorunluluk, tercih değil):**
> Cevap **sadece** o ilanın kendi alanlarından ve tenant'ın kendi bölge rehberinden üretilir. Kullanılan alan gösterilir. Kaynak yoksa cevap: *"Bu bilgi ilanda yok, danışmana bağlayayım."*

**Kullanılmayan zengin kaynaklar (chatbot'un besleneceği yer):**
- 20 By Design: **Seller's Story + Location prose** — çalışmadaki en iyi kaynak metin
- 08 Jacksons: **Advice Hub** — rehberler, SSS, bölge içerikleri
- 17 Grey & Co ve 21 Courtyard: bölge rehberleri
- 05 Property Cloud: Giraffe360 + drone medyası

**Sadece konuşmasın, iş yapsın — tool-calling:**
```
search_properties(filters)   → arama yapar
book_viewing(id, slot)       → gerçek Cal.com rezervasyonu
get_area_data(postcode)      → okul/ulaşım/sel verisi
handoff_to_branch(postcode)  → doğru şubeye yönlendirir
```

**Human-only markalar için (01 Kinetic, 20 By Design bilinçli tercih):** Chatbot'u "danışman yerine geçen" olarak değil, **"randevu öncesi brifing"** olarak konumlandır — AI ilanı özetler, soruları toplar, danışman görüşmeyi yapar.

**Kritik uyarı:** Boş açma. 20 By Design'da chatbot'un dormant kalmasının sebebi muhtemelen arkasında bir şey olmaması. Arkası hazır olmadan feature flag açmak, olmamasından kötüdür.

**Dünya örneği:** **Ask Rightmove** (ilan seviyesinde soru-cevap, Google Maps bölge verisiyle birleştirilmiş), **Ask Redfin** (ilan + bölge + okul), **Realtor.com RealAssist AI**.

---

## 6 · Site tasarımları daha "fresh" olmalı

**Dürüst not:** Analizlerde çoğu sitenin "Genel UX" notu **"İyi"**. Zayıf bulunan şey görsel tasarım değil, **keşif deneyimi ve bilgi mimarisi.** Toplantıda "tasarım eski" dersen kanıtın zayıf kalır; aşağıdakileri dersen kalmaz.

**Somut bulgular:**

| Bulgu | Site |
|---|---|
| Footer iki kez render + `Company Number: NaN` | 01, 07 (tema build hatası, platform geneli) |
| JS SyntaxError → form kırık | 09 |
| **Floor Plans sekmesi tamamen boş** | 05 |
| "Key Facts for Buyers" (Sprift raporu) "Media Links" akordeonuna gömülü, görünmüyor | 07 |
| EPC için sekme yok — Features metninde tek satır | 10 |
| Varsayılan sıralama "Highest Price" | 12 |
| Varsayılan yarıçap 0.25 mil | 11, 21 |
| İlan açıklama kalitesi 3 satır ↔ 3 paragraf arası değişiyor | 12 ve geneli |
| Mobil kullanılabilirlik **hiçbir sitede gerçek cihazda test edilmemiş** | 21/21 |

**En çarpıcı olan:** 20 By Design'da **£4.5M'lık bir ilanda bile** özet yok, Q&A yok, benzer ilan yok. Orada sorun renk paleti değil.

**Çözüm — bu sırayla:**

1. **Hafta 1-2 — Bilgi mimarisi ve varsayılanlar (bedava kazanç):**
   - Varsayılanları düzelt (yarıçap, sıralama, SSTC)
   - Gömülü içerikleri yüzeye çıkar (07'nin Sprift raporu, 10'un EPC'si)
   - Boş sekmeleri ya doldur ya kaldır (05 Floor Plans)
   - Tema build hatalarını kapat (duble footer, NaN)
2. **Hafta 2-4 — İlan sayfası yeniden düzeni:** AI özet + Top-5 highlights kartın üstünde, Q&A alanı, benzer ilanlar, bölge verisi bloğu
3. **Gerçek cihazda mobil testi** — 21 raporun hepsinde "gerçek cihazda test edilmeli" notu var, hiçbirinde yapılmamış
4. **Görsel yenileme** — ayrı proje, AI kapsamı dışında. Yukarıdaki 3 adım tamamlanmadan başlanırsa aynı sorunlar yeni renklerle tekrar eder.

---

# BÖLÜM 2 — Listede olmayan ama analizden çıkan 4 kritik madde

Bunlar senin 6 maddende yok ama kanıtı en güçlü olanlar arasında. Toplantıda bunlar konuşulmazsa en büyük fırsat kaçmış olur.

## A · Sahte "100% MATCH" rozeti

**Kanıt (sayısal, tartışmasız):**
- 02 Town and City: **44/44 ilanda %100** — anonim kullanıcıya
- 03 Harrisons: 8/8
- 04 Lloyds: 9/9 (**SSTC ilanlar dahil**)
- 05 Property Cloud: %100 + içi boş *"HAVE YOU LOGGED IN?"* butonu
- 06 Property Connections: 27/27
- **20 By Design: hiçbir kriter girilmediğinde bile %100**
- 13 Keys and Lee: aslında filtre uyumu, ama "AI" olarak pazarlanıyor

**Çözüm — iki seçenek:**
- **(a) Rozeti tamamen kaldır** — en dürüst, en hızlı
- **(b) Gerçek skorla:** kriter uyumu (filtre eşleşme %) + davranış sinyali (görüntülenen/kaydedilen ilanların embedding'i) + light rerank. Kullanıcıya skorun **neden** o olduğu gösterilir.

**Neden Faz 1:** Sahte etiket dururken üstüne AI eklersen, güven kırıldığı gün AI'ı da beraberinde götürür. Bu bir sıralama meselesi, tercih meselesi değil.

## B · Değerleme: rakam var, gerekçe yok

**Kanıt — siteler kendi araçlarına güvenmediklerini kendileri yazıyor:**
- **11 Cope & Co: ekranda açıkça "60-70% ACCURATE" yazıyor**
- 05 Property Cloud: *"Up to 60% accurate"*
- 04 Lloyds'un kendi metni: *"real accuracy comes from experience, not algorithms"*
- 10 Roderick Thomas (ValPal): *"algorithm isn't always possible. Call our experts"*
- 19 Browns: *"doesn't take your property's uniqueness into account"*
- 08 Jacksons: *"just a starting point"*
- 16 Beercocks: "recent local sales + property data" ile rakam, "neden" yok
- 21 Courtyard: 60 saniyede guide price, açıklama yok
- 02 Town and City: aralık gösteriyor (£174–301k), gerekçe yok
- 12 Price: rakamı ekranda hiç göstermiyor, **iletişim bilgisi karşılığında e-postayla** yolluyor

**Çözüm:** AVM inşa etme — **"neden"i** inşa et. Mevcut AVM/ValPal/Hometrack çıktısının altına 3 faktörlü açıklama:

> *"Bu tahmin şunlara dayanıyor: (1) son 6 ayda aynı postcode'daki satışlar, (2) m² × oda sayısı, (3) EPC notu. Hesaba katılmayanlar: mülkün durumu, tadilat, manzara. Kesin değer için yerinde inceleme gerekir."*

**Dil kuralı:** Tavsiye değil, açıklama. *"Şu fiyata satın"* **yasak.**

**Bonus:** 15 No.86 zaten fotoğraf topluyor → vision modele ver → 24 saat beklemeden anlık ön tahmin, insan onaylar.

## C · Yanlış hukuki ve coğrafi bağlam

- **06 Property Connections: İskoçya'da (Livingston/Bathgate, SC şirketi) İngiliz SDLT hesaplayıcı kullanıyor.** İskoçya'da LBTT uygulanır. Bu **hukuki risk**, UX sorunu değil.
- 06: iletişim haritası 600 km yanlış (Bathgate → Kent)
- 06: İskoç piyasasının "Offers Over" fiyatlandırması için alıcı rehberi yok

**Çözüm (AI değil, deterministik kural motoru):** Postcode → ülke → doğru vergi kuralı (SDLT / LBTT / LTT). Build sırasında geocode doğrulaması.

## D · Toplanan veri hiç kullanılmıyor

- **Heads-Up formu 21/21 tenant'ta** taşınma zamanı, avukat durumu, mortgage durumu, mevcut evin satılması gerekip gerekmediğini topluyor → **sıralamaya, önceliğe, hiçbir yere yansımıyor**
- 17 Grey & Co: `ice.js` davranış izliyor → kişiselleştirmeye dönüşmüyor
- 16–21: `IcebergTracker` davranış verisi topluyor → hiçbir yere gitmiyor
- 07 Keystone: çalışmadaki en zengin form (8 mülk tipi, durum seçicileri) → sadece talep gönderiyor

**Çözüm:** Lead scoring (kural motoru + light ML): taşınma yakın × avukat hazır × mortgage onaylı × zincir temiz = yüksek öncelik. Davranış verisi → embedding tabanlı benzer ilan + sıralama.

**GDPR korkuluğu:** Skor **yönlendirsin, kapıyı kapatmasın.** Düşük skorlu lead sıraya alınır, elenmez.

---

# BÖLÜM 3 — Yol haritası

### Faz 1 — Güven onarımı (hafta 1-3) · AI yok
Randevu/değerleme bug'ları (09 acil) · Cal.com'u 21 tenant'a aç · Sahte %100 rozetini kaldır · Dormant bileşenler · SDLT/LBTT · Varsayılanlar (yarıçap, sıralama, SSTC) · Tema hataları (NaN, duble footer) · Şube yönlendirme

> Bu fazın tamamı config ve bug işi. En hızlı görünür kazanç burada.

### Faz 2 — Arama ve keşif (hafta 3-8)
Genişletilmiş filtre seti · Doğal dil arama (tool-calling → JSON) · Fuzzy matching · Harita görünümü · Otomatik bölge/ulaşım verisi

> Platform seviyesinde bir kez yazılır, 21 sitede açılır.

### Faz 3 — Anlama ve açıklama (hafta 6-12)
İlan AI özeti + Top-5 highlights (batch üretim, ilan başına 1×, saklanır — ölçek maliyeti yok) · İlan Q&A (RAG) · Mesai dışı chatbot · Değerleme açıklama katmanı · EPC / floor plan / Home Report özeti

### Faz 4 — Dönüşüm ve otomasyon (hafta 10+)
Lead scoring · Davranış tabanlı sıralama · Danışman içerik stüdyosu · Marka metni farklılaştırma · Vision değerleme

---

# BÖLÜM 4 — Korkuluklar (pazarlık konusu değil)

1. **Misdescription en büyük hukuki risk.** Her AI cevabı ilan alanına dayanacak, alan gösterilecek, bilinmiyorsa *"bilmiyorum + danışmana yönlendir"*. Mimari zorunluluk.
2. **Yayına giden hiçbir AI metni danışman onayı olmadan çıkmasın.**
3. **Değerleme dili tavsiye değil, açıklama.** *"Şu fiyata satın"* yasak.
4. **Lead skorlama GDPR kapsamında otomatik karar sayılır.** Skor yönlendirsin, kapıyı kapatmasın.
5. **Eval harness olmadan prompt değiştirme = sessiz bozulma.** 21 siteden toplanan gerçek sorgularla 200 örneklik altın küme zorunlu.
6. **Arkası hazır olmadan feature flag açma.** Boş chatbot, chatbot olmamasından kötüdür.

---

# BÖLÜM 5 — Dünya örnekleri (kanıt olarak)

Amaç "Zillow'u kopyalayalım" değil — **"bu çözülebilir bir problem, çözenler var"** demek.

| Bizdeki sorun | Kim çözmüş | Nasıl |
|---|---|---|
| Doğal dil arama yok (21/21) | Zillow (2023, 2024 genişletme) | NL search → yapılandırılmış filtre |
| Filtrede olmayan özellikler aranamıyor | Zoopla smart tags, Rightmove AI Keywords | NLP + computer vision ile ilan metni ve görselinden etiket çıkarma |
| İlan Q&A yok (21/21) | Ask Rightmove, Ask Redfin, Realtor RealAssist | RAG — ilan verisi + bölge verisi |
| Çok turlu arama yok | Redfin Conversational Search | Oturum bazlı filtre güncelleme |
| Değerleme açıklaması yok | Zillow Zestimate, Redfin Estimate, Zoopla Estimate | Rakam + faktör açıklaması |
| Kişiselleştirme sahte | Zillow personalization | Gerçek davranış sinyali |
| Floor plan ham görsel | Zillow Showcase interactive floorplans | Görsel → oda-oda yapılandırılmış veri |

**Ölçek uyarısı — toplantıda mutlaka söyle:** Zillow'un ekibi bizim müşterimizin ekibi değil. Bu örnekleri **yön göstergesi** olarak kullan, hedef olarak değil. Faz 1'in tamamı 3 kişilik bir ekiple 2-3 haftada çıkar; asıl ikna edici olan bu.

---

## Ek dokümanlar

| Dosya | İçerik |
|---|---|
| `eksikler-ve-cozumler.md` | 15 eksiğin site site kanıtı (G1–G15) |
| `ai-ozellik-ve-dunya-ornekleri.md` | 12 AI özelliği + kaynakçalı dünya örnekleri |
| `ozellik-ozeti.md` | Tek tablo özet |
| `tool-calling-ornek-tr.json` | Doğal dil → JSON çıktı örneği |
| `gg/mimari-onerisi.md` | Ortak AI servis katmanı mimarisi |
| `sites/01–21` | Site başına 4 turlu inceleme notları |
