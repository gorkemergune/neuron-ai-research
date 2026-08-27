# AI Özellik Önceliği ve Dünya Örnekleri

> **Amaç:** İncelenen Neuron sitelerine hangi AI özelliklerinin eklenmesinin daha anlamlı olacağını sıralamak ve dünyadaki emlak platformlarında benzer örnekleri göstermek.
> **Bağlam:** 21 site incelemesinde özellikle doğal dil arama, ilan Q&A, AI özet, kişiselleştirme, area/commute bilgisi, değerleme açıklaması ve mesai dışı lead yakalama eksik görünüyor.
> **Not:** Aşağıdaki örneklerin bir kısmı beta / kademeli rollout durumunda olabilir. Kaynak tarihleri 2024-2026 aralığındadır.

---

## Kısa Karar

Bu özelliklerin hepsi teorik olarak faydalı, ancak hepsi aynı anda eklenmemeli. İlk fazda kullanıcı akışını doğrudan iyileştiren ve mevcut veriyle çalışabilen özellikler seçilmeli:

1. **AI Natural-Language Search**
2. **Conversational Property Search**
3. **Ask This Property / AI Property Q&A**
4. **AI Area / Neighborhood Advisor**
5. **Personalized Property Recommendations**
6. **AI Property Valuation Explanation**

İkinci fazda karar destek özellikleri eklenebilir:

7. **AI Property Comparison**
8. **AI Match Score**
9. **AI Interactive Floorplan**

Son fazda daha pahalı veya daha niş özellikler düşünülmeli:

10. **Visual Semantic Search**
11. **AI Virtual Staging**
12. **AI Investment Analysis**

---

## 1. AI Natural-Language Search


**Ne yapar:** Kullanıcı klasik filtre seçmek yerine "okula yakın, 3 yatak odalı, bahçeli, 300 bin altı ev" gibi bir cümle yazar. Sistem bunu konum, fiyat, oda sayısı, özellik ve sıralama kriterlerine çevirir.

**Eklenirse neden iyi olur:**

- İncelenen sitelerde serbest cümleler çoğunlukla adres gibi yorumlanıyor.
- Filtre menüsüyle uğraşmadan daha hızlı arama yapılır.
- "Aile evi", "istasyona yakın", "bahçeli", "chain free" gibi niyetler yakalanabilir.
- Platform seviyesinde bir kez yazılıp tüm tenant'lara açılabilir.

**Dünya örnekleri:**

- **Zillow:** AI destekli doğal dil arama ile kullanıcıların filtre seçmeden cümleyle arama yapabildiğini duyurdu. 2024 güncellemesinde commute, affordability, schools ve points of interest gibi sorguları da destekledi.
- **Realtor.com:** AI-powered search özelliği, doğal dil sorgularını mevcut filtre şemasına çeviren hızlı ve deterministik bir parser yaklaşımıyla çalışıyor.
- **Rightmove:** AI Search, kullanıcıların doğal dil prompt'larıyla arama yapmasını sağlayan deneysel bir özellik olarak kademeli sunuluyor.
- **Zoopla:** Tam serbest sohbet araması yerine AI/NLP/CV ile çıkarılmış "smart tags" üzerinden daha zengin arama filtreleri sunuyor.

---

## 2. Conversational Property Search


**Ne yapar:** Natural-language search'ün sohbetli devamıdır. Kullanıcı ilk aramadan sonra "biraz daha ucuz olsun", "okula daha yakın olsun", "bahçesi büyük olsun" gibi takip mesajlarıyla aramayı daraltır.

**Eklenirse neden iyi olur:**

- Kullanıcı ev ararken tek seferlik filtre değil, adım adım karar verir.
- Büyük sonuç setlerinde "LOAD MORE" yerine akıllı daraltma sağlar.
- Mesai dışında da kullanıcıyı boşta bırakmaz.
- Kullanıcının gerçek tercihleri lead verisine dönüşür.

**Dünya örnekleri:**

- **Redfin:** Conversational Search özelliğiyle kullanıcılar Redfin üzerinde doğal, ileri-geri sohbetle arama kriterlerini rafine edebiliyor.
- **Zillow AI Mode:** Kullanıcıların konuşarak ev keşfetmesini, karşılaştırma yapmasını ve tur planlama gibi aksiyonlara ilerlemesini hedefliyor.
- **Realtor.com RealAssist AI:** Kullanıcıyla konuşarak bütçe, commute, must-have ve tercihleri anlayan AI-first home search deneyimi olarak duyuruldu.
- **Rightmove Ask Rightmove:** AI search chat içinde aramayı daraltma/genişletme önerileri sunuyor.

