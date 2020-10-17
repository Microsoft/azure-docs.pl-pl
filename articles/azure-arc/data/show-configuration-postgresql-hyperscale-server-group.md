---
title: Pokaż konfigurację PostgreSQL grupy serwerów z włączonym łukiem
titleSuffix: Azure Arc enabled data services
description: Pokaż konfigurację PostgreSQL grupy serwerów z włączonym łukiem
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 716759fd6542cd473c236992ac88b69bfe5d0a66
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148010"
---
# <a name="show-the-configuration-of-an-arc-enabled-postgresql-hyperscale-server-group"></a>Pokaż konfigurację PostgreSQL grupy serwerów z włączonym łukiem

W tym artykule opisano sposób wyświetlania konfiguracji grup serwerów. Jest to możliwe dzięki przewidywaniu niektórych pytań i odpowiedzi na nie. Czasami może istnieć kilka prawidłowych odpowiedzi. W tym artykule są naważnie najważniejsze lub użyteczne. Te pytania są pogrupowane według motywu:

- z punktu widzenia Kubernetes
- z punktu widzenia usługi Data Services z włączonym usługą Azure Arc

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-a-kubernetes-point-of-view"></a>Z punktu widzenia Kubernetes

### <a name="how-many-pods-are-used-by-azure-arc-enabled-postgresql-hyperscale"></a>Ile zasobników są używane przez usługę Azure ARC z włączonym skalowaniem PostgreSQL?

Utwórz listę zasobów Kubernetes typu Postgres. Uruchom polecenie:

```console
kubectl get postgresqls [-n <namespace name>]
```

Dane wyjściowe tego polecenia przedstawiają listę utworzonych grup serwerów. Dla każdego z nich wskazuje liczbę numerów. Na przykład:

```output
NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      6h34m
postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d7h
```

Ten przykład pokazuje, że tworzone są dwie grupy serwerów, a każda z nich jest uruchamiana na 3 zasobnikach (1 koordynator + 2 procesy robocze). Oznacza to, że grupy serwerów utworzone w tym kontrolerze danych Azure Arc używają 6 zasobników.

### <a name="what-pods-are-used-by-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Które zasobniki są używane przez usługę Azure ARC z włączonymi grupami serwerów PostgreSQL?

Uruchom:

```console
kubectl get pods [-n <namespace name>]
```

Spowoduje to zwrócenie listy wartości. Zostaną wyświetlone zasobniki używane przez grupy serwerów na podstawie nazw nadanych tym grupom serwerów. Na przykład:

```console 
NAME                 READY   STATUS    RESTARTS   AGE
bootstrapper-vdltm   1/1     Running   0          6d8h
control-h6kc9        2/2     Running   0          6d8h
controldb-0          2/2     Running   0          6d8h
controlwd-96sbn      1/1     Running   0          6d8h
logsdb-0             1/1     Running   0          6d8h
logsui-7wkg2         1/1     Running   0          6d8h
metricsdb-0          1/1     Running   0          6d8h
metricsdc-28ffl      1/1     Running   0          6d8h
metricsui-k7qsh      1/1     Running   0          6d8h
mgmtproxy-gd84z      2/2     Running   0          6d8h
postgres01-0         3/3     Running   0          6h50m
postgres01-1         3/3     Running   0          6h50m
postgres01-2         3/3     Running   0          6h50m
postgres02-0         3/3     Running   0          22h
postgres02-1         3/3     Running   0          22h
postgres02-2         3/3     Running   0          22h
```

W tym przykładzie sześć zasobników, które obsługują dwie tworzone grupy serwerów:
- `postgres01-0`
- `postgres01-1`
- `postgres01-2`
- `postgres02-0`
- `postgres02-1`
- `postgres02-2`  

### <a name="what-server-group-pod-is-used-for-what-role-the-server-group"></a>Której grupy serwerów pod jest używana dla roli grupy serwerów?

Wszystkie nazwy pod sufiksem `-0` reprezentują węzeł koordynatora. Nazwa dowolnego węzła, `-x` gdzie jest 1 lub większa, jest węzłem procesu roboczego. W powyższym przykładzie:
- Koordynatorzy to: `postgres01-0` , `postgres02-0`
- Pracownicy to: `postgres01-2` , `postgres01-2` , `postgres02-1` , `postgres02-2`

### <a name="what-is-the-status-of-the-pods"></a>Jaki jest stan?

Uruchom `kubectl get pods` i spójrz na kolumnę `STATUS`

### <a name="what-persistent-volume-claims-pvcs-are-being-used"></a>Jakie trwałe oświadczenia woluminu są używane? 

Aby zrozumieć, które są używane, a także które są używane do danych, dzienników i kopii zapasowych, uruchom polecenie: 

```console
kubectl get pvc [-n <namespace name>]
```

