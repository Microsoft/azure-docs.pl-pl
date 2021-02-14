---
title: Ciągła kopia zapasowa z funkcją przywracania do punktu w czasie w Azure Cosmos DB
description: Funkcja przywracania do punktu w czasie Azure Cosmos DB ułatwia odzyskanie danych z przypadkowego zapisu, operacji usuwania lub przywracania danych w dowolnym regionie. Dowiedz się więcej o cenach i bieżących ograniczeniach.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.custom: references_regions
ms.openlocfilehash: d1dc108ecec93dddeb768eb61af425ba67f23002
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393143"
---
# <a name="continuous-backup-with-point-in-time-restore-preview-feature-in-azure-cosmos-db"></a>Ciągła kopia zapasowa z funkcją przywracania do punktu w czasie (wersja zapoznawcza) w Azure Cosmos DB
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Funkcja przywracania do punktu w czasie (tryb ciągłej kopii zapasowej) dla Azure Cosmos DB jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Funkcja przywracania do punktu w czasie Azure Cosmos DB (wersja zapoznawcza) pomaga w wielu scenariuszach, takich jak następujące:

* Odzyskiwanie po przypadkowym operacji zapisu lub usuwania w kontenerze.
* Przywrócenie usuniętego konta, bazy danych lub kontenera.
* Aby przywrócić do dowolnego regionu (w którym istnieją kopie zapasowe) w punkcie przywracania w czasie.

