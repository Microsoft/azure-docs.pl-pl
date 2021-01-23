---
title: Co to jest model i pakiet usługi w chmurze platformy Azure (obsługa rozszerzona)
description: Opisuje model usługi w chmurze (obsługa rozszerzona) (. csdef,. cscfg) i pakiet (. cspkg) na platformie Azure
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 3baea92d78cf15c35d2fb36692dab1edfd950699
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744519"
---
# <a name="what-is-the-azure-cloud-service-model-and-how-do-i-package-it"></a>Co to jest model usługi w chmurze platformy Azure i jak go spakować?

Usługa w chmurze jest tworzona na podstawie trzech składników: definicji usługi *(. csdef)*, konfiguracji usługi *(. cscfg)* i pakietu usługi *(. cspkg)*. Pliki **ServiceDefinition. csdef** i **ServiceConfig. cscfg** są oparte na języku XML i opisują strukturę usługi w chmurze oraz sposób jej konfiguracji; nazywana zbiorczo modelem. **Servicepackage. cspkg** to plik zip, który jest generowany na podstawie elementu **ServiceDefinition. csdef** i między innymi, zawiera wszystkie wymagane zależności oparte na plikach binarnych. Platforma Azure tworzy usługę w chmurze zarówno z **servicepackage. cspkg** , jak i **ServiceConfig. cscfg**.

Gdy usługa w chmurze jest uruchomiona na platformie Azure, można ją skonfigurować ponownie za pomocą pliku **ServiceConfig. cscfg** , ale nie można zmienić definicji.

