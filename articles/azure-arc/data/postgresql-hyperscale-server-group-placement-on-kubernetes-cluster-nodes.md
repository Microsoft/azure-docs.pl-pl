---
title: Umieszczanie grupy serwerów PostgreSQL w skali w węzłach klastra Kubernetes
description: Wyjaśnia, w jaki sposób wystąpienia PostgreSQL tworzące grupę serwerów PostgreSQL ze skalą są umieszczane w węzłach klastra Kubernetes
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 2b69eb076c727a4383b7459ef914ac79dca31c84
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91628421"
---
# <a name="azure-arc-enabled-postgresql-hyperscale-server-group-placement"></a>Rozmieszczenie grupy serwerów PostgreSQL na platformie Azure z włączonym łukiem

W tym artykule zajmiemy się przykładem, aby zilustrować, jak PostgreSQL wystąpienia usługi Azure ARC z włączoną grupą serwerów PostgreSQL do skalowania na węzły fizyczne klastra Kubernetes, który je obsługuje. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="configuration"></a>Konfiguracja

W tym przykładzie korzystamy z klastra usługi Azure Kubernetes Service (AKS), który ma cztery węzły fizyczne. 

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/1_cluster_portal.png" alt-text="4 węzeł klastra AKS w Azure Portal":::

Wyświetl listę fizycznych węzłów klastra Kubernetes, uruchamiając polecenie:

```console
kubectl get nodes
```

Który pokazuje cztery węzły fizyczne wewnątrz klastra Kubernetes:

```output
NAME                                STATUS   ROLES   AGE   VERSION
aks-agentpool-42715708-vmss000000   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000001   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000002   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000003   Ready    agent   11h   v1.17.9
```

Architektura może być reprezentowana jako:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/2_logical_cluster.png" alt-text="4 węzeł klastra AKS w Azure Portal":::

Klaster Kubernetes hostuje jeden kontroler danych usługi Azure Arc i jedną grupę serwerów z obsługą usługi Azure Arc PostgreSQL. Ta grupa serwerów została utworzona przez trzy wystąpienia PostgreSQL: jeden koordynator i dwóch procesów roboczych.

Utwórz listę zasobników za pomocą polecenia:

```console
kubectl get pods -n arc3
```
Co generuje następujące dane wyjściowe:

```output
NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01-0         3/3     Running   0          9h
postgres01-1         3/3     Running   0          9h
postgres01-2         3/3     Running   0          9h
```
Każdy z tych zasobników jest hostem wystąpienia PostgreSQL. Razem z usługą Azure Arc PostgreSQLą grupę serwerów z włączonym skalowaniem:

```output
Pod name    Role in the server group
postgres01-0            Coordinator
postgres01-1    Worker
postgres01-2    Worker
```

## <a name="placement"></a>Umieszczanie
Przyjrzyjmy się, jak Kubernetes umieszcza w grupie serwerów. Opisz każdy pod i określ, na którym węźle fizycznym klastra Kubernetes są one umieszczone. Na przykład dla koordynatora Uruchom następujące polecenie:

```console
kubectl describe pod postgres01-0 -n arc3
```

Co generuje następujące dane wyjściowe:

```output
Name:         postgres01-0
Namespace:    arc3
Priority:     0
Node:         aks-agentpool-42715708-vmss000000
Start Time:   Thu, 17 Sep 2020 00:40:33 -0700
…
```

Po uruchomieniu tego polecenia dla każdego z tych zasobników podsumowujemy bieżące umiejscowienie jako:

| Rola grupy serwerów|Grupa serwerów pod|Kubernetes węzeł fizyczny hostujący pod |
|--|--|--|
| Odpowiedzialn|postgres01-1|AKS-nieznanej obiektu agentpool-42715708-vmss000002 |
| Odpowiedzialn|postgres01-2|AKS-nieznanej obiektu agentpool-42715708-vmss000003 |

Należy również pamiętać, że w opisie wartości nazwanych kontenerów, które znajdują się w poszczególnych hostach. Na przykład dla drugiego procesu roboczego Uruchom następujące polecenie:

```console
kubectl describe pod postgres01-2 -n arc3
```

Co generuje następujące dane wyjściowe:

```output
…
Node:         aks-agentpool-42715708-vmss000003/10.240.0.7
..
Containers:
  Fluentbit:
…
  Postgres:
…
  Telegraf:
…
```

