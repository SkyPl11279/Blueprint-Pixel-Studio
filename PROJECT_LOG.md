# Dziennik projektu — Blueprint Pixel Studio

## Cel projektu

Lokalne narzędzie bez serwera do tworzenia pionowych blueprintów do **Scrap Mechanic** z obrazu lub wklejonego kodu JSON. Użytkownik ma otrzymać kompletny folder blueprintu, gotowy do umieszczenia w katalogu `Blueprints` gry.

## Ustalenia z rozmowy

- Użytkownik nie wymaga konkretnej formy aplikacji (strona czy program Windows), ale wymaga pracy w pełni lokalnej, bez uruchamiania serwera.
- Wybrana forma: pojedyncza strona HTML otwierana lokalnie w przeglądarce. Nie ma zależności, instalacji ani połączeń sieciowych.
- Obraz jest zawsze blueprintem **pionowym**, o grubości jednego bloku. Szerokość leży na osi `X`, wysokość na osi `Z`, a głębokość (`Y`) ma grubość 1.
- Użytkownik chce wybierać: Beton 1, Drewno 1, Szkło 1 albo Metal 1.
- Użytkownik chce niezależnie ustawiać szerokość i wysokość w blokach; mniejszy rozmiar daje bardziej rozpikselizowany efekt, większy — dokładniejszy.
- Użytkownik dostarczył obraz palety malowania: 40 kolorów (siatka 10 × 4).
- Użytkownik dostarczył przykładowy folder blueprintu `18ce9d3d-62a0-4dc7-8575-2f2e9a0da7e9`, zawierający po 41 bloków każdego z czterech materiałów (40 kolorów + niepomalowany blok). Z niego odczytano poprawne identyfikatory kształtów.
- Użytkownik chce dwa wejścia: wczytanie obrazu oraz wklejenie pełnego JSON-a blueprintu. Wklejony JSON ma natychmiast dać podgląd pikselowy i umożliwić pobranie blueprintu.
- Użytkownik wkleił bardzo duży przykładowy JSON blueprintu. Jest to prawidłowy JSON Scrap Mechanic z `bodies[0].childs`, blokami 1×1×1, pozycjami `pos` i kolorami sześciocyfrowymi hex.

## Format Blueprintu Scrap Mechanic — zweryfikowany na danych użytkownika

- `blueprint.json` ma postać `{"bodies":[{"childs":[...]}],"version":4}`.
- Pojedynczy blok ma pola `bounds`, `color`, `pos`, `shapeId`, `xaxis` i `zaxis`.
- Dla pionowego obrazu generator zapisuje blok jako `bounds: {x:1,y:1,z:1}`, `pos: {x: kolumna, y: 0, z: wysokość - 1 - wiersz}`, `xaxis:1`, `zaxis:3`.
- Eksport ma tworzyć archiwum ZIP, zawierające jeden folder o nowym UUID oraz `blueprint.json`, `description.json` i `icon.png`. Po wypakowaniu użytkownik kopiuje folder do katalogu blueprintów gry.

## Identyfikatory materiałów poziomu 1

| Materiał | `shapeId` |
|---|---|
| Metal 1 | `8aedf6c2-94e1-4506-89d4-a0227c552f1e` |
| Drewno 1 | `df953d9c-234f-4ac2-af5e-f0490b223e71` |
| Beton 1 | `a6c6ce30-dd47-4587-b475-085d55c6a3b4` |
| Szkło 1 | `5f41af56-df4c-4837-9b3c-10781335757f` |

## Zmiany

### 2026-10-14 — opcje kolorów i proporcje podziału

- Nazwy wyboru materiału uproszczono do: Metal, Drewno, Beton i Szkło.
- Domyślnym trybem kolorów jest teraz „Zachowaj kolory obrazu”. Do wyboru pozostają pełna paleta 40 kolorów gry oraz „Własne kolory z palety gry”.
- W trybie własnym widoczna jest siatka 40 kolorów; wszystkie są początkowo zaznaczone, a użytkownik może odznaczyć dowolne pozycje. Konwersja dobiera najbliższy z pozostawionych kolorów.
- Dodano „Zachowaj proporcje podziału”. Gdy jest zaznaczone, zmiana liczby kolumn automatycznie przelicza wiersze i odwrotnie. Po odznaczeniu oba pola można zmieniać niezależnie.
- Test: `node --check app.js` zakończony powodzeniem.

### 2026-10-14 — limit części 50 000 bloków

- Na prośbę użytkownika limit ostrzegawczy i blokada eksportu pojedynczej części zostały zmienione z 62 500 na 50 000 bloków.

### 2026-10-14 — większy podział i pionowa nawigacja

