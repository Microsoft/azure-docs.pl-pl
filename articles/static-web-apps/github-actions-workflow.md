---
title: GitHub Actions przepływów pracy dla Azure Static Web Apps
description: Dowiedz się, jak za pomocą repozytoriów GitHub skonfigurować ciągłe wdrażanie w Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: cshoe
ms.openlocfilehash: b20a1670c13a272ed48088567a205d854ac99179
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791251"
---
# <a name="github-actions-workflows-for-azure-static-web-apps-preview"></a>GitHub Actions przepływów pracy dla wersji Azure Static Web Apps zapoznawczej

Po utworzeniu nowego zasobu Azure Static Web Apps platforma Azure GitHub Actions przepływ pracy w celu kontrolowania ciągłego wdrażania aplikacji. Przepływ pracy jest sterowany przez plik YAML. W tym artykule o szczegółowe informacje o strukturze i opcjach pliku przepływu pracy.

Wdrożenia są inicjowane przez [wyzwalacze](#triggers), które uruchamiają [zadania](#jobs) zdefiniowane przez poszczególne [kroki](#steps).

> [!NOTE]
> Azure Static Web Apps obsługuje również Azure DevOps. Aby [uzyskać informacje Azure DevOps](publish-devops.md) konfigurowania potoku, zobacz Publikowanie za pomocą Azure DevOps z raportami.

## <a name="file-location"></a>Lokalizacja pliku

Po podaniu linku do repozytorium GitHub Azure Static Web Apps do repozytorium zostanie dodany plik przepływu pracy.

Wykonaj następujące kroki, aby wyświetlić wygenerowany plik przepływu pracy.

1. Otwórz repozytorium aplikacji w witrynie GitHub.
1. Na karcie _Kod_ kliknij `.github/workflows` folder .
1. Kliknij plik o nazwie podobnej do `azure-static-web-apps-<RANDOM_NAME>.yml` .

Plik YAML w repozytorium będzie podobny do następującego przykładu:

```yml
name: Azure Static Web Apps CI/CD

on:
  push:
    branches:
      - main
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main

jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true
      - name: Build And Deploy
        id: builddeploy
        uses: Azure/static-web-apps-deploy@v0.0.1-preview
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
          repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
          action: 'upload'
          ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
          app_location: '/' # App source code path
          api_location: 'api' # Api source code path - optional
          output_location: 'dist' # Built app content directory - optional
          ###### End of Repository/Build Configurations ######

  close_pull_request_job:
    if: github.event_name == 'pull_request' && github.event.action == 'closed'
    runs-on: ubuntu-latest
    name: Close Pull Request Job
    steps:
      - name: Close Pull Request
        id: closepullrequest
        uses: Azure/static-web-apps-deploy@v0.0.1-preview
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
          action: 'close'
```

## <a name="triggers"></a>Wyzwalacze

Wyzwalacz GitHub Actions [powiadamia](https://help.github.com/actions/reference/events-that-trigger-workflows) przepływ pracy GitHub Actions o uruchomieniu zadania na podstawie wyzwalaczy zdarzeń. Wyzwalacze są wyświetlane przy użyciu `on` właściwości w pliku przepływu pracy.

```yml
on:
  push:
    branches:
      - main
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main
```

Za pomocą ustawień skojarzonych z właściwością można zdefiniować, które gałęzie wyzwalają zadanie, i ustawić wyzwalacze do wyzwalania `on` dla różnych stanów żądania ściągnnięcia.

W tym przykładzie przepływ pracy jest uruchomiony w przypadku _zmiany gałęzi_ głównej. Zmiany, które uruchamiają przepływ pracy, obejmują wypychanie zatwierdzeń i otwieranie żądań ściągnięć dla wybranej gałęzi.

## <a name="jobs"></a>Stanowiska

Każdy wyzwalacz zdarzenia wymaga programu obsługi zdarzeń. [Zadania](https://help.github.com/actions/reference/workflow-syntax-for-github-actions#jobs) definiują, co się dzieje po wyzwoleniu zdarzenia.

W pliku Static Web Apps przepływu pracy dostępne są dwa zadania.

| Nazwa                     | Opis                                                                                                    |
| ------------------------ | -------------------------------------------------------------------------------------------------------------- |
| `build_and_deploy_job`   | Jest wykonywane podczas wypychania zatwierdzeń lub otwierania żądania ściągnnięcia względem gałęzi wymienionej we właściwości `on` .          |
| `close_pull_request_job` | Wykonuje polecenie TYLKO po zamknięciu żądania ściągnięcia, co powoduje usunięcie środowiska przejściowego utworzonego z żądań ściągnięcia. |

## <a name="steps"></a>Kroki

Kroki są zadaniami sekwencyjnymi dla zadania. Krok wykonuje akcje, takie jak instalowanie zależności, uruchamianie testów i wdrażanie aplikacji w środowisku produkcyjnym.

Plik przepływu pracy definiuje następujące kroki.

| Zadanie                      | Kroki                                                                                                                              |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------- |
| `build_and_deploy_job`   | <ol><li>Sprawdza repozytorium w środowisku akcji.<li>Kompilowanie i wdrażanie repozytorium w Azure Static Web Apps.</ol> |
| `close_pull_request_job` | <ol><li>Powiadamia Azure Static Web Apps, że żądanie ściągnięto.</ol>                                                        |

## <a name="build-and-deploy"></a>Tworzenie i wdrażanie

Krok o nazwie `Build and Deploy` jest kompilowany i wdrażany w Azure Static Web Apps wystąpienia. W sekcji `with` możesz dostosować następujące wartości dla swojego wdrożenia.

```yml
with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
  repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
  action: 'upload'
  ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
  app_location: '/' # App source code path
  api_location: 'api' # Api source code path - optional
  output_location: 'dist' # Built app content directory - optional
  ###### End of Repository/Build Configurations ######
```

[!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

Wartości `repo_token` `action` , i są `azure_static_web_apps_api_token` ustawiane automatycznie, Azure Static Web Apps nie należy zmieniać ręcznie.

## <a name="custom-build-commands"></a>Niestandardowe polecenia kompilacji

Możesz mieć precyzyjne sterowanie tym, jakie polecenia są uruchamiane podczas wdrażania. Poniższe polecenia można zdefiniować w sekcji `with` zadania.

Wdrożenie zawsze wywołuje przed `npm install` każdym poleceniem niestandardowym.

| Polecenie             | Opis                                                                                                                                                                                                                                                                                                                                                                                |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `app_build_command` | Definiuje niestandardowe polecenie do uruchomienia podczas wdrażania aplikacji zawartości statycznej.<br><br>Aby na przykład skonfigurować kompilację produkcyjną dla aplikacji Angular, utwórz skrypt npm o nazwie do uruchomienia i wprowadź polecenie `build-prod` `ng build --prod` jako polecenie `npm run build-prod` niestandardowe. Jeśli pole pozostaje puste, przepływ pracy próbuje uruchomić `npm run build` polecenia lub `npm run build:azure` . |
| `api_build_command` | Definiuje niestandardowe polecenie do uruchomienia podczas wdrażania aplikacji Azure Functions API.                                                                                                                                                                                                                                                                                                  |

## <a name="skip-app-build"></a>Pomijanie kompilacji aplikacji

Jeśli potrzebujesz pełnej kontroli nad tworzeniem aplikacji frontonie, możesz dodać niestandardowe kroki kompilacji w przepływie pracy. Następnie możesz skonfigurować akcję Static Web Apps, aby pominąć proces automatycznej kompilacji i po prostu wdrożyć aplikację skompilowaną w poprzednim kroku.

Aby pominąć tworzenie aplikacji, ustaw na i `skip_app_build` `true` na `app_location` lokalizację folderu do wdrożenia.

```yml
with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
  repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
  action: 'upload'
  ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
  app_location: 'dist' # Application build output generated by a previous step
  api_location: 'api' # Api source code path - optional
  output_location: '' # Leave this empty
  skip_app_build: true
  ###### End of Repository/Build Configurations ######
```

| Właściwość         | Opis                                                 |
| ---------------- | ----------------------------------------------------------- |
| `skip_app_build` | Ustaw wartość na , `true` aby pominąć tworzenie aplikacji frontonie. |

> [!NOTE]
> Kompilację dla aplikacji frontonie można pominąć tylko. Jeśli aplikacja ma interfejs API, nadal będzie budowaną za pomocą Static Web Apps GitHub.

## <a name="route-file-location"></a>Lokalizacja pliku tras

Możesz dostosować przepływ pracy, aby szukaćroutes.js[ w](routes.md) dowolnym folderze w repozytorium. Następującą właściwość można zdefiniować w sekcji `with` zadania.

| Właściwość          | Opis                                                                                                                                 |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| `routes_location` | Definiuje lokalizację katalogu, w _którymroutes.jsw_ pliku. Ta lokalizacja jest względna względem katalogu głównego repozytorium. |

Jawne informacje o lokalizacji pliku _routes.js_ są szczególnie ważne, jeśli krok kompilacji struktury frontonie domyślnie nie przenosi tego pliku `output_location` do pliku .

> [!IMPORTANT]
> Funkcje zdefiniowane w _routes.jsw pliku_ są teraz przestarzałe. Zobacz plik konfiguracji [Azure Static Web Apps,](./configuration.md) aby uzyskać informacje ostaticwebapp.config.js _na stronie_.

## <a name="environment-variables"></a>Zmienne środowiskowe

Zmienne środowiskowe dla kompilacji można ustawić za pośrednictwem sekcji `env` konfiguracji zadania.

```yaml
jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true
      - name: Build And Deploy
        id: builddeploy
        uses: Azure/static-web-apps-deploy@v0.0.1-preview
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          repo_token: ${{ secrets.GITHUB_TOKEN }}
          action: 'upload'
          ###### Repository/Build Configurations
          app_location: '/'
          api_location: 'api'
          output_location: 'public'
          ###### End of Repository/Build Configurations ######
        env: # Add environment variables here
          HUGO_VERSION: 0.58.0
```

## <a name="monorepo-support"></a>Obsługa monorepo

Monorepo to repozytorium zawierające kod dla więcej niż jednej aplikacji. Domyślnie plik przepływu Static Web Apps śledzi wszystkie pliki w repozytorium, ale można go dostosować tak, aby był ukierunkowany na pojedynczą aplikację. W związku z tym w przypadku monorepos każda aplikacja statyczna ma własny plik konfiguracji, który znajduje się obok siebie w folderze _.github/workflows repozytorium._

```files
├── .github
│   └── workflows
│       ├── azure-static-web-apps-purple-pond.yml
│       └── azure-static-web-apps-yellow-shoe.yml
│
├── app1  👉 controlled by: azure-static-web-apps-purple-pond.yml
├── app2  👉 controlled by: azure-static-web-apps-yellow-shoe.yml
│
├── api1  👉 controlled by: azure-static-web-apps-purple-pond.yml
├── api2  👉 controlled by: azure-static-web-apps-yellow-shoe.yml
│
└── README.md
```

Aby wskazać plik przepływu pracy dla jednej aplikacji, należy określić ścieżki w `push` sekcjach `pull_request` i .

W poniższym przykładzie pokazano, jak dodać węzeł do sekcji i pliku o `paths` `push` nazwie `pull_request` _azure-static-web-apps-purple-yml._

```yml
on:
  push:
    branches:
      - main
    paths:
      - app1/**
      - api1/**
      - .github/workflows/azure-static-web-apps-purple-pond.yml
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main
    paths:
      - app1/**
      - api1/**
      - .github/workflows/azure-static-web-apps-purple-pond.yml
```

W tym przypadku tylko zmiany wprowadzone w następujących plikach wyzwalają nową kompilację:

- Wszystkie pliki w _folderze app1_
- Wszystkie pliki w _folderze api1_
- Zmiany w pliku przepływu pracy _azure-static-web-apps-purple-html.yml_ aplikacji

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przeglądanie żądań ściągnięcia w środowiskach przedprodukcyjnych](review-publish-pull-requests.md)
