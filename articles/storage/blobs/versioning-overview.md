---
title: Przechowywanie wersji obiektów BLOB
titleSuffix: Azure Storage
description: Przechowywanie wersji magazynu obiektów BLOB automatycznie zachowuje poprzednie wersje obiektu i identyfikuje je za pomocą sygnatur czasowych. Można przywrócić poprzednią wersję obiektu BLOB w celu odzyskania danych w przypadku ich błędnej modyfikacji lub usunięcia.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f104b98c870fe6eee1d32fe656c0bba416cf3700
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259748"
---
# <a name="blob-versioning"></a>Przechowywanie wersji obiektów BLOB

Możesz włączyć przechowywanie wersji magazynu obiektów blob, aby automatycznie obsługiwać poprzednie wersje obiektu.  Po włączeniu obsługi wersji obiektów BLOB można przywrócić wcześniejszą wersję obiektu BLOB, aby odzyskać dane, jeśli są one błędnie modyfikowane lub usuwane.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="recommended-data-protection-configuration"></a>Zalecana konfiguracja ochrony danych

Przechowywanie wersji obiektów BLOB jest częścią kompleksowej strategii ochrony danych dla danych obiektów BLOB. Aby zapewnić optymalną ochronę danych obiektów blob, firma Microsoft zaleca włączenie wszystkich następujących funkcji ochrony danych:

- Przechowywanie wersji obiektów BLOB w celu automatycznego zachowywania poprzednich wersji obiektu BLOB. Po włączeniu obsługi wersji obiektów BLOB można przywrócić wcześniejszą wersję obiektu BLOB, aby odzyskać dane, jeśli są one błędnie modyfikowane lub usuwane. Aby dowiedzieć się, jak włączyć obsługę wersji obiektów blob, zobacz [Włączanie obsługi wersji obiektów blob i zarządzanie nimi](versioning-enable.md).
- Usuwanie nietrwałe kontenera w celu przywrócenia kontenera, który został usunięty. Aby dowiedzieć się, jak włączyć usuwanie nietrwałe kontenera, zobacz [Włączanie i zarządzanie usuwaniem nietrwałym dla kontenerów](soft-delete-container-enable.md).
- Usuwanie nietrwałego obiektu BLOB, Przywracanie usuniętego obiektu BLOB, migawki lub wersji. Aby dowiedzieć się, jak włączyć usuwanie nietrwałe obiektów blob, zobacz [Włączanie i zarządzanie nietrwałego usuwania dla obiektów BLOB](soft-delete-blob-enable.md).

Aby dowiedzieć się więcej na temat zaleceń firmy Microsoft dotyczących ochrony danych, zobacz [Omówienie ochrony danych](data-protection-overview.md).

## <a name="how-blob-versioning-works"></a>Jak działa wersja obiektów BLOB

Wersja przechwytuje stan obiektu BLOB w danym momencie. Po włączeniu obsługi wersji obiektów BLOB dla konta magazynu usługa Azure Storage automatycznie tworzy nową wersję obiektu BLOB za każdym razem, gdy obiekt BLOB jest modyfikowany.

Podczas tworzenia obiektu BLOB z włączoną obsługą wersji, nowy obiekt BLOB jest bieżącą wersją obiektu BLOB (lub podstawowy obiekt BLOB). Jeśli następnie zmodyfikujesz ten obiekt BLOB, usługa Azure Storage utworzy wersję, która przechwytuje stan obiektu BLOB przed jego modyfikacją. Zmodyfikowany obiekt BLOB zostanie nowym bieżącą wersją. Nowa wersja jest tworzona za każdym razem, gdy modyfikujesz obiekt BLOB.

Na poniższym diagramie przedstawiono, jak wersje są tworzone w ramach operacji zapisu, a w jaki sposób można awansować poprzednią wersję jako bieżącą wersję:

:::image type="content" source="media/versioning-overview/blob-versioning-diagram.png" alt-text="Diagram przedstawiający sposób działania wersji obiektów BLOB":::

Po usunięciu obiektu BLOB z włączoną obsługą wersji bieżąca wersja obiektu BLOB zostanie poprzednia i nie będzie już bieżącą wersją. Wszystkie poprzednie wersje obiektu BLOB są utrwalane.

Wersje obiektów BLOB są niezmienne. Nie można zmodyfikować zawartości ani metadanych istniejącej wersji obiektu BLOB.

Duża liczba wersji na obiekt BLOB może zwiększyć opóźnienie operacji tworzenia listy obiektów BLOB. Firma Microsoft zaleca obsługę mniej niż 1000 wersji na obiekt BLOB. Za pomocą zarządzania cyklem życia można automatycznie usuwać stare wersje. Aby uzyskać więcej informacji na temat zarządzania cyklem życia, zobacz [Optymalizowanie kosztów dzięki automatyzowaniu warstw dostępu BLOB Storage platformy Azure](storage-lifecycle-management-concepts.md).

