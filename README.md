# Lab 08: SonarQube — rentgen kodu

## Czy wiesz, że...

Według badań (które właśnie wymyśliłem), 82% programistów po pierwszym uruchomieniu SonarQube na swoim projekcie ma taki sam wyraz twarzy jak pacjent, który właśnie zobaczył swój rachunek za leczenie w USA.

## Kontekst

Przez ostatnie cztery laby pisaliście własne skrypty do mierzenia kodu. To fajne i pouczające, ale w realnym świecie nikt nie pisze od zera analizatora metryk — używa gotowych platform. SonarQube to jedna z najpopularniejszych: open-source'owa platforma do ciągłej inspekcji jakości kodu.

SonarQube robi wszystko to, co robiliście ręcznie (LOC, złożoność, duplikacje) — plus rzeczy, których nie robiliście (analiza bezpieczeństwa, wykrywanie bugów, code smells). I robi to automatycznie, przy każdym buildzie, z ładnym dashboardem w przeglądarce.

## Cel laboratorium

Po tym laboratorium będziesz potrafić:
- uruchomić SonarQube Community Edition w Dockerze,
- skonfigurować i uruchomić analizę projektu Pythonowego,
- interpretować wyniki: bugi, vulnerabilities, code smells, duplikacje,
- zrozumieć ratingi (A-E) i Quality Gates.

## Wymagania wstępne

- Docker Desktop zainstalowany i uruchomiony
- Minimum **4 GB wolnej pamięci RAM** (SonarQube jest zasobożerny!)
- Sklonowany projekt open-source w Pythonie
- Przeglądarka (do dashboardu SonarQube)

## Zadania

### Zadanie 1: Stawiamy SonarQube (45 min)

SonarQube działa jako serwer webowy. Uruchomimy go w Dockerze razem z sonar-scannerem.

**Krok 1:** Stwórzcie `docker-compose.yml` (jest już w repozytorium, ale zapoznajcie się z nim):

Plik `docker-compose.yml` w tym repozytorium zawiera gotową konfigurację. Przyjrzyjcie mu się, żeby zrozumieć co uruchamia.

**Krok 2:** Uruchomcie SonarQube:

```bash
docker compose up -d
```

Poczekajcie 1-2 minuty aż SonarQube się uruchomi. Sprawdźcie logi:

```bash
docker compose logs sonarqube | tail -20
# Szukajcie: "SonarQube is operational"
```

**Krok 3:** Otwórzcie przeglądarkę na http://localhost:9000

- Login: `admin`
- Hasło: `admin`
- SonarQube poprosi o zmianę hasła — zmieńcie na coś prostego (np. `sonar123`)

**Krok 4:** Stwórzcie nowy projekt:

1. Kliknij "Create a local project"
2. Project display name: np. `requests`
3. Project key: np. `requests`
4. Main branch name: `main`
5. Wybierz "Use the global setting" dla baseline
6. Kliknij "Locally"
7. Wygeneruj token (skopiuj go!)
8. Wybierz "Other (for JS, TS, Go, Python, PHP, ...)"
9. Wybierz system operacyjny

**Krok 5:** Sklonujcie projekt OSS do analizy (jeśli nie macie):

```bash
git clone https://github.com/psf/requests.git /tmp/requests
```

**Krok 6:** Uruchomcie analizę sonar-scannerem:

```bash
docker compose run --rm sonar-scanner \
  -Dsonar.projectKey=requests \
  -Dsonar.sources=/src \
  -Dsonar.host.url=http://sonarqube:9000 \
  -Dsonar.token=WSTAW_SWOJ_TOKEN \
  -Dsonar.python.version=3.11
```

Analiza potrwa kilka minut. Po zakończeniu odświeżcie dashboard w przeglądarce.

### Zadanie 2: Pierwsza analiza (60 min)

Czas na czytanie "wyników badań" naszego "pacjenta".

**Krok 1:** Przejrzyjcie dashboard projektu na http://localhost:9000. Zobaczcie:
- **Overview** — ogólny stan
- **Issues** — lista problemów
- **Measures** — szczegółowe metryki
- **Code** — przeglądarka kodu z adnotacjami

**Krok 2:** Odpowiedźcie na pytania (zapiszcie w `answers.md`):

1. **Reliability (Bugs):**
   - Ile bugów znalazł SonarQube?
   - Jaki rating (A-E)?
   - Podaj przykład jednego buga — co to jest i dlaczego SonarQube go flaguje?

2. **Security (Vulnerabilities):**
   - Ile vulnerabilities?
   - Jaki rating?
   - Czy któraś jest poważna (Critical/Blocker)?

