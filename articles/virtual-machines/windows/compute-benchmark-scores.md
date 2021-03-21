---
title: Oblicz wyniki testów porównawczych dla maszyn wirtualnych z systemem Windows Azure
description: Porównaj wyniki testów porównawczych SPECint dla maszyn wirtualnych platformy Azure z systemem Windows Server.
author: cynthn
ms.service: virtual-machines
ms.subservice: azure-compute-unit
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 04/29/2020
ms.author: cynthn
ms.reviewer: davberg
ms.openlocfilehash: bf809b695e8df14288c2c55e580ee76d37f487d4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102550777"
---
# <a name="compute-benchmark-scores-for-windows-vms"></a>Oblicz wyniki testów porównawczych dla maszyn wirtualnych z systemem Windows
Poniższe wyniki testu porównawczego SPECInt pokazują wydajność obliczeń dla wybranych maszyn wirtualnych platformy Azure z systemem Windows Server. Wyniki testów porównawczych obliczeń są również dostępne dla [maszyn wirtualnych z systemem Linux](../linux/compute-benchmark-scores.md).


## <a name="av2---general-compute"></a>Av2 — ogólne obliczenia

| Rozmiar | Procesory wirtualne | Węzły NUMA | Procesor CPU | Uruchamianie | Średnia stawka podstawowa | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standardowa_A1_v2 | 1 | 1 | Procesor Intel (R) Xeon (R) E5-2660 0 @ 2.20 GHz | 12 | 14,2 | 0.3 | 
| Standardowa_A1_v2 | 1 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 9 | 13.2 | 0,6 | 
| Standardowa_A1_v2 | 1 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 10 | 14,1 | 0.7 | 
| Standardowa_A2_v2 | 2 | 1 | Procesor Intel (R) Xeon (R) E5-2660 0 @ 2.20 GHz | 14 | 28,9 | 0,6 | 
| Standardowa_A2_v2 | 2 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 10 | 27,4 | 1.6 | 
| Standardowa_A2_v2 | 2 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 17 | 28,9 | 1.8 | 
| Standardowa_A2m_v2 | 2 | 1 | Procesor Intel (R) Xeon (R) E5-2660 0 @ 2.20 GHz | 14 | 29,0 | 0,5 | 
| Standardowa_A2m_v2 | 2 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 11 | 26,3 | 0,8 | 
| Standardowa_A2m_v2 | 2 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 21 | 28,4 | 1.0 | 
| Standardowa_A4_v2 | 4 | 1 | Procesor Intel (R) Xeon (R) E5-2660 0 @ 2.20 GHz | 27 | 56,6 | 1.0 | 
| Standardowa_A4_v2 | 4 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 13 | 52,8 | 2.0 | 
| Standardowa_A4_v2 | 4 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 15 | 52,1 | 4.5 | 
| Standardowa_A4m_v2 | 4 | 1 | Procesor Intel (R) Xeon (R) E5-2660 0 @ 2.20 GHz | 17 | 56,4 | 1.8 | 
| Standardowa_A4m_v2 | 4 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 6 | 53,4 | 1.9 | 
| Standardowa_A4m_v2 | 4 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 23 | 57,1 | 3,6 | 
| Standardowa_A8_v2 | 8 | 1 | Procesor Intel (R) Xeon (R) E5-2660 0 @ 2.20 GHz | 14 | 109,1 | 1.6 | 
| Standardowa_A8_v2 | 8 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 6 | 101,5 | 2,8 | 
| Standardowa_A8_v2 | 8 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 11 | 101,9 | 2.7 | 
| Standardowa_A8m_v2 | 8 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 11 | 101,4 | 1.2 | 
| Standardowa_A8m_v2 | 8 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 10 | 104,5 | 5,1 | 
| Standardowa_A8m_v2 | 8 | 2 | Procesor Intel (R) Xeon (R) E5-2660 0 @ 2.20 GHz | 13 | 111,6 | 2.3 | 


> [!NOTE]
> Maszyny wirtualne z serii Av2 można wdrażać na różnych typach sprzętu i procesorach (jak pokazano powyżej). Maszyny wirtualne z serii Av2 mają wydajność procesora CPU i konfiguracje pamięci, które najlepiej pasują do obciążeń poziomu wejścia, takich jak programowanie i testowanie. Rozmiar jest ograniczany w celu zapewnienia stosunkowo spójnej wydajności procesora dla uruchomionego wystąpienia, niezależnie od sprzętu, na którym jest wdrożony. jednak oprogramowanie, które korzysta z konkretnych nowszych optymalizacji procesora, może zobaczyć bardziej znaczące różnice między różnymi typami procesorów.

## <a name="b---burstable"></a>B — do serii

(Zaktualizowano 2019-10-23 do 2019-11-03 PBI: 5604451)

