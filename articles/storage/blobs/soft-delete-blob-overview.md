---
title: Usuwanie nietrwałe dla obiektów blob
titleSuffix: Azure Storage
description: Nietrwałe usuwanie obiektów BLOB chroni dane, dzięki czemu można łatwiej odzyskać dane, gdy są one błędnie modyfikowane lub usuwane przez aplikację lub przez innego użytkownika konta magazynu.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/27/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 29d9dd7757319e59fc12b42d89c2ce16dec71b8b
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551071"
---
# <a name="soft-delete-for-blobs"></a>Usuwanie nietrwałe dla obiektów blob

Usuwanie nietrwałe obiektów BLOB chroni pojedynczy obiekt BLOB, migawkę lub wersję przed przypadkowym usunięciem lub zastąpieniem przez utrzymywanie usuniętych danych w systemie przez określony przedział czasu. W okresie przechowywania można przywrócić nieusunięty trwale obiekt do jego stanu w momencie jego usunięcia. Po upływie okresu przechowywania obiekt zostanie trwale usunięty.

## <a name="recommended-data-protection-configuration"></a>Zalecana konfiguracja ochrony danych

Usuwanie nietrwałe obiektów BLOB jest częścią kompleksowej strategii ochrony danych obiektów BLOB. Aby zapewnić optymalną ochronę danych obiektów blob, firma Microsoft zaleca włączenie wszystkich następujących funkcji ochrony danych:

- Usuwanie nietrwałe kontenera w celu przywrócenia kontenera, który został usunięty. Aby dowiedzieć się, jak włączyć usuwanie nietrwałe kontenera, zobacz [Włączanie i zarządzanie usuwaniem nietrwałym dla kontenerów](soft-delete-container-enable.md).
- Przechowywanie wersji obiektów BLOB w celu automatycznego zachowywania poprzednich wersji obiektu BLOB. Po włączeniu obsługi wersji obiektów BLOB można przywrócić wcześniejszą wersję obiektu BLOB, aby odzyskać dane, jeśli są one błędnie modyfikowane lub usuwane. Aby dowiedzieć się, jak włączyć obsługę wersji obiektów blob, zobacz [Włączanie obsługi wersji obiektów blob i zarządzanie nimi](versioning-enable.md).
- Usuwanie nietrwałego obiektu BLOB, Przywracanie usuniętego obiektu BLOB, migawki lub wersji. Aby dowiedzieć się, jak włączyć usuwanie nietrwałe obiektów blob, zobacz [Włączanie i zarządzanie nietrwałego usuwania dla obiektów BLOB](soft-delete-blob-enable.md).

