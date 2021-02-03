---
title: Skonfiguruj konto Azure Cosmos DB przy użyciu okresowej kopii zapasowej
description: W tym artykule opisano sposób konfigurowania kont Azure Cosmos DB przy użyciu okresowych kopii zapasowych z interwałem tworzenia kopii zapasowych. i przechowywanie. Należy również skontaktować się z pomocą techniczną, aby przywrócić dane.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 185320868c491d98df5fb6e31d9a627157431944
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527530"
---
# <a name="configure-azure-cosmos-db-account-with-periodic-backup"></a>Skonfiguruj konto Azure Cosmos DB przy użyciu okresowej kopii zapasowej
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB automatycznie pobiera kopie zapasowe danych w regularnych odstępach czasu. Automatyczne kopie zapasowe są wykonywane bez wpływu na wydajność ani dostępność operacji bazy danych. Wszystkie kopie zapasowe są przechowywane oddzielnie w usłudze magazynu, a te kopie zapasowe są replikowane globalnie w celu odporności na awarie regionalne. W przypadku Azure Cosmos DB nie tylko Twoich danych, ale również kopie zapasowe danych są wysoce nadmiarowe i odporne na awarie regionalne. Poniższe kroki pokazują, jak Azure Cosmos DB wykonuje kopię zapasową danych:

* Azure Cosmos DB automatycznie wykonuje pełną kopię zapasową bazy danych co 4 godziny i w dowolnym momencie, domyślnie są przechowywane tylko najnowsze kopie zapasowe. Jeśli domyślne interwały nie są wystarczające dla obciążeń, można zmienić interwał tworzenia kopii zapasowych i okres przechowywania z Azure Portal. Konfigurację kopii zapasowej można zmienić podczas tworzenia konta usługi Azure Cosmos lub po nim. W przypadku usunięcia kontenera lub bazy danych Azure Cosmos DB zachowywania istniejących migawek danego kontenera lub bazy danych przez 30 dni.

* Azure Cosmos DB przechowuje te kopie zapasowe w usłudze Azure Blob Storage, a rzeczywiste dane znajdują się lokalnie w Azure Cosmos DB.

* W celu zagwarantowania małych opóźnień migawka kopii zapasowej jest przechowywana w usłudze Azure Blob Storage w tym samym regionie co bieżący region zapisu (lub **jeden** z regionów zapisu w przypadku, gdy istnieje wieloregionowa Konfiguracja zapisu). Aby zapewnić odporność na awarie regionalne, każda migawka danych kopii zapasowej w usłudze Azure Blob Storage jest ponownie replikowana do innego regionu przez magazyn geograficznie nadmiarowy. Region, do którego jest replikowana kopia zapasowa, jest oparty na regionie źródłowym i parze regionalnej powiązanej z regionem źródłowym. Aby dowiedzieć się więcej, zobacz [listę par geograficznie nadmiarowych w regionach platformy Azure](../best-practices-availability-paired-regions.md) . Nie można uzyskać bezpośredniego dostępu do tej kopii zapasowej. Zespół usługi Azure Cosmos DB przywróci kopię zapasową na żądanie wysłane we wniosku o pomoc techniczną.

  Na poniższej ilustracji przedstawiono sposób tworzenia kopii Blob Storage zapasowej kontenera usługi Azure Cosmos ze wszystkimi trzema podstawowymi partycjami fizycznymi w regionie zachodnie stany USA.

  :::image type="content" source="./media/configure-periodic-backup-restore/automatic-backup.png" alt-text="Okresowe pełne kopie zapasowe wszystkich jednostek Cosmos DB w usłudze GRS Azure Storage." lightbox="./media/configure-periodic-backup-restore/automatic-backup.png" border="false":::

* Kopie zapasowe są wykonywane bez wpływu na wydajność i dostępność aplikacji. Azure Cosmos DB wykonuje kopię zapasową danych w tle bez użycia dodatkowej, zainicjowanej przepływności (jednostek ru) ani wpływu na wydajność i dostępność bazy danych.

## <a name="modify-the-backup-interval-and-retention-period"></a><a id="configure-backup-interval-retention"></a>Modyfikowanie interwału i okresu przechowywania kopii zapasowych

Azure Cosmos DB automatycznie wykonuje pełną kopię zapasową danych przez co 4 godziny i w dowolnym momencie są przechowywane najnowsze kopie zapasowe. Ta konfiguracja jest opcją domyślną i jest oferowana bez dodatkowych kosztów. Można zmienić domyślny interwał tworzenia kopii zapasowych i okres przechowywania podczas tworzenia konta usługi Azure Cosmos lub po utworzeniu konta. Konfiguracja kopii zapasowej jest ustawiana z poziomu konta usługi Azure Cosmos i należy ją skonfigurować na każdym koncie. Po skonfigurowaniu opcji tworzenia kopii zapasowej dla konta są one stosowane do wszystkich kontenerów w ramach tego konta. Obecnie opcje tworzenia kopii zapasowej można zmienić tylko w witrynie Azure Portal.

Jeśli dane zostały przypadkowo usunięte lub uszkodzone, **przed utworzeniem żądania obsługi w celu przywrócenia danych należy zwiększyć czas przechowywania kopii zapasowej dla konta na co najmniej siedem dni. Najlepszym rozwiązaniem jest zwiększenie okresu przechowywania w ciągu 8 godzin od tego zdarzenia.** Dzięki temu zespół Azure Cosmos DB ma wystarczająco dużo czasu na przywrócenie konta.

Wykonaj następujące kroki, aby zmienić domyślne opcje tworzenia kopii zapasowej dla istniejącego konta usługi Azure Cosmos:

1. Zaloguj się do [Azure Portal.](https://portal.azure.com/)
1. Przejdź do konta usługi Azure Cosmos i Otwórz okienko **& przywracanie kopii zapasowej** . W razie potrzeby zaktualizuj interwał tworzenia kopii zapasowych i okres przechowywania kopii zapasowych.

   * **Interwał tworzenia kopii zapasowych** — jest to interwał, w którym Azure Cosmos DB próbuje wykonać kopię zapasową danych. Kopia zapasowa pobiera niezerową ilość czasu, a w niektórych przypadkach może się nie powieść z powodu zależności podrzędnych. Azure Cosmos DB próbuje utworzyć kopię zapasową w skonfigurowanym interwale, ale nie gwarantuje to, że wykonywanie kopii zapasowej zakończy się w tym przedziale czasu. Tę wartość można skonfigurować w godzinach lub minutach. Interwał tworzenia kopii zapasowej nie może być krótszy niż 1 godzina i dłuższy niż 24 godziny. Po zmianie tego interwału nowy interwał zacznie obowiązywać od momentu utworzenia ostatniej kopii zapasowej.

   * **Przechowywanie kopii zapasowych** — reprezentuje okres, w którym każda kopia zapasowa jest zachowywana. Możesz ją skonfigurować w godzinach lub dniach. Minimalny okres przechowywania nie może być krótszy niż dwa razy dłuższy niż interwał wykonywania kopii zapasowych (w godzinach) i nie może być większy niż 720 godzin.

   * **Kopie przechowywanych danych** — domyślnie dwie kopie zapasowe danych są oferowane bezpłatnie. Jeśli potrzebujesz więcej niż dwóch kopii, występuje dodatkowa opłata. Zapoznaj się z sekcją zużyty magazyn na [stronie z cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/) , aby poznać dokładną cenę za dodatkowe kopie.

   :::image type="content" source="./media/configure-periodic-backup-restore/configure-backup-interval-retention.png" alt-text="Skonfiguruj interwał i przechowywanie kopii zapasowej dla istniejącego konta usługi Azure Cosmos." border="true":::

W przypadku konfigurowania opcji tworzenia kopii zapasowej podczas tworzenia konta można skonfigurować **zasady tworzenia kopii zapasowych**, które są **okresowe** lub **ciągłe**. Zasady okresowe umożliwiają skonfigurowanie interwału kopii zapasowych i przechowywania kopii zapasowych. Zasady ciągłe są obecnie dostępne tylko w przypadku rejestracji. Zespół Azure Cosmos DB oceni obciążenie i zatwierdzi Twoje żądanie.

:::image type="content" source="./media/configure-periodic-backup-restore/configure-periodic-continuous-backup-policy.png" alt-text="Skonfiguruj okresowe lub ciągłe zasady tworzenia kopii zapasowych dla nowych kont usługi Azure Cosmos." border="true":::

## <a name="request-data-restore-from-a-backup"></a><a id="request-restore"></a>Żądanie przywracania danych z kopii zapasowej

Jeśli przypadkowo usuniesz bazę danych lub kontener, możesz [utworzyć bilet pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) lub [wywoływać pomoc techniczną platformy Azure](https://azure.microsoft.com/support/options/) w celu przywrócenia danych z automatycznych kopii zapasowych online. Pomoc techniczna systemu Azure jest dostępna dla wybranych planów, takich jak **Standard**, **Developer** i plany wyższe niż te. Pomoc techniczna systemu Azure nie jest dostępna w planie **Basic** . Aby dowiedzieć się więcej o różnych planach pomocy technicznej, zobacz stronę [plany pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/) .

Aby przywrócić określoną migawkę kopii zapasowej, Azure Cosmos DB wymaga, aby dane były dostępne podczas cyklu tworzenia kopii zapasowej dla tej migawki.
Przed zażądaniem przywrócenia należy mieć następujące szczegóły:

* Przygotuj swój identyfikator subskrypcji.

* W zależności od tego, jak dane zostały przypadkowo usunięte lub zmodyfikowane, należy przygotować się do posiadania dodatkowych informacji. Zaleca się, aby te informacje były dostępne z wyprzedzeniem, aby zminimalizować liczbę ponownych prób, które mogą być szkodliwe w pewnych przypadkach.

* Jeśli całe konto Azure Cosmos DB zostanie usunięte, należy podać nazwę usuniętego konta. Jeśli utworzysz inne konto o tej samej nazwie co usunięte konto, udostępnij je zespołowi pomocy technicznej, ponieważ pomaga określić odpowiednie konto do wybrania. Zalecane jest, aby pliki były różne biletów pomocy technicznej dla każdego usuniętego konta, ponieważ minimalizuje to pomylenie stanu przywracania.

* Jeśli co najmniej jedna baza danych została usunięta, należy podać konto usługi Azure Cosmos oraz nazwę bazy danych usługi Azure Cosmos i określić, czy istnieje nowa baza danych o tej samej nazwie.

* W przypadku usunięcia co najmniej jednego kontenera należy podać nazwę konta usługi Azure Cosmos, nazwy baz danych i nazwy kontenerów. I określ, czy kontener o tej samej nazwie już istnieje.

* Jeśli przypadkowo usunięto lub uszkodzenie danych, należy skontaktować się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/options/) w ciągu 8 godzin, aby zespół Azure Cosmos DB mógł ułatwić przywrócenie danych z kopii zapasowych. **Przed utworzeniem żądania pomocy technicznej w celu przywrócenia danych pamiętaj o [zwiększeniu okresu przechowywania kopii zapasowych](#configure-backup-interval-retention) dla konta na co najmniej siedem dni. Najlepszym rozwiązaniem jest zwiększenie okresu przechowywania w ciągu 8 godzin od tego zdarzenia.** Dzięki temu zespół pomocy technicznej Azure Cosmos DB będzie miał wystarczająco dużo czasu na przywrócenie Twojego konta.

Oprócz nazw kont usługi Azure Cosmos, nazw baz danych i nazw kontenerów należy określić punkt w czasie, do którego można przywrócić dane. Ważne jest, aby zapewnić możliwie precyzyjne określenie najlepszych dostępnych kopii zapasowych. **Ważne jest również, aby określić godzinę w formacie UTC.**

Poniższy zrzut ekranu ilustruje sposób tworzenia żądania obsługi dla kontenera (kolekcji/wykresu/tabeli) w celu przywrócenia danych przy użyciu Azure Portal. Podaj inne szczegóły, takie jak typ danych, cel przywracania, czas, po usunięciu danych, aby pomóc nam określić priorytet żądania.

:::image type="content" source="./media/configure-periodic-backup-restore/backup-support-request-portal.png" alt-text="Utwórz żądanie obsługi kopii zapasowej za pomocą Azure Portal." border="true":::

## <a name="considerations-for-restoring-the-data-from-a-backup"></a>Zagadnienia dotyczące przywracania danych z kopii zapasowej

Dane można przypadkowo usunąć lub zmodyfikować w jednym z następujących scenariuszy:  

* Usuń całe konto usługi Azure Cosmos.

* Usuń co najmniej jedną bazę danych usługi Azure Cosmos.

* Usuń jeden lub więcej kontenerów usługi Azure Cosmos.

* Usuwanie lub modyfikowanie elementów usługi Azure Cosmos (na przykład dokumentów) w kontenerze. Ten konkretny przypadek jest zwykle nazywany uszkodzeniem danych.

* Baza danych udostępnionej oferty lub kontenery w ramach udostępnionej bazy danych oferty są usuwane lub uszkodzone.

Azure Cosmos DB może przywrócić dane we wszystkich powyższych scenariuszach. Podczas przywracania nowe konto usługi Azure Cosmos jest tworzone w celu przechowywania przywróconych danych. Nazwa nowego konta, jeśli nie jest określona, będzie mieć format `<Azure_Cosmos_account_original_name>-restored1` . Ostatnia cyfra jest zwiększana, gdy zostanie podjęta próba wykonania wielu operacji przywracania. Nie można przywrócić danych do wstępnie utworzonego konta usługi Azure Cosmos.

Po przypadkowe usunięciu konta usługi Azure Cosmos można przywrócić dane do nowego konta o tej samej nazwie, jeśli nazwa konta nie jest używana. Dlatego zalecamy, aby nie utworzyć ponownie konta po jego usunięciu. Ponieważ nie tylko uniemożliwia przywrócone dane, aby używały tej samej nazwy, ale również odnajduje odpowiednie konto, aby przywrócić trudno.

Po przypadkowej usunięciu bazy danych usługi Azure Cosmos można przywrócić całą bazę danych lub podzestaw kontenerów w tej bazie danych. Istnieje również możliwość wybrania określonych kontenerów między bazami danych i przywrócenia ich do nowego konta usługi Azure Cosmos.

Po Przypadkowe usunięcie lub zmodyfikowanie jednego lub większej liczby elementów w kontenerze (przypadek uszkodzenia danych) należy określić czas przywracania. Czas jest ważny w przypadku uszkodzenia danych. Ponieważ kontener jest aktywny, kopia zapasowa jest nadal uruchomiona, więc jeśli czas oczekiwania przekracza okres przechowywania (wartość domyślna to osiem godzin), kopie zapasowe zostaną nadpisywane. Aby zapobiec nadpisaniu kopii zapasowej, Zwiększ czas przechowywania kopii zapasowej dla konta na co najmniej siedem dni. Najlepszym rozwiązaniem jest zwiększenie okresu przechowywania w ciągu 8 godzin od uszkodzenia danych.

Jeśli przypadkowo usunięto lub uszkodzenie danych, należy skontaktować się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/options/) w ciągu 8 godzin, aby zespół Azure Cosmos DB mógł ułatwić przywrócenie danych z kopii zapasowych. Dzięki temu zespół pomocy technicznej Azure Cosmos DB będzie miał wystarczająco dużo czasu na przywrócenie Twojego konta.

> [!NOTE]
> Po przywróceniu danych nie wszystkie możliwości ani ustawienia źródłowe są przenoszone na przywrócone konto. Następujące ustawienia nie są przenoszone na nowe konto:
> * Listy kontroli dostępu do sieci wirtualnej
> * Procedury składowane, wyzwalacze i funkcje zdefiniowane przez użytkownika
> * Ustawienia wieloregionu  

Jeśli zainicjujesz przepływność na poziomie bazy danych, proces tworzenia kopii zapasowych i przywracania w tym przypadku odbywa się na całym poziomie bazy danych, a nie na poziomie poszczególnych kontenerów. W takich przypadkach nie można wybrać podzestawu kontenerów do przywrócenia.

## <a name="required-permissions-to-change-retention-or-restore-from-the-portal"></a>Uprawnienia wymagane do zmiany przechowywania lub przywracania z portalu
Podmioty zabezpieczeń będące częścią roli [CosmosdbBackupOperator](../role-based-access-control/built-in-roles.md#cosmosbackupoperator), Owner lub współautor mogą żądać przywrócenia lub zmienić okres przechowywania.

## <a name="understanding-costs-of-extra-backups"></a>Informacje o kosztach dodatkowych kopii zapasowych
Dwa kopie zapasowe są udostępniane bezpłatnie, a dodatkowe kopie zapasowe są naliczone zgodnie z cennikiem opartym na regionie dla magazynu kopii zapasowych opisanym w [cenniku usługi Backup Storage](https://azure.microsoft.com/en-us/pricing/details/cosmos-db/). Na przykład jeśli przechowywanie kopii zapasowych jest skonfigurowane na 240 godz., 10 dni i interwał tworzenia kopii zapasowej do 24 godzin. To oznacza 10 kopii kopii zapasowych danych. Zakładając, że 1 TB danych w regionie zachodnie stany USA 2, 1000 * 0,12 ~ $120 dla magazynu kopii zapasowych w danym miesiącu. 


## <a name="options-to-manage-your-own-backups"></a>Opcje zarządzania własnymi kopiami zapasowymi

Za pomocą Azure Cosmos DB kont interfejsu API SQL można również zachować własne kopie zapasowe, korzystając z jednej z następujących metod:

* Użyj [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) , aby okresowo przenosić dane do wybranego miejsca.

* Użyj Azure Cosmos DB [źródła zmian](change-feed.md) , aby okresowo odczytywać dane dla pełnych kopii zapasowych lub przyrostowych zmian, i przechowywać je w magazynie.

## <a name="post-restore-actions"></a>Akcje po przywróceniu

Głównym celem przywracania danych jest odzyskanie danych, które zostały przypadkowo usunięte lub zmodyfikowane. Dlatego zalecamy, aby najpierw sprawdzić zawartość odzyskanych danych, aby upewnić się, że jest to oczekiwane. Jeśli wszystko wygląda dobrze, można przeprowadzić migrację danych z powrotem do konta głównego. Chociaż istnieje możliwość użycia przywróconego konta jako nowego aktywnego konta, nie jest to zalecana opcja, jeśli masz obciążenia produkcyjne. 

Po przywróceniu danych otrzymasz powiadomienie o nazwie nowego konta (zwykle w formacie `<original-name>-restored1` ) i czasie, w którym konto zostało przywrócone. Przywrócone konto będzie miało taką samą przepływność, a zasady indeksowania i znajdują się w tym samym regionie co oryginalne konto. Użytkownik będący administratorem subskrypcji lub współadministratorem może zobaczyć przywrócone konto.

### <a name="migrate-data-to-the-original-account"></a>Migrowanie danych do oryginalnego konta

Poniżej przedstawiono różne sposoby migrowania danych z powrotem do oryginalnego konta:

* Użyj [Narzędzia do migracji danych Azure Cosmos DB](import-data.md).
* Użyj [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).
* Użyj [źródła zmian](change-feed.md) w Azure Cosmos DB.
* Można napisać własny kod niestandardowy.

Zaleca się usunięcie kontenera lub bazy danych bezpośrednio po przeprowadzeniu migracji danych. Jeśli nie usuniesz przywróconych baz danych lub kontenerów, zostaną naliczone opłaty za jednostki żądań, magazyn i ruch wychodzący.

## <a name="next-steps"></a>Następne kroki

* Aby wykonać żądanie przywrócenia, skontaktuj się z pomocą techniczną platformy Azure, Utwórz [bilet z Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
* Skonfiguruj ciągłą kopię zapasową i zarządzaj nią przy użyciu [Azure Portal](continuous-backup-restore-portal.md), [programu PowerShell](continuous-backup-restore-powershell.md), [interfejsu wiersza polecenia](continuous-backup-restore-command-line.md)lub [Azure Resource Manager](continuous-backup-restore-template.md).
* [Zarządzanie uprawnieniami](continuous-backup-restore-permissions.md) wymaganymi do przywracania danych z trybem ciągłej kopii zapasowej.