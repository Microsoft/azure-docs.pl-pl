---
title: Zarządzane rejestry kontenerów
description: Wprowadzenie do usługi Azure Container Registry dostarczającej oparte na chmurze, zarządzane rejestry prywatne platformy Docker.
author: stevelas
ms.topic: overview
ms.date: 02/10/2020
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: b5c81d8b2e6d7eac2dcd9070bf1f448340ea1a18
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96341269"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Wprowadzenie do prywatnych rejestrów kontenerów platformy Docker na platformie Azure

Azure Container Registry to zarządzana, prywatna usługa rejestru platformy Docker oparta na rejestrze platformy Docker typu open source 2,0. Twórz i obsługuj rejestry kontenerów platformy Azure w celu przechowywania prywatnych obrazów kontenerów Docker i powiązanych artefaktów oraz zarządzania nimi.

Korzystaj z rejestrów kontenerów platformy Azure z istniejącymi potokami tworzenia i wdrażania kontenerów, a także korzystaj z Azure Container Registry zadań, aby tworzyć obrazy kontenerów na platformie Azure. Kompiluj na żądanie lub w pełni Automatyzuj kompilacje z wyzwalaczami, takimi jak zatwierdzenia kodu źródłowego i aktualizacje obrazu podstawowego.

Aby uzyskać więcej informacji na temat pojęć związanych z platformą Docker i rejestrem, zobacz [Omówienie platformy Docker](https://docs.docker.com/engine/docker-overview/) i [Informacje o rejestrach, repozytoriach i obrazach](container-registry-concepts.md).

## <a name="use-cases"></a>Przypadki zastosowań

Ściąganie obrazów z rejestru kontenerów platformy Azure do różnych celów wdrażania:

* **Skalowalne systemy organizowania** zarządzające konteneryzowanymi aplikacjami w klastrach hostów, włączając w to rozwiązania [DC/OS](https://kubernetes.io/docs/), [Docker Swarm](https://docs.mesosphere.com/) i [Kubernetes](https://docs.docker.com/get-started/swarm-deploy/).
* **Usługi platformy Azure** obsługujące kompilowanie i uruchamianie aplikacji w odpowiedniej skali, w tym usługi [Azure Kubernetes Service (AKS)](../aks/index.yml), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](../service-fabric/index.yml) i inne.

Deweloperzy mogą również przeprowadzać wypychanie do rejestru kontenerów w ramach przepływu pracy opracowywania kontenera. Na przykład należy wskazać rejestr kontenerów z poziomu narzędzia do ciągłej integracji i dostarczania, takiego jak [Azure Pipelines](/azure/devops/pipelines/ecosystems/containers/acr-template) lub [Jenkins](https://jenkins.io/).

Skonfiguruj zadania ACR do automatycznego odbudowywania obrazów aplikacji, gdy ich obrazy podstawowe są aktualizowane lub Automatyzuj kompilacje obrazu, gdy zespół zatwierdzi kod w repozytorium git. Twórz wieloetapowe zadania w celu zautomatyzowania kompilowania, testowania i poprawiania wielu obrazów kontenerów równolegle w chmurze.

Platforma Azure udostępnia narzędzia, w tym interfejsy Command-Line platformy Azure, Azure Portal i obsługa interfejsów API do zarządzania rejestrami kontenerów platformy Azure. Opcjonalnie zainstaluj [rozszerzenie Docker dla Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) i rozszerzenie [konta platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) , aby współpracowało z rejestrami kontenerów platformy Azure. Ściągaj i wypychanie obrazów do usługi Azure Container Registry lub uruchamiaj zadania ACR, w Visual Studio Code.

## <a name="key-features"></a>Najważniejsze funkcje

* **Warstwy usług rejestru** — Utwórz co najmniej jeden rejestr kontenerów w ramach subskrypcji platformy Azure. Rejestry są dostępne w trzech warstwach: [podstawowa, standardowa i Premium](container-registry-skus.md), z których każdy obsługuje integrację elementów webhook, uwierzytelnianie rejestru przy użyciu Azure Active Directory i usuwanie funkcji. Utwórz rejestr w tej samej lokalizacji platformy Azure, w której znajdują się wdrożenia, aby korzystać z lokalnego, bliskiego sieciowo magazynu obrazów kontenerów. Użyj funkcji [replikacji geograficznej](container-registry-geo-replication.md) dostępnej w rejestrach w warstwie Premium dla scenariuszy zaawansowanej replikacji i dystrybucji obrazu kontenera. 

* **Zabezpieczenia i dostęp** — Zaloguj się do rejestru przy użyciu interfejsu wiersza polecenia platformy Azure lub standardowej opcji `docker login` . Azure Container Registry transferuje obrazy kontenerów za pośrednictwem protokołu HTTPS i obsługuje protokół TLS w celu zabezpieczania połączeń klientów. 

  > [!IMPORTANT]
  > Od 13 stycznia 2020, Azure Container Registry będą wymagały wszystkich bezpiecznych połączeń z serwerów i aplikacji do korzystania z protokołu TLS 1,2. Włącz protokół TLS 1,2 przy użyciu dowolnego ostatniego klienta platformy Docker (wersja 18.03.0 lub nowsza). Obsługa protokołu TLS 1,0 i 1,1 zostanie wycofana. 

  Możesz [kontrolować dostęp](container-registry-authentication.md) do rejestru kontenerów za pomocą tożsamości platformy Azure, [jednostki usługi](../active-directory/develop/app-objects-and-service-principals.md) wspieranej przez usługę Azure Active Directory lub podanego konta administratora. Za pomocą kontroli dostępu opartej na rolach (Azure RBAC) można przypisywać użytkownikom lub systemom szczegółowe uprawnienia do rejestru.

  Funkcje zabezpieczeń warstwy usługi Premium obejmują [zaufanie zawartości](container-registry-content-trust.md) dla podpisywania znacznika obrazu oraz [zapory i sieci wirtualne (wersja zapoznawcza)](container-registry-vnet.md) w celu ograniczenia dostępu do rejestru. Azure Security Center opcjonalnie integrują się z Azure Container Registry, aby [skanować obrazy](../security-center/defender-for-container-registries-introduction.md?bc=%2fazure%2fcontainer-registry%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fcontainer-registry%2ftoc.json) przy każdym wypchnięciu obrazu do rejestru.

* **Obsługiwane obrazy i artefakty** — pogrupowane w repozytorium, każdy obraz jest migawką tylko do odczytu kontenera zgodnego z platformą Docker. Rejestry kontenerów platformy Azure mogą obejmować zarówno obrazy systemu Windows, jak i Linux. Możesz kontrolować nazwy obrazów wszystkich wdrożeń kontenera. Użyj standardowych [poleceń platformy Docker](https://docs.docker.com/engine/reference/commandline/), aby wypchnąć obrazy do repozytorium lub aby ściągnąć je z repozytorium. Oprócz obrazów kontenerów platformy Docker Azure Container Registry przechowuje [powiązane formaty zawartości](container-registry-image-formats.md) , takie jak [wykresy Helm](container-registry-helm-repos.md) i obrazy skompilowane zgodnie ze [specyfikacją formatu obrazu typu Open Container Initiative (OCI)](https://github.com/opencontainers/image-spec/blob/master/spec.md).

* **Zautomatyzowane kompilacje obrazów** — służą [Azure Container Registry zadania](container-registry-tasks-overview.md) (zadania ACR), aby usprawnić kompilowanie, testowanie, wypychanie i wdrażanie obrazów na platformie Azure. Na przykład można użyć zadań ACR, aby zwiększyć wewnętrzną pętlę tworzenia do chmury, przenosząc `docker build` operacje na platformę Azure. Konfiguruj zadania kompilacji w celu automatyzowania potoku poprawiania platformy i systemu operacyjnego kontenera, gdy zespół zatwierdza kod w ramach kontroli źródła.

  [Wieloetapowe zadania](container-registry-tasks-overview.md#multi-step-tasks) zapewniają definicje zadań oparte na krokach i wykonywanie w celu tworzenia, testowania i poprawiania obrazów kontenerów w chmurze. Kroki zadań definiują pojedyncze operacje tworzenia i wypychania obrazu kontenera. Mogą one również definiować wykonanie jednego lub kilku kontenerów, z każdym krokiem używającym kontenera jako jego środowiska wykonawczego.

## <a name="next-steps"></a>Następne kroki

* [Create a container registry using the Azure portal](container-registry-get-started-portal.md) (Tworzenie rejestru kontenerów za pomocą witryny Azure Portal)
* [Tworzenie rejestru kontenerów za pomocą interfejsu wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md)
* [Automatyzowanie kompilacji i konserwacji kontenerów za pomocą zadań ACR](container-registry-tasks-overview.md)