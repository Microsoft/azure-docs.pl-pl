---
title: Konfigurowanie autonomicznego klastra platformy Azure Service Fabric
description: Dowiedz się, jak skonfigurować autonomiczny lub lokalny klaster usługi Azure Service Fabric.
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: fd93263b38340ce080cca1aecb98f3a599ff1861
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91843162"
---
# <a name="configuration-settings-for-a-standalone-windows-cluster"></a>Ustawienia konfiguracji dla autonomicznego klastra systemu Windows
W tym artykule opisano ustawienia konfiguracji autonomicznego klastra Service Fabric platformy Azure, które można ustawić w *ClusterConfig.js* pliku. Ten plik będzie używany do określania informacji o węzłach klastra, konfiguracjach zabezpieczeń, a także topologii sieci pod względem błędów i domen uaktualniania.  Po zmianie lub dodaniu ustawień konfiguracji można [utworzyć autonomiczny klaster](service-fabric-cluster-creation-for-windows-server.md) lub [uaktualnić konfigurację klastra autonomicznego](service-fabric-cluster-config-upgrade-windows-server.md).

Po [pobraniu pakietu autonomicznego Service Fabric](service-fabric-cluster-creation-for-windows-server.md#downloadpackage)jest również uwzględniony ClusterConfig.jsna przykładach. Przykłady, których nazwy zawierają "DevCluster", tworzą klaster ze wszystkimi trzema węzłami na tym samym komputerze przy użyciu węzłów logicznych. Z tych węzłów co najmniej jeden musi być oznaczony jako węzeł podstawowy. Ten typ klastra jest przydatny w środowisku deweloperskim lub testowym. Nie jest obsługiwany jako klaster produkcyjny. Przykłady, w których nazwy są "wielokomputerowe", ułatwiają tworzenie klastrów klasy produkcyjnej, z każdym węzłem na osobnym komputerze. Liczba węzłów podstawowych dla tych klastrów jest oparta na [poziomie niezawodności](#reliability)klastra. W wersji 5,7 interfejsu API, wersja 05-2017, została usunięta Właściwość poziomu niezawodności. Zamiast tego nasz kod oblicza najbardziej zoptymalizowany poziom niezawodności dla klastra. Nie należy próbować ustawiać wartości tej właściwości w wersji 5,7 lub nowszej.

* ClusterConfig.Unsecure.DevCluster.json i ClusterConfig.Unsecure.MultiMachine.jsna pokazuje, jak utworzyć niezabezpieczony test lub klaster produkcyjny.

* ClusterConfig.Windows.DevCluster.json i ClusterConfig.Windows.MultiMachine.jsna temat pokazuje, jak tworzyć klastry testowe lub produkcyjne zabezpieczone przy użyciu [zabezpieczeń systemu Windows](service-fabric-windows-cluster-windows-security.md).

* ClusterConfig.X509.DevCluster.json i ClusterConfig.X509.MultiMachine.jsna temat pokazuje, jak tworzyć klastry testowe lub produkcyjne zabezpieczone przy użyciu [zabezpieczeń opartych na certyfikatach x509](service-fabric-windows-cluster-x509-security.md).

Teraz Przeanalizujmy różne sekcje ClusterConfig.jspliku.

## <a name="general-cluster-configurations"></a>Ogólne Konfiguracje klastra
Ogólne Konfiguracje klastra obejmują szeroką konfigurację specyficzną dla klastra, jak pokazano w poniższym fragmencie kodu JSON:

```json
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "01-2017",
```

Do klastra Service Fabric można nadać dowolną przyjazną nazwę, przypisując ją do zmiennej nazwy. ClusterConfigurationVersion jest numerem wersji klastra. Zwiększ go za każdym razem, gdy uaktualniasz klaster Service Fabric. Pozostaw wartość domyślną apiVersion.

## <a name="nodes-on-the-cluster"></a>Węzły w klastrze
Węzły w klastrze Service Fabric można skonfigurować przy użyciu sekcji węzły, jak pokazano w poniższym fragmencie kodu:
```json
"nodes": [{
    "nodeName": "vm0",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD0"
}, {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType1",
    "faultDomain": "fd:/dc1/r1",
    "upgradeDomain": "UD1"
}, {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType2",
    "faultDomain": "fd:/dc1/r2",
    "upgradeDomain": "UD2"
}],
```

Klaster Service Fabric musi zawierać co najmniej trzy węzły. Do tej sekcji można dodać więcej węzłów zgodnie z konfiguracją. W poniższej tabeli objaśniono ustawienia konfiguracji dla każdego węzła:

| **Konfiguracja węzła** | **Opis** |
| --- | --- |
| nodeName |Do węzła można nadać dowolną przyjazną nazwę. |
| Adresu |Sprawdź adres IP węzła, otwierając okno wiersza polecenia i wpisując `ipconfig` . Zanotuj adres IPV4 i przypisz go do zmiennej iPAddress. |
| nodeTypeRef |Do każdego węzła można przypisać inny typ węzła. [Typy węzłów](#node-types) są zdefiniowane w poniższej sekcji. |
| faultDomain |Domeny błędów umożliwiają administratorom klastrów Definiowanie węzłów fizycznych, które mogą się kończyć niepowodzeniem w tym samym czasie, ze względu na współużytkowane zależności fizyczne. |
| upgradeDomain |Domeny uaktualnień opisują zestawy węzłów, które są zamykane do Service Fabric uaktualnień w tym samym czasie. Można wybrać węzły, które mają zostać przypisane do których domen uaktualnienia, ponieważ nie są ograniczone przez wymagania fizyczne. |

## <a name="cluster-properties"></a>Właściwości klastra
Sekcja właściwości w ClusterConfig.jsna służy do konfigurowania klastra, jak pokazano poniżej:

### <a name="reliability"></a>Niezawodność
Pojęcie reliabilityLevel definiuje liczbę replik lub wystąpień usług systemu Service Fabric, które mogą być uruchamiane na głównych węzłach klastra. Określa niezawodność tych usług i w związku z tym klaster. Wartość jest obliczana przez system podczas tworzenia klastra i czasu uaktualniania.

### <a name="diagnostics"></a>Diagnostyka
W sekcji diagnosticsStore można skonfigurować parametry w celu włączenia diagnostyki i rozwiązywania problemów z awariami węzłów lub klastrów, jak pokazano w poniższym fragmencie kodu: 

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "FileShare",
    "IsEncrypted": "false",
    "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
}
```

Metadane są opisami diagnostyki klastra i można je ustawić zgodnie z konfiguracją. Te zmienne pomagają zbierać dzienniki śledzenia ETW i Zrzuty awaryjne oraz liczniki wydajności. Aby uzyskać więcej informacji na temat dzienników śledzenia ETW, zobacz [tracelog](/windows-hardware/drivers/devtest/tracelog) and [ETW Tracing](/dotnet/framework/wcf/samples/etw-tracing). Wszystkie dzienniki, w tym [Zrzuty awaryjne](https://techcommunity.microsoft.com/t5/ask-the-performance-team/bg-p/AskPerf) i [liczniki wydajności](/windows/win32/perfctrs/performance-counters-portal), można skierować do folderu ConnectionString na komputerze. Można również użyć AzureStorage do przechowywania danych diagnostycznych. Zobacz następujący fragment przykładu:

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "AzureStorage",
    "IsEncrypted": "false",
    "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
}
```

### <a name="security"></a>Zabezpieczenia
Sekcja zabezpieczenia jest niezbędna dla klastra autonomicznego Service Fabric. Poniższy fragment kodu przedstawia część tej sekcji:

```json
"security": {
    "metadata": "This cluster is secured using X509 certificates.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    . . .
}
```

Metadane są opisem bezpiecznego klastra i można je ustawić zgodnie z konfiguracją. ClusterCredentialType i ServerCredentialType określają typ zabezpieczeń, które implementuje klaster i węzły. Można je ustawić na wartość *x509* w przypadku zabezpieczeń opartych na certyfikatach lub *systemu Windows* na potrzeby zabezpieczeń opartych na Active Directory. Pozostała część sekcji Security jest oparta na typie zabezpieczeń. Aby uzyskać informacje na temat sposobu wypełniania reszty sekcji Security, zobacz [zabezpieczenia oparte na certyfikatach w klastrze autonomicznym](service-fabric-windows-cluster-x509-security.md) lub [zabezpieczenia systemu Windows w klastrze autonomicznym](service-fabric-windows-cluster-windows-security.md).

### <a name="node-types"></a>Typy węzłów
Sekcja elementów NodeType opisuje typ węzłów, które zawiera klaster. Dla klastra należy określić co najmniej jeden typ węzła, jak pokazano w poniższym fragmencie kodu: 

```json
"nodeTypes": [{
    "name": "NodeType0",
    "clientConnectionEndpointPort": "19000",
    "clusterConnectionEndpointPort": "19001",
    "leaseDriverEndpointPort": "19002"
    "serviceConnectionEndpointPort": "19003",
    "httpGatewayEndpointPort": "19080",
    "reverseProxyEndpointPort": "19081",
    "applicationPorts": {
        "startPort": "20575",
        "endPort": "20605"
    },
    "ephemeralPorts": {
        "startPort": "20606",
        "endPort": "20861"
    },
    "isPrimary": true
}]
```

Nazwa jest przyjazną nazwą dla tego konkretnego typu węzła. Aby utworzyć węzeł tego typu węzła, przypisz jego przyjazną nazwę zmiennej nodeTypeRef dla tego węzła, jak [wspomniano powyżej](#nodes-on-the-cluster). Dla każdego typu węzła Zdefiniuj używane punkty końcowe połączenia. Można wybrać dowolny numer portu dla tych punktów końcowych połączenia, o ile nie powoduje konfliktu z innymi punktami końcowymi w tym klastrze. W przypadku klastra wielowęzłowego istnieje co najmniej jeden węzeł podstawowy (oznacza to, że właściwość isprimary jest ustawiona na *wartość true*), w zależności od [reliabilityLevel](#reliability). Aby dowiedzieć się więcej na temat podstawowych i niepodstawowych typów węzłów, zobacz [Service Fabric zagadnienia dotyczące planowania pojemności klastra](service-fabric-cluster-capacity.md) , aby uzyskać informacje na temat elementów NodeType i reliabilityLevel. 

#### <a name="endpoints-used-to-configure-the-node-types"></a>Punkty końcowe używane do konfigurowania typów węzłów
* clientConnectionEndpointPort to port używany przez klienta do nawiązywania połączenia z klastrem, gdy są używane interfejsy API klienta. 
* clusterConnectionEndpointPort to port, do którego węzły komunikują się ze sobą.
* leaseDriverEndpointPort jest portem używanym przez sterownik dzierżawy klastra do sprawdzenia, czy węzły są nadal aktywne. 
* serviceConnectionEndpointPort to port używany przez aplikacje i usługi wdrożone w węźle do komunikowania się z klientem Service Fabric w tym konkretnym węźle.
* httpGatewayEndpointPort to port używany przez Service Fabric Explorer do nawiązywania połączenia z klastrem.
* ephemeralPorts Zastąp [porty dynamiczne używane przez system operacyjny](https://support.microsoft.com/kb/929851). Service Fabric używa tych portów jako portów aplikacji, a pozostałe są dostępne dla systemu operacyjnego. Ponadto mapuje ten zakres na istniejący zakres obecny w systemie operacyjnym, dlatego można użyć zakresów podanej w przykładowych plikach JSON. Upewnij się, że różnica między portem początkowym i końcowym wynosi co najmniej 255. Mogą wystąpić konflikty, jeśli różnica jest zbyt niska, ponieważ ten zakres jest współużytkowany z systemem operacyjnym. Aby zobaczyć skonfigurowany zakres portów dynamicznych, uruchom polecenie `netsh int ipv4 show dynamicport tcp` .
* applicationPorts są portami używanymi przez aplikacje Service Fabric. Zakres portów aplikacji powinien być wystarczająco duży, aby pokryć wymagania dotyczące punktu końcowego aplikacji. Ten zakres powinien być poza zakresem portów dynamicznych na komputerze, czyli zakresem ephemeralPorts ustawionym w konfiguracji. Service Fabric używa tych portów, gdy wymagane są nowe porty i należy zachować ostrożność otwierania zapory dla tych portów. 
* reverseProxyEndpointPort jest opcjonalnym punktem końcowym zwrotnego serwera proxy. Aby uzyskać więcej informacji, zobacz [Service Fabric zwrotny serwer proxy](service-fabric-reverseproxy.md). 

### <a name="log-settings"></a>Ustawienia dziennika
W sekcji fabricSettings można ustawić katalogi główne dla Service Fabric danych i dzienników. Te katalogi można dostosować tylko podczas początkowego tworzenia klastra. Zobacz następujący fragment przykładu w tej sekcji:

```json
"fabricSettings": [{
    "name": "Setup",
    "parameters": [{
        "name": "FabricDataRoot",
        "value": "C:\\ProgramData\\SF"
    }, {
        "name": "FabricLogRoot",
        "value": "C:\\ProgramData\\SF\\Log"
}]
```

Zalecamy używanie dysku innego niż OS jako FabricDataRoot i FabricLogRoot. Zapewnia większą niezawodność w sytuacjach, gdy system operacyjny przestanie odpowiadać. W przypadku dostosowania tylko katalogu głównego danych główny Dziennik zostanie umieszczony na poziomie poniżej katalogu głównego danych.

### <a name="stateful-reliable-services-settings"></a>Ustawienia stanowe Reliable Services
W sekcji KtlLogger można ustawić globalne ustawienia konfiguracji Reliable Services. Aby uzyskać więcej informacji na temat tych ustawień, zobacz [Konfigurowanie stanowego Reliable Services](service-fabric-reliable-services-configuration.md). Poniższy przykład pokazuje, jak zmienić udostępniony dziennik transakcji tworzony w celu przywrócenia wszelkich niezawodnych kolekcji dla usług stanowych:

```json
"fabricSettings": [{
    "name": "KtlLogger",
    "parameters": [{
        "name": "SharedLogSizeInMB",
        "value": "4096"
    }]
}]
```

### <a name="add-on-features"></a>Funkcje dodatków
Aby skonfigurować funkcje dodatków, skonfiguruj apiVersion jako 04-2017 lub wyższej i skonfiguruj addonFeatures, jak pokazano poniżej:

```json
"apiVersion": "04-2017",
"properties": {
    "addOnFeatures": [
        "DnsService",
        "RepairManager"
    ]
}
```
Wszystkie dostępne funkcje dodatków można zobaczyć w [dokumentacji interfejsu API REST Service Fabric](/rest/api/servicefabric/sfrp-model-addonfeatures).

### <a name="container-support"></a>Obsługa kontenerów
Aby włączyć obsługę kontenerów dla kontenerów systemu Windows Server i kontenerów funkcji Hyper-V dla klastrów autonomicznych, należy włączyć funkcję dodatku DnsService.

## <a name="next-steps"></a>Następne kroki
Po zakończeniu *ClusterConfig.jsna* pliku skonfigurowanym zgodnie z konfiguracją klastra autonomicznego można wdrożyć klaster. Wykonaj kroki opisane w temacie [Tworzenie autonomicznego klastra Service Fabric](service-fabric-cluster-creation-for-windows-server.md). 

W przypadku wdrożenia klastra autonomicznego można także [uaktualnić konfigurację klastra autonomicznego](service-fabric-cluster-config-upgrade-windows-server.md). 

Dowiedz się, jak [wizualizować klaster za pomocą Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
