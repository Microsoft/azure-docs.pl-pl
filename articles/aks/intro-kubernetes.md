---
title: Wprowadzenie do usługi Azure Kubernetes Service
description: Poznaj funkcje i korzyści usługi Azure Kubernetes Service ułatwiające wdrażanie aplikacji opartych na kontenerach i zarządzanie nimi na platformie Azure.
services: container-service
ms.topic: overview
ms.date: 02/24/2021
ms.custom: mvc
ms.openlocfilehash: bb4adac1f59370959830f418d27bc27f9aaf63d2
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493020"
---
# <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Usługa Azure Kubernetes Service (AKS) upraszcza wdrażanie zarządzanego klastra Kubernetes na platformie Azure przez przeciążanie obciążenia operacyjnego platformą Azure. Jako hostowana usługa Kubernetes, platforma Azure obsługuje krytyczne zadania, takie jak monitorowanie kondycji i konserwacja. Ponieważ wzorce Kubernetes są zarządzane przez platformę Azure, można zarządzać węzłami agentów tylko i obsługiwać je. W ten sposób AKS jest bezpłatny. Płacisz tylko za węzły agentów w klastrach, a nie dla wzorców.  

Klaster AKS można utworzyć przy użyciu:
* [Interfejs wiersza polecenia platformy Azure](kubernetes-walkthrough.md)
* [Witryna Azure Portal](kubernetes-walkthrough-portal.md)
* [Azure PowerShell](kubernetes-walkthrough-powershell.md)
* Korzystanie z opcji wdrażania opartych na szablonach, takich jak [szablony Azure Resource Manager](kubernetes-walkthrough-rm-template.md) i Terraform 

Podczas wdrażania klastra usługi AKS wzorzec Kubernetes i wszystkie węzły są wdrażane i konfigurowane automatycznie. Zaawansowana sieć, integracja z usługą Azure Active Directory (Azure AD), monitorowanie i inne funkcje, można skonfigurować w ramach procesu wdrażania. 

Aby uzyskać więcej informacji na temat Kubernetes, zobacz [podstawowe pojęcia dotyczące Kubernetes dla AKS][concepts-clusters-workloads].

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]
> AKS obsługuje również kontenery systemu Windows Server.

## <a name="access-security-and-monitoring"></a>Dostęp, zabezpieczenia i monitorowanie

Aby uzyskać lepsze zabezpieczenia i zarządzanie, AKS umożliwia integrację z usługą Azure AD w celu:
* Użyj kontroli dostępu opartej na rolach Kubernetes (Kubernetes RBAC). 
* Monitoruj kondycję klastra i zasobów.

### <a name="identity-and-security-management"></a>Zarządzanie tożsamościami i zabezpieczeniami

#### <a name="kubernetes-rbac"></a>Kontrola dostępu na podstawie ról na platformie Kubernetes

Aby ograniczyć dostęp do zasobów klastra, AKS obsługuje [KUBERNETES RBAC][kubernetes-rbac]. Kubernetes RBAC kontroluje dostęp i uprawnienia do zasobów Kubernetes i przestrzeni nazw.  

#### <a name="azure-ad"></a>Azure AD

Klaster AKS można skonfigurować w celu integracji z usługą Azure AD. Dzięki integracji z usługą Azure AD można skonfigurować dostęp Kubernetes na podstawie istniejącej tożsamości i członkostwa w grupie. Istniejący użytkownicy i grupy usługi Azure AD mogą mieć zintegrowane środowisko logowania i dostęp do zasobów AKS.  

Aby uzyskać więcej informacji na temat tożsamości, zobacz [Opcje dostępu i tożsamości dla AKS][concepts-identity].

Aby zabezpieczyć klastry usługi AKS, zobacz [Integrowanie usługi Azure Active Directory z usługą AKS][aks-aad].

### <a name="integrated-logging-and-monitoring"></a>Zintegrowane rejestrowanie i monitorowanie

Azure Monitor dla kondycji kontenera zbiera metryki wydajności pamięci i procesora z kontenerów, węzłów i kontrolerów w klastrze AKS i wdrożonych aplikacjach. Można przejrzeć zarówno dzienniki kontenerów, jak i [Kubernetes dzienniki główne][aks-master-logs], które są następujące:
* Przechowywane w obszarze roboczym usługi Azure Log Analytics.
* Dostępne za pomocą Azure Portal, interfejsu wiersza polecenia platformy Azure lub punktu końcowego REST.

Aby uzyskać więcej informacji, zobacz [Monitorowanie kondycji kontenera usługi Azure Kubernetes Service][container-health].

## <a name="clusters-and-nodes"></a>Klastry i węzły

Węzły AKS są uruchamiane na maszynach wirtualnych platformy Azure. Za pomocą węzłów AKS można połączyć magazyn z węzłami i zasobnikami, uaktualnić składniki klastra i korzystać z procesorów GPU. AKS obsługuje klastry Kubernetes z wieloma pulami węzłów w celu obsługi mieszanych systemów operacyjnych i kontenerów systemu Windows Server.  

