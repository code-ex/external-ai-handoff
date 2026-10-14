---
name: external-ai-handoff
description: Karmaşık, kritik veya derin muhakeme gerektiren yazılım ve mühendislik problemlerini daha güçlü bir dış yapay zeka modeline aktarmak için bağımsız (self-contained) prompt paketleri oluşturur ve dış modelden gelen çözümü mevcut projeye entegre eder. Kullanıcı başka bir yapay zekaya danışmak istediğinde, karmaşık bir mimari veya algoritmik engelle karşılaşıldığında, "bunu diğer yapay zekaya soracağım", "handoff promptu hazırla", "güçlü modele gönder" dediğinde veya dış modelden aldığı yanıtı projeye uygulamak için geri yapıştırdığında bu skilli kullanın.
---
# External AI Handoff

Proje geliştirme sırasında karşılaşılan yüksek karmaşıklıktaki mimari, algoritmik veya kritik hata ayıklama (debugging) problemlerini daha güçlü bir dış yapay zeka modeline (örn. derin düşünme / reasoning modellerine) eksiksiz bağlamla aktarmak ve gelen çözümü projeye güvenle uygulamak için iki aşamalı (Giden Prompt / Gelen Çözüm) bir iş akışı sağlar.

## When to Use

- Kullanıcı mevcut problemi daha güçlü bir yapay zeka modeline sormak için bir prompt istediğinde ("bunu diğer AI'a sorayım", "handoff promptu ver", "güçlü modele göndereceğim")
- Çözülmesi zor bir bug, karmaşık bir algoritma tasarımı, performans darboğazı veya kritik bir mimari karar noktasına gelindiğinde
- Kullanıcı dış bir yapay zeka modelinden aldığı yanıtı sohbete yapıştırıp mevcut projeye entegre edilmesini istediğinde

## Steps

### Aşama 1: Giden Prompt Paketi Oluşturma (Outbound Handoff)

Dış yapay zeka modelinin yerel proje dosyalarına ve geçmiş sohbet bağlamına erişimi yoktur. Bu nedenle, kullanıcının tek tıkla kopyalayıp yapıştırabileceği tek bir Markdown kod bloğu içinde tamamen bağımsız (self-contained) bir prompt hazırla.

Prompt paketini oluştururken şu adımları izle:

1. **Bağlamı Topla ve Damıt:** Problemle doğrudan ilgili dosyaları, veri yapılarını, hata kayıtlarını (stack trace) ve teknoloji yığınını (dil, framework, sürümler, veritabanı) tespit et. Gereksiz kod kalabalığını çıkar, sadece sorunun anlaşılması için zorunlu olan kritik kod parçalarını ve arayüzleri seç.
2. **Hedefi ve Kısıtları Netleştir:** Dış modelin ne üretmesi gerektiğini (örn. zaman karmaşıklığı hedefi, thread-safety gereksinimi, geriye dönük uyumluluk, kullanılmaması gereken kütüphaneler) açıkça belirt.
3. **Kopyalanabilir Prompt Bloğunu Üret:** Kullanıcıya aşağıdaki şablonu tek bir kod bloğu içinde sun:

```markdown
# Role & Objective
Sen kıdemli bir yazılım mimarı ve algoritma uzmanısın. Aşağıdaki projede karşılaştığımız kritik problemi analiz edip doğrudan uygulanabilir, üretim kalitesinde (production-ready) bir çözüm üretmeni istiyorum.

# Project Context & Tech Stack
- **Dil / Framework:** [Dil, framework ve versiyon bilgileri]
- **Mimari / Ortam:** [İlgili sistem mimarisi, veritabanı, çalışma ortamı]
- **Mevcut Durum Özeti:** [Projenin bu modülünün ne yaptığı ve şu ana kadar yapılanlar]

# Relevant Code & Data Structures
[Sorunla doğrudan ilgili dosya yolları, sınıflar, fonksiyonlar veya şema tanımları]

# The Core Problem / Task
- **Beklenen Davranış / Hedef:** [Tam olarak neyin başarılması gerektiği]
- **Mevcut Sorun / Engel:** [Neden mevcut yaklaşımın yetmediği, alınan hatalar veya darboğazlar]
- **Başarısız Denemeler:** [Daha önce denenen ve işe yaramayan yaklaşımlar - dış modelin aynı hataları tekrarlamaması için]

# Constraints & Requirements
- [Performans, bellek, güvenlik veya mimari kısıtlar]
- [Değiştirilmemesi gereken arayüzler veya API sözleşmeleri]

# Required Output Format
Yanıtını mevcut geliştirme asistanıma doğrudan geri yapıştıracağım. Lütfen yanıtını şu yapıda ver:
1. **Kök Neden / Mimari Analiz:** Kısa ve teknik değerlendirme.
2. **Çözüm Planı:** Hangi dosyada neyin neden değişeceği.
3. **Tam Kod Değişiklikleri:** Dosya bazlı net kod blokları (eksik yer bırakmadan, "// mevcut kod" şeklinde belirsiz kesmeler yapmadan).
4. **Doğrulama Adımları:** Çözümün test edilmesi için gereken uç durumlar (edge cases) ve test senaryoları.
```

4. **Beklemeye Geç:** Promptu verdikten sonra kullanıcıya bu bloğu güçlü modele yapıştırmasını ve aldığı yanıtı buraya geri göndermesini söyle. Kullanıcı yanıtı getirene kadar varsayımsal kod değişikliği yapma.

### Aşama 2: Gelen Çözümü Değerlendirme ve Uygulama (Inbound Integration)

Kullanıcı dış modelden aldığı yanıtı sohbete yapıştırdığında körü körüne uygulama. Şu adımları izle:

1. **Teknik Doğrulama (Sanity Check):** Dış modelin önerdiği çözümü mevcut kod tabanıyla karşılaştır. Dış modelin bilmediği yerel bağımlılıklar, yanlış varsayılan importlar, tip uyuşmazlıkları veya olası yan etkiler (side effects) olup olmadığını kontrol et.
2. **Uyumsuzlukları Gider:** Eğer dış modelin kodunda projeyle uyuşmayan küçük farklılıklar (değişken adları, paket yapısı vb.) varsa bunları yerel projeye uyarla ve kullanıcıya kısaca bilgi ver. Kritik bir mimari çelişki varsa uygulamadan önce kullanıcıyı uyar.
3. **Değişiklikleri Uygula:** Onaylanan veya uyarlanan kod değişikliklerini ilgili dosyalara temiz bir şekilde uygula.
4. **Test ve Doğrulama:** Dış modelin belirttiği uç durumları (edge cases) ve derleme/test adımlarını çalıştırarak çözümün çalıştığını doğrula.

## Gotchas

- **Eksik Bağlam Tuzağı:** Dış modele gönderilen promptta "yukarıdaki fonksiyonda görüldüğü gibi" veya "projemizdeki User sınıfı" gibi dış modelin göremediği referanslar kullanma. Gerekli her sınıf tanımını veya imzasını promptun içine açıkça ekle.
- **Aşırı Dosya Yüklemesi:** Tüm kod tabanını prompta dökme. Sadece sorunun çözülmesi için gerekli olan arayüzleri, modelleri ve sorunlu fonksiyonları seçerek token limitini ve modelin odak noktasını koru.
- **Başarısız Denemeleri Atlamak:** Daha önce denenen ve başarısız olan yaklaşımları prompta mutlaka yaz. Aksi takdirde dış model de ilk akla gelen ama çalışmayan o çözümü önerecektir.
- **Kör Entegrasyon:** Dış model ne kadar güçlü olursa olsun yerel dosya ağacını tam görmediği için hayali metodlar (hallucinated APIs) kullanabilir. Kodu projeye yazmadan önce yerel imzalarla eşleştiğini doğrula.
