---
title: Jednostki SKU SAP HANA na platformie Azure (duże wystąpienia) | Microsoft Docs
description: Jednostki SKU dla SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
keywords: HLI, HANA, SKU, S896, S224, S448, S672, Optane, SAP
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 4/16/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017, references_regions
ms.openlocfilehash: 3ecbbe4d477f3e6c3c6606528c51b934b6cf534a
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718741"
---
# <a name="available-skus-for-hana-large-instances"></a>Dostępne jednostki SKU dla dużych wystąpień HANA

Usługa BareMetal Infrastructure (certyfikowana dla obciążeń SAP HANA) oparta na wersji Rev 4.2* jest dostępna w następujących regionach:
- West Europe
- Europa Północna
- Niemcy Zachodnio-środkowe z obsługą stref
- Wschodnie usa z obsługą stref
- Wschodnie stany USA 2
- South Central US
- Zachodnie stany USA 2 z obsługą stref

Usługa BareMetal Infrastructure (certyfikowana dla obciążeń SAP HANA) oparta na rev 3* ma ograniczoną dostępność w następujących regionach:
- Zachodnie stany USA
- East US 
- Australia Wschodnia 
- Australia Południowo-Wschodnia
- Japonia Wschodnia


Poniżej znajduje się lista dostępnych dużych wystąpień platformy Azure.

> [!IMPORTANT]
> Należy pamiętać o pierwszej kolumnie, która reprezentuje stan certyfikacji platformy HANA dla każdego z typów dużych wystąpień na liście. Kolumna powinna być skorelowana [z SAP HANA sprzętowym](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) dla jednostki SKU platformy Azure, które zaczynają się literą **S**.



