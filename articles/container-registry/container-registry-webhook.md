---
title: Elementy webhook odpowiadające na akcje rejestru
description: Dowiedz się, jak używać elementów webhook do wyzwalania zdarzeń w przypadku akcji wypychania lub ściągania w repozytoriach rejestru.
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 5374b58ba72727500294a173c26e9a131b29fe34
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101722249"
---
# <a name="using-azure-container-registry-webhooks"></a>Używanie Azure Container Registry elementów webhook

Rejestr kontenera platformy Azure przechowuje prywatne obrazy kontenerów platformy Docker i zarządza nimi podobnie, jak koncentrator platformy Docker przechowuje publiczne obrazy platformy Docker. Może również hostować repozytoria dla [wykresów Helm](container-registry-helm-repos.md) (wersja zapoznawcza) — format pakietu do wdrażania aplikacji w Kubernetes. Elementów webhook można użyć do wyzwalania zdarzeń, gdy pewne akcje są wykonywane w jednym z repozytoriów rejestru. Elementy webhook mogą reagować na zdarzenia na poziomie rejestru lub mogą być ograniczone do określonego tagu repozytorium. W przypadku rejestru z  [replikacją geograficzną](container-registry-geo-replication.md) należy skonfigurować każdy element webhook w celu reagowania na zdarzenia w określonej replice regionalnej.

Punkt końcowy elementu webhook musi być publicznie dostępny z rejestru. Można skonfigurować żądania elementu webhook rejestru w celu uwierzytelnienia w zabezpieczonym punkcie końcowym.

Aby uzyskać szczegółowe informacje o żądaniach elementu webhook, zobacz [Azure Container Registry odwołanie do schematu elementu webhook](container-registry-webhook-reference.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Usługa Azure Container Registry — Tworzy rejestr kontenera w subskrypcji platformy Azure. Na przykład użyj [Azure Portal](container-registry-get-started-portal.md) lub [interfejsu wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md). [Warstwy usługi Azure Container Registry](container-registry-skus.md) mają różne przydziały elementów webhook.
* Interfejs wiersza polecenia platformy Docker — Aby skonfigurować lokalny komputer jako hosta platformy Docker i uzyskać dostęp do poleceń interfejsu wiersza polecenia platformy Docker, zainstaluj [aparat platformy Docker](https://docs.docker.com/engine/installation/).

## <a name="create-webhook---azure-portal"></a>Utwórz element webhook — Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do rejestru kontenerów, w której chcesz utworzyć element webhook.
1. W obszarze **usługi** wybierz pozycję elementy **webhook**.
1. Wybierz pozycję **Dodaj** na pasku narzędzi elementu webhook.
1. Wypełnij formularz *Utwórz element webhook* , podając następujące informacje:

| Wartość | Opis |
|---|---|
| Nazwa elementu webhook | Nazwa, która ma zostać nadana elementowi webhook. Może zawierać tylko litery i cyfry i musi mieć 5-50 znaków. |
| Lokalizacja | W przypadku rejestru z [replikacją geograficzną](container-registry-geo-replication.md) Określ region platformy Azure repliki rejestru. 
| Identyfikator URI usługi | Identyfikator URI, w którym element webhook powinien wysyłać powiadomienia po stronie. |
| Nagłówki niestandardowe | Nagłówki, które mają zostać przekazane wraz z żądaniem POST. Powinny mieć format "klucz: wartość". |
| Akcje wyzwalania | Akcje wyzwalające element webhook. Akcje obejmują wypychanie obrazu, Usuwanie obrazu, Helm wykresu wypychania, usuwanie wykresu Helm i kwarantannę obrazu. Możesz wybrać jedną lub więcej akcji, aby wyzwolić element webhook. |
| Stan | Stan elementu webhook po jego utworzeniu. Jest on domyślnie włączony. |
| Zakres | Zakres, w którym działa element webhook. Jeśli nie zostanie określony, zakres dotyczy wszystkich zdarzeń w rejestrze. Może być określony dla repozytorium lub tag przy użyciu formatu "Repository: tag" lub "Repository: *" dla wszystkich tagów w repozytorium. |

Przykładowy formularz elementu webhook:

![Zrzut ekranu przedstawiający tworzenie elementu webhook ACR U I w Azure Portal.](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook---azure-cli"></a>Tworzenie elementu webhook — interfejs wiersza polecenia platformy Azure

Aby utworzyć element webhook przy użyciu interfejsu wiersza polecenia platformy Azure, użyj polecenie [AZ ACR webhook Create](/cli/azure/acr/webhook#az-acr-webhook-create) . Następujące polecenie tworzy element webhook dla wszystkich zdarzeń usunięcia obrazu w rejestrze *mycontainerregistry*:

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Testuj element webhook

### <a name="azure-portal"></a>Azure Portal

Przed użyciem elementu webhook można przetestować go za pomocą przycisku **ping** . Polecenie ping wysyła ogólne żądanie POST do określonego punktu końcowego i rejestruje odpowiedź. Korzystanie z funkcji ping może pomóc w sprawdzeniu, czy poprawnie skonfigurowano element webhook.

1. Wybierz element webhook, który chcesz przetestować.
2. Na górnym pasku narzędzi wybierz polecenie **ping**.
3. Sprawdź odpowiedź punktu końcowego w kolumnie **stan http** .

![Interfejs użytkownika tworzenia elementu webhook ACR w Azure Portal](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby przetestować element webhook ACR za pomocą interfejsu wiersza polecenia platformy Azure, użyj polecenie [AZ ACR webhook ping](/cli/azure/acr/webhook#az-acr-webhook-ping) .

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Aby wyświetlić wyniki, użyj polecenia [AZ ACR webhook list-Events](/cli/azure/acr/webhook) .

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Usuń element webhook

### <a name="azure-portal"></a>Azure Portal

Każdy element webhook można usunąć, wybierając element webhook, a następnie przycisk **Usuń** w Azure Portal.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>Następne kroki

### <a name="webhook-schema-reference"></a>Odwołanie do schematu elementu webhook

Aby uzyskać szczegółowe informacje o formacie i właściwościach ładunków zdarzeń JSON emitowanych przez Azure Container Registry, zobacz odwołanie do schematu elementu webhook:

[Odwołanie do schematu elementu webhook Azure Container Registry](container-registry-webhook-reference.md)

### <a name="event-grid-events"></a>Zdarzenia Event Grid

Oprócz natywnych zdarzeń elementu webhook rejestru omówionych w tym artykule Azure Container Registry mogą emitować zdarzenia do Event Grid:

[Szybki Start: wysyłanie zdarzeń rejestru kontenera do Event Grid](container-registry-event-grid-quickstart.md)
