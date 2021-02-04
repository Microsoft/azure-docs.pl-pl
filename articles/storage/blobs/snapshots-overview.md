---
title: Migawki obiektów BLOB
titleSuffix: Azure Storage
description: Informacje na temat działania migawek obiektów blob i sposobu ich rozliczania.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/02/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: debce0a1b4c09bb89cdceb1cd29e59e1976c939a
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539160"
---
# <a name="blob-snapshots"></a>Migawki obiektów BLOB

Migawka to wersja obiektu BLOB tylko do odczytu, która jest wykonywana w danym momencie.

> [!NOTE]
> Przechowywanie wersji obiektów BLOB oferuje znakomitą metodę obsługi poprzednich wersji obiektu BLOB. Aby uzyskać więcej informacji, zobacz [przechowywanie wersji obiektów BLOB](versioning-overview.md).

## <a name="about-blob-snapshots"></a>Informacje o migawek obiektów BLOB

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

Migawka obiektu BLOB jest taka sama jak jego podstawowy obiekt BLOB, z tą różnicą, że identyfikator URI obiektu BLOB ma dołączoną wartość **DateTime** do identyfikatora URI obiektu BLOB, aby wskazać godzinę utworzenia migawki. Na przykład jeśli identyfikator URI strony ma wartość `http://storagesample.core.blob.windows.net/mydrives/myvhd` , identyfikator URI migawki jest podobny do `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z` .

> [!NOTE]
> Wszystkie migawki korzystają z identyfikatora URI podstawowego obiektu BLOB. Jedyną różnicą między bazowym obiektem blob a migawką jest dołączona wartość **DateTime** .

Obiekt BLOB może zawierać dowolną liczbę migawek. Migawki zachowują się do momentu usunięcia ich jawnie, niezależnie lub jako część operacji [usuwania obiektu BLOB](/rest/api/storageservices/delete-blob) dla podstawowego obiektu BLOB. Można wyliczyć migawki skojarzone z podstawowym obiektem BLOB w celu śledzenia bieżących migawek.

Podczas tworzenia migawki obiektu BLOB właściwości systemu obiektu BLOB są kopiowane do migawki z tymi samymi wartościami. Metadane podstawowego obiektu BLOB są również kopiowane do migawki, o ile nie zostanie określone oddzielne metadane dla migawki podczas jej tworzenia. Po utworzeniu migawki można ją odczytywać, kopiować lub usuwać, ale nie można jej modyfikować.

Wszystkie dzierżawy skojarzone z podstawowym obiektem BLOB nie mają wpływu na migawkę. Nie można uzyskać dzierżawy na migawce.

Plik VHD jest używany do przechowywania bieżących informacji i stanu dla dysku maszyny wirtualnej. Możesz odłączyć dysk od maszyny wirtualnej lub zamknąć maszynę wirtualną, a następnie wykonać migawkę jego pliku VHD. Możesz użyć tego pliku migawek później, aby pobrać plik VHD w tym momencie i ponownie utworzyć maszynę wirtualną.

## <a name="understand-how-snapshots-accrue-charges"></a>Zrozumienie, jak naliczane są opłaty za migawki

### <a name="important-billing-considerations"></a>Ważne zagadnienia dotyczące rozliczeń

Poniższa lista zawiera kluczowe kwestie, które należy wziąć pod uwagę podczas tworzenia migawki.

