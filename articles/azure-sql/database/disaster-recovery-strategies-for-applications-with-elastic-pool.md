---
title: Projektowanie rozwiązań odzyskiwania po awarii
description: Dowiedz się, jak zaprojektować rozwiązanie w chmurze na potrzeby odzyskiwania po awarii, wybierając odpowiedni wzorzec trybu failover.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb-1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 0463d11466859c0f30901a0afd960bdc7b2599a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91357791"
---
# <a name="disaster-recovery-strategies-for-applications-using-azure-sql-database-elastic-pools"></a>Strategie odzyskiwania po awarii dla aplikacji korzystających z Azure SQL Database pul elastycznych
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database oferuje kilka funkcji zapewniających ciągłość działania aplikacji w przypadku wystąpienia katastrofalnych zdarzeń. [Pule elastyczne](elastic-pool-overview.md) i pojedyncze bazy danych obsługują tego samego rodzaju możliwości odzyskiwania po awarii (Dr). W tym artykule opisano kilka strategii odzyskiwania po awarii dla pul elastycznych, które wykorzystują te Azure SQL Database funkcje ciągłości działania.

W tym artykule jest stosowany następujący wzorzec kanonicznej aplikacji SaaS niezależnego dostawcy oprogramowania:

Nowoczesne aplikacje sieci Web oparte na chmurze udostępniają jedną bazę danych dla każdego użytkownika końcowego. Dostawca ISV ma wielu klientów i w związku z tym używa wielu baz danych, nazywanych bazami danych dzierżawcy. Ponieważ bazy danych dzierżaw zwykle mają nieprzewidywalne wzorce aktywności, dostawca ISV używa puli elastycznej, aby zapewnić, że baza danych jest bardzo przewidywalna w ciągu dłuższych okresów czasu. Elastyczna Pula upraszcza również zarządzanie wydajnością, gdy działanie użytkownika zostanie pożądane. Oprócz baz danych dzierżawców aplikacja używa także kilku baz danych do zarządzania profilami użytkowników, zabezpieczeniami, zbierania wzorców użycia itp. Dostępność indywidualnych dzierżawców nie ma wpływu na dostępność aplikacji jako całości. Jednak dostępność i wydajność baz danych zarządzania ma kluczowe znaczenie dla funkcji aplikacji, a jeśli bazy danych zarządzania są w trybie offline, cała aplikacja jest w trybie offline.

W tym artykule omówiono strategie odzyskiwania po awarii obejmujące różne scenariusze od rozróżniania kosztów aplikacji uruchamiania do tych, które mają rygorystyczne wymagania dotyczące dostępności.

> [!NOTE]
> Jeśli używasz baz danych Premium lub Krytyczne dla działania firmy i pul elastycznych, możesz je odporne na awarie regionalne, konwertując je na konfigurację nadmiarowego wdrożenia strefy. Zobacz [bazy danych strefowo nadmiarowe](high-availability-sla.md).

## <a name="scenario-1-cost-sensitive-startup"></a>Scenariusz 1. Uruchamianie z uwzględnieniem kosztów

Jestem biznesem uruchamianym i są niezwykle opłacalne.  Chcę uprościć wdrażanie aplikacji i zarządzanie nią i mogę mieć ograniczoną umowę SLA dla poszczególnych klientów. Ale chcę mieć pewność, że aplikacja jako całość nigdy nie jest w trybie offline.

Aby spełnić wymagania prostoty, wdróż wszystkie bazy danych dzierżaw w jednej elastycznej puli w wybranym regionie platformy Azure i Wdróż bazy danych zarządzania jako pojedyncze bazy danych z replikacją geograficzną. W przypadku odzyskiwania po awarii dzierżawców należy użyć funkcji przywracania geograficznego, która nie oferuje żadnych dodatkowych kosztów. Aby zapewnić dostępność baz danych zarządzania, należy przeprowadzić replikację geograficzną w innym regionie przy użyciu grupy autotrybu failover (krok 1). Ciągły koszt konfiguracji odzyskiwania po awarii w tym scenariuszu jest równy łącznym kosztom pomocniczych baz danych. Ta konfiguracja jest zilustrowana na następnym diagramie.

![Rysunek 1.](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-1.png)

Jeśli w regionie podstawowym wystąpi awaria, kroki odzyskiwania umożliwiające przełączenie aplikacji do trybu online są zilustrowane na następnym diagramie.

