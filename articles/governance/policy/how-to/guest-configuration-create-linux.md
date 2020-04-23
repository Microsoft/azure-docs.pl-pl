---
title: Jak utworzyć zasady konfiguracji gościa dla systemu Linux
description: Dowiedz się, jak utworzyć zasady konfiguracji gościa zasad platformy Azure dla systemu Linux.
ms.date: 03/20/2020
ms.topic: how-to
ms.openlocfilehash: 219b38bd81cae8d16241d1ee16cfdd2f400ae91e
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024986"
---
# <a name="how-to-create-guest-configuration-policies-for-linux"></a>Jak utworzyć zasady konfiguracji gościa dla systemu Linux

Przed utworzeniem zasad niestandardowych zapoznaj się z informacjami omówienia w [witrynie Konfiguracja gościa zasad azure](../concepts/guest-configuration.md).
 
Aby dowiedzieć się więcej o tworzeniu zasad konfiguracji gościa dla systemu Windows, zobacz stronę [Jak utworzyć zasady konfiguracji gościa dla systemu Windows](./guest-configuration-create.md)

Podczas inspekcji Linuksa, Konfiguracja gościa używa [Chef InSpec](https://www.inspec.io/). Profil InSpec definiuje warunek, w który powinna znajdować się maszyna. Jeśli ocena konfiguracji nie powiedzie się, audyt efektu **zasadIfIfNotExists** jest wyzwalany i maszyna jest uważana za **niezgodną**.

[Konfiguracja gościa zasad azure](../concepts/guest-configuration.md) może służyć tylko do inspekcji ustawień wewnątrz komputerów. Korygowanie ustawień wewnątrz maszyn nie jest jeszcze dostępne.

Poniższe akcje można utworzyć w celu sprawdzenia poprawności stanu komputera platformy Azure lub komputera platformy innych niż Azure.

> [!IMPORTANT]
> Zasady niestandardowe z konfiguracją gościa to funkcja podglądu.
>
> Rozszerzenie konfiguracji gościa jest wymagane do wykonywania inspekcji na maszynach wirtualnych platformy Azure.
> Aby wdrożyć rozszerzenie na dużą skalę na wszystkich komputerach z systemem Linux, przypisz następującą definicję zasad:
>   - [Wdrażanie wymagań wstępnych w celu włączenia zasad konfiguracji gościa na maszynach wirtualnych z systemem Linux.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb27e9e0-526e-4ae1-89f2-a2a0bf0f8a50)

## <a name="install-the-powershell-module"></a>Instalowanie modułu programu PowerShell

Tworzenie artefaktu konfiguracji gościa, automatyczne testowanie artefaktu, tworzenie definicji zasad i publikowanie zasad jest całkowicie zautomatyzowane przy użyciu modułu konfiguracji gościa w programie PowerShell. Moduł można zainstalować na komputerze z systemem Windows, macOS lub Linux z programem PowerShell 6.2 lub nowszym działającym lokalnie, z [usługą Azure Cloud Shell](https://shell.azure.com)lub z [obrazem platformy Azure PowerShell Core Docker](https://hub.docker.com/r/azuresdk/azure-powershell-core).

> [!NOTE]
> Kompilacja konfiguracji nie jest obsługiwana w systemie Linux.

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

## <a name="guest-configuration-artifacts-and-policy-for-linux"></a>Artefakty konfiguracji gościa i zasady dla systemu Linux

Nawet w środowiskach systemu Linux konfiguracja gościa używa konfiguracji żądanego stanu jako abstrakcji języka. Implementacja jest oparta na kodzie macierzystym (C++), więc nie wymaga ładowania programu PowerShell. Jednak wymaga konfiguracji MOF opisujące szczegóły dotyczące środowiska. DSC działa jako otoka dla InSpec do standaryzacji, jak jest wykonywany, jak parametry są dostarczane i jak dane wyjściowe są zwracane do usługi. Podczas pracy z niestandardową zawartością InSpec wymagana jest niewielka znajomość dsc.

#### <a name="configuration-requirements"></a>Wymagania konfiguracyjne

Nazwa konfiguracji niestandardowej musi być spójna wszędzie. Nazwa pliku zip dla pakietu zawartości, nazwa konfiguracji w pliku MOF i nazwa przypisania gościa w szablonie Menedżera zasobów muszą być takie same.

### <a name="custom-guest-configuration-configuration-on-linux"></a>Konfiguracja niestandardowej konfiguracji gościa w systemie Linux

Konfiguracja gościa w `ChefInSpecResource` systemie Linux używa zasobu, aby zapewnić silnikowi nazwę [profilu InSpec](https://www.inspec.io/docs/reference/profiles/). **Nazwa** jest jedyną wymaganą właściwością zasobu. Utwórz plik YaML i plik skryptu Ruby, jak opisano poniżej.

Najpierw utwórz plik YaML używany przez InSpec. Plik zawiera podstawowe informacje o środowisku. Poniżej podano przykład:

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

Zapisz ten plik `inspec.yml` o nazwie `linux-path` w folderze o nazwie w katalogu projektu.

Następnie utwórz plik Ruby z abstrakcją języka InSpec używaną do inspekcji komputera.

```Ruby
describe file('/tmp') do
    it { should exist }
end
```

Zapisz ten plik `linux-path.rb` z nazwą `controls` w `linux-path` nowym folderze o nazwie wewnątrz katalogu.

Na koniec utwórz konfigurację, zaimportuj moduł zasobu **PSDesiredStateConfiguration** i skompiluj konfigurację.

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

Zapisz ten plik `config.ps1` o nazwie w folderze projektu. Uruchom go w programie PowerShell, `./config.ps1` wykonując go w terminalu. Zostanie utworzony nowy plik mof.

Polecenie `Node AuditFilePathExists` nie jest technicznie wymagane, ale tworzy plik `AuditFilePathExists.mof` o nazwie, `localhost.mof`a nie domyślny, . Posiadanie nazwy pliku .mof zgodnie z konfiguracją ułatwia organizowanie wielu plików podczas pracy na dużą skalę.



Teraz powinieneś mieć strukturę projektu, jak poniżej:

```file
/ AuditFilePathExists
    / Config
        AuditFilePathExists.mof
    / linux-path
        linux-path.yml
        / controls
            linux-path.rb 
```

Pliki pomocnicze muszą być spakowane razem. Ukończony pakiet jest używany przez konfigurację gościa do tworzenia definicji zasad platformy Azure.

Polecenie `New-GuestConfigurationPackage` cmdlet tworzy pakiet. Parametry polecenia `New-GuestConfigurationPackage` cmdlet podczas tworzenia zawartości systemu Linux:

- **Nazwa**: Nazwa pakietu konfiguracji gościa.
- **Konfiguracja**: Skompilowany dokument konfiguracyjny pełną ścieżkę.
- **Ścieżka**: Ścieżka folderu wyjściowego. Ten parametr jest opcjonalny. Jeśli nie zostanie określony, pakiet jest tworzony w bieżącym katalogu.
- **ChefProfilePath**: Pełna ścieżka do profilu InSpec. Ten parametr jest obsługiwany tylko podczas tworzenia zawartości do inspekcji systemu Linux.

Uruchom następujące polecenie, aby utworzyć pakiet przy użyciu konfiguracji podanej w poprzednim kroku:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditFilePathExists' `
  -Configuration './Config/AuditFilePathExists.mof' `
  -ChefInSpecProfilePath './'
```

Po utworzeniu pakietu konfiguracji, ale przed opublikowaniem go na platformie Azure, można przetestować pakiet ze stacji roboczej lub środowiska ciągłej integracji/ciągłego wdrażania. Polecenie cmdlet `Test-GuestConfigurationPackage` guestconfiguration zawiera tego samego agenta w środowisku programistycznym, który jest używany na komputerach platformy Azure. Za pomocą tego rozwiązania można wykonać testowanie integracji lokalnie przed zwolnieniem do środowisk chmury rozliczane.

Ponieważ agent faktycznie ocenia środowisko lokalne, w większości przypadków należy uruchomić polecenie cmdlet testowe na tej samej platformie systemu operacyjnego, co planujesz przeprowadzić inspekcję.

Parametry polecenia `Test-GuestConfigurationPackage` cmdlet:

- **Nazwa**: Nazwa zasad konfiguracji gościa.
- **Parametr**: Parametry zasad podane w formacie mieszalalnym.
- **Ścieżka:** Pełna ścieżka pakietu konfiguracji gościa.

Uruchom następujące polecenie, aby przetestować pakiet utworzony przez poprzedni krok:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditFilePathExists/AuditFilePathExists.zip
```

Polecenie cmdlet obsługuje również dane wejściowe z potoku programu PowerShell. Przesuń `New-GuestConfigurationPackage` wyjście polecenia `Test-GuestConfigurationPackage` cmdlet do polecenia cmdlet.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditFilePathExists -Configuration ./Config/AuditFilePathExists.mof -ChefProfilePath './' | Test-GuestConfigurationPackage
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
  -filePath ./AuditFilePathExists.zip `
  -blobName 'AuditFilePathExists'
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
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Platform 'Linux' `
    -Version 1.0.0 `
    -Verbose
```

Następujące pliki są `New-GuestConfigurationPolicy`tworzone przez:

- **audytIfNotExists.json**
- **deployIfNotExists.json**
- **Inicjatywa.json**

Wyjście polecenia cmdlet zwraca obiekt zawierający nazwę wyświetlaną inicjatywy i ścieżkę plików zasad.

Na koniec opublikuj definicje zasad `Publish-GuestConfigurationPolicy` przy użyciu polecenia cmdlet.
Polecenie cmdlet ma tylko parametr **Path,** który wskazuje lokalizację `New-GuestConfigurationPolicy`plików JSON utworzonych przez program .

Aby uruchomić polecenie Publikuj, potrzebujesz dostępu do tworzenia zasad na platformie Azure. Określone wymagania dotyczące autoryzacji są udokumentowane na stronie [Omówienie zasad platformy Azure.](../overview.md) Najlepszą wbudowaną rolą jest **współautor zasad zasobów**.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy `
  -Path '.\policyDefinitions'
```

 Polecenie `Publish-GuestConfigurationPolicy` cmdlet akceptuje ścieżkę z potoku programu PowerShell. Ta funkcja oznacza, że można utworzyć pliki zasad i opublikować je w jednym zestawie poleceń potoku.

 ```azurepowershell-interactive
 New-GuestConfigurationPolicy `
  -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit Linux file path.' `
  -Description 'Audit that a file path exists on a Linux machine.' `
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

Z InSpec parametry są zazwyczaj obsługiwane jako dane wejściowe w czasie wykonywania lub jako kod przy użyciu atrybutów. Konfiguracja gościa zaciemnia ten proces, dzięki czemu dane wejściowe mogą być dostarczane po przypisaniu zasad. Plik atrybutów jest tworzony automatycznie na komputerze. Nie trzeba tworzyć i dodawać pliku w projekcie. Istnieją dwa kroki do dodawania parametrów do projektu inspekcji systemu Linux.

Zdefiniuj dane wejściowe w pliku Ruby, w którym skrypt, co do inspekcji na komputerze. Poniżej podano przykład.

```Ruby
attr_path = attribute('path', description: 'The file path to validate.')

describe file(attr_path) do
    it { should exist }
end
```

Polecenia cmdlet `New-GuestConfigurationPolicy` `Test-GuestConfigurationPolicyPackage` i zawierają parametr o nazwie **Parametry**. Ten parametr przyjmuje skrót, w tym wszystkie szczegóły dotyczące każdego parametru i automatycznie tworzy wszystkie wymagane sekcje plików używanych do tworzenia każdej definicji zasad platformy Azure.

Poniższy przykład tworzy definicję zasad do inspekcji ścieżki pliku, gdzie użytkownik udostępnia ścieżkę w czasie przypisywania zasad.

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
    -Parameters $PolicyParameterInfo `
    -Version 1.0.0
```

W przypadku zasad systemu Linux należy uwzględnić właściwość **AttributesYmlContent** w konfiguracji i zastąpić wartości w razie potrzeby. Agent konfiguracji gościa automatycznie tworzy plik YAML używany przez inspec do przechowywania atrybutów. Zobacz przykład poniżej.

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

Aby zwolnić aktualizację definicji zasad, istnieją dwa pola, które wymagają uwagi.

- **Wersja:** Po uruchomieniu `New-GuestConfigurationPolicy` polecenia cmdlet należy określić numer wersji większy niż aktualnie opublikowany. Właściwość aktualizuje wersję przypisania konfiguracji gościa, dzięki czemu agent rozpoznaje zaktualizowany pakiet.
- **contentHash:** Ta właściwość jest `New-GuestConfigurationPolicy` aktualizowana automatycznie przez polecenie cmdlet. Jest to wartość skrótu pakietu utworzonego przez `New-GuestConfigurationPackage`. Właściwość musi być `.zip` poprawna dla pliku, który publikujesz. Jeśli tylko **contentUri** właściwość jest aktualizowana, rozszerzenie nie akceptuje pakietu zawartości.

Najprostszym sposobem wydania zaktualizowanego pakietu jest powtórzenie procesu opisanego w tym artykule i podanie zaktualizowanego numeru wersji. Ten proces gwarantuje, że wszystkie właściwości zostały poprawnie zaktualizowane.

## <a name="optional-signing-guest-configuration-packages"></a>Opcjonalnie: Podpisywanie pakietów konfiguracyjnych gościa

Niestandardowe zasady konfiguracji gościa używają skrótu SHA256 do sprawdzania poprawności pakietu zasad nie uległy zmianie.
Opcjonalnie klienci mogą również używać certyfikatu do podpisywania pakietów i wymuszania rozszerzenia konfiguracji gościa, aby zezwalać tylko na podpisaną zawartość.

Aby włączyć ten scenariusz, istnieją dwa kroki, które należy wykonać. Uruchom polecenie cmdlet, aby podpisać pakiet zawartości i dołączyć znacznik do maszyn, które powinny wymagać podpisania kodu.

Aby użyć funkcji sprawdzania poprawności `Protect-GuestConfigurationPackage` podpisu, uruchom polecenie cmdlet, aby podpisać pakiet przed jego opublikowaniem. To polecenie cmdlet wymaga certyfikatu "Podpisywanie kodu".

Parametry polecenia `Protect-GuestConfigurationPackage` cmdlet:

- **Ścieżka:** Pełna ścieżka pakietu konfiguracji gościa.
- **PublicGpgKeyPath**: Ścieżka klucza publicznego GPG. Ten parametr jest obsługiwany tylko podczas podpisywania zawartości dla systemu Linux.

Dobrym punktem odniesienia do tworzenia kluczy GPG do użytku z komputerami z systemem Linux jest artykuł na Temat GitHub, [Generowanie nowego klucza GPG](https://help.github.com/en/articles/generating-a-new-gpg-key).

Agent konfiguracji guestconfiguration oczekuje, że klucz publiczny `/usr/local/share/ca-certificates/extra` certyfikatu będzie obecny w ścieżce na komputerach z systemem Linux. Aby węzeł weryfikował podpisaną zawartość, zainstaluj klucz publiczny certyfikatu na komputerze przed zastosowaniem zasad niestandardowych. Ten proces można wykonać przy użyciu dowolnej techniki wewnątrz maszyny Wirtualnej lub przy użyciu zasad platformy Azure. Przykładowy szablon znajduje [się tutaj](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows).
Zasady dostępu usługi Key Vault muszą umożliwiać dostawcy zasobów obliczeniowych dostęp do certyfikatów podczas wdrożeń. Aby uzyskać szczegółowe kroki, zobacz [Konfigurowanie magazynu kluczy dla maszyn wirtualnych w usłudze Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Po opublikowaniu zawartości dołącz tag o `GuestConfigPolicyCertificateValidation` nazwie `enabled` i wartości do wszystkich maszyn wirtualnych, na których powinno być wymagane podpisywanie kodu. Zobacz [przykłady tagów,](../samples/built-in-policies.md#tags) jak tagi mogą być dostarczane na dużą skalę przy użyciu usługi Azure Policy. Po wprowadzeniu tego tagu definicja zasad `New-GuestConfigurationPolicy` wygenerowana przy użyciu polecenia cmdlet umożliwia wymaganie za pośrednictwem rozszerzenia konfiguracji gościa.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>Rozwiązywanie problemów z przypisaniami zasad konfiguracji gościa (wersja zapoznawcza)

Narzędzie jest dostępne w wersji zapoznawczej, aby pomóc w rozwiązywaniu problemów z przypisaniami konfiguracji gościa usługi Azure Policy. Narzędzie znajduje się w wersji zapoznawczej i zostało opublikowane w Galerii programu PowerShell jako narzędzie do rozwiązywania problemów [z konfiguracją gościa](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/)o nazwie modułu.

Aby uzyskać więcej informacji na temat poleceń cmdlet w tym narzędziu, użyj polecenia Get-Help w programie PowerShell, aby wyświetlić wbudowane wskazówki. Ponieważ narzędzie jest coraz częste aktualizacje, jest to najlepszy sposób, aby uzyskać najnowsze informacje.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o inspekcji maszyn wirtualnych za pomocą [konfiguracji gościa](../concepts/guest-configuration.md).
- Dowiedz się, jak [programowo tworzyć zasady](programmatically-create.md).
- Dowiedz się, jak [uzyskać dane dotyczące zgodności](get-compliance-data.md).
