---
title: Technologie wdrażania w Azure Functions
description: Poznaj różne sposoby wdrażania kodu w Azure Functions.
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: ca81067fa60836d77c4d8af121ebf415c772a1d7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789217"
---
# <a name="deployment-technologies-in-azure-functions"></a>Technologie wdrażania w Azure Functions

Możesz użyć kilku różnych technologii, aby wdrożyć kod projektu Azure Functions na platformie Azure. Ten artykuł zawiera omówienie dostępnych metod wdrażania oraz zalecenia dotyczące najlepszej metody do użycia w różnych scenariuszach. Zawiera również wyczerpującą listę kluczowych informacji o podstawowych technologiach wdrażania. 

## <a name="deployment-methods"></a>Metody wdrażania

Technologia wdrażania, której używasz do publikowania kodu na platformie Azure, jest ogólnie określana przez sposób publikowania aplikacji. Odpowiednia metoda wdrażania jest określana na podstawie określonych potrzeb i punktu w cyklu projektowania. Na przykład podczas opracowywania i testowania można wdrożyć bezpośrednio z narzędzia dewelopera, takiego jak Visual Studio Code. Gdy aplikacja jest w środowisku produkcyjnym, istnieje większe prawdopodobieństwo, że będziesz publikować stale z kontroli źródła lub przy użyciu zautomatyzowanego potoku publikowania, który obejmuje dodatkową walidację i testowanie.  

W poniższej tabeli opisano dostępne metody wdrażania dla projektu funkcji.

