---
title: Konfigurowanie anonimowego publicznego dostępu do odczytu dla kontenerów i obiektów blob
titleSuffix: Azure Storage
description: Dowiedz się, jak zezwolić na anonimowy dostęp do danych obiektów blob dla konta magazynu lub go nie zezwalać. Ustaw ustawienie dostępu publicznego kontenera, aby udostępnić kontenery i obiekty blob na dostęp anonimowy.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/03/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: 31812a7b2dddad474ab5cd422a15f6e5368dba5c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774633"
---
# <a name="configure-anonymous-public-read-access-for-containers-and-blobs"></a>Konfigurowanie anonimowego publicznego dostępu do odczytu dla kontenerów i obiektów blob

Usługa Azure Storage obsługuje opcjonalny anonimowy publiczny dostęp do odczytu dla kontenerów i obiektów blob. Domyślnie anonimowy dostęp do danych nigdy nie jest dozwolony. O ile jawnie nie włączysz dostępu anonimowego, wszystkie żądania do kontenera i jego obiektów blob muszą być autoryzowane. Po skonfigurowaniu ustawienia poziomu dostępu publicznego kontenera w celu zezwolenia na dostęp anonimowy klienci mogą odczytywać dane w tym kontenerze bez autoryzowania żądania.

> [!WARNING]
> Gdy kontener jest skonfigurowany do dostępu publicznego, każdy klient może odczytywać dane w tym kontenerze. Dostęp publiczny stanowi potencjalne zagrożenie bezpieczeństwa, dlatego jeśli scenariusz go nie wymaga, firma Microsoft zaleca, aby nie zezwalać na korzystanie z niego na koncie magazynu. Aby uzyskać więcej informacji, zobacz [Zapobieganie anonimowemu publicznemu dostępowi do odczytu do kontenerów i obiektów blob.](anonymous-read-access-prevent.md)

W tym artykule opisano sposób konfigurowania anonimowego publicznego dostępu do odczytu dla kontenera i jego obiektów blob. Aby uzyskać informacje o tym, jak anonimowo uzyskać dostęp do danych obiektów blob z aplikacji klienckiej, zobacz Anonimowy dostęp do publicznych kontenerów i obiektów [blob za pomocą platformy .NET](anonymous-read-access-client.md).

## <a name="about-anonymous-public-read-access"></a>Informacje o anonimowym publicznym dostępie do odczytu

Publiczny dostęp do danych jest zawsze domyślnie zabroniony. Istnieją dwa oddzielne ustawienia, które mają wpływ na dostęp publiczny:

1. **Zezwalaj na dostęp publiczny dla konta magazynu.** Domyślnie konto magazynu umożliwia użytkownikowi z odpowiednimi uprawnieniami, aby umożliwić publiczny dostęp do kontenera. Dane obiektów blob nie są dostępne dla dostępu publicznego, chyba że użytkownik przejmie dodatkowy krok w celu jawnego skonfigurowania ustawienia dostępu publicznego kontenera.
1. **Skonfiguruj ustawienie dostępu publicznego kontenera.** Domyślnie ustawienie dostępu publicznego kontenera jest wyłączone, co oznacza, że autoryzacja jest wymagana dla każdego żądania do kontenera lub jego danych. Użytkownik z odpowiednimi uprawnieniami może zmodyfikować ustawienie dostępu publicznego kontenera, aby umożliwić dostęp anonimowy tylko wtedy, gdy dla konta magazynu jest dozwolony dostęp anonimowy.

W poniższej tabeli przedstawiono sposób, w jaki oba ustawienia razem wpływają na publiczny dostęp do kontenera.

| Ustawienie dostępu publicznego | Dostęp publiczny dla kontenera jest wyłączony (ustawienie domyślne) | Dla publicznego dostępu do kontenera ustawiono wartość Kontener | Dostęp publiczny do kontenera jest ustawiony na wartość Obiekt blob |
|--|--|--|--|
| Dostęp publiczny do konta magazynu jest niedozwolone | Brak publicznego dostępu do żadnego kontenera na koncie magazynu. | Brak publicznego dostępu do żadnego kontenera na koncie magazynu. Ustawienie konta magazynu zastępuje ustawienie kontenera. | Brak publicznego dostępu do żadnego kontenera na koncie magazynu. Ustawienie konta magazynu zastępuje ustawienie kontenera. |
| Dostęp publiczny jest dozwolony dla konta magazynu (ustawienie domyślne) | Brak publicznego dostępu do tego kontenera (konfiguracja domyślna). | Dostęp publiczny jest dozwolony do tego kontenera i jego obiektów blob. | Dostęp publiczny jest dozwolony do obiektów blob w tym kontenerze, ale nie do samego kontenera. |

