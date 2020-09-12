---
title: Informacje na temat zarządzania obciążeniem Kubernetes na urządzeniu Azure Stack Edge | Microsoft Docs
description: Opisuje, w jaki sposób można zarządzać obciążeniami Kubernetes na urządzeniu brzegowym Azure Stack.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: alkohli
ms.openlocfilehash: 53bd7a404e4635833b03507e8b5ae93ae40b1c61
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89318985"
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

## <a name="deployment-flow"></a>Przepływ wdrożenia

Aby wdrożyć aplikacje na Azure Stack urządzeniu brzegowym, wykonaj następujące kroki: 
 
1. **Konfigurowanie dostępu**: najpierw użyjesz obszaru działania programu PowerShell, aby utworzyć użytkownika, utworzyć przestrzeń nazw i udzielić użytkownikowi dostępu do tej przestrzeni nazw.
2. **Konfigurowanie magazynu**: następnie do tworzenia woluminów trwałych przy użyciu statycznej lub dynamicznej obsługi aplikacji stanowych, które zostaną wdrożone, będzie używany zasób Azure Stack Edge w Azure Portal.
3. **Konfigurowanie sieci**: na koniec należy używać usług do udostępniania aplikacji zewnętrznych i w klastrze Kubernetes.
 
## <a name="deployment-types"></a>Typy wdrożeń

Istnieją trzy podstawowe sposoby wdrażania obciążeń. Każda z tych metod wdrażania umożliwia nawiązanie połączenia z odrębną przestrzenią nazw na urządzeniu, a następnie wdrożenie aplikacji bezstanowych lub stanowych.

![Wdrożenie obciążenia Kubernetes](./media/azure-stack-edge-gpu-kubernetes-workload-management/kubernetes-workload-management-1.png)

- **Wdrożenie lokalne**: to wdrożenie jest dostępne za pomocą narzędzia wiersza polecenia, takiego jak `kubectl` , które pozwala na wdrożenie Kubernetes `yamls` . Dostęp do klastra Kubernetes można uzyskać na Azure Stackej krawędzi za pomocą `kubeconfig` pliku. Aby uzyskać więcej informacji, przejdź do [obszaru dostęp do klastra Kubernetes za pośrednictwem usługi polecenia kubectl](azure-stack-edge-gpu-create-kubernetes-cluster.md).

- **Wdrożenie IoT Edge**: odbywa się to za pomocą IoT Edge, który łączy się z IoT Hub platformy Azure. Nawiąż połączenie z klastrem Kubernetes Azure Stack na urządzeniu brzegowym za pośrednictwem `iotedge` przestrzeni nazw. IoT Edge agenci wdrożoni w tej przestrzeni nazw są odpowiedzialni za łączność z platformą Azure. Konfiguracja zostanie zastosowana `IoT Edge deployment.json` za pomocą usługi Azure DEVOPS Ci/CD. Przestrzeń nazw i zarządzanie IoT Edge są wykonywane za poorednictwem operatora chmury.

- **Wdrożenie platformy Azure/Arc**: usługa Azure Arc jest narzędziem do zarządzania hybrydowego, które umożliwi wdrażanie aplikacji w klastrach Kubernetes. Klaster Kubernetes można połączyć na urządzeniu brzegowym Azure Stack za pośrednictwem `azure-arc namespace` . Agenci są wdrażani w tej przestrzeni nazw, które są odpowiedzialne za łączność z platformą Azure. Konfigurację wdrożenia należy zastosować przy użyciu GitOps zarządzania konfiguracją. Usługa Azure Arc umożliwi także używanie Azure Monitor do kontenerów w celu wyświetlania i monitorowania klastrów. Aby uzyskać więcej informacji, przejdź do [co to jest usługa Azure-Arc włączona Kubernetes?](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview).

## <a name="choose-the-deployment-type"></a>Wybierz typ wdrożenia

Podczas wdrażania aplikacji należy wziąć pod uwagę następujące informacje:

- **Jeden lub wiele typów**: można wybrać jedną opcję wdrożenia lub kombinację różnych opcji wdrażania.
- **Chmura a lokalna**: w zależności od aplikacji można wybrać wdrożenie lokalne za pośrednictwem polecenia kubectl lub wdrożenia w chmurze za pośrednictwem IoT Edge i Azure Arc. 
    - W przypadku wybrania wdrożenia lokalnego nastąpi ograniczenie do sieci, w której wdrożono urządzenie Azure Stack Edge.
    - Jeśli masz agenta w chmurze, który można wdrożyć, należy wdrożyć operator chmury i użyć zarządzania chmurą.
- **Usługa IoT vs Azure Arc**: wybór wdrożenia zależy również od intencji scenariusza produktu. W przypadku wdrażania aplikacji lub kontenerów, które mają większą integrację z usługą IoT lub ekosystem IoT, wybierz IoT Edge w celu wdrożenia aplikacji. Jeśli masz istniejące wdrożenia programu Kubernetes, najlepszym wyborem będzie usługa Azure Arc.


## <a name="next-steps"></a>Następne kroki

Aby lokalnie wdrożyć aplikację za pomocą polecenia kubectl, zobacz:

- [Wdróż bezstanową aplikację na Azure Stackej krawędzi za pośrednictwem polecenia kubectl](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md).

Aby wdrożyć aplikację za pośrednictwem IoT Edge, zobacz:

- [Wdróż przykładowy moduł na Azure Stackej krawędzi za pośrednictwem IoT Edge](azure-stack-edge-gpu-deploy-sample-module.md).

Aby wdrożyć aplikację za pośrednictwem usługi Azure ARC, zobacz:

- [Wdróż aplikację przy użyciu usługi Azure Arc](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md).
