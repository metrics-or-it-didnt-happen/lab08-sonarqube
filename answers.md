Analizowano repozytorium: https://github.com/encode/httpx.git
1. **Reliability (Bugs):**
   - Ile bugów znalazł SonarQube? 13
   - Jaki rating (A-E)? E
   - Podaj przykład jednego buga - co to jest i dlaczego SonarQube go flaguje? \
    `Replace this expression with an iterable object.Why is this an issue?` Odpalana jest pętla for na obiekcie który nie jest iterable.
2. **Security (Vulnerabilities):**
   - Ile vulnerabilities? 311
   - Jaki rating? E
   - Czy któraś jest poważna (Critical/Blocker)? TAK \
    W wielu testach hasła/url podane są na sztywno co system wykrywa jako niebezpieczne, jednak w testach jest to dozwolone.

3. **Maintainability (Code Smells):**
   - Ile code smells? 54
   - Jaki rating? A
   - Jakie są top 5 najczęstszych typów code smell? 
        * kod bez pokrycia testami
        * funkcja z 14 parametrami

4. **Duplikacje:**
   - Jaki % kodu to duplikaty?   2.1%
   - Który plik ma najwyższy % duplikacji?   `tests/client/test_cookies.py`

5. **Metryki:**
   - Ile łącznie LOC?  12,252
   - Jaka jest średnia złożoność cyklomatyczna?  1,870
   - Ile plików ma CC > 20? 7

6. **Ogólna ocena:**
   - Czy projekt "przechodzi" domyślny Quality Gate? TAK
   - Jak oceniasz jakość tego projektu na podstawie wyników? \
   Poprawność oceniam na bardzo wysoką. Znaczna większość problemów znajduje się w plikach testów.