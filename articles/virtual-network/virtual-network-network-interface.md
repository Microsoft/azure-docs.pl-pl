---
title: Tworzenie, zmienianie lub usuwanie interfejsu sieciowego platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, co to jest interfejs sieciowy i jak utworzyć, zmienić ustawienia dla i usunąć je.
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
ms.openlocfilehash: 99905e58cbcd9d0a5c5397aee125675a70e799fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89657962"
---
# <a name="create-change-or-delete-a-network-interface"></a>Create, change, or delete a network interface (Tworzenie, zmienianie i usuwanie interfejsów sieciowych)

Dowiedz się, jak tworzyć, zmieniać ustawienia i usuwać interfejs sieciowy. Interfejs sieciowy umożliwia maszynie wirtualnej platformy Azure komunikowanie się z Internetem, platformą Azure i zasobami lokalnymi. Podczas tworzenia maszyny wirtualnej przy użyciu Azure Portal Portal tworzy jeden interfejs sieciowy z ustawieniami domyślnymi. Zamiast tego można utworzyć interfejsy sieciowe z ustawieniami niestandardowymi i dodać do maszyny wirtualnej co najmniej jeden interfejs sieciowy podczas jego tworzenia. Możesz również zmienić ustawienia domyślne interfejsu sieciowego dla istniejącego interfejsu sieciowego. W tym artykule wyjaśniono, jak utworzyć interfejs sieciowy z ustawieniami niestandardowymi, zmienić istniejące ustawienia, takie jak filtr sieci (Grupa zabezpieczeń sieci), przypisywanie podsieci, ustawienia serwera DNS i przekazywanie adresów IP oraz usuwanie interfejsu sieciowego.

Jeśli musisz dodać, zmienić lub usunąć adresy IP dla interfejsu sieciowego, zobacz [Zarządzanie adresami IP](virtual-network-network-interface-addresses.md). Jeśli musisz dodać interfejsy sieciowe do lub usunąć interfejsy sieciowe z maszyn wirtualnych, zobacz [Dodawanie lub usuwanie interfejsów sieciowych](virtual-network-network-interface-vm.md).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przed wykonaniem kroków opisanych w sekcji tego artykułu wykonaj następujące zadania:

- Jeśli nie masz jeszcze konta platformy Azure, Utwórz [konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free).
- Jeśli używasz portalu, Otwórz https://portal.azure.com program i zaloguj się przy użyciu konta platformy Azure.
- W przypadku wykonywania zadań w tym artykule przy użyciu poleceń programu PowerShell uruchom polecenia w [Azure Cloud Shell](https://shell.azure.com/powershell)lub przez uruchomienie programu PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Ten samouczek wymaga modułu Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.
- W przypadku korzystania z poleceń interfejsu wiersza polecenia (CLI) platformy Azure w celu wykonania zadań w tym artykule Uruchom polecenia w [Azure Cloud Shell](https://shell.azure.com/bash)lub przez uruchomienie interfejsu wiersza polecenia na komputerze. Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, musisz też uruchomić polecenie `az login` , aby utworzyć połączenie z platformą Azure.

Konto, do którego należy się zalogować lub połączyć się z platformą Azure za pomocą programu, musi być przypisane do roli [współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , do której przypisano odpowiednie akcje wymienione w obszarze [uprawnienia](#permissions).

## <a name="create-a-network-interface"></a>Tworzenie interfejsu sieciowego

Podczas tworzenia maszyny wirtualnej przy użyciu Azure Portal Portal tworzy interfejs sieciowy z ustawieniami domyślnymi. Jeśli nie określisz wszystkich ustawień interfejsu sieciowego, możesz utworzyć interfejs sieciowy z ustawieniami niestandardowymi i dołączyć interfejs sieciowy do maszyny wirtualnej podczas tworzenia maszyny wirtualnej (przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure). Możesz również utworzyć interfejs sieciowy i dodać go do istniejącej maszyny wirtualnej (przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure). Aby dowiedzieć się, jak utworzyć maszynę wirtualną z istniejącym interfejsem sieciowym lub dodać lub usunąć interfejsy sieciowe z istniejących maszyn wirtualnych, zobacz [Dodawanie lub usuwanie interfejsów sieciowych](virtual-network-network-interface-vm.md). Przed utworzeniem interfejsu sieciowego należy mieć istniejącą [sieć wirtualną](manage-virtual-network.md) w tej samej lokalizacji i subskrypcji, w której jest tworzony interfejs sieciowy.

1. W polu zawierającym *zasoby wyszukiwania* tekstu w górnej części Azure Portal wpisz *interfejsy sieciowe*. Gdy **interfejsy sieciowe** pojawiają się w wynikach wyszukiwania, wybierz je.
2. Wybierz pozycję **+ Dodaj** w obszarze **interfejsy sieciowe**.
3. Wprowadź lub wybierz wartości dla następujących ustawień, a następnie wybierz pozycję **Utwórz**:

    |Ustawienie|Wymagane?|Szczegóły|
    |---|---|---|
    |Nazwa|Tak|Nazwa musi być unikatowa w ramach wybranej grupy zasobów. W miarę upływu czasu prawdopodobnie masz kilka interfejsów sieciowych w ramach subskrypcji platformy Azure. Aby uzyskać sugestie dotyczące tworzenia konwencji nazewnictwa w celu ułatwienia zarządzania kilkoma interfejsami sieciowymi, zobacz [konwencje nazewnictwa](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming). Nie można zmienić nazwy po utworzeniu interfejsu sieciowego.|
    |Sieć wirtualna|Tak|Wybierz sieć wirtualną dla interfejsu sieciowego. Interfejs sieciowy można przypisać tylko do sieci wirtualnej, która istnieje w tej samej subskrypcji i lokalizacji co interfejs sieciowy. Po utworzeniu interfejsu sieciowego nie można zmienić sieci wirtualnej, do której jest przypisana. Maszyna wirtualna, do której należy dodać interfejs sieciowy, również istnieje w tej samej lokalizacji i subskrypcji co interfejs sieciowy.|
    |Podsieć|Tak|Wybierz podsieć w wybranej sieci wirtualnej. Można zmienić podsieć, do której przypisany jest interfejs sieciowy po jego utworzeniu.|
    |Przypisanie prywatnego adresu IP|Tak| W tym ustawieniu wybiera się metodę przypisywania adresu IPv4. Wybierz jedną z następujących metod przypisywania: **dynamiczne:** w przypadku wybrania tej opcji platforma Azure automatycznie przypisze następny dostępny adres z przestrzeni adresowej wybranej podsieci. **Statyczny:** W przypadku wybrania tej opcji należy ręcznie przypisać dostępny adres IP z przestrzeni adresowej wybranej podsieci. Adresy statyczne i dynamiczne nie zmieniają się, dopóki ich nie zmienisz lub interfejs sieciowy nie zostanie usunięty. Można zmienić metodę przypisywania po utworzeniu interfejsu sieciowego. Serwer DHCP platformy Azure przypisuje ten adres do interfejsu sieciowego w ramach systemu operacyjnego maszyny wirtualnej.|
    |Sieciowa grupa zabezpieczeń|Nie| Pozostaw wartość **Brak**, wybierz istniejącą [sieciową grupę zabezpieczeń](security-overview.md)lub [Utwórz sieciową grupę zabezpieczeń](tutorial-filter-network-traffic.md). Sieciowe grupy zabezpieczeń umożliwiają filtrowanie ruchu sieciowego do i z interfejsu sieciowego. Do interfejsu sieciowego można zastosować zero lub jedną grupę zabezpieczeń sieci. Do podsieci, do której jest przypisany interfejs sieciowy, można również zastosować zero lub jedną grupę zabezpieczeń sieci. Po zastosowaniu sieciowej grupy zabezpieczeń do interfejsu sieciowego i podsieci, do której jest przypisany interfejs sieciowy, czasami wystąpią nieoczekiwane wyniki. Aby rozwiązać problemy dotyczące sieciowych grup zabezpieczeń stosowanych do interfejsów sieciowych i podsieci, zobacz [Rozwiązywanie problemów z sieciowymi grupami zabezpieczeń](diagnose-network-traffic-filter-problem.md).|
    |Subskrypcja|Tak|Wybierz jedną z [subskrypcji](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)platformy Azure. Do maszyny wirtualnej, do której jest dołączany interfejs sieciowy, i sieci wirtualnej, z którą jest nawiązane połączenie, musi znajdować się w tej samej subskrypcji.|
    |Prywatny adres IP (IPv6)|Nie| Jeśli zaznaczysz to pole wyboru, adres IPv6 zostanie przypisany do interfejsu sieciowego, a nie do adresu IPv4 przypisanego do interfejsu sieciowego. Zobacz sekcję dotyczącą protokołu IPv6 w tym artykule, aby uzyskać ważne informacje dotyczące korzystania z protokołu IPv6 z interfejsami sieciowymi. Nie można wybrać metody przypisania dla adresu IPv6. Jeśli zdecydujesz się przypisać adres IPv6, zostanie on przypisany przy użyciu metody dynamicznej.
    |Nazwa IPv6 (pojawia się tylko wtedy, gdy jest zaznaczone pole wyboru **prywatny adres IP (IPv6)** ) |Tak, jeśli jest zaznaczone pole wyboru **prywatny adres IP (IPv6)** .| Ta nazwa jest przypisana do pomocniczej konfiguracji adresu IP dla interfejsu sieciowego. Aby dowiedzieć się więcej na temat konfiguracji protokołu IP, zobacz [Wyświetlanie ustawień interfejsu sieciowego](#view-network-interface-settings).|
    |Grupa zasobów|Tak|Wybierz istniejącą [grupę zasobów](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) lub utwórz ją. Interfejs sieciowy może istnieć w tej samej lub innej grupie zasobów niż maszyna wirtualna, do której jest dołączana, lub do sieci wirtualnej, z którą jest nawiązane połączenie.|
    |Lokalizacja|Tak|Maszyna wirtualna, do której jest dołączany interfejs sieciowy, i sieci wirtualnej, z którą jest ona podłączona, musi znajdować się w tej samej [lokalizacji](https://azure.microsoft.com/regions), nazywana również regionem.|

Portal nie udostępnia opcji przypisywania publicznego adresu IP do interfejsu sieciowego podczas jego tworzenia, ale Portal tworzy publiczny adres IP i przypisuje go do interfejsu sieciowego podczas tworzenia maszyny wirtualnej przy użyciu portalu. Aby dowiedzieć się, jak dodać publiczny adres IP do interfejsu sieciowego po jego utworzeniu, zobacz [Zarządzanie adresami IP](virtual-network-network-interface-addresses.md). Jeśli chcesz utworzyć interfejs sieciowy z publicznym adresem IP, musisz użyć interfejsu wiersza polecenia lub programu PowerShell, aby utworzyć interfejs sieciowy.

Portal nie udostępnia opcji przypisywania interfejsu sieciowego do grup zabezpieczeń aplikacji podczas tworzenia interfejsu sieciowego, ale w interfejsie wiersza polecenia platformy Azure i programie PowerShell. Istnieje jednak możliwość przypisania istniejącego interfejsu sieciowego do grupy zabezpieczeń aplikacji przy użyciu portalu, o ile interfejs sieciowy jest podłączony do maszyny wirtualnej. Aby dowiedzieć się, jak przypisać interfejs sieciowy do grupy zabezpieczeń aplikacji, zobacz [Dodawanie do lub usuwanie z grup zabezpieczeń aplikacji](#add-to-or-remove-from-application-security-groups).

>[!Note]
> Platforma Azure przypisuje adres MAC do interfejsu sieciowego dopiero po dołączeniu interfejsu sieciowego do maszyny wirtualnej, a maszyna wirtualna jest uruchamiana po raz pierwszy. Nie można określić adresu MAC, który usługa Azure przypisuje do interfejsu sieciowego. Adres MAC pozostaje przypisany do interfejsu sieciowego do momentu usunięcia interfejsu sieciowego lub zmiany prywatnego adresu IP przypisanego do podstawowej konfiguracji adresu IP podstawowego interfejsu sieciowego. Aby dowiedzieć się więcej na temat adresów IP i konfiguracji protokołu IP, zobacz [Zarządzanie adresami IP](virtual-network-network-interface-addresses.md)

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az network nic create](/cli/azure/network/nic)|
|Program PowerShell|[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)|

## <a name="view-network-interface-settings"></a>Wyświetl ustawienia interfejsu sieciowego

Można wyświetlać i zmieniać większość ustawień interfejsu sieciowego po jego utworzeniu. W portalu nie jest wyświetlany sufiks DNS ani członkostwo w grupach zabezpieczeń aplikacji dla interfejsu sieciowego. Aby wyświetlić sufiks DNS i członkostwo w grupie zabezpieczeń aplikacji, można użyć poleceń programu PowerShell lub interfejsu wiersza [polecenia](#view-settings-commands) platformy Azure.

1. W polu zawierającym *zasoby wyszukiwania* tekstu w górnej części Azure Portal wpisz *interfejsy sieciowe*. Gdy **interfejsy sieciowe** pojawiają się w wynikach wyszukiwania, wybierz je.
2. Wybierz z listy interfejs sieciowy, dla którego chcesz wyświetlić lub zmienić ustawienia.
3. Następujące elementy są wyświetlane dla wybranego interfejsu sieciowego:
   - **Przegląd:** Zawiera informacje o interfejsie sieciowym, takie jak przypisane do niego adresy IP, Sieć wirtualna/podsieć, do której jest przypisany interfejs sieciowy, oraz do maszyny wirtualnej, do której jest dołączony interfejs sieciowy (jeśli jest on dołączony do jednego). Na poniższej ilustracji przedstawiono ustawienia dotyczące interfejsu sieciowego o nazwie **mywebserver256**: ![ Network Interface Overview](./media/virtual-network-network-interface/nic-overview.png)

     Interfejs sieciowy można przenieść do innej grupy zasobów lub subskrypcji, wybierając pozycję (**Zmień**) obok nazwy **grupy zasobów** lub **subskrypcji**. Jeśli przeniesiesz interfejs sieciowy do nowej subskrypcji, musisz przenieść wszystkie zasoby związane z interfejsem sieciowym. Jeśli interfejs sieciowy jest podłączony do maszyny wirtualnej, na przykład należy również przenieść maszynę wirtualną i inne zasoby związane z maszyną wirtualną. Aby przenieść interfejs sieciowy, zobacz [przenoszenie zasobu do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-the-portal). Artykuł zawiera listę wymagań wstępnych oraz sposób przenoszenia zasobów przy użyciu Azure Portal, programu PowerShell i interfejsu wiersza polecenia platformy Azure.
   - **Konfiguracje protokołu IP:** Publiczne i prywatne adresy IPv4 i IPv6 przypisane do konfiguracji protokołu IP są wymienione tutaj. Jeśli adres IPv6 jest przypisany do konfiguracji adresu IP, adres nie jest wyświetlany. Aby dowiedzieć się więcej na temat konfiguracji protokołu IP oraz jak dodawać i usuwać adresy IP, zobacz [Konfigurowanie adresów IP dla interfejsu sieciowego platformy Azure](virtual-network-network-interface-addresses.md). W tej sekcji konfigurowane są również przekazywanie adresów IP i przypisanie podsieci. Aby dowiedzieć się więcej na temat tych ustawień, zobacz [Włączanie lub wyłączanie przekazywania adresów IP](#enable-or-disable-ip-forwarding) i [Zmienianie przypisania podsieci](#change-subnet-assignment).
   - **Serwery DNS:** Można określić, który serwer DNS ma być przypisany przez serwery DHCP platformy Azure. Interfejs sieciowy może dziedziczyć ustawienia z sieci wirtualnej, do której jest przypisany interfejs sieciowy, lub mieć niestandardowe ustawienie, które zastępuje ustawienie sieci wirtualnej, do której jest przypisane. Aby zmodyfikować wyświetlane elementy, zobacz [Zmienianie serwerów DNS](#change-dns-servers).
   - **Sieciowa Grupa zabezpieczeń (sieciowej grupy zabezpieczeń):** Wskazuje, który sieciowej grupy zabezpieczeń jest skojarzony z interfejsem sieciowym (jeśli istnieje). SIECIOWEJ grupy zabezpieczeń zawiera reguły ruchu przychodzącego i wychodzącego do filtrowania ruchu sieciowego dla interfejsu sieciowego. Jeśli sieciowej grupy zabezpieczeń jest skojarzona z interfejsem sieciowym, zostanie wyświetlona nazwa skojarzonego sieciowej grupy zabezpieczeń. Aby zmodyfikować wyświetlane elementy, zobacz [kojarzenie lub usuwanie skojarzenia sieciowej grupy zabezpieczeń](#associate-or-dissociate-a-network-security-group).
   - **Właściwości:** Wyświetla ustawienia klucza dotyczące interfejsu sieciowego, w tym jego adres MAC (puste, jeśli interfejs sieciowy nie jest dołączony do maszyny wirtualnej) i subskrypcję, w której istnieje.
   - **Obowiązujące reguły zabezpieczeń:**  Reguły zabezpieczeń są wyświetlane, jeśli interfejs sieciowy jest dołączony do uruchomionej maszyny wirtualnej, a sieciowej grupy zabezpieczeń jest skojarzony z interfejsem sieciowym, podsiecią, do której jest przypisana, lub obie. Aby dowiedzieć się więcej na temat informacji, zobacz temat [Wyświetlanie obowiązujących reguł zabezpieczeń](#view-effective-security-rules). Aby dowiedzieć się więcej na temat sieciowych grup zabezpieczeń, zobacz [Network Security Groups](security-overview.md).
   - **Efektywne trasy:** Trasy są wyświetlane, jeśli interfejs sieciowy jest podłączony do uruchomionej maszyny wirtualnej. Trasy są kombinacją domyślnych tras platformy Azure, wszelkich tras zdefiniowanych przez użytkownika i wszelkich tras BGP, które mogą istnieć dla podsieci, do której jest przypisany interfejs sieciowy. Aby dowiedzieć się więcej na temat tego, co jest wyświetlane, zobacz [Wyświetlanie efektywnych tras](#view-effective-routes). Aby dowiedzieć się więcej na temat domyślnych tras platformy Azure i tras zdefiniowanych przez użytkownika, zobacz [Omówienie routingu](virtual-networks-udr-overview.md).
Typowe ustawienia Azure Resource Manager: Aby dowiedzieć się więcej o typowych ustawieniach Azure Resource Manager, zobacz [Dziennik aktywności](../azure-monitor/platform/platform-logs-overview.md), [Kontrola dostępu (IAM)](../role-based-access-control/overview.md), [Tagi](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [blokady](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)i [skrypt automatyzacji](../azure-resource-manager/templates/export-template-portal.md).

<a name="view-settings-commands"></a>**Polecenia**

Jeśli adres IPv6 jest przypisany do interfejsu sieciowego, dane wyjściowe programu PowerShell zwracają fakt, że adres jest przypisany, ale nie zwraca przypisanego adresu. Podobnie interfejs wiersza polecenia zwraca informację o tym, że adres jest przypisany, ale zwraca *wartość null* w danych wyjściowych adresu.

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[AZ Network nic list](/cli/azure/network/nic) , aby wyświetlić interfejsy sieciowe w subskrypcji; [AZ Network nic show](/cli/azure/network/nic) do wyświetlania ustawień interfejsu sieciowego|
|Program PowerShell|[Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) , aby wyświetlić interfejsy sieciowe w ramach subskrypcji lub ustawienia widoku dla interfejsu sieciowego|

## <a name="change-dns-servers"></a>Zmień serwery DNS

Serwer DNS jest przypisywany przez serwer DHCP platformy Azure do interfejsu sieciowego w ramach systemu operacyjnego maszyny wirtualnej. Przypisany serwer DNS jest bez względu na to, że ustawienie serwera DNS dotyczy interfejsu sieciowego. Aby dowiedzieć się więcej na temat ustawień rozpoznawania nazw dla interfejsu sieciowego, zobacz [rozpoznawanie nazw dla maszyn wirtualnych](virtual-networks-name-resolution-for-vms-and-role-instances.md). Interfejs sieciowy może dziedziczyć ustawienia z sieci wirtualnej lub użyć własnych unikatowych ustawień, które zastępują ustawienia sieci wirtualnej.

1. W polu zawierającym *zasoby wyszukiwania* tekstu w górnej części Azure Portal wpisz *interfejsy sieciowe*. Gdy **interfejsy sieciowe** pojawiają się w wynikach wyszukiwania, wybierz je.
2. Wybierz interfejs sieciowy, dla którego chcesz zmienić serwer DNS z listy.
3. W obszarze **Ustawienia**wybierz pozycję **serwery DNS** .
4. Wybierz jedną z opcji:
   - **Dziedzicz z sieci wirtualnej**: Wybierz tę opcję, aby odziedziczyć ustawienie serwera DNS zdefiniowane dla sieci wirtualnej, do której jest przypisany interfejs sieciowy. Na poziomie sieci wirtualnej jest zdefiniowany niestandardowy serwer DNS lub serwer DNS dostarczony przez platformę Azure. Serwer DNS dostarczony przez platformę Azure może rozpoznać nazwy hostów dla zasobów przypisanych do tej samej sieci wirtualnej. Nazwa FQDN musi być używana do rozwiązywania zasobów przypisanych do różnych sieci wirtualnych.
   - **Niestandardowo**: można skonfigurować własny serwer DNS do rozpoznawania nazw w wielu sieciach wirtualnych. Wprowadź adres IP serwera, który ma być używany jako serwer DNS. Określony adres serwera DNS jest przypisywany tylko do tego interfejsu sieciowego i zastępuje wszystkie ustawienia DNS sieci wirtualnej, do której przypisany jest interfejs sieciowy.
     >[!Note]
     >Jeśli maszyna wirtualna korzysta z karty sieciowej, która jest częścią zestawu dostępności, wszystkie serwery DNS, które są określone dla każdej z maszyn wirtualnych ze wszystkich kart sieciowych, które są częścią zestawu dostępności, będą dziedziczone.
5. Wybierz pozycję **Zapisz**.

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[AZ Network nic Update](/cli/azure/network/nic)|
|Program PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="enable-or-disable-ip-forwarding"></a>Włączanie lub wyłączanie przekazywania adresów IP

Przekazywanie adresów IP włącza maszynę wirtualną, do której jest dołączony interfejs sieciowy:
- Odbieraj ruch sieciowy, który nie jest przeznaczony dla jednego z adresów IP przypisanych do dowolnych konfiguracji protokołu IP przypisanych do interfejsu sieciowego.
- Wysyłaj ruch sieciowy z innym źródłowym adresem IP niż ten przypisany do jednej z konfiguracji protokołu IP interfejsu sieciowego.

Ustawienie musi być włączone dla każdego interfejsu sieciowego, który jest dołączony do maszyny wirtualnej, która odbiera ruch, którego maszyna wirtualna musi przesłać dalej. Maszyna wirtualna może przekazywać ruch, niezależnie od tego, czy ma on wiele interfejsów sieciowych, czy dołączony do niego pojedynczy interfejs sieciowy. Gdy przekazywanie adresów IP to ustawienie platformy Azure, maszyna wirtualna musi również uruchamiać aplikację, która umożliwia przekazywanie ruchu sieciowego, takich jak zapora, optymalizacja sieci WAN i aplikacje równoważenia obciążenia. Gdy maszyna wirtualna korzysta z aplikacji sieciowych, maszyna wirtualna jest często określana jako sieciowe urządzenie wirtualne. Możesz wyświetlić listę gotowych do wdrożenia sieciowych urządzeń wirtualnych w [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). Przekazywanie adresów IP jest zwykle używane w przypadku tras zdefiniowanych przez użytkownika. Aby dowiedzieć się więcej o trasach zdefiniowanych przez użytkownika, zobacz [trasy zdefiniowane przez użytkownika](virtual-networks-udr-overview.md).

1. W polu zawierającym *zasoby wyszukiwania* tekstu w górnej części Azure Portal wpisz *interfejsy sieciowe*. Gdy **interfejsy sieciowe** pojawiają się w wynikach wyszukiwania, wybierz je.
2. Wybierz interfejs sieciowy, który chcesz włączyć lub wyłączyć przekazywanie adresów IP.
3. W sekcji **Ustawienia** wybierz pozycję **konfiguracje adresów IP** .
4. Wybierz opcję **włączone** lub **wyłączone** (ustawienie domyślne), aby zmienić ustawienie.
5. Wybierz pozycję **Zapisz**.

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[AZ Network nic Update](/cli/azure/network/nic)|
|Program PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="change-subnet-assignment"></a>Zmień przypisanie podsieci

Można zmienić podsieć, ale nie sieć wirtualną, do której przypisany jest interfejs sieciowy.

1. W polu zawierającym *zasoby wyszukiwania* tekstu w górnej części Azure Portal wpisz *interfejsy sieciowe*. Gdy **interfejsy sieciowe** pojawiają się w wynikach wyszukiwania, wybierz je.
2. Wybierz interfejs sieciowy, dla którego chcesz zmienić przypisanie podsieci.
3. W obszarze **Ustawienia**wybierz pozycję **konfiguracje adresów IP** . Jeśli obok żadnych prywatnych adresów IP dla dowolnych konfiguracji protokołu IP ma **(statyczny)** , należy zmienić metodę przypisywania adresów IP na dynamiczną, wykonując poniższe kroki. Wszystkie prywatne adresy IP muszą być przypisane przy użyciu metody dynamicznego przypisywania, aby zmienić przypisanie podsieci dla interfejsu sieciowego. Jeśli adresy są przypisywane przy użyciu metody dynamicznej, przejdź do kroku 5. Jeśli dowolnych adresów IPv4 zostanie przypisanych za pomocą metody przypisania statycznej, wykonaj następujące kroki, aby zmienić metodę przypisywania na dynamiczną:
   - Wybierz konfigurację adresu IP, dla której chcesz zmienić metodę przypisywania adresów IPv4 z listy konfiguracji protokołu IP.
   - Wybierz pozycję **dynamiczne** dla metody **przypisywania** prywatnych adresów IP. Nie można przypisać adresu IPv6 za pomocą metody przypisania statycznego.
   - Wybierz pozycję **Zapisz**.
4. Wybierz podsieć, do której chcesz przenieść interfejs sieciowy z listy rozwijanej **podsieć** .
5. Wybierz pozycję **Zapisz**. Nowe adresy dynamiczne są przypisywane z zakresu adresów podsieci dla nowej podsieci. Po przypisaniu interfejsu sieciowego do nowej podsieci można przypisać statyczny adres IPv4 z nowego zakresu adresów podsieci, jeśli wybierzesz opcję. Aby dowiedzieć się więcej na temat dodawania, zmieniania i usuwania adresów IP dla interfejsu sieciowego, zobacz [Zarządzanie adresami IP](virtual-network-network-interface-addresses.md).

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[AZ Network nic IP-config Update](/cli/azure/network/nic/ip-config)|
|Program PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="add-to-or-remove-from-application-security-groups"></a>Dodaj do lub Usuń z grup zabezpieczeń aplikacji

Możesz dodać interfejs sieciowy do lub usunąć interfejs sieciowy z grupy zabezpieczeń aplikacji przy użyciu portalu, jeśli interfejs sieciowy jest podłączony do maszyny wirtualnej. Możesz użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure, aby dodać interfejs sieciowy do lub usunąć interfejs sieciowy z grupy zabezpieczeń aplikacji, niezależnie od tego, czy interfejs sieciowy jest dołączony do maszyny wirtualnej, czy nie. Dowiedz się więcej o [grupach zabezpieczeń aplikacji](security-overview.md#application-security-groups) oraz o sposobie [tworzenia grupy zabezpieczeń aplikacji](manage-network-security-group.md).

1. W polu *Wyszukaj zasoby, usługi i dokumenty* w górnej części portalu zacznij pisać nazwę maszyny wirtualnej, która ma interfejs sieciowy, do którego chcesz dodać lub usunąć z grupy zabezpieczeń aplikacji. Gdy nazwa maszyny wirtualnej zostanie wyświetlona w wynikach wyszukiwania, wybierz ją.
2. W obszarze **USTAWIENIA** wybierz pozycję **Sieć**.  Wybierz pozycję **grupy zabezpieczeń aplikacji** , a następnie **Skonfiguruj grupy zabezpieczeń**aplikacji wybierz grupy zabezpieczeń aplikacji, do których chcesz dodać interfejs sieciowy, lub usuń zaznaczenie grup zabezpieczeń aplikacji, z których chcesz usunąć interfejs sieciowy, a następnie wybierz pozycję **Zapisz**. Tylko interfejsy sieciowe, które znajdują się w tej samej sieci wirtualnej, można dodać do tej samej grupy zabezpieczeń aplikacji. Grupa zabezpieczeń aplikacji musi znajdować się w tej samej lokalizacji co interfejs sieciowy.

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[AZ Network nic Update](/cli/azure/network/nic)|
|Program PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="associate-or-dissociate-a-network-security-group"></a>Kojarzenie lub usuwanie skojarzenia sieciowej grupy zabezpieczeń

1. W polu wyszukiwania w górnej części portalu wprowadź *interfejsy sieciowe* w polu wyszukiwania. Gdy **interfejsy sieciowe** pojawiają się w wynikach wyszukiwania, wybierz je.
2. Wybierz interfejs sieciowy z listy, do której chcesz skojarzyć sieciową grupę zabezpieczeń, lub usuń skojarzenie sieciowej grupy zabezpieczeń z.
3. W obszarze **Ustawienia**wybierz pozycję **sieciowa Grupa zabezpieczeń** .
4. Kliknij pozycję **Edytuj**.
5. Wybierz pozycję **sieciowa Grupa zabezpieczeń** , a następnie wybierz grupę zabezpieczeń sieci, która ma zostać skojarzona z interfejsem sieciowym, lub wybierz opcję **Brak**, aby usunąć skojarzenie sieciowej grupy zabezpieczeń.
6. Wybierz pozycję **Zapisz**.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [AZ Network nic Update](/cli/azure/network/nic#az-network-nic-update)
- PowerShell: [Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)

## <a name="delete-a-network-interface"></a>Usuwanie interfejsu sieciowego

Możesz usunąć interfejs sieciowy, o ile nie jest on dołączony do maszyny wirtualnej. Jeśli interfejs sieciowy jest podłączony do maszyny wirtualnej, należy najpierw umieścić maszynę wirtualną w stanie zatrzymania (bez przydziału), a następnie odłączyć interfejs sieciowy od maszyny wirtualnej. Aby odłączyć interfejs sieciowy od maszyny wirtualnej, wykonaj kroki opisane w temacie [Odłącz interfejs sieciowy od maszyny wirtualnej](virtual-network-network-interface-vm.md#remove-a-network-interface-from-a-vm). Nie można odłączyć interfejsu sieciowego od maszyny wirtualnej, jeśli jest to jedyny interfejs sieciowy dołączony do maszyny wirtualnej. Do maszyny wirtualnej musi być zawsze dołączony co najmniej jeden interfejs sieciowy. Usunięcie maszyny wirtualnej powoduje odłączenie wszystkich podłączonych do niej interfejsów sieciowych, ale nie powoduje usunięcia interfejsów sieciowych.

1. W polu zawierającym *zasoby wyszukiwania* tekstu w górnej części Azure Portal wpisz *interfejsy sieciowe*. Gdy **interfejsy sieciowe** pojawiają się w wynikach wyszukiwania, wybierz je.
2. Wybierz z listy interfejs sieciowy, który chcesz usunąć.
3. W obszarze **Przegląd** wybierz pozycję **Usuń**.
4. Wybierz pozycję **tak** , aby potwierdzić usunięcie interfejsu sieciowego.

Po usunięciu interfejsu sieciowego zostaną wydane wszystkie przypisane adresy MAC lub IP.

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[AZ Network nic Delete](/cli/azure/network/nic)|
|Program PowerShell|[Remove-AzNetworkInterface](/powershell/module/az.network/remove-aznetworkinterface)|

## <a name="resolve-connectivity-issues"></a>Rozwiązywanie problemów z łącznością

Jeśli nie można nawiązać połączenia z maszyną wirtualną lub z niej, reguły zabezpieczeń grupy zabezpieczeń sieci lub trasy obowiązujące dla interfejsu sieciowego mogą powodować problem. Dostępne są następujące opcje ułatwiające rozwiązanie tego problemu:

### <a name="view-effective-security-rules"></a>Wyświetlanie obowiązujących reguł zabezpieczeń

Obowiązujące reguły zabezpieczeń dla każdego interfejsu sieciowego dołączonego do maszyny wirtualnej są kombinacją reguł utworzonych w sieciowej grupie zabezpieczeń i [domyślnych reguł zabezpieczeń](security-overview.md#default-security-rules). Zrozumienie obowiązujących reguł zabezpieczeń dla interfejsu sieciowego może pomóc w ustaleniu, dlaczego nie można komunikować się z maszyną wirtualną ani z niej. Można wyświetlić obowiązujące reguły dla dowolnego interfejsu sieciowego dołączonego do uruchomionej maszyny wirtualnej.

1. W polu wyszukiwania w górnej części portalu wprowadź nazwę maszyny wirtualnej, dla której chcesz wyświetlić obowiązujące reguły zabezpieczeń. Jeśli nie znasz nazwy maszyny wirtualnej, wprowadź *maszyny wirtualne* w polu wyszukiwania. Gdy **maszyny wirtualne** pojawiają się w wynikach wyszukiwania, wybierz je, a następnie wybierz maszynę wirtualną z listy.
2. W obszarze **Ustawienia**wybierz pozycję **Sieć** .
3. Wybierz nazwę interfejsu sieciowego.
4. Wybierz pozycję **efektywne reguły zabezpieczeń** w obszarze **Pomoc techniczna i rozwiązywanie problemów**.
5. Przejrzyj listę obowiązujących reguł zabezpieczeń, aby ustalić, czy istnieją poprawne reguły dla wymaganej komunikacji przychodzącej i wychodzącej. Dowiedz się więcej na temat tego, co widzisz na liście w temacie [Network Security Group — Omówienie](security-overview.md).

Funkcja weryfikacji przepływu IP w usłudze Azure Network Watcher może również pomóc w ustaleniu, czy reguły zabezpieczeń uniemożliwiają komunikację między maszyną wirtualną a punktem końcowym. Aby dowiedzieć się więcej, zobacz [Weryfikowanie przepływu adresów IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [AZ Network nic list-sieciowej grupy zabezpieczeń](/cli/azure/network/nic#az-network-nic-list-effective-nsg)
- PowerShell: [Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup)

### <a name="view-effective-routes"></a>Wyświetl efektywne trasy

Efektywne trasy interfejsów sieciowych dołączonych do maszyny wirtualnej to kombinacja domyślnych tras, wszelkich utworzonych tras oraz wszelkich tras propagowanych z sieci lokalnych za pośrednictwem protokołu BGP za pośrednictwem bramy sieci wirtualnej platformy Azure. Zrozumienie obowiązujących tras dla interfejsu sieciowego może pomóc w ustaleniu, dlaczego nie można komunikować się z maszyną wirtualną lub z niej. Efektywne trasy można wyświetlić dla dowolnego interfejsu sieciowego dołączonego do uruchomionej maszyny wirtualnej.

1. W polu wyszukiwania w górnej części portalu wprowadź nazwę maszyny wirtualnej, dla której chcesz wyświetlić obowiązujące reguły zabezpieczeń. Jeśli nie znasz nazwy maszyny wirtualnej, wprowadź *maszyny wirtualne* w polu wyszukiwania. Gdy **maszyny wirtualne** pojawiają się w wynikach wyszukiwania, wybierz je, a następnie wybierz maszynę wirtualną z listy.
2. W obszarze **Ustawienia**wybierz pozycję **Sieć** .
3. Wybierz nazwę interfejsu sieciowego.
4. Wybierz pozycję **efektywne trasy** w obszarze **Pomoc techniczna i rozwiązywanie problemów**.
5. Przejrzyj listę efektywnych tras, aby ustalić, czy istnieją poprawne trasy dla wymaganej komunikacji przychodzącej i wychodzącej. Dowiedz się więcej o tym, co widzisz na liście w temacie [Omówienie routingu](virtual-networks-udr-overview.md).

Funkcja następnego przeskoku usługi Azure Network Watcher może również pomóc określić, czy trasy uniemożliwiają komunikację między maszyną wirtualną a punktem końcowym. Aby dowiedzieć się więcej, zobacz [Następny przeskok](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [AZ Network nic show-skuteczna-Route-Table](/cli/azure/network/nic#az-network-nic-show-effective-route-table)
- PowerShell: [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)

## <a name="permissions"></a>Uprawnienia

Aby wykonać zadania w interfejsach sieciowych, Twoje konto musi być przypisane do roli [współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do roli [niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , do której przypisano odpowiednie uprawnienia wymienione w poniższej tabeli:

| Akcja                                                                     | Nazwa                                                      |
| ---------                                                                  | -------------                                             |
| Microsoft. Network/networkInterfaces/odczyt                                   | Pobierz interfejs sieciowy                                     |
| Microsoft. Network/networkInterfaces/Write                                  | Utwórz lub zaktualizuj interfejs sieciowy                        |
| Microsoft. Network/networkInterfaces/Join/Action                            | Dołączanie interfejsu sieciowego do maszyny wirtualnej           |
| Microsoft. Network/networkInterfaces/Delete                                 | Usuń interfejs sieciowy                                  |
| Microsoft. Network/networkInterfaces/joinViaPrivateIp/akcja                | Dołącz zasób do interfejsu sieciowego za pośrednictwem Servi...     |
| Microsoft. Network/networkInterfaces/effectiveRouteTable/akcja             | Pobierz tabelę efektywnej trasy interfejsu sieciowego               |
| Microsoft. Network/networkInterfaces/effectiveNetworkSecurityGroups/akcja  | Pobierz efektywne grupy zabezpieczeń interfejsu sieciowego           |
| Microsoft. Network/networkInterfaces/loadBalancers/odczyt                     | Pobierz moduły równoważenia obciążenia w interfejsie sieciowym                      |
| Microsoft. Network/networkInterfaces/serviceassociations/Read               | Pobierz skojarzenie usługi                                   |
| Microsoft. Network/networkInterfaces/serviceassociations/Write              | Tworzenie lub aktualizowanie skojarzenia usługi                    |
| Microsoft. Network/networkInterfaces/serviceassociations/Delete             | Usuń skojarzenie usługi                                |
| Microsoft. Network/networkInterfaces/serviceassociations/Validate/Action    | Weryfikuj skojarzenie usługi                              |
| Microsoft. Network/networkInterfaces/ipconfiguration/odczyt                  | Pobierz konfigurację protokołu IP interfejsu sieciowego                    |

## <a name="next-steps"></a>Następne kroki

- Tworzenie maszyny wirtualnej z wieloma kartami sieciowymi przy użyciu [interfejsu wiersza polecenia platformy Azure](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [programu PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Tworzenie pojedynczej maszyny wirtualnej karty sieciowej z wieloma adresami IPv4 przy użyciu [interfejsu wiersza polecenia platformy Azure](virtual-network-multiple-ip-addresses-cli.md) lub [programu PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
- Utwórz pojedynczą maszynę wirtualną kart sieciowych z prywatnym adresem IPv6 (za Azure Load Balancer) przy użyciu [interfejsu wiersza polecenia platformy Azure](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [programu PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)lub [szablonu Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Tworzenie interfejsu sieciowego przy użyciu [programu PowerShell](powershell-samples.md) lub przykładowego skryptu [interfejsu wiersza polecenia platformy Azure](cli-samples.md) lub użycie [szablonu Menedżer zasobów](template-samples.md) platformy Azure
- Tworzenie i przypisywanie [definicji Azure Policy](policy-samples.md) dla sieci wirtualnych
