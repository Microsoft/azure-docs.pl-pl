---
title: Samouczek — Oczyszczanie zasobów siatki usługi Azure Service Fabric
description: Dowiedz się, jak usunąć zasoby usługi Azure Service Fabric Mesh, aby uniknąć naliczania opłat za nieużywane już zasoby.
author: georgewallace
ms.topic: tutorial
ms.date: 09/18/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: 1d594001340f76c6dde9f3e00de2d76ee4e5104b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843332"
---
# <a name="tutorial-remove-azure-resources"></a>Samouczek: usuwanie zasobów platformy Azure

Ten samouczek jest piątą częścią serii. Pokazano w nim, jak usunąć aplikację i jej zasoby, aby uniknąć naliczania za nie opłat.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Czyszczenie zasobów używanych przez aplikację w celu uniknięcia naliczania za nie opłat.

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * [Tworzenie aplikacji usługi Service Fabric Mesh w programie Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Debugowanie aplikacji usługi Service Fabric Mesh działającej w lokalnym klastrze projektowym](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Wdrażanie aplikacji usługi Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Uaktualnianie aplikacji usługi Service Fabric Mesh](service-fabric-mesh-tutorial-upgrade.md)
> * Czyszczenie zasobów usługi Service Fabric Mesh

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Jeśli nie została wdrożona aplikacja z listą zadań do wykonania, postępuj zgodnie z instrukcjami zamieszczonymi w artykule [Publikowanie aplikacji internetowej usługi Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

To jest koniec samouczka. Jeśli utworzone zasoby nie są już Ci potrzebne, usuń je, aby uniknąć naliczania opłat za nieużywane już zasoby. Jest to szczególnie ważne, ponieważ Mesh jest usługą bezserwerową z sekundowym naliczaniem opłat. Aby dowiedzieć się więcej na temat cen usługi Mesh, odwiedź adres https://aka.ms/sfmeshpricing.

Jedno z udogodnień zapewnianych przez platformę Azure polega na tym, że po utworzeniu zasobów, które są skojarzone z określoną grupą zasobów, usunięcie tej grupy zasobów powoduje usunięcie wszystkich skojarzonych zasobów. Dzięki temu nie trzeba ich usuwać pojedynczo.

Ponieważ nowa grupa zasobów została utworzona w celu utworzenia aplikacji z listą zadań do wykonania, można bezpiecznie usunąć tę grupę zasobów. Spowoduje to usunięcie wszystkich skojarzonych zasobów.

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

Alternatywnie możesz usunąć grupę zasobów **sfmeshTutorial1RG**[z poziomu portalu](../azure-resource-manager/management/manage-resource-groups-portal.md#delete-resource-groups). 

## <a name="next-steps"></a>Następne kroki

Teraz, po zakończeniu publikowania aplikacji usługi Service Fabric Mesh na platformie Azure, spróbuj wykonać następujące czynności:

* Aby zobaczyć inny przykład komunikacji między usługami, zapoznaj się z [przykładową aplikacją do głosowania](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp).
* Aby dowiedzieć się więcej o modelu zasobów usługi Service Fabric, zobacz [Model zasobów usługi Service Fabric Mesh](service-fabric-mesh-service-fabric-resources.md).
* Aby dowiedzieć się więcej na temat usługi Service Fabric Mesh, przeczytaj [omówienie usługi Service Fabric Mesh](service-fabric-mesh-overview.md).
* Przeczytaj więcej o usłudze [Cloud Shell](../cloud-shell/overview.md)
