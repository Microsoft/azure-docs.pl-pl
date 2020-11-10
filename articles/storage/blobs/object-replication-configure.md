---
title: Konfigurowanie replikacji obiektów
titleSuffix: Azure Storage
description: Dowiedz się, jak skonfigurować replikację obiektów do asynchronicznego kopiowania blokowych obiektów blob z kontenera na jednym koncie magazynu do innego.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/09/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: e3503a9eef5c11db35684ca61fb1ee39525a465d
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427602"
---
# <a name="configure-object-replication-for-block-blobs"></a>Konfiguruj replikację obiektów dla blokowych obiektów BLOB

Replikacja obiektów asynchronicznie kopiuje blokowe obiekty blob między źródłowym kontem magazynu a kontem docelowym. Aby uzyskać więcej informacji na temat replikacji obiektów, zobacz [replikacja obiektów](object-replication-overview.md).

Podczas konfigurowania replikacji obiektów należy utworzyć zasady replikacji określające źródłowe konto magazynu i konto docelowe. Zasady replikacji obejmują co najmniej jedną regułę określającą kontener źródłowy i kontener docelowy i wskazujący, które blokowe obiekty blob w kontenerze źródłowym zostaną zreplikowane.

W tym artykule opisano sposób konfigurowania replikacji obiektów dla konta magazynu przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Możesz również użyć jednej z bibliotek klienta dostawcy zasobów usługi Azure Storage, aby skonfigurować replikację obiektów.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="create-a-replication-policy-and-rules"></a>Tworzenie zasad i reguł replikacji

Przed skonfigurowaniem replikacji obiektów Utwórz źródłową i docelową konto magazynu, jeśli jeszcze nie istnieją. Oba konta muszą być kontami magazynu ogólnego przeznaczenia w wersji 2. Aby uzyskać więcej informacji, zobacz [Tworzenie konta usługi Azure Storage](../common/storage-account-create.md).

Replikacja obiektów wymaga włączenia obsługi wersji obiektów BLOB zarówno dla konta źródłowego, jak i docelowego, a dla konta źródłowego jest włączone Źródło zmian obiektów BLOB. Aby dowiedzieć się więcej na temat przechowywania wersji obiektów blob, zobacz temat [przechowywanie wersji obiektów BLOB](versioning-overview.md). Aby dowiedzieć się więcej na temat źródła zmian, zobacz [Obsługa kanału informacyjnego zmiany w usłudze Azure Blob Storage](storage-blob-change-feed.md). Należy pamiętać, że włączenie tych funkcji może skutkować dodatkowymi kosztami.

Konto magazynu może stanowić konto źródłowe dla maksymalnie dwóch kont docelowych. Konta źródłowe i docelowe mogą znajdować się w tym samym regionie lub w różnych regionach. Mogą również znajdować się w różnych subskrypcjach i w różnych dzierżawach Azure Active Directory (Azure AD). Dla każdej pary kont można utworzyć tylko jedną zasadę replikacji.

Podczas konfigurowania replikacji obiektów należy utworzyć zasady replikacji na koncie docelowym za pośrednictwem dostawcy zasobów usługi Azure Storage. Po utworzeniu zasad replikacji usługa Azure Storage przypisze mu identyfikator zasad. Następnie należy powiązać te zasady replikacji z kontem źródłowym przy użyciu identyfikatora zasad. Aby replikacja była wykonywana, identyfikator zasad na kontach źródłowych i docelowych musi być taki sam.

Aby skonfigurować zasady replikacji obiektów dla konta magazynu, musisz mieć przypisaną rolę **współautor** Azure Resource Manager, zakresem do poziomu konta magazynu lub wyższą. Aby uzyskać więcej informacji, zobacz [role wbudowane platformy Azure](../../role-based-access-control/built-in-roles.md) w dokumentacji kontroli dostępu opartej na rolach (Azure RBAC).

### <a name="configure-object-replication-when-you-have-access-to-both-storage-accounts"></a>Skonfiguruj replikację obiektów, gdy masz dostęp do obu kont magazynu

