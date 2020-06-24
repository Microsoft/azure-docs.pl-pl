---
title: Konfiguruj replikację obiektów (wersja zapoznawcza)
titleSuffix: Azure Storage
description: Dowiedz się, jak skonfigurować replikację obiektów do asynchronicznego kopiowania blokowych obiektów blob z kontenera na jednym koncie magazynu do innego.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 9cb9f1a33c37487f4bfb1419d45d4e42a862d815
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84888115"
---
# <a name="configure-object-replication-for-block-blobs-preview"></a>Konfiguruj replikację obiektów dla blokowych obiektów BLOB (wersja zapoznawcza)

Replikacja obiektów (wersja zapoznawcza) asynchronicznie kopiuje blokowe obiekty blob między źródłowym kontem magazynu a kontem docelowym. Aby uzyskać więcej informacji na temat replikacji obiektów, zobacz [replikacja obiektów (wersja zapoznawcza)](object-replication-overview.md).

Podczas konfigurowania replikacji obiektów należy utworzyć zasady replikacji określające źródłowe konto magazynu i konto docelowe. Zasady replikacji obejmują co najmniej jedną regułę określającą kontener źródłowy i kontener docelowy i wskazujący, które blokowe obiekty blob w kontenerze źródłowym zostaną zreplikowane.

W tym artykule opisano sposób konfigurowania replikacji obiektów dla konta magazynu przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Możesz również użyć jednej z bibliotek klienta dostawcy zasobów usługi Azure Storage, aby skonfigurować replikację obiektów.

## <a name="create-a-replication-policy-and-rules"></a>Tworzenie zasad i reguł replikacji

Przed skonfigurowaniem replikacji obiektów Utwórz źródłową i docelową konto magazynu, jeśli jeszcze nie istnieją. Oba konta muszą być kontami magazynu ogólnego przeznaczenia w wersji 2. Aby uzyskać więcej informacji, zobacz [Tworzenie konta usługi Azure Storage](../common/storage-account-create.md).

Konto magazynu może stanowić konto źródłowe dla maksymalnie dwóch kont docelowych. A konto docelowe może zawierać nie więcej niż dwa konta źródłowe. Konta źródłowe i docelowe mogą znajdować się w różnych regionach. W celu replikowania danych do każdego z kont docelowych można skonfigurować oddzielne zasady replikacji.

Przed rozpoczęciem upewnij się, że zarejestrowano dla następujących wersji zapoznawczych funkcji:

- [Replikacja obiektów (wersja zapoznawcza)](object-replication-overview.md)
- [Przechowywanie wersji obiektów BLOB (wersja zapoznawcza)](versioning-overview.md)
- [Obsługa kanału informacyjnego zmiany w usłudze Azure Blob Storage (wersja zapoznawcza)](storage-blob-change-feed.md)

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Przed skonfigurowaniem replikacji obiektów w Azure Portal należy utworzyć kontenery źródłowe i docelowe na odpowiednich kontach magazynu, jeśli jeszcze nie istnieją. Ponadto włączono obsługę wersji obiektów blob i źródło zmian na koncie źródłowym i Włącz obsługę wersji obiektów BLOB na koncie docelowym.

Aby utworzyć zasady replikacji w Azure Portal, wykonaj następujące kroki:

1. Przejdź do źródłowego konta magazynu w Azure Portal.
1. W obszarze **BLOB Service**wybierz pozycję **replikacja obiektów**.
1. Wybierz pozycję **Konfiguruj replikację**.
1. Wybierz subskrypcję docelową i konto magazynu.
1. W sekcji **pary kontenerów** wybierz kontener źródłowy z konta źródłowego i kontener docelowy z konta docelowego. Można utworzyć maksymalnie 10 par kontenerów dla każdej zasady replikacji.

    Na poniższej ilustracji przedstawiono zestaw reguł replikacji.

    :::image type="content" source="media/object-replication-configure/configure-replication-policy.png" alt-text="Zrzut ekranu przedstawiający reguły replikacji w Azure Portal":::

1. W razie potrzeby określ co najmniej jeden filtr, aby skopiować tylko obiekty blob, które pasują do wzorca prefiksu. Na przykład, jeśli określisz prefiks `b` , replikowane są tylko obiekty blob, których nazwa rozpoczyna się od tej litery. Jako część prefiksu można określić katalog wirtualny.

    Na poniższej ilustracji przedstawiono filtry, które ograniczają, które obiekty blob są kopiowane w ramach reguły replikacji.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-prefix.png" alt-text="Zrzut ekranu przedstawiający filtry dla reguły replikacji":::

