---
title: Użyj polecenia kubectl do wdrożenia aplikacji stanowej Kubernetes za pośrednictwem dynamicznego udostępniania udziałów na urządzeniu z systemem Azure Stack EDGE Pro GPU | Microsoft Docs
description: Zawiera opis sposobu tworzenia i zarządzania wdrożeniem aplikacji stanowych Kubernetes za pośrednictwem dynamicznego udostępniania przy użyciu usługi polecenia kubectl na urządzeniu GPU z systemem Microsoft Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/26/2020
ms.author: alkohli
ms.openlocfilehash: d37152f7dec78d5f5db21fdde9a8ec25c36c4e05
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90899480"
---
# <a name="use-kubectl-to-run-a-kubernetes-stateful-application-with-storageclass-on-your-azure-stack-edge-pro-gpu-device"></a>Użyj polecenia kubectl, aby uruchomić aplikację stanową Kubernetes z StorageClass na urządzeniu z systemem Azure Stack Edge

W tym artykule pokazano, jak wdrożyć aplikację stanową pojedynczego wystąpienia w programie Kubernetes przy użyciu StorageClass do dynamicznego aprowizacji magazynu i wdrożenia. Wdrożenie używa `kubectl` poleceń w istniejącym klastrze Kubernetes i wdraża aplikację MySQL. 

