# RealTimeOperatingSystem

Implementation programs for uCOS

## Wersja polska [PL]

### Wprowadzenie

Celem programu jest wykorzystanie podstawowych mechanizmów i funkcji API występujących w systemach czasu rzeczywistego. 

RTOS - Real Time Operating System to system, który odpowiada w sposób przewidywalny na nieprzewidywalne zewnętrzne pobudzenie przy dowolnym obciążeniu. 
Pisząc program należy przestrzegać warunków tj.: granice czasowe, równoległość, przewidywalność i zależność.

### Realizacja

Przeprowadzono analizę działania pod kątem zmienności obciążenia procesora, ilości aktywnych zadań oraz przełączanych zadań w zależności od wartości obciążenia przy użyciu procesora Intel(R)Core(™) i3-3110M CPU@ 2.40GHz.

Wykres 1. przedstawia zależność obciążenia procesora od zadanej wartości obciążenia. Maksymalna wartość zużycia CPU - 100%, została osiągnięta dla wartości obciążenia równej 85 000. Między wartością obciążenia zakresu 250 do 50 000 wartość zużycia CPU gwałtownie wzrastała. W zakresie obciążenia 50 000 do 85 000 zmiana wartości zużycia CPU nie była już tak szybka. Po przekroczeniu wartości obciążenia 85 000 wartość zużycia CPU pozostaje stała - 100%.  

Wykres 2. przedstawia zależność liczby przełączanych zadań od zadanej wartości obciążenia. Przy minimalnym obciążeniu ilość przełączanych zadań wynosi 16 652. Podczas zwiększania obciążenia do 100 000 liczba przełączanych zadań maleje dwukrotnie. Szybki spadek liczby przełączanych zadań utrzymuje się do wartości obciążenia równej 1 000 000. Po przekroczeniu wartości obciążenia równej 50 000 000 liczba przełączanych zadań oscyluje wokół wartości 670.  

Wykres 3. przedstawia zależność liczby zadań aktywnych od zadanej wartości obciążenia. Zmiana liczby zadań aktywnych nastąpiła dopiero przy obciążeniu 85 000, liczba zadań aktywnych wyniosła 21. Gwałtowny spadek liczby aktywnych zadań utrzymywał się do wartości obciążenia 2 500 000. Po osiągnięciu tej wartości obciążenia liczba zadań aktywnych zmieniała się bardzo wolno. 
	
Na podstawie wykresu 4. możemy zauważyć, że szybkie zmiany, wzrosty i spadki poszczególnych zależności występują w podobnym przedziale wartości obciążenia. Oś y jest przedstawiona w skali logarytmicznej, ze względu na bardzo dużą różnicę wartości dla poszczególnych zależności.


### Napotkane problemy, popełnione błędy i ich rozwiązanie

Pierwszym problemem z jakim miałyśmy doczynienia, było nieodpowiednie napisanie funkcji obliczającej deltę. Z początku użyłyśmy funkcji OSTimeDly(1), aby odczekać 1 sekundę i otrzymać kolejną wartość licznika, którego różnica miała dać nam poszukiwaną wartość. Oczywiście w tym przypadku delta zwróciła nam wartość 0. Następnie na stronie doc.micrium.com w zakładce API - Time Management znalazłyśmy przykładowe użycie funkcji OSTimeGet(), która pozwala na uzyskanie aktualnej wartości zegara systemowego:


Example Usage

```
void TaskX (void *p_arg){
   OS_TICK  clk;
   OS_ERR   err;
   while (DEF_ON) {
      clk = OSTimeGet(&err);  /* Get current value of system clock */
      /* Check “err” */
   }
}
```

Po otrzymaniu tej wartości wystarczyło podzielić ją przez częstotliwość pracy systemu 333 Hz, zadeklarowaną w pliku OS_CFG.c: OS_TICKS_PER_SEC 333 do otrzymania aktualnej sekundy.
Następny błąd jaki popełniłyśmy to napisanie wykorzystanie tych funkcji w zadaniu Display(), zamiast dla poszczególnych: Mailbox(), Queue() i Semaphore().

### Podsumowanie

Podsumowując, program pozwolił na zapoznanie się z systemem uCOS, zrozumieć mechanizmy występujące w systemach operacyjnych czasu rzeczywistego (RTOS). Należało odpowiednio zinterpretować zależność wartości obciążenia od ilości aktywnych zadań, liczby przełączanych zadań oraz obciążenia CPU procesora.
Na podstawie wyżej dołączonych wykresów, zauważono, że szybkie zmiany, wzrosty i spadki poszczególnych zależności występują w podobnym przedziale wartości obciążenia. 

