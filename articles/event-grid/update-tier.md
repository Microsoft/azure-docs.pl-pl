---
title: Aktualizowanie warstwy cenowej tematu Azure Event Grid lub domeny
description: W tym artykule opisano sposób aktualizacji warstwy cenowej tematu Azure Event Grid lub domeny (Basic to Premium, Premium to Basic) przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure i Azure PowerShell.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: spelluru
ms.openlocfilehash: caea8d515964510fce432eb0497e3af19ecc1369
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82101641"
---
# <a name="update-pricing-tier"></a>Aktualizowanie warstwy cenowej 
W tym artykule opisano sposób aktualizacji warstwy cenowej tematu Azure Event Grid lub domeny przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure i Azure PowerShell. 

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal
W tej sekcji przedstawiono sposób zmiany warstwy cenowej tematu lub domeny w Azure Portal. 

### <a name="overview-page"></a>Strona przeglądu
Warstwę cenową tematu lub domeny można zmienić na stronie **Przegląd** . Poniższy przykład pokazuje, jak uaktualnić temat z warstwy Podstawowa do warstwy Premium. Kroki prowadzące do obniżenia poziomu warstwy Premium do warstwy Podstawowa są podobne.

1. W [Azure Portal](https://portal.azure.com)przejdź do strony tematu lub domeny. 
2. Na stronie **Przegląd** wybierz bieżącą warstwę cenową (w poniższym przykładzie jest to **podstawowa**).
    
    ![Wybierz bieżącą warstwę cenową](./media/update-tier/select-tier.png)
3. Na stronie **warstwa cenowa** Zmień warstwę, a następnie wybierz przycisk **OK**. 

    ![Aktualizowanie warstwy cenowej](./media/update-tier/change-tier.png)
4. Sprawdź stan operacji na stronie **powiadomienia** .

    ![Stan aktualizacji](./media/update-tier/status.png)    
5. Upewnij się, że zaktualizowana warstwa została wyświetlona na stronie **Przegląd** . 

    ![Stan aktualizacji](./media/update-tier/changed-tier.png)

### <a name="networking-page"></a>Strona sieci
Możesz **przeprowadzić uaktualnienie** z warstwy Podstawowa do warstwy Premium na stronie **Sieć** . Nie można przeprowadzić obniżenia poziomu warstwy Premium do warstwy Podstawowa na tej stronie. 

1. W [Azure Portal](https://portal.azure.com)przejdź do strony tematu lub domeny. 
2. Na stronie **Sieć** przejdź do karty **prywatne połączenia punktu końcowego (wersja zapoznawcza)** . 
3. Jeśli bieżąca warstwa cenowa jest **podstawowa**, zostanie wyświetlony następujący komunikat. Wybierz ją. 

    ![Strona aktualizacji warstwy na połączeniach prywatnych punktów końcowych](./media/update-tier/private-endpoint-connections-page.png)
4. Na stronie **Aktualizuj do warstwy cenowej Premium** wybierz pozycję **tak**. 
    
    ![Potwierdzenie uaktualnienia](./media/update-tier/message-private-endpoint-connection.png)
5. Sprawdź stan operacji na stronie **powiadomienia** .

    ![Stan aktualizacji](./media/update-tier/status.png)



## <a name="use-azure-cli"></a>Interfejs wiersza polecenia platformy Azure
W tej sekcji pokazano, jak zmienić warstwę cenową tematu za pomocą poleceń interfejsu wiersza polecenia platformy Azure. Aby zaktualizować warstwę cenową domeny, należy użyć `az eventgrid domain update` polecenia w podobny sposób.

### <a name="prerequisites"></a>Wymagania wstępne
Zaktualizuj rozszerzenie Azure Event Grid dla interfejsu wiersza polecenia, uruchamiając następujące polecenie: 

```azurecli-interactive
az extension update -n eventgrid
```

Jeśli rozszerzenie nie jest zainstalowane, uruchom następujące polecenie, aby je zainstalować: 

```azurecli-interactive
az extension add -n eventgrid
```

### <a name="upgrade-a-topic-from-basic-to-premium"></a>Uaktualnianie tematu z warstwy Podstawowa do Premium

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --sku "Premium" 
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>Obniżenie poziomu tematu z warstwy Premium do warstwy Podstawowa

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --sku "Basic" 
```


## <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell
W tej sekcji przedstawiono sposób użycia poleceń programu PowerShell do zmiany warstwy cenowej tematu lub domeny. 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Przygotuj token i nagłówki dla wywołań interfejsu API REST 
Uruchom następujące polecenia dotyczące wymagań wstępnych, aby uzyskać token uwierzytelniania do użycia z wywołaniami interfejsu API REST oraz informacje o autoryzacji i innych nagłówkach. 

```azurepowershell-interactive
# replace <CLIENT ID> and <CLIENT SECRET>
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get the authentication token. Replace <TENANT ID>
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token `
    -Body $body `
    -ContentType 'application/x-www-form-urlencoded'

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
$Headers.Add("Content-Type","application/json")
```

### <a name="upgrade-a-topic-from-basic-to-premium"></a>Uaktualnianie tematu z warstwy Podstawowa do Premium

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>Obniżenie poziomu tematu z warstwy Premium do warstwy Podstawowa

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>Uaktualnianie domeny z warstwy Podstawowa do Premium

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>Obniżanie poziomu domeny z warstwy Premium do warstwy Podstawowa

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

## <a name="next-steps"></a>Następne kroki
W przypadku tematów i domen w warstwie Premium można skonfigurować prywatne punkty końcowe, aby ograniczyć dostęp tylko z wybranych sieci wirtualnych. Aby uzyskać instrukcje krok po kroku, zobacz [Konfigurowanie prywatnych punktów końcowych](configure-private-endpoints.md).
