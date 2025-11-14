# Kompilacja Process Hacker

Poniższe instrukcje opisują sposób zbudowania Process Hacker 2 w środowisku Windows.

## Wymagania wstępne

- **System operacyjny:** Windows 10 lub nowszy (wersja 64-bitowa). Proces budowy wymaga narzędzi, które są dostępne tylko w systemie Windows.
- **Visual Studio 2022** z następującymi składnikami:
  - Workload **Desktop development with C++** (Rozwój klasycznych aplikacji z użyciem C++).
  - Komponent **MSVC v143 - VS 2022 C++ x64/x86 build tools**.
  - **Windows 10 SDK** lub nowszy (w wersji zgodnej z Twoim systemem).
- **Git** do pobrania repozytorium (opcjonalnie można pobrać archiwum ZIP).

## Pobranie kodu źródłowego

```powershell
# sklonuj repozytorium
git clone https://github.com/processhacker/processhacker.git
cd processhacker
```

Możesz również pobrać paczkę ZIP z kodem źródłowym z GitHuba i rozpakować ją w wybranym katalogu.

## Konfiguracja podprojektów

Repozytorium zawiera kilka podprojektów wymagających wstępnej konfiguracji:

1. Uruchom skrypt `tools\SyncDependencies.bat`, aby pobrać prekompilowane zależności (biblioteki PDB, symbole i nagłówki) używane przez główne projekty.
2. Jeśli planujesz pracę z wtyczkami, otwórz rozwiązanie `plugins\Plugins.sln` i upewnij się, że wszystkie projekty ładują się poprawnie.

## Kompilacja w Visual Studio

1. Otwórz plik rozwiązania `ProcessHacker.sln` w Visual Studio.
2. Wybierz odpowiednią konfigurację i platformę z górnego paska narzędzi:
   - Konfiguracja: `Release` lub `Debug`.
   - Platforma: `x86` (32-bit) lub `x64` (64-bit).
3. W menu `Build` wybierz `Build Solution` (`Ctrl+Shift+B`). Visual Studio zbuduje wszystkie projekty zależne, w tym sterownik KProcessHacker.
4. Po zakończeniu procesu wyniki znajdziesz w katalogach `build\bin\<platforma>\<konfiguracja>`.

## Podpisywanie sterownika

Sterownik KProcessHacker wymaga podpisu cyfrowego, aby można go było zainstalować w Windows z włączonym wymuszaniem podpisów sterowników.

- Do celów testowych możesz uruchomić system w trybie testowym (`bcdedit /set testsigning on`).
- Dla dystrybucji produkcyjnej wymagany jest podpis EV (Extended Validation) oraz przesłanie sterownika do usługi Microsoft WHQL.

## Testowanie uruchomienia

1. Zainstaluj sterownik `KProcessHacker3` za pomocą dostarczonego skryptu `build\bin\drivers\KProcessHacker3.inf` (kliknij prawym przyciskiem myszy i wybierz **Install**) lub użyj `pnputil`.
2. Uruchom `ProcessHacker.exe` z katalogu wyjściowego. Jeśli uruchamiasz go po raz pierwszy, zalecane jest uruchomienie jako administrator, aby uzyskać pełną funkcjonalność.

## Rozwiązywanie problemów

- Jeśli kompilacja zakończy się błędami dotyczących brakujących nagłówków, upewnij się, że zainstalowałeś Windows SDK oraz że skrypt `SyncDependencies.bat` zakończył się powodzeniem.
- W przypadku błędów linkera sprawdź, czy Visual Studio używa prawidłowego zestawu narzędzi (Platform Toolset `v143`). Możesz to zmienić w właściwościach projektu (`Project > Properties > General > Platform Toolset`).
- Jeśli sterownik nie instaluje się z powodu braku podpisu, uruchom system w trybie testowym lub podpisz sterownik certyfikatem testowym.

## Automatyzacja

Do budowy z wiersza poleceń możesz użyć następującego polecenia (uruchomionego w "Developer Command Prompt for VS 2022"):

```cmd
msbuild ProcessHacker.sln /t:Build /p:Configuration=Release /p:Platform=x64
```

Możesz utworzyć skrypt `.cmd`, który uruchomi powyższe polecenie po wcześniejszym zsynchronizowaniu zależności.

