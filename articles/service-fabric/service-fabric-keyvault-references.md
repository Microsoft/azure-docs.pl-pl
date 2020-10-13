---
title: Service Fabric platformy Azure — korzystanie z Service Fabric odwołań do magazynu kluczy aplikacji
description: W tym artykule wyjaśniono, jak używać obsługi KeyVaultReference usługi Service-Fabric dla wpisów tajnych aplikacji.
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: c4de6ae17ae728e1dbadbd6d6e2d94c0e1471112
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91261145"
---
# <a name="keyvaultreference-support-for-service-fabric-applications-preview"></a>Obsługa KeyVaultReference dla aplikacji Service Fabric (wersja zapoznawcza)

Typowym wyzwaniem podczas kompilowania aplikacji w chmurze jest sposób bezpiecznego przechowywania wpisów tajnych wymaganych przez aplikację. Na przykład możesz chcieć zapisać poświadczenia repozytorium kontenera w magazynie kluczy i odwołać się do niego w manifeście aplikacji. Service Fabric KeyVaultReference korzysta z tożsamości zarządzanej Service Fabric i ułatwia odwoływanie się do wpisów tajnych magazynu kluczy. W pozostałej części tego artykułu szczegółowo przedstawiono sposób korzystania z Service Fabric KeyVaultReference i zawiera on typowe użycie.

> [!IMPORTANT]
> Korzystanie z tej funkcji w wersji zapoznawczej nie jest zalecane w środowiskach produkcyjnych.

> [!NOTE]
> Funkcja w wersji zapoznawczej magazynu [kluczy obsługuje](https://docs.microsoft.com/azure/key-vault/general/about-keys-secrets-certificates#objects-identifiers-and-versioning) tylko wpisy tajne. Wpisy tajne bez wersji nie są obsługiwane.

## <a name="prerequisites"></a>Wymagania wstępne

- Tożsamość zarządzana dla aplikacji (MIT)
    
    Service Fabric obsługa KeyVaultReference korzysta z tożsamości zarządzanej aplikacji, w związku z czym aplikacje planujące użycie KeyVaultReferences powinny używać tożsamości zarządzanej. Postępuj zgodnie z tym [dokumentem](concepts-managed-identity.md) , aby włączyć zarządzaną tożsamość aplikacji.

- Magazyn centralnych wpisów tajnych (CSS).

    Magazyn centralnych wpisów tajnych (CSS) jest Service Fabric zaszyfrowanych lokalnych kluczy tajnych. CSS to lokalna pamięć podręczna magazynu lokalnego wpisu tajnego, która przechowuje dane poufne, takie jak hasło, tokeny i klucze, szyfrowane w pamięci. KeyVaultReference, po pobraniu znajdują się w pamięci podręcznej w CSS.

    Dodaj poniżej do konfiguracji klastra, `fabricSettings` Aby włączyć wszystkie funkcje wymagane do obsługi KeyVaultReference.

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
                    "value":  "3"
                },
                {
                    "name":  "TargetReplicaSetSize",
                    "value":  "3"
                }
                ]
            },
            {
                "name":  "ManagedIdentityTokenService",
                "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                }
                ]
            }
            ]
    ```

    > [!NOTE] 
    > Zaleca się użycie oddzielnego certyfikatu szyfrowania dla CSS. Można go dodać w sekcji "CentralSecretService".
    

    ```json
        {
            "name": "EncryptionCertificateThumbprint",
            "value": "<EncryptionCertificateThumbprint for CSS>"
        }
    ```
Aby zmiany zaczęły obowiązywać, należy również zmienić zasady uaktualniania, aby określić wymuszenie ponownego uruchomienia Service Fabric środowiska uruchomieniowego na każdym węźle w miarę postępu uaktualniania przez klaster. To ponowne uruchomienie zapewnia, że nowo włączona usługa systemu zostanie uruchomiona i uruchomiona w każdym węźle. W poniższym fragmencie kodu forceRestart jest podstawowym ustawieniem. Użyj istniejących wartości dla pozostałej części ustawień.
```json
"upgradeDescription": {
    "forceRestart": true,
    "healthCheckRetryTimeout": "00:45:00",
    "healthCheckStableDuration": "00:05:00",
    "healthCheckWaitDuration": "00:05:00",
    "upgradeDomainTimeout": "02:00:00",
    "upgradeReplicaSetCheckTimeout": "1.00:00:00",
    "upgradeTimeout": "12:00:00"
}
```
- Przyznawanie uprawnień dostępu do tożsamości zarządzanej aplikacji do magazynu kluczy

    Odwołuje się do tego [dokumentu](how-to-grant-access-other-resources.md) , aby dowiedzieć się, jak udzielić zarządzanej tożsamości dostępu do magazynu kluczy. Należy również pamiętać, że jeśli używasz tożsamości zarządzanej przypisanej przez system, tożsamość zarządzana jest tworzona dopiero po wdrożeniu aplikacji.

## <a name="keyvault-secret-as-application-parameter"></a>Wpis tajny magazynu kluczy jako parametr aplikacji
Załóżmy, że aplikacja musi odczytać hasło bazy danych zaplecza przechowywane w magazynie kluczy, Service Fabric pomoc techniczną KeyVaultReference. Poniżej przykład odczytuje `DBPassword` wpis tajny z magazynu kluczy przy użyciu Service Fabric KeyVaultReference.

- Dodaj sekcję do settings.xml

    Zdefiniuj `DBPassword` parametr z typem `KeyVaultReference` i wartością `<KeyVaultURL>`

    ```xml
    <Section Name="dbsecrets">
        <Parameter Name="DBPassword" Type="KeyVaultReference" Value="https://vault200.vault.azure.net/secrets/dbpassword/8ec042bbe0ea4356b9b171588a8a1f32"/>
    </Section>
    ```
- Odwołuje się do nowej sekcji w ApplicationManifest.xml w `<ConfigPackagePolicies>`

    ```xml
    <ServiceManifestImport>
        <Policies>
        <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="ttkappuser" />
        <ConfigPackagePolicies CodePackageRef="Code">
            <!--Linux container example-->
            <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="/var/secrets"/>
            <!--Windows container example-->
            <!-- <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="C:\secrets"/> -->
        </ConfigPackagePolicies>
        </Policies>
    </ServiceManifestImport>
    ```

- Korzystanie z KeyVaultReference w aplikacji

    Service Fabric przy tworzeniu wystąpienia usługi rozwiąże parametr KeyVaultReference za pomocą tożsamości zarządzanej aplikacji. Każdy parametr w obszarze `<Section  Name=dbsecrets>` będzie plikiem w folderze wskazanym przez zmiennych środowiskowych SecretPath. Poniższy fragment kodu w języku C# pokazuje, jak odczytać dbpassword w aplikacji.

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "DBPassword"))) 
    {
        string dbPassword =  sr.ReadToEnd();
        // dbPassword to connect to DB
    }
    ```
    > [!NOTE] 
    > W scenariuszu kontenera można użyć MountPoint, aby określić, gdzie `secrets` zostanie zainstalowana.

