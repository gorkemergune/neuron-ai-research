# Eksikler ve Çözümler

> **Hazırlayan:** Yasemin
> **Kaynak:** 21 sitenin (`sites/01–21`) 4 turlu inceleme notlarının analizi

---

## BÖLÜM A — 21 SİTEDE TEKRAR EDEN GENEL EKSİKLER + ÇÖZÜMLER

### G1 · Doğal dil arama tüm sitelerde çalışmıyor
**Kanıt (21/21):** Her sitede aynı hata — "Couldn't find the address". Arama = sadece adres eşleştirme. "3 bed with garage under £400k in Rainham" (08), "2 bed flat near Gilberd School under £200k" (09), "4 bed eco home near Castle Cary" (10), "family home near schools" (12, 13), "3 bed near station £450k chain free" (05) — hepsi adres olarak yorumlanıp bloklandı.

**Çözüm:**
- **Ne:** LLM tool-calling + structured JSON output
- **Nasıl:** Küçük hızlı model (Gemini Flash / Haiku sınıfı). "3 bed under £300k near schools" → `{beds:3, max_price:300000, tags:["school"]}` JSON, mevcut Neuron filter API'sine geçir. 14 Nicholsons'ta şema zaten telde: `property_types`, `property_styles`, `tenure_types`
- **Neden bu yaklaşım:** Model bilgi üretmez, sadece cümleyi API parametresine çevirir → halüsinasyon riski minimum, test edilebilirlik yüksek (JSON output regresyon testi)

---

### G2 · Yazım/eş anlam toleransı yok
**Kanıt:**
- 18 Allen: "Paultn" → "Paul Tann Ltd" (Paulton bölgesi yerine firma adı)
- 19 Browns: "Sheilds" → "SHEilds Ltd" (South Shields yerine eğitim firması)
- 01 Kinetic: "lin" → İskoçya (bölge sınırı yanlış)
- 16 Beercocks: "Paultn" → "Paul Tann Ltd" (aynı hata)

**Çözüm:**
- **Ne:** Fuzzy string matching + intent classifier
- **Nasıl:** Öncelik sırası: yer > firma > kişi. Elasticsearch/Typesense fuzzy + LLM disambiguation ("Paultn muhtemelen Paulton mu?"). Boş sonuçta "şunu mu demek istediniz" önerisi

---

### G3 · Sahte kişiselleştirme ("100% MATCH")
**Kanıt:**
- 02 Town and City: 44/44 ilanda %100 (anonim kullanıcıya)
- 03 Harrisons: 8/8 ilanda %100
- 04 Lloyds: 9/9 ilanda %100 (SSTC dahil)
- 05 Property Cloud: %100 + boş "HAVE YOU LOGGED IN?" butonu
- 06 Property Connections: 27/27 ilanda %100
- 20 By Design: **kriter girilmediğinde bile %100**
- 13 Keys and Lee: filtre-uyumu ama "AI" olarak pazarlanıyor (etiket yanıltıcı)

