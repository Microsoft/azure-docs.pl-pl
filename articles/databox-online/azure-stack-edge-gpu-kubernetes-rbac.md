---
title: Informacje na temat kontroli dostępu opartej na rolach Kubernetes na urządzeniu Azure Stack EDGE Pro | Microsoft Docs
description: Opisuje, w jaki sposób kontrola dostępu oparta na rolach Kubernetes odbywa się na urządzeniu z Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: alkohli
ms.openlocfilehash: 9a9625dcf40ae7d11e1154fc89b7f04652c8ca16
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2020
ms.locfileid: "94635844"
---
# <a name="kubernetes-role-based-access-control-on-your-azure-stack-edge-pro-gpu-device"></a>Kubernetes kontrolę dostępu opartą na rolach na urządzeniu z procesorem GPU Pro Azure Stack Edge


Na urządzeniu Azure Stack EDGE Pro podczas konfigurowania roli obliczeniowej tworzony jest klaster Kubernetes. Za pomocą kontroli dostępu opartej na rolach Kubernetes (Kubernetes RBAC) można ograniczyć dostęp do zasobów klastra na urządzeniu.

Ten artykuł zawiera omówienie systemu Kubernetes RBAC dostarczonego przez Kubernetes i sposób, w jaki zaimplementowano Kubernetes RBAC na urządzeniu Azure Stack EDGE Pro. 

## <a name="kubernetes-rbac"></a>Kontrola dostępu na podstawie ról na platformie Kubernetes

Kubernetes RBAC pozwala przypisywać użytkowników lub grupy użytkowników, uprawnienia do wykonywania takich czynności jak tworzenie lub modyfikowanie zasobów lub wyświetlanie dzienników z uruchamiania obciążeń aplikacji. Te uprawnienia mogą być ograniczone do pojedynczej przestrzeni nazw lub przyznawane przez cały klaster. 

Podczas konfigurowania klastra Kubernetes zostaje utworzony jeden użytkownik odpowiadający temu klastrowi i jest on nazywany administratorem klastra.  `kubeconfig`Plik jest skojarzony z użytkownikiem administratora klastra. `kubeconfig`Plik jest plikiem tekstowym zawierającym wszystkie informacje o konfiguracji wymagane do nawiązania połączenia z klastrem w celu uwierzytelnienia użytkownika.

## <a name="namespaces-types"></a>Typy przestrzeni nazw

Zasoby Kubernetes, takie jak grupy miar i wdrożenia, są logicznie pogrupowane w przestrzeni nazw. Dzięki tym grupom można logicznie podzielić klaster Kubernetes i ograniczyć dostęp do tworzenia, wyświetlania i zarządzania zasobami. Użytkownicy mogą korzystać tylko z zasobami w ramach przypisanych przestrzeni nazw.

Przestrzenie nazw są przeznaczone do użycia w środowiskach, w których wielu użytkowników rozprzestrzenia się między wieloma zespołami lub projektami. Aby uzyskać więcej informacji, zobacz [Kubernetes przestrzenie nazw](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/).

Urządzenie Azure Stack EDGE Pro zawiera następujące przestrzenie nazw:

- **Przestrzeń nazw systemu** — w tym obszarze nazw znajdują się zasoby podstawowe, takie jak usługa DNS i serwer proxy, lub pulpit nawigacyjny Kubernetes. Zwykle nie są wdrażane własne aplikacje w tej przestrzeni nazw. Ta przestrzeń nazw służy do debugowania wszelkich problemów z klastrem Kubernetes. 

    Na urządzeniu istnieje wiele przestrzeni nazw systemu, a nazwy odpowiadające tym systemowym przestrzeniom nazw są zarezerwowane. Poniżej znajduje się lista zarezerwowanych przestrzeni nazw systemu: 
    - polecenia — system
    - metallb — system
    - DBE — przestrzeń nazw
    - default
    - Kubernetes — pulpit nawigacyjny
    - polecenia — dzierżawa węzła
    - polecenia — publiczny


    Upewnij się, że nie używasz nazw zarezerwowanych dla tworzonych przestrzeni nazw użytkownika. 
<!--- **default namespace** - This namespace is where pods and deployments are created by default when none is provided and you have admin access to this namespace. When you interact with the Kubernetes API, such as with `kubectl get pods`, the default namespace is used when none is specified.-->

- **Przestrzeń nazw użytkownika** — te przestrzenie nazw można tworzyć za pośrednictwem **polecenia kubectl** lub za pomocą interfejsu programu PowerShell urządzenia do lokalnego wdrażania aplikacji.
 
- **IoT Edge przestrzeń nazw** — Połącz się z tą `iotedge` przestrzenią nazw, aby zarządzać aplikacjami wdrożonymi za pośrednictwem IoT Edge.

- **Przestrzeń nazw usługi Azure Arc** — Połącz się z tą `azure-arc` przestrzenią nazw, aby zarządzać aplikacjami wdrożonymi za pośrednictwem usługi Azure Arc. Za pomocą usługi Azure Arc można także wdrażać aplikacje w innych obszarach nazw użytkowników. 

## <a name="namespaces-and-users"></a>Przestrzenie nazw i użytkownicy

W świecie rzeczywistym należy podzielić klaster na wiele przestrzeni nazw. 

- **Wielu użytkowników** : Jeśli masz wielu użytkowników, wówczas wiele obszarów nazw zezwoli tym użytkownikom na wdrożenie ich aplikacji i usług w określonych przestrzeniach nazw w izolacji ze sobą. 
- **Pojedynczy użytkownik** : nawet jeśli istnieje pojedynczy użytkownik, wiele przestrzeni nazw zezwoli temu użytkownikowi na uruchamianie wielu wersji aplikacji w tym samym klastrze Kubernetes.

