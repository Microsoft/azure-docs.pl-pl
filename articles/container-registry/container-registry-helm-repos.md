---
title: Przechowuj wykresy Helm
description: Dowiedz się, jak przechowywać wykresy Helm dla aplikacji Kubernetes przy użyciu repozytoriów w Azure Container Registry
ms.topic: article
ms.date: 06/12/2020
ms.openlocfilehash: 9897ed6e43813c16314076b0322cd263cd2ed150
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106223085"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Wypychanie i ściąganie wykresów Helm do usługi Azure Container Registry

Aby szybko zarządzać aplikacjami dla programu Kubernetes i wdrażać je, można użyć [Menedżera pakietów Helm Open Source][helm]. W przypadku Helm pakiety aplikacji są definiowane jako [wykresy](https://helm.sh/docs/topics/charts/), które są zbierane i przechowywane w [repozytorium wykresu Helm](https://helm.sh/docs/topics/chart_repository/).

W tym artykule opisano sposób hostowania repozytoriów wykresów Helm w usłudze Azure Container Registry przy użyciu poleceń Helm 3. W wielu scenariuszach utworzysz i przekażesz własne wykresy dla aplikacji, które tworzysz. Aby uzyskać więcej informacji na temat tworzenia własnych wykresów Helm, zobacz [przewodnik dewelopera szablonu wykresu][develop-helm-charts]. Istnieje również możliwość przechowywania istniejącego wykresu Helm z innego repozytorium Helm.

> [!IMPORTANT]
> Obsługa wykresów Helm w Azure Container Registry jest obecnie w wersji zapoznawczej. Wersje zapoznawcze są udostępniane w przypadku, gdy użytkownik wyrazi zgodę na uzupełniające [warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="helm-3-or-helm-2"></a>Helm 3 lub Helm 2?

Aby przechowywać i instalować wykresy Helm oraz zarządzać nimi, należy użyć klienta Helm i interfejsu wiersza polecenia Helm. Główne wersje klienta Helm obejmują Helm 3 i Helm 2. Aby uzyskać szczegółowe informacje dotyczące różnic wersji, zobacz sekcję [często zadawane pytania dotyczące wersji](https://helm.sh/docs/faq/). 

Helm 3 należy używać do hostowania wykresów Helm w Azure Container Registry. Helm 3:

* Można utworzyć co najmniej jeden repozytoria Helm w usłudze Azure Container Registry
* Przechowuj wykresy Helm 3 w rejestrze jako [artefakty OCI](container-registry-image-formats.md#oci-artifacts). Obecnie Helm 3 Obsługa OCI jest *eksperymentalna*.
* Uwierzytelnianie za pomocą rejestru przy użyciu `helm registry login` polecenia.
* Używanie `helm chart` poleceń w interfejsie wiersza polecenia Helm do wypychania i ściągania wykresów Helm i zarządzania nimi w rejestrze
* Służy `helm install` do instalowania wykresów w klastrze Kubernetes z lokalnej pamięci podręcznej repozytorium.
> [!NOTE]
> Od Helm 3 polecenia [AZ ACR Helm][az-acr-helm] do użycia z klientem Helm 2 są przestarzałe. Przed usunięciem polecenia zostanie podane co najmniej 3 miesiące. Jeśli wcześniej wdrożono wykresy Helm 2, zobacz [Migrowanie Helm v2 do wersji v3](https://helm.sh/docs/topics/v2_v3_migration/).

## <a name="prerequisites"></a>Wymagania wstępne

W scenariuszu w tym artykule są następujące zasoby:

- **Rejestr kontenerów platformy Azure** w ramach subskrypcji platformy Azure. W razie konieczności Utwórz rejestr przy użyciu [Azure Portal](container-registry-get-started-portal.md) lub [interfejsu wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md).
- **Helm klienta w wersji 3.1.0 lub nowszej** — Uruchom `helm version` , aby znaleźć bieżącą wersję. Aby uzyskać więcej informacji na temat instalowania i uaktualniania Helm, zobacz [Instalowanie Helm][helm-install].
- **Klaster Kubernetes** , w którym zostanie zainstalowany wykres Helm. W razie konieczności Utwórz [klaster usługi Azure Kubernetes][aks-quickstart]. 
- **Interfejs wiersza polecenia platformy Azure w wersji 2.0.71 lub nowszej** — Uruchom `az --version` , aby znaleźć wersję. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="enable-oci-support"></a>Włącz obsługę OCI

Użyj `helm version` polecenia, aby sprawdzić, czy zainstalowano program Helm 3:

```console
helm version
```

Ustaw następujące zmienne środowiskowe, aby włączyć obsługę OCI w kliencie Helm 3. Obecnie to wsparcie jest eksperymentalne. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

## <a name="create-a-sample-chart"></a>Tworzenie przykładowego wykresu

Utwórz wykres testowy przy użyciu następujących poleceń:

```console
mkdir helmtest

cd helmtest
helm create hello-world
```

Jako podstawowy przykład zmień katalog na `templates` folder i najpierw usuń jego zawartość:

```console
cd hello-world/templates
rm -rf *
```

W `templates` folderze Utwórz plik o nazwie `configmap.yaml` , uruchamiając następujące polecenie:

```console
cat <<EOF > configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: hello-world-configmap
data:
  myvalue: "Hello World"
EOF
```

Aby uzyskać więcej informacji na temat tworzenia i uruchamiania tego przykładu, zobacz [wprowadzenie](https://helm.sh/docs/chart_template_guide/getting_started/) w dokumentacji Helm.

## <a name="save-chart-to-local-registry-cache"></a>Zapisz wykres w lokalnej pamięci podręcznej rejestru

Zmień katalog na `hello-world` podkatalog. Następnie uruchom polecenie, `helm chart save` Aby zapisać kopię wykresu lokalnie, a także utworzyć alias z w pełni kwalifikowaną nazwą rejestru (wszystkie małe litery) i repozytorium docelowym i znacznikiem. 

W poniższym przykładzie nazwa rejestru to *mycontainerregistry*, repozytorium Target to *Hello-World*, a znacznik wykresu docelowego to *V1*, ale zastępujący wartości dla danego środowiska:

```console
cd ..
helm chart save . hello-world:v1
helm chart save . mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Uruchom `helm chart list` , aby potwierdzić, że Zapisano wykresy w lokalnej pamięci podręcznej rejestru. Dane wyjściowe są podobne do następujących:

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
hello-world:v1                                           hello-world       0.1.0   5899db0 3.2 KiB        2 minutes 
mycontainerregistry.azurecr.io/helm/hello-world:v1       hello-world       0.1.0   5899db0 3.2 KiB        2 minutes
```

## <a name="authenticate-with-the-registry"></a>Uwierzytelnianie przy użyciu rejestru

Uruchom `helm registry login` polecenie w interfejsie wiersza polecenia Helm 3, aby [uwierzytelnić się z rejestrem](container-registry-authentication.md) przy użyciu poświadczeń odpowiednich dla danego scenariusza.

Na przykład Utwórz jednostkę usługi Azure Active Directory [z uprawnieniami ściągania i wypychania](container-registry-auth-service-principal.md#create-a-service-principal) (rolą AcrPush) do rejestru. Następnie podaj poświadczenia nazwy głównej usługi `helm registry login` . Poniższy przykład dostarcza hasła przy użyciu zmiennej środowiskowej:

```console
echo $spPassword | helm registry login mycontainerregistry.azurecr.io \
  --username <service-principal-id> \
  --password-stdin
```

## <a name="push-chart-to-registry"></a>Wypychanie wykresu do rejestru

Uruchom `helm chart push` polecenie w interfejsie wiersza polecenia Helm 3, aby wypchnąć wykres do w pełni kwalifikowanego repozytorium docelowego:

```console
helm chart push mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Po pomyślnym wypchnięciu dane wyjściowe są podobne do:

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/hello-world]
ref:     mycontainerregistry.azurecr.io/helm/hello-world:v1
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    3.2 KiB
name:    hello-world
version: 0.1.0
```

## <a name="list-charts-in-the-repository"></a>Wyświetlanie listy wykresów w repozytorium

Podobnie jak w przypadku obrazów przechowywanych w usłudze Azure Container Registry, można użyć polecenia [AZ ACR Repository][az-acr-repository] Commands, aby pokazać repozytoria obsługujące wykresy oraz Tagi wykresu i manifesty. 

Na przykład uruchom [AZ ACR Repository show][az-acr-repository-show] , aby wyświetlić właściwości repozytorium utworzonego w poprzednim kroku:

```azurecli
az acr repository show \
  --name mycontainerregistry \
  --repository helm/hello-world
```

Dane wyjściowe są podobne do następujących:

```output
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2020-03-20T18:11:37.6701689Z",
  "imageName": "helm/hello-world",
  "lastUpdateTime": "2020-03-20T18:11:37.7637082Z",
  "manifestCount": 1,
  "registry": "mycontainerregistry.azurecr.io",
  "tagCount": 1
}
```

Uruchom polecenie [AZ ACR Repository show-Manifests][az-acr-repository-show-manifests] , aby wyświetlić szczegóły wykresu przechowywanego w repozytorium. Na przykład:

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/hello-world --detail
```

Wynik, skrócony w tym przykładzie, pokazuje `configMediaType` `application/vnd.cncf.helm.config.v1+json` :

```output
[
  {
    [...]
    "configMediaType": "application/vnd.cncf.helm.config.v1+json",
    "createdTime": "2020-03-20T18:11:37.7167893Z",
    "digest": "sha256:0c03b71c225c3ddff53660258ea16ca7412b53b1f6811bf769d8c85a1f0663ee",
    "imageSize": 3301,
    "lastUpdateTime": "2020-03-20T18:11:37.7167893Z",
    "mediaType": "application/vnd.oci.image.manifest.v1+json",
    "tags": [
      "v1"
    ]
```

## <a name="pull-chart-to-local-cache"></a>Ściąganie wykresu do lokalnej pamięci podręcznej

Aby zainstalować wykres Helm do Kubernetes, wykres musi znajdować się w lokalnej pamięci podręcznej. W tym przykładzie najpierw należy uruchomić polecenie, `helm chart remove` Aby usunąć istniejący wykres lokalny o nazwie `mycontainerregistry.azurecr.io/helm/hello-world:v1` :

```console
helm chart remove mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Uruchom, `helm chart pull` Aby pobrać wykres z usługi Azure Container Registry do lokalnej pamięci podręcznej:

```console
helm chart pull mycontainerregistry.azurecr.io/helm/hello-world:v1
```

## <a name="export-helm-chart"></a>Eksportuj wykres Helm

Aby kontynuować, wyeksportuj go do katalogu lokalnego przy użyciu `helm chart export` . Na przykład wyeksportuj wykres, który został pobrany do `install` katalogu:

```console
helm chart export mycontainerregistry.azurecr.io/helm/hello-world:v1 \
  --destination ./install
```

Aby wyświetlić informacje dotyczące wyeksportowanego wykresu w repozytorium, uruchom `helm show chart` polecenie w katalogu, w którym został wyeksportowany wykres.

```console
cd install
helm show chart hello-world
```

Helm zwraca szczegółowe informacje o najnowszej wersji wykresu, jak pokazano w następujących przykładowych danych wyjściowych:

```output
apiVersion: v2
appVersion: 1.16.0
description: A Helm chart for Kubernetes
name: hello-world
type: application
version: 0.1.0    
```

## <a name="install-helm-chart"></a>Zainstaluj wykres Helm

Uruchom, `helm install` Aby zainstalować wykres Helm, który został pobrany do lokalnej pamięci podręcznej i wyeksportowany. Określ nazwę wydania, taką jak *myhelmtest*, lub Przekaż `--generate-name` parametr. Na przykład:

```console
helm install myhelmtest ./hello-world
```

Dane wyjściowe po pomyślnej instalacji wykresu są podobne do:

```console
NAME: myhelmtest
LAST DEPLOYED: Fri Mar 20 14:14:42 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

Aby zweryfikować instalację, uruchom `helm get manifest` polecenie. 

```console
helm get manifest myhelmtest
```

Polecenie zwraca dane YAML w `configmap.yaml` pliku szablonu.

Uruchom, `helm uninstall` Aby odinstalować wydanie wykresu w klastrze:

```console
helm uninstall myhelmtest
```

## <a name="delete-chart-from-the-registry"></a>Usuwanie wykresu z rejestru

Aby usunąć wykres z rejestru kontenerów, użyj polecenia [AZ ACR Repository Delete][az-acr-repository-delete] . Uruchom następujące polecenie i potwierdź operację po wyświetleniu monitu:

```azurecli
az acr repository delete --name mycontainerregistry --image helm/hello-world:v1
```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat tworzenia i wdrażania wykresów Helm, zobacz [Tworzenie wykresów Helm][develop-helm-charts].
* Dowiedz się więcej o instalowaniu aplikacji z programem Helm w [usłudze Azure Kubernetes Service (AKS)](../aks/kubernetes-helm.md).
* Wykresy Helm mogą być używane jako część procesu tworzenia kontenera. Aby uzyskać więcej informacji, zobacz [używanie Azure Container Registry zadań][acr-tasks].

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://helm.sh/docs/intro/install/
[develop-helm-charts]: https://helm.sh/docs/chart_template_guide/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az-configure
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-helm]: /cli/azure/acr/helm
[az-acr-repository]: /cli/azure/acr/repository
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[acr-tasks]: container-registry-tasks-overview.md
