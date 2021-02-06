---
title: Przenoszenie aplikacji siatki Service Fabric do innego regionu
description: Zasoby siatki Service Fabric można przenosić, wdrażając kopię bieżącego szablonu w nowym regionie platformy Azure.
author: erikadoyle
ms.author: edoyle
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 1b59d482b8b88e37da2d61636ff3f254a46ba5c2
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626091"
---
# <a name="move-a-service-fabric-mesh-application-to-another-azure-region"></a>Przenoszenie aplikacji siatki Service Fabric do innego regionu platformy Azure

> [!IMPORTANT]
> Wersja zapoznawcza siatki Service Fabric platformy Azure została wycofana. Nowe wdrożenia nie będą już dozwolone za pomocą interfejsu API Service Fabric siatki. Obsługa istniejących wdrożeń będzie kontynuowana do 28 kwietnia 2021.
> 
> Aby uzyskać szczegółowe informacje, zobacz wycofywanie w [wersji zapoznawczej usługi Azure Service Fabric siatki](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

W tym artykule opisano sposób przenoszenia aplikacji siatki Service Fabric i jej zasobów do innego regionu platformy Azure. Możesz przenieść zasoby do innego regionu z kilku powodów. Na przykład w odpowiedzi na awarię, aby uzyskać funkcje lub usługi dostępne tylko w określonych regionach, spełniać wewnętrzne wymagania dotyczące zasad i zarządzania lub w odpowiedzi na wymagania dotyczące planowania pojemności.

 [Siatka Service Fabric nie obsługuje](../azure-resource-manager/management/region-move-support.md#microsoftservicefabricmesh) bezpośredniego przenoszenia zasobów w różnych regionach platformy Azure. Można jednak przenosić zasoby pośrednio, wdrażając kopię bieżącego szablonu Azure Resource Manager w nowym regionie docelowym, a następnie przekierowując ruch przychodzący i zależności do nowo utworzonej aplikacji Service Fabric siatki.

## <a name="prerequisites"></a>Wymagania wstępne

* Kontroler transferu danych przychodzących (na przykład [Application Gateway](../application-gateway/index.yml)), który będzie używany jako pośrednik do routingu ruchu między klientami a aplikacją Service Fabric siatką
* Dostępność siatki Service Fabric (wersja zapoznawcza) w docelowym regionie platformy Azure ( `westus` , `eastus` lub `westeurope` )

## <a name="prepare"></a>Przygotowywanie

1. Utwórz "migawkę" bieżącego stanu aplikacji siatki Service Fabric, eksportując szablon Azure Resource Manager i parametry z najnowszego wdrożenia. Aby to zrobić, wykonaj kroki opisane w sekcji [Eksportowanie szablonu po wdrożeniu](../azure-resource-manager/templates/export-template-portal.md#export-template-after-deployment) przy użyciu Azure Portal. Można również użyć interfejsu [wiersza polecenia platformy Azure](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates), [Azure PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)lub [API REST](/rest/api/resources/resourcegroups/exporttemplate).

2. W razie potrzeby [wyeksportuj inne zasoby w tej samej grupie zasobów](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource-group) na potrzeby ponownego wdrożenia w regionie docelowym.

3. Przejrzyj (i Edytuj, w razie potrzeby) wyeksportowany szablon, aby upewnić się, że istniejące wartości właściwości są tymi, których chcesz użyć w regionie docelowym. Nowy `location` (region platformy Azure) to parametr, który będzie dostarczany podczas ponownego wdrażania.

## <a name="move"></a>Move

1. Utwórz nową grupę zasobów (lub Użyj istniejącej) w regionie docelowym.

2. W wyeksportowanym szablonie postępuj zgodnie z instrukcjami w temacie [wdrażanie zasobów z szablonu niestandardowego](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) przy użyciu Azure Portal. Można również użyć interfejsu [wiersza polecenia platformy Azure](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)lub [API REST](../azure-resource-manager/templates/deploy-rest.md).

3. Aby uzyskać wskazówki dotyczące przeniesienia powiązanych zasobów, takich jak [konta usługi Azure Storage](../storage/common/storage-account-move.md), zapoznaj się ze wskazówkami dotyczącymi poszczególnych usług wymienionych w temacie [przemieszczanie zasobów platformy Azure między regionami](../azure-resource-manager/management/move-region.md).

## <a name="verify"></a>Weryfikacja

1. Po zakończeniu wdrożenia Przetestuj punkty końcowe aplikacji, aby zweryfikować funkcjonalność aplikacji.

2. Możesz również sprawdzić stan aplikacji, sprawdzając stan aplikacji ([AZ siatk aplikacji show](/cli/azure/ext/mesh/mesh/app#ext-mesh-az-mesh-app-show)) i przeglądając Dzienniki aplikacji oraz ([AZ siatking-Package-log](/cli/azure/ext/mesh/mesh/code-package-log)) polecenia przy użyciu [interfejsu wiersza poleceń usługi Azure Service Fabric siatka](./service-fabric-mesh-quickstart-deploy-container.md#set-up-service-fabric-mesh-cli).

## <a name="commit"></a>Zatwierdzenie

Po potwierdzeniu równoważnej funkcjonalności aplikacji siatki Service Fabric w regionie docelowym Skonfiguruj kontroler transferu danych przychodzących (na przykład [Application Gateway](../application-gateway/redirect-overview.md)), aby przekierować ruch do nowej aplikacji.

## <a name="clean-up-source-resources"></a>Czyszczenie zasobów źródłowych

Aby zakończyć przenoszenie aplikacji siatki Service Fabric, [Usuń aplikację źródłową i/lub nadrzędną grupę zasobów](../azure-resource-manager/management/delete-resource-group.md).

## <a name="next-steps"></a>Następne kroki

* [Przenoszenie zasobów platformy Azure między regionami](../azure-resource-manager/management/move-region.md)
* [Obsługa przemieszczania zasobów platformy Azure między regionami](../azure-resource-manager/management/region-move-support.md)
* [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Obsługa operacji przenoszenia dla zasobów](../azure-resource-manager/management/move-support-resources.md
)
