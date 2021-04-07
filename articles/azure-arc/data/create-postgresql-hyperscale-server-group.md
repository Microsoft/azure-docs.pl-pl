---
title: Tworzenie grupy serwerów PostgreSQL w warstwie Hiperskala z obsługą usługi Azure Arc
description: Tworzenie grupy serwerów PostgreSQL w warstwie Hiperskala z obsługą usługi Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: 046f9d80c034e1ac1f2e7ffe144b4f389861b043
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101687944"
---
# <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Tworzenie grupy serwerów PostgreSQL w warstwie Hiperskala z obsługą usługi Azure Arc

W tym dokumencie opisano kroki tworzenia grupy serwerów PostgreSQL na platformie Azure Arc.

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="getting-started"></a>Wprowadzenie
Jeśli znasz już poniższe tematy, możesz pominąć ten akapit.
Istnieją ważne tematy, które warto przeczytać przed kontynuowaniem tworzenia:
- [Omówienie usług danych z obsługą usługi Azure Arc](overview.md)
- [Tryby i wymagania dotyczące łączności](connectivity.md)
- [Konfiguracja magazynu i pojęcia dotyczące magazynu Kubernetes](storage-configuration.md)
- [Model zasobów Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

Jeśli wolisz wypróbować elementy bez aprowizacji całkowicie środowiska, szybko Rozpocznij pracę z usługą [Azure Arc szybko Rozpocznij pracę](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) w usłudze Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (eks), Google Cloud Kubernetes Engine (GKE) lub na maszynie wirtualnej platformy Azure.


## <a name="login-to-the-azure-arc-data-controller"></a>Zaloguj się do kontrolera danych usługi Azure Arc

Aby można było utworzyć wystąpienie, należy najpierw zalogować się do kontrolera danych usługi Azure Arc. Jeśli użytkownik jest już zalogowany do kontrolera danych, można pominąć ten krok.

```console
azdata login
```

Następnie zostanie wyświetlony monit o podanie nazwy użytkownika, hasła i przestrzeni nazw System.  

> Jeśli skrypt został użyty do utworzenia kontrolera danych, przestrzeń nazw powinna być **łukiem**

```console
Namespace: arc
Username: arcadmin
Password:
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>Wstępny i tymczasowy krok dotyczący tylko użytkowników OpenShift
Przed przejściem do następnego kroku Zaimplementuj ten krok. Aby wdrożyć grupę serwerów PostgreSQL na platformie Red Hat OpenShift w projekcie innym niż domyślny, należy wykonać następujące polecenia w odniesieniu do klastra w celu zaktualizowania ograniczeń zabezpieczeń. To polecenie przyznaje odpowiednie uprawnienia do kont usług, które będą uruchamiać grupę serwerów PostgreSQL. Łuk ograniczenia kontekstu zabezpieczeń (SCC) — Data-SCC to ten, który został dodany podczas wdrażania kontrolera danych usługi Azure Arc.

```Console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

**Nazwa grupy serwerów to nazwa grupy serwerów, która zostanie utworzona w następnym kroku.**

Aby uzyskać więcej informacji na temat SCCs w OpenShift, zapoznaj się z [dokumentacją OpenShift](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html). Teraz można zaimplementować następny krok.


## <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Tworzenie grupy serwerów PostgreSQL w warstwie Hiperskala z obsługą usługi Azure Arc

Aby utworzyć na kontrolerze danych Arc grupę serwerów z włączoną funkcją Azure Arc PostgreSQL, należy użyć polecenia, `azdata arc postgres server create` do którego zostanie przekazane kilka parametrów.

Aby uzyskać szczegółowe informacje na temat wszystkich parametrów, które można ustawić w czasie tworzenia, Przejrzyj dane wyjściowe polecenia:
```console
azdata arc postgres server create --help
```

Należy wziąć pod uwagę główne parametry:
- **Nazwa grupy serwerów** , która ma zostać wdrożona. Wskaż albo `--name` `-n` po którym następuje nazwa, której długość nie może przekraczać 11 znaków.

- **wersja aparatu PostgreSQL** , którą chcesz wdrożyć: domyślnie jest to wersja 12. Aby wdrożyć wersję 12, można pominąć ten parametr lub przekazać jeden z następujących parametrów: `--engine-version 12` lub `-ev 12` . Aby wdrożyć wersję 11, wskaż `--engine-version 11` lub `-ev 11` .

- **Liczba węzłów procesu roboczego** , które mają zostać wdrożone w celu skalowania w poziomie i potencjalnie osiągania lepszych wydajności. Przed przejściem tutaj Przeczytaj [koncepcje dotyczące skalowania Postgres](concepts-distributed-postgres-hyperscale.md). Aby określić liczbę węzłów procesu roboczego do wdrożenia, użyj parametru lub, `--workers` `-w` po których następuje liczba całkowita większa lub równa 2. Jeśli na przykład chcesz wdrożyć grupę serwerów z 2 węzłami roboczymi, wskaż `--workers 2` lub `-w 2` . Spowoduje to utworzenie trzech zasobników, jednego dla węzła/wystąpienia koordynatora i dwóch dla węzłów procesu roboczego/wystąpień (jeden dla każdego z procesów roboczych).

- **klasy magazynu** , które mają być używane przez grupę serwerów. Ważne jest, aby ustawić klasę magazynu bezpośrednio w czasie wdrażania grupy serwerów, ponieważ nie można jej zmienić po wdrożeniu. W przypadku zmiany klasy magazynu po wdrożeniu należy wyodrębnić dane, usunąć grupę serwerów, utworzyć nową grupę serwerów i zaimportować dane. Możesz określić klasy magazynu do użycia dla danych, dzienników i kopii zapasowych. Domyślnie, jeśli nie wskazano klas magazynu, zostaną użyte klasy magazynu kontrolera danych.
    - Aby ustawić klasę magazynu dla danych, wskaż parametr `--storage-class-data` lub `-scd` po nim nazwę klasy magazynu.
    - Aby ustawić klasę magazynu dla dzienników, wskaż parametr `--storage-class-logs` lub `-scl` po nim nazwę klasy magazynu.
    - Aby ustawić klasę magazynu dla kopii zapasowych: w tej wersji zapoznawczej usługi Azure ARC z włączonym skalowaniem PostgreSQL istnieją dwa sposoby ustawiania klas magazynu w zależności od tego, jakie typy operacji tworzenia kopii zapasowej/przywracania chcesz wykonać. Pracujemy nad uproszczeniem tego środowiska. Wskażesz klasę magazynu lub instalację z wnioskiem o wolumin. Instalacja z wnioskiem o woluminie to para istniejących, trwałych roszczeń woluminu (w tej samej przestrzeni nazw) i typu woluminu (i opcjonalnych metadanych w zależności od typu woluminu) rozdzielonych średnikami. Wolumin trwały zostanie zainstalowany w każdym obszarze pod dla grupy serwerów PostgreSQL.
        - Jeśli chcesz, aby planowane było tylko pełne przywrócenie bazy danych, ustaw parametr `--storage-class-backups` lub `-scb` po nim nazwę klasy magazynu.
        - Jeśli planujesz przeprowadzenie pełnej przywracania bazy danych i przywrócenie do punktu w czasie, ustaw parametr `--volume-claim-mounts` lub `-vcm` po nim nazwę żądania woluminu i typ woluminu.

Należy pamiętać, że po wykonaniu polecenia Utwórz zostanie wyświetlony monit o wprowadzenie hasła domyślnego `postgres` użytkownika administracyjnego. Nie można zmienić nazwy tego użytkownika w tej wersji zapoznawczej. Możesz pominąć interaktywny monit przez ustawienie `AZDATA_PASSWORD` zmiennej środowiskowej sesji przed uruchomieniem polecenia CREATE.

### <a name="examples"></a>Przykłady

**Aby wdrożyć grupę serwerów Postgres w wersji 12 o nazwie postgres01 z 2 węzłami roboczymi, które używają tych samych klas magazynu co kontroler danych, uruchom następujące polecenie:**
```console
azdata arc postgres server create -n postgres01 --workers 2
```

**Aby wdrożyć grupę serwerów programu Postgres w wersji 12 o nazwie postgres01 z 2 węzłami roboczymi, które używają tych samych klas magazynu co kontroler danych dla danych i dzienników, ale jego konkretna Klasa magazynu do wykonywania operacji pełnego przywracania i przywracania do punktu w czasie, wykonaj następujące czynności:**

 W tym przykładzie przyjęto założenie, że Grupa serwerów jest hostowana w klastrze usługi Azure Kubernetes Service (AKS). W tym przykładzie zastosowano azurefile-Premium jako nazwę klasy magazynu. Możesz dostosować Poniższy przykład, aby dopasować go do środowiska. Należy pamiętać, że dla tej konfiguracji **jest wymagany AccessModes ReadWriteMany** .  

Najpierw utwórz plik YAML zawierający Poniższy opis obwodu PVC kopii zapasowej i nadaj mu nazwę CreateBackupPVC. yml na przykład:
```console
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: backup-pvc
  namespace: arc
spec:
  accessModes:
    - ReadWriteMany
  volumeMode: Filesystem
  resources:
    requests:
      storage: 100Gi
  storageClassName: azurefile-premium
```

Następnie utwórz obwód PVC przy użyciu definicji przechowywanej w pliku YAML:

```console
kubectl create -f e:\CreateBackupPVC.yml -n arc
``` 

Następnie utwórz grupę serwerów:

```console
azdata arc postgres server create -n postgres01 --workers 2 -vcm backup-pvc:backup
```

> [!IMPORTANT]
> - Zapoznaj się z [bieżącymi ograniczeniami dotyczącymi tworzenia kopii zapasowej/przywracania](limitations-postgresql-hyperscale.md#backup-and-restore).


> [!NOTE]  
> - Jeśli kontroler danych został wdrożony przy użyciu `AZDATA_USERNAME` i `AZDATA_PASSWORD` zmiennych środowiskowych sesji w tej samej sesji terminalu, wartości dla programu `AZDATA_PASSWORD` zostaną użyte do wdrożenia grupy serwerów PostgreSQL. Jeśli wolisz użyć innego hasła, (1) zaktualizuj wartość dla `AZDATA_PASSWORD` lub (2) Usuń `AZDATA_PASSWORD` zmienną środowiskową lub (3) Usuń jej wartość, aby uzyskać monit o podanie hasła interaktywnie podczas tworzenia grupy serwerów.
> - Utworzenie grupy serwerów PostgreSQL ze skalowaniem nie spowoduje natychmiastowego zarejestrowania zasobów na platformie Azure. W ramach procesu przekazywania danych dotyczących [spisu zasobów](upload-metrics-and-logs-to-azure-monitor.md)  lub [użycia](view-billing-data-in-azure.md) na platformę Azure zasoby zostaną utworzone na platformie Azure i będzie można zobaczyć swoje zasoby w Azure Portal.



## <a name="list-the-postgresql-hyperscale-server-groups-deployed-in-your-arc-data-controller"></a>Wyświetl listę grup serwerów PostgreSQL ze skalą wdrożonych w kontrolerze danych Arc

Aby wyświetlić listę grup serwerów PostgreSQL ze skalą wdrożonych w kontrolerze danych ARC, uruchom następujące polecenie:

```console
azdata arc postgres server list
```


```output
Name        State     Workers
----------  --------  ---------
postgres01  Ready     2
```

## <a name="get-the-endpoints-to-connect-to-your-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Pobierz punkty końcowe, aby połączyć się z usługą Azure ARC z włączonymi grupami serwerów PostgreSQL

Aby wyświetlić punkty końcowe dla grupy serwerów PostgreSQL, uruchom następujące polecenie:

```console
azdata arc postgres endpoint list -n <server group name>
```
Na przykład:
```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

Punkt końcowy wystąpienia PostgreSQL można użyć do nawiązania połączenia z grupą serwerów PostgreSQL z ulubionym narzędziem:  [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio), [Pgcli](https://www.pgcli.com/) PSQL, pgAdmin itd. Gdy to zrobisz, nawiążesz połączenie z węzłem/wystąpieniem koordynatora, który jest odpowiedzialny za kierowanie zapytania do odpowiednich węzłów/wystąpień procesu roboczego, jeśli utworzono tabele rozproszone. Aby uzyskać więcej informacji, zapoznaj się z [pojęciami dotyczącymi usługi Azure ARC z włączonym skalowaniem PostgreSQL](concepts-distributed-postgres-hyperscale.md).

## <a name="special-note-about-azure-virtual-machine-deployments"></a>Specjalna Uwaga dotycząca wdrożeń maszyn wirtualnych platformy Azure

W przypadku korzystania z maszyny wirtualnej platformy Azure adres IP punktu końcowego nie będzie zawierał _publicznego_ adresu IP. Aby zlokalizować publiczny adres IP, użyj następującego polecenia:

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

Można następnie połączyć publiczny adres IP z portem, aby nawiązać połączenie.

Może być również konieczne uwidocznienie portu grupy serwerów PostgreSQL w usłudze Network Security Gateway (sieciowej grupy zabezpieczeń). Aby zezwolić na ruch za pośrednictwem (sieciowej grupy zabezpieczeń), musisz dodać regułę, którą można wykonać przy użyciu następującego polecenia:

Aby ustawić regułę, należy znać nazwę sieciowej grupy zabezpieczeń. SIECIOWEJ grupy zabezpieczeń należy określić przy użyciu poniższego polecenia:

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

Po utworzeniu nazwy sieciowej grupy zabezpieczeń można dodać regułę zapory za pomocą następującego polecenia. Przykładowe wartości w tym miejscu tworzą regułę sieciowej grupy zabezpieczeń dla portu 30655 i umożliwia połączenie z **dowolnego** źródłowego adresu IP.  Nie jest to najlepsze rozwiązanie w zakresie zabezpieczeń.  Można łatwiej blokować elementy, określając wartość-Source-Address-prefix, która jest specyficzna dla adresu IP klienta lub zakresu adresów IP, który obejmuje adresy IP Twojego zespołu lub organizacji.

Zastąp wartość parametru--destination-port-rangi poniżej numerem portu uzyskanym z powyższego polecenia "azdata Arc Postgres Server list".

```azurecli
az network nsg rule create -n db_port --destination-port-ranges 30655 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="connect-with-azure-data-studio"></a>Nawiązywanie połączenia z narzędziem Azure Data Studio

Otwórz Azure Data Studio i Połącz się z wystąpieniem przy użyciu zewnętrznego adresu IP i numeru portu punktu końcowego oraz hasła określonego w momencie utworzenia wystąpienia.  Jeśli PostgreSQL nie jest dostępny na liście rozwijanej *Typ połączenia* , można zainstalować rozszerzenie PostgreSQL, wyszukując PostgreSQL na karcie rozszerzenia.

> [!NOTE]
> Aby wprowadzić numer portu, należy kliknąć przycisk [Zaawansowane] w panelu połączenie.

Pamiętaj, że jeśli korzystasz z maszyny wirtualnej platformy Azure, będzie potrzebny _publiczny_ adres IP, który jest dostępny za pośrednictwem następującego polecenia:

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

## <a name="connect-with-psql"></a>Łączenie z usługą PSQL

Aby uzyskać dostęp do grupy serwerów PostgreSQL do skalowania, Przekaż zewnętrzny punkt końcowy grupy serwerów PostgreSQL w skali, która została pobrana z powyższych:

Teraz możesz połączyć dowolną PSQL:

```console
psql postgresql://postgres:<EnterYourPassword>@10.0.0.4:30655
```

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z pojęciami i przewodnikami dotyczącymi Azure Database for PostgreSQL skalowania, aby dystrybuować dane w wielu węzłach PostgreSQL.
    * [Węzły i tabele](../../postgresql/concepts-hyperscale-nodes.md)
    * [Określanie typu aplikacji](../../postgresql/concepts-hyperscale-app-type.md)
    * [Wybieranie kolumny dystrybucji](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Kolokacja tabeli](../../postgresql/concepts-hyperscale-colocation.md)
    * [Rozpowszechnianie i modyfikowanie tabel](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Projektowanie bazy danych z wieloma dzierżawcami](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Projektowanie pulpitu nawigacyjnego analizy w czasie rzeczywistym](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \* W powyższych dokumentach Pomiń sekcje **Zaloguj się do Azure Portal**, & **Utwórz Azure Database for PostgreSQL-Citus**. Zaimplementuj pozostałe kroki w ramach wdrożenia usługi Azure Arc. Te sekcje są specyficzne dla Azure Database for PostgreSQL Citus) oferowanych jako usługa PaaS w chmurze platformy Azure, ale inne części dokumentów są bezpośrednio stosowane do wieloskalowania z włączonym systemem Azure Arc PostgreSQL.

- [Skalowanie w poziomie usługi Azure ARC z włączonym grupą serwerów PostgreSQL](scale-out-postgresql-hyperscale-server-group.md)
- [Konfiguracja magazynu i pojęcia dotyczące magazynu Kubernetes](storage-configuration.md)
- [Rozszerzanie trwałych oświadczeń woluminu](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Model zasobów Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
