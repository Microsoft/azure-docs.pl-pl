---
title: Przewodnik po zabezpieczeniach Azure Stack Edge w usłudze mini R | Microsoft Docs
description: Opisuje konwencje bezpieczeństwa, wytyczne, zagadnienia i wyjaśniono, jak bezpiecznie instalować i obsługiwać urządzenie Azure Stack Edge mini R.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: 507ceef0f13951eafdcb02d586f35c1d61764c4e
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467476"
---
# <a name="azure-stack-edge-mini-r-safety-instructions"></a>Instrukcje dotyczące bezpieczeństwa w Azure Stack Edge

![Ikona ostrzeżenia 1 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ Odczyt ikona uwagi ](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png)
 **dotyczącej bezpieczeństwa i informacji o kondycji**

Przeczytaj wszystkie informacje o zabezpieczeniach w tym artykule przed rozpoczęciem korzystania z urządzenia z systemem Azure Stack Edge mini R, kompozycji jednego z pakietów baterii, jednego zasilacza prądu przemiennego lub zasilania DC, jednego modułu adaptera zasilania i jednego modułu serwera. Nieprzestrzeganie instrukcji może skutkować pożarem, porażeniem elektrycznym, urazami lub uszkodzeniem właściwości. Przeczytaj wszystkie poniższe informacje zabezpieczające przed rozpoczęciem korzystania z Azure Stack Edge mini R.

## <a name="safety-icon-conventions"></a>Konwencje ikon bezpieczeństwa

Następujące słowa sygnalizacyjne dla znaków ostrzegania o zagrożeniu są:

| Ikona | Opis |
|:--- |:--- |
| ![Symbol zagrożenia](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)| **Niebezpieczeństwo:** Wskazuje niebezpieczną sytuację, która w przypadku braku problemów spowoduje śmierć lub poważną szkodę. <br> **Ostrzeżenie:** Wskazuje niebezpieczną sytuację, która w przypadku braku problemów może spowodować śmierć lub poważną szkodę. <br> **Przestroga:** Wskazuje niebezpieczną sytuację, która w przypadku braku problemów może skutkować drobną lub średnią szkodą.|
|

Podczas konfigurowania i uruchamiania urządzenia Azure Stack w usłudze mini R należy przestrzegać następujących ikon zagrożeń:

| Ikona | Opis |
|:--- |:--- |
| ![Najpierw przeczytaj wszystkie instrukcje](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png) | Najpierw przeczytaj wszystkie instrukcje |
| ![Symbol zagrożenia](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) | Symbol zagrożenia |
| ![Ikona porażenia energii elektrycznej](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) | Zagrożenie elektryczne |
| ![Tylko użycie wewnętrzne](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) | Tylko użycie wewnętrzne |
| ![Ikona żadnej części usługi użytkownika](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) | Brak części z obsługą użytkownika. Nie należy uzyskiwać dostępu, o ile nie jest prawidłowo szkolony. |
|

## <a name="handling-precautions-and-site-selection"></a>Obsługa środków ostrożności i wybór witryny

Urządzenie Azure Stack Edge mini R zawiera następujące środki ostrożności dotyczące obsługi i kryteria wyboru witryny:

![Ikona ostrzeżenia 2 ikona ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ porażenia ruchu elektrycznego ikona ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
 ![ niedostępności części ](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) **:**

* Sprawdź, czy urządzenie *as otrzymało* szkody. Jeśli Obudowa urządzenia jest uszkodzona, [skontaktuj się z firmą pomoc techniczna firmy Microsoft](azure-stack-edge-placeholder.md) , aby uzyskać zastąpienie. Nie należy próbować korzystać z urządzenia.
* Jeśli podejrzewasz, że urządzenie nie działa prawidłowo, [skontaktuj się z firmą pomoc techniczna firmy Microsoft](azure-stack-edge-placeholder.md) , aby uzyskać zastąpienie. Nie należy podejmować próby obsługi urządzenia.
* Urządzenie nie zawiera części z obsługą użytkownika. W programie obecne są niebezpieczne napięcia, bieżące i poziomy energii. Nie otwieraj. Zwróć urządzenie do firmy Microsoft w celu obsługi.

![Ikona ostrzeżenia 3 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Przestroga:**

Zalecane jest, aby obsługiwać system:

* Poza źródłami ciepła, w tym bezpośrednimi światłami słonecznymi i grzejnikami.
* W lokalizacjach niewidocznych dla wilgoci lub deszczu.
* Znajduje się w miejscu, które minimalizuje wibracje i fizyczne porażenie.  System został zaprojektowany pod kątem wstrząsów i wibracji zgodnie z parametrem MIL-STD-810G.
* Izolowane od silnych pól elektromagnetycznych produkowanych przez urządzenia elektryczne.
* Nie Zezwalaj na wprowadzanie systemu żadnych płynów ani jakichkolwiek obiektów obcych. Nie należy umieszczać napojów ani żadnych innych kontenerów płynów w systemie ani w ich sąsiedztwie.

![Ikona ostrzeżenia 4 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ nie należy zachować ikony części z obsługą użytkownika ](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) **.**

* Ten sprzęt zawiera baterię litową. Nie należy próbować obsługiwać pakietu baterii. Baterie tego sprzętu nie są obsługiwane przez użytkownika. Ryzyko wybuchu w przypadku zastąpienia baterii o nieprawidłowym typie.

![Ikona ostrzeżenia 5 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Przestroga:**

Należy naliczać opłaty za pakiet baterii tylko wtedy, gdy jest częścią urządzenia z systemem Azure Stack Edge mini R, nie należy naliczać opłat za oddzielne urządzenie.

![Ikona ostrzeżenia 6 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Przestroga:**

* Przełącznik WŁĄCZania/wyłączania w pakiecie baterii służy do naładowania baterii tylko do modułu serwera. Jeśli zasilacz jest podłączony do pakietu baterii, zasilanie jest przesyłane do modułu serwera, nawet jeśli przełącznik jest wyłączony.

![Ikona ostrzeżenia 7 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Przestroga:**

* Nie Nagrywaj ani nie skracaj obwodu pakietu baterii. Należy ją odtworzyć lub usunąć prawidłowo.

![Ikona ostrzeżenia 8 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Przestroga:**

* W przypadku korzystania z podanego zasilacza AC/DC ten system ma również opcję użycia dostarczonego pola typu 2590 baterii. W takim przypadku użytkownik końcowy sprawdzi, czy spełnia on wszystkie obowiązujące zabezpieczenia, transport, środowisko i wszelkie inne regulacje krajowe/lokalne.
* Podczas obsługi systemu z typem 2590 baterii, należy obsłużyć baterię w warunkach użytkowania określonych przez producenta baterii.

![Ikona ostrzeżenia 9 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Przestroga:**

To urządzenie ma dwa porty SFP +, które mogą być używane z urządzeniami nadawczymi optycznymi. Aby uniknąć niebezpiecznego promieniowania laserowego, należy używać tylko z odbiornikami odbiorczymi klasy 1.

## <a name="electrical-precautions"></a>Środki bezpieczeństwa elektrycznego

Urządzenie Azure Stack Edge mini R ma następujące środki ostrożności elektryczne:

![Ikona ostrzeżenia 10 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) ![ ikona porażenia elektrycznego ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) **Ostrzeżenie:**

Gdy jest używany z adapterem zasilacza:

* Zapewnienie bezpiecznego połączenia elektrycznego z przedłużaczem zasilacza. Napięty prądu przemiennego (AC) ma trójwymiarowy wtyk (wtyk, który ma uziemienie). Ten wtyk pasuje tylko do uziemionego gniazda zasilania. Nie obniżaj poziomu numeru PIN.
* Mając na względzie, że wtyk odbiornika zasilania jest głównym urządzeniem odłączania, upewnij się, że są one zlokalizowane blisko urządzenia i są łatwo dostępne.
* Odłącz kable zasilania (przez ściąganie wtyku, a nie przewodu) i Rozłącz wszystkie przewody, jeśli istnieją jakiekolwiek z następujących warunków:

  * Przewód zasilający lub wtyk zostanie pokryte lub uszkodzony.
  * Urządzenie jest narażone na deszcz, nadmiar wilgoci lub inne cieczki.
  * Urządzenie zostało porzucone i wielkość liter urządzenia została uszkodzona.
  * Podejrzewasz, że urządzenie wymaga usługi lub naprawy.
* Trwale Odłącz jednostkę przed jej przeniesieniem lub jeśli uważasz, że została ona uszkodzona w jakikolwiek sposób.

* Podaj odpowiednie źródło zasilania z ochroną przed przeciążeniem elektrycznym, aby spełnić następujące wymagania dotyczące zasilania:

* Napięcie: 100-240 woltów AC
* Current: 1,7 Amperes
* Częstotliwość: od 50 do 60 Hz

![Ikona ostrzeżenia 11 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ ikona porażenie elektryczne ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) **Ostrzeżenie:**

* Nie należy próbować modyfikować ani używać przewodów zasilania AC innych niż dostarczone z urządzeniami.

![Ikona ostrzeżenia 12 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ ikona wstrząsu elektrycznego tylko w przypadku użycia w obwodach ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
 ![ ](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) **:**

* Zasilacz oznaczony etykietą z tym symbolem jest klasyfikowany wyłącznie do użytku pomieszczeń.

## <a name="regulatory-information"></a>Informacje prawne

Poniżej znajdują się informacje o przepisach dotyczących urządzeń z Azure Stack Edge mini R, numer modelu prawnego: TMA01.

Urządzenie Azure Stack Edge mini R jest przeznaczone do użycia z NRTL na liście (UL, CSA, ETL itp.), a IEC/EN 60950-1 lub IEC/EN 62368-1 zgodne (CE oznaczono) sprzęt technologii informatycznej.

W krajach innych niż USA i Kanada kable sieciowe (niedostarczone z urządzeniami) nie są instalowane z tym wyposażeniem, jeśli ich długość jest większa niż 3 metry.

Sprzęt jest przeznaczony do działania w następujących środowiskach:

| Środowisko | Specyfikacje |
|:---  |:--- |
| Specyfikacje temperatury systemu | <ul><li>Temperatura magazynu: – 20 &deg; c – 50 &deg; c (– 4 &deg; f-122 &deg; f)</li><li>Ciągła operacja: 0 &deg; c – 40 &deg; C (32 &deg; f – 104 &deg; f)</li></ul> |
| Specyfikacje względnej wilgotności (RH) | <ul><li>Magazyn: od 5% do 95% wilgotność względna</li><li>Działa: od 10% do 90% wilgotność względna</li></ul>|
| Wymagania dotyczące maksymalnej wysokości | <ul><li>Działa: 15 000 stóp (4 572 metrów)</li><li>Niedziałające: 40 000 stóp (12 192 metrów)</li></ul>|

> ![Powiadomienie o ikonie powiadomienia ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **:** &nbsp; zmiany lub modyfikacje sprzętu, które nie zostały wyraźnie zatwierdzone przez firmę Microsoft, mogą unieważnić urząd użytkownika w celu obsługi sprzętu.

Kanada i Stany Zjednoczone:

Uwaga: ten sprzęt został przetestowany i odnaleziony w celu zachowania zgodności z ograniczeniami dla klasy urządzenie cyfrowe, zgodnie z częścią 15 reguł FCC. Limity te zostały zaprojektowane w celu zapewnienia odpowiedniej ochrony przed szkodliwymi zakłóceniami, gdy sprzęt jest eksploatowany w środowisku komercyjnym. Ten sprzęt generuje, używa i może wypromieniowanać energię radiową oraz, jeśli nie jest zainstalowany i używany zgodnie z instrukcją ręczną instrukcji, może spowodować szkodliwe zakłócenia komunikacji radiowej. Działanie tego sprzętu w obszarze mieszkalnym prawdopodobnie spowoduje szkodliwe zakłócenia, w takim przypadku użytkownik będzie musiał poprawić zakłócenie na własny koszt.

To urządzenie jest zgodne z częścią 15 reguł FCC i standardami RSS w branży. Operacje podlegają następującym dwóm warunkom: (1) to urządzenie może nie prowadzić do szkodliwych zakłóceń i (2) to urządzenie musi akceptować wszelkie odebrane zakłócenia, w tym zakłócenia, które mogą spowodować niepożądane działanie urządzenia.

![Ostrzeżenie dotyczące informacji prawnych 1](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)

MOŻE ICES-3 (A)/NMB-3 (A) Microsoft Corporation, jeden Microsoft Way, Redmond, WA 98052, USA.
Stany Zjednoczone: (800) 426-9400 Kanada: (800) 933-4750

Unia Europejska: Zażądaj kopii deklaracji zgodności UE.

> ![Ikona ostrzeżenia 13 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) jest produktem klasy a. W środowisku wewnętrznym ten produkt może spowodować zakłócenia radioelektrycznych, w takim przypadku użytkownik może być zobowiązany do podjęcia odpowiednich działań.

Usuwanie baterii i sprzętu elektrycznego i elektronicznego:

![Ikona ostrzeżenia 14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

Ten symbol na produkcie lub jego baterie lub jego opakowanie oznacza, że ten produkt i wszystkie baterie, które zawiera, nie mogą być usuwane z odpadami z gospodarstwa domowego. Zamiast tego ponosisz odpowiedzialność za przekazanie tej informacji do odpowiedniego punktu kolekcji na potrzeby odtwarzania baterii i sprzętu elektrycznego i elektronicznego. Ta osobna kolekcja i recykling mogą pomóc w zapisywaniu zasobów naturalnych i zapobieganiu potencjalnym negatywnym skutkom dla zdrowia ludzkiego i środowiska ze względu na możliwą obecność niebezpiecznych substancji w bateriach i sprzęcie elektrycznym i elektronicznym, co może być spowodowane niewłaściwym usuwaniem. Aby uzyskać więcej informacji o tym, gdzie należy porzucić baterie i odpady elektryczne i elektroniczne, należy skontaktować się z lokalnym miastem/urzędem Centralnym, usługą usuwania odpadów domowych lub z drukarnią, w której zakupiono ten produkt. Skontaktuj się z nami, erecycle@microsoft.com Aby uzyskać dodatkowe informacje na temat WEEE.

Ten produkt zawiera baterie z komórkami monet.
Microsoft Irlandia Sandyford IND Est Dublin D18 KX32 IRL numer telefonu: + 353 1 295 3826 numer faksu: + 353 1 706 4110

## <a name="next-steps"></a>Następne kroki

- [Przygotowanie do wdrożenia Azure Stack Edge mini R](azure-stack-edge-mini-r-deploy-prep.md)