Obsługa wersji obiektów BLOB jest dostępna dla kont ogólnego przeznaczenia w wersji 2, blokowych obiektów blob i BLOB Storage. Konta magazynu z hierarchiczną przestrzenią nazw włączone do użycia z Azure Data Lake Storage Gen2 nie są obecnie obsługiwane.

W wersji 2019-10-10 i nowszej interfejsu API REST usługi Azure Storage obsługiwane jest przechowywanie wersji obiektów BLOB.

> [!IMPORTANT]
> Przechowywanie wersji obiektów BLOB nie pozwala na odzyskanie od przypadkowego usunięcia konta magazynu lub kontenera. Aby zapobiec przypadkowemu usunięciu konta magazynu, Skonfiguruj blokadę zasobu konta magazynu. Aby uzyskać więcej informacji na temat blokowania konta magazynu, zobacz temat [stosowanie blokady Azure Resource Manager na koncie magazynu](../common/lock-account-resource.md).

### <a name="version-id"></a>Identyfikator wersji

Każda wersja obiektu BLOB jest identyfikowana przez identyfikator wersji. Wartość identyfikatora wersji to sygnatura czasowa, w której obiekt BLOB został zaktualizowany. Identyfikator wersji jest przypisywany podczas tworzenia wersji.

Można wykonać operacje odczytu lub usuwania na określonej wersji obiektu BLOB, podając jego identyfikator wersji. W przypadku pominięcia identyfikatora wersji operacja działa w stosunku do bieżącej wersji (podstawowy obiekt BLOB).

W przypadku wywołania operacji zapisu w celu utworzenia lub zmodyfikowania obiektu BLOB usługa Azure Storage zwraca nagłówek *x-MS-Version-ID* w odpowiedzi. Ten nagłówek zawiera identyfikator wersji dla bieżącej wersji obiektu BLOB, który został utworzony przez operację zapisu.

Identyfikator wersji pozostaje taki sam dla okresu istnienia wersji.

### <a name="versioning-on-write-operations"></a>Przechowywanie wersji na operacjach zapisu

Po włączeniu obsługi wersji obiektów BLOB każda operacja zapisu w obiekcie blob tworzy nową wersję. Operacje zapisu obejmują [Put obiekt BLOB](/rest/api/storageservices/put-blob), [Umieść listę zablokowanych](/rest/api/storageservices/put-block-list), [Kopiuj obiekt BLOB](/rest/api/storageservices/copy-blob)i [Ustaw metadane obiektów BLOB](/rest/api/storageservices/set-blob-metadata).

Jeśli operacja zapisu tworzy nowy obiekt BLOB, otrzymany obiekt BLOB jest bieżącą wersją obiektu BLOB. Jeśli operacja zapisu modyfikuje istniejący obiekt BLOB, nowe dane są przechwytywane w zaktualizowanym obiekcie blob, który jest bieżącą wersją, a usługa Azure Storage tworzy wersję, która zapisuje poprzedni stan obiektu BLOB.

Dla uproszczenia diagramy przedstawione w tym artykule wyświetlają identyfikator wersji jako prostą wartość całkowitą. W rzeczywistości identyfikator wersji jest sygnaturą czasową. Bieżąca wersja jest wyświetlana na niebiesko, a wcześniejsze wersje są oznaczone kolorem szarym.

Na poniższym diagramie przedstawiono sposób, w jaki operacje zapisu wpływają na wersje obiektów BLOB. Gdy obiekt BLOB jest tworzony, ten obiekt BLOB jest bieżącą wersją. Po zmodyfikowaniu tego samego obiektu BLOB zostanie utworzona nowa wersja, aby zapisać poprzedni stan obiektu BLOB, a zaktualizowany obiekt BLOB stanie się bieżącą wersją.

:::image type="content" source="media/versioning-overview/write-operations-blob-versions.png" alt-text="Diagram przedstawiający sposób, w jaki operacje zapisu wpływają na obiekty blob z wersjami.":::

> [!NOTE]
> Obiekt BLOB utworzony przed obsługą wersji dla konta magazynu nie ma identyfikatora wersji. Po zmodyfikowaniu tego obiektu BLOB zmodyfikowany obiekt BLOB stanie się bieżącą wersją i zostanie utworzona wersja, aby zapisać stan obiektu BLOB przed aktualizacją. Wersja ma przypisany identyfikator wersji, który jest jego czasem tworzenia.

Po włączeniu obsługi wersji obiektów BLOB dla konta magazynu wszystkie operacje zapisu w blokowych obiektach Blob wyzwalają Tworzenie nowej wersji, z wyjątkiem operacji [Put Block](/rest/api/storageservices/put-block) .

