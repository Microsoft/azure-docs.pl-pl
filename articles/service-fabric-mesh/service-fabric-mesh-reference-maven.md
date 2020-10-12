---
title: Informacje o Maven siatki platformy Azure Service Fabric
description: Zawiera informacje dotyczące korzystania z wtyczki Maven dla siatki Service Fabric
author: suhuruli
ms.author: suhuruli
ms.date: 11/26/2018
ms.topic: reference
ms.custom: devx-track-java
ms.openlocfilehash: 3a1aa004f47ba700ef4b96004dfe5b835788dcc7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87372471"
---
# <a name="maven-plugin-for-service-fabric-mesh"></a>Wtyczka Maven dla Service Fabric siatki

## <a name="prerequisites"></a>Wymagania wstępne

- Zestaw SDK Java
- Maven
- Interfejs wiersza polecenia platformy Azure z rozszerzeniem siatki
- Interfejs wiersza polecenia usługi Service Fabric

## <a name="goals"></a>Cele

### `azure-sfmesh:init`
- Tworzy `servicefabric` folder zawierający `appresources` folder, w którym znajduje się `application.yaml` plik. 

### `azure-sfmesh:addservice`
- Tworzy folder wewnątrz `servicefabric` folderu z nazwą usługi i tworzy plik YAML usługi. 

### `azure-sfmesh:addnetwork`
- Generuje `network` YAML z podaną nazwą sieciową w `appresources` folderze 

### `azure-sfmesh:addgateway`
- Generuje `gateway` YAML z podaną nazwą bramy w `appresources` folderze 

#### `azure-sfmesh:addvolume`
- Generuje `volume` YAML o podanej nazwie woluminu w `appresources` folderze.

### `azure-sfmesh:addsecret`
- Generuje `secret` YAML o podanej nazwie klucza tajnego w `appresources` folderze 

### `azure-sfmesh:addsecretvalue`
- Generuje `secretvalue` YAML o podanej nazwie tajnej i wartości tajnej w `appresources` folderze 

### `azure-sfmesh:deploy`
- Scala yamls z `servicefabric` folderu i tworzy plik JSON szablonu Azure Resource Manager w bieżącym folderze.
- Wdraża wszystkie zasoby w środowisku siatki usługi Azure Service Fabric 

### `azure-sfmesh:deploytocluster`
- Tworzy folder ( `meshDeploy` ), który zawiera dane JSON wdrożenia wygenerowane z yamls, które mają zastosowanie do klastrów Service Fabric
- Wdraża wszystkie zasoby w klastrze Service Fabric
 

## <a name="usage"></a>Użycie

Aby używać wtyczki programu Maven w aplikacji Java programu Maven, dodaj poniższy fragment kodu do pliku pom.xml:

```XML
<project>
  ...
  <build>
    ...
    <plugins>
      ...
      <plugin>
        <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-sfmesh-maven-plugin</artifactId>
          <version>0.1.0</version>
      </plugin>
    </plugins>
  </build>
</project>
```

## <a name="common-configuration"></a>Typowa konfiguracja

Wtyczka Maven obecnie nie obsługuje wspólnych konfiguracji wtyczek Maven dla platformy Azure.

## <a name="how-to"></a>Instrukcje

### <a name="initialize-maven-project-for-azure-service-fabric-mesh"></a>Zainicjuj projekt Maven dla siatki Service Fabric platformy Azure
Uruchom następujące polecenie, aby utworzyć plik YAML zasobów aplikacji.

```cmd
mvn azure-sfmesh:init -DapplicationName=helloworldserver
```

- Tworzy folder o nazwie `servicefabric->appresources` w folderze głównym, który zawiera aplikację YAML o nazwie `app_helloworldserver`

### <a name="add-resource-to-your-application"></a>Dodawanie zasobu do aplikacji

#### <a name="add-a-new-network-to-your-application"></a>Dodawanie nowej sieci do aplikacji
Uruchom poniższe polecenie, aby utworzyć zasób sieciowy YAML. 

```cmd
mvn azure-sfmesh:addnetwork -DnetworkName=helloworldservicenetwork -DnetworkAddressPrefix=10.0.0.0/22
```

- Tworzy YAML sieci w folderze `servicefabric->appresources` o nazwie `network_helloworldservicenetwork`

#### <a name="add-a-new-service-to-your-application"></a>Dodawanie nowej usługi do aplikacji
Uruchom poniższe polecenie, aby utworzyć YAML usługi. 

```cmd
mvn azure-sfmesh:addservice -DapplicationName=helloworldserver -DserviceName=helloworldservice -DimageName=helloworldserver:latest -DlistenerPort=8080 -DnetworkRef=helloworldservicenetwork
```