## <a name="allow-or-disallow-public-read-access-for-a-storage-account"></a>Zezwalanie lub nie zezwalanie na publiczny dostęp do odczytu dla konta magazynu

Domyślnie konto magazynu jest skonfigurowane tak, aby zezwalać użytkownikowi z odpowiednimi uprawnieniami umożliwiającymi publiczny dostęp do kontenera. Gdy dostęp publiczny jest dozwolony, użytkownik z odpowiednimi uprawnieniami może zmodyfikować ustawienie dostępu publicznego kontenera, aby umożliwić anonimowy publiczny dostęp do danych w tym kontenerze. Dane obiektów blob nigdy nie są dostępne dla dostępu publicznego, chyba że użytkownik przejmie dodatkowy krok w celu jawnego skonfigurowania ustawienia dostępu publicznego kontenera.

Należy pamiętać, że publiczny dostęp do kontenera jest zawsze domyślnie wyłączony i musi być jawnie skonfigurowany do zezwalania na żądania anonimowe. Niezależnie od ustawienia na koncie magazynu dane nigdy nie będą dostępne dla publicznego dostępu, chyba że użytkownik z odpowiednimi uprawnieniami przejmie ten dodatkowy krok, aby włączyć dostęp publiczny do kontenera.

Uniemożliwienie dostępu publicznego dla konta magazynu uniemożliwia anonimowy dostęp do wszystkich kontenerów i obiektów blob na tym koncie. W przypadku niedozwolonego dostępu publicznego dla konta nie można skonfigurować ustawienia dostępu publicznego dla kontenera, aby zezwolić na dostęp anonimowy. W celu poprawy zabezpieczeń firma Microsoft zaleca, aby nie zezwalać na publiczny dostęp do kont magazynu, chyba że scenariusz wymaga anonimowego dostępu użytkowników do zasobów obiektów blob.

> [!IMPORTANT]
> Nie zezwalanie na publiczny dostęp do konta magazynu zastępuje ustawienia dostępu publicznego dla wszystkich kontenerów na tym koncie magazynu. W przypadku niedozwolonego dostępu publicznego do konta magazynu wszystkie przyszłe anonimowe żądania do tego konta nie będą mieć miejsca. Przed zmianą tego ustawienia należy zrozumieć wpływ na aplikacje klienckie, które mogą anonimowo uzyskać dostęp do danych na koncie magazynu. Aby uzyskać więcej informacji, zobacz [Zapobieganie anonimowemu publicznemu dostępowi do odczytu do kontenerów i obiektów blob.](anonymous-read-access-prevent.md)

Aby zezwolić na dostęp publiczny dla konta magazynu lub go uniemożliwić, skonfiguruj właściwość **AllowBlobPublicAccess** konta. Ta właściwość jest dostępna dla wszystkich kont magazynu utworzonych za pomocą Azure Resource Manager modelu wdrażania. Aby uzyskać więcej informacji, zobacz [Omówienie konta magazynu.](../common/storage-account-overview.md)

Właściwość **AllowBlobPublicAccess** nie jest domyślnie ustawiona dla konta magazynu i nie zwraca wartości, dopóki nie zostanie jawnie ustawiona. Konto magazynu zezwala na dostęp publiczny, gdy wartość właściwości ma wartość **null** lub **true.**

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Aby zezwolić na dostęp publiczny do konta magazynu w chmurze lub go Azure Portal, wykonaj następujące kroki:

1. W witrynie Azure Portal przejdź do swojego konta magazynu.
1. Znajdź ustawienie **Konfiguracja** w obszarze **Ustawienia**.
1. Ustaw **publiczny dostęp do obiektu blob** na wartość **Włączone** lub **Wyłączone.**

    :::image type="content" source="media/anonymous-read-access-configure/blob-public-access-portal.png" alt-text="Zrzut ekranu przedstawiający sposób zezwalania lub nie zezwalania na publiczny dostęp do obiektów blob dla konta":::

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby zezwolić na publiczny dostęp do konta magazynu lub go nie zezwalać za pomocą programu PowerShell, Azure PowerShell w wersji [4.4.0](https://www.powershellgallery.com/packages/Az/4.4.0) lub nowszej. Następnie skonfiguruj właściwość **AllowBlobPublicAccess** dla nowego lub istniejącego konta magazynu.

Poniższy przykład tworzy konto magazynu i jawnie ustawia właściwość **AllowBlobPublicAccess** na **wartość true.** Następnie aktualizuje konto magazynu, aby ustawić właściwość **AllowBlobPublicAccess** na **wartość false**. Przykład pobiera również wartość właściwości w każdym przypadku. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$location = "<location>"

# Create a storage account with AllowBlobPublicAccess set to true (or null).
New-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -Location $location `
    -SkuName Standard_GRS
    -AllowBlobPublicAccess $false

# Read the AllowBlobPublicAccess property for the newly created storage account.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).AllowBlobPublicAccess

# Set AllowBlobPublicAccess set to false
Set-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -AllowBlobPublicAccess $false

# Read the AllowBlobPublicAccess property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).AllowBlobPublicAccess
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby zezwolić na publiczny dostęp do konta magazynu za pomocą interfejsu wiersza polecenia platformy Azure lub go nie zezwalać, zainstaluj interfejs wiersza polecenia platformy Azure w wersji 2.9.0 lub nowszej. Aby uzyskać więcej informacji, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli) Następnie skonfiguruj właściwość **allowBlobPublicAccess** dla nowego lub istniejącego konta magazynu.

Poniższy przykład tworzy konto magazynu i jawnie ustawia właściwość **allowBlobPublicAccess** na **wartość true.** Następnie aktualizuje konto magazynu, aby ustawić właściwość **allowBlobPublicAccess** na **wartość false**. Przykład pobiera również wartość właściwości w każdym przypadku. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --kind StorageV2 \
    --location <location> \
    --allow-blob-public-access true

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query allowBlobPublicAccess \
    --output tsv

az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --allow-blob-public-access false

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query allowBlobPublicAccess \
    --output tsv
```

# <a name="template"></a>[Szablon](#tab/template)

Aby zezwolić na publiczny dostęp do konta magazynu przy użyciu szablonu lub go nie zezwalać, utwórz szablon z właściwością **AllowBlobPublicAccess** ustawioną na **wartość true** lub **false.** W poniższych krokach opisano sposób tworzenia szablonu w Azure Portal.

1. W Azure Portal wybierz pozycję **Utwórz zasób.**
1. W **polu Wyszukaj w witrynie Marketplace** wpisz wdrożenie **szablonu**, a następnie naciśnij **klawisz ENTER.**
1. Wybierz Template deployment (wdrażanie przy użyciu szablonów **niestandardowych) (wersja zapoznawcza),** wybierz pozycję **Utwórz,** a następnie wybierz pozycję Build your own template in the editor (Utwórz własny szablon **w edytorze).**
1. W edytorze szablonów wklej następujący kod JSON, aby utworzyć nowe konto, i ustaw właściwość **AllowBlobPublicAccess** na **wartość true** lub **false.** Pamiętaj, aby zastąpić symbole zastępcze w nawiasach kątowych własnymi wartościami.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {
            "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'template')]"
        },
        "resources": [
            {
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "location": "<location>",
            "properties": {
                "allowBlobPublicAccess": false
            },
            "dependsOn": [],
            "sku": {
              "name": "Standard_GRS"
            },
            "kind": "StorageV2",
            "tags": {}
            }
        ]
    }
    ```

1. Zapisz szablon.
1. Określ parametr grupy zasobów, a następnie wybierz przycisk Przejrzyj **i** utwórz, aby wdrożyć szablon i utworzyć konto magazynu ze skonfigurowaną **właściwością allowBlobPublicAccess.**

---

> [!NOTE]
> Nie zezwalanie na publiczny dostęp do konta magazynu nie ma wpływu na statyczne witryny internetowe hostowane na tym koncie magazynu. Kontener **$web** jest zawsze dostępny publicznie.
>
> Po zaktualizowaniu ustawienia dostępu publicznego dla konta magazynu może upłynieć do 30 sekund, zanim zmiana zostanie w pełni rozpropagowana.

Zezwalanie lub nie zezwalanie na publiczny dostęp do obiektów blob wymaga dostawcy zasobów usługi Azure Storage w wersji 2019-04-01 lub nowszej. Aby uzyskać więcej informacji, zobacz [Interfejs API REST dostawcy zasobów usługi Azure Storage.](/rest/api/storagerp/)

W przykładach w tej sekcji przedstawiono sposób odczytywania właściwości **AllowBlobPublicAccess** dla konta magazynu w celu określenia, czy dostęp publiczny jest obecnie dozwolony, czy niedozwolone. Aby dowiedzieć się więcej o tym, jak sprawdzić, czy ustawienie dostępu publicznego konta jest skonfigurowane w celu zapobiegania dostępowi anonimowemu, zobacz [Korygowanie anonimowego dostępu publicznego.](anonymous-read-access-prevent.md#remediate-anonymous-public-access)

## <a name="set-the-public-access-level-for-a-container"></a>Ustawianie poziomu dostępu publicznego dla kontenera

Aby udzielić użytkownikom anonimowym dostępu do odczytu do kontenera i jego obiektów blob, najpierw zezwonij na dostęp publiczny dla konta magazynu, a następnie ustaw poziom dostępu publicznego kontenera. W przypadku odmowy dostępu publicznego dla konta magazynu nie będzie można skonfigurować dostępu publicznego dla kontenera.

Jeśli dostęp publiczny jest dozwolony dla konta magazynu, możesz skonfigurować kontener z następującymi uprawnieniami:

- **Brak publicznego dostępu do odczytu:** Dostęp do kontenera i jego obiektów blob można uzyskać tylko za pomocą autoryzowanego żądania. Ta opcja jest wartością domyślną dla wszystkich nowych kontenerów.
- **Publiczny dostęp do odczytu tylko dla obiektów blob:** Obiekty blob w kontenerze mogą być odczytywane przez żądanie anonimowe, ale dane kontenera nie są dostępne anonimowo. Klienci anonimowi nie mogą wyliczać obiektów blob w kontenerze.
- **Publiczny dostęp do odczytu dla kontenera i jego obiektów blob:** Dane kontenera i obiektu blob mogą być odczytywane przez żądanie anonimowe, z wyjątkiem ustawień uprawnień kontenera i metadanych kontenera. Klienci mogą wyliczać obiekty blob w kontenerze przez żądanie anonimowe, ale nie mogą wyliczać kontenerów w ramach konta magazynu.

Nie można zmienić poziomu dostępu publicznego dla pojedynczego obiektu blob. Poziom dostępu publicznego jest ustawiany tylko na poziomie kontenera. Poziom dostępu publicznego kontenera można ustawić podczas tworzenia kontenera lub można zaktualizować ustawienie w istniejącym kontenerze.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Aby zaktualizować poziom dostępu publicznego dla co najmniej jednego istniejącego kontenera w Azure Portal, wykonaj następujące kroki:

1. Przejdź do przeglądu konta magazynu w Azure Portal.
1. W **Blob service** menu wybierz pozycję **Kontenery**.
1. Wybierz kontenery, dla których chcesz ustawić poziom dostępu publicznego.
1. Użyj **przycisku Zmień poziom dostępu,** aby wyświetlić ustawienia dostępu publicznego.
1. Wybierz żądany poziom dostępu publicznego z listy rozwijanej **Poziom** dostępu publicznego i kliknij przycisk OK, aby zastosować zmianę do wybranych kontenerów.

    ![Zrzut ekranu przedstawiający sposób ustawienia poziomu dostępu publicznego w portalu](./media/anonymous-read-access-configure/configure-public-access-container.png)

W przypadku niedozwolonego dostępu publicznego do konta magazynu nie można ustawić poziomu dostępu publicznego kontenera. Jeśli spróbujesz ustawić poziom dostępu publicznego kontenera, zobaczysz, że to ustawienie jest wyłączone, ponieważ dostęp publiczny jest niedozwolone dla konta.

:::image type="content" source="media/anonymous-read-access-configure/container-public-access-blocked.png" alt-text="Zrzut ekranu pokazujący, że ustawienie poziomu dostępu publicznego kontenera jest blokowane w przypadku niedozwolonego dostępu publicznego":::

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby zaktualizować poziom dostępu publicznego dla co najmniej jednego kontenera przy użyciu programu PowerShell, wywołaj polecenie [Set-AzStorageContainerAcl.](/powershell/module/az.storage/set-azstoragecontaineracl) Autoryzuj tę operację, przekazując klucz konta, ciąg połączenia lub sygnaturę dostępu współdzielonego (SAS). Operacja [ustawienia listy ACL kontenera,](/rest/api/storageservices/set-container-acl) która ustawia poziom dostępu publicznego kontenera, nie obsługuje autoryzacji w usłudze Azure AD. Aby uzyskać więcej informacji, zobacz Permissions for calling blob and queue data operations (Uprawnienia [do wywoływania operacji na danych obiektów blob i kolejek).](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)

Poniższy przykład tworzy kontener z wyłączonym dostępem publicznym, a następnie aktualizuje ustawienie dostępu publicznego kontenera, aby zezwolić na anonimowy dostęp do kontenera i jego obiektów blob. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```powershell
# Set variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Get context object.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