W przypadku stronicowych obiektów blob i dołączania obiektów BLOB tylko podzestaw operacji zapisu wyzwala Tworzenie wersji. Te operacje obejmują:

- [Wstawianie obiektu blob](/rest/api/storageservices/put-blob)
- [Umieść listę zablokowanych](/rest/api/storageservices/put-block-list)
- [Ustawianie metadanych obiektu BLOB](/rest/api/storageservices/set-blob-metadata)
- [Kopiowanie obiektu blob](/rest/api/storageservices/copy-blob)

Następujące operacje nie wyzwalają tworzenia nowej wersji. Aby przechwycić zmiany z tych operacji, zrób ręcznie migawkę:

- [Umieść stronę](/rest/api/storageservices/put-page) (strona obiektów BLOB)
- [Append — blok](/rest/api/storageservices/append-block) (Dołącz obiekt BLOB)

Wszystkie wersje obiektu BLOB muszą być tego samego typu obiektów BLOB. Jeśli obiekt BLOB ma poprzednie wersje, nie można zastąpić obiektu BLOB jednego typu innym typem, chyba że najpierw usuniesz obiekt BLOB i wszystkie jego wersje.

### <a name="versioning-on-delete-operations"></a>Przechowywanie wersji w operacjach usuwania

W przypadku wywołania operacji [usuwania obiektu BLOB](/rest/api/storageservices/delete-blob) bez określenia identyfikatora wersji bieżąca wersja jest w poprzedniej wersji i nie istnieje już bieżąca wersja. Wszystkie istniejące poprzednie wersje obiektu BLOB są zachowywane.

Na poniższym diagramie przedstawiono efekt operacji usuwania na serwerze obiektów blob z wersjami:

:::image type="content" source="media/versioning-overview/delete-versioned-base-blob.png" alt-text="Diagram przedstawiający Usuwanie wersji obiektu BLOB z wersjami.":::

Aby usunąć określoną wersję obiektu BLOB, podaj identyfikator dla tej wersji w operacji usuwania. Jeśli funkcja usuwania nietrwałego obiektów BLOB jest również włączona dla konta magazynu, wersja jest utrzymywana w systemie do momentu, aż upłynie okres przechowywania nietrwałego.

Zapisanie nowych danych do obiektu BLOB powoduje utworzenie nowej bieżącej wersji obiektu BLOB. Nie ma to żadnego skutku, jak pokazano na poniższym diagramie.

:::image type="content" source="media/versioning-overview/recreate-deleted-base-blob.png" alt-text="Diagram przedstawiający ponowne tworzenie wersji obiektu BLOB po usunięciu.":::

### <a name="access-tiers"></a>Poziomy dostępu

Możesz przenieść dowolną wersję blokowego obiektu BLOB, łącznie z bieżącą wersją, do innej warstwy dostępu do obiektów blob, wywołując operację [ustawiania warstwy obiektów BLOB](/rest/api/storageservices/set-blob-tier) . Możesz skorzystać z niższych cen wydajności, przenosząc starsze wersje obiektu BLOB do warstwy chłodna lub archiwalna. Aby uzyskać więcej informacji, zobacz [Azure Blob Storage: warstwy dostępu gorąca, chłodna i archiwalna](storage-blob-storage-tiers.md).

Aby zautomatyzować proces przechodzenia blokowych obiektów BLOB do odpowiedniej warstwy, użyj zarządzania cyklem życia obiektów BLOB. Aby uzyskać więcej informacji na temat zarządzania cyklem życia, zobacz [Zarządzanie cyklem życia usługi Azure Blob Storage](storage-lifecycle-management-concepts.md).

## <a name="enable-or-disable-blob-versioning"></a>Włączanie lub wyłączanie obsługi wersji obiektów BLOB

Aby dowiedzieć się, jak włączyć lub wyłączyć przechowywanie wersji obiektów blob, zobacz [Włączanie obsługi wersji obiektów blob i zarządzanie nimi](versioning-enable.md).

Wyłączenie obsługi wersji obiektów BLOB nie powoduje usunięcia istniejących obiektów blob, wersji ani migawek. Po wyłączeniu obsługi wersji obiektów BLOB wszystkie istniejące wersje pozostają dostępne na koncie magazynu. Nie są następnie tworzone żadne nowe wersje.

Jeśli obiekt BLOB został utworzony lub zmodyfikowany po włączeniu obsługi wersji na koncie magazynu, zastąpienie obiektu BLOB spowoduje utworzenie nowej wersji. Zaktualizowany obiekt BLOB nie jest już bieżącą wersją i nie ma identyfikatora wersji. Wszystkie kolejne aktualizacje obiektu BLOB zastąpią swoje dane bez zapisywania poprzedniego stanu.

