---
title: Zarządzanie Traffic Manager na platformie Azure przy użyciu programu PowerShell
description: Za pomocą tej ścieżki szkoleniowej Rozpocznij korzystanie z Azure PowerShell Traffic Manager.
services: traffic-manager
documentationcenter: na
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: duau
ms.openlocfilehash: 21076fe23301c189d9987f78706cc81691ce7a4f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89400572"
---
# <a name="using-powershell-to-manage-traffic-manager"></a>Zarządzanie Traffic Manager przy użyciu programu PowerShell

Azure Resource Manager jest preferowanym interfejsem zarządzania dla usług platformy Azure. Profilami Traffic Manager platformy Azure można zarządzać przy użyciu opartych na Azure Resource Manager interfejsów API i narzędzi.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-model"></a>Model zasobów

Usługa Azure Traffic Manager jest konfigurowana przy użyciu kolekcji ustawień o nazwie profil Traffic Manager. Ten profil zawiera ustawienia DNS, ustawienia routingu ruchu, ustawienia monitorowania punktu końcowego oraz listę punktów końcowych usługi, do których jest kierowany ruch.

Każdy profil Traffic Manager jest reprezentowany przez zasób typu "TrafficManagerProfiles". Na poziomie interfejsu API REST identyfikator URI dla każdego profilu jest następujący:

`https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}`

## <a name="setting-up-azure-powershell"></a>Konfigurowanie Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Te instrukcje używają Microsoft Azure PowerShell. W poniższym artykule wyjaśniono, jak zainstalować i skonfigurować Azure PowerShell.

* [How to install and configure Azure PowerShell](/powershell/azure/)

W przykładach w tym artykule założono, że masz istniejącą grupę zasobów. Grupę zasobów można utworzyć przy użyciu następującego polecenia:

```powershell
New-AzResourceGroup -Name MyRG -Location "West US"
```

> [!NOTE]
> Azure Resource Manager wymaga, aby wszystkie grupy zasobów miały lokalizację. Ta lokalizacja jest używana jako wartość domyślna dla zasobów utworzonych w tej grupie zasobów. Ponieważ jednak zasoby profilu Traffic Manager są globalne, a nie regionalne, wybór lokalizacji grupy zasobów nie ma wpływu na usługę Azure Traffic Manager.

## <a name="create-a-traffic-manager-profile"></a>Tworzenie profilu Traffic Manager

Aby utworzyć profil Traffic Manager, należy użyć `New-AzTrafficManagerProfile` polecenia cmdlet:

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

W poniższej tabeli opisano parametry:

| Parametr | Opis |
| --- | --- |
| Nazwa |Nazwa zasobu dla zasobu profilu Traffic Manager. Profile w tej samej grupie zasobów muszą mieć unikatowe nazwy. Ta nazwa jest oddzielona od nazwy DNS używanej w zapytaniach DNS. |
| ResourceGroupName |Nazwa grupy zasobów zawierającej zasób profilu. |
| TrafficRoutingMethod |Określa metodę routingu ruchu użytą do określenia, który punkt końcowy jest zwracany w odpowiedzi na zapytanie DNS. Możliwe wartości to "Performance", "ważone" lub "Priority". |
| RelativeDnsName |Określa część nazwy hosta DNS dostarczoną przez ten profil Traffic Manager. Ta wartość jest połączona z nazwą domeny DNS używaną przez platformę Azure Traffic Manager do tworzenia w pełni kwalifikowanej nazwy domeny (FQDN) profilu. Na przykład ustawienie wartości "contoso" zmieni się na "contoso.trafficmanager.net". |
| TTL |Określa czas wygaśnięcia (TTL) DNS (w sekundach). Ten czas TTL informuje lokalnych nazw DNS i klientów DNS, jak długo mają być buforowane odpowiedzi DNS dla tego profilu Traffic Manager. |
| MonitorProtocol |Określa protokół, który ma być używany do monitorowania kondycji punktu końcowego. Możliwe wartości to "HTTP" i "HTTPS". |
| MonitorPort |Określa port TCP używany do monitorowania kondycji punktu końcowego. |
| MonitorPath |Określa ścieżkę względną nazwy domeny punktu końcowego służącą do sondowania kondycji punktu końcowego. |

