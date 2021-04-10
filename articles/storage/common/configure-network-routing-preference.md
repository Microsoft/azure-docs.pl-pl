---
title: Konfigurowanie preferencji routingu sieciowego
titleSuffix: Azure Storage
description: Skonfiguruj preferencję routingu sieciowego dla konta usługi Azure Storage, aby określić, w jaki sposób ruch sieciowy jest kierowany do konta z klientów przez Internet.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 03/17/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 0738f7e427c2ff094c9b6df7539ba67dff80d095
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104589858"
---
# <a name="configure-network-routing-preference-for-azure-storage"></a>Konfigurowanie preferencji routingu sieciowego dla usługi Azure Storage

W tym artykule opisano, jak można skonfigurować preferencję routingu sieciowego i punkty końcowe specyficzne dla trasy dla konta magazynu. 

Preferencja routingu sieciowego określa, w jaki sposób ruch sieciowy jest kierowany do konta z klientów przez Internet. Punkty końcowe specyficzne dla trasy są nowymi punktami końcowymi tworzonymi przez usługę Azure Storage dla konta magazynu. Te punkty końcowe kierują ruchem do odpowiedniej ścieżki bez zmiany domyślnego preferencji routingu. Aby dowiedzieć się więcej, zobacz [preferencja routingu sieciowego dla usługi Azure Storage](network-routing-preference.md).

## <a name="configure-the-routing-preference-for-the-default-public-endpoint"></a>Skonfiguruj preferencję routingu dla domyślnego publicznego punktu końcowego

Domyślnie preferencja routingu dla publicznego punktu końcowego konta magazynu jest ustawiona na Microsoft Global Network. Jako domyślne preferencje routingu dla publicznego punktu końcowego konta magazynu można wybrać między siecią globalną i routingiem internetowym firmy Microsoft. Aby dowiedzieć się więcej o różnicach między tymi dwoma typami routingu, zobacz [preferencja routingu sieciowego dla usługi Azure Storage](network-routing-preference.md). 

### <a name="portal"></a>[Portal](#tab/azure-portal)

Aby zmienić preferencję routingu na Routing internetowy:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Przejdź do swojego konta magazynu w portalu.

3. W obszarze **Ustawienia** wybierz pozycję **Sieć**.

    > [!div class="mx-imgBorder"]
    > ![Opcja menu Sieć](./media/configure-network-routing-preference/networking-option.png)

4.  Na karcie **zapory i sieci wirtualne** w obszarze **Routing sieciowy** Zmień ustawienie **preferencji Routing** na **Routing internetowy**.

5.  Kliknij pozycję **Zapisz**.

    > [!div class="mx-imgBorder"]
    > ![Opcja routingu internetowego](./media/configure-network-routing-preference/internet-routing-option.png)

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Zaloguj się do subskrypcji platformy Azure za pomocą `Connect-AzAccount` polecenia i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie w celu uwierzytelnienia.

   ```powershell
   Connect-AzAccount
   ```

2. Jeśli Twoja tożsamość jest skojarzona z więcej niż jedną subskrypcją, ustaw aktywną subskrypcję na konto magazynu, które będzie hostować statyczną witrynę sieci Web.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Zastąp `<subscription-id>` wartość symbolu zastępczego identyfikatorem subskrypcji.