**Çözüm:**
- **Ne:** İki seçenek — (a) rozeti tamamen kaldır (en dürüst), (b) gerçek skorla
- **Nasıl (b):** Kriter uyumu (filtre eşleşmesi %) + davranış (görüntülenen/kaydedilen ilanların embedding'i) + light rerank
- **Neden kritik:** Sahte etiket düzeltilmeden AI eklenirse, güven kırıldığında AI'ı da beraberinde götürür

---

### G4 · Değerleme "rakam var, gerekçe yok"
**Kanıt:**
- 16 Beercocks: "recent local sales + property data" ile ekranda rakam, "neden" yok
- 19 Browns: rakam gösteriyor + dürüstçe uyarıyor: *"doesn't take your property's uniqueness into account"*
- 21 Courtyard: 60 saniyede guide price, açıklama yok
- 02 Town and City: aralık gösteriyor (£174-301k), "neden" yok
- 04 Lloyds: kendi kopyası itiraf ediyor — *"real accuracy comes from experience, not algorithms"*
- 08 Jacksons: *"just a starting point"*
- 10 Roderick Thomas ValPal: *"algorithm isn't always possible. Call our experts"*
- 11 Cope and Co: **"60-70% ACCURATE"** açıkça yazıyor

**Çözüm:**
- **Ne:** AI açıklama katmanı (AVM inşa etme, "neden"i inşa et)
- **Nasıl:** Rakamın altına 3-faktör açıklaması: "Bu tahmin şuna dayanıyor — (1) son 6 ay bölge satışları, (2) m² × oda sayısı, (3) EPC rating. Şu hesaba katılmadı — mülkün durumu, tadilat, manzara. Kesinlik için yerinde inceleme."
- **Dil kuralı:** Tavsiye değil, açıklama. "Şu fiyata satın" YASAK

---

### G5 · Dormant bileşenler (kod var, özellik kapalı)
**Kanıt:**
- 20 By Design: **Chatbot + InstantValuation + StampDutyCalculator üçü birden pasif** (platform tezinin en net kanıtı)
- 01, 04, 07, 08, 11, 12, 17, 18: `whatsapp-chatbot` / `msgboxx` bundle kaynakta yüklü, DOM'da yok
- 11 Cope and Co: BlendedFeeCalculator + Chatbot dormant
- 01 Kinetic: `live-search-container` sınıfı pasif

**Çözüm:**
- **Ne:** AI değil, config işi — en hızlı zafer
- **Nasıl:** Feature flag mekanizması zaten var (her tenant kendi tema config'i). Aç + arkasına gerçek asistan bağla
- **Uyarı:** Boş açmak zararlıdır — arkası olmadan açma (dormant kalmasının nedeni muhtemelen bu)

---

### G6 · Veri toplanıyor, hiç kullanılmıyor
**Kanıt:**
- **Heads-Up formu 21/21 tenant'ta** taşınma zamanı, avukat durumu, mortgage durumu, satma durumu topluyor → sıralamaya yansımıyor
- 17 Grey & Co: `ice.js` davranış izliyor → kişiselleştirmeye dönüşmüyor
- 16–21 (Grup D): `IcebergTracker` davranış verisi topluyor → hiçbir yere gitmiyor
- 15 No.86: Virtual Valuation fotoğraf/tadilat/satış noktası topluyor → 24 saat sonra insan manuel rapor
- 07 Keystone: çalışmadaki en zengin form (8 mülk tipi, durum seçicileri) → sadece talep gönderiyor

**Çözüm:**
- **Ne:** Lead scoring + davranış-tabanlı ranking
- **Nasıl:**
  - Form verisi → skorla (kural + ML): taşınma yakın × avukat hazır × mortgage onaylı × chain temiz = yüksek priority
  - Davranış verisi → embedding tabanlı benzer ilan + ranking
  - 15'in fotoğraflarını vision modele (GPT-4V / Claude vision) ver → anlık ön tahmin
- **GDPR korkuluk:** Skor yönlendirsin, kapıyı kapatmasın; düşük skorlu lead sıraya alınır, elenmez

---

### G7 · AI ilan özeti / Q&A hiçbir yerde yok (21/21)
**Kanıt:**
- Tüm ilanlar elle yazılmış paragraflar, özet yok, soru sorma alanı yok
- 20 By Design'da £4.5M ilanda bile özet/Q&A/similar yok
- **Kullanılmayan zengin kaynaklar:** 20 Seller's Story + Location prose (en iyi kaynak metin), 08 Jacksons Advice Hub (rehberler, FAQ, alan içerikleri), 17 Grey & Co ve 21 Courtyard bölge rehberleri, 05 Property Cloud Giraffe360 + drone medya
- 12 Price: açıklama kalitesi tutmuyor (3 satır ↔ 3 paragraf)

**Çözüm:**
- **Ne:** İlan başına bir kez üretilip saklanan AI özet + anlık ilan Q&A + Top-5 highlights
- **Nasıl:**
  - **Özet (batch):** İlan yayınlandığında/güncellendiğinde bir kez üret, DB'ye kaydet — ölçek maliyeti yok
  - **Q&A (anlık):** RAG — cevap sadece ilanın kendi alanlarından ve tenant'ın kendi bölge rehberinden. Kaynak yoksa "bilmiyorum, danışmana yönlendir"
  - **Highlights:** Top-5 madde kartın üstünde
- **Neden RAG, fine-tune değil:** İlan verisi sürekli değişir, ağırlığa gömmek bayatlar; kaynak gösteremezsin

---

### G8 · Mesai dışı yanıt yok (21/21)
**Kanıt:** 21 sitede tek bir out-of-hours chatbot yok. 01 Kinetic ve 20 By Design bilinçli "human-only" pozisyonu ama gece 11'deki satıcıya dead-end. 07 Keystone "24/7 buradayız" vaat ediyor ama opening hours 9-17 ve Pazar kapalı.

**Çözüm:**
- **Ne:** Mesai dışı chatbot (site geneli + ilan-özel context ile)
- **Nasıl:** Aynı Q&A altyapısı + emin değilse "sabah 9'da geri arayalım" formu + Cal.com randevu
- **Human-only markalar için:** "Randevu öncesi brifing" formatında — AI ilanı özetler, danışman görüşmeyi yapar

---

### G9 · Ulaşım/area insights elle yazılıyor
**Kanıt:**
- 10 Roderick Thomas: her ilana *"Glastonbury 8 miles, Paddington 1.5 hours, A303 3.5 miles (approximate)"* elle yazılıyor
- 13 Keys and Lee: *"0.8 miles to Clockhouse primary school"* elle
- 18 Allen: Bristol/Bath ulaşım süreleri prose'ye yazılı
- 04 Lloyds (Londra): yarıçap filtresi kullanıyor — alıcı "Central line'a yürüme mesafesi" ile düşünüyor

**Çözüm:**
- **Ne:** Otomatik commute + area data
- **Nasıl:** Postcode → API katmanı:
  - Ulaşım: Google Maps / TfL API
  - Okul: Ofsted API (İngiltere) + Education Scotland
  - Sel: Environment Agency flood risk API
  - Broadband: Ofcom
  - Londra'da yarıçap yerine **ulaşım süresi filtresi** (TfL API)
- LLM sadece bu verileri **özetler**, üretmez

---

### G10 · Yayın öncesi veri kalitesi denetimi yok
**Kanıt:**
- 01 Kinetic: başlıkta 2 yatak, material info'da 3 yatak (aynı ilan)
- 03 Harrisons, 07 Keystone: başlık/detay fiyat tutarsızlığı (£150k vs £150-170k)
- 01, 07: footer "Company Number: NaN" + duble render (tema build bug'ı)
- 09 Harris and Wood: instant valuation formu kırık — URL hash CSS selector olarak kullanılıyor, `#2` geçersiz selector SyntaxError'ı
- 19 Browns: "3D Digital Twin / Matterport" iddiası homepage'de, ilan sayfalarında hiç görünmüyor

**Çözüm:**
- **Ne:** Yayın öncesi çelişki denetçisi
- **Nasıl:** İki katman —
  1. **Kural motoru:** `baslik.yatak == detay.yatak`, fiyat aralığı gerçekçi, postcode formatı, NaN/undefined kontrol, iddia edilen medya gerçekten var mı
  2. **LLM validator:** "başlık ve detay tutarlı mı?" (kural yakalamayan çelişkiler için)
- Danışmana uyarı kutusu, otomatik düzeltmez

---

### G11 · Yanlış hukuki/coğrafi bağlam
**Kanıt:**
- **06 Property Connections: İskoçya'da İngiliz SDLT hesaplayıcı** (LBTT uygulanmalı) — hukuki risk
- 06: Contact haritası **600 km yanlış** (Bathgate → Folkestone/Kent gösteriyor)
- 06: "Offers Over" fiyatlandırması için alıcı rehberi yok (İskoç piyasa boşluğu)
- 12 Price: **varsayılan sıralama "Highest Price"** — ilk kez ev alan en pahalı evle karşılaşıyor
- 11 Cope and Co, 21 Courtyard: **varsayılan yarıçap 0.25 mil** — kasabada neredeyse boş sonuç, gevşetme önerisi yok
- 11 Cope and Co: SSTC/Let Agreed varsayılan olarak sonuçlara dahil

**Çözüm:**
- **Ne:** Ülke-farkında + tenant-farkında config kuralları (AI değil, deterministik)
- **Nasıl:**
  - Postcode → ülke → doğru vergi kuralı (SDLT/LBTT/LTT)
  - Geocode doğrulaması build sırasında (Bathgate koordinatı Kent'te olamaz)
  - Sensible defaults: yarıçap 3mi, sıralama "Most Relevant", SSTC hariç

---

### G12 · Randevu genellikle sahte
**Kanıt:**
- **Sadece 03 Harrisons ve 14 Nicholsons** Cal.com ile gerçek rezervasyon çalışıyor (25.08.2026 15:00 doğrulandı)
- 10 Roderick Thomas: randevu modalı açıkça diyor: *"This does not confirm the viewing"*
- 13 Keys and Lee: tarih/saat seçme var, *"This does not confirm the viewing"* uyarısı
- 16 Beercocks: Book-a-viewing müsaitlik göstermez, form-only
- 19 Browns: 24/7 self-service booking var (rule-based, en ileride) ama scoring yok

**Çözüm:**
- **Ne:** Cal.com (veya benzeri) entegrasyonu platform seviyesinde
- **Nasıl:** 03 ve 14'te çalışan entegrasyonu template olarak al, tool-calling üzerinden AI asistanın çağırabileceği fonksiyon: `book_viewing(property_id, slot)` → gerçek rezervasyon + onay maili
- 19'un altyapısına lead scoring bağla (priority danışmana yönlendirme)

---

### G13 · Belge/EPC/Floor Plan açıklaması yok
**Kanıt:**
- Tüm sitelerde EPC görselleri ham (açıklamasız)
- **06 Property Connections: Scottish Home Report 20-40 sayfa PDF, hiç özetlenmeden indiriliyor**
- 05 Property Cloud: Floor Plans sekmesi tamamen boş
- 10 Roderick Thomas: EPC için ayrı sekme bile yok, sadece Features metninde bir satır
- 07 Keystone: "Key Facts for Buyers" Sprift raporu "Media Links" akordeonunda gömülü, görünmez

**Çözüm:**
- **Ne:** Belge-özel açıklama modelleri
- **Nasıl:**
  - **EPC:** görsel-OCR + kural motoru → "D rated, tahmini yıllık enerji £1,800, C'ye çıkmak için £3-8k yatırım"
  - **Floor plan:** vision model + m² hesabı + oda-oda özet
  - **PDF raporlar (Scottish Home Report):** LLM chunked özet + risk bayraklama (structural issues, damp, subsidence)

---

### G14 · Aynı içerik 3+ yerde elle yazılıyor
**Kanıt:**
- 05 Property Cloud: aynı ilan içeriği 3 farklı yerde (web + sosyal + portal) elle yazılıyor
- Tüm sitelerde: portal (Rightmove/Zoopla) başlığı, sosyal post, alt-text ayrı süreçler

**Çözüm:**
- **Ne:** Danışman içerik stüdyosu
- **Nasıl:** Tek girdi (fotoğraflar + temel bilgi) → LLM çıktısı: (a) site açıklaması, (b) portal başlığı, (c) sosyal post, (d) alt-text
- **Zorunlu korkuluk:** Danışman onay ekranı — yayına gitmeden önce. Onaylar zamanla fine-tune veri seti olur

---

### G15 · Çok şubeli sitede yönlendirme yok
**Kanıt:**
- **16 Beercocks: 11 şube** ayrı listeli, postcode-tabanlı yönlendirme yok
- 09 Harris and Wood: 4 departman telefon numarası, kullanıcı doğru numarayı bulmak zorunda
- 18 Allen: 3 şube, yönlendirme yok
- 04 Lloyds: çok ofisli yapı sitede hiç kullanılmıyor, sonuçlar ofise göre ayrılmıyor
- 14 Nicholsons: Contact routing statik (Buy/Sell/Rent seçimi hiçbir şey değiştirmiyor)

**Çözüm:**
- **Ne:** Postcode → şube kural motoru (AI değil)
- **Nasıl:** Şube-postcode eşleme tablosu + contact form'da otomatik ön-doldurma. Chatbot bunu bilerek doğru şubeyi handoff etsin

---

## BÖLÜM B — Çözümlerin Teknik Sözlüğü (Ne = Neyle)

| Çözüm türü | Kullanılan teknoloji | Hangi eksiği çözer |
|---|---|---|
| **NL Arama** | LLM tool-calling + structured JSON output | G1, G2 |
| **AI Özet / Highlights** | LLM (batch, ilan başına 1×, saklanır) | G7 |
| **Q&A / Chatbot** | LLM + RAG (grounding: ilan alanları + tenant rehberleri) | G7, G8, G15 |
| **Değerleme Açıklaması** | LLM + comparable data (Land Registry) | G4 |
| **Vision (fotoğraf/EPC/floor plan)** | Vision model (GPT-4V, Claude vision) | G13, G6 (15 Virtual Val) |
| **Lead Scoring** | Kural motoru + light ML | G6 |
| **Similar / Ranking** | Vector DB + embedding | G3, G6 |
| **Fuzzy Match** | Elasticsearch / Typesense + LLM disambiguation | G2 |
| **Area/Commute Data** | Google Maps, TfL, Ofsted, Environment Agency API | G9 |
| **Yayın Öncesi Denetim** | Kural motoru + LLM validator | G10 |
| **Ülke/Bölge Kuralları** | Deterministik kural motoru (AI değil) | G11 |
| **Randevu** | Cal.com tool-calling | G8, G12 |
| **Dormant açma** | Feature flag config (AI değil) | G5 |
| **İçerik üretimi** | LLM + zorunlu onay paneli | G14 |
| **Multi-branch routing** | Postcode kural motoru (AI değil) | G15 |

---

## BÖLÜM C — Öncelik Sırası

Öncelik "en az efor + en yüksek etki + minimum risk":

1. **Config/bug düzeltmeleri** (G3 rozet, G5 dormant, G10 denetim, G11 kurallar, G15 routing) — AI değil, hafta 1-2, güven onarımı
2. **NL arama + query understanding** (G1, G2) — platform-geneli, tek yazılıp 21× açılır
3. **İlan özet + Q&A** (G7) — batch üretim, ölçek maliyeti düşük
4. **Değerleme açıklaması + area data** (G4, G9) — rakam/veri zaten var, katman ekle
5. **Lead scoring + mesai dışı + randevu** (G6, G8, G12) — en fazla sorumluluk, en son
6. **Vision + içerik stüdyosu** (G13, G14) — olgunlaştıkça ekle

---

## BÖLÜM D — Kritik Korkuluklar

1. **Misdescription en büyük hukuki risk.** Her cevap ilan alanına dayanacak, alan gösterilecek, bilinmiyorsa *"bilmiyorum + danışmana yönlendir"* — mimari zorunluluk, tercih değil
2. **Yayına giden hiçbir AI metni danışman onayı olmadan çıkmasın** (G14)
3. **Değerleme dili tavsiye değil, açıklama** — *"Şu fiyata satın"* yasak (G4)
4. **Lead skorlama GDPR kapsamında otomatik karar** — skor yönlendirsin, kapıyı kapatmasın; düşük skorlu lead sıraya alınır, elenmez (G6)
5. **Eval harness olmadan prompt değişikliği = sessiz bozulma** — 200 örneklik altın küme (21 siteden gerçek sorgular) zorunlu
