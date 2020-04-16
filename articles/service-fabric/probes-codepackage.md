---
title: Sondy sieci szkieletowej usług Azure
description: Jak modelować sondę żywotności w sieci szkieletowej usługi Azure przy użyciu plików manifestu aplikacji i usług.
ms.topic: conceptual
ms.date: 3/12/2020
ms.openlocfilehash: 38f3888a29bf505b723d40bc7cd08fb0c7e29eff
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431217"
---
# <a name="liveness-probe"></a>Sonda żywotności
Począwszy od sieci szkieletowej usług 7.1 obsługuje mechanizm liveness probe dla aplikacji [konteneryzowanych.][containers-introduction-link] Liveness Probe pomóc ogłosić żywotność konteneryzowanej aplikacji i gdy nie reagują w odpowiednim czasie, spowoduje to ponowne uruchomienie.
Ten artykuł zawiera omówienie sposobu definiowania sondy żywotności za pomocą plików manifestu.

Przed przystąpieniem do tego artykułu zalecamy zapoznanie się z [modelem aplikacji sieci szkieletowej usług][application-model-link] i [modelem hostingu sieci szkieletowej usług.][hosting-model-link]

> [!NOTE]
> Liveness Probe jest obsługiwany tylko dla kontenerów w trybie sieciowym NAT.

## <a name="semantics"></a>Semantyka
Można określić tylko 1 sonda żywotności na kontener i można kontrolować jego zachowanie za pomocą tych pól:

* `initialDelaySeconds`: Początkowe opóźnienie w sekundach, aby rozpocząć wykonywanie sondy po uruchomieniu kontenera. Obsługiwana wartość to int. Wartość domyślna to 0. Minimalna wartość to 0.

* `timeoutSeconds`: Okres w sekundach, po którym uważamy sondę za nieudaną, jeśli nie została ukończona pomyślnie. Obsługiwana wartość to int. Wartość domyślna to 1. Minimum to 1.

* `periodSeconds`: Okres w sekundach, aby określić, jak często sondujemy. Obsługiwana wartość to int. Wartość domyślna to 10. Minimum to 1.

* `failureThreshold`: Po trafieniu FailureThreshold kontener zostanie ponownie uruchomiony. Obsługiwana wartość to int. Wartość domyślna to 3. Minimum to 1.

* `successThreshold`: W przypadku awarii, dla sondy, które mają być uznane za sukces musi wykonać pomyślnie dla SuccessThreshold. Obsługiwana wartość to int. Wartość domyślna to 1. Minimum to 1.

W jednym momencie będzie co najwyżej 1 sonda do kontenera. Jeśli sonda nie zostanie ukończona w **przeliczeniu limitu czasuSekseksujmy** czeka i liczymy go w kierunku **awariiThreshold**. 

Ponadto ServiceFabric podniesie następujące [raporty kondycji][health-introduction-link] sondy na DeployedServicePackage:

* `Ok`: Jeśli sonda powiedzie się dla **sukcesuThreshold** następnie zgłaszamy kondycję jako Ok.

* `Error`: Jeśli sonda failureCount == **failureThreshold**, przed ponownym uruchomieniem kontenera zgłaszamy błąd.

* `Warning`: 
    1. Jeśli sonda nie powiedzie się i failureCount < **failureThreshold** zgłaszamy Warning. Ten raport o kondycji pozostaje do failureCount osiągnie **failureThreshold** lub **successThreshold**.
    2. Na sukces po niepowodzeniu, nadal raport Ostrzeżenie, ale z zaktualizowanym sukcesem z rzędu.

## <a name="specifying-liveness-probe"></a>Określanie sondy żywotności

Można określić sondę w applicationmanifest.xml w obszarze ServiceManifestImport:

Sonda może jedną z:

1. HTTP
2. TCP
3. Exec 

## <a name="http-probe"></a>Sonda HTTP

W przypadku sondy HTTP sieci szkieletowej usług zostanie wysłane żądanie HTTP do określonego portu i ścieżki. Kod zwrotny większy lub równy 200 i mniej niż 400 wskazuje na sukces.

Oto przykład jak określić HttpGet sondy:

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

Sonda HttpGet ma dodatkowe właściwości, które można ustawić:

* `path`: Ścieżka dostępu w żądaniu HTTP.

* `port`: Port dostępu do sond. Zakres wynosi od 1 do 65535. Obowiązkowy.

* `scheme`: Schemat do użycia do łączenia się z pakietem kodu. Jeśli ustawiona jest wartość HTTPS, weryfikacja certyfikatu jest pomijana. Domyślnie http

* `httpHeader`: Nagłówki ustawione w żądaniu. Można określić wiele z nich.

* `host`: Host IP, aby się połączyć.

## <a name="tcp-probe"></a>Sonda TCP

W przypadku sondy TCP sieci szkieletowej usług spróbuje otworzyć gniazdo w kontenerze z określonym portem. Jeśli można ustanowić połączenie, sonda jest uważany za sukces. Oto przykład sposobu określania sondy, która używa gniazda TCP:

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

## <a name="exec-probe"></a>Sonda Exec

Ta sonda wyda exec do kontenera i czekać na polecenie, aby zakończyć.

> [!NOTE]
> Polecenie Exec przyjmuje ciąg przecinkowy. Następujące polecenie w przykładzie będzie działać dla kontenera systemu Linux.
> Jeśli próbujesz kontenera systemu Windows, <Command>użyj</Command>

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
Zobacz następujące artykuły, aby uzyskać powiązane informacje.
* [Tkanina serwisowa i kontenery.][containers-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[health-introduction-link]: service-fabric-health-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md

