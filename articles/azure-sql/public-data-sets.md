---
title: Publiczne zestawy danych dla usługi Azure Analytics
description: Dowiedz się więcej o publicznych zestawach danych, których można użyć do prototypowania i testowania usług i rozwiązań usługi Azure Analytics.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/01/2018
ms.openlocfilehash: 30f6bf7629152050bc1249a1e22aff0aa30642de
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88209536"
---
# <a name="public-data-sets-for-testing-and-prototyping"></a>Publiczne zestawy danych do testowania i tworzenia prototypów
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

Przejrzyj listę publicznych zestawów danych dla danych, których można użyć do prototypowania i testowania usług i rozwiązań magazynu oraz analiz.

## <a name="us-government-and-agency-data"></a>Dane rządowe Stanów Zjednoczonych i agencji

| Źródło danych | Informacje o danych | Informacje o plikach |
|---|---|---|
| [Dane rządowe Stanów Zjednoczonych](https://catalog.data.gov/dataset) | Ponad 250 000 zbiorów danych obejmujących rolnictwo, klimatyczne, konsumenta, ekosystemy, edukację, energię, finanse, zdrowie, lokalne władze administracyjne, produkcję, morski, ocean, bezpieczeństwo publiczne i naukę oraz badania w Stanach Zjednoczonych | Pliki różnych rozmiarów w różnych formatach, w tym HTML, XML, CSV, JSON, Excel i wiele innych. Można filtrować dostępne zestawy danych według formatu pliku. |
| [Dane spisu USA](https://www.census.gov/data.html) | Dane statystyczne dotyczące populacji w Stanach Zjednoczonych | Zestawy danych są w różnych formatach. |
| [Dane analizy naziemnej z NASA](https://earthdata.nasa.gov/) | Ponad 32 000 zbierania danych obejmujących rolnictwo, atmosfera, biosfery, klimatu, cryosphere, wymiary ludzkie, sfera, Powierzchnia gruntów, oceany, interakcje Sun i inne. | Zestawy danych są w różnych formatach. |
| [Opóźnienia lotu i inne dane transportu](https://www.transtats.bts.gov/OT_Delay/OT_DelayCause1.asp) | "Wydział statystyk transportu w Stanach Zjednoczonych (BTS) jest śledzenie wydajności lotów domowych prowadzonych przez dużych przewoźników lotniczych. Wyświetlane są podsumowanie informacji o liczbie przelotów, opóźnionych, anulowanych i przekierowane loty... w tabelach podsumowujących ogłoszonych w tej witrynie sieci Web ". | Pliki są w formacie CSV. |
| [Ruch Fatalities — system raportowania analizy krytycznej dla Stanów Zjednoczonych (FARS)](https://www.nhtsa.gov/FARS) | "FARS jest spisem krajowym dostarczającym NHTSA, Kongres i amerykańskie dane roczne dotyczące krytycznych urazów poniesionych w przypadku awarii ruchu pojazdów mechanicznych". | "Utwórz własne dane krytyczne w trybie online przy użyciu systemu zapytań FARS. Lub Pobierz wszystkie dane FARS z 1975 do prezentacji z witryny FTP ". |
| [Toksyczne dane chemiczne — dane prognozy toksyczności EPA (ToxCast &trade; )](https://www.epa.gov/chemical-research/toxicity-forecaster-toxcasttm-data) | "Większość aktualizacji EPA, publicznie dostępnych danych toksyczności o wysokiej przepływności na tysiącach chemikaliów. Te dane są generowane za pomocą nakładu pracy badawczej w ToxCast EPA ". | Zestawy danych są dostępne w różnych formatach, w tym w arkuszach kalkulacyjnych, pakietach R i plikach baz danych MySQL. |
| [Toksyczne dane chemiczne — NIH Tox21 Data Challenge 2014](https://tripod.nih.gov/tox21) | "Żądanie Tox21 danych 2014 zostało zaprojektowane, aby pomóc analitykom w zrozumieniu potencjału chemikaliów i związków, które są testowane przez toksykologia w ramach inicjatywy dwudziestego wieku, aby przerwać ścieżki biologiczne w sposób, który może spowodować toksyczne efekty". | Zestawy danych są dostępne w formatach UŚMIECHów i SDF. Dane zapewniają "informacje o działaniach i struktury chemiczne w kolekcji Tox21 ~ 10 000 związki (Tox21 10 tys.)". |
| [Biotechnologia i dane genomu z NCBI](https://www.ncbi.nlm.nih.gov/guide/data-software/) | Wiele zestawów danych obejmujących geny, genomy i białka. | Zestawy danych są w postaci tekstowej, XML, w formatach i innych. Dostępna jest poprzednia aplikacja. |

## <a name="other-statistical-and-scientific-data"></a>Inne dane statystyczne i naukowe

| Źródło danych | Informacje o danych | Informacje o plikach |
|---|---|---|
| [Dane dotyczące taksówki w Nowym Jorku](http://www.nyc.gov/html/tlc/html/about/trip_record_data.shtml) | "Rekordy podróży z taksówką zawierają pola przechwytujące pobieranie i Dropoff daty/godziny, lokalizacje pobierania i Dropoff, odległości podróży, opłaty za elementy, typy stawek, typy płatności i liczby osób zgłaszanych przez sterowniki". | Zestawy danych są w plikach CSV według miesiąca. |
| [Zestawy danych badawczych firmy Microsoft — "Nauka danych do badań"](https://www.microsoft.com/research/academic-program/data-science-microsoft-research/) | Wiele zestawów danych obejmujących interakcje między komputerami, audio/wideo, wyszukiwanie danych/pobieranie informacji, geograficzną i lokalizację, przetwarzanie w języku naturalnym oraz obsługa programu Robotics/Computer. | Zestawy danych są w różnych formatach, spakowane do pobrania. |
| [Otwórz dane analizy danych w chmurze](https://www.opensciencedatacloud.org/projects/) | "Otwarta analiza danych w chmurze umożliwia społeczności naukowej korzystanie z zasobów służących do przechowywania, udostępniania i analizowania petabajtówych zestawów DataSet i skalowania w poziomie."| Zestawy danych są w różnych formatach. |
| [Globalne dane klimatyczne — WorldClim](https://worldclim.org/) | "WorldClim to zestaw globalnych warstw klimatycznych (gridded dane klimatyczne) z rozdzielczością przestrzenną około 1 km2. Te dane mogą służyć do mapowania i modelowania przestrzennego ". | Te pliki zawierają dane geograficzne. Aby uzyskać więcej informacji, zobacz [Format danych](https://worldclim.org/formats1). |
| [Dane dotyczące społeczeństwa ludzkiego — projekt GDELT](https://www.gdeltproject.org/data.html) | "Projekt GDELT to największa, najbardziej kompleksowa i najwyższa otwarta baza danych dla ludzkiego społeczeństwa." | Pliki danych pierwotnych są w formacie CSV. |
| [Anonsowanie kliknij pozycję dane prognozowania dla uczenia maszynowego z Criteo](https://labs.criteo.com/2013/12/download-terabyte-click-logs/) | "Największy wcześniej opublikowany zestaw danych ML". Aby uzyskać więcej informacji, zobacz [Criteo 1 TB kliknij zestaw danych przewidywania](https://blogs.technet.microsoft.com/machinelearning/20../../now-available-on-azure-ml-criteos-1tb-click-prediction-dataset/). | |
| [Zestaw danych wyszukiwania tekstu ClueWeb09 z projektu Lemur](https://www.lemurproject.org/clueweb09.php/) | "Zestaw danych ClueWeb09 został utworzony, aby obsługiwał badania dotyczące pobierania informacji i pokrewnych technologii związanych z językiem ludzkim. Składa się ona z około 1 000 000 000 stron sieci Web w 10 językach, które zostały zebrane w styczniu i lutym 2009. " | Zobacz [Informacje o zestawie danych](https://www.lemurproject.org/clueweb09/datasetInformation.php).|

## <a name="online-service-data"></a>Dane usługi online

| Źródło danych | Informacje o danych | Informacje o plikach |
|---|---|---|
| [Archiwum GitHub](https://www.githubarchive.org/) | "Archiwum GitHub to projekt służący do rejestrowania publicznej osi czasu serwisu GitHub [zdarzenia], archiwizowania go i ułatwienia do dalszej analizy." | Pobierz archiwa zdarzeń kodowane w formacie JSON z klienta sieci Web przy użyciu formatu GZ (gzip). |
| [Dane działania GitHub z projektu GHTorrent](http://ghtorrent.org/) | "Projekt GHTorrent [IS] ma na celu tworzenie skalowalnych, queryableych duplikatów danych w trybie offline oferowanych przez interfejs API REST usługi GitHub. GHTorrent monitoruje wiersz czasu publicznego zdarzenia usługi GitHub. Dla każdego zdarzenia pobiera jego zawartość wraz z ich zależnościami. | Zrzuty baz danych MySQL są w formacie CSV. |
| [Zrzut danych Stack Overflow](https://archive.org/details/stackexchange) | "Jest to zrzut anonimowe całej zawartości współtworzonej przez użytkownika w sieci wymiany na stos [w tym Stack Overflow]." | "Każda lokacja [taka jak Stack Overflow] jest formatowana jako oddzielne archiwum składające się z plików XML, które zostały spakowane za pośrednictwem 7-zip przy użyciu kompresji BZIP2. Każdy archiwum witryn obejmuje wpisy, użytkowników, głosy, komentarze, PostHistory i PostLinks ". |
