---
title: Informacje na temat sieci Kubernetes na urządzeniu z systemem Azure Stack Edge | Microsoft Docs
description: Opisuje sposób działania sieci Kubernetes na urządzeniu z systemem Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: alkohli
ms.openlocfilehash: 001304ad6eda27db2285aaa9ad8b28929e2a04f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90899325"
---
# <a name="kubernetes-networking-in-your-azure-stack-edge-pro-gpu-device"></a>Kubernetes sieci na urządzeniu GPU z Azure Stack Edge

Na urządzeniu Azure Stack EDGE Pro tworzony jest klaster Kubernetes podczas konfigurowania roli obliczeniowej. Po utworzeniu klastra Kubernetes można wdrożyć aplikacje kontenera w klastrze Kubernetes w zasobnikach. Istnieją różne sposoby używania sieci dla zasobników w klastrze Kubernetes. 

W tym artykule opisano sieci w klastrze Kubernetes ogólnie i w kontekście Twojego urządzenia Azure Stack EDGE Pro. 

## <a name="networking-requirements"></a>Wymagania dotyczące sieci

Oto przykład typowej aplikacji dwuwarstwowej wdrożonej w klastrze Kubernetes.

- Aplikacja ma fronton serwera sieci Web i aplikację bazy danych w zapleczu. 
- Każdemu użytkownikowi przypisano adres IP, ale te IP mogą ulec zmianie po ponownym uruchomieniu i przełączeniu w tryb failover pod. 
- Każda aplikacja składa się z wielu zasobników i należy zrównoważyć obciążenie ruchu między wszystkimi replikami. 

![Wymagania dotyczące sieci Kubernetes](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-1.png)

W powyższym scenariuszu przedstawiono następujące wymagania sieciowe:

 - Istnieje potrzeba uzyskania dostępu do zewnętrznej aplikacji, która nie jest dostępna dla użytkownika aplikacji poza klastrem Kubernetes za pomocą nazwy lub adresu IP. 
 - Aplikacje znajdujące się w klastrze Kubernetes, na przykład frontonu i zaplecza wewnętrznej bazy danych, powinny być w stanie komunikować się ze sobą.

Aby rozwiązać powyższe wymagania, wprowadzimy usługę Kubernetes. 


## <a name="networking-services"></a>Usługi sieciowe

Istnieją dwa typy usług Kubernetes Services: 

- **Usługa IP klastra** — należy zastanowić się, że zapewnia to stały punkt końcowy dla aplikacji. Nie można uzyskać dostępu do żadnego z tych usług, które są skojarzone z tymi usługami spoza klastra Kubernetes. Adres IP używany z tymi usługami pochodzi z przestrzeni adresowej w sieci prywatnej. 
- **Adres IP usługi równoważenia obciążenia** , który jest używany przez adres IP klastra, ale skojarzony adres IP pochodzi z sieci zewnętrznej i można uzyskać do niego dostęp spoza klastra Kubernetes.


<!--## Networking example for an app


![Kubernetes networking example](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-2.png)

Each of these applications pods has a label associated with it. For example, the web server application pods have a label `app = WS` and the service has a label selector which the same as `app = WS`. Whenever a service of type load balancer or cluster IP is created, there is a control loop that runs in the master and publishes an endpoint corresponding to this service. This service uses a combination of labels and label selectors to discover the pods associated with this service. As a pod gets created, the new endpoint for the pod is added to the endpoint mapping. Whenever a pod is deleted, it gets deleted from the endpoint mapping. Using this endpoint controller, the service has a most up-to-date view of the pods that make up this application.

For discovery of applications within the cluster, Kubernetes cluster has an inbuilt DNS server pod. This is a cluster DNS that resolves service names to cluster IP. Anytime a cluster IP service is created, a DNS record is added to the DNS server that maps the name of the service to the cluster internal IP. That is how the applications within the cluster can discover each other. For load balancing, there is also the `kube-proxy`. This runs on every node and captures the traffic that comes in through the cluster IP and then distributes the traffic across the pods. 

When an application or the end user would first use the IP address associated with the service of type load balancer to discover the service. Then it would use the label select `app = WS` to discover the pods associated with the application. The `kube-proxy` component would then distribute the traffic and ensure that it hits one of the web server application pods. If the web server app wanted to talk to the database app, then it would simply use the name of the service and using the name and the DNS server pod, resolve the name to an IP address. Again using labels and label selector, it would discover the pods associated with the database application. The `kube-proxy` would then distribute the traffic across each of the database app nodes.-->


## <a name="kubernetes-networking-on-azure-stack-edge-pro"></a>Kubernetes Networking na Azure Stack EDGE Pro

Calico, Metallb i Core DNS to wszystkie składniki, które są zainstalowane dla sieci na Azure Stack Edge. 

- **Calico** przypisuje adres IP z prywatnego zakresu adresów IP do każdego pod i konfiguruje sieć dla tych zasobników, tak aby w przypadku jednego węzła można było komunikować się z pod innym węzłem. 
- **Metallb** działa w klastrze pod i przypisuje adresy IP do usług typu równoważenia obciążenia. Adresy IP modułu równoważenia obciążenia są wybierane z zakresu adresów IP usługi dostarczonych za pośrednictwem lokalnego interfejsu użytkownika. 
- **Podstawowa usługa DNS** — ten dodatek konfiguruje rekordy DNS mapowanie nazwy usługi na adres IP klastra.

Adresy IP używane dla węzłów Kubernetes i usług zewnętrznych są udostępniane za pośrednictwem strony **sieci obliczeniowej** w lokalnym interfejsie użytkownika urządzenia.

![Kubernetes przypisanie adresu IP w lokalnym interfejsie użytkownika](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-ip-assignment-local-ui-1.png)

Przypisanie adresu IP:

- **Adresy IP węzła Kubernetes**: ten zakres adresów jest używany dla węzłów głównych Kubernetes i procesów roboczych. Ten adres IP jest używany, gdy węzły Kubernetes komunikują się ze sobą.

- **Adresy IP usług zewnętrznych Kubernetes**: ten zakres adresów jest używany dla usług zewnętrznych (znanych również jako usługi Load Balancer), które są udostępniane poza klastrem Kubernetes.  


## <a name="next-steps"></a>Następne kroki

Aby skonfigurować sieć Kubernetes na Azure Stack EDGE Pro, zobacz:

- [Udostępnienie bezstanowej aplikacji na Azure Stack EDGE Pro za pośrednictwem IoT Edge](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md).

- [Udostępnienie bezstanowej aplikacji na Azure Stack brzeg Pro za pośrednictwem kuebctl](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md).
