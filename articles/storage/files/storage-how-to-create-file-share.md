---
title: Tworzenie udziału plików platformy Azure
titleSuffix: Azure Files
description: Jak utworzyć udział plików platformy Azure przy użyciu interfejsu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/05/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, references_regions
ms.openlocfilehash: 91f42ae671cb1696e5b088bafde8362cf19ce856
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717965"
---
# <a name="create-an-azure-file-share"></a>Tworzenie udziału plików platformy Azure
Aby utworzyć udział plików platformy Azure, musisz odpowiedzieć na trzy pytania dotyczące sposobu jego używania:

- **Jakie są wymagania dotyczące wydajności udziału plików platformy Azure?**  
    Azure Files udostępnia standardowe udziały plików, które są hostowane na sprzęcie opartym na dyskach twardych (hdd), oraz udziały plików w warstwie Premium, które są hostowane na sprzęcie opartym na dyskach PÓŁPRZEWODNIKOWYCH (opartych na dyskach SSD).

- **Jakie są wymagania dotyczące nadmiarowości udziału plików platformy Azure?**  
    Standardowe udziały plików oferują magazyn lokalnie nadmiarowy (LRS), strefowo nadmiarowy (ZRS), geograficznie nadmiarowy (GRS) lub magazyn geograficznie i strefowo nadmiarowy (GZRS), jednak funkcja dużego udziału plików jest obsługiwana tylko w lokalnie nadmiarowych i strefowo nadmiarowych udziałach plików. Udziały plików Premium nie obsługują żadnej formy nadmiarowości geograficznej.

    Udziały plików Premium są dostępne z lokalną nadmiarowością i nadmiarowością strefową w podzestawie regionów. Aby dowiedzieć się, czy udziały plików Premium są obecnie dostępne w Twoim regionie, zobacz stronę produktów [dostępnych według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=storage) dla platformy Azure. Aby uzyskać informacje o regionach, które obsługują magazyn ZRS, zobacz [Nadmiarowość usługi Azure Storage.](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

- **Jakiego rozmiaru udziału plików potrzebujesz?**  
    W przypadku kont magazynu lokalnego i strefowo nadmiarowego udziały plików platformy Azure mogą obejmować do 100 TiB, jednak w przypadku kont magazynu geograficznie i strefowo nadmiarowego udziały plików platformy Azure mogą obejmować tylko 5 TiB. 

Aby uzyskać więcej informacji na temat tych trzech opcji, zobacz Planning for an Azure Files deployment (Planowanie [wdrożenia Azure Files wdrożenia).](storage-files-planning.md)

## <a name="prerequisites"></a>Wymagania wstępne
- W tym artykule przyjęto założenie, że utworzono już subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Jeśli zamierzasz używać programu Azure PowerShell, [zainstaluj najnowszą wersję programu](/powershell/azure/install-az-ps).
- Jeśli zamierzasz używać interfejsu wiersza polecenia platformy Azure, [zainstaluj najnowszą wersję .](/cli/azure/install-azure-cli)

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu
Udziały plików platformy Azure są wdrażane na *kontach* magazynu , które są obiektami najwyższego poziomu, które reprezentują udostępnioną pulę magazynu. Ta pula magazynu może służyć do wdrażania wielu udziałów plików. 

Platforma Azure obsługuje wiele typów kont magazynu dla różnych scenariuszy magazynowania, które mogą mieć klienci, ale istnieją dwa główne typy kont magazynu dla Azure Files. Typ konta magazynu, który należy utworzyć, zależy od tego, czy chcesz utworzyć standardowy udział plików, czy udział plików w chmurze Premium: 

- Konta magazynu ogólnego przeznaczenia w wersji **2 (GPv2):** konta magazynu GPv2 umożliwiają wdrażanie udziałów plików platformy Azure na standardowym/twardym sprzęcie (opartym na dyskach twardych). Oprócz przechowywania udziałów plików platformy Azure konta magazynu GPv2 mogą przechowywać inne zasoby magazynu, takie jak kontenery obiektów blob, kolejki lub tabele. Udziały plików można wdrażać w warstwach zoptymalizowanych pod kątem transakcji (ustawienie domyślne), gorąca lub chłodna.

- **Konta magazynu FileStorage:** konta magazynu FileStorage umożliwiają wdrażanie udziałów plików platformy Azure na sprzęcie opartym na dyskach SSD w warstwie Premium/SSD. Konta FileStorage mogą być używane tylko do przechowywania udziałów plików platformy Azure; Nie można wdrożyć żadnych innych zasobów magazynu (kontenerów obiektów blob, kolejek, tabel itp.) na koncie FileStorage.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Aby utworzyć konto magazynu za pośrednictwem Azure Portal, wybierz **pozycję + Utwórz zasób z** pulpitu nawigacyjnego. W wynikowym Azure Marketplace wyszukiwania wyszukaj konto **magazynu** i wybierz wynikowy wynik wyszukiwania. Spowoduje to prowadzi do strony przeglądu dla kont magazynu; Wybierz **pozycję Utwórz,** aby przejść do kreatora tworzenia konta magazynu.

![Zrzut ekranu przedstawiający opcję szybkiego tworzenia konta magazynu w przeglądarce](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="basics"></a>Podstawy
Pierwsza sekcja, która zostanie ukończona w celu utworzenia konta magazynu, ma **etykietę Podstawowe.** Zawiera on wszystkie pola wymagane do utworzenia konta magazynu. Aby utworzyć konto magazynu GPv2, upewnij się, że  przycisk radiowy Wydajność jest ustawiony na *Standardowa,* a na liście rozwijanej Rodzaj konta wybrano opcję  *StorageV2 (ogólnego* przeznaczenia, wersja 2).

:::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-performance-standard.png" alt-text="Zrzut ekranu przedstawiający przycisk radiowy wydajności z wybraną standardowych i rodzaj konta storagev2 wybrane.":::

Aby utworzyć konto magazynu FileStorage, upewnij się, że  przycisk radiowy Wydajność  jest ustawiony na Premium, a udziały plików jest wybrany na liście rozwijanej Typ konta Premium.  

:::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-performance-premium.png" alt-text="Zrzut ekranu przedstawiający przycisk radiowy wydajności z wybranym pakietem Premium i rodzajem konta z wybraną usługą FilesTorage.":::

Pozostałe pola podstawowe są niezależne od wyboru konta magazynu:
- **Nazwa konta magazynu:** nazwa zasobu konta magazynu do utworzenia. Ta nazwa musi być globalnie unikatowa, ale w przeciwnym razie może mieć dowolną nazwę. Nazwa konta magazynu będzie używana jako nazwa serwera podczas instalacji udziału plików platformy Azure za pośrednictwem SMB.
- **Lokalizacja:** region konta magazynu, w którym ma zostać wdrożone. Może to być region skojarzony z grupą zasobów lub dowolny inny dostępny region.
- **Replikacja:** mimo że jest to replikacja oznaczona etykietą, to pole w rzeczywistości oznacza **nadmiarowość**; jest to żądany poziom nadmiarowości: lokalna nadmiarowość (LRS), nadmiarowość strefy (ZRS), nadmiarowość geograficzna (GRS) i nadmiarowość strefy geograficznej (GZRS). Ta lista rozwijana zawiera również nadmiarowość geograficzną dostępu do odczytu (RA-GRS) i nadmiarowość stref geograficznych dostępu do odczytu (RA-GZRS), które nie mają zastosowania do udziałów plików platformy Azure; Każdy udział plików utworzony na koncie magazynu z wybranymi udziałami faktycznie będzie geograficznie nadmiarowy lub geograficznie strefowo nadmiarowy. 

#### <a name="networking"></a>Sieć
Sekcja sieć umożliwia skonfigurowanie opcji sieciowych. Te ustawienia są opcjonalne w przypadku tworzenia konta magazynu i można je skonfigurować później, w razie potrzeby. Aby uzyskać więcej informacji na temat tych opcji, [zobacz Azure Files zagadnienia dotyczące sieci.](storage-files-networking-overview.md)

#### <a name="data-protection"></a>Ochrona danych
Sekcja ochrona danych umożliwia skonfigurowanie zasad usuwania nie soft-delete dla udziałów plików platformy Azure na koncie magazynu. Inne ustawienia związane z usuwaniem nie soft dla obiektów blob, kontenerów, przywracania do punktu w czasie dla kontenerów, przechowywania wersji i zestawienia zmian dotyczą tylko usługi Azure Blob Storage.

#### <a name="advanced"></a>Zaawansowany
Sekcja zaawansowana zawiera kilka ważnych ustawień udziałów plików platformy Azure:

- **Wymagany bezpieczny transfer:** to pole wskazuje, czy konto magazynu wymaga szyfrowania podczas przesyłania w celu komunikacji z kontem magazynu. Jeśli potrzebujesz obsługi SMB 2.1, musisz ją wyłączyć.

    :::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-secure-transfer.png" alt-text="Zrzut ekranu przedstawiający włączony bezpieczny transfer w ustawieniach zaawansowanych konta magazynu.":::

- **Duże udziały plików:** to pole umożliwia kontu magazynu dla udziałów plików o zakresie do 100 TiB. Włączenie tej funkcji spowoduje ograniczenie konta magazynu tylko do opcji magazynu lokalnie nadmiarowego i strefowo nadmiarowego. Po włączeniu konta magazynu GPv2 dla dużych udziałów plików nie można wyłączyć możliwości dużego udziału plików. Konta magazynu FileStorage (konta magazynu dla udziałów plików w chmurze w chmurze Premium) nie mają tej opcji, ponieważ wszystkie udziały plików w chmurze Premium mogą być skalowane do 100 TiB. 

    :::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-large-file-shares.png" alt-text="Zrzut ekranu przedstawiający ustawienie dużego udziału plików w zaawansowanym bloku konta magazynu.":::

Inne ustawienia dostępne na karcie zaawansowanej (hierarchiczna przestrzeń nazw dla usługi Azure Data Lake Storage gen 2, domyślna warstwa obiektu blob, NFSv3 dla magazynu obiektów blob itp.) nie mają zastosowania do usługi Azure Files.

> [!Important]  
> Wybranie warstwy dostępu do obiektu blob nie ma wpływu na warstwę udziału plików.

#### <a name="tags"></a>Tagi
Tagi to pary nazwa/wartość, które umożliwiają kategoryzowanie zasobów i wyświetlanie skonsolidowanych rozliczeń przez zastosowanie tego samego tagu do wielu zasobów i grup zasobów. Są one opcjonalne i można je stosować po utworzeniu konta magazynu.

#### <a name="review--create"></a>Przeglądanie i tworzenie
Ostatnim krokiem tworzenia konta magazynu jest wybranie przycisku **Utwórz** na karcie **Przeglądanie i** tworzenie. Ten przycisk nie będzie dostępny, jeśli nie zostaną wypełnione wszystkie wymagane pola dla konta magazynu.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
Aby utworzyć konto magazynu przy użyciu programu PowerShell, użyjemy `New-AzStorageAccount` polecenia cmdlet . To polecenie cmdlet ma wiele opcji; Wyświetlane są tylko wymagane opcje. Aby dowiedzieć się więcej na temat opcji zaawansowanych, zobacz [ `New-AzStorageAccount` dokumentację polecenia cmdlet](/powershell/module/az.storage/new-azstorageaccount).

Aby uprościć tworzenie konta magazynu i kolejnych udziałów plików, będziemy przechowywać kilka parametrów w zmiennych. Zawartość zmiennej można zastąpić dowolnymi wartościami, jednak należy pamiętać, że nazwa konta magazynu musi być globalnie unikatowa.

```powershell
$resourceGroupName = "myResourceGroup"
$storageAccountName = "mystorageacct$(Get-Random)"
$region = "westus2"
```

Aby utworzyć konto magazynu umożliwiające przechowywanie standardowych udziałów plików platformy Azure, użyjemy następującego polecenia. Parametr odnosi się do żądanego typu nadmiarowości. Jeśli potrzebujesz konta magazynu geograficznie nadmiarowego lub strefowo nadmiarowego geograficznie, musisz również `-SkuName` usunąć `-EnableLargeFileShare` parametr .

```powershell
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Standard_LRS `
    -Location $region `
    -Kind StorageV2 `
    -EnableLargeFileShare
```

Aby utworzyć konto magazynu umożliwiające przechowywanie udziałów plików platformy Azure w witrynie Premium, użyjemy następującego polecenia. Należy zauważyć, że parametr został zmieniony, aby obejmował zarówno poziom `-SkuName` nadmiarowości lokalnie nadmiarowej `Premium` ( `LRS` ). Parametr jest zamiast tego, ponieważ udziały plików w chmurze Premium należy utworzyć na koncie magazynu FileStorage zamiast na `-Kind` `FileStorage` koncie magazynu `StorageV2` GPv2.

```powershell
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $region `
    -Kind FileStorage 
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Aby utworzyć konto magazynu przy użyciu interfejsu wiersza polecenia platformy Azure, użyjemy polecenia az storage account create. To polecenie ma wiele opcji; Wyświetlane są tylko wymagane opcje. Aby dowiedzieć się więcej o opcjach zaawansowanych, zobacz [ `az storage account create` dokumentację polecenia](/cli/azure/storage/account).

Aby uprościć tworzenie konta magazynu i kolejnych udziałów plików, będziemy przechowywać kilka parametrów w zmiennych. Zawartość zmiennej można zastąpić dowolnymi wartościami, jednak należy pamiętać, że nazwa konta magazynu musi być globalnie unikatowa.

```azurecli
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

Aby utworzyć konto magazynu umożliwiające przechowywanie standardowych udziałów plików platformy Azure, użyjemy następującego polecenia. Parametr odnosi się do żądanego typu nadmiarowości. Jeśli potrzebujesz konta magazynu geograficznie nadmiarowego lub strefowo nadmiarowego geograficznie, musisz również `--sku` usunąć `--enable-large-file-share` parametr .

```azurecli
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

Aby utworzyć konto magazynu umożliwiające przechowywanie udziałów plików platformy Azure w witrynie Premium, użyjemy następującego polecenia. Należy zauważyć, że parametr został zmieniony, aby obejmował zarówno poziom `--sku` nadmiarowości lokalnie nadmiarowej `Premium` ( `LRS` ). Parametr jest zamiast dlatego, że udziały plików w chmurze w chmurze Premium należy utworzyć na koncie `--kind` `FileStorage` magazynu `StorageV2` FileStorage, a nie na koncie magazynu GPv2.

```azurecli
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind FileStorage \
    --sku Premium_LRS \
    --output none
```

---

## <a name="create-a-file-share"></a>Tworzenie udziału plików
Po utworzeniu konta magazynu pozostało tylko utworzenie udziału plików. Ten proces jest w większości taki sam niezależnie od tego, czy używasz udziału plików w chmurze Premium, czy standardowego udziału plików. Należy wziąć pod uwagę następujące różnice.

Udziały plików w warstwie Standardowa można wdrożyć w jednej z warstw standardowych: zoptymalizowane pod kątem transakcji (ustawienie domyślne), gorąca lub chłodna. Jest to warstwa dla udziału plików, na która nie ma wpływu warstwa dostępu do obiektów **blob** konta magazynu (ta właściwość odnosi się tylko do usługi Azure Blob Storage — w ogóle nie Azure Files z magazynem). Warstwę udziału można zmienić w dowolnym momencie po jego wdrożeniu. Udziałów plików w warstwie Premium nie można bezpośrednio konwertować na dowolną warstwę Standardowa.

> [!Important]  
> Udziały plików można przenosić między warstwami w ramach typów kont magazynu GPv2 (zoptymalizowanych pod kątem transakcji, gorących i chłodnych). Przenoszenie udziałów między warstwami powoduje naliczanie transakcji: przejście z warstwy chłodniejszej do chłodniejszej spowoduje naliczenie opłaty za transakcję zapisu w chłodniejszej warstwie dla każdego pliku w udziału, podczas gdy przejście z chłodniejszej warstwy do warstwy cieplej spowoduje naliczenie opłaty za transakcję odczytu dla każdego pliku udziału.

Właściwość **quota** oznacza nieco inną różnicę między udziałami plików w standardowych i standardowych wersjach:

- W przypadku standardowych udziałów plików jest to górna granica udziału plików platformy Azure, po której użytkownicy końcowi nie mogą przejść. Jeśli limit przydziału nie zostanie określony, standardowy udział plików może obejmować do 100 TiB (lub 5 TiB, jeśli właściwość dużych udziałów plików nie jest ustawiona dla konta magazynu).

- W przypadku udziałów plików w premium limit przydziału oznacza **aprowizowany rozmiar**. Aprowizowany rozmiar to kwota, za którą będą naliczane opłaty niezależnie od rzeczywistego użycia. Aby uzyskać więcej informacji na temat planowania udziału plików w wersji Premium, zobacz [aprowizowanie udziałów plików Premium.](understanding-billing.md#provisioned-model)

# <a name="portal"></a>[Portal](#tab/azure-portal)
Jeśli konto magazynu właśnie utworzono, możesz przejść do niego z ekranu wdrażania, wybierając pozycję **Przejdź do zasobu.** Na koncie magazynu wybierz pozycję **Udziały plików** w spisie treści dla konta magazynu.

Na liście udziałów plików powinny być w tym przypadku wszystkie udziały plików utworzone wcześniej na tym koncie magazynu. pusta tabela, jeśli nie utworzono jeszcze żadnych udziałów plików. Wybierz **pozycję + Udział plików,** aby utworzyć nowy udział plików.

Na ekranie powinien zostać wyświetlony blok nowego udziału plików. Wypełnij pola w bloku nowego udziału plików, aby utworzyć udział plików:

- **Nazwa:** nazwa udziału plików do utworzenia.
- **Limit** przydziału: limit przydziału udziału plików dla standardowych udziałów plików; aprowizowany rozmiar udziału plików dla udziałów plików Premium.
- **Warstwy:** wybrana warstwa udziału plików. To pole jest dostępne tylko na koncie **magazynu ogólnego przeznaczenia (GPv2).** Możesz wybrać pozycję Zoptymalizowane pod kątem transakcji, Gorąca lub Chłodna. Warstwę udziału można zmienić w dowolnym momencie. Zalecamy wybranie warstwy najgorętszej możliwej podczas migracji, aby zminimalizować wydatki związane z transakcjami, a następnie przełączenie do niższej warstwy w razie potrzeby po zakończeniu migracji.

Wybierz **pozycję Utwórz,** aby zakończyć tworzenie nowego udziału.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
Udział plików platformy Azure można utworzyć za pomocą [`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare) polecenia cmdlet . W poniższych poleceniach programu PowerShell założono, że zostały ustawione zmienne i zgodnie z powyższymi definicjami w sekcji tworzenie konta magazynu przy `$resourceGroupName` `$storageAccountName` Azure PowerShell magazynu. 

W poniższym przykładzie pokazano tworzenie udziału plików z jawną warstwą przy użyciu `-AccessTier` parametru . Wymaga to użycia modułu Az.Storage w wersji zapoznawczej, jak wskazano w przykładzie. Jeśli warstwa nie jest określona, ponieważ używasz modułu Az.Storage ga ga lub nie dodano tego polecenia, warstwa domyślna dla standardowych udziałów plików jest zoptymalizowana pod kątem transakcji.

> [!Important]  
> W przypadku udziałów plików w premium parametr odnosi się do `-QuotaGiB` aprowizowanych rozmiarów udziału plików. Aprowizowany rozmiar udziału plików to kwota, za którą będą naliczane opłaty, niezależnie od użycia. Standardowe udziały plików są rozliczane na podstawie użycia, a nie aprowizowanych rozmiarów.

```powershell
# Assuming $resourceGroupName and $storageAccountName from earlier in this document have already
# been populated. The access tier parameter may be TransactionOptimized, Hot, or Cool for GPv2 
# storage accounts. Standard tiers are only available in standard storage accounts. 
$shareName = "myshare"

New-AzRmStorageShare `
        -ResourceGroupName $resourceGroupName `
        -StorageAccountName $storageAccountName `
        -Name $shareName `
        -AccessTier TransactionOptimized `
        -QuotaGiB 1024 | `
    Out-Null
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Udział plików platformy Azure można utworzyć za pomocą [`az storage share-rm create`](/cli/azure/storage/share-rm#az_storage_share_rm_create) polecenia . Następujące polecenia interfejsu wiersza polecenia platformy Azure zakładają, że zmienne zostały ustawione i zgodnie z powyższymi definicjami w sekcji tworzenie konta magazynu `$resourceGroupName` `$storageAccountName` za pomocą interfejsu wiersza polecenia platformy Azure.

> [!Important]  
> W przypadku udziałów plików w premium parametr odnosi się `--quota` do aprowizowanych rozmiarów udziału plików. Aprowizowany rozmiar udziału plików to kwota, za którą będą naliczane opłaty, niezależnie od użycia. Standardowe udziały plików są rozliczane na podstawie użycia, a nie aprowizowanych rozmiarów.

```azurecli
shareName="myshare"

az storage share-rm create \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "TransactionOptimized" \
    --quota 1024 \
    --output none
```

---

> [!Note]  
> Nazwa udziału plików musi się składać z samych małych liter. Aby uzyskać szczegółowe informacje na temat nazewnictwa udziałów plików i plików, zobacz [Naming and referencing shares, directories, files, and metadata](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)(Nazywanie i odwoływanie się do udziałów, katalogów, plików i metadanych).

### <a name="change-the-tier-of-an-azure-file-share"></a>Zmienianie warstwy udziału plików platformy Azure
Udziały plików wdrożone na koncie magazynu ogólnego przeznaczenia w wersji **2 (GPv2)** mogą być w warstwach zoptymalizowanych pod kątem transakcji, gorącej lub chłodnej. Warstwę udziału plików platformy Azure można zmienić w dowolnym momencie, z zastrzeżeniem kosztów transakcji, jak opisano powyżej.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Na stronie głównego konta  magazynu wybierz pozycję Udziały plików i wybierz  kafelek z etykietą **Udziały** plików (możesz również przejść do opcji Udziały plików za pośrednictwem spisu treści dla konta magazynu).

:::image type="content" source="media/storage-files-quick-create-use-windows/click-files.png" alt-text="Zrzut ekranu przedstawiający blok konta magazynu z wybranymi udziałami plików.":::

Na liście tabel udziałów plików wybierz udział plików, dla którego chcesz zmienić warstwę. Na stronie przeglądu udziału plików wybierz z menu pozycję Zmień **warstwę.**

![Zrzut ekranu przedstawiający stronę przeglądu udziału plików z wyróżniony przyciskiem zmień warstwę](media/storage-how-to-create-file-share/change-tier-0.png)

W wynikowym oknie dialogowym wybierz żądaną warstwę: zoptymalizowaną pod kątem transakcji, gorącą lub chłodną.

![Zrzut ekranu przedstawiający okno dialogowe zmiany warstwy](media/storage-how-to-create-file-share/change-tier-1.png)

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
Następujące polecenie cmdlet programu PowerShell zakłada, że ustawiono zmienne , , zgodnie z opisem we `$resourceGroupName` `$storageAccountName` wcześniejszych `$shareName` sekcjach tego dokumentu.

```PowerShell
Update-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -AccessTier Cool
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Następujące polecenie interfejsu wiersza polecenia platformy Azure zakłada, że ustawiono zmienne , i zgodnie z opisem we wcześniejszych `$resourceGroupName` `$storageAccountName` `$shareName` sekcjach tego dokumentu.

```azurecli
az storage share-rm update \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "Cool"
```

---

## <a name="next-steps"></a>Następne kroki
- [Zaplanuj wdrożenie usługi Azure Files](storage-files-planning.md) lub [Zaplanuj wdrożenie usługi Azure File Sync](storage-sync-files-planning.md). 
- [Omówienie sieci.](storage-files-networking-overview.md)
- Połącz i zainstaluj udział plików w [systemach Windows,](storage-how-to-use-files-windows.md) [macOS](storage-how-to-use-files-mac.md)i [Linux.](storage-how-to-use-files-linux.md)