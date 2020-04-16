---
title: Rejestrowanie usługi Azure Key Vault | Dokumenty firmy Microsoft
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
ms.openlocfilehash: e9198892f95635add27bcfe9e479d0dd6fe3f08d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422592"
---
# <a name="azure-key-vault-logging"></a>Funkcja rejestrowania usługi Azure Key Vault

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Po utworzeniu jednego lub więcej magazynów kluczy prawdopodobnie będziesz chciał monitorować, jak i kiedy są dostępne magazyny kluczy i przez kogo. Można to zrobić, włączając rejestrowanie usługi Azure Key Vault, która zapisuje informacje na koncie magazynu platformy Azure, które udostępniasz. Nowy kontener o nazwie **insights-logs-auditevent** jest tworzony automatycznie dla określonego konta magazynu. Tego samego konta magazynu można używać do zbierania dzienników dla wielu magazynów kluczy.

Dostęp do informacji rejestrowania można uzyskać co najwyżej 10 minut (najwyżej) po operacji magazynu kluczy. W większości przypadków czas będzie krótszy.  To Ty zarządzasz dziennikami na swoim koncie magazynu:

* Użyj standardowych metod kontroli dostępu platformy Azure w celu zabezpieczenia dzienników, wprowadzając ograniczenia co do tego, kto może uzyskiwać do nich dostęp.
* Usuń dzienniki, których nie chcesz już przechowywać na koncie magazynu.

Użyj tego samouczka, aby rozpocząć pracę z funkcją rejestrowania usługi Azure Key Vault. Utworzysz konto magazynu, włączysz rejestrowanie i zinterpretujesz zebrane informacje dziennika.  

> [!NOTE]
> Ten samouczek nie zawiera instrukcji dotyczących sposobu tworzenia magazynów kluczy, kluczy ani kluczy tajnych. Aby uzyskać te informacje, zobacz [Co to jest usługa Azure Key Vault?](overview.md)). Lub, w przypadku instrukcji interfejsu wiersza polecenia platformy Azure między platformami, zobacz [ten równoważny samouczek).](manage-with-cli2.md)
>
> Ten artykuł zawiera instrukcje programu Azure PowerShell dotyczące aktualizowania rejestrowania diagnostycznego. Rejestrowanie diagnostyczne można również zaktualizować przy użyciu usługi Azure Monitor w sekcji **Dzienniki diagnostyczne** w witrynie Azure portal. 
>

Aby uzyskać omówienie informacji o usłudze Key Vault, zobacz [Co to jest usługa Azure Key Vault?](overview.md)). Aby uzyskać informacje o tym, gdzie jest dostępna usługa Key Vault, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Istniejący magazyn kluczy, który był przez Ciebie używany.  
* Azure PowerShell, minimalna wersja 1.0.0. Aby zainstalować program Azure PowerShell i skojarzyć go z subskrypcją platformy Azure, zobacz [Sposób instalowania i konfigurowania programu Azure PowerShell](/powershell/azure/overview). Jeśli masz już zainstalowany program Azure PowerShell i nie znasz wersji, z `$PSVersionTable.PSVersion`konsoli programu Azure PowerShell wprowadź .  
* Wystarczająca ilość miejsca w magazynie platformy Azure dla dzienników usługi Key Vault.

## <a name="connect-to-your-key-vault-subscription"></a><a id="connect"></a>Łączenie się z subskrypcją magazynu kluczy

Pierwszym krokiem w konfigurowaniu rejestrowania kluczy jest wskazywanie programu Azure PowerShell do magazynu kluczy, który chcesz zarejestrować.

Uruchom sesję programu Azure PowerShell i zaloguj się do konta platformy Azure za pomocą następującego polecenia:  

```powershell
Connect-AzAccount
```

W podręcznym oknie przeglądarki wprowadź nazwę użytkownika i hasło dla konta platformy Azure. Program Azure PowerShell pobiera wszystkie subskrypcje, które są skojarzone z tym kontem. Domyślnie program PowerShell używa pierwszego.

Może być trzeba określić subskrypcję, która została użyta do utworzenia magazynu kluczy. Wprowadź następujące polecenie, aby wyświetlić subskrypcje konta:

```powershell
Get-AzSubscription
```

Następnie, aby określić subskrypcję skojarzoną z magazynem kluczy, który będzie rejestrowany, wprowadź:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