- Limit pól podziału zwiększono do 5000 kolumn i 5000 wierszy; usunięto wcześniejszy limit 50 wszystkich części.
- Przyciski lewo/prawo zachowują dotychczasowe działanie: przechodzą do poprzedniej/następnej części w kolejności numeracji.
- Dodano `↑ Część` i `↓ Część`: przechodzą o jeden wiersz w górę lub w dół, zachowując tę samą kolumnę; na skraju przechodzą odpowiednio do ostatniego/pierwszego wiersza tej kolumny.
- Test: `node --check app.js` zakończony powodzeniem; potwierdzono obecność limitów 5000 i czterech przycisków nawigacji.

### 2026-10-14 — korekta podglądu i nawigacji części

- Usunięto kadrowanie oraz przycisk „Pełny obraz”, ponieważ funkcja nie działała niezawodnie i użytkownik wskazał, że może kadrować poza aplikacją.
- Przy podstawowym zoomie 100% canvas otrzymuje klasę dopasowania do panelu, więc cały obraz jest widoczny bez przewijania. Przewijanie podglądu jest potrzebne dopiero po zwiększeniu zoomu.
- Dodano przyciski „← Część” i „Część →” do pewnego przechodzenia między fragmentami podzielonego obrazu. Kliknięcie części na podglądzie pozostaje dostępne; obsługa kliknięcia nie jest już blokowana przez tryb kadrowania.
- Limit pojedynczej eksportowanej części zmieniono z ostrożnościowych 10 000 na 62 500 bloków, zgodnie z ustaleniem z rozmowy. Nadal nie jest to gwarancja, że gra postawi każdą konstrukcję o tej wielkości.
- Naprawiono regresję podglądu: obraz jest najpierw próbkowany do wybranej szerokości i wysokości bloków, a dopiero potem powiększany z wyłączonym wygładzaniem. Niższy wymiar ponownie pokazuje faktycznie rozpikselizowany rezultat.
- Testy: `node --check app.js` zakończony powodzeniem; statyczna weryfikacja potwierdziła kod próbkowania i limit 62 500 (`PIXEL_PREVIEW_AND_LIMIT_OK`).

### 2026-10-14 — kadrowanie, zoom i eksport części

- Zmieniono opis w nagłówku na informację o tworzeniu pionowych blueprintów z obrazów i JSON-a.
- Skrócono opis eksportu do „Eksport pobiera plik ZIP.”
- Dodano podpowiedzi po najechaniu na opcje pomijania przezroczystych pikseli oraz zachowania materiałów z JSON-a.
- Maksymalny rozmiar wejściowy zwiększono z 500 × 500 do 5000 × 5000 bloków. Sam większy rozmiar nie gwarantuje możliwości postawienia konstrukcji w grze.
- Dodano podział na kolumny i wiersze (maksymalnie 50 części), zaznaczanie części kliknięciem na podglądzie oraz eksport tylko aktualnie wybranej części. Nazwa eksportowanej części zawiera jej numer.
- Dodano licznik bloków wybranej części w formacie `liczba / 10 000`, ostrzeżenie powyżej progu i blokadę eksportu części ponad 10 000 bloków. Jest to bezpieczny limit aplikacji wynikający z problemów gry z bardzo dużymi konstrukcjami; nie jest deklarowany jako oficjalny limit Scrap Mechanic.
- Dodano przyciski kadrowania obrazu, przywracania pełnego kadru i przybliżania podglądu (od 100% do 400%). Kadrowanie działa tylko dla obrazu, nie dla wczytanego JSON-a.
- Zachowano działanie offline, format `blueprint.json`, `description.json`, `icon.png` oraz bezbiblioteczny eksport ZIP.
- Testy: `node --check app.js` przeszedł bez błędów; test generatora ZIP w pamięci potwierdził prawidłową sygnaturę archiwum (`ZIP_OK`).

### 2026-10-14 — poprawne mapowanie materiałów

- Użytkownik dostarczył blueprint testowy `0ae8274a-6fad-46b9-8a65-d8677dcc2d68` z czterema materiałami w potwierdzonej kolejności: beton, drewno, szkło, metal; drugi zestaw miał kolory rozróżniające: niebieski, zielony, żółty, czerwony.
- Odczyt z kolorowego zestawu potwierdził: beton = `a6c6ce30-dd47-4587-b475-085d55c6a3b4`, drewno = `df953d9c-234f-4ac2-af5e-f0490b223e71`, szkło = `5f41af56-df4c-4837-9b3c-10781335757f`, metal = `8aedf6c2-94e1-4506-89d4-a0227c552f1e`.
- W `app.js` poprawiono zamienione wcześniej identyfikatory betonu i metalu. Drewno oraz szkło nie wymagały zmiany.
- Test: `node --check app.js` zakończony powodzeniem; stała `MATERIALS` zawiera zweryfikowane identyfikatory.

