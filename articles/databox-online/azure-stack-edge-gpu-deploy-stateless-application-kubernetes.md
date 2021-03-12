---
title: Wdrażanie bezstanowej aplikacji Kubernetes na urządzeniu z systemem Azure Stack EDGE Pro GPU przy użyciu polecenia kubectl | Microsoft Docs
description: Zawiera opis sposobu tworzenia i zarządzania Kubernetes bezstanowej aplikacji przy użyciu polecenia kubectl na urządzeniu z systemem Microsoft Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: fd55ce702ae3c9485fc2dcc37fc90915a8990ce7
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "102638152"
---
# <a name="deploy-a-kubernetes-stateless-application-via-kubectl-on-your-azure-stack-edge-pro-gpu-device"></a>Wdrażanie bezstanowej aplikacji Kubernetes za pośrednictwem polecenia kubectl na urządzeniu z systemem Azure Stack EDGE Pro GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

W tym artykule opisano sposób wdrażania bezstanowej aplikacji przy użyciu poleceń polecenia kubectl w istniejącym klastrze Kubernetes. W tym artykule omówiono również proces tworzenia i konfigurowania zasobników w aplikacji bezstanowej.

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było utworzyć klaster Kubernetes i użyć `kubectl` narzędzia wiersza polecenia, należy upewnić się, że:

- Poświadczenia logowania są dostępne na urządzeniu z 1 węzłem Azure Stack Edge.

- Program Windows PowerShell 5,0 lub nowszy jest zainstalowany w systemie klienta systemu Windows w celu uzyskania dostępu do urządzenia Azure Stack EDGE Pro. Możesz również mieć dowolnego innego klienta z obsługiwanym systemem operacyjnym. W tym artykule opisano procedurę w przypadku korzystania z klienta systemu Windows. Aby pobrać najnowszą wersję programu Windows PowerShell, przejdź do temat [Instalowanie programu Windows PowerShell](/powershell/scripting/install/installing-windows-powershell).

- Obliczenia są włączane na urządzeniu Azure Stack EDGE Pro. Aby włączyć obliczenia, przejdź do strony **obliczenia** w lokalnym interfejsie użytkownika urządzenia. Następnie wybierz interfejs sieciowy, który chcesz włączyć dla obliczeń. Wybierz pozycję **Włącz**. Włączenie obliczeń powoduje utworzenie przełącznika wirtualnego na urządzeniu w tym interfejsie sieciowym. Aby uzyskać więcej informacji, zobacz [Włączanie usługi COMPUTE Network w Azure Stack Edge](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).

- Na urządzeniu z systemem Azure Stack Edge Kubernetes jest uruchomiony serwer klastra programu w wersji 2.1 lub nowszej. Aby uzyskać więcej informacji, zobacz [Tworzenie klastra Kubernetes i zarządzanie nim na urządzeniu Microsoft Azure Stack EDGE Pro](azure-stack-edge-gpu-create-kubernetes-cluster.md).

- Zainstalowano program `kubectl` .

## <a name="deploy-a-stateless-application"></a>Wdrażanie aplikacji bezstanowej

Przed rozpoczęciem należy:

1. Utworzono klaster Kubernetes.
2. Skonfiguruj przestrzeń nazw.
3. Skojarzono użytkownika z przestrzenią nazw.
4. Zapisano konfigurację użytkownika w programie `C:\Users\<username>\.kube` .
5. Zainstalowany `kubectl` .

Teraz można rozpocząć pracę i zarządzać wdrożeniami aplikacji bezstanowych na urządzeniu z systemem Azure Stack brzeg Pro. Przed rozpoczęciem korzystania z programu należy `kubectl` sprawdzić, czy jest dostępna poprawna wersja programu `kubectl` .

### <a name="verify-you-have-the-correct-version-of-kubectl-and-set-up-configuration"></a>Sprawdź, czy masz poprawną wersję programu polecenia kubectl i skonfiguruj konfigurację

Aby sprawdzić wersję programu `kubectl` :

1. Sprawdź, czy wersja programu `kubectl` jest większa lub równa 1,9:

   ```powershell
   kubectl version
   ```
    
   Poniżej przedstawiono przykład danych wyjściowych:
    
   ```powershell
   PS C:\WINDOWS\system32> C:\windows\system32\kubectl.exe version
   Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.2", GitCommit:"f6278300bebbb750328ac16ee6dd3aa7d3549568", GitTreeState:"clean", BuildDate:"2019-08-05T09:23:26Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"windows/amd64"}
   Server Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.1", GitCommit:"4485c6f18cee9a5d3c3b4e523bd27972b1b53892", GitTreeState:"clean", BuildDate:"2019-07-18T09:09:21Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"linux/amd64"}
   ```

   W takim przypadku wersja klienta polecenia kubectl jest 1.15.2 i jest zgodna, aby kontynuować.

