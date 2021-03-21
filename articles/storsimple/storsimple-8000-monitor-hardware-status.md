---
title: Składniki sprzętowe serii StorSimple 8000 i stan | Microsoft Docs
description: Dowiedz się, jak monitorować składniki sprzętowe urządzenia StorSimple za pomocą usługi StorSimple Device Manager.
services: storsimple
documentationcenter: ''
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2018
ms.author: alkohli
ms.openlocfilehash: 6b750659bf22e856ee8ad7368e3fea64dd7245df
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94960363"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-hardware-components-and-status"></a>Monitorowanie składników i stanu sprzętu za pomocą usługi StorSimple Device Manager

## <a name="overview"></a>Omówienie
W tym artykule opisano różne składniki fizyczne i logiczne w lokalnym urządzeniu z serii StorSimple 8000. Wyjaśniono również sposób monitorowania stanu składnika urządzenia przy użyciu bloku **stan i kondycja sprzętu** w usłudze StorSimple Device Manager.

Blok **kondycja stanu i sprzętu** przedstawia stan sprzętu wszystkich składników urządzenia StorSimple.

Pod listą składników dla 8100 istnieją trzy sekcje opisujące:

* **Współużytkowane składniki** — te elementy nie są częścią kontrolerów, takich jak stacje dysków, składniki obudowy, zasilania i chłodzenia modułów (PCM), temperatura PCM, napięcie liniowe i bieżące czujniki liniowe.
* **Składniki kontrolera 0** — składniki, które znajdują się na kontrolerze 0, takie jak kontroler, Ekspander SAS i łącznik, czujniki temperatury kontrolera i różne interfejsy sieciowe.
* **Składniki kontrolera 1** — składniki tworzące kontroler 1, podobne do tych szczegółowych dla kontrolera 0.

Na urządzeniu 8600 znajdują się dodatkowe składniki, które odnoszą się do obudowy rozszerzonych dysków (EBOD). W obszarze listy składników znajduje się pięć sekcji. Istnieją trzy sekcje, które zawierają składniki w obudowie podstawowej i są identyczne z tymi opisanymi dla 8100. Istnieją dwie dodatkowe sekcje dla obudowy EBOD, które opisują:

* **Składniki kontrolera EBOD 0** — składniki, które znajdują się w obudowie EBOD 0, takie jak kontroler EBOD, Ekspander SAS i łącznik oraz czujniki temperatury kontrolera.
* **Składniki kontrolera EBOD 1** — składniki tworzące obudowa EBOD 1, podobne do tych, które zostały przedstawione w przypadku EBOD obudowy 0.
* **Współużytkowane składniki obudowy EBOD** — składniki obecne w obudowie EBOD i module PCM, które nie są częścią kontrolera EBOD.

> [!NOTE]
> **Stan sprzętu nie jest dostępny dla urządzenia w chmurze StorSimple (8010/8020).**


## <a name="monitor-the-hardware-status"></a>Monitorowanie stanu sprzętu
Wykonaj następujące kroki, aby wyświetlić stan sprzętu składnika urządzenia:

1. Przejdź do opcji **urządzenia**, wybierz określone urządzenie StorSimple. Przejdź do pozycji **monitoruj > kondycja sprzętu**.

    ![Zrzut ekranu przedstawiający blok ustawienia usługi Device Manager. W obszarze monitor jest zaznaczona opcja kondycja sprzętu.](./media/storsimple-8000-monitor-hardware-status/hw-health1.png)