Polecenie cmdlet tworzy profil Traffic Manager na platformie Azure i zwraca odpowiedni obiekt profilu do programu PowerShell. W tym momencie profil nie zawiera żadnych punktów końcowych. Aby uzyskać więcej informacji na temat dodawania punktów końcowych do profilu Traffic Manager, zobacz Dodawanie Traffic Manager punktów końcowych.

## <a name="get-a-traffic-manager-profile"></a>Pobierz profil Traffic Manager

Aby pobrać istniejący obiekt profilu Traffic Manager, użyj `Get-AzTrafficManagerProfle` polecenia cmdlet:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

To polecenie cmdlet zwraca obiekt profilu Traffic Manager.

## <a name="update-a-traffic-manager-profile"></a>Aktualizowanie profilu Traffic Manager

Modyfikowanie profilów Traffic Manager jest zgodne z 3-etapowym procesem:

1. Pobierz profil przy użyciu `Get-AzTrafficManagerProfile` lub Użyj profilu zwróconego przez `New-AzTrafficManagerProfile` .
2. Zmodyfikuj profil. Możesz dodawać i usuwać punkty końcowe albo zmieniać parametry punktu końcowego lub profilu. Te zmiany są operacjami poza wierszem. Zmieniasz tylko lokalny obiekt w pamięci, który reprezentuje profil.
3. Zatwierdź zmiany przy użyciu `Set-AzTrafficManagerProfile` polecenia cmdlet.

Wszystkie właściwości profilu można zmienić z wyjątkiem RelativeDnsName profilu. Aby zmienić RelativeDnsName, należy usunąć profil i nowy profil o nowej nazwie.

Poniższy przykład ilustruje sposób zmiany czasu wygaśnięcia profilu:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
$TmProfile.Ttl = 300
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

Istnieją trzy typy punktów końcowych Traffic Manager:

1. **Punkty końcowe platformy Azure** to usługi hostowane na platformie Azure
2. **Zewnętrzne punkty końcowe** to usługi hostowane poza platformą Azure
3. **Zagnieżdżone punkty końcowe** są używane do konstruowania zagnieżdżonych hierarchii profilów Traffic Manager. Zagnieżdżone punkty końcowe umożliwiają zaawansowane konfiguracje routingu ruchu dla złożonych aplikacji.

We wszystkich trzech przypadkach punkty końcowe można dodać na dwa sposoby:

1. Zastosowanie opisanego wcześniej procesu 3-etapowego. Zaletą tej metody jest to, że kilka zmian punktów końcowych można wprowadzać w jednej aktualizacji.
2. Za pomocą polecenia cmdlet New-AzTrafficManagerEndpoint. To polecenie cmdlet dodaje punkt końcowy do istniejącego profilu Traffic Manager w ramach jednej operacji.

## <a name="adding-azure-endpoints"></a>Dodawanie Punkty końcowe platformy Azure

Usługi referencyjne Azure Endpoints są hostowane na platformie Azure. Obsługiwane są dwa typy punktów końcowych platformy Azure:

1. Azure App Service
2. Zasoby usługi Azure PublicIpAddress (które można dołączyć do modułu równoważenia obciążenia lub karty sieciowej maszyny wirtualnej). PublicIpAddress musi mieć przypisaną nazwę DNS do użycia w Traffic Manager.

W każdym przypadku:

* Usługa jest określona przy użyciu parametru "element targetresourceid" `Add-AzTrafficManagerEndpointConfig` lub `New-AzTrafficManagerEndpoint` .
* Wartości "target" i "EndpointLocation" są implikowane przez element targetresourceid.
* Określenie opcji "waga" jest opcjonalne. Wagi są używane tylko wtedy, gdy profil jest skonfigurowany do korzystania z metody routingu ruchu "ważone". W przeciwnym razie są one ignorowane. Jeśli ta wartość jest określona, musi to być liczba z zakresu od 1 do 1000. Wartość domyślna to "1".
* Określanie priorytetu jest opcjonalne. Priorytety są stosowane tylko wtedy, gdy profil jest skonfigurowany do korzystania z metody routingu ruchu "Priority". W przeciwnym razie są one ignorowane. Prawidłowe wartości to od 1 do 1000 z niższymi wartościami wskazującymi wyższy priorytet. Jeśli określono dla jednego punktu końcowego, muszą one być określone dla wszystkich punktów końcowych. W przypadku pominięcia wartości domyślne zaczynające się od "1" są stosowane w kolejności, w jakiej są wyświetlane punkty końcowe.

