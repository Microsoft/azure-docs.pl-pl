---
title: Dodawanie tożsamości zarządzanej do roli w Azure Event Grid miejscu docelowym
description: W tym artykule opisano sposób dodawania tożsamości zarządzanej do ról platformy Azure w miejscach docelowych, takich jak Azure Service Bus i Azure Event Hubs.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 1bcef878c982122d80980dd7194fae2de6fc8762
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630455"
---
# <a name="add-an-identity-to-azure-roles-on-azure-event-grid-destinations"></a>Dodawanie tożsamości do ról platformy Azure na Azure Event Grid miejscach docelowych
W tej sekcji opisano, jak dodać tożsamość dla tematu systemu, tematu niestandardowego lub domeny do roli platformy Azure. 

## <a name="prerequisites"></a>Wymagania wstępne
Przypisanie zarządzanej tożsamości przypisanej do systemu przy użyciu instrukcji z następujących artykułów:

- [Niestandardowe tematy lub domeny](enable-identity-custom-topics-domains.md)
- [Tematy systemowe](enable-identity-system-topics.md)

## <a name="supported-destinations-and-azure-roles"></a>Obsługiwane miejsca docelowe i role platformy Azure
Po włączeniu tożsamości dla niestandardowego tematu lub domeny usługi Event Grid platforma Azure automatycznie tworzy tożsamość w Azure Active Directory. Dodaj tę tożsamość do odpowiednich ról platformy Azure, aby niestandardowy temat lub domena mogły przesyłać zdarzenia do obsługiwanych miejsc docelowych. Na przykład Dodaj tożsamość do roli **nadawca danych usługi azure Event Hubs** dla przestrzeni nazw Event Hubs platformy Azure, tak aby niestandardowy temat usługi Event Grid mógł przesyłać zdarzenia do centrów zdarzeń w tej przestrzeni nazw. 

Obecnie usługa Azure Event Grid obsługuje niestandardowe tematy lub domeny skonfigurowane przy użyciu tożsamości zarządzanej przypisanej przez system do przesyłania zdarzeń do następujących miejsc docelowych. Ta tabela zawiera również role, w których powinna być tożsamość, aby temat niestandardowy mógł przesłać dalej zdarzenia.

