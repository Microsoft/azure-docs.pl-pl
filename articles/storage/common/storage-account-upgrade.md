---
title: Uaktualnienie konta magazynu ogólnego przeznaczenia do wersji 2
titleSuffix: Azure Storage
description: Uaktualnij do kont magazynu ogólnego przeznaczenia w wersji 2 za pomocą Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Określ warstwę dostępu dla danych obiektów BLOB.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/30/2021
ms.author: tamram
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 30c73849d07c1495251c17510ea71f29c674c618
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967493"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>Uaktualnienie konta magazynu ogólnego przeznaczenia do wersji 2

Konta magazynu ogólnego przeznaczenia w wersji 2 obsługują najnowsze funkcje usługi Azure Storage i obejmują wszystkie funkcje kont w ramach ogólnego przeznaczenia w wersji 1 i usługi BLOB Storage. Konta ogólnego przeznaczenia w wersji 2 są zalecane w przypadku większości scenariuszy magazynu. Konta ogólnego przeznaczenia w wersji 2 zapewniają najniższy poziom wydajności dla usługi Azure Storage, a także konkurencyjne w branży ceny transakcji. Konta ogólnego przeznaczenia w wersji 2 obsługują domyślne warstwy dostępu do zasobów gorąca i chłodna oraz poziomu obiektów BLOB między gorącą, chłodną lub archiwalną.

Uaktualnianie do konta magazynu ogólnego przeznaczenia w wersji 2 z poziomu kont ogólnego przeznaczenia w wersji 1 lub magazynu obiektów BLOB jest proste. Uaktualnienie można wykonać przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Brak przestojów ani ryzyka utraty danych skojarzonych z uaktualnianiem do konta magazynu ogólnego przeznaczenia w wersji 2. Uaktualnienie konta odbywa się za pośrednictwem prostej operacji Azure Resource Manager, która zmienia typ konta.

> [!IMPORTANT]
> Uaktualnianie konta ogólnego przeznaczenia v1 lub BLOB Storage do ogólnego przeznaczenia w wersji 2 jest trwałe i nie można go cofnąć.

> [!NOTE]
> Mimo że firma Microsoft zaleca konta ogólnego przeznaczenia w wersji 2 dla większości scenariuszy, firma Microsoft będzie nadal obsługiwała konta ogólnego przeznaczenia w wersji 1 dla nowych i istniejących klientów. W nowych regionach można tworzyć konta magazynu ogólnego przeznaczenia w wersji 1, gdy usługa Azure Storage jest dostępna w tych regionach. Firma Microsoft nie ma obecnie planu na wycofanie pomocy technicznej dla kont ogólnego przeznaczenia w wersji 1 i udostępnienie co najmniej jednego roku przed wycofaniem jakiejkolwiek funkcji usługi Azure Storage. Firma Microsoft będzie nadal dostarczać aktualizacje zabezpieczeń dla kont ogólnego przeznaczenia w wersji 1, ale dla tego typu konta nie jest oczekiwane tworzenie nowych funkcji.
>
> W przypadku nowych regionów świadczenia usługi Azure, które przechodzą w tryb online po 1 października 2020, ceny dla kont ogólnego przeznaczenia w wersji 1 uległy zmianie i są one równoważne z cenami dla kont ogólnego przeznaczenia w wersji 2 w tych regionach. Cennik kont ogólnego przeznaczenia w wersji 1 w regionach świadczenia usługi Azure, które istniały przed 1 października 2020, nie został zmieniony. Szczegóły cennika kont ogólnego przeznaczenia w wersji 1 w określonym regionie znajdują się na stronie cennika usługi Azure Storage. Wybierz region, a następnie w obszarze **oferty cennika** wybierz pozycję **inne**.

## <a name="upgrade-an-account"></a>Uaktualnianie konta

Aby uaktualnić konto usługi Microsoft w wersji 1 lub BLOB do konta ogólnego przeznaczenia w wersji 2, użyj Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do konta magazynu.
3. W sekcji **Ustawienia** kliknij pozycję **Konfiguracja**.
4. W obszarze **rodzaj konta** kliknij pozycję **Uaktualnij**.
5. W obszarze **Potwierdź uaktualnianie** wpisz nazwę konta.
6. Kliknij przycisk **Uaktualnij** w dolnej części bloku.

    ![Typ konta uaktualnienia](../blobs/media/storage-blob-account-upgrade/upgrade-to-gpv2-account.png)

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Aby uaktualnić konto ogólnego przeznaczenia w wersji 1 do konta ogólnego przeznaczenia w wersji 2 przy użyciu programu PowerShell, najpierw zaktualizuj program PowerShell, aby używał najnowszej wersji modułu **AZ. Storage** . Aby uzyskać więcej informacji na temat instalowania programu PowerShell, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/install-Az-ps).

Następnie Wywołaj następujące polecenie, aby uaktualnić konto, podstawiając nazwę grupy zasobów, nazwę konta magazynu i żądaną warstwę dostępu do konta.

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -Name <storage-account> -UpgradeToStorageV2 -AccessTier <Hot/Cool>
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uaktualnić konto ogólnego przeznaczenia w wersji 1 do konta ogólnego przeznaczenia w wersji 2 przy użyciu interfejsu wiersza polecenia platformy Azure, najpierw zainstaluj najnowszą wersję interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji na temat instalowania interfejsu wiersza polecenia, zobacz [Instalacja interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli).

Następnie Wywołaj następujące polecenie, aby uaktualnić konto, podstawiając nazwę grupy zasobów, nazwę konta magazynu i żądaną warstwę dostępu do konta.

```azurecli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2 --access-tier=<Hot/Cool>
```

---

## <a name="specify-an-access-tier-for-blob-data"></a>Określanie warstwy dostępu dla danych obiektów BLOB

Konta ogólnego przeznaczenia w wersji 2 obsługują wszystkie usługi Azure Storage i obiekty danych, ale warstwy dostępu są dostępne tylko w celu blokowania obiektów BLOB w magazynie obiektów BLOB. W przypadku uaktualniania do konta magazynu ogólnego przeznaczenia w wersji 2 można określić domyślną warstwę dostępu do konta gorąca lub chłodna, która wskazuje warstwę domyślną dane obiektów BLOB zostaną przekazane, tak jakby nie określono indywidualnego parametru warstwy dostępu do obiektów BLOB.

Warstwy dostępu do obiektów BLOB umożliwiają wybranie najbardziej ekonomicznego magazynu w oparciu o przewidywane wzorce użycia. Blokowe obiekty blob mogą być przechowywane w warstwach gorąca, chłodna lub archiwalna. Aby uzyskać więcej informacji na temat warstw dostępu, zobacz [Azure Blob Storage: warstwy magazynowania gorąca, chłodna i archiwalna](../blobs/storage-blob-storage-tiers.md).

Domyślnie nowe konto magazynu jest tworzone w warstwie dostępu gorąca, a konto magazynu ogólnego przeznaczenia w wersji 1 można uaktualnić do warstwy konta gorąca lub chłodna. Jeśli podczas uaktualniania nie określono warstwy dostępu do konta, zostanie ona domyślnie uaktualniona do wersji gorąca. Jeśli szukasz warstwy dostępu, która ma być używana na potrzeby uaktualnienia, weź pod uwagę bieżący scenariusz użycia danych. Istnieją dwa typowe scenariusze dotyczące migracji do konta ogólnego przeznaczenia w wersji 2:

* Masz już istniejące konto magazynu ogólnego przeznaczenia w wersji 1 i chcesz oszacować uaktualnienie do konta magazynu ogólnego przeznaczenia w wersji 2, z odpowiednią warstwą dostępu do magazynu dla danych obiektów BLOB.
* Użytkownik zdecydował się użyć konta magazynu ogólnego przeznaczenia w wersji 2 lub już go i chcieć sprawdzić, czy w przypadku danych obiektów BLOB należy używać warstwy dostępu do magazynu gorąca lub chłodna.

W obu przypadkach pierwszy priorytet ma na celu oszacowanie kosztów przechowywania, uzyskiwania dostępu do danych przechowywanych w ramach konta magazynu ogólnego przeznaczenia w wersji 2 i porównywania ich z bieżącymi kosztami.

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

Uaktualnianie konta magazynu w wersji 1 do konta ogólnego przeznaczenia w wersji 2 jest bezpłatne. W trakcie procesu uaktualniania można określić żądaną warstwę konta. Jeśli podczas uaktualniania nie zostanie określona warstwa konta, będzie ona domyślną warstwą konta uaktualnionego konta `Hot` . Jednak zmiana warstwy dostępu do magazynu po uaktualnieniu może spowodować wprowadzenie zmian w rachunku, aby podczas uaktualniania zaleca się określenie nowej warstwy konta.

Wszystkie konta magazynu używają modelu cenowego dla magazynu obiektów blob opartego na warstwie każdego obiektu blob. W przypadku korzystania z konta magazynu mają zastosowanie następujące zagadnienia dotyczące rozliczeń:

* **Koszty magazynowania**: oprócz ilości przechowywanych danych koszt przechowywania danych różni się w zależności od warstwy dostępu do magazynu. Koszt za gigabajt zmniejsza się w miarę, jak warstwa staje się chłodniejsza.

* **Koszty dostępu do danych**: opłaty za dostęp do danych wzrastają w miarę, jak warstwa staje się chłodniejsza. W przypadku danych w warstwie dostępu do magazynu chłodna i archiwalna naliczana jest opłata za dostęp do danych za każdy gigabajt dla operacji odczytu.

* **Koszty transakcji**: w przypadku wszystkich warstw naliczana jest opłata za transakcję, która wzrasta w miarę, jak warstwa staje się chłodniejsza.

* **Koszty transferu danych replikacji geograficznej**: ta opłata dotyczy tylko kont ze skonfigurowaną replikacją geograficzną, w tym GRS i RA-GRS. Transfer danych w ramach replikacji geograficznej powoduje naliczanie opłaty za każdy gigabajt.

* **Koszty transferu danych wychodzących**: transfery danych wychodzących (dane przesyłane poza region platformy Azure) powodują naliczanie opłat za zużycie przepustowości za każdy gigabajt, co jest spójne z kontami magazynu ogólnego przeznaczenia.

* **Zmiana warstwy dostępu do magazynu**: zmiana warstwy dostępu do magazynu konta z chłodna na gorąca spowoduje naliczenie opłaty równej odczytaniu wszystkich danych istniejących na koncie magazynu. Jednak zmiana warstwy dostępu do konta z gorąca na chłodna spowoduje naliczenie opłaty równej zapisywaniu wszystkich danych w warstwie chłodna (tylko konta GPv2).

