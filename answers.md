
## 1.Reliability (Bugs):
- ile: 0
- rating: A
- przykład: -

## 2. Security (Vulnerabilities):
- ile: 0
- rating: A
- critical/blocker: 0

## 3. Maintainability (Code Smells):
- ile: 35
- rating: A
- top 5 najczęstszych typów code smell:
    - brain-overload: 14
        np. "Refactor this function to reduce its Cognitive Complexity from 23 to the 15 allowed"
    - convention: 6
        np. rename function "SOCKSProxyManager" to match the regular expression ^[a-z_][a-z0-9_]*$.
    - cwe: 5 
        Complete the task associated to this "TODO" comment.
    - clumsy: 3
        Merge this if statement with the enclosing one.
    - unused: 3
        np. Remove the unused function parameter "poolmanager".

## 4. Duplikacje
- 0%
- jaki plik ma najwyższy % duplikacji: -

## 5. Metryki
- liczba linii kodu (LOC): ok. 3600
- całkowita złożoność cyklomatyczna: 785
- liczba analizowanych plików: 19
- średnia złożoność CC: ok. 41
- liczba plików o CC > 20: 6

## 6. Ogólna ocena
- czy przechodzi domyślny Quality Gate: Tak
- jakość projektu na podstawie tego wyniku: Projekt jest aktualnie w bardzo dobrym stanie. Brak bugów/vulnerabilities. Jedyne code smells.