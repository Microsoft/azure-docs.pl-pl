---
title: Dostarczanie zdarzeń, tożsamość usługi zarządzanej i link prywatny
description: W tym artykule opisano sposób włączania tożsamości usługi zarządzanej w temacie Azure Event Grid. Służy do przekazywania zdarzeń do obsługiwanych miejsc docelowych.
ms.topic: how-to
ms.date: 10/22/2020
ms.openlocfilehash: edb3e5ac8257a29ecd3835e1dfd4c116c3cc7164
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368617"
---
# <a name="event-delivery-with-a-managed-identity"></a>Dostarczanie zdarzeń przy użyciu tożsamości zarządzanej
W tym artykule opisano sposób włączania [tożsamości usługi zarządzanej](../active-directory/managed-identities-azure-resources/overview.md) dla tematów lub domen w usłudze Azure Event Grid. Służy do przekazywania zdarzeń do obsługiwanych miejsc docelowych, takich jak kolejki Service Bus i tematy, Centra zdarzeń i konta magazynu.

Poniżej przedstawiono kroki, które są szczegółowo omówione w tym artykule:
1. Utwórz temat lub domenę z tożsamością przypisaną do systemu lub zaktualizuj istniejący temat lub domenę, aby włączyć tożsamość. 
1. Dodaj tożsamość do odpowiedniej roli (na przykład Service Bus nadawcy danych) w miejscu docelowym (na przykład Kolejka Service Bus).
1. Podczas tworzenia subskrypcji zdarzeń należy włączyć użycie tożsamości w celu dostarczenia zdarzeń do miejsca docelowego. 