Wskazywanie programu PowerShell do odpowiedniej subskrypcji jest ważnym krokiem, zwłaszcza jeśli masz wiele subskrypcji skojarzonych z kontem. Aby uzyskać więcej informacji na temat konfigurowania programu Azure PowerShell, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

## <a name="create-a-storage-account-for-your-logs"></a><a id="storage"></a>Tworzenie konta magazynu dla dzienników

Chociaż można użyć istniejącego konta magazynu dla dzienników, utworzymy konto magazynu, które będzie dedykowane do dzienników usługi Key Vault. Dla wygody, gdy musimy określić to później, będziemy przechowywać szczegóły w zmiennej o nazwie **sa**.

Aby uzyskać dodatkową łatwość zarządzania, użyjemy również tej samej grupy zasobów, co ta zawierająca magazyn kluczy. Z [samouczka wprowadzenie,](../secrets/quick-create-cli.md)ta grupa zasobów nosi nazwę **ContosoResourceGroup**i będziemy nadal używać lokalizacji Azji Wschodniej. W stosownych przypadkach zastąp te wartości własnymi:

```powershell
 $sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> Jeśli zdecydujesz się użyć istniejącego konta magazynu, musi używać tej samej subskrypcji co magazyn kluczy. I musi używać modelu wdrażania usługi Azure Resource Manager, a nie klasycznego modelu wdrażania.
>
>

## <a name="identify-the-key-vault-for-your-logs"></a><a id="identify"></a>Identyfikowanie magazynu kluczy dla dzienników

W [samouczku wprowadzenie](../secrets/quick-create-cli.md)nazwa magazynu kluczy to **ContosoKeyVault**. Będziemy nadal używać tej nazwy i przechowywać szczegóły w zmiennej o nazwie **kv:**

```powershell
$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'
```

## <a name="enable-logging"></a><a id="enable"></a>Włącz rejestrowanie

Aby włączyć rejestrowanie dla magazynu kluczy, użyjemy polecenia cmdlet **Set-AzDiagnosticSetting** wraz ze zmiennymi utworzonymi dla nowego konta magazynu i magazynu kluczy. Ustawimy również flagę **-Enabled,** aby **$true** i ustawiliśmy kategorię na **Inspekcja** (jedyna kategoria rejestrowania usługi Key Vault):

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

Dane wyjściowe wyglądają następująco:

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0

To dane wyjściowe potwierdza, że rejestrowanie jest teraz włączone dla magazynu kluczy i zapisze informacje na koncie magazynu.

Opcjonalnie można ustawić zasady przechowywania dzienników, tak aby starsze dzienniki były automatycznie usuwane. Na przykład ustaw zasady przechowywania, ustawiając flagę **-RetentionEnabled** **na $true**i ustaw parametr **-RetentionInDays** na **90,** aby dzienniki starsze niż 90 dni były automatycznie usuwane.

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

Co jest rejestrowane:

* Wszystkie uwierzytelnione żądania interfejsu API REST, w tym żądania nieudane w wyniku uprawnień dostępu, błędów systemowych lub złych żądań.
* Operacje w samym magazynie kluczy, w tym tworzenie, usuwanie, ustawianie zasad dostępu do magazynu kluczy i aktualizowanie atrybutów magazynu kluczy, takich jak tagi.
* Operacje dotyczące kluczy i wpisów tajnych w magazynie kluczy, w tym:
  * Tworzenie, modyfikowanie lub usuwanie tych kluczy lub wpisów tajnych.
  * Podpisywanie, weryfikowanie, szyfrowanie, odszyfrowywanie, zawijanie i rozpakowywanie kluczy, uzyskiwanie wpisów tajnych oraz wyświetlanie listy kluczy i wpisów tajnych (i ich wersji).
* Nieuwierzytelnione żądania, które powodują uzyskanie odpowiedzi 401. Przykładami są żądania, które nie mają tokenu nośnika, które są zniekształcone lub wygasły lub które mają nieprawidłowy token.  

## <a name="access-your-logs"></a><a id="access"></a>Uzyskiwanie dostępu do dzienników

Dzienniki usługi Key Vault są przechowywane w kontenerze **insights-logs-auditevent** na podanym koncie magazynu. Aby wyświetlić dzienniki, należy pobrać obiekty BLOB.

Najpierw utwórz zmienną dla nazwy kontenera. Użyjesz tej zmiennej w pozostałej części instruktażu.

```powershell
$container = 'insights-logs-auditevent'
```

Aby wyświetlić listę wszystkich obiektów blob w tym kontenerze, wprowadź:

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

Jak widać z tego danych wyjściowych, obiekty blob są zgodne z konwencją nazewnictwa:`resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Wartości daty i godziny używają czasu UTC.

