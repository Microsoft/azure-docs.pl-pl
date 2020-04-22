---
title: Jak utworzyć zasady konfiguracji gościa dla systemu Windows
description: Dowiedz się, jak utworzyć zasady konfiguracji gościa zasad platformy Azure dla systemu Windows.
ms.date: 03/20/2020
ms.topic: how-to
ms.openlocfilehash: 7b06aa0a70bfa17d67da9c6af447138f8bb9e712
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757418"
---
# <a name="how-to-create-guest-configuration-policies-for-windows"></a>Jak utworzyć zasady konfiguracji gościa dla systemu Windows

Przed utworzeniem zasad niestandardowych warto zapoznać się z informacjami o omówieniu koncepcyjnym na stronie [Konfiguracja gościa zasad azure.](../concepts/guest-configuration.md)
 
Aby dowiedzieć się więcej o tworzeniu zasad konfiguracji gościa dla systemu Linux, zobacz stronę [Jak utworzyć zasady konfiguracji gościa dla systemu Linux](./guest-configuration-create-linux.md)

Podczas inspekcji systemu Windows konfiguracja gościa używa modułu zasobu [żądanej konfiguracji stanu](/powershell/scripting/dsc/overview/overview) (DSC) do pliku konfiguracyjnego i. Konfiguracja DSC definiuje warunek, w który powinien znajdować się komputer.
Jeśli ocena konfiguracji nie powiedzie się, audyt efektu **zasadIfIfNotExists** jest wyzwalany i maszyna jest uważana za **niezgodną**.

[Konfiguracja gościa zasad azure](../concepts/guest-configuration.md) może służyć tylko do inspekcji ustawień wewnątrz komputerów. Korygowanie ustawień wewnątrz maszyn nie jest jeszcze dostępne.

Poniższe akcje można utworzyć w celu sprawdzenia poprawności stanu komputera platformy Azure lub komputera platformy innych niż Azure.

> [!IMPORTANT]
> Zasady niestandardowe z konfiguracją gościa to funkcja podglądu.
>
> Rozszerzenie konfiguracji gościa jest wymagane do wykonywania inspekcji na maszynach wirtualnych platformy Azure.
> Aby wdrożyć rozszerzenie na dużą skalę, przypisz następujące definicje zasad:
>   - Wdrażanie wymagań wstępnych w celu włączenia zasad konfiguracji gościa na maszynach wirtualnych systemu Windows.
>   - Wdrażanie wymagań wstępnych w celu włączenia zasad konfiguracji gościa na maszynach wirtualnych z systemem Linux.

## <a name="install-the-powershell-module"></a>Instalowanie modułu programu PowerShell

