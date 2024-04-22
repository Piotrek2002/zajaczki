### Algorytm Zarządzania Imprezami w Lesie dla Zajączków i Niedźwiedzi

#### Struktury i zmienne:
- **`WaitQueue`:** Kolejka procesów oczekujących na ACK dla każdej polany, początkowo pusta.
- **`AvailableSpaces`:** Liczba dostępnych miejsc na każdej polanie, początkowo każda polana ma `S` miejsc.
- **`AlcoholRequirements`:** Informacja o tym, czy dla danej polany wymagany jest dodatkowy alkohol, początkowo `false` dla każdej polany.
- **`AckNum`:** Licznik liczby otrzymanych potwierdzeń ACK dla każdego żądania dostępu, początkowo 0.
- **`n`:** Całkowita liczba procesów (`Z` zajączków + `N` niedźwiedzi).

#### Wiadomości:
- **`REQ`:** Żądanie dostępu do polany, zawiera identyfikator polany, liczbę potrzebnych miejsc oraz flagę zapotrzebowania na dodatkowy alkohol.
- **`ACK`:** Potwierdzenie zgody na dostęp, wysyłane po przetworzeniu żądania REQ.
- **`RELEASE`:** Informacja o zwolnieniu miejsca na polanie, wysyłana po zakończeniu imprezy.

#### Stany:
- **`REST`:** Stan początkowy, proces nie ubiega się o dostęp do polany.
- **`WAIT`:** Proces oczekuje na zgody od wszystkich pozostałych procesów, aby wejść na polanę.
- **`INSECTION`:** Proces znajduje się na polanie, uczestniczy w imprezie.

#### Szkic algorytmu:
1. **Inicjalizacja:**
    - Procesy startują w stanie `REST` z odpowiednio zainicjalizowanymi zmiennymi.

2. **Generowanie żądania dostępu:**
    - Proces w stanie `REST` decyduje o zorganizowaniu imprezy, generuje `REQ` z informacjami o wybranej polanie, potrzebnej liczbie miejsc (1 dla zajączka, 4 dla niedźwiedzia) i zapotrzebowaniu na alkohol (true jeśli niedźwiedź jest inicjatorem), następnie wysyła `REQ` do wszystkich procesów i przechodzi do stanu `WAIT`.

3. **Odbiór żądania i wysyłanie ACK:**
    - Procesy odbierające `REQ` aktualizują swoje informacje. Jeżeli na żądanej polanie jest wystarczająco miejsca i alkohol, wysyłają `ACK`. Jeśli nie, żądanie jest dodawane do `WaitQueue`.

4. **Wejście na polanę (`INSECTION`):**
    - Proces w stanie `WAIT` monitoruje `AckNum`. Gdy liczba otrzymanych `ACK` osiągnie `n-1`, proces aktualizuje `AvailableSpaces` i `AlcoholRequirements`, wchodzi na polanę i przechodzi do stanu `INSECTION`.

5. **Zarządzanie imprezą:**
    - Proces w `INSECTION` uczestniczy w imprezie. Po zakończeniu, wysyła `RELEASE` do wszystkich procesów, informując o zwolnieniu miejsca i przechodzi z powrotem

do stanu `REST`.

6. **Przetwarzanie kolejnych żądań:**
    - Procesy odbierające `RELEASE` aktualizują `AvailableSpaces` i `AlcoholRequirements` i przetwarzają żądania z `WaitQueue`, które teraz mogą być zaakceptowane, wysyłając `ACK` tam, gdzie to możliwe.