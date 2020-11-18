---
title: Jak tworzyć zasady konfiguracji gościa dla systemu Windows
description: Dowiedz się, jak utworzyć Azure Policy zasady konfiguracji gościa dla systemu Windows.
ms.date: 08/17/2020
ms.topic: how-to
ms.openlocfilehash: ea9b40006deefbac2c253082eda4ef2da12149a4
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94700684"
---
# <a name="how-to-create-guest-configuration-policies-for-windows"></a>Jak tworzyć zasady konfiguracji gościa dla systemu Windows

Przed utworzeniem niestandardowych definicji zasad warto przeczytać informacje na temat przeglądu koncepcyjnego na stronie [Azure Policy konfiguracji gościa](../concepts/guest-configuration.md).
 
Aby dowiedzieć się więcej o tworzeniu zasad konfiguracji gościa dla systemu Linux, zobacz stronę [jak utworzyć zasady konfiguracji gościa dla systemu Linux](./guest-configuration-create-linux.md) .

Podczas przeprowadzania inspekcji systemu Windows konfiguracja gościa używa modułu zasobów platformy [Desired State Configuration](/powershell/scripting/dsc/overview/overview) (DSC) do utworzenia pliku konfiguracji. Konfiguracja platformy DSC definiuje stan, w jakim powinna być maszyna. Jeśli Ocena konfiguracji nie powiedzie się, zostanie wyzwolony efekt zasad **auditIfNotExists** i maszyna zostanie uznana za **niezgodną**.

[Azure Policy konfiguracja gościa](../concepts/guest-configuration.md) może być używana tylko do inspekcji ustawień wewnątrz maszyn. Korygowanie ustawień wewnątrz maszyn nie jest jeszcze dostępne.

Wykonaj poniższe czynności, aby utworzyć własną konfigurację służącą do sprawdzania poprawności stanu maszyny platformy Azure lub spoza niej.

> [!IMPORTANT]
> Niestandardowe definicje zasad z konfiguracją gościa w Azure Government i środowiska Chin platformy Azure to funkcja w wersji zapoznawczej.
>
> Do przeprowadzania inspekcji na maszynach wirtualnych platformy Azure jest wymagane rozszerzenie konfiguracji gościa.
> Aby wdrożyć rozszerzenie na dużą skalę na wszystkich maszynach z systemem Windows, przypisz następujące definicje zasad: `Deploy prerequisites to enable Guest Configuration Policy on Windows VMs`
> 
> Nie używaj wpisów tajnych ani informacji poufnych w niestandardowych pakietach zawartości.

## <a name="install-the-powershell-module"></a>Zainstaluj moduł programu PowerShell

Moduł konfiguracji gościa automatyzuje proces tworzenia zawartości niestandardowej, w tym:

- Tworzenie artefaktu zawartości konfiguracji gościa (zip)
- Automatyczne testowanie artefaktu
- Tworzenie definicji zasad
- Publikowanie zasad

