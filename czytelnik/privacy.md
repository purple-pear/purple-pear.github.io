---
layout: default
title: Privacy Policy
---

<script>
  // Run before the sections below are parsed, so only the selected language
  // is ever painted. Without JS the `has-js` class is absent and both
  // language sections stay visible.
  document.documentElement.className += ' has-js';
</script>

<style>
  .lang-switch { display: none; gap: .5rem; margin-bottom: 1.5rem; }
  .has-js .lang-switch { display: flex; }
  .lang-switch button {
    font: inherit;
    padding: .4rem 1rem;
    cursor: pointer;
    border: 1px solid #1e6bb8;
    border-radius: .3rem;
    background: transparent;
    color: #1e6bb8;
  }
  .lang-switch button[aria-pressed="true"] { background: #1e6bb8; color: #fff; }
  .has-js .lang-section { display: none; }
  .has-js .lang-section.is-active { display: block; }
  .lang-section + .lang-section { margin-top: 3rem; }
  .has-js .lang-section + .lang-section { margin-top: 0; }
</style>

<div class="lang-switch" role="group" aria-label="Language / Język">
  <button type="button" data-lang="pl" aria-pressed="false">Polski</button>
  <button type="button" data-lang="en" aria-pressed="false">English</button>
</div>

{% capture privacy_pl %}{% include czytelnik/privacy-pl.md %}{% endcapture %}
{% capture privacy_en %}{% include czytelnik/privacy-en.md %}{% endcapture %}

<div class="lang-section" id="lang-pl" lang="pl">{{ privacy_pl | markdownify }}</div>

<div class="lang-section" id="lang-en" lang="en">{{ privacy_en | markdownify }}</div>

<script>
  (function () {
    var LANGS = ['pl', 'en'];
    var STORAGE_KEY = 'czytelnik-privacy-lang';
    var buttons = document.querySelectorAll('.lang-switch button');

    function isLang(value) {
      return LANGS.indexOf(value) !== -1;
    }

    function stored() {
      try { return localStorage.getItem(STORAGE_KEY); } catch (e) { return null; }
    }

    function remember(lang) {
      try { localStorage.setItem(STORAGE_KEY, lang); } catch (e) { /* private mode */ }
    }

    // Precedence: URL fragment, then previous choice, then browser language.
    function initialLang() {
      var hash = location.hash.replace('#', '');
      if (isLang(hash)) return hash;
      var saved = stored();
      if (isLang(saved)) return saved;
      var nav = (navigator.language || '').toLowerCase();
      return nav.indexOf('pl') === 0 ? 'pl' : 'en';
    }

    function show(lang) {
      LANGS.forEach(function (code) {
        var section = document.getElementById('lang-' + code);
        if (section) section.classList.toggle('is-active', code === lang);
      });
      Array.prototype.forEach.call(buttons, function (button) {
        button.setAttribute('aria-pressed', String(button.dataset.lang === lang));
      });
      document.title = lang === 'pl'
        ? 'Polityka prywatności - Czytelnik'
        : 'Privacy Policy - Czytelnik';
    }

    Array.prototype.forEach.call(buttons, function (button) {
      button.addEventListener('click', function () {
        var lang = button.dataset.lang;
        show(lang);
        remember(lang);
        // replaceState keeps the URL shareable without scrolling the page.
        history.replaceState(null, '', '#' + lang);
      });
    });

    window.addEventListener('hashchange', function () {
      var hash = location.hash.replace('#', '');
      if (isLang(hash)) show(hash);
    });

    show(initialLang());
  })();
</script>