---

## 3. Ask This Property / AI Property Q&A


**Ne yapar:** Kullanıcı bir ilan sayfasında "bu ev aile için uygun mu?", "driveway iki araba alır mı?", "EPC ne anlama geliyor?", "okula yakın mı?" gibi sorular sorar. AI cevapları sadece ilan verisi, floor plan, EPC, açıklama ve güvenilir area verisine dayandırır.

**Eklenirse neden iyi olur:**

- İncelenen sitelerde ilan açıklamaları çok dengesiz: bazıları kısa, bazıları uzun.
- Kullanıcı danışman beklemeden temel sorularına cevap alır.
- Form doldurma baskısını azaltır, kaliteli enquiry üretir.
- Zayıf açıklamalı ilanların dönüşümünü iyileştirir.

**Dünya örnekleri:**

- **Redfin Ask Redfin:** İlan detayları, local market, nearby schools, touring availability gibi konularda generative AI destekli cevap veriyor ve gerektiğinde kullanıcıyı lisanslı agente aktarıyor.
- **Rightmove Ask Rightmove:** Listing sayfalarına taşınan AI deneyimi, property details ve local area bilgilerini birleştirerek "en yakın supermarket nerede?" veya "driveway iki araba alır mı?" gibi soruları cevaplamayı hedefliyor.
- **Realtor.com RealAssist AI:** Belirli mülkler, neighborhood, affordability ve closing süreci hakkında doğal konuşmayla yardımcı olacak şekilde konumlanıyor.

---

## 4. AI Area / Neighborhood Advisor


**Ne yapar:** Postcode veya ilan konumundan okul, ulaşım, commute, sel riski, broadband, market, park, restoran, mahalle karakteri gibi bilgileri toplar ve sade dille yorumlar.

**Eklenirse neden iyi olur:**

- İncelenen sitelerde çoğu zaman sadece Google Maps var; okul/ulaşım/mahalle içgörüsü yok.
- Alıcılar evi değil, yaşam düzenini satın alır: okul, iş yolu, park, market çok belirleyici.
- Danışmanların elle yazdığı area/commute metinlerini standartlaştırır.
- Londra gibi yerlerde yarıçap filtresi yerine gerçek ulaşım süresi daha anlamlıdır.

**Dünya örnekleri:**

- **Zillow:** Natural language search güncellemesinde commute time, schools ve points of interest sorgularını desteklediğini duyurdu.
- **Realtor.com RealAssist AI:** Kullanıcının günlük rutinini haritalama, commute süreleri, parklar/işletmeler ve neighborhood keşfi için Google verisini kullanan bir deneyim olarak tanıtıldı.
- **Rightmove Ask Rightmove:** Google Maps local area verisiyle property bilgilerini birleştiren AI-powered assistant olarak duyuruldu.
- **Redfin:** Listing sayfalarında school, neighborhood, Walk Score/Transit Score ve nearby amenities bilgilerini sunuyor; Ask Redfin bunları soru-cevap deneyimine taşıyor.

---

## 5. Personalized Property Recommendations


**Ne yapar:** Kullanıcının arama, tıklama, kaydetme ve tercih davranışına göre "bunlar da ilgini çekebilir" veya "bu kritere daha iyi uyan ilanlar" önerir.

**Eklenirse neden iyi olur:**

- İncelenen sitelerde sidebar çoğunlukla "newest properties"; kişiselleştirilmiş değil.
- Kullanıcı birden fazla ilan gezdikçe sistem tercihlerini öğrenebilir.
- Heads-Up Alerts gibi formlar zaten kriter topluyor; bu veri önerilere bağlanabilir.
- Enquiry kalitesini artırır.

**Dünya örnekleri:**