2. Pobierz listę zasobników uruchomionych w klastrze Kubernetes. Jest to kontener aplikacji lub proces uruchomiony w klastrze Kubernetes.

   ```powershell
   kubectl get pods -n <namespace-string>
   ```
    
   Poniżej przedstawiono przykładowe użycie poleceń:
    
   ```powershell
   PS C:\WINDOWS\system32> kubectl get pods -n "test1"
   No resources found.
   PS C:\WINDOWS\system32>
   ```
    
   Dane wyjściowe powinny stwierdzać, że nie znaleziono żadnych zasobów (na podst.), ponieważ nie ma żadnych aplikacji uruchomionych w klastrze.

   Polecenie wypełni strukturę katalogów "C:\Users \\ &lt; username &gt; \\ . polecenia \" z plikami konfiguracyjnymi. Narzędzie wiersza polecenia polecenia kubectl będzie używać tych plików do tworzenia bezstanowych aplikacji i zarządzania nimi w klastrze Kubernetes.  

3. Ręcznie sprawdź strukturę katalogów "C:\Users \\ &lt; username &gt; \\ . polecenia, \" Aby upewnić się, że *polecenia kubectl* został wypełniony przy użyciu następujących podfolderów:

   ```powershell
   PS C:\Users\username> ls .kube
    
    
      Directory: C:\Users\user\.kube
    
   Mode                LastWriteTime         Length Name
   ----                -------------         ------ ----
   d-----         2/18/2020 11:05 AM                cache
   d-----         2/18/2020 11:04 AM                http-cache
   -a----         2/18/2020 10:41 AM           5377 config
   ```

> [!NOTE]
> Aby wyświetlić listę wszystkich poleceń polecenia kubectl, wpisz `kubectl --help` .

### <a name="create-a-stateless-application-using-a-deployment"></a>Tworzenie bezstanowej aplikacji przy użyciu wdrożenia

Teraz, gdy została sprawdzona, że wersja wiersza polecenia polecenia kubectl jest poprawna i masz wymagane pliki konfiguracji, można utworzyć wdrożenie aplikacji bezstanowej.

A pod jest podstawową jednostką wykonywania aplikacji Kubernetes, najmniejszą i najprostszą jednostką w modelu obiektu Kubernetes, który tworzysz lub wdrażasz. W obszarze jest również hermetyzowane zasoby magazynu, unikatowy adres IP sieci i opcje, które regulują sposób działania kontenerów.

Typ aplikacji bezstanowej, którą tworzysz, to wdrożenie serwera sieci Web Nginx.

Wszystkie polecenia polecenia kubectl służące do tworzenia i zarządzania wdrożeniami aplikacji bezstanowych muszą określać przestrzeń nazw skojarzoną z konfiguracją. Przestrzeń nazw została utworzona podczas połączenia z klastrem na urządzeniu z systemem Azure Stack EDGE Pro w [witrynie Tworzenie klastra Kubernetes i zarządzanie nim](azure-stack-edge-gpu-create-kubernetes-cluster.md) za pomocą programu Microsoft Azure Stack EDGE Pro `New-HcsKubernetesNamespace` .

Aby określić przestrzeń nazw w polecenia kubectl, użyj polecenia `kubectl <command> -n <namespace-string>` .

Wykonaj następujące kroki, aby utworzyć wdrożenie Nginx:

1. Zastosuj bezstanową aplikację przez utworzenie obiektu wdrożenia Kubernetes:

   ```powershell
   kubectl apply -f <yaml-file> -n <namespace-string>
   ```

   W tym przykładzie ścieżka do pliku YAML aplikacji jest źródłem zewnętrznym.

   Oto przykładowe użycie polecenia i jego danych wyjściowych:

   ```powershell
   PS C:\WINDOWS\system32> kubectl apply -f https://k8s.io/examples/application/deployment.yaml -n "test1"
    
   deployment.apps/nginx-deployment created
   ```

   Alternatywnie można zapisać niższą wartość promocji na komputerze lokalnym i zastąpić ścieżkę i nazwę pliku w parametrze *-f* . Na przykład "C:\Kubernetes\deployment.yaml". Konfiguracja wdrożenia aplikacji będzie:

   ```markdown
   apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
   kind: Deployment
   metadata:
     name: nginx-deployment
   spec:
     selector:
       matchLabels:
         app: nginx
     replicas: 2 # tells deployment to run 2 pods matching the template
     template:
       metadata:
         labels:
           app: nginx
       spec:
         containers:
         - name: nginx
           image: nginx:1.7.9
           ports:
           - containerPort: 80
   ```

   To polecenie tworzy domyślne wdrożenie Nginx, które ma dwa zasobniki do uruchomienia aplikacji.

