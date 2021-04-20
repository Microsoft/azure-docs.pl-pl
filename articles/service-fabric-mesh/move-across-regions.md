---
title: Przenoszenie aplikacji Service Fabric Mesh do innego regionu
description: Możesz przenieść Service Fabric Mesh, wdrażając kopię bieżącego szablonu w nowym regionie platformy Azure.
author: erikadoyle
ms.author: edoyle
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 9f3fcdc56b4e8d7873872212cb62f57a7669b459
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726632"
---
# <a name="move-a-service-fabric-mesh-application-to-another-azure-region"></a>Przenoszenie aplikacji Service Fabric Mesh do innego regionu świadczenia usługi Azure

> [!IMPORTANT]
> Wersja zapoznawcza Azure Service Fabric Mesh została wycofana. Nowe wdrożenia nie będą już dozwolone za pośrednictwem interfejsu API Service Fabric Mesh API. Obsługa istniejących wdrożeń będzie kontynuowana do 28 kwietnia 2021 r.
> 
> Aby uzyskać szczegółowe informacje, zobacz [Azure Service Fabric Mesh wersji zapoznawczej wycofanie](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

W tym artykule opisano sposób przenoszenia aplikacji Service Fabric Mesh i jej zasobów do innego regionu świadczenia usługi Azure. Możesz przenieść zasoby do innego regionu z kilku powodów. Na przykład w odpowiedzi na przepustek, w celu uzyskania funkcji lub usług dostępnych tylko w określonych regionach, spełnienia wewnętrznych wymagań dotyczących zasad i ładu lub w odpowiedzi na wymagania dotyczące planowania pojemności.

 [Service Fabric Mesh nie obsługuje możliwości](../azure-resource-manager/management/move-support-resources.md#microsoftservicefabricmesh) bezpośredniego przenoszenia zasobów między regionami świadczenia usługi Azure. Zasoby można jednak przenieść pośrednio, wdrażając kopię bieżącego szablonu usługi Azure Resource Manager do nowego regionu docelowego, a następnie przekierowuje ruch przychodzący i zależności do nowo utworzonej aplikacji Service Fabric Mesh docelowej.

## <a name="prerequisites"></a>Wymagania wstępne

* Kontroler ruchu wychodzącego (taki [jak Application Gateway](../application-gateway/index.yml)) służy jako pośrednik w routingu ruchu między klientami a aplikacją Service Fabric Mesh klienta
* Service Fabric Mesh (wersja zapoznawcza) w docelowym regionie platformy Azure ( `westus` `eastus` , lub `westeurope` )

## <a name="prepare"></a>Przygotowywanie

1. Zrób "migawkę" bieżącego stanu aplikacji Service Fabric Mesh przez wyeksportowanie szablonu Azure Resource Manager parametrów z ostatniego wdrożenia. Aby to zrobić, wykonaj kroki opisane w te [tematu Eksportowanie szablonu po wdrożeniu](../azure-resource-manager/templates/export-template-portal.md#export-template-after-deployment) przy użyciu Azure Portal. Możesz również użyć interfejsu [wiersza polecenia](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates)platformy [Azure, interfejsu Azure PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)lub [interfejsu API REST.](/rest/api/resources/resourcegroups/exporttemplate)

2. Jeśli ma to [zastosowanie, wyeksportuj](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource-group) inne zasoby w tej samej grupie zasobów do ponownego użycia w regionie docelowym.

3. Przejrzyj (i w razie potrzeby edytuj) wyeksportowany szablon, aby upewnić się, że istniejące wartości właściwości są wartościami, których chcesz użyć w regionie docelowym. Nowy `location` (region świadczenia usługi Azure) jest parametrem, który należy podać podczas ponownego dostarczania.

## <a name="move"></a>Move

1. Utwórz nową grupę zasobów (lub użyj istniejącej) w regionie docelowym.

2. W przypadku wyeksportowanego szablonu wykonaj kroki opisane w te [tematu Wdrażanie zasobów z szablonu niestandardowego](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) przy użyciu Azure Portal. Możesz również użyć interfejsu wiersza polecenia platformy [Azure,](../azure-resource-manager/templates/deploy-cli.md) [interfejsu Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)lub [interfejsu API REST.](../azure-resource-manager/templates/deploy-rest.md)

3. Aby uzyskać wskazówki dotyczące przenoszenia powiązanych zasobów, takich jak konta [usługi Azure Storage,](../storage/common/storage-account-move.md)zapoznaj się ze wskazówkami dla poszczególnych usług wymienionymi w temacie Przenoszenie zasobów [platformy Azure między regionami.](../azure-resource-manager/management/move-resources-overview.md#move-resources-across-regions)

## <a name="verify"></a>Weryfikacja

1. Po zakończeniu wdrażania przetestuj punkty końcowe aplikacji, aby zweryfikować funkcjonalność aplikacji.

2. Możesz również sprawdzić stan aplikacji, sprawdzając stan aplikacji[(az mesh app show](/cli/azure/ext/mesh/mesh/app#ext-mesh-az-mesh-app-show)) i przeglądając dzienniki aplikacji oraz polecenia[(az mesh code-package-log)](/cli/azure/ext/mesh/mesh/code-package-log)przy użyciu interfejsu wiersza [Azure Service Fabric Mesh wiersza polecenia](./service-fabric-mesh-quickstart-deploy-container.md#set-up-service-fabric-mesh-cli).

## <a name="commit"></a>Zatwierdzenie

Po potwierdzeniu równoważnych funkcji aplikacji Service Fabric Mesh w regionie docelowym skonfiguruj kontroler ruchu wychodzącego (na przykład [Application Gateway](../application-gateway/redirect-overview.md)) w celu przekierowania ruchu do nowej aplikacji.

## <a name="clean-up-source-resources"></a>Czyszczenie zasobów źródłowych

Aby ukończyć przenoszenie aplikacji Service Fabric Mesh, usuń aplikację źródłową [i/lub nadrzędną grupę zasobów.](../azure-resource-manager/management/delete-resource-group.md)

## <a name="next-steps"></a>Następne kroki

* [Przenoszenie zasobów platformy Azure między regionami](../azure-resource-manager/management/move-resources-overview.md#move-resources-across-regions)
* [Obsługa przenoszenia zasobów platformy Azure między regionami](../azure-resource-manager/management/move-support-resources.md)
* [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Obsługa operacji przenoszenia dla zasobów](../azure-resource-manager/management/move-support-resources.md
)