# Create a new container with public access setting set to Off.
$containerName = "<container>"
New-AzStorageContainer -Name $containerName -Permission Off -Context $ctx

# Read the container's public access setting.
Get-AzStorageContainerAcl -Container $containerName -Context $ctx

# Update the container's public access setting to Container.
Set-AzStorageContainerAcl -Container $containerName -Permission Container -Context $ctx

# Read the container's public access setting.
Get-AzStorageContainerAcl -Container $containerName -Context $ctx
```

W przypadku niedozwolonego dostępu publicznego do konta magazynu nie można ustawić poziomu dostępu publicznego kontenera. Jeśli spróbujemy ustawić poziom dostępu publicznego do kontenera, usługa Azure Storage zwróci błąd wskazujący, że dostęp publiczny na koncie magazynu jest niedozwolony.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby zaktualizować poziom dostępu publicznego dla co najmniej jednego kontenera za pomocą interfejsu wiersza polecenia platformy Azure, wywołaj [polecenie az storage container set permission.](/cli/azure/storage/container#az_storage_container_set_permission) Autoryzuj tę operację, przekazując klucz konta, ciąg połączenia lub sygnaturę dostępu współdzielonego (SAS). Operacja [ustawienia listy ACL kontenera,](/rest/api/storageservices/set-container-acl) która ustawia poziom dostępu publicznego kontenera, nie obsługuje autoryzacji w usłudze Azure AD. Aby uzyskać więcej informacji, zobacz Permissions for calling blob and queue data operations (Uprawnienia [do wywoływania operacji na danych obiektów blob i kolejek).](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)

Poniższy przykład tworzy kontener z wyłączonym dostępem publicznym, a następnie aktualizuje ustawienie dostępu publicznego kontenera, aby zezwolić na anonimowy dostęp do kontenera i jego obiektów blob. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```azurecli-interactive
az storage container create \
    --name <container-name> \
    --account-name <account-name> \
    --resource-group <resource-group>
    --public-access off \
    --account-key <account-key> \
    --auth-mode key

