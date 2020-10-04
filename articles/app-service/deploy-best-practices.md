---
title: Najlepsze praktyki dotyczące wdrażania
description: Zapoznaj się z najważniejszymi mechanizmami wdrażania do Azure App Service. Znajdź zalecenia specyficzne dla języka i inne zastrzeżenia.
keywords: usługa Azure App Service, aplikacja sieci Web, wdrażanie, wdrażanie, potoki, kompilacja
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: 37c1854aeb1a1fa3d9283c00b07c665b213b306c
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2020
ms.locfileid: "91708156"
---
# <a name="deployment-best-practices"></a>Najlepsze rozwiązania dotyczące wdrażania

Każdy zespół programistyczny ma unikatowe wymagania, które mogą spowodować, że wdrożenie wydajnego potoku wdrożenia jest trudne dla każdej usługi w chmurze. W tym artykule przedstawiono trzy główne składniki wdrażania do App Service: źródła wdrożenia, potoki kompilacji i mechanizmy wdrażania. W tym artykule opisano również najlepsze rozwiązania i wskazówki dotyczące konkretnych stosów języka.

## <a name="deployment-components"></a>Składniki wdrożenia

### <a name="deployment-source"></a>Źródło wdrożenia

Źródłem wdrożenia jest lokalizacja kodu aplikacji. W przypadku aplikacji produkcyjnych źródłem wdrożenia jest zwykle repozytorium hostowane przez oprogramowanie kontroli wersji, takie jak [GitHub, BitBucket lub Azure Repos](deploy-continuous-deployment.md). W przypadku scenariuszy deweloperskich i testowych źródłem wdrożenia może być [projekt na komputerze lokalnym](deploy-local-git.md). App Service obsługuje również [foldery OneDrive i Dropbox](deploy-content-sync.md) jako źródła wdrożenia. Foldery w chmurze ułatwiają rozpoczęcie pracy z App Service, jednak zazwyczaj nie zaleca się używania tego źródła dla aplikacji produkcyjnych na poziomie przedsiębiorstwa. 

### <a name="build-pipeline"></a>Potok kompilacji

Po podjęciu decyzji dotyczącej źródła wdrożenia następnym krokiem jest wybranie potoku kompilacji. Potok kompilacji odczytuje kod źródłowy ze źródła wdrożenia i wykonuje serię kroków (takich jak kompilowanie kodu, minifikacja HTML i JavaScript, uruchamianie testów i składniki pakowania) w celu uzyskania aplikacji w stanie możliwy do uruchomienia. Określone polecenia wykonywane przez potok kompilacji zależą od stosu języka. Te operacje mogą być wykonywane na serwerze kompilacji, takim jak Azure Pipelines lub wykonywane lokalnie.

### <a name="deployment-mechanism"></a>Mechanizm wdrażania

Mechanizm wdrażania to akcja użyta do umieszczenia skompilowanej aplikacji w katalogu */Home/site/wwwroot* aplikacji sieci Web. Katalog */wwwroot* to zainstalowana lokalizacja magazynu udostępniana przez wszystkie wystąpienia aplikacji sieci Web. Gdy mechanizm wdrażania umieści aplikację w tym katalogu, Twoje wystąpienia otrzymają powiadomienie w celu zsynchronizowania nowych plików. App Service obsługuje następujące mechanizmy wdrażania:

- Punkty końcowe kudu: [kudu](https://github.com/projectkudu/kudu/wiki) to narzędzie do wydajnej produktywności dla deweloperów typu "open source", które działa jako oddzielny proces w systemie Windows App Service i jako drugi kontener w App Service systemu Linux. Kudu obsługuje ciągłe wdrożenia i udostępnia punkty końcowe HTTP do wdrożenia, takie jak zipdeploy.
- FTP i webdeploy: używanie [poświadczeń witryny lub użytkownika](deploy-configure-credentials.md)umożliwia przekazywanie plików [za pośrednictwem protokołu FTP](deploy-ftp.md) lub webdeploy. Te mechanizmy nie przechodzą przez kudu.  

Narzędzia wdrażania, takie jak Azure Pipelines, Jenkins i wtyczki edytora, korzystają z jednego z tych mechanizmów wdrażania.

## <a name="use-deployment-slots"></a>Korzystanie z miejsc wdrożenia

Jeśli to możliwe, należy używać [miejsc wdrożenia](deploy-staging-slots.md) podczas wdrażania nowej kompilacji produkcyjnej. W przypadku korzystania ze standardowej warstwy planu App Service lub lepszej można wdrożyć aplikację w środowisku przejściowym, zweryfikować zmiany i przeprowadzić testy dymu. Gdy wszystko będzie gotowe, możesz wymienić miejsca przejściowe i produkcyjne. Operacja wymiany rozgrzewa niezbędne wystąpienia procesów roboczych w celu dopasowania do skali produkcyjnej, eliminując w ten sposób przestoje.

### <a name="continuously-deploy-code"></a>Ciągle Wdrażaj kod

Jeśli projekt wyznaczył rozgałęzienia do testowania, kontroli jakości i przemieszczania, każda z tych gałęzi powinna być ciągle wdrażana do miejsca przejściowego. (Jest to tzw. [projekt Gitflow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)). Dzięki temu zainteresowane strony mogą łatwo ocenić i przetestować wdrożone rozgałęzienie. 

Nie należy włączać ciągłego wdrażania dla miejsca produkcyjnego. Zamiast tego gałąź produkcyjna (często Master) powinna zostać wdrożona w gnieździe nieprodukcyjnym. Gdy wszystko jest gotowe do zwolnienia gałęzi bazowej, należy zamienić ją na miejsce produkcyjne. Zamiana na produkcję — zamiast wdrażania w środowisku produkcyjnym — zapobiega przestojom i pozwala wycofać zmiany przez zamianę. 

![Diagram przedstawiający przepływ między gałęziami dev, Staging i Master oraz miejsca, w których są wdrażane.](media/app-service-deploy-best-practices/slot_flow_code_diagam.png)

### <a name="continuously-deploy-containers"></a>Ciągle Wdrażaj kontenery

W przypadku kontenerów niestandardowych z platformy Docker lub innych rejestrów kontenerów należy wdrożyć obraz w miejscu przejściowym i przełączyć się do środowiska produkcyjnego, aby zapobiec przestojom. Automatyzacja jest bardziej złożona niż wdrożenie kodu, ponieważ należy wypchnąć obraz do rejestru kontenerów i zaktualizować tag obrazu na WEBAPP.

Dla każdej gałęzi, którą chcesz wdrożyć w gnieździe, skonfiguruj automatyzację, aby wykonać następujące czynności na każdym zatwierdzeniu do gałęzi.

1. **Kompiluj i Oznacz obraz**. W ramach potoku kompilacji Oznacz obraz za pomocą identyfikatora zatwierdzenia git, sygnatury czasowej lub innych informacji, które można zidentyfikować. Najlepiej nie używać domyślnego tagu "Najnowsza". W przeciwnym razie trudno jest śledzić, jaki kod jest obecnie wdrożony, co sprawia, że debugowanie znacznie trudniejsze.
1. **Wypchnij oznakowany obraz**. Po skompilowaniu i oznakowaniu obrazu potok wypychany do rejestru kontenerów. W następnym kroku miejsce wdrożenia spowoduje ściąganie oznakowanego obrazu z rejestru kontenerów.
1. **Zaktualizuj miejsce wdrożenia przy użyciu nowego tagu obrazu**. Po zaktualizowaniu tej właściwości lokacja zostanie automatycznie uruchomiona ponownie i zostanie pobrany nowy obraz kontenera.

![Wizualizacja użycia miejsca](media/app-service-deploy-best-practices/slot_flow_container_diagram.png)

Poniżej przedstawiono przykłady typowych struktur automatyzacji.

### <a name="use-azure-devops"></a>Korzystanie z usługi Azure DevOps

App Service ma [wbudowane ciągłe dostarczanie](deploy-continuous-deployment.md) kontenerów za pomocą centrum wdrażania. Przejdź do aplikacji w [Azure Portal](https://portal.azure.com/) a następnie wybierz pozycję **centrum wdrażania** w obszarze **wdrażanie**. Postępuj zgodnie z instrukcjami, aby wybrać repozytorium i gałąź. Spowoduje to skonfigurowanie potoku kompilowania i wydawania DevOps w celu automatycznego kompilowania, oznaczania i wdrażania kontenera podczas wypychania nowych zatwierdzeń do wybranej gałęzi.

### <a name="use-github-actions"></a>Korzystanie z akcji usługi GitHub

Możesz również zautomatyzować wdrożenie kontenera [za pomocą akcji usługi GitHub](deploy-container-github-action.md).  Poniższy plik przepływu pracy kompiluje i oznaczy kontener przy użyciu identyfikatora zatwierdzenia, wypchnij go do rejestru kontenerów i zaktualizuje określone miejsce w miejscu przy użyciu nowego tagu obrazu.

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

### <a name="use-other-automation-providers"></a>Korzystanie z innych dostawców automatyzacji

Wymienione wcześniej kroki dotyczą innych narzędzi do automatyzacji, takich jak CircleCI lub rozwiązania Travis CI. Należy jednak użyć interfejsu wiersza polecenia platformy Azure w celu zaktualizowania miejsc wdrożenia przy użyciu nowych tagów obrazu w ostatnim kroku. Aby użyć interfejsu wiersza polecenia platformy Azure w skrypcie automatyzacji, wygeneruj nazwę główną usługi przy użyciu poniższego polecenia.

```shell
az ad sp create-for-rbac --name "myServicePrincipal" --role contributor \
   --scopes /subscriptions/{subscription}/resourceGroups/{resource-group} \
   --sdk-auth
```

W skrypcie Zaloguj się przy użyciu `az login --service-principal` , dostarczając informacje o podmiotu zabezpieczeń. Można następnie użyć `az webapp config container set` , aby ustawić nazwę kontenera, tag, adres URL rejestru i hasło rejestru. Poniżej znajdują się przydatne linki służące do konstruowania procesu elementu konfiguracji kontenera.

- [Jak zalogować się do interfejsu wiersza polecenia platformy Azure w ramach elementu konfiguracji okręgu](https://circleci.com/orbs/registry/orb/circleci/azure-cli) 

## <a name="language-specific-considerations"></a>Uwagi dotyczące języka

### <a name="java"></a>Java

Użyj kudu [zipdeploy/](deploy-zip.md) API do wdrażania aplikacji jar i [Narzędzia WARDEPLOY/](deploy-zip.md#deploy-war-file) for War. Jeśli używasz Jenkins, możesz używać tych interfejsów API bezpośrednio w fazie wdrażania. Aby uzyskać więcej informacji, zobacz [ten artykuł](/azure/developer/jenkins/deploy-to-azure-app-service-using-azure-cli).

### <a name="node"></a>Węzeł

Domyślnie kudu wykonuje kroki kompilacji dla aplikacji węzła ( `npm install` ). Jeśli używasz usługi kompilacji, takiej jak Azure DevOps, kompilacja kudu jest niepotrzebna. Aby wyłączyć kompilację kudu, Utwórz ustawienie aplikacji `SCM_DO_BUILD_DURING_DEPLOYMENT` o wartości `false` .

### <a name="net"></a>.NET 

Domyślnie kudu wykonuje kroki kompilacji dla aplikacji .NET ( `dotnet build` ). Jeśli używasz usługi kompilacji, takiej jak Azure DevOps, kompilacja kudu jest niepotrzebna. Aby wyłączyć kompilację kudu, Utwórz ustawienie aplikacji `SCM_DO_BUILD_DURING_DEPLOYMENT` o wartości `false` .

## <a name="other-deployment-considerations"></a>Inne zagadnienia dotyczące wdrażania

### <a name="local-cache"></a>Lokalna pamięć podręczna

Azure App Service zawartość jest przechowywana w usłudze Azure Storage i jest wystawiona w sposób trwały jako udział zawartości. Jednak niektóre aplikacje potrzebują tylko magazynu zawartości o wysokiej wydajności, który może działać z wysoką dostępnością. Te aplikacje mogą korzystać z [lokalnej pamięci podręcznej](overview-local-cache.md). Lokalna pamięć podręczna nie jest zalecana w przypadku witryn zarządzania zawartością, takich jak WordPress.

Zawsze używaj lokalnej pamięci podręcznej w połączeniu z miejscami [wdrożenia](deploy-staging-slots.md) , aby zapobiec przestojom. Zapoznaj się z [tą sekcją](overview-local-cache.md#best-practices-for-using-app-service-local-cache) , aby uzyskać informacje na temat korzystania z tych funkcji razem.

### <a name="high-cpu-or-memory"></a>Wysoki procesor lub pamięć

Jeśli plan App Service jest używany przez ponad 90% dostępnego procesora lub pamięci, źródłowa maszyna wirtualna może mieć problemy z przetwarzaniem wdrożenia. W takim przypadku należy tymczasowo skalować liczbę wystąpień w celu przeprowadzenia wdrożenia. Po zakończeniu wdrożenia można zwrócić liczbę wystąpień do poprzedniej wartości.

Aby uzyskać więcej informacji na temat najlepszych rozwiązań, odwiedź stronę [App Service Diagnostics](./overview-diagnostics.md) , aby dowiedzieć się, jakie są najlepsze rozwiązania, które są odpowiednie dla danego zasobu.

- Przejdź do aplikacji sieci Web w [Azure Portal](https://portal.azure.com).
- Kliknij przycisk **diagnozowanie i rozwiązywanie problemów** w lewym okienku nawigacji, co spowoduje otwarcie App Service diagnostyki.
- Wybierz kafelek strony głównej **najlepszych** rozwiązań.
- Kliknij pozycję **najlepsze rozwiązania dotyczące dostępności & wydajność** lub **najlepsze rozwiązania w zakresie optymalnej konfiguracji** , aby wyświetlić bieżący stan aplikacji w odniesieniu do tych najlepszych rozwiązań.

Za pomocą tego linku można również bezpośrednio otworzyć App Service Diagnostics dla zasobu: `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot` .