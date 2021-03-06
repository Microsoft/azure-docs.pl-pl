---
title: 'Samouczek: implementowanie funkcji CI/CD za pomocą usługi GitOps Azure Arc klastrów Kubernetes z włączoną obsługą'
description: Ten samouczek zawiera instrukcji konfigurowania rozwiązania ci/CD przy użyciu usługi GitOps Azure Arc klastrów Kubernetes z włączoną obsługą. Koncepcyjne zastosowanie tego przepływu pracy można znaleźć w artykule Ci/CD Workflow using GitOps - Azure Arc enabled Kubernetes (Przepływ pracy ci/CD korzystający z usługi GitOps — Azure Arc usługi Kubernetes).
author: tcare
ms.author: tcare
ms.service: azure-arc
ms.topic: tutorial
ms.date: 03/03/2021
ms.custom: template-tutorial, devx-track-azurecli
ms.openlocfilehash: 9a228ce6f8b18afb77b656765abbad0bb4ae877f
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589146"
---
# <a name="tutorial-implement-cicd-with-gitops-using-azure-arc-enabled-kubernetes-clusters"></a>Samouczek: implementowanie funkcji CI/CD za pomocą usługi GitOps Azure Arc klastrów Kubernetes z włączoną obsługą


W tym samouczku skonfigurujesz rozwiązanie ci/CD przy użyciu usługi GitOps z włączonymi Azure Arc Kubernetes. Korzystając z przykładowej aplikacji Do głosowania na platformie Azure, możesz:

> [!div class="checklist"]
> * Utwórz Azure Arc Kubernetes z włączoną obsługą.
> * Połącz aplikację i repozytoria GitOps z Azure Repos.
> * Zaimportuj potoki ci/CD.
> * Połącz swoją Azure Container Registry (ACR) z Azure DevOps i Kubernetes.
> * Tworzenie grup zmiennych środowiskowych.
> * Wdrażanie środowisk `dev` `stage` i .
> * Przetestuj środowiska aplikacji.

Jeśli nie masz subskrypcji ™ Azure, przed rozpoczęciem [](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) utwórz bezpłatne konto.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym samouczku założono znajomość Azure DevOps, Azure Repos i potoków oraz interfejsu wiersza polecenia platformy Azure.

