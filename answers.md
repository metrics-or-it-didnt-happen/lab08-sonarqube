**ZADANIE 2**

analizę przeprowadzono na projekcie Django 

1. **Reliability (Bugs):**
   - Ile bugów znalazł SonarQube? 
       - 173
   - Jaki rating (A-E)? 
       - E
   - Podaj przykład jednego buga - co to jest i dlaczego SonarQube go flaguje? 
       - Co: Niejednoznaczna alternatywa w wyrażeniu regularnym w pliku `urlify.js` (okolice 163. linijki): `s.replace(/^\s+|\s+$/g, '')`.
       - Dlaczego SonarQube to flaguje: Złamanie reguły S5850. Wskazuje ona, że używając alternatyw wewnątrz wyrażeń regularnych w połączeniu z kotwicami (`^` lub `$`), należy je otoczyć nawiasami grupującymi, aby jednoznacznie zdefiniować ich zasięg działania. Choć w obecnej formie zapis technicznie działa poprawnie (znajduje spacje na początku bądź na końcu), może być nieczytelny dla programistów i łatwo go popsuć przy ewentualnych modyfikacjach.
       - Konsekwencja: Pogorszona czytelność i utrudnione utrzymanie kodu (Reliability/Maintainability). Sonar klasyfikuje to jako problem rzędu Major (szacowany czas naprawy: ok. 10 minut).
  
2. **Security (Vulnerabilities):**
   - Ile vulnerabilities? 
       - 119
   - Jaki rating? 
       - E
   - Czy któraś jest poważna (Critical/Blocker)?
       - Tak, poniżej znajdują się przykłady:
       - Critical (reguła python:S2053): Przypadek w `tests/utils_tests/test_crypto.py` (linia ~199) – hash generowany jest bez dodatku losowej soli. Komunikat sugeruje dodanie nieprzewidywalnej wartości soli do tego hasha.
       - Blocker (reguła python:S6437): Liczne wystąpienia w pliku `tests/auth_tests/test_hashers.py` (m.in. linia ~62) dotyczące obecności wpisanych na sztywno, skompromitowanych haseł w kodzie testów. Komunikat z Sonara wyraźnie nakazuje ich unieważnienie i zmianę ("Revoke and change this password, as it is compromised.").

3. **Maintainability (Code Smells):**
   - Ile code smells?
       - 2914
   - Jaki rating?
       - A
   - Jakie są top 5 najczęstszych typów code smell?
       - `python:S1481`: Pozostawienie nieużywanych zmiennych lokalnych – klasyfikowane jako martwy kod (Dead Code).
       - `python:S6794`: Deklarowanie aliasów typów bez użycia instrukcji "type" – kwestia stylu kodowania oraz utrzymywalności (Primitive Obsession).
       - `python:S3776`: Zbyt duża złożoność kognitywna funkcji (Cognitive Complexity) – powoduje powstawanie rozbudowanych, trudnych do analizy metod (antywzorzec Bloaters / Long Methods).
       - `python:S1172`: Nieużywane parametry przekazywane do funkcji – nadmiarowy kod wydłużający sygnatury (Dead Code / Long Parameter Lists).
       - `python:S117`: Nazwy parametrów i zmiennych nieprzestrzegające ustalonych konwencji – problem z czytelnością i złym nazewnictwem.

4. **Duplikacje:**
   - Jaki % kodu to duplikaty? 
       - 1.1%
   - Który plik ma najwyższy % duplikacji? 
       - `tests/migrations/test_migrations_fake_split_initial/0001_initial.py`
       - Osiągnął wynik 96.7%

5. **Metryki:**
   - Ile łącznie LOC? 
       - 403312
   - Jaka jest średnia złożoność cyklomatyczna? 
       - 52,923 / 3,315 = 15.95 (średnio wskaźnik CC wynosi ok. 15.95)
   - Ile plików ma CC > 20?
       - 589

