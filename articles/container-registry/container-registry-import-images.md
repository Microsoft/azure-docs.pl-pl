---
title: Importowanie obrazów kontenerów
description: Importowanie obrazów kontenerów do usługi Azure Container Registry przy użyciu interfejsów API platformy Azure bez konieczności uruchamiania poleceń platformy Docker.
ms.topic: article
ms.date: 01/15/2021
ms.openlocfilehash: e7becadab7f23acd7b85d6d82fd8abbfa7608add
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781527"
---
# <a name="import-container-images-to-a-container-registry"></a>Importowanie obrazów kontenerów do rejestru kontenerów

Obrazy kontenerów można łatwo importować (kopiować) do rejestru kontenerów platformy Azure bez używania poleceń platformy Docker. Można na przykład zaimportować obrazy z rejestru dewelopera do rejestru produkcyjnego lub skopiować obrazy podstawowe z rejestru publicznego.

Azure Container Registry obsługuje wiele typowych scenariuszy kopiowania obrazów i innych artefaktów z istniejącego rejestru:

* Importowanie obrazów z rejestru publicznego

* Importowanie obrazów lub artefaktów OCI, w tym wykresów programu Helm 3 z innego rejestru kontenerów platformy Azure, w tej samej lub innej subskrypcji lub dzierżawie platformy Azure

* Importowanie z prywatnego rejestru kontenerów spoza platformy Azure

Importowanie obrazów do usługi Azure Container Registry ma następujące korzyści w witrynie z użyciem poleceń interfejsu wiersza polecenia platformy Docker:

* Ponieważ środowisko klienta nie wymaga lokalnej instalacji platformy Docker, zaimportuj dowolny obraz kontenera, niezależnie od obsługiwanego typu systemu operacyjnego.

* Podczas importowania obrazów o wielu architekturach (takich jak oficjalne obrazy platformy Docker) kopiowane są obrazy dla wszystkich architektur i platform określonych na liście manifestów.

* Dostęp do rejestru docelowego nie musi używać publicznego punktu końcowego rejestru.

Aby zaimportować obrazy kontenerów, ten artykuł wymaga uruchomienia interfejsu wiersza polecenia platformy Azure Azure Cloud Shell lokalnie (zalecana jest wersja 2.0.55 lub nowsza). Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

> [!NOTE]
> Jeśli musisz dystrybuować identyczne obrazy kontenerów w wielu regionach platformy Azure, Azure Container Registry obsługuje również [replikację geograficzną.](container-registry-geo-replication.md) Dzięki replikacji geograficznej rejestru (wymagana warstwa usługi Premium) można obsługiwać wiele regionów z identycznymi nazwami obrazów i tagów z jednego rejestru.
>

> [!IMPORTANT]
> Zmiany importowania obrazów między dwoma rejestrami kontenerów platformy Azure zostały wprowadzone od stycznia 2021 r.:
> * Importowanie do lub z rejestru kontenerów platformy Azure [](allow-access-trusted-services.md) z ograniczeniami sieci wymaga rejestru z ograniczeniami, aby zezwolić na dostęp przez zaufane usługi w celu obejścia sieci. Domyślnie to ustawienie jest włączone, co umożliwia importowanie. Jeśli to ustawienie nie jest włączone w nowo utworzonym rejestrze z prywatnym punktem końcowym lub z regułami zapory rejestru, importowanie nie powiedzie się. 
> * W istniejącym rejestrze kontenerów platformy Azure z ograniczeniami sieci, który jest używany jako źródło importu lub obiekt docelowy, włączenie tej funkcji zabezpieczeń sieci jest opcjonalne, ale zalecane.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz jeszcze rejestru kontenerów platformy Azure, utwórz rejestr. Aby uzyskać instrukcje, zobacz [Szybki start: tworzenie prywatnego rejestru kontenerów przy użyciu interfejsu wiersza polecenia platformy Azure.](container-registry-get-started-azure-cli.md)

