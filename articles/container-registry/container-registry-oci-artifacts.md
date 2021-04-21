---
title: Wypychanie i ściąganie artefaktu OCI
description: Wypychanie i ściąganie artefaktów open container initiative (OCI) przy użyciu prywatnego rejestru kontenerów na platformie Azure
author: SteveLasker
manager: gwallace
ms.topic: article
ms.date: 02/03/2021
ms.author: stevelas
ms.openlocfilehash: 399bb001432759556cd0ba8bf15f7738dd4edb7c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781491"
---
# <a name="push-and-pull-an-oci-artifact-using-an-azure-container-registry"></a>Wypychanie i ściąganie artefaktu OCI przy użyciu rejestru kontenerów platformy Azure

Usługa Azure Container Registry umożliwia przechowywanie artefaktów [open container initiative (OCI)](container-registry-image-formats.md#oci-artifacts) oraz obrazów kontenerów zgodnych z platformą Docker i platformą Docker oraz zarządzanie nimi.

Aby zademonstrować tę możliwość, w tym artykule pokazano, jak za pomocą narzędzia [OCI Registry as Storage (ORAS)](https://github.com/deislabs/oras) wypchnąć przykładowy artefakt — plik tekstowy — do rejestru kontenerów platformy Azure. Następnie ściągnij artefakt z rejestru. Możesz zarządzać różnymi artefaktami OCI w rejestrze kontenerów platformy Azure przy użyciu różnych narzędzi wiersza polecenia odpowiednich dla każdego artefaktu.

## <a name="prerequisites"></a>Wymagania wstępne

* **Usługa Azure Container Registry** — Tworzy rejestr kontenera w subskrypcji platformy Azure. Na przykład użyj interfejsu [wiersza Azure Portal](container-registry-get-started-portal.md) platformy [Azure.](container-registry-get-started-azure-cli.md)
* **Narzędzie ORAS** — pobierz i zainstaluj bieżącą wersję ORAS dla swojego systemu operacyjnego z [repozytorium GitHub.](https://github.com/deislabs/oras/releases) Narzędzie jest zwalniane jako skompresowany plik tarball `.tar.gz` (). Wyodrębnij i zainstaluj plik przy użyciu standardowych procedur dla systemu operacyjnego.
* **Azure Active Directory usługi (opcjonalnie)** — aby uwierzytelniać się bezpośrednio w usłudze ORAS, utwórz jednostkę usługi w celu [uzyskania](container-registry-auth-service-principal.md) dostępu do rejestru. Upewnij się, że do jednostki usługi przypisano rolę, taką jak AcrPush, aby mieć uprawnienia do wypychania i ściągania artefaktów.
* **Interfejs wiersza polecenia platformy Azure (opcjonalnie)** — aby użyć indywidualnej tożsamości, musisz mieć lokalną instalację interfejsu wiersza polecenia platformy Azure. Zalecana jest wersja 2.0.71 lub nowsza. Uruchom `az --version ` , aby znaleźć wersję. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
* **Docker (opcjonalnie)** — aby użyć indywidualnej tożsamości, musisz mieć również lokalnie zainstalowaną aplikację Docker w celu uwierzytelnienia w rejestrze. Środowisko Docker zawiera pakiety, które umożliwiają łatwe konfigurowanie platformy Docker w systemie [macOS][docker-mac], [Windows][docker-windows] lub [Linux][docker-linux].


## <a name="sign-in-to-a-registry"></a>Logowanie do rejestru

W tej sekcji przedstawiono dwa sugerowane przepływy pracy logowania do rejestru, w zależności od używanej tożsamości. Wybierz metodę odpowiednią dla Twojego środowiska.

### <a name="sign-in-with-oras"></a>Logowanie przy użyciu usług ORAS

Używając [jednostki usługi](container-registry-auth-service-principal.md) z prawami wypychania, uruchom polecenie , aby zalogować się do rejestru przy użyciu identyfikatora aplikacji jednostki usługi `oras login` i hasła. Określ w pełni kwalifikowaną nazwę rejestru (wszystkie małe litery), w tym przypadku *myregistry.azurecr.io*. Identyfikator aplikacji jednostki usługi jest przekazywany w zmiennej środowiskowej `$SP_APP_ID` , a hasło w zmiennej `$SP_PASSWD` .

```bash
oras login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Aby odczytać hasło ze stdin, `--password-stdin` użyj .

### <a name="sign-in-with-azure-cli"></a>Logowanie się za pomocą interfejsu wiersza polecenia platformy Azure

[Zaloguj się do](/cli/azure/authenticate-azure-cli) interfejsu wiersza polecenia platformy Azure przy użyciu swojej tożsamości, aby wypychać i ściągać artefakty z rejestru kontenerów.

Następnie użyj polecenia interfejsu wiersza polecenia platformy Azure [az acr login,](/cli/azure/acr#az_acr_login) aby uzyskać dostęp do rejestru. Na przykład w celu uwierzytelnienia w rejestrze o *nazwie myregistry:*

```azurecli
az login
az acr login --name myregistry
```

> [!NOTE]
> `az acr login` używa klienta platformy Docker do Azure Active Directory tokenu w `docker.config` pliku. Aby ukończyć przepływ uwierzytelniania indywidualnego, należy zainstalować i uruchomiony klient platformy Docker.

## <a name="push-an-artifact"></a>Wypychanie artefaktu

Utwórz plik tekstowy w lokalnym katalogu pracy z przykładowym tekstem. Na przykład w powłoce bash:

```bash
echo "Here is an artifact" > artifact.txt
```

Użyj polecenia `oras push` , aby wypchnąć ten plik tekstowy do rejestru. Poniższy przykład wypycha przykładowy plik tekstowy do `samples/artifact` repo. Rejestr jest identyfikowany za pomocą w pełni kwalifikowanej nazwy *myregistry.azurecr.io* (wszystkie małe litery). Artefakt jest oznaczony tagiem `1.0` . Artefakt ma domyślnie niezdefiniowany typ identyfikowany przez *ciąg* typu nośnika następujący po nazwie pliku `artifact.txt` . Zobacz [Artefakty OCI,](https://github.com/opencontainers/artifacts) aby uzyskać dodatkowe typy. 

**Linux lub macOS**

```bash
oras push myregistry.azurecr.io/samples/artifact:1.0 \
    --manifest-config /dev/null:application/vnd.unknown.config.v1+json \
    ./artifact.txt:application/vnd.unknown.layer.v1+txt
```

**Windows**

```cmd
.\oras.exe push myregistry.azurecr.io/samples/artifact:1.0 ^
    --manifest-config NUL:application/vnd.unknown.config.v1+json ^
    .\artifact.txt:application/vnd.unknown.layer.v1+txt
```

Dane wyjściowe pomyślnego wypychania są podobne do następujących:

```console
Uploading 33998889555f artifact.txt
Pushed myregistry.azurecr.io/samples/artifact:1.0
Digest: sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx
```

Aby zarządzać artefaktami w rejestrze, jeśli używasz interfejsu wiersza polecenia platformy Azure, uruchom standardowe polecenia `az acr` do zarządzania obrazami. Na przykład pobierz atrybuty artefaktu za pomocą polecenia [az acr repository show:][az-acr-repository-show]

```azurecli
az acr repository show \
    --name myregistry \
    --image samples/artifact:1.0
```

Dane wyjściowe będą podobne do następujących:

```json
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2019-08-28T20:43:31.0001687Z",
  "digest": "sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx",
  "lastUpdateTime": "2019-08-28T20:43:31.0001687Z",
  "name": "1.0",
  "signed": false
}
```

## <a name="pull-an-artifact"></a>Ściąganie artefaktu

Uruchom polecenie `oras pull` , aby ściągnąć artefakt z rejestru.

Najpierw usuń plik tekstowy z lokalnego katalogu roboczego:

```bash
rm artifact.txt
```

Uruchom `oras pull` , aby ściągnąć artefakt, i określ typ nośnika używany do wypychania artefaktu:

```bash
oras pull myregistry.azurecr.io/samples/artifact:1.0 \
    --media-type application/vnd.unknown.layer.v1+txt
```

Sprawdź, czy ściągnięcie powiodło się:

```bash
$ cat artifact.txt
Here is an artifact
```

## <a name="remove-the-artifact-optional"></a>Usuwanie artefaktu (opcjonalnie)

Aby usunąć artefakt z rejestru kontenerów platformy Azure, użyj [polecenia az acr repository delete.][az-acr-repository-delete] Poniższy przykład usuwa zapisany w tym miejscu artefakt:

```azurecli
az acr repository delete \
    --name myregistry \
    --image samples/artifact:1.0
```

## <a name="example-build-docker-image-from-oci-artifact"></a>Przykład: Kompilowanie obrazu platformy Docker z artefaktu OCI

Kod źródłowy i pliki binarne do kompilowania obrazu kontenera mogą być przechowywane jako artefakty OCI w rejestrze kontenerów platformy Azure. Możesz odwoływać się do artefaktu źródłowego jako kontekstu kompilacji dla zadania [ACR](container-registry-tasks-overview.md). W tym przykładzie pokazano, jak przechowywać plik Dockerfile jako artefakt OCI, a następnie odwoływać się do artefaktu w celu skompilowania obrazu kontenera.

Na przykład utwórz plik Dockerfile z jednym wierszem:

```bash
echo "FROM mcr.microsoft.com/hello-world" > hello-world.dockerfile
```

Zaloguj się do docelowego rejestru kontenerów.

```azurecli
az login
az acr login --name myregistry
```

Utwórz i wypchń nowy artefakt OCI do rejestru docelowego za pomocą `oras push` polecenia . W tym przykładzie ustawiany jest domyślny typ nośnika artefaktu.

```bash
oras push myregistry.azurecr.io/dockerfile:1.0 hello-world.dockerfile
```

Uruchom polecenie [az acr build,](/cli/azure/acr#az_acr_build) aby skompilować obraz hello-world przy użyciu nowego artefaktu jako kontekstu kompilacji:

```azurecli
az acr build --registry myregistry --image builds/hello-world:v1 \
  --file hello-world.dockerfile \
  oci://myregistry.azurecr.io/dockerfile:1.0
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej [na temat biblioteki ORAS,](https://github.com/deislabs/oras/tree/master/docs)w tym sposobu konfigurowania manifestu dla artefaktu
* Odwiedź [repo OCI Artifacts (Artefakty OCI),](https://github.com/opencontainers/artifacts) aby uzyskać informacje referencyjne dotyczące nowych typów artefaktów



<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az_acr_repository_show
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
