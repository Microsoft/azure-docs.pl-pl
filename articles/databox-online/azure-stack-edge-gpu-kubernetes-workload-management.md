---
title: Informacje na temat zarządzania obciążeniem Kubernetes na urządzeniu Azure Stack Edge | Microsoft Docs
description: Opisuje, w jaki sposób można zarządzać obciążeniami Kubernetes na urządzeniu brzegowym Azure Stack.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: alkohli
ms.openlocfilehash: 21845b51fdd108221d5e1bce50e953b79084d17d
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89085360"
---
# <a name="kubernetes-workload-management-on-your-azure-stack-edge-device"></a>Kubernetes zarządzanie obciążeniami na urządzeniu brzegowym Azure Stack

Na urządzeniu Azure Stack Edge tworzony jest klaster Kubernetes podczas konfigurowania roli obliczeniowej. Po utworzeniu klastra Kubernetes można wdrożyć aplikacje kontenera w klastrze Kubernetes w zasobnikach. Istnieją różne sposoby wdrażania obciążeń w klastrze Kubernetes. 

W tym artykule opisano różne metody, których można użyć do wdrożenia obciążeń na urządzeniu Azure Stack Edge.

## <a name="workload-types"></a>Typy obciążeń

Dwa popularne typy obciążeń, które można wdrożyć na urządzeniu brzegowym Azure Stack, to bezstanowe aplikacje lub aplikacje stanowe.

- **Bezstanowe aplikacje** nie zachowują swojego stanu ani nie zapisują danych w magazynie trwałym. Wszystkie dane użytkownika i sesji pozostają razem z klientem programu. Niektóre przykłady aplikacji bezstanowych obejmują frontony sieci Web, takie jak Nginx i inne aplikacje sieci Web.

    Wdrożenie Kubernetes można utworzyć w celu wdrożenia aplikacji bezstanowej w klastrze. 

- **Aplikacje stanowe** wymagają zapisania ich stanu. Aplikacje stanowe korzystają z magazynu trwałego, takiego jak woluminy trwałe, w celu zapisywania danych do użytku przez serwer lub przez innych użytkowników. Przykłady aplikacji stanowych obejmują bazy danych, takie jak MongoDB.

    Wdrożenie Kubernetes można utworzyć w celu wdrożenia aplikacji stanowej. 

## <a name="namespaces-types"></a>Typy przestrzeni nazw

Zasoby Kubernetes, takie jak grupy miar i wdrożenia, są logicznie pogrupowane w przestrzeni nazw. Dzięki tym grupom można logicznie podzielić klaster Kubernetes i ograniczyć dostęp do tworzenia, wyświetlania i zarządzania zasobami. Użytkownicy mogą korzystać tylko z zasobami w ramach przypisanych przestrzeni nazw.

Przestrzenie nazw są przeznaczone do użycia w środowiskach, w których wielu użytkowników rozprzestrzenia się między wieloma zespołami lub projektami. W przypadku klastrów z niewielką liczbą użytkowników nie trzeba tworzyć ani myśleć o przestrzeniach nazw. Zacznij korzystać z przestrzeni nazw, gdy potrzebujesz udostępnianych funkcji.

Aby uzyskać więcej informacji, zobacz [Kubernetes przestrzenie nazw](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/).


Urządzenie brzegowe Azure Stack ma następujące przestrzenie nazw:

- **Przestrzeń nazw systemu** — w tym obszarze nazw znajdują się zasoby podstawowe, takie jak usługa DNS i serwer proxy, lub pulpit nawigacyjny Kubernetes. Zwykle nie są wdrażane własne aplikacje w tej przestrzeni nazw. Ta przestrzeń nazw służy do debugowania wszelkich problemów z klastrem Kubernetes. 

    Na urządzeniu istnieje wiele przestrzeni nazw systemu, a nazwy odpowiadające tym systemowym przestrzeniom nazw są zarezerwowane. Poniżej znajduje się lista zarezerwowanych przestrzeni nazw systemu: 
    - polecenia — system
    - metallb — system
    - DBE — przestrzeń nazw
    - default
    - Kubernetes — pulpit nawigacyjny
    - default
    - polecenia — dzierżawa węzła
    - polecenia — publiczny
    - iotedge
    - Azure — łuk

    Upewnij się, że nie używasz nazw zarezerwowanych dla tworzonych przestrzeni nazw użytkownika. 
<!--- **default namespace** - This namespace is where pods and deployments are created by default when none is provided and you have admin access to this namespace. When you interact with the Kubernetes API, such as with `kubectl get pods`, the default namespace is used when none is specified.-->

- **Przestrzeń nazw użytkownika** — te przestrzenie nazw można tworzyć za pośrednictwem **polecenia kubectl** do lokalnego wdrażania aplikacji.
 