| Rozmiar | Procesory wirtualne | Węzły NUMA | Procesor CPU | Uruchamianie | Średnia stawka podstawowa | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standard_B1ms | 1 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 9 | 6.3 | 0,2 | 
| Standard_B1ms | 1 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 47 | 6.4 | 0,2 | 
| Standard_B2ms | 2 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 36 | 19,8 | 0,8 | 
| Standard_B2s | 2 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 2 | 13,0 | 0,0 | 
| Standard_B2s | 2 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 29 | 13,0 | 0,5 | 
| Standard_B4ms | 4 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 6 | 27,1 | 1.0 | 
| Standard_B4ms | 4 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 43 | 28,3 | 0.7 | 
| Standard_B8ms | 8 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 3 | 42,0 | 0,0 | 
| Standard_B8ms | 8 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 25 | 41,4 | 0,9 | 
| Standard_B12ms | 12 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 lub v4 | 19 | 58,9 | 2.3 |
| Standard_B16ms | 16 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 lub v4 | 18 | 75,4 | 2.1 |
| Standard_B20ms | 20 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 lub v4| 2 | 90,6 | 1.3 |


>[!NOTE]
> Maszyny wirtualne z serii B są przeznaczone do obciążeń z wymaganymi możliwościami wydajności. Wystąpienia maszyn wirtualnych gromadzą kredyty przy użyciu mniejszej niż jej podstawy. Gdy maszyna wirtualna ma Zakumulowany kredyt, maszyna wirtualna może przekroczyć poziom odniesienia do 100%, aby spełnić krótkie wymagania dotyczące obciążeń procesora. Czas szeregowania zależy od dostępnych kredytów, które są funkcją rozmiaru i czasu maszyn wirtualnych.  
>
> Specyfikacja int to dość długotrwały test, który zazwyczaj wyczerpuje dostępne środki na korzystanie z serii.  W związku z tym Powyższe liczby są bliżej wydajności linii bazowej maszyny wirtualnej (chociaż mogą one odzwierciedlać czas szeregowania między przebiegami).  W przypadku krótkich, obciążeń (typowych dla serii B) wydajność zwykle będzie bliżej tego, czy jest to wersja z serii ds..

## <a name="dsv3---general-compute--premium-storage"></a>DSv3 — ogólne obliczenia i Premium Storage

(Zaktualizowano 2019-10-23 do 2019-11-03 PBI: 5604451)

| Rozmiar | Procesory wirtualne | Węzły NUMA | Procesor CPU | Uruchamianie | Średnia stawka podstawowa | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standardowa_D2s_v3 | 2 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 10 | 40,8 | 2.3 | 
| Standardowa_D2s_v3 | 2 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 52 | 43,3 | 2.1 | 
| Standardowa_D4s_v3 | 4 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 21 | 77,9 | 2,6 | 
| Standardowa_D4s_v3 | 4 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 29 | 82,3 | 2.5 | 
| Standardowa_D8s_v3 | 8 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 7 | 148,3 | 1.9 | 
| Standardowa_D8s_v3 | 8 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 28 | 155,4 | 5,6 | 
| Standardowa_D16s_v3 | 16 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 3 | 275,7 | 5,1 | 
| Standardowa_D16s_v3 | 16 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 38 | 298,2 | 4.4 | 
| Standard_D32s_v3 | 32 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 24 | 545,8 | 10.5 | 
| Standard_D32s_v3 | 32 | 2 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 9 | 535,6 | 12,6 | 
| Standard_D32 — 8s_v3 | 8 |  | Procesor Intel (R) Xeon (R) E5-2673 v3 lub v4 | 6 | 166,0 | 8.8 |
| Standard_D32 — 16s_v3 | 16 |  | Procesor Intel (R) Xeon (R) E5-2673 v3 lub v4 | 4 | 300,8 | 6.4 |
| Standard_D48s_v3 | 48 | 2 | Procesor Intel (R) Xeon (R) E5-2673 v3 lub v4 | 1 | 838,0 | 0,0 |
| Standard_D64s_v3 | 64 | 2 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 35 | 1070,6 | 2,4 | 
| Standard_D64 — 16s_v3 | 16 |  | Procesor Intel (R) Xeon (R) E5-2673 v3 lub v4 | 4 | 340,0 | 21,4 |
| Standard_D64 — 32s_v3 | 32 |  | Procesor Intel (R) Xeon (R) E5-2673 v3 lub v4 | 3 | 592,3 | 1.5 |

## <a name="dv3---general-compute"></a>Dv3 — ogólne obliczenia

(Zaktualizowano 2019-10-23 do 2019-11-03 PBI: 5604451)

