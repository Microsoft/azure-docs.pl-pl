---
title: Usuwanie grupy serwerów PostgreSQL z funkcją Azure Arc
description: Usuwanie grupy serwerów Postgres z funkcją Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 7932ad3b30910e539acfbff2329a03f80a4d1a0b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670362"
---
# <a name="delete-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Usuwanie grupy serwerów PostgreSQL z funkcją Azure Arc

W tym dokumencie opisano kroki usuwania grupy serwerów z konfiguracji usługi Azure Arc.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="delete-the-server-group"></a>Usuń grupę serwerów

Rozważmy na przykład, że chcemy usunąć wystąpienie _postgres01_ z poniższej konfiguracji:

```console
azdata arc postgres server list
Name        State    Workers
----------  -------  ---------
postgres01  Ready    3
```

Ogólny format polecenia Delete to:
```console
azdata arc postgres server delete -n <server group name>
```
Po wykonaniu tego polecenia pojawi się prośba o potwierdzenie usunięcia grupy serwerów. Jeśli używasz skryptów do automatyzowania operacji usuwania, musisz użyć parametru--Force, aby pominąć żądanie potwierdzenia. Na przykład można uruchomić polecenie podobne do następujących: 
```console
azdata arc postgres server delete -n <server group name> --force
```

Aby uzyskać więcej informacji na temat polecenia Delete, uruchom polecenie:
```console
azdata arc postgres server delete --help
```

### <a name="delete-the-server-group-used-in-this-example"></a>Usuń grupę serwerów użytą w tym przykładzie

```console
azdata arc postgres server delete -n postgres01
```

## <a name="reclaim-the-kubernetes-persistent-volume-claims-pvcs"></a>Odzyskaj Kubernetes trwałe oświadczenia woluminu (PVC)

Usunięcie grupy serwerów nie powoduje usunięcia skojarzonych z nimi [obwodów PVC](https://kubernetes.io/docs/concepts/storage/persistent-volumes/). Jest to celowe. Ma to na celu ułatwienie użytkownikowi uzyskania dostępu do plików bazy danych w razie przypadkowego usunięcia wystąpienia. Usuwanie roszczeń do woluminów trwałych nie jest obowiązkowe. Jest to jednak zalecane. Jeśli nie odzyskasz tych roszczeń do woluminów trwałych, w końcu wystąpią błędy, ponieważ klaster Kubernetes pomyśli, że kończy się miejsce na dysku. Aby odzyskać roszczenia do woluminów trwałych, wykonaj następujące czynności:

### <a name="1-list-the-pvcs-for-the-server-group-you-deleted"></a>1. Utwórz listę obwodów PVC dla usuniętej grupy serwerów

Aby wyświetlić listę obwodów PVC, uruchom następujące polecenie:

```console
kubectl get pvc [-n <namespace name>]
```

Zwraca listę obwodów PVC, w szczególności obwodów PVC dla usuniętej grupy serwerów. Na przykład:

```output
kubectl get pvc
NAME                                         STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
data-few7hh0k4npx9phsiobdc3hq-postgres01-0   Bound    pvc-72ccc225-dad0-4dee-8eae-ed352be847aa   5Gi        RWO            default        2d18h
data-few7hh0k4npx9phsiobdc3hq-postgres01-1   Bound    pvc-ce6f0c51-faed-45ae-9472-8cdf390deb0d   5Gi        RWO            default        2d18h
data-few7hh0k4npx9phsiobdc3hq-postgres01-2   Bound    pvc-5a863ab9-522a-45f3-889b-8084c48c32f8   5Gi        RWO            default        2d18h
data-few7hh0k4npx9phsiobdc3hq-postgres01-3   Bound    pvc-00e1ace3-1452-434f-8445-767ec39c23f2   5Gi        RWO            default        2d15h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-0   Bound    pvc-8b810f4c-d72a-474a-a5d7-64ec26fa32de   5Gi        RWO            default        2d18h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-1   Bound    pvc-51d1e91b-08a9-4b6b-858d-38e8e06e60f9   5Gi        RWO            default        2d18h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-2   Bound    pvc-8e5ad55e-300d-4353-92d8-2e383b3fe96e   5Gi        RWO            default        2d18h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-3   Bound    pvc-f9e4cb98-c943-45b0-aa07-dd5cff7ea585   5Gi        RWO            default        2d15h
```
Dla tej grupy serwerów istnieje 8 obwodów PVC.

### <a name="2-delete-each-of-the-pvcs"></a>2. Usuń wszystkie obwody PVC

Usuń dane i Rejestruj obwody PVC dla każdego z węzłów PostgreSQL (koordynator i procesy robocze) usuniętej grupy serwerów.

Ogólny format tego polecenia to: 

```console
kubectl delete pvc <name of pvc>  [-n <namespace name>]
```

Na przykład:

```console
kubectl delete pvc data-few7hh0k4npx9phsiobdc3hq-postgres01-0
kubectl delete pvc data-few7hh0k4npx9phsiobdc3hq-postgres01-1
kubectl delete pvc data-few7hh0k4npx9phsiobdc3hq-postgres01-2
kubectl delete pvc data-few7hh0k4npx9phsiobdc3hq-postgres01-3
kubectl delete pvc logs-few7hh0k4npx9phsiobdc3hq-postgres01-0
kubectl delete pvc logs-few7hh0k4npx9phsiobdc3hq-postgres01-1
kubectl delete pvc logs-few7hh0k4npx9phsiobdc3hq-postgres01-2
kubectl delete pvc logs-few7hh0k4npx9phsiobdc3hq-postgres01-3
```

Każde z tych poleceń polecenia kubectl potwierdzi pomyślne usunięcie obwodu PVC. Na przykład:

```output
persistentvolumeclaim "data-postgres01-0" deleted
```
  

>[!NOTE]
> Jak wskazano, nie należy usuwać obwodów PVC ostatecznie można uzyskać klaster Kubernetes w sytuacji, w której będzie zgłaszał błędy. Niektóre z tych błędów mogą polegać na tym, że nie można zalogować się do klastra Kubernetes z azdata, ponieważ z tego powodu problem z magazynem (normalne zachowanie Kubernetes) może zostać wykluczony.
>
> Można na przykład wyświetlić komunikaty w dziennikach podobne do następujących:  
> ```output
> Annotations:    microsoft.com/ignore-pod-health: true  
> Status:         Failed  
> Reason:         Evicted  
> Message:        The node was low on resource: ephemeral-storage. Container controller was using 16372Ki, which exceeds its request of 0.
> ```
    
## <a name="next-step"></a>Następny krok
Utwórz [skalowanie PostgreSQL z włączoną funkcją Azure Arc](create-postgresql-hyperscale-server-group.md)
