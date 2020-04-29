---
title: Przyspieszone odzyskiwanie bazy danych
description: Azure SQL Database ma nową funkcję, która zapewnia szybkie i spójne odzyskiwanie bazy danych, natychmiastowe wycofywanie transakcji oraz agresywne obcinanie dzienników dla pojedynczych baz danych i baz danych w puli w Azure SQL Database i bazach danych w Azure SQL Data Warehouse.
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 03/24/2020
ms.openlocfilehash: 57ca594dd067d15009de5e3abf7276fae48720d2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80238661"
---
# <a name="accelerated-database-recovery"></a>Szybsze odzyskiwanie bazy danych

**Szybsze odzyskiwanie bazy danych (ADR)** to funkcja aparatu bazy danych SQL, która znacznie zwiększa dostępność bazy danych, szczególnie w przypadku długotrwałych transakcji, przez przeprojektowanie procesu odzyskiwania aparatu bazy danych SQL. ADR jest obecnie dostępna dla Azure SQL Database pojedynczej, elastycznej puli i wystąpienia zarządzanego oraz baz danych w Azure SQL Data Warehouse (obecnie w wersji zapoznawczej). Podstawowe korzyści wynikające z ADR są następujące:

- **Szybkie i spójne odzyskiwanie bazy danych**

  W przypadku opcji ADR długotrwałe transakcje nie wpływają na ogólny czas odzyskiwania, umożliwiając szybkie i spójne odzyskiwanie bazy danych niezależnie od liczby aktywnych transakcji w systemie lub ich rozmiarów.

- **Natychmiastowe wycofywanie transakcji**

  Za pomocą reguły ADR wycofywanie transakcji jest chwilowo, niezależnie od czasu aktywności transakcji lub liczby wykonanych aktualizacji.

- **Agresywne obcinanie dziennika**

  W przypadku korzystania z reguły ADR dziennik transakcji jest agresywnie obcinany, nawet w obecności aktywnych długotrwałych transakcji, które uniemożliwiają przeprowadzenie kontroli.

## <a name="the-current-database-recovery-process"></a>Bieżący proces odzyskiwania bazy danych

