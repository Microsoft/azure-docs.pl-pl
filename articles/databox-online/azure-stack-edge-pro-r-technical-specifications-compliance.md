---
title: Microsoft Azure Stack Edge specyfikacje techniczne i zgodność z pakietem Pro R | Microsoft Docs
description: Dowiedz się więcej na temat specyfikacji technicznych i zgodności dla urządzenia z Azure Stack EDGE Pro R
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: alkohli
ms.openlocfilehash: 3b323bf920bd884e821d03bf2def37471775e720
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312709"
---
# <a name="azure-stack-edge-pro-r-technical-specifications"></a>Azure Stack Edge — specyfikacje techniczne w wersji Pro R

Składniki sprzętowe urządzenia Azure Stack EDGE Pro R są zgodne ze specyfikacjami technicznymi opisanymi w tym artykule. Specyfikacje techniczne opisują jednostki zasilacza (PSUs), pojemność magazynu, obudowy i standardy środowiskowe.


## <a name="compute-memory-specifications"></a>Obliczenia, specyfikacje pamięci

Urządzenie Azure Stack EDGE Pro R ma następujące specyfikacje dotyczące obliczeń i pamięci:

| Specyfikacja  | Wartość                                             |
|----------------|---------------------------------------------------|
| Typ procesora       | Podwójny procesor Intel Xeon Silver 4114                   |
| Procesor CPU: RAW       | 20 całkowitej liczby rdzeni, 40 łącznie procesorów wirtualnych vCPU                    |
| Procesor CPU: użyteczny    | 32 procesorów wirtualnych vCPU                                          |
| Typ pamięci    | Zgodny z firmą Dell 16 GB RDIMM, 2666 MT/s, Podwójna ranga |
| Pamięć: RAW    | 256 GB pamięci RAM (16 x 16 GB)                           |
| Pamięć: użyteczna | 230 GB PAMIĘCI RAM                                        |

## <a name="compute-acceleration-specifications"></a>Specyfikacje przyspieszania obliczeń

Procesor graficzny (GPU) jest dołączany do każdego urządzenia, które umożliwia Kubernetes, uczenie głębokie i uczenie maszynowe.

| Specyfikacja           | Wartość                      |
|-------------------------|----------------------------|
| Procesory GPU   | Jeden procesor graficzny nVidia T4 <br> Aby uzyskać więcej informacji, zobacz [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/). | 

## <a name="power-supply-unit-specifications"></a>Specyfikacje jednostek zasilacza

Urządzenie Azure Stack EDGE Pro R ma dwie jednostki dostawy 100-240 V (PSUs) z wentylatorami o wysokiej wydajności. Dwa PSUs zapewniają nadmiarową konfigurację zasilacza. Jeśli PSU nie powiedzie się, urządzenie będzie nadal działać normalnie na innych PSU, dopóki nie zostanie zastąpiony moduł zakończony niepowodzeniem. W poniższej tabeli przedstawiono specyfikacje techniczne PSUs.

| Specyfikacja              | 550 W PSU                  |
|----------------------------|----------------------------|
| Maksymalna moc wyjściowa       | 550 W                      |
| Rozpraszanie ciepła (maksimum) | 2891 BTU/godz.                |
| Częstotliwość                  | 50/60 Hz                   |
| Wybór zakresu napięcia    | Wybór na Wydziale: 115-230 V AC |
| Możliwość podłączenia gorąca              | Tak                        |

## <a name="network-specifications"></a>Specyfikacje sieci

Urządzenie Azure Stack EDGE Pro R ma cztery interfejsy sieciowe — PORT1-PORT4.


|Specyfikacja         |Opis                       |
|----------------------|----------------------------------|
|Interfejsy sieciowe    |**2 x 1 GbE RJ45** <br> PORT 1 jest używany jako interfejs zarządzania dla początkowej konfiguracji i jest domyślnie statyczny. Po zakończeniu wstępnej instalacji można użyć interfejsu dla danych z dowolnymi adresami IP. Jednak po zresetowaniu interfejs przywraca statyczny adres IP. <br>Inny interfejs, PORT 2, który jest konfigurowany przez użytkownika, może być używany do transferu danych i domyślnie jest DHCP. |
|Interfejsy sieciowe    |**2 x 25 GbE SFP28** <br> Te interfejsy danych na PORTach 3 i 4 można skonfigurować jako DHCP (domyślne) lub statycznie. |

Urządzenie, na którym znajduje się Azure Stack EDGE Pro, ma następujący sprzęt sieciowy:

* **Mellanox Dual port 25G ConnectX-4 Channel karta sieciowa** -port 3 i 4. 

<!--Here are the details for the Mellanox card: MCX4421A-ACAN

| Parameter           | Description                 |
|-------------------------|----------------------------|
| Model    | ConnectX®-4 Lx EN network interface card                      |
| Model Description               | 25 GbE dual-port SFP28; PCIe3.0 x8; ROHS R6                    |
| Device Part Number (XR2) | MCX4421A-ACAN  |
| PSID (R640)           | MT_2420110034                         |-->
<!-- confirm w/ Ravi what is this-->

