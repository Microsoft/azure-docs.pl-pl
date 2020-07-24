---
title: Rejestrowanie Azure Key Vault | Microsoft Docs
description: Użyj tego samouczka, aby rozpocząć pracę z funkcją rejestrowania usługi Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 9c5b07d402219907337a590e1131691fb1e24cc2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87090590"
---
# <a name="azure-key-vault-logging"></a>Funkcja rejestrowania usługi Azure Key Vault

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Po utworzeniu co najmniej jednego magazynu kluczy prawdopodobnie zechcesz monitorować sposób i czas uzyskiwania dostępu do Twoich magazynów kluczy oraz przez kogo. Można to zrobić przez włączenie rejestrowania dla Azure Key Vault, które zapisuje informacje na koncie usługi Azure Storage, które podano. Nowy kontener o nazwie **Insights-Logs-auditevent** jest tworzony automatycznie dla określonego konta magazynu. Tego samego konta magazynu można używać do zbierania dzienników dla wielu magazynów kluczy.

Możesz uzyskać dostęp do informacji o rejestrowaniu 10 minut (maksymalnie) po operacji magazynu kluczy. W większości przypadków czas będzie krótszy.  To Ty zarządzasz dziennikami na swoim koncie magazynu:

* Użyj standardowych metod kontroli dostępu platformy Azure w celu zabezpieczenia dzienników, wprowadzając ograniczenia co do tego, kto może uzyskiwać do nich dostęp.
* Usuń dzienniki, których nie chcesz już przechowywać na koncie magazynu.

Użyj tego samouczka, aby rozpocząć pracę z funkcją rejestrowania usługi Azure Key Vault. Utworzysz konto magazynu, włączysz rejestrowanie i interpretujesz zebrane informacje dziennika.  

> [!NOTE]
> Ten samouczek nie zawiera instrukcji dotyczących sposobu tworzenia magazynów kluczy, kluczy ani kluczy tajnych. Aby uzyskać więcej informacji, zobacz [co to jest Azure Key Vault?](overview.md)). Lub, aby uzyskać instrukcje dla wieloplatformowego interfejsu wiersza polecenia platformy Azure, zobacz [ten odpowiednik samouczka](manage-with-cli2.md)).
>
> Ten artykuł zawiera Azure PowerShell instrukcje dotyczące aktualizowania rejestrowania diagnostycznego. Rejestrowanie diagnostyczne można również zaktualizować za pomocą Azure Monitor w sekcji **dzienniki diagnostyczne** Azure Portal. 
>

Aby uzyskać przegląd informacji na temat Key Vault, zobacz [co to jest Azure Key Vault?](overview.md)). Informacje o tym, gdzie Key Vault są dostępne, można znaleźć na [stronie z cennikiem](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Istniejący magazyn kluczy, który był przez Ciebie używany.  
* Azure PowerShell, minimalna wersja 1.0.0. Aby zainstalować program Azure PowerShell i skojarzyć go z subskrypcją platformy Azure, zobacz [Sposób instalowania i konfigurowania programu Azure PowerShell](/powershell/azure/). Jeśli zainstalowano już Azure PowerShell i nie znasz wersji, w konsoli Azure PowerShell wprowadź wartość `$PSVersionTable.PSVersion` .  
* Wystarczająca ilość miejsca w magazynie platformy Azure dla dzienników usługi Key Vault.

## <a name="connect-to-your-key-vault-subscription"></a><a id="connect"></a>Nawiązywanie połączenia z subskrypcją magazynu kluczy

Pierwszym krokiem w konfigurowaniu rejestrowania kluczy jest wskazanie Azure PowerShell do magazynu kluczy, który ma zostać zarejestrowana.

Rozpocznij sesję Azure PowerShell i zaloguj się do konta platformy Azure przy użyciu następującego polecenia:  

```powershell
Connect-AzAccount
```

W podręcznym oknie przeglądarki wprowadź nazwę użytkownika i hasło dla konta platformy Azure. Azure PowerShell pobiera wszystkie subskrypcje, które są skojarzone z tym kontem. Domyślnie program PowerShell używa pierwszego z nich.

Być może trzeba będzie określić subskrypcję, która została użyta do utworzenia magazynu kluczy. Wprowadź następujące polecenie, aby wyświetlić subskrypcje dla konta:

```powershell
Get-AzSubscription
```

Następnie, aby określić subskrypcję skojarzoną z magazynem kluczy, który będziesz rejestrować, wprowadź:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

Wskazywanie programu PowerShell z odpowiednią subskrypcją jest ważnym krokiem, szczególnie w przypadku, gdy masz wiele subskrypcji skojarzonych z Twoim kontem. Aby uzyskać więcej informacji na temat konfigurowania programu Azure PowerShell, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/).

