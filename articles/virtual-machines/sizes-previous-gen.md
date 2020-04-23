---
title: Rozmiary maszyn wirtualnych z systemem Azure z systemem Linux — poprzednie generacje | Dokumenty firmy Microsoft
description: Wyświetla listę poprzednich generacji rozmiarów dostępnych dla maszyn wirtualnych systemu Linux na platformie Azure. Wyświetla informacje o liczbie procesorów wirtualnych, dysków danych i kart sieciowych, a także przepustowości magazynu i przepustowości sieci dla rozmiarów w tej serii.
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/20/2020
ms.author: jonbeck
ms.openlocfilehash: ac49d2da9d05c9677dbb6f5328874ab3a45ff661
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82081544"
---
# <a name="previous-generations-of-virtual-machine-sizes"></a>Poprzednie generacje rozmiarów maszyn wirtualnych

Ta sekcja zawiera informacje na temat poprzednich generacji rozmiarów maszyn wirtualnych. Te rozmiary mogą być nadal używane, ale są dostępne nowsze generacje.

## <a name="f-series"></a>Seria F

Seria F jest oparta na procesorze Intel Xeon® E5-2673 v3 (Haswell) 2,4 GHz, który dzięki technologii Intel Turbo Boost 2.0 może osiągnąć prędkość zegara do 3,1 GHz. Wydajność procesora CPU jest taka sama jak w przypadku maszyn wirtualnych serii Dv2.  

Maszyny wirtualne z serii F są doskonałym wyborem dla obciążeń wymagających szybszych procesorów CPU, ale nie potrzebujących tak dużej ilości pamięci lub magazynu tymczasowego na każdy procesor wirtualny vCPU.  Z wartości serii F skorzystają obciążenia takie jak analizy, serwery gier, serwery sieci Web i przetwarzanie wsadowe.

ACU: 210–250

Pamięć masowa w wersji Premium: nie jest obsługiwana

Buforowanie magazynu w wersji Premium: nie jest obsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepustowość magazynu temp: IOPS / Odczyt MB/Zapis MB/s | Maksymalna ilość dysków/przepływności danych: Usługi We/Wy | Maksymalna przepustowość sieci ową/oczekiwana przepustowość sieci (Mb/s) |
|---|---|---|---|---|---|---|
| Standardowa_F1  | 1  | 2  | 16  | 3000/46/23    | 4/4x500   | 2/750   |
| Standardowa_F2  | 2  | 4  | 32  | 6000/93/46    | 8/8x500   | 2/1500  |
| Standardowa_F4  | 4  | 8  | 64  | 12000/187/93  | 16/16x500 | 4/3000  |
| Standardowa_F8  | 8  | 16 | 128 | 24000/375/187 | 32/32x500 | 8/6000  |
| Standardowa_F16 | 16 | 32 | 256 | 48000/750/375 | 64/64x500 | 8/12000 |

## <a name="fs-series-sup1sup"></a>Seria FS <sup>1</sup>

Seria Fs ma magazyn w warstwie Premium i wszystkie zalety serii F.

ACU: 210–250

Pamięć masowa w wersji premium: obsługiwane

Buforowanie magazynu w wersji Premium: obsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność pamięci podręcznej i magazynu tymczasowego: IOPS/MBPS (rozmiar pamięci podręcznej w GiB) | Maksymalna przepustowość dysku bez buforów: IOPS/MB/s | Maksymalna przepustowość sieci ową/oczekiwana przepustowość sieci (Mb/s) |
|---|---|---|---|---|---|---|---|
| Standardowa_F1s  | 1  | 2  | 4  | 4  | 4000/32 (12)    | 3200/48   | 2/750   |
| Standardowa_F2s  | 2  | 4  | 8  | 8  | 8000/64 (24)    | 6400/96   | 2/1500  |
| Standardowa_F4s  | 4  | 8  | 16 | 16 | 16000/128 (48)  | 12800/192 | 4/3000  |
| Standardowa_F8s  | 8  | 16 | 32 | 32 | 32000/256 (96)  | 25600/384 | 8/6000  |
| Standardowa_F16s | 16 | 32 | 64 | 64 | 64000/512 (192) | 51200/768 | 8/12000 |