3. Aby zmienić preferencję routingu na Routing internetowy, użyj polecenia [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) i ustaw `--routing-choice` parametr na wartość `InternetRouting` .

   ```powershell
   Set-AzStorageAccount -ResourceGroupName <resource-group-name> `
    -AccountName <storage-account-name> `
    -RoutingChoice InternetRouting
   ```

   Zastąp `<resource-group-name>` wartość symbolu zastępczego nazwą grupy zasobów zawierającej konto magazynu.

   Zastąp `<storage-account-name>` wartość symbolu zastępczego nazwą konta magazynu.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Zaloguj się do subskrypcji platformy Azure.

   - Aby uruchomić Azure Cloud Shell, zaloguj się do [Azure Portal](https://portal.azure.com).

   - Aby zalogować się do lokalnej instalacji interfejsu wiersza polecenia, uruchom polecenie [AZ login](/cli/azure/reference-index#az-login) :

     ```azurecli
     az login
     ```
2. Jeśli Twoja tożsamość jest skojarzona z więcej niż jedną subskrypcją, ustaw aktywną subskrypcję na konto magazynu, które będzie hostować statyczną witrynę sieci Web.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Zastąp `<subscription-id>` wartość symbolu zastępczego identyfikatorem subskrypcji.

3. Aby zmienić preferencję routingu na Routing internetowy, użyj polecenia [AZ Storage account Update](/cli/azure/storage/account#az_storage_account_update) i ustaw `--routing-choice` parametr na `InternetRouting` .

   ```azurecli
   az storage account update --name <storage-account-name> --routing-choice InternetRouting
   ```

   Zastąp wartość symbolu zastępczego `<storage-account-name>` nazwą konta magazynu.

---

## <a name="configure-a-route-specific-endpoint"></a>Konfigurowanie punktu końcowego specyficznego dla trasy

Można również skonfigurować punkt końcowy specyficzny dla trasy. Można na przykład ustawić preferencję routingu domyślnego punktu końcowego na *Routing internetowy*, a następnie opublikować punkt końcowy specyficzny dla trasy, który umożliwi ruch między klientami w Internecie a kontem magazynu, aby można było je kierować za pośrednictwem sieci globalnej firmy Microsoft.

To preferencje ma wpływ tylko na punkt końcowy dotyczący trasy. Preferencja nie ma wpływu na domyślne preferencje routingu.  

### <a name="portal"></a>[Portal](#tab/azure-portal)

1.  Przejdź do swojego konta magazynu w portalu.

2.  W obszarze **Ustawienia** wybierz pozycję **Sieć**.

3.  Na karcie **zapory i sieci wirtualne** w obszarze **Publikowanie punktów końcowych właściwych dla trasy** wybierz preferencję routingu punktu końcowego określonego dla trasy, a następnie kliknij przycisk **Zapisz**.

    Na poniższej ilustracji przedstawiono wybraną opcję **routingu sieciowego firmy Microsoft** .

    > [!div class="mx-imgBorder"]
    > ![Opcja routingu sieci firmy Microsoft](./media/configure-network-routing-preference/microsoft-network-routing-option.png)

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Aby skonfigurować punkt końcowy specyficzny dla trasy, użyj polecenia [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) . 

   - Aby utworzyć punkt końcowy specyficzny dla trasy, który używa preferencji routingu sieci firmy Microsoft, ustaw `-PublishMicrosoftEndpoint` parametr na `true` . 

   - Aby utworzyć punkt końcowy specyficzny dla trasy, który używa preferencji routingu internetowego, ustaw `-PublishInternetEndpointTo` parametr na `true` .  

   Poniższy przykład tworzy punkt końcowy specyficzny dla trasy, który używa preferencji routingu sieciowego firmy Microsoft.

   ```powershell
   Set-AzStorageAccount -ResourceGroupName <resource-group-name> `
    -AccountName <storage-account-name> `
    -PublishMicrosoftEndpoint $true
   ```

   Zastąp `<resource-group-name>` wartość symbolu zastępczego nazwą grupy zasobów zawierającej konto magazynu.

   Zastąp `<storage-account-name>` wartość symbolu zastępczego nazwą konta magazynu.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Aby skonfigurować punkt końcowy specyficzny dla trasy, użyj polecenia [AZ Storage account Update](/azure/storage/account#az-storage-account-update) . 

   - Aby utworzyć punkt końcowy specyficzny dla trasy, który używa preferencji routingu sieci firmy Microsoft, ustaw `--publish-microsoft-endpoints` parametr na `true` . 

   - Aby utworzyć punkt końcowy specyficzny dla trasy, który używa preferencji routingu internetowego, ustaw `--publish-internet-endpoints` parametr na `true` .  

   Poniższy przykład tworzy punkt końcowy specyficzny dla trasy, który używa preferencji routingu sieciowego firmy Microsoft.

   ```azurecli
   az storage account update --name <storage-account-name> --publish-microsoft-endpoints true
   ```

   Zastąp `<storage-account-name>` wartość symbolu zastępczego nazwą konta magazynu.

---

## <a name="find-the-endpoint-name-for-a-route-specific-endpoint"></a>Znajdź nazwę punktu końcowego dla punktu końcowego określonego dla trasy

W przypadku skonfigurowania punktu końcowego określonego dla trasy można znaleźć punkt końcowy we właściwościach konta magazynu.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1.  W obszarze **Ustawienia** wybierz pozycję **Właściwości**.

    > [!div class="mx-imgBorder"]
    > ![opcja menu właściwości](./media/configure-network-routing-preference/properties.png)

2.  Punkt końcowy **routingu sieci firmy Microsoft** jest wyświetlany dla każdej usługi, która obsługuje preferencje routingu. Ten obraz pokazuje punkt końcowy dla usług BLOB i plików.

    > [!div class="mx-imgBorder"]
    > ![Opcja routingu sieci firmy Microsoft dla punktów końcowych specyficznych dla trasy](./media/configure-network-routing-preference/routing-url.png)

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Aby wydrukować punkty końcowe do konsoli programu, użyj `PrimaryEndpoints` właściwości obiektu konto magazynu.

   ```powershell
   Get-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name>
   write-Output $StorageAccount.PrimaryEndpoints
   ```

   Zastąp `<resource-group-name>` wartość symbolu zastępczego nazwą grupy zasobów zawierającej konto magazynu.

   Zastąp `<storage-account-name>` wartość symbolu zastępczego nazwą konta magazynu.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Aby wydrukować punkty końcowe do konsoli programu, użyj właściwości [AZ Storage account show](/cli/azure/storage/account#az_storage_account_show) dla obiektu konto magazynu.

   ```azurecli
   az storage account show -g <resource-group-name> -n <storage-account-name>
   ```

   Zastąp `<resource-group-name>` wartość symbolu zastępczego nazwą grupy zasobów zawierającej konto magazynu.

   Zastąp `<storage-account-name>` wartość symbolu zastępczego nazwą konta magazynu.

---

## <a name="see-also"></a>Zobacz też

- [Preferencja routingu sieciowego](network-routing-preference.md)
- [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](storage-network-security.md)
- [Zalecenia dotyczące zabezpieczeń usługi BLOB Storage](../blobs/security-recommendations.md)
