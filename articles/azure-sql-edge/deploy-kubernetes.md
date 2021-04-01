---
title: Wdrażanie kontenera usługi Azure SQL Edge w Kubernetes — Azure SQL Edge
description: Dowiedz się więcej o wdrażaniu kontenera usługi Azure SQL Edge w Kubernetes
keywords: SQL Edge, kontener, Kubernetes
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 31a454c93ad5192f387306a8ec557c4e4d3ae991
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93395295"
---
# <a name="deploy-an-azure-sql-edge-container-in-kubernetes"></a>Wdrażanie kontenera usługi Azure SQL Edge w Kubernetes

Usługę Azure SQL Edge można wdrożyć w klastrze Kubernetes zarówno jako moduł IoT Edge za pomocą Azure IoT Edge działającego na Kubernetes, jak i w autonomicznym kontenerze pod. W pozostałej części tego artykułu skupmy się na wdrożeniu kontenera autonomicznego w klastrze Kubernetes. Aby uzyskać informacje na temat wdrażania Azure IoT Edge na Kubernetes, zapoznaj się z artykułem [Azure IoT Edge on Kubernetes (wersja zapoznawcza)](https://microsoft.github.io/iotedge-k8s-doc/introduction.html).

W tym samouczku pokazano, jak skonfigurować wystąpienie usługi Azure SQL o wysokiej dostępności w kontenerze w klastrze Kubernetes. 

> [!div class="checklist"]
> * Utwórz hasło administratora systemu
> * Tworzenie magazynu
> * Tworzenie wdrożenia
> * Łączenie z SQL Server Management Studio (SSMS)
> * Weryfikuj awarię i odzyskiwanie

Kubernetes 1,6 i nowsze obsługują [klasy magazynu](https://kubernetes.io/docs/concepts/storage/storage-classes/), [trwałe oświadczenia woluminu](https://kubernetes.io/docs/concepts/storage/storage-classes/#persistentvolumeclaims)i [Typ woluminu dysku platformy Azure](https://github.com/kubernetes/examples/tree/master/staging/volumes/azure_disk). Możesz tworzyć wystąpienia usługi Azure SQL Edge i zarządzać nimi natywnie w Kubernetes. W przykładzie w tym artykule przedstawiono sposób tworzenia [wdrożenia](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) w celu osiągnięcia konfiguracji wysokiej dostępności podobnej do wystąpienia klastra trybu failover dysku udostępnionego. W tej konfiguracji Kubernetes odgrywa rolę koordynatora klastra. Gdy wystąpienie usługi Azure SQL Edge w kontenerze nie powiedzie się, program Orchestrator będzie Bootstrap inne wystąpienie kontenera dołączonego do tego samego magazynu trwałego.

![Usługa Azure SQL Edge w klastrze Kubernetes](media/deploy-kubernetes/kubernetes-sql-edge.png)

Na powyższym diagramie `azure-sql-edge` jest kontenerem w elemencie [](https://kubernetes.io/docs/concepts/workloads/pods/pod/). Kubernetes organizuje zasoby w klastrze. [Zestaw replik](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/) gwarantuje, że element pod zostanie automatycznie odzyskany po awarii węzła. Aplikacje nawiązują połączenie z usługą. W takim przypadku usługa reprezentuje moduł równoważenia obciążenia, który hostuje adres IP, który pozostaje taki sam po awarii `azure-sql-edge` .

Na poniższym diagramie `azure-sql-edge` kontener nie powiódł się. Ponieważ koordynator Kubernetes gwarantuje poprawną liczbę wystąpień w dobrej kondycji w zestawie replik i uruchamia nowy kontener zgodnie z konfiguracją. Program Orchestrator uruchamia nowe miejsce w tym samym węźle i `azure-sql-edge` ponownie nawiązuje połączenie z tym samym magazynem trwałym. Usługa nawiązuje połączenie z nowo utworzoną usługą `azure-sql-edge` .

![Usługa Azure SQL Edge w klastrze Kubernetes po awarii elementu pod](media/deploy-kubernetes/kubernetes-sql-edge-after-pod-fail.png)

Na poniższym diagramie węzeł, w którym znajduje `azure-sql-edge` się kontener, nie powiódł się. Program Orchestrator uruchamia nowy element pod innym węzłem i `azure-sql-edge` nawiązuje połączenie z tym samym magazynem trwałym. Usługa nawiązuje połączenie z nowo utworzoną usługą `azure-sql-edge` .

![Usługa Azure SQL Edge w klastrze Kubernetes po awarii węzła](media/deploy-kubernetes/kubernetes-sql-edge-after-node-fail.png)

## <a name="prerequisites"></a>Wymagania wstępne

* **Klaster Kubernetes**
   - Samouczek wymaga klastra Kubernetes. Do zarządzania klastrem służy procedura [polecenia kubectl](https://kubernetes.io/docs/user-guide/kubectl/) . 

   - Na potrzeby tego samouczka będziemy używać usługi Azure Kubernetes Service do wdrażania usługi Azure SQL Edge. Zobacz [wdrażanie klastra usługi Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-deploy-cluster.md) , aby utworzyć klaster Kubernetes z jednym węzłem i połączyć się z nim w AKS `kubectl` . 

   >[!NOTE]
   >Aby zapewnić ochronę przed awarią węzła, klaster Kubernetes wymaga więcej niż jednego węzła.

* **Interfejs wiersza polecenia platformy Azure**
   - Instrukcje podane w tym samouczku zostały sprawdzone pod kątem interfejsu wiersza polecenia platformy Azure 2.10.1.

## <a name="create-a-kubernetes-namespace-for-sql-edge-deployment"></a>Tworzenie przestrzeni nazw Kubernetes dla wdrożenia programu SQL Edge

Utwórz nową przestrzeń nazw w klastrze Kubernetes. Ta przestrzeń nazw zostanie użyta do wdrożenia programu SQL Edge i wszystkich wymaganych artefaktów. Aby uzyskać więcej informacji na temat przestrzeni nazw Kubernetes, odwołuje się do [przestrzeni nazw](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/).

   ```azurecli
   kubectl create namespace <namespace name>
   ```  

## <a name="create-an-sa-password"></a>Utwórz hasło administratora systemu

Utwórz hasło administratora systemu w klastrze Kubernetes. Kubernetes może zarządzać poufnymi informacjami o konfiguracji, takimi jak hasła, jako wpisy [tajne](https://kubernetes.io/docs/concepts/configuration/secret/).

Następujące polecenie tworzy hasło dla konta administratora systemu:

   ```azurecli
   kubectl create secret generic mssql --from-literal=SA_PASSWORD="MyC0m9l&xP@ssw0rd" -n <namespace name>
   ```  

   Zamień `MyC0m9l&xP@ssw0rd` na złożone hasło.

## <a name="create-storage"></a>Tworzenie magazynu

Skonfiguruj w klastrze Kubernetes [wolumin trwały](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) i [trwałe](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistent-volume-claim-protection) . Wykonaj poniższe czynności: 

1. Utwórz manifest w celu zdefiniowania klasy magazynu i trwałego zastrzeżenia woluminu.  Manifest określa Inicjowanie obsługi magazynu, parametry i [zasady odzyskiwania](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#reclaiming). Klaster Kubernetes używa tego manifestu do utworzenia trwałego magazynu. 

   Poniższy przykład YAML definiuje klasy magazynu i trwałego zastrzeżenia woluminu. Zainicjowanie klasy magazynu jest `azure-disk` , ponieważ ten klaster Kubernetes znajduje się na platformie Azure. Typ konta magazynu to `Standard_LRS` . Liczba trwałych roszczeń jest nazywana `mssql-data` . Metadane trwałego żądania zbiorowego zawierają adnotację łączącą ją z powrotem z klasą magazynu. 

   ```yaml
   kind: StorageClass
   apiVersion: storage.k8s.io/v1
   metadata:
        name: azure-disk
   provisioner: kubernetes.io/azure-disk
   parameters:
     storageaccounttype: Standard_LRS
     kind: managed
   ---
   kind: PersistentVolumeClaim
   apiVersion: v1
   metadata:
     name: mssql-data
     annotations:
       volume.beta.kubernetes.io/storage-class: azure-disk
   spec:
     accessModes:
     - ReadWriteOnce
     resources:
       requests:
         storage: 8Gi
   ```

   Zapisz plik (na przykład **PVC. YAML**).

2. Utwórz w Kubernetes wartość trwałego żądania woluminu.

   ```azurecli
   kubectl apply -f <Path to pvc.yaml file> -n <namespace name>
   ```

   `<Path to pvc.yaml file>` jest lokalizacją, w której zapisano plik.

   Wolumin trwały jest automatycznie tworzony jako konto usługi Azure Storage i jest powiązany z nietrwałym wnioskiem o woluminie. 

    ![Zrzut ekranu przedstawiający polecenie trwałego żądania woluminu](media/deploy-kubernetes/pvc-cmd.png)

3. Sprawdź poprawność woluminu trwałego.

   ```azurecli
   kubectl describe pvc <PersistentVolumeClaim>  -n <name of the namespace>
   ```

   `<PersistentVolumeClaim>` jest nazwą zastrzeżenia trwałego woluminu.

   W poprzednim kroku, trwałego wystąpienia woluminu ma nazwę `mssql-data` . Aby wyświetlić metadane dotyczące trwałego zastrzeżenia woluminu, uruchom następujące polecenie:

   ```azurecli
   kubectl describe pvc mssql-data  -n <namespace name>
   ```

   Zwrócone metadane zawierają wartość o nazwie `Volume` . Ta wartość jest mapowana na nazwę obiektu BLOB.

   ![Zrzut ekranu przedstawiający zwrócone metadane, w tym wolumin](media/deploy-kubernetes/describe-volume.png)

4. Sprawdź wolumin trwały.

   ```azurecli
   kubectl describe pv -n <namespace name>
   ```

   `kubectl` zwraca metadane dotyczące woluminu trwałego, który został automatycznie utworzony i powiązany z nietrwałym wnioskiem o woluminie. 

## <a name="create-the-deployment"></a>Tworzenie wdrożenia

W tym przykładzie kontener obsługujący wystąpienie usługi Azure SQL Edge jest opisany jako obiekt wdrożenia Kubernetes. Wdrożenie tworzy zestaw replik. Zestaw replik tworzy pod. 

W tym kroku utworzysz manifest opisujący kontener oparty na obrazie Docker usługi Azure SQL Edge. Manifest odwołuje się do `mssql-data` trwałego żądania woluminu i `mssql` wpisu tajnego, który został już zastosowany do klastra Kubernetes. Manifest zawiera również opis [usługi](https://kubernetes.io/docs/concepts/services-networking/service/). Ta usługa jest modułem równoważenia obciążenia. Moduł równoważenia obciążenia gwarantuje, że adres IP będzie nadal występować po odzyskaniu wystąpienia usługi Azure SQL Edge. 

1. Utwórz manifest (plik YAML), aby opisać wdrożenie. W poniższym przykładzie opisano wdrożenie, w tym kontener oparty na obrazie kontenera usługi Azure SQL Edge.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sqledge-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sqledge
  template:
    metadata:
      labels:
        app: sqledge
    spec:
      volumes:
        - name: sqldata
          persistentVolumeClaim:
            claimName: mssql-data
      containers:
        - name: azuresqledge
          image: mcr.microsoft.com/azure-sql-edge:latest
          ports:
            - containerPort: 1433
          volumeMounts:
            - name: sqldata
              mountPath: /var/opt/mssql
          env:
            - name: MSSQL_PID
              value: "Developer"
            - name: ACCEPT_EULA
              value: "Y"
            - name: SA_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mssql
                  key: SA_PASSWORD
            - name: MSSQL_AGENT_ENABLED
              value: "TRUE"
            - name: MSSQL_COLLATION
              value: "SQL_Latin1_General_CP1_CI_AS"
            - name: MSSQL_LCID
              value: "1033"
      terminationGracePeriodSeconds: 30
      securityContext:
        fsGroup: 10001
---
apiVersion: v1
kind: Service
metadata:
  name: sqledge-deployment
spec:
  selector:
    app: sqledge
  ports:
    - protocol: TCP
      port: 1433
      targetPort: 1433
      name: sql
  type: LoadBalancer
```

   Skopiuj poprzedni kod do nowego pliku o nazwie `sqldeployment.yaml` . Zaktualizuj następujące wartości: 

   * MSSQL_PID `value: "Developer"` : ustawia kontener do uruchomienia usługi Azure SQL Edge Developer Edition. Wersja Developer Edition nie ma licencji na dane produkcyjne. Jeśli wdrożenie jest przeznaczone do użytku produkcyjnego, ustaw wersję na `Premium` . 

      >[!NOTE]
      >Aby uzyskać więcej informacji, zobacz artykuł [jak uzyskać licencję na usługę Azure SQL Edge](https://azure.microsoft.com/pricing/details/sql-edge/).

   * `persistentVolumeClaim`: Ta wartość wymaga wpisu dla `claimName:` mapowania na nazwę używaną dla trwałego żądania woluminu. W tym samouczku użyto regionu `mssql-data`. 

   * `name: SA_PASSWORD`: Konfiguruje obraz kontenera w celu ustawienia hasła administratora systemu zgodnie z definicją w tej sekcji.

     ```yaml
     valueFrom:
       secretKeyRef:
         name: mssql
         key: SA_PASSWORD 
     ```

     Gdy Kubernetes wdraża kontener, odwołuje się on do wpisu tajnego, `mssql` Aby uzyskać wartość hasła. 

   >[!NOTE]
   >Przy użyciu `LoadBalancer` typu usługi wystąpienie programu Azure SQL Edge jest dostępne zdalnie (za pośrednictwem Internetu) na porcie 1433.

   Zapisz plik (na przykład **sqledgedeploy. YAML**).

2. Utwórz wdrożenie.

   ```azurecli
   kubectl apply -f <Path to sqledgedeploy.yaml file> -n <namespace name>
   ```

   `<Path to sqldeployment.yaml file>` jest lokalizacją, w której zapisano plik.

   ![Zrzut ekranu przedstawiający polecenie wdrożenia](media/deploy-kubernetes/deploy-sql-cmd.png)

   Wdrożenie i usługa są tworzone. Wystąpienie usługi Azure SQL Edge znajduje się w kontenerze podłączonym do magazynu trwałego.

   Aby wyświetlić stan elementu pod, wpisz `kubectl get pod -n <namespace name>` .

   ![Zrzut ekranu przedstawiający polecenie Get pod](media/deploy-kubernetes/get-sql-pod-cmd.png)

   Na powyższym obrazie element pod ma stan `Running` . Ten stan wskazuje, że kontener jest gotowy. Może to potrwać kilka minut.

   >[!NOTE]
   >Po utworzeniu wdrożenia może upłynąć kilka minut, zanim będzie widoczne. Opóźnienie wynika z faktu, że klaster pobiera obraz kontenera usługi Azure SQL Edge z centrum Docker. Po pierwszym ściągnięciu obrazu kolejne wdrożenia mogą być szybsze, jeśli wdrożenie jest w węźle, w którym znajduje się już na nim obraz. 

3. Sprawdź, czy usługi są uruchomione. Uruchom następujące polecenie:

   ```azurecli
   kubectl get services -n <namespace name>
   ```

   To polecenie zwraca usługi, które są uruchomione, a także wewnętrzne i zewnętrzne adresy IP dla usług. Zanotuj zewnętrzny adres IP dla `mssql-deployment` usługi. Użyj tego adresu IP, aby nawiązać połączenie z usługą Azure SQL Edge. 

   ![Zrzut ekranu przedstawiający polecenie Get Service](media/deploy-kubernetes/get-service-cmd.png)

   Aby uzyskać więcej informacji na temat stanu obiektów w klastrze Kubernetes, uruchom polecenie:

   ```azurecli
   az aks browse --resource-group <MyResourceGroup> --name <MyKubernetesClustername>
   ```  

## <a name="connect-to-the-azure-sql-edge-instance"></a>Nawiązywanie połączenia z wystąpieniem usługi Azure SQL Edge

W przypadku skonfigurowania kontenera zgodnie z opisem można nawiązać połączenie z aplikacją spoza sieci wirtualnej platformy Azure. Użyj `sa` konta i zewnętrznego adresu IP dla usługi. Użyj hasła, które zostało skonfigurowane jako wpis tajny Kubernetes. Aby uzyskać więcej informacji na temat nawiązywania połączenia z wystąpieniem usługi Azure SQL Edge, zapoznaj się z tematem [Connect to Azure SQL Edge](connect.md).

## <a name="verify-failure-and-recovery"></a>Weryfikuj awarię i odzyskiwanie

Aby sprawdzić awarię i odzyskiwanie, można usunąć pod. Wykonaj następujące czynności:

1. Utwórz listę pod kontrolą usługi Azure SQL Edge.

   ```azurecli
   kubectl get pods -n <namespace name>
   ```

   Zanotuj nazwę pod kontrolą usługi Azure SQL Edge.

2. Usuń pod.

   ```azurecli
   kubectl delete pod sqledge-deployment-7df66c9999-rc9xl
   ```
   `sqledge-deployment-7df66c9999-rc9xl` jest wartością zwracaną z poprzedniego kroku dla nazwy pod. 

Kubernetes automatycznie ponownie tworzy element pod, aby odzyskać wystąpienie usługi Azure SQL Edge i nawiązać połączenie z magazynem trwałym. Użyj `kubectl get pods` , aby sprawdzić, czy nowy pod został wdrożony. Użyj, `kubectl get services` Aby sprawdzić, czy adres IP dla nowego kontenera jest taki sam. 

## <a name="summary"></a>Podsumowanie

W tym samouczku przedstawiono sposób wdrażania kontenerów usługi Azure SQL Edge w klastrze Kubernetes w celu zapewnienia wysokiej dostępności. 

> [!div class="checklist"]
> * Utwórz hasło administratora systemu
> * Tworzenie magazynu
> * Tworzenie wdrożenia
> * Nawiązywanie połączenia z usługą Azure SQL Edge Management Studios (SSMS)
> * Weryfikuj awarię i odzyskiwanie

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do platformy Kubernetes](../aks/intro-kubernetes.md)
- [Machine Learning i sztuczna inteligencja przy użyciu ONNX w programie SQL Edge](onnx-overview.md).
- [Tworzenie kompleksowego rozwiązania IoT za pomocą programu SQL Edge przy użyciu IoT Edge](tutorial-deploy-azure-resources.md).
- [Przesyłanie strumieniowe danych w usłudze Azure SQL Edge](stream-data.md)