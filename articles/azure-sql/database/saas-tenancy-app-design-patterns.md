---
title: Wzorce SaaS z wieloma dzierżawcami
description: Zapoznaj się z wymaganiami i typowymi wzorcami architektury danych dla aplikacji bazy danych SaaS (Software as a Service), które działają w środowisku chmury platformy Azure.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.custom: seoapril2019, sqldbrb=1
ms.openlocfilehash: 8a13c641d50a68d9661b4aa6caf8effb82d53dd7
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92793232"
---
# <a name="multi-tenant-saas-database-tenancy-patterns"></a>Wzorce dzierżawy bazy danych SaaS z wieloma dzierżawcami
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

W tym artykule opisano różne modele dzierżawców dostępne dla wielodostępnej aplikacji SaaS.

Podczas projektowania wielodostępnej aplikacji SaaS należy starannie wybrać model dzierżawy, który najlepiej odpowiada wymaganiom Twojej aplikacji.  Model dzierżawy określa, w jaki sposób dane poszczególnych dzierżawców są mapowane na magazyn.  Wybór modelu dzierżawy wpływa na projekt aplikacji i zarządzanie nim.  Późniejsze przełączanie na inny model jest czasami kosztowne.

## <a name="a-saas-concepts-and-terminology"></a>A. Pojęcia i terminologia SaaS

W modelu oprogramowanie jako usługa (SaaS) firma nie sprzedaje *licencji* na oprogramowanie. Zamiast tego każdy klient dokonuje płatności w firmie, a każdy klient ma *dzierżawę* swojej firmy.

W przypadku płatnej dzierżawy każdy dzierżawca otrzymuje dostęp do składników aplikacji SaaS i ma swoje dane przechowywane w systemie SaaS.

Termin *model dzierżawy* odnosi się do sposobu organizowania danych przechowywanych przez dzierżawców:

- *Pojedyncza dzierżawa:* &nbsp; Każda baza danych przechowuje dane tylko z jednej dzierżawy.
- *Wiele dzierżawców:* &nbsp; Każda baza danych przechowuje dane pochodzące z wielu różnych dzierżawców (z mechanizmami ochrony prywatności danych).
- Dostępne są również modele dzierżaw hybrydowej.

## <a name="b-how-to-choose-the-appropriate-tenancy-model"></a>B. Jak wybrać odpowiedni model dzierżawy

Ogólnie rzecz biorąc, model dzierżawy nie ma wpływu na funkcję aplikacji, ale prawdopodobnie ma wpływ na inne aspekty ogólnego rozwiązania.  Do oceny poszczególnych modeli służą następujące kryteria:

- **Względem**
    - Liczba dzierżawców.
    - Magazyn dla dzierżawy.
    - Magazyn w agregacji.
    - Roboczych.

- **Izolacja dzierżawy:** &nbsp; Izolacja i wydajność danych (czy obciążenie jednej dzierżawy wpływa na innych).

- **Koszt dla dzierżawy:** &nbsp; Koszty bazy danych.

- **Złożoność rozwoju:**
    - Zmiany w schemacie.
    - Zmiany w zapytaniach (wymagane przez wzorzec).

- **Złożoność operacyjna:**
    - Monitorowanie wydajności i zarządzanie nią.
    - Zarządzanie schematami.
    - Przywracanie dzierżawy.
    - Odzyskiwanie sprawności systemu po awarii.

- **Szerszym:** &nbsp; Łatwość obsługi dostosowań schematu, które są specyficzne dla konkretnych dzierżawców lub klasy dzierżawy.

Dyskusja o dzierżawie koncentruje się na warstwie *danych* .  Należy jednak wziąć pod uwagę chwilę warstwy *aplikacji* .  Warstwa aplikacji jest traktowana jako jednostka monolityczna.  W przypadku dzielenia aplikacji na wiele małych składników wybór modelu dzierżawy może ulec zmianie.  Niektóre składniki mogą być traktowane inaczej niż inne osoby, które dotyczą zarówno usługi dzierżawy, jak i używanej platformy.

