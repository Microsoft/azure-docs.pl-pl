---
title: Zarządzanie wpisami tajnymi aplikacji platformy Azure Service Fabric
description: Dowiedz się, jak zabezpieczyć wartości tajne w aplikacji Service Fabric (platform-niezależny od).
ms.topic: conceptual
ms.date: 01/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: a11869c3b606ed9e74ce4f598109139fa1bb4164
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89012827"
---
# <a name="manage-encrypted-secrets-in-service-fabric-applications"></a>Zarządzanie zaszyfrowanymi wpisami tajnymi w aplikacjach Service Fabric
Ten przewodnik przeprowadzi Cię przez kroki związane z zarządzaniem wpisami tajnymi w aplikacji Service Fabric. Wpisy tajne mogą być dowolnymi informacjami poufnymi, takimi jak parametry połączenia magazynu, hasła lub inne wartości, które nie powinny być obsługiwane w postaci zwykłego tekstu.

Korzystanie z zaszyfrowanych wpisów tajnych w aplikacji Service Fabric obejmuje trzy kroki:
* Skonfiguruj certyfikat szyfrowania i Szyfruj wpisy tajne.
* Określ zaszyfrowane wpisy tajne w aplikacji.
* Odszyfruj zaszyfrowane klucze tajne z kodu usługi.

## <a name="set-up-an-encryption-certificate-and-encrypt-secrets"></a>Konfigurowanie certyfikatu szyfrowania i szyfrowanie wpisów tajnych
Skonfigurowanie certyfikatu szyfrowania i użycie go do szyfrowania wpisów tajnych zależy od systemów Windows i Linux.
* [Skonfiguruj certyfikat szyfrowania i Szyfruj wpisy tajne w klastrach systemu Windows.][secret-management-windows-specific-link]
* [Skonfiguruj certyfikat szyfrowania i Szyfruj wpisy tajne w klastrach systemu Linux.][secret-management-linux-specific-link]

## <a name="specify-encrypted-secrets-in-an-application"></a>Określanie szyfrowanych wpisów tajnych w aplikacji
W poprzednim kroku opisano sposób szyfrowania wpisu tajnego za pomocą certyfikatu i tworzenia zakodowanego ciągu Base-64 do użycia w aplikacji. Ten zakodowany ciąg Base-64 może być określony jako zaszyfrowany [parametr][parameters-link] w Settings.xml usługi lub jako [zmienna środowiskowa][environment-variables-link] zaszyfrowana w ServiceManifest.xml usługi.

Określ zaszyfrowany [parametr][parameters-link] w pliku konfiguracji Settings.xml usługi z `IsEncrypted` atrybutem ustawionym na `true` :

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```
Określ zaszyfrowaną [zmienną środowiskową][environment-variables-link] w pliku ServiceManifest.xml usługi z `Type` atrybutem ustawionym na `Encrypted` :
```xml
<CodePackage Name="Code" Version="1.0.0">
  <EnvironmentVariables>
    <EnvironmentVariable Name="MyEnvVariable" Type="Encrypted" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </EnvironmentVariables>
</CodePackage>
```

Wpisy tajne powinny być również zawarte w aplikacji Service Fabric przez określenie certyfikatu w manifeście aplikacji. Dodaj element **SecretsCertificate** do **ApplicationManifest.xml** i Uwzględnij odcisk palca żądanego certyfikatu.

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```
> [!NOTE]
> Po aktywowaniu aplikacji, która określa SecretsCertificate, Service Fabric odnajdzie pasujący certyfikat i udzielą tożsamości, która jest uruchomiona przez aplikację w ramach pełnych uprawnień do klucza prywatnego certyfikatu. Service Fabric również będzie monitorować certyfikat pod kątem zmian i ponownie stosować odpowiednie uprawnienia. Aby wykryć zmiany dla certyfikatów zadeklarowanych według nazwy pospolitej, Service Fabric uruchamia zadanie okresowe, które znajduje wszystkie zgodne certyfikaty i porównuje je z zbuforowaną listą odcisków palców. Gdy zostanie wykryty nowy odcisk palca, oznacza to, że certyfikat przez ten podmiot został odnowiony. Zadanie jest uruchamiane raz na minutę w każdym węźle klastra.
>
> Mimo że SecretsCertificate zezwala na deklaracje oparte na podmiotach, należy pamiętać, że zaszyfrowane ustawienia są powiązane z parą kluczy, która została użyta do szyfrowania ustawienia na kliencie. Musisz się upewnić, że oryginalny certyfikat szyfrowania (lub jego odpowiednik) jest zgodny z deklaracją opartą na podmiotach i że jest zainstalowany, w tym odpowiadającym mu kluczem prywatnym, na każdym węźle klastra, który może obsługiwać aplikację. Wszystkie ważne od czasu certyfikaty zgodne z deklaracją opartą na podmiotach i tworzone na podstawie tej samej pary kluczy, co oryginalny certyfikat szyfrowania, są uznawane za równoważne.
>

