### Zadanie 2

1. **Reliability (Bugs):**
   - Ile bugów znalazł SonarQube? 
   
     Odp. Znalazł **4 bugi** (screenshot 1.1).


     <img src="screenshots/1.1-bugs.jpg" alt="screenshot 1.1" width="200" height="150">
   - Jaki rating (A-E)? 
   
     Odp. Reliability overall code rating to **E**.

     <img src="screenshots/1.2-raiting.jpg" alt="screenshot 1.2" width="200" height="150">
   - Podaj przykład jednego buga — co to jest i dlaczego SonarQube go flaguje? 
   
     Odp. Błąd widoczny na poniższym screenshocie jest flagowany, ponieważ `<iframe>` bez atrybutu title jest **niezgodny z zasadami dostępności** (accessibility). Jest to tylko **minor bug**.

     <img src="screenshots/1.3-bug-example.jpg" alt="screenshot 1.3" width="480" height="270">

2. **Security (Vulnerabilities):**
   - Ile vulnerabilities? 
   
     Odp. **0 vulnerablilities** (screenshot 1.1).
   - Jaki rating? 
     
     Odp. **A**
     
     <img src="screenshots/2-security.jpg" alt="screenshot 2" width="200" height="150">
   - Czy któraś jest poważna (Critical/Blocker)? 
   
     Odp. **Nie**, bo nie ma żadnej.

3. **Maintainability (Code Smells):**
   - Ile code smells? 
   
     Odp. **93** (screenshot 1.1)
   - Jaki rating? 
   
     Odp. Maintainability overll code rating to **A**.

     <img src="screenshots/3-maintainability.jpg" alt="screenshot 3" width="200" height="150">   
   - Jakie są top 5 najczęstszych typów code smell? 
   
     Odp. Jeśli typy code smell to `tags` -> 5 najczęstszych to: **convension, brain-overload, suspicious, cwe i unused**.

     <img src="screenshots/3.3.2-codesmell.jpg" alt="screenshot 3.3.2" width="200" height="150"> 
     
     A jeśli chodziło o `clean code atribute` -> są to **consistency, intentionality i adaptability**.

     <img src="screenshots/3.3-codesmell.jpg" alt="screenshot 3.3" width="200" height="150"> 

4. **Duplikacje:**
   - Jaki % kodu to duplikaty? 
   
     Odp. **0.4%** kodu to duplikaty.

     <img src="screenshots/4.1-duplicates.jpg" alt="screenshot 4.1" width="200" height="150">
   - Który plik ma najwyższy % duplikacji? 
   
     Odp. `test_requests.py`.

     <img src="screenshots/4.2.jpg" alt="screenshot 4.2" width="480" height="270">

5. **Metryki:**
   - Ile łącznie LOC? 
   
     Odp. **LOC = 3 959**
     
     <img src="screenshots/5.1-loc.jpg" alt="screenshot 5.1" width="200" >
   - Jaka jest średnia złożoność cyklomatyczna? 
   
     Odp. **Sumaryczne CC = 495**, zatem:
     
     - średnie CC na plik to: **495/15 = 33**
     - średnie CC na funkcję to: **495/439 = 1.13**

     <img src="screenshots/5.2-cc.jpg" alt="screenshot 5.2" width="200">
   - Ile plików ma CC > 20? 
   
     Odp. W folderze `tests\` - 3 pliki, `tests\testserver\` - 1 plik, `src\requests` - 6 plików. Czyli w sumie **10 plików**.

6. **Ogólna ocena:**
   - Czy projekt "przechodzi" domyślny Quality Gate? 
   
     Odp. **Tak**.

     <img src="screenshots/6.1.jpg" alt="screenshot 6.1" width="300">
   - Jak oceniasz jakość tego projektu na podstawie wyników? 
   
     Odp. Projekt jest **bezpieczny** (security OK) i ma **mało duplikacji**, ale jednocześnie jest **trudny w utrzymaniu** i **potencjalnie nieczytelny**. Mogą w nim też pojawiać się potencjalne **problemy z niezawodnością** (4 bugs + E rating).

### Porównanie wyników SonarQube z tymi, które zostały otrzymane na wcześniejszych laboratoriach:

 - Czy LOC się zgadza z `cloc`/`loc_counter.py`?
   - SonarQube: **3 959**
   - `cloc` (tylko SLOC): **7 039**
   - `loc_counter.py` (tylko SLOC): **7 420**

   Nie, bo SonarQube raportuje znacznie mniej linii kodu niż `cloc` i `loc_counter.py`.
   
   Wiąże się to z faktem, że stosuje on własne reguły analizy kodu źródłowego. 
   
   **Pomija przy tym część plików uznanych za testowe, pomocnicze lub niespełniających kryteriów „produkcyjnego kodu”.**
   
   Dodatkowo SonarQube liczy przede wszystkim analizowalne linie kodu (ang. **executable lines**), a nie wszystkie fizyczne linie SLOC.

`cloc` oraz własny skrypt `loc_counter.py` zliczają praktycznie wszystkie niepuste linie kodu w plikach źródłowych, dlatego wyniki są wyższe.

 - Czy złożoność się zgadza z `radon`?
   - SonarQube: avg. CC = **1.13 (A)**
   - `radon`: avg. CC =  **2.84 (A)**

   Średnia złożoność cyklomatyczna raportowana przez SonarQube jest niższa niż wynik uzyskany przez `radon`, choć obie klasyfikują się jako (**A**).
   
   Przyczyną jest to, że poróœnywane narzędzia stosują różne definicje i sposoby oblicznia CC.

   Radon analizuje złożoność na poziomie funkcji i metod, uwzględniając większość instrukcji sterujących w Pythonie.
   
   SonarQube natomiast stosuje własny model complexity oraz często agreguje wyniki inaczej, np. pomijając część prostych konstrukcji lub inaczej traktując elementy składni języka.

 - Co SonarQube znalazł, czego wasze skrypty nie złapały?

   SonarQube wykrył problemy, których wcześniejsze skrypty nie analizowały między innymi:
      - **duplikacje kodu**,
      - **potencjalne błędy** (bugs),
      - **code smells** (oznaki problematycznego lub trudnego w utrzymaniu kodu), w tym nieużywane (**unused**) fragmenty


### Zadanie 3

Zostało wykonane, jak widać na poniższych screenshotach:


<img src="screenshots/zad3.1.jpg" alt="screenshot zad3.1" width="500">


<img src="screenshots/zad3.2.jpg" alt="screenshot zad3.2" width="200">
 
Nowy, bardziej restrykcyjny Quality Gate `failed`.

<img src="screenshots/zad3.3.jpg" alt="screenshot zad3.3" width="400">