## <a name="c-standalone-single-tenant-app-with-single-tenant-database"></a>C. Autonomiczna aplikacja jednodostępna z jedną dzierżawą

#### <a name="application-level-isolation"></a>Izolacja poziomu aplikacji

W tym modelu cała aplikacja jest instalowana wielokrotnie, raz dla każdej dzierżawy.  Każde wystąpienie aplikacji jest wystąpieniem autonomicznym, więc nigdy nie współdziała z żadnym innym wystąpieniem autonomicznym.  Każde wystąpienie aplikacji ma tylko jedną dzierżawę i w związku z tym wymaga tylko jednej bazy danych.  Dzierżawca ma całą bazę danych.

![Projektowanie aplikacji autonomicznej z dokładnie jedną bazą danych o pojedynczej dzierżawie.][image-standalone-app-st-db-111a]

Każde wystąpienie aplikacji jest instalowane w oddzielnej grupie zasobów platformy Azure.  Grupa zasobów może należeć do subskrypcji należącej do dostawcy oprogramowania lub dzierżawy.  W obu przypadkach dostawca może zarządzać oprogramowaniem dla dzierżawy.  Każde wystąpienie aplikacji jest skonfigurowane do nawiązywania połączenia z odpowiadającą jej bazą danych.

Każda baza danych dzierżawy jest wdrażana jako pojedyncza baza danych.  Ten model zapewnia największą izolację bazy danych.  Natomiast izolacja wymaga przydzielenia wystarczającej ilości zasobów do każdej bazy danych w celu obsługi obciążeń szczytowych.  W tym miejscu nie można używać pul elastycznych dla baz danych wdrożonych w różnych grupach zasobów lub w różnych subskrypcjach.  To ograniczenie powoduje, że ten autonomiczny model aplikacji z jedną dzierżawą to najbardziej kosztowne rozwiązanie od ogólnego punktu widzenia kosztów bazy danych.

#### <a name="vendor-management"></a>Zarządzanie dostawcami

Dostawca może uzyskać dostęp do wszystkich baz danych we wszystkich wystąpieniach aplikacji autonomicznej, nawet jeśli wystąpienia aplikacji są zainstalowane w różnych subskrypcjach dzierżawy.  Dostęp jest uzyskiwany za pośrednictwem połączeń SQL.  Ten dostęp z wielu wystąpień może umożliwić dostawcy scentralizowane zarządzanie schematami i zapytania obejmujące wiele baz danych na potrzeby raportowania lub analizy.  Jeśli jest to konieczne scentralizowane zarządzanie, należy wdrożyć wykaz, który mapuje identyfikatory dzierżawców na identyfikatory URI bazy danych.  Azure SQL Database udostępnia bibliotekę fragmentowania, która jest używana razem w celu udostępnienia katalogu.  Biblioteka fragmentowania jest formalnie nazywana [biblioteką klienta Elastic Database][docu-elastic-db-client-library-536r].

## <a name="d-multi-tenant-app-with-database-per-tenant"></a>D. Aplikacja wielodostępna z bazą danych na dzierżawcę

Ten następny wzorzec używa aplikacji wielodostępnej z wieloma bazami danych, które są bazami danych o pojedynczej dzierżawie.  Dla każdej nowej dzierżawy jest inicjowana Nowa baza danych.  Warstwa aplikacji jest skalowana w poziomie *up* , dodając więcej zasobów na węzeł.  Lub aplikacja jest skalowana *w poziomie,* dodając więcej węzłów.  Skalowanie bazuje na obciążeniu i jest niezależne od liczby lub skali poszczególnych baz danych.

![Projektowanie aplikacji z wieloma dzierżawcami przy użyciu bazy danych na dzierżawcę.][image-mt-app-db-per-tenant-132d]

#### <a name="customize-for-a-tenant"></a>Dostosowywanie dla dzierżawy