* Zaloguj się [do Azure DevOps Services](https://dev.azure.com/).
* Ukończ poprzedni [samouczek,](./tutorial-use-gitops-connected-cluster.md) aby dowiedzieć się, jak wdrożyć usługę GitOps dla środowiska ci/cd.
* Poznaj [korzyści i architekturę](./conceptual-configurations.md) tej funkcji.
* Sprawdź, czy masz:
  * Połączony [klaster Azure Arc Kubernetes o](./quickstart-connect-cluster.md#connect-an-existing-kubernetes-cluster) nazwie **arc-cicd-cluster.**
  * Połączona usługa Azure Container Registry (ACR) z integracją usługi [AKS](../../aks/cluster-container-registry-integration.md) lub uwierzytelnianiem klastra bez [usługi AKS.](../../container-registry/container-registry-auth-kubernetes.md)
  * Uprawnienia "Administrator kompilacji" i "Administrator projektu" [dla Azure Repos](/azure/devops/repos/get-started/what-is-repos) [i Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started).
* Zainstaluj następujące Azure Arc interfejsu wiersza polecenia kubernetes w wersjach >= 1.0.0:

  ```azurecli
  az extension add --name connectedk8s
  az extension add --name k8sconfiguration
  ```
  * Aby zaktualizować te rozszerzenia do najnowszej wersji, uruchom następujące polecenia:

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8sconfiguration
    ```

## <a name="import-application-and-gitops-repos-into-azure-repos"></a>Importowanie aplikacji i repozytoriów GitOps do Azure Repos

[Zaimportuj repozytorium aplikacji](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-ci-cd#application-repo) i [repozytorium GitOps](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-ci-cd#gitops-repo) do Azure Repos. W tym samouczku użyj następujących przykładowych repos:

* **arc-cicd-demo-src application repo (repo aplikacji arc-cicd-demo-src)**
   * Adres url: https://github.com/Azure/arc-cicd-demo-src
   * Zawiera przykład aplikacji Azure Vote, która zostanie wdrożona przy użyciu usługi GitOps.
* **arc-cicd-demo-gitops** Repozytorium GitOps
   * Adres url: https://github.com/Azure/arc-cicd-demo-gitops
   * Działa jako podstawa dla zasobów klastra, w których znajduje się aplikacja Azure Vote.

Dowiedz się więcej [o importowaniu repozytoriów Git.](/azure/devops/repos/git/import-git-repository)

>[!NOTE]
> Importowanie i używanie dwóch oddzielnych repozytoriów dla aplikacji i repozytoriów GitOps może zwiększyć bezpieczeństwo i prostotę. Uprawnienia aplikacji i repozytoriów GitOps można dostroić indywidualnie.
> Na przykład administrator klastra może nie znaleźć zmian w kodzie aplikacji odpowiednich dla żądanego stanu klastra. Z kolei deweloper aplikacji nie musi znać konkretnych parametrów dla każdego środowiska — zestaw wartości testowych, które zapewniają pokrycie parametrów, może być wystarczający.

## <a name="connect-the-gitops-repo"></a>Łączenie repozytorium GitOps

Aby stale wdrażać aplikację, połącz repozytorium aplikacji z klastrem przy użyciu usługi GitOps. Repozytorium **GitOps arc-cicd-demo-gitops** zawiera podstawowe zasoby służące do uruchamiania aplikacji w klastrze **arc-cicd-cluster.**

Początkowe repozytorium GitOps zawiera tylko [manifest,](https://github.com/Azure/arc-cicd-demo-gitops/blob/master/arc-cicd-cluster/manifests/namespaces.yml)  który tworzy **przestrzenie** nazw tworzenia i wdrażania odpowiadające środowiskom wdrażania.

Tworzone połączenie GitOps będzie automatycznie:
* Zsynchronizuj manifesty w katalogu manifestu.
* Zaktualizuj stan klastra.

Przepływ pracy ci/CD wypełni katalog manifestu dodatkowymi manifestami w celu wdrożenia aplikacji.


1. [Utwórz nowe połączenie GitOps z](./tutorial-use-gitops-connected-cluster.md) nowo zaimportowane repozytorium **arc-cicd-demo-gitops** w Azure Repos.

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

1. Upewnij się, że *strumień używa* `arc-cicd-cluster/manifests` tylko katalogu jako ścieżki podstawowej. Zdefiniuj ścieżkę przy użyciu następującego parametru operatora:

   `--git-path=arc-cicd-cluster/manifests`

   > [!NOTE]
   > Jeśli używasz parametrów połączenia HTTPS i występują problemy z połączeniem, upewnij się, że pominięto prefiks nazwy użytkownika w adresie URL. Na przykład `https://alice@dev.azure.com/contoso/arc-cicd-demo-gitops` musi zostać `alice@` usunięta. Zamiast `--https-user` tego określa użytkownika, na przykład `--https-user alice` .

1. Sprawdź stan wdrożenia w programie Azure Portal.
   * Jeśli to się powiedzie, zobaczysz przestrzenie nazw `dev` i `stage` utworzone w klastrze.

## <a name="import-the-cicd-pipelines"></a>Importowanie potoków ciasnych/cd

Po zsynchronizowaniu połączenia z usługą GitOps należy zaimportować potoki ci/CD, które tworzą manifesty.

Repo aplikacji zawiera folder z potokami, których będziesz używać na użytek `.pipeline` PRs, CI i CD. Zaimportuj i zmień nazwy trzech potoków podanych w przykładowym repo:

| Nazwa pliku potoku | Opis |
| ------------- | ------------- |
| [`.pipelines/az-vote-pr-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-pr-pipeline.yaml)  | Potok application PR o nazwie **arc-cicd-demo-src PR** |
| [`.pipelines/az-vote-ci-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-ci-pipeline.yaml) | Potok ci aplikacji o nazwie **arc-cicd-demo-src CI** |
| [`.pipelines/az-vote-cd-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-cd-pipeline.yaml) | Potok cd aplikacji o nazwie **arc-cicd-demo-src CD** |



## <a name="connect-your-acr"></a>Łączenie z rozpoznawaniem znaków
Zarówno potoki, jak i klaster będą przechowywać i pobierać obrazy platformy Docker przy użyciu usługi ACR.

### <a name="connect-acr-to-azure-devops"></a>Łączenie acr z Azure DevOps
Podczas procesu ciasnych informacji wdrożysz kontenery aplikacji w rejestrze. Rozpocznij od utworzenia połączenia z usługą platformy Azure:

1. W Azure DevOps otwórz stronę **Połączenia z** usługą na stronie ustawień projektu. W programie TFS otwórz **stronę Usługi** za pomocą **ikony ustawień** na górnym pasku menu.
2. Wybierz **pozycję + Nowe połączenie z** usługą i wybierz odpowiedni typ połączenia z usługą.
3. Wypełnij parametry połączenia z usługą. W tym samouczku:
   * Nadaj połączeniu z **usługą nazwę arc-demo-acr.** 
   * Wybierz **grupę zasobów myResourceGroup.**
4. Wybierz pozycję **U przyznaj uprawnienie dostępu do wszystkich potoków.** 
   * Ta opcja autoryzuje pliki potoku YAML dla połączeń usług. 
5. Wybierz **przycisk OK,** aby utworzyć połączenie.

### <a name="connect-acr-to-kubernetes"></a>Łączenie acr z platformą Kubernetes
Włącz klaster Kubernetes, aby ściągał obrazy z usługi ACR. Jeśli jest to prywatne, wymagane będzie uwierzytelnianie.

#### <a name="connect-acr-to-existing-aks-clusters"></a>Łączenie usługi ACR z istniejącymi klastrami usługi AKS

Zintegruj istniejącą usługę ACR z istniejącymi klastrami usługi AKS przy użyciu następującego polecenia:

```azurecli
az aks update -n arc-cicd-cluster -g myResourceGroup --attach-acr arc-demo-acr
```

#### <a name="create-an-image-pull-secret"></a>Tworzenie tajnego ściągnięć obrazu

Aby połączyć klastry inne niż AKS i klastry lokalne z twoim kluczem tajnym ściągania obrazu, utwórz klucz tajny ściągania obrazu. Kubernetes używa wpisów tajnych ściągania obrazów do przechowywania informacji wymaganych do uwierzytelnienia rejestru.

Utwórz klucz tajny ściągania obrazu za pomocą następującego `kubectl` polecenia. Powtórz dla przestrzeni nazw `dev` `stage` i .
```console
kubectl create secret docker-registry <secret-name> \
    --namespace <namespace> \
    --docker-server=<container-registry-name>.azurecr.io \
    --docker-username=<service-principal-ID> \
    --docker-password=<service-principal-password>
```

Aby uniknąć konieczności ustawienia elementu imagePullSecret dla każdego zasobnika, rozważ dodanie elementu imagePullSecret do konta usługi w `dev` `stage` przestrzeniach nazw i . Zobacz [samouczek kubernetes,](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#add-imagepullsecrets-to-a-service-account) aby uzyskać więcej informacji.

## <a name="create-environment-variable-groups"></a>Tworzenie grup zmiennych środowiskowych

### <a name="app-repo-variable-group"></a>Grupa zmiennych w repo aplikacji
[Utwórz grupę zmiennych o](/azure/devops/pipelines/library/variable-groups) **nazwie az-vote-app-dev.** Ustaw następujące wartości:

| Zmienna | Wartość |
| -------- | ----- |
| AZ_ACR_NAME | (na przykład wystąpienie usługi ACR. azurearctest.azurecr.io) |
| AZURE_SUBSCRIPTION | (połączenie usługi platformy Azure, które powinno być **arc-demo-acr** z wcześniejszej części tego samouczka) |
| AZURE_VOTE_IMAGE_REPO | Pełna ścieżka do repo aplikacji Azure Vote, na przykład azurearctest.azurecr.io/azvote |
| ENVIRONMENT_NAME | Deweloperskie |
| MANIFESTS_BRANCH | `master` |
| MANIFESTS_REPO | Ciąg połączenia usługi Git dla repozytorium GitOps |
| Osobisty token dostępu | Utworzony [token tokenu dostępu z](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate#create-a-pat) uprawnieniami do odczytu/zapisu źródła. Zapisz go do użycia później podczas tworzenia `stage` grupy zmiennych. |
| SRC_FOLDER | `azure-vote` | 
| TARGET_CLUSTER | `arc-cicd-cluster` |
| TARGET_NAMESPACE | `dev` |

> [!IMPORTANT]
> Oznacz swój pat pat jako typ wpisów tajnych. W aplikacjach rozważ połączenie wpisów tajnych z [usługi Azure KeyVault.](/azure/devops/pipelines/library/variable-groups#link-secrets-from-an-azure-key-vault)
>
### <a name="stage-environment-variable-group"></a>Grupa zmiennych środowiskowych etapu

1. **Sklonuj grupę zmiennych az-vote-app-dev.**
1. Zmień nazwę na **az-vote-app-stage.**
1. Upewnij się, że dla odpowiednich zmiennych są podane następujące wartości:

| Zmienna | Wartość |
| -------- | ----- |
| ENVIRONMENT_NAME | Etap |
| TARGET_NAMESPACE | `stage` |

Teraz możesz rozpocząć wdrażanie w `dev` środowiskach `stage` i .

## <a name="deploy-the-dev-environment-for-the-first-time"></a>Wdrażanie środowiska dewelopera po raz pierwszy
Po utworzeniu potoków ci i CD uruchom potok ci, aby wdrożyć aplikację po raz pierwszy.

### <a name="ci-pipeline"></a>Potok ci

Podczas początkowego uruchomienia potoku ci może wystąpić błąd autoryzacji zasobów podczas odczytywania nazwy połączenia usługi.
1. Sprawdź, czy zmienna, do której uzyskiwany jest AZURE_SUBSCRIPTION.
1. Autoryzowanie użycia.
1. Ponownie uruchomić potok.

Potok ci:
* Zapewnia, że zmiana aplikacji pomyślnie przejdzie wszystkie zautomatyzowane kontrole jakości dla wdrożenia.
* Czy wszelkie dodatkowe walidacji, których nie można ukończyć w potoku pr.
    * Specyficzny dla usługi GitOps potok publikuje również artefakty dla zatwierdzenia, które zostanie wdrożone przez potok cd.
* Sprawdza, czy obraz platformy Docker został zmieniony, a nowy obraz jest wypychany.

### <a name="cd-pipeline"></a>Potok cd
Pomyślne uruchomienie potoku ci wyzwala potok wdrażania w celu ukończenia procesu wdrażania. W każdym środowisku będziesz wdrażać przyrostowo.

> [!TIP]
> Jeśli potok cd nie jest wyzwalany automatycznie:
> 1. Sprawdź, czy nazwa jest odpowiada wyzwalaczowi gałęzi w [`.pipelines/az-vote-cd-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-cd-pipeline.yaml)
>    * Powinna ona mieć wartość `arc-cicd-demo-src CI`.
> 1. Ponownie uruchomić potok ci.

Po wygenerowaniu szablonu i manifestu zmian w repozytorium GitOps potok cd utworzy zatwierdzenie, wypchnie je i utworzy do zatwierdzenia.
1. Otwórz link do pr yzyka podany w danych `Create PR` wyjściowych zadania.
1. Sprawdź zmiany w repozytorium GitOps. Powinien zostać wyświetlony następujący ekran:
   * Zmiany szablonów usługi Helm wysokiego poziomu.
   * Manifesty kubernetes niskiego poziomu, które pokazują podstawowe zmiany żądanego stanu. Strumień wdraża te manifesty.
1. Jeśli wszystko wygląda dobrze, zatwierdź i ukończ jego ściągnięcie.

1. Po kilku minutach strumień Flux przejmuje zmianę i rozpoczyna wdrażanie.
1. Przekaż port lokalnie przy użyciu `kubectl` funkcji i upewnij się, że aplikacja działa prawidłowo, używając:

   `kubectl port-forward -n dev svc/azure-vote-front 8080:80`

1. Wyświetl aplikację Azure Vote w przeglądarce pod `http://localhost:8080/` adresem .

1. Zagłosuj na ulubione i przygotuj się do pewnych zmian w aplikacji.

## <a name="set-up-environment-approvals"></a>Konfigurowanie zatwierdzeń środowiska
Podczas wdrażania aplikacji można nie tylko wprowadzać zmiany w kodzie lub szablonach, ale także w niezamierzony sposób wprowadzić klaster w zły stan.

Jeśli środowisko dewelopera ujawni przerwę po wdrożeniu, nie wychodz do nowszych środowisk przy użyciu zatwierdzeń środowiska.

1. W Azure DevOps przejdź do środowiska, które musi być chronione.
1. Przejdź do **zatwierdzeń i kontroli** zasobu.
1. Wybierz przycisk **Utwórz**.
1. Podaj osoby zatwierdzające i opcjonalną wiadomość.
1. Wybierz **ponownie pozycję** Utwórz, aby zakończyć dodawanie ręcznego sprawdzania zatwierdzenia.

Aby uzyskać więcej informacji, zobacz [samouczek Definiowanie zatwierdzenia i](/azure/devops/pipelines/process/approvals) kontroli.

Przy następnym uruchamianiu potoku cd potok zostanie wstrzymany po utworzeniu repozytorium GitOps PR. Sprawdź, czy zmiana została prawidłowo zsynchronizowana, i czy podstawowa funkcjonalność została pomyślnie wywłaszowana. Zatwierdź sprawdzanie z potoku, aby pozwolić przepływowi zmiany na następne środowisko.

## <a name="make-an-application-change"></a>Zmienianie aplikacji

Dzięki temu podstawowemu zestawowi szablonów i manifestów reprezentujących stan w klastrze w aplikacji zostanie w niewielkim sposób w nim wywłaszsz.

1. W **repocie arc-cicd-demo-src** edytuj [`azure-vote/src/azure-vote-front/config_file.cfg`](https://github.com/Azure/arc-cicd-demo-src/blob/master/azure-vote/src/azure-vote-front/config_file.cfg) plik.

2. Ponieważ "Cats vs Dogs" nie ma wystarczającej liczby głosów, zmień ją na "Karty a spacje", aby podniesieć liczbę głosów.

3. Zatnij zmianę w nowej gałęzi, wypchnij ją i utwórz żądanie ściągnięcie.
   * Jest to typowy przepływ dewelopera, który rozpocznie cykl życia ciasnych/cd.

## <a name="pr-validation-pipeline"></a>Potok walidacji ściągnięć

Potok pr yzyka to pierwsza linia obrony przed błędną zmianą. Zwykle testy jakości kodu aplikacji obejmują linting i analizę statyczną. Z perspektywy usługi GitOps musisz również zapewnić taką samą jakość, aby wynikowa infrastruktura do wdrożenia.

Wykresy Dockerfile i Helm aplikacji mogą używać lintingu w sposób podobny do aplikacji.

Błędy znalezione podczas wyliczania błędów:
* Nieprawidłowo sformatowane pliki YAML, do
* Sugestie dotyczące najlepszych rozwiązań, takie jak ustawianie limitów procesora CPU i pamięci dla aplikacji.

> [!NOTE]
> Aby uzyskać najlepsze pokrycie z lintingu programu Helm w rzeczywistej aplikacji, należy zastąpić wartości, które są względnie podobne do tych używanych w rzeczywistym środowisku.

Błędy znalezione podczas wykonywania potoku są wyświetlane w sekcji wyników testu uruchomienia. W tym miejscu można wykonać następujące czynności:
* Śledzenie przydatnych statystyk dotyczących typów błędów.
* Znajdź pierwsze zatwierdzenie, w którym zostały wykryte.
* Styl śledzenia stosu zawiera linki do sekcji kodu, które spowodowały błąd.

Po zakończeniu uruchomienia potoku masz pewność co do jakości kodu aplikacji i szablonu, który go wdroży. Teraz możesz zatwierdzić i ukończyć to ściągnięcie. Ci zostanie ponownie uruchomiony, ponowne generowanie szablonów i manifestów, przed wyzwoleniem potoku cd.

> [!TIP]
> W rzeczywistym środowisku nie zapomnij ustawić zasad gałęzi, aby zagwarantować, że prysłowy przebieg kontroli jakości. Aby uzyskać więcej informacji, zobacz [artykuł Ustawianie zasad gałęzi.](/azure/devops/repos/git/branch-policies)

## <a name="cd-process-approvals"></a>Zatwierdzenia procesów cd

Pomyślne uruchomienie potoku ci powoduje wyzwolenie potoku wdrażania w celu ukończenia procesu wdrażania. Podobnie jak przy pierwszym potoku wdrażania, potok wdrażania będzie wdrażany przyrostowo w każdym środowisku. Tym razem potok wymaga zatwierdzenia każdego środowiska wdrażania.

1. Zatwierdź wdrożenie w `dev` środowisku.
1. Po wygenerowaniu szablonu i manifestu zmian w repozytorium GitOps potok cd utworzy zatwierdzenie, wypchnie je i utworzy do zatwierdzenia.
1. Otwórz link do pr yzyka podany w zadaniu.
1. Sprawdź zmiany w repozytorium GitOps. Powinien zostać wyświetlony następujący ekran:
   * Zmiany szablonów helm wysokiego poziomu.
   * Manifesty kubernetes niskiego poziomu, które pokazują podstawowe zmiany żądanego stanu.
1. Jeśli wszystko wygląda dobrze, zatwierdź i ukończ jego ściągnięcie.
1. Zaczekaj na zakończenie wdrażania.
1. W podstawowym teście palowym przejdź do strony aplikacji i sprawdź, czy aplikacja do głosowania wyświetla teraz karty i spacje.
   * Przekaż port lokalnie przy użyciu `kubectl` funkcji i upewnij się, że aplikacja działa prawidłowo, używając: `kubectl port-forward -n dev svc/azure-vote-front 8080:80`
   * Wyświetl aplikację Azure Vote w przeglądarce pod adres i sprawdź, czy opcje głosowania zostały zmienione na http://localhost:8080/ Karty i spacje. 
1. Powtórz kroki 1–7 dla `stage` środowiska.

Wdrożenie jest teraz ukończone. W ten sposób kończy się przepływ pracy ci/CD.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie zamierzasz w dalszym ciągu korzystać z tej aplikacji, usuń wszystkie zasoby, aby wykonać następujące czynności:

1. Usuń połączenie Azure Arc GitOps:
   ```azurecli
   az k8sconfiguration delete \
   --name cluster-config \
   --cluster-name arc-cicd-cluster \
   --resource-group myResourceGroup \
   --cluster-type connectedClusters
   ```

2. Usuń przestrzeń `dev` nazw:
   * `kubectl delete namespace dev`

3. Usuń przestrzeń `stage` nazw:
   * `kubectl delete namespace stage`

## <a name="next-steps"></a>Następne kroki

W tym samouczku został ustawiony pełny przepływ pracy ci/CD, który implementuje metodyki DevOps od tworzenia aplikacji po wdrożenie. Zmiany w aplikacji automatycznie wyzwalają walidację i wdrażanie, co jest bramą zatwierdzeń ręcznych.

Zapoznaj się z naszym artykułem koncepcyjnym, aby dowiedzieć się więcej o usłudze GitOps i konfiguracjach z Azure Arc obsługą rozwiązania Kubernetes.

> [!div class="nextstepaction"]
> [Przepływ pracy ci/cd przy użyciu usługi GitOps — Azure Arc kubernetes z włączoną obsługą](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-ci-cd)
