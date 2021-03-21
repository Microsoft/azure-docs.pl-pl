---
title: Wdrożenie usługi zip push dla Azure Functions
description: Aby opublikować Azure Functions, użyj funkcji wdrożenia pliku zip usługi wdrażania kudu.
ms.topic: conceptual
ms.date: 08/12/2018
ms.openlocfilehash: e104661dcdf1f6c6fd6dd5eb1024748980e7931f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018449"
---
# <a name="zip-deployment-for-azure-functions"></a>Wdrażanie pliku zip dla usługi Azure Functions

W tym artykule opisano sposób wdrażania plików projektu aplikacji funkcji na platformie Azure z pliku zip (skompresowanego). Dowiesz się, jak przeprowadzić wdrożenie wypychane, zarówno przy użyciu interfejsu wiersza polecenia platformy Azure, jak i za pomocą interfejsów API REST. [Azure Functions Core Tools](functions-run-local.md) używa również tych interfejsów API wdrażania podczas publikowania projektu lokalnego na platformie Azure.

Azure Functions ma pełny zakres opcji ciągłego wdrażania i integracji zapewnianych przez Azure App Service. Aby uzyskać więcej informacji, zobacz [wdrażanie ciągłe dla Azure Functions](functions-continuous-deployment.md).

Aby przyspieszyć programowanie, można łatwiej wdrożyć pliki projektu aplikacji funkcji bezpośrednio z pliku zip. Interfejs API wdrażania. zip pobiera zawartość pliku zip i wyodrębnia zawartość do `wwwroot` folderu aplikacji funkcji. To wdrożenie pliku zip używa tej samej usługi kudu, która zapewnia ciągłe wdrażanie oparte na integracji, w tym:

+ Usuwanie plików, które zostały pozostawione z wcześniejszych wdrożeń.
+ Dostosowanie wdrożenia, w tym uruchamianie skryptów wdrażania.
+ Dzienniki wdrożenia.
+ Synchronizowanie wyzwalaczy funkcji w aplikacji funkcji [planu zużycia](functions-scale.md) .

Aby uzyskać więcej informacji, zobacz [Informacje o wdrożeniu. zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

## <a name="deployment-zip-file-requirements"></a>Wymagania dotyczące pliku Deployment. zip

Plik. zip, który jest używany do wdrażania wypychanego musi zawierać wszystkie pliki potrzebne do uruchomienia funkcji.

>[!IMPORTANT]
> W przypadku użycia pliku. zip, wszystkie pliki z istniejącego wdrożenia, które nie zostały znalezione w plik zip, są usuwane z aplikacji funkcji.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Aplikacja funkcji zawiera wszystkie pliki i foldery znajdujące się w `wwwroot` katalogu. Wdrożenie pliku. zip obejmuje zawartość `wwwroot` katalogu, ale nie sam katalog. Podczas wdrażania projektu biblioteki klas C# należy uwzględnić skompilowane pliki biblioteki i zależności w `bin` podfolderze pakietu. zip.

## <a name="download-your-function-app-files"></a>Pobieranie plików aplikacji funkcji

Podczas tworzenia na komputerze lokalnym, można łatwo utworzyć plik. zip folderu projektu aplikacji funkcji na komputerze deweloperskim.

Można jednak utworzyć funkcje przy użyciu edytora w Azure Portal. Istniejący projekt aplikacji funkcji można pobrać w jeden z następujących sposobów:

+ **Z Azure Portal:**

  1. Zaloguj się do [Azure Portal](https://portal.azure.com), a następnie przejdź do swojej aplikacji funkcji.

  2. Na karcie **Przegląd** wybierz pozycję **Pobierz zawartość aplikacji**. Wybierz opcje pobierania, a następnie wybierz pozycję **Pobierz**.

      ![Pobieranie projektu aplikacji funkcji](./media/deployment-zip-push/download-project.png)

     Pobrany plik zip ma prawidłowy format do ponownego opublikowania w aplikacji funkcji przy użyciu polecenia. zip push Deployment. Pobranie portalu umożliwia również dodanie plików potrzebnych do otwarcia aplikacji funkcji bezpośrednio w programie Visual Studio.

+ **Korzystanie z interfejsów API REST:**

    Aby pobrać pliki z projektu, należy użyć następującego interfejsu GET API `<function_app>` : 

    ```http
    https://<function_app>.scm.azurewebsites.net/api/zip/site/wwwroot/
    ```

    W tym celu `/site/wwwroot/` upewnij się, że plik zip zawiera tylko pliki projektu aplikacji funkcji, a nie całą lokację. Jeśli użytkownik nie jest jeszcze zalogowany na platformie Azure, zostanie poproszony o to.  

Możesz również pobrać plik zip z repozytorium GitHub. Pobranie repozytorium GitHub jako pliku zip spowoduje dodanie dodatkowego poziomu folderów dla gałęzi. Ten dodatkowy poziom folderów oznacza, że nie można wdrożyć pliku zip bezpośrednio po pobraniu go z usługi GitHub. Jeśli używasz repozytorium GitHub do obsługi aplikacji funkcji, użyj [ciągłej integracji](functions-continuous-deployment.md) w celu wdrożenia aplikacji.  

## <a name="deploy-by-using-azure-cli"></a><a name="cli"></a>Wdrażanie przy użyciu interfejsu wiersza polecenia platformy Azure

Aby wyzwolić wdrożenie wypychane, można użyć interfejsu wiersza polecenia platformy Azure. Wypychanie Wdróż plik. zip w aplikacji funkcji przy użyciu polecenia [AZ functionapp Deployment Source config-zip](/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config-zip) . Aby użyć tego polecenia, musisz użyć interfejsu wiersza polecenia platformy Azure w wersji 2.0.21 lub nowszej. Aby sprawdzić, jaka wersja interfejsu wiersza polecenia platformy Azure jest używana, użyj `az --version` polecenia.

W poniższym poleceniu Zastąp `<zip_file_path>` symbol zastępczy ścieżką do lokalizacji pliku. zip. Ponadto Zastąp `<app_name>` wartość unikatową nazwą aplikacji funkcji i Zamień na `<resource_group>` nazwę grupy zasobów.

```azurecli-interactive
az functionapp deployment source config-zip -g <resource_group> -n \
<app_name> --src <zip_file_path>
```

To polecenie wdraża pliki projektu z pobranego pliku zip w aplikacji funkcji na platformie Azure. Następnie ponownie uruchamia aplikację. Aby wyświetlić listę wdrożeń dla tej aplikacji funkcji, należy użyć interfejsów API REST.

Gdy korzystasz z interfejsu wiersza polecenia platformy Azure na komputerze lokalnym, `<zip_file_path>` to ścieżka do pliku zip na komputerze. Interfejs wiersza polecenia platformy Azure można również uruchomić w [Azure Cloud Shell](../cloud-shell/overview.md). W przypadku korzystania z Cloud Shell należy najpierw przekazać plik Deployment. zip do konta Azure Files skojarzonego z Twoim Cloud Shell. W takim przypadku `<zip_file_path>` jest lokalizacją przechowywania używaną przez konto Cloud Shell. Aby uzyskać więcej informacji, zobacz [utrwalanie plików w Azure Cloud Shell](../cloud-shell/persisting-shell-storage.md).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

## <a name="run-functions-from-the-deployment-package"></a>Uruchamianie funkcji z pakietu wdrożeniowego

Możesz również uruchomić funkcje bezpośrednio z pliku pakietu wdrożeniowego. Ta metoda pomija krok wdrożenia kopiowania plików z pakietu do `wwwroot` katalogu aplikacji funkcji. Zamiast tego plik pakietu jest instalowany przez środowisko uruchomieniowe funkcji, a zawartość `wwwroot` katalogu staje się tylko do odczytu.  

Wdrożenie zip integruje się z tą funkcją, którą można włączyć przez ustawienie ustawienia aplikacji funkcji `WEBSITE_RUN_FROM_PACKAGE` na wartość `1` . Aby uzyskać więcej informacji, zobacz [Uruchamianie funkcji z pliku pakietu wdrożeniowego](run-functions-from-deployment-package.md).

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Ciągłe wdrażanie dla Azure Functions](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
