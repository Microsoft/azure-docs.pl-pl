---
title: Tworzenie, zmienianie lub usuwanie komunikacji równorzędnej sieci wirtualnej platformy Azure | Microsoft Docs
description: Tworzenie, zmienianie lub usuwanie komunikacji równorzędnej sieci wirtualnej. Za pomocą komunikacji równorzędnej sieci wirtualnych można połączyć sieci wirtualne w tym samym regionie i w różnych regionach.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: altambaw
ms.openlocfilehash: b5099f839da4547ab9551ea131fa65100d25fe65
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763047"
---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>Tworzenie, zmienianie lub usuwanie komunikacji równorzędnej sieci wirtualnej

Dowiedz się, jak tworzyć, zmieniać lub usuwać wirtualne sieci równorzędne. Komunikacja równorzędna sieci wirtualnych umożliwia łączenie sieci wirtualnych w tym samym regionie i między regionami (nazywanymi również globalnymi sieciami równorzędnymi) za pośrednictwem usługi Azure Websites. Po nawiązaniu połączenia równorzędnego sieci wirtualne są nadal zarządzane jako oddzielne zasoby. Jeśli jesteś nowym elementem komunikacji równorzędnej sieci wirtualnej, możesz dowiedzieć się więcej na ten temat w temacie [Omówienie komunikacji równorzędnej sieci wirtualnej](virtual-network-peering-overview.md) lub przez ukończenie [samouczka](tutorial-connect-virtual-networks-portal.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przed wykonaniem kroków opisanych w sekcji tego artykułu wykonaj następujące zadania:

- Jeśli nie masz jeszcze konta platformy Azure, Utwórz [konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free).
- W przypadku korzystania z portalu Otwórz https://portal.azure.com program i zaloguj się przy użyciu konta, które ma [niezbędne uprawnienia](#permissions) do pracy z komunikacjami równorzędnymi.
- W przypadku wykonywania zadań w tym artykule przy użyciu poleceń programu PowerShell uruchom polecenia w [Azure Cloud Shell](https://shell.azure.com/powershell)lub przez uruchomienie programu PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Ten samouczek wymaga modułu Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). W przypadku korzystania z programu PowerShell lokalnie należy również uruchomić `Connect-AzAccount` program przy użyciu konta, które ma [niezbędne uprawnienia](#permissions) do pracy z usługą komunikacji równorzędnej, aby utworzyć połączenie z platformą Azure.
- W przypadku korzystania z poleceń interfejsu wiersza polecenia (CLI) platformy Azure w celu wykonania zadań w tym artykule Uruchom polecenia w [Azure Cloud Shell](https://shell.azure.com/bash)lub przez uruchomienie interfejsu wiersza polecenia na komputerze. Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, musisz również uruchomić `az login` program przy użyciu konta, które ma [niezbędne uprawnienia](#permissions) do pracy z usługą komunikacji równorzędnej, aby utworzyć połączenie z platformą Azure.

Konto, do którego należy się zalogować lub połączyć się z platformą Azure za pomocą programu, musi być przypisane do roli [współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , do której przypisano odpowiednie akcje wymienione w obszarze [uprawnienia](#permissions).

## <a name="create-a-peering"></a>Tworzenie komunikacji równorzędnej

Przed utworzeniem komunikacji równorzędnej zapoznaj się z wymaganiami i ograniczeniami oraz [niezbędnymi uprawnieniami](#permissions).

1. W polu wyszukiwania w górnej części Azure Portal wprowadź *sieci wirtualne* w polu wyszukiwania. Gdy **sieci wirtualne** pojawiają się w wynikach wyszukiwania, wybierz je. Nie wybieraj **sieci wirtualnych (klasycznych)** , jeśli pojawia się na liście, ponieważ nie można utworzyć komunikacji równorzędnej z sieci wirtualnej wdrożonej za pomocą klasycznego modelu wdrażania.
2. Wybierz sieć wirtualną na liście, dla której chcesz utworzyć komunikację równorzędną.
3. W obszarze **Ustawienia** wybierz pozycję **Komunikacja równorzędna**.
4. Wybierz pozycję **+ Dodaj**. 
5. <a name="add-peering"></a>Wprowadź lub wybierz wartości dla następujących ustawień:
    - **Nazwa:** Nazwa komunikacji równorzędnej musi być unikatowa w obrębie sieci wirtualnej.
    - **Model wdrażania sieci wirtualnej:** Wybierz model wdrażania, za pomocą którego została wdrożona Sieć wirtualna, z którą chcesz nawiązać komunikację równorzędną.
    - **Wiemy o identyfikatorze zasobu:** Jeśli masz dostęp do odczytu do sieci wirtualnej, z którą chcesz nawiązać komunikację równorzędną, pozostaw to pole wyboru niezaznaczone. Jeśli nie masz dostępu do odczytu do sieci wirtualnej lub subskrypcji, z którą chcesz nawiązać komunikację równorzędną, zaznacz to pole wyboru. Wprowadź pełny identyfikator zasobu sieci wirtualnej, z którą chcesz nawiązać połączenie równorzędne w polu **Identyfikator zasobu** , które pojawiło się po zaznaczeniu pola. Wprowadzony identyfikator zasobu musi należeć do sieci wirtualnej, która istnieje w tym samym lub jest [obsługiwany w innym](#requirements-and-constraints) [regionie](https://azure.microsoft.com/regions) świadczenia usługi Azure jako tej sieci wirtualnej. Pełny identyfikator zasobu wygląda podobnie do `/subscriptions/<Id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>` . Identyfikator zasobu dla sieci wirtualnej można uzyskać, wyświetlając właściwości sieci wirtualnej. Aby dowiedzieć się, jak wyświetlić właściwości sieci wirtualnej, zobacz [Zarządzanie sieciami wirtualnymi](manage-virtual-network.md#view-virtual-networks-and-settings). Jeśli subskrypcja jest skojarzona z inną dzierżawą Azure Active Directory niż subskrypcja sieci wirtualnej, z której tworzysz komunikację równorzędną, należy najpierw dodać użytkownika z każdej dzierżawy jako [użytkownika-gościa](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) w dzierżawie przeciwległej.
    - **Subskrypcja:** Wybierz [subskrypcję](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) sieci wirtualnej, z którą chcesz nawiązać komunikację równorzędną. Zostanie wyświetlona co najmniej jedna subskrypcja, w zależności od liczby subskrypcji, do których konto ma dostęp do odczytu. Jeśli zaznaczono pole wyboru **Identyfikator zasobu** , to ustawienie nie jest dostępne.
    - **Sieć wirtualna:** Wybierz sieć wirtualną, z którą chcesz nawiązać komunikację równorzędną. Możesz wybrać sieć wirtualną utworzoną za pomocą dowolnego modelu wdrażania platformy Azure. Jeśli chcesz wybrać sieć wirtualną w innym regionie, musisz wybrać sieć wirtualną w [obsługiwanym regionie](#cross-region). Użytkownik musi mieć dostęp do odczytu do sieci wirtualnej, aby mógł być widoczny na liście. Jeśli sieć wirtualna jest wyświetlana, ale jest wyszarzona, może to być spowodowane tym, że przestrzeń adresowa sieci wirtualnej nakłada się na przestrzeń adresową tej sieci wirtualnej. Jeśli przestrzenie adresów sieci wirtualnej nakładają się na siebie, nie mogą być połączone za pomocą komunikacji równorzędnej. Jeśli zaznaczono pole wyboru **Identyfikator zasobu** , to ustawienie nie jest dostępne.
    - **Zezwalaj na dostęp do sieci wirtualnej:** Wybierz opcję **włączone** (domyślnie), jeśli chcesz włączyć komunikację między dwiema sieciami wirtualnymi za pomocą `VirtualNetwork` przepływu domyślnego. Włączenie komunikacji między sieciami wirtualnymi pozwala zasobom połączonym z siecią wirtualną komunikować się ze sobą za pomocą tej samej przepustowości i opóźnienia, tak jakby były połączone z tą samą siecią wirtualną. Cała komunikacja między zasobami w dwóch sieciach wirtualnych odbywa się za pośrednictwem sieci prywatnej platformy Azure. Tag usługi **VirtualNetwork** dla sieciowych grup zabezpieczeń obejmuje sieć wirtualną i równorzędną sieć wirtualną, gdy to ustawienie jest **włączone**. (Aby dowiedzieć się więcej na temat tagów usługi sieciowych grup zabezpieczeń, zobacz [Omówienie sieciowych grup zabezpieczeń](security-overview.md#service-tags)). Wybierz pozycję **wyłączone** , jeśli nie chcesz, aby ruch domyślnie przepływał do sieci wirtualnej równorzędnej. Możesz wybrać opcję **wyłączone** , jeśli sieć wirtualna została połączona z inną siecią wirtualną, ale czasami chcesz wyłączyć domyślny przepływ ruchu między dwiema sieciami wirtualnymi. Włączenie/wyłączenie może być wygodniejsze niż usunięcie i ponowne utworzenie komunikacji równorzędnej. Gdy to ustawienie jest wyłączone, ruch domyślnie nie przepływa między równorzędnymi sieciami wirtualnymi; Jednak ruch może nadal przepływać, jeśli jest jawnie dozwolony przez regułę [sieciowej grupy zabezpieczeń](security-overview.md) , która zawiera odpowiednie adresy IP lub grupy zabezpieczeń aplikacji.
      > [!WARNING]
      > Wyłączenie ustawienia **Zezwalaj na dostęp do sieci wirtualnej** zmienia tylko definicję tagu usługi **VirtualNetwork** . *Nie* całkowicie zapobiega przepływowi ruchu przez połączenie równorzędne, jak wyjaśniono w opisie tego ustawienia.    
    - **Zezwalaj na ruch przesłany dalej:** Zaznacz to pole wyboru, aby zezwalać na ruch *kierowany* przez sieciowe urządzenie wirtualne w sieci wirtualnej (niepochodzącej od sieci wirtualnej) do przepływu tej sieci wirtualnej za pośrednictwem komunikacji równorzędnej. Rozważmy na przykład trzy sieci wirtualne o nazwach Spoke1, Spoke2 i Hub. Komunikacja równorzędna istnieje między każdą siecią wirtualną szprych i centralną siecią wirtualną, ale Komunikacja równorzędna między sieciami wirtualnymi szprych nie istnieje. Wirtualne urządzenie sieciowe jest wdrażane w centrum sieci wirtualnej, a trasy zdefiniowane przez użytkownika są stosowane do każdej sieci wirtualnej szprych, która kieruje ruchem między podsieciami za pomocą wirtualnego urządzenia sieciowego. Jeśli to pole wyboru nie jest zaznaczone dla komunikacji równorzędnej między sieciami wirtualnymi szprych i siecią wirtualną Hub, ruch między sieciami wirtualnymi szprych nie przepływa, ponieważ koncentrator nie przekazuje ruchu między sieciami wirtualnymi. Włączenie tej funkcji umożliwia przesyłanie dalej ruchu przez komunikację równorzędną, ale nie tworzy żadnych tras zdefiniowanych przez użytkownika ani sieciowych urządzeń wirtualnych. Trasy zdefiniowane przez użytkownika i wirtualne urządzenia sieciowe są tworzone osobno. Dowiedz się więcej o [trasach zdefiniowanych przez użytkownika](virtual-networks-udr-overview.md#user-defined). Nie musisz sprawdzać tego ustawienia, jeśli ruch jest przesyłany między sieciami wirtualnymi za pomocą VPN Gateway platformy Azure.
    - **Zezwalaj na tranzyt bramy:** Zaznacz to pole, jeśli do tej sieci wirtualnej jest dołączona Brama sieci wirtualnej i chcesz zezwolić na przepływ ruchu z równorzędnej sieci wirtualnej do przepływu przez bramę. Na przykład ta sieć wirtualna może być dołączona do sieci lokalnej za pomocą bramy sieci wirtualnej. Brama może być ExpressRouteą lub bramą sieci VPN. Zaznaczenie tego pola wyboru umożliwia ruch z równorzędnej sieci wirtualnej do przepływu za pośrednictwem bramy dołączonej do tej sieci wirtualnej do sieci lokalnej. Po zaznaczeniu tego pola, równorzędna Sieć wirtualna nie może mieć skonfigurowanej bramy. W przypadku konfigurowania komunikacji równorzędnej z innej sieci wirtualnej do tej sieci wirtualnej w równorzędnej sieci wirtualnej musi być zaznaczone pole wyboru **Użyj bram zdalnych** . Jeśli to pole nie zostanie zaznaczone (domyślnie), ruch z równorzędnej sieci wirtualnej nadal przepływie do tej sieci wirtualnej, ale nie może przepływać przez bramę sieci wirtualnej dołączonej do tej sieci wirtualnej. Jeśli Komunikacja równorzędna jest między siecią wirtualną (Menedżer zasobów) i siecią wirtualną (klasyczną), Brama musi znajdować się w sieci wirtualnej (Menedżer zasobów).

       Oprócz przekazywania ruchu do sieci lokalnej, Brama sieci VPN może przekazywać ruch sieciowy między sieciami wirtualnymi łączącymi się z siecią wirtualną, w której znajduje się brama, bez konieczności komunikacji równorzędnej między sieciami wirtualnymi. Korzystanie z bramy sieci VPN do przekazywania ruchu jest przydatne, gdy chcesz użyć bramy sieci VPN w centrum (Zobacz przykład Hub i szprych opisany dla opcji **Zezwól na ruch kierowany**) sieć wirtualna do kierowania ruchu między sieciami wirtualnymi szprych, które nie są równorzędne ze sobą. Aby dowiedzieć się więcej o zezwalaniu na korzystanie z bramy do celów tranzytowych, zobacz [Konfigurowanie bramy sieci VPN do tranzytu w wirtualnej sieci równorzędnej](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ten scenariusz wymaga implementacji tras zdefiniowanych przez użytkownika, które określają bramę sieci wirtualnej jako typ następnego przeskoku. Dowiedz się więcej o [trasach zdefiniowanych przez użytkownika](virtual-networks-udr-overview.md#user-defined). Bramę sieci VPN można określić jako typ następnego przeskoku w ramach trasy zdefiniowanej przez użytkownika nie można określić bramy ExpressRoute jako typu następnego przeskoku w marszrucie zdefiniowanej przez użytkownika.

    - **Korzystanie z bram zdalnych:** Zaznacz to pole, aby zezwolić na przepływ ruchu z tej sieci wirtualnej przez bramę sieci wirtualnej dołączoną do sieci wirtualnej, z którą nawiązujesz komunikację równorzędną. Na przykład Sieć wirtualna, z którą nawiązano połączenie, ma dołączoną bramę sieci VPN, która umożliwia komunikację z siecią lokalną.  Zaznaczenie tego pola wyboru umożliwia przepływ ruchu z tej sieci wirtualnej do przepływu przez bramę sieci VPN dołączoną do sieci wirtualnej komunikacji równorzędnej. Po zaznaczeniu tego pola, równorzędna Sieć wirtualna musi mieć dołączoną bramę sieci wirtualnej i musi mieć zaznaczone pole wyboru **Zezwalaj na tranzyt bramy** . Jeśli to pole nie zostanie zaznaczone (domyślnie), ruch z równorzędnej sieci wirtualnej może nadal przepływać do tej sieci wirtualnej, ale nie może przepływać przez bramę sieci wirtualnej podłączonej do tej sieci wirtualnej.
    
      To ustawienie można włączyć tylko dla jednej komunikacji równorzędnej dla tej sieci wirtualnej.

      Nie można używać bram zdalnych, jeśli masz już skonfigurowaną bramę w sieci wirtualnej. Aby dowiedzieć się więcej o używaniu bramy do przesyłania, zobacz [Konfigurowanie bramy sieci VPN do tranzytu w wirtualnej sieci równorzędnej](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
        
    > [!NOTE]
    > Jeśli używasz bramy Virtual Network do wysyłania ruchu lokalnego przechodniego do sieci wirtualnej w sieci równorzędnej, zakres adresów IP komunikacji równorzędnej dla lokalnego urządzenia sieci VPN musi być ustawiony na "interesujący" ruch. W przeciwnym razie zasoby lokalne nie będą mogły komunikować się z zasobami w równorzędnej sieci wirtualnej.

6. Wybierz **przycisk OK** , aby dodać komunikację równorzędną do wybranej sieci wirtualnej.

Aby uzyskać instrukcje krok po kroku dotyczące implementowania komunikacji równorzędnej między sieciami wirtualnymi w różnych subskrypcjach i modelach wdrażania, zobacz [następne kroki](#next-steps).

### <a name="commands"></a>Polecenia

- **Interfejs wiersza polecenia platformy Azure**: [AZ Network VNET peering Create](/cli/azure/network/vnet/peering)
- **PowerShell**: [Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering)

## <a name="view-or-change-peering-settings"></a>Wyświetlanie lub zmiana ustawień komunikacji równorzędnej

Przed zmianą komunikacji równorzędnej zapoznaj się z wymaganiami i ograniczeniami oraz [niezbędnymi uprawnieniami](#permissions).

1. W polu wyszukiwania w górnej części portalu wprowadź *sieci wirtualne* w polu wyszukiwania. Gdy **sieci wirtualne** pojawiają się w wynikach wyszukiwania, wybierz je. Nie wybieraj **sieci wirtualnych (klasycznych)** , jeśli pojawia się na liście, ponieważ nie można utworzyć komunikacji równorzędnej z sieci wirtualnej wdrożonej za pomocą klasycznego modelu wdrażania.
2. Wybierz z listy sieć wirtualną, dla której chcesz zmienić ustawienia komunikacji równorzędnej.
3. W obszarze **Ustawienia** wybierz pozycję **Komunikacja równorzędna**.
4. Wybierz komunikację równorzędną, dla której chcesz wyświetlić lub zmienić ustawienia.
5. Zmień odpowiednie ustawienie. Przeczytaj o opcjach dla każdego ustawienia w [kroku 5](#add-peering) metody tworzenia komunikacji równorzędnej.
6. Wybierz pozycję **Zapisz**.

**Polecenia**

- **Interfejs wiersza polecenia platformy Azure**: [AZ Network VNET peering list](/cli/azure/network/vnet/peering) , aby wyświetlić komunikację równorzędną dla sieci wirtualnej, [AZ Network VNET peering show](/cli/azure/network/vnet/peering) , aby wyświetlić ustawienia dla określonej komunikacji równorzędnej i polecenie [AZ Network VNET peering Update](/cli/azure/network/vnet/peering) w celu zmiany ustawień komunikacji równorzędnej. |
- **PowerShell**: [Get-AzVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering) , aby pobrać ustawienia wyświetlania komunikacji równorzędnej i [Set-AzVirtualNetworkPeering](/powershell/module/az.network/set-azvirtualnetworkpeering) w celu zmiany ustawień.

## <a name="delete-a-peering"></a>Usuwanie komunikacji równorzędnej

Przed usunięciem komunikacji równorzędnej upewnij się, że konto ma [wymagane uprawnienia](#permissions).

Po usunięciu komunikacji równorzędnej ruch z sieci wirtualnej nie jest już przenoszony do sieci wirtualnej równorzędnej. Gdy sieci wirtualne wdrożone za pomocą Menedżer zasobów są połączone przy użyciu komunikacji równorzędnej, każda sieć wirtualna ma komunikację równorzędną z inną siecią wirtualną. Mimo że usunięcie komunikacji równorzędnej z jednej sieci wirtualnej wyłącza komunikację między sieciami wirtualnymi, nie powoduje usunięcia połączenia równorzędnego z innej sieci wirtualnej. Stan komunikacji równorzędnej dla komunikacji równorzędnej, która istnieje w innej sieci wirtualnej, jest **odłączony**. Nie można odtworzyć komunikacji równorzędnej, dopóki nie utworzysz ponownie komunikacji równorzędnej w pierwszej sieci wirtualnej, a stan komunikacji równorzędnej dla obu sieci wirtualnych zmieni się na *połączone*.

Jeśli chcesz, aby sieci wirtualne komunikują się czasami, ale nie zawsze, zamiast usuwać komunikację równorzędną, można ustawić ustawienie **Zezwalaj na dostęp do sieci wirtualnej** na wartość **wyłączone** . Aby dowiedzieć się, jak przeczytać krok 6 sekcji Tworzenie komunikacji równorzędnej w tym artykule. Wyłączenie i włączenie dostępu do sieci może być łatwiejsze niż usuwanie i odtwarzanie komunikacji równorzędnej.

1. W polu wyszukiwania w górnej części portalu wprowadź *sieci wirtualne* w polu wyszukiwania. Gdy **sieci wirtualne** pojawiają się w wynikach wyszukiwania, wybierz je. Nie wybieraj **sieci wirtualnych (klasycznych)** , jeśli pojawia się na liście, ponieważ nie można utworzyć komunikacji równorzędnej z sieci wirtualnej wdrożonej za pomocą klasycznego modelu wdrażania.
2. Wybierz sieć wirtualną na liście, dla której chcesz usunąć komunikację równorzędną.
3. W obszarze **Ustawienia** wybierz pozycję **Komunikacja równorzędna**.
4. Po prawej stronie komunikacji równorzędnej, którą chcesz usunąć, wybierz pozycję **...**, wybierz pozycję **Usuń**, a następnie wybierz pozycję **tak** , aby usunąć komunikację równorzędną z pierwszej sieci wirtualnej.
5. Wykonaj poprzednie kroki, aby usunąć komunikację równorzędną z innej sieci wirtualnej w komunikacji równorzędnej.

**Polecenia**

- **Interfejs wiersza polecenia platformy Azure**: [AZ Network VNET peering Delete](/cli/azure/network/vnet/peering)
- **PowerShell**: [Remove-AzVirtualNetworkPeering](/powershell/module/az.network/remove-azvirtualnetworkpeering)

## <a name="requirements-and-constraints"></a>Wymagania i ograniczenia

- <a name="cross-region"></a>Można równorzędne sieci wirtualne w tym samym regionie lub w różnych regionach. Komunikacja równorzędna sieci wirtualnych w różnych regionach jest również określana jako *globalna komunikacja równorzędna wirtualna*. 
- Podczas tworzenia globalnej komunikacji równorzędnej sieci wirtualne równorzędne mogą znajdować się w dowolnym regionie chmury publicznej platformy Azure lub w regionach w chmurze lub regionach w chmurze dla instytucji rządowych. Nie można nawiązać komunikacji równorzędnej między chmurami. Na przykład Sieć wirtualna w chmurze publicznej platformy Azure nie może być połączona z siecią wirtualną w chmurze platformy Azure w Chinach.
- Zasoby w jednej sieci wirtualnej nie mogą komunikować się z adresem IP frontonu wewnętrznego modułu równoważenia obciążenia w warstwie Podstawowa w sieci wirtualnej z globalną komunikacją równorzędną. Pomoc techniczna dla usługi Load Balancer w warstwie Podstawowa jest dostępna tylko w obrębie tego samego regionu. Pomoc techniczna dla usługi Load Balancer w warstwie Standardowa jest dostępna dla wirtualnych sieci równorzędnych i globalnych wirtualnych sieci równorzędnych. Usługi korzystające z podstawowego modułu równoważenia obciążenia, które nie będą działały w ramach globalnej komunikacji równorzędnej sieci wirtualnej, są udokumentowane w [tym miejscu.](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)
- Bram zdalnych można używać lub zezwalać na tranzyt bramy w wirtualnych sieciach równorzędnych i lokalnie połączonych równorzędnych sieciach wirtualnych.
- Sieci wirtualne mogą znajdować się w tych samych lub różnych subskrypcjach. W przypadku równorzędnych sieci wirtualnych w różnych subskrypcjach obie subskrypcje mogą być skojarzone z tą samą lub inną dzierżawą Azure Active Directory. Jeśli nie masz jeszcze dzierżawy usługi AD, możesz ją [utworzyć](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json-a-new-azure-ad-tenant).
- Równorzędne sieci wirtualne muszą mieć nienakładające się przestrzenie adresów IP.
- Nie można dodać zakresów adresów do lub usunąć zakresów adresów z przestrzeni adresowej sieci wirtualnej, gdy sieć wirtualna jest połączona z inną siecią wirtualną. Aby dodać lub usunąć zakresy adresów, Usuń komunikację równorzędną, Dodaj lub Usuń zakresy adresów, a następnie ponownie utwórz komunikację równorzędną. Aby dodać zakresy adresów do lub usunąć zakresy adresów z sieci wirtualnych, zobacz [Zarządzanie sieciami wirtualnymi](manage-virtual-network.md).
- Dwie sieci wirtualne wdrożone za pomocą Menedżer zasobów lub sieci wirtualnej wdrożone za pomocą Menedżer zasobów z siecią wirtualną wdrożoną za pomocą klasycznego modelu wdrażania. Nie można połączyć komunikacji równorzędnej dwóch sieci wirtualnych utworzonych za pomocą klasycznego modelu wdrażania. Jeśli nie znasz modeli wdrażania platformy Azure, zapoznaj się z artykułem [Omówienie modeli wdrażania platformy Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . Do połączenia dwóch sieci wirtualnych utworzonych za pomocą klasycznego modelu wdrażania można użyć usługi [VPN Gateway](../vpn-gateway/design.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V).
- W przypadku łączenia dwóch sieci wirtualnych utworzonych w usłudze Resource Manager za pomocą komunikacji równorzędnej należy skonfigurować komunikację równorzędną dla każdej objętej nią sieci wirtualnej. Zostanie wyświetlony jeden z następujących typów stanu komunikacji równorzędnej: 
  - *Zainicjowane:* Podczas tworzenia komunikacji równorzędnej z drugą siecią wirtualną z pierwszej sieci wirtualnej stan komunikacji równorzędnej zostanie *zainicjowany*. 
  - *Połączono:* Po utworzeniu komunikacji równorzędnej z drugiej sieci wirtualnej do pierwszej sieci wirtualnej jej stan komunikacji równorzędnej jest *połączony*. Jeśli zobaczysz stan komunikacji równorzędnej dla pierwszej sieci wirtualnej, zobaczysz, że jego stan został zmieniony z *zainicjowane* na *połączone*. Komunikacja równorzędna nie zostanie pomyślnie nawiązana, dopóki nie zostanie *podłączony* stan komunikacji równorzędnej dla obu wirtualnych sieci równorzędnych.
- W przypadku komunikacji równorzędnej z siecią wirtualną utworzoną za pomocą Menedżer zasobów z siecią wirtualną utworzoną za pomocą klasycznego modelu wdrażania można skonfigurować komunikację równorzędną dla sieci wirtualnej wdrożonej za pomocą usługi Menedżer zasobów. Nie można skonfigurować komunikacji równorzędnej dla sieci wirtualnej (klasycznej) ani między dwiema sieciami wirtualnymi wdrożonymi przy użyciu klasycznego modelu wdrażania. Podczas tworzenia komunikacji równorzędnej z sieci wirtualnej (Menedżer zasobów) w sieci wirtualnej (klasycznej) Stan komunikacji równorzędnej jest *aktualizowany*, a następnie wkrótce *zmienia się.*
- Połączenie komunikacji równorzędnej między dwiema sieciami wirtualnymi. Komunikację równorzędną przez siebie nie są przechodnie. W przypadku tworzenia komunikacji równorzędnej między:
  - VirtualNetwork1 & VirtualNetwork2-VirtualNetwork1 & VirtualNetwork2
  - VirtualNetwork2 & VirtualNetwork3-VirtualNetwork2 & VirtualNetwork3


  Nie ma komunikacji równorzędnej między VirtualNetwork1 i VirtualNetwork3 przez VirtualNetwork2. Jeśli chcesz utworzyć wirtualną sieć równorzędną między VirtualNetwork1 i VirtualNetwork3, musisz utworzyć komunikację równorzędną między VirtualNetwork1 i VirtualNetwork3. Nie ma komunikacji równorzędnej między VirtualNetwork1 i VirtualNetwork3 przez VirtualNetwork2. Jeśli chcesz, aby VirtualNetwork1 i VirtualNetwork3 się bezpośrednio komunikować, musisz utworzyć jawną komunikację równorzędną między VirtualNetwork1 i VirtualNetwork3 lub przejść przez urządzenie WUS w sieci centrum.  
- Nie można rozpoznać nazw w wirtualnych sieciach równorzędnych przy użyciu domyślnego rozpoznawania nazw platformy Azure. Aby rozwiązać nazwy w innych sieciach wirtualnych, należy użyć [Azure DNS w przypadku domen prywatnych](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub NIESTANDARDOWEGO serwera DNS. Aby dowiedzieć się, jak skonfigurować własny serwer DNS, zobacz [rozpoznawanie nazw przy użyciu własnego serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
- Zasoby w równorzędnych sieciach wirtualnych w tym samym regionie mogą komunikować się ze sobą za pomocą tej samej przepustowości i opóźnienia, tak jakby znajdowały się w tej samej sieci wirtualnej. Rozmiar każdej maszyny wirtualnej ma jednak własną maksymalną przepustowość sieci. Aby dowiedzieć się więcej o maksymalnej przepustowości sieci dla różnych rozmiarów maszyn wirtualnych, zobacz rozmiary maszyn wirtualnych z [systemem Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) .
- Sieć wirtualna może być połączona za pomocą komunikacji równorzędnej z inną siecią wirtualną, a także mieć połączenie z inną siecią wirtualną przy użyciu bramy sieci wirtualnej platformy Azure. Gdy sieci wirtualne są połączone za pomocą komunikacji równorzędnej i bramy, ruch między sieciami wirtualnymi przechodzi przez konfigurację komunikacji równorzędnej, a nie z bramą.
- Klientów sieci VPN typu punkt-lokacja należy pobrać ponownie po pomyślnym skonfigurowaniu komunikacji równorzędnej sieci wirtualnej, aby upewnić się, że nowe trasy zostaną pobrane do klienta.
- Istnieje nominalna opłata za ruch przychodzący i wychodzący w wirtualnych sieciach równorzędnych. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="permissions"></a>Uprawnienia

Konta używane do pracy z usługą komunikacji równorzędnej sieci wirtualnej muszą być przypisane do następujących ról:

- [Współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor): dla sieci wirtualnej wdrożonej za pomocą Menedżer zasobów.
- [Współautor klasycznej sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor): dla sieci wirtualnej wdrożonej za pomocą klasycznego modelu wdrażania.

Jeśli Twoje konto nie jest przypisane do jednej z poprzednich ról, musi być przypisane do [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , do której przypisano wymagane akcje z poniższej tabeli:

| Akcja                                                          | Nazwa |
|---                                                              |---   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write  | Wymagane do utworzenia komunikacji równorzędnej z sieci wirtualnej A do sieci wirtualnej B. Sieć wirtualna A musi być siecią wirtualną (Menedżer zasobów)          |
| Microsoft. Network/virtualNetworks/peer/Action                   | Wymagane do utworzenia komunikacji równorzędnej z sieci wirtualnej B (Menedżer zasobów) do sieci wirtualnej A                                                       |
| Microsoft. ClassicNetwork/virtualNetworks/peer/Action                   | Wymagane do utworzenia komunikacji równorzędnej z sieci wirtualnej B (klasycznej) do sieci wirtualnej A                                                                |
| Microsoft. Network/virtualNetworks/virtualNetworkPeerings/odczyt   | Odczytaj komunikację równorzędną sieci wirtualnej   |
| Microsoft. Network/virtualNetworks/virtualNetworkPeerings/Delete | Usuwanie komunikacji równorzędnej sieci wirtualnej |

## <a name="next-steps"></a>Następne kroki

- Równorzędne sieci wirtualne tworzy się między sieciami wirtualnymi utworzonymi za pomocą tych samych lub różnych modeli wdrażania istniejących w tej samej lub w różnych subskrypcjach. Ukończ samouczek dla jednego z następujących scenariuszy:

  |Model wdrażania platformy Azure             | Subskrypcja  |
  |---------                          |---------|
  |Resource Manager — w obu przypadkach              |[Ta sama](tutorial-connect-virtual-networks-portal.md)|
  |                                   |[Różne](create-peering-different-subscriptions.md)|
  |Jedna sieć — Resource Manager, druga — model klasyczny  |[Ta sama](create-peering-different-deployment-models.md)|
  |                                   |[Różne](create-peering-different-deployment-models-subscriptions.md)|

- Dowiedz się jak, utworzyć [topologię sieciową typu gwiazda](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Tworzenie komunikacji równorzędnej sieci wirtualnej przy użyciu [programu PowerShell](powershell-samples.md) lub przykładowych skryptów [interfejsu wiersza polecenia platformy Azure](cli-samples.md) lub korzystanie z szablonów usługi Azure [Menedżer zasobów](template-samples.md)
- Tworzenie i przypisywanie [definicji Azure Policy](policy-samples.md) dla sieci wirtualnych