Tworzenie artefaktu konfiguracji gościa, automatyczne testowanie artefaktu, tworzenie definicji zasad i publikowanie zasad jest całkowicie zautomatyzowane przy użyciu modułu konfiguracji gościa w programie PowerShell. Moduł można zainstalować na komputerze z systemem Windows, macOS lub Linux z programem PowerShell 6.2 lub nowszym działającym lokalnie, z [usługą Azure Cloud Shell](https://shell.azure.com)lub z [obrazem platformy Azure PowerShell Core Docker](https://hub.docker.com/r/azuresdk/azure-powershell-core).

> [!NOTE]
> Kompilacja konfiguracji nie jest jeszcze obsługiwana w systemie Linux.

### <a name="base-requirements"></a>Wymagania podstawowe

Systemy operacyjne, w których można zainstalować moduł:

- Linux
- macOS
- Windows

Moduł zasobów konfiguracji gościa wymaga następującego oprogramowania:

- Program PowerShell 6.2 lub nowszy. Jeśli jeszcze go nie zainstalowano, postępuj zgodnie z [tymi instrukcjami](/powershell/scripting/install/installing-powershell).
- Program Azure PowerShell 1.5.0 lub nowszy. Jeśli jeszcze go nie zainstalowano, postępuj zgodnie z [tymi instrukcjami](/powershell/azure/install-az-ps).
  - Wymagane są tylko moduły AZ 'Az.Accounts' i 'Az.Resources'.

### <a name="install-the-module"></a>Instalowanie modułu

Aby zainstalować moduł **GuestConfiguration** w programie PowerShell:

1. Z monitu programu PowerShell uruchom następujące polecenie:

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. Sprawdź, czy moduł został zaimportowany:

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="guest-configuration-artifacts-and-policy-for-windows"></a>Artefakty i zasady konfiguracji gościa dla systemu Windows

Konfiguracja gościa używa konfiguracji żądanego stanu programu PowerShell jako abstrakcji języka do pisania, co należy przeprowadzić inspekcję w systemie Windows. Agent ładuje autonomiczne wystąpienie programu PowerShell 6.2, więc nie ma konfliktu z użyciem programu PowerShell DSC w programie Windows PowerShell 5.1 i nie ma wymogu wstępnej instalacji programu PowerShell 6.2 lub nowszego.

Aby zapoznać się z omówieniem pojęć i terminologii DSC dsc, zobacz [Omówienie dsc programu PowerShell](/powershell/scripting/dsc/overview/overview).

### <a name="how-guest-configuration-modules-differ-from-windows-powershell-dsc-modules"></a>Czym różnią się moduły konfiguracji gościa od modułów DSC programu Windows PowerShell

Gdy konfiguracja gościa przeprowadza inspekcję komputera:

1. Agent uruchamia `Test-TargetResource` się najpierw, aby ustalić, czy konfiguracja jest w prawidłowym stanie.
1. Wartość logiczna zwracana przez funkcję określa, czy stan usługi Azure Resource Manager dla przypisania gościa powinien być zgodny/niezgodny.
1. Dostawca uruchamia `Get-TargetResource` się, aby zwrócić bieżący stan każdego ustawienia, więc dostępne są szczegółowe informacje zarówno o tym, dlaczego komputer nie jest zgodny, jak i w celu potwierdzenia, że bieżący stan jest zgodny.

### <a name="get-targetresource-requirements"></a>Wymagania dotyczące źródła danych typu Get-TargetResource

Funkcja `Get-TargetResource` ma specjalne wymagania dotyczące konfiguracji gościa, które nie były potrzebne dla konfiguracji żądanego stanu systemu Windows.

- Skrót, który jest zwracany musi zawierać właściwość o nazwie **Przyczyny**.
- Właściwość Reasons musi być tablicą.
- Każdy element w tablicy powinien być hashtable z kluczami o nazwie **Kod** i **Fraza**.

Właściwość Reasons jest używana przez usługę do standaryzacji sposobu przedstawiania informacji, gdy maszyna jest niezgodna. Każdy element w uzasadnieniu można potraktować jako "powód", że zasób nie jest zgodny. Właściwość jest tablicą, ponieważ zasób może być niezgodny z więcej niż jedną przyczyną.

Właściwości **Kod** i **Fraza** są oczekiwane przez usługę. Podczas tworzenia zasobu niestandardowego ustaw tekst (zazwyczaj stdout), który chcesz pokazać jako przyczynę, dla której zasób nie jest zgodny jako wartość **frazy**. **Kod** ma określone wymagania dotyczące formatowania, dzięki czemu raportowanie może wyraźnie wyświetlać informacje o zasobie używanym do inspekcji. To rozwiązanie sprawia, że konfiguracja gościa jest rozszerzalna. Każde polecenie może być uruchomione tak długo, jak dane wyjściowe mogą być zwracane jako wartość ciągu dla **Phrase** właściwości.

- **Kod** (ciąg): Nazwa zasobu, powtórzone, a następnie krótka nazwa bez spacji jako identyfikator z tego powodu. Te trzy wartości powinny być dwukropkiem bez spacji.
  - Przykładem może być`registry:registry:keynotpresent`
- **Fraza** (ciąg): tekst czytelny dla człowieka, aby wyjaśnić, dlaczego ustawienie nie jest zgodne.
  - Przykładem może być`The registry key $key is not present on the machine.`

```powershell
$reasons = @()
$reasons += @{
  Code = 'Name:Name:ReasonIdentifer'
  Phrase = 'Explain why the setting is not compliant'
}
return @{
    reasons = $reasons
}
```

Właściwość Reasons musi również zostać dodana do schematu MOF dla zasobu jako klasa osadzona.

```mof
[ClassVersion("1.0.0.0")] 
class Reason
{
    [Read] String Phrase;
    [Read] String Code;
};

[ClassVersion("1.0.0.0"), FriendlyName("ResourceName")]
class ResourceName : OMI_BaseResource
{
    [Key, Description("Example description")] String Example;
    [Read, EmbeddedInstance("Reason")] String Reasons[];
};
```

### <a name="configuration-requirements"></a>Wymagania konfiguracyjne

Nazwa konfiguracji niestandardowej musi być spójna wszędzie. Nazwa pliku zip dla pakietu zawartości, nazwa konfiguracji w pliku MOF i nazwa przypisania gościa w szablonie Menedżera zasobów muszą być takie same.

### <a name="scaffolding-a-guest-configuration-project"></a>Rusztowania projektu konfiguracji gościa

Deweloperzy, którzy chcieliby przyspieszyć proces rozpoczynania pracy i pracy z przykładowego kodu, mogą zainstalować projekt społeczności o nazwie **Guest Configuration Project**. Projekt instaluje szablon dla modułu [Plaster](https://github.com/powershell/plaster) PowerShell. To narzędzie może służyć do szkieletowania projektu, w tym konfiguracji roboczej i przykładowego zasobu oraz zestawu testów [Pester,](https://github.com/pester/pester) aby sprawdzić poprawność projektu. Szablon zawiera również narzędzia zadań dla programu Visual Studio Code do automatyzacji tworzenia i sprawdzania poprawności pakietu konfiguracji gościa. Aby uzyskać więcej informacji, zobacz [projekt konfiguracji gościa projektu](https://github.com/microsoft/guestconfigurationproject)GitHub .

Aby uzyskać więcej informacji na temat pracy z konfiguracjami w ogóle, zobacz [Zapis, kompilowanie i Stosowanie konfiguracji](/powershell/scripting/dsc/configurations/write-compile-apply-configuration).

### <a name="expected-contents-of-a-guest-configuration-artifact"></a>Oczekiwana zawartość artefaktu konfiguracji gościa

Ukończony pakiet jest używany przez konfigurację gościa do tworzenia definicji zasad platformy Azure. Pakiet składa się z:

- Skompilowana konfiguracja DSC jako MOF
- Folder Moduły
  - Moduł konfiguracji gościa
  - Moduł DscNativeResources
  - (Windows) Moduły zasobów DSC wymagane przez MOF

Polecenia cmdlet programu PowerShell pomagają w tworzeniu pakietu.
Nie jest wymagany folder ani folder wersji na poziomie głównym.
Format pakietu musi być plikiem zip.

### <a name="storing-guest-configuration-artifacts"></a>Przechowywanie artefaktów konfiguracji gościa

Pakiet .zip musi być przechowywany w lokalizacji, która jest dostępna dla zarządzanych maszyn wirtualnych.
Przykłady obejmują repozytoria GitHub, repozytorium platformy Azure lub magazynu platformy Azure. Jeśli wolisz nie upubliczniać pakietu, możesz dołączyć [token sygnatury dostępu Współdzielonego](../../../storage/common/storage-dotnet-shared-access-signature-part-1.md) w adresie URL.
Można również zaimplementować [punkt końcowy usługi](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) dla komputerów w sieci prywatnej, chociaż ta konfiguracja dotyczy tylko dostępu do pakietu i nie komunikuje się z usługą.

## <a name="step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows"></a>Krok po kroku tworzenie niestandardowych zasad inspekcji konfiguracji gościa dla systemu Windows

Utwórz konfigurację DSC do inspekcji ustawień. Poniższy przykład skryptu programu PowerShell tworzy konfigurację o nazwie **AuditBitLocker**, importuje `Service` moduł zasobów **PsDscResources** i używa zasobu do inspekcji uruchomionej usługi. Skrypt konfiguracyjny może być wykonywany z komputera z systemem Windows lub macOS.

```powershell
# Define the DSC configuration and import GuestConfiguration
Configuration AuditBitLocker
{
    Import-DscResource -ModuleName 'PSDscResources'

    Node AuditBitlocker {
      Service 'Ensure BitLocker service is present and running'
      {
          Name = 'BDESVC'
          Ensure = 'Present'
          State = 'Running'
      }
    }
}

# Compile the configuration to create the MOF files
AuditBitLocker ./Config
```

Zapisz ten plik `config.ps1` o nazwie w folderze projektu. Uruchom go w programie PowerShell, `./config.ps1` wykonując go w terminalu. Zostanie utworzony nowy plik mof.

Polecenie `Node AuditBitlocker` nie jest technicznie wymagane, ale tworzy plik `AuditBitlocker.mof` o nazwie, `localhost.mof`a nie domyślny, . Posiadanie nazwy pliku .mof zgodnie z konfiguracją ułatwia organizowanie wielu plików podczas pracy na dużą skalę.

Po skompilowaniu MOF pliki pomocnicze muszą być spakowane razem. Ukończony pakiet jest używany przez konfigurację gościa do tworzenia definicji zasad platformy Azure.

Polecenie `New-GuestConfigurationPackage` cmdlet tworzy pakiet. Moduły, które są potrzebne przez konfigurację, muszą być dostępne w programie `$Env:PSModulePath`. Parametry polecenia `New-GuestConfigurationPackage` cmdlet podczas tworzenia zawartości systemu Windows:

- **Nazwa**: Nazwa pakietu konfiguracji gościa.
- **Konfiguracja**: Skompilowana ścieżka dokumentu konfiguracyjnego DSC.
- **Ścieżka**: Ścieżka folderu wyjściowego. Ten parametr jest opcjonalny. Jeśli nie zostanie określony, pakiet jest tworzony w bieżącym katalogu.

Uruchom następujące polecenie, aby utworzyć pakiet przy użyciu konfiguracji podanej w poprzednim kroku:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditBitlocker' `
  -Configuration './Config/AuditBitlocker.mof'
```

Po utworzeniu pakietu konfiguracji, ale przed opublikowaniem go na platformie Azure, można przetestować pakiet ze stacji roboczej lub środowiska ciągłej integracji/ciągłego wdrażania. Polecenie cmdlet `Test-GuestConfigurationPackage` guestconfiguration zawiera tego samego agenta w środowisku programistycznym, który jest używany na komputerach platformy Azure. Korzystając z tego rozwiązania, można wykonać testowanie integracji lokalnie przed zwolnieniem do środowisk chmury rozliczane.

Ponieważ agent faktycznie ocenia środowisko lokalne, w większości przypadków należy uruchomić polecenie cmdlet testowe na tej samej platformie systemu operacyjnego, co planujesz przeprowadzić inspekcję. Test będzie używać tylko modułów, które są zawarte w pakiecie zawartości.

Parametry polecenia `Test-GuestConfigurationPackage` cmdlet:

- **Nazwa**: Nazwa zasad konfiguracji gościa.
- **Parametr**: Parametry zasad podane w formacie mieszalalnym.
- **Ścieżka:** Pełna ścieżka pakietu konfiguracji gościa.

Uruchom następujące polecenie, aby przetestować pakiet utworzony przez poprzedni krok:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditBitlocker.zip
```

Polecenie cmdlet obsługuje również dane wejściowe z potoku programu PowerShell. Przesuń `New-GuestConfigurationPackage` wyjście polecenia `Test-GuestConfigurationPackage` cmdlet do polecenia cmdlet.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditBitlocker -Configuration ./Config/AuditBitlocker.mof | Test-GuestConfigurationPackage
```

Następnym krokiem jest opublikowanie pliku do magazynu obiektów blob. Poniższy skrypt zawiera funkcję, której można użyć do zautomatyzowania tego zadania. Polecenia używane w `publish` funkcji wymagają `Az.Storage` modułu.

```azurepowershell-interactive
function publish {
    param(
    [Parameter(Mandatory=$true)]
    $resourceGroup,
    [Parameter(Mandatory=$true)]
    $storageAccountName,
    [Parameter(Mandatory=$true)]
    $storageContainerName,
    [Parameter(Mandatory=$true)]
    $filePath,
    [Parameter(Mandatory=$true)]
    $blobName
    )

    # Get Storage Context
    $Context = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
        -Name $storageAccountName | `
        ForEach-Object { $_.Context }

    # Upload file
    $Blob = Set-AzStorageBlobContent -Context $Context `
        -Container $storageContainerName `
        -File $filePath `
        -Blob $blobName `
        -Force

    # Get url with SAS token
    $StartTime = (Get-Date)
    $ExpiryTime = $StartTime.AddYears('3')  # THREE YEAR EXPIRATION
    $SAS = New-AzStorageBlobSASToken -Context $Context `
        -Container $storageContainerName `
        -Blob $blobName `
        -StartTime $StartTime `
        -ExpiryTime $ExpiryTime `
        -Permission rl `
        -FullUri

    # Output
    return $SAS
}

# replace the $storageAccountName value below, it must be globally unique
$resourceGroup        = 'policyfiles'
$storageAccountName   = 'youraccountname'
$storageContainerName = 'artifacts'

$uri = publish `
  -resourceGroup $resourceGroup `
  -storageAccountName $storageAccountName `
  -storageContainerName $storageContainerName `
  -filePath ./AuditBitlocker.zip `
  -blobName 'AuditBitlocker'
```

Po utworzeniu i przekazaniu niestandardowego pakietu zasad konfiguracji gościa utwórz definicję zasad konfiguracji gościa. Polecenie `New-GuestConfigurationPolicy` cmdlet przyjmuje niestandardowy pakiet zasad i tworzy definicję zasad.

Parametry polecenia `New-GuestConfigurationPolicy` cmdlet:

- **ContentUri**: Publiczne http(s) uri pakietu zawartości konfiguracji gościa.
- **DisplayName**: Nazwa wyświetlana zasad.
- **Opis**: Opis zasad.
- **Parametr**: Parametry zasad podane w formacie mieszalalnym.
- **Wersja**: Wersja zasad.
- **Ścieżka:** Ścieżka docelowa, w której tworzone są definicje zasad.
- **Platforma:** Platforma docelowa (Windows/Linux) dla zasad konfiguracji gościa i pakietu zawartości.

Poniższy przykład tworzy definicje zasad w określonej ścieżce z niestandardowego pakietu zasad:

```azurepowershell-interactive
New-GuestConfigurationPolicy `
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit BitLocker Service.' `
    -Description 'Audit if BitLocker is not enabled on Windows machine.' `
    -Path './policies' `
    -Platform 'Windows' `
    -Version 1.0.0 `
    -Verbose
```

Następujące pliki są `New-GuestConfigurationPolicy`tworzone przez:

- **audytIfNotExists.json**
- **deployIfNotExists.json**
- **Inicjatywa.json**

Wyjście polecenia cmdlet zwraca obiekt zawierający nazwę wyświetlaną inicjatywy i ścieżkę plików zasad.

Na koniec opublikuj definicje zasad `Publish-GuestConfigurationPolicy` przy użyciu polecenia cmdlet. Polecenie cmdlet ma tylko parametr **Path,** który wskazuje lokalizację `New-GuestConfigurationPolicy`plików JSON utworzonych przez program .

Aby uruchomić polecenie Publikuj, potrzebujesz dostępu do tworzenia zasad na platformie Azure. Określone wymagania dotyczące autoryzacji są udokumentowane na stronie [Omówienie zasad platformy Azure.](../overview.md) Najlepszą wbudowaną rolą jest **współautor zasad zasobów**.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions'
```

Polecenie `Publish-GuestConfigurationPolicy` cmdlet akceptuje ścieżkę z potoku programu PowerShell. Ta funkcja oznacza, że można utworzyć pliki zasad i opublikować je w jednym zestawie poleceń potoku.

```azurepowershell-interactive
New-GuestConfigurationPolicy `
 -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit BitLocker service.' `
  -Description 'Audit if the BitLocker service is not enabled on Windows machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
```

W ramach zasad utworzonych na platformie Azure ostatnim krokiem jest przypisanie inicjatywy. Zobacz, jak przypisać inicjatywę za pomocą [portalu,](../assign-policy-portal.md) [interfejsu wiersza polecenia platformy Azure](../assign-policy-azurecli.md)i programu Azure [PowerShell.](../assign-policy-powershell.md)

> [!IMPORTANT]
> Zasady konfiguracji gościa muszą być **zawsze** przypisane przy użyciu inicjatywy, która łączy _auditIfNotExists_ i _DeployIfNotExists_ zasad. Jeśli przypisano tylko zasady _AuditIfNotExists,_ wymagania wstępne nie są wdrażane, a zasady zawsze pokazują, że serwery "0" są zgodne.

Przypisywanie definicji zasad za pomocą _efektu DeployIfNotExists_ wymaga dodatkowego poziomu dostępu. Aby przyznać najmniejsze uprawnienia, można utworzyć niestandardową definicję roli, która rozszerza **współautora zasad zasobów**. W poniższym przykładzie utworzy się rolę o nazwie **Dine współautora zasad zasobów** z dodatkowym uprawnieniem _Microsoft.Authorization/roleAssignments/write_.

```azurepowershell-interactive
$subscriptionid = '00000000-0000-0000-0000-000000000000'
$role = Get-AzRoleDefinition "Resource Policy Contributor"
$role.Id = $null
$role.Name = "Resource Policy Contributor DINE"
$role.Description = "Can assign Policies that require remediation."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Authorization/roleAssignments/write")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/$subscriptionid")
New-AzRoleDefinition -Role $role
```

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Korzystanie z parametrów w niestandardowych zasadach konfiguracji gościa

Konfiguracja gościa obsługuje nadrzędne właściwości konfiguracji w czasie wykonywania. Ta funkcja oznacza, że wartości w pliku MOF w pakiecie nie muszą być uważane za statyczne. Wartości zastępowania są dostarczane za pośrednictwem usługi Azure Policy i nie mają wpływu na sposób tworzenia lub kompilowania konfiguracji.

Polecenia cmdlet `New-GuestConfigurationPolicy` `Test-GuestConfigurationPolicyPackage` i zawierają parametr o nazwie **Parametry**. Ten parametr przyjmuje definicję mieszania zawierającą wszystkie szczegóły dotyczące każdego parametru i tworzy wymagane sekcje każdego pliku używanego dla definicji zasad platformy Azure.

Poniższy przykład tworzy definicję zasad do inspekcji usługi, gdzie użytkownik wybiera z listy w czasie przypisywania zasad.

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'ServiceName'                                            # Policy parameter name (mandatory)
        DisplayName = 'windows service name.'                           # Policy parameter display name (mandatory)
        Description = "Name of the windows service to be audited."      # Policy parameter description (optional)
        ResourceType = "Service"                                        # DSC configuration resource type (mandatory)
        ResourceId = 'windowsService'                                   # DSC configuration resource property name (mandatory)
        ResourcePropertyName = "Name"                                   # DSC configuration resource property name (mandatory)
        DefaultValue = 'winrm'                                          # Policy parameter default value (optional)
        AllowedValues = @('BDESVC','TermService','wuauserv','winrm')    # Policy parameter allowed values (optional)
    }
)

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Windows Service.' `
    -Description 'Audit if a Windows Service is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Parameters $PolicyParameterInfo `
    -Version 1.0.0
```

## <a name="policy-lifecycle"></a>Cykl życia zasad

Jeśli chcesz zwolnić aktualizację zasad, istnieją dwa pola, które wymagają uwagi.

- **Wersja:** Po uruchomieniu `New-GuestConfigurationPolicy` polecenia cmdlet należy określić numer wersji większy niż aktualnie opublikowany. Właściwość aktualizuje wersję przypisania konfiguracji gościa, dzięki czemu agent rozpoznaje zaktualizowany pakiet.
- **contentHash:** Ta właściwość jest `New-GuestConfigurationPolicy` aktualizowana automatycznie przez polecenie cmdlet. Jest to wartość skrótu pakietu utworzonego przez `New-GuestConfigurationPackage`. Właściwość musi być `.zip` poprawna dla pliku, który publikujesz. Jeśli tylko **contentUri** właściwość jest aktualizowana, rozszerzenie nie akceptuje pakietu zawartości.

Najprostszym sposobem wydania zaktualizowanego pakietu jest powtórzenie procesu opisanego w tym artykule i podanie zaktualizowanego numeru wersji. Ten proces gwarantuje, że wszystkie właściwości zostały poprawnie zaktualizowane.

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>Konwertowanie zawartości zasad grupy systemu Windows na konfigurację gościa zasad platformy Azure

Konfiguracja gościa podczas inspekcji maszyn z systemem Windows jest implementacją składni konfiguracji żądanego stanu programu PowerShell. Społeczność DSC opublikowała narzędzia do konwersji eksportowanych szablonów zasad grupy na format DSC. Za pomocą tego narzędzia wraz z poleceń cmdlet konfiguracji gościa opisane powyżej, można przekonwertować zawartość zasad grupy systemu Windows i pakiet/publikować go dla zasad platformy Azure do inspekcji. Aby uzyskać szczegółowe informacje na temat korzystania z narzędzia, zobacz artykuł [Szybki start: Konwertowanie zasad grupy na DSC](/powershell/scripting/dsc/quickstarts/gpo-quickstart).
Po przekonwertowaniu zawartości powyższe kroki, aby utworzyć pakiet i opublikować go jako zasady platformy Azure są takie same, jak dla każdej zawartości DSC.

## <a name="optional-signing-guest-configuration-packages"></a>Opcjonalnie: Podpisywanie pakietów konfiguracyjnych gościa

Niestandardowe zasady konfiguracji gościa używają skrótu SHA256 do sprawdzania poprawności pakietu zasad nie uległy zmianie.
Opcjonalnie klienci mogą również używać certyfikatu do podpisywania pakietów i wymuszania rozszerzenia konfiguracji gościa, aby zezwalać tylko na podpisaną zawartość.

Aby włączyć ten scenariusz, istnieją dwa kroki, które należy wykonać. Uruchom polecenie cmdlet, aby podpisać pakiet zawartości i dołączyć znacznik do maszyn, które powinny wymagać podpisania kodu.

Aby użyć funkcji sprawdzania poprawności `Protect-GuestConfigurationPackage` podpisu, uruchom polecenie cmdlet, aby podpisać pakiet przed jego opublikowaniem. To polecenie cmdlet wymaga certyfikatu "Podpisywanie kodu".

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

Parametry polecenia `Protect-GuestConfigurationPackage` cmdlet:

- **Ścieżka:** Pełna ścieżka pakietu konfiguracji gościa.
- **Certyfikat**: Certyfikat podpisywania kodu do podpisania pakietu. Ten parametr jest obsługiwany tylko podczas podpisywania zawartości dla systemu Windows.

Agent konfiguracji guestconfiguration oczekuje, że klucz publiczny certyfikatu będzie obecny w "Zaufanych urzędach certyfikacji głównej" na komputerach z systemem Windows i w ścieżce `/usr/local/share/ca-certificates/extra` na komputerach z systemem Linux. Aby węzeł weryfikował podpisaną zawartość, zainstaluj klucz publiczny certyfikatu na komputerze przed zastosowaniem zasad niestandardowych. Ten proces można wykonać przy użyciu dowolnej techniki wewnątrz maszyny Wirtualnej lub przy użyciu zasad platformy Azure. Przykładowy szablon znajduje [się tutaj](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows).
Zasady dostępu usługi Key Vault muszą umożliwiać dostawcy zasobów obliczeniowych dostęp do certyfikatów podczas wdrożeń. Aby uzyskać szczegółowe kroki, zobacz [Konfigurowanie magazynu kluczy dla maszyn wirtualnych w usłudze Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Poniżej przedstawiono przykład wyeksportowania klucza publicznego z certyfikatu podpisywania w celu zaimportowania go do komputera.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

Po opublikowaniu zawartości dołącz tag o `GuestConfigPolicyCertificateValidation` nazwie `enabled` i wartości do wszystkich maszyn wirtualnych, na których powinno być wymagane podpisywanie kodu. Zobacz [przykłady tagów,](../samples/built-in-policies.md#tags) jak tagi mogą być dostarczane na dużą skalę przy użyciu usługi Azure Policy. Po wprowadzeniu tego tagu definicja zasad `New-GuestConfigurationPolicy` wygenerowana przy użyciu polecenia cmdlet umożliwia wymaganie za pośrednictwem rozszerzenia konfiguracji gościa.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>Rozwiązywanie problemów z przypisaniami zasad konfiguracji gościa (wersja zapoznawcza)

Narzędzie jest dostępne w wersji zapoznawczej, aby pomóc w rozwiązywaniu problemów z przypisaniami konfiguracji gościa usługi Azure Policy. Narzędzie znajduje się w wersji zapoznawczej i zostało opublikowane w Galerii programu PowerShell jako narzędzie do rozwiązywania problemów [z konfiguracją gościa](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/)o nazwie modułu.

Aby uzyskać więcej informacji na temat poleceń cmdlet w tym narzędziu, użyj polecenia Get-Help w programie PowerShell, aby wyświetlić wbudowane wskazówki. Ponieważ narzędzie jest coraz częste aktualizacje, jest to najlepszy sposób, aby uzyskać najnowsze informacje.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o inspekcji maszyn wirtualnych za pomocą [konfiguracji gościa](../concepts/guest-configuration.md).
- Dowiedz się, jak [programowo tworzyć zasady](programmatically-create.md).
- Dowiedz się, jak [uzyskać dane dotyczące zgodności](get-compliance-data.md).
