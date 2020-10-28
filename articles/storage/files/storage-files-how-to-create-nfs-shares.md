---
title: Tworzenie udziału NFS — Azure Files
description: Dowiedz się, jak utworzyć udział plików platformy Azure, który można zainstalować przy użyciu protokołu sieciowego systemu plików.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: bf75537c0baf029bc3fc63e320f6290a1f41a524
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92738833"
---
# <a name="how-to-create-an-nfs-share"></a>Jak utworzyć udział NFS

Udziały plików platformy Azure to w pełni zarządzane udziały plików w chmurze. Dostęp do nich można uzyskać przy użyciu protokołu bloku komunikatów serwera lub protokołu sieciowego systemu plików (NFS). W tym artykule opisano tworzenie udziału plików, który używa protokołu NFS. Aby uzyskać więcej informacji na temat obu protokołów, zobacz [Protokoły udziałów plików platformy Azure](storage-files-compare-protocols.md).

## <a name="limitations"></a>Ograniczenia

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Dostępność regionalna

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Utwórz [konto FileStorage](storage-how-to-create-premium-fileshare.md).

    > [!IMPORTANT]
    > Dostęp do udziałów NFS można uzyskać tylko z zaufanych sieci. Połączenia z udziałem NFS muszą pochodzić z jednego z następujących źródeł:

    - [Utwórz prywatny punkt końcowy](storage-files-networking-endpoints.md#create-a-private-endpoint) (zalecane) lub [Ogranicz dostęp do publicznego punktu końcowego](storage-files-networking-endpoints.md#restrict-public-endpoint-access).
    - [Skonfiguruj sieć VPN typu punkt-lokacja (P2S) w systemie Linux do użycia z Azure Files](storage-files-configure-p2s-vpn-linux.md).
    - [Skonfiguruj sieć VPN typu lokacja-lokacja do użycia z Azure Files](storage-files-configure-s2s-vpn.md).
    - Skonfiguruj [ExpressRoute](../../expressroute/expressroute-introduction.md).
- Jeśli zamierzasz korzystać z interfejsu wiersza polecenia platformy Azure, [Zainstaluj najnowszą wersję](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="register-the-nfs-41-protocol"></a>Rejestrowanie protokołu NFS 4,1

Jeśli używasz modułu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, zarejestruj swoją funkcję przy użyciu następujących poleceń:

### <a name="powershell"></a>PowerShell

```azurepowershell
Connect-AzAccount
$context = Get-AzSubscription -SubscriptionId <yourSubscriptionIDHere>
Set-AzContext $context
Register-AzProviderFeature -FeatureName AllowNfsFileShares -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
az login
az feature register --name AllowNfsFileShares \
                    --namespace Microsoft.Storage \
                    --subscription <yourSubscriptionIDHere>
az provider register --namespace Microsoft.Storage
```

## <a name="verify-that-the-feature-is-registered"></a>Sprawdź, czy funkcja jest zarejestrowana

Zatwierdzenie rejestracji może potrwać do godziny. Aby sprawdzić, czy rejestracja została zakończona, użyj następujących poleceń:

### <a name="powershell"></a>PowerShell

```azurepowershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName AllowNfsFileShares
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
az feature show --name AllowNfsFileShares --namespace Microsoft.Storage --subscription <yourSubscriptionIDHere>
```

## <a name="create-an-nfs-share"></a>Tworzenie udziału NFS

# <a name="portal"></a>[Portal](#tab/azure-portal)

Po utworzeniu konta FileStorage i skonfigurowaniu sieci można utworzyć udział plików NFS. Ten proces jest podobny do tworzenia udziału SMB, podczas tworzenia udziału wybiera się system **plików NFS** zamiast **SMB** .

1. Przejdź do konta magazynu i wybierz pozycję **udziały plików** .
1. Wybierz pozycję **+ udział plików** , aby utworzyć nowy udział plików.
1. Nazwij udział plików, wybierz pojemność zainicjowaną.
1. Dla opcji wybór **protokołu** **NFS (wersja zapoznawcza)** .
1. Dla **elementu głównego squash** wybierz opcję.

    - Root squash (domyślnie) — dostęp do zdalnej administratora (root) jest mapowany na UID (65534) i GID (65534).
    - Brak elementu głównego squash-Remote administratora (root) odbiera dostęp jako główny.
    - Wszystkie squash — dostęp wszystkich użytkowników jest mapowany na UID (65534) i GID (65534).
    
1. Wybierz pozycję **Utwórz** .

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/create-nfs-file-share.png" alt-text="Zrzut ekranu bloku tworzenia udziału plików":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Upewnij się, że program .NET Framework jest zainstalowany. Zobacz [pobieranie .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
 
1. Sprawdź, czy zainstalowana wersja programu PowerShell jest `5.1` lub nowsza przy użyciu następującego polecenia.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Aby uaktualnić wersję programu PowerShell, zobacz [uaktualnianie istniejącego środowiska Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)
    
1. Zainstaluj najnowszą wersję modułu PowershellGet.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

1. Zamknij program, a następnie ponownie otwórz konsolę programu PowerShell.

1. Zainstaluj moduł **AZ. Storage** Preview w wersji **2.5.2-Preview** .

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   Aby uzyskać więcej informacji na temat sposobu instalowania modułów programu PowerShell, zobacz [Instalowanie modułu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)
   
1. Aby utworzyć udział plików w warstwie Premium przy użyciu modułu Azure PowerShell, należy użyć polecenia cmdlet [New-AzRmStorageShare](/powershell/module/az.storage/new-azrmstorageshare) .

> [!NOTE]
> Rozmiar udostępnianych udziałów jest określany przez przydział udziału, a udziały plików są rozliczane zgodnie z przydziałem. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/storage/files/).

  ```powershell
  New-AzRmStorageShare `
   -ResourceGroupName $resourceGroupName `
   -StorageAccountName $storageAccountName `
   -Name myshare `
   -EnabledProtocol NFS `
   -RootSquash RootSquash `
   -Context $storageAcct.Context
  ```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby utworzyć udział plików w warstwie Premium za pomocą interfejsu wiersza polecenia platformy Azure, użyj polecenie [AZ Storage Share Create](/cli/azure/storage/share-rm) .

> [!NOTE]
> Rozmiar udostępnianych udziałów jest określany przez przydział udziału, a udziały plików są rozliczane zgodnie z przydziałem. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/storage/files/).

```azurecli-interactive
az storage share-rm create \
    --storage-account $STORAGEACCT \
    --enabled-protocol NFS \
    --root-squash RootSquash \
    --name "myshare" 
```
---

## <a name="next-steps"></a>Następne kroki

Po utworzeniu udziału NFS należy go zainstalować na komputerze klienckim z systemem Linux. Aby uzyskać szczegółowe informacje, zobacz [jak zainstalować udział NFS](storage-files-how-to-mount-nfs-shares.md).

Jeśli występują jakieś problemy, zobacz [Rozwiązywanie problemów z udziałami plików NFS systemu Azure](storage-troubleshooting-files-nfs.md).
