---
title: Określ numer portu usługi przy użyciu parametrów
description: Pokazuje, jak użyć parametrów, aby określić port dla aplikacji w Service Fabric
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: ba2fb459dc9c981ad168aca4d0edf969650ccf48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576710"
---
# <a name="how-to-specify-the-port-number-of-a-service-using-parameters-in-service-fabric"></a>Jak określić numer portu usługi przy użyciu parametrów w Service Fabric

W tym artykule pokazano, jak określić numer portu usługi przy użyciu parametrów w Service Fabric przy użyciu programu Visual Studio.

## <a name="procedure-for-specifying-the-port-number-of-a-service-using-parameters"></a>Procedura określania numeru portu usługi przy użyciu parametrów

W tym przykładzie ustawisz numer portu dla asp.net podstawowego interfejsu API sieci Web przy użyciu parametru.

1. Otwórz program Visual Studio i Utwórz nową aplikację Service Fabric.
1. Wybierz szablon ASP.NET Core bezstanowej.
1. Wybierz pozycję internetowy interfejs API.
1. Otwórz plik ServiceManifest.xml.
1. Zanotuj nazwę punktu końcowego określonego dla usługi. Wartość domyślna to `ServiceEndpoint`.
1. Otwórz plik ApplicationManifest.xml
1. W `ServiceManifestImport` elemencie Dodaj nowy `RessourceOverrides` element z odwołaniem do punktu końcowego w pliku ServiceManifest.xml.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
        <ResourceOverrides>
          <Endpoints>
            <Endpoint Name="ServiceEndpoint"/>
          </Endpoints>
        </ResourceOverrides>
        <ConfigOverrides />
      </ServiceManifestImport>
    ```

1. W `Endpoint` elemencie można teraz przesłonić dowolny atrybut przy użyciu parametru. W tym przykładzie określisz `Port` i ustawisz ją jako nazwę parametru przy użyciu nawiasów kwadratowych — na przykład `[MyWebAPI_PortNumber]`

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
        <ResourceOverrides>
          <Endpoints>
            <Endpoint Name="ServiceEndpoint" Port="[MyWebAPI_PortNumber]"/>
          </Endpoints>
        </ResourceOverrides>
        <ConfigOverrides />
      </ServiceManifestImport>
    ```

1. Nadal w pliku ApplicationManifest.xml, należy określić parametr w `Parameters` elemencie

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" />
      </Parameters>
    ```

1. I Zdefiniuj `DefaultValue`

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" DefaultValue="8080" />
      </Parameters>
    ```

1. Otwórz folder elementu applicationparameters i `Cloud.xml` plik
1. Aby określić inny port do użycia podczas publikowania w klastrze zdalnym, należy dodać parametr z numerem portu do tego pliku.

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" Value="80" />
      </Parameters>
    ```

Podczas publikowania aplikacji z programu Visual Studio przy użyciu Cloud.xml profilu publikowania usługa jest skonfigurowana do korzystania z portu 80. Jeśli aplikacja zostanie wdrożona bez określenia parametru MyWebAPI_PortNumber, usługa korzysta z portu 8080.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat podstawowych pojęć omówionych w tym artykule, zobacz [artykuł Zarządzanie aplikacjami dla wielu środowisk](service-fabric-manage-multiple-environment-app-configuration.md).

Aby uzyskać informacje na temat innych funkcji zarządzania aplikacjami, które są dostępne w programie Visual Studio, zobacz [Zarządzanie aplikacjami Service Fabric w programie Visual Studio](service-fabric-manage-application-in-visual-studio.md).