Domyślnie prefiks nazwy obwodu PVC wskazuje jego użycie:

- `backups-`...: to obwód PVC używany do tworzenia kopii zapasowych
- `data-`...: jest używany obwód PVC dla plików danych
- `logs-`...: to obwód PVC używany dla plików dzienników transakcji/pliku WAL

Na przykład:

```output
NAME                                            STATUS   VOLUME              CAPACITY   ACCESS MODES   STORAGECLASS    AGE
backups-few7hh0k4npx9phsiobdc3hq-postgres01-0   Bound    local-pv-485e37db   1938Gi     RWO            local-storage   6d6h
backups-few7hh0k4npx9phsiobdc3hq-postgres01-1   Bound    local-pv-9d3d4a15   1938Gi     RWO            local-storage   6d6h
backups-few7hh0k4npx9phsiobdc3hq-postgres01-2   Bound    local-pv-7b8dd819   1938Gi     RWO            local-storage   6d6h
...
data-few7hh0k4npx9phsiobdc3hq-postgres01-0      Bound    local-pv-3c1a8cc5   1938Gi     RWO            local-storage   6d6h
data-few7hh0k4npx9phsiobdc3hq-postgres01-1      Bound    local-pv-8303ab19   1938Gi     RWO            local-storage   6d6h
data-few7hh0k4npx9phsiobdc3hq-postgres01-2      Bound    local-pv-55572fe6   1938Gi     RWO            local-storage   6d6h
...
logs-few7hh0k4npx9phsiobdc3hq-postgres01-0      Bound    local-pv-5e852b76   1938Gi     RWO            local-storage   6d6h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-1      Bound    local-pv-55d309a7   1938Gi     RWO            local-storage   6d6h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-2      Bound    local-pv-5ccd02e6   1938Gi     RWO            local-storage   6d6h
...
```


## <a name="from-an-azure-arc-enabled-data-services-point-of-view"></a>Z punktu widzenia z usługą Azure ARC z włączonym punktem usług danych:

* Jak wiele grup serwerów jest tworzonych w kontrolerze danych Arc?
* Jakie są nazwy?
* Ilu węzłów procesu roboczego używają?
* Jaką wersję Postgres uruchomić?

Użyj jednego z następujących poleceń.
- **Z polecenia kubectl:**

   ```console
   kubectl get postgresqls [-n <namespace name>]
   ``` 

   Na przykład generuje poniższe dane wyjściowe, gdzie każdy wiersz jest `servergroup` . Struktura nazwy na poniższym ekranie jest sformatowana jako:

   `<Name-Of-Custom-Resource-Definition>`/`<Server-Group-Name>`

   ```output
   NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
   postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      7h15m
   postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d7h
   ```

   Dane wyjściowe powyżej przedstawiają dwie grupy serwerów, które mają Postgres w wersji 12. Jeśli wersja była Postgres 11, zamiast niej powinna być postgresql-11.arcdata.microsoft.com.

   Każda z nich jest uruchamiana na 3 węzłach/na podst. 1 koordynator i 2 procesy robocze.

- **Z azdata:**

Uruchom następujące polecenie. Dane wyjściowe przedstawiają podobne informacje, które polecenia kubectle:

   ```console
   azdata arc postgres server list

   `output
   Name        State    Workers
   ----------  -------  ---------
   postgres01  Ready    2
   postgres02  Ready    2
   ```


### <a name="how-much-memory-and-vcores-are-being-used-and-what-extensions-were-created-for-a-group"></a>Ile pamięci i rdzeni wirtualnych są używane i jakie rozszerzenia zostały utworzone dla grupy?

Uruchom jedno z następujących poleceń

**Z polecenia kubectl:**

Użyj polecenia kubectl do opisania zasobów Postgres. Aby to zrobić, potrzebny jest jego rodzaj (nazwa zasobu Kubernetes (CRD) dla odpowiedniej wersji Postgres, jak pokazano powyżej) oraz nazwę grupy serwerów.
Ogólny format tego polecenia to:

```console
kubectl describe <CRD name>/<server group name> [-n <namespace name>]
```

Na przykład:

```console
kubectl describe postgresql-12/postgres02
```

Te polecenia pokazują konfigurację grupy serwerów:

```output
Name:         postgres02
Namespace:    arc
Labels:       <none>
Annotations:  <none>
API Version:  arcdata.microsoft.com/v1alpha1
Kind:         postgresql-12
Metadata:
  Creation Timestamp:  2020-08-31T21:01:07Z
  Generation:          10
  Resource Version:    569516
  Self Link:           /apis/arcdata.microsoft.com/v1alpha1/namespaces/arc/postgresql-12s/postgres02
  UID:                 8a9cd118-361b-4a2e-8a9d-5f9257bf6abb
