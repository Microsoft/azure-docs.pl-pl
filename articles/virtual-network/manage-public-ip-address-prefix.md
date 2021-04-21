---
title: Tworzenie, zmienianie lub usuwanie prefiksu publicznego adresu IP platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się więcej na temat prefiksów publicznych adresów IP oraz sposobu ich tworzenia, zmieniania lub usuwania. Zobacz, gdzie można znaleźć dodatkowe informacje.
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/13/2019
ms.author: allensu
ms.openlocfilehash: 173fa3a8288ccceb07048e83fcec35d67b2fd35f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783435"
---
# <a name="create-change-or-delete-a-public-ip-address-prefix"></a>Tworzenie, zmienianie i usuwanie prefiksu publicznego adresu IP

Dowiedz się więcej na temat prefiksu publicznego adresu IP oraz sposobu jego tworzenia, zmieniania i usuwania. Prefiks publicznego adresu IP to ciągły zakres adresów oparty na liczbie podanych publicznych adresów IP. Adresy są przypisywane do subskrypcji. Podczas tworzenia zasobu publicznego adresu IP można przypisać statyczny publiczny adres IP z prefiksu i skojarzyć go z maszynami wirtualnymi, usługami równoważenia obciążenia lub innymi zasobami, aby umożliwić łączność z Internetem. Jeśli nie znasz prefiksów publicznych adresów IP, zobacz [Omówienie prefiksów publicznych adresów IP](public-ip-address-prefix.md)

## <a name="before-you-begin"></a>Zanim rozpoczniesz

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przed wykonaniem kroków w dowolnej sekcji tego artykułu wykonaj następujące zadania:

