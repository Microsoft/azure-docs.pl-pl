---
title: Wdrażanie `Guestbook` aplikacji PHP na łuku z włączonym Kubernetes na urządzeniu z systemem Azure Stack EDGE Pro GPU | Microsoft Docs
description: Zawiera opis sposobu wdrażania `Guestbook` bezstanowej aplikacji PHP przy użyciu usługi Redis za pomocą GitOps w klastrze z włączonym centrum Kubernetes na urządzeniu Azure Stack EDGE Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 920f7912a1289fe92618d893b94943784e4a9a3a
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102520731"
---
# <a name="deploy-a-php-guestbook-stateless-application-with-redis-on-arc-enabled-kubernetes-cluster-on-azure-stack-edge-pro-gpu"></a>Wdróż `Guestbook` bezstanową aplikację PHP z Redis na łuku z włączonym klastrem Kubernetes w systemie Azure Stack Edge — procesor GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

W tym artykule pokazano, jak skompilować i wdrożyć prostą wielowarstwową aplikację sieci Web przy użyciu Kubernetes i Azure Arc. Ten przykład składa się z następujących składników:

- Redis wzorzec pojedynczego wystąpienia do przechowywania `guestbook` wpisów
- Wiele zreplikowanych wystąpień Redis do obsługiwania operacji odczytu
- Wiele wystąpień frontonu sieci Web

Wdrożenie jest wykonywane przy użyciu GitOps w klastrze Kubernetes z włączonym łukiem na urządzeniu z systemem Azure Stack EDGE Pro. 

Ta procedura jest przeznaczona dla osób, które sprawdziły [obciążenia Kubernetes na urządzeniu Azure Stack EDGE Pro](azure-stack-edge-gpu-kubernetes-workload-management.md) i znają koncepcje [co to jest usługa Azure Arc Kubernetes (wersja zapoznawcza)](../azure-arc/kubernetes/overview.md).

> [!NOTE]
> Ten artykuł zawiera odwołania do warunku podrzędnego, termin, który nie jest już wykorzystywany przez firmę Microsoft. Gdy termin zostanie usunięty z oprogramowania, usuniemy go z tego artykułu.

## <a name="prerequisites"></a>Wymagania wstępne

Przed wdrożeniem bezstanowej aplikacji upewnij się, że zostały spełnione następujące wymagania wstępne na urządzeniu oraz klient, który będzie używany do uzyskiwania dostępu do urządzenia:

> [!NOTE]
> Ten artykuł zawiera odwołania do warunku podrzędnego, termin, który nie jest już wykorzystywany przez firmę Microsoft. Gdy termin zostanie usunięty z oprogramowania, usuniemy go z tego artykułu.

### <a name="for-device"></a>Na potrzeby urządzenia

1. Poświadczenia logowania są dostępne na urządzeniu z 1 węzłem Azure Stack Edge.
    1. Urządzenie zostało aktywowane. Zobacz [Aktywowanie urządzenia](azure-stack-edge-gpu-deploy-activate.md).
    1. Urządzenie ma rolę obliczeniową skonfigurowaną za pośrednictwem Azure Portal i ma klaster Kubernetes. Zobacz [Konfigurowanie obliczeń](azure-stack-edge-gpu-deploy-configure-compute.md).

1. Usługa Azure Arc została włączona na istniejącym klastrze Kubernetes na urządzeniu i masz odpowiedni zasób usługi Azure Arc w Azure Portal. Aby uzyskać szczegółowe instrukcje, zobacz [Włączanie usługi Azure Arc na urządzeniu Azure Stack EDGE Pro](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md).

### <a name="for-client-accessing-the-device"></a>Do uzyskiwania dostępu do urządzenia przez klienta

1. Masz system klienta systemu Windows, który będzie używany do uzyskiwania dostępu do urządzenia z systemem Azure Stack Edge.
  
    - Klient korzysta z programu Windows PowerShell 5,0 lub nowszego. Aby pobrać najnowszą wersję programu Windows PowerShell, przejdź do obszaru [Instalowanie programu Windows PowerShell](/powershell/scripting/install/installing-windows-powershell).
    
    - Możesz również mieć dowolnego innego klienta z [obsługiwanym systemem operacyjnym](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) . W tym artykule opisano procedurę w przypadku korzystania z klienta systemu Windows. 
    
1. Procedura opisana w artykule [Uzyskiwanie dostępu do klastra Kubernetes na urządzeniu z systemem Azure Stack Edge w systemie](azure-stack-edge-gpu-create-kubernetes-cluster.md). Masz:
    
    - Zainstalowane `kubectl` na kliencie programu. <!--and saved the `kubeconfig` file with the user configuration to C:\\Users\\&lt;username&gt;\\.kube. -->
    
    - Upewnij się, że `kubectl` wersja klienta jest skośna nie więcej niż jedna wersja z wersji głównej Kubernetes działającej na urządzeniu Azure Stack EDGE Pro. 
      - Użyj, `kubectl version` Aby sprawdzić wersję polecenia kubectl działającą na kliencie. Zanotuj pełną wersję.
      - W lokalnym interfejsie użytkownika urządzenia z usługą Azure Stack Edge, przejdź do **omówienia** i zanotuj numer oprogramowania Kubernetes. 
      - Sprawdź te dwie wersje pod kątem zgodności z mapowania podanego w obsługiwanej wersji Kubernetes. <!--insert link-->