Ponieważ można użyć tego samego konta magazynu do zbierania dzienników dla wielu zasobów, pełny identyfikator zasobu w nazwie obiektu blob jest przydatny do uzyskania dostępu lub pobrania tylko potrzebne obiekty BLOB. Jednak zanim do tego przejdziemy, najpierw zostanie omówiony sposób pobierania wszystkich obiektów blob.

Utwórz folder, aby pobrać obiekty blob. Przykład:

```powershell 
New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force
```

Następnie uzyskaj listę wszystkich obiektów blob:  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Potok tej listy za pośrednictwem **Get-AzStorageBlobContent** pobrać obiekty blob do folderu docelowego:

```powershell
$blobs | Get-AzStorageBlobContent -Destination C:\Users\username\ContosoKeyVaultLogs'
```

Po uruchomieniu tego drugiego **/** polecenia ogranicznik w nazwach obiektów blob tworzy pełną strukturę folderów w folderze docelowym. Użyjesz tej struktury do pobierania i przechowywania obiektów blob jako plików.

Aby selektywnie pobierać obiekty blob, użyj symboli wieloznacznych. Przykład:

* Jeśli masz wiele magazynów kluczy i chcesz pobrać dzienniki dla tylko jednego magazynu kluczy o nazwie CONTOSOKEYVAULT3:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* Jeśli masz wiele grup zasobów i chcesz pobrać dzienniki dla tylko jednej grupy zasobów, użyj parametru `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* Jeśli chcesz pobrać wszystkie dzienniki w styczniu 2019, `-Blob '*/year=2019/m=01/*'`użyj :

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

Teraz możesz rozpocząć wyszukiwanie informacji zawartych w dziennikach. Ale zanim przejdziemy do tego, powinieneś znać jeszcze dwa polecenia:

* Aby wykonać zapytanie o stan ustawień diagnostycznych dla zasobu magazynu kluczy: `Get-AzDiagnosticSetting -ResourceId $kv.ResourceId`
* Aby wyłączyć rejestrowanie dla zasobu magazynu kluczy: `Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Category AuditEvent`

## <a name="interpret-your-key-vault-logs"></a><a id="interpret"></a>Interpretowanie dzienników usługi Key Vault

Poszczególne obiekty blob są przechowywane jako tekst w formacie obiektu blob JSON. Przyjrzyjmy się przykładowy wpis dziennika. Uruchom następujące polecenie:

```powershell
Get-AzKeyVault -VaultName 'contosokeyvault'`
```

Zwraca wpis dziennika podobny do tego:

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

W poniższej tabeli wymieniono nazwy i opisy pól:

| Nazwa pola | Opis |
| --- | --- |
| **Czas** |Data i godzina w utc. |
| **Resourceid** |Identyfikator zasobu usługi Azure Resource Manager. W przypadku dzienników usługi Key Vault jest to zawsze identyfikator zasobu magazynu kluczy. |
| **Operationname** |Nazwa operacji zgodnie z opisem w następnej tabeli. |
| **operationVersion** |WERSJA INTERFEJSU API REST żądana przez klienta. |
| **Kategorii** |Typ wyniku. W przypadku dzienników usługi Key Vault **AuditEvent** jest pojedynczą, dostępną wartością. |
| **resultType** |Wynik żądania interfejsu API REST. |
| **resultSignature** |Stan HTTP. |
| **resultDescription** |Dodatkowy opis wyniku, jeśli jest dostępny. |
| **durationMs** |Czas potrzebny do obsłużenia żądania interfejsu API REST podany w milisekundach. Nie obejmuje opóźnienia sieci, więc czas zmierzony po stronie klienta może być niezgodny z tym czasem. |
| **callerIpAddress** |Adres IP klienta, który złożył żądanie. |
| **Correlationid** |Opcjonalny identyfikator GUID, który klient może przekazać w celu skorelowania dzienników po stronie klienta z dziennikami po stronie usługi (Key Vault). |
| **Tożsamości** |Tożsamość z tokenu, który został przedstawiony w żądaniu interfejsu API REST. Zazwyczaj jest to "użytkownik", "podmiot usługi" lub kombinacja "użytkownik+identyfikator aplikacji", jak w przypadku żądania, które wynika z polecenia cmdlet programu Azure PowerShell. |
| **Właściwości** |Informacje, które różnią się w zależności od operacji (**operationName**). W większości przypadków to pole zawiera informacje o kliencie (ciąg agenta użytkownika przekazywany przez klienta), dokładny identyfikator URI żądania interfejsu API REST i kod stanu HTTP. Ponadto, gdy obiekt jest zwracany w wyniku żądania (na przykład **KeyCreate** lub **VaultGet**), zawiera również klucz URI (jako "id"), identyfikator URI magazynu lub tajny identyfikator URI. |

Wartości pól **operationName** są w formacie *ObjectVerb.* Przykład:

* Wszystkie operacje przechowalni `Vault<action>` `VaultGet` kluczy mają format, taki jak i `VaultCreate`.
* Wszystkie kluczowe operacje `Key<action>` mają format, taki jak `KeySign` i `KeyList`.
* Wszystkie tajne operacje `Secret<action>` mają format, taki jak `SecretGet` i `SecretListVersions`.

W poniższej tabeli wymieniono wartości **operationName** i odpowiadające im polecenia interfejsu API REST:

| operationName | REST API, polecenie |
| --- | --- |
| **Authentication** |Uwierzytelnianie za pośrednictwem punktu końcowego usługi Azure Active Directory |
| **VaultGet** |[Pobierz informacje o magazynie kluczy](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[Utwórz lub zaktualizuj magazyn kluczy](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[Usuń magazyn kluczy](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[Zaktualizuj magazyn kluczy](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[Utwórz listę wszystkich magazynów kluczy w grupie zasobów](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[Utwórz klucz](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[Pobierz informacje o kluczu](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **KeyImport** |[Importuj klucz do magazynu](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **KeyBackup** |[3.](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
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
| **SecretGet** |[Zdobądź tajemnicę](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[Zaktualizuj klucz tajny](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[Usuń klucz tajny](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[Utwórz listę kluczy tajnych w magazynie](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[Utwórz listę wersji klucza tajnego](https://msdn.microsoft.com/library/azure/dn986824.aspx) |

## <a name="use-azure-monitor-logs"></a><a id="loganalytics"></a>Korzystanie z dzienników usługi Azure Monitor

Za pomocą rozwiązania Usługi Key Vault w dziennikach usługi Azure Monitor można przejrzeć dzienniki **zdarzeń inspekcji usługi** Key Vault. W dziennikach usługi Azure Monitor używasz zapytań dziennika do analizowania danych i uzyskania potrzebnych informacji. 

Aby uzyskać więcej informacji, w tym jak to skonfigurować, zobacz [Rozwiązanie usługi Azure Key Vault w dziennikach usługi Azure Monitor.](../../azure-monitor/insights/azure-key-vault.md) Ten artykuł zawiera również instrukcje, jeśli chcesz przeprowadzić migrację ze starego rozwiązania usługi Key Vault, które było oferowane podczas podglądu dzienników usługi Azure Monitor, w którym najpierw przekierowano dzienniki do konta usługi Azure storage i skonfigurowano dzienniki usługi Azure Monitor do odczytu z tego miejsca.

## <a name="next-steps"></a><a id="next"></a>Następne kroki

Aby zapoznać się z samouczkiem, który używa usługi Azure Key Vault w aplikacji sieci web .NET, zobacz [Korzystanie z usługi Azure Key Vault z aplikacji sieci web](tutorial-net-create-vault-azure-web-app.md).

Odwołania dotyczące programowania znajdują się w [przewodniku dewelopera usługi Azure Key Vault](developers-guide.md).

Aby uzyskać listę poleceń cmdlet programu Azure PowerShell 1.0 dla usługi Azure Key Vault, zobacz [polecenia cmdlet usługi Azure Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault).

Aby zapoznać się z samouczkiem na temat rotacji kluczy i inspekcji dzienników za pomocą usługi Azure Key Vault, zobacz [Konfigurowanie usługi Key Vault z rotacją i inspekcją kluczy end-to-end.](../secrets/key-rotation-log-monitoring.md)
