---
title: Obsługiwane formaty zawartości
description: Dowiedz się więcej na temat formatów zawartości obsługiwanych przez Azure Container Registry, w tym obrazów kontenerów zgodnych z platformą Docker, wykresów Helm, obrazów OCI i artefaktów OCI.
ms.topic: article
ms.date: 03/02/2021
ms.openlocfilehash: 218d98f3f16e8d0ca76a24692afbb2b69606564b
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106223068"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Formaty zawartości obsługiwane w Azure Container Registry

Aby zarządzać jednym z następujących formatów zawartości, należy użyć prywatnego repozytorium w Azure Container Registry. 

## <a name="docker-compatible-container-images"></a>Obrazy kontenerów zgodnych z platformą Docker

Obsługiwane są następujące formaty obrazów kontenerów platformy Docker:

* [Manifest obrazu platformy Docker v2, schemat 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Manifest obrazu platformy Docker v2, schemat 2](https://docs.docker.com/registry/spec/manifest-v2-2/) — zawiera listy manifestów, które umożliwiają rejestrom przechowywanie [obrazów wieloarchitekturowych](push-multi-architecture-images.md) w ramach jednego `image:tag` odwołania

## <a name="oci-images"></a>Obrazy OCI

Azure Container Registry obsługuje obrazy zgodne ze [specyfikacją formatu obrazu Open Container Initiative (OCI)](https://github.com/opencontainers/image-spec/blob/master/spec.md), łącznie z opcjonalną specyfikacją [indeksu obrazu](https://github.com/opencontainers/image-spec/blob/master/image-index.md) . Formaty pakietów obejmują [Format obrazu Singularity (sif)](https://github.com/sylabs/sif).

## <a name="oci-artifacts"></a>Artefakty OCI

Azure Container Registry obsługuje [specyfikację dystrybucji OCI](https://github.com/opencontainers/distribution-spec), neutralną dla dostawcy specyfikację Cloud-niezależny od do przechowywania, udostępniania, zabezpieczania i wdrażania obrazów kontenerów i innych typów zawartości (artefaktów). Specyfikacja umożliwia rejestrowi przechowywanie szerokiego zakresu artefaktów oprócz obrazów kontenerów. Do wypychania i ściągania artefaktów służy narzędzie odpowiednie dla artefaktu. Aby zapoznać się z przykładem, zobacz [wypychanie i ściąganie artefaktu OCI przy użyciu usługi Azure Container Registry](container-registry-oci-artifacts.md).

Aby dowiedzieć się więcej o artefaktach OCI, zobacz repozytorium [OCI rejestru as Storage (ORAS)](https://github.com/deislabs/oras) i repozytorium [OCI artefaktów](https://github.com/opencontainers/artifacts) w witrynie GitHub.

## <a name="helm-charts"></a>Pakiety programu Helm

Azure Container Registry mogą hostować repozytoria dla [wykresów Helm](https://helm.sh/), format pakietu służący do szybkiego zarządzania aplikacjami i wdrażania ich w programie Kubernetes. Zaleca się używanie [klienta Helm](https://docs.helm.sh/using_helm/#installing-helm) w wersji 3. Zobacz [wypychanie i ściąganie wykresów Helm do usługi Azure Container Registry](container-registry-helm-repos.md).

## <a name="next-steps"></a>Następne kroki

* Zobacz, jak [wypychanie i ściąganie](container-registry-get-started-docker-cli.md) obrazów za pomocą Azure Container Registry.

* Za pomocą [zadań ACR](container-registry-tasks-overview.md) można tworzyć i testować obrazy kontenerów. 

* Użyj [Moby BuildKit](https://github.com/moby/buildkit) do kompilowania kontenerów i pakietów w formacie OCI.

* Skonfiguruj [repozytorium Helm](container-registry-helm-repos.md) hostowane w Azure Container Registry. 