1. Istnieje [Konfiguracja GitOps, której można użyć do uruchomienia wdrożenia usługi Azure Arc](https://github.com/kagoyal/dbehaikudemo). W tym przykładzie zostaną użyte następujące `yaml` pliki do wdrożenia na urządzeniu Azure Stack EDGE Pro.

    - `frontend-deployment.yaml`<!-- - The guestbook application has a web frontend serving the HTTP requests written in PHP. It is configured to connect to the redis-master Service for write requests and the redis-slave service for Read requests. This file describes a deployment that runs the frontend of the guestbook application.-->
    - `frontend-service.yaml` <!-- - This allows you to configure an externally visible frontend Service that can be accessed from outside the Kubernetes cluster on your device.-->
    - `redis-master-deployment.yaml` <!-- - This deployment file runs a single replica Redis master Pod. The guestbook application uses Redis to store its data. It writes its data to a Redis master instance and reads data from multiple Redis slave instances.-->
    - `redis-master-service.yaml` <!-- - The guestbook application needs to communicate to the Redis master to write its data. You need to apply a Service to proxy the traffic to the Redis master Pod. A Service defines a policy to access the Pods.-->
    - `redis-slave-deployment.yaml` <!-- - Although the Redis master is a single pod, you can make it highly available to meet traffic demands by adding replica Redis slaves. The Redis Slave Deployment specifies two replicas.-->
    - `redis-slave-service.yaml` <!-- - The guestbook application needs to communicate to Redis slaves to read data. To make the Redis slaves discoverable, you need to set up a Service. A Service provides transparent load balancing to a set of Pods.-->

<!-- az cli version requirements-->


## <a name="deploy-configuration"></a>Wdrażanie konfiguracji

Wykonaj następujące kroki, aby skonfigurować zasób usługi Azure Arc do wdrażania konfiguracji GitOps za pośrednictwem Azure Portal: 

1. W Azure Portal przejdź do zasobu usługi Azure ARC, który został utworzony po włączeniu usługi Azure Arc w klastrze Kubernetes na urządzeniu. 

    ![Przejdź do zasobu usługi Azure Arc](media/azure-stack-edge-gpu-connect-powershell-interface/verify-azure-arc-enabled-1.png)

1. Przejdź do pozycji **konfiguracje** i wybierz pozycję **+ Dodaj konfigurację**.

    ![Zrzut ekranu przedstawia klaster Kubernetes z włączoną funkcją Azure ARC z wybraną pozycją Dodaj konfigurację.](media/azure-stack-edge-gpu-connect-powershell-interface/select-configurations-1.png)

1. W obszarze **Dodaj konfigurację** wprowadź odpowiednie wartości pól, a następnie wybierz pozycję **Zastosuj**.

    |Parametr  |Opis |
    |---------|---------|
    |Nazwa konfiguracji     | Nazwa zasobu konfiguracji.        |
    |Nazwa wystąpienia operatora     |Nazwa wystąpienia operatora służącego do identyfikowania określonej konfiguracji. Nazwa jest ciągiem zawierającym maksymalnie 253 znaków, które muszą zawierać małe litery, alfanumeryczne, łączniki i kropki.         |
    |Przestrzeń nazw operatora     | Ustaw wartość **demotestguestbook** na zgodną z przestrzenią nazw określoną we wdrożeniu `yaml` . <br> Pole definiuje przestrzeń nazw, w której zainstalowano operatora. Nazwa jest ciągiem zawierającym maksymalnie 253 znaków, które muszą zawierać małe litery, alfanumeryczne, łączniki i kropki.         |
    |Adres URL repozytorium     |<br>Ścieżka do repozytorium Git w `http://github.com/username/repo` lub w `git://github.com/username/repo` formacie, w którym znajduje się konfiguracja GitOps.         |
    |Zakres operatorów     | Wybierz pozycję **przestrzeń nazw**. <br>Ten parametr określa zakres, w którym zainstalowano operatora. Wybierz przestrzeń nazw, aby zainstalować operator w przestrzeni nazw określonej w plikach wdrożenia YAML.       |
    |Typ operatora     | Pozostaw domyślnie. <br>Ten parametr określa typ operatora — domyślnie ustawia się jako strumień.        |
    |Parametry operatora     | Pozostaw to pole puste. <br>Ten parametr zawiera parametry, które mają zostać przekazane do operatora strumienia.        |
    |Helm     | Ustaw ten parametr na **wyłączony**. <br>Włącz tę opcję, jeśli będziesz wykonywać wdrożenia oparte na wykresach.        |


    ![Dodawanie konfiguracji](media/azure-stack-edge-gpu-connect-powershell-interface/add-configuration-1.png)


1. Wdrożenie konfiguracji rozpocznie się, a **stan operatora** wskazuje jako **oczekujące**. 

    ![Zrzut ekranu przedstawia klaster Kubernetes z obsługą usługi Azure Arc w stanie oczekiwania, gdy zostanie odświeżony.](media/azure-stack-edge-gpu-connect-powershell-interface/view-configurations-1.png)

1. Wdrożenie zajmie kilka minut. Po zakończeniu wdrożenia **stan operatora** zostanie wyświetlony jako **zainstalowano**.

    ![Zrzut ekranu przedstawia klaster Kubernetes z włączoną funkcją Azure Arc w stanie zainstalowanym.](media/azure-stack-edge-gpu-connect-powershell-interface/view-configurations-2.png)

## <a name="verify-deployment"></a>Weryfikowanie wdrożenia

Wdrożenie za pośrednictwem konfiguracji GitOps tworzy `demotestguestbook` przestrzeń nazw określoną w `yaml` plikach wdrożenia znajdujących się w repozytorium git.

1. Po zastosowaniu konfiguracji GitOps Połącz się z [interfejsem programu PowerShell urządzenia](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
1. Uruchom następujące polecenie, aby wyświetlić listę zasobników uruchomionych w `demotestguestbook` przestrzeni nazw odpowiadających wdrożeniu.

    `kubectl get pods -n <your-namespace>`
  
    Oto przykładowe dane wyjściowe.
  
    ```powershell
    [10.128.44.240]: PS>kubectl get pods -n demotestguestbook
    NAME                            READY   STATUS    RESTARTS   AGE
    aseoperator1-5569658644-cqtb5   1/1     Running   0          91m
    frontend-6cb7f8bd65-4xb4f       1/1     Running   0          91m
    frontend-6cb7f8bd65-q9cxj       1/1     Running   0          91m
    frontend-6cb7f8bd65-xpzs6       1/1     Running   0          91m
    memcached-86bdf9f56b-5l2fq      1/1     Running   0          91m
    redis-master-7db7f6579f-2z29w   1/1     Running   0          91m
    redis-slave-7664787fbc-lgr2n    1/1     Running   0          91m
    redis-slave-7664787fbc-vlvzn    1/1     Running   0          91m
    [10.128.44.240]: PS>
    ```  

1. W tym przykładzie usługa frontonu została wdrożona jako typ: moduł równoważenia obciążenia. Musisz znaleźć adres IP tej usługi, aby wyświetlić `guestbook` . Uruchom następujące polecenie.

    `kubectl get service -n <your-namespace>`
    
    ```powershell
    [10.128.44.240]: PS>kubectl get service -n demotestguestbook
    NAME           TYPE           CLUSTER-IP       EXTERNAL-IP     PORT(S)        AGE
    frontend       LoadBalancer   10.96.79.38      10.128.44.245   80:31238/TCP   85m
    memcached      ClusterIP      10.102.47.75     <none>          11211/TCP      85m
    redis-master   ClusterIP      10.104.32.99     <none>          6379/TCP       85m
    redis-slave    ClusterIP      10.104.215.146   <none>          6379/TCP       85m
    [10.128.44.240]: PS>
    ```
1. Usługa frontonu `type:LoadBalancer` ma zewnętrzny adres IP. Ten adres IP pochodzi z zakresu adresów IP określonego dla usług zewnętrznych podczas konfigurowania ustawień sieciowych obliczeń na urządzeniu. Użyj tego adresu IP, aby wyświetlić `guestbook` adres URL: `https://<external-IP-address>` .

    ![Wyświetlanie księgi Gości](media/azure-stack-edge-gpu-connect-powershell-interface/view-guestbook-1.png)

## <a name="delete-deployment"></a>Usuń wdrożenie

Aby usunąć wdrożenie, można usunąć konfigurację z Azure Portal. Usunięcie konfiguracji spowoduje usunięcie obiektów, które zostały utworzone, w tym wdrożeń i usług.

1. W Azure Portal przejdź do konfiguracji > zasobów usługi Azure Arc. 
1. Znajdź konfigurację, którą chcesz usunąć. Wybierz... Aby wywołać menu kontekstowe i wybrać pozycję **Usuń**.
    ![Usuń konfigurację](media/azure-stack-edge-gpu-connect-powershell-interface/delete-configuration-1.png)

Usunięcie konfiguracji może potrwać kilka minut.
 
<!--```powershell
kubectl delete deployment <deployment-name> -n <your-namespace>
kubectl delete service <service-name> -n <your-namespace>
```

Here is a sample output of when you delete the deployments and the services.

```powershell
C:\Users\user>kubectl delete deployment,svc mysql -n userns1
deployment.apps "mysql" deleted
service "mysql" deleted
C:\Users\user>
```-->


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [za pomocą pulpitu nawigacyjnego Kubernetes monitorować wdrożenia na urządzeniu z systemem Azure Stack EDGE Pro](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md)