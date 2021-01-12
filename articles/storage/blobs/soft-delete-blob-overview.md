---
title: Usuwanie nietrwałe dla obiektów blob
titleSuffix: Azure Storage
description: Nietrwałe usuwanie obiektów BLOB chroni dane, dzięki czemu można łatwiej odzyskać dane, gdy są one błędnie modyfikowane lub usuwane przez aplikację lub przez innego użytkownika konta magazynu.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/15/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: a2c26c3e41f64a1593a2d3386c76427c0b9682e9
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98127485"
---
# <a name="soft-delete-for-blobs"></a>Usuwanie nietrwałe dla obiektów blob

Nietrwałe usuwanie obiektów BLOB chroni dane przed przypadkowym lub błędnym modyfikacją lub usunięciem. Gdy usuwanie nietrwałe dla obiektów BLOB jest włączone dla konta magazynu, obiekty blob, wersje obiektów blob i migawki na tym koncie magazynu mogą zostać odzyskane po ich usunięciu w okresie przechowywania określonym przez użytkownika.

Jeśli istnieje możliwość, że dane mogą zostać przypadkowo zmodyfikowane lub usunięte przez aplikację lub innego użytkownika konta magazynu, firma Microsoft zaleca włączenie usuwania nietrwałego. Aby uzyskać więcej informacji na temat włączania usuwania nietrwałego, zobacz [enable and Manage unsoft Delete for Blobs](./soft-delete-blob-enable.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="about-soft-delete-for-blobs"></a>Informacje o nietrwałej usunięciu dla obiektów BLOB

Po włączeniu usuwania nietrwałego dla obiektów BLOB na koncie magazynu można odzyskiwać obiekty po ich usunięciu w określonym okresie przechowywania danych. Ta ochrona rozciąga się na wszystkie obiekty blob (blokowe obiekty blob, Dołącz obiekty blob lub stronicowe obiekty blob), które są wymazywane jako wynik zastępowania.

Jeśli dane w istniejącym obiekcie blob lub migawce zostaną usunięte podczas włączania usuwania nietrwałego obiektów blob, ale funkcja obsługi wersji obiektów BLOB nie jest włączona, zostanie wygenerowana nietrwała migawka w celu zapisania stanu nadpisanych danych. Po upływie określonego okresu przechowywania obiekt zostanie trwale usunięty.

Jeśli na koncie magazynu są włączone wersje obiektów blob i usuwanie nietrwałego obiektu BLOB, usunięcie obiektu BLOB spowoduje utworzenie nowej wersji zamiast migawki nieusuniętej. Nowa wersja nie jest usuwana nietrwale i nie jest usuwana po wygaśnięciu okresu przechowywania nietrwałego. W okresie przechowywania można przywrócić nietrwałe wersje obiektów BLOB przez wywołanie operacji [usuwania obiektu BLOB](/rest/api/storageservices/undelete-blob) . Obiekt BLOB można następnie przywrócić z jednej z jego wersji, wywołując operację [kopiowania obiektu BLOB](/rest/api/storageservices/copy-blob) . Aby uzyskać więcej informacji o korzystaniu z funkcji przechowywania wersji obiektów blob i usuwania nietrwałego, zobacz [przechowywanie wersji obiektów blob i usuwanie nietrwałe](versioning-overview.md#blob-versioning-and-soft-delete).

Usunięte obiekty nietrwałe są niewidoczne, chyba że są jawnie wymienione.

Usuwanie nietrwałe obiektów BLOB jest zgodne z poprzednimi wersjami, więc nie musisz wprowadzać żadnych zmian w aplikacjach, aby korzystać z ochrony oferowanej przez tę funkcję. Jednak [odzyskiwanie danych](#recovery) wprowadza nowy, **cofający Usuwanie interfejsu API obiektów BLOB** .

Usuwanie nietrwałe obiektów BLOB jest dostępne zarówno dla nowych, jak i istniejących kont w celu ogólnego przeznaczenia w wersji 2, ogólnego przeznaczenia i usługi BLOB Storage. Obsługiwane są zarówno typy kont w warstwie Standardowa, jak i Premium. Funkcja usuwania nietrwałego obiektów BLOB jest dostępna dla wszystkich warstw magazynowania, w tym gorąca, chłodna i archiwalna. Usuwanie nietrwałe jest dostępne dla dysków niezarządzanych, które są stronicowymi obiektami BLOB w ramach okładek, ale nie są dostępne dla dysków zarządzanych.

### <a name="configuration-settings"></a>Ustawienia konfiguracji

Podczas tworzenia nowego konta usuwanie nietrwałe jest domyślnie wyłączone. Usuwanie nietrwałe jest również domyślnie wyłączone dla istniejących kont magazynu. W dowolnym momencie możesz włączyć lub wyłączyć usuwanie nietrwałe dla konta magazynu.

Po włączeniu usuwania nietrwałego należy skonfigurować okres przechowywania. Okres przechowywania wskazuje ilość czasu, przez który nietrwałe usunięte dane są przechowywane i dostępne do odzyskania. W przypadku obiektów, które są jawnie usuwane, zegar okresu przechowywania jest uruchamiany po usunięciu danych. W przypadku nietrwałych usuniętych wersji lub migawek wygenerowanych przez funkcję usuwania nietrwałego, gdy dane są zastępowane, Zegar rozpocznie się po wygenerowaniu wersji lub migawki. Okres przechowywania może wynosić od 1 do 365 dni.

Okres przechowywania nietrwałego usuwania można zmienić w dowolnym momencie. Zaktualizowany okres przechowywania dotyczy tylko nowo usuniętych danych. Wcześniej usunięte dane wygasną na podstawie okresu przechowywania, który został skonfigurowany podczas usuwania tych danych. Próba usunięcia nietrwałego usuniętego obiektu nie ma wpływu na jego czas wygaśnięcia.

Wyłączenie usuwania nietrwałego pozwala nadal uzyskiwać dostęp do nietrwałych danych usuniętych i odzyskiwać je na koncie magazynu, które zostało zapisane podczas włączania funkcji.

### <a name="saving-deleted-data"></a>Zapisywanie usuniętych danych

Nietrwałe usuwanie zachowuje dane w wielu przypadkach, w których obiekty są usuwane lub zastępowane.

Gdy obiekt BLOB zostanie zastąpiony przy użyciu funkcji **Put BLOB**, **Put Block list** lub **copy BLOB**, wersja lub migawka stanu obiektu BLOB przed operacją zapisu jest generowana automatycznie. Ten obiekt jest niewidoczny, jeśli nie zostały jawnie wymienione obiekty nietrwałe. Zobacz sekcję [odzyskiwanie](#recovery) , aby dowiedzieć się, jak wyświetlić nietrwałe obiekty usunięte.

![Diagram przedstawiający sposób przechowywania migawek obiektów BLOB w trakcie ich nadpisania przy użyciu funkcji Put BLOB, Put Block list lub Copy BLOB.](media/soft-delete-blob-overview/storage-blob-soft-delete-overwrite.png)

*Usunięte dane nietrwałe są szare, a aktywne dane są niebieskie. Ostatnio zapisywane dane są wyświetlane poniżej starszych danych. Gdy B0 jest zastępowany przez B1, generowana jest nietrwałe migawka B0. Gdy B1 zostanie zastąpiona przez B2, generowana jest niewygładzona migawka z B1.*

> [!NOTE]  
> Funkcja usuwania nietrwałego umożliwia zastępowanie ochrony operacji kopiowania, gdy jest włączona dla konta docelowego obiektu BLOB.

> [!NOTE]  
> Usuwanie nietrwałe nie zapewnia ochrony przed zastąpieniem obiektów BLOB w warstwie archiwum. Jeśli obiekt BLOB w archiwum zostanie zastąpiony nowym obiektem BLOB w dowolnej warstwie, zastąpiony obiekt BLOB zostanie trwale wygasł.

Gdy **obiekt BLOB jest usuwany** w migawce, migawka jest oznaczona jako nietrwała. Nie Wygenerowano nowej migawki.

![Diagram przedstawiający sposób usuwania nietrwałych migawek obiektów BLOB podczas korzystania z usuwania obiektów BLOB.](media/soft-delete-blob-overview/storage-blob-soft-delete-explicit-delete-snapshot.png)

*Usunięte dane nietrwałe są szare, a aktywne dane są niebieskie. Ostatnio zapisywane dane są wyświetlane poniżej starszych danych. Po wywołaniu **obiektu BLOB Snapshot** B0 stanie się migawką, a B1 jest aktywnym stanem obiektu BLOB. Po usunięciu migawki B0 jest ona oznaczona jako nietrwała.*

Kiedy **obiekt BLOB Delete** jest wywoływany na bazowym obiekcie BLOB (dowolny obiekt BLOB, który nie jest samym migawką), ten obiekt BLOB jest oznaczony jako usunięty. Spójne z poprzednim zachowaniem, wywołanie **usuwania obiektów BLOB** na obiekcie blob, który ma aktywne migawki, zwraca błąd. Wywołanie metody **delete BLOB** na obiekcie blob z nietrwałymi usuniętymi migawkami nie powoduje zwrócenia błędu. Nadal można usunąć obiekt BLOB i wszystkie jego migawki w ramach jednej operacji, gdy jest włączona funkcja usuwania nietrwałego. Spowoduje to oznaczenie podstawowego obiektu BLOB i migawek jako nietrwałego usunięcia.

![Diagram przedstawiający, co się dzieje w przypadku wywołania elementu "Usuń blog" na bazowym obiekcie blob.](media/soft-delete-blob-overview/storage-blob-soft-delete-explicit-include.png)

*Usunięte dane nietrwałe są szare, a aktywne dane są niebieskie. Ostatnio zapisywane dane są wyświetlane poniżej starszych danych. W tym miejscu jest wykonywane wywołanie **usuwania obiektu BLOB** , aby usunąć B2 i wszystkie skojarzone migawki. Aktywne obiekty blob, B2 i wszystkie skojarzone migawki są oznaczane jako nietrwałe usunięte.*

> [!NOTE]  
> Po nadpisaniu nietrwałego usuniętego obiektu BLOB nietrwała usunięta migawka stanu obiektu BLOB przed operacją zapisu jest generowana automatycznie. Nowy obiekt BLOB dziedziczy warstwę nadpisywanego obiektu BLOB.

Usuwanie nietrwałe nie zapisuje danych w przypadku usunięcia kontenera lub konta, a także gdy metadane obiektów blob i właściwości obiektu BLOB są zastępowane. Aby chronić konto magazynu przed usunięciem, można skonfigurować blokadę przy użyciu Azure Resource Manager. Aby uzyskać więcej informacji, zobacz artykuł Azure Resource Manager [Zablokuj zasoby, aby zapobiec nieoczekiwanym zmianom](../../azure-resource-manager/management/lock-resources.md).

W poniższej tabeli przedstawiono oczekiwane zachowanie podczas włączania usuwania nietrwałego:

| Operacja interfejsu API REST | Typ zasobu | Opis | Zmiana w zachowaniu |
|--------------------|---------------|-------------|--------------------|
| [Usuwanie](/rest/api/storagerp/StorageAccounts/Delete) | Konto | Usuwa konto magazynu, w tym wszystkie kontenery i obiekty blob, które zawiera.                           | Nie widać żadnej zmiany. Kontenery i obiekty blob w usuniętym koncie nie są możliwe do odzyskania. |
| [Usuwanie kontenera](/rest/api/storageservices/delete-container) | Kontener | Usuwa kontener, w tym wszystkie obiekty blob, które zawiera. | Nie widać żadnej zmiany. Nie da się odzyskać obiektów BLOB w usuniętym kontenerze. |
| [Wstawianie obiektu blob](/rest/api/storageservices/put-blob) | Blokowe, dołączanie i stronicowe obiekty blob | Tworzy nowy obiekt BLOB lub zastępuje istniejący obiekt BLOB w kontenerze | Jeśli jest używany do zastępowania istniejącego obiektu BLOB, automatycznie generowana jest migawka stanu obiektu BLOB przed wywołaniem. Dotyczy to również wcześniej nietrwałego usuniętego obiektu BLOB, jeśli i tylko wtedy, gdy jest zastępowany przez obiekt BLOB tego samego typu (blok, dołączanie lub strona). Jeśli zostanie on zastąpiony przez obiekt BLOB innego typu, wszystkie istniejące nietrwałe dane usunięte zostaną trwale wygasłe. |
| [Usuwanie obiektu blob](/rest/api/storageservices/delete-blob) | Blokowe, dołączanie i stronicowe obiekty blob | Oznacza obiekt BLOB lub migawkę obiektu BLOB do usunięcia. Obiekt BLOB lub migawka został później usunięty podczas wyrzucania elementów bezużytecznych | Jeśli zostanie użyta do usunięcia migawki obiektu BLOB, ta migawka jest oznaczona jako nietrwała. Jeśli jest używany do usuwania obiektu BLOB, ten obiekt BLOB jest oznaczony jako usunięty. |
| [Kopiowanie obiektu blob](/rest/api/storageservices/copy-blob) | Blokowe, dołączanie i stronicowe obiekty blob | Kopiuje źródłowy obiekt BLOB do docelowego obiektu BLOB na tym samym koncie magazynu lub na innym koncie magazynu. | Jeśli jest używany do zastępowania istniejącego obiektu BLOB, automatycznie generowana jest migawka stanu obiektu BLOB przed wywołaniem. Dotyczy to również wcześniej nietrwałego usuniętego obiektu BLOB, jeśli i tylko wtedy, gdy jest zastępowany przez obiekt BLOB tego samego typu (blok, dołączanie lub strona). Jeśli zostanie on zastąpiony przez obiekt BLOB innego typu, wszystkie istniejące nietrwałe dane usunięte zostaną trwale wygasłe. |
| [Umieść blok](/rest/api/storageservices/put-block) | Blokowe obiekty blob | Tworzy nowy blok, który ma zostać przekazany jako część blokowego obiektu BLOB. | Jeśli używany do zatwierdzania bloku do obiektu BLOB, który jest aktywny, nie ma zmian. Jeśli jest używany do zatwierdzania bloku do obiektu BLOB, który jest usuwany nietrwale, tworzony jest nowy obiekt BLOB, a migawka jest generowana automatycznie w celu przechwycenia stanu nietrwałego usuniętego obiektu BLOB. |
| [Umieść listę zablokowanych](/rest/api/storageservices/put-block-list) | Blokowe obiekty blob | Zatwierdza obiekt BLOB przez określenie zestawu identyfikatorów bloków, które składają się na blokowy obiekt BLOB. | Jeśli jest używany do zastępowania istniejącego obiektu BLOB, automatycznie generowana jest migawka stanu obiektu BLOB przed wywołaniem. Dotyczy to również poprzednio nietrwałego usuniętego obiektu BLOB, jeśli i tylko wtedy, gdy jest to blokowy obiekt BLOB. Jeśli zostanie on zastąpiony przez obiekt BLOB innego typu, wszystkie istniejące nietrwałe dane usunięte zostaną trwale wygasłe. |
| [Umieść stronę](/rest/api/storageservices/put-page) | Stronicowe obiekty blob | Zapisuje zakres stron na stronie obiektu BLOB. | Nie widać żadnej zmiany. Dane stronicowego obiektu BLOB zastępowane lub wyczyszczone przy użyciu tej operacji nie zostały zapisane i nie są możliwe do odzyskania. |
| [Dołącz blok](/rest/api/storageservices/append-block) | Obiekty blob dołączania | Zapisuje blok danych na końcu dołączanego obiektu BLOB | Nie widać żadnej zmiany. |
| [Ustawianie właściwości obiektu BLOB](/rest/api/storageservices/set-blob-properties) | Blokowe, dołączanie i stronicowe obiekty blob | Ustawia wartości dla właściwości systemu zdefiniowanych dla obiektu BLOB. | Nie widać żadnej zmiany. Nie da się odzyskać właściwości obiektu BLOB z zastępowaniem. |
| [Ustawianie metadanych obiektu BLOB](/rest/api/storageservices/set-blob-metadata) | Blokowe, dołączanie i stronicowe obiekty blob | Ustawia metadane zdefiniowane przez użytkownika dla określonego obiektu BLOB jako jedną lub więcej par nazwa-wartość. | Nie widać żadnej zmiany. Nie ma możliwości odzyskania nadpisanych metadanych obiektów BLOB. |

Należy zauważyć, że wywołanie funkcji **Put Page** to overwrite lub Clear zakresy obiektu BLOB Page nie spowoduje automatycznego generowania migawek. Dyski maszyny wirtualnej są obsługiwane przez stronicowe obiekty blob i wykorzystują **stronę Put** do zapisu danych.

### <a name="recovery"></a>Odzyskiwania

Wywołanie operacji [cofnięcia usunięcia obiektu](/rest/api/storageservices/undelete-blob) BLOB dla nietrwałego, usuniętego obiektu typu Base spowoduje przywrócenie tego elementu i wszystkich skojarzonych nietrwałych usuniętych migawek jako aktywnych. Wywołanie operacji **cofnięcia usunięcia obiektu BLOB** na aktywnym podstawowym obiekcie blob spowoduje przywrócenie wszystkich skojarzonych usuniętych nietrwałych migawek jako aktywnych. Gdy migawki są przywracane jako aktywne, wyglądają jak migawki wygenerowane przez użytkownika; nie zastępuje podstawowego obiektu BLOB.

Aby przywrócić obiekt BLOB do określonej nietrwałej migawki usuniętej, można wywołać **cofnięcie obiektu BLOB** na podstawowym obiekcie blob. Następnie można skopiować migawkę za pośrednictwem teraz aktywnego obiektu BLOB. Możesz również skopiować migawkę do nowego obiektu BLOB.

![Diagram przedstawiający, co się stanie, gdy zostanie użyty cofający usunięcie obiektu BLOB.](media/soft-delete-blob-overview/storage-blob-soft-delete-recover.png)

*Usunięte dane nietrwałe są szare, a aktywne dane są niebieskie. Ostatnio zapisywane dane są wyświetlane poniżej starszych danych. W tym miejscu **obiekt BLOB Undelete** jest wywoływany w obiekcie blob B, a tym samym przywracasz podstawowy obiekt BLOB, B1 i wszystkie skojarzone migawki, tutaj tylko B0 jako aktywny. W drugim kroku B0 jest kopiowany przez podstawowy obiekt BLOB. Ta operacja kopiowania generuje niewygładzoną migawkę z B1.*

Aby wyświetlić nietrwałe usunięte obiekty blob i migawki obiektów blob, możesz dołączyć usunięte dane do **listy obiektów BLOB**. Można wybrać opcję wyświetlania tylko usuniętych nietrwałych obiektów blob lub do dołączenia nietrwałych usuniętych migawek obiektów BLOB. W przypadku wszystkich nietrwałych danych usuniętych można wyświetlić czas, w którym dane zostały usunięte, a także liczbę dni, po których dane będą trwale wygasłe.

### <a name="example"></a>Przykład

Poniżej znajduje się dane wyjściowe konsoli skryptu .NET, który przekazuje, zastępuje, migawka, usuwa i przywraca obiekt BLOB o nazwie *HelloWorld* po włączeniu usuwania nietrwałego:

```bash
Upload:
- HelloWorld (is soft deleted: False, is snapshot: False)

Overwrite:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Snapshot:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Delete (including snapshots):
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: False)

Undelete:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Copy a snapshot over the base blob:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)
```

Zapoznaj się z sekcją [następne kroki](#next-steps) , aby uzyskać wskaźnik do aplikacji, która wygenerowała te dane wyjściowe.

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

Wszystkie usunięte nietrwałe dane są rozliczane według tej samej stawki co aktywne dane. Nie zostanie naliczona opłata za dane, które zostały trwale usunięte po upływie skonfigurowanego okresu przechowywania. Aby uzyskać bardziej szczegółowy szczegółowe migawek i sposób ich naliczania, zobacz [Opis sposobu naliczania opłat za migawki](./snapshots-overview.md).

Nie będą naliczane opłaty za transakcje związane z automatyczną generowaniem migawek. Opłaty są naliczane za **cofanie usuwania transakcji obiektów BLOB** według stawki za operacje zapisu.

Aby uzyskać więcej szczegółowych informacji na temat cen usługi Azure Blob Storage ogólnie, zapoznaj się z [cennikiem usługi azure BLOB Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

Po pierwszym włączeniu usuwania nietrwałego firma Microsoft zaleca użycie krótkiego okresu przechowywania, aby lepiej zrozumieć, w jaki sposób funkcja wpłynie na rachunek.

Włączenie usuwania nietrwałego dla często zamienionych danych może spowodować zwiększenie opłat za pojemność magazynu i zwiększenie opóźnień podczas tworzenia listy obiektów BLOB. Aby wyeliminować ten dodatkowy koszt i czas oczekiwania, można przechowywać często zastąpione dane na osobnym koncie magazynu, w którym jest wyłączone usuwanie nietrwałe.

## <a name="faq"></a>Często zadawane pytania

### <a name="can-i-use-the-set-blob-tier-api-to-tier-blobs-with-soft-deleted-snapshots"></a>Czy można użyć zestawu API warstwy obiektów BLOB do warstwy obiektów blob z nietrwałymi usuniętymi migawkami?

Tak. Usunięte nietrwałe migawki pozostaną w oryginalnej warstwie, ale podstawowy obiekt BLOB zostanie przeniesiony do nowej warstwy.

### <a name="premium-storage-accounts-have-a-per-blob-snapshot-limit-of-100-do-soft-deleted-snapshots-count-toward-this-limit"></a>Dla kont magazynu w warstwie Premium obowiązuje limit migawek obiektów BLOB równy 100. Czy liczba nietrwałych usuniętych migawek zbliża się do tego limitu?

Nie. nietrwałe usunięte migawki nie są wliczane do tego limitu.

### <a name="if-i-delete-an-entire-account-or-container-with-soft-delete-turned-on-will-all-associated-blobs-be-saved"></a>Czy usunięcie całego konta lub kontenera z włączonym usuwaniem nietrwałego spowoduje zapisanie wszystkich skojarzonych obiektów BLOB?

Nie, jeśli usuniesz całe konto lub kontener, wszystkie skojarzone obiekty blob zostaną trwale usunięte. Aby uzyskać więcej informacji na temat ochrony konta magazynu przed przypadkowym usunięciem, zobacz [blokowanie zasobów, aby zapobiec nieoczekiwanym zmianom](../../azure-resource-manager/management/lock-resources.md).

### <a name="can-i-view-capacity-metrics-for-deleted-data"></a>Czy można wyświetlić metryki pojemności dla usuniętych danych?

Nietrwałe usunięte dane są dołączane jako część całkowitej pojemności konta magazynu. Aby uzyskać więcej informacji o śledzeniu i monitorowaniu pojemności magazynu, zobacz [analityka magazynu](../common/storage-analytics.md).

### <a name="can-i-read-and-copy-out-soft-deleted-snapshots-of-my-blob"></a>Czy mogę odczytywać i kopiować nietrwałe usunięte migawki mojego obiektu BLOB?  

Tak, ale najpierw należy wywołać cofanie usunięcia obiektu BLOB.

### <a name="is-soft-delete-available-for-virtual-machine-disks"></a>Czy dla dysków maszyny wirtualnej jest dostępne usuwanie nietrwałe?  

Usuwanie nietrwałe jest dostępne dla dysków niezarządzanych w warstwie Premium i standardowa, które są stronicowymi obiektami BLOB w ramach okładek. Funkcja usuwania nietrwałego ułatwia odzyskanie danych usuniętych przez operacje **usuwania obiektów BLOB**, **Put obiektów BLOB**, **umieszczenia bloków list** i **kopiowania obiektów BLOB** . Dane zastąpione przez wywołanie **strony** nie są możliwe do odzyskania.

Maszyna wirtualna platformy Azure zapisuje dane na dysku niezarządzanym przy użyciu wywołań do **umieszczania na stronie**, dlatego użycie nietrwałego usunięcia umożliwia cofnięcie operacji zapisu na dysku niezarządzanym z maszyny wirtualnej platformy Azure nie jest obsługiwanym scenariuszem.

### <a name="do-i-need-to-change-my-existing-applications-to-use-soft-delete"></a>Czy muszę zmienić istniejące aplikacje, aby użyć usuwania nietrwałego?

Można korzystać z usuwania nietrwałego niezależnie od używanej wersji interfejsu API. Jednak w celu wyświetlenia i odzyskania nietrwałych usuniętych obiektów blob i migawek obiektów BLOB konieczne będzie użycie wersji 2017-07-29 [interfejsu API REST usługi Azure Storage](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) lub nowszego. Firma Microsoft zaleca, aby zawsze używać najnowszej wersji interfejsu API usługi Azure Storage.

## <a name="next-steps"></a>Następne kroki

- [Włączanie usuwania nietrwałego dla obiektów blob](./soft-delete-blob-enable.md)
- [Przechowywanie wersji obiektów BLOB](versioning-overview.md)