Podobnie jak w przypadku wzorca aplikacji autonomicznej, korzystanie z baz danych o pojedynczej dzierżawie zapewnia silną izolację dzierżawy.  W dowolnej aplikacji, której model określa tylko bazy danych z jedną dzierżawą, schemat dla każdej danej bazy danych można dostosować i zoptymalizować dla swojej dzierżawy.  To dostosowanie nie ma wpływu na innych dzierżawców w aplikacji. Prawdopodobnie Dzierżawca może potrzebować danych poza podstawowymi polami danych, które są potrzebne wszystkim dzierżawcom.  Dodatkowo pole dodatkowe dane może potrzebować indeksu.

W przypadku bazy danych na dzierżawcę dostosowanie schematu dla co najmniej jednego dzierżawy jest proste.  Dostawca aplikacji musi projektować procedury, aby dokładnie zarządzać dostosowaniami schematu w odpowiedniej skali.

#### <a name="elastic-pools"></a>Pule elastyczne

Po wdrożeniu baz danych w tej samej grupie zasobów można je grupować w pule elastyczne.  Pule zapewniają oszczędny sposób udostępniania zasobów w wielu bazach danych.  Ta opcja puli jest tańsza niż wymaganie, aby każda baza danych była wystarczająco duża, aby pomieścić szczytowe użycie.  Mimo że bazy danych w puli współużytkują dostęp do zasobów, można nadal uzyskać wysoką izolację wydajności.

![Projektowanie aplikacji wielodostępnej z użyciem bazy danych na dzierżawcę przy użyciu puli elastycznej.][image-mt-app-db-per-tenant-pool-153p]

Azure SQL Database udostępnia narzędzia niezbędne do konfigurowania i monitorowania udostępniania oraz zarządzania nim.  Zarówno metryki wydajności na poziomie puli, jak i na poziomie bazy danych są dostępne w Azure Portal oraz w dziennikach Azure Monitor.  Metryki mogą dać doskonały wgląd w wydajność zagregowaną i konkretną dla dzierżawy.  Pojedyncze bazy danych można przenosić między pulami w celu zapewnienia zasobów zarezerwowanych dla określonej dzierżawy.  Te narzędzia pozwalają zapewnić dobrą wydajność w sposób ekonomiczny.

#### <a name="operations-scale-for-database-per-tenant"></a>Skala operacji dla bazy danych na dzierżawcę

Azure SQL Database ma wiele funkcji zarządzania przeznaczonych do zarządzania dużą liczbą baz danych w odpowiedniej skali, na przykład ponad 100 000 baz danych.  Te funkcje sprawiają, że wzorzec dla dzierżawy bazy danych jest wiarygodny.

Załóżmy na przykład, że system ma bazę danych dzierżawcy 1000 jako tylko jedną bazę danych.  Baza danych może mieć 20 indeksów.  Jeśli system jest konwertowany na 1000 baz danych o pojedynczej dzierżawie, liczba indeksów rośnie do 20 000.  W Azure SQL Database w ramach [dostrajania automatycznego][docu-sql-db-automatic-tuning-771a]funkcja automatycznego indeksowania jest domyślnie włączona.  Automatyczne indeksowanie jest zarządzane przez wszystkie indeksy 20 000 i ich bieżące optymalizacje tworzenia i upuszczania.  Te automatyczne akcje są wykonywane w ramach pojedynczej bazy danych i nie są skoordynowane ani ograniczone przez podobne działania w innych bazach danych.  Automatyczne indeksowanie traktuje indeksy inaczej niż w przypadku zajętej bazy danych niż w mniej obciążonaj bazie danych.  Ten typ dostosowania zarządzania indeksami będzie niepraktyczny w skali dla dzierżawy bazy danych, jeśli to ogromne zadanie zarządzania musiało zostać wykonane ręcznie.

Inne funkcje zarządzania, które również skalują, obejmują:

- Wbudowane kopie zapasowe.
- Wysoka dostępność.
- Szyfrowanie na dysku.
- Dane telemetryczne wydajności.

#### <a name="automation"></a>Automatyzacja

Operacje zarządzania mogą być przetwarzane przy użyciu skryptów i oferowane przez model [DevOps][http-visual-studio-devops-485m] .  Operacje mogą nawet być zautomatyzowane i uwidaczniane w aplikacji.

Na przykład można zautomatyzować odzyskiwanie pojedynczej dzierżawy do wcześniejszego punktu w czasie.  Odzyskiwanie wymaga jedynie przywrócenia jednej bazy danych z pojedynczą dzierżawą, która przechowuje dzierżawcę.  To przywracanie nie ma wpływu na innych dzierżawców, co potwierdza, że operacje zarządzania są na poziomie szczegółowości poszczególnych dzierżawców.

## <a name="e-multi-tenant-app-with-multi-tenant-databases"></a>E. Aplikacja wielodostępna z bazami danych z wieloma dzierżawcami

Innym dostępnym wzorcem jest przechowywanie wielu dzierżawców w bazie danych z wieloma dzierżawcami.  Wystąpienie aplikacji może zawierać dowolną liczbę baz danych z wieloma dzierżawami.  Schemat bazy danych z wieloma dzierżawcami musi mieć co najmniej jedną kolumnę identyfikatora dzierżawy, aby można było selektywnie pobrać dane z danej dzierżawy.  Ponadto schemat może wymagać kilku tabel lub kolumn, które są używane tylko przez podzestaw dzierżawców.  Jednak kod statyczny i dane referencyjne są przechowywane tylko raz i są udostępniane wszystkim dzierżawcom.

#### <a name="tenant-isolation-is-sacrificed"></a>Izolacja dzierżawy jest niedostępna

*Dane:* &nbsp; Baza danych z wieloma dzierżawcami musi być niezbędna do izolacji dzierżawy.  Dane wielu dzierżawców są przechowywane razem w jednej bazie danych.  Podczas programowania upewnij się, że zapytania nigdy nie ujawniają danych z więcej niż jednej dzierżawy.  SQL Database obsługuje [zabezpieczenia na poziomie wierszy][docu-sql-svr-db-row-level-security-947w], które mogą wymusić, że dane zwrócone z zapytania zostaną objęte zakresem pojedynczej dzierżawy.

*Przetwarzanie:* &nbsp; Baza danych z wieloma dzierżawcami udostępnia zasoby obliczeniowe i magazynowe we wszystkich dzierżawach.  Bazę danych jako całość można monitorować, aby upewnić się, że wykonuje zadowalająco.  Jednak system Azure nie ma wbudowanej metody monitorowania użycia tych zasobów ani zarządzania nimi przez indywidualną dzierżawę.  W związku z tym baza danych z wieloma dzierżawcami ma zwiększone ryzyko wystąpienia nieoczekiwanych obciążeń, w przypadku których obciążenie jednej dzierżawy ma wpływ na wydajność innych dzierżawców w tej samej bazie danych.  Dodatkowe monitorowanie na poziomie aplikacji może monitorować wydajność na poziomie dzierżawy.

#### <a name="lower-cost"></a>Niższy koszt

Ogólnie rzecz biorąc, wielodostępne bazy danych mają najniższy koszt dla każdej dzierżawy.  Koszty zasobów dla pojedynczej bazy danych są mniejsze niż w przypadku puli elastycznej o równoważnej wielkości.  Ponadto w scenariuszach, w których dzierżawcy potrzebują tylko ograniczonego magazynu, potencjalnie miliony dzierżawców może być przechowywanych w pojedynczej bazie danych.  Żadna Pula elastyczna nie może zawierać milionów baz danych.  Jednak rozwiązanie zawierające 1000 baz danych na pulę z pulami 1000 może osiągnąć skalę milionów na ryzyko nieporęczny zarządzania.

