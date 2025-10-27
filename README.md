# SpeakerAI

SpeakerAI, toplantı kayıtlarını metne dönüştürerek konuşmacıları tanıyan, ses karakteristiklerini çıkaran ve psikometrik analizler sunan bir çözüm sunar. Sistem, mp3 formatındaki toplantı kayıtlarını işler, konuşmacılara tutarlı kimlikler atar ve konuşmacı hakkında toplanabilen tüm bilgileri raporlar. Ayrıca ses imzası niteliğindeki istatistiksel değerleri toplayarak gelecekteki toplantılarda konuşmacıların eşleştirilmesini kolaylaştırır.

## Özellikler
- Whisper tabanlı (veya isteğe bağlı hafif yedek) metne döküm.
- Enerji temelli diarizasyon ile konuşmacı tespiti.
- Ses karakteristiği çıkartımı (pitch, enerji, konuşma hızı, spektral centroid).
- Psikometrik analiz ve duygu kestirimi.
- SQLite veri tabanı sayesinde konuşmacı profilinin saklanması ve yeniden eşleştirilmesi.
- FastAPI tabanlı web arayüzü (7883 portu) ve CLI.
- Transkript ve konuşmacı raporu çıktıları.

## Kurulum
```bash
python -m venv .venv
source .venv/bin/activate
pip install -U pip
pip install -r requirements.txt
```

> **Not:** `openai-whisper`, `librosa` ve `vaderSentiment` gibi bağımlılıklar ses işleme ve psikometrik analiz için gereklidir. GPU destekli kullanımlarda `torch` kurulumu için [PyTorch](https://pytorch.org/get-started/locally/) yönergelerini izleyin.

## CLI Kullanımı
```bash
python -m speakerai.cli /path/to/meeting.mp3 --team "Ayşe Yılmaz" "John Doe"
```
Komut çalıştırıldığında transkript `data/outputs/transcripts/` klasörüne, konuşmacı raporu ise `data/outputs/reports/` klasörüne kaydedilir.

## Web Uygulaması
Sunucuyu başlatmak için:
```bash
uvicorn speakerai.web.app:app --host 0.0.0.0 --port 7883 --reload
```
Ardından tarayıcıdan `http://localhost:7883` adresine giderek mp3 dosyanızı yükleyin. Sistem, konuşma dökümü ile konuşmacı raporlarını üretir ve önceki toplantılardan öğrendiği konuşmacılarla eşleştirme önerileri sunar.

## Veri Tabanı
Konuşmacılar `data/speaker_profiles.sqlite3` içerisinde saklanır. Web arayüzü veya CLI ile yeni bir konuşmacı kaydı yapıldığında bu veri tabanı güncellenir.

## Testler
```bash
pytest
```

## Geliştirme Notları
- Transkripsiyon için Whisper modeli otomatik olarak yüklenir; eğer model bulunamazsa sistem sahte transkript segmentleri oluşturarak geliştirme/test aşamasında akışı bozmadan ilerlemenizi sağlar.
- Diarizasyon ve özellik çıkarımı, `librosa` tabanlı hafif yaklaşımlarla gerçekleştirilir. İhtiyaca göre daha ileri diarizasyon modelleri entegre edilebilir.
- Psikometrik analizde `vaderSentiment` kullanılır; yoksa anahtar kelime tabanlı heuristik devreye girer.
