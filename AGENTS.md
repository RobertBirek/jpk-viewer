
# AGENTS.md – JPK_MAG Przeglądarka

## Język

Odpowiadaj zawsze po polsku.

## O repozytorium

Pojedynczy plik `index.html` (CSS + JS inline) – przeglądarka plików JPK_MAG (XML schematu JPK_VAT z częścią magazynową). Aplikacja w pełni po stronie klienta – żadne dane nie są wysyłane na serwer.

## Struktura

```
index.html          → cała aplikacja (HTML, <style>, <script>)
vendor/
  fontawesome/      → ikony (CSS + czcionki)
  xlsx/             → SheetJS – eksport do XLSX
  sax/              → SAX parser – strumieniowe parsowanie dużych plików (>30 MB)
.gitignore
```

## Stack

- Czysty HTML5 + CSS3 + JavaScript (ES6+). Brak frameworków, brak narzędzi budujących.
- Brak `package.json`, npm, webpack, build step – aplikację otwiera się bezpośrednio w przeglądarce.
- Brak testów, lintera, formattera, typechecka, CI/CD.

## Jak uruchomić

Otwórz `index.html` w przeglądarce (wystarczy `file://`). Nie potrzeba serwera.

## Cechy aplikacji

- **Parsowanie XML** – obsługiwane dwa tryby:
  - Poniżej 30 MB: `DOMParser` (synchroniczny, pełne drzewo DOM)
  - Powyżej 30 MB: SAX parser strumieniowy (`vendor/sax/sax.min.js`) – czyta plik w 1 MB chunkach
- **IndexedDB** – dane są automatycznie zapisywane i przywracane po odświeżeniu strony (klucz `jpkData`)
- **Eksport XLSX** – używa SheetJS (`vendor/xlsx/xlsx.full.min.js`), osobny przycisk na każdym widoku + zbiorczy w sidebarze

## Obsługiwane sekcje JPK_MAG

| Sekcja | Nazwa w XML | Wyświetlane jako |
|--------|-------------|------------------|
| Nagłówek | `Naglowek` | Karty informacyjne |
| Podmiot | `Podmiot1` | Karty informacyjne |
| Magazyn | `Magazyn` | Pole w statystykach |
| PZ nagłówki | `PZWartosc` | Tabela z paginacją (100 wierszy/strona) |
| PZ pozycje | `PZWiersz` | Tabela z paginacją |
| WZ nagłówki | `WZWartosc` | Tabela z paginacją |
| WZ pozycje | `WZWiersz` | Tabela z paginacją |
| MM | `MMWartosc` + `MMWiersz` | Połączone w jeden widok |

## Uwagi dla dewelopera

- **Paginacja**: `PAGE_SIZE = 100` (stała na górze bloku `<script>`)
- **Sortowanie**: własna implementacja (funkcja `sortTable`), locale `pl`
- **Filtrowanie**: wyszukiwarka tekstowa + rozwijane listy dla dostawców/odbiorców/numerów dokumentów
- **Edycja kodu**: wszystkie zmiany w `index.html`. CSS wewnątrz `<style>`, JS wewnątrz `<script>`.
- **Brak zależności zewnętrznych** poza trzema vendored bibliotekami – nie są potrzebne `npm install` ani CDN.
- **Wtyczki do przeglądarki**: jeśli edytujesz kod, przetestuj otwierając `index.html` na nowo (Ctrl+F5 aby ominąć cache).
- **Git**: tylko gałąź `main`, prosta historia.
