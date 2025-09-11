Matura informatyka maj 2025
Zadanie 7.1 - Podaj nazwę obszaru, na którym znaleziono łącznie we wszystkich pomiarach najwięcej m3
wody na głębokości do 100 metrów włącznie. Jest jeden taki obszar.

W skrócie: szukamy jednego obszaru, który ma największą sumę ilości wody z pomiarów o głębokości ≤ 100 m.

Wskazówki:

KOD SQL:
SELECT TOP 1 o.nazwa_obszaru,
       SUM(p.ilosc) AS suma_wody_m3
-----------------------------------------------------------------------------------------------------------
SELECT – wybieramy kolumny, które chcemy zobaczyć w wyniku.

TOP 1 – ograniczamy wynik do jednego rekordu (najlepszego po sortowaniu).

o.nazwa_obszaru – nazwa obszaru z tabeli obszary.

SUM(p.ilosc) – sumujemy ilość wody z tabeli pomiary.

AS suma_wody_m3 – nadajemy kolumnie wynikowej czytelną nazwę.

KOD SQL:
FROM obszary AS o
INNER JOIN pomiary AS p
    ON p.kod_obszaru = o.kod_obszaru
-----------------------------------------------------------------------------------------------------------
FROM – określa tabelę główną (obszary).

INNER JOIN – łączy wiersze z obu tabel, gdy kod_obszaru jest taki sam.

Dzięki temu wiemy, które pomiary należą do którego obszaru.

KOD SQL:
WHERE p.glebokosc <= 100
WHERE – filtruje dane, zostawiając tylko pomiary do 100 m głębokości.
-----------------------------------------------------------------------------------------------------------
KOD SQL:
GROUP BY o.nazwa_obszaru
-----------------------------------------------------------------------------------------------------------
GROUP BY – grupuje dane według obszaru, aby można było policzyć sumę wody dla każdego.

KOD SQL:
ORDER BY SUM(p.ilosc) DESC;
-----------------------------------------------------------------------------------------------------------
ORDER BY – sortuje wyniki malejąco (DESC) według sumy wody.

Dzięki temu obszar z największą ilością wody będzie na górze.


Zadanie 7.2 - Podaj nazwę łazika, który wykonywał pomiary w najdłuższym okresie, licząc od pierwszego
(najwcześniejszego) do ostatniego (najpóźniejszego) pomiaru. Podaj datę pierwszego
i ostatniego pomiaru wykonanego przez ten łazik.

W skrócie:
Szukamy jednego łazika, który miał najdłuższy odstęp czasu między pierwszym a ostatnim pomiarem. Dodatkowo chcemy zobaczyć daty graniczne tych pomiarów.

KOD SQL:
SELECT TOP 1 l.nazwa_lazika,
       MIN(p.data_pomiaru) AS pierwszy_pomiar,
       MAX(p.data_pomiaru) AS ostatni_pomiar,
       DATEDIFF('d', MIN(p.data_pomiaru), MAX(p.data_pomiaru)) AS liczba_dni
-----------------------------------------------------------------------------------------------------------
SELECT – wybieramy kolumny, które mają się pojawić w wyniku.

TOP 1 – ograniczamy wynik do jednego łazika (tego z najdłuższym okresem).

l.nazwa_lazika – nazwa łazika z tabeli laziki.

MIN(p.data_pomiaru) – najwcześniejsza data pomiaru dla danego łazika.

MAX(p.data_pomiaru) – najpóźniejsza data pomiaru dla danego łazika.

DATEDIFF('d', MIN, MAX) – oblicza liczbę dni między pierwszym a ostatnim pomiarem.

AS ... – nadajemy kolumnom czytelne nazwy w wynikach.


KOD SQL:
FROM laziki AS l
INNER JOIN pomiary AS p ON l.nr_lazika = p.nr_lazika
FROM – określa tabelę główną (laziki).
-----------------------------------------------------------------------------------------------------------

INNER JOIN – łączy dane z tabeli pomiary, dopasowując je po numerze łazika (nr_lazika).

Dzięki temu wiemy, które pomiary należą do którego łazika.

KOD SQL:
GROUP BY l.nazwa_lazika
GROUP BY – grupuje dane według nazwy łazika.
-----------------------------------------------------------------------------------------------------------

