---
title: Rozmiary maszyn wirtualnych platformy Azure — poprzednie generacje | Microsoft Docs
description: Zawiera listę poprzednich generacji rozmiarów dostępnych dla maszyn wirtualnych na platformie Azure. Wyświetla informacje o liczbie procesorów wirtualnych vCPU, dyskach danych i kartach sieciowych oraz o przepływności magazynu i przepustowości sieci dla rozmiarów w tej serii.
services: virtual-machines
ms.subservice: sizes
author: mimckitt
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 11/01/2020
ms.author: mimckitt
ms.openlocfilehash: 90bc98d63b45e43c9325eed4fe019b18f52d0de8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96500295"
---
# <a name="previous-generations-of-virtual-machine-sizes"></a>Poprzednie generacji rozmiarów maszyn wirtualnych

Ta sekcja zawiera informacje dotyczące wcześniejszych generacji rozmiarów maszyn wirtualnych. Nadal mogą być używane te rozmiary, ale dostępne są nowsze generacji.

## <a name="f-series"></a>Seria F

Seria F jest oparta na procesorze Intel Xeon® E5-2673 v3 (Haswell) 2,4 GHz, który dzięki technologii Intel Turbo Boost 2.0 może osiągnąć prędkość zegara do 3,1 GHz. Wydajność procesora CPU jest taka sama jak w przypadku maszyn wirtualnych serii Dv2.  

Maszyny wirtualne z serii F są doskonałym wyborem dla obciążeń wymagających szybszych procesorów CPU, ale nie potrzebujących tak dużej ilości pamięci lub magazynu tymczasowego na każdy procesor wirtualny vCPU.  Z wartości serii F skorzystają obciążenia takie jak analizy, serwery gier, serwery sieci Web i przetwarzanie wsadowe.

ACU: 210–250

Premium Storage: nieobsługiwane

Buforowanie Premium Storage: nieobsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepływność magazynu: IOPS/Odczyt MB/s/zapis MB/s | Maksymalna liczba dysków danych/przepływność: IOPS | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|---|---|---|---|---|---|---|
| Standardowa_F1  | 1  | 2  | 16  | 3000/46/23    | 4/4x500   | 2/750   |
| Standardowa_F2  | 2  | 4  | 32  | 6000/93/46    | 8/8x500   | 2/1500  |
| Standardowa_F4  | 4  | 8  | 64  | 12000/187/93  | 16/16x500 | 4/3000  |
| Standardowa_F8  | 8  | 16 | 128 | 24000/375/187 | 32/32x500 | 8/6000  |
| Standardowa_F16 | 16 | 32 | 256 | 48000/750/375 | 64/64x500 | 8/12000 |

## <a name="fs-series-sup1sup"></a>FS — seria <sup>1</sup>

Seria Fs ma magazyn w warstwie Premium i wszystkie zalety serii F.

ACU: 210–250

Premium Storage: obsługiwane

Buforowanie Premium Storage: obsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu w pamięci podręcznej i temp: IOPS/MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność dysku w pamięci podręcznej: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|---|---|---|---|---|---|---|---|
| Standardowa_F1s  | 1  | 2  | 4  | 4  | 4000/32 (12)    | 3200/48   | 2/750   |
| Standardowa_F2s  | 2  | 4  | 8  | 8  | 8000/64 (24)    | 6400/96   | 2/1500  |
| Standardowa_F4s  | 4  | 8  | 16 | 16 | 16000/128 (48)  | 12800/192 | 4/3000  |
| Standardowa_F8s  | 8  | 16 | 32 | 32 | 32000/256 (96)  | 25600/384 | 8/6000  |
| Standardowa_F16s | 16 | 32 | 64 | 64 | 64000/512 (192) | 51200/768 | 8/12000 |

MB/s = 10^6 bajtów na sekundę, GiB = 1024^3 bajtów.

<sup>1</sup> maksymalna przepływność dysku (IOPS lub MB/s) możliwa dla maszyny wirtualnej z serii FS może być ograniczona przez liczbę, rozmiar i rozkładanie dołączonych dysków.  Aby uzyskać szczegółowe informacje, zobacz [projektowanie pod kątem wysokiej wydajności](premium-storage-performance.md).


## <a name="nvv2-series"></a>Seria NVv2

