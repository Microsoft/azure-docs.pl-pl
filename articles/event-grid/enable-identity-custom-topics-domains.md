---
title: Włącz zarządzaną tożsamość na Azure Event Grid niestandardowych tematach i domenach
description: W tym artykule opisano sposób włączania tożsamości usługi zarządzanej dla Azure Event Grid niestandardowego tematu lub domeny.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: b93fd44282d9e19e7111dd52c73d8d4c01c67a10
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278222"
---
# <a name="assign-a-system-managed-identity-to-an-event-grid-custom-topic-or-domain"></a>Przypisywanie tożsamości zarządzanej przez system do Event Grid niestandardowego tematu lub domeny 
W tym artykule opisano sposób włączania tożsamości zarządzanej przez system dla Event Grid niestandardowego tematu lub domeny. Aby dowiedzieć się więcej o tożsamościach zarządzanych, zobacz [co to są tożsamości zarządzane dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="enable-identity-at-the-time-of-creation"></a>Włącz tożsamość w momencie tworzenia

### <a name="using-azure-portal"></a>Korzystanie z witryny Azure Portal
Tożsamość przypisaną przez system można włączyć dla niestandardowego tematu lub domeny podczas jej tworzenia w Azure Portal. Na poniższej ilustracji przedstawiono sposób włączania tożsamości zarządzanej przez system dla tematu niestandardowego. W zasadzie należy wybrać opcję **Włącz tożsamość przypisany system** na stronie **Zaawansowane** Kreatora tworzenia tematu. Ta opcja zostanie wyświetlona na stronie **Zaawansowane** Kreatora tworzenia domeny. 

![Włącz tożsamość podczas tworzenia tematu niestandardowego](./media/managed-service-identity/create-topic-identity.png)

### <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure
Możesz również użyć interfejsu wiersza polecenia platformy Azure do utworzenia niestandardowego tematu lub domeny z tożsamością przypisaną przez system. Użyj `az eventgrid topic create` polecenia z `--identity` parametrem ustawionym na `systemassigned` . Jeśli nie określisz wartości tego parametru, `noidentity` zostanie użyta wartość domyślna. 

```azurecli-interactive
# create a custom topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

Podobnie można użyć `az eventgrid domain create` polecenia, aby utworzyć domenę z tożsamością zarządzaną przez system.

## <a name="enable-identity-for-an-existing-custom-topic-or-domain"></a>Włącz tożsamość dla istniejącego niestandardowego tematu lub domeny
W tej sekcji dowiesz się, jak włączyć tożsamość zarządzaną przez system dla istniejącego tematu lub domeny niestandardowej. 

### <a name="using-azure-portal"></a>Korzystanie z witryny Azure Portal
Poniższa procedura pokazuje, jak włączyć tożsamość zarządzaną przez system dla tematu niestandardowego. Kroki umożliwiające włączenie tożsamości dla domeny są podobne. 

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Wyszukaj **Tematy usługi Event Grid** na pasku wyszukiwania u góry.
3. Wybierz **temat niestandardowy** , dla którego chcesz włączyć zarządzaną tożsamość. 
4. Przejdź do karty **tożsamość** . 
5. Włącz **przełącznik, aby** włączyć tożsamość. 
1. Wybierz pozycję **Zapisz** na pasku narzędzi, aby zapisać ustawienie. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-topic.png" alt-text="Strona tożsamości tematu niestandardowego"::: 

Możesz użyć podobnych kroków, aby włączyć tożsamość dla domeny usługi Event Grid.

### <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure
Użyj `az eventgrid topic update` polecenia z `--identity` ustawioną opcją `systemassigned` , aby włączyć tożsamość przypisaną przez system dla istniejącego tematu niestandardowego. Jeśli chcesz wyłączyć tożsamość, określ `noidentity` jako wartość. 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

Polecenie aktualizowania istniejącej domeny jest podobne ( `az eventgrid domain update` ).


## <a name="next-steps"></a>Następne kroki
Dodaj tożsamość do odpowiedniej roli (na przykład Service Bus nadawcy danych) w miejscu docelowym (na przykład Kolejka Service Bus). Aby uzyskać szczegółowe instrukcje, zobacz [udzielanie tożsamości zarządzanej dostępu do Event Grid docelowej](add-identity-roles.md). 
