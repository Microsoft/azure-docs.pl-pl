---
title: Jak tworzyć zasady konfiguracji gościa dla systemu Linux
description: Dowiedz się, jak utworzyć Azure Policy zasady konfiguracji gościa dla systemu Linux.
ms.date: 08/17/2020
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9d80ae44e5cc34ec3b3378f8ed4a68cc02464216
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92542900"
---
# <a name="how-to-create-guest-configuration-policies-for-linux"></a>Jak tworzyć zasady konfiguracji gościa dla systemu Linux

Przed utworzeniem zasad niestandardowych zapoznaj się z omówieniem w temacie [Azure Policy konfiguracja gościa](../concepts/guest-configuration.md).
 
Aby dowiedzieć się więcej o tworzeniu zasad konfiguracji gościa dla systemu Windows, zobacz stronę [jak utworzyć zasady konfiguracji gościa dla systemu Windows](./guest-configuration-create.md)

Podczas inspekcji systemu Linux konfiguracja gościa używa oprogramowania [Chef InSpec](https://www.inspec.io/). Profil oprogramowania InSpec definiuje stan, w jakim powinna być maszyna. Jeśli Ocena konfiguracji nie powiedzie się, zostanie wyzwolony efekt zasad **auditIfNotExists** i maszyna zostanie uznana za **niezgodną** .

[Azure Policy konfiguracja gościa](../concepts/guest-configuration.md) może być używana tylko do inspekcji ustawień wewnątrz maszyn. Korygowanie ustawień wewnątrz maszyn nie jest jeszcze dostępne.

Wykonaj poniższe czynności, aby utworzyć własną konfigurację służącą do sprawdzania poprawności stanu maszyny platformy Azure lub spoza niej.

> [!IMPORTANT]
> Do przeprowadzania inspekcji na maszynach wirtualnych platformy Azure jest wymagane rozszerzenie konfiguracji gościa. Aby wdrożyć rozszerzenie w skali na wszystkich komputerach z systemem Linux, Przypisz następującą definicję zasad: `Deploy prerequisites to enable Guest Configuration Policy on Linux VMs`

## <a name="install-the-powershell-module"></a>Zainstaluj moduł programu PowerShell

Moduł konfiguracji gościa automatyzuje proces tworzenia zawartości niestandardowej, w tym:

- Tworzenie artefaktu zawartości konfiguracji gościa (zip)
- Automatyczne testowanie artefaktu
- Tworzenie definicji zasad
- Publikowanie zasad

Moduł można zainstalować na komputerze z systemem Windows, macOS lub Linux przy użyciu programu PowerShell 6,2 lub nowszego uruchomionego lokalnie lub z [Azure Cloud Shell](https://shell.azure.com)lub z [obrazem platformy Docker Azure PowerShell Core](https://hub.docker.com/r/azuresdk/azure-powershell-core).

> [!NOTE]
> Kompilacja konfiguracji nie jest obsługiwana w systemie Linux.

### <a name="base-requirements"></a>Wymagania podstawowe

Systemy operacyjne, w których można zainstalować moduł:

- Linux
- macOS
- Windows

> [!NOTE]
> Polecenie cmdlet "test-GuestConfigurationPackage" wymaga OpenSSL w wersji 1,0 ze względu na zależność od OMI. Powoduje to błąd w dowolnym środowisku z OpenSSL 1,1 lub nowszym.

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

## <a name="guest-configuration-artifacts-and-policy-for-linux"></a>Artefakty konfiguracji gościa i zasady dla systemu Linux

Nawet w środowiskach systemu Linux konfiguracja gościa używa konfiguracji żądanego stanu jako abstrakcji języka. Implementacja jest oparta na kodzie macierzystym (C++), więc nie wymaga ładowania programu PowerShell. Jednak wymaga to konfiguracji pliku MOF opisującego szczegółowe informacje o środowisku.
Konfiguracja DSC działa jako otoka do standaryzacji w celu ujednolicenia sposobu wykonywania, sposobu, w jaki są podane parametry i sposobu, w jaki dane wyjściowe są zwracane do usługi. W przypadku pracy z niestandardową zawartością specyfikacji jest wymagana mała znajomość konfiguracji DSC.

#### <a name="configuration-requirements"></a>Wymagania dotyczące konfiguracji

Nazwa konfiguracji niestandardowej musi być spójna wszędzie. Nazwa pliku. zip pakietu zawartości, nazwa konfiguracji w pliku MOF i nazwa przypisywania gościa w szablonie Azure Resource Manager (szablon ARM) musi być taka sama.

### <a name="custom-guest-configuration-configuration-on-linux"></a>Konfiguracja niestandardowej konfiguracji gościa w systemie Linux

Konfiguracja gościa w systemie Linux używa `ChefInSpecResource` zasobu do zapewnienia aparatu o nazwie [profilu INSPEC](https://www.inspec.io/docs/reference/profiles/). **Nazwa** jest jedyną wymaganą właściwością zasobu. Utwórz plik YaML i plik skryptu Ruby, jak pokazano poniżej.

Najpierw utwórz plik YaML używany przez specyfikację. Plik zawiera podstawowe informacje o środowisku. Poniżej przedstawiono przykład:

```YaML
name: linux-path
title: Linux path
maintainer: Test
summary: Test profile
license: MIT
version: 1.0.0
supports:
    - os-family: unix
```

Zapisz ten plik o nazwie `inspec.yml` do folderu o nazwie `linux-path` w katalogu projektu.

Następnie utwórz plik Ruby przy użyciu abstrakcji języka INSPEC użytego do inspekcji maszyny.

```Ruby
describe file('/tmp') do
    it { should exist }
end
```

Zapisz ten plik o nazwie `linux-path.rb` w nowym folderze o nazwie `controls` wewnątrz `linux-path` katalogu.

Na koniec Utwórz konfigurację, zaimportuj moduł zasobów **PSDesiredStateConfiguration** i skompiluj konfigurację.

```powershell
# Define the configuration and import GuestConfiguration
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
        }
    }
}

# Compile the configuration to create the MOF files
import-module PSDesiredStateConfiguration
AuditFilePathExists -out ./Config
```

Zapisz ten plik o nazwie `config.ps1` w folderze projektu. Uruchom ją w programie PowerShell, wykonując `./config.ps1` w terminalu. Zostanie utworzony nowy plik MOF.

`Node AuditFilePathExists`Polecenie nie jest technicznie wymagane, ale tworzy plik o nazwie `AuditFilePathExists.mof` zamiast domyślnego, `localhost.mof` . Jeśli nazwa pliku MOF jest zgodna z konfiguracją, ułatwia organizowanie wielu plików podczas pracy w dużej skali.

Teraz należy mieć strukturę projektu w następujący sposób:

```file
/ AuditFilePathExists
    / Config
        AuditFilePathExists.mof
    / linux-path
        linux-path.yml
        / controls
            linux-path.rb 
```

Pliki pomocnicze muszą być spakowane razem. Ukończony pakiet jest używany przez konfigurację gościa do tworzenia definicji Azure Policy.

`New-GuestConfigurationPackage`Polecenie cmdlet tworzy pakiet. Parametry `New-GuestConfigurationPackage` polecenia cmdlet podczas tworzenia zawartości systemu Linux:

- **Nazwa** : Nazwa pakietu konfiguracji gościa.
- **Konfiguracja** : pełna ścieżka do skompilowanego dokumentu konfiguracyjnego.
- **Ścieżka** : ścieżka folderu wyjściowego. Ten parametr jest opcjonalny. Jeśli nie zostanie określony, pakiet zostanie utworzony w bieżącym katalogu.
- **ChefProfilePath** : pełna ścieżka do profilu INSPEC. Ten parametr jest obsługiwany tylko podczas tworzenia zawartości do inspekcji systemu Linux.

Uruchom następujące polecenie, aby utworzyć pakiet przy użyciu konfiguracji podanych w poprzednim kroku:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditFilePathExists' `
  -Configuration './Config/AuditFilePathExists.mof' `
  -ChefInSpecProfilePath './'
```

Po utworzeniu pakietu konfiguracyjnego, ale przed opublikowaniem go na platformie Azure, można przetestować pakiet z poziomu stacji roboczej lub środowiska ciągłej integracji i ciągłego wdrażania (CI/CD). Polecenie cmdlet GuestConfiguration `Test-GuestConfigurationPackage` zawiera tego samego agenta w środowisku deweloperskim, jak jest używane wewnątrz maszyn platformy Azure. Korzystając z tego rozwiązania, można przeprowadzić testowanie integracji lokalnie przed wydaniem do rozliczane środowiska chmury.

Ponieważ agent rzeczywiście ocenia środowisko lokalne, w większości przypadków należy uruchomić polecenie cmdlet Test-na tej samej platformie systemu operacyjnego, co planujesz przeprowadzić inspekcję.

Parametry `Test-GuestConfigurationPackage` polecenia cmdlet:

- **Nazwa** : Nazwa zasad konfiguracji gościa.
- **Parameter** : parametry zasad podane w formacie Hashtable.
- **Ścieżka** : pełna ścieżka pakietu konfiguracji gościa.

Uruchom następujące polecenie, aby przetestować pakiet utworzony przez poprzedni krok:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditFilePathExists/AuditFilePathExists.zip
```

Polecenie cmdlet obsługuje również dane wejściowe z potoku programu PowerShell. Potoku dane wyjściowe `New-GuestConfigurationPackage` polecenia cmdlet do `Test-GuestConfigurationPackage` polecenia cmdlet.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditFilePathExists -Configuration ./Config/AuditFilePathExists.mof -ChefProfilePath './' | Test-GuestConfigurationPackage
```

Następnym krokiem jest opublikowanie pliku na platformie Azure Blob Storage.  Polecenie `Publish-GuestConfigurationPackage` wymaga `Az.Storage` modułu.

```azurepowershell-interactive
Publish-GuestConfigurationPackage -Path ./AuditBitlocker.zip -ResourceGroupName myResourceGroupName -StorageAccountName myStorageAccountName
```

Po utworzeniu i przekazaniu niestandardowego pakietu zasad konfiguracji gościa Utwórz definicję zasad konfiguracji gościa. `New-GuestConfigurationPolicy`Polecenie cmdlet przyjmuje niestandardowy pakiet zasad i tworzy definicję zasad.

Parametry `New-GuestConfigurationPolicy` polecenia cmdlet:

- **ContentUri** : publiczny identyfikator URI http (s) dla pakietu zawartości konfiguracji gościa.
- **DisplayName** : Nazwa wyświetlana zasad.
- **Opis** : Opis zasad.
- **Parameter** : parametry zasad podane w formacie Hashtable.
- **Wersja** : wersja zasad.
- **Ścieżka** : ścieżka docelowa, w której są tworzone definicje zasad.
- **Platforma** : platforma docelowa (Windows/Linux) dla zasad konfiguracji gościa i pakietu zawartości.
- **Tag** dodaje jeden lub więcej filtrów tagów do definicji zasad
- **Kategoria** ustawia pole metadanych kategorii w definicji zasad

Poniższy przykład tworzy definicje zasad w określonej ścieżce z niestandardowego pakietu zasad:

```azurepowershell-interactive
New-GuestConfigurationPolicy `
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Platform 'Linux' `
    -Version 1.0.0 `
    -Verbose
```

Następujące pliki są tworzone przez `New-GuestConfigurationPolicy` :

- **auditIfNotExists.jsna**

Dane wyjściowe polecenia cmdlet zwracają obiekt zawierający nazwę wyświetlaną inicjatywy i ścieżkę plików zasad.

Na koniec Opublikuj definicje zasad przy użyciu `Publish-GuestConfigurationPolicy` polecenia cmdlet. Polecenie cmdlet ma tylko parametr **Path** wskazujący lokalizację plików JSON utworzonych przez `New-GuestConfigurationPolicy` .

Aby uruchomić polecenie publikowania, musisz mieć dostęp do tworzenia zasad na platformie Azure. Wymagania dotyczące autoryzacji są udokumentowane na stronie [przegląd Azure Policy](../overview.md) . Najlepsza wbudowana rola to **współautor zasad zasobów** .

```azurepowershell-interactive
Publish-GuestConfigurationPolicy `
  -Path '.\policyDefinitions'
```

 `Publish-GuestConfigurationPolicy`Polecenie cmdlet akceptuje ścieżkę z potoku programu PowerShell. Ta funkcja oznacza, że można tworzyć pliki zasad i publikować je w pojedynczym zestawie poleceń potokowych.

 ```azurepowershell-interactive
 New-GuestConfigurationPolicy `
  -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit Linux file path.' `
  -Description 'Audit that a file path exists on a Linux machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
 ```

W przypadku zasad utworzonych na platformie Azure ostatni krok to przypisanie definicji. Zobacz, jak przypisać definicję przy użyciu [portalu](../assign-policy-portal.md), [interfejsu wiersza polecenia platformy Azure](../assign-policy-azurecli.md)i [Azure PowerShell](../assign-policy-powershell.md).

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Używanie parametrów w niestandardowych zasadach konfiguracji gościa

Konfiguracja gościa obsługuje Zastępowanie właściwości konfiguracji w czasie wykonywania. Ta funkcja oznacza, że wartości w pliku MOF w pakiecie nie muszą być uznawane za statyczne. Wartości przesłonięć są udostępniane za pomocą Azure Policy i nie mają wpływu na sposób tworzenia lub kompilowania konfiguracji.

W przypadku wartości INSPEC parametry są zwykle obsługiwane jako dane wejściowe w czasie wykonywania lub jako kod przy użyciu atrybutów. Konfiguracja gościa zasłania ten proces, więc dane wejściowe można podać podczas przypisywania zasad. Plik atrybutów jest automatycznie tworzony w ramach maszyny. Nie musisz tworzyć i dodawać pliku w projekcie. Istnieją dwa kroki umożliwiające dodanie parametrów do projektu inspekcji systemu Linux.

Zdefiniuj dane wejściowe w pliku Ruby, w którym skrypt ma być poddawany inspekcji na komputerze. Poniżej znajduje się przykład.

```Ruby
attr_path = attribute('path', description: 'The file path to validate.')

describe file(attr_path) do
    it { should exist }
end
```

Polecenia cmdlet `New-GuestConfigurationPolicy` i `Test-GuestConfigurationPolicyPackage` zawierają parametr o nazwie **Parameter** . Ten parametr pobiera tablicę skrótów obejmującą wszystkie szczegóły każdego parametru i automatycznie tworzy wszystkie wymagane sekcje plików użytych do utworzenia każdej definicji Azure Policy.

W poniższym przykładzie jest tworzona definicja zasad służąca do inspekcji ścieżki pliku, gdzie użytkownik udostępnia ścieżkę w momencie przypisywania zasad.

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'FilePath'                             # Policy parameter name (mandatory)
        DisplayName = 'File path.'                    # Policy parameter display name (mandatory)
        Description = "File path to be audited."      # Policy parameter description (optional)
        ResourceType = "ChefInSpecResource"           # Configuration resource type (mandatory)
        ResourceId = 'Audit Linux path exists'        # Configuration resource property name (mandatory)
        ResourcePropertyName = "AttributesYmlContent" # Configuration resource property name (mandatory)
        DefaultValue = '/tmp'                         # Policy parameter default value (optional)
    }
)

# The hashtable also supports a property named 'AllowedValues' with an array of strings to limit input to a list

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Parameter $PolicyParameterInfo `
    -Version 1.0.0
```

W przypadku zasad systemu Linux Uwzględnij Właściwość **AttributesYmlContent** w konfiguracji i Zastąp wartości zgodnie z potrzebami. Agent konfiguracji gościa automatycznie tworzy plik YAML używany przez specyfikację do przechowywania atrybutów. Zobacz przykład poniżej.

```powershell
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
            AttributesYmlContent = "path: /tmp"
        }
    }
}
```

## <a name="policy-lifecycle"></a>Cykl życia zasad

Aby można było wydać aktualizację definicji zasad, istnieją dwa pola, które wymagają uwagi.

- **Wersja** : po uruchomieniu `New-GuestConfigurationPolicy` polecenia cmdlet należy określić numer wersji większy niż aktualnie opublikowany. Właściwość aktualizuje wersję przypisania konfiguracji gościa, aby Agent rozpoznał zaktualizowany pakiet.
- **contentHash** : Ta właściwość jest automatycznie aktualizowana przez `New-GuestConfigurationPolicy` polecenie cmdlet. Jest to wartość skrótu pakietu utworzonego przez `New-GuestConfigurationPackage` . Właściwość musi być poprawna dla `.zip` publikowanych plików. Jeśli zostanie zaktualizowana tylko właściwość **contentUri** , rozszerzenie nie zaakceptuje pakietu zawartości.

Najprostszym sposobem zwolnienia zaktualizowanego pakietu jest powtórzenie procesu opisanego w tym artykule i udostępnienie zaktualizowanego numeru wersji. Ten proces gwarantuje, że wszystkie właściwości zostały prawidłowo zaktualizowane.


### <a name="filtering-guest-configuration-policies-using-tags"></a>Filtrowanie zasad konfiguracji gościa za pomocą tagów

Zasady utworzone za pomocą poleceń cmdlet w module konfiguracji gościa mogą opcjonalnie zawierać filtr dla tagów. Parametr **-tag** programu `New-GuestConfigurationPolicy` obsługuje tablicę elementów Hashtable zawierających poszczególne Tagi. Tagi zostaną dodane do `If` sekcji definicji zasad i nie mogą być modyfikowane przez przypisanie zasady.

Przykładowy fragment definicji zasad, który będzie filtrowany dla tagów, podano poniżej.

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
      // Original Guest Configuration content will follow
    }
  ]
}
```