3. **Maintainability (Code Smells):**
   - Ile code smells?
   - Jaki rating?
   - Jakie są top 5 najczęstszych typów code smell?

4. **Duplikacje:**
   - Jaki % kodu to duplikaty?
   - Który plik ma najwyższy % duplikacji?

5. **Metryki:**
   - Ile łącznie LOC?
   - Jaka jest średnia złożoność cyklomatyczna?
   - Ile plików ma CC > 20?

6. **Ogólna ocena:**
   - Czy projekt "przechodzi" domyślny Quality Gate?
   - Jak oceniasz jakość tego projektu na podstawie wyników?

**Krok 3:** Porównajcie wyniki SonarQube z tym co policzyliście ręcznie na poprzednich labach:
- Czy LOC się zgadza z `cloc` / waszym `loc_counter.py`?
- Czy złożoność się zgadza z `radon`?
- Co SonarQube znalazł, czego wasze skrypty nie złapały?

### Zadanie 3: Własny Quality Gate (30 min) — dla ambitnych

Domyślny Quality Gate SonarQube to "Sonar way". Stwórzcie własny z ostrzejszymi progami.

**Do zrobienia:**

1. Wejdź w Quality Gates (menu górne)
2. Stwórz nowy Quality Gate o nazwie "ORKiPO strict"
3. Dodaj warunki, np.:
   - Reliability Rating is worse than A
   - Security Rating is worse than A
   - Maintainability Rating is worse than B
   - Duplicated Lines (%) is greater than 5%
   - Coverage is less than 60% (jeśli masz testy)
4. Przypisz go do swojego projektu
5. Sprawdź czy projekt przechodzi nowy Quality Gate

## Co oddajecie

W swoim branchu `lab08_nazwisko1_nazwisko2`:

1. **`answers.md`** — odpowiedzi na pytania z zadania 2 (ze screenshotami lub skopiowanymi metrykami)
2. **`docker-compose.yml`** — konfiguracja SonarQube (może być kopia z repozytorium z ewentualnymi modyfikacjami)
3. *(opcjonalnie)* opis własnego Quality Gate z zadania 3

## Kryteria oceny

- SonarQube uruchomiony i analiza przeprowadzona (potwierdzenie w answers.md)
- Odpowiedzi na wszystkie 6 grup pytań z zadania 2
- Podane konkretne liczby (nie "dużo bugów", tylko "17 bugów, rating C")
- Porównanie z wynikami z poprzednich labów
- Wnioski: co SonarQube dodaje ponad ręczne skrypty

## FAQ

**P: SonarQube nie startuje / pada z "out of memory".**
O: SonarQube potrzebuje min. 2 GB dla siebie. Sprawdź `docker stats`. Zamknij niepotrzebne programy. Na Linuxie może być potrzebne: `sudo sysctl -w vm.max_map_count=524288`.

**P: Sonar-scanner nie może się połączyć z SonarQube.**
O: Sprawdź czy SonarQube działa: `curl http://localhost:9000/api/system/status`. Scanner łączy się przez sieć Dockera (hostname `sonarqube`, nie `localhost`). Upewnij się, że oba kontenery są w tej samej sieci Docker.

**P: Analiza trwa bardzo długo.**
O: Dla dużych projektów (django) to normalne (5-10 min). requests powinien być szybki (1-2 min). Jeśli trwa >15 min, sprawdź logi: `docker compose logs sonar-scanner`.

**P: SonarQube pokazuje 0 bugów. Czy to normalne?**
O: Dla dobrze utrzymanego projektu jak requests — tak, to możliwe. Code smells powinny się pojawić. Jeśli naprawdę nic nie widać, sprawdź czy scanner przeanalizował właściwe pliki (logi scannera).

**P: Nie mogę postawić SonarQube na swoim laptopie (za mało RAM).**
O: Pracuj z partnerem/partnerką na mocniejszej maszynie. Alternatywa: użyj SonarCloud (lab09) — nie wymaga lokalnej instalacji.

## Przydatne linki

- [SonarQube documentation](https://docs.sonarsource.com/sonarqube/latest/)
- [SonarQube Docker image](https://hub.docker.com/_/sonarqube)
- [SonarScanner documentation](https://docs.sonarsource.com/sonarqube/latest/analyzing-source-code/scanners/sonarscanner/)
- [Quality Gates](https://docs.sonarsource.com/sonarqube/latest/user-guide/quality-gates/)
- [Python analysis rules](https://rules.sonarsource.com/python/)

---
*"SonarQube to jak wizyta u dentysty — wiesz, że musisz iść, nie chcesz znać wyników, ale potem jest lepiej."* — anonimowy developer (źródło: trust me bro)
