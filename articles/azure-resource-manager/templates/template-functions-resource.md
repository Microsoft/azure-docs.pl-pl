---
title: Funkcje szablonu — zasoby
description: Opisuje funkcje, które mają być używane w szablonie Azure Resource Manager (szablon ARM) do pobierania wartości dotyczących zasobów.
ms.topic: conceptual
ms.date: 02/10/2021
ms.openlocfilehash: efd7c554e6da8b60d4834d1a1290407a6b9e94d4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105544115"
---
# <a name="resource-functions-for-arm-templates"></a>Funkcje zasobów dla szablonów ARM

Menedżer zasobów udostępnia następujące funkcje do pobierania wartości zasobów w szablonie Azure Resource Manager (szablon ARM):

* [extensionResourceId](#extensionresourceid)
* [staw](#list)
* [pickZones](#pickzones)
* [udostępnia](#providers)
* [odwoła](#reference)
* [resourceGroup](#resourcegroup)
* [Identyfikator](#resourceid)
* [ramach](#subscription)
* [subscriptionResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

Aby uzyskać wartości z parametrów, zmiennych lub bieżącego wdrożenia, zobacz [funkcje wartości wdrożenia](template-functions-deployment.md).

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="extensionresourceid"></a>extensionResourceId

`extensionResourceId(resourceId, resourceType, resourceName1, [resourceName2], ...)`

Zwraca identyfikator zasobu dla [zasobu rozszerzenia](../management/extension-resource-types.md), który jest typem zasobu, który jest stosowany do innego zasobu, aby można go było dodać do jego możliwości.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| resourceId |Tak |ciąg |Identyfikator zasobu dla zasobu, do którego zastosowano zasób rozszerzenia. |
| resourceType |Tak |ciąg |Typ zasobu, w tym przestrzeń nazw dostawcy zasobów. |
| resourceName1 |Tak |ciąg |Nazwa zasobu. |
| resourceName2 |Nie |ciąg |Następny segment nazwy zasobu, w razie konieczności. |

Kontynuuj dodawanie nazw zasobów jako parametrów, gdy typ zasobu zawiera więcej segmentów.

### <a name="return-value"></a>Wartość zwracana

Podstawowy format identyfikatora zasobu zwracanego przez tę funkcję to:

```json
{scope}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Segment zakresu zależy od rozszerzonego zasobu.

Gdy zasób rozszerzenia zostanie zastosowany do **zasobu**, identyfikator zasobu jest zwracany w następującym formacie:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{baseResourceProviderNamespace}/{baseResourceType}/{baseResourceName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Gdy zasób rozszerzenia zostanie zastosowany do **grupy zasobów**, format jest:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Gdy zasób rozszerzenia zostanie zastosowany do **subskrypcji**, format jest:

```json
/subscriptions/{subscriptionId}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Gdy zasób rozszerzenia zostanie zastosowany do **grupy zarządzania**, format jest:

```json
/providers/Microsoft.Management/managementGroups/{managementGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

### <a name="extensionresourceid-example"></a>przykład extensionResourceId

Poniższy przykład zwraca identyfikator zasobu dla blokady grupy zasobów.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "lockName": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "lockResourceId": {
      "type": "string",
      "value": "[extensionResourceId(resourceGroup().Id , 'Microsoft.Authorization/locks', parameters('lockName'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param lockName string

output lockResourceId string = extensionResourceId(resourceGroup().Id, 'Microsoft.Authorization/locks', lockName)
```

---

Definicja zasad niestandardowych wdrożona w grupie zarządzania jest zaimplementowana jako zasób rozszerzenia. Aby utworzyć i przypisać zasady, wdróż następujący szablon w grupie zarządzania.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/quickstart-templates/managementgroup-deployments/mg-policy/azuredeploy.json":::

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param targetMG string
param allowedLocations array = [
  'australiaeast'
  'australiasoutheast'
  'australiacentral'
]

var mgScope = tenantResourceId('Microsoft.Management/managementGroups', targetMG)
var policyDefinition = 'LocationRestriction'

resource myDefinition 'Microsoft.Authorization/policyDefinitions@2019-09-01' = {
  name: policyDefinition
  properties: {
    policyType: 'Custom'
    mode: 'All'
    parameters: {}
    policyRule: {
      'if': {
        'not': {
          'field': 'location'
          'in': allowedLocations
        }
      }
      'then': {
        'effect': 'deny'
      }
    }
  }
}

resource myAssignment 'Microsoft.Authorization/policyAssignments@2019-09-01' = {
  name: 'location-lock'
  properties: {
    scope: mgScope
    policyDefinitionId: extensionResourceId(mgScope, 'Microsoft.Authorization/policyDefinitions', policyDefinition)
  }
}
```

---

Wbudowane definicje zasad to zasoby na poziomie dzierżawy. Aby zapoznać się z przykładem wdrażania wbudowanej definicji zasad, zobacz [tenantResourceId](#tenantresourceid).

<a id="listkeys"></a>
<a id="list"></a>

## <a name="list"></a>staw

`list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)`

Składnia tej funkcji różni się od nazwy operacji na liście. Każda implementacja zwraca wartości dla typu zasobu, który obsługuje operację listy. Nazwa operacji musi rozpoczynać się od `list` i może mieć sufiks. Niektóre typowe zastosowania to `list` ,, `listKeys` `listKeyValue` , i `listSecrets` .

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| resourceName lub resourceIdentifier |Tak |ciąg |Unikatowy identyfikator zasobu. |
| apiVersion |Tak |ciąg |Wersja interfejsu API stanu środowiska uruchomieniowego zasobu. Zwykle w formacie **rrrr-mm-dd**. |
| functionValues |Nie |object | Obiekt, który zawiera wartości dla funkcji. Podaj tylko ten obiekt dla funkcji, które obsługują otrzymywanie obiektów z wartościami parametrów, takimi jak **listAccountSas** na koncie magazynu. Przykład przekazywania wartości funkcji przedstawiono w tym artykule. |

### <a name="valid-uses"></a>Prawidłowe zastosowania

Funkcji list można używać we właściwościach definicji zasobu. Nie używaj funkcji list, która uwidacznia poufne informacje w sekcji dane wyjściowe szablonu. Wartości wyjściowe są przechowywane w historii wdrożenia i mogą być pobierane przez złośliwego użytkownika.

Gdy jest używany z [iteracją właściwości](copy-properties.md), można użyć funkcji listy dla, `input` ponieważ wyrażenie jest przypisane do właściwości zasobów. Nie można ich używać z, `count` ponieważ należy określić liczbę przed rozliczeniem funkcji listy.

### <a name="implementations"></a>Implementacje

W poniższej tabeli przedstawiono możliwe zastosowania list *.

| Typ zasobu | Nazwa funkcji |
| ------------- | ------------- |
| Microsoft. Dodatki/supportProviders | listsupportplaninfo |
| Microsoft. AnalysisServices/serwery | [listGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft. ApiManagement/Service/authorizationServers | [listSecrets](/rest/api/apimanagement/2019-12-01/authorizationserver/listsecrets) |
| Microsoft. ApiManagement/Service/Gateways | [listKeys](/rest/api/apimanagement/2019-12-01/gateway/listkeys) |
| Microsoft. ApiManagement/Service/skojarzeni | [listSecrets](/rest/api/apimanagement/2019-12-01/identityprovider/listsecrets) |
| Microsoft. ApiManagement/Service/namedValues | [Wartość listy](/rest/api/apimanagement/2019-12-01/namedvalue/listvalue) |
| Microsoft. ApiManagement/Service/openidConnectProviders | [listSecrets](/rest/api/apimanagement/2019-12-01/openidconnectprovider/listsecrets) |
| Microsoft. ApiManagement/Service/subscriptions | [listSecrets](/rest/api/apimanagement/2019-12-01/subscription/listsecrets) |
| Microsoft. AppConfiguration/configurationStores | [ListKeys](/rest/api/appconfiguration/configurationstores/listkeys) |
| Microsoft. AppPlatform/Sprężyna | [listTestKeys](/rest/api/azurespringcloud/services/listtestkeys) |
| Microsoft. Automation/automationAccounts | [listKeys](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.Batch/batchAccounts | [listkeys](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft.BatchAI/obszary robocze/eksperymenty/zadania | [listoutputfiles](/rest/api/batchai/jobs/listoutputfiles) |
| Microsoft. łańcucha bloków/blockchainMembers | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Microsoft. łańcucha bloków/blockchainMembers/transactionNodes | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Microsoft. BotService/botServices/Channels | [listChannelWithKeys](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/botservice/resource-manager/Microsoft.BotService/stable/2020-06-02/botservice.json#L553) |
| Microsoft. cache/Redis | [listKeys](/rest/api/redis/redis/listkeys) |
| Microsoft. CognitiveServices/konta | [listKeys](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Microsoft. ContainerRegistry/rejestry | [listBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/getbuildsourceuploadurl) |
| Microsoft. ContainerRegistry/rejestry | [listCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Microsoft. ContainerRegistry/rejestry | [listUsages](/rest/api/containerregistry/registries/listusages) |
| Microsoft. ContainerRegistry/rejestry/agentpools | listQueueStatus |
| Microsoft. ContainerRegistry/rejestry/buildTasks | listSourceRepositoryProperties |
| Microsoft. ContainerRegistry/rejestry/buildTasks/kroki | listBuildArguments |
| Microsoft. ContainerRegistry/rejestry/taskruns | listDetails |
| Microsoft. ContainerRegistry/rejestry/elementy webhook | [listEvents](/rest/api/containerregistry/webhooks/listevents) |
| Microsoft. ContainerRegistry/rejestry/uruchomienia | [listLogSasUrl](/rest/api/containerregistry/runs/getlogsasurl) |
| Microsoft. ContainerRegistry/rejestry/zadania | [listDetails](/rest/api/containerregistry/tasks/getdetails) |
| Microsoft. ContainerService/managedClusters | [listClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft. ContainerService/managedClusters | [listClusterMonitoringUserCredential](/rest/api/aks/managedclusters/listclustermonitoringusercredentials) |
| Microsoft. ContainerService/managedClusters | [listClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Microsoft. ContainerService/managedClusters/accessProfiles | [listCredential](/rest/api/aks/managedclusters/getaccessprofile) |
| Microsoft. DataBox/zadania | listCredentials |
| Microsoft. DataFactory/datafactors/Gateways | listauthkeys |
| Microsoft. DataFactory/Factors/integrationruntimes | [listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft. DataLakeAnalytics/accounts/storageAccounts/Containers | [listSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Microsoft. dataudział/konta/udziały | [listSynchronizations](/rest/api/datashare/shares/listsynchronizations) |
| Microsoft. datashare/accounts/shareSubscriptions | [listSourceShareSynchronizationSettings](/rest/api/datashare/sharesubscriptions/listsourcesharesynchronizationsettings) |
| Microsoft. datashare/accounts/shareSubscriptions | [listSynchronizationDetails](/rest/api/datashare/sharesubscriptions/listsynchronizationdetails) |
| Microsoft. datashare/accounts/shareSubscriptions | [listSynchronizations](/rest/api/datashare/sharesubscriptions/listsynchronizations) |
| Microsoft. Devices/iotHubs | [listkeys](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft. Devices/iotHubs/iotHubKeys | [listkeys](/rest/api/iothub/iothubresource/getkeysforkeyname) |
| Microsoft. Devices/provisioningServices/klucze | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft. Devices/provisioningServices | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft. wspólny/laboratoria | [ListVhds](/rest/api/dtl/labs/listvhds) |
| Microsoft. wspólny/Labs/harmonogramy | [ListApplicable](/rest/api/dtl/schedules/listapplicable) |
| Microsoft. wspólny/Labs/użytkownicy/servicefabrics | [ListApplicableSchedules](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft. wspólny/Labs/virtualMachines | [ListApplicableSchedules](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft.DocumentDB/databaseAccounts | [listConnectionStrings](/rest/api/cosmos-db-resource-provider/2021-03-01-preview/databaseaccounts/listconnectionstrings) |
| Microsoft.DocumentDB/databaseAccounts | [listKeys](/rest/api/cosmos-db-resource-provider/2021-03-01-preview/databaseaccounts/listkeys) |
| Microsoft.DocumentDB/databaseAccounts/notebookWorkspaces | [listConnectionInfo](/rest/api/cosmos-db-resource-provider/2021-03-01-preview/notebookworkspaces/listconnectioninfo) |
| Microsoft. DomainRegistration | [listDomainRecommendations](/rest/api/appservice/domains/listrecommendations) |
| Microsoft. DomainRegistration/topLevelDomains | [listAgreements](/rest/api/appservice/topleveldomains/listagreements) |
| Microsoft. EventGrid/domeny | [listKeys](/rest/api/eventgrid/version2020-06-01/domains/listsharedaccesskeys) |
| Microsoft. EventGrid/tematy | [listKeys](/rest/api/eventgrid/version2020-06-01/topics/listsharedaccesskeys) |
| Microsoft. EventHub/przestrzenie nazw/reguł autoryzacji | [listkeys](/rest/api/eventhub) |
| Microsoft. EventHub/przestrzenie nazw/disasterRecoveryConfigs/reguł autoryzacji | [listkeys](/rest/api/eventhub) |
| Microsoft. EventHub/przestrzenie nazw/eventhubs/reguł autoryzacji | [listkeys](/rest/api/eventhub) |
| Microsoft. ImportExport/zadania | [listBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Microsoft. Kusto/klastry/bazy danych | [ListPrincipals](/rest/api/azurerekusto/databases/listprincipals) |
| Microsoft. LabServices/użytkownicy | [ListEnvironments](/rest/api/labservices/globalusers/listenvironments) |
| Microsoft. LabServices/użytkownicy | [ListLabs](/rest/api/labservices/globalusers/listlabs) |
| Microsoft. Logic/integrationAccounts/umowy | [listContentCallbackUrl](/rest/api/logic/agreements/listcontentcallbackurl) |
| Microsoft. Logic/integrationAccounts/zestawy | [listContentCallbackUrl](/rest/api/logic/integrationaccountassemblies/listcontentcallbackurl) |
| Microsoft. Logic/integrationAccounts | [listCallbackUrl](/rest/api/logic/integrationaccounts/getcallbackurl) |
| Microsoft. Logic/integrationAccounts | [listKeyVaultKeys](/rest/api/logic/integrationaccounts/listkeyvaultkeys) |
| Microsoft. Logic/integrationAccounts/Maps | [listContentCallbackUrl](/rest/api/logic/maps/listcontentcallbackurl) |
| Microsoft. Logic/integrationAccounts/partnerzy | [listContentCallbackUrl](/rest/api/logic/partners/listcontentcallbackurl) |
| Microsoft. Logic/integrationAccounts/schematy | [listContentCallbackUrl](/rest/api/logic/schemas/listcontentcallbackurl) |
| Microsoft. Logic/przepływy pracy | [listCallbackUrl](/rest/api/logic/workflows/listcallbackurl) |
| Microsoft. Logic/przepływy pracy | [listSwagger](/rest/api/logic/workflows/listswagger) |
| Microsoft. Logic/przepływy pracy/uruchomienia/akcje | [listExpressionTraces](/rest/api/logic/workflowrunactions/listexpressiontraces) |
| Microsoft. Logic/przepływy pracy/uruchomienia/akcje/powtórzenia | [listExpressionTraces](/rest/api/logic/workflowrunactionrepetitions/listexpressiontraces) |
| Microsoft. Logic/przepływy pracy/wyzwalacze | [listCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Microsoft. Logic/przepływy pracy/wersje/wyzwalacze | [listCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Microsoft. MachineLearning/WebServices | [listkeys](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft. MachineLearning/obszary robocze | listworkspacekeys |
| Microsoft. MachineLearningServices/obszary robocze/obliczenia | [listKeys](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listkeys) |
| Microsoft. MachineLearningServices/obszary robocze/obliczenia | [listNodes](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listnodes) |
| Microsoft. MachineLearningServices/obszary robocze | [listKeys](/rest/api/azureml/workspacesandcomputes/workspaces/listkeys) |
| Microsoft. Maps/konta | [listKeys](/rest/api/maps-management/accounts/listkeys) |
| Microsoft. Media/MediaServices/zasoby | [listContainerSas](/rest/api/media/assets/listcontainersas) |
| Microsoft. Media/MediaServices/zasoby | [listStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Microsoft. Media/MediaServices/streamingLocators | [listContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft. Media/MediaServices/streamingLocators | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| Microsoft. Network/applicationSecurityGroups | listIpConfigurations |
| Microsoft. NotificationHubs/przestrzenie nazw/reguł autoryzacji | [listkeys](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft. NotificationHubs/Namespaces/NotificationHubs/reguł autoryzacji | [listkeys](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft. OperationalInsights/obszary robocze | [list](/rest/api/loganalytics/workspaces/list) |
| Microsoft. OperationalInsights/obszary robocze | listKeys |
| Microsoft. PolicyInsights/korygowania | [listDeployments](/rest/api/policy/remediations/listdeploymentsatresourcegroup) |
| Microsoft. RedHatOpenShift/openShiftClusters | [listCredentials](/rest/api/openshift/openshiftclusters/listcredentials) |
| Microsoft. Relay/przestrzenie nazw/reguł autoryzacji | [listkeys](/rest/api/relay/namespaces/listkeys) |
| Microsoft. Relay/przestrzenie nazw/disasterRecoveryConfigs/reguł autoryzacji | listkeys |
| Microsoft. Relay/przestrzenie nazw/HybridConnections/reguł autoryzacji | [listkeys](/rest/api/relay/hybridconnections/listkeys) |
| Microsoft. Relay/przestrzenie nazw/WcfRelays/reguł autoryzacji | [listkeys](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft. Search/searchServices | [listAdminKeys](/rest/api/searchmanagement/adminkeys/get) |
| Microsoft. Search/searchServices | [listQueryKeys](/rest/api/searchmanagement/querykeys/listbysearchservice) |
| Microsoft. ServiceBus/przestrzenie nazw/reguł autoryzacji | [listkeys](/rest/api/servicebus/stable/namespaces%20-%20authorization%20rules/listkeys) |
| Microsoft. ServiceBus/Namespaces/disasterRecoveryConfigs/reguł autoryzacji | [listkeys](/rest/api/servicebus/stable/disasterrecoveryconfigs/listkeys) |
| Microsoft. ServiceBus/przestrzenie nazw/kolejki/reguł autoryzacji | [listkeys](/rest/api/servicebus/stable/queues%20-%20authorization%20rules/listkeys) |
| Microsoft. ServiceBus/przestrzenie nazw/tematy/reguł autoryzacji | [listkeys](/rest/api/servicebus/stable/topics%20–%20authorization%20rules/listkeys) |
| Microsoft. SignalRService/sygnalizujący | [listkeys](/rest/api/signalr/signalr/listkeys) |
| Microsoft. Storage/storageAccounts | [listAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft. Storage/storageAccounts | [listkeys](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft. Storage/storageAccounts | [listServiceSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Microsoft. StorSimple/menedżerowie/urządzenia | [listFailoverSets](/rest/api/storsimple/devices/listfailoversets) |
| Microsoft. StorSimple/menedżerowie/urządzenia | [listFailoverTargets](/rest/api/storsimple/devices/listfailovertargets) |
| Microsoft. StorSimple/menedżerowie | [listActivationKey](/rest/api/storsimple/managers/getactivationkey) |
| Microsoft. StorSimple/menedżerowie | [listPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Microsoft. Synapse/Workspaces/integrationRuntimes | [listAuthKeys](/rest/api/synapse/integrationruntimeauthkeys/list) |
| Microsoft. Web/connectionGateways | ListStatus |
| Microsoft. Web/Connections | listconsentlinks |
| Microsoft. Web/customApis | listWsdlInterfaces |
| Microsoft. Web/lokalizacje | listwsdlinterfaces |
| Microsoft. Web/apimanagementaccounts/interfejsy API/połączenia | listconnectionkeys |
| Microsoft. Web/apimanagementaccounts/interfejsy API/połączenia | listsecrets |
| Microsoft. Web/Sites/kopie zapasowe | [list](/rest/api/appservice/webapps/listbackups) |
| Microsoft. Web/Sites/config | [list](/rest/api/appservice/webapps/listconfigurations) |
| Microsoft. Web/Sites/Functions | [listkeys](/rest/api/appservice/webapps/listfunctionkeys)
| Microsoft. Web/Sites/Functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| Microsoft. Web/Sites/hybridconnectionnamespaces/Przekaźniki | [listkeys](/rest/api/appservice/appserviceplans/listhybridconnectionkeys) |
| Microsoft. Web/witryny | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| Microsoft. Web/Sites/Slots/Functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |
| Microsoft. Web/Sites/gniazda/kopie zapasowe | [list](/rest/api/appservice/webapps/listbackupsslot) |
| Microsoft. Web/Sites/szczeliny/konfiguracja | [list](/rest/api/appservice/webapps/listconfigurationsslot) |
| Microsoft. Web/Sites/Slots/Functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |

Aby określić, które typy zasobów mają operację listy, dostępne są następujące opcje:

* Wyświetlanie [operacji interfejsu API REST](/rest/api/) dla dostawcy zasobów i wyszukiwanie list operacji. Na przykład konta magazynu mają [operację listKeys](/rest/api/storagerp/storageaccounts).
* Użyj polecenia cmdlet programu PowerShell [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) . Poniższy przykład pobiera wszystkie operacje na listach dla kont magazynu:

  ```powershell
  Get-AzProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```

* Następujące polecenie interfejsu wiersza polecenia platformy Azure służy do filtrowania tylko operacji na listach:

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

### <a name="return-value"></a>Wartość zwracana

Zwrócony obiekt różni się w zależności od używanej funkcji list. Na przykład listKeys dla konta magazynu zwraca następujący format:

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

Inne funkcje list mają różne formaty Return. Aby wyświetlić format funkcji, należy dołączyć ją w sekcji dane wyjściowe, jak pokazano w przykładowym szablonie.

### <a name="remarks"></a>Uwagi

Określ zasób przy użyciu nazwy zasobu lub [funkcji ResourceID](#resourceid). W przypadku używania funkcji list w tym samym szablonie, który wdraża przywoływany zasób, należy użyć nazwy zasobu.

Jeśli używasz funkcji **list** w zasobie, który jest wdrażany warunkowo, funkcja zostanie oceniona, nawet jeśli zasób nie zostanie wdrożony. Występuje błąd, jeśli funkcja **list** odwołuje się do zasobu, który nie istnieje. Użyj funkcji **if** , aby upewnić się, że funkcja jest obliczana tylko wtedy, gdy zasób jest wdrażany. Zobacz [funkcję if](template-functions-logical.md#if) , aby zapoznać się z przykładowym szablonem, który używa elementu if i z użyciem warunkowo wdrożonego zasobu.

### <a name="list-example"></a>Przykład listy

Poniższy przykład używa listKeys podczas ustawiania wartości dla [skryptów wdrażania](deployment-script-template.md).

# <a name="json"></a>[JSON](#tab/json)

```json
"storageAccountSettings": {
  "storageAccountName": "[variables('storageAccountName')]",
  "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
storageAccountSettings: {
  storageAccountName: storageAccountName
  storageAccountKey: listKeys(resourceId('Microsoft.Storage/storageAccounts', storageAccountName), '2019-06-01').keys[0].value
}

```

---

W następnym przykładzie pokazano funkcję listy, która przyjmuje parametr. W takim przypadku funkcja jest **listAccountSas**. Przekaż obiekt przez czas wygaśnięcia. Czas wygaśnięcia musi przypadać w przyszłości.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "accountSasProperties": {
    "type": "object",
    "defaultValue": {
      "signedServices": "b",
      "signedPermission": "r",
      "signedExpiry": "2020-08-20T11:00:00Z",
      "signedResourceTypes": "s"
    }
  }
},
...
"sasToken": "[listAccountSas(parameters('storagename'), '2018-02-01', parameters('accountSasProperties')).accountSasToken]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param accountSasProperties object {
  default: {
    signedServices: 'b'
    signedPermission: 'r'
    signedExpiry: '2020-08-20T11:00:00Z'
    signedResourceTypes: 's'
  }
}
...
sasToken: listAccountSas(storagename, '2018-02-01', accountSasProperties).accountSasToken
```

---

## <a name="pickzones"></a>pickZones

`pickZones(providerNamespace, resourceType, location, [numberOfZones], [offset])`

Określa, czy typ zasobu obsługuje strefy dla regionu.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| providerNamespace | Tak | ciąg | Przestrzeń nazw dostawcy zasobów dla typu zasobu, aby sprawdzić obsługę strefy. |
| resourceType | Tak | ciąg | Typ zasobu służący do sprawdzania obsługi strefy. |
| location | Tak | ciąg | Region, w którym ma zostać wyszukana obsługa strefy. |
| numberOfZones | Nie | liczba całkowita | Liczba stref logicznych do zwrócenia. Wartość domyślna to 1. Liczba musi być dodatnią liczbą całkowitą z przedziału od 1 do 3.  W przypadku zasobów z jedną strefą Użyj wartości 1. W przypadku zasobów z obsługą wielu stref wartość musi być mniejsza lub równa liczbie obsługiwanych stref. |
| przesunięcie | Nie | liczba całkowita | Przesunięcie od początkowej strefy logicznej. Funkcja zwraca błąd, jeśli Offset plus numberOfZones przekracza liczbę obsługiwanych stref. |

### <a name="return-value"></a>Wartość zwracana

Tablica z obsługiwanymi strefami. W przypadku używania wartości domyślnych dla przesunięcia i numberOfZones, typ zasobu i region obsługujący strefy zwracają następującą tablicę:

```json
[
    "1"
]
```

Gdy `numberOfZones` parametr jest ustawiony na 3, zwraca:

```json
[
    "1",
    "2",
    "3"
]
```

Gdy typ zasobu lub region nie obsługuje stref, zwracana jest pusta tablica.

```json
[
]
```

### <a name="pickzones-example"></a>przykład pickZones

Poniższy szablon przedstawia trzy wyniki przy użyciu funkcji pickZones.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "functions": [],
  "variables": {},
  "resources": [],
  "outputs": {
    "supported": {
      "type": "array",
      "value": "[pickZones('Microsoft.Compute', 'virtualMachines', 'westus2')]"
    },
    "notSupportedRegion": {
      "type": "array",
      "value": "[pickZones('Microsoft.Compute', 'virtualMachines', 'northcentralus')]"
    },
    "notSupportedType": {
      "type": "array",
      "value": "[pickZones('Microsoft.Cdn', 'profiles', 'westus2')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output supported array = pickZones('Microsoft.Compute', 'virtualMachines', 'westus2')
output notSupportedRegion array = pickZones('Microsoft.Compute', 'virtualMachines', 'northcentralus')
output notSupportedType array = pickZones('Microsoft.Cdn', 'profiles', 'westus2')
```

---

Dane wyjściowe z powyższych przykładów zwracają trzy tablice.

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| Obsługiwane | array | ["1"] |
| notSupportedRegion | array | [] |
| notSupportedType | array | [] |

Możesz użyć odpowiedzi z pickZones, aby określić, czy podać wartość null dla stref lub przypisać maszyny wirtualne do różnych stref. Poniższy przykład ustawia wartość dla strefy na podstawie dostępności stref.

# <a name="json"></a>[JSON](#tab/json)

```json
"zones": {
  "value": "[if(not(empty(pickZones('Microsoft.Compute', 'virtualMachines', 'westus2'))), string(add(mod(copyIndex(),3),1)), json('null'))]"
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> Pętle i funkcji copyindex () nie są jeszcze zaimplementowane.  Zobacz [pętle](https://github.com/Azure/bicep/blob/main/docs/spec/loops.md).

---

## <a name="providers"></a>dostawców

`providers(providerNamespace, [resourceType])`

Zwraca informacje o dostawcy zasobów i jego obsługiwanych typach zasobów. Jeśli nie podano typu zasobu, funkcja zwraca wszystkie obsługiwane typy dla dostawcy zasobów.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| providerNamespace |Tak |ciąg |Przestrzeń nazw dostawcy |
| resourceType |Nie |ciąg |Typ zasobu w określonym obszarze nazw. |

### <a name="return-value"></a>Wartość zwracana

Każdy obsługiwany typ jest zwracany w następującym formacie:

```json
{
  "resourceType": "{name of resource type}",
  "locations": [ all supported locations ],
  "apiVersions": [ all supported API versions ]
}
```

Kolejność tablic dla zwracanych wartości nie jest gwarantowana.

### <a name="providers-example"></a>Przykład dostawcy

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json) pokazuje, jak używać funkcji dostawcy:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "providerNamespace": {
      "type": "string"
    },
    "resourceType": {
      "type": "string"
    }
  },
  "resources": [],
  "outputs": {
    "providerOutput": {
      "type": "object",
      "value": "[providers(parameters('providerNamespace'), parameters('resourceType'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param providerNamespace string
param resourceType string

output providerOutput array = providers(providerNamespace, resourceType)
```

---

W przypadku zasobu dostawcy zasobów **Microsoft. Web** i typu **witryny** powyższy przykład zwraca obiekt w następującym formacie:

```json
{
  "resourceType": "sites",
  "locations": [
    "South Central US",
    "North Europe",
    "West Europe",
    "Southeast Asia",
    ...
  ],
  "apiVersions": [
    "2016-08-01",
    "2016-03-01",
    "2015-08-01-preview",
    "2015-08-01",
    ...
  ]
}
```

## <a name="reference"></a>reference

`reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])`

Zwraca obiekt reprezentujący stan środowiska uruchomieniowego zasobu.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| resourceName lub resourceIdentifier |Tak |ciąg |Nazwa lub unikatowy identyfikator zasobu. W przypadku odwoływania się do zasobu w bieżącym szablonie podaj tylko nazwę zasobu jako parametr. W przypadku odwoływania się do wcześniej wdrożonego zasobu lub gdy nazwa zasobu jest niejednoznaczna, podaj identyfikator zasobu. |
| apiVersion |Nie |ciąg |Wersja interfejsu API określonego zasobu. **Ten parametr jest wymagany, jeśli zasób nie jest obsługiwany w ramach tego samego szablonu.** Zwykle w formacie **rrrr-mm-dd**. Aby uzyskać prawidłowe wersje interfejsu API dla zasobu, zobacz [Dokumentacja szablonu](/azure/templates/). |
| Szczegółowe |Nie |ciąg |Wartość określająca, czy ma zostać zwrócony pełny obiekt zasobów. Jeśli nie zostanie określony `'Full'` , zwracany jest tylko obiekt właściwości zasobu. Pełny obiekt zawiera wartości, takie jak identyfikator zasobu i lokalizacja. |

### <a name="return-value"></a>Wartość zwracana

Każdy typ zasobu zwraca różne właściwości funkcji Reference. Funkcja nie zwraca pojedynczego, wstępnie zdefiniowanego formatu. Ponadto zwracana wartość różni się w zależności od wartości `'Full'` argumentu. Aby wyświetlić właściwości typu zasobu, zwróć obiekt w sekcji dane wyjściowe, jak pokazano w przykładzie.

### <a name="remarks"></a>Uwagi

Funkcja Reference Pobiera stan środowiska uruchomieniowego wcześniej wdrożonego zasobu lub zasobu wdrożonego w bieżącym szablonie. W tym artykule przedstawiono przykłady dla obu scenariuszy.

Zwykle funkcja **Reference** służy do zwracania określonej wartości z obiektu, takiego jak identyfikator URI punktu końcowego obiektu BLOB lub w pełni kwalifikowana nazwa domeny.

# <a name="json"></a>[JSON](#tab/json)

```json
"outputs": {
  "BlobUri": {
    "type": "string",
    "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))).primaryEndpoints.blob]"
  },
  "FQDN": {
    "type": "string",
    "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName'))).dnsSettings.fqdn]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output BlobUri string = reference(resourceId('Microsoft.Storage/storageAccounts', storageAccountName)).primaryEndpoints.blob
output FQDN string = reference(resourceId('Microsoft.Network/publicIPAddresses', ipAddressName)).dnsSettings.fqdn
```

---

Użyj, `'Full'` gdy potrzebujesz wartości zasobów, które nie są częścią schematu właściwości. Na przykład aby ustawić zasady dostępu do magazynu kluczy, Pobierz właściwości tożsamości dla maszyny wirtualnej.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "apiVersion": "2019-09-01",
  "name": "vaultName",
  "properties": {
    "tenantId": "[subscription().tenantId]",
    "accessPolicies": [
      {
        "tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.tenantId]",
        "objectId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.principalId]",
        "permissions": {
          "keys": [
            "all"
          ],
          "secrets": [
            "all"
          ]
        }
      }
    ],
    ...
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource myVault 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: 'vaultName'
  properties: {
    tenantId: subscription().tenantId
    accessPolicies: [
      {
        'tenantId': reference(resourceId('Microsoft.Compute/virtualMachines', vmName), '2019-03-01', 'Full').identity.tenantId
        'objectId': reference(resourceId('Microsoft.Compute/virtualMachines', vmName), '2019-03-01', 'Full').identity.principalId
        'permissions': {
          'keys': [
            'all'
          ]
          'secrets': [
            'all'
          ]
        }
      }
    ]
  }
}
```

---

### <a name="valid-uses"></a>Prawidłowe zastosowania

Funkcji Reference można używać tylko we właściwościach definicji zasobu i sekcji Output szablonu lub wdrożenia. Gdy jest używany z [iteracją właściwości](copy-properties.md), można użyć funkcji Reference dla, `input` ponieważ wyrażenie jest przypisane do właściwości Resource.

Nie można użyć funkcji Reference do ustawienia wartości `count` właściwości w pętli kopiowania. Można użyć, aby ustawić inne właściwości w pętli. Odwołanie jest blokowane dla właściwości count, ponieważ ta właściwość musi zostać określona przed rozwiązanym funkcją referencyjną.

Aby użyć funkcji Reference lub dowolnej funkcji list * w sekcji Output (dane wyjściowe) szablonu, należy ustawić  ```expressionEvaluationOptions``` do używania [wewnętrznej oceny zakresu](linked-templates.md#expression-evaluation-scope-in-nested-templates) lub użyć połączone zamiast szablonu zagnieżdżonego.

Jeśli używasz funkcji **Reference** w zasobie, który jest wdrażany warunkowo, funkcja jest oceniana, nawet jeśli zasób nie zostanie wdrożony.  Występuje błąd, jeśli funkcja **referencyjna** odwołuje się do zasobu, który nie istnieje. Użyj funkcji **if** , aby upewnić się, że funkcja jest obliczana tylko wtedy, gdy zasób jest wdrażany. Zobacz [funkcję if](template-functions-logical.md#if) , aby zapoznać się z przykładowym szablonem, który używa if i Reference z wdrożonym warunkowo zasobem.

### <a name="implicit-dependency"></a>Niejawna zależność

Za pomocą funkcji Reference, niejawnie deklaruje, że jeden zasób zależy od innego zasobu, jeśli zasób, do którego się odwoływano, jest inicjowany w ramach tego samego szablonu i odwołujesz się do zasobu według jego nazwy (nie identyfikatora zasobu). Nie trzeba również używać właściwości dependsOn. Funkcja nie jest szacowana do momentu zakończenia wdrożenia zasobu, do którego się odwoływano.

### <a name="resource-name-or-identifier"></a>Nazwa lub identyfikator zasobu

W przypadku odwoływania się do zasobu, który jest wdrożony w tym samym szablonie, podaj nazwę zasobu.

# <a name="json"></a>[JSON](#tab/json)

```json
"value": "[reference(parameters('storageAccountName'))]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
value: reference(storageAccountName)
```

---

W przypadku odwoływania się do zasobu, który nie jest wdrożony w tym samym szablonie, podaj identyfikator zasobu i `apiVersion` .

# <a name="json"></a>[JSON](#tab/json)

```json
"value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
value: reference(resourceId(storageResourceGroup, 'Microsoft.Storage/storageAccounts', storageAccountName), '2018-07-01')]"
```

---

Aby uniknąć niejednoznaczności, do której odwołuje się zasób, można podać w pełni kwalifikowany identyfikator zasobu.

# <a name="json"></a>[JSON](#tab/json)

```json
"value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName')))]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
value: reference(resourceId('Microsoft.Network/publicIPAddresses', ipAddressName))
```

---

W przypadku konstruowania w pełni kwalifikowanego odwołania do zasobu kolejność łączenia segmentów z typu i nazwy nie jest po prostu połączeniem obu. Zamiast tego, po przestrzeni nazw, należy użyć sekwencji par *typu/nazwa* od najmniej określonych do najbardziej szczegółowych:

**{Resource-Provider-Namespace}/{Parent-Resource-Type}/{Parent-Resource-Name} [/{Child-Resource-Type}/{Child-resource-name}]**

Na przykład:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt``Microsoft.Compute/virtualMachines/extensions/myVM/myExt`jest niepoprawny

Aby uprościć tworzenie dowolnego identyfikatora zasobu, użyj `resourceId()` funkcji opisanych w tym dokumencie zamiast `concat()` funkcji.

### <a name="get-managed-identity"></a>Pobierz tożsamość zarządzaną

[Zarządzane tożsamości dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md) to [typy zasobów rozszerzeń](../management/extension-resource-types.md) , które są tworzone niejawnie dla niektórych zasobów. Ponieważ zarządzana tożsamość nie jest jawnie zdefiniowana w szablonie, należy odwołać się do zasobu, do którego jest stosowana tożsamość. Użyj `Full` , aby pobrać wszystkie właściwości, w tym niejawnie utworzoną tożsamość.

Wzorzec to:

`"[reference(resourceId(<resource-provider-namespace>, <resource-name>, <API-version>, 'Full').Identity.propertyName]"`

Aby na przykład uzyskać identyfikator podmiotu zabezpieczeń dla tożsamości zarządzanej, która jest zastosowana do maszyny wirtualnej, należy użyć:

# <a name="json"></a>[JSON](#tab/json)

```json
"[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')),'2019-12-01', 'Full').identity.principalId]",
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
reference(resourceId('Microsoft.Compute/virtualMachines', vmName),'2019-12-01', 'Full').identity.principalId
```

---

Lub, aby uzyskać identyfikator dzierżawy dla tożsamości zarządzanej, która została zastosowana do zestawu skalowania maszyn wirtualnych, użyj:

# <a name="json"></a>[JSON](#tab/json)

```json
"[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), 2019-12-01, 'Full').Identity.tenantId]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  vmNodeType0Name), 2019-12-01, 'Full').Identity.tenantId
```

---

### <a name="reference-example"></a>Przykład odwołania

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json) wdraża zasób i odwołuje się do tego zasobu.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {
      "referenceOutput": {
        "type": "object",
        "value": "[reference(parameters('storageAccountName'))]"
      },
      "fullReferenceOutput": {
        "type": "object",
        "value": "[reference(parameters('storageAccountName'), '2016-12-01', 'Full')]"
      }
    }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageAccountName string

resource myStorage 'Microsoft.Storage/storageAccounts@2016-12-01' = {
  name: storageAccountName
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  tags: {}
  properties: {}
}

output referenceOutput object = reference(storageAccountName)
output fullReferenceOutput object = reference(storageAccountName, '2016-12-01', 'Full')
```

---

Powyższy przykład zwraca dwa obiekty. Obiekt właściwości ma następujący format:

```json
{
   "creationTime": "2017-10-09T18:55:40.5863736Z",
   "primaryEndpoints": {
     "blob": "https://examplestorage.blob.core.windows.net/",
     "file": "https://examplestorage.file.core.windows.net/",
     "queue": "https://examplestorage.queue.core.windows.net/",
     "table": "https://examplestorage.table.core.windows.net/"
   },
   "primaryLocation": "southcentralus",
   "provisioningState": "Succeeded",
   "statusOfPrimary": "available",
   "supportsHttpsTrafficOnly": false
}
```

Pełny obiekt ma następujący format:

```json
{
  "apiVersion":"2016-12-01",
  "location":"southcentralus",
  "sku": {
    "name":"Standard_LRS",
    "tier":"Standard"
  },
  "tags":{},
  "kind":"Storage",
  "properties": {
    "creationTime":"2017-10-09T18:55:40.5863736Z",
    "primaryEndpoints": {
      "blob":"https://examplestorage.blob.core.windows.net/",
      "file":"https://examplestorage.file.core.windows.net/",
      "queue":"https://examplestorage.queue.core.windows.net/",
      "table":"https://examplestorage.table.core.windows.net/"
    },
    "primaryLocation":"southcentralus",
    "provisioningState":"Succeeded",
    "statusOfPrimary":"available",
    "supportsHttpsTrafficOnly":false
  },
  "subscriptionId":"<subscription-id>",
  "resourceGroupName":"functionexamplegroup",
  "resourceId":"Microsoft.Storage/storageAccounts/examplestorage",
  "referenceApiVersion":"2016-12-01",
  "condition":true,
  "isConditionTrue":true,
  "isTemplateResource":false,
  "isAction":false,
  "provisioningOperation":"Read"
}
```

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/reference.json) odwołuje się do konta magazynu, które nie zostało wdrożone w tym szablonie. Konto magazynu już istnieje w ramach tej samej subskrypcji.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageResourceGroup": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    }
  },
  "resources": [],
  "outputs": {
    "ExistingStorage": {
      "type": "object",
      "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageResourceGroup string
param storageAccountName string

output ExistingStorage object = reference(resourceId(storageAccountName), 'Microsoft.Storage/storageAccounts', storageAccountName), '2018-07-01')
```

---

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

Zwraca obiekt, który reprezentuje bieżącą grupę zasobów.

### <a name="return-value"></a>Wartość zwracana

Zwrócony obiekt ma następujący format:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "{resourceGroupLocation}",
  "managedBy": "{identifier-of-managing-resource}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

Właściwość **zarządzane** jest zwracana tylko dla grup zasobów zawierających zasoby, które są zarządzane przez inną usługę. W przypadku aplikacji zarządzanych, datakostki i AKS wartość właściwości jest identyfikator zasobu zasobu zarządzania.

### <a name="remarks"></a>Uwagi

`resourceGroup()`Nie można użyć funkcji w szablonie [wdrożonym na poziomie subskrypcji](deploy-to-subscription.md). Może być używany tylko w szablonach wdrożonych w grupie zasobów. Możesz użyć `resourceGroup()` funkcji w [połączonym lub zagnieżdżonym szablonie (z zakresem wewnętrznym)](linked-templates.md) , który jest przeznaczony dla grupy zasobów, nawet jeśli szablon nadrzędny został wdrożony w subskrypcji. W tym scenariuszu szablon połączony lub zagnieżdżony jest wdrażany na poziomie grupy zasobów. Aby uzyskać więcej informacji na temat określania docelowych grup zasobów w ramach wdrożenia na poziomie subskrypcji, zobacz [wdrażanie zasobów platformy Azure w więcej niż jednej subskrypcji lub grupie zasobów](./deploy-to-resource-group.md).

Typowym zastosowaniem funkcji resourceing jest utworzenie zasobów w tej samej lokalizacji co grupa zasobów. Poniższy przykład używa lokalizacji grupy zasobów dla domyślnej wartości parametru.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param location string = resourceGroup().location
```

---

Można również użyć funkcji grupy zasobów, aby zastosować do zasobu Tagi ze źródła danych. Aby uzyskać więcej informacji, zobacz [stosowanie tagów z grupy zasobów](../management/tag-resources.md#apply-tags-from-resource-group).

W przypadku użycia szablonów zagnieżdżonych do wdrożenia w wielu grupach zasobów można określić zakres oceniania funkcji ResourceManager. Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów platformy Azure w więcej niż jednej subskrypcji lub grupie zasobów](./deploy-to-resource-group.md).

### <a name="resource-group-example"></a>Przykład grupy zasobów

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json) zwraca właściwości grupy zasobów.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "resourceGroupOutput": {
      "type" : "object",
      "value": "[resourceGroup()]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output resourceGroupOutput object = resourceGroup()
```

---

Poprzedni przykład zwraca obiekt w następującym formacie:

```json
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/examplegroup",
  "name": "examplegroup",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

## <a name="resourceid"></a>resourceId

`resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2], ...)`

Zwraca unikatowy identyfikator zasobu. Ta funkcja jest używana, gdy nazwa zasobu jest niejednoznaczna lub nie została zainicjowana w ramach tego samego szablonu. Format zwróconego identyfikatora różni się w zależności od tego, czy wdrożenie występuje w zakresie grupy zasobów, subskrypcji, grupy zarządzania czy dzierżawcy.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| subscriptionId |Nie |ciąg (w formacie identyfikatora GUID) |Wartość domyślna to bieżąca subskrypcja. Określ tę wartość, jeśli chcesz pobrać zasób w innej subskrypcji. Podaj tę wartość tylko podczas wdrażania w zakresie grupy zasobów lub subskrypcji. |
| resourceGroupName |Nie |ciąg |Wartość domyślna to bieżąca Grupa zasobów. Określ tę wartość, jeśli chcesz pobrać zasób z innej grupy zasobów. Podaj tę wartość tylko w przypadku wdrażania w zakresie grupy zasobów. |
| resourceType |Tak |ciąg |Typ zasobu, w tym przestrzeń nazw dostawcy zasobów. |
| resourceName1 |Tak |ciąg |Nazwa zasobu. |
| resourceName2 |Nie |ciąg |Następny segment nazwy zasobu, w razie konieczności. |

Kontynuuj dodawanie nazw zasobów jako parametrów, gdy typ zasobu zawiera więcej segmentów.

### <a name="return-value"></a>Wartość zwracana

Gdy szablon zostanie wdrożony w zakresie grupy zasobów, identyfikator zasobu jest zwracany w następującym formacie:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Można użyć funkcji resourceId dla innych zakresów wdrożenia, ale format identyfikatora zmienia się.

Jeśli używasz resourceId podczas wdrażania subskrypcji, identyfikator zasobu jest zwracany w następującym formacie:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Jeśli używasz resourceId podczas wdrażania do grupy zarządzania lub dzierżawy, identyfikator zasobu jest zwracany w następującym formacie:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Aby uniknąć nieporozumień, zalecamy użycie resourceId podczas pracy z zasobami wdrożonymi w subskrypcji, grupie zarządzania lub dzierżawie. Zamiast tego należy użyć funkcji identyfikatora, która została zaprojektowana dla zakresu.

W przypadku [zasobów na poziomie subskrypcji](deploy-to-subscription.md)należy użyć funkcji [subscriptionResourceId](#subscriptionresourceid) .

W przypadku [zasobów na poziomie grupy zarządzania](deploy-to-management-group.md)Użyj funkcji [extensionResourceId](#extensionresourceid) , aby odwołać się do zasobu, który jest zaimplementowany jako rozszerzenie grupy zarządzania. Na przykład niestandardowe definicje zasad wdrożone w grupie zarządzania to rozszerzenia grupy zarządzania. Użyj funkcji [tenantResourceId](#tenantresourceid) , aby odwoływać się do zasobów wdrożonych w dzierżawie, ale dostępnych w grupie zarządzania. Na przykład wbudowane definicje zasad są implementowane jako zasoby na poziomie dzierżawy.

W przypadku [zasobów na poziomie dzierżawy](deploy-to-tenant.md)Użyj funkcji [tenantResourceId](#tenantresourceid) . Użyj tenantResourceId dla wbudowanych definicji zasad, ponieważ są one implementowane na poziomie dzierżawy.

### <a name="remarks"></a>Uwagi

Liczba parametrów, które należy podać, zależy od tego, czy zasób jest zasobem nadrzędnym, czy podrzędnym, oraz czy zasób znajduje się w tej samej subskrypcji lub grupie zasobów.

Aby uzyskać identyfikator zasobu dla zasobu nadrzędnego w tej samej subskrypcji i grupie zasobów, podaj typ i nazwę zasobu.

# <a name="json"></a>[JSON](#tab/json)

```json
"[resourceId('Microsoft.ServiceBus/namespaces', 'namespace1')]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resourceId('Microsoft.ServiceBus/namespaces', 'namespace1')
```

---

Aby uzyskać identyfikator zasobu dla zasobu podrzędnego, należy zwrócić uwagę na liczbę segmentów w typie zasobu. Podaj nazwę zasobu dla każdego segmentu typu zasobu. Nazwa segmentu odpowiada zasobowi, który istnieje dla tej części hierarchii.

# <a name="json"></a>[JSON](#tab/json)

```json
"[resourceId('Microsoft.ServiceBus/namespaces/queues/authorizationRules', 'namespace1', 'queue1', 'auth1')]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resourceId('Microsoft.ServiceBus/namespaces/queues/authorizationRules', 'namespace1', 'queue1', 'auth1')
```

---

Aby uzyskać identyfikator zasobu dla zasobu w tej samej subskrypcji, ale w innej grupie zasobów, podaj nazwę grupy zasobów.

# <a name="json"></a>[JSON](#tab/json)

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts', 'examplestorage')]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts', 'examplestorage')
```

---

Aby uzyskać identyfikator zasobu dla zasobu w innej subskrypcji i grupie zasobów, podaj identyfikator subskrypcji i nazwę grupy zasobów.

# <a name="json"></a>[JSON](#tab/json)

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')
```

---

Często należy używać tej funkcji w przypadku korzystania z konta magazynu lub sieci wirtualnej w alternatywnej grupie zasobów. Poniższy przykład pokazuje, jak można łatwo używać zasobu z zewnętrznej grupy zasobów:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "virtualNetworkName": {
      "type": "string"
    },
    "virtualNetworkResourceGroup": {
      "type": "string"
    },
    "subnet1Name": {
      "type": "string"
    },
    "nicName": {
      "type": "string"
    }
  },
  "variables": {
    "subnet1Ref": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnet1Name'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Network/networkInterfaces",
      "apiVersion": "2015-05-01-preview",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "subnet": {
                "id": "[variables('subnet1Ref')]"
              }
            }
          }
        ]
      }
    }
  ]
}
```
# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param location string
param virtualNetworkName string
param virtualNetworkResourceGroup string
param subnet1Name string
param nicName string

var subnet1Ref = resourceId('virtualNetworkResourceGroup', 'Microsoft.Network/virtualNetworks/subnets', 'virtualNetworkName', 'subnet1Name')

resource myInterface 'Microsoft.Network/networkInterfaces@2015-05-01-preview' = {
  name: nicName
  location: location
  properties: {
    ipConfigurations: [
      {
        name: 'ipconfig1'
        properties: {
          privateIPAllocationMethod: 'Dynamic'
          subnet: {
            id: subnet1Ref
          }
        }
      }
    ]
  }
}
```

---


### <a name="resource-id-example"></a>Przykład identyfikatora zasobu

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json) zwraca identyfikator zasobu dla konta magazynu w grupie zasobów:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "sameRGOutput": {
      "type": "string",
      "value": "[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]"
    },
    "differentRGOutput": {
      "type": "string",
      "value": "[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
    },
    "differentSubOutput": {
      "type": "string",
      "value": "[resourceId('11111111-1111-1111-1111-111111111111', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
    },
    "nestedResourceOutput": {
      "type": "string",
      "value": "[resourceId('Microsoft.SQL/servers/databases', 'serverName', 'databaseName')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output sameRGOutput string = resourceId('Microsoft.Storage/storageAccounts','examplestorage')
output differentRGOutput string = resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')
output differentSubOutput string = resourceId('11111111-1111-1111-1111-111111111111', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')
output nestedResourceOutput string = resourceId('Microsoft.SQL/servers/databases', 'serverName', 'databaseName')
```

---

Dane wyjściowe z poprzedniego przykładu z wartościami domyślnymi są następujące:

| Nazwa | Typ | Wartość |
| ---- | ---- | ----- |
| sameRGOutput | Ciąg | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | Ciąg | /subscriptions/{current-sub-id}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | Ciąg | /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | Ciąg | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/serverName/databases/databaseName |

## <a name="subscription"></a>subskrypcja

`subscription()`

Zwraca szczegółowe informacje o subskrypcji dla bieżącego wdrożenia.

### <a name="return-value"></a>Wartość zwracana

Funkcja zwraca następujący format:

```json
{
  "id": "/subscriptions/{subscription-id}",
  "subscriptionId": "{subscription-id}",
  "tenantId": "{tenant-id}",
  "displayName": "{name-of-subscription}"
}
```

### <a name="remarks"></a>Uwagi

W przypadku używania szablonów zagnieżdżonych do wdrażania w wielu subskrypcjach można określić zakres oceniania funkcji subskrypcji. Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów platformy Azure w więcej niż jednej subskrypcji lub grupie zasobów](./deploy-to-resource-group.md).

### <a name="subscription-example"></a>Przykład subskrypcji

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json) pokazuje funkcję subskrypcji o nazwie w sekcji dane wyjściowe.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "subscriptionOutput": {
      "value": "[subscription()]",
      "type" : "object"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output subscriptionOutput object = subscription()
```

---

## <a name="subscriptionresourceid"></a>subscriptionResourceId

`subscriptionResourceId([subscriptionId], resourceType, resourceName1, [resourceName2], ...)`

Zwraca unikatowy identyfikator zasobu wdrożonego na poziomie subskrypcji.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| subscriptionId |Nie |ciąg (w formacie identyfikatora GUID) |Wartość domyślna to bieżąca subskrypcja. Określ tę wartość, jeśli chcesz pobrać zasób w innej subskrypcji. |
| resourceType |Tak |ciąg |Typ zasobu, w tym przestrzeń nazw dostawcy zasobów. |
| resourceName1 |Tak |ciąg |Nazwa zasobu. |
| resourceName2 |Nie |ciąg |Następny segment nazwy zasobu, w razie konieczności. |

Kontynuuj dodawanie nazw zasobów jako parametrów, gdy typ zasobu zawiera więcej segmentów.

### <a name="return-value"></a>Wartość zwracana

Identyfikator jest zwracany w następującym formacie:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Uwagi

Ta funkcja służy do pobierania identyfikatora zasobu dla zasobów [wdrożonych w subskrypcji](deploy-to-subscription.md) , a nie w grupie zasobów. Zwrócony identyfikator różni się od wartości zwracanej przez funkcję [ResourceID](#resourceid) przez nie uwzględniając wartości grupy zasobów.

### <a name="subscriptionresourceid-example"></a>przykład subscriptionResourceID

Poniższy szablon przypisuje wbudowaną rolę. Można wdrożyć je w grupie zasobów lub w ramach subskrypcji. Używa funkcji subscriptionResourceId, aby uzyskać identyfikator zasobu dla ról wbudowanych.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The principal to assign the role to"
      }
    },
    "builtInRoleType": {
      "type": "string",
      "allowedValues": [
        "Owner",
        "Contributor",
        "Reader"
      ],
      "metadata": {
        "description": "Built-in role to assign"
      }
    },
    "roleNameGuid": {
      "type": "string",
      "defaultValue": "[newGuid()]",
      "metadata": {
        "description": "A new GUID used to identify the role assignment"
      }
    }
  },
  "variables": {
    "Owner": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
    "Contributor": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
    "Reader": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
  },
  "resources": [
    {
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2018-09-01-preview",
      "name": "[parameters('roleNameGuid')]",
      "properties": {
        "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
        "principalId": "[parameters('principalId')]"
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param principalId string {
  metadata: {
    'description': 'principalId'
  }
}
param builtInRoleType string {
  'allowed': [
    'Owner'
    'Contributor'
    'Reader'
  ]
  'metadata': {
      'description': 'Built-in role to assign'
  }
}
param roleNameGuid string {
  default: newGuid()
  metadata: {
    'description': 'A new GUID used to identify the role assignment'
  }
}

var roleDefinitionId = {
  Owner: {
    id: subscriptionResourceId('Microsoft.Authorization/roleDefinitions', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')
  }
  Contributor: {
    id: subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'b24988ac-6180-42a0-ab88-20f7382dd24c')
  }
  Reader: {
    id: subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')
  }
}

resource myRoleAssignment 'Microsoft.Authorization/roleAssignments@2018-09-01-preview' = {
  name: roleNameGuid
  properties: {
    roleDefinitionId: roleDefinitionId[builtInRoleType].id
    principalId: principalId
  }
}
```

---

## <a name="tenantresourceid"></a>tenantResourceId

`tenantResourceId(resourceType, resourceName1, [resourceName2], ...)`

Zwraca unikatowy identyfikator zasobu wdrożonego na poziomie dzierżawy.

### <a name="parameters"></a>Parametry

| Parametr | Wymagane | Typ | Opis |
|:--- |:--- |:--- |:--- |
| resourceType |Tak |ciąg |Typ zasobu, w tym przestrzeń nazw dostawcy zasobów. |
| resourceName1 |Tak |ciąg |Nazwa zasobu. |
| resourceName2 |Nie |ciąg |Następny segment nazwy zasobu, w razie konieczności. |

Kontynuuj dodawanie nazw zasobów jako parametrów, gdy typ zasobu zawiera więcej segmentów.

### <a name="return-value"></a>Wartość zwracana

Identyfikator jest zwracany w następującym formacie:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Uwagi

Ta funkcja służy do pobierania identyfikatora zasobu dla zasobu, który jest wdrażany w dzierżawie. Zwrócony identyfikator różni się od wartości zwracanych przez inne funkcje identyfikatora zasobu, nie uwzględniając wartości grup zasobów lub subskrypcji.

### <a name="tenantresourceid-example"></a>przykład tenantResourceId

Wbudowane definicje zasad to zasoby na poziomie dzierżawy. Aby wdrożyć przypisanie zasad odwołujące się do wbudowanej definicji zasad, użyj funkcji tenantResourceId.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "policyDefinitionID": {
      "type": "string",
      "defaultValue": "0a914e76-4921-4c19-b460-a2d36003525a",
      "metadata": {
        "description": "Specifies the ID of the policy definition or policy set definition being assigned."
      }
    },
    "policyAssignmentName": {
      "type": "string",
      "defaultValue": "[guid(parameters('policyDefinitionID'), resourceGroup().name)]",
      "metadata": {
        "description": "Specifies the name of the policy assignment, can be used defined or an idempotent name as the defaultValue provides."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "name": "[parameters('policyAssignmentName')]",
      "apiVersion": "2019-09-01",
      "properties": {
        "scope": "[subscriptionResourceId('Microsoft.Resources/resourceGroups', resourceGroup().name)]",
        "policyDefinitionId": "[tenantResourceId('Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param policyDefinitionID string{
  default: '0a914e76-4921-4c19-b460-a2d36003525a'
  metadata: {
    'description': 'Specifies the ID of the policy definition or policy set definition being assigned.'
  }
}

param policyAssignmentName string {
  default: guid(policyDefinitionID, resourceGroup().name)
  metadata: {
    'description': 'Specifies the name of the policy assignment, can be used defined or an idempotent name as the defaultValue provides.'
  }
}

resource myPolicyAssignment 'Microsoft.Authorization/policyAssignments@2019-09-01' = {
  name: policyAssignmentName
  properties: {
    scope: subscriptionResourceId('Microsoft.Resources/resourceGroups', resourceGroup().name)
    policyDefinitionId: tenantResourceId('Microsoft.Authorization/policyDefinitions', policyDefinitionID)
  }
}
```

---

## <a name="next-steps"></a>Następne kroki

* Opis sekcji w szablonie ARM można znaleźć [w temacie Omówienie struktury i składni szablonów usługi ARM](template-syntax.md).
* Aby scalić wiele szablonów, zobacz [Używanie połączonych i zagnieżdżonych szablonów podczas wdrażania zasobów platformy Azure](linked-templates.md).
* Aby powtórzyć określoną liczbę razy podczas tworzenia typu zasobu, zobacz [iteracja zasobów w szablonach ARM](copy-resources.md).
* Aby dowiedzieć się, jak wdrożyć utworzony szablon, zobacz [wdrażanie zasobów przy użyciu szablonów ARM i Azure PowerShell](deploy-powershell.md).
