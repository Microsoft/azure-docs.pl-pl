---
title: Konfigurowanie zasad punktu końcowego usługi — Azure HDInsight
description: Dowiedz się, jak skonfigurować zasady punktu końcowego usługi dla sieci wirtualnej za pomocą usługi Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.date: 07/15/2020
ms.openlocfilehash: 992cd994f96b5637d5afd91bccfecde8704d2886
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98940618"
---
# <a name="configure-virtual-network-service-endpoint-policies-for-azure-hdinsight"></a>Konfigurowanie zasad punktu końcowego usługi sieci wirtualnej na potrzeby usługi Azure HDInsight

Ten artykuł zawiera informacje dotyczące implementowania zasad punktu końcowego usługi w sieciach wirtualnych przy użyciu usługi Azure HDInsight.

## <a name="background"></a>Tło

Usługa Azure HDInsight umożliwia tworzenie klastrów w sieci wirtualnej. Jeśli musisz zezwolić na ruch wychodzący z sieci wirtualnej do innych usług platformy Azure, takich jak konta magazynu, możesz utworzyć [zasady punktu końcowego usługi](../virtual-network/virtual-network-service-endpoint-policies-overview.md). Zasady punktu końcowego usługi, które są tworzone za pomocą Azure Portal, jednak umożliwiają tylko tworzenie zasad dla jednego konta, wszystkich kont w ramach subskrypcji lub wszystkich kont w grupie zasobów.

Ponieważ usługa Azure HDInsight zbiera dane i pliki dziennika z każdego klastra na określonych kontach magazynu w każdym regionie. Aby te dane miały dostęp do usługi HDInsight z sieci wirtualnej, konieczne jest utworzenie zasad punktu końcowego usług, które zezwalają na ruch wychodzący do określonych punktów zbierania danych zarządzanych przez usługę Azure HDInsight.

## <a name="service-endpoint-policies-for-hdinsight"></a>Zasady punktu końcowego usługi HDInsight

Te zasady punktu końcowego usługi obsługują następujące funkcje:

- Zbieranie dzienników i danych telemetrycznych podczas tworzenia klastra, wykonywania zadań i operacji na platformie, takich jak skalowanie.
- Dołączanie wirtualnych dysków twardych (VHD) do nowo utworzonych węzłów klastra w celu aprowizacji oprogramowania i bibliotek w klastrze.

Jeśli zasady punktu końcowego usługi nie są tworzone w celu włączenia tego przepływu danych, tworzenie klastra może się nie powieść, a usługa Azure HDInsight nie będzie mogła zapewnić obsługi klastrów.

## <a name="create-service-endpoint-policies-for-hdinsight"></a>Tworzenie zasad punktu końcowego usługi HDInsight

Przed utworzeniem nowych klastrów upewnij się, że odpowiednie zasady punktu końcowego usługi są dołączone do sieci wirtualnej. W przeciwnym razie Tworzenie klastra może zakończyć się niepowodzeniem lub spowodować błąd.

Aby utworzyć niezbędne zasady punktu końcowego usługi, należy wykonać następujące czynności:

1. Wybierz region, w którym będzie tworzony klaster usługi HDInsight.
1. Wyszukanie tego regionu na [liście zasobów zasad punktu końcowego usługi](https://github.com/Azure-Samples/hdinsight-enterprise-security/blob/main/hdinsight-service-endpoint-policy-resources.json), który zapewnia wszystkie grupy zasobów dla kont magazynu zarządzania usługą HDInsight.
1. Wybierz listę grup zasobów dla danego regionu. Poniżej przedstawiono przykład zasobów dla programu `Canada Central` :

    ```json
    "Canada Central":[
        "/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",
        "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",
        "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG"
    ],
    ```

1. Wstaw tę listę grup zasobów do skryptu instalacyjnego zapisaną w interfejsie wiersza polecenia platformy Azure lub Azure PowerShell.

    ```azurecli
    $subscriptionId = "<subscription id>"
    $rgName="<resource group name> "
    $location="<location name>"
    $vnetName="<vnet name>"
    $subnetName="<subnet name>"
    $sepName="<service endpoint policy name>"
    $sepDefName="<service endpoint policy definition name>"
    
    # Set to the right subscription ID
    az account set --subscription $subscriptionId
    
    # setup service endpoint on the virtual network subnet
    az network vnet subnet update -g $rgName --vnet-name $vnetName -n $subnetName --service-endpoints Microsoft.Storage
    
    # Create Service Endpoint Policy
    az network service-endpoint policy create -g $rgName  -n $sepName -l $location
    
    # Insert the list of HDInsight owned resources for the region your clusters will be created in.
    [String[]]$resources = @("/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",`
    "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",`
    "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG")
    
    #Assign service resources to the SEP policy.
    az network service-endpoint policy-definition create -g $rgName --policy-name $sepName -n $sepDefName --service "Microsoft.Storage" --service-resources $resources
    
    # Associate a subnet to the service endpoint policy just created. If there is a delay in updating it to subnet, you can use the Azure portal to associate the policy with the subnet.
    az network vnet subnet update -g $rgName --vnet-name $vnetName -n $subnetName --service-endpoint-policy $sepName
    ```

    Jeśli wolisz skonfigurować zasady punktu końcowego usługi przy użyciu programu PowerShell, użyj poniższego fragmentu kodu.
    
    ```powershell
    #Script to assign SEP 
    $subscriptionId = "<subscription id>"
    $rgName = "<resource group name>"
    $vnetName = "<vnet name>"
    $subnetName = "<subnet Name"
    $location = "Canada Central"
    
    # Connect to your Azure Account
    Connect-AzAccount
    
    # Select the Subscription that you want to use
    Select-AzSubscription -SubscriptionId $subscriptionId
    
    # Retrieve VNet Config
    $vnet = Get-AzVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
    
    # Retrieve Subnet Config
    $subnet = Get-AzVirtualNetworkSubnetConfig -Name $subnetName -VirtualNetwork $vnet
    
    # Insert the list of HDInsight owned resources for the region your clusters will be created in.
    [String[]]$resources = @("/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",
    "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",
    "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG")
    
    #Declare service endpoint policy definition
    $sepDef = New-AzServiceEndpointPolicyDefinition -Name "SEPHDICanadaCentral" -Description "Service Endpoint Policy Definition" -Service "Microsoft.Storage" -ServiceResource $resources
    
    # Service Endpoint Policy
    $sep= New-AzServiceEndpointPolicy -ResourceGroupName $rgName -Name "SEPHDICanadaCentral" -Location $location -ServiceEndpointPolicyDefinition $sepDef
    
    # Associate a subnet to the service endpoint policy just created. If there is a delay in updating it to subnet, you can use the Azure portal to associate the policy with the subnet.
    Set-AzVirtualNetworkSubnetConfig -Name $subnetName -VirtualNetwork $vnet -AddressPrefix $subnet.AddressPrefix -ServiceEndpointPolicy $sep
    ```

## <a name="next-steps"></a>Następne kroki

* [Architektura sieci wirtualnej usługi Azure HDInsight](hdinsight-virtual-network-architecture.md)
* [Konfigurowanie wirtualnego urządzenia sieciowego](./network-virtual-appliance.md)