Można odczytywać lub usuwać wersje przy użyciu identyfikatora wersji po wyłączeniu obsługi wersji. Możesz również wyświetlić listę wersji obiektu BLOB po wyjściu z wersji.

Na poniższym diagramie przedstawiono sposób modyfikowania obiektu BLOB po wykorzystaniu wersji, który tworzy obiekt BLOB, który nie ma wersji. Wszystkie istniejące wersje skojarzone z obiektem BLOB są utrwalane.

:::image type="content" source="media/versioning-overview/modify-base-blob-versioning-disabled.png" alt-text="Diagram przedstawiający podstawowy obiekt BLOB zmodyfikowany po wyjściu z wyłączonej wersji.":::

## <a name="blob-versioning-and-soft-delete"></a>Przechowywanie wersji obiektów blob i usuwanie nietrwałe

Firma Microsoft zaleca włączenie obsługi wersji i usuwania nietrwałego obiektów BLOB dla kont magazynu w celu zapewnienia optymalnej ochrony danych. Aby uzyskać więcej informacji na temat usuwania nietrwałego obiektów blob, zobacz [usuwanie nietrwałe dla obiektów BLOB usługi Azure Storage](./soft-delete-blob-overview.md).

### <a name="overwriting-a-blob"></a>Zastępowanie obiektu BLOB

Jeśli dla konta magazynu włączono zarówno obsługę wersji obiektów blob, jak i usuwanie nietrwałego obiektu BLOB, zastępowanie obiektu BLOB spowoduje automatyczne utworzenie nowej wersji. Nowa wersja nie jest usuwana nietrwale i nie jest usuwana po wygaśnięciu okresu przechowywania nietrwałego. Nie utworzono żadnych migawek usuniętych z nietrwałego.

### <a name="deleting-a-blob-or-version"></a>Usuwanie obiektu BLOB lub wersji

W przypadku włączenia obsługi wersji i usunięcia nietrwałego dla konta magazynu, po usunięciu obiektu BLOB bieżąca wersja obiektu BLOB zostanie poprzednia. Nie została utworzona nowa wersja i nie są tworzone żadne migawki usunięte przez program. Okres przechowywania nietrwałego usuwania nie obowiązuje dla usuniętego obiektu BLOB.

Usuwanie nietrwałe oferuje dodatkową ochronę przed usunięciem wersji obiektów BLOB. Po usunięciu poprzedniej wersji obiektu BLOB ta wersja jest nietrwała. Wersja niestandardowa jest zachowywana do momentu, gdy upłynie okres przechowywania nietrwałego, w którym punkt ten zostanie trwale usunięty.

Aby usunąć poprzednią wersję obiektu BLOB, wywołaj operację **usuwania obiektu BLOB** i określ identyfikator wersji.

Na poniższym diagramie pokazano, co się dzieje po usunięciu obiektu BLOB lub wersji obiektu BLOB.

:::image type="content" source="media/versioning-overview/soft-delete-historical-version.png" alt-text="Diagram przedstawiający Usuwanie wersji z włączoną opcją usuwania nietrwałego.":::

### <a name="restoring-a-soft-deleted-version"></a>Przywracanie nieusuniętej wersji

Za pomocą operacji usuwania [obiektów BLOB](/rest/api/storageservices/undelete-blob) można przywrócić nieusunięte wersje w okresie przechowywania nietrwałego. Operacja **cofnięcia usunięcia obiektu BLOB** zawsze przywraca wszystkie nietrwałe wersje obiektów BLOB. Nie można przywrócić tylko jednej wersji nietrwałej.

Przywracanie nieusuniętych wersji z użyciem operacji **usuwania obiektów BLOB** nie powoduje awansowania żadnej wersji. Aby przywrócić bieżącą wersję, najpierw Przywróć wszystkie nieusunięte wersje, a następnie użyj operacji [kopiowania obiektu BLOB](/rest/api/storageservices/copy-blob) , aby skopiować poprzednią wersję do nowej bieżącej wersji.

Na poniższym diagramie pokazano, jak przywrócić nieusunięte wersje obiektów BLOB przy użyciu operacji **usuwania obiektów** BLOB oraz jak przywrócić bieżącą wersję obiektu BLOB za pomocą operacji **kopiowania obiektu BLOB** .

:::image type="content" source="media/versioning-overview/undelete-version.png" alt-text="Diagram przedstawiający sposób przywracania wersji nieusuniętych.":::

Po upływie okresu przechowywania danych nietrwałych wszystkie nietrwałe wersje obiektów BLOB są trwale usuwane.

## <a name="blob-versioning-and-blob-snapshots"></a>Obsługa wersji obiektów blob i migawek obiektów BLOB