### <a name="inject-application-secrets-into-application-instances"></a>Wsuń wpisy tajne aplikacji do wystąpień aplikacji
W idealnym przypadku wdrożenie w różnych środowiskach powinno być tak zautomatyzowane jak to możliwe. Można to osiągnąć przez przeprowadzenie tajnego szyfrowania w środowisku kompilacji i dostarczenie zaszyfrowanych kluczy tajnych jako parametrów podczas tworzenia wystąpień aplikacji.

#### <a name="use-overridable-parameters-in-settingsxml"></a>Użyj parametrów do zastąpienia w Settings.xml
Plik konfiguracji Settings.xml umożliwia zastąpienia parametrów, które mogą być dostarczane podczas tworzenia aplikacji. Użyj `MustOverride` atrybutu zamiast podawania wartości dla parametru:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

Aby przesłonić wartości w Settings.xml, zadeklaruj parametr przesłonięcia dla usługi w ApplicationManifest.xml:

```xml
<ApplicationManifest ... >
  <Parameters>
    <Parameter Name="MySecret" DefaultValue="" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides>
      <ConfigOverride Name="Config">
        <Settings>
          <Section Name="MySettings">
            <Parameter Name="MySecret" Value="[MySecret]" IsEncrypted="true" />
          </Section>
        </Settings>
      </ConfigOverride>
    </ConfigOverrides>
  </ServiceManifestImport>
 ```

Teraz można określić wartość jako *parametr aplikacji* podczas tworzenia wystąpienia aplikacji. Tworzenie wystąpienia aplikacji może być inicjowane za pomocą programu PowerShell lub zapisywane w języku C# w celu zapewnienia łatwej integracji w procesie kompilacji.

Przy użyciu programu PowerShell parametr jest dostarczany do `New-ServiceFabricApplication` polecenia jako [Tablica skrótów](/previous-versions/windows/it-pro/windows-powershell-1.0/ee692803(v=technet.10)):

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

Przy użyciu języka C# parametry aplikacji są określone w `ApplicationDescription` `NameValueCollection` :

```csharp
FabricClient fabricClient = new FabricClient();

NameValueCollection applicationParameters = new NameValueCollection();
applicationParameters["MySecret"] = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=";

ApplicationDescription applicationDescription = new ApplicationDescription(
    applicationName: new Uri("fabric:/MyApp"),
    applicationTypeName: "MyAppType",
    applicationTypeVersion: "1.0.0",
    applicationParameters: applicationParameters)
);

await fabricClient.ApplicationManager.CreateApplicationAsync(applicationDescription);
```

## <a name="decrypt-encrypted-secrets-from-service-code"></a>Odszyfruj zaszyfrowane klucze tajne z kodu usługi
Interfejsy API służące do uzyskiwania dostępu do [parametrów][parameters-link] i [zmiennych środowiskowych][environment-variables-link] umożliwiają łatwe odszyfrowywanie szyfrowanych wartości. Ponieważ zaszyfrowany ciąg zawiera informacje o certyfikacie używanym do szyfrowania, nie trzeba ręcznie określać certyfikatu. Certyfikat należy zainstalować tylko w węźle, w którym uruchomiono usługę.

```csharp
// Access decrypted parameters from Settings.xml
ConfigurationPackage configPackage = FabricRuntime.GetActivationContext().GetConfigurationPackageObject("Config");
bool MySecretIsEncrypted = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].IsEncrypted;
if (MySecretIsEncrypted)
{
    SecureString MySecretDecryptedValue = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].DecryptValue();
}

// Access decrypted environment variables from ServiceManifest.xml
// Note: you do not have to call any explicit API to decrypt the environment variable.
string MyEnvVariable = Environment.GetEnvironmentVariable("MyEnvVariable");
```

## <a name="next-steps"></a>Następne kroki
* [Magazyn danych tajnych](service-fabric-application-secret-store.md) Service Fabric 
* Dowiedz się więcej o [zabezpieczeniach aplikacji i usługi](service-fabric-application-and-service-security.md)

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[service fabric secrets store]: service-fabric-application-secret-store.md