MB/s = 10^6 bajtów na sekundę, GiB = 1024^3 bajtów.

<sup>1</sup> Maksymalna przepustowość dysku (IOPS lub MBPS) możliwa w maszynach wirtualnych serii FS może być ograniczona przez liczbę, rozmiar i rozkład dysku podłączonego(-ych).  Aby uzyskać szczegółowe informacje, zobacz projektowanie wysokiej wydajności dla [systemu Windows](windows/premium-storage-performance.md) lub [Linux](linux/premium-storage-performance.md).  


## <a name="nvv2-series"></a>Seria NVv2

**Nowsze zalecenie rozmiaru:** [Seria NVv3](nvv3-series.md)

Maszyny wirtualne serii NVv2 są zasilane przez procesory graficzne [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) i technologię NVIDIA GRID z procesorami Intel Broadwell. Te maszyny wirtualne są przeznaczone dla aplikacji graficznych akcelerowanych gpu i pulpitów wirtualnych, gdzie klienci chcą wizualizować swoje dane, symulować wyniki, aby wyświetlić, pracować nad CAD lub renderować i przesyłać strumieniowo zawartość. Ponadto te maszyny wirtualne mogą uruchamiać pojedyncze obciążenia precyzyjne, takie jak kodowanie i renderowanie. Maszyny wirtualne NVv2 obsługują pamięć masową premium i są wyposażone w dwukrotnie większą pamięć systemową (RAM) w porównaniu z poprzednią serią NV.  

Każdy procesor GRAFICZNY w wystąpieniach NVv2 jest wyposażony w licencję GRID. Ta licencja zapewnia elastyczność używania wystąpienia NV jako wirtualnej stacji roboczej dla pojedynczego użytkownika lub 25 równoczesnych użytkowników może połączyć się z maszyną wirtualną w przypadku scenariusza aplikacji wirtualnej.

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Pamięć GPU: GiB | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych | Wirtualne stacje robocze | Aplikacje wirtualne |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6s_v2  | 6  | 112 | 320  | 1 | 8  | 12 | 4 | 1 | 25  |
| Standard_NV12s_v2 | 12 | 224 | 640  | 2 | 16 | 24 | 8 | 2 | 50  |
| Standard_NV24s_v2 | 24 | 448 | 1280 | 4 | 32 | 32 | 8 | 4 | 100 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="older-generations-of-virtual-machine-sizes"></a>Starsze generacje rozmiarów maszyn wirtualnych

Ta sekcja zawiera informacje na temat starszych generacji rozmiarów maszyn wirtualnych. Te rozmiary są nadal obsługiwane, ale nie otrzymają dodatkowej pojemności. Istnieją nowsze lub alternatywne rozmiary, które są ogólnie dostępne. Zapoznaj się [z rozmiary maszyn wirtualnych systemu Linux na platformie Azure,](linux/sizes.md) aby wybrać rozmiary maszyn wirtualnych, które najlepiej odpowiadają Twoim potrzebom.  

Aby uzyskać więcej informacji na temat zmiany rozmiaru maszyny Wirtualnej z systemem Linux, zobacz [Zmiana rozmiaru maszyny Wirtualnej z systemem Linux](linux/change-vm-size.md).  

<br>

### <a name="basic-a"></a>Podstawowa A  

**Nowsze zalecenie rozmiaru:** [Seria Av2](av2-series.md)

Pamięć masowa w wersji Premium: nie jest obsługiwana

Buforowanie magazynu w wersji Premium: nie jest obsługiwane

Rozmiary warstwy Podstawowa są przeznaczone głównie dla obciążeń związanych z tworzeniem aplikacji i innych aplikacji, które nie wymagają równoważenia obciążenia, automatycznego skalowania ani maszyn wirtualnych korzystających z dużej ilości pamięci.