6. **Ogólna ocena:**
   - Czy projekt "przechodzi" domyślny Quality Gate? 
       - Failed
   - Jak oceniasz jakość tego projektu na podstawie wyników? 
       - Aplikacja cierpi na poważne usterki związane ze stabilnością i bezpieczeństwem (119 luk, w tym krytyczne i blokujące; do tego 173 błędy, co skutkuje oceną E z Reliability). Stan ten wymaga wdrożenia natychmiastowych poprawek. Chociaż Maintainability oceniono na A, to obecność aż 2914 code smellów i znacznej liczby punktów o wysokiej złożoności (średnie CC wynosi niemal 16, a aż 589 plików przekracza próg 20) świadczy o pilnej potrzebie refaktoryzacji. Projekt oblewa Quality Gate. Plan naprawczy powinien rozpocząć się od wyeliminowania problemów Blocker/Critical, następnie wykonania przeglądu 20 najbardziej skomplikowanych plików pod kątem CC i finalnie podpięcia automatycznych skanerów kodu do potoków CI.

- Czy LOC się zgadza z `cloc` / waszym `loc_counter.py`?
    - Nie, wyniki różnią się od siebie. Nasz skrypt `loc_counter.py` wyliczył 376419 linii, podczas gdy SonarQube raportuje 403312. Z kolei użycie narzędzia `cloc` dało wartość 384503 linii.

- Czy złożoność się zgadza z `radon`?
    - Złożoność zliczona przez `radon` drastycznie odbiega od tej z SonarQube. Wynika to z faktu, że Sonar z reguły podaje całkowitą sumę złożoności cyklomatycznej dla całego projektu (sumuje CC z każdej zaimplementowanej metody i funkcji). Z kolei użyty przez nas Radon z przełącznikiem `--total-average` wyciąga uśrednioną wartość złożoności na pojedynczą funkcję, przez co wypluwa wynik o wiele niższy (np. rzędu 1.8 zamiast wielotysięcznych sum).

- Co SonarQube znalazł, czego wasze skrypty nie złapały?
    - SonarQube skutecznie odnalazł usterki oparte na zaawansowanych regułach, w tym krytyczne z punktu widzenia bezpieczeństwa braki – np. zapisane jawnym tekstem hasła czy brak soli kryptograficznych w algorytmach (Blocker/Critical). Do tego rozpoznał specyficzne dla danego języka code smelle (błędy konwencji nazewnictwa, niewykorzystane parametry/zmienne, problemy z typowaniem).
    - Ponadto, Sonar potrafi agregować i układać problemy według priorytetów (tworzy sumy CC dla poszczególnych plików, generuje listę plików z najwyższym CC), wykrywa zduplikowane bloki bezpośrednio na poziomie plików (rekordzista miał 96.7% kopii) i całościowo ocenia repozytorium przez pryzmat Quality Gate. Te funkcjonalności wykraczają poza możliwości prostych narzędzi rzędu cloc, loc_counter czy radon.
    - Dodatkowo, przy analizie podstawowych wskaźników (np. linii kodu), Sonar polega na swoich własnych, złożonych parserach i regułach, co sprawia, że ostateczny wynik w dużej mierze zależy od użytego algorytmu. Nasz skrypt `loc_counter.py` był z natury dużo prostszy niż machina pod maską Sonara, dlatego różnice w wyliczeniach są tak wyraźne.


**ZADANIE 3**


W projekcie skonfigurowano dedykowany profil Quality Gate w SonarQube, który automatycznie weryfikuje każdą zmianę w kodzie. Niespełnienie któregokolwiek z warunków kończy się statusem FAILED i blokuje wdrożenie aplikacji do czasu wniesienia poprawek.

Aby kod przeszedł weryfikację, musi spełniać następujące kryteria:

* **Brak nowych błędów:** 0 nowych issues.
* **Bezpieczeństwo:** Przejrzane 100% Security Hotspots.
* **Testy:** Coverage na poziomie minimum 60%.
* **Duplikacje:** Maksymalnie 5% powielonego kodu.
* **Oceny SonarQube:**
    * Maintainability: minimum B
    * Reliability: A
    * Security: A

Tak rygorystyczna konfiguracja skutecznie kontroluje dług technologiczny, zapobiega podatnościom i wymusza wysoki standard rozwijanej aplikacji.