Spec:
  Engine:
    Extensions:
      Name:  citus
      Name:  pg_stat_statements
  Scale:
    Shards:  2
  Scheduling:
    Default:
      Resources:
        Limits:
          Cpu:     4
          Memory:  1024Mi
        Requests:
          Cpu:     1
          Memory:  512Mi
  Service:
    Type:  NodePort
  Storage:
    Data:
      Class Name:  local-storage
      Size:        5Gi
    Logs:
      Class Name:  local-storage
      Size:        5Gi
Status:
  External Endpoint:  10.0.0.4:31066
  Ready Pods:         3/3
  State:              Ready
Events:               <none>
```

Zadzwońmy do określonych punktów orientacyjnych w opisie `servergroup` pokazanego powyżej. Co informuje nas o tej grupie serwerów?

- Jest to wersja 12 Postgres: 
   > Natur         `postgresql-12`
- Został utworzony w miesiącu 2020 sierpnia:
   > Sygnatura czasowa tworzenia:  `2020-08-31T21:01:07Z`
- W tej grupie serwerów utworzono dwa rozszerzenia Postgres: `citus` i `pg_stat_statements`
   > Aparat: rozszerzenia: Name:  `citus` name:  `pg_stat_statements`
- Używa dwóch węzłów procesu roboczego
   > Skala: fragmentów:  `2`
- Gwarantowane jest użycie 1 procesora CPU/rdzeń wirtualny i 512 MB pamięci RAM na węzeł. Będzie ona używać więcej niż 4 procesor CPU/rdzeni wirtualnych i 1024MB pamięci:
   > Planowanie: domyślne: zasoby: limity: procesor CPU: 4 pamięć: żądania 1024Mi: procesor: 1 pamięć: 512Mi
 - Jest on dostępny dla zapytań i nie ma żadnego problemu. Wszystkie węzły są uruchomione:
   > Stan:... Gotowe zasobniki: 3/3 stan: gotowe

**Z azdata:**

Ogólny format polecenia to:

```console
azdata arc postgres server show -n <server group name>
```

Na przykład:

```console
azdata arc postgres server show -n postgres02
```

Zwraca poniższe dane wyjściowe w formacie i treści bardzo podobne do zwróconego przez polecenia kubectl.

```console
{
  "apiVersion": "arcdata.microsoft.com/v1alpha1",
  "kind": "postgresql-12",
  "metadata": {
    "creationTimestamp": "2020-08-31T21:01:07Z",
    "generation": 10,
    "name": "postgres02",
    "namespace": "arc",
    "resourceVersion": "569516",
    "selfLink": "/apis/arcdata.microsoft.com/v1alpha1/namespaces/arc/postgresql-12s/postgres02",
    "uid": "8a9cd118-361b-4a2e-8a9d-5f9257bf6abb"
  },
  "spec": {
    "engine": {
      "extensions": [
        {
          "name": "citus"
        },
        {
          "name": "pg_stat_statements"
        }
      ]
    },
    "scale": {
      "shards": 2
    },
    "scheduling": {
      "default": {
        "resources": {
          "limits": {
            "cpu": "4",
            "memory": "1024Mi"
          },
          "requests": {
            "cpu": "1",
            "memory": "512Mi"
          }
        }
      }
    },
    "service": {
      "type": "NodePort"
    },
    "storage": {
      "data": {
        "className": "local-storage",
        "size": "5Gi"
      },
      "logs": {
        "className": "local-storage",
        "size": "5Gi"
      }
    }
  },
  "status": {
    "externalEndpoint": "10.0.0.4:31066",
    "readyPods": "3/3",
    "state": "Ready"
  }
}
```

## <a name="next-steps"></a>Następne kroki
- [Zapoznaj się z pojęciami dotyczącymi usługi Azure ARC z włączonym skalowaniem PostgreSQL](concepts-distributed-postgres-hyperscale.md)
- [Przeczytaj, jak skalować w poziomie (Dodaj węzły procesu roboczego) do grupy serwerów](scale-out-postgresql-hyperscale-server-group.md)
- [Przeczytaj o sposobie skalowania w górę/w dół (zwiększanie lub zmniejszanie ilości pamięci i/lub rdzeni wirtualnych) grupy serwerów](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)
- [Przeczytaj informacje o konfiguracji magazynu](storage-configuration.md)
- [Przeczytaj, jak monitorować wystąpienie bazy danych](monitor-grafana-kibana.md)
- [Korzystanie z rozszerzeń PostgreSQL w grupie serwerów PostgreSQL w usłudze Azure Arc](using-extensions-in-postgresql-hyperscale-server-group.md)
- [Konfigurowanie zabezpieczeń grupy serwerów PostgreSQL w warstwie Hiperskala z obsługą usługi Azure Arc](configure-security-postgres-hyperscale.md)
