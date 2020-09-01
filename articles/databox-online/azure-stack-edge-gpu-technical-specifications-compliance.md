---
title: Microsoft Azure Stack Edge ze specyfikacją techniczną procesora GPU i zgodnością | Microsoft Docs
description: Dowiedz się więcej na temat specyfikacji technicznych i zgodności dla urządzenia Azure Stack Edge z procesorem GPU
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/26/2020
ms.author: alkohli
ms.openlocfilehash: 3f354655a612d4085b0a0de45ae1a6e5ee097ade
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89266667"
---
# <a name="technical-specifications-and-compliance-for-azure-stack-edge-with-gpu"></a>Specyfikacje techniczne i zgodność dla Azure Stack Edge z procesorem GPU 

Składniki sprzętowe Azure Stack krawędzi z dołączaniem procesora graficznego (GPU), które są zgodne ze specyfikacjami technicznymi i normami obowiązującymi w tym artykule. Specyfikacje techniczne opisują sprzęt, jednostki zasilacza (PSUs), pojemność magazynu, obudowy i standardy środowiskowe.

## <a name="compute-and-memory-specifications"></a>Specyfikacje obliczeniowe i pamięci

Urządzenie brzegowe Azure Stack ma następujące specyfikacje dotyczące obliczeń i pamięci:

| Specyfikacja           | Wartość                  |
|-------------------------|----------------------------|
| Procesor CPU                     | 2 X procesor Intel Xeon Silver 4214 (Kaskada Lake)            |
| Pamięć                  | 128 (8x16 GB) GB pamięci RAM                     |


## <a name="compute-acceleration-specifications"></a>Specyfikacje przyspieszania obliczeń

Procesor graficzny (GPU) jest dołączany do każdego Azure Stack urządzenia brzegowego, które umożliwia Kubernetes, uczenie głębokie oraz scenariusze uczenia maszynowego.

| Specyfikacja           | Wartość                  |
|-------------------------|----------------------------|
| Procesory GPU   | Jeden lub dwa procesory GPU w technologii nVidia T4 <br> Aby uzyskać więcej informacji, zobacz [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/).| 


## <a name="power-supply-unit-specifications"></a>Specyfikacje jednostek zasilacza

Urządzenie brzegowe Azure Stack ma dwie jednostki zasilacza 100-240 V (PSUs) z wentylatorami o wysokiej wydajności. Dwa PSUs zapewniają nadmiarową konfigurację zasilacza. Jeśli PSU nie powiedzie się, urządzenie będzie nadal działać normalnie na innych PSU, dopóki nie zostanie zastąpiony moduł zakończony niepowodzeniem. W poniższej tabeli przedstawiono specyfikacje techniczne PSUs.

| Specyfikacja           | 750 W PSU                  |
|-------------------------|----------------------------|
| Maksymalna moc wyjściowa    | 750 W                     |
| Częstotliwość               | 50/60 Hz                   |
| Wybór zakresu napięcia | Wybór na Wydziale: 100-240 V AC |
| Możliwość podłączenia gorąca           | Yes                        |


## <a name="network-interface-specifications"></a>Specyfikacje interfejsu sieciowego

Urządzenie brzegowe Azure Stack ma sześć interfejsów sieciowych PORT1-PORT6.

| Specyfikacja           | Opis                 |
|-------------------------|----------------------------|
|  Interfejsy sieciowe    | **2 X 1 GbE interfejsy** — 1 interfejs zarządzania port 1 jest używany do początkowej konfiguracji i jest domyślnie statyczny. Po zakończeniu wstępnej instalacji można użyć interfejsu dla danych z dowolnymi adresami IP. Jednak po zresetowaniu interfejs przywraca statyczny adres IP. <br>Innym interfejsem jest port 2 konfigurowany przez użytkownika, który może być używany do transferu danych i domyślnie jest DHCP. <br>**interfejsy 4 X 25 GbE** — te interfejsy danych, port 3 przez port 6 mogą być konfigurowane przez użytkownika jako serwer DHCP (domyślnie) lub statyczny. Mogą one również działać jako interfejsy 10 GbE.  | 

