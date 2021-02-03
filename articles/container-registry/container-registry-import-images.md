---
title: Importowanie obrazów kontenerów
description: Zaimportuj obrazy kontenerów do usługi Azure Container Registry za pomocą interfejsów API platformy Azure bez konieczności uruchamiania poleceń platformy Docker.
ms.topic: article
ms.date: 01/15/2021
ms.openlocfilehash: 364c90b857d0d7d479152e2aa56db4d80041f037
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99524502"
---
# <a name="import-container-images-to-a-container-registry"></a>Importowanie obrazów kontenera do rejestru kontenerów

Można łatwo importować (kopiować) obrazy kontenerów do usługi Azure Container Registry bez używania poleceń platformy Docker. Na przykład zaimportuj obrazy z rejestru projektowego do rejestru produkcyjnego lub skopiuj obrazy podstawowe z rejestru publicznego.

Azure Container Registry obsługuje wiele typowych scenariuszy kopiowania obrazów z istniejącego rejestru:

* Importuj z rejestru publicznego

* Importuj z innego rejestru kontenera platformy Azure, w ramach tej samej lub innej subskrypcji lub dzierżawy platformy Azure

* Importuj z rejestru kontenerów prywatnych spoza platformy Azure

Importowanie obrazów do usługi Azure Container Registry ma następujące zalety, aby korzystać z poleceń interfejsu wiersza polecenia platformy Docker:

* Ze względu na to, że środowisko klienta nie wymaga lokalnej instalacji platformy Docker, zaimportuj dowolny obraz kontenera niezależnie od obsługiwanego typu systemu operacyjnego.

* Podczas importowania obrazów wieloarchitekturowych (takich jak oficjalne obrazy platformy Docker) obrazy wszystkich architektur i platform określonych na liście manifestów zostaną skopiowane.

* Dostęp do rejestru docelowego nie ma konieczności używania publicznego punktu końcowego rejestru.

Aby zaimportować obrazy kontenerów, ten artykuł wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w Azure Cloud Shell lub lokalnie (zalecane jest w wersji 2.0.55 lub nowszej). Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

> [!NOTE]
> Jeśli konieczne jest dystrybuowanie identycznych obrazów kontenerów w wielu regionach platformy Azure, Azure Container Registry obsługuje również [replikację geograficzną](container-registry-geo-replication.md). Dzięki replikacji geograficznej rejestru (wymagana warstwa usługi Premium) można obsłużyć wiele regionów z identycznymi nazwami obrazów i tagów z jednego rejestru.
>

> [!IMPORTANT]
> Wprowadzono zmiany w importowaniu obrazów między dwoma rejestrami kontenerów platformy Azure, które zostały wprowadzone od stycznia 2021:
> * Importowanie do lub z usługi Azure Container Registry z ograniczeniami w sieci wymaga, aby rejestr z ograniczeniami [**zezwalał na dostęp zaufanych usług**](allow-access-trusted-services.md) w celu obejścia sieci. Domyślnie to ustawienie jest włączone, co umożliwia import. Jeśli to ustawienie nie jest włączone w nowo utworzonym rejestrze z prywatnym punktem końcowym lub z regułami zapory rejestru, importowanie zakończy się niepowodzeniem. 
> * W istniejącym w sieci ograniczonym rejestrze kontenerów platformy Azure, który jest używany jako źródło lub cel importu, włączenie tej funkcji zabezpieczeń sieci jest opcjonalne, ale zalecane.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz jeszcze usługi Azure Container Registry, Utwórz rejestr. Aby uzyskać instrukcje, zobacz [Szybki Start: Tworzenie prywatnego rejestru kontenerów za pomocą interfejsu wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md).

