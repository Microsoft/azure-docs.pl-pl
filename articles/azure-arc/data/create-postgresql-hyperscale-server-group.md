---
title: Tworzenie grupy serwerów z Azure Database for PostgreSQLm skalowaniem w usłudze Azure Arc
description: Tworzenie grupy serwerów z Azure Database for PostgreSQLm skalowaniem w usłudze Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: fb628df5151f9124d7b7f319ff109ffca030ee90
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317348"
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

Jeśli wolisz wypróbować problemy bez aprowizacji całego środowiska, szybko Rozpocznij pracę z usługą [Azure Arc szybko Rozpocznij pracę](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) w usłudze Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (eks), Google Cloud Kubernetes Engine (GKE) lub na maszynie wirtualnej platformy Azure.


## <a name="login-to-the-azure-arc-data-controller"></a>Zaloguj się do kontrolera danych usługi Azure Arc

Aby można było utworzyć wystąpienie, należy najpierw zalogować się do kontrolera danych usługi Azure Arc. Jeśli użytkownik jest już zalogowany do kontrolera danych, można pominąć ten krok.

```console
azdata login
```

Następnie zostanie wyświetlony monit o podanie nazwy użytkownika, hasła i systemowej przestrzeni nazw.  

> Jeśli skrypt został użyty do utworzenia kontrolera danych, przestrzeń nazw powinna być **łukiem**

```console
Namespace: arc
Username: arcadmin
Password:
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>Wstępny i tymczasowy krok dotyczący tylko użytkowników OpenShift

Przed przejściem do następnego kroku Zaimplementuj ten krok. Aby wdrożyć grupę serwerów PostgreSQL na platformie Red Hat OpenShift w projekcie innym niż domyślny, należy wykonać następujące polecenia w odniesieniu do klastra w celu zaktualizowania ograniczeń zabezpieczeń. To polecenie przyznaje odpowiednie uprawnienia do kont usług, które będą uruchamiać grupę serwerów PostgreSQL. Łuk ograniczenia kontekstu zabezpieczeń (SCC) **_— Data-SCC_** to ten, który został dodany podczas wdrażania kontrolera danych usługi Azure Arc.

```console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

_**Nazwa grupy serwerów** to nazwa grupy serwerów, która zostanie utworzona w następnym kroku._
   
Aby uzyskać więcej informacji na temat SCCs w OpenShift, zapoznaj się z [dokumentacją OpenShift](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html).
Teraz można zaimplementować następny krok.

## <a name="create-an-azure-database-for-postgresql-hyperscale-server-group"></a>Tworzenie grupy serwerów z Azure Database for PostgreSQLm skalowaniem

Aby utworzyć Azure Database for PostgreSQL grupę serwerów na potrzeby skalowania w usłudze Azure ARC, użyj następującego polecenia:

```console
azdata arc postgres server create -n <name> --workers <# worker nodes with #>=2> --storage-class-data <storage class name> --storage-class-logs <storage class name> --storage-class-backups <storage class name>

#Example
#azdata arc postgres server create -n postgres01 --workers 2
```

> [!NOTE]
> - **Dostępne są inne parametry wiersza polecenia.  Zapoznaj się z pełną listą opcji, uruchamiając `azdata arc postgres server create --help` .**
> - Klasa magazynu używana do tworzenia kopii zapasowych (_--Storage-Class-Backup-SCB_) domyślnie jest klasą magazynu danych kontrolera danych, jeśli nie została dostarczona.
> - Jednostka zaakceptowana przez parametry--Volume-* parametrów jest Kubernetesą ilością zasobów (liczba całkowita, po której następuje jeden z tych elementów SI (T, G, M, K, m) lub ich potęgi dwóch odpowiedników (TI, gi, mi, ki)).
> - Nazwy muszą mieć długość co najmniej 12 znaków i być zgodne z konwencjami nazewnictwa DNS.
> - Zostanie wyświetlony monit o wprowadzenie hasła dla standardowego użytkownika administracyjnego _Postgres_ .  Możesz pominąć interaktywny monit przez ustawienie `AZDATA_PASSWORD` zmiennej środowiskowej sesji przed uruchomieniem polecenia CREATE.
> - Jeśli kontroler danych został wdrożony przy użyciu AZDATA_USERNAME i AZDATA_PASSWORD zmiennych środowiskowych sesji w ramach tej samej sesji terminala, zostaną użyte wartości AZDATA_PASSWORD do wdrożenia grupy serwerów PostgreSQL. Jeśli wolisz użyć innego hasła, albo (1) zaktualizuj wartość AZDATA_PASSWORD lub (2) Usuń zmienną środowiskową AZDATA_PASSWORD lub Usuń jej wartość, gdy zostanie wyświetlony monit o wprowadzenie hasła interaktywnie podczas tworzenia grupy serwerów.
> - Nazwa domyślnego użytkownika administratora dla aparatu bazy danych PostgreSQL jest _Postgres_ i nie można jej zmienić w tym momencie.
> - Utworzenie grupy serwerów PostgreSQL ze skalowaniem nie spowoduje natychmiastowego zarejestrowania zasobów na platformie Azure. W ramach procesu przekazywania danych dotyczących [spisu zasobów](upload-metrics-and-logs-to-azure-monitor.md)  lub [użycia](view-billing-data-in-azure.md) na platformę Azure zasoby zostaną utworzone na platformie Azure i będzie można zobaczyć swoje zasoby w Azure Portal.



## <a name="list-your-azure-database-for-postgresql-server-groups-created-in-your-arc-setup"></a>Wyświetl listę grup serwerów Azure Database for PostgreSQL utworzonych w ustawieniach Arc

Aby wyświetlić grupy serwerów PostgreSQL na platformie Azure ARC, użyj następującego polecenia:

```console
azdata arc postgres server list
```


```output
Name        State     Workers
----------  --------  ---------
postgres01  Ready     2
```

## <a name="get-the-endpoints-to-connect-to-your-azure-database-for-postgresql-server-groups"></a>Pobierz punkty końcowe, aby połączyć się z grupami serwerów Azure Database for PostgreSQL

Aby wyświetlić punkty końcowe dla wystąpienia PostgreSQL, uruchom następujące polecenie:

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

Punkt końcowy wystąpienia PostgreSQL można użyć do nawiązania połączenia z grupą serwerów PostgreSQL z ulubionym narzędziem:  [Azure Data Studio](https://aka.ms/getazuredatastudio), [Pgcli](https://www.pgcli.com/) PSQL, pgAdmin itd.

Jeśli używasz maszyny wirtualnej platformy Azure do testowania, postępuj zgodnie z poniższymi instrukcjami:

## <a name="special-note-about-azure-virtual-machine-deployments"></a>Specjalna Uwaga dotycząca wdrożeń maszyn wirtualnych platformy Azure

W przypadku korzystania z maszyny wirtualnej platformy Azure adres IP punktu końcowego nie będzie zawierał _publicznego_ adresu IP. Aby zlokalizować publiczny adres IP, użyj następującego polecenia:

```console
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

Można następnie połączyć publiczny adres IP z portem, aby nawiązać połączenie.

Może być również konieczne uwidocznienie portu grupy serwerów PostgreSQL w usłudze Network Security Gateway (sieciowej grupy zabezpieczeń). Aby zezwolić na ruch za pośrednictwem (sieciowej grupy zabezpieczeń), musisz dodać regułę, którą można wykonać przy użyciu następującego polecenia:

Aby ustawić regułę, należy znać nazwę sieciowej grupy zabezpieczeń. SIECIOWEJ grupy zabezpieczeń należy określić przy użyciu poniższego polecenia:

```console
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

Po utworzeniu nazwy sieciowej grupy zabezpieczeń można dodać regułę zapory za pomocą następującego polecenia. Przykładowe wartości w tym miejscu tworzą regułę sieciowej grupy zabezpieczeń dla portu 30655 i umożliwia połączenie z **dowolnego** źródłowego adresu IP.  Nie jest to najlepsze rozwiązanie w zakresie zabezpieczeń.  Można łatwiej blokować elementy, określając wartość-Source-Address-prefix, która jest specyficzna dla adresu IP klienta lub zakresu adresów IP, który obejmuje adresy IP Twojego zespołu lub organizacji.

Zastąp wartość parametru--destination-port-rangi poniżej numerem portu uzyskanym z powyższego polecenia "azdata Arc Postgres Server list".

```console
az network nsg rule create -n db_port --destination-port-ranges 30655 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="connect-with-azure-data-studio"></a>Nawiązywanie połączenia z narzędziem Azure Data Studio

Otwórz Azure Data Studio i Połącz się z wystąpieniem przy użyciu zewnętrznego adresu IP i numeru portu punktu końcowego oraz hasła określonego w momencie utworzenia wystąpienia.  Jeśli PostgreSQL nie jest dostępny na liście rozwijanej *Typ połączenia* , można zainstalować rozszerzenie PostgreSQL, wyszukując PostgreSQL na karcie rozszerzenia.

> [!NOTE]
> Aby wprowadzić numer portu, należy kliknąć przycisk [Zaawansowane] w panelu połączenie.

Pamiętaj, że jeśli korzystasz z maszyny wirtualnej platformy Azure, będzie potrzebny _publiczny_ adres IP, który jest dostępny za pośrednictwem następującego polecenia:

```console
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

## <a name="connect-with-psql"></a>Łączenie z usługą PSQL

Aby uzyskać dostęp do grupy serwerów PostgreSQL do skalowania, Przekaż zewnętrzny punkt końcowy grupy serwerów PostgreSQL w skali, która została pobrana z powyższych:

Teraz możesz połączyć dowolną PSQL:

```console
psql postgresql://postgres:<EnterYourPassword>@10.0.0.4:30655
```

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z pojęciami i przewodnikami dotyczącymi Azure Database for PostgreSQL skalowania, aby dystrybuować dane między wieloma Azure Database for PostgreSQL węzłami PostgreSQL. :
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
- [Rozszerzanie trwałych oświadczeń woluminu](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Model zasobów Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
