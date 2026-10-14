# Blueprint Pixel Studio

Prosty narzędzi do tworzenia blueprintów dla Scrap Mechanic na podstawie obrazów lub pliku JSON.

## Co robi aplikacja?

- wczytuje obraz PNG/JPG/WEBP/GIF
- konwertuje go na pionowy blueprint w rozmiarze blokowym
- pozwala ustawić szerokość, wysokość, materiał i kolory
- obsługuje podział na części
- eksportuje gotowy projekt jako ZIP z plikiem blueprint.json, description.json i ikoną

## Jak uruchomić lokalnie?

Najprościej:

1. Wejdź do folderu projektu.
2. Otwórz plik `index.html` w przeglądarce.
3. Możesz też uruchomić lokalny serwer:

```bash
python -m http.server 8000
```

Następnie otwórz:

```text
http://localhost:8000
```

## Jak wystawić stronę online?

To jest strona statyczna, więc najłatwiej użyć GitHub Pages.

Kroki:

1. Wejdź do repozytorium na GitHub.
2. Kliknij `Settings`.
3. Wybierz `Pages`.
4. W sekcji `Build and deployment` ustaw:
   - `Source`: `Deploy from a branch`
   - `Branch`: `master` (lub `main`)
   - `Folder`: `/root`
5. Zapisz.
6. GitHub wygeneruje link w stylu:

```text
https://twoja-nazwa-uzytkownika.github.io/Blueprint-Pixel-Studio/
```

> Ta aplikacja działa jako strona frontendowa i nie wymaga backendu ani serwera Node.js.

## Pliki projektu

- `index.html` – struktura strony
- `style.css` – wygląd interfejsu
- `app.js` – logika aplikacji
- `PROJECT_LOG.md` – notatki projektu

## Uwagi

- Aplikacja działa całkowicie po stronie klienta.
- Nie wysyła żadnych danych do zewnętrznego serwera.
- Do poprawnego działania online wystarczy GitHub Pages.
