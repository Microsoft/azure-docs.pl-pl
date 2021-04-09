---
title: Technologie wdrażania w Azure Functions
description: Poznaj różne sposoby wdrażania kodu do Azure Functions.
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: 4a65a00c28a20c9381d3dcc6fd7545137528d5c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943630"
---
# <a name="deployment-technologies-in-azure-functions"></a>Technologie wdrażania w Azure Functions

Do wdrożenia kodu projektu Azure Functions na platformie Azure można użyć kilku różnych technologii. Ten artykuł zawiera omówienie dostępnych metod wdrażania i zaleceń dotyczących najlepszej metody do użycia w różnych scenariuszach. Zawiera również wyczerpującą listę najważniejszych informacji o podstawowych technologiach wdrażania. 

## <a name="deployment-methods"></a>Metody wdrażania

Technologia wdrażania używana do publikowania kodu na platformie Azure jest ogólnie określana w sposób, w jaki publikujesz aplikację. Odpowiednia metoda wdrażania jest określana zgodnie z konkretnymi potrzebami i punktem w cyklu programowania. Na przykład podczas tworzenia i testowania można wdrożyć program bezpośrednio z poziomu narzędzia programistycznego, takiego jak Visual Studio Code. Gdy aplikacja jest w środowisku produkcyjnym, najprawdopodobniej możesz publikować ją w sposób ciągły z kontroli źródła lub za pomocą zautomatyzowanego potoku publikowania, który obejmuje dodatkowe sprawdzanie poprawności i testowanie.  

W poniższej tabeli opisano dostępne metody wdrażania dla projektu funkcji.

