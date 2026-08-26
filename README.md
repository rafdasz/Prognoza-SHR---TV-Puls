# Prognoza Oglądalności TV Puls (Model XGBoost)

Repozytorium zawiera kod i architekturę modelu opartego na algorytmie XGBoost, służącego do prognozowania udziałów (SHR) stacji TV Puls w interwałach 15-minutowych.

## 📋 Instrukcja obsługi modelu

Postępuj zgodnie z poniższymi krokami, aby wygenerować prognozę na kolejny miesiąc. Wszystkie operacje na plikach muszą odbywać się w **folderze roboczym całego projektu**.

### Krok 1: Przygotowanie danych wejściowych
Wyeksportuj historyczne raporty z systemu i zapisz je w formacie `.csv` wewnątrz folderu `Input_data/` w głównym folderze roboczym:
* `ATV_hourly_TSV.csv`
* `Programmes Puls pg.csv` (raport programowy)
* `Time Puls dp 15m.csv` (raport z danymi 15-minutowymi)

**Ważne:** Upewnij się, że plik `Premiery.xlsx` (znajdujący się również w folderze `Input_data/`) został zaktualizowany o listę premier na nowy sezon.

### Krok 2: Konfiguracja parametrów
Przed uruchomieniem skryptu przejdź do pierwszego bloku kodu (Konfiguracja i Parametry). Zaktualizuj parametry odpowiadające za horyzont czasowy prognozy.
* Szczególną uwagę zwróć na zmienną `NEXT_MONTH_END` i ustaw ją na ostatni dzień miesiąca prognozowanego (np. `"2026-09-30 23:45:00"`).

### Krok 3: Generowanie szablonu predykcyjnego
Uruchom kod do momentu wygenerowania szablonu (zakończ na Bloku 7). Skrypt utworzy szkielet danych dla przyszłego miesiąca.
* Wygenerowany plik znajdziesz w ścieżce `Processed data/df_pred.xlsx`.

### Krok 4: Uzupełnienie ramówki (Krok manualny)
Otwórz plik szablonu i na podstawie planowanej ramówki TV Puls uzupełnij następujące kolumny:
* `Programme`
* `Type`
* `Duration`
* `ATV`

⚠️ **Krytyczne zasady mapowania:**
1. **Zgodność nazw:** Nazwy programów w kolumnie `Programme` muszą być w 100% zgodne z nazwami historycznymi (możesz je zweryfikować z plikiem `Processed data/data.xlsx`).
2. **Pasma nocne:** Programy emitowane w godzinach **02:00 – 06:00** zawsze muszą mieć wpisaną nazwę `ln` oraz długość `12`.
3. **Filmy:** Pozycje filmowe muszą mieć wpisaną nazwę `film` oraz długość `120`. Kolumnę `Type` uzupełnij analogiczną wartością dla danego programu.

Po wypełnieniu danych zapisz plik pod nazwą **`df_pred NEWDATA.xlsx`** i przenieś go do folderu `Input_data/`.

### Krok 5: Prognoza właściwa
Uruchom ostatni blok algorytmu (Autoregresyjna prognoza iteracyjna). Model wykorzysta dostarczoną ramówkę i zaktualizowany bufor historyczny do wyliczenia wyników.
* Ostateczny plik z gotową prognozą w interwale 15-minutowym zostanie wyeksportowany do folderu **`Output_data/`**.
