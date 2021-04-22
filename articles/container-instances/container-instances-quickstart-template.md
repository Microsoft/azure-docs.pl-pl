---
title: Szybki start — tworzenie wystąpienia kontenera — Azure Resource Manager szablonu
description: W tym przewodniku Szybki start użyjemy szablonu usługi Azure Resource Manager, aby szybko wdrożyć konteneryzowana aplikację internetową uruchamianą w izolowanym wystąpieniu kontenera platformy Azure.
services: azure-resource-manager
ms.date: 04/30/2020
ms.topic: quickstart
ms.service: azure-resource-manager
ms.custom:
- subject-armqs
- devx-track-js
- mode-arm
ms.openlocfilehash: 2f57e86421f7522467a3f4adf4a4e9a21456ba4c
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878744"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-an-arm-template"></a>Szybki start: wdrażanie wystąpienia kontenera na platformie Azure przy użyciu szablonu usługi ARM

Używaj Azure Container Instances, aby uruchamiać bez serwera kontenery platformy Docker na platformie Azure z prostotą i szybkością. Wdrażanie aplikacji w wystąpieniu kontenera na żądanie, gdy nie potrzebujesz pełnej platformy orkiestracji kontenerów, na przykład Azure Kubernetes Service. W tym przewodniku Szybki start użyjemy szablonu usługi Azure Resource Manager (arm), aby wdrożyć izolowany kontener platformy Docker i udostępnić jego aplikację internetową przy użyciu publicznego adresu IP.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aci-linuxcontainer-public-ip%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-aci-linuxcontainer-public-ip/).

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.containerinstance/aci-linuxcontainer-public-ip/azuredeploy.json":::

W szablonie zdefiniowano następujący zasób:

* **[Microsoft.ContainerInstance/containerGroups:](/azure/templates/microsoft.containerinstance/containergroups)** utwórz grupę kontenerów platformy Azure. Ten szablon definiuje grupę składającą się z jednego wystąpienia kontenera.

Więcej Azure Container Instances przykładów szablonów można znaleźć w galerii szablonów [szybkiego startu.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Containerinstance&pageNumber=1&sort=Popular)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

 1. Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon. Szablon tworzy rejestr i replikę w innej lokalizacji.

    [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aci-linuxcontainer-public-ip%2Fazuredeploy.json)

 2. Wybierz lub wprowadź następujące wartości.

    * **Subskrypcja**: wybierz subskrypcję platformy Azure.
    * **Grupa zasobów:** wybierz **pozycję Utwórz nową,** wprowadź unikatową nazwę grupy zasobów, a następnie wybierz przycisk **OK.**
    * **Lokalizacja**: wybierz lokalizację grupy zasobów. Przykład: **Środkowe usa**.
    * **Nazwa**: zaakceptuj wygenerowaną nazwę wystąpienia lub wprowadź nazwę.
    * **Obraz:** zaakceptuj domyślną nazwę obrazu. Ten przykładowy obraz systemu Linux pakuje małą aplikację internetową napisaną Node.js, która obsługuje statyczną stronę HTML. 

    Zaakceptuj wartości domyślne dla pozostałych właściwości.

    Zapoznaj się z warunkami i postanowieniami. Jeśli się zgadzasz, wybierz pozycję Wyrażam zgodę na **powyższe warunki i postanowienia.**

    ![Właściwości szablonu](media/container-instances-quickstart-template/template-properties.png)

 3. Po pomyślnym utworzeniu wystąpienia otrzymasz powiadomienie:

    ![Powiadomienie w portalu](media/container-instances-quickstart-template/deployment-notification.png)

 Szablon jest wdrażany za pomocą witryny Azure Portal. Oprócz interfejsu Azure Portal można użyć interfejsu Azure PowerShell, interfejsu wiersza polecenia platformy Azure i interfejsu API REST. Aby poznać inne metody wdrażania, zobacz [Wdrażanie szablonów](../azure-resource-manager/templates/deploy-cli.md).

## <a name="review-deployed-resources"></a>Przeglądanie wdrożonych zasobów

Użyj Azure Portal lub narzędzia, takiego jak interfejs wiersza polecenia [platformy Azure,](container-instances-quickstart.md) aby przejrzeć właściwości wystąpienia kontenera.

1. W portalu wyszukaj pozycję Container Instances i wybierz utworzone wystąpienie kontenera.

1. Na **stronie Przegląd** zanotuj **stan** wystąpienia i jego **adres IP**.

    ![Przegląd wystąpienia](media/container-instances-quickstart-template/aci-overview.png)

2. Gdy jego stan to *Uruchomiona,* przejdź do adresu IP w przeglądarce. 

    ![Widziana w przeglądarce aplikacja wdrożona za pomocą usługi Azure Container Instances](media/container-instances-quickstart-template/view-application-running-in-an-azure-container-instance.png)

### <a name="view-container-logs"></a>Wyświetlanie dzienników kontenerów

W przypadku rozwiązywania problemów z kontenerem lub uruchomioną w nim aplikacją przydatne jest przeglądanie dzienników wystąpienia kontenera.

Aby wyświetlić dzienniki kontenera, w obszarze **Ustawienia wybierz** pozycję **Dzienniki**  >  **kontenerów.** Powinno pojawić się żądanie HTTP GET generowane podczas wyświetlania aplikacji w przeglądarce.

![Dzienniki kontenera w witrynie Azure Portal](media/container-instances-quickstart-template/aci-logs.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy wszystko będzie gotowe do pracy z kontenerem, na stronie **Przegląd** wystąpienia kontenera wybierz pozycję **Usuń**. Po wyświetleniu monitu potwierdź usunięcie.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono wystąpienie kontenera platformy Azure na podstawie publicznego obrazu firmy Microsoft. Jeśli chcesz skompilować obraz kontenera i wdrożyć go za pomocą prywatnej usługi Azure Container Registry, przejdź do samouczka dotyczącego usługi Azure Container Instances.

> [!div class="nextstepaction"]
> [Samouczek: tworzenie obrazu kontenera do wdrożenia w Azure Container Instances](./container-instances-tutorial-prepare-app.md)

Aby uzyskać samouczek krok po kroku, który przeprowadzi Cię przez proces tworzenia szablonu, zobacz:

> [!div class="nextstepaction"]
> [Samouczek: tworzenie i wdrażanie pierwszego szablonu usługi ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
