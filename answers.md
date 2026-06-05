# Odpowiedzi - Analiza SonarQube (projekt requests)

## 1. Reliability (Bugs):
- **Ile bugów znalazł SonarQube?** Znalazł 4 bugi.
- **Jaki rating (A-E)?** E
- **Podaj przykład jednego buga:**
  - *Przykład:* `Return an object complying with iterator protocol` w pliku `test_requests.py` z poziomem `BLOCKER`.
  - *Dlaczego Sonar go flaguje:* W kodzie znajduje się obiekt lub funkcja oczekująca iteratora, ale zwrócony typ nie implementuje wymaganych w Pythonie metod magicznych (np. `__iter__` oraz `__next__`), co może spowodować błąd w czasie działania programu.

## 2. Security (Vulnerabilities):
- **Ile vulnerabilities?** 0 podatności.
- **Jaki rating?** Rating A.
- **Czy któraś jest poważna (Critical/Blocker)?** Nie

## 3. Maintainability (Code Smells):
- **Ile code smells?** Znaleziono 93 code smells.
- **Jaki rating?** A
- **Jakie są top 5 najczęstszych typów code smell?**
  1. Złe nazewnictwo metod/zmiennych (27 wystąpień)
  2. Zbyt duża złożoność poznawcza (Cognitive Complexity) funkcji (13 wystąpień)
  3. Zbyt podobne/te same nazwy w różnych zakresach (9 wystąpień)
  4. Puste metody bez implementacji/ciała (7 wystąpień)
  5. Pozostawione tagi TODO / zakomentowane fragmenty do zrobienia (6 wystąpień)

## 4. Duplikacje:
- **Jaki % kodu to duplikaty?**  0.4%
- **Który plik ma najwyższy % duplikacji?** test_requests.py

## 5. Metryki:
- **Ile łącznie LOC?** 7703
- **Jaka jest średnia złożoność cyklomatyczna?** na plik około 33
- **Ile plików ma CC > 20?** 10

## 6. Ogólna ocena:
- **Czy projekt "przechodzi" domyślny Quality Gate?** 
  - Tak
- **Jak oceniasz jakość tego projektu na podstawie wyników?**
  - Jakość bazy kodowej `requests` jest bardzo wysoka. Zero podatności (vulnerabilities), niewielka liczba bugów (z czego większość to problemy mniejszej wagi w testach i templatkach HTML), oraz mały dług technologiczny (93 smells w 104 plikach to mniej niż 1 smell na plik).

---

## Krok 3: Porównanie wyników z poprzednimi narzędziami

1. **Czy LOC się zgadza z cloc / waszym loc_counter.py?**
   Nie, wyniki się różnią. SonarQube podał **7703** linii kodu, podczas gdy z poprzednich labów dla projektu `requests` wyniki wynosiły:
   - `cloc`: **7040** SLOC
   - `loc_counter`: **6324** SLOC
   Różnica wynika głównie z faktu, że narzędzia różnie definiują, co wchodzi w skład LOC. SonarQube domyślnie uwzględnia również inne języki analizowane w projekcie (np. tagi i logikę w szablonach HTML dla dokumentacji), a ponadto ma własne algorytmy dla traktowania m.in. docstringów w Pythonie.

2. **Czy złożoność się zgadza z radon?**
   Nie, wyniki się różnią. Nawet jeśli w SonarQube sprawdzimy złożoność średnią na jedną funkcję (wychodzi ok. 1.81), to znacznie różni się ona od średniej z narzędzia `radon` (wynoszącej ok. 3.1). Wynika to z faktu, że oba narzędzia opierają się na nieco innych algorytmach i zestawach reguł przy liczeniu ścieżek przepływu sterowania (Cyclomatic Complexity) dla specyficznych konstrukcji w Pythonie. Co więcej, Sonar wprowadza dodatkową metrykę `Cognitive Complexity` (Złożoność poznawcza), której bazowy `radon` w ogóle nie liczy. Zgadzają się jednak co do jednego: pliki i funkcje wskazane jako "najcięższe" (np. `adapters.py`, funkcja `HTTPAdapter.send`) są trafnie wyłapywane jako trudne przez oba z nich.

3. **Co SonarQube znalazł, czego wasze skrypty nie złapały?**
   Proste skrypty (`loc_counter`, `cloc`) oraz `radon` skupiają się tylko na zliczaniu znaków/linii lub ilości ścieżek sterowania. SonarQube jako narzędzie do Statycznej Analizy Kodu (SAST) znalazł rzeczy wymagające zrozumienia logiki kodu:
   - **Błędy uruchomieniowe (Bugi)** - np. wykrycie nieprawidłowej implementacji interfejsu iteratora (`BLOCKER`), co mogłoby wysypać program.
   - **Code Smells** - łamanie konwencji nazewnictwa w Pythonie, zostawione tagi TODO, czy nieużywany, zakomentowany kod.
   - **Duplikacje kodu** - Sonar znalazł pliki, gdzie metoda "Kopiuj-Wklej" doprowadziła do duplikacji (`0.4%`), czego zwykłe zliczarki LOC absolutnie nie widzą.

---

## Zadanie 3: Własny Quality Gate

Utworzono i przypisano nowy, bardziej rygorystyczny Quality Gate (np. "ORKiPO strict"). Po ponownym przeliczeniu metryk przez SonarQube projekt **nie przeszedł** tej weryfikacji (otrzymał status Failed). 

Dokładne wytyczne i warunki oblania naszego Quality Gate można zobaczyć na załączonym zrzucie ekranu: **`conditions.png`**.