1. Domyślnie zakres kopiowania jest ustawiony na kopiowanie tylko nowych obiektów. Aby skopiować wszystkie obiekty w kontenerze lub skopiować obiekty rozpoczynając od niestandardowej daty i godziny, wybierz łącze **Zmień** i skonfiguruj zakres kopiowania dla pary kontenerów.

    Na poniższej ilustracji przedstawiono niestandardowy zakres kopiowania.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-scope.png" alt-text="Zrzut ekranu przedstawiający niestandardowy zakres kopiowania dla replikacji obiektów":::

1. Wybierz pozycję **Zapisz i Zastosuj** , aby utworzyć zasady replikacji i rozpocząć replikowanie danych.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Aby utworzyć zasady replikacji przy użyciu programu PowerShell, najpierw Zainstaluj wersję [2.0.1 — wersja zapoznawcza](https://www.powershellgallery.com/packages/Az.Storage/2.0.1-preview) modułu programu PowerShell AZ. Storage. Wykonaj następujące kroki, aby zainstalować moduł w wersji zapoznawczej:

1. Odinstaluj wszystkie poprzednie instalacje Azure PowerShell z systemu Windows za pomocą ustawienia **aplikacje & funkcje** w obszarze **Ustawienia**.

1. Upewnij się, że masz zainstalowaną najnowszą wersję programu PowerShellGet. Otwórz okno programu Windows PowerShell i uruchom następujące polecenie, aby zainstalować najnowszą wersję:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

    Zamknij i ponownie otwórz okno programu PowerShell po zainstalowaniu PowerShellGet.

1. Zainstaluj najnowszą wersję Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Zainstaluj moduł AZ. Storage Preview:

    ```powershell
    Install-Module Az.Storage -Repository PSGallery -RequiredVersion 2.0.1-preview -AllowPrerelease -AllowClobber -Force
    ```

Aby uzyskać więcej informacji o instalowaniu Azure PowerShell, zobacz [Install Azure PowerShell with PowerShellGet](/powershell/azure/install-az-ps).

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

Aby utworzyć zasady replikacji przy użyciu interfejsu wiersza polecenia platformy Azure, najpierw zainstaluj rozszerzenie wersji zapoznawczej dla usługi Azure Storage.:

```azurecli
az extension add -n storage-or-preview
```

Następnie zaloguj się przy użyciu poświadczeń platformy Azure:

```azurecli
az login
```

Włącz obsługę wersji obiektów BLOB na kontach magazynu źródłowego i docelowego, a następnie Włącz źródło zmian na koncie źródłowym. Pamiętaj, aby zamienić wartości w nawiasy kątowe własnymi wartościami:

```azurecli
az storage blob service-properties update --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-versioning

az storage blob service-properties update --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-change-feed

az storage blob service-properties update --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --enable-versioning
```

Utwórz kontenery źródłowe i docelowe na odpowiednich kontach magazynu.

```azurecli
az storage container create --account-name <source-storage-account> --name source-container3 --auth-mode login
az storage container create --account-name <source-storage-account> --name source-container4 --auth-mode login

az storage container create --account-name <dest-storage-account> --name source-container3 --auth-mode login
az storage container create --account-name <dest-storage-account> --name source-container4 --auth-mode login
```

Utwórz nowe zasady replikacji i skojarzone reguły na koncie docelowym.

```azurecli
az storage account or-policy create --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-account <source-storage-account> \
    --destination-account <dest-storage-account> \
    --source-container source-container3 \
    --destination-container dest-container3 \
    --min-creation-time '2020-05-10T00:00:00Z' \
    --prefix-match a

az storage account or-policy rule add --account-name <dest-storage-account> \
    --destination-container dest-container4 \
    --policy-id <policy-id> \
    --resource-group <resource-group> \
    --source-container source-container4 \
    --prefix-match b
```

Utwórz zasady na koncie źródłowym przy użyciu identyfikatora zasad.

```azurecli
az storage account or-policy show --resource-group <resource-group> \
    --name <dest-storage-account> \
    --policy-id <policy-id> |
    --az storage account or-policy create --resource-group <resource-group> \
    --name <source-storage-account> \
    --policy "@-"
```

---

## <a name="remove-a-replication-policy"></a>Usuwanie zasad replikacji

Aby usunąć zasady replikacji i skojarzone z nimi reguły, użyj Azure Portal, PowerShell lub interfejsu wiersza polecenia.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Aby usunąć zasady replikacji w Azure Portal, wykonaj następujące kroki:

1. Przejdź do źródłowego konta magazynu w Azure Portal.
1. W obszarze **Ustawienia**wybierz pozycję **replikacja obiektów**.
1. Kliknij przycisk **więcej** obok nazwy zasad.
1. Wybierz pozycję **Usuń reguły**.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

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
    --policy-id $policyid \
    --account-name <source-storage-account> \
    --resource-group <resource-group>

az storage account or-policy delete \
    --policy-id $policyid \
    --account-name <dest-storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>Następne kroki

- [Omówienie replikacji obiektów (wersja zapoznawcza)](object-replication-overview.md)
