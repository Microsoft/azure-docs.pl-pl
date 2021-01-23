---
title: Azure Cloud Services (klasyczny) def. Rola procesu roboczego schemat | Microsoft Docs
description: Rola procesu roboczego platformy Azure służy do ogólnego opracowywania i może wykonywać przetwarzanie w tle dla roli sieci Web. Dowiedz się więcej o schemacie roli proces roboczy platformy Azure.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 0871527187a3d678cb2b94bd8dc342cf2abde1ba
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743138"
---
# <a name="azure-cloud-services-classic-definition-workerrole-schema"></a>Schemat rola procesu roboczego definicji platformy Cloud Services Azure (klasyczny)

> [!IMPORTANT]
> [Azure Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md) to nowy model wdrażania oparty na Azure Resource Manager dla produktu Cloud Services platformy Azure.Ta zmiana spowoduje, że usługa Azure Cloud Services uruchomiona w ramach modelu wdrażania opartego na usłudze Azure Service Manager została zmieniona jako Cloud Services (klasyczny), a wszystkie nowe wdrożenia powinny używać [Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md).

Rola procesu roboczego platformy Azure to rola, która jest przydatna do uogólnionego programowania i może wykonywać przetwarzanie w tle dla roli sieci Web.

Domyślnym rozszerzeniem dla pliku definicji usługi jest. csdef.

## <a name="basic-service-definition-schema-for-a-worker-role"></a>Podstawowy schemat definicji usługi dla roli procesu roboczego.
Poniżej przedstawiono podstawowy format pliku definicji usługi zawierającego rolę procesu roboczego.