| Typ &nbsp; wdrożenia | Metody | Najlepsze dla... |
| -- | -- | -- |
| Oparte na narzędziach | &bull;&nbsp;[Publikowanie &nbsp; w programie Visual Studio &nbsp; Code &nbsp;](functions-develop-vs-code.md#publish-to-azure)<br/>&bull;&nbsp;[Visual Studio publikowania](functions-develop-vs.md#publish-to-azure)<br/>&bull;&nbsp;[Publikowanie narzędzi Core Tools](functions-run-local.md#publish) | Wdrożenia podczas opracowywania i inne wdrożenia ad hoc. Wdrożenia są zarządzane lokalnie za pomocą narzędzi. | 
| App Service zarządzane| &bull;&nbsp;[Centrum &nbsp; &nbsp; wdrażania (CI/CD)](functions-continuous-deployment.md)<br/>&bull;&nbsp;[Wdrożenia &nbsp; kontenerów](functions-create-function-linux-custom-image.md#enable-continuous-deployment-to-azure) |  Ciągłe wdrażanie (CI/CD) z kontroli źródła lub z rejestru kontenerów. Wdrożenia są zarządzane przez platformę App Service (Kudu).|
| Potoki zewnętrzne|&bull;&nbsp;[Azure Pipelines](functions-how-to-azure-devops.md)<br/>&bull;&nbsp;[GitHub Actions](functions-how-to-github-actions.md) | Potoki produkcyjne i DevOps, które obejmują dodatkową walidację, testowanie i inne akcje, są uruchamiane w ramach zautomatyzowanego wdrożenia. Wdrożenia są zarządzane przez potok. |

Podczas gdy określone wdrożenia usługi Functions korzystają z najlepszej technologii w zależności od kontekstu, większość metod wdrażania jest oparta na wdrożeniu [zip.](#zip-deploy)

## <a name="deployment-technology-availability"></a>Dostępność technologii wdrażania

Azure Functions obsługuje lokalne tworzenie i hostowanie na wiele platform w systemach Windows i Linux. Obecnie dostępne są trzy plany hostingu:

+ [Zużycie](consumption-plan.md)
+ [Premium](functions-premium-plan.md)
+ [Dedykowane (App Service)](dedicated-plan.md)

Każdy plan ma inne zachowania. Nie wszystkie technologie wdrażania są dostępne dla każdego Azure Functions. Na poniższym wykresie przedstawiono, które technologie wdrażania są obsługiwane dla każdej kombinacji systemu operacyjnego i planu hostingu:

| Technologia wdrażania | Zużycie systemu Windows | Windows Premium | Windows Dedicated  | Zużycie w systemie Linux | Linux Premium | Linux Dedicated |
|-----------------------|:-------------------:|:-------------------------:|:------------------:|:---------------------------:|:-------------:|:---------------:|
| Adres URL<sup>pakietu zewnętrznego 1</sup> |✔|✔|✔|✔|✔|✔|
| Wdrażanie z pliku zip |✔|✔|✔|✔|✔|✔|
| Kontener platformy Docker | | | | |✔|✔|
| Web Deploy |✔|✔|✔| | | |
| Kontrola źródła |✔|✔|✔| |✔|✔|
| Lokalne repozytorium Git<sup>1</sup> |✔|✔|✔| |✔|✔|
| Synchronizacja<sup>chmury 1</sup> |✔|✔|✔| |✔|✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|✔|
| Edytowanie portalu |✔|✔|✔| |✔<sup>2</sup>|✔<sup>2</sup>|

<sup>1 Technologia</sup> wdrażania wymagająca [ręcznej synchronizacji wyzwalaczy.](#trigger-syncing)
<sup>2 Edytowanie</sup> portalu jest włączone tylko dla wyzwalaczy HTTP i czasomierza dla funkcji w systemie Linux przy użyciu planów Premium i Dedicated.

## <a name="key-concepts"></a>Kluczowe pojęcia

Niektóre kluczowe pojęcia mają kluczowe znaczenie dla zrozumienia sposobu działania wdrożeń w Azure Functions.

### <a name="trigger-syncing"></a>Synchronizacja wyzwalaczy

Po zmianie dowolnego wyzwalacza infrastruktura funkcji musi wiedzieć o zmianach. Synchronizacja jest automatyczna dla wielu technologii wdrażania. Jednak w niektórych przypadkach należy ręcznie zsynchronizować wyzwalacze. W przypadku wdrażania aktualizacji przez odwołanie się do zewnętrznego adresu URL pakietu, lokalnego narzędzia Git, synchronizacji w chmurze lub protokołu FTP należy ręcznie zsynchronizować wyzwalacze. Wyzwalacze można synchronizować na jeden z trzech sposobów:

* Uruchom ponownie aplikację funkcji w Azure Portal
* Wyślij żądanie HTTP POST na adres `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` przy użyciu klucza [głównego](functions-bindings-http-webhook-trigger.md#authorization-keys).
* Wyślij żądanie HTTP POST na adres `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01` . Zastąp symbole zastępcze identyfikatorem subskrypcji, nazwą grupy zasobów i nazwą aplikacji funkcji.

### <a name="remote-build"></a>Kompilacja zdalna

Azure Functions automatycznie tworzyć kompilacje kodu odbierane po wdrożeniach zip. Te kompilacje zachowują się nieco inaczej w zależności od tego, czy aplikacja działa w systemie Windows, czy Linux. Zdalne kompilacje nie są wykonywane, gdy wcześniej ustawiono uruchamianie aplikacji w trybie [Uruchom z](run-functions-from-deployment-package.md) pakietu. Aby dowiedzieć się, jak używać kompilacji zdalnej, przejdź do pliku [zip deploy](#zip-deploy).

> [!NOTE]
> Jeśli masz problemy z kompilacją zdalną, może to być spowodowane tym, że aplikacja została utworzona przed jej udostępnionej (1 sierpnia 2019 r.). Spróbuj utworzyć nową aplikację funkcji lub uruchomić, `az functionapp update -g <RESOURCE_GROUP_NAME> -n <APP_NAME>` aby zaktualizować aplikację funkcji. To polecenie może potrwać dwa próby powodzenia.

#### <a name="remote-build-on-windows"></a>Zdalna kompilacja w systemie Windows

Wszystkie aplikacje funkcji działające w systemie Windows mają małą aplikację do zarządzania, witrynę SCM [(lub Kudu).](https://github.com/projectkudu/kudu) Ta witryna obsługuje większość logiki wdrażania i kompilowania dla Azure Functions.

Po wdrożeniu aplikacji w systemie Windows są uruchamiane polecenia specyficzne dla języka, takie jak `dotnet restore` (C#) lub `npm install` (JavaScript).

#### <a name="remote-build-on-linux"></a>Zdalna kompilacja w systemie Linux

Aby włączyć zdalną kompilację w systemie Linux, należy [ustawić](functions-how-to-use-azure-function-app-settings.md#settings) następujące ustawienia aplikacji:

* `ENABLE_ORYX_BUILD=true`
* `SCM_DO_BUILD_DURING_DEPLOYMENT=true`

Domyślnie zarówno [program Azure Functions Core Tools,](functions-run-local.md) [jak i rozszerzenie Azure Functions na](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure) Visual Studio Code kompilacje zdalne podczas wdrażania w systemie Linux. W związku z tym oba narzędzia automatycznie tworzą te ustawienia na platformie Azure.

Gdy aplikacje są budowane zdalnie w systemie Linux, [są uruchamiane z pakietu wdrożeniowego](run-functions-from-deployment-package.md).

##### <a name="consumption-plan"></a>Plan Zużycie

Aplikacje funkcji systemu Linux uruchomione w planie Zużycie nie mają witryny SCM/Kudu, co ogranicza opcje wdrażania. Jednak aplikacje funkcji w systemie Linux uruchomione w planie Zużycie obsługują kompilacje zdalne.

##### <a name="dedicated-and-premium-plans"></a>Plany dedykowane i Premium

Aplikacje funkcji działające w systemie Linux w planie [dedykowanym (App Service)](dedicated-plan.md) i [planie Premium](functions-premium-plan.md) mają również ograniczoną witrynę SCM/Kudu.

## <a name="deployment-technology-details"></a>Szczegóły technologii wdrażania

Następujące metody wdrażania są dostępne w Azure Functions.

### <a name="external-package-url"></a>Adres URL pakietu zewnętrznego

Adres URL pakietu zewnętrznego umożliwia odwołanie się do pliku pakietu zdalnego (zip) zawierającego aplikację funkcji. Plik zostanie pobrany z podanego adresu URL, a aplikacja zostanie uruchomiona [w trybie Uruchom z](run-functions-from-deployment-package.md) pakietu.

>__Jak z niego korzystać:__ Dodaj [`WEBSITE_RUN_FROM_PACKAGE`](functions-app-settings.md#website_run_from_package) do ustawień aplikacji. Wartością tego ustawienia powinien być adres URL (lokalizacja określonego pliku pakietu, który chcesz uruchomić). Ustawienia można dodać w [portalu lub](functions-how-to-use-azure-function-app-settings.md#settings) przy [użyciu interfejsu wiersza polecenia platformy Azure.](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set)
>
>Jeśli używasz usługi Azure Blob Storage, użyj prywatnego kontenera z sygnaturą dostępu współdzielonego [(SAS),](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) aby udzielić usłudze Functions dostępu do pakietu. Za każdym razem, gdy aplikacja zostanie ponownie uruchomiona, pobiera kopię zawartości. Odwołanie musi być prawidłowe przez okres istnienia aplikacji.

>__Kiedy jej używać:__ Adres URL pakietu zewnętrznego jest jedyną obsługiwaną metodą wdrażania Azure Functions w systemie Linux w planie Zużycie, jeśli użytkownik nie chce, aby kompilacja [zdalna](#remote-build) została uruchomiona. Po zaktualizowaniu pliku pakietu, do który [](#trigger-syncing) odwołuje się aplikacja funkcji, musisz ręcznie zsynchronizować wyzwalacze, aby poinformować platformę Azure, że twoja aplikacja uległa zmianie.

### <a name="zip-deploy"></a>Wdrażanie z pliku zip

Użyj narzędzia zip deploy, aby wypchnąć plik zip zawierający aplikację funkcji na platformę Azure. Opcjonalnie możesz ustawić aplikację tak, aby uruchamiała [się z](run-functions-from-deployment-package.md)pakietu , lub określić, że odbywa się [kompilacja zdalna.](#remote-build)

>__Jak z niego korzystać:__ Wdrażanie przy użyciu ulubionego narzędzia klienckiego: [Visual Studio Code](functions-develop-vs-code.md#publish-to-azure), [Visual Studio](functions-develop-vs.md#publish-to-azure), lub z wiersza polecenia przy użyciu [Azure Functions Core Tools](functions-run-local.md#project-file-deployment). Domyślnie te narzędzia używają wdrożenia pliku zip i [uruchamiają je z pakietu](run-functions-from-deployment-package.md). Narzędzia Core Tools i rozszerzenie Visual Studio Code umożliwiają [zdalną kompilację](#remote-build) podczas wdrażania w systemie Linux. Aby ręcznie wdrożyć plik zip w aplikacji funkcji, postępuj zgodnie z instrukcjami w temacie Deploy from a zip file or URL (Wdrażanie z pliku [zip lub adresu URL).](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)

>W przypadku wdrażania przy użyciu pliku zip deploy można ustawić aplikację do uruchamiania [z pakietu](run-functions-from-deployment-package.md). Aby uruchomić z pakietu, ustaw [`WEBSITE_RUN_FROM_PACKAGE`](functions-app-settings.md#website_run_from_package) wartość ustawienia aplikacji na `1` . Zalecamy wdrożenie pliku zip. Daje to krótszy czas ładowania aplikacji i jest ustawieniem domyślnym dla aplikacji VS Code, Visual Studio i interfejsu wiersza polecenia platformy Azure.

>__Kiedy jej używać:__ Wdrażanie z pliku zip jest zalecaną technologią wdrażania dla Azure Functions.

### <a name="docker-container"></a>Kontener platformy Docker

Możesz wdrożyć obraz kontenera systemu Linux zawierający aplikację funkcji.

>__Jak z niego korzystać:__ Utwórz aplikację funkcji systemu Linux w planie Premium lub Dedicated i określ obraz kontenera do uruchomienia. Możesz to zrobić na dwa sposoby:
>
>* Utwórz aplikację funkcji systemu Linux w planie Azure App Service w Azure Portal. W **przypadku opcji** Publikuj wybierz pozycję Docker Image ( Obraz platformy **Docker),** a następnie skonfiguruj kontener. Wprowadź lokalizację, w której jest hostowany obraz.
>* Utwórz aplikację funkcji systemu Linux w planie App Service przy użyciu interfejsu wiersza polecenia platformy Azure. Aby dowiedzieć się, jak to zrobić, zobacz Create a function on Linux by using a custom image (Tworzenie [funkcji w systemie Linux przy użyciu obrazu niestandardowego).](functions-create-function-linux-custom-image.md#create-supporting-azure-resources-for-your-function)
>
>Aby wdrożyć aplikację w istniejącej aplikacji przy użyciu kontenera niestandardowego, w Azure Functions Core Tools [użyj](functions-run-local.md) [`func deploy`](functions-run-local.md#publish) polecenia .

>__Kiedy jej używać:__ Użyj opcji Kontenera platformy Docker, gdy potrzebujesz większej kontroli nad środowiskiem systemu Linux, w którym działa aplikacja funkcji. Ten mechanizm wdrażania jest dostępny tylko dla funkcji uruchomionych w systemie Linux.

### <a name="web-deploy-msdeploy"></a>Web Deploy (MSDeploy)

Web Deploy aplikacji systemu Windows i wdraża je na dowolnym serwerze usług IIS, w tym na aplikacjach funkcji działających w systemie Windows na platformie Azure.

>__Jak z niego korzystać:__ Użyj [Visual Studio narzędzi do Azure Functions](functions-create-your-first-function-visual-studio.md). Wyczyść pole **wyboru Uruchom z pliku pakietu (zalecane).**
>
>Możesz również pobrać wersję [Web Deploy 3.6](https://www.iis.net/downloads/microsoft/web-deploy) i wywołać `MSDeploy.exe` ją bezpośrednio.

>__Kiedy jej używać:__ Web Deploy jest obsługiwany i nie ma żadnych problemów, ale preferowanym mechanizmem jest wdrażanie pliku zip z [włączonym trybem Uruchom z pakietu.](#zip-deploy) Aby dowiedzieć się więcej, zobacz [Visual Studio przewodnik dewelopera.](functions-develop-vs.md#publish-to-azure)

### <a name="source-control"></a>Kontrola źródła

Użyj kontroli źródła, aby połączyć aplikację funkcji z repozytorium Git. Aktualizacja kodu w tym repozytorium wyzwala wdrożenie. Aby uzyskać więcej informacji, zobacz [kudu wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments).

>__Jak z niego korzystać:__ Użyj Centrum wdrażania w obszarze Funkcje portalu, aby skonfigurować publikowanie z kontroli źródła. Aby uzyskać więcej informacji, zobacz [Continuous deployment for Azure Functions (Ciągłe](functions-continuous-deployment.md)wdrażanie Azure Functions ).

>__Kiedy jej używać:__ Korzystanie z kontroli źródła jest najlepszym rozwiązaniem dla zespołów, które współpracują nad aplikacjami funkcji. Kontrola źródła to dobra opcja wdrażania, która umożliwia bardziej zaawansowane potoki wdrażania.

### <a name="local-git"></a>Lokalna usługa Git

Lokalnego narzędzia Git można użyć do wypychania kodu z komputera lokalnego do Azure Functions przy użyciu usługi Git.

>__Jak z niego korzystać:__ Postępuj zgodnie z instrukcjami w [te tematu Wdrażanie lokalnego narzędzia Git, aby Azure App Service](../app-service/deploy-local-git.md).

>__Kiedy jej używać:__ Ogólnie rzecz biorąc, zalecamy użycie innej metody wdrażania. Podczas publikowania z lokalnego narzędzia Git należy [ręcznie zsynchronizować wyzwalacze](#trigger-syncing).

### <a name="cloud-sync"></a>Synchronizacja z chmurą

Synchronizacja z chmurą jest w stanie synchronizować zawartość z usług Dropbox i OneDrive Azure Functions.

>__Jak z niego korzystać:__ Postępuj zgodnie z instrukcjami [w tece Synchronizuj zawartość z folderu w chmurze.](../app-service/deploy-content-sync.md)

>__Kiedy jej używać:__ Ogólnie rzecz biorąc, zalecamy inne metody wdrażania. W przypadku publikowania przy użyciu synchronizacji chmury należy [ręcznie zsynchronizować wyzwalacze](#trigger-syncing).

### <a name="ftp"></a>FTP

Za pomocą protokołu FTP można bezpośrednio przesyłać pliki do Azure Functions.

>__Jak z niego korzystać:__ Postępuj zgodnie z instrukcjami [w te tematu Wdrażanie zawartości przy użyciu protokołu FTP/s.](../app-service/deploy-ftp.md)

>__Kiedy jej używać:__ Ogólnie rzecz biorąc, zalecamy inne metody wdrażania. W przypadku publikowania przy użyciu protokołu FTP należy [ręcznie zsynchronizować wyzwalacze](#trigger-syncing).

### <a name="portal-editing"></a>Edytowanie portalu

W edytorze opartym na portalu możesz bezpośrednio edytować pliki, które znajdują się w aplikacji funkcji (zasadniczo wdrażane za każdym razem, gdy zapisujesz zmiany).

>__Jak z niego korzystać:__ Aby móc edytować funkcje w Azure Portal, musisz utworzyć [funkcje w portalu](./functions-get-started.md). Aby zachować pojedyncze źródło prawdy, użycie dowolnej innej metody wdrażania sprawia, że funkcja jest tylko do odczytu i uniemożliwia kontynuowanie edytowania w portalu. Aby powrócić do stanu, w którym można edytować pliki w programie Azure Portal, możesz ręcznie przełączyć tryb edycji z powrotem do i usunąć wszystkie ustawienia aplikacji związane z wdrożeniem `Read/Write` (takie jak [`WEBSITE_RUN_FROM_PACKAGE`](functions-app-settings.md#website_run_from_package) .

>__Kiedy jej używać:__ Portal to dobry sposób na rozpoczynanie pracy z Azure Functions. W przypadku bardziej intensywnej pracy deweloperskiej zalecamy użycie jednego z następujących narzędzi klienckich:
>
>* [Visual Studio Code](./create-first-function-vs-code-csharp.md)
>* [Azure Functions Core Tools (wiersz polecenia)](functions-run-local.md)
>* [Visual Studio](functions-create-your-first-function-visual-studio.md)

W poniższej tabeli przedstawiono systemy operacyjne i języki, które obsługują edytowanie portalu:

| Język | Zużycie systemu Windows | Windows Premium | Windows Dedicated | Zużycie w systemie Linux | Linux Premium | Linux Dedicated |
|-|:-----------------: |:----------------:|:-----------------:|:-----------------:|:-------------:|:---------------:|
| C# | | | | | |
| Skrypt języka C# |✔|✔|✔| |✔<sup>\*</sup> |✔<sup>\*</sup>|
| F# | | | | | | |
| Java | | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>\*</sup>|✔<sup>\*</sup>|
| Python (wersja zapoznawcza) | | | | | | |
| PowerShell (wersja zapoznawcza) |✔|✔|✔| | | |
| TypeScript (Node.js) | | | | | | |

<sup>*</sup> Edytowanie portalu jest włączone tylko dla wyzwalaczy HTTP i czasomierza dla usługi Functions w systemie Linux przy użyciu planów Premium i Dedicated.

## <a name="deployment-behaviors"></a>Zachowania wdrażania

Po wykonaniu wdrożenia wszystkie istniejące wykonania mogą zakończyć się lub przekrój czasu, po którym nowy kod zostanie załadowany w celu rozpoczęcia przetwarzania żądań.

Jeśli potrzebujesz większej kontroli nad tym przejściem, użyj miejsc wdrożenia.

## <a name="deployment-slots"></a>Miejsca wdrożenia

Podczas wdrażania aplikacji funkcji na platformie Azure można wdrożyć ją w osobnym miejscu wdrożenia, a nie bezpośrednio w środowisku produkcyjnym. Aby uzyskać więcej informacji na temat miejsc wdrożenia, zobacz [dokumentację Azure Functions wdrożenia,](functions-deployment-slots.md) aby uzyskać szczegółowe informacje.

## <a name="next-steps"></a>Następne kroki

Przeczytaj następujące artykuły, aby dowiedzieć się więcej na temat wdrażania aplikacji funkcji:

+ [Ciągłe wdrażanie dla usługi Azure Functions](functions-continuous-deployment.md)
+ [Ciągłe dostarczanie przy użyciu Azure DevOps](functions-how-to-azure-devops.md)
+ [Wdrożenia plików zip dla Azure Functions](deployment-zip-push.md)
+ [Uruchamianie Azure Functions z pliku pakietu](run-functions-from-deployment-package.md)
+ [Automatyzowanie wdrażania zasobów dla aplikacji funkcji w Azure Functions](functions-infrastructure-as-code.md)
