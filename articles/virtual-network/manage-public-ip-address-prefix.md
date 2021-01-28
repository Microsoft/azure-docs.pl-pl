---
title: Tworzenie, zmienianie lub usuwanie prefiksu publicznego adresu IP platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się więcej na temat prefiksów publicznych adresów IP i sposobu ich tworzenia, zmieniania lub usuwania. Zobacz, gdzie znaleźć dodatkowe informacje.
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
ms.openlocfilehash: 2e32faad698fbf316d51123cc8b7845a3b262c7f
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98938661"
---
# <a name="create-change-or-delete-a-public-ip-address-prefix"></a>Tworzenie, zmienianie i usuwanie prefiksu publicznego adresu IP

Dowiedz się więcej na temat prefiksu publicznego adresu IP oraz sposobu tworzenia, zmieniania i usuwania jednego z nich. Prefiks publicznego adresu IP to ciągły zakres adresów na podstawie liczby określonych publicznych adresów IP. Adresy są przypisywane do subskrypcji. Podczas tworzenia zasobu publicznego adresu IP można przypisać statyczny publiczny adres IP z prefiksu i skojarzyć go z maszynami wirtualnymi, modułami równoważenia obciążenia lub innymi zasobami, aby umożliwić łączność z Internetem. Jeśli nie znasz publicznych prefiksów adresów IP, zobacz temat [prefiks publicznego adresu IP — Omówienie](public-ip-address-prefix.md)

## <a name="before-you-begin"></a>Zanim rozpoczniesz

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przed wykonaniem kroków opisanych w sekcji tego artykułu wykonaj następujące zadania:

