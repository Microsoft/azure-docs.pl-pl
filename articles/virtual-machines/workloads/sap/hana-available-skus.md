---
title: Jednostki SKU dla SAP HANA na platformie Azure (duże wystąpienia) | Microsoft Docs
description: Jednostki SKU dla SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
keywords: HLI, HANA, SKU, S896, S224, S448, S672, Optane, SAP
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/23/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fd979f553ff03d8373948d73c416a96bb140b544
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94964766"
---
# <a name="available-skus-for-hana-large-instances"></a>Dostępne jednostki SKU dla dużych wystąpień HANA

Usługa SAP HANA na platformie Azure (duże wystąpienia) oparta na tylko sygnaturach poprawki 3 jest dostępna w kilku konfiguracjach w regionach świadczenia usługi Azure:

- Australia Wschodnia
- Australia Południowo-Wschodnia
- Japonia Wschodnia
- Japonia Zachodnia

Usługa SAP HANA na platformie Azure (duże wystąpienia) oparta na sygnaturach poprawek 4 jest dostępna w kilku konfiguracjach w regionach świadczenia usługi Azure:

- Zachodnie stany USA 2
- East US
- Wschodnie stany USA 2
- South Central US
- West Europe
- Europa Północna



Lista dostępnych dużych wystąpień platformy Azure, które są oferowane w postaci list poniżej.

> [!IMPORTANT]
> Należy pamiętać o pierwszej kolumnie, która reprezentuje stan certyfikacji platformy HANA dla każdego z typów dużych wystąpień na liście. Kolumna powinna być skorelowane z [SAP HANA katalogu sprzętu](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) dla jednostek SKU platformy Azure, które zaczynają się od litery **S**