| Rozmiar — rozmiar\nazwa | Procesor wirtualny | Memory (Pamięć)|Karty sieciowe (maks.)| Maksymalny rozmiar dysku tymczasowego | Maksymalnie z dyski z danymi (po 1023 GB)| Maksymalnie z liczba operacji we/wy na sekundę (300 na dysk) |
|---|---|---|---|---|---|---|
| A0\Podstawowa_A0 | 1 | 768 MB  | 2 | 20 GB  | 1  | 1x300  |
| A1\Podstawowa_A1 | 1 | 1,75 GB | 2 | 40 GB  | 2  | 2x300  |
| A2\Podstawowa_A2 | 2 | 3,5 GB  | 2 | 60 GB  | 4  | 4x300  |
| A3\Podstawowa_A3 | 4 | 7 GB    | 2 | 120 GB | 8  | 8x300  |
| A4\Podstawowa_A4 | 8 | 14 GB   | 2 | 240 GB | 16 | 16x300 |

<br>

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standardowa_A0–A4 w przypadku używania interfejsu wiersza polecenia i programu PowerShell

W klasycznym modelu wdrażania niektóre nazwy rozmiarów maszyny wirtualnej są nieco inne w interfejsie wiersza polecenia i programie PowerShell:

* Standardowa_A0 = Bardzo mała
* Standardowa_A1 = Mała
* Standardowa_A2 = Średnia
* Standardowa_A3 = Duża
* Standardowa_A4 = Bardzo duża

### <a name="a-series"></a>Seria A  

**Nowsze zalecenie rozmiaru:** [Seria Av2](av2-series.md)

ACU: 50–100

Pamięć masowa w wersji Premium: nie jest obsługiwana

Buforowanie magazynu w wersji Premium: nie jest obsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (HDD): GiB | Maks. liczba dysków danych | Maksymalna przepływność dysków danych: liczba operacji we/wy na sekundę | Maksymalna przepustowość sieci ową/oczekiwana przepustowość sieci (Mb/s) |
| --- | --- | --- | --- | --- | --- | --- |
| &nbsp;Standard_A0<sup>1</sup> | 1 | 0,768 | 20 | 1 | 1 x 500 | 2/100 |
| Standardowa_A1 | 1 | 1,75 | 70  | 2  | 2 x 500  | 2/500  |
| Standardowa_A2 | 2 | 3,5  | 135 | 4  | 4 x 500  | 2/500  |
| Standardowa_A3 | 4 | 7    | 285 | 8  | 8 x 500  | 2/1000 |
| Standardowa_A4 | 8 | 14   | 605 | 16 | 16 x 500 | 4/2000 |
| Standardowa_A5 | 2 | 14   | 135 | 4  | 4 x 500  | 2/500  |
| Standardowa_A6 | 4 | 28   | 285 | 8  | 8 x 500  | 2/1000 |
| Standardowa_A7 | 8 | 56   | 605 | 16 | 16 x 500 | 4/2000 |

<sup>1</sup> Rozmiar A0 jest nadmiernie subskrybowany na sprzęcie fizycznym. Tylko w przypadku tego konkretnego rozmiaru inne wdrożenia klienta mogą mieć wpływ na wydajność uruchomionego obciążenia. Wydajność względna jest przedstawiona poniżej jako oczekiwana linia bazowa, podlegająca przybliżonej zmienności w granicach 15 procent.

<br>

### <a name="a-series---compute-intensive-instances"></a>Seria A — wystąpienia intensywnie korzystające z mocy obliczeniowej  

**Nowsze zalecenie rozmiaru:** [Seria Av2](av2-series.md)

ACU: 225

Pamięć masowa w wersji Premium: nie jest obsługiwana

Buforowanie magazynu w wersji Premium: nie jest obsługiwane

Rozmiary A8–A11 i serii H są również nazywane *wystąpieniami intensywnie korzystającymi z mocy obliczeniowej*. Sprzęt, na którym działają te rozmiary maszyn wirtualnych, został zaprojektowany i zoptymalizowany pod kątem aplikacji intensywnie korzystających z mocy obliczeniowej i sieci, w tym aplikacji klastrów obliczeń o wysokiej wydajności, modelowania i symulacji. Maszyny wirtualne serii A8–A11 korzystają z procesorów Intel Xeon E5-2670 o częstotliwości 2,6 GHz, a seria H korzysta z procesorów Intel Xeon E5-2667 v3 o częstotliwości 3,2 GHz.  

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (HDD): GiB | Maks. liczba dysków danych | Maksymalna przepływność dysków danych: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych|
|---|---|---|---|---|---|---|
| &nbsp;Standard_A8<sup>1</sup> | 8  | 56  | 382 | 32 | 32 x 500 | 2 |
| &nbsp;Standard_A9<sup>1</sup> | 16 | 112 | 382 | 64 | 64x500 | 4 |
| Standardowa_A10 | 8  | 56  | 382 | 32 | 32 x 500 | 2 |
| Standardowa_A11 | 16 | 112 | 382 | 64 | 64x500 | 4 |