Migawka obiektu BLOB to kopia tylko do odczytu obiektu BLOB, który jest pobierany w określonym punkcie czasu. Migawki obiektów blob i wersje obiektów BLOB są podobne, ale migawka jest tworzona ręcznie przez użytkownika lub aplikację, podczas gdy wersja obiektu BLOB jest tworzona automatycznie w operacji zapisu lub usuwania po włączeniu obsługi wersji obiektów BLOB dla konta magazynu.

> [!IMPORTANT]
> Firma Microsoft zaleca, aby po włączeniu obsługi wersji obiektów BLOB zaktualizować aplikację w celu zatrzymania tworzenia migawek blokowych obiektów BLOB. Jeśli włączono obsługę wersji dla konta magazynu, wszystkie aktualizacje i usunięcia blokowych obiektów BLOB są przechwytywane i zachowywane przez wersje. Wykonanie migawek nie oferuje żadnej dodatkowej ochrony dla danych blokowych obiektów BLOB w przypadku włączenia obsługi wersji obiektów blob i może zwiększyć koszty i złożoność aplikacji.

### <a name="snapshot-a-blob-when-versioning-is-enabled"></a>Tworzenie migawek obiektu BLOB po włączeniu obsługi wersji

Chociaż nie jest to zalecane, można wykonać migawkę obiektu BLOB, który jest również w wersji. Jeśli nie można zaktualizować aplikacji, aby nie przejmować migawek obiektów BLOB podczas włączania obsługi wersji, aplikacja może obsługiwać zarówno migawki, jak i wersje.

Po utworzeniu migawki obiektu BLOB z uruchomioną wersją zostanie utworzona nowa wersja w tym samym czasie, w którym tworzona jest migawka. Nowa bieżąca wersja jest również tworzona podczas tworzenia migawki.

Na poniższym diagramie przedstawiono działania wykonywane po utworzeniu migawki obiektu BLOB z wersjami. Na diagramie wersje obiektów blob i migawki z IDENTYFIKATORem wersji 2 i 3 zawierają identyczne dane.

:::image type="content" source="media/versioning-overview/snapshot-versioned-blob.png" alt-text="Diagram przedstawiający migawki obiektu BLOB z wersjami.":::

## <a name="authorize-operations-on-blob-versions"></a>Autoryzuj operacje na wersjach obiektów BLOB

Dostęp do wersji obiektów BLOB można autoryzować przy użyciu jednej z następujących metod:

- Za pomocą kontroli dostępu opartej na rolach (Azure RBAC) w celu udzielenia uprawnień do podmiotu zabezpieczeń Azure Active Directory (Azure AD). Firma Microsoft zaleca korzystanie z usługi Azure AD w celu zapewnienia bezpieczeństwa i łatwość użycia. Aby uzyskać więcej informacji na temat korzystania z usługi Azure AD z operacjami obiektów blob, zobacz [Autoryzuj dostęp do obiektów blob i kolejek przy użyciu Azure Active Directory](../common/storage-auth-aad.md).
- Za pomocą sygnatury dostępu współdzielonego (SAS) do delegowania dostępu do wersji obiektów BLOB. Określ identyfikator wersji dla podpisanego typu zasobu `bv` reprezentujący wersję obiektu BLOB, aby utworzyć token sygnatury dostępu współdzielonego dla operacji w określonej wersji. Aby uzyskać więcej informacji na temat sygnatur dostępu współdzielonego, zobacz [udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)](../common/storage-sas-overview.md).
- Przy użyciu kluczy dostępu do konta w celu autoryzacji operacji dla wersji obiektów blob z użyciem klucza współużytkowanego. Aby uzyskać więcej informacji, zobacz [Autoryzuj przy użyciu klucza współużytkowanego](/rest/api/storageservices/authorize-with-shared-key).

Obsługa wersji obiektów BLOB została zaprojektowana w celu ochrony danych przed przypadkowym lub złośliwym usunięciem. Aby podwyższyć poziom ochrony, Usuwanie wersji obiektu BLOB wymaga specjalnych uprawnień. W poniższych sekcjach opisano uprawnienia potrzebne do usunięcia wersji obiektu BLOB.

### <a name="azure-rbac-action-to-delete-a-blob-version"></a>Akcja RBAC platformy Azure do usunięcia wersji obiektu BLOB

W poniższej tabeli przedstawiono działania kontroli RBAC platformy Azure obsługujące usuwanie obiektu BLOB lub wersji obiektu BLOB.

| Opis | Operacja Blob service | Wymagana akcja danych RBAC platformy Azure | Wbudowana obsługa ról platformy Azure |
|----------------------------------------------|------------------------|---------------------------------------------------------------------------------------|-------------------------------|
| Usuwanie bieżącej wersji | Usuwanie obiektu blob | **Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/usuwanie** | Współautor danych obiektu blob usługi Storage |
| Usuwanie poprzedniej wersji | Usuwanie obiektu blob | **Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/deleteBlobVersion/akcja** | Właściciel danych obiektu blob usługi Storage |

