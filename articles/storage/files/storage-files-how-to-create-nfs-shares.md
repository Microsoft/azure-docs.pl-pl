---
title: Tworzenie udziału NFS — Azure Files (wersja zapoznawcza)
description: Dowiedz się, jak utworzyć udział plików platformy Azure, który można tworzyć przy użyciu protokołu sieciowego systemu plików.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/05/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: b549c625f0a6ff0480eafc38f84d292e66350950
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717135"
---
# <a name="how-to-create-an-nfs-share"></a>Jak utworzyć udział NFS
Udziały plików platformy Azure to w pełni zarządzane udziały plików, które znajdują się w chmurze. W tym artykule o mowa o tworzeniu udziału plików, który używa protokołu NFS. Aby uzyskać więcej informacji na temat obu protokołów, zobacz [Azure file share protocols (Protokoły udziałów plików platformy Azure).](storage-files-compare-protocols.md)

## <a name="limitations"></a>Ograniczenia
[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Dostępność w regionach
[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Wymagania wstępne
- Dostęp do udziałów NFS można uzyskać tylko z zaufanych sieci. Połączenia z udziałami NFS muszą pochodzić z jednego z następujących źródeł:
    - Utwórz [prywatny punkt końcowy](storage-files-networking-endpoints.md#create-a-private-endpoint) (zalecane) lub ogranicz dostęp do publicznego punktu [końcowego.](storage-files-networking-endpoints.md#restrict-public-endpoint-access)
    - [Skonfiguruj sieć VPN typu punkt-lokacja (P2S)](storage-files-configure-p2s-vpn-linux.md)w systemie Linux do użycia z systemem Azure Files .
    - [Skonfiguruj sieć VPN typu lokacja-lokacja do użycia z usługą Azure Files](storage-files-configure-s2s-vpn.md).
    - Skonfiguruj [usługę ExpressRoute.](../../expressroute/expressroute-introduction.md)

- Jeśli zamierzasz używać interfejsu wiersza polecenia platformy Azure, [zainstaluj najnowszą wersję .](/cli/azure/install-azure-cli)

## <a name="register-the-nfs-41-protocol"></a>Rejestrowanie protokołu NFS 4.1
Jeśli używasz modułu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, zarejestruj swoją funkcję przy użyciu następujących poleceń:

# <a name="portal"></a>[Portal](#tab/azure-portal)
Użyj interfejsu Azure PowerShell wiersza polecenia platformy Azure, aby zarejestrować funkcję systemu plików NFS 4.1 na Azure Files.

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

Zatwierdzenie rejestracji może potrwać do godziny. Aby sprawdzić, czy rejestracja jest ukończona, użyj następujących poleceń:

# <a name="portal"></a>[Portal](#tab/azure-portal)
Użyj interfejsu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, aby sprawdzić rejestrację funkcji systemu plików NFS 4.1 dla systemu Azure Files. 

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

## <a name="create-a-filestorage-storage-account"></a>Tworzenie konta magazynu FileStorage
Obecnie udziały NFS 4.1 są dostępne tylko jako udziały plików Premium. Aby wdrożyć udział plików w chmurze w chmurze z obsługą protokołu NFS 4.1, należy najpierw utworzyć konto magazynu FileStorage. Konto magazynu to obiekt najwyższego poziomu na platformie Azure, który reprezentuje udostępnioną pulę magazynu, która może służyć do wdrażania wielu udziałów plików platformy Azure.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Aby utworzyć konto magazynu FileStorage, przejdź do Azure Portal.

1. W okienku Azure Portal pozycję **Konta magazynu** w menu po lewej stronie.

    ![Azure Portal stronie głównej wybierz konto magazynu.](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. W oknie **Konta magazynu**, które zostanie wyświetlone, wybierz pozycję **Dodaj**.
1. Wybierz subskrypcję, w ramach której chcesz utworzyć konto magazynu.
1. Wybierz grupę zasobów, w której chcesz utworzyć konto magazynu
1. Następnie wprowadź nazwę konta magazynu. Wybrana nazwa musi być unikatowa w obrębie całej platformy Azure. Ponadto nazwa musi mieć długość od 3 do 24 znaków i może zawierać tylko cyfry i małe litery.
1. Wybierz lokalizację konta magazynu lub użyj lokalizacji domyślnej.
1. W **przypadku opcji Wydajność** wybierz pozycję **Premium.**

    Aby udostępnić **opcję Udziały** **plików** na liście rozwijanej **Rodzaj** konta, musisz wybrać opcję Premium.

1. W **przypadku konta Premium wybierz** pozycję **Udziały plików.**

    :::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-performance-premium.png" alt-text="Zrzut ekranu przedstawiający wybraną wydajność Premium.":::

1. Pozostaw **wartość** domyślną Replikacja magazynu **lokalnie nadmiarowego (LRS).**
1. Wybierz pozycję **Przejrzyj i utwórz**, aby przejrzeć ustawienia konta magazynu i utworzyć konto.
1. Wybierz przycisk **Utwórz**.

Po utworzeniu zasobu konta magazynu przejdź do niego.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
Aby utworzyć konto magazynu FileStorage, otwórz wiersz polecenia programu PowerShell i wykonaj następujące polecenia, pamiętając o zastąpieniu wartości i odpowiednimi wartościami `<resource-group>` `<storage-account>` dla środowiska.

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
Aby utworzyć konto magazynu FileStorage, otwórz terminal i wykonaj następujące polecenia, pamiętając o zastąpień i odpowiednimi wartościami `<resource-group>` `<storage-account>` dla środowiska.

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

Po utworzeniu konta FileStorage i skonfigurowaniu sieci można utworzyć udział plików NFS. Proces jest podobny do tworzenia udziału SMB. Podczas tworzenia udziału należy wybrać opcję **NFS** zamiast **SMB.**

1. Przejdź do konta magazynu i wybierz **pozycję Udziały plików.**
1. Wybierz **pozycję + Udział plików,** aby utworzyć nowy udział plików.
1. Nadaj nazwę udziałowi plików i wybierz aprowizowana pojemność.
1. W **przypadku opcji Protokół** wybierz pozycję **NFS (wersja zapoznawcza).**
1. W **przypadku opcji Root Squash** (Squash główny) zaznacz opcje.

    - Główny squash (ustawienie domyślne) — dostęp do zdalnego superużytkownika (głównego) jest mapowany na wartości UID (65534) i GID (65534).
    - Brak głównego typu "squash" — zdalny superużytkownik (główny) otrzymuje dostęp jako użytkownik główny.
    - Wszystkie squash — cały dostęp użytkownika jest mapowany na identyfikator UID (65534) i GID (65534).
    
1. Wybierz przycisk **Utwórz**.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/files-nfs-create-share.png" alt-text="Zrzut ekranu przedstawiający blok tworzenia udziału plików.":::

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Upewnij się, że program .NET Framework jest zainstalowany. Zobacz [Pobieranie .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
 
1. Sprawdź, czy zainstalowana wersja programu PowerShell jest lub `5.1` nowsza, używając następującego polecenia.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Aby uaktualnić wersję programu PowerShell, zobacz [Uaktualnianie istniejących Windows PowerShell](/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell)
    
1. Zainstaluj najnowszą wersję modułu PowershellGet.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

1. Zamknij, a następnie otwórz ponownie konsolę programu PowerShell.

1. Zainstaluj moduł **Az.Storage** w wersji zapoznawczej **w wersji 2.5.2-preview.**

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   Aby uzyskać więcej informacji na temat sposobu instalowania modułów programu PowerShell, zobacz [Instalowanie Azure PowerShell modułu](/powershell/azure/install-az-ps)
   
1. Aby utworzyć udział plików Premium przy użyciu modułu Azure PowerShell, użyj polecenia cmdlet [New-AzRmStorageShare.](/powershell/module/az.storage/new-azrmstorageshare)

    > [!NOTE]
    > Udziały plików w wersji Premium są rozliczane przy użyciu aprowizowanych modeli. Aprowizowany rozmiar udziału jest określony przez `QuotaGiB` poniżej. Aby uzyskać więcej informacji, zobacz [Understanding the provisioned model (Omówienie](understanding-billing.md#provisioned-model) aprowizowanych modeli) [i Azure Files cennika](https://azure.microsoft.com/pricing/details/storage/files/)usługi .

    ```powershell
    New-AzRmStorageShare `
        -StorageAccount $storageAccount `
        -Name myshare `
        -EnabledProtocol NFS `
        -RootSquash RootSquash `
        -QuotaGiB 1024
    ```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Aby utworzyć udział plików w witrynie Premium przy użyciu interfejsu wiersza polecenia platformy Azure, użyj [polecenia az storage share create.](/cli/azure/storage/share-rm)

> [!NOTE]
> Udziały plików w wersji Premium są rozliczane przy użyciu aprowizowanych modeli. Aprowizowany rozmiar udziału jest określony przez `quota` poniżej. Aby uzyskać więcej informacji, zobacz [Understanding the provisioned model (Omówienie](understanding-billing.md#provisioned-model) aprowizowanych modeli) [i Azure Files cennika.](https://azure.microsoft.com/pricing/details/storage/files/)

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
Teraz, po utworzeniu udziału NFS, aby go użyć, musisz zainstalować go na kliencie systemu Linux. Aby uzyskać szczegółowe informacje, [zobacz How to mount an NFS share (Jak zainstalować udział NFS).](storage-files-how-to-mount-nfs-shares.md)

Jeśli wystąpią jakiekolwiek problemy, zobacz [Troubleshoot Azure NFS file shares (Rozwiązywanie problemów z udziałami plików NFS platformy Azure).](storage-troubleshooting-files-nfs.md)