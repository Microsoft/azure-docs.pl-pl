---
title: Informacje na temat zarządzania obciążeniem Kubernetes na urządzeniu Azure Stack Edge | Microsoft Docs
description: Opisuje, w jaki sposób można zarządzać obciążeniami Kubernetes na urządzeniach z systemem Azure Stack EDGE Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: aac4278c6ce03c43418e99978cd039e24dc01194
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101719274"
---
# <a name="kubernetes-workload-management-on-your-azure-stack-edge-pro-device"></a>Kubernetes zarządzanie obciążeniami na urządzeniu Azure Stack EDGE Pro

Na urządzeniu Azure Stack EDGE Pro tworzony jest klaster Kubernetes podczas konfigurowania roli obliczeniowej. Po utworzeniu klastra Kubernetes można wdrożyć aplikacje kontenera w klastrze Kubernetes w zasobnikach. Istnieją różne sposoby wdrażania obciążeń w klastrze Kubernetes. 

W tym artykule opisano różne metody, których można użyć do wdrożenia obciążeń na urządzeniu z systemem Azure Stack brzeg Pro.

## <a name="workload-types"></a>Typy obciążeń

Dwa popularne typy obciążeń, które można wdrożyć na urządzeniu z systemem Azure Stack brzeg Pro, to bezstanowe aplikacje lub aplikacje stanowe.

- **Bezstanowe aplikacje** nie zachowują swojego stanu ani nie zapisują danych w magazynie trwałym. Wszystkie dane użytkownika i sesji pozostają razem z klientem programu. Niektóre przykłady aplikacji bezstanowych obejmują frontony sieci Web, takie jak Nginx i inne aplikacje sieci Web.

    Wdrożenie Kubernetes można utworzyć w celu wdrożenia aplikacji bezstanowej w klastrze. 

- **Aplikacje stanowe** wymagają zapisania ich stanu. Aplikacje stanowe korzystają z magazynu trwałego, takiego jak woluminy trwałe, w celu zapisywania danych do użytku przez serwer lub przez innych użytkowników. Przykłady aplikacji stanowych obejmują bazy danych, takie jak [Azure SQL Edge](../azure-sql-edge/overview.md) i MongoDB.

    Wdrożenie Kubernetes można utworzyć w celu wdrożenia aplikacji stanowej. 

## <a name="deployment-flow"></a>Przepływ wdrożenia

Aby wdrażać aplikacje na urządzeniu z systemem Azure Stack Edge, wykonaj następujące kroki: 
 
1. **Konfigurowanie dostępu**: najpierw użyjesz obszaru działania programu PowerShell, aby utworzyć użytkownika, utworzyć przestrzeń nazw i udzielić użytkownikowi dostępu do tej przestrzeni nazw.
2. **Konfigurowanie magazynu**: następnie do tworzenia woluminów trwałych przy użyciu statycznej lub dynamicznej obsługi aplikacji stanowych, które zostaną wdrożone, będzie używany zasób Azure Stack Edge w Azure Portal.
3. **Konfigurowanie sieci**: na koniec należy używać usług do udostępniania aplikacji zewnętrznych i w klastrze Kubernetes.
 
## <a name="deployment-types"></a>Typy wdrożeń

Istnieją trzy podstawowe sposoby wdrażania obciążeń. Każda z tych metod wdrażania umożliwia nawiązanie połączenia z odrębną przestrzenią nazw na urządzeniu, a następnie wdrożenie aplikacji bezstanowych lub stanowych.

![Wdrożenie obciążenia Kubernetes](./media/azure-stack-edge-gpu-kubernetes-workload-management/kubernetes-workload-management-1.png)

- **Wdrożenie lokalne**: to wdrożenie jest dostępne za pomocą narzędzia wiersza polecenia, takiego jak `kubectl` , które pozwala na wdrożenie Kubernetes `yamls` . Użytkownik uzyskuje dostęp do klastra Kubernetes na Azure Stack Edge w systemie za pośrednictwem `kubeconfig` pliku. Aby uzyskać więcej informacji, przejdź do [obszaru dostęp do klastra Kubernetes za pośrednictwem usługi polecenia kubectl](azure-stack-edge-gpu-create-kubernetes-cluster.md).

