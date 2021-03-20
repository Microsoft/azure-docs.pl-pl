---
title: Wysyłanie zdarzeń magazynu obiektów BLOB do punktu końcowego sieci Web — szablon
description: Użyj Azure Event Grid i szablonu Azure Resource Manager do tworzenia konta usługi BLOB Storage i subskrybowania jego zdarzeń. Wyślij zdarzenia do elementu webhook ".
ms.date: 07/07/2020
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: bfaee324f3e46f64fd4ad0d8b7e1240331b56c27
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92093654"
---
# <a name="quickstart-route-blob-storage-events-to-web-endpoint-by-using-an-arm-template"></a>Szybki Start: kierowanie zdarzeń usługi BLOB Storage do punktu końcowego w sieci Web przy użyciu szablonu ARM

Azure Event Grid to usługa obsługi zdarzeń dla chmury. W tym artykule opisano użycie szablonu Azure Resource Manager (szablon ARM) do tworzenia konta usługi BLOB Storage, subskrybowania zdarzeń dla tego magazynu obiektów blob i wyzwalania zdarzenia w celu wyświetlenia wyniku. Zazwyczaj użytkownik wysyła zdarzenia do punktu końcowego, w którym następuje przetwarzanie danych zdarzenia i są wykonywane akcje. Jednak aby uprościć ten artykuł, zdarzenia zostaną wysłane do aplikacji internetowej, która zbiera i wyświetla komunikaty.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-event-grid-subscription-and-storage%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

### <a name="create-a-message-endpoint"></a>Tworzenie punktu końcowego komunikatów

Przed zasubskrybowaniem zdarzeń w ramach usługi Blob Storage utwórzmy punkt końcowy dla komunikatów o zdarzeniach. Zazwyczaj w punkcie końcowym akcje są wykonywane na podstawie danych zdarzenia. Aby uprościć ten przewodnik Szybki Start, wdrożysz [wstępnie zbudowaną aplikację sieci Web](https://github.com/Azure-Samples/azure-event-grid-viewer), która będzie wyświetlać komunikaty o zdarzeniach. Wdrożone rozwiązanie zawiera plan usługi App Service, aplikację internetową usługi App Service i kod źródłowy z repozytorium GitHub.

1. Wybierz pozycję **Wdróż na platformie Azure** w celu wdrożenia rozwiązania w subskrypcji. W witrynie Azure Portal podaj wartości parametrów.

    [Wdróż na platformie Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json)
1. Wdrożenie może potrwać kilka minut. Po pomyślnym wdrożeniu należy wyświetlić aplikację sieci Web i upewnić się, że jest uruchomiona. W przeglądarce sieci Web przejdź do: `https://<your-site-name>.azurewebsites.net`

1. Zobaczysz witrynę, w której nie opublikowano jeszcze żadnych zdarzeń.

   ![Wyświetlanie nowej witryny](./media/blob-event-quickstart-portal/view-site.png)

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-event-grid-subscription-and-storage/).

:::code language="json" source="~/quickstart-templates/101-event-grid-subscription-and-storage/azuredeploy.json":::

Dwa zasoby platformy Azure są zdefiniowane w szablonie:

* [**Microsoft. Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts): Tworzenie konta usługi Azure Storage.
* [**Microsoft. EventGrid/systemTopics**](/azure/templates/microsoft.eventgrid/systemtopics): Tworzenie tematu systemu o określonej nazwie dla konta magazynu.
* [**Microsoft. EventGrid/systemTopics/eventSubscriptions**](/azure/templates/microsoft.eventgrid/systemtopics/eventsubscriptions): tworzenie subskrypcji Azure Event Grid dla tematu systemowego.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz poniższy link, aby zalogować się na platformie Azure i otworzyć szablon. Ten szablon umożliwia utworzenie magazynu kluczy oraz wpisu tajnego.

    [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-event-grid-subscription-and-storage%2Fazuredeploy.json)

2. Określ **punkt końcowy**: Podaj adres URL aplikacji sieci Web i Dodaj adres `api/updates` URL strony głównej.
3. Wybierz pozycję **Kup** , aby wdrożyć szablon.

  Azure Portal jest używany tutaj do wdrożenia szablonu. Można również użyć Azure PowerShell, interfejsu wiersza polecenia platformy Azure i API REST. Aby poznać inne metody wdrażania, zobacz [wdrażanie szablonów](../azure-resource-manager/templates/deploy-powershell.md).

> [!NOTE]
> Więcej przykładów szablonów Azure Event Grid można znaleźć [tutaj](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Eventgrid&pageNumber=1&sort=Popular).

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

Wyświetl aplikację sieci Web ponownie i zwróć uwagę, że zdarzenie sprawdzania poprawności subskrypcji zostało do niej wysłane. Wybierz ikonę oka, aby rozwinąć dane zdarzenia. Usługa Event Grid wysyła zdarzenie weryfikacji, aby w punkcie końcowym mogło nastąpić sprawdzenie, czy dane zdarzenia mają być odbierane. Aplikacja internetowa zawiera kod do sprawdzania poprawności subskrypcji.

![Wyświetlanie zdarzenia subskrypcji](./media/blob-event-quickstart-portal/view-subscription-event.png)

Teraz wyzwólmy zdarzenie, aby zobaczyć, jak usługa Event Grid dystrybuuje komunikat do punktu końcowego.

Zdarzenie magazynu Blob Storage jest wyzwalane przez przekazanie pliku. Plik nie wymaga żadnej określonej zawartości. W artykule założono, że masz plik o nazwie testfile.txt, ale możesz użyć dowolnego pliku.

Podczas przekazywania pliku do magazynu obiektów blob platformy Azure program Event Grid wysyła komunikat do punktu końcowego skonfigurowanego podczas subskrybowania. Komunikat jest w formacie JSON i zawiera tablicę z co najmniej jednym zdarzeniem. W poniższym przykładzie komunikat JSON zawiera tablicę z jednym zdarzeniem. Wyświetl aplikację internetową i zwróć uwagę, że otrzymano zdarzenie utworzone przez obiekt blob.

![Wyświetlanie wyników](./media/blob-event-quickstart-portal/view-results.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli grupa zasobów nie będzie już konieczna, [Usuń ją](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group
).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat szablonów Azure Resource Manager, zobacz następujące artykuły:

* [Dokumentacja usługi Azure Resource Manager](../azure-resource-manager/index.yml)
* [Definiowanie zasobów w szablonie usługi Azure Resource Manager](/azure/templates/)
* [Szablony przewodników Szybki start platformy Azure](https://azure.microsoft.com/resources/templates/)
* [Szablony Azure Event Grid](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Eventgrid).