Aby dowiedzieć się więcej na temat zaleceń firmy Microsoft dotyczących ochrony danych, zobacz [Omówienie ochrony danych](data-protection-overview.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-blob-soft-delete-works"></a>Jak działa usuwanie nietrwałe obiektów BLOB

Po włączeniu usuwania nietrwałego obiektów BLOB dla konta magazynu należy określić okres przechowywania dla usuniętych obiektów z zakresu od 1 do 365 dni. Okres przechowywania wskazuje, jak długo dane pozostają dostępne po ich usunięciu lub zapisaniu. Zegar rozpocznie się w okresie przechowywania zaraz po usunięciu lub zapisaniu obiektu.

Gdy okres przechowywania jest aktywny, można przywrócić usunięty obiekt BLOB wraz z jego migawkami lub usunięta wersja przez wywołanie operacji [cofania usunięcia obiektu BLOB](/rest/api/storageservices/undelete-blob) . Na poniższym diagramie przedstawiono sposób przywracania usuniętego obiektu, gdy jest włączone usuwanie nietrwałe obiektów blob:

:::image type="content" source="media/soft-delete-blob-overview/blob-soft-delete-diagram.png" alt-text="Diagram przedstawiający sposób przywracania nietrwałego obiektu BLOB":::

Okres przechowywania nietrwałego usuwania można zmienić w dowolnym momencie. Zaktualizowany okres przechowywania ma zastosowanie tylko do danych, które zostały usunięte po zmianie okresu przechowywania. Wszystkie dane, które zostały usunięte przed okres przechowywania, podlegają okresowi przechowywania, który obowiązywał, gdy został usunięty.

Próba usunięcia nietrwałego obiektu nie ma wpływu na jego czas wygaśnięcia.

Wyłączenie usuwania nietrwałego obiektów BLOB pozwala nadal uzyskiwać dostęp do nietrwałych obiektów usuniętych i odzyskiwać je na koncie magazynu, dopóki nie upłynie okres przechowywania nietrwałego.

Obsługa wersji obiektów BLOB jest dostępna dla kont ogólnego przeznaczenia w wersji 2, blokowych obiektów blob i BLOB Storage. Konta magazynu z hierarchiczną przestrzenią nazw włączone do użycia z Azure Data Lake Storage Gen2 nie są obecnie obsługiwane.

W wersji 2017-07-29 i nowszej interfejsu API REST usługi Azure Storage obsługa usuwania nietrwałego obiektu BLOB.

> [!IMPORTANT]
> Można użyć usuwania nietrwałego obiektów BLOB tylko w celu przywrócenia pojedynczego obiektu BLOB, migawki lub wersji. Aby przywrócić kontener i jego zawartość, należy również włączyć opcję usuwania nietrwałego kontenera dla konta magazynu. Firma Microsoft zaleca włączenie funkcji usuwania nietrwałego kontenera i przechowywania wersji obiektów BLOB wraz z usuwaniem nietrwałego obiektu BLOB w celu zapewnienia pełnej ochrony danych obiektów BLOB. Aby uzyskać więcej informacji, zobacz [Omówienie ochrony danych](data-protection-overview.md).
>
> Usuwanie nietrwałe obiektów BLOB nie chroni przed usunięciem konta magazynu. Aby chronić konto magazynu przed usunięciem, należy skonfigurować blokadę zasobu konta magazynu. Aby uzyskać więcej informacji na temat blokowania konta magazynu, zobacz temat [stosowanie blokady Azure Resource Manager na koncie magazynu](../common/lock-account-resource.md).

### <a name="how-deletions-are-handled-when-soft-delete-is-enabled"></a>Jak operacje usuwania są obsługiwane po włączeniu usuwania nietrwałego

Po włączeniu usuwania nietrwałego obiektów BLOB usuwanie obiektów blob, które są usuwane jako nietrwałe. Nie utworzono migawki. Gdy okres przechowywania wygaśnie, usunięty z nietrwałego obiektu BLOB zostanie trwale usunięty.

Jeśli obiekt BLOB zawiera migawki, nie można usunąć obiektu BLOB, chyba że migawki również zostaną usunięte. Po usunięciu obiektu BLOB i jego migawek obiekty blob i migawki są oznaczane jako nietrwałe. Nie są tworzone żadne nowe migawki.

Można również usunąć co najmniej jedną aktywną migawkę bez usuwania podstawowego obiektu BLOB. W takim przypadku migawka jest usuwana.

Obiekty nietrwałe są niewidoczne, chyba że zostaną jawnie wyświetlone lub wyświetlone. Aby uzyskać więcej informacji na temat wyświetlania nietrwałych obiektów usuniętych, zobacz [Zarządzanie i przywracanie](soft-delete-blob-manage.md)nietrwałych, usuniętych obiekty blob.

### <a name="how-overwrites-are-handled-when-soft-delete-is-enabled"></a>Jak zastępowanie jest obsługiwane po włączeniu usuwania nietrwałego

Wywołanie operacji, takiej jak [Put BLOB](/rest/api/storageservices/put-blob), [Put list Block](/rest/api/storageservices/put-block-list)lub [copy BLOB](/rest/api/storageservices/copy-blob) zastępuje dane w obiekcie blob. Po włączeniu usuwania nietrwałego obiektów blob, zastąpienie obiektu BLOB powoduje automatyczne utworzenie nietrwałej migawki stanu obiektu BLOB przed operacją zapisu. Gdy okres przechowywania wygaśnie, migawka usuwania nietrwałego zostanie trwale usunięta.

Migawki usunięte nietrwale są niewidoczne, chyba że jawnie są wyświetlane lub wyświetlane obiekty nietrwałe. Aby uzyskać więcej informacji na temat wyświetlania nietrwałych obiektów usuniętych, zobacz [Zarządzanie i przywracanie](soft-delete-blob-manage.md)nietrwałych, usuniętych obiekty blob.

Aby chronić operację kopiowania, należy włączyć opcję usuwania nietrwałego obiektu BLOB dla docelowego konta magazynu.

Usuwanie nietrwałe obiektów BLOB nie chroni przed operacjami zapisywania metadanych lub właściwości obiektów BLOB. Podczas aktualizowania metadanych lub właściwości obiektu BLOB nie jest tworzona migawka nietrwałego usuwania.

Usuwanie nietrwałe obiektów BLOB nie zapewnia ochrony przed zastąpieniem obiektów BLOB w warstwie archiwum. Jeśli obiekt BLOB w warstwie archiwum zostanie zastąpiony nowym obiektem BLOB w dowolnej warstwie, nadpisany obiekt BLOB zostanie trwale usunięty.

W przypadku kont usługi Premium Storage migawki usunięte nietrwale nie są wliczane do limitu dla poszczególnych obiektów BLOB 100 migawek.

### <a name="restoring-soft-deleted-objects"></a>Przywracanie nietrwałych obiektów usuniętych

Można przywrócić nieusunięte obiekty blob, wywołując operację [cofnięcia usunięcia obiektu BLOB](/rest/api/storageservices/undelete-blob) w ramach okresu przechowywania. Operacja **cofnięcia usunięcia obiektu** BLOB przywraca obiekt BLOB i wszystkie skojarzone z nim migawki usunięte. Wszystkie migawki, które zostały usunięte w okresie przechowywania, zostaną przywrócone.

Wywołanie **obiektu BLOB Undelete** na obiekcie blob, który nie został usunięty z nietrwałego, spowoduje przywrócenie wszystkich usuniętych nietrwałych migawek skojarzonych z obiektem BLOB. Jeśli obiekt BLOB nie ma migawek i nie jest usuwany nietrwale, wywołanie **obiektu BLOB Undelete** nie przynosi żadnego efektu.

Aby podnieść nietrwałą migawkę do podstawowego obiektu BLOB, najpierw Wywołaj metodę **Undelete BLOB** na podstawowym obiekcie blob, aby przywrócić obiekt BLOB i jego migawki. Następnie skopiuj żądaną migawkę przez podstawowy obiekt BLOB. Możesz również skopiować migawkę do nowego obiektu BLOB.

Nie można odczytać danych z nietrwałego obiektu BLOB lub migawki, dopóki obiekt nie zostanie przywrócony.

Aby uzyskać więcej informacji na temat przywracania obiektów usuniętych z nietrwałych, zobacz Zarządzanie i przywracanie nietrwałych, [usuniętych i](soft-delete-blob-manage.md)odniesień.

## <a name="blob-soft-delete-and-versioning"></a>Usuwanie i przechowywanie nietrwałego obiektu BLOB

Jeśli dla konta magazynu włączono zarówno obsługę wersji obiektów blob, jak i usuwanie nietrwałego obiektu BLOB, zastępowanie obiektu BLOB spowoduje automatyczne utworzenie nowej wersji. Nowa wersja nie jest usuwana nietrwale i nie jest usuwana po wygaśnięciu okresu przechowywania nietrwałego. Nie utworzono żadnych migawek usuniętych z nietrwałego. Po usunięciu obiektu BLOB bieżąca wersja obiektu BLOB zostanie wykorzystana w poprzedniej wersji, a bieżąca wersja zostanie usunięta. Nie została utworzona nowa wersja i nie są tworzone żadne migawki usunięte przez program.

Włączenie nietrwałego usuwania i przechowywania wersji razem chroni wersje obiektów BLOB przed usunięciem. Po włączeniu usuwania nietrwałego usunięcie wersji powoduje utworzenie nieusuniętej wersji. Możesz użyć operacji **usuwania obiektów BLOB** do przywrócenia nieusuniętej wersji, o ile istnieje bieżąca wersja obiektu BLOB. Jeśli nie ma bieżącej wersji, przed wywołaniem operacji **usuwania obiektów BLOB** należy skopiować poprzednią wersję do bieżącej wersji.

> [!NOTE]
> Wywołanie operacji **usuwania obiektu BLOB** na usuniętym obiekcie blob, gdy włączono obsługę wersji, przywraca wszystkie nietrwałe wersje lub migawki, ale nie przywraca bazowego obiektu BLOB. Aby przywrócić podstawowy obiekt BLOB, Podwyższ jego poprzednią wersję, kopiując go do podstawowego obiektu BLOB.

Firma Microsoft zaleca włączenie obsługi wersji i usuwania nietrwałego obiektów BLOB dla kont magazynu w celu zapewnienia optymalnej ochrony danych. Aby uzyskać więcej informacji o korzystaniu z funkcji przechowywania wersji obiektów blob i usuwania nietrwałego, zobacz [przechowywanie wersji obiektów blob i usuwanie nietrwałe](versioning-overview.md#blob-versioning-and-soft-delete).

## <a name="blob-soft-delete-protection-by-operation"></a>Ochrona usuwania nietrwałego obiektu BLOB według operacji

W poniższej tabeli opisano oczekiwane zachowanie operacji usuwania i zapisu, gdy jest włączone usuwanie nietrwałe obiektów blob, z użyciem lub bez obsługi wersji obiektów blob:

| Operacje interfejsu API REST | Włączono usuwanie nietrwałe | Nietrwałe usuwanie i obsługa wersji |
|--|--|--|
| [Usuwanie konta magazynu](/rest/api/storagerp/storageaccounts/delete) | Nie widać żadnej zmiany. Kontenery i obiekty blob w usuniętym koncie nie są możliwe do odzyskania. | Nie widać żadnej zmiany. Kontenery i obiekty blob w usuniętym koncie nie są możliwe do odzyskania. |
| [Usuwanie kontenera](/rest/api/storageservices/delete-container) | Nie widać żadnej zmiany. Nie da się odzyskać obiektów BLOB w usuniętym kontenerze. | Nie widać żadnej zmiany. Nie da się odzyskać obiektów BLOB w usuniętym kontenerze. |
| [Usuwanie obiektu blob](/rest/api/storageservices/delete-blob) | Jeśli jest używany do usuwania obiektu BLOB, ten obiekt BLOB jest oznaczony jako usunięty. <br /><br /> Jeśli jest używany do usuwania migawki obiektu BLOB, migawka jest oznaczona jako nietrwała. | Jeśli obiekt BLOB jest używany do usuwania, bieżąca wersja jest poprzednią wersją, a bieżąca wersja zostanie usunięta. Nie została utworzona nowa wersja i nie są tworzone żadne migawki usunięte przez program.<br /><br /> Jeśli zostanie użyta do usunięcia wersji obiektu BLOB, wersja jest oznaczona jako nietrwała. |
| [Cofanie usunięcia obiektu BLOB](/rest/api/storageservices/delete-blob) | Przywraca obiekt BLOB i wszystkie migawki, które zostały usunięte w okresie przechowywania. | Przywraca obiekt BLOB i wszystkie wersje, które zostały usunięte w okresie przechowywania. |
| [Wstawianie obiektu blob](/rest/api/storageservices/put-blob)<br />[Umieść listę zablokowanych](/rest/api/storageservices/put-block-list)<br />[Kopiowanie obiektu blob](/rest/api/storageservices/copy-blob)<br />[Kopiuj obiekt BLOB z adresu URL](/rest/api/storageservices/copy-blob) | Jeśli wywoływana na aktywnym obiekcie blob, tworzona jest migawka stanu obiektu BLOB przed operacją. <br /><br /> Jeśli wywoływana dla nietrwałego obiektu BLOB, migawka poprzedniego stanu obiektu BLOB jest generowana tylko wtedy, gdy jest zastępowana przez obiekt BLOB tego samego typu. Jeśli obiekt BLOB ma inny typ, zostaną trwale usunięte wszystkie istniejące nietrwałe dane usunięte. | Nowa wersja, która przechwytuje stan obiektu BLOB przed operacją, jest generowana automatycznie. |
| [Umieść blok](/rest/api/storageservices/put-block) | Jeśli jest używany do zatwierdzania bloku do aktywnego obiektu BLOB, nie ma zmian.<br /><br />Jeśli jest używany do zatwierdzania bloku do obiektu BLOB, który jest usuwany niemiękki, tworzony jest nowy obiekt BLOB, a migawka jest generowana automatycznie w celu przechwycenia stanu usuniętego obiektu BLOB. | Nie widać żadnej zmiany. |
| [Umieść stronę](/rest/api/storageservices/put-page)<br />[Umieść stronę na podstawie adresu URL](/rest/api/storageservices/put-page-from-url) | Nie widać żadnej zmiany. Dane stronicowego obiektu BLOB zastępowane lub wyczyszczone przy użyciu tej operacji nie zostały zapisane i nie są możliwe do odzyskania. | Nie widać żadnej zmiany. Dane stronicowego obiektu BLOB zastępowane lub wyczyszczone przy użyciu tej operacji nie zostały zapisane i nie są możliwe do odzyskania. |
| [Dołącz blok](/rest/api/storageservices/append-block)<br />[Dołącz blok z adresu URL](/rest/api/storageservices/append-block-from-url) | Nie widać żadnej zmiany. | Nie widać żadnej zmiany. |
| [Ustawianie właściwości obiektu BLOB](/rest/api/storageservices/set-blob-properties) | Nie widać żadnej zmiany. Nie da się odzyskać właściwości obiektu BLOB z zastępowaniem. | Nie widać żadnej zmiany. Nie da się odzyskać właściwości obiektu BLOB z zastępowaniem. |
| [Ustawianie metadanych obiektu BLOB](/rest/api/storageservices/set-blob-metadata) | Nie widać żadnej zmiany. Nie ma możliwości odzyskania nadpisanych metadanych obiektów BLOB. | Nowa wersja, która przechwytuje stan obiektu BLOB przed operacją, jest generowana automatycznie. |
| [Ustawianie warstwy obiektu blob](/rest/api/storageservices/set-blob-tier) | Podstawowy obiekt BLOB jest przenoszony do nowej warstwy. Wszystkie aktywne lub nietrwałe migawki pozostaną w oryginalnej warstwie. Nie została utworzona migawka usuwania nietrwałego. | Podstawowy obiekt BLOB jest przenoszony do nowej warstwy. Wszystkie aktywne lub nietrwałe wersje są zachowywane w oryginalnej warstwie. Nie zostanie utworzona nowa wersja. |

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

Wszystkie usunięte nietrwałe dane są rozliczane według tej samej stawki co aktywne dane. Nie zostanie naliczona opłata za dane, które zostały trwale usunięte po upływie okresu przechowywania.

Po włączeniu usuwania nietrwałego firma Microsoft zaleca użycie krótkiego okresu przechowywania, aby lepiej zrozumieć, jak ta funkcja wpłynie na rachunek. Minimalny zalecany okres przechowywania wynosi siedem dni.

Włączenie usuwania nietrwałego dla często zamienionych danych może spowodować zwiększenie opłat za pojemność magazynu i zwiększenie opóźnień podczas tworzenia listy obiektów BLOB. Aby wyeliminować ten dodatkowy koszt i czas oczekiwania, można przechowywać często zastąpione dane na osobnym koncie magazynu, w którym jest wyłączone usuwanie nietrwałe.

Nie są naliczane opłaty za transakcje związane z automatycznym generowaniem migawek lub wersji, gdy obiekt BLOB zostanie nadpisany lub usunięty. Opłaty są naliczane za wywołania operacji **usuwania obiektów BLOB** przy użyciu stawki transakcji dla operacji zapisu.

Aby uzyskać więcej informacji na temat cen Blob Storage, zobacz stronę [cennika BLOB Storage](https://azure.microsoft.com/pricing/details/storage/blobs/) .

## <a name="blob-soft-delete-and-virtual-machine-disks"></a>Usuwanie nietrwałego obiektu BLOB i dysków maszyny wirtualnej  

Usuwanie nietrwałe obiektów BLOB jest dostępne dla dysków niezarządzanych w warstwie Premium i standardowa, które są stronicowymi obiektami BLOB w ramach okładek. Usuwanie nietrwałe może ułatwić odzyskiwanie danych usuniętych lub nadpisanych przez operacje **usuwania obiektów BLOB**, **Put obiektów BLOB**, **Put** i **kopiowania obiektów BLOB** .

Dane, które są zastępowane przez wywołanie **strony** , nie są możliwe do odzyskania. Maszyna wirtualna platformy Azure zapisuje dane na dysku niezarządzanym przy użyciu wywołań do **umieszczania na stronie**, dlatego użycie nietrwałego usunięcia umożliwia cofnięcie operacji zapisu na dysku niezarządzanym z maszyny wirtualnej platformy Azure nie jest obsługiwanym scenariuszem.

## <a name="next-steps"></a>Następne kroki

- [Włączanie usuwania nietrwałego dla obiektów blob](./soft-delete-blob-enable.md)
- [Zarządzanie i przywracanie nietrwałych usuniętych obiektów BLOB](soft-delete-blob-manage.md)
- [Przechowywanie wersji obiektów BLOB](versioning-overview.md)