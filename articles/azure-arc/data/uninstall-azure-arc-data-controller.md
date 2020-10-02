---
title: Usuń kontroler danych usługi Azure Arc
description: Usuń kontroler danych usługi Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a040200c5746defcaee84a951521d5919c0c4d28
ms.sourcegitcommit: 487a9f5272300d60df2622c3d13e794d54680f90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91660680"
---
# <a name="delete-azure-arc-data-controller"></a>Usuń kontroler danych usługi Azure Arc

W tym artykule opisano sposób usuwania kontrolera danych usługi Azure Arc.

Przed kontynuowaniem upewnij się, że wszystkie usługi danych utworzone na kontrolerze danych zostaną usunięte w następujący sposób:

## <a name="log-in-to-the-data-controller"></a>Zaloguj się do kontrolera danych

Zaloguj się do kontrolera danych, który chcesz usunąć:

```
azdata login
```

## <a name="list--delete-existing-data-services"></a>Lista & Usuń istniejące usługi danych

Uruchom następujące polecenie, aby sprawdzić, czy istnieją utworzone wystąpienia zarządzane przez program SQL:

```
azdata arc sql mi list
```

Dla każdego wystąpienia zarządzanego SQL z powyższej listy Uruchom polecenie Delete w następujący sposób:

```
azdata arc sql mi delete -n <name>
# for example: azdata arc sql mi delete -n sqlinstance1
```

Podobnie aby sprawdzić wystąpienia PostgreSQL, uruchom następujące polecenie:

```
azdata arc postgres server list
```

I dla każdego wystąpienia PostgreSQL należy uruchomić polecenie Delete w następujący sposób:
```
azdata arc postgres server delete -n <name>
# for example: azdata arc postgres server delete -n pg1
```

## <a name="delete-controller"></a>Usuń kontroler

Po usunięciu wszystkich wystąpień zarządzanych SQL i wystąpień PostgreSQL można usunąć kontroler danych w następujący sposób:

```
azdata arc dc delete -n <name> -ns <namespace>
# for example: azdata arc dc delete -ns arc -n arcdc
```

### <a name="remove-sccs-red-hat-openshift-only"></a>Usuń SCCs (tylko Red Hat OpenShift)

```console
oc adm policy remove-scc-from-user privileged -z default -n arc
oc adm policy remove-scc-from-user anyuid     -z default -n arc
```

### <a name="delete-cluster-level-objects"></a>Usuwanie obiektów poziomu klastra

Oprócz obiektów o zakresie przestrzeni nazw, jeśli chcesz również usunąć obiekty poziomu klastra, takie jak CRDs, `clusterroles` i `clusterrolebindings` Uruchom następujące polecenia:

```console
# Cleanup azure arc data service artifacts
#Delete CRDs
kubectl delete crd datacontrollers.arcdata.microsoft.com 
kubectl delete crd sqlmanagedinstances.sql.arcdata.microsoft.com 
kubectl delete crd postgresql-11s.arcdata.microsoft.com 
kubectl delete crd postgresql-12s.arcdata.microsoft.com
#Delete clusterrole
kubectl delete clusterrole <namespace>:cr-arc-metricsdc-reader
#For example: kubectl delete clusterrole arc:cr-arc-metricsdc-reader
#Delete rolebinding
kubectl delete clusterrolebinding <namespace>:crb-arc-metricsdc-reader
#For example: kubectl delete clusterrolebinding arc:crb-arc-metricsdc-reader
```

### <a name="optionally-delete-the-azure-arc-data-controller-namespace"></a>Opcjonalnie Usuń przestrzeń nazw kontrolera danych usługi Azure Arc


```console
kubectl delete ns <nameSpecifiedDuringCreation>
# for example: kubectl delete ns arc
```

## <a name="next-steps"></a>Następne kroki

[Co to są usługi danych z włączonym usługą Azure Arc?](overview.md)
