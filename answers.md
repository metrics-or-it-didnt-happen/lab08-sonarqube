**ZADANIE 2**

**Uwaga: wykonano dla Django (commit 378481165d14fea4c2a4b7717af3d7bdf9150f08) w celu łatwiejszej analizy w "Krok 3".**

**Krok 2:** Odpowiedźcie na pytania (zapiszcie w `answers.md`):

1. **Reliability (Bugs):**
   - Ile bugów znalazł SonarQube? 
       - 173
   - Jaki rating (A-E)? 
       - E
   - Podaj przykład jednego buga - co to jest i dlaczego SonarQube go flaguje? 
       - Co: niejasna alternacja w regexie w urlify.js (linia około 163): s.replace(/^\s+|\s+$/g, ''). 
       - Dlaczego SonarQube to flaguje: reguła S5850 — alternatywy w wyrażeniach regularnych użyte razem z kotwicami (^/$) powinny być pogrupowane, żeby jawnie określić kolejność/zakres działania. Bez grupowania wyrażenie jest poprawne i działa (dopasowuje początkową lub końcową spację), ale może być mylące dla czytelnika i łatwo ulec błędnej modyfikacji.
       - Konsekwencja: czytelność/utrzymanie (Reliability/Maintainability). Sonar traktuje to jako Major (wysiłek ~10min).
  
2. **Security (Vulnerabilities):**
   - Ile vulnerabilities?
       - 119
   - Jaki rating?
       - E
   - Czy któraś jest poważna (Critical/Blocker)?
       - Tak, o to przykłady:
       - Blocker (rule python:S6437): wiele wystąpień w tests/auth_tests/test_hashers.py (np. linia ~62) — hard-coded/kompromitowane hasła w testach. Sonar: "Revoke and change this password, as it is compromised."
       - Critical (rule python:S2053): tests/utils_tests/test_crypto.py (linia ~199) — brak nieprzewidywalnej soli w hashu ("Add an unpredictable salt value to this hash.").

3. **Maintainability (Code Smells):**
   - Ile code smells?
       - 2914
   - Jaki rating?
       - A
   - Jakie są top 5 najczęstszych typów code smell?
       - python:S3776: Cognitive Complexity of functions should not be too high — odpowiada za długie/zbite/metody (Bloaters / Long Methods).
       - python:S1481: Unused local variables should be removed — martwy kod (Dead Code).
       - python:S117: Local variable and function parameter names should comply with a naming convention — problem czytelności/konwencji (niewłaściwe nazwy).
       - python:S1172: Unused function parameters should be removed — martwy kod / zbędne parametry (Long Parameter Lists / Dead Code).
       - python:S6794: Type aliases should be declared with a "type" statement — styl/maintainability (Primitive Obsession / typy).

1. **Duplikacje:**
   - Jaki % kodu to duplikaty?
       - 1.1%
   - Który plik ma najwyższy % duplikacji?
       - tests/migrations/test_migrations_fake_split_initial/0001_initial.py
       - 96.7%

2. **Metryki:**
   - Ile łącznie LOC?
       - 403312
   - Jaka jest średnia złożoność cyklomatyczna?
       - 52,923 / 3,315 = 15.95 (średnio CC ≈ 15.95)
   - Ile plików ma CC > 20?
       - 589

3. **Ogólna ocena:**
   - Czy projekt "przechodzi" domyślny Quality Gate?
       - Failed
   - Jak oceniasz jakość tego projektu na podstawie wyników?
       - Projekt ma poważne problemy bezpieczeństwa i stabilności (119 vuln, w tym Blocker/Critical; 173 błędy → Reliability E), więc wymaga natychmiastowych poprawek. Mimo oceny Maintainability A jest 2,914 code smells i dużo hot‑spotów z wysoką złożonością (średnio CC ≈16, 589 plików z CC>20) — potrzebna refaktoryzacja. Quality Gate nie przechodzi; najpierw usuń Blocker/Critical, potem triage top‑20 plików wg CC i wprowadź automatyczne skanowanie w CI.

