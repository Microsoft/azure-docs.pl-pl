---
title: Struktury danych usług mobilności (wersja zapoznawcza) w Microsoft Azure Maps
description: Dowiedz się, jak dane są zorganizowane w obszary Metro w Azure Maps usługach mobilności (wersja zapoznawcza). Zobacz, w których polach są przechowywane informacje o zatrzymaniu i wierszach tranzytu publicznego.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8ffedc18ba331733723a6293756b60b733cc32cf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96904724"
---
# <a name="data-structures-in-azure-maps-mobility-services-preview"></a>Struktury danych w Azure Maps usługach mobilności (wersja zapoznawcza) 

> [!IMPORTANT]
> Usługi mobilności Azure Maps są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



W tym artykule wprowadzono koncepcję obszaru Metro w [Azure Maps usługach mobilności](/rest/api/maps/mobility). Omawiamy niektóre typowe pola, które są zwracane, gdy ta usługa jest przedmiotem zapytania o zatrzymanie i obsługę tranzytu publicznego. Zalecamy odczytywanie tego artykułu przed programowaniem przy użyciu interfejsów API usług mobilności.

## <a name="metro-area"></a>Obszar Metro

Dane usług mobilności (wersja zapoznawcza) są pogrupowane według obsługiwanych obszarów Metro. Obszary Metro nie obserwują granic miejscowości. Obszar Metro może zawierać wiele miast, gęsto wypełnione miasto i otaczające miasta. W rzeczywistości kraj/region może być jednym obszarem Metro. 

`metroID`Jest to identyfikator obszaru Metro, którego można użyć do wywołania [interfejsu API informacji o powierzchni Metro](/rest/api/maps/mobility/getmetroareainfopreview). Użyj Azure Maps "" Get Metro ", aby zażądać typów tranzytowych, agencji tranzytowych, aktywnych alertów i dodatkowych szczegółów dla wybranej linii metra. Możesz również zażądać obsługiwanych obszarów Metro i metroIDs. Identyfikatory obszaru Metro mogą ulec zmianie.

**metroID:** 522   **Nazwa:** Seattle-Tacoma-Bellevue

![Seattle — obszar Metro](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>Zatrzymywanie identyfikatorów

Zatrzymywanie tranzytu może być określane przez dwa typy identyfikatorów, identyfikator [GFTS (Generally exportion Specification)](http://gtfs.org/) i identyfikator zatrzymania Azure Maps. Identyfikator GFTS jest określany jako stopKey, a Azure Maps identyfikator zatrzymania jest określany jako stopID. Podczas częstego odwoływania się do zatrzymywania tranzytu zaleca się użycie identyfikatora zatrzymania Azure Maps. stopID jest bardziej stabilna i prawdopodobnie pozostaje taka sama, o ile istnieje fizyczne zatrzymanie. Identyfikator zatrzymania GTFS jest aktualizowany częściej. Na przykład identyfikator zatrzymania GTFS można zaktualizować na żądanie dostawcy GTFS lub w przypadku wydania nowej wersji GTFS. Mimo że fizyczne zatrzymanie nie miało żadnej zmiany, identyfikator zatrzymania GTFS może ulec zmianie.

Aby rozpocząć, możesz zażądać zatrzymania tranzytu w pobliżu za pomocą [interfejsu API pobierania pobliskich tranzytów](/rest/api/maps/mobility/getnearbytransitpreview).

## <a name="line-groups-and-lines"></a>Wiersze i grupy wierszy

Usługi mobilności (wersja zapoznawcza) używają Parallel model danych dla linii i grup liniowych. Ten model jest używany do lepszego postępowania z zmianami dziedziczonymi z tras [GTFS](http://gtfs.org/) i danych podróży.


### <a name="line-groups"></a>Grupy wierszy

Grupa wierszy jest jednostką, która grupuje ze sobą wszystkie wiersze, które są logicznie częścią tej samej grupy. Zwykle grupa wierszy zawiera dwa wiersze, jeden od punktu A do B, a druga zwraca od punktu B do A. Oba wiersze powinny należeć do tej samej publicznej agencji transportowej i mieć ten sam numer wiersza. Mogą jednak wystąpić sytuacje, w których grupa linii ma więcej niż dwa wiersze lub tylko jeden wiersz.


### <a name="lines"></a>Linie

Jak opisano powyżej, każda grupa wierszy składa się z zestawu wierszy. Każda grupa wierszy składa się z dwóch wierszy, a każdy wiersz opisuje kierunek.  Istnieją jednak przypadki, w których więcej wierszy redaguje grupę wierszy. Na przykład jest to linia, która czasami zawiera kilka przewodników w określonym klubie i czasami nie jest. W obu przypadkach działa pod tym samym numerem wiersza. Ponadto grupa wierszy może składać się z jednego wiersza. Cykliczna linia z pojedynczym kierunkiem jest grupą z jednym wierszem.

Aby rozpocząć, możesz zażądać grup wierszy przy użyciu [interfejsu API uzyskiwania linii tranzytowej](/rest/api/maps/mobility/gettransitlineinfopreview).


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak żądać danych tranzytowych przy użyciu usług mobilności (wersja zapoznawcza):

> [!div class="nextstepaction"]
> [Jak żądać danych tranzytowych](how-to-request-transit-data.md)

Informacje na temat żądania danych w czasie rzeczywistym przy użyciu usług mobilności (wersja zapoznawcza):

> [!div class="nextstepaction"]
> [Jak żądać danych w czasie rzeczywistym](how-to-request-real-time-data.md)

Poznaj dokumentację interfejsu API usługi Azure Maps Mobility Services (wersja zapoznawcza)

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API usług mobilności](/rest/api/maps/mobility)