Jeśli masz dostęp do konta magazynu źródłowego i docelowego, możesz skonfigurować zasady replikacji obiektów na obu kontach.

Przed skonfigurowaniem replikacji obiektów w Azure Portal należy utworzyć kontenery źródłowe i docelowe na odpowiednich kontach magazynu, jeśli jeszcze nie istnieją. Ponadto Włącz obsługę wersji obiektów blob i źródło zmian na koncie źródłowym i Włącz obsługę wersji obiektów BLOB na koncie docelowym.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Azure Portal automatycznie tworzy zasady na koncie źródłowym po ich skonfigurowaniu dla konta docelowego.

Aby utworzyć zasady replikacji w Azure Portal, wykonaj następujące kroki:

1. Przejdź do źródłowego konta magazynu w Azure Portal.
1. W obszarze **BLOB Service** wybierz pozycję **replikacja obiektów**.
1. Wybierz pozycję **Konfiguruj reguły replikacji**.
1. Wybierz subskrypcję docelową i konto magazynu.
1. W sekcji **pary kontenerów** wybierz kontener źródłowy z konta źródłowego i kontener docelowy z konta docelowego. Można utworzyć maksymalnie 10 par kontenerów dla każdej zasady replikacji.

    Na poniższej ilustracji przedstawiono zestaw reguł replikacji.

    :::image type="content" source="media/object-replication-configure/configure-replication-policy.png" alt-text="Zrzut ekranu przedstawiający reguły replikacji w Azure Portal":::

1. W razie potrzeby określ co najmniej jeden filtr, aby skopiować tylko obiekty blob, które pasują do wzorca prefiksu. Na przykład, jeśli określisz prefiks `b` , replikowane są tylko obiekty blob, których nazwa rozpoczyna się od tej litery. Jako część prefiksu można określić katalog wirtualny. Ciąg prefiksu nie obsługuje symboli wieloznacznych.

    Na poniższej ilustracji przedstawiono filtry, które ograniczają, które obiekty blob są kopiowane w ramach reguły replikacji.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-prefix.png" alt-text="Zrzut ekranu przedstawiający filtry dla reguły replikacji":::

1. Domyślnie zakres kopiowania jest ustawiony na kopiowanie tylko nowych obiektów. Aby skopiować wszystkie obiekty w kontenerze lub skopiować obiekty rozpoczynając od niestandardowej daty i godziny, wybierz łącze **Zmień** i skonfiguruj zakres kopiowania dla pary kontenerów.

    Na poniższym obrazie przedstawiono niestandardowy zakres kopiowania, który kopiuje obiekty z określonego dnia i czasu do momentu.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-scope.png" alt-text="Zrzut ekranu przedstawiający niestandardowy zakres kopiowania dla replikacji obiektów":::

1. Wybierz pozycję **Zapisz i Zastosuj** , aby utworzyć zasady replikacji i rozpocząć replikowanie danych.

Po skonfigurowaniu replikacji obiektów Azure Portal zostaną wyświetlone zasady replikacji i reguły, jak pokazano na poniższej ilustracji.