| Rozmiar | Procesory wirtualne | Węzły NUMA | Procesor CPU | Uruchamianie | Średnia stawka podstawowa | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standardowa_D2_v3 | 2 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 10 | 38,6 | 1.8 | 
| Standardowa_D2_v3 | 2 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 24 | 41,8 | 3.3 | 
| Standardowa_D4_v3 | 4 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 17 | 77,8 | 1.3 | 
| Standardowa_D4_v3 | 4 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 45 | 82,7 | 4.5 | 
| Standardowa_D8_v3 | 8 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 9 | 146,7 | 10,4 | 
| Standardowa_D8_v3 | 8 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 27 | 159,9 | 8.3 | 
| Standardowa_D16_v3 | 16 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 10 | 274,1 | 3,8 | 
| Standardowa_D16_v3 | 16 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 32 | 300,7 | 8.8 | 
| Standard_D32_v3 | 32 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 24 | 549,3 | 11,1 | 
| Standard_D32_v3 | 32 | 2 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 7 | 538,6 | 9,4 | 
| Standard_D48_v3 | 48 |  |  Procesor Intel (R) Xeon (R) E5-2673 v3 lub v4 | 3 | 839,7 | 14,4 |
| Standard_D64_v3 | 64 | 2 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 32 | 1070,6 | 12,4 | 

## <a name="dsv2---storage-optimized"></a>DSv2 — zoptymalizowane pod kątem magazynu

| Rozmiar | Procesory wirtualne | Węzły NUMA | Procesor CPU | Uruchamianie | Średnia stawka podstawowa | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standardowa_DS1_v2 | 1 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 12 | 33,0 | 1.1 | 
| Standardowa_DS1_v2 | 1 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 37 | 33,8 | 2.5 | 
| Standard_DS2_v2 | 2 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 33 | 63,9 | 1.7 | 
| Standard_DS2_v2 | 2 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 32 | 66,6 | 4.8 | 
| Standardowa_DS3_v2 | 4 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 15 | 125,5 | 3.2 | 
| Standardowa_DS3_v2 | 4 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 47 | 130,1 | 4.3 | 
| Standardowa_DS4_v2 | 8 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 23 | 235,7 | 6.6 | 
| Standardowa_DS4_v2 | 8 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 34 | 249,4 | 2,8 | 
| Standard_DS5_v2 | 16 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 11 | 414,9 | 5,1 | 
| Standard_DS5_v2 | 16 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 31 | 470,6 | 5.7 | 
| Standardowa_DS11_v2 | 2 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 22 | 66,3 | 2,8 | 
| Standardowa_DS11_v2 | 2 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 34 | 64,8 | 2,8 | 
| Standard_DS11 — 1_v2 | 1 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 17 | 33,6 | 1.8 | 
| Standard_DS11 — 1_v2 | 1 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 41 | 36,0 | 1.7 | 
| Standardowa_DS12_v2 | 4 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 10 | 126,8 | 2.7 | 
| Standardowa_DS12_v2 | 4 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 30 | 127,5 | 3.3 | 
| Standard_DS12 — 1_v2 | 1 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 20 | 33,5 | 1.4 | 
| Standard_DS12 — 1_v2 | 1 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 30 | 34,8 | 2,4 | 
| Standard_DS12 — 2_v2 | 2 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 17 | 65,5 | 2.3 | 
| Standard_DS12 — 2_v2 | 2 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 33 | 67,7 | 5,1 | 
| Standardowa_DS13_v2 | 8 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 20 | 234,1 | 7.1 | 
| Standardowa_DS13_v2 | 8 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 23 | 248,0 | 2,2 | 
| Standard_DS13 — 2_v2 | 2 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 17 | 65,2 | 3,1 | 
| Standard_DS13 — 2_v2 | 2 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 15 | 72,8 | 3,8 | 
| Standard_DS13 — 4_v2 | 4 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 24 | 126,1 | 4.3 | 
| Standard_DS13 — 4_v2 | 4 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 27 | 133,3 | 2,8 | 
| Standardowa_DS14_v2 | 16 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 22 | 469,5 | 6,9 | 
| Standardowa_DS14_v2 | 16 | 2 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 16 | 456,6 | 7.3 | 
| Standard_DS14 — 4_v2 | 4 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 28 | 132,8 | 6.6 | 
| Standard_DS14 — 4_v2 | 4 | 2 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 16 | 125,1 | 4.8 | 
| Standard_DS14 — 8_v2 | 8 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 27 | 251,3 | 2,4 | 
| Standard_DS14 — 8_v2 | 8 | 2 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 14 | 247,4 | 10,2 | 
| Standard_DS15_v2 | 20 | 2 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 45 | 546,1 | 10.5 | 

## <a name="dv2---general-compute"></a>Dv2 — ogólne obliczenia

