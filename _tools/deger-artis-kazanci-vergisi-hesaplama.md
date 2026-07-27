---
layout: page
title: "Değer Artış Kazancı Vergisi Hesaplama"
description: "Evinizi satarken ne kadar vergi ödeyeceğinizi anında hesaplayın! 5 yıl muafiyet kuralı ve enflasyon
düzeltmesiyle değer artış kazancı verginizi öğrenin."
permalink: /araclar/deger-artis-kazanci-vergisi-hesaplama.html
---

<div class="container my-4" style="max-width: 520px;">
  <div class="card border">
    <div class="card-body p-4">
      <form id="capitalGainsTaxForm" onsubmit="event.preventDefault(); calculateCapitalGainsTax();">
        <!-- Purchase Price & Date -->
        <div class="row g-2 mb-3">
          <div class="col-md-7">
            <div class="form-floating">
              <input type="number" class="form-control" id="purchasePrice" placeholder="2000000" value="2000000"
                required>
              <label for="purchasePrice">Alış Bedeli (TL)</label>
            </div>
          </div>
          <div class="col-md-5">
            <div class="form-floating">
              <input type="date" class="form-control" id="purchaseDate" value="2023-05-15" required>
              <label for="purchaseDate">Alış Tarihi</label>
            </div>
          </div>
        </div>

        <!-- Sale Price & Date -->
        <div class="row g-2 mb-3">
          <div class="col-md-7">
            <div class="form-floating">
              <input type="number" class="form-control" id="salePrice" placeholder="6500000" value="6500000" required>
              <label for="salePrice">Satış Bedeli (TL)</label>
            </div>
          </div>
          <div class="col-md-5">
            <div class="form-floating">
              <input type="date" class="form-control" id="saleDate" value="2026-07-01" required>
              <label for="saleDate">Satış Tarihi</label>
            </div>
          </div>
        </div>

        <!-- Inflation Indexing Factor (Estimated PPI Adjustment) -->
        <div class="form-floating mb-3">
          <input type="number" step="0.01" class="form-control" id="ppiInflationFactor" placeholder="1.85" value="1.85"
            required>
          <label for="ppiInflationFactor">ÜFE Endeksleme Çarpanı (Tahmini)</label>
        </div>

        <!-- Calculate Button -->
        <button type="submit" class="btn btn-dark w-100 py-3 fw-bold rounded-3">
          Vergi Tutarlarını Hesapla
        </button>
      </form>

      <!-- Results Section -->
      <div id="taxResultsContainer" class="mt-4 p-3 bg-body-tertiary rounded-3 border d-none">
        <h6 class="fw-bold text-dark border-bottom pb-2 mb-3">Hesaplama Özeti</h6>

        <div class="d-flex justify-content-between align-items-center py-2 border-bottom">
          <span class="text-secondary small">Sahiplik Süresi:</span>
          <span class="fw-semibold" id="holdingPeriodOutput">0 Yıl</span>
        </div>
        <div class="d-flex justify-content-between align-items-center py-2 border-bottom">
          <span class="text-secondary small">Endekslenmiş Alış Bedeli:</span>
          <span class="fw-semibold" id="indexedPurchasePriceOutput">0 TL</span>
        </div>
        <div class="d-flex justify-content-between align-items-center py-2 border-bottom">
          <span class="text-secondary small">Safiy Kazanç (Net Kâr):</span>
          <span class="fw-semibold" id="netGainOutput">0 TL</span>
        </div>
        <div class="d-flex justify-content-between align-items-center py-2 border-bottom">
          <span class="text-secondary small">Yasal Muafiyet / İstisna:</span>
          <span class="fw-semibold text-success" id="exemptionOutput">0 TL</span>
        </div>

        <div class="d-flex justify-content-between align-items-center py-3 my-2 bg-danger-subtle px-3 rounded-3">
          <span class="fw-bold text-danger-emphasis">Ödenecek Vergi Tutarı:</span>
          <span class="fw-bold fs-4 text-danger" id="taxAmountOutput">0 TL</span>
        </div>
      </div>

      <!-- Error / Exemption Info Alert -->
      <div id="taxInfoMessage" class="alert alert-info mt-3 mb-0 d-none" role="alert"></div>
    </div>
  </div>
</div>

