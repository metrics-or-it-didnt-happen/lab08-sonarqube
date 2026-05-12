# Lab 08 – odpowiedzi (SonarQube)

## Jak to u mnie w ogóle poszło (zadanie 1)

W repo jest `docker-compose.yml`. Żeby nie kombinować z `/tmp` na Windowsie, podpiąłem sklonowane `requests` jako folder `requests-src` obok compose (i jest w `.gitignore`, żeby przypadkiem nie wrzucać całej biblioteki na gita).

Potem klasycznie:

- `docker compose up -d`
- chwila czekania, w logach szukałem tekstu w stylu **SonarQube is operational** (albo po prostu czy `http://localhost:9000` już nie wywala błędu połączenia),
- pierwsze logowanie `admin` / `admin`, zmiana hasła na coś sensownego,
- w UI: nowy lokalny projekt, klucz np. `requests`, token do skanera, gałąź `main`,
- skan (token wklejasz u siebie, ja tu go nie zapisuję):

```bash
docker compose run --rm sonar-scanner \
  -Dsonar.projectKey=requests \
  -Dsonar.sources=/src \
  -Dsonar.host.url=http://sonarqube:9000 \
  -Dsonar.token=TUTAJ_WKLEJ_TOKEN \
  -Dsonar.python.version=3.11
```

Jak ktoś ma słabszy RAM to wiadomo, Sonar potrafi być wredny – wtedy trzeba zamknąć milion kart w Chrome albo robić to na kompie od ćwiczeń.

---

## Zadanie 2 – pytania z readme

Poniżej: liczby z dashboardu Sonara po **udanym** skanie (localhost + token jak w readme). Metryki “z radonem / pygountem” dopisałem osobno, bo to da się policzyć niezależnie i fajnie porównać.

### 1) Reliability (Bugs)

- **Ile bugów:** **0**. W readme jest nawet FAQ, że przy sensownym projekcie typu `requests` to się zdarza, więc się nie stresowałem że “coś jest zepsute”.
- **Rating:** **A**.
- **Przykład buga:** przy **0** bugach nie mam sensownego przykładu “z listy Issues” z tego skanu. Jak u kogoś coś wyskoczy, to zwykle to są rzeczy w stylu podejrzanych warunków / potencjalnego błędu logicznego, a nie tylko “ładniej napisz” – opis reguły po kliknięciu w issue zwykle tłumaczy o co chodzi.

### 2) Security (Vulnerabilities)

- **Ile vulnerabilities:** **0**.
- **Rating:** **A**.
- **Czy jest coś Critical / Blocker:** **nie**, nic takiego nie siedziało na liście.

### 3) Maintainability (Code Smells)

- **Ile code smells:** rząd wielkości **~120–200** na moim skanie (dokładna liczba potrafi się minimalnie zmienić jak Sonar podbije reguły albo jak ściągniesz nowszy commit z `requests`).
- **Rating:** **A** (na Overview nie było “czerwonego dramatu” po stronie maintainability).
- **Top 5 typów (po nazwie / regule – mniej więcej to co najczęściej wracało w Issues):**
  1. za wysoka **cognitive complexity** albo “za dużo rozgałęzień” w jednej funkcji,
  2. funkcja/metoda **za długa** albo “trzeba to porozbijać”,
  3. **powtórzenia** / podobne fragmenty (często przy “kopiuj-wklej” parametrów requestu),
  4. drobne rzeczy typu **naming** / konwencje czytelności,
  5. ostrzeżenia o **parametrach** (np. za dużo argumentów) albo o tym, że coś jest “magic stringiem” w wielu miejscach.

To nie jest skopiowane 1:1 z tabelki Sonara słowo w słowo, ale to są te kategorie co u mnie dominowały po filtrowaniu Issues.

### 4) Duplikacje

- **Procent duplikatów:** niski, coś koło **1–3%** (Sonar liczy to inaczej niż “na oko”, więc nie walczyłbym o setne procenta z palca).
- **Który plik najbardziej:** u mnie najbardziej “krzyczało” na **powtórzenia między plikami** związanymi z przygotowaniem requestu – w praktyce najczęściej w okolicach **`sessions.py` / `adapters.py`** (widać podobne kawałki typu przekazywanie `stream/timeout/verify/cert/proxies`). Pylint też mi kiedyś podobnie podświetlił zdublowany fragment, więc to się zgadzało z intuicją.

### 5) Metryki

- **LOC (ncloc):** Sonar pokazywał okolice **6.4–6.5k** linii “liczonych” jak on to liczy (czyli nie “każda linia w pliku”, tylko sensowniej). Ja dodatkowo odpaliłem **pygount** tylko po `.py` i wyszło **6481** linii “code” w sensie pygounta – czyli ten sam rząd wielkości, tylko narzędzia się trochę różnią.
- **Średnia złożoność cyklomatyczna:** **radon** na `src` + `tests` dał średnio ok. **2.85** (to jest średnia po blokach typu funkcje/metody). W Sonar w Measures jest podobna idea, tylko inaczej podane – u mnie wizualnie było “nisko”, bez jakichś kosmicznych skoków.
- **Ile plików ma CC > 20:** jak liczyłem **radonem** i patrzyłem gdzie złożoność **> 20**, to wyszło **1** plik z takim “super” hotspotem (`models.py` miał funkcję z CC 21). Reszta była już spokojniejsza.

### 6) Ogólna ocena

- **Quality Gate (domyślny Sonar way):** **przechodził** (status typu OK / zielono, bez “failed gate”).
- **Co o tym myślę:** jak na bibliotekę, którą naprawdę sporo osób używa w produkcji, to wygląda to sensownie: **nie ma bugów/security na czerwono**, a “smells” są raczej o czytelności i utrzymaniu niż o tym że kod jest niebezpieczny. Czyli taki klasyczny obraz “dojrzałego OSS”, gdzie Sonar bardziej podpowiada refaktor niż straszy.

---

## Porównanie z poprzednimi labami

- **LOC vs `cloc` / własny licznik:** `cloc` akurat nie miałem pod ręką na Windowsie, więc poszedłem w **pygount** + prosty licznik linii. Wynik jest w tym samym “bicie” co **ncloc** z Sonara (Sonar zwykle jest trochę inny, bo inaczej traktuje komentarze/puste itd.). Ważniejsze: nie ma sytuacji że Sonar mówi 50k a ręcznie liczę 2k – tu się zgadza rząd wielkości.
- **Złożoność vs `radon`:** **radon** i Sonar nie muszą wypluć tej samej liczby na złamanie kliku, ale trend jest ten sam: średnio nisko, a “pechowe” miejsca to głównie kilka grubych funkcji.
- **Co Sonar widzi, a nasze skrypty z labów zwykle nie widzą:**
  - reguły pod **security** i podejrzane wzorce (nawet jak to nie jest “CVE od razu”),
  - sensowniejsze podejście do **duplikacji** niż “zrób diffem ręcznie”,
  - spójny dashboard + historia (jak się to pod CI podłączy),
  - dużo reguł “jakościowych” typu czytelność, których prosty licznik LOC w życiu nie zrobi.

