---
title: Monitoruj urządzenie Azure Stack Edge za pośrednictwem pulpitu nawigacyjnego Kubernetes | Microsoft Docs
description: Opisuje sposób uzyskiwania dostępu do pulpitu nawigacyjnego Kubernetes i używania go do monitorowania urządzenia brzegowego Azure Stack.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/27/2020
ms.author: alkohli
ms.openlocfilehash: 9224888a38c86e35df9ad516c761fd7012824c15
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89085281"
---
# <a name="use-kubernetes-dashboard-to-monitor-your-azure-stack-edge-gpu-device"></a>Korzystanie z pulpitu nawigacyjnego Kubernetes do monitorowania urządzenia z procesorem GPU Azure Stack Edge

W tym artykule opisano, jak uzyskać dostęp do pulpitu nawigacyjnego Kubernetes i korzystać z niego do monitorowania urządzeń Azure Stack z procesorem GPU. Aby monitorować urządzenie, można użyć wykresów w Azure Portal, wyświetlić pulpit nawigacyjny Kubernetes lub uruchomić `kubectl` polecenia za pomocą interfejsu programu PowerShell urządzenia. 

Ten artykuł koncentruje się tylko na zadaniach monitorowania, które można wykonać na pulpicie nawigacyjnym Kubernetes.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
>
> * Dostęp do pulpitu nawigacyjnego Kubernetes na urządzeniu
> * Wyświetlanie modułów wdrożonych na urządzeniu
> * Uzyskaj adres IP dla aplikacji wdrożonych na urządzeniu
> * Wyświetlanie dzienników kontenerów dla modułów wdrożonych na urządzeniu


## <a name="about-kubernetes-dashboard"></a>Informacje o pulpicie nawigacyjnym Kubernetes

Pulpit nawigacyjny Kubernetes jest interfejsem użytkownika opartym na sieci Web, którego można użyć do rozwiązywania problemów z aplikacjami w kontenerach. Pulpit nawigacyjny Kubernetes jest alternatywną metodą interfejsu `kubectl` wiersza polecenia Kubernetes. 

Na urządzeniu Azure Stack Edge możesz użyć pulpitu nawigacyjnego Kubernetes w trybie tylko do odczytu, aby uzyskać przegląd aplikacji uruchomionych na urządzeniu brzegowym Azure Stack, wyświetlić stan zasobów klastra Kubernetes i zobaczyć błędy, które wystąpiły na urządzeniu.

## <a name="access-dashboard"></a>Dostęp do pulpitu nawigacyjnego

Pulpit nawigacyjny Kubernetes jest tylko do odczytu i uruchamiany w węźle głównym Kubernetes na porcie 31000. Wykonaj następujące kroki, aby uzyskać dostęp do pulpitu nawigacyjnego: 

1. W lokalnym interfejsie użytkownika urządzenia przejdź do pozycji **urządzenie** , a następnie przejdź do obszaru **punkty końcowe urządzenia**. Wybierz adres URL pulpitu nawigacyjnego Kubernetes, aby otworzyć pulpit nawigacyjny w przeglądarce.

    ![Adres URL pulpitu nawigacyjnego Kubernetes na stronie urządzenia w lokalnym interfejsie użytkownika](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-url-local-ui-1.png)