Każdy element, który jest częścią usługi Azure ARC z włączoną obsługą PostgreSQL w ramach grupy serwerów, obsługuje następujące trzy kontenery:

|Containers|Opis
|----|----|
|`Fluentbit` |Dane * moduł zbierający dzienniki: https://fluentbit.io/
|`Postgres`|Część PostgreSQL wystąpienia usługi Azure ARC z włączoną grupą serwerów PosgreSQL do skalowania
|`Telegraf` |Moduł zbierający metryk: https://www.influxdata.com/time-series-platform/telegraf/

Architektura wygląda następująco:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/3_pod_placement.png" alt-text="4 węzeł klastra AKS w Azure Portal":::

Oznacza to, że w tym momencie każde wystąpienie PostgreSQL tworzące grupę serwerów PostgreSQL z funkcją Azure Arc jest hostowane na określonym hoście fizycznym w kontenerze Kubernetes. Jest to Najlepsza konfiguracja, która pomaga w uzyskaniu największej wydajności z usługi Azure ARC z włączoną obsługą PostgreSQL, ponieważ każda rola (koordynator i procesy robocze) korzysta z zasobów każdego węzła fizycznego. Te zasoby nie są współużytkowane przez kilka ról PostgreSQL.

## <a name="scale-out-azure-arc-enabled-postgresql-hyperscale"></a>Skalowanie w poziomie usługi Azure ARC z włączonym skalowaniem PostgreSQL

Teraz przejdźmy do skalowania w poziomie, aby dodać trzeci węzeł procesu roboczego do grupy serwerów i obserwować, co się dzieje. Zostanie utworzone czwarte wystąpienie PostgreSQL, które będzie hostowane w czwartym pod.
Aby skalować w poziomie, uruchom polecenie:

```console
azdata arc postgres server edit --name postgres01 --workers 3
```

Generuje następujące dane wyjściowe:

```output
Updating postgres01 in namespace `arc3`
postgres01 is Ready
```

Utwórz listę grup serwerów wdrożonych w kontrolerze danych usługi Azure Arc i sprawdź, czy Grupa serwerów działa teraz z trzema pracownikami. Uruchom polecenie:

```console
azdata arc postgres server list
```

I należy zauważyć, że przeprowadzono skalowanie od dwóch procesów roboczych do trzech procesów roboczych:

```output
Name        State    Workers
----------  -------  ---------
postgres01  Ready    3
```

Tak jak wcześniej, zwróć uwagę na to, że Grupa serwerów używa teraz łącznie czterech zasobników:

```console
kubectl get pods -n arc3
```

```output
NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01-0         3/3     Running   0          11h
postgres01-1         3/3     Running   0          11h
postgres01-2         3/3     Running   0          11h
postgres01-3         3/3     Running   0          5m2s
```

I opisz nowy pod, aby określić, na których węzłach fizycznych klastra Kubernetes jest hostowany.
Uruchom polecenie:

```console
kubectl describe pod postgres01-3 -n arc3
```

Aby zidentyfikować nazwę węzła hostingu:

```output
Name:         postgres01-3
Namespace:    arc3
Priority:     0
Node:         aks-agentpool-42715708-vmss000000
```

Umieszczanie wystąpień PostgreSQL w węzłach fizycznych klastra jest teraz:

|Rola grupy serwerów|Grupa serwerów pod|Kubernetes węzeł fizyczny hostujący pod
|-----|-----|-----
|Obiekt|postgres01-0|AKS-nieznanej obiektu agentpool-42715708-vmss000000
|Odpowiedzialn|postgres01-1|AKS-nieznanej obiektu agentpool-42715708-vmss000002
|Odpowiedzialn|postgres01-2|AKS-nieznanej obiektu agentpool-42715708-vmss000003
|Odpowiedzialn|postgres01-3|AKS-nieznanej obiektu agentpool-42715708-vmss000000

Należy zauważyć, że pod nowym pracownikiem (postgres01-3) został umieszczony w tym samym węźle co koordynator. 

Architektura wygląda następująco:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/4_pod_placement_.png" alt-text="4 węzeł klastra AKS w Azure Portal":::

Dlaczego nowy proces roboczy/pod nie znajduje się w pozostałym węźle fizycznym klastra Kubernetes AKS-nieznanej obiektu agentpool-42715708-vmss000003?