- Jeśli nie masz jeszcze konta platformy Azure, Utwórz [konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free).
- Jeśli używasz portalu, Otwórz https://portal.azure.com program i zaloguj się przy użyciu konta platformy Azure.
- W przypadku wykonywania zadań w tym artykule przy użyciu poleceń programu PowerShell uruchom polecenia w [Azure Cloud Shell](https://shell.azure.com/powershell)lub przez uruchomienie programu PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Ten samouczek wymaga modułu Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.
- W przypadku korzystania z poleceń interfejsu wiersza polecenia (CLI) platformy Azure w celu wykonania zadań w tym artykule Uruchom polecenia w [Azure Cloud Shell](https://shell.azure.com/bash)lub przez uruchomienie interfejsu wiersza polecenia na komputerze. Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.41 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, musisz też uruchomić polecenie `az login` , aby utworzyć połączenie z platformą Azure.

Konto, do którego należy się zalogować lub połączyć się z platformą Azure za pomocą programu, musi być przypisane do roli [współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , do której przypisano odpowiednie akcje wymienione w obszarze [uprawnienia](#permissions).

Prefiksy publicznych adresów IP są naliczane. Aby uzyskać szczegółowe informacje, zobacz [Cennik](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="create-a-public-ip-address-prefix"></a>Utwórz prefiks publicznego adresu IP

1. W lewym górnym rogu portalu wybierz pozycję **+ Utwórz zasób**.
2. Wprowadź *prefiks publiczny adres IP* w polu *Wyszukaj w portalu Marketplace* . Gdy w wynikach wyszukiwania pojawia się **prefiks publicznego adresu IP** , wybierz go.
3. W obszarze **prefiks publicznego adresu IP** wybierz pozycję **Utwórz**.
4. Wprowadź lub wybierz wartości dla następujących ustawień, w obszarze **Utwórz prefiks publicznego adresu IP**, a następnie wybierz pozycję **Utwórz**:

   |Ustawienie|Wymagane?|Szczegóły|
   |---|---|---|
   |Subskrypcja|Tak|Musi znajdować się w tej samej [subskrypcji](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) co zasób, do którego chcesz skojarzyć publiczny adres IP.|
   |Grupa zasobów|Tak|Może istnieć w tej samej lub innej [grupie zasobów](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) co zasób, do którego chcesz skojarzyć publiczny adres IP.|
   |Nazwa|Tak|Nazwa musi być unikatowa w ramach wybranej grupy zasobów.|
   |Region (Region)|Tak|Musi istnieć w tym samym [regionie](https://azure.microsoft.com/regions)co publiczne adresy IP, z którego będą przypisywane adresy z zakresu.|
   |Rozmiar prefiksu|Tak| Rozmiar wymaganego prefiksu. Domyślne adresy IP/28 lub 16.

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[AZ Network Public-IP prefix Create](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create)|
|PowerShell|[New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix)|

>[!NOTE]
>W regionach ze strefami dostępności można użyć poleceń programu PowerShell lub interfejsu wiersza polecenia do utworzenia prefiksu publicznego adresu IP jako: nienależącego do strefy, skojarzonego z określoną strefą, lub do użycia nadmiarowości stref.  W przypadku interfejsu API w wersji 2020-08-01 lub nowszej, jeśli parametr strefy nie zostanie podany, tworzony jest prefiks publicznego adresu IP bez stref. W przypadku wersji interfejsu API starszej niż 2020-08-01 tworzony jest nadmiarowy publiczny adres IP strefy. 

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Tworzenie statycznego publicznego adresu IP z prefiksu
Po utworzeniu prefiksu należy utworzyć statyczne adresy IP z prefiksu. Aby to zrobić, wykonaj czynności opisane poniżej.

1. W polu zawierającym *zasoby wyszukiwania* tekstu w górnej części Azure Portal wpisz *prefiks publicznego adresu IP*. Gdy **publiczne prefiksy adresów IP** są wyświetlane w wynikach wyszukiwania, wybierz je.
2. Wybierz prefiks, z którego chcesz utworzyć publiczne adresy IP.
3. Gdy pojawi się w wynikach wyszukiwania, wybierz ją, a następnie kliknij pozycję **+ Dodaj adres IP** w sekcji Przegląd.
4. Wprowadź lub wybierz wartości dla następujących ustawień w obszarze **Utwórz publiczny adres IP**. Ponieważ prefiks jest przeznaczony dla standardowej jednostki SKU, IPv4 i static, należy podać tylko następujące informacje:

   |Ustawienie|Wymagane?|Szczegóły|
   |---|---|---|
   |Nazwa|Tak|Nazwa publicznego adresu IP musi być unikatowa w ramach wybranej grupy zasobów.|
   |Limit czasu bezczynności (minuty)|Nie|Liczba minut utrzymywania otwartego połączenia TCP lub HTTP bez polegania na klientach do wysyłania komunikatów Keep-Alive. |
   |Etykieta nazwy DNS|Nie|Musi być unikatowa w ramach regionu platformy Azure, w którym jest tworzona nazwa (między wszystkimi subskrypcjami i wszystkimi klientami). Platforma Azure automatycznie rejestruje nazwę i adres IP w systemie DNS, aby można było połączyć się z zasobem o nazwie. System Azure dołącza domyślną podsieć, taką jak *Location.cloudapp.Azure.com* (lokalizacja jest wybieraną lokalizacją) do podania nazwy, aby utworzyć w pełni KWALIFIKOWANĄ nazwę DNS. Aby uzyskać więcej informacji, zobacz [używanie Azure DNS z publicznym adresem IP platformy Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|

Alternatywnie możesz użyć poleceń interfejsu wiersza polecenia i PS poniżej z parametrami--Public-IP-prefix (CLI) i-PublicIpPrefix (PS), aby utworzyć zasób publicznego adresu IP. 

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)|

## <a name="view-or-delete-a-prefix"></a>Wyświetlanie lub usuwanie prefiksu

1. W polu zawierającym *zasoby wyszukiwania* tekstu w górnej części Azure Portal wpisz *prefiks publicznego adresu IP*. Gdy **publiczne prefiksy adresów IP** są wyświetlane w wynikach wyszukiwania, wybierz je.
2. Wybierz nazwę publicznego prefiksu adresu IP, który chcesz wyświetlić, Zmień ustawienia dla lub Usuń z listy.
3. Wykonaj jedną z następujących czynności, w zależności od tego, czy chcesz wyświetlić, usunąć lub zmienić prefiks publicznego adresu IP.
   - **Widok**: sekcja **Przegląd** zawiera najważniejsze ustawienia dla prefiksu publicznego adresu IP, takie jak prefiks.
   - **Usuń**: Aby usunąć prefiks publicznego adresu IP, wybierz pozycję **Usuń** w sekcji **Przegląd** . Jeśli adresy w ramach prefiksu są skojarzone z zasobami publicznych adresów IP, należy najpierw usunąć zasoby publicznych adresów IP. Zobacz [usuwanie publicznego adresu IP](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address).

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[AZ Network Public-IP prefix list](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-list) , aby wyświetlić listę publicznych adresów IP, [AZ Network Public-IP prefix show](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-show) , aby wyświetlić ustawienia. [AZ Network Public-IP prefix Update](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-update) w celu aktualizacji; [AZ Network Public-IP prefix Delete](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-delete) do usunięcia|
|PowerShell|[Get-AzPublicIpPrefix](/powershell/module/az.network/get-azpublicipprefix) , aby pobrać obiekt publicznego adresu IP i wyświetlić jego ustawienia, [Ustaw polecenie Set-AzPublicIpPrefix](/powershell/module/az.network/set-azpublicipprefix) , aby zaktualizować ustawienia. [Remove-AzPublicIpPrefix](/powershell/module/az.network/remove-azpublicipprefix) do usunięcia|

## <a name="permissions"></a>Uprawnienia

Aby wykonać zadania dotyczące publicznych prefiksów adresów IP, Twoje konto musi być przypisane do roli [współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do roli [niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , do której przypisane są odpowiednie działania wymienione w poniższej tabeli:

| Akcja                                                            | Nazwa                                                           |
| ---------                                                         | -------------                                                  |
| Microsoft. Network/publicIPPrefixes/odczyt                           | Odczytaj prefiks publicznego adresu IP                                |
| Microsoft. Network/publicIPPrefixes/Write                          | Utwórz lub zaktualizuj prefiks publicznego adresu IP                    |
| Microsoft. Network/publicIPPrefixes/Delete                         | Usuwanie prefiksu publicznego adresu IP                              |
|Microsoft. Network/publicIPPrefixes/Join/Action                     | Utwórz publiczny adres IP z prefiksu |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat scenariuszy i korzyści wynikających z używania [publicznego prefiksu adresu IP](public-ip-address-prefix.md)