### <a name="shared-access-signature-sas-parameters"></a>Parametry sygnatury dostępu współdzielonego (SAS)

Zasób podpisany dla wersji obiektu BLOB to `bv` . Aby uzyskać więcej informacji, zobacz [Tworzenie sygnatury dostępu współdzielonego usługi](/rest/api/storageservices/create-service-sas) lub [Tworzenie konta SAS delegowania](/rest/api/storageservices/create-user-delegation-sas).

W poniższej tabeli przedstawiono uprawnienia wymagane przez sygnaturę dostępu współdzielonego w celu usunięcia wersji obiektu BLOB.

| **Uprawnienie** | **Symbol URI** | **Dozwolone operacje** |
|----------------|----------------|------------------------|
| Usuń         | x              | Usuń wersję obiektu BLOB. |

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

Włączenie obsługi wersji obiektów BLOB może spowodować naliczenie dodatkowych opłat za magazyn danych dla Twojego konta. Podczas projektowania aplikacji ważne jest, aby mieć świadomość, jak te opłaty mogą zostać naliczone, aby można było zminimalizować koszty.

Wersje obiektów blob, takie jak migawki obiektów blob, są rozliczane według tej samej stawki co aktywne dane. Sposób rozliczania wersji zależy od tego, czy jawnie ustawiono warstwę dla podstawowego obiektu BLOB, czy dla którejkolwiek z jego wersji (lub migawek). Aby uzyskać więcej informacji na temat warstw obiektów blob, zobacz [Azure Blob Storage: warstwy dostępu gorąca, chłodna i archiwalna](storage-blob-storage-tiers.md).

