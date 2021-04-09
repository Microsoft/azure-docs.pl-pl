---
title: Grupy umieszczania zbliżeniowe platformy Azure dla aplikacji SAP | Microsoft Docs
description: Opisuje scenariusze wdrażania SAP przy użyciu grup umieszczania usługi Azure zbliżeniowe
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/29/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 924fdef475c43023c69c3006db19cd9a5aa15349
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101669667"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>Grupy umieszczania bliskości platformy Azure w celu uzyskania optymalnego opóźnienia sieci przy użyciu aplikacji SAP
Aplikacje SAP oparte na architekturze SAP NetWeaver lub SAP S/4HANA są wrażliwe na opóźnienie sieci między warstwą aplikacji SAP a warstwą bazy danych SAP. Ta czułość jest wynikiem większości logiki biznesowej działającej w warstwie aplikacji. Ze względu na to, że warstwa aplikacji SAP uruchamia logikę biznesową, wystawia zapytania do warstwy bazy danych z dużą częstotliwością, a liczba tysięcy lub dziesiątki tysięcy na sekundę. W większości przypadków charakter tych zapytań jest prosty. Często można je uruchamiać w warstwie bazy danych w 500 mikrosekundach lub mniej.

Czas spędzony na sieci do wysłania takiego zapytania z warstwy aplikacji do warstwy bazy danych i otrzymania z powrotem zestawu wyników ma znaczny wpływ na czas potrzebny na uruchomienie procesów firmy. Ta czułość na opóźnienie sieci polega na tym, że warto uzyskać pewne maksymalne opóźnienie sieci w projektach wdrożenia SAP. Zobacz [uwagi dotyczące oprogramowania SAP #1100926 — często zadawane pytania: wydajność sieci](https://launchpad.support.sap.com/#/notes/1100926/E) , aby uzyskać wskazówki dotyczące klasyfikowania opóźnienia sieci.

W wielu regionach świadczenia usługi Azure liczba centrów danych wzrosła. W tym samym czasie klienci, szczególnie w przypadku systemów SAP wysokiej klasy, korzystają z bardziej specjalnych jednostek SKU maszyn wirtualnych w rodzinie M lub Mv2 lub w dużych wystąpieniach platformy HANA. Te typy maszyn wirtualnych platformy Azure nie są zawsze dostępne we wszystkich centrach danych, które stanowią uzupełnienie regionu platformy Azure. Te fakty mogą tworzyć szanse na zoptymalizowanie opóźnień sieci między warstwą aplikacji SAP i warstwą SAP DBMS.

Aby zapewnić możliwość optymalizacji opóźnienia sieci, platforma Azure oferuje [grupy umieszczania sąsiedztwa](../../co-location.md). Grupy umieszczania w sąsiedztwie mogą służyć do wymuszania grupowania różnych typów maszyn wirtualnych w jednym centrum danych platformy Azure w celu zoptymalizowania opóźnień sieci między różnymi typami maszyn wirtualnych. W procesie wdrażania pierwszej maszyny wirtualnej w takiej grupie umieszczania w sąsiedztwie maszyna wirtualna jest powiązana z określonym centrum danych. Jako odnoszące się do tych dźwięków, użycie konstrukcji wprowadza pewne ograniczenia:

- Nie można założyć, że wszystkie typy maszyn wirtualnych platformy Azure są dostępne w każdym i wszystkich centrach danych platformy Azure. W związku z tym kombinacja różnych typów maszyn wirtualnych w jednej grupie umieszczania sąsiedztwa może być ograniczona. Te ograniczenia występują, ponieważ sprzęt hosta, który jest wymagany do uruchomienia określonego typu maszyny wirtualnej, może nie być obecny w centrum danych, w którym wdrożono grupę umieszczania
- Podczas zmiany rozmiaru części maszyn wirtualnych znajdujących się w jednej grupie umieszczania sąsiedztwa nie można automatycznie założyć, że we wszystkich przypadkach nowy typ maszyny wirtualnej jest dostępny w tym samym centrum danych co inne maszyny wirtualne, które są częścią grupy umieszczania sąsiedztwa
- Ponieważ usługa Azure recommissioning sprzęt może wymusić pewne maszyny wirtualne grupy umieszczania sąsiedztwa w innym centrum danych platformy Azure. Aby zapoznać się ze szczegółami dotyczącymi tego przypadku, zapoznaj się z dokumentem [Lokalizowanie zasobów w celu zwiększenia opóźnienia](../../co-location.md#planned-maintenance-and-proximity-placement-groups)  

> [!IMPORTANT]
> W wyniku potencjalnych ograniczeń należy użyć grup umieszczania sąsiedztwa:
>
> - Tylko w razie potrzeby
> - Tylko na poziomie szczegółowości pojedynczego systemu SAP, a nie całego systemu
> - W celu zachowania różnych typów maszyn wirtualnych i liczby maszyn wirtualnych w grupie umieszczania zbliżeniowego do minimum

Przyjęto założenie, że w przypadku wdrażania maszyn wirtualnych przez określenie Strefy dostępności i wybranie tej samej Strefy dostępności opóźnienie sieci między tymi maszynami wirtualnymi powinno być wystarczające do działania systemów SAP NetWeaver i S/4HANA w celu zapewnienia wydajności i przepływności. To założenie jest niezależne od faktu, czy określona strefa została utworzona w jednym centrum danych, czy w wielu centrach danych. Jedyną przyczyną używania grup umieszczania sąsiedztwa w ramach wdrożeń stref jest przypadek, w którym chcesz przydzielić maszyny wirtualne wdrożone w zestawie dostępności platformy Azure razem z maszynami wirtualnymi wdrożonymi w strefach.


## <a name="what-are-proximity-placement-groups"></a>Co to są grupy umieszczania zbliżeniowe? 
Grupa umieszczania bliskości platformy Azure jest konstrukcyjną logiczną. Po zdefiniowaniu grupy umieszczania bliskości jest ona powiązana z regionem świadczenia usługi Azure i grupą zasobów platformy Azure. Po wdrożeniu maszyn wirtualnych odwołuje się do niej Grupa umieszczania:

- Pierwsza maszyna wirtualna platformy Azure wdrożona w centrum danych. Pierwszą maszynę wirtualną można traktować jako "zakres maszyn wirtualnych", która jest wdrażana w centrum danych w oparciu o algorytmy alokacji platformy Azure, które ostatecznie łączą się z definicjami użytkowników dla określonej strefy dostępności.
- Wszystkie kolejne wdrożone maszyny wirtualne odwołujące się do grupy położenia zbliżeniowe, aby umieścić wszystkie następnie wdrożone maszyny wirtualne platformy Azure w tym samym centrum danych co pierwsza maszyna wirtualna.

> [!NOTE]
> Jeśli nie ma wdrożonego sprzętu hosta, który może uruchamiać określony typ maszyny wirtualnej w centrum danych, w którym została umieszczona Pierwsza maszyna wirtualna, wdrożenie żądanego typu maszyny wirtualnej nie powiedzie się. Zostanie wyświetlony komunikat o błędzie.

Jedna [Grupa zasobów platformy Azure](../../../azure-resource-manager/management/manage-resources-portal.md) może mieć wiele przypisanych grup umieszczania sąsiedztwa. Jednak grupę umieszczania sąsiedztwa można przypisać tylko do jednej grupy zasobów platformy Azure.


## <a name="proximity-placement-groups-with-sap-systems-that-use-only-azure-vms"></a>Grupy umieszczania zbliżeniowe z systemami SAP, które używają tylko maszyn wirtualnych platformy Azure
Większość wdrożeń systemu SAP NetWeaver i S/4HANA na platformie Azure nie używa [dużych wystąpień platformy Hana](./hana-overview-architecture.md). W przypadku wdrożeń, które nie korzystają z dużych wystąpień usługi HANA, ważne jest zapewnienie optymalnej wydajności między warstwą aplikacji SAP i warstwą DBMS. W tym celu należy zdefiniować grupę umieszczania bliskości platformy Azure tylko dla systemu.

W większości wdrożeń klientów klienci tworzą jedną [grupę zasobów platformy Azure](../../../azure-resource-manager/management/manage-resources-portal.md) dla systemów SAP. W takim przypadku istnieje relacja jeden do jednego między, na przykład grupa zasobów systemowej usługi ERP i jej Grupa umieszczania sąsiedztwa. W innych przypadkach klienci organizują grupy zasobów w poziomie i zbierają wszystkie systemy produkcyjne w jednej grupie zasobów. W takim przypadku istnieje relacja jeden do wielu między grupą zasobów dla produkcyjnych systemów SAP oraz kilka grup umieszczenia sąsiedztwa dla produkcji SAP ERP, SAP BW i tak dalej.

Należy unikać grupowania kilku systemów produkcyjnych lub nieprodukcyjnych SAP w pojedynczej grupie położenia sąsiedztwa. W przypadku niewielkiej liczby systemów SAP lub systemu SAP i niektórych otaczających aplikacji musi być dostępna komunikacja sieciowa o małym opóźnieniu, dlatego warto rozważyć przeniesienie tych systemów do jednej grupy umieszczania sąsiedztwa. Unikaj pakietów systemów, ponieważ więcej systemów należy grupować w grupie umieszczania bliskości, im im większa szansa:

- Wymagany jest typ maszyny wirtualnej, który nie może zostać uruchomiony w konkretnym centrum danych, do którego zakresem jest Grupa położenia sąsiedztwa.
- Zasoby nietypowej maszyny wirtualnej, takie jak maszyny wirtualne z serii M, mogą zostać ostatecznie niewykorzystane, gdy potrzeba więcej, ponieważ dodajesz oprogramowanie do grupy umieszczania sąsiedztwa w czasie.

Oto, jak opisano idealną konfigurację, która wygląda następująco:

![Grupy umieszczania zbliżeniowe z tylko maszynami wirtualnymi platformy Azure](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

W takim przypadku pojedyncze systemy SAP są pogrupowane w jednej grupie zasobów, z każdą każdą grupą umieszczenia sąsiedztwa. Nie ma zależności od tego, czy używane są konfiguracje skalowania w poziomie i w systemie DBMS.

## <a name="proximity-placement-groups-and-hana-large-instances"></a>Grupy umieszczania zbliżeniowe i duże wystąpienia HANA
Jeśli niektóre systemy SAP korzystają z [dużych wystąpień platformy Hana](./hana-overview-architecture.md) dla warstwy aplikacji, można napotkać znaczne ulepszenia opóźnienia sieci między jednostką dużej liczby wystąpień Hana i maszynami wirtualnymi platformy Azure w przypadku korzystania z jednostek usługi Hana Large Instances, które są wdrażane w [wersji 4 wierszy lub sygnaturach](./hana-network-architecture.md#networking-architecture-for-hana-large-instance). Jedno Ulepszenie polega na tym, że jednostki w dużych wystąpieniach platformy HANA są wdrażane i wdrażane przy użyciu grupy umieszczania sąsiedztwa. Aby wdrożyć maszyny wirtualne warstwy aplikacji, można użyć tej grupy umieszczania w sąsiedztwie. W związku z tym te maszyny wirtualne zostaną wdrożone w tym samym centrum danych, które obsługuje jednostkę duże wystąpienia platformy HANA.

Aby określić, czy jednostka usługi HANA Large Instances została wdrożona w sygnaturze lub wierszu poprawki 4, zapoznaj się z artykułem [Kontrola dużych wystąpień usługi Azure Hana przez Azure Portal](./hana-li-portal.md#look-at-attributes-of-single-hli-unit). W omówieniu atrybutów jednostki dużego wystąpienia usługi HANA można także określić nazwę grupy umieszczania bliskości, ponieważ została ona utworzona podczas wdrażania jednostki usługi HANA Large Instances. Nazwa, która pojawia się w przeglądzie atrybutów, to nazwa grupy położenia bliskości, w której należy wdrożyć maszyny wirtualne warstwy aplikacji.

W porównaniu z systemami SAP, w których używane są tylko usługi Azure Virtual Machines, w przypadku dużych wystąpień programu HANA należy mieć mniejszą elastyczność w wyborze liczby [grup zasobów platformy Azure](../../../azure-resource-manager/management/manage-resources-portal.md) . Wszystkie jednostki dużego wystąpienia HANA [dzierżawy dużych wystąpień programu Hana](./hana-know-terms.md) są pogrupowane w jednej grupie zasobów, zgodnie z opisem w [tym artykule](./hana-li-portal.md#display-of-hana-large-instance-units-in-the-azure-portal). O ile nie zostaną wdrożone w różnych dzierżawach w celu oddzielenia na przykład systemów produkcyjnych i nieprodukcyjnych lub innych systemów, wszystkie jednostki dużego wystąpienia platformy HANA zostaną wdrożone w dzierżawie dużej liczby wystąpień usługi HANA. Ta dzierżawa zawiera relację jeden do jednego z grupą zasobów. Jednak dla każdej pojedynczej jednostki zostanie zdefiniowana oddzielna grupa położenia sąsiedztwa.

W związku z tym relacje między grupami zasobów platformy Azure i grupami położenia bliskości dla jednej dzierżawy będą następujące:

![Grupy umieszczania zbliżeniowe i duże wystąpienia HANA](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)

## <a name="example-of-deployment-with-proximity-placement-groups"></a>Przykład wdrożenia z grupami umieszczania zbliżeniowe
Poniżej przedstawiono niektóre polecenia programu PowerShell, które służą do wdrażania maszyn wirtualnych przy użyciu grup umieszczania usługi Azure zbliżeniowe.

Pierwszym krokiem po zalogowaniu się do [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)jest sprawdzenie, czy jesteś w subskrypcji platformy Azure, której chcesz użyć do wdrożenia:

<pre><code>
Get-AzureRmContext
</code></pre>

Jeśli musisz zmienić na inną subskrypcję, możesz to zrobić, uruchamiając następujące polecenie:

<pre><code>
Set-AzureRmContext -Subscription "my PPG test subscription"
</code></pre>

Utwórz nową grupę zasobów platformy Azure, uruchamiając następujące polecenie:

<pre><code>
New-AzResourceGroup -Name "myfirstppgexercise" -Location "westus2"
</code></pre>

Utwórz nową grupę umieszczania sąsiedztwa, uruchamiając następujące polecenie:

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose" -Location "westus2"
</code></pre>

Wdróż pierwszą maszynę wirtualną w grupie położenia sąsiedztwa przy użyciu polecenia takiego jak ten:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

Poprzednie polecenie wdraża maszynę wirtualną z systemem Windows. Po pomyślnym wdrożeniu maszyny wirtualnej w regionie platformy Azure zostanie zdefiniowany zakres centrum danych grupy umieszczania sąsiedztwa. Wszystkie kolejne wdrożenia maszyn wirtualnych odwołujące się do grupy umieszczania bliskości, jak pokazano w poprzednim poleceniu, zostaną wdrożone w tym samym centrum danych platformy Azure, o ile typ maszyny wirtualnej może być hostowany na sprzęcie umieszczonym w tym centrum danych, a pojemność dla tego typu maszyny wirtualnej jest dostępna.

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>Łączenie zestawów dostępności i Strefy dostępności z grupami umieszczania zbliżeniowe
Jedną z wad korzystania z Strefy dostępności dla wdrożeń systemu SAP jest to, że nie można wdrożyć warstwy aplikacji SAP przy użyciu zestawów dostępności w ramach określonej strefy. Chcesz, aby warstwa aplikacji SAP była wdrożona w tych samych strefach co warstwa DBMS. Odwoływanie się do strefy dostępności i zestawu dostępności podczas wdrażania pojedynczej maszyny wirtualnej nie jest obsługiwane. Dlatego wcześniej wymuszono wdrożenie warstwy aplikacji przez odwołanie do strefy. Utracono możliwość upewnienia się, że maszyny wirtualne warstwy aplikacji zostały rozłożone w różnych domenach aktualizacji i niepowodzeń.

Korzystając z grup umieszczania zbliżeniowe, można pominąć to ograniczenie. Oto sekwencja wdrożenia:

- Utwórz grupę umieszczania sąsiedztwa.
- Wdróż zakotwiczenie maszyny wirtualnej, zazwyczaj serwer DBMS, odwołując się do strefy dostępności.
- Utwórz zestaw dostępności odwołujący się do grupy bliskości platformy Azure. (Zobacz polecenie w dalszej części tego artykułu).
- Wdróż maszyny wirtualne warstwy aplikacji, odwołując się do zestawu dostępności i grupy umieszczania sąsiedztwa.

Zamiast wdrażać pierwszą maszynę wirtualną, jak pokazano w poprzedniej sekcji, należy odwołać się do strefy dostępności i grupy umieszczania sąsiedztwa podczas wdrażania maszyny wirtualnej:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "letsgetclose" -Size "Standard_E16_v3"
</code></pre>

Pomyślne wdrożenie tej maszyny wirtualnej będzie hostować wystąpienie bazy danych systemu SAP w jednej strefie dostępności. Zakres grupy umieszczania bliskości jest ustalony dla jednego z centrów danych, które reprezentują zdefiniowaną strefę dostępności.

Przyjęto założenie, że maszyny wirtualne usług centralnych są wdrażane w taki sam sposób, jak maszyny wirtualne systemu DBMS, odwołujące się do tej samej strefy lub stref i tych samych grup umieszczania W następnym kroku należy utworzyć zestawy dostępności, które mają być używane dla warstwy aplikacji systemu SAP.

Zdefiniuj i Utwórz grupę umieszczania sąsiedztwa. Polecenie do tworzenia zestawu dostępności wymaga dodatkowego odwołania do identyfikatora grupy położenia zbliżeniowe (nie nazwy). Możesz uzyskać identyfikator grupy położenia zbliżeniowe przy użyciu tego polecenia:

<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose"
</code></pre>

Podczas tworzenia zestawu dostępności należy wziąć pod uwagę dodatkowe parametry w przypadku korzystania z usługi Managed disks (domyślnie, chyba że określono inaczej) i grupy umieszczania zbliżeniowe:

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "myfirstppgexercise" -Name "myppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

W idealnym przypadku należy użyć trzech domen błędów. Jednak liczba obsługiwanych domen błędów może się różnić od regionu do regionu. W takim przypadku Maksymalna liczba domen błędów możliwych dla określonych regionów wynosi 2. Aby wdrożyć maszyny wirtualne warstwy aplikacji, musisz dodać odwołanie do nazwy zestawu dostępności i nazwy grupy położenia bliskości, jak pokazano poniżej:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppgavsetappvm" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

Wynikiem tego wdrożenia jest:
- Warstwa DBMS i centralne usługi dla systemu SAP, które znajdują się w określonej strefie dostępności lub Strefy dostępności.
- Warstwa aplikacji SAP, która znajduje się za pośrednictwem zestawów dostępności w tych samych centrach danych platformy Azure, co maszyna wirtualna DBMS lub maszyny wirtualne.

> [!NOTE]
> Ze względu na to, że po wdrożeniu jednej maszyny wirtualnej DBMS w jednej strefie i drugiej maszynie wirtualnej DBMS do innej strefy w celu utworzenia konfiguracji wysokiej dostępności potrzebna jest inna grupa umieszczania bliskości dla każdej z tych stref. Ta sama wartość dotyczy wszystkich używanych zestawów dostępności.

## <a name="move-an-existing-system-into-proximity-placement-groups"></a>Przenieś istniejący system do grup umieszczania w sąsiedztwie
Jeśli masz już wdrożone systemy SAP, możesz chcieć zoptymalizować opóźnienie sieci niektórych krytycznych systemów i zlokalizować warstwę aplikacji i warstwę DBMS w tym samym centrum danych. Aby przenieść maszyny wirtualne kompletnego zestawu dostępności platformy Azure do istniejącej grupy położenia bliskości, która ma już zakres, należy zamknąć wszystkie maszyny wirtualne zestawu dostępności i przypisać zestaw dostępności do istniejącej grupy umieszczania sąsiedztwa za pośrednictwem Azure Portal, programu PowerShell lub interfejsu wiersza polecenia. Jeśli chcesz przenieść maszynę wirtualną, która nie jest częścią zestawu dostępności do istniejącej grupy położenia bliskości, wystarczy zamknąć maszynę wirtualną i przypisać ją do istniejącej grupy umieszczania sąsiedztwa. 


## <a name="next-steps"></a>Następne kroki
Zapoznaj się z dokumentacją:

- [Obciążenia SAP na platformie Azure: Lista kontrolna planowania i wdrażania](./sap-deployment-checklist.md)
- [Wersja zapoznawcza: wdrażanie maszyn wirtualnych do grup umieszczania zbliżeniowe przy użyciu interfejsu wiersza polecenia](../../linux/proximity-placement-groups.md)
- [Wersja zapoznawcza: wdrażanie maszyn wirtualnych do grup umieszczania zbliżeniowe przy użyciu programu PowerShell](../../windows/proximity-placement-groups.md)
- [Zagadnienia dotyczące wdrażania systemu Azure Virtual Machines DBMS dla obciążeń SAP](./dbms_guide_general.md)