### 2026-10-14 — naprawa eksportu ZIP

- Zgłoszenie użytkownika: kliknięcie „Pobierz blueprint ZIP” wyświetlało błąd `Assignment to constant variable.`
- Przyczyna: w `zip()` licznik przesunięcia danych ZIP (`offset`) został zadeklarowany jako `const`, mimo że w każdej iteracji pętli jest zwiększany.
- Poprawka: `offset` jest teraz zmienną `let`; format i lista plików w ZIP pozostały bez zmian.
- Test składni: `node --check app.js` zakończony powodzeniem.
- Test generatora ZIP: wygenerowano w pamięci archiwum z dwoma plikami; potwierdzono sygnaturę ZIP oraz obecność obu ścieżek (`ZIP_OK`, 228 bajtów).
- Automatyczny klik w lokalnym interfejsie nie był możliwy, ponieważ izolowane narzędzie przeglądarkowe nie ma dostępu do adresów `file:///`. Nie użyto obejścia ani serwera, zgodnie z wymaganiem działania lokalnego bez serwera.

### 2026-10-14 — wersja początkowa lokalnej aplikacji

Utworzono trzy pliki aplikacji:

- `index.html` — interfejs: wybór obrazu, wklejanie JSON-a, ustawienia wymiarów, materiału i kolorów, podgląd oraz eksport.
- `style.css` — responsywny, ciemny interfejs bez zewnętrznych zasobów.
- `app.js` — cała logika działająca lokalnie w przeglądarce.

Zaimplementowane funkcje:

1. Wczytywanie PNG, JPG, WEBP i GIF z komputera użytkownika; plik nie opuszcza urządzenia.
2. Skalowanie obrazu metodą najbliższego sąsiada (piksel = blok) do wybranego rozmiaru, z opcją zachowania proporcji.
3. Automatyczne dopasowanie kolorów obrazu do 40 kolorów gry metodą najmniejszej odległości RGB oraz opcja zachowania oryginalnych kolorów hex.
4. Pomijanie przezroczystych pikseli.
5. Wczytywanie standardowego JSON-a blueprintu Scrap Mechanic, odczytywanie jego układu na osiach X/Z i przedstawianie go jako obraz z siatką.
6. Dla JSON-a: opcja pozostawienia oryginalnych materiałów albo zastąpienia ich aktualnie wybranym materiałem.
7. Generowanie nowego `blueprint.json`, `description.json`, podglądowej `icon.png` oraz bezbibliotecznego archiwum ZIP z folderem UUID.
8. Dołączono przycisk przykładowego obrazu do szybkiego sprawdzenia aplikacji.

## Testy do wykonania / status

- [x] Sprawdzono składnię JavaScript poleceniem `node --check app.js` — zakończone bez błędów.
- [ ] Otworzyć stronę lokalnie i sprawdzić import przykładowego obrazu oraz eksport ZIP.
- [ ] Sprawdzić, czy wygenerowany ZIP zawiera oczekiwane trzy pliki i prawidłowy JSON.
- [ ] Test w samej grze Scrap Mechanic pozostaje do wykonania przez użytkownika, ponieważ tylko użytkownik ma dostęp do lokalnej instalacji gry.

### 2026-10-14 — test techniczny po utworzeniu aplikacji

- Potwierdzono poprawność parsowania pliku `app.js` przez środowisko Node.js (`node --check`).
- Próba automatycznego otwarcia lokalnego pliku HTML w izolowanej przeglądarce testowej została zablokowana przez jej politykę bezpieczeństwa (przeglądarka testowa dopuszcza wyłącznie adresy `http` i `https`, a projekt nie może uruchamiać serwera). Nie stosowano obejścia, ponieważ wymóg użytkownika wyraźnie zabrania serwera.
- Nie wykonywano jeszcze testu interakcyjnego w zwykłej przeglądarce ani testu importu do gry. Kolejny etap powinien otworzyć `index.html` lokalnie, uruchomić przycisk „Wczytaj przykład”, pobrać ZIP oraz sprawdzić jego zawartość.

## Instrukcja kontynuacji dla przyszłego czatu

1. Najpierw przeczytaj ten plik oraz pliki `index.html`, `style.css`, `app.js`.
2. Zachowuj wymóg działania offline: nie dodawaj CDN, API ani procesu serwera bez jednoznacznej zgody użytkownika.
3. Po każdej zmianie zaktualizuj sekcję **Zmiany** tego pliku, wskazując co zmieniono, dlaczego i jak sprawdzono efekt.
4. Nie nadpisuj oryginalnego przykładowego blueprintu użytkownika w folderze `18ce9d3d-62a0-4dc7-8575-2f2e9a0da7e9`.
