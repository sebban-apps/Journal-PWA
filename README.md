# Dziennik nauki

Prosta aplikacja do mierzenia czasu nauki i zapisywania krótkich notatek po każdej sesji. Działa w przeglądarce na telefonie i komputerze, a na telefonie można ją zainstalować jak zwykłą aplikację.

Adres aplikacji **https://sebban-apps.github.io/Journal-PWA/**

## Jak jest ułożona

Na telefonie zakładki są na dole ekranu, na komputerze u góry.

| Zakładka | Co w niej jest |
|---|---|
| **Dziś** | timer z tarczą, notatka po sesji, podsumowanie dnia i plan dnia |
| **Dziennik** | wszystkie wpisy pogrupowane po dniach, wyszukiwanie, filtry, dodawanie wpisów |
| **Kalendarz** | miesiąc z zaznaczonymi dniami nauki i wpisy wybranego dnia |
| **Statystyki** | czas nauki z ostatnich 7 lub 30 dni, seria dni z rzędu, podział na kategorie |
| **Ustawienia** | motyw, kategorie, skróty timera, kopia zapasowa, informacje o wersji |

## Jak korzystać

### Timer (zakładka Dziś)

1. Wybierz skrót (np. **Techniczna · 45**) albo wpisz własny czas od 1 do 180 minut.
2. Naciśnij **Zacznij**. W trakcie możesz dać **Pauza** i **Wznów**.
3. Gdy czas minie, telefon krótko zawibruje (jeśli to obsługuje) i pojawi się ekran notatki. Sesję można też skończyć wcześniej przyciskiem **Zakończ i zapisz**, wtedy zapisze się faktycznie przepracowany czas.
4. Wybierz kategorię, dopisz kilka słów i naciśnij **Zapisz**. **Pomiń** wraca do timera bez zapisu.

Wpis dostaje godzinę **rozpoczęcia** sesji. Trwająca sesja przetrwa zamknięcie aplikacji albo przeładowanie strony. Po ponownym otwarciu timer liczy dalej, a jeśli czas minął w międzyczasie, od razu pojawi się ekran notatki.

Gdy przejdziesz do innej zakładki w trakcie sesji, na dole ekranu pojawi się pasek z pozostałym czasem. Dotknięcie go wraca do timera.

Pod timerem widać, ile dziś się uczyłeś, ile było sesji i ile dni z rzędu trwa seria.

### Plan dnia (zakładka Dziś)

Wpisz godzinę (np. `9:00`, można pominąć) i treść punktu, potem naciśnij **+** albo Enter. Kwadrat po lewej oznacza punkt jako zrobiony, a pasek pokazuje postęp. Strzałki przełączają dni, a **wróć do dziś** wraca do bieżącego dnia.

### Dziennik

* **Dodaj wpis** otwiera formularz, w którym podajesz datę, godzinę rozpoczęcia, czas trwania (do 1440 minut), kategorię i notatkę. Ten sam formularz otwiera link **zapisz sesję, która już się odbyła** pod timerem.
* Ołówek przy wpisie otwiera ten sam formularz do edycji. Można zmienić także datę i godzinę.
* Gwiazdka dodaje wpis do ulubionych, kosz go usuwa (po potwierdzeniu).
* Pole wyszukiwania przeszukuje notatki i nazwy kategorii. Gwiazdka obok pokazuje tylko ulubione, a kategorie pod spodem filtrują listę.
* Nad listą widać liczbę wpisów i łączny czas dla aktualnych filtrów. **wyczyść filtry** pokazuje znów wszystko.

### Kalendarz

Strzałki zmieniają miesiąc. Dni z nauką mają kropki i sumę minut, dzisiejszy dzień jest obwiedziony. Po kliknięciu dnia widać jego wpisy (z tymi samymi przyciskami co w dzienniku) i przycisk **Dodaj**, który otwiera formularz z wybraną datą.

### Statystyki

* Kafelki pokazują czas z dzisiaj, serię dni z rzędu i łączny czas.
* Wykres **Czas nauki** pokazuje minuty z każdego dnia z ostatnich 7 albo 30 dni. Dotknięcie słupka pokazuje dokładną wartość.
* **Kategorie** pokazują, ile czasu poszło na każdą kategorię w ostatnich 7 dniach, 30 dniach albo w całej historii.

### Ustawienia

* **Wygląd** pozwala wybrać motyw jasny, ciemny albo zgodny z ustawieniami telefonu.
* **Kategorie** można dodawać, usuwać i zmieniać im nazwy. Zmiana nazwy poprawia też wszystkie wpisy z tą kategorią. Usunięcie kategorii nie usuwa wpisów.
* **Skróty timera** można dodawać i usuwać.
* **Kopia zapasowa** opisana jest niżej.

## Instalacja na telefonie

Otwórz adres aplikacji w przeglądarce.

* **Android (Chrome)** menu ⋮ i **Dodaj do ekranu głównego** lub **Zainstaluj aplikację**
* **iPhone (Safari)** przycisk udostępniania i **Do ekranu początkowego**

Na Androidzie przytrzymanie ikony pokazuje skróty **Nowy wpis** i **Statystyki**.

Jeśli masz zainstalowaną wersję spod starego adresu `sebban03.github.io`, usuń ją i zainstaluj aplikację od nowa z nowego adresu (najpierw przenieś dane, patrz niżej). Dziennik nie myli się z innymi aplikacjami spod `sebban-apps.github.io`, każdą można zainstalować osobno.

## Gdzie są zapisane dane

Wszystkie wpisy, kategorie, skróty, plany dnia i trwająca sesja są zapisywane **tylko w tej przeglądarce na tym urządzeniu**. Nic nie jest wysyłane na serwer i nie ma konta.

Oznacza to, że

* dane z telefonu nie pojawią się same na komputerze,
* wyczyszczenie danych przeglądarki usuwa wpisy,
* pod innym adresem strony (np. po przeniesieniu repozytorium) dziennik jest pusty,
* tryb prywatny może nie zapamiętywać danych. Aplikacja ostrzega wtedy komunikatem.

### Kopia zapasowa

W **Ustawieniach** przycisk **Eksportuj kopię** zapisuje wszystkie dane do pliku JSON. Na telefonie plik można od razu wysłać, np. na Dysk Google. **Przywróć z pliku** wczytuje taki plik i **zastępuje** obecne dane, więc aplikacja pyta przed tym o potwierdzenie. W ten sam sposób przeniesiesz dane na inne urządzenie albo pod nowy adres.

Aplikacja pamięta datę ostatniej kopii i przypomina, jeśli minęły ponad 2 tygodnie.

## Ograniczenia

* Aplikacja potrzebuje internetu przy otwieraniu. Nie ma trybu offline z pamięcią podręczną.
* Timer nie wysyła powiadomień systemowych. Przy zablokowanym ekranie czas liczy się poprawnie, ale sygnał końca może nie dotrzeć.
* Statystyka „7 dni” obejmuje dzisiejszy dzień i sześć poprzednich, a nie tydzień kalendarzowy.