Azure Cosmos DB wykonuje kopię zapasową danych w tle bez użycia dodatkowej, zainicjowanej przepływności (jednostek ru) ani wpływu na wydajność i dostępność bazy danych. Ciągłe kopie zapasowe są wykonywane w każdym regionie, w którym istnieje konto. Na poniższej ilustracji przedstawiono sposób tworzenia kopii zapasowej kontenera z regionem zapisu w regionie zachodnie stany USA, odczytywanie regionów w regionach wschodnie i Wschodnie stany USA 2 do zdalnego konta usługi Azure Blob Storage w odpowiednich regionach. Domyślnie każdy region przechowuje kopię zapasową w lokalnie nadmiarowych kontach magazynu. Jeśli w regionie są włączone [strefy dostępności](high-availability.md#availability-zone-support) , kopia zapasowa jest przechowywana na koncie magazynu Zone-Redundant.

:::image type="content" source="./media/continuous-backup-restore-introduction/continuous-backup-restore-blob-storage.png" alt-text="Azure Cosmos DB kopię zapasową danych na platformie Azure Blob Storage." lightbox="./media/continuous-backup-restore-introduction/continuous-backup-restore-blob-storage.png" border="false":::

Przedział czasu dostępnego dla przywracania (nazywany także okresem przechowywania) to niższa wartość z następujących dwóch: *30 dni wstecz od teraz* lub *do czasu utworzenia zasobu*. Punkt w czasie dla przywracania może być dowolną sygnaturą czasową w okresie przechowywania.

W publicznej wersji zapoznawczej można przywrócić konto Azure Cosmos DB dla interfejsu API SQL lub punktu zawartości MongoDB w czasie do innego konta przy użyciu [Azure Portal](continuous-backup-restore-portal.md), [interfejsu wiersza polecenia platformy Azure](continuous-backup-restore-command-line.md) (az CLI), [Azure PowerShell](continuous-backup-restore-powershell.md)lub [Azure Resource Manager](continuous-backup-restore-template.md).

## <a name="what-is-restored"></a>Co jest przywracane?

W stanie stałym kopia zapasowa wszystkich mutacji wykonanych na koncie źródłowym (w tym bazami danych, kontenerami i elementami) jest wykonywana asynchronicznie w ciągu 100 sekund. Jeśli nośnik kopii zapasowej (w usłudze Azure Storage) nie działa lub jest niedostępny, mutacje są utrwalane lokalnie do momentu, gdy nośnik będzie dostępny z powrotem, a następnie są przywracane, aby zapobiec utracie danych operacji, które mogą zostać przywrócone.

Można przywrócić dowolną kombinację kontenerów z aprowizowaną przepływnością, bazę danych z udostępnioną przepływnością lub całe konto. Akcja przywracania przywraca wszystkie dane i właściwości ich indeksu do nowego konta. Proces przywracania gwarantuje, że wszystkie dane przywrócone do konta, bazy danych lub kontenera są spójne do określonego czasu przywracania. Czas trwania przywracania będzie zależeć od ilości danych, które mają zostać przywrócone.

> [!NOTE]
> W trybie ciągłej kopii zapasowej kopie zapasowe są wykonywane w każdym regionie, w którym jest dostępne konto Azure Cosmos DB. Kopie zapasowe wykonane dla każdego konta regionu są lokalnie nadmiarowe i strefowo nadmiarowe, jeśli konto ma włączoną funkcję [strefy dostępności](high-availability.md#availability-zone-support) dla tego regionu. Akcja Przywróć zawsze przywraca dane do nowego konta.

## <a name="what-is-not-restored"></a>Co nie jest przywracane?

Następujące konfiguracje nie są przywracane po przeprowadzeniu odzyskiwania do punktu w czasie:

* Zapora, Sieć wirtualna, ustawienia prywatnych punktów końcowych.
* Ustawienia spójności Domyślnie konto jest przywracane ze spójnością sesji.  
* Regionach.
* Procedury składowane, wyzwalacze, UDF.

Po zakończeniu przywracania można dodać te konfiguracje do przywróconego konta.

## <a name="restore-scenarios"></a>Scenariusze przywracania

Poniżej przedstawiono niektóre z kluczowych scenariuszy, które są rozwiązywane przez funkcję Przywracanie do określonego momentu. Scenariusze [a] do [c] pokazują, jak wyzwolić przywracanie, jeśli sygnatura czasowa przywracania jest znana wcześniej.
Mogą jednak wystąpić sytuacje, w których nie wiadomo, czy nie jest dokładny czas przypadkowego usunięcia lub uszkodzenia. Scenariusze [d] i [e] przedstawiają sposób _odnajdywania_ sygnatury czasowej przywracania przy użyciu nowych interfejsów API strumieniowego źródła zdarzeń w bazie danych lub kontenerach dostępnych.

:::image type="content" source="./media/continuous-backup-restore-introduction/restorable-account-scenario.png" alt-text="Zdarzenia cyklu życia z sygnaturami czasowymi dla konta dostępnych." lightbox="./media/continuous-backup-restore-introduction/restorable-account-scenario.png" border="false":::

a. **Przywracanie usuniętego konta** — wszystkie usunięte konta, które można przywrócić, są widoczne w okienku **przywracanie** . Na przykład jeśli *konto A* zostanie usunięte z sygnatury czasowej T3. W takim przypadku sygnatura czasowa tuż przed łączem T3, lokalizacją, nazwą konta docelowego, grupą zasobów i nazwą konta docelowego jest wystarczająca do przywracania z [Azure Portal](continuous-backup-restore-portal.md#restore-deleted-account), [PowerShell](continuous-backup-restore-powershell.md#trigger-restore)lub [interfejsu wiersza polecenia](continuous-backup-restore-command-line.md#trigger-restore).  

:::image type="content" source="./media/continuous-backup-restore-introduction/restorable-container-database-scenario.png" alt-text="Zdarzenia cyklu życia z sygnaturami czasowymi dla bazy danych dostępnych i kontenera." lightbox="./media/continuous-backup-restore-introduction/restorable-container-database-scenario.png" border="false":::

b. **Przywracanie danych konta w określonym regionie** — na przykład jeśli *konto* istnieje w dwóch regionach *Wschodnie stany USA* i *zachodnie stany USA* pod sygnaturą czasową T3. Jeśli potrzebujesz kopii konta A w regionie *zachodnie stany USA*, możesz wykonać przywracanie do punktu w czasie z [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md#trigger-restore)lub [interfejsu wiersza polecenia](continuous-backup-restore-command-line.md#trigger-restore) w regionie zachodnie stany USA jako lokalizację docelową.

c. **Odzyskaj od przypadkowej operacji zapisu lub usuwania w obrębie kontenera ze znaną sygnaturą czasową przywracania** — na przykład jeśli **wiesz** , że zawartość *kontenera 1* w *bazie danych 1* została przypadkowo zmodyfikowana z sygnaturą czasową T3. Możesz wykonać przywracanie do punktu w czasie z [Azure Portal](continuous-backup-restore-portal.md#restore-live-account), [PowerShell](continuous-backup-restore-powershell.md#trigger-restore)lub [interfejsu wiersza polecenia](continuous-backup-restore-command-line.md#trigger-restore) do innego konta w sygnaturze czasowej T3, aby odzyskać żądany stan kontenera.

d. **Przywróć konto do wcześniejszego punktu w czasie przed przypadkowym usunięciem bazy danych** — w [Azure Portal](continuous-backup-restore-portal.md#restore-live-account)możesz użyć okienka źródło zdarzeń, aby określić, kiedy baza danych została usunięta, i Znajdź czas przywracania. Podobnie, przy użyciu [interfejsu wiersza polecenia platformy Azure](continuous-backup-restore-command-line.md#trigger-restore) i [programu PowerShell](continuous-backup-restore-powershell.md#trigger-restore), można wykryć zdarzenie usunięcia bazy danych przez Wyliczenie źródła zdarzeń bazy danych, a następnie Wyzwól polecenie Restore z wymaganymi parametrami.

e. **Przywróć konto do wcześniejszego punktu w czasie przed przypadkowym usunięciem lub modyfikacją właściwości kontenera.** -W [Azure Portal](continuous-backup-restore-portal.md#restore-live-account)można użyć okienka źródło zdarzeń, aby określić, kiedy kontener został utworzony, zmodyfikowany lub usunięty, aby znaleźć czas przywracania. Podobnie jak w przypadku [interfejsu wiersza polecenia platformy Azure](continuous-backup-restore-command-line.md#trigger-restore) i [programu PowerShell](continuous-backup-restore-powershell.md#trigger-restore), można odnaleźć wszystkie zdarzenia kontenera, wyliczając źródło zdarzeń kontenera, a następnie wyzwalając polecenie Restore z wymaganymi parametrami.

## <a name="permissions"></a>Uprawnienia

Azure Cosmos DB umożliwia izolowanie i ograniczanie uprawnień przywracania dla konta ciągłej kopii zapasowej do określonej roli lub podmiotu zabezpieczeń. Właściciel konta może wyzwolić przywracanie i przypisać rolę do innych podmiotów zabezpieczeń, aby wykonać operację przywracania. Aby dowiedzieć się więcej, zobacz artykuł dotyczący [uprawnień](continuous-backup-restore-permissions.md) .

## <a name="pricing"></a><a id="continuous-backup-pricing"></a>Cennik

Konta Azure Cosmos DB, dla których włączono ciągłą kopię zapasową, będą naliczane dodatkowe miesięczne opłaty za *przechowywanie kopii zapasowych* i *przywracanie danych*. Koszt przywrócenia jest dodawany przy każdym zainicjowaniu operacji przywracania. Jeśli skonfigurujesz konto z ciągłą kopią zapasową, ale nie przywracasz danych, na rachunku zostanie uwzględniony tylko koszt magazynu kopii zapasowej.

Poniższy przykład jest oparty na cenie konta usługi Azure Cosmos wdrożonego w regionie nierządowym w Stanach Zjednoczonych. Ceny i obliczenia mogą się różnić w zależności od regionu, z którego korzystasz, aby uzyskać najnowsze informacje o cenach, zobacz [stronę z cennikiem Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) .

* Wszystkie konta z włączonymi zasadami ciągłego tworzenia kopii zapasowych ponoszą dodatkową miesięczną opłatę za magazyn kopii zapasowych, która jest obliczana w następujący sposób:

  $0,20/GB * rozmiar danych w GB na koncie * liczba regionów

* Każde wywołanie interfejsu API przywracania wiąże się z jednorazowym obciążeniem. Opłata jest funkcją ilości przywracania danych i jest obliczana w następujący sposób:

  rozmiar danych $0.15/GB * w GB.

Na przykład jeśli masz 1 TB danych w dwóch regionach, wówczas:

* Koszt magazynu kopii zapasowych jest obliczany jako (1000 * 0,20 * 2) = $400 miesięcznie

* Koszt przywrócenia jest obliczany jako (1000 * 0,15) = $150 na przywracanie

## <a name="current-limitations-public-preview"></a>Bieżące ograniczenia (publiczna wersja zapoznawcza)

Obecnie funkcja przywracania do punktu w czasie jest dostępna w publicznej wersji zapoznawczej i ma następujące ograniczenia:

* W przypadku ciągłej kopii zapasowej są obsługiwane tylko interfejsy API usługi Azure Cosmos DB dla baz danych SQL i MongoDB. Interfejsy API oprogramowania Cassandra, tabel i języka Gremlin nie są jeszcze obsługiwane.

* Nie można przekonwertować istniejącego konta z domyślnymi okresowymi zasadami tworzenia kopii zapasowych na używanie trybu ciągłej kopii zapasowej.

* Regiony i Azure Government w chmurze na platformie Azure nie są jeszcze obsługiwane.

* Konta z kluczami zarządzanymi przez klienta nie są obsługiwane do korzystania z ciągłej kopii zapasowej.

* Wieloregionowe konta zapisu nie są obsługiwane.

* Konta z włączonym łączem Synapse nie są obsługiwane.

* Przywracane konto jest tworzone w tym samym regionie, w którym znajduje się konto źródłowe. Nie można przywrócić konta do regionu, w którym konto źródłowe nie istnieje.

* Okno przywracania ma tylko 30 dni i nie można go zmienić.

* Kopie zapasowe nie są automatycznie geograficznie odporne na awarie. Musisz jawnie dodać kolejny region w celu uzyskania odporności dla konta i kopii zapasowej.

* Gdy przywracanie jest w toku, nie Modyfikuj ani nie usuwaj zasad zarządzania tożsamościami i dostępem (IAM), które przyznają uprawnienia dla konta, lub Zmień konfigurację zapory sieci wirtualnej.

* Konta interfejsu API usługi Azure Cosmos DB dla bazy danych SQL lub MongoDB, które tworzą unikatowy indeks po utworzeniu kontenera, nie są obsługiwane przez funkcję ciągłej kopii zapasowej. Obsługiwane są tylko kontenery, które tworzą unikatowy indeks w ramach początkowego tworzenia kontenera. W przypadku kont MongoDB tworzysz unikatowy indeks przy użyciu [poleceń rozszerzenia](mongodb-custom-commands.md).

* Funkcja przywracania do punktu w czasie zawsze przywraca do nowego konta usługi Azure Cosmos. Przywracanie do istniejącego konta nie jest obecnie obsługiwane. Jeśli interesuje Cię przesyłanie opinii na temat przywracania w miejscu, skontaktuj się z zespołem Azure Cosmos DB za pośrednictwem przedstawiciela konta lub usługi [UserVoice](https://feedback.azure.com/forums/263030-azure-cosmos-db).

* Wszystkie nowe interfejsy API udostępniane do wyświetlania na liście,,,, `RestorableDatabaseAccount` `RestorableSqlDatabases` `RestorableSqlContainer` `RestorableMongodbDatabase` `RestorableMongodbCollection` podlegają zmianom, gdy funkcja jest w wersji zapoznawczej.

* Po przywróceniu jest możliwe, że dla niektórych kolekcji można ponownie skompilować spójny indeks. Stan operacji odbudowywania można sprawdzić za pomocą właściwości [IndexTransformationProgress](how-to-manage-indexing-policy.md) .

* Proces przywracania przywraca wszystkie właściwości kontenera, w tym jego konfigurację TTL. W związku z tym jest możliwe, że przywrócone dane zostaną natychmiast usunięte, jeśli tak określa konfiguracja. Aby zapobiec takiej sytuacji, znacznik czasu przywracania musi określać czas przed dodaniem właściwości TTL do kontenera.

## <a name="next-steps"></a>Następne kroki

* Skonfiguruj ciągłą kopię zapasową i zarządzaj nią przy użyciu [Azure Portal](continuous-backup-restore-portal.md), [programu PowerShell](continuous-backup-restore-powershell.md), [interfejsu wiersza polecenia](continuous-backup-restore-command-line.md)lub [Azure Resource Manager](continuous-backup-restore-template.md).
* [Zarządzanie uprawnieniami](continuous-backup-restore-permissions.md) wymaganymi do przywracania danych z trybem ciągłej kopii zapasowej.
* [Model zasobów trybu ciągłej kopii zapasowej](continuous-backup-restore-resource-model.md)
