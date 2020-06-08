---
title: Przechowywanie i utrwalanie danych aplikacji w chmurze oraz zarządzanie nimi za pomocą usług Visual Studio App Center i Azure
description: Dowiedz się więcej na temat usług, takich jak Visual Studio App Center, które umożliwiają przechowywanie i utrwalanie danych aplikacji mobilnych w chmurze oraz zarządzanie nimi.
author: codemillmatt
ms.assetid: 12344321-0123-4678-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/05/2020
ms.author: masoucou
ms.openlocfilehash: dc92b187d1bef52339466cf46dbabde7662e95f9
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2020
ms.locfileid: "84482546"
---
# <a name="store-sync-and-query-mobile-application-data-from-the-cloud"></a>Przechowuj, Synchronizuj i badaj dane aplikacji mobilnych z chmury
Niezależnie od tego, jakiego rodzaju aplikacja została utworzona, prawdopodobnie będziesz generować i przetwarzać dane. Użytkownicy Twojej aplikacji mają duże oczekiwania. Chcą, aby aplikacja działała szybko i bezproblemowo, we wszystkich przypadkach. Większość aplikacji również pracuje na wielu urządzeniach. Możesz uzyskać dostęp do aplikacji z poziomu pulpitu lub urządzenia przenośnego. Wielu użytkowników może korzystać z aplikacji w tym samym czasie i udostępniać dane przy użyciu oczekiwań, aby uzyskiwać dostęp do danych na bieżąco i w czasie rzeczywistym.

Użytkownicy aplikacji nie będą mieć zawsze łączności z Internetem. Aplikacje są przeznaczone do pracy z połączeniem internetowym lub bez niego. Deweloperzy muszą wybrać odpowiednie rozwiązanie do przechowywania i synchronizowania danych w chmurze w celu zapewnienia doskonałego środowiska klienta dla swojej aplikacji.

Firma Microsoft oferuje różne usługi, które eliminują potrzebę zwiększania liczby serwerów, wybierania bazy danych lub przejmowania się skalowaniem lub zabezpieczeniami w celu zapewnienia jak największej możliwości. Te usługi zapewniają wspaniałe środowisko programistyczne, które umożliwia przechowywanie danych aplikacji w chmurze przy użyciu interfejsów API SQL lub NoSQL. Możesz także synchronizować dane na wszystkich urządzeniach i umożliwić aplikacji działanie z połączeniem sieciowym lub bez niego, aby ułatwić tworzenie skalowalnych i niezawodnych aplikacji.

Użyj następujących usług, aby zarządzać danymi aplikacji mobilnych i przechowywać je w chmurze.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) to globalnie dystrybuowana, wielomodelowa usługa bazy danych. Można go użyć do kompilowania aplikacji globalnej. Dzięki Azure Cosmos DB można elastycznie i niezależnie skalować przepływność oraz magazyn w dowolnej liczbie regionów świadczenia usługi Azure na całym świecie. Możesz korzystać z szybkiego, jednocyfrowego dostępu do danych, korzystając z ulubionych powierzchni interfejsu API. Te powierzchnie obejmują SQL, MongoDB, Cassandra, tabele lub Gremlin. Azure Cosmos DB jednoznacznie zapewnia kompleksowe umowy dotyczące poziomu usług (umowy SLA) na potrzeby przepływności, opóźnień, dostępności i spójności.

