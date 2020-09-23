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
ms.openlocfilehash: 2bae661218989d49b74ed8ca3f694ccb912ef912
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939111"
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

### <a name="optionally-delete-the-azure-arc-data-controller-namespace"></a>Opcjonalnie Usuń przestrzeń nazw kontrolera danych usługi Azure Arc


```console
kubectl delete ns <nameSpecifiedDuringCreation>
# for example kubectl delete ns arc
```

## <a name="next-steps"></a>Następne kroki

[Co to są usługi danych z obsługą usługi Azure Arc?](overview.md)