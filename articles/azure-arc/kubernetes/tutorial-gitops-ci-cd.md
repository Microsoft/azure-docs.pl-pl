---
title: 'Samouczek: implementowanie ciągłej integracji/ciągłego wdrażania za pomocą GitOps przy użyciu klastrów Kubernetes z obsługą usługi Azure Arc'
description: Ten samouczek przeprowadzi Cię przez proces konfigurowania rozwiązania ciągłej integracji/ciągłego wdrażania przy użyciu usługi GitOps z obsługą klastrów Kubernetes w usłudze Azure Arc. Aby uzyskać informacje o pojęciach związanych z tym przepływem pracy, zobacz przepływ pracy ciągłej integracji/ciągłego wdrażania za pomocą GitOps — Kubernetes.
author: tcare
ms.author: tcare
ms.service: azure-arc
ms.topic: tutorial
ms.date: 03/03/2021
ms.custom: template-tutorial
ms.openlocfilehash: 72caca47cde960eb7298ec2cf0c6994755cb3159
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121613"
---
# <a name="tutorial-implement-cicd-with-gitops-using-azure-arc-enabled-kubernetes-clusters"></a>Samouczek: implementowanie ciągłej integracji/ciągłego wdrażania za pomocą GitOps przy użyciu klastrów Kubernetes z obsługą usługi Azure Arc


W tym samouczku skonfigurujesz rozwiązanie ciągłej integracji/ciągłego wdrażania przy użyciu usługi GitOps z obsługą klastrów Kubernetes w usłudze Azure Arc. Korzystając z przykładowej aplikacji do głosowania platformy Azure, możesz:

> [!div class="checklist"]
> * Utwórz klaster Kubernetes z obsługą usługi Azure Arc.
> * Połącz swoją aplikację i GitOps repozytoria do Azure Repos.
> * Importuj potoki ciągłej integracji/ciągłego wdrażania.
> * Połącz Azure Container Registry (ACR) z usługą Azure DevOps i Kubernetes.
> * Utwórz grupy zmiennych środowiskowych.
> * Wdróż `dev` środowiska i `stage` .
> * Przetestuj środowiska aplikacji.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym samouczku założono znajomość DevOps, Azure Repos i potoków platformy Azure oraz interfejsu wiersza polecenia platformy Azure.

