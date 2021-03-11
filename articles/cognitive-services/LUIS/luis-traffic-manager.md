---
title: Zwiększ limit przydziału punktu końcowego — LUIS
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) oferuje możliwość zwiększenia limitu przydziału żądania punktu końcowego poza limit przydziału pojedynczego klucza. W tym celu należy utworzyć więcej kluczy dla LUIS i dodać je do aplikacji LUIS na stronie **publikowania** w sekcji **zasoby i klucze** .
manager: nitinme
ms.custom: seodec18, devx-track-js, devx-track-azurepowershell
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 08/20/2019
ms.openlocfilehash: 37c4bd2af080a76e93bc9599f06e4d502985979f
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102609655"
---
# <a name="use-microsoft-azure-traffic-manager-to-manage-endpoint-quota-across-keys"></a>Użyj Microsoft Azure Traffic Manager, aby zarządzać limitem punktów końcowych między kluczami
Language Understanding (LUIS) oferuje możliwość zwiększenia limitu przydziału żądania punktu końcowego poza limit przydziału pojedynczego klucza. W tym celu należy utworzyć więcej kluczy dla LUIS i dodać je do aplikacji LUIS na stronie **publikowania** w sekcji **zasoby i klucze** .

Klient-aplikacja musi zarządzać ruchem przez klucze. LUIS.

