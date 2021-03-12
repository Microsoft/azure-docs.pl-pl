---
title: Użyj polecenia kubectl do wdrożenia aplikacji stanowej Kubernetes za pośrednictwem udziału z alokacją statyczną na urządzeniu Azure Stack EDGE Pro | Microsoft Docs
description: Zawiera opis sposobu tworzenia i zarządzania wdrożeniem aplikacji stanowych Kubernetes za pomocą udziału statycznego, który jest używany przez polecenia kubectl na urządzeniu GPU z systemem Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/09/2021
ms.author: alkohli
ms.openlocfilehash: 01ba8e1f22deb376fd461be24d3f66f0a7f5e1ae
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102610488"
---
# <a name="use-kubectl-to-run-a-kubernetes-stateful-application-with-a-persistentvolume-on-your-azure-stack-edge-pro-device"></a>Użyj polecenia kubectl, aby uruchomić aplikację stanową Kubernetes przy użyciu PersistentVolume na urządzeniu Azure Stack EDGE Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

W tym artykule pokazano, jak wdrożyć aplikację stanową pojedynczego wystąpienia w programie Kubernetes przy użyciu PersistentVolume (PV) i wdrożenia. Wdrożenie używa `kubectl` poleceń w istniejącym klastrze Kubernetes i wdraża aplikację MySQL. 

Ta procedura jest przeznaczona dla osób, które sprawdziły [Magazyn Kubernetes na urządzeniu Azure Stack EDGE Pro](azure-stack-edge-gpu-kubernetes-storage.md) i znają koncepcje [magazynu Kubernetes](https://kubernetes.io/docs/concepts/storage/). 

Azure Stack EDGE Pro obsługuje również uruchamianie kontenerów usługi Azure SQL Edge i można je wdrożyć w podobny sposób, jak w przypadku programu MySQL. Aby uzyskać więcej informacji, zobacz [Azure SQL Edge](../azure-sql-edge/overview.md).


## <a name="prerequisites"></a>Wymagania wstępne

Przed wdrożeniem aplikacji stanowej wykonaj następujące wymagania wstępne na urządzeniu oraz klienta, który będzie używany do uzyskiwania dostępu do urządzenia:

### <a name="for-device"></a>Na potrzeby urządzenia

- Poświadczenia logowania są dostępne na urządzeniu z 1 węzłem Azure Stack Edge.
    - Urządzenie zostało aktywowane. Zobacz [Aktywowanie urządzenia](azure-stack-edge-gpu-deploy-activate.md).
    - Urządzenie ma rolę obliczeniową skonfigurowaną za pośrednictwem Azure Portal i ma klaster Kubernetes. Zobacz [Konfigurowanie obliczeń](azure-stack-edge-gpu-deploy-configure-compute.md).

### <a name="for-client-accessing-the-device"></a>Do uzyskiwania dostępu do urządzenia przez klienta

- Masz system klienta systemu Windows, który będzie używany do uzyskiwania dostępu do urządzenia z systemem Azure Stack Edge.
    - Klient korzysta z programu Windows PowerShell 5,0 lub nowszego. Aby pobrać najnowszą wersję programu Windows PowerShell, przejdź do obszaru [Instalowanie programu Windows PowerShell](/powershell/scripting/install/installing-windows-powershell).
    
    - Możesz również mieć dowolnego innego klienta z [obsługiwanym systemem operacyjnym](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) . W tym artykule opisano procedurę w przypadku korzystania z klienta systemu Windows. 
    
    - Procedura opisana w artykule [Uzyskiwanie dostępu do klastra Kubernetes na urządzeniu z systemem Azure Stack Edge w systemie](azure-stack-edge-gpu-create-kubernetes-cluster.md). Masz:
      - Utworzono `userns1` przestrzeń nazw za pomocą `New-HcsKubernetesNamespace` polecenia. 
      - Utworzono użytkownika `user1` za pomocą `New-HcsKubernetesUser` polecenia. 
      - Udzielono `user1` dostępu `userns1` za pośrednictwem `Grant-HcsKubernetesNamespaceAccess` polecenia.       
      - Zainstalowany `kubectl` na kliencie i zapisany `kubeconfig` plik z konfiguracją użytkownika w języku C: \\ Users \\ &lt; &gt; \\ . polecenia. 
    
    - Upewnij się, że `kubectl` wersja klienta jest skośna nie więcej niż jedna wersja z wersji głównej Kubernetes działającej na urządzeniu Azure Stack EDGE Pro. 
        - Użyj, `kubectl version` Aby sprawdzić wersję polecenia kubectl działającą na kliencie. Zanotuj pełną wersję.
        - W lokalnym interfejsie użytkownika urządzenia z usługą Azure Stack Edge, przejdź do **omówienia** i zanotuj numer oprogramowania Kubernetes. 
        - Sprawdź te dwie wersje pod kątem zgodności z mapowania podanego w obsługiwanej wersji Kubernetes.<!-- insert link--> 


Możesz przystąpić do wdrażania aplikacji stanowej na urządzeniu, na którym znajduje się Azure Stack Edge. 

## <a name="provision-a-static-pv"></a>Udostępnianie statycznego PV

Aby statycznie zainicjować obsługę PV, należy utworzyć udział na urządzeniu. Wykonaj następujące kroki, aby zainicjować obsługę PV w udziale SMB. 

> [!NOTE]
> - Konkretny przykład użyty w tym artykule zawierającym instrukcje nie działa z udziałami NFS. Ogólnie rzecz biorąc, udziały NFS mogą być obsługiwane na urządzeniu z systemem Azure Stack Edge przy użyciu aplikacji innych niż bazy danych.
> - Aby wdrożyć aplikacje stanowe korzystające z woluminów magazynu w celu zapewnienia trwałego magazynu, zalecamy korzystanie z programu `StatefulSet` . Ten przykład używa `Deployment` tylko z jedną repliką i jest odpowiedni do tworzenia i testowania. 

1. Wybierz, czy chcesz utworzyć udział graniczny lub udział lokalny krawędzi. Postępuj zgodnie z instrukcjami w temacie [Dodawanie udziału](azure-stack-edge-manage-shares.md#add-a-share) , aby utworzyć udział. Pamiętaj, aby zaznaczyć pole wyboru **Użyj udziału z obliczeniem krawędzi**.

    ![Udział lokalny na potrzeby PV](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/edge-local-share-static-provision-1.png)

    1. Zamiast tworzyć nowy udział, jeśli zdecydujesz się użyć istniejącego udziału, należy zainstalować udział.
    
        W Azure Portal dla zasobu Azure Stack Edge przejdź do pozycji **udziały**. Z istniejącej listy udziałów wybierz i kliknij udział, którego chcesz użyć.

        ![Wybierz istniejący udział lokalny do PV](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/mount-edge-share-1.png)

    1. Wybierz pozycję **Zainstaluj** i Potwierdź zainstalowanie po wyświetleniu monitu.  

        ![Zainstaluj istniejący udział lokalny na potrzeby funkcji PV](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/mount-edge-share-2.png)

1. Zanotuj nazwę udziału. Po utworzeniu tego udziału tworzony jest automatycznie obiekt woluminu trwałego w klastrze Kubernetes odpowiadającym utworzonemu udziałowi SMB. 

## <a name="deploy-mysql"></a>Wdrażanie bazy danych MySQL

Teraz uruchomisz aplikację stanową, tworząc wdrożenie Kubernetes i łącząc ją z wa utworzoną w poprzednim kroku przy użyciu PersistentVolumeClaim (PVC). 

Wszystkie `kubectl` polecenia używane do tworzenia i zarządzania wdrożeniami aplikacji stanowych muszą określać przestrzeń nazw skojarzoną z konfiguracją. Aby określić przestrzeń nazw w polecenia kubectl, użyj polecenia `kubectl <command> -n <your-namespace>` .

1. Pobierz listę zasobników uruchomionych w klastrze Kubernetes w przestrzeni nazw. Jest to kontener aplikacji lub proces uruchomiony w klastrze Kubernetes.

   ```powershell
   kubectl get pods -n <your-namespace>
   ```
    
   Oto przykład użycia polecenia:
    
   ```powershell
    C:\Users\user>kubectl get pods -n "userns1"
    No resources found in userns1 namespace.    
    C:\Users\user>
   ```
    
   Dane wyjściowe powinny stwierdzać, że nie znaleziono żadnych zasobów (na podst.), ponieważ nie ma żadnych aplikacji uruchomionych w klastrze.

1. Będziesz używać następujących plików YAML. `mysql-deployment.yml`Plik zawiera opis wdrożenia z programem MySQL i odwołuje się do obwodu PVC. Plik definiuje instalację woluminu dla programu `/var/lib/mysql` , a następnie tworzy obwód PVC, który szuka woluminu o wielkości 20 GB. 

    To zgłoszenie jest spełnione przez wszystkie istniejące metody PV, które były statycznie udostępniane podczas tworzenia udziału we wcześniejszym kroku. Na urządzeniu jest tworzony duży WB o 32 TB dla każdego udziału. WB spełnia wymagania określone przez obwód PVC, a obwód PVC powinien być powiązany z tym PV.

    Skopiuj i Zapisz następujący `mysql-deployment.yml` plik do folderu na kliencie systemu Windows, który jest używany do uzyskania dostępu do urządzenia Azure Stack EDGE Pro.
    
    ```yml
    apiVersion: v1
    kind: Service
    metadata:
      name: mysql
    spec:
      ports:
      - port: 3306
      selector:
        app: mysql
      clusterIP: None
    ---
    apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
    kind: Deployment
    metadata:
      name: mysql
    spec:
      selector:
        matchLabels:
          app: mysql
      strategy:
        type: Recreate
      template:
        metadata:
          labels:
            app: mysql
        spec:
          containers:
          - image: mysql:5.6
            name: mysql
            env:
              # Use secret in real usage
            - name: MYSQL_ROOT_PASSWORD
              value: password
            ports:
            - containerPort: 3306
              name: mysql
            volumeMounts:
            - name: mysql-persistent-storage
              mountPath: /var/lib/mysql
          volumes:
          - name: mysql-persistent-storage
            persistentVolumeClaim:
              claimName: mysql-pv-claim
    ```
    
2. Skopiuj i Zapisz jako `mysql-pv.yml` plik w tym samym folderze, w którym został zapisany `mysql-deployment.yml` . Aby użyć udziału SMB utworzonego wcześniej w programie `kubectl` , należy ustawić `volumeName` pole w obiekcie PVC na nazwę udziału. 

    > [!NOTE] 
    > Upewnij się, że pliki YAML mają poprawne wcięcia. Możesz sprawdzić za pomocą [YAML lint](http://www.yamllint.com/) , aby sprawdzić poprawność, a następnie zapisać.
   
    ```yml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: mysql-pv-claim
    spec:
      volumeName: <smb-share-name-here>
      storageClassName: ""
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 20Gi
    ```

3. Wdróż `mysql-pv.yaml` plik.

    `kubectl apply -f <URI path to the mysql-pv.yml file> -n <your-user-namespace>`
    
    Oto przykładowe dane wyjściowe wdrożenia.

    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-pv.yml" -n userns1
    persistentvolumeclaim/mysql-pv-claim created
    
    C:\Users\user>
    ```
   Zanotuj nazwę utworzonego obwodu PVC. Zostanie ona użyta w późniejszym kroku. 

4. Wdróż zawartość `mysql-deployment.yml` pliku.

    `kubectl apply -f <URI path to mysql-deployment.yml file> -n <your-user-namespace>`

    Oto przykładowe dane wyjściowe wdrożenia.
    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-deployment.yml" -n userns1
        service/mysql created
        deployment.apps/mysql created
    ```
    
5. Wyświetl informacje o wdrożeniu.

    `kubectl describe deployment <app-label> -n <your-user-namespace>`
    
    ```powershell
    C:\Users\user>kubectl describe deployment mysql -n userns1
    Name:               mysql
    Namespace:          userns1
    CreationTimestamp:  Tue, 18 Aug 2020 09:44:58 -0700
    Labels:             <none>
    Annotations:        deployment.kubernetes.io/revision: 1
                        kubectl.kubernetes.io/last-applied-configuration:
                          {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"mysql","namespace":"userns1"},"spec":{"selector":{"matchL...
    Selector:           app=mysql
    Replicas:           1 desired | 1 updated | 1 total | 1 available | 0 unavailable
    StrategyType:       Recreate
    MinReadySeconds:    0
    Pod Template:
      Labels:  app=mysql
      Containers:
       mysql:
        Image:      mysql:5.6
        Port:       3306/TCP
        Host Port:  0/TCP
        Environment:
          MYSQL_ROOT_PASSWORD:  password
        Mounts:
          /var/lib/mysql from mysql-persistent-storage (rw)
      Volumes:
       mysql-persistent-storage:
        Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
        ClaimName:  mysql-pv-claim
        ReadOnly:   false
    Conditions:
      Type           Status  Reason
      ----           ------  ------
      Progressing    True    NewReplicaSetAvailable
      Available      True    MinimumReplicasAvailable
    OldReplicaSets:  <none>
    NewReplicaSet:   mysql-c85f7f79c (1/1 replicas created)
    Events:
      Type    Reason             Age   From                   Message
      ----    ------             ----  ----                   -------
      Normal  ScalingReplicaSet  10m   deployment-controller  Scaled up replica set mysql-c85f7f79c to 1
    
    C:\Users\user>
    ```
    

6. Utwórz listę zasobników utworzonych przez wdrożenie.

    `kubectl get pods -l <app=label> -n <your-user-namespace>`

    Oto przykładowe dane wyjściowe.

    
    ```powershell
    C:\Users\user>kubectl get pods -l app=mysql -n userns1
    NAME                    READY   STATUS    RESTARTS   AGE
    mysql-c85f7f79c-vzz7j   1/1     Running   1          14m
    
    C:\Users\user>
    ```
    
7. Sprawdź PersistentVolumeClaim.

    `kubectl describe pvc <your-pvc-name>`

    Oto przykładowe dane wyjściowe.

    
    ```powershell
    C:\Users\user>kubectl describe pvc mysql-pv-claim -n userns1
    Name:          mysql-pv-claim
    Namespace:     userns1
    StorageClass:
    Status:        Bound
    Volume:        mylocalsmbshare1
    Labels:        <none>
    Annotations:   kubectl.kubernetes.io/last-applied-configuration:
                     {"apiVersion":"v1","kind":"PersistentVolumeClaim","metadata":{"annotations":{},"name":"mysql-pv-claim","namespace":"userns1"},"spec":{"acc...
                   pv.kubernetes.io/bind-completed: yes
    Finalizers:    [kubernetes.io/pvc-protection]
    Capacity:      32Ti
    Access Modes:  RWO,RWX
    VolumeMode:    Filesystem
    Mounted By:    mysql-c85f7f79c-vzz7j
    Events:        <none>
    
    C:\Users\user>
    ```
    

## <a name="verify-mysql-is-running"></a>Sprawdź, czy program MySQL jest uruchomiony


Aby uruchomić polecenie względem kontenera w obszarze, w którym działa baza danych MySQL, wpisz:

`kubectl exec <your-pod-with-the-app> -i -t -n <your-namespace> -- mysql`

Oto przykładowe dane wyjściowe.

```powershell
C:\Users\user>kubectl exec mysql-c85f7f79c-vzz7j -i -t -n userns1 -- mysql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 1
Server version: 5.6.49 MySQL Community Server (GPL)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

## <a name="delete-a-deployment"></a>Usuwanie wdrożenia

Aby usunąć wdrożenie, Usuń wdrożone obiekty według nazwy. Do tych obiektów należą wdrażanie, usługa i obwód PVC.
 
```powershell
kubectl delete deployment <deployment-name>,svc <service-name> -n <your-namespace>
kubectl delete pvc <your-pvc-name> -n <your-namespace>
```

Oto przykładowe dane wyjściowe po usunięciu wdrożenia i usługi.

```powershell
C:\Users\user>kubectl delete deployment,svc mysql -n userns1
deployment.apps "mysql" deleted
service "mysql" deleted
C:\Users\user>
```
Oto przykładowe dane wyjściowe po usunięciu obwodu PVC.

```powershell
C:\Users\user>kubectl delete pvc mysql-pv-claim -n userns1
persistentvolumeclaim "mysql-pv-claim" deleted
C:\Users\user>
```

Funkcja PV nie jest już powiązana z obwodem PVC w przypadku usunięcia obwodu PVC. Ponieważ funkcja PV została zainicjowana podczas tworzenia udziału, należy usunąć udział. Wykonaj następujące kroki:

1. Odinstaluj udział. W Azure Portal przejdź do **Azure Stack zasobów brzegowych > udziały** i wybierz i kliknij udział, który chcesz odinstalować. Wybierz pozycję **Odinstaluj** i potwierdź operację. Poczekaj na odinstalowanie udziału. Dezinstalacja zwalnia udział (i w związku z tym skojarzone PersistentVolume) z klastra Kubernetes. 

    ![Odinstalowanie udziału lokalnego na potrzeby funkcji PV](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/unmount-edge-local-share-1.png)

1. Teraz możesz wybrać pozycję **Usuń** i potwierdzić usunięcie udziału. Należy również usunąć udział i odpowiednie WB.

    ![Usuń udział lokalny dla PV](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/delete-edge-local-share-1.png)


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak dynamicznie zainicjować obsługę magazynu, zobacz [wdrażanie aplikacji stanowej za pośrednictwem dynamicznego inicjowania obsługi na urządzeniu z systemem Azure Stack brzeg Pro](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md) .
