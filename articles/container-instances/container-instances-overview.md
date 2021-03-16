---
title: Kontenery bezserwerowe na platformie Azure
description: Usługa Azure Container Instances to najszybszy i najprostszy sposób uruchamiania wyizolowanych kontenerów na platformie Azure, bez konieczności zarządzania jakimikolwiek maszynami wirtualnymi ani korzystania z orkiestratora wyższego poziomu.
ms.topic: overview
ms.date: 08/10/2020
ms.custom: seodec18, mvc
ms.openlocfilehash: 873f7cc51d1b369503a72501ae000000ff06f805
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103573017"
---
# <a name="what-is-azure-container-instances"></a>Co to jest usługa Azure Container Instances?

Kontenery stają się preferowanym sposobem tworzenia pakietów aplikacji w chmurze, ich wdrażania oraz zarządzania nimi. Usługa Azure Container Instances to najszybszy i najprostszy sposób uruchomienia kontenera na platformie Azure, bez konieczności zarządzania jakimikolwiek maszynami wirtualnymi ani korzystania z usługi wyższego poziomu.

Usługa Azure Container Instances to doskonałe rozwiązanie dla wszystkich scenariuszy, które może działać w kontenerach izolowanych, w tym w przypadku prostych aplikacji, automatyzacji zadań i zadań kompilacji. W przypadku scenariuszy, w których potrzebna jest pełna aranżacja kontenera, w tym odnajdowania usług w wielu kontenerach, Skalowanie automatyczne i skoordynowane uaktualnienia aplikacji, zalecamy korzystanie z [usługi Azure Kubernetes Service (AKS)](../aks/index.yml).

## <a name="fast-startup-times"></a>Krótki czas uruchamiania

Kontenery oferują znaczące korzyści związane z uruchamianiem w porównaniu do maszyn wirtualnych. Usługa Azure Container Instances umożliwia uruchamianie kontenera na platformie Azure w ciągu kilku sekund bez konieczności aprowizowania maszyn wirtualnych i zarządzania nimi.

Przenoszenie obrazów kontenera systemu Linux lub Windows z usługi Docker Hub, prywatnego [rejestru kontenera platformy Azure](../container-registry/index.yml)lub innego rejestru platformy Docker opartego na chmurze. Odwiedź stronę [często zadawanych pytań](container-instances-faq.md) , aby dowiedzieć się, które rejestry są obsługiwane przez ACI. Azure Container Instances buforuje kilka typowych obrazów podstawowych systemów operacyjnych, pomagając przyspieszyć wdrażanie niestandardowych obrazów aplikacji.

## <a name="container-access"></a>Dostęp do kontenera

Azure Container Instances umożliwia Uwidacznianie grup kontenerów bezpośrednio w Internecie przy użyciu adresu IP i w pełni kwalifikowanej nazwy domeny (FQDN). Podczas tworzenia wystąpienia kontenera możesz określić niestandardową etykietę nazwy DNS, dzięki czemu aplikacja będzie dostępna pod adresem *etykieta_niestandardowa*.*region_świadczenia_usługi_Azure*.azurecontainer.io.

Azure Container Instances obsługuje również wykonywanie polecenia w działającym kontenerze, zapewniając interaktywną powłokę ułatwiającą Tworzenie aplikacji i rozwiązywanie problemów. Dostęp odbywa się za pośrednictwem protokołu HTTPS przy użyciu protokołu TLS do zabezpieczania połączeń klientów.

> [!IMPORTANT]
> Od 13 stycznia 2020, Azure Container Instances będą wymagały wszystkich bezpiecznych połączeń z serwerów i aplikacji do korzystania z protokołu TLS 1,2. Obsługa protokołu TLS 1,0 i 1,1 zostanie wycofana.

## <a name="compliant-deployments"></a>Zgodne wdrożenia

### <a name="hypervisor-level-security"></a>Zabezpieczenia na poziomie funkcji hypervisor

W przeszłości kontenery oferowały zarządzanie zasobami i izolację zależności aplikacji, ale nie były wystarczająco odporne na użycie wielu obcych dzierżaw. Usługa Azure Container Instances gwarantuje, że aplikacja jest izolowana w kontenerze w takim samym stopniu, w jakim byłaby na maszynie wirtualnej.

### <a name="customer-data"></a>Dane klienta

