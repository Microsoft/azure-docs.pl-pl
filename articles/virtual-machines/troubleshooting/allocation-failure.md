---
title: Rozwiązywanie problemów z błędami alokacji maszyny wirtualnej platformy Azure | Microsoft Docs
description: Rozwiązywanie problemów z błędami alokacji podczas tworzenia lub ponownego uruchamiania maszyny wirtualnej lub zmieniania jej rozmiaru na platformie Azure
services: virtual-machines
documentationcenter: ''
author: DavidCBerry13
manager: felixwu
editor: ''
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: 1ef41144-6dd6-4a56-b180-9d8b3d05eae7
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 11/06/2020
ms.author: daberry
ms.openlocfilehash: 79bc043a991404a3ee9da954b9639bf1a41f2c51
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2020
ms.locfileid: "94365877"
---
# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-vms-in-azure"></a>Rozwiązywanie problemów związanych z błędami alokacji występującymi podczas tworzenia lub ponownego uruchamiania maszyn wirtualnych na platformie Azure bądź zmieniania ich rozmiaru

Podczas tworzenia maszyny wirtualnej, ponownego uruchamiania zatrzymanej (cofniętej) maszyn wirtualnych lub zmiany rozmiaru maszyny wirtualnej Microsoft Azure przydziela zasoby obliczeniowe do subskrypcji. Firma Microsoft stale inwestuje w dodatkowe infrastruktury i funkcje, aby upewnić się, że wszystkie typy maszyn wirtualnych są dostępne do obsługi popytu klientów. Czasami mogą wystąpić błędy alokacji zasobów z powodu niespotykanego wzrostu popytu dla usług platformy Azure w określonych regionach. Ten problem może wystąpić podczas próby tworzenia lub uruchamiania maszyn wirtualnych w regionie, gdy maszyny wirtualne wyświetlają następujący kod błędu i komunikat:

**Kod błędu** : AllocationFailed lub ZonalAllocationFailed

**Komunikat o błędzie** : "Alokacja nie powiodła się. Nie ma wystarczającej pojemności dla żądanego rozmiaru maszyny wirtualnej w tym regionie. Przeczytaj więcej o tym, jak zwiększyć prawdopodobieństwo sukcesu alokacji przy użyciu protokołu https: \/ /aka.MS/Allocation-Guidance "

> [!NOTE]
> W przypadku rozwiązywania problemów z zestawem skalowania maszyn wirtualnych (VMSS) proces ten jest taki sam jak w przypadku standardowej maszyny wirtualnej. Aby rozwiązać ten problem, należy postępować zgodnie z instrukcjami w tym artykule.
> 
>**Komunikat o błędzie** : "Alokacja nie powiodła się. Jeśli próbujesz dodać nową MASZYNę wirtualną do zestawu skalowania maszyn wirtualnych przy użyciu pojedynczej grupy umieszczania lub zaktualizować/zmienić rozmiar istniejącej maszyny wirtualnej w zestawie skalowania maszyn wirtualnych za pomocą pojedynczej grupy umieszczania, należy pamiętać, że taka alokacja jest objęta zakresem pojedynczego klastra i istnieje możliwość braku możliwości klastra. Przeczytaj więcej na temat zwiększania prawdopodobieństwa sukcesu alokacji na http: \/ /aka.MS/Allocation-Guidance. "

W tym artykule wyjaśniono przyczyny niektórych typowych niepowodzeń alokacji i zaproponowano możliwe środki zaradcze.