W tym artykule wyjaśniono, jak zarządzać ruchem między kluczami za pomocą usługi Azure [Traffic Manager][traffic-manager-marketing]. Musisz mieć już przeszkolone i opublikowane aplikacje LUIS. Jeśli go nie masz, postępuj zgodnie z prekompilowaną domeną [szybkiego startu](luis-get-started-create-app.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connect-to-powershell-in-the-azure-portal"></a>Nawiązywanie połączenia z programem PowerShell w Azure Portal
W witrynie [Azure][azure-portal] Portal Otwórz okno programu PowerShell. Ikona okna programu PowerShell jest **>_** na górnym pasku nawigacyjnym. Za pomocą programu PowerShell z portalu otrzymujesz najnowszą wersję programu PowerShell i masz uwierzytelnienie. Program PowerShell w portalu wymaga konta [usługi Azure Storage](https://azure.microsoft.com/services/storage/) .

![Zrzut ekranu przedstawiający Azure Portal z otwartym oknem programu PowerShell](./media/traffic-manager/azure-portal-powershell.png)

W poniższych sekcjach są używane [Traffic Manager poleceń cmdlet programu PowerShell](/powershell/module/az.trafficmanager/#traffic_manager).

## <a name="create-azure-resource-group-with-powershell"></a>Tworzenie grupy zasobów platformy Azure przy użyciu programu PowerShell
Przed utworzeniem zasobów platformy Azure należy utworzyć grupę zasobów zawierającą wszystkie zasoby. Nazwij grupę zasobów `luis-traffic-manager` i Użyj regionu `West US` . Region grupy zasobów przechowuje metadane dotyczące grupy. Nie spowalnia zasobów, jeśli znajdują się w innym regionie.

Utwórz grupę zasobów za pomocą polecenia cmdlet **[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)** :

```powerShell
New-AzResourceGroup -Name luis-traffic-manager -Location "West US"
```

## <a name="create-luis-keys-to-increase-total-endpoint-quota"></a>Utwórz klucze LUIS, aby zwiększyć łączny przydział punktu końcowego
1. W Azure Portal Utwórz dwa klucze **Language Understanding** , jeden w `West US` i jeden w `East US` . Użyj istniejącej grupy zasobów utworzonej w poprzedniej sekcji o nazwie `luis-traffic-manager` .

    ![Zrzut ekranu przedstawiający Azure Portal z dwoma kluczami LUIS w grupie zasobów Luis-Traffic-Manager](./media/traffic-manager/luis-keys.png)

2. W witrynie sieci Web [Luis][LUIS] w sekcji **Zarządzanie** na stronie **zasoby platformy Azure** Przypisz klucze do aplikacji i ponownie Opublikuj aplikację, wybierając przycisk **Publikuj** w prawym górnym menu.

    Przykładowy adres URL w kolumnie **Endpoint** używa żądania GET z kluczem punktu końcowego jako parametru zapytania. Skopiuj dwa nowe klucze adresów URL punktów końcowych. Są one używane jako część konfiguracji Traffic Manager w dalszej części tego artykułu.

## <a name="manage-luis-endpoint-requests-across-keys-with-traffic-manager"></a>Zarządzanie żądaniami punktów końcowych LUIS między kluczami przy użyciu Traffic Manager
Traffic Manager tworzy nowy punkt dostępu DNS dla punktów końcowych. Nie działa jako brama ani serwer proxy, ale wyłącznie na poziomie systemu DNS. Ten przykład nie zmienia żadnych rekordów DNS. Używa biblioteki DNS do komunikowania się z Traffic Manager, aby uzyskać prawidłowy punkt końcowy dla tego konkretnego żądania. _Każde_ żądanie przeznaczone do Luis najpierw wymaga żądania Traffic Manager, aby określić, który punkt końcowy Luis do użycia.

### <a name="polling-uses-luis-endpoint"></a>Sondowanie używa punktu końcowego LUIS
Traffic Manager okresowo sonduje punkty końcowe, aby upewnić się, że punkt końcowy jest nadal dostępny. Przesondowany adres URL Traffic Manager musi być dostępny z żądaniem GET i zwracać 200. Adres URL punktu końcowego na stronie **publikacji** . Ponieważ każdy klucz punktu końcowego ma inną trasę i parametry ciągu zapytania, każdy klucz punktu końcowego wymaga innej ścieżki sondowania. Za każdym razem, gdy Traffic Manager sondy, jest to koszt żądania przydziału. Parametr ciąg zapytania **q** punktu końcowego Luis jest wypowiedź wysyłany do Luis. Ten parametr, zamiast wysyłania elementu wypowiedź, jest używany do dodawania sondowania Traffic Manager do dziennika punktu końcowego LUIS jako technikę debugowania podczas pobierania Traffic Manager skonfigurowany.

Ponieważ każdy punkt końcowy LUIS potrzebuje własnej ścieżki, musi mieć własny profil Traffic Manager. Aby zarządzać między profilami, Utwórz [ _zagnieżdżoną_](../../traffic-manager/traffic-manager-nested-profiles.md) architekturę Traffic Manager. Jeden profil nadrzędny wskazuje profile podrzędne i zarządza ruchem między nimi.

Po skonfigurowaniu Traffic Manager należy pamiętać o zmianie ścieżki, aby użyć parametru "Rejestrowanie = FAŁSZ ciągu zapytania", aby dziennik nie wypełniał sondowania.

## <a name="configure-traffic-manager-with-nested-profiles"></a>Konfigurowanie Traffic Manager przy użyciu profilów zagnieżdżonych
W poniższych sekcjach opisano tworzenie dwóch profilów podrzędnych, jeden dla klucza Wschodnie LUIS i jeden dla klucza zachodniego LUIS. Następnie tworzony jest profil nadrzędny, a dwa podrzędne profile są dodawane do profilu nadrzędnego.

### <a name="create-the-east-us-traffic-manager-profile-with-powershell"></a>Tworzenie profilu Traffic Manager Wschodnie stany USA przy użyciu programu PowerShell
Aby utworzyć profil Traffic Manager Wschodnie stany USA, należy wykonać kilka czynności: Utwórz profil, Dodaj punkt końcowy i ustaw punkt końcowy. Profil Traffic Manager może mieć wiele punktów końcowych, ale każdy punkt końcowy ma tę samą ścieżkę walidacji. Ponieważ adresy URL punktów końcowych LUIS dla subskrypcji wschodnie i zachodnie różnią się w zależności od regionu i klucza punktu końcowego, każdy punkt końcowy LUIS musi być pojedynczym punktem końcowym w profilu.

1. Utwórz profil za pomocą polecenia cmdlet **[New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile)**

    Aby utworzyć profil, użyj następującego polecenia cmdlet. Upewnij się, że zmienisz `appIdLuis` i `subscriptionKeyLuis` . SubscriptionKey dotyczy klucza Wschodnie stany USA LUIS. Jeśli ścieżka jest niepoprawna, łącznie z IDENTYFIKATORem aplikacji LUIS i kluczem punktu końcowego, sondowanie Traffic Manager jest stanem, `degraded` ponieważ zarządzanie ruchem nie może pomyślnie zażądać punktu końcowego Luis. Upewnij się, że wartość `q` jest `traffic-manager-east` tak, aby była widoczna w dziennikach punktów końcowych Luis.

    ```powerShell
    $eastprofile = New-AzTrafficManagerProfile -Name luis-profile-eastus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-eastus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appID>?subscription-key=<subscriptionKey>&q=traffic-manager-east"
    ```

    W tej tabeli opisano każdą zmienną w poleceniu cmdlet:

    |Parametr konfiguracji|Nazwa zmiennej lub wartość|Przeznaczenie|
    |--|--|--|
    |-Name|Luis-profile-wschód|Nazwa Traffic Manager w Azure Portal|
    |-ResourceGroupName|Luis-Traffic-Manager|Utworzone w poprzedniej sekcji|
    |-TrafficRoutingMethod|Wydajność|Aby uzyskać więcej informacji, zobacz [Traffic Manager metod routingu][routing-methods]. W przypadku korzystania z wydajności żądanie adresu URL do Traffic Manager musi pochodzić z regionu użytkownika. Jeśli przeprowadzisz przez chatbot lub inną aplikację, jest on odpowiedzialny za chatbot do naśladowania regionu w wywołaniu Traffic Manager. |
    |-RelativeDnsName|Luis — DNS — Wschodnie|Jest to poddomena usługi: luis-dns-eastus.trafficmanager.net|
    |-TTL|30|Interwał sondowania, 30 sekund|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port i protokół dla LUIS to HTTPS/443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-east`|Zamień `<appIdLuis>` i `<subscriptionKeyLuis>` na własne wartości.|

    Pomyślne żądanie nie ma odpowiedzi.

2. Dodaj punkt końcowy Wschodnie stany USA z poleceniem cmdlet **[Add-AzTrafficManagerEndpointConfig](/powershell/module/az.trafficmanager/add-aztrafficmanagerendpointconfig)**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-east-endpoint -TrafficManagerProfile $eastprofile -Type ExternalEndpoints -Target eastus.api.cognitive.microsoft.com -EndpointLocation "eastus" -EndpointStatus Enabled
    ```
    W tej tabeli opisano każdą zmienną w poleceniu cmdlet:

    |Parametr konfiguracji|Nazwa zmiennej lub wartość|Przeznaczenie|
    |--|--|--|
    |-EndpointName|Luis — wschód-punkt końcowy|Nazwa punktu końcowego wyświetlana w profilu|
    |-TrafficManagerProfile|$eastprofile|Użyj obiektu profilu utworzonego w kroku 1|
    |-Typ|ExternalEndpoints|Aby uzyskać więcej informacji, zobacz [Traffic Manager Endpoint][traffic-manager-endpoints] |
    |-Target|eastus.api.cognitive.microsoft.com|Jest to domena dla punktu końcowego LUIS.|
    |-EndpointLocation|eastus|Region punktu końcowego|
    |-EndpointStatus|Enabled (Włączony)|Włącz punkt końcowy podczas tworzenia|

    Pomyślna odpowiedź wygląda następująco:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-eastus
    Name                             : luis-profile-eastus
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-eastus
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /luis/v2.0/apps/<luis-app-id>?subscription-key=f0517d185bcf467cba5147d6260bb868&q=traffic-manager-east
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {luis-east-endpoint}
    ```

3. Ustaw punkt końcowy Wschodnie stany USA przy użyciu polecenia cmdlet **[Set-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/set-aztrafficmanagerprofile)**

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $eastprofile
    ```

    Pomyślna odpowiedź będzie taka sama jak w kroku 2.

### <a name="create-the-west-us-traffic-manager-profile-with-powershell"></a>Tworzenie profilu Traffic Manager zachodnich Stanów Zjednoczonych przy użyciu programu PowerShell
Aby utworzyć profil Traffic Manager zachodnie stany USA, wykonaj te same czynności: Utwórz profil, Dodaj punkt końcowy i ustaw punkt końcowy.

1. Utwórz profil za pomocą polecenia cmdlet **[New-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)**

    Aby utworzyć profil, użyj następującego polecenia cmdlet. Upewnij się, że zmienisz `appIdLuis` i `subscriptionKeyLuis` . SubscriptionKey dotyczy klucza Wschodnie stany USA LUIS. Jeśli ścieżka jest niepoprawna, łącznie z IDENTYFIKATORem aplikacji LUIS i kluczem punktu końcowego, sondowanie Traffic Manager jest stanem, `degraded` ponieważ zarządzanie ruchem nie może pomyślnie zażądać punktu końcowego Luis. Upewnij się, że wartość `q` jest `traffic-manager-west` tak, aby była widoczna w dziennikach punktów końcowych Luis.

    ```powerShell
    $westprofile = New-AzTrafficManagerProfile -Name luis-profile-westus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-westus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west"
    ```

    W tej tabeli opisano każdą zmienną w poleceniu cmdlet:

    |Parametr konfiguracji|Nazwa zmiennej lub wartość|Przeznaczenie|
    |--|--|--|
    |-Name|Luis-profile-zachodni|Nazwa Traffic Manager w Azure Portal|
    |-ResourceGroupName|Luis-Traffic-Manager|Utworzone w poprzedniej sekcji|
    |-TrafficRoutingMethod|Wydajność|Aby uzyskać więcej informacji, zobacz [Traffic Manager metod routingu][routing-methods]. W przypadku korzystania z wydajności żądanie adresu URL do Traffic Manager musi pochodzić z regionu użytkownika. Jeśli przeprowadzisz przez chatbot lub inną aplikację, jest on odpowiedzialny za chatbot do naśladowania regionu w wywołaniu Traffic Manager. |
    |-RelativeDnsName|Luis — DNS — zachodnie|Jest to poddomena usługi: luis-dns-westus.trafficmanager.net|
    |-TTL|30|Interwał sondowania, 30 sekund|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port i protokół dla LUIS to HTTPS/443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west`|Zamień `<appId>` i `<subscriptionKey>` na własne wartości. Pamiętaj, że ten klucz punktu końcowego różni się od klucza punktu końcowego "Wschód"|

    Pomyślne żądanie nie ma odpowiedzi.

2. Dodaj zachodnie stany USA z poleceniem cmdlet **[Add-AzTrafficManagerEndpointConfig](/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-west-endpoint -TrafficManagerProfile $westprofile -Type ExternalEndpoints -Target westus.api.cognitive.microsoft.com -EndpointLocation "westus" -EndpointStatus Enabled
    ```

    W tej tabeli opisano każdą zmienną w poleceniu cmdlet:

    |Parametr konfiguracji|Nazwa zmiennej lub wartość|Przeznaczenie|
    |--|--|--|
    |-EndpointName|Luis-zachodni — punkt końcowy|Nazwa punktu końcowego wyświetlana w profilu|
    |-TrafficManagerProfile|$westprofile|Użyj obiektu profilu utworzonego w kroku 1|
    |-Typ|ExternalEndpoints|Aby uzyskać więcej informacji, zobacz [Traffic Manager Endpoint][traffic-manager-endpoints] |
    |-Target|westus.api.cognitive.microsoft.com|Jest to domena dla punktu końcowego LUIS.|
    |-EndpointLocation|westus|Region punktu końcowego|
    |-EndpointStatus|Enabled (Włączony)|Włącz punkt końcowy podczas tworzenia|

    Pomyślna odpowiedź wygląda następująco:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-westus
    Name                             : luis-profile-westus
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-westus
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /luis/v2.0/apps/c3fc5d1e-5187-40cc-af0f-fbde328aa16b?subscription-key=e3605f07e3cc4bedb7e02698a54c19cc&q=traffic-manager-west
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {luis-west-endpoint}
    ```

3. Ustaw zachodnie stany USA przy użyciu polecenia cmdlet **[Set-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)**

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $westprofile
    ```

    Pomyślna odpowiedź to taka sama odpowiedź jak krok 2.

### <a name="create-parent-traffic-manager-profile"></a>Utwórz profil Traffic Manager nadrzędny
Utwórz profil nadrzędny Traffic Manager i połącz dwa podrzędne profile Traffic Manager z elementem nadrzędnym.

1. Utwórz profil nadrzędny przy użyciu polecenia cmdlet **[New-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)**

    ```powerShell
    $parentprofile = New-AzTrafficManagerProfile -Name luis-profile-parent -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-parent -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/"
    ```

    W tej tabeli opisano każdą zmienną w poleceniu cmdlet:

    |Parametr konfiguracji|Nazwa zmiennej lub wartość|Przeznaczenie|
    |--|--|--|
    |-Name|Luis-profile-Parent|Nazwa Traffic Manager w Azure Portal|
    |-ResourceGroupName|Luis-Traffic-Manager|Utworzone w poprzedniej sekcji|
    |-TrafficRoutingMethod|Wydajność|Aby uzyskać więcej informacji, zobacz [Traffic Manager metod routingu][routing-methods]. W przypadku korzystania z wydajności żądanie adresu URL do Traffic Manager musi pochodzić z regionu użytkownika. Jeśli przeprowadzisz przez chatbot lub inną aplikację, jest on odpowiedzialny za chatbot do naśladowania regionu w wywołaniu Traffic Manager. |
    |-RelativeDnsName|Luis-DNS-Parent|Jest to poddomena usługi: luis-dns-parent.trafficmanager.net|
    |-TTL|30|Interwał sondowania, 30 sekund|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port i protokół dla LUIS to HTTPS/443|
    |-MonitorPath|`/`|Ta ścieżka nie ma znaczenia, ponieważ zamiast niej używane są ścieżki podrzędnego punktu końcowego.|

    Pomyślne żądanie nie ma odpowiedzi.

2. Dodaj profil podrzędny Wschodnie stany USA do elementu nadrzędnego z typem **[Add-AzTrafficManagerEndpointConfig](/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** i **NestedEndpoints**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-useast -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $eastprofile.Id -EndpointStatus Enabled -EndpointLocation "eastus" -MinChildEndpoints 1
    ```

    W tej tabeli opisano każdą zmienną w poleceniu cmdlet:

    |Parametr konfiguracji|Nazwa zmiennej lub wartość|Przeznaczenie|
    |--|--|--|
    |-EndpointName|Child-Endpoint-USEast|Profil wschodni|
    |-TrafficManagerProfile|$parentprofile|Profil, do którego ma zostać przypisany ten punkt końcowy|
    |-Typ|NestedEndpoints|Aby uzyskać więcej informacji, zobacz [Add-AzTrafficManagerEndpointConfig](/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig). |
    |-Element targetresourceid|$eastprofile. #C1|Identyfikator profilu podrzędnego|
    |-EndpointStatus|Enabled (Włączony)|Stan punktu końcowego po dodaniu do elementu nadrzędnego|
    |-EndpointLocation|eastus|[Nazwa regionu platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/)|
    |-MinChildEndpoints|1|Minimalna liczba do podrzędnych punktów końcowych|

    Pomyślna odpowiedź wygląda jak poniżej i zawiera nowy `child-endpoint-useast` punkt końcowy:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-parent
    Name                             : luis-profile-parent
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-parent
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {child-endpoint-useast}
    ```

3. Dodawanie profilu podrzędnego zachodnie stany USA do elementu nadrzędnego przy użyciu polecenia cmdlet **[Add-AzTrafficManagerEndpointConfig](/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** i typu **NestedEndpoints**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-uswest -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $westprofile.Id -EndpointStatus Enabled -EndpointLocation "westus" -MinChildEndpoints 1
    ```

    W tej tabeli opisano każdą zmienną w poleceniu cmdlet:

    |Parametr konfiguracji|Nazwa zmiennej lub wartość|Przeznaczenie|
    |--|--|--|
    |-EndpointName|Child-Endpoint-uswest|Profil zachodni|
    |-TrafficManagerProfile|$parentprofile|Profil, do którego ma zostać przypisany ten punkt końcowy|
    |-Typ|NestedEndpoints|Aby uzyskać więcej informacji, zobacz [Add-AzTrafficManagerEndpointConfig](/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig). |
    |-Element targetresourceid|$westprofile. #C1|Identyfikator profilu podrzędnego|
    |-EndpointStatus|Enabled (Włączony)|Stan punktu końcowego po dodaniu do elementu nadrzędnego|
    |-EndpointLocation|westus|[Nazwa regionu platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/)|
    |-MinChildEndpoints|1|Minimalna liczba do podrzędnych punktów końcowych|

    Pomyślna odpowiedź wygląda jak i obejmuje zarówno poprzedni `child-endpoint-useast` punkt końcowy, jak i nowy `child-endpoint-uswest` punkt końcowy:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-parent
    Name                             : luis-profile-parent
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-parent
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {child-endpoint-useast, child-endpoint-uswest}
    ```

4. Ustaw punkty końcowe za pomocą polecenia cmdlet **[Set-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)**

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $parentprofile
    ```

    Pomyślna odpowiedź jest taka sama jak w kroku 3.

### <a name="powershell-variables"></a>Zmienne programu PowerShell
W poprzednich sekcjach utworzono trzy zmienne programu PowerShell: `$eastprofile` , `$westprofile` , `$parentprofile` . Te zmienne są używane do końca konfiguracji Traffic Manager. Jeśli nie wybrano opcji tworzenia zmiennych lub nie przeprowadzisz limitu czasu okna programu PowerShell, możesz użyć polecenia cmdlet programu PowerShell **[Get-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile)**, aby ponownie uzyskać profil i przypisać go do zmiennej.

Zastąp elementy w nawiasach kątowych, `<>` z prawidłowymi wartościami dla każdego z trzech potrzebnych profilów.

```powerShell
$<variable-name> = Get-AzTrafficManagerProfile -Name <profile-name> -ResourceGroupName luis-traffic-manager
```

## <a name="verify-traffic-manager-works"></a>Weryfikuj Traffic Manager działa
Aby upewnić się, że profile Traffic Manager działają, profile muszą mieć stan `Online` tego stanu na podstawie ścieżki sondowania punktu końcowego.

### <a name="view-new-profiles-in-the-azure-portal"></a>Wyświetl nowe profile w Azure Portal
Możesz sprawdzić, czy wszystkie trzy profile zostały utworzone, przeglądając zasoby w `luis-traffic-manager` grupie zasobów.

![Zrzut ekranu grupy zasobów platformy Azure Luis-Traffic-Manager](./media/traffic-manager/traffic-manager-profiles.png)

### <a name="verify-the-profile-status-is-online"></a>Sprawdź, czy profil jest w trybie online
Traffic Manager sonduje ścieżkę każdego punktu końcowego, aby upewnić się, że jest w trybie online. Jeśli jest w trybie online, stan profilów podrzędnych to `Online` . Ta wartość jest wyświetlana w **omówieniu** poszczególnych profilów.

![Zrzut ekranu przedstawiający Omówienie profilu usługi Azure Traffic Manager w trybie online](./media/traffic-manager/profile-status-online.png)

### <a name="validate-traffic-manager-polling-works"></a>Sprawdź poprawność działania sondowania Traffic Manager
Innym sposobem sprawdzenia poprawności działania sondowania w usłudze Traffic Manager jest LUISe dzienników punktów końcowych. Na stronie listy aplikacji witryny sieci Web [Luis][LUIS] wyeksportuj dziennik punktu końcowego dla aplikacji. Ponieważ Traffic Manager sondy często dla dwóch punktów końcowych, w dziennikach znajdują się wpisy nawet wtedy, gdy mają one tylko kilka minut. Pamiętaj, aby wyszukać wpisy, w których rozpoczyna się zapytanie `traffic-manager-` .

```console
traffic-manager-west    6/7/2018 19:19  {"query":"traffic-manager-west","intents":[{"intent":"None","score":0.944767}],"entities":[]}
traffic-manager-east    6/7/2018 19:20  {"query":"traffic-manager-east","intents":[{"intent":"None","score":0.944767}],"entities":[]}
```

### <a name="validate-dns-response-from-traffic-manager-works"></a>Sprawdź poprawność odpowiedzi DNS z Traffic Manager działa
Aby sprawdzić, czy odpowiedź DNS zwraca punkt końcowy LUIS, zażądaj ruchu Zarządzanie profilem nadrzędnym DNS przy użyciu biblioteki klienta DNS. Nazwa DNS dla profilu nadrzędnego to `luis-dns-parent.trafficmanager.net` .

Poniższy kod Node.js wykonuje żądanie dla profilu nadrzędnego i zwraca punkt końcowy LUIS:

```javascript
const dns = require('dns');

dns.resolveAny('luis-dns-parent.trafficmanager.net', (err, ret) => {
  console.log('ret', ret);
});
```

Pomyślna odpowiedź z punktem końcowym LUIS to:

```json
[
    {
        value: 'westus.api.cognitive.microsoft.com',
        type: 'CNAME'
    }
]
```

## <a name="use-the-traffic-manager-parent-profile"></a>Użyj Traffic Manager profilu nadrzędnego
Aby zarządzać ruchem między punktami końcowymi, należy wstawić wywołanie do Traffic Manager DNS, aby znaleźć punkt końcowy LUIS. To wywołanie jest nawiązywane dla każdego żądania punktu końcowego LUIS i musi symulować lokalizację geograficzną użytkownika aplikacji klienckiej LUIS. Dodaj kod odpowiedzi DNS między aplikacją kliencką LUIS a żądaniem LUIS dla prognozowania punktów końcowych.

## <a name="resolving-a-degraded-state"></a>Rozpoznawanie stanu obniżonej wydajności

Włącz [dzienniki diagnostyczne](../../traffic-manager/traffic-manager-diagnostic-logs.md) dla Traffic Manager, aby sprawdzić, dlaczego stan punktu końcowego ma obniżony poziom.

## <a name="clean-up"></a>Czyszczenie
Usuń dwa klucze punktu końcowego LUIS, trzy profile Traffic Manager i grupę zasobów zawierającą te pięć zasobów. Jest to realizowane z Azure Portal. Należy usunąć pięć zasobów z listy zasobów. Następnie Usuń grupę zasobów.

## <a name="next-steps"></a>Następne kroki

Przejrzyj opcje [oprogramowania pośredniczącego](/azure/bot-service/bot-builder-create-middleware?tabs=csaddmiddleware%252ccsetagoverwrite%252ccsmiddlewareshortcircuit%252ccsfallback%252ccsactivityhandler) w programie BotFramework v4, aby zrozumieć, jak ten kod zarządzania ruchem można dodać do BotFramework bot.

[traffic-manager-marketing]: https://azure.microsoft.com/services/traffic-manager/
[traffic-manager-docs]: ../../traffic-manager/index.yml
[LUIS]: ./luis-reference-regions.md#luis-website
[azure-portal]: https://portal.azure.com/
[azure-storage]: https://azure.microsoft.com/services/storage/
[routing-methods]: ../../traffic-manager/traffic-manager-routing-methods.md
[traffic-manager-endpoints]: ../../traffic-manager/traffic-manager-endpoint-types.md