2. Pobierz opis utworzonego wdrożenia Kubernetes Nginx:

   ```powershell
   kubectl describe deployment nginx-deployment -n <namespace-string>
   ```

   Poniżej przedstawiono przykładowe użycie polecenia z danymi wyjściowymi:
    
   ```powershell
   PS C:\Users\user> kubectl describe deployment nginx-deployment -n "test1"
    
   Name:                   nginx-deployment
   Namespace:              test1
   CreationTimestamp:      Tue, 18 Feb 2020 13:35:29 -0800
   Labels:                 <none>
   Annotations:            deployment.kubernetes.io/revision: 1
                           kubectl.kubernetes.io/last-applied-configuration:
                             {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"nginx-deployment","namespace":"test1"},"spec":{"repl...
   Selector:               app=nginx
   Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
   StrategyType:           RollingUpdate
   MinReadySeconds:        0
   RollingUpdateStrategy:  25% max unavailable, 25% max surge
   Pod Template:
      Labels:  app=nginx
      Containers:
       nginx:
        Image:        nginx:1.7.9
        Port:         80/TCP
        Host Port:    0/TCP
        Environment:  <none>
        Mounts:       <none>
      Volumes:        <none>
   Conditions:
      Type           Status  Reason
      ----           ------  ------
      Available      True    MinimumReplicasAvailable
      Progressing    True    NewReplicaSetAvailable
   OldReplicaSets:  <none>
   NewReplicaSet:   nginx-deployment-5754944d6c (2/2 replicas created)
   Events:
     Type    Reason             Age    From                   Message
     ----    ------             ----   ----                   -------
     Normal  ScalingReplicaSet  2m22s  deployment-controller  Scaled up replica set nginx-deployment-5754944d6c to 2
   ```

   W przypadku ustawienia *repliki* zobaczysz:
    
   ```powershell
   Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
   ```

   Ustawienie *repliki* wskazuje, że Specyfikacja wdrożenia wymaga dwóch zasobników i że te zasobniki zostały utworzone i zaktualizowane i są gotowe do użycia.

   > [!NOTE]
   > Zestaw replik zastępuje te, które zostały usunięte z jakiegokolwiek powodu, na przykład w przypadku awarii węzła urządzenia lub nieprzerwanego uaktualnienia urządzenia. Z tego powodu zalecamy użycie zestawu replik, nawet jeśli aplikacja wymaga tylko jednego elementu.

3. Aby wyświetlić listę zasobników we wdrożeniu:

   ```powershell
   kubectl get pods -l app=nginx -n <namespace-string>
   ```
    
   Poniżej przedstawiono przykładowe użycie polecenia z danymi wyjściowymi:
    
   ```powershell
   PS C:\Users\user> kubectl get pods -l app=nginx -n "test1"
    
   NAME                                READY   STATUS    RESTARTS   AGE
   nginx-deployment-5754944d6c-7wqjd   1/1     Running   0          3m13s
   nginx-deployment-5754944d6c-nfj2h   1/1     Running   0          3m13s
   ```

   Dane wyjściowe sprawdzają, czy mamy dwa zasobniki z unikatowymi nazwami, do których można się odwoływać za pomocą polecenia kubectl.

