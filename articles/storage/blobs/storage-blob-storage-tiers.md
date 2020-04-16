---
title: Warstwy dostępu gorąca, chłodna i archiwizowana dla obiektów blob — usługa Azure Storage
description: Warstwy dostępu gorąca, chłodna i archiwizowana dla kont magazynu platformy Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/23/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: c803d489b70cda6910865f6096d21c2021c4ae3a
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393705"
---
# <a name="azure-blob-storage-hot-cool-and-archive-access-tiers"></a>Usługa Azure Blob Storage: warstwy dostępu Gorąca, Chłodna i Archiwum

Azure storage offers different access tiers, which allow you to store blob object data in the most cost-effective manner. Dostępne warstwy dostępu obejmują:

- **Hot** — zoptymalizowane do przechowywania danych, które są często dostępne.
- **Cool** — zoptymalizowane do przechowywania danych, które są rzadko dostępne i przechowywane przez co najmniej 30 dni.
- **Archiwum** — zoptymalizowane pod kątem przechowywania danych, które rzadko są dostępne i przechowywane przez co najmniej 180 dni z wymaganiami dotyczącymi elastycznego opóźnienia (w kolejności godzin).

Następujące zagadnienia dotyczą różnych warstw dostępu:

- Tylko gorące i fajne warstwy dostępu można ustawić na poziomie konta. Warstwa dostępu do archiwum nie jest dostępna na poziomie konta.
- Warstwy gorąca, chłodna i archiwalna można ustawić na poziomie obiektu blob podczas przesyłania lub po przesłaniu.
- Dane w warstwie dostępu chłodnego mogą tolerować nieco niższą dostępność, ale nadal wymagają wysokiej trwałości, opóźnienia pobierania i charakterystyki przepływności podobne do gorących danych. W przypadku chłodnych danych nieco niższa umowa o poziomie usług dostępności (SLA) i wyższe koszty dostępu w porównaniu z gorącymi danymi są akceptowalnymi kompromisami dla niższych kosztów magazynowania.
- Archiwum pamięci masowej przechowuje dane w trybie offline i oferuje najniższe koszty magazynowania, ale także najwyższe koszty ponownego nawodnienia i dostępu do danych.

Dane przechowywane w chmurze rosną w wykładniczym tempie. Aby zarządzać kosztami zwiększających się potrzeb dotyczących magazynowania, warto zorganizować dane na podstawie atrybutów, takich jak częstotliwość dostępu i planowany okres przechowywania. Pozwoli to na optymalizację kosztów. Dane przechowywane w chmurze mogą się różnić w zależności od sposobu ich generowania, przetwarzania i uzyskiwania do nich dostępu przez cały okres jej istnienia. Do niektórych danych często uzyskuje się dostęp. Są one również często modyfikowane w trakcie całego okresu istnienia. Do niektórych danych często uzyskuje się dostęp na początkowym etapie istnienia, a z czasem już zdecydowanie rzadziej. Niektóre dane pozostają bezczynne w chmurze i rzadko, jeśli w ogóle, są dostępne po ich zapisaniu.

Każdy z tych scenariuszy dostępu do danych korzysta z innej warstwy dostępu, która jest zoptymalizowana pod kątem określonego wzorca dostępu. Dzięki warstwom dostępu do gorąca, chłodna i archiwizowana usługa Azure Blob storage zaspokaja tę potrzebę zróżnicowanych warstw dostępu za pomocą oddzielnych modeli cenowych.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>Konta magazynu z obsługą warstw

Warstwowa warstwa danych magazynu obiektów między plikami gorąca, chłodna i archiwum jest obsługiwana tylko w magazynowaniu obiektów Blob i kontach ogólnego przeznaczenia w wersji 2 (GPv2). Konta ogólnego przeznaczenia w wersji 1 (GPv1) nie obsługują warstw. Klienci mogą łatwo konwertować istniejące konta magazynu GPv1 lub obiektów blob na konta GPv2 za pośrednictwem witryny Azure portal. GPv2 zapewnia nowe ceny i funkcje dla obiektów blob, plików i kolejek. Niektóre funkcje i obniżki cen są oferowane tylko na kontach GPv2. Oceń przy użyciu kont GPv2 po kompleksowym zapoznaniu się z cenami. Niektóre obciążenia mogą być droższe w GPv2 niż GPv1. Aby uzyskać więcej informacji, zobacz [Omówienie konta magazynu platformy Azure](../common/storage-account-overview.md).

