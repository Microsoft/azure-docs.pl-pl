---
title: Przyspieszone odzyskiwanie bazy danych
titleSuffix: Azure SQL
description: Szybsze odzyskiwanie bazy danych zapewnia szybkie i spójne odzyskiwanie bazy danych, natychmiastowe wycofywanie transakcji oraz agresywne obcinanie dzienników dla baz danych w portfolio Azure SQL.
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/19/2020
ms.openlocfilehash: bd1b33b2f6b1b0e0bf94639b3991b6507a89f5a9
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400798"
---
# <a name="accelerated-database-recovery-in-azure-sql"></a>Szybsze odzyskiwanie bazy danych w usłudze Azure SQL 
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

**Szybsze odzyskiwanie bazy danych (ADR)** to funkcja aparatu bazy danych SQL Server, która znacznie zwiększa dostępność bazy danych, szczególnie w przypadku długotrwałych transakcji, przez przeprojektowanie procesu odzyskiwania aparatu SQL Server Database. 

Usługa ADR jest obecnie dostępna dla Azure SQL Database, wystąpienia zarządzanego usługi Azure SQL, baz danych w usłudze Azure Synapse Analytics i SQL Server na maszynach wirtualnych platformy Azure, począwszy od SQL Server 2019. 

> [!NOTE] 
> Funkcja ADR jest domyślnie włączona w Azure SQL Database i wystąpienie zarządzane SQL platformy Azure i wyłączanie reguły ADR dla każdego produktu nie jest obsługiwane. 

## <a name="overview"></a>Omówienie

Podstawowe korzyści wynikające z ADR są następujące:

- **Szybkie i spójne odzyskiwanie bazy danych**

  W przypadku opcji ADR długotrwałe transakcje nie wpływają na ogólny czas odzyskiwania, umożliwiając szybkie i spójne odzyskiwanie bazy danych niezależnie od liczby aktywnych transakcji w systemie lub ich rozmiarów.

- **Natychmiastowe wycofywanie transakcji**

  Za pomocą reguły ADR wycofywanie transakcji jest chwilowo, niezależnie od czasu aktywności transakcji lub liczby wykonanych aktualizacji.

- **Agresywne obcinanie dziennika**

  W przypadku korzystania z reguły ADR dziennik transakcji jest agresywnie obcinany, nawet w obecności aktywnych długotrwałych transakcji, które uniemożliwiają przeprowadzenie kontroli.

## <a name="standard-database-recovery-process"></a>Standardowy proces odzyskiwania bazy danych

