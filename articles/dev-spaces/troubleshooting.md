---
title: Rozwiązywanie problemów
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: troubleshooting
description: Dowiedz się, jak rozwiązywać typowe problemy podczas włączania i używania Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, Service siatk, Service siatk Routing, polecenia kubectl, k8s '
ms.openlocfilehash: d697a11f3087c31a49d9b88e99b18bab686a2b59
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90981065"
---
# <a name="azure-dev-spaces-troubleshooting"></a>Rozwiązywanie problemów Azure Dev Spaces

Ten przewodnik zawiera informacje o typowych problemach, które mogą wystąpić podczas korzystania z Azure Dev Spaces.

Jeśli wystąpi problem podczas korzystania z Azure Dev Spaces, Utwórz [problem w repozytorium Azure dev Spaces GitHub](https://github.com/Azure/dev-spaces/issues).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Aby bardziej efektywnie rozwiązywać problemy, może to pomóc w tworzeniu bardziej szczegółowych dzienników do przeglądu.

Dla programu Visual Studio ustaw wartość `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` zmiennej środowiskowej na 1. Aby zmienna środowiskowa zaczęła obowiązywać, należy ponownie uruchomić program Visual Studio. Po włączeniu szczegółowe dzienniki są zapisywane w `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` katalogu.

W interfejsie wiersza polecenia można wyprowadzić więcej informacji podczas wykonywania poleceń przy użyciu `--verbose` przełącznika. Więcej szczegółowych dzienników można także przeglądać w temacie `%TEMP%\Azure Dev Spaces` . Na komputerze Mac katalog *tymczasowy* można znaleźć, uruchamiając `echo $TMPDIR` z okna terminalu. Na komputerze z systemem Linux katalog *temp* jest zwykle `/tmp` . Ponadto sprawdź, czy rejestrowanie jest włączone w [pliku konfiguracji interfejsu wiersza polecenia platformy Azure](/cli/azure/azure-cli-configuration?view=azure-cli-latest#cli-configuration-values-and-environment-variables).

Azure Dev Spaces sprawdza się również najlepiej podczas debugowania pojedynczego wystąpienia lub pod. `azds.yaml`Plik zawiera ustawienie *replicaCount*, które wskazuje liczbę Kubernetes uruchomionych dla usługi. Jeśli zmienisz *replicaCount* w celu skonfigurowania aplikacji tak, aby uruchamiała wiele zasobników dla danej usługi, debuger dołącza do pierwszego pod, gdy zostanie wyświetlony alfabetycznie. Debuger dołącza się do innego, pod, kiedy pierwotne odzyskanie, prawdopodobnie wystąpiło nieoczekiwane zachowanie.

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>Typowe problemy występujące podczas włączania Azure Dev Spaces

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Błąd "nie można utworzyć kontrolera Azure Dev Spaces"

Ten błąd może pojawić się, gdy coś się nie udaje przy tworzeniu kontrolera. Jeśli jest to błąd przejściowy, Usuń i Utwórz ponownie kontroler, aby rozwiązać ten problem.

Możesz również spróbować usunąć kontroler:

```bash
azds remove -g <resource group name> -n <cluster name>
```

Użyj interfejsu wiersza polecenia Azure Dev Spaces, aby usunąć kontroler. Nie można usunąć kontrolera z programu Visual Studio. Nie można również zainstalować interfejsu wiersza polecenia Azure Dev Spaces w Azure Cloud Shell, dlatego nie można usunąć kontrolera z Azure Cloud Shell.

Jeśli nie masz zainstalowanego interfejsu wiersza polecenia Azure Dev Spaces, możesz go najpierw zainstalować przy użyciu następującego polecenie, a następnie usunąć kontroler:

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

### <a name="controller-create-failing-because-of-controller-name-length"></a>Tworzenie kontrolera kończy się niepowodzeniem z powodu długości nazwy kontrolera

Nazwa kontrolera Azure Dev Spaces nie może być dłuższa niż 31 znaków. Jeśli nazwa kontrolera przekracza 31 znaków po włączeniu funkcji miejsca do magazynowania w klastrze AKS lub utworzeniu kontrolera, zostanie wyświetlony komunikat o błędzie. Na przykład:

```console
Failed to create a Dev Spaces controller for cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev Spaces Controller name 'a-controller-name-that-is-way-too-long-aks-east-us' is invalid. Constraint(s) violated: Azure Dev Spaces Controller names can only be at most 31 characters long*
```

Aby rozwiązać ten problem, Utwórz kontroler z alternatywną nazwą. Na przykład:

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

### <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>Włączanie funkcji Spaces dev, gdy pule węzłów systemu Windows są dodawane do klastra AKS

Obecnie Azure Dev Spaces jest przeznaczony do uruchamiania tylko w przypadku systemów i węzłów systemu Linux. Jeśli masz klaster AKS z pulą węzłów systemu Windows, musisz upewnić się, że Azure Dev Spaces są planowane tylko w węzłach z systemem Linux. Jeśli Azure Dev Spaces pod zaplanowano uruchomienie w węźle systemu Windows, nie zostanie on uruchomiony, a włączenie funkcji Spaces nie powiedzie się.

Aby rozwiązać ten problem, [Dodaj](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) do klastra AKS, aby upewnić się, że w węźle systemu Windows nie zaplanowano uruchamiania systemów Linux.

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Błąd "znaleziono brak przygotowanych węzłów systemu Linux w stanie gotowe w klastrze. Do wdrożenia w przestrzeni nazw "azds" musi być co najmniej jeden węzeł systemu Linux, który jest w stanie gotowości.

Azure Dev Spaces nie można utworzyć kontrolera w klastrze AKS, ponieważ nie można odnaleźć w stanie *gotowości* węzła niemającego przystosowanego do harmonogramu. Azure Dev Spaces wymaga co najmniej jednego węzła systemu Linux w stanie *gotowości* , który umożliwia planowanie zasobników bez określania tolerowania.

Aby rozwiązać ten problem, [zaktualizuj konfigurację tego programu](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) w klastrze AKS, aby upewnić się, że co najmniej jeden węzeł systemu Linux zezwala na planowanie zasobników bez określania tolerowanych danych. Ponadto upewnij się, że co najmniej jeden węzeł systemu Linux, który umożliwia planowanie zasobników bez określania tolerowania, jest w stanie *gotowe* . Jeśli Twój węzeł zajmuje dużo czasu na osiągnięcie stanu *gotowości* , możesz spróbować ponownie uruchomić węzeł.

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>Błąd "Azure Dev Spaces interfejs wiersza polecenia nie został poprawnie zainstalowany" podczas uruchamiania polecenia AZ AKS use-dev-Spaces

Aktualizacja interfejsu wiersza polecenia Azure Dev Spaces została zmieniona ścieżka instalacji. Jeśli używasz wersji interfejsu wiersza polecenia platformy Azure starszej niż 2.0.63, może pojawić się ten błąd. Aby wyświetlić wersję interfejsu wiersza polecenia platformy Azure, użyj polecenia `az --version` .

```azurecli
az --version
```

```output
azure-cli                         2.0.60 *
...
```

Mimo tego, że podczas uruchamiania programu `az aks use-dev-spaces` z wersją interfejsu wiersza polecenia platformy Azure przed 2.0.63m zostanie wyświetlony komunikat o błędzie, instalacja zakończy się pomyślnie. Można nadal używać `azds` bez żadnych problemów.

Aby rozwiązać ten problem, zaktualizuj instalację [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) do 2.0.63 lub nowszego. Ta aktualizacja rozwiązuje komunikat o błędzie, który pojawia się podczas uruchamiania `az aks use-dev-spaces` . Alternatywnie można nadal używać bieżącej wersji interfejsu wiersza polecenia platformy Azure i interfejsu wiersza polecenia Azure Dev Spaces.

### <a name="error-unable-to-reach-kube-apiserver"></a>Błąd "nie można nawiązać połączenia z polecenia-apiserver"

Ten błąd może pojawić się, gdy Azure Dev Spaces nie może nawiązać połączenia z serwerem interfejsu API klastra AKS.

Jeśli dostęp do serwera interfejsu API klastra AKS jest zablokowany lub jeśli masz włączone [zakresy dozwolonych adresów IP serwera interfejsu API](../aks/api-server-authorized-ip-ranges.md) dla klastra AKS, musisz również [utworzyć](../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled) lub [zaktualizować](../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges) klaster, aby zezwolić na [dodatkowe zakresy w oparciu o region](configure-networking.md#aks-cluster-network-requirements)

Upewnij się, że serwer interfejsu API jest dostępny, uruchamiając polecenia polecenia kubectl. Jeśli serwer interfejsu API jest niedostępny, skontaktuj się z pomocą techniczną usługi AKS i spróbuj ponownie, gdy działa serwer interfejsu API.

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>Typowe problemy podczas przygotowywania projektu dla Azure Dev Spaces

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Ostrzeżenie "nie można wygenerować pliku dockerfile z powodu nieobsługiwanego języka"
Azure Dev Spaces zapewnia natywną obsługę języka C# i Node.js. Po uruchomieniu `azds prep` w katalogu z kodem zapisanym w jednym z tych języków Azure dev Spaces automatycznie tworzy odpowiednie pliku dockerfile.

Nadal możesz używać Azure Dev Spaces z kodem zapisanym w innych językach, ale musisz ręcznie utworzyć pliku dockerfile przed uruchomieniem po `azds up` raz pierwszy.

Jeśli aplikacja jest zapisywana w języku, który Azure Dev Spaces nie obsługuje natywnie, musisz podać odpowiedni pliku dockerfile, aby utworzyć obraz kontenera, w którym działa kod. Platforma Docker zawiera [listę najlepszych rozwiązań dotyczących pisania wieloetapowe dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) i [odwołania pliku dockerfile](https://docs.docker.com/engine/reference/builder/) , które mogą pomóc napisać pliku dockerfile, który odpowiada Twoim potrzebom.

Po wybraniu odpowiedniej pliku dockerfile można uruchomić `azds up` aplikację w Azure dev Spaces.

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>Typowe problemy występujące podczas uruchamiania lub zatrzymywania usług za pomocą Azure Dev Spaces

### <a name="error-config-file-not-found"></a>Błąd "nie znaleziono pliku konfiguracji:"

`azds up`Ten błąd może zostać wyświetlony w trakcie działania. Oba `azds up` i `azds prep` muszą być uruchamiane z katalogu głównego projektu, który ma być uruchamiany w obszarze dev.

Aby rozwiązać ten problem:
1. Zmień bieżący katalog na folder główny zawierający kod usługi. 
1. Jeśli nie masz pliku _azds. YAML_ w folderze Code, uruchom polecenie `azds prep` w celu wygenerowania zasobów platformy Docker, Kubernetes i Azure dev Spaces.

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Limit czasu w "Oczekiwanie na kompilację obrazu kontenera..." krok z węzłami wirtualnymi AKS

Ten limit czasu występuje podczas próby użycia obszarów deweloperskich do uruchomienia usługi, która jest skonfigurowana do uruchamiania w [węźle wirtualnym AKS](../aks/virtual-nodes-portal.md). Obszary deweloperów nie obsługują obecnie usług kompilowania i debugowania w węzłach wirtualnych.

Jeśli uruchamiasz `azds up` polecenie z `--verbose` przełącznikiem lub włączysz pełne rejestrowanie w programie Visual Studio, zobaczysz dodatkowe szczegóły:

```cmd
azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

Powyższe polecenie pokazuje, że usługa poniżej została przypisana do *wirtualnego węzła-ACI-Linux*, który jest węzłem wirtualnym.

Aby rozwiązać ten problem, zaktualizuj wykres Helm dla usługi, aby usunąć wszelkie wartości *nodeSelector* lub *tolerowania* , które umożliwiają uruchamianie usługi w węźle wirtualnym. Te wartości są zwykle zdefiniowane w `values.yaml` pliku wykresu.

Nadal można korzystać z klastra AKS, który ma włączoną funkcję węzłów wirtualnych, jeśli usługa, którą chcesz skompilować lub debugować za pośrednictwem funkcji Spaces, działa w węźle maszyny wirtualnej. Domyślną konfiguracją jest uruchamianie usługi z miejscami deweloperskimi w węźle maszyny wirtualnej.

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>Błąd "nie można znaleźć gotowego do przydzielenia na" pod "podczas uruchamiania miejsc programistycznych

Ten błąd występuje, gdy klient Helm nie może komunikować się z usługą do wykonywania w klastrze.

Aby rozwiązać ten problem, uruchom ponownie węzły agenta w klastrze.

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>Błąd "Release azds — \<identifier\> - \<spacename\> - \<servicename\> Niepowodzenie: usługi" \<servicename\> "już istnieją" lub "odmowa dostępu do ściągania dla elementu \<servicename\> , repozytorium nie istnieje lub może wymagać elementu" Docker login' "

Te błędy mogą wystąpić, jeśli Mieszasz uruchomione bezpośrednie polecenia Helm (takie jak `helm install` , `helm upgrade` lub `helm delete` ) z poleceniami dev Spaces (takimi jak `azds up` i) w `azds down` tym samym obszarze dev. Są one wykonywane, ponieważ spacje do deweloperów mają własne wystąpienie do przyciągania, które powoduje konflikt z własnym wystąpieniem programu do wykonywania w tym samym obszarze dev.

Warto używać zarówno poleceń Helm, jak i poleceń dev Spaces z tym samym klastrem AKS, ale każda przestrzeń nazw z włączoną funkcją dev Spaces powinna używać jednego lub drugiego.

Załóżmy na przykład, że używasz polecenia Helm do uruchamiania całej aplikacji w nadrzędnym obszarze dev. Można utworzyć podrzędne miejsca deweloperskie dla tego elementu nadrzędnego, użyć funkcji miejsca do magazynowania w celu uruchomienia poszczególnych usług w podrzędnych miejscach dev i przetestowania usług. Gdy wszystko będzie gotowe do zaewidencjonowania zmian, użyj polecenia Helm w celu wdrożenia zaktualizowanego kodu w nadrzędnym obszarze dev. Nie należy używać `azds up` do uruchamiania zaktualizowanej usługi w nadrzędnym obszarze dev, ponieważ spowoduje to konflikt z usługą uruchomioną początkowo przy użyciu Helm.

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>Istniejące pliku dockerfile nie są używane do kompilowania kontenera

Azure Dev Spaces można skonfigurować tak, aby wskazywały określony _pliku dockerfile_ w projekcie. Jeśli jest wyświetlany Azure Dev Spaces nie używa _pliku dockerfile_ , aby móc kompilować kontenery, może być konieczne jawne poinformowanie Azure dev Spaces, których pliku dockerfile użyć. 

Aby rozwiązać ten problem, Otwórz plik _azds. YAML_ , który Azure dev Spaces wygenerowany w projekcie. *Konfiguracje aktualizacji: Programowanie: kompilacja: pliku dockerfile* , aby wskazać pliku dockerfile, którego chcesz użyć. Na przykład:

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>Błąd "nieautoryzowane: wymagane uwierzytelnienie" podczas próby użycia obrazu platformy Docker z rejestru prywatnego

Używasz obrazu platformy Docker z rejestru prywatnego wymagającego uwierzytelniania.

Aby rozwiązać ten problem, można zezwolić na używanie obszarów deweloperskich do uwierzytelniania i ściągania obrazów z tego rejestru prywatnego przy użyciu usługi [imagePullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets). Aby użyć imagePullSecrets, [Utwórz wpis tajny Kubernetes](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) w przestrzeni nazw, w której jest używany obraz. Podaj klucz tajny jako imagePullSecret w `azds.yaml` .

Poniżej znajduje się przykład określania imagePullSecrets w `azds.yaml` .

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: $BUILD_CONTEXT$
  dockerfile: Dockerfile
install:
  chart: $CHART_DIR$
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    # Optional, specify an array of imagePullSecrets. These secrets must be manually created in the namespace.
    # This will override the imagePullSecrets array in values.yaml file.
    # If the dockerfile specifies any private registry, the imagePullSecret for the registry must be added here.
    # ref: https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod
    #
    # This uses credentials from secret "myRegistryKeySecretName".
    imagePullSecrets:
      - name: myRegistryKeySecretName
```

> [!IMPORTANT]
> Ustawienie imagePullSecrets w programie `azds.yaml` spowoduje przesłonięcie imagePullSecrets określonych w elemencie `values.yaml` .

### <a name="error-service-cannot-be-started"></a>Błąd "nie można uruchomić usługi".

Ten błąd może pojawić się, gdy uruchomienie kodu usługi nie powiedzie się. Przyczyną jest często kod użytkownika. Aby uzyskać więcej informacji diagnostycznych, należy włączyć bardziej szczegółowe rejestrowanie podczas uruchamiania usługi.

W wierszu polecenia Użyj, `--verbose` Aby włączyć bardziej szczegółowe rejestrowanie. Możesz również określić format danych wyjściowych przy użyciu `--output` . Na przykład:

```cmd
azds up --verbose --output json
```

W programie Visual Studio:

1. Otwórz **narzędzia > opcje** i w obszarze **projekty i rozwiązania**wybierz opcję **Kompiluj i uruchom**.
2. Zmień ustawienia dla **szczegółowości danych wyjściowych kompilacji projektu programu MSBuild** na **szczegóły** lub **diagnostykę**.

    ![Zrzut ekranu przedstawiający okno dialogowe opcji narzędzi](media/common/VerbositySetting.PNG)

### <a name="rerunning-a-service-after-controller-re-creation"></a>Ponowne uruchamianie usługi po ponownym utworzeniu kontrolera

Wystąpił błąd *podczas próby ponownego uruchomienia usługi* po usunięciu i ponownym utworzeniu kontrolera Azure dev Spaces skojarzonego z tym klastrem. W tej sytuacji pełne dane wyjściowe zawierają następujący tekst:

```output
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

Ten błąd występuje, ponieważ usunięcie kontrolera usługi Dev Spaces nie powoduje usunięcia usług wcześniej zainstalowanych przez ten kontroler. Ponowne utworzenie kontrolera, a następnie próba uruchomienia usług przy użyciu nowego kontrolera nie powiedzie się, ponieważ stare usługi są nadal dostępne.

Aby rozwiązać ten problem, należy użyć `kubectl delete` polecenia, aby ręcznie usunąć stare usługi z klastra, a następnie ponownie uruchomić obszary deweloperów, aby zainstalować nowe usługi.

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>Błąd "nie można uruchomić usługi". w przypadku korzystania z wieloetapowych wieloetapowe dockerfile

W przypadku korzystania z wieloetapowej pliku dockerfile *nie można uruchomić usługi* . W tej sytuacji pełne dane wyjściowe zawierają następujący tekst:

```cmd
$ azds up -v
Using dev space 'default' with target 'AksClusterName'
Synchronizing files...6s
Installing Helm chart...2s
Waiting for container image build...10s
Building container image...
Step 1/12 : FROM [imagename:tag] AS base
Error parsing reference: "[imagename:tag] AS base" is not a valid repository/tag: invalid reference format
Failed to build container image.
Service cannot be started.
```

Ten błąd występuje, ponieważ Azure Dev Spaces nie obsługuje obecnie kompilacji wieloetapowych. Aby uniknąć kompilacji wieloetapowych, napisz ponownie pliku dockerfile.

### <a name="network-traffic-is-not-forwarded-to-your-aks-cluster-when-connecting-your-development-machine"></a>Ruch sieciowy nie jest przekazywany do klastra AKS podczas nawiązywania połączenia z maszyną deweloperskią

W przypadku korzystania z [Azure dev Spaces do łączenia klastra AKS z maszyną deweloperskią](https://code.visualstudio.com/docs/containers/bridge-to-kubernetes)może wystąpić problem polegający na tym, że ruch sieciowy nie jest przekazywany między komputerem deweloperskim i klastrem AKS.

Podczas łączenia komputera deweloperskiego z klastrem usługi AKS Azure Dev Spaces przekazuje ruch sieciowy między klastrem AKS i komputerem deweloperskim, modyfikując plik maszyny deweloperskiej `hosts` . Azure Dev Spaces tworzy wpis w polu `hosts` z adresem usługi Kubernetes, która jest zastępowana jako nazwa hosta. Ten wpis jest używany z przekazywaniem portów do bezpośredniego ruchu sieciowego między komputerem deweloperskim i klastrem AKS. Jeśli usługa na komputerze deweloperskim jest w konflikcie z portem usługi Kubernetes, która jest zastępowana, Azure Dev Spaces nie może przekazywać ruchu sieciowego dla usługi Kubernetes. Na przykład usługa *Windows BranchCache* jest zwykle powiązana z *0.0.0.0:80*, co spowoduje konflikty dla portu 80 na wszystkich lokalnych adresach IP.

Aby rozwiązać ten problem, należy zatrzymać wszystkie usługi lub procesy, które powodują konflikt z portem usługi Kubernetes, którą próbujesz zastąpić. Za pomocą narzędzi, takich jak *netstat*, można sprawdzić, jakie usługi lub procesy na komputerze deweloperskim są w konflikcie.

Na przykład aby zatrzymać i wyłączyć usługę *Windows BranchCache* :
* Uruchom `services.msc` polecenie w wierszu polecenia.
* Kliknij prawym przyciskiem myszy *usługę BranchCache* i wybierz pozycję *Właściwości*.
* Kliknij przycisk *Zatrzymaj*.
* Opcjonalnie możesz ją wyłączyć, ustawiając *Typ uruchamiania* na *wyłączone*.
* Kliknij przycisk *OK*.

### <a name="error-no-azureassignedidentity-found-for-podazdsazds-webhook-deployment-id-in-assigned-state"></a>Błąd "nie znaleziono AzureAssignedIdentity dla elementu pod: azds/azds-webhook- \<id\> w stanie przypisanym do wdrożenia"

Podczas uruchamiania usługi z Azure Dev Spaces w klastrze AKS z [zarządzaną tożsamością](../aks/use-managed-identity.md) i [tożsamościami zarządzanymi](../aks/developer-best-practices-pod-security.md#use-pod-managed-identities) , proces może przestać odpowiadać po kroku *instalacji wykresu* . Po sprawdzeniu *azds-wtryskiwacza elementu webhook* w przestrzeni nazw *azds* może zostać wyświetlony ten błąd.

Azure Dev Spaces usługi są uruchamiane w klastrze przy użyciu tożsamości zarządzanej klastra, aby komunikować się z usługami zaplecza Azure Dev Spaces poza klastrem. Gdy jest zainstalowana tożsamość zarządzana, reguły sieci są konfigurowane w węzłach klastra w celu przekierowania wszystkich wywołań poświadczeń tożsamości zarządzanej do [tożsamości zarządzanej przez węzeł (NMI) elementu daemonset zainstalowanej w klastrze](https://github.com/Azure/aad-pod-identity#node-managed-identity). Ten NMI elementu daemonset identyfikuje wywoływanie pod i gwarantuje, że pod etykietą jest odpowiednio etykieta, aby uzyskać dostęp do żądanej tożsamości zarządzanej. Azure Dev Spaces nie może wykryć, czy klaster ma zainstalowaną tożsamość zarządzaną i nie może wykonać niezbędnej konfiguracji, aby umożliwić Azure Dev Spaces usługom dostęp do tożsamości zarządzanej klastra. Ponieważ usługi Azure Dev Spaces nie zostały skonfigurowane do uzyskiwania dostępu do tożsamości zarządzanej klastra, NMI elementu daemonset nie zezwoli im na uzyskanie tokenu usługi Azure AD dla tożsamości zarządzanej i nie będzie mógł komunikować się z usługami zaplecza Azure Dev Spaces.

Aby rozwiązać ten problem, Zastosuj element [AzurePodIdentityException](https://github.com/Azure/aad-pod-identity/blob/master/docs/readmes/README.app-exception.md) dla *azds-iniektora-webhook* i updatebinding z instrumentacją Azure dev Spaces, aby uzyskać dostęp do tożsamości zarządzanej.

Utwórz plik o nazwie *webhookexception. YAML* i Skopiuj następującą definicję YAML:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: azds-infrastructure-exception
  namespace: azds
spec:
  PodLabels:
    azds.io/uses-cluster-identity: "true"
```

Powyższy plik tworzy obiekt *AzurePodIdentityException* dla *azds-wtryskiwacza elementu webhook*. Aby wdrożyć ten obiekt, użyj `kubectl` :

```cmd
kubectl apply -f webhookException.yaml
```

Aby Azure Dev Spaces zaktualizować usługi YAML w celu uzyskania dostępu do tożsamości zarządzanej, należy zaktualizować *przestrzeń nazw* w poniższej definicji usługi i użyć `kubectl` jej do zastosowania do każdego obszaru dev.

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: azds-infrastructure-exception
  namespace: myNamespace
spec:
  PodLabels:
    azds.io/instrumented: "true"
```

Alternatywnie można tworzyć obiekty *AzureIdentity* i *AzureIdentityBinding* oraz aktualizować etykiety pod kątem obciążeń działających w miejscach, w których Instrumentacja Azure dev Spaces uzyskać dostęp do zarządzanej tożsamości utworzonej przez klaster AKS.

Aby wyświetlić szczegóły zarządzanej tożsamości, uruchom następujące polecenie dla klastra AKS:

```azurecli
az aks show -g <resourcegroup> -n <cluster> -o json --query "{clientId: identityProfile.kubeletidentity.clientId, resourceId: identityProfile.kubeletidentity.resourceId}"
```

Powyższe polecenie wyprowadza *clientId* i *ResourceID* dla tożsamości zarządzanej. Na przykład:

```json
{
  "clientId": "<clientId>",
  "resourceId": "/subscriptions/<subid>/resourcegroups/<resourcegroup>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<name>"
}
```

Aby utworzyć obiekt *AzureIdentity* , Utwórz plik o nazwie *clusteridentity. YAML* i użyj następującej zaktualizowanej definicji YAML z informacjami o tożsamości zarządzanej z poprzedniego polecenia:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentity
metadata:
  name: my-cluster-mi
spec:
  type: 0
  ResourceID: /subscriptions/<subid>/resourcegroups/<resourcegroup>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<name>
  ClientID: <clientId>
```

Aby utworzyć obiekt *AzureIdentityBinding* , Utwórz plik o nazwie *clusteridentitybinding. YAML* i użyj następującej definicji YAML:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: my-cluster-mi-binding
spec:
  AzureIdentity: my-cluster-mi
  Selector: my-label-value
```

Aby wdrożyć obiekty *AzureIdentity* i *AzureIdentityBinding* , użyj `kubectl` :

```cmd
kubectl apply -f clusteridentity.yaml
kubectl apply -f clusteridentitybinding.yaml
```

Po wdrożeniu obiektów *AzureIdentity* i *AzureIdentityBinding* każde obciążenie z etykietą *aadpodidbinding: My-Label-Value* może uzyskać dostęp do zarządzanej tożsamości klastra. Dodaj tę etykietę i Wdróż ponownie wszystkie obciążenia działające w dowolnym miejscu dev. Na przykład:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: sample
        aadpodidbinding: my-label-value
    spec:
      [...]
```

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>Typowe problemy związane z używaniem programu Visual Studio i Visual Studio Code z Azure Dev Spaces

### <a name="error-required-tools-and-configurations-are-missing"></a>Błąd "Brak wymaganych narzędzi i konfiguracji"

Ten błąd może wystąpić podczas uruchamiania VS Code: "[Azure Dev Spaces] nie ma wymaganych narzędzi i konfiguracji do kompilowania i debugowania" [nazwa projektu] "."
Błąd oznacza, że azds.exe nie znajduje się w zmiennej środowiskowej PATH, jak pokazano w VS Code.

Spróbuj uruchomić VS Code z poziomu wiersza polecenia, gdzie zmienna środowiskowa PATH jest ustawiona prawidłowo.

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Błąd "wymagane narzędzia do kompilacji i debugowania" ProjectName "są nieaktualne".

Ten błąd jest wyświetlany w Visual Studio Code, jeśli masz nowszą wersję rozszerzenia VS Code dla Azure Dev Spaces, ale starszą wersję interfejsu wiersza polecenia Azure Dev Spaces.

Spróbuj pobrać i zainstalować najnowszą wersję interfejsu wiersza polecenia Azure Dev Spaces:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>Błąd: "nie można odnaleźć rozszerzenia debugera dla typu: CoreCLR"

Ten błąd może pojawić się podczas uruchamiania debugera Visual Studio Code. Być może nie masz rozszerzenia VS Code dla języka C# zainstalowanego na komputerze deweloperskim. Rozszerzenie języka C# obejmuje obsługę debugowania dla platformy .NET Core (CoreCLR).

Aby rozwiązać ten problem, zainstaluj [rozszerzenie vs Code dla języka C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>Błąd "skonfigurowany typ debugowania" CoreCLR "nie jest obsługiwany"

Ten błąd może pojawić się podczas uruchamiania debugera Visual Studio Code. Być może nie masz rozszerzenia VS Code Azure Dev Spaces zainstalowane na komputerze deweloperskim.

Aby rozwiązać ten problem, zainstaluj rozszerzenie VS Code dla Azure Dev Spaces.

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Błąd "Nieprawidłowa wartość" cwd ""/src ". System nie może odnaleźć określonego pliku. " lub "Launch: program/src/[ścieżka do pliku binarnego projektu] ' nie istnieje"

Ten błąd może pojawić się podczas uruchamiania debugera Visual Studio Code. Domyślnie rozszerzenie VS Code używa `src` jako katalogu roboczego dla projektu w kontenerze. Jeśli Zaktualizowano `Dockerfile` w celu określenia innego katalogu roboczego, ten błąd może zostać wyświetlony.

Aby rozwiązać ten problem, zaktualizuj `launch.json` plik w `.vscode` podkatalogu folderu projektu. Zmień `configurations->cwd` dyrektywę tak, aby wskazywała ten sam katalog, jak `WORKDIR` zdefiniowany w projekcie `Dockerfile` . Może być również konieczne zaktualizowanie `configurations->program` dyrektywy.

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Błąd "azds" programu potoku "zakończył się nieoczekiwanie z kodem 126."

Ten błąd może pojawić się podczas uruchamiania debugera Visual Studio Code.

Aby rozwiązać ten problem, Zamknij i ponownie otwórz Visual Studio Code. Uruchom ponownie debuger.

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Błąd "nie można przeprowadzić wewnętrznej obserwacji: Watch ENOSPC" podczas dołączania debugowania do aplikacji Node.js

Ten błąd występuje, gdy węzeł, na którym działa program, z aplikacją Node.js, którą próbujesz dołączyć do debugera, przekroczył wartość *FS.inotify.max_user_watches* . W niektórych przypadkach [wartość domyślna *FS.inotify.max_user_watches* może być za mała, aby obsłużyć bezpośrednie dołączenie debugera do poziomu](https://github.com/Azure/AKS/issues/772).

Tymczasowe obejście tego problemu polega na zwiększeniu wartości *FS.inotify.max_user_watches* w każdym węźle klastra i ponownym uruchomieniu tego węzła, aby zmiany zaczęły obowiązywać.

## <a name="other-common-issues"></a>Inne typowe problemy

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Błąd "azds" nie został rozpoznany jako polecenie wewnętrzne lub zewnętrzne, program interoperacyjny lub plik wsadowy

Ten błąd może wystąpić `azds.exe` , jeśli nie został poprawnie zainstalowany lub skonfigurowany.

Aby rozwiązać ten problem:

1. Sprawdź lokalizację% ProgramFiles%/Microsoft SDKs\Azure\Azure dev Spaces CLI dla programu `azds.exe` . Jeśli tak jest, Dodaj tę lokalizację do zmiennej środowiskowej PATH.
2. Jeśli `azds.exe` program nie jest zainstalowany, uruchom następujące polecenie:

    ```azurecli
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>Błąd autoryzacji "Microsoft. DevSpaces/Register/Action"

Aby zarządzać Azure Dev Spaces, musisz mieć dostęp do *właściciela* lub *współautora* w ramach subskrypcji platformy Azure. Jeśli próbujesz zarządzać miejscami deweloperskimi, a nie masz dostępu *właściciela* lub *współautora* do skojarzonej subskrypcji platformy Azure, może zostać wyświetlony błąd autoryzacji. Na przykład:

```output
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

Aby rozwiązać ten problem, użyj konta z dostępem *właściciela* lub *współautora* do subskrypcji platformy Azure, ręcznie Zarejestruj `Microsoft.DevSpaces` przestrzeń nazw:

```azurecli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>Nowe zasobniki nie są uruchamiane

Inicjator Kubernetes nie może zastosować PodSpec dla nowych, z powodu zmian uprawnień RBAC w roli *administratora klastra* w klastrze. Nowy element pod może również mieć nieprawidłową PodSpec, na przykład konto usługi skojarzone ze znakiem "już nie istnieje". Aby wyświetlić zasobniki, które znajdują się w stanie *oczekiwania* ze względu na problem z inicjatorem, użyj `kubectl get pods` polecenia:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Ten problem może mieć wpływ na *wszystkie przestrzenie nazw* w klastrze, w tym przestrzenie nazw, w których Azure dev Spaces nie jest włączona.

Aby rozwiązać ten problem, [zaktualizuj interfejs wiersza polecenia dev Spaces do najnowszej wersji](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) , a następnie usuń *azds InitializerConfiguration* z kontrolera Azure dev Spaces:

```azurecli
az aks get-credentials --resource-group <resource group name> --name <cluster name>
```

```bash
kubectl delete InitializerConfiguration azds
```

Po usunięciu *Azds InitializerConfiguration* z kontrolera Azure dev Spaces Użyj, `kubectl delete` Aby usunąć wszystkie zasobniki w stanie *oczekiwania* . Po usunięciu wszystkich oczekujących zasobników należy ponownie wdrożyć Twoje dane.

Jeśli nowe zasobniki są nadal zablokowane w stanie *oczekiwania* po ponownej instalacji, użyj, `kubectl delete` Aby usunąć wszystkie zasobniki w stanie *oczekiwania* . Po usunięciu wszystkich oczekujących zasobników Usuń kontroler z klastra i zainstaluj go ponownie:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

Po ponownym zainstalowaniu kontrolera ponownie Wdróż swój zasobnik.

### <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Nieprawidłowe uprawnienia RBAC do wywoływania kontrolera i interfejsów API programu dev Spaces

Użytkownik, który uzyskuje dostęp do kontrolera Azure Dev Spaces, musi mieć dostęp do odczytu *kubeconfig* administratora w klastrze AKS. Na przykład to uprawnienie jest dostępne w [wbudowanej roli administratora klastra usługi Kubernetes platformy Azure](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions). Użytkownik uzyskujący dostęp do kontrolera Azure Dev Spaces musi także mieć rolę *współautora* lub *właściciela* dla kontrolera. Więcej szczegółów na temat aktualizowania uprawnień użytkownika do klastra AKS są dostępne [tutaj](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group).

Aby zaktualizować rolę platformy Azure użytkownika dla kontrolera:

1. Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.
1. Przejdź do grupy zasobów zawierającej kontroler, która jest zwykle taka sama jak w przypadku klastra AKS.
1. Włącz pole wyboru *Pokaż ukryte typy* .
1. Kliknij kontroler.
1. Otwórz okienko *Access Control (IAM)* .
1. Kliknij kartę *przypisania ról* .
1. Kliknij przycisk *Dodaj* , a następnie *Dodaj przypisanie roli*.
    * W obszarze *rola*wybierz opcję *współautor* lub *właściciel*.
    * W obszarze *Przypisywanie dostępu do*wybierz pozycję *użytkownik, Grupa lub nazwa główna usługi Azure AD*.
    * Dla *opcji wybierz*Wyszukaj użytkownika, którym chcesz nadać uprawnienia.
1. Kliknij przycisk *Zapisz*.

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Rozpoznawanie nazw DNS nie powiodło się dla publicznego adresu URL skojarzonego z usługą Spaces

Możesz skonfigurować publiczny punkt końcowy adresu URL dla usługi `--enable-ingress` , określając przełącznik do `azds prep` polecenia lub zaznaczając `Publicly Accessible` pole wyboru w programie Visual Studio. Publiczna nazwa DNS jest automatycznie rejestrowana podczas uruchamiania usługi w obszarze dev Spaces. Jeśli ta nazwa DNS nie jest zarejestrowana, zobaczysz, że *nie można wyświetlić strony* lub *nie można uzyskać dostępu do witryny* w przeglądarce sieci Web podczas łączenia się z publicznym adresem URL.

Aby rozwiązać ten problem:

* Sprawdź stan wszystkich adresów URL skojarzonych z usługami dev Spaces:

  ```console
  azds list-uris
  ```

* Jeśli adres URL jest w stanie *oczekiwania* , spacje deweloperów nadal czekają na ukończenie rejestracji DNS. Czasami Rejestracja może potrwać kilka minut. Miejsca deweloperskie otwierają także tunel localhost dla każdej usługi, którego można użyć podczas oczekiwania na rejestrację DNS.
* Jeśli adres URL pozostaje w stanie *oczekiwania* przez więcej niż 5 minut, może to wskazywać na problem z zewnętrznym systemem DNS, który tworzy publiczny punkt końcowy lub kontroler Nginx transferu danych przychodzących pod warunkiem, że uzyskuje publiczny punkt końcowy. Użyj następujących poleceń, aby usunąć te zasobniki i zezwolić AKS na ich automatyczne odtworzenie:
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Błąd "Wystąpił błąd połączenia nadrzędnego lub odłącz/Zresetuj przed nagłówkami"

Ten błąd może pojawić się podczas próby uzyskania dostępu do usługi. Na przykład po przejściu do adresu URL usługi w przeglądarce. Ten błąd oznacza, że port kontenera nie jest dostępny. Może to być spowodowane następującymi przyczynami:

* Kontener nadal trwa Kompilowanie i wdrażanie. Ten problem może wystąpić, jeśli uruchomisz `azds up` lub uruchomisz debuger, a następnie spróbujesz uzyskać dostęp do kontenera, zanim zostanie on pomyślnie wdrożony.
* Konfiguracja portów nie jest spójna na wykresie _pliku dockerfile_, Helm i dowolnym kodzie serwera otwierającym port.

Aby rozwiązać ten problem:

1. Jeśli trwa Kompilowanie/wdrażanie kontenera, możesz poczekać 2-3 sekund i ponownie spróbować uzyskać dostęp do usługi. 
1. Sprawdź konfigurację portu w następujących zasobach:
    * ** [Wykres Helm](https://docs.helm.sh):** Określone przez `service.port` i `deployment.containerPort` w wartości. YAML szkieletowe przez `azds prep` polecenie.
    * Wszystkie porty otwierane w kodzie aplikacji, na przykład w Node.js: `var server = app.listen(80, function () {...}`

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>Nie można znaleźć nazwy typu lub przestrzeni nazw "Moja biblioteka"

Nie można znaleźć projektu biblioteki, którego używasz. W przypadku obszarów programistycznych kontekst kompilacji jest domyślnie na poziomie projektu/usługi.  

Aby rozwiązać ten problem:

1. Zmodyfikuj `azds.yaml` plik w celu ustawienia kontekstu kompilacji na poziom rozwiązania.
2. Zmodyfikuj `Dockerfile` pliki i `Dockerfile.develop` , aby odwoływać się do plików projektu, na przykład `.csproj` prawidłowo względem nowego kontekstu kompilacji.
3. Dodaj do tego `.dockerignore` samego katalogu co `.sln` plik.
4. Zaktualizuj `.dockerignore` program przy użyciu dodatkowych wpisów zgodnie z wymaganiami.

Przykład można znaleźć [tutaj](https://github.com/sgreenmsft/buildcontextsample).

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>Skalowanie w poziomie nie działa w obszarze dev

Po uruchomieniu usługi w obszarze deweloperskim, ta usługa jest wprowadzana [z dodatkowymi kontenerami dla Instrumentacji](how-dev-spaces-works-cluster-setup.md#prepare-your-aks-cluster) , a wszystkie kontenery w miejscu muszą mieć limity zasobów i żądania ustawione na automatyczne skalowanie w poziomie.

Aby rozwiązać ten problem, Zastosuj żądanie zasobu i Ogranicz do wprowadzonych kontenerów miejsc dev. Żądania zasobów i limity można zastosować dla wstrzykniętego kontenera (devspaces-proxy), dodając `azds.io/proxy-resources` adnotację do specyfikacji pod. Wartość powinna być ustawiona na obiekt JSON reprezentujący sekcję zasobów specyfikacji kontenera dla serwera proxy.

Poniżej znajduje się przykład adnotacji zasobów serwera proxy, która ma zostać zastosowana do specyfikacji pod.
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

### <a name="enable-azure-dev-spaces-on-an-existing-namespace-with-running-pods"></a>Włącz Azure Dev Spaces w istniejącej przestrzeni nazw z uruchomionymi jednostkami

Być może masz istniejący klaster AKS i przestrzeń nazw z uruchomionymi jednostkami, w których chcesz włączyć Azure Dev Spaces.

Aby włączyć Azure Dev Spaces w istniejącej przestrzeni nazw w klastrze AKS, uruchom polecenie `use-dev-spaces` i Użyj, `kubectl` Aby ponownie uruchomić wszystkie zasobniki w tej przestrzeni nazw.

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space my-namespace --yes
```

```console
kubectl -n my-namespace delete pod --all
```

Po ponownym uruchomieniu swoich zasobników możesz rozpocząć korzystanie z istniejącej przestrzeni nazw za pomocą Azure Dev Spaces.

### <a name="enable-azure-dev-spaces-on-aks-cluster-with-restricted-egress-traffic-for-cluster-nodes"></a>Włącz Azure Dev Spaces w klastrze AKS z ograniczonym ruchem wychodzącym dla węzłów klastra

Aby włączyć Azure Dev Spaces w klastrze AKS, dla którego ruch wychodzący z węzłów klastra jest ograniczony, należy zezwolić na następujące nazwy FQDN:

| Nazwa FQDN                                    | Port      | Zastosowanie      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS: 443 | Aby ściągnąć obrazy z systemem Linux Alpine i innymi Azure Dev Spaces |
| gcr.io | HTTP: 443 | Aby ściągnąć obrazy Helm/er|
| storage.googleapis.com | HTTP: 443 | Aby ściągnąć obrazy Helm/er|

Zaktualizuj zaporę lub konfigurację zabezpieczeń, aby zezwalać na ruch sieciowy do i z wszystkich powyższych nazw FQDN i [usług Azure dev Spaces infrastruktury](../dev-spaces/configure-networking.md#virtual-network-or-subnet-configurations).

### <a name="error-could-not-find-the-cluster-cluster-in-subscription-subscriptionid"></a>Błąd "nie można odnaleźć klastra \<cluster\> w subskrypcji \<subscriptionId\> "

Ten błąd może pojawić się, jeśli plik kubeconfig jest przeznaczony dla innego klastra lub subskrypcji niż próbujesz użyć go przy użyciu narzędzi po stronie klienta Azure Dev Spaces. Narzędzia po stronie klienta Azure Dev Spaces replikuje zachowanie *polecenia kubectl*, które używa [co najmniej jednego pliku kubeconfig](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/) do wybierania i komunikowania się z klastrem.

Aby rozwiązać ten problem:

* Użyj `az aks use-dev-spaces -g <resource group name> -n <cluster name>` , aby zaktualizować bieżący kontekst. To polecenie umożliwia również Azure Dev Spaces w klastrze AKS, jeśli nie jest jeszcze włączona. Alternatywnie, można użyć, `kubectl config use-context <cluster name>` Aby zaktualizować bieżący kontekst.
* Użyj `az account show` , aby wyświetlić bieżącą subskrypcję platformy Azure, której dotyczy, i sprawdź, czy jest ona poprawna. Możesz zmienić subskrypcję, której używasz `az account set` .

### <a name="error-using-dev-spaces-after-rotating-aks-certificates"></a>Błąd przy użyciu funkcji Spaces dev po AKS certyfikatów

Po [obróceniu certyfikatów w KLASTRZE AKS](../aks/certificate-rotation.md)niektóre operacje, takie jak i, zakończą `azds space list` `azds up` się niepowodzeniem. Należy również odświeżyć certyfikaty na kontrolerze Azure Dev Spaces po obróceniu certyfikatów w klastrze.

Aby rozwiązać ten problem, upewnij się, że *kubeconfig* ma zaktualizowane certyfikaty, `az aks get-credentials` a następnie uruchom `azds controller refresh-credentials` polecenie. Na przykład:

```azurecli
az aks get-credentials -g <resource group name> -n <cluster name>
```

```console
azds controller refresh-credentials -g <resource group name> -n <cluster name>
```
