---
title: Szybki Start — tworzenie rejestru z replikacją geograficzną — Azure Resource Manager szablonu
description: Informacje o sposobie tworzenia rejestru kontenerów platformy Azure z replikacją geograficzną przy użyciu szablonu Azure Resource Manager.
services: azure-resource-manager
author: dlepow
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: danlep
ms.date: 10/06/2020
ms.openlocfilehash: 97b556e0329644b973def8333ddb5e70e370b0bc
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91827002"
---
# <a name="quickstart-create-a-geo-replicated-container-registry-by-using-an-arm-template"></a>Szybki Start: tworzenie rejestru kontenerów z replikacją geograficzną przy użyciu szablonu ARM

W tym przewodniku szybki start pokazano, jak utworzyć wystąpienie Azure Container Registry przy użyciu szablonu Azure Resource Manager (szablon ARM). Szablon konfiguruje rejestr z [replikacją geograficzną](container-registry-geo-replication.md) , który automatycznie synchronizuje zawartość rejestru w więcej niż jednym regionie świadczenia usługi Azure. Replikacja geograficzna umożliwia dostęp do sieci w pobliżu obrazów ze wdrożeń regionalnych, zapewniając jednocześnie pojedyncze środowisko zarządzania. Jest to funkcja warstwy usługi rejestr w [warstwie Premium](container-registry-skus.md) .

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-container-registry-geo-replication%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-container-registry-geo-replication/). Szablon konfiguruje rejestr i dodatkową replikę regionalną.

:::code language="json" source="~/quickstart-templates/101-container-registry-geo-replication/azuredeploy.json":::

Następujące zasoby są zdefiniowane w szablonie:

* **[Microsoft. ContainerRegistry/rejestry](/azure/templates/microsoft.containerregistry/registries)**: tworzenie rejestru kontenerów platformy Azure
* **[Microsoft. ContainerRegistry/rejestry/replikacje](/azure/templates/microsoft.containerregistry/registries/replications)**: Tworzenie repliki rejestru kontenerów

Więcej przykładów szablonów Azure Container Registry można znaleźć w [galerii szablonów szybkiego startu](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Containerregistry&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Wdrożenie szablonu

 1. Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon.

    [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-container-registry-geo-replication%2Fazuredeploy.json)

 1. Wybierz lub wprowadź następujące wartości.

    * **Subskrypcja**: wybierz subskrypcję platformy Azure.
    * **Grupa zasobów**: wybierz pozycję **Utwórz nową**, wprowadź unikatową nazwę grupy zasobów, a następnie wybierz przycisk **OK**.
    * **Region**: Wybierz lokalizację dla grupy zasobów. Przykład: **środkowe stany USA**.
    * **Nazwa ACR**: Zaakceptuj wygenerowaną nazwę rejestru lub wprowadź nazwę. Musi być globalnie unikatowa.
    * **ACR administrator włączony**: Zaakceptuj wartość domyślną.
    * **Lokalizacja**: Zaakceptuj wygenerowaną lokalizację repliki głównej rejestru lub wprowadź lokalizację, taką jak **środkowe stany USA**. 
    * **ACR SKU**: Zaakceptuj wartość domyślną.
    * **Lokalizacja repliki usługi ACR**: wprowadź lokalizację repliki rejestru przy użyciu krótkiej nazwy regionu. Musi być inna niż lokalizacja w rejestrze macierzystym. Przykład: **westeurope**.

        :::image type="content" source="media/container-registry-get-started-geo-replication-template/template-properties.png" alt-text="Właściwości szablonu":::

1. Wybierz pozycję **Recenzja + Utwórz**, a następnie przejrzyj warunki i postanowienia. Jeśli zgadzasz się, wybierz pozycję **Utwórz**.

1. Po pomyślnym utworzeniu rejestru otrzymasz powiadomienie:

     :::image type="content" source="media/container-registry-get-started-geo-replication-template/deployment-notification.png" alt-text="Właściwości szablonu":::

 Szablon jest wdrażany za pomocą witryny Azure Portal. Oprócz Azure Portal można użyć Azure PowerShell, interfejsu wiersza polecenia platformy Azure i usługi API REST. Aby poznać inne metody wdrażania, zobacz [wdrażanie szablonów](../azure-resource-manager/templates/deploy-cli.md).

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

Użyj Azure Portal lub narzędzia, takiego jak interfejs wiersza polecenia platformy Azure, aby przejrzeć właściwości rejestru kontenerów.

1. W portalu Wyszukaj rejestry kontenerów i wybierz utworzony przez siebie rejestr kontenerów.

1. Na stronie **Przegląd** Zanotuj nazwę **serwera logowania** rejestru. Użyj tego identyfikatora URI w przypadku używania platformy Docker do tagów i wypychania obrazów do rejestru. Aby uzyskać więcej informacji, zobacz [wypychanie pierwszego obrazu przy użyciu interfejsu wiersza polecenia platformy Docker](container-registry-get-started-docker-cli.md).

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/registry-overview.png" alt-text="Właściwości szablonu":::

1. Na stronie **replikacje** Potwierdź lokalizacje repliki głównej oraz replikę dodaną przez szablon. W razie potrzeby Dodaj więcej replik na tej stronie.

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/registry-replications.png" alt-text="Właściwości szablonu":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie są już potrzebne, Usuń grupę zasobów, rejestr i replikę rejestru. W tym celu przejdź do Azure Portal, wybierz grupę zasobów zawierającą rejestr, a następnie wybierz pozycję **Usuń grupę zasobów**.

:::image type="content" source="media/container-registry-get-started-geo-replication-template/delete-resource-group.png" alt-text="Właściwości szablonu":::

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono Azure Container Registry z szablonem ARM i skonfigurowano replikę rejestru w innej lokalizacji. Przejdź do samouczków usługi Azure Container Registry, aby dowiedzieć się więcej o tej usłudze.

> [!div class="nextstepaction"]
> [Samouczki dotyczące usługi Azure Container Registry](container-registry-tutorial-prepare-registry.md)

Aby zapoznać się z samouczkiem krok po kroku, który przeprowadzi Cię przez proces tworzenia szablonu, zobacz:

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie i wdrażanie pierwszego szablonu ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