Odzyskiwanie bazy danych w SQL Server jest zgodne z modelem odzyskiwania [Aries](https://people.eecs.berkeley.edu/~brewer/cs262/Aries.pdf) i składa się z trzech faz, które przedstawiono na poniższym diagramie i wyjaśniono bardziej szczegółowo po diagramie.

![bieżący proces odzyskiwania](./media/sql-database-accelerated-database-recovery/current-recovery-process.png)

- **Faza analizy**

  Przeprowadź skanowanie dziennika transakcji od początku ostatniego pomyślnego punktu kontrolnego (lub najstarszej wersji LSN strony) aż do zakończenia, aby określić stan każdej transakcji w chwili SQL Server zatrzymania.

- **Wykonaj ponownie fazę**

  Przeprowadź skanowanie dziennika transakcji od najstarszej niezatwierdzonej transakcji do końca, aby przenieść bazę danych do stanu, który był w chwili awarii, wykonując ponownie wszystkie operacje zatwierdzone.

- **Faza cofania**

  W przypadku każdej transakcji, która była aktywna jako czas awarii, przejdzie do tyłu dziennika, cofając operacje wykonywane przez tę transakcję.

W oparciu o ten projekt czas odzyskiwania aparatu bazy danych SQL po nieoczekiwanym ponownym uruchomieniu jest (w przybliżeniu) proporcjonalny do rozmiaru najdłuższej aktywnej transakcji w systemie w chwili awarii. Odzyskiwanie wymaga wycofania wszystkich nieukończonych transakcji. Czas wymagany jest proporcjonalny do pracy wykonanej przez transakcję i czasu, gdy był aktywny. W związku z tym proces odzyskiwania SQL Server może zająć dużo czasu w obecności długotrwałych transakcji (takich jak duże operacje wstawiania zbiorczego lub operacje kompilowania indeksów dla dużej tabeli).

Ponadto anulowanie i wycofywanie dużej transakcji na podstawie tego projektu może być również czasochłonne, ponieważ używa tej samej fazy odzyskiwania cofania, jak opisano powyżej.

Ponadto aparat bazy danych SQL nie może obciąć dziennika transakcji, gdy istnieją długotrwałe transakcje, ponieważ odpowiednie rekordy dzienników są zbędne dla procesów odzyskiwania i wycofywania. W wyniku tego projektu aparatu bazy danych SQL, niektórzy klienci, którzy napotykają na problem, że rozmiar dziennika transakcji rośnie bardzo duże i zużywają ogromną ilość miejsca na dysku.

## <a name="the-accelerated-database-recovery-process"></a>Proces odzyskiwania przyspieszonej bazy danych

ADR eliminuje powyższe problemy poprzez całkowite przeprojektowanie procesu odzyskiwania aparatu bazy danych SQL do:

- Zwiększ czas/natychmiast, unikając konieczności skanowania dziennika z/do początku najstarszej aktywnej transakcji. Za pomocą reguły ADR dziennik transakcji jest przetwarzany tylko z ostatniego pomyślnego punktu kontrolnego (lub najstarszego nieprawidłowego numeru sekwencyjnego dziennika strony (LSN)). W związku z tym czas odzyskiwania nie ma wpływu na długotrwałe transakcje.
- Zminimalizuj wymaganą ilość miejsca w dzienniku transakcji, ponieważ nie ma już potrzeby przetwarzania dziennika dla całej transakcji. W związku z tym dziennik transakcji można skrócić w sposób agresywny jako punkty kontrolne i kopie zapasowe.

Na wysokim poziomie funkcja ADR umożliwia szybkie odzyskiwanie bazy danych przez przechowywanie wersji wszystkich modyfikacji fizycznych baz danych i wykonywanie tylko operacji logicznych, które są ograniczone i mogą być cofnięte niemal natychmiastowo. Wszystkie transakcje, które były aktywne jako czas awarii, są oznaczane jako przerwane i w związku z tym wszystkie wersje wygenerowane przez te transakcje mogą być ignorowane przez współbieżne zapytania użytkownika.

Proces odzyskiwania ADR ma te same trzy fazy jak bieżący proces odzyskiwania. Sposób działania tych faz z użyciem reguły ADR przedstawiono na poniższym diagramie i wyjaśniono bardziej szczegółowo po diagramie.

![Proces odzyskiwania ADR](./media/sql-database-accelerated-database-recovery/adr-recovery-process.png)

- **Faza analizy**

  Proces pozostaje taki sam jak przed dodaniem sLog i skopiowaniem rekordów dziennika dla operacji nienależących do wersji.
  
- **Wykonaj ponownie** fazę

  Podzielone na dwie fazy (P)
  - Faza 1

      Wykonaj ponownie z sLog (najstarszej niezatwierdzonej transakcji do ostatniego punktu kontrolnego). Wykonaj ponownie szybką operację, ponieważ wymaga tylko przetworzenia kilku rekordów z sLog.
      
  - Faza 2

     Ponowne wykonywanie z dziennika transakcji zaczyna się od ostatniego punktu kontrolnego (zamiast najstarszej niezatwierdzonej transakcji)
     
- **Faza cofania**

   Faza cofania z ADR kończy niemal natychmiastowo za pomocą sLog do cofania operacji niezwiązanych z wersjami i trwałego magazynu wersji (PVS) z przywróceniem logicznym do wykonywania operacji cofania opartej na poziomie wiersza.

## <a name="adr-recovery-components"></a>Składniki odzyskiwania ADR

Cztery kluczowe składniki reguły ADR są następujące:

- **Magazyn trwałych wersji (PVS)**

  Utrwalony magazyn wersji to nowy mechanizm aparatu usługi SQL Database służący do utrwalania wersji wiersza generowanych w bazie danych zamiast tradycyjnego `tempdb` magazynu wersji. PVS umożliwia izolację zasobów, a także zwiększa dostępność czytelnych serwerów pomocniczych.

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
- Obciążenia, na których wystąpiły długotrwałe okresy niedostępności bazy danych z powodu SQL Server długotrwałego odzyskiwania (na przykład nieoczekiwane SQL Server ponownego uruchomienia lub ręcznego wycofywania transakcji).