| Element docelowy | Rola na platformie Azure | 
| ----------- | --------- | 
| Service Bus kolejki i tematy | [Nadawca danych Azure Service Bus](../service-bus-messaging/authenticate-application.md#azure-built-in-roles-for-azure-service-bus) |
| Azure Event Hubs | [Nadawca danych Event Hubs platformy Azure](../event-hubs/authorize-access-azure-active-directory.md#azure-built-in-roles-for-azure-event-hubs) | 
| Azure Blob Storage | [Współautor danych obiektu blob usługi Storage](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) |
| Azure Queue Storage |[Nadawca komunikatu o danych kolejki magazynu](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) | 


## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal
Azure Portal można użyć do przypisania niestandardowego tematu lub tożsamości domeny do odpowiedniej roli, dzięki czemu niestandardowy temat lub domena mogą przesyłać zdarzenia do miejsca docelowego. 

Poniższy przykład dodaje tożsamość zarządzaną dla tematu niestandardowego usługi Event Grid o nazwie **msitesttopic** do roli **nadawca danych Azure Service Bus** dla Service Bus przestrzeni nazw, która zawiera zasób kolejki lub tematu. Po dodaniu do roli na poziomie przestrzeni nazw, niestandardowy temat usługi Event Grid może przesłać zdarzenia do wszystkich jednostek w przestrzeni nazw. 

1. Przejdź do **przestrzeni nazw Service Bus** w [Azure Portal](https://portal.azure.com). 
1. W lewym okienku wybierz pozycję **Access Control** . 
1. Wybierz pozycję **Dodaj** w sekcji **Dodawanie przypisania roli** . 
1. Na stronie **Dodawanie przypisania roli** wykonaj następujące czynności:
    1. Wybierz rolę. W tym przypadku **Azure Service Bus nadawcy danych**. 
    1. Wybierz **tożsamość** niestandardowego tematu lub domeny usługi Event Grid. 
    1. Wybierz pozycję **Zapisz** , aby zapisać konfigurację.

Kroki są podobne do dodawania tożsamości do innych ról wymienionych w tabeli. 

## <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure
W przykładzie w tej sekcji pokazano, jak dodać tożsamość do roli platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure. Przykładowe polecenia są przeznaczone dla tematów niestandardowych usługi Event Grid. Polecenia dla domen usługi Event Grid są podobne. 

### <a name="get-the-principal-id-for-the-custom-topics-system-identity"></a>Uzyskaj identyfikator podmiotu zabezpieczeń dla tożsamości systemowej tematu niestandardowego 
Najpierw Pobierz identyfikator podmiotu zabezpieczeń tożsamości zarządzanej przez system i przypisz tożsamość do odpowiednich ról.

```azurecli-interactive
topic_pid=$(az ad sp list --display-name "$<TOPIC NAME>" --query [].objectId -o tsv)
```

### <a name="create-a-role-assignment-for-event-hubs-at-various-scopes"></a>Tworzenie przypisania roli dla centrów zdarzeń w różnych zakresach 
Poniższy przykład interfejsu wiersza polecenia pokazuje, jak dodać tożsamość tematu niestandardowego do roli **nadawca danych usługi Azure Event Hubs** na poziomie przestrzeni nazw lub na poziomie centrum zdarzeń. Jeśli utworzysz przypisanie roli na poziomie przestrzeni nazw, temat niestandardowy może przesłać zdarzenia do wszystkich centrów zdarzeń w tej przestrzeni nazw. Jeśli utworzysz przypisanie roli na poziomie centrum zdarzeń, temat niestandardowy może przesłać zdarzenia tylko do tego konkretnego centrum zdarzeń. 


```azurecli-interactive
role="Azure Event Hubs Data Sender" 
namespaceresourceid=$(az eventhubs namespace show -n $<EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 
eventhubresourceid=$(az eventhubs eventhub show -n <EVENT HUB NAME> --namespace-name <EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one event hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$eventhubresourceid" 
```

### <a name="create-a-role-assignment-for-a-service-bus-topic-at-various-scopes"></a>Tworzenie przypisania roli dla Service Bus tematu w różnych zakresach 
Poniższy przykład interfejsu wiersza polecenia pokazuje, jak dodać tożsamość tematu niestandardowego w usłudze Event Grid do roli **nadawca danych Azure Service Bus** na poziomie przestrzeni nazw lub na poziomie tematu Service Bus. Jeśli utworzysz przypisanie roli na poziomie przestrzeni nazw, temat usługi Event Grid może przekazywać zdarzenia do wszystkich jednostek (Service Bus kolejki lub tematy) w tej przestrzeni nazw. Jeśli utworzysz przypisanie roli na poziomie kolejki lub tematu Service Bus, niestandardowy temat usługi Event Grid może przesłać zdarzenia tylko do tej konkretnej kolejki Service Bus lub tematu. 

```azurecli-interactive
role="Azure Service Bus Data Sender" 
namespaceresourceid=$(az servicebus namespace show -n $RG\SB -g "$RG" --query "{I:id}" -o tsv 
sbustopicresourceid=$(az servicebus topic show -n topic1 --namespace-name $RG\SB -g "$RG" --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$sbustopicresourceid" 
```

## <a name="next-steps"></a>Następne kroki
Teraz, gdy przypisano tożsamość przypisaną do systemu do tematu systemu, tematu niestandardowego lub domeny, i dodano tożsamość do odpowiednich ról w miejscach docelowych, zobacz [zdarzenia Devlier przy użyciu tożsamości](managed-service-identity.md) w przypadku dostarczania zdarzeń do miejsc docelowych przy użyciu tożsamości.


