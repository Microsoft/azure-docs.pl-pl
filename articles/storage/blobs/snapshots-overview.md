---
title: Migawki obiektów BLOB
titleSuffix: Azure Storage
description: Dowiedz się, jak utworzyć migawkę obiektu BLOB w trybie tylko do odczytu, aby utworzyć kopię zapasową danych obiektów BLOB w danym momencie.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/02/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: cd8771afdc9c4462faaff758ffdd073382f69f53
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85550979"
---
# <a name="blob-snapshots"></a>Migawki obiektów BLOB

Migawka to wersja obiektu BLOB tylko do odczytu, która jest wykonywana w danym momencie.

> [!NOTE]
> Przechowywanie wersji obiektów BLOB (wersja zapoznawcza) oferuje alternatywny sposób obsługi historycznych kopii obiektu BLOB. Aby uzyskać więcej informacji, zobacz temat [przechowywanie wersji obiektów BLOB (wersja zapoznawcza)](versioning-overview.md).

## <a name="about-blob-snapshots"></a>Informacje o migawek obiektów BLOB

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

Migawka obiektu BLOB jest taka sama jak jego podstawowy obiekt BLOB, z tą różnicą, że identyfikator URI obiektu BLOB ma dołączoną wartość **DateTime** do identyfikatora URI obiektu BLOB, aby wskazać godzinę utworzenia migawki. Na przykład jeśli identyfikator URI strony ma wartość `http://storagesample.core.blob.windows.net/mydrives/myvhd` , identyfikator URI migawki jest podobny do `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z` .

> [!NOTE]
> Wszystkie migawki korzystają z identyfikatora URI podstawowego obiektu BLOB. Jedyną różnicą między bazowym obiektem blob a migawką jest dołączona wartość **DateTime** .
>

Obiekt BLOB może zawierać dowolną liczbę migawek. Migawki są utrwalane, dopóki nie zostaną jawnie usunięte, co oznacza, że migawka nie może na bieżąco odistnieć podstawowego obiektu BLOB. Można wyliczyć migawki skojarzone z podstawowym obiektem BLOB w celu śledzenia bieżących migawek.

Podczas tworzenia migawki obiektu BLOB właściwości systemu obiektu BLOB są kopiowane do migawki z tymi samymi wartościami. Metadane podstawowego obiektu BLOB są również kopiowane do migawki, o ile nie zostanie określone oddzielne metadane dla migawki podczas jej tworzenia. Po utworzeniu migawki można ją odczytywać, kopiować lub usuwać, ale nie można jej modyfikować.

Wszystkie dzierżawy skojarzone z podstawowym obiektem BLOB nie mają wpływu na migawkę. Nie można uzyskać dzierżawy na migawce.

Plik VHD jest używany do przechowywania bieżących informacji i stanu dla dysku maszyny wirtualnej. Możesz odłączyć dysk od maszyny wirtualnej lub zamknąć maszynę wirtualną, a następnie wykonać migawkę jego pliku VHD. Możesz użyć tego pliku migawek później, aby pobrać plik VHD w tym momencie i ponownie utworzyć maszynę wirtualną.

## <a name="understand-how-snapshots-accrue-charges"></a>Zrozumienie, jak naliczane są opłaty za migawki

Utworzenie migawki, która jest kopią tylko do odczytu obiektu BLOB, może spowodować naliczenie dodatkowych opłat za magazyn danych dla Twojego konta. Podczas projektowania aplikacji ważne jest, aby mieć świadomość, jak te opłaty mogą zostać naliczone, aby można było zminimalizować koszty.

### <a name="important-billing-considerations"></a>Ważne zagadnienia dotyczące rozliczeń

Poniższa lista zawiera kluczowe kwestie, które należy wziąć pod uwagę podczas tworzenia migawki.

- Konto magazynu wiąże się z opłatami za unikatowe bloki lub strony, niezależnie od tego, czy znajdują się w obiekcie blob, czy w migawce. Twoje konto nie wiąże się z dodatkowymi opłatami za migawki skojarzone z obiektem BLOB do momentu zaktualizowania obiektu BLOB, na którym się znajdują. Po zaktualizowaniu podstawowego obiektu BLOB jest on rozbieżny od jego migawek. W takim przypadku opłata jest naliczana za unikatowe bloki lub strony w każdym obiekcie blob lub w migawce.
- Gdy zastąpisz blok w blokowym obiekcie blob, ten blok jest następnie naliczany jako unikatowy blok. Jest to prawdziwe, nawet jeśli blok ma ten sam identyfikator bloku i te same dane, które znajdują się w migawce. Po ponownym zatwierdzeniu bloku jest on rozbieżny od jego odpowiednika w dowolnej migawce i zostanie naliczona opłata za dane. Te same wartości mają wartość true dla strony w obiekcie blob stronicowania, która jest aktualizowana o identyczne dane.
- Zastępowanie blokowego obiektu BLOB przez wywołanie metody [UploadFromFile] [dotnet_UploadFromFile], [UploadText] [dotnet_UploadText], [UploadFromStream] [dotnet_UploadFromStream] lub [UploadFromByteArray] [dotnet_UploadFromByteArray] zastępuje wszystkie bloki w obiekcie blob. Jeśli posiadasz migawkę skojarzoną z tym obiektem BLOB, wszystkie bloki w podstawowym obiekcie blob i migawek są teraz rozbieżne i zostanie naliczona opłata za wszystkie bloki w obu obiektach Blob. Jest to prawdziwe, nawet jeśli dane w podstawowym obiekcie blob i migawka pozostają identyczne.
- Blob service platformy Azure nie ma metody, aby określić, czy dwa bloki zawierają identyczne dane. Każdy przekazany i zatwierdzony blok jest traktowany jako unikatowy, nawet jeśli ma takie same dane i ten sam identyfikator bloku. Ze względu na to, że opłaty są naliczane dla unikatowych bloków, należy wziąć pod uwagę, że aktualizacja obiektu BLOB z migawką skutkuje dodatkowymi unikatowymi blokami i dodatkowymi opłatami.

### <a name="minimize-cost-with-snapshot-management"></a>Minimalizacja kosztów za pomocą zarządzania migawkami

Zalecamy staranne zarządzanie migawkami, aby uniknąć dodatkowych opłat. Poniższe najlepsze rozwiązania mogą pomóc zminimalizować koszty związane z magazynowaniem migawek:

- Usuń i ponownie utwórz migawki skojarzone z obiektem BLOB za każdym razem, gdy aktualizujesz obiekt BLOB, nawet jeśli aktualizujesz te same dane, chyba że projekt aplikacji wymaga utrzymania migawek. Przez usunięcie i ponowne utworzenie migawek obiektu BLOB można upewnić się, że obiekt BLOB i migawki nie są rozbieżne.
- Jeśli są zachowywane migawki obiektu BLOB, należy unikać wywoływania [UploadFromFile] [dotnet_UploadFromFile], [UploadText] [dotnet_UploadText], [UploadFromStream] [dotnet_UploadFromStream] lub [UploadFromByteArray] [dotnet_UploadFromByteArray] w celu zaktualizowania obiektu BLOB. Te metody zastępują wszystkie bloki w obiekcie blob, co powoduje znaczne rozbieżność podstawowego obiektu BLOB i jego migawek. Zamiast tego należy zaktualizować możliwie najmniejszą liczbę bloków przy użyciu metod [PutBlock] [dotnet_PutBlock] i [PutBlockList] [dotnet_PutBlockList].

### <a name="snapshot-billing-scenarios"></a>Scenariusze rozliczania migawek

W poniższych scenariuszach pokazano, jak naliczane są opłaty za blokowy obiekt BLOB i jego migawki.

#### <a name="scenario-1"></a>Scenariusz 1

W scenariuszu 1 podstawowy obiekt BLOB nie został zaktualizowany po wykonaniu migawki, więc opłaty są naliczane tylko dla unikatowych bloków 1, 2 i 3.

![Zasoby usługi Azure Storage](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>Scenariusz 2

W scenariuszu 2 podstawowy obiekt BLOB został zaktualizowany, ale migawka nie jest. Blok 3 został zaktualizowany, a mimo to zawiera te same dane i ten sam identyfikator, ale nie jest taki sam jak blok 3 w migawce. W związku z tym konto jest obciążane czterema blokami.

![Zasoby usługi Azure Storage](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>Scenariusz 3

W scenariuszu 3 podstawowy obiekt BLOB został zaktualizowany, ale migawka nie jest. Blok 3 został zastąpiony blokiem 4 w podstawowym obiekcie blob, ale migawka nadal odzwierciedla blok 3. W związku z tym konto jest obciążane czterema blokami.

![Zasoby usługi Azure Storage](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>Scenariusz 4

W scenariuszu 4 podstawowy obiekt BLOB został całkowicie zaktualizowany i nie zawiera żadnego z jego oryginalnych bloków. W związku z tym konto jest obciążane za wszystkie osiem unikatowych bloków. Ten scenariusz może wystąpić, jeśli używasz metody Update, takiej jak [UploadFromFile] [dotnet_UploadFromFile], [UploadText] [dotnet_UploadText], [UploadFromStream] [dotnet_UploadFromStream] lub [UploadFromByteArray] [dotnet_UploadFromByteArray], ponieważ te metody zastępują całą zawartość obiektu BLOB.

![Zasoby usługi Azure Storage](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Następne kroki

- [Tworzenie migawki obiektu BLOB w programie .NET i zarządzanie nią](snapshots-manage-dotnet.md)
- [Tworzenie kopii zapasowych niezarządzanych dysków maszyny wirtualnej platformy Azure przy użyciu migawek przyrostowych](../../virtual-machines/windows/incremental-snapshots.md)