- Konto magazynu wiąże się z opłatami za unikatowe bloki lub strony, niezależnie od tego, czy znajdują się w obiekcie blob, czy w migawce. Twoje konto nie wiąże się z dodatkowymi opłatami za migawki skojarzone z obiektem BLOB do momentu zaktualizowania obiektu BLOB, na którym się znajdują. Po zaktualizowaniu podstawowego obiektu BLOB jest on rozbieżny od jego migawek. W takim przypadku opłata jest naliczana za unikatowe bloki lub strony w każdym obiekcie blob lub w migawce.
- Gdy zastąpisz blok w blokowym obiekcie blob, ten blok jest następnie naliczany jako unikatowy blok. Jest to prawdziwe, nawet jeśli blok ma ten sam identyfikator bloku i te same dane, które znajdują się w migawce. Po ponownym zatwierdzeniu bloku jest on rozbieżny od jego odpowiednika w dowolnej migawce i zostanie naliczona opłata za dane. Te same wartości mają wartość true dla strony w obiekcie blob stronicowania, która jest aktualizowana o identyczne dane.
- Aktualizacja blokowego obiektu BLOB przez wywołanie metody, która zastępuje całą zawartość obiektu BLOB, spowoduje zastąpienie wszystkich bloków w obiekcie blob. Jeśli posiadasz migawkę skojarzoną z tym obiektem BLOB, wszystkie bloki w podstawowym obiekcie blob i migawek są teraz rozbieżne i zostanie naliczona opłata za wszystkie bloki w obu obiektach Blob. Jest to prawdziwe, nawet jeśli dane w podstawowym obiekcie blob i migawka pozostają identyczne.
- Blob service platformy Azure nie ma metody, aby określić, czy dwa bloki zawierają identyczne dane. Każdy przekazany i zatwierdzony blok jest traktowany jako unikatowy, nawet jeśli ma takie same dane i ten sam identyfikator bloku. Ze względu na to, że opłaty są naliczane dla unikatowych bloków, należy wziąć pod uwagę, że aktualizacja obiektu BLOB z migawką skutkuje dodatkowymi unikatowymi blokami i dodatkowymi opłatami.

### <a name="minimize-costs-with-snapshot-management"></a>Minimalizacja kosztów za pomocą zarządzania migawkami

Zalecamy staranne zarządzanie migawkami, aby uniknąć dodatkowych opłat. Poniższe najlepsze rozwiązania mogą pomóc zminimalizować koszty związane z magazynowaniem migawek:

- Usuń i ponownie utwórz migawki skojarzone z obiektem BLOB za każdym razem, gdy aktualizujesz obiekt BLOB, nawet jeśli aktualizujesz te same dane, chyba że projekt aplikacji wymaga utrzymania migawek. Przez usunięcie i ponowne utworzenie migawek obiektu BLOB można upewnić się, że obiekt BLOB i migawki nie są rozbieżne.
- Jeśli tworzysz migawki dla obiektu BLOB, unikaj wywoływania metod, które zastępują cały obiekt BLOB podczas aktualizowania obiektu BLOB. Zamiast tego należy zaktualizować możliwie najmniejszą liczbę bloków, aby zachować niskie koszty.

### <a name="snapshot-billing-scenarios"></a>Scenariusze rozliczania migawek

W poniższych scenariuszach pokazano, jak naliczane są opłaty za blokowy obiekt BLOB i jego migawki.

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

Utworzenie migawki, która jest kopią tylko do odczytu obiektu BLOB, może spowodować naliczenie dodatkowych opłat za magazyn danych dla Twojego konta. Podczas projektowania aplikacji ważne jest, aby mieć świadomość, jak te opłaty mogą zostać naliczone, aby można było zminimalizować koszty.

Migawki obiektów blob, takie jak wersje obiektów blob, są rozliczane według tej samej stawki co aktywne dane. Sposób rozliczania migawek zależy od tego, czy użytkownik jawnie ustawił warstwę dla podstawowego obiektu BLOB, czy dla którejkolwiek z jego migawek (lub wersji). Aby uzyskać więcej informacji na temat warstw obiektów blob, zobacz [Azure Blob Storage: warstwy dostępu gorąca, chłodna i archiwalna](storage-blob-storage-tiers.md).

Jeśli nie zmieniono warstwy obiektu BLOB lub migawki, opłaty są naliczane za unikatowe bloki danych w ramach tego obiektu BLOB, jego migawki i wszystkie wersje, które mogą mieć. Aby uzyskać więcej informacji, zobacz [rozliczenia, gdy warstwa obiektów BLOB nie została jawnie ustawiona](#billing-when-the-blob-tier-has-not-been-explicitly-set).

Jeśli zmieniono warstwę obiektu BLOB lub migawki, opłaty są naliczane za cały obiekt, niezależnie od tego, czy obiekt BLOB i migawka znajdują się ostatecznie w tej samej warstwie. Aby uzyskać więcej informacji, zobacz [rozliczenia, gdy warstwa obiektów BLOB została ustawiona jawnie](#billing-when-the-blob-tier-has-been-explicitly-set).

Aby uzyskać więcej informacji na temat szczegółów rozliczeń dla wersji obiektów blob, zobacz temat [przechowywanie wersji obiektów BLOB](versioning-overview.md).

### <a name="billing-when-the-blob-tier-has-not-been-explicitly-set"></a>Rozliczanie, gdy warstwa obiektów BLOB nie została jawnie ustawiona

Jeśli nie ustawisz jawnie warstwy obiektu BLOB dla podstawowego obiektu BLOB lub żadnej z jej migawek, naliczanie opłat za unikatowe bloki lub strony w obiekcie blob, jego migawki i wszystkie wersje, które mogą mieć. Dane, które są współużytkowane przez obiekt BLOB i jego migawki, są naliczone tylko raz. Po zaktualizowaniu obiektu BLOB dane w bazowym obiekcie BLOB są rozbieżne od danych przechowywanych w jego migawkach, a opłaty za unikatowe dane są naliczone na blok lub na stronie.

Gdy zastąpisz blok w blokowym obiekcie blob, ten blok jest następnie naliczany jako unikatowy blok. Jest to prawdziwe, nawet jeśli blok ma ten sam identyfikator bloku i te same dane, które znajdują się w migawce. Po ponownym zatwierdzeniu bloku jest on rozbieżny od jego odpowiednika w migawce i zostanie naliczona opłata za swoje dane. Te same wartości mają wartość true dla strony w obiekcie blob stronicowania, która jest aktualizowana o identyczne dane.

Usługa BLOB Storage nie ma metody, aby określić, czy dwa bloki zawierają identyczne dane. Każdy przekazany i zatwierdzony blok jest traktowany jako unikatowy, nawet jeśli ma takie same dane i ten sam identyfikator bloku. Ze względu na to, że opłaty są naliczane dla unikatowych bloków, należy pamiętać, że aktualizacja obiektu BLOB, gdy ten obiekt BLOB ma migawki lub wersje, spowoduje powstanie dodatkowych unikatowych bloków i dodatkowych opłat.

Gdy obiekt BLOB ma migawki, wywołaj operacje aktualizacji na blokowych obiektów blob, aby zaktualizować możliwie najmniejszą liczbę bloków. Operacje zapisu, które zezwalają na szczegółową kontrolę nad blokami, powodują [umieszczenie bloku](/rest/api/storageservices/put-block) i [umieszczenie listy zablokowanych](/rest/api/storageservices/put-block-list). Z drugiej strony operacja [Put obiektu BLOB](/rest/api/storageservices/put-blob) zastępuje całą zawartość obiektu BLOB, co może prowadzić do dodatkowych opłat.

W poniższych scenariuszach pokazano, jak naliczane są opłaty za blokowy obiekt BLOB i jego migawki, gdy warstwa obiektu BLOB nie została jawnie ustawiona.

#### <a name="scenario-1"></a>Scenariusz 1

W scenariuszu 1 podstawowy obiekt BLOB nie został zaktualizowany po wykonaniu migawki, więc opłaty są naliczane tylko dla unikatowych bloków 1, 2 i 3.

![Diagram 1 pokazujący rozliczenia dla unikatowych bloków w podstawowym obiekcie blob i w migawce.](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>Scenariusz 2

W scenariuszu 2 podstawowy obiekt BLOB został zaktualizowany, ale migawka nie jest. Blok 3 został zaktualizowany, a mimo to zawiera te same dane i ten sam identyfikator, ale nie jest taki sam jak blok 3 w migawce. W związku z tym konto jest obciążane czterema blokami.

![Diagram 2 przedstawiający rozliczenia dla unikatowych bloków w podstawowym obiekcie blob i w migawce.](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>Scenariusz 3

W scenariuszu 3 podstawowy obiekt BLOB został zaktualizowany, ale migawka nie jest. Blok 3 został zastąpiony blokiem 4 w podstawowym obiekcie blob, ale migawka nadal odzwierciedla blok 3. W związku z tym konto jest obciążane czterema blokami.

![Diagram 3 przedstawiający rozliczenia dla unikatowych bloków w podstawowym obiekcie blob i w migawce.](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>Scenariusz 4

W scenariuszu 4 podstawowy obiekt BLOB został całkowicie zaktualizowany i nie zawiera żadnego z jego oryginalnych bloków. W związku z tym konto jest obciążane za wszystkie osiem unikatowych bloków.

![Diagram 4 przedstawiający rozliczenia dla unikatowych bloków w podstawowym obiekcie blob i w migawce.](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-4.png)

> [!TIP]
> Należy unikać wywoływania metod, które zastępują cały obiekt BLOB, a zamiast tego aktualizować poszczególne bloki, aby zachować niskie koszty.

### <a name="billing-when-the-blob-tier-has-been-explicitly-set"></a>Rozliczanie, gdy warstwa obiektu BLOB została jawnie ustawiona

Jeśli jawnie ustawisz warstwę obiektów BLOB dla obiektu BLOB lub migawki (lub wersji), zostanie naliczona opłata za pełną długość zawartości obiektu w nowej warstwie, bez względu na to, czy ma on udział w blokach z obiektem w warstwie pierwotnej. Opłata jest naliczana również za pełną długość zawartości najstarszej wersji w oryginalnej warstwie. W przypadku wszystkich wersji lub migawek, które pozostaną w oryginalnej warstwie, są naliczane opłaty za unikatowe bloki, które mogą być udostępniane, zgodnie z opisem w temacie [rozliczenia, gdy warstwa obiektów BLOB nie została jawnie ustawiona](#billing-when-the-blob-tier-has-not-been-explicitly-set).

#### <a name="moving-a-blob-to-a-new-tier"></a>Przeniesienie obiektu BLOB do nowej warstwy

W poniższej tabeli opisano zachowanie dotyczące rozliczeń dla obiektu BLOB lub migawki po przeniesieniu ich do nowej warstwy.

| Gdy warstwa obiektów BLOB jest ustawiona jawnie w... | Następnie opłaty są naliczane za... |
|-|-|
| Podstawowy obiekt BLOB z migawką | Podstawowy obiekt BLOB w nowej warstwie i najstarszej migawce w oryginalnej warstwie oraz wszelkich unikatowych bloków w innych migawkach. <sup>1</sup> |
| Podstawowy obiekt BLOB z poprzednią wersją i migawką | Podstawowy obiekt BLOB w nowej warstwie, najstarsza wersja w oryginalnej warstwie oraz najstarsza migawka w oryginalnej warstwie oraz wszystkie unikatowe bloki w innych wersjach lub migawek<sup>1</sup>. |
| Migawka | Migawka w nowej warstwie i podstawowy obiekt BLOB w oryginalnej warstwie oraz wszystkie unikatowe bloki w innych migawkach. <sup>1</sup> |

<sup>1</sup> Jeśli istnieją inne poprzednie wersje lub migawki, które nie zostały przeniesione z oryginalnej warstwy, te wersje lub migawki są rozliczane na podstawie liczby unikatowych bloków, zgodnie z opisem w [rozliczeniach, gdy warstwa obiektu BLOB nie została jawnie ustawiona](#billing-when-the-blob-tier-has-not-been-explicitly-set).

Na poniższym diagramie pokazano, w jaki sposób obiekty są rozliczane, gdy obiekt BLOB z migawkami jest przenoszony do innej warstwy.

:::image type="content" source="media/snapshots-overview/snapshot-billing-tiers.png" alt-text="Diagram przedstawiający sposób, w jaki obiekty są rozliczane, gdy obiekt BLOB z migawkami jest jawnie warstwowy.":::

Jawnie Ustawianie warstwy dla obiektu BLOB, wersji lub migawki nie można cofnąć. Jeśli przeniesiesz obiekt BLOB do nowej warstwy, a następnie przeniesiesz go z powrotem do jego oryginalnej warstwy, naliczona zostanie opłata za pełną długość zawartości obiektu nawet wtedy, gdy współużytkuje bloki z innymi obiektami w pierwotnej warstwie.

Operacje, które jawnie ustawiają warstwę obiektu BLOB, wersji lub migawki, obejmują:

- [Ustawianie warstwy obiektu blob](/rest/api/storageservices/set-blob-tier)
- [Umieść obiekt BLOB](/rest/api/storageservices/put-blob) z określoną warstwą
- [Umieść listę zablokowanych](/rest/api/storageservices/put-block-list) z określoną warstwą
- [Kopiuj obiekt BLOB](/rest/api/storageservices/copy-blob) z określoną warstwą

#### <a name="deleting-a-blob-when-soft-delete-is-enabled"></a>Usuwanie obiektu BLOB po włączeniu usuwania nietrwałego

Po włączeniu usuwania nietrwałego obiektu BLOB, jeśli usuniesz lub zastąpisz podstawowy obiekt BLOB, dla którego ustawiono warstwę jawnie, wówczas wszystkie poprzednie wersje lub migawki obiektu BLOB usuniętego z nietrwałego są rozliczane z pełną długością zawartości. Aby uzyskać więcej informacji na temat współdziałania wersji obiektów blob i usuwania nietrwałego, zobacz [przechowywanie wersji obiektów blob i usuwanie nietrwałe](versioning-overview.md#blob-versioning-and-soft-delete).

W poniższej tabeli opisano zachowanie dotyczące rozliczeń dla obiektu BLOB, który jest usuwany nietrwale, w zależności od tego, czy włączono lub wyłączono obsługę wersji. Po włączeniu obsługi wersji jest tworzona nowa wersja, gdy obiekt BLOB jest usuwany w sposób nietrwały. Gdy obsługa wersji jest wyłączona, usuwanie obiektów BLOB powoduje utworzenie nietrwałej migawki.

| W przypadku zastąpienia bazowego obiektu BLOB z ustawioną jawnie jego warstwą... | Następnie opłaty są naliczane za... |
|-|-|
| Jeśli są włączone trwałe usuwanie i przechowywanie wersji obiektów BLOB | Wszystkie istniejące wersje o pełnej długości zawartości niezależnie od warstwy. |
| Jeśli usuwanie nietrwałe obiektów BLOB jest włączone, ale przechowywanie wersji jest wyłączone | Wszystkie istniejące nietrwałe migawki usuwania w pełnej długości zawartości niezależnie od warstwy. |

## <a name="next-steps"></a>Następne kroki

- [Przechowywanie wersji obiektów BLOB](versioning-overview.md)
- [Tworzenie migawki obiektu BLOB w programie .NET i zarządzanie nią](snapshots-manage-dotnet.md)
- [Tworzenie kopii zapasowych niezarządzanych dysków maszyny wirtualnej platformy Azure przy użyciu migawek przyrostowych](../../virtual-machines/windows/incremental-snapshots.md)