Dwie zmiany modelu bazy danych z wieloma dzierżawcami zostały omówione w poniższej sekcji, dzięki czemu model wielodostępowy podzielonej na fragmenty jest najbardziej elastyczny i skalowalny.

## <a name="f-multi-tenant-app-with-a-single-multi-tenant-database"></a>F. Aplikacja wielodostępna z jedną bazą danych z wieloma dzierżawcami

Najprostszy wzorzec bazy danych z wieloma dzierżawcami używa pojedynczej bazy danych do hostowania danych dla wszystkich dzierżawców.  Po dodaniu większej liczby dzierżawców baza danych jest skalowana z większą ilością pamięci masowej i zasobów obliczeniowych.  Skalowanie w górę może być konieczne, chociaż zawsze istnieje ostateczny limit skalowania.  Jednak długo przed osiągnięciem tego limitu baza danych będzie nieporęczny do zarządzania.

Operacje zarządzania, które koncentrują się na poszczególnych dzierżawcach, są bardziej skomplikowane do wdrożenia w wielodostępnej bazie danych.  I na dużą skalę operacje mogą stać się nieakceptowalne.  Przykładem jest przywracanie do punktu w czasie dla tylko jednej dzierżawy.

## <a name="g-multi-tenant-app-with-sharded-multi-tenant-databases"></a>G. Aplikacja wielodostępna z bazami danych z wieloma dzierżawcami podzielonej na fragmenty

Większość aplikacji SaaS jednocześnie uzyskuje dostęp do danych tylko z jednej dzierżawy.  Ten wzorzec dostępu umożliwia dystrybuowanie danych dzierżaw między wieloma bazami danych lub fragmentów, gdzie wszystkie dane dla jednej dzierżawy znajdują się w jednym fragmentu.  W połączeniu z wielodostępnym wzorcem bazy danych model podzielonej na fragmenty umożliwia niemal nieograniczoną skalę.

![Projektowanie aplikacji wielodostępnej za pomocą podzielonej na fragmenty baz danych z wieloma dzierżawcami.][image-mt-app-sharded-mt-db-174s]

#### <a name="manage-shards"></a>Zarządzanie fragmentów

Fragmentowania dodaje złożoność zarówno do projektu, jak i zarządzania operacyjnego.  Katalog jest wymagany, aby zachować mapowanie między dzierżawcami i bazami danych.  Ponadto, aby zarządzać fragmentów i populacją dzierżawy, wymagane są procedury zarządzania.  Na przykład procedury muszą zostać zaprojektowane w celu dodawania i usuwania fragmentów oraz przenoszenia danych dzierżawy między fragmentów.  Jednym ze sposobów skalowania jest dodanie nowego fragmentu i wypełnianie go nowymi dzierżawcami.  W innych przypadkach można podzielić gęsto wypełnionego fragmentu na dwa niegęsto wypełnione fragmentów.  Po przeniesieniu lub wycofaniu kilku dzierżawców można scalić rozrzedzonie wypełnione fragmentów.  Scalanie mogłoby spowodować zwiększenie ekonomicznego wykorzystania zasobów.  Dzierżawy mogą być również przenoszone między fragmentów, aby zrównoważyć obciążenia.

SQL Database udostępnia narzędzie do dzielenia/scalania, które działa w połączeniu z biblioteką fragmentowania i bazą danych wykazu.  Podana aplikacja może dzielić i scalać fragmentów, a dane dzierżawy mogą być przenoszone między fragmentów.  Aplikacja zachowuje również katalog podczas tych operacji, zaznaczając w trybie offline dzierżawy, których dotyczą przed przeniesieniem.  Po przeniesieniu aplikacja ponownie zaktualizuje katalog przy użyciu nowego mapowania i oznaczy dzierżawę jako przełączenia w tryb online.

#### <a name="smaller-databases-more-easily-managed"></a>Łatwiejsze zarządzanie mniejszymi bazami danych

