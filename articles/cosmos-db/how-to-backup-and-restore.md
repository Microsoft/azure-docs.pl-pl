---
title: Jak przywrócić Azure Cosmos DB dane z kopii zapasowej
description: W tym artykule opisano, jak przywrócić Azure Cosmos DB dane z kopii zapasowej, jak skontaktować się z pomocą techniczną platformy Azure w celu przywrócenia danych, czynności, które należy wykonać po przywróceniu danych.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/24/2020
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 9956ca0ca9c0957557e7ee74883a75c074ff22f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88797976"
---
# <a name="restore-data-from-a-backup-in-azure-cosmos-db"></a>Przywracanie danych z kopii zapasowej w Azure Cosmos DB

Jeśli przypadkowo usuniesz bazę danych lub kontener, możesz [utworzyć bilet pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) lub [wywoływać pomoc techniczną platformy Azure](https://azure.microsoft.com/support/options/) w celu przywrócenia danych z automatycznych kopii zapasowych online. Pomoc techniczna systemu Azure jest dostępna dla wybranych planów, takich jak **Standard**, **Developer**i plany wyższe niż te. Pomoc techniczna systemu Azure nie jest dostępna w planie **Basic** . Aby dowiedzieć się więcej o różnych planach pomocy technicznej, zobacz stronę [plany pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/) .

Aby przywrócić określoną migawkę kopii zapasowej, Azure Cosmos DB wymaga, aby dane były dostępne na czas trwania cyklu tworzenia kopii zapasowej dla tej migawki.

## <a name="request-a-restore"></a>Zażądaj przywrócenia

Przed zażądaniem przywrócenia należy mieć następujące szczegóły:

* Przygotuj swój identyfikator subskrypcji.

* W zależności od tego, jak dane zostały przypadkowo usunięte lub zmodyfikowane, należy przygotować się do posiadania dodatkowych informacji. Zaleca się, aby te informacje były dostępne z wyprzedzeniem, aby zminimalizować liczbę ponownych prób, które mogą być szkodliwe w pewnych przypadkach.

* Jeśli całe konto Azure Cosmos DB zostanie usunięte, należy podać nazwę usuniętego konta. Jeśli utworzysz inne konto o tej samej nazwie co usunięte konto, udostępnij je zespołowi pomocy technicznej, ponieważ pomaga określić odpowiednie konto do wybrania. Zalecane jest, aby pliki były różne biletów pomocy technicznej dla każdego usuniętego konta, ponieważ minimalizuje to pomylenie stanu przywracania.

* Jeśli co najmniej jedna baza danych została usunięta, należy podać konto usługi Azure Cosmos, a także nazwę bazy danych usługi Azure Cosmos i określić, czy istnieje nowa baza danych o takiej samej nazwie.

* W przypadku usunięcia co najmniej jednego kontenera należy podać nazwę konta usługi Azure Cosmos, nazwy baz danych i nazwy kontenerów. I określ, czy kontener o tej samej nazwie już istnieje.

* Jeśli przypadkowo usunięto lub uszkodzenie danych, należy skontaktować się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/options/) w ciągu 8 godzin, aby zespół Azure Cosmos DB mógł ułatwić przywrócenie danych z kopii zapasowych. **Przed utworzeniem żądania pomocy technicznej w celu przywrócenia danych pamiętaj o [zwiększeniu okresu przechowywania kopii zapasowych](online-backup-and-restore.md) dla konta na co najmniej siedem dni. Najlepszym rozwiązaniem jest zwiększenie okresu przechowywania w ciągu 8 godzin od tego zdarzenia.** Dzięki temu zespół pomocy technicznej Azure Cosmos DB będzie miał wystarczająco dużo czasu na przywrócenie Twojego konta.

Oprócz nazw kont usługi Azure Cosmos, nazw baz danych i nazw kontenerów należy określić punkt w czasie, do którego można przywrócić dane. Ważne jest, aby zapewnić możliwie precyzyjne określenie najlepszych dostępnych kopii zapasowych. **Ważne jest również, aby określić godzinę w formacie UTC.**

Poniższy zrzut ekranu ilustruje sposób tworzenia żądania obsługi dla kontenera (kolekcji/wykresu/tabeli) w celu przywrócenia danych przy użyciu Azure Portal. Podaj dodatkowe szczegóły, takie jak typ danych, cel przywracania, czas, po usunięciu danych, aby pomóc nam określić priorytet żądania.

:::image type="content" source="./media/how-to-backup-and-restore/backup-support-request-portal.png" alt-text="Utwórz żądanie obsługi kopii zapasowej za pomocą Azure Portal":::

## <a name="post-restore-actions"></a>Akcje po przywróceniu

Po przywróceniu danych otrzymasz powiadomienie o nazwie nowego konta (zwykle w formacie `<original-name>-restored1` ) i czasie, w którym konto zostało przywrócone. Przywrócone konto będzie miało taką samą przepływność, a zasady indeksowania i znajdują się w tym samym regionie co oryginalne konto. Użytkownik będący administratorem subskrypcji lub współadministratorem może zobaczyć przywrócone konto.

Po przywróceniu danych należy sprawdzić i sprawdzić poprawność danych na przywróconym koncie i upewnić się, że zawiera ona oczekiwaną wersję. Jeśli wszystko wygląda dobrze, należy przeprowadzić migrację danych z powrotem do oryginalnego konta przy użyciu [Azure Cosmos DB zmian](change-feed.md) lub [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

Zaleca się usunięcie kontenera lub bazy danych bezpośrednio po przeprowadzeniu migracji danych. Jeśli nie usuniesz przywróconych baz danych lub kontenerów, zostaną naliczone opłaty za jednostki żądań, magazyn i ruch wychodzący.

## <a name="next-steps"></a>Następne kroki

Następnie możesz dowiedzieć się, jak przeprowadzić migrację danych z powrotem do oryginalnego konta, korzystając z następujących artykułów:

* Aby wykonać żądanie przywrócenia, skontaktuj się z pomocą techniczną platformy Azure, Utwórz [bilet z Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* Aby przenieść dane do Azure Cosmos DB, [Użyj kanału informacyjnego zmiany Cosmos DB](change-feed.md) .

* Przenieś dane do Azure Cosmos DB [za pomocą Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) .