**Krok 3:** Porównajcie wyniki SonarQube z tym co policzyliście ręcznie na poprzednich labach:
- Czy LOC się zgadza z `cloc` / waszym `loc_counter.py`?
    - Nie zgadza się z naszym loc_counter.py, tam otrzymaliśmy wynik 376419 w porównaniu do SonarQube, który ma 403312. `cloc` natomiast zwrócił wynik 384503. 

- Czy złożoność się zgadza z `radon`?
    - Złożoność `radon` nie zgadza się z wynikiem SonarQube. SonarQube pokazuje najczęściej całkowitą sumę złożoności cyklomatycznej dla całego projektu, czyli sumuje complexity wszystkich funkcji i metod. Natomiast Radon w opcji --total-average oblicza średnią złożoność pojedynczej funkcji, dlatego wynik jest znacznie mniejszy (np. 1.8 zamiast kilkudziesięciu tysięcy). 

- Co SonarQube znalazł, czego wasze skrypty nie złapały?
    - SonarQube wykrył regułowe i bezpieczeństwa‑krytyczne problemy, których proste skrypty nie łapią — np. hard‑coded hasła i brak nieprzewidywalnej soli (Blocker/Critical) oraz rule‑based code smells (niewłaściwe nazwy, unused vars/params, typy).
    - Dodatkowo Sonar agreguje i priorytetyzuje hot‑spoty (sumy CC per‑file, lista plików z wysokim CC), wykrywa duplikacje na poziomie plików (jeden plik 96.7% duplikacji) i ocenia Quality Gate — wszystko to wykracza poza zakres cloc/loc_counter/radon.
    - Ponadto przy liczeniu prostych metryk (np. `cloc`) wykorzystuje SonarQube swoje reguły stądteż metryki takie zależą w główniej mierze od sposobu implementacji algorytmu. Nasza implementacja `loc_counter.py` na pewno była mniej zaawansowana niż ta, którą wykorzystuje SonarQube, zatem róznice są widoczne. 


**ZADANIE 3**
W projekcie został utworzony własny Quality Gate w SonarQube, którego zadaniem jest automatyczna kontrola jakości kodu podczas analizy aplikacji. Mechanizm ten pozwala sprawdzić, czy nowe zmiany wprowadzane do projektu spełniają ustalone wymagania dotyczące jakości, bezpieczeństwa oraz utrzymywalności kodu. Jeśli choć jeden z warunków nie zostanie spełniony, analiza kończy się statusem FAILED, co oznacza konieczność poprawienia wskazanych problemów przed dalszym wdrażaniem aplikacji.

W ramach Quality Gate ustawiono kilka kluczowych kryteriów. Pierwszym z nich jest całkowity brak nowych issue’ów, dzięki czemu każda nowa zmiana w kodzie musi być wolna od wykrytych problemów. Dodatkowo wymagane jest przejrzenie wszystkich Security Hotspots, co pozwala zweryfikować potencjalne miejsca ryzyka związanego z bezpieczeństwem aplikacji.

Kolejnym elementem jest kontrola pokrycia kodu testami. Ustalono minimalny poziom coverage na 60%, co ma zapewnić odpowiedni poziom testowania najważniejszych fragmentów aplikacji i ograniczyć ryzyko błędów po wdrożeniu. Quality Gate sprawdza również poziom duplikacji kodu — udział zduplikowanych linii nie może przekraczać 5%, co pomaga utrzymać czytelność projektu i ograniczać powielanie tej samej logiki w wielu miejscach.

Istotną częścią konfiguracji są również oceny jakości SonarQube. Maintainability Rating musi utrzymywać się minimum na poziomie B, co oznacza kontrolę nad technical debt i utrzymywanie kodu w stanie umożliwiającym łatwy rozwój oraz dalsze utrzymanie projektu. Reliability Rating oraz Security Rating zostały ustawione na poziom A, dzięki czemu aplikacja musi pozostawać wolna od poważnych błędów oraz podatności bezpieczeństwa.

Tak skonfigurowany Quality Gate pozwala utrzymać wysoki standard jakości projektu i wymusza stosowanie dobrych praktyk programistycznych podczas rozwijania aplikacji.