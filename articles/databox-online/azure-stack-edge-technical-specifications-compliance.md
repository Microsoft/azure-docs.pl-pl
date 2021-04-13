---
title: Microsoft Azure Stack Edge — specyfikacje techniczne i zgodność | Microsoft Docs
description: Dowiedz się więcej na temat specyfikacji technicznych i zgodności dla Twojego Azure Stack EDGE Pro
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/12/2021
ms.author: alkohli
ms.openlocfilehash: 91bb558d47e56ebbcbdf25b0276c2a247ec81b47
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312641"
---
# <a name="azure-stack-edge-pro-technical-specifications"></a>Azure Stack Edge — specyfikacje techniczne Pro

Składniki sprzętowe urządzenia Microsoft Azure Stack Edge w wersji zastosowanej w programie są zgodne ze specyfikacjami technicznymi i normami prawnymi opisanymi w tym artykule. Specyfikacje techniczne opisują jednostki zasilacza (PSUs), pojemność magazynu, obudowy i standardy środowiskowe.

## <a name="compute-memory-specifications"></a>Obliczenia, specyfikacje pamięci

Urządzenie Azure Stack EDGE Pro ma następujące specyfikacje dotyczące obliczeń i pamięci:

| Specyfikacja           | Wartość                             |
|-------------------------|-----------------------------------|
| Typ procesora                | Dwa procesory Intel Xeon Silver 4114 2,2 G |
| Procesor CPU: RAW                | 20 całkowitej liczby rdzeni, 40 łącznie procesorów wirtualnych vCPU    |
| Procesor CPU: użyteczny             | 32 procesorów wirtualnych vCPU                          |
| Typ pamięci             | 8 x 16 GB RDIMM                   |
| Pamięć: RAW             | 128 GB pamięci RAM (8 x 16 GB)           |
| Pamięć: użyteczna          | 102 GB PAMIĘCI RAM                        |


## <a name="fpga-specifications"></a>Specyfikacje FPGA

FPGA jest dołączana do każdego urządzenia z Azure Stack Edge, które umożliwia Machine Learning (ML) scenariusze.