| Rozmiar | Procesory wirtualne | Węzły NUMA | Procesor CPU | Uruchamianie | Średnia stawka podstawowa | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standardowa_D1_v2 | 1 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 30 | 33,5 | 1.7 | 
| Standardowa_D1_v2 | 1 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 31 | 34,7 | 2.5 | 
| Standardowa_D2_v2 | 2 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 18 | 66,0 | 1.8 | 
| Standardowa_D2_v2 | 2 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 31 | 69,9 | 5.0 | 
| Standardowa_D3_v2 | 4 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 27 | 127,7 | 3.0 | 
| Standardowa_D3_v2 | 4 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 27 | 133,4 | 9,1 | 
| Standardowa_D4_v2 | 8 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 15 | 238,7 | 4.4 | 
| Standardowa_D4_v2 | 8 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 36 | 248,9 | 4.8 | 
| Standardowa_D5_v2 | 16 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 9 | 413,9 | 14,1 | 
| Standardowa_D5_v2 | 16 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 27 | 470,2 | 8.1 | 
| Standardowa_D5_v2 | 16 | 2 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 5 | 466,0 | 0,0 | 
| Standardowa_D11_v2 | 2 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 22 | 66,4 | 2.9 | 
| Standardowa_D11_v2 | 2 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 27 | 69,0 | 6.7 | 
| Standardowa_D12_v2 | 4 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 24 | 127,7 | 4,6 | 
| Standardowa_D12_v2 | 4 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 20 | 135,9 | 9,3 | 
| Standardowa_D13_v2 | 8 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 16 | 237,4 | 6.6 | 
| Standardowa_D13_v2 | 8 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 28 | 250,2 | 3,8 | 
| Standardowa_D14_v2 | 16 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 23 | 473,0 | 9,4 | 
| Standardowa_D14_v2 | 16 | 2 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 17 | 443,9 | 18,8 | 
| Standard_D15_v2 | 20 | 2 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 37 | 558,8 | 8.4 | 

## <a name="esv3---memory-optimized--premium-storage"></a>Esv3 — zoptymalizowane pod kątem pamięci + Premium Storage

| Rozmiar | Procesory wirtualne | Węzły NUMA | Procesor CPU | Uruchamianie | Średnia stawka podstawowa | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standardowa_E2s_v3 | 2 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 39 | 42,5 | 2,2 | 
| Standardowa_E4s_v3 | 4 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 28 | 81,4 | 3.3 | 
| Standardowa_E8s_v3 | 8 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 29 | 156,3 | 5,1 | 
| Standard_E8 — 2s_v3 | 2 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 57 | 41,8 | 2,6 | 
| Standard_E8 — 4s_v3 | 4 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 45 | 82,9 | 3.0 | 
| Standardowa_E16s_v3 | 16 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 31 | 295,7 | 4.5 | 
| Standard_E16 — 4s_v3 | 4 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 45 | 82,7 | 3,8 | 
| Standard_E16 — 8s_v3 | 8 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 39 | 158,3 | 4.5 | 
| Standard_E20s_v3 | 20 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 27 | 369,7 | 3.2 | 
| Standardowa_E32s_v3 | 32 | 2 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 31 | 577,9 | 9,4 | 
| Standard_E32 — 8s_v3 | 8 | 2 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 31 | 163,4 | 6.8 | 
| Standard_E32 — 16s_v3 | 16 | 2 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 41 | 307,1 | 8.7 | 
| Standard_E4 — 2s_v3 | 2 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 65 | 41,9 | 2,4 | 
| Standardowa_E64s_v3 | 64 | 2 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 1 | 1080,0 | 0,0 | 
| Standard_E64 — 16s_v3 | 16 | 2 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 3 | 334,3 | 1.5 | 
| Standard_E64 — 32s_v3 | 32 | 2 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 4 | 592,5 | 4.4 | 

## <a name="eisv3---memory-opt--premium-storage-isolated"></a>Eisv3-Memory opt + Premium Storage (izolowany)

| Rozmiar | Procesory wirtualne | Węzły NUMA | Procesor CPU | Uruchamianie | Średnia stawka podstawowa | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standard_E64is_v3 | 64 | 2 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 28 | 1073,9 | 5.7 | 

## <a name="ev3---memory-optimized"></a>EV3 — zoptymalizowane pod kątem pamięci

| Rozmiar | Procesory wirtualne | Węzły NUMA | Procesor CPU | Uruchamianie | Średnia stawka podstawowa | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standardowa_E2_v3 | 2 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 41 | 41,2 | 2,4 | 
| Standardowa_E4_v3 | 4 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 43 | 81,4 | 5.3 | 
| Standardowa_E8_v3 | 8 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 39 | 157,4 | 8.1 | 
| Standardowa_E16_v3 | 16 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 49 | 301,6 | 8,9 | 
| Standard_E20_v3 | 20 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 35 | 371,0 | 6,9 | 
| Standardowa_E32_v3 | 32 | 2 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 35 | 579,9 | 16,1 | 
| Standardowa_E64_v3 | 64 | 2 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 31 | 1080,0 | 11,3 | 

## <a name="eiv3---memory-optimized-isolated"></a>Eiv3 — zoptymalizowane pod kątem pamięci (izolowany)

| Rozmiar | Procesory wirtualne | Węzły NUMA | Procesor CPU | Uruchamianie | Średnia stawka podstawowa | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standard_E64i_v3 | 64 | 2 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 28 | 1081,4 | 11,1 | 

## <a name="fsv2---compute--storage-optimized"></a>Fsv2-COMPUTE + magazyn zoptymalizowany