Aby zapoznać się z pełną listą obsługiwanych kabli, przełączników i urządzeń nadawczych dla tych kart sieciowych, przejdź do [karty Mellanox Dual port 25G ConnectX-4 Channel Network Compatible](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products).

## <a name="storage-specifications"></a>Specyfikacje magazynu

W przypadku urządzeń z systemem Azure Stack EDGE Pro są dostępne osiem dysków z danymi i dwa dyski SATA. 2, które stanowią dyski systemu operacyjnego. Aby uzyskać więcej informacji, przejdź do [dysków SATA (M. 2](https://en.wikipedia.org/wiki/M.2)).

#### <a name="storage-for-1-node-device"></a>Magazyn dla urządzenia 1-węzłowego

Poniższa tabela zawiera szczegółowe informacje dotyczące pojemności magazynu na urządzeniu z 1 węzłem.

|     Specyfikacja                          |     Wartość             |
|--------------------------------------------|-----------------------|
|    Liczba dysków półprzewodnikowych (dysków SSD)     |    8                  |
|    Pojemność jednego dysku SSD                     |    8 TB               |
|    Całkowita pojemność                          |    64 TB              |
|    Łączna pojemność użyteczna *                  |    ~ 42 TB            |

**Niektóre miejsca są zarezerwowane do użytku wewnętrznego.*

<!--#### Storage for 4-node device

The following table has the details for the storage capacity of the 4-node device.

|     Specification                          |     Value             |
|--------------------------------------------|-----------------------|
|    Number of solid-state drives (SSDs)     |    32 (4 X 8 disks for 4 devices)                |
|    Single SSD capacity                     |    8 TB               |
|    Total capacity                          |    256 TB              |
|    Total usable capacity*                  |    ~ 163 TB          |

**After mirroring and parity, and reserving some space for internal use.* -->


## <a name="enclosure-dimensions-and-weight-specifications"></a>Wymiary obudowy i specyfikacje wagi

W poniższych tabelach przedstawiono różne specyfikacje obudowy dla wymiarów i wag.

### <a name="enclosure-dimensions"></a>Wymiary obudowy 

W poniższej tabeli przedstawiono wymiary urządzenia i zasilacza UPS z niewytrzymałym przypadkiem w milimetrach i calach.

|     Szaf     |     Milimetrach     |     15     |
|-------------------|---------------------|----------------|
|    Height         |    301,2            |    11,86       |
|    Width          |    604,5            |    23,80       |
|    Długość         |    740,4            |    35,50       |

<!--#### For the 4-node system

For the 4-node system, the servers and the heater are shipped in a 5U case and the UPS are shipped in a 4U case.

The following table lists the dimensions of the 5U device case:  

|     Enclosure     |     Millimeters   |     Inches     |
|-------------------|-------------------|----------------|
|    Height         |    387.4          |    15.25       |
|    Width          |    604.5          |    23.80       |
|    Length         |    901.7          |    35.50       |

The following table lists the dimensions of the 4U UPS case: 

|     Enclosure     |     Millimeters   |     Inches    |
|-------------------|-------------------|---------------|
|    Height         |    342.9          |    13.5       |
|    Width          |    604.5          |   23.80       |
|    Length         |    901.7          |   35.50       |
-->

### <a name="enclosure-weight"></a>Waga obudowy 

Waga urządzenia zależy od konfiguracji obudowy.

|     Szaf                                 |     Waga          |
|-----------------------------------------------|---------------------|
|    Łączna waga urządzenia z 1 węzłem + odporność na wielkie litery z zakończeniami     |    ~ 114 funtów          |

<!--#### For the 4-node system

|     Enclosure                                 |     Weight          |
|-----------------------------------------------|---------------------|
|   Approximate weight of fully populated 4 devices + heater in 5U case     |    ~200 lbs.          |
|   Approximate weight of fully populated 4 UPS in 4U case    |    ~145 lbs.          |
-->

## <a name="enclosure-environment-specifications"></a>Specyfikacje środowiska obudowy

W tej sekcji przedstawiono specyfikacje dotyczące środowiska obudowy, takie jak temperatura, wibracje, wstrząsy i wysokość.


|     Specyfikacja              |     Wartość    |
|--------------------------------|-------------------------------------------------------------------|
|     Zakres temperatury          |     0 – 43 ° C (operacyjna)    |
|     Wibracj                  |     MIL-STD-810 Metoda 514,7 *<br>Procedura I kot 4, 20                  |
|     Trwa                      |     MIL-STD-810 Metoda 516,7 *<br>Procedura IV, logistyka                 |
|     Wysokości                   |     Działa: 10 000 stóp<br>Niedziałanie: 40 000 stóp          |

**Wszystkie odwołania są do MIL-STD-810G Change 1 (2014)*

## <a name="next-steps"></a>Następne kroki

- [Wdrażanie Azure Stack Edge](azure-stack-edge-placeholder.md)
