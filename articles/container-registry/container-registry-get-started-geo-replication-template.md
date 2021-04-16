---
title: Szybki start — tworzenie rejestru z replikacją geograficzną — Azure Resource Manager szablonu
description: Dowiedz się, jak utworzyć rejestr kontenerów platformy Azure z replikacją geograficzną przy użyciu Azure Resource Manager szablonu.
services: azure-resource-manager
author: dlepow
ms.author: danlep
ms.date: 10/06/2020
ms.topic: quickstart
ms.service: azure-resource-manager
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: c59c7897054b2ad65a76353e6d886af46cac91e0
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537430"
---
# <a name="quickstart-create-a-geo-replicated-container-registry-by-using-an-arm-template"></a>Szybki start: tworzenie rejestru kontenerów z replikacją geograficzną przy użyciu szablonu usługi ARM

W tym przewodniku Szybki start pokazano, jak utworzyć Azure Container Registry przy użyciu szablonu Azure Resource Manager (szablonu USŁUGI ARM). Szablon konfiguruje rejestr [z replikacją](container-registry-geo-replication.md) geograficzną, który automatycznie synchronizuje zawartość rejestru w więcej niż jednym regionie platformy Azure. Replikacja geograficzna umożliwia dostęp do obrazów z wdrożeń regionalnych w pobliżu sieci, zapewniając jednocześnie jedno środowisko zarządzania. Jest to funkcja warstwy usługi [rejestru Premium.](container-registry-skus.md)

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-container-registry-geo-replication%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-container-registry-geo-replication/). Szablon konfiguruje rejestr i dodatkową replikę regionalną.

:::code language="json" source="~/quickstart-templates/101-container-registry-geo-replication/azuredeploy.json":::

W szablonie zdefiniowano następujące zasoby:

* **[Microsoft.ContainerRegistry/registries:](/azure/templates/microsoft.containerregistry/registries)** tworzenie rejestru kontenerów platformy Azure
* **[Microsoft.ContainerRegistry/registries/replications:](/azure/templates/microsoft.containerregistry/registries/replications)** tworzenie repliki rejestru kontenerów

Więcej Azure Container Registry przykładów szablonów można znaleźć w [galerii szablonów szybkiego startu.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Containerregistry&pageNumber=1&sort=Popular)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

 1. Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon.

    [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-container-registry-geo-replication%2Fazuredeploy.json)

 1. Wybierz lub wprowadź następujące wartości.

    * **Subskrypcja**: wybierz subskrypcję platformy Azure.
    * **Grupa zasobów:** wybierz **pozycję Utwórz nową,** wprowadź unikatową nazwę grupy zasobów, a następnie wybierz przycisk **OK.**
    * **Region:** wybierz lokalizację grupy zasobów. Przykład: **Środkowe usa**.
    * **Nazwa ACR:** zaakceptuj wygenerowaną nazwę rejestru lub wprowadź nazwę. Musi być unikatowa w skali globalnej.
    * **Acr Admin User Enabled (Włączony użytkownik administracyjny usługi ACR):** zaakceptuj wartość domyślną.
    * **Lokalizacja:** zaakceptuj wygenerowaną lokalizację dla repliki głównej rejestru lub wprowadź lokalizację, taką jak **Środkowe usa.** 
    * **Acr Sku:** zaakceptuj wartość domyślną.
    * **Lokalizacja repliki ACR:** wprowadź lokalizację repliki rejestru przy użyciu krótkiej nazwy regionu. Musi ona być inna niż lokalizacja rejestru macierzystego. Przykład: **westeurope**.

        :::image type="content" source="media/container-registry-get-started-geo-replication-template/template-properties.png" alt-text="Właściwości szablonu":::

1. Wybierz **pozycję Przeglądanie + tworzenie,** a następnie przejrzyj warunki i postanowienia. Jeśli się zgadzasz, wybierz pozycję **Utwórz**.

1. Po pomyślnym utworzeniu rejestru otrzymasz powiadomienie:

     :::image type="content" source="media/container-registry-get-started-geo-replication-template/deployment-notification.png" alt-text="Powiadomienie w portalu":::

 Szablon jest wdrażany za pomocą witryny Azure Portal. Oprócz interfejsu Azure Portal można użyć interfejsu Azure PowerShell, interfejsu wiersza polecenia platformy Azure i interfejsu API REST. Aby poznać inne metody wdrażania, zobacz [Wdrażanie szablonów](../azure-resource-manager/templates/deploy-cli.md).

## <a name="review-deployed-resources"></a>Przeglądanie wdrożonych zasobów

Użyj Azure Portal lub narzędzia, takiego jak interfejs wiersza polecenia platformy Azure, aby przejrzeć właściwości rejestru kontenerów.

1. W portalu wyszukaj pozycję Rejestry kontenerów i wybierz utworzony rejestr kontenerów.

1. Na **stronie** Przegląd zanotuj **wartość Serwer logowania** rejestru. Użyj tego URI, gdy używasz platformy Docker do tagów i wypychania obrazów do rejestru. Aby uzyskać więcej informacji, zobacz [Push your first image using the Docker CLI (Wypychanie pierwszego obrazu przy użyciu interfejsu wiersza polecenia platformy Docker).](container-registry-get-started-docker-cli.md)

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/registry-overview.png" alt-text="Omówienie rejestru":::

1. Na **stronie Replikacje** potwierdź lokalizacje repliki głównej i repliki dodanej za pomocą szablonu. W razie potrzeby dodaj więcej replik na tej stronie.

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/registry-replications.png" alt-text="Replikacje rejestru":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie będą już potrzebne, usuń grupę zasobów, rejestr i replikę rejestru. W tym celu przejdź do Azure Portal, wybierz grupę zasobów zawierającą rejestr, a następnie wybierz pozycję **Usuń grupę zasobów.**

:::image type="content" source="media/container-registry-get-started-geo-replication-template/delete-resource-group.png" alt-text="Usuwanie grupy zasobów":::

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono Azure Container Registry szablonu usługi ARM i skonfigurowano replikę rejestru w innej lokalizacji. Przejdź do samouczków usługi Azure Container Registry, aby dowiedzieć się więcej o tej usłudze.

> [!div class="nextstepaction"]
> [Samouczki dotyczące usługi Azure Container Registry](container-registry-tutorial-prepare-registry.md)

Aby uzyskać samouczek krok po kroku, który przeprowadzi Cię przez proces tworzenia szablonu, zobacz:

> [!div class="nextstepaction"]
> [Samouczek: tworzenie i wdrażanie pierwszego szablonu usługi ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