* Grupa trybu failover inicjuje automatyczną pracę awaryjną bazy danych zarządzania w regionie odzyskiwania po awarii. Aplikacja jest automatycznie ponownie łączona z nowym serwerem podstawowym, a wszystkie nowe konta i bazy danych dzierżaw są tworzone w regionie odzyskiwania po awarii. Istniejący klienci zobaczą, że ich dane są tymczasowo niedostępne.
* Utwórz pulę elastyczną z taką samą konfiguracją jak oryginalna Pula (2).
* Użyj funkcji przywracania geograficznego, aby utworzyć kopie baz danych dzierżaw (3). Można rozważyć wyzwolenie poszczególnych przywracania przez połączenia użytkowników końcowych lub użycie innego schematu priorytetu specyficznego dla aplikacji.

W tym momencie aplikacja przechodzi do trybu online w regionie odzyskiwania po awarii, ale niektórzy klienci napotykają opóźnienia podczas uzyskiwania dostępu do danych.

![Rysunek 2](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-2.png)

Jeśli awaria była tymczasowa, możliwe jest odzyskanie regionu podstawowego przez platformę Azure przed ukończeniem przywracania bazy danych w regionie odzyskiwania po awarii. W takim przypadku należy zorganizować przeniesienie aplikacji z powrotem do regionu podstawowego. Proces wykonuje kroki opisane na następnym diagramie.

* Anuluj wszystkie zaległe żądania przywracania geograficznego.
* Przełączenie baz danych zarządzania w tryb failover do regionu podstawowego (5). Po odzyskaniu w regionie stare Primaries mają automatycznie pełnić rolę pomocniczą. Teraz ponownie przełączają role.
* Zmień parametry połączenia aplikacji tak, aby wskazywały region podstawowy. Teraz wszystkie nowe konta i bazy danych dzierżawy są tworzone w regionie podstawowym. Niektórzy istniejący klienci zobaczą, że ich dane są tymczasowo niedostępne.
* Ustaw wszystkie bazy danych w puli DR na tylko do odczytu, aby upewnić się, że nie można ich modyfikować w regionie odzyskiwania po awarii (6).
* Dla każdej bazy danych w puli odzyskiwania po awarii, która zmieniła się od momentu odzyskiwania, Zmień nazwę lub usuń odpowiednie bazy danych w puli podstawowej (7).
* Skopiuj zaktualizowane bazy danych z puli odzyskiwania po awarii do puli podstawowej (8).
* Usuwanie puli odzyskiwania po awarii (9)

W tym momencie aplikacja jest w trybie online w regionie podstawowym ze wszystkimi bazami danych dzierżawy dostępnymi w puli podstawowej.

![Rysunek 3](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-3.png)

### <a name="benefit"></a>Korzyść

Najważniejsze korzyści wynikające z tej strategii to niski ciągły koszt nadmiarowości warstwy danych. Azure SQL Database automatycznie tworzy kopie zapasowe baz danych bez ponownego zapisywania aplikacji bez dodatkowych kosztów. Koszt jest naliczany tylko w przypadku przywrócenia elastycznych baz danych. 

### <a name="trade-off"></a>Handel

Dzięki temu cały czas odzyskiwanie wszystkich baz danych dzierżawy trwa znacznie. Czas zależy od łącznej liczby operacji przywracania zainicjowanej w regionie DR oraz całkowitego rozmiaru baz danych dzierżawy. Nawet jeśli określisz priorytety niektórych dzierżawców, będziesz mieć możliwość konkurowania ze wszystkimi innymi przywracaniami, które są inicjowane w tym samym regionie, w którym usługa rozstrzyga i ogranicza ograniczenia, aby zminimalizować ogólny wpływ na istniejące bazy danych klientów. Ponadto nie można uruchomić odzyskiwania baz danych dzierżawy, dopóki nie zostanie utworzona nowa pula elastyczna w regionie odzyskiwania po awarii.

## <a name="scenario-2-mature-application-with-tiered-service"></a>Scenariusz 2. Aplikacja dla dorosłych z usługą warstwową

Jestem dojrzałą aplikacją SaaS z ofertami usług warstwowych i różnymi umowy slami dla klientów korzystających z wersji próbnej oraz w przypadku płacenia klientów. W przypadku klientów korzystających z wersji próbnych należy zmniejszyć koszty tak, jak to możliwe. Klienci korzystający z wersji próbnej mogą podejmować przestoje, ale chcą ograniczyć jej prawdopodobieństwo. W przypadku płatnych odbiorców każdy przestój jest ryzykiem związanym z lotem. Dlatego chcę mieć pewność, że płacący klienci zawsze mogą uzyskać dostęp do swoich danych.