| Rozmiar | Procesory wirtualne | Węzły NUMA | Procesor CPU | Uruchamianie | Średnia stawka podstawowa | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standard_F2s_v2 | 2 | 1 | Procesor Intel (R) Xeon (R) Platinum 8168 CPU @ 2.70 GHz | 46 | 56,5 | 2,4 | 
| Standard_F4s_v2 | 4 | 1 | Procesor Intel (R) Xeon (R) Platinum 8168 CPU @ 2.70 GHz | 60 | 110,2 | 4.7 | 
| Standard_F8s_v2 | 8 | 1 | Procesor Intel (R) Xeon (R) Platinum 8168 CPU @ 2.70 GHz | 36 | 215,2 | 5.3 | 
| Standard_F16s_v2 | 16 | 1 | Procesor Intel (R) Xeon (R) Platinum 8168 CPU @ 2.70 GHz | 36 | 409,3 | 15,5 | 
| Standard_F32s_v2 | 32 | 1 | Procesor Intel (R) Xeon (R) Platinum 8168 CPU @ 2.70 GHz | 31 | 760,9 | 16,9 | 
| Standard_F64s_v2 | 64 | 2 | Procesor Intel (R) Xeon (R) Platinum 8168 CPU @ 2.70 GHz | 33 | 1440,9 | 26,0 | 
| Standard_F72s_v2 | 72 | 2 | Procesor Intel (R) Xeon (R) Platinum 8168 CPU @ 2.70 GHz | 29 | 1372,1 | 8.2 | 

## <a name="fs---compute-and-storage-optimized"></a>FS — zoptymalizowane pod kątem obliczeń i magazynu

| Rozmiar | Procesory wirtualne | Węzły NUMA | Procesor CPU | Uruchamianie | Średnia stawka podstawowa | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standardowa_F1s | 1 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 31 | 33,2 | 1.0 | 
| Standardowa_F1s | 1 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 41 | 35,1 | 2.0 | 
| Standardowa_F2s | 2 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 18 | 63,7 | 1.8 | 
| Standardowa_F2s | 2 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 21 | 66,6 | 3,8 | 
| Standardowa_F4s | 4 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 14 | 128,4 | 2.9 | 
| Standardowa_F4s | 4 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 25 | 127,7 | 4.5 | 
| Standardowa_F8s | 8 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 11 | 234,9 | 3.7 | 
| Standardowa_F8s | 8 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 19 | 251,2 | 4.5 | 
| Standardowa_F16s | 16 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 9 | 413,9 | 3,6 | 
| Standardowa_F16s | 16 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 36 | 471,8 | 7,5 | 

## <a name="f---compute-optimized"></a>F — Optymalizacja obliczeń

| Rozmiar | Procesory wirtualne | Węzły NUMA | Procesor CPU | Uruchamianie | Średnia stawka podstawowa | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standardowa_F1 | 1 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 15 | 32,8 | 1.8 | 
| Standardowa_F1 | 1 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 13 | 33,3 | 2.0 | 
| Standardowa_F2 | 2 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 27 | 64,9 | 6.0 | 
| Standardowa_F2 | 2 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 21 | 67,8 | 4,9 | 
| Standardowa_F4 | 4 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 18 | 128,4 | 3.3 | 
| Standardowa_F4 | 4 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 32 | 132,1 | 7,8 | 
| Standardowa_F8 | 8 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 17 | 239,4 | 2.3 | 
| Standardowa_F8 | 8 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 25 | 251,2 | 7.0 | 
| Standardowa_F16 | 16 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 19 | 424,1 | 8.2 | 
| Standardowa_F16 | 16 | 1 | Procesor Intel (R) Xeon (R) E5-2673 v4 @ 2.30 GHz | 32 | 467,8 | 11,1 | 
| Standardowa_F16 | 16 | 2 | Procesor Intel (R) Xeon (R) E5-2673 v3 @ 2.40 GHz | 6 | 472,3 | 13.2 | 

## <a name="gs---storage-optimized"></a>GS — zoptymalizowane pod kątem magazynu

| Rozmiar | Procesory wirtualne | Węzły NUMA | Procesor CPU | Uruchamianie | Średnia stawka podstawowa | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standardowa_GS1 | 2 | 1 | Procesor Intel (R) Xeon (R) E5-2698B v3 @ 2,00 GHz | 29 | 63,6 | 4.7 | 
| Standardowa_GS2 | 4 | 1 | Procesor Intel (R) Xeon (R) E5-2698B v3 @ 2,00 GHz | 29 | 122,3 | 6,9 | 
| Standardowa_GS3 | 8 | 1 | Procesor Intel (R) Xeon (R) E5-2698B v3 @ 2,00 GHz | 31 | 222,4 | 8.1 | 
| Standardowa_GS4 | 16 | 1 | Procesor Intel (R) Xeon (R) E5-2698B v3 @ 2,00 GHz | 31 | 391,4 | 28,6 | 
| Standard_GS4-4 | 4 | 1 | Procesor Intel (R) Xeon (R) E5-2698B v3 @ 2,00 GHz | 28 | 127,5 | 5.3 | 
| Standard_GS4-8 | 8 | 1 | Procesor Intel (R) Xeon (R) E5-2698B v3 @ 2,00 GHz | 31 | 226,7 | 5.8 | 
| Standardowa_GS5 | 32 | 2 | Procesor Intel (R) Xeon (R) E5-2698B v3 @ 2,00 GHz | 31 | 760,9 | 6,2 | 
| Standardowa_GS5-8 | 8 | 2 | Procesor Intel (R) Xeon (R) E5-2698B v3 @ 2,00 GHz | 31 | 259,5 | 2.7 | 
| Standard_GS5-16 | 16 | 2 | Procesor Intel (R) Xeon (R) E5-2698B v3 @ 2,00 GHz | 31 | 447,9 | 4,0 | 

