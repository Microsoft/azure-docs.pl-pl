---
title: Wdrażanie wypychane pliku zip dla Azure Functions
description: Skorzystaj z funkcji wdrażania plików zip usługi wdrażania Kudu, aby opublikować Azure Functions.
ms.topic: conceptual
ms.date: 08/12/2018
ms.openlocfilehash: fb6867d7719f9650acb00f80ac3a933713ce0e23
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777655"
---
# <a name="zip-deployment-for-azure-functions"></a>Wdrażanie pliku zip dla usługi Azure Functions

W tym artykule opisano sposób wdrażania plików projektu aplikacji funkcji na platformie Azure z pliku zip (skompresowanego). Dowiesz się, jak wykonać wdrożenie wypychane, zarówno przy użyciu interfejsu wiersza polecenia platformy Azure, jak i interfejsów API REST. [Azure Functions Core Tools](functions-run-local.md) te interfejsy API wdrażania są również używane podczas publikowania projektu lokalnego na platformie Azure.

Azure Functions oferuje pełny zakres opcji ciągłego wdrażania i integracji, które są udostępniane przez Azure App Service. Aby uzyskać więcej informacji, zobacz [Continuous deployment for Azure Functions](functions-continuous-deployment.md).

Aby przyspieszyć opracowywanie, możesz łatwiej wdrożyć pliki projektu aplikacji funkcji bezpośrednio z pliku zip. Interfejs API wdrażania zip pobiera zawartość pliku zip i wyodrębnia zawartość do `wwwroot` folderu aplikacji funkcji. To wdrożenie pliku zip używa tej samej usługi Kudu, która jest oparta na ciągłej integracji, w tym:

+ Usunięcie plików, które zostały pozostawione we wcześniejszych wdrożeniach.
+ Dostosowywanie wdrożenia, w tym uruchamianie skryptów wdrażania.
+ Dzienniki wdrażania.
+ Wyzwalacze funkcji synchronizacji w aplikacji [funkcji planu](functions-scale.md) zużycia.

Aby uzyskać więcej informacji, zobacz zip deployment reference (Informacje dotyczące [wdrażania pliku ZIP).](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)

## <a name="deployment-zip-file-requirements"></a>Wymagania dotyczące pliku zip wdrożenia

Plik zip, który jest potrzebny do wdrożenia wypychania, musi zawierać wszystkie pliki potrzebne do uruchomienia funkcji.

>[!IMPORTANT]
> W przypadku korzystania z wdrożenia zip wszystkie pliki z istniejącego wdrożenia, które nie znajdują się w pliku zip, są usuwane z aplikacji funkcji.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Aplikacja funkcji zawiera wszystkie pliki i foldery w `wwwroot` katalogu. Wdrożenie pliku zip obejmuje zawartość `wwwroot` katalogu, ale nie sam katalog. Podczas wdrażania projektu biblioteki klas języka C# należy dołączyć skompilowane pliki biblioteki i zależności do `bin` podfolderu w pakiecie zip.

## <a name="download-your-function-app-files"></a>Pobieranie plików aplikacji funkcji

Podczas tworzenia aplikacji na komputerze lokalnym można łatwo utworzyć plik zip folderu projektu aplikacji funkcji na komputerze dewelopera.

Jednak funkcje mogły zostać utworzone przy użyciu edytora w Azure Portal. Istniejący projekt aplikacji funkcji można pobrać na jeden z tych sposobów:

+ **Z Azure Portal:**

  1. Zaloguj się do [Azure Portal](https://portal.azure.com), a następnie przejdź do aplikacji funkcji.

  2. Na karcie **Przegląd** wybierz pozycję **Pobierz zawartość aplikacji.** Wybierz opcje pobierania, a następnie wybierz pozycję **Pobierz.**

      ![Pobieranie projektu aplikacji funkcji](./media/deployment-zip-push/download-project.png)

     Pobrany plik zip ma prawidłowy format, który ma zostać ponownie opublikowany w aplikacji funkcji przy użyciu wdrożenia wypychania zip. Pobieranie portalu umożliwia również dodawanie plików potrzebnych do otwierania aplikacji funkcji bezpośrednio w Visual Studio.

+ **Korzystanie z interfejsów API REST:**

    Użyj następującego interfejsu API GET wdrożenia, aby pobrać pliki z `<function_app>` projektu: 

    ```http
    https://<function_app>.scm.azurewebsites.net/api/zip/site/wwwroot/
    ```

    Uwzględnianie zapewnia, że plik zip zawiera tylko pliki projektu `/site/wwwroot/` aplikacji funkcji, a nie całą witrynę. Jeśli jeszcze nie zalogowano się na platformie Azure, zostanie o to poproszony.  

Możesz również pobrać plik zip z repozytorium GitHub. Po pobraniu repozytorium GitHub jako pliku zip usługa GitHub dodaje dodatkowy poziom folderu dla gałęzi . Ten dodatkowy poziom folderu oznacza, że nie można wdrożyć pliku zip bezpośrednio podczas pobierania go z usługi GitHub. Jeśli do obsługi aplikacji funkcji używasz repozytorium GitHub, [](functions-continuous-deployment.md) w celu wdrożenia aplikacji należy użyć ciągłej integracji.  

## <a name="deploy-by-using-azure-cli"></a><a name="cli"></a>Wdrażanie przy użyciu interfejsu wiersza polecenia platformy Azure

Za pomocą interfejsu wiersza polecenia platformy Azure można wyzwolić wdrożenie wypychane. Wypchnij plik zip do aplikacji funkcji przy użyciu polecenia [az functionapp deployment source config-zip.](/cli/azure/functionapp/deployment/source#az_functionapp_deployment_source_config_zip) Aby użyć tego polecenia, musisz użyć interfejsu wiersza polecenia platformy Azure w wersji 2.0.21 lub nowszej. Aby sprawdzić używaną wersję interfejsu wiersza polecenia platformy Azure, użyj `az --version` polecenia .

W poniższym poleceniu zastąp symbol `<zip_file_path>` zastępczy ścieżką do lokalizacji pliku zip. Ponadto zastąp nazwą unikatową aplikacji funkcji i zastąp nazwą `<app_name>` `<resource_group>` grupy zasobów.

```azurecli-interactive
az functionapp deployment source config-zip -g <resource_group> -n \
<app_name> --src <zip_file_path>
```

To polecenie wdraża pliki projektu z pobranego pliku zip do aplikacji funkcji na platformie Azure. Następnie aplikacja zostanie ponownie uruchomiona. Aby wyświetlić listę wdrożeń dla tej aplikacji funkcji, należy użyć interfejsów API REST.

Jeśli używasz interfejsu wiersza polecenia platformy Azure na komputerze lokalnym, jest to ścieżka do pliku `<zip_file_path>` zip na komputerze. Interfejs wiersza polecenia platformy Azure można również uruchomić w [Azure Cloud Shell](../cloud-shell/overview.md). W przypadku Cloud Shell należy najpierw przekazać plik zip wdrożenia na konto Azure Files skojarzone z twoim Cloud Shell. W takim przypadku `<zip_file_path>` jest to lokalizacja magazynu używana przez Cloud Shell magazynu. Aby uzyskać więcej informacji, zobacz [Utrwalanie plików w Azure Cloud Shell](../cloud-shell/persisting-shell-storage.md).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

## <a name="run-functions-from-the-deployment-package"></a>Uruchamianie funkcji z pakietu wdrożeniowego

Możesz również uruchamiać funkcje bezpośrednio z pliku pakietu wdrożeniowego. Ta metoda pomija krok wdrażania kopiowania plików z pakietu do `wwwroot` katalogu aplikacji funkcji. Zamiast tego plik pakietu jest zainstalowany przez środowisko uruchomieniowe usługi Functions, a zawartość katalogu staje się tylko `wwwroot` do odczytu.  

Wdrożenie pliku zip integruje się z tą funkcją, którą można włączyć, ustawiając ustawienie aplikacji funkcji `WEBSITE_RUN_FROM_PACKAGE` na wartość `1` . Aby uzyskać więcej informacji, [zobacz Uruchamianie funkcji z pliku pakietu wdrożeniowego](run-functions-from-deployment-package.md).

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Ciągłe wdrażanie dla usługi Azure Functions](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
