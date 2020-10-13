---
title: Szybki Start — Tworzenie wystąpienia usługi Azure API Management przy użyciu szablonu ARM
description: Dowiedz się, jak utworzyć wystąpienie usługi Azure API Management w warstwie deweloper przy użyciu szablonu Azure Resource Manager (szablon ARM).
services: azure-resource-manager
author: dlepow
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: danlep
ms.date: 10/09/2020
ms.openlocfilehash: d4baa97794f53e59169d0e9cbbcbf80bc84ad9bb
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91935923"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-using-an-arm-template"></a>Szybki Start: Tworzenie nowego wystąpienia usługi Azure API Management przy użyciu szablonu ARM

W tym przewodniku szybki start opisano, jak używać szablonu Azure Resource Manager (szablon ARM) do tworzenia wystąpienia usługi Azure API Management (APIM). APIM pomaga organizacjom publikować interfejsy API dla deweloperów zewnętrznych, partnerskich i wewnętrznych, aby odblokować potencjał swoich danych i usług. Usługa API Management zapewnia podstawowe możliwości, które pozwalają na pomyślne programowanie interfejsów API przez zaangażowanych deweloperów, a także udostępnia wgląd w kwestie biznesowe, analizy, zabezpieczenia i ochronę. APIM umożliwia tworzenie nowoczesnych bram interfejsu API i zarządzanie nimi dla istniejących usług zaplecza hostowanych w dowolnym miejscu. Aby uzyskać więcej informacji, zapoznaj się z [omówieniem](api-management-key-concepts.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-api-management-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-azure-api-management-create/).


:::code language="json" source="~/quickstart-templates/101-azure-api-management-create/azuredeploy.json":::

Następujący zasób jest zdefiniowany w szablonie:

- **[Microsoft.ApiManagement/service](https://docs.microsoft.com/azure/templates/microsoft.apimanagement/service)**

Więcej przykładów szablonów API Management platformy Azure można znaleźć w [szablonach szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Apimanagement&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon. Szablon tworzy wystąpienie usługi API Management z automatycznie wygenerowaną nazwą. 
  
    [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-api-management-create%2Fazuredeploy.json)

    W tym przykładzie wystąpienie jest skonfigurowane w warstwie dewelopera, która jest ekonomiczną opcją oszacowania API Management platformy Azure. Ta warstwa nie jest używana do użycia w środowisku produkcyjnym. Aby uzyskać więcej informacji na temat skalowania warstw usługi API Management, zobacz [Upgrade and scale](upgrade-and-scale.md) (Uaktualnianie i skalowanie).

1. Wybierz lub wprowadź następujące wartości.
    * **Subskrypcja**: wybierz subskrypcję platformy Azure.
    * **Grupa zasobów**: wybierz pozycję **Utwórz nową**, wprowadź unikatową nazwę grupy zasobów, a następnie wybierz przycisk **OK**.
    * **Region**: Wybierz lokalizację dla grupy zasobów. Przykład: **środkowe stany USA**.
    * Adres **E-mail wydawcy**: wprowadź adres e-mail, na który mają być wysyłane powiadomienia.
    * **Nazwa wydawcy**: Wprowadź wybraną nazwę dla wydawcy interfejsu API.
    * **Jednostka SKU**: Zaakceptuj wartość domyślną **dewelopera**. 
    * **Liczba jednostek SKU**: Zaakceptuj wartość domyślną.
    * **Lokalizacja**: Zaakceptuj wygenerowaną lokalizację usługi API Management.

    :::image type="content" source="media/quickstart-arm-template/create-instance-template.png" alt-text="Właściwości szablonu API Management":::


 1. Wybierz pozycję **Recenzja + Utwórz**, a następnie przejrzyj warunki i postanowienia. Jeśli zgadzasz się, wybierz pozycję **Utwórz**.
    
    > [!TIP]
    >  Utworzenie i aktywowanie usługi API Management w warstwie deweloper może potrwać od 30 do 40 minut. 

 1. Po pomyślnym utworzeniu wystąpienia otrzymujesz powiadomienie:
 
    :::image type="content" source="media/quickstart-arm-template/deployment-notification.png" alt-text="Właściwości szablonu API Management":::

 Szablon jest wdrażany za pomocą witryny Azure Portal. Oprócz Azure Portal można również użyć Azure PowerShell, interfejsu wiersza polecenia platformy Azure i API REST. Aby poznać inne metody wdrażania, zobacz [wdrażanie szablonów](../azure-resource-manager/templates/deploy-cli.md).

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

Użyj Azure Portal, aby sprawdzić wdrożone zasoby, lub użyj narzędzi, takich jak interfejs wiersza polecenia platformy Azure lub Azure PowerShell, aby wyświetlić listę wdrożonych zasobów.


1. W [Azure Portal](https://portal.azure.com)Wyszukaj i wybierz pozycję **usługi API Management Services**, a następnie wybierz utworzone wystąpienie usługi.
1. Zapoznaj się z właściwościami usługi na stronie **Przegląd** .

:::image type="content" source="media/quickstart-arm-template/service-instance-created.png" alt-text="Właściwości szablonu API Management":::

Gdy wystąpienie usługi API Management jest w trybie online, możesz go używać. Zacznij od samouczka, aby [zaimportować i opublikować](import-and-publish.md) pierwszy interfejs API.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz kontynuować pracę z kolejnymi samouczkami, możesz chcieć opuścić wystąpienie API Management. Gdy grupa zasobów nie będzie już konieczna, usuń ją, co spowoduje usunięcie zasobów z grupy zasobów.

1. W [Azure Portal](https://portal.azure.com)Wyszukaj i wybierz pozycję **grupy zasobów**. Możesz również wybrać **grupy zasobów** na stronie **głównej** .
1. Na stronie **grupy zasobów** wybierz grupę zasobów.
1. Na stronie Grupa zasobów wybierz pozycję **Usuń grupę zasobów**.

    :::image type="content" source="media/quickstart-arm-template/delete-resource-group.png" alt-text="Właściwości szablonu API Management":::
1. Wpisz nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Importowanie i publikowanie pierwszego interfejsu API](import-and-publish.md)