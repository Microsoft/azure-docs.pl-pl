---
title: Cloud Services (klasyczny) konfiguracja roli XPath Ściągawka arkusz | Microsoft Docs
description: Różne ustawienia XPath, których można użyć w konfiguracji roli usługi w chmurze w celu udostępnienia ustawień jako zmiennej środowiskowej.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 397bc6845dc8d2d8bc44c00c27f6c12037651337
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98741387"
---
# <a name="expose-role-configuration-settings-as-an-environment-variable-with-xpath"></a>Uwidacznianie ustawień konfiguracji roli jako zmiennej środowiskowej przy użyciu wyrażenia XPath

> [!IMPORTANT]
> [Azure Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md) to nowy model wdrażania oparty na Azure Resource Manager dla produktu Cloud Services platformy Azure.Ta zmiana spowoduje, że usługa Azure Cloud Services uruchomiona w ramach modelu wdrażania opartego na usłudze Azure Service Manager została zmieniona jako Cloud Services (klasyczny), a wszystkie nowe wdrożenia powinny używać [Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md).

W pliku definicji procesu roboczego usługi w chmurze lub usługi roli sieci Web można uwidocznić wartości konfiguracji środowiska uruchomieniowego jako zmienne środowiskowe. Obsługiwane są następujące wartości XPath (które odpowiadają wartościom interfejsu API).

Te wartości XPath są również dostępne w bibliotece [Microsoft. windowsazure. serviceruntime](/previous-versions/azure/reference/ee773173(v=azure.100)) . 

## <a name="app-running-in-emulator"></a>Aplikacja uruchomiona w emulatorze
Wskazuje, że aplikacja działa w emulatorze.

| Typ | Przykład |
| --- | --- |
| XPath |XPath = " /RoleEnvironment/Deployment/@emulated " |
| Kod |var x = RoleEnvironment. isemulowane; |

## <a name="deployment-id"></a>Identyfikator wdrożenia
Pobiera identyfikator wdrożenia dla tego wystąpienia.

| Typ | Przykład |
| --- | --- |
| XPath |XPath = " /RoleEnvironment/Deployment/@id " |
| Kod |var deploymentId = RoleEnvironment. DeploymentId; |

## <a name="role-id"></a>Identyfikator roli
Pobiera bieżący identyfikator roli dla tego wystąpienia.

| Typ | Przykład |
| --- | --- |
| XPath |XPath = " /RoleEnvironment/CurrentInstance/@id " |
| Kod |Identyfikator var = RoleEnvironment.CurrentRoleInstance.Id; |

## <a name="update-domain"></a>Aktualizowanie domeny
Pobiera domenę aktualizacji wystąpienia.

| Typ | Przykład |
| --- | --- |
| XPath |XPath = " /RoleEnvironment/CurrentInstance/@updateDomain " |
| Kod |var ud = RoleEnvironment. CurrentRoleInstance. UpdateDomain; |

## <a name="fault-domain"></a>Domena błędów
Pobiera domenę błędów wystąpienia.

| Typ | Przykład |
| --- | --- |
| XPath |XPath = " /RoleEnvironment/CurrentInstance/@faultDomain " |
| Kod |var FD = RoleEnvironment. CurrentRoleInstance. FaultDomain; |

## <a name="role-name"></a>Nazwa roli
Pobiera nazwę roli wystąpień.

| Typ | Przykład |
| --- | --- |
| XPath |XPath = " /RoleEnvironment/CurrentInstance/@roleName " |
| Kod |var rname = RoleEnvironment.CurrentRoleInstance.Role.Name; |

## <a name="config-setting"></a>Ustawienia konfiguracji
Pobiera wartość określonego ustawienia konfiguracji.

| Typ | Przykład |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/CurrentInstance/ConfigurationSettings/ConfigurationSetting [ @name =" Setting1 "] /@value " |
| Kod |ustawienie var = RoleEnvironment. GetConfigurationSettingValue ("Setting1"); |

## <a name="local-storage-path"></a>Ścieżka do magazynu lokalnego
Pobiera lokalną ścieżkę magazynu dla tego wystąpienia.

| Typ | Przykład |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/CurrentInstance/LocalResources/LocalResource [ @name =" LocalStore1 "] /@path " |
| Kod |var localResourcePath = RoleEnvironment. GetLocalResource ("LocalStore1"). Właściwość RootPath |

## <a name="local-storage-size"></a>Rozmiar magazynu lokalnego
Pobiera rozmiar lokalnego magazynu dla tego wystąpienia.

| Typ | Przykład |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/CurrentInstance/LocalResources/LocalResource [ @name =" LocalStore1 "] /@sizeInMB " |
| Kod |var localResourceSizeInMB = RoleEnvironment. GetLocalResource ("LocalStore1"). MaximumSizeInMegabytes; |

## <a name="endpoint-protocol"></a>Protokół punktu końcowego
Pobiera protokół punktu końcowego dla tego wystąpienia.

| Typ | Przykład |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/CurrentInstance/Endpoints/Endpoint [ @name =" Endpoint1 "] /@protocol " |
| Kod |var Prot = RoleEnvironment. CurrentRoleInstance. InstanceEndpoints ["Endpoint1"]. Protokol |

## <a name="endpoint-ip"></a>Adres IP punktu końcowego
Pobiera adres IP określonego punktu końcowego.

| Typ | Przykład |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/CurrentInstance/Endpoints/Endpoint [ @name =" Endpoint1 "] /@address " |
| Kod |var Address = RoleEnvironment. CurrentRoleInstance. InstanceEndpoints ["Endpoint1"]. IPEndpoint. Address |

## <a name="endpoint-port"></a>Port punktu końcowego
Pobiera Port punktu końcowego dla tego wystąpienia.

| Typ | Przykład |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/CurrentInstance/Endpoints/Endpoint [ @name =" Endpoint1 "] /@port " |
| Kod |var port = RoleEnvironment. CurrentRoleInstance. InstanceEndpoints ["Endpoint1"]. IPEndpoint. Port; |

## <a name="example"></a>Przykład
Poniżej przedstawiono przykład roli procesu roboczego, która tworzy zadanie uruchamiania z zmienną środowiskową o nazwie `TestIsEmulated` Set na [ @emulated wartość XPath](#app-running-in-emulator). 

```xml
<WorkerRole name="Role1">
    <ConfigurationSettings>
      <Setting name="Setting1" />
    </ConfigurationSettings>
    <LocalResources>
      <LocalStorage name="LocalStore1" sizeInMB="1024"/>
    </LocalResources>
    <Endpoints>
      <InternalEndpoint name="Endpoint1" protocol="tcp" />
    </Endpoints>
    <Startup>
      <Task commandLine="example.cmd inputParm">
        <Environment>
          <Variable name="TestConstant" value="Constant"/>
          <Variable name="TestEmptyValue" value=""/>
          <Variable name="TestIsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
          </Variable>
          ...
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="TestConstant" value="Constant"/>
        <Variable name="TestEmptyValue" value=""/>
        <Variable name="TestIsEmulated">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
        </Variable>
        ...
      </Environment>
    </Runtime>
    ...
</WorkerRole>
```

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o pliku [ServiceConfiguration. cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) .

Utwórz pakiet [servicepackage. cspkg](cloud-services-model-and-package.md#servicepackagecspkg) .

Włącz [pulpit zdalny](cloud-services-role-enable-remote-desktop-new-portal.md) dla roli.




