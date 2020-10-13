---
title: Konfigurowanie anonimowego publicznego dostępu do odczytu dla kontenerów i obiektów BLOB
titleSuffix: Azure Storage
description: Dowiedz się, jak zezwolić na dostęp anonimowy do danych obiektów BLOB dla konta magazynu lub go zabronić. Ustaw wartość ustawienia dostęp publiczny kontenera, aby kontenery i obiekty blob były dostępne dla dostępu anonimowego.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/09/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: 7248dff25af4693f7f264c8cbf42236612dddda0
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91931074"
---
# <a name="configure-anonymous-public-read-access-for-containers-and-blobs"></a>Konfigurowanie anonimowego publicznego dostępu do odczytu dla kontenerów i obiektów BLOB

Usługa Azure Storage obsługuje opcjonalny anonimowy dostęp do odczytu dla kontenerów i obiektów BLOB. Domyślnie dostęp anonimowy do danych jest nigdy niedozwolony. O ile jawnie nie włączysz dostępu anonimowego, wszystkie żądania do kontenera i jego obiektów BLOB muszą być autoryzowane. W przypadku skonfigurowania ustawienia publicznego poziomu dostępu kontenera w celu zezwolenia na dostęp anonimowy klienci mogą odczytywać dane w tym kontenerze bez autoryzowania żądania.

> [!WARNING]
> Gdy kontener jest skonfigurowany pod kątem dostępu publicznego, każdy klient może odczytywać dane w tym kontenerze. Dostęp publiczny stwarza potencjalne zagrożenie bezpieczeństwa, więc jeśli twój scenariusz nie jest wymagany, firma Microsoft zaleca, aby nie zezwalać na konto magazynu. Aby uzyskać więcej informacji, zobacz [zapobieganie Anonimowemu dostępowi do odczytu do kontenerów i obiektów BLOB](anonymous-read-access-prevent.md).

W tym artykule opisano sposób konfigurowania anonimowego publicznego dostępu do odczytu dla kontenera i jego obiektów BLOB. Aby uzyskać informacje na temat uzyskiwania anonimowego dostępu do danych obiektów blob z aplikacji klienckiej, zobacz [dostęp do publicznych kontenerów i obiektów BLOB anonimowo za pomocą platformy .NET](anonymous-read-access-client.md).

## <a name="about-anonymous-public-read-access"></a>Anonimowy publiczny dostęp do odczytu — informacje

Publiczny dostęp do danych jest zawsze zabroniony domyślnie. Istnieją dwa oddzielne ustawienia, które mają wpływ na dostęp publiczny:

1. **Zezwalaj na dostęp publiczny do konta magazynu.** Domyślnie konto magazynu zezwala użytkownikowi z odpowiednimi uprawnieniami do umożliwienia publicznego dostępu do kontenera. Dane obiektów BLOB nie są dostępne do publicznego dostępu, chyba że użytkownik wykona dodatkowy krok, aby jawnie skonfigurować ustawienie dostępu publicznego kontenera.
1. **Skonfiguruj ustawienie dostępu publicznego kontenera.** Domyślnie ustawienie dostępu publicznego kontenera jest wyłączone, co oznacza, że autoryzacja jest wymagana dla każdego żądania do kontenera lub jego danych. Użytkownik z odpowiednimi uprawnieniami może zmodyfikować ustawienie dostępu publicznego kontenera, aby włączyć dostęp anonimowy tylko wtedy, gdy dostęp anonimowy jest dozwolony dla konta magazynu.

W poniższej tabeli podsumowano, jak oba ustawienia mają wpływ na publiczny dostęp do kontenera.