Przyczyną jest to, że ostatni fizyczny węzeł klastra Kubernetes w rzeczywistości obsługuje kilka zasobników, które obsługują dodatkowe składniki wymagane do uruchamiania usług danych z obsługą usługi Azure Arc. Kubernetes ocenił, że najlepszy kandydat — w czasie planowania — w celu hostowania dodatkowego procesu roboczego jest węzłem fizycznym AKS-nieznanej obiektu agentpool-42715708-vmss000000. 

Przy użyciu tych samych poleceń jak powyżej; widzimy, co każdy węzeł fizyczny jest hostem:

|Inne nazwy na podst.\* |Użycie|Kubernetes węzeł fizyczny hostujący zasobniki
|----|----|----
|program inicjujący — jh48b|Jest to usługa, która obsługuje żądania przychodzące do tworzenia, edytowania i usuwania zasobów niestandardowych, takich jak wystąpienia zarządzane SQL, grupy serwerów PostgreSQL w skali i kontrolery danych|AKS-nieznanej obiektu agentpool-42715708-vmss000003
|Control-gwmbs||AKS-nieznanej obiektu agentpool-42715708-vmss000002
|controldb-0|Jest to magazyn danych kontrolera, który jest używany do przechowywania konfiguracji i stanu dla kontrolera danych.|AKS-nieznanej obiektu agentpool-42715708-vmss000001
|controlwd-zzjp7|Jest to usługa "Watch Dog", która utrzymuje dostęp do kontrolera danych.|AKS-nieznanej obiektu agentpool-42715708-vmss000000
|logsdb-0|Jest to elastyczne wystąpienie wyszukiwania, które służy do przechowywania wszystkich dzienników zebranych przez wszystkie zbiory danych Arc. Elasticsearch, odbiera dane z `Fluentbit` kontenera każdego z nich|AKS-nieznanej obiektu agentpool-42715708-vmss000003
|logsui-5fzv5|Jest to wystąpienie Kibana, które znajduje się na wierzchu bazy danych wyszukiwania elastycznego, aby przedstawić graficzny interfejs użytkownika usługi log Analytics.|AKS-nieznanej obiektu agentpool-42715708-vmss000003
|metricsdb-0|Jest to wystąpienie InfluxDB, które jest używane do przechowywania wszystkich metryk zbieranych na wszystkich serwerach Arc Data Services. InfluxDB, odbiera dane z `Telegraf` kontenera każdego pod|AKS-nieznanej obiektu agentpool-42715708-vmss000000
|metricsdc-47d47|Jest to elementu daemonset wdrożony we wszystkich węzłach Kubernetes w klastrze, aby zbierać metryki na poziomie węzła dotyczące węzłów.|AKS-nieznanej obiektu agentpool-42715708-vmss000002
|metricsdc-864kj|Jest to elementu daemonset wdrożony we wszystkich węzłach Kubernetes w klastrze, aby zbierać metryki na poziomie węzła dotyczące węzłów.|AKS-nieznanej obiektu agentpool-42715708-vmss000001
|metricsdc-l8jkf|Jest to elementu daemonset wdrożony we wszystkich węzłach Kubernetes w klastrze, aby zbierać metryki na poziomie węzła dotyczące węzłów.|AKS-nieznanej obiektu agentpool-42715708-vmss000003
|metricsdc-nxm4l|Jest to elementu daemonset wdrożony we wszystkich węzłach Kubernetes w klastrze, aby zbierać metryki na poziomie węzła dotyczące węzłów.|AKS-nieznanej obiektu agentpool-42715708-vmss000000
|metricsui-4fb7l|Jest to wystąpienie Grafana, które znajduje się na bazie danych InfluxDB, aby przedstawić graficzny interfejs użytkownika monitorowania.|AKS-nieznanej obiektu agentpool-42715708-vmss000003
|mgmtproxy-4qppp|Jest to warstwa serwera proxy aplikacji sieci Web, która znajduje się przed wystąpieniami Grafana i Kibana.|AKS-nieznanej obiektu agentpool-42715708-vmss000002

> \* Sufiks pod nazwami pod nazw różni się w zależności od innych wdrożeń. Ponadto wymieniamy tutaj tylko te, które są hostowane w przestrzeni nazw Kubernetes kontrolera danych usługi Azure Arc.

Architektura wygląda następująco:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/5_full_list_of_pods.png" alt-text="4 węzeł klastra AKS w Azure Portal":::

