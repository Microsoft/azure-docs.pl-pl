---
title: Utwórz skalowanie PostgreSQL z użyciem usługi Azure ARC przy użyciu Azure Data Studio
description: Utwórz skalowanie PostgreSQL z użyciem usługi Azure ARC przy użyciu Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: e2007d8f0c558d35c0507b6e12bce6d6777fad52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92310897"
---
# <a name="create-azure-arc-enabled-postgresql-hyperscale-using-azure-data-studio"></a>Utwórz skalowanie PostgreSQL z użyciem usługi Azure ARC przy użyciu Azure Data Studio

Ten dokument zawiera szczegółowe instrukcje dotyczące korzystania z Azure Data Studio w celu aprowizacji grup serwerów PostgreSQL na platformie Azure.

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connect-to-the-azure-arc-data-controller"></a>Nawiązywanie połączenia z kontrolerem danych usługi Azure Arc

Aby można było utworzyć wystąpienie, zaloguj się do kontrolera danych usługi Azure ARC, jeśli nie jest jeszcze zalogowany.

```console
azdata login
```

Następnie zostanie wyświetlony monit o przestrzeń nazw, w której utworzono kontroler danych, nazwę użytkownika i hasło, aby zalogować się do kontrolera.

> Jeśli musisz sprawdzić poprawność przestrzeni nazw, możesz uruchomić polecenie, ```kubectl get pods -A``` Aby uzyskać listę wszystkich przestrzeni nazw w klastrze.

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>Wstępny i tymczasowy krok dotyczący tylko użytkowników OpenShift

Przed przejściem do następnego kroku Zaimplementuj ten krok. Aby wdrożyć grupę serwerów PostgreSQL na platformie Red Hat OpenShift w projekcie innym niż domyślny, należy wykonać następujące polecenia w odniesieniu do klastra w celu zaktualizowania ograniczeń zabezpieczeń. To polecenie przyznaje odpowiednie uprawnienia do kont usług, które będą uruchamiać grupę serwerów PostgreSQL. Łuk ograniczenia kontekstu zabezpieczeń (SCC) **_— Data-SCC_** to ten, który został dodany podczas wdrażania kontrolera danych usługi Azure Arc.

```console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

_**Server-Group-Name** to nazwa grupy serwerów, która zostanie wdrożona w następnym kroku._
   
Aby uzyskać więcej informacji na temat SCCs w OpenShift, zapoznaj się z [dokumentacją OpenShift](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html).
Teraz można zaimplementować następny krok.

## <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Tworzenie grupy serwerów PostgreSQL w warstwie Hiperskala z obsługą usługi Azure Arc

1. Azure Data Studio uruchamiania
1. Na karcie połączenia kliknij trzy kropki w lewym górnym rogu i wybierz pozycję "nowe wdrożenie".
1. Z opcji wdrożenia wybierz pozycję **PostgreSQL Grupa serwerów — Azure Arc**
    >[!NOTE]
    > Może zostać wyświetlony monit o zainstalowanie tego programu, [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] Jeśli nie jest on aktualnie zainstalowany.
1. Zaakceptuj postanowienia dotyczące prywatności i licencji, a następnie kliknij pozycję **Wybierz** u dołu
1. W bloku Wdróż PostgreSQL grupy serwerów — Azure Arc wprowadź następujące informacje:
   - Wprowadź nazwę grupy serwerów
   - Wprowadź i Potwierdź hasło dla użytkownika administratora _Postgres_ grupy serwerów
   - Wybierz klasę magazynu odpowiednią dla danych
   - Wybierz klasę magazynu odpowiednią dla dzienników
   - Wybierz klasę magazynu odpowiednią dla kopii zapasowych
   - Wybierz liczbę węzłów procesu roboczego do aprowizacji
1. Kliknij przycisk **Wdróż**

Spowoduje to rozpoczęcie tworzenia grupy serwerów PostgreSQL na platformie Azure z włączonym skalowaniem na kontrolerze danych.

W ciągu kilku minut tworzenie powinno zakończyć się pomyślnie.

## <a name="next-steps"></a>Następne kroki
- [Zarządzanie grupą serwerów za pomocą Azure Data Studio](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md)
- [Monitorowanie grupy serwerów](monitor-grafana-kibana.md)
- Zapoznaj się z pojęciami i przewodnikami dotyczącymi Azure Database for PostgreSQL skalowania w celu rozproszenia danych między wieloma węzłami PostgreSQL. :
    * [Węzły i tabele](../../postgresql/concepts-hyperscale-nodes.md)
    * [Określanie typu aplikacji](../../postgresql/concepts-hyperscale-app-type.md)
    * [Wybieranie kolumny dystrybucji](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Kolokacja tabeli](../../postgresql/concepts-hyperscale-colocation.md)
    * [Rozpowszechnianie i modyfikowanie tabel](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Projektowanie bazy danych z wieloma dzierżawcami](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Projektowanie pulpitu nawigacyjnego analizy w czasie rzeczywistym](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \* W powyższych dokumentach Pomiń sekcje **Zaloguj się do Azure Portal**, & **Utwórz Azure Database for PostgreSQL-Citus**. Zaimplementuj pozostałe kroki w ramach wdrożenia usługi Azure Arc. Te sekcje są specyficzne dla Azure Database for PostgreSQL Citus) oferowanych jako usługa PaaS w chmurze platformy Azure, ale inne części dokumentów są bezpośrednio stosowane do wieloskalowania z włączonym systemem Azure Arc PostgreSQL.

- [Skalowanie w poziomie grupy serwerów usługi Azure Database for PostgreSQL — hiperskala](scale-out-postgresql-hyperscale-server-group.md)
- [Konfiguracja magazynu i pojęcia dotyczące magazynu Kubernetes](storage-configuration.md)
- [Model zasobów Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