Aby obsłużyć ten scenariusz, należy oddzielić dzierżawy w wersji próbnej od płatnych dzierżawców, umieszczając je w osobnych pulach elastycznych. Klienci korzystający z wersji próbnej mają mniejszą liczbę jednostek eDTU lub rdzeni wirtualnych na dzierżawcę oraz niższą umowę SLA o dłuższym czasie odzyskiwania. Płacący klienci są w puli o wyższej liczby jednostek eDTU lub rdzeni wirtualnych na dzierżawcę i wyższą umowę SLA. W celu zagwarantowania najmniejszego czasu odzyskiwania bazy danych dzierżawy klientów z wypłaceniem są replikowane geograficznie. Ta konfiguracja jest zilustrowana na następnym diagramie.

![Na diagramie przedstawiono region podstawowy i region D R, który wykorzystuje replikację geograficzną między bazą danych zarządzania a płatną pulą podstawową i pomocniczą klientów, bez replikacji dla puli klientów wersji próbnej.](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-4.png)

Jak w pierwszym scenariuszu bazy danych zarządzania są dość aktywne, aby można było korzystać z jednej geograficznie zreplikowanej bazy danych (1). Zapewnia to przewidywalną wydajność nowych subskrypcji klientów, aktualizacji profilów i innych operacji zarządzania. Region, w którym znajduje się Primaries baz danych zarządzania, jest regionem podstawowym, a region, w którym znajdują się pomocnicze bazy danych zarządzania, jest regionem odzyskiwania po awarii.

Bazy danych dzierżawy klientów dokonujących wpłaty mają aktywne bazy danych w puli "płatne", która została zainicjowana w regionie podstawowym. Zainicjuj obsługę puli pomocniczej o tej samej nazwie w regionie odzyskiwania po awarii. Każda dzierżawa jest replikowana geograficznie do puli pomocniczej (2). Umożliwia to szybkie odzyskiwanie wszystkich baz danych dzierżaw przy użyciu trybu failover.

Jeśli w regionie podstawowym wystąpi awaria, kroki odzyskiwania umożliwiające przełączenie aplikacji do trybu online są zilustrowane na następnym diagramie:

![Diagram przedstawia awarię regionu podstawowego, z przełączeniem w tryb failover do bazy danych zarządzania, płatną dodatkową pulę klientów oraz tworzenie i przywracanie dla klientów z wersjami próbnymi.](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-5.png)

* Natychmiastowe przełączenie baz danych zarządzania do regionu odzyskiwania po awarii (3).
* Zmień parametry połączenia aplikacji tak, aby wskazywały region DR. Teraz wszystkie nowe konta i bazy danych dzierżawy są tworzone w regionie odzyskiwania po awarii. Obecni klienci korzystający z wersji próbnej zobaczą, że dane są tymczasowo niedostępne.
* Przełączenie w tryb failover baz danych płatnej dzierżawy do puli w regionie odzyskiwania po awarii w celu natychmiastowego przywrócenia ich dostępności (4). Ponieważ przejście w tryb failover to szybka zmiana poziomu metadanych, należy rozważyć optymalizację, w której poszczególne przełączenia w tryb failover są wyzwalane na żądanie przez połączenia użytkowników końcowych.
* Jeśli rozmiar eDTU puli dodatkowej lub wartość rdzeń wirtualny była niższa niż podstawowa, ponieważ pomocnicze bazy danych wymagały przetworzenia dzienników zmian tylko wtedy, gdy były one pomocnicze, natychmiast Zwiększ pojemność puli teraz, aby uwzględnić pełne obciążenie wszystkich dzierżawców (5).
* Utwórz nową pulę elastyczną o tej samej nazwie i tej samej konfiguracji w regionie DR dla baz danych wersji próbnych klientów (6).
* Po utworzeniu puli wersji próbnych klientów Użyj funkcji przywracania geograficznego, aby przywrócić pojedyncze bazy danych dzierżawy wersji próbnej do nowej puli (7). Należy rozważyć wyzwolenie poszczególnych przywracania przez połączenia użytkowników końcowych lub użycie innego schematu priorytetu specyficznego dla aplikacji.

W tym momencie aplikacja przechodzi do trybu online w regionie odzyskiwania po awarii. Wszyscy klienci z płatnościami mają dostęp do swoich danych, podczas gdy próbuje oni uzyskać opóźnienia podczas uzyskiwania dostępu do danych.