## <a name="g---compute-optimized"></a>G — Optymalizacja obliczeń
| Rozmiar | Procesory wirtualne | Węzły NUMA | Procesor CPU | Uruchamianie | Średnia stawka podstawowa | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standardowa_G1 | 2 | 1 | Procesor Intel (R) Xeon (R) E5-2698B v3 @ 2,00 GHz | 29 | 64,7 | 9,2 | 
| Standardowa_G2 | 4 | 1 | Procesor Intel (R) Xeon (R) E5-2698B v3 @ 2,00 GHz | 30 | 127,9 | 12,2 | 
| Standardowa_G3 | 8 | 1 | Procesor Intel (R) Xeon (R) E5-2698B v3 @ 2,00 GHz | 30 | 231,7 | 12,6 | 
| Standardowa_G4 | 16 | 1 | Procesor Intel (R) Xeon (R) E5-2698B v3 @ 2,00 GHz | 31 | 400,2 | 3.9 | 
| Standard_G5 | 32 | 2 | Procesor Intel (R) Xeon (R) E5-2698B v3 @ 2,00 GHz | 31 | 774,1 | 4.1 | 

## <a name="h---high-performance-compute-hpc"></a>Obliczenia o wysokiej wydajności (HPC)

| Rozmiar | Procesory wirtualne | Węzły NUMA | Procesor CPU | Uruchamianie | Średnia stawka podstawowa | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standardowa_H8 | 8 | 1 | Procesor Intel (R) Xeon (R) E5-2667 V3 @ 3.20 GHz | 31 | 296,1 | 1.4 | 
| Standardowa_H8m | 8 | 1 | Procesor Intel (R) Xeon (R) E5-2667 V3 @ 3.20 GHz | 34 | 295,1 | 1.5 | 
| Standardowa_H16 | 16 | 2 | Procesor Intel (R) Xeon (R) E5-2667 V3 @ 3.20 GHz | 19 | 563,5 | 4.3 | 
| Standardowa_H16m | 16 | 2 | Procesor Intel (R) Xeon (R) E5-2667 V3 @ 3.20 GHz | 19 | 562,9 | 3.3 | 
| Standard_H16mr | 16 | 2 | Procesor Intel (R) Xeon (R) E5-2667 V3 @ 3.20 GHz | 18 | 563,6 | 3.7 | 
| Standard_H16r | 16 | 2 | Procesor Intel (R) Xeon (R) E5-2667 V3 @ 3.20 GHz | 17 | 562,2 | 4.2 | 

## <a name="ls---storage-optimized"></a>LS — magazyn zoptymalizowany pod kątem

| Rozmiar | Procesory wirtualne | Węzły NUMA | Procesor CPU | Uruchamianie | Średnia stawka podstawowa | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standardowa_L4s | 4 | 1 | Procesor Intel (R) Xeon (R) E5-2698B v3 @ 2,00 GHz | 29 | 122,7 | 6.6 | 
| Standardowa_L8s | 8 | 1 | Procesor Intel (R) Xeon (R) E5-2698B v3 @ 2,00 GHz | 30 | 223,3 | 7,5 | 
| Standardowa_L16s | 16 | 1 | Procesor Intel (R) Xeon (R) E5-2698B v3 @ 2,00 GHz | 31 | 397,3 | 2.5 | 
| Standardowa_L32s | 32 | 2 | Procesor Intel (R) Xeon (R) E5-2698B v3 @ 2,00 GHz | 31 | 766,1 | 3,5 | 

## <a name="m---memory-optimized"></a>M — zoptymalizowane pod kątem pamięci

