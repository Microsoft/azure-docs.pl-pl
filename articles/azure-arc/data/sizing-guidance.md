---
title: Wskazówki dotyczące określania rozmiaru
description: Zaplanuj rozmiar wdrożenia usług danych z obsługą usługi Azure Arc.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 6e7f2e445c3e4e8df7420c0587e156968f3a2c92
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94542681"
---
# <a name="sizing-guidance"></a>Wskazówki dotyczące zmiany wielkości

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="overview-of-sizing-guidance"></a>Przegląd wskazówek dotyczących ustalania wielkości

Planując wdrożenie usługi Azure Arc Data Services, należy zaplanować poprawną ilość zasobów obliczeniowych, pamięci i magazynu, które będą wymagane do uruchomienia kontrolera danych usługi Azure ARC, oraz liczby grup wystąpień zarządzanych SQL i PostgreSQL, które zostaną wdrożone.  Ponieważ usługi danych z włączonym łukiem platformą Azure są wdrażane w systemie Kubernetes, istnieje elastyczność dodawania dodatkowej pojemności do klastra Kubernetes z upływem czasu przez dodanie dodatkowych węzłów obliczeniowych lub magazynu.  W tym przewodniku przedstawiono wskazówki dotyczące minimalnych wymagań, a także wskazówki dotyczące zalecanych rozmiarów niektórych typowych wymagań.

## <a name="general-sizing-requirements"></a>Ogólne wymagania dotyczące ustalania wielkości

> [!NOTE]
> Jeśli nie znasz koncepcji w tym artykule, możesz zapoznać się z tematem więcej informacji na temat [Kubernetesego zarządzania zasobami](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/) i [Kubernetes](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes).

Liczba rdzeni musi być liczbą całkowitą większą lub równą 1.

W przypadku używania azdata do wdrożenia wartości pamięci należy określić w potęgi dwóch liczb — tj. przy użyciu sufiksów: ki, mi lub gi.

Wartości graniczne muszą zawsze być większe niż wartość żądania, jeśli zostały określone.

Wartości graniczne dla rdzeni to metryka rozliczana w wystąpieniu zarządzanym SQL i grupy serwerów PostgreSQL.

## <a name="minimum-deployment-requirements"></a>Minimalne wymagania dotyczące wdrażania

Minimalny rozmiar wdrożenia usług danych z obsługą usługi Azure Arc może być uznawany za kontroler danych usługi Azure Arc i jedno wystąpienie zarządzane SQL oraz jedną grupę serwerów PostgreSQL z dwoma węzłami roboczymi.  W przypadku tej konfiguracji potrzebna jest co najmniej 16 GB pamięci RAM i 4 rdzenie _dostępnej_ pojemności w klastrze Kubernetes.  Upewnij się, że masz minimalny rozmiar węzła Kubernetes wynoszący 8 GB pamięci RAM i 4 rdzenie oraz łączną pojemność 16 GB pamięci RAM dostępne we wszystkich węzłach Kubernetes.  Można na przykład mieć 1 węzeł w 32 GB pamięci RAM i 4 rdzenie lub 2 węzły z 16GB pamięci RAM i 4 rdzenie.

Szczegółowe informacje na temat określania wielkości magazynu można znaleźć w artykule dotyczącym [magazynu — konfiguracja](storage-configuration.md) .

## <a name="data-controller-sizing-details"></a>Szczegóły zmiany wielkości kontrolera danych

Kontroler danych jest zbiorem zasobników wdrożonych w klastrze Kubernetes w celu udostępnienia interfejsu API, usługi kontrolera, programu inicjującego oraz baz danych i pulpitów nawigacyjnych monitorowania.  W tej tabeli opisano domyślne wartości żądań pamięci i procesora CPU oraz limity.

|Pod nazwą|Żądanie procesora CPU|Żądanie pamięci|Limit CPU|Limit pamięci|Uwagi|
|---|---|---|---|---|---|
|**program inicjujący**|100 mln|100Mi|200m|200Mi||
|**kontroli**|400m|2Gi|1800m|2Gi||
|**controldb**|200m|3Gi|800m|6Gi||
|**controlwd**|10 mln|100Mi|100 mln|200Mi||
|**logsdb**|200m|1600Mi|2|1600Mi||
|**logsui**|100 mln|500Mi|2|2Gi||
|**metricsdb**|200m|800Mi|400m|2Gi||
|**metricsdc**|100 mln|200Mi|200m|300Mi|Metricsdc to elementu daemonset, która jest tworzona na każdym z węzłów Kubernetes w klastrze.  Liczby w tabeli są w tym miejscu _na węzeł_. Jeśli ustawisz allowNodeMetricsCollection = false w pliku profilu wdrożenia przed utworzeniem kontrolera danych, metricsdc elementu daemonset nie zostanie utworzony.|
|**metricsui**|20 mln|200Mi|2500 mln|200Mi||
|**mgmtproxy**|200m|250Mi|2500 mln|500Mi||