- **Wdrożenie IoT Edge**: odbywa się to za pomocą IoT Edge, który łączy się z IoT Hub platformy Azure. Nawiąż połączenie z klastrem Kubernetes na urządzeniu z systemem Azure Stack EDGE Pro za pośrednictwem `iotedge` przestrzeni nazw. Agenci usługi IoT Edge wdrożeni w tej przestrzeni nazw są odpowiedzialni za łączność z platformą Azure. Konfiguracja zostanie zastosowana `IoT Edge deployment.json` za pomocą usługi Azure DEVOPS Ci/CD. Przestrzeń nazw i zarządzanie IoT Edge są wykonywane za poorednictwem operatora chmury.

- **Usługa Azure ARC z włączonym Kubernetes wdrożeniem**: usługa Azure Arc Kubernetes jest narzędziem do zarządzania hybrydowego, które umożliwi wdrażanie aplikacji w klastrach Kubernetes. Nawiąż połączenie z klastrem Kubernetes na urządzeniu z systemem Azure Stack EDGE Pro za pośrednictwem `azure-arc namespace` . Agenci wdrożoni w tej przestrzeni nazw są odpowiedzialni za łączność z platformą Azure. Konfigurację wdrożenia należy zastosować przy użyciu GitOps zarządzania konfiguracją. 
    
    Usługa Azure ARC z włączonym Kubernetes umożliwia również korzystanie z Azure Monitor kontenerów do wyświetlania i monitorowania klastra. Aby uzyskać więcej informacji, przejdź do [co to jest usługa Azure Arc włączona Kubernetes?](../azure-arc/kubernetes/overview.md).
    
    Począwszy od marca 2021, usługa Azure ARC z włączonym Kubernetes będzie ogólnie dostępna dla użytkowników i standardowych opłat za użycie. Jako cenny klient wersji zapoznawczej usługa Azure Arc Kubernetes będzie dostępna bezpłatnie dla Azure Stack urządzeń brzegowych. Aby skorzystać z oferty wersji zapoznawczej, Utwórz [support Request](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest):

    1. W kategorii **Typ problemu** wybierz pozycję **Rozliczenia**.
    2. W obszarze **Subskrypcja** wybierz swoją subskrypcję.
    3. W obszarze **Usługa** wybierz pozycję **Moje usługi**, a następnie wybierz pozycję **Azure Stack Edge**.
    4. W obszarze **zasób** wybierz zasób.
    5. W obszarze **Podsumowanie** wpisz opis problemu.
    6. W obszarze **typ problemu** wybierz pozycję **nieoczekiwane opłaty**.
    7. W obszarze **podtyp problemu** wybierz pozycję **Pomóż mi zrozumieć opłaty za bezpłatną wersję próbną**.


## <a name="choose-the-deployment-type"></a>Wybierz typ wdrożenia

Podczas wdrażania aplikacji należy wziąć pod uwagę następujące informacje:

- **Jeden lub wiele typów**: można wybrać jedną opcję wdrożenia lub kombinację różnych opcji wdrażania.
- **Chmura a lokalna**: w zależności od aplikacji można wybrać wdrożenie lokalne za pośrednictwem polecenia kubectl lub wdrożenia w chmurze za pośrednictwem IoT Edge i Azure Arc. 
    - W przypadku wybrania wdrożenia lokalnego nastąpi ograniczenie do sieci, w której wdrożono urządzenie Azure Stack EDGE Pro.
    - Jeśli masz agenta w chmurze, który można wdrożyć, należy wdrożyć operator chmury i użyć zarządzania chmurą.
- **Usługa IoT vs Azure Arc**: wybór wdrożenia zależy również od intencji scenariusza produktu. W przypadku wdrażania aplikacji lub kontenerów, które mają większą integrację z usługą IoT lub ekosystem IoT, wybierz IoT Edge w celu wdrożenia aplikacji. Jeśli masz istniejące wdrożenia programu Kubernetes, najlepszym wyborem będzie usługa Azure Arc.


## <a name="next-steps"></a>Następne kroki

Aby lokalnie wdrożyć aplikację za pomocą polecenia kubectl, zobacz:

- [Wdróż bezstanową aplikację na Azure Stack EDGE Pro za pośrednictwem polecenia kubectl](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md).

Aby wdrożyć aplikację za pośrednictwem IoT Edge, zobacz:

- [Wdróż przykładowy moduł na Azure Stack EDGE Pro za pośrednictwem IoT Edge](azure-stack-edge-gpu-deploy-sample-module.md).

Aby wdrożyć aplikację za pośrednictwem usługi Azure ARC, zobacz:

- [Wdróż aplikację przy użyciu usługi Azure Arc](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md).