```xml
<ServiceDefinition …>
  <WorkerRole name="<worker-role-name>" vmsize="<worker-role-size>" enableNativeCodeExecution="[true|false]">
    <Certificates>
      <Certificate name="<certificate-name>" storeLocation="[CurrentUser|LocalMachine]" storeName="[My|Root|CA|Trust|Disallow|TrustedPeople|TrustedPublisher|AuthRoot|AddressBook|<custom-store>" />
    </Certificates>
    <ConfigurationSettings>
      <Setting name="<setting-name>" />
    </ConfigurationSettings>
    <Endpoints>
      <InputEndpoint name="<input-endpoint-name>" protocol="[http|https|tcp|udp]" localPort="<local-port-number>" port="<port-number>" certificate="<certificate-name>" loadBalancerProbe="<load-balancer-probe-name>" />
      <InternalEndpoint name="<internal-endpoint-name" protocol="[http|tcp|udp|any]" port="<port-number>">
         <FixedPort port="<port-number>"/>
         <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>
      </InternalEndpoint>
     <InstanceInputEndpoint name="<instance-input-endpoint-name>" localPort="<port-number>" protocol="[udp|tcp]">
         <AllocatePublicPortFrom>
            <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>
         </AllocatePublicPortFrom>
      </InstanceInputEndpoint>
    </Endpoints>
    <Imports>
      <Import moduleName="[RemoteAccess|RemoteForwarder|Diagnostics]"/>
    </Imports>
    <LocalResources>
      <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />
    </LocalResources>
    <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />
    <Runtime executionContext="[limited|elevated]">
      <Environment>
         <Variable name="<variable-name>" value="<variable-value>">
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>
          </Variable>
      </Environment>
      <EntryPoint>
         <NetFxEntryPoint assemblyName="<name-of-assembly-containing-entrypoint>" targetFrameworkVersion="<.net-framework-version>"/>
         <ProgramEntryPoint commandLine="<application>" setReadyOnProcessStart="[true|false]"/>
      </EntryPoint>
    </Runtime>
    <Startup priority="<for-internal-use-only>">
      <Task commandLine="" executionContext="[limited|elevated]" taskType="[simple|foreground|background]">
        <Environment>
         <Variable name="<variable-name>" value="<variable-value>">
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>
          </Variable>
        </Environment>
      </Task>
    </Startup>
    <Contents>
      <Content destination="<destination-folder-name>" >
        <SourceDirectory path="<local-source-directory>" />
      </Content>
    </Contents>
  </WorkerRole>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Elementy schematu
Plik definicji usługi zawiera te elementy, które opisano szczegółowo w kolejnych sekcjach w tym temacie:

[Rola procesu roboczego](#WorkerRole)

[ConfigurationSettings](#ConfigurationSettings)

[Ustawienie](#Setting)

[LocalResources](#LocalResources)

[LocalStorage](#LocalStorage)

[Punkty końcowe](#Endpoints)

[InputEndpoint](#InputEndpoint)

[InternalEndpoint](#InternalEndpoint)

[InstanceInputEndpoint](#InstanceInputEndpoint)

[AllocatePublicPortFrom](#AllocatePublicPortFrom)

[FixedPort](#FixedPort)

[FixedPortRange](#FixedPortRange)

[Certyfikaty](#Certificates)

[Certyfikat](#Certificate)

[Importowania](#Imports)

[Importuj](#Import)

[Środowisko uruchomieniowe](#Runtime)

[Środowisko](#Environment)

[EntryPoint](#EntryPoint)

[NetFxEntryPoint](#NetFxEntryPoint)

[ProgramEntryPoint](#ProgramEntryPoint)

[Zmienna](#Variable)

[RoleInstanceValue](#RoleInstanceValue)

[Uruchamianie](#Startup)

[Zadanie](#Task)

[Zawartość](#Contents)

[Zawartość](#Content)

[SourceDirectory](#SourceDirectory)

##  <a name="workerrole"></a><a name="WorkerRole"></a> Rola procesu roboczego
`WorkerRole`Element opisuje rolę, która jest przydatna do uogólnionego programowania i może wykonywać przetwarzanie w tle dla roli sieci Web. Usługa może zawierać co najmniej zero ról procesów roboczych.

W poniższej tabeli opisano atrybuty `WorkerRole` elementu.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|name|ciąg|Wymagane. Nazwa roli procesu roboczego. Nazwa roli musi być unikatowa.|
|enableNativeCodeExecution|boolean|Opcjonalny. Wartość domyślna to `true` : wykonywanie kodu natywnego i pełne zaufanie są domyślnie włączone. Ustaw ten atrybut na `false` , aby wyłączyć wykonywanie kodu natywnego dla roli proces roboczy i zamiast tego użyć częściowej relacji zaufania platformy Azure.|
|VmSize|ciąg|Opcjonalny. Ustaw tę wartość, aby zmienić rozmiar maszyny wirtualnej przydzielonej dla tej roli. Wartość domyślna to `Small`. Aby uzyskać listę możliwych rozmiarów maszyn wirtualnych i ich atrybutów, zobacz [rozmiary maszyn wirtualnych dla Cloud Services](cloud-services-sizes-specs.md).|

##  <a name="configurationsettings"></a><a name="ConfigurationSettings"></a> ConfigurationSettings
`ConfigurationSettings`Element opisuje zbiór ustawień konfiguracji dla roli proces roboczy. Ten element jest elementem nadrzędnym `Setting` elementu.

##  <a name="setting"></a><a name="Setting"></a> Konfigurowania
`Setting`Element opisuje parę nazwa i wartość, która określa ustawienie konfiguracji dla wystąpienia roli.

W poniższej tabeli opisano atrybuty `Setting` elementu.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|name|ciąg|Wymagane. Unikatowa nazwa ustawienia konfiguracji.|

Ustawienia konfiguracji roli to pary nazw i wartości, które są zadeklarowane w pliku definicji usługi i ustawiane w pliku konfiguracji usługi.

##  <a name="localresources"></a><a name="LocalResources"></a> LocalResources
`LocalResources`Element opisuje kolekcję zasobów magazynu lokalnego dla roli proces roboczy. Ten element jest elementem nadrzędnym `LocalStorage` elementu.

##  <a name="localstorage"></a><a name="LocalStorage"></a> LocalStorage
`LocalStorage`Element identyfikuje zasób magazynu lokalnego, który zapewnia miejsce w systemie plików dla usługi w czasie wykonywania. Rola może definiować zero lub więcej zasobów magazynu lokalnego.

> [!NOTE]
>  `LocalStorage`Element może być wyświetlany jako element podrzędny elementu, `WorkerRole` Aby zapewnić zgodność z wcześniejszymi wersjami zestawu Azure SDK.

W poniższej tabeli opisano atrybuty `LocalStorage` elementu.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|name|ciąg|Wymagane. Unikatowa nazwa magazynu lokalnego.|
|cleanOnRoleRecycle|boolean|Opcjonalny. Wskazuje, czy Magazyn lokalny ma być czyszczony, gdy rola zostanie ponownie uruchomiona. Wartość domyślna to `true`.|
|Wartość argumentu sizeinmb|int|Opcjonalny. Wymagana ilość miejsca do magazynowania do przydzielenia dla lokalnego magazynu, w MB. Jeśli nie zostanie określony, domyślnym miejscem do magazynowania jest 100 MB. Minimalna ilość dostępnego miejsca do magazynowania to 1 MB.<br /><br /> Maksymalny rozmiar zasobów lokalnych zależy od rozmiaru maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych dla Cloud Services](cloud-services-sizes-specs.md).|

Nazwa katalogu przypisana do zasobu magazynu lokalnego odpowiada wartości podanej dla atrybutu Name.

##  <a name="endpoints"></a><a name="Endpoints"></a> Punktów końcowych
`Endpoints`Element opisuje kolekcję wejściowych punktów końcowych (zewnętrznych), wewnętrznych i wejściowych wystąpień dla roli. Ten element jest elementem nadrzędnym `InputEndpoint` elementów, `InternalEndpoint` i `InstanceInputEndpoint` .

Wejściowe i wewnętrzne punkty końcowe są przydzielone osobno. Usługa może mieć łączną liczbę punktów końcowych wejściowych, wewnętrznych i wejściowych wystąpień, które można przydzielyć w ramach 25 ról dozwolonych w usłudze. Na przykład jeśli masz 5 ról, możesz przydzielić 5 wejściowych punktów końcowych na rolę lub przydzielić 25 wejściowych punktów końcowych do pojedynczej roli lub można przydzielić 1 wejściowy punkt końcowy każdy do 25 ról.

> [!NOTE]
>  Wszystkie wdrożone role wymagają jednego wystąpienia na rolę. Domyślne Inicjowanie obsługi dla subskrypcji jest ograniczone do 20 rdzeni i w rezultacie jest ograniczone do 20 wystąpień roli. Jeśli aplikacja wymaga więcej wystąpień niż jest to obsługiwane przez domyślne Inicjowanie obsługi [, zobacz Zarządzanie rozliczeniami, subskrypcjami i przydziałami,](https://azure.microsoft.com/support/options/) Aby uzyskać więcej informacji na temat zwiększania limitu przydziału.

##  <a name="inputendpoint"></a><a name="InputEndpoint"></a> InputEndpoint
`InputEndpoint`Element opisuje zewnętrzny punkt końcowy dla roli proces roboczy.

Istnieje możliwość zdefiniowania wielu punktów końcowych, które są kombinacją punktów końcowych HTTP, HTTPS, UDP i TCP. Możesz określić dowolny numer portu wybrany dla wejściowego punktu końcowego, ale numery portów określone dla każdej roli w usłudze muszą być unikatowe. Na przykład, jeśli określisz, że w przypadku protokołu HTTP i portu 443 dla roli jest stosowany port 80, możesz określić, że druga rola używa portu 8080 dla protokołu HTTP i portu 8043 dla protokołu HTTPS.

W poniższej tabeli opisano atrybuty `InputEndpoint` elementu.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|name|ciąg|Wymagane. Unikatowa nazwa zewnętrznego punktu końcowego.|
|protokol|ciąg|Wymagane. Protokół transportu dla zewnętrznego punktu końcowego. Dla roli proces roboczy możliwe wartości to `HTTP` ,, `HTTPS` `UDP` , lub `TCP` .|
|port|int|Wymagane. Port zewnętrznego punktu końcowego. Możesz określić dowolny wybrany numer portu, ale numery portów określone dla każdej roli w usłudze muszą być unikatowe.<br /><br /> Możliwe wartości mieszczą się w zakresie od 1 do 65535 włącznie (zestaw SDK platformy Azure w wersji 1,7 lub nowszej).|
|certyfikat|ciąg|Wymagane dla punktu końcowego HTTPS. Nazwa certyfikatu zdefiniowanego przez `Certificate` element.|
|localPort|int|Opcjonalny. Określa port używany na potrzeby połączeń wewnętrznych w punkcie końcowym. `localPort`Atrybut mapuje port zewnętrzny w punkcie końcowym na port wewnętrzny w roli. Jest to przydatne w scenariuszach, w których rola musi komunikować się z wewnętrznym składnikiem na porcie, który różni się od tego, który jest udostępniany zewnętrznie.<br /><br /> Jeśli nie zostanie określony, wartość `localPort` jest taka sama jak `port` atrybut. Ustaw wartość `localPort` na "*", aby automatycznie przypisywać nieprzydzielony port, który jest wykrywalny przy użyciu interfejsu API środowiska uruchomieniowego.<br /><br /> Możliwe wartości mieszczą się w zakresie od 1 do 65535 włącznie (zestaw SDK platformy Azure w wersji 1,7 lub nowszej).<br /><br /> Ten `localPort` atrybut jest dostępny tylko przy użyciu zestawu Azure SDK w wersji 1,3 lub nowszej.|
|ignoreRoleInstanceStatus|boolean|Opcjonalny. Gdy wartość tego atrybutu jest ustawiona na `true` , stan usługi jest ignorowany, a punkt końcowy nie zostanie usunięty przez moduł równoważenia obciążenia. Ustawienie tej wartości pozwala na `true` debugowanie zajętych wystąpień usługi. Wartość domyślna to `false`. **Uwaga:** Punkt końcowy może nadal odbierać ruch nawet wtedy, gdy rola nie jest w stanie gotowości.|
|loadBalancerProbe|ciąg|Opcjonalny. Nazwa sondy modułu równoważenia obciążenia skojarzona z wejściowym punktem końcowym. Aby uzyskać więcej informacji, zobacz [schemat LoadBalancerProbe](schema-csdef-loadbalancerprobe.md).|

##  <a name="internalendpoint"></a><a name="InternalEndpoint"></a> InternalEndpoint
`InternalEndpoint`Element opisuje wewnętrzny punkt końcowy do roli procesu roboczego. Wewnętrzny punkt końcowy jest dostępny tylko dla innych wystąpień roli uruchomionych w ramach usługi. nie jest on dostępny dla klientów spoza usługi. Rola procesu roboczego może mieć maksymalnie pięć wewnętrznych punktów końcowych HTTP, UDP lub TCP.

W poniższej tabeli opisano atrybuty `InternalEndpoint` elementu.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|name|ciąg|Wymagane. Unikatowa nazwa wewnętrznego punktu końcowego.|
|protokol|ciąg|Wymagane. Protokół transportu dla wewnętrznego punktu końcowego. Możliwe wartości to `HTTP` , `TCP` , `UDP` , lub `ANY` .<br /><br /> Wartość `ANY` określa, że dowolny protokół, dowolny port jest dozwolony.|
|port|int|Opcjonalny. Port używany do wewnętrznych połączeń z równoważeniem obciążenia w punkcie końcowym. Punkt końcowy ze zrównoważonym obciążeniem używa dwóch portów. Port używany dla publicznego adresu IP oraz port używany przez prywatny adres IP. Zazwyczaj są to te same ustawienia, ale można wybrać używanie różnych portów.<br /><br /> Możliwe wartości mieszczą się w zakresie od 1 do 65535 włącznie (zestaw SDK platformy Azure w wersji 1,7 lub nowszej).<br /><br /> Ten `Port` atrybut jest dostępny tylko przy użyciu zestawu Azure SDK w wersji 1,3 lub nowszej.|

##  <a name="instanceinputendpoint"></a><a name="InstanceInputEndpoint"></a> InstanceInputEndpoint
`InstanceInputEndpoint`Element opisuje wejściowy punkt końcowy wystąpienia do roli procesu roboczego. Wejściowy punkt końcowy wystąpienia jest skojarzony z określonym wystąpieniem roli przy użyciu przekazywania portów w module równoważenia obciążenia. Każdy wejściowy punkt końcowy wystąpienia jest mapowany na określony port z zakresu możliwych portów. Ten element jest elementem nadrzędnym `AllocatePublicPortFrom` elementu.

`InstanceInputEndpoint`Element jest dostępny tylko przy użyciu zestawu Azure SDK w wersji 1,7 lub nowszej.

W poniższej tabeli opisano atrybuty `InstanceInputEndpoint` elementu.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|name|ciąg|Wymagane. Unikatowa nazwa punktu końcowego.|
|localPort|int|Wymagane. Określa port wewnętrzny, do którego wszystkie wystąpienia ról będą nasłuchiwać w celu odbierania ruchu przychodzącego przesyłanego z modułu równoważenia obciążenia. Możliwe wartości mieszczą się w zakresie od 1 do 65535 włącznie.|
|protokol|ciąg|Wymagane. Protokół transportu dla wewnętrznego punktu końcowego. Możliwe wartości to `udp` lub `tcp`. Używany `tcp` do ruchu opartego na protokole HTTP/HTTPS.|

##  <a name="allocatepublicportfrom"></a><a name="AllocatePublicPortFrom"></a> AllocatePublicPortFrom
`AllocatePublicPortFrom`Element opisuje publiczny zakres portów, który może być używany przez klientów zewnętrznych do uzyskiwania dostępu do każdego wejściowego punktu końcowego wystąpienia. Numer portu publicznego (VIP) jest przydzielany z tego zakresu i przypisywany do każdego punktu końcowego poszczególnych wystąpień ról podczas wdrażania i aktualizowania dzierżawy. Ten element jest elementem nadrzędnym `FixedPortRange` elementu.

`AllocatePublicPortFrom`Element jest dostępny tylko przy użyciu zestawu Azure SDK w wersji 1,7 lub nowszej.

##  <a name="fixedport"></a><a name="FixedPort"></a> FixedPort
`FixedPort`Element określa port wewnętrznego punktu końcowego, który umożliwia połączenia z równoważeniem obciążenia w punkcie końcowym.

`FixedPort`Element jest dostępny tylko przy użyciu zestawu Azure SDK w wersji 1,3 lub nowszej.

W poniższej tabeli opisano atrybuty `FixedPort` elementu.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|port|int|Wymagane. Port wewnętrznego punktu końcowego. Ma to taki sam efekt jak ustawienie wartości `FixedPortRange` minimalnej i maksymalnej na ten sam port.<br /><br /> Możliwe wartości mieszczą się w zakresie od 1 do 65535 włącznie (zestaw SDK platformy Azure w wersji 1,7 lub nowszej).|

##  <a name="fixedportrange"></a><a name="FixedPortRange"></a> FixedPortRange
`FixedPortRange`Element określa zakres portów przypisanych do wewnętrznego punktu końcowego lub wejściowy punkt końcowy wystąpienia oraz ustawia port używany na potrzeby połączeń z równoważeniem obciążenia w punkcie końcowym.

> [!NOTE]
>  `FixedPortRange`Element działa inaczej w zależności od elementu, w którym się znajduje. Gdy `FixedPortRange` element znajduje się w `InternalEndpoint` elemencie, otwiera wszystkie porty w module równoważenia obciążenia w zakresie atrybutów minimalnych i maksymalnych dla wszystkich maszyn wirtualnych, na których jest uruchomiona rola. Gdy `FixedPortRange` element znajduje się w `InstanceInputEndpoint` elemencie, otwiera tylko jeden port w zakresie atrybutów minimalnych i maksymalnych dla każdej maszyny wirtualnej, na której działa rola.

`FixedPortRange`Element jest dostępny tylko przy użyciu zestawu Azure SDK w wersji 1,3 lub nowszej.

W poniższej tabeli opisano atrybuty `FixedPortRange` elementu.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|min|int|Wymagane. Minimalny port w zakresie. Możliwe wartości mieszczą się w zakresie od 1 do 65535 włącznie (zestaw SDK platformy Azure w wersji 1,7 lub nowszej).|
|max|ciąg|Wymagane. Maksymalny port w zakresie. Możliwe wartości mieszczą się w zakresie od 1 do 65535 włącznie (zestaw SDK platformy Azure w wersji 1,7 lub nowszej).|

##  <a name="certificates"></a><a name="Certificates"></a> Przystawki
`Certificates`Element opisuje zbiór certyfikatów dla roli proces roboczy. Ten element jest elementem nadrzędnym `Certificate` elementu. Rola może mieć dowolną liczbę skojarzonych certyfikatów. Aby uzyskać więcej informacji na temat korzystania z elementu Certificates, zobacz [Modyfikowanie pliku definicji usługi za pomocą certyfikatu](cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files).

##  <a name="certificate"></a><a name="Certificate"></a> Certyfikatu
`Certificate`Element opisuje certyfikat skojarzony z rolą proces roboczy.

W poniższej tabeli opisano atrybuty `Certificate` elementu.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|name|ciąg|Wymagane. Nazwa tego certyfikatu, która jest używana do odwoływania się do niego, gdy jest on skojarzony z `InputEndpoint` elementem https.|
|storeLocation|ciąg|Wymagane. Lokalizacja magazynu certyfikatów, w którym ten certyfikat znajduje się na komputerze lokalnym. Możliwe wartości to `CurrentUser` i `LocalMachine` .|
|storeName|ciąg|Wymagane. Nazwa magazynu certyfikatów, w którym znajduje się ten certyfikat na komputerze lokalnym. Możliwe wartości to wbudowane nazwy magazynów,,,,,,,, `My` `Root` `CA` `Trust` `Disallowed` `TrustedPeople` `TrustedPublisher` `AuthRoot` `AddressBook` lub dowolnych nazw magazynów niestandardowych. Jeśli określono niestandardową nazwę magazynu, magazyn zostanie utworzony automatycznie.|
|permissionLevel|ciąg|Opcjonalny. Określa uprawnienia dostępu nadawane procesom roli. Jeśli chcesz, aby dostęp do klucza prywatnego był możliwy tylko z podniesionymi procesami, określ `elevated` uprawnienie. `limitedOrElevated` uprawnienie umożliwia wszystkim procesom roli dostęp do klucza prywatnego. Możliwe wartości to `limitedOrElevated` lub `elevated`. Wartość domyślna to `limitedOrElevated`.|

##  <a name="imports"></a><a name="Imports"></a> Importowania
`Imports`Element opisuje zbiór modułów importu dla roli proces roboczy, który dodaje składniki do systemu operacyjnego gościa. Ten element jest elementem nadrzędnym `Import` elementu. Ten element jest opcjonalny, a rola może mieć tylko jeden blok czasu wykonywania.

`Imports`Element jest dostępny tylko przy użyciu zestawu Azure SDK w wersji 1,3 lub nowszej.

##  <a name="import"></a><a name="Import"></a> Zaimportować
`Import`Element określa moduł, który ma zostać dodany do systemu operacyjnego gościa.

`Import`Element jest dostępny tylko przy użyciu zestawu Azure SDK w wersji 1,3 lub nowszej.

W poniższej tabeli opisano atrybuty `Import` elementu.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|moduleName|ciąg|Wymagane. Nazwa modułu do zaimportowania. Prawidłowe moduły importu:<br /><br /> -RemoteAccess<br />- RemoteForwarder<br />-Diagnostyka<br /><br /> Moduły RemoteAccess i RemoteForwarder umożliwiają skonfigurowanie wystąpienia roli dla połączeń pulpitu zdalnego. Aby uzyskać więcej informacji, zobacz [włączanie Podłączanie pulpitu zdalnego](cloud-services-role-enable-remote-desktop-new-portal.md).<br /><br /> Moduł diagnostyki umożliwia zbieranie danych diagnostycznych dla wystąpienia roli|

##  <a name="runtime"></a><a name="Runtime"></a> Środowiska uruchomieniowego
`Runtime`Element opisuje zbiór ustawień zmiennych środowiskowych dla roli procesu roboczego kontrolujących środowisko uruchomieniowe procesu hosta platformy Azure. Ten element jest elementem nadrzędnym `Environment` elementu. Ten element jest opcjonalny, a rola może mieć tylko jeden blok czasu wykonywania.

`Runtime`Element jest dostępny tylko przy użyciu zestawu Azure SDK w wersji 1,3 lub nowszej.

W poniższej tabeli opisano atrybuty `Runtime` elementu:

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|Kontekście wykonywania|ciąg|Opcjonalny. Określa kontekst, w którym uruchamiany jest proces roli. Domyślny kontekst to `limited` .<br /><br /> -   `limited` — Proces jest uruchamiany bez uprawnień administratora.<br />-   `elevated` — Proces jest uruchamiany z uprawnieniami administratora.|

##  <a name="environment"></a><a name="Environment"></a> Naturalne
`Environment`Element opisuje zbiór ustawień zmiennych środowiskowych dla roli proces roboczy. Ten element jest elementem nadrzędnym `Variable` elementu. Rola może mieć określoną liczbę zmiennych środowiskowych.

##  <a name="variable"></a><a name="Variable"></a> Zmiennej
`Variable`Element określa zmienną środowiskową, która ma zostać ustawiona w systemie operacyjnym gościa.

`Variable`Element jest dostępny tylko przy użyciu zestawu Azure SDK w wersji 1,3 lub nowszej.

W poniższej tabeli opisano atrybuty `Variable` elementu:

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|name|ciąg|Wymagane. Nazwa zmiennej środowiskowej, która ma zostać ustawiona.|
|wartość|ciąg|Opcjonalny. Wartość, która ma zostać ustawiona dla zmiennej środowiskowej. Musisz dołączyć atrybut value lub `RoleInstanceValue` element.|

##  <a name="roleinstancevalue"></a><a name="RoleInstanceValue"></a> RoleInstanceValue
`RoleInstanceValue`Element określa wyrażenie XPath, z którego ma zostać pobrana wartość zmiennej.

W poniższej tabeli opisano atrybuty `RoleInstanceValue` elementu.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|Lokalizacja|ciąg|Opcjonalny. Ścieżka lokalizacji ustawień wdrożenia dla tego wystąpienia. Aby uzyskać więcej informacji, zobacz [Zmienne konfiguracyjne z wyrażeniem XPath](cloud-services-role-config-xpath.md).<br /><br /> Musisz dołączyć atrybut value lub `RoleInstanceValue` element.|

##  <a name="entrypoint"></a><a name="EntryPoint"></a> Punkt
`EntryPoint`Element określa punkt wejścia dla roli. Ten element jest elementem nadrzędnym `NetFxEntryPoint` elementów. Te elementy umożliwiają określenie aplikacji innej niż domyślna WaWorkerHost.exe do działania jako punkt wejścia roli.

`EntryPoint`Element jest dostępny tylko przy użyciu zestawu Azure SDK w wersji 1,5 lub nowszej.

##  <a name="netfxentrypoint"></a><a name="NetFxEntryPoint"></a> NetFxEntryPoint
`NetFxEntryPoint`Element określa program do uruchomienia dla roli.

> [!NOTE]
>  `NetFxEntryPoint`Element jest dostępny tylko przy użyciu zestawu Azure SDK w wersji 1,5 lub nowszej.

W poniższej tabeli opisano atrybuty `NetFxEntryPoint` elementu.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|assemblyName|ciąg|Wymagane. Ścieżka i nazwa pliku zestawu zawierającego punkt wejścia. Ścieżka jest względna w stosunku do folderu **\\ %ROLEROOT%\Approot** (nie określaj **\\ %ROLEROOT%\Approot** w `commandLine` , założono). **% ROLEROOT%** to zmienna środowiskowa obsługiwana przez platformę Azure, która reprezentuje lokalizację folderu głównego dla Twojej roli. Folder **\\ %ROLEROOT%\Approot** reprezentuje folder aplikacji dla Twojej roli.|
|targetFrameworkVersion|ciąg|Wymagane. Wersja programu .NET Framework, na którym został skompilowany zestaw. Na przykład `targetFrameworkVersion="v4.0"`.|

##  <a name="programentrypoint"></a><a name="ProgramEntryPoint"></a> ProgramEntryPoint
`ProgramEntryPoint`Element określa program do uruchomienia dla roli. `ProgramEntryPoint`Element umożliwia określenie punktu wejścia programu, który nie jest oparty na zestawie .NET.

> [!NOTE]
>  `ProgramEntryPoint`Element jest dostępny tylko przy użyciu zestawu Azure SDK w wersji 1,5 lub nowszej.

W poniższej tabeli opisano atrybuty `ProgramEntryPoint` elementu.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|Wiersza polecenia|ciąg|Wymagane. Ścieżka, nazwa pliku i argumenty wiersza polecenia programu do wykonania. Ścieżka jest względna w stosunku do folderu **%ROLEROOT%\Approot** (nie określaj **%ROLEROOT%\Approot** w wierszu polecenia, jest założono). **% ROLEROOT%** to zmienna środowiskowa obsługiwana przez platformę Azure, która reprezentuje lokalizację folderu głównego dla Twojej roli. Folder **%ROLEROOT%\Approot** reprezentuje folder aplikacji dla Twojej roli.<br /><br /> Jeśli program zostanie zakończony, rola jest odtwarzana, dlatego na ogół ustawia program do dalszego uruchomienia, a nie program, który właśnie uruchamia i uruchamia skończone zadanie.|
|setReadyOnProcessStart|boolean|Wymagane. Określa, czy wystąpienie roli czeka na uruchomienie programu wiersza polecenia. Ta wartość musi być teraz ustawiona na `true` ten czas. Ustawienie wartości na `false` jest zarezerwowane do użytku w przyszłości.|

##  <a name="startup"></a><a name="Startup"></a> Folderze
`Startup`Element opisuje zbiór zadań, które są uruchamiane, gdy rola jest uruchomiona. Ten element może być elementem nadrzędnym `Variable` elementu. Aby uzyskać więcej informacji o korzystaniu z zadań uruchamiania roli, zobacz [jak skonfigurować zadania uruchamiania](cloud-services-startup-tasks.md). Ten element jest opcjonalny i rola może mieć tylko jeden blok startowy.

W poniższej tabeli opisano atrybut `Startup` elementu.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|priority|int|Tylko do użytku wewnętrznego.|

##  <a name="task"></a><a name="Task"></a> Zadaniem
`Task`Element Określa zadanie uruchamiania, które odbywa się podczas uruchamiania roli. Zadania uruchamiania mogą służyć do wykonywania zadań przygotowujących rolę do uruchamiania takich składników oprogramowania lub uruchamiania innych aplikacji. Zadania są wykonywane w kolejności, w jakiej występują w `Startup` bloku elementu.

`Task`Element jest dostępny tylko przy użyciu zestawu Azure SDK w wersji 1,3 lub nowszej.

W poniższej tabeli opisano atrybuty `Task` elementu.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|Wiersza polecenia|ciąg|Wymagane. Skrypt, taki jak plik CMD zawierający polecenia do uruchomienia. Polecenia uruchamiania i pliki wsadowe muszą być zapisane w formacie ANSI. Formaty plików ustawiające znacznik kolejności bajtów na początku pliku nie będą przetwarzać się prawidłowo.|
|Kontekście wykonywania|ciąg|Określa kontekst, w którym skrypt jest uruchamiany.<br /><br /> -   `limited` [Domyślnie] — Uruchom z takimi samymi uprawnieniami jak rola hostującym proces.<br />-   `elevated` — Uruchom z uprawnieniami administratora.|
|taskType|ciąg|Określa zachowanie wykonywania polecenia.<br /><br /> -   `simple` [Domyślnie] — system czeka na zakończenie zadania przed uruchomieniem innych zadań.<br />-   `background` — System nie czeka na zakończenie zadania.<br />-   `foreground` — Podobnie jak w tle, z wyjątkiem tego, że rola nie jest uruchamiana ponownie do momentu zakończenia wszystkich zadań pierwszego planu.|

##  <a name="contents"></a><a name="Contents"></a> Contents
`Contents`Element opisuje zbiór zawartości dla roli proces roboczy. Ten element jest elementem nadrzędnym `Content` elementu.

`Contents`Element jest dostępny tylko przy użyciu zestawu Azure SDK w wersji 1,5 lub nowszej.

##  <a name="content"></a><a name="Content"></a> Treści
`Content`Element definiuje lokalizację źródłową zawartości do skopiowania na maszynę wirtualną platformy Azure i ścieżkę docelową, do której jest kopiowany.

`Content`Element jest dostępny tylko przy użyciu zestawu Azure SDK w wersji 1,5 lub nowszej.

W poniższej tabeli opisano atrybuty `Content` elementu.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|destination|ciąg|Wymagane. Lokalizacja na maszynie wirtualnej platformy Azure, do której zostanie umieszczona zawartość. Ta lokalizacja jest określana względem folderu **%ROLEROOT%\Approot**.|

Ten element jest elementem nadrzędnym `SourceDirectory` elementu.

##  <a name="sourcedirectory"></a><a name="SourceDirectory"></a> SourceDirectory
`SourceDirectory`Element definiuje katalog lokalny, z którego kopiowana jest zawartość. Użyj tego elementu, aby określić lokalną zawartość do skopiowania na maszynę wirtualną platformy Azure.

`SourceDirectory`Element jest dostępny tylko przy użyciu zestawu Azure SDK w wersji 1,5 lub nowszej.

W poniższej tabeli opisano atrybuty `SourceDirectory` elementu.

| Atrybut | Typ | Opis |
| --------- | ---- | ----------- |
|path|ciąg|Wymagane. Ścieżka względna lub bezwzględna katalogu lokalnego, którego zawartość zostanie skopiowana na maszynę wirtualną platformy Azure. Rozszerzanie zmiennych środowiskowych w ścieżce katalogu jest obsługiwane.|

## <a name="see-also"></a>Zobacz też
[Schemat definicji usługi w chmurze (klasycznej)](schema-csdef-file.md)