### <a name="example-1-adding-app-service-endpoints-using-add-aztrafficmanagerendpointconfig"></a>Przykład 1: Dodawanie App Service punktów końcowych przy użyciu `Add-AzTrafficManagerEndpointConfig`

W tym przykładzie utworzysz profil Traffic Manager i dodasz dwa punkty końcowe App Service przy użyciu `Add-AzTrafficManagerEndpointConfig` polecenia cmdlet.

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$webapp1 = Get-AzWebApp -Name webapp1
Add-AzTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $TmProfile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
$webapp2 = Get-AzWebApp -Name webapp2
Add-AzTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $TmProfile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```
### <a name="example-2-adding-a-publicipaddress-endpoint-using-new-aztrafficmanagerendpoint"></a>Przykład 2: Dodawanie punktu końcowego publicIpAddress za pomocą `New-AzTrafficManagerEndpoint`

W tym przykładzie do profilu Traffic Manager zostanie dodany zasób publicznego adresu IP. Publiczny adres IP musi mieć skonfigurowaną nazwę DNS i może być powiązany z kartą sieciową maszyny wirtualnej lub modułem równoważenia obciążenia.

```powershell
$ip = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="adding-external-endpoints"></a>Dodawanie Zewnętrzne punkty końcowe

Traffic Manager używa zewnętrznych punktów końcowych do kierowania ruchu do usług hostowanych poza platformą Azure. Tak jak w przypadku punktów końcowych platformy Azure, zewnętrzne punkty końcowe można dodać przy użyciu `Add-AzTrafficManagerEndpointConfig` , a następnie `Set-AzTrafficManagerProfile` , lub `New-AzTrafficManagerEndpoint` .

Podczas określania zewnętrznych punktów końcowych:

