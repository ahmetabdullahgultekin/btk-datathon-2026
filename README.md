# BTK Datathon 2026 — Çözüm

**Sonuç:** Private **#4 / 579** · MSE **81.6322** · final-snapshot public #15 → private #4 (+11 sıra).
**Katılımcı:** Ahmet Abdullah Gültekin (Bireysel) · **Yarışma:** Kaggle `datathon-2026`

---

## Görev

`career_success_score` (0–100) regresyonu. **Metrik: MSE** (düşük = iyi).
10.000 train / 10.000 test satırı, 45 sütun + Türkçe `mentor_feedback_text` serbest-metin kolonu.
Veri **sentetik** olarak üretilmiştir — bu çözümün ayırt edici noktası, üretecin mantığını çözmektir.

## Çözümün özeti — dört sütun

1. **Üreteci anlamak.** Sentetik veri üreteci iz bırakır. Kolon-kolon ondalık-hassasiyet parmak iziyle
   rol-uyum boost mekanizması çözüldü (6 beceri kolonunda, `target_role`'a ~%97 deterministik) → üreteç
   bilgisine dayalı özellikler. *(Defter §2)*
2. **Türkçe metni ciddiye almak.** `mentor_feedback_text` gizli skoru sözelleştiriyor. Leksikon →
   TF-IDF OOF stack → çok-dilli transformer fine-tune (BERTurk / multilingual-e5) merdiveni —
   yarışmanın en güçlü tekil sinyali. *(§4)*
3. **Tahminci çeşitliliği.** GBDT (CatBoost / LightGBM / XGBoost) + metin üyeleri + ortogonal aileler
   (TabPFN-v2, kernel-ridge, kNN). 23 üyeli iki-seviyeli stacking. Her üye en iyi olduğu için değil,
   **farklı hata yaptığı (ortogonal olduğu)** için yerini hak etti; transfer etmeyen üyeler elendi. *(§5)*
4. **Ölçümü ciddiye almak.** Test güncel-yıl ağırlıklı → forward-year CV + test-yıl-ağırlıklı OOF.
   Winner's-curse disiplini (3 kez kanıtlandı). MSE geometrisinden kapalı-form harman (exact-identity QP)
   + dağılım kalibrasyonu. *(§6, §7)*

## Neden private #4

Public'i ezberlemeyen temiz model, gizli private kümede sahanın **yarısı kadar** kaydı
(bizim kayma +0.42, saha ortalaması +0.74) → public #15'ten private #4'e yükseldi.
Public lideri overfit yüzünden #6'ya düştü. Kalibrasyon katmanı public-ezberi değildi: kalibre çözüm
private'ta ham stack'leri ~0.15 MSE geçti — yani gizli kümeye de transfer etti. *(§9)*

## Bu repo

- **[`jury_solution.ipynb`](jury_solution.ipynb)** — çözümün uçtan uca, her iddianın doğrulanabilir kanıtıyla
  anlatısı (9 bölüm). **Çıktılar gömülü:** defteri çalıştırmadan baştan sona okuyabilir, tüm tablo ve
  grafikleri görebilirsiniz.
- Yarışma verisi (Kaggle `datathon-2026`) yarışmaya özeldir ve burada paylaşılmamıştır. Defter, 23 modeli
  canlı eğitmek yerine kayıtlı ara çıktılar (üye OOF/test tahminleri) üzerinden anlatıyı yeniden üretir.

## Çalıştırma

```bash
pip install -r requirements.txt
jupyter notebook jury_solution.ipynb
```

> Not: Defter, gömülü çıktılarıyla doğrudan okunmak üzere hazırlanmıştır.
