### Algorytm Zarządzania Dostępem do Polan dla Zajączków i Niedźwiedzi

#### Struktury i zmienne:
1. **`WaitQueue`:** Kolejka procesów oczekujących na ACK dla każdej polany, początkowo pusta.
2. **`AckNum`:** Licznik liczby otrzymanych potwierdzeń ACK, początkowo ustawiony na 0 dla każdej polany.
3. **`n`:** Łączna liczba procesów (zajączków + niedźwiedzi).

#### Wiadomości:
- Wszystkie wiadomości są opatrzone znacznikiem czasowym, modyfikowanym zgodnie z zasadami zegara Lamporta.
    - **`REQ`:** Żądanie dostępu do polany. Zawiera informacje o żądanej polanie, priorytet żądania, liczbę miejsc, które proces zamierza zająć, oraz flagę czy proces to niedźwiedź.
    - **`ACK`:** Potwierdzenie, że inny proces przyjął żądanie dostępu.

#### Stany:
1. **`REST`:** Stan początkowy, proces nie ubiega się o dostęp do polany.
2. **`WAIT`:** Stan oczekiwania na dostęp do polany.
3. **`INSECTION`:** Stan przebywania na polanie.

#### Szkic algorytmu:
1. **Generowanie żądania (REQ):**
    - Proces decyduje o wejściu do sekcji krytycznej (polany) i wysyła żądanie `REQ` do wszystkich procesów, ustawiając `AckNum` na zero. Zegar Lamporta jest aktualizowany przy każdym wysłaniu i odbiorze wiadomości.

2. **Odbieranie żądania (REQ) i wysyłanie ACK:**
    - Procesy odbierające `REQ` aktualizują swój zegar Lamporta i porównują priorytet otrzymanego żądania z priorytetem własnych żądań o dostęp do tej samej polany.
    - Jeśli priorytet żądania nadawcy jest większy (niższy numer zegara), odsyłają `ACK` od razu. W przeciwnym razie dodają żądanie do `WaitQueue` i odsyłają `ACK` po wyjściu z polany.

3. **Przejście do stanu INSECTION:**
    - Proces w stanie `WAIT` monitoruje licznik `AckNum`. Gdy równa się \( n - 1 \) (wszystkie ACK zostały otrzymane), proces wchodzi na polanę i przechodzi do stanu `INSECTION`.

4. **Zarządzanie sekcją krytyczną (INSECTION):**
    - Proces przebywa na polanie. Po zakończeniu, wysyła wiadomości `RELEASE` do wszystkich procesów, sygnalizując opuszczenie polany. Żądania z `WaitQueue` są przetwarzane po opuszczeniu polany.

5. **Powrót do stanu REST:**
    - Po opuszczeniu sekcji krytycznej proces powraca do stanu `REST`, gotowy do rozpatrzenia kolejnych żądań.

#### Reakcje na wiadomości:
- **Na REQ:**
    - Zwraca `ACK`, jeśli otrzymuje żądanie z mniejszym priorytetem.
    - Buforuje żądanie w `WaitQueue`, jeśli ma większy priorytet.
- **Na ACK:**
    - Inkrementuje `AckNum`. Jeśli licznik równa się \( n - 1 \), proces wchodzi do sekcji.

Ten algorytm rozdziela zasoby (polany) między zajączki i niedźwiedzie, gdzie zajączki są odpowiedzialne za zarządzanie dostępem i dodatkowym alkoholem dla niedźwiedzi, z zachowaniem uczciwości i efektywności dzięki zastosowaniu zegara Lamporta i kolejkowania żą

dań.

### Uproszczony Algorytm Zarządzania Dostępem do Polan dla Zajączków (bez niedźwiedzi)

W uproszczonej wersji algorytmu, wszystkie złożone mechanizmy związane z zarządzaniem większym zapotrzebowaniem na miejsce i dodatkowym alkoholem dla niedźwiedzi są eliminowane, skupiamy się wyłącznie na zarządzaniu dostępem zajączków do polan.

#### Struktury i zmienne:
1. **`WaitQueue`:** Kolejka procesów oczekujących na ACK dla każdej polany, początkowo pusta.
2. **`AckNum`:** Licznik liczby otrzymanych potwierdzeń ACK, początkowo ustawiony na 0 dla każdej polany.
3. **`n`:** Liczba procesów (tylko zajączki).

#### Wiadomości:
- Wszystkie wiadomości są opatrzone znacznikiem czasowym, modyfikowanym zgodnie z zasadami zegara Lamporta.
    - **`REQ`:** Żądanie dostępu do polany. Zawiera informacje o żądanej polanie i priorytet żądania.
    - **`ACK`:** Potwierdzenie, że inny proces przyjął żądanie dostępu.

#### Stany:
1. **`REST`:** Stan początkowy, proces nie ubiega się o dostęp do polany.
2. **`WAIT`:** Stan oczekiwania na dostęp do polany.
3. **`INSECTION`:** Stan przebywania na polanie.

#### Szkic algorytmu:
1. **Generowanie żądania (REQ):**
    - Proces w stanie `REST` decyduje o wejściu na polanę i wysyła żądanie `REQ` do wszystkich procesów, ustawiając `AckNum` na zero. Zegar Lamporta jest aktualizowany przy każdym wysłaniu i odbiorze wiadomości.

2. **Odbieranie żądania (REQ) i wysyłanie ACK:**
    - Procesy odbierające `REQ` aktualizują swój zegar Lamporta i porównują priorytet otrzymanego żądania z priorytetem własnych żądań o dostęp do tej samej polany.
    - Jeśli priorytet żądania nadawcy jest wyższy, odsyłają `ACK` od razu. W przeciwnym razie dodają żądanie do `WaitQueue` i odsyłają `ACK` po wyjściu z polany.

3. **Przejście do stanu INSECTION:**
    - Proces w stanie `WAIT` monitoruje licznik `AckNum`. Gdy równa się \( n - 1 \) (wszystkie ACK zostały otrzymane), proces wchodzi na polanę i przechodzi do stanu `INSECTION`.

4. **Zarządzanie sekcją krytyczną (INSECTION):**
    - Proces przebywa na polanie. Po zakończeniu, wysyła wiadomości `RELEASE` do wszystkich procesów, sygnalizując opuszczenie polany. Żądania z `WaitQueue` są przetwarzane po opuszczeniu polany.

5. **Powrót do stanu REST:**
    - Po opuszczeniu sekcji krytycznej proces powraca do stanu `REST`, gotowy do rozpatrzenia kolejnych żądań.

#### Reakcje na wiadomości:
- **Na REQ:**
    - Zwraca `ACK`, jeśli otrzymuje żądanie z mniejszym priorytetem.
    - Buforuje żądanie w `WaitQueue`, jeśli ma większy priorytet.
- **Na ACK:**
    - Inkrementuje `AckNum`. Jeśli licznik równa się \( n - 1 \), proces wchodzi do sekcji.

#### Uwagi końcowe:
Ten uproszczony algorytm jest efektywny w środowisku, gdzie tylko zajączki uczestniczą w zarządzaniu

dostępem do polan. Usunięcie niedźwiedzi z modelu znacznie upraszcza zarządzanie zasobami i pozwala skupić się na optymalizacji komunikacji i czasu odpowiedzi wśród zajączków. Zastosowanie zegara Lamporta nadal zapewnia sprawiedliwy dostęp do zasobów i minimalizuje ryzyko zakleszczeń oraz zagłodzenia.

