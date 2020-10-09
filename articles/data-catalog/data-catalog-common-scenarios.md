---
title: Azure Data Catalog common scenarios (Typowe scenariusze dla usługi Azure Data Catalog)
description: Omówienie typowych scenariuszy dotyczących Azure Data Catalog, w tym rejestracji i odnajdywania źródeł danych o wysokiej wartości, umożliwiających samoobsługową analizę biznesową oraz przechwytywanie istniejącej wiedzy o źródłach danych i procesach.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: aeae505b510f563a6640726c384ea358983eb24f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "68736458"
---
# <a name="azure-data-catalog-common-scenarios"></a>Azure Data Catalog common scenarios (Typowe scenariusze dla usługi Azure Data Catalog)
W tym artykule przedstawiono typowe scenariusze, w których Azure Data Catalog może pomóc organizacji w zwiększeniu wartości z istniejących źródeł danych.

## <a name="scenario-1-registration-of-central-data-sources"></a>Scenariusz 1: Rejestracja centralnych źródeł danych
Organizacje często mają wiele źródeł danych o wysokiej wartości. Te źródła danych obejmują systemy biznesowe, usługi przetwarzania transakcji online (OLTP), magazyny danych i bazy danych analizy biznesowej/analityczne. Liczba systemów i nakładanie się między nimi, zazwyczaj rośnie wraz z upływem czasu w miarę rozwoju potrzeb firmy, a sama sama firma rozwija się, na przykład fuzje i przejęcia.

Może być trudne, aby członkowie organizacji wiedzieli, gdzie można zlokalizować dane w tych źródłach danych. Często zadawane pytania są następujące:

* Z trzech systemów kadr używanych w firmie, których należy użyć do utworzenia tego typu raportu?
* Gdzie warto uzyskać certyfikowane numery sprzedaży dla roku obrachunkowego, który właśnie zakończył?
* Kto powinien zadawać pytania lub jaki jest proces, którego należy użyć, aby uzyskać dostęp do magazynu danych?
* Nie wiem, czy te liczby są odpowiednie. Kto może zadawać wgląd w szczegółowe informacje na temat tego, jak te dane powinny być używane, zanim udostępnię ten pulpit nawigacyjny mojego zespołu?

Na te i inne pytania Azure Data Catalog mogą zapewnić odpowiedzi. Centralne i wysoce wartości zarządzane przez nią źródła danych, które są używane w różnych organizacjach, są często logicznym punktem wyjścia do wypełniania katalogu. Mimo że każdy użytkownik może zarejestrować źródło danych, dzięki czemu wykaz rozpoczyna się od źródeł danych, które najprawdopodobniej zapewniają wartość największą liczbę użytkowników, ułatwiają wdrażanie i korzystanie z systemu. 

Jeśli rozpoczynasz pracę z Azure Data Catalog, identyfikowanie i rejestrowanie najważniejszych źródeł danych, które są używane przez wielu różnych zespołów odbiorców danych, może być pierwszym krokiem do sukcesu.

W tym scenariuszu przedstawiono również możliwość dodawania adnotacji do źródeł danych o wysokiej wartości, które ułatwiają zrozumienie i dostęp do nich. Jednym z kluczowych aspektów tego nakładu jest dołączenie informacji na temat sposobu, w jaki użytkownicy mogą żądać dostępu do źródła danych. Za pomocą Azure Data Catalog można podać adres e-mail użytkownika lub zespołu, który jest odpowiedzialny za kontrolowanie dostępu do źródła danych, linki do istniejących narzędzi lub dokumentacji lub bezpłatny tekst opisujący proces żądania dostępu. Te informacje ułatwiają członkom, którzy odnajdują zarejestrowane źródła danych, ale którzy nie mają jeszcze uprawnień dostępu do danych w celu łatwego żądania dostępu przy użyciu procesów, które są zdefiniowane i kontrolowane przez właścicieli źródła danych.

## <a name="scenario-2-self-service-business-intelligence"></a>Scenariusz 2: samoobsługowa analiza biznesowa
Chociaż tradycyjne korporacyjne rozwiązania do analizy biznesowej w dalszym ciągu są niecenną częścią Landscapes danych organizacji, zmiana tempa biznesowego była samoobsługowa i bardziej ważna. Dzięki użyciu samoobsługowej analizy biznesowej Pracownicy przetwarzający informacje i analitycy mogą tworzyć własne raporty, skoroszyty i pulpity nawigacyjne, bez polegania na centralnym zespole IT lub ograniczonym przez ten plan i dostępność.

