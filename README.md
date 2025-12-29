# Doküman Tabanlı Soru–Cevap Sistemi 

Bu proje, kullanıcıların PDF, TXT ve DOCX formatındaki dokümanları yükleyerek doküman hakkında soru sorabildiği, iki farklı türde cevap üreten bir doküman tabanlı soru–cevap sistemidir.

Sistem hem genel bilgiye dayalı bir cevap hem de yalnızca yüklenen dokümana dayanan, kanıtlı (grounded) bir RAG cevabı üretir. Ayrıca cevapların doğruluğunu ölçmek için nicel metrikler hesaplanır.

---

## Proje Amacı

Bu projenin amacı, büyük ve karmaşık dokümanlar üzerinde güvenilir, tutarlı ve ölçülebilir soru–cevap çıktıları üretebilen bir Retrieval-Augmented Generation (RAG) sistemi geliştirmektir.

Sistem, klasik dil modellerinde sıkça görülen:
- Doküman dışı bilgi üretimi (hallucination),
- Kaynak belirsizliği,
- Cümle ortasından başlayan veya kesilen cevaplar,
- Uzun PDF dosyalarında bağlam kaybı

gibi problemleri çözmeyi hedeflemektedir.

---

## Problem Tanımı

Standart büyük dil modelleri, kullanıcı tarafından sağlanan dokümanları doğrudan ve güvenilir biçimde referans almaz. Bu durum, özellikle akademik veya teknik içeriklerde doğruluk ve izlenebilirlik sorunlarına yol açar.

Bu proje, model çıktılarının yalnızca dokümandaki kanıtlara dayandırılmasını zorunlu kılan “STRICT RAG” yaklaşımını benimseyerek bu problemi çözmeyi amaçlamaktadır.

---

## Sistem Özellikleri

- PDF, TXT ve DOCX formatında doküman yükleme
- Türkçe ve İngilizce doküman desteği
- Dil uyumlu cevap üretimi:
  - İngilizce soru ve doküman için İngilizce cevap
  - Türkçe soru ve doküman için Türkçe cevap
- İki tür çıktı:
  - Dokümansız (genel) cevap
  - Dokümana dayalı STRICT RAG cevabı
- Cümle ortasından başlamayan ve cümle sonuyla biten özet, önizleme ve cevaplar
- Başlık, madde işareti veya biçim bozulması içermeyen RAG çıktıları
- Kaynak gösterimi (hangi doküman parçalarının kullanıldığı)
- Nicel performans metrikleri

---

## Kullanılan Yöntem

### Doküman İşleme
Yüklenen dokümanlardan metin çıkarılır, temizlenir ve anlamsal bütünlüğü korunacak şekilde cümle tabanlı parçalara (chunk) ayrılır.

### Embedding
Her parça, çok dilli bir embedding modeli kullanılarak vektörleştirilir. Bu işlem, sorular ile doküman parçaları arasında anlamsal benzerlik hesaplamayı mümkün kılar.

### Retrieval
FAISS kütüphanesi kullanılarak, soruya en yakın doküman parçaları cosine similarity üzerinden bulunur. Maximum Marginal Relevance (MMR) yöntemiyle tekrarlı içerik azaltılır.

### Cevap Üretimi
- Önce yalnızca doküman cümlelerinden oluşan bir taslak cevap oluşturulur.
- Ardından isteğe bağlı olarak yalnızca akıcılığı artıran bir yeniden yazım (rewrite) uygulanır.
- Yeni bilgi eklenmesine kesinlikle izin verilmez.

---

## Kullanılan Teknolojiler

- Python
- Google Colab (GPU destekli)
- Gradio (kullanıcı arayüzü)
- FAISS (vektör indeksleme)
- Sentence-Transformers (embedding)
- Hugging Face Transformers (yerel LLM)

---

## Dil Modeli ve Seçim Gerekçesi

### Büyük Dil Modeli
Qwen/Qwen2.5-1.5B-Instruct

Bu model:
- API gerektirmeden yerel çalışabilmesi,
- Instruction-tuned olması,
- Türkçe ve İngilizce metinlerde yeterli performans sunması

nedeniyle tercih edilmiştir.

### Embedding Modeli
sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2

Bu model, çok dilli yapısı ve anlamsal benzerlik görevlerindeki başarımı nedeniyle seçilmiştir.

---

## Veritabanı ve İndeksleme

Bu projede vektör araması için FAISS kullanılmıştır.

FAISS, küçük ve orta ölçekli doküman setlerinde yüksek performans sunması, kurulumu kolay olması ve harici bir servis gerektirmemesi nedeniyle tercih edilmiştir.

---

## Performans Değerlendirmesi

Sistem çıktıları aşağıdaki nicel metriklerle değerlendirilir:

- Top-1 Retrieval Skoru
- Avg-3 Retrieval Skoru
- Denklik (Cevap ile Doküman Arasındaki Semantik Uyum)
- Başarı Oranı (kompozit skor)

Başarı oranı, denklik ağırlıklı olacak şekilde hesaplanmaktadır.

---

## Genişletilebilirlik ve Donanım Etkisi

Daha güçlü GPU kaynakları kullanılması durumunda:
- Daha büyük dil modelleri çalıştırılabilir
- Yanıt süresi düşürülebilir
- Uzun dokümanlarda bağlam kapsaması artırılabilir

Ayrıca modelin alan-spesifik verilerle fine-tune edilmesi, cevap doğruluğunu daha da artırabilir. Örneğin, güçlü bir GPU ile 15 saat sürebilecek bir fine-tuning süreci 3–5 saate düşürülebilir.

---

## Sınırlılıklar

- Retrieval performansı doküman kalitesine bağlıdır
- Çok büyük dokümanlarda parça boyutu ve overlap ayarları sonucu etkileyebilir
- Model performansı donanım kapasitesine bağlıdır

---

## Proje Sahibi

Beyza Şahin  
Yapay Zekâ Mühendisliği (4. Sınıf)  
OSTİM Teknik Üniversitesi

---

## Lisans

Bu proje eğitim amaçlı geliştirilmiştir.