**Nowsze zalecenie dotyczące rozmiaru**: [Seria NVv3](nvv3-series.md)

Maszyny wirtualne z serii NVv2 są obsługiwane przez procesory [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU i technologię NVIDIA GRID z procesorami Intel Broadwell. Te maszyny wirtualne są przeznaczone dla procesorów GPU i pulpitów wirtualnych, na których klienci chcą wizualizować swoje dane, symulują wyniki do wyświetlania, pracy w programie CAD lub renderowania i przesyłania strumieniowego zawartości. Ponadto te maszyny wirtualne mogą uruchamiać obciążenia o pojedynczej precyzji, takie jak kodowanie i renderowanie. NVv2 maszyny wirtualne obsługują Premium Storage i mają dwa razy większą ilość pamięci systemowej (RAM) w porównaniu z poprzednią serią NV.  

Każdy procesor GPU w wystąpieniach NVv2 zawiera licencję siatki. Ta licencja zapewnia elastyczność używania wystąpienia TRWAŁEgo jako wirtualnej stacji roboczej dla jednego użytkownika lub 25 jednoczesnych użytkowników może połączyć się z MASZYNą wirtualną w scenariuszu aplikacji wirtualnej.

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Pamięć procesora GPU: GiB | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych | Wirtualne stacje robocze | Aplikacje wirtualne |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6s_v2  | 6  | 112 | 320  | 1 | 8  | 12 | 4 | 1 | 25  |
| Standard_NV12s_v2 | 12 | 224 | 640  | 2 | 16 | 24 | 8 | 2 | 50  |
| Standard_NV24s_v2 | 24 | 448 | 1280 | 4 | 32 | 32 | 8 | 4 | 100 |

## <a name="older-generations-of-virtual-machine-sizes"></a>Starsze generacji rozmiarów maszyn wirtualnych

Ta sekcja zawiera informacje dotyczące starszych generacji rozmiarów maszyn wirtualnych. Te rozmiary są nadal obsługiwane, ale nie będą otrzymywać dodatkowej pojemności. Istnieją również nowsze lub alternatywne rozmiary, które są ogólnie dostępne. Zapoznaj się z [rozmiarem maszyn wirtualnych na platformie Azure](./sizes.md) , aby wybrać rozmiary maszyny wirtualnej, które najlepiej pasują do Twoich potrzeb.  

Aby uzyskać więcej informacji na temat zmiany rozmiaru maszyny wirtualnej z systemem Linux, zobacz [zmiana rozmiaru maszyny wirtualnej z systemem Linux](linux/change-vm-size.md).  

<br>

### <a name="basic-a"></a>Podstawowa A  

**Nowsze zalecenie dotyczące rozmiaru**: [Seria Av2](av2-series.md)

Premium Storage: nieobsługiwane

Buforowanie Premium Storage: nieobsługiwane

Rozmiary warstwy Podstawowa są przeznaczone głównie dla obciążeń związanych z tworzeniem aplikacji i innych aplikacji, które nie wymagają równoważenia obciążenia, automatycznego skalowania ani maszyn wirtualnych korzystających z dużej ilości pamięci.

| Rozmiar — rozmiar\nazwa | Procesor wirtualny | Pamięć|Karty sieciowe (maks.)| Maksymalny rozmiar dysku tymczasowego | Maksymalnie z dyski z danymi (1023 GB każdego)| Maksymalnie z liczba operacji we/wy na sekundę (300 na dysk) |
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

**Nowsze zalecenie dotyczące rozmiaru**: [Seria Av2](av2-series.md)

ACU: 50–100

Premium Storage: nieobsługiwane

Buforowanie Premium Storage: nieobsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (HDD): GiB | Maks. liczba dysków danych | Maksymalna przepływność dysków danych: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0 &nbsp; <sup>1</sup> | 1 | 0,768 | 20 | 1 | 1 x 500 | 2/100 |
| Standardowa_A1 | 1 | 1,75 | 70  | 2  | 2 x 500  | 2/500  |
| Standardowa_A2 | 2 | 3,5  | 135 | 4  | 4 x 500  | 2/500  |
| Standardowa_A3 | 4 | 7    | 285 | 8  | 8 x 500  | 2/1000 |
| Standardowa_A4 | 8 | 14   | 605 | 16 | 16 x 500 | 4/2000 |
| Standardowa_A5 | 2 | 14   | 135 | 4  | 4 x 500  | 2/500  |
| Standardowa_A6 | 4 | 28   | 285 | 8  | 8 x 500  | 2/1000 |
| Standardowa_A7 | 8 | 56   | 605 | 16 | 16 x 500 | 4/2000 |

<sup>1</sup> rozmiar a0 jest nadmiernie subskrybowany na sprzęcie fizycznym. Tylko w przypadku tego konkretnego rozmiaru inne wdrożenia klienta mogą mieć wpływ na wydajność uruchomionego obciążenia. Wydajność względna jest przedstawiona poniżej jako oczekiwana linia bazowa, podlegająca przybliżonej zmienności w granicach 15 procent.

<br>

### <a name="a-series---compute-intensive-instances"></a>Seria A — wystąpienia intensywnie korzystające z mocy obliczeniowej  

**Nowsze zalecenie dotyczące rozmiaru**: [Seria Av2](av2-series.md)

ACU: 225

Premium Storage: nieobsługiwane

Buforowanie Premium Storage: nieobsługiwane

Rozmiary A8–A11 i serii H są również nazywane *wystąpieniami intensywnie korzystającymi z mocy obliczeniowej*. Sprzęt, na którym działają te rozmiary maszyn wirtualnych, został zaprojektowany i zoptymalizowany pod kątem aplikacji intensywnie korzystających z mocy obliczeniowej i sieci, w tym aplikacji klastrów obliczeń o wysokiej wydajności, modelowania i symulacji. Maszyny wirtualne serii A8–A11 korzystają z procesorów Intel Xeon E5-2670 o częstotliwości 2,6 GHz, a seria H korzysta z procesorów Intel Xeon E5-2667 v3 o częstotliwości 3,2 GHz.  

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (HDD): GiB | Maks. liczba dysków danych | Maksymalna przepływność dysków danych: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych|
|---|---|---|---|---|---|---|
| Standard_A8 &nbsp; <sup>1</sup> | 8  | 56  | 382 | 32 | 32 x 500 | 2 |
| Standard_A9 &nbsp; <sup>1</sup> | 16 | 112 | 382 | 64 | 64x500 | 4 |
| Standardowa_A10 | 8  | 56  | 382 | 32 | 32 x 500 | 2 |
| Standardowa_A11 | 16 | 112 | 382 | 64 | 64x500 | 4 |

<sup>1</sup> W przypadku aplikacji MPI dedykowana sieć zaplecza RDMA jest włączana przez sieć FDR InfiniBand, która zapewnia bardzo małe opóźnienia i wysoką przepustowość.  

> [!NOTE]
> [Maszyny wirtualne A8 – A11 są planowane do wycofania w dniu 3/2021](https://azure.microsoft.com/updates/a8-a11-azure-virtual-machine-sizes-will-be-retired-on-march-1-2021/). Zdecydowanie zalecamy, aby nie tworzyć żadnych nowych maszyn wirtualnych A8 – A11. Przeprowadź migrację wszystkich istniejących maszyn wirtualnych z systemem A8 – A11 do nowszych i zaawansowanych rozmiarów maszyn wirtualnych o wysokiej wydajności, takich jak H, HB, HC, HBv2, a także rozmiarów maszyn wirtualnych obliczeń ogólnego przeznaczenia, takich jak D, E i F, w celu uzyskania lepszej wydajności cenowej. Aby uzyskać więcej informacji, zobacz [Przewodnik migracji HPC](https://azure.microsoft.com/resources/hpc-migration-guide/).

<br>

### <a name="d-series"></a>Seria D  

**Nowsze zalecenie dotyczące rozmiaru**: serii [Dav4](dav4-dasv4-series.md), [Dv4](dv4-dsv4-series.md) i [Ddv4](ddv4-ddsv4-series.md)

ACU: 160-250 <sup>1</sup>

Premium Storage: nieobsługiwane

Buforowanie Premium Storage: nieobsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepływność magazynu: IOPS/Odczyt MB/s/zapis MB/s | Maksymalna liczba dysków danych/przepływność: IOPS | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|---|---|---|---|---|---|---|
| Standardowa_D1  | 1 | 3,5 | 50  | 3000/46/23    | 4/4x500   | 2/500  |
| Standardowa_D2  | 2 | 7   | 100 | 6000/93/46    | 8/8x500   | 2/1000 |
| Standardowa_D3  | 4 | 14  | 200 | 12000/187/93  | 16/16x500 | 4/2000 |
| Standardowa_D4  | 8 | 28  | 400 | 24000/375/187 | 32/32x500 | 8/4000 |

<sup>1</sup> rodzina maszyn wirtualnych może działać na jednym z następujących procesorów: 2,2 GHz intel Xeon® E5-2660 v2, 2,4 GHz intel Xeon® E5-2673 v3 (Haswell) lub 2,3 GHz intel Xeon® E5-2673 v4 (Broadwell)  

<br>

### <a name="d-series---memory-optimized"></a>Seria D — zoptymalizowana pod kątem pamięci  

**Nowsze zalecenie dotyczące rozmiaru**: serii [Dav4](dav4-dasv4-series.md), [Dv4](dv4-dsv4-series.md) i [Ddv4](ddv4-ddsv4-series.md)

ACU: 160-250 <sup>1</sup>

Premium Storage: nieobsługiwane

Buforowanie Premium Storage: nieobsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepływność magazynu: IOPS/Odczyt MB/s/zapis MB/s | Maksymalna liczba dysków danych/przepływność: IOPS | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|---|---|---|---|---|---|---|
| Standardowa_D11 | 2  | 14  | 100 | 6000/93/46    | 8/8x500   | 2/1000 |
| Standardowa_D12 | 4  | 28  | 200 | 12000/187/93  | 16/16x500 | 4/2000 |
| Standardowa_D13 | 8  | 56  | 400 | 24000/375/187 | 32/32x500 | 8/4000 |
| Standardowa_D14 | 16 | 112 | 800 | 48000/750/375 | 64/64x500 | 8/8000 |

<sup>1</sup> rodzina maszyn wirtualnych może działać na jednym z następujących procesorów: 2,2 GHz intel Xeon® E5-2660 v2, 2,4 GHz intel Xeon® E5-2673 v3 (Haswell) lub 2,3 GHz intel Xeon® E5-2673 v4 (Broadwell)  

<br>

### <a name="preview-dc-series"></a>Wersja zapoznawcza: Seria DC

**Nowsze zalecenie dotyczące rozmiaru**: [Seria DCsv2](dcv2-series.md)

Premium Storage: obsługiwane

Buforowanie Premium Storage: obsługiwane

Seria DC używa najnowszej generacji procesora Intel XEON E-2176G z technologią SGX, dzięki czemu technologia zwiększania wydajności Intel Turbo może mieć wartość do 4,7 GHz. 

| Rozmiar          | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: liczba operacji we/wy na sekundę / MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: liczba operacji we/wy na sekundę / MB/s | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|---------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC2s | 2    | 8           | 100                    | 2              | 4000/32 (43)                                                          | 3200/48                                  | 2 / 1500                                     |
| Standard_DC4s | 4    | 16          | 200                    | 4              | 8000/64 (86)                                                          | 6400/96                                  | 2 / 3000                                     |

> [!IMPORTANT]
>
> Maszyny wirtualne z serii DC to [maszyny wirtualne 2. generacji](./generation-2.md#creating-a-generation-2-vm) i obsługują tylko `Gen2` obrazy.


### <a name="ds-series"></a>Seria DS  

**Nowsze zalecenie dotyczące rozmiaru**: serii [Dasv4](dav4-dasv4-series.md), [Dsv4](dv4-dsv4-series.md) i [Ddsv4](ddv4-ddsv4-series.md)

ACU: 160-250 <sup>1</sup>

Premium Storage: obsługiwane

Buforowanie Premium Storage: obsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu w pamięci podręcznej i temp: IOPS/MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność dysku w pamięci podręcznej: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|---|---|---|---|---|---|---|---|
| Standardowa_DS1 | 1 | 3,5 | 7  | 4  | 4000/32 (43)    | 3200/32   | 2/500  |
| Standardowa_DS2 | 2 | 7   | 14 | 8  | 8000/64 (86)    | 6400/64   | 2/1000 |
| Standardowa_DS3 | 4 | 14  | 28 | 16 | 16000/128 (172) | 12800/128 | 4/2000 |
| Standardowa_DS4 | 8 | 28  | 56 | 32 | 32000/256 (344) | 25600/256 | 8/4000 |

<sup>1</sup> rodzina maszyn wirtualnych może działać na jednym z następujących procesorów: 2,2 GHz intel Xeon® E5-2660 v2, 2,4 GHz intel Xeon® E5-2673 v3 (Haswell) lub 2,3 GHz intel Xeon® E5-2673 v4 (Broadwell)  

<br>

### <a name="ds-series---memory-optimized"></a>Zoptymalizowane pod kątem pamięci z serii DS  

**Nowsze zalecenie dotyczące rozmiaru**: serii [Dasv4](dav4-dasv4-series.md), [Dsv4](dv4-dsv4-series.md) i [Ddsv4](ddv4-ddsv4-series.md)

ACU: 160-250 <sup>1, 2</sup>

Premium Storage: obsługiwane

Buforowanie Premium Storage: obsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu w pamięci podręcznej i temp: IOPS/MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność dysku w pamięci podręcznej: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|---|---|---|---|---|---|---|---|
| Standardowa_DS11 | 2  | 14  | 28  | 8  | 8000/64 (72)    | 6400/64   | 2/1000 |
| Standardowa_DS12 | 4  | 28  | 56  | 16 | 16000/128 (144) | 12800/128 | 4/2000 |
| Standardowa_DS13 | 8  | 56  | 112 | 32 | 32000/256 (288) | 25600/256 | 8/4000 |
| Standardowa_DS14 | 16 | 112 | 224 | 64 | 64000/512 (576) | 51200/512 | 8/8000 |

<sup>1</sup> maksymalna przepływność dysku (IOPS lub MB/s) możliwa dla maszyny wirtualnej z serii DS może być ograniczona przez liczbę, rozmiar i rozkładanie dołączonych dysków.  Aby uzyskać szczegółowe informacje, zobacz [projektowanie pod kątem wysokiej wydajności](premium-storage-performance.md).
<sup>2</sup> rodzina maszyn wirtualnych może działać na jednym z następujących procesorów: 2,2 GHz intel Xeon® E5-2660 v2, 2,4 GHz intel Xeon® E5-2673 v3 (Haswell) lub 2,3 GHz intel Xeon® E5-2673 v4 (Broadwell)  

<br>

### <a name="ls-series"></a>Seria Ls

**Nowsze zalecenie dotyczące rozmiaru**: [Seria Lsv2](lsv2-series.md)

Maszyny wirtualne serii Ls oferują do 32 procesorów wirtualnych vCPU — procesor [Intel® Xeon® z rodziny E5 v3](https://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). Wydajność procesora CPU dla serii Ls jest taka sama jak w przypadku maszyn wirtualnych serii G/GS. Na procesor wirtualny vCPU przypada pamięć o pojemności 8 GiB.

Seria ls nie obsługuje tworzenia lokalnej pamięci podręcznej w celu zwiększenia liczby operacji we/wy osiągalnej przez trwałe dyski danych. Wysoka przepływność i liczba operacji we/wy na dysku lokalnym sprawia, że maszyny wirtualne z serii LS są idealne dla magazynów NoSQL, takich jak Apache Cassandra i MongoDB, które replikuje dane między wieloma maszynami wirtualnymi w celu osiągnięcia trwałości w przypadku awarii pojedynczej maszyny wirtualnej.

ACU: 180–240

Premium Storage: obsługiwane

Buforowanie Premium Storage: nieobsługiwane

| Rozmiar | Procesor wirtualny | Pamięć (GiB) | Magazyn tymczasowy (GiB) | Maks. liczba dysków danych | Maksymalna przepływność magazynu (IOPS/MB/s) | Maksymalna przepływność dysku w pamięci podręcznej (IOPS/MB/s) | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|---|---|---|---|---|---|---|---|
| Standardowa_L4s   | 4  | 32  | 678  | 16 | 20000/200 | 5000/125  | 2/4000  |
| Standardowa_L8s   | 8  | 64  | 1388 | 32 | 40000/400 | 10000/250 | 4/8000  |
| Standardowa_L16s  | 16 | 128 | 2807 | 64 | 80000/800 | 20000/500 | 8/16000 |
| Standard_L32s &nbsp; <sup>1</sup> | 32 | 256 | 5630 | 64 | 160000/1600 | 40000/1000 | 8/20000 |

Maksymalna przepływność dysku możliwa w przypadku maszyn wirtualnych z serii LS może być ograniczona przez liczbę, rozmiar i rozłożenie dowolnych dołączonych dysków. Aby uzyskać szczegółowe informacje, zobacz [projektowanie pod kątem wysokiej wydajności](premium-storage-performance.md).

<sup>1</sup> wystąpienie jest izolowane do sprzętu dedykowanego pojedynczemu klientowi.

### <a name="gs-series"></a>Seria GS

**Nowsze zalecenie dotyczące rozmiaru**: serii [Easv4](eav4-easv4-series.md), [Esv4](ev4-esv4-series.md), [Edsv4](edv4-edsv4-series.md) i serii [M](m-series.md)

ACU: 180 – 240 <sup>1</sup>

Premium Storage: obsługiwane

Buforowanie Premium Storage: obsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: liczba operacji we/wy na sekundę / MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność dysku w pamięci podręcznej: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|---|---|---|---|---|---|---|---|
| Standardowa_GS1 | 2 | 28  | 56  | 8  | 10000/100 (264)  | 5000/ 125  | 2/2000 |
| Standardowa_GS2 | 4 | 56  | 112 | 16 | 20000/200 (528)  | 10000/ 250 | 2/4000 |
| Standardowa_GS3 | 8 | 112 | 224 | 32 | 40000/400 (1056) | 20000/ 500 | 4/8000 |
| Standard_GS4 &nbsp; <sup>3</sup> | 16 | 224 | 448 | 64 | 80000/800 (2112) | 40000/1000 | 8/16000 |
| Standard_GS5 &nbsp; <sup>2, &nbsp; 3</sup> | 32 | 448 |896 | 64 |160000/1600 (4224) | 80000/2000 | 8/20000 |

<sup>1</sup> maksymalna przepływność dysku (IOPS lub MB/s) możliwa dla maszyny wirtualnej serii GS może być ograniczona przez liczbę, rozmiar i rozkładanie dołączonych dysków. Aby uzyskać szczegółowe informacje, zobacz [projektowanie pod kątem wysokiej wydajności](premium-storage-performance.md).

<sup>2</sup> wystąpienie jest izolowane do sprzętu dedykowanego pojedynczemu klientowi.

dostępne są <sup>3</sup> ograniczone rozmiary rdzeni.

<br>

### <a name="g-series"></a>Seria G

**Nowsze zalecenie dotyczące rozmiaru**: serii [Eav4](eav4-easv4-series.md), [Ev4](ev4-esv4-series.md) i [Edv4](edv4-edsv4-series.md) oraz seria [M](m-series.md)

ACU: 180–240

Premium Storage: nieobsługiwane

Buforowanie Premium Storage: nieobsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maksymalna przepływność magazynu: IOPS/Odczyt MB/s/zapis MB/s | Maksymalna liczba dysków danych/przepływność: IOPS | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|---|---|---|---|---|---|---|
| Standardowa_G1  | 2  | 28  | 384  | 6000/93/46    | 8/8x500   | 2/2000  |
| Standardowa_G2  | 4  | 56  | 768  | 12000/187/93  | 16/16x500 | 2/4000  |
| Standardowa_G3  | 8  | 112 | 1536 | 24000/375/187 | 32/32x500 | 4/8000  |
| Standardowa_G4  | 16 | 224 | 3072 | 48000/750/375 | 64/64x500 | 8/16000 |
| Standard_G5 &nbsp; <sup>1</sup> | 32 | 448 | 6144 | 96000/1500/750| 64/64x500 | 8/20000 |

<sup>1</sup> wystąpienie jest izolowane do sprzętu dedykowanego pojedynczemu klientowi.
<br>

### <a name="nv-series"></a>Seria NV
**Nowsze zalecenie dotyczące rozmiaru**: serii [NVv3](nvv3-series.md) i [NVv4](nvv4-series.md)

Maszyny wirtualne z serii NV są obsługiwane przez procesory [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) i technologię NVIDIA GRID dla aplikacji klasycznych i pulpitów wirtualnych, na których klienci mogą wizualizować swoje dane lub symulacje. Użytkownicy mogą wizualizować przepływy pracy intensywnie korzystających z grafiki w wystąpieniach TRWAŁych w celu uzyskania najwyższej jakości grafiki i dodatkowo uruchamiania obciążeń o pojedynczej precyzji, takich jak kodowanie i renderowanie. Maszyny wirtualne z serii NV są również obsługiwane przez procesory Intel Xeon E5-2690 v3 (Haswell).

Każdy procesor GPU w wystąpieniach TRWAŁych zawiera licencję siatki. Ta licencja zapewnia elastyczność używania wystąpienia TRWAŁEgo jako wirtualnej stacji roboczej dla jednego użytkownika lub 25 jednoczesnych użytkowników może połączyć się z MASZYNą wirtualną w scenariuszu aplikacji wirtualnej.

Premium Storage: nieobsługiwane

Buforowanie Premium Storage: nieobsługiwane

Migracja na żywo: nieobsługiwane

Aktualizacje z zachowaniem pamięci: nieobsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Pamięć procesora GPU: GiB | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych | Wirtualne stacje robocze | Aplikacje wirtualne |
|---|---|---|---|---|---|---|---|---|---|
| Standardowa_NV6  | 6  | 56  | 340  | 1 | 8  | 24 | 1 | 1 | 25  |
| Standardowa_NV12 | 12 | 112 | 680  | 2 | 16 | 48 | 2 | 2 | 50  |
| Standardowa_NV24 | 24 | 224 | 1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 procesor GPU = połowa karty M60.
<br>

### <a name="nc-series"></a>Seria NC
**Nowsza wersja zalecenia**: [NC T4 v3 — seria](nct4-v3-series.md)

Maszyny wirtualne z serii NC są obsługiwane przez kartę [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) i procesor Intel Xeon E5-2690 v3 (Haswell). Użytkownicy mogą szybciej przetwarzanie dane, wykorzystując CUDA do obsługi aplikacji, symulacji awarii, renderowanie śledzenia, śledzenie, uczenie głębokie i wiele innych. Konfiguracja NC24r zapewnia małe opóźnienia i wysoką przepływność interfejsu sieciowego zoptymalizowane pod kątem ściśle sprzężonych równoległych obciążeń obliczeniowych.

[Premium Storage](premium-storage-performance.md): nieobsługiwane<br>
[Buforowanie Premium Storage](premium-storage-performance.md): nieobsługiwane<br>
[Migracja na żywo](maintenance-and-updates.md): nieobsługiwane<br>
[Aktualizacje z zachowaniem pamięci](maintenance-and-updates.md): nieobsługiwane<br>
[Obsługa generacji maszyny wirtualnej](generation-2.md): generacja 1<br>
<br>

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Pamięć procesora GPU: GiB | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych |
|---|---|---|---|---|---|---|---|
| Standardowa_NC6    | 6  | 56  | 340  | 1 | 12 | 24 | 1 |
| Standardowa_NC12   | 12 | 112 | 680  | 2 | 24 | 48 | 2 |
| Standardowa_NC24   | 24 | 224 | 1440 | 4 | 48 | 64 | 4 |
| Standardowa_NC24r* | 24 | 224 | 1440 | 4 | 48 | 64 | 4 |

1 procesor GPU = połowa karty K80.

*Obsługa technologii RDMA


<br>


### <a name="ncv2-series"></a>Seria NCv2
**Zalecenie dotyczące nowszej wielkości**: [NC T4 v3 — seria](nct4-v3-series.md) i [NC V100 v3](ncv3-series.md)

Maszyny wirtualne z serii NCv2 są obsługiwane przez procesory NVIDIA Tesla P100 GPU. Te procesory GPU mogą zapewnić ponad 2. wydajność obliczeniową serii NC. Klienci mogą korzystać z tych zaktualizowanych procesorów GPU dla tradycyjnych obciążeń HPC, takich jak modelowanie zbiorników, sekwencjonowanie DNA, analiza białka, symulacje Monte Carlo i inne. Poza procesorami GPU maszyny wirtualne serii NCv2 są również obsługiwane przez procesory Intel Xeon E5-2690 v4 (Broadwell).

Konfiguracja NC24rs v2 zapewnia małe opóźnienia i wysoką przepływność interfejsu sieciowego zoptymalizowane pod kątem ściśle sprzężonych równoległych obciążeń obliczeniowych.

[Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Buforowanie Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Migracja na żywo](maintenance-and-updates.md): nieobsługiwane<br>
[Aktualizacje z zachowaniem pamięci](maintenance-and-updates.md): nieobsługiwane<br>
[Obsługa generacji maszyny wirtualnej](generation-2.md): generacja 1 i 2<br>

> W przypadku tej serii maszyn wirtualnych limit przydziału vCPU (rdzeń) w subskrypcji jest początkowo ustawiany na 0 w każdym regionie. [Zażądaj zwiększenia limitu przydziału vCPU](../azure-portal/supportability/resource-manager-core-quotas-request.md) dla tej serii w [dostępnym regionie](https://azure.microsoft.com/regions/services/).
>
| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Pamięć procesora GPU: GiB | Maks. liczba dysków danych | Maksymalna przepływność dysku w pamięci podręcznej: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych |
|---|---|---|---|---|---|---|---|---|
| Standard_NC6s_v2    | 6  | 112 | 736  | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v2   | 12 | 224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v2   | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |
| Standard_NC24rs_v2 * | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 procesor GPU = jedna karta P100.

*Obsługa technologii RDMA

<br>

### <a name="nd-series"></a>Seria ND
**Zalecenie dotyczące nowszej wielkości**: Seria [NDv2](ndv2-series.md) i [NC V100 v3](ncv3-series.md)

Maszyny wirtualne z serii ND to nowe dodanie do rodziny procesorów GPU zaprojektowanej dla systemu AI i obciążeń głębokiego uczenia. Oferują one doskonałą wydajność na potrzeby szkoleń i wnioskowania. Wystąpienia ND są obsługiwane przez procesory [NVIDIA Tesla P40](https://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) i procesory Intel Xeon E5-2690 v4 (Broadwell). Te wystąpienia zapewniają doskonałą wydajność operacji zmiennoprzecinkowych o pojedynczej precyzji dla obciążeń AI wykorzystujących Microsoft Cognitive Toolkit, TensorFlow, Caffe i inne platformy. Seria ND oferuje również znacznie większy rozmiar pamięci procesora GPU (24 GB), co umożliwia dopasowanie znacznie większych modeli sieci neuronowych. Podobnie jak w przypadku serii NC, Seria ND oferuje konfigurację z dodatkową siecią o małym opóźnieniu i wysokiej przepływności za pośrednictwem funkcji RDMA oraz łączności InfiniBand, dzięki czemu można uruchamiać zadania szkoleniowe o dużej skali obejmujące wiele procesorów GPU.

[Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Buforowanie Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Migracja na żywo](maintenance-and-updates.md): nieobsługiwane<br>
[Aktualizacje z zachowaniem pamięci](maintenance-and-updates.md): nieobsługiwane<br>
[Obsługa generacji maszyny wirtualnej](generation-2.md): generacja 1 i 2<br>

> Dla tej serii maszyn wirtualnych limit przydziału vCPU (rdzeń) na region w ramach subskrypcji jest początkowo ustawiony na 0. [Zażądaj zwiększenia limitu przydziału vCPU](../azure-portal/supportability/resource-manager-core-quotas-request.md) dla tej serii w [dostępnym regionie](https://azure.microsoft.com/regions/services/).
>
| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Pamięć procesora GPU: GiB | Maks. liczba dysków danych | Maksymalna przepływność dysku w pamięci podręcznej: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych |
|---|---|---|---|---|---|---|---|---|
| Standard_ND6s    | 6  | 112 | 736  | 1 | 24 | 12 | 20000/200 | 4 |
| Standard_ND12s   | 12 | 224 | 1474 | 2 | 48 | 24 | 40000/400 | 8 |
| Standard_ND24s   | 24 | 448 | 2948 | 4 | 24 | 32 | 80000/800 | 8 |
| Standard_ND24rs * | 24 | 448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |

1 procesor GPU = jedna karta P40.

*Obsługa technologii RDMA

<br>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.