Gdy podstawowy region jest odzyskiwany przez platformę Azure po przywróceniu aplikacji w regionie odzyskiwania *po* awarii, można kontynuować uruchamianie aplikacji w tym regionie lub można zdecydować, aby powrócić do regionu podstawowego. Jeśli region podstawowy zostanie odzyskany *przed* ukończeniem procesu przełączania do trybu failover, należy natychmiast rozważyć powrót po awarii. Powrót po awarii powoduje wykonanie kroków przedstawionych na następnym diagramie:

![Na diagramie przedstawiono kroki powrotu po awarii, które należy wykonać po przywróceniu regionu podstawowego.](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-6.png)

* Anuluj wszystkie zaległe żądania przywracania geograficznego.
* Przełączenie w tryb failover baz danych zarządzania (8). Po odzyskiwaniu w regionie stary podstawowy automatycznie staje się pomocniczą. Teraz zostanie on ponownie podstawowy.  
* Przełączenie w tryb failover płatnych baz danych dzierżaw (9). Podobnie po odzyskaniu regionu stare Primaries automatycznie stają się pomocniczymi. Teraz stają się one Primaries ponownie.
* Ustaw przywrócone bazy danych w wersji próbnej, które zostały zmienione w regionie odzyskiwania po awarii (10).
* Dla każdej bazy danych w puli odzyskiwania po awarii dla klientów w wersji próbnej, która zmieniła się od momentu odzyskania, należy zmienić nazwę odpowiedniej bazy danych lub usunąć ją w podstawowej puli klientów wersji próbnej
* Skopiuj zaktualizowane bazy danych z puli odzyskiwania po awarii do puli podstawowej (12).
* Usuń pulę odzyskiwania po awarii (13).

> [!NOTE]
> Operacja przejścia w tryb failover jest asynchroniczna. Aby zminimalizować czas odzyskiwania, należy wykonać polecenie trybu failover baz danych dzierżawy w partiach co najmniej 20 baz danych.

### <a name="benefit"></a>Korzyść

Najważniejsze korzyści wynikające z tej strategii zapewniają najwyższą umowę SLA dla klientów korzystających z płatności. Gwarantuje również, że nowe wersje próbne zostaną odblokowane natychmiast po utworzeniu puli odzyskiwania po awarii. 

### <a name="trade-off"></a>Handel

W tym przypadku taka konfiguracja zwiększa łączny koszt baz danych dzierżawy przez koszt dodatkowej puli DR dla płatnych klientów. Ponadto, jeśli Pula pomocnicza ma inny rozmiar, płatne klienci mogą obniżyć wydajność po przejściu do trybu failover do momentu zakończenia uaktualniania puli w regionie odzyskiwania po awarii.

## <a name="scenario-3-geographically-distributed-application-with-tiered-service"></a>Scenariusz 3. Aplikacja rozproszona geograficznie z usługą warstwową

Mam nieSaaSą aplikację z ofertami usług warstwowych. Chcę zaoferować bardzo agresywną umowę SLA klientom z płatnościami i zminimalizować ryzyko wystąpienia awarii, ponieważ nawet krótkie przerwy mogą spowodować niezadowolenie klientów. Ważne jest, aby płacący klienci zawsze mogli uzyskiwać dostęp do swoich danych. Wersje próbne są bezpłatne i Umowa SLA nie jest oferowana w okresie próbnym.

Aby obsłużyć ten scenariusz, należy użyć trzech oddzielnych pul elastycznych. Udostępnij dwie pule o równym rozmiarze z wysoką jednostek eDTU lub rdzeni wirtualnych na bazę danych w dwóch różnych regionach, aby zawierały płatne bazy danych dzierżaw klientów. Trzecia Pula zawierająca dzierżawy w wersji próbnej może mieć niższą jednostek eDTU lub rdzeni wirtualnych na bazę danych i być obsługiwana w jednym z dwóch regionów.

W celu zagwarantowania najmniejszego czasu odzyskiwania w trakcie awarii, wypłatne bazy danych dzierżawy klientów są replikowane geograficznie z 50% podstawowych baz danych w każdym z tych dwóch regionów. Podobnie każdy region ma 50% pomocniczych baz danych. W ten sposób, jeśli region jest w trybie offline, wpływają tylko na 50% baz danych płatnych klientów i będzie można przechodzić w tryb failover. Pozostałe bazy danych pozostają bez zmian. Ta konfiguracja jest zilustrowana na poniższym diagramie:

![Diagram przedstawia region podstawowy nazywany regionem a i regionem pomocniczym o nazwie region B, który wykorzystuje replikację geograficzną między bazą danych zarządzania i płatną pulą podstawową dla klientów i pulę pomocniczą bez replikacji dla puli klientów w wersji próbnej.](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-7.png)

Podobnie jak w poprzednich scenariuszach, bazy danych zarządzania są dość aktywne, dlatego należy je skonfigurować jako pojedyncze bazy danych z replikacją geograficzną (1). Zapewnia to przewidywalną wydajność nowych subskrypcji klientów, aktualizacje profilów i inne operacje zarządzania. Region A jest regionem podstawowym baz danych zarządzania, a region B jest używany do odzyskiwania baz danych zarządzania.

Bazy danych dzierżawy klientów płacących również są replikowane geograficznie, ale z Primaries i pomocniczymi rozbiciem między region A i region B (2). W ten sposób podstawowe bazy danych dzierżawy, na które wpływa awaria, mogą być przełączane w tryb failover do innego regionu i stają się dostępne. Nie ma to wpływu na drugą połowę baz danych dzierżawy.

Na następnym diagramie przedstawiono kroki odzyskiwania, które należy wykonać w przypadku wystąpienia awarii w regionie A.

![Diagram przedstawia awarię regionu podstawowego, z przełączeniem w tryb failover do bazy danych zarządzania, płatną pulę pomocniczą klienta oraz tworzenie i przywracanie dla klientów z wersjami próbnymi w regionie B.](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-8.png)

* Natychmiastowe przełączanie baz danych zarządzania do regionu B (3).
* Zmień parametry połączenia aplikacji tak, aby wskazywały bazy danych zarządzania w regionie B. Zmodyfikuj bazy danych zarządzania, aby upewnić się, że nowe konta i bazy danych dzierżawy są tworzone w regionie B i tam znajdują się również istniejące bazy danych dzierżaw. Obecni klienci korzystający z wersji próbnej zobaczą, że dane są tymczasowo niedostępne.
* Przełączenie w tryb failover baz danych płatnej dzierżawy do puli 2 w regionie B w celu natychmiastowego przywrócenia ich dostępności (4). Ponieważ przejście w tryb failover to szybka zmiana poziomu metadanych, można rozważyć optymalizację, w której poszczególne przełączenia w tryb failover są wyzwalane na żądanie przez połączenia użytkowników końcowych.
* Ponieważ pula 2 zawiera tylko podstawowe bazy danych, Łączne obciążenie w puli rośnie i może natychmiast zwiększyć swój rozmiar eDTU (5) lub liczbę rdzeni wirtualnych.
* Utwórz nową pulę elastyczną o tej samej nazwie i tej samej konfiguracji w regionie B dla baz danych wersji próbnych klientów (6).
* Po utworzeniu puli Użyj funkcji przywracania geograficznego, aby przywrócić bazę danych dzierżawy pojedynczej wersji próbnej do puli (7). Można rozważyć wyzwolenie poszczególnych przywracania przez połączenia użytkowników końcowych lub użycie innego schematu priorytetu specyficznego dla aplikacji.

> [!NOTE]
> Operacja przejścia w tryb failover jest asynchroniczna. Aby zminimalizować czas odzyskiwania, należy wykonać polecenie trybu failover baz danych dzierżawy w partiach co najmniej 20 baz danych.

W tym momencie aplikacja przechodzi do trybu online w regionie B. Wszyscy klienci z płatnościami mają dostęp do swoich danych, podczas gdy próbuje oni uzyskać opóźnienia podczas uzyskiwania dostępu do danych.

W przypadku odzyskania regionu A należy zdecydować, czy chcesz użyć regionu B dla klientów wersji próbnej lub powrotu po awarii do korzystania z puli próbnej klientów w regionie A. Jednym z kryteriów może być% baz danych dzierżawy w wersji próbnej zmodyfikowanych od momentu odzyskania. Niezależnie od tej decyzji należy ponownie zrównoważyć płatne dzierżawy między dwiema pulami. na następnym diagramie przedstawiono proces ponownego powrotu baz danych dzierżawy do regionu A.  

![Na diagramie przedstawiono kroki powrotu po awarii do wdrożenia po przywróceniu regionu A.](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-9.png)

