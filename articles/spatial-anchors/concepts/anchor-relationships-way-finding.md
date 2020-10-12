---
title: Relacje zakotwiczeń i znajdowanie drogi
description: Zapoznaj się z modelem koncepcyjnym związanym z relacjami zakotwiczenia. Dowiedz się, jak połączyć kotwice w miejscu i użyć interfejsu API w pobliżu do realizacji scenariusza szukania.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 13c85dff40e266287d893d9e45b32a66a33f027e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83006016"
---
# <a name="anchor-relationships-and-way-finding-in-azure-spatial-anchors"></a>Relacje zakotwiczenia i sposób znajdowania w kotwicach przestrzennych platformy Azure

Przy użyciu relacji zakotwiczenia można utworzyć połączone kotwice w miejscu, a następnie zadawać pytania podobne do następujących:

* Czy istnieją kotwice w pobliżu?
* Jak daleko jest?

## <a name="examples"></a>Przykłady

W takich przypadkach można używać połączonych kotwic, takich jak:

* Proces roboczy musi wykonać zadanie, które obejmuje odwiedzanie różnych lokalizacji w fabryce przemysłowej. Fabryka ma kotwice przestrzenne w każdej lokalizacji. Aplikacja HoloLens lub Mobile ułatwia proces roboczy z jednej lokalizacji do następnego. Aplikacja najpierw pyta o kotwice znajdujące się w pobliżu, a następnie prowadzi proces roboczy do następnej lokalizacji. Aplikacja wizualnie pokazuje ogólny kierunek i odległość do następnej lokalizacji.

* Muzeów tworzy kotwice przestrzenne na publicznych ekranach. Razem te kotwice tworzą jednogodzinną prezentację najważniejszych publicznych ekranów muzeów. Na publicznym ekranie odwiedzający mogą otworzyć aplikację rzeczywistości mieszanej muzeów na urządzeniu przenośnym. Następnie wskażą one kamerę telefoniczną wokół miejsca, aby zobaczyć ogólny kierunek i odległość do innych publicznych wyświetlaczy w przewodniku. Gdy użytkownik przegląda się w kierunku wyświetlania publicznego, aplikacja aktualizuje ogólny kierunek i odległość, aby pomóc użytkownikowi.

## <a name="set-up-way-finding"></a>Konfigurowanie sposobu wyszukiwania

Aplikacja, która używa kierunku i odległości między zakotwiczeniami, aby zapewnić wskazówki, w *jaki sposób można znaleźć*. Sposób znajdowania różni się od przełączania nawigacji. W obszarze Nawigacja z przełączaniem użytkownicy prowadzą do ścian, za pomocą drzwi i między piętrami. Dzięki sposobom wyszukiwania użytkownik otrzymuje wskazówki dotyczące ogólnego kierunku lokalizacji docelowej. Jednak wnioskowanie lub znajomość miejsca również ułatwia użytkownikowi przechodzenie przez strukturę do miejsca docelowego.

Aby skompilować sposób znalezienia środowiska, należy najpierw przygotować miejsce na środowisko i opracować aplikację, z którą użytkownicy będą pracować. Oto podstawowe kroki:

1. **Zaplanuj miejsce**: Zdecyduj, które lokalizacje w tym miejscu będą częścią środowiska wyszukiwania. W naszych scenariuszach Nadzorca fabryki lub Koordynator samouczka muzeów może zdecydować, które lokalizacje mają być uwzględnione w środowisku wyszukiwania.
2. **Zakotwiczenia połączeń**: Przejdź do wybranych lokalizacji, aby utworzyć kotwice przestrzenne. Można to zrobić w trybie administratora aplikacji użytkownika końcowego lub innej aplikacji. Nastąpi połączenie lub powiąże się z poszczególnymi zakotwiczeniami. Usługa zachowuje te relacje.
3. **Uruchom środowisko użytkownika końcowego**: użytkownicy uruchamiają aplikację, aby zlokalizować kotwicę, która może znajdować się w jednej z wybranych lokalizacji. Ogólny projekt powinien określać lokalizacje, w których użytkownicy mogą wprowadzać te czynności.
4. **Znajdź kotwice w pobliżu**: po znalezieniu kotwicy przez użytkownika aplikacja może zażądać zakotwiczenia w pobliżu. Ta procedura zwraca ułożenie między urządzeniem a tymi kotwicami.
5. **Przewodnik użytkownika**: aplikacja może użyć ułożenia do każdej z tych kotwic, aby uzyskać wskazówki dotyczące ogólnego kierunku i drogi użytkownika. Na przykład kanał informacyjny kamery w aplikacji może zawierać ikonę i strzałkę reprezentującą każde potencjalne miejsce docelowe, jak pokazano na poniższej ilustracji.
6. **Uściślij wskazówki**: w miarę przeprowadzania przez użytkownika aplikacja może okresowo obliczyć nowe ułożenie między urządzeniem a zakotwiczeniem docelowym. Aplikacja w dalszym ciągu udoskonala wskazówki, które pomagają użytkownikowi dotrzeć do miejsca docelowego.

    ![Przykład sposobu, w jaki aplikacja może wyświetlać wskazówki dotyczące sposobu szukania](./media/meeting-spot.png)

