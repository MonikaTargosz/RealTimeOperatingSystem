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

### Specyfikacja

Metoda obsługi wyświetlenia:
```
void  Wyswietl(void *pdata){

	struct struktura *wskazniknastruktura;
	struct struktura dane;
   	char   s[80];
	char *msg;
	unsigned long int liczniklokalny;
	unsigned long int idlokalne;

	INT8U  err;
	INT8U j=0;
	INT8U  x=0;
	INT8U  y=6;
	pdata = pdata;
	
    for(;;) {
		wskazniknastruktura = OSQPend(structure, 0, &err);
		dane=*wskazniknastruktura;
		msg=dane.key;
		sprintf(s, "%10lu", obciazenie); //wyowietla aktualnie dzia3aj1ce obci1?enie
		PC_DispStr(0, 4, s, DISP_FGND_BLACK + DISP_BGND_LIGHT_GRAY);
		idlokalne=dane.id;

		//odpowiada za aktualizowanie wyowietlania licznika

			if(idlokalne<6){			//do semaforów
				liczniklokalny=dane.licznik;
				sprintf(s, "%6ld", (liczniklokalny));
     	   			PC_DispStr(50 , (7+((idlokalne))*2), s, DISP_FGND_BLACK + DISP_BGND_LIGHT_GRAY);
			}

			else if(idlokalne>=6 && idlokalne<=11) //kolejek{
				liczniklokalny=dane.licznik;
				sprintf(s, "%6ld", (liczniklokalny));
     	    			PC_DispStr(60 , (-5+((idlokalne))*2), s, DISP_FGND_BLACK + DISP_BGND_LIGHT_GRAY);
			}

			else if(idlokalne>=12 && idlokalne<=17){  //mailboxów
				liczniklokalny=dane.licznik;
				sprintf(s, "%6ld", (liczniklokalny));
     	    			PC_DispStr(70 , (-17+((idlokalne))*2), s, DISP_FGND_BLACK + DISP_BGND_LIGHT_GRAY);
			}

		//obs3uguje wypisywanie wprowadzania obci1?enia

		if (*msg == 0x08){ //backspace 
			if (x!=0){ // czyszczenie nastepuje tylko gdy nie znajdujemy sie na krawedzi 
				x--;					
				PC_DispChar(x, y, 32, DISP_FGND_BLACK + DISP_BGND_LIGHT_GRAY);
			}
		}

		else if (*msg == 0x0D){ //enter
			for(j=0;j<10;j++) PC_DispChar(j, y, ' ', DISP_FGND_BLACK + DISP_BGND_LIGHT_GRAY);
				x=0;
			}
			
		else if(x<9 && *msg>47 && *msg<58){ //wypisuj znaki tylko z przedzi3u ASCII 47 --> 58 
						//ale równie? nie pozwól wpisaa wiecej ni? 9 znaków
			PC_DispChar(x, y, *msg, DISP_FGND_BLACK + DISP_BGND_LIGHT_GRAY);
			x++;
			}
    	}

}
```

Metoda obsługi semafora:

```
void  Semaphore(void *pdata){
	struct struktura dane;
	unsigned long int i;
	int x;
	unsigned long int obciazenielokalne;
	unsigned long int licznikWykonania = 0;

	for (;;){
		dane.id= (int) pdata;//numer zadania
		OSSemAccept(Sem);  //pobierz zajety zasób
		obciazenielokalne = obciazenie; // przepisanie zmiennej globalnej do lokalne
		OSSemPost(Sem); //zwolnij zajety zasób

	for(i = 0; i < obciazenielokalne; i++) {x++;} //petla przeci1?enia


		licznikWykonania++;					//inkrementacja liczby wykonan
		dane.licznik = licznikWykonania;	//wpisanie do struktury
		dane.obciazenie=obciazenielokalne;			//wpisanie do struktury
		OSQPost(structure, &dane); 		// wys3anie struktury
		OSTimeDly(1);
	}

}
```


Metoda obsługi kolejki:

```
void Queue(void *pdata){
	struct struktura dane;
	unsigned long int *odbierana;
	unsigned long int i;
	int x;
	unsigned long int obciazenielokalne=1;
	unsigned long int licznikWykonania = 0;

	for (;;) {
		dane.id=(int *) pdata;//numer zadania
		odbierana =OSQAccept(Kol); //odbieramy nowe obci1?enie z kolejki

		if (odbierana!=NULL){
			obciazenielokalne = *((INT32U *)odbierana); // je?eli ró?ne od zera to przypisz
		}	

	    for(i = 0; i < obciazenielokalne; i++){x++;} //petla przeci1?enia

		licznikWykonania++;		//inkrementacja liczby wykonan
		dane.licznik = licznikWykonania; //wpisz do struktury
		dane.obciazenie=obciazenielokalne;	//wpisz do struktury
		OSQPost(structure, &dane); // wys3anie struktury
		OSTimeDly(1);

	}

}
```

Metoda obsługi 'skrzynki wiadomości':
```
void Mailbox(void *pdata)
{
	struct struktura dane;
	unsigned long int *odbierana;
	unsigned long int i;
	int x;
	unsigned long int obciazenielokalne=1;
	unsigned long int licznikWykonania = 0;

	for (;;) {

		dane.id=(int *)pdata;//numer zadania
		odbierana = OSMboxAccept(Mail[dane.id-12]); //otrzymane od zadania rozg3aszaj1cego obci1?enie

		if (odbierana!=NULL){ //je?eli odrzymana wartooa ró?na od 0
			OSFlagPost(Mail_flag,1<<(dane.id-12),OS_FLAG_CLR, (void *)0);  //dajemy sygnal o zczytaniu wartosci ze skrzynki
			obciazenielokalne = *((INT32U *)odbierana); // przypisanie wartooci ze skrzynki
			OSMemPut(Mail_mem, (void *)odbierana);		//zwolnij pamiea
		}

	  for(i = 0; i < obciazenielokalne; i++){x++;} //petla przeci1?enia

		licznikWykonania++;//inkrementacja liczby wykonan
		dane.licznik = licznikWykonania; //wpisz do struktury
		dane.obciazenie=obciazenielokalne;  //wpisz do struktury
		OSQPost(structure, &dane); // wyslij swoje informacje
		OSTimeDly(1);

	}
```


