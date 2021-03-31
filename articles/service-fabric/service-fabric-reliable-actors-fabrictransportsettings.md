---
title: Zmień ustawienia FabricTransport
description: Dowiedz się więcej o konfigurowaniu ustawień komunikacji aktora w usłudze Azure Service Fabric dla różnych konfiguracji aktora.
author: suchiagicha
ms.topic: conceptual
ms.date: 04/20/2017
ms.author: pepogors
ms.custom: devx-track-csharp
ms.openlocfilehash: e3c20d86bd29d60eca328a44ab5d5d600bbf4da4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89010940"
---
# <a name="configure-fabrictransport-settings-for-reliable-actors"></a>Konfigurowanie ustawień FabricTransport dla Reliable Actors

Oto ustawienia, które można skonfigurować:
- C#: [FabricTransportRemotingSettings](/java/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportremotingsettings)
- Java: [FabricTransportRemotingSettings](/java/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportremotingsettings)

Domyślną konfigurację FabricTransport można zmodyfikować w następujący sposób.

## <a name="assembly-attribute"></a>Atrybut zestawu

Atrybut [FabricTransportActorRemotingProvider](/dotnet/api/microsoft.servicefabric.actors.remoting.fabrictransport.fabrictransportactorremotingproviderattribute) musi zostać zastosowany dla zespołów aktora i usługi aktora.

Poniższy przykład pokazuje, jak zmienić wartość domyślną ustawień FabricTransport OperationTimeout:

  ```csharp
    using Microsoft.ServiceFabric.Actors.Remoting.FabricTransport;
    [assembly:FabricTransportActorRemotingProvider(OperationTimeoutInSeconds = 600)]
   ```

   Drugi przykład zmienia wartości domyślne parametrów FabricTransport MaxMessageSize i OperationTimeoutInSeconds.

  ```csharp
    using Microsoft.ServiceFabric.Actors.Remoting.FabricTransport;
    [assembly:FabricTransportActorRemotingProvider(OperationTimeoutInSeconds = 600,MaxMessageSize = 134217728)]
   ```

## <a name="config-package"></a>Pakiet konfiguracyjny

Aby zmodyfikować konfigurację domyślną, można użyć [pakietu konfiguracyjnego](service-fabric-application-and-service-manifests.md) .

> [!IMPORTANT]
> W węzłach systemu Linux certyfikaty muszą mieć format PEM. Aby dowiedzieć się więcej na temat lokalizowania i konfigurowania certyfikatów dla systemu Linux, zobacz [Konfigurowanie certyfikatów w systemie Linux](./service-fabric-configure-certificates-linux.md). 
> 

### <a name="configure-fabrictransport-settings-for-the-actor-service"></a>Konfigurowanie ustawień FabricTransport dla usługi aktora

Dodaj sekcję TransportSettings w pliku settings.xml.

Domyślnie kod aktora wyszukuje Sekcjęname jako " &lt; aktorname &gt; TransportSettings". Jeśli ta wartość nie zostanie znaleziona, sprawdza obecność Sekcjiname jako "TransportSettings".

  ```xml
  <Section Name="MyActorServiceTransportSettings">
       <Parameter Name="MaxMessageSize" Value="10000000" />
       <Parameter Name="OperationTimeoutInSeconds" Value="300" />
       <Parameter Name="SecurityCredentialsType" Value="X509" />
       <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
       <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
       <Parameter Name="CertificateRemoteThumbprints" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662" />
       <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
       <Parameter Name="CertificateStoreName" Value="My" />
       <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
       <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
   </Section>
  ```

### <a name="configure-fabrictransport-settings-for-the-actor-client-assembly"></a>Konfigurowanie ustawień FabricTransport dla zestawu klienta aktora

Jeśli klient nie jest uruchomiony jako część usługi, można utworzyć &lt; plik "Nazwa exe klienta &gt;.settings.xml" w tej samej lokalizacji, w której znajduje się plik Client. exe. Następnie Dodaj sekcję TransportSettings w tym pliku. Sekcjaname powinna mieć wartość "TransportSettings".

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
    <Section Name="TransportSettings">
      <Parameter Name="SecurityCredentialsType" Value="X509" />
      <Parameter Name="OperationTimeoutInSeconds" Value="300" />
      <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
      <Parameter Name="CertificateFindValue" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662" />
      <Parameter Name="CertificateRemoteThumbprints" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
      <Parameter Name="OperationTimeoutInSeconds" Value="300" />
      <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
      <Parameter Name="CertificateStoreName" Value="My" />
      <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="CertificateRemoteCommonNames" Value="WinFabric-Test-SAN1-Alice" />
    </Section>
  </Settings>
   ```

* Konfigurowanie ustawień FabricTransport dla bezpiecznego usługi aktora/klienta z certyfikatem pomocniczym.
  Dodatkowe informacje o certyfikacie można dodać, dodając parametr CertificateFindValuebySecondary.
  Poniżej znajduje się przykład TransportSettings odbiornika.

  ```xml
  <Section Name="TransportSettings">
  <Parameter Name="SecurityCredentialsType" Value="X509" />
  <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
  <Parameter Name="CertificateFindValue" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662" />
  <Parameter Name="CertificateFindValuebySecondary" Value="h9449b018d0f6839a2c5d62b5b6c6ac822b6f690" />
  <Parameter Name="CertificateRemoteThumbprints" Value="4FEF3950642138446CC364A396E1E881DB76B48C,a9449b018d0f6839a2c5d62b5b6c6ac822b6f667" />
  <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
  <Parameter Name="CertificateStoreName" Value="My" />
  <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
  </Section>
   ```
   Poniżej znajduje się przykład TransportSettings klienta.

  ```xml
  <Section Name="TransportSettings">
  <Parameter Name="SecurityCredentialsType" Value="X509" />
  <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
  <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
  <Parameter Name="CertificateFindValuebySecondary" Value="a9449b018d0f6839a2c5d62b5b6c6ac822b6f667" />
  <Parameter Name="CertificateRemoteThumbprints" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662,h9449b018d0f6839a2c5d62b5b6c6ac822b6f690" />
  <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
  <Parameter Name="CertificateStoreName" Value="My" />
  <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
  </Section>
   ```
  * Konfigurowanie ustawień FabricTransport do zabezpieczania usługi aktora/klienta przy użyciu nazwy podmiotu.
    Użytkownik musi podać wartość findtype jako FindBySubjectName, dodać wartości CertificateIssuerThumbprints i CertificateRemoteCommonNames.
    Poniżej znajduje się przykład TransportSettings odbiornika.

    ```xml
    <Section Name="TransportSettings">
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateFindType" Value="FindBySubjectName" />
    <Parameter Name="CertificateFindValue" Value="CN = WinFabric-Test-SAN1-Alice" />
    <Parameter Name="CertificateIssuerThumbprints" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662" />
    <Parameter Name="CertificateRemoteCommonNames" Value="WinFabric-Test-SAN1-Bob" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateStoreName" Value="My" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
    </Section>
    ```
    Poniżej znajduje się przykład TransportSettings klienta.

  ```xml
   <Section Name="TransportSettings">
  <Parameter Name="SecurityCredentialsType" Value="X509" />
  <Parameter Name="CertificateFindType" Value="FindBySubjectName" />
  <Parameter Name="CertificateFindValue" Value="CN = WinFabric-Test-SAN1-Bob" />
  <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
  <Parameter Name="CertificateStoreName" Value="My" />
  <Parameter Name="CertificateRemoteCommonNames" Value="WinFabric-Test-SAN1-Alice" />
  <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
  </Section>
   ```