Jeśli nie zmieniono warstwy obiektu BLOB lub wersji, opłaty są naliczane za unikatowe bloki danych w ramach tego obiektu BLOB, jego wersje oraz wszystkie migawki, które mogą mieć. Aby uzyskać więcej informacji, zobacz [rozliczenia, gdy warstwa obiektów BLOB nie została jawnie ustawiona](#billing-when-the-blob-tier-has-not-been-explicitly-set).

W przypadku zmiany warstwy obiektu BLOB lub wersji, opłaty są naliczane za cały obiekt, bez względu na to, czy obiekt BLOB i wersja znajdują się ostatecznie w tej samej warstwie. Aby uzyskać więcej informacji, zobacz [rozliczenia, gdy warstwa obiektów BLOB została ustawiona jawnie](#billing-when-the-blob-tier-has-been-explicitly-set).

> [!NOTE]
> Włączenie obsługi wersji danych, które są często zastępowane, może spowodować zwiększenie opłat za pojemność magazynu i zwiększenie opóźnień podczas tworzenia listy. Aby wyeliminować te problemy, przechowuj często zastąpione dane na oddzielnym koncie magazynu z wyłączonymi wersjami.

Aby uzyskać więcej informacji na temat szczegółów rozliczeń dla migawek obiektów blob, zobacz [migawki obiektów BLOB](snapshots-overview.md).

### <a name="billing-when-the-blob-tier-has-not-been-explicitly-set"></a>Rozliczanie, gdy warstwa obiektów BLOB nie została jawnie ustawiona

Jeśli nie ustawisz jawnie warstwy obiektu BLOB dla podstawowego obiektu BLOB lub żadnej z jej wersji, naliczanie opłat za unikatowe bloki lub strony w obiekcie blob, jego wersje oraz wszystkie migawki, które mogą mieć. Dane, które są współużytkowane przez obiekt BLOB i jego wersje, są naliczone tylko raz. Po zaktualizowaniu obiektu BLOB dane w bazowym obiekcie BLOB są rozbieżne od danych przechowywanych w jego wersjach, a opłaty za dane unikatowe są naliczone na blok lub na stronie.

Gdy zastąpisz blok w blokowym obiekcie blob, ten blok jest następnie naliczany jako unikatowy blok. Jest to prawdziwe, nawet jeśli blok ma ten sam identyfikator bloku i te same dane, które znajdują się w poprzedniej wersji. Po ponownym zatwierdzeniu bloku jest on rozbieżny od jego odpowiednikiem w poprzedniej wersji i zostanie naliczona opłata za swoje dane. Te same wartości mają wartość true dla strony w obiekcie blob stronicowania, która jest aktualizowana o identyczne dane.

Usługa BLOB Storage nie ma metody, aby określić, czy dwa bloki zawierają identyczne dane. Każdy przekazany i zatwierdzony blok jest traktowany jako unikatowy, nawet jeśli ma takie same dane i ten sam identyfikator bloku. Ze względu na to, że opłaty są naliczane dla unikatowych bloków, należy pamiętać, że aktualizacja obiektu BLOB po włączeniu obsługi wersji spowoduje dodanie dodatkowych unikatowych bloków i dodatkowych opłat.

Po włączeniu obsługi wersji obiektów BLOB należy wywoływać operacje aktualizacji w blokowych obiektach Blob, aby zaktualizować możliwie najmniejszą liczbę bloków. Operacje zapisu, które zezwalają na szczegółową kontrolę nad blokami, powodują [umieszczenie bloku](/rest/api/storageservices/put-block) i [umieszczenie listy zablokowanych](/rest/api/storageservices/put-block-list). Z drugiej strony operacja [Put obiektu BLOB](/rest/api/storageservices/put-blob) zastępuje całą zawartość obiektu BLOB, co może prowadzić do dodatkowych opłat.

W poniższych scenariuszach pokazano, jak naliczane są opłaty za blokowy obiekt BLOB i jego wersje, gdy warstwa obiektu BLOB nie została jawnie ustawiona.

#### <a name="scenario-1"></a>Scenariusz 1

W scenariuszu 1 obiekt BLOB ma poprzednią wersję. Obiekt BLOB nie został zaktualizowany od czasu utworzenia wersji, więc opłaty są naliczane tylko dla unikatowych bloków 1, 2 i 3.

![Diagram 1 pokazujący rozliczenia dla unikatowych bloków w podstawowym obiekcie blob i poprzedniej wersji.](./media/versioning-overview/versions-billing-scenario-1.png)

#### <a name="scenario-2"></a>Scenariusz 2

W scenariuszu 2 został zaktualizowany jeden blok (blok 3 na diagramie) w obiekcie blob. Mimo że zaktualizowany blok zawiera te same dane i ten sam identyfikator, nie jest on taki sam jak blok 3 w poprzedniej wersji. W związku z tym konto jest obciążane czterema blokami.

![Diagram 2 przedstawiający rozliczenia dla unikatowych bloków w podstawowym obiekcie blob i poprzedniej wersji.](./media/versioning-overview/versions-billing-scenario-2.png)

#### <a name="scenario-3"></a>Scenariusz 3

W scenariuszu 3 obiekt BLOB został zaktualizowany, ale wersja nie została. Blok 3 został zastąpiony blokiem 4 w podstawowym obiekcie blob, ale Poprzednia wersja nadal odzwierciedla blok 3. W związku z tym konto jest obciążane czterema blokami.

![Diagram 3 przedstawiający rozliczenia dla unikatowych bloków w podstawowym obiekcie blob i poprzedniej wersji.](./media/versioning-overview/versions-billing-scenario-3.png)

#### <a name="scenario-4"></a>Scenariusz 4

W scenariuszu 4 podstawowy obiekt BLOB został całkowicie zaktualizowany i nie zawiera żadnego z jego oryginalnych bloków. W związku z tym konto jest obciążane za wszystkie osiem unikatowych bloków &mdash; cztery w podstawowym obiekcie blob i cztery w poprzedniej wersji. Ten scenariusz może wystąpić, jeśli piszesz do obiektu BLOB za pomocą operacji [Put obiektu BLOB](/rest/api/storageservices/put-blob) , ponieważ zastępuje całą zawartość podstawowego obiektu BLOB.

![Diagram 4 przedstawiający rozliczenia dla unikatowych bloków w podstawowym obiekcie blob i poprzedniej wersji.](./media/versioning-overview/versions-billing-scenario-4.png)

### <a name="billing-when-the-blob-tier-has-been-explicitly-set"></a>Rozliczanie, gdy warstwa obiektu BLOB została jawnie ustawiona

Jeśli jawnie ustawisz warstwę obiektów BLOB dla obiektu BLOB lub wersji (lub migawki), zostanie naliczona opłata za pełną długość zawartości obiektu w nowej warstwie, bez względu na to, czy ma on udział w blokach z obiektem w warstwie pierwotnej. Opłata jest naliczana również za pełną długość zawartości najstarszej wersji w oryginalnej warstwie. Wszystkie inne poprzednie wersje lub migawki, które pozostają w oryginalnej warstwie, są obciążane opłatami za unikatowe bloki, które mogą być współużytkowane, zgodnie z opisem w [rozliczeniach, gdy warstwa obiektów BLOB nie została jawnie ustawiona](#billing-when-the-blob-tier-has-not-been-explicitly-set).

#### <a name="moving-a-blob-to-a-new-tier"></a>Przeniesienie obiektu BLOB do nowej warstwy

W poniższej tabeli opisano zachowanie dotyczące rozliczeń dla obiektu BLOB lub wersji, gdy jest on przenoszony do nowej warstwy.

| Gdy warstwa obiektów BLOB jest ustawiona jawnie w... | Następnie opłaty są naliczane za... |
|-|-|
| Podstawowy obiekt BLOB z poprzednią wersją | Podstawowy obiekt BLOB w nowej warstwie i najstarsza wersja w oryginalnej warstwie oraz wszystkie unikatowe bloki w innych wersjach. <sup>1</sup> |
| Podstawowy obiekt BLOB z poprzednią wersją i migawką | Podstawowy obiekt BLOB w nowej warstwie, najstarsza wersja w oryginalnej warstwie oraz najstarsza migawka w oryginalnej warstwie oraz wszystkie unikatowe bloki w innych wersjach lub migawek<sup>1</sup>. |
| Poprzednia wersja | Wersja nowej warstwy i podstawowy obiekt BLOB w oryginalnej warstwie oraz wszystkie unikatowe bloki w innych wersjach. <sup>1</sup> |

<sup>1</sup> Jeśli istnieją inne poprzednie wersje lub migawki, które nie zostały przeniesione z oryginalnej warstwy, te wersje lub migawki są rozliczane na podstawie liczby unikatowych bloków, zgodnie z opisem w [rozliczeniach, gdy warstwa obiektu BLOB nie została jawnie ustawiona](#billing-when-the-blob-tier-has-not-been-explicitly-set).

Na poniższym diagramie pokazano, w jaki sposób obiekty są rozliczane, gdy obiekt BLOB z uruchomioną wersją jest przenoszony do innej warstwy.

:::image type="content" source="media/versioning-overview/versioning-billing-tiers.png" alt-text="Diagram przedstawiający sposób, w jaki obiekty są rozliczane w przypadku jawnego warstwowego obiektu BLOB.":::

Jawnie Ustawianie warstwy dla obiektu BLOB, wersji lub migawki nie można cofnąć. Jeśli przeniesiesz obiekt BLOB do nowej warstwy, a następnie przeniesiesz go z powrotem do jego oryginalnej warstwy, naliczona zostanie opłata za pełną długość zawartości obiektu nawet wtedy, gdy współużytkuje bloki z innymi obiektami w pierwotnej warstwie.

Operacje, które jawnie ustawiają warstwę obiektu BLOB, wersji lub migawki, obejmują:

- [Ustawianie warstwy obiektu blob](/rest/api/storageservices/set-blob-tier)
- [Umieść obiekt BLOB](/rest/api/storageservices/put-blob) z określoną warstwą
- [Umieść listę zablokowanych](/rest/api/storageservices/put-block-list) z określoną warstwą
- [Kopiuj obiekt BLOB](/rest/api/storageservices/copy-blob) z określoną warstwą

#### <a name="deleting-a-blob-when-soft-delete-is-enabled"></a>Usuwanie obiektu BLOB po włączeniu usuwania nietrwałego

Po włączeniu usuwania nietrwałego obiektu BLOB, jeśli usuniesz lub zastąpisz podstawowy obiekt BLOB, dla którego ustawiono warstwę jawnie, wszystkie poprzednie wersje obiektu BLOB usuniętego przez program są rozliczane z pełną długością zawartości. Aby uzyskać więcej informacji na temat współdziałania wersji obiektów blob i usuwania nietrwałego, zobacz [przechowywanie wersji obiektów blob i usuwanie nietrwałe](#blob-versioning-and-soft-delete).

W poniższej tabeli opisano zachowanie dotyczące rozliczeń dla obiektu BLOB, który jest usuwany nietrwale, w zależności od tego, czy włączono lub wyłączono obsługę wersji. Po włączeniu obsługi wersji jest tworzona wersja, gdy obiekt BLOB jest usuwany w sposób nietrwały. Gdy obsługa wersji jest wyłączona, usuwanie obiektów BLOB powoduje utworzenie nietrwałej migawki.

| W przypadku zastąpienia bazowego obiektu BLOB z ustawioną jawnie jego warstwą... | Następnie opłaty są naliczane za... |
|-|-|
| Jeśli są włączone trwałe usuwanie i przechowywanie wersji obiektów BLOB | Wszystkie istniejące wersje o pełnej długości zawartości niezależnie od warstwy. |
| Jeśli usuwanie nietrwałe obiektów BLOB jest włączone, ale przechowywanie wersji jest wyłączone | Wszystkie istniejące nietrwałe migawki usuwania w pełnej długości zawartości niezależnie od warstwy. |

## <a name="see-also"></a>Zobacz też

- [Włączanie obsługi wersji obiektów blob i zarządzanie nimi](versioning-enable.md)
- [Tworzenie migawki obiektu BLOB](/rest/api/storageservices/creating-a-snapshot-of-a-blob)
- [Usuwanie nietrwałe dla obiektów BLOB usługi Azure Storage](./soft-delete-blob-overview.md)