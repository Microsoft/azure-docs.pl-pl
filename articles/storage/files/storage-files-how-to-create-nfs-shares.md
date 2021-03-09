---
title: Tworzenie udziału NFS — Azure Files (wersja zapoznawcza)
description: Dowiedz się, jak utworzyć udział plików platformy Azure, który można zainstalować przy użyciu protokołu sieciowego systemu plików.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/22/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: b085b9991175d8cd43e2dac0db80c5af4e703c34
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521241"
---
# <a name="how-to-create-an-nfs-share"></a>Jak utworzyć udział NFS
Udziały plików platformy Azure to w pełni zarządzane udziały plików w chmurze. W tym artykule opisano tworzenie udziału plików, który używa protokołu NFS. Aby uzyskać więcej informacji na temat obu protokołów, zobacz [Protokoły udziałów plików platformy Azure](storage-files-compare-protocols.md).

## <a name="limitations"></a>Ograniczenia
[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Dostępność regionalna
[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Wymagania wstępne
- Dostęp do udziałów NFS można uzyskać tylko z zaufanych sieci. Połączenia z udziałem NFS muszą pochodzić z jednego z następujących źródeł:
    - [Utwórz prywatny punkt końcowy](storage-files-networking-endpoints.md#create-a-private-endpoint) (zalecane) lub [Ogranicz dostęp do publicznego punktu końcowego](storage-files-networking-endpoints.md#restrict-public-endpoint-access).
    - [Skonfiguruj sieć VPN typu punkt-lokacja (P2S) w systemie Linux do użycia z Azure Files](storage-files-configure-p2s-vpn-linux.md).
    - [Skonfiguruj sieć VPN typu lokacja-lokacja do użycia z Azure Files](storage-files-configure-s2s-vpn.md).
    - Skonfiguruj [ExpressRoute](../../expressroute/expressroute-introduction.md).

- Jeśli zamierzasz korzystać z interfejsu wiersza polecenia platformy Azure, [Zainstaluj najnowszą wersję](/cli/azure/install-azure-cli).

## <a name="register-the-nfs-41-protocol"></a>Rejestrowanie protokołu NFS 4,1
Jeśli używasz modułu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, zarejestruj swoją funkcję przy użyciu następujących poleceń:

# <a name="portal"></a>[Portal](#tab/azure-portal)
Użyj Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, aby zarejestrować funkcję systemu plików NFS 4,1 dla Azure Files.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
```azurepowershell
# Connect your PowerShell session to your Azure account, if you have not already done so.
Connect-AzAccount

# Set the actively selected subscription, if you have not already done so.
$subscriptionId = "<yourSubscriptionIDHere>"
$context = Get-AzSubscription -SubscriptionId $subscriptionId
Set-AzContext $context

# Register the NFS 4.1 feature with Azure Files to enable the preview.
Register-AzProviderFeature `
    -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowNfsFileShares 
    
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
```azurecli
# Connect your Azure CLI to your Azure account, if you have not already done so.
az login

# Provide the subscription ID for the subscription where you would like to 
# register the feature
subscriptionId="<yourSubscriptionIDHere>"

az feature register \
    --name AllowNfsFileShares \
    --namespace Microsoft.Storage \
    --subscription $subscriptionId

az provider register \
    --namespace Microsoft.Storage
```

---

Zatwierdzenie rejestracji może potrwać do godziny. Aby sprawdzić, czy rejestracja została zakończona, użyj następujących poleceń:

# <a name="portal"></a>[Portal](#tab/azure-portal)
Użyj Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, aby sprawdzić rejestrację funkcji systemu plików NFS 4,1 dla Azure Files. 

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
```azurepowershell
Get-AzProviderFeature `
    -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowNfsFileShares
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
```azurecli
az feature show \
    --name AllowNfsFileShares \
    --namespace Microsoft.Storage \
    --subscription $subscriptionId
```

---

## <a name="create-a-filestorage-storage-account"></a>Utwórz konto magazynu FileStorage
Obecnie udziały plików w systemie plików NFS 4,1 są dostępne tylko w przypadku udziałów pliku w warstwie Premium. Aby wdrożyć udział plików w warstwie Premium z obsługą protokołu NFS 4,1, należy najpierw utworzyć konto magazynu FileStorage. Konto magazynu jest obiektem najwyższego poziomu na platformie Azure, który reprezentuje udostępnioną pulę magazynów, której można użyć do wdrożenia wielu udziałów plików platformy Azure.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Aby utworzyć konto magazynu FileStorage, przejdź do Azure Portal.

1. W Azure Portal wybierz pozycję **konta magazynu** w menu po lewej stronie.

    ![Strona główna Azure Portal Wybieranie konta magazynu](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

2. W oknie **Konta magazynu**, które zostanie wyświetlone, wybierz pozycję **Dodaj**.
3. Wybierz subskrypcję, w ramach której chcesz utworzyć konto magazynu.
4. Wybierz grupę zasobów, w której chcesz utworzyć konto magazynu

5. Następnie wprowadź nazwę konta magazynu. Wybrana nazwa musi być unikatowa w obrębie całej platformy Azure. Ponadto nazwa musi mieć długość od 3 do 24 znaków i może zawierać tylko cyfry i małe litery.
6. Wybierz lokalizację konta magazynu lub użyj lokalizacji domyślnej.
7. W obszarze **wydajność** wybierz opcję **Premium**.

    Musisz wybrać opcję **Premium** for **FileStorage** , aby była dostępna opcja na liście rozwijanej **rodzaj konta** .

8. Wybierz pozycję **rodzaj konta** i wybierz pozycję **FileStorage**.
9. Dla opcji **replikacja** ustaw wartość domyślną **Magazyn lokalnie nadmiarowy (LRS)**.

    ![Jak utworzyć konto magazynu dla udziału plików w warstwie Premium](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

10. Wybierz pozycję **Przejrzyj i utwórz**, aby przejrzeć ustawienia konta magazynu i utworzyć konto.
11. Wybierz przycisk **Utwórz**.

Po utworzeniu zasobu konta magazynu przejdź do niego.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
Aby utworzyć konto magazynu FileStorage, Otwórz wiersz programu PowerShell i wykonaj następujące polecenia, zastępując je zastępując `<resource-group>` i `<storage-account>` z odpowiednimi wartościami dla danego środowiska.

```powershell
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"
$location = "westus2"

$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $location `
    -Kind FileStorage
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Aby utworzyć konto magazynu FileStorage, Otwórz Terminal i wykonaj następujące polecenia, zastępując je zastępując `<resource-group>` i `<storage-account>` z odpowiednimi wartościami dla danego środowiska.

```azurecli-interactive
resourceGroup="<resource-group>"
storageAccount="<storage-account>"
location="westus2"

az storage account create \
    --resource-group $resourceGroup \
    --name $storageAccount \
    --location $location \
    --sku Premium_LRS \
    --kind FileStorage
```
---

## <a name="create-an-nfs-share"></a>Tworzenie udziału NFS

# <a name="portal"></a>[Portal](#tab/azure-portal)

Po utworzeniu konta FileStorage i skonfigurowaniu sieci można utworzyć udział plików NFS. Ten proces jest podobny do tworzenia udziału SMB, podczas tworzenia udziału wybiera się system **plików NFS** zamiast **SMB** .

1. Przejdź do konta magazynu i wybierz pozycję **udziały plików**.
1. Wybierz pozycję **+ udział plików** , aby utworzyć nowy udział plików.
1. Nazwij udział plików, wybierz pojemność zainicjowaną.
1. Dla opcji wybór **protokołu** **NFS (wersja zapoznawcza)**.
1. Dla **elementu głównego squash** wybierz opcję.

    - Root squash (domyślnie) — dostęp do zdalnej administratora (root) jest mapowany na UID (65534) i GID (65534).
    - Brak elementu głównego squash-Remote administratora (root) odbiera dostęp jako główny.
    - Wszystkie squash — dostęp wszystkich użytkowników jest mapowany na UID (65534) i GID (65534).
    
1. Wybierz przycisk **Utwórz**.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/create-nfs-file-share.png" alt-text="Zrzut ekranu bloku tworzenia udziału plików":::

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Upewnij się, że program .NET Framework jest zainstalowany. Zobacz [pobieranie .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
 
1. Sprawdź, czy zainstalowana wersja programu PowerShell jest `5.1` lub nowsza przy użyciu następującego polecenia.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Aby uaktualnić wersję programu PowerShell, zobacz [uaktualnianie istniejącego środowiska Windows PowerShell](/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell)
    
1. Zainstaluj najnowszą wersję modułu PowershellGet.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

1. Zamknij program, a następnie ponownie otwórz konsolę programu PowerShell.

1. Zainstaluj moduł **AZ. Storage** Preview w wersji **2.5.2-Preview**.

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   Aby uzyskać więcej informacji na temat sposobu instalowania modułów programu PowerShell, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps)
   
1. Aby utworzyć udział plików w warstwie Premium przy użyciu modułu Azure PowerShell, należy użyć polecenia cmdlet [New-AzRmStorageShare](/powershell/module/az.storage/new-azrmstorageshare) .

    > [!NOTE]
    > Udziały plików w warstwie Premium są rozliczane przy użyciu modelu aprowizacji. Udostępniony rozmiar udziału jest określony `QuotaGiB` poniżej. Aby uzyskać więcej informacji, zobacz [Omówienie modelu aprowizacji](understanding-billing.md#provisioned-model) i [cennika Azure Files](https://azure.microsoft.com/pricing/details/storage/files/).

    ```powershell
    New-AzRmStorageShare `
        -StorageAccount $storageAccount `
        -Name myshare `
        -EnabledProtocol NFS `
        -RootSquash RootSquash `
        -QuotaGiB 1024
    ```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Aby utworzyć udział plików w warstwie Premium za pomocą interfejsu wiersza polecenia platformy Azure, użyj polecenie [AZ Storage Share Create](/cli/azure/storage/share-rm) .

> [!NOTE]
> Udziały plików w warstwie Premium są rozliczane przy użyciu modelu aprowizacji. Udostępniony rozmiar udziału jest określony `quota` poniżej. Aby uzyskać więcej informacji, zobacz [Omówienie modelu aprowizacji](understanding-billing.md#provisioned-model) i [cennika Azure Files](https://azure.microsoft.com/pricing/details/storage/files/).

```azurecli-interactive
az storage share-rm create \
    --resource-group $resourceGroup \
    --storage-account $storageAccount \
    --name "myshare" \
    --enabled-protocol NFS \
    --root-squash RootSquash \
    --quota 1024
```
---

## <a name="next-steps"></a>Następne kroki
Po utworzeniu udziału NFS należy go zainstalować na komputerze klienckim z systemem Linux. Aby uzyskać szczegółowe informacje, zobacz [jak zainstalować udział NFS](storage-files-how-to-mount-nfs-shares.md).

Jeśli występują jakieś problemy, zobacz [Rozwiązywanie problemów z udziałami plików NFS systemu Azure](storage-troubleshooting-files-nfs.md).