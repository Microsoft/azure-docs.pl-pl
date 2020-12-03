---
title: Wprowadzenie do usługi Azure kontrolą (wersja zapoznawcza)
description: Ten artykuł zawiera omówienie usługi Azure kontrolą, w tym jej funkcji i występujących w nim problemów. Usługa Azure kontrolą umożliwia każdemu użytkownikowi rejestrowanie, odnajdowanie i poznawanie źródeł danych oraz korzystanie z nich.
author: hophan
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 11/30/2020
ms.openlocfilehash: e7ad561cf9ce9983f41fa166eb744c90f126e01e
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96555961"
---
# <a name="what-is-azure-purview"></a>Co to jest Azure kontrolą?

> [!IMPORTANT]
> Usługa Azure kontrolą jest obecnie dostępna w wersji zapoznawczej. Dodatkowe [warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych programu Microsoft Azure to m.in. Postanowienia prawne dotyczące funkcji systemu Azure, które są dostępne w wersjach beta, Preview lub w inny sposób nie są jeszcze ogólnie udostępniane.

Azure kontrolą to nowa usługa w chmurze do użycia przez użytkowników danych. Za pomocą usługi Azure kontrolą można centralnie zarządzać nadzorem danych w ramach nieruchomości, obejmującą zarówno środowiska w chmurze, jak i w Premium. Ta usługa umożliwia analitykom biznesowym wyszukiwanie odpowiednich danych przy użyciu zrozumiałych warunków firmy.

Za pomocą interfejsu użytkownika kontrolą lub interfejsu API usługi Apache Atlas użytkownicy techniczni mogą wyświetlać metadane i elementy zasobów danych w katalogu centralnym.

Eksperci, właściciele danych i administratorzy zabezpieczeń mogą dodawać adnotacje do zasobów danych w celu udostępnienia ich swojej znajomości. Wykonują te czynności za pomocą interfejsu użytkownika lub na dużą skalę poprzez automatyczne klasyfikowanie i automatyczne etykietowanie zasad na podstawie metadanych i inspekcji zawartości.

## <a name="discovery-challenges-for-data-consumers"></a>Wyzwania dotyczące odnajdywania dla konsumentów danych

Tradycyjnie, odnajdywanie źródeł danych w przedsiębiorstwie jest procesem organicznym opartym na wiedzy komunalnej. W przypadku firm, które mają największą wartość z zasobów informacji, takie podejście przedstawia wiele wyzwań:

* Ponieważ nie ma centralnej lokalizacji do rejestrowania źródeł danych, użytkownicy mogą nie wiedzieć o źródle danych, o ile nie będą mogli skontaktować się z nim w ramach innego procesu.
* Jeśli użytkownicy nie znają lokalizacji źródła danych, nie mogą łączyć się z danymi za pomocą aplikacji klienckiej. Podczas korzystania z danych użytkownicy muszą znać parametry połączenia lub ścieżkę.
* Zamierzone użycie danych jest ukryte dla użytkowników, chyba że znają lokalizację dokumentacji źródła danych. Źródła danych i dokumentacja mogą znajdować się w kilku miejscach i być używane przez różne rodzaje środowiska.
* Jeśli użytkownicy mają pytania dotyczące zasobu informacyjnego, muszą zlokalizować specjalistę lub zespół, który jest odpowiedzialny za te dane, i skontaktować się z nimi w trybie offline. Nie istnieje jawne połączenie między danymi a ekspertami, którzy mają perspektywę do użycia.
* Jeśli użytkownicy nie będą zrozumieć procesu żądania dostępu do źródła danych, odnajdywanie źródła danych i jego dokumentacja nie ułatwią im uzyskiwania dostępu do danych.

## <a name="discovery-challenges-for-data-producers"></a>Wyzwania dotyczące odnajdywania dla producentów danych

Chociaż konsumenci danych stawiają czoła tym wyżej wymienionym wyzwaniom, przed użytkownikami odpowiedzialnymi za tworzenie i utrzymywanie zasobów informacyjnych stoją inne wyzwania:

* Dodawanie adnotacji do źródeł danych z opisowymi metadanymi jest często niepotrzebnym wysiłkiem. Aplikacje klienckie zwykle ignorują opisy przechowywane w źródle danych.
* Tworzenie dokumentacji dla źródeł danych może być trudne i jest ciągłą odpowiedzialnością za synchronizację dokumentacji ze źródłami danych. Użytkownicy nie mogą ufać dokumentacji, która jest postrzegana jako nieaktualna.
* Tworzenie i utrzymywanie dokumentacji źródeł danych to złożony i czasochłonny proces. Częściej nawet większym wyzwaniem jest zapewnienie łatwości dostępu do dokumentacji dla wszystkich użytkowników źródła danych.
* Nieustannym wyzwaniem jest też ograniczanie dostępu do źródeł danych i zapewnienie, że użytkownicy danych wiedzą, w jaki sposób zażądać dostępu do nich.

Gdy takie wyzwania są połączone, stanowią one znaczącą barierę dla firm, które chcą zachęcić i wspierać użycie i zrozumienie danych przedsiębiorstwa.

## <a name="discovery-challenges-for-security-administrators"></a>Wyzwania dotyczące odnajdywania dla administratorów zabezpieczeń

Użytkownicy, którzy są odpowiedzialni za zapewnienie bezpieczeństwa danych organizacji, mogą mieć jakiekolwiek wyzwania wymienione powyżej jako użytkownicy i producenci danych, a także następujące dodatkowe wyzwania:

* Dane organizacji ciągle rośnie, są przechowywane i udostępniane w nowych kierunkach. Zadanie odnajdywania, ochrony i zarządzania danymi poufnymi jest takie, które nigdy nie zostało zakończone. Należy upewnić się, że zawartość organizacji jest udostępniana odpowiednim osobom, aplikacjom i z odpowiednimi uprawnieniami.
* Zrozumienie poziomów ryzyka w danych organizacji wymaga głębokiego wglądu w zawartość, wyszukiwania słów kluczowych, wzorców wyrażeń regularnych i/lub poufnych typów danych. Poufne typy danych mogą obejmować numery kart kredytowych, numery ubezpieczenia społecznego lub numery kont bankowych. Należy stale monitorować wszystkie źródła danych dla poufnej zawartości, ponieważ nawet najmniejsza ilość utraconych danych może być istotna dla organizacji.
* Upewnienie się, że organizacja jest w dalszym ciągu zgodna z firmowymi zasadami zabezpieczeń, jest to trudne zadanie, ponieważ zawartość jest zwiększana i zmienia się, a wymagania i zasady zostały zaktualizowane w celu zmiany ich liczby. Administratorzy zabezpieczeń często mają zapewnioną ochronę danych w szybszym możliwym czasie.

## <a name="azure-purview-advantages"></a>Zalety usługi Azure kontrolą

Usługa Azure kontrolą została zaprojektowana tak, aby rozwiązywać problemy wymienione w poprzednich sekcjach i pomóc przedsiębiorstwom w uzyskaniu największej wartości z istniejących zasobów informacyjnych. Wykaz sprawia, że źródła danych są łatwo odnajdywane i zrozumiałe dla użytkowników, którzy zarządzają danymi.

Usługa Azure kontrolą udostępnia usługę opartą na chmurze, w której można rejestrować źródła danych. Podczas rejestracji dane pozostają w istniejącej lokalizacji, ale kopia jej metadanych jest dodawana do usługi Azure kontrolą wraz z odwołaniem do lokalizacji źródła danych. Te metadane są również indeksowane, aby można było je łatwo odnaleźć za pomocą wyszukiwania oraz uczynić je zrozumiałymi dla użytkowników, którzy je odnajdą.

Po zarejestrowaniu źródła danych możesz wzbogacić swoje metadane. Użytkownik, który zarejestrował źródło danych lub innego użytkownika w przedsiębiorstwie, dodaje metadane. Każdy użytkownik może dodawać adnotacje do źródła danych, dostarczając opisy, Tagi lub inne metadane dotyczące żądania dostępu do źródła danych. Te metadane opisowe uzupełniają metadane strukturalne, takie jak nazwy kolumn i typy danych, zarejestrowane ze źródła danych.

Wykrywanie źródeł danych i ich zrozumienie to główny cel rejestrowania źródeł. Użytkownicy w organizacji mogą potrzebować danych do analizy biznesowej, projektowania aplikacji, analizy danych lub innych zadań, w których wymagane są odpowiednie dane. Wykorzystują one środowisko odnajdywania usługi Data Catalog, aby szybko znaleźć dane, które pasują do swoich potrzeb, zrozumieć dane umożliwiające ocenę przydatności do tego celu i wykorzystać dane przez otwarcie źródła danych w wybranym przez siebie narzędziu.

Jednocześnie użytkownicy mogą wnieść swój wkład do wykazu danych poprzez znakowanie, dokumentowanie i dodawanie adnotacji do zarejestrowanych już źródeł. Mogą także rejestrować nowe źródła danych, które są następnie wykrywane, zrozumiałe i zużywane przez społeczność użytkowników wykazu.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z usługą Azure kontrolą, zobacz [Tworzenie konta usługi Azure kontrolą](create-catalog-portal.md).