Dzięki temu możemy obliczyć MIN, MAX i DATEDIFF dla każdego łazika osobno.

KOD SQL:
ORDER BY DATEDIFF('d', MIN(p.data_pomiaru), MAX(p.data_pomiaru)) DESC;
ORDER BY – sortuje wyniki malejąco (DESC) według liczby dni między pomiarami.
-----------------------------------------------------------------------------------------------------------

Dzięki temu łazik z najdłuższym okresem pomiarowym będzie na górze.

TOP 1 wybierze tylko jego.



Zadanie 7.3 - Podaj nazwy obszarów na Marsie, na których żaden z łazików nie wykonał ani jednego
pomiaru w tym samym roku, w którym został wysłany z Ziemi.

W skrócie:
Szukamy obszarów, które nie zostały zbadane przez łaziki w roku ich wysłania. Czyli: jeśli łazik został wysłany w 2059, a zrobił pomiar w 2059 — taki obszar odpada. Zostają tylko te, gdzie nie było żadnego pomiaru w roku wysłania.

Wskazówki:
KOD SQL:
SELECT o.nazwa_obszaru
SELECT – wybieramy tylko nazwę obszaru, bo to nas interesuje.
-----------------------------------------------------------------------------------------------------------

o.nazwa_obszaru – kolumna z tabeli obszary, zawierająca nazwę każdego obszaru.

KOD SQL:
FROM obszary AS o
WHERE o.kod_obszaru NOT IN (
    SELECT DISTINCT p.kod_obszaru
    FROM pomiary AS p
    INNER JOIN laziki AS l ON p.nr_lazika = l.nr_lazika
    WHERE YEAR(p.data_pomiaru) = l.rok_wyslania
)
-----------------------------------------------------------------------------------------------------------

FROM obszary AS o – główna tabela, z której wybieramy obszary.

WHERE ... NOT IN (...) – filtrujemy obszary, które nie znajdują się w wyniku podzapytania.

Podzapytanie:

SELECT DISTINCT p.kod_obszaru – wybiera unikalne kody obszarów, w których był pomiar.

INNER JOIN laziki – łączy pomiary z łazikami, żeby znać rok wysłania.

WHERE YEAR(p.data_pomiaru) = l.rok_wyslania – wybiera tylko te pomiary, które były wykonane w roku wysłania łazika.

Czyli: podzapytanie zwraca obszary, które były badane w roku wysłania — a główne zapytanie odrzuca je.

KOD SQL:
ORDER BY o.nazwa_obszaru;
-----------------------------------------------------------------------------------------------------------

ORDER BY – sortuje wyniki alfabetycznie według nazwy obszaru.

Dzięki temu wynik jest czytelny i uporządkowany.




Zadanie 7.4 - Podaj nazwy łazików, które wylądowały na półkuli południowej, ale wykonywały pomiary na
obu półkulach: północnej (N) i południowej (S).

W skrócie:
Szukamy łazików, które:

wylądowały na południu (czyli ich współrzędne lądowania zawierają literę „S”),

ale później wykonywały pomiary zarówno na północy („N”) jak i południu („S”).

Wskazówki:
KOD SQL:
SELECT l.nazwa_lazika
SELECT – wybieramy tylko nazwę łazika.

l.nazwa_lazika – kolumna z tabeli Laziki, zawierająca nazwę każdego łazika.

KOD SQL:
FROM Laziki AS l
INNER JOIN Pomiary AS p ON l.nr_lazika = p.nr_lazika
FROM Laziki AS l – główna tabela z danymi o łazikach.
-----------------------------------------------------------------------------------------------------------

INNER JOIN Pomiary AS p – łączymy z tabelą pomiarów, żeby wiedzieć, gdzie łazik dokonywał pomiarów.

ON l.nr_lazika = p.nr_lazika – łączenie po numerze łazika.

KOD SQL:
WHERE l.wsp_ladowania LIKE "*S*"
WHERE – filtruje łaziki, które wylądowały na południowej półkuli.
-----------------------------------------------------------------------------------------------------------

LIKE "S"** – sprawdza, czy współrzędne lądowania zawierają literę „S”.

KOD SQL:
sql
GROUP BY l.nazwa_lazika
GROUP BY – grupuje dane według łazika, żeby można było analizować jego pomiary zbiorczo.
-----------------------------------------------------------------------------------------------------------

