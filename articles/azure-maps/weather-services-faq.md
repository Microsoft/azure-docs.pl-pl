---
title: Microsoft Azure Maps usługi Pogoda (wersja zapoznawcza) — często zadawane pytania
description: Znajdź odpowiedzi na często zadawane pytania dotyczące danych i funkcji usług pogody Azure Maps (wersja zapoznawcza).
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 9c8e971b4fda313ffede58455dd6d057d6848ce4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98678133"
---
# <a name="azure-maps-weather-services-preview-frequently-asked-questions-faq"></a>Azure Maps usługi pogodowe (wersja zapoznawcza) — często zadawane pytania

> [!IMPORTANT]
> Usługi Azure Maps Pogoda są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące danych i funkcji [usług pogoda](/rest/api/maps/weather) Azure Maps. Omówiono następujące tematy:

* Źródła danych i modele danych
* Pokrycie i dostępność usług pogodowych
* Częstotliwość aktualizacji danych
* Programowanie przy użyciu zestawów SDK Azure Maps
* Opcje wizualizacji danych pogodowych, w tym integracja z programem Microsoft Power BI

## <a name="data-sources-and-data-models"></a>Źródła danych i modele danych

**Jak Azure Maps źródła danych pogody?**

Azure Maps jest opracowana z myślą o współpracy partnerów technologicznych w zakresie mobilności i lokalizacji, w tym AccuWeather, którzy dostarczają podstawowe dane pogodowe. Aby zapoznać się z ogłoszeniem Azure Maps "współpracy z usługą AccuWeather, zobacz" [deszcze "i" usługa Azure Maps Pogoda "spowoduje przeprowadzenie wglądu w dane przedsiębiorstwa](https://azure.microsoft.com/blog/rain-or-shine-azure-maps-weather-services-will-bring-insights-to-your-enterprise/).

AccuWeather mają informacje o pogodzie i środowisku w czasie rzeczywistym dostępne w dowolnym miejscu na świecie, z powodu ich partnerstwa z wieloma krajowymi agencjami pogody i innymi obowiązującymi przepisami. Lista tych informacji jest dostępna poniżej.

* Publicznie dostępne globalne obserwacje powierzchni instytucji rządowych
* Własnościowe obserwacje powierzchni od rządów i firm prywatnych
* Dane radaru o wysokiej rozdzielczości dla ponad 40 krajów/regionów
* Najlepsze w swojej klasie dane globalne piorunów w czasie rzeczywistym
* Ostrzeżenia o wydawaniu pogody przez rząd dla ponad 60 krajów/regionów i terytoriów
* Dane satelitarne ze satelitarnych satelitów pogodowych obejmujących cały świat
* Ponad 150 liczbowych modeli prognoz, w tym wewnętrznych, własnościowego modelowania, modeli rządowych, takich jak globalny system prognoz (GFS), i unikatowych modeli downscaled dostarczonych przez firmy prywatne
* Obserwacje jakości powietrza
* Obserwacje z działów transportu

Dziesiątki tysięcy obserwacji powierzchni, wraz z innymi danymi, są dołączone do tworzenia i wywierania wpływu na bieżące warunki udostępniane użytkownikom. Obejmuje to nie tylko dostępne, standardowe zestawy danych, ale również unikatowe obserwacje uzyskane z krajowych usług meteorologicznych w wielu krajach/regionach, w tym Indie, Brazylia i Kanada oraz inne własnościowe dane wejściowe. Te unikatowe zestawy danych zwiększają przestrzenne i bezproblemowe rozpoznawanie bieżącego stanu dla naszych użytkowników. 

Te zestawy danych są przeglądane w czasie rzeczywistym w celu uzyskania dokładności dla systemu prognoz cyfrowych, który wykorzystuje AccuWeathere sztuczne algorytmy analizy, aby ciągle modyfikować prognozy, gwarantując, że zawsze zawierają najnowsze dane, a tym samym maksymalizując ich ciągłą dokładność.

**Jakie modele tworzą dane prognozy pogody?**

Wiele systemów wskazówek dotyczących prognoz pogodowych jest wykorzystywanych do formułowania prognoz globalnych. Ponad 150 liczbowych modeli prognoz jest używanych każdego dnia, zarówno zewnętrznych, jak i wewnętrznych zestawów danych. Dotyczy to również modeli instytucji rządowych, takich jak Europejski centrum ECMWF oraz globalny system prognozowania (GFS). Ponadto AccuWeather obejmuje własnościowe modele o wysokiej rozdzielczości, które skalować prognoz do określonych lokalizacji i strategicznych domen regionalnych, aby przewidzieć Pogoda z dokładnością. AccuWeather unikatowe algorytmy mieszania i ważenia w ciągu ostatnich kilku dekad. Algorytmy te optymalnie wykorzystują wiele danych wejściowych prognoz, aby zapewnić wysoce dokładne prognozy.

## <a name="weather-services-preview-coverage-and-availability"></a>Pokrycie i dostępność usług pogody (wersja zapoznawcza)

**Jakiego rodzaju pokrycie może oczekiwać w różnych krajach/regionach?**

Pokrycie usług pogodowych różni się w zależności od kraju/regionu. Wszystkie funkcje nie są dostępne w każdym kraju/regionie. Aby uzyskać więcej informacji, zobacz [dokumentację dotyczącą pokrycia](./weather-coverage.md).

## <a name="data-update-frequency"></a>Częstotliwość aktualizacji danych

**Jak często są aktualizowane dane dotyczące bieżących warunków?**

Dane dotyczące bieżących warunków są aktualizowane co najmniej raz na godzinę, ale można je aktualizować częściej i szybko zmieniać warunki — na przykład zmiany dotyczące dużej temperatury, zmiany warunków i opadów, zmiany wytrącania i tak dalej. Większość stacji obserwacyjnych na całym świecie raportuje wiele razy na godzinę w przypadku zmiany warunków. Jednak niektóre obszary będą nadal aktualizowane tylko raz, dwa razy lub cztery razy na godzinę w zaplanowanych odstępach czasu.  

Azure Maps buforuje dane bieżących warunków przez maksymalnie 10 minut, aby ułatwić przechwytywanie częstotliwości aktualizacji danych w czasie rzeczywistym. Aby zobaczyć, kiedy wygasa buforowana odpowiedź i unikaj wyświetlania nieaktualnych danych, można wykorzystać informacje nagłówka wygaśnięcia w nagłówku HTTP odpowiedzi interfejsu API Azure Maps.

**Jak często codziennie i co godzinę dane prognozy są aktualizowane?**

Codziennie i co godzinę dane prognozy są aktualizowane wiele razy dziennie, ponieważ odbierane są zaktualizowane uwagi.  Na przykład w przypadku przekroczenia prognozowanej wysokiej/niskiej temperatury dane prognozy zostaną dopasowane w następnym cyklu aktualizacji. Może się to zdarzyć w różnych interwałach, ale zazwyczaj odbywa się w ciągu godziny. Wiele nagłych warunków pogodowych może spowodować zmianę danych prognozy. Na przykład w gorącej południu, izolowany Thunderstorm może nagle się pojawić, dzięki czemu można uzyskać duże pokrycie w chmurze i opady. Izolowana burza może efektywnie porzucić temperaturę o 10 stopni. Ta nowa wartość temperatury będzie miała wpływ na godziny i dzienne prognozy dla pozostałej części dnia, a w związku z tym będzie aktualizowana w naszych zestawach danych.

Interfejsy API prognozowania Azure Maps są buforowane przez maksymalnie 30 minut. Aby zobaczyć, kiedy wygasa buforowana odpowiedź i unikaj wyświetlania nieaktualnych danych, można wykorzystać informacje nagłówka wygaśnięcia w nagłówku HTTP odpowiedzi interfejsu API Azure Maps. Zalecamy aktualizację w miarę potrzeb w zależności od przypadku użycia i interfejsu użytkownika określonego produktu.

## <a name="developing-with-azure-maps-sdks"></a>Programowanie przy użyciu zestawów SDK Azure Maps

**Czy Azure Maps zestaw SDK sieci Web natywnie obsługuje integrację usług pogody (wersja zapoznawcza)?**

Zestaw SDK sieci Web Azure Maps zawiera moduł usług. Moduł usług jest biblioteką pomocniczą, która ułatwia korzystanie z Azure Maps usług REST w aplikacjach sieci Web lub Node.js. za pomocą języka JavaScript lub TypeScript. Aby rozpocząć, zapoznaj się z naszą [dokumentacją](./how-to-use-services-module.md).

**Czy Azure Maps Android SDK natywnie obsługiwać integrację usług pogodowych (wersja zapoznawcza)?**

Azure Maps zestaw SDK systemu Android obsługuje warstwy kafelków Merkatora, które mogą mieć notację x/y/Powiększ, przełożonej notację klucza lub notację pola EPSG 3857.

Planujemy utworzenie modułu usług dla środowiska Java/Android podobnego do modułu zestawu SDK sieci Web. Moduł usług systemu Android ułatwi dostęp do wszystkich usług Azure Maps w aplikacji Java lub Android.  

## <a name="data-visualizations"></a>Wizualizacja danych  

**Czy Azure Maps Power BI obsługi wizualizacji Azure Maps kafelków pogody?**

Tak. Aby dowiedzieć się, jak migrować kafelki satelitarne radaru i podczerwieni do wizualizacji Power BI firmy Microsoft, zobacz [Dodawanie warstwy kafelków do Power BI wizualizacji](./power-bi-visual-add-tile-layer.md). 

**Jak mogę Interpretuj kolory używane dla kafelków radarowych i satelitarnych?**

Artykuł dotyczący [koncepcji Azure Maps Pogoda](./weather-services-concepts.md#radar-and-satellite-imagery-color-scale) zawiera przewodnik ułatwiający interpretację kolorów używanych na potrzeby kafelków radarowych i satelitarnych. W artykule omówiono przykłady kolorów i kody koloru SZESNASTKOWego.
 
**Czy mogę tworzyć animacje kafelków radarowych i satelitarnych?**

Tak. Oprócz kafelków radarowych i satelitarnych, Azure Maps klienci mogą żądać wcześniejszych i przyszłych kafelków, aby usprawnić wizualizacje danych za pomocą nakładki mapy. Można to zrobić przez bezpośrednie wywołanie [interfejsu API Get map kafelka v2](/rest/api/maps/renderv2/getmaptilepreview) lub zażądanie kafelków za pośrednictwem Azure Maps Web SDK. Kafelki radarowe są dostępne przez maksymalnie 1,5 godzin w przeszłości i przez maksymalnie 2 godziny w przyszłości. Kafelki i są dostępne w 5-minutowych interwałach. Kafelki podczerwieni są udostępniane przez maksymalnie 3 godziny w przeszłości i są dostępne w 10-minutowych interwałach. Aby uzyskać więcej informacji, zobacz [przykład kodu](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Animated%20tile%20layer)animacji "open source" kafelka Pogoda.  

**Czy są oferowane ikony różnych warunków pogodowych?**

Tak. W [tym miejscu](./weather-services-concepts.md#weather-icons)można znaleźć ikony i odpowiednie kody. Należy zauważyć, że tylko niektóre interfejsy API usługi Pogoda (wersja zapoznawcza), takie jak  [pobieranie bieżących warunków API](/rest/api/maps/weather/getcurrentconditionspreview), zwracają *iconCode* w odpowiedzi. Aby uzyskać więcej informacji, zobacz bieżący [przykład kodu](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Get%20current%20weather%20at%20a%20location)Open Source WeatherConditions.

## <a name="next-steps"></a>Następne kroki

Jeśli te często zadawane pytania nie odpowiedzą na Twoje pytanie, możesz skontaktować się z nami za pomocą następujących kanałów (w kolejności eskalacji):

* Sekcja komentarzy w tym artykule.
* [MSFT Q&stronę Azure Maps](/answers/topics/azure-maps.html).
* pomoc techniczna firmy Microsoft. Aby utworzyć nowe żądanie obsługi, w [Azure Portal](https://portal.azure.com/)na karcie Pomoc wybierz przycisk **Pomoc i** obsługa techniczna, a następnie wybierz pozycję **nowe żądanie obsługi**.
* [Azure Maps UserVoice](https://feedback.azure.com/forums/909172-azure-maps) , aby przesłać żądania funkcji.

Dowiedz się, jak żądać danych pogodowych w czasie rzeczywistym i prognozowanych przy użyciu Azure Maps usług pogodowych (wersja zapoznawcza):
> [!div class="nextstepaction"]
> [Żądaj danych pogodowych w czasie rzeczywistym ](how-to-request-weather-data.md)

Artykuł pojęcia dotyczące Azure Maps usług pogodowych (wersja zapoznawcza):
> [!div class="nextstepaction"]
> [Pojęcia dotyczące usług pogodowych](weather-coverage.md)

Poznaj dokumentację interfejsu API usługi Azure Maps Pogoda (wersja zapoznawcza):

> [!div class="nextstepaction"]
> [Usługi pogodowe w usłudze Azure Maps](/rest/api/maps/weather)