## <a name="optional-signing-guest-configuration-packages"></a>Opcjonalne: podpisywanie pakietów konfiguracji gościa

Zasady niestandardowe konfiguracji gościa używają skrótu SHA256, aby zweryfikować, że pakiet zasad nie został zmieniony.
Opcjonalnie klienci mogą również używać certyfikatu do podpisywania pakietów i wymuszania rozszerzenia konfiguracji gościa, aby zezwalać tylko na podpisaną zawartość.

Aby włączyć ten scenariusz, należy wykonać dwa kroki. Uruchom polecenie cmdlet, aby podpisać pakiet zawartości, i Dołącz tag do maszyn, które powinny wymagać podpisania kodu.

Aby skorzystać z funkcji walidacji podpisu, uruchom `Protect-GuestConfigurationPackage` polecenie cmdlet w celu podpisania pakietu przed jego opublikowaniem. To polecenie cmdlet wymaga certyfikatu "podpisywanie kodu".

Parametry `Protect-GuestConfigurationPackage` polecenia cmdlet:

- **Ścieżka** : pełna ścieżka pakietu konfiguracji gościa.
- **PublicGpgKeyPath** : Public GPG Key Path. Ten parametr jest obsługiwany tylko w przypadku podpisywania zawartości dla systemu Linux.

