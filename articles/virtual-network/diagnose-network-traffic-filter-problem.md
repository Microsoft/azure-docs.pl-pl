---
title: Diagnozowanie problemu z filtrowaniem ruchu sieciowego maszyny wirtualnej | Microsoft Docs
description: Dowiedz się, jak zdiagnozować problem z filtrowaniem ruchu sieciowego maszyny wirtualnej, wyświetlając obowiązujące reguły zabezpieczeń dla maszyny wirtualnej.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: a54feccf-0123-4e49-a743-eb8d0bdd1ebc
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2018
ms.author: kumud
ms.openlocfilehash: d6835d06015923a70301c95370c76efbd0c2163e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776739"
---
# <a name="diagnose-a-virtual-machine-network-traffic-filter-problem"></a>Diagnozowanie problemu z filtrowaniem ruchu sieciowego maszyny wirtualnej

Z tego artykułu dowiesz się, jak zdiagnozować problem z filtrowaniem ruchu sieciowego, wyświetlając reguły zabezpieczeń sieciowej grupy zabezpieczeń obowiązujące w przypadku maszyny wirtualnej.

NSG umożliwia kontrolowanie typów ruchu, który przepływa do i z maszyny wirtualnej. Sieciową chmurze można skojarzyć z podsiecią w sieci wirtualnej platformy Azure, interfejsem sieciowym dołączonym do maszyny wirtualnej lub obiema. Obowiązujące reguły zabezpieczeń stosowane do interfejsu sieciowego są agregacją reguł, które istnieją w sieciowej skojarzonej z interfejsem sieciowym i podsiecią, w których znajduje się interfejs sieciowy. Reguły w różnych sieciowych sieciowych sieciach czasami mogą ze sobą kolidować i wpływać na łączność sieciową maszyny wirtualnej. Możesz wyświetlić wszystkie obowiązujące reguły zabezpieczeń z sieciowych zasad zabezpieczeń, które są stosowane do interfejsów sieciowych maszyny wirtualnej. Jeśli nie znasz pojęć dotyczących sieci wirtualnej, interfejsu sieciowego lub [](virtual-network-network-interface.md)sieciowej grupy zabezpieczeń, zobacz Omówienie sieci wirtualnej, [](virtual-networks-overview.md)Interfejs sieciowy i Sieciowe grupy zabezpieczeń — [omówienie.](./network-security-groups-overview.md)

## <a name="scenario"></a>Scenariusz

