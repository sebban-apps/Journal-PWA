# Dziennik nauki, dokumentacja techniczna

## Przegląd

Aplikacja jest statyczną stroną PWA (bez service workera). Cały kod, czyli HTML, CSS i JavaScript, jest w jednym pliku `index.html`, bez frameworków, bundlera i zależności npm. Dane trzymane są lokalnie w przeglądarce.

Interfejs korzysta z systemu wizualnego **Modern Teal Signature** (paleta teal, Manrope i Inter, jasny i ciemny motyw).

Wersja aplikacji to stała `APP_VERSION` w skrypcie (obecnie `2.1`). Widać ją w **Ustawienia → O aplikacji**, co pozwala sprawdzić, czy GitHub Pages serwuje już nową wersję.

## Struktura plików

| Plik | Rola |
|---|---|
| `index.html` | cała aplikacja (punkt startowy, `start_url`) |
| `dziennik-nauki.html` | stary adres aplikacji, przekierowuje na `./` z zachowaniem części po `#` |
| `manifest.json` | manifest PWA |
| `icon-192.png`, `icon-512.png` | ikony aplikacji |
| `icon-maskable-512.png` | ikona dla Androida (adaptacyjna, treść w bezpiecznym kole) |
| `README.md` | instrukcja dla użytkownika |
| `KONFIGURACJA.md` | ten plik |

`dziennik-nauki.html` **nie wolno usuwać**. Starsze instalacje i zakładki otwierają właśnie ten adres.

## Wymagania i uruchomienie

* Dowolny serwer plików statycznych lub GitHub Pages
* Nowoczesna przeglądarka
* Brak zmiennych środowiskowych, kluczy API i bazy danych

Manifest i instalacja działają tylko przez HTTP, nie z `file://`. Żeby lokalnie odwzorować ścieżkę z GitHub Pages, uruchom serwer katalog wyżej.

```bash
cd katalog-nad-projektem          # tu leży folder Journal-PWA
python3 -m http.server 8000
# otwórz http://localhost:8000/Journal-PWA/
```

## Publikacja na GitHub Pages

Repozytorium to `sebban-apps/Journal-PWA`, adres strony to `https://sebban-apps.github.io/Journal-PWA/`.

1. Wrzuć pliki do katalogu głównego gałęzi `main`.
2. **Settings → Pages → Build and deployment → Source** ustaw na **Deploy from a branch**, gałąź `main`, folder `/ (root)`.
3. Po każdym commicie w zakładce **Actions** powinno pojawić się „pages build and deployment”. Brak takiego wpisu oznacza, że źródło Pages jest ustawione na **GitHub Actions** bez pliku workflow (tak było po przeniesieniu repozytorium).

GitHub Pages trzyma pliki w pamięci podręcznej do około 10 minut. Nową wersję najpewniej sprawdzisz w karcie incognito, porównując numer wersji w Ustawieniach.

## Manifest i tożsamość aplikacji (PWA)

```json
"id": "/Journal-PWA/dziennik-nauki.html",
"start_url": "./",
"scope": "./"
```

* **`id`** to stały identyfikator aplikacji. Chrome rozwiązuje go względem domeny, więc pełna tożsamość to `https://sebban-apps.github.io/Journal-PWA/dziennik-nauki.html`. Wartość jest celowo taka sama jak identyfikator, który Chrome wyliczał wcześniej, gdy manifest nie miał `id`, a `start_url` wskazywał `dziennik-nauki.html`. Dzięki temu istniejące instalacje pod tym adresem są dalej rozpoznawane jako ta sama aplikacja. **Nie zmieniaj `id`**, nawet przy zmianie plików. Zmiana utworzyłaby w przeglądarce drugą, osobną aplikację.
* **`start_url`** `./` to `/Journal-PWA/`, czyli `index.html`.
* **`scope`** `./` obejmuje tylko `/Journal-PWA/`. Nie sięga do innych aplikacji pod tą samą domeną.
* **`shortcuts`** dodają do ikony na Androidzie pozycje **Nowy wpis** (`./#dodaj`) i **Statystyki** (`./#statystyki`).

### Inne aplikacje pod `sebban-apps.github.io`

Wszystkie aplikacje z organizacji działają w tej samej domenie, więc dzielą też `localStorage`. Stan sprawdzony 4 listopada 2026.

| Aplikacja | Identyfikator (Chrome) | Klucze danych |
|---|---|---|
| Journal-PWA | `/Journal-PWA/dziennik-nauki.html` (jawne `id`) | `dziennik-nauki:*` |
| Calendar-PWA | `/Calendar-PWA/kalendarz-nawykow.html` (wyliczone, bez `id`) | `kalendarz-nawykow:*` |
| training-log | `/training-log/index.html` (jawne `id`) | IndexedDB `workoutTrackerDB`, `dziennik.theme` |

Identyfikatory, zakresy i klucze się nie pokrywają. Chrome w zwykłym profilu nie zgłasza dla żadnej z tych aplikacji błędów instalowalności.