<sup>1.</sup> W przypadku aplikacji MPI dedykowana sieć zaplecza RDMA jest włączona przez sieć FDR InfiniBand, która zapewnia bardzo małe opóźnienia i wysoką przepustowość.  

> [!NOTE]
> Maszyny wirtualne A8 – A11 są planowane na emeryturę w dniu 3/2021. Aby uzyskać więcej informacji, zobacz [Przewodnik po migracji HPC](https://azure.microsoft.com/resources/hpc-migration-guide/).

<br>

### <a name="d-series"></a>Seria D  

**Nowsze zalecenie rozmiaru:** [Seria Dv3](dv3-dsv3-series.md)

ACU: 160-250 <sup>1</sup>

Pamięć masowa w wersji Premium: nie jest obsługiwana

Buforowanie magazynu w wersji Premium: nie jest obsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepustowość magazynu temp: IOPS / Odczyt MB/Zapis MB/s | Maksymalna ilość dysków/przepływności danych: Usługi We/Wy | Maksymalna przepustowość sieci ową/oczekiwana przepustowość sieci (Mb/s) |
|---|---|---|---|---|---|---|
| Standardowa_D1  | 1 | 3,5 | 50  | 3000/46/23    | 4/4x500   | 2/500  |
| Standardowa_D2  | 2 | 7   | 100 | 6000/93/46    | 8/8x500   | 2/1000 |
| Standardowa_D3  | 4 | 14  | 200 | 12000/187/93  | 16/16x500 | 4/2000 |
| Standardowa_D4  | 8 | 28  | 400 | 24000/375/187 | 32/32x500 | 8/4000 |

<sup>Rodzina 1</sup> maszyn wirtualnych może działać na jednym z następujących procesorów: 2,2 GHz Intel Xeon® E5-2660 v2, 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) lub 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="d-series---memory-optimized"></a>Seria D - zoptymalizowana pod kątem pamięci  

**Nowsze zalecenie rozmiaru:** [Seria Dv3](dv3-dsv3-series.md)

ACU: 160-250 <sup>1</sup>

Pamięć masowa w wersji Premium: nie jest obsługiwana

Buforowanie magazynu w wersji Premium: nie jest obsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepustowość magazynu temp: IOPS / Odczyt MB/Zapis MB/s | Maksymalna ilość dysków/przepływności danych: Usługi We/Wy | Maksymalna przepustowość sieci ową/oczekiwana przepustowość sieci (Mb/s) |
|---|---|---|---|---|---|---|
| Standardowa_D11 | 2  | 14  | 100 | 6000/93/46    | 8/8x500   | 2/1000 |
| Standardowa_D12 | 4  | 28  | 200 | 12000/187/93  | 16/16x500 | 4/2000 |
| Standardowa_D13 | 8  | 56  | 400 | 24000/375/187 | 32/32x500 | 8/4000 |
| Standardowa_D14 | 16 | 112 | 800 | 48000/750/375 | 64/64x500 | 8/8000 |

<sup>Rodzina 1</sup> maszyn wirtualnych może działać na jednym z następujących procesorów: 2,2 GHz Intel Xeon® E5-2660 v2, 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) lub 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

## <a name="preview-dc-series"></a>Podgląd: seria DC

**Nowsze zalecenie rozmiaru:** [Seria DCsv2](dcv2-series.md)

Pamięć masowa w wersji premium: obsługiwane

Buforowanie magazynu w wersji Premium: obsługiwane

Seria DC wykorzystuje najnowszą generację procesora Intel XEON E-2176G 3,7 GHz z technologią SGX, a technologia Intel Turbo Boost może wzrosnąć do 4,7 GHz. 