KOD SQL:
HAVING
    SUM(IIF(p.wspolrzedne LIKE "*N*", 1, 0)) > 0
    AND SUM(IIF(p.wspolrzedne LIKE "*S*", 1, 0)) > 0;
-----------------------------------------------------------------------------------------------------------

HAVING – warunek po grupowaniu, działa podobnie jak WHERE, ale dla agregatów.

SUM(IIF(...)) > 0 – zliczamy pomiary na półkuli północnej i południowej:

IIF(p.wspolrzedne LIKE "N", 1, 0)** – jeśli pomiar był na północy, dodaj 1.

IIF(p.wspolrzedne LIKE "S", 1, 0)** – jeśli pomiar był na południu, dodaj 1.

AND – łazik musi mieć pomiary na obu półkulach.



Zadanie 7.5 - Do tabel utworzonych na podstawie opisanych wcześniej plików dołączamy kolejną –
o nazwie Producent, w której zapisano informacje o producentach poszczególnych modeli
łazików.

W skrócie:
Szukamy producentów, których łaziki:
wykonywały pomiary w obszarze Arcadia, i robiły to w roku 2060. Wynik ma zawierać unikalne nazwy producentów — bez powtórzeń.

Wskazówki:

KOD SQL:
SELECT DISTINCT p.nazwa
-----------------------------------------------------------------------------------------------------------

SELECT – wybieramy kolumnę nazwa z tabeli producent.

DISTINCT – usuwa duplikaty, żeby każdy producent pojawił się tylko raz.

p.nazwa – nazwa producenta łazika.


KOD SQL:
FROM producent AS p
INNER JOIN laziki AS l
    ON p.kod_producenta = l.kod_producenta
-----------------------------------------------------------------------------------------------------------

FROM producent AS p – główna tabela z informacjami o producentach.

INNER JOIN laziki AS l – łączymy z tabelą laziki, żeby wiedzieć, który producent stworzył który łazik.

ON p.kod_producenta = l.kod_producenta – łączenie po kodzie producenta.


KOD SQL:

INNER JOIN pomiary AS pm
    ON l.nr_lazika = pm.nr_lazika
-----------------------------------------------------------------------------------------------------------

Łączymy łaziki z ich pomiarami.

Dzięki temu wiemy, gdzie i kiedy dany łazik wykonywał pomiary.


KOD SQL:
INNER JOIN obszary AS o
    ON pm.kod_obszaru = o.kod_obszaru
-----------------------------------------------------------------------------------------------------------

Łączymy pomiary z tabelą obszary, żeby znać nazwę obszaru, w którym wykonano pomiar.

Dzięki temu możemy sprawdzić, czy pomiar był w Arcadii.


KOD SQL:
WHERE o.nazwa_obszaru = "Arcadia"
  AND YEAR(pm.data_pomiaru) = 2060;
-----------------------------------------------------------------------------------------------------------

WHERE – filtruje dane według dwóch warunków:

o.nazwa_obszaru = "Arcadia" – tylko pomiary w obszarze Arcadia.
YEAR(pm.data_pomiaru) = 2060 – tylko pomiary wykonane w roku 2060.


PODSUMOWANIE: 
Każde zapytanie analizuje dane z różnych tabel, łącząc je przez wspólne klucze i filtrując według konkretnych warunków. Wykorzystujemy funkcje agregujące, warunki logiczne i sortowanie, aby wyłonić rekordy spełniające określone kryteria.

Łączymy tabele za pomocą INNER JOIN, aby uzyskać pełny kontekst danych.

Używamy WHERE, GROUP BY, HAVING i ORDER BY, by zawęzić, pogrupować i uporządkować wyniki.

Funkcje takie jak SUM, MIN, MAX, DATEDIFF, IIF i YEAR pozwalają na obliczenia i warunki czasowe.

TOP 1 i DISTINCT pomagają wybrać najważniejsze lub unikalne rekordy.

Każde zapytanie odpowiada na konkretne pytanie badawcze — czy to o ilość wody, zakres pomiarów, aktywność łazików, czy producentów sprzętu — i pokazuje, jak SQL może precyzyjnie przeszukiwać dane, by znaleźć to, co najistotniejsze.