## <a name="create-a-storage-account-for-your-logs"></a><a id="storage"></a>Tworzenie konta magazynu dla dzienników

Mimo że możesz użyć istniejącego konta magazynu dla dzienników, utworzymy konto magazynu, które będzie przeznaczone do Key Vault dzienników. Aby dowiedzieć się, w jaki sposób należy określić tę opcję później, dane będą przechowywane w zmiennej o nazwie **sa**.

Aby ułatwić zarządzanie, użyjemy również tej samej grupy zasobów, która zawiera Magazyn kluczy. W [samouczku z wprowadzeniem](../secrets/quick-create-cli.md)ta grupa zasobów ma nazwę **ContosoResourceGroup**i będziemy nadal korzystać z lokalizacji Azja Wschodnia. Zastąp te wartości własnymi, stosownie do potrzeb:

```powershell
 $sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> Jeśli zdecydujesz się użyć istniejącego konta magazynu, musi on używać tej samej subskrypcji co Magazyn kluczy. I musi korzystać z modelu wdrażania Azure Resource Manager, a nie klasycznego modelu wdrażania.
>
>

## <a name="identify-the-key-vault-for-your-logs"></a><a id="identify"></a>Identyfikowanie magazynu kluczy dla dzienników

W [samouczku z wprowadzeniem](../secrets/quick-create-cli.md)nazwa magazynu kluczy została **ContosoKeyVault**. Będziemy nadal używać tej nazwy i przechowywać szczegóły w zmiennej o nazwie **KV**:

```powershell
$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'
```

## <a name="enable-logging-using-azure-powershell"></a><a id="enable"></a>Włącz rejestrowanie przy użyciu Azure PowerShell

Aby włączyć rejestrowanie dla Key Vault, użyjemy polecenia cmdlet **Set-AzDiagnosticSetting** wraz ze zmiennymi utworzonymi dla nowego konta magazynu i magazynem kluczy. Ustawimy również flagę **-Enabled** na **$true** i ustawimy kategorię na `AuditEvent` (jedyną kategorię dla Key Vault rejestrowania):

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

Dane wyjściowe wyglądają następująco:

```output
StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccountContosoKeyVaultLogs
ServiceBusRuleId   :
StorageAccountName :
    Logs
    Enabled           : True
    Category          : AuditEvent
    RetentionPolicy
    Enabled : False
    Days    : 0
```

Dane wyjściowe potwierdzają, że rejestrowanie jest teraz włączone dla magazynu kluczy i będzie zapisywać informacje na koncie magazynu.

Opcjonalnie możesz ustawić zasady przechowywania dla dzienników, tak aby starsze dzienniki były automatycznie usuwane. Na przykład ustaw zasady przechowywania przez ustawienie flagi **-retentionenable** na **$true**i ustaw parametr **-RetentionInDays** na **90** , aby dzienniki starsze niż 90 dni zostały automatycznie usunięte.

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

Co jest rejestrowane:

* Wszystkie uwierzytelnione żądania interfejsu API REST, w tym żądania zakończone niepowodzeniem w wyniku uprawnień dostępu, błędów systemu lub nieudanych żądań.
* Operacje związane z magazynem kluczy, w tym tworzenie, usuwanie, Ustawianie zasad dostępu magazynu kluczy i aktualizowanie atrybutów magazynu kluczy, takich jak Tagi.
* Operacje dotyczące kluczy i wpisów tajnych w magazynie kluczy, w tym:
  * Tworzenie, modyfikowanie lub usuwanie tych kluczy lub wpisów tajnych.
  * Podpisywanie, weryfikowanie, szyfrowanie, odszyfrowywanie, zawijanie i depakowanie kluczy, pobieranie wpisów tajnych i wyświetlanie listy kluczy i wpisów tajnych (oraz ich wersji).
* Nieuwierzytelnione żądania, które powodują uzyskanie odpowiedzi 401. Przykłady to żądania, które nie mają tokenu okaziciela, które są źle sformułowane lub wygasłe lub które mają nieprawidłowy token.  

## <a name="enable-logging-using-azure-cli"></a>Włączanie rejestrowania przy użyciu interfejsu wiersza polecenia platformy Azure

```azurecli
az login

az account set --subscription {AZURE SUBSCRIPTION ID}

