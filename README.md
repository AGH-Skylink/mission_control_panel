# Mission Control – Umbrella Repository

To repozytorium zbiera w jednym miejscu wszystkie komponenty systemu Mission Control
jako submoduły Gita (tzw. *umbrella repo*).

Struktura katalogów (przykładowa):

```text
mission_control/
├─ manager/   → https://github.com/AGH-Skylink/mission_control_manager
├─ audio/     → https://github.com/AGH-Skylink/mission_control_audio
├─ server/    → https://github.com/AGH-Skylink/mission_control_server
└─ ui/        → https://github.com/AGH-Skylink/mission_control_ui
```

Każdy katalog jest **osobnym repozytorium** (submodule), ze swoją historią, remote’em
i workflowem. Umbrella repo tylko „zapamiętuje”, jaki commit każdego submodułu
jest używany w danej wersji całego systemu.

---

## 1. Pierwsza instalacja (dla nowych osób w projekcie)

### 1.1. Wymagania

- Git (zalecana konfiguracja z kluczem SSH do GitHuba)
- Python / Node / itd. – zgodnie z README w poszczególnych modułach

### 1.2. Klonowanie całego systemu (rekomendowane)

```bash
git clone --recurse-submodules https://github.com/AGH-Skylink/mission_control.git
cd mission_control
```

Flaga `--recurse-submodules` od razu dociąga zawartość `manager/`, `audio/`, `server/` i `ui/`.

### 1.3. Jeśli ktoś już sklonował repo bez submodułów

Jeżeli ktoś zrobił zwykłe:

```bash
git clone https://github.com/AGH-Skylink/mission_control.git
```

to potem wystarczy:

```bash
cd mission_control
git submodule update --init --recursive
```

Ta komenda:

- inicjuje submoduły,
- pobiera ich zawartość na commit wskazany przez umbrella repo.

---

## 2. Praca nad konkretnym projektem (submodule)

Każdy submodule to normalne repo Gita. Przykład dla **UI**.

### 2.1. Wchodzenie do modułu i praca jak zwykle

```bash
cd mission_control/ui

# normalne operacje
git status
git add .
git commit -m "Opis zmian"
git push
```

Repozytorium `ui/` nadal ma swój własny remote na GitHubie
(`AGH-Skylink/mission_control_ui`), więc pushujesz tam jak zawsze.

### 2.2. Aktualizacja refa submodułu w umbrella repo

Po zmianach w submodule (np. `ui/`):

```bash
cd mission_control/ui
# ... commit/push ...

cd ..                  # wracamy do umbrella
git status             # zobaczysz, że katalog ui ma zmieniony commit
git add ui
git commit -m "Update ui submodule to latest"
git push
```

Umbrella zapisze:  
> „W wersji X całego systemu, `ui` jest na commicie Y”.

To samo dotyczy `manager/`, `audio/`, `server/`.

---

## 3. Odświeżanie submodułów do najnowszych commitów

Jeśli ktoś inny zaktualizował submoduły i zrobił `git push` w umbrella repo:

```bash
git pull
git submodule update --init --recursive
```

Dzięki temu wszystkie moduły wskoczą na commity zapisane w aktualnym stanie umbrella.

---

## 4. Krótki workflow „end-to-end”

1. **Pobierasz cały projekt**:

   ```bash
   git clone --recurse-submodules git@github.com:AGH-Skylink/mission_control.git
   cd mission_control
   ```

2. **Pracujesz w swoim module** (np. audio):

   ```bash
   cd audio
   git checkout -b feature/new-magic
   # ... kod ...
   git commit -m "Add new magic"
   git push
   ```

3. **Aktualizujesz umbrella**:

   ```bash
   cd ..
   git add audio
   git commit -m "Update audio submodule to feature/new-magic"
   git push
   ```

4. Inne osoby robią:

   ```bash
   git pull
   git submodule update --init --recursive
   ```

i mają tę samą wersję wszystkich modułów.

---

## 5. Przydatne komendy podsumowane

- Klonowanie z submodułami:

  ```bash
  git clone --recurse-submodules https://github.com/AGH-Skylink/mission_control.git
  ```

- Inicjalizacja / aktualizacja submodułów:

  ```bash
  git submodule update --init --recursive
  ```

- Wejście do modułu i praca jak w normalnym repo:

  ```bash
  cd ui          # lub manager / audio / server
  git status
  git add .
  git commit -m "..."
  git push
  ```

- Zapisanie nowej wersji submodułu w umbrella:

  ```bash
  cd ..
  git add ui     # lub manager / audio / server
  git commit -m "Update ui submodule"
  git push
  ```

---

Jeżeli ktoś się gubi w submodułach – najważniejsze zasady:

1. **Każdy folder (manager, audio, server, ui) to osobne repo Gita.**
2. Zawsze najpierw:
   - commit + push w submodule,
   - dopiero potem commit + push w umbrella repo.
3. Po `git pull` w umbrella **zawsze** warto zrobić:

   ```bash
   git submodule update --init --recursive
   ```

   żeby lokalne submoduły wskoczyły na właściwe commity.
