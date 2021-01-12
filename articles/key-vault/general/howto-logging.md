---
title: Jak włączyć rejestrowanie Azure Key Vault
description: Jak włączyć rejestrowanie Azure Key Vault, które zapisuje informacje na koncie usługi Azure Storage, które podano.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/01/2020
ms.author: mbaldwin
ms.openlocfilehash: 0e1ce841f6da8f15bd977437bca6b835a7b0d745
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108742"
---
# <a name="how-to-enable-key-vault-logging"></a>Jak włączyć rejestrowanie Key Vault

Po utworzeniu co najmniej jednego magazynu kluczy prawdopodobnie zechcesz monitorować sposób i czas uzyskiwania dostępu do Twoich magazynów kluczy oraz przez kogo. Aby uzyskać szczegółowe informacje na temat tej funkcji, zobacz [rejestrowanie Key Vault](logging.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Istniejący magazyn kluczy, który był przez Ciebie używany.  
* [Azure Cloud Shell](https://shell.azure.com) — środowisko bash
* Wystarczająca ilość miejsca w magazynie platformy Azure dla dzienników usługi Key Vault.

Te polecenia przewodnika są formatowane dla [Cloud Shell](https://shell.azure.com) z bash jako środowiska.

## <a name="connect-to-your-key-vault-subscription"></a>Nawiązywanie połączenia z subskrypcją usługi Key Vault

Pierwszy krok konfigurowania rejestrowania kluczy nawiązuje połączenie z subskrypcją zawierającą magazyn kluczy. Jest to szczególnie ważne, jeśli masz wiele subskrypcji skojarzonych z Twoim kontem.

Za pomocą interfejsu wiersza polecenia platformy Azure można wyświetlić wszystkie subskrypcje za pomocą poleceń [AZ Account List](/cli/azure/account?view=azure-cli-latest#az_account_list) , a następnie połączyć się z jednym przy użyciu [AZ Account Set](/cli/azure/account?view=azure-cli-latest#az_account_set):

```azurecli-interactive
az account list

az account set --subscription "<subscriptionID>"
```

Za pomocą Azure PowerShell można najpierw wystawić subskrypcje za pomocą polecenia cmdlet [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?view=azps-4.7.0) , a następnie połączyć się z jednym przy użyciu polecenia cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-4.7.0) : 

```powershell-interactive
Get-AzSubscription

Set-AzContext -SubscriptionId "<subscriptionID>"
```

## <a name="create-a-storage-account-for-your-logs"></a>Tworzenie konta magazynu dla dzienników

Mimo że możesz użyć istniejącego konta magazynu dla dzienników, utworzymy nowe konto magazynu przeznaczone do Key Vault dzienników. 

Aby ułatwić zarządzanie, użyjemy również tej samej grupy zasobów, która zawiera Magazyn kluczy. W [interfejsie wiersza polecenia platformy Azure — szybki start](quick-create-cli.md) i [Azure PowerShell szybki start](quick-create-powershell.md), ta grupa zasobów ma nazwę moja **zasobów**, a lokalizacja to *Wschodnie*. Zastąp te wartości własnymi, stosownie do potrzeb. 

Należy również podać nazwę konta magazynu. Nazwy kont magazynu muszą mieć unikatową długość od 3 do 24 znaków i używać tylko cyfr i małych liter.  Na koniec utworzymy konto magazynu dla jednostki SKU "Standard_LRS".

Korzystając z interfejsu wiersza polecenia platformy Azure, użyj polecenie [AZ Storage account Create](/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_create) .

```azurecli-interactive
az storage account create --name "<your-unique-storage-account-name>" -g "myResourceGroup" --sku "Standard_LRS"
```

Za pomocą Azure PowerShell Użyj polecenia cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount?view=azps-4.7.0) . Należy podać lokalizację odpowiadającą grupie zasobów.

```powershell
 New-AzStorageAccount -ResourceGroupName myResourceGroup -Name "<your-unique-storage-account-name>" -Type "Standard_LRS" -Location "eastus"
```

W obu przypadkach należy zwrócić uwagę na "Identyfikator" konta magazynu. Operacja interfejsu wiersza polecenia platformy Azure zwraca wartość "ID" w danych wyjściowych. Aby uzyskać identyfikator "ID" z Azure PowerShell, należy użyć polecenia [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount?view=azps-4.7.0) i przypisać dane wyjściowe do zmiennej $sa. Następnie można zobaczyć konto magazynu z $sa. ID. ("$Sa. Kontekst "właściwość zostanie również użyta w dalszej części tego artykułu.)

```powershell-interactive
$sa = Get-AzStorageAccount -Name "<your-unique-storage-account-name>" -ResourceGroup "myResourceGroup"
$sa.id
```

Identyfikator "ID" konta magazynu będzie miał format "/subscriptions/<IDENTYFIKATORem subskrypcji —>/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/<unikatowych nazw kont magazynu>".

> [!NOTE]
> Jeśli zdecydujesz się użyć istniejącego konta magazynu, musi on używać tej samej subskrypcji, co Magazyn kluczy, i musi korzystać z modelu wdrażania Azure Resource Manager, a nie z klasycznego modelu wdrażania.

## <a name="obtain-your-key-vault-resource-id"></a>Uzyskaj identyfikator zasobu magazynu kluczy

W [interfejsie wiersza polecenia szybki start](quick-create-cli.md) i programie [PowerShell](quick-create-powershell.md)utworzono klucz z unikatową nazwą.  Użyj tej nazwy ponownie w poniższych krokach.  Jeśli nie możesz zapamiętać nazwy magazynu kluczy, możesz użyć polecenia cmdlet [AZ Key magazyn list](/cli/azure/keyvault?view=azure-cli-latest#az_keyvault_list) lub Azure PowerShell [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault?view=azps-4.7.0) , aby je wyświetlić.

Użyj nazwy magazynu kluczy, aby znaleźć identyfikator zasobu.  Korzystając z interfejsu wiersza polecenia platformy Azure, użyj [AZ kluczy show](/cli/azure/keyvault?view=azure-cli-latest#az_keyvault_show) polecenie.

```azurecli-interactive
az keyvault show --name "<your-unique-keyvault-name>"
```

Za pomocą Azure PowerShell Użyj polecenia cmdlet [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault?view=azps-4.7.0) .

```powershell-interactive
Get-AzKeyVault -VaultName "<your-unique-keyvault-name>"
```

Identyfikator zasobu dla magazynu kluczy będzie miał format "/subscriptions/<IDENTYFIKATORem subskrypcji,>/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/<identyfikatora magazynu unikatowych nazw>". Zwróć uwagę na następny krok.

## <a name="enable-logging-using-azure-powershell"></a>Włącz rejestrowanie przy użyciu Azure PowerShell

Aby włączyć rejestrowanie Key Vault, użyjemy interfejsu wiersza polecenia platformy Azure [AZ monitor Diagnostic-Settings Create](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest) lub polecenia cmdlet [Set-AZDIAGNOSTICSETTING](/powershell/module/az.monitor/set-azdiagnosticsetting?view=azps-4.7.0) wraz z identyfikatorem konta magazynu i identyfikatorem zasobu magazynu kluczy.

```azurecli-interactive
az monitor diagnostic-settings create --storage-account "<storage-account-id>" --resource "<key-vault-resource-id>" --name "Key vault logs" --logs '[{"category": "AuditEvent","enabled": true}]' --metrics '[{"category": "AllMetrics","enabled": true}]'
```

Za pomocą Azure PowerShell użyjemy polecenia cmdlet [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting?view=azps-4.7.0) z flagą **-Enabled** ustawionym na **$true** i kategorię ustawioną na `AuditEvent` (jedyną kategorię dla rejestrowania Key Vault):

```powershell-interactive
Set-AzDiagnosticSetting -ResourceId "<key-vault-resource-id>" -StorageAccountId $sa.id -Enabled $true -Category "AuditEvent"
```

Opcjonalnie możesz ustawić zasady przechowywania dla dzienników, aby starsze dzienniki były automatycznie usuwane po upływie określonego czasu. Można na przykład ustawić zasady przechowywania, które automatycznie usuwają dzienniki starsze niż 90 dni.

<!-- With the Azure CLI, use the [az monitor diagnostic-settings update](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az_monitor_diagnostic_settings_update) command. 

```azurecli-interactive
az monitor diagnostic-settings update 
```
-->

Za pomocą Azure PowerShell Użyj polecenia cmdlet [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting?view=azps-4.7.0) . 

```powershell-interactive
Set-AzDiagnosticSetting "<key-vault-resource-id>" -StorageAccountId $sa.id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

Co to jest rejestrowane:

* Wszystkie uwierzytelnione żądania interfejsu API REST, w tym żądania zakończone niepowodzeniem w wyniku uprawnień dostępu, błędów systemu lub nieudanych żądań.
* Operacje związane z magazynem kluczy, w tym tworzenie, usuwanie, Ustawianie zasad dostępu magazynu kluczy i aktualizowanie atrybutów magazynu kluczy, takich jak Tagi.
* Operacje dotyczące kluczy i wpisów tajnych w magazynie kluczy, w tym:
  * Tworzenie, modyfikowanie lub usuwanie tych kluczy lub wpisów tajnych.
  * Podpisywanie, weryfikowanie, szyfrowanie, odszyfrowywanie, zawijanie i depakowanie kluczy, pobieranie wpisów tajnych i wyświetlanie listy kluczy i wpisów tajnych (oraz ich wersji).
* Nieuwierzytelnione żądania, które powodują uzyskanie odpowiedzi 401. Przykłady to żądania, które nie mają tokenu okaziciela, które są źle sformułowane lub wygasłe lub które mają nieprawidłowy token.  
* Event Grid zdarzenia powiadomień o zbliżającym się wygaśnięciu, wygaśnięciu i zmianie zasad dostępu do magazynu (nowe zdarzenie wersji nie jest rejestrowane). Zdarzenia są rejestrowane niezależnie od tego, czy utworzono subskrypcję zdarzeń w magazynie kluczy. Aby uzyskać więcej informacji, zobacz [schemat zdarzeń Event Grid dla Key Vault](../../event-grid/event-schema-key-vault.md)

## <a name="access-your-logs"></a>Uzyskiwanie dostępu do dzienników

Dzienniki Key Vault są przechowywane w kontenerze "Insights-Logs-auditevent" na podanym koncie magazynu. Aby wyświetlić dzienniki, należy pobrać obiekty blob.

Najpierw utwórz listę wszystkich obiektów BLOB w kontenerze.  Korzystając z interfejsu wiersza polecenia platformy Azure, użyj polecenie [AZ Storage BLOB list](/cli/azure/storage/blob?view=azure-cli-latest#az_storage_blob_list) .

```azurecli-interactive
az storage blob list --account-name "<your-unique-storage-account-name>" --container-name "insights-logs-auditevent"
```

W Azure PowerShell Użyj listy [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob?view=azps-4.7.0) wszystkich obiektów BLOB w tym kontenerze, wpisz:

```powershell
Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context
```

Jak widać na podstawie danych wyjściowych polecenia cmdlet platformy Azure lub Azure PowerShell, nazwy obiektów BLOB są w formacie `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json` . Wartości daty i godziny używają czasu UTC.

Ponieważ do zbierania dzienników dla wielu zasobów można użyć tego samego konta magazynu, pełny identyfikator zasobu w nazwie obiektu BLOB jest przydatny do uzyskiwania dostępu do obiektów blob, które są potrzebne. Jednak zanim do tego przejdziemy, najpierw zostanie omówiony sposób pobierania wszystkich obiektów blob.

Korzystając z interfejsu wiersza polecenia platformy Azure, użyj polecenie [AZ Storage BLOB Download](/cli/azure/storage/blob?view=azure-cli-latest#az_storage_blob_download) , przekaż je do nazw obiektów blob i ścieżkę do pliku, w którym chcesz zapisać wyniki.

```azurecli-interactive
az storage blob download --container-name "insights-logs-auditevent" --file <path-to-file> --name "<blob-name>" --account-name "<your-unique-storage-account-name>"
```

W Azure PowerShell Użyj polecenia cmdlet [gt-AzStorageBlobs](/powershell/module/az.storage/get-azstorageblob?view=azps-4.7.0) , aby uzyskać listę obiektów blob, a następnie wybierz potok do polecenia cmdlet [Get-AzStorageBlobContent](/powershell/module/az.storage/get-azstorageblobcontent?view=azps-4.7.0) w celu pobrania dzienników do wybranej ścieżki.

```powershell-interactive
$blobs = Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context | Get-AzStorageBlobContent -Destination "<path-to-file>"
```

Po uruchomieniu drugiego polecenia cmdlet w programie PowerShell, **/** ogranicznik w nazwach obiektów BLOB tworzy pełną strukturę folderów w folderze docelowym. Ta struktura będzie używana do pobierania i przechowywania obiektów BLOB jako plików.

Aby selektywnie pobierać obiekty blob, użyj symboli wieloznacznych. Na przykład:

* Jeśli masz wiele magazynów kluczy i chcesz pobrać dzienniki dla tylko jednego magazynu kluczy o nazwie CONTOSOKEYVAULT3:

  ```powershell
  Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* Jeśli masz wiele grup zasobów i chcesz pobrać dzienniki dla tylko jednej grupy zasobów, użyj parametru `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

  ```powershell
  Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* Jeśli chcesz pobrać wszystkie dzienniki przez miesiąc stycznia 2019, użyj `-Blob '*/year=2019/m=01/*'` :

  ```powershell
  Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

Teraz możesz rozpocząć wyszukiwanie informacji zawartych w dziennikach. Jednak przed przejściem do tego celu należy znać dwa więcej poleceń:

Aby uzyskać szczegółowe informacje na temat odczytywania dzienników, zobacz [Key Vault logging: interpretowanie dzienników Key Vault](logging.md#interpret-your-key-vault-logs)

## <a name="use-azure-monitor-logs"></a>Korzystanie z dzienników usługi Azure Monitor

Możesz użyć rozwiązania Key Vault w dziennikach Azure Monitor do przeglądania dzienników Key Vault `AuditEvent` . W dziennikach Azure Monitor są używane zapytania dzienników do analizowania danych i uzyskiwania potrzebnych informacji.

Aby uzyskać więcej informacji, w tym o sposobie konfigurowania tego elementu, zobacz [Azure Key Vault w Azure monitor](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje koncepcyjne, w tym sposób interpretacji dzienników Key Vault, zobacz [rejestrowanie Key Vault](logging.md)
- Aby zapoznać się z samouczkiem korzystającym Azure Key Vault w aplikacji sieci Web platformy .NET, zobacz [Korzystanie z Azure Key Vault z aplikacji sieci Web](tutorial-net-create-vault-azure-web-app.md).
- Odwołania dotyczące programowania znajdują się w [przewodniku dewelopera usługi Azure Key Vault](developers-guide.md).
