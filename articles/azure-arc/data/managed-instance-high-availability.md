---
title: Usługa Azure ARC z włączoną funkcją wysokiej dostępności wystąpienia zarządzanego
titleSuffix: Deploy Azure Arc enabled Managed Instance with high availability
description: Dowiedz się, jak wdrożyć wystąpienie zarządzane z funkcją Azure ARC z wysoką dostępnością.
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 03/02/2021
ms.topic: conceptual
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 92f5c900238fc5d40e22870e2f00f8adeb5d335f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102032198"
---
# <a name="azure-arc-enabled-managed-instance-high-availability"></a>Usługa Azure ARC z włączoną funkcją wysokiej dostępności wystąpienia zarządzanego

Wystąpienie zarządzane usługi Azure Arc w systemie jest wdrażane na Kubernetes jako aplikacja kontenera i korzysta z konstrukcji Kubernetes, takich jak zestawy stanowe i magazyn trwały, aby zapewnić wbudowaną funkcję monitorowania kondycji, wykrywania awarii i mechanizmów trybu failover w celu utrzymania kondycji usługi. Aby zwiększyć niezawodność, można również skonfigurować wystąpienie zarządzane usługi Azure ARC z obsługą dodatkowych replik w konfiguracji o wysokiej dostępności. Monitorowanie, wykrywanie błędów i automatyczne przełączanie w tryb failover są zarządzane przez kontroler danych usługi Arc Data Services. Ta usługa jest świadczona bez interwencji użytkownika — wszystko z konfiguracji grupy dostępności, Konfigurowanie punktów końcowych dublowania baz danych, Dodawanie baz danych do grupy dostępności lub przejście w tryb failover i koordynacja uaktualniania. W tym dokumencie przedstawiono oba typy wysokiej dostępności.

## <a name="built-in-high-availability"></a>Wbudowana wysoka dostępność 

Wbudowana wysoka dostępność jest zapewniana przez Kubernetes, gdy zdalny magazyn trwały jest skonfigurowany i udostępniany węzłom używanym przez wdrożenie usługi danych Arc. W tej konfiguracji Kubernetes odgrywa rolę koordynatora klastra. Gdy wystąpienie zarządzane w kontenerze lub w podstawowym węźle nie powiedzie się, program Orchestrator uruchamia kolejne wystąpienie kontenera i dołącza go do tego samego magazynu trwałego. Ten typ jest domyślnie włączany podczas wdrażania wystąpienia zarządzanego usługi Azure ARC z włączoną obsługą.

### <a name="verify-built-in-high-availability"></a>Weryfikowanie wbudowanej wysokiej dostępności

Ta sekcja umożliwia zweryfikowanie wbudowanej wysokiej dostępności udostępnionej przez Kubernetes. Po wykonaniu kroków w celu przetestowania tej funkcji należy usunąć z niej część istniejącego wystąpienia zarządzanego i sprawdzić, czy Kubernetes odzyskuje się z tej akcji. 

### <a name="prerequisites"></a>Wymagania wstępne