Oznacza to, że węzły koordynatora (1) z włączoną grupą Postgres z systemem Azure Arc współużytkują te same zasoby fizyczne, co trzeci węzeł procesu roboczego (pod 4) grupy serwerów. Jest to akceptowalne w przypadku, gdy węzeł koordynator zazwyczaj używa bardzo małych zasobów w porównaniu z tym, co może być używane przez węzeł procesu roboczego. W tym celu można wywnioskować, że należy starannie wybrać:
- rozmiar klastra Kubernetes i właściwości każdego z jego węzłów fizycznych (pamięć, rdzeń wirtualny)
- Liczba węzłów fizycznych w klastrze Kubernetes
- aplikacje lub obciążenia, które są hostowane w klastrze Kubernetes.

Skutkiem hostingu zbyt wielu obciążeń w klastrze Kubernetes może być ograniczenie przepustowości dla grupy serwerów PostgreSQL w skali usługi Azure Arc. W takim przypadku nie będzie można korzystać z tej funkcji w celu skalowania w poziomie. Wydajność, którą wykorzystasz z systemu, nie wystarczy na umieszczenie lub fizyczne właściwości węzłów fizycznych lub systemu magazynu. Dostępna jest także informacja o sposobie konfigurowania każdego z zasobów uruchomionych w klastrze Kubernetes (w tym Azure ARC z włączonym skalowaniem PostgreSQL), na przykład w przypadku żądań i limitów ustawionych dla pamięci i rdzeń wirtualny. Ilość obciążeń, które można hostować w danym klastrze Kubernetes, jest określana względem właściwości klastra Kubernetes, rodzaju obciążeń, liczby użytkowników, sposobu wykonywania operacji klastra Kubernetes...

## <a name="scale-out-aks"></a>Skalowanie w poziomie AKS

Pokażmy, że skalowanie w poziomie zarówno dla klastra AKS, jak i z włączonym PostgreSQLem usługi Azure Arc jest najlepszym sposobem, aby skorzystać z wysokiej wydajności usługi Azure ARC z włączonym skalowaniem PostgreSQL.
Dodajmy piąty węzeł do klastra AKS:

:::row:::
    :::column:::
        Stary adres
    :::column-end:::
    :::column:::
        Nowy adres
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/6_layout_before.png" alt-text="4 węzeł klastra AKS w Azure Portal":::
    :::column-end:::
    :::column:::
        :::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/7_layout_after.png" alt-text="4 węzeł klastra AKS w Azure Portal":::
    :::column-end:::
:::row-end:::

Architektura wygląda następująco:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/8_logical_layout_after.png" alt-text="4 węzeł klastra AKS w Azure Portal":::

Przyjrzyjmy się tym, jakie elementy obszaru nazw danych Arc są hostowane w nowym węźle fizycznym AKS, uruchamiając polecenie:

```console
kubectl describe node aks-agentpool-42715708-vmss000004
```

Następnie zaktualizujemy reprezentację architektury naszego systemu:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/9_updated_list_of_pods.png" alt-text="4 węzeł klastra AKS w Azure Portal":::

Można zauważyć, że nowy fizyczny węzeł klastra Kubernetes obsługuje tylko te metryki, które są niezbędne dla usług Azure Arc Data Services. Należy zauważyć, że w tym przykładzie koncentrujemy się tylko na przestrzeni nazw kontrolera danych ARC, ale nie reprezentujemy innych zasobników.

## <a name="scale-out-azure-arc-enabled-postgresql-hyperscale-again"></a>Skalowanie w poziomie usługi Azure ARC z włączonym skalowaniem PostgreSQL

Piąty węzeł fizyczny nie obsługuje jeszcze żadnego obciążenia. W miarę skalowania w poziomie usługi Azure ARC z włączonym skalowaniem PostgreSQL Kubernetes zoptymalizuje rozmieszczenie nowych PostgreSQL pod kątem i nie należy jej przyłączyć do węzłów fizycznych, które już obsługują więcej obciążeń. Uruchom następujące polecenie, aby skalować PostgreSQL z 3 do 4 procesów roboczych z włączonym skalowaniem platformy Azure. Po zakończeniu operacji Grupa serwerów zostanie utworzona i rozłożona na pięć wystąpień PostgreSQL, jednego koordynatora i czterech procesów roboczych.

```console
azdata arc postgres server edit --name postgres01 --workers 4
```

Generuje następujące dane wyjściowe:

```output
Updating postgres01 in namespace `arc3`
postgres01 is Ready
```

