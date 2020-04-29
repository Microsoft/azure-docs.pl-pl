---
title: Jednostki SKU dla SAP HANA na platformie Azure (duże wystąpienia) | Microsoft Docs
description: Jednostki SKU dla SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aad4e3ff3df8b4aeecbbbee7883ba383b9fd0d9c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77617261"
---
# <a name="available-skus-for-hli"></a>Dostępne jednostki SKU dla oprogramowania SAP HANA na platformie Azure — duże wystąpienia

Usługa SAP HANA na platformie Azure (duże wystąpienia) oparta na sygnaturach poprawek 3 jest dostępna w kilku konfiguracjach w regionach świadczenia usługi Azure:

- Zachodnie stany USA
- Wschodnie stany USA
- Australia Wschodnia
- Australia Południowo-Wschodnia
- Europa Zachodnia
- Europa Północna
- Japonia Wschodnia
- Japonia Zachodnia

Usługa SAP HANA na platformie Azure (duże wystąpienia) oparta na sygnaturach poprawek 4 jest dostępna w kilku konfiguracjach w regionach świadczenia usługi Azure:

- Zachodnie stany USA 2
- Wschodnie stany USA
- Południowo-środkowe stany USA
- Europa Zachodnia
- Europa Północna



[SAP HANA certyfikowane jednostki SKU z listy dużych wystąpień Hana,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) takie jak:

| Rozwiązanie SAP | Procesor CPU | Memory (Pamięć) | Magazyn | Dostępność |
| --- | --- | --- | --- | --- |
| Optymalizacja pod kątem OLAP: SAP BW, BW/4HANA<br /> lub SAP HANA dla ogólnego obciążenia OLAP | SAP HANA na platformie Azure S72<br /> — 2 x procesor Intel® Xeon® E7-8890 v3<br /> 36 rdzeni procesora CPU i 72 wątki procesora |  768 GB |  3 TB | Nie jest już oferowane |
| --- | SAP HANA na platformie Azure S144<br /> — 4 x procesor Intel® Xeon® E7-8890 v3<br /> 72 rdzeni procesora CPU i 144 wątki procesora |  1,5 TB |  6 TB | Nie jest już oferowane |
| --- | SAP HANA na platformie Azure S192<br /> — 4 x procesor Intel® Xeon® E7-8890 v4<br /> 96 rdzeni procesora CPU i 192 wątki procesora |  2,0 TB |  8 TB | Nie jest już oferowane |
| --- | SAP HANA na platformie Azure S224<br /> — 4 x procesor Intel® Xeon® Platinum 8276 (znany również jako kaskada Lake)<br /> 112 rdzeni procesora CPU i 224 wątki procesora |  3,0 TB |  6,3 TB | Dostępne w sygnaturach Revision3 i Revision4  |
| --- | SAP HANA na platformie Azure S384<br /> – 8 x procesor Intel® Xeon® E7-8890 v4<br /> 192 rdzeni procesora CPU i 384 wątki procesora |  4,0 TB |  16 TB | Dostępne w sygnaturach Revision4 |
| Optymalizacja pod kątem OLTP: SAP Business Suite<br /> na SAP HANA lub S/4HANA (OLTP),<br /> ogólne OLTP | SAP HANA na platformie Azure S72m<br /> — 2 x procesor Intel® Xeon® E7-8890 v3<br /> 36 rdzeni procesora CPU i 72 wątki procesora |  1,5 TB |  6 TB | Nie jest już oferowane |
|---| SAP HANA na platformie Azure S144m<br /> — 4 x procesor Intel® Xeon® E7-8890 v3<br /> 72 rdzeni procesora CPU i 144 wątki procesora |  3,0 TB |  12 TB | Nie jest już oferowane |
|---| SAP HANA na platformie Azure S192m<br /> — 4 x procesor Intel® Xeon® E7-8890 v4<br /> 96 rdzeni procesora CPU i 192 wątki procesora  |  4,0 TB |  16 TB | Nie jest już oferowane |
| --- | SAP HANA na platformie Azure S224m<br /> — 4 x procesor Intel® Xeon® Platinum 8276 (znany również jako kaskada Lake)<br /> 112 rdzeni procesora CPU i 224 wątki procesora |  6,0 TB |  10,5 TB | Dostępne w sygnaturach Revision3 i Revision4  |
|---| SAP HANA na platformie Azure S384m<br /> – 8 x procesor Intel® Xeon® E7-8890 v4<br /> 192 rdzeni procesora CPU i 384 wątki procesora |  6,0 TB |  18 TB | Dostępne w sygnaturach Revision4|
|---| SAP HANA na platformie Azure S384xm<br /> – 8 x procesor Intel® Xeon® E7-8890 v4<br /> 192 rdzeni procesora CPU i 384 wątki procesora |  8,0 TB |  22 TB |  Dostępne w sygnaturach Revision4 |
|---| SAP HANA na platformie Azure S576m<br /> – 12 x procesor Intel® Xeon® E7-8890 v4<br /> 288 rdzeni procesora CPU i 576 wątki procesora |  12,0 TB |  28 TB | Dostępne w sygnaturach Revision4|
|---| SAP HANA na platformie Azure S768m<br /> – 16 x procesor Intel® Xeon® E7-8890 v4<br /> 384 rdzeni procesora CPU i 768 wątki procesora |  16,0 TB |  36 TB | Dostępne w sygnaturach Revision4|
|---| SAP HANA na platformie Azure S960m<br /> – 20 x procesor Intel® Xeon® E7-8890 v4<br /> 480 rdzeni procesora CPU i 960 wątki procesora |  20,0 TB |  46 TB | Dostępne w sygnaturach Revision4|


W obszarze SAP HANA TDIv5, SAP pozwala na ustalanie wielkości charakterystycznej dla klienta i projektów specyficznych dla klienta, co może prowadzić do konfiguracji serwera, które nie są wymienione jako certyfikowane w programie:

- [SAP HANA certyfikowane urządzenia](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/appliances.html)
- [SAP HANA certyfikowane platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)

W wielu przypadkach te konfiguracje serwera specyficzne dla klienta zawierają więcej pamięci niż jednostki serwera certyfikowane za pomocą SAP. W pracy z oprogramowaniem SAP klienci mogą uzyskać pomoc techniczną SAP i poświadczać o ich konfiguracjach specyficznych dla klientów. Na platformie Azure dostępne są następujące jednostki SKU w warstwie Standardowa platformy HANA, a na liście cenowej firmy Microsoft można znaleźć takie TDIv5 projekty ustalające rozmiar dla klientów.

| SKU|Procesor CPU | Memory (Pamięć) | Magazyn | Dostępność |
| ---| --- | --- | --- | --- |
| S96 | SAP HANA na platformie Azure s96<br /> — 2 x procesor Intel® Xeon® E7-8890 v4<br /> 48 rdzeni procesora CPU i 96 wątki procesora |  768 GB |  3 TB | Dostępne w sygnaturach Revision3 i Revision4|


| Oryginalna jednostka SKU, która może być <br /> rozszerzone w pamięci | Procesor CPU | Memory (Pamięć) | Magazyn | Dostępność |
| --- | --- | --- | --- | --- |
| S192m można rozszerzyć na | SAP HANA na platformie Azure S192xm<br /> — 4 x procesor Intel® Xeon® E7-8890 v4<br /> 96 rdzeni procesora CPU i 192 wątki procesora |  6,0 TB |  16 TB | Nie jest już oferowane |
| S384xm można rozszerzyć na | SAP HANA na platformie Azure S384xxm<br /> – 8 x procesor Intel® Xeon® E7-8890 v4<br /> 192 rdzeni procesora CPU i 384 wątki procesora |  12,0 TB |  28 TB | Dostępne w sygnaturach Revision4 |
| S576m można rozszerzyć na | SAP HANA na platformie Azure S576xm<br /> – 12 x procesor Intel® Xeon® E7-8890 v4<br /> 288 rdzeni procesora CPU i 576 wątki procesora |  18,0 TB |  41 TB | Dostępne w sygnaturach Revision4|
| S768m można rozszerzyć na | SAP HANA na platformie Azure S768xm<br /> – 16 x procesor Intel® Xeon® E7-8890 v4<br /> 384 rdzeni procesora CPU i 768 wątki procesora |  24,0 TB |  56 TB | Dostępne w sygnaturach Revision4 |

- Rdzenie procesora = suma rdzeni procesora CPU, które nie są oparte na funkcji Hyper-Thread, sumy procesorów jednostki serwera.
- Wątki procesora = suma wątków obliczeniowych dostarczanych przez rdzenie procesora CPU przez wątki z sumą procesorów jednostki serwera. Większość jednostek jest domyślnie skonfigurowanych do korzystania z technologii Hyper-Threading.
- Na podstawie zaleceń dotyczących dostawcy S768m, S768xm i S960m nie są skonfigurowane do korzystania z funkcji Hyper-Threading do uruchamiania SAP HANA.


Wybrane konfiguracje są zależne od obciążenia, zasobów procesora CPU i żądanej pamięci. Istnieje możliwość, że obciążenie OLTP wykorzystuje jednostki SKU zoptymalizowane pod kątem obciążenia OLAP. 

Baza sprzętu dla ofert, z wyjątkiem jednostek dla projektów ustalających rozmiar dla klienta, jest SAP HANA certyfikat TDI. Dwie różne klasy sprzętu dzielą jednostki SKU na:

- S72, S72m, s96, S144, S144m, S192, S192m, S192xm, S224 i S224m, które są określane jako "Klasa I" klasy jednostek SKU.
- S384, S384m, S384xm, S384xxm, S576m, S576xm S768m, S768xm i S960m, które są określane jako "Typ II klasy" jednostek SKU.
- Jeśli interesuje Cię inne S224e jednostki SKU oferowane od 4.5 TB do 9TB z Optane, skontaktuj się z zespołem konto Microsoft, aby uzyskać więcej informacji. 


Pełna sygnatura dużego wystąpienia HANA nie jest przeznaczona wyłącznie dla jednego klienta&#39;s. Ten fakt dotyczy stojaków zasobów obliczeniowych i magazynu połączonych przez sieć szkieletową sieci wdrożoną na platformie Azure. Infrastruktura dużej instancji HANA, &quot;&quot; taka jak Azure, wdraża różne dzierżawy klientów, które są odizolowane od siebie na następujących trzech poziomach:

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
