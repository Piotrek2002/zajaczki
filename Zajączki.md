# Lepsza wersja

### Algorytm Zarządzania Imprezami w Lesie dla Zajączków i Niedźwiedzi

#### Struktury i zmienne:
- **`WaitQueue`:** Kolejka procesów oczekujących na ACK, zapisująca żądania dostępu do każdej z polan, początkowo pusta.
- **`AvailableSpaces`:** Tablica zawierająca dostępną liczbę miejsc na każdej polanie, początkowo każda polana ma `S` miejsc.
- **`AlcoholRequirements`:** Tablica boolowska określająca, czy dla danej polany jest wymagany dodatkowy alkohol, początkowo `false` dla każdej polany.
- **`AckNum`:** Tablica liczników otrzymanych potwierdzeń ACK dla każdego żądania dostępu, początkowo 0.
- **`n`:** Całkowita liczba procesów (`Z` zajączków + `N` niedźwiedzi).

#### Wiadomości:
- **`REQ`:** Żądanie dostępu do polany, zawiera identyfikator polany, liczbę potrzebnych miejsc oraz flagę zapotrzebowania na dodatkowy alkohol.
- **`ACK`:** Potwierdzenie zgody na dostęp, wysyłane po przetworzeniu żądania REQ.
- **`RELEASE`:** Informacja o zwolnieniu miejsca na polanie, wysyłana po zakończeniu imprezy.

#### Stany:
- **`REST`:** Proces nie ubiega się o dostęp, gotowy do rozważenia nowego żądania.
- **`WAIT`:** Proces oczekuje na zgody od wszystkich pozostałych procesów, aby wejść na polanę.
- **`INSECTION`:** Proces znajduje się na polanie, uczestniczy w imprezie.

#### Szkic algorytmu:
1. **Inicjalizacja:**
    - Wszystkie procesy startują w stanie `REST` z odpowiednio zainicjalizowanymi zmiennymi.

2. **Generowanie żądania dostępu:**
    - Proces w stanie `REST` decyduje o zorganizowaniu imprezy, generuje `REQ` z informacjami o wybranej polanie, potrzebnej liczbie miejsc i zapotrzebowaniu na alkohol. Następnie wysyła `REQ` do wszystkich procesów i przechodzi do stanu `WAIT`.

3. **Odbiór żądania i wysyłanie ACK:**
    - Procesy odbierające `REQ` porównują dostępne miejsca i wymagania alkoholowe z żądaniem. Jeśli mogą zaakceptować żądanie (dostępne miejsca i spełnione wymagania alkoholowe), wysyłają `ACK` natychmiast. W przeciwnym razie żądanie jest dodawane do `WaitQueue`.

4. **Wejście na polanę (`INSECTION`):**
    - Proces w stanie `WAIT` monitoruje `AckNum`. Gdy liczba otrzymanych `ACK` osiągnie `n-1`, proces aktualizuje `AvailableSpaces` i `AlcoholRequirements`, wchodzi na polanę i przechodzi do stanu `INSECTION`.

5. **Zarządzanie imprezą:**
    - Proces w `INSECTION` uczestniczy w imprezie. Po zakończeniu wysyła `RELEASE` do wszystkich procesów, informując o zwolnieniu miejsca i przechodzi z powrotem do stanu `REST`.

6. **Przetwarzanie kolejnych żądań:**
    - Procesy odbierające `RELEASE` aktualizują `AvailableSpaces` i przetwarzają żądania z `WaitQueue`, które teraz mogą być zaakceptowane, wysyłając `ACK` tam, gdzie to możliwe.

Ten algorytm zapewnia sprawiedliwy dostęp do zasobów oraz koordynuje udział zajączków i niedźwiedzi w imprezach, jednocześnie dbając o odpowiednie zasoby (miejsca i alkohol), potrzebne

### Algorytm Zarządzania Imprezami w Lesie tylko dla Zajączków (bez niedźwiedzi)

#### Struktury i zmienne:
- **`WaitQueue`:** Kolejka procesów oczekujących na ACK dla każdej polany, początkowo pusta.
- **`AvailableSpaces`:** Liczba dostępnych miejsc na każdej polanie, początkowo każda polana ma `S` miejsc.
- **`AckNum`:** Licznik liczby otrzymanych potwierdzeń ACK dla każdego żądania dostępu, początkowo 0.
- **`n`:** Całkowita liczba procesów (tylko zajączki).

#### Wiadomości:
- **`REQ`:** Żądanie dostępu do polany, zawiera identyfikator polany.
- **`ACK`:** Potwierdzenie zgody na dostęp, wysyłane po przetworzeniu żądania REQ.
- **`RELEASE`:** Informacja o zwolnieniu miejsca na polanie, wysyłana po zakończeniu imprezy.

#### Stany:
- **`REST`:** Stan początkowy, proces nie ubiega się o dostęp do polany.
- **`WAIT`:** Proces oczekuje na zgody od wszystkich pozostałych procesów, aby wejść na polanę.
- **`INSECTION`:** Proces znajduje się na polanie, uczestniczy w imprezie.

#### Szkic algorytmu:
1. **Inicjalizacja:**
    - Wszystkie zajączki zaczynają w stanie `REST` z odpowiednio zainicjalizowanymi zmiennymi.

2. **Generowanie żądania dostępu:**
    - Zajączek w stanie `REST` decyduje o zorganizowaniu imprezy, generuje `REQ` z informacjami o wybranej polanie, wysyła `REQ` do wszystkich innych zajączków i przechodzi do stanu `WAIT`.

3. **Odbiór żądania i wysyłanie ACK:**
    - Zajączki odbierające `REQ` porównują dostępne miejsca z żądaniem. Jeśli na żądanej polanie są dostępne miejsca, wysyłają `ACK` natychmiast. W przeciwnym razie żądanie jest dodawane do `WaitQueue`.

4. **Wejście na polanę (`INSECTION`):**
    - Zajączek w stanie `WAIT` monitoruje `AckNum`. Gdy liczba otrzymanych `ACK` osiągnie `n-1`, proces aktualizuje `AvailableSpaces`, wchodzi na polanę i przechodzi do stanu `INSECTION`.

5. **Zarządzanie imprezą:**
    - Zajączek w `INSECTION` uczestniczy w imprezie. Po zakończeniu wysyła `RELEASE` do wszystkich innych zajączków, informując o zwolnieniu miejsca i przechodzi z powrotem do stanu `REST`.

6. **Przetwarzanie kolejnych żądań:**
    - Zajączki odbierające `RELEASE` aktualizują `AvailableSpaces` i przetwarzają żądania z `WaitQueue`, które teraz mogą być zaakceptowane, wysyłając `ACK` tam, gdzie to możliwe.

#### Uproszczenia:
- Brak niedźwiedzi eliminuje potrzebę zarządzania dodatkowymi zasadami dotyczącymi alkoholu i większego zajmowania przestrzeni, co upraszcza logikę decyzyjną i zarządzanie zasobami.
- Zajączki samodzielnie zarządzają wszystkimi aspektami imprez, co skupia całą odpowiedzialność i kontrolę na jednym typie procesu.