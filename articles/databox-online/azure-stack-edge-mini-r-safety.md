---
title: Przewodnik po zabezpieczeniach Azure Stack Edge w usłudze mini R | Microsoft Docs
description: Opisuje konwencje bezpieczeństwa, wytyczne, zagadnienia i wyjaśniono, jak bezpiecznie instalować i obsługiwać urządzenie Azure Stack Edge mini R.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: alkohli
ms.openlocfilehash: eb42a9a77927d8577dfec3c9167294eb8f809fec
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100382632"
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
| ![Powiadomienie o ikonie powiadomienia ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **:** | Wskazuje informacje uznawane za ważne, ale nie związane z zagrożeniami. |
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

* Ten sprzęt zawiera baterię litową. Nie należy podejmować próby obsługi pakietu baterii. Baterie tego sprzętu nie są obsługiwane przez użytkownika. Ryzyko wybuchu w przypadku zastąpienia baterii o nieprawidłowym typie.

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

To urządzenie ma dwa porty SFP +, które mogą być używane z odbiornikami optycznymi. Aby uniknąć niebezpiecznego promieniowania laserowego, należy używać tylko z odbiornikami odbiorczymi klasy 1.

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

> ![Ikona powiadomienia — 2 ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **Uwaga:** &nbsp; zmiany lub modyfikacje sprzętu, które nie zostały wyraźnie zatwierdzone przez firmę Microsoft, mogą unieważnić urząd użytkownika w celu obsługi sprzętu.

#### <a name="canada-and-usa"></a>Kanada i Stany Zjednoczone:

> ![Ikona powiadomienia — 3 ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **powiadomienie:** &nbsp; ten sprzęt został przetestowany i odnaleziony w celu zachowania zgodności z limitami dla klasy urządzenie cyfrowe, zgodnie z częścią 15 reguł FCC. Limity te zostały zaprojektowane w celu zapewnienia odpowiedniej ochrony przed szkodliwymi zakłóceniami, gdy sprzęt jest eksploatowany w środowisku komercyjnym. Ten sprzęt generuje, używa i może wypromieniowanać energię radiową oraz, jeśli nie jest zainstalowany i używany zgodnie z instrukcją ręczną instrukcji, może spowodować szkodliwe zakłócenia komunikacji radiowej. Działanie tego sprzętu w obszarze mieszkalnym prawdopodobnie spowoduje szkodliwe zakłócenia, w takim przypadku użytkownik będzie musiał poprawić zakłócenie na własny koszt.

Karta NETGEAR A6150 Wi-Fi USB dostarczona z tym wyposażeniem jest przeznaczona do działania w pobliżu treści ludzkiej. Limit SAR ustawiony przez FCC jest 1,6 W/kg w przypadku średniej wartości z 1 g tkanki. W przypadku przenoszenia produktu lub korzystania z niego w trakcie jego działania należy zachować odległość 10 mm od treści, aby zapewnić zgodność z wymaganiami dotyczącymi narażenia na korzystanie z Federacji.

Karta NETGEAR A6150 Wi-Fi USB jest zgodna z ANSI/IEEE C 95.1-1999 i została przetestowana zgodnie z metodami pomiaru i procedurami określonymi w biuletynie OET 65 uzupełnienie C.

Karta NETGEAR A6150 (SAR): 1,18 z/kg średnia dla 1 g tkanki

Karta NETGEAR A6150 WiFi USB jest używana tylko z zatwierdzonymi antenami. To urządzenie i jego anteny nie mogą być wspólnie zlokalizowane ani działać w połączeniu z żadną inną anteną lub nadajnikiem, z wyjątkiem tego, że są zgodne z procedurami wieloprzekaźnikowych. W przypadku produktów dostępnych na rynku USA można obsługiwać tylko kanał 1 ~ 11. Nie można wybrać innych kanałów.

Operacja w paśmie z pasmem 5150 – 5250 MHz służy tylko do obniżenia potencjału szkodliwego zakłócenia w systemach satelitarnych urządzeń przenośnych.

![Ostrzeżenie dotyczące informacji o przepisach — użycie wewnętrzne](./media/azure-stack-edge-mini-r-safety/regulatory-information-indoor-use-only.png)

Użytkownicy są informowani, że duże ilości energii są przydzieleni jako użytkownicy podstawowi (Użytkownicy o priorytecie) z pasm 5250 – 5350 MHz i 5650 – 5850 MHz, a te radary mogą spowodować zakłócenia i/lub uszkodzenia urządzeń z systemem LE-LAN.

Ten sprzęt generuje, używa i może wypromieniowanać energię radiową i, jeśli nie jest zainstalowany i używany zgodnie z instrukcjami, może spowodować szkodliwe zakłócenia komunikacji radiowej. Nie ma jednak gwarancji, że zakłócenia nie wystąpi w danej instalacji.

Jeśli ten sprzęt powoduje szkodliwe zakłócenia w odniesieniu do odbiornika radiowego lub odbiornika, który można ustalić, włączając urządzenia i włączania, użytkownik jest zachęcany do próby naprawienia zakłóceń przez jedną lub więcej z następujących miar:

- Zmiana orientacji lub zmiana położenia anteny odbiorczej.
- Zwiększ rozdzielenie między sprzętem a odbiornikiem.
- Podłącz sprzęt do sieci elektrycznej obwodu innego niż ten, do którego jest podłączony odbiornik.
- Aby uzyskać pomoc, skontaktuj się z przedstawicielem lub specjalistą radiową/TELEWIZORem.

Aby uzyskać więcej informacji na temat problemów z zakłóceniami, przejdź do witryny sieci Web w witrynie [fcc.gov/cgb/consumerfacts/interference.html](https://www.fcc.gov/consumers/guides/interference-radio-tv-and-telephone-signals). Można również wywołać FCC z 1-888-CALL FCC, aby zażądać interwencji i arkuszy faktów zakłóceń telefonicznych.

Dodatkowe informacje na temat bezpieczeństwa usługi Radiofrequency można znaleźć w witrynie sieci Web ze standardem FCC pod adresem [https://www.fcc.gov/general/radio-frequency-safety-0](https://www.fcc.gov/general/radio-frequency-safety-0) i w witrynie sieci Web branża Canada pod adresem [http://www.ic.gc.ca/eic/site/smt-gst.nsf/eng/sf01904.html](http://www.ic.gc.ca/eic/site/smt-gst.nsf/eng/sf01904.html) .

Ten produkt wykazuje zgodność ze standardem EMC pod warunkiem, które obejmują używanie zgodnych urządzeń peryferyjnych i kabli z osłoną między składnikami systemowymi. Ważne jest, aby korzystać z zgodnych urządzeń peryferyjnych i kabli z osłoną między składnikami systemowymi, aby zmniejszyć prawdopodobieństwo wystąpienia zakłóceń dla radia, zestawów TV i innych urządzeń elektronicznych.

To urządzenie jest zgodne z częścią 15 reguł FCC i standardami RSS w branży. Operacje podlegają następującym dwóm warunkom: (1) to urządzenie może nie prowadzić do szkodliwych zakłóceń i (2) to urządzenie musi akceptować wszelkie odebrane zakłócenia, w tym zakłócenia, które mogą spowodować niepożądane działanie urządzenia.

![Ostrzeżenie dotyczące informacji prawnych 1](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)

MOŻE ICES-3 (A)/NMB-3 (A)

Microsoft Corporation, jeden Microsoft Way, Redmond, WA 98052, USA

Stany Zjednoczone: (800) 426-9400

Kanada: (800) 933-4750

Karta NETGEAR A6150 WiFi USB Adapter FCC ID: PY318300429
 
Identyfikator IC karty MAGISTRALi NETGEAR A6150 Wi-Fi: 4054A-18300429

Karta NETGEAR A6150 Wi-Fi USB dostarczona z tym wyposażeniem jest zgodna z przepisami SAR dla ogólnych populacji/niekontrolowanych limitów ekspozycji w danych RSS-102 i została przetestowana zgodnie z metodami pomiaru i procedurami określonymi w standardzie IEEE 1528. Zachowaj co najmniej 10 mm w przypadku warunków związanych z treścią.

Karta NETGEAR A6150 Wi-Fi USB jest zgodna z limitem przenośnego obejścia RF w Kanadzie ustawionym dla niekontrolowanego środowiska i jest bezpieczna dla zamierzonej operacji zgodnie z opisem w jej ręcznym. Dalszą redukcję narażenia na działanie Federacji można osiągnąć, utrzymując produkt jak najprawdopodobniej z treści lub przez ustawienie mniejszej mocy wyjściowej, jeśli taka funkcja jest dostępna.

Tabelę z określoną szybkością pochłaniania (SAR) średnią z 1 g dla każdego produktu można zobaczyć w powyższej sekcji USA.

![Ostrzeżenie informacje o przepisach 2](./media/azure-stack-edge-mini-r-safety/regulatory-information-2.png)

#### <a name="european-union"></a>UNIA EUROPEJSKA:

Zażądaj kopii deklaracji zgodności UE dla tego sprzętu. Wyślij wiadomość e-mail do [CSI_Compliance@microsoft.com](mailto:CSI_Compliance@microsoft.com).

Karta NETGEAR A6150 Wi-Fi USB dostarczona z tym wyposażeniem jest zgodna z dyrektywą 2014/53/UE i może być również świadczona na żądanie.

![Ikona ostrzeżenia 13 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Ostrzeżenie:**  

To jest produkt klasy A. W środowisku wewnętrznym ten produkt może spowodować zakłócenia radioelektrycznych, w takim przypadku użytkownik może być zobowiązany do podjęcia odpowiednich działań.

Usuwanie baterii i sprzętu elektrycznego i elektronicznego:

![Ikona ostrzeżenia 14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

Ten symbol na produkcie lub jego baterie lub jego opakowanie oznacza, że ten produkt i wszystkie baterie, które zawiera, nie mogą być usuwane z odpadami z gospodarstwa domowego. Zamiast tego ponosisz odpowiedzialność za przekazanie tej informacji do odpowiedniego punktu kolekcji na potrzeby odtwarzania baterii i sprzętu elektrycznego i elektronicznego. Ta osobna kolekcja i recykling mogą pomóc w zapisywaniu zasobów naturalnych i zapobieganiu potencjalnym negatywnym skutkom dla zdrowia ludzkiego i środowiska ze względu na możliwą obecność niebezpiecznych substancji w bateriach i sprzęcie elektrycznym i elektronicznym, co może być spowodowane niewłaściwym usuwaniem. Aby uzyskać więcej informacji o tym, gdzie należy porzucić baterie i odpady elektryczne i elektroniczne, należy skontaktować się z lokalnym miastem/urzędem Centralnym, usługą usuwania odpadów domowych lub z drukarnią, w której zakupiono ten produkt. Skontaktuj się z nami, erecycle@microsoft.com Aby uzyskać dodatkowe informacje na temat WEEE.

Ten produkt zawiera baterie z komórkami monet.

Karta NETGEAR A6150 Wi-Fi USB dostarczona z tym sprzętem ma być bliska treści ciała ludzkiego i przetestowana pod kątem zgodności z konkretną stawką absorpcji (SAR) (patrz poniżej wartości). W przypadku przenoszenia produktu lub korzystania z niego w trakcie jego działania należy zachować odległość 10mm od treści, aby zapewnić zgodność z wymaganiami dotyczącymi narażenia Federacji.

**Karta NETGEAR A6150 (SAR):** 0,54 z/kg średnia z 10 10g tkanki

 
To urządzenie może działać we wszystkich państwach członkowskich UE. Przestrzegaj przepisów krajowych i lokalnych, w których urządzenie jest używane. To urządzenie jest ograniczone do użytku pomieszczeń, tylko gdy działa w zakresie częstotliwości 5150-5350 MHz w następujących krajach:  

![Kraje w Unii Europejskiej, które wymagają tylko użycia w obmieszczeń](./media/azure-stack-edge-mini-r-safety/mini-r-safety-eu-indoor-use-only.png)

Zgodnie z artykułami 10.8 (a) i 10.8 (b) CZERWONEj, Poniższa tabela zawiera informacje na temat używanych pasm częstotliwości i maksymalnej mocy przekazywania RF dla produktów bezprzewodowych NETGEAR do sprzedaży w Unii Europejskiej:

**Karta**

| Zakres częstotliwości (MHz) | Używane kanały | Maksymalna moc transmisji (dBm/mW) |
| --------------------- | ------------- | --------------------------- |
| 2400-2483.5 | 1-13    | ODFM: 19,9 dBm (97,7 mW) <br> CCK: 17,9 dBm (61,7 mW) |
| 5150-5320   | 36-48   | 22,9 dBm (195 mW) |
| 5250-5350   | 52-64   | 22,9 dBm (195 mW) z TPC <br> 19,9 dBm (97,7 mW) nietpc |
| 5470-5725   | 100-140 | 29,9 dBm (977 mW) z TPC <br> 29,6 dBm (490 mW) nietpc |

Microsoft Irlandia Sandyford IND Est Dublin D18 KX32 IRL

Numer telefonu: + 353 1 295 3826

Numer faksu: + 353 1 706 4110

#### <a name="singapore"></a>Singapur:

Karta NETGEAR A6150 WiFi USB dostarczonej z tym sprzętem jest zgodna ze standardami IMDA.


## <a name="next-steps"></a>Następne kroki

- [Przygotowanie do wdrożenia Azure Stack Edge mini R](azure-stack-edge-mini-r-deploy-prep.md)
