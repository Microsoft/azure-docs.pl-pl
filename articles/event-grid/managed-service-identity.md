---
title: Dostarczanie zdarzeń, tożsamość usługi zarządzanej i link prywatny
description: W tym artykule opisano sposób włączania tożsamości usługi zarządzanej w temacie Azure Event Grid. Służy do przekazywania zdarzeń do obsługiwanych miejsc docelowych.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 76f10b4627dc9578b1e616a868eab03431b59b69
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625282"
---
# <a name="event-delivery-with-a-managed-identity"></a>Dostarczanie zdarzeń przy użyciu tożsamości zarządzanej
W tym artykule opisano sposób użycia [tożsamości usługi zarządzanej](../active-directory/managed-identities-azure-resources/overview.md) dla tematu systemu usługi Azure Event Grid, tematu niestandardowego lub domeny. Służy do przekazywania zdarzeń do obsługiwanych miejsc docelowych, takich jak kolejki Service Bus i tematy, Centra zdarzeń i konta magazynu.



## <a name="prerequisites"></a>Wymagania wstępne
1. Przypisywanie tożsamości przypisanej do systemu do tematu systemu, tematu niestandardowego lub domeny. 
    - W przypadku niestandardowych tematów i domen zobacz [Włączanie zarządzanej tożsamości dla niestandardowych tematów i domen](enable-identity-custom-topics-domains.md). 
    - Tematy dotyczące systemu można znaleźć w temacie [Włączanie zarządzanej tożsamości dla systemu tematów](enable-identity-system-topics.md)
