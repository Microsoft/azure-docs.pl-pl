---
title: Azure Stack Edge — Przewodnik bezpieczeństwa w programie R. | Microsoft Docs
description: Opisuje konwencje bezpieczeństwa, wytyczne, zagadnienia i wyjaśniono, jak bezpiecznie instalować i obsługiwać urządzenie Azure Stack EDGE Pro R.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: alkohli
ms.openlocfilehash: dacc9ecc28ffa482b60d1e48735fe3620b5b7558
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100363070"
---
# <a name="azure-stack-edge-pro-r-safety-instructions"></a>Azure Stack Edge — instrukcje bezpieczeństwa w programie R.

![Ikona ostrzeżenia ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ Odczytaj ikonę powiadomienia o zabezpieczeniach ](./media/azure-stack-edge-pro-r-safety/icon-safety-read-all-instructions.png)
 **Przeczytaj informacje o zabezpieczeniach i kondycji**

Przeczytaj wszystkie informacje o bezpieczeństwie w tym artykule przed użyciem urządzenia z Azure Stack EDGE Pro R. Nieprzestrzeganie instrukcji może skutkować pożarem, porażeniem elektrycznym, urazami lub uszkodzeniem właściwości. Przeczytaj wszystkie poniższe informacje zabezpieczające przed użyciem Azure Stack EDGE Pro R.

## <a name="safety-icon-conventions"></a>Konwencje ikon bezpieczeństwa

Następujące słowa sygnalizacyjne dla znaków ostrzegania o zagrożeniu są:

| Ikona | Opis |
|:--- |:--- |
| ![Symbol zagrożenia](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)| **Niebezpieczeństwo:** Wskazuje niebezpieczną sytuację, która w przypadku braku problemów spowoduje śmierć lub poważną szkodę. <br> **Ostrzeżenie:** Wskazuje niebezpieczną sytuację, która w przypadku braku problemów może spowodować śmierć lub poważną szkodę. <br> **Przestroga:** Wskazuje niebezpieczną sytuację, która w przypadku braku problemów może skutkować drobną lub średnią szkodą.|
|

Następujące ikony zagrożeń są przestrzegane podczas konfigurowania i uruchamiania urządzenia Azure Stack Edge w programie R Edge w systemie:

| Ikona | Opis |
|:--- |:--- |
| ![Najpierw przeczytaj wszystkie instrukcje](./media/azure-stack-edge-pro-r-safety/icon-safety-read-all-instructions.png) | Najpierw przeczytaj wszystkie instrukcje |
| ![Powiadomienie o ikonie powiadomienia ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **:** | Wskazuje informacje uznawane za ważne, ale nie związane z zagrożeniami. || ![Symbol zagrożenia](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) | Symbol zagrożenia |
| ![Ikona zagrożenia Porada](./media/azure-stack-edge-pro-r-safety/icon-safety-tip-hazard.png)  | Niebezpieczeństwo Porada|
| ![Ikona dużej wagi](./media/azure-stack-edge-pro-r-safety/icon-safety-heavy-weight.png)  | Duże zagrożenie wagi|
| ![Ikona porażenia energii elektrycznej](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) | Zagrożenie elektryczne |
| ![Ikona żadnej części usługi użytkownika](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) | Brak części z obsługą użytkownika. Nie należy uzyskiwać dostępu, o ile nie jest prawidłowo szkolony. |
| ![Ikona wielu źródeł napięcia](./media/azure-stack-edge-pro-r-safety/icon-safety-disconnect-all-power.png)  | Wiele źródeł. Odłącz wszystkie przewody zasilające, aby usunąć wszystkie możliwości z urządzenia. |
| ![Ikona punktów uszczypnięć](./media/azure-stack-edge-pro-r-safety/icon-pinching-points.png)  | Uszczypnięć punkty. |
| ![Ikona gorące składniki lub powierzchnie](./media/azure-stack-edge-pro-r-safety/icon-hot-component-surface.png)  | Wskazuje aktywne składniki lub powierzchnie. |
|

## <a name="handling-precautions-and-site-selection"></a>Obsługa środków ostrożności i wybór witryny

![Ostrzeżenie ikony ostrzeżenia ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) **:**

* Odpowiedni sprzęt (na przykład gniazdo palet) i osobisty sprzęt ochronny (ŚOI), na przykład, rękawice muszą być używane podczas przechodzenia i obsługiwania urządzenia wysyłanego przez program.

![Ostrzeżenie ikony ostrzegawczej ikona ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)![ zagrożenia ](./media/azure-stack-edge-pro-r-safety/icon-safety-tip-hazard.png) **:**

* Umieść sprzęt na płaskim, twardą i stabilną powierzchnię, aby uniknąć potencjalnego zagrożenia poradami lub zgniataniem.
* Nie układaj więcej niż 2 urządzenia na siebie nawzajem.
* Upewnij się, że system jest bezpieczny przed stosem.
* Nie przenosij ani nie przenoś urządzeń skumulowanych.
* Nie stosuj urządzeń, które są dynamiczniejsze z kablami zewnętrznymi.
* Upewnij się, że przewody zasilające nie są rozłożone lub uszkodzone podczas operacji na stosie.
* Urządzenia nie mogą być używane jako tabele ani obszary robocze.

![Ikona ostrzeżenia: ikona ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ porażenia elektryczną ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png)
 ![ Brak ikony części z obsługą użytkowników ](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) **Uwaga:**

* Sprawdź, czy urządzenie *as otrzymało* szkody. Jeśli Obudowa urządzenia jest uszkodzona, [skontaktuj się z firmą pomoc techniczna firmy Microsoft](azure-stack-edge-contact-microsoft-support.md) , aby uzyskać zastąpienie. Nie należy próbować korzystać z urządzenia.
* Jeśli podejrzewasz, że urządzenie nie działa prawidłowo, [skontaktuj się z firmą pomoc techniczna firmy Microsoft](azure-stack-edge-contact-microsoft-support.md) , aby uzyskać zastąpienie. Nie należy podejmować próby obsługi urządzenia.
* Urządzenie nie zawiera części z obsługą użytkownika. W programie obecne są niebezpieczne napięcia, bieżące i poziomy energii. Nie otwieraj. Zwróć urządzenie do firmy Microsoft w celu obsługi.

![](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ Ostrzeżenie ikony dużej grubości ikony ostrzeżenia ](./media/azure-stack-edge-pro-r-safety/icon-safety-heavy-weight.png) **:**

* Usunięcie modułu zasilacza zasilacza UPS uwidacznia dynamiczniejsze części w ramach zasilacza UPS. Nie wstawiaj obiektów obcych wewnątrz przedziału modułu zasilacza.
* Nie próbuj samodzielnie podnieść Azure Stack Edge urządzenia brzegowego Pro R. Obudowa może ważyć do 52 kg i 93 kg (115 funtów i 205 funtów); Korzystanie z pomocy mechanicznej lub innej odpowiedniej pomocy w przypadku przemieszczania i podnoszenia sprzętu. Zgodność z lokalnymi wymaganiami dotyczącymi kondycji i bezpieczeństwa podczas przemieszczania i podnoszenia sprzętu.
* Nie należy podejmować próby podnoszenia sprzętu bez właściwej pomocy mechanicznej. Należy pamiętać, że próba podnoszenia wagi może spowodować poważne uszkodzenia.

![Ostrzeżenie ikony ostrzeżenia ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) **:**  

* System został zaprojektowany tak, aby działał w środowisku kontrolowanym. Wybierz lokację:
  * Dobrze wentylowane i poza źródłami ciepła, w tym bezpośrednimi światłami słonecznymi i grzejnikami.
  * Nie jest narażony na wilgoć lub deszcz.
  * Znajduje się w miejscu, które minimalizuje wibracje i fizyczne porażenie.  System został zaprojektowany pod kątem wstrząsów i wibracji zgodnie z parametrem MIL-STD-810G.
  * Izolowane od silnych pól elektromagnetycznych produkowanych przez urządzenia elektryczne.
  * Zapewniane z prawidłowymi, uziemionymi.
  * Zapewnianie wystarczającej ilości miejsca do uzyskania dostępu do przewodów zasilacza, ponieważ służą one jako główne rozłączenie z zasilaniem produktu.
* Kable Ethernet nie są dostarczane z produktem. Aby zmniejszyć liczbę zakłóceń elektromagnetycznych, zaleca się użycie okablowania "skrętka" z osłoną.
* Skonfiguruj urządzenia w obszarze roboczym, co pozwala na odpowiedni obieg powietrza wokół sprzętu; Upewnij się, że okładki frontonu i tyłu są całkowicie usuwane, gdy urządzenie jest uruchomione.
* Kable Ethernet nie są dostarczane z produktem. Aby zmniejszyć liczbę zakłóceń elektromagnetycznych, zaleca się użycie okablowania z osłoną (STP) Cat 6.
* Instalowanie sprzętu w obszarze kontrolowanym przez temperaturę, bez zanieczyszczeń, i Zezwalanie na odpowiedni obieg powietrza wokół sprzętu.
* Zachowaj sprzęt z źródeł płynnych i nadmiernie humidych środowisk.
* Nie Zezwalaj na wprowadzanie systemu żadnych płynów ani jakichkolwiek obiektów obcych. Nie należy umieszczać napojów ani żadnych innych kontenerów płynów w systemie ani w ich sąsiedztwie.

## <a name="heater-precautions"></a>Środki ostrożności

![Ikona ostrzeżenia ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ : aktywne składniki lub ikona ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png)  

* Automatyczne działanie grzejników, gdy system jest włączony, może stworzyć zagrożenie dotykiem ze względu na wysokie temperatury powierzchni na okładce zestawu grzejników. Nie dotykaj tej powierzchni, gdy system jest włączony. Zezwalaj na 10-minutowe okresy w czasie, gdy system jest wyłączony.

![Ikona ostrzeżenia ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ uszczypnięć ikona punktów 1 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **Ostrzeżenie:** 

* Gdy system jest włączony, automatyczne uruchamianie tylnych drzwi plenum może stworzyć zagrożenia szczypania. Pamiętaj o tym, gdy system jest włączony.

## <a name="electrical-precautions"></a>Środki bezpieczeństwa elektrycznego

![Ikona ostrzeżenia ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ ikona porażenia elektryczną ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **Ostrzeżenie:**

* Zapewnienie bezpiecznego połączenia elektrycznego z przedłużaczem zasilacza. Napięty prądu przemiennego (AC) ma trójwymiarowy wtyk (wtyk, który ma uziemienie). Ten wtyk pasuje tylko do uziemionego gniazda zasilania. Nie obniżaj poziomu numeru PIN.
* Mając na względzie, że wtyk odbiornika zasilania jest głównym urządzeniem odłączania, upewnij się, że są one zlokalizowane blisko urządzenia i są łatwo dostępne.
* Odłącz kable zasilania (przez ściąganie wtyku, a nie przewodu) i Rozłącz wszystkie przewody, jeśli istnieją jakiekolwiek z następujących warunków:

  * Przewód zasilający lub wtyk zostanie pokryte lub uszkodzony.
  * Możesz oblewać coś do wielkości liter urządzeń.
  * Urządzenie jest narażone na deszcz lub nadmierną wilgoć.
  * Urządzenie zostało porzucone i wielkość liter urządzenia jest uszkodzona.
  * Podejrzewasz, że urządzenie wymaga usługi lub naprawy.
* Trwale Odłącz jednostkę przed jej przeniesieniem lub jeśli uważasz, że została ona uszkodzona w jakikolwiek sposób.
* Aby zapobiec bieżącym wyciekom, gdy pojedynczy przypadek tranzytowy ma więcej niż jedno nieprzerwane zasilacz (UPS), zaleca się, aby każdy zasilacz UPS był połączony z niezależnym obwodem gałęzi. Jednak w przypadku korzystania z jednostki dystrybucji zasilania (PDU) lub innego urządzenia w przypadku, gdy bezpieczeństwo każdej zasilacza UPS polega na wykorzystaniu jednego przewodu narzutcy z jednostki PDU, każdy z nich musi być również używany z dodatkowym źródłem budynku.

  > [!NOTE]
  > Jeśli używana jest jednostka PDU, która ma już dodatkowy Przewodnik uziemienia, korzystanie z dodatkowego terminalu uziemienia w UPS nie jest wymagane.

* Podaj odpowiednie źródło zasilania z ochroną przed przeciążeniem elektrycznym, aby spełnić następujące wymagania dotyczące zasilania:

  * Napięcie: od 100 do 240 woltów AC
  * Bieżąca: 20 A, maksymalnie na przewód zasilający. Podano przewody zasilające.
  * Częstotliwość: od 50 do 60 Hz

![Ikona ostrzeżenia: ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ ikona porażenia elektryczną ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png)
 ![ wiele ostrzeżeń dotyczących źródeł zasilania ](./media/azure-stack-edge-pro-r-safety/icon-safety-disconnect-all-power.png) **:**

* W przypadku systemów bez nieprzerwanego zasilacza (UPS) Odłącz wszystkie wtyki prądu przemiennego, aby całkowicie usunąć zasilanie prądu przemiennego z urządzenia.
* W przypadku systemów z zasilaczem UPS Odłącz wszystkie przewody zasilania i Użyj zasilacza UPS, aby Energize system. Zasilacz UPS zawiera niebezpieczne napięcia AC i DC.
* Jeśli system zawiera zasilacz UPS, zasilacz UPS został dostarczony z podłączonym kablem wejściowym z osłoną. Musisz użyć wejściowego kabla zasilającego, nie zamieniaj ani nie Modyfikuj kabla.

![Ikona ostrzeżenia ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ ikona porażenia elektryczną ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **Ostrzeżenie:**

* W przypadku systemów z zasilaczem UPS, zasilacz i/lub napięcie DC zawsze wiążą się z potencjalnym ryzykiem napięcia AC w danych wyjściowych UPS generowanych z baterii lub narzędzi. Aby uniknąć uszkodzenia sprzętu lub uszkodzenia ciała, należy zawsze założyć, że w danych wyjściowych UPS może nastąpić napięcie, nawet w przypadku odłączenia od podstawowego źródła zasilania.
* W przypadku systemów z systemem UPS wszystkie połączenia zewnętrzne usługi UPS dynamiczniejsze są żeńskimi. Nie usuwaj wielkości liter ani nie wstawiaj niczego do tych elementów ani żadnych łączników na UPS.

![Ikona ostrzeżenia ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ ikona porażenia elektryczną ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **Ostrzeżenie:**

* Nie należy próbować modyfikować ani używać przewodów zasilania AC innych niż dostarczone z urządzeniami.

![Ikona ostrzeżenia ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ : nie należy zachować ikony części z obsługą użytkownika ](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) **.**

* Ten sprzęt zawiera komórki monety i/lub akumulatory fosforanu żelaza. Nie należy podejmować próby obsługi sprzętu. Baterie tego sprzętu nie są obsługiwane przez użytkownika. Ryzyko wybuchu w przypadku zastąpienia baterii o nieprawidłowym typie.
* Usunięcie modułu baterii zasilacza UPS uwidacznia dynamiczniejsze części w ramach zasilacza UPS. Nie wstawiaj obiektów obcych wewnątrz przedziału modułu baterii.

## <a name="regulatory-information"></a>Informacje prawne

Ta sekcja zawiera informacje o przepisach dotyczących urządzeń z Azure Stack EDGE Pro R, numer modelu prawnego: Azure Stack EDGE Pro R.

Urządzenie brzegowe programu Azure Stack EDGE Pro R jest przeznaczone do użycia z NRTLami (UL, CSA, ETL itp.) oraz IEC/EN 60950-1 lub IEC/EN 62368-1 zgodne (CE oznaczone) sprzęt technologii informacyjnej.

Urządzenie zostało zaprojektowane do działania w następujących środowiskach:

| Środowisko | Specyfikacje |
|:--- |:--- |
|Specyfikacje temperatury | <ul><li>Temperatura magazynu: – 33 &deg; c – 63 &deg; c (– 28 &deg; f-145 &deg; F) </li><li>Ciągła operacja: 5 &deg; c – 43 &deg; c (41 &deg; f – 110 &deg; f)</li><li>Maksymalny gradient temperatury (systemy operacyjne i magazynowe): 20 &deg; C/h (68 &deg; F/h)</li></ul> |
|Względne specyfikacje wilgotności | <ul><li>Magazyn: 5% do 95% RH z 33 &deg; C (91 &deg; F) maksymalny punkt Rosa. W każdym momencie atmosfera musi być bez kondensacji.</li><li>Działa: od 5% do 85% wilgotność względna z 29 &deg; C (84,2 &deg; F) maksymalny punkt Rosa</li></ul> |
| Wymagania dotyczące maksymalnej wysokości | <ul><li>Działające (bez UPS): 15 000 ft (4 572 metrów)</li><li>Działające (z zasilaczem UPS): 10 000 ft (3 048 metrów)</li><li>Magazyn: 40 000 ft (12 192 metrów)</li></ul> |

<!--|Standard operating temperature specifications | <ul>Continuous operation (for altitude less than 950 m or 3117 ft): +5&deg;C–45&deg;C (41&deg;F–113&deg;F) with no direct sunlight on the equipment</ui>
|Expanded operating temperature specifications | <ul><li>Continuous operation: 5&deg;C to 45&deg;C at 5% to 85% RH with 29&deg;C dew point.</li><li></= 1% of annual operating hours: –5&deg;C to 55&deg;C at 5% to 90% RH with 29&deg;C dew point. |
| Expanded operating temperature restrictions | <ul><li>Do not perform cold start below -15&deg;C according to IEC 60945.</li><li>The operating temperature specified is for a maximum altitude of 950 meters.</li></ul> |
| Gaseous contamination specifications | <ul><li>Copper coupon corrosion rate: < 300 &Aring;/month per Class G1 as defined by ANSI/ISA71.04-1985.</li><li>Silver coupon corrosion rate: < 200 &Aring;/month as defined by AHSRAE TC9.9.</li></ul>|

> [!NOTE]
> Outside the standard operating temperature (10&deg;C to 35&deg;C):
> - The system can operate continuously in temperatures as low as 5&deg;C and as high as 45&deg;C. For temperatures between 35&deg;C and 45&deg;C, de-rate maximum allowable temperature by 1&deg;C per 175 m above 950 m (1&deg;F per 319 ft).
> - The system can operate down to –5&deg;C or up to 55&deg;C for a maximum of 1% of its annual operating hours. For temperatures between 45&deg;C and 55&deg;C, de-rate maximum allowable temperature by 1&deg;C per 125 m above 950 m (1&deg;F per 228 ft).
>
> When operating in the expanded temperature range:
> - System performance may be impacted.
> - Ambient temperature warnings may be reported in the System Event Log.     
>
> Maximum corrosive contaminant levels measured at &lt;/= 50% relative humidity. 


A device that has a UPS installed is designed to operate in the following environments:

| Environment | Specifications |
|:---  |:--- |
|Temperature specifications | <ul><li>Storage temperature: –40&deg;C–70&deg;C (–40&deg;F-158&deg;F) per Mil-Std 810G Method 501.5, Proc 1</li><li>Continuous operation (for altitude less than 950 m or 3117 ft): 5&deg;C–45&deg;C (41&deg;F–113&deg;F), with no direct sunlight on the equipment</li><li>Excursion temperature: 50&deg;C per Mil-Std 810G</li><li>Maximum temperature gradient (operating and storage): 20&deg;C/h (68&deg;F/h)</li></ul> |
|Relative humidity specifications | <ul><li>Storage: 5% to 95% RH with 33&deg;C (91&deg;F) maximum dew point. Atmosphere must be non-condensing at all times.</li><li>Operating: 5% to 85% relative humidity with 29&deg;C (84.2&deg;F) maximum dew point</li></ul>|
|Maximum altitude specifications | <ul><li>Storage: Mil-Std 810G method 500.5, Proc. I, 40,000 ft for 1 hour after stabilization </li><li>Operating: Mil-Std 810G method 500.5, Proc. II, air carriage, 15,000 ft for 1 hour after stabilization</li></ul>|
|Standard operating temperature specifications |<ul><li>Continuous operation (for altitude less than 950 m or 3117 ft)</li><li>+5&deg;C–45&deg;C (41&deg;F–113&deg;F) with no direct sunlight on the equipment</li></ul>|
|Expanded operating temperature specifications | <ul><li>Continuous operation: 5&deg;C to 45&deg;C at 5% to 85% RH with 29&deg;C dew point.</li><li>&lt;/= 1% of annual operating hours: 0&deg;C to 50&deg;C at 5% to 90% RH with 29&deg;C dew point.</li></ul>|
|Expanded operating temperature restrictions | <ul><li>Do not perform cold start below -15C Per IEC 60945</li><li>The operating temperature specified is for a maximum altitude of 950 meters</li></ul> |
|Gaseous contamination specifications | <ul><li>Copper coupon corrosion rate: < 300 &Aring;/month per Class G1 as defined by ANSI/ISA71.04-1985.</li><li>Silver coupon corrosion rate: < 200 &Aring;/month as defined by AHSRAE TC9.9.|-->

<!--
> [!NOTE]
> Outside the standard operating temperature (10&deg;C to 35&deg;C):
> - The system can operate continuously in temperatures as low as 5&deg;C and as high as 45&deg;C. For temperatures between 35&deg;C and 45&deg;C, de-rate maximum allowable temperature by 1&deg;C per 175 m above 950 m (1&deg;F per 319 ft).
> - The system can operate down to 0&deg;C or up to 50&deg;C for a maximum of 1% of its annual operating hours. For temperatures between 45&deg;C and 55&deg;C, de-rate maximum allowable temperature by 1&deg;C per 125 m above 950 m (1&deg;F per 228 ft).
> 
> When operating in the expanded temperature range:
> - System performance may be impacted.
> - Ambient temperature warnings may be reported in the System Event Log.
>
> Maximum corrosive contaminant levels measured at &lt;/= 50% relative humidity. --> 


> ![Ikona powiadomienia — 2 ](./media/azure-stack-edge-pro-r-safety/icon-safety-notice.png) **Uwaga:** &nbsp; zmiany lub modyfikacje sprzętu, które nie zostały wyraźnie zatwierdzone przez firmę Microsoft, mogą unieważnić urząd użytkownika w celu obsługi sprzętu.

#### <a name="canada-and-usa"></a>Kanada i Stany Zjednoczone:

> ![Ikona powiadomienia — 2 ](./media/azure-stack-edge-pro-r-safety/icon-safety-notice.png) **Uwaga:** &nbsp; ten sprzęt został przetestowany i odnaleziony w celu zachowania zgodności z limitami dla klasy urządzenie cyfrowe, zgodnie z częścią 15 reguł FCC. Limity te zostały zaprojektowane w celu zapewnienia odpowiedniej ochrony przed szkodliwymi zakłóceniami, gdy sprzęt jest eksploatowany w środowisku komercyjnym. Ten sprzęt generuje, używa i może wypromieniowanać energię radiową oraz, jeśli nie jest zainstalowany i używany zgodnie z instrukcją ręczną instrukcji, może spowodować szkodliwe zakłócenia komunikacji radiowej. Działanie tego sprzętu w obszarze mieszkalnym prawdopodobnie spowoduje szkodliwe zakłócenia, w takim przypadku użytkownik będzie musiał poprawić zakłócenie na własny koszt.

To urządzenie jest zgodne z częścią 15 reguł FCC i standardami RSS w branży. Operacje podlegają następującym dwóm warunkom: (1) to urządzenie może nie prowadzić do szkodliwych zakłóceń i (2) to urządzenie musi akceptować wszelkie odebrane zakłócenia, w tym zakłócenia, które mogą spowodować niepożądane działanie urządzenia.

![Ostrzeżenie dotyczące informacji prawnych 1](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)


MOŻE ICES-3 (A)/NMB-3 (A) Microsoft Corporation, jeden Microsoft Way, Redmond, WA 98052, USA Stany Zjednoczone: (800) 426-9400 Kanada: (800) 933-4750

#### <a name="european-union"></a>UNIA EUROPEJSKA:

Zażądaj kopii deklaracji zgodności UE. Wyślij wiadomość e-mail do [CSI_Compliance@microsoft.com](mailto:CSI_Compliance@microsoft.com).

![Ostrzeżenie ikony ostrzeżenia ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) **!**

To jest produkt klasy A. W środowisku wewnętrznym ten produkt może spowodować zakłócenia radioelektrycznych, w takim przypadku użytkownik może być zobowiązany do podjęcia odpowiednich działań.

Usuwanie baterii i sprzętu elektrycznego i elektronicznego:

![Ikona ostrzeżenia 14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

Ten symbol na produkcie lub jego baterie lub jego opakowanie oznacza, że ten produkt i wszystkie baterie, które zawiera, nie mogą być usuwane z odpadami z gospodarstwa domowego. Zamiast tego ponosisz odpowiedzialność za przekazanie tej informacji do odpowiedniego punktu kolekcji na potrzeby odtwarzania baterii i sprzętu elektrycznego i elektronicznego. Ta osobna kolekcja i recykling mogą pomóc w zapisywaniu zasobów naturalnych i zapobieganiu potencjalnym negatywnym skutkom dla zdrowia ludzkiego i środowiska ze względu na możliwą obecność niebezpiecznych substancji w bateriach i sprzęcie elektrycznym i elektronicznym, co może być spowodowane niewłaściwym usuwaniem. Aby uzyskać więcej informacji o tym, gdzie należy porzucić baterie i odpady elektryczne i elektroniczne, należy skontaktować się z lokalnym miastem/urzędem Centralnym, usługą usuwania odpadów domowych lub z drukarnią, w której zakupiono ten produkt. Skontaktuj się z nami, erecycle@microsoft.com Aby uzyskać dodatkowe informacje na temat WEEE.

Ten produkt zawiera baterie z komórkami monet.

Microsoft Irlandia Sandyford IND Est Dublin D18 KX32 IRL numer telefonu: + 353 1 295 3826 numer faksu: + 353 1 706 4110


## <a name="next-steps"></a>Następne kroki

- [Przygotowanie do wdrożenia Azure Stack krawędzi R](azure-stack-edge-pro-r-deploy-prep.md)
