---
title: Wprowadzenie do Azure Data Catalog
description: W tym artykule omówiono usługę Microsoft Azure Data Catalog i jej funkcje oraz problemy, które ona rozwiązuje. Usługa Data Catalog umożliwia każdemu użytkownikowi rejestrowanie, odnajdywanie, zrozumienie i używanie źródeł danych.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: overview
ms.date: 08/01/2019
ms.openlocfilehash: 9b7a5d5ca6cbad897839c8e28c9e3efd1eeabaf0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "68736977"
---
# <a name="what-is-azure-data-catalog"></a>Co to jest usługa Azure Data Catalog?

Azure Data Catalog to w pełni zarządzana usługa w chmurze. Umożliwia użytkownikom odnajdywanie potrzebnych źródeł danych i zrozumienie znalezionych przez nich źródeł danych. Jednocześnie usługa Data Catalog pomaga organizacjom w osiąganiu większych zysków z ich istniejących inwestycji.

Za pomocą usługi Data Catalog każdy użytkownik (analityk, badacz danych lub deweloper) może odnaleźć, zrozumieć i używać źródła danych. Usługa Data Catalog korzysta z crowdsourcingowego modelu metadanych i adnotacji. Jest to centralne miejsce dla wszystkich użytkowników w organizacji, którzy dzielą się swoją wiedzą w celu stworzenia społeczności i uporządkowania danych.

## <a name="discovery-challenges-for-data-consumers"></a>Wyzwania dotyczące odnajdywania dla konsumentów danych

Tradycyjnie wykrywanie firmowych źródeł danych było organicznym procesem opartym na wiedzy pochodzącej z wewnątrz firmy. Przed firmami, które chciały uzyskać najlepsze efekty podczas pracy ze swoimi zasobami informacyjnymi, takie podejście stawia wiele wyzwań:

* Użytkownicy mogą nie wiedzieć, że źródło danych istnieje, chyba że nastąpi kontakt z działem IT w ramach innego procesu. Brak lokalizacji centralnej rejestracji źródła danych.
* Dopóki użytkownicy nie będą znali lokalizacji źródła danych, nie mogą połączyć się z danymi za pomocą aplikacji klienckiej. Podczas korzystania z danych użytkownicy muszą znać parametry połączenia lub ścieżkę.
* Jeśli użytkownicy nie znają lokalizacji dokumentacji źródła danych, nie mogą zrozumieć zamierzonych sposobów użycia danych. Źródła danych i dokumentacja mogą znajdować się w różnych miejscach i być używane w różny sposób.
* Jeśli użytkownicy mają pytania dotyczące zasobu informacyjnego, muszą zlokalizować specjalistę lub zespół, który jest odpowiedzialny za te dane, i skontaktować się z nimi w trybie offline. Nie istnieje jawne połączenie między danymi a ekspertami, którzy mają perspektywę do użycia.
* Jeśli użytkownik nie ma wiedzy na temat procesu żądania dostępu do źródła danych, nawet odnalezienie źródła danych i jego dokumentacji nie pomoże mu w dostępie do danych.

## <a name="discovery-challenges-for-data-producers"></a>Wyzwania dotyczące odnajdywania dla producentów danych

Chociaż konsumenci danych stawiają czoła tym wyżej wymienionym wyzwaniom, przed użytkownikami odpowiedzialnymi za tworzenie i utrzymywanie zasobów informacyjnych stoją inne wyzwania:

* Dodawanie adnotacji do źródeł danych z opisowymi metadanymi jest często niepotrzebnym wysiłkiem. Aplikacje klienckie zwykle ignorują opisy przechowywane w źródle danych.
* Tworzenie dokumentacji dla źródeł danych jest często niepotrzebnym wysiłkiem. Utrzymywanie dokumentacji w synchronizacji ze źródłami danych jest ciągłą odpowiedzialnością. Użytkownicy mogą nie mieć zaufania w dokumentacji, która jest postrzegana jako nieaktualna.
* Tworzenie i utrzymywanie dokumentacji źródeł danych to złożony i czasochłonny proces. Częściej nawet większym wyzwaniem jest zapewnienie łatwości dostępu do dokumentacji dla wszystkich użytkowników źródła danych.
* Nieustannym wyzwaniem jest też ograniczanie dostępu do źródeł danych i zapewnienie, że użytkownicy danych wiedzą, w jaki sposób zażądać dostępu do nich.

