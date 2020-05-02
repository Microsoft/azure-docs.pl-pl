---
title: Szybki Start — tworzenie rejestru z replikacją geograficzną — Menedżer zasobów szablonu
description: W tym przewodniku szybki start użyjesz szablonu Azure Resource Manager, aby szybko wdrożyć aplikację sieci Web, która działa w izolowanym wystąpieniu kontenera platformy Azure.
services: azure-resource-manager
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/30/2020
ms.openlocfilehash: 92856f48cb915dd91f132a61e83a2da776db5170
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629910"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-a-resource-manager-template"></a>Szybki Start: Wdrażanie wystąpienia kontenera na platformie Azure przy użyciu szablonu Menedżer zasobów

Używanie Azure Container Instances do uruchamiania kontenerów platformy Docker bez serwera na platformie Azure z prostotą i szybkością. Wdróż aplikację w wystąpieniu kontenera na żądanie, gdy nie potrzebujesz pełnej platformy aranżacji kontenerów, takiej jak usługa Azure Kubernetes.

W tym przewodniku szybki start użyjesz szablonu Azure Resource Manager do wdrożenia izolowanego kontenera platformy Docker i udostępnienia jego aplikacji sieci Web za pomocą publicznego adresu IP. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="prerequisites"></a>Wymagania wstępne

Brak.

## <a name="create-a-container-instance"></a>Tworzenie wystąpienia kontenera

### <a name="review-the-template"></a>Zapoznaj się z szablonem

Szablon używany w tym przewodniku szybki start pochodzi z [szablonów szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-linuxcontainer-public-ip).

[!code-json[<Azure Resource Manager template create geo-replicated registry>](~/quickstart-templates/101-aci-linuxcontainer-public-ip/azuredeploy.json)]

Następujący zasób jest zdefiniowany w szablonie:

* **[Microsoft. ContainerInstance/containerGroups](/azure/templates/microsoft.containerinstance/containergroups)**: Tworzenie grupy kontenerów platformy Azure. Ten szablon definiuje grupę składającą się z jednego wystąpienia kontenera.

Więcej przykładów szablonów Azure Container Instances można znaleźć w [galerii szablonów szybkiego startu](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Containerinstance&pageNumber=1&sort=Popular).

### <a name="deploy-the-template"></a>Wdrożenie szablonu

 1. Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon. Szablon tworzy rejestr i replikę w innej lokalizacji.

    [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aci-linuxcontainer-public-ip%2Fazuredeploy.json)

 2. Wybierz lub wprowadź następujące wartości.

    * **Subskrypcja**: wybierz subskrypcję platformy Azure.
    * **Grupa zasobów**: wybierz pozycję **Utwórz nową**, wprowadź unikatową nazwę grupy zasobów, a następnie wybierz przycisk **OK**.
    * **Lokalizacja**: wybierz lokalizację grupy zasobów. Przykład: **środkowe stany USA**.
    * **Nazwa**: Zaakceptuj wygenerowaną nazwę wystąpienia lub wprowadź nazwę.
    * **Obraz**: zaakceptuj domyślną nazwę obrazu. W tym przykładowym obrazie systemu Linux jest to mała aplikacja internetowa zapisywana w języku Node. js, która obsługuje statyczną stronę HTML. 

    Zaakceptuj wartości domyślne dla pozostałych właściwości.

    Przejrzyj warunki i postanowienia. Jeśli zgadzasz się, wybierz opcję **Akceptuję warunki i postanowienia podane powyżej**.

    ![Właściwości szablonu](media/container-instances-quickstart-template/template-properties.png)

 3. Po pomyślnym utworzeniu wystąpienia otrzymujesz powiadomienie:

    ![Powiadomienie portalu](media/container-instances-quickstart-template/deployment-notification.png)

 Azure Portal jest używany do wdrożenia szablonu. Oprócz Azure Portal można użyć Azure PowerShell, interfejsu wiersza polecenia platformy Azure i usługi API REST. Aby poznać inne metody wdrażania, zobacz [wdrażanie szablonów](../azure-resource-manager/templates/deploy-cli.md).

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

Użyj Azure Portal lub narzędzia, takiego jak [interfejs wiersza polecenia platformy Azure](container-instances-quickstart.md) , aby przejrzeć właściwości wystąpienia kontenera.

1. W portalu Wyszukaj Container Instances i wybierz utworzone wystąpienie kontenera.

1. Na stronie **Przegląd** Sprawdź **stan** wystąpienia i jego **adres IP**.

    ![Przegląd wystąpienia](media/container-instances-quickstart-template/aci-overview.png)

2. Gdy stan jest *uruchomiony*, przejdź na adres IP w przeglądarce. 

    ![Widziana w przeglądarce aplikacja wdrożona za pomocą usługi Azure Container Instances](media/container-instances-quickstart-template/view-application-running-in-an-azure-container-instance.png)

### <a name="view-container-logs"></a>Wyświetlanie dzienników kontenerów

W przypadku rozwiązywania problemów z kontenerem lub uruchomioną w nim aplikacją przydatne jest przeglądanie dzienników wystąpienia kontenera.

Aby wyświetlić dzienniki kontenera, w obszarze **Ustawienia**wybierz pozycję Dzienniki **kontenerów** > **Logs**. Powinno pojawić się żądanie HTTP GET generowane podczas wyświetlania aplikacji w przeglądarce.

![Dzienniki kontenera w witrynie Azure Portal](media/container-instances-quickstart-template/aci-logs.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy z kontenerem na stronie **Przegląd** dla wystąpienia kontenera wybierz pozycję **Usuń**. Po wyświetleniu monitu potwierdź usunięcie.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono wystąpienie kontenera platformy Azure na podstawie publicznego obrazu firmy Microsoft. Jeśli chcesz skompilować obraz kontenera i wdrożyć go za pomocą prywatnej usługi Azure Container Registry, przejdź do samouczka dotyczącego usługi Azure Container Instances.

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie obrazu kontenera na potrzeby wdrożenia Azure Container Instances](./container-instances-tutorial-prepare-app.md)

Aby zapoznać się z samouczkiem krok po kroku, który przeprowadzi Cię przez proces tworzenia szablonu, zobacz:

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie i wdrażanie pierwszego szablonu Azure Resource Manager](/azure/azure-resource-manager/templates/template-tutorial-create-first-template.md)