az provider register -n Microsoft.KeyVault

az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="access-your-logs"></a><a id="access"></a>Uzyskiwanie dostępu do dzienników

Dzienniki Key Vault są przechowywane w kontenerze **Insights-Logs-auditevent** na podanym koncie magazynu. Aby wyświetlić dzienniki, należy pobrać obiekty blob.

Najpierw utwórz zmienną dla nazwy kontenera. Ta zmienna zostanie użyta w pozostałej części przewodnika.

```powershell
$container = 'insights-logs-auditevent'
```

Aby wyświetlić listę wszystkich obiektów BLOB w tym kontenerze, wpisz:

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

Dane wyjściowe wyglądają podobnie do następujących:

```
Container Uri: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent

Name

---
resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json
```

Jak widać na podstawie tych danych wyjściowych, obiekty blob są zgodne z konwencją nazewnictwa:`resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Wartości daty i godziny używają czasu UTC.

Ponieważ do zbierania dzienników dla wielu zasobów można użyć tego samego konta magazynu, pełny identyfikator zasobu w nazwie obiektu BLOB jest przydatny do uzyskiwania dostępu do obiektów blob, które są potrzebne. Jednak zanim do tego przejdziemy, najpierw zostanie omówiony sposób pobierania wszystkich obiektów blob.

Utwórz folder, aby pobrać obiekty blob. Na przykład:

```powershell 
New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force
```

Następnie uzyskaj listę wszystkich obiektów blob:  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Potoku tej listy za pomocą elementu **Get-AzStorageBlobContent** w celu pobrania obiektów BLOB do folderu docelowego:

```powershell
$blobs | Get-AzStorageBlobContent -Destination C:\Users\username\ContosoKeyVaultLogs'
```

Po uruchomieniu drugiego polecenia **/** ogranicznik w nazwach obiektów BLOB tworzy pełną strukturę folderów w folderze docelowym. Ta struktura będzie używana do pobierania i przechowywania obiektów BLOB jako plików.

Aby selektywnie pobierać obiekty blob, użyj symboli wieloznacznych. Na przykład:

* Jeśli masz wiele magazynów kluczy i chcesz pobrać dzienniki dla tylko jednego magazynu kluczy o nazwie CONTOSOKEYVAULT3:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* Jeśli masz wiele grup zasobów i chcesz pobrać dzienniki dla tylko jednej grupy zasobów, użyj parametru `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* Jeśli chcesz pobrać wszystkie dzienniki przez miesiąc stycznia 2019, użyj `-Blob '*/year=2019/m=01/*'` :

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

Teraz możesz rozpocząć wyszukiwanie informacji zawartych w dziennikach. Jednak przed przejściem do tego celu należy znać dwa więcej poleceń:

* Aby wykonać zapytanie o stan ustawień diagnostycznych dla zasobu magazynu kluczy: `Get-AzDiagnosticSetting -ResourceId $kv.ResourceId`
* Aby wyłączyć rejestrowanie dla zasobu magazynu kluczy: `Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Category AuditEvent`


## <a name="interpret-your-key-vault-logs"></a><a id="interpret"></a>Interpretowanie dzienników usługi Key Vault

Poszczególne obiekty blob są przechowywane jako tekst w formacie obiektu blob JSON. Przyjrzyjmy się przykładowym wpisowi dziennika. 

