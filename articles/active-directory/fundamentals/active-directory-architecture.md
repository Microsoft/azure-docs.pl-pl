---
title: Przegląd architektury — Azure Active Directory | Microsoft Docs
description: Dowiedz się, co to jest Azure Active Directory dzierżawa i jak zarządzać platformą Azure przy użyciu Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 476cf8013f5dc8b5d54efb573cf305d81fc690b1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95996716"
---
# <a name="what-is-the-azure-active-directory-architecture"></a>Co to jest architektura Azure Active Directory?

Usługa Azure Active Directory (Azure AD) umożliwia bezpieczne zarządzanie dostępem do usług i zasobów platformy Azure dla użytkowników. W ramach usługi Azure AD można skorzystać z pełnego zestawu możliwości zarządzania tożsamościami. Aby uzyskać więcej informacji na temat funkcji usługi Azure AD, zobacz [Co to jest usługa Azure Active Directory?](active-directory-whatis.md)

Za pomocą usługi Azure AD możesz tworzyć użytkowników i grupy oraz zarządzać nimi, a także zezwalać na dostęp do zasobów organizacji i odmawiać go przy użyciu uprawnień. Aby uzyskać informacje dotyczące zarządzania tożsamościami, zobacz [The fundamentals of Azure identity management](active-directory-whatis.md) (Podstawy zarządzania tożsamościami platformy Azure).

## <a name="azure-ad-architecture"></a>Architektura usługi Azure AD

Architektura rozproszona geograficznie usługi Azure AD obejmuje szerokie możliwości monitorowania, zautomatyzowanego ponownego routingu, pracy w trybie failover i odzyskiwania, które zapewniają klientom dostępność i wydajność w całej firmie.

W tym artykule omówione są następujące elementy architektury:

*   Projekt architektury usługi
*   Skalowalność
*   Ciągła dostępność
*   Centra danych

### <a name="service-architecture-design"></a>Projekt architektury usługi

Najbardziej typowym sposobem na skompilowanie dostępnego i możliwego do użycia systemu danych jest użycie niezależnych bloków konstrukcyjnych lub jednostek skalowania. W przypadku warstwy danych usługi Azure AD jednostki skalowania są nazywane *partycjami*.

Warstwa danych ma kilka usług frontonu, które zapewniają możliwość odczytu i zapisu. Na poniższym diagramie przedstawiono sposób, w jaki składniki partycji pojedynczego katalogu są dostarczane w obrębie rozproszonych geograficznie centrów danych.

  ![Diagram partycji z jednym katalogiem](./media/active-directory-architecture/active-directory-architecture.png)

Składniki architektury usługi Azure AD obejmują repliki podstawowe i pomocnicze.

#### <a name="primary-replica"></a>Replika podstawowa

*Replika podstawowa* odbiera wszystkie operacje *zapisu* dla partycji, do której należy. Wszystkie operacje zapisu są natychmiast replikowane do repliki pomocniczej znajdującej się w innym centrum danych przed zwróceniem do obiektu wywołującego informacji o powodzeniu, co pozwala na zapewnienie nadmiarowej geograficznie trwałości zapisów.

#### <a name="secondary-replicas"></a>Repliki pomocnicze

Wszystkie *odczyty* katalogów są obsługiwane z *replik pomocniczych*, które znajdują się w centrach danych, które fizycznie znajdują się na różnych lokalizacje geograficzne. Istnieje wiele replik pomocniczych, ponieważ dane są replikowane w sposób asynchroniczny. Odczyty katalogów, takie jak żądania uwierzytelniania, są serwisowane z centrów danych, które znajdują się blisko klientów. Repliki pomocnicze są odpowiedzialne za skalowalność odczytu.

### <a name="scalability"></a>Skalowalność

Skalowalność to możliwość rozszerzania działania usługi w związku z rosnącymi wymaganiami dotyczącymi wydajności. Skalowalność zapisu jest osiągana przez partycjonowanie danych. Skalowalność odczytu uzyskuje się przez replikację danych z jednej partycji do wielu replik pomocniczych rozproszonych na całym świecie.

Żądania z aplikacji katalogu są kierowane do centrum danych, które są fizycznie najbliższe. Zapisy są w sposób niewidoczny dla użytkownika przekierowywane do repliki podstawowej w celu zapewnienia spójności odczytu i zapisu. Repliki pomocnicze znacznie rozszerzają skalę partycji, ponieważ katalogi zazwyczaj przez większość czasu obsługują operacje odczytu.