Urządzenie brzegowe Azure Stack ma następujący sprzęt sieciowy:

* **Niestandardowy Microsoft QLogic Cavium 25G NDC adapter** -port 1 przez port 4.
* **Mellanox Dual port 25G ConnectX-4 Channel karta sieciowa** -port 5 i port 6.

Poniżej znajdują się szczegółowe informacje dotyczące karty Mellanox:

| Parametr           | Opis                 |
|-------------------------|----------------------------|
| Model    | ConnectX®-4 LX interfejsu sieciowego karty sieciowej                      |
| Opis modelu               | 25GbE Dual-port SFP28; PCIe 3.0 x8; R6 ROHS                    |
| Numer części urządzenia (R640) | MCX4121A-ACAT  |
| PUSTY PSID (R640)           | MT_2420110034                         |

Aby uzyskać pełną listę obsługiwanych kabli, przełączników i urządzeń nadawczych dla tych kart sieciowych, przejdź do:

- [Macierz programu QLogic Cavium 25G NDC adaptera współdziałania](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).
- [Karta Mellanox Dual port 25G ConnectX-4 Channel Network zgodnych produktów](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products).  

## <a name="storage-specifications"></a>Specyfikacje magazynu

Azure Stack urządzenia brzegowe mają pięć 2,5 "interfejsu NVMe DC P4610 dysków SSD, każdy z pojemnością 1,6 TB. Stacja dysków rozruchowych to dysk SSD SATA 240 GB. Całkowita pojemność urządzenia wynosi około 8,28 TB. W poniższej tabeli wymieniono pojemność magazynu urządzenia.

|     Specyfikacja                          |     Wartość             |
|--------------------------------------------|-----------------------|
|    Rozruch dysków SSD SATA (SSD)      |    1                  |
|    Liczba dysków SSD interfejsu NVMe                     |    5                  |
|    Pojemność dysków SSD rozruchowego                       |    240 GB             |
|    Jedna pojemność SSD interfejsu NVMe                |    1,6 TB             |
|    Całkowita pojemność                          |    8,28 TB            |
|    Łączna pojemność użyteczna *                  |    ~ 7,95 TB          |
|    Kontroler SAS                          |    HBA330 12 GB/s     |


**Niektóre miejsca są zarezerwowane do użytku wewnętrznego.*

<!--Remove based on feedback from Ravi
## Other hardware specifications

Your Azure Stack Edge device also contains the following hardware:

* iDRAC baseboard management
* Performance fans
* Custom ID module-->

## <a name="enclosure-dimensions-and-weight-specifications"></a>Wymiary obudowy i specyfikacje wagi

W poniższych tabelach przedstawiono różne specyfikacje obudowy dla wymiarów i wag.

### <a name="enclosure-dimensions"></a>Wymiary obudowy

W poniższej tabeli przedstawiono wymiary obudowy urządzenia o rozmiarze 1U w milimetrach i calach.

|     Szaf     |     Milimetrach     |     15     |
|-------------------|---------------------|----------------|
|    Height         |    44,45            |    1,75 "       |
|    Width          |    434,1            |    17,09 "      |
|    Długość         |    740,4            |    29,15 "      |

W poniższej tabeli przedstawiono wymiary pakietu wysyłki w milimetrach i calach.

|     Pakiet     |     Milimetrach     |     15     |
|-------------------|---------------------|----------------|
|    Height         |    311,2            |    12,25 "          |
|    Width          |    642,8            |    25,31 "          |
|    Długość         |    1 051,1          |    41,38 "          |

### <a name="enclosure-weight"></a>Waga obudowy

Pakiet urządzenia waży 66 funtów. i wymaga od dwóch osób, aby je obsłużyć. Waga urządzenia zależy od konfiguracji obudowy.

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
|    Orientacja i zamontowanie             |    Standardowa 19 "montaż w stojaku (1U)                                                                                                                                                                                       |
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

[Wdrażanie Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md)
