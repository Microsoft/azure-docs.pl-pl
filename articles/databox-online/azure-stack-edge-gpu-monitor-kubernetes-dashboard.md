---
title: Monitoruj urządzenie Azure Stack EDGE Pro za pośrednictwem pulpitu nawigacyjnego Kubernetes | Microsoft Docs
description: Zawiera opis sposobu uzyskiwania dostępu do pulpitu nawigacyjnego Kubernetes i używania go do monitorowania urządzenia z usługą Azure Stack EDGE Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 19b3595228c29814e42af88696972fd81b156190
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443050"
---
# <a name="use-kubernetes-dashboard-to-monitor-your-azure-stack-edge-pro-gpu-device"></a>Korzystanie z pulpitu nawigacyjnego Kubernetes do monitorowania urządzenia z procesorem GPU w Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

W tym artykule opisano, jak uzyskać dostęp do pulpitu nawigacyjnego Kubernetes i korzystać z niego do monitorowania urządzenia z procesorem GPU w Azure Stack Edge. Aby monitorować urządzenie, można użyć wykresów w Azure Portal, wyświetlić pulpit nawigacyjny Kubernetes lub uruchomić `kubectl` polecenia za pomocą interfejsu programu PowerShell urządzenia. 

Ten artykuł koncentruje się tylko na zadaniach monitorowania, które można wykonać na pulpicie nawigacyjnym Kubernetes.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
>
> * Dostęp do pulpitu nawigacyjnego Kubernetes na urządzeniu
> * Wyświetlanie modułów wdrożonych na urządzeniu
> * Uzyskaj adres IP dla aplikacji wdrożonych na urządzeniu
> * Wyświetlanie dzienników kontenerów dla modułów wdrożonych na urządzeniu


## <a name="about-kubernetes-dashboard"></a>Informacje o pulpicie nawigacyjnym Kubernetes

Pulpit nawigacyjny Kubernetes jest internetowym interfejsem użytkownika, którego można użyć do rozwiązywania problemów z aplikacjami w kontenerach. Pulpit nawigacyjny Kubernetes jest alternatywną metodą interfejsu `kubectl` wiersza polecenia Kubernetes. Aby uzyskać więcej informacji, zobacz [Pulpit nawigacyjny Kubernetes](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/). 

Na urządzeniu Azure Stack EDGE Pro możesz użyć pulpitu nawigacyjnego Kubernetes w trybie *tylko do odczytu* , aby uzyskać przegląd aplikacji uruchomionych na urządzeniu Azure Stack EDGE Pro, wyświetlić stan zasobów klastra Kubernetes i zobaczyć błędy, które wystąpiły na urządzeniu.

## <a name="access-dashboard"></a>Dostęp do pulpitu nawigacyjnego

Pulpit nawigacyjny Kubernetes jest *tylko do odczytu* i uruchamiany w węźle głównym Kubernetes na porcie 31000. Wykonaj następujące kroki, aby uzyskać dostęp do pulpitu nawigacyjnego: 

1. W lokalnym interfejsie użytkownika urządzenia przejdź do pozycji **urządzenie** , a następnie przejdź do obszaru **punkty końcowe urządzenia**. 
1. Wybierz pozycję **Pobierz konfigurację** , aby pobrać plik `kubeconfig` , który umożliwia dostęp do pulpitu nawigacyjnego. Zapisz `config.json` plik w systemie lokalnym.
1. Wybierz adres URL pulpitu nawigacyjnego Kubernetes, aby otworzyć pulpit nawigacyjny w przeglądarce.

    ![Adres URL pulpitu nawigacyjnego Kubernetes na stronie urządzenia w lokalnym interfejsie użytkownika](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-url-local-ui-1.png)

1. Na stronie **logowania na pulpicie nawigacyjnym Kubernetes** :
    
    1. Wybierz pozycję **kubeconfig**. 
        ![Wybierz opcję kubeconfig](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-sign-in-1.png) 
    1. Wybierz wielokropek **...**. Przeglądaj i wskaż `kubeconfig` pobrany wcześniej w systemie lokalnym. Wybierz pozycję **Zaloguj się**.
        ![Przejdź do pliku kubeconfig](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-sign-in-2.png)    

6. Teraz możesz wyświetlić pulpit nawigacyjny Kubernetes dla urządzenia z systemem Azure Stack EDGE Pro w trybie tylko do odczytu.

    ![Strona główna pulpitu nawigacyjnego Kubernetes](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-main-page-1.png)

## <a name="view-module-status"></a>Wyświetl stan modułu

Moduły obliczeniowe są kontenerami, w których zaimplementowano logikę biznesową. Możesz użyć pulpitu nawigacyjnego, aby sprawdzić, czy moduł obliczeniowy został pomyślnie wdrożony na urządzeniu Azure Stack EDGE Pro.

