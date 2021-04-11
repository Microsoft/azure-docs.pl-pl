---
title: 'Samouczek: zdarzenia zmiany stanu zasad trasy do Event Grid za pomocą interfejsu wiersza polecenia platformy Azure'
description: W tym samouczku skonfigurujesz Event Grid do nasłuchiwania zdarzeń zmiany stanu zasad i wywoływania elementu webhook.
ms.date: 03/29/2021
ms.topic: tutorial
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1fe87e4fd3349df7d8f5d57b2b2d95f95ed3fba8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105735547"
---
# <a name="tutorial-route-policy-state-change-events-to-event-grid-with-azure-cli"></a>Samouczek: zdarzenia zmiany stanu zasad trasy do Event Grid za pomocą interfejsu wiersza polecenia platformy Azure

W tym artykule dowiesz się, jak skonfigurować Azure Policy subskrypcje zdarzeń do wysyłania zdarzeń zmiany stanu zasad do punktu końcowego w sieci Web. Azure Policy użytkownicy mogą subskrybować zdarzenia emitowane po wystąpieniu zmian stanu zasad w zasobach. Zdarzenia te mogą wyzwalać elementy webhook, [Azure Functions](../../../azure-functions/index.yml), [kolejki usługi Azure Storage](../../../storage/queues/index.yml)lub inne procedury obsługi zdarzeń obsługiwane przez [Azure Event Grid](../../../event-grid/index.yml). Zazwyczaj użytkownik wysyła zdarzenia do punktu końcowego, w którym następuje przetwarzanie danych zdarzenia i są wykonywane akcje. Aby uprościć ten samouczek, należy jednak wysłać zdarzenia do aplikacji sieci Web, która gromadzi i wyświetla komunikaty.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

- Ten przewodnik Szybki Start wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2.0.76 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

- Nawet jeśli wcześniej była używana Azure Policy lub Event Grid, należy ponownie zarejestrować odpowiednich dostawców zasobów:

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Provider register: Register the Azure Event Grid provider
  az provider register --namespace Microsoft.EventGrid
  ```

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Tematy usługi Event Grid to zasoby platformy Azure i muszą być umieszczone w grupie zasobów platformy Azure. Grupa zasobów to kolekcja logiczna przeznaczona do wdrażania zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). 

Poniższy przykład tworzy grupę zasobów o nazwie `<resource_group_name>` w lokalizacji _zachodniej_ . Zamień `<resource_group_name>` na unikatową nazwę grupy zasobów.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az group create --name <resource_group_name> --location westus
```

## <a name="create-an-event-grid-system-topic"></a>Tworzenie tematu systemu Event Grid

Teraz, gdy mamy już grupę zasobów, utworzymy [temat systemowy](../../../event-grid/system-topics.md). Temat systemowy w Event Grid reprezentuje jedno lub więcej zdarzeń opublikowanych przez usługi platformy Azure, takie jak Azure Policy i Azure Event Hubs. Ten temat systemowy używa `Microsoft.PolicyInsights.PolicyStates` typu tematu w celu Azure Policy zmian stanu. Zastąp wartość `<SubscriptionID>` w parametrze **SCOPE** identyfikatorem subskrypcji i `<resource_group_name>` w parametrze **grupy zasobów** z wcześniej utworzoną grupą zasobów.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az eventgrid system-topic create --name PolicyStateChanges --location global --topic-type Microsoft.PolicyInsights.PolicyStates --source "/subscriptions/<SubscriptionID>" --resource-group "<resource_group_name>"
```

## <a name="create-a-message-endpoint"></a>Tworzenie punktu końcowego komunikatów

Przed zasubskrybowaniem tematu utwórzmy punkt końcowy dla komunikatów o zdarzeniach. Zazwyczaj w punkcie końcowym akcje są wykonywane na podstawie danych zdarzenia. Aby uprościć ten przewodnik Szybki Start, wdrożysz [wstępnie zbudowaną aplikację sieci Web](https://github.com/Azure-Samples/azure-event-grid-viewer), która będzie wyświetlać komunikaty o zdarzeniach. Wdrożone rozwiązanie zawiera plan usługi App Service, aplikację internetową usługi App Service i kod źródłowy z repozytorium GitHub.

Zastąp `<your-site-name>` unikatową nazwą aplikacji internetowej. Nazwa aplikacji internetowej musi być unikatowa, ponieważ stanowi część wpisu DNS.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az deployment group create \
  --resource-group <resource_group_name> \
  --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
  --parameters siteName=<your-site-name> hostingPlanName=viewerhost
```

Wdrożenie może potrwać kilka minut. Po pomyślnym wdrożeniu należy wyświetlić aplikację sieci Web i upewnić się, że jest uruchomiona. W przeglądarce sieci Web przejdź do: `https://<your-site-name>.azurewebsites.net`

Powinna być widoczna witryna internetowa bez żadnych aktualnie wyświetlanych komunikatów.

## <a name="subscribe-to-the-system-topic"></a>Subskrybowanie tematu systemu

Subskrybowanie tematu to dla usługi Event Grid informacja o tym, które zdarzenia chcesz śledzić i gdzie mają być one wysyłane. Poniższy przykład subskrybuje utworzony temat systemowy i przekazuje adres URL z aplikacji sieci Web jako punkt końcowy do odbierania powiadomień o zdarzeniach. Zastąp element `<event_subscription_name>` nazwą Twojej subskrypcji zdarzeń. Jako parametrów `<resource_group_name>` i `<your-site-name>` użyj utworzonych wcześniej wartości.

