---
title: Store Helm charts (Przechowuj wykresy helm)
description: Dowiedz się, jak przechowywać wykresy programu Helm dla aplikacji Kubernetes przy użyciu repozytoriów w Azure Container Registry
ms.topic: article
ms.date: 04/15/2021
ms.openlocfilehash: c7dcdf222e9628daedb7e1c3617efb0b9c7af185
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772383"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Wypychanie i ściąganie wykresów programu Helm do rejestru kontenerów platformy Azure

Aby szybko zarządzać aplikacjami dla rozwiązania Kubernetes i wdrażać je, możesz użyć menedżera pakietów [Helm typu open source.][helm] W programie Helm pakiety aplikacji są definiowane jako [wykresy](https://helm.sh/docs/topics/charts/), które są zbierane i przechowywane w [repozytorium pakietów Programu Helm.](https://helm.sh/docs/topics/chart_repository/)

W tym artykule pokazano, jak hostować repozytoria pakietów helm w rejestrze kontenerów platformy Azure przy użyciu poleceń programu Helm 3. W wielu scenariuszach należy tworzyć i przekazywać własne wykresy dla aplikacji, które tworzysz. Aby uzyskać więcej informacji na temat tworzenia własnych wykresów programu Helm, zobacz Przewodnik dewelopera szablonu [wykresu.][develop-helm-charts] Możesz również przechowywać istniejący pakiet Helm z innego repo narzędzia Helm.

## <a name="helm-3-or-helm-2"></a>Helm 3 czy Helm 2?

Do przechowywania i instalowania wykresów programu Helm oraz zarządzania nimi należy użyć klienta programu Helm i interfejsu wiersza polecenia programu Helm. Główne wersje klienta helm to Helm 3 i Helm 2. Aby uzyskać szczegółowe informacje na temat różnic między wersjami, zobacz [często zadawane pytania dotyczące wersji.](https://helm.sh/docs/faq/) 

Helm 3 powinien być używany do hostowania wykresów helm w Azure Container Registry. W programie Helm 3:

* Może utworzyć co najmniej jedno repozytorium programu Helm w rejestrze kontenerów platformy Azure
* Przechowuj wykresy helm 3 w rejestrze jako [artefakty OCI.](container-registry-image-formats.md#oci-artifacts) Azure Container Registry obsługuje artefakty [OCI,](container-registry-oci-artifacts.md)w tym wykresy Helm, w gachodniej.
* Uwierzytelnij się w rejestrze za pomocą `helm registry login` polecenia .
* Używanie poleceń w interfejsie wiersza polecenia programu Helm do wypychania i ściągania wykresów `helm chart` Programu Helm oraz zarządzania nimi w rejestrze
* Użyj `helm install` funkcji , aby zainstalować wykresy w klastrze Kubernetes z lokalnej pamięci podręcznej repozytorium.
> [!NOTE]
> Od programu Helm 3 polecenia [az acr helm][az-acr-helm] do użycia z klientem helm 2 są przestarzałe. Powiadomienie z co najmniej 3-miesięcy zostanie dostarczone przed usunięciem polecenia. Jeśli wcześniej wdrożono wykresy helm 2, zobacz [Migrowanie usługi Helm w wersji 2 do wersji 3.](https://helm.sh/docs/topics/v2_v3_migration/)

## <a name="prerequisites"></a>Wymagania wstępne

W scenariuszu w tym artykule są potrzebne następujące zasoby:

- **Rejestr kontenerów platformy Azure** w ramach subskrypcji platformy Azure. W razie potrzeby utwórz rejestr przy użyciu interfejsu [Azure Portal](container-registry-get-started-portal.md) lub interfejsu wiersza polecenia [platformy Azure.](container-registry-get-started-azure-cli.md)
- **Klient Helm w wersji 3.1.0** lub nowszej — uruchom program , `helm version` aby znaleźć bieżącą wersję. Aby uzyskać więcej informacji na temat instalowania i uaktualniania programu Helm, zobacz [Instalowanie programu Helm.][helm-install]
- **Klaster Kubernetes,** w którym zostanie zainstalowany pakiet Helm. W razie potrzeby utwórz [klaster Azure Kubernetes Service klastra][aks-quickstart]. 
- Interfejs wiersza polecenia platformy Azure w wersji **2.0.71** lub nowszej — uruchom narzędzie , `az --version` aby znaleźć wersję. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="enable-oci-support"></a>Włączanie obsługi OCI

Użyj polecenia `helm version` , aby sprawdzić, czy zainstalowano program Helm 3:

```console
helm version
```

Ustaw następującą zmienną środowiskową, aby włączyć obsługę OCI w kliencie programu Helm 3. Obecnie ta obsługa jest eksperymentalna. 

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

W `templates` folderze utwórz plik o nazwie `configmap.yaml` , uruchamiając następujące polecenie:

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

Aby uzyskać więcej informacji na temat tworzenia i uruchamiania tego przykładu, [zobacz Wprowadzenie](https://helm.sh/docs/chart_template_guide/getting_started/) w dokumentów dotyczących helm.

## <a name="save-chart-to-local-registry-cache"></a>Zapisywanie wykresu w lokalnej pamięci podręcznej rejestru

Zmień katalog na `hello-world` podkatalog. Następnie uruchom plik , aby zapisać kopię wykresu lokalnie, a także utworzyć alias z w pełni kwalifikowaną nazwą rejestru (wszystkie małe litery) oraz docelowym repozytorium `helm chart save` i tagiem. 

W poniższym przykładzie nazwa rejestru to *mycontainerregistry,* docelowe repo to *hello-world,* a tag wykresu docelowego to *v1,* ale zastąp wartości dla swojego środowiska:

```console
cd ..
helm chart save . hello-world:v1
helm chart save . mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Uruchom `helm chart list` , aby potwierdzić zapisanie wykresów w lokalnej pamięci podręcznej rejestru. Dane wyjściowe są podobne do następujących:

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
hello-world:v1                                           hello-world       0.1.0   5899db0 3.2 KiB        2 minutes 
mycontainerregistry.azurecr.io/helm/hello-world:v1       hello-world       0.1.0   5899db0 3.2 KiB        2 minutes
```

## <a name="authenticate-with-the-registry"></a>Uwierzytelnianie za pomocą rejestru

Uruchom polecenie `helm registry login` w interfejsie wiersza [](container-registry-authentication.md) polecenia programu Helm 3, aby uwierzytelnić się w rejestrze przy użyciu poświadczeń odpowiednich dla Twojego scenariusza.

Na przykład utwórz jednostkę usługi Azure Active Directory z uprawnieniami ściągania i wypychania [(rola](container-registry-auth-service-principal.md#create-a-service-principal) AcrPush) do rejestru. Następnie należy podać poświadczenia jednostki usługi w usłudze `helm registry login` . Poniższy przykład dostarcza hasło przy użyciu zmiennej środowiskowej:

```console
echo $spPassword | helm registry login mycontainerregistry.azurecr.io \
  --username <service-principal-id> \
  --password-stdin
```

## <a name="push-chart-to-registry"></a>Wypychanie wykresu do rejestru

Uruchom polecenie `helm chart push` w interfejsie wiersza polecenia programu Helm 3, aby wypchnąć wykres do w pełni kwalifikowanego repozytorium docelowego:

```console
helm chart push mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Po pomyślnym wypchnąć dane wyjściowe są podobne do:

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/hello-world]
ref:     mycontainerregistry.azurecr.io/helm/hello-world:v1
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    3.2 KiB
name:    hello-world
version: 0.1.0
```

## <a name="list-charts-in-the-repository"></a>Lista wykresów w repozytorium

Podobnie jak w przypadku obrazów przechowywanych w rejestrze kontenerów platformy Azure, możesz użyć poleceń [az acr repository,][az-acr-repository] aby wyświetlić repozytoria hostowane na wykresach oraz tagi i manifesty wykresów. 

Na przykład uruchom [az acr repository show,][az-acr-repository-show] aby wyświetlić właściwości repozytorium utworzonego w poprzednim kroku:

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

Uruchom polecenie [az acr repository show-manifests,][az-acr-repository-show-manifests] aby wyświetlić szczegóły wykresu przechowywanego w repozytorium. Na przykład:

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/hello-world --detail
```

Dane wyjściowe, skrócone w tym przykładzie, pokazują `configMediaType` : `application/vnd.cncf.helm.config.v1+json`

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

Aby zainstalować pakiet Helm na platformie Kubernetes, musi on znajdować się w lokalnej pamięci podręcznej. W tym przykładzie najpierw uruchom plik , `helm chart remove` aby usunąć istniejący wykres lokalny o nazwie `mycontainerregistry.azurecr.io/helm/hello-world:v1` :

```console
helm chart remove mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Uruchom `helm chart pull` , aby pobrać wykres z rejestru kontenerów platformy Azure do lokalnej pamięci podręcznej:

```console
helm chart pull mycontainerregistry.azurecr.io/helm/hello-world:v1
```

## <a name="export-helm-chart"></a>Eksportowanie wykresu narzędzia Helm

Aby dalej pracować z wykresem, wyeksportuj go do katalogu lokalnego przy użyciu narzędzia `helm chart export` . Na przykład wyeksportuj wykres ściągniętą do `install` katalogu:

```console
helm chart export mycontainerregistry.azurecr.io/helm/hello-world:v1 \
  --destination ./install
```

Aby wyświetlić informacje dotyczące wyeksportowanego wykresu w repo, uruchom polecenie w katalogu, w `helm show chart` którym został wyeksportowany wykres.

```console
cd install
helm show chart hello-world
```

Program Helm zwraca szczegółowe informacje o najnowszej wersji pakietu, jak pokazano w następujących przykładowych danych wyjściowych:

```output
apiVersion: v2
appVersion: 1.16.0
description: A Helm chart for Kubernetes
name: hello-world
type: application
version: 0.1.0    
```

## <a name="install-helm-chart"></a>Instalowanie pakietu Helm

Uruchom `helm install` , aby zainstalować pakiet Helm, który został ściągnięty do lokalnej pamięci podręcznej i wyeksportowany. Określ nazwę wydania, taką jak *myhelmtest,* lub przekaż `--generate-name` parametr . Na przykład:

```console
helm install myhelmtest ./hello-world
```

Dane wyjściowe po pomyślnym zainstalowaniu pakietu są podobne do:

```console
NAME: myhelmtest
LAST DEPLOYED: Fri Mar 20 14:14:42 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

Aby zweryfikować instalację, uruchom `helm get manifest` polecenie . 

```console
helm get manifest myhelmtest
```

Polecenie zwraca dane YAML w `configmap.yaml` pliku szablonu.

Uruchom `helm uninstall` , aby odinstalować wydanie wykresu w klastrze:

```console
helm uninstall myhelmtest
```

## <a name="delete-chart-from-the-registry"></a>Usuwanie wykresu z rejestru

Aby usunąć wykres z rejestru kontenerów, użyj [polecenia az acr repository delete.][az-acr-repository-delete] Uruchom następujące polecenie i potwierdź operację po wyświetleniu monitu:

```azurecli
az acr repository delete --name mycontainerregistry --image helm/hello-world:v1
```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat tworzenia i wdrażania wykresów usługi Helm, zobacz [Tworzenie wykresów helm.][develop-helm-charts]
* Dowiedz się więcej o instalowaniu aplikacji za pomocą programu Helm [w Azure Kubernetes Service (AKS).](../aks/kubernetes-helm.md)
* Wykresy helm mogą być używane jako część procesu kompilacji kontenera. Aby uzyskać więcej informacji, zobacz [Używanie zadania usługi Azure Container Registry][acr-tasks].

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://helm.sh/docs/intro/install/
[develop-helm-charts]: https://helm.sh/docs/chart_template_guide/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az_configure
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-helm]: /cli/azure/acr/helm
[az-acr-repository]: /cli/azure/acr/repository
[az-acr-repository-show]: /cli/azure/acr/repository#az_acr_repository_show
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az_acr_repository_show_tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az_acr_repository_show_manifests
[acr-tasks]: container-registry-tasks-overview.md