> [!NOTE]
> Więcej informacji dotyczących modelu cenowego dla kont magazynu można znaleźć na stronie [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/). Więcej informacji dotyczących opłat za transfer danych wychodzących można znaleźć na stronie [Szczegóły cennika transferów danych](https://azure.microsoft.com/pricing/details/data-transfers/).

### <a name="estimate-costs-for-your-current-usage-patterns"></a>Oszacuj koszty bieżących wzorców użytkowania

Aby oszacować koszt przechowywania i uzyskiwania dostępu do danych obiektów BLOB w ramach konta magazynu ogólnego przeznaczenia w wersji 2 w określonej warstwie, Oceń istniejący wzorzec użycia lub przybliżony oczekiwany wzorzec użycia. Ogólnie potrzebne są odpowiedzi na następujące pytania:

* Użycie magazynu obiektów BLOB w gigabajtach, w tym:
  * Ile danych jest przechowywanych na koncie magazynu?
  * Jak zmienia się ilość danych w miesiącu? Czy nowe dane nieustannie zastępują stare dane?

* Podstawowy wzorzec dostępu do danych usługi BLOB Storage, w tym:
  * Jak dużo danych jest odczytywanych i zapisywanych na koncie magazynu?
  * Ile operacji odczytu i operacji zapisu odbywa się na danych na koncie magazynu?

Aby podjąć decyzję dotyczącą najlepszej warstwy dostępu do Twoich potrzeb, można ułatwić ustalenie pojemności danych obiektów blob oraz sposobu używania tych danych. Można to zrobić najlepiej, przeglądając metryki monitorowania dla Twojego konta.

### <a name="monitoring-existing-storage-accounts"></a>Monitorowanie istniejących kont magazynu

Aby monitorować istniejące konta magazynu i gromadzić uzyskane dane, można skorzystać z usługi Azure Storage Analytics, która umożliwia rejestrowanie i dostarcza danych metryk dotyczących konta magazynu. Usługa Storage Analytics może przechowywać metryki, które obejmują zagregowane statystyki transakcji oraz dane pojemności o żądaniach do usługi Storage dla następujących rodzajów kont: GPv1, GPv2 i Blob Storage. Te dane są przechowywane w dobrze znanych tabelach tego samego konta magazynu.

Aby uzyskać więcej informacji, zapoznaj się z artykułami [About Storage Analytics Metrics (Informacje o metrykach w usłudze Storage Analytics)](../blobs/monitor-blob-storage.md) i [Storage Analytics Metrics Table Schema (Schemat tabeli metryk usługi Storage Analytics)](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema)

> [!NOTE]
> Konta usługi Blob Storage ujawniają punkt końcowy usługi tabel tylko w odniesieniu do przechowywania i uzyskiwania dostępu do danych metryk dla tego konta.

Aby monitorować użycie magazynu dla usługi Blob Storage, należy włączyć metryki pojemności.
Dzięki temu dane pojemności są rejestrowane codziennie dla Blob service konta magazynu i rejestrowane jako wpis tabeli, który jest zapisywany w tabeli *$MetricsCapacityBlob* w ramach tego samego konta magazynu.

Aby monitorować wzorce dostępu do danych dla usługi Blob Storage, należy włączyć godzinowe metryki transakcji z poziomu interfejsu API. Dzięki włączeniu godzinowych metryk transakcji transakcje interfejsu API są agregowane co godzinę i rejestrowane jako wpis tabeli, który jest zapisywany w tabeli *$MetricsHourPrimaryTransactionsBlob* w obrębie tego samego konta magazynu. W przypadku używania kont magazynu RA-GRS tabela *$MetricsHourSecondaryTransactionsBlob* rejestruje transakcje kierowane do pomocniczego punktu końcowego.

> [!NOTE]
> Jeśli masz konto magazynu ogólnego przeznaczenia, w którym są przechowywane stronicowe obiekty blob i dyski maszyny wirtualnej albo kolejki, pliki lub tabele (obok danych blokowych obiektów blob i uzupełnialnych obiektów blob), ten proces szacowania nie ma zastosowania. Dane pojemności nie obejmują rozróżnienia blokowych obiektów blob od innych typów ani danych pojemności dla innych typów danych. Jeśli używasz tych typów, alternatywną metodologią będzie zapoznanie się z ilościami na najnowszym rachunku.

Aby uzyskać najbardziej zbliżone do prawdziwych informacje o użyciu danych i wzorcu dostępu, zalecamy wybranie takiego okresu przechowywania dla metryk, który odzwierciedla normalne użycie, i ekstrapolację. Jedną z opcji jest przechowywanie danych metryk przez siedem dni i zbieranie danych co tydzień, aby przeprowadzić analizę pod koniec miesiąca. Innym rozwiązaniem jest przechowywanie danych metryk z ostatnich 30 dni i zbieranie oraz analizowanie danych z końcem 30-dniowego okresu.

Aby uzyskać szczegółowe informacje na temat włączania, gromadzenia i wyświetlania danych metryk, zobacz [metryki analizy magazynu](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Za przechowywanie i pobieranie danych analitycznych oraz uzyskiwanie dostępu do nich również są naliczane opłaty, podobnie jak za zwykłe dane użytkowników.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Używanie metryk użycia do szacowania kosztów

#### <a name="capacity-costs"></a>Koszty pojemności

Najnowszy wpis w tabeli metryk pojemności *$MetricsCapacityBlob* z kluczem wiersza *„data”* pokazuje pojemność magazynu zajętą przez dane użytkownika. Najnowszy wpis w tabeli metryk pojemności *$MetricsCapacityBlob* z kluczem wiersza *„analytics”* pokazuje pojemność magazynu zajętą przez dzienniki analiz.

Łączna pojemność zajęta przez dane użytkownika i dzienniki analiz (jeśli są włączone) może być użyta do szacowania kosztów magazynowania danych w ramach konta magazynu. Tę samą metodę można również użyć do szacowania kosztów magazynowania na kontach magazynu GPv1.

#### <a name="transaction-costs"></a>Koszty transakcji

Suma *„TotalBillableRequests”* dla wszystkich wpisów interfejsu API w tabeli metryk transakcji wskazuje całkowitą liczbę transakcji dla tego konkretnego interfejsu API. *Na przykład* całkowitą liczbę transakcji *„GetBlob”* w danym okresie można obliczyć, sumując liczbę płatnych żądań dla wszystkich wpisów z kluczem wiersza *'user;GetBlob'*.

Aby oszacować koszty transakcji dla kont usługi Blob Storage, konieczne będzie podzielenie transakcji na trzy grupy, ponieważ mają one różne ceny.

* Transakcje zapisu, takie jak *„PutBlob”*, *„PutBlock”*, *„PutBlockList”*, *„AppendBlock"*, *„ListBlobs”*, *„ListContainers”*, *„CreateContainer”*, *„SnapshotBlob”* i *„CopyBlob”*.
* Transakcje usuwania, takie jak *„DeleteBlob”* i *„DeleteContainer”*.
* Wszystkie inne transakcje.

W celu oszacowania kosztów transakcji dla kont magazynu GPv1 należy zagregować wszystkie transakcje niezależnie od operacji/interfejsu API.

#### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Dostęp do danych i koszty transferu danych replikacji geograficznej

Usługa Storage Analytics nie udostępnia informacji na temat ilości odczytywanych i zapisywanych danych na koncie magazynu, ale można je w przybliżeniu oszacować, analizując tabelę metryk transakcji. Suma *„TotalIngress”* dla wszystkich wpisów interfejsu API w tabeli metryk transakcji wskazuje całkowitą ilość danych przychodzących w bajtach dla tego konkretnego interfejsu API. Podobnie suma *„TotalEgress”* wskazuje całkowitą ilość danych wychodzących w bajtach.

Aby oszacować koszty dostępu do danych dla kont usługi Blob Storage, konieczne będzie podzielenie transakcji na dwie grupy.

* Ilość danych pobieranych z konta magazynu można oszacować, przyglądając się sumie *„TotalEgress”* przede wszystkim dla operacji *„GetBlob”* i *„CopyBlob”*.

* Ilość danych zapisywanych na koncie magazynu można oszacować, przyglądając się sumie *„TotalIngress”* przede wszystkim dla operacji *„PutBlob”*, *„PutBlock”*, *„CopyBlob”* i *„AppendBlock”*.

Koszt transferu danych replikacji geograficznej dla kont usługi Blob Storage można obliczyć, korzystając z kalkulacji ilości danych zapisanych w przypadku używania konta magazynu GRS lub RA-GRS.

> [!NOTE]
> Aby zapoznać się z bardziej szczegółowym przykładem obliczania kosztów korzystania z warstwy dostępu do magazynu gorącego lub chłodnego, zapoznaj się z często zadawanymi pytaniami zatytułowanymi *"co to są warstwy dostępu gorąca i chłodna" i jak określić, która z nich ma być używana?* na stronie z [cennikiem usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

## <a name="next-steps"></a>Następne kroki

* [Omówienie kont magazynu](storage-account-overview.md)
* [Tworzenie konta magazynu](storage-account-create.md)
* [Przenoszenie konta usługi Azure Storage do innego regionu](storage-account-move.md)
* [Odzyskiwanie usuniętego konta magazynu](storage-account-recover.md)
