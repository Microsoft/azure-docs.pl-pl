---
title: Obsługiwane wersje Postgres z usługą Azure ARC z włączonym skalowaniem PostgreSQL
description: Obsługiwane wersje Postgres z usługą Azure ARC z włączonym skalowaniem PostgreSQL
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 0f3f9e7f4566800bdea6871cae1c5bafb3b37b95
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90939186"
---
# <a name="supported-versions-of-postgres-with-azure-arc-enabled-postgresql-hyperscale"></a>Obsługiwane wersje bazy danych Postgres w przypadku bazy danych PostgreSQL w warstwie Hiperskala z obsługą usługi Azure Arc

W tym artykule wyjaśniono, jakie wersje programu Postgres są dostępne z funkcją Azure ARC z włączonym skalowaniem PostgreSQL.
Lista obsługiwanych wersji jest zmieniana w miarę upływu czasu. Obecnie wersje główne, które są obsługiwane, to:
- Postgres 12 (wartość domyślna)
- Postgres 11

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="how-to-chose-between-versions"></a>Jak wybrać wersje?
Zaleca się, aby sprawdzić, do jakich wersji zostały zaprojektowane aplikacje i jakie są możliwości każdej z tych wersji. Aby dowiedzieć się więcej, Przeczytaj o każdej wersji w oficjalnej witrynie Postgres:
- [Postgres 12 (wartość domyślna)](https://www.postgresql.org/docs/12/index.html)
- [Postgres 11](https://www.postgresql.org/docs/11/index.html)

## <a name="how-to-create-a-particular-version-in-azure-arc-enabled-postgresql-hyperscale"></a>Jak utworzyć określoną wersję usługi Azure ARC z włączonym skalowaniem PostgreSQL?
W czasie tworzenia można wskazać, którą wersję utworzyć, przekazując parametr _--Engine-Version_ . Jeśli nie wskazano informacji o wersji, domyślnie zostanie utworzona grupa serwerów Postgres w wersji 12.

## <a name="how-do-be-notified-when-other-versions-are-available"></a>Jak ma być powiadamiany, gdy są dostępne inne wersje?
Wróć i przeczytaj ten artykuł. Zostanie ona zaktualizowana odpowiednio do potrzeb. Możesz również wyświetlić listę rodzajów niestandardowych definicji zasobów (CRD) w kontrolerze danych Arc w klastrze Kubernetes.
Uruchom następujące polecenie:
```console
kubectl get crds
```

Zwróci dane wyjściowe podobne do:
```console
NAME                                            CREATED AT
datacontrollers.arcdata.microsoft.com           2020-08-31T20:15:16Z
postgresql-11s.arcdata.microsoft.com            2020-08-31T20:15:16Z
postgresql-12s.arcdata.microsoft.com            2020-08-31T20:15:16Z
sqlmanagedinstances.sql.arcdata.microsoft.com   2020-08-31T20:15:16Z
```

W tym przykładzie dane wyjściowe wskazują, że istnieją dwa rodzaje CRD związanych z Postgres:
- postgresql-12s.arcdata.microsoft.com to CRD dla Postgres 12
- postgresql-11s.arcdata.microsoft.com jest CRD dla Postgres 11

Są to CRDs, a nie grupy serwerów. Obecność elementu CRD nie wskazuje, że masz lub nie utworzono grupy serwerów tej wersji.
CRD to wskazanie rodzaju zasobów, które można utworzyć.

## <a name="next-steps"></a>Następne kroki:
- [Przeczytaj o tworzeniu PostgreSQL](create-postgresql-hyperscale-server-group.md)
- [Zapoznaj się z informacjami na temat pobierania listy grup serwerów z obsługą usługi Azure Arc PostgreSQL, które utworzono w Twoim kontrolerze danych Arc](list-server-groups-postgres-hyperscale.md)