4. Aby wyświetlić informacje dotyczące poszczególnych osób w danym wdrożeniu:

   ```powershell
   kubectl describe pod <podname-string> -n <namespace-string>
   ```

  Poniżej przedstawiono przykładowe użycie polecenia z danymi wyjściowymi:

   ```powershell
   PS C:\Users\user> kubectl describe pod "nginx-deployment-5754944d6c-7wqjd" -n "test1"

   Name:           nginx-deployment-5754944d6c-7wqjd
   Namespace:      test1
   Priority:       0
   Node:           k8s-1d9qhq2cl-n1/10.128.46.184
   Start Time:     Tue, 18 Feb 2020 13:35:29 -0800
   Labels:         app=nginx
                   pod-template-hash=5754944d6c
   Annotations:    <none>
   Status:         Running
   IP:             172.17.246.200
   Controlled By:  ReplicaSet/nginx-deployment-5754944d6c
    Containers:
      nginx:
        Container ID:   docker://280b0f76bfdc14cde481dc4f2b8180cf5fbfc90a084042f679d499f863c66979
        Image:          nginx:1.7.9
        Image ID:       docker-pullable://nginx@sha256:e3456c851a152494c3e4ff5fcc26f240206abac0c9d794affb40e0714846c451
        Port:           80/TCP
        Host Port:      0/TCP
        State:          Running
          Started:      Tue, 18 Feb 2020 13:35:35 -0800
        Ready:          True
        Restart Count:  0
        Environment:    <none>
        Mounts:
          /var/run/secrets/kubernetes.io/serviceaccount from default-token-8gksw (ro)
    Conditions:
      Type              Status
      Initialized       True
      Ready             True
      ContainersReady   True
      PodScheduled      True
    Volumes:
      default-token-8gksw:
        Type:        Secret (a volume populated by a Secret)
        SecretName:  default-token-8gksw
        Optional:    false
    QoS Class:       BestEffort
    Node-Selectors:  <none>
    Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                     node.kubernetes.io/unreachable:NoExecute for 300s
    Events:
      Type    Reason     Age    From                       Message
      ----    ------     ----   ----                       -------
      Normal  Scheduled  4m58s  default-scheduler          Successfully assigned test1/nginx-deployment-5754944d6c-7wqjd to k8s-1d9qhq2cl-n1
      Normal  Pulling    4m57s  kubelet, k8s-1d9qhq2cl-n1  Pulling image "nginx:1.7.9"
      Normal  Pulled     4m52s  kubelet, k8s-1d9qhq2cl-n1  Successfully pulled image "nginx:1.7.9"
      Normal  Created    4m52s  kubelet, k8s-1d9qhq2cl-n1  Created container nginx
      Normal  Started    4m52s  kubelet, k8s-1d9qhq2cl-n1  Started container nginx
   ```

### <a name="rescale-the-application-deployment-by-increasing-the-replica-count"></a>Ponowne skalowanie wdrożenia aplikacji przez zwiększenie liczby replik

Każdy element pod jest przeznaczony do uruchamiania pojedynczego wystąpienia danej aplikacji. Jeśli chcesz skalować aplikację w poziomie w celu uruchomienia wielu wystąpień, możesz zwiększyć liczbę zasobników do jednego dla każdego wystąpienia. W Kubernetes jest to nazywane replikacją.
Można zwiększyć liczbę zasobników we wdrożeniu aplikacji, stosując nowy plik YAML. Plik YAML zmienia ustawienia replik na 4, co zwiększa liczbę zasobników we wdrożeniu do czterech zasobników. Aby zwiększyć liczbę numerów z 2 do 4:

```powershell
PS C:\WINDOWS\system32> kubectl apply -f https://k8s.io/examples/application/deployment-scale.yaml -n "test1"
```

Alternatywnie można zapisać niższą wartość promocji na komputerze lokalnym i zastąpić ścieżkę i nazwę pliku dla parametru *-f* `kubectl apply` . Na przykład "C:\Kubernetes\deployment-scale.yaml". Konfiguracją skalowania wdrożenia aplikacji będą:

```markdown
apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 4 # Update the replicas from 2 to 4
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.8
        ports:
        - containerPort: 80
```

Aby sprawdzić, czy wdrożenie ma cztery zasobniki:

```powershell
kubectl get pods -l app=nginx
```

Poniżej przedstawiono przykładowe dane wyjściowe dla wdrożenia ponownego skalowania z dwóch do czterech zasobników:

```powershell
PS C:\WINDOWS\system32> kubectl get pods -l app=nginx

NAME                               READY     STATUS    RESTARTS   AGE
nginx-deployment-148880595-4zdqq   1/1       Running   0          25s
nginx-deployment-148880595-6zgi1   1/1       Running   0          25s
nginx-deployment-148880595-fxcez   1/1       Running   0          2m
nginx-deployment-148880595-rwovn   1/1       Running   0          2m
```

Jak widać na podstawie danych wyjściowych, masz teraz cztery zasobniki w ramach wdrożenia, które mogą uruchamiać aplikację.

### <a name="delete-a-deployment"></a>Usuwanie wdrożenia

Aby usunąć wdrożenie, w tym wszystkie zasobniki, należy uruchomić polecenie, `kubectl delete deployment` określając nazwę wdrożenia *Nginx-Deployment* i nazwę przestrzeni nazw. Aby usunąć wdrożenie:

   ```powershell
   kubectl delete deployment nginx-deployment -n <namespace-string>
   ```

Poniżej przedstawiono przykładowe użycie poleceń z danymi wyjściowymi:

```powershell
PS C:\Users\user> kubectl delete deployment nginx-deployment -n "test1"
deployment.extensions "nginx-deployment" deleted
```

## <a name="next-steps"></a>Następne kroki

[Kubernetes — Omówienie](azure-stack-edge-gpu-kubernetes-overview.md)