- **Zillow:** Kişiselleştirme ve recommendation engine üzerine uzun süredir çalışıyor; AI Mode içinde kullanıcının geçmiş arama ve davranış bağlamını koruyarak daha ilgili öneriler sunmayı hedefliyor.
- **Redfin Conversational Search:** Kullanıcının takip sorularına ve tercih sinyallerine göre aramayı daraltıyor.
- **Zoopla:** AI-enabled users tarafında listing views ve leads artışı raporlayarak AI destekli kişiselleştirme ve öneri tarafında yatırım yaptığını duyurdu.

---

## 6. AI Property Valuation Explanation


**Ne yapar:** Burada önemli ayrım şu: AI'ın tek başına "kesin değer" üretmesi değil, mevcut instant valuation / AVM çıktısını açıklaması. Örneğin "bu fiyat aralığı son satışlar, m2, oda sayısı ve EPC verisine dayanıyor; mülk kondisyonu hesaba katılmadı" gibi.

**Eklenirse neden iyi olur:**

- İncelenen sitelerde ValPal / instant valuation akışları var ama çoğu rakamı ya göstermiyor ya da gerekçe sunmuyor.
- Satıcı güveni artar; form tamamlama oranı yükselebilir.
- Mortgage calculator gibi çıplak sayılar "bu bütçen için ne demek?" şeklinde açıklanabilir.
- Hukuki risk için dil "tavsiye" değil "açıklama" olmalıdır.

**Dünya örnekleri:**

- **Zillow Zestimate:** AI/ML destekli otomatik değerleme sistemi; Neural Zestimate ile güncel piyasa sinyallerini daha iyi yakalamaya çalışıyor.
- **Redfin Estimate:** MLS ve machine learning temelli market value estimate sunuyor; Redfin bunun appraisal yerine başlangıç noktası olduğunu özellikle belirtiyor.
- **Zoopla Estimate:** Hometrack AVM ile house price estimate üretir ve confidence rating / veri kaynaklarını açıklar.
- **HouseCanary CanaryAI:** Valuation, comps, rental forecasts ve market insights gibi yatırım/valuation odaklı verileri generative AI assistant üzerinden sunuyor.

---

## 7. AI Property Comparison


**Ne yapar:** Kullanıcının seçtiği 2-4 ilanı fiyat, oda, alan, ulaşım, okul, EPC, monthly cost, artı/eksi ve riskler açısından yan yana karşılaştırır.

**Eklenirse neden iyi olur:**

- Kullanıcılar ev ararken sekmeler arasında kayboluyor.
- Karar verme süresini kısaltır.
- "Bu ev daha ucuz ama commute daha uzun" gibi trade-off'ları açık eder.
- Danışmana daha net niyetli lead gider.

**Dünya örnekleri:**

- **Zillow AI Mode:** Kullanıcıların seçenekleri karşılaştırmasına ve sonraki aksiyonlara ilerlemesine yardım eden AI deneyimi olarak tanıtıldı.
- **Realtor.com RealAssist AI:** Homes ve neighborhoods side-by-side comparison özelliğini temel özellikler arasında sayıyor.
- **Redfin App in ChatGPT:** Kullanıcıların arama sonrası neighborhood, commute ve fiyat değişimleri gibi sorularla seçenekleri konuşarak değerlendirmesine izin veriyor.

---

## 8. AI Match Score


**Ne yapar:** Bir ilanın kullanıcının kriterlerine ne kadar uyduğunu skorlar. Örneğin fiyat, oda, konum, commute, must-have özellikleri, kaydedilen ilanlara benzerlik gibi sinyallerden gerçek bir skor üretir.

**Eklenirse neden iyi olur:**

- Çok ilanlı listelerde hızlı tarama sağlar.
- Kriterlere göre sıralama daha anlaşılır hale gelir.
- Mevcut "100% MATCH" gibi sahte rozetler düzeltilirse güven artar.

**Dikkat:**

- Gerçek kullanıcı kriteri yoksa skor gösterilmemeli.
- "AI match" diye pazarlanacaksa kaynakları açıklanmalı: fiyat uyumu, oda uyumu, commute, özellikler, davranış sinyali.
- Herkese %100 göstermek güveni kırar.

**Dünya örnekleri:**

- **Rightmove AI Keyword Sort:** AI modelinin property images ve text üzerinden keyword match yaparak sonuçları match rate'e göre sıraladığını açıklıyor.
- **Zillow:** Recommendation engine ve personalized ranking çalışmaları, kullanıcı profili ile candidate homes arasındaki predicted match fikrine dayanıyor.
- **Zoopla smart tags:** Skor göstermese de NLP/CV ile property attributes çıkararak daha doğru eşleşme için altyapı sağlıyor.

