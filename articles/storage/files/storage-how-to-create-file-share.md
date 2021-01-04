---
title: Tworzenie udziału plików platformy Azure
titleSuffix: Azure Files
description: Jak utworzyć udział plików platformy Azure przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 2/22/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, references_regions
ms.openlocfilehash: 705910a9e2f4ebc80a63ab22ac4edecc5ae03cd0
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724803"
---
# <a name="create-an-azure-file-share"></a>Tworzenie udziału plików platformy Azure
Aby utworzyć udział plików platformy Azure, musisz odpowiedzieć na trzy pytania dotyczące sposobu ich używania:

- **Jakie są wymagania dotyczące wydajności udziału plików platformy Azure?**  
    Azure Files oferuje standardowe udziały plików (w tym zoptymalizowane pod kątem transakcji, gorącą i chłodną udziały plików), które są hostowane na dyskach twardych na podstawie sprzętu i w warstwie Premium, które są hostowane na sprzęcie opartym na dyskach półprzewodnikowych (SSD).

- **Jakiego rozmiaru udziału plików potrzebujesz?**  
    Standardowe udziały plików mogą obejmować do 100 TiB, jednak ta funkcja nie jest domyślnie włączona. Jeśli potrzebujesz udziału plików, który jest większy niż 5 TiB, musisz włączyć funkcję dużego udziału plików dla konta magazynu. Udziały plików w warstwie Premium mogą obejmować do 100 TiB bez żadnych specjalnych ustawień, jednak są udostępniane udziały plików w warstwie Premium, a nie płatność zgodnie z rzeczywistym użyciem w przypadku standardowych udziałów plików. Oznacza to, że udostępnienie udziału plików znacznie większe niż to, co jest potrzebne, spowoduje zwiększenie łącznego kosztu magazynowania.