az storage container show-permission \
    --name <container-name> \
    --account-name <account-name> \
    --account-key <account-key> \
    --auth-mode key

az storage container set-permission \
    --name <container-name> \
    --account-name <account-name> \
    --public-access container \
    --account-key <account-key> \
    --auth-mode key

az storage container show-permission \
    --name <container-name> \
    --account-name <account-name> \
    --account-key <account-key> \
    --auth-mode key
```

W przypadku niedozwolonego dostępu publicznego do konta magazynu nie można ustawić poziomu dostępu publicznego kontenera. Jeśli spróbujemy ustawić poziom dostępu publicznego do kontenera, usługa Azure Storage zwróci błąd wskazujący, że dostęp publiczny na koncie magazynu jest niedozwolony.

# <a name="template"></a>[Szablon](#tab/template)

Nie dotyczy.

---

## <a name="check-the-public-access-setting-for-a-set-of-containers"></a>Sprawdzanie ustawienia dostępu publicznego dla zestawu kontenerów

Istnieje możliwość sprawdzenia, które kontenery na co najmniej jednym koncie magazynu są skonfigurowane do dostępu publicznego, poprzez wyświetlanie listy kontenerów i sprawdzanie ustawienia dostępu publicznego. Takie podejście jest praktyczną opcją, gdy konto magazynu nie zawiera dużej liczby kontenerów lub gdy sprawdzasz ustawienie na niewielkiej liczbie kont magazynu. Jednak w przypadku próby wyliczenia dużej liczby kontenerów wydajność może się różnić.

W poniższym przykładzie użyto programu PowerShell, aby uzyskać ustawienie dostępu publicznego dla wszystkich kontenerów na koncie magazynu. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Get-AzStorageContainer -Context $ctx | Select Name, PublicAccess
```

## <a name="next-steps"></a>Następne kroki

- [Zapobieganie anonimowemu publicznemu dostępowi do odczytu do kontenerów i obiektów blob](anonymous-read-access-prevent.md)
- [Anonimowy dostęp do publicznych kontenerów i obiektów blob za pomocą platformy .NET](anonymous-read-access-client.md)
- [Autoryzowanie dostępu do usługi Azure Storage](../common/storage-auth.md)
