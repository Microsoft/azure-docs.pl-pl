---
title: Diagnozowanie problemu z filtrem ruchu sieciowego maszyny wirtualnej | Microsoft Docs
description: Dowiedz się, jak zdiagnozować problem z filtrem ruchu sieciowego maszyny wirtualnej, wyświetlając obowiązujące reguły zabezpieczeń dla maszyny wirtualnej.
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
ms.openlocfilehash: 73562d8d32f265fa43ca80d2f8d4f84b1b631ec6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98223673"
---
# <a name="diagnose-a-virtual-machine-network-traffic-filter-problem"></a>Diagnozowanie problemu z filtrem ruchu sieciowego maszyny wirtualnej

W tym artykule dowiesz się, jak zdiagnozować problem z filtrem ruchu sieciowego, wyświetlając reguły zabezpieczeń grupy zabezpieczeń sieci (sieciowej grupy zabezpieczeń), które obowiązują dla maszyny wirtualnej.

Sieciowych grup zabezpieczeń umożliwiają kontrolowanie typów ruchu, który przepływa do i z maszyny wirtualnej. Można skojarzyć sieciowej grupy zabezpieczeń z podsiecią w sieci wirtualnej platformy Azure, interfejsem sieciowym podłączonym do maszyny wirtualnej lub z obu tych systemów. Obowiązujące reguły zabezpieczeń stosowane do interfejsu sieciowego są agregacją reguł istniejących w sieciowej grupy zabezpieczeń skojarzonym z interfejsem sieciowym i podsiecią, w której znajduje się interfejs sieciowy. Reguły w różnych sieciowych grup zabezpieczeń mogą czasami powodować konflikty i mieć wpływ na łączność sieciową maszyny wirtualnej. Można wyświetlić wszystkie obowiązujące reguły zabezpieczeń z sieciowych grup zabezpieczeń, które są stosowane do interfejsów sieciowych maszyny wirtualnej. Jeśli nie znasz pojęć dotyczących sieci wirtualnych, interfejsów sieciowych lub sieciowej grupy zabezpieczeń, zobacz Omówienie usługi [Virtual Network](virtual-networks-overview.md), [interfejs sieciowy](virtual-network-network-interface.md)i [sieciowe grupy zabezpieczeń](./network-security-groups-overview.md).

## <a name="scenario"></a>Scenariusz