Aby uzyskać więcej informacji na temat możliwości Kubernetes klastra, węzła i puli węzłów, zobacz [podstawowe pojęcia Kubernetes dla AKS][concepts-clusters-workloads].

### <a name="cluster-node-and-pod-scaling"></a>Skalowanie węzłów klastra i zasobników

Jako zapotrzebowanie na zmiany zasobów liczba węzłów klastra lub zasobników, które uruchamiają usługi, są automatycznie skalowane w górę lub w dół. Można dostosować zarówno skalowanie w poziomie, jak i automatyczne skalowanie klastra, aby dostosować je do wymagań i uruchamiać tylko niezbędne zasoby.

Aby uzyskać więcej informacji, zobacz [Skalowanie klastra usługi Azure Kubernetes Service (AKS)][aks-scale].

### <a name="cluster-node-upgrades"></a>Uaktualnienia węzłów klastra

AKS oferuje wiele wersji Kubernetes. Nowe wersje są dostępne w programie AKS, ale można je uaktualnić przy Azure Portal użyciu interfejsu wiersza polecenia platformy Azure. Podczas procesu uaktualniania węzły są dokładnie odizolowywane i opróżniane, aby zminimalizować zakłócenia dla działających aplikacji.  

Aby dowiedzieć się więcej na temat wersji cyklu życia, zobacz [Obsługiwane wersje platformy Kubernetes w usłudze AKS][aks-supported versions]. Aby uzyskać instrukcje dotyczące sposobu uaktualniania, zobacz [Uaktualnianie klastra usługi Azure Kubernetes Service (AKS)][aks-upgrade].

### <a name="gpu-enabled-nodes"></a>Węzły obsługujące procesor GPU

AKS obsługuje tworzenie pul węzłów z obsługą procesora GPU. Platforma Azure udostępnia obecnie jedną lub wiele maszyn wirtualnych z obsługą procesora GPU. Maszyny wirtualne z obsługą procesora GPU są przeznaczone do obciążeń intensywnie korzystających z operacji obliczeniowych, intensywnie wykorzystujących grafikę i wizualizacje.

Aby uzyskać więcej informacji, zobacz [Korzystanie z procesorów GPU w usłudze AKS][aks-gpu].

### <a name="confidential-computing-nodes-public-preview"></a>Węzły do przetwarzania poufnego (publiczna wersja zapoznawcza)

Program AKS obsługuje tworzenie opartych na technologii Intel SGX pul węzłów, które są poufne (DCSv2 maszyn wirtualnych). Węzły przetwarzania poufnego umożliwiają uruchamianie kontenerów w środowisku sprzętowym, zaufanym wykonywaniem (enclaves). Izolacja między kontenerami, w połączeniu z integralnością kodu dzięki zaświadczeniu, może pomóc w strategii bezpieczeństwa kontenerów z kompleksową ochroną. Węzły przetwarzania poufnego obsługują zarówno kontenery poufne (istniejące aplikacje platformy Docker), jak i kontenery obsługujące enklawy.

Aby uzyskać więcej informacji, zobacz [poufne węzły obliczeniowe w AKS][conf-com-node].

### <a name="storage-volume-support"></a>Obsługa woluminów magazynu

Aby zapewnić obsługę obciążeń aplikacji, można zainstalować statyczne lub dynamiczne woluminy magazynu dla danych trwałych. W zależności od liczby elementów, które mają być współużytkowane przez program, można użyć magazynu obsługiwanego przez jedną z nich:
* Dyski platformy Azure z dostępem jednokrotnym lub 
* Azure Files wielu współbieżnych dostępów.

Aby uzyskać więcej informacji, zobacz [Opcje magazynu dla aplikacji w AKS][concepts-storage].

Rozpocznij pracę z dynamicznymi woluminami trwałymi przy użyciu [usługi Azure disks][azure-disk] lub [Azure Files][azure-files].

## <a name="virtual-networks-and-ingress"></a>Sieci wirtualne i ruch przychodzący

Klaster usługi AKS można wdrożyć w istniejącej sieci wirtualnej. W tej konfiguracji każdy pod w klastrze ma przypisany adres IP w sieci wirtualnej i może komunikować się bezpośrednio z:
* Inne zasobniki w klastrze 
* Inne węzły w sieci wirtualnej. 

W przypadku połączeń z innymi usługami w sieci wirtualnej równorzędnej i w sieciach lokalnych za pośrednictwem połączenia sieci VPN typu lokacja-lokacja (S2S) można także łączyć się z innymi usługi w usłudze peer-Network.  

Aby uzyskać więcej informacji, zobacz [pojęcia dotyczące sieci dla aplikacji w AKS][aks-networking].