1. Dodaj tożsamość do odpowiedniej roli (na przykład Service Bus nadawcy danych) w miejscu docelowym (na przykład Kolejka Service Bus). Aby uzyskać szczegółowe instrukcje, zobacz [Dodawanie tożsamości do ról platformy Azure w miejscach docelowych](add-identity-roles.md)

    > [!NOTE]
    > Obecnie nie jest możliwe dostarczanie zdarzeń przy użyciu [prywatnych punktów końcowych](../private-link/private-endpoint-overview.md). Aby uzyskać więcej informacji, zobacz sekcję [prywatne punkty końcowe](#private-endpoints) na końcu tego artykułu. 

## <a name="create-event-subscriptions-that-use-an-identity"></a>Tworzenie subskrypcji zdarzeń, które używają tożsamości
Po utworzeniu tematu niestandardowego lub tematu systemu lub domeny z tożsamością zarządzaną przez system oraz dodaniu tożsamości do odpowiedniej roli w miejscu docelowym można utworzyć subskrypcje, które używają tożsamości. 

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal
Podczas tworzenia subskrypcji zdarzeń zostanie wyświetlona opcja umożliwiająca użycie tożsamości przypisanej do systemu dla punktu końcowego w sekcji **Szczegóły punktu końcowego** . 

![Włącz tożsamość podczas tworzenia subskrypcji zdarzeń dla kolejki Service Bus](./media/managed-service-identity/service-bus-queue-subscription-identity.png)

Można również włączyć za pomocą tożsamości przypisanej do systemu, aby można było używać ich na potrzeby utraconych wiadomości na karcie **funkcje dodatkowe** . 

![Włącz tożsamość przypisaną przez system do obsługi utraconych wiadomości](./media/managed-service-identity/enable-deadletter-identity.png)

### <a name="use-the-azure-cli---service-bus-queue"></a>Korzystanie z kolejki Service Bus interfejsu wiersza polecenia platformy Azure 
W tej sekcji dowiesz się, jak używać interfejsu wiersza polecenia platformy Azure, aby umożliwić korzystanie z tożsamości przypisanej do systemu w celu dostarczania zdarzeń do kolejki Service Bus. Tożsamość musi być członkiem roli **nadawca danych Azure Service Bus** . Musi być również członkiem roli **współautor danych obiektów blob magazynu** na koncie magazynu, które jest używane do utraconych wiadomości. 

#### <a name="define-variables"></a>Definiowanie zmiennych
Najpierw określ wartości następujących zmiennych, które mają być używane w interfejsie wiersza polecenia. 

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the service bus queue resource id
queueid=$(az servicebus queue show --namespace-name <SERVICE BUS NAMESPACE NAME> --name <QUEUE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
sb_esname = "<Specify a name for the event subscription>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Tworzenie subskrypcji zdarzeń przy użyciu tożsamości zarządzanej do dostarczania 
To przykładowe polecenie tworzy subskrypcję zdarzeń dla tematu niestandardowego usługi Event Grid z typem punktu końcowego ustawionym na **Service Bus Queue**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sb_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery-and-dead-lettering"></a>Tworzenie subskrypcji zdarzeń przy użyciu tożsamości zarządzanej na potrzeby dostarczania i utraconych wiadomości
To przykładowe polecenie tworzy subskrypcję zdarzeń dla tematu niestandardowego usługi Event Grid z typem punktu końcowego ustawionym na **Service Bus Queue**. Określa również, że tożsamość zarządzana przez system ma być używana w przypadku utraconych wiadomości. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sb_esnameq 
```

### <a name="use-the-azure-cli---event-hubs"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure — Event Hubs 
W tej sekcji dowiesz się, jak używać interfejsu wiersza polecenia platformy Azure, aby umożliwić korzystanie z tożsamości przypisanej do systemu w celu dostarczania zdarzeń do centrum zdarzeń. Tożsamość musi być członkiem roli **nadawca danych usługi Azure Event Hubs** . Musi być również członkiem roli **współautor danych obiektów blob magazynu** na koncie magazynu, które jest używane do utraconych wiadomości. 

#### <a name="define-variables"></a>Definiowanie zmiennych
```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID CUSTOM TOPIC NAME>"

hubid=$(az eventhubs eventhub show --name <EVENT HUB NAME> --namespace-name <NAMESPACE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
eh_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Tworzenie subskrypcji zdarzeń przy użyciu tożsamości zarządzanej do dostarczania 
To przykładowe polecenie tworzy subskrypcję zdarzeń dla tematu niestandardowego usługi Event Grid z typem punktu końcowego ustawionym na **Event Hubs**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type eventhub 
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    -n $sbq_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>Tworzenie subskrypcji zdarzeń przy użyciu tożsamości zarządzanej na potrzeby dostarczania i przesyłania wiadomości 
To przykładowe polecenie tworzy subskrypcję zdarzeń dla tematu niestandardowego usługi Event Grid z typem punktu końcowego ustawionym na **Event Hubs**. Określa również, że tożsamość zarządzana przez system ma być używana w przypadku utraconych wiadomości. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    --deadletter-identity-endpoint $eh_deadletterendpoint
    --deadletter-identity systemassigned 
    -n $eh_esname 
```

### <a name="use-the-azure-cli---azure-storage-queue"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure — Kolejka usługi Azure Storage 
W tej sekcji dowiesz się, jak używać interfejsu wiersza polecenia platformy Azure, aby umożliwić korzystanie z tożsamości przypisanej do systemu w celu dostarczania zdarzeń do kolejki usługi Azure Storage. Tożsamość musi być członkiem roli **nadawca komunikatu danych kolejki magazynu** na koncie magazynu. Musi być również członkiem roli **współautor danych obiektów blob magazynu** na koncie magazynu, które jest używane do utraconych wiadomości.

#### <a name="define-variables"></a>Definiowanie zmiennych  

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID CUSTOM TOPIC NAME>"

# get the storage account resource id
storageid=$(az storage account show --name <STORAGE ACCOUNT NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)

# build the resource id for the queue
queueid="$storageid/queueservices/default/queues/<QUEUE NAME>" 

sa_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Tworzenie subskrypcji zdarzeń przy użyciu tożsamości zarządzanej do dostarczania 

```azurecli-interactive
az eventgrid event-subscription create 
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sa_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>Tworzenie subskrypcji zdarzeń przy użyciu tożsamości zarządzanej na potrzeby dostarczania i przesyłania wiadomości 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sa_esname 
```

## <a name="private-endpoints"></a>Prywatne punkty końcowe
Obecnie nie jest możliwe dostarczanie zdarzeń przy użyciu [prywatnych punktów końcowych](../private-link/private-endpoint-overview.md). Oznacza to, że nie ma żadnej obsługi, jeśli masz ścisłe wymagania izolacji sieciowej, gdy ruch zdarzeń nie może opuścić prywatnego obszaru adresów IP. 

Jeśli jednak wymagania wymagają bezpiecznego sposobu wysyłania zdarzeń za pomocą zaszyfrowanego kanału i znanej tożsamości nadawcy (w tym przypadku Event Grid) przy użyciu publicznej przestrzeni adresów IP, można dostarczyć zdarzenia do Event Hubs, Service Bus lub usługi Azure Storage za pomocą niestandardowego tematu usługi Azure Event Grid lub domeny z tożsamością zarządzaną przez system, tak jak pokazano w tym artykule. Następnie możesz użyć prywatnego linku skonfigurowanego w Azure Functions lub elementu webhook wdrożonego w sieci wirtualnej do ściągania zdarzeń. Zobacz przykład: [łączenie z prywatnymi punktami końcowymi przy użyciu Azure Functions](/samples/azure-samples/azure-functions-private-endpoints/connect-to-private-endpoints-with-azure-functions/).

W ramach tej konfiguracji ruch przechodzi przez publiczny adres IP/Internet z Event Grid do Event Hubs, Service Bus lub Azure Storage, ale kanał może być zaszyfrowany i używana jest tożsamość zarządzana Event Grid. Jeśli skonfigurujesz Azure Functions lub element webhook wdrożony w sieci wirtualnej tak, aby używał Event Hubs, Service Bus lub usługi Azure Storage za pośrednictwem prywatnego linku, ta sekcja ruchu będzie oczywista na platformie Azure.


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o tożsamościach zarządzanych, zobacz [co to są tożsamości zarządzane dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md).