W scenariuszach samoobsługowej analizy biznesowej użytkownicy często łączą dane z wielu źródeł, z których wiele mogło nie zostać wcześniej użyte do analizy biznesowej i analiz. Mimo że niektóre z tych źródeł danych mogą już być znane, może być trudne do odnalezienia, co należy zrobić, aby znaleźć i oszacować potencjalne źródła danych dla danego zadania.

Tradycyjnie ten proces odnajdywania jest jednym z nich: analitycy używają równorzędnych połączeń sieciowych do identyfikowania osób, które współpracują z danymi. Po znalezieniu i użyciu źródła danych proces ten jest powtarzany ponownie dla każdego kolejnego wysiłku samoobsługowego analizy biznesowej z wieloma użytkownikami wykonującymi nadmiarowy ręczny proces odnajdywania.

W przypadku Azure Data Catalog organizacja może przerwać ten cykl nakładu pracy. Po znalezieniu źródła danych za pośrednictwem tradycyjnych metod analityk może go zarejestrować, aby łatwiej było go odnajdywać przez innych użytkowników w przyszłości. Chociaż analityk może dodać więcej wartości, dodając adnotację do zarejestrowanych zasobów danych, ta Adnotacja nie musi być wykonywana w tym samym czasie co rejestracja. Użytkownicy mogą w miarę upływu czasu, jak ich harmonogramy, stopniowo dodawać wartości do źródeł danych zarejestrowanych w wykazie.

Ten organiczny wzrost zawartości katalogu jest naturalnym uzupełnieniem rejestracji centralnych źródeł danych. Wstępne wypełnianie wykazu danymi, których potrzebuje wielu użytkowników, może być motywacja do początkowego użycia i odnajdywania. Umożliwienie użytkownikom rejestrowania dodatkowych źródeł i dodawania do nich adnotacji może być sposobem na ich utrzymywanie i innym członkom organizacji.

Warto zauważyć, że mimo że ten scenariusz koncentruje się na samoobsługowym analizie biznesowej, te same wzorce i wyzwania mają zastosowanie w przypadku projektów analizy biznesowej w dużej skali. Korzystając z Data Catalog, organizacja może poprawić każdy nakład pracy, który obejmuje ręczne proces odnajdywania źródła danych.

## <a name="scenario-3-capturing-tribal-knowledge"></a>Scenariusz 3: Przechwytywanie informacji o plemienne
Jak poznać dane potrzebne do wykonania zadania i miejsce, w którym można znaleźć te dane?

Jeśli jesteś w Twoim zadaniu przez pewien czas, prawdopodobnie wiesz. Przeszedłeś przez proces uczenia się, a w miarę upływu czasu zapoznaj się ze źródłami danych, które są kluczowe dla codziennej pracy.

Po dołączeniu nowego pracownika do zespołu, w jaki sposób ta osoba wie, jakie dane są wymagane dla tego zadania i gdzie można je znaleźć?

Szanse to, że nowa osoba jest powiązana z tymi pytaniami.

Ten ciągły transfer wiedzy plemienne jest częścią procesu odnajdywania źródła danych w dużych i małych organizacjach. Coraz więcej starszych i doświadczonych członków zespołu utworzyły wiedzę w ciągu lat, a nowsze członków zespołu zapoznają się z zapytaniem. Najważniejsze informacje często występują tylko w imieniu kilku najważniejszych osób i gdy osoby te znajdują się na urlopie lub opuszczają zespół, organizacja cierpi.

Eksperci danych zwykle podejmują wysiłki w celu udokumentowania swojej wiedzy, udostępniania jej za pośrednictwem poczty e-mail lub dokumentów programu Word w witrynie zespołu programu SharePoint. Chociaż ta metoda może być cenna, wprowadza nowy problem z odnajdywaniem: jak ludzie wiedzą, jaka dokumentacja istnieje i gdzie ją znaleźć?

W przypadku Azure Data Catalog organizacja ma jedną centralną lokalizację do przechowywania i udostępniania wiedzy plemienne oraz ułatwia jej odnajdowanie. W Data Catalog eksperci danych mogą bezpośrednio dodawać adnotacje do zasobów danych i udostępniać linki do istniejącej dokumentacji. Gdy członkowie organizacji używają wykazu do odnajdywania źródła danych, znajdą nie tylko źródło, ale również wiedzę, która wcześniej istniała tylko w zdanie ekspertów w organizacji.
