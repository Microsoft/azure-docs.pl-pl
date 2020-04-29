---
title: Typy jednostek — LUIS
titleSuffix: Azure Cognitive Services
description: 'Jednostki wyodrębniają dane z wypowiedź. Typy jednostek umożliwiają przewidywalną wyodrębnianie danych. Istnieją dwa typy jednostek: Uczenie maszynowe i niekomputerowe. Ważne jest, aby wiedzieć, który typ jednostki jest używany w wyrażenia długości.'
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 6ee156efb5512c92d86ba05513b6a2b91df4eae8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79221030"
---
# <a name="entities-and-their-purpose-in-luis"></a>Jednostki i ich cele w LUIS

Głównym celem jednostek jest nadanie aplikacji klienckiej przewidywalnej ekstrakcji danych. _Opcjonalne_, pomocnicze przeznaczenie polega na zwiększeniu przewidywania zamiaru lub innych jednostek z deskryptorami.

Istnieją dwa typy jednostek:

* komputer — z kontekstu
* nieprzeznaczone dla maszyn — dokładne dopasowania tekstu, dopasowania wzorców lub wykrycie przez wstępnie utworzone jednostki

Jednostki zdobyte na maszynie zapewniają szeroką gamę opcji wyodrębniania danych. Jednostki, które nie są poznanie maszynowe, współpracują z dopasowywaniem tekstu i mogą być używane niezależnie lub jako [ograniczenia](#design-entities-for-decomposition) dla jednostki, która jest poznania maszyn.

## <a name="entities-represent-data"></a>Jednostki reprezentują dane

Jednostki to dane, które mają zostać pobrane z wypowiedź, takie jak nazwy, daty, nazwy produktów lub dowolna znacząca Grupa wyrazów. Wypowiedź może zawierać wiele jednostek lub wcale nie. Aby można było wykonać zadanie, aplikacja kliencka _może_ potrzebować danych.

Jednostki muszą mieć spójną etykietę we wszystkich wyrażenia długościach szkoleniowych dla każdego zamiaru w modelu.

 Możesz zdefiniować własne jednostki lub użyć wstępnie utworzonych jednostek, aby zaoszczędzić czas dla typowych koncepcji, takich jak [datetimeV2](luis-reference-prebuilt-datetimev2.md), numer [porządkowy](luis-reference-prebuilt-ordinal.md), [adres e-mail](luis-reference-prebuilt-email.md)i [numery telefonów](luis-reference-prebuilt-phonenumber.md).

|Wypowiedź|Jednostka|Dane|
|--|--|--|
|Kup 3 bilety do Nowego Jorku|Wstępnie zbudowany numer<br>Location. Destination|3<br>Nowy Jork|
|Kup bilet od Nowego Jorku do Londyn w dniu 5 marca|Lokalizacja. Źródło<br>Location. Destination<br>Wstępnie zbudowany datetimeV2|Nowy Jork<br>Londyn<br>5 marca 2018|

### <a name="entities-are-optional"></a>Jednostki są opcjonalne

Podczas gdy wymagane są wymagania, jednostki są opcjonalne. Nie trzeba tworzyć jednostek dla każdej koncepcji w aplikacji, ale tylko dla tych, które są wymagane do podjęcia działania przez aplikację kliencką.

Jeśli wyrażenia długości nie ma danych wymaganych przez aplikację kliencką, nie musisz dodawać jednostek. Podczas opracowywania aplikacji i tworzenia nowego zapotrzebowania na dane można później dodać odpowiednie jednostki do modelu LUIS.

## <a name="entity-compared-to-intent"></a>Jednostka porównana z intencją

Jednostka reprezentuje koncepcję danych w wypowiedź, który ma zostać wyodrębniony.

Element wypowiedź może opcjonalnie zawierać jednostki. Zgodnie z porównaniem _wymagana_ jest prognoza zamiaru wypowiedź i reprezentuje cały Wypowiedź. LUIS wymaga przykładowego wyrażenia długości zawartego w zamierzeniu.

Weź pod uwagę następujące 4 wyrażenia długości:

|Wypowiedź|Przewidywany cel|Wyodrębnione jednostki|Wyjaśnienie|
|--|--|--|--|
|Pomoc|Pomoc|-|Brak elementów do wyodrębnienia.|
|Wyślij coś|sendSomething|-|Brak elementów do wyodrębnienia. Model nie został przeszkolony do wyodrębnienia `something` w tym kontekście i nie ma żadnego odbiorcy.|
|Wyślij Roberta|sendSomething|`Bob`, `present`|Model został przeszkolony z wbudowaną jednostką nazwisko [osoby](luis-reference-prebuilt-person.md) , która wyodrębni nazwę `Bob`. Jednostka poznania maszynowego została użyta do wyodrębnienia `present`.|
|Wyślij Roberta do pudełka czekolady|sendSomething|`Bob`, `box of chocolates`|Dwie ważne fragmenty danych `Bob` i `box of chocolates`, które zostały wyodrębnione według jednostek.|

## <a name="design-entities-for-decomposition"></a>Projektowanie jednostek do dekompozycji

Jest to dobry projekt jednostki, aby uczynić jednostkę najwyższego poziomu daną maszyną. Pozwala to na zmianę projektu jednostki w czasie i użycie **podskładników** (jednostek podrzędnych), opcjonalnie z **ograniczeniami** i **deskryptorami**, aby rozłożyć jednostkę najwyższego poziomu do części wymaganych przez aplikację kliencką.

Projektowanie pod kątem dekompozycji umożliwia LUISom zwrócenie głębokiego stopnia rozpoznawania jednostek do aplikacji klienckiej. Dzięki temu aplikacja kliencka może skupić się na regułach biznesowych i pozostawić dane do LUIS.

### <a name="machine-learned-entities-are-primary-data-collections"></a>Jednostki poznanie maszyn są podstawowymi kolekcjami danych

Obiekty, które są [**poznanie maszyn**](tutorial-machine-learned-entity.md) , są jednostką danych najwyższego poziomu. Podskładniki są jednostkami podrzędnymi jednostek obsługiwanych przez maszynę.

Wyznaczone przez maszynę wyzwalacze jednostki na podstawie kontekstu uzyskanego w ramach szkolenia wyrażenia długości. **Ograniczenia** są opcjonalnymi regułami stosowanymi do jednostki, która jest stosowana przez maszynę, która jest w trakcie dalszych ograniczeń wyzwalanych na podstawie definicji dopasowania tekstu dokładnego, takiego jak [Lista](reference-entity-list.md) lub [wyrażenie regularne](reference-entity-regular-expression.md). Na `size` przykład jednostka uczenia maszynowego może mieć ograniczenie jednostki `sizeList` listy, która ogranicza `size` jednostkę do wyzwalania tylko wtedy, `sizeList` gdy napotkane są wartości zawarte w jednostce.

[**Deskryptory**](luis-concept-feature.md) to funkcje stosowane w celu zwiększenia znaczenia wyrazów lub fraz dla przewidywania. Są one nazywane *deskryptorami* , ponieważ są używane do *opisywania* zamiaru lub jednostki. Deskryptory opisują cechy charakterystyczne lub atrybuty danych, takie jak ważne słowa lub frazy, które LUISją się i poznają.

Gdy tworzysz funkcję listy fraz w aplikacji LUIS, jest ona domyślnie włączona i ma zastosowanie równomiernie we wszystkich intencjach i jednostkach. Jednakże w przypadku zastosowania listy fraz jako deskryptora (funkcji) jednostki (lub *modelu*), a następnie jej zakres ogranicza się do zastosowania tylko do tego modelu i nie jest już używany w przypadku wszystkich innych modeli. Użycie listy fraz jako deskryptora dla modelu ułatwia dekompozycję, pomagając z dokładnością dla modelu, do którego jest stosowana.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>Typy jednostek

Wybierz jednostkę na podstawie sposobu wyodrębniania danych i sposobu ich reprezentowania po wyodrębnieniu.

|Typ jednostki|Przeznaczenie|
|--|--|
|[**Komputer — informacje**](tutorial-machine-learned-entity.md)|Jednostki poznanie maszyn są zgodne z kontekstem w wypowiedź. Nadrzędne grupowanie jednostek, niezależnie od typu jednostki. Sprawia to, że zmiany położenia w przykładzie wyrażenia długości są istotne. |
|[**Staw**](reference-entity-list.md)|Lista elementów i ich synonimy wyodrębnione z **dokładnym dopasowaniem do tekstu**.|
|[**Wzorzec. any**](reference-entity-pattern-any.md)|Obiekt, na którym koniec jednostki jest trudny do określenia. |
|[**Wstępnie utworzonych**](luis-reference-prebuilt-entities.md)|Jest już szkolony do wyodrębniania określonego rodzaju danych, takich jak adres URL lub wiadomość e-mail. Niektóre z tych wstępnie skompilowanych jednostek są zdefiniowane w projekcie typu "Open Source [" — tekst](https://github.com/Microsoft/Recognizers-Text) . Jeśli określona kultura lub jednostka nie jest obecnie obsługiwana, współtworzy projekt.|
|[**Wyrażenie regularne**](reference-entity-regular-expression.md)|Używa wyrażenia regularnego do **dokładnego dopasowania tekstu**.|

## <a name="extracting-contextually-related-data"></a>Wyodrębnianie danych związanych z kontekstem

Wypowiedź może zawierać dwa lub więcej wystąpień jednostki, gdzie znaczenie danych opiera się na kontekście w wypowiedź. Przykładem jest wypowiedź do zarezerwowania lotu, który ma dwie lokalizacje, źródło i miejsce docelowe.

`Book a flight from Seattle to Cairo`

Dwa przykłady `location` jednostki muszą zostać wyodrębnione. Klient-aplikacja musi znać typ lokalizacji dla każdej z nich, aby zakończyć zakupy biletów.

Istnieją dwie techniki wyodrębniania danych kontekstowych:

 * `location` Jednostka jest jednostką wydaną przez maszynę i używa dwóch jednostek podskładników do przechwytywania `origin` i `destination` (preferowany)
 * Jednostka używa dwóch **ról** z `origin` `location``destination`

W wypowiedź może istnieć wiele jednostek i można go wyodrębnić bez użycia dekompozycji lub ról, jeśli kontekst, w którym są używane, nie ma znaczenia. Na przykład jeśli wypowiedź zawiera listę lokalizacji, jest to lista `I want to travel to Seattle, Cairo, and London.`, w której każdy element nie ma dodatkowego znaczenia.

### <a name="using-subcomponent-entities-of-a-machine-learned-entity-to-define-context"></a>Używanie jednostek podskładnika jednostki, która jest poznania maszynowego do definiowania kontekstu

Można użyć [**jednostki uczenia maszynowego**](tutorial-machine-learned-entity.md) , aby wyodrębnić dane opisujące akcję założenia, a następnie rozłożyć jednostkę najwyższego poziomu do oddzielnych części wymaganych przez aplikację kliencką.

W tym przykładzie `Book a flight from Seattle to Cairo`jednostka najwyższego poziomu może być `travelAction` oznaczona etykietą do wyodrębnienia. `flight from Seattle to Cairo` Następnie są tworzone dwa jednostki podskładnikowe, `origin` wywoływane `destination`i, z ograniczeniami zastosowanymi wstępnie skompilowanej `geographyV2` jednostki. W wyrażenia długości szkoleniowym `origin` i `destination` są odpowiednio oznaczone.

### <a name="using-entity-role-to-define-context"></a>Używanie roli jednostki do definiowania kontekstu

Rola jest aliasem nazwanym dla jednostki na podstawie kontekstu w wypowiedź. Roli można używać z dowolnym prekompilowanym lub niestandardowym typem jednostki i używanym w obu przykładach wyrażenia długości i wzorców. W tym przykładzie `location` jednostka musi mieć dwie role z `origin` i `destination` i oba muszą być oznaczone w przykładzie wyrażenia długości.

Jeśli LUIS odnajdzie element `location` , ale nie może ustalić roli, jednostka lokalizacji jest nadal zwracana. Aplikacja kliencka będzie musiała postępować zgodnie z pytaniem, aby określić, który typ lokalizacji należy do użytkownika.


## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Jeśli potrzebujesz więcej niż maksymalnej liczby jednostek

Jeśli potrzebujesz więcej niż limit, skontaktuj się z pomocą techniczną. Aby to zrobić, Zbierz szczegółowe informacje o systemie, przejdź do witryny sieci Web [Luis](luis-reference-regions.md#luis-website) , a następnie wybierz pozycję **Pomoc techniczna**. Jeśli Twoja subskrypcja platformy Azure obejmuje usługi pomocy technicznej, skontaktuj się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/options/).

## <a name="entity-prediction-status"></a>Stan przewidywania jednostek

Portal LUIS pokazuje, kiedy jednostka, w przykładzie wypowiedź, ma inne przewidywania jednostek niż wybrana przez Ciebie jednostka. Ten różny wynik jest oparty na bieżącym przeszkolonym modelu.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z pojęciami dotyczącymi dobrego [wyrażenia długości](luis-concept-utterance.md).

Zobacz [Dodawanie jednostek](luis-how-to-add-entities.md) , aby dowiedzieć się więcej na temat dodawania jednostek do aplikacji Luis.

Zapoznaj się z [samouczkiem: Wyodrębnij dane strukturalne z wypowiedź użytkownika z jednostkami maszynowymi w Language Understanding (Luis)](tutorial-machine-learned-entity.md) , aby dowiedzieć się, jak wyodrębnić dane strukturalne z wypowiedź przy użyciu jednostki, która jest poznania maszyny.
 