| Rozmiar | Procesory wirtualne | Węzły NUMA | Procesor CPU | Uruchamianie | Średnia stawka podstawowa | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standard_M8 — 2 MS | 2 | 1 | Procesor Intel (R) Xeon (R) E7-8890 v3 @ 2.50 GHz | 15 | 42,1 | 2.1 | 
| Standard_M8 — 4 MS | 4 | 1 | Procesor Intel (R) Xeon (R) E7-8890 v3 @ 2.50 GHz | 13 | 81,6 | 2.9 | 
| Standard_M16 — 4 MS | 4 | 1 | Procesor Intel (R) Xeon (R) E7-8890 v3 @ 2.50 GHz | 14 | 82,5 | 2.5 | 
| Standard_M16 — 8ms | 8 | 1 | Procesor Intel (R) Xeon (R) E7-8890 v3 @ 2.50 GHz | 20 | 157,2 | 6.0 | 
| Standard_M32 — 8ms | 8 | 1 | Procesor Intel (R) Xeon (R) E7-8890 v3 @ 2.50 GHz | 18 | 162,5 | 2.1 | 
| Standard_M32 — 16ms | 16 | 1 | Procesor Intel (R) Xeon (R) E7-8890 v3 @ 2.50 GHz | 12 | 306,5 | 0,5 | 
| Standard_M64 | 64 | 2 | Procesor Intel (R) Xeon (R) E7-8890 v3 @ 2.50 GHz | 11 | 1010,9 | 5.4 | 
| Standard_M64 — 16ms | 16 | 2 | Procesor Intel (R) Xeon (R) E7-8890 v3 @ 2.50 GHz | 13 | 316,0 | 2,4 | 
| Standard_M64 — 32ms | 32 | 2 | Procesor Intel (R) Xeon (R) E7-8890 v3 @ 2.50 GHz | 12 | 586,8 | 5.4 | 
| Standard_M64m | 64 | 2 | Procesor Intel (R) Xeon (R) E7-8890 v3 @ 2.50 GHz | 12 | 1005,5 | 12,3 | 
| Standardowa_M64ms | 64 | 2 | Procesor Intel (R) Xeon (R) E7-8890 v3 @ 2.50 GHz | 12 | 1012,9 | 12,5 | 
| Standard_M64s | 64 | 2 | Procesor Intel (R) Xeon (R) E7-8890 v3 @ 2.50 GHz | 12 | 1012,5 | 4.5 | 
| Standard_M128 | 128 | 4 | Procesor Intel (R) Xeon (R) E7-8890 v3 @ 2.50 GHz | 11 | 1777,3 | 15,6 | 
| Standard_M128 — 32ms | 32 | 4 | Procesor Intel (R) Xeon (R) E7-8890 v3 @ 2.50 GHz | 13 | 620,5 | 2.5 | 
| Standard_M128 — 64ms | 64 | 4 | Procesor Intel (R) Xeon (R) E7-8890 v3 @ 2.50 GHz | 12 | 1140,8 | 2.9 | 
| Standard_M128m | 128 | 4 | Procesor Intel (R) Xeon (R) E7-8890 v3 @ 2.50 GHz | 12 | 1778,3 | 10,3 | 
| Standard_M128ms | 128 | 4 | Procesor Intel (R) Xeon (R) E7-8890 v3 @ 2.50 GHz | 15 | 1780,7 | 18,3 | 
| Standard_M128s | 128 | 4 | Procesor Intel (R) Xeon (R) E7-8890 v3 @ 2.50 GHz | 12 | 1775,8 | 11,6 | 
| Standard_M16ms | 16 | 1 | Procesor Intel (R) Xeon (R) E7-8890 v3 @ 2.50 GHz | 20 | 293,1 | 11,8 | 
| Standard_M32ls | 32 | 1 | Procesor Intel (R) Xeon (R) E7-8890 v3 @ 2.50 GHz | 13 | 535,2 | 4.8 | 
| Standard_M32ms | 32 | 1 | Procesor Intel (R) Xeon (R) E7-8890 v3 @ 2.50 GHz | 11 | 534,1 | 4,6 | 
| Standard_M32ms | 32 | 2 | Procesor Intel (R) Xeon (R) E7-8890 v3 @ 2.50 GHz | 1 | 589,0 | 0,0 | 
| Standard_M32ts | 32 | 1 | Procesor Intel (R) Xeon (R) E7-8890 v3 @ 2.50 GHz | 12 | 538,6 | 3.2 | 
| Standard_M64ls | 64 | 2 | Procesor Intel (R) Xeon (R) E7-8890 v3 @ 2.50 GHz | 13 | 1015,2 | 10,0 | 
| Standard_M8ms | 8 | 1 | Procesor Intel (R) Xeon (R) E7-8890 v3 @ 2.50 GHz | 13 | 158,2 | 5,5 | 

## <a name="ncsv3---gpu-enabled"></a>NCSv3 — procesor GPU włączony

| Rozmiar | Procesory wirtualne | Węzły NUMA | Procesor CPU | Uruchamianie | Średnia stawka podstawowa | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standard_NC6s_v3 | 6 | 1 | Procesor Intel (R) Xeon (R) E5-2690 v4 @ 2.60 GHz | 6 | 230,2 | 1.6 | 
| Standard_NC12s_v3 | 12 | 1 | Procesor Intel (R) Xeon (R) E5-2690 v4 @ 2.60 GHz | 7 | 425,0 | 3,6 | 
| Standard_NC24rs_v3 | 24 | 2 | Procesor Intel (R) Xeon (R) E5-2690 v4 @ 2.60 GHz | 2 | 811,0 | 4.2 | 
| Standard_NC24s_v3 | 24 | 2 | Procesor Intel (R) Xeon (R) E5-2690 v4 @ 2.60 GHz | 3 | 809,3 | 2.3 | 

