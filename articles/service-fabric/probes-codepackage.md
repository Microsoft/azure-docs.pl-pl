---
title: Sondy usługi Azure Service Fabric
description: Jak modelować sondę na żywo w usłudze Azure Service Fabric przy użyciu plików manifestu aplikacji i usługi.
ms.topic: conceptual
author: tugup
ms.author: tugup
ms.date: 3/12/2020
ms.openlocfilehash: 07a1b836ca7ea79244e303f54654dfcaa6e5fcb9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "82137590"
---
# <a name="liveness-probe"></a>Sonda na żywo
Począwszy od wersji 7,1, platforma Azure Service Fabric obsługuje mechanizm sondowania na żywo dla aplikacji [kontenerowych][containers-introduction-link] . Sonda na żywo pomaga w raportowaniu na żywo aplikacji kontenera, która zostanie uruchomiona ponownie, jeśli nie odpowie szybko.
Ten artykuł zawiera omówienie sposobu definiowania sondy na żywo przy użyciu plików manifestu.

Przed przejściem do tego artykułu zapoznaj się z [modelem aplikacji Service Fabric][application-model-link] i [Service Fabric modelem hostingu][hosting-model-link].

> [!NOTE]
> Sonda na żywo jest obsługiwana tylko w przypadku kontenerów w trybie sieci NAT.

## <a name="semantics"></a>Semantyka
Można określić tylko jedną sondę na żywo na kontener i kontrolować jej zachowanie przy użyciu następujących pól:

* `initialDelaySeconds`: Początkowe opóźnienie (w sekundach) uruchomienia sondy po rozpoczęciu kontenera. Obsługiwana wartość to **int**. Wartość domyślna to 0, a wartość minimalna to 0.

* `timeoutSeconds`: Okres (w sekundach), po którym uważamy, że sonda zakończyła się niepowodzeniem, jeśli nie została ukończona pomyślnie. Obsługiwana wartość to **int**. Wartość domyślna to 1, a wartość minimalna to 1.

* `periodSeconds`: Okres (w sekundach) określający częstotliwość sondowania. Obsługiwana wartość to **int**. Wartość domyślna to 10, a wartość minimalna to 1.

* `failureThreshold`: Gdy ta wartość zostanie osiągnięta, kontener zostanie uruchomiony ponownie. Obsługiwana wartość to **int**. Wartość domyślna to 3, a wartość minimalna to 1.

* `successThreshold`: W przypadku niepowodzenia, aby sonda mogła zostać uznana za pomyślne, musi ona zostać pomyślnie uruchomiona dla tej wartości. Obsługiwana wartość to **int**. Wartość domyślna to 1, a wartość minimalna to 1.

W każdej chwili może istnieć co najwyżej jedna sonda do jednego kontenera. Jeśli sonda nie zakończy się w czasie ustawionym w **timeoutSeconds**, poczekaj i Policz czas na **failureThreshold**. 

Ponadto Service Fabric spowodują [wygenerowanie następujących raportów o kondycji][health-introduction-link] sond w **DeployedServicePackage**:

* `OK`: Sonda powiodła się dla wartości ustawionej w **successThreshold**.

* `Error`: Sonda **failureCount**  ==   **failureThreshold**przed ponownym uruchomieniem kontenera.

* `Warning`: 
    * Sonda kończy się niepowodzeniem i **failureCount**  <  **failureThreshold**. Ten raport kondycji pozostaje do momentu, gdy **failureCount** osiągnie wartość ustawioną w **failureThreshold** lub **successThreshold**.
    * Po pomyślnym wystąpieniu błędu ostrzeżenie pozostaje niezmienione, ale z zaktualizowanymi kolejnymi sukcesami.

## <a name="specifying-a-liveness-probe"></a>Określanie sondy na żywo

Możesz określić sondę w pliku ApplicationManifest.xml w obszarze **ServiceManifestImport**.

Sonda może mieć jedną z następujących wartości:

* HTTP
* TCP
* Exec 

### <a name="http-probe"></a>Sonda HTTP

W przypadku sondy HTTP Service Fabric wyśle żądanie HTTP do określonego portu i ścieżki. Kod powrotny, który jest większy niż lub równy 200 i mniejszy niż 400, wskazuje na powodzenie.

Oto przykład sposobu określania sondy HTTP:

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <HttpGet Path="/" Port="8081" Scheme="http">
              <HttpHeader Name="Foo" Value="Val"/>
              <HttpHeader Name="Bar" Value="val1"/>
            </HttpGet>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

Sonda HTTP ma dodatkowe właściwości, które można ustawić:

* `path`: Ścieżka do użycia w żądaniu HTTP.

* `port`: Port używany do sondowania. Ta właściwość jest obowiązkowa. Zakres wynosi od 1 do 65535.

* `scheme`: Schemat, który ma być używany do nawiązywania połączenia z pakietem kodu. Jeśli ta właściwość jest ustawiona na HTTPS, weryfikacja certyfikatu jest pomijana. Ustawieniem domyślnym jest HTTP.

* `httpHeader`: Nagłówki, które mają zostać ustawione w żądaniu. Można określić wiele nagłówków.

* `host`: Adres IP hosta, z którym ma zostać nawiązane połączenie.

### <a name="tcp-probe"></a>Sonda TCP

W przypadku sondy TCP Service Fabric próbuje otworzyć gniazdo w kontenerze przy użyciu określonego portu. Jeśli może nawiązać połączenie, sonda zostanie uznana za pomyślnie. Oto przykład sposobu określania sondy korzystającej z gniazda TCP:

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <TcpSocket Port="8081"/>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

### <a name="exec-probe"></a>Sonda exec

To sondowanie spowoduje wydanie polecenia **exec** do kontenera i poczekanie na zakończenie polecenia.

> [!NOTE]
> **Exec** polecenie pobiera ciąg rozdzielony przecinkami. Polecenie w poniższym przykładzie będzie działało dla kontenera systemu Linux.
> Jeśli próbujesz sondować kontener systemu Windows, użyj **polecenia cmd**.

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <Exec>
              <Command>ping,-c,2,localhost</Command>
            </Exec>
          </Probe>        
       </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

## <a name="next-steps"></a>Następne kroki
Informacje pokrewne można znaleźć w następującym artykule:
* [Service Fabric i kontenery][containers-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[health-introduction-link]: service-fabric-health-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md