Magazyn obiektów blob i konta GPv2 ujawniają atrybut **warstwy dostępu** na poziomie konta. Ten atrybut umożliwia określenie domyślnej warstwy dostępu dla dowolnego obiektu blob, który nie ma jawnego zestawu na poziomie obiektu. W przypadku obiektów z warstwą ustawioną na poziomie obiektu warstwa konta nie będzie stosowana. Warstwę archiwum można zastosować tylko na poziomie obiektu. W dowolnym momencie można przełączać się między tymi warstwami dostępu.

## <a name="hot-access-tier"></a>Warstwa dostępu Gorąca

Warstwa dostępu na gorąco ma wyższe koszty magazynowania niż warstwy chłodne i archiwalne, ale najniższe koszty dostępu. Przykładowe scenariusze użycia dla warstwy dostępu gorąca obejmują:

- Dane, które są w aktywnym użyciu lub oczekuje się, że będą dostępne (odczytywane z i zapisywane do) często.
- Dane, które są przygotowane do przetwarzania i ewentualnej migracji do warstwy dostępu chłodnego.

## <a name="cool-access-tier"></a>Warstwa dostępu Chłodna

Warstwa chłodnego dostępu ma niższe koszty magazynowania i wyższe koszty dostępu w porównaniu do pamięci masowej na gorąco. Ta warstwa jest przeznaczona dla danych, które pozostaną w warstwie Chłodna przez co najmniej 30 dni. Przykładowe scenariusze użycia dla warstwy dostępu chłodnego obejmują:

- Krótkoterminowe kopie zapasowe i zestawy danych odzyskiwania po awarii.
- Starszą zawartość nośników, która nie jest już często wyświetlana, ale oczekiwane jest, że będzie ona natychmiast dostępna, gdy będzie to wymagane.
- Duże zbiory danych, które muszą być przechowywane w sposób ekonomiczny, podczas gdy na potrzeby przyszłego przetwarzania zbierana jest większa ilość danych (*np.* długoterminowe magazynowanie danych naukowych lub nieprzetworzonych danych telemetrycznych z zakładu produkcyjnego).

## <a name="archive-access-tier"></a>Warstwa dostępu Archiwum

Warstwa dostępu do archiwum ma najniższy koszt magazynowania. Ale ma wyższe koszty pobierania danych w porównaniu do gorących i chłodnych warstw. Pobieranie danych w warstwie archiwum może potrwać kilka godzin. Dane muszą pozostać w warstwie archiwum przez co najmniej 180 dni lub podlegać wczesnemu uszczubieniu.

Gdy obiekt blob znajduje się w magazynie archiwum, dane obiektu blob są w trybie offline i nie można ich odczytać, nadpisania ani zmodyfikować. Aby odczytać lub pobrać obiekt blob w archiwum, należy najpierw nawodnić go do warstwy online. Nie można robić migawek obiektu blob w magazynie archiwum. Jednak metadane obiektu blob pozostaje w trybie online i dostępne, co pozwala na listę obiektu blob i jego właściwości. W przypadku obiektów blob w archiwum jedynymi prawidłowymi operacjami są GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier, CopyBlob i DeleteBlob. Aby dowiedzieć się [więcej, zobacz Ponowne nawodnienie danych obiektów blob z warstwy archiwum.](storage-blob-rehydration.md)

Przykładowe scenariusze użycia dla warstwy dostępu do archiwum obejmują:

- Długoterminowe kopie zapasowe, dodatkowe kopie zapasowej i archiwalne zestawy danych
- Oryginalne (nieprzetworzone) dane, które muszą zostać zachowane, nawet po przetworzeniu ich do ostatecznej użytecznej postaci
- Dane zgodności i dane archiwalne, które muszą być przechowywane przez długi czas, i do których bardzo rzadko uzyskuje się dostęp

## <a name="account-level-tiering"></a>Poziom konta