Można zastąpić domyślne ustawienia dla controldb i kontrolek w pliku profilu wdrożenia lub pliku YAML kontrolera danych.  Przykład:

```yaml
  resources:
    controller:
      limits:
        cpu: "1000m"
        memory: "3Gi"
      requests:
        cpu: "800m"
        memory: "2Gi"
    controllerDb:
      limits:
        cpu: "800m"
        memory: "8Gi"
      requests:
        cpu: "200m"
        memory: "4Gi"
```

Szczegółowe informacje na temat określania wielkości magazynu można znaleźć w artykule dotyczącym [magazynu — konfiguracja](storage-configuration.md) .

## <a name="sql-managed-instance-sizing-details"></a>Szczegóły ustalania wielkości wystąpienia zarządzanego SQL

Każde wystąpienie zarządzane SQL musi mieć następujące minimalne żądania zasobów:
- Pamięć: 2Gi
- Rdzenie: 1

Każde utworzone wystąpienie zarządzane SQL ma trzy kontenery:

|Nazwa kontenera|Żądanie procesora CPU|Żądanie pamięci|Limit CPU|Limit pamięci|Uwagi|
|---|---|---|---|---|---|
|fluentbit|100 mln|100Mi|Nie określono|Nie określono|Żądania zasobów kontenera fluentbit są _uzupełnieniem_ żądań określonych dla wystąpienia zarządzanego SQL.||
|łuk — sqlmi|Określono lub nie określono użytkownika.|Określono lub nie określono użytkownika.|Określono lub nie określono użytkownika.|Określono lub nie określono użytkownika.||
|zebrane|Nie określono|Nie określono|Nie określono|Nie określono||

Domyślny rozmiar woluminu dla wszystkich woluminów trwałych to 5Gi.

## <a name="postgresql-hyperscale-server-group-sizing-details"></a>Szczegóły zmiany rozmiaru grupy serwerów PostgreSQL.

Każdy węzeł grupy serwerów ze skalą PostgreSQL musi mieć następujące minimalne żądania zasobów:
- Pamięć: 256Mi
- Rdzenie: 1

Każdy utworzony przez siebie koordynator grupy serwerów lub proces roboczy PostgreSQL ma trzy kontenery:

|Nazwa kontenera|Żądanie procesora CPU|Żądanie pamięci|Limit CPU|Limit pamięci|Uwagi|
|---|---|---|---|---|---|
|fluentbit|100 mln|100Mi|Nie określono|Nie określono|Żądania zasobów kontenera fluentbit są _uzupełnieniem_ żądań określonych dla węzłów grupy serwerów PostgreSQL ze skalowaniem.|
|postgres|Określono lub nie określono użytkownika.|Określony przez użytkownika lub 256Mi (domyślnie).|Określono lub nie określono użytkownika.|Określono lub nie określono użytkownika.||
|telegraf|Nie określono|Nie określono|Nie określono|Nie określono||

## <a name="cumulative-sizing"></a>Rozmiar skumulowany

Całkowity rozmiar środowiska wymaganego dla usług danych z obsługą usługi Azure Arc jest przede wszystkim funkcją liczby i rozmiaru wystąpień bazy danych, które zostaną utworzone.  Ogólny rozmiar może być trudny do przewidywania przed czasem, wiedząc, że liczba wystąpień zwiększy się i zmniejszy, a ilość zasobów wymaganych dla każdego wystąpienia bazy danych ulegnie zmianie.

Rozmiar linii bazowej dla danego środowiska usług danych z obsługą usługi Azure Arc jest rozmiarem kontrolera danych, który wymaga 4 rdzeni i 16 GB pamięci RAM.  Z tego miejsca można dodać na podstawie łącznej łącznej liczby rdzeni i pamięci wymaganej przez wystąpienia bazy danych.  W przypadku wystąpienia zarządzanego SQL liczba numerów jest równa liczbie utworzonych wystąpień zarządzanych przez program SQL Server.  W przypadku grup serwerów PostgreSQL w ramach skalowania liczba numerów jest równa liczbie węzłów procesu roboczego i jeden dla węzła koordynatora.  Na przykład, jeśli istnieje grupa serwerów PostgreSQL z 3 węzłami roboczymi, Łączna liczba zasobników wynosi 4.

Oprócz rdzeni i pamięci, którą poprosisz o każde wystąpienie bazy danych, należy dodać 250m rdzeni i 250Mi pamięci RAM dla kontenerów agentów.

Poniżej przedstawiono przykładowe Obliczanie wielkości.

Wymagania:

- **"SQL1"**: 1 wystąpienie zarządzane SQL z 16 GB pamięci RAM, 4 rdzeni
- **"Sql2"**: 1 wystąpienie zarządzane SQL z 256 GB pamięci RAM, 16 rdzeni
- **"Postgres1"**: 1 PostgreSQL grupy serwerów z 4 pracownikami w 12 GB pamięci RAM, 4 rdzeni

Obliczanie rozmiarów:

- Rozmiar "SQL1" to: 1 na * ([16 gi RAM, 4 rdzenie] + [250Mi pamięci RAM, rdzenie 250m]) dla agentów na mocy = 16,25 gi pamięci RAM, 4,25 rdzeni.
- Rozmiar "SQL2" to: 1 pod * ([256 gi RAM, 16 rdzeni] + + [250Mi pamięci RAM, rdzenie 250m]) dla agentów na mocy = 256,25 gi RAM, 16,25 rdzeni.
- Łączny rozmiar SQL 1 i SQL 2 to: (16,25 GB + 256,25 gi) = 272,5 GB pamięci RAM, (4,25 rdzeni + 16,25) = rdzenie 20,5.

- Rozmiar "Postgres1" to: (4 proces roboczy 1 i koordynator pod) * ([12 GB pamięci RAM, 4 rdzenie] + [250Mi pamięci RAM, rdzenie 250m]) dla agentów na 61,25 GB pamięci RAM, 21,25 rdzeni.

- Całkowita pojemność wymagana dla wystąpień bazy danych to: 272,5 GB pamięci RAM, 20,5 rdzeni dla SQL + 61,25 GB pamięci RAM 21,25, PostgreSQL rdzenie = 333,75 GB pamięci RAM, rdzenie 42,5.

- Całkowita pojemność wymagana dla wystąpień bazy danych i kontrolera danych to: 333,75 GB pamięci RAM, 42,5 rdzeni dla wystąpień bazy danych + 16 GB pamięci RAM, 4 rdzenie dla kontrolera danych = 349,75 GB pamięci RAM, rdzenie dla programu 46,5.

Szczegółowe informacje na temat określania wielkości magazynu można znaleźć w artykule dotyczącym [magazynu — konfiguracja](storage-configuration.md) .

## <a name="other-considerations"></a>Inne zagadnienia

Należy pamiętać, że określone żądanie rozmiaru wystąpienia bazy danych dla rdzeni lub pamięci RAM nie może przekroczyć dostępnej pojemności węzłów Kubernetes w klastrze.  Na przykład jeśli największy węzeł Kubernetes w klastrze Kubernetes to 256 GB pamięci RAM i 24 rdzenie, nie będzie można utworzyć wystąpienia bazy danych z żądaniem 512 GB pamięci RAM i 48 rdzeni.  

Dobrym pomysłem jest zachowanie co najmniej 25% dostępnej pojemności w węzłach Kubernetes, aby umożliwić Kubernetes wydajny harmonogram tworzenia i zapewnianie elastycznego skalowania i dłuższego wzrostu popytu na żądanie.  

W obliczeniach dotyczących rozmiarów nie zapomnij dodać w wymaganiach dotyczących zasobów Kubernetesowych i innych obciążeń, które mogą być współużytkowane z usługami danych usługi Azure Arc włączonymi w tym samym klastrze Kubernetes.

Aby zachować wysoką dostępność podczas planowanej konserwacji i ciągłości awarii, należy zaplanować co najmniej jeden węzeł Kubernetes w klastrze, aby był niedostępny w danym momencie.  Kubernetes spróbuje ponownie zaplanować wystąpienia, które były uruchomione w danym węźle, który został przełączony do trybu konserwacji lub z powodu błędu.  Jeśli nie ma dostępnej pojemności w pozostałych węzłach, te zasobniki nie będą ponownie planowane do utworzenia do momentu ponownego udostępnienia dostępnej pojemności.  Należy zachować szczególną ostrożność dzięki dużym wystąpieniu bazy danych.  Na przykład jeśli jeden węzeł Kubernetes jest wystarczająco duży, aby spełniał wymagania dotyczące zasobów dużego wystąpienia bazy danych i ten węzeł ulegnie awarii, Kubernetes nie będzie mógł zaplanować tego wystąpienia bazy danych na innym węźle Kubernetes.

Należy pamiętać o [maksymalnym limicie rozmiaru klastra Kubernetes](https://kubernetes.io/docs/setup/best-practices/cluster-large/) .

Administrator Kubernetes mógł skonfigurować [przydziały zasobów](https://kubernetes.io/docs/concepts/policy/resource-quotas/) dla przestrzeni nazw/projektu.  Należy pamiętać o tym, gdy planujesz rozmiary wystąpienia bazy danych.