| &nbsp;Typ wdrożenia | Metody | Najlepsze dla... |
| -- | -- | -- |
| Oparte na narzędziach | &bull;&nbsp;[&nbsp; &nbsp; Publikowanie kodu w programie Visual Studio &nbsp;](functions-develop-vs-code.md#publish-to-azure)<br/>&bull;&nbsp;[Publikowanie w programie Visual Studio](functions-develop-vs.md#publish-to-azure)<br/>&bull;&nbsp;[Publikowanie podstawowych narzędzi](functions-run-local.md#publish) | Wdrożenia podczas opracowywania i inne wdrożenia usługi AD-Hock. Wdrożenia są zarządzane lokalnie przez narzędzia. | 
| App Service zarządzane| &bull;&nbsp;[&nbsp;Centrum wdrażania &nbsp; (Ci/CD)](functions-continuous-deployment.md)<br/>&bull;&nbsp;[Wdrożenia kontenerów &nbsp;](functions-create-function-linux-custom-image.md#enable-continuous-deployment-to-azure) |  Ciągłe wdrażanie (CI/CD) z kontroli źródła lub z rejestru kontenerów. Wdrożenia są zarządzane przez platformę App Service (kudu).|
| Potoki zewnętrzne|&bull;&nbsp;[Azure Pipelines](functions-how-to-azure-devops.md)<br/>&bull;&nbsp;[Akcje GitHub](functions-how-to-github-actions.md) | Potoki produkcyjne i DevOps, które obejmują dodatkowe sprawdzanie poprawności, testowanie i inne akcje, są uruchamiane w ramach zautomatyzowanego wdrożenia. Wdrożenia są zarządzane przez potok. |

Podczas gdy określone funkcje wdrożenia korzystają z najlepszej technologii na podstawie ich kontekstu, większość metod wdrażania jest oparta na [rozmieszczeniu zip](#zip-deploy).

## <a name="deployment-technology-availability"></a>Dostępność technologii wdrażania

Azure Functions obsługuje Międzyplatformowe programowanie lokalne i hosting w systemach Windows i Linux. Obecnie dostępne są trzy plany hostingu:

+ [Zużycie](consumption-plan.md)
+ [Premium](functions-premium-plan.md)
+ [Dedykowane (App Service)](dedicated-plan.md)

Każdy plan ma inne zachowania. Nie wszystkie technologie wdrażania są dostępne dla każdej wersji Azure Functions. Na poniższym wykresie przedstawiono, które technologie wdrażania są obsługiwane dla każdej kombinacji systemu operacyjnego i planu hostingu:

| Technologia wdrażania | Użycie systemu Windows | Windows Premium | Dedykowane systemu Windows  | Użycie systemu Linux | System Linux Premium | System Linux — dedykowany |
|-----------------------|:-------------------:|:-------------------------:|:------------------:|:---------------------------:|:-------------:|:---------------:|
| Zewnętrzny adres URL pakietu<sup>1</sup> |✔|✔|✔|✔|✔|✔|
| Wdróż plik zip |✔|✔|✔|✔|✔|✔|
| Kontener platformy Docker | | | | |✔|✔|
| Web Deploy |✔|✔|✔| | | |
| Kontrola źródła |✔|✔|✔| |✔|✔|
| Lokalne git<sup>1</sup> |✔|✔|✔| |✔|✔|
| Synchronizacja w chmurze<sup>1</sup> |✔|✔|✔| |✔|✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|✔|
| Edytowanie portalu |✔|✔|✔| |✔<sup>2</sup>|✔<sup>2</sup>|

<sup>1</sup> technologia wdrażania, która wymaga [synchronizacji ręcznej wyzwalacza](#trigger-syncing).
<sup>2</sup> edytowanie w portalu jest włączone tylko dla wyzwalaczy http i Timer dla funkcji w systemie Linux przy użyciu planów Premium i dedykowanych.

## <a name="key-concepts"></a>Kluczowe pojęcia

Niektóre kluczowe koncepcje mają na celu zrozumienie, w jaki sposób wdrożenia działają w Azure Functions.

### <a name="trigger-syncing"></a>Wyzwalanie synchronizacji

Po zmianie któregokolwiek z wyzwalaczy infrastruktura funkcji musi pamiętać o zmianach. Synchronizacja odbywa się automatycznie w przypadku wielu technologii wdrażania. Jednak w niektórych przypadkach należy ręcznie zsynchronizować wyzwalacze. Podczas wdrażania aktualizacji przez odwołanie się do zewnętrznego adresu URL pakietu, lokalnego narzędzia Git, synchronizacji w chmurze lub FTP należy ręcznie zsynchronizować wyzwalacze. Wyzwalacze można synchronizować na jeden z trzech sposobów:

* Uruchom ponownie aplikację funkcji w Azure Portal
* Wyślij żądanie HTTP POST, aby `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` użyć [klucza głównego](functions-bindings-http-webhook-trigger.md#authorization-keys).
* Wyślij żądanie HTTP POST do `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01` . Zamień symbole zastępcze na identyfikator subskrypcji, nazwę grupy zasobów i nazwę aplikacji funkcji.

### <a name="remote-build"></a>Kompilacja zdalna

Azure Functions może automatycznie wykonywać kompilacje na kodzie, który odbiera po wdrożeniach zip. Te kompilacje działają nieco inaczej w zależności od tego, czy aplikacja działa w systemie Windows, czy Linux. Kompilacje zdalne nie są wykonywane, gdy aplikacja została wcześniej ustawiona do uruchamiania w trybie [uruchamiania z pakietu](run-functions-from-deployment-package.md) . Aby dowiedzieć się, jak korzystać z kompilacji zdalnej, przejdź do pliku [zip Deploy](#zip-deploy).

> [!NOTE]
> Jeśli masz problemy z kompilacją zdalną, może to być spowodowane tym, że aplikacja została utworzona przed udostępnieniem funkcji (1 sierpnia 2019). Spróbuj utworzyć nową aplikację funkcji lub uruchomić `az functionapp update -g <RESOURCE_GROUP_NAME> -n <APP_NAME>` ją w celu zaktualizowania aplikacji funkcji. Wykonanie tego polecenia może potrwać dwie próby.

#### <a name="remote-build-on-windows"></a>Kompilacja zdalna w systemie Windows

Wszystkie aplikacje funkcji działające w systemie Windows mają małą aplikację do zarządzania, witrynę SCM (lub [kudu](https://github.com/projectkudu/kudu)). Ta lokacja obsługuje wiele wdrożeń i logikę kompilacji dla Azure Functions.

Gdy aplikacja jest wdrażana w systemie Windows, uruchamiane są polecenia specyficzne dla języka `dotnet restore` (np. C#) lub `npm install` (JavaScript).

#### <a name="remote-build-on-linux"></a>Kompilacja zdalna w systemie Linux

Aby włączyć kompilację zdalną w systemie Linux, należy ustawić następujące [Ustawienia aplikacji](functions-how-to-use-azure-function-app-settings.md#settings) :

* `ENABLE_ORYX_BUILD=true`
* `SCM_DO_BUILD_DURING_DEPLOYMENT=true`

Domyślnie zarówno [Azure Functions Core Tools](functions-run-local.md) , jak i [rozszerzenie Azure Functions dla Visual Studio Code](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure) wykonują kompilacje zdalne podczas wdrażania w systemie Linux. W związku z tym oba narzędzia automatycznie tworzą te ustawienia na platformie Azure.

Gdy aplikacje są kompilowane zdalnie w systemie Linux, są one [uruchamiane z pakietu wdrożeniowego](run-functions-from-deployment-package.md).

##### <a name="consumption-plan"></a>Plan Zużycie

Aplikacje funkcji systemu Linux działające w ramach planu zużycia nie mają witryny SCM/kudu, która ogranicza opcje wdrażania. Jednak aplikacje funkcji w systemie Linux działające w ramach planu zużycia obsługują kompilacje zdalne.

##### <a name="dedicated-and-premium-plans"></a>Plany dedykowane i Premium

Aplikacje funkcji działające w systemie Linux w ramach [dedykowanego planu (App Service)](dedicated-plan.md) i [planu Premium](functions-premium-plan.md) mają również ograniczoną witrynę SCM/kudu.

## <a name="deployment-technology-details"></a>Szczegóły technologii wdrażania

W Azure Functions są dostępne następujące metody wdrażania.

### <a name="external-package-url"></a>Zewnętrzny adres URL pakietu

Możesz użyć zewnętrznego adresu URL pakietu, aby odwołać się do pliku pakietu zdalnego (zip), który zawiera aplikację funkcji. Plik zostanie pobrany z podanego adresu URL, a aplikacja zostanie uruchomiona w trybie [uruchamiania z poziomu pakietu](run-functions-from-deployment-package.md) .

>__Jak z niej korzystać:__ Dodaj [`WEBSITE_RUN_FROM_PACKAGE`](functions-app-settings.md#website_run_from_package) do ustawień aplikacji. Wartość tego ustawienia powinna być adresem URL (lokalizacją określonego pliku pakietu, który chcesz uruchomić). Możesz dodać ustawienia [w portalu](functions-how-to-use-azure-function-app-settings.md#settings) lub za [pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).
>
>Jeśli używasz usługi Azure Blob Storage, użyj prywatnego kontenera z [sygnaturą dostępu współdzielonego](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) , aby zapewnić funkcje dostępu do pakietu. Za każdym razem, gdy aplikacja zostanie ponownie uruchomiona, pobiera kopię zawartości. Twoje odwołanie musi być ważne przez okres istnienia aplikacji.

>__Kiedy używać go:__ Zewnętrzny adres URL pakietu to jedyna obsługiwana metoda wdrażania dla Azure Functions uruchamiana w systemie Linux w planie zużycia, jeśli użytkownik nie chce, aby [zdalna kompilacja](#remote-build) była wykonywana. Gdy aktualizujesz plik pakietu, do którego odwołuje się aplikacja funkcji, musisz [ręcznie zsynchronizować wyzwalacze](#trigger-syncing) , aby poinformować platformę Azure, że Twoja aplikacja uległa zmianie.

### <a name="zip-deploy"></a>Wdróż plik zip

Użyj narzędzia zip Deploy, aby wypchnąć plik zip, który zawiera aplikację funkcji na platformie Azure. Opcjonalnie możesz ustawić aplikację, aby rozpocząć [Uruchamianie z pakietu](run-functions-from-deployment-package.md)lub określić, że [kompilacja zdalna](#remote-build) jest wykonywana.

>__Jak z niej korzystać:__ Wdróż przy użyciu ulubionego narzędzia klienckiego: [Visual Studio Code](functions-develop-vs-code.md#publish-to-azure), [Visual Studio](functions-develop-vs.md#publish-to-azure)lub z wiersza polecenia przy użyciu [Azure Functions Core Tools](functions-run-local.md#project-file-deployment). Domyślnie narzędzia te używają wdrożenia zip i [uruchamiania z pakietu](run-functions-from-deployment-package.md). Podstawowe narzędzia i rozszerzenie Visual Studio Code umożliwiają [zdalne Kompilowanie](#remote-build) podczas wdrażania w systemie Linux. Aby ręcznie wdrożyć plik zip w aplikacji funkcji, postępuj zgodnie z instrukcjami w temacie [Deploy from a. zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

>Podczas wdrażania przy użyciu narzędzia zip Deploy można ustawić [Uruchamianie aplikacji z pakietu](run-functions-from-deployment-package.md). Aby uruchomić z pakietu, ustaw `WEBSITE_RUN_FROM_PACKAGE` wartość ustawienia aplikacji [] (Functions-App-Settings. MD # website_run_from_package `1` . Zalecamy wdrożenie pliku zip. Zapewnia ona krótszy czas ładowania aplikacji i jest to wartość domyślna dla VS Code, Visual Studio i interfejsu wiersza polecenia platformy Azure.

>__Kiedy używać go:__ Wdrożenie zip jest zalecaną technologią wdrażania dla Azure Functions.

### <a name="docker-container"></a>Kontener platformy Docker

Można wdrożyć obraz kontenera systemu Linux zawierający aplikację funkcji.

>__Jak z niej korzystać:__ Utwórz aplikację funkcji systemu Linux w planie Premium lub dedykowanym i określ, z którego obrazu kontenera chcesz korzystać. Możesz to zrobić na dwa sposoby:
>
>* Utwórz aplikację funkcji systemu Linux na planie Azure App Service w Azure Portal. W obszarze **Publikowanie** wybierz opcję **obraz platformy Docker**, a następnie skonfiguruj kontener. Wprowadź lokalizację, w której jest hostowany obraz.
>* Tworzenie aplikacji funkcji systemu Linux w planie App Service przy użyciu interfejsu wiersza polecenia platformy Azure. Aby dowiedzieć się, jak to zrobić, zobacz [Tworzenie funkcji w systemie Linux przy użyciu obrazu niestandardowego](functions-create-function-linux-custom-image.md#create-supporting-azure-resources-for-your-function).
>
>Aby wdrożyć w istniejącej aplikacji przy użyciu niestandardowego kontenera, w [Azure Functions Core Tools](functions-run-local.md)Użyj [`func deploy`](functions-run-local.md#publish) polecenia.

>__Kiedy używać go:__ Użyj opcji kontenera Docker, gdy potrzebna jest większa kontrola nad środowiskiem systemu Linux, w której działa aplikacja funkcji. Ten mechanizm wdrażania jest dostępny tylko w przypadku funkcji działających w systemie Linux.

### <a name="web-deploy-msdeploy"></a>Web Deploy (MSDeploy)

Web Deploy pakiety i wdraża aplikacje systemu Windows na dowolnym serwerze usług IIS, w tym aplikacje funkcji działające w systemie Windows na platformie Azure.

>__Jak z niej korzystać:__ Użyj [narzędzi Visual Studio Tools for Azure Functions](functions-create-your-first-function-visual-studio.md). Wyczyść pole wyboru **Uruchom z pliku pakietu (zalecane)** .
>
>Możesz również pobrać [Web Deploy 3,6](https://www.iis.net/downloads/microsoft/web-deploy) i bezpośrednio wywoływać `MSDeploy.exe` .

>__Kiedy używać go:__ Web Deploy jest obsługiwana i nie ma żadnych problemów, ale preferowany mechanizm to polecenie [zip Deploy z włączonym pakietem uruchamiania z pakietu](#zip-deploy). Aby dowiedzieć się więcej, zobacz [Przewodnik programowania w programie Visual Studio](functions-develop-vs.md#publish-to-azure).

### <a name="source-control"></a>Kontrola źródła

Użyj kontroli źródła, aby połączyć aplikację funkcji z repozytorium git. Aktualizacja kodu w tym repozytorium wyzwala wdrożenie. Aby uzyskać więcej informacji, zobacz witrynę [typu wiki kudu](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments).

>__Jak z niej korzystać:__ Użyj centrum wdrażania w obszarze funkcje portalu, aby skonfigurować publikowanie z kontroli źródła. Aby uzyskać więcej informacji, zobacz [wdrażanie ciągłe dla Azure Functions](functions-continuous-deployment.md).

>__Kiedy używać go:__ Użycie kontroli źródła jest najlepszym rozwiązaniem dla zespołów, które współpracują ze swoimi aplikacjami funkcji. Kontrola źródła jest dobrą opcją wdrażania, która umożliwia bardziej zaawansowane potoki wdrażania.

### <a name="local-git"></a>Lokalna usługa Git

Możesz użyć lokalnego narzędzia Git do wypychania kodu z komputera lokalnego do Azure Functions przy użyciu narzędzia Git.

>__Jak z niej korzystać:__ Postępuj zgodnie z instrukcjami w [lokalnym wdrożeniu git, aby Azure App Service](../app-service/deploy-local-git.md).

>__Kiedy używać go:__ Ogólnie rzecz biorąc zalecamy użycie innej metody wdrażania. W przypadku publikowania z poziomu lokalnego narzędzia Git należy [ręcznie zsynchronizować wyzwalacze](#trigger-syncing).

### <a name="cloud-sync"></a>Synchronizacja z chmurą

Zsynchronizuj zawartość z usługi Dropbox i OneDrive do Azure Functions za pomocą synchronizacji z chmurą.

>__Jak z niej korzystać:__ Postępuj zgodnie z instrukcjami w temacie [synchronizowanie zawartości z folderu w chmurze](../app-service/deploy-content-sync.md).

>__Kiedy używać go:__ Ogólnie rzecz biorąc, zalecamy stosowanie innych metod wdrażania. W przypadku publikowania przy użyciu funkcji synchronizacji z chmurą należy [ręcznie zsynchronizować wyzwalacze](#trigger-syncing).

### <a name="ftp"></a>FTP

Za pomocą protokołu FTP można bezpośrednio przetransferować pliki do Azure Functions.

>__Jak z niej korzystać:__ Postępuj zgodnie z instrukcjami podanymi w temacie [wdrażanie zawartości przy użyciu protokołu FTP/s](../app-service/deploy-ftp.md).

>__Kiedy używać go:__ Ogólnie rzecz biorąc, zalecamy stosowanie innych metod wdrażania. W przypadku publikowania przy użyciu protokołu FTP należy [ręcznie zsynchronizować wyzwalacze](#trigger-syncing).

### <a name="portal-editing"></a>Edytowanie portalu

W edytorze opartym na portalu można bezpośrednio edytować pliki znajdujące się w aplikacji funkcji (zasadniczo wdrażane za każdym razem, gdy zapisujesz zmiany).

>__Jak z niej korzystać:__ Aby móc edytować funkcje w Azure Portal, musisz [utworzyć funkcje w portalu](./functions-get-started.md). Aby zachować pojedyncze Źródło prawdy, przy użyciu dowolnej innej metody wdrażania funkcja jest tylko do odczytu i zapobiega edytowaniu portalu. Aby powrócić do stanu, w którym można edytować pliki w Azure Portal, można ręcznie włączyć tryb edycji z powrotem do `Read/Write` i usunąć wszystkie ustawienia aplikacji związane z wdrożeniem (na przykład [`WEBSITE_RUN_FROM_PACKAGE`](functions-app-settings.md#website_run_from_package) .

>__Kiedy używać go:__ Portal jest dobrym sposobem na rozpoczęcie pracy z Azure Functions. Aby uzyskać więcej intensywnych prac programistycznych, zalecamy użycie jednego z następujących narzędzi klienta:
>
>* [Visual Studio Code](./create-first-function-vs-code-csharp.md)
>* [Azure Functions Core Tools (wiersz polecenia)](functions-run-local.md)
>* [Visual Studio](functions-create-your-first-function-visual-studio.md)

W poniższej tabeli przedstawiono systemy operacyjne i języki obsługujące edycję portalu:

| Język | Użycie systemu Windows | Windows Premium | Dedykowane systemu Windows | Użycie systemu Linux | System Linux Premium | System Linux — dedykowany |
|-|:-----------------: |:----------------:|:-----------------:|:-----------------:|:-------------:|:---------------:|
| C# | | | | | |
| Skrypt C# |✔|✔|✔| |✔<sup>\*</sup> |✔<sup>\*</sup>|
| F# | | | | | | |
| Java | | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>\*</sup>|✔<sup>\*</sup>|
| Python (wersja zapoznawcza) | | | | | | |
| PowerShell (wersja zapoznawcza) |✔|✔|✔| | | |
| TypeScript (Node.js) | | | | | | |

<sup>*</sup> Edytowanie portalu jest włączone tylko dla wyzwalaczy HTTP i Timer dla funkcji w systemie Linux przy użyciu Premium i planów dedykowanych.

## <a name="deployment-behaviors"></a>Zachowania wdrażania

Podczas wdrażania wdrożenia można wykonać wszystkie istniejące wykonania lub przekroczyć limit czasu, po którym załadowano nowy kod, aby rozpocząć przetwarzanie żądań.

Jeśli potrzebujesz większej kontroli nad tym przejściem, musisz używać miejsc wdrożenia.

## <a name="deployment-slots"></a>Miejsca wdrożenia

Po wdrożeniu aplikacji funkcji na platformie Azure można wdrożyć ją w osobnym miejscu wdrożenia, a nie bezpośrednio w środowisku produkcyjnym. Więcej informacji na temat miejsc wdrożenia znajduje się w dokumentacji dotyczącej [miejsc wdrażania Azure Functions](functions-deployment-slots.md) .

## <a name="next-steps"></a>Następne kroki

Przeczytaj te artykuły, aby dowiedzieć się więcej o wdrażaniu aplikacji funkcji:

+ [Ciągłe wdrażanie dla Azure Functions](functions-continuous-deployment.md)
+ [Ciągłe dostarczanie za pomocą usługi Azure DevOps](functions-how-to-azure-devops.md)
+ [Wdrożenia ZIP dla Azure Functions](deployment-zip-push.md)
+ [Uruchamianie Azure Functions z pliku pakietu](run-functions-from-deployment-package.md)
+ [Automatyzowanie wdrażania zasobów dla aplikacji funkcji w Azure Functions](functions-infrastructure-as-code.md)
