---
title: Wprowadzenie do usługi Azure kontrolą (wersja zapoznawcza)
description: Ten artykuł zawiera omówienie usługi Azure kontrolą, w tym jej funkcji i występujących w nim problemów. Usługa Azure kontrolą umożliwia każdemu użytkownikowi rejestrowanie, odnajdowanie i poznawanie źródeł danych oraz korzystanie z nich.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 11/30/2020
ms.openlocfilehash: 3fadfa9d4467209ddbe997afabba27a3752f685d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103419936"
---
# <a name="what-is-azure-purview"></a>Co to jest usługa Azure Purview?

> [!IMPORTANT]
> Usługa Azure kontrolą jest obecnie dostępna w wersji zapoznawczej. Dodatkowe [warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych programu Microsoft Azure to m.in. Postanowienia prawne dotyczące funkcji systemu Azure, które są dostępne w wersjach beta, Preview lub w inny sposób nie są jeszcze ogólnie udostępniane.

Azure kontrolą to ujednolicona usługa zarządzania danymi, która ułatwia zarządzanie lokalnymi, wielochmurowymi i opartymi na usługach (SaaS) danymi. Łatwo twórz całościową, aktualną mapę danych z automatycznym odnajdywaniem danych, poufną klasyfikacją danych i kompleksową składową danych. Pozwalaj konsumentom danych na znalezienie cennych i godnych zaufania danych.

Usługa Azure kontrolą Data map zapewnia podstawę do odnajdywania danych i efektywnego zarządzania danymi. Kontrolą Data map to natywna usługa PaaS w chmurze, która przechwytuje metadane dotyczące danych w przedsiębiorstwie, które znajdują się w środowisku analitycznym i w chmurze. Mapa danych kontrolą jest automatycznie aktualizowana przy użyciu wbudowanego zautomatyzowanego systemu skanowania i klasyfikacji. Użytkownicy biznesowi mogą konfigurować i używać mapy danych kontrolą za pośrednictwem intuicyjnego interfejsu użytkownika, a deweloperzy mogą programistycznie korzystać z mapy danych za pomocą interfejsów API usługi Apache Atlas 2,0.

Usługa Azure kontrolą Data map umożliwia kontrolą Data Catalog i kontrolą Insights w ramach ujednoliconych środowisk w ramach kontrolą Studio.
 
Dzięki kontrolą Data Catalog użytkownikom biznesowym i technicznym mogą szybko & łatwo znaleźć odpowiednie dane przy użyciu funkcji wyszukiwania z filtrami opartymi na różnych kloszach, takich jak terminologia słownika, klasyfikacje, etykietki czułe i inne. W przypadku ekspertów, Stewards danych i funkcjonariuszy, kontrolą Data Catalog zapewnia funkcje nadzoru danych, takie jak zarządzanie słownikiem biznesowym i możliwość automatyzowania tagowania zasobów danych za pomocą terminów słownika. Odbiorcy danych i producenci mogą również wizualnie śledzić zakres zasobów danych zaczynający się od systemów operacyjnych lokalnie, przenosząc & wzbogacania przy użyciu różnych systemów przetwarzania & magazynu danych w chmurze do użycia w systemie analitycznym, takim jak Power BI.

Dzięki kontrolą wglądu w dane, funkcjonariusze danych i funkcjonariusze ds. zabezpieczeń mogą uzyskać wgląd w oczy oraz szybko zrozumieć, jakie dane są aktywnie skanowane, gdzie dane poufne są i jak są zmieniane.

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

## <a name="in-region-data-residency"></a>Zamieszkania danych w regionie
Usługa Azure kontrolą nie przenosi ani nie zapisuje danych klienta z regionu, w którym został wdrożony.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z usługą Azure kontrolą, zobacz [Tworzenie konta usługi Azure kontrolą](create-catalog-portal.md).