2. Znajdź sekcję **składniki sprzętowe** i wybierz spośród dostępnych składników. Wystarczy kliknąć etykietę składnika, aby rozwinąć listę i wyświetlić stan różnych składników urządzeń. Zapoznaj się z [listą szczegółowych składników dla obudowy podstawowej](#component-list-for-primary-enclosure-of-storsimple-device) i [Szczegółowa lista składników dla obudowy EBOD](#component-list-for-ebod-enclosure-of-storsimple-device).

    ![Zrzut ekranu przedstawiający jeden składnik wybrany w sekcji składniki sprzętowe. Sąsiadujący blok pokazuje nazwę i stan części danego składnika.](./media/storsimple-8000-monitor-hardware-status/hw-health2.png)

3. Aby interpretować stan składnika, użyj następującego schematu kodowania kolorów:
   
   * **Zielone sprawdzenie** — wskazuje składnik dobrej kondycji z **prawidłowym** stanem.
   * **Żółty** — oznacza, że obniżony składnik jest w stanie **ostrzegawczym** .
   * **Czerwony wykrzyknik** — wskazuje niepowodzenie składnika, który ma stan **niepowodzenia** .
   * **Biały z czarnym tekstem** — oznacza nieobecny składnik.
   
   Poniższy zrzut ekranu przedstawia urządzenie ze składnikami w stanie **prawidłowym**, **ostrzegawczym** i **niepowodzenia** .
       
   ![Zrzut ekranu przedstawiający trzy składniki w sekcji składniki sprzętowe: jeden w prawidłowym stanie, jeden w stanie awarii i jeden w stanie ostrzeżenia.](./media/storsimple-8000-monitor-hardware-status/hw-health3.png)

   Rozwijanie **listy współużytkowanych składników** można zobaczyć, że pamięć NVRAM i klaster mają obniżoną wydajność.

   ![Zrzut ekranu przedstawiający wybrany element składniki udostępnione. W sąsiednim bloku N V R a M i klaster jest w stanie ostrzegawczym.](./media/storsimple-8000-monitor-hardware-status/hw-health5.png)

   Rozwijanie listy **składników kontrolera 1** , można zobaczyć, że węzeł klastra zakończył się niepowodzeniem.  

   ![Zrzut ekranu przedstawiający wybrany element składników kontrolera 1. W sąsiednim bloku węzeł klastra jest w stanie awarii.](./media/storsimple-8000-monitor-hardware-status/hw-health4.png)  

4. Jeśli napotkasz składnik, który nie jest w **dobrej kondycji** , skontaktuj się z pomoc techniczna firmy Microsoft. Jeśli na urządzeniu są włączone alerty, zostanie wyświetlony alert e-mail. Jeśli zachodzi potrzeba zastąpienia uszkodzonego składnika sprzętowego, zobacz [StorSimple sprzętu sprzętowego](./storsimple-8000-hardware-component-replacement.md).

## <a name="component-list-for-primary-enclosure-of-storsimple-device"></a>Lista składników dla podstawowej obudowy urządzenia StorSimple
W poniższej tabeli przedstawiono składniki fizyczne i logiczne zawarte w podstawowej obudowie (obecne zarówno w 8100 i 8600) lokalnego urządzenia StorSimple.

| Składnik | Moduł | Typ | Lokalizacja | Jednostka przemieszczenia pola (FRU)? | Opis |
| --- | --- | --- | --- | --- | --- |
| Napęd w gnieździe [0-11] |Stacje dysków |Fizyczny |Udostępniona |Tak |Dla każdego dysku SSD lub dysków twarde w obudowie podstawowej jest prezentowany jeden wiersz. |
| Czujnik temperatury otoczenia |Szaf |Fizyczny |Udostępniona |Nie |Mierzy temperaturę w obudowie. |
| Czujnik temperatury średniej płaszczyzny |Szaf |Fizyczny |Udostępniona |Nie |Mierzy temperaturę płaszczyzny środkowej. |
| Alarm dźwiękowy |Szaf |Fizyczny |Udostępniona |Nie |Wskazuje, czy dźwiękowy podsystem alarmowy w obudowie jest funkcjonalny. |
| Szaf |Szaf |Fizyczny |Udostępniona |Tak |Wskazuje obecność obudowy. |
| Ustawienia obudowy |Szaf |Fizyczny |Udostępniona |Nie |Odnosi się do panelu przedniego obudowy. |
| Czujniki napięcia linii |PCM |Fizyczny |Udostępniona |Nie |W przypadku wielu czujników napięcia linii są wyświetlane ich Stany, które wskazują, czy mierzone napięcie jest objęte tolerancją. |
| Czujniki bieżące liniowe |PCM |Fizyczny |Udostępniona |Nie |Wszystkie czujniki bieżące z wieloma wierszami mają wyświetlany stan, który wskazuje, czy mierzona wartość bieżąca jest równa tolerancji. |
| Czujniki temperatury w module PCM |PCM |Fizyczny |Udostępniona |Nie |W przypadku wielu czujników temperatury, takich jak czujniki wlotu i hotspotu, są wyświetlane ich Stany, wskazujące, czy zmierzona temperatura jest w zakresie tolerancji. |
| Zasilacz [0-1] |PCM |Fizyczny |Udostępniona |Tak |Dla każdego zasilacza w dwóch PCMs znajdujących się w tylnej części urządzenia jest prezentowany jeden wiersz. |
| Chłodzenie [0-1] |PCM |Fizyczny |Udostępniona |Tak |Jeden wiersz jest prezentowany dla każdego z czterech wentylatorów chłodzących znajdujących się w dwóch PCMs. |
| Bateria [0-1] |PCM |Fizyczny |Udostępniona |Tak |Dla każdego z modułów baterii tworzenia kopii zapasowych, które znajdują się w module PCM, jest prezentowany jeden wiersz. |
| Funkcja Metis |Nie dotyczy |Wartości logiczne |Udostępniona |Nie dotyczy |Wyświetla stan baterii: czy potrzebują ładowania i zbliżają się do końca okresu użytkowania. |
| Klaster |Nie dotyczy |Wartości logiczne |Udostępniona |Nie dotyczy |Przedstawia stan klastra utworzonego między dwoma modułami zintegrowanego kontrolera. |
| Węzeł klastra |Nie dotyczy |Wartości logiczne |Udostępniona |Nie dotyczy |Wskazuje stan kontrolera w ramach klastra. |
| Kworum klastra |Nie dotyczy |Wartości logiczne | |Nie dotyczy |Wskazuje obecność większościowego członkostwa dysku w puli magazynu dysk twardy. |
| Obszar danych dysku twardego |Nie dotyczy |Wartości logiczne |Udostępniona |Nie dotyczy |Miejsce do magazynowania używane dla danych w puli magazynów dysków twardych (dysk twardy). |
| Przestrzeń zarządzania dyskami twardymi |Nie dotyczy |Wartości logiczne |Udostępniona |Nie dotyczy |Miejsce zarezerwowane w puli magazynów dysków twardych na potrzeby zadań zarządzania. |
| Obszar kworum dysku twardego |Nie dotyczy |Wartości logiczne |Udostępniona |Nie dotyczy |Miejsce zarezerwowane w puli magazynów dysków twardych dla kworum klastra. |
| Przestrzeń zastępująca dysk twardy |Nie dotyczy |Wartości logiczne |Udostępniona |Nie dotyczy |Miejsce zarezerwowane w puli magazynów dysków twardych do zastępowania kontrolera. |
| Przestrzeń danych SSD |Nie dotyczy |Wartości logiczne |Udostępniona |Nie dotyczy |Miejsce do magazynowania używane na potrzeby danych w puli magazynów półprzewodnikowych (SSD). |
| Przestrzeń dyskowa SSD |Nie dotyczy |Wartości logiczne |Udostępniona |Nie dotyczy |Miejsce do magazynowania w puli magazynów SSD dedykowane dla logiki NVRAM. |
| Pula magazynów dysków twardych |Nie dotyczy |Wartości logiczne |Udostępniona |Nie dotyczy |Przedstawia stan puli magazynu logicznego, która jest tworzona na podstawie urządzenia HDD. |
| Pula magazynu SSD |Nie dotyczy |Wartości logiczne |Udostępniona |Nie dotyczy |Przedstawia stan puli magazynu logicznego, która jest tworzona na podstawie urządzenia dysków SSD. |
| Kontroler [0-1] [stan] |WE/WY |Fizyczny |Kontroler |Tak |Wyświetla stan kontrolera i czy jest on w trybie aktywnym lub w stanie wstrzymania w ramach obudowy. |
| Czujniki temperatury w kontrolerze |WE/WY |Fizyczny |Kontroler |Nie |W przypadku wielu czujników temperatury, takich jak moduł we/wy, czujniki procesora CPU, moduły DIMM i PCIe, są wyświetlane ich Stany, które wskazują, czy napotkana temperatura jest w zakresie tolerancji. |
| Ekspander SAS |WE/WY |Fizyczny |Kontroler |Nie |Wskazuje stan ekspandera Serial Attached SCSI (SAS), który jest używany do łączenia zintegrowanego magazynu z kontrolerem. |
| Łącznik SAS [0-1] |WE/WY |Fizyczny |Kontroler |Nie |Wskazuje stan każdego łącznika SAS, który jest używany do łączenia magazynu zintegrowanego z ekspanderem SAS. |
| SBB — połączenie średniej płaszczyzny |WE/WY |Fizyczny |Kontroler |Nie |Wskazuje stan łącznika środkowej płaszczyzny, który jest używany do łączenia poszczególnych kontrolerów z płaszczyzną środkową. |
| Rdzeń procesora |WE/WY |Fizyczny |Kontroler |Nie |Wskazuje stan rdzeni procesora w poszczególnych kontrolerach. |
| Moc elektroniki obudowy |WE/WY |Fizyczny |Kontroler |Nie |Wskazuje stan systemu zasilacza używanego przez obudowę. |
| Diagnostyka elektroniki obudowy |WE/WY |Fizyczny |Kontroler |Nie |Wskazuje stan podsystemów diagnostyki dostarczonych przez kontroler. |
| Kontroler zarządzania płytą główną (BMC) |WE/WY |Fizyczny |Kontroler |Nie |Wskazuje stan kontrolera zarządzania płytą główną (BMC), który jest wyspecjalizowanym procesorem usług, który monitoruje urządzenie sprzętowe za pośrednictwem czujników i komunikuje się z administratorem systemu za pośrednictwem niezależnego połączenia. |
| Sieć Ethernet |WE/WY |Fizyczny |Kontroler |Nie |Wskazuje stan każdego z interfejsów sieciowych, czyli portów zarządzania i danych znajdujących się na kontrolerze. |
| PAMIĘCI |WE/WY |Fizyczny |Kontroler |Nie |Wskazuje stan pamięci NVRAM, czyli nielotną pamięć z dostępem losowym, utworzoną przez baterię, która służy do zachowywania informacji krytycznych dla aplikacji w przypadku awarii zasilania. |

## <a name="component-list-for-ebod-enclosure-of-storsimple-device"></a>Lista składników dla obudowy EBOD urządzenia StorSimple
W poniższej tabeli przedstawiono składniki fizyczne i logiczne zawarte w obudowie EBOD (obecne tylko w modelu 8600) lokalnego urządzenia StorSimple.

| Składnik | Moduł | Typ | Lokalizacja | FRU? | Opis |
| --- | --- | --- | --- | --- | --- |
| Napęd w gnieździe [0-11] |Stacje dysków |Fizyczny |Udostępniona |Tak |Jeden wiersz jest prezentowany dla każdego z dysków twardych znajdujących się na początku obudowy EBOD. |
| Czujnik temperatury otoczenia |Szaf |Fizyczny |Udostępniona |Nie |Mierzy temperaturę w obudowie. |
| Czujnik temperatury średniej płaszczyzny |Szaf |Fizyczny |Udostępniona |Nie |Mierzy temperaturę płaszczyzny środkowej. |
| Alarm dźwiękowy |Szaf |Fizyczny |Udostępniona |Nie |Wskazuje, czy dźwiękowy podsystem alarmowy w obudowie jest funkcjonalny. |
| Szaf |Szaf |Fizyczny |Udostępniona |Tak |Wskazuje obecność obudowy. |
| Ustawienia obudowy |Szaf |Fizyczny |Udostępniona |Nie |Odnosi się do działania lub panelu przedniego obudowy. |
| Czujniki napięcia linii |PCM |Fizyczny |Udostępniona |Nie |W przypadku wielu czujników napięcia linii są wyświetlane ich Stany, które wskazują, czy mierzone napięcie jest objęte tolerancją. |
| Czujniki bieżące liniowe |PCM |Fizyczny |Udostępniona |Nie |Wszystkie czujniki bieżące z wieloma wierszami mają wyświetlany stan, który wskazuje, czy mierzona wartość bieżąca jest równa tolerancji. |
| Czujniki temperatury w module PCM |PCM |Fizyczny |Udostępniona |Nie |W przypadku wielu czujników temperatury, takich jak czujniki wlotu i hotspotu, są wyświetlane ich Stany, które wskazują, czy zmierzona temperatura jest w zakresie tolerancji. |
| Zasilacz [0-1] |PCM |Fizyczny |Udostępniona |Tak |Dla każdego zasilacza w dwóch PCMs znajdujących się w tylnej części urządzenia jest prezentowany jeden wiersz. |
| Chłodzenie [0-1] |PCM |Fizyczny |Udostępniona |Tak |Jeden wiersz jest prezentowany dla każdego z czterech wentylatorów chłodzących znajdujących się w dwóch PCMs. |
| Magazyn lokalny [dysk twardy] |Nie dotyczy |Wartości logiczne |Udostępniona |Nie dotyczy |Przedstawia stan puli magazynu logicznego, która jest tworzona na podstawie urządzenia HDD. |
| Kontroler [0-1] [stan] |WE/WY |Fizyczny |Kontroler |Tak |Wyświetla stan kontrolerów w module EBOD. |
| Czujniki temperatury w EBOD |WE/WY |Fizyczny |Kontroler |Nie |W przypadku wielu czujników temperatury z każdego kontrolera są wyświetlane ich Stany, które wskazują, czy napotkana temperatura jest tolerancja. |
| Ekspander SAS |WE/WY |Fizyczny |Kontroler |Nie |Wskazuje stan ekspandera SAS, który jest używany do łączenia magazynu zintegrowanego z kontrolerem. |
| Łącznik SAS [0-2] |WE/WY |Fizyczny |Kontroler |Nie |Wskazuje stan każdego łącznika SAS, który jest używany do łączenia magazynu zintegrowanego z ekspanderem SAS. |
| SBB — połączenie średniej płaszczyzny |WE/WY |Fizyczny |Kontroler |Nie |Wskazuje stan łącznika środkowej płaszczyzny, który jest używany do łączenia poszczególnych kontrolerów z płaszczyzną środkową. |
| Moc elektroniki obudowy |WE/WY |Fizyczny |Kontroler |Nie |Wskazuje stan systemu zasilacza używanego przez obudowę. |
| Diagnostyka elektroniki obudowy |WE/WY |Fizyczny |Kontroler |Nie |Wskazuje stan podsystemów diagnostyki dostarczonych przez kontroler. |
| Połączenie z kontrolerem urządzenia |WE/WY |Fizyczny |Kontroler |Nie |Wskazuje stan połączenia między modułem we/wy EBOD i kontrolerem urządzenia. |

## <a name="next-steps"></a>Następne kroki
* Aby użyć usługi StorSimple Device Manager do administrowania urządzeniem, przejdź do [korzystania z usługi StorSimple Device Manager w celu administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).
* Jeśli trzeba rozwiązać problem ze składnikiem urządzenia o stanie obniżony lub niepowodzenie, zapoznaj się ze [wskaźnikami monitorowania StorSimple](storsimple-monitoring-indicators.md).
* Aby zastąpić uszkodzony składnik sprzętowy, zobacz [StorSimple sprzętu sprzętowego](./storsimple-8000-hardware-component-replacement.md).
* Jeśli nadal występują problemy z urządzeniem, [skontaktuj się z firmą pomoc techniczna firmy Microsoft](storsimple-8000-contact-microsoft-support.md).