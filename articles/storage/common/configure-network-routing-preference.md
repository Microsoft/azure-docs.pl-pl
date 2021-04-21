---
title: Konfigurowanie preferencji routingu sieciowego
titleSuffix: Azure Storage
description: Skonfiguruj preferencję routingu sieciowego dla konta usługi Azure Storage, aby określić sposób kierowania ruchu sieciowego do konta z klientów przez Internet.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 03/17/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: ed248480803370a75b40c18ee7d0e2641254d84a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790459"
---
# <a name="configure-network-routing-preference-for-azure-storage"></a>Konfigurowanie preferencji routingu sieciowego dla usługi Azure Storage

W tym artykule opisano sposób konfigurowania preferencji routingu sieciowego i punktów końcowych specyficznych dla trasy dla konta magazynu. 

Preferencja routingu sieciowego określa sposób kierowania ruchu sieciowego do konta z klientów przez Internet. Punkty końcowe specyficzne dla trasy to nowe punkty końcowe, które usługa Azure Storage tworzy dla konta magazynu. Te punkty końcowe przekierują ruch przez żądaną ścieżkę bez zmiany domyślnych preferencji routingu. Aby dowiedzieć się więcej, zobacz [Network routing preference for Azure Storage (Preferencje routingu sieciowego dla usługi Azure Storage).](network-routing-preference.md)

## <a name="configure-the-routing-preference-for-the-default-public-endpoint"></a>Konfigurowanie preferencji routingu dla domyślnego publicznego punktu końcowego

Domyślnie preferencja routingu dla publicznego punktu końcowego konta magazynu jest ustawiona na sieć globalną firmy Microsoft. Jako domyślną preferencję routingu dla publicznego punktu końcowego konta magazynu możesz wybrać między siecią globalną firmy Microsoft a routingiem internetowym. Aby dowiedzieć się więcej o różnicach między tymi dwoma typami routingu, zobacz [Network routing preference for Azure Storage (Preferencje routingu sieciowego dla usługi Azure Storage).](network-routing-preference.md) 

### <a name="portal"></a>[Portal](#tab/azure-portal)

Aby zmienić preferencję routingu na routing internetowy:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Przejdź do konta magazynu w portalu.

3. W **obszarze Ustawienia** wybierz pozycję **Sieć.**

    > [!div class="mx-imgBorder"]
    > ![Opcja menu Sieć](./media/configure-network-routing-preference/networking-option.png)

4.  Na karcie **Zapory i sieci wirtualne** w obszarze Routing **sieciowy** zmień ustawienie **preferencji Routing** na **Routing internetowy.**

5.  Kliknij pozycję **Zapisz**.

    > [!div class="mx-imgBorder"]
    > ![opcja routingu internetowego](./media/configure-network-routing-preference/internet-routing-option.png)

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia i postępuj zgodnie z `Connect-AzAccount` instrukcjami na ekranie, aby się uwierzytelnić.

   ```powershell
   Connect-AzAccount
   ```

2. Jeśli tożsamość jest skojarzona z więcej niż jedną subskrypcją, ustaw aktywną subskrypcję na subskrypcję konta magazynu, które będzie hostować statyczną witrynę internetową.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Zastąp wartość `<subscription-id>` symbolu zastępczego identyfikatorem subskrypcji.