- **Jakie są wymagania związane z nadmiarowością udziału plików platformy Azure?**  
    Standardowe udziały plików oferują Magazyn lokalnie nadmiarowy (LRS), strefowo nadmiarowy (ZRS), Geograficznie nadmiarowy (GRS) lub strefy geograficznej (GZRS), jednak funkcja dużego udziału plików jest obsługiwana tylko lokalnie nadmiarowe i nadmiarowe udziały plików. Udziały plików w warstwie Premium nie obsługują żadnej formy nadmiarowości geograficznej.

    Udziały plików w warstwie Premium są dostępne z lokalną nadmiarowością w większości regionów, które oferują konta magazynu i nadmiarowość stref w mniejszym podzbiorze regionów. Aby dowiedzieć się, czy w Twoim regionie są obecnie dostępne udziały plików w warstwie Premium, zobacz stronę [dostępne według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=storage) na platformie Azure. Aby uzyskać informacje na temat regionów, które obsługują ZRS, zobacz [nadmiarowość usługi Azure Storage](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Aby uzyskać więcej informacji na temat tych trzech opcji, zobacz [Planowanie wdrożenia Azure Files](storage-files-planning.md).

## <a name="prerequisites"></a>Wymagania wstępne
- W tym artykule przyjęto założenie, że utworzono już subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Jeśli zamierzasz używać Azure PowerShell, [Zainstaluj najnowszą wersję](/powershell/azure/install-az-ps).
- Jeśli zamierzasz korzystać z interfejsu wiersza polecenia platformy Azure, [Zainstaluj najnowszą wersję](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu
Udziały plików platformy Azure są wdrażane na *kontach magazynu*, które są obiektami najwyższego poziomu reprezentującymi udostępnioną pulę magazynu. Ta pula magazynu może służyć do wdrażania wielu udziałów plików. 

Platforma Azure obsługuje wiele typów kont magazynu dla różnych klientów scenariuszy magazynu, które mogą mieć, ale istnieją dwa główne typy kont magazynu dla Azure Files. Typ konta magazynu, który należy utworzyć, zależy od tego, czy chcesz utworzyć standardowy udział plików, czy udział plików w warstwie Premium: 

- **Konta magazynu ogólnego przeznaczenia w wersji 2 (GPv2)**: GPv2 konta magazynu umożliwiają wdrażanie udziałów plików platformy Azure na sprzęcie opartym na dyskach standardowych/twardych. Oprócz przechowywania udziałów plików platformy Azure GPv2 konta magazynu mogą przechowywać inne zasoby magazynu, takie jak kontenery obiektów blob, kolejki lub tabele. Udziały plików można wdrażać w ramach transakcji zoptymalizowanej (domyślnej), gorąca lub chłodna.

- **FileStorage kont magazynu**: FileStorage konta magazynu umożliwiają wdrażanie udziałów plików platformy Azure na sprzęcie opartym na dyskach Premium/SSD (opartym na dysku półprzewodnikowym). Kont FileStorage można używać tylko do przechowywania udziałów plików platformy Azure. nie można wdrażać innych zasobów magazynu (kontenerów obiektów blob, kolejek, tabel itp.) w ramach konta FileStorage.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Aby utworzyć konto magazynu za pośrednictwem Azure Portal, wybierz pozycję **+ Utwórz zasób** z poziomu pulpitu nawigacyjnego. W wynikowym oknie wyszukiwania portalu Azure Marketplace Wyszukaj pozycję **konto magazynu** i wybierz wynikowe wyniki wyszukiwania. Spowoduje to wyświetlenie strony Przegląd kont magazynu; Wybierz pozycję **Utwórz** , aby kontynuować pracę z kreatorem tworzenia konta magazynu.

![Zrzut ekranu przedstawiający opcję szybkie tworzenie konta magazynu w przeglądarce](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="the-basics-section"></a>Sekcja podstawy
Pierwsza sekcja, która ma zostać ukończona, aby utworzyć konto magazynu, jest oznaczona jako **podstawy**. Zawiera wszystkie wymagane pola, aby utworzyć konto magazynu. Aby utworzyć konto magazynu GPv2, upewnij się, że przycisk radiowy **wydajność** jest ustawiony na wartość *standardowa* , a w polu listy rozwijanej **rodzaj konta** jest wybrana wartość *StorageV2 (ogólnego przeznaczenia w wersji 2)*.

![Zrzut ekranu przedstawiający przycisk radiowy wydajności z wybranym standardem i rodzajem konta z wybranym StorageV2](media/storage-how-to-create-file-share/create-storage-account-1.png)

Aby utworzyć konto magazynu FileStorage, upewnij się, że przycisk radiowy **wydajność** jest ustawiony na wartość *Premium* , a na liście rozwijanej **rodzaj konta** jest wybrana wartość *FileStorage*.

![Zrzut ekranu przedstawiający przycisk radiowy wydajności z wybranym typem Premium i rodzajem konta z wybraną pozycją FileStorage](media/storage-how-to-create-file-share/create-storage-account-2.png)

Inne podstawowe pola są niezależne od wyboru konta magazynu:
- **Subskrypcja**: subskrypcja konta magazynu do wdrożenia. 
- **Grupa zasobów**: Grupa zasobów dla konta magazynu, do której ma zostać wdrożone. Możesz utworzyć nową grupę zasobów lub użyć istniejącej grupy zasobów. Grupa zasobów to logiczny kontener przeznaczony do grupowania usług platformy Azure. Podczas tworzenia konta magazynu masz możliwość utworzenia nowej grupy zasobów lub użycia istniejącej grupy zasobów.
- **Nazwa konta magazynu**: nazwa zasobu konta magazynu, który ma zostać utworzony. Ta nazwa musi być globalnie unikatowa, ale w przeciwnym razie można mieć dowolną nazwę. Nazwa konta magazynu zostanie użyta jako nazwa serwera podczas instalowania udziału plików platformy Azure za pośrednictwem protokołu SMB.
- **Lokalizacja**: region konta magazynu, w którym ma zostać wdrożone. Może to być region skojarzony z grupą zasobów lub dowolnym innym dostępnym regionem.
- **Replikacja**: Chociaż jest to nazwa replikacji, to pole faktycznie oznacza **nadmiarowość**; jest to żądany poziom nadmiarowości: lokalna nadmiarowość (LRS), nadmiarowość stref (ZRS), nadmiarowość geograficzna (GRS) i strefa geograficzna — nadmiarowość. Ta lista rozwijana zawiera również informacje o nadmiarowości geograficznej (RA-GRS) dostępu do odczytu i dostęp do odczytu (RA-GZRS), które nie dotyczą udziałów plików platformy Azure; wszystkie udziały plików utworzone na koncie magazynu z tymi wybranymi będzie odpowiednio Geograficznie nadmiarowy lub geograficznie nadmiarowy. Niektóre opcje nadmiarowości mogą nie być dozwolone w zależności od regionu lub wybranego typu konta magazynu.
- **Warstwa dostępu do obiektów BLOB**: to pole nie ma zastosowania do Azure Files, więc można wybrać jeden z przycisków radiowych. 

> [!Important]  
> Wybór warstwy dostępu do obiektów BLOB nie ma wpływu na warstwę udziału plików.

#### <a name="the-networking-blade"></a>Blok sieć
Sekcja sieci umożliwia skonfigurowanie opcji sieciowych. Te ustawienia są opcjonalne do tworzenia konta magazynu i można je później skonfigurować w razie potrzeby. Aby uzyskać więcej informacji na temat tych opcji, zobacz [Azure Files zagadnienia dotyczące sieci](storage-files-networking-overview.md).

#### <a name="the-advanced-blade"></a>Zaawansowany blok
Sekcja zaawansowane zawiera kilka ważnych ustawień udziałów plików platformy Azure:

- **Wymagany bezpieczny transfer**: to pole wskazuje, czy konto magazynu wymaga szyfrowania podczas przesyłania komunikacji z kontem magazynu. Zaleca się pozostawienie tej funkcji, jeśli jest wymagana obsługa protokołu SMB 2,1, należy ją wyłączyć. Zalecamy wyłączenie szyfrowania, aby ograniczyć dostęp konta magazynu do sieci wirtualnej za pomocą punktów końcowych usługi i/lub prywatnych punktów końcowych.
- **Duże udziały plików**: to pole umożliwia konto magazynu dla udziałów plików o rozmiarze do 100 TIB. Włączenie tej funkcji ograniczy konto magazynu tylko do lokalnego nadmiarowego i nadmiarowego magazynu strefy. Po włączeniu konta magazynu GPv2 dla dużych udziałów plików nie można wyłączyć funkcji dużego udziału plików. W przypadku kont magazynu FileStorage (konta magazynu dla udziałów plików w warstwie Premium) nie ma tej opcji, ponieważ wszystkie udziały plików w warstwie Premium mogą być skalowane do 100 TiB. 

![Zrzut ekranu przedstawiający ważne zaawansowane ustawienia, które mają zastosowanie do Azure Files](media/storage-how-to-create-file-share/create-storage-account-3.png)

Inne ustawienia, które są dostępne na karcie Zaawansowane (obiekty blob unsoft-Delete, hierarchiczna przestrzeń nazw dla Azure Data Lake Storage Gen 2 i NFSv3 for BLOB Storage) nie mają zastosowania do Azure Files.

#### <a name="tags"></a>Tagi
Tagi to pary nazwa/wartość, które umożliwiają kategoryzowanie zasobów i wyświetlanie skonsolidowanych rozliczeń przez zastosowanie tego samego tagu dla wielu zasobów i grup zasobów. Są one opcjonalne i mogą być stosowane po utworzeniu konta magazynu.

#### <a name="review--create"></a>Przeglądanie i tworzenie
Ostatnim krokiem tworzenia konta magazynu jest wybranie przycisku **Utwórz** na karcie **Recenzja + tworzenie** . Ten przycisk nie będzie dostępny, jeśli nie wypełniono wszystkich pól wymaganych dla konta magazynu.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
Aby utworzyć konto magazynu przy użyciu programu PowerShell, użyjemy `New-AzStorageAccount` polecenia cmdlet. To polecenie cmdlet ma wiele opcji; wyświetlane są tylko wymagane opcje. Aby dowiedzieć się więcej na temat opcji zaawansowanych, zobacz [ `New-AzStorageAccount` dokumentację poleceń cmdlet](/powershell/module/az.storage/new-azstorageaccount).

Aby uprościć tworzenie konta magazynu i kolejnych udziałów plików, firma Microsoft będzie przechowywać kilka parametrów w zmiennych. Zawartość zmiennej można zastąpić dowolną wartością, jednak należy pamiętać, że nazwa konta magazynu musi być globalnie unikatowa.

```powershell
$resourceGroupName = "myResourceGroup"
$storageAccountName = "mystorageacct$(Get-Random)"
$region = "westus2"
```

Aby utworzyć konto magazynu z możliwością przechowywania standardowych udziałów plików platformy Azure, użyjemy poniższego polecenia. `-SkuName`Parametr odnosi się do odpowiedniego typu nadmiarowości. Jeśli potrzebujesz geograficznie nadmiarowego lub geograficznie nadmiarowego konta magazynu, należy również usunąć `-EnableLargeFileShare` parametr.

```powershell
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Standard_LRS `
    -Location $region `
    -Kind StorageV2 `
    -EnableLargeFileShare
```

Aby utworzyć konto magazynu z możliwością przechowywania udziałów plików platformy Azure w warstwie Premium, użyjemy poniższego polecenia. Należy zauważyć, że `-SkuName` parametr został zmieniony w taki sposób, aby obejmował zarówno `Premium` , jak i żądany poziom nadmiarowości lokalnie nadmiarowy ( `LRS` ). Ten `-Kind` parametr jest `FileStorage` zamiast tego, `StorageV2` ponieważ udziały plików w warstwie Premium muszą zostać utworzone na koncie magazynu FileStorage, a nie na koncie magazynu GPv2.

```powershell
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $region `
    -Kind FileStorage 
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Aby utworzyć konto magazynu przy użyciu interfejsu wiersza polecenia platformy Azure, zostanie użyte polecenie AZ Storage account Create. To polecenie ma wiele opcji; wyświetlane są tylko wymagane opcje. Aby dowiedzieć się więcej o opcjach zaawansowanych, zobacz [ `az storage account create` dokumentację poleceń](/cli/azure/storage/account).

Aby uprościć tworzenie konta magazynu i kolejnych udziałów plików, firma Microsoft będzie przechowywać kilka parametrów w zmiennych. Zawartość zmiennej można zastąpić dowolną wartością, jednak należy pamiętać, że nazwa konta magazynu musi być globalnie unikatowa.

```bash
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

Aby utworzyć konto magazynu z możliwością przechowywania standardowych udziałów plików platformy Azure, użyjemy poniższego polecenia. `--sku`Parametr odnosi się do odpowiedniego typu nadmiarowości. Jeśli potrzebujesz geograficznie nadmiarowego lub geograficznie nadmiarowego konta magazynu, należy również usunąć `--enable-large-file-share` parametr.

```bash
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

Aby utworzyć konto magazynu z możliwością przechowywania udziałów plików platformy Azure w warstwie Premium, użyjemy poniższego polecenia. Należy zauważyć, że `--sku` parametr został zmieniony w taki sposób, aby obejmował zarówno `Premium` , jak i żądany poziom nadmiarowości lokalnie nadmiarowy ( `LRS` ). Ten `--kind` parametr jest `FileStorage` zamiast tego, `StorageV2` ponieważ udziały plików w warstwie Premium muszą zostać utworzone na koncie magazynu FileStorage, a nie na koncie magazynu GPv2.

```bash
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind FileStorage \
    --sku Premium_LRS \
    --output none
```

---

## <a name="create-file-share"></a>Tworzenie udziału plików
Po utworzeniu konta magazynu pozostało do utworzenia udziału plików. Ten proces jest w większości taka sama niezależnie od tego, czy używany jest udział plików w warstwie Premium czy standardowy udział plików. Należy wziąć pod uwagę następujące różnice.

Standardowe udziały plików można wdrożyć w jednej z warstw standardowych: zoptymalizowane pod kątem transakcji (ustawienie domyślne), gorąca lub chłodna. Jest to warstwa udziału plików, której nie dotyczy **Warstwa dostępu do obiektów BLOB** konta magazynu (Ta właściwość odnosi się tylko do magazynu obiektów blob platformy Azure — nie odnosi się do Azure Files w ogóle). Warstwę udziału można zmienić w dowolnym momencie po jego wdrożeniu. Udziałów plików w warstwie Premium nie można bezpośrednio przekonwertować na standardowe udziały plików w żadnej warstwie Standardowa.

> [!Important]  
> Udziały plików można przenosić między warstwami w ramach typów kont magazynu GPv2 (zoptymalizowane pod kątem transakcji, gorąca i chłodna). Udział przenoszony między warstwami pociąga za sobą transakcje: przeniesienie z warstwy hotter do warstwy chłodnicy spowoduje naliczenie opłaty za transakcje zapisu warstwy dla każdego pliku w udziale, podczas gdy przejście z warstwy z chłodnicy do warstwy hotter spowoduje naliczenie opłaty za transakcje odczytu warstwy chłodnej dla każdego pliku.

Właściwość **limit przydziału** oznacza nieco inaczej między udziałami plików w warstwie Premium i standardowa:

- W przypadku standardowych udziałów plików jest to górne ograniczenie udziału plików platformy Azure, poza tym, którzy użytkownicy końcowi nie mogą go używać. Głównym celem przydziału dla standardowego udziału plików jest budżet: "nie chcę, aby ten udział plików został powiększony poza ten punkt". Jeśli nie określono limitu przydziału, standardowy udział plików może obejmować do 100 TiB (lub 5 TiB, jeśli nie ustawiono właściwości dużych udziałów plików dla konta magazynu).

- W przypadku udziałów plików w warstwie Premium limit przydziału jest przeciążony do średniego **rozmiaru**. Zarezerwowany rozmiar to kwota, za którą zostanie naliczona opłata, niezależnie od rzeczywistego użycia. Gdy udostępniasz udział plików w warstwie Premium, chcesz wziąć pod uwagę dwa czynniki: 1) przyszły wzrost udziału z perspektywy użycia miejsca i 2) liczba IOPS wymagana dla obciążenia. Każdy zainicjowany GiB uprawnia do dodatkowych operacji wejścia/wyjścia na sekundę. Aby uzyskać więcej informacji na temat planowania udziału plików w warstwie Premium, zobacz Udostępnianie [udziałów plików w warstwie Premium](understanding-billing.md#provisioned-billing).

# <a name="portal"></a>[Portal](#tab/azure-portal)
Jeśli konto magazynu zostało właśnie utworzone, możesz przejść do niego z poziomu ekranu wdrożenia, wybierając pozycję **Przejdź do zasobu**. Jeśli wcześniej utworzono konto magazynu, możesz przejść do niego za pośrednictwem grupy zasobów zawierającej ją. Na koncie magazynu wybierz kafelek z etykietą **udziały plików** (można również przejść do **udziałów plików** za pośrednictwem spisu treści dla konta magazynu).

![Zrzut ekranu przedstawiający kafelek udziały plików](media/storage-how-to-create-file-share/create-file-share-1.png)

Na liście udział plików powinny być widoczne wszystkie udziały plików utworzone wcześniej na tym koncie magazynu. pusta tabela, jeśli nie utworzono jeszcze żadnych udziałów plików. Wybierz pozycję **+ udział plików** , aby utworzyć nowy udział plików.

Nowy blok udział plików powinien pojawić się na ekranie. Wypełnij pola w bloku nowy udział plików, aby utworzyć udział plików:

- **Name**: nazwa udziału plików, który ma zostać utworzony.
- **Przydział**: przydział udziału plików dla standardowych udziałów plików; udostępniony rozmiar udziału plików dla udziałów plików w warstwie Premium.
- **Warstwy**: wybrana warstwa udziału plików. To pole jest dostępne tylko w ramach **konta magazynu ogólnego przeznaczenia (GPv2)**. Można wybrać transakcję zoptymalizowaną, gorącą lub chłodną. Warstwę udziału można zmienić w dowolnym momencie. Zalecamy wybranie najwyższej warstwy możliwej podczas migracji, w celu zminimalizowania wydatków transakcji, a następnie przełączenie na niższą warstwę w razie potrzeby po zakończeniu migracji.

Wybierz pozycję **Utwórz** , aby ukończyć tworzenie nowego udziału. Należy pamiętać, że jeśli konto magazynu znajduje się w sieci wirtualnej, nie będzie można pomyślnie utworzyć udziału plików platformy Azure, chyba że klient jest również w sieci wirtualnej. Możesz również obejść to ograniczenie do czasu w czasie za pomocą `New-AzRmStorageShare` polecenia cmdlet Azure PowerShell.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
Udział plików platformy Azure można utworzyć przy użyciu [`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare) polecenia cmdlet. W poniższych poleceniach programu PowerShell przyjęto założenie, że zostały ustawione zmienne `$resourceGroupName` i `$storageAccountName` zdefiniowane powyżej w sekcji Tworzenie konta magazynu z Azure PowerShell. 

Poniższy przykład pokazuje, jak utworzyć udział plików z jawną warstwą przy użyciu `-AccessTier` parametru. Wymaga to użycia modułu podglądu AZ. Storage, jak pokazano w przykładzie. Jeśli warstwa nie jest określona, ponieważ jest używany moduł GA AZ. Storage lub nie zostało ono uwzględnione, domyślna warstwa dla standardowych udziałów plików jest zoptymalizowana pod kątem transakcji.

> [!Important]  
> Dla udziałów plików w warstwie Premium `-QuotaGiB` parametr odnosi się do udostępnionego rozmiaru udziału plików. Udostępniony rozmiar udziału plików to kwota, za którą zostanie naliczona opłata, niezależnie od użycia. Standardowe udziały plików są rozliczane na podstawie użycia, a nie od rozmiaru aprowizacji.

```powershell
# Update the Azure storage module to use the preview version. You may need to close and 
# reopen PowerShell before running this command. If you are running PowerShell 5.1, ensure 
# the following:
# - Run the below cmdlets as an administrator.
# - Have PowerShellGet 2.2.3 or later. Uncomment the following line to check.
# Get-Module -ListAvailable -Name PowerShellGet
Remove-Module -Name Az.Storage -ErrorAction SilentlyContinue
Uninstall-Module -Name Az.Storage
Install-Module -Name Az.Storage -RequiredVersion "2.1.1-preview" -AllowClobber -AllowPrerelease 

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

> [!Note]  
> Możliwość ustawiania i zmieniania warstw za pośrednictwem programu PowerShell znajduje się w module Podgląd AZ. Storage PowerShell. Te polecenia cmdlet lub ich dane wyjściowe mogą ulec zmianie przed udostępnieniem w ogólnie dostępnym module AZ. Storage PowerShell, dlatego należy utworzyć skrypty z tym zagadnieniem.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Udział plików platformy Azure można utworzyć za pomocą [`az storage share-rm create`](/cli/azure/storage/share-rm?preserve-view=true&view=azure-cli-latest#az_storage_share_rm_create) polecenia. Przy użyciu następujących poleceń interfejsu wiersza polecenia platformy Azure przyjęto założenie, że zostały ustawione zmienne `$resourceGroupName` i `$storageAccountName` zdefiniowane powyżej w sekcji Tworzenie konta magazynu za pomocą interfejsu wiersza polecenia platformy Azure.

Funkcja tworzenia lub przenoszenia udziału plików do określonej warstwy jest dostępna w najnowszej aktualizacji interfejsu wiersza polecenia platformy Azure. Aktualizowanie interfejsu wiersza polecenia platformy Azure jest specyficzne dla używanego dystrybucji systemu operacyjnego/Linux. Aby uzyskać instrukcje dotyczące aktualizowania interfejsu wiersza polecenia platformy Azure w systemie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).

> [!Important]  
> Dla udziałów plików w warstwie Premium `--quota` parametr odnosi się do udostępnionego rozmiaru udziału plików. Udostępniony rozmiar udziału plików to kwota, za którą zostanie naliczona opłata, niezależnie od użycia. Standardowe udziały plików są rozliczane na podstawie użycia, a nie od rozmiaru aprowizacji.

```bash
shareName="myshare"

az storage share-rm create \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "TransactionOptimized" \
    --quota 1024 \
    --output none
```

> [!Note]  
> Możliwość ustawienia warstwy z `--access-tier` parametrem jest dostępna w wersji zapoznawczej w najnowszym pakiecie interfejsu wiersza polecenia platformy Azure. To polecenie lub jego dane wyjściowe mogą ulec zmianie przed oznaczeniem ich jako ogólnie dostępne, dlatego należy utworzyć skrypty z tym zagadnieniem.

---

> [!Note]  
> Nazwa udziału plików musi się składać z samych małych liter. Aby uzyskać szczegółowe informacje o nazewnictwie udziałów plików i plików, zobacz [nazewnictwo i odwoływanie się do udziałów, katalogów, plików i metadanych](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

### <a name="changing-the-tier-of-an-azure-file-share"></a>Zmienianie warstwy udziału plików platformy Azure
Udziały plików wdrożone w ramach **konta magazynu ogólnego przeznaczenia w wersji 2 (GPv2)** mogą znajdować się w warstwach zoptymalizowanych, gorąca lub chłodna. Warstwę udziału plików platformy Azure można zmienić w dowolnym momencie, z uwzględnieniem kosztów transakcji, zgodnie z powyższym opisem.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Na stronie Główne konto magazynu wybierz opcję **udziały plików**  wybierz kafelek z etykietą **udziały plików** (można także przejść do **udziałów plików** za pośrednictwem spisu treści dla konta magazynu).

![Zrzut ekranu przedstawiający kafelek udziały plików](media/storage-how-to-create-file-share/create-file-share-1.png)

Na liście udziałów plików w tabeli wybierz udział plików, dla którego chcesz zmienić warstwę. Na stronie Przegląd udziału plików wybierz z menu pozycję **Zmień warstwę** .

![Zrzut ekranu przedstawiający stronę przeglądu udziału plików z wyróżnionym przyciskiem Zmień warstwę](media/storage-how-to-create-file-share/change-tier-0.png)

W oknie dialogowym Wyniki wybierz odpowiednią warstwę: transakcja zoptymalizowana, gorąca lub chłodna.

![Zrzut ekranu okna dialogowego Zmień warstwę](media/storage-how-to-create-file-share/change-tier-1.png)

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)
Następujące polecenie cmdlet programu PowerShell zakłada, że ustawiono `$resourceGroupName` zmienne, `$storageAccountName` ,, `$shareName` zgodnie z opisem w poprzednich sekcjach tego dokumentu.

```PowerShell
# This cmdlet requires Az.Storage version 2.1.1-preview, which is installed
# in the earlier example.
Update-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -AccessTier Cool
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
Następujące polecenie interfejsu wiersza polecenia platformy Azure zakłada, że ustawiono `$resourceGroupName` `$storageAccountName` zmienne, i, zgodnie z `$shareName` opisem w poprzednich sekcjach tego dokumentu.

```bash
az storage share-rm update \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "Cool"
```

---

## <a name="next-steps"></a>Następne kroki
- [Zaplanuj wdrożenie Azure Files](storage-files-planning.md) lub [Zaplanuj wdrożenie Azure File Sync](storage-sync-files-planning.md). 
- [Omówienie sieci](storage-files-networking-overview.md).
- Łączenie i Instalowanie udziału plików w [systemach Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md)i [Linux](storage-how-to-use-files-linux.md).