## <a name="keyvault-secret-as-environment-variable"></a>Wpis tajny magazynu kluczy jako zmienna środowiskowa

Zmienne środowiskowe Service Fabric obsługują teraz typ KeyVaultReference, poniżej przedstawiono sposób powiązania zmiennej środowiskowej z wpisem tajnym przechowywanym w magazynie kluczy.

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="EventStorePassword" Type="KeyVaultReference" Value="https://ttkvault.vault.azure.net/secrets/clustercert/e225bd97e203430d809740b47736b9b8"/>
</EnvironmentVariables>
```

```C#
string eventStorePassword =  Environment.GetEnvironmentVariable("EventStorePassword");
```
## <a name="keyvault-secret-as-container-repository-password"></a>Wpis tajny magazynu kluczy jako hasło repozytorium kontenera
KeyVaultReference jest obsługiwanym typem kontenera RepositoryCredentials, poniżej pokazano, jak używać odwołania do magazynu kluczy jako hasła repozytorium kontenerów.
```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="user1" Type="KeyVaultReference" Password="https://ttkvault.vault.azure.net/secrets/containerpwd/e225bd97e203430d809740b47736b9b8"/>
      </ContainerHostPolicies>
```
## <a name="faq"></a>Najczęściej zadawane pytania
- Tożsamość zarządzana musi być włączona na potrzeby obsługi KeyVaultReference, jednak aktywacja aplikacji zakończy się niepowodzeniem, jeśli KeyVaultReference jest używany bez włączania tożsamości zarządzanej.

- Jeśli używasz tożsamości przypisanej do systemu, jest ona tworzona dopiero po wdrożeniu aplikacji i spowoduje to utworzenie zależności cyklicznej. Po wdrożeniu aplikacji można przyznać systemowi uprawnienia dostępu do tożsamości dla magazynu kluczy. Tożsamość przypisaną przez system można znaleźć według nazwy {Cluster}/{Application Name}/{ServiceName}

- Magazyn kluczy musi znajdować się w tej samej subskrypcji co klaster usługi Service Fabric. 

## <a name="next-steps"></a>Następne kroki

* [Dokumentacja usługi Azure — Magazyn kluczy](../key-vault/index.yml)
