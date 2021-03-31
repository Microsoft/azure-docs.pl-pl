---
title: Service Fabric platformy Azure — korzystanie z Service Fabric odwołań do magazynu kluczy aplikacji
description: W tym artykule wyjaśniono, jak używać obsługi KeyVaultReference usługi Service-Fabric dla wpisów tajnych aplikacji.
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: a0e4ef0decae8cc9ab4dc5f8c69dfef854af81f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98898600"
---
# <a name="keyvaultreference-support-for-azure-deployed-service-fabric-applications"></a>Obsługa KeyVaultReference dla aplikacji Service Fabric wdrożonych na platformie Azure

Typowym wyzwaniem podczas kompilowania aplikacji w chmurze jest sposób bezpiecznego dystrybuowania kluczy tajnych do aplikacji. Na przykład możesz chcieć wdrożyć klucz bazy danych w aplikacji bez uwidaczniania klucza w trakcie potoku lub do operatora. Service Fabric obsługa KeyVaultReference ułatwia wdrażanie wpisów tajnych aplikacji po prostu przez odwołanie do adresu URL tajnego, który jest przechowywany w Key Vault. Service Fabric będzie obsługiwać pobieranie tego wpisu tajnego w imieniu zarządzanej tożsamości aplikacji i aktywowanie aplikacji przy użyciu klucza tajnego.

> [!NOTE]
> KeyVaultReference support for Service Fabric Applications to GA (wersja zapoznawcza), począwszy od Service Fabric wersji 7,2 CU5. Zaleca się przeprowadzenie uaktualnienia do tej wersji przed użyciem tej funkcji.

> [!NOTE]
> Obsługa KeyVaultReference dla aplikacji Service Fabric obsługuje tylko wpisy tajne w [wersji](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning) . Wpisy tajne bez wersji nie są obsługiwane. Key Vault musi znajdować się w tej samej subskrypcji co klaster usługi Service Fabric. 

## <a name="prerequisites"></a>Wymagania wstępne

- Tożsamość zarządzana dla aplikacji Service Fabric

    Service Fabric obsługa KeyVaultReference korzysta z tożsamości zarządzanej aplikacji, aby pobrać wpisy tajne w imieniu aplikacji, dzięki czemu aplikacja musi zostać wdrożona za pośrednictwem i przypisana tożsamość zarządzana. Postępuj zgodnie z tym [dokumentem](concepts-managed-identity.md) , aby włączyć zarządzaną tożsamość aplikacji.

- Magazyn centralnych wpisów tajnych (CSS).

    Magazyn centralnych wpisów tajnych (CSS) jest Service Fabric zaszyfrowanych lokalnych kluczy tajnych. Ta funkcja używa CSS do ochrony i utrwalania wpisów tajnych po ich pobraniu z Key Vault. Aby korzystać z tej funkcji, wymagane jest również włączenie tej opcjonalnej usługi systemowej. Postępuj zgodnie z tym [dokumentem](service-fabric-application-secret-store.md) , aby włączyć i skonfigurować CSS.

- Przyznawanie uprawnień dostępu do tożsamości zarządzanej aplikacji do magazynu kluczy

    Odwołuje się do tego [dokumentu](how-to-grant-access-other-resources.md) , aby dowiedzieć się, jak udzielić zarządzanej tożsamości dostępu do magazynu kluczy. Należy również pamiętać, że jeśli używasz tożsamości zarządzanej przypisanej przez system, tożsamość zarządzana jest tworzona dopiero po wdrożeniu aplikacji. Może to stworzyć sytuacje wyścigu, w których aplikacja próbuje uzyskać dostęp do wpisu tajnego, zanim tożsamość będzie mogła uzyskać dostęp do magazynu. Nazwa tożsamości przypisanej do systemu będzie mieć wartość `{cluster name}/{application name}/{service name}` .
    
## <a name="use-keyvaultreferences-in-your-application"></a>Korzystanie z KeyVaultReferences w aplikacji
KeyVaultReferences mogą być używane na wiele sposobów
- [Jako zmienna środowiskowa](#as-an-environment-variable)
- [Zainstalowane jako plik w kontenerze](#mounted-as-a-file-into-your-container)
- [Jako odwołanie do hasła repozytorium kontenera](#as-a-reference-to-a-container-repository-password)

### <a name="as-an-environment-variable"></a>Jako zmienna środowiskowa

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="MySecret" Type="KeyVaultReference" Value="<KeyVaultURL>"/>
</EnvironmentVariables>
```

```C#
string secret =  Environment.GetEnvironmentVariable("MySecret");
```

### <a name="mounted-as-a-file-into-your-container"></a>Zainstalowane jako plik w kontenerze

- Dodaj sekcję do settings.xml

    Zdefiniuj `MySecret` parametr z typem `KeyVaultReference` i wartością `<KeyVaultURL>`

    ```xml
    <Section Name="MySecrets">
        <Parameter Name="MySecret" Type="KeyVaultReference" Value="<KeyVaultURL>"/>
    </Section>
    ```

- Odwołuje się do nowej sekcji w ApplicationManifest.xml w `<ConfigPackagePolicies>`

    ```xml
    <ServiceManifestImport>
        <Policies>
        <IdentityBindingPolicy ServiceIdentityRef="MyServiceMI" ApplicationIdentityRef="MyApplicationMI" />
        <ConfigPackagePolicies CodePackageRef="Code">
            <!--Linux container example-->
            <ConfigPackage Name="Config" SectionName="MySecrets" EnvironmentVariableName="SecretPath" MountPoint="/var/secrets"/>
            <!--Windows container example-->
            <!-- <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="C:\secrets"/> -->
        </ConfigPackagePolicies>
        </Policies>
    </ServiceManifestImport>
    ```

- Korzystanie z wpisów tajnych z kodu usługi

    Każdy parametr w obszarze `<Section  Name=MySecrets>` będzie plikiem w folderze wskazanym przez zmiennych środowiskowych SecretPath. Poniższy fragment kodu w języku C# pokazuje, jak odczytać z aplikacji wpis tajny.

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "MySecret"))) 
    {
        string secret =  sr.ReadToEnd();
    }
    ```
    > [!NOTE] 
    > MountPoint steruje folderem, w którym zostaną zainstalowane pliki zawierające wartości tajne.

### <a name="as-a-reference-to-a-container-repository-password"></a>Jako odwołanie do hasła repozytorium kontenera

```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="MyACRUser" Type="KeyVaultReference" Password="<KeyVaultURL>"/>
      </ContainerHostPolicies>
```

## <a name="next-steps"></a>Następne kroki

* [Dokumentacja usługi Azure — Magazyn kluczy](../key-vault/index.yml)
* [Dowiedz się więcej na temat centralnego magazynu wpisów tajnych](service-fabric-application-secret-store.md)
* [Informacje o tożsamości zarządzanej dla aplikacji Service Fabric](concepts-managed-identity.md)