| Rozmiar          | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: liczba operacji we/wy na sekundę / MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: liczba operacji we/wy na sekundę / MB/s | Maksymalna karta sieciowa / oczekiwana przepustowość sieci (Mb/s) |
|---------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC2s | 2    | 8           | 100                    | 2              | 4000 / 32 (43)                                                          | 3200 /48                                  | 2 / 1500                                     |
| Standard_DC4s | 4    | 16          | 200                    | 4              | 8000 / 64 (86)                                                          | 6400 /96                                  | 2 / 3000                                     |

> [!IMPORTANT]
>
> Maszyny wirtualne z serii DC są [generacjami 2 maszyn wirtualnych](./linux/generation-2.md#creating-a-generation-2-vm) i obsługują `Gen2` tylko obrazy.


### <a name="ds-series"></a>Seria DS  

**Nowsze zalecenie rozmiaru**: [Dsv3-series](dv3-dsv3-series.md)

ACU: 160-250 <sup>1</sup>

Pamięć masowa w wersji premium: obsługiwane

Buforowanie magazynu w wersji Premium: obsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność pamięci podręcznej i magazynu tymczasowego: IOPS/MBPS (rozmiar pamięci podręcznej w GiB) | Maksymalna przepustowość dysku bez buforów: IOPS/MB/s | Maksymalna przepustowość sieci ową/oczekiwana przepustowość sieci (Mb/s) |
|---|---|---|---|---|---|---|---|
| Standardowa_DS1 | 1 | 3,5 | 7  | 4  | 4000/32 (43)    | 3200/32   | 2/500  |
| Standardowa_DS2 | 2 | 7   | 14 | 8  | 8000/64 (86)    | 6400/64   | 2/1000 |
| Standardowa_DS3 | 4 | 14  | 28 | 16 | 16000/128 (172) | 12800/128 | 4/2000 |
| Standardowa_DS4 | 8 | 28  | 56 | 32 | 32000/256 (344) | 25600/256 | 8/4000 |

<sup>Rodzina 1</sup> maszyn wirtualnych może działać na jednym z następujących procesorów: 2,2 GHz Intel Xeon® E5-2660 v2, 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) lub 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="ds-series---memory-optimized"></a>Seria DS - zoptymalizowana pod kątem pamięci  

**Nowsze zalecenie rozmiaru**: [Dsv3-series](dv3-dsv3-series.md)

ACU: 160-250 <sup>1,2</sup>

Pamięć masowa w wersji premium: obsługiwane

Buforowanie magazynu w wersji Premium: obsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność pamięci podręcznej i magazynu tymczasowego: IOPS/MBPS (rozmiar pamięci podręcznej w GiB) | Maksymalna przepustowość dysku bez buforów: IOPS/MB/s | Maksymalna przepustowość sieci ową/oczekiwana przepustowość sieci (Mb/s) |
|---|---|---|---|---|---|---|---|
| Standardowa_DS11 | 2  | 14  | 28  | 8  | 8000/64 (72)    | 6400/64   | 2/1000 |
| Standardowa_DS12 | 4  | 28  | 56  | 16 | 16000/128 (144) | 12800/128 | 4/2000 |
| Standardowa_DS13 | 8  | 56  | 112 | 32 | 32000/256 (288) | 25600/256 | 8/4000 |
| Standardowa_DS14 | 16 | 112 | 224 | 64 | 64000/512 (576) | 51200/512 | 8/8000 |

<sup>1</sup> Maksymalna przepustowość dysku (IOPS lub MB/s) możliwa w maszynach wirtualnych serii DS może być ograniczona liczbą, rozmiarem i rozkładem podłączonego dysku(-ów).  Aby uzyskać szczegółowe informacje, zobacz projektowanie wysokiej wydajności dla [systemu Windows](windows/premium-storage-performance.md) lub [Linux](linux/premium-storage-performance.md).
<sup>Rodzina 2</sup> maszyn wirtualnych może działać na jednym z następujących procesorów: 2,2 GHz Intel Xeon® E5-2660 v2, 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) lub 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="ls-series"></a>Seria Ls