### <a name="roles-and-rolebindings"></a>Role i RoleBindings

Kubernetes ma koncepcje powiązań ról i ról, które umożliwiają Przyznawanie uprawnień użytkownikom lub zasobom na poziomie przestrzeni nazw i na poziomie klastra. 

- **Role** : można zdefiniować uprawnienia dla użytkowników jako **rolę** , a następnie użyć **ról** do udzielenia uprawnień w przestrzeni nazw. 
- **RoleBindings** : po zdefiniowaniu ról można użyć **RoleBindings** do przypisania ról dla danego obszaru nazw. 

Takie podejście umożliwia logicznie segregowanie pojedynczego klastra Kubernetes, dzięki czemu użytkownicy mogą uzyskiwać dostęp do zasobów aplikacji w ich przypisanej przestrzeni nazw. 

## <a name="kubernetes-rbac-on-azure-stack-edge-pro"></a>Kubernetes RBAC na Azure Stack EDGE Pro

W bieżącej implementacji usługi Kubernetes RBAC Azure Stack EDGE Pro umożliwia wykonywanie następujących akcji w obszarze działania programu PowerShell z ograniczeniami:

- Utwórz przestrzenie nazw.  
- Utwórz dodatkowych użytkowników.
- Przyznaj administratorowi dostęp do utworzonych przestrzeni nazw. Należy pamiętać, że nie będziesz mieć dostępu do roli administratora klastra ani widoku zasobów w całym klastrze.
- Pobierz `kubeconfig` plik z informacjami, aby uzyskać dostęp do klastra Kubernetes.


Urządzenie Azure Stack EDGE Pro ma wiele przestrzeni nazw systemu i można utworzyć przestrzenie nazw użytkowników z `kubeconfig` plikami, aby uzyskać dostęp do tych przestrzeni nazw. Użytkownicy mają pełną kontrolę nad tymi przestrzeniami nazw i mogą tworzyć lub modyfikować użytkowników lub przyznawać dostęp użytkownikom. Tylko administrator klastra ma pełny dostęp do przestrzeni nazw systemu i zasobów cały klaster. `aseuser`Ma dostęp tylko do odczytu do przestrzeni nazw systemu.

Poniżej znajduje się Diagram przedstawiający implementację Kubernetes RBAC na urządzeniu Azure Stack EDGE Pro.

![Kubernetes RBAC na urządzeniu Azure Stack EDGE Pro](./media/azure-stack-edge-gpu-kubernetes-rbac/rbac-view-1.png)

Na tym diagramie Alicja, Robert i Jan mają dostęp do przypisanych przestrzeni nazw użytkowników, które w tym przypadku są `ns1` , `ns2` i `ns3` odpowiednio. W tych obszarach nazw mają dostęp administratora. Administrator klastra z drugiej strony ma dostęp administratora do przestrzeni nazw systemu i zasobów cały klaster.

Jako użytkownik można tworzyć przestrzenie nazw i użytkowników, przypisywać użytkowników do przestrzeni nazw lub pobierać `kubeconfig` pliki. Aby uzyskać szczegółowe instrukcje krok po kroku, przejdź do pozycji [dostęp do klastra Kubernetes za pośrednictwem Azure Stack usługi kuebctl w witrynie EDGE Pro](azure-stack-edge-gpu-create-kubernetes-cluster.md).


Podczas pracy z przestrzeniami nazw i użytkownikami na urządzeniach z systemem Azure Stack EDGE Pro mają zastosowanie następujące zastrzeżenia:

- Nie można wykonywać żadnych operacji, takich jak tworzenie użytkowników, przyznawanie lub odwoływanie dostępu do przestrzeni nazw do użytkownika dla dowolnych przestrzeni nazw systemu. Przykłady przestrzeni nazw systemu obejmują `kube-system` , `metallb-system` , `kubernetes-dashboard` , `default` , `kube-node-lease` , `kube-public` . Przestrzenie nazw systemu obejmują również przestrzenie nazw zarezerwowane dla typów wdrożeń, takich jak `iotedge` (IoT Edge Namespace) i `azure-arc` (przestrzeń nazw usługi Azure ARC).
- Można utworzyć przestrzenie nazw użytkowników i w ramach tych przestrzeni nazw utworzyć dodatkowych użytkowników i udzielić lub odwołać dostęp do przestrzeni nazw dla tych użytkowników.
- Nie można utworzyć żadnych przestrzeni nazw o nazwach identycznych z tymi dla każdej przestrzeni nazw systemu. Nazwy systemowych przestrzeni nazw są zastrzeżone.  
- Nie można tworzyć żadnych przestrzeni nazw użytkownika o nazwach, które są już używane przez inne przestrzenie nazw użytkownika. Na przykład, jeśli masz `test-ns` utworzony, nie możesz utworzyć kolejnej `test-ns` przestrzeni nazw.
- Nie masz uprawnień do tworzenia użytkowników z nazwami, które są już zarezerwowane. Na przykład `aseuser` jest zastrzeżonym użytkownikiem i nie można go użyć.


## <a name="next-steps"></a>Następne kroki

Aby zrozumieć, jak można utworzyć użytkownika, utworzyć obszar nazw i udzielić użytkownikowi dostępu do przestrzeni nazw, zobacz dostęp do [klastra Kubernetes za pośrednictwem polecenia kubectl](azure-stack-edge-gpu-create-kubernetes-cluster.md).

