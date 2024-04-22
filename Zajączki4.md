### Algorytm Zarządzania Imprezami w Lesie tylko dla Zajączków

#### Struktury i zmienne:
- **`WaitQueue`:** Kolejka procesów oczekujących na ACK dla każdej polany, początkowo pusta.
- **`AvailableSpaces`:** Liczba dostępnych miejsc na każdej polanie, początkowo każda polana ma `S` miejsc.
- **`AckNum`:** Licznik liczby otrzymanych potwierdzeń ACK dla każdego żądania dostępu, początkowo 0.
- **`n`:** Całkowita liczba zajączków.

#### Wiadomości:
- **`REQ`:** Żądanie dostępu do polany, zawiera identyfikator polany.
- **`ACK`:** Potwierdzenie zgody na dostęp, wysyłane po przetworzeniu żądania REQ.
- **`RELEASE`:** Informacja o zwolnieniu miejsca na polanie, wysyłana po zakończeniu imprezy.

#### Stany:
- **`REST`:** Stan początkowy, zajączek nie ubiega się o dostęp do polany.
- **`WAIT`:** Zajączek oczekuje na zgody od wszystkich pozostałych zajączków, aby wejść na polanę.
- **`INSECTION`:** Zajączek znajduje się na polanie, uczestniczy w imprezie.

#### Szkic algorytmu:
1. **Inicjalizacja:**
    - Wszystkie zajączki startują w stanie `REST` z odpowiednio zainicjalizowanymi zmiennymi.

2. **Generowanie żądania dostępu:**
    - Zajączek w stanie `REST` decyduje o zorganizowaniu imprezy, generuje `REQ` z informacjami o wybranej polanie, wysyła `REQ` do wszystkich innych zajączków i przechodzi do stanu `WAIT`.

3. **Odbiór żądania i wysyłanie ACK:**
    - Zajączki odbierające `REQ` porównują dostępne miejsca na polanie z żądaniem. Jeśli na żądanej polanie są dostępne miejsca, wysyłają `ACK` natychmiast. W przeciwnym razie żądanie jest dodawane do `WaitQueue`.

4. **Wejście na polanę (`INSECTION`):**
    - Zajączek w stanie `WAIT` monitoruje `AckNum`. Gdy liczba otrzymanych `ACK` osiągnie `n-1`, proces aktualizuje `AvailableSpaces`, wchodzi na polanę i przechodzi do stanu `INSECTION`.

5. **Zarządzanie imprezą:**
    - Zajączek w `INSECTION` uczestniczy w imprezie. Po zakończeniu, wysyła `RELEASE` do wszystkich innych zajączków, informując o zwolnieniu miejsca i przechodzi z powrotem do stanu `REST`.

6. **Przetwarzanie kolejnych żądań:**
    - Zajączki odbierające `RELEASE` aktualizują `AvailableSpaces` i przetwarzają żądania z `WaitQueue`, które teraz mogą być zaakceptowane, wysyłając `ACK` tam, gdzie to możliwe.

#### Uproszczenia:
- Brak niedźwiedzi oznacza, że nie ma potrzeby zarządzania dodatkowym alkoholem, co upraszcza logikę zarządzania zasobami i żądaniami.
- Algorytm skupia się tylko na logistyce miejsc, co pozwala na bardziej przewidywalne i prostsze zarządzanie zasobami i potrzebami zajączków.