## <a name="ncsv2---gpu-enabled"></a>NCSv2 — procesor GPU włączony

| Rozmiar | Procesory wirtualne | Węzły NUMA | Procesor CPU | Uruchamianie | Średnia stawka podstawowa | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standard_NC6s_v2 | 6 | 1 | Procesor Intel (R) Xeon (R) E5-2690 v4 @ 2.60 GHz | 11 | 227,0 | 6,2 | 
| Standard_NC12s_v2 | 12 | 1 | Procesor Intel (R) Xeon (R) E5-2690 v4 @ 2.60 GHz | 9 | 427,3 | 1.3 | 
| Standard_NC24rs_v2 | 24 | 2 | Procesor Intel (R) Xeon (R) E5-2690 v4 @ 2.60 GHz | 12 | 811,0 | 5.4 | 
| Standard_NC24s_v2 | 24 | 2 | Procesor Intel (R) Xeon (R) E5-2690 v4 @ 2.60 GHz | 11 | 811,5 | 4.4 | 

## <a name="nc---gpu-enabled"></a>NC — procesor GPU włączony

| Rozmiar | Procesory wirtualne | Węzły NUMA | Procesor CPU | Uruchamianie | Średnia stawka podstawowa | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standardowa_NC6 | 6 | 1 | Procesor Intel (R) Xeon (R) E5-2690 v3 @ 2.60 GHz | 27 | 209,6 | 4.4 | 
| Standardowa_NC12 | 12 | 1 | Procesor Intel (R) Xeon (R) E5-2690 v3 @ 2.60 GHz | 28 | 394,4 | 3,8 | 
| Standardowa_NC24 | 24 | 2 | Procesor Intel (R) Xeon (R) E5-2690 v3 @ 2.60 GHz | 28 | 751,7 | 3,5 | 
| Standard_NC24r | 24 | 2 | Procesor Intel (R) Xeon (R) E5-2690 v3 @ 2.60 GHz | 27 | 752,9 | 3.4 | 

## <a name="nds--gpu-enabled"></a>NDs — włączono procesor GPU

| Rozmiar | Procesory wirtualne | Węzły NUMA | Procesor CPU | Uruchamianie | Średnia stawka podstawowa | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standard_ND6s | 6 | 1 | Procesor Intel (R) Xeon (R) E5-2690 v4 @ 2.60 GHz | 8 | 230,1 | 1.2 | 
| Standard_ND12s | 12 | 1 | Procesor Intel (R) Xeon (R) E5-2690 v4 @ 2.60 GHz | 11 | 426,5 | 1.4 | 
| Standard_ND24rs | 24 | 2 | Procesor Intel (R) Xeon (R) E5-2690 v4 @ 2.60 GHz | 10 | 811,4 | 3,5 | 
| Standard_ND24s | 24 | 2 | Procesor Intel (R) Xeon (R) E5-2690 v4 @ 2.60 GHz | 11 | 812,6 | 4.4 | 

## <a name="nv---gpu-enabled"></a>NV — procesor GPU włączony

| Rozmiar | Procesory wirtualne | Węzły NUMA | Procesor CPU | Uruchamianie | Średnia stawka podstawowa | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standardowa_NV6 | 6 | 1 | Procesor Intel (R) Xeon (R) E5-2690 v3 @ 2.60 GHz | 28 | 210,5 | 6.1 | 
| Standardowa_NV12 | 12 | 1 | Procesor Intel (R) Xeon (R) E5-2690 v3 @ 2.60 GHz | 28 | 394,5 | 2.3 | 
| Standardowa_NV24 | 24 | 2 | Procesor Intel (R) Xeon (R) E5-2690 v3 @ 2.60 GHz | 26 | 752,2 | 4.4 | 

## <a name="about-specint"></a>Informacje o SPECint
Numery systemu Windows zostały obliczone przez uruchomienie [SPECint 2006](https://www.spec.org/cpu2006/results/rint2006.html) w systemie Windows Server. SPECint został uruchomiony przy użyciu opcji stawki bazowej (SPECint_rate2006) z jedną kopią na vCPU. SPECint składa się z 12 oddzielnych testów, każdy przebiega trzy razy, pobierając wartość mediany z każdego testu i ważoną ich w celu utworzenia złożonego wyniku. Te testy zostały następnie uruchomione na wielu maszynach wirtualnych, aby zapewnić średnią liczbę wyświetlanych wyników.

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać informacje na temat pojemności magazynu, szczegółów dysku i dodatkowych zagadnień związanych z wybieraniem rozmiarów maszyn wirtualnych, zobacz [rozmiary maszyny wirtualnej](../sizes.md).