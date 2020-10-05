---
title: Szybki Start — wdrażanie aplikacji sieci Web na platformie Azure Service Fabric siatką
description: W tym przewodniku Szybki start opisano, jak utworzyć witrynę internetową platformy ASP.NET Core i opublikować ją w usłudze Azure Service Fabric Mesh za pomocą programu Visual Studio.
author: dkkapur
ms.topic: quickstart
ms.date: 07/17/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 0828ad1acd23afdb7ebe7b89b20d8674e5876319
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91653302"
---
# <a name="quickstart-create-and-deploy-a-web-app-to-azure-service-fabric-mesh"></a>Szybki start: Tworzenie i wdrażanie aplikacji internetowych w usłudze Azure Service Fabric Mesh

Usługa Azure Service Fabric Mesh to w pełni zarządzana usługa, która pozwala deweloperom na wdrażanie aplikacji mikrousług bez zarządzania maszynami wirtualnymi, magazynem i siecią.

W tym przewodniku Szybki start opisano tworzenie nowej aplikacji Service Fabric Mesh, która składa się z aplikacji internetowej platformy ASP.NET Core, jej uruchomienia w lokalnym klastrze projektowym oraz opublikowania w celu uruchomienia na platformie Azure.

Konieczna jest subskrypcja platformy Azure. Jeśli jej nie masz, możesz łatwo utworzyć bezpłatną subskrypcję platformy Azure; przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/). Trzeba również [skonfigurować środowisko deweloperskie](service-fabric-mesh-howto-setup-developer-environment-sdk.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="create-a-service-fabric-mesh-project"></a>Tworzenie projektu usługi Service Fabric Mesh

Otwórz program Visual Studio i wybierz pozycję **plik**  >  **Nowy**  >  **projekt...**

W oknie dialogowym **Nowy projekt** w polu **Wyszukaj** u góry wpisz `mesh`. Wybierz szablon **Aplikacja Service Fabric Mesh**. Jeśli go nie widzisz, upewnij się, że zainstalowano zestaw SDK usługi Mesh i narzędzia VS w wersji zapoznawczej zgodnie z opisem w temacie [Konfigurowanie środowiska projektowego](service-fabric-mesh-howto-setup-developer-environment-sdk.md). 

W polu **Nazwa** wpisz **ServiceFabricMesh1**, a w polu **Lokalizacja** ustaw ścieżkę folderu, w którym będą przechowywane pliki projektu.

Upewnij się, że opcja **Utwórz katalog dla rozwiązania** jest zaznaczona, a następnie kliknij przycisk **OK**, aby utworzyć projekt usługi Service Fabric Mesh.

![Zrzut ekranu pokazujący sposób tworzenia projektu siatki Service Fabric.](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-project.png)

### <a name="create-a-service"></a>Tworzenie usługi

Po kliknięciu przycisku **OK** pojawi się okno dialogowe **Nowa usługa Service Fabric**. Wybierz typ projektu **ASP.NET Core** i upewnij się, że w obszarze **System operacyjny kontenera** wybrano opcję **Windows**, a następnie kliknij przycisk **OK**, aby utworzyć projekt ASP.NET Core. 

![Okno dialogowe nowego projektu usługi Service Fabric Mesh w programie Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-service-fabric-service.png)

Zostanie wyświetlone okno dialogowe **Nowa aplikacja internetowa platformy ASP.NET Core**. Wybierz pozycję **Aplikacja internetowa**, a następnie kliknij przycisk **OK**.

![Nowa aplikacja platformy ASP.NET Core w programie Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-aspnetcore-app.png)

Program Visual Studio tworzy projekt aplikacji Service Fabric Mesh i projekt platformy ASP.NET Core.

## <a name="build-and-publish-to-your-local-cluster"></a>Kompilowanie i publikowanie w klastrze lokalnym

Bezpośrednio po załadowaniu projektu obraz platformy Docker jest automatycznie kompilowany i publikowany w klastrze lokalnym. Ten proces może trochę potrwać. Jeśli chcesz monitorować postęp działania narzędzi Service Fabric w oknie **Dane wyjściowe**, wybierz element **Service Fabric Tools** w oknie rozwijanym **Dane wyjściowe**. Podczas wdrażania obrazu platformy Docker możesz kontynuować pracę.

Po utworzeniu projektu kliknij klawisz **F5**, aby debugować usługę lokalnie. Po zakończeniu lokalnego wdrożenia i uruchomieniu projektu w programie Visual Studio zostanie otwarte okno przeglądarki z wyświetloną przykładową stroną internetową.

Po zakończeniu przeglądania wdrożonej usługi zatrzymaj debugowanie projektu przez naciśnięcie kombinacji klawiszy **Shift + F5** w programie Visual Studio.

## <a name="publish-to-azure"></a>Publikowanie na platformie Azure

Aby opublikować projekt usługi Service Fabric Mesh na platformie Azure, kliknij prawym przyciskiem myszy pozycję **Projekt Service Fabric Mesh** w programie Visual Studio i wybierz pozycję **Opublikuj...**

![Kliknięcie prawym przyciskiem myszy projektu Service Fabric Mesh w programie Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-right-click-publish.png)

Zostanie wyświetlone okno dialogowe **Publikowanie aplikacji usługi Service Fabric**.

![Okno dialogowe publikowania usługi Service Fabric Mesh w programie Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-dialog.png)

Wybierz konto i subskrypcję platformy Azure. Wybierz **lokalizację**. W tym artykule jest używany region **Wschodnie stany USA**.

W obszarze **Grupa zasobów**wybierz pozycję **\<Create New Resource Group...>** . Zostanie wyświetlone okno dialogowe **Tworzenie grupy zasobów**. Ustaw opcje **Nazwa grupy zasobów** i **Lokalizacja**.  W tym przewodniku Szybki start jest używana lokalizacja **Wschodnie stany USA** i nazwa grupy **sfmeshTutorial1RG** (jeśli w Twojej organizacji wiele osób korzysta z tej samej subskrypcji, wybierz unikatową nazwę grupy zasobów).  Naciśnij pozycję **Utwórz**, aby utworzyć grupę zasobów i wrócić do okna dialogowego publikowania.

![Zrzut ekranu pokazujący sposób tworzenia nowej grupy zasobów.](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-new-resource-group-dialog.png)

Wróć do okna dialogowego **publikowanie aplikacji Service Fabric** w obszarze **Azure Container Registry**wybierz pozycję **\<Create New Container Registry...>** . W oknie dialogowym **Tworzenie rejestru kontenerów** użyj unikatowej nazwy w polu **Nazwa rejestru kontenerów**. Podaj **lokalizację** (w tym przewodniku Szybki start jest używany region **Wschodnie stany USA**). Z listy rozwijanej wybierz **grupę zasobów** utworzoną w poprzednim kroku, na przykład **sfmeshTutorial1RG**. Ustaw **jednostkę SKU** na **Podstawowa**, a następnie naciśnij przycisk **Utwórz**, aby wrócić do okna dialogowego publikowania.

![Okno dialogowe nowej grupy zasobów usługi Service Fabric Mesh w programie Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-new-container-registry-dialog.png)

W oknie dialogowym publikowania naciśnij przycisk **Opublikuj**, aby wdrożyć aplikację usługi Service Fabric Mesh na platformie Azure.

W przypadku publikowania na platformie Azure po raz pierwszy obraz platformy docker zostanie wypchnięty do usługi Azure Container Registry (ACR). Czas wykonywania tej czynności zależy od rozmiaru obrazu. Kolejne operacje publikowania tego samego projektu będą wykonywane szybciej. Aby monitorować postęp wdrażania, możesz wybrać **Narzędzia usługi Service Fabric** w oknie rozwijanym **Dane wyjściowe** programu Visual Studio. Po zakończeniu wdrożenia w obszarze danych wyjściowych **Narzędzia usługi Service Fabric** adres IP i port aplikacji zostaną wyświetlone w postaci adresu URL.

```
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMesh1\Web1\bin\Any CPU\Release\netcoreapp2.0\Web1.dll
Uploading the images to Azure Container Registry...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://...
```

Otwórz przeglądarkę internetową i przejdź do adresu URL, aby wyświetlić witrynę internetową działającą na platformie Azure:

![Uruchamianie aplikacji internetowej usługi Service Fabric Mesh](media/service-fabric-mesh-tutorial-deploy-dotnetcore/deployed-web-project.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli już nie potrzebujesz żadnych zasobów utworzonych na potrzeby tego przewodnika Szybki start, usuń je. Ponieważ utworzono nową grupę zasobów w celu hostowania zasobów usług ACR i Service Fabric Mesh, można bezpiecznie usunąć tę grupę zasobów. Jest to prosty sposób na usunięcie wszystkich skojarzonych z nią zasobów.

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Connect-AzureRmAccount
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

Alternatywnie możesz usunąć grupę zasobów [z poziomu witryny Azure Portal](https://portal.azure.com).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat tworzenia i wdrażania aplikacji usługi Service Fabric Mesh, przejdź do samouczka.
> [!div class="nextstepaction"]
> [Tworzenie, debugowanie i wdrażanie aplikacji internetowej usługi Service Fabric Mesh dla wielu usług](service-fabric-mesh-tutorial-create-dotnetcore.md)
