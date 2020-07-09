---
title: Kopia zapasowa online i przywracanie danych na żądanie w Azure Cosmos DB
description: W tym artykule opisano, jak automatyczne przywracanie danych online i wykonywanie kopii zapasowych na żądanie działa w Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 8ed9e23b178b8eeefbd3c3a690491124e6901180
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85112926"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Kopia zapasowa online i przywracanie danych na żądanie w Azure Cosmos DB

Azure Cosmos DB automatycznie pobiera kopie zapasowe danych w regularnych odstępach czasu. Automatyczne kopie zapasowe są wykonywane bez wpływu na wydajność ani dostępność operacji bazy danych. Wszystkie kopie zapasowe są przechowywane oddzielnie w usłudze magazynu, a te kopie zapasowe są replikowane globalnie w celu odporności na awarie regionalne. Automatyczne kopie zapasowe są przydatne w scenariuszach, w których przypadkowo usunięto lub Zaktualizowano konto, bazę danych lub kontener usługi Azure Cosmos, a następnie wymagane jest odzyskanie danych.

## <a name="automatic-and-online-backups"></a>Automatyczne i online kopie zapasowe

W przypadku Azure Cosmos DB nie tylko Twoich danych, ale również kopie zapasowe danych są wysoce nadmiarowe i odporne na awarie regionalne. Poniższe kroki pokazują, jak Azure Cosmos DB wykonuje kopię zapasową danych:

* Usługa Azure Cosmos DB automatycznie wykonuje kopię zapasową bazy danych co 4 godziny, a w dowolnym momencie są przechowywane tylko 2 ostatnie kopie zapasowe. Jeśli jednak kontener lub baza danych zostanie usunięta, usługa Azure Cosmos DB zachowuje istniejące migawki danego kontenera lub bazy danych przez 30 dni.

* Azure Cosmos DB przechowuje te kopie zapasowe w usłudze Azure Blob Storage, a rzeczywiste dane znajdują się lokalnie w Azure Cosmos DB.

*  W celu zagwarantowania małych opóźnień migawka kopii zapasowej jest przechowywana w usłudze Azure Blob Storage w tym samym regionie co bieżący region zapisu (lub jeden z regionów zapisu w przypadku, gdy masz konfigurację z wieloma wzorcami) konta bazy danych Azure Cosmos. Aby zapewnić odporność na awarie regionalne, każda migawka danych kopii zapasowej w usłudze Azure Blob Storage jest ponownie replikowana do innego regionu za pomocą magazynu geograficznie nadmiarowego (GRS). Region, do którego jest replikowana kopia zapasowa, jest oparty na regionie źródłowym i pary regionalnej skojarzonej z regionem źródłowym. Aby dowiedzieć się więcej, zobacz [listę par geograficznie nadmiarowych w regionach platformy Azure](../best-practices-availability-paired-regions.md) . Nie można uzyskać dostępu bezpośrednio do tej kopii zapasowej. Azure Cosmos DB będzie używać tej kopii zapasowej tylko wtedy, gdy zostanie zainicjowane przywracanie kopii zapasowej.

* Kopie zapasowe są wykonywane bez wpływu na wydajność i dostępność aplikacji. Azure Cosmos DB wykonuje kopię zapasową danych w tle bez użycia dodatkowej, zainicjowanej przepływności (jednostek ru) ani wpływu na wydajność i dostępność bazy danych.

* Jeśli przypadkowo usunięto lub uszkodzenie danych, należy skontaktować się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/options/) w ciągu 8 godzin, aby zespół Azure Cosmos DB mógł ułatwić przywrócenie danych z kopii zapasowych.

Na poniższej ilustracji przedstawiono sposób tworzenia kopii Blob Storage zapasowej kontenera usługi Azure Cosmos ze wszystkimi trzema podstawowymi partycjami fizycznymi w regionie zachodnie stany USA.

:::image type="content" source="./media/online-backup-and-restore/automatic-backup.png" alt-text="Okresowe pełne kopie zapasowe wszystkich jednostek Cosmos DB w usłudze GRS Azure Storage" border="false":::

## <a name="options-to-manage-your-own-backups"></a>Opcje zarządzania własnymi kopiami zapasowymi

Za pomocą Azure Cosmos DB kont interfejsu API SQL można również zachować własne kopie zapasowe, korzystając z jednej z następujących metod:

* Użyj [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) , aby okresowo przenosić dane do wybranego miejsca.

* Użyj Azure Cosmos DB [źródła zmian](change-feed.md) , aby okresowo odczytywać dane dla pełnych kopii zapasowych, a także w przypadku zmian przyrostowych i przechowywać je w magazynie.

## <a name="backup-retention-period"></a>Okres przechowywania kopii zapasowej

Azure Cosmos DB wykonuje migawki danych co cztery godziny. W danym momencie tylko ostatnie dwie migawki są zachowywane. Jeśli jednak kontener lub baza danych zostanie usunięta, usługa Azure Cosmos DB zachowuje istniejące migawki danego kontenera lub bazy danych przez 30 dni.

## <a name="restoring-data-from-online-backups"></a>Przywracanie danych z kopii zapasowych online

Przypadkowe usunięcie lub zmodyfikowanie danych może wystąpić w jednym z następujących scenariuszy:  

* Całe konto usługi Azure Cosmos zostało usunięte

* Co najmniej jedna baza danych usługi Azure Cosmos jest usuwana

* Co najmniej jeden kontener usługi Azure Cosmos został usunięty

* Elementy usługi Azure Cosmos (na przykład dokumenty) w kontenerze są usuwane lub modyfikowane. Ten konkretny przypadek jest zwykle określany jako "uszkodzenie danych".

* Baza danych udostępnionej oferty lub kontenery w ramach udostępnionej bazy danych oferty są usuwane lub uszkodzone

Azure Cosmos DB może przywrócić dane we wszystkich powyższych scenariuszach. Proces przywracania zawsze tworzy nowe konto usługi Azure Cosmos na potrzeby przechowywania przywróconych danych. Nazwa nowego konta, jeśli nie zostanie określona, będzie miała format `<Azure_Cosmos_account_original_name>-restored1` . Ostatnia cyfra jest zwiększana, jeśli zostanie podjęta próba wielokrotnej operacji przywracania. Nie można przywrócić danych do wstępnie utworzonego konta usługi Azure Cosmos.

Po usunięciu konta usługi Azure Cosmos można przywrócić dane do konta o tej samej nazwie, pod warunkiem, że nazwa konta nie jest używana. W takich przypadkach zaleca się, aby nie utworzyć ponownie konta po usunięciu, ponieważ nie tylko uniemożliwia przywrócone dane, aby używały tej samej nazwy, ale również odnajduje odpowiednie konto, aby przywrócić je z trudniejszych. 

Po usunięciu bazy danych usługi Azure Cosmos istnieje możliwość przywrócenia całej bazy danych lub podzbioru kontenerów w tej bazie danych. Istnieje również możliwość wybrania kontenerów między bazami danych i przywrócenia ich, a wszystkie przywrócone dane są umieszczane na nowym koncie usługi Azure Cosmos.

Gdy co najmniej jeden element w kontenerze zostanie przypadkowo usunięty lub zmieniony (przypadek uszkodzenia danych), należy określić czas przywracania. W tym przypadku czas jest częścią. Ponieważ kontener jest aktywny, kopia zapasowa jest nadal uruchomiona, więc jeśli czas oczekiwania przekracza okres przechowywania (wartość domyślna to osiem godzin), kopie zapasowe zostaną nadpisywane. W przypadku usunięć dane nie są już przechowywane, ponieważ nie zostaną zastąpione przez cykl tworzenia kopii zapasowych. Kopie zapasowe usuniętych baz danych lub kontenerów są zapisywane przez 30 dni.

W przypadku aprowizacji przepływności na poziomie bazy danych (czyli w przypadku, gdy zestaw kontenerów udostępnia przepustą przepływność), proces tworzenia kopii zapasowej i przywracania w tym przypadku ma miejsce na całym poziomie bazy danych, a nie na poziomie poszczególnych kontenerów. W takich przypadkach wybór podzbioru kontenerów do przywrócenia nie jest opcją.

## <a name="migrating-data-to-the-original-account"></a>Migrowanie danych do oryginalnego konta

Głównym celem przywracania danych jest zapewnienie możliwości odzyskania wszelkich przypadkowo usuniętych lub modyfikowanych danych. Dlatego zalecamy, aby najpierw sprawdzić zawartość odzyskanych danych, aby upewnić się, że jest to oczekiwane. Następnie Pracuj nad migracją danych z powrotem do konta podstawowego. Mimo że można użyć przywróconego konta jako konta na żywo, nie jest to zalecana opcja, jeśli masz obciążenia produkcyjne.  

Poniżej przedstawiono różne sposoby migrowania danych z powrotem do oryginalnego konta usługi Azure Cosmos:

* Korzystanie z [Narzędzia do migracji danych Cosmos DB](import-data.md)
* Używanie [Azure Data Factory]( ../data-factory/connector-azure-cosmos-db.md)
* Używanie [źródła zmian](change-feed.md) w Azure Cosmos DB 
* Napisz kod niestandardowy

Po zakończeniu migracji Usuń przywrócone konta, ponieważ będą one powodować naliczanie opłat.

## <a name="next-steps"></a>Następne kroki

Następnie możesz dowiedzieć się, jak przywrócić dane z konta usługi Azure Cosmos lub dowiedzieć się, jak przeprowadzić migrację danych do konta usługi Azure Cosmos

* Aby wykonać żądanie przywrócenia, skontaktuj się z pomocą techniczną platformy Azure, Utwórz [bilet z Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Jak przywrócić dane z konta usługi Azure Cosmos](how-to-backup-and-restore.md)
* Aby przenieść dane do Azure Cosmos DB, [Użyj kanału informacyjnego zmiany Cosmos DB](change-feed.md) .
* Przenieś dane do Azure Cosmos DB [za pomocą Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) .