Odpowiednie informacje na temat tworzenia kluczy GPG do użycia z maszynami z systemem Linux są dostępne w artykule w witrynie GitHub, [generując nowy klucz GPG](https://help.github.com/en/articles/generating-a-new-gpg-key).

Agent GuestConfiguration oczekuje, że klucz publiczny certyfikatu ma być obecny w ścieżce `/usr/local/share/ca-certificates/extra` na komputerach z systemem Linux. Aby węzeł mógł zweryfikować podpisaną zawartość, przed zastosowaniem zasad niestandardowych Zainstaluj klucz publiczny certyfikatu na komputerze. Ten proces można wykonać przy użyciu dowolnej techniki wewnątrz maszyny wirtualnej lub za pomocą Azure Policy. Przykładowy szablon jest [dostępny tutaj](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows).
Zasady dostępu Key Vault muszą zezwalać dostawcy zasobów obliczeniowych na dostęp do certyfikatów podczas wdrożeń. Aby uzyskać szczegółowe instrukcje, zobacz [konfigurowanie Key Vault dla maszyn wirtualnych w programie Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Po opublikowaniu zawartości Dodaj tag o nazwie `GuestConfigPolicyCertificateValidation` i wartości `enabled` do wszystkich maszyn wirtualnych, na których powinno być wymagane podpisywanie kodu. Zapoznaj się z [przykładami znaczników](../samples/built-in-policies.md#tags) , w jaki sposób można dostarczyć Tagi na dużą skalę przy użyciu Azure Policy. Po zastosowaniu tego tagu definicja zasad wygenerowana przy użyciu `New-GuestConfigurationPolicy` polecenia cmdlet włącza wymaganie za pośrednictwem rozszerzenia konfiguracji gościa.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>Rozwiązywanie problemów z przypisaniami zasad konfiguracji gościa (Podgląd)

Narzędzie jest dostępne w wersji zapoznawczej, aby pomóc w rozwiązywaniu problemów z przypisaniami konfiguracji gościa Azure Policy. Narzędzie jest w wersji zapoznawczej i zostało opublikowane w Galeria programu PowerShell jako nazwa modułu [Narzędzia do rozwiązywania problemów z konfiguracją gościa](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Aby uzyskać więcej informacji na temat poleceń cmdlet w tym narzędziu, użyj polecenia Get-Help w programie PowerShell, aby wyświetlić wbudowane wskazówki. Ponieważ narzędzie pobiera częste aktualizacje, to najlepszy sposób uzyskiwania najnowszych informacji.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat inspekcji maszyn wirtualnych z [konfiguracją gościa](../concepts/guest-configuration.md).
- Dowiedz się, jak [programowo utworzyć zasady](./programmatically-create.md).
- Dowiedz się, jak [uzyskać dane zgodności](./get-compliance-data.md).