* Zaloguj się do [Azure DevOps Services](https://dev.azure.com/).
* Wykonaj [poprzedni samouczek](https://docs.microsoft.com/azure/azure-arc/kubernetes/tutorial-use-gitops-connected-cluster) , aby dowiedzieć się, jak wdrożyć GitOps dla środowiska Ci/CD.
* Zapoznaj się z [zaletami i architekturą](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-configurations) tej funkcji.
* Sprawdź, czy masz:
  * [Połączony klaster Kubernetes z obsługą usługi Azure Arc](https://docs.microsoft.com/azure/azure-arc/kubernetes/quickstart-connect-cluster#connect-an-existing-kubernetes-cluster) o nazwie **Arc-cicd-Cluster**.
  * Połączone Azure Container Registry (ACR) z [integracją AKS](https://docs.microsoft.com/azure/aks/cluster-container-registry-integration) lub [uwierzytelnianiem klastra innym niż AKS](https://docs.microsoft.com/azure/container-registry/container-registry-auth-kubernetes).
  * Uprawnienia "Administrator kompilacji" i "Administrator projektu" dla [Azure Repos](https://docs.microsoft.com/azure/devops/repos/get-started/what-is-repos) i [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-get-started).
* Zainstaluj następujące rozszerzenia interfejsu wiersza polecenia usługi Azure Arc Kubernetes dla wersji >= 1.0.0:

  ```azurecli
  az extension add --name connectedk8s
  az extension add --name k8s-configuration
  ```
  * Aby zaktualizować te rozszerzenia do najnowszej wersji, uruchom następujące polecenia:

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-configuration
    ```

## <a name="import-application-and-gitops-repos-into-azure-repos"></a>Importuj repozytoria aplikacji i GitOps do Azure Repos

Zaimportuj [repozytorium aplikacji](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-cicd#application-repo) i [repozytorium GitOps](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-cicd#gitops-repo) do Azure Repos. Na potrzeby tego samouczka Użyj następujących przykładowych repozytoriów:

* **łuk-cicd-Demonstracja-src** repozytorium aplikacji
   * Adres URL: https://github.com/Azure/arc-cicd-demo-src
   * Zawiera przykład aplikacji do głosowania platformy Azure, która zostanie wdrożona za pomocą GitOps.
* **łuk-cicd-Demonstracja-gitops** Repozytorium GitOps
   * Adres URL: https://github.com/Azure/arc-cicd-demo-gitops
   * Działa jako podstawa dla zasobów klastra, które są przechowywane w aplikacji do głosowania platformy Azure.

Dowiedz się więcej o [importowaniu repozytoriów usługi git](https://docs.microsoft.com/azure/devops/repos/git/import-git-repository).

>[!NOTE]
> Importowanie i używanie dwóch oddzielnych repozytoriów dla repozytoriów aplikacji i GitOps może poprawić bezpieczeństwo i prostotę. Uprawnienia i widoczność repozytoriów aplikacji i GitOps można dostrajać pojedynczo.
> Na przykład administrator klastra może nie znaleźć zmian w kodzie aplikacji, które są istotne dla żądanego stanu klastra. Z drugiej strony deweloperzy aplikacji nie muszą znać określonych parametrów dla każdego środowiska — zestaw wartości testowych, które zapewniają pokrycie parametrów, może być wystarczający.

## <a name="connect-the-gitops-repo"></a>Łączenie repozytorium GitOps

Aby ciągle wdrażać aplikację, Połącz repozytorium aplikacji z klastrem za pomocą GitOps. Repozytorium **cicd-Demonstracja gitops** gitops zawiera podstawowe zasoby, dzięki którym aplikacja jest uruchamiana w klastrze **Arc-cicd-Cluster** .

Początkowe repozytorium GitOps zawiera tylko [manifest](https://github.com/Azure/arc-cicd-demo-gitops/blob/master/arc-cicd-cluster/manifests/namespaces.yml) , który tworzy przestrzenie nazw **dev** i **Stage** odpowiadające środowisku wdrażania.

Utworzone połączenie GitOps będzie automatycznie:
* Zsynchronizuj manifesty w katalogu manifestu.
* Aktualizowanie stanu klastra.

Przepływ pracy ciągłej integracji/ciągłego zapełni katalog manifestu dodatkowymi manifestami w celu wdrożenia aplikacji.


1. [Utwórz nowe połączenie usługi GitOps](https://docs.microsoft.com/azure/azure-arc/kubernetes/tutorial-use-gitops-connected-cluster) z nowo zaimportowanym repozytorium **Arc-cicd-Demonstracja GitOps** w Azure Repos.

   ```azurecli
   az k8sconfiguration create \
      --name cluster-config \
      --cluster-name arc-cicd-cluster \
      --resource-group myResourceGroup \
      --operator-instance-name cluster-config \
      --operator-namespace cluster-config \
      --repository-url https://dev.azure.com/<Your organization>/arc-cicd-demo-gitops \
      --https-user <Azure Repos username> \
      --https-key <Azure Repos PAT token> \
      --scope cluster \
      --cluster-type connectedClusters \
      --operator-params='--git-readonly --git-path=arc-cicd-cluster/manifests'
   ```

1. Upewnij się, że strumień używa *tylko* `arc-cicd-cluster/manifests` katalogu jako ścieżki podstawowej. Zdefiniuj ścieżkę przy użyciu następującego parametru operatora:

   `--git-path=arc-cicd-cluster/manifests`

   > [!NOTE]
   > Jeśli używasz parametrów połączenia HTTPS i występują problemy z połączeniem, upewnij się, że pominięto prefiks nazwy użytkownika w adresie URL. Na przykład, `https://alice@dev.azure.com/contoso/arc-cicd-demo-gitops` musi zostać `alice@` usunięty. Na `--https-user` przykład określa użytkownika `--https-user alice` .

1. Sprawdź stan wdrożenia w Azure Portal.
   * Jeśli to się powiedzie, zobaczysz zarówno `dev` , jak i `stage` przestrzeń nazw utworzoną w klastrze.

## <a name="import-the-cicd-pipelines"></a>Importowanie potoków ciągłej integracji/ciągłego wdrażania

Po zsynchronizowaniu połączenia GitOps należy zaimportować potoki ciągłej integracji/ciągłego wdrażania, które tworzą manifesty.

Repozytorium aplikacji zawiera `.pipeline` folder z potokami, które będą używane dla żądań ściągnięcia, ci i CD. Zaimportuj i Zmień nazwy trzech potoków, które znajdują się w przykładowym repozytorium:

| Nazwa pliku potoku | Opis |
| ------------- | ------------- |
| [`.pipelines/az-vote-pr-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-pr-pipeline.yaml)  | Potok aplikacji w ramach żądania ściągnięcia o nazwie **Arc-cicd-demonstracyjny-src PR** |
| [`.pipelines/az-vote-ci-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-ci-pipeline.yaml) | Potok CI aplikacji o nazwie **Arc-cicd-demonstracji-src Ci** |
| [`.pipelines/az-vote-cd-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-cd-pipeline.yaml) | Potok CD aplikacji o nazwie **Arc-cicd-demo-src CD** |



## <a name="connect-your-acr"></a>Łączenie ACR
Zarówno potoki, jak i klaster będą korzystali z ACR do przechowywania i pobierania obrazów platformy Docker.

### <a name="connect-acr-to-azure-devops"></a>Łączenie ACR z usługą Azure DevOps
W trakcie procesu CI można wdrożyć kontenery aplikacji w rejestrze. Zacznij od utworzenia połączenia usługi platformy Azure:

1. W usłudze Azure DevOps Otwórz stronę **połączenia usługi** na stronie Ustawienia projektu. W programie TFS Otwórz stronę **usług** na ikonie **ustawień** na górnym pasku menu.
2. Wybierz pozycję **+ nowe połączenie usługi** i wybierz wymagany typ połączenia z usługą.
3. Wprowadź parametry połączenia z usługą. W tym samouczku:
   * Nazwij połączenie z usługą **Arc-demonstracyjne-ACR**. 
   * Wybierz **pozycję Grupa zasobów.**
4. Wybierz **uprawnienie Udziel dostępu do wszystkich potoków**. 
   * Ta opcja autoryzuje pliki potoku YAML dla połączeń usług. 
5. Wybierz **przycisk OK** , aby utworzyć połączenie.

### <a name="connect-acr-to-kubernetes"></a>Połącz ACR z Kubernetes
Zezwól klastrowi Kubernetes na ściąganie obrazów z ACR. Jeśli jest to prywatne, wymagane jest uwierzytelnienie.

#### <a name="connect-acr-to-existing-aks-clusters"></a>Łączenie ACR z istniejącymi klastrami AKS

Zintegruj istniejący ACR z istniejącymi klastrami AKS przy użyciu następującego polecenia:

```azurecli
az aks update -n arc-cicd-cluster -g myResourceGroup --attach-acr arc-demo-acr
```

#### <a name="create-an-image-pull-secret"></a>Utwórz klucz tajny ściągania obrazu

Aby połączyć klastry inne niż AKS i lokalne z ACR, Utwórz klucz tajny obrazu. Kubernetes używa kluczy tajnych ściągania obrazów do przechowywania informacji potrzebnych do uwierzytelnienia rejestru.

Utwórz klucz tajny obrazu za pomocą następującego `kubectl` polecenia. Powtórz dla obu `dev` `stage` przestrzeni nazw i.
```console
kubectl create secret docker-registry <secret-name> \
    --namespace <namespace> \
    --docker-server=<container-registry-name>.azurecr.io \
    --docker-username=<service-principal-ID> \
    --docker-password=<service-principal-password>
```

> [!TIP]
> Aby uniknąć konieczności ustawiania imagePullSecret dla każdego z nich, rozważ dodanie imagePullSecret do konta usługi w `dev` `stage` przestrzeniach nazw i. Aby uzyskać więcej informacji, zobacz [samouczek Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#add-imagepullsecrets-to-a-service-account) .

## <a name="create-environment-variable-groups"></a>Tworzenie grup zmiennych środowiskowych

### <a name="app-repo-variable-group"></a>Grupa zmiennych repozytorium aplikacji
[Utwórz grupę zmiennych](https://docs.microsoft.com/azure/devops/pipelines/library/variable-groups) o nazwie **AZ-głosowa-App-dev**. Ustaw następujące wartości:

| Zmienna | Wartość |
| -------- | ----- |
| AZ_ACR_NAME | (na przykład wystąpienie ACR. azurearctest.azurecr.io) |
| AZURE_SUBSCRIPTION | (połączenie usługi platformy Azure, które powinno mieć wartość **Arc-demonstracyjnej — ACR** z wcześniejszej części samouczka) |
| AZURE_VOTE_IMAGE_REPO | Pełna ścieżka do repozytorium aplikacji do głosowania platformy Azure, na przykład azurearctest.azurecr.io/azvote |
| ENVIRONMENT_NAME | Deweloperskie |
| MANIFESTS_BRANCH | `master` |
| MANIFESTS_REPO | Parametry połączenia git dla repozytorium GitOps |
| Osobisty token dostępu | [Utworzony token](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?#create-a-pat) źródła danych z uprawnieniami do odczytu i zapisu. Zapisz go do późniejszego użycia podczas tworzenia `stage` grupy zmiennych. |
| SRC_FOLDER | `azure-vote` | 
| TARGET_CLUSTER | `arc-cicd-cluster` |
| TARGET_NAMESPACE | `dev` |

> [!IMPORTANT]
> Oznacz swoją wartość jako typ wpisu tajnego. W aplikacjach należy rozważyć łączenie wpisów tajnych z [magazynu kluczy platformy Azure](https://docs.microsoft.com/azure/devops/pipelines/library/variable-groups#link-secrets-from-an-azure-key-vault).
>
### <a name="stage-environment-variable-group"></a>Grupa zmiennych środowiskowych etapów

1. Sklonuj grupę zmiennych **AZ-głosowa-App-dev** .
1. Zmień nazwę na **AZ-głosowa-App-Stage**.
1. Upewnij się, że następujące wartości dla odpowiednich zmiennych:

| Zmienna | Wartość |
| -------- | ----- |
| ENVIRONMENT_NAME | Etap |
| TARGET_NAMESPACE | `stage` |

Teraz można przystąpić do wdrażania w `dev` `stage` środowiskach i.

## <a name="deploy-the-dev-environment-for-the-first-time"></a>Wdróż środowisko deweloperskie po raz pierwszy
Po utworzeniu potoków ciągłej integracji i ciągłego uruchamiania potoku CI, aby wdrożyć aplikację po raz pierwszy.

### <a name="ci-pipeline"></a>Potok elementu konfiguracji

Podczas początkowego uruchomienia potoku ciągłej konfiguracji może wystąpić błąd autoryzacji zasobu podczas odczytywania nazwy połączenia usługi.
1. Sprawdź, czy zmienna, do której uzyskuje dostęp, jest AZURE_SUBSCRIPTION.
1. Autoryzuj użycie.
1. Uruchom ponownie potok.

Potok CI:
* Zapewnia, że zmiana aplikacji przekaże wszystkie zautomatyzowane sprawdzanie jakości dla wdrożenia.
* Wykonuje wszelkie dodatkowe walidacje, których nie można ukończyć w potoku żądania ściągnięcia.
    * Określony dla GitOps, potok publikuje także artefakty dla zatwierdzenia, które zostaną wdrożone przez potok CD.
* Weryfikuje, czy obraz platformy Docker został zmieniony, a nowy obraz jest wypychany.

### <a name="cd-pipeline"></a>Potok CD
Pomyślne uruchomienie potoku CI wyzwala potok CD, aby ukończyć proces wdrażania. Wdrożenie zostanie wdrożone w każdym środowisku przyrostowo.

> [!TIP]
> Jeśli potok CD nie jest automatycznie wyzwalany:
> 1. Sprawdź, czy nazwa jest zgodna z wyzwalaczem gałęzi w [`.pipelines/az-vote-cd-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-cd-pipeline.yaml)
>    * Powinna ona mieć wartość `arc-cicd-demo-src CI`.
> 1. Uruchom ponownie potok elementu konfiguracji.

Po wygenerowaniu szablonu i manifestu GitOps repozytorium, potok CD utworzy zatwierdzenie, wypchnięcie i utworzenie żądania ściągnięcia do zatwierdzenia.
1. Otwórz link PR określony w `Create PR` danych wyjściowych zadania.
1. Sprawdź zmiany w repozytorium GitOps. Powinien zostać wyświetlony następujący ekran:
   * Zmiany szablonu Helm wysokiego poziomu.
   * Manifesty Kubernetes niskiego poziomu, które pokazują bazowe zmiany w żądanym stanie. Strumień wdraża te manifesty.
1. Jeśli wszystko wygląda dobrze, Zatwierdź i Ukończ żądanie ściągnięcia.

1. Po kilku minutach strumień pobiera zmianę i uruchamia wdrożenie.
1. Przekazują port lokalnie przy użyciu `kubectl` i upewnij się, że aplikacja działa prawidłowo przy użyciu:

   `kubectl port-forward -n dev svc/azure-vote-front 8080:80`

1. Wyświetlanie aplikacji do głosowania platformy Azure w przeglądarce `http://localhost:8080/` .

1. Zagłosuj na ulubione i przygotuj się, aby wprowadzić zmiany w aplikacji.

## <a name="set-up-environment-approvals"></a>Konfigurowanie zatwierdzeń środowiska
Po wdrożeniu aplikacji nie można wprowadzać zmian w kodzie ani w szablonach, ale można również przypadkowo przełączyć klaster w stan nieprawidłowy.

Jeśli środowisko deweloperskie ujawnia przerwanie po wdrożeniu, zachowaj je w nowszych środowiskach przy użyciu zatwierdzeń środowiska.

1. W projekcie usługi Azure DevOps przejdź do środowiska, które musi być chronione.
1. Przejdź do **zatwierdzenia i czeków** dla zasobu.
1. Wybierz przycisk **Utwórz**.
1. Podaj osoby zatwierdzające i opcjonalną wiadomość.
1. Wybierz pozycję **Utwórz** ponownie, aby ukończyć Dodawanie ręcznego sprawdzania zatwierdzania.

Aby uzyskać więcej informacji, zobacz samouczek [Definiuj zatwierdzenie i sprawdzanie](https://docs.microsoft.com/azure/devops/pipelines/process/approvals) .

Po następnym uruchomieniu potoku dysku CD potok zostanie wstrzymany po utworzeniu żądania ściągnięcia GitOps. Sprawdź, czy zmiana została prawidłowo zsynchronizowana i przeszedł podstawową funkcję. Zatwierdź sprawdzenie z potoku, aby umożliwić zmianę przepływu zmian do następnego środowiska.

## <a name="make-an-application-change"></a>Zmiana aplikacji

Z tym zestawem bazowym szablonów i manifestów reprezentujących stan w klastrze zostanie wprowadzona niewielka zmiana w aplikacji.

1. W repozytorium **Arc-cicd-demo-src** Edytuj [`azure-vote/src/azure-vote-front/config_file.cfg`](https://github.com/Azure/arc-cicd-demo-src/blob/master/azure-vote/src/azure-vote-front/config_file.cfg) plik.

2. Ponieważ "koty vs psy" nie otrzymuje wystarczającej liczby głosów, Zmień ją na "karty vs Spaces", aby zwiększyć liczbę głosów.

3. Zatwierdź zmianę w nowej gałęzi, Wypchnij ją i Utwórz żądanie ściągnięcia.
   * Jest to typowy przepływ dla deweloperów, który uruchomi cykl życia CI/CD.

## <a name="pr-validation-pipeline"></a>Potok weryfikacji żądania ściągnięcia

Potok żądania ściągnięcia jest pierwszym wierszem obrony przed błędną zmianą. Standardowe testy jakości kodu aplikacji obejmują Zaznaczanie błędów i analizę statyczną. Z perspektywy GitOps należy również zapewnić taką samą jakość dla powstającej infrastruktury, która ma zostać wdrożona.

Wykresy pliku dockerfile i Helm aplikacji mogą używać Zaznaczanie błędów w podobny sposób do aplikacji.

Znaleziono błędy w zakresie Zaznaczanie błędów z:
* Niepoprawnie sformatowane pliki YAML, do
* Sugestie dotyczące najlepszych rozwiązań, takie jak Ustawianie limitów procesora i pamięci dla aplikacji.

> [!NOTE]
> Aby uzyskać najlepsze pokrycie z Helm Zaznaczanie błędów w prawdziwej aplikacji, należy zastąpić wartości, które są w sposób zbliżony do tych używanych w realnym środowisku.

Błędy znalezione podczas wykonywania potoku pojawiają się w sekcji wyniki testu w przebiegu. W tym miejscu można wykonać następujące czynności:
* Śledź przydatne statystyki dotyczące typów błędów.
* Znajdź pierwsze zatwierdzenie, na którym zostały wykryte.
* Styl śledzenia stosu łączy z sekcjami kodu, które spowodowały błąd.

Po zakończeniu przebiegu potoku masz pewność, że jakość kodu aplikacji i szablon, który zostanie wdrożony. Teraz możesz zatwierdzić i zakończyć żądanie ściągnięcia. Przed wyzwoleniem potoku dysku CD zostanie ponownie uruchomiony element konfiguracji i manifesty.

> [!TIP]
> W rzeczywistym środowisku nie zapomnij ustawić zasad gałęzi, aby upewnić się, że żądanie ściągnięcia przekazuje kontrolę jakości. Aby uzyskać więcej informacji, zobacz artykuł [Ustawianie zasad gałęzi](https://docs.microsoft.com/azure/devops/repos/git/branch-policies) .

## <a name="cd-process-approvals"></a>Zatwierdzenia procesów dysku CD

Pomyślne uruchomienie potoku CI wyzwala potok CD, aby ukończyć proces wdrażania. Podobnie jak w przypadku pierwszego uruchomienia potoku dysku CD, należy wdrożyć każde środowisko przyrostowo. Tym razem potok wymaga zatwierdzenia każdego środowiska wdrażania.

1. Zatwierdź wdrożenie w `dev` środowisku.
1. Po wygenerowaniu szablonu i manifestu GitOps repozytorium, potok CD utworzy zatwierdzenie, wypchnięcie i utworzenie żądania ściągnięcia do zatwierdzenia.
1. Otwórz link PR określony w zadaniu.
1. Sprawdź zmiany w repozytorium GitOps. Powinien zostać wyświetlony następujący ekran:
   * Zmiany szablonu Helm wysokiego poziomu.
   * Manifesty Kubernetes niskiego poziomu, które pokazują bazowe zmiany w żądanym stanie.
1. Jeśli wszystko wygląda dobrze, Zatwierdź i Ukończ żądanie ściągnięcia.
1. Zaczekaj na zakończenie wdrażania.
1. Jako podstawowy test dymu, przejdź do strony aplikacji i sprawdź, czy aplikacja do głosowania wyświetla teraz tabulatory a spacje.
   * Przekazują port lokalnie przy użyciu `kubectl` i upewnij się, że aplikacja działa prawidłowo przy użyciu: `kubectl port-forward -n dev svc/azure-vote-front 8080:80`
   * Wyświetl aplikację do głosowania platformy Azure w przeglądarce pod adresem http://localhost:8080/ i sprawdź, czy opcje głosowania zostały zmienione na karty vs Spaces. 
1. Powtórz kroki 1-7 dla `stage` środowiska.

Wdrożenie zostało zakończone. Spowoduje to zakończenie przepływu pracy ciągłej integracji/ciągłego wdrażania.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń wszystkie zasoby z następującymi krokami:

1. Usuń połączenie z konfiguracją usługi Azure Arc GitOps:
   ```azurecli
   az k8sconfiguration delete \
   --name cluster-config \
   --cluster-name arc-cicd-cluster \
   --resource-group myResourceGroup \
   --cluster-type connectedClusters
   ```

2. Usuń `dev` przestrzeń nazw:
   * `kubectl delete namespace dev`

3. Usuń `stage` przestrzeń nazw:
   * `kubectl delete namespace stage`

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurowano pełny przepływ pracy ciągłej integracji/ciągłego wdrażania, który implementuje DevOps z tworzenia aplikacji przez wdrożenie. Zmiany w aplikacji automatycznie wyzwalają weryfikację i wdrożenie, które są warunkowo zatwierdzeń ręcznych.

Przejdź do naszego artykułu koncepcyjnego, aby dowiedzieć się więcej na temat GitOps i konfiguracji z włączonym usługą Azure Arc Kubernetes.

> [!div class="nextstepaction"]
> [Przepływ pracy ciągłej integracji/ciągłego wdrażania za pomocą GitOps — usługa Azure Arc Kubernetes](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-cicd)