:::image type="content" source="media/object-replication-configure/object-replication-policies-portal.png" alt-text="Zrzut ekranu przedstawiający zasady replikacji obiektów w Azure Portal":::

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby utworzyć zasady replikacji przy użyciu programu PowerShell, najpierw Zainstaluj wersję [2.5.0](https://www.powershellgallery.com/packages/Az.Storage/2.5.0) lub nowszą modułu programu PowerShell AZ. Storage. Aby uzyskać więcej informacji o instalowaniu Azure PowerShell, zobacz [Install Azure PowerShell with PowerShellGet](/powershell/azure/install-az-ps).

Poniższy przykład przedstawia sposób tworzenia zasad replikacji na kontach źródłowych i docelowych. Pamiętaj, aby zamienić wartości w nawiasy kątowe własnymi wartościami:

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set variables.
$rgName = "<resource-group>"
$srcAccountName = "<source-storage-account>"
$destAccountName = "<destination-storage-account>"
$srcContainerName1 = "source-container1"
$destContainerName1 = "dest-container1"
$srcContainerName2 = "source-container2"
$destContainerName2 = "dest-container2"

# Enable blob versioning and change feed on the source account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable blob versioning on the destination account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -IsVersioningEnabled $true

# List the service properties for both accounts.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName

# Create containers in the source and destination accounts.
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName2
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName2

# Define replication rules for each container.
$rule1 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName1 `
    -DestinationContainer $destContainerName1 `
    -PrefixMatch b
$rule2 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName2 `
    -DestinationContainer $destContainerName2  `
    -MinCreationTime 2020-05-10T00:00:00Z

# Create the replication policy on the destination account.
$destPolicy = Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId default `
    -SourceAccount $srcAccountName `
    -Rule $rule1,$rule2

# Create the same policy on the source account.
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -InputObject $destPolicy
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby utworzyć zasady replikacji przy użyciu interfejsu wiersza polecenia platformy Azure, najpierw zainstaluj interfejs wiersza polecenia platformy Azure w wersji 2.11.1 lub nowszej. Aby uzyskać więcej informacji, zobacz Rozpoczynanie [pracy z interfejsem wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli).

Następnie Włącz obsługę wersji obiektów BLOB na kontach magazynu źródłowego i docelowego i Włącz źródło zmian na koncie źródłowym, wywołując polecenie [AZ Storage account BLOB-Service-Properties Update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) . Pamiętaj, aby zamienić wartości w nawiasy kątowe własnymi wartościami:

```azurecli
az login

az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-versioning

az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-change-feed

az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --enable-versioning
```

Utwórz kontenery źródłowe i docelowe na odpowiednich kontach magazynu.

```azurecli
az storage container create \
    --account-name <source-storage-account> \
    --name source-container-1 \
    --auth-mode login
az storage container create \
    --account-name <source-storage-account> \
    --name source-container-2 \
    --auth-mode login

az storage container create \
    --account-name <dest-storage-account> \
    --name dest-container-1 \
    --auth-mode login
az storage container create \
    --account-name <dest-storage-account> \
    --name dest-container-1 \
    --auth-mode login
```

Utwórz nowe zasady replikacji i skojarzoną regułę na koncie docelowym, wywołując metodę [AZ Storage account lub-Policy Create](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_create).

```azurecli
az storage account or-policy create \
    --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-account <source-storage-account> \
    --destination-account <dest-storage-account> \
    --source-container source-container-1 \
    --destination-container dest-container-1 \
    --min-creation-time '2020-09-10T00:00:00Z' \
    --prefix-match a

```

Usługa Azure Storage ustawia identyfikator zasad dla nowych zasad podczas jego tworzenia. Aby dodać do zasad dodatkowe reguły, wywołaj polecenie [AZ Storage account lub-Policy Rule Add](/cli/azure/storage/account/or-policy/rule#az_storage_account_or_policy_rule_add) i podaj identyfikator zasad.

```azurecli
az storage account or-policy rule add \
    --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-container source-container-2 \
    --destination-container dest-container-2 \
    --policy-id <policy-id> \
    --prefix-match b
```

Następnie utwórz zasady na koncie źródłowym przy użyciu identyfikatora zasad.

```azurecli
az storage account or-policy show \
    --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --policy-id <policy-id> |
    az storage account or-policy create --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --policy "@-"
```

---

### <a name="configure-object-replication-when-you-have-access-only-to-the-destination-account"></a>Skonfiguruj replikację obiektów, gdy masz dostęp tylko do konta docelowego

Jeśli nie masz uprawnień do źródłowego konta magazynu, możesz skonfigurować replikację obiektów na koncie docelowym i udostępnić plik JSON zawierający definicję zasad innemu użytkownikowi, aby utworzyć te same zasady na koncie źródłowym. Na przykład, jeśli konto źródłowe znajduje się w innej dzierżawie usługi Azure AD z konta docelowego, można użyć tej metody w celu skonfigurowania replikacji obiektów.

Pamiętaj, że musisz mieć przypisaną rolę **Współautora** Azure Resource Manager zakresem do poziomu docelowego konta magazynu lub wyższą, aby utworzyć zasady. Aby uzyskać więcej informacji, zobacz [role wbudowane platformy Azure](../../role-based-access-control/built-in-roles.md) w dokumentacji kontroli dostępu opartej na rolach (Azure RBAC).

Poniższa tabela zawiera podsumowanie wartości, które mają być używane dla identyfikatora zasad i identyfikatorów reguł w pliku JSON w każdym scenariuszu.

| Podczas tworzenia pliku JSON dla tego konta... | Ustaw identyfikator zasad i identyfikatory reguł na tę wartość... |
|-|-|
| Konto docelowe | Wartość *Domyślna* wartości ciągu. Usługa Azure Storage utworzy identyfikator zasad i identyfikatory reguł. |
| Konto źródłowe | Wartości identyfikatora zasad i identyfikatorów reguł zwracanych podczas pobierania zasad zdefiniowanych na koncie docelowym jako plik JSON. |

Poniższy przykład definiuje zasady replikacji na koncie docelowym z pojedynczą regułą zgodną z prefiksem *b* i ustawia minimalny czas tworzenia dla obiektów blob, które mają być replikowane. Pamiętaj, aby zamienić wartości w nawiasy kątowe własnymi wartościami:

```json
{
  "properties": {
    "policyId": "default",
    "sourceAccount": "<source-account>",
    "destinationAccount": "<dest-account>",
    "rules": [
      {
        "ruleId": "default",
        "sourceContainer": "<source-container>",
        "destinationContainer": "<destination-container>",
        "filters": {
          "prefixMatch": [
            "b"
          ],
          "minCreationTime": "2020-08-028T00:00:00Z"
        }
      }
    ]
  }
}
```

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Aby skonfigurować replikację obiektów na koncie docelowym przy użyciu pliku JSON w Azure Portal, wykonaj następujące kroki:

1. Utwórz lokalny plik JSON, który definiuje zasady replikacji na koncie docelowym. W polu **policyId** Ustaw **wartość domyślne** , aby usługa Azure Storage definiowała identyfikator zasad.

    Prostym sposobem utworzenia pliku JSON, który definiuje zasady replikacji, jest utworzenie testowej zasady replikacji między dwoma kontami magazynu w Azure Portal. Następnie można pobrać reguły replikacji i zmodyfikować plik JSON zgodnie z wymaganiami.

1. Przejdź do ustawień **replikacji obiektów** dla konta docelowego w Azure Portal.
1. Wybierz pozycję **Przekaż reguły replikacji**.
1. Przekaż plik JSON. Azure Portal wyświetla zasady i reguły, które zostaną utworzone, jak pokazano na poniższej ilustracji.

    :::image type="content" source="media/object-replication-configure/replication-rules-upload-portal.png" alt-text="Zrzut ekranu przedstawiający sposób przekazywania pliku JSON w celu zdefiniowania zasad replikacji":::

1. Wybierz pozycję **Przekaż** , aby utworzyć zasady replikacji na koncie docelowym.

Następnie można pobrać plik JSON zawierający definicję zasad, którą można podać innemu użytkownikowi, aby skonfigurować konto źródłowe. Aby pobrać ten plik JSON, wykonaj następujące kroki:

1. Przejdź do ustawień **replikacji obiektów** dla konta docelowego w Azure Portal.
1. Wybierz przycisk **więcej** obok zasad, które chcesz pobrać, a następnie wybierz pozycję **Pobierz reguły** , jak pokazano na poniższej ilustracji.

    :::image type="content" source="media/object-replication-configure/replication-rules-download-portal.png" alt-text="Zrzut ekranu przedstawiający sposób pobierania reguł replikacji do pliku JSON":::

1. Zapisz plik JSON na komputerze lokalnym, aby udostępnić go innemu użytkownikowi w celu skonfigurowania zasad na koncie źródłowym.

Pobrany plik JSON zawiera identyfikator zasad utworzony przez usługę Azure Storage dla zasad na koncie docelowym. Aby skonfigurować replikację obiektów na koncie źródłowym, należy użyć tego samego identyfikatora zasad.

Należy pamiętać, że przekazanie pliku JSON w celu utworzenia zasad replikacji dla konta docelowego za pośrednictwem Azure Portal nie powoduje automatycznego tworzenia tych samych zasad na koncie źródłowym. Inny użytkownik musi utworzyć zasady na koncie źródłowym, zanim usługa Azure Storage rozpocznie replikację obiektów.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby pobrać plik JSON, który zawiera definicję zasad replikacji dla konta docelowego z programu PowerShell, wywołaj polecenie [Get-AzStorageObjectReplicationPolicy](/powershell/module/az.storage/get-azstorageobjectreplicationpolicy) w celu zwrócenia zasad. Następnie przekonwertuj zasady na format JSON i Zapisz je jako plik lokalny, jak pokazano w poniższym przykładzie. Pamiętaj, aby zastąpić wartości w nawiasach kątowych i ścieżkę pliku własnymi wartościami:

```powershell
$rgName = "<resource-group>"
$destAccountName = "<destination-storage-account>"

$destPolicy = Get-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName
$destPolicy | ConvertTo-Json -Depth 5 > c:\temp\json.txt
```

Aby użyć pliku JSON w celu skonfigurowania zasad replikacji na koncie źródłowym przy użyciu programu PowerShell, Pobierz plik lokalny i przekonwertuj z formatu JSON na obiekt. Następnie Wywołaj polecenie [Set-AzStorageObjectReplicationPolicy](/powershell/module/az.storage/set-azstorageobjectreplicationpolicy) , aby skonfigurować zasady na koncie źródłowym, jak pokazano w poniższym przykładzie. Pamiętaj, aby zastąpić wartości w nawiasach kątowych i ścieżkę pliku własnymi wartościami:

```powershell
$object = Get-Content -Path C:\temp\json.txt | ConvertFrom-Json
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $object.PolicyId `
    -SourceAccount $object.SourceAccount `
    -DestinationAccount $object.DestinationAccount `
    -Rule $object.Rules
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby zapisać definicję zasad replikacji dla konta docelowego w pliku JSON z interfejsu wiersza polecenia platformy Azure, wywołaj polecenie [AZ Storage account lub-Policy show](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_show) i Output to File.

Poniższy przykład zapisuje definicję zasad do pliku JSON o nazwie *policy.jsna*. Pamiętaj, aby zastąpić wartości w nawiasach kątowych i ścieżkę pliku własnymi wartościami:

```azurecli
az storage account or-policy show \
    --account-name <dest-account-name> \
    --policy-id  <policy-id> > policy.json
```

Aby użyć pliku JSON w celu skonfigurowania zasad replikacji na koncie źródłowym przy użyciu interfejsu wiersza polecenia platformy Azure, wywołaj polecenie [AZ Storage account lub-Policy Create](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_create) , a następnie odwołują się do *policy.js* pliku. Pamiętaj, aby zastąpić wartości w nawiasach kątowych i ścieżkę pliku własnymi wartościami:

```azurecli
az storage account or-policy create \
    -resource-group <resource-group> \
    --source-account <source-account-name> \
    --policy @policy.json
```

---

## <a name="check-the-replication-status-of-a-blob"></a>Sprawdzanie stanu replikacji obiektu BLOB

Stan replikacji obiektu BLOB można sprawdzić na koncie źródłowym przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Właściwości replikacji obiektów nie są wypełniane, dopóki replikacja nie została ukończona lub zakończyła się niepowodzeniem.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Aby sprawdzić stan replikacji obiektu BLOB na koncie źródłowym w Azure Portal, wykonaj następujące czynności:

1. Przejdź do konta źródłowego w Azure Portal.
1. Znajdź kontener zawierający źródłowy obiekt BLOB.
1. Wybierz obiekt BLOB, aby wyświetlić jego właściwości. Jeśli obiekt BLOB został zreplikowany pomyślnie, w sekcji **replikacja obiektów** zostanie wyświetlona wartość *ukończono*. Zostaną również wymienione identyfikatory zasad replikacji i Identyfikator reguły dotyczącej replikacji obiektów dla tego kontenera.

:::image type="content" source="media/object-replication-configure/check-replication-status-source.png" alt-text="Zrzut ekranu przedstawiający stan replikacji obiektu BLOB na koncie źródłowym":::

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby sprawdzić stan replikacji obiektu BLOB na koncie źródłowym przy użyciu programu PowerShell, Pobierz wartość właściwości **ReplicationStatus** replikacji obiektów, jak pokazano w poniższym przykładzie. Pamiętaj, aby zamienić wartości w nawiasy kątowe własnymi wartościami:

```powershell
$ctxSrc = (Get-AzStorageAccount -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName).Context
$blobSrc = Get-AzStorageBlob -Container $srcContainerName1 `
    -Context $ctxSrc `
    -Blob <blob-name>
$blobSrc.BlobProperties.ObjectReplicationSourceProperties[0].Rules[0].ReplicationStatus
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby sprawdzić stan replikacji obiektu BLOB na koncie źródłowym przy użyciu interfejsu wiersza polecenia platformy Azure, Pobierz wartość właściwości **stan** replikacji obiektu, jak pokazano w następującym przykładzie:

```azurecli
az storage blob show \
    --account-name <source-account-name> \
    --container-name <source-container-name> \
    --name <source-blob-name> \
    --query 'objectReplicationSourceProperties[].rules[].status' \
    --output tsv \
    --auth-mode login
```

---

Jeśli stan replikacji obiektu BLOB na koncie źródłowym wskazuje błąd, zbadaj następujące możliwe przyczyny:

- Upewnij się, że na koncie docelowym są skonfigurowane zasady replikacji obiektów.
- Sprawdź, czy kontener docelowy nadal istnieje.
- Jeśli źródłowy obiekt BLOB został zaszyfrowany za pomocą klucza dostarczonego przez klienta w ramach operacji zapisu, replikacja obiektów zakończy się niepowodzeniem. Więcej informacji o kluczach dostarczonych przez klienta znajduje się [w temacie zapewnianie klucza szyfrowania w żądaniu usługi BLOB Storage](encryption-customer-provided-keys.md).

## <a name="remove-a-replication-policy"></a>Usuwanie zasad replikacji

Aby usunąć zasady replikacji i skojarzone z nimi reguły, użyj Azure Portal, PowerShell lub interfejsu wiersza polecenia.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Aby usunąć zasady replikacji w Azure Portal, wykonaj następujące kroki:

1. Przejdź do źródłowego konta magazynu w Azure Portal.
1. W obszarze **Ustawienia** wybierz pozycję **replikacja obiektów**.
1. Kliknij przycisk **więcej** obok nazwy zasad.
1. Wybierz pozycję **Usuń reguły**.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby usunąć zasady replikacji, Usuń zasady z konta źródłowego i docelowego. Usunięcie zasad spowoduje również usunięcie skojarzonych z nią reguł.

```powershell
# Remove the policy from the destination account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId $destPolicy.PolicyId

# Remove the policy from the source account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $destPolicy.PolicyId
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby usunąć zasady replikacji, Usuń zasady z konta źródłowego i docelowego. Usunięcie zasad spowoduje również usunięcie skojarzonych z nią reguł.

```azurecli
az storage account or-policy delete \
    --policy-id <policy-id> \
    --account-name <source-storage-account> \
    --resource-group <resource-group>

az storage account or-policy delete \
    --policy-id <policy-id> \
    --account-name <dest-storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>Następne kroki

- [Omówienie replikacji obiektów](object-replication-overview.md)
- [Włączanie obsługi wersji obiektów blob i zarządzanie nimi](versioning-enable.md)
- [Kanał informacyjny zmiany procesu w usłudze Azure Blob Storage](storage-blob-change-feed-how-to.md)
