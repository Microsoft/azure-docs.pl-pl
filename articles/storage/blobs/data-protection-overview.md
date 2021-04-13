---
title: Omówienie ochrony danych
titleSuffix: Azure Storage
description: Opcje ochrony danych dostępne dla Blob Storage i Azure Data Lake Storage Gen2 danych umożliwiają ochronę danych przed ich usunięciem lub zastąpieniem. Jeśli konieczne jest odzyskanie danych, które zostały usunięte lub nadpisane, ten przewodnik może ułatwić wybranie opcji odzyskiwania, która jest Najlepsza dla danego scenariusza.
services: storage
author: tamram
ms.service: storage
ms.date: 04/09/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: prishet
ms.subservice: common
ms.openlocfilehash: 90c83397089b77d30694041a37debc0731ea2a38
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304260"
---
# <a name="data-protection-overview"></a>Omówienie ochrony danych

Usługa Azure Storage zapewnia ochronę danych Blob Storage i Azure Data Lake Storage Gen2, która ułatwia przygotowanie się do scenariuszy, w których dane zostały usunięte lub nadpisywane. Należy pamiętać o tym, jak najlepiej chronić dane przed wystąpieniem zdarzenia, które może go złamać. Ten przewodnik może pomóc w podjęciu decyzji z wyprzedzeniem, które funkcje ochrony danych są wymagane przez scenariusz, oraz sposobu ich implementacji. Jeśli konieczne jest odzyskanie danych, które zostały usunięte lub nadpisane, ten przegląd zawiera również wskazówki dotyczące dalszego korzystania z tego scenariusza.