Punkt końcowy dla aplikacji internetowej musi zawierać sufiks `/api/updates/`.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Create the subscription
az eventgrid system-topic event-subscription create \
  --name <event_subscription_name> \
  --resource-group <resource_group_name> \
  --system-topic-name PolicyStateChanges \
  --endpoint https://<your-site-name>.azurewebsites.net/api/updates
```

Wyświetl aplikację sieci Web ponownie i zwróć uwagę, że zdarzenie sprawdzania poprawności subskrypcji zostało do niej wysłane. Wybierz ikonę oka, aby rozwinąć dane zdarzenia. Usługa Event Grid wysyła zdarzenie weryfikacji, aby w punkcie końcowym mogło nastąpić sprawdzenie, czy dane zdarzenia mają być odbierane. Aplikacja internetowa zawiera kod do sprawdzania poprawności subskrypcji.

:::image type="content" source="../media/route-state-change-events/view-subscription-event.png" alt-text="Zrzut ekranu przedstawiający zdarzenie weryfikacji subskrypcji Event Grid w wstępnie skompilowanej aplikacji sieci Web.":::

## <a name="create-a-policy-assignment"></a>Tworzenie przypisania zasad

W tym przewodniku szybki start utworzysz przypisanie zasad i przypiszesz opcję **Wymagaj tagu dla grup zasobów** . Ta definicja zasad identyfikuje grupy zasobów, w których brakuje tagu skonfigurowanego podczas przypisywania zasad.

Uruchom następujące polecenie, aby utworzyć przypisanie zasad do grupy zasobów utworzonej w celu przechowywania tematu w usłudze Event Grid:

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az policy assignment create --name 'requiredtags-events' --display-name 'Require tag on RG' --scope '<ResourceGroupScope>' --policy '<policy definition ID>' --params '{ "tagName": { "value": "EventTest" } }'
```

Poprzednie polecenie korzysta z następujących informacji:

- **Nazwa** —rzeczywista nazwa przypisania. W tym przykładzie użyto _requiredtags-Events_ .
- **DisplayName** — nazwa wyświetlana przypisania zasad. W tym przypadku jest używany _tag Wymagaj w RG_.
- **Zakres** — zakres określa, jakie zasoby lub grupy zasobów są wymuszane w ramach przypisania zasad. Może obejmować zarówno subskrypcje, jak i grupy zasobów. Pamiętaj, aby zastąpić fragment &lt;zakres&gt; nazwą grupy zasobów. Jest to format zakresu grupy zasobów `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>` .
- **Zasady** — identyfikator definicji zasad, na podstawie której tworzysz przypisanie. W tym przypadku jest to identyfikator definicji zasad _wymaga tagu w grupach zasobów_. Aby uzyskać identyfikator definicji zasad, uruchom następujące polecenie: `az policy definition list --query "[?displayName=='Require a tag on resource groups']"`

Po utworzeniu przypisania zasad poczekaj na wyświetlenie powiadomienia o zdarzeniu **Microsoft. PolicyInsights. PolicyStateCreated** w aplikacji sieci Web. Utworzona grupa zasobów pokazuje `data.complianceState` wartość _niezgodną_ do uruchomienia.

:::image type="content" source="../media/route-state-change-events/view-policystatecreated-event.png" alt-text="Zrzut ekranu przedstawiający zdarzenie tworzenia stanu zasad subskrypcji Event Grid dla grupy zasobów w wstępnie skompilowanej aplikacji sieci Web.":::

> [!NOTE]
> Jeśli grupa zasobów dziedziczy inne przypisania zasad z hierarchii subskrypcji lub grupy zarządzania, wyświetlane są również zdarzenia dla każdego z nich. Potwierdź, że zdarzenie dotyczy przypisania w tym samouczku, oceniając `data.policyDefinitionId` Właściwość.

## <a name="trigger-a-change-on-the-resource-group"></a>Wyzwól zmianę grupy zasobów

Aby zapewnić zgodność grupy zasobów, wymagany jest tag o nazwie **EventTest** . Dodaj tag do grupy zasobów przy użyciu następującego polecenia zastępującego `<SubscriptionID>` Identyfikator subskrypcji i `<ResourceGroup>` nazwą grupy zasobów:

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az tag create --resource-id '/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>' --tags EventTest=true
```

Po dodaniu wymaganego tagu do grupy zasobów poczekaj na wyświetlenie powiadomienia o zdarzeniu **Microsoft. PolicyInsights. PolicyStateChanged** w aplikacji sieci Web. Rozwiń zdarzenie, a `data.complianceState` wartość zostanie wyświetlona jako _zgodne_.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz kontynuować pracę z tą aplikacją sieci Web i Azure Policy subskrypcją zdarzeń, nie czyść zasobów utworzonych w tym artykule. Jeśli nie planujesz kontynuować pracy, użyj następującego polecenia, aby usunąć zasoby utworzone w tym artykule.

Zamień `<resource_group_name>` na utworzoną powyżej grupę zasobów.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak tworzyć tematy i subskrypcje zdarzeń dla Azure Policy, Dowiedz się więcej o zdarzeniach zmiany stanu zasad i o tym, co Event Grid może Ci pomóc:

- [Oddziałanie do Azure Policy zdarzeń zmiany stanu](../concepts/event-overview.md)
- [Azure Policy szczegóły schematu Event Grid](../../../event-grid/event-schema-policy.md)
- [Event Grid — informacje](../../../event-grid/overview.md)