Odzyskiwanie bazy danych jest zgodne z modelem odzyskiwania [Aries](https://people.eecs.berkeley.edu/~brewer/cs262/Aries.pdf) i składa się z trzech faz, które przedstawiono na poniższym diagramie i wyjaśniono bardziej szczegółowo po diagramie.

![bieżący proces odzyskiwania](./media/accelerated-database-recovery/current-recovery-process.png)

- **Faza analizy**

  Przeprowadź skanowanie dziennika transakcji od początku ostatniego pomyślnego punktu kontrolnego (lub najstarszego stanu LSN strony) do końca, aby określić stan każdej transakcji w chwili zatrzymania bazy danych.

- **Wykonaj ponownie fazę**

  Przeprowadź skanowanie dziennika transakcji od najstarszej niezatwierdzonej transakcji do końca, aby przenieść bazę danych do stanu, który był w chwili awarii, wykonując ponownie wszystkie operacje zatwierdzone.

- **Faza cofania**

  W przypadku każdej transakcji, która była aktywna jako czas awarii, przejdzie do tyłu dziennika, cofając operacje wykonywane przez tę transakcję.

W oparciu o ten projekt czas potrzebny aparat bazy danych SQL Server do odzyskania po nieoczekiwanym ponownym uruchomieniu jest (w przybliżeniu) proporcjonalny do rozmiaru najdłuższej aktywnej transakcji w systemie w chwili awarii. Odzyskiwanie wymaga wycofania wszystkich nieukończonych transakcji. Czas wymagany jest proporcjonalny do pracy wykonanej przez transakcję i czasu, gdy był aktywny. W związku z tym proces odzyskiwania może zająć dużo czasu w obecności długotrwałych transakcji (takich jak duże operacje wstawiania zbiorczego lub operacje kompilowania indeksów względem dużej tabeli).

Ponadto anulowanie i wycofywanie dużej transakcji na podstawie tego projektu może być również czasochłonne, ponieważ używa tej samej fazy odzyskiwania cofania, jak opisano powyżej.

Ponadto aparat bazy danych SQL Server nie może obciąć dziennika transakcji, jeśli istnieją długotrwałe transakcje, ponieważ odpowiednie rekordy dzienników są zbędne dla procesów odzyskiwania i wycofywania. W wyniku tego projektu aparatu bazy danych SQL Server, niektórzy klienci napotykają problem polegający na tym, że rozmiar dziennika transakcji jest zbyt duży i zużywa ogromną ilość miejsca na dysku.

## <a name="the-accelerated-database-recovery-process"></a>Proces odzyskiwania przyspieszonej bazy danych

ADR eliminuje powyższe problemy poprzez całkowite przeprojektowanie procesu odzyskiwania aparatu bazy danych SQL Server do:

- Zwiększ czas/natychmiast, unikając konieczności skanowania dziennika z/do początku najstarszej aktywnej transakcji. Za pomocą reguły ADR dziennik transakcji jest przetwarzany tylko z ostatniego pomyślnego punktu kontrolnego (lub najstarszego nieprawidłowego numeru sekwencyjnego dziennika strony (LSN)). W związku z tym czas odzyskiwania nie ma wpływu na długotrwałe transakcje.
- Zminimalizuj wymaganą ilość miejsca w dzienniku transakcji, ponieważ nie ma już potrzeby przetwarzania dziennika dla całej transakcji. W związku z tym dziennik transakcji można skrócić w sposób agresywny jako punkty kontrolne i kopie zapasowe.

Na wysokim poziomie funkcja ADR umożliwia szybkie odzyskiwanie bazy danych przez przechowywanie wersji wszystkich modyfikacji fizycznych baz danych i wykonywanie tylko operacji logicznych, które są ograniczone i mogą być cofnięte niemal natychmiastowo. Wszystkie transakcje, które były aktywne jako czas awarii, są oznaczane jako przerwane i w związku z tym wszystkie wersje wygenerowane przez te transakcje mogą być ignorowane przez współbieżne zapytania użytkownika.

Proces odzyskiwania ADR ma te same trzy fazy jak bieżący proces odzyskiwania. Sposób działania tych faz z użyciem reguły ADR przedstawiono na poniższym diagramie i wyjaśniono bardziej szczegółowo po diagramie.

![Proces odzyskiwania ADR](./media/accelerated-database-recovery/adr-recovery-process.png)

- **Faza analizy**

  Proces pozostaje taki sam jak przed dodaniem sLog i skopiowaniem rekordów dziennika dla operacji nienależących do wersji.
  
- **Wykonaj ponownie** fazę

  Podzielone na dwie fazy (P)
  - Faza 1

      Wykonaj ponownie z sLog (najstarszej niezatwierdzonej transakcji do ostatniego punktu kontrolnego). Wykonaj ponownie szybką operację, ponieważ wymaga tylko przetworzenia kilku rekordów z sLog.

  - Faza 2

     Ponowne wykonywanie z dziennika transakcji zaczyna się od ostatniego punktu kontrolnego (zamiast najstarszej niezatwierdzonej transakcji)

- **Faza cofania**

   Faza cofania z ADR kończy niemal natychmiastowo za pomocą sLog do cofania operacji niezwiązanych z wersjami i trwałego magazynu wersji (PVS) z przywróceniem logicznym do wykonywania operacji cofania opartej na poziomie wiersza.

## <a name="adr-recovery-components"></a>Składniki odzyskiwania ADR

Cztery kluczowe składniki reguły ADR są następujące:

- **Magazyn trwałych wersji (PVS)**

  Utrwalony magazyn wersji jest nowym mechanizmem aparatu bazy danych SQL Server, aby utrwalić wersje wierszy wygenerowane w bazie danych zamiast tradycyjnego `tempdb` magazynu wersji. PVS umożliwia izolację zasobów, a także zwiększa dostępność czytelnych serwerów pomocniczych.

- **Przywrócenie logiczne**

  Przywrócenie logiczne to proces asynchroniczny odpowiedzialny za wykonywanie operacji cofania opartego na poziomie wiersza, która umożliwia wycofywanie transakcji i cofanie dla wszystkich operacji związanych z wersjami. Przywrócenie logiczne jest realizowane przez:

  - Śledź wszystkie przerwane transakcje i oznaczaj je jako niewidoczne dla innych transakcji. 
  - Wykonywanie wycofywania przy użyciu PVS dla wszystkich transakcji użytkownika, a nie na fizycznym skanowaniu dziennika transakcji i cofanie zmian po jednym naraz.
  - Zwalnianie wszystkich blokad natychmiast po przerwaniu transakcji. Ponieważ przerwanie obejmuje samo oznaczenie zmian w pamięci, proces jest bardzo wydajny i dlatego blokady nie muszą być przechowywane przez długi czas.

- **sLog**

  sLog to pomocniczy strumień dziennika w pamięci, który przechowuje rekordy dziennika dla operacji niezwiązanych z wersjami (takich jak unieważnienie pamięci podręcznej metadanych, blokady pozyskiwania itd.). SLog:

  - Niski wolumin i w pamięci
  - Utrwalone na dysku przez jego serializację podczas procesu Checkpoint
  - Okresowo obcinaj jako zatwierdzenie transakcji
  - Przyspiesza ponowne wykonywanie i cofanie przez przetwarzanie tylko operacji niezwiązanych z wersjami  
  - Umożliwia agresywne obcinanie dziennika transakcji przez zachowanie tylko wymaganych rekordów dziennika

- **Kontur**

  Oczyszczarka to proces asynchroniczny, który okresowo wznawia i czyści niewymagane wersje stron.

## <a name="accelerated-database-recovery-patterns"></a>Wzorce odzyskiwania przyspieszonej bazy danych

Następujące typy obciążeń korzystają z większości wartości ADR:

- Obciążenia z długotrwałymi transakcjami.
- Obciążenia, które wystąpiły przypadki, w których aktywne transakcje powodują znaczne zwiększenie rozmiaru dziennika transakcji.  
- Obciążenia, które miały długotrwałe okresy niedostępności bazy danych z powodu długotrwałego odzyskiwania (na przykład nieoczekiwanego ponownego uruchomienia usługi lub ręcznego wycofywania transakcji).
