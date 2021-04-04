---
title: Określanie zmiennych środowiskowych dla usług
description: Pokazuje, jak używać zmiennych środowiskowych dla aplikacji w Service Fabric
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: 610e44ac98b8b8a2ce5a91fdbcfda145ae36a94b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576744"
---
# <a name="how-to-specify-environment-variables-for-services-in-service-fabric"></a>Jak określić zmienne środowiskowe dla usług w Service Fabric

W tym artykule opisano sposób określania zmiennych środowiskowych dla usługi lub kontenera w Service Fabric.

## <a name="procedure-for-specifying-environment-variables-for-services"></a>Procedura określania zmiennych środowiskowych dla usług

W tym przykładzie ustawisz zmienną środowiskową dla kontenera. W tym artykule założono, że masz już manifest aplikacji i usługi.

1. Otwórz plik ServiceManifest.xml.
2. W `CodePackage` elemencie Dodaj nowy `EnvironmentVariables` element i `EnvironmentVariable` element dla każdej zmiennej środowiskowej.

    ```xml
    <CodePackage Name="MyCode" Version="CodeVersion1">
            ...
            <EnvironmentVariables>
                  <EnvironmentVariable Name="MyEnvVariable" Value="DefaultValue"/>
                  <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentVariables>
    </CodePackage>
    ```

   Zmienne środowiskowe można przesłonić w manifeście aplikacji.

3. Aby zastąpić zmienne środowiskowe w manifeście aplikacji, użyj `EnvironmentOverrides` elementu.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestVersion="1.0.0" />
        <EnvironmentOverrides CodePackageRef="MyCode">
          <EnvironmentVariable Name="MyEnvVariable" Value="OverrideValue"/>
        </EnvironmentOverrides>
      </ServiceManifestImport>
    ```

## <a name="specifying-environment-variables-dynamically-using-docker-compose"></a>Dynamiczne określanie zmiennych środowiskowych za pomocą Docker Compose

Service Fabric obsługuje możliwość [użycia Docker Compose do wdrożenia](service-fabric-docker-compose.md#supported-compose-directives). Pliki redagowania mogą źródłowe zmienne środowiskowe z powłoki. To zachowanie może służyć do dynamicznego podstawiania żądanych wartości środowiska:

```yml
environment:
  - "hostname:${hostname}"
```

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat podstawowych pojęć omówionych w tym artykule, zobacz [artykuł Zarządzanie aplikacjami dla wielu środowisk](service-fabric-manage-multiple-environment-app-configuration.md).

Aby uzyskać informacje na temat innych funkcji zarządzania aplikacjami, które są dostępne w programie Visual Studio, zobacz [Zarządzanie aplikacjami Service Fabric w programie Visual Studio](service-fabric-manage-application-in-visual-studio.md).