* Anuluj wszystkie zaległe żądania przywracania geograficznego w puli odzyskiwania po awarii.
* Przełączenie w tryb failover bazy danych zarządzania (8). Po odzyskaniu w regionie stary podstawowy automatycznie stał się pomocniczy. Teraz zostanie on ponownie podstawowy.  
* Wybierz płatne bazy danych dzierżawy powrotu po awarii do puli 1 i zainicjuj pracę w trybie failover dla swoich serwerów pomocniczych (9). Po odzyskaniu regionu wszystkie bazy danych w puli 1 automatycznie stały się pomocniczymi. Teraz 50% z nich staje się ponownie Primaries.
* Zmniejsz rozmiar puli 2 do oryginalnej liczby jednostek eDTU (10) lub rdzeni wirtualnych.
* Ustaw wszystkie przywrócone bazy danych w wersji próbnej w regionie B w trybie tylko do odczytu (11).
* Dla każdej bazy danych w puli odzyskiwania po awarii, która zmieniła się od momentu odzyskania, Zmień nazwę odpowiedniej bazy danych na podstawową (12) lub usuń ją z niej.
* Skopiuj zaktualizowane bazy danych z puli odzyskiwania po awarii do puli podstawowej (13).
* Usuń pulę odzyskiwania po awarii (14).

### <a name="benefit"></a>Korzyść

Najważniejsze korzyści wynikające z tej strategii są następujące:

* Obsługuje ona najbardziej agresywne umowy SLA dla klientów korzystających z tej usługi, ponieważ gwarantuje to, że awaria nie może wpływać na ponad 50% baz danych dzierżaw.
* Gwarantuje to, że nowe wersje próbne zostaną odblokowane, gdy tylko podczas odzyskiwania zostanie utworzona końcowa Pula programu DR.
* Zapewnia to wydajniejsze korzystanie z pojemności puli jako 50% pomocniczych baz danych w puli 1, a pula 2 gwarantuje mniej aktywności niż w przypadku podstawowych baz danych.

### <a name="trade-offs"></a>Kompromisy

Główne wady są następujące:

* Operacje CRUD z bazami danych zarządzania mają małe opóźnienia dla użytkowników końcowych połączonych z regionem A, A nie dla użytkowników końcowych podłączonych do regionu B, gdy są wykonywane względem podstawowej bazy danych zarządzania.
* Wymaga bardziej złożonego projektu bazy danych zarządzania. Na przykład każdy rekord dzierżawy ma tag lokalizacji, który należy zmienić podczas pracy w trybie failover i powrotu po awarii.  
* Klienci korzystający z płacenia mogą mieć niższą wydajność niż zwykle do momentu zakończenia uaktualniania puli w regionie B.

## <a name="summary"></a>Podsumowanie

Ten artykuł koncentruje się na strategii odzyskiwania po awarii dla warstwy bazy danych używanej przez wielodostępną aplikację SaaS niezależnego dostawcy oprogramowania. Wybrana strategia zależy od potrzeb aplikacji, takich jak model biznesowy, umowa SLA, która ma być oferowana klientom, ograniczenie budżetu itp. Każda opisana strategia przedstawia korzyści i wymianę, dzięki czemu można podejmować świadome decyzje. Ponadto konkretna aplikacja będzie zawierać inne składniki platformy Azure. Należy zapoznać się ze wskazówkami dotyczącymi ciągłości działania i zorganizować odzyskiwanie warstwy bazy danych. Aby dowiedzieć się więcej o zarządzaniu odzyskiwaniem aplikacji bazy danych na platformie Azure, zapoznaj się z artykułem [projektowanie rozwiązań w chmurze na potrzeby odzyskiwania po awarii](designing-cloud-solutions-for-disaster-recovery.md).  

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o Azure SQL Database zautomatyzowanych kopii zapasowych, zobacz [Azure SQL Database zautomatyzowane kopie zapasowe](automated-backups-overview.md).
* Aby zapoznać się z omówieniem i scenariuszami ciągłości działania, zobacz temat [ciągłość działania — Omówienie](business-continuity-high-availability-disaster-recover-hadr-overview.md).
* Aby dowiedzieć się więcej o korzystaniu z automatycznych kopii zapasowych na potrzeby odzyskiwania, zobacz [przywracanie bazy danych z kopii zapasowych inicjowanych przez usługę](recovery-using-backups.md).
* Aby dowiedzieć się więcej na temat szybszych opcji odzyskiwania, zobacz [aktywnej replikacji geograficznej](active-geo-replication-overview.md) i [grup Autotryb failover](auto-failover-group-overview.md).
* Aby dowiedzieć się więcej o korzystaniu z automatycznych kopii zapasowych do archiwizowania, zobacz [kopia bazy danych](database-copy.md).
