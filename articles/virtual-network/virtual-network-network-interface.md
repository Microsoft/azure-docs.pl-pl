---
title: Tworzenie, zmienianie lub usuwanie interfejsu sieciowego platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, czym jest interfejs sieciowy oraz jak go tworzyć, zmieniać i usuwać.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/22/2020
ms.author: kumud
ms.openlocfilehash: 8003bf14bcade08f36a7877fdb3a53998aff9e63
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773073"
---
# <a name="create-change-or-delete-a-network-interface"></a>Create, change, or delete a network interface (Tworzenie, zmienianie i usuwanie interfejsów sieciowych)

Dowiedz się, jak tworzyć, zmieniać ustawienia i usuwać interfejs sieciowy. Interfejs sieciowy umożliwia maszynie wirtualnej platformy Azure komunikowanie się z Internetem, platformą Azure i zasobami lokalnymi. Podczas tworzenia maszyny wirtualnej przy użyciu Azure Portal portal tworzy jeden interfejs sieciowy z ustawieniami domyślnymi. Zamiast tego można utworzyć interfejsy sieciowe z ustawieniami niestandardowymi i dodać do maszyny wirtualnej co najmniej jeden interfejs sieciowy podczas jego tworzenia. Można również zmienić domyślne ustawienia interfejsu sieciowego dla istniejącego interfejsu sieciowego. W tym artykule wyjaśniono, jak utworzyć interfejs sieciowy z ustawieniami niestandardowymi, zmienić istniejące ustawienia, takie jak przypisanie filtru sieci (sieciowej grupy zabezpieczeń), przypisanie podsieci, ustawienia serwera DNS i przekazywanie adresów IP oraz usunąć interfejs sieciowy.

Jeśli musisz dodać, zmienić lub usunąć adresy IP dla interfejsu sieciowego, zobacz [Zarządzanie adresami IP.](virtual-network-network-interface-addresses.md) Jeśli musisz dodać interfejsy sieciowe do usługi lub usunąć interfejsy sieciowe z maszyn wirtualnych, zobacz [Dodawanie lub usuwanie interfejsów sieciowych.](virtual-network-network-interface-vm.md)

## <a name="before-you-begin"></a>Zanim rozpoczniesz

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przed wykonaniem kroków w dowolnej sekcji tego artykułu wykonaj następujące zadania:

- Jeśli nie masz jeszcze konta platformy Azure, zarejestruj się, aby uzyskać bezpłatne [konto próbne.](https://azure.microsoft.com/free)
- Jeśli korzystasz z portalu, otwórz plik https://portal.azure.com i zaloguj się przy użyciu konta platformy Azure.
- Jeśli do wykonywania zadań w tym artykule używasz poleceń programu PowerShell, uruchom polecenia w Azure Cloud Shell [lub](https://shell.azure.com/powershell)uruchamiając program PowerShell na komputerze. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Ten samouczek wymaga Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.
- Jeśli do wykonywania zadań w tym artykule używasz poleceń interfejsu wiersza polecenia platformy Azure, uruchom polecenia w witrynie [Azure Cloud Shell](https://shell.azure.com/bash)lub uruchamiając interfejs wiersza polecenia na komputerze. Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, musisz również uruchomić narzędzie , aby `az login` utworzyć połączenie z platformą Azure.

Konto, na które się logujesz lub łączysz się [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) z platformą [](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure, musi być przypisane do roli współautora sieci lub roli niestandardowej, która ma przypisane odpowiednie akcje wymienione w Te [uprawnienia.](#permissions)

## <a name="create-a-network-interface"></a>Tworzenie interfejsu sieciowego

Podczas tworzenia maszyny wirtualnej przy użyciu Azure Portal portal tworzy interfejs sieciowy z ustawieniami domyślnymi. Jeśli wolisz określić wszystkie ustawienia interfejsu sieciowego, możesz utworzyć interfejs sieciowy z ustawieniami niestandardowymi i dołączyć interfejs sieciowy do maszyny wirtualnej podczas tworzenia maszyny wirtualnej (przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure). Możesz również utworzyć interfejs sieciowy i dodać go do istniejącej maszyny wirtualnej (przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure). Aby dowiedzieć się, jak utworzyć maszynę wirtualną z istniejącym interfejsem sieciowym lub dodać do lub usunąć interfejsy sieciowe z istniejących maszyn wirtualnych, zobacz Dodawanie lub [usuwanie interfejsów sieciowych.](virtual-network-network-interface-vm.md) Przed utworzeniem interfejsu sieciowego musisz mieć istniejącą sieć [wirtualną](manage-virtual-network.md) w tej samej lokalizacji i subskrypcji, w których tworzysz interfejs sieciowy.

1. W polu zawierającym tekst *Wyszukaj zasoby* w górnej części okna Azure Portal wpisz *interfejsy sieciowe*. Gdy **w wynikach wyszukiwania** pojawią się interfejsy sieciowe, wybierz je.
2. Wybierz **pozycję + Dodaj** w obszarze **Interfejsy sieciowe.**
3. Wprowadź lub wybierz wartości dla następujących ustawień, a następnie wybierz pozycję **Utwórz**:

    |Ustawienie|Wymagane?|Szczegóły|
    |---|---|---|
    |Nazwa|Tak|Nazwa musi być unikatowa w obrębie wybranej grupy zasobów. W czasie prawdopodobnie będziesz mieć kilka interfejsów sieciowych w ramach subskrypcji platformy Azure. Aby uzyskać sugestie podczas tworzenia konwencji nazewnictwa w celu ułatwienia zarządzania kilkoma interfejsami sieciowym, zobacz [Konwencje nazewnictwa](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming). Nazwy nie można zmienić po utworzeniu interfejsu sieciowego.|
    |Sieć wirtualna|Tak|Wybierz sieć wirtualną dla interfejsu sieciowego. Interfejs sieciowy można przypisać tylko do sieci wirtualnej, która istnieje w tej samej subskrypcji i tej samej lokalizacji co interfejs sieciowy. Po utworzeniu interfejsu sieciowego nie można zmienić przypisanej do niego sieci wirtualnej. Maszyna wirtualna, do których dodajesz interfejs sieciowy, musi również istnieć w tej samej lokalizacji i subskrypcji co interfejs sieciowy.|
    |Podsieć|Tak|Wybierz podsieć w wybranej sieci wirtualnej. Możesz zmienić podsieć, do których jest przypisany interfejs sieciowy, po jej utworzeniu.|
    |Przypisanie prywatnego adresu IP|Tak| W tym ustawieniu wybierasz metodę przypisania dla adresu IPv4. Wybierz jedną z następujących metod przypisania: **Dynamiczne:** po wybraniu tej opcji platforma Azure automatycznie przypisuje następny dostępny adres z przestrzeni adresowej wybranej podsieci. **Statyczne:** Po wybraniu tej opcji należy ręcznie przypisać dostępny adres IP z przestrzeni adresowej wybranej podsieci. Adresy statyczne i dynamiczne nie zmieniają się do momentu ich zmiany lub usunięcia interfejsu sieciowego. Metodę przypisania można zmienić po utworzeniu interfejsu sieciowego. Serwer DHCP platformy Azure przypisuje ten adres do interfejsu sieciowego w systemie operacyjnym maszyny wirtualnej.|
    |Sieciowa grupa zabezpieczeń|Nie| Pozostaw wartość **Brak,** wybierz istniejącą [sieciową grupę zabezpieczeń](./network-security-groups-overview.md)lub [utwórz sieciową grupę zabezpieczeń.](tutorial-filter-network-traffic.md) Sieciowe grupy zabezpieczeń umożliwiają filtrowanie ruchu sieciowego do i z interfejsu sieciowego. Do interfejsu sieciowego można zastosować zero lub jedną sieciową grupę zabezpieczeń. Zero lub jedną sieciową grupę zabezpieczeń można również zastosować do podsieci, do których jest przypisany interfejs sieciowy. Gdy sieciowa grupa zabezpieczeń jest stosowana do interfejsu sieciowego i podsieci, do których jest przypisany interfejs sieciowy, czasami występują nieoczekiwane wyniki. Aby rozwiązać problemy z sieciowymi grupami zabezpieczeń stosowanymi do interfejsów sieciowych i podsieci, zobacz [Rozwiązywanie problemów z sieciowymi grupami zabezpieczeń](diagnose-network-traffic-filter-problem.md).|
    |Subskrypcja|Tak|Wybierz jedną z subskrypcji [platformy](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)Azure. Maszyna wirtualna, do która jest dołączany interfejs sieciowy, oraz sieć wirtualna, z która jest podpowiąkana, musi istnieć w tej samej subskrypcji.|
    |Prywatny adres IP (IPv6)|Nie| Jeśli zaznaczysz to pole wyboru, adres IPv6 jest przypisywany do interfejsu sieciowego oprócz adresu IPv4 przypisanego do interfejsu sieciowego. Zobacz sekcję IPv6 tego artykułu, aby uzyskać ważne informacje dotyczące korzystania z protokołu IPv6 z interfejsami sieciowym. Nie można wybrać metody przypisania dla adresu IPv6. Jeśli zdecydujesz się przypisać adres IPv6, zostanie on przypisany za pomocą metody dynamicznej.
    |Nazwa IPv6 (wyświetlana tylko wtedy, gdy pole wyboru Prywatny **adres IP (IPv6)** jest zaznaczone) |Tak, jeśli **pole wyboru Prywatny adres IP (IPv6)** jest zaznaczone.| Ta nazwa jest przypisywana do dodatkowej konfiguracji adresu IP dla interfejsu sieciowego. Aby dowiedzieć się więcej na temat konfiguracji adresów IP, [zobacz Wyświetlanie ustawień interfejsu sieciowego.](#view-network-interface-settings)|
    |Grupa zasobów|Tak|Wybierz istniejącą [grupę zasobów](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) lub utwórz ją. Interfejs sieciowy może istnieć w tej samej lub innej grupie zasobów niż maszyna wirtualna, do która go dołączasz, lub sieć wirtualna, z która jest nawiązywana.|
    |Lokalizacja|Tak|Maszyna wirtualna, do której jest dołączany interfejs sieciowy, oraz [](https://azure.microsoft.com/regions)sieć wirtualna, z której jest on łączyny, muszą istnieć w tej samej lokalizacji , zwanej również regionem.|

Portal nie umożliwia przypisania publicznego adresu IP do interfejsu sieciowego podczas jego tworzenia, chociaż portal tworzy publiczny adres IP i przypisuje go do interfejsu sieciowego podczas tworzenia maszyny wirtualnej przy użyciu portalu. Aby dowiedzieć się, jak dodać publiczny adres IP do interfejsu sieciowego po jego utworzeniu, zobacz [Zarządzanie adresami IP](virtual-network-network-interface-addresses.md). Jeśli chcesz utworzyć interfejs sieciowy z publicznym adresem IP, musisz użyć interfejsu wiersza polecenia lub programu PowerShell, aby utworzyć interfejs sieciowy.

Portal nie umożliwia przypisania interfejsu sieciowego do grup zabezpieczeń aplikacji podczas tworzenia interfejsu sieciowego, ale interfejs wiersza polecenia platformy Azure i program PowerShell to robią. Istniejący interfejs sieciowy można jednak przypisać do grupy zabezpieczeń aplikacji przy użyciu portalu, o ile interfejs sieciowy jest dołączony do maszyny wirtualnej. Aby dowiedzieć się, jak przypisać interfejs sieciowy do grupy zabezpieczeń aplikacji, zobacz Dodawanie do grup zabezpieczeń aplikacji [lub ich usuwanie.](#add-to-or-remove-from-application-security-groups)

>[!Note]
> Platforma Azure przypisuje adres MAC do interfejsu sieciowego dopiero wtedy, gdy interfejs sieciowy jest dołączony do maszyny wirtualnej i maszyna wirtualna jest uruchomiona po raz pierwszy. Nie można określić adresu MAC przypisanego przez platformę Azure do interfejsu sieciowego. Adres MAC pozostaje przypisany do interfejsu sieciowego do momentu usunięcia interfejsu sieciowego lub zmiany prywatnego adresu IP przypisanego do podstawowej konfiguracji adresu IP podstawowego interfejsu sieciowego. Aby dowiedzieć się więcej o adresach IP i konfiguracjach adresów IP, zobacz [Zarządzanie adresami IP](virtual-network-network-interface-addresses.md)

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az network nic create](/cli/azure/network/nic)|
|PowerShell|[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)|

## <a name="view-network-interface-settings"></a>Wyświetlanie ustawień interfejsu sieciowego

Większość ustawień interfejsu sieciowego można wyświetlać i zmieniać po jego utworzeniu. W portalu nie jest wyświetlany sufiks DNS ani członkostwo w grupie zabezpieczeń aplikacji dla interfejsu sieciowego. Możesz użyć poleceń programu PowerShell lub interfejsu wiersza polecenia [platformy Azure,](#view-settings-commands) aby wyświetlić sufiks DNS i członkostwo w grupie zabezpieczeń aplikacji.

1. W polu zawierającym tekst *Wyszukaj* zasoby w górnej części okna Azure Portal wpisz *interfejsy sieciowe*. Gdy **interfejsy sieciowe pojawią** się w wynikach wyszukiwania, wybierz je.
2. Wybierz z listy interfejs sieciowy, dla którego chcesz wyświetlić lub zmienić ustawienia.
3. Dla wybranego interfejsu sieciowego są wyświetlane następujące elementy:
   - **Omówienie:** Zawiera informacje o interfejsie sieciowym, takie jak przypisane do niego adresy IP, sieć wirtualna/podsieć, do których jest przypisany interfejs sieciowy, oraz maszyna wirtualna, do których jest dołączony interfejs sieciowy (jeśli jest do niej dołączony). Na poniższej ilustracji przedstawiono ustawienia przeglądu interfejsu sieciowego o nazwie **mywebserver256:** ![ Omówienie interfejsu sieciowego](./media/virtual-network-network-interface/nic-overview.png)

     Interfejs sieciowy można przenieść do innej grupy zasobów lub subskrypcji,  wybierając pozycję **(zmień**) obok nazwy grupy zasobów **lub subskrypcji**. W przypadku przeniesienia interfejsu sieciowego do nowej subskrypcji należy przenieść wszystkie zasoby związane z interfejsem sieciowym. Jeśli na przykład interfejs sieciowy jest dołączony do maszyny wirtualnej, musisz również przenieść maszynę wirtualną i inne zasoby związane z maszyną wirtualną. Aby przenieść interfejs sieciowy, zobacz [Przenoszenie zasobu do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-the-portal). W tym artykule wymieniono wymagania wstępne i sposób przenoszenia zasobów przy użyciu interfejsu Azure Portal, programu PowerShell i interfejsu wiersza polecenia platformy Azure.
   - **Konfiguracje adresów IP:** W tym miejscu wymieniono publiczne i prywatne adresy IPv4 i IPv6 przypisane do konfiguracji adresów IP. Jeśli adres IPv6 jest przypisany do konfiguracji adresu IP, adres nie jest wyświetlany. Aby dowiedzieć się więcej na temat konfiguracji adresów IP oraz sposobu dodawania i usuwania adresów IP, zobacz Configure IP addresses for an Azure network interface (Konfigurowanie [adresów IP dla interfejsu sieciowego platformy Azure).](virtual-network-network-interface-addresses.md) W tej sekcji skonfigurowano również przekazywanie adresów IP i przypisywanie podsieci. Aby dowiedzieć się więcej na temat tych ustawień, zobacz [Włączanie lub wyłączanie](#enable-or-disable-ip-forwarding) przekazywania adresów IP i Zmienianie przypisania [podsieci.](#change-subnet-assignment)
   - **Serwery DNS:** Można określić, który serwer DNS jest przypisany do interfejsu sieciowego przez serwery DHCP platformy Azure. Interfejs sieciowy może dziedziczyć ustawienie z sieci wirtualnej, do których jest przypisany interfejs sieciowy, lub może mieć ustawienie niestandardowe, które przesłania ustawienie dla sieci wirtualnej, do która jest do niego przypisana. Aby zmodyfikować wyświetlane dane, zobacz [Zmienianie serwerów DNS.](#change-dns-servers)
   - **Sieciowa grupa zabezpieczeń:** Wyświetla sieciową skojarzoną z interfejsem sieciowym (jeśli istnieje). Sieciowa żadna z tych sieci zawiera reguły ruchu przychodzącego i wychodzącego do filtrowania ruchu sieciowego dla interfejsu sieciowego. Jeśli sieciowa grupy zarządzania są skojarzone z interfejsem sieciowym, wyświetlana jest nazwa skojarzonej sieciowej grupy sieciowej. Aby zmodyfikować wyświetlane informacje, zobacz [Kojarzenie lub kojarzenie sieciowej grupy zabezpieczeń.](#associate-or-dissociate-a-network-security-group)
   - **Właściwości:** Wyświetla kluczowe ustawienia dotyczące interfejsu sieciowego, w tym jego adres MAC (pusty, jeśli interfejs sieciowy nie jest dołączony do maszyny wirtualnej) i subskrypcję, w ramach których istnieje.
   - **Obowiązujące reguły zabezpieczeń:**  Reguły zabezpieczeń są wyświetlane, jeśli interfejs sieciowy jest dołączony do uruchomionej maszyny wirtualnej, a sieciowa grupy zabezpieczeń jest skojarzona z interfejsem sieciowym, podsiecią, do która jest przypisana, lub z obiema sieciami. Aby dowiedzieć się więcej na temat wyświetlanych informacji, zobacz [Wyświetlanie obowiązujących reguł zabezpieczeń.](#view-effective-security-rules) Aby dowiedzieć się więcej na temat sieciowych grup zabezpieczeń, zobacz [Sieciowe grupy zabezpieczeń](./network-security-groups-overview.md).
   - **Skuteczne trasy:** Trasy są wyświetlane, jeśli interfejs sieciowy jest dołączony do uruchomionej maszyny wirtualnej. Trasy są kombinacją tras domyślnych platformy Azure, tras zdefiniowanych przez użytkownika i wszelkich tras protokołu BGP, które mogą istnieć dla podsieci, do których jest przypisany interfejs sieciowy. Aby dowiedzieć się więcej na temat wyświetlanych informacji, zobacz [Wyświetlanie efektywnych tras](#view-effective-routes). Aby dowiedzieć się więcej o trasach domyślnych platformy Azure i trasach zdefiniowanych przez użytkownika, zobacz [Routing overview (Omówienie routingu).](virtual-networks-udr-overview.md)
Typowe Azure Resource Manager ustawień: aby dowiedzieć się więcej na temat typowych ustawień Azure Resource Manager, zobacz Dziennik [aktywności,](../azure-monitor/essentials/platform-logs-overview.md)Kontrola dostępu [(IAM),](../role-based-access-control/overview.md) [Tagi,](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)Blokady [i](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)skrypt [automatyzacji.](../azure-resource-manager/templates/export-template-portal.md)

<a name="view-settings-commands"></a>**Polecenia**

Jeśli adres IPv6 jest przypisany do interfejsu sieciowego, dane wyjściowe programu PowerShell zwracają fakt, że adres jest przypisany, ale nie zwraca przypisanego adresu. Podobnie interfejs wiersza polecenia zwraca fakt przypisania adresu, ale zwraca wartość *null* w danych wyjściowych adresu.

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az network nic list](/cli/azure/network/nic) to view network interfaces in the subscription; az network nic show to view settings for a network interface [(az network nic show,](/cli/azure/network/nic) aby wyświetlić ustawienia interfejsu sieciowego)|
|PowerShell|[Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) do wyświetlania interfejsów sieciowych w subskrypcji lub wyświetlania ustawień interfejsu sieciowego|

## <a name="change-dns-servers"></a>Zmienianie serwerów DNS

Serwer DNS jest przypisywany przez serwer DHCP platformy Azure do interfejsu sieciowego w systemie operacyjnym maszyny wirtualnej. Przypisany serwer DNS jest niezależnie od ustawienia serwera DNS dla interfejsu sieciowego. Aby dowiedzieć się więcej na temat ustawień rozpoznawania nazw dla interfejsu sieciowego, zobacz Name resolution for virtual machines (Rozpoznawania [nazw dla maszyn wirtualnych).](virtual-networks-name-resolution-for-vms-and-role-instances.md) Interfejs sieciowy może dziedziczyć ustawienia z sieci wirtualnej lub używać własnych unikatowych ustawień, które przesłaniają ustawienie dla sieci wirtualnej.

1. W polu zawierającym tekst *Wyszukaj zasoby* w górnej części okna Azure Portal wpisz *interfejsy sieciowe*. Gdy **w wynikach wyszukiwania** pojawią się interfejsy sieciowe, wybierz je.
2. Wybierz z listy interfejs sieciowy, dla którego chcesz zmienić serwer DNS.
3. Wybierz **pozycję Serwery DNS w** obszarze **USTAWIENIA.**
4. Wybierz jedną z tych opcji:
   - **Dziedzicz z sieci wirtualnej:** wybierz tę opcję, aby dziedziczyć ustawienia serwera DNS zdefiniowane dla sieci wirtualnej, do których jest przypisany interfejs sieciowy. Na poziomie sieci wirtualnej jest definiowany niestandardowy serwer DNS lub serwer DNS dostarczany przez platformę Azure. Serwer DNS dostępny na platformie Azure może rozpoznać nazwy hostów dla zasobów przypisanych do tej samej sieci wirtualnej. Do rozpoznawania zasobów przypisanych do różnych sieci wirtualnych należy użyć nazw FQDN.
   - **Niestandardowe:** możesz skonfigurować własny serwer DNS, aby rozstrzygnąć nazwy w wielu sieciach wirtualnych. Wprowadź adres IP serwera, który ma być serwerem DNS. Określony adres serwera DNS jest przypisywany tylko do tego interfejsu sieciowego i zastępuje dowolne ustawienie DNS dla sieci wirtualnej, do których jest przypisany interfejs sieciowy.
     >[!Note]
     >Jeśli maszyna wirtualna używa karty sieciowej, która jest częścią zestawu dostępności, zostaną odziedziczone wszystkie serwery DNS określone dla każdej maszyny wirtualnej ze wszystkich kart sieciowych, które są częścią zestawu dostępności.
5. Wybierz pozycję **Zapisz**.

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az network nic update](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="enable-or-disable-ip-forwarding"></a>Włączanie lub wyłączanie przekazywania adresów IP

Przekazywanie ip umożliwia maszynie wirtualnej, do których jest dołączony interfejs sieciowy:
- Odbieraj ruch sieciowy, który nie jest przeznaczony dla jednego z adresów IP przypisanych do żadnej z konfiguracji adresów IP przypisanych do interfejsu sieciowego.
- Wysyłaj ruch sieciowy z innym źródłowym adresem IP niż przypisany do jednej z konfiguracji adresów IP interfejsu sieciowego.

To ustawienie musi być włączone dla każdego interfejsu sieciowego dołączonego do maszyny wirtualnej, która odbiera ruch, który musi być przesyłany dalej przez maszynę wirtualną. Maszyna wirtualna może przesyłać dalej ruch niezależnie od tego, czy ma do niej dołączonych wiele interfejsów sieciowych, czy pojedynczy interfejs sieciowy. Przekazywanie adresów IP jest ustawieniem platformy Azure, ale maszyna wirtualna musi również uruchomić aplikację, która może przesyłać dalej ruch, taki jak zapora, optymalizacja sieci WAN i aplikacje równoważenia obciążenia. Gdy na maszynie wirtualnej są uruchomione aplikacje sieciowe, maszyna wirtualna jest często nazywana wirtualnym urządzeniem sieciowym. Możesz wyświetlić listę gotowych do wdrożenia wirtualnych urządzeń sieciowych w Azure Marketplace [.](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) Przekazywanie ip jest zwykle używane z trasami zdefiniowanymi przez użytkownika. Aby dowiedzieć się więcej o trasach zdefiniowanych przez użytkownika, zobacz [Trasy zdefiniowane przez użytkownika](virtual-networks-udr-overview.md).

1. W polu zawierającym tekst *Wyszukaj* zasoby w górnej części okna Azure Portal wpisz *interfejsy sieciowe*. Gdy **w wynikach wyszukiwania** pojawią się interfejsy sieciowe, wybierz je.
2. Wybierz interfejs sieciowy, dla którego chcesz włączyć lub wyłączyć przekazywanie adresów IP.
3. Wybierz **pozycję Konfiguracje adresów IP** w **sekcji** USTAWIENIA.
4. Wybierz **pozycję Włączone** lub **Wyłączone** (ustawienie domyślne), aby zmienić ustawienie.
5. Wybierz pozycję **Zapisz**.

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az network nic update](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="change-subnet-assignment"></a>Zmienianie przypisania podsieci

Możesz zmienić podsieć, ale nie sieć wirtualną, do których jest przypisany interfejs sieciowy.

1. W polu zawierającym tekst *Wyszukaj* zasoby w górnej części okna Azure Portal wpisz *interfejsy sieciowe*. Gdy **interfejsy sieciowe pojawią** się w wynikach wyszukiwania, wybierz je.
2. Wybierz interfejs sieciowy, dla którego chcesz zmienić przypisanie podsieci.
3. Wybierz **pozycję Konfiguracje adresów IP** w obszarze **USTAWIENIA.** Jeśli obok dowolnych prywatnych adresów  IP dla dowolnej z wymienionych konfiguracji IP znajduje się (statyczny), należy zmienić metodę przypisywania adresów IP na dynamiczną, wykonując poniższe kroki. Wszystkie prywatne adresy IP muszą być przypisane za pomocą dynamicznej metody przypisania, aby zmienić przypisanie podsieci dla interfejsu sieciowego. Jeśli adresy są przypisane za pomocą metody dynamicznej, przejdź do kroku 5. Jeśli jakiekolwiek adresy IPv4 są przypisane za pomocą metody przypisania statycznego, wykonaj następujące kroki, aby zmienić metodę przypisania na dynamiczną:
   - Z listy konfiguracji adresów IP wybierz konfigurację adresu IP, dla której chcesz zmienić metodę przypisywania adresu IPv4.
   - Wybierz **opcję Dynamiczny** jako metodę przypisania **prywatnego adresu** IP. Nie można przypisać adresu IPv6 za pomocą metody przypisania statycznego.
   - Wybierz pozycję **Zapisz**.
4. Wybierz z listy rozwijanej Podsieć  podsieć, do której chcesz przenieść interfejs sieciowy.
5. Wybierz pozycję **Zapisz**. Nowe adresy dynamiczne są przypisywane z zakresu adresów podsieci dla nowej podsieci. Po przypisaniu interfejsu sieciowego do nowej podsieci możesz przypisać statyczny adres IPv4 z nowego zakresu adresów podsieci, jeśli wybierzesz tę opcję. Aby dowiedzieć się więcej na temat dodawania, zmieniania i usuwania adresów IP dla interfejsu sieciowego, zobacz [Zarządzanie adresami IP.](virtual-network-network-interface-addresses.md)

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az network nic ip-config update](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="add-to-or-remove-from-application-security-groups"></a>Dodawanie do grup zabezpieczeń aplikacji lub ich usuwanie

Interfejs sieciowy można dodać lub usunąć z grupy zabezpieczeń aplikacji tylko wtedy, gdy interfejs sieciowy jest dołączony do maszyny wirtualnej. Możesz użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure, aby dodać interfejs sieciowy do grupy zabezpieczeń aplikacji lub usunąć interfejs sieciowy bez względu na to, czy interfejs sieciowy jest dołączony do maszyny wirtualnej, czy nie. Dowiedz się więcej [na temat grup zabezpieczeń aplikacji](./network-security-groups-overview.md#application-security-groups) i sposobu tworzenia grupy zabezpieczeń [aplikacji.](manage-network-security-group.md)

1. W polu Wyszukaj *zasoby,* usługi i dokumenty w górnej części portalu rozpocznij wpisywanie nazwy maszyny wirtualnej, która ma interfejs sieciowy, który chcesz dodać lub usunąć z grupy zabezpieczeń aplikacji. Gdy nazwa maszyny wirtualnej pojawi się w wynikach wyszukiwania, wybierz ją.
2. W obszarze **USTAWIENIA** wybierz pozycję **Sieć**.  Wybierz  pozycję Grupy zabezpieczeń aplikacji, a następnie pozycję Konfiguruj grupy zabezpieczeń aplikacji, wybierz grupy zabezpieczeń aplikacji, do których chcesz dodać interfejs sieciowy, lub usuń zaznaczenie grup zabezpieczeń aplikacji, z których chcesz usunąć interfejs sieciowy, a następnie wybierz pozycję **Zapisz**. Tylko interfejsy sieciowe, które istnieją w tej samej sieci wirtualnej, można dodać do tej samej grupy zabezpieczeń aplikacji. Grupa zabezpieczeń aplikacji musi istnieć w tej samej lokalizacji co interfejs sieciowy.

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az network nic update](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="associate-or-dissociate-a-network-security-group"></a>Kojarzenie lub kojarzenie sieciowej grupy zabezpieczeń

1. W polu wyszukiwania w górnej części portalu wprowadź w polu wyszukiwania *interfejsy* sieciowe. Gdy **w wynikach wyszukiwania** pojawią się interfejsy sieciowe, wybierz je.
2. Wybierz z listy interfejs sieciowy, z którym chcesz skojarzyć sieciową grupę zabezpieczeń, lub skojarz sieciową grupę zabezpieczeń.
3. Wybierz **pozycję Sieciowa grupa zabezpieczeń w** obszarze **USTAWIENIA.**
4. Wybierz pozycję **Edytuj**.
5. Wybierz **pozycję Sieciowa grupa zabezpieczeń,** a następnie wybierz sieciową grupę zabezpieczeń, którą chcesz skojarzyć z interfejsem sieciowym, lub wybierz pozycję **Brak,** aby usunąć skojarzenie sieciowej grupy zabezpieczeń.
6. Wybierz pozycję **Zapisz**.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [az network nic update](/cli/azure/network/nic#az_network_nic_update)
- PowerShell: [Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)

## <a name="delete-a-network-interface"></a>Usuwanie interfejsu sieciowego

Interfejs sieciowy można usunąć, o ile nie jest on dołączony do maszyny wirtualnej. Jeśli interfejs sieciowy jest dołączony do maszyny wirtualnej, należy najpierw umieścić maszynę wirtualną w stanie zatrzymanym (cofniesz jej alokację), a następnie odłączyć interfejs sieciowy od maszyny wirtualnej. Aby odłączyć interfejs sieciowy od maszyny wirtualnej, wykonaj kroki opisane w temacie Odłączanie interfejsu sieciowego [od maszyny wirtualnej.](virtual-network-network-interface-vm.md#remove-a-network-interface-from-a-vm) Nie można odłączyć interfejsu sieciowego od maszyny wirtualnej, jeśli jest to jednak jedyny interfejs sieciowy dołączony do maszyny wirtualnej. Maszyna wirtualna musi zawsze mieć dołączony co najmniej jeden interfejs sieciowy. Usunięcie maszyny wirtualnej powoduje odłączenie wszystkich dołączonych do niej interfejsów sieciowych, ale nie powoduje usunięcia interfejsów sieciowych.

1. W polu zawierającym tekst *Wyszukaj zasoby* w górnej części okna Azure Portal wpisz *interfejsy sieciowe*. Gdy **w wynikach wyszukiwania** pojawią się interfejsy sieciowe, wybierz je.
2. Wybierz z listy interfejs sieciowy, który chcesz usunąć.
3. W **obszarze Przegląd** wybierz pozycję **Usuń.**
4. Wybierz **pozycję Tak,** aby potwierdzić usunięcie interfejsu sieciowego.

Po usunięciu interfejsu sieciowego zostaną zwolnione wszystkie przypisane do niego adresy MAC lub IP.

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az network nic delete](/cli/azure/network/nic)|
|PowerShell|[Remove-AzNetworkInterface](/powershell/module/az.network/remove-aznetworkinterface)|

## <a name="resolve-connectivity-issues"></a>Rozwiązywanie problemów z łącznością

Jeśli nie możesz komunikować się z maszyną wirtualną lub z maszyny wirtualnej, przyczyną problemu mogą być reguły zabezpieczeń sieciowej grupy zabezpieczeń lub trasy obowiązujące w interfejsie sieciowym. Dostępne są następujące opcje, które mogą pomóc w rozwiązaniu problemu:

### <a name="view-effective-security-rules"></a>Wyświetlanie obowiązujących reguł zabezpieczeń

Obowiązujące reguły zabezpieczeń dla każdego interfejsu sieciowego dołączonego do maszyny wirtualnej są kombinacją reguł utworzonych w sieciowej grupie zabezpieczeń i domyślnych [reguł zabezpieczeń.](./network-security-groups-overview.md#default-security-rules) Zrozumienie obowiązujących reguł zabezpieczeń dla interfejsu sieciowego może pomóc w ustaleniu, dlaczego nie można komunikować się z maszyną wirtualną lub z maszyny wirtualnej. Można wyświetlić obowiązujące reguły dla dowolnego interfejsu sieciowego dołączonego do uruchomionej maszyny wirtualnej.

1. W polu wyszukiwania w górnej części portalu wprowadź nazwę maszyny wirtualnej, dla której chcesz wyświetlić obowiązujące reguły zabezpieczeń. Jeśli nie znasz nazwy maszyny wirtualnej, wprowadź maszyny wirtualne *w* polu wyszukiwania. Gdy **w wynikach** wyszukiwania pojawią się maszyny wirtualne, wybierz je, a następnie wybierz maszynę wirtualną z listy.
2. Wybierz **pozycję Sieć** w obszarze **USTAWIENIA.**
3. Wybierz nazwę interfejsu sieciowego.
4. Wybierz **pozycję Obowiązujące reguły zabezpieczeń w** obszarze POMOC TECHNICZNA I ROZWIĄZYWANIE **PROBLEMÓW.**
5. Przejrzyj listę obowiązujących reguł zabezpieczeń, aby określić, czy istnieją prawidłowe reguły dla wymaganej komunikacji przychodzącej i wychodzącej. Dowiedz się więcej o tym, co widzisz na liście w te [tematach Network security group overview (Omówienie sieciowej grupy zabezpieczeń).](./network-security-groups-overview.md)

Funkcja weryfikowania przepływu adresów IP usługi Azure Network Watcher może również pomóc w ustaleniu, czy reguły zabezpieczeń uniemożliwiają komunikację między maszyną wirtualną a punktem końcowym. Aby dowiedzieć się więcej, zobacz [Weryfikowanie przepływu adresów IP.](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [az network nic list-effective-nsg](/cli/azure/network/nic#az_network_nic_list_effective_nsg)
- PowerShell: [Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup)

### <a name="view-effective-routes"></a>Wyświetlanie efektywnych tras

Skuteczne trasy dla interfejsów sieciowych dołączonych do maszyny wirtualnej są kombinacją tras domyślnych, wszystkich utworzonych tras i wszelkich tras propagowanych z sieci lokalnych za pośrednictwem protokołu BGP za pośrednictwem bramy sieci wirtualnej platformy Azure. Zrozumienie efektywnych tras dla interfejsu sieciowego może pomóc w ustaleniu, dlaczego nie można komunikować się z maszyną wirtualną lub z maszyny wirtualnej. Można wyświetlić skuteczne trasy dla dowolnego interfejsu sieciowego dołączonego do uruchomionej maszyny wirtualnej.

1. W polu wyszukiwania w górnej części portalu wprowadź nazwę maszyny wirtualnej, dla której chcesz wyświetlić obowiązujące reguły zabezpieczeń. Jeśli nie znasz nazwy maszyny wirtualnej, wprowadź maszyny wirtualne *w* polu wyszukiwania. Gdy **w wynikach** wyszukiwania pojawią się maszyny wirtualne, wybierz ją, a następnie wybierz maszynę wirtualną z listy.
2. Wybierz **pozycję Sieć** w obszarze **USTAWIENIA.**
3. Wybierz nazwę interfejsu sieciowego.
4. Wybierz **pozycję Skuteczne trasy w** obszarze POMOC TECHNICZNA I ROZWIĄZYWANIE **PROBLEMÓW.**
5. Przejrzyj listę efektywnych tras, aby określić, czy istnieją prawidłowe trasy dla wymaganej komunikacji przychodzącej i wychodzącej. Dowiedz się więcej o tym, co widzisz na liście w te [tematach Routing overview (Omówienie routingu).](virtual-networks-udr-overview.md)

Funkcja następnego przeskoku usługi Azure Network Watcher może również pomóc w ustaleniu, czy trasy uniemożliwiają komunikację między maszyną wirtualną a punktem końcowym. Aby dowiedzieć się więcej, zobacz [Następny przeskok](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [az network nic show-effective-route-table](/cli/azure/network/nic#az_network_nic_show_effective_route_table)
- PowerShell: [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)

## <a name="permissions"></a>Uprawnienia

Aby można było wykonywać zadania w interfejsach sieciowych, twoje konto [](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) musi być przypisane do roli współautora sieci lub roli niestandardowej, która ma przypisane odpowiednie uprawnienia wymienione w poniższej tabeli: [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)

| Akcja                                                                     | Nazwa                                                      |
| ---------                                                                  | -------------                                             |
| Microsoft.Network/networkInterfaces/read                                   | Uzyskiwanie interfejsu sieciowego                                     |
| Microsoft.Network/networkInterfaces/write                                  | Tworzenie lub aktualizowanie interfejsu sieciowego                        |
| Microsoft.Network/networkInterfaces/join/action                            | Dołączanie interfejsu sieciowego do maszyny wirtualnej           |
| Microsoft.Network/networkInterfaces/delete                                 | Usuwanie interfejsu sieciowego                                  |
| Microsoft.Network/networkInterfaces/joinViaPrivateIp/action                | Dołączanie zasobu do interfejsu sieciowego za pośrednictwem...     |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action             | Pobierz tabelę efektywnych tras dla interfejsu sieciowego               |
| Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action  | Uzyskiwanie efektywnych grup zabezpieczeń interfejsu sieciowego           |
| Microsoft.Network/networkInterfaces/loadBalancers/read                     | Uzyskiwanie usług równoważenia obciążenia interfejsu sieciowego                      |
| Microsoft.Network/networkInterfaces/serviceAssociations/read               | Uzyskiwanie skojarzenia usługi                                   |
| Microsoft.Network/networkInterfaces/serviceAssociations/write              | Tworzenie lub aktualizowanie skojarzenia usługi                    |
| Microsoft.Network/networkInterfaces/serviceAssociations/delete             | Usuwanie skojarzenia usługi                                |
| Microsoft.Network/networkInterfaces/serviceAssociations/validate/action    | Weryfikowanie skojarzenia usługi                              |
| Microsoft.Network/networkInterfaces/ipconfigurations/read                  | Uzyskiwanie konfiguracji adresu IP interfejsu sieciowego                    |

## <a name="next-steps"></a>Następne kroki

- Tworzenie maszyny wirtualnej z wieloma karty sieciowe przy użyciu interfejsu wiersza [polecenia platformy Azure](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub programu [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Tworzenie pojedynczej maszyny wirtualnej karty sieciowej z wieloma adresami IPv4 przy użyciu interfejsu wiersza [polecenia platformy Azure](virtual-network-multiple-ip-addresses-cli.md) lub programu [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
- Tworzenie pojedynczej maszyny wirtualnej karty sieciowej z prywatnym adresem IPv6 (za Azure Load Balancer) przy użyciu interfejsu wiersza polecenia platformy [Azure,](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json)programu [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [lub Azure Resource Manager szablonu](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Tworzenie interfejsu sieciowego przy użyciu [przykładowych skryptów programu PowerShell](powershell-samples.md) lub interfejsu wiersza polecenia platformy [Azure](cli-samples.md) albo szablonu Resource Manager [Azure](template-samples.md)
- Tworzenie i [przypisywanie Azure Policy sieci](./policy-reference.md) wirtualnych