Dzięki dystrybucji dzierżawców w wielu bazach danych rozwiązanie podzielonej na fragmenty z wieloma dzierżawcami skutkuje mniejszymi bazami danych, które są łatwiejsze do zarządzania.  Na przykład przywrócenie określonej dzierżawy do wcześniejszego punktu w czasie obejmuje teraz przywrócenie pojedynczej mniejszej bazy danych z kopii zapasowej, a nie większej bazy danych zawierającej wszystkich dzierżawców. Rozmiar bazy danych oraz liczbę dzierżawców na bazę danych można wybrać w celu zrównoważenia obciążenia i wysiłków związanych z zarządzaniem.

#### <a name="tenant-identifier-in-the-schema"></a>Identyfikator dzierżawy w schemacie

W zależności od używanej metody fragmentowania, w schemacie bazy danych mogą być narzucone dodatkowe ograniczenia.  Aplikacja z podziałem/scalaniem SQL Database wymaga, aby schemat zawierał klucz fragmentowania, który zazwyczaj jest identyfikatorem dzierżawy.  Identyfikator dzierżawy jest elementem wiodącym w kluczu podstawowym wszystkich tabel podzielonej na fragmenty.  Identyfikator dzierżawy umożliwia aplikacji Split/Merge szybkie lokalizowanie i przenoszenie danych skojarzonych z konkretną dzierżawą.

#### <a name="elastic-pool-for-shards"></a>Pula elastyczna dla fragmentów

Bazy danych z wieloma dzierżawcami podzielonej na fragmenty można umieścić w pulach elastycznych.  Ogólnie rzecz biorąc wiele baz danych o pojedynczej dzierżawie w puli jest tańsze niż wiele dzierżawców w kilku bazach danych z wieloma dzierżawami.  Bazy danych z wieloma dzierżawcami są korzystne, gdy istnieje duża liczba nieaktywnych dzierżawców.

## <a name="h-hybrid-sharded-multi-tenant-database-model"></a>C. Model bazy danych hybrydowej podzielonej na fragmenty z wieloma dzierżawcami

W modelu hybrydowym wszystkie bazy danych mają identyfikator dzierżawy w swoim schemacie.  Bazy danych są w stanie przechowywać więcej niż jedną dzierżawę, a bazy danych mogą być podzielonej na fragmenty.  Dlatego w sensie schematu są to wszystkie wielodostępne bazy danych.  Jednak niektóre z tych baz danych zawierają tylko jedną dzierżawę.  Niezależnie od ilości dzierżawców przechowywanych w danej bazie danych nie ma wpływu na schemat bazy danych.

#### <a name="move-tenants-around"></a>Przenoszenie dzierżawców

W dowolnym momencie można przenieść konkretną dzierżawę do własnej bazy danych z wieloma dzierżawcami.  W dowolnym momencie możesz zmienić zdanie i przenieść dzierżawcę z powrotem do bazy danych zawierającej wiele dzierżawców.  Możesz również przypisać dzierżawcę do nowej bazy danych z jedną dzierżawą podczas aprowizacji nowej bazy danych.

Model hybrydowy ma duże różnice między potrzebami zasobów dla możliwych do zidentyfikowania grup dzierżawców.  Załóżmy na przykład, że dzierżawcy uczestniczący w bezpłatnej wersji próbnej nie zagwarantujemy tego samego wysokiego poziomu wydajności, który subskrybuje dzierżawy.  Zasady mogą być przeznaczone dla dzierżawców w fazie bezpłatnej wersji próbnej w ramach wielodostępnej bazy danych, która jest współużytkowana przez wszystkie dzierżawy bezpłatnych wersji próbnych.  Gdy dzierżawa bezpłatnej wersji próbnej subskrybuje podstawową warstwę usług, dzierżawa może zostać przeniesiona do innej wielodostępnej bazy danych, która może mieć mniejszą liczbę dzierżawców.  Subskrybent, który płaci za warstwę usługi Premium, można przenieść do własnej nowej bazy danych o pojedynczej dzierżawie.