* Nazwa domeny punktu końcowego musi być określona przy użyciu parametru "target"
* Jeśli jest używana metoda routingu ruchu "Performance", wymagana jest wartość "EndpointLocation". W przeciwnym razie jest to opcjonalne. Wartość musi być [prawidłową nazwą regionu platformy Azure](https://azure.microsoft.com/regions/).
* "Waga" i "priorytet" są opcjonalne.

### <a name="example-1-adding-external-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>Przykład 1: dodawanie zewnętrznych punktów końcowych przy użyciu `Add-AzTrafficManagerEndpointConfig` i `Set-AzTrafficManagerProfile`

W tym przykładzie utworzysz profil Traffic Manager, dodasz dwa zewnętrzne punkty końcowe i zatwierdzisz zmiany.

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $TmProfile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointLocation "North Europe" -EndpointStatus Enabled
Add-AzTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $TmProfile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointLocation "Central US" -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

### <a name="example-2-adding-external-endpoints-using-new-aztrafficmanagerendpoint"></a>Przykład 2: dodawanie zewnętrznych punktów końcowych przy użyciu `New-AzTrafficManagerEndpoint`

W tym przykładzie dodamy zewnętrzny punkt końcowy do istniejącego profilu. Profil jest określany przy użyciu nazw profilów i grup zasobów.

```powershell
New-AzTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
```

## <a name="adding-nested-endpoints"></a>Dodawanie punktów końcowych "nested"

Każdy profil Traffic Manager określa metodę routingu pojedynczego ruchu sieciowego. Istnieją jednak scenariusze, które wymagają bardziej zaawansowanego routingu ruchu niż Routing dostarczany przez pojedynczy profil Traffic Manager. Profile Traffic Manager można zagnieżdżać, aby łączyć korzyści z więcej niż jednej metody routingu ruchu. Profile zagnieżdżone umożliwiają zastąpienie domyślnego zachowania Traffic Manager w celu obsługi większych i bardziej złożonych wdrożeń aplikacji. Aby uzyskać bardziej szczegółowe przykłady, zobacz [profile Traffic Manager zagnieżdżonych](traffic-manager-nested-profiles.md).

Zagnieżdżone punkty końcowe są konfigurowane w profilu nadrzędnym, przy użyciu określonego typu punktu końcowego "NestedEndpoints". Podczas określania zagnieżdżonych punktów końcowych:

* Punkt końcowy musi być określony przy użyciu parametru "element targetresourceid"
* Jeśli jest używana metoda routingu ruchu "Performance", wymagana jest wartość "EndpointLocation". W przeciwnym razie jest to opcjonalne. Wartość musi być [prawidłową nazwą regionu platformy Azure](https://azure.microsoft.com/regions/).
* "Waga" i "priorytet" są opcjonalne, jak w przypadku punktów końcowych platformy Azure.
* Parametr "MinChildEndpoints" jest opcjonalny. Wartość domyślna to "1". Jeśli liczba dostępnych punktów końcowych spadnie poniżej tego progu, profil nadrzędny uwzględnia profil podrzędny, a ruch jest przekierowywany do innych punktów końcowych w profilu nadrzędnym.

### <a name="example-1-adding-nested-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>Przykład 1: Dodawanie zagnieżdżonych punktów końcowych przy użyciu `Add-AzTrafficManagerEndpointConfig` i `Set-AzTrafficManagerProfile`

W tym przykładzie utworzysz nowe Traffic Manager podrzędne i nadrzędne profile, dodajesz element podrzędny jako zagnieżdżony punkt końcowy do elementu nadrzędnego i zatwierdzisz zmiany.

```powershell
$child = New-AzTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$parent = New-AzTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
Set-AzTrafficManagerProfile -TrafficManagerProfile $parent
```

W przypadku zwięzłości w tym przykładzie nie dodano żadnych innych punktów końcowych do profilów podrzędnych lub nadrzędnych.

### <a name="example-2-adding-nested-endpoints-using-new-aztrafficmanagerendpoint"></a>Przykład 2: Dodawanie zagnieżdżonych punktów końcowych przy użyciu `New-AzTrafficManagerEndpoint`

W tym przykładzie dodamy istniejący profil podrzędny jako zagnieżdżony punkt końcowy do istniejącego profilu nadrzędnego. Profil jest określany przy użyciu nazw profilów i grup zasobów.

```powershell
$child = Get-AzTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
```

## <a name="adding-endpoints-from-another-subscription"></a>Dodawanie punktów końcowych z innej subskrypcji

Traffic Manager można korzystać z punktów końcowych z różnych subskrypcji. Musisz przełączyć się do subskrypcji za pomocą punktu końcowego, który chcesz dodać, aby pobrać wymagane dane wejściowe do Traffic Manager. Następnie należy przełączyć się do subskrypcji przy użyciu profilu Traffic Manager i dodać do niego punkt końcowy. Poniższy przykład pokazuje, jak to zrobić za pomocą publicznego adresu IP.

```powershell
Set-AzContext -SubscriptionId $EndpointSubscription
$ip = Get-AzPublicIpAddress -Name $IpAddressName -ResourceGroupName $EndpointRG

Set-AzContext -SubscriptionId $trafficmanagerSubscription
New-AzTrafficManagerEndpoint -Name $EndpointName -ProfileName $ProfileName -ResourceGroupName $TrafficManagerRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="update-a-traffic-manager-endpoint"></a>Aktualizowanie punktu końcowego Traffic Manager

Istnieją dwa sposoby aktualizowania istniejącego punktu końcowego Traffic Manager:

1. Pobierz profil Traffic Manager za pomocą polecenia `Get-AzTrafficManagerProfile` , zaktualizuj właściwości punktu końcowego w ramach profilu i zatwierdź zmiany przy użyciu polecenia `Set-AzTrafficManagerProfile` . Ta metoda ma możliwość aktualizowania więcej niż jednego punktu końcowego w ramach jednej operacji.
2. Pobierz punkt końcowy Traffic Manager za pomocą polecenia `Get-AzTrafficManagerEndpoint` , zaktualizuj właściwości punktu końcowego i zatwierdź zmiany przy użyciu polecenia `Set-AzTrafficManagerEndpoint` . Ta metoda jest prostsza, ponieważ nie wymaga indeksowania do tablicy punktów końcowych w profilu.

### <a name="example-1-updating-endpoints-using-get-aztrafficmanagerprofile-and-set-aztrafficmanagerprofile"></a>Przykład 1: aktualizowanie punktów końcowych przy użyciu `Get-AzTrafficManagerProfile` i `Set-AzTrafficManagerProfile`

W tym przykładzie zmienimy priorytet dwóch punktów końcowych w ramach istniejącego profilu.

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
$TmProfile.Endpoints[0].Priority = 2
$TmProfile.Endpoints[1].Priority = 1
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

### <a name="example-2-updating-an-endpoint-using-get-aztrafficmanagerendpoint-and-set-aztrafficmanagerendpoint"></a>Przykład 2: aktualizowanie punktu końcowego przy użyciu `Get-AzTrafficManagerEndpoint` i `Set-AzTrafficManagerEndpoint`

W tym przykładzie zmodyfikujemy wagę pojedynczego punktu końcowego w istniejącym profilu.

```powershell
$endpoint = Get-AzTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
$endpoint.Weight = 20
Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint
```

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>Włączanie i wyłączanie punktów końcowych i profilów

Traffic Manager pozwala na włączenie i wyłączenie poszczególnych punktów końcowych, a także umożliwienie włączania i wyłączania całych profilów.
Te zmiany można wykonać przez pobranie/zaktualizowanie/ustawienie zasobów punktu końcowego lub profilu. Aby usprawnić te Typowe operacje, są one również obsługiwane za pośrednictwem dedykowanych poleceń cmdlet.

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>Przykład 1: Włączanie i wyłączanie profilu Traffic Manager

Aby włączyć profil Traffic Manager, użyj `Enable-AzTrafficManagerProfile` . Profil można określić przy użyciu obiektu profil. Obiekt profilu może być przesyłany za pośrednictwem potoku lub przy użyciu parametru "-TrafficManagerProfile". W tym przykładzie określimy profil według nazwy profilu i grupy zasobów.

```powershell
Enable-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Aby wyłączyć profil Traffic Manager:

```powershell
Disable-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Disable-AzTrafficManagerProfile polecenie cmdlet monituje o potwierdzenie. Ten monit można pominąć przy użyciu parametru "-Force".

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>Przykład 2: Włączanie i wyłączanie punktu końcowego Traffic Manager

Aby włączyć punkt końcowy Traffic Manager, użyj `Enable-AzTrafficManagerEndpoint` . Istnieją dwa sposoby określania punktu końcowego

1. Używanie obiektu TrafficManagerEndpoint przekazanego za pośrednictwem potoku lub przy użyciu parametru "-TrafficManagerEndpoint"
2. Przy użyciu nazwy punktu końcowego, typu punktu końcowego, nazwy profilu i nazwy grupy zasobów:

```powershell
Enable-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Analogicznie, aby wyłączyć punkt końcowy Traffic Manager:

```powershell
Disable-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

Podobnie jak w przypadku `Disable-AzTrafficManagerProfile` , `Disable-AzTrafficManagerEndpoint` polecenie cmdlet monituje o potwierdzenie. Ten monit można pominąć przy użyciu parametru "-Force".

## <a name="delete-a-traffic-manager-endpoint"></a>Usuń punkt końcowy Traffic Manager

Aby usunąć poszczególne punkty końcowe, użyj `Remove-AzTrafficManagerEndpoint` polecenia cmdlet:

```powershell
Remove-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

To polecenie cmdlet monituje o potwierdzenie. Ten monit można pominąć przy użyciu parametru "-Force".

## <a name="delete-a-traffic-manager-profile"></a>Usuwanie profilu Traffic Manager

Aby usunąć profil Traffic Manager, należy użyć `Remove-AzTrafficManagerProfile` polecenia cmdlet, określając nazwę profilu i grupy zasobów:

```powershell
Remove-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

To polecenie cmdlet monituje o potwierdzenie. Ten monit można pominąć przy użyciu parametru "-Force".

Profil, który ma zostać usunięty, można również określić przy użyciu obiektu profilu:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
Remove-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile [-Force]
```

Ta sekwencja może również być potokowa:

```powershell
Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>Następne kroki

[Monitorowanie Traffic Manager](traffic-manager-monitoring.md)

[Zagadnienia dotyczące wydajności usługi Traffic Manager](traffic-manager-performance-considerations.md)