| SAP HANA certyfikowane | Model | Łączna ilość pamięci | Pamięć DRAM | Optane pamięci | Storage | Dostępność |
| --- | --- | --- | --- | --- | --- | --- |
| TAK <br />[OLAP,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2185) [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2265) | SAP HANA na platformie Azure S96<br /> – 2 procesory Intel® Xeon® E7-8890 v4 <br /> 48 rdzeni procesora CPU i 96 wątków procesora CPU |  768 GB | 768 GB | --- | 3,0 TB | Dostępne |
| TAK <br /> [OLAP,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2186) [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2269) | SAP HANA na platformie Azure S224<br /> – 4 procesory Intel® Xeon® Dosm 8276 <br /> 112 rdzeni procesora CPU i 224 wątki procesora CPU |  3,0 TB | 3,0 TB | --- | 6,3 TB | Dostępne |
| TAK <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2297) | SAP HANA na platformie Azure S224 m<br /> – 4 procesory Intel® Xeon® Dosm 8276 <br /> 112 rdzeni procesora CPU i 224 wątki procesora CPU |  6,0 TB | 6,0 TB | --- | 10,5 TB | Dostępne |
| TAK <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2381) | SAP HANA na platformie Azure S224om<br /> – 4 procesory Intel® Xeon® Dosm 8276 <br /> 112 rdzeni procesora CPU i 224 wątki procesora CPU | 6,0 TB |  3,0 TB |  3,0 TB | 10,5 TB | Dostępne |
| NO | SAP HANA na platformie Azure S224oo<br /> – 4 x procesor Intel® Xeon® z serii Amd 8276 <br /> 112 rdzeni procesora CPU i 224 wątki procesora CPU | 4,5 TB |  1,5 TB |  3,0 TB | 8,4 TB | Dostępne |
| NO | SAP HANA na platformie Azure S224ooo<br /> – 4 x procesor Intel® Xeon® z serii Amd 8276 <br /> 112 rdzeni procesora CPU i 224 wątki procesora CPU | 7,5 TB |  1,5 TB |  6,0 TB | 12,7 TB | Dostępne |
| NO | SAP HANA na platformie Azure S224oom<br /> – 4 x procesor Intel® Xeon® z serii Amd 8276 <br /> 112 rdzeni procesora CPU i 224 wątki procesora CPU | 9,0 TB |  3,0 TB |  6,0 TB | 14,8 TB | Dostępne |
| TAK <br />[OLAP,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1983) [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2268) | SAP HANA na platformie Azure S384<br /> – 8 procesorów Intel® Xeon® E7-8890 v4<br /> 192 rdzenie procesora CPU i 384 wątki procesora CPU |  4,0 TB | 4,0 TB | --- | 16 TB | Dostępne |
| TAK <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2080) | SAP HANA na platformie Azure S384 m<br /> – 8 procesorów Intel® Xeon® E7-8890 v4<br /> 192 rdzenie procesora CPU i 384 wątki procesora CPU |  6,0 TB | 6,0 TB | --- | 18 TB |  Dostępne  |
| TAK <br />[OLAP,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1984) [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2267) | SAP HANA na platformie Azure S384xm<br /> – 8 procesorów Intel® Xeon® E7-8890 v4<br /> 192 rdzenie procesora CPU i 384 wątki procesora CPU |  8,0 TB | 8,0 TB | --- | 22 TB | Dostępne |
| TAK <br /> [OLAP,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2411) [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2378) | SAP HANA na platformie Azure S448<br /> – 8 x procesor Intel® Xeon® z procesorem Intel 8276 <br /> 224 rdzenie procesora CPU i 448 wątków procesora CPU | 6,0 TB |  6,0 TB |  --- | 10,5 TB | Dostępne (tylko Wersja 4.2) |
| TAK <br /> [OLAP,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2410) [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2377) | SAP HANA na platformie Azure S448 m<br /> – 8 x procesor Intel® Xeon® z procesorem Intel 8276 <br /> 224 rdzenie procesora CPU i 448 wątków procesora CPU | 12,0 TB |  12,0 TB |  --- | 18,9 TB | Dostępne (tylko Wersja 4.2) |
| NO | SAP HANA na platformie Azure S448oo<br /> – 8 x procesor Intel® Xeon® z procesorem Intel 8276 <br /> 224 rdzenie procesora CPU i 448 wątków procesora CPU | 9,0 TB |  3,0 TB |  6,0 TB | 14,8 TB  | Dostępne (tylko Rev 4.2) |
| NO | SAP HANA na platformie Azure S448om<br /> – 8 procesorów Intel® Xeon® Dosm 8276 <br /> 224 rdzenie procesora CPU i 448 wątków procesora CPU | 12,0 TB |  6,0 TB |  6,0 TB | 18,9 TB  | Dostępne (tylko Rev 4.2) |
| NO | SAP HANA na platformie Azure S448ooo<br /> – 8 procesorów Intel® Xeon® Dosm 8276 <br /> 224 rdzenie procesora CPU i 448 wątków procesora CPU | 15,0 TB |  3,0 TB |  12,0 TB | 23,2 TB  | Dostępne (tylko Wersja 4.2) |
| NO | SAP HANA na platformie Azure S448oom<br /> – 8 x procesor Intel® Xeon® z procesorem Intel 8276 <br /> 224 rdzenie procesora CPU i 448 wątków procesora CPU | 18,0 TB |  6,0 TB |  12,0 TB | 27,4 TB  | Dostępne (tylko Wersja 4.2) |
| TAK <br /> [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2049) | SAP HANA na platformie Azure S576 m<br /> – 12 x procesor Intel® Xeon® E7-8890 v4<br /> 288 rdzeni procesora CPU i 576 wątków procesora CPU |  12,0 TB | 12,0 TB | --- | 28 TB | Dostępne (tylko Wersja 4.2) |
| NO | SAP HANA na platformie Azure S576xm<br /> – 12 x procesor Intel® Xeon® E7-8890 v4<br /> 288 rdzeni procesora CPU i 576 wątków procesora CPU |  18,0 TB | 18.0 | --- |  41 TB | Dostępne |
| TAK <br /> [OLAP,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2409) [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2380) | SAP HANA na platformie Azure S672<br /> – 12 procesorów Intel® Xeon® Dosm 8276 <br /> 336 rdzeni procesora CPU i 672 wątki procesora CPU | 9,0 TB |  9,0 TB |  --- | 14,7 TB | Dostępne (tylko Rev 4.2) |
| TAK <br /> [OLAP,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2408) [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2379) | SAP HANA na platformie Azure S672 m<br /> – 12 procesorów Intel® Xeon® Dosm 8276 <br /> 336 rdzeni procesora CPU i 672 wątki procesora CPU | 18,0 TB |  18,0 TB |  --- | 27,4 TB | Dostępne (tylko Rev 4.2) |
| NO | SAP HANA na platformie Azure S672oo<br /> – 12 procesorów Intel® Xeon® Dosm 8276 <br /> 336 rdzeni procesora CPU i 672 wątki procesora CPU | 13,5 TB |  4,5 TB |  9,0 TB | 21,1 TB  | Dostępne (tylko Wersja 4.2) |
| NO | SAP HANA na platformie Azure S672om<br /> – 12 x procesor Intel® Xeon® z procesorem Intel 8276 <br /> 336 rdzeni procesora CPU i 672 wątki procesora CPU | 18,0 TB |  9,0 TB |  9,0 TB | 27,4 TB  | Dostępne (tylko Wersja 4.2) |
| NO | SAP HANA na platformie Azure S672ooo<br /> – 12 x procesor Intel® Xeon® z procesorem Intel 8276 <br /> 336 rdzeni procesora CPU i 672 wątki procesora CPU | 22,5 TB |  4,5 TB |  18,0 TB | 33,7 TB  | Dostępne (tylko Rev 4.2) |
| NO | SAP HANA na platformie Azure S672oom<br /> – 12 procesorów Intel® Xeon® Dosm 8276 <br /> 336 rdzeni procesora CPU i 672 wątki procesora CPU | 27,0 TB |  9,0 TB |  18,0 TB | 40,0 TB  | Dostępne (tylko Rev 4.2) |
| TAK <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1985) | SAP HANA na platformie Azure S768 m<br /> – 16 procesorów Intel® Xeon® E7-8890 v4<br /> 384 rdzenie procesora CPU i 768 wątków procesora CPU |  16,0 TB | 16,0 TB | -- | 36 TB | Dostępne |
| NO | SAP HANA na platformie Azure S768xm<br /> – 16 procesorów Intel® Xeon® E7-8890 v4<br /> 384 rdzenie procesora CPU i 768 wątków procesora CPU |  24,0 TB | 24,0 TB | --- | 56 TB | Dostępne |
|  TAK <br /> [OLAP,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2407) [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2376)  | SAP HANA na platformie Azure S896<br /> – 16 procesorów Intel® Xeon® Dosad 8276 <br /> 448 rdzeni procesora CPU i 896 wątków procesora CPU | 12,0 TB |  12,0 TB |  --- | 18,9 TB | Dostępne (tylko Wersja 4.2) |
| TAK <br /> [OLAP,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2406) [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2328) | SAP HANA na platformie Azure S896 m<br /> – 16 procesorów Intel® Xeon® Dosad 8276 <br /> 448 rdzeni procesora CPU i 896 wątków procesora CPU | 24,0 TB | 24,0 TB | -- | 35,8 TB | Dostępne |
| NO | SAP HANA na platformie Azure S896oo<br /> – 16 procesorów Intel® Xeon® Dosad 8276 <br /> 448 rdzeni procesora CPU i 896 wątków procesora CPU | 18,0 TB |  6,0 TB |  12,0 TB | 27,4 TB  | Dostępne (tylko Rev 4.2) |
| NO | SAP HANA na platformie Azure S896om<br /> – 16 procesorów Intel® Xeon® Dosm 8276 <br /> 448 rdzeni procesora CPU i 896 wątków procesora CPU | 24,0 TB |  12,0 TB |  12,0 TB | 35,8 TB  | Dostępne (tylko Rev 4.2) |
| NO | SAP HANA na platformie Azure S896ooo<br /> – 16 procesorów Intel® Xeon® Dosm 8276 <br /> 448 rdzeni procesora CPU i 896 wątków procesora CPU | 30,0 TB |  6,0 TB |  24,0 TB | 44,3 TB  | Dostępne (tylko Wersja 4.2) |
| NO | SAP HANA na platformie Azure S896oom<br /> – 16 procesorów Intel® Xeon® Dosad 8276 <br /> 448 rdzeni procesora CPU i 896 wątków procesora CPU | 36,0 TB |  12,0 TB |  24,0 TB | 52,7 TB  | Dostępne (tylko Wersja 4.2) |
| TAK <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1986) | SAP HANA na platformie Azure S960 m<br /> – 20 x procesor Intel® Xeon® E7-8890 v4<br /> 480 rdzeni procesora CPU i 960 wątków procesora CPU |  20,0 TB | 20,0 TB | -- | 46 TB | Dostępne (tylko Wersja 4.2) |


