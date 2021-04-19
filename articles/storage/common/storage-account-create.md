---
title: Tworzenie konta magazynu
titleSuffix: Azure Storage
description: Dowiedz się, jak utworzyć konto magazynu do przechowywania obiektów blob, plików, kolejek i tabel. Konto usługi Azure Storage zapewnia unikatową przestrzeń nazw w Microsoft Azure do odczytywania i zapisywania danych.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/19/2021
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: cb5caeb7f75834a317b222392c6e827185cfac00
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714360"
---
# <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Konto usługi Azure Storage zawiera wszystkie obiekty danych usługi Azure Storage: obiekty blob, pliki, kolejki i tabele. Konto magazynu zapewnia unikatową przestrzeń nazw dla danych usługi Azure Storage, która jest dostępna z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. Aby uzyskać więcej informacji na temat kont usługi Azure Storage, zobacz [Omówienie konta magazynu](storage-account-overview.md).

Z tego artykułu dowiesz się, jak utworzyć konto magazynu przy użyciu interfejsu [Azure Portal,](https://portal.azure.com/) [Azure PowerShell,](/powershell/azure/)interfejsu wiersza polecenia platformy [Azure](/cli/azure)lub szablonu [Azure Resource Manager magazynu.](../../azure-resource-manager/management/overview.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

# <a name="portal"></a>[Portal](#tab/azure-portal)

Brak.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby utworzyć konto usługi Azure Storage przy użyciu programu PowerShell, upewnij się, że zainstalowano moduł [Az programu PowerShell](https://www.powershellgallery.com/packages/Az)w wersji 0.7 lub nowszej. Aby uzyskać więcej informacji, [zobacz Introducing the Azure PowerShell Az module](/powershell/azure/new-azureps-module-az)(Wprowadzenie Azure PowerShell Az).

Aby znaleźć bieżącą wersję, uruchom następujące polecenie:

```powershell
Get-InstalledModule -Name "Az"
```

Aby zainstalować lub uaktualnić Azure PowerShell, zobacz [Instalowanie Azure PowerShell modułu](/powershell/azure/install-az-ps).

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Możesz zalogować się do platformy Azure i uruchomić polecenia interfejsu wiersza polecenia platformy Azure na jeden z dwóch sposobów:

- Polecenia interfejsu wiersza polecenia można uruchamiać z poziomu Azure Portal, w Azure Cloud Shell.
- Interfejs wiersza polecenia można zainstalować i uruchamiać polecenia interfejsu wiersza polecenia lokalnie.

### <a name="use-azure-cloud-shell"></a>Używanie usługi Azure Cloud Shell

Usługa Azure Cloud Shell jest bezpłatną powłoką Bash, którą można uruchamiać bezpośrednio w witrynie Azure Portal. Interfejs wiersza polecenia platformy Azure jest wstępnie zainstalowany i skonfigurowany do używania z Twoim kontem. Kliknij przycisk **Cloud Shell** w menu w prawym górnym rogu strony Azure Portal:

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Przycisk uruchamia interaktywną powłokę, która umożliwia uruchamianie kroków opisanych w tym artykule z instrukcjami:

[![Zrzut ekranu przedstawiający Cloud Shell witryny Azure Portal](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Instalowanie interfejsu wiersza polecenia lokalnie

Interfejs wiersza polecenia platformy Azure możesz również zainstalować i używać lokalnie. Przykłady w tym artykule wymagają interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom `az --version` , aby znaleźć zainstalowaną wersję. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

# <a name="template"></a>[Szablon](#tab/template)

Brak.

---

Następnie zaloguj się do platformy Azure.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia i postępuj zgodnie z `Connect-AzAccount` instrukcjami na ekranie, aby się uwierzytelnić.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uruchomić Azure Cloud Shell, zaloguj się do [Azure Portal](https://portal.azure.com).

Aby zalogować się do lokalnej instalacji interfejsu wiersza polecenia, uruchom [polecenie az login:](/cli/azure/reference-index#az-login)

```azurecli-interactive
az login
```

# <a name="template"></a>[Szablon](#tab/template)

Nie dotyczy

---

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Konto magazynu jest zasobem Azure Resource Manager zasobów. Resource Manager to usługa wdrażania i zarządzania dla platformy Azure. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/management/overview.md).

Każdy Resource Manager zasobów, w tym konto usługi Azure Storage, musi należeć do grupy zasobów platformy Azure. Grupa zasobów to logiczny kontener przeznaczony do grupowania usług platformy Azure. Podczas tworzenia konta magazynu masz możliwość utworzenia nowej grupy zasobów lub użycia istniejącej grupy zasobów. Ten sposób pokazuje, jak utworzyć nową grupę zasobów.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby utworzyć konto usługi Azure Storage przy użyciu Azure Portal, wykonaj następujące kroki:

1. W menu portalu po lewej stronie wybierz **pozycję Konta magazynu,** aby wyświetlić listę kont magazynu.
1. Na stronie **Konta magazynu** wybierz pozycję **Nowy.**

Opcje nowego konta magazynu są podzielone na karty na **stronie Tworzenie konta** magazynu. W poniższych sekcjach opisano poszczególne karty i ich opcje.

### <a name="basics-tab"></a>Karta Podstawowe

Na karcie **Podstawowe** podaj podstawowe informacje dotyczące konta magazynu. Po ukończeniu  pracy z kartą Podstawowe możesz dodatkowo dostosować nowe konto magazynu, ustawiając opcje na innych kartach. Możesz też wybrać pozycję Przejrzyj **i** utwórz, aby zaakceptować opcje domyślne, a następnie przejść do weryfikacji i utworzenia konta.

W poniższej tabeli opisano pola na **karcie Podstawy.**

| Sekcja | Pole | Wymagane lub opcjonalne | Opis |
|--|--|--|--|
| Szczegóły projektu | Subskrypcja | Wymagane | Wybierz subskrypcję nowego konta magazynu. |
| Szczegóły projektu | Grupa zasobów | Wymagane | Utwórz nową grupę zasobów dla tego konta magazynu lub wybierz istniejącą. Aby uzyskać więcej informacji, zobacz [Grupy zasobów](../../azure-resource-manager/management/overview.md#resource-groups). |
| Szczegóły wystąpienia | Nazwa konta magazynu | Wymagane | Wybierz unikatową nazwę konta magazynu. Nazwy kont usługi Storage muszą mieć długość od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery. |
| Szczegóły wystąpienia | Region (Region) | Wymagane | Wybierz odpowiedni region dla konta magazynu. Aby uzyskać więcej informacji, zobacz Regions and Strefy dostępności in Azure (Regiony i Strefy dostępności [platformie Azure).](../../availability-zones/az-overview.md)<br /><br />Nie wszystkie regiony są obsługiwane dla wszystkich typów kont magazynu lub konfiguracji nadmiarowości. Aby uzyskać więcej informacji, zobacz [Nadmiarowość usługi Azure Storage](storage-redundancy.md).<br /><br />Wybór regionu może mieć wpływ na rozliczenia. Aby uzyskać więcej informacji, zobacz [Rozliczenia konta magazynu.](storage-account-overview.md#storage-account-billing) |
| Szczegóły wystąpienia | Wydajność | Wymagane | Wybierz **pozycję Standardowa** wydajność dla kont magazynu ogólnego przeznaczenia w wersji 2 (ustawienie domyślne). Ten typ konta jest zalecany przez firmę Microsoft w przypadku większości scenariuszy. Aby uzyskać więcej informacji, zobacz [Typy kont magazynu.](storage-account-overview.md#types-of-storage-accounts)<br /><br />Wybierz pozycję **Premium** w przypadku scenariuszy wymagających małych opóźnień. Po wybraniu **opcji Premium** wybierz typ konta usługi Premium Storage do utworzenia. Dostępne są następujące typy kont magazynu w chmurze Premium Storage: <ul><li>[Blokowe obiekty blob](../blobs/storage-blob-performance-tiers.md)</li><li>[Udziały plików](../files/storage-files-planning.md#management-concepts)</li><li>[Stronicowe obiekty blob](../blobs/storage-blob-pageblob-overview.md)</li></ul> |
| Szczegóły wystąpienia | Nadmiarowość | Wymagane | Wybierz żądaną konfigurację nadmiarowości. Nie wszystkie opcje nadmiarowości są dostępne dla wszystkich typów kont magazynu we wszystkich regionach. Aby uzyskać więcej informacji na temat konfiguracji nadmiarowości, zobacz [Nadmiarowość usługi Azure Storage.](storage-redundancy.md)<br /><br />W przypadku wybrania konfiguracji geograficznie nadmiarowej (GRS lub GZRS) dane są replikowane do centrum danych w innym regionie. Aby uzyskać dostęp do odczytu danych w regionie pomocniczym, wybierz pozycję Udostępnij dostęp do odczytu do danych w przypadku regionalnej **niedostępności.** |

Na poniższej ilustracji przedstawiono standardową konfigurację nowego konta magazynu.

:::image type="content" source="media/storage-account-create/create-account-basics-tab.png" alt-text="Zrzut ekranu przedstawiający standardową konfigurację nowego konta magazynu — karta Podstawowe":::

### <a name="advanced-tab"></a>Karta Zaawansowane

Na karcie **Zaawansowane** można skonfigurować dodatkowe opcje i zmodyfikować ustawienia domyślne dla nowego konta magazynu. Niektóre z tych opcji można również skonfigurować po utworzeniu konta magazynu, podczas gdy inne muszą być konfigurowane w czasie tworzenia.

W poniższej tabeli opisano pola na **karcie** Zaawansowane.

| Sekcja | Pole | Wymagane lub opcjonalne | Opis |
|--|--|--|--|
| Zabezpieczenia | Włącz bezpieczny transfer | Opcjonalne | Włącz bezpieczny transfer, aby wymagać, aby żądania przychodzące do tego konta magazynu zostały wykonane tylko za pośrednictwem protokołu HTTPS (ustawienie domyślne). Zalecane w celu zapewnienia optymalnych zabezpieczeń. Aby uzyskać więcej informacji, zobacz [Require secure transfer to ensure secure connections (Wymaganie bezpiecznego transferu w celu zapewnienia bezpiecznych połączeń).](storage-require-secure-transfer.md) |
| Zabezpieczenia | Włączanie szyfrowania infrastruktury | Opcjonalne | Domyślnie szyfrowanie infrastruktury nie jest włączone. Włącz szyfrowanie infrastruktury, aby szyfrować dane zarówno na poziomie usługi, jak i na poziomie infrastruktury. Aby uzyskać więcej informacji, zobacz [Tworzenie konta magazynu z włączonym](infrastructure-encryption-enable.md)szyfrowaniem infrastruktury w celu podwójnego szyfrowania danych. |
| Zabezpieczenia | Włączanie dostępu publicznego do obiektów blob | Opcjonalne | Po włączeniu to ustawienie umożliwia użytkownikowi z odpowiednimi uprawnieniami, aby umożliwić anonimowy publiczny dostęp do kontenera na koncie magazynu (ustawienie domyślne). Wyłączenie tego ustawienia uniemożliwia cały anonimowy dostęp publiczny do konta magazynu. Aby uzyskać więcej informacji, zobacz Prevent anonymous public read access to containers and blobs (Zapobieganie [anonimowemu publicznemu dostępowi do odczytu do kontenerów i obiektów blob).](../blobs/anonymous-read-access-prevent.md)<br> <br> Włączenie dostępu publicznego do obiektów blob nie powoduje, że dane obiektów blob są dostępne dla publicznego dostępu, chyba że użytkownik przejmie dodatkowy krok w celu jawnego skonfigurowania ustawienia dostępu publicznego kontenera. |
| Zabezpieczenia | Włączanie dostępu do klucza konta magazynu (wersja zapoznawcza) | Opcjonalne | Po włączeniu tego ustawienia klienci mogą autoryzować żądania do konta magazynu przy użyciu kluczy dostępu do konta lub konta Azure Active Directory (Azure AD) (ustawienie domyślne). Wyłączenie tego ustawienia uniemożliwia autoryzację przy użyciu kluczy dostępu do konta. Aby uzyskać więcej informacji, zobacz [Zapobieganie autoryzacji klucza wspólnego dla konta usługi Azure Storage (wersja zapoznawcza).](shared-key-authorization-prevent.md) |
| Zabezpieczenia | Wersja minimalna protokołu TLS | Wymagane | Wybierz minimalną wersję Transport Layer Security (TLS) dla żądań przychodzących do konta magazynu. Wartość domyślna to TLS w wersji 1.2. W przypadku ustawienia wartości domyślnej żądania przychodzące wykonane przy użyciu TLS 1.0 lub TLS 1.1 są odrzucane. Aby uzyskać więcej informacji, zobacz Wymuszanie minimalnej wymaganej [wersji usługi Transport Layer Security (TLS) dla](transport-layer-security-configure-minimum-version.md)żądań do konta magazynu. |
| Data Lake Storage Gen2 | Włączanie hierarchicznej przestrzeni nazw | Opcjonalne | Aby używać tego konta magazynu Azure Data Lake Storage Gen2 obciążeniami, skonfiguruj hierarchiczną przestrzeń nazw. Aby uzyskać więcej informacji, [zobacz Wprowadzenie do Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md). |
| Blob Storage | Włączanie sieciowego udziału plików (NFS) w wersji 3 (wersja zapoznawcza) | Opcjonalne | System plików NFS w wersji 3 zapewnia zgodność systemu plików z systemem Linux na dużą skalę magazynu obiektów, co umożliwia klientom systemu Linux montowanie kontenera w magazynie obiektów blob z maszyny wirtualnej platformy Azure lub komputera lokalnego. Aby uzyskać więcej informacji, zobacz Obsługa protokołu [NFS (Network File System) 3.0 w usłudze Azure Blob Storage (wersja zapoznawcza).](../blobs/network-file-system-protocol-support.md) |
| Blob Storage | Warstwa dostępu | Wymagane | Warstwy dostępu do obiektów blob umożliwiają przechowywanie danych obiektów blob w najbardziej opłacalny sposób na podstawie użycia. Wybierz warstwę Gorąca (domyślną) dla często używanych danych. Wybierz warstwę chłodną dla rzadko używanych danych. Aby uzyskać więcej informacji, zobacz Warstwy dostępu [dla Azure Blob Storage — Gorąca, Chłodna i Archiwum.](../blobs/storage-blob-storage-tiers.md) |
| Azure Files | Włączanie obsługi dużych udziałów plików | Opcjonalne | Dostępne tylko dla kont usługi Premium Storage dla udziałów plików. Aby uzyskać więcej informacji, zobacz Włączanie standardowych udziałów plików o rozmiarze [do 100 TiB.](../files/storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib) |
| Tabele i kolejki | Włączanie obsługi kluczy zarządzanych przez klienta | Opcjonalne | Aby włączyć obsługę kluczy zarządzanych przez klienta dla tabel i kolejek, należy wybrać to ustawienie podczas tworzenia konta magazynu. Aby uzyskać więcej informacji, zobacz Tworzenie konta obsługującego klucze zarządzane przez [klienta dla tabel i kolejek.](account-encryption-key-create.md) |

### <a name="networking-tab"></a>Karta Sieć

Na karcie **Sieć** można skonfigurować ustawienia preferencji łączności sieciowej i routingu dla nowego konta magazynu. Te opcje można również skonfigurować po utworzeniu konta magazynu.

W poniższej tabeli opisano pola na **karcie** Sieć.

| Sekcja | Pole | Wymagane lub opcjonalne | Opis |
|--|--|--|--|
| Łączność sieciowa | Metoda łączności | Wymagane | Domyślnie przychodzący ruch sieciowy jest przekierowyny do publicznego punktu końcowego dla konta magazynu. Możesz określić, że ruch musi być przekierowyny do publicznego punktu końcowego za pośrednictwem sieci wirtualnej platformy Azure. Możesz również skonfigurować prywatne punkty końcowe dla konta magazynu. Aby uzyskać więcej informacji, zobacz Use private endpoints for Azure Storage (Używanie [prywatnych punktów końcowych dla usługi Azure Storage).](storage-private-endpoints.md) |
| Routing sieciowy | Preferencja routingu | Wymagane | Preferencja routingu sieciowego określa sposób kierowania ruchu sieciowego do publicznego punktu końcowego konta magazynu z klientów przez Internet. Domyślnie nowe konto magazynu używa routingu sieciowego firmy Microsoft. Można również wybrać trasę ruchu sieciowego przez pop najbliżej konta magazynu, co może obniżyć koszty sieci. Aby uzyskać więcej informacji, zobacz [Network routing preference for Azure Storage (Preferencje routingu sieciowego dla usługi Azure Storage).](network-routing-preference.md) |

### <a name="data-protection-tab"></a>Karta Ochrona danych

Na karcie **Ochrona danych** można skonfigurować opcje ochrony danych dla danych obiektów blob na nowym koncie magazynu.  Te opcje można również skonfigurować po utworzeniu konta magazynu. Aby uzyskać omówienie opcji ochrony danych w usłudze Azure Storage, zobacz [Omówienie ochrony danych](../blobs/data-protection-overview.md).

W poniższej tabeli opisano pola na **karcie Ochrona** danych.

| Sekcja | Pole | Wymagane lub opcjonalne | Opis |
|--|--|--|--|
| Odzyskiwania | Włączanie przywracania do punktu w czasie dla kontenerów | Opcjonalne | Przywracanie do punktu w czasie zapewnia ochronę przed przypadkowym usunięciem lub uszkodzeniem, umożliwiając przywrócenie danych blokowych obiektów blob do wcześniejszego stanu. Aby uzyskać więcej informacji, zobacz [Przywracanie do punktu w czasie dla blokowych obiektów blob.](../blobs/point-in-time-restore-overview.md)<br /><br />Włączenie przywracania do punktu w czasie umożliwia również korzystanie z wersji obiektów blob, usuwania nieużywanego obiektów blob i zestawienia zmian obiektów blob. Te funkcje wymagań wstępnych mogą mieć wpływ na koszty. Aby uzyskać więcej informacji, zobacz [Cennik i rozliczenia dotyczące](../blobs/point-in-time-restore-overview.md#pricing-and-billing) przywracania do punktu w czasie. |
| Odzyskiwania | Włączanie usuwania nietrwałego dla obiektów blob | Opcjonalne | Usuwanie nieumyślne obiektu blob chroni pojedynczy obiekt blob, migawkę lub wersję przed przypadkowym usunięciem lub zastąpieniem, utrzymując usunięte dane w systemie przez określony okres przechowywania. W trakcie okresu przechowywania można przywrócić nieukreślony obiekt do stanu z chwili jego usunięcia. Aby uzyskać więcej informacji, zobacz [Usuwanie nieaduchomielne dla obiektów blob](../blobs/soft-delete-blob-overview.md).<br /><br />Firma Microsoft zaleca włączenie usuwania nie softowego obiektów blob dla kont magazynu i ustawienie minimalnego okresu przechowywania na siedem dni. |
| Odzyskiwania | Włączanie usuwania nie soft dla kontenerów (wersja zapoznawcza) | Opcjonalne | Usuwanie nieumyślne kontenera chroni kontener i jego zawartość przed przypadkowym usunięciem, utrzymując usunięte dane w systemie przez określony okres przechowywania. W okresie przechowywania można przywrócić nieukońcony usunięty kontener do jego stanu w momencie jego usunięcia. Aby uzyskać więcej informacji, zobacz [Usuwanie nie softowe dla kontenerów (wersja zapoznawcza).](../blobs/soft-delete-container-overview.md)<br /><br />Firma Microsoft zaleca włączenie usuwania nie softowego kontenera dla kont magazynu i ustawienie minimalnego okresu przechowywania na siedem dni. |
| Odzyskiwania | Włączanie usuwania nie soft dla udziałów plików | Opcjonalne | Usuwanie nieumyślne udziałów plików chroni udział plików i jego zawartość przed przypadkowym usunięciem przez utrzymywanie usuniętych danych w systemie przez określony okres przechowywania. W okresie przechowywania można przywrócić nieużytkowo usunięty udział plików do jego stanu w momencie jego usunięcia. Aby uzyskać więcej informacji, zobacz [Zapobieganie przypadkowemu usunięciu udziałów plików platformy Azure.](../files/storage-files-prevent-file-share-deletion.md)<br /><br />Firma Microsoft zaleca włączenie usuwania nie soft dla udziałów plików Azure Files obciążeń i ustawienie minimalnego okresu przechowywania na siedem dni. |
| Śledzenie | Włączanie obsługi wersji dla obiektów blob | Opcjonalne | Wersja obiektu blob automatycznie zapisuje stan obiektu blob w poprzedniej wersji, gdy obiekt blob zostanie zastąpiony. Aby uzyskać więcej informacji, zobacz [Blob versioning (Wersjonarowanie obiektów blob).](../blobs/versioning-overview.md)<br /><br />Firma Microsoft zaleca włączenie obsługi wersji obiektów blob w celu zapewnienia optymalnej ochrony danych dla konta magazynu. |
| Śledzenie | Włączanie zestawienia zmian obiektów blob | Opcjonalne | Zestawienia zmian obiektów blob zawierają dzienniki transakcji wszystkich zmian wszystkich obiektów blob na koncie magazynu, a także ich metadanych. Aby uzyskać więcej informacji, zobacz [Obsługa zestawienia zmian w Azure Blob Storage](../blobs/storage-blob-change-feed.md). |

### <a name="tags-tab"></a>Karta Tagi

Na karcie **Tagi** możesz określić tagi Resource Manager, aby ułatwić organizowanie zasobów platformy Azure. Aby uzyskać więcej informacji, zobacz [Tagowanie zasobów, grup zasobów i subskrypcji dla organizacji logicznej.](../../azure-resource-manager/management/tag-resources.md)

### <a name="review--create-tab"></a>Karta Przeglądanie i tworzenie

Po przechodzeniu do karty **Przeglądanie + tworzenie** platforma Azure uruchamia walidację wybranych ustawień konta magazynu. Jeśli weryfikacja przebiegnie pomyślnie, możesz kontynuować tworzenie konta magazynu.

Jeśli walidacja nie powiedzie się, portal wskaże, które ustawienia należy zmodyfikować.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby utworzyć konto magazynu ogólnego przeznaczenia w wersji 2 przy użyciu programu PowerShell, najpierw utwórz nową grupę zasobów, wywołując polecenie [New-AzResourceGroup:](/powershell/module/az.resources/new-azresourcegroup)

```azurepowershell-interactive
$resourceGroup = "<resource-group>"
$location = "<location>"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

Jeśli nie masz pewności, który region należy określić dla parametru `-Location`, za pomocą polecenia [Get-AzLocation](/powershell/module/az.resources/get-azlocation) możesz pobrać listę obsługiwanych regionów dla swojej subskrypcji:

```azurepowershell-interactive
Get-AzLocation | select Location
```

Następnie utwórz standardowe konto magazynu ogólnego przeznaczenia w wersji 2 z magazynem geograficznie nadmiarowym do odczytu (RA-GRS) przy użyciu polecenia [New-AzStorageAccount.](/powershell/module/az.storage/new-azstorageaccount) Pamiętaj, że nazwa konta magazynu musi być unikatowa na platformie Azure, dlatego zastąp wartość symbolu zastępczego w nawiasach własną unikatową wartością:

```azurepowershell-interactive
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2
```

Aby włączyć hierarchiczną przestrzeń nazw dla konta magazynu na Azure Data Lake Storage [,](https://azure.microsoft.com/services/storage/data-lake-storage/)uwzględnij parametr w wywołaniu polecenia `-EnableHierarchicalNamespace $True` **New-AzStorageAccount.**

W poniższej tabeli przedstawiono wartości, których należy użyć dla parametrów i w celu utworzenia określonego typu konta magazynu z `-SkuName` `-Kind` żądaną konfiguracją nadmiarowości.

| Typ konta magazynu | Obsługiwane konfiguracje nadmiarowości | Wartość parametru -Kind | Możliwe wartości parametru -SkuName | Obsługuje hierarchiczną przestrzeń nazw |
|--|--|--|--|--|
| Standardowe ogólnego przeznaczenia, wersja 2 | LRS/ GRS / RA-GRS / ZRS / GZRS / RA-GZRS | StorageV2 | Standard_LRS / Standard_GRS / Standard_RAGRS / Standard_ZRS / Standard_GZRS / Standard_RAGZRS | Tak |
| Blokowe obiekty blob w wersji Premium | LRS/ZRS | BlockBlobStorage | Premium_LRS / Premium_ZRS | Tak |
| Udziały plików Premium | LRS/ZRS | FileStorage | Premium_LRS / Premium_ZRS | Nie |
| Stronicowe obiekty blob w wersji Premium | LRS | StorageV2 | Premium_LRS | Nie |
| Starsza wersja standardowa ogólnego przeznaczenia, wersja 1 | LRS/ GRS / RA-GRS | Storage | Standard_LRS / Standard_GRS / Standard_RAGRS | Nie |
| Starszy magazyn obiektów blob | LRS/ GRS / RA-GRS | BlobStorage | Standard_LRS / Standard_GRS / Standard_RAGRS | Nie |

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby utworzyć konto magazynu ogólnego przeznaczenia w wersji 2 za pomocą interfejsu wiersza polecenia platformy Azure, najpierw utwórz nową grupę zasobów, wywołując [polecenie az group create.](/cli/azure/group#az_group_create)

```azurecli-interactive
az group create \
  --name storage-resource-group \
  --location westus
```

Jeśli nie masz pewności, który region należy określić dla parametru `--location`, za pomocą polecenia [az account list-locations](/cli/azure/account#az_account_list) możesz pobrać listę obsługiwanych regionów dla swojej subskrypcji.

```azurecli-interactive
az account list-locations \
  --query "[].{Region:name}" \
  --out table
```

Następnie utwórz standardowe konto magazynu ogólnego przeznaczenia w wersji 2 z magazynem geograficznie nadmiarowym do odczytu przy użyciu [polecenia az storage account create.](/cli/azure/storage/account#az_storage_account_create) Pamiętaj, że nazwa konta magazynu musi być unikatowa na platformie Azure, dlatego zastąp wartość symbolu zastępczego w nawiasach własną unikatową wartością:

```azurecli-interactive
az storage account create \
  --name <account-name> \
  --resource-group storage-resource-group \
  --location westus \
  --sku Standard_RAGRS \
  --kind StorageV2
```

Aby włączyć hierarchiczną przestrzeń nazw dla konta magazynu na Azure Data Lake Storage [,](https://azure.microsoft.com/services/storage/data-lake-storage/)uwzględnij parametr w wywołaniu polecenia `--enable-hierarchical-namespace true` az storage account **create.** Tworzenie hierarchicznej przestrzeni nazw wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.79 lub nowszej.

W poniższej tabeli przedstawiono wartości, których należy użyć dla parametrów i w celu utworzenia określonego typu konta magazynu z `-sku` `-kind` żądaną konfiguracją nadmiarowości.

| Typ konta magazynu | Obsługiwane konfiguracje nadmiarowości | Wartość parametru -kind | Możliwe wartości parametru -sku | Obsługuje hierarchiczną przestrzeń nazw |
|--|--|--|--|--|
| Standardowe ogólnego przeznaczenia, wersja 2 | LRS/ GRS / RA-GRS / ZRS / GZRS / RA-GZRS | StorageV2 | Standard_LRS / Standard_GRS / Standard_RAGRS / Standard_ZRS / Standard_GZRS / Standard_RAGZRS | Tak |
| Blokowe obiekty blob w wersji Premium | LRS/ZRS | BlockBlobStorage | Premium_LRS / Premium_ZRS | Tak |
| Udziały plików w wersji Premium | LRS/ZRS | FileStorage | Premium_LRS / Premium_ZRS | Nie |
| Stronicowe obiekty blob w wersji Premium | LRS | StorageV2 | Premium_LRS | Nie |
| Starsza wersja standardowa ogólnego przeznaczenia, wersja 1 | LRS/ GRS / RA-GRS | Storage | Standard_LRS / Standard_GRS / Standard_RAGRS | Nie |
| Starszy magazyn obiektów blob | LRS/ GRS / RA-GRS | BlobStorage | Standard_LRS / Standard_GRS / Standard_RAGRS | Nie |

# <a name="template"></a>[Szablon](#tab/template)

Do wdrożenia szablonu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure można użyć szablonu usługi Resource Manager w celu utworzenia konta magazynu. Szablon używany w tym artykule z niestandardowych przewodników Szybki start Azure Resource Manager [.](https://azure.microsoft.com/resources/templates/101-storage-account-create/) Aby uruchomić skrypty, wybierz **pozycję Wypróbuj,** aby otworzyć Azure Cloud Shell. Aby wkleić skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz pozycję **Wklej**.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

> [!NOTE]
> Ten szablon służy tylko jako przykład. Istnieje wiele ustawień konta magazynu, które nie są konfigurowane w ramach tego szablonu. Jeśli na przykład chcesz użyć Data Lake Storage [,](https://azure.microsoft.com/services/storage/data-lake-storage/)zmodyfikuj ten szablon, ustawiając właściwość `isHnsEnabledad` obiektu na `StorageAccountPropertiesCreateParameters` `true` .

Aby dowiedzieć się, jak zmodyfikować ten szablon lub utworzyć nowe, zobacz:

- [Azure Resource Manager dokumentacji programu](../../azure-resource-manager/index.yml).
- [Odwołanie do szablonu konta magazynu.](/azure/templates/microsoft.storage/allversions)
- [Dodatkowe przykłady szablonów konta magazynu.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage)

---

## <a name="delete-a-storage-account"></a>Usuwanie konta magazynu

Usunięcie konta magazynu powoduje usunięcie całego konta, w tym wszystkich danych na koncie, i nie można go cofnąć.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Przejdź do konta magazynu w Azure Portal [.](https://portal.azure.com)
1. Kliknij polecenie **Usuń**.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby usunąć konto magazynu, użyj [polecenia Remove-AzStorageAccount:](/powershell/module/az.storage/remove-azstorageaccount)

```powershell
Remove-AzStorageAccount -Name <storage-account> -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby usunąć konto magazynu, użyj [polecenia az storage account delete:](/cli/azure/storage/account#az-storage-account-delete)

```azurecli-interactive
az storage account delete --name <storage-account> --resource-group <resource-group>
```

# <a name="template"></a>[Szablon](#tab/template)

Aby usunąć konto magazynu, użyj polecenia Azure PowerShell wiersza polecenia platformy Azure.

```azurepowershell-interactive
$storageResourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
Remove-AzStorageAccount -Name $storageAccountName -ResourceGroupName $storageResourceGroupName
```

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --name storageAccountName --resource-group resourceGroupName
```

---

Alternatywnie możesz usunąć grupę zasobów, co spowoduje usunięcie konta magazynu i innych zasobów w tej grupie zasobów. Aby uzyskać więcej informacji na temat usuwania grupy zasobów, zobacz [Usuwanie grupy zasobów i zasobów.](../../azure-resource-manager/management/delete-resource-group.md)

> [!WARNING]
> Nie można przywrócić usuniętego konta magazynu ani odzyskać żadnej zawartości znajdującej się na koncie przed usunięciem. Zanim usuniesz konto, wykonaj kopię zapasową wszystkich danych, które chcesz zapisać. Dotyczy to również wszystkich zasobów na koncie — po usunięciu obiektu Blob, tabeli, kolejki lub pliku elementy te są trwale usuwane.
>
> Podczas próby usunięcia konta magazynu skojarzonego z maszyną wirtualną platformy Azure może zostać wyświetlony komunikat o błędzie dotyczący wciąż używanego konta magazynu. Aby uzyskać pomoc w rozwiązywaniu problemów z tym błędem, zobacz Rozwiązywanie problemów [z błędami podczas usuwania kont magazynu.](/troubleshoot/azure/virtual-machines/welcome-virtual-machines)

## <a name="next-steps"></a>Następne kroki

- [Omówienie kont magazynu](storage-account-overview.md)
- [Uaktualnianie do konta magazynu ogólnego przeznaczenia w wersji 2](storage-account-upgrade.md)
- [Przenoszenie konta magazynu do innego regionu](storage-account-move.md)
- [Odzyskiwanie usuniętego konta magazynu](storage-account-recover.md)