Aplikacje katalogów nawiązują połączenie z najbliższymi centrami danych. To połączenie zwiększa wydajność i w związku z tym skalowanie jest możliwe. Ponieważ partycje katalogów mogą mieć wiele replik pomocniczych, repliki pomocnicze można umieścić bliżej klientów katalogu. Tylko wewnętrzne składniki usługi katalogu przeprowadzające wiele operacji odczytu bezpośrednio komunikują się z aktywną repliką podstawową.

### <a name="continuous-availability"></a>Ciągła dostępność

Dostępność (lub czas dostępności) definiuje możliwość wykonywania przez system nieprzerwanej pracy. Klucz do wysokiej dostępności w usłudze Azure AD polega na tym, że usługi mogą szybko przenoszony ruch na wiele rozproszonych geograficznie centrów danych. Każde centrum danych jest niezależne, co umożliwia usuwanie nieskorelowanych trybów awarii. W tym projekcie o wysokiej dostępności usługa Azure AD nie wymaga żadnych przestojów w przypadku działań konserwacyjnych.

Projekt partycji usługi Azure AD jest uproszczony w porównaniu z projektem przedsiębiorstwa usługi AD przy użyciu jednego wzorca, który obejmuje starannie zorganizowany i deterministyczny proces trybu failover repliki podstawowej.

#### <a name="fault-tolerance"></a>Odporność na uszkodzenia

System jest bardziej dostępny, jeśli jest odporny na błędy sprzętu, sieci i oprogramowania. Dla każdej partycji w katalogu istnieje replika główna o wysokiej dostępności — replika podstawowa. W ramach tej repliki są wykonywane tylko operacje zapisu. Ta replika jest stale i ściśle monitorowana, a operacje zapisu w przypadku wykrycia błędu mogą zostać natychmiast przełączone do innej repliki, która staje się wtedy nową repliką podstawową. Podczas pracy w trybie failover może to być związane z utratą dostępności do zapisu trwającą zwykle od 1 do 2 minut. Taka sytuacja nie ma wpływu na dostępność do odczytu.

Operacje odczytu (których liczba jest wiele rzędów wielkości większa od operacji zapisu) odbywają się tylko za pośrednictwem replik pomocniczych. Ponieważ repliki pomocnicze są idempotentne, utrata dowolnej repliki w danej partycji jest łatwo kompensowana przez przełączenie operacji odczytu do innej repliki, która zwykle znajduje się w tym samym centrum danych.

#### <a name="data-durability"></a>Trwałość danych

Zapis jest trwale zatwierdzany do co najmniej dwóch centrów danych przed potwierdzeniem. Jest to wykonywane przez pierwsze zatwierdzenie zapisu na serwerze podstawowym, a następnie natychmiastowe replikowanie zapisu do co najmniej jednego innego centrum danych. Ta akcja zapisu zapewnia, że potencjalna strata centrum danych, w którym znajduje się podstawowy, nie powoduje utraty.

