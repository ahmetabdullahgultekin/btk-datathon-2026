# BTK Datathon 2026 Çözüm Defteri

**Sonuç:** Private **#4 / 579**, MSE **81.6322**. Final anında public 15. sıradayken gizli test setinde 4. sıraya çıktım (+11 sıra).

**Katılımcı:** Ahmet Abdullah Gültekin (Bireysel). Marmara Üniversitesi Bilgisayar Mühendisliği son sınıf öğrencisi, TÜBİTAK BİLGEM aday araştırmacı.
**Yarışma:** Kaggle `datathon-2026`.

---

## Görev

`career_success_score` (0 ile 100 arası) regresyonu. **Metrik MSE** (düşük olması iyi).
10.000 train, 10.000 test satırı; 45 sütun ve bir de Türkçe `mentor_feedback_text` serbest-metin kolonu.
Veri **sentetik** olarak üretilmiş. Bu çözümün ayırt edici noktası da zaten üretecin mantığını çözmek oldu.

## Çözümün özeti: dört sütun

1. **Üreteci anlamak.** Sentetik veri üreteci iz bırakır. Kolon kolon ondalık-hassasiyet parmak iziyle
   rol-uyum boost mekanizmasını çözdüm (6 beceri kolonunda, role göre yaklaşık %97 deterministik). Bu da
   üreteç bilgisine dayalı özellikleri doğurdu. *(Defter §2)*
2. **Türkçe metni ciddiye almak.** `mentor_feedback_text` gizli skoru âdeta kelimelerle anlatıyor.
   Sözlükten TF-IDF OOF stack'e, oradan çok dilli transformer fine-tune'una (BERTurk, multilingual-e5) uzanan
   bir merdiven kurdum. Yarışmanın en güçlü tekil sinyali buydu. *(§4)*
3. **Tahminci çeşitliliği.** GBDT (CatBoost, LightGBM, XGBoost), metin üyeleri ve ortogonal aileler
   (TabPFN-v2, kernel-ridge, kNN). 23 üyeli iki seviyeli stacking. Her üye en iyi olduğu için değil,
   **farklı hata yaptığı (ortogonal olduğu)** için yerini hak etti; transfer etmeyen üyeleri eledim. *(§5)*
4. **Ölçümü ciddiye almak.** Test güncel yıllara kayık olduğu için forward-year CV ve test-yıl-ağırlıklı OOF
   kullandım. Winner's-curse disiplini (üç kez kanıtlandı). MSE geometrisinden kapalı-form harman
   (exact-identity QP) ve dağılım kalibrasyonu. *(§6, §7)*

## Neden private #4

Public'i ezberlemeyen temiz model, gizli private kümede sahanın **yarısı kadar** kaydı (benim kaymam +0.42,
saha ortalaması +0.74). Böylece public 15. sıradan private 4. sıraya yükseldim; public lideri ise overfit
yüzünden 6. sıraya düştü. Kalibrasyon katmanı public'i ezberlemek değildi: kalibre çözüm private'ta ham
stack'leri yaklaşık 0.15 MSE geçti, yani gizli kümeye de transfer etti. *(§9)*

## Bu repo

- **[`jury_solution.ipynb`](jury_solution.ipynb)**: çözümün uçtan uca, her iddianın doğrulanabilir kanıtıyla
  anlatısı (9 bölüm). **Çıktılar gömülü**, yani defteri çalıştırmadan baştan sona okuyabilir, tüm tablo ve
  grafikleri görebilirsiniz.
- Yarışma verisi (Kaggle `datathon-2026`) yarışmaya özeldir ve burada paylaşılmamıştır. Defter, 23 modeli
  canlı eğitmek yerine kayıtlı ara çıktılar üzerinden hazırlanmıştır ve çıktılar gömülü olduğu için doğrudan okunabilir.
- Defterde geçen `src/...`, `reports/...`, `kernels/...` yolları çözümün tam proje deposuna yapılan izlenebilirlik referanslarıdır; bu özette yer almazlar.

## Çalıştırma

```bash
pip install -r requirements.txt
jupyter notebook jury_solution.ipynb
```

> Not: Defter, gömülü çıktılarıyla doğrudan okunmak üzere hazırlanmıştır.