Ta procedura jest przeznaczona dla osób, które sprawdziły [Magazyn Kubernetes na urządzeniu Azure Stack EDGE Pro](azure-stack-edge-gpu-kubernetes-storage.md) i znają koncepcje [magazynu Kubernetes](https://kubernetes.io/docs/concepts/storage/).


## <a name="prerequisites"></a>Wymagania wstępne

Przed wdrożeniem aplikacji stanowej upewnij się, że zostały spełnione następujące wymagania wstępne na urządzeniu oraz klient, który będzie używany do uzyskiwania dostępu do urządzenia:

### <a name="for-device"></a>Na potrzeby urządzenia

- Poświadczenia logowania są dostępne na urządzeniu z 1 węzłem Azure Stack Edge.
    - Urządzenie zostało aktywowane. Zobacz [Aktywowanie urządzenia](azure-stack-edge-gpu-deploy-activate.md).
    - Urządzenie ma rolę obliczeniową skonfigurowaną za pośrednictwem Azure Portal i ma klaster Kubernetes. Zobacz [Konfigurowanie obliczeń](azure-stack-edge-gpu-deploy-configure-compute.md).

### <a name="for-client-accessing-the-device"></a>Do uzyskiwania dostępu do urządzenia przez klienta

- Masz system klienta systemu Windows, który będzie używany do uzyskiwania dostępu do urządzenia z systemem Azure Stack Edge.
    - Klient korzysta z programu Windows PowerShell 5,0 lub nowszego. Aby pobrać najnowszą wersję programu Windows PowerShell, przejdź do obszaru [Instalowanie programu Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-7).
    
    - Możesz również mieć dowolnego innego klienta z [obsługiwanym systemem operacyjnym](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) . W tym artykule opisano procedurę w przypadku korzystania z klienta systemu Windows. 
    
    - Procedura opisana w artykule [Uzyskiwanie dostępu do klastra Kubernetes na urządzeniu z systemem Azure Stack Edge w systemie](azure-stack-edge-gpu-create-kubernetes-cluster.md). Masz:
      - Utworzono `userns1` przestrzeń nazw za pomocą `New-HcsKubernetesNamespace` polecenia. 
      - Utworzono użytkownika `user1` za pomocą `New-HcsKubernetesUser` polecenia. 
      - Udzielono `user1` dostępu `userns1` za pośrednictwem `Grant-HcsKubernetesNamespaceAccess` polecenia.       
      - Zainstalowany `kubectl` na kliencie i zapisany `kubeconfig` plik z konfiguracją użytkownika w języku C: \\ Users \\ &lt; &gt; \\ . polecenia. 
    
    - Upewnij się, że `kubectl` wersja klienta jest skośna nie więcej niż jedna wersja z wersji głównej Kubernetes działającej na urządzeniu Azure Stack EDGE Pro. 
        - Użyj, `kubectl version` Aby sprawdzić wersję polecenia kubectl działającą na kliencie. Zanotuj pełną wersję.
        - W lokalnym interfejsie użytkownika urządzenia z usługą Azure Stack Edge, przejdź do **omówienia** i zanotuj numer oprogramowania Kubernetes. 
        - Sprawdź te dwie wersje pod kątem zgodności z mapowania podanego w obsługiwanej wersji Kubernetes<!-- insert link-->. 


Możesz przystąpić do wdrażania aplikacji stanowej na urządzeniu, na którym znajduje się Azure Stack Edge. 


## <a name="deploy-mysql"></a>Wdrażanie bazy danych MySQL

Teraz uruchomisz aplikację stanową, tworząc wdrożenie Kubernetes i łącząc go z wbudowaną StorageClass przy użyciu PersistentVolumeClaim (PVC). 

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

1. Będziesz używać następujących plików YAML. `mysql-deployment.yml`Plik zawiera opis wdrożenia z programem MySQL i odwołuje się do obwodu PVC. Plik definiuje instalację woluminu dla programu `/var/lib/mysql` , a następnie tworzy obwód PVC, który szuka woluminu o wielkości 20 GB. Dynamiczna funkcja PV jest inicjowana, a obwód PVC jest powiązany z tym PV.

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
              claimName: mysql-pv-claim-sc
    ```
    
2. Skopiuj i Zapisz jako `mysql-pvc.yml` plik w tym samym folderze, w którym został zapisany `mysql-deployment.yml` . Aby użyć wbudowanej StorageClass, która Azure Stack na podłączonym dysku z danymi, należy ustawić `storageClassName` pole w obiekcie PVC na `ase-node-local` i accessModes powinno być `ReadWriteOnce` . 

    > [!NOTE] 
    > Upewnij się, że pliki YAML mają poprawne wcięcia. Możesz sprawdzić za pomocą [YAML lint](http://www.yamllint.com/) , aby sprawdzić poprawność, a następnie zapisać.
   
    ```yml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: mysql-pv-claim-sc
    spec:
      storageClassName: ase-node-local
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 20Gi
    ```

3. Wdróż `mysql-pvc.yaml` plik.

    `kubectl apply -f <URI path to the mysql-pv.yml file> -n <your-user-namespace>`
    
    Oto przykładowe dane wyjściowe wdrożenia.

    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-pvc.yml" -n userns1
    persistentvolumeclaim/mysql-pv-claim-sc created
    C:\Users\user>
    ```
   Zanotuj nazwę utworzonego obwodu PVC `mysql-pv-claim-sc` . Zostanie ona użyta w późniejszym kroku. 

4. Wdróż zawartość `mysql-deployment.yml` pliku.

    `kubectl apply -f <URI path to mysql-deployment.yml file> -n <your-user-namespace>`

    Oto przykładowe dane wyjściowe wdrożenia.
    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-deployment.yml" -n userns1
    service/mysql created
    deployment.apps/mysql created
    C:\Users\user>
    ```
    
5. Wyświetl informacje o wdrożeniu.

    `kubectl describe deployment <app-label> -n <your-user-namespace>`
    
    ```powershell
    C:\Users\user>kubectl describe deployment mysql -n userns1
    Name:               mysql
    Namespace:          userns1
    CreationTimestamp:  Thu, 20 Aug 2020 11:14:25 -0700
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
        ClaimName:  mysql-pv-claim-sc
        ReadOnly:   false
    Conditions:
      Type           Status  Reason
      ----           ------  ------
      Available      True    MinimumReplicasAvailable
      Progressing    True    NewReplicaSetAvailable
    OldReplicaSets:  <none>
    NewReplicaSet:   mysql-695c4d9dcd (1/1 replicas created)
    Events:
      Type    Reason             Age   From                   Message
      ----    ------             ----  ----                   -------
      Normal  ScalingReplicaSet  24s   deployment-controller  Scaled up replica set mysql-695c4d9dcd to 1
    C:\Users\user>
    ```
    

6. Utwórz listę zasobników utworzonych przez wdrożenie.

    `kubectl get pods -l <app=label> -n <your-user-namespace>`

    Oto przykładowe dane wyjściowe.

    
    ```powershell
    C:\Users\user>kubectl get pods -l app=mysql -n userns1
    NAME                     READY   STATUS    RESTARTS   AGE
    mysql-695c4d9dcd-rvzff   1/1     Running   0          40s
    C:\Users\user>
    ```
    
7. Sprawdź PersistentVolumeClaim.

    `kubectl describe pvc <your-pvc-name>`

    Oto przykładowe dane wyjściowe.

    
    ```powershell
    C:\Users\user>kubectl describe pvc mysql-pv-claim-sc -n userns1
    Name:          mysql-pv-claim-sc
    Namespace:     userns1
    StorageClass:  ase-node-local
    Status:        Bound
    Volume:        pvc-dc48253c-82dc-42a4-a7c6-aaddc97c9b8a
    Labels:        <none>
    Annotations:   kubectl.kubernetes.io/last-applied-configuration:
                     {"apiVersion":"v1","kind":"PersistentVolumeClaim","metadata":{"annotations":{},"name":"mysql-pv-claim-sc","namespace":"userns1"},"spec":{"...
                   pv.kubernetes.io/bind-completed: yes
                   pv.kubernetes.io/bound-by-controller: yes
                   volume.beta.kubernetes.io/storage-provisioner: rancher.io/local-path
                   volume.kubernetes.io/selected-node: k8s-3q7lhq2cl-3q7lhq2
    Finalizers:    [kubernetes.io/pvc-protection]
    Capacity:      20Gi
    Access Modes:  RWO
    VolumeMode:    Filesystem
    Mounted By:    mysql-695c4d9dcd-rvzff
    Events:
      Type    Reason                 Age                From                                                                                                Message
      ----    ------                 ----               ----                                                                                                -------
      Normal  WaitForFirstConsumer   71s (x2 over 77s)  persistentvolume-controller                                                                         waiting for first consumer to be created before binding
      Normal  ExternalProvisioning   62s                persistentvolume-controller                                                                         waiting for a volume to be created, either by external provisioner "rancher.io/local-path" or manually created by system administrator
      Normal  Provisioning           62s                rancher.io/local-path_local-path-provisioner-6b84988bf9-tx8mz_1896d824-f862-4cbf-912a-c8cc0ca05574  External provisioner is provisioning volume for claim "userns1/mysql-pv-claim-sc"
      Normal  ProvisioningSucceeded  60s                rancher.io/local-path_local-path-provisioner-6b84988bf9-tx8mz_1896d824-f862-4cbf-912a-c8cc0ca05574  Successfully provisioned volume pvc-dc48253c-82dc-42a4-a7c6-aaddc97c9b8a
    C:\Users\user>
    ```
    

## <a name="verify-mysql-is-running"></a>Sprawdź, czy program MySQL jest uruchomiony

Aby sprawdzić, czy aplikacja jest uruchomiona, wpisz:

`kubectl exec <your-pod-with-the-app> -i -t -n <your-namespace> -- mysql -p`

Po wyświetleniu monitu podaj hasło. Hasło znajduje się w `mysql-deployment` pliku.

Oto przykładowe dane wyjściowe.

```powershell
C:\Users\user>kubectl exec mysql-695c4d9dcd-rvzff -i -t -n userns1 -- mysql -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
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

Poniżej przedstawiono przykładowe dane wyjściowe po usunięciu wdrożenia i usługi.

```powershell
C:\Users\user>kubectl delete deployment,svc mysql -n userns1
deployment.apps "mysql" deleted
service "mysql" deleted
C:\Users\user>
```
Poniżej przedstawiono przykładowe dane wyjściowe po usunięciu obwodu PVC.

```powershell
C:\Users\user>kubectl delete pvc mysql-pv-claim-sc -n userns1
persistentvolumeclaim "mysql-pv-claim-sc" deleted
C:\Users\user>
```                                                                                         


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak skonfigurować sieć za pośrednictwem polecenia kubectl, zobacz [wdrażanie aplikacji bezstanowej na urządzeniu Azure Stack EDGE Pro](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md)