Do zaimportowania obrazu do usługi Azure Container Registry tożsamość musi mieć uprawnienia do zapisu w rejestrze docelowym (co najmniej rola współautor lub rola niestandardowa, która umożliwia działanie importImage). Zobacz [Azure Container Registry ról i uprawnień](container-registry-roles.md#custom-roles). 

## <a name="import-from-a-public-registry"></a>Importuj z rejestru publicznego

### <a name="import-from-docker-hub"></a>Importuj z usługi Docker Hub

Na przykład użyj polecenia [AZ ACR import][az-acr-import] w celu zaimportowania obrazu wieloarchitekturowego `hello-world:latest` z usługi Docker Hub do rejestru o nazwie Moje *Rejestr*. Ponieważ `hello-world` jest oficjalnym obrazem z usługi Docker Hub, ten obraz znajduje się w domyślnym `library` repozytorium. Dołącz nazwę repozytorium i opcjonalnie tag w wartości `--source` parametru obrazu. (Opcjonalnie można zidentyfikować obraz według jego skrótu manifestu zamiast znacznika, który gwarantuje określoną wersję obrazu).
 
```azurecli
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest
```

Aby sprawdzić, czy do tego obrazu są skojarzone wiele manifestów, należy uruchomić `az acr repository show-manifests` polecenie:

```azurecli
az acr repository show-manifests \
  --name myregistry \
  --repository hello-world
```

Poniższy przykład importuje obraz publiczny z `tensorflow` repozytorium w usłudze Docker Hub:

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/tensorflow/tensorflow:latest-gpu \
  --image tensorflow:latest-gpu
```

### <a name="import-from-microsoft-container-registry"></a>Importuj z Container Registry firmy Microsoft

Na przykład zaimportuj `ltsc2019` obraz systemu Windows Server Core z `windows` repozytorium w programie Microsoft Container Registry.

```azurecli
az acr import \
--name myregistry \
--source mcr.microsoft.com/windows/servercore:ltsc2019 \
--image servercore:ltsc2019
```

## <a name="import-from-an-azure-container-registry-in-the-same-ad-tenant"></a>Importowanie z usługi Azure Container Registry w ramach tej samej dzierżawy usługi AD

Możesz zaimportować obraz z usługi Azure Container Registry w tej samej dzierżawie usługi AD przy użyciu zintegrowanych uprawnień Azure Active Directory.

* Twoja tożsamość musi mieć uprawnienia Azure Active Directory do odczytu z rejestru źródłowego (roli czytelnika) oraz do importowania do rejestru docelowego (rola współautora lub [rola niestandardowa](container-registry-roles.md#custom-roles) , która umożliwia działanie importImage).

* Rejestr może znajdować się w tej samej lub innej subskrypcji platformy Azure w tej samej dzierżawie Active Directory.

* [Publiczny dostęp](container-registry-access-selected-networks.md#disable-public-network-access) do rejestru źródłowego może być wyłączony. Jeśli dostęp publiczny jest wyłączony, określ rejestr źródłowy według identyfikatora zasobu zamiast nazwy serwera logowania rejestru.

* Jeśli rejestr źródłowy i/lub rejestr docelowy mają prywatny punkt końcowy lub reguły zapory rejestru są stosowane, należy się upewnić, że rejestr z ograniczeniami [zezwala zaufanym usługom](allow-access-trusted-services.md) na dostęp do sieci.

### <a name="import-from-a-registry-in-the-same-subscription"></a>Importuj z rejestru w ramach tej samej subskrypcji

Na przykład zaimportuj `aci-helloworld:latest` obraz ze źródłowego rejestru *mysourceregistry* do *rejestru* w tej samej subskrypcji platformy Azure.

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld:latest \
  --image aci-helloworld:latest
```

Poniższy przykład importuje `aci-helloworld:latest` obraz do *rejestru* ze źródłowego rejestru *mysourceregistry* , w którym dostęp do publicznego punktu końcowego rejestru jest wyłączony. Podaj identyfikator zasobu rejestru źródłowego z `--registry` parametrem. Należy zauważyć, że `--source` parametr określa tylko repozytorium źródłowe i tag, a nie nazwę serwera logowania rejestru.

```azurecli
az acr import \
  --name myregistry \
  --source aci-helloworld:latest \
  --image aci-helloworld:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

Poniższy przykład importuje obraz przez szyfrowanie manifestu (skrót SHA-256, reprezentowane jako `sha256:...` ), a nie przez tag:

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>Importuj z rejestru w innej subskrypcji

W poniższym przykładzie *mysourceregistry* znajduje się w innej subskrypcji z *rejestru* w tej samej dzierżawie Active Directory. Podaj identyfikator zasobu rejestru źródłowego z `--registry` parametrem. Należy zauważyć, że `--source` parametr określa tylko repozytorium źródłowe i tag, a nie nazwę serwera logowania rejestru.

```azurecli
az acr import \
  --name myregistry \
  --source samples/aci-helloworld:latest \
  --image aci-hello-world:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>Importowanie z rejestru przy użyciu poświadczeń jednostki usługi

Aby zaimportować z rejestru, do którego nie można uzyskać dostępu za pomocą uprawnień zintegrowanych Active Directory, można użyć poświadczeń jednostki usługi (jeśli są dostępne) w rejestrze źródłowym. Podaj identyfikator appID i hasło [nazwy głównej usługi](container-registry-auth-service-principal.md) Active Directory, która ma dostęp ACRPull do rejestru źródłowego. Użycie jednostki usługi jest przydatne w przypadku systemów kompilacji i innych systemów nienadzorowanych, które muszą importować obrazy do rejestru.

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

## <a name="import-from-an-azure-container-registry-in-a-different-ad-tenant"></a>Importowanie z usługi Azure Container Registry w innej dzierżawie usługi AD

Aby zaimportować dane z rejestru kontenerów platformy Azure w innej dzierżawie Azure Active Directory, określ rejestr źródłowy według nazwy serwera logowania i podaj poświadczenia nazwy użytkownika i hasła, które umożliwiają uzyskanie dostępu do rejestru. Można na przykład użyć tokenu i hasła w [zakresie repozytorium](container-registry-repository-scoped-permissions.md) lub identyfikatora AppID i hasła jednostki [usługi](container-registry-auth-service-principal.md) Active Directory, która ma dostęp ACRPull do rejestru źródłowego. 

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>Importuj z rejestru kontenerów prywatnych spoza platformy Azure

Zaimportuj obraz z rejestru prywatnego niepochodzącego od platformy Azure, określając poświadczenia umożliwiające dostęp ściągający do rejestru. Na przykład Pobierz obraz z prywatnego rejestru platformy Docker: 

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/sourcerepo/sourceimage:tag \
  --image sourceimage:tag \
  --username <username> \
  --password <password>
```

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono informacje o importowaniu obrazów kontenerów do usługi Azure Container Registry z rejestru publicznego lub innego rejestru prywatnego. Dodatkowe opcje importowania obrazu można znaleźć w temacie [AZ ACR import][az-acr-import] Command Reference. 


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-import]: /cli/azure/acr#az-acr-import
[azure-cli]: /cli/azure/install-azure-cli
