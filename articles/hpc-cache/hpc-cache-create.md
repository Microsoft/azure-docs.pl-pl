---
title: Tworzenie Azure HPC Cache
description: Jak utworzyć wystąpienie Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 02934a1943ef37d282dd2a2e7862c5695bbd6ecb
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862709"
---
# <a name="create-an-azure-hpc-cache"></a>Tworzenie Azure HPC Cache

Użyj interfejsu Azure Portal lub interfejsu wiersza polecenia platformy Azure, aby utworzyć pamięć podręczną.

![zrzut ekranu przedstawiający przegląd pamięci podręcznej w Azure Portal, z przyciskiem utwórz u dołu](media/hpc-cache-home-page.png)

Kliknij poniższy obraz, aby obejrzeć [pokaz wideo](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) z tworzeniem pamięci podręcznej i dodawaniem miejsca docelowego magazynu.

[![miniatura wideo: Azure HPC Cache: Instalator (kliknij, aby odwiedzić stronę wideo)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="portal"></a>[Portal](#tab/azure-portal)

## <a name="define-basic-details"></a>Definiowanie podstawowych szczegółów

![zrzut ekranu przedstawiający stronę szczegółów projektu w Azure Portal](media/hpc-cache-create-basics.png)

W **skrypcie Project Details** wybierz subskrypcję i grupę zasobów, które będą hostować pamięć podręczną.

W **szczegółach usługi** ustaw nazwę pamięci podręcznej i inne atrybuty:

* Lokalizacja — wybierz jeden z [obsługiwanych regionów.](hpc-cache-overview.md#region-availability)
* Sieć wirtualna — możesz wybrać istniejącą lub utworzyć nową sieć wirtualną.
* Podsieć — wybierz lub utwórz podsieć z co najmniej 64 adresami IP (/24). Ta podsieć musi być używana tylko dla tego Azure HPC Cache wystąpienia.

## <a name="set-cache-capacity"></a>Ustawianie pojemności pamięci podręcznej
<!-- referenced from GUI - update aka.ms link if you change this header text -->

Na stronie **Pamięć** podręczna należy ustawić pojemność pamięci podręcznej. Ustawione w tym miejscu wartości określają, ile danych może przechowywać pamięć podręczna i jak szybko może ona przetwarzać żądania klientów.

Pojemność wpływa również na koszt pamięci podręcznej.

Wybierz pojemność, ustawiając następujące dwie wartości:

* Maksymalna szybkość transferu danych dla pamięci podręcznej (przepływność) w GB/sekundę
* Ilość miejsca w magazynie przydzielonego dla danych w pamięci podręcznej w TB

Wybierz jedną z dostępnych wartości przepływności i rozmiary magazynu pamięci podręcznej.

Należy pamiętać, że rzeczywista szybkość transferu danych zależy od obciążenia, szybkości sieci i typu obiektów docelowych magazynu. Wybierane wartości ustawiają maksymalną przepływność dla całego systemu pamięci podręcznej, ale niektóre z nich są używane w przypadku zadań narzutu. Jeśli na przykład klient żąda pliku, który nie jest jeszcze przechowywany w pamięci podręcznej, lub jeśli plik jest oznaczony jako nieaktywne, pamięć podręczna używa części przepływności do pobrania go z magazynu na zabytce.

Azure HPC Cache zarządza plikami, które są buforowane i wstępnie ładowane, aby zmaksymalizować liczbę trafień w pamięci podręcznej. Zawartość pamięci podręcznej jest stale oceniana, a pliki są przenoszone do magazynu długoterminowego, gdy są one rzadziej używane. Wybierz rozmiar magazynu pamięci podręcznej, który może pomieścić aktywny zestaw plików roboczych, a także dodatkowe miejsce na metadane i inne obciążenia.

![zrzut ekranu przedstawiający stronę zmiany rozmiaru pamięci podręcznej](media/hpc-cache-create-capacity.png)

## <a name="enable-azure-key-vault-encryption-optional"></a>Włączanie Azure Key Vault szyfrowania danych (opcjonalnie)

Między **kartami Pamięć podręczna** i Tagi zostanie wyświetlona **strona** Klucze **szyfrowania** dysków.<!-- Read [Regional availability](hpc-cache-overview.md#region-availability) to learn more about region support. -->

Jeśli chcesz zarządzać kluczami szyfrowania używanymi dla magazynu pamięci podręcznej, Azure Key Vault informacje na stronie **Klucze szyfrowania** dysków. Magazyn kluczy musi znajdować się w tym samym regionie i w tej samej subskrypcji co pamięć podręczna.

Możesz pominąć tę sekcję, jeśli nie potrzebujesz kluczy zarządzanych przez klienta. Platforma Azure domyślnie szyfruje dane przy użyciu kluczy zarządzanych przez firmę Microsoft. Przeczytaj [szyfrowanie usługi Azure Storage,](../storage/common/storage-service-encryption.md) aby dowiedzieć się więcej.

> [!NOTE]
>
> * Po utworzeniu pamięci podręcznej nie można zmienić kluczy zarządzanych przez firmę Microsoft na klucze zarządzane przez klienta.
> * Po utworzeniu pamięci podręcznej należy autoryzować ją w celu uzyskania dostępu do magazynu kluczy. Kliknij przycisk **Włącz szyfrowanie** na stronie Przegląd pamięci podręcznej, aby włączyć szyfrowanie.  Ten krok należy podjąć w ciągu 90 minut od utworzenia pamięci podręcznej.
> * Dyski pamięci podręcznej są tworzone po tej autoryzacji. Oznacza to, że początkowy czas tworzenia pamięci podręcznej jest krótki, ale pamięć podręczna nie będzie gotowa do użycia przez 10 minut lub dłużej po autoryzacji dostępu.

Aby uzyskać pełne wyjaśnienie procesu szyfrowania klucza zarządzanego przez klienta, zobacz [Używanie zarządzanych](customer-keys.md)przez klienta kluczy szyfrowania na potrzeby Azure HPC Cache .

![zrzut ekranu przedstawiający stronę kluczy szyfrowania z wybranymi polami "Zarządzane przez klienta" i magazynem kluczy](media/create-encryption.png)

Wybierz **pozycję Klient zarządzany,** aby wybrać szyfrowanie kluczem zarządzanym przez klienta. Zostaną wyświetlone pola specyfikacji magazynu kluczy. Wybierz Azure Key Vault, a następnie wybierz klucz i wersję do użycia dla tej pamięci podręcznej. Klucz musi być 2048-bitowym kluczem RSA. Na tej stronie możesz utworzyć nowy magazyn kluczy, klucz lub wersję klucza.

Po utworzeniu pamięci podręcznej należy autoryzować ją do korzystania z usługi magazynu kluczy. Przeczytaj [autoryzowanie Azure Key Vault szyfrowania danych z pamięci podręcznej, aby](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) uzyskać szczegółowe informacje.

## <a name="add-resource-tags-optional"></a>Dodawanie tagów zasobów (opcjonalnie)

Strona **Tagi** umożliwia dodawanie [tagów zasobów](../azure-resource-manager/management/tag-resources.md) do Azure HPC Cache wystąpienia.

## <a name="finish-creating-the-cache"></a>Kończanie tworzenia pamięci podręcznej

Po skonfigurowaniu nowej pamięci podręcznej kliknij **kartę Przeglądanie i tworzenie.** Portal weryfikuje wybrane opcje i umożliwia ich przejrzenie. Jeśli wszystko jest poprawne, kliknij pozycję **Utwórz**.

Tworzenie pamięci podręcznej trwa około 10 minut. Postęp można śledzić w panelu powiadomień Azure Portal aplikacji.

![zrzut ekranu przedstawiający tworzenie pamięci podręcznej na stronach "wdrażanie w toku" i "powiadomienia" w portalu](media/hpc-cache-deploy-status.png)

Po zakończeniu tworzenia zostanie wyświetlone powiadomienie z linkiem do nowego Azure HPC Cache, a pamięć podręczna pojawi się na **liście Zasoby subskrypcji.**

![zrzut ekranu Azure HPC Cache wystąpienia w Azure Portal](media/hpc-cache-new-overview.png)

> [!NOTE]
> Jeśli pamięć podręczna używa kluczy szyfrowania zarządzanych przez klienta, pamięć podręczna może pojawić się na liście zasobów, zanim stan wdrożenia zmieni się na ukończony. Gdy tylko stan pamięci podręcznej będzie mieć stan **Oczekiwanie na klucz,** [możesz](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) autoryzować go do korzystania z magazynu kluczy.

## <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

## <a name="create-the-cache-with-azure-cli"></a>Tworzenie pamięci podręcznej przy użyciu interfejsu wiersza polecenia platformy Azure

[Skonfiguruj interfejs wiersza polecenia platformy Azure dla Azure HPC Cache](./az-cli-prerequisites.md).

> [!NOTE]
> Interfejs wiersza polecenia platformy Azure obecnie nie obsługuje tworzenia pamięci podręcznej z kluczami szyfrowania zarządzanymi przez klienta. Użyj witryny Azure Portal.

Użyj polecenia [az hpc-cache create,](/cli/azure/hpc-cache#az_hpc_cache_create) aby utworzyć nową Azure HPC Cache.

Należy podać następujące wartości:

* Nazwa grupy zasobów pamięci podręcznej
* Nazwa pamięci podręcznej
* Region platformy Azure
* Podsieć pamięci podręcznej w tym formacie:

  ``--subnet "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/sub
nets/<cache_subnet_name>"``

  Podsieć pamięci podręcznej wymaga co najmniej 64 adresów IP (/24) i nie może mieć żadnych innych zasobów.

* Pojemność pamięci podręcznej. Dwie wartości ustawiają maksymalną przepływność Azure HPC Cache:

  * Rozmiar pamięci podręcznej (w GB)
  * SKU maszyn wirtualnych używanych w infrastrukturze pamięci podręcznej

  [Az hpc-cache skus list zawiera](/cli/azure/hpc-cache/skus) dostępne jednostki SKU i prawidłowe opcje rozmiaru pamięci podręcznej dla każdej z nich. Opcje rozmiaru pamięci podręcznej mogą mieć rozmiar od 3 TB do 48 TB, ale obsługiwane są tylko niektóre wartości.

  Ten wykres pokazuje, które kombinacje rozmiaru pamięci podręcznej i jednostek SKU są prawidłowe w momencie przygotowania tego dokumentu (lipiec 2020 r.).

  | Rozmiar pamięci podręcznej | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3072 GB    | tak         | nie          | nie          |
  | 6144 GB    | tak         | tak         | nie          |
  | 12288 GB   | tak         | tak         | tak         |
  | 24576 GB   | nie          | tak         | tak         |
  | 49152 GB   | nie          | nie          | tak         |

  Zapoznaj się **z sekcją** Ustawianie pojemności pamięci podręcznej na karcie instrukcji portalu, aby uzyskać ważne informacje na temat cen, przepływności i sposobu odpowiedniego rozmiaru pamięci podręcznej dla przepływu pracy.

Przykład tworzenia pamięci podręcznej:

```azurecli
az hpc-cache create --resource-group doc-demo-rg --name my-cache-0619 \
    --location "eastus" --cache-size-gb "3072" \
    --subnet "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default" \
    --sku-name "Standard_2G"
```

Tworzenie pamięci podręcznej trwa kilka minut. W przypadku powodzenia polecenie create zwraca dane wyjściowe podobne do tych:

```azurecli
{
  "cacheSizeGb": 3072,
  "health": {
    "state": "Healthy",
    "statusDescription": "The cache is in Running state"
  },
  "id": "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.StorageCache/caches/my-cache-0619",
  "location": "eastus",
  "mountAddresses": [
    "10.3.0.17",
    "10.3.0.18",
    "10.3.0.19"
  ],
  "name": "my-cache-0619",
  "provisioningState": "Succeeded",
  "resourceGroup": "doc-demo-rg",
  "sku": {
    "name": "Standard_2G"
  },
  "subnet": "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default",
  "tags": null,
  "type": "Microsoft.StorageCache/caches",
  "upgradeStatus": {
    "currentFirmwareVersion": "5.3.42",
    "firmwareUpdateDeadline": "0001-01-01T00:00:00+00:00",
    "firmwareUpdateStatus": "unavailable",
    "lastFirmwareUpdate": "2020-04-01T15:19:54.068299+00:00",
    "pendingFirmwareVersion": null
  }
}
```

Komunikat zawiera kilka przydatnych informacji, w tym następujące elementy:

* Adresy instalacji klienta — użyj tych adresów IP, gdy wszystko będzie gotowe do połączenia klientów z pamięcią podręczną. Przeczytaj [temat Mount the Azure HPC Cache](hpc-cache-mount.md) (Zainstaluj Azure HPC Cache), aby dowiedzieć się więcej.
* Stan uaktualnienia — po zwolnieniu aktualizacji oprogramowania ten komunikat zmieni się. Oprogramowanie pamięci [podręcznej można](hpc-cache-manage.md#upgrade-cache-software) uaktualnić ręcznie w dogodnym czasie lub zostanie ono zastosowane automatycznie po kilku dniach.

## <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

> [!CAUTION]
> Moduł Az.HPCCache programu PowerShell jest obecnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza jest dostępna bez umowy dotyczącej poziomu usług. Nie jest ona zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane lub mogą mieć ograniczone możliwości. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Wymagania

Jeśli zdecydujesz się używać programu PowerShell lokalnie, ten artykuł wymaga zainstalowania modułu Az programu PowerShell i nawiązania połączenia z kontem platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Aby uzyskać więcej informacji na temat instalowania modułu Az programu PowerShell, zobacz [Instalowanie Azure PowerShell](/powershell/azure/install-az-ps). Jeśli zdecydujesz się używać Cloud Shell, zobacz [Omówienie Azure Cloud Shell,](../cloud-shell/overview.md) aby uzyskać więcej informacji.

> [!IMPORTANT]
> Gdy moduł **Az.HPCCache** programu PowerShell jest w wersji zapoznawczej, należy zainstalować go oddzielnie przy użyciu `Install-Module` polecenia cmdlet . Gdy ten moduł programu PowerShell stanie się ogólnie dostępny, będzie częścią przyszłych wersji modułu Az programu PowerShell i będzie dostępny natywnie z poziomu Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.HPCCache
```

## <a name="create-the-cache-with-azure-powershell"></a>Tworzenie pamięci podręcznej za pomocą Azure PowerShell

> [!NOTE]
> Azure PowerShell obecnie nie obsługuje tworzenia pamięci podręcznej z kluczami szyfrowania zarządzanymi przez klienta. Użyj witryny Azure Portal.

Użyj polecenia cmdlet [New-AzHpcCache,](/powershell/module/az.hpccache/new-azhpccache) aby utworzyć nową Azure HPC Cache.

Podaj następujące wartości:

* Nazwa grupy zasobów pamięci podręcznej
* Nazwa pamięci podręcznej
* Region platformy Azure
* Podsieć pamięci podręcznej w tym formacie:

  `-SubnetUri "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/sub
nets/<cache_subnet_name>"`

  Podsieć pamięci podręcznej wymaga co najmniej 64 adresów IP (/24) i nie może mieć żadnych innych zasobów.

* Pojemność pamięci podręcznej. Dwie wartości ustawiają maksymalną przepływność Azure HPC Cache:

  * Rozmiar pamięci podręcznej (w GB)
  * SKU maszyn wirtualnych używanych w infrastrukturze pamięci podręcznej

  [Get-AzHpcCacheSku](/powershell/module/az.hpccache/get-azhpccachesku) pokazuje dostępne jednostki SKU i prawidłowe opcje rozmiaru pamięci podręcznej dla każdej z nich. Opcje rozmiaru pamięci podręcznej mogą mieć rozmiar od 3 TB do 48 TB, ale obsługiwane są tylko niektóre wartości.

  Ten wykres pokazuje, które kombinacje rozmiaru pamięci podręcznej i jednostek SKU są prawidłowe w momencie przygotowania tego dokumentu (lipiec 2020 r.).

  | Rozmiar pamięci podręcznej | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3072 GB    | tak         | nie          | nie          |
  | 6144 GB    | tak         | tak         | nie          |
  | 12 288 GB   | tak         | tak         | tak         |
  | 24 576 GB   | nie          | tak         | tak         |
  | 49 152 GB   | nie          | nie          | tak         |

  Zapoznaj się **z sekcją** Ustawianie pojemności pamięci podręcznej na karcie instrukcji portalu, aby uzyskać ważne informacje na temat cen, przepływności i sposobu odpowiedniego rozmiaru pamięci podręcznej dla przepływu pracy.

Przykład tworzenia pamięci podręcznej:

```azurepowershell-interactive
$cacheParams = @{
  ResourceGroupName = 'doc-demo-rg'
  CacheName = 'my-cache-0619'
  Location = 'eastus'
  cacheSize = '3072'
  SubnetUri = "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default"
  Sku = 'Standard_2G'
}
New-AzHpcCache @cacheParams
```

Tworzenie pamięci podręcznej trwa kilka minut. W przypadku powodzenia polecenie create zwraca następujące dane wyjściowe:

```Output
cacheSizeGb       : 3072
health            : @{state=Healthy; statusDescription=The cache is in Running state}
id                : /subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.StorageCache/caches/my-cache-0619
location          : eastus
mountAddresses    : {10.3.0.17, 10.3.0.18, 10.3.0.19}
name              : my-cache-0619
provisioningState : Succeeded
resourceGroup     : doc-demo-rg
sku               : @{name=Standard_2G}
subnet            : /subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default
tags              :
type              : Microsoft.StorageCache/caches
upgradeStatus     : @{currentFirmwareVersion=5.3.42; firmwareUpdateDeadline=1/1/0001 12:00:00 AM; firmwareUpdateStatus=unavailable; lastFirmwareUpdate=4/1/2020 10:19:54 AM; pendingFirmwareVersion=}
```

Komunikat zawiera kilka przydatnych informacji, w tym następujące elementy:

* Adresy instalacji klienta — użyj tych adresów IP, gdy wszystko będzie gotowe do połączenia klientów z pamięcią podręczną. Przeczytaj [temat Mount the Azure HPC Cache](hpc-cache-mount.md) (Zainstaluj Azure HPC Cache), aby dowiedzieć się więcej.
* Stan uaktualnienia — po zwolnieniu aktualizacji oprogramowania ten komunikat zmieni się. Oprogramowanie pamięci [podręcznej można](hpc-cache-manage.md#upgrade-cache-software) uaktualnić ręcznie w dogodnym czasie lub jest ono stosowane automatycznie po kilku dniach.

---

## <a name="next-steps"></a>Następne kroki

Gdy pamięć podręczna pojawi się na **liście Zasoby,** możesz przejść do następnego kroku.

* [Zdefiniuj obiekty docelowe](hpc-cache-add-storage.md) magazynu, aby zapewnić pamięci podręcznej dostęp do źródeł danych.
* Jeśli używasz kluczy szyfrowania zarządzanych przez [](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) klienta, musisz autoryzować szyfrowanie Azure Key Vault na stronie przeglądu pamięci podręcznej, aby ukończyć konfigurację pamięci podręcznej. Ten krok należy wykonać przed dodaniu magazynu. Aby uzyskać szczegółowe informacje, zobacz Używanie [kluczy szyfrowania zarządzanych](customer-keys.md) przez klienta.
