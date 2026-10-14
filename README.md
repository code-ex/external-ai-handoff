# External AI Handoff - Antigravity Skill

Geliştirme yaparken karşılaşılan yüksek karmaşıklıktaki mimari kararları, zorlu algoritmaları veya derin muhakeme (reasoning) gerektiren bug'ları daha güçlü bir dış yapay zeka modeline eksiksiz bağlamla aktarmak ve gelen çözümü projeye güvenle entegre etmek için tasarlanmış bir **Antigravity Agent Skill**'idir.

## Nasıl Çalışır?

1. **Outbound Handoff (Giden Paket):** Asistana *"Bunu diğer yapay zekaya soracağım, handoff promptu hazırla"* dediğinizde; projenin teknoloji yığınını, ilgili kod parçalarını, başarısız denemeleri ve kısıtları dış modelin tek seferde anlayacağı bağımsız (self-contained) bir Markdown bloğu haline getirir.
2. **Inbound Integration (Gelen Çözüm):** Güçlü modelden aldığınız yanıtı sohbete geri yapıştırdığınızda; asistan çözümü yerel kod tabanıyla (importlar, tip imzaları, yan etkiler) karşılaştırır, uyarlar ve dosyalara uygular.

## Kurulum

### 1. Tek Bir Projeye Kurulum (Workspace)
Projenizin kök dizinindeki `.agent/skills/` klasörüne klonlayın:

```bash
mkdir -p .agent/skills
git clone [https://github.com/](https://github.com/)<KULLANICI_ADINIZ>/external-ai-handoff.git .agent/skills/external-ai-handoff
```

### 2. Tüm Projeler İçin Kurulum (Global)
Tüm Antigravity çalışma alanlarınızda aktif olması için global dizine klonlayın:

```bash
mkdir -p ~/.gemini/antigravity/skills
git clone [https://github.com/](https://github.com/)<KULLANICI_ADINIZ>/external-ai-handoff.git ~/.gemini/antigravity/skills/external-ai-handoff
```

## Kullanım Örnekleri

**Prompt oluşturmak için:**
> "Bu modüldeki eşzamanlılık (concurrency) sorununu çözemedik. Güçlü modele göndereceğim, bana bir handoff promptu hazırla."

**Cevabı entegre etmek için:**
> "Diğer modelden şu yanıtı aldım, kontrol edip projeye uygular mısın: [Yanıtı Yapıştır]"