- Tworzy YAML usługi w folderze `servicefabric->helloworldservice` o nazwie `service_helloworldservice` odwołującej się do `helloworldservicenetwork` `helloworldserver` aplikacji &
- Usługa nasłuchuje na porcie 8080
- Usługa będzie używać ***helloworldserver: Najnowsza*** jako obrazu kontenera.

#### <a name="add-a-new-gateway-resource-to-your-application"></a>Dodawanie nowego zasobu bramy do aplikacji
Uruchom poniższe polecenie, aby utworzyć zasób bramy YAML. 

```cmd
mvn azure-sfmesh:addgateway -DapplicationName=helloworldserver -DdestinationNetwork=helloworldservicenetwork -DgatewayName=helloworldgateway -DlistenerName=helloworldserviceListener -DserviceName=helloworldservice -DsourceNetwork=open -DtcpPort=80
```

- Tworzy nową bramę YAML w folderze `servicefabric->appresources` o nazwie `gateway_helloworldgateway`
- Odwołania `helloworldservicelistener` jako odbiornik usługi, który nasłuchuje wywołań z tej bramy. Odwołuje się również do `helloworldservice` usługi jako `helloworldservicenetwork` sieci i `helloworldserver` aplikacji. 
- Nasłuchuje żądań na porcie 80

#### <a name="add-a-new-volume-to-your-application"></a>Dodawanie nowego woluminu do aplikacji
Uruchom poniższe polecenie, aby utworzyć zasób woluminu YAML. 

```cmd
mvn azure-sfmesh:addvolume -DvolumeAccountKey=key -DvolumeAccountName=name -DvolumeName=vol1 -DvolumeShareName=share
```

- Tworzy wolumin YAML w folderze `servicefabric->appresources` o nazwie `volume_vol1`
- Ustawia właściwości wymaganych parametrów, `volumeAccountKey` i `volumeShareName` jak powyżej
- Więcej informacji na temat sposobu odwoływania się do utworzonego woluminu można znaleźć w następujących tematach: [wdrażanie aplikacji przy użyciu woluminu Azure Files](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)

#### <a name="add-a-new-secret-resource-to-your-application"></a>Dodawanie nowego zasobu tajnego do aplikacji
Uruchom poniższe polecenie, aby utworzyć tajne YAML zasobów. 

```cmd
mvn azure-sfmesh:addsecret -DsecretName=secret1
```

- Tworzy wpis tajny YAML w folderze `servicefabric->appresources` o nazwie `secret_secret1`
- Aby uzyskać więcej informacji na temat sposobu odwoływania się do utworzonego klucza tajnego [,](service-fabric-mesh-howto-manage-secrets.md) zapoznaj się z poniższymi tematami.

#### <a name="add-a-new-secretvalue-resource-to-your-application"></a>Dodawanie nowego zasobu secretvalue do aplikacji
Uruchom poniższe polecenie, aby utworzyć zasób secretvalue YAML. 

```cmd
mvn azure-sfmesh:addsecretvalue -DsecretValue=someVal -DsecretValueName=secret1/v1
```

- Utwórz element secretvalue YAML w folderze `servicefabric->appresources` o nazwie `secretvalue_secret1_v1`

### <a name="run-the-application-locally"></a>Lokalne uruchamianie aplikacji

Aby uzyskać pomoc dotyczącą celu `azure-sfmesh:deploytocluster` , możesz uruchomić aplikację lokalnie przy użyciu poniższego polecenia:

```cmd
mvn azure-sfmesh:deploytocluster
```

Domyślnie ten cel wdraża zasoby w lokalnym klastrze. W przypadku wdrażania w klastrze lokalnym zakłada się, że klaster Service Fabric jest uruchomiony lokalnie. Lokalny klaster Service Fabric dla zasobów jest obecnie obsługiwany tylko w [systemie Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

- Tworzy dane JSON z yamls, które mają zastosowanie do klastrów Service Fabric
- Wdraża następnie do punktu końcowego klastra

### <a name="deploy-application-to-azure-service-fabric-mesh"></a>Wdrażanie aplikacji na platformie Azure Service Fabric siatką

Aby uzyskać pomoc dotyczącą celu `azure-sfmesh:deploy` , możesz wdrożyć program do Service Fabric środowiska siatki, uruchamiając poniższe polecenie:

```cmd
mvn azure-sfmesh:deploy -DresourceGroup=todoapprg -Dlocation=eastus
```

- Tworzy grupę zasobów o nazwie `todoapprg` , jeśli nie istnieje.
- Tworzy plik JSON szablonu Azure Resource Manager przez scalenie YAMLs. 
- Wdraża kod JSON w środowisku siatki usługi Azure Service Fabric.