> [!NOTE]
> Obecnie nie jest możliwe dostarczanie zdarzeń przy użyciu [prywatnych punktów końcowych](../private-link/private-endpoint-overview.md). Aby uzyskać więcej informacji, zobacz sekcję [prywatne punkty końcowe](#private-endpoints) na końcu tego artykułu. 

## <a name="create-a-topic-or-domain-with-an-identity"></a>Tworzenie tematu lub domeny z tożsamością
Najpierw przyjrzyjmy się sposobom tworzenia tematu lub domeny z tożsamością zarządzaną przez system.

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal
Tożsamość przypisaną przez system można włączyć dla tematu lub domeny, podczas gdy tworzysz ją w Azure Portal. Na poniższej ilustracji przedstawiono sposób włączania tożsamości zarządzanej przez system dla tematu. W zasadzie należy wybrać opcję **Włącz tożsamość przypisany system** na stronie **Zaawansowane** Kreatora tworzenia tematu. Ta opcja zostanie wyświetlona na stronie **Zaawansowane** Kreatora tworzenia domeny. 

![Włącz tożsamość podczas tworzenia tematu](./media/managed-service-identity/create-topic-identity.png)

### <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure
Możesz również użyć interfejsu wiersza polecenia platformy Azure do utworzenia tematu lub domeny z tożsamością przypisaną do systemu. Użyj `az eventgrid topic create` polecenia z `--identity` parametrem ustawionym na `systemassigned` . Jeśli nie określisz wartości tego parametru, `noidentity` zostanie użyta wartość domyślna. 

```azurecli-interactive
# create a topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

Podobnie można użyć `az eventgrid domain create` polecenia, aby utworzyć domenę z tożsamością zarządzaną przez system.

## <a name="enable-an-identity-for-an-existing-topic-or-domain"></a>Włączanie tożsamości dla istniejącego tematu lub domeny
W poprzedniej sekcji przedstawiono sposób włączania tożsamości zarządzanej przez system podczas tworzenia tematu lub domeny. W tej sekcji dowiesz się, jak włączyć tożsamość zarządzaną przez system dla istniejącego tematu lub domeny. 

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal
Poniższa procedura pokazuje, jak włączyć tożsamość zarządzaną przez system dla tematu. Kroki umożliwiające włączenie tożsamości dla domeny są podobne. 

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Wyszukaj **Tematy usługi Event Grid** na pasku wyszukiwania u góry.
3. Wybierz **temat** , dla którego chcesz włączyć zarządzaną tożsamość. 
4. Przejdź do karty **tożsamość** . 
5. Włącz **przełącznik, aby** włączyć tożsamość. 
1. Wybierz pozycję **Zapisz** na pasku narzędzi, aby zapisać ustawienie. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-topic.png" alt-text="Strona tożsamości tematu"::: 

Możesz użyć podobnych kroków, aby włączyć tożsamość dla domeny usługi Event Grid.

### <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure
Użyj `az eventgrid topic update` polecenia z `--identity` ustawioną opcją `systemassigned` , aby włączyć tożsamość przypisaną przez system dla istniejącego tematu. Jeśli chcesz wyłączyć tożsamość, określ `noidentity` jako wartość. 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

Polecenie aktualizowania istniejącej domeny jest podobne ( `az eventgrid domain update` ).

## <a name="supported-destinations-and-azure-roles"></a>Obsługiwane miejsca docelowe i role platformy Azure
Po włączeniu tożsamości dla tematu lub domeny w usłudze Event Grid platforma Azure automatycznie tworzy tożsamość w Azure Active Directory. Dodaj tę tożsamość do odpowiednich ról platformy Azure, aby temat lub domena mogły przesyłać zdarzenia do obsługiwanych miejsc docelowych. Na przykład Dodaj tożsamość do roli **nadawca danych usługi azure Event Hubs** dla przestrzeni nazw usługi Azure Event Hubs, aby temat usługi Event Grid mógł przesyłać zdarzenia do centrów zdarzeń w tej przestrzeni nazw. 

Obecnie usługa Azure Event Grid obsługuje tematy lub domeny skonfigurowane przy użyciu tożsamości zarządzanej przypisanej przez system do przesyłania zdarzeń do następujących miejsc docelowych. Ta tabela zawiera również role, w których powinna znajdować się tożsamość, aby temat mógł przesłać dalej zdarzenia.

| Miejsce docelowe | Rola na platformie Azure | 
| ----------- | --------- | 
| Service Bus kolejki i tematy | [Nadawca danych Azure Service Bus](../service-bus-messaging/authenticate-application.md#azure-built-in-roles-for-azure-service-bus) |
| Azure Event Hubs | [Nadawca danych Event Hubs platformy Azure](../event-hubs/authorize-access-azure-active-directory.md#azure-built-in-roles-for-azure-event-hubs) | 
| Usługa Azure Blob Storage | [Współautor danych obiektu blob usługi Storage](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) |
| Azure Queue Storage |[Nadawca komunikatu o danych kolejki magazynu](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) | 

## <a name="add-an-identity-to-azure-roles-on-destinations"></a>Dodawanie tożsamości do ról platformy Azure w miejscach docelowych
W tej sekcji opisano, jak dodać tożsamość tematu lub domeny do roli platformy Azure. 

### <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal
Za pomocą Azure Portal można przypisać temat lub tożsamość domeny do odpowiedniej roli, aby temat lub domena mogły przesyłać zdarzenia do miejsca docelowego. 

Poniższy przykład dodaje tożsamość zarządzaną dla tematu usługi Event Grid o nazwie **msitesttopic** do roli **nadawca danych Azure Service Bus** dla przestrzeni nazw Service Bus, która zawiera zasób kolejki lub tematu. Po dodaniu do roli na poziomie przestrzeni nazw temat może przesłać zdarzenia do wszystkich jednostek w przestrzeni nazw. 

1. Przejdź do **przestrzeni nazw Service Bus** w [Azure Portal](https://portal.azure.com). 
1. W lewym okienku wybierz pozycję **Access Control** . 
1. Wybierz pozycję **Dodaj** w sekcji **Dodawanie przypisania roli** . 
1. Na stronie **Dodawanie przypisania roli** wykonaj następujące czynności:
    1. Wybierz rolę. W tym przypadku **Azure Service Bus nadawcy danych**. 
    1. Wybierz **tożsamość** tematu lub domeny. 
    1. Wybierz pozycję **Zapisz** , aby zapisać konfigurację.

Kroki są podobne do dodawania tożsamości do innych ról wymienionych w tabeli. 

### <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure
W przykładzie w tej sekcji pokazano, jak dodać tożsamość do roli platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure. Przykładowe polecenia są przeznaczone dla tematów usługi Event Grid. Polecenia dla domen usługi Event Grid są podobne. 

#### <a name="get-the-principal-id-for-the-topics-system-identity"></a>Pobierz identyfikator podmiotu zabezpieczeń dla tożsamości systemu tematu 
Najpierw Pobierz identyfikator podmiotu zabezpieczeń tożsamości zarządzanej przez system i przypisz tożsamość do odpowiednich ról.

```azurecli-interactive
topic_pid=$(az ad sp list --display-name "$<TOPIC NAME>" --query [].objectId -o tsv)
```

#### <a name="create-a-role-assignment-for-event-hubs-at-various-scopes"></a>Tworzenie przypisania roli dla centrów zdarzeń w różnych zakresach 
Poniższy przykład interfejsu wiersza polecenia pokazuje, jak dodać tożsamość tematu do roli **nadawca danych usługi Azure Event Hubs** na poziomie przestrzeni nazw lub na poziomie centrum zdarzeń. Jeśli utworzysz przypisanie roli na poziomie przestrzeni nazw, temat może przesłać zdarzenia do wszystkich centrów zdarzeń w tej przestrzeni nazw. Jeśli utworzysz przypisanie roli na poziomie centrum zdarzeń, w temacie można przesłać zdarzenia tylko do tego konkretnego centrum zdarzeń. 


```azurecli-interactive
role="Azure Event Hubs Data Sender" 
namespaceresourceid=$(az eventhubs namespace show -n $<EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 
eventhubresourceid=$(az eventhubs eventhub show -n <EVENT HUB NAME> --namespace-name <EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one event hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$eventhubresourceid" 
```

#### <a name="create-a-role-assignment-for-a-service-bus-topic-at-various-scopes"></a>Tworzenie przypisania roli dla Service Bus tematu w różnych zakresach 
Poniższy przykład interfejsu wiersza polecenia pokazuje, jak dodać tożsamość tematu do roli **nadawca danych Azure Service Bus** na poziomie przestrzeni nazw lub na poziomie tematu Service Bus. Jeśli utworzysz przypisanie roli na poziomie przestrzeni nazw, temat usługi Event Grid może przekazywać zdarzenia do wszystkich jednostek (Service Bus kolejki lub tematy) w tej przestrzeni nazw. Jeśli utworzysz przypisanie roli na poziomie kolejki lub tematu Service Bus, temat usługi Event Grid może przekazywać zdarzenia tylko do tej konkretnej kolejki Service Bus lub tematu. 

```azurecli-interactive
role="Azure Service Bus Data Sender" 
namespaceresourceid=$(az servicebus namespace show -n $RG\SB -g "$RG" --query "{I:id}" -o tsv 
sbustopicresourceid=$(az servicebus topic show -n topic1 --namespace-name $RG\SB -g "$RG" --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$sbustopicresourceid" 
```

## <a name="create-event-subscriptions-that-use-an-identity"></a>Tworzenie subskrypcji zdarzeń, które używają tożsamości
Po utworzeniu tematu lub domeny z tożsamością zarządzaną przez system i dodaniu tożsamości do odpowiedniej roli w miejscu docelowym można utworzyć subskrypcje, które używają tożsamości. 

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
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the service bus queue resource id
queueid=$(az servicebus queue show --namespace-name <SERVICE BUS NAMESPACE NAME> --name <QUEUE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
sb_esname = "<Specify a name for the event subscription>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Tworzenie subskrypcji zdarzeń przy użyciu tożsamości zarządzanej do dostarczania 
To przykładowe polecenie tworzy subskrypcję zdarzeń dla tematu usługi Event Grid z typem punktu końcowego ustawionym na **Service Bus Queue**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sb_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery-and-dead-lettering"></a>Tworzenie subskrypcji zdarzeń przy użyciu tożsamości zarządzanej na potrzeby dostarczania i utraconych wiadomości
To przykładowe polecenie tworzy subskrypcję zdarzeń dla tematu usługi Event Grid z typem punktu końcowego ustawionym na **Service Bus Queue**. Określa również, że tożsamość zarządzana przez system ma być używana w przypadku utraconych wiadomości. 

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
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

hubid=$(az eventhubs eventhub show --name <EVENT HUB NAME> --namespace-name <NAMESPACE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
eh_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Tworzenie subskrypcji zdarzeń przy użyciu tożsamości zarządzanej do dostarczania 
To przykładowe polecenie tworzy subskrypcję zdarzeń dla tematu usługi Event Grid z typem punktu końcowego ustawionym na **Event Hubs**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type eventhub 
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    -n $sbq_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>Tworzenie subskrypcji zdarzeń przy użyciu tożsamości zarządzanej na potrzeby dostarczania i przesyłania wiadomości 
To przykładowe polecenie tworzy subskrypcję zdarzeń dla tematu usługi Event Grid z typem punktu końcowego ustawionym na **Event Hubs**. Określa również, że tożsamość zarządzana przez system ma być używana w przypadku utraconych wiadomości. 

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
W tej sekcji dowiesz się, jak używać interfejsu wiersza polecenia platformy Azure, aby umożliwić korzystanie z tożsamości przypisanej do systemu w celu dostarczania zdarzeń do kolejki usługi Azure Storage. Tożsamość musi być członkiem roli **współautor danych obiektów blob magazynu** na koncie magazynu.

#### <a name="define-variables"></a>Definiowanie zmiennych  

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

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

Jeśli jednak wymagania wymagają bezpiecznego sposobu wysyłania zdarzeń za pomocą zaszyfrowanego kanału i znanej tożsamości nadawcy (w tym przypadku Event Grid) przy użyciu publicznej przestrzeni adresów IP, można dostarczyć zdarzenia do Event Hubs, Service Bus lub usługi Azure Storage za pomocą tematu Azure Event Grid lub domeny z tożsamością zarządzaną przez system, jak pokazano w tym artykule. Następnie możesz użyć prywatnego linku skonfigurowanego w Azure Functions lub elementu webhook wdrożonego w sieci wirtualnej do ściągania zdarzeń. Zobacz przykład: [łączenie z prywatnymi punktami końcowymi przy użyciu Azure Functions](/samples/azure-samples/azure-functions-private-endpoints/connect-to-private-endpoints-with-azure-functions/).

Należy pamiętać, że w ramach tej konfiguracji ruch przechodzi przez publiczny adres IP/Internet z Event Grid do Event Hubs, Service Bus lub Azure Storage, ale kanał może być zaszyfrowany i używana jest tożsamość zarządzana Event Grid. Jeśli skonfigurujesz Azure Functions lub element webhook wdrożony w sieci wirtualnej tak, aby używał Event Hubs, Service Bus lub usługi Azure Storage za pośrednictwem prywatnego linku, ta sekcja ruchu będzie oczywista na platformie Azure.


## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat tożsamości usługi zarządzanej, zobacz [co to są tożsamości zarządzane dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md). 
