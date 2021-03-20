---
title: Wyświetlanie dzienników kontenerów na platformie Azure Service Fabric
description: Opisuje sposób wyświetlania dzienników kontenerów dla uruchomionych usług kontenera Service Fabric przy użyciu Service Fabric Explorer.
ms.topic: conceptual
ms.date: 05/15/2018
ms.openlocfilehash: c47a408b272f95dbfcf3d791c644bfeb52254a72
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "75458185"
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Wyświetlanie dzienników usługi kontenera Service Fabric
Usługa Azure Service Fabric jest koordynatorem kontenera i obsługuje [kontenery systemu Linux i Windows](service-fabric-containers-overview.md).  W tym artykule opisano sposób wyświetlania dzienników kontenerów działającej usługi kontenera lub martwego kontenera, dzięki czemu można diagnozować i rozwiązywać problemy.

## <a name="access-the-logs-of-a-running-container"></a>Dostęp do dzienników działającego kontenera
Dostęp do dzienników kontenerów można uzyskać przy użyciu [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).  W przeglądarce sieci Web otwórz Service Fabric Explorer z punktu końcowego zarządzania klastrami, przechodząc do witryny `http://mycluster.region.cloudapp.azure.com:19080/Explorer` .  

Dzienniki kontenerów znajdują się w węźle klastra, na którym jest uruchomione wystąpienie usługi kontenera. Przykładowo można pobrać dzienniki kontenera frontonu sieci Web [aplikacji przykładowej głosowania systemu Linux](service-fabric-quickstart-containers-linux.md). W widoku drzewa rozwiń węzeł aplikacje **klastra** >  >  > **:/głosowania/azurevotefront**.  Następnie rozwiń partycję (d1aa737e-f22a-e347-BE16-eec90be24bc1 w tym przykładzie) i sprawdź, czy kontener jest uruchomiony w węźle klastra *_lnxvm_0*.

W widoku drzewa Znajdź pakiet kodu w węźle *_lnxvm_0* przez powiększanie **węzłów** > **_lnxvm_0** > **Sieć szkieletowa:/głosowania** >  > **kod pakietów kodu** azurevotfrontPkg > .  Następnie wybierz opcję **dzienniki kontenerów** , aby wyświetlić dzienniki kontenerów.

![Platforma Service Fabric][Image1]

## <a name="access-the-logs-of-a-dead-or-crashed-container"></a>Dostęp do dzienników martwego lub uszkodzonego kontenera
Począwszy od wersji 6.2, można również pobrać dzienniki dla uszkodzonego kontenera lub za pomocą [interfejsów API REST](/rest/api/servicefabric/sfclient-index) lub [Service Fabric interfejsu wiersza polecenia (SFCTL)](service-fabric-cli.md) .

### <a name="set-container-retention-policy"></a>Ustawianie zasad przechowywania kontenerów
Aby ułatwić diagnozowanie błędów uruchamiania kontenerów, usługa Service Fabric (w wersji 6.1 lub nowszej) obsługuje przechowywanie kontenerów, w przypadku których działanie zostało przerwane lub uruchamianie nie powiodło się. Te zasady można ustawić w pliku **ApplicationManifest.xml**, jak pokazano w poniższym fragmencie kodu:
```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
 ```

Ustawienie **ContainersRetentionCount** określa liczbę kontenerów do przechowywania w przypadku wystąpienia w nich błędu. Jeśli zostanie określona wartość ujemna, będą przechowywane wszystkie kontenery z błędami. Jeśli atrybut **ContainersRetentionCount** nie jest określony, kontenery nie będą zachowywane. Atrybut **ContainersRetentionCount** obsługuje też parametry aplikacji, dzięki czemu użytkownicy mogą określać różne wartości dla klastrów testowych i produkcyjnych. W przypadku używania tej funkcji można zastosować ograniczenia rozmieszczania, tak aby obiektem docelowym usługi kontenera był określony węzeł, co zapobiega przenoszeniu usługi kontenera do innych węzłów. Wszelkie kontenery przechowywane przy użyciu tej funkcji należy usunąć ręcznie.

Ustawienie **RunInteractive** odnosi się do flag platformy Docker `--interactive` i `tty` [](https://docs.docker.com/engine/reference/commandline/run/#options). Gdy to ustawienie ma wartość true w pliku manifestu, te flagi są używane do uruchamiania kontenera.  

### <a name="rest"></a>REST
Użyj operacji [Pobierz dzienniki kontenera wdrożone w węźle](/rest/api/servicefabric/sfclient-api-getcontainerlogsdeployedonnode) , aby pobrać dzienniki dla nieoczekiwanego kontenera. Określ nazwę węzła, na którym uruchomiono kontener, nazwę aplikacji, nazwę manifestu usługi i nazwę pakietu kodu.  Podaj wartość `&Previous=true`. Odpowiedź będzie zawierać dzienniki kontenerów dla martwego kontenera wystąpienia pakietu kodu.

Identyfikator URI żądania ma następującą formę:

```
/Nodes/{nodeName}/$/GetApplications/{applicationId}/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName={ServiceManifestName}&CodePackageName={CodePackageName}&Previous={Previous}
```

Przykładowe żądanie:
```
GET http://localhost:19080/Nodes/_Node_0/$/GetApplications/SimpleHttpServerApp/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName=SimpleHttpServerSvcPkg&CodePackageName=Code&Previous=true  
```

treść odpowiedzi 200:
```json
{   "Content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" } 
```

### <a name="service-fabric-sfctl"></a>Service Fabric (SFCTL)
Użyj polecenia [Get-Container-Logs usługi sfctl](service-fabric-sfctl-service.md) w celu pobrania dzienników dla kontenera, który uległ awarii.  Określ nazwę węzła, na którym uruchomiono kontener, nazwę aplikacji, nazwę manifestu usługi i nazwę pakietu kodu. Określ `--previous` flagę.  Odpowiedź będzie zawierać dzienniki kontenerów dla martwego kontenera wystąpienia pakietu kodu.

```
sfctl service get-container-logs --node-name _Node_0 --application-id SimpleHttpServerApp --service-manifest-name SimpleHttpServerSvcPkg --code-package-name Code –-previous
```
Odpowiedź:
```json
{   "content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" }
```

## <a name="next-steps"></a>Następne kroki
- Pracuj w [samouczku Tworzenie aplikacji kontenera systemu Linux](service-fabric-tutorial-create-container-images.md).
- Dowiedz się więcej o [Service Fabric i kontenerach](service-fabric-containers-overview.md)

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