Usługa ACI przechowuje minimalne dane klienta wymagane do upewnienia się, że grupy kontenerów działają zgodnie z oczekiwaniami. Przechowywanie danych klienta w pojedynczym regionie jest obecnie dostępne tylko w regionie Azja Południowo-Wschodnia (Singapur Azja i Pacyfik) regionu geograficznego i Brazylia Południowa (stan Świętego Paulo). W przypadku wszystkich innych regionów dane klienta są przechowywane w [lokalizacji geograficznej](https://azure.microsoft.com/global-infrastructure/geographies/). Skontaktuj się z pomocą techniczną platformy Azure, aby dowiedzieć się więcej.

## <a name="custom-sizes"></a>Rozmiary niestandardowe

Kontenery są przeważnie optymalizowane do uruchamiania tylko jednej aplikacji, ale szczegółowe potrzeby poszczególnych aplikacji mogą się bardzo różnić. Usługa Azure Container Instances zapewnia optymalne wykorzystanie, zezwalając na korzystanie z dokładnych specyfikacji rdzeni procesora i pamięci. Opłaty są naliczane zgodnie z zapotrzebowaniem i za sekundę, dlatego można skutecznie dostosować wydatki w oparciu o potrzeby.

W przypadku zadań wykorzystujących znaczną moc obliczeniową, takich jak uczenie maszynowe, usługa Azure Container Instances może zaplanować używanie [zasobów procesorów GPU](container-instances-gpu.md) NVIDIA Tesla (wersja zapoznawcza) przez kontenery systemu Linux.

## <a name="persistent-storage"></a>Magazyn trwały

Aby można było pobrać i zachować stan z Azure Container Instances, oferujemy bezpośrednie [Instalowanie udziałów Azure Files udostępnionych](./container-instances-volume-azure-files.md) przez usługę Azure Storage.

## <a name="linux-and-windows-containers"></a>Kontenery systemów Linux i Windows

Usługa Azure Container Instances umożliwia planowanie kontenerów systemów Windows i Linux przy użyciu tego samego interfejsu API. Podczas tworzenia [grup kontenerów](container-instances-container-groups.md) po prostu określ typ systemu operacyjnego.

Niektóre funkcje są obecnie ograniczone do kontenerów systemu Linux:

* Wiele kontenerów na grupę kontenerów
* Instalowanie woluminu ([Azure Files](container-instances-volume-azure-files.md), [emptyDir](container-instances-volume-emptydir.md), [GitRepo](container-instances-volume-gitrepo.md), [Secret](container-instances-volume-secret.md))
* [Metryki użycia zasobów](container-instances-monitor.md) z Azure monitor
* [Wdrażanie sieci wirtualnej](container-instances-vnet.md)
* [Zasoby procesora GPU](container-instances-gpu.md) (wersja zapoznawcza)

W przypadku wdrożeń kontenerów systemu Windows należy używać obrazów opartych na typowych [obrazach podstawowych systemu Windows](container-instances-faq.md#what-windows-base-os-images-are-supported).

## <a name="co-scheduled-groups"></a>Grupy planowane wspólnie

Usługa Azure Container Instances obsługuje planowanie [grup wielu kontenerów](container-instances-container-groups.md), które współużytkują maszynę hosta, sieć lokalną, magazyn i cykl życia. Dzięki temu można łączyć główny kontener aplikacji z innymi kontenerami pełniącymi rolę pomocniczą, np. przyczepkami rejestrowania.

## <a name="virtual-network-deployment"></a>Wdrażanie sieci wirtualnej

Azure Container Instances umożliwia [wdrażanie wystąpień kontenerów w sieci wirtualnej platformy Azure](container-instances-vnet.md). Wystąpienia kontenerów, które są wdrażane w podsieci w sieci wirtualnej, mogą bezpiecznie komunikować się z innymi zasobami w sieci wirtualnej, w tym z lokalnymi (za pośrednictwem [bramy sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) lub [ExpressRoute](../expressroute/expressroute-introduction.md)).

## <a name="next-steps"></a>Następne kroki

Spróbuj wdrożyć kontener na platformie Azure za pomocą jednego polecenia, korzystając z naszego przewodnika Szybki start:

> [!div class="nextstepaction"]
> [Azure Container Instances — Szybki start](container-instances-quickstart.md)

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
