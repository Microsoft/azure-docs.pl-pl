---
title: Webhook do reagowania na akcje rejestru
description: Dowiedz się, jak używać webhook do wyzwalania zdarzeń w przypadku wystąpienia akcji wypychania lub ściągania w repozytoriach rejestru.
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 4f6fb719f8d9d51429a19616aa5548b32a2687e0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773404"
---
# <a name="using-azure-container-registry-webhooks"></a>Używanie Azure Container Registry webhook

Rejestr kontenera platformy Azure przechowuje prywatne obrazy kontenerów platformy Docker i zarządza nimi podobnie, jak koncentrator platformy Docker przechowuje publiczne obrazy platformy Docker. Może również hostować repozytoria dla pakietów [Helm](container-registry-helm-repos.md) (wersja zapoznawcza), czyli formatu pakietów do wdrażania aplikacji na platformie Kubernetes. Za pomocą webhook można wyzwalać zdarzenia, gdy pewne akcje są podejmowane w jednym z repozytoriów rejestru. Webhook może odpowiadać na zdarzenia na poziomie rejestru lub może być ograniczony do określonego tagu repozytorium. Rejestr z  [replikacją geograficzną](container-registry-geo-replication.md) umożliwia skonfigurowanie każdego z nich w celu reagowania na zdarzenia w określonej replice regionalnej.

Punkt końcowy dla urządzenia webhook musi być publicznie dostępny z rejestru. Można skonfigurować żądania rejestru dla webhook do uwierzytelniania w zabezpieczonym punkcie końcowym.

Aby uzyskać szczegółowe informacje na temat żądań dotyczących obiektów webhook, [zobacz Azure Container Registry webhook schema reference](container-registry-webhook-reference.md)(

## <a name="prerequisites"></a>Wymagania wstępne

* Usługa Azure Container Registry — Tworzy rejestr kontenera w subskrypcji platformy Azure. Na przykład użyj interfejsu [Azure Portal](container-registry-get-started-portal.md) lub interfejsu wiersza [polecenia platformy Azure.](container-registry-get-started-azure-cli.md) Warstwy [Azure Container Registry mają](container-registry-skus.md) różne limity przydziału dla webhook.
* Interfejs wiersza polecenia platformy Docker — Aby skonfigurować lokalny komputer jako hosta platformy Docker i uzyskać dostęp do poleceń interfejsu wiersza polecenia platformy Docker, zainstaluj [aparat platformy Docker](https://docs.docker.com/engine/installation/).

## <a name="create-webhook---azure-portal"></a>Tworzenie webhook — Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do rejestru kontenerów, w którym chcesz utworzyć webhook.
1. W **obszarze Usługi** wybierz pozycję **Webhook**.
1. Wybierz **pozycję Dodaj** na pasku narzędzi webhook.
1. Wypełnij formularz *Tworzenie elementy webhook,* aby uzyskać następujące informacje:

| Wartość | Opis |
|---|---|
| Nazwa urządzenia webhook | Nazwa, którą chcesz nadać do webhook. Może zawierać tylko litery i cyfry i musi mieć długość od 5 do 50 znaków. |
| Lokalizacja | W przypadku [rejestru z replikacją](container-registry-geo-replication.md) geograficzną określ region platformy Azure repliki rejestru. 
| Service URI | URI, do którego powinien wysyłać powiadomienia POST. |
| Nagłówki niestandardowe | Nagłówki, które chcesz przekazać wraz z żądaniem POST. Powinny one być w formacie "klucz: wartość". |
| Akcje wyzwalacza | Akcje, które wyzwalają webhook. Akcje obejmują wypychanie obrazów, usuwanie obrazu, wypychanie elementu chart narzędzia Helm, usuwanie elementu chart narzędzia Helm i kwarantannę obrazu. Możesz wybrać jedną lub więcej akcji, aby wyzwolić ten webhook. |
| Stan | Stan dla tego webhook po jego utworzeniu. Jest ona domyślnie włączona. |
| Zakres | Zakres, w którym działa ten webhook. Jeśli nie zostanie określony, zakres jest dla wszystkich zdarzeń w rejestrze. Można go określić dla repozytorium lub tagu przy użyciu formatu "repository:tag" lub "repository:*" dla wszystkich tagów w repozytorium. |

Przykładowy formularz webhook:

![Zrzut ekranu przedstawiający interfejs I tworzenia webhook usługi ACR w Azure Portal.](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook---azure-cli"></a>Tworzenie webhook — interfejs wiersza polecenia platformy Azure

Aby utworzyć webhook przy użyciu interfejsu wiersza polecenia platformy Azure, użyj [polecenia az acr webhook create.](/cli/azure/acr/webhook#az_acr_webhook_create) Następujące polecenie tworzy elementy webhook dla wszystkich zdarzeń usuwania obrazów w rejestrze *mycontainerregistry:*

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Testowanie webhook

### <a name="azure-portal"></a>Azure Portal

Przed użyciem tego webhook możesz przetestować go za pomocą **przycisku Ping.** Polecenie ping wysyła ogólne żądanie POST do określonego punktu końcowego i rejestruje odpowiedź. Użycie funkcji ping może pomóc w sprawdzeniu, czy element webhook został poprawnie skonfigurowany.

1. Wybierz pozycję webhook, który chcesz przetestować.
2. Na górnym pasku narzędzi wybierz pozycję **Wyślij polecenie Ping.**
3. Sprawdź odpowiedź punktu końcowego w kolumnie **STAN PROTOKOŁU HTTP.**

![Interfejs użytkownika tworzenia elementów webhook usługi ACR w Azure Portal](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby przetestować element webhook usługi ACR przy użyciu interfejsu wiersza polecenia platformy Azure, użyj [polecenia az acr webhook ping.](/cli/azure/acr/webhook#az_acr_webhook_ping)

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Aby wyświetlić wyniki, użyj [polecenia az acr webhook list-events.](/cli/azure/acr/webhook)

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Usuwanie webhook

### <a name="azure-portal"></a>Azure Portal

Każdy elementy webhook można usunąć, wybierając pozycję webhook, a następnie przycisk **Usuń** w Azure Portal.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>Następne kroki

### <a name="webhook-schema-reference"></a>Odwołanie do schematu obiektu webhook

Aby uzyskać szczegółowe informacje na temat formatu i właściwości ładunków zdarzeń JSON emitowanych przez Azure Container Registry, zobacz informacje o schemacie obiektu webhook:

[Azure Container Registry schematu obiektu webhook](container-registry-webhook-reference.md)

### <a name="event-grid-events"></a>Event Grid zdarzeń

Oprócz zdarzeń natywnych wpisów webhook rejestru omówiony w tym artykule, Azure Container Registry emitować zdarzenia do Event Grid:

[Szybki start: wysyłanie zdarzeń rejestru kontenerów do Event Grid](container-registry-event-grid-quickstart.md)