- Klaster Kubernetes musi mieć [udostępniony Magazyn zdalny](storage-configuration.md#factors-to-consider-when-choosing-your-storage-configuration) 
- Wystąpienie zarządzane z funkcją Azure ARC, które zostało wdrożone z jedną repliką (domyślnie)

1. Wyświetlanie zasobników. 

   ```console
   kubectl get pods -n <namespace of data controller>
   ```

2. Usuń wystąpienie zarządzane pod.

   ```console
   kubectl delete pod <name of managed instance>-0 -n <namespace of data controller>
   ```

   Na przykład

   ```output
   user@pc:/# kubectl delete pod sql1-0 -n arc
   pod "sql1-0" deleted
   ```

3. Wyświetl zasobniki, aby sprawdzić, czy zarządzane wystąpienie jest odzyskiwane.

   ```console
   kubectl get pods -n <namespace of data controller>
   ```

   Na przykład

   ```output
   user@pc:/# kubectl get pods -n arc
   NAME                 READY   STATUS    RESTARTS   AGE
   sql1-0               2/3     Running   0          22s
   ```

Po odzyskaniu wszystkich kontenerów w ramach programu można nawiązać połączenie z wystąpieniem zarządzanym.

## <a name="deploy-with-always-on-availability-groups"></a>Wdróż z zawsze włączonymi grupami dostępności

W celu zwiększenia niezawodności można skonfigurować wystąpienie zarządzane z obsługą usługi Azure Arc w celu wdrożenia z dodatkowymi replikami w konfiguracji o wysokiej dostępności. 

Możliwości włączane przez grupy dostępności:

- W przypadku wdrożenia z wieloma replikami tworzona jest pojedyncza Grupa dostępności o nazwie `containedag` . Domyślnie program `containedag` ma trzy repliki, w tym podstawowy. Wszystkie operacje CRUD dla grupy dostępności są zarządzane wewnętrznie, w tym do tworzenia grupy dostępności lub łączenia replik z utworzoną grupą dostępności. Nie można tworzyć dodatkowych grup dostępności w wystąpieniu zarządzanym z włączonym łukiem Azure.

- Wszystkie bazy danych są automatycznie dodawane do grupy dostępności, w tym wszystkich baz danych użytkowników i systemów, takich jak `master` i `msdb` . Ta funkcja zapewnia jednolity widok między replikami grup dostępności. Zwróć uwagę `containedag_master` na te i `containedag_msdb` bazy danych, jeśli łączysz się bezpośrednio z wystąpieniem. `containedag_*`Bazy danych reprezentują i należy do `master` `msdb` grupy dostępności.

- Zewnętrzny punkt końcowy jest automatycznie inicjowany do łączenia z bazami danych w grupie dostępności. Ten punkt końcowy `<managed_instance_name>-svc-external` odgrywa rolę odbiornika grupy dostępności.

### <a name="deploy"></a>Wdróż

Aby wdrożyć wystąpienie zarządzane z grupami dostępności, uruchom następujące polecenie.

```console
azdata arc sql mi create -n <name of instance> --replicas 3
```

### <a name="check-status"></a>Sprawdzanie stanu
Po wdrożeniu wystąpienia Uruchom następujące polecenia, aby sprawdzić stan wystąpienia:

```console
azdata arc sql mi list
azdata arc sql mi show -n <name of instance>
```

Przykładowe dane wyjściowe:

```output
user@pc:/# azdata arc sql mi list
ExternalEndpoint    Name    Replicas    State
------------------  ------  ----------  -------
20.131.31.58,1433   sql2    3/3         Ready

user@pc:/#  azdata arc sql mi show -n sql2
{
...
  "status": {
    "AGStatus": "Healthy",
    "externalEndpoint": "20.131.31.58,1433",
    "logSearchDashboard": "link to logs dashboard",
    "metricsDashboard": "link to metrics dashboard",
    "readyReplicas": "3/3",
    "state": "Ready"
  }
}
```

Zwróć uwagę na dodatkową liczbę `Replicas` i `AGstatus` pole wskazujące prawidłowość grupy dostępności. Jeśli wszystkie repliki są zsynchronizowane i synchronizowane, ta wartość jest `healthy` . 

### <a name="restore-a-database"></a>Przywracanie bazy danych 
Aby przywrócić bazę danych do grupy dostępności, wymagane są dodatkowe czynności. Poniższe kroki przedstawiają sposób przywracania bazy danych do wystąpienia zarządzanego i dodawania go do grupy dostępności. 

1. Uwidocznij zewnętrzny punkt końcowy wystąpienia, tworząc nową usługę Kubernetes.

    Ustal, który hostuje replikę podstawową, łącząc się z wystąpieniem zarządzanym i uruchom:

    ```sql
    SELECT @@SERVERNAME
    ```
    Utwórz usługę Kubernetes do wystąpienia podstawowego, uruchamiając poniższe polecenie, Jeśli klaster Kubernetes korzysta z usług nodePort Services. Zamień na `podName` nazwę serwera, który został zwrócony w poprzednim kroku, `serviceName` z nazwą preferowaną dla utworzonej usługi Kubernetes.

    ```bash
    kubectl -n <namespaceName> expose pod <podName> --port=1533  --name=<serviceName> --type=NodePort
    ```

    W przypadku usługi równoważenia obciążenia Uruchom to samo polecenie, z tą różnicą, że tworzony jest typ usługi `LoadBalancer` . Na przykład: 

    ```bash
    kubectl -n <namespaceName> expose pod <podName> --port=1533  --name=<serviceName> --type=LoadBalancer
    ```

    Oto przykład tego polecenia w odniesieniu do usługi Azure Kubernetes, gdzie hostem podstawowym jest `sql2-0` :

    ```bash
    kubectl -n arc-cluster expose pod sql2-0 --port=1533  --name=sql2-0-p --type=LoadBalancer
    ```

    Pobierz adres IP usługi Kubernetes:

    ```bash
    kubectl get services -n <namespaceName>
    ```
2. Przywróć bazę danych do punktu końcowego podstawowego wystąpienia.

    Dodaj plik kopii zapasowej bazy danych do kontenera wystąpienia podstawowego.

    ```console
    kubectl cp <source file location> <pod name>:var/opt/mssql/data/<file name> -n <namespace name>
    ```

    Przykład

    ```console
    kubectl cp /home/WideWorldImporters-Full.bak sql2-1:var/opt/mssql/data/WideWorldImporters-Full.bak -c arc-sqlmi -n arc
    ```

    Przywróć plik kopii zapasowej bazy danych, uruchamiając poniższe polecenie.

    ```sql 
    RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/<file name>.bak'
    WITH MOVE '<database name>' to '/var/opt/mssql/data/<file name>.mdf'  
    ,MOVE '<database name>' to '/var/opt/mssql/data/<file name>_log.ldf'  
    ,RECOVERY, REPLACE, STATS = 5;  
    GO
    ```
    
    Przykład

    ```sql
    RESTORE Database WideWorldImporters
    FROM DISK = '/var/opt/mssql/data/WideWorldImporters-Full.BAK'
    WITH
    MOVE 'WWI_Primary' TO '/var/opt/mssql/data/WideWorldImporters.mdf',
    MOVE 'WWI_UserData' TO '/var/opt/mssql/data/WideWorldImporters_UserData.ndf',
    MOVE 'WWI_Log' TO '/var/opt/mssql/data/WideWorldImporters.ldf',
    MOVE 'WWI_InMemory_Data_1' TO '/var/opt/mssql/data/WideWorldImporters_InMemory_Data_1',
    RECOVERY, REPLACE, STATS = 5;  
    GO
    ```

3. Dodaj bazę danych do grupy dostępności.

    Aby baza danych była dodawana do usługi AG, musi być uruchomiona w trybie pełnego odzyskiwania i należy wykonać kopię zapasową dziennika. Uruchom poniższe instrukcje TSQL, aby dodać przywróconą bazę danych do grupy dostępności.

    ```sql
    ALTER DATABASE <databaseName> SET RECOVERY FULL;
    BACKUP DATABASE <databaseName> TO DISK='<filePath>'
    ALTER AVAILABILITY GROUP containedag ADD DATABASE <databaseName>
    ```

    Poniższy przykład dodaje bazę danych o nazwie `WideWorldImporters` , która została przywrócona dla wystąpienia:

    ```sql
    ALTER DATABASE WideWorldImporters SET RECOVERY FULL;
    BACKUP DATABASE WideWorldImporters TO DISK='/var/opt/mssql/data/WideWorldImporters.bak'
    ALTER AVAILABILITY GROUP containedag ADD DATABASE WideWorldImporters
    ```

> [!IMPORTANT]
> Najlepszym rozwiązaniem jest oczyszczenie przez usunięcie usługi Kubernetes utworzonej powyżej, uruchamiając następujące polecenie:
>
>```bash
>kubectl delete svc sql2-0-p -n arc
>```

### <a name="limitations"></a>Ograniczenia

Grupy dostępności wystąpienia zarządzanego z obsługą usługi Azure Arc mają takie same [ograniczenia jak w przypadku grup dostępności klastra danych Big Data. Kliknij tutaj, aby dowiedzieć się więcej.](/sql/big-data-cluster/deployment-high-availability#known-limitations)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [funkcjach i możliwościach wystąpienia zarządzanego przez usługę Azure Arc](managed-instance-features.md)