W dokumentacji usługi Azure Storage *Ochrona danych* odnosi się do strategii ochrony konta magazynu i danych w ramach tej ochrony przed ich usunięciem lub zmodyfikowaniem bądź przywróceniem danych po ich usunięciu lub zmodyfikowaniu. Usługa Azure Storage oferuje również opcje *odzyskiwania po awarii*, w tym wiele poziomów nadmiarowości, chroniące dane przed awarią usługi ze względu na problemy ze sprzętem lub klęski żywiołowe, a także tryb failover zarządzany przez klienta w przypadku niedostępności centrum danych w regionie podstawowym. Aby uzyskać więcej informacji o tym, jak dane są chronione przed awarią usługi, zobacz [odzyskiwanie po awarii](#disaster-recovery).

## <a name="recommendations-for-basic-data-protection"></a>Zalecenia dotyczące podstawowej ochrony danych

Jeśli szukasz podstawowego zakresu ochrony danych dla konta magazynu i danych, które zawiera, firma Microsoft zaleca wykonanie następujących kroków, aby zacząć od:

- Skonfiguruj blokadę Azure Resource Manager na koncie magazynu, aby chronić konto przed usunięciem lub zmianą konfiguracji. [Dowiedz się więcej...](../common/lock-account-resource.md)
- Włącz nietrwałe usunięcie kontenera dla konta magazynu w celu odzyskania usuniętego kontenera i jego zawartości. [Dowiedz się więcej...](soft-delete-container-enable.md)
- Zapisz stan obiektu BLOB w regularnych odstępach czasu:
  - W przypadku obciążeń Blob Storage należy włączyć obsługę wersji obiektów blob, aby automatycznie zapisywał stan danych za każdym razem, gdy obiekt BLOB zostanie nadpisany. [Dowiedz się więcej...](versioning-enable.md)
  - W przypadku obciążeń Azure Data Lake Storage wykonaj ręcznie migawki w celu zapisania stanu danych w konkretnym momencie. [Dowiedz się więcej...](snapshots-overview.md)

Te opcje, a także dodatkowe opcje ochrony danych dla innych scenariuszy, zostały opisane bardziej szczegółowo w poniższej sekcji.

Aby zapoznać się z omówieniem kosztów związanych z tymi funkcjami, zobacz [Podsumowanie zagadnień dotyczących kosztów](#summary-of-cost-considerations).

## <a name="overview-of-data-protection-options"></a>Przegląd opcji ochrony danych

Poniższa tabela zawiera podsumowanie opcji dostępnych w usłudze Azure Storage na potrzeby typowych scenariuszy ochrony danych. Wybierz scenariusze, które mają zastosowanie do danej sytuacji, aby dowiedzieć się więcej o dostępnych opcjach. Należy pamiętać, że nie wszystkie funkcje są teraz dostępne dla kont magazynu z włączoną hierarchiczną przestrzenią nazw.

| Scenariusz | Opcja ochrony danych | Zalecenia | Korzyść ochrony | Dostępne dla Data Lake Storage |
|--|--|--|--|--|
| Zapobiegaj usunięciu lub zmodyfikowaniu konta magazynu. | Blokada Azure Resource Manager<br />[Dowiedz się więcej...](../common/lock-account-resource.md) | Zablokuj wszystkie konta magazynu z blokadą Azure Resource Manager, aby zapobiec usunięciu konta magazynu. | Chroni konto magazynu przed usunięciem lub zmianą konfiguracji.<br /><br />Nie chroni kontenerów ani obiektów BLOB na koncie przed ich usunięciem lub zastąpieniem. | Tak |
| Zapobiegaj usuwaniu i modyfikowaniu kontenera oraz jego obiektów BLOB dla interwału, który kontrolujesz. | Zasady niezmienności w kontenerze<br />[Dowiedz się więcej...](storage-blob-immutable-storage.md) | Ustaw zasady niezmienności w kontenerze, aby chronić dokumenty o krytycznym znaczeniu dla firmy, na przykład w celu spełnienia wymagań prawnych lub przepisów dotyczących zgodności. | Chroni kontener i jego obiekty blob ze wszystkich usunięć i nadpisań.<br /><br />Gdy obowiązuje Blokada prawna lub zablokowana zasada przechowywania oparta na czasie, konto magazynu również jest chronione przed usunięciem. Kontenery, dla których nie ustawiono zasad niezmiennościymi, nie są chronione przed usunięciem. | Tak, w wersji zapoznawczej |
| Przywracanie usuniętego kontenera w określonym interwale. | Usuwanie nietrwałe kontenera (wersja zapoznawcza)<br />[Dowiedz się więcej...](soft-delete-container-overview.md) | Włącz usuwanie nietrwałe kontenera dla wszystkich kont magazynu z minimalnym interwałem przechowywania wynoszącym 7 dni.<br /><br />Włącz obsługę wersji obiektów blob oraz usuwanie nietrwałego obiektu BLOB z użyciem nietrwałego usuwania kontenera, aby chronić poszczególne obiekty blob w kontenerze.<br /><br />Kontenery magazynu, które wymagają różnych okresów przechowywania na oddzielnych kontach magazynu. | Usunięty kontener i jego zawartość mogą zostać przywrócone w okresie przechowywania.<br /><br />Można przywrócić tylko operacje na poziomie kontenera (np. [usuwanie kontenerów](/rest/api/storageservices/delete-container)). Usuwanie nietrwałe kontenera nie pozwala na przywrócenie pojedynczego obiektu BLOB w kontenerze, jeśli ten obiekt BLOB zostanie usunięty. | Tak, w wersji zapoznawczej |
| Automatycznie Zapisz stan obiektu BLOB w poprzedniej wersji, gdy zostanie on nadpisany. | Przechowywanie wersji obiektów BLOB<br />[Dowiedz się więcej...](versioning-overview.md) | Włącz przechowywanie wersji obiektów BLOB wraz z usuwaniem nietrwałym kontenera i usuwaniem nietrwałym obiektów BLOB dla kont magazynu, dla których potrzebna jest optymalna ochrona danych obiektów BLOB.<br /><br />Przechowuj dane obiektów blob, które nie wymagają przechowywania wersji na osobnym koncie, aby ograniczyć koszty. | Każda operacja zapisu obiektu BLOB tworzy nową wersję. Bieżąca wersja obiektu BLOB może zostać przywrócona z poprzedniej wersji, jeśli bieżąca wersja została usunięta lub zastąpiona. | Nie |
| Przywracanie usuniętego obiektu BLOB lub wersji obiektu BLOB w określonym interwale. | Usuwanie nietrwałe obiektów BLOB<br />[Dowiedz się więcej...](soft-delete-blob-overview.md) | Włącz usuwanie nietrwałe obiektów BLOB dla wszystkich kont magazynu z minimalnym interwałem przechowywania wynoszącym 7 dni.<br /><br />Włącz obsługę wersji obiektów blob i Usuń nietrwałe kontenery wraz z usuwaniem nietrwałego obiektu BLOB w celu zapewnienia optymalnej ochrony danych obiektów BLOB.<br /><br />Przechowuj obiekty blob, które wymagają różnych okresów przechowywania w oddzielnym koncie magazynu. | Usunięty obiekt BLOB lub wersja obiektu BLOB mogą zostać przywrócone w ramach okresu przechowywania. | Nie |
| Przywróć zestaw blokowych obiektów BLOB do wcześniejszego punktu w czasie. | Przywracanie do określonego momentu<br />[Dowiedz się więcej...](point-in-time-restore-overview.md) | Aby użyć przywracania do określonego momentu w celu przywrócenia wcześniejszego stanu, Zaprojektuj aplikację w celu usunięcia pojedynczych blokowych obiektów BLOB zamiast usuwania kontenerów. | Zestaw blokowych obiektów BLOB może zostać przywrócony do ich stanu w określonym punkcie w przeszłości.<br /><br />Przywracane są tylko operacje wykonywane na blokowych obiektach Blob. Wszelkie operacje wykonywane na kontenerach, stronicowych obiektach Blob lub dołączanych obiektach Blob nie są przywracane. | Nie |
| Ręcznie Zapisz stan obiektu BLOB w danym momencie. | Migawka obiektu BLOB<br />[Dowiedz się więcej...](snapshots-overview.md) | Zalecane jako alternatywa dla wersji obiektów blob, gdy przechowywanie wersji nie jest odpowiednie dla danego scenariusza, ze względu na koszty lub inne zagadnienia, lub jeśli konto magazynu ma włączoną hierarchiczną przestrzeń nazw. | Obiekt BLOB może zostać przywrócony z migawki, jeśli obiekt BLOB zostanie zastąpiony. Jeśli obiekt BLOB zostanie usunięty, migawki również zostaną usunięte. | Tak, w wersji zapoznawczej |
| Obiekt BLOB może zostać usunięty lub nadpisany, ale dane są regularnie kopiowane na drugie konto magazynu. | Rozwiązanie do kopiowania danych na drugie konto przy użyciu funkcji replikacji obiektów usługi Azure Storage lub narzędzia, takiego jak AzCopy lub Azure Data Factory. | Zalecane dla ochrony pokoju przed nieoczekiwanymi zamierzonymi akcjami lub nieprzewidywalnymi scenariuszami.<br /><br />Utwórz drugie konto magazynu w tym samym regionie co konto podstawowe, aby uniknąć naliczania opłat za ruch wychodzący. | Dane można przywrócić z drugiego konta magazynu, jeśli konto podstawowe zostanie naruszone w dowolny sposób. | AzCopy i Azure Data Factory są obsługiwane.<br /><br />Replikacja obiektów nie jest obsługiwana. |

## <a name="data-protection-by-resource-type"></a>Ochrona danych według typu zasobu

Poniższa tabela zawiera podsumowanie opcji ochrony danych usługi Azure Storage w zależności od chronionych zasobów.

| Opcja ochrony danych | Chroni konto przed usunięciem | Chroni kontener przed usunięciem | Chroni obiekt BLOB przed usunięciem | Chroni obiekt BLOB przed zastępowaniem |
|--|--|--|--|--|
| Blokada Azure Resource Manager | Tak | Nie<sup>1</sup> | Nie | Nie |
| Zasady niezmienności w kontenerze | Tak<sup>2</sup> | Tak | Tak | Tak |
| Usuwanie nietrwałe kontenera | Nie | Tak | Nie | Nie |
| Wersja<sup>3</sup> obiektów BLOB | Nie | Nie | Tak | Tak |
| Obiekt BLOB unsoft Delete<sup>3</sup> | Nie | Nie | Tak | Tak |
| Przywracanie do punktu w czasie<sup>3</sup> | Nie | Nie | Tak | Tak |
| Migawka obiektu BLOB | Nie | Nie | Nie | Tak |
| Pakiet zbiorczy służący do kopiowania danych na drugie konto<sup>4</sup> | Nie | Tak | Tak | Tak |

<sup>1</sup> blokada Azure Resource Manager nie chroni kontenera przed usunięciem.<br />
<sup>2</sup> gdy dla kontenera obowiązuje Blokada prawna lub zablokowane zasady przechowywania oparte na czasie, konto magazynu również jest chronione przed usunięciem.<br />
<sup>3</sup> nie jest obecnie obsługiwane dla obciążeń Data Lake Storage.<br />
<sup>4</sup> AzCopy i Azure Data Factory są opcjami, które są obsługiwane dla obciążeń Blob Storage i Data Lake Storage. Replikacja obiektów jest obsługiwana tylko dla obciążeń Blob Storage.<br />

## <a name="recover-deleted-or-overwritten-data"></a>Odzyskiwanie usuniętych lub nadpisanych danych

Jeśli konieczne jest odzyskanie danych, które zostały nadpisywane lub usunięte, sposób działania zależy od tego, które opcje ochrony danych zostały włączone i jakie zasoby miały wpływ. W poniższej tabeli opisano akcje, które można wykonać w celu odzyskania danych.

| Zasób usunięty lub nadpisany | Możliwe akcje odzyskiwania | Wymagania dotyczące odzyskiwania |
|--|--|--|
| Konto magazynu | Próba odzyskania usuniętego konta magazynu<br />[Dowiedz się więcej...](../common/storage-account-recover.md) | Konto magazynu zostało pierwotnie utworzone przy użyciu modelu wdrażania Azure Resource Manager i zostało usunięte w ciągu ostatnich 14 dni. Nowe konto magazynu o tej samej nazwie nie zostało utworzone, ponieważ oryginalne konto zostało usunięte. |
| Kontener | Odzyskaj usunięty trwale kontener i jego zawartość<br />[Dowiedz się więcej...](soft-delete-container-enable.md) | Usuwanie nietrwałe kontenera jest włączone i okres przechowywania nietrwałego usuwania kontenera jeszcze nie wygasł. |
| Kontenery i obiekty blob | Przywracanie danych z drugiego konta magazynu | Wszystkie operacje kontenera i obiektów BLOB zostały efektywnie zreplikowane na drugie konto magazynu. |
| Obiekt BLOB (dowolny typ) | Przywracanie obiektu BLOB z poprzedniej wersji<sup>1</sup><br />[Dowiedz się więcej...](versioning-enable.md) | Obsługa wersji obiektów BLOB jest włączona, a obiekt BLOB ma co najmniej jedną poprzednią wersję. |
| Obiekt BLOB (dowolny typ) | Odzyskiwanie nietrwałego, usuniętego obiektu BLOB<sup>1</sup><br />[Dowiedz się więcej...](soft-delete-blob-enable.md) | Usuwanie nietrwałe obiektów BLOB jest włączone, a interwał przechowywania nietrwałego usuwania nie wygasł. |
| Obiekt BLOB (dowolny typ) | Przywracanie obiektu BLOB z migawki<br />[Dowiedz się więcej...](snapshots-manage-dotnet.md) | Obiekt BLOB zawiera co najmniej jedną migawkę. |
| Zestaw blokowych obiektów BLOB | Odzyskaj zestaw blokowych obiektów BLOB do ich stanu w wcześniejszym punkcie w czasie<sup>1</sup><br />[Dowiedz się więcej...](point-in-time-restore-manage.md) | Przywracanie do punktu w czasie jest włączone, a punkt przywracania jest w okresie przechowywania. Konto magazynu nie zostało naruszone lub zostało uszkodzone. |
| Wersja obiektu BLOB | Odzyskaj nietrwałe wersje<sup>1</sup><br />[Dowiedz się więcej...](soft-delete-blob-enable.md) | Usuwanie nietrwałe obiektów BLOB jest włączone, a interwał przechowywania nietrwałego usuwania nie wygasł. |

<sup>1</sup> nie jest obecnie obsługiwane dla obciążeń Data Lake Storage.

## <a name="summary-of-cost-considerations"></a>Podsumowanie zagadnień dotyczących kosztów

Poniższa tabela zawiera podsumowanie zagadnień dotyczących kosztów związanych z różnymi opcjami ochrony danych opisanymi w tym przewodniku.

| Opcja ochrony danych | Kwestie związane z kosztami |
|-|-|
| Azure Resource Manager blokadę dla konta magazynu | Brak opłat za skonfigurowanie blokady na koncie magazynu. |
| Zasady niezmienności w kontenerze | Brak opłat za skonfigurowanie zasad niezmienności w kontenerze. |
| Usuwanie nietrwałe kontenera | Brak opłat za włączenie usuwania nietrwałego kontenera dla konta magazynu. Dane w kontenerze z usuniętym usuwaniem są rozliczane z taką samą szybkością jak dane aktywne do momentu trwałego usunięcia usuniętego kontenera. |
| Przechowywanie wersji obiektów BLOB | Brak opłat za włączenie obsługi wersji obiektów BLOB dla konta magazynu. Po włączeniu obsługi wersji obiektów BLOB każda operacja zapisu lub usuwania na obiekcie BLOB w ramach konta tworzy nową wersję, co może prowadzić do zwiększonego kosztu zdolności produkcyjnych.<br /><br />Wersja obiektu BLOB jest rozliczana na podstawie unikatowych bloków lub stron. W związku z tym koszty są zwiększane, ponieważ podstawowy obiekt BLOB jest niezależny od określonej wersji. Zmiana warstwy obiektu BLOB lub obiektu BLOB może mieć wpływ na rozliczenia. Aby uzyskać więcej informacji, zobacz [Cennik i rozliczenia](versioning-overview.md#pricing-and-billing).<br /><br />Użyj zarządzania cyklem życia, aby usunąć starsze wersje w miarę potrzeby w celu kontrolowania kosztów. Aby uzyskać więcej informacji, zobacz [Optymalizowanie kosztów dzięki automatyzowaniu warstw dostępu BLOB Storage platformy Azure](storage-lifecycle-management-concepts.md). |
| Usuwanie nietrwałe obiektów BLOB | Brak opłat za włączenie usuwania nietrwałego obiektu BLOB dla konta magazynu. Dane w obiekcie blob, które zostały usunięte, są rozliczane o tej samej stawce jako aktywne dane do momentu usunięcia trwałego usuniętego obiektu BLOB. |
| Przywracanie do określonego momentu | Bez opłat, aby włączyć przywracanie do punktu w czasie dla konta magazynu; Jednak włączenie przywracania do punktu w czasie umożliwia również przechowywanie wersji obiektów blob, usuwania nietrwałego i źródła zmian, z których każdy może spowodować naliczenie dodatkowych opłat.<br /><br />Opłaty są naliczane w przypadku przywracania do punktu w czasie podczas wykonywania operacji przywracania. Koszt operacji przywracania zależy od ilości przywracanych danych. Aby uzyskać więcej informacji, zobacz [Cennik i rozliczenia](point-in-time-restore-overview.md#pricing-and-billing). |
| Migawki obiektów BLOB | Dane w migawce są rozliczane na podstawie unikatowych bloków lub stron. W związku z tym koszty są zwiększane, ponieważ podstawowy obiekt BLOB jest niezależny od migawki. Zmiana warstwy obiektu BLOB lub migawki może mieć wpływ na rozliczenia. Aby uzyskać więcej informacji, zobacz [Cennik i rozliczenia](snapshots-overview.md#pricing-and-billing).<br /><br />Użyj zarządzania cyklem życia, aby usunąć starsze migawki zgodnie z wymaganiami w celu kontrolowania kosztów. Aby uzyskać więcej informacji, zobacz [Optymalizowanie kosztów dzięki automatyzowaniu warstw dostępu BLOB Storage platformy Azure](storage-lifecycle-management-concepts.md). |
| Kopiowanie danych na drugie konto magazynu | Utrzymywanie danych w drugim koncie magazynu spowoduje policzenie wydajności i kosztów transakcji. Jeśli drugie konto magazynu znajduje się w innym regionie niż konto źródłowe, a następnie skopiowanie danych na to drugie konto spowoduje również naliczanie opłat za ruch wychodzący. |

## <a name="disaster-recovery"></a>Odzyskiwanie po awarii

Usługa Azure Storage zawsze utrzymuje wiele kopii danych, dzięki czemu jest chroniona przed planowanymi i nieplanowanymi zdarzeniami, w tym przejściowymi awariami sprzętowymi, siecią lub przestojem, a także ogromnymi klęskami żywiołowymi. Nadmiarowość gwarantuje, że Twoje konto magazynu spełnia jego dostępność i cele trwałości nawet w przypadku awarii. Aby uzyskać więcej informacji na temat sposobu konfigurowania konta magazynu pod kątem wysokiej dostępności, zobacz [nadmiarowość usługi Azure Storage](../common/storage-redundancy.md).

W przypadku wystąpienia awarii w centrum danych, jeśli konto magazynu jest nadmiarowe w dwóch regionach geograficznych (Geograficznie nadmiarowy), można wybrać opcję przełączania konta z regionu podstawowego do regionu pomocniczego. Aby uzyskać więcej informacji, zobacz [odzyskiwanie po awarii i konto magazynu w trybie failover](../common/storage-disaster-recovery-guidance.md).

Tryb failover zarządzany przez klienta nie jest obecnie obsługiwany dla kont magazynu z włączoną hierarchiczną przestrzenią nazw. Aby uzyskać więcej informacji, zobacz [funkcje magazynu obiektów BLOB dostępne w Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="next-steps"></a>Następne kroki

- [Nadmiarowość usługi Azure Storage](../common/storage-redundancy.md)
- [Odzyskiwanie po awarii i tryb failover konta magazynu](../common/storage-disaster-recovery-guidance.md)