| Ustawienie dostępu publicznego | Dostęp publiczny jest wyłączony dla kontenera (ustawienie domyślne) | Dostęp publiczny do kontenera jest ustawiony na kontener | Dostęp publiczny kontener jest ustawiony na obiekt BLOB |
|--|--|--|--|
| Dostęp publiczny jest niedozwolony dla konta magazynu | Brak publicznego dostępu do żadnego kontenera na koncie magazynu. | Brak publicznego dostępu do żadnego kontenera na koncie magazynu. Ustawienie konta magazynu zastępuje ustawienie kontenera. | Brak publicznego dostępu do żadnego kontenera na koncie magazynu. Ustawienie konta magazynu zastępuje ustawienie kontenera. |
| Dostęp publiczny jest dozwolony dla konta magazynu (ustawienie domyślne) | Brak publicznego dostępu do tego kontenera (Konfiguracja domyślna). | Dostęp publiczny jest dozwolony dla tego kontenera i jego obiektów BLOB. | Dostęp publiczny jest dozwolony dla obiektów BLOB w tym kontenerze, ale nie do samego kontenera. |

## <a name="allow-or-disallow-public-read-access-for-a-storage-account"></a>Zezwalaj lub nie Zezwalaj na publiczny dostęp do odczytu dla konta magazynu

Domyślnie konto magazynu jest skonfigurowane tak, aby zezwalać użytkownikowi z odpowiednimi uprawnieniami do włączania publicznego dostępu do kontenera. Jeśli dostęp publiczny jest dozwolony, użytkownik z odpowiednimi uprawnieniami może zmodyfikować ustawienie dostępu publicznego kontenera, aby umożliwić anonimowy dostęp publiczny do danych w tym kontenerze. Dane obiektów BLOB nigdy nie są dostępne do publicznego dostępu, chyba że użytkownik wykona dodatkowy krok, aby jawnie skonfigurować ustawienie dostępu publicznego kontenera.

Należy pamiętać, że publiczny dostęp do kontenera jest zawsze domyślnie wyłączony i musi być jawnie skonfigurowany do zezwalania na żądania anonimowe. Niezależnie od ustawienia na koncie magazynu Twoje dane nigdy nie będą dostępne do publicznego dostępu, chyba że użytkownik z odpowiednimi uprawnieniami podejmie ten dodatkowy krok w celu włączenia publicznego dostępu do kontenera.

Niezezwalanie na dostęp publiczny do konta magazynu uniemożliwia anonimowy dostęp do wszystkich kontenerów i obiektów BLOB w ramach tego konta. Jeśli dostęp publiczny jest niedozwolony dla konta, nie można skonfigurować ustawienia dostępu publicznego dla kontenera w celu zezwolenia na dostęp anonimowy. W celu zwiększenia bezpieczeństwa firma Microsoft zaleca, aby nie zezwalać na dostęp publiczny do kont magazynu, chyba że scenariusz wymaga, aby użytkownicy mieli anonimowe dostęp do zasobów obiektów BLOB.

> [!IMPORTANT]
> Niezezwalanie na dostęp publiczny do konta magazynu zastępuje ustawienia dostępu publicznego dla wszystkich kontenerów na tym koncie magazynu. Gdy dostęp publiczny jest niedozwolony dla konta magazynu, wszelkie przyszłe żądania anonimowe do tego konta będą kończyć się niepowodzeniem. Przed zmianą tego ustawienia należy zapoznać się z tematem wpływ na aplikacje klienckie, które mogą anonimowo uzyskiwać dostęp do danych na koncie magazynu. Aby uzyskać więcej informacji, zobacz [zapobieganie Anonimowemu dostępowi do odczytu do kontenerów i obiektów BLOB](anonymous-read-access-prevent.md).

Aby zezwolić na dostęp publiczny do konta magazynu lub go uniemożliwić, skonfiguruj Właściwość **AllowBlobPublicAccess** konta. Ta właściwość jest dostępna dla wszystkich kont magazynu utworzonych za pomocą modelu wdrażania Azure Resource Manager. Aby uzyskać więcej informacji, zobacz temat [konto magazynu — Omówienie](../common/storage-account-overview.md).

> [!NOTE]
> Właściwość **AllowBlobPublicAccess** nie jest domyślnie ustawiona i nie zwraca wartości, dopóki nie zostanie jawnie ustawiona. Konto magazynu zezwala na dostęp publiczny, gdy wartość właściwości jest **równa null** lub jeśli jest **prawdziwa**.
>
> Właściwość **AllowBlobPublicAccess** jest dostępna dla wszystkich kont magazynu w chmurze publicznej platformy Azure i w chmurach Azure Government.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Aby zezwolić na dostęp publiczny do konta magazynu w Azure Portal lub go uniemożliwić, wykonaj następujące czynności:

1. W witrynie Azure Portal przejdź do swojego konta magazynu.
1. W obszarze **Ustawienia**Znajdź ustawienie **Konfiguracja** .
1. Ustaw **dostęp publiczny obiektu BLOB** na **włączony** lub **wyłączony**.

    :::image type="content" source="media/anonymous-read-access-configure/blob-public-access-portal.png" alt-text="Zrzut ekranu przedstawiający sposób zezwalania lub zapobiegania dostępowi do dostępu publicznego obiektu BLOB dla konta":::

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby zezwolić na dostęp publiczny do konta magazynu przy użyciu programu PowerShell lub go uniemożliwić, zainstaluj [Azure PowerShell wersji 4.4.0](https://www.powershellgallery.com/packages/Az/4.4.0) lub nowszej. Następnie skonfiguruj Właściwość **AllowBlobPublicAccess** dla nowego lub istniejącego konta magazynu.

Poniższy przykład tworzy konto magazynu i jawnie ustawia właściwość **AllowBlobPublicAccess** na **true**. Następnie aktualizuje konto magazynu, aby ustawić właściwość **AllowBlobPublicAccess** na **wartość false**. Przykład pobiera również wartość właściwości w każdym przypadku. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

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

Aby zezwolić na dostęp publiczny do konta magazynu przy użyciu interfejsu wiersza polecenia platformy Azure lub go uniemożliwić, zainstaluj interfejs wiersza polecenia platformy Azure w wersji 2.9.0 lub nowszej. Aby uzyskać więcej informacji, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Następnie skonfiguruj Właściwość **allowBlobPublicAccess** dla nowego lub istniejącego konta magazynu.

Poniższy przykład tworzy konto magazynu i jawnie ustawia właściwość **allowBlobPublicAccess** na **true**. Następnie aktualizuje konto magazynu, aby ustawić właściwość **allowBlobPublicAccess** na **wartość false**. Przykład pobiera również wartość właściwości w każdym przypadku. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

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

Aby zezwolić na dostęp publiczny do konta magazynu z szablonem lub go uniemożliwić, Utwórz szablon z właściwością **AllowBlobPublicAccess** ustawioną na **wartość true** lub **false**. Poniższe kroki opisują sposób tworzenia szablonu w Azure Portal.

1. W Azure Portal wybierz pozycję **Utwórz zasób**.
1. W obszarze **Wyszukaj w portalu Marketplace**wpisz **wdrożenie szablonu**, a następnie naciśnij klawisz **Enter**.
1. Wybierz **Template Deployment (Wdróż przy użyciu szablonów niestandardowych) (wersja zapoznawcza)**, wybierz pozycję **Utwórz**, a następnie wybierz opcję **Kompiluj własny szablon w edytorze**.
1. W edytorze szablonów wklej poniższy kod JSON, aby utworzyć nowe konto i ustawić dla właściwości **AllowBlobPublicAccess** **wartość true** lub **false**. Pamiętaj, aby zastąpić symbole zastępcze w nawiasach kątowe własnymi wartościami.

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
1. Określ parametr grupy zasobów, a następnie wybierz przycisk **Recenzja + Utwórz** , aby wdrożyć szablon i utworzyć konto magazynu przy użyciu skonfigurowanej właściwości **allowBlobPublicAccess** .

---

> [!NOTE]
> Niezezwalanie na dostęp publiczny do konta magazynu nie ma wpływu na żadne statyczne witryny internetowe hostowane na tym koncie magazynu. Kontener **$Web** jest zawsze dostępny publicznie.
>
> Po zaktualizowaniu ustawienia dostępu publicznego dla konta magazynu może upłynąć do 30 sekund, zanim zmiana zostanie w pełni rozpropagowana.

Umożliwienie lub niezezwalanie na dostęp publiczny obiektu BLOB wymaga wersji 2019-04-01 lub nowszej dostawcy zasobów usługi Azure Storage. Aby uzyskać więcej informacji, zobacz [interfejs API REST dostawcy zasobów usługi Azure Storage](/rest/api/storagerp/).

W przykładach w tej sekcji pokazano, jak odczytać właściwość **AllowBlobPublicAccess** konta magazynu, aby określić, czy dostęp publiczny jest obecnie dozwolony czy niedozwolony. Aby dowiedzieć się więcej o tym, jak sprawdzić, czy ustawienie dostępu publicznego do konta jest skonfigurowane tak, aby uniemożliwić dostęp anonimowy, zobacz [korygowanie anonimowego dostępu publicznego](anonymous-read-access-prevent.md#remediate-anonymous-public-access).

## <a name="set-the-public-access-level-for-a-container"></a>Ustawianie publicznego poziomu dostępu dla kontenera

Aby udzielić anonimowym użytkownikom dostępu do odczytu do kontenera i jego obiektów blob, najpierw Zezwól na dostęp publiczny do konta magazynu, a następnie ustaw poziom dostępu publicznego kontenera. Jeśli dostęp publiczny zostanie odrzucony dla konta magazynu, nie będzie można skonfigurować dostępu publicznego dla kontenera.

Jeśli dostęp publiczny jest dozwolony dla konta magazynu, można skonfigurować kontener z następującymi uprawnieniami:

- **Brak publicznego dostępu do odczytu:** Do kontenera i jego obiektów BLOB można uzyskać dostęp tylko za pomocą autoryzowanego żądania. Ta opcja jest wartością domyślną dla wszystkich nowych kontenerów.
- **Publiczny dostęp do odczytu tylko dla obiektów blob:** Obiekty blob w kontenerze mogą być odczytywane przez żądanie anonimowe, ale dane kontenera nie są dostępne anonimowo. Klienci anonimowi nie mogą wyliczyć obiektów BLOB w kontenerze.
- **Publiczny dostęp do odczytu dla kontenera i jego obiektów blob:** Dane kontenera i obiektu BLOB mogą być odczytywane przez żądanie anonimowe, z wyjątkiem ustawień uprawnień kontenera i metadanych kontenera. Klienci mogą wyliczać obiekty blob w kontenerze przez żądanie anonimowe, ale nie mogą wyliczać kontenerów na koncie magazynu.

Nie można zmienić publicznego poziomu dostępu dla pojedynczego obiektu BLOB. Poziom dostępu publicznego jest ustawiany tylko na poziomie kontenera. Można ustawić publiczny poziom dostępu kontenera podczas tworzenia kontenera lub zaktualizować ustawienia w istniejącym kontenerze.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Aby zaktualizować poziom dostępu publicznego dla jednego lub kilku istniejących kontenerów w Azure Portal, wykonaj następujące kroki:

1. Przejdź do omówienia konta magazynu w Azure Portal.
1. W obszarze **BLOB Service** w bloku menu wybierz pozycję **kontenery**.
1. Wybierz kontenery, dla których chcesz ustawić poziom dostępu publicznego.
1. Użyj przycisku **Zmień poziom dostępu** , aby wyświetlić ustawienia dostępu publicznego.
1. Wybierz żądany poziom dostępu publicznego z listy rozwijanej **poziom dostępu publicznego** , a następnie kliknij przycisk OK, aby zastosować zmianę do wybranych kontenerów.

    ![Zrzut ekranu przedstawiający sposób ustawiania poziomu dostępu publicznego w portalu](./media/anonymous-read-access-configure/configure-public-access-container.png)

Jeśli dostęp publiczny jest niedozwolony dla konta magazynu, nie można ustawić publicznego poziomu dostępu kontenera. Jeśli zostanie podjęta próba ustawienia publicznego poziomu dostępu kontenera, zobaczysz, że ustawienie jest wyłączone, ponieważ dostęp publiczny jest niedozwolony dla konta.

:::image type="content" source="media/anonymous-read-access-configure/container-public-access-blocked.png" alt-text="Zrzut ekranu przedstawiający sposób zezwalania lub zapobiegania dostępowi do dostępu publicznego obiektu BLOB dla konta":::

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby zaktualizować poziom dostępu publicznego dla co najmniej jednego kontenera przy użyciu programu PowerShell, wywołaj polecenie [Set-AzStorageContainerAcl](/powershell/module/az.storage/set-azstoragecontaineracl) . Autoryzuj tę operację przez przekazanie klucza konta, parametrów połączenia lub sygnatury dostępu współdzielonego (SAS). Operacja [ustawiania listy ACL kontenera](/rest/api/storageservices/set-container-acl) , która ustawia poziom dostępu publicznego kontenera, nie obsługuje autoryzacji w usłudze Azure AD. Aby uzyskać więcej informacji, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

Poniższy przykład tworzy kontener z wyłączonym dostępem publicznym, a następnie aktualizuje ustawienia dostępu publicznego kontenera, aby zezwolić na anonimowy dostęp do kontenera i jego obiektów BLOB. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

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

Jeśli dostęp publiczny jest niedozwolony dla konta magazynu, nie można ustawić publicznego poziomu dostępu kontenera. W przypadku próby ustawienia publicznego poziomu dostępu do kontenera usługa Azure Storage zwraca błąd wskazujący, że dostęp publiczny nie jest dozwolony na koncie magazynu.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby zaktualizować poziom dostępu publicznego dla co najmniej jednego kontenera za pomocą interfejsu wiersza polecenia platformy Azure, wywołaj polecenie [AZ Storage Container Set Permission](/cli/azure/storage/container#az-storage-container-set-permission) . Autoryzuj tę operację przez przekazanie klucza konta, parametrów połączenia lub sygnatury dostępu współdzielonego (SAS). Operacja [ustawiania listy ACL kontenera](/rest/api/storageservices/set-container-acl) , która ustawia poziom dostępu publicznego kontenera, nie obsługuje autoryzacji w usłudze Azure AD. Aby uzyskać więcej informacji, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

Poniższy przykład tworzy kontener z wyłączonym dostępem publicznym, a następnie aktualizuje ustawienia dostępu publicznego kontenera, aby zezwolić na anonimowy dostęp do kontenera i jego obiektów BLOB. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

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

Jeśli dostęp publiczny jest niedozwolony dla konta magazynu, nie można ustawić publicznego poziomu dostępu kontenera. W przypadku próby ustawienia publicznego poziomu dostępu do kontenera usługa Azure Storage zwraca błąd wskazujący, że dostęp publiczny nie jest dozwolony na koncie magazynu.

# <a name="template"></a>[Szablon](#tab/template)

Nie dotyczy.

---

## <a name="check-the-public-access-setting-for-a-set-of-containers"></a>Sprawdź ustawienia dostępu publicznego dla zestawu kontenerów

Istnieje możliwość sprawdzenia, które kontenery w co najmniej jednym koncie magazynu są skonfigurowane do dostępu publicznego przez wystawienie kontenerów i sprawdzenie ustawienia dostępu publicznego. To podejście jest praktyczną opcją, gdy konto magazynu nie zawiera dużej liczby kontenerów lub gdy sprawdzasz ustawienia na małej liczbie kont magazynu. Wydajność może jednak wystąpić, jeśli spróbujesz wyliczyć dużą liczbę kontenerów.

Poniższy przykład używa programu PowerShell, aby uzyskać ustawienia dostępu publicznego dla wszystkich kontenerów na koncie magazynu. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach własnymi wartościami:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Get-AzStorageContainer -Context $ctx | Select Name, PublicAccess
```

## <a name="next-steps"></a>Następne kroki

- [Zapobiegaj Anonimowemu dostępowi do odczytu do kontenerów i obiektów BLOB](anonymous-read-access-prevent.md)
- [Anonimowe uzyskiwanie dostępu do publicznych kontenerów i obiektów BLOB przy użyciu platformy .NET](anonymous-read-access-client.md)
- [Autoryzowanie dostępu do usługi Azure Storage](../common/storage-auth.md)
