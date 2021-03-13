---
title: Skonfiguruj adresy IP dla interfejsu sieciowego platformy Azure | Microsoft Docs
description: Dowiedz się, jak dodawać, zmieniać i usuwać prywatne i publiczne adresy IP dla interfejsu sieciowego.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/22/2020
ms.author: allensu
ms.openlocfilehash: 1df132e558421d2ec6e26c3883c89457716dfc42
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2021
ms.locfileid: "103419018"
---
# <a name="add-change-or-remove-ip-addresses-for-an-azure-network-interface"></a>Dodawanie, zmienianie i usuwanie adresów IP dla interfejsów sieciowych platformy Azure

Dowiedz się, jak dodawać, zmieniać i usuwać publiczne i prywatne adresy IP dla interfejsu sieciowego. Prywatne adresy IP przypisane do interfejsu sieciowego umożliwiają maszynie wirtualnej komunikowanie się z innymi zasobami w sieci wirtualnej platformy Azure i połączonych sieciach. Prywatny adres IP umożliwia również komunikację wychodzącą z Internetem przy użyciu nieprzewidywalnego adresu IP. [Publiczny adres IP](virtual-network-public-ip-address.md) przypisany do interfejsu sieciowego umożliwia komunikację przychodzącą z maszyną wirtualną z Internetu. Adres umożliwia również komunikację wychodzącą z maszyny wirtualnej z Internetem przy użyciu przewidywalnego adresu IP. Aby uzyskać szczegółowe informacje, zobacz [Opis połączeń wychodzących na platformie Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Jeśli musisz utworzyć, zmienić lub usunąć interfejs sieciowy, zapoznaj się z artykułem [Zarządzanie interfejsem sieciowym](virtual-network-network-interface.md) . Jeśli zachodzi potrzeba dodania interfejsów sieciowych do lub usunięcia interfejsów sieciowych z maszyny wirtualnej, przeczytaj artykuł [Dodawanie lub usuwanie interfejsów sieciowych](virtual-network-network-interface-vm.md) .

## <a name="before-you-begin"></a>Zanim rozpoczniesz

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przed wykonaniem kroków opisanych w sekcji tego artykułu wykonaj następujące zadania:

- Jeśli nie masz jeszcze konta platformy Azure, Utwórz [konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free).
- Jeśli używasz portalu, Otwórz https://portal.azure.com program i zaloguj się przy użyciu konta platformy Azure.
- W przypadku wykonywania zadań w tym artykule przy użyciu poleceń programu PowerShell uruchom polecenia w [Azure Cloud Shell](https://shell.azure.com/powershell)lub przez uruchomienie programu PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Ten samouczek wymaga modułu Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.
- W przypadku korzystania z poleceń interfejsu wiersza polecenia (CLI) platformy Azure w celu wykonania zadań w tym artykule Uruchom polecenia w [Azure Cloud Shell](https://shell.azure.com/bash)lub przez uruchomienie interfejsu wiersza polecenia na komputerze. Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, musisz też uruchomić polecenie `az login` , aby utworzyć połączenie z platformą Azure.

Konto, do którego należy się zalogować lub połączyć się z platformą Azure za pomocą programu, musi być przypisane do roli [współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , do której przypisano odpowiednie działania wymienione w [uprawnieniach interfejsu sieciowego](virtual-network-network-interface.md#permissions).

## <a name="add-ip-addresses"></a>Dodaj adresy IP

Możesz dodać dowolną liczbę [prywatnych](#private) i [publicznych](#public) adresów [IPv4](#ipv4) do interfejsu sieciowego, w granicach wymienionych w artykule [limity platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) . Prywatny adres IPv6 można dodać do jednej [pomocniczej konfiguracji adresu IP](#secondary) (o ile nie istnieją żadne istniejące konfiguracje pomocniczych adresów IP) dla istniejącego interfejsu sieciowego. Każdy interfejs sieciowy może mieć co najwyżej jeden prywatny adres IPv6. Opcjonalnie możesz dodać publiczny adres IPv6 do konfiguracji interfejsu sieciowego IPv6. Szczegółowe informacje o używaniu adresów IPv6 można znaleźć w [protokole IPv6](#ipv6) .

1. W polu zawierającym *zasoby wyszukiwania* tekstu w górnej części Azure Portal wpisz *interfejsy sieciowe*. Gdy **interfejsy sieciowe** pojawiają się w wynikach wyszukiwania, wybierz je.
2. Wybierz interfejs sieciowy, dla którego chcesz dodać adres IPv4 z listy.
3. W obszarze **Ustawienia** wybierz pozycję **konfiguracje adresów IP**.
4. W obszarze **konfiguracje adresów IP** wybierz pozycję **+ Dodaj**.
5. Określ następujące opcje, a następnie wybierz przycisk **OK**:

   |Ustawienie|Wymagane?|Szczegóły|
   |---|---|---|
   |Nazwa|Tak|Musi być unikatowa dla interfejsu sieciowego|
   |Typ|Tak|Ponieważ dodawana jest konfiguracja protokołu IP do istniejącego interfejsu sieciowego, a każdy interfejs sieciowy musi mieć [podstawową](#primary) konfigurację adresu IP, jedyną opcją jest **pomocnicza**.|
   |Metoda przypisania prywatnego adresu IP|Tak|[**Dynamiczny**](#dynamic): platforma Azure przypisuje następny dostępny adres dla zakresu adresów podsieci, w którym jest wdrożony interfejs sieciowy. [**Statyczny**](#static): przypiszesz nieużywany adres dla zakresu adresów podsieci, w którym jest wdrożony interfejs sieciowy.|
   |Publiczny adres IP|Nie|**Wyłączone:** Z konfiguracją IP nie jest obecnie skojarzony żaden zasób publicznego adresu IP. **Włączone:** Wybierz istniejący publiczny adres IP IPv4 lub Utwórz nowy. Aby dowiedzieć się, jak utworzyć publiczny adres IP, zapoznaj się z artykułem [publiczne adresy IP](virtual-network-public-ip-address.md#create-a-public-ip-address) .|
6. Ręcznie Dodaj pomocnicze prywatne adresy IP do systemu operacyjnego maszyny wirtualnej, wykonując instrukcje podane w artykule [przypisywanie wielu adresów IP do systemów operacyjnych maszyn wirtualnych](virtual-network-multiple-ip-addresses-portal.md#os-config) . Zapoznaj się z [prywatnymi](#private) adresami IP, aby uzyskać specjalne uwagi przed ręcznym dodaniem adresów IP do systemu operacyjnego maszyny wirtualnej. Nie należy dodawać żadnych publicznych adresów IP do systemu operacyjnego maszyny wirtualnej.

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az network nic ip-config create](/cli/azure/network/nic/ip-config)|
|PowerShell|[Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/add-aznetworkinterfaceipconfig)|

## <a name="change-ip-address-settings"></a>Zmień ustawienia adresu IP

Może być konieczna zmiana metody przypisania adresu IPv4, zmiana statycznego adresu IPv4 lub zmiana publicznego adresu IP przypisanego do interfejsu sieciowego. Jeśli zmieniasz prywatny adres IPv4 dodatkowej konfiguracji adresu IP skojarzonego z dodatkowym interfejsem sieciowym na maszynie wirtualnej (Dowiedz się więcej na temat [podstawowych i dodatkowych interfejsów sieciowych](virtual-network-network-interface-vm.md)), umieść maszynę wirtualną w stanie zatrzymania (bez przydziału) przed wykonaniem następujących kroków:

1. W polu zawierającym *zasoby wyszukiwania* tekstu w górnej części Azure Portal wpisz *interfejsy sieciowe*. Gdy **interfejsy sieciowe** pojawiają się w wynikach wyszukiwania, wybierz je.
2. Wybierz z listy interfejs sieciowy, dla którego chcesz wyświetlić lub zmienić ustawienia adresu IP.
3. W obszarze **Ustawienia** wybierz pozycję **konfiguracje adresów IP**.
4. Wybierz konfigurację adresu IP, którą chcesz zmodyfikować z listy.
5. Zmień ustawienia zgodnie z potrzebami, korzystając z informacji o ustawieniach w kroku 5 w temacie [Dodawanie konfiguracji protokołu IP](#add-ip-addresses).
6. Wybierz pozycję **Zapisz**.

>[!NOTE]
>Jeśli podstawowy interfejs sieciowy ma wiele konfiguracji adresów IP i zmienisz prywatny adres IP konfiguracji podstawowego adresu IP, musisz ręcznie ponownie przypisać podstawowe i pomocnicze adresy IP do interfejsu sieciowego w systemie Windows (nie jest to wymagane dla systemu Linux). Aby ręcznie przypisać adresy IP do interfejsu sieciowego w ramach systemu operacyjnego, zobacz [przypisywanie wielu adresów IP do maszyn wirtualnych](virtual-network-multiple-ip-addresses-portal.md#os-config). Aby uzyskać specjalne uwagi przed ręcznym dodaniem adresów IP do systemu operacyjnego maszyny wirtualnej, zobacz [prywatne](#private) adresy IP. Nie należy dodawać żadnych publicznych adresów IP do systemu operacyjnego maszyny wirtualnej.

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[AZ Network nic IP-config Update](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="remove-ip-addresses"></a>Usuń adresy IP

Można usunąć [prywatne](#private) i [publiczne](#public) adresy IP z interfejsu sieciowego, ale do interfejsu sieciowego musi być przypisany co najmniej jeden prywatny adres IPv4.

1. W polu zawierającym *zasoby wyszukiwania* tekstu w górnej części Azure Portal wpisz *interfejsy sieciowe*. Gdy **interfejsy sieciowe** pojawiają się w wynikach wyszukiwania, wybierz je.
2. Wybierz interfejs sieciowy, dla którego chcesz usunąć adresy IP z listy.
3. W obszarze **Ustawienia** wybierz pozycję **konfiguracje adresów IP**.
4. Wybierz prawym przyciskiem myszy konfigurację [pomocniczego](#secondary) adresu IP (nie można usunąć konfiguracji [podstawowej](#primary) ), którą chcesz usunąć, wybierz pozycję **Usuń**, a następnie wybierz pozycję **tak**, aby potwierdzić usunięcie. Jeśli z konfiguracją skojarzono zasób publicznego adresu IP, zasób jest usuwany z konfiguracji adresu IP, ale zasób nie zostanie usunięty.

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[AZ Network nic IP-config Delete](/cli/azure/network/nic/ip-config)|
|PowerShell|[Remove-AzNetworkInterfaceIpConfig](/powershell/module/az.network/remove-aznetworkinterfaceipconfig)|

## <a name="ip-configurations"></a>Konfiguracje protokołu IP

[Prywatne](#private) i (opcjonalnie) [publiczne](#public) adresy IP są przypisywane do co najmniej jednej konfiguracji adresu IP przypisanej do interfejsu sieciowego. Istnieją dwa typy konfiguracji protokołu IP:

### <a name="primary"></a>Podstawowe

Każdy interfejs sieciowy ma przypisaną jedną podstawową konfigurację adresu IP. Podstawowa konfiguracja adresu IP:

- Ma przypisany [prywatny](#private) adres [IPv4](#ipv4) . Nie można przypisać prywatnego adresu [IPv6](#ipv6) do podstawowej konfiguracji adresu IP.
- Może również mieć przypisany [publiczny](#public) adres IPv4. Nie można przypisać publicznego adresu IPv6 do konfiguracji podstawowego protokołu IP (IPv4). 

### <a name="secondary"></a>Pomocniczy

Oprócz podstawowej konfiguracji adresu IP, interfejs sieciowy może mieć przypisaną zero lub więcej pomocniczych konfiguracji adresów IP. Pomocnicza Konfiguracja adresu IP:

- Musi mieć przypisany prywatny adres IPv4 lub IPv6. Jeśli adres jest adresem IPv6, interfejs sieciowy może mieć tylko jedną dodatkową konfigurację adresu IP. Jeśli adres jest adresem IPv4, interfejs sieciowy może mieć przypisane wiele pomocniczych konfiguracji adresów IP. Aby dowiedzieć się więcej na temat liczby prywatnych i publicznych adresów IPv4, które można przypisać do interfejsu sieciowego, zobacz artykuł dotyczący [limitów platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) .
- Może również mieć przypisany publiczny adres IPv4 lub IPv6. Przypisywanie wielu adresów IPv4 do interfejsu sieciowego jest przydatne w scenariuszach takich jak:
  - Hostowanie wielu witryn sieci Web lub usług z różnymi adresami IP i certyfikatami TLS/SSL na jednym serwerze.
  - Maszyna wirtualna służąca jako sieciowe urządzenie wirtualne, takie jak zapora lub moduł równoważenia obciążenia.
  - Możliwość dodawania dowolnych prywatnych adresów IPv4 dla dowolnego z interfejsów sieciowych do Azure Load Balancer puli zaplecza. W przeszłości tylko podstawowy adres IPv4 dla podstawowego interfejsu sieciowego może zostać dodany do puli zaplecza. Aby dowiedzieć się więcej na temat równoważenia obciążenia wieloma konfiguracjami IPv4, zobacz artykuł dotyczący [równoważenia obciążenia wielu adresów IP](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . 
  - Możliwość równoważenia obciążenia jednego adresu IPv6 przypisanego do interfejsu sieciowego. Aby dowiedzieć się więcej o tym, jak równoważyć obciążenie z prywatnym adresem IPv6, zobacz artykuł [równoważenie obciążenia IPv6 addresss](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) .

## <a name="address-types"></a>Typy adresów

Do [konfiguracji protokołu IP](#ip-configurations)można przypisać następujące typy adresów IP:

### <a name="private"></a>Prywatny

Prywatne adresy [IPv4](#ipv4) lub IPv6 umożliwiają maszynie wirtualnej komunikowanie się z innymi zasobami w sieci wirtualnej lub innych połączonych sieciach. 

Domyślnie serwery DHCP platformy Azure przypisują prywatny adres IPv4 dla [podstawowej konfiguracji adresu IP](#primary) interfejsu sieciowego platformy Azure do interfejsu sieciowego w ramach systemu operacyjnego maszyny wirtualnej. Jeśli to konieczne, nigdy nie należy ręcznie ustawiać adresu IP interfejsu sieciowego w ramach systemu operacyjnego maszyny wirtualnej.

> [!WARNING]
> Jeśli adres IPv4 ustawiony jako podstawowy adres IP interfejsu sieciowego w ramach systemu operacyjnego maszyny wirtualnej jest kiedykolwiek inny niż prywatny adres IPv4 przypisany do podstawowej konfiguracji adresu IP podstawowego interfejsu sieciowego dołączonego do maszyny wirtualnej na platformie Azure, nastąpi utrata łączności z maszyną wirtualną.

Istnieją scenariusze, w których konieczne jest ręczne ustawienie adresu IP interfejsu sieciowego w ramach systemu operacyjnego maszyny wirtualnej. Na przykład należy ręcznie ustawić podstawowe i pomocnicze adresy IP systemu operacyjnego Windows podczas dodawania wielu adresów IP do maszyny wirtualnej platformy Azure. W przypadku maszyny wirtualnej z systemem Linux może być konieczne ręczne ustawienie pomocniczych adresów IP. Aby uzyskać szczegółowe informacje [, zobacz Dodawanie adresów IP do systemu operacyjnego maszyny wirtualnej](virtual-network-multiple-ip-addresses-portal.md#os-config) . Jeśli kiedykolwiek zajdzie potrzeba zmiany adresu przypisanego do konfiguracji protokołu IP, zalecane jest:

1. Upewnij się, że maszyna wirtualna otrzymuje adres z serwerów DHCP platformy Azure. Po zakończeniu należy zmienić przypisanie adresu IP z powrotem do protokołu DHCP w systemie operacyjnym i ponownie uruchomić maszynę wirtualną.
2. Zatrzymaj (Cofnij przydział) maszyny wirtualnej.
3. Zmień adres IP dla konfiguracji protokołu IP na platformie Azure.
4. Uruchom maszynę wirtualną.
5. [Ręcznie skonfiguruj](virtual-network-multiple-ip-addresses-portal.md#os-config) pomocnicze adresy IP w ramach systemu operacyjnego (a także podstawowy adres IP w systemie Windows), aby dopasować je do wartości ustawionej na platformie Azure.

Zgodnie z powyższymi krokami prywatny adres IP przypisany do interfejsu sieciowego na platformie Azure i w systemie operacyjnym maszyny wirtualnej pozostaje taki sam. Aby śledzić, które maszyny wirtualne w ramach subskrypcji ręcznie ustawili adresy IP w ramach systemu operacyjnego, należy rozważyć dodanie [znacznika](../azure-resource-manager/management/tag-resources.md) platformy Azure do maszyn wirtualnych. Możesz użyć "przypisywania adresów IP: static", na przykład. W ten sposób można łatwo znaleźć maszyny wirtualne w ramach subskrypcji, które ręcznie ustawili adres IP w ramach systemu operacyjnego.

Poza umożliwieniem, aby maszyna wirtualna mogła komunikować się z innymi zasobami w ramach tych samych lub połączonych sieci wirtualnych, prywatny adres IP również umożliwia maszynie wirtualnej komunikację wychodzącą z Internetem. Połączenia wychodzące to adresy sieci źródłowej tłumaczone przez platformę Azure na nieprzewidywalny publiczny adres IP. Aby dowiedzieć się więcej na temat wychodzącej łączności z Internetem na platformie Azure, przeczytaj artykuł dotyczący [wychodzącego połączenia z Internetem](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Nie można przekazać komunikacji przychodzącej do prywatnego adresu IP maszyny wirtualnej z Internetu. Jeśli połączenia wychodzące wymagają przewidywalnego publicznego adresu IP, skojarz zasób publicznego adresu IP z interfejsem sieciowym.

### <a name="public"></a>Publiczne

Publiczne adresy IP przypisane za pośrednictwem zasobu publicznego adresu IP umożliwiają komunikację przychodzącą z maszyną wirtualną z Internetu. Połączenia wychodzące z Internetem używają przewidywalnego adresu IP. Aby uzyskać szczegółowe informacje, zobacz [Opis połączeń wychodzących na platformie Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . Do konfiguracji adresu IP można przypisać publiczny adres IP, ale nie jest to wymagane. Jeśli publiczny adres IP nie zostanie przypisany do maszyny wirtualnej przez skojarzenie zasobu publicznego adresu IP, maszyna wirtualna może nadal komunikować się z Internetem. W tym przypadku prywatny adres IP jest adresem sieci źródłowej przetłumaczonym przez platformę Azure do nieprzewidywalnego publicznego adresu IP. Aby dowiedzieć się więcej na temat zasobów publicznego adresu IP, zobacz [zasób publicznego adresu IP](virtual-network-public-ip-address.md).

Istnieją limity liczby prywatnych i publicznych adresów IP, które można przypisać do interfejsu sieciowego. Aby uzyskać szczegółowe informacje, przeczytaj artykuł dotyczący [limitów platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) .

> [!NOTE]
> Platforma Azure tłumaczy prywatny adres IP maszyny wirtualnej na publiczny adres IP. W związku z tym system operacyjny maszyny wirtualnej nie jest uważany za każdy przypisany do niego publiczny adres IP, dlatego nie trzeba ręcznie przypisywać publicznego adresu IP w ramach systemu operacyjnego.

## <a name="assignment-methods"></a>Metody przypisywania

Publiczne i prywatne adresy IP są przypisywane przy użyciu jednej z następujących metod przypisywania:

### <a name="dynamic"></a>Dynamiczny

Dynamiczne prywatne adresy IPv4 i IPv6 (opcjonalnie) są przypisywane domyślnie.

- **Tylko publiczne**: platforma Azure przypisuje adres z zakresu unikatowego dla każdego regionu platformy Azure. Aby dowiedzieć się, które zakresy są przypisane do każdego regionu, zobacz [Microsoft Azure zakresów adresów IP centrum](https://www.microsoft.com/download/details.aspx?id=41653)danych. Adres może ulec zmianie, gdy maszyna wirtualna jest zatrzymana (cofnięcie przydziału), a następnie ponownie uruchomiona. Nie można przypisać publicznego adresu IPv6 do konfiguracji protokołu IP przy użyciu metody przypisywania.
- **Tylko prywatne**: platforma Azure rezerwuje pierwsze cztery adresy w każdym zakresie adresów podsieci i nie przypisuje adresów. Platforma Azure przypisuje następny dostępny adres do zasobu z zakresu adresów podsieci. Na przykład jeśli zakres adresów podsieci to 10.0.0.0/16, a adresy 10.0.0.4-10.0.0.14 zostały już przypisane (. 0-.3 są zastrzeżone), platforma Azure przypisuje adres 10.0.0.15 do zasobu. Metoda dynamiczna to domyślna metoda alokacji. Po przypisaniu dynamiczne adresy IP są zwalniane, tylko jeśli interfejs sieciowy zostanie usunięty, przypisany do innej podsieci w tej samej sieci wirtualnej, lub metoda alokacji zostanie zmieniona na Statyczna i zostanie podany inny adres IP. Domyślnie platforma Azure przypisuje poprzedni adres dynamicznie przypisany jako adres statyczny po zmianie metody alokacji z dynamicznej na statyczną. 

### <a name="static"></a>Static

Możesz (opcjonalnie) przypisywać publiczny lub prywatny statyczny adres IPv4 lub IPv6 do konfiguracji adresu IP. Aby dowiedzieć się więcej o tym, jak platforma Azure przypisuje statyczne publiczne adresy IPv4, zobacz [publiczne adresy IP](virtual-network-public-ip-address.md).

- **Tylko publiczne**: platforma Azure przypisuje adres z zakresu unikatowego dla każdego regionu platformy Azure. Możesz pobrać listę zakresów (prefiksów) dla następujących chmur platformy Azure: [Publiczna](https://www.microsoft.com/download/details.aspx?id=56519), [Instytucje rządowe USA](https://www.microsoft.com/download/details.aspx?id=57063), [Chiny](https://www.microsoft.com/download/details.aspx?id=57062) i [Niemcy](https://www.microsoft.com/download/details.aspx?id=57064). Adres nie zmienia się, dopóki nie zostanie usunięty zasób publicznego adresu IP, do którego jest przypisany, lub metoda przypisania zostanie zmieniona na dynamiczną. Jeśli zasób publicznego adresu IP jest skojarzony z konfiguracją IP, przed zmianą jego metody przypisania należy usunąć jego skojarzenie z konfiguracji protokołu IP.
- **Tylko prywatne**: wybierasz i przypiszesz adres z zakresu adresów podsieci. Możesz przypisać dowolny adres z zakresu adresów podsieci, który nie jest jednym z pierwszych czterech adresów w zakresie adresów podsieci i nie jest aktualnie przypisany do żadnego innego zasobu w podsieci. Adresy statyczne są zwalniane tylko w przypadku usunięcia interfejsu sieciowego. Jeśli zmienisz metodę alokacji na statyczną, platforma Azure dynamicznie przypisze wcześniej przypisany dynamiczny adres IP jako adres statyczny, nawet jeśli adres nie jest następnym dostępnym adresem w zakresie adresów podsieci. Adres zmieni się także, jeśli interfejs sieciowy zostanie przypisany do innej podsieci w tej samej sieci wirtualnej, ale aby przypisać interfejs sieciowy do innej podsieci, musisz najpierw zmienić metodę alokacji ze statycznej na dynamiczną. Po przypisaniu interfejsu sieciowego do innej podsieci możesz zmienić metodę alokacji z powrotem na statyczną i przypisać adres IP z zakresu adresów nowej podsieci.

## <a name="ip-address-versions"></a>Wersje adresów IP

Podczas przypisywania adresów można określić następujące wersje:

### <a name="ipv4"></a>Protokół IPv4

Każdy interfejs sieciowy musi mieć jedną [podstawową](#primary) konfigurację adresu IP z przypisanym [prywatnym](#private) adresem [IPv4](#ipv4) . Można dodać jedną lub więcej [pomocniczych](#secondary) konfiguracji adresów IP, z których każdy ma prywatny adres IPv4, i (opcjonalnie), [publicznego](#public) adresu IP IPv4.

### <a name="ipv6"></a>Protokół IPv6

Można przypisać zero lub jeden prywatny adres [IPv6](#ipv6) do jednej dodatkowej konfiguracji adresu IP interfejsu sieciowego. Interfejs sieciowy nie może mieć żadnych istniejących konfiguracji pomocniczych adresów IP. Każdy interfejs sieciowy może mieć co najwyżej jeden prywatny adres IPv6. Opcjonalnie możesz dodać publiczny adres IPv6 do konfiguracji interfejsu sieciowego IPv6.

> [!NOTE]
> Chociaż można utworzyć interfejs sieciowy z adresem IPv6 przy użyciu portalu, nie można dodać istniejącego interfejsu sieciowego do nowej lub istniejącej maszyny wirtualnej przy użyciu portalu. Użyj programu PowerShell lub interfejsu wiersza polecenia platformy Azure, aby utworzyć interfejs sieciowy z prywatnym adresem IPv6, a następnie Dołącz interfejs sieciowy podczas tworzenia maszyny wirtualnej. Nie można dołączyć interfejsu sieciowego z przypisanym prywatnym adresem IPv6 do istniejącej maszyny wirtualnej. Nie można dodać prywatnego adresu IPv6 do konfiguracji protokołu IP dla dowolnego interfejsu sieciowego dołączonego do maszyny wirtualnej przy użyciu narzędzi (Portal, interfejsu wiersza polecenia lub programu PowerShell).

## <a name="skus"></a>Jednostki SKU

Publiczny adres IP jest tworzony przy użyciu podstawowej lub standardowej jednostki SKU. Aby uzyskać więcej informacji na temat różnic między jednostkami SKU, zobacz [Zarządzanie publicznymi adresami IP](virtual-network-public-ip-address.md).

> [!NOTE]
> Po przypisaniu standardowego publicznego adresu IP jednostki SKU do interfejsu sieciowego maszyny wirtualnej musisz jawnie zezwolić na ruch do miejsca przeznaczenia przy użyciu [sieciowej grupy zabezpieczeń](./network-security-groups-overview.md#network-security-groups). Próba komunikacji z zasobem będzie kończyć się niepowodzeniem do momentu utworzenia i skojarzenia sieciowej grupy zabezpieczeń, a następnie jawnego zezwolenia na żądany ruch.

## <a name="next-steps"></a>Następne kroki
Aby utworzyć maszynę wirtualną z różnymi konfiguracjami protokołu IP, zapoznaj się z następującymi artykułami:

|Zadanie|Narzędzie|
|---|---|
|Tworzenie maszyny wirtualnej z wieloma interfejsami sieciowymi|[Interfejs wiersza polecenia](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), program [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Tworzenie pojedynczej maszyny wirtualnej karty sieciowej z wieloma adresami IPv4|[Interfejs wiersza polecenia](virtual-network-multiple-ip-addresses-cli.md), program [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Utwórz pojedynczą maszynę wirtualną kart sieciowych z prywatnym adresem IPv6 (za Azure Load Balancer)|[Interfejs wiersza polecenia](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), program [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [szablon Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|