| SAP HANA certyfikat | Model | Całkowita ilość pamięci | Pamięć DRAM | Optane pamięci | Magazyn | Dostępność |
| --- | --- | --- | --- | --- | --- | --- |
| TAK <br />[OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2185), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2265) | SAP HANA na platformie Azure s96<br /> — 2 x procesor Intel® Xeon® E7-8890 v4 <br /> 48 rdzeni procesora CPU i 96 wątki procesora |  768 GB | 768 GB | --- | 3,0 TB | Dostępne |
| TAK <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2186), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2269) | SAP HANA na platformie Azure S224<br /> — 4 x procesor Intel® Xeon® Platinum 8276 <br /> 112 rdzeni procesora CPU i 224 wątki procesora |  3,0 TB | 3,0 TB | --- | 6,3 TB | Dostępne |
| TAK <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2297) | SAP HANA na platformie Azure S224m<br /> — 4 x procesor Intel® Xeon® Platinum 8276 <br /> 112 rdzeni procesora CPU i 224 wątki procesora |  6,0 TB | 6,0 TB | --- | 10,5 TB | Dostępne |
| TAK <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2381) | SAP HANA na platformie Azure S224om<br /> — 4 x procesor Intel® Xeon® Platinum 8276 <br /> 112 rdzeni procesora CPU i 224 wątki procesora | 6,0 TB |  3,0 TB |  3,0 TB | 10,5 TB | Dostępne |
| NO | SAP HANA na platformie Azure S224oo<br /> — 4 x procesor Intel® Xeon® Platinum 8276 <br /> 112 rdzeni procesora CPU i 224 wątki procesora | 4,5 TB |  1,5 TB |  3,0 TB | 8,4 TB | Dostępne |
| NO | SAP HANA na platformie Azure S224ooo<br /> — 4 x procesor Intel® Xeon® Platinum 8276 <br /> 112 rdzeni procesora CPU i 224 wątki procesora | 7,5 TB |  1,5 TB |  6,0 TB | 12,7 TB | Dostępne |
| NO | SAP HANA na platformie Azure S224oom<br /> — 4 x procesor Intel® Xeon® Platinum 8276 <br /> 112 rdzeni procesora CPU i 224 wątki procesora | 9,0 TB |  3,0 TB |  6,0 TB | 14,8 TB | Dostępne |
| TAK <br />[OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1983), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2268) | SAP HANA na platformie Azure S384<br /> – 8 x procesor Intel® Xeon® E7-8890 v4<br /> 192 rdzeni procesora CPU i 384 wątki procesora |  4,0 TB | 4,0 TB | --- | 16 TB | Dostępne |
| TAK <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2080) | SAP HANA na platformie Azure S384m<br /> – 8 x procesor Intel® Xeon® E7-8890 v4<br /> 192 rdzeni procesora CPU i 384 wątki procesora |  6,0 TB | 6,0 TB | --- | 18 TB |  Dostępne  |
| TAK <br />[OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1984), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2267) | SAP HANA na platformie Azure S384xm<br /> – 8 x procesor Intel® Xeon® E7-8890 v4<br /> 192 rdzeni procesora CPU i 384 wątki procesora |  8,0 TB | 8,0 TB | --- | 22 TB | Dostępne |
| TAK <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2378) | SAP HANA na platformie Azure S448<br /> – 8 x procesor Intel® Xeon® Platinum 8276 <br /> 224 rdzeni procesora CPU i 448 wątki procesora | 6,0 TB |  6,0 TB |  --- | 10,5 TB | Dostępne (tylko wersja rev 4) |
| TAK <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2377) | SAP HANA na platformie Azure S448m<br /> – 8 x procesor Intel® Xeon® Platinum 8276 <br /> 224 rdzeni procesora CPU i 448 wątki procesora | 12,0 TB |  12,0 TB |  --- | 18,9 TB | Dostępne (tylko wersja rev 4) |
| NO | SAP HANA na platformie Azure S448oo<br /> – 8 x procesor Intel® Xeon® Platinum 8276 <br /> 224 rdzeni procesora CPU i 448 wątki procesora | 9,0 TB |  3,0 TB |  6,0 TB | 14,8 TB  | Dostępne (tylko wersja rev 4) |
| NO | SAP HANA na platformie Azure S448om<br /> – 8 x procesor Intel® Xeon® Platinum 8276 <br /> 224 rdzeni procesora CPU i 448 wątki procesora | 12,0 TB |  6,0 TB |  6,0 TB | 18,9 TB  | Dostępne (tylko wersja rev 4) |
| NO | SAP HANA na platformie Azure S448ooo<br /> – 8 x procesor Intel® Xeon® Platinum 8276 <br /> 224 rdzeni procesora CPU i 448 wątki procesora | 15,0 TB |  3,0 TB |  12,0 TB | 23,2 TB  | Dostępne (tylko wersja rev 4) |
| NO | SAP HANA na platformie Azure S448oom<br /> – 8 x procesor Intel® Xeon® Platinum 8276 <br /> 224 rdzeni procesora CPU i 448 wątki procesora | 18,0 TB |  6,0 TB |  12,0 TB | 27,4 TB  | Dostępne (tylko wersja rev 4) |
| TAK <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2049) | SAP HANA na platformie Azure S576m<br /> – 12 x procesor Intel® Xeon® E7-8890 v4<br /> 288 rdzeni procesora CPU i 576 wątki procesora |  12,0 TB | 12,0 TB | --- | 28 TB | Dostępne (tylko wersja rev 4) |
| NO | SAP HANA na platformie Azure S576xm<br /> – 12 x procesor Intel® Xeon® E7-8890 v4<br /> 288 rdzeni procesora CPU i 576 wątki procesora |  18,0 TB | 18.0 | --- |  41 TB | Dostępne |
| TAK <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2380) | SAP HANA na platformie Azure S672<br /> – 12 x procesor Intel® Xeon® Platinum 8276 <br /> 336 rdzeni procesora CPU i 672 wątki procesora | 9,0 TB |  9,0 TB |  --- | 14,7 TB | Dostępne (tylko wersja rev 4) |
| TAK <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2379) | SAP HANA na platformie Azure S672m<br /> – 12 x procesor Intel® Xeon® Platinum 8276 <br /> 336 rdzeni procesora CPU i 672 wątki procesora | 18,0 TB |  18,0 TB |  --- | 27,4 TB | Dostępne (tylko wersja rev 4) |
| NO | SAP HANA na platformie Azure S672oo<br /> – 12 x procesor Intel® Xeon® Platinum 8276 <br /> 336 rdzeni procesora CPU i 672 wątki procesora | 13,5 TB |  4,5 TB |  9,0 TB | 21,1 TB  | Dostępne (tylko wersja rev 4) |
| NO | SAP HANA na platformie Azure S672om<br /> – 12 x procesor Intel® Xeon® Platinum 8276 <br /> 336 rdzeni procesora CPU i 672 wątki procesora | 18,0 TB |  9,0 TB |  9,0 TB | 27,4 TB  | Dostępne (tylko wersja rev 4) |
| NO | SAP HANA na platformie Azure S672ooo<br /> – 12 x procesor Intel® Xeon® Platinum 8276 <br /> 336 rdzeni procesora CPU i 672 wątki procesora | 22,5 TB |  4,5 TB |  18,0 TB | 33,7 TB  | Dostępne (tylko wersja rev 4) |
| NO | SAP HANA na platformie Azure S672oom<br /> – 12 x procesor Intel® Xeon® Platinum 8276 <br /> 336 rdzeni procesora CPU i 672 wątki procesora | 27,0 TB |  9,0 TB |  18,0 TB | 40,0 TB  | Dostępne (tylko wersja rev 4) |
| TAK <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1985) | SAP HANA na platformie Azure S768m<br /> – 16 x procesor Intel® Xeon® E7-8890 v4<br /> 384 rdzeni procesora CPU i 768 wątki procesora |  16,0 TB | 16,0 TB | -- | 36 TB | Dostępne |
| NO | SAP HANA na platformie Azure S768xm<br /> – 16 x procesor Intel® Xeon® E7-8890 v4<br /> 384 rdzeni procesora CPU i 768 wątki procesora |  24,0 TB | 24,0 TB | --- | 56 TB | Dostępne |
|  TAK <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2376)  | SAP HANA na platformie Azure S896<br /> – 16 x procesor Intel® Xeon® Platinum 8276 <br /> 448 rdzeni procesora CPU i 896 wątki procesora | 12,0 TB |  12,0 TB |  --- | 18,9 TB | Dostępne (tylko wersja rev 4) |
| TAK <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2328) | SAP HANA na platformie Azure S896m<br /> – 16 x procesor Intel® Xeon® Platinum 8276 <br /> 448 rdzeni procesora CPU i 896 wątki procesora | 24,0 TB | 24,0 TB | -- | 35,8 TB | Dostępne |
| NO | SAP HANA na platformie Azure S896oo<br /> – 16 x procesor Intel® Xeon® Platinum 8276 <br /> 448 rdzeni procesora CPU i 896 wątki procesora | 18,0 TB |  6,0 TB |  12,0 TB | 27,4 TB  | Dostępne (tylko wersja rev 4) |
| NO | SAP HANA na platformie Azure S896om<br /> – 16 x procesor Intel® Xeon® Platinum 8276 <br /> 448 rdzeni procesora CPU i 896 wątki procesora | 24,0 TB |  12,0 TB |  12,0 TB | 35,8 TB  | Dostępne (tylko wersja rev 4) |
| NO | SAP HANA na platformie Azure S896ooo<br /> – 16 x procesor Intel® Xeon® Platinum 8276 <br /> 448 rdzeni procesora CPU i 896 wątki procesora | 30,0 TB |  6,0 TB |  24,0 TB | 44,3 TB  | Dostępne (tylko wersja rev 4) |
| NO | SAP HANA na platformie Azure S896oom<br /> – 16 x procesor Intel® Xeon® Platinum 8276 <br /> 448 rdzeni procesora CPU i 896 wątki procesora | 36,0 TB |  12,0 TB |  24,0 TB | 52,7 TB  | Dostępne (tylko wersja rev 4) |
| TAK <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1986) | SAP HANA na platformie Azure S960m<br /> – 20 x procesor Intel® Xeon® E7-8890 v4<br /> 480 rdzeni procesora CPU i 960 wątki procesora |  20,0 TB | 20,0 TB | -- | 46 TB | Dostępne (tylko wersja rev 4) |