Takie wyzwania stanowią dużą przeszkodę dla przedsiębiorstw, które promują używanie danych firmowych i ich zrozumienie.

## <a name="azure-data-catalog-can-help"></a>Usługa Azure Data Catalog może pomóc

Usługa Data Catalog została zaprojektowana w celu rozwiązywania powyższych problemów i umożliwienia przedsiębiorstwom osiągnięcia jak największych korzyści z istniejących zasobów informacyjnych. Usługa Data Catalog ułatwia odnajdywanie źródeł danych i ich zrozumienie przez użytkowników, którzy zarządzają danymi.

Usługa Data Catalog udostępnia usługę w chmurze, w której można zarejestrować źródło danych. Dane pozostają w istniejącej lokalizacji, ale kopia ich metadanych wraz z odwołaniem do lokalizacji źródła danych jest dodawana do usługi Data Catalog. Te metadane są również indeksowane, aby można było je łatwo odnaleźć za pomocą wyszukiwania oraz uczynić je zrozumiałymi dla użytkowników, którzy je odnajdą.

Po zarejestrowaniu źródła danych jego metadane można wzbogacać. Metadane mogą być dodawane przez użytkownika, który zarejestrował go lub przez innych użytkowników w przedsiębiorstwie. Każdy użytkownik może dodawać adnotacje do źródła danych, podając opisy, tagi lub inne metadane, takie jak dokumentacja i procesy służące do żądania dostępu do źródła danych. Te metadane opisowe uzupełniają metadane strukturalne (takie jak nazwy kolumn i typy danych) zarejestrowane ze źródła danych.

Wykrywanie źródeł danych i ich zrozumienie to główny cel rejestrowania źródeł. Użytkownicy w organizacji mogą potrzebować danych do analizy biznesowej, projektowania aplikacji, analizy danych lub innych zadań, w których wymagane są odpowiednie dane. Aby szybko znaleźć odpowiadające ich potrzebom dane, zrozumieć je w celu oceny ich przydatności do tego celu i skorzystać z danych przez otwarcie źródła danych w wybranym narzędziu, mogą użyć środowiska odnajdywania usługi Data Catalog. 

Jednocześnie użytkownicy mogą wnieść swój wkład do wykazu danych poprzez znakowanie, dokumentowanie i dodawanie adnotacji do zarejestrowanych już źródeł. Mogą również rejestrować nowe źródła, które następnie mogą zostać odnalezione, zrozumiane i wykorzystane przez społeczność użytkowników wykazu danych.

![Możliwości usługi Data Catalog](./media/data-catalog-what-is-data-catalog/data-catalog-capabilities.png)

## <a name="learn-more-about-data-catalog"></a>Dowiedz się więcej na temat usługi Data Catalog

Aby dowiedzieć się więcej o możliwościach wykazu danych, zobacz:

* [Jak rejestrować źródła danych](data-catalog-how-to-register.md)
* [Jak odnajdować źródła danych](data-catalog-how-to-discover.md)
* [Jak dodawać adnotacje do źródeł danych](data-catalog-how-to-annotate.md)
* [Jak dokumentować źródła danych](data-catalog-how-to-documentation.md)
* [Jak nawiązywać połączenie ze źródłami danych](data-catalog-how-to-connect.md)
* [Jak pracować z danymi big data](data-catalog-how-to-big-data.md)
* [Jak zarządzać zasobami danych](data-catalog-how-to-manage.md)
* [Jak skonfigurować słownik biznesowy](data-catalog-how-to-business-glossary.md)
* [Często zadawane pytania](data-catalog-frequently-asked-questions.md)

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z Data Catalog:

* [Szybki Start: Tworzenie Azure Data Catalog](data-catalog-get-started.md)
* [Otwórz Azure Data Catalog](https://www.azuredatacatalog.com)