## <a name="connect-anchors"></a>Połącz kotwice

Aby skompilować środowisko szukania, należy najpierw umieścić kotwice w wybranych lokalizacjach. W tej sekcji przyjęto założenie, że administrator aplikacji zakończył już tę pracę.

### <a name="connect-anchors-in-a-single-session"></a>Połącz kotwice w pojedynczej sesji

Aby połączyć kotwice:

1. Zapoznaj się z pierwszą lokalizacją i Utwórz kotwicę A za pomocą CloudSpatialAnchorSession.
2. Przechodzenie do drugiej lokalizacji. Podstawowa platforma MR/AR śledzi przemieszczenie.
3. Utwórz kotwicę B przy użyciu tego samego CloudSpatialAnchorSession. Kotwice A i B są teraz połączone. Usługa kotwice przestrzenne utrzymuje tę relację.
4. Kontynuuj procedurę dla pozostałych kotwic.

### <a name="connect-anchors-in-multiple-sessions"></a>Połącz kotwice w wielu sesjach

Można połączyć kotwice przestrzenne z wieloma sesjami. Korzystając z tej metody, można utworzyć i połączyć niektóre kotwice naraz, a następnie utworzyć i połączyć więcej kotwic.

Aby połączyć kotwice dla wielu sesji:

1. Aplikacja tworzy niektóre kotwice w jednym CloudSpatialAnchorSession.
2. W innym czasie aplikacja lokalizuje jedną z tych kotwic (na przykład zakotwiczenie A) przy użyciu nowego CloudSpatialAnchorSession.
3. Przechodzenie do nowej lokalizacji. Podstawowa platforma w rzeczywistości mieszanej lub rozszerzonej rzeczywistości śledzi ruch.
4. Utwórz kotwicę C przy użyciu tego samego CloudSpatialAnchorSession. Kotwice A, B i C są teraz połączone. Usługa kotwice przestrzenne utrzymuje tę relację.

Możesz kontynuować tę procedurę, aby uzyskać więcej kotwic i więcej sesji w czasie.

### <a name="verify-anchor-connections"></a>Weryfikuj połączenia zakotwiczenia

Aplikacja może sprawdzić, czy dwie kotwice są połączone przez wystawienie zapytania dla zakotwiczenia w pobliżu, przez ustawienie `NearAnchorCriteria` na `CloudSpatialAnchorWatcher` . Gdy wynik zapytania zawiera docelową kotwicę, połączenie zakotwiczenia zostanie zweryfikowane. Jeśli kotwice nie są połączone, aplikacja może spróbować połączyć je ponownie.

Oto kilka powodów, dla których kotwice mogą się nie powieść:

* Podstawowa platforma mieszana lub większa-rzeczywistość ma utracone śledzenie podczas procesu łączenia kotwic.
* Ze względu na błąd sieciowy podczas komunikacji z usługą kotwice przestrzenne nie można utrwalić połączenia zakotwiczonego.

### <a name="find-sample-code"></a>Znajdowanie przykładowego kodu

Aby znaleźć przykładowy kod, który pokazuje, jak połączyć kotwice i wykonywać zapytania w pobliżu, zobacz [kotwice przestrzenne — przykładowe aplikacje](https://github.com/Azure/azure-spatial-anchors-samples).