W repozytorium **Calendar-PWA** leży omyłkowo wgrana kopia `dziennik-nauki.html` (dodana 3 listopada 2026). Pod adresem `/Calendar-PWA/dziennik-nauki.html` otwiera się wtedy dziennik z manifestem kalendarza. Plik trzeba usunąć z tamtego repozytorium. Calendar-PWA warto też uzupełnić o jawne `"id": "/Calendar-PWA/kalendarz-nawykow.html"`, zgodne z obecnym wyliczonym identyfikatorem.

## Usługi zewnętrzne

Jedyna zależność sieciowa to czcionki **Google Fonts** (Inter i Manrope). Gdy są niedostępne, przeglądarka używa czcionki systemowej i aplikacja działa dalej.

## Przechowywanie danych

Warstwa `Store` wybiera magazyn w tej kolejności

1. `window.storage`, jeśli istnieje (środowisko artefaktów Claude)
2. `localStorage` z prefiksem `dziennik-nauki:`
3. pamięć operacyjna, gdy zapis jest zablokowany. Aplikacja pokazuje wtedy ostrzeżenie

| Klucz | Zawartość | W kopii zapasowej |
|---|---|---|
| `sessions` | `[{ id, date (ISO, początek sesji), subject, minutes, note, favorite }]` | tak |
| `categories` | tablica nazw kategorii | tak |
| `presets` | skróty timera w minutach (1 do 180) | tak |
| `dayplans` | `{ "RRRR-MM-DD": [{ id, time, text, done }] }` | tak |
| `timer` | stan trwającej sesji `{ phase, total, left, endAt, running, startedAt, lastMinutes }` albo `null` | nie |
| `lastBackup` | data ostatniego eksportu (ISO) | nie |
| `installDismissed` | `1`, gdy użytkownik ukrył baner instalacji. Zapisywany bezpośrednio w `localStorage` | nie |
| `theme` | `light` lub `dark`, brak klucza oznacza motyw systemowy. Czytany bezpośrednio z `localStorage` przed pierwszym malowaniem | nie |

Odczyty przechodzą przez funkcje strażnicze (`asArray`, `asPlainObject`, filtry w `load*`), które odrzucają uszkodzone dane. Format danych jest zgodny z wersją 1.x, więc wcześniejsze wpisy wczytują się bez migracji.

### Plik kopii zapasowej

```json
{
  "app": "dziennik-nauki",
  "version": 1,
  "exportedAt": "2026-11-04T12:00:00.000Z",
  "sessions": [],
  "categories": ["SQL", "Python", "Angielski", "Inne"],
  "presets": [20, 30, 45],
  "dayplans": {}
}
```

Import przechodzi przez `sanitizeBackupPayload` i zastępuje cztery klucze danych. Przy zmianie formatu podnieś `BACKUP_VERSION` i obsłuż starsze wersje w tej funkcji.

## Architektura skryptu

* **Nawigacja** `showView(name)` przełącza widoki `today`, `log`, `calendar`, `stats`, `settings` i zapisuje adres po `#` (`#dzis`, `#dziennik`, `#kalendarz`, `#statystyki`, `#ustawienia`) przez `history.replaceState`, bez dokładania historii. `#dodaj` otwiera formularz nowego wpisu. Zmiana adresu przy otwartej aplikacji jest obsługiwana przez zdarzenie `hashchange`.
* **Timer** liczy czas od znacznika końca `T.endAt`, a nie od taktów `setInterval`, więc nie gubi sekund przy uśpionej karcie. Fazy to `idle`, `active` i `note`. Każda zmiana stanu trafia do klucza `timer`, a `restoreTimer()` odtwarza ją przy starcie.
* **Formularz wpisu** `openEntry({ session?, date? })` obsługuje dodawanie i edycję z dziennika, kalendarza i timera. `handleEntryAction()` obsługuje gwiazdkę, edycję i usuwanie w dzienniku i kalendarzu.
* **Okna** `Overlay` trzyma stos otwartych warstw, więc Escape zamyka tylko górną. `Dialog.confirm` i `Dialog.prompt` zastępują `confirm()` i `prompt()`.
* **Instalacja** skrypt w `<head>` przechwytuje zdarzenie `beforeinstallprompt` (wywołuje `preventDefault()` i zapisuje je w `window.__installEvent`), bo przeglądarka może je wysłać, zanim wczyta się reszta skryptu. `renderInstall()` wybiera jeden z pięciu stanów karty **Ustawienia → Aplikacja**, czyli tryb zainstalowanej aplikacji (`display-mode: standalone` lub `navigator.standalone`), świeżo zainstalowana (`appinstalled`), instalacja możliwa, instrukcja dla iPhone'a albo wyjaśnienie, czemu przeglądarka nie proponuje instalacji. `doInstall()` wywołuje `prompt()` i czyta `userChoice`. Identyfikator pokazywany w karcie jest wyliczany z pobranego `manifest.json` tak samo jak w Chrome (`id` rozwiązane względem domeny). Baner na ekranie Dziś pojawia się tylko przy dostępnym `beforeinstallprompt`, poza trybem aplikacji i jeśli nie został ukryty. Safari i Firefox na komputerze nie wysyłają `beforeinstallprompt`, dlatego tam działa tylko instrukcja.
* **Odświeżanie** `refreshAll()` przerysowuje dziennik, kalendarz, statystyki, podsumowanie dnia i ustawienia po każdej zmianie danych.