3. Aby zmienić preferencję routingu na routing internetowy, użyj polecenia [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) i ustaw `--routing-choice` parametr na `InternetRouting` .

   ```powershell
   Set-AzStorageAccount -ResourceGroupName <resource-group-name> `
    -AccountName <storage-account-name> `
    -RoutingChoice InternetRouting
   ```

   Zastąp wartość `<resource-group-name>` symbolu zastępczego nazwą grupy zasobów zawierającej konto magazynu.

   Zastąp `<storage-account-name>` wartość symbolu zastępczego nazwą konta magazynu.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Zaloguj się do subskrypcji platformy Azure.

   - Aby uruchomić Azure Cloud Shell, zaloguj się do [Azure Portal](https://portal.azure.com).

   - Aby zalogować się do lokalnej instalacji interfejsu wiersza polecenia, uruchom [polecenie az login:](/cli/azure/reference-index#az_login)

     ```azurecli
     az login
     ```
2. Jeśli Twoja tożsamość jest skojarzona z więcej niż jedną subskrypcją, ustaw aktywną subskrypcję na subskrypcję konta magazynu, które będzie hostować statyczną witrynę internetową.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Zastąp wartość `<subscription-id>` symbolu zastępczego identyfikatorem subskrypcji.

3. Aby zmienić preferencję routingu na routing internetowy, użyj [polecenia az storage account update](/cli/azure/storage/account#az_storage_account_update) i ustaw parametr na `--routing-choice` `InternetRouting` .

   ```azurecli
   az storage account update --name <storage-account-name> --routing-choice InternetRouting
   ```

   Zastąp wartość symbolu zastępczego `<storage-account-name>` nazwą konta magazynu.

---

## <a name="configure-a-route-specific-endpoint"></a>Konfigurowanie punktu końcowego specyficznego dla trasy

Można również skonfigurować punkt końcowy specyficzny dla trasy. Na przykład można ustawić preferencję routingu dla domyślnego punktu końcowego na *routing* internetowy, a następnie opublikować punkt końcowy specyficzny dla trasy, który umożliwia kierowanie ruchu między klientami w Internecie a kontem magazynu za pośrednictwem sieci globalnej firmy Microsoft.

Ta preferencja ma wpływ tylko na punkt końcowy specyficzny dla trasy. Ta preferencja nie ma wpływu na domyślną preferencję routingu.  

### <a name="portal"></a>[Portal](#tab/azure-portal)

1.  Przejdź do konta magazynu w portalu.

2.  W **obszarze Ustawienia** wybierz pozycję **Sieć.**

3.  Na karcie Zapory i **sieci** wirtualne w obszarze Publikowanie punktów końcowych specyficznych dla trasy wybierz preferencję routingu punktu końcowego specyficznego dla trasy, a następnie kliknij przycisk **Zapisz.**

    Na poniższej ilustracji przedstawiono **wybraną opcję routingu sieciowego firmy Microsoft.**

    > [!div class="mx-imgBorder"]
    > ![Opcja routingu sieciowego firmy Microsoft](./media/configure-network-routing-preference/microsoft-network-routing-option.png)

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Aby skonfigurować punkt końcowy specyficzny dla trasy, użyj [polecenia Set-AzStorageAccount.](/powershell/module/az.storage/set-azstorageaccount) 

   - Aby utworzyć punkt końcowy specyficzny dla trasy, który używa preferencji routingu sieciowego firmy Microsoft, ustaw `-PublishMicrosoftEndpoint` parametr na `true` . 

   - Aby utworzyć punkt końcowy specyficzny dla trasy, który używa preferencji routingu internetowego, ustaw `-PublishInternetEndpointTo` parametr na `true` wartość .  

   Poniższy przykład tworzy punkt końcowy specyficzny dla trasy, który używa preferencji routingu sieciowego firmy Microsoft.

   ```powershell
   Set-AzStorageAccount -ResourceGroupName <resource-group-name> `
    -AccountName <storage-account-name> `
    -PublishMicrosoftEndpoint $true
   ```

   Zastąp wartość `<resource-group-name>` symbolu zastępczego nazwą grupy zasobów zawierającej konto magazynu.

   Zastąp wartość `<storage-account-name>` symbolu zastępczego nazwą konta magazynu.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Aby skonfigurować punkt końcowy specyficzny dla trasy, użyj [polecenia az storage account update.](/azure/storage/account#az_storage_account_update) 

   - Aby utworzyć punkt końcowy specyficzny dla trasy, który używa preferencji routingu sieciowego firmy Microsoft, ustaw `--publish-microsoft-endpoints` parametr na `true` . 

   - Aby utworzyć punkt końcowy specyficzny dla trasy, który używa preferencji routingu internetowego, ustaw `--publish-internet-endpoints` parametr na `true` wartość .  

   Poniższy przykład tworzy punkt końcowy specyficzny dla trasy, który używa preferencji routingu sieciowego firmy Microsoft.

   ```azurecli
   az storage account update --name <storage-account-name> --publish-microsoft-endpoints true
   ```

   Zastąp wartość `<storage-account-name>` symbolu zastępczego nazwą konta magazynu.

---

## <a name="find-the-endpoint-name-for-a-route-specific-endpoint"></a>Znajdowanie nazwy punktu końcowego dla punktu końcowego specyficznego dla trasy

Jeśli skonfigurowano punkt końcowy specyficzny dla trasy, można go znaleźć we właściwościach konta magazynu.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1.  W **obszarze Ustawienia** wybierz pozycję **Właściwości.**

    > [!div class="mx-imgBorder"]
    > ![opcja menu właściwości](./media/configure-network-routing-preference/properties.png)

2.  Punkt **końcowy routingu sieci firmy Microsoft** jest wyświetlany dla każdej usługi obsługującej preferencje routingu. Na tej ilustracji przedstawiono punkt końcowy dla usług obiektów blob i plików.

    > [!div class="mx-imgBorder"]
    > ![Opcja routingu sieciowego firmy Microsoft dla punktów końcowych specyficznych dla trasy](./media/configure-network-routing-preference/routing-url.png)

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Aby wydrukować punkty końcowe w konsoli, użyj `PrimaryEndpoints` właściwości obiektu konta magazynu.

   ```powershell
   Get-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name>
   write-Output $StorageAccount.PrimaryEndpoints
   ```

   Zastąp wartość `<resource-group-name>` symbolu zastępczego nazwą grupy zasobów zawierającej konto magazynu.

   Zastąp wartość `<storage-account-name>` symbolu zastępczego nazwą konta magazynu.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Aby wydrukować punkty końcowe w konsoli, użyj właściwości [az storage account show](/cli/azure/storage/account#az_storage_account_show) obiektu konta magazynu.

   ```azurecli
   az storage account show -g <resource-group-name> -n <storage-account-name>
   ```

   Zastąp wartość `<resource-group-name>` symbolu zastępczego nazwą grupy zasobów zawierającej konto magazynu.

   Zastąp `<storage-account-name>` wartość symbolu zastępczego nazwą konta magazynu.

---

## <a name="see-also"></a>Zobacz też

- [Preferencja routingu sieciowego](network-routing-preference.md)
- [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](storage-network-security.md)
- [Zalecenia dotyczące zabezpieczeń usługi Blob Storage](../blobs/security-recommendations.md)
