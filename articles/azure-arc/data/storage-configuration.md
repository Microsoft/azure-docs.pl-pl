---
title: Konfiguracja usługi Storage
description: Objaśnia opcje konfiguracji magazynu usługi Azure ARC z obsługą danych
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: uc-msft
ms.author: umajay
ms.reviewer: mikeray
ms.date: 10/12/2020
ms.topic: conceptual
ms.openlocfilehash: 7b683029b7fd05078755d4e8cd027f55c805f991
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97107264"
---
# <a name="storage-configuration"></a>Konfiguracja usługi Storage

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="kubernetes-storage-concepts"></a>Pojęcia dotyczące magazynu Kubernetes

Kubernetes zapewnia warstwę abstrakcji infrastruktury na podstawowym stosie technicznym wirtualizacji (opcjonalnie) i sprzęcie. Sposób, w jaki Kubernetes jest abstrakcją magazynu, odbywa się za pomocą **[klas magazynu](https://kubernetes.io/docs/concepts/storage/storage-classes/)**. W chwili aprowizacji elementu pod, Klasa magazynu może być określona do użycia dla każdego woluminu. W czasie, gdy jest to możliwe, Zainicjowano obsługę klasy magazynu, aby **[zainicjować obsługę magazynu](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/)** , a następnie tworzony jest **[wolumin trwały](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)** dla tego magazynu, a następnie na woluminie trwałym jest on instalowany w ramach **[trwałego żądania woluminu](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims)**.

Kubernetes umożliwia dostawcom infrastruktury magazynu podłączenie sterowników (nazywanych również "wtyczkami"), które zwiększają Kubernetes. Dodatki magazynu muszą być zgodne ze **[standardem interfejsu magazynu kontenerów](https://kubernetes.io/blog/2019/01/15/container-storage-interface-ga/)**. Istnieją dziesiątki dodatków, które można znaleźć w tej nieostatecznej **[liście sterowników CSI](https://kubernetes-csi.github.io/docs/drivers.html)**. Używany sterownik CSI będzie zależeć od takich czynników, jak w przypadku usługi w chmurze, zarządzanej Kubernetes lub dostawcy OEM używanym dla danego sprzętu.

Aby wyświetlić klasy magazynu, które są skonfigurowane w klastrze Kubernetes, należy uruchomić następujące polecenie:

```console
kubectl get storageclass
```

Przykładowe dane wyjściowe z klastra usługi Azure Kubernetes Service (AKS):

```console
NAME                PROVISIONER                AGE
azurefile           kubernetes.io/azure-file   15d
azurefile-premium   kubernetes.io/azure-file   15d
default (default)   kubernetes.io/azure-disk   4d3h
managed-premium     kubernetes.io/azure-disk   4d3h
```

Aby uzyskać szczegółowe informacje o klasie magazynu, należy uruchomić następujące polecenie:

```console
kubectl describe storageclass/<storage class name>
```

Przykład:

```console
kubectl describe storageclass/azurefile

Name:            azurefile
IsDefaultClass:  No
Annotations:     kubectl.kubernetes.io/last-applied-configuration={"allowVolumeExpansion":true,"apiVersion":"storage.k8s.io/v1beta1","kind":"StorageClass","metadata":{"annotations":{},"labels":{"kubernetes.io/cluster-service":"true"},"name":"azurefile"},"parameters":{"sku
Name":"Standard_LRS"},"provisioner":"kubernetes.io/azure-file"}

Provisioner:           kubernetes.io/azure-file
Parameters:            skuName=Standard_LRS
AllowVolumeExpansion:  True
MountOptions:          <none>
ReclaimPolicy:         Delete
VolumeBindingMode:     Immediate
Events:                <none>
```

Obecnie zainicjowane woluminy trwałe i trwałe oświadczenia woluminu są wyświetlane, uruchamiając następujące polecenia:

```console
kubectl get persistentvolumes -n <namespace>

kubectl get persistentvolumeclaims -n <namespace>
```

Przykład przedstawiania woluminów trwałych:

```console

kubectl get persistentvolumes -n arc

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                      STORAGECLASS   REASON   AGE
pvc-07fc7b9f-9a37-4796-9442-4405147120da   15Gi       RWO            Delete           Bound    arc/sqldemo11-data-claim   default                 7d3h
pvc-3e772f20-ed89-4642-b34d-8bb11b088afa   15Gi       RWO            Delete           Bound    arc/data-metricsdb-0       default                 7d14h
pvc-41b33bbd-debb-4153-9a41-02ce2bf9c665   10Gi       RWO            Delete           Bound    arc/sqldemo11-logs-claim   default                 7d3h
pvc-4ccda3e4-fee3-4a89-b92d-655c04fa62ad   15Gi       RWO            Delete           Bound    arc/data-controller        default                 7d14h
pvc-63e6bb4c-7240-4de5-877e-7e9ea4e49c91   10Gi       RWO            Delete           Bound    arc/logs-controller        default                 7d14h
pvc-8a1467fe-5eeb-4d73-b99a-f5baf41eb493   10Gi       RWO            Delete           Bound    arc/logs-metricsdb-0       default                 7d14h
pvc-8e2cacbc-e953-4901-8591-e77df9af309c   10Gi       RWO            Delete           Bound    arc/sqldemo10-logs-claim   default                 7d14h
pvc-9fb79ba3-bd3e-42aa-aa09-3090135d4513   15Gi       RWO            Delete           Bound    arc/sqldemo10-data-claim   default                 7d14h
pvc-a39c85d4-5cd9-4249-9915-68a70a9bb5e5   15Gi       RWO            Delete           Bound    arc/data-controldb         default                 7d14h
pvc-c9cbd74a-76ca-4be5-b598-0c7a45749bfb   10Gi       RWO            Delete           Bound    arc/logs-controldb         default                 7d14h
pvc-d576e9d4-0a09-4dd7-b806-be8ed461f8a4   10Gi       RWO            Delete           Bound    arc/logs-logsdb-0          default                 7d14h
pvc-ecd7d07f-2c2c-421d-98d7-711ec5d4a0cd   15Gi       RWO            Delete           Bound    arc/data-logsdb-0          default                 7d14h
```

Przykład przedstawiania trwałych oświadczeń woluminu:

```console

kubectl get persistentvolumeclaims -n arc

NAME                   STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
data-controldb         Bound    pvc-a39c85d4-5cd9-4249-9915-68a70a9bb5e5   15Gi       RWO            default        7d14h
data-controller        Bound    pvc-4ccda3e4-fee3-4a89-b92d-655c04fa62ad   15Gi       RWO            default        7d14h
data-logsdb-0          Bound    pvc-ecd7d07f-2c2c-421d-98d7-711ec5d4a0cd   15Gi       RWO            default        7d14h
data-metricsdb-0       Bound    pvc-3e772f20-ed89-4642-b34d-8bb11b088afa   15Gi       RWO            default        7d14h
logs-controldb         Bound    pvc-c9cbd74a-76ca-4be5-b598-0c7a45749bfb   10Gi       RWO            default        7d14h
logs-controller        Bound    pvc-63e6bb4c-7240-4de5-877e-7e9ea4e49c91   10Gi       RWO            default        7d14h
logs-logsdb-0          Bound    pvc-d576e9d4-0a09-4dd7-b806-be8ed461f8a4   10Gi       RWO            default        7d14h
logs-metricsdb-0       Bound    pvc-8a1467fe-5eeb-4d73-b99a-f5baf41eb493   10Gi       RWO            default        7d14h
sqldemo10-data-claim   Bound    pvc-9fb79ba3-bd3e-42aa-aa09-3090135d4513   15Gi       RWO            default        7d14h
sqldemo10-logs-claim   Bound    pvc-8e2cacbc-e953-4901-8591-e77df9af309c   10Gi       RWO            default        7d14h
sqldemo11-data-claim   Bound    pvc-07fc7b9f-9a37-4796-9442-4405147120da   15Gi       RWO            default        7d4h
sqldemo11-logs-claim   Bound    pvc-41b33bbd-debb-4153-9a41-02ce2bf9c665   10Gi       RWO            default        7d4h

```

## <a name="factors-to-consider-when-choosing-your-storage-configuration"></a>Czynniki, które należy wziąć pod uwagę podczas wybierania konfiguracji magazynu

Wybór odpowiedniej klasy magazynu jest istotny dla odporności i wydajności danych. Wybranie niewłaściwej klasy magazynu może spowodować, że dane będą narażone na ryzyko utraty danych w przypadku awarii sprzętu lub w wyniku mniejszej wydajności.

Zwykle istnieją dwa typy magazynów:

- **Magazyn lokalny** — magazyn z obsługą administracyjną na lokalnych dyskach twardych w danym węźle. Ten rodzaj magazynu może być idealny pod względem wydajności, ale wymaga zaprojektowania nadmiarowości danych przez replikowanie danych między wieloma węzłami.
- **Zdalne, udostępnione magazyny** — obsługiwane magazyny na niektórych zdalnych urządzeniach magazynujących — na przykład, San, nas lub usługi magazynu w chmurze, takie jak EBS lub Azure Files. Ten rodzaj magazynu zapewnia ogólnie nadmiarowość danych, ale nie tak szybko, jak w przypadku magazynu lokalnego.

> [!NOTE]
> Na razie w przypadku korzystania z systemu plików NFS należy ustawić allowRunAsRoot na wartość true w pliku profilu wdrożenia przed wdrożeniem kontrolera danych usługi Azure Arc.

### <a name="data-controller-storage-configuration"></a>Konfiguracja magazynu kontrolera danych

Niektóre usługi w usłudze Azure ARC dla usług danych są zależne od konfiguracji do korzystania z zdalnego, udostępnionego magazynu, ponieważ usługi nie mają możliwości replikowania danych. Te usługi są dostępne w kolekcji:

|**Usługa**|**Trwałe oświadczenia woluminu**|
|---|---|
|**ElasticSearch**|`<namespace>/logs-logsdb-0`, `<namespace>/data-logsdb-0`|
|**InfluxDB**|`<namespace>/logs-metricsdb-0`, `<namespace>/data-metricsdb-0`|
|**Wystąpienie serwera SQL**|`<namespace>/logs-controldb`, `<namespace>/data-controldb`|
|**Usługa interfejsu API kontrolera**|`<namespace>/data-controller`|

W momencie aprowizacji kontrolera danych Klasa magazynu, która będzie używana dla każdego z tych woluminów trwałych jest określona przez przekazanie klasy--Storage-Class | -SC parametru do `azdata arc dc create` polecenia lub ustawiając klasy magazynu w control.jsw pliku szablonu wdrożenia, który jest używany.

Szablony wdrażania, które są dostępne w polu, mają określoną domyślną klasę magazynu odpowiednią dla środowiska docelowego, ale można ją zastąpić podczas wdrażania. Zapoznaj się ze szczegółowymi krokami, aby zmienić [profil wdrożenia](create-data-controller.md) w celu zmiany konfiguracji klasy magazynu dla zasobników kontrolera danych podczas wdrażania.

W przypadku ustawienia klasy magazynu przy użyciu klasy--Storage-Class | -SC parametr Klasa Storage zostanie użyta dla klas magazynu dzienników i danych. Jeśli ustawisz klasy magazynu w pliku szablonu wdrożenia, możesz określić różne klasy magazynu dla dzienników i danych.

Ważne czynniki, które należy wziąć pod uwagę podczas wybierania klasy magazynu dla pojemności kontrolera danych:

- Aby zapewnić trwałość danych, **należy** użyć zdalnej, współdzielonej klasy magazynu, dzięki czemu w przypadku, gdy program lub węzeł jest uważany, że po utworzeniu kopii zapasowej można ponownie nawiązać połączenie z woluminem trwałym.
- Dane zapisywane w wystąpieniu SQL Server, w bazie danych metryk i w bazie danych dzienników są zwykle dość małe i nie są wrażliwe na opóźnienia, co sprawia, że magazyn o wysokiej wydajności nie jest krytyczny. Jeśli masz użytkowników, którzy często korzystają z interfejsów Grafana i Kibana i masz dużą liczbę wystąpień bazy danych, użytkownicy mogą korzystać z szybszego przechowywania.
- Wymagana pojemność magazynu jest zmienna wraz z liczbą wdrożonych wystąpień bazy danych, ponieważ dzienniki i metryki są zbierane dla każdego wystąpienia bazy danych. Dane są przechowywane w bazie danych dzienników i metryk przez dwa (2) tygodnie przed przeczyszczeniem. 
- Zmiana wdrożenia klasy magazynu jest trudna, nie udokumentowana i nie jest obsługiwana. Upewnij się, że Klasa magazynu została prawidłowo wybrana w czasie wdrażania.

> [!NOTE]
> Jeśli nie określono klasy magazynu, zostanie użyta domyślna Klasa magazynu. Może istnieć tylko jedna domyślna Klasa magazynu na klaster Kubernetes. Można [zmienić domyślną klasę magazynu](https://kubernetes.io/docs/tasks/administer-cluster/change-default-storage-class/).

### <a name="database-instance-storage-configuration"></a>Konfiguracja magazynu wystąpienia bazy danych

Każde wystąpienie bazy danych ma dane, dzienniki i woluminy trwałe kopii zapasowych. Klasy magazynu dla tych woluminów trwałych można określić w czasie wdrażania. Jeśli nie określono klasy magazynu, zostanie użyta domyślna Klasa magazynu.

Podczas tworzenia wystąpienia przy użyciu albo `azdata arc sql mi create` lub `azdata arc postgres server create` , istnieją dwa parametry, których można użyć do ustawiania klas magazynu:

> [!NOTE]
> Niektóre z tych parametrów są opracowywane i staną się dostępne w `azdata arc sql mi create` `azdata arc postgres server create` przyszłych wersjach.

|Nazwa parametru, krótka nazwa|Sposób użycia|
|---|---|
|`--storage-class-data`, `-scd`|Służy do określania klasy magazynu dla wszystkich plików danych, w tym plików dziennika transakcji|
|`--storage-class-logs`, `-scl`|Służy do określania klasy magazynu dla wszystkich plików dziennika|
|`--storage-class-data-logs`, `-scdl`|Służy do określania klasy magazynu dla plików dziennika transakcji bazy danych. **Uwaga: jeszcze nie jest dostępna.**|
|`--storage-class-backups`, `-scb`|Służy do określania klasy magazynu dla wszystkich plików kopii zapasowej. **Uwaga: jeszcze nie jest dostępna.**|

W poniższej tabeli wymieniono ścieżki wewnątrz kontenera wystąpienia zarządzanego usługi Azure SQL, które są mapowane na wolumin trwały dla danych i dzienników:

|Nazwa parametru, krótka nazwa|Ścieżka wewnątrz kontenera MSSQL-miaa|Opis|
|---|---|---|
|`--storage-class-data`, `-scd`|/var/opt|Zawiera katalogi dla instalacji programu MSSQL i innych procesów systemowych. Katalog programu MSSQL zawiera dane domyślne (w tym dzienniki transakcji), dziennik błędów & katalogi kopii zapasowych|
|`--storage-class-logs`, `-scl`|/var/log|Zawiera katalogi, które przechowują dane wyjściowe konsoli (stderr, stdout), inne informacje rejestrowania procesów wewnątrz kontenera|

W poniższej tabeli przedstawiono ścieżki wewnątrz kontenera wystąpienia PostgreSQL, które są mapowane na wolumin trwały dla danych i dzienników:

|Nazwa parametru, krótka nazwa|Ścieżka wewnątrz kontenera Postgres|Opis|
|---|---|---|
|`--storage-class-data`, `-scd`|/var/opt/postgresql|Zawiera katalogi danych i dzienników dla instalacji Postgres|
|`--storage-class-logs`, `-scl`|/var/log|Zawiera katalogi, które przechowują dane wyjściowe konsoli (stderr, stdout), inne informacje rejestrowania procesów wewnątrz kontenera|

Każde wystąpienie bazy danych będzie miało oddzielny wolumin trwały dla plików danych, dzienników i kopii zapasowych. Oznacza to, że w przypadku każdego z tych typów plików zostanie przeprowadzone oddzielenie operacji we/wy z uwzględnieniem sposobu udostępniania magazynu przez program Volume Provisioning. Każde wystąpienie bazy danych ma własne trwałe oświadczenia woluminu i woluminy trwałe.

Jeśli w danym wystąpieniu bazy danych istnieje wiele baz danych, wszystkie bazy danych będą używać tego samego trwałego żądania woluminu, trwałego woluminu i klasy magazynu. Wszystkie kopie zapasowe — zarówno kopie zapasowe dziennika różnicowego, jak i pełne kopie zapasowe będą używać tego samego trwałego żądania woluminu i woluminu trwałego. Poniżej przedstawiono oświadczenia woluminu trwałego dotyczącego wystąpień bazy danych:

|**Wystąpienie**|**Trwałe oświadczenia woluminu**|
|---|---|
|**Wystąpienie zarządzane Azure SQL**|`<namespace>/logs-<instance name>-0`, `<namespace>/data-<instance name>-0`|
|**Wystąpienie usługi Azure Database for PostgreSQL**|`<namespace>/logs--<instance name>-0`, `<namespace>/data--<instance name>-0`|
|**Skalowanie PostgreSQL na platformie Azure**|`<namespace>/logs-<instance namme>-<ordinal>`, `<namespace>/data-<instance namme>-<ordinal>` *(Liczby porządkowe z zakresu od 0 do w miejscu, w którym jest liczbą procesów roboczych)*|

Ważne czynniki, które należy wziąć pod uwagę podczas wybierania klasy magazynu dla wystąpienia bazy danych:

- Wystąpienia bazy danych można wdrożyć w jednym wzorcu pod wzorcem lub z wieloma wzorcami. Przykładem pojedynczego wzorca pod wzorcem jest wystąpienie dewelopera wystąpienia zarządzanego Azure SQL lub warstwa cenowa ogólnego zastosowania wystąpienia zarządzanego Azure SQL. Przykładem z wieloma wzorcami jest usługa o wysokiej dostępności dla firmowego poziomu usługi Azure SQL. (Uwaga: warstwy cenowe są w trakcie opracowywania i nie są jeszcze dostępne dla klientów).  Wystąpienia bazy danych wdrożone przy użyciu wzorca pojedynczego pod **muszą** używać zdalnej, współdzielonej klasy magazynu w celu zapewnienia trwałości danych i tak, aby w przypadku, gdy po utworzeniu kopii zapasowej na stronie lub w węźle został nawiązane ponowne połączenie z woluminem trwałym. Natomiast wystąpienie zarządzane Azure SQL o wysokiej dostępności używa zawsze włączonych grup dostępności do replikowania danych z jednego wystąpienia na inny albo synchronicznie lub asynchronicznie. Szczególnie w przypadku, gdy dane są replikowane synchronicznie, zawsze istnieje wiele kopii danych — zazwyczaj trzy (3). Z tego względu można używać magazynu lokalnego lub zdalnych, współdzielonych klas magazynu dla plików danych i dziennika. W przypadku używania magazynu lokalnego dane są nadal zachowywane nawet w przypadku niepowodzenia, węzła lub sprzętu magazynu. W celu zapewnienia lepszej wydajności możesz użyć magazynu lokalnego.
- Wydajność bazy danych jest w dużym stopniu funkcją przepływności we/wy danego urządzenia magazynującego. Jeśli baza danych ma duże ilości operacji odczytu lub dużego zapisu, należy wybrać klasę magazynu z sprzętem przeznaczonym dla tego typu obciążenia. Na przykład jeśli baza danych jest najczęściej używana do zapisu, można wybrać pozycję Magazyn lokalny z użyciem macierzy RAID 0. Jeśli baza danych jest najczęściej używana do odczytywania małych ilości danych "gorąca", ale istnieje duża ilość pamięci masowej w przypadku zimnych danych, można wybrać urządzenie SAN obsługujące magazyn warstwowy. Wybór odpowiedniej klasy magazynu nie różni się od wybrania typu magazynu, który będzie używany dla dowolnej bazy danych.
- Jeśli używasz lokalnego aprowizacji woluminu magazynu, upewnij się, że woluminy lokalne, które są obsługiwane na potrzeby danych, dzienników i kopii zapasowych, są wyładunkiem na różnych podstawowych urządzeniach magazynujących, aby uniknąć rywalizacji o dyskowe operacje we/wy. System operacyjny powinien również znajdować się na woluminie, który jest instalowany na oddzielnych dyskach. Jest to zasadniczo takie same wskazówki, jak w przypadku wystąpienia bazy danych na sprzęcie fizycznym.
- Ponieważ wszystkie bazy danych w danym wystąpieniu mają udział trwały i wolumin trwały, należy się upewnić, że w tym samym wystąpieniu bazy danych nie można umieścić wystąpień zajętej bazy danych. Jeśli to możliwe, rozdziel zajęte bazy danych na własne wystąpienia bazy danych, aby uniknąć rywalizacji O operacje we/wy. Ponadto należy użyć etykiety węzła do wystąpień lądowych baz danych na osobne węzły, aby można było rozpowszechnić ogólny ruch we/wy między wieloma węzłami. W przypadku korzystania z wirtualizacji należy rozważyć rozproszenie ruchu we/wy, a nie tylko na poziomie węzła, ale również połączone operacje we/wy wykonywane przez wszystkie węzły maszyny wirtualne na danym hoście fizycznym.

## <a name="estimating-storage-requirements"></a>Szacowanie wymagań dotyczących magazynu
Każdy składnik zawierający dane stanowe używa dwóch woluminów trwałych w tej wersji — jeden wolumin trwały dla danych i inny wolumin trwały dla dzienników. W poniższej tabeli przedstawiono liczbę woluminów trwałych wymaganych przez jeden kontroler danych, wystąpienie zarządzane Azure SQL, wystąpienie Azure Database for PostgreSQL i wystąpienie usługi Azure PostgreSQL w skali:

|Typ zasobu|Liczba numerów stanowych|Wymagana liczba woluminów trwałych|
|---|---|---|
|Kontroler danych|4 ( `control` ,,, `controldb` `logsdb` `metricsdb` )|4 * 2 = 8|
|Wystąpienie zarządzane Azure SQL|1|2|
|Wystąpienie Azure Database for PostgreSQL|1| 2|
|Skalowanie PostgreSQL na platformie Azure|1 + W (W = liczba procesów roboczych)|2 * (1 + W)|

W poniższej tabeli przedstawiono łączną liczbę woluminów trwałych wymaganych do wdrożenia przykładowego:

|Typ zasobu|Liczba wystąpień|Wymagana liczba woluminów trwałych|
|---|---|---|
|Kontroler danych|1|4 * 2 = 8|
|Wystąpienie zarządzane Azure SQL|5|5 * 2 = 10|
|Wystąpienie Azure Database for PostgreSQL|5| 5 * 2 = 10|
|Skalowanie PostgreSQL na platformie Azure|2 (liczba procesów roboczych = 4 na wystąpienie)|2 * 2 * (1 + 4) = 20|
|***Łączna liczba woluminów trwałych***||8 + 10 + 10 + 20 = 48|

Tego obliczenia można użyć do zaplanowania magazynu dla klastra Kubernetes w oparciu o aprowizacji lub środowisko pamięci masowej. Na przykład jeśli lokalna usługa magazynu jest używana dla klastra Kubernetes z pięcioma (5) węzłami, dla przykładowego wdrożenia powyżej każdy węzeł wymaga co najmniej magazynu dla 10 woluminów trwałych. Podobnie podczas aprowizacji klastra usługi Azure Kubernetes Service (AKS) z pięcioma (5) węzłami wybierają odpowiedni rozmiar maszyny wirtualnej dla puli węzłów, aby można było dołączać 10 dysków z danymi. Więcej informacji na temat sposobu zmieniania węzłów dla potrzeb magazynu dla węzłów AKS można znaleźć [tutaj](../../aks/operator-best-practices-storage.md#size-the-nodes-for-storage-needs).

## <a name="choosing-the-right-storage-class"></a>Wybieranie odpowiedniej klasy magazynu

### <a name="on-premises-and-edge-sites"></a>Lokacje lokalne i brzegowe

Firma Microsoft i jej partnerzy OEM, OS i Kubernetes działają w programie certyfikacyjnym dla usług Azure Arc Data Services. Ten program zapewni klientom porównywalne wyniki testów z zestawu narzędzi do testowania certyfikacji. Testy będą szacować zgodność funkcji, wyniki testów obciążeniowych oraz wydajność i skalowalność. Każdy z tych wyników testów będzie wskazywać używany system operacyjny, używany do dystrybucji Kubernetes, używany dodatek CSI i używane klasy magazynu. Ułatwi to klientom wybranie najlepszej klasy magazynu, systemu operacyjnego, dystrybucji Kubernetes i sprzętu w celu spełnienia wymagań. Więcej informacji na temat tego programu i początkowych wyników testów będzie można znaleźć bliżej ogólnej dostępności usługi Azure Arc Data Services.

#### <a name="public-cloud-managed-kubernetes-services"></a>Chmura publiczna, zarządzane usługi Kubernetes Services

W przypadku publicznych usług Kubernetes zarządzanych w chmurze firma Microsoft może podejmować następujące zalecenia:

|Usługa w chmurze publicznej|Zalecenie|
|---|---|
|**Azure Kubernetes Service (AKS)**|Usługa Azure Kubernetes Service (AKS) ma dwa typy magazynów — Azure Files i Managed Disks platformy Azure. Każdy typ magazynu ma dwie ceny/warstwy wydajności — standardowe (dysk twardy) i Premium (SSD). W ten sposób cztery klasy magazynu udostępniane w AKS są `azurefile` (Azure Files warstwy Standardowa), `azurefile-premium` (Azure Files warstwy Premium), `default` (warstwa Standardowa usługi Azure disks) i `managed-premium` (Azure disks w warstwie Premium). Domyślną klasą magazynu jest `default` (usługa Azure disks w warstwie Standardowa). Istnieją istotne **[różnice cenowe](https://azure.microsoft.com/en-us/pricing/details/storage/)** między typami i warstwami, które powinny zostać uwzględnione w decyzji. W przypadku obciążeń produkcyjnych z wymaganiami o wysokiej wydajności zalecamy użycie `managed-premium` dla wszystkich klas magazynu. W przypadku obciążeń związanych z tworzeniem i testowaniem, weryfikacjami koncepcji itp., gdzie koszt jest brany pod uwagę, `azurefile` jest to najtańsza opcja. Wszystkie cztery opcje mogą być używane w przypadku sytuacji wymagających zdalnego magazynu udostępnionego, ponieważ są one urządzeniami magazynującymi dołączonymi do sieci na platformie Azure. Przeczytaj więcej na temat [magazynu AKS](../../aks/concepts-storage.md).|
|**AWS Elastic Kubernetes Service (EKS)**| Elastyczna usługa Kubernetes firmy Amazon ma jedną podstawową klasę magazynu opartą na [sterowniku magazynu EBS CSI](https://docs.aws.amazon.com/eks/latest/userguide/ebs-csi.html). Jest to zalecane w przypadku obciążeń produkcyjnych. Istnieje nowy sterownik magazynu — [sterownik magazynu CSI programu EFS](https://docs.aws.amazon.com/eks/latest/userguide/efs-csi.html) , który można dodać do klastra eks, ale jest obecnie w fazie beta i może ulec zmianie. Chociaż AWS oznacza, że ten sterownik magazynu jest obsługiwany dla środowiska produkcyjnego, nie zalecamy jej używania, ponieważ nadal jest w wersji beta i może ulec zmianie. Klasa magazynu EBS jest wartością domyślną i jest wywoływana `gp2` . Przeczytaj więcej na temat [magazynu eks](https://docs.aws.amazon.com/eks/latest/userguide/storage-classes.html).|
|**Google Kubernetes Engine (GKE)**|Aparat Google Kubernetes Engine (GKE) ma tylko jedną klasę magazynu o nazwie `standard` , która jest używana na potrzeby [GCE dysków trwałych](https://kubernetes.io/docs/concepts/storage/volumes/#gcepersistentdisk). Jest to jedyna wartość domyślna. Mimo że istnieje [lokalna, statyczna prowizja woluminu](https://cloud.google.com/kubernetes-engine/docs/how-to/persistent-volumes/local-ssd#run-local-volume-static-provisioner) dla GKE, która może być używana z bezpośrednio dołączonymi dysków SSD, nie zalecamy jej używania, ponieważ nie jest ona obsługiwana przez firmę Google ani nie obsługuje jej. Przeczytaj więcej na temat [magazynu GKE](https://cloud.google.com/kubernetes-engine/docs/concepts/persistent-volumes).