## System wizualny

Kolory, promienie, odstępy, czcionki i czasy animacji są tokenami CSS w `:root`. Motyw ciemny jest zdefiniowany dla `:root[data-theme="dark"]` oraz dla `prefers-color-scheme: dark`, gdy użytkownik nie wybrał jasnego. Komponenty używają tokenów pochodnych (`--accent`, `--accent-tint`, `--accent-text`, `--surface`, `--elevated`).

Nawigacja poniżej 760 px jest przypięta do dołu ekranu, powyżej staje się paskiem pod nagłówkiem. Od 960 px zakładka Dziś ma dwie kolumny (timer i plan), a Statystyki dwie karty obok siebie.

Uwaga dla przycisków przesuniętych przez `transform`. Ogólna reguła `button:active { transform: scale(.98) }` nadpisuje ich położenie, dlatego `.mini-timer` ma własną regułę `:active`. Bez niej pasek przeskakiwał przy dotknięciu i kliknięcie się nie liczyło.

## Testy

Automatycznych testów w repozytorium nie ma. Elementy mają atrybuty `data-testid`, a po starcie ustawiana jest flaga `window.__appReady = true`, co ułatwia testy np. w Playwright. Do sprawdzenia tożsamości PWA przydają się polecenia DevTools Protocol `Page.getAppId` i `Page.getInstallabilityErrors` (w trybie incognito Chrome zawsze zgłasza `in-incognito`, to nie jest błąd aplikacji).

Lista kontrolna po zmianach

* timer, pauza, zakończenie, przeładowanie strony w trakcie sesji i na ekranie notatki
* pasek trwającej sesji w innych zakładkach i powrót po dotknięciu
* dodanie, edycja (ze zmianą daty) i usunięcie wpisu
* wyszukiwanie, ulubione, filtr kategorii, wyczyść filtry
* wpis z kalendarza, przełączanie miesięcy
* plan dnia, walidacja godziny, odhaczanie
* statystyki 7 i 30 dni, dymek nad słupkiem
* zmiana nazwy i usunięcie kategorii, dodanie i usunięcie skrótu
* eksport i import kopii
* motyw systemowy, jasny i ciemny
* instalacja z banera i z Ustawień, ukrycie banera, karta w zainstalowanej aplikacji, karta w Safari na iPhonie (do testów w Chrome przydaje się flaga `--bypass-app-banner-engagement-checks`)
* adresy `./#dodaj` i `dziennik-nauki.html#statystyki`
* szerokość 360 px i komputer
* po wdrożeniu `Page.getAppId` pod adresem z GitHub Pages zwraca `.../Journal-PWA/dziennik-nauki.html`

## Rozwiązywanie problemów

| Problem | Przyczyna i rozwiązanie |
|---|---|
| Po wgraniu plików widać starą wersję | Źródło Pages ustawione na GitHub Actions bez workflow, trwający build albo pamięć podręczna. Sprawdź **Actions**, **Settings → Pages** i numer wersji w Ustawieniach (w karcie incognito) |
| „Ta aplikacja jest już zainstalowana” przy innej aplikacji | Dwie aplikacje mają ten sam identyfikator. Sprawdź `id` i `start_url` w ich manifestach oraz zbłąkane kopie plików w cudzych repozytoriach |
| Brak opcji instalacji | Strona otwarta z `file://`, tryb incognito albo błąd w manifeście |
| Dane zniknęły | Inny adres strony, wyczyszczone dane witryny albo tryb prywatny. Przywróć z kopii |
| Inne czcionki niż w projekcie | Google Fonts zablokowane, aplikacja działa na czcionkach systemowych |
| Import zgłasza błąd | Plik nie jest JSON albo nie pochodzi z tej aplikacji |

## Wersjonowanie i aktualizacje

* Przy każdej widocznej zmianie podnieś `APP_VERSION` w `index.html`.
* Format danych to `BACKUP_VERSION`. Zmiany struktury danych muszą być wstecznie zgodne albo obsłużone migracją w funkcjach `load*`.
* Aktualizacja polega na podmianie plików w repozytorium. Bez service workera użytkownik dostaje nową wersję przy kolejnym załadowaniu strony.
* `id` w manifeście pozostaje bez zmian między wersjami.

## Możliwe rozszerzenia

Nie są zaimplementowane.

* service worker z pamięcią podręczną dla pracy offline
* powiadomienia systemowe o końcu sesji