Utwórz listę grup serwerów wdrożonych w kontrolerze danych i sprawdź, czy Grupa serwerów działa teraz z czterema pracownikami:

```console
azdata arc postgres server list
```

I należy zauważyć, że przeprowadzono skalowanie od trzech do czterech procesów roboczych. 

```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    4
```

Tak jak wcześniej, obserwuj, że Grupa serwerów korzysta teraz z czterech zasobników:

```output
kubectl get pods -n arc3

NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01-0         3/3     Running   0          13h
postgres01-1         3/3     Running   0          13h
postgres01-2         3/3     Running   0          13h
postgres01-3         3/3     Running   0          179m
postgres01-4         3/3     Running   0          3m13s
```

Kształt grupy serwerów jest teraz:

|Rola grupy serwerów|Grupa serwerów pod
|----|-----
|Obiekt|postgres01-0
|Odpowiedzialn|postgres01-1
|Odpowiedzialn|postgres01-2
|Odpowiedzialn|postgres01-3
|Odpowiedzialn|postgres01-4

Opiszmy postgres01-4 pod, aby określić, w jakim węźle fizycznym jest hostowany:

```console
kubectl describe pod postgres01-4 -n arc3
```

I zapoznaj się z informacjami na temat tego, co uruchamia:

|Rola grupy serwerów|Grupa serwerów pod| Pod
|----|-----|------
|Obiekt|postgres01-0|AKS-nieznanej obiektu agentpool-42715708-vmss000000
|Odpowiedzialn|postgres01-1|AKS-nieznanej obiektu agentpool-42715708-vmss000002
|Odpowiedzialn|postgres01-2|AKS-nieznanej obiektu agentpool-42715708-vmss000003
|Odpowiedzialn|postgres01-3|AKS-nieznanej obiektu agentpool-42715708-vmss000000
|Odpowiedzialn|postgres01-4|AKS-nieznanej obiektu agentpool-42715708-vmss000004

Architektura wygląda następująco:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/10_kubernetes_schedules_newest_pod.png" alt-text="4 węzeł klastra AKS w Azure Portal":::

Kubernetes zaplanował nowy PostgreSQL w najmniej załadowanym fizycznym węźle klastra Kubernetes.

## <a name="summary"></a>Podsumowanie

Aby skorzystać z zalet skalowalności i wydajności skalowania grupy serwerów z obsługą usługi Azure Arc w poziomie, należy uniknąć rywalizacji o zasoby w klastrze Kubernetes:
- między usługą Azure ARC z włączoną grupą PostgreSQL i innymi obciążeniami obsługiwanymi w tym samym klastrze Kubernetes
- między wszystkimi wystąpieniami PostgreSQL, które stanowią grupę serwerów z włączoną funkcją Azure Arc PostgreSQL

Można to osiągnąć na kilka sposobów:
- Skalowanie w poziomie zarówno Kubernetes, jak i platformy Azure z włączonym skalowaniem Postgres: Rozważ przeskalowanie do wewnątrz klastra Kubernetes w taki sam sposób, jak w przypadku skalowania grupy serwerów w ramach usługi Azure Arc w systemie PostgreSQL. Dodaj węzeł fizyczny do klastra dla każdego procesu roboczego, który zostanie dodany do grupy serwerów.
- Skalowanie w poziomie przy użyciu usługi Azure ARC z włączonym skalowaniem Postgres bez skalowania Kubernetesu: poprzez ustawienie odpowiednich ograniczeń zasobów (żądanie i limity pamięci i rdzeń wirtualny) w obciążeniach hostowanych w Kubernetes (usługa Azure ARC z włączonym wieloskalowaniem) umożliwia włączenie wspólnej lokalizacji obciążeń na PostgreSQL i zmniejszenie ryzyka rywalizacji o zasoby. Należy upewnić się, że fizyczne właściwości fizycznych węzłów klastra Kubernetes mogą honorować zdefiniowane ograniczenia zasobów. Należy również upewnić się, że równowaga pozostanie w miarę rozwoju obciążeń w miarę upływu czasu, lub gdy w klastrze Kubernetes są dodawane większe obciążenia.
- Użyj mechanizmów Kubernetes (pod selektorem, koligacja, ochrona przed koligacją), aby wpływać na rozmieszczenie zasobników.

## <a name="next-steps"></a>Następne kroki

[Skalowanie w poziomie grupy serwerów PostgreSQL na platformie Azure](scale-out-postgresql-hyperscale-server-group.md)