- **IoT Edge przestrzeń nazw** — Połącz się z tą `iotedge` przestrzenią nazw, aby wdrażać aplikacje za pośrednictwem IoT Edge.

- **Przestrzeń nazw usługi Azure Arc** — Połącz się z tą `azure-arc` przestrzenią nazw, aby wdrażać aplikacje za pośrednictwem usługi Azure Arc.

 
## <a name="deployment-types"></a>Typy wdrożeń

Istnieją trzy podstawowe sposoby wdrażania obciążeń. Każda z tych metod wdrażania umożliwia nawiązanie połączenia z odrębną przestrzenią nazw na urządzeniu, a następnie wdrożenie aplikacji bezstanowych lub stanowych.

![Wdrożenie obciążenia Kubernetes](./media/azure-stack-edge-gpu-kubernetes-workload-management/kubernetes-workload-management-1.png)

- **Lokalne wdrożenie**: jest to za poorednictwem narzędzia dostępu do wiersza polecenia, takiego jak `kubectl` , które pozwala na wdrożenie K8 `yamls` . Nawiąż połączenie z klastrem K8 na krawędzi Azure Stack utworzonej przy użyciu `kubeconfig` pliku. Aby uzyskać więcej informacji, przejdź do [obszaru dostęp do klastra Kubernetes za pośrednictwem usługi polecenia kubectl](azure-stack-edge-gpu-create-kubernetes-cluster.md).

- **Wdrożenie IoT Edge**: odbywa się to za pomocą IoT Edge, który łączy się z IoT Hub platformy Azure. Nawiąż połączenie z klastrem K8 Azure Stack na urządzeniu brzegowym za pośrednictwem `iotedge` przestrzeni nazw. IoT Edge agenci wdrożoni w tej przestrzeni nazw są odpowiedzialni za łączność z platformą Azure. Konfiguracja zostanie zastosowana `IoT Edge deployment.json` za pomocą usługi Azure DEVOPS Ci/CD. Przestrzeń nazw i zarządzanie IoT Edge są wykonywane za poorednictwem operatora chmury.

- **Wdrożenie platformy Azure/Arc**: usługa Azure Arc jest narzędziem do zarządzania hybrydowego, które umożliwi wdrażanie aplikacji w klastrach K8. Klaster K8 można połączyć na urządzeniu brzegowym Azure Stack za pośrednictwem `azure-arc namespace` .  Agenci są wdrażani w tej przestrzeni nazw, które są odpowiedzialne za łączność z platformą Azure. Konfigurację wdrożenia należy zastosować przy użyciu GitOps zarządzania konfiguracją. Usługa Azure Arc umożliwi także używanie Azure Monitor do kontenerów w celu wyświetlania i monitorowania klastrów. Aby uzyskać więcej informacji, przejdź do [co to jest usługa Azure-Arc włączona Kubernetes?](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview).

## <a name="choose-the-deployment-type"></a>Wybierz typ wdrożenia

Podczas wdrażania aplikacji należy wziąć pod uwagę następujące informacje:

- **Jeden lub wiele typów**: można wybrać jedną opcję wdrożenia lub kombinację różnych opcji wdrażania.
- **Chmura a lokalna**: w zależności od aplikacji można wybrać wdrożenie lokalne za pośrednictwem polecenia kubectl lub wdrożenia w chmurze za pośrednictwem IoT Edge i Azure Arc. 
    - Wdrożenie lokalne jest bardziej odpowiednie dla scenariuszy programistycznych. W przypadku wybrania wdrożenia lokalnego nastąpi ograniczenie do sieci, w której wdrożono urządzenie Azure Stack Edge.
    - Jeśli masz agenta w chmurze, który można wdrożyć, należy wdrożyć operator chmury i użyć zarządzania chmurą.
- **Usługa IoT vs Azure Arc**: wybór wdrożenia zależy również od intencji scenariusza produktu. W przypadku wdrażania aplikacji lub kontenerów, które mają głębią integrację z ekosystemem IoT lub IoT, należy wybrać IoT Edge sposób wdrażania aplikacji. Jeśli masz istniejące wdrożenia programu Kubernetes, najlepszym wyborem będzie usługa Azure Arc.


## <a name="next-steps"></a>Następne kroki

Aby lokalnie wdrożyć aplikację za pomocą polecenia kubectl, zobacz:

- [Wdróż bezstanową aplikację na Azure Stackej krawędzi za pośrednictwem polecenia kubectl](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md).

Aby wdrożyć aplikację za pośrednictwem IoT Edge, zobacz:

- [Wdróż przykładowy moduł na Azure Stackej krawędzi za pośrednictwem IoT Edge](azure-stack-edge-gpu-deploy-sample-module.md).

Aby wdrożyć aplikację za pośrednictwem usługi Azure ARC, zobacz:

- [Wdróż aplikację przy użyciu usługi Azure Arc](azure-stack-edge-gpu-deploy-sample-module.md).