Aby wyświetlić stan modułu, wykonaj następujące kroki na pulpicie nawigacyjnym:

1. W lewym okienku pulpitu nawigacyjnego przejdź do **obszaru nazw**. Filtruj według przestrzeni nazw, w której są wyświetlane IoT Edge modułów, w tym przypadku **iotedge**.
1. W lewym okienku przejdź do pozycji **obciążenia > wdrożenia**.
1. W okienku po prawej stronie zostaną wyświetlone wszystkie moduły wdrożone na urządzeniu. W takim przypadku moduł GettingStartedWithGPU został wdrożony na Azure Stack brzeg Pro. Można zobaczyć, że moduł został wdrożony.

    ![Wyświetl wdrożenie modułu](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-module-deployment-1.png)

 
## <a name="get-ip-address-for-services-or-modules"></a>Uzyskaj adres IP dla usług lub modułów

Możesz użyć pulpitu nawigacyjnego, aby uzyskać adresy IP usług lub modułów, które chcesz uwidocznić poza klastrem Kubernetes. 

Zakres adresów IP dla tych usług zewnętrznych można przypisać za pomocą lokalnego interfejsu użytkownika sieci Web urządzenia na stronie **Ustawienia sieci obliczeniowej** . Po wdrożeniu modułów IoT Edge można uzyskać adres IP przypisany do określonego modułu lub usługi. 

Aby uzyskać adres IP, wykonaj następujące kroki na pulpicie nawigacyjnym:

1. W lewym okienku pulpitu nawigacyjnego przejdź do **obszaru nazw**. Filtrowanie według przestrzeni nazw, w której wdrożono usługę zewnętrzną, w tym przypadku **iotedge**.
1. W okienku po lewej stronie przejdź do pozycji **odnajdywanie i równoważenie obciążenia > usługi**.
1. W okienku po prawej stronie zostaną wyświetlone wszystkie usługi, które działają w `iotedge` przestrzeni nazw na urządzeniu Azure Stack EDGE Pro.

    ![Uzyskaj adres IP dla usług zewnętrznych](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-get-ip-external-service-1.png)

## <a name="view-container-logs"></a>Wyświetlanie dzienników kontenerów

Istnieją wystąpienia, w których konieczne jest wyświetlenie dzienników kontenerów. Możesz użyć pulpitu nawigacyjnego, aby uzyskać dzienniki dla określonego kontenera, który został wdrożony w klastrze Kubernetes.

Aby wyświetlić dzienniki kontenerów, wykonaj następujące kroki na pulpicie nawigacyjnym:

1. W lewym okienku pulpitu nawigacyjnego przejdź do **obszaru nazw**. Filtruj według przestrzeni nazw, w której wdrożono moduły IoT Edge, w tym przypadku **iotedge**.
1. W lewym okienku przejdź do pozycji **obciążenia > zasobniki**.
1. W okienku po prawej stronie zostaną wyświetlone wszystkie zasobniki uruchomione na urządzeniu. Określ pod, który jest uruchomiony moduł, dla którego chcesz wyświetlić dzienniki. Wybierz wielokropek pionowy dla zidentyfikowanego pod, a następnie z menu kontekstowego wybierz pozycję **dzienniki**.

    ![Wyświetl dzienniki kontenerów 1](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)

1. Dzienniki są wyświetlane w podglądzie dzienników wbudowanym do pulpitu nawigacyjnego. Możesz również pobrać dzienniki.

    ![Wyświetlanie dzienników kontenerów 2](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)
    

## <a name="view-cpu-memory-usage"></a>Wyświetlanie użycia procesora CPU, pamięci

Pulpit nawigacyjny Kubernetes dla urządzenia z systemem Azure Stack EDGE Pro ma także [dodatek serwer metryk](https://kubernetes.io/docs/tasks/debug-application-cluster/resource-metrics-pipeline/) , który agreguje użycie procesora i pamięci w zasobach Kubernetes.
 
Na przykład można wyświetlić procesor i pamięć zużywaną między wdrożeniami we wszystkich przestrzeniach nazw. 

![Wyświetlanie użycia procesora i pamięci we wszystkich wdrożeniach](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/view-cpu-memory-all-1.png)

Można również filtrować według określonej przestrzeni nazw. W poniższym przykładzie można wyświetlić użycie procesora i pamięci tylko dla wdrożeń usługi Azure Arc.  

![Wyświetlanie użycia procesora i pamięci dla wdrożeń usługi Azure Arc](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/view-cpu-memory-azure-arc-1.png)

Serwer metryk Kubernetes zapewnia potoki skalowania automatycznego, takie jak [poziome skalowanie w poziomie](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/).


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [rozwiązywać problemy z urządzeniem](azure-stack-edge-gpu-troubleshoot.md).