Maszyny wirtualne serii Ls oferują do 32 procesorów wirtualnych vCPU — procesor [Intel® Xeon® z rodziny E5 v3](https://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). Wydajność procesora CPU dla serii Ls jest taka sama jak w przypadku maszyn wirtualnych serii G/GS. Na procesor wirtualny vCPU przypada pamięć o pojemności 8 GiB.

Seria Ls nie obsługuje tworzenia lokalnej pamięci podręcznej w celu zwiększenia liczby współużytkowanych we/wy osiągalnej przez dyski z danymi trwałe. Wysoka przepustowość i we/wy na dysku lokalnym sprawia, że maszyny wirtualne z serii Ls są idealne dla magazynów NoSQL, takich jak Apache Cassandra i MongoDB, które replikują dane między wieloma maszynami wirtualnymi, aby osiągnąć trwałość w przypadku awarii pojedynczej maszyny wirtualnej.

ACU: 180–240

Pamięć masowa w wersji premium: obsługiwane

Buforowanie magazynu w wersji Premium: nie jest obsługiwane

| Rozmiar | Procesor wirtualny | Pamięć (GiB) | Pamięć masowa (GiB) | Maks. liczba dysków danych | Maksymalna przepustowość magazynu tymczasowego (IOPS/MB/s) | Maksymalna przepływność dysku nieskłoniętym (IOPS/MBps) | Maksymalna przepustowość sieci ową/oczekiwana przepustowość sieci (Mb/s) |
|---|---|---|---|---|---|---|---|
| Standardowa_L4s   | 4  | 32  | 678  | 16 | 20000/200 | 5000/125  | 2/4000  |
| Standardowa_L8s   | 8  | 64  | 1388 | 32 | 40000/400 | 10000/250 | 4/8000  |
| Standardowa_L16s  | 16 | 128 | 2807 | 64 | 80000/800 | 20000/500 | 8/16000 |
| &nbsp;Standard_L32s<sup>1</sup> | 32 | 256 | 5630 | 64 | 160000/1600 | 40000/1000 | 8/20000 |

Maksymalna możliwa przepustowość dysku za pomocą maszyn wirtualnych z serii Ls może być ograniczona przez liczbę, rozmiar i rozkładanie wszystkich dołączonych dysków. Aby uzyskać szczegółowe informacje, zobacz projektowanie wysokiej wydajności dla [systemu Windows](windows/premium-storage-performance.md) lub [Linux](linux/premium-storage-performance.md).

<sup>1</sup> Wystąpienie jest izolowane do sprzętu dedykowanego jednemu klientowi.

### <a name="gs-series"></a>Seria GS

ACU: 180 - 240 <sup>1</sup>

Pamięć masowa w wersji premium: obsługiwane

Buforowanie magazynu w wersji Premium: obsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: liczba operacji we/wy na sekundę / MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepustowość dysku bez buforów: IOPS/MB/s | Maksymalna przepustowość sieci ową/oczekiwana przepustowość sieci (Mb/s) |
|---|---|---|---|---|---|---|---|
| Standardowa_GS1 | 2 | 28  | 56  | 8  | 10000/100 (264)  | 5000/ 125  | 2/2000 |
| Standardowa_GS2 | 4 | 56  | 112 | 16 | 20000/200 (528)  | 10000/ 250 | 2/4000 |
| Standardowa_GS3 | 8 | 112 | 224 | 32 | 40000/400 (1056) | 20000/ 500 | 4/8000 |
| &nbsp;Standard_GS4<sup>3</sup> | 16 | 224 | 448 | 64 | 80000/800 (2112) | 40000/1000 | 8/16000 |
| &nbsp;Standard_GS5<sup>&nbsp;2, 3</sup> | 32 | 448 |896 | 64 |160000/1600 (4224) | 80000/2000 | 8/20000 |

<sup>1</sup> Maksymalna przepustowość dysku (IOPS lub MB/s) możliwa w maszynach wirtualnych serii GS może być ograniczona przez liczbę, rozmiar i rozkład podłączony dysk(-y). Aby uzyskać szczegółowe informacje, zobacz projektowanie wysokiej wydajności dla [systemu Windows](windows/premium-storage-performance.md) lub [Linux](linux/premium-storage-performance.md).

<sup>2</sup> Wystąpienie jest izolowane do sprzętu dedykowanego jednemu klientowi.

<sup>3</sup> Dostępne ograniczone rozmiary rdzeni.

<br>

### <a name="g-series"></a>Seria G

ACU: 180–240

Pamięć masowa w wersji Premium: nie jest obsługiwana

Buforowanie magazynu w wersji Premium: nie jest obsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepustowość magazynu temp: IOPS / Odczyt MB/Zapis MB/s | Maksymalna ilość dysków/przepływności danych: Usługi We/Wy | Maksymalna przepustowość sieci ową/oczekiwana przepustowość sieci (Mb/s) |
|---|---|---|---|---|---|---|
| Standardowa_G1  | 2  | 28  | 384  | 6000/93/46    | 8/8x500   | 2/2000  |
| Standardowa_G2  | 4  | 56  | 768  | 12000/187/93  | 16/16x500 | 2/4000  |
| Standardowa_G3  | 8  | 112 | 1536 | 24000/375/187 | 32/32x500 | 4/8000  |
| Standardowa_G4  | 16 | 224 | 3072 | 48000/750/375 | 64/64x500 | 8/16000 |
| &nbsp;Standard_G5<sup>1</sup> | 32 | 448 | 6144 | 96000/1500/750| 64/64x500 | 8/20000 |

<sup>1</sup> Wystąpienie jest izolowane do sprzętu dedykowanego jednemu klientowi.
<br>

## <a name="nv-series"></a>Seria NV
**Nowsze polecanie rozmiaru:** [seria NVv3](nvv3-series.md) i [seria NVv4](nvv4-series.md)

Maszyny wirtualne z serii NV są zasilane przez procesory graficzne [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) i technologię NVIDIA GRID dla aplikacji akcelerowanych na komputerach stacjonarnych i pulpitów wirtualnych, w których klienci są w stanie wizualizować swoje dane lub symulacje. Użytkownicy są w stanie wizualizować swoje grafiki intensywne przepływy pracy w wystąpieniach NV, aby uzyskać doskonałe możliwości grafiki i dodatkowo uruchomić pojedyncze obciążenia precyzji, takie jak kodowanie i renderowanie. Maszyny wirtualne z serii NV są również obsługiwane przez procesory Intel Xeon E5-2690 v3 (Haswell).

Każdy procesor graficzny w wystąpieniach NV jest wyposażony w licencję GRID. Ta licencja zapewnia elastyczność używania wystąpienia NV jako wirtualnej stacji roboczej dla pojedynczego użytkownika lub 25 równoczesnych użytkowników może połączyć się z maszyną wirtualną w przypadku scenariusza aplikacji wirtualnej.

Pamięć masowa w wersji Premium: nie jest obsługiwana

Buforowanie magazynu w wersji Premium: nie jest obsługiwane

Migracja na żywo: nie jest obsługiwana

Aktualizacje zachowywania pamięci: nie są obsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Pamięć GPU: GiB | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych | Wirtualne stacje robocze | Aplikacje wirtualne |
|---|---|---|---|---|---|---|---|---|---|
| Standardowa_NV6  | 6  | 56  | 340  | 1 | 8  | 24 | 1 | 1 | 25  |
| Standardowa_NV12 | 12 | 112 | 680  | 2 | 16 | 48 | 2 | 2 | 50  |
| Standardowa_NV24 | 24 | 224 | 1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 procesor GPU = połowa karty M60.
<br>

## <a name="other-sizes"></a>Inne rozmiary

* [Zastosowania ogólne](sizes-general.md)
* [Optymalizacja pod kątem obliczeń](sizes-compute.md)
* [Optymalizacja pod kątem pamięci](sizes-memory.md)
* [Optymalizacja pod kątem magazynu](sizes-storage.md)
* [Procesor GPU](sizes-gpu.md)
* [Obliczenia o wysokiej wydajności](sizes-hpc.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [jednostki obliczeniowe platformy Azure (ACU)](acu.md) mogą pomóc w porównywaniu wydajności obliczeniowej w jednostkach SKU platformy Azure.