| Specyfikacja           | Wartość                      |
|-------------------------|----------------------------|
| FPGA   | Intel Arria 10 <br> Dostępne modele sieci neuronowych Network (DNN) są takie same, jak te [obsługiwane przez wystąpienia FPGA chmury](../machine-learning/how-to-deploy-fpga-web-service.md#fpga-support-in-azure).|

## <a name="power-supply-unit-specifications"></a>Specyfikacje jednostek zasilacza

Urządzenie Azure Stack EDGE Pro ma dwie jednostki dostarczania 100-240 V (PSUs) z wentylatorami o wysokiej wydajności. Dwa PSUs zapewniają nadmiarową konfigurację zasilacza. Jeśli PSU nie powiedzie się, urządzenie będzie nadal działać normalnie na innych PSU, dopóki nie zostanie zastąpiony moduł zakończony niepowodzeniem. W poniższej tabeli przedstawiono specyfikacje techniczne PSUs.

| Specyfikacja           | 750 W PSU                  |
|-------------------------|----------------------------|
| Maksymalna moc wyjściowa    | 750 W                      |
| Częstotliwość               | 50/60 Hz                   |
| Wybór zakresu napięcia | Wybór na Wydziale: 100-240 V AC |
| Możliwość podłączenia gorąca           | Tak                        |

### <a name="azure-stack-edge-pro-power-cord-specifications-by-region"></a>Azure Stack Edge — specyfikacje dotyczące przewodu Power BI według regionu

Twoje urządzenie Azure Stack EDGE Pro wymaga przewodu zasilającego, które różni się w zależności od regionu platformy Azure.
Aby zapoznać się ze specyfikacją techniczną wszystkich obsługiwanych przewodów zasilających, zobacz [Azure Stack Edge — specyfikacje dotyczące przewodu Power BI według regionów](azure-stack-edge-technical-specifications-power-cords-regional.md).

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Azure Stack Edge Pro device.-->

## <a name="network-interface-specifications"></a>Specyfikacje interfejsu sieciowego

Twoje urządzenie Azure Stack EDGE Pro ma 6 interfejsów sieciowych, PORT1-PORT6.

| Specyfikacja           | Opis                 |
|-------------------------|----------------------------|
|  Interfejsy sieciowe    | 2 interfejsy 1 GbE — 1 do zarządzania, bez możliwości konfigurowania przez użytkownika, używany do konfiguracji początkowej. Drugi interfejs może być konfigurowany przez użytkownika i służyć do transferu danych. Domyślnie jest interfejsem DHCP. <br>2 interfejsy 25 GbE — mogą również działać jako interfejsy 10 GbE. Te interfejsy danych mogą być konfigurowane przez użytkownika jako DHCP (ustawienie domyślne) lub statyczne. <br> 2 interfejsy 25 GbE — te interfejsy danych mogą być konfigurowane przez użytkownika jako DHCP (ustawienie domyślne) lub statyczne.                  |

Używane karty sieciowe to:

| Specyfikacja           | Opis                 |
|-------------------------|----------------------------|
|Karta sieciowa (rNDC) |QLogic FastLinQ 41264 Dual port 25GbE SFP +, Dual port 1GbE, rNDC|
|Karta sieciowa PCI |Karta QLogic FastLinQ 41262 Zwei port 25Gbit/s SFP28|

Zapoznaj się z listą zgodności sprzętu z technologią Intel QLogic dla zgodnego konwertera interfejsów Gigabit (GBIC). Interfejs Gigabit Interface Converter (GBIC) nie jest uwzględniony w dostarczaniu Azure Stack Edge. 

## <a name="storage-specifications"></a>Specyfikacje magazynu

Urządzenia Azure Stack EDGE Pro mają 9 X 2,5 "interfejsu NVMe dysków SSD, każdy z pojemnością 1,6 TB. Z tych dysków SSD 1 to dysk systemu operacyjnego, a drugi 8 to dyski z danymi. Całkowita pojemność urządzenia wynosi około 12,5 TB. Poniższa tabela zawiera szczegółowe informacje dotyczące pojemności magazynu urządzenia.

|     Specyfikacja                          |     Wartość             |
|--------------------------------------------|-----------------------|
|    Liczba dysków półprzewodnikowych (dysków SSD)     |    8                  |
|    Pojemność jednego dysku SSD                     |    1,6 TB             |
|    Całkowita pojemność                          |    12,8 TB            |
|    Łączna pojemność użyteczna *                  |    ~ 12,5 TB          |

**Niektóre miejsca są zarezerwowane do użytku wewnętrznego.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Wymiary obudowy i specyfikacje wagi

W poniższych tabelach przedstawiono różne specyfikacje obudowy dla wymiarów i wag.

### <a name="enclosure-dimensions"></a>Wymiary obudowy

W poniższej tabeli przedstawiono wymiary obudowy w milimetrach i calach.

|     Szaf     |     Milimetrach    |     15     |
|-------------------|--------------------|----------------|
|    Height         |    44,45           |    1,75 "       |
|    Width          |    434,1           |    17,09 "      |
|    Długość         |    740,4           |    29,15 "      |

W poniższej tabeli przedstawiono wymiary pakietu wysyłki w milimetrach i calach.

|     Pakiet       |     Milimetrach     |     15     |
|-------------------|---------------------|----------------|
|    Height         |    311,2            |    12,25 "      |
|    Width          |    642,8            |    25,31 "      |
|    Długość         |   1 051,1           |    41,38 "      |

### <a name="enclosure-weight"></a>Waga obudowy

Pakiet urządzenia waży 61 funtów. i wymaga od dwóch osób, aby je obsłużyć. Waga urządzenia zależy od konfiguracji obudowy.

|     Szaf                                 |     Waga          |
|-----------------------------------------------|---------------------|
|    Łączna waga włącznie z opakowaniem       |    61 funtów          |
|    Waga urządzenia                       |    35 funtów          |

## <a name="enclosure-environment-specifications"></a>Specyfikacje środowiska obudowy

W tej sekcji przedstawiono specyfikacje dotyczące środowiska obudowy, takie jak temperatura, wilgotność i wysokość.

### <a name="temperature-and-humidity"></a>Temperatura i wilgotność

|     Szaf         |     Zakres temperatury otoczenia     |     Wilgotność względna     |     Maksymalny punkt Rosa     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    Operacyjne        |    10 OC – 35 °C (50 °F-86 °F)         |    10%-80% bez kondensacji.         |    29 °C (84 °F)            |
|    Niedziałające    |    -40 °C do 65 °C (-40 °F-149 °F)     |    5%-95% bez kondensacji.          |    33 °C (91 °F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Przepływ powietrza, wysokość, wstrząsy, wibracje, Orientacja, bezpieczeństwo i EMC

|     Szaf                           |     Specyfikacje operacyjne                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    Przepływ powietrza                              |    Przepływ powietrza systemu jest przedsunięty do tyłu. System musi być obsługiwany przy użyciu instalacji z niską ilością wylotu. <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    Maksymalna wysokość, operacyjna        |    3048 mierników (10 000 stóp) z maksymalną temperaturą działania, która jest określana przez niesklasyfikowane [wymagania dotyczące temperatury działania](#operating-temperature-de-rating-specifications).                                                                                |
|    Maksymalna wysokość, niedziałająca    |    12 000 metrów (39 370 stóp)                                                                                                                                                                                         |
|    Wstrząsy, operacyjne                   |    6 G dla 11 milisekund w 6 orientacjach                                                                                                                                                                         |
|    Wstrząsy, niedziałające               |    71 G przez 2 milisekundy w 6 orientacjach                                                                                                                                                                           |
|    Wibracje, operacyjne               |    0,26 G<sub>RMS</sub> 5 hz do 350 Hz                                                                                                                                                                                     |
|    Wibracje, niedziałające           |    1,88 G<sub>RMS</sub> 10 Hz do 500 Hz przez 15 minut (przetestowane wszystkie sześć stron).                                                                                                                                                  |
|    Orientacja i zamontowanie             |    19 "montaż w stojaku                                                                                                                                                                                        |
|    Bezpieczeństwo i zatwierdzenia                 |    EN 60950-1:2006 + A1:2010 + a2:2013 + A11:2009 + A12:2011/IEC 60950-1:2005 ED2 + a1:2009 + a2:2013 EN 62311:2008                                                                                                                                                                       |
|    ELEKTROMAGNETYCZNEJ                                  |    FCC A, ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 + A1:2015/CISPR 24:2010 + A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (Klasa D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    Energetyczna             |    Rozporządzenie Komisji (UE) nr. 617/2013                                                                                                                                                                                        |
|    RoHS           |    EN 50581:2012                                                                                                                                                                                        |

### <a name="operating-temperature-de-rating-specifications"></a>Specyfikacje dotyczące nieklasyfikacji temperatury działania

|     Obniżenie temperatury działania     |     Zakres temperatury otoczenia                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    Do 35 °C (95 °F)                       |    Maksymalną temperaturę zmniejsza się o 1 c/300 m (1 °F/547 ft) powyżej 950 m (3 117 ft).    |
|    35 oC do 40 °C (95 °F do 104 °F)            |    Maksymalna temperatura jest zmniejszona o 1 c/175 m (1 °F/319 ft) powyżej 950 m (3 117 ft).    |
|    40 °C do 45 °C (104 °F do 113 °F)           |    Maksymalna temperatura jest zmniejszona o 1 °C/125 m (1 °F/950 ft) powyżej m (3 117 ft).    |

## <a name="next-steps"></a>Następne kroki

- [Wdróż swoją Azure Stack EDGE Pro](azure-stack-edge-deploy-prep.md)