- Rdzenie procesora CPU = suma niewątkowych rdzeni procesora CPU sumy procesorów jednostki serwera.
- Wątki procesora CPU = suma wątków obliczeniowych dostarczanych przez rdzenie procesora CPU hiperwątkowego sumy procesorów jednostki serwera. Większość jednostek jest domyślnie skonfigurowana do używania technologii Hyper-Threading.
- Zgodnie z zaleceniami dostawców S768m, S768xm i S960m nie są skonfigurowane do używania Hyper-Threading do uruchamiania SAP HANA.


> [!IMPORTANT]
> Nie można już kupić następujących jednostki SKU, chociaż nadal obsługiwane: S72, S72m, S144, S144m, S192 i S192 m 

Wybrane konfiguracje zależą od obciążenia, zasobów procesora CPU i żądanej pamięci. Obciążenie OLTP może używać jednostki SKU zoptymalizowanych pod kątem obciążenia OLAP. 

Dwie różne klasy sprzętu dzielą jednostki SKU na:

- S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224 i S224m, S224oo, S224om, S224ooo, S224oom są określane jako "klasa typu I" dla jednostki SKU.
- Wszystkie inne jednostki SKU są określane jako "klasa TYPU II" dla jednostki SKU.
- Jeśli interesują Cię jednostki SKU, które nie są jeszcze wymienione w katalogu sprzętu SAP, skontaktuj się z zespołem konto Microsoft, aby uzyskać więcej informacji. 