### <a name="ingress-with-http-application-routing"></a>Ruch przychodzący z routingiem aplikacji protokołu HTTP

Dodatek routingu aplikacji HTTP ułatwia dostęp do aplikacji wdrożonych w klastrze AKS. Po włączeniu konfiguruje ono kontroler ruchu przychodzącego w klastrze usługi AKS.  

Po wdrożeniu aplikacji, publicznie dostępne nazwy DNS są automatycznie konfigurowane. Routing aplikacji protokołu HTTP konfiguruje strefę DNS i integruje ją z klastrem AKS. Następnie możesz wdrożyć zasoby ruchu przychodzącego platformy Kubernetes w zwykły sposób.  

Aby rozpocząć pracę z ruchem danych przychodzących, zobacz [Routing aplikacji protokołu HTTP][aks-http-routing].

## <a name="development-tooling-integration"></a>Integracja narzędzi deweloperskich

Kubernetes ma bogaty ekosystem narzędzi do tworzenia i zarządzania, które bezproblemowo współpracują z AKS. Te narzędzia obejmują Helm i rozszerzenie Kubernetes dla Visual Studio Code.   

Platforma Azure udostępnia kilka narzędzi, które pomagają usprawnić Kubernetes, takie jak Azure Dev Spaces i DevOps Starter.  

### <a name="azure-dev-spaces"></a>Azure Dev Spaces

Usługa Azure Dev Spaces udostępnia środowisko do szybkiego, iteracyjnego programowania na platformie Kubernetes dla zespołów. Wykonując minimum czynności konfiguracyjnych, możesz uruchamiać i debugować kontenery bezpośrednio w usłudze AKS. Aby rozpocząć pracę, zobacz usługę [Azure Dev Spaces][azure-dev-spaces].

### <a name="devops-starter"></a>DevOps Starter

DevOps Starter oferuje proste rozwiązanie do przenoszenia istniejącego kodu i repozytoriów Git na platformę Azure. DevOps Starter automatycznie:
* Tworzy zasoby platformy Azure (na przykład AKS); 
* Konfiguruje potok wydania w Azure DevOps Services, który obejmuje potok kompilacji dla CI; 
* Konfiguruje potok wydania dla dysku CD; lub 
* Generuje zasób Application Insights platformy Azure do monitorowania. 

Aby uzyskać więcej informacji, zobacz [DevOps Starter][azure-devops].

## <a name="docker-image-support-and-private-container-registry"></a>Obsługa obrazów platformy Docker i prywatny rejestr kontenerów

Usługa AKS obsługuje format obrazu platformy Docker. Integracja usługi AKS z usługą Azure Container Registry (ACR) pozwala uzyskać prywatny magazyn obrazów platformy Docker.

Aby utworzyć prywatny magazyn obrazów, zobacz [Azure Container Registry][acr-docs].

## <a name="kubernetes-certification"></a>Certyfikat platformy Kubernetes

AKS został CNCF certyfikowany jako Kubernetes zgodny.

## <a name="regulatory-compliance"></a>Zgodność z przepisami

AKS jest zgodna z SOC, ISO, PCI DSS i HIPAA. Aby uzyskać więcej informacji, zobacz [Omówienie zgodności Microsoft Azure][compliance-doc].

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat wdrażania AKS i zarządzania nim przy użyciu interfejsu wiersza polecenia platformy Azure.

> [!div class="nextstepaction"]
> [Wdrażanie klastra AKS przy użyciu interfejsu wiersza polecenia platformy Azure][aks-cli]

<!-- LINKS - external -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[compliance-doc]: https://azure.microsoft.com/en-us/overview/trusted-cloud/compliance/

<!-- LINKS - internal -->
[acr-docs]: ../container-registry/container-registry-intro.md
[aks-aad]: ./azure-ad-integration-cli.md
[aks-cli]: ./kubernetes-walkthrough.md
[aks-gpu]: ./gpu-cluster.md
[aks-http-routing]: ./http-application-routing.md
[aks-networking]: ./concepts-network.md
[aks-portal]: ./kubernetes-walkthrough-portal.md
[aks-scale]: ./tutorial-kubernetes-scale.md
[aks-upgrade]: ./upgrade-cluster.md
[azure-dev-spaces]: ../dev-spaces/index.yml
[azure-devops]: ../devops-project/overview.md
[azure-disk]: ./azure-disks-dynamic-pv.md
[azure-files]: ./azure-files-dynamic-pv.md
[container-health]: ../azure-monitor/containers/container-insights-overview.md
[aks-master-logs]: ./view-control-plane-logs.md
[aks-supported versions]: supported-kubernetes-versions.md
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[kubernetes-rbac]: concepts-identity.md#kubernetes-role-based-access-control-kubernetes-rbac
[concepts-identity]: concepts-identity.md
[concepts-storage]: concepts-storage.md
[conf-com-node]: ../confidential-computing/confidential-nodes-aks-overview.md