Podjęto próbę nawiązania połączenia z maszyną wirtualną przez port 80 z Internetu, ale połączenie nie powiedzie się. Aby określić, dlaczego nie można uzyskać dostępu do portu 80 z Internetu, można wyświetlić obowiązujące reguły zabezpieczeń dla interfejsu sieciowego przy użyciu witryny Azure [Portal](#diagnose-using-azure-portal), [programu PowerShell](#diagnose-using-powershell)lub [interfejsu wiersza polecenia platformy Azure](#diagnose-using-azure-cli).

W poniższej procedurze przyjęto założenie, że masz istniejącą maszynę wirtualną do wyświetlania obowiązujących reguł zabezpieczeń. Jeśli nie masz istniejącej maszyny wirtualnej, najpierw Wdróż maszynę wirtualną z systemem [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , aby wykonać zadania z tego artykułu. Przykłady w tym artykule dotyczą maszyny wirtualnej o nazwie *myVM* z interfejsem sieciowym o nazwie *myVMVMNic*. Maszyna wirtualna i interfejs sieciowy znajdują się w grupie zasobów o nazwie Moja *zasobów* i znajdują się w regionie *Wschodnie stany USA* . W razie potrzeby zmień wartości w odpowiednich krokach dla maszyny wirtualnej, dla której jest diagnozowany problem.

## <a name="diagnose-using-azure-portal"></a>Diagnozuj przy użyciu Azure Portal

1. Zaloguj się do witryny Azure [Portal](https://portal.azure.com) przy użyciu konta platformy Azure, które ma [wymagane uprawnienia](virtual-network-network-interface.md#permissions).
2. W górnej części Azure Portal wprowadź nazwę maszyny wirtualnej w polu wyszukiwania. Gdy nazwa maszyny wirtualnej zostanie wyświetlona w wynikach wyszukiwania, wybierz ją.
3. W obszarze **Ustawienia** wybierz pozycję **Sieć**, jak pokazano na poniższej ilustracji:

   ![Zrzut ekranu przedstawia Azure Portal z ustawieniami sieci dla mojej karty sieciowej z funkcją V M V.](./media/diagnose-network-traffic-filter-problem/view-security-rules.png)

   Reguły widoczne na liście na poprzednim obrazie dotyczą interfejsu sieciowego o nazwie **myVMVMNic**. Zobaczysz, że dla interfejsu sieciowego istnieją **reguły portów ruchu przychodzącego** z dwóch różnych sieciowych grup zabezpieczeń:
   
   - **mySubnetNSG**: skojarzona z podsiecią, w której znajduje się interfejs sieciowy.
   - **myVMNSG**: skojarzona z interfejsem sieciowym w maszynie wirtualnej o nazwie **myVMVMNic**.

   Reguła o nazwie **DenyAllInBound** polega na tym, że zapobieganie komunikacji przychodzącej z maszyną wirtualną przez port 80 z Internetu, zgodnie z opisem w tym [scenariuszu](#scenario). Reguła zawiera listę wartości *0.0.0.0/0* dla **źródła**, w tym Internet. Żadna inna reguła o wyższym priorytecie (Dolna liczba) zezwala na ruch przychodzący do portu 80. Aby zezwolić na ruch przychodzący portu 80 do maszyny wirtualnej z Internetu, zobacz temat [Rozwiązywanie problemu](#resolve-a-problem). Aby dowiedzieć się więcej o regułach zabezpieczeń i sposobach ich stosowania przez platformę Azure, zobacz [Network Security Groups](./network-security-groups-overview.md).

   W dolnej części obrazu widoczne są także **reguły portów wychodzących**. W obszarze są to reguły portów wychodzących dla interfejsu sieciowego. Chociaż obraz zawiera tylko cztery reguły ruchu przychodzącego dla każdego sieciowej grupy ZABEZPIECZEŃu, sieciowych grup zabezpieczeń może mieć wiele więcej niż czterech reguł. Na obrazie zobaczysz **VirtualNetwork** w obszarze **źródłowym** i **docelowym** oraz **AzureLoadBalancer** w obszarze **Źródło**. **VirtualNetwork** i **AzureLoadBalancer** to [Tagi usług](./network-security-groups-overview.md#service-tags). Tagi usług reprezentują grupę prefiksów adresów IP, aby zminimalizować złożoność tworzenia reguł zabezpieczeń.

4. Upewnij się, że maszyna wirtualna jest w stanie uruchomienia, a następnie wybierz pozycję **obowiązujące reguły zabezpieczeń**, jak pokazano na poprzedniej ilustracji, aby wyświetlić obowiązujące reguły zabezpieczeń, pokazane na poniższej ilustracji:

   ![Zrzut ekranu przedstawia okienko aktywne reguły zabezpieczeń z wybraną pozycją Pobierz i AllowAzureLoadBalancerInbound ruchu przychodzącego.](./media/diagnose-network-traffic-filter-problem/view-effective-security-rules.png)

   Wymienione reguły są takie same, jak w kroku 3, chociaż istnieją różne karty dla sieciowej grupy zabezpieczeń skojarzonych z interfejsem sieciowym i podsiecią. Jak widać na zdjęciu, wyświetlane są tylko pierwsze reguły 50. Aby pobrać plik CSV, który zawiera wszystkie reguły, wybierz pozycję **Pobierz**.

   Aby zobaczyć, które prefiksy są reprezentowane przez każdy tag usługi, wybierz regułę, taką jak reguła o nazwie **AllowAzureLoadBalancerInbound**. Na poniższej ilustracji przedstawiono prefiksy dla tagu usługi **AzureLoadBalancer** :

   ![Zrzut ekranu przedstawia prefiksy adresów dla AllowAzureLoadBalancerInbound wprowadzone.](./media/diagnose-network-traffic-filter-problem/address-prefixes.png)

   Chociaż tag usługi **AzureLoadBalancer** reprezentuje tylko jeden prefiks, inne Tagi usług reprezentują kilka prefiksów.

5. Poprzednie kroki wykazały reguły zabezpieczeń dla interfejsu sieciowego o nazwie **myVMVMNic**, ale w niektórych z poprzednich zdjęć pojawił się również interfejs sieciowy o nazwie **myVMVMNic2** . Do maszyny wirtualnej w tym przykładzie są dołączone dwa interfejsy sieciowe. Obowiązujące reguły zabezpieczeń mogą być różne dla każdego interfejsu sieciowego.

   Aby wyświetlić reguły dla interfejsu sieciowego **myVMVMNic2** , zaznacz go. Jak pokazano na poniższej ilustracji, interfejs sieciowy ma te same reguły, które są skojarzone z podsiecią, jako interfejs sieciowy **myVMVMNic** , ponieważ oba interfejsy sieciowe znajdują się w tej samej podsieci. Po skojarzeniu sieciowej grupy zabezpieczeń z podsiecią reguły są stosowane do wszystkich interfejsów sieciowych w podsieci.

   ![Zrzut ekranu przedstawia Azure Portal z ustawieniami sieci dla My V m V M karta sieciowa 2.](./media/diagnose-network-traffic-filter-problem/view-security-rules2.png)

   W przeciwieństwie do interfejsu sieciowego **myVMVMNic** , interfejs sieciowy **myVMVMNic2** nie ma skojarzonej sieciowej grupy zabezpieczeń. Każdy interfejs sieciowy i podsieć mogą mieć sieciowej grupy zabezpieczeń, lub jeden z nich. SIECIOWEJ grupy zabezpieczeń skojarzone z poszczególnymi interfejsami sieciowymi lub podsiecią może być taka sama lub inna. Tę samą grupę zabezpieczeń sieci można skojarzyć z dowolną liczbą interfejsów sieciowych i podsieci.

Mimo że skuteczne reguły zabezpieczeń były przeglądane za pośrednictwem maszyny wirtualnej, można także wyświetlać obowiązujące reguły zabezpieczeń za pośrednictwem indywidualnych:
- **Interfejs sieciowy**: informacje na temat [wyświetlania interfejsu sieciowego](virtual-network-network-interface.md#view-network-interface-settings).
- **Sieciowej grupy zabezpieczeń**: informacje o sposobie [wyświetlania sieciowej grupy zabezpieczeń](manage-network-security-group.md#view-details-of-a-network-security-group).

## <a name="diagnose-using-powershell"></a>Diagnozuj przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Można uruchomić następujące polecenia w [Azure Cloud Shell](https://shell.azure.com/powershell)lub przez uruchomienie programu PowerShell z komputera. Azure Cloud Shell to bezpłatna interaktywna powłoka. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. W przypadku uruchomienia programu PowerShell z komputera potrzebny jest moduł Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom `Get-Module -ListAvailable Az` polecenie na komputerze, aby znaleźć zainstalowaną wersję. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie, `Connect-AzAccount` Aby zalogować się do platformy Azure przy użyciu konta, które ma [wymagane uprawnienia](virtual-network-network-interface.md#permissions).

Pobierz efektywne reguły zabezpieczeń dla interfejsu sieciowego za pomocą [Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup). Poniższy przykład pobiera obowiązujące reguły zabezpieczeń dla interfejsu sieciowego o nazwie *myVMVMNic*, który znajduje się w grupie *zasobów o nazwie* moja Grupa:

```azurepowershell-interactive
Get-AzEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVMVMNic `
  -ResourceGroupName myResourceGroup
```

Dane wyjściowe są zwracane w formacie JSON. Aby zrozumieć dane wyjściowe, zobacz [interpretowanie danych wyjściowych polecenia](#interpret-command-output).
Dane wyjściowe są zwracane tylko wtedy, gdy sieciowej grupy zabezpieczeń jest skojarzona z interfejsem sieciowym, podsiecią, w której znajduje się interfejs sieciowy, lub obie. Maszyna wirtualna musi być w stanie uruchomionym. Maszyna wirtualna może mieć wiele interfejsów sieciowych z różnymi sieciowych grup zabezpieczeńmi. W przypadku rozwiązywania problemów Uruchom polecenie dla każdego interfejsu sieciowego.

Jeśli nadal masz problem z łącznością, zobacz [dodatkową diagnostykę](#additional-diagnosis) i [zagadnienia](#considerations).

Jeśli nie znasz nazwy interfejsu sieciowego, ale znasz nazwę maszyny wirtualnej, do której jest dołączony interfejs sieciowy, następujące polecenia zwracają identyfikatory wszystkich interfejsów sieciowych dołączonych do maszyny wirtualnej:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Otrzymasz dane wyjściowe podobne do następującego przykładu:

```output
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

W poprzednich danych wyjściowych nazwa interfejsu sieciowego to *myVMVMNic*.

## <a name="diagnose-using-azure-cli"></a>Diagnozuj przy użyciu interfejsu wiersza polecenia platformy Azure

W przypadku korzystania z poleceń interfejsu wiersza polecenia (CLI) platformy Azure w celu wykonania zadań w tym artykule Uruchom polecenia w [Azure Cloud Shell](https://shell.azure.com/bash)lub przez uruchomienie interfejsu wiersza polecenia na komputerze. Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.32 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, musisz również uruchomić `az login` i zalogować się do platformy Azure przy użyciu konta, które ma [wymagane uprawnienia](virtual-network-network-interface.md#permissions).

Pobierz efektywne reguły zabezpieczeń dla interfejsu sieciowego za pomocą [AZ Network nic list-sieciowej grupy zabezpieczeń](/cli/azure/network/nic#az-network-nic-list-effective-nsg). Poniższy przykład pobiera obowiązujące reguły zabezpieczeń dla interfejsu sieciowego o nazwie *myVMVMNic* , który znajduje się w grupie zasobów o nazwie Moja *zasobów*:

```azurecli-interactive
az network nic list-effective-nsg \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

Dane wyjściowe są zwracane w formacie JSON. Aby zrozumieć dane wyjściowe, zobacz [interpretowanie danych wyjściowych polecenia](#interpret-command-output).
Dane wyjściowe są zwracane tylko wtedy, gdy sieciowej grupy zabezpieczeń jest skojarzona z interfejsem sieciowym, podsiecią, w której znajduje się interfejs sieciowy, lub obie. Maszyna wirtualna musi być w stanie uruchomionym. Maszyna wirtualna może mieć wiele interfejsów sieciowych z różnymi sieciowych grup zabezpieczeńmi. W przypadku rozwiązywania problemów Uruchom polecenie dla każdego interfejsu sieciowego.

Jeśli nadal masz problem z łącznością, zobacz [dodatkową diagnostykę](#additional-diagnosis) i [zagadnienia](#considerations).

Jeśli nie znasz nazwy interfejsu sieciowego, ale znasz nazwę maszyny wirtualnej, do której jest dołączony interfejs sieciowy, następujące polecenia zwracają identyfikatory wszystkich interfejsów sieciowych dołączonych do maszyny wirtualnej:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

W zwróconym wyjściu zobaczysz informacje podobne do następującego przykładu:

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

W poprzednich danych wyjściowych nazwa interfejsu sieciowego to *myVMVMNic interfejs*.

## <a name="interpret-command-output"></a>Interpretowanie danych wyjściowych polecenia

Bez względu na to, czy użyto programu [PowerShell](#diagnose-using-powershell), czy [interfejsu wiersza polecenia platformy Azure](#diagnose-using-azure-cli) w celu zdiagnozowania problemu otrzymujesz dane wyjściowe zawierające następujące informacje:

- **NetworkSecurityGroup**: Identyfikator sieciowej grupy zabezpieczeń.
- **Skojarzenie**: określa, czy sieciowa Grupa zabezpieczeń jest skojarzona z *interfejsu sieciowego* czy *podsiecią*. Jeśli sieciowej grupy zabezpieczeń jest skojarzona z obydwoma, dane wyjściowe są zwracane z **NetworkSecurityGroup**, **Association** i **EffectiveSecurityRules** dla każdego sieciowej grupy zabezpieczeń. Jeśli sieciowej grupy zabezpieczeń jest skojarzony lub usuwany bezpośrednio przed uruchomieniem polecenia w celu wyświetlenia obowiązujących reguł zabezpieczeń, może być konieczne odczekanie kilku sekund, aby zmiana została odzwierciedlona w danych wyjściowych polecenia.
- **EffectiveSecurityRules**: wyjaśnienie każdej właściwości jest szczegółowo opisane w temacie [Create a Security Rule](manage-network-security-group.md#create-a-security-rule). Nazwy reguł poprzedzone *defaultSecurityRules/* są domyślnymi regułami zabezpieczeń, które istnieją w każdym sieciowej grupy zabezpieczeńe. Nazwy reguł poprzedzone *securityRules/* są regułami, które zostały utworzone. Reguły określające [tag usługi](./network-security-groups-overview.md#service-tags), takie jak **Internet**, **VirtualNetwork** i **AzureLoadBalancer** dla właściwości **destinationAddressPrefix** lub **DestinationPortRange i SourceAddressPrefix** , również mają wartości właściwości **expandedDestinationAddressPrefix** . Właściwość **expandedDestinationAddressPrefix** wyświetla wszystkie prefiksy adresów reprezentowane przez tag usługi.

Jeśli w danych wyjściowych zostaną wyświetlone zduplikowane reguły, jest to spowodowane tym, że element sieciowej grupy zabezpieczeń jest skojarzony zarówno z interfejsem sieciowym, jak i podsiecią. Oba sieciowych grup zabezpieczeń mają te same reguły domyślne i mogą mieć dodatkowe zduplikowane reguły, jeśli utworzono własne reguły, które są takie same w obu sieciowych grup zabezpieczeńach.

Reguła o nazwie **defaultSecurityRules/DenyAllInBound** polega na tym, że zapobieganie komunikacji przychodzącej z maszyną wirtualną przez port 80 z Internetu, zgodnie z opisem w tym [scenariuszu](#scenario). Żadna inna reguła z wyższym priorytetem (niższym numerem) zezwala na ruch przychodzący do portu 80 z Internetu.

## <a name="resolve-a-problem"></a>Rozwiązywanie problemu

W przypadku korzystania z witryny Azure [Portal](#diagnose-using-azure-portal), [programu PowerShell](#diagnose-using-powershell)lub [interfejsu wiersza polecenia platformy Azure](#diagnose-using-azure-cli) w celu zdiagnozowania problemu przedstawionego w [scenariuszu](#scenario) w tym artykule Rozwiązanie polega na utworzeniu reguły zabezpieczeń sieci z następującymi właściwościami:

| Właściwość                | Wartość                                                                              |
|---------                |---------                                                                           |
| Źródło                  | Dowolne                                                                                |
| Zakresy portów źródłowych      | Dowolne                                                                                |
| Element docelowy             | Adres IP maszyny wirtualnej, zakres adresów IP lub wszystkie adresy w podsieci. |
| Zakresy portów docelowych | 80                                                                                 |
| Protokół                | TCP                                                                                |
| Akcja                  | Zezwalaj                                                                              |
| Priorytet                | 100                                                                                |
| Nazwa                    | Zezwalaj-HTTP-All                                                                     |

Po utworzeniu reguły port 80 jest dozwolony dla ruchu przychodzącego z Internetu, ponieważ priorytet reguły jest wyższy niż domyślna reguła zabezpieczeń o nazwie *DenyAllInBound*, która odmówi ruch. Dowiedz się, jak [utworzyć regułę zabezpieczeń](manage-network-security-group.md#create-a-security-rule). Jeśli różne sieciowych grup zabezpieczeń są skojarzone zarówno z interfejsem sieciowym, jak i podsiecią, należy utworzyć tę samą regułę w obu sieciowych grup zabezpieczeń.

Gdy platforma Azure przetwarza ruch przychodzący, przetwarza reguły w sieciowej grupy zabezpieczeń skojarzonej z podsiecią (jeśli istnieje skojarzona sieciowej grupy zabezpieczeń), a następnie przetwarza reguły w sieciowej grupy zabezpieczeń skojarzonym z interfejsem sieciowym. Jeśli istnieje sieciowej grupy zabezpieczeń skojarzona z interfejsem sieciowym i podsiecią, Port musi być otwarty w obu sieciowych grup zabezpieczeńach dla ruchu, który ma dotrzeć do maszyny wirtualnej. Aby ułatwić administrowanie i problemy z komunikacją, zalecamy skojarzenie sieciowej grupy zabezpieczeń z podsiecią, a nie pojedynczymi interfejsami sieciowymi. Jeśli maszyny wirtualne w podsieci wymagają różnych reguł zabezpieczeń, można ustawić interfejsy sieciowe jako elementy członkowskie grupy zabezpieczeń aplikacji (ASG) i określić ASG jako źródło i miejsce docelowe reguły zabezpieczeń. Dowiedz się więcej o [grupach zabezpieczeń aplikacji](./network-security-groups-overview.md#application-security-groups).

Jeśli nadal występują problemy z komunikacją, zapoznaj się z [zagadnieniami](#considerations) i dodatkową diagnostyką.

## <a name="considerations"></a>Zagadnienia do rozważenia

Podczas rozwiązywania problemów z łącznością należy wziąć pod uwagę następujące kwestie:

* Domyślne reguły zabezpieczeń blokują dostęp przychodzący z Internetu i zezwalają na ruch przychodzący z sieci wirtualnej. Aby zezwolić na ruch przychodzący z Internetu, Dodaj reguły zabezpieczeń z wyższym priorytetem niż reguły domyślne. Dowiedz się więcej o [domyślnych regułach zabezpieczeń](./network-security-groups-overview.md#default-security-rules)lub sposobach [dodawania reguły zabezpieczeń](manage-network-security-group.md#create-a-security-rule).
* Jeśli masz równorzędne sieci wirtualne, domyślnie tag usługi **VIRTUAL_NETWORK** automatycznie rozszerza się, aby uwzględnić prefiksy dla wirtualnych sieci równorzędnych. Aby rozwiązać problemy związane z równorzędnymi sieciami wirtualnymi, można wyświetlić prefiksy na liście **ExpandedAddressPrefix** . Dowiedz się więcej o [wirtualnych sieciach równorzędnych](virtual-network-peering-overview.md) i [znacznikach usług](./network-security-groups-overview.md#service-tags).
* Obowiązujące reguły zabezpieczeń są wyświetlane tylko dla interfejsu sieciowego, jeśli istnieje sieciowej grupy zabezpieczeń skojarzona z interfejsem sieciowym maszyny wirtualnej, lub, podsiecią i, jeśli maszyna wirtualna jest w stanie uruchomienia.
* Jeśli nie ma żadnych sieciowych grup zabezpieczeń skojarzonych z interfejsem sieciowym lub podsiecią i masz przypisany [publiczny adres IP](virtual-network-public-ip-address.md) do maszyny wirtualnej, wszystkie porty są otwarte dla dostępu przychodzącego z i wychodzącego do dowolnego miejsca. Jeśli maszyna wirtualna ma publiczny adres IP, zalecamy zastosowanie sieciowej grupy zabezpieczeń do podsieci interfejsu sieciowego.

## <a name="additional-diagnosis"></a>Dodatkowa Diagnostyka

* Aby uruchomić szybki test w celu ustalenia, czy ruch jest dozwolony do lub z maszyny wirtualnej, użyj funkcji [weryfikowania przepływu IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) w usłudze Azure Network Watcher. Weryfikacja przepływu IP informuje o tym, czy ruch jest dozwolony, czy odrzucany. Jeśli odmówiono, sprawdzenie przepływu IP informuje, która reguła zabezpieczeń odmówił ruchu.
* Jeśli nie ma żadnych reguł zabezpieczeń powodujących niepowodzenie łączności sieciowej maszyny wirtualnej, problem może być spowodowany przez:
  * Oprogramowanie zapory uruchomione w systemie operacyjnym maszyny wirtualnej
  * Trasy skonfigurowane dla urządzeń wirtualnych lub ruchu lokalnego. Ruch internetowy może być przekierowywany do sieci lokalnej za pośrednictwem [tunelowania wymuszonego](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Jeśli wymusisz tunelowanie ruchu internetowego do urządzenia wirtualnego lub lokalnego, może nie być możliwe nawiązanie połączenia z MASZYNą wirtualną z Internetu. Aby dowiedzieć się, jak zdiagnozować problemy z trasą, które mogą utrudnić przepływ ruchu z maszyny wirtualnej, zobacz [diagnozowanie problemu z routingiem ruchu sieciowego maszyny wirtualnej](diagnose-network-routing-problem.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o wszystkich zadaniach, właściwościach i ustawieniach dla [sieciowej grupy zabezpieczeń](manage-network-security-group.md#work-with-network-security-groups) i [reguł zabezpieczeń](manage-network-security-group.md#work-with-security-rules).
- Dowiedz się więcej na temat [domyślnych reguł zabezpieczeń](./network-security-groups-overview.md#default-security-rules), [tagów usługi](./network-security-groups-overview.md#service-tags)i [sposobu, w jaki platforma Azure przetwarza reguły zabezpieczeń dla ruchu przychodzącego i wychodzącego](./network-security-groups-overview.md#network-security-groups) dla maszyny wirtualnej.