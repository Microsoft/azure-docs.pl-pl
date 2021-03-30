---
title: Importowanie certyfikatów do kontenera
description: Zapoznaj się teraz z zaimportowaniem plików certyfikatów do usługi kontenera Service Fabric.
ms.topic: conceptual
ms.date: 2/23/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 219882a3f7f6db665f1ec311098ef53464773b71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92313697"
---
# <a name="import-a-certificate-file-into-a-container-running-on-service-fabric"></a>Zaimportuj plik certyfikatu do kontenera działającego na Service Fabric

> [!NOTE]
> W przypadku klastrów Service Fabric działających na platformie Azure zaleca się użycie [Service Fabric tożsamości zarządzanej aplikacji](./concepts-managed-identity.md) do udostępniania certyfikatów aplikacji z poziomu kontenera. Tożsamość zarządzana zapewnia izolację wpisów tajnych i certyfikatów na poziomie usługi i zezwala na to, aby obsługa certyfikatów aplikacji była częścią przepływu pracy aplikacji, a nie z przepływem pracy infrastruktury. Mechanizm CertificateRef będzie przestarzały w przyszłej wersji.

Usługi kontenera można zabezpieczyć, określając certyfikat. Service Fabric udostępnia mechanizm dla usług wewnątrz kontenera w celu uzyskania dostępu do certyfikatu zainstalowanego na węzłach w klastrze z systemem Windows lub Linux (wersja 5,7 lub nowsza). Certyfikat należy zainstalować w magazynie certyfikatów w obszarze LocalMachine na wszystkich węzłach klastra. Klucz prywatny odpowiadający certyfikatowi musi być dostępny, dostępny i włączony do eksportu systemu Windows. Informacje o certyfikacie znajdują się w manifeście aplikacji pod `ContainerHostPolicies` tagiem, jak pokazano w poniższym fragmencie kodu:

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

W przypadku klastrów systemu Windows, podczas uruchamiania aplikacji, środowisko uruchomieniowe eksportuje każdy przywoływany certyfikat i jego klucz prywatny do pliku PFX zabezpieczony przy użyciu losowo wygenerowanego hasła. Pliki PFX i Password odpowiednio są dostępne wewnątrz kontenera przy użyciu następujących zmiennych środowiskowych: 

* Certificates_ServicePackageName_CodePackageName_CertName_PFX
* Certificates_ServicePackageName_CodePackageName_CertName_Password

W przypadku klastrów systemu Linux certyfikaty (PEM) są kopiowane z magazynu określonego przez X509StoreName do kontenera. Odpowiednie zmienne środowiskowe w systemie Linux to:

* Certificates_ServicePackageName_CodePackageName_CertName_PEM
* Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey

Należy pamiętać, że oba `PEM` `PrivateKey` pliki i zawierają certyfikat oraz niezaszyfrowany klucz prywatny.

Alternatywnie, jeśli masz już certyfikaty w wymaganym formularzu i chcesz uzyskać do nich dostęp wewnątrz kontenera, możesz utworzyć pakiet danych wewnątrz pakietu aplikacji i określić następujący element wewnątrz manifestu aplikacji:

```xml
<ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
  <CertificateRef Name="MyCert1" DataPackageRef="[DataPackageName]" DataPackageVersion="[Version]" RelativePath="[Relative Path to certificate inside DataPackage]" Password="[password]" IsPasswordEncrypted="[true/false]"/>
 ```

Usługa kontenera lub proces jest odpowiedzialny za Importowanie plików certyfikatów do kontenera. Aby zaimportować certyfikat, można użyć `setupentrypoint.sh` skryptów lub wykonać niestandardowy kod w procesie kontenera. Oto przykładowy kod w języku C# do importowania pliku PFX:

```csharp
string certificateFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_PFX");
string passwordFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_Password");
X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
string password = File.ReadAllLines(passwordFilePath, Encoding.Default)[0];
password = password.Replace("\0", string.Empty);
X509Certificate2 cert = new X509Certificate2(certificateFilePath, password, X509KeyStorageFlags.MachineKeySet | X509KeyStorageFlags.PersistKeySet);
store.Open(OpenFlags.ReadWrite);
store.Add(cert);
store.Close();
```
Ten certyfikat PFX może służyć do uwierzytelniania aplikacji lub usługi lub bezpiecznej komunikacji z innymi usługami. Domyślnie pliki są ACLed tylko do systemu. Listę ACL można na inne konta, zgodnie z wymaganiami usługi.

Następnym krokiem jest zapoznanie się z następującymi artykułami:

* [Wdrażanie kontenera systemu Windows w celu Service Fabric w systemie Windows Server 2016](service-fabric-get-started-containers.md)
* [Wdrażanie kontenera platformy Docker w celu Service Fabric w systemie Linux](service-fabric-get-started-containers-linux.md)