- Rdzenie procesora = suma rdzeni procesora CPU, które nie są oparte na funkcji Hyper-Thread, sumy procesorów jednostki serwera.
- Wątki procesora = suma wątków obliczeniowych dostarczanych przez rdzenie procesora CPU przez wątki z sumą procesorów jednostki serwera. Większość jednostek jest skonfigurowanych domyślnie do korzystania z technologii Hyper-Threading.
- Na podstawie zaleceń dotyczących dostawcy S768m, S768xm i S960m nie są skonfigurowane do używania Hyper-Threading do uruchamiania SAP HANA.


> [!IMPORTANT]
> Nie można już kupić następujących jednostek SKU: S72, S72m, S144, S144m, S192 i S192m 

Wybrane konfiguracje są zależne od obciążenia, zasobów procesora CPU i żądanej pamięci. Istnieje możliwość, że obciążenie OLTP wykorzystuje jednostki SKU zoptymalizowane pod kątem obciążenia OLAP. 

Dwie różne klasy sprzętu dzielą jednostki SKU na:

- S72, S72m, s96, S144, S144m, S192, S192m, S192xm, S224, S224m, S224oo, S224om, S224ooo, S224oom są określane jako "typ I Klasa" jednostek SKU.
- Wszystkie inne jednostki SKU są określane jako "Typ II Klasa" jednostek SKU.
- Jeśli interesuje jednostki SKU, które nie znajdują się jeszcze w katalogu sprzętu SAP, skontaktuj się z zespołem konto Microsoft, aby uzyskać więcej informacji. 