- Jeśli nie masz jeszcze konta platformy Azure, zarejestruj się, aby uzyskać bezpłatne [konto próbne.](https://azure.microsoft.com/free)
- Jeśli korzystasz z portalu, otwórz plik https://portal.azure.com i zaloguj się przy użyciu konta platformy Azure.
- Jeśli do wykonywania zadań w tym artykule używasz poleceń programu PowerShell, uruchom polecenia w Azure Cloud Shell [lub](https://shell.azure.com/powershell)uruchamiając program PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Ten samouczek wymaga modułu Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.
- Jeśli do wykonywania zadań w tym artykule używasz poleceń interfejsu wiersza polecenia platformy Azure, uruchom polecenia w witrynie [Azure Cloud Shell](https://shell.azure.com/bash)lub przez uruchomienie interfejsu wiersza polecenia na komputerze. Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.41 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, musisz również uruchomić narzędzie , aby `az login` utworzyć połączenie z platformą Azure.

Konto, na które się logujesz lub łączysz się [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) z platformą [](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure, musi być przypisane do roli współautora sieci lub roli niestandardowej, która ma przypisane odpowiednie akcje wymienione w Te [uprawnienia.](#permissions)

Za prefiksy publicznych adresów IP są naliczane opłaty. Aby uzyskać szczegółowe informacje, zobacz [cennik](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="create-a-public-ip-address-prefix"></a>Tworzenie prefiksu publicznego adresu IP

1. W lewym górnym rogu portalu wybierz pozycję **+ Utwórz zasób.**
2. Wprowadź *prefiks publicznego adresu IP* w polu Wyszukaj w *witrynie Marketplace.* Gdy **prefiks publicznego adresu IP** pojawi się w wynikach wyszukiwania, wybierz go.
3. W **obszarze Prefiks publicznego adresu IP** wybierz pozycję **Utwórz**.
4. Wprowadź lub wybierz wartości następujących ustawień w obszarze Utwórz **prefiks publicznego adresu IP,** a następnie wybierz pozycję **Utwórz**:

   |Ustawienie|Wymagane?|Szczegóły|
   |---|---|---|
   |Subskrypcja|Tak|Musi istnieć w tej samej [subskrypcji](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) co zasób, z którym chcesz skojarzyć publiczny adres IP.|
   |Grupa zasobów|Tak|Może istnieć w tej samej lub innej [grupie](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) zasobów co zasób, z którym chcesz skojarzyć publiczny adres IP.|
   |Nazwa|Tak|Nazwa musi być unikatowa w obrębie wybranej grupy zasobów.|
   |Region (Region)|Tak|Muszą istnieć w tym samym [regionie](https://azure.microsoft.com/regions)co publiczne adresy IP, do których zostaną przypisane adresy z zakresu.|
   |Rozmiar prefiksu|Tak| Rozmiar potrzebnego prefiksu. Adres IP /28 lub 16 jest wartością domyślną.

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az network public-ip prefix create](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_create)|
|PowerShell|[New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix)|

>[!NOTE]
>W regionach ze strefami dostępności można użyć poleceń programu PowerShell lub interfejsu wiersza polecenia, aby utworzyć prefiks publicznego adresu IP w następujący sposób: niezeonalny, skojarzony z określoną strefą lub w celu użycia nadmiarowości strefy.  W przypadku interfejsu API w wersji 2020-08-01 lub nowszej, jeśli parametr strefy nie zostanie podany, zostanie utworzony nie strefowy prefiks publicznego adresu IP. W przypadku wersji interfejsu API starszych niż 2020-08-01 jest tworzony prefiks strefowo nadmiarowego publicznego adresu IP. 

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Tworzenie statycznego publicznego adresu IP z prefiksu
Po utworzeniu prefiksu należy utworzyć statyczne adresy IP na jego stronie. Aby to zrobić, wykonaj poniższe kroki.

1. W polu zawierającym tekst *Wyszukaj* zasoby w górnej części okna Azure Portal wpisz prefiks *publicznego adresu IP*. Gdy **prefiksy publicznych adresów IP** pojawią się w wynikach wyszukiwania, wybierz go.
2. Wybierz prefiks, z którego chcesz utworzyć publiczne ip.
3. Gdy pojawi się w wynikach wyszukiwania, wybierz go i kliknij pozycję **+Dodaj adres IP** w sekcji Przegląd.
4. Wprowadź lub wybierz wartości następujących ustawień w obszarze **Utwórz publiczny adres IP.** Ponieważ prefiks jest dla standardowej wersji SKU, protokołu IPv4 i statycznego, wystarczy podać tylko następujące informacje:

   |Ustawienie|Wymagane?|Szczegóły|
   |---|---|---|
   |Nazwa|Tak|Nazwa publicznego adresu IP musi być unikatowa w obrębie wybranej grupy zasobów.|
   |Limit czasu bezczynności (minuty)|Nie|Ile minut ma być otwarte połączenie TCP lub HTTP bez konieczności wysyłania komunikatów keep-alive przez klientów. |
   |Etykieta nazwy DNS|Nie|Musi być unikatowa w obrębie regionu świadczenia usługi Azure, w ramach który tworzysz nazwę (dla wszystkich subskrypcji i wszystkich klientów). Platforma Azure automatycznie rejestruje nazwę i adres IP w systemie DNS, aby można było połączyć się z zasobem przy użyciu nazwy . Platforma Azure dołącza domyślną podsieć, taką *location.cloudapp.azure.com* (gdzie lokalizacja to wybierana lokalizacja) do podanej nazwy, aby utworzyć w pełni kwalifikowaną nazwę DNS. Aby uzyskać więcej informacji, zobacz Use Azure DNS with an Azure public IP address (Używanie usługi Azure DNS [publicznym adresem IP platformy Azure).](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address)|

Alternatywnie możesz użyć poniższych poleceń interfejsu wiersza polecenia i programu PS z parametrami --public-ip-prefix (CLI) i -PublicIpPrefix (PS), aby utworzyć zasób publicznego adresu IP. 

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)|

## <a name="view-or-delete-a-prefix"></a>Wyświetlanie lub usuwanie prefiksu

1. W polu zawierającym tekst *Wyszukaj* zasoby w górnej części okna Azure Portal wpisz prefiks *publicznego adresu IP*. Gdy **prefiksy publicznych adresów IP** pojawią się w wynikach wyszukiwania, wybierz go.
2. Wybierz nazwę prefiksu publicznego adresu IP, który chcesz wyświetlić, zmienić ustawienia lub usunąć z listy.
3. Wykonaj jedną z następujących opcji, w zależności od tego, czy chcesz wyświetlić, usunąć lub zmienić prefiks publicznego adresu IP.
   - **Widok:** sekcja **Przegląd** zawiera kluczowe ustawienia prefiksu publicznego adresu IP, takie jak prefiks.
   - **Usuń:** Aby usunąć prefiks publicznego adresu IP, wybierz pozycję **Usuń** w **sekcji** Przegląd. Jeśli adresy w prefiksie są skojarzone z zasobami publicznego adresu IP, należy najpierw usunąć zasoby publicznych adresów IP. Zobacz [usuwanie publicznego adresu IP.](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address)

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az network public-ip prefix list](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_list) to list public IP addresses, [az network public-ip prefix show](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_show) to show settings; [az network public-ip prefix update;](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_update) [az network public-ip prefix delete](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_delete) to delete|
|PowerShell|[Get-AzPublicIpPrefix](/powershell/module/az.network/get-azpublicipprefix) do pobierania obiektu publicznego adresu IP i wyświetlania jego ustawień, [Set-AzPublicIpPrefix,](/powershell/module/az.network/set-azpublicipprefix) aby zaktualizować ustawienia; [Remove-AzPublicIpPrefix do](/powershell/module/az.network/remove-azpublicipprefix) usunięcia|

## <a name="permissions"></a>Uprawnienia

Aby wykonać zadania dotyczące prefiksów publicznych adresów IP, [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) twoje konto musi [](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) być przypisane do roli współautora sieci lub roli niestandardowej, która ma przypisane odpowiednie akcje wymienione w poniższej tabeli:

| Akcja                                                            | Nazwa                                                           |
| ---------                                                         | -------------                                                  |
| Microsoft.Network/publicIPPrefixes/read                           | Odczytywanie prefiksu publicznego adresu IP                                |
| Microsoft.Network/publicIPPrefixes/write                          | Tworzenie lub aktualizowanie prefiksu publicznego adresu IP                    |
| Microsoft.Network/publicIPPrefixes/delete                         | Usuwanie prefiksu publicznego adresu IP                              |
|Microsoft.Network/publicIPPrefixes/join/action                     | Tworzenie publicznego adresu IP z prefiksu |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o scenariuszach i korzyściach wynikających z używania [prefiksu publicznego adresu IP](public-ip-address-prefix.md)
