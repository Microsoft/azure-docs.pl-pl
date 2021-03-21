---
title: Samouczek — Wdrażanie aplikacji usługi Service Fabric Mesh
description: Dowiedz się, jak używać programu Visual Studio do publikowania aplikacji usługi Azure Service Fabric Mesh składającej się z witryny internetowej platformy ASP.NET Core, która komunikuje się z usługą internetową zaplecza.
author: georgewallace
ms.topic: tutorial
ms.date: 09/18/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: 713f8dcb3d3b3d30fecbea4bb6b50cc4e47d451d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102216755"
---
# <a name="tutorial-deploy-a-service-fabric-mesh-application"></a>Samouczek: wdrażanie aplikacji usługi Service Fabric Mesh

> [!IMPORTANT]
> Wersja zapoznawcza siatki Service Fabric platformy Azure została wycofana. Nowe wdrożenia nie będą już dozwolone za pomocą interfejsu API Service Fabric siatki. Obsługa istniejących wdrożeń będzie kontynuowana do 28 kwietnia 2021.
> 
> Aby uzyskać szczegółowe informacje, zobacz wycofywanie w [wersji zapoznawczej usługi Azure Service Fabric siatki](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Ten samouczek to trzecia część serii. Przedstawiono w nim sposób publikowania internetowej aplikacji usługi Azure Service Fabric Mesh bezpośrednio z poziomu programu Visual Studio.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Opublikuj aplikację na platformie Azure przy użyciu programu Visual Studio.
> * Sprawdzanie stanu wdrożenia aplikacji
> * Wyświetlanie wszystkich aplikacji aktualnie wdrożonych w subskrypcji

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * [Tworzenie aplikacji usługi Service Fabric Mesh w programie Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Debugowanie aplikacji usługi Service Fabric Mesh działającej w lokalnym klastrze projektowym](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * Wdrażanie aplikacji usługi Service Fabric Mesh
> * [Uaktualnianie aplikacji usługi Service Fabric Mesh](service-fabric-mesh-tutorial-upgrade.md)
> * [Czyszczenie zasobów usługi Service Fabric Mesh](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem możesz [utworzyć bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Upewnij się, że masz [skonfigurowane środowisko projektowe](service-fabric-mesh-howto-setup-developer-environment-sdk.md) z zainstalowanym środowiskiem uruchomieniowym usługi Service Fabric, zestawem SDK, platformą Docker i programem Visual Studio 2017.

## <a name="download-the-to-do-sample-application"></a>Pobieranie przykładowej aplikacji z zadaniami do wykonania

Jeśli nie skompilowano przykładowej aplikacji z zadaniami do wykonania w [drugiej części tej serii samouczków](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md), można ją pobrać. W oknie polecenia uruchom następujące polecenie, aby sklonować przykładowe repozytorium aplikacji na komputer lokalny.

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

Aplikacja znajduje się w katalogu `src\todolistapp`.

## <a name="publish-to-azure"></a>Publikowanie na platformie Azure

Aby opublikować projekt usługi Service Fabric Mesh na platformie Azure, kliknij prawym przyciskiem myszy pozycję **todolistapp** w programie Visual Studio i wybierz pozycję **Opublikuj...**

Następnie zobaczysz okno dialogowe **Publikowanie aplikacji usługi Service Fabric**.

![Okno dialogowe publikowania usługi Service Fabric Mesh w programie Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Wybierz konto i subskrypcję platformy Azure. Wybierz **lokalizację**. W tym artykule jest używany region **Wschodnie stany USA**.

W obszarze **Grupa zasobów** wybierz pozycję **\<Create New Resource Group...>** . Zostanie wyświetlone okno dialogowe umożliwiające utworzenie nowej grupy zasobów. W tym artykule jest używana lokalizacja **Wschodnie stany USA** i nazwa grupy **sfmeshTutorial1RG** (jeśli w Twojej organizacji wiele osób korzysta z tej samej subskrypcji, wybierz unikatową nazwę grupy).  Naciśnij pozycję **Utwórz**, aby utworzyć grupę zasobów i wrócić do okna dialogowego publikowania.

![Okno dialogowe nowej grupy zasobów usługi Service Fabric Mesh w programie Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-resource-group-dialog.png)

Wróć do okna dialogowego **publikowanie aplikacji Service Fabric** w obszarze **Azure Container Registry** wybierz pozycję **\<Create New Container Registry...>** . W oknie dialogowym **Tworzenie rejestru kontenerów** użyj unikatowej nazwy w polu **Nazwa rejestru kontenerów**. Określ **lokalizację** (w tym samouczku jest używany region **Wschodnie stany USA**). Z listy rozwijanej wybierz **grupę zasobów** utworzoną w poprzednim kroku, na przykład **sfmeshTutorial1RG**. Ustaw pozycję **Jednostka SKU** na wartość **Podstawowa**, a następnie wybierz pozycję **Utwórz**, aby utworzyć prywatny rejestr kontenerów platformy Azure i wrócić do okna dialogowego publikowania.

![Okno dialogowe nowego rejestru kontenerów usługi Service Fabric Mesh w programie Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-container-registry-dialog.png)

Jeśli zostanie wyświetlony błąd wskazujący, że dostawca zasobów nie został zarejestrowany, możesz go zarejestrować. Najpierw sprawdź, czy dostawca zasobów jest dostępny w przypadku Twojej subskrypcji:

```Powershell
Get-AzureRmResourceProvider -ListAvailable
```

Jeśli dostawca rejestru kontenerów (`Microsoft.ContainerRegistry`) jest dostępny, zarejestruj go z poziomu programu PowerShell:

```Powershell
Connect-AzureRmAccount
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ContainerRegistry
```

W oknie dialogowym publikowania naciśnij przycisk **Opublikuj**, aby wdrożyć aplikację usługi Service Fabric na platformie Azure.

W przypadku publikowania na platformie Azure po raz pierwszy obraz platformy docker zostanie wypchnięty do usługi Azure Container Registry (ACR). Czas wykonywania tej czynności zależy od rozmiaru obrazu. Kolejne operacje publikowania tego samego projektu będą wykonywane szybciej. Aby monitorować postęp wdrażania, możesz wybrać okienko **Narzędzia usługi Service Fabric** w oknie **Dane wyjściowe** programu Visual Studio. Po zakończeniu wdrożenia w obszarze danych wyjściowych **Narzędzia usługi Service Fabric** adres IP i port aplikacji zostaną wyświetlone w postaci adresu URL.

```
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMeshApp\ToDoService\bin\Any CPU\Release\netcoreapp2.0\ToDoService.dll
Uploading the images to Azure Container Registry...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Otwórz przeglądarkę internetową i przejdź do adresu URL, aby wyświetlić witrynę internetową działającą na platformie Azure.

## <a name="set-up-service-fabric-mesh-cli"></a>Konfigurowanie interfejsu wiersza polecenia usługi Service Fabric Mesh

Podczas wykonywania pozostałych kroków możesz użyć usługi Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure. Zainstaluj moduł rozszerzenia interfejsu wiersza polecenia usługi Azure Service Fabric Mesh, korzystając z tych [instrukcji](service-fabric-mesh-howto-setup-cli.md).

## <a name="check-application-deployment-status"></a>Sprawdzanie stanu wdrożenia aplikacji

Na tym etapie Twoja aplikacja została wdrożona. Jej stan możesz sprawdzić za pomocą polecenia `app show`. 

Nazwa aplikacji w samouczku to `todolistapp`. Zbierz szczegółowe informacje dotyczące aplikacji za pomocą następującego polecenia:

```azurecli-interactive
az mesh app show --resource-group $rg --name todolistapp
```

## <a name="get-the-ip-address-of-your-deployment"></a>Uzyskiwanie adresu IP wdrożenia

Jeśli chcesz uzyskać adres IP dla swojej aplikacji, użyj następującego polecenia:
  
```azurecli-interactive
az mesh gateway show --resource-group myResourceGroup --name todolistappGateway
```

## <a name="see-all-applications-currently-deployed-to-your-subscription"></a>Wyświetlanie wszystkich aplikacji aktualnie wdrożonych w subskrypcji

Możesz użyć polecenia „app list” w celu uzyskania listy aplikacji wdrożonych w subskrypcji.

```azurecli-interactive
az mesh app list --output table
```

## <a name="next-steps"></a>Następne kroki

W tej części samouczka zawarto informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Publikowanie aplikacji na platformie Azure
> * Sprawdzanie stanu wdrożenia aplikacji
> * Wyświetlanie wszystkich aplikacji, które zostały aktualnie wdrożone w subskrypcji

Przejdź do następnego samouczka:
> [!div class="nextstepaction"]
> [Uaktualnianie aplikacji usługi Service Fabric Mesh](service-fabric-mesh-tutorial-upgrade.md)

[azure-cli-install]: /cli/azure/install-azure-cli