1. Na stronie **logowania do pulpitu nawigacyjnego Kubernetes** wybierz pozycję **token**. 
1. Podaj token. 
    1. Aby uzyskać token, [Połącz się za pośrednictwem interfejsu programu PowerShell urządzenia](azure-stack-edge-gpu-connect-powershell-interface.md).
    1. Uruchom polecenie:  `Get-HcsKubernetesDashboardToken`
    
    1. Skopiuj przedstawiony ciąg tokenu w wierszu polecenia. Oto przykładowe dane wyjściowe:
        
        ```powershell
        [10.100.10.10]: PS>Get-HcsKubernetesDashboardToken
        eyJhbGciOiJSUzI1NiIsImtpZCI6IkpFTEtBYTMyZ0Ezb01OYTVFSnVaUV85OWtLdXNETTZQR0k0UlFybGdReFUifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJrdWJlcm5ldGVzLWRhc2hib2FyZC10b2tlbi03czZ6ayIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6IjU3NzY3ZDAzLTJlYWUtNDlkMi1hNDEyLTNkOTU3MDFiMThiMyIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDprdWJlcm5ldGVzLWRhc2hib2FyZDprdWJlcm5ldGVzLWRhc2hib2FyZCJ9.UgNrpVYVJBEaWxFlljuENUQQmzFXMYG2VsJUIYFdp2AO20zX0k5dRvwcCpeGlqSKb9MyYjG0c6RmT9uCOZk-vAwt7btszQLD7KPCwh_nn_NiIyO8ApgGRYZP8NuP8CBTX3tl_hpwfHtZ0ksbuKAduIL-0uPF0rG5wgLk9cTEw6fKSc2UZW6bIzhNSp_uSiP6MexOS6OftF9JFZejkIGd33dSp-k-tgFlm2Zy96sdFJC0q-XsH7jygiVnfxA9XMs5wqW26LkCh0rfO2WI3C1XFK-4TpufRZLJHo5WPlu-Tnsxa8xmtk2jQ3us-sXcBRrvhPNPrNKkbqc9hbjmWfGD0Q
        [10.100.10.10]: PS>
        ```
        
1. Wybierz pozycję **Zaloguj się**.

    ![Zaloguj się do pulpitu nawigacyjnego Kubernetes](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-sign-in-1.png)

6. Teraz możesz wyświetlić pulpit nawigacyjny usługi Kubernetes dla urządzenia brzegowego Azure Stack w trybie tylko do odczytu.

    ![Strona główna pulpitu nawigacyjnego Kubernetes](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-main-page-1.png)

## <a name="view-module-status"></a>Wyświetl stan modułu

Moduły obliczeniowe są kontenerami, w których zaimplementowano logikę biznesową. Możesz użyć pulpitu nawigacyjnego, aby sprawdzić, czy moduł obliczeniowy został pomyślnie wdrożony na urządzeniu Azure Stack Edge.

Aby wyświetlić stan modułu, wykonaj następujące kroki na pulpicie nawigacyjnym:

1. W lewym okienku pulpitu nawigacyjnego przejdź do **obszaru nazw**. Filtruj według przestrzeni nazw, w której są wyświetlane IoT Edge modułów, w tym przypadku **iotedge**.
1. W lewym okienku przejdź do pozycji **obciążenia > wdrożenia**.
1. W okienku po prawej stronie zostaną wyświetlone wszystkie moduły wdrożone na urządzeniu. W takim przypadku moduł GettingStartedWithGPU został wdrożony na Azure Stack brzegowej. Można zobaczyć, że moduł został wdrożony.

    ![Wyświetl wdrożenie modułu](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-module-deployment-1.png)

 
## <a name="get-ip-address-for-services-or-modules"></a>Uzyskaj adres IP dla usług lub modułów

Możesz użyć pulpitu nawigacyjnego, aby uzyskać adresy IP usług lub modułów, które chcesz uwidocznić poza klastrem Kubernetes. 

Zakres adresów IP dla tych usług zewnętrznych można przypisać za pomocą lokalnego interfejsu użytkownika sieci Web urządzenia na stronie **Ustawienia sieci obliczeniowej** . Po wdrożeniu modułów IoT Edge można uzyskać adres IP przypisany do określonego modułu lub usługi. 

Aby uzyskać adres IP, wykonaj następujące kroki na pulpicie nawigacyjnym:

1. W lewym okienku pulpitu nawigacyjnego przejdź do **obszaru nazw**. Filtrowanie według przestrzeni nazw, w której wdrożono usługę zewnętrzną, w tym przypadku **iotedge**.
1. W okienku po lewej stronie przejdź do pozycji **odnajdywanie i równoważenie obciążenia > usługi**.
1. W okienku po prawej stronie zostaną wyświetlone wszystkie usługi, które działają w `iotedge` przestrzeni nazw na urządzeniu Azure Stack Edge.

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
    

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak rozwiązywać problemy z Kubernetes <!--insert link-->.