Moduł można zainstalować na komputerze z systemem Windows, macOS lub Linux przy użyciu programu PowerShell 6,2 lub nowszego uruchomionego lokalnie lub z [Azure Cloud Shell](https://shell.azure.com)lub z [obrazem platformy Docker Azure PowerShell Core](https://hub.docker.com/r/azuresdk/azure-powershell-core).

> [!NOTE]
> Kompilacja konfiguracji nie jest jeszcze obsługiwana w systemie Linux.

### <a name="base-requirements"></a>Wymagania podstawowe

Systemy operacyjne, w których można zainstalować moduł:

- Linux
- macOS
- Windows

Moduł zasobów konfiguracji gościa wymaga następującego oprogramowania:

- Program PowerShell 6,2 lub nowszy. Jeśli jeszcze go nie zainstalowano, postępuj zgodnie z [tymi instrukcjami](/powershell/scripting/install/installing-powershell).
- Azure PowerShell 1.5.0 lub wyższy. Jeśli jeszcze go nie zainstalowano, postępuj zgodnie z [tymi instrukcjami](/powershell/azure/install-az-ps).
  - Wymagane są tylko AZ modules "AZ. Accounts" i "AZ. resources".

### <a name="install-the-module"></a>Instalowanie modułu

Aby zainstalować moduł **GuestConfiguration** w programie PowerShell:

1. W wierszu polecenia programu PowerShell uruchom następujące polecenie:

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. Sprawdź, czy moduł został zaimportowany:

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="guest-configuration-artifacts-and-policy-for-windows"></a>Artefakty konfiguracji gościa i zasady dla systemu Windows

Konfiguracja gościa używa konfiguracji żądanego stanu programu PowerShell jako abstrakcji języka na potrzeby zapisywania elementów do inspekcji w systemie Windows. Agent ładuje autonomiczne wystąpienie programu PowerShell 6,2, dlatego nie istnieje konflikt z użyciem środowiska PowerShell DSC w programie Windows PowerShell 5,1 i nie jest wymagane wstępne zainstalowanie programu PowerShell 6,2 lub nowszego.

Omówienie pojęć i terminologii DSC można znaleźć w temacie [Omówienie DSC programu PowerShell](/powershell/scripting/dsc/overview/overview).

### <a name="how-guest-configuration-modules-differ-from-windows-powershell-dsc-modules"></a>Jak moduły konfiguracji gościa różnią się od modułów DSC środowiska Windows PowerShell

Gdy konfiguracja gościa przeprowadza inspekcję komputera, sekwencja zdarzeń jest inna niż w przypadku programu Windows PowerShell DSC.

1. Agent najpierw uruchamia `Test-TargetResource` się w celu ustalenia, czy konfiguracja jest w poprawnym stanie.
1. Wartość logiczna zwrócona przez funkcję określa, czy stan Azure Resource Manager dla przypisania gościa powinien być zgodny/niezgodny.
1. Dostawca uruchamia program `Get-TargetResource` w celu zwrócenia bieżącego stanu każdego ustawienia, dlatego szczegółowe informacje o tym, dlaczego komputer nie jest zgodny, i upewnić się, że bieżący stan jest zgodny.

Parametry w Azure Policy, które przekazują wartości do przypisań konfiguracji gościa, muszą być typu _String_ . Nie można przekazać tablic za pomocą parametrów, nawet jeśli zasób DSC obsługuje tablice.

### <a name="get-targetresource-requirements"></a>Wymagania Get-TargetResource

Funkcja `Get-TargetResource` ma specjalne wymagania dotyczące konfiguracji gościa, która nie jest wymagana w przypadku konfiguracji żądanego stanu systemu Windows.

- Zwracana tablica skrótów musi zawierać właściwość o nazwie **powody**.
- Właściwość przyczyn musi być tablicą.
- Każdy element w tablicy powinien być tablicą skrótów z kluczami o nazwie **Code** i **phrase**.

Właściwość powody jest używana przez usługę do standaryzacji sposobu prezentowania informacji, gdy maszyna nie jest zgodna. Każdy element może być uważany za "powód", że zasób nie jest zgodny. Właściwość jest tablicą, ponieważ zasób może być niezgodny z więcej niż jedną przyczyną.

**Kod** właściwości i **frazy** są oczekiwane przez usługę. Podczas tworzenia zasobu niestandardowego Ustaw tekst (zazwyczaj stdout), który ma być pokazywany jako powód, w którym zasób nie jest zgodny jako wartość **frazy**. **Kod** ma określone wymagania dotyczące formatowania, więc raporty mogą jasno wyświetlać informacje o zasobie służące do przeprowadzania inspekcji. To rozwiązanie sprawia, że konfiguracja gościa jest rozszerzalna. Każde polecenie można uruchomić, o ile dane wyjściowe mogą być zwracane jako wartość ciągu dla właściwości **phrase** .

- **Kod** (ciąg): nazwa zasobu, powtórzona i krótka nazwa bez spacji jako identyfikator przyczyny. Te trzy wartości powinny być rozdzielane średnikami bez spacji.
  - Przykładem może być `registry:registry:keynotpresent`
- **Phrase** (ciąg): czytelny dla człowieka tekst objaśniający, dlaczego ustawienie nie jest zgodne.
  - Przykładem może być `The registry key $key is not present on the machine.`

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

Należy dodać właściwość powody do schematu MOF dla zasobu jako osadzoną klasę.

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

### <a name="configuration-requirements"></a>Wymagania dotyczące konfiguracji

Nazwa konfiguracji niestandardowej musi być spójna wszędzie. Nazwa pliku. zip pakietu zawartości, nazwa konfiguracji w pliku MOF i nazwa przypisywania gościa w szablonie Azure Resource Manager (szablon ARM) musi być taka sama.

### <a name="scaffolding-a-guest-configuration-project"></a>Tworzenie szkieletu projektu konfiguracji gościa

Deweloperzy, którzy chcą skrócić proces uruchamiania i pracy z przykładowego kodu, mogą zainstalować projekt społecznościowy o nazwie " **Projekt konfiguracji gościa**". Projekt instaluje szablon dla modułu [gips](https://github.com/powershell/plaster) PowerShell. To narzędzie może służyć do tworzenia szkieletu projektu, w tym konfiguracji roboczej i przykładowego zasobu, oraz zestawu testów [szkodników](https://github.com/pester/pester) do sprawdzania poprawności projektu. Szablon zawiera również moduły uruchamiające zadania dla Visual Studio Code do automatyzacji kompilowania i weryfikowania pakietu konfiguracji gościa. Aby uzyskać więcej informacji, zobacz [Projekt konfiguracji gościa](https://github.com/microsoft/guestconfigurationproject)projektu GitHub.

Aby uzyskać więcej informacji o pracy z konfiguracjami ogólnymi, zobacz [Zapisywanie, kompilowanie i stosowanie konfiguracji](/powershell/scripting/dsc/configurations/write-compile-apply-configuration).

### <a name="expected-contents-of-a-guest-configuration-artifact"></a>Oczekiwana zawartość artefaktu konfiguracji gościa

Ukończony pakiet jest używany przez konfigurację gościa do tworzenia definicji Azure Policy. Pakiet składa się z:

- Skompilowana Konfiguracja DSC jako plik MOF
- Folder modułów
  - Moduł GuestConfiguration
  - Moduł DscNativeResources
  - Systemy Moduły zasobów DSC wymagane przez plik MOF

Polecenia cmdlet programu PowerShell ułatwiają tworzenie pakietu.
Nie jest wymagany folder poziomu głównego ani folder wersji.
Format pakietu musi być plikiem zip.

### <a name="storing-guest-configuration-artifacts"></a>Przechowywanie artefaktów konfiguracji gościa

Pakiet ZIP musi być przechowywany w lokalizacji dostępnej dla zarządzanych maszyn wirtualnych.
Przykłady obejmują repozytoria GitHub, repozytorium platformy Azure lub usługę Azure Storage. Jeśli wolisz nie udostępniać pakietu publicznie, możesz dołączyć [token sygnatury dostępu współdzielonego](../../../storage/common/storage-sas-overview.md) w adresie URL. Można również zaimplementować [punkt końcowy usługi](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) dla maszyn w sieci prywatnej, chociaż ta konfiguracja ma zastosowanie tylko do uzyskiwania dostępu do pakietu i nie komunikuje się z usługą.

## <a name="step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows"></a>Krok po kroku, tworzenie niestandardowych zasad inspekcji konfiguracji Gości dla systemu Windows

Utwórz konfigurację DSC w celu przeprowadzenia inspekcji ustawień. Poniższy przykład skryptu programu PowerShell tworzy konfigurację o nazwie **AuditBitLocker**, importuje moduł zasobów **PsDscResources** i używa tego `Service` zasobu do inspekcji dla działającej usługi. Skrypt konfiguracji można wykonać z komputera z systemem Windows lub macOS.

```powershell
# Add PSDscResources module to environment
Install-Module 'PSDscResources'

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

Zapisz ten plik o nazwie `config.ps1` w folderze projektu. Uruchom ją w programie PowerShell, wykonując `./config.ps1` w terminalu. Zostanie utworzony nowy plik MOF.

`Node AuditBitlocker`Polecenie nie jest technicznie wymagane, ale tworzy plik o nazwie `AuditBitlocker.mof` zamiast domyślnego, `localhost.mof` . Jeśli nazwa pliku MOF jest zgodna z konfiguracją, ułatwia organizowanie wielu plików podczas pracy w dużej skali.

Po skompilowaniu pliku MOF pliki pomocnicze muszą być spakowane razem. Ukończony pakiet jest używany przez konfigurację gościa do tworzenia definicji Azure Policy.

`New-GuestConfigurationPackage`Polecenie cmdlet tworzy pakiet. Moduły, które są wymagane przez konfigurację, muszą być dostępne w programie `$Env:PSModulePath` . Parametry `New-GuestConfigurationPackage` polecenia cmdlet podczas tworzenia zawartości systemu Windows:

- **Nazwa**: Nazwa pakietu konfiguracji gościa.
- **Konfiguracja**: pełna ścieżka do skompilowanego dokumentu konfiguracji DSC.
- **Ścieżka**: ścieżka folderu wyjściowego. Ten parametr jest opcjonalny. Jeśli nie zostanie określony, pakiet zostanie utworzony w bieżącym katalogu.

Uruchom następujące polecenie, aby utworzyć pakiet przy użyciu konfiguracji podanych w poprzednim kroku:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditBitlocker' `
  -Configuration './Config/AuditBitlocker.mof'
```

Po utworzeniu pakietu konfiguracyjnego, ale przed opublikowaniem go na platformie Azure, można przetestować pakiet z poziomu stacji roboczej lub środowiska ciągłej integracji i ciągłego wdrażania (CI/CD). Polecenie cmdlet GuestConfiguration `Test-GuestConfigurationPackage` zawiera tego samego agenta w środowisku deweloperskim, jak jest używane wewnątrz maszyn platformy Azure. Korzystając z tego rozwiązania, można przeprowadzić testowanie integracji lokalnie przed zwolnieniem do rozliczane środowiska chmury.

Ponieważ agent rzeczywiście ocenia środowisko lokalne, w większości przypadków należy uruchomić polecenie cmdlet Test-na tej samej platformie systemu operacyjnego, co planujesz przeprowadzić inspekcję. Test używa tylko modułów, które są zawarte w pakiecie zawartości.

Parametry `Test-GuestConfigurationPackage` polecenia cmdlet:

- **Nazwa**: Nazwa zasad konfiguracji gościa.
- **Parameter**: parametry zasad podane w formacie Hashtable.
- **Ścieżka**: pełna ścieżka pakietu konfiguracji gościa.

Uruchom następujące polecenie, aby przetestować pakiet utworzony przez poprzedni krok:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditBitlocker.zip
```

Polecenie cmdlet obsługuje również dane wejściowe z potoku programu PowerShell. Potoku dane wyjściowe `New-GuestConfigurationPackage` polecenia cmdlet do `Test-GuestConfigurationPackage` polecenia cmdlet.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditBitlocker -Configuration ./Config/AuditBitlocker.mof | Test-GuestConfigurationPackage
```

Następnym krokiem jest opublikowanie pliku na platformie Azure Blob Storage. Polecenie `Publish-GuestConfigurationPackage` wymaga `Az.Storage` modułu.

```azurepowershell-interactive
Publish-GuestConfigurationPackage -Path ./AuditBitlocker.zip -ResourceGroupName myResourceGroupName -StorageAccountName myStorageAccountName
```

Po utworzeniu i przekazaniu niestandardowego pakietu zasad konfiguracji gościa Utwórz definicję zasad konfiguracji gościa. `New-GuestConfigurationPolicy`Polecenie cmdlet przyjmuje niestandardowy pakiet zasad i tworzy definicję zasad.

Parametry `New-GuestConfigurationPolicy` polecenia cmdlet:

- **ContentUri**: publiczny identyfikator URI http (s) dla pakietu zawartości konfiguracji gościa.
- **DisplayName**: Nazwa wyświetlana zasad.
- **Opis**: Opis zasad.
- **Parameter**: parametry zasad podane w formacie Hashtable.
- **Wersja**: wersja zasad.
- **Ścieżka**: ścieżka docelowa, w której są tworzone definicje zasad.
- **Platforma**: platforma docelowa (Windows/Linux) dla zasad konfiguracji gościa i pakietu zawartości.
- **Tag** dodaje jeden lub więcej filtrów tagów do definicji zasad
- **Kategoria** ustawia pole metadanych kategorii w definicji zasad

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

Następujące pliki są tworzone przez `New-GuestConfigurationPolicy` :

- **auditIfNotExists.jsna**

Dane wyjściowe polecenia cmdlet zwracają obiekt zawierający nazwę wyświetlaną inicjatywy i ścieżkę plików zasad.

Na koniec Opublikuj definicje zasad przy użyciu `Publish-GuestConfigurationPolicy` polecenia cmdlet. Polecenie cmdlet ma tylko parametr **Path** wskazujący lokalizację plików JSON utworzonych przez `New-GuestConfigurationPolicy` .

Aby uruchomić polecenie publikowania, musisz mieć dostęp do tworzenia zasad na platformie Azure. Wymagania dotyczące autoryzacji są udokumentowane na stronie [przegląd Azure Policy](../overview.md) . Najlepsza wbudowana rola to **współautor zasad zasobów**.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions'
```

`Publish-GuestConfigurationPolicy`Polecenie cmdlet akceptuje ścieżkę z potoku programu PowerShell. Ta funkcja oznacza, że można tworzyć pliki zasad i publikować je w pojedynczym zestawie poleceń potokowych.

```azurepowershell-interactive
New-GuestConfigurationPolicy `
 -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit BitLocker service.' `
  -Description 'Audit if the BitLocker service is not enabled on Windows machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
```

W przypadku zasad utworzonych na platformie Azure ostatni krok to przypisanie definicji. Zobacz, jak przypisać definicję przy użyciu [portalu](../assign-policy-portal.md), [interfejsu wiersza polecenia platformy Azure](../assign-policy-azurecli.md)i [Azure PowerShell](../assign-policy-powershell.md).

### <a name="filtering-guest-configuration-policies-using-tags"></a>Filtrowanie zasad konfiguracji gościa za pomocą tagów

Definicje zasad utworzone za pomocą poleceń cmdlet w module konfiguracji gościa mogą opcjonalnie zawierać filtr dla tagów. Parametr **tag** programu `New-GuestConfigurationPolicy` obsługuje tablicę skrótów zawierających poszczególne Tagi. Tagi są dodawane do `If` sekcji definicji zasad i nie mogą być modyfikowane przez przypisanie zasady.

Przykładowy fragment definicji zasad, który filtruje pod kątem tagów, znajduje się poniżej.

```json
"if": {
  "allOf" : [
    {
      "allOf": [
        {
          "field": "tags.Owner",
          "equals": "BusinessUnit"
        },
        {
          "field": "tags.Role",
          "equals": "Web"
        }
      ]
    },
    {
      // Original Guest Configuration content
    }
  ]
}
```

### <a name="using-parameters-in-custom-guest-configuration-policy-definitions"></a>Używanie parametrów w niestandardowych definicjach zasad konfiguracji gościa

Konfiguracja gościa obsługuje Zastępowanie właściwości konfiguracji w czasie wykonywania. Ta funkcja oznacza, że wartości w pliku MOF w pakiecie nie muszą być uznawane za statyczne. Wartości przesłonięć są udostępniane za pomocą Azure Policy i nie mają wpływu na sposób tworzenia lub kompilowania konfiguracji.

Polecenia cmdlet `New-GuestConfigurationPolicy` i `Test-GuestConfigurationPolicyPackage` zawierają parametr o nazwie **Parameter**. Ten parametr przyjmuje definicję obiektu Hashtable obejmującą wszystkie szczegóły każdego z parametrów i tworzy wymagane sekcje każdego pliku używanego do Azure Policy definicji.

Poniższy przykład tworzy definicję zasad w celu przeprowadzenia inspekcji usługi, w której użytkownik wybiera z listy w momencie przypisywania zasad.

```azurepowershell-interactive
# This DSC Resource text:
Service 'UserSelectedNameExample'
      {
          Name = 'ParameterValue'
          Ensure = 'Present'
          State = 'Running'
      }

# Would require the following hashtable:
$PolicyParameterInfo = @(
    @{
        Name = 'ServiceName'                                            # Policy parameter name (mandatory)
        DisplayName = 'windows service name.'                           # Policy parameter display name (mandatory)
        Description = "Name of the windows service to be audited."      # Policy parameter description (optional)
        ResourceType = "Service"                                        # DSC configuration resource type (mandatory)
        ResourceId = 'UserSelectedNameExample'                                   # DSC configuration resource id (mandatory)
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
    -Parameter $PolicyParameterInfo `
    -Version 1.0.0
```

## <a name="extending-guest-configuration-with-third-party-tools"></a>Rozszerzanie konfiguracji gościa za pomocą narzędzi innych firm

Pakiety artefaktów dla konfiguracji gościa można rozszerzyć w celu uwzględnienia narzędzi innych firm.
Rozszerzanie konfiguracji gościa wymaga opracowania dwóch składników.

- Zasób konfiguracji żądanego stanu, który obsługuje wszystkie działania związane z zarządzaniem narzędziem innej firmy
  - Instalowanie
  - Invoke
  - Konwertuj dane wyjściowe
- Zawartość w poprawnym formacie dla narzędzia do natywnego użycia

Zasób DSC wymaga niestandardowego programowania, jeśli rozwiązanie społecznościowe jeszcze nie istnieje.
Rozwiązania społecznościowe mogą zostać odnalezione przez przeszukanie Galeria programu PowerShell znacznika [GuestConfiguration](https://www.powershellgallery.com/packages?q=Tags%3A%22GuestConfiguration%22).

> [!Note]
> Rozszerzalność konfiguracji gościa jest scenariuszem "Przenieś własną licencję". Upewnij się, że spełniono warunki i postanowienia dotyczące narzędzi innych firm przed użyciem.

Po zainstalowaniu zasobu DSC w środowisku deweloperskim, użyj parametru **FilesToInclude** w `New-GuestConfigurationPackage` celu dołączenia zawartości dla platformy innej firmy w artefaktie zawartości.

### <a name="step-by-step-creating-a-content-artifact-that-uses-third-party-tools"></a>Krok po kroku, tworzenie artefaktu zawartości korzystającego z narzędzi innych firm

Tylko `New-GuestConfigurationPackage` polecenie cmdlet wymaga zmiany instrukcji krok po kroku dotyczących artefaktów zawartości DSC. W tym przykładzie należy użyć `gcInSpec` modułu, aby zwiększyć konfigurację gościa w celu inspekcji maszyn z systemem Windows przy użyciu platformy nieobjętej specyfikacją, a nie wbudowanego modułu używanego w systemie Linux. Moduł społeczności jest obsługiwany jako [projekt Open Source w usłudze GitHub](https://github.com/microsoft/gcinspec).

Zainstaluj wymagane moduły w środowisku deweloperskim:

```azurepowershell-interactive
# Update PowerShellGet if needed to allow installing PreRelease versions of modules
Install-Module PowerShellGet -Force

# Install GuestConfiguration module prerelease version
Install-Module GuestConfiguration -allowprerelease

# Install commmunity supported gcInSpec module
Install-Module gcInSpec
```

Najpierw utwórz plik YaML używany przez specyfikację. Plik zawiera podstawowe informacje o środowisku. Poniżej przedstawiono przykład:

```YaML
name: wmi_service
title: Verify WMI service is running
maintainer: Microsoft Corporation
summary: Validates that the Windows Service 'winmgmt' is running
copyright: Microsoft Corporation
license: MIT
version: 1.0.0
supports:
  - os-family: windows
```

Zapisz ten plik o nazwie `wmi_service.yml` w folderze o nazwie `wmi_service` w katalogu projektu.

Następnie utwórz plik Ruby przy użyciu abstrakcji języka INSPEC użytego do inspekcji maszyny.

```Ruby
control 'wmi_service' do
  impact 1.0
  title 'Verify windows service: winmgmt'
  desc 'Validates that the service, is installed, enabled, and running'

  describe service('winmgmt') do
    it { should be_installed }
    it { should be_enabled }
    it { should be_running }
  end
end

```

Zapisz ten plik `wmi_service.rb` w nowym folderze o nazwie `controls` wewnątrz `wmi_service` katalogu.

Na koniec Utwórz konfigurację, zaimportuj moduł zasobów **GuestConfiguration** i użyj zasobu, `gcInSpec` Aby ustawić nazwę profilu INSPEC.

```powershell
# Define the configuration and import GuestConfiguration
Configuration wmi_service
{
    Import-DSCResource -Module @{ModuleName = 'gcInSpec'; ModuleVersion = '2.1.0'}
    node 'wmi_service'
    {
        gcInSpec wmi_service
        {
            InSpecProfileName       = 'wmi_service'
            InSpecVersion           = '3.9.3'
            WindowsServerVersion    = '2016'
        }
    }
}

# Compile the configuration to create the MOF files
wmi_service -out ./Config
```

Teraz należy mieć strukturę projektu w następujący sposób:

```file
/ wmi_service
    / Config
        wmi_service.mof
    / wmi_service
        wmi_service.yml
        / controls
            wmi_service.rb 
```

Pliki pomocnicze muszą być spakowane razem. Ukończony pakiet jest używany przez konfigurację gościa do tworzenia definicji Azure Policy.

`New-GuestConfigurationPackage`Polecenie cmdlet tworzy pakiet. W przypadku zawartości innej firmy Użyj parametru **FilesToInclude** , aby dodać zawartość specyfikacji do pakietu. Nie musisz określać **ChefProfilePath** jako pakietów systemu Linux.

- **Nazwa**: Nazwa pakietu konfiguracji gościa.
- **Konfiguracja**: pełna ścieżka do skompilowanego dokumentu konfiguracyjnego.
- **Ścieżka**: ścieżka folderu wyjściowego. Ten parametr jest opcjonalny. Jeśli nie zostanie określony, pakiet zostanie utworzony w bieżącym katalogu.
- **FilesoInclude**: pełna ścieżka do profilu INSPEC.

Uruchom następujące polecenie, aby utworzyć pakiet przy użyciu konfiguracji podanych w poprzednim kroku:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'wmi_service' `
  -Configuration './Config/wmi_service.mof' `
  -FilesToInclude './wmi_service'  `
  -Path './package' 
```

## <a name="policy-lifecycle"></a>Cykl życia zasad

Jeśli chcesz wydać aktualizację zasad, istnieją trzy pola, które wymagają uwagi.

> [!NOTE]
> `version`Właściwość przypisania konfiguracji gościa ma wpływ tylko na pakiety hostowane przez firmę Microsoft. Najlepszym rozwiązaniem w przypadku przechowywania wersji zawartości niestandardowej jest uwzględnienie wersji w nazwie pliku.

- **Wersja**: po uruchomieniu `New-GuestConfigurationPolicy` polecenia cmdlet należy określić numer wersji większy niż aktualnie opublikowany.
- **contentUri**: po uruchomieniu `New-GuestConfigurationPolicy` polecenia cmdlet należy określić identyfikator URI dla lokalizacji pakietu. Dołączenie wersji pakietu do nazwy pliku zapewni zmianę wartości tej właściwości w każdej wersji.
- **contentHash**: Ta właściwość jest automatycznie aktualizowana przez `New-GuestConfigurationPolicy` polecenie cmdlet. Jest to wartość skrótu pakietu utworzonego przez `New-GuestConfigurationPackage` . Właściwość musi być poprawna dla `.zip` publikowanych plików. Jeśli zostanie zaktualizowana tylko właściwość **contentUri** , rozszerzenie nie zaakceptuje pakietu zawartości.

Najprostszym sposobem zwolnienia zaktualizowanego pakietu jest powtórzenie procesu opisanego w tym artykule i udostępnienie zaktualizowanego numeru wersji. Ten proces gwarantuje, że wszystkie właściwości zostały prawidłowo zaktualizowane.

## <a name="optional-signing-guest-configuration-packages"></a>Opcjonalne: podpisywanie pakietów konfiguracji gościa

Zasady niestandardowe konfiguracji gościa używają skrótu SHA256, aby zweryfikować, że pakiet zasad nie został zmieniony.
Opcjonalnie klienci mogą również używać certyfikatu do podpisywania pakietów i wymuszania rozszerzenia konfiguracji gościa, aby zezwalać tylko na podpisaną zawartość.

Aby włączyć ten scenariusz, należy wykonać dwa kroki. Uruchom polecenie cmdlet, aby podpisać pakiet zawartości, i Dołącz tag do maszyn, które powinny wymagać podpisania kodu.

Aby skorzystać z funkcji walidacji podpisu, uruchom `Protect-GuestConfigurationPackage` polecenie cmdlet w celu podpisania pakietu przed jego opublikowaniem. To polecenie cmdlet wymaga certyfikatu "podpisywanie kodu".

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

Parametry `Protect-GuestConfigurationPackage` polecenia cmdlet:

- **Ścieżka**: pełna ścieżka pakietu konfiguracji gościa.
- **Certyfikat**: certyfikat podpisywania kodu w celu podpisania pakietu. Ten parametr jest obsługiwany tylko podczas podpisywania zawartości dla systemu Windows.

Agent GuestConfiguration oczekuje, że klucz publiczny certyfikatu ma być obecny w "zaufanych głównych urzędach certyfikacji" na maszynach z systemem Windows i w ścieżce `/usr/local/share/ca-certificates/extra` na komputerach z systemem Linux. Aby węzeł mógł zweryfikować podpisaną zawartość, przed zastosowaniem zasad niestandardowych Zainstaluj klucz publiczny certyfikatu na komputerze. Ten proces można wykonać przy użyciu dowolnej techniki wewnątrz maszyny wirtualnej lub za pomocą Azure Policy. Przykładowy szablon jest [dostępny tutaj](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows).
Zasady dostępu Key Vault muszą zezwalać dostawcy zasobów obliczeniowych na dostęp do certyfikatów podczas wdrożeń. Aby uzyskać szczegółowe instrukcje, zobacz [konfigurowanie Key Vault dla maszyn wirtualnych w programie Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Poniżej znajduje się przykład eksportowania klucza publicznego z certyfikatu podpisywania w celu zaimportowania go do maszyny.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

Po opublikowaniu zawartości Dodaj tag o nazwie `GuestConfigPolicyCertificateValidation` i wartości `enabled` do wszystkich maszyn wirtualnych, na których powinno być wymagane podpisywanie kodu. Zapoznaj się z [przykładami znaczników](../samples/built-in-policies.md#tags) , w jaki sposób można dostarczyć Tagi na dużą skalę przy użyciu Azure Policy. Po zastosowaniu tego tagu definicja zasad wygenerowana przy użyciu `New-GuestConfigurationPolicy` polecenia cmdlet włącza wymaganie za pośrednictwem rozszerzenia konfiguracji gościa.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat inspekcji maszyn wirtualnych z [konfiguracją gościa](../concepts/guest-configuration.md).
- Dowiedz się, jak [programowo utworzyć zasady](./programmatically-create.md).
- Dowiedz się, jak [uzyskać dane zgodności](./get-compliance-data.md).