Pełna sygnatura dużego wystąpienia HANA nie jest przeznaczona wyłącznie dla jednego klienta&#39;s. Ten fakt dotyczy stojaków zasobów obliczeniowych i magazynu połączonych przez sieć szkieletową sieci wdrożoną na platformie Azure. Infrastruktura dużej instancji HANA, taka jak Azure, wdraża różne &quot; dzierżawy klientów, &quot; które są odizolowane od siebie na następujących trzech poziomach:

- **Sieć**: Izolacja za pomocą sieci wirtualnych w sygnaturze dużej instancji Hana.
- **Magazyn**: Izolacja za poorednictwem maszyn wirtualnych magazynu z przydzielonymi woluminami magazynu i Izoluj woluminy magazynu między dzierżawcami.
- **Obliczanie**: dedykowane przypisywanie jednostek serwera do pojedynczej dzierżawy. Nie ma twardej ani miękkiej partycjonowania jednostek serwerów. Brak udostępniania pojedynczego serwera lub jednostki hosta między dzierżawcami. 

Wdrożenia jednostek dużego wystąpienia HANA między różnymi dzierżawcami nie są widoczne dla siebie nawzajem. Jednostki z dużymi wystąpieniami HANA wdrożone w różnych dzierżawcach nie mogą komunikować się bezpośrednio ze sobą na poziomie sygnatury dużego wystąpienia HANA. Na poziomie sygnatury dużego wystąpienia HANA można komunikować się tylko z jednostkami dużego wystąpienia HANA w obrębie jednej dzierżawy.

Wdrożona dzierżawa w sygnaturze dużego wystąpienia jest przypisana do jednej subskrypcji platformy Azure na potrzeby rozliczania. W przypadku sieci można uzyskać do niej dostęp z wirtualnych sieci innych subskrypcji platformy Azure w ramach tej samej rejestracji platformy Azure. W przypadku wdrożenia z inną subskrypcją platformy Azure w tym samym regionie świadczenia usługi Azure można również wybrać opcję poproszenia o rozdzieloną dzierżawę dużego wystąpienia HANA.

Istnieją znaczne różnice między działami SAP HANA w przypadku dużego wystąpienia usługi HANA i SAP HANA uruchomionych na maszynach wirtualnych wdrożonych na platformie Azure:

- Brak warstwy wirtualizacji dla SAP HANA na platformie Azure (duże wystąpienia). Uzyskasz wydajność podstawowego sprzętu bez systemu operacyjnego.
- W przeciwieństwie do platformy Azure, SAP HANA na platformie Azure (duże wystąpienia) serwer jest przeznaczony dla określonego klienta. Nie istnieje możliwość, że jednostka lub Host serwera nie jest podzielony na partycje. W związku z tym jednostka dużego wystąpienia HANA jest używana jako całość dla dzierżawy i z tą usługą. Ponowne uruchomienie lub zamknięcie serwera nie prowadzi automatycznie do systemu operacyjnego i SAP HANA wdrażane na innym serwerze. (Dla jednostek SKU typu I klasy jedynym wyjątkiem jest to, że serwer napotyka problemy i ponowne wdrożenie musi zostać wykonane na innym serwerze).
- W przeciwieństwie do platformy Azure, w której są wybrane typy procesorów hosta dla najlepszego współczynnika ceny/wydajności, typy procesorów wybrane dla SAP HANA na platformie Azure (duże wystąpienia) są największą wydajnością linii procesora Intel E7v3 i E7v4.

**Następne kroki**
- Odwołuje się do [wielkości HLI](hana-sizing.md)