Próbujesz nawiązać połączenie z maszyną wirtualną za pośrednictwem portu 80 z Internetu, ale połączenie kończy się niepowodzeniem. Aby ustalić, dlaczego nie możesz uzyskać dostępu do portu 80 z Internetu, możesz [](#diagnose-using-azure-portal)wyświetlić obowiązujące reguły zabezpieczeń dla interfejsu sieciowego przy użyciu witryny Azure Portal, programu [PowerShell](#diagnose-using-powershell)lub interfejsu wiersza [polecenia platformy Azure.](#diagnose-using-azure-cli)

W poniższych krokach założono, że masz istniejącą maszynę wirtualną do wyświetlania obowiązujących reguł zabezpieczeń. Jeśli nie masz istniejącej maszyny wirtualnej, najpierw wd wdrażaj maszynę wirtualną z systemem [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Windows,](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) aby wykonać zadania z tego artykułu. Przykłady w tym artykule są dostępne dla maszyny wirtualnej o nazwie *myVM* z interfejsem sieciowym *o nazwie myVMVMNic.* Maszyna wirtualna i interfejs sieciowy znajdują się w grupie zasobów o nazwie *myResourceGroup* i znajdują się w *regionie Wschodnie usa.* Zmień wartości w odpowiednich krokach dla maszyny wirtualnej, dla których jest diagnozowanie problemu.

## <a name="diagnose-using-azure-portal"></a>Diagnozowanie przy użyciu Azure Portal

1. Zaloguj się do witryny Azure [Portal](https://portal.azure.com) przy użyciu konta platformy Azure z [niezbędnymi uprawnieniami.](virtual-network-network-interface.md#permissions)
2. W górnej części Azure Portal wprowadź nazwę maszyny wirtualnej w polu wyszukiwania. Gdy nazwa maszyny wirtualnej pojawi się w wynikach wyszukiwania, wybierz ją.
3. W **obszarze USTAWIENIA** wybierz pozycję **Sieć,** jak pokazano na poniższej ilustracji:

   ![Zrzut ekranu przedstawia Azure Portal z ustawieniami sieci dla karty sieciowej V M V M.](./media/diagnose-network-traffic-filter-problem/view-security-rules.png)

   Reguły wymienione na poprzedniej ilustracji są dla interfejsu sieciowego o nazwie **myVMVMNic.** Zobaczysz, że istnieją reguły **PORTÓW DLA RUCHU PRZYCHODZĄCEGO** dla interfejsu sieciowego z dwóch różnych sieciowych grup zabezpieczeń:
   
   - **mySubnetNSG:** skojarzone z podsiecią, w których znajduje się interfejs sieciowy.
   - **myVMNSG:** Skojarzona z interfejsem sieciowym na maszynie wirtualnej o **nazwie myVMVMNic.**

   Reguła o nazwie **DenyAllInBound** uniemożliwia komunikację przychodzących z maszyną wirtualną przez port 80 z Internetu, zgodnie z opisem w [scenariuszu](#scenario). Reguła wyświetla *listę 0.0.0.0/0* dla **źródła**, które obejmuje Internet. Żadna inna reguła o wyższym priorytecie (niższym numerze) nie zezwala na ruch przychodzący na porcie 80. Aby zezwolić na ruch przychodzący na porcie 80 do maszyny wirtualnej z Internetu, zobacz [Rozwiązywanie problemu.](#resolve-a-problem) Aby dowiedzieć się więcej na temat reguł zabezpieczeń i sposobu, w jaki platforma Azure je stosuje, zobacz [Sieciowe grupy zabezpieczeń](./network-security-groups-overview.md).

   W dolnej części obrazu zobaczysz również reguły PORTÓW **WYCHODZĄCYCH**. W tym obszarze znajdują się reguły portów wychodzących dla interfejsu sieciowego. Chociaż na ilustracji przedstawiono tylko cztery reguły ruchu przychodzącego dla każdej sieciowej sieci, może to być o wiele więcej niż cztery reguły. Na ilustracji zobaczysz sieć **wirtualną w** obszarze **SOURCE** i **DESTINATION** oraz **AzureLoadBalancer** w **obszarze SOURCE**. **VirtualNetwork** i **AzureLoadBalancer** to [tagi usługi](./network-security-groups-overview.md#service-tags). Tagi usługi reprezentują grupę prefiksów adresów IP, aby zminimalizować złożoność tworzenia reguł zabezpieczeń.

4. Upewnij się, że maszyna wirtualna jest w stanie uruchomienia, a następnie wybierz pozycję Obowiązujące reguły zabezpieczeń **,** jak pokazano na poprzedniej ilustracji, aby wyświetlić obowiązujące reguły zabezpieczeń, jak pokazano na poniższej ilustracji:

   ![Zrzut ekranu przedstawia okienko Obowiązujące reguły zabezpieczeń z wybranymi opcjami Pobierz i ogólną regułą ruchu przychodzącego allowAzureLoadBalancerInbound.](./media/diagnose-network-traffic-filter-problem/view-effective-security-rules.png)

   Wymienione reguły są takie same jak w kroku 3, chociaż istnieją różne karty sieciowej skojarzonej z interfejsem sieciowym i podsiecią. Jak widać na ilustracji, wyświetlane jest tylko 50 pierwszych reguł. Aby pobrać plik CSV zawierający wszystkie reguły, wybierz pozycję **Pobierz**.

   Aby zobaczyć, które prefiksy reprezentuje każdy tag usługi, wybierz regułę, taką jak reguła o nazwie **AllowAzureLoadBalancerInbound.** Na poniższej ilustracji przedstawiono prefiksy tagu **usługi AzureLoadBalancer:**

   ![Zrzut ekranu przedstawia prefiksy adresów dla wprowadzonego elementu AllowAzureLoadBalancerInbound.](./media/diagnose-network-traffic-filter-problem/address-prefixes.png)

   Chociaż tag **usługi AzureLoadBalancer** reprezentuje tylko jeden prefiks, inne tagi usługi reprezentują kilka prefiksów.

5. W poprzednich krokach pokazaliśmy reguły zabezpieczeń dla interfejsu sieciowego o nazwie **myVMVMNic,** ale w niektórych poprzednich obrazach widzieliśmy również interfejs sieciowy o nazwie **myVMVMNic2.** Do maszyny wirtualnej w tym przykładzie są dołączone dwa interfejsy sieciowe. Obowiązujące reguły zabezpieczeń mogą różnić się dla każdego interfejsu sieciowego.

   Aby wyświetlić reguły dla interfejsu **sieciowego myVMVMNic2,** wybierz go. Jak pokazano na ilustracji poniżej, interfejs sieciowy ma te same reguły skojarzone z jego podsiecią co interfejs sieciowy **myVMVMNic,** ponieważ oba interfejsy sieciowe znajdują się w tej samej podsieci. W przypadku skojarzenia sieciowej z podsiecią jej reguły są stosowane do wszystkich interfejsów sieciowych w podsieci.

   ![Zrzut ekranu przedstawia Azure Portal z ustawieniami sieci dla mojej karty sieciowej V M V M 2.](./media/diagnose-network-traffic-filter-problem/view-security-rules2.png)

   W przeciwieństwie do interfejsu sieciowego **myVMVMNic** interfejs sieciowy **myVMVMNic2** nie ma skojarzonej sieciowej grupy zabezpieczeń. Z każdym interfejsem sieciowym i podsiecią może być skojarzona żadna lub jedna sieciowa sieciowa skojarzona. Sieciowa skojarzona z każdym interfejsem sieciowym lub podsiecią może być taka sama lub inna. Tę samą sieciową grupę zabezpieczeń można skojarzyć z tylu interfejsów sieciowych i podsieci, ile chcesz.

Mimo że obowiązujące reguły zabezpieczeń były przeglądane za pośrednictwem maszyny wirtualnej, można również wyświetlać obowiązujące reguły zabezpieczeń za pośrednictwem osoby:
- **Interfejs sieciowy:** dowiedz się, jak [wyświetlić interfejs sieciowy.](virtual-network-network-interface.md#view-network-interface-settings)
- **Sieciowa żadna z sieciowych** sieci: dowiedz [się, jak wyświetlić sieciowa nr 1.](manage-network-security-group.md#view-details-of-a-network-security-group)

## <a name="diagnose-using-powershell"></a>Diagnozowanie przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Możesz uruchomić polecenia, które należy wykonać w [Azure Cloud Shell](https://shell.azure.com/powershell), lub uruchamiając program PowerShell z komputera. Interfejs Azure Cloud Shell jest bezpłatną interaktywną powłoką. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Jeśli uruchamiasz program PowerShell z komputera, potrzebujesz modułu Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom `Get-Module -ListAvailable Az` program na komputerze, aby znaleźć zainstalowaną wersję. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz również uruchomić narzędzie , aby zalogować się na platformie Azure przy użyciu konta z `Connect-AzAccount` [niezbędnymi uprawnieniami](virtual-network-network-interface.md#permissions).

Pobierz obowiązujące reguły zabezpieczeń dla interfejsu sieciowego za pomocą [get-AzEffectiveNetworkSecurityGroup.](/powershell/module/az.network/get-azeffectivenetworksecuritygroup) Poniższy przykład pobiera obowiązujące reguły zabezpieczeń dla interfejsu sieciowego o nazwie *myVMVMNic,* który znajduje się w grupie zasobów *o nazwie myResourceGroup:*

```azurepowershell-interactive
Get-AzEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVMVMNic `
  -ResourceGroupName myResourceGroup
```

Dane wyjściowe są zwracane w formacie JSON. Aby zrozumieć dane wyjściowe, zobacz [interpretowanie danych wyjściowych polecenia](#interpret-command-output).
Dane wyjściowe są zwracane tylko wtedy, gdy z interfejsem sieciowym jest skojarzona sieciowa sieć, podsieć, w których znajduje się interfejs sieciowy, lub obie te sieci. Maszyna wirtualna musi być w stanie uruchomienia. Maszyna wirtualna może mieć wiele interfejsów sieciowych z zastosowanymi różnymi sieciową sieciową gs. Podczas rozwiązywania problemów uruchom polecenie dla każdego interfejsu sieciowego.

Jeśli nadal masz problem z łącznością, zapoznaj się z [dodatkową diagnostyką](#additional-diagnosis) [i zagadnieniami.](#considerations)

Jeśli nie znasz nazwy interfejsu sieciowego, ale nie wiesz, do czego jest dołączony interfejs sieciowy, następujące polecenia zwracają identyfikatory wszystkich interfejsów sieciowych dołączonych do maszyny wirtualnej:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Otrzymasz dane wyjściowe podobne do następujących:

```output
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

W poprzednich danych wyjściowych nazwa interfejsu sieciowego to *myVMVMNic.*

## <a name="diagnose-using-azure-cli"></a>Diagnozowanie przy użyciu interfejsu wiersza polecenia platformy Azure

Jeśli do wykonywania zadań w tym artykule używasz poleceń interfejsu wiersza polecenia platformy Azure, uruchom polecenia w witrynie [Azure Cloud Shell](https://shell.azure.com/bash)lub przez uruchomienie interfejsu wiersza polecenia na komputerze. Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.32 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, musisz również uruchomić narzędzie i zalogować się na platformie Azure przy użyciu konta z `az login` [niezbędnymi uprawnieniami.](virtual-network-network-interface.md#permissions)

Pobierz obowiązujące reguły zabezpieczeń dla interfejsu sieciowego za pomocą [az network nic list-effective-nsg](/cli/azure/network/nic#az_network_nic_list_effective_nsg). Poniższy przykład pobiera obowiązujące reguły zabezpieczeń dla interfejsu sieciowego o nazwie *myVMVMNic,* który znajduje się w grupie zasobów *o nazwie myResourceGroup:*

```azurecli-interactive
az network nic list-effective-nsg \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

Dane wyjściowe są zwracane w formacie JSON. Aby zrozumieć dane wyjściowe, zobacz [interpretowanie danych wyjściowych polecenia](#interpret-command-output).
Dane wyjściowe są zwracane tylko wtedy, gdy sieciowa żadna z sieciowych sieciowych sieci jest skojarzona z interfejsem sieciowym, podsieć, w których znajduje się interfejs sieciowy, lub obie te sieci. Maszyna wirtualna musi być w stanie uruchomienia. Maszyna wirtualna może mieć wiele interfejsów sieciowych z zastosowanymi różnymi sieciową sieciową gs. Podczas rozwiązywania problemów uruchom polecenie dla każdego interfejsu sieciowego.

Jeśli nadal masz problem z łącznością, zapoznaj się z [dodatkową diagnostyką](#additional-diagnosis) [i zagadnieniami.](#considerations)

Jeśli nie znasz nazwy interfejsu sieciowego, ale nie wiesz, do czego jest dołączony interfejs sieciowy, następujące polecenia zwracają identyfikatory wszystkich interfejsów sieciowych dołączonych do maszyny wirtualnej:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

W zwróconych danych wyjściowych zobaczysz informacje podobne do następujących:

```output
"networkProfile": {
    "additionalProperties": {},
    "networkInterfaces": [
      {
        "additionalProperties": {},
        "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
        "primary": true,
        "resourceGroup": "myResourceGroup"
      },
```

W poprzednich danych wyjściowych nazwą interfejsu sieciowego jest *interfejs myVMVMNic.*

## <a name="interpret-command-output"></a>Interpretowanie danych wyjściowych polecenia

Niezależnie od tego, czy do zdiagnozowania problemu został użyty program [PowerShell,](#diagnose-using-powershell)czy interfejs wiersza polecenia platformy [Azure,](#diagnose-using-azure-cli) otrzymasz dane wyjściowe zawierające następujące informacje:

- **NetworkSecurityGroup:** identyfikator sieciowej grupy zabezpieczeń.
- Skojarzenie: określa, czy sieciowa grupa zabezpieczeń jest skojarzona z *sieciąInterface,* *czy podsiecią*. Jeśli sieciowa grupa sieciowa jest skojarzona z obiema sieciami, dane wyjściowe są zwracane z elementami **NetworkSecurityGroup,** **Association** i **EffectiveSecurityRules** dla każdej sieciowej grupy roboczej. Jeśli sieciowa skojarzenie jest skojarzona lub nieskojarzona bezpośrednio przed uruchomieniem polecenia w celu wyświetlenia obowiązujących reguł zabezpieczeń, może być konieczne odczekaj kilka sekund, aż zmiana zostanie odzwierciedlona w danych wyjściowych polecenia.
- **EffectiveSecurityRules:** Objaśnienie każdej właściwości jest szczegółowo opisane w części [Tworzenie reguły zabezpieczeń](manage-network-security-group.md#create-a-security-rule). Nazwy reguł poprzedzone *właściwością defaultSecurityRules/* są domyślnymi regułami zabezpieczeń, które istnieją w każdej sieciowej grupy zabezpieczeń. Nazwy reguł poprzedzone *regułami securityRules/* to utworzone reguły. Reguły określające [tag](./network-security-groups-overview.md#service-tags)usługi , taki jak **Internet,** **VirtualNetwork** i **AzureLoadBalancer** dla właściwości **destinationAddressPrefix** lub **sourceAddressPrefix,** również mają wartości właściwości **expandedDestinationAddressPrefix.** Właściwość **expandedDestinationAddressPrefix** wyświetla wszystkie prefiksy adresów reprezentowane przez tag usługi.

Jeśli w danych wyjściowych są wyświetlane zduplikowane reguły, jest to spowodowane tym, że sieciowa skojarzona jest zarówno z interfejsem sieciowym, jak i podsiecią. Obie sieciowe grupy zasad grupy mają te same reguły domyślne i mogą mieć dodatkowe zduplikowane reguły, jeśli zostały utworzone własne reguły, które są takie same w obu sieciowych grupych zasad grupy.

Reguła o nazwie **defaultSecurityRules/DenyAllInBound** uniemożliwia komunikację przychodzącymi z Internetu do maszyny wirtualnej za pośrednictwem portu 80, zgodnie z opisem w [scenariuszu](#scenario). Żadna inna reguła o wyższym priorytecie (niższym numerze) nie zezwala na ruch przychodzący z Internetu przez port 80.

## <a name="resolve-a-problem"></a>Rozwiązywanie problemu

Niezależnie od tego, czy do zdiagnozowania problemu przedstawionego w [](#scenario) tym artykule używasz witryny Azure Portal, programu [PowerShell](#diagnose-using-powershell)lub interfejsu wiersza polecenia platformy [Azure,](#diagnose-using-azure-cli) rozwiązaniem jest utworzenie reguły zabezpieczeń sieci z następującymi właściwościami: [](#diagnose-using-azure-portal)

| Właściwość                | Wartość                                                                              |
|---------                |---------                                                                           |
| Źródło                  | Dowolne                                                                                |
| Zakresy portów źródłowych      | Dowolne                                                                                |
| Element docelowy             | Adres IP maszyny wirtualnej, zakres adresów IP lub wszystkie adresy w podsieci. |
| Zakresy portów docelowych | 80                                                                                 |
| Protokół                | TCP                                                                                |
| Akcja                  | Zezwalaj                                                                              |
| Priorytet                | 100                                                                                |
| Nazwa                    | Allow-HTTP-All                                                                     |

Po utworzeniu reguły port 80 jest dozwolony dla ruchu przychodzącego z Internetu, ponieważ priorytet reguły jest wyższy niż domyślna reguła zabezpieczeń o nazwie *DenyAllInBound,* która nie zezwala na ruch. Dowiedz się, [jak utworzyć regułę zabezpieczeń](manage-network-security-group.md#create-a-security-rule). Jeśli różne sieciowe grupy sieciowe są skojarzone zarówno z interfejsem sieciowym, jak i podsiecią, należy utworzyć tę samą regułę w obu sieciowych grupyach zarządzania.

Gdy platforma Azure przetwarza ruch przychodzący, przetwarza reguły w sieciowej skojarzonej z podsiecią (jeśli istnieje skojarzona sieciowa sieciowa grupa zasobów), a następnie przetwarza reguły w sieciowej organizacji sieciowej skojarzonej z interfejsem sieciowym. Jeśli z interfejsem sieciowym i podsiecią jest skojarzona sieciowa sieciowa skojarzona sieć, port musi być otwarty w obu sieciowych sieciach, aby ruch docierał do maszyny wirtualnej. Aby ułatwić problemy z administracją i komunikacją, zalecamy skojarzenie sieciowej sieci z podsiecią, a nie z poszczególnymi interfejsami sieciowym. Jeśli maszyny wirtualne w podsieci wymagają różnych reguł zabezpieczeń, można ustawić interfejsy sieciowe jako elementy członkowskie grupy zabezpieczeń aplikacji i określić grupę zabezpieczeń aplikacji jako źródło i obiekt docelowy reguły zabezpieczeń. Dowiedz się więcej [o grupach zabezpieczeń aplikacji.](./network-security-groups-overview.md#application-security-groups)

Jeśli nadal występują problemy z komunikacją, zobacz [Zagadnienia i](#considerations) dodatkowa diagnostyka.

## <a name="considerations"></a>Zagadnienia do rozważenia

Podczas rozwiązywania problemów z łącznością należy wziąć pod uwagę następujące kwestie:

* Domyślne reguły zabezpieczeń blokują dostęp przychodzący z Internetu i zezwalają tylko na ruch przychodzący z sieci wirtualnej. Aby zezwolić na ruch przychodzący z Internetu, dodaj reguły zabezpieczeń o wyższym priorytecie niż reguły domyślne. Dowiedz się więcej na [temat domyślnych reguł zabezpieczeń](./network-security-groups-overview.md#default-security-rules)lub sposobu [dodawania reguły zabezpieczeń.](manage-network-security-group.md#create-a-security-rule)
* Jeśli masz wirtualne sieci równorzędne, domyślnie **tag usługi VIRTUAL_NETWORK** jest automatycznie rozwijany w celu dołączyć prefiksy dla równorzędnych sieci wirtualnych. Aby rozwiązać problemy związane z wirtualną siecią równorzędną, możesz wyświetlić prefiksy na liście **ExpandedAddressPrefix.** Dowiedz się więcej o [komunikacji równorzędnej sieci wirtualnej](virtual-network-peering-overview.md) [i tagach usługi.](./network-security-groups-overview.md#service-tags)
* Obowiązujące reguły zabezpieczeń są wyświetlane tylko dla interfejsu sieciowego, jeśli istnieje sieciowa skojarzona z interfejsem sieciowym maszyny wirtualnej i podsieć lub, oraz jeśli maszyna wirtualna jest w stanie uruchomienia.
* Jeśli z interfejsem sieciowym lub podsiecią nie są skojarzone żadne sieciowe grupy sieciowe i masz publiczny adres [IP](virtual-network-public-ip-address.md) przypisany do maszyny wirtualnej, wszystkie porty są otwarte dla dostępu przychodzącego z i wychodzącego do dowolnego miejsca. Jeśli maszyna wirtualna ma publiczny adres IP, zalecamy zastosowanie sieciowej organizacji sieciowej do podsieci interfejsu sieciowego.

## <a name="additional-diagnosis"></a>Dodatkowa diagnostyka

* Aby uruchomić szybki test w celu określenia, czy ruch jest dozwolony do lub z maszyny wirtualnej, użyj funkcji weryfikowania przepływu adresów [IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) usługi Azure Network Watcher. Weryfikacja przepływu adresów IP informuje o tym, czy ruch jest dozwolony, czy odrzucony. W przypadku odmowy weryfikacja przepływu adresów IP informuje o tym, która reguła zabezpieczeń odrzuca ruch.
* Jeśli nie ma reguł zabezpieczeń powodujących niepowodzenie łączności sieciowej maszyny wirtualnej, problem może być spowodowany:
  * Oprogramowanie zapory działające w systemie operacyjnym maszyny wirtualnej
  * Trasy skonfigurowane dla urządzeń wirtualnych lub ruchu lokalnego. Ruch internetowy można przekierować do sieci lokalnej za pośrednictwem [wymuszonego tunelowania.](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Jeśli wymusisz tunelowanie ruchu internetowego do urządzenia wirtualnego lub lokalnego, połączenie z maszyną wirtualną z Internetu może nie być możliwe. Aby dowiedzieć się, jak diagnozować problemy z trasą, które mogą utrudniać przepływ ruchu z maszyny wirtualnej, zobacz Diagnozowanie problemu z routingiem ruchu [sieciowego maszyny wirtualnej](diagnose-network-routing-problem.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o wszystkich zadaniach, właściwościach i ustawieniach sieciowej [grupy zabezpieczeń i](manage-network-security-group.md#work-with-network-security-groups) reguł [zabezpieczeń.](manage-network-security-group.md#work-with-security-rules)
- Dowiedz się więcej [na temat domyślnych reguł zabezpieczeń,](./network-security-groups-overview.md#default-security-rules) [tagów usług](./network-security-groups-overview.md#service-tags)i [sposobu,](./network-security-groups-overview.md#network-security-groups) w jaki platforma Azure przetwarza reguły zabezpieczeń dla ruchu przychodzącego i wychodzącego dla maszyny wirtualnej.