Kompletna sygnatura dużego wystąpienia hana nie jest przydzielana wyłącznie do jednego klienta&#39;do użytku. Ten fakt dotyczy regałów zasobów obliczeniowych i magazynowych połączonych za pośrednictwem sieci szkieletowej sieci wdrożonej również na platformie Azure. Infrastruktura hana o dużym wystąpieniu, taka jak platforma Azure, wdraża różne dzierżawy klientów, które są odizolowane od siebie na &quot; &quot; następujących trzech poziomach:

- **Sieć:** izolacja za pośrednictwem sieci wirtualnych w sygnaturze dużego wystąpienia HANA.
- **Magazyn:** izolacja za pośrednictwem maszyn wirtualnych magazynu, które mają przypisane woluminy magazynu, oraz izolowanie woluminów magazynu między dzierżawami.
- **Obliczenia:** dedykowane przypisanie jednostek serwera do pojedynczej dzierżawy. Brak partycjonowania twardego lub programowego jednostek serwera. Brak udostępniania pojedynczego serwera lub jednostki hosta między dzierżawami. 

Wdrożenia jednostek dużego wystąpienia HANA między różnymi dzierżawami nie są widoczne dla siebie nawzajem. Jednostki dużego wystąpienia hana wdrożone w różnych dzierżawach nie mogą komunikować się bezpośrednio ze sobą na poziomie sygnatury dużego wystąpienia HANA. Tylko jednostki dużego wystąpienia hana w ramach jednej dzierżawy mogą komunikować się ze sobą na poziomie sygnatury dużego wystąpienia HANA.

Wdrożona dzierżawa w sygnaturze dużego wystąpienia jest przypisywana do jednej subskrypcji platformy Azure na potrzeby rozliczeń. W przypadku sieci można uzyskać do niej dostęp z sieci wirtualnych innych subskrypcji platformy Azure w ramach tej samej rejestracji platformy Azure. Jeśli wdrażasz przy użyciu innej subskrypcji platformy Azure w tym samym regionie świadczenia usługi Azure, możesz również poprosić o oddzielną dzierżawę hana o dużym wystąpieniu.

Istnieją istotne różnice między uruchamianiem aplikacji SAP HANA dużym wystąpieniu HANA i uruchamianiem SAP HANA na maszynach wirtualnych wdrożonych na platformie Azure:

- Nie ma żadnej warstwy wirtualizacji dla SAP HANA na platformie Azure (duże wystąpienia). Można uzyskać wydajność podstawowego sprzętu bez systemu metalowego.
- W przeciwieństwie do platformy Azure SAP HANA na platformie Azure (duże wystąpienia) jest przeznaczony dla określonego klienta. Nie ma możliwości, że jednostka serwera lub host jest partycjonowany na partycje twarde lub programowe. W związku z tym jednostka dużego wystąpienia HANA jest używana jako całość przypisana do dzierżawy i z tym do Ciebie. Ponowne uruchomienie lub zamknięcie serwera nie prowadzi automatycznie do systemu operacyjnego i SAP HANA na innym serwerze. (W przypadku jednostki SKU klasy I jedynym wyjątkiem jest to, że serwer napotka problemy i ponownego wykonania na innym serwerze.)
- W przeciwieństwie do platformy Azure, w której typy procesorów hosta są wybierane w celu najlepszego stosunku ceny do wydajności, typy procesorów wybrane dla usługi SAP HANA na platformie Azure (duże wystąpienia) mają najwyższą wydajność linii procesorów Intel E7v3 i E7v4.

## <a name="next-steps"></a>Następne kroki
- Zapoznaj się z [tematem HLI Sizing (Zmiany rozmiaru dla języka HLI).](hana-sizing.md)
