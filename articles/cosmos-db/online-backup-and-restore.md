---
title: Kopia zapasowa online i przywracanie danych na żądanie w Azure Cosmos DB
description: W tym artykule opisano, jak działa automatyczne tworzenie kopii zapasowych, przywracanie danych na żądanie, sposób konfigurowania interwału i przechowywania kopii zapasowych w Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 0db34a615c9d92401e760c702feb0dbbf13ce01d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91803878"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Kopia zapasowa online i przywracanie danych na żądanie w Azure Cosmos DB

Azure Cosmos DB automatycznie pobiera kopie zapasowe danych w regularnych odstępach czasu. Automatyczne kopie zapasowe są wykonywane bez wpływu na wydajność ani dostępność operacji bazy danych. Wszystkie kopie zapasowe są przechowywane oddzielnie w usłudze magazynu, a te kopie zapasowe są replikowane globalnie w celu odporności na awarie regionalne. Automatyczne kopie zapasowe są przydatne w scenariuszach, w których przypadkowo usunięto lub Zaktualizowano konto, bazę danych lub kontener usługi Azure Cosmos, a następnie wymagane jest odzyskanie danych.

## <a name="automatic-and-online-backups"></a>Automatyczne i online kopie zapasowe

W przypadku Azure Cosmos DB nie tylko Twoich danych, ale również kopie zapasowe danych są wysoce nadmiarowe i odporne na awarie regionalne. Poniższe kroki pokazują, jak Azure Cosmos DB wykonuje kopię zapasową danych:

* Azure Cosmos DB automatycznie wykonuje pełną kopię zapasową bazy danych co 4 godziny i w dowolnym momencie, domyślnie są przechowywane tylko najnowsze kopie zapasowe. Jeśli domyślne interwały nie są wystarczające dla obciążeń, można zmienić interwał kopii zapasowych i okres przechowywania z Azure Portal. Konfigurację kopii zapasowej można zmienić podczas tworzenia konta usługi Azure Cosmos lub po nim. W przypadku usunięcia kontenera lub bazy danych Azure Cosmos DB zachowywania istniejących migawek danego kontenera lub bazy danych przez 30 dni.

* Azure Cosmos DB przechowuje te kopie zapasowe w usłudze Azure Blob Storage, a rzeczywiste dane znajdują się lokalnie w Azure Cosmos DB.

* W celu zagwarantowania małych opóźnień migawka kopii zapasowej jest przechowywana w usłudze Azure Blob Storage w tym samym regionie co bieżący region zapisu (lub **jeden** z regionów zapisu w przypadku, gdy istnieje wieloregionowa Konfiguracja zapisu). Aby zapewnić odporność na awarie regionalne, każda migawka danych kopii zapasowej w usłudze Azure Blob Storage jest ponownie replikowana do innego regionu przez magazyn geograficznie nadmiarowy. Region, do którego jest replikowana kopia zapasowa, jest oparty na regionie źródłowym i parze regionalnej powiązanej z regionem źródłowym. Aby dowiedzieć się więcej, zobacz [listę par geograficznie nadmiarowych w regionach platformy Azure](../best-practices-availability-paired-regions.md) . Nie można uzyskać bezpośredniego dostępu do tej kopii zapasowej. Zespół usługi Azure Cosmos DB przywróci kopię zapasową na żądanie wysłane we wniosku o pomoc techniczną.

   Na poniższej ilustracji przedstawiono sposób tworzenia kopii Blob Storage zapasowej kontenera usługi Azure Cosmos ze wszystkimi trzema podstawowymi partycjami fizycznymi w regionie zachodnie stany USA.

  :::image type="content" source="./media/online-backup-and-restore/automatic-backup.png" alt-text="Okresowe pełne kopie zapasowe wszystkich jednostek Cosmos DB w usłudze GRS Azure Storage" border="false":::

* Kopie zapasowe są wykonywane bez wpływu na wydajność i dostępność aplikacji. Azure Cosmos DB wykonuje kopię zapasową danych w tle bez użycia dodatkowej, zainicjowanej przepływności (jednostek ru) ani wpływu na wydajność i dostępność bazy danych.

## <a name="options-to-manage-your-own-backups"></a>Opcje zarządzania własnymi kopiami zapasowymi

Za pomocą Azure Cosmos DB kont interfejsu API SQL można również zachować własne kopie zapasowe, korzystając z jednej z następujących metod:

* Użyj [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) , aby okresowo przenosić dane do wybranego miejsca.

* Użyj Azure Cosmos DB [źródła zmian](change-feed.md) , aby okresowo odczytywać dane dla pełnych kopii zapasowych lub przyrostowych zmian, i przechowywać je w magazynie.

## <a name="modify-the-backup-interval-and-retention-period"></a>Modyfikowanie interwału i okresu przechowywania kopii zapasowych

Azure Cosmos DB automatycznie wykonuje pełną kopię zapasową danych przez co 4 godziny i w dowolnym momencie są przechowywane najnowsze kopie zapasowe. Ta konfiguracja jest opcją domyślną i jest oferowana bez dodatkowych kosztów. Można zmienić domyślny interwał tworzenia kopii zapasowych i okres przechowywania podczas tworzenia konta usługi Azure Cosmos lub po utworzeniu konta. Konfiguracja kopii zapasowej jest ustawiana z poziomu konta usługi Azure Cosmos i należy ją skonfigurować na każdym koncie. Po skonfigurowaniu opcji tworzenia kopii zapasowej dla konta są one stosowane do wszystkich kontenerów w ramach tego konta. Obecnie opcje tworzenia kopii zapasowej można zmienić tylko w witrynie Azure Portal.

Jeśli dane zostały przypadkowo usunięte lub uszkodzone, **przed utworzeniem żądania obsługi w celu przywrócenia danych należy zwiększyć czas przechowywania kopii zapasowej dla konta na co najmniej siedem dni. Najlepszym rozwiązaniem jest zwiększenie okresu przechowywania w ciągu 8 godzin od tego zdarzenia.** Dzięki temu zespół Azure Cosmos DB ma wystarczająco dużo czasu na przywrócenie konta.

Wykonaj następujące kroki, aby zmienić domyślne opcje tworzenia kopii zapasowej dla istniejącego konta usługi Azure Cosmos:

1. Zaloguj się do [Azure Portal.](https://portal.azure.com/)
1. Przejdź do konta usługi Azure Cosmos i Otwórz okienko **& przywracanie kopii zapasowej** . W razie potrzeby zaktualizuj interwał tworzenia kopii zapasowych i okres przechowywania kopii zapasowych.

   * **Interwał tworzenia kopii zapasowych** — jest to interwał, w którym Azure Cosmos DB próbuje wykonać kopię zapasową danych. Kopia zapasowa pobiera niezerową ilość czasu, a w niektórych przypadkach może się nie powieść z powodu zależności podrzędnych. Azure Cosmos DB próbuje utworzyć kopię zapasową w skonfigurowanym interwale, ale nie gwarantuje to, że wykonywanie kopii zapasowej zakończy się w tym przedziale czasu. Tę wartość można skonfigurować w godzinach lub minutach. Interwał tworzenia kopii zapasowej nie może być krótszy niż 1 godzina i dłuższy niż 24 godziny. Po zmianie tego interwału nowy interwał zacznie obowiązywać od momentu utworzenia ostatniej kopii zapasowej.

   * **Przechowywanie kopii zapasowych** — reprezentuje okres, w którym każda kopia zapasowa jest zachowywana. Możesz ją skonfigurować w godzinach lub dniach. Minimalny okres przechowywania nie może być krótszy niż dwa razy dłuższy niż interwał wykonywania kopii zapasowych (w godzinach) i nie może być większy niż 720 godzin.

   * **Kopie przechowywanych danych** — domyślnie dwie kopie zapasowe danych są oferowane bezpłatnie. Jeśli potrzebujesz więcej niż dwóch kopii, występuje dodatkowa opłata. Zobacz sekcję Wykorzystany magazyn na stronie [Cennik](https://azure.microsoft.com/pricing/details/cosmos-db/), aby poznać dokładną cenę dodatkowych kopii.

   :::image type="content" source="./media/online-backup-and-restore/configure-backup-interval-retention.png" alt-text="Okresowe pełne kopie zapasowe wszystkich jednostek Cosmos DB w usłudze GRS Azure Storage" border="true":::

W przypadku konfigurowania opcji tworzenia kopii zapasowej podczas tworzenia konta można skonfigurować **zasady tworzenia kopii zapasowych**, które są **okresowe** lub **ciągłe**. Zasady okresowe umożliwiają skonfigurowanie interwału kopii zapasowych i przechowywania kopii zapasowych. Zasady ciągłe są obecnie dostępne tylko w przypadku rejestracji. Zespół Azure Cosmos DB oceni obciążenie i zatwierdzi Twoje żądanie.

:::image type="content" source="./media/online-backup-and-restore/configure-periodic-continuous-backup-policy.png" alt-text="Okresowe pełne kopie zapasowe wszystkich jednostek Cosmos DB w usłudze GRS Azure Storage" border="true":::

## <a name="restore-data-from-an-online-backup"></a>Przywracanie danych z kopii zapasowej online

Dane można przypadkowo usunąć lub zmodyfikować w jednym z następujących scenariuszy:  

* Usuń całe konto usługi Azure Cosmos.

* Usuń co najmniej jedną bazę danych usługi Azure Cosmos.

* Usuń jeden lub więcej kontenerów usługi Azure Cosmos.

* Usuwanie lub modyfikowanie elementów usługi Azure Cosmos (na przykład dokumentów) w kontenerze. Ten konkretny przypadek jest zwykle nazywany uszkodzeniem danych.

* Baza danych udostępnionej oferty lub kontenery w ramach udostępnionej bazy danych oferty są usuwane lub uszkodzone.

Azure Cosmos DB może przywrócić dane we wszystkich powyższych scenariuszach. Podczas przywracania nowe konto usługi Azure Cosmos jest tworzone w celu przechowywania przywróconych danych. Nazwa nowego konta, jeśli nie jest określona, będzie mieć format `<Azure_Cosmos_account_original_name>-restored1` . Ostatnia cyfra jest zwiększana, gdy zostanie podjęta próba wykonania wielu operacji przywracania. Nie można przywrócić danych do wstępnie utworzonego konta usługi Azure Cosmos.

Po przypadkowe usunięciu konta usługi Azure Cosmos można przywrócić dane do nowego konta o tej samej nazwie, pod warunkiem, że nazwa konta nie jest używana. Dlatego zalecamy, aby nie utworzyć ponownie konta po jego usunięciu. Ponieważ nie tylko uniemożliwia przywrócone dane, aby używały tej samej nazwy, ale również odnajduje odpowiednie konto, aby przywrócić trudno.

Po przypadkowej usunięciu bazy danych usługi Azure Cosmos można przywrócić całą bazę danych lub podzestaw kontenerów w tej bazie danych. Istnieje również możliwość wybrania określonych kontenerów między bazami danych i przywrócenia ich do nowego konta usługi Azure Cosmos.

Po Przypadkowe usunięcie lub zmodyfikowanie jednego lub większej liczby elementów w kontenerze (przypadek uszkodzenia danych) należy określić czas przywracania. Czas jest ważny w przypadku uszkodzenia danych. Ponieważ kontener jest aktywny, kopia zapasowa jest nadal uruchomiona, więc jeśli czas oczekiwania przekracza okres przechowywania (wartość domyślna to osiem godzin), kopie zapasowe zostaną nadpisywane. **Aby zapobiec nadpisaniu kopii zapasowej, Zwiększ czas przechowywania kopii zapasowej dla konta na co najmniej siedem dni. Najlepszym rozwiązaniem jest zwiększenie okresu przechowywania w ciągu 8 godzin od uszkodzenia danych.**

Jeśli przypadkowo usunięto lub uszkodzenie danych, należy skontaktować się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/options/) w ciągu 8 godzin, aby zespół Azure Cosmos DB mógł ułatwić przywrócenie danych z kopii zapasowych. Dzięki temu zespół pomocy technicznej Azure Cosmos DB będzie miał wystarczająco dużo czasu na przywrócenie Twojego konta.

> [!NOTE]
> Po przywróceniu danych nie wszystkie możliwości ani ustawienia źródłowe są przenoszone na przywrócone konto. Następujące ustawienia nie są przenoszone na nowe konto:

> * Listy kontroli dostępu do sieci wirtualnej
> * Procedury składowane, wyzwalacze i funkcje zdefiniowane przez użytkownika
> * Ustawienia wieloregionu  

Jeśli zainicjujesz przepływność na poziomie bazy danych, proces tworzenia kopii zapasowych i przywracania w tym przypadku odbywa się na całym poziomie bazy danych, a nie na poziomie poszczególnych kontenerów. W takich przypadkach nie można wybrać podzestawu kontenerów do przywrócenia.

## <a name="migrate-data-to-the-original-account"></a>Migrowanie danych do oryginalnego konta

Głównym celem przywracania danych jest odzyskanie danych, które zostały przypadkowo usunięte lub zmodyfikowane. Dlatego zalecamy, aby najpierw sprawdzić zawartość odzyskanych danych, aby upewnić się, że jest to oczekiwane. Później można migrować dane z powrotem do konta głównego. Chociaż istnieje możliwość użycia przywróconego konta jako nowego aktywnego konta, nie jest to zalecana opcja, jeśli masz obciążenia produkcyjne.  

Poniżej przedstawiono różne sposoby migrowania danych z powrotem do oryginalnego konta usługi Azure Cosmos:

* Użyj [Narzędzia do migracji danych Azure Cosmos DB](import-data.md).
* Użyj [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).
* Użyj [źródła zmian](change-feed.md) w Azure Cosmos DB.
* Można napisać własny kod niestandardowy.

Pamiętaj o usunięciu przywróconych kont zaraz po przeprowadzeniu migracji danych, ponieważ będą one powodować naliczanie opłat.

## <a name="next-steps"></a>Następne kroki

Następnie możesz dowiedzieć się, jak przywrócić dane z konta usługi Azure Cosmos lub dowiedzieć się, jak przeprowadzić migrację danych do konta usługi Azure Cosmos

* Aby wykonać żądanie przywrócenia, skontaktuj się z pomocą techniczną platformy Azure, Utwórz [bilet z Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Jak przywrócić dane z konta usługi Azure Cosmos](how-to-backup-and-restore.md)
* Aby przenieść dane do Azure Cosmos DB, [Użyj kanału informacyjnego zmiany Cosmos DB](change-feed.md) .
* Przenieś dane do Azure Cosmos DB [za pomocą Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) .