## <a name="what-would-you-like-to-know-more-about"></a>Jak chcesz dowiedzieć się więcej?
* Chcę dowiedzieć się więcej na temat plików [ServiceDefinition. csdef](#csdef) i [ServiceConfig. cscfg](#cscfg) .
* Już wie o tym, co można zrobić, aby uzyskać więcej [przykładów](#next-steps) na temat konfiguracji.
* Chcę utworzyć [servicepackage. cspkg](#cspkg).
* Używam programu Visual Studio i chcę...
  * [Tworzenie usługi w chmurze] [vs_create]
  * [Zmień konfigurację istniejącej usługi w chmurze] [vs_reconfigure]
  * [Wdrażanie projektu usługi w chmurze] [vs_deploy]
  * [Pulpit zdalny do wystąpienia usługi w chmurze] [remotedesktop]

<a name="csdef"></a>

## <a name="servicedefinitioncsdef"></a>ServiceDefinition. csdef
Plik **ServiceDefinition. csdef** określa ustawienia, które są używane przez platformę Azure do konfigurowania usługi w chmurze. [Schemat definicji usługi platformy Azure (plik csdef)](schema-csdef-file.md) zawiera dozwolony format dla pliku definicji usługi. W poniższym przykładzie przedstawiono ustawienia, które można zdefiniować dla ról Sieć Web i proces roboczy:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalHttpIn" protocol="http" />
    </Endpoints>
    <Certificates>
      <Certificate name="Certificate1" storeLocation="LocalMachine" storeName="My" />
    </Certificates>
    <Imports>
      <Import moduleName="Connect" />
      <Import moduleName="Diagnostics" />
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <LocalResources>
      <LocalStorage name="localStoreOne" sizeInMB="10" />
      <LocalStorage name="localStoreTwo" sizeInMB="10" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" />
    </Startup>
  </WebRole>

  <WorkerRole name="WorkerRole1">
    <ConfigurationSettings>
      <Setting name="DiagnosticsConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10000" />
      <InternalEndpoint name="Endpoint2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

Można odwołać się do [schematu definicji usługi](schema-csdef-file.md), aby lepiej zrozumieć schemat XML użyty w tym miejscu, ale poniżej przedstawiono krótkie wyjaśnienie niektórych elementów:

**Witryny**  
Zawiera definicje witryn internetowych lub aplikacji sieci Web, które są hostowane w IIS7.

**InputEndpoints**  
Zawiera definicje punktów końcowych, które są używane do kontaktowania się z usługą w chmurze.

**InternalEndpoints**  
Zawiera definicje dla punktów końcowych, które są używane przez wystąpienia ról do komunikowania się ze sobą.

**ConfigurationSettings**  
Zawiera definicje ustawień dla funkcji określonej roli.

**Certyfikaty**  
Zawiera definicje certyfikatów, które są zbędne dla roli. Poprzedni przykład kodu przedstawia certyfikat używany do konfiguracji usługi Azure Connect.

**LocalResources**  
Zawiera definicje zasobów magazynu lokalnego. Lokalny zasób magazynu jest katalogiem zastrzeżonym w systemie plików maszyny wirtualnej, w którym jest uruchomiona wystąpienie roli.

**Importowania**  
Zawiera definicje importowanych modułów. Poprzedni przykład kodu przedstawia moduły Podłączanie pulpitu zdalnego i Azure Connect.

**Uruchamianie**  
Zawiera zadania, które są uruchamiane, gdy zostanie uruchomiona rola. Zadania są zdefiniowane w pliku cmd lub wykonywalnym.

<a name="cscfg"></a>

## <a name="serviceconfigurationcscfg"></a>ServiceConfiguration. cscfg
Konfiguracja ustawień dla usługi w chmurze zależy od wartości w pliku **ServiceConfiguration. cscfg** . Należy określić liczbę wystąpień, które mają zostać wdrożone dla każdej roli w tym pliku. Wartości ustawień konfiguracji zdefiniowanych w pliku definicji usługi są dodawane do pliku konfiguracji usługi. Odciski palców dla wszystkich certyfikatów zarządzania skojarzonych z usługą w chmurze są również dodawane do pliku. [Schemat konfiguracji usługi platformy Azure (plik cscfg)](schema-cscfg-file.md) zawiera dozwolony format dla pliku konfiguracji usługi.

Plik konfiguracji usługi nie jest spakowany z aplikacją, ale jest przekazywany do platformy Azure jako oddzielny plik i służy do konfigurowania usługi w chmurze. Można przekazać nowy plik konfiguracji usługi bez ponownego wdrażania usługi w chmurze. Wartości konfiguracji usługi w chmurze można zmienić, gdy usługa w chmurze jest uruchomiona. Poniższy przykład przedstawia ustawienia konfiguracji, które można zdefiniować dla ról Sieć Web i proces roboczy:

```xml
<?xml version="1.0"?>
<ServiceConfiguration serviceName="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration">
  <Role name="WebRole1">
    <Instances count="2" />
    <ConfigurationSettings>
      <Setting name="SettingName" value="SettingValue" />
    </ConfigurationSettings>

    <Certificates>
      <Certificate name="CertificateName" thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
      <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption"
         thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
    </Certificates>
  </Role>
</ServiceConfiguration>
```

Aby lepiej zrozumieć schemat XML użyty w tym miejscu, można zapoznać się ze [schematem konfiguracji usługi](schema-cscfg-file.md) , ale poniżej przedstawiono krótkie wyjaśnienie elementów:

**Wystąpienia**  
Konfiguruje liczbę uruchomionych wystąpień dla roli. Aby zapobiec ewentualnej niedostępności usługi w chmurze podczas uaktualniania, zalecamy wdrożenie więcej niż jednego wystąpienia ról dostępnych w sieci Web. Wdrażając więcej niż jedno wystąpienie, przestrzegasz wytycznych dotyczących usługi [Azure Compute umowa dotycząca poziomu usług (SLA)](https://azure.microsoft.com/support/legal/sla/), co gwarantuje 99,95% łączności zewnętrznej dla ról mających dostęp do Internetu po wdrożeniu co najmniej dwóch wystąpień roli dla usługi.

**ConfigurationSettings**  
Konfiguruje ustawienia dla uruchomionych wystąpień dla roli. Nazwa `<Setting>` elementów musi być zgodna z definicjami ustawień w pliku definicji usługi.

**Certyfikaty**  
Konfiguruje certyfikaty, które są używane przez usługę. W poprzednim przykładzie kodu pokazano, jak zdefiniować certyfikat dla modułu dostępu zdalnego. Wartość atrybutu *odcisku palca* musi być ustawiona na odcisk palca certyfikatu, który ma być używany.

<p/>

> [!NOTE]
> Odcisk palca certyfikatu można dodać do pliku konfiguracji przy użyciu edytora tekstu. Lub wartość można dodać na karcie **Certyfikaty** na stronie **Właściwości** roli w programie Visual Studio.
> 
> 

## <a name="defining-ports-for-role-instances"></a>Definiowanie portów dla wystąpień roli
System Azure umożliwia tylko jeden punkt wejścia do roli sieci Web. Oznacza to, że cały ruch odbywa się przy użyciu jednego adresu IP. Można skonfigurować witryny sieci Web do udostępniania portu przez skonfigurowanie nagłówka hosta, aby skierować żądanie do odpowiedniej lokalizacji. Możesz również skonfigurować aplikacje do nasłuchiwania dobrze znanych portów w adresie IP.

Poniższy przykład pokazuje konfigurację roli sieci Web z witryną internetową i aplikacją sieci Web. Witryna internetowa jest konfigurowana jako domyślna lokalizacja wpisu na porcie 80, a aplikacje sieci Web są skonfigurowane do odbierania żądań z alternatywnego nagłówka hosta o nazwie "mail.mysite.cloudapp.net".

```xml
<WebRole>
  <ConfigurationSettings>
    <Setting name="DiagnosticsConnectionString" />
  </ConfigurationSettings>
  <Endpoints>
    <InputEndpoint name="HttpIn" protocol="http" port="80" />
    <InputEndpoint name="Https" protocol="https" port="443" certificate="SSL"/>
    <InputEndpoint name="NetTcp" protocol="tcp" port="808" certificate="SSL"/>
  </Endpoints>
  <LocalResources>
    <LocalStorage name="Sites" cleanOnRoleRecycle="true" sizeInMB="100" />
  </LocalResources>
  <Site name="Mysite" packageDir="Sites\Mysite">
    <Bindings>
      <Binding name="http" endpointName="HttpIn" />
      <Binding name="https" endpointName="Https" />
      <Binding name="tcp" endpointName="NetTcp" />
    </Bindings>
  </Site>
  <Site name="MailSite" packageDir="MailSite">
    <Bindings>
      <Binding name="mail" endpointName="HttpIn" hostHeader="mail.mysite.cloudapp.net" />
    </Bindings>
    <VirtualDirectory name="artifacts" />
    <VirtualApplication name="storageproxy">
      <VirtualDirectory name="packages" packageDir="Sites\storageProxy\packages"/>
    </VirtualApplication>
  </Site>
</WebRole>
```


## <a name="changing-the-configuration-of-a-role"></a>Zmienianie konfiguracji roli
Konfigurację usługi w chmurze można zaktualizować, gdy jest ona uruchomiona na platformie Azure bez przełączania usługi do trybu offline. Aby zmienić informacje o konfiguracji, można przekazać nowy plik konfiguracji lub edytować plik konfiguracji w miejscu i zastosować go do uruchomionej usługi. W konfiguracji usługi można wprowadzać następujące zmiany:

* **Zmiana wartości ustawień konfiguracji**  
  Po zmianie ustawienia konfiguracji wystąpienie roli może zdecydować się na zastosowanie zmiany, gdy wystąpienie jest w trybie online lub aby bezpiecznie odtworzyć wystąpienie i zastosować zmiany, gdy wystąpienie jest w trybie offline.
* **Zmiana topologii usługi wystąpień roli**  
  Zmiany topologii nie wpływają na uruchomione wystąpienia, chyba że wystąpienie jest usuwane. Wszystkie pozostałe wystąpienia zwykle nie muszą być odtwarzane; można jednak wybrać odtwarzanie wystąpień ról w odpowiedzi na zmianę topologii.
* **Zmiana odcisku palca certyfikatu**  
  Certyfikat można aktualizować tylko wtedy, gdy wystąpienie roli jest w trybie offline. Jeśli certyfikat zostanie dodany, usunięty lub zmieniony, gdy wystąpienie roli jest w trybie online, platforma Azure bezpiecznie przełącza wystąpienie do trybu offline w celu zaktualizowania certyfikatu i przełączenia go do trybu online po zakończeniu zmiany.

### <a name="handling-configuration-changes-with-service-runtime-events"></a>Obsługa zmian konfiguracji przy użyciu zdarzeń środowiska uruchomieniowego usługi
Biblioteka środowiska uruchomieniowego platformy Azure obejmuje przestrzeń nazw Microsoft. WindowsAzure. serviceruntime, która zapewnia klasy umożliwiające współdziałanie ze środowiskiem platformy Azure z roli. Klasa RoleEnvironment definiuje następujące zdarzenia, które są wywoływane przed zmianą konfiguracji i po niej:

* **Zmiana zdarzenia**  
  Dzieje się tak przed zastosowaniem zmiany konfiguracji do określonego wystąpienia roli, dzięki czemu można w razie potrzeby wyłączyć wystąpienia roli.
* **Zmieniono zdarzenie**  
  Występuje po zastosowaniu zmiany konfiguracji do określonego wystąpienia roli.

> [!NOTE]
> Ponieważ zmiany certyfikatu zawsze przyjmują wystąpienia roli w trybie offline, nie zgłaszają zdarzeń RoleEnvironment. zmiana lub RoleEnvironment.
> 

<a name="cspkg"></a>

## <a name="servicepackagecspkg"></a>Servicepackage. cspkg
> [!NOTE]
> Maksymalny rozmiar pakietu, który można wdrożyć, to 600MB

Aby wdrożyć aplikację jako usługę w chmurze na platformie Azure, musisz najpierw spakować aplikację w odpowiednim formacie. Aby utworzyć plik pakietu jako alternatywę dla programu Visual Studio, można użyć narzędzia wiersza polecenia **CSPack** (zainstalowanego z [zestawem Azure SDK](https://azure.microsoft.com/downloads/)).

**CSPack** używa zawartości pliku definicji usługi i pliku konfiguracji usługi do definiowania zawartości pakietu. **CSPack** generuje plik pakietu aplikacji (. cspkg), który można przekazać do platformy Azure za pomocą [Azure Portal](../cloud-services/cloud-services-how-to-create-deploy-portal.md#create-and-deploy). Domyślnie pakiet nosi nazwę `[ServiceDefinitionFileName].cspkg` , ale można określić inną nazwę przy użyciu `/out` opcji **CSPack**.

**CSPack** znajduje się w lokalizacji  
`C:\Program Files\Microsoft SDKs\Azure\.NET SDK\[sdk-version]\bin\`

> [!NOTE]
> CSPack.exe (w systemie Windows) jest dostępny przez uruchomienie skrótu **wiersza polecenia Microsoft Azure** , który jest instalowany z zestawem SDK.  
> 
> Uruchom program CSPack.exe, aby wyświetlić dokumentację dotyczącą wszystkich możliwych przełączników i poleceń.
> 
> 

<p />

> [!TIP]
> Uruchom usługę w chmurze lokalnie w **emulatorze obliczeń Microsoft Azure**, użyj opcji **/CopyOnly** . Ta opcja powoduje skopiowanie plików binarnych aplikacji do układu katalogu, z którego mogą być uruchamiane w emulatorze obliczeniowym.
> 
> 

### <a name="example-command-to-package-a-cloud-service"></a>Przykładowe polecenie do spakowania usługi w chmurze
Poniższy przykład tworzy pakiet aplikacji, który zawiera informacje dla roli sieci Web. Polecenie Określa plik definicji usługi, który ma być używany, katalog, w którym można znaleźć pliki binarne, oraz nazwę pliku pakietu.

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /out:[OutputFileName]
```

Jeśli aplikacja zawiera zarówno rolę sieci Web, jak i rolę procesu roboczego, używane jest następujące polecenie:

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /out:[OutputFileName]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /role:[RoleName];[RoleBinariesDirectory];[RoleAssemblyName]
```

Gdzie zmienne są zdefiniowane w następujący sposób:

| Zmienna | Wartość |
| --- | --- |
| \[DirectoryName\] |Podkatalog w katalogu głównym projektu, który zawiera plik. csdef projektu platformy Azure. |
| \[ServiceDefinition\] |Nazwa pliku definicji usługi. Domyślnie ten plik nosi nazwę ServiceDefinition. csdef. |
| \[OutputFileName\] |Nazwa wygenerowanego pliku pakietu. Zazwyczaj jest to nazwa aplikacji. Jeśli nazwa pliku nie zostanie określona, pakiet aplikacji zostanie utworzony jako \[ ApplicationName \] . cspkg. |
| \[RoleName\] |Nazwa roli zdefiniowanej w pliku definicji usługi. |
| \[RoleBinariesDirectory] |Lokalizacja plików binarnych dla roli. |
| \[VirtualPath\] |Katalogi fizyczne dla każdej ścieżki wirtualnej zdefiniowanej w sekcji Lokacje definicji usługi. |
| \[PhysicalPath\] |Katalogi fizyczne zawartości dla każdej ścieżki wirtualnej zdefiniowanej w węźle lokacja definicji usługi. |
| \[RoleAssemblyName\] |Nazwa pliku binarnego dla roli. |

## <a name="next-steps"></a>Następne kroki 
- Zapoznaj się z [wymaganiami wstępnymi](deploy-prerequisite.md) dotyczącymi wdrażania Cloud Services (obsługa rozszerzona).
- Wdróż usługę w chmurze (obsługę rozszerzoną) przy użyciu [Azure Portal](deploy-portal.md), [programu PowerShell](deploy-powershell.md), [szablonu](deploy-template.md) lub [programu Visual Studio](deploy-visual-studio.md).
- Zapoznaj się z [często zadawanymi pytaniami](faq.md) dotyczącymi Cloud Services (obsługa rozszerzona).