Aby zaimportować obraz do rejestru kontenerów platformy Azure, tożsamość musi mieć uprawnienia do zapisu w rejestrze docelowym (co najmniej rola Współautor lub rola niestandardowa, która zezwala na akcję importImage). Zobacz [Azure Container Registry i uprawnienia.](container-registry-roles.md#custom-roles) 

## <a name="import-from-a-public-registry"></a>Importowanie z rejestru publicznego

### <a name="import-from-docker-hub"></a>Importowanie z Docker Hub

Na przykład użyj polecenia [az acr import,][az-acr-import] aby zaimportować obraz o wielu architekturach z Docker Hub do rejestru `hello-world:latest` o nazwie *myregistry*. Ponieważ `hello-world` jest to oficjalny obraz z Docker Hub, ten obraz znajduje się w `library` repozytorium domyślnym. Dołącz nazwę repozytorium i opcjonalnie tag w wartości `--source` parametru obrazu. (Opcjonalnie można zidentyfikować obraz według jego skrótu manifestu zamiast według tagu, co gwarantuje określoną wersję obrazu).
 
```azurecli
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest
```

Możesz sprawdzić, czy z tym obrazem jest skojarzonych wiele manifestów, uruchamiając `az acr repository show-manifests` polecenie:

```azurecli
az acr repository show-manifests \
  --name myregistry \
  --repository hello-world
```

Jeśli masz konto [Docker Hub](https://www.docker.com/pricing), zalecamy użycie poświadczeń podczas importowania obrazu z Docker Hub. Przekaż nazwę Docker Hub użytkownika i hasło lub [osobisty token dostępu jako](https://docs.docker.com/docker-hub/access-tokens/) parametry do pliku `az acr import` . Poniższy przykład importuje obraz publiczny z repozytorium w `tensorflow` programie Docker Hub przy użyciu Docker Hub poświadczeń:

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/tensorflow/tensorflow:latest-gpu \
  --image tensorflow:latest-gpu
  --username <Docker Hub user name>
  --password <Docker Hub token>
```

### <a name="import-from-microsoft-container-registry"></a>Importowanie z Microsoft Container Registry

Na przykład zaimportuj `ltsc2019` obraz Systemu Windows Server Core z `windows` repozytorium w Microsoft Container Registry.

```azurecli
az acr import \
--name myregistry \
--source mcr.microsoft.com/windows/servercore:ltsc2019 \
--image servercore:ltsc2019
```

## <a name="import-from-an-azure-container-registry-in-the-same-ad-tenant"></a>Importowanie z rejestru kontenerów platformy Azure w tej samej dzierżawie usługi AD

Obraz można zaimportować z rejestru kontenerów platformy Azure w tej samej dzierżawie usługi AD przy użyciu zintegrowanych Azure Active Directory usługi.

* Tożsamość musi mieć uprawnienia Azure Active Directory odczytu z rejestru źródłowego (roli Czytelnik) i importowania do rejestru [](container-registry-roles.md#custom-roles) docelowego (rola współautora lub roli niestandardowej, która umożliwia akcję importImage).

* Rejestr może być w tej samej lub innej subskrypcji platformy Azure w tej samej dzierżawie usługi Active Directory.

* [Dostęp publiczny](container-registry-access-selected-networks.md#disable-public-network-access) do rejestru źródłowego może być wyłączony. Jeśli dostęp publiczny jest wyłączony, określ rejestr źródłowy według identyfikatora zasobu, a nie nazwy serwera logowania rejestru.

* Jeśli w rejestrze źródłowym i/lub rejestrze docelowym zastosowano reguły zapory prywatnego punktu końcowego [](allow-access-trusted-services.md) lub rejestru, upewnij się, że rejestr z ograniczeniami umożliwia zaufanym usługom dostęp do sieci.

### <a name="import-from-a-registry-in-the-same-subscription"></a>Importowanie z rejestru w tej samej subskrypcji

Na przykład zaimportuj obraz z rejestru `aci-helloworld:latest` źródłowego *mysourceregistry* do *myregistry* w tej samej subskrypcji platformy Azure.

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld:latest \
  --image aci-helloworld:latest
```

Poniższy przykład importuje obraz do `aci-helloworld:latest` *myregistry* z rejestru źródłowego *mysourceregistry,* w którym dostęp do publicznego punktu końcowego rejestru jest wyłączony. W identyfikatorze zasobu rejestru źródłowego należy podać `--registry` parametr . Zwróć uwagę, że parametr określa tylko repozytorium źródłowe i tag, a `--source` nie nazwę serwera logowania rejestru.

```azurecli
az acr import \
  --name myregistry \
  --source aci-helloworld:latest \
  --image aci-helloworld:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

Poniższy przykład importuje obraz według skrótu manifestu (skrót SHA-256 reprezentowany jako `sha256:...` ) zamiast według tagu:

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>Importowanie z rejestru w innej subskrypcji

W poniższym przykładzie *mysourceregistry* znajduje się w innej subskrypcji niż *myregistry* w tej samej dzierżawie usługi Active Directory. W identyfikatorze zasobu rejestru źródłowego należy podać `--registry` parametr . Zwróć uwagę, że parametr określa tylko repozytorium źródłowe i tag, a `--source` nie nazwę serwera logowania rejestru.

```azurecli
az acr import \
  --name myregistry \
  --source samples/aci-helloworld:latest \
  --image aci-hello-world:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>Importowanie z rejestru przy użyciu poświadczeń jednostki usługi

Aby zaimportować dane z rejestru, do których nie można uzyskać dostępu przy użyciu zintegrowanych uprawnień usługi Active Directory, możesz użyć poświadczeń jednostki usługi (jeśli są dostępne) do rejestru źródłowego. Należy podać wartość appID i hasło jednostki usługi Active [Directory,](container-registry-auth-service-principal.md) która ma dostęp do rejestru źródłowego za pomocą usługi ACRPull. Użycie jednostki usługi jest przydatne w przypadku systemów kompilacji i innych systemów nienadzorowanych, które muszą importować obrazy do rejestru.

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

## <a name="import-from-an-azure-container-registry-in-a-different-ad-tenant"></a>Importowanie z rejestru kontenerów platformy Azure w innej dzierżawie usługi AD

Aby zaimportować dane z rejestru kontenerów platformy Azure w innej dzierżawie usługi Azure Active Directory, określ rejestr źródłowy według nazwy serwera logowania i podaj poświadczenia nazwy użytkownika i hasła, które umożliwiają dostęp do ściągania do rejestru. Na przykład użyj [](container-registry-repository-scoped-permissions.md) tokenu i hasła w zakresie repozytorium lub wartości appID i hasła jednostki usługi Active [Directory,](container-registry-auth-service-principal.md) która ma dostęp do rejestru źródłowego za pomocą usługi ACRPull. 

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>Importowanie z prywatnego rejestru kontenerów spoza platformy Azure

Zaimportuj obraz z rejestru prywatnego spoza platformy Azure, określając poświadczenia umożliwiające dostęp do ściągania do rejestru. Na przykład ściągnij obraz z prywatnego rejestru platformy Docker: 

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/sourcerepo/sourceimage:tag \
  --image sourceimage:tag \
  --username <username> \
  --password <password>
```

## <a name="next-steps"></a>Następne kroki

W tym artykule o tym, jak importować obrazy kontenerów do rejestru kontenerów platformy Azure z rejestru publicznego lub innego rejestru prywatnego. Aby uzyskać dodatkowe opcje importowania obrazów, zobacz informacje o [poleceniu az acr import.][az-acr-import] 


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az_login
[az-acr-import]: /cli/azure/acr#az_acr_import
[azure-cli]: /cli/azure/install-azure-cli
