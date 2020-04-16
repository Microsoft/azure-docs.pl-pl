---
title: Wykresy helm sklepu
description: Dowiedz się, jak przechowywać wykresy helm dla aplikacji Kubernetes przy użyciu repozytoriów w rejestrze kontenerów platformy Azure
ms.topic: article
ms.date: 03/20/2020
ms.openlocfilehash: 3f1a68258b758380a66b63e3c3137f1d460d288c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399388"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Wypychanie i ściąganie wykresów helm do rejestru kontenerów platformy Azure

Aby szybko zarządzać aplikacjami dla aplikacji Kubernetes i wdrażać je, można użyć [menedżera pakietów Helm typu open source.][helm] Za pomocą programu Helm pakiety aplikacji są definiowane jako [wykresy,](https://helm.sh/docs/topics/charts/)które są zbierane i przechowywane w [repozytorium wykresu Helm.](https://helm.sh/docs/topics/chart_repository/)

W tym artykule pokazano, jak hostować repozytoria wykresów Helm w rejestrze kontenerów platformy Azure przy użyciu instalacji Helm 3 lub Helm 2. W wielu scenariuszach można utworzyć i przekazać własne wykresy dla aplikacji, które tworzysz. Aby uzyskać więcej informacji na temat tworzenia własnych wykresów Helm, zobacz [Przewodnik dla deweloperów szablonów wykresów][develop-helm-charts]. Można również zapisać istniejący wykres Helm z innego repozytorium Helm.

> [!IMPORTANT]
> Obsługa wykresów helm w rejestrze kontenerów platformy Azure jest obecnie w wersji zapoznawczej. Podglądy są udostępniane pod warunkiem, że zgadzasz się na dodatkowe [warunki użytkowania.][terms-of-use] Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="helm-3-or-helm-2"></a>Helm 3 lub Helm 2?

Do przechowywania wykresów Helm, zarządzania nimi i instalowania należy użyć klienta Helm i interfejsu wiersza polecenia helmu. Główne wydania klienta Helm to Helm 3 i Helm 2. Helm 3 obsługuje nowy format wykresu i nie instaluje już składnika po stronie serwera Tiller. Aby uzyskać szczegółowe informacje na temat różnic w wersji, zobacz często zadawane pytania [dotyczące wersji](https://helm.sh/docs/faq/). Jeśli wcześniej wdrożono wykresy Helm 2, zobacz [Migrowanie helma w wersji 2 do wersji 3](https://helm.sh/docs/topics/v2_v3_migration/).

Za pomocą helm 3 lub Helm 2 można hostować wykresy Helm w rejestrze kontenerów platformy Azure, z przepływami pracy specyficznymi dla każdej wersji:

* [Helm 3](#use-the-helm-3-client) client `helm chart` - użyj poleceń w interfejsu wiersza polecenia Helm do zarządzania wykresami w rejestrze jako [artefakty OCI](container-registry-image-formats.md#oci-artifacts)
* [Klient Helm 2](#use-the-helm-2-client) — [dodawanie][az-acr-helm] rejestru kontenerów jako repozytorium wykresu Helm i zarządzanie nim jako repozytorium wykresów Helm

### <a name="additional-information"></a>Dodatkowe informacje

* W większości scenariuszy zaleca się używanie przepływu pracy `helm chart` Helm 3 z poleceniami macierzystymi do zarządzania wykresami jako artefaktami OCI.
* Za pomocą starszych poleceń interfejsu [wiersza][az-acr-helm] polecenia platformy Azure i przepływu pracy można używać starszych poleceń interfejsu wiersza polecenia platformy Azure i przepływu pracy z klientem helm 3 i wykresami. Jednak niektóre polecenia, `az acr helm list` takie jak nie są zgodne z wykresami Helm 3.
* Od Helm 3 polecenia [az acr steru][az-acr-helm] są obsługiwane głównie ze względu na zgodność z klientem Helm 2 i formatem wykresu. Przyszłe opracowanie tych poleceń nie jest obecnie planowane.

## <a name="use-the-helm-3-client"></a>Korzystanie z klienta Helm 3

### <a name="prerequisites"></a>Wymagania wstępne

- **Rejestr kontenerów platformy Azure** w subskrypcji platformy Azure. W razie potrzeby utwórz rejestr przy użyciu [witryny Azure portal](container-registry-get-started-portal.md) lub [interfejsu wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md).
- **Helm klienta w wersji 3.1.0 lub nowszej** — uruchom, `helm version` aby znaleźć bieżącą wersję. Aby uzyskać więcej informacji na temat instalowania i uaktualniania helm, zobacz [Instalowanie helm][helm-install].
- **Klaster Kubernetes,** w którym zostanie zainstalowany wykres Helm. W razie potrzeby utwórz [klaster usługi Azure Kubernetes][aks-quickstart]. 
- **Interfejsu wiersza polecenia platformy Azure w wersji 2.0.71 lub nowszej** — uruchom, `az --version` aby znaleźć wersję. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

### <a name="high-level-workflow"></a>Przepływ pracy wysokiego poziomu

Z **Helm 3** ty:

* Można utworzyć co najmniej jedno repozytoria helm w rejestrze kontenerów platformy Azure
* Przechowuj wykresy Helm 3 w rejestrze jako [artefakty OCI](container-registry-image-formats.md#oci-artifacts). Obecnie obsługa Helm 3 dla OCI jest *eksperymentalna.*
* Uwierzytelnij się w `helm registry login` rejestrze za pomocą polecenia.
* Używanie `helm chart` poleceń w wierszu polecenia Helm CLI do wypychania, ściągania i zarządzania wykresami helm w rejestrze
* Służy `helm install` do instalowania wykresów w klastrze Kubernetes z lokalnej pamięci podręcznej repozytorium.

Zobacz poniższe sekcje, aby zapoznać się z przykładami.

### <a name="enable-oci-support"></a>Włącz obsługę OCI

Ustaw następującą zmienną środowiskową, aby włączyć obsługę OCI w kliencie Helm 3. Obecnie ta obsługa jest eksperymentalna. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

### <a name="create-a-sample-chart"></a>Tworzenie przykładowego wykresu

Utwórz wykres testowy przy użyciu następujących poleceń:

```console
mkdir helmtest

cd helmtest
helm create hello-world
```

Na przykład należy zmienić katalog `templates` na folder i najpierw usunąć tam zawartość:

```console
cd hello-world/templates
rm -rf *
```

W `templates` folderze utwórz `configmap.yaml` plik o nazwie o następującej zawartości:

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

Aby uzyskać więcej informacji na temat tworzenia i uruchamiania w tym przykładzie, zobacz [Wprowadzenie](https://helm.sh/docs/chart_template_guide/getting_started/) w dokumenty helm.

### <a name="save-chart-to-local-registry-cache"></a>Zapisywanie wykresu w lokalnej pamięci podręcznej rejestru

Zmień katalog na `hello-world` podkatalog. Następnie uruchom, `helm chart save` aby zapisać kopię wykresu lokalnie, a także utworzyć alias z w pełni kwalifikowaną nazwą rejestru (wszystkie małe litery) oraz repozytorium i tag docelowy. 

W poniższym przykładzie nazwa rejestru to *mycontainerregistry*, repozytorium docelowe to *hello-world,* a docelowy znacznik wykresu to *v1*, ale wartości zastępcze dla środowiska:

```console
cd ..
helm chart save . hello-world:v1
helm chart save . mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Uruchom, `helm chart list` aby potwierdzić, że zapisano wykresy w lokalnej pamięci podręcznej rejestru. Dane wyjściowe są podobne do następujących:

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
hello-world:v1                                           hello-world       0.1.0   5899db0 3.2 KiB        2 minutes 
mycontainerregistry.azurecr.io/helm/hello-world:v1       hello-world       0.1.0   5899db0 3.2 KiB        2 minutes
```

### <a name="authenticate-with-the-registry"></a>Uwierzytelnij się w rejestrze

Uruchom `helm registry login` polecenie w interfejsu wiersza polecenia Helm 3, aby [uwierzytelnić się w rejestrze](container-registry-authentication.md) przy użyciu poświadczeń odpowiednich dla danego scenariusza.

Na przykład utwórz jednostkę usługi Azure Active Directory [z uprawnieniami ściągania i wypychania](container-registry-auth-service-principal.md#create-a-service-principal) (rola AcrPush) do rejestru. Następnie podać poświadczenia jednostki `helm registry login`usługi do . Poniższy przykład dostarcza hasło przy użyciu zmiennej środowiskowej:

```console
echo $spPassword | helm registry login mycontainerregistry.azurecr.io \
  --username <service-principal-id> \
  --password-stdin
```

### <a name="push-chart-to-azure-container-registry"></a>Wykres wypychania do rejestru kontenerów platformy Azure

Uruchom `helm chart push` polecenie w wierszu polecenia Helm 3, aby wypchnąć wykres do w pełni kwalifikowanego repozytorium docelowego:

```console
helm chart push mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Po pomyślnym wypchnięciu wyjście jest podobne do:

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/hello-world]
ref:     mycontainerregistry.azurecr.io/helm/hello-world:v1
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    3.2 KiB
name:    hello-world
version: 0.1.0
```

### <a name="list-charts-in-the-repository"></a>Lista wykresów w repozytorium

Podobnie jak w przypadku obrazów przechowywanych w rejestrze kontenerów platformy Azure, można użyć [az acr poleceń repozytorium,][az-acr-repository] aby wyświetlić repozytoria hostowania wykresów i znaczników wykresu i manifestów. 

Na przykład uruchom [az acr repozytorium show,][az-acr-repository-show] aby wyświetlić właściwości repozytorium utworzonego w poprzednim kroku:

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

Uruchom polecenie [az acr repozytorium show-manifests,][az-acr-repository-show-manifests] aby wyświetlić szczegóły wykresu przechowywanego w repozytorium. Przykład:

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/hello-world --detail
```

Dane wyjściowe, w skrócie w `configMediaType` `application/vnd.cncf.helm.config.v1+json`tym przykładzie, pokazuje:

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

### <a name="pull-chart-to-local-cache"></a>Ciągnienie wykresu do lokalnej pamięci podręcznej

Aby zainstalować wykres Helm do kubernetes, wykres musi znajdować się w lokalnej pamięci podręcznej. W tym przykładzie `helm chart remove` najpierw uruchom, aby `mycontainerregistry.azurecr.io/helm/hello-world:v1`usunąć istniejący wykres lokalny o nazwie:

```console
helm chart remove mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Uruchom, `helm chart pull` aby pobrać wykres z rejestru kontenerów platformy Azure do lokalnej pamięci podręcznej:

```console
helm chart pull mycontainerregistry.azurecr.io/helm/hello-world:v1
```

### <a name="export-helm-chart"></a>Wykres Helm eksportu

Aby pracować dalej z wykresem, wyeksportuj go do katalogu lokalnego za pomocą programu `helm chart export`. Na przykład wyeksportuj `install` wykres pobrany do katalogu:

```console
helm chart export mycontainerregistry.azurecr.io/helm/hello-world:v1 \
  --destination ./install
```

Aby wyświetlić informacje dotyczące wyeksportowanego `helm show chart` wykresu w repozytorium, uruchom polecenie w katalogu, w którym wyeksportowano wykres.

```console
cd install
helm show chart hello-world
```

Helm zwraca szczegółowe informacje o najnowszej wersji wykresu, jak pokazano na poniższym przykładowym wyjściu:

```output
apiVersion: v2
appVersion: 1.16.0
description: A Helm chart for Kubernetes
name: hello-world
type: application
version: 0.1.0    
```

### <a name="install-helm-chart"></a>Zainstaluj wykres Helm

Uruchom, `helm install` aby zainstalować wykres Helm, który został pobrany do lokalnej pamięci podręcznej i wyeksportowany. Określ nazwę wydania, taką jak *myhelmtest,* lub przekaż `--generate-name` parametr. Przykład:

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

Aby zweryfikować instalację, uruchom `helm get manifest` polecenie. Polecenie zwraca dane YAML `configmap.yaml` w pliku szablonu.

Uruchom, `helm uninstall` aby odinstalować wersję wykresu w klastrze:

```console
helm uninstall myhelmtest
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Usuwanie wykresu helm z repozytorium

Aby usunąć wykres z repozytorium, użyj polecenia [az acr repozytorium delete.][az-acr-repository-delete] Uruchom następujące polecenie i potwierdź operację po wyświetleniu monitu:

```azurecli
az acr repository delete --name mycontainerregistry --image helm/hello-world:v1
```

## <a name="use-the-helm-2-client"></a>Korzystanie z klienta Helm 2

### <a name="prerequisites"></a>Wymagania wstępne

- **Rejestr kontenerów platformy Azure** w subskrypcji platformy Azure. W razie potrzeby utwórz rejestr przy użyciu [witryny Azure portal](container-registry-get-started-portal.md) lub [interfejsu wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md).
- **Helm client w wersji 2.11.0 (nie w wersji RC) lub nowszej** — uruchom, `helm version` aby znaleźć bieżącą wersję. Potrzebny jest również serwer Helm (Tiller) zainicjowany w klastrze Kubernetes. W razie potrzeby utwórz [klaster usługi Azure Kubernetes][aks-quickstart]. Aby uzyskać więcej informacji na temat instalowania i uaktualniania helm, zobacz [Instalowanie helm][helm-install-v2].
- **Interfejsu wiersza polecenia platformy Azure w wersji 2.0.46 lub nowszej** — uruchom, `az --version` aby znaleźć wersję. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

### <a name="high-level-workflow"></a>Przepływ pracy wysokiego poziomu

Z **Helm 2** ty:

* Skonfiguruj rejestr kontenerów platformy Azure jako *jedno* repozytorium wykresu Helm. Usługa Azure Container Registry zarządza definicją indeksu podczas dodawania i usuwania wykresów do repozytorium.
* Uwierzytelnij się za pomocą rejestru kontenerów platformy Azure za pośrednictwem interfejsu wiersza polecenia platformy Azure, który następnie automatycznie aktualizuje klienta helmu za pomocą identyfikatora URI rejestru i poświadczeń. Nie trzeba ręcznie określić te informacje rejestru, więc poświadczenia nie są widoczne w historii poleceń.
* Użyj [az acr polecenia helm][az-acr-helm] w narzędzia Azure CLI, aby dodać rejestr kontenerów platformy Azure jako repozytorium wykresu Helm i do wypychania i zarządzania wykresami. Te polecenia interfejsu wiersza polecenia platformy Azure są zawijane polecenia klienta Helm 2.
* Dodaj repozytorium wykresu w rejestrze kontenerów platformy Azure do lokalnego indeksu repozytorium Helm, obsługującego wyszukiwanie wykresów.
* Służy `helm install` do instalowania wykresów w klastrze Kubernetes z lokalnej pamięci podręcznej repozytorium.

Zobacz poniższe sekcje, aby zapoznać się z przykładami.

### <a name="add-repository-to-helm-client"></a>Dodawanie repozytorium do klienta helm

Dodaj repozytorium wykresu helm rejestru kontenera platformy Azure do klienta helmu za pomocą polecenia [dodawania repozytorium az acr helm.][az-acr-helm-repo-add] To polecenie pobiera token uwierzytelniania dla rejestru kontenerów platformy Azure, który jest używany przez klienta helm. Token uwierzytelniania jest ważny przez 3 godziny. Podobnie `docker login`jak w programach , można uruchomić to polecenie w przyszłych sesjach interfejsu wiersza polecenia, aby uwierzytelnić klienta helmu za pomocą repozytorium wykresów helm rejestru kontenerów platformy Azure:

```azurecli
az acr helm repo add --name mycontainerregistry
```

### <a name="add-a-sample-chart-to-the-repository"></a>Dodawanie przykładowego wykresu do repozytorium

Najpierw utwórz katalog lokalny w *~/acr-helm,* a następnie pobierz istniejący wykres *stabilny/wordpress:*

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm repo update
helm fetch stable/wordpress
```

Wpisz, `ls` aby wyświetlić listę pobranych wykresów, i zanotuj wersję Wordpressa zawartą w nazwach plików. Polecenie `helm fetch stable/wordpress` nie określiło określonej wersji, więc *pobrano najnowszą* wersję. W poniższym przykładzie danych wyjściowych wykres Wordpress jest w wersji *8.1.0:*

```output
wordpress-8.1.0.tgz
```

Wypychaj wykres do repozytorium wykresu Helm w rejestrze kontenerów platformy Azure przy użyciu polecenia [wypychania az acr helm][az-acr-helm-push] w usłudze Azure CLI. Określ nazwę wykresu Helm pobranego w poprzednim kroku, takiego jak *wordpress-8.1.0.tgz*:

```azurecli
az acr helm push --name mycontainerregistry wordpress-8.1.0.tgz
```

Po kilku chwilach interfejsu wiersza polecenia platformy Azure raportuje, że wykres jest zapisywany, jak pokazano w poniższym przykładzie danych wyjściowych:

```output
{
  "saved": true
}
```

### <a name="list-charts-in-the-repository"></a>Lista wykresów w repozytorium

Aby użyć wykresu przekazanego w poprzednim kroku, lokalny indeks repozytorium Helm musi zostać zaktualizowany. Można ponownie indeksować repozytoria w kliencie helm lub użyć interfejsu wiersza polecenia platformy Azure, aby zaktualizować indeks repozytorium. Za każdym razem, gdy dodajesz wykres do repozytorium, ten krok musi zostać wykonany:

```azurecli
az acr helm repo add --name mycontainerregistry
```

Z wykresu przechowywane w repozytorium i zaktualizowany indeks dostępny lokalnie, można użyć zwykłych poleceń klienta Helm do wyszukiwania lub instalacji. Aby wyświetlić wszystkie wykresy w repozytorium, użyj `helm search` polecenia, podając własną nazwę rejestru kontenerów platformy Azure:

```console
helm search mycontainerregistry
```

Wykres Wordpress wypchnięty w poprzednim kroku jest wymieniony, jak pokazano w poniższym przykładzie danych wyjściowych:

```output
NAME                  CHART VERSION    APP VERSION    DESCRIPTION
helmdocs/wordpress    8.1.0           5.3.2          Web publishing platform for building blogs and websites.
```

Można również wyświetlić listę wykresów za pomocą interfejsu wiersza polecenia platformy Azure, używając [listy az acr helm:][az-acr-helm-list]

```azurecli
az acr helm list --name mycontainerregistry
```

### <a name="show-information-for-a-helm-chart"></a>Pokazywale wykresu Helm

Aby wyświetlić informacje dotyczące określonego wykresu w `helm inspect` repozytorium, można użyć polecenia.

```console
helm inspect mycontainerregistry/wordpress
```

Jeśli nie podano numeru wersji, używana jest *najnowsza* wersja. Helm zwraca szczegółowe informacje o wykresie, jak pokazano na poniższym skróconym przykładzie:

```output
apiVersion: v1
appVersion: 5.3.2
description: Web publishing platform for building blogs and websites.
engine: gotpl
home: http://www.wordpress.com/
icon: https://bitnami.com/assets/stacks/wordpress/img/wordpress-stack-220x234.png
keywords:
- wordpress
- cms
- blog
- http
- web
- application
- php
maintainers:
- email: containers@bitnami.com
  name: Bitnami
name: wordpress
sources:
- https://github.com/bitnami/bitnami-docker-wordpress
version: 8.1.0
[...]
```

Można również wyświetlić informacje dotyczące wykresu za pomocą polecenia [azure cli az acr helm show.][az-acr-helm-show] Ponownie, *najnowsza* wersja wykresu jest zwracana domyślnie. Można dołączyć `--version` do listy określoną wersję wykresu, taką jak *8.1.0*:

```azurecli
az acr helm show --name mycontainerregistry wordpress
```

### <a name="install-a-helm-chart-from-the-repository"></a>Instalowanie wykresu helm z repozytorium

Wykres Helm w repozytorium jest instalowany przez określenie nazwy repozytorium i nazwy wykresu. Użyj klienta Helm, aby zainstalować wykres Wordpress:

```console
helm install mycontainerregistry/wordpress
```

> [!TIP]
> Jeśli wypchniesz do repozytorium wykresu Helm rejestru kontenerów platformy Azure, a później zwrócisz w nowej sesji interfejsu wiersza polecenia, lokalny klient helmu potrzebuje zaktualizowanego tokenu uwierzytelniania. Aby uzyskać nowy token uwierzytelniania, użyj polecenia [az acr helm repo add.][az-acr-helm-repo-add]

Podczas procesu instalacji wykonywane są następujące kroki:

- Klient Helm przeszukuje indeks lokalnego repozytorium.
- Odpowiedni wykres jest pobierany z repozytorium rejestru kontenerów platformy Azure.
- Wykres jest wdrażany przy użyciu kultywatora w klastrze Kubernetes.

W miarę postępu instalacji postępuj zgodnie z instrukcjami w danych wyjściowych polecenia, aby wyświetlić adresy URL i poświadczenia WorPress. Można również uruchomić `kubectl get pods` polecenie, aby wyświetlić zasoby Kubernetes wdrożone za pośrednictwem wykresu Helm:

```output
NAME                                    READY   STATUS    RESTARTS   AGE
wordpress-1598530621-67c77b6d86-7ldv4   1/1     Running   0          2m48s
wordpress-1598530621-mariadb-0          1/1     Running   0          2m48s
[...]
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Usuwanie wykresu helm z repozytorium

Aby usunąć wykres z repozytorium, użyj polecenia [az acr helm delete.][az-acr-helm-delete] Określ nazwę wykresu, taką jak *wordpress,* i wersję do usunięcia, na przykład *8.1.0*.

```azurecli
az acr helm delete --name mycontainerregistry wordpress --version 8.1.0
```

Jeśli chcesz usunąć wszystkie wersje nazwanego wykresu, `--version` pozostaw parametr.

Wykres jest nadal zwracany po `helm search`uruchomieniu . Ponownie klient Helm nie aktualizuje automatycznie listy dostępnych wykresów w repozytorium. Aby zaktualizować indeks repozytorium klienta Helm, użyj polecenia [az acr helm repo add][az-acr-helm-repo-add] ponownie:

```azurecli
az acr helm repo add --name mycontainerregistry
```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat tworzenia i wdrażania wykresów helm, zobacz [Tworzenie wykresów helm][develop-helm-charts].
* Dowiedz się więcej o instalowaniu aplikacji z usługą Helm w [usłudze Azure Kubernetes Service (AKS).](../aks/kubernetes-helm.md)
* Wykresy helm może służyć jako część procesu kompilacji kontenera. Aby uzyskać więcej informacji, zobacz [Korzystanie z zadań rejestru kontenerów platformy Azure][acr-tasks].

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-v2]: https://v2.helm.sh/docs/using_helm/#installing-helm
[develop-helm-charts]: https://helm.sh/docs/chart_template_guide/
[semver2]: https://semver.org/
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
[az-acr-helm-repo-add]: /cli/azure/acr/helm/repo#az-acr-helm-repo-add
[az-acr-helm-push]: /cli/azure/acr/helm#az-acr-helm-push
[az-acr-helm-list]: /cli/azure/acr/helm#az-acr-helm-list
[az-acr-helm-show]: /cli/azure/acr/helm#az-acr-helm-show
[az-acr-helm-delete]: /cli/azure/acr/helm#az-acr-helm-delete
[acr-tasks]: container-registry-tasks-overview.md