#### <a name="pools"></a>Pule

W tym modelu hybrydowym bazy danych o pojedynczej dzierżawie dla dzierżawców subskrybentów mogą być umieszczane w pulach zasobów, aby zmniejszyć koszty bazy danych dla dzierżawy.  Jest to również wykonywane w modelu dzierżawy bazy danych.

## <a name="i-tenancy-models-compared"></a>I. Porównanie modeli dzierżawy

Poniższa tabela zawiera podsumowanie różnic między głównymi modelami dzierżawców.

| Miara | Aplikacja autonomiczna | Baza danych — na dzierżawcę | Podzielonej na fragmenty z wieloma dzierżawcami |
| :---------- | :------------- | :------------------ | :------------------- |
| Skalowanie | Średniaa<br />1 – 100s | Bardzo wysoka<br />1 – 100, 000 | Nieograniczona liczba<br />1-1 000, 000 |
| Izolacja dzierżawy | Bardzo wysoka | Wysoki | Małą z wyjątkiem pojedynczej dzierżawy (to samo w przypadku MT DB). |
| Koszt bazy danych na dzierżawcę | Wysokowydajn ma rozmiar dla wartości szczytowych. | Małą używane pule. | Najniższa dla małych dzierżawców w MT baz danych. |
| Monitorowanie wydajności i zarządzanie nimi | Tylko dla dzierżawy | Agregowanie i na dzierżawcę | Agreguj Chociaż jest dla dzierżawy tylko dla zmiennoprzecinkowych. |
| Złożoność programowania | Niski | Niski | Średniookresow ze względu na fragmentowania. |
| Złożoność operacyjna | Niska-wysoka. Indywidualnie proste i złożone na dużą skalę. | Niski poziom. Złożoność adresów wzorców na dużą skalę. | Niska-wysoka. Indywidualne zarządzanie dzierżawcą jest złożone. |
| &nbsp; ||||

## <a name="next-steps"></a>Następne kroki

- [Wdróż i Eksploruj wielodostępną aplikację Wingtip, która korzysta z modelu SaaS opartego na bazie danych na dzierżawcę — Azure SQL Database][docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]

- [Witamy w aplikacji Wingtip bilety przykład SaaS Azure SQL Database dzierżawy][docu-saas-tenancy-welcome-wingtip-tickets-app-384w]


<!--  Article link references.  -->

[http-visual-studio-devops-485m]: https://www.visualstudio.com/devops/

[docu-sql-svr-db-row-level-security-947w]: /sql/relational-databases/security/row-level-security

[docu-elastic-db-client-library-536r]:elastic-database-client-library.md
[docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]: saas-dbpertenant-get-started-deploy.md
[docu-sql-db-automatic-tuning-771a]:automatic-tuning-overview.md
[docu-saas-tenancy-welcome-wingtip-tickets-app-384w]: saas-tenancy-welcome-wingtip-tickets-app.md


<!--  Image references.  -->

[image-standalone-app-st-db-111a]: media/saas-tenancy-app-design-patterns/saas-standalone-app-single-tenant-database-11.png "Projektowanie aplikacji autonomicznej z dokładnie jedną bazą danych o pojedynczej dzierżawie."

[image-mt-app-db-per-tenant-132d]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-13.png "Projektowanie aplikacji z wieloma dzierżawcami przy użyciu bazy danych na dzierżawcę."

[image-mt-app-db-per-tenant-pool-153p]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-pool-15.png "Projektowanie aplikacji wielodostępnej z użyciem bazy danych na dzierżawcę przy użyciu puli elastycznej."

[image-mt-app-sharded-mt-db-174s]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-sharded-multi-tenant-databases-17.png "Projektowanie aplikacji wielodostępnej za pomocą podzielonej na fragmenty baz danych z wieloma dzierżawcami."