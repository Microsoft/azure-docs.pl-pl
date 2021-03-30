---
title: Magazyn centralnych wpisów tajnych usługi Azure Service Fabric
description: W tym artykule opisano sposób korzystania z magazynu centralnych wpisów tajnych w usłudze Azure Service Fabric.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: e9fd435803ad5354b0eb2d4f5de50009a8cbbfe2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88869759"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Magazyn centralnych wpisów tajnych na platformie Azure Service Fabric 
W tym artykule opisano sposób korzystania z magazynu centralnych wpisów tajnych (CSS) w usłudze Azure Service Fabric do tworzenia wpisów tajnych w aplikacjach Service Fabric. CSS to lokalna pamięć podręczna magazynu lokalnego wpisu tajnego, która przechowuje dane poufne, takie jak hasło, tokeny i klucze, szyfrowane w pamięci.

  > [!NOTE] 
  > Podczas aktywowania CSS po raz pierwszy przed wersjami 7,1. CU3 Aktywacja może zakończyć się niepowodzeniem i pozostawić kod CSS w złej kondycji, jeśli: CSS jest aktywowany w uwierzytelnianym klastrze systemu Windows; CSS jest aktywowany w dowolnym klastrze `EncryptionCertificateThumbprint` , ale jest nieprawidłowo zadeklarowany lub odpowiedni certyfikat nie jest zainstalowany/list ACL-ED w węzłach. W przypadku klastra uwierzytelniania systemu Windows Weź pod 7,1. CU3 przed kontynuowaniem. W przypadku innych klastrów należy dokładnie sprawdzić te nieposiadane warianty lub zachodzą na 7,1. CU3.
  
## <a name="enable-central-secrets-store"></a>Włącz magazyn centralnych wpisów tajnych
Dodaj następujący skrypt do konfiguracji klastra w `fabricSettings` celu włączenia CSS. Zalecamy użycie certyfikatu innego niż certyfikat klastra dla CSS. Upewnij się, że certyfikat szyfrowania został zainstalowany we wszystkich węzłach i `NetworkService` ma uprawnienia do odczytu klucza prywatnego certyfikatu.
  ```json
    "fabricSettings": 
    [
        ...
    {
        "name":  "CentralSecretService",
        "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                },
                {
                    "name":  "MinReplicaSetSize",
                    "value":  "1"
                },
                {
                    "name":  "TargetReplicaSetSize",
                    "value":  "3"
                },
                 {
                    "name" : "EncryptionCertificateThumbprint",
                    "value": "<thumbprint>"
                 }
                ,
                ],
            },
            ]
     }
        ...
     ]
```
## <a name="declare-a-secret-resource"></a>Deklarowanie tajnego zasobu
Zasób tajny można utworzyć przy użyciu interfejsu API REST.
  > [!NOTE] 
  > Jeśli klaster używa uwierzytelniania systemu Windows, żądanie REST jest wysyłane za pośrednictwem niezabezpieczonego kanału HTTP. Zalecenie polega na użyciu klastra z certyfikatem x509 z bezpiecznymi punktami końcowymi.

Aby utworzyć `supersecret` zasób tajny przy użyciu interfejsu API REST, wprowadź żądanie Put `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview` . Do utworzenia tajnego zasobu potrzebny jest certyfikat klastra lub certyfikat klienta administratora.

```powershell
$json = '{"properties": {"kind": "inlinedValue", "contentType": "text/plain", "description": "supersecret"}}'
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint> -Body $json
```

## <a name="set-the-secret-value"></a>Ustaw wartość klucza tajnego

Użyj poniższego skryptu, aby użyć interfejsu API REST w celu ustawienia wartości klucza tajnego.
```powershell
$Params = '{"properties": {"value": "mysecretpassword"}}'
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
### <a name="examine-the-secret-value"></a>Badanie wartości klucza tajnego
```powershell
Invoke-WebRequest -CertificateThumbprint <ClusterCertThumbprint> -Method POST -Uri "https:<clusterfqdn>/Resources/Secrets/supersecret/values/ver1/list_value?api-version=6.4-preview"
```
## <a name="use-the-secret-in-your-application"></a>Używanie wpisu tajnego w aplikacji

Wykonaj następujące kroki, aby użyć wpisu tajnego w aplikacji Service Fabric.

1. Dodaj sekcję w pliku **settings.xml** z następującym fragmentem kodu. Zwróć uwagę, że wartość jest w formacie { `secretname:version` }.

   ```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
   ```

1. Zaimportuj sekcję w **ApplicationManifest.xml**.
   ```xml
     <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
       <ConfigOverrides />
       <Policies>
         <ConfigPackagePolicies CodePackageRef="Code">
           <ConfigPackage Name="Config" SectionName="testsecrets" EnvironmentVariableName="SecretPath" />
           </ConfigPackagePolicies>
       </Policies>
     </ServiceManifestImport>
   ```

   Zmienna środowiskowa `SecretPath` będzie wskazywała katalog, w którym są przechowywane wszystkie wpisy tajne. Każdy parametr wymieniony w `testsecrets` sekcji jest przechowywany w osobnym pliku. Aplikacja może teraz używać klucza tajnego w następujący sposób:
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. Zainstaluj wpisy tajne w kontenerze. Jedyną zmianą wymaganą do udostępnienia wpisów tajnych wewnątrz kontenera jest `specify` punkt instalacji w programie `<ConfigPackage>` .
Poniższy fragment kodu jest modyfikowany **ApplicationManifest.xml**.  

   ```xml
   <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
       <ConfigOverrides />
       <Policies>
         <ConfigPackagePolicies CodePackageRef="Code">
           <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="C:\secrets" EnvironmentVariableName="SecretPath" />
           <!-- Linux Container
            <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="/mnt/secrets" EnvironmentVariableName="SecretPath" />
           -->
         </ConfigPackagePolicies>
       </Policies>
     </ServiceManifestImport>
   ```
   Wpisy tajne są dostępne w punkcie instalacji wewnątrz kontenera.

1. Wpis tajny można powiązać ze zmienną środowiskową procesu, określając `Type='SecretsStoreRef` . Poniższy fragment kodu stanowi przykład powiązania `supersecret` wersji ze `ver1` zmienną środowiskową `MySuperSecret` w **ServiceManifest.xml**.

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [zabezpieczeniach aplikacji i usług](service-fabric-application-and-service-security.md).
