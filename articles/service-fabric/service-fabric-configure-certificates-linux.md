---
title: Konfigurowanie certyfikatów dla aplikacji w systemie Linux
description: Konfigurowanie certyfikatów dla aplikacji za pomocą środowiska uruchomieniowego Service Fabric w klastrze z systemem Linux
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: pepogors
ms.openlocfilehash: 70f9cc38d84681f68c10882889214648a4dd2624
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98785570"
---
# <a name="certificates-and-security-on-linux-clusters"></a>Certyfikaty i zabezpieczenia w klastrach systemu Linux

Ten artykuł zawiera informacje dotyczące konfigurowania certyfikatów X. 509 w klastrach systemu Linux.

## <a name="location-and-format-of-x509-certificates-on-linux-nodes"></a>Lokalizacja i format certyfikatów X. 509 w węzłach systemu Linux

Service Fabric zazwyczaj oczekuje, że certyfikaty X. 509 mają być obecne w katalogu */var/lib/sfcerts* w węzłach klastra systemu Linux. Jest to prawdziwe w przypadku certyfikatów klastra, certyfikatów klientów itp. W niektórych przypadkach można określić lokalizację inną niż folder *var/lib/sfcerts* dla certyfikatów. Na przykład w przypadku Reliable Services utworzonych przy użyciu zestawu SDK języka Java Service Fabric można określić inną lokalizację za pośrednictwem pakietu konfiguracji (Settings.xml) dla niektórych certyfikatów specyficznych dla aplikacji. Aby dowiedzieć się więcej, zobacz [certyfikaty, do których odwołuje się pakiet konfiguracyjny (Settings.xml)](#certificates-referenced-in-the-configuration-package-settingsxml).

W przypadku klastrów systemu Linux Service Fabric oczekuje, że certyfikaty mają być obecne jako plik PEM zawierający zarówno certyfikat, jak i klucz prywatny, albo plik CRT zawierający certyfikat i plik klucza, który zawiera klucz prywatny. Wszystkie pliki powinny mieć format PEM. 

W przypadku instalowania certyfikatu z Azure Key Vault przy użyciu [szablonu Menedżer zasobów](./service-fabric-cluster-creation-create-template.md) lub poleceń [programu PowerShell](/powershell/module/az.servicefabric/) certyfikat zostanie zainstalowany w poprawnym formacie w katalogu */var/lib/sfcerts* w każdym węźle. Jeśli certyfikat jest instalowany za pomocą innej metody, należy się upewnić, że certyfikat jest prawidłowo zainstalowany w węzłach klastra.

## <a name="certificates-referenced-in-the-application-manifest"></a>Certyfikaty, do których odwołuje się manifest aplikacji

Certyfikaty określone w manifeście aplikacji, na przykład przez elementy [**SecretsCertificate**](./service-fabric-service-model-schema-elements.md#secretscertificate-element) lub [**EndpointCertificate**](./service-fabric-service-model-schema-elements.md#endpointcertificate-element) , muszą znajdować się w katalogu */var/lib/sfcerts* . Elementy, które są używane do określania certyfikatów w manifeście aplikacji, nie przyjmują atrybutu Path, więc certyfikaty muszą być obecne w katalogu domyślnym. Te elementy przyjmują opcjonalny atrybut **X509StoreName** . Wartość domyślna to "my", która wskazuje katalog */var/lib/sfcerts* w węzłach systemu Linux. Jakakolwiek inna wartość jest niezdefiniowana w klastrze systemu Linux. Firma Microsoft zaleca, aby pominąć atrybut **X509StoreName** dla aplikacji działających w klastrach systemu Linux. 

## <a name="certificates-referenced-in-the-configuration-package-settingsxml"></a>Certyfikaty, do których odwołuje się pakiet konfiguracyjny (Settings.xml)

W przypadku niektórych usług można skonfigurować certyfikaty X. 509 w [ConfigPackage](./service-fabric-application-and-service-manifests.md) (domyślnie Settings.xml). Na przykład dzieje się tak w przypadku deklarowania certyfikatów używanych do zabezpieczania kanałów RPC dla Reliable Services usług utworzonych przy użyciu zestawów SDK Service Fabric .NET Core lub Java. Istnieją dwa sposoby odwoływania się do certyfikatów w pakiecie konfiguracyjnym. Obsługa różni się między zestawami SDK platformy .NET Core i Java.

### <a name="using-x509-securitycredentialstype"></a>Korzystanie z certyfikatu x509 SecurityCredentialsType

Za pomocą zestawów SDK dla platformy .NET lub Java możesz określić opcję **x509** dla **SecurityCredentialsType**. Odnosi się to do `X509Credentials` typu ([.NET](/previous-versions/azure/reference/mt124925(v=azure.100)) / [Java](/java/api/system.fabric.x509credentials)) `SecurityCredentials` ([.NET](/previous-versions/azure/reference/mt124894(v=azure.100)) / [Java](/java/api/system.fabric.securitycredentials)).

Odwołanie **x509** lokalizuje certyfikat w magazynie certyfikatów. Poniższy kod XML przedstawia parametry używane do określania lokalizacji certyfikatu:

```xml
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateStoreName" Value="My" />
```

W przypadku usługi działającej w systemie Linux **LocalMachine** / **Moje** punkty w domyślną lokalizację dla certyfikatów, katalog */var/lib/sfcerts* . W przypadku systemu Linux wszystkie inne kombinacje **CertificateStoreLocation** i **CertificateStoreName** są niezdefiniowane. 

Zawsze należy określić **LocalMachine** dla parametru **CertificateStoreLocation** . Nie trzeba określać parametru **CertificateStoreName** , ponieważ jego wartość domyślna to "my". Za pomocą odwołania **x509** pliki certyfikatów muszą znajdować się w katalogu */var/lib/sfcerts* w węźle klastra.  

Poniższy kod XML przedstawia sekcję **TransportSettings** na podstawie tego stylu:

```xml
<Section Name="HelloWorldStatefulTransportSettings">
    <Parameter Name="MaxMessageSize" Value="10000000" />
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
    <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
    <Parameter Name="CertificateRemoteThumbprints" Value="9FEF3950642138446CC364A396E1E881DB76B483" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
    <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
</Section>
```

### <a name="using-x509_2-securitycredentialstype"></a>Używanie X509_2 SecurityCredentialsType

Za pomocą zestawu SDK języka Java można określić **X509_2** dla **SecurityCredentialsType**. Odnosi się to do `X509Credentials2` typu[(Java](/java/api/system.fabric.x509credentials2)) `SecurityCredentials` języka ([Java](/java/api/system.fabric.securitycredentials)). 

Za pomocą odwołania **X509_2** należy określić parametr path, aby można było zlokalizować certyfikat w katalogu innym niż */var/lib/sfcerts*.  Poniższy kod XML przedstawia parametry używane do określania lokalizacji certyfikatu: 

```xml
     <Parameter Name="SecurityCredentialsType" Value="X509_2" />
     <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
```

Poniższy kod XML przedstawia sekcję **TransportSettings** na podstawie tego stylu.

```xml
<!--Section name should always end with "TransportSettings".-->
<!--Here we are using a prefix "HelloWorldStateless".-->
<Section Name="HelloWorldStatelessTransportSettings">
    <Parameter Name="MaxMessageSize" Value="10000000" />
    <Parameter Name="SecurityCredentialsType" Value="X509_2" />
    <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptandSign" />
    <Parameter Name="CertificateRemoteThumbprints" Value="BD1C71E248B8C6834C151174DECDBDC02DE1D954" />
</Section>
```

> [!NOTE]
> Certyfikat jest określony jako plik CRT w powyższym kodzie XML. Oznacza to, że istnieje również plik. Key zawierający klucz prywatny w tej samej lokalizacji.

## <a name="configure-a-reliable-services-app-to-run-on-linux-clusters"></a>Konfigurowanie aplikacji Reliable Services do uruchamiania w klastrach systemu Linux

Zestawy SDK Service Fabric umożliwiają komunikowanie się z interfejsami API środowiska uruchomieniowego Service Fabric w celu wykorzystania platformy. Po uruchomieniu dowolnej aplikacji korzystającej z tej funkcji w zabezpieczonych klastrach systemu Linux należy skonfigurować aplikację przy użyciu certyfikatu, który może być używany do sprawdzania poprawności w środowisku uruchomieniowym Service Fabric. Aplikacje, które zawierają Service Fabric niezawodne usługi usług zapisaną przy użyciu zestawów SDK .NET Core lub Java, wymagają tej konfiguracji. 

Aby skonfigurować aplikację, Dodaj element [**SecretsCertificate**](./service-fabric-service-model-schema-elements.md#secretscertificate-element) w obszarze tag **Certificates (certyfikaty** ) znajdujący się w tagu **ApplicationManifest** w pliku *ApplicationManifest.xml* . Poniższy kod XML przedstawia certyfikat, do którego odwołuje się odcisk palca: 

```xml
   <Certificates>
       <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="0A00AA0AAAA0AAA00A000000A0AA00A0AAAA00" />
   </Certificates>   
```

Można odwołać się do certyfikatu klastra lub certyfikatu, który został zainstalowany w każdym węźle klastra. W systemie Linux pliki certyfikatów muszą znajdować się w katalogu */var/lib/sfcerts* . Aby dowiedzieć się więcej, zobacz temat [lokalizowanie i formatowanie certyfikatów X. 509 w węzłach systemu Linux](#location-and-format-of-x509-certificates-on-linux-nodes).