```json
    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

W poniższej tabeli wymieniono nazwy pól i opisy:

| Nazwa pola | Opis |
| --- | --- |
| **pierwszym** |Data i godzina w formacie UTC. |
| **Identyfikator** |Azure Resource Manager identyfikator zasobu. W przypadku dzienników Key Vault jest to zawsze identyfikator zasobu Key Vault. |
| **operationName** |Nazwa operacji zgodnie z opisem w następnej tabeli. |
| **operationVersion** |Wersja interfejsu API REST żądana przez klienta. |
| **kategorii** |Typ wyniku. W przypadku dzienników Key Vault `AuditEvent` jest to jedyna dostępna wartość. |
| **Result** |Wynik żądania interfejsu API REST. |
| **resultSignature** |Stan HTTP. |
| **resultDescription** |Dodatkowy opis wyniku, jeśli jest dostępny. |
| **Milisekundach)** |Czas potrzebny do obsłużenia żądania interfejsu API REST podany w milisekundach. Nie obejmuje opóźnienia sieci, więc czas zmierzony po stronie klienta może być niezgodny z tym czasem. |
| **callerIpAddress** |Adres IP klienta, który wykonał żądanie. |
| **korelacj** |Opcjonalny identyfikator GUID, który klient może przekazać w celu skorelowania dzienników po stronie klienta z dziennikami po stronie usługi (Key Vault). |
| **Identity** |Tożsamość z tokenu, która została przedstawiona w żądaniu interfejsu API REST. Zwykle jest to "użytkownik", "Nazwa główna usługi" lub kombinacja "użytkownik + appId", jak w przypadku żądania, które wynika z Azure PowerShell polecenia cmdlet. |
| **aœciwoœci** |Informacje, które różnią się w zależności od operacji (**OperationName**). W większości przypadków to pole zawiera informacje o kliencie (ciąg agenta użytkownika przekazaną przez klienta), dokładny identyfikator URI żądania interfejsu API REST i kod stanu HTTP. Ponadto, gdy obiekt jest zwracany w wyniku żądania (na przykład **Create** lub **VaultGet**), zawiera również identyfikator URI klucza (AS `id` ), identyfikator URI magazynu lub identyfikator URI wpisu tajnego. |

Wartości pola **OperationName** są w formacie *ObjectVerb* . Na przykład:

* Wszystkie operacje magazynu kluczy mają `Vault<action>` Format, taki jak `VaultGet` i `VaultCreate` .
* Wszystkie operacje na kluczach mają `Key<action>` Format, taki jak `KeySign` i `KeyList` .
* Wszystkie operacje tajne mają `Secret<action>` Format, taki jak `SecretGet` i `SecretListVersions` .

W poniższej tabeli wymieniono wartości **OperationName** i odpowiednie polecenia interfejsu API REST:

| operationName | Polecenie interfejsu API REST |
| --- | --- |
| **Uwierzytelnianie** |Uwierzytelnianie za pośrednictwem punktu końcowego Azure Active Directory |
| **VaultGet** |[Pobierz informacje o magazynie kluczy](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[Utwórz lub zaktualizuj magazyn kluczy](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[Usuń magazyn kluczy](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[Zaktualizuj magazyn kluczy](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[Utwórz listę wszystkich magazynów kluczy w grupie zasobów](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[Utwórz klucz](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[Pobierz informacje o kluczu](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **KeyImport** |[Importuj klucz do magazynu](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **KeyBackup** |[Tworzenie kopii zapasowej klucza](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
| **KeyDelete** |[Usuń klucz](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| **KeyRestore** |[Przywróć klucz](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| **KeySign** |[Podpisz przy użyciu klucza](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| **KeyVerify** |[Weryfikuj za pomocą klucza](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| **KeyWrap** |[Opakuj klucz](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| **KeyUnwrap** |[Odpakuj klucz](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| **KeyEncrypt** |[Szyfruj za pomocą klucza](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| **KeyDecrypt** |[Odszyfruj za pomocą klucza](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| **KeyUpdate** |[Zaktualizuj klucz](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| **KeyList** |[Utwórz listę kluczy w magazynie](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| **KeyListVersions** |[Utwórz listę wersji klucza](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| **SecretSet** |[Utwórz klucz tajny](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| **SecretGet** |[Pobierz wpis tajny](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[Zaktualizuj klucz tajny](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[Usuń klucz tajny](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[Utwórz listę kluczy tajnych w magazynie](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[Utwórz listę wersji klucza tajnego](https://msdn.microsoft.com/library/azure/dn986824.aspx) |

## <a name="use-azure-monitor-logs"></a><a id="loganalytics"></a>Korzystanie z dzienników Azure Monitor

Możesz użyć rozwiązania Key Vault w dziennikach Azure Monitor do przeglądania dzienników Key Vault `AuditEvent` . W dziennikach Azure Monitor są używane zapytania dzienników do analizowania danych i uzyskiwania potrzebnych informacji. 

Aby uzyskać więcej informacji, w tym o sposobie konfigurowania tego elementu, zobacz [Azure Key Vault w Azure monitor](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="next-steps"></a><a id="next"></a>Następne kroki

Aby zapoznać się z samouczkiem korzystającym Azure Key Vault w aplikacji sieci Web platformy .NET, zobacz [Korzystanie z Azure Key Vault z aplikacji sieci Web](tutorial-net-create-vault-azure-web-app.md).

Odwołania dotyczące programowania znajdują się w [przewodniku dewelopera usługi Azure Key Vault](developers-guide.md).

Aby uzyskać listę poleceń cmdlet Azure PowerShell 1,0 dla Azure Key Vault, zobacz [polecenia cmdlet Azure Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault).
