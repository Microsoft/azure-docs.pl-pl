---
title: Tworzenie pamięci podręcznej platformy Azure HPC
description: Jak utworzyć wystąpienie pamięci podręcznej platformy Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 867cfa1321106c24354b29ea803a4fb914a6778d
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92341384"
---
# <a name="create-an-azure-hpc-cache"></a>Tworzenie pamięci podręcznej platformy Azure HPC

Użyj Azure Portal lub interfejsu wiersza polecenia platformy Azure, aby utworzyć pamięć podręczną.

![zrzut ekranu przedstawiający przegląd pamięci podręcznej w Azure Portal z przyciskiem Utwórz u dołu](media/hpc-cache-home-page.png)

Kliknij poniższy obraz, aby obejrzeć [film wideo](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) przedstawiający tworzenie pamięci podręcznej i Dodawanie miejsca docelowego magazynu.

[![Miniatura wideo: Azure HPC cache: Setup (kliknij, aby odwiedzić stronę wideo)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="portal"></a>[Portal](#tab/azure-portal)

## <a name="define-basic-details"></a>Definiuj podstawowe szczegóły

![zrzut ekranu strony szczegółów projektu w Azure Portal](media/hpc-cache-create-basics.png)

W obszarze **szczegóły projektu**wybierz subskrypcję i grupę zasobów, w której będzie hostowana pamięć podręczna.

W obszarze **Szczegóły usługi**Ustaw nazwę pamięci podręcznej i inne atrybuty:

* Lokalizacja — wybierz jeden z [obsługiwanych regionów](hpc-cache-overview.md#region-availability).
* Sieć wirtualna — możesz wybrać istniejącą lub utworzyć nową sieć wirtualną.
* Podsieć — wybierz lub Utwórz podsieć z co najmniej 64 adresami IP (/24). Ta podsieć musi być używana tylko dla tego wystąpienia pamięci podręcznej platformy Azure HPC.

## <a name="set-cache-capacity"></a>Ustawianie pojemności pamięci podręcznej
<!-- referenced from GUI - update aka.ms link if you change this header text -->

Na stronie **pamięć podręczna** należy ustawić pojemność pamięci podręcznej. Wartości ustawione w tym miejscu określają ilość danych, które mogą być przechowywane w pamięci podręcznej i jak szybko mogą obsłużyć żądania klientów.

Pojemność ma także wpływ na koszt pamięci podręcznej.

Wybierz pojemność, ustawiając te dwie wartości:

* Maksymalna szybkość transferu danych w pamięci podręcznej (przepływność), w GB/s
* Ilość miejsca do magazynowania przydzieloną dla danych w pamięci podręcznej w TB

Wybierz jedną z dostępnych wartości przepływności i rozmiar pamięci podręcznej.

Należy pamiętać, że Rzeczywista szybkość transferu danych zależy od obciążenia, szybkości sieci i typu miejsca docelowego magazynu. Wybrane wartości ustawiają maksymalną przepływność dla całego systemu pamięci podręcznej, ale niektóre z nich są używane do zadań dodatkowych. Na przykład jeśli klient żąda pliku, który nie jest już przechowywany w pamięci podręcznej, lub jeśli plik jest oznaczony jako przestarzały, pamięć podręczna będzie używać niektórych przepływności do pobrania z magazynu zaplecza.

Pamięć podręczna platformy Azure HPC zarządza, które pliki są buforowane i wstępnie załadowane, aby zmaksymalizować szybkość trafień pamięci podręcznej. Zawartość pamięci podręcznej jest stale oceniana, a pliki są przenoszone do magazynu długoterminowego, gdy są one rzadziej używane. Wybierz rozmiar pamięci podręcznej, który może wygodnie przechowywać aktywny zestaw plików roboczych, a także dodatkowe miejsce dla metadanych i innych obciążeń.

![zrzut ekranu strony zmiany wielkości pamięci podręcznej](media/hpc-cache-create-capacity.png)

## <a name="enable-azure-key-vault-encryption-optional"></a>Włącz szyfrowanie Azure Key Vault (opcjonalnie)

Jeśli pamięć podręczna znajduje się w regionie, który obsługuje zarządzane przez klienta klucze szyfrowania, zostanie wyświetlona strona **klucze szyfrowania dysku** między kartami **pamięci podręcznej** a **tagami** . Przeczytaj [dostępność regionalną](hpc-cache-overview.md#region-availability) , aby dowiedzieć się więcej o obsłudze regionów.

Jeśli chcesz zarządzać kluczami szyfrowania używanymi w magazynie pamięci podręcznej, podaj informacje o Azure Key Vault na stronie **kluczy szyfrowania dysku** . Magazyn kluczy musi znajdować się w tym samym regionie i w tej samej subskrypcji co pamięć podręczna.

Możesz pominąć tę sekcję, jeśli nie są potrzebne klucze zarządzane przez klienta. Platforma Azure domyślnie szyfruje dane za pomocą kluczy zarządzanych przez firmę Microsoft. Aby dowiedzieć się więcej, przeczytaj artykuł [szyfrowanie usługi Azure Storage](../storage/common/storage-service-encryption.md) .

> [!NOTE]
>
> * Po utworzeniu pamięci podręcznej nie można zmienić między kluczami zarządzanymi przez firmę Microsoft i kluczami zarządzanymi przez klienta.
> * Po utworzeniu pamięci podręcznej należy autoryzować ją w celu uzyskania dostępu do magazynu kluczy. Kliknij przycisk **Włącz szyfrowanie** na stronie **Przegląd** pamięci podręcznej, aby włączyć szyfrowanie. Wykonaj ten krok w ciągu 90 minut od utworzenia pamięci podręcznej.
> * Dyski pamięci podręcznej są tworzone po tej autoryzacji. Oznacza to, że początkowa godzina tworzenia pamięci podręcznej jest krótka, ale pamięć podręczna nie będzie gotowa do użycia przez dziesięć minut lub dłużej po udzieleniu zgody na dostęp.

Aby uzyskać pełny opis procesu szyfrowania klucza zarządzanego przez klienta, przeczytaj artykuł [Korzystanie z kluczy szyfrowania zarządzanych przez klienta dla pamięci podręcznej platformy Azure HPC](customer-keys.md).

![zrzut ekranu strony kluczy szyfrowania z wybraną opcją "zarządzane przez klienta" i pola magazynu kluczy pokazujące](media/create-encryption.png)

Wybierz pozycję **zarządzane przez klienta** , aby wybrać szyfrowanie klucza zarządzanego przez klienta. Zostaną wyświetlone pola Specyfikacja magazynu kluczy. Wybierz Azure Key Vault, którego chcesz użyć, a następnie wybierz klucz i wersję, która ma być używana dla tej pamięci podręcznej. Klucz musi być 2048-bitowym kluczem RSA. Na tej stronie można utworzyć nowy magazyn kluczy, klucz lub wersję klucza.

Po utworzeniu pamięci podręcznej należy autoryzować ją do korzystania z usługi magazynu kluczy. Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem autoryzuj Azure Key Vault szyfrowanie z pamięci podręcznej](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache)

## <a name="add-resource-tags-optional"></a>Dodaj Tagi zasobów (opcjonalnie)

Na stronie **Tagi** można dodać [Tagi zasobów](../azure-resource-manager/management/tag-resources.md) do wystąpienia pamięci podręcznej platformy Azure HPC.

## <a name="finish-creating-the-cache"></a>Zakończ tworzenie pamięci podręcznej

Po skonfigurowaniu nowej pamięci podręcznej kliknij kartę **Recenzja + tworzenie** . Portal sprawdza poprawność wybranych opcji i pozwala przejrzeć wybrane opcje. Jeśli wszystko jest poprawne, kliknij przycisk **Utwórz**.

Tworzenie pamięci podręcznej zajmie około 10 minut. Postęp można śledzić w panelu powiadomienia Azure Portal.

![zrzut ekranu przedstawiający tworzenie stron "wdrażanie" i "powiadomienia" w portalu](media/hpc-cache-deploy-status.png)

Po zakończeniu tworzenia zostanie wyświetlone powiadomienie z linkiem do nowego wystąpienia usługi Azure HPC cache, a pamięć podręczna zostanie wyświetlona na liście **zasobów** subskrypcji.

![zrzut ekranu wystąpienia pamięci podręcznej platformy Azure HPC w Azure Portal](media/hpc-cache-new-overview.png)

> [!NOTE]
> Jeśli pamięć podręczna korzysta z kluczy szyfrowania zarządzanych przez klienta, pamięć podręczna może pojawić się na liście zasobów, zanim stan wdrożenia zostanie zmieniony na ukończone. Gdy tylko stan pamięci podręcznej **oczekuje na klucz** , możesz [autoryzować go](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) do korzystania z magazynu kluczy.

## <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

## <a name="create-the-cache-with-azure-cli"></a>Tworzenie pamięci podręcznej przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

> [!NOTE]
> Interfejs wiersza polecenia platformy Azure aktualnie nie obsługuje tworzenia pamięci podręcznej z użyciem kluczy szyfrowania zarządzanych przez klienta. Użyj witryny Azure Portal.

Użyj polecenia [AZ HPC-cache Create](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-create) , aby utworzyć nową pamięć podręczną platformy Azure HPC.

Podaj następujące wartości:

* Nazwa grupy zasobów pamięci podręcznej
* Nazwa pamięci podręcznej
* Region platformy Azure
* Podsieć pamięci podręcznej w tym formacie:

  ``--subnet "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/sub
nets/<cache_subnet_name>"``

  Podsieć pamięci podręcznej wymaga co najmniej 64 adresów IP (/24) i nie może być żadnym z innych zasobów.

* Pojemność pamięci podręcznej. Dwie wartości ustawiają maksymalną przepływność pamięci podręcznej platformy Azure HPC:

  * Rozmiar pamięci podręcznej (w GB)
  * Jednostka SKU maszyn wirtualnych używanych w infrastrukturze pamięci podręcznej

  [AZ HPC-cache SKU list](/cli/azure/ext/hpc-cache/hpc-cache/skus) zawiera dostępne jednostki SKU i prawidłowe opcje rozmiaru pamięci podręcznej dla każdego z nich. Opcje rozmiaru pamięci podręcznej mieszczą się w zakresie od 3 TB do 48 TB, ale obsługiwane są tylko niektóre wartości.

  Ten wykres pokazuje, który rozmiar pamięci podręcznej i kombinacje jednostek SKU są prawidłowe w momencie przygotowywania tego dokumentu (lipiec 2020).

  | Rozmiar pamięci podręcznej | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3072 GB    | tak         | nie          | nie          |
  | 6144 GB    | tak         | tak         | nie          |
  | 12288 GB   | tak         | tak         | tak         |
  | 24576 GB   | nie          | tak         | tak         |
  | 49152 GB   | nie          | nie          | tak         |

  Zapoznaj się z sekcją **Ustawianie pojemności pamięci podręcznej** na karcie instrukcje portalu, aby uzyskać ważne informacje o cenach, przepływności i sposobie odpowiedniej wielkości pamięci podręcznej dla przepływu pracy.

Przykład tworzenia pamięci podręcznej:

```azurecli
az hpc-cache create --resource-group doc-demo-rg --name my-cache-0619 \
    --location "eastus" --cache-size-gb "3072" \
    --subnet "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default" \
    --sku-name "Standard_2G"
```

Tworzenie pamięci podręcznej trwa kilka minut. Po powodzeniu polecenie Create zwraca dane wyjściowe podobne do następujących:

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

Komunikat zawiera pewne przydatne informacje, w tym następujące elementy:

* Adresy instalacji klienta — Użyj tych adresów IP, gdy wszystko jest gotowe do łączenia klientów z pamięcią podręczną. Przeczytaj temat [Instalowanie pamięci podręcznej platformy Azure HPC,](hpc-cache-mount.md) aby dowiedzieć się więcej.
* Stan uaktualnienia — po wydaniu aktualizacji oprogramowania ten komunikat zostanie zmieniony. [Oprogramowanie pamięci podręcznej można uaktualnić](hpc-cache-manage.md#upgrade-cache-software) ręcznie w dogodnym czasie lub będzie stosowane automatycznie po kilku dniach.

## <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

> [!CAUTION]
> Moduł programu PowerShell AZ. HPCCache jest obecnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza jest dostępna bez umowy dotyczącej poziomu usług. Nie jest ona zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane lub mogą mieć ograniczone możliwości. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Wymagania

Jeśli zdecydujesz się używać programu PowerShell lokalnie, ten artykuł będzie wymagał instalacji modułu AZ PowerShell i nawiązania połączenia z kontem platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Aby uzyskać więcej informacji na temat instalowania modułu AZ PowerShell module, zobacz [Install Azure PowerShell](/powershell/azure/install-az-ps). Jeśli zdecydujesz się używać Cloud Shell, zobacz [omówienie Azure Cloud Shell](../cloud-shell/overview.md) , aby uzyskać więcej informacji.

> [!IMPORTANT]
> Mimo że moduł **AZ. HPCCache** PowerShell jest w wersji zapoznawczej, należy go zainstalować oddzielnie przy użyciu `Install-Module` polecenia cmdlet. Po ogólnym udostępnieniu tego modułu programu PowerShell będzie on częścią przyszłych wydań modułu AZ PowerShell i dostępne natywnie z poziomu Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.HPCCache
```

## <a name="create-the-cache-with-azure-powershell"></a>Tworzenie pamięci podręcznej za pomocą Azure PowerShell

> [!NOTE]
> Azure PowerShell obecnie nie obsługuje tworzenia pamięci podręcznej z użyciem kluczy szyfrowania zarządzanych przez klienta. Użyj witryny Azure Portal.

Użyj polecenia cmdlet [New-AzHpcCache](/powershell/module/az.hpccache/new-azhpccache) , aby utworzyć nową pamięć podręczną platformy Azure HPC.

Podaj następujące wartości:

* Nazwa grupy zasobów pamięci podręcznej
* Nazwa pamięci podręcznej
* Region platformy Azure
* Podsieć pamięci podręcznej w tym formacie:

  `-SubnetUri "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/sub
nets/<cache_subnet_name>"`

  Podsieć pamięci podręcznej wymaga co najmniej 64 adresów IP (/24) i nie może być żadnym z innych zasobów.

* Pojemność pamięci podręcznej. Dwie wartości ustawiają maksymalną przepływność pamięci podręcznej platformy Azure HPC:

  * Rozmiar pamięci podręcznej (w GB)
  * Jednostka SKU maszyn wirtualnych używanych w infrastrukturze pamięci podręcznej

  [Get-AzHpcCacheSku](/powershell/module/az.hpccache/get-azhpccachesku) wyświetla dostępne jednostki SKU i prawidłowe opcje rozmiaru pamięci podręcznej dla każdego z nich. Opcje rozmiaru pamięci podręcznej mieszczą się w zakresie od 3 TB do 48 TB, ale obsługiwane są tylko niektóre wartości.

  Ten wykres pokazuje, który rozmiar pamięci podręcznej i kombinacje jednostek SKU są prawidłowe w momencie przygotowywania tego dokumentu (lipiec 2020).

  | Rozmiar pamięci podręcznej | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3072 GB    | tak         | nie          | nie          |
  | 6144 GB    | tak         | tak         | nie          |
  | 12 288 GB   | tak         | tak         | tak         |
  | 24 576 GB   | nie          | tak         | tak         |
  | 49 152 GB   | nie          | nie          | tak         |

  Zapoznaj się z sekcją **Ustawianie pojemności pamięci podręcznej** na karcie instrukcje portalu, aby uzyskać ważne informacje o cenach, przepływności i sposobie odpowiedniej wielkości pamięci podręcznej dla przepływu pracy.

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

Tworzenie pamięci podręcznej trwa kilka minut. Po powodzeniu polecenie CREATE zwróci następujące dane wyjściowe:

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

Komunikat zawiera pewne przydatne informacje, w tym następujące elementy:

* Adresy instalacji klienta — Użyj tych adresów IP, gdy wszystko jest gotowe do łączenia klientów z pamięcią podręczną. Przeczytaj temat [Instalowanie pamięci podręcznej platformy Azure HPC,](hpc-cache-mount.md) aby dowiedzieć się więcej.
* Stan uaktualnienia — po wydaniu aktualizacji oprogramowania ten komunikat zostanie zmieniony. [Oprogramowanie pamięci podręcznej można uaktualnić](hpc-cache-manage.md#upgrade-cache-software) ręcznie w dogodnym czasie lub jest stosowane automatycznie po kilku dniach.

---

## <a name="next-steps"></a>Następne kroki

Gdy pamięć podręczna zostanie wyświetlona na liście **zasobów** , możesz przejść do następnego kroku.

* [Zdefiniuj cele magazynu](hpc-cache-add-storage.md) , aby zapewnić dostęp do pamięci podręcznej do źródeł danych.
* W przypadku korzystania z kluczy szyfrowania zarządzanych przez klienta należy [autoryzować Azure Key Vault szyfrowanie](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) z poziomu strony Przegląd pamięci podręcznej, aby ukończyć konfigurację pamięci podręcznej. Ten krok należy wykonać, aby można było dodać magazyn. Aby uzyskać szczegółowe informacje, przeczytaj artykuł [Używanie kluczy szyfrowania zarządzanych przez klienta](customer-keys.md) .