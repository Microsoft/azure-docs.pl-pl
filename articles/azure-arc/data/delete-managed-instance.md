---
title: Usuń wystąpienie zarządzane SQL z obsługą usługi Azure Arc
description: Usuń wystąpienie zarządzane SQL z obsługą usługi Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: e531349e8f404380d9f0601caa3b66557c297062
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90940850"
---
# <a name="delete-azure-arc-enabled-sql-managed-instance"></a>Usuń wystąpienie zarządzane SQL z obsługą usługi Azure Arc
W tym artykule opisano, jak usunąć wystąpienie zarządzane SQL z obsługą usługi Azure Arc.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="view-existing-azure-arc-enabled-sql-managed-instances"></a>Wyświetl istniejące wystąpienia zarządzane w usłudze Azure Arc
Aby wyświetlić wystąpienia zarządzane SQL, uruchom następujące polecenie:

```console
azdata arc sql mi list
```

Dane wyjściowe powinny wyglądać następująco:

```console
Name    Replicas    ServerEndpoint    State
------  ----------  ----------------  -------
demo-mi 1/1         10.240.0.4:32023  Ready
```

## <a name="delete-a-azure-arc-enabled-sql-managed-instance"></a>Usuń wystąpienie zarządzane SQL z funkcją Azure Arc
Aby usunąć wystąpienie zarządzane SQL, uruchom następujące polecenie:

```console
azdata arc sql mi delete -n <NAME_OF_INSTANCE>
```

Dane wyjściowe powinny wyglądać następująco:

```console
# azdata arc sql mi delete -n demo-mi
Deleted demo-mi from namespace arc
```

## <a name="reclaim-the-kubernetes-persistent-volume-claims-pvcs"></a>Odzyskaj Kubernetes trwałe oświadczenia woluminu (PVC)

Usunięcie wystąpienia zarządzanego SQL nie powoduje usunięcia skojarzonych z nim [obwodów PVC](https://kubernetes.io/docs/concepts/storage/persistent-volumes/). Jest to celowe. Ma to na celu ułatwienie użytkownikowi uzyskania dostępu do plików bazy danych w razie przypadkowego usunięcia wystąpienia. Usuwanie roszczeń do woluminów trwałych nie jest obowiązkowe. Jest to jednak zalecane. Jeśli nie odzyskasz tych obwodów PVC, ostatecznie zakończy się z błędami, ponieważ w klastrze Kubernetes nie ma miejsca na dysku. Aby odzyskać roszczenia do woluminów trwałych, wykonaj następujące czynności:

### <a name="1-list-the-pvcs-for-the-server-group-you-deleted"></a>1. Utwórz listę obwodów PVC dla usuniętej grupy serwerów
Aby wyświetlić listę obwodów PVC, uruchom następujące polecenie:
```console
kubectl get pvc
```

W poniższym przykładzie Zwróć uwagę na obwody PVC dla usuniętych wystąpień zarządzanych przez program SQL Server.
```console
# kubectl get pvc -n arc

NAME                    STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
data-demo-mi-0        Bound     pvc-1030df34-4b0d-4148-8986-4e4c20660cc4   5Gi        RWO            managed-premium   13h
logs-demo-mi-0        Bound     pvc-11836e5e-63e5-4620-a6ba-d74f7a916db4   5Gi        RWO            managed-premium   13h
```

### <a name="2-delete-each-of-the-pvcs"></a>2. Usuń wszystkie obwody PVC
Usuń dane i rejestry PVC dla każdego z usuniętych wystąpień zarządzanych przez program SQL Server.
Ogólny format tego polecenia to: 
```console
kubectl delete pvc <name of pvc>
```

Na przykład:
```console
kubectl delete pvc data-demo-mi-0 -n arc
kubectl delete pvc logs-demo-mi-0 -n arc
```

Każde z tych poleceń polecenia kubectl potwierdzi pomyślne usunięcie obwodu PVC. Na przykład:
```console
persistentvolumeclaim "data-demo-mi-0" deleted
persistentvolumeclaim "logs-demo-mi-0" deleted
```
  

> [!NOTE]
> Jak wskazano, nie należy usuwać obwodów PVC ostatecznie można uzyskać klaster Kubernetes w sytuacji, w której będzie zgłaszał błędy. Niektóre z tych błędów mogą polegać na tym, że nie można zalogować się do klastra Kubernetes z azdata, ponieważ z tego powodu problem z magazynem (normalne zachowanie Kubernetes) może zostać wykluczony.
>
> Można na przykład wyświetlić komunikaty w dziennikach podobne do następujących:  
> - Adnotacje: microsoft.com/ignore-pod-health: true  
> - Stan: niepowodzenie  
> - Przyczyna: wykluczone  
> - Komunikat: w węźle brakuje zasobu: tymczasowych magazynów. Kontroler kontenera używa 16372Ki, który przekracza jego żądanie równe 0.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [funkcjach i możliwościach wystąpienia zarządzanego przez usługę Azure Arc](managed-instance-features.md)

[Zacznij od utworzenia kontrolera danych](create-data-controller.md)

Utworzono już kontroler danych? [Utwórz wystąpienie zarządzane SQL z funkcją Azure Arc](create-sql-managed-instance.md)