Obiekty BLOB we wszystkich trzech warstwach dostępu mogą współistnieć w ramach tego samego konta. Każdy obiekt blob, który nie ma jawnie przypisanej warstwy, wywnioskuje warstwę z ustawienia warstwy dostępu do konta. Jeśli warstwa dostępu pochodzi z konta, zostanie wyświetlona właściwość **wywnioskowane** obiekty blob warstwy dostępu ustawiona na "true", a właściwość obiektu blob **warstwy dostępu** jest zgodna z warstwą kont. W witrynie Azure portal właściwość _wywnioskowana warstwy dostępu_ jest wyświetlana z warstwą dostępu do obiektów blob jako **gorąca (wywnioskowana)** lub **Chłodna (wywnioskowana).**

Zmiana warstwy dostępu do konta ma zastosowanie do wszystkich obiektów _wywnioskowanych warstwy dostępu_ przechowywanych na koncie, które nie mają jawnego zestawu warstw. Jeśli przełączysz warstwę konta z gorącej na chłodną, zostanie naliczona opłata za operacje zapisu (za 10 000) dla wszystkich obiektów blob bez warstwy zestawu tylko na kontach GPv2. Nie ma żadnych opłat za tę zmianę na kontach magazynu obiektów Blob. Jeśli przełączysz się z chłodnego na gorący w magazynie obiektów Blob lub na konta GPv2, zostanie naliczona opłata zarówno za operacje odczytu (na 10 000), jak i za pobranie danych (za GB).

## <a name="blob-level-tiering"></a>Obsługa warstw na poziomie obiektów blob