Usługa Azure AD utrzymuje zerowy [cel czasu odzyskiwania (RTO)](https://en.wikipedia.org/wiki/Recovery_time_objective) , aby nie utracić danych w trybie failover. Obejmuje to następujące działania:

* Wystawianie tokenów i odczyty katalogów
* Zezwalanie na zapisywanie w katalogu tylko około 5 minut RTO

### <a name="datacenters"></a>Centra danych

Repliki usługi Azure AD są przechowywane w centrach danych znajdujących się na całym świecie. Aby uzyskać więcej informacji, zobacz [globalna infrastruktura platformy Azure](https://azure.microsoft.com/global-infrastructure/).

Usługa Azure AD działa w centrach danych o następujących cechach:

* Uwierzytelnianie, Graf i inne usługi AD znajdują się za usługą bramy. Brama zarządza równoważeniem obciążenia tych usług. Zostanie automatycznie przełączona w tryb failover w przypadku wykrycia wszelkich serwerów w złej kondycji przy użyciu transakcyjnych sond kondycji. W oparciu o te sondy kondycji Brama dynamicznie kieruje ruch do centrów danych w dobrej kondycji.
* W przypadku operacji *odczytu* katalog ma repliki pomocnicze i odpowiadające im usługi frontonu w konfiguracji aktywne-aktywne działające w wielu centrach danych. W przypadku awarii całego centrum danych ruch zostanie automatycznie przekierowany do innego centrum.
 * W przypadku *operacji zapisu* katalog przejdzie do trybu failover (Master) repliki w centrach danych przez zaplanowaną (Nowa podstawowa jest synchronizowana z poprzednimi podstawowymi) lub awaryjnymi procedurami pracy awaryjnej. Trwałość danych jest uzyskiwana przez replikację wszelkich zatwierdzeń do co najmniej dwóch centrum danych.

#### <a name="data-consistency"></a>Spójność danych

Model katalogu jest jedną z ewentualnych spójności. Jednym z typowych problemów z rozproszonymi systemami replikacji asynchronicznej jest to, że dane zwracane z "konkretnej" repliki mogą być nieaktualne. 

Usługa Azure AD zapewnia spójność odczytu i zapisu dla aplikacji działających w ramach repliki pomocniczej, kierując jej operacje zapisu do repliki podstawowej i synchronicznie pobierając operacje zapisu do repliki pomocniczej.

Operacje zapisu aplikacji przy użyciu interfejsu API Microsoft Graph usługi Azure AD są wyodrębniane z zachowania koligacji z repliką katalogu w celu zapewnienia spójności odczytu i zapisu. Usługa interfejsu API Microsoft Graph obsługuje sesję logiczną, która ma koligację z repliką pomocniczą używaną do odczytu; koligacja jest przechwytywana w "token repliki", który jest buforowany przez usługę przy użyciu rozproszonej pamięci podręcznej w pomocniczym centrum danych repliki. Token ten jest następnie używany na potrzeby kolejnych operacji w ramach tej samej sesji logicznej. Aby nadal używać tej samej sesji logicznej, kolejne żądania muszą być kierowane do tego samego centrum danych usługi Azure AD. Nie można kontynuować sesji logicznej, jeśli żądania klienta katalogu są kierowane do wielu centrów danych usługi Azure AD; w takim przypadku klient ma wiele sesji logicznych, które mają niezależne spójności odczytu i zapisu.

 >[!NOTE]
 >Operacje zapisu są natychmiast replikowane do repliki pomocniczej, dla której zostały utworzone operacje odczytu sesji logicznej.

#### <a name="backup-protection"></a>Ochrona kopii zapasowych

Katalog implementuje usuwanie elastyczne zamiast usuwania całkowitego w celu umożliwienia użytkownikom i dzierżawcom łatwego odzyskiwania w razie wykonania przypadkowego usunięcia przez klienta. Jeśli administrator dzierżawy przypadkowo usunie użytkowników, może łatwo cofnąć i przywrócić usuniętych użytkowników.

Usługa Azure AD implementuje codzienne tworzenie kopii zapasowych wszystkich danych i w związku z tym umożliwia autorytatywne przywrócenie danych w przypadku wykonania jakichkolwiek logicznych operacji usuwania lub uszkodzenia danych. Warstwa danych służy do poprawiania kodów błędów, dzięki czemu może sprawdzać występowanie błędów i automatycznie poprawiać błędy poszczególnych typów dysków.

#### <a name="metrics-and-monitors"></a>Metryki i monitory

Uruchamianie usługi o wysokiej dostępności wymaga światowej klasy metryk i możliwości monitorowania. Usługa Azure AD stale analizuje i raportuje kluczowe metryki kondycji usługi i kryteria powodzenia dla każdej ze swoich usług. Istnieje również ciągły rozwój i dostrajanie metryk oraz monitorowania i alertów dla każdego scenariusza, w ramach każdej usługi Azure AD i dla wszystkich usług.

Jeśli jakakolwiek usługa Azure AD nie działa zgodnie z oczekiwaniami, akcje są natychmiast podejmowane w celu szybkiego przywrócenia funkcjonalności. Najważniejszymi metrykami śledzenia usługi Azure AD jest to, jak szybko problemy z witryną na żywo mogą być wykrywane i zmniejszane dla klientów. Ciężko pracujemy nad funkcjami monitorowania i alertów w celu zminimalizowania czasu wykrywania (docelowy czas wykrywania: poniżej 5 minut) oraz nad gotowością operacyjną w celu zminimalizowania czasu usuwania problemu (docelowy czas usuwania problemu: poniżej 30 minut).

#### <a name="secure-operations"></a>Zabezpieczanie operacji

Korzystanie z kontroli operacyjnych, takich jak uwierzytelnianie wieloskładnikowe (MFA) dla każdej operacji, a także Inspekcja wszystkich operacji. Ponadto przy użyciu systemu podniesienia uprawnień just-in-Time w celu zapewnienia ciągłego tymczasowego dostępu do każdego zadania operacyjnego na żądanie. Aby uzyskać więcej informacji, zobacz [Zaufana chmura](https://azure.microsoft.com/support/trust-center).

## <a name="next-steps"></a>Następne kroki

[Przewodnik dewelopera usługi Azure Active Directory](../develop/index.yml)