---

## 9. AI Interactive Floorplan


**Ne yapar:** Floor plan'ı sadece statik görsel olmaktan çıkarır. Kullanıcı "mutfak salona açık mı?", "evin akışı nasıl?", "home office için uygun oda var mı?" diye sorabilir; fotoğraflar oda planıyla eşleştirilir.

**Eklenirse neden iyi olur:**

- İncelenen sitelerde floor plan çoğunlukla açıklamasız.
- Kullanıcı evi gezmeden yerleşimi daha iyi anlar.
- Gereksiz viewing azalabilir; daha kaliteli viewing gelir.
- EPC/floor plan explanation ile birlikte güçlü çalışır.

**Dünya örnekleri:**

- **Zillow Showcase:** Interactive floor plans, room-by-room photo organization ve AI-powered Showcase listing deneyimi sunuyor.
- **Zillow Showcase / ShowingTime+:** AI'ın hero images seçmesi, fotoğrafları odalarla ilişkilendirmesi ve interactive floor plan kullanması vurgulanıyor.

---

## 10. Visual Semantic Search


**Ne yapar:** Kullanıcı görsel veya semantik özelliklerle arama yapar: "aydınlık mutfak", "modern kitchen", "period property", "kitchen island", "river view", "large windows" gibi. Sistem fotoğraf ve metinlerden özellik çıkarır.

**Eklenirse neden iyi olur:**

- Klasik filtrelerde olmayan özellikleri yakalar.
- Fotoğraf yatırımı olan ilanlarda daha iyi keşif sağlar.
- "Bahçeli" gibi basit filtrelerin ötesinde tarz/atmosfer araması yapılabilir.

**Neden ilk faz değil:**

- Fotoğraf kalitesi ve etiketleme altyapısı gerekir.
- Yanlış görsel yorumlama riski vardır.
- Natural-language search ve Q&A kadar hızlı ürün değeri üretmeyebilir.

**Dünya örnekleri:**

- **Zoopla smart tags:** NLP ile açıklamalardan, computer vision ile görsellerden conservatory, kitchen island, patio, period property gibi özellikler çıkarıyor.
- **Realtor.com:** AI-generated listing tags ve image/text enrichment ile doğal dil aramayı güçlendirdiğini açıklıyor.
- **Rightmove AI Keywords:** Listing images ve text tarayarak exposed brick, river view, underfloor heating gibi özelliklerde daha ilgili sonuçlar göstermeyi hedefliyor.
- **Realtor.com AI Dream Home:** Kullanıcı prompt'undan görsel ev üretip görsel olarak benzer gerçek ilanları arama deneyimi sundu.

---

## 11. AI Virtual Staging


**Ne yapar:** Boş veya kötü döşenmiş odaları AI ile farklı dekorasyon stillerinde sahneler; kullanıcı aynı odanın modern, coastal, Scandinavian gibi varyasyonlarını görebilir.

**Eklenirse neden iyi olur:**

- Boş evlerde alıcıların mekânı hayal etmesini kolaylaştırır.
- Satıcı/agent tarafında ilan sunumunu güçlendirir.
- Premium listing paketi olarak satılabilir.

**Neden ilk faz değil:**

- Doğrudan arama/enquiry problemini çözmez.
- Görsel doğruluk ve yanıltıcı sunum riski yönetilmelidir.
- Daha çok agent/admin tarafı değer üretir.

**Dünya örnekleri:**

- **Zillow Showcase Virtual Staging:** Kullanıcıların select room photos üzerinde AI ile farklı tasarım stilleri seçip staged görüntü oluşturmasını sağlıyor.
- **Rightmove Style with AI:** Kullanıcıların bir evin potansiyelini hayal etmesine yardım eden deneysel AI aracı olarak duyuruldu.

---

## 12. AI Investment Analysis


**Ne yapar:** Yatırımcı için ROI, rental yield, refurb cost, GDV/end value, comparable sales, risk ve local market trend analizi üretir.

**Eklenirse neden iyi olur:**

- Investor/buy-to-let kitlesi olan sitelerde karar desteği sağlar.
- Estate agent için yatırımcı lead'lerini daha iyi nitelendirebilir.
- Değerleme, mortgage ve local market verisiyle birlikte güçlüdür.

**Neden çoğu tenant için ilk faz değil:**

- Finansal tavsiye gibi algılanma riski vardır.
- Veri kalitesi, regülasyon ve sorumluluk daha ağırdır.
- Genel alıcı kitlesi için Q&A, area advisor ve search kadar temel değildir.

**Dünya örnekleri:**

- **PropertyPortal.ai:** Rightmove linki veya AI smart search ile refurb cost, ROI, yield, end value, EPC, floorplan, comparables ve IMD score içeren yatırım analizi sunuyor.
- **HouseCanary / CanaryAI:** Investors için valuation, rental forecasts, market insights ve fırsat analizi sağlayan AI real estate assistant sunuyor.



## Kaynaklar

- Zillow, AI Mode: https://www.zillow.com/news/zillow-debuts-ai-mode/
- Zillow, AI Mode teknik açıklama: https://www.zillow.com/news/how-zillows-new-ai-mode-works-throughout-the-real-estate-journey/
- Zillow, Natural Language Search: https://zillow.mediaroom.com/2023-01-26-Zillows-new-AI-powered-natural-language-search-is-a-first-in-real-estate
- Zillow, Natural Language Search commute/schools update: https://zillow.mediaroom.com/2024-09-04-Zillows-AI-powered-home-search-gets-smarter-with-new-natural-language-features
- Zillow, Virtual Staging: https://zillow.mediaroom.com/2025-09-10-Zillow-brings-AI-powered-Virtual-Staging-to-Showcase-listings
- Zillow, Showcase interactive floor plans: https://zillow.mediaroom.com/2023-06-27-The-listing-of-the-future-is-here-ShowingTime-launches-new%2C-immersive-listing-experience-on-Zillow
- Zillow, Neural Zestimate: https://www.zillow.com/news/building-the-neural-zestimate/
- Zillow, recommendations / personalization: https://www.zillow.com/news/personalized-location-preference/
- Redfin, Conversational Search: https://www.redfin.com/news/press-releases/redfin-debuts-conversational-search-to-reinvent-how-people-find-homes/
- Redfin, Ask Redfin: https://www.redfin.com/news/ask-redfin-launches-nationwide/
- Redfin, App in ChatGPT: https://www.redfin.com/news/press-releases/redfin-debuts-real-estate-app-in-chatgpt/
- Redfin Estimate: https://www.redfin.com/redfin-estimate
- Realtor.com, AI-powered search architecture: https://techblog.realtor.com/search-how-youd-say-it-building-ai-powered-search-at-realtor-com/
- Realtor.com, RealAssist AI: https://mediaroom.realtor.com/2026-06-02-Realtor-com-R-Launches-RealAssist-TM-AI-A-Completely-Reimagined-Way-to-Find-A-Home
- Realtor.com, AI Dream Home: https://www.realtor.com/research/dream-home-june-2023/
- Rightmove, AI Search and Ask Rightmove: https://faq.rightmove.co.uk/support/solutions/articles/7000100087-ai-search-and-ask-rightmove-
- Rightmove, Ask Rightmove listing rollout: https://www.rightmove.co.uk/press-centre/rightmove-extends-conversational-search-experience-to-property-listings/
- Rightmove, AI Keyword Sort: https://faq.rightmove.co.uk/support/solutions/articles/7000048756-keyword-sort
- Rightmove, AI Keywords / Style with AI: https://www.rightmove.co.uk/press-centre/rightmove-unveils-ai-tools-to-enhance-home-search-experience/
- Zoopla, AI smart tags: https://www.zoopla.co.uk/press/releases/zoopla-further-personalises-search-functionality-using-ai-driving-more/
- Zoopla, OpenAI agreement / AI-enabled users: https://www.zoopla.co.uk/press/releases/zoopla-signs-enterprise-agreement-with-openai-investing-in-ai-to-drive-higher-lead-quality-and-customer-roi/
- Zoopla Estimate: https://help.zoopla.co.uk/hc/en-gb/articles/360006701777-What-is-a-Zoopla-house-price-estimate
- PropertyPortal.ai: https://propertyportal.ai/faq
- HouseCanary CanaryAI: https://www.housecanary.com/products/canary-ai