Warstwowe na poziomie obiektów blob umożliwia przekazywanie danych do wybranej warstwy dostępu przy użyciu operacji [Umieść obiekt blob](/rest/api/storageservices/put-blob) lub [Umieść listę bloków](/rest/api/storageservices/put-block-list) i zmienianie warstwy danych na poziomie obiektu przy użyciu operacji Set [Blob Tier](/rest/api/storageservices/set-blob-tier) lub funkcji [zarządzania cyklem życia.](#blob-lifecycle-management) Można przekazać dane do wymaganej warstwy dostępu, a następnie łatwo zmienić warstwę dostępu do obiektów blob wśród warstw gorących, chłodnych lub archiwalnych w miarę zmiany wzorców użycia bez konieczności przenoszenia danych między kontami. Wszystkie żądania zmiany warstwy zdarzają się natychmiast, a zmiany warstwy między gorącym i chłodnym są natychmiastowe. Jednak ponowne nawadnianie obiektu blob z archiwum może potrwać kilka godzin.

Czas ostatniej zmiany warstwy obiektu blob jest uwidaczniany za pomocą właściwości obiektu blob **Czas zmiany warstwy dostępu**. Podczas zastępowania obiektu blob w warstwie gorąca lub chłodna nowo utworzony obiekt blob dziedziczy warstwę obiektu blob, która została zastąpiona, chyba że nowa warstwa dostępu do obiektu blob jest jawnie ustawiona podczas tworzenia. Jeśli obiekt blob znajduje się w warstwie archiwum, nie można go zastąpić, więc przekazywanie tego samego obiektu blob nie jest dozwolone w tym scenariuszu. 

> [!NOTE]
> Magazyn Archiwum i funkcja obsługi warstw na poziomie obiektów blob obsługują tylko blokowe obiekty blob. Obecnie nie można również zmienić warstwy bloku obiektu blob, który ma migawki.

### <a name="blob-lifecycle-management"></a>Zarządzanie cyklem życia obiektów blob

Zarządzanie cyklem życia magazynu obiektów blob oferuje bogatą zasadę opartą na regułach, której można użyć do przejścia danych do najlepszej warstwy dostępu i wygaśnięcia danych po zakończeniu cyklu życia. Aby dowiedzieć się [więcej, zobacz Zarządzanie cyklem życia magazynu obiektów blob platformy Azure.](storage-lifecycle-management-concepts.md)  

> [!NOTE]
> Dane przechowywane na koncie magazynu obiektów blob bloku (wydajność Premium) nie mogą być obecnie warstwowe do hot, cool lub archiwum przy użyciu [set warstwy obiektów blob](/rest/api/storageservices/set-blob-tier) lub przy użyciu zarządzania cyklem życia usługi Azure Blob Storage.
> Aby przenieść dane, należy zsynchronizować obiekty blob z konta magazynu obiektów blob bloku do warstwy dostępu gorąca na innym koncie przy użyciu [interfejsu API adresu URL Odmienny](/rest/api/storageservices/put-block-from-url) lub wersji AzCopy, która obsługuje ten interfejs API.
> Interfejs API *adresu URL Umieść blok od* synchronicznie kopiuje dane na serwerze, co oznacza, że wywołanie kończy się tylko wtedy, gdy wszystkie dane zostaną przeniesione z oryginalnej lokalizacji serwera do lokalizacji docelowej.

### <a name="blob-level-tiering-billing"></a>Rozliczanie obsługi warstw na poziomie obiektów blob

Gdy obiekt blob zostanie przekazany lub przeniesiony do warstwy gorąca, chłodna lub archiwum, jest naliczana według odpowiedniej stawki natychmiast po zmianie warstwy.

Gdy obiekt blob jest przenoszony do warstwy chłodniejszej (hot->chłodnym, archiwum hot->lub cool->), operacja jest rozliczana jako operacja zapisu do warstwy docelowej, w której stosuje się operację zapisu (na 10 000) i opłaty za zapis danych (na GB) warstwy docelowej.

Gdy obiekt blob jest przenoszony do cieplejszej warstwy (>archiwum chłodne, >archiwum gorące lub >chłodzone), operacja jest rozliczana jako odczyt z warstwy źródłowej, gdzie stosuje się operację odczytu (na 10 000) i pobierające dane (na GB) opłaty w warstwie źródłowej. Mogą również obowiązywać opłaty za wczesne usunięcie dla dowolnego obiektu przeniesionego z warstwy Chłodna lub Archiwum. [Ponowne nawadnianie danych z archiwum](storage-blob-rehydration.md) wymaga czasu, a dane będą naliczane ceny archiwów, dopóki dane nie zostaną przywrócone online i zmienią warstwę obiektów blob na gorącą lub chłodną. W poniższej tabeli podsumowano sposób rozliczania zmian warstwy:

| | **Opłaty za zapis (operacja + dostęp)** | **Opłaty za odczyt (operacja + dostęp)**
| ---- | ----- | ----- |
| **Kierunek SetBlobTier** | gorące >chłodne,<br> archiwum hot->,<br> archiwum cool-> | archiwum->chłodne,<br> archiwum >gorące,<br> chłodny >gorący

### <a name="cool-and-archive-early-deletion"></a>Opłaty za wcześniejsze usunięcie w warstwach Chłodna i Archiwum

Każdy obiekt blob, który jest przenoszony do warstwy chłodnej (tylko konta GPv2) podlega chłodnemu okresowi wczesnego usuwania wynoszącym 30 dni. Każdy obiekt blob, który zostanie przeniesiony do warstwy archiwum podlega okresowi wczesnego usuwania archiwum wynoszącym 180 dni. Ta opłata jest naliczana proporcjonalnie. Jeśli na przykład obiekt blob zostanie przeniesiony do archiwum, a następnie usunięty lub przeniesiony do warstwy gorąca po 45 dniach, zostanie naliczona opłata za wcześniejsze usunięcie równą 135 (180 minus 45) dni przechowywania tego obiektu blob w archiwum.

Wczesne usunięcie można obliczyć przy użyciu właściwości obiektu blob, **Last-Modified**, jeśli nie wprowadzono żadnych zmian w warstwie dostępu. W przeciwnym razie można użyć, gdy warstwa dostępu została ostatnio zmodyfikowana do chłodzenia lub archiwizacji, wyświetlając właściwość obiektu blob: **access-tier-change-time**. Aby uzyskać więcej informacji na temat właściwości obiektu blob, zobacz [Get Właściwości obiektu Blob](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties).

## <a name="comparing-block-blob-storage-options"></a>Porównywanie opcji przechowywania bloków obiektów blob

W poniższej tabeli przedstawiono porównanie magazynu bloków obiektów blob o wydajności premium oraz warstw dostępu do gorących, chłodnych i archiwalnych.

|                                           | **Najwyższa wydajność**   | **Warstwa gorąca** | **Fajny poziom**       | **Warstwa archiwum**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **Dostępność**                          | 99,9%                     | 99,9%        | 99%                 | W trybie offline           |
| **Dostępność** <br> **(odczyty RA-GRS)**  | Nie dotyczy                       | 99,99%       | 99,9%               | W trybie offline           |
| **Opłaty za użycie**                         | Wyższe koszty magazynowania, niższy dostęp i koszty transakcji | Wyższe koszty magazynowania, niższy dostęp i koszty transakcji | Niższe koszty magazynowania, wyższy dostęp i koszty transakcji | Najniższe koszty magazynowania, najwyższy dostęp i koszty transakcji |
| **Minimalny rozmiar obiektu**                   | Nie dotyczy                       | Nie dotyczy          | Nie dotyczy                 | Nie dotyczy               |
| **Minimalny czas magazynowania**              | Nie dotyczy                       | Nie dotyczy          | 30 dni<sup>1</sup> | 180 dni
| **Opóźnienie** <br> **(czas do pierwszego bajtu)** | Jednocyfrowe milisekundy | milisekundy | milisekundy        | <sup>godz.</sup> |

<sup>1</sup> Obiekty w warstwie chłodnej na kontach GPv2 mają minimalny czas przechowywania wynoszący 30 dni. Konta magazynu obiektów blob nie mają minimalnego czasu przechowywania dla warstwy chłodnej.

<sup>2</sup> Archiwum Storage obecnie obsługuje 2 nawadniać priorytety, Wysoki i Standardowy, który oferuje różne opóźnienia pobierania. Aby uzyskać więcej informacji, zobacz [Ponowne nawadnianie danych obiektów blob z warstwy archiwum](storage-blob-rehydration.md).

> [!NOTE]
> Konta magazynu obiektów Blob obsługują te same cele wydajności i skalowalności, co konta magazynu ogólnego przeznaczenia w wersji 2. Aby uzyskać więcej informacji, zobacz [Skalowalność i cele wydajności dla magazynu obiektów Blob](scalability-targets.md).

## <a name="quickstart-scenarios"></a>Scenariusze typu Szybki start

W tej sekcji przedstawiono następujące scenariusze przy użyciu witryny Azure portal i powershell:

- Jak zmienić domyślną warstwę dostępu konta GPv2 lub usługi Blob Storage.
- Jak zmienić warstwę obiektu blob na koncie GPv2 lub usługi Blob Storage.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Zmienianie domyślnej warstwy dostępu konta GPv2 lub usługi Blob Storage

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W witrynie Azure portal wyszukaj i wybierz pozycję **Wszystkie zasoby**.

1. Wybierz swoje konto magazynu.

1. W **obszarze Ustawienia**wybierz pozycję **Konfiguracja,** aby wyświetlić i zmienić konfigurację konta.

1. Wybierz odpowiednią warstwę dostępu dla swoich potrzeb: ustaw **warstwę Dostęp** na **Chłodną** lub **Gorącą**.

1. Kliknij **pozycję Zapisz** u góry.

![Zmienianie warstwy konta magazynu](media/storage-tiers/account-tier.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Następujący skrypt programu PowerShell może służyć do zmiany warstwy konta. Zmienna `$rgName` musi zostać zainicjowana z nazwą grupy zasobów. Zmienna `$accountName` musi zostać zainicjowana z nazwą konta magazynu. 
```powershell
#Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

#Change the storage account tier to hot
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Hot
```
---

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Zmienianie warstwy obiektu blob na koncie magazynu GPv2 lub obiektów blob
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W witrynie Azure portal wyszukaj i wybierz pozycję **Wszystkie zasoby**.

1. Wybierz swoje konto magazynu.

1. Wybierz kontener, a następnie wybierz obiekt blob.

1. We **właściwościach obiektu Blob**wybierz pozycję **Zmień warstwę**.

1. Wybierz warstwę dostępu **Gorąca,** **Chłodna**lub **Archiwum.** Jeśli obiekt blob znajduje się obecnie w archiwum i chcesz nawodnienie do warstwy online, można również wybrać priorytet ponownego nawadniania **standardu** lub **wysokiego**.

1. Wybierz **pozycję Zapisz** u dołu.

![Zmienianie warstwy konta magazynu](media/storage-tiers/blob-access-tier.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Następujący skrypt programu PowerShell może służyć do zmiany warstwy obiektów blob. Zmienna `$rgName` musi zostać zainicjowana z nazwą grupy zasobów. Zmienna `$accountName` musi zostać zainicjowana z nazwą konta magazynu. Zmienna `$containerName` musi zostać zainicjowana z nazwą kontenera. Zmienna `$blobName` musi zostać zainicjowana z nazwą obiektu blob. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName == ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

#Change the blob’s access tier to archive
$blob.ICloudBlob.SetStandardBlobTier("Archive")
```
---

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

Wszystkie konta magazynu używają modelu cenowego dla magazynu bloków obiektów blob na podstawie warstwy każdego obiektu blob. Należy pamiętać o następujących zagadnieniach dotyczących rozliczeń:

- **Koszty usługi Storage**: oprócz ilości przechowywanych danych koszt przechowywania danych różni się w zależności od warstwy dostępu. Koszt za gigabajt zmniejsza się w miarę, jak warstwa staje się chłodniejsza.
- **Koszty dostępu do danych**: opłaty za dostęp do danych wzrastają w miarę, jak warstwa staje się chłodniejsza. W przypadku danych w warstwie dostępu do danych chłodnych i archiwalnych pobierana jest opłata za dostęp do danych za gigabajt dla odczytów.
- **Koszty transakcji:** Opłata za transakcję jest pobierana za wszystkie warstwy, która wzrasta wraz z niższą chłodsicarką warstwy.
- **Koszty transferu danych replikacji geograficznej**: ta opłata dotyczy tylko kont ze skonfigurowaną replikacją geograficzną, w tym GRS i RA-GRS. Transfer danych w ramach replikacji geograficznej powoduje naliczanie opłaty za każdy gigabajt.
- **Koszty transferu danych wychodzących**: transfery danych wychodzących (dane przesyłane poza region platformy Azure) powodują naliczanie opłat za zużycie przepustowości za każdy gigabajt, co jest spójne z kontami magazynu ogólnego przeznaczenia.
- **Zmiana warstwy dostępu:** Zmiana warstwy dostępu do konta spowoduje, że opłaty za zmianę warstwy _warstwy dostępu wywnioskowane_ obiekty blob przechowywane na koncie, które nie mają jawnego zestawu warstw. Aby uzyskać informacje na temat zmiany warstwy dostępu dla pojedynczego obiektu blob, zobacz [rozliczenia warstwowe na poziomie obiektów blob](#blob-level-tiering-billing).

> [!NOTE]
> Aby uzyskać więcej informacji na temat cen obiektów blob bloku, zobacz stronę [cennika usługi Azure Storage.](https://azure.microsoft.com/pricing/details/storage/blobs/) Więcej informacji dotyczących opłat za transfer danych wychodzących można znaleźć na stronie [Szczegóły cennika transferów danych](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="faq"></a>Często zadawane pytania

**Czy należy używać magazynu obiektów Blob lub kont GPv2, jeśli chcę warstwy moich danych?**

Zalecamy korzystanie z kont GPv2 zamiast kont usługi Blob Storage na potrzeby obsługi warstw. Konta GPv2 obsługują wszystkie funkcje, które obsługują konta usługi Blob Storage, i wiele innych. Ceny kont usługi Blob Storage i GPv2 są niemal identyczne, ale niektóre nowe funkcje i rabaty cenowe będą dostępne tylko na kontach GPv2. Konta GPv1 nie obsługują warstw.

Struktury cen kont GPv1 i GPv2 są różne, a klienci powinni dokładnie je sprawdzić przed podjęciem decyzji o użyciu kont GPv2. Istniejące konto usługi Blob Storage lub GPv1 można łatwo przekonwertować na konto GPv2 w ramach prostego procesu uruchamianego jednym kliknięciem. Aby uzyskać więcej informacji, zobacz [Omówienie konta magazynu platformy Azure](../common/storage-account-overview.md).

**Czy mogę przechowywać obiekty we wszystkich trzech warstwach dostępu (gorący, chłodny i archiwalny) na tym samym koncie?**

Tak. Atrybut **warstwy dostępu** ustawiony na poziomie konta jest domyślną warstwą konta, która ma zastosowanie do wszystkich obiektów na tym koncie bez jawnej warstwy zestawu. Warstwowe na poziomie obiektów blob umożliwia ustawienie warstwy dostępu na poziomie obiektu, niezależnie od tego, jakie jest ustawienie warstwy dostępu na koncie. Obiekty BLOB w dowolnej z trzech warstw dostępu (gorąca, chłodna lub archiwum) mogą istnieć w ramach tego samego konta.

**Czy mogę zmienić domyślną warstwę dostępu mojego konta magazynu obiektu Blob lub GPv2?**

Tak, możesz zmienić domyślną warstwę konta, ustawiając atrybut **warstwy dostęp** na koncie magazynu. Zmiana warstwy konta dotyczy wszystkich obiektów przechowywanych na koncie, które nie mają jawnego zestawu warstw (na przykład **Hot (wywnioskowane)** lub **Cool (wywnioskowane).** Przełączanie warstwy konta od gorącej do chłodnej wiąże się z operacjami zapisu (na 10 000) dla wszystkich obiektów blob bez warstwy zestawu tylko na kontach GPv2 i przełączaniem od chłodnych do gorących, co wiąże się zarówno z operacjami odczytu (na 10 000) i pobieraniem danych (na GB) opłatami za wszystkie obiekty blob w magazynie obiektów Blob i kontach GPv2.

**Czy mogę ustawić domyślną warstwę dostępu do konta na Archiwum?**

Nie. Tylko warstwy dostępu na gorąco i chłodno mogą być ustawione jako domyślna warstwa dostępu do konta. Archiwum można ustawić tylko na poziomie obiektu. W przekazywaniu obiektów blob można określić warstwę dostępu do wybranej warstwy, aby była gorąca, chłodna lub archiwum, niezależnie od domyślnej warstwy konta. Ta funkcja umożliwia zapisywanie danych bezpośrednio w warstwie archiwum, aby zrealizować oszczędności od momentu utworzenia danych w magazynie obiektów blob.

**W jakich regionach dostępne są warstwy dostępu do gorących, chłodnych i archiwalnych?**

Warstwy dostępu gorącego i chłodnego wraz z warstwami na poziomie obiektów blob są dostępne we wszystkich regionach. Magazyn w warstwie Archiwum będzie początkowo dostępny tylko w wybranych regionach. Pełną listę można znaleźć w temacie [Dostępność produktów platformy Azure według regionów](https://azure.microsoft.com/regions/services/).

**Czy obiekty blob w warstwie dostępu chłodnego zachowują się inaczej niż w warstwie dostępu gorąca?**

Obiekty BLOB w warstwie dostępu gorąca mają takie same opóźnienia jak obiekty blob na kontach magazynu GPv1, GPv2 i blob. Obiekty BLOB w warstwie dostępu chłodnego mają podobne opóźnienie (w milisekundach) jak obiekty blob na kontach magazynu GPv1, GPv2 i obiektów blob. Obiekty BLOB w warstwie dostępu do archiwum mają kilka godzin opóźnienia na kontach magazynu GPv1, GPv2 i Blob.

Obiekty BLOB w warstwie dostępu chłodnego mają nieco niższy poziom usługi dostępności (SLA) niż obiekty blob przechowywane w warstwie dostępu gorąca. Aby uzyskać więcej informacji, zobacz [Magazyn — umowa SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).

**Czy operacje w warstwach Gorąca, Chłodna i Archiwum są takie same?**

Wszystkie operacje w warstwach Gorąca i Chłodna są w 100% spójne. Wszystkie prawidłowe operacje archiwum, w tym GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier i DeleteBlob są w 100% zgodne z hot and cool. Nie można odczytać ani zmodyfikować danych obiektów blob w warstwie archiwum do czasu ponownego nawodnienia; obsługiwane są tylko operacje odczytu metadanych obiektów blob w archiwum.

**Kiedy podczas ponownego wypełniania obiektu blob z warstwy Archiwum do warstwy Gorąca lub Chłodna będę wiedzieć, że ten proces został ukończony?**

Podczas ponownego nawodnienia można użyć get blob właściwości operacji sondowania **atrybutu Stan archiwum** i potwierdzić, po zakończeniu zmiany warstwy. Właściwość ta ma wartość „rehydrate-pending-to-hot” (ponowne wypełnianie w celu przejścia do warstwy gorącej) lub „rehydrate-pending-to-cool” (ponowne wypełnianie w celu przejścia do warstwy chłodnej) w zależności od warstwy docelowej. Po zakończeniu tego procesu właściwość obiektu blob „stan archiwum” jest usuwana, a wartość właściwości **Warstwa dostępu** odpowiada nowej warstwie Gorąca lub Chłodna. Aby dowiedzieć się [więcej, zobacz Ponowne nawodnienie danych obiektów blob z warstwy archiwum.](storage-blob-rehydration.md)

**Kiedy po ustawieniu warstwy obiektu blob rozpocznie się naliczanie opłat przy użyciu odpowiedniej stawki?**

Każdy obiekt blob jest zawsze rozliczane zgodnie z warstwy wskazanej przez obiekt **dostępu warstwy** obiektu blob właściwości. Po ustawieniu nowej warstwy online dla obiektu blob właściwość **warstwy dostępu** natychmiast odzwierciedla nową warstwę dla wszystkich przejść. Jednak ponowne nawadnianie obiektu blob z warstwy archiwum trybu offline do warstwy gorącej lub chłodnej może potrwać kilka godzin. W takim przypadku naliczane są naliczane stawki za archiwalne do czasu zakończenia ponownego nawodnienia, w którym to momencie właściwość **warstwy dostępu** odzwierciedla nową warstwę. Po ponownym nawodnieniu do warstwy online, jesteś rozliczane za ten obiekt blob w gorącej lub chłodnej stawki.

**Jak ustalić, czy podczas usuwania lub przenoszenia obiektu blob z warstwy chłodnej lub archiwum poniesieem opłatę za wcześniejsze usunięcie?**

Proporcjonalne opłaty za wczesne usunięcie będą dotyczyć wszystkich obiektów blob usuniętych lub przeniesionych z warstwy Chłodna (tylko konta GPv2) lub Archiwum przed upływem odpowiednio 30 lub 180 dni. Można określić, jak długo obiekt blob został w warstwie cool lub archiwum, sprawdzając właściwość obiektu blob **czasu zmiany warstwy dostępu,** która zapewnia pieczęć ostatniej zmiany warstwy. Jeśli warstwa obiektu blob nigdy nie została zmieniona, można sprawdzić **właściwości ostatni zmodyfikowany** obiekt blob. Aby uzyskać więcej informacji, zobacz [Fajne i archiwizuj wczesne usunięcie](#cool-and-archive-early-deletion).

**Które narzędzia i zestawy SDK platformy Azure obsługują warstwy na poziomie obiektów blob i magazyn w warstwie Archiwum?**

Następujące rozwiązania: witryna Azure Portal, program PowerShell, narzędzia interfejsu wiersza polecenia oraz biblioteki klienta środowisk .NET, Java, Python i Node.js obsługują warstwy na poziomie obiektów blob i magazyn w warstwie Archiwum.  

**Ile danych można przechowywać w warstwach Gorąca, Chłodna i Archiwum?**

Przechowywanie danych wraz z innymi limitami są ustawiane na poziomie konta, a nie na warstwę dostępu. Możesz użyć całego limitu w jednej warstwie lub we wszystkich trzech warstwach. Aby uzyskać więcej informacji, zobacz [Cele skalowalności i wydajności dla standardowych kont magazynu](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>Następne kroki

Ocena gorących, chłodnych i archiwizowanych na kontach magazynu GPv2 i Blob

- [Sprawdzanie dostępności warstw Gorąca, Chłodna i Archiwum według regionu](https://azure.microsoft.com/regions/#services)
- [Zarządzanie cyklem życia magazynu usługi Azure Blob Storage](storage-lifecycle-management-concepts.md)
- [Dowiedz się więcej o nawadnianiu danych obiektów blob z warstwy archiwum](storage-blob-rehydration.md)
- [Określ, czy wydajność premium przyniesie korzyści Twojej aplikacji](storage-blob-performance-tiers.md)
- [Ocena użycia bieżących kont magazynu przez włączenie metryk usługi Azure Storage](../common/storage-enable-and-view-metrics.md)
- [Sprawdzanie cen gorących, chłodnych i archiwalnych w magazynach obiektów Blob i kontach GPv2 według regionów](https://azure.microsoft.com/pricing/details/storage/)
- [Sprawdzanie ceny transferu danych](https://azure.microsoft.com/pricing/details/data-transfers/)