Jeśli problem z platformą Azure nie został rozwiązany w tym artykule, odwiedź [fora platformy Azure w witrynie MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Możesz ogłosić swój problem na tych forach lub @AzureSupport w serwisie Twitter. Ponadto możesz wysłać żądanie pomocy technicznej platformy Azure, wybierając pozycję Uzyskaj pomoc techniczną w witrynie [pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) .

Dopóki preferowany typ maszyny wirtualnej nie będzie dostępny w preferowanym regionie, firma Microsoft zaleca klientom, którzy napotykają problemy z wdrożeniem, rozważą wskazówki podane w poniższej tabeli jako tymczasowe obejście tego problemu. 

Zidentyfikuj scenariusz, który najlepiej pasuje do Twojego przypadku, a następnie ponów próbę wykonania żądania alokacji, używając odpowiedniego sugerowanego obejścia, aby zwiększyć prawdopodobieństwo sukcesu alokacji. Alternatywnie możesz spróbować ponownie później. Wynika to z faktu, że wystarczająca ilość zasobów mogła zostać zwolniona w klastrze, regionie lub strefie w celu uwzględnienia Twojego żądania. 


## <a name="resize-a-vm-or-add-vms-to-an-existing-availability-set"></a>Zmiana rozmiaru maszyny wirtualnej lub dodanie maszyn wirtualnych do istniejącego zestawu dostępności

### <a name="cause"></a>Przyczyna

Żądanie zmiany rozmiaru maszyny wirtualnej lub dodanie maszyny wirtualnej do istniejącego zestawu dostępności musi być podejmowane w oryginalnym klastrze, który hostuje istniejący zestaw dostępności. Żądany rozmiar maszyny wirtualnej jest obsługiwany przez klaster, ale klaster może nie mieć obecnie wystarczającej pojemności. 

### <a name="workaround"></a>Obejście

Jeśli maszyna wirtualna może być częścią innego zestawu dostępności, Utwórz maszynę wirtualną w innym zestawie dostępności (w tym samym regionie). Nową MASZYNę wirtualną można następnie dodać do tej samej sieci wirtualnej.

Zatrzymaj (Cofnij przydział) wszystkie maszyny wirtualne w tym samym zestawie dostępności, a następnie uruchom je ponownie.
Aby zatrzymać: kliknij pozycję grupy zasobów > [Grupa zasobów] > zasobów > [Twój zestaw dostępności] > Virtual Machines > [Twoja maszyna wirtualna] > Zatrzymaj.
Po zatrzymaniu wszystkich maszyn wirtualnych wybierz pierwszą maszynę wirtualną, a następnie kliknij przycisk Uruchom.
Ten krok zapewnia, że zostanie uruchomiona nowa próba alokacji i można wybrać nowy klaster o wystarczającej pojemności.

## <a name="restart-partially-stopped-deallocated-vms"></a>Ponowne uruchomienie częściowo zatrzymanych maszyn wirtualnych (z cofniętą alokacją)

### <a name="cause"></a>Przyczyna

Częściowe cofanie alokacji oznacza, że zostało zatrzymane (cofnięto przydział) co najmniej jednej maszyny wirtualnej w zestawie dostępności. Po cofnięciu przydziału maszyny wirtualnej skojarzone zasoby zostaną wydane. Ponowne uruchamianie maszyn wirtualnych w częściowo cofniętym zestawie dostępności jest takie samo jak dodanie maszyn wirtualnych do istniejącego zestawu dostępności. W związku z tym żądanie alokacji musi być podejmowane w oryginalnym klastrze, który hostuje istniejący zestaw dostępności, który może nie mieć wystarczającej pojemności.

### <a name="workaround"></a>Obejście

Zatrzymaj (Cofnij przydział) wszystkie maszyny wirtualne w tym samym zestawie dostępności, a następnie uruchom je ponownie.
Aby zatrzymać: kliknij pozycję grupy zasobów > [Grupa zasobów] > zasobów > [Twój zestaw dostępności] > Virtual Machines > [Twoja maszyna wirtualna] > Zatrzymaj.
Po zatrzymaniu wszystkich maszyn wirtualnych wybierz pierwszą maszynę wirtualną, a następnie kliknij przycisk Uruchom.
Spowoduje to upewnienie się, że zostanie uruchomiona nowa próba alokacji i że można wybrać nowy klaster o wystarczającej pojemności.

## <a name="restart-fully-stopped-deallocated-vms"></a>Ponowne uruchomienie w pełni zatrzymanych maszyn wirtualnych (z cofniętą alokacją)

### <a name="cause"></a>Przyczyna

Całkowite cofanie alokacji oznacza, że wszystkie maszyny wirtualne w zestawie dostępności zostały zatrzymane (cofnięte alokacje). Żądanie alokacji dotyczące ponownego uruchomienia tych maszyn wirtualnych będzie ukierunkowane na wszystkie Klastry obsługujące żądany rozmiar w obrębie regionu lub strefy. Zmień żądanie alokacji zgodnie z sugestiami w tym artykule, a następnie spróbuj ponownie wykonać żądanie, aby zwiększyć prawdopodobieństwo sukcesu alokacji. 

### <a name="workaround"></a>Obejście

Jeśli używasz starszej serii lub rozmiarów maszyn wirtualnych, takich jak Dv1, DSv1, Av1, D15v2 lub DS15v2, rozważ przeniesienie do nowszych wersji. Zapoznaj się z tymi zaleceniami dotyczącymi określonych rozmiarów maszyn wirtualnych.
Jeśli nie masz opcji używania innego rozmiaru maszyny wirtualnej, spróbuj wykonać wdrożenie w innym regionie w tym samym georegionie. Aby uzyskać więcej informacji na temat dostępnych rozmiarów maszyn wirtualnych w każdym regionie w https://aka.ms/azure-regions

Jeśli używasz stref dostępności, wypróbuj inną strefę w regionie, która może mieć dostępną pojemność dla żądanego rozmiaru maszyny wirtualnej.

Jeśli żądanie alokacji jest duże (więcej niż 500 rdzeni), zapoznaj się ze wskazówkami w poniższych sekcjach, aby podzielić żądanie na mniejsze wdrożenia.

Spróbuj ponownie [wdrożyć maszynę wirtualną](./redeploy-to-new-node-windows.md). Ponowne wdrożenie maszyny wirtualnej przydziela maszynę wirtualną do nowego klastra w regionie.

## <a name="allocation-failures-for-older-vm-sizes-av1-dv1-dsv1-d15v2-ds15v2-etc"></a>Błędy alokacji dla starszych rozmiarów maszyn wirtualnych (Av1, Dv1, DSv1, D15v2, DS15v2 itp.)

Gdy rozszerzamy infrastrukturę platformy Azure, wdrażamy sprzęt nowszej generacji zaprojektowany do obsługi najnowszych typów maszyn wirtualnych. Niektóre z starszych maszyn wirtualnych serii nie są uruchamiane w naszej najnowszej infrastrukturze generacji. Z tego powodu klienci mogą czasami napotkać błędy alokacji dla tych starszych jednostek SKU. Aby uniknąć tego problemu, zachęcamy klientów korzystających z maszyn wirtualnych z starszymi seriami, aby rozważyć przechodzenie do odpowiednich nowszych maszyn wirtualnych zgodnie z poniższymi zaleceniami: te maszyny wirtualne są zoptymalizowane pod kątem najnowszego sprzętu i umożliwią korzystanie z lepszych cen i wydajności. 

|Starsza wersja maszyn wirtualnych — seria/rozmiar|Zalecana nowsza seria maszyn wirtualnych/rozmiar|Więcej informacji|
|----------------------|----------------------------|--------------------|
|Seria Av1|[Seria Av2](../av2-series.md)|https://azure.microsoft.com/blog/new-av2-series-vm-sizes/
|Seria Dv1 lub DSv1 (D1 – do + +)|[Dv3 lub DSv3 — seria](../dv3-dsv3-series.md)|https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/
|Dv1 lub DSv1 — seria (D11 do D14)|[EV3 lub ESv3 — seria](../ev3-esv3-series.md)|
|D15v2 lub DS15v2|Jeśli używasz modelu wdrażania programu theResource Manager, aby móc korzystać z większych rozmiarów maszyn wirtualnych, rozważ przechodzenie do D16v3/DS16v3 lub D32v3/DS32v3. Są one przeznaczone do uruchamiania na najnowszym sprzęcie generacji. Jeśli używasz modelu wdrażania Menedżer zasobów, aby upewnić się, że wystąpienie maszyny wirtualnej jest izolowane do sprzętu dedykowanego pojedynczemu klientowi, rozważ przejście do nowych, odizolowanych rozmiarów maszyn wirtualnych, E64i_v3 lub E64is_v3, które są przeznaczone do uruchamiania na najnowszym sprzęcie generacji. |https://azure.microsoft.com/blog/new-isolated-vm-sizes-now-available/

## <a name="allocation-failures-for-large-deployments-more-than-500-cores"></a>Błędy alokacji dla dużych wdrożeń (ponad 500 rdzeni)

Zmniejsz liczbę wystąpień żądanego rozmiaru maszyny wirtualnej, a następnie spróbuj ponownie wykonać operację wdrażania. Ponadto w przypadku większych wdrożeń warto oszacować [zestawy skalowania maszyn wirtualnych platformy Azure](../../virtual-machine-scale-sets/index.yml). Liczba wystąpień maszyn wirtualnych może być automatycznie zwiększana lub zmniejszana w odpowiedzi na zapotrzebowanie lub zdefiniowany harmonogram i istnieje większa szansa sukcesu alokacji, ponieważ wdrożenia mogą być rozłożone w wielu klastrach. 

## <a name="background-information"></a>Informacje ogólne
### <a name="how-allocation-works"></a>Jak działa alokacja
Serwery w centrach danych platformy Azure są partycjonowane na klastry. Przeważnie próba wykonania żądania alokacji jest podejmowana w wielu klastrach, ale istnieje możliwość, że niektóre ograniczenia wynikające z żądania alokacji wymuszają próbę żądania tylko w jednym klastrze platformy Azure. W tym artykule odnosimy się do "przypięty do klastra". Diagram 1 poniżej ilustruje przypadek normalnej alokacji, która została podjęta w wielu klastrach. Na diagramie 2 przedstawiono wielkość przydziału przypiętego do klastra 2, ponieważ jest to miejsce, w którym jest hostowana istniejąca usługa w chmurze CS_1 lub zestaw dostępności.
![Diagram alokacji](./media/virtual-machines-common-allocation-failure/Allocation1.png)

### <a name="why-allocation-failures-happen"></a>Dlaczego występują błędy alokacji
Gdy żądanie alokacji jest przypięte do klastra, istnieje większa szansa, że nie można znaleźć bezpłatnych zasobów, ponieważ dostępna Pula zasobów jest mniejsza. Ponadto jeśli żądanie alokacji jest przypięte do klastra, ale żądany typ zasobu nie jest obsługiwany przez ten klaster, żądanie zakończy się niepowodzeniem, nawet jeśli klaster zawiera bezpłatne zasoby. Poniższy diagram 3 ilustruje przypadek, w którym przypięta alokacja nie powiedzie się, ponieważ jedyny klaster kandydujący nie ma bezpłatnych zasobów. Diagram 4 ilustruje przypadek, w którym przypięta alokacja nie powiedzie się, ponieważ jedyny klaster kandydujący nie obsługuje żądanego rozmiaru maszyny wirtualnej, nawet jeśli klaster ma bezpłatne zasoby.

![Niepowodzenie przypiętej alokacji](./media/virtual-machines-common-allocation-failure/Allocation2.png)
