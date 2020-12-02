---
title: Microsoft Azure Stack i zgodność ze standardami i zgodnością w programie Edge Microsoft Docs
description: Dowiedz się więcej na temat specyfikacji technicznych i zgodności dla urządzenia z Azure Stack Edge mini R
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: alkohli
ms.openlocfilehash: e6dff06e92126e2fc4538273e229dcb0904e3101
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467452"
---
# <a name="azure-stack-edge-mini-r-technical-specifications"></a>Wymagania techniczne dotyczące Azure Stack Edge

Składniki sprzętowe urządzenia Microsoft Azure Stack Edge mini R są zgodne ze specyfikacjami technicznymi i normami prawnymi opisanymi w tym artykule. Specyfikacje techniczne opisują procesor CPU, pamięć, jednostki zasilacza (PSUs), pojemność magazynu, wymiary obudowy i wagi.


## <a name="compute-memory-specifications"></a>Obliczenia, specyfikacje pamięci

Urządzenie Azure Stack Edge mini R ma następujące specyfikacje dotyczące obliczeń i pamięci:

| Specyfikacja           | Wartość                  |
|-------------------------|------------------------|
| Procesor CPU    | 16-rdzeniowy procesor, Intel Xeon-D 1577 |
| Pamięć              | 48 GB pamięci RAM (2400 MT/s)                  |


## <a name="compute-acceleration-specifications"></a>Specyfikacje przyspieszania obliczeń

Jednostka przetwarzania wzrokowego (VPU) jest uwzględniona na każdym urządzeniu, na którym znajduje się urządzenie typu mini R o Azure Stack Edge, które umożliwia Kubernetes, głębokie neuronowych sieci i oparty na komputerach.

| Specyfikacja           | Wartość                  |
|-------------------------|------------------------|
| Karta przyspieszania obliczeń         | Intel Movidius wyposażono X VPU <br> Aby uzyskać więcej informacji, zobacz [Intel Movidius wyposażono X VPU](https://www.movidius.com/MyriadX) |


## <a name="storage-specifications"></a>Specyfikacje magazynu

Urządzenie Azure Stack Edge mini R ma 1 dysk danych i 1 dysk rozruchowy (który służy jako magazyn systemu operacyjnego). W poniższej tabeli przedstawiono szczegółowe informacje na temat pojemności magazynu urządzenia.

|     Specyfikacja                          |     Wartość             |
|--------------------------------------------|-----------------------|
|    Liczba dysków półprzewodnikowych (dysków SSD)     |    2 X 1 TB dysków <br> Jeden dysk danych i jeden dysk rozruchowy                  |
|    Pojemność jednego dysku SSD                     |    1 TB               |
|    Całkowita pojemność (tylko dane)              |    1 TB              |
|    Łączna pojemność użyteczna *                  |    ~ 750 GB        |

**Niektóre miejsca są zarezerwowane do użytku wewnętrznego.*

## <a name="network-specifications"></a>Specyfikacje sieci

Urządzenie Azure Stack Edge mini R ma następujące specyfikacje dotyczące sieci:


|Specyfikacja  |Wartość  |
|---------|---------|
|Interfejsy sieciowe    |2 x 10 GbE SFP + <br> Przedstawiane jako PORT 3 i 4 w lokalnym interfejsie użytkownika           |
|Interfejsy sieciowe    |2 x 1 GbE RJ45 <br> Przedstawiane jako PORT 1 i 2 w lokalnym interfejsie użytkownika          |
|Wi-Fi   |Standard 802.11ac         |


## <a name="power-supply-unit-specifications"></a>Specyfikacje jednostek zasilacza

Urządzenie Azure Stack Edge mini R obejmuje zewnętrzną kartę 85 W ZASILANiu z zasilaniem i naliczeniem baterii.

W poniższej tabeli przedstawiono specyfikacje jednostek zasilacza:

| Specyfikacja           | Wartość                      |
|-------------------------|----------------------------|
| Maksymalna moc wyjściowa    | 85 W                       |
| Częstotliwość               | 50/60 Hz                   |
| Wybór zakresu napięcia | Wybór na Wydziale: 100-240 V AC |



## <a name="included-battery"></a>Wliczone baterie

Urządzenie Azure Stack Edge mini R obejmuje również baterię dołączaną w ramach zasilacza. 

Dodatkowy typ baterii 2590 może być używany w połączeniu z baterią dołączania, aby zwiększyć użycie urządzenia między opłatami. Ta bateria powinna być zgodna ze wszystkimi przepisami dotyczącymi bezpieczeństwa, transportu i środowiska, które mają zastosowanie w kraju użytkowania.


| Specyfikacja           | Wartość                      |
|-------------------------|----------------------------|
| Pojemność baterii dołączania | 73 WH                    |

## <a name="enclosure-dimensions-and-weight-specifications"></a>Wymiary obudowy i specyfikacje wagi

W poniższych tabelach przedstawiono różne specyfikacje obudowy dla wymiarów i wag.

### <a name="enclosure-dimensions"></a>Wymiary obudowy

W poniższej tabeli przedstawiono wymiary urządzenia i USP z niewytrzymałym przypadkiem w milimetrach i calach.

|     Szaf     |     Milimetrach     |     15     |
|-------------------|---------------------|----------------|
|    Height         |    68            |    2,68          |
|    Width          |    208          |      8,19          |
|    Długość          |   259           |    10,20          |


### <a name="enclosure-weight"></a>Waga obudowy

Poniższa tabela zawiera listę wag urządzeń, w tym baterii.

|     Szaf                                 |     Waga          |
|-----------------------------------------------|---------------------|
|    Łączna waga urządzenia     |    7 funtów          |

## <a name="enclosure-environment-specifications"></a>Specyfikacje środowiska obudowy


W tej sekcji przedstawiono specyfikacje dotyczące środowiska obudowy, takie jak temperatura, wilgotność i wysokość.


|     Specyfikacje             |     Opis                                                          |
|--------------------------------|--------------------------------------------------------------------------|
|     Zakres temperatury          |     0 – 43 ° C (operacyjna)                                              |
|     Wibracj                  |     MIL-STD-810 Metoda 514,7 *<br> Procedura I kot 4, 20                  |
|     Trwa                      |     MIL-STD-810 Metoda 516,7 *<br> Procedura IV, logistyka                 |
|     Wysokości                   |     Działa: 10 000 stóp<br> Niedziałanie: 40 000 stóp          |

**Wszystkie odwołania są do MIL-STD-810G Change 1 (2014)*


## <a name="next-steps"></a>Następne kroki

- [Wdrażanie Azure Stack Edge](azure-stack-edge-placeholder.md)