<div class="container my-4" style="max-width: 520px;">
  <div class="card border">
    <div class="card-body p-4">
      <h4 class="card-title text-dark fw-bold mb-3 mt-0">
        Değer Artış Kazancı Vergisi Mantığı
      </h4>
      <p class="text-muted small mb-4">
        Gelir Vergisi Kanunu'nun Mükerrer 80. maddesine göre, ivazlı (bedel karşılığı) edinilen gayrimenkullerin
        <strong>5 yıl içinde</strong> elden çıkarılmasından doğan kazançlar vergiye tabidir.
      </p>

      <ul class="list-group list-group-flush">
        <li class="list-group-item d-flex justify-content-between align-items-start px-0">
          <div class="ms-2 me-auto">
            <div class="fw-bold text-dark">5 Yıl Muafiyet Kuralı</div>
            <span class="small text-secondary">Alış tarihinden itibaren 5 tam yıl geçtikten sonra yapılan satışlarda
              vergi ödenmez.</span>
          </div>
        </li>
        <li class="list-group-item d-flex justify-content-between align-items-start px-0">
          <div class="ms-2 me-auto">
            <div class="fw-bold text-dark">Enflasyon Düzeltmesi (Endeksleme)</div>
            <span class="small text-secondary">Alış bedeli, alış ve satış tarihlerindeki TÜİK Yİ-ÜFE oranlarına göre
              güncellenerek sanal kârın önüne geçilir.</span>
          </div>
        </li>
        <li class="list-group-item d-flex justify-content-between align-items-start px-0">
          <div class="ms-2 me-auto">
            <div class="fw-bold text-dark">Yıllık İstisna Tutarı</div>
            <span class="small text-secondary">Hesaplanan net kârdan her yıl belirlenen yasal istisna tutarı
              düşülür.</span>
          </div>
        </li>
      </ul>

      <!-- Bağlantı Alanı -->
      <div class="mt-3 p-3 bg-body-tertiary rounded-3 border">
        <span class="small">
          → Gayrimenkul satışında vergi muafiyeti ve istisnalar hakkında detaylı bilgi almak için <a
            href="https://www.melihkocaturk.com/gayrimenkul-deger-artis-kazanci-vergisi-nasil-hesaplanir"
            target="_blank" class="fw-semibold text-decoration-underline text-dark">Gayrimenkul
            Değer Artış Kazancı Vergisi Nasıl Hesaplanır?</a> başlıklı rehberimizi inceleyebilirsiniz.
        </span>
      </div>
    </div>
  </div>
</div>

<script>
  function calculateCapitalGainsTax() {
    // Get inputs
    const purchasePrice = parseFloat(document.getElementById('purchasePrice').value) || 0;
    const salePrice = parseFloat(document.getElementById('salePrice').value) || 0;
    const purchaseDate = new Date(document.getElementById('purchaseDate').value);
    const saleDate = new Date(document.getElementById('saleDate').value);
    const ppiFactor = parseFloat(document.getElementById('ppiInflationFactor').value) || 1.0;

    const taxResultsContainer = document.getElementById('taxResultsContainer');
    const taxInfoMessage = document.getElementById('taxInfoMessage');

    // Reset UI
    taxInfoMessage.classList.add('d-none');

    if (saleDate <= purchaseDate) {
      taxInfoMessage.className = "alert alert-danger mt-3 mb-0";
      taxInfoMessage.innerText = "Satış tarihi, alış tarihinden sonra olmalıdır.";
      taxInfoMessage.classList.remove('d-none');
      taxResultsContainer.classList.add('d-none');
      return;
    }

    // Calculate holding period in months and years
    const diffTime = Math.abs(saleDate - purchaseDate);
    const diffDays = Math.ceil(diffTime / (1000 * 60 * 60 * 24));
    const holdingYears = diffDays / 365.25;

    // Currency Formatter
    const currencyFormatter = new Intl.NumberFormat('tr-TR', {
      style: 'currency',
      currency: 'TRY',
      maximumFractionDigits: 0
    });

    // CHECK RULE 1: If held for more than 5 years (1825 days) -> Exempt from Tax
    if (holdingYears >= 5) {
      taxInfoMessage.className = "alert alert-success mt-3 mb-0";
      taxInfoMessage.innerHTML = "<strong>Tebrikler!</strong> Gayrimenkulü alım tarihinden itibaren 5 yıldan (" + holdingYears.toFixed(1) + " yıl) fazla süreyle elinizde tuttuğunuz için <strong>Değer Artış Kazancı Vergisinden tamamen muafsınız.</strong> Ödenecek Vergi: 0 TL";
      taxInfoMessage.classList.remove('d-none');
      taxResultsContainer.classList.add('d-none');
      return;
    }

    // Inflation indexing on purchase price
    const indexedPurchasePrice = purchasePrice * ppiFactor;
    const netGain = salePrice - indexedPurchasePrice;

    // Exemption Limit (Annual tax exemption allowance)
    const annualExemptionLimit = 87000; // Standard annual exemption threshold
    let taxableAmount = 0;
    let taxAmount = 0;

    if (netGain > annualExemptionLimit) {
      taxableAmount = netGain - annualExemptionLimit;

      // Progressive Income Tax Brackets Approximation
      if (taxableAmount <= 110000) {
        taxAmount = taxableAmount * 0.15;
      } else if (taxableAmount <= 230000) {
        taxAmount = 16500 + (taxableAmount - 110000) * 0.20;
      } else if (taxableAmount <= 580000) {
        taxAmount = 40500 + (taxableAmount - 230000) * 0.27;
      } else if (taxableAmount <= 3000000) {
        taxAmount = 135000 + (taxableAmount - 580000) * 0.35;
      } else {
        taxAmount = 982000 + (taxableAmount - 3000000) * 0.40;
      }
    } else {
      taxAmount = 0;
    }

    // Output formatted results
    document.getElementById('holdingPeriodOutput').innerText = holdingYears.toFixed(1) + " Yıl (" + Math.round(holdingYears * 12) + " Ay)";
    document.getElementById('indexedPurchasePriceOutput').innerText = currencyFormatter.format(indexedPurchasePrice);
    document.getElementById('netGainOutput').innerText = currencyFormatter.format(Math.max(0, netGain));
    document.getElementById('exemptionOutput').innerText = currencyFormatter.format(annualExemptionLimit);
    document.getElementById('taxAmountOutput').innerText = currencyFormatter.format(taxAmount);

    // Display results
    taxResultsContainer.classList.remove('d-none');
  }
</script>