**Najważniejsze funkcje**
- Obsługuje szeroką gamę interfejsów API, w tym interfejs API SQL (rdzeń), interfejs API Cassandra, interfejs API MongoDB, interfejs API Gremlin i interfejs API tabel.
- Gotowe Global Distribution replikuje dane wszędzie tam, gdzie jesteś użytkownikami. Użytkownicy mogą współdziałać z repliką danych, które znajdują się najbliżej nich.
- Brak zarządzania schematem lub indeksem, ponieważ aparat bazy danych jest w pełni niezależny od schematem.
- Powszechna obecność regionalna, ponieważ Azure Cosmos DB jest dostępna we wszystkich regionach świadczenia usługi Azure na całym świecie, co obejmuje 54 + regiony w chmurze publicznej.
- Precyzyjnie zdefiniowana, wiele opcji spójności, ponieważ protokół replikacji wielu wzorców Azure Cosmos DB jest starannie zaprojektowany w celu zaoferowania pięciu dobrze zdefiniowanych opcji spójności. Te pięć opcji są silne, ograniczone, nieodświeżone, sesja, spójny prefiks i ostateczne.
- dostępność na 99,999% dla operacji odczytu i zapisu.
- Program programistyczny (lub za pośrednictwem Azure Portal) wywołuje regionalną pracę w trybie failover na koncie Azure Cosmos DB, aby upewnić się, że aplikacja została zaprojektowana w celu wytrzymania regionalnego awarii.
- Gwarantowane małe opóźnienia w 99 percentylu na całym świecie.

**Dokumentacja**
- [Azure Portal](https://portal.azure.com) 
- [Dokumentacja usługi Azure Cosmos DB](/azure/cosmos-db/introduction)

## <a name="azure-sql-database"></a>Azure SQL Database
 [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) to usługa zarządzana relacyjnej bazy danych ogólnego przeznaczenia. Można go użyć do utworzenia warstwy magazynu danych o wysokiej dostępności dla aplikacji i rozwiązań w chmurze platformy Azure.

**Najważniejsze funkcje**
- **Modele i narzędzia Elastic Database:** Elastyczna baza danych pozwala deweloperom na pule zasobów między grupami baz danych do skalowania. Aby zarządzać tymi zasobami administracyjnymi, należy przesłać skrypt jako zadanie. Następnie baza danych SQL wykonuje skrypt w bazach danych.
- **Wysoka wydajność:** Aplikacje o wysokiej przepływności mogą korzystać z najnowszej wersji. Zapewnia 25% więcej mocy bazy danych w warstwie Premium.
- **Kopie zapasowe, replikacja i wysoka dostępność:** Wbudowana replikacja i Umowa SLA firmy Microsoft na poziomie bazy danych zapewniają ciągłość działania aplikacji oraz ochronę przed katastrofami. Aktywna replikacja geograficzna umożliwia skonfigurowanie trybu failover i samoobsługowego odzyskiwania, co zapewnia pełną kontrolę nad "usługą". Przywracanie danych jest dostępne z kopii zapasowych danych przez maksymalnie 35 dni.
- **Konserwacja niemal 0:** Automatyczne oprogramowanie jest częścią usługi. Wbudowane repliki systemu pomagają zapewnić wewnętrzną ochronę danych, nieprzerwaną pracę bazy danych i stabilność systemu. Repliki systemu są automatycznie przenoszone na nowe komputery. Są one inicjowane na bieżąco w przypadku niepowodzenia starych.
- **Zabezpieczenia:** Azure SQL Database oferuje portfolio funkcji zabezpieczeń, które są zgodne z obowiązującymi w branży zasadami zgodności:
    - Inspekcja zapewnia deweloperom możliwość wykonywania zadań związanych z zgodnością i uzyskiwania informacji o działaniach.
    - Deweloperzy i mogą wdrożyć zasady na poziomie bazy danych, aby ograniczyć dostęp do poufnych danych z zabezpieczeniami na poziomie wierszy, dynamicznym maską danych i przezroczystym szyfrowaniem danych dla Azure SQL Database.
    - Azure SQL Database jest weryfikowana przez najważniejszych audytorów chmury w ramach zakresu najważniejszych certyfikatów i zatwierdzeń zgodności platformy Azure, takich jak HIPAA BAA, ISO/IEC 27001:2005, FedRAMP i klauzule modelowe UE.

**Dokumentacja**
- [Azure Portal](https://portal.azure.com) 
- [Dokumentacja usługi Azure SQL Database](/azure/sql-database/) 
