---
title: Najlepsze praktyki dotyczące wdrażania
description: Dowiedz się więcej o kluczowych mechanizmach wdrażania w usłudze Azure App Service. Znajdź zalecenia specyficzne dla języka i inne zastrzeżenia.
keywords: usługa aplikacji platformy Azure, aplikacja internetowa, wdrażanie, wdrażanie, potoki, kompilacja
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: 4dd959d75fd582d787e68db4a415a4a694b9cda8
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770696"
---
# <a name="deployment-best-practices"></a>Najważniejsze wskazówki dotyczące wdrażania

Każdy zespół deweloperów ma unikatowe wymagania, które mogą utrudnić wdrożenie potoku wdrażania wydajnego w dowolnej usłudze w chmurze. W tym artykule przedstawiono trzy główne składniki wdrażania w usłudze App Service: źródła wdrażania, potoki kompilacji i mechanizmy wdrażania. W tym artykule opisano również niektóre najlepsze rozwiązania i porady dotyczące stosów określonych języków.

## <a name="deployment-components"></a>Składniki wdrażania

### <a name="deployment-source"></a>Źródło wdrożenia

Źródło wdrożenia jest lokalizacją kodu aplikacji. W przypadku aplikacji produkcyjnych źródłem wdrożenia jest zwykle repozytorium hostowane przez oprogramowanie do kontroli wersji, takie jak [GitHub, BitBucket lub Azure Repos](deploy-continuous-deployment.md). W scenariuszach programowania i testowania źródłem wdrożenia może być [projekt na komputerze lokalnym.](deploy-local-git.md) Usługa App Service obsługuje również [foldery OneDrive i Dropbox](deploy-content-sync.md) jako źródła wdrożenia. Podczas gdy foldery w chmurze można ułatwić rozpoczęcie pracy z usługą App Service, zazwyczaj nie zaleca się używać tego źródła dla aplikacji produkcyjnych na poziomie przedsiębiorstwa. 

### <a name="build-pipeline"></a>Potok kompilacji

Po podjęciu decyzji o źródle wdrożenia następnym krokiem jest wybranie potoku kompilacji. Potok kompilacji odczytuje kod źródłowy ze źródła wdrożenia i wykonuje serię kroków (takich jak kompilacja kodu, minifying HTML i JavaScript, uruchamianie testów i składników pakowania), aby uzyskać aplikację w stanie runnable. Określone polecenia wykonywane przez potok kompilacji zależą od stosu języka. Te operacje mogą być wykonywane na serwerze kompilacji, takich jak usługi Azure Potoki, lub wykonywane lokalnie.

### <a name="deployment-mechanism"></a>Mechanizm wdrażania

Mechanizm wdrażania jest działaniem używanym do umieszczenia wbudowanej aplikacji w katalogu */home/site/wwwroot* aplikacji sieci web. Katalog */wwwroot* jest zainstalowaną lokalizacją magazynu współużytkowanym przez wszystkie wystąpienia aplikacji sieci web. Gdy mechanizm wdrażania umieszcza aplikację w tym katalogu, wystąpienia otrzymują powiadomienie o synchronizacji nowych plików. Usługa App Service obsługuje następujące mechanizmy wdrażania:

- Punkty końcowe Kudu: [Kudu](https://github.com/projectkudu/kudu/wiki) to narzędzie do produktywności dla deweloperów typu open source, które działa jako oddzielny proces w usłudze Windows App Service i jako drugi kontener w usłudze Linux App Service. Kudu obsługuje ciągłe wdrożenia i zapewnia punkty końcowe HTTP do wdrożenia, takie jak zipdeploy.
- FTP i WebDeploy: Za pomocą [poświadczeń witryny lub użytkownika](deploy-configure-credentials.md)można przesyłać pliki [za pośrednictwem ftp](deploy-ftp.md) lub WebDeploy. Mechanizmy te nie przechodzą przez Kudu.  

Narzędzia wdrażania, takie jak potoki platformy Azure, jenkins i wtyczki edytora używają jednego z tych mechanizmów wdrażania.

## <a name="use-deployment-slots"></a>Korzystanie z gniazd wdrożeniowych

Jeśli to możliwe, należy używać gniazd wdrażania podczas wdrażania nowej [kompilacji](deploy-staging-slots.md) produkcyjnej. Korzystając z warstwy Standardowy plan usługi aplikacji lub lepiej, można wdrożyć aplikację w środowisku przejściowym, sprawdzić poprawność zmian i zrobić testy dymu. Gdy będziesz gotowy, możesz zamienić swoje miejsca przejściowe i produkcyjne. Operacja wymiany rozgrzewa wystąpienia niezbędne do pracy, aby dopasować je do skali produkcyjnej, eliminując w ten sposób przestoje.

### <a name="continuously-deploy-code"></a>Ciągłe wdrażanie kodu

Jeśli projekt ma wyznaczone gałęzie do testowania, kontroli jakości i przemieszczania, a następnie każda z tych gałęzi powinny być stale wdrażane w miejscu przejściowym. (Jest to znane jako [projekt Gitflow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow).) Dzięki temu interesariusze łatwo ocenić i przetestować wdrożoną gałąź. 

Ciągłe wdrażanie nigdy nie powinny być włączone dla gniazda produkcyjnego. Zamiast tego gałęzi produkcji (często master) powinny być wdrożone na nieprodukcyjnym miejscu. Gdy wszystko będzie gotowe do zwolnienia gałęzi podstawowej, zamienić ją w gniazdo produkcyjne. Zamiana na produkcję — zamiast wdrażania w produkcji — zapobiega przestojom i umożliwia wycofanie zmian przez ponowną zamianę. 

![Wizualne użycie gniazda](media/app-service-deploy-best-practices/slot_flow_code_diagam.png)

### <a name="continuously-deploy-containers"></a>Ciągłe wdrażanie kontenerów

W przypadku kontenerów niestandardowych z platformy Docker lub innych rejestrów kontenerów należy wdrożyć obraz w miejscu przejściowym i zamienić się w środowiskach produkcyjnych, aby zapobiec przestojom. Automatyzacja jest bardziej złożona niż wdrożenie kodu, ponieważ należy wypchnąć obraz do rejestru kontenerów i zaktualizować znacznik obrazu w aplikacji webapp.

Dla każdej gałęzi, którą chcesz wdrożyć w gnieździe, skonfiguruj automatyzację, aby wykonać następujące czynności w każdym zatwierdzeniu do gałęzi.

1. **Tworzenie i oznaczanie obrazu**. W ramach potoku kompilacji otaguj obraz identyfikatorem zatwierdzenia git, sygnaturą czasową lub innymi identyfikowalnymi informacjami. Najlepiej nie używać domyślnego tagu "najnowszego". W przeciwnym razie trudno jest prześledzić, jaki kod jest obecnie wdrażany, co znacznie utrudnia debugowanie.
1. **Naciśnij oznaczony obraz**. Gdy obraz jest zbudowany i oznakowany, potok wypycha obraz do naszego rejestru kontenerów. W następnym kroku gniazdo wdrożenia będzie pobierać oznakowany obraz z rejestru kontenerów.
1. **Zaktualizuj gniazdo wdrożenia za pomocą nowego znacznika obrazu**. Po zaktualizowaniu tej właściwości witryna automatycznie uruchomi się ponownie i wyciągnie nowy obraz kontenera.

![Wizualne użycie gniazda](media/app-service-deploy-best-practices/slot_flow_container_diagram.png)

Istnieją przykłady poniżej dla typowych platform automatyzacji.

### <a name="use-azure-devops"></a>Korzystanie z usługi Azure DevOps

Usługa App Service ma [wbudowane ciągłe dostarczanie](deploy-continuous-deployment.md) kontenerów za pośrednictwem Centrum wdrażania. Przejdź do aplikacji w [witrynie Azure portal](https://portal.azure.com/) i wybierz **Pozycję Centrum wdrażania** w obszarze **Wdrażanie**. Postępuj zgodnie z instrukcjami, aby wybrać repozytorium i gałąź. Spowoduje to skonfigurowanie potoku kompilacji i wydania DevOps, aby automatycznie tworzyć, oznaczać i wdrażać kontener, gdy nowe zatwierdzenia są wypychane do wybranej gałęzi.

### <a name="use-github-actions"></a>Korzystanie z akcji GitHub

Można również zautomatyzować wdrażanie kontenera [za pomocą akcji GitHub](containers/deploy-container-github-action.md).  Poniższy plik przepływu pracy utworu będzie budować i oznaczać kontener z identyfikatorem zatwierdzenia, wypchnąć go do rejestru kontenerów i zaktualizować określone gniazdo lokacji z nowym tagiem obrazu.

```yaml
name: Build and deploy a container image to Azure Web Apps

on:
  push:
    branches:
    - <your-branch-name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@master

    -name: Authenticate using a Service Principal
      uses: azure/actions/login@v1
      with:
        creds: ${{ secrets.AZURE_SP }}

    - uses: azure/container-actions/docker-login@v1
      with:
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_PASSWORD }}

    - name: Build and push the image tagged with the git commit hash
      run: |
        docker build . -t contoso/demo:${{ github.sha }}
        docker push contoso/demo:${{ github.sha }}

    - name: Update image tag on the Azure Web App
      uses: azure/webapps-container-deploy@v1
      with:
        app-name: '<your-webapp-name>'
        slot-name: '<your-slot-name>'
        images: 'contoso/demo:${{ github.sha }}'
```

### <a name="use-other-automation-providers"></a>Korzystanie z usług innych dostawców automatyzacji

Kroki wymienione wcześniej dotyczą innych narzędzi automatyzacji, takich jak CircleCI lub Travis CI. Jednak należy użyć interfejsu wiersza polecenia platformy Azure, aby zaktualizować miejsca wdrożenia za pomocą nowych tagów obrazu w ostatnim kroku. Aby użyć interfejsu wiersza polecenia platformy Azure w skrypcie automatyzacji, wygeneruj jednostkę usługi przy użyciu następującego polecenia.

```shell
az ad sp create-for-rbac --name "myServicePrincipal" --role contributor \
   --scopes /subscriptions/{subscription}/resourceGroups/{resource-group} \
   --sdk-auth
```

W skrypcie `az login --service-principal`zaloguj się przy użyciu , podając informacje o zleceniodawcy. Następnie można `az webapp config container set` ustawić nazwę kontenera, tag, adres URL rejestru i hasło rejestru. Poniżej znajduje się kilka przydatnych linków do konstruowania procesu ci kontenera.

- [Jak zalogować się do interfejsu wiersza polecenia platformy Azure w circle ci](https://circleci.com/orbs/registry/orb/circleci/azure-cli) 

## <a name="language-specific-considerations"></a>Zagadnienia specyficzne dla języka

### <a name="java"></a>Java

Użyj [zipdeploy](deploy-zip.md) Kudu / API do wdrażania aplikacji JAR i [wardeploy /](deploy-zip.md#deploy-war-file) dla aplikacji WAR. Jeśli używasz usługi Jenkins, można użyć tych interfejsów API bezpośrednio w fazie wdrażania. Aby uzyskać więcej informacji, zobacz [ten artykuł](../jenkins/execute-cli-jenkins-pipeline.md).

### <a name="node"></a>Węzeł

Domyślnie Kudu wykonuje kroki kompilacji dla`npm install`aplikacji Node ( ). Jeśli używasz usługi kompilacji, takich jak Azure DevOps, następnie kompilacji Kudu jest niepotrzebne. Aby wyłączyć kompilację Kudu, utwórz ustawienie aplikacji, `SCM_DO_BUILD_DURING_DEPLOYMENT`o wartości . `false`

### <a name="net"></a>.NET 

Domyślnie Kudu wykonuje kroki kompilacji dla aplikacji`dotnet build`.NET ( ). Jeśli używasz usługi kompilacji, takich jak Azure DevOps, następnie kompilacji Kudu jest niepotrzebne. Aby wyłączyć kompilację Kudu, utwórz ustawienie aplikacji, `SCM_DO_BUILD_DURING_DEPLOYMENT`o wartości . `false`

## <a name="other-deployment-considerations"></a>Inne zagadnienia dotyczące wdrażania

### <a name="local-cache"></a>Lokalna pamięć podręczna

Zawartość usługi Azure App Service jest przechowywana w usłudze Azure Storage i jest udostępniana w trwały sposób jako udział zawartości. Jednak niektóre aplikacje potrzebują tylko wydajnego magazynu zawartości tylko do odczytu, który można uruchomić z wysoką dostępnością. Te aplikacje mogą korzystać z [lokalnej pamięci podręcznej](overview-local-cache.md). Lokalna pamięć podręczna nie jest zalecana dla witryn do zarządzania treścią, takich jak WordPress.

Zawsze używaj lokalnej pamięci podręcznej w połączeniu z [gniazdami wdrażania,](deploy-staging-slots.md) aby zapobiec przestojom. Zobacz [tę sekcję,](overview-local-cache.md#best-practices-for-using-app-service-local-cache) aby uzyskać informacje na temat korzystania z tych funkcji razem.

### <a name="high-cpu-or-memory"></a>Wysoki poziom procesora lub pamięci

Jeśli plan usługi aplikacji używa ponad 90% dostępnego procesora CPU lub pamięci, podstawowa maszyna wirtualna może mieć problemy z przetwarzaniem wdrożenia. W takim przypadku tymczasowo skaluj liczbę wystąpień w górę, aby wykonać wdrożenie. Po zakończeniu wdrażania można zwrócić liczbę wystąpień do poprzedniej wartości.

Aby uzyskać więcej informacji na temat najlepszych rozwiązań, odwiedź [diagnostykę usługi aplikacji,](https://docs.microsoft.com/azure/app-service/overview-diagnostics) aby dowiedzieć się, jakie praktyczne są najlepsze rozwiązania dotyczące danego zasobu.

- Przejdź do aplikacji sieci Web w [witrynie Azure portal](https://portal.azure.com).
- Kliknij przycisk **Diagnozuj i rozwiązuj problemy** w lewej nawigacji, która otwiera diagnostykę usługi app service.
- Wybierz kafelek strony głównej **Najważniejsze wskazówki.**
- Kliknij **przycisk Najważniejsze wskazówki dotyczące dostępności & wydajności** lub najlepszych **rozwiązań dotyczących optymalnej konfiguracji,** aby wyświetlić bieżący stan aplikacji w odniesieniu do tych najlepszych rozwiązań.

Możesz również użyć tego łącza, aby bezpośrednio otworzyć diagnostykę usługi app service dla zasobu: `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot`.
