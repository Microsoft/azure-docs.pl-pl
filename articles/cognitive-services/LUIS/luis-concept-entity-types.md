---
title: Typy jednostek — LUIS
description: Jednostka wyodrębnia dane z wypowiedź użytkownika w środowisku uruchomieniowym przewidywania. _Opcjonalne_, pomocnicze przeznaczenie ma na celu zwiększenie przewidywania zamiaru lub innych jednostek przy użyciu jednostki jako funkcji.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 398d18642052726af4d4920443bad515ec0b5bef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316566"
---
# <a name="extract-data-with-entities"></a>Wyodrębnij dane przy użyciu jednostek

Jednostka wyodrębnia dane z wypowiedź użytkownika w środowisku uruchomieniowym przewidywania. _Opcjonalne_, pomocnicze przeznaczenie ma na celu zwiększenie przewidywania zamiaru lub innych jednostek przy użyciu jednostki jako funkcji.

Istnieje kilka typów jednostek:

* [Jednostka uczenia maszynowego](reference-entity-machine-learned-entity.md) — jest to jednostka podstawowa. Przed rozpoczęciem korzystania z innych jednostek należy zaprojektować schemat przy użyciu tego typu jednostki.
* Niemachine-uczenie używane jako wymagana [Funkcja](luis-concept-feature.md) — dla dokładnego dopasowania tekstu, dopasowania wzorców lub wykrycia przez wstępnie utworzone jednostki
* [Wzorzec. any](#patternany-entity) -do wyodrębniania tekstu dowolnego formularza, takiego jak tytuły książek ze [wzorca](reference-entity-pattern-any.md)

jednostki uczenia maszynowego zapewniają szeroką gamę opcji wyodrębniania danych. Obiekty inne niż Uczenie maszynowe współpracują z dopasowywaniem tekstu i są używane jako [funkcje wymagane](#design-entities-for-decomposition) dla obiektu lub zamiaru uczenia maszynowego.

## <a name="entities-represent-data"></a>Jednostki reprezentują dane

Jednostki to dane, które mają zostać pobrane z wypowiedź, takie jak nazwy, daty, nazwy produktów lub dowolna znacząca Grupa wyrazów. Wypowiedź może zawierać wiele jednostek lub wcale nie. Aby można było wykonać zadanie, aplikacja kliencka _może_ potrzebować danych.

Jednostki muszą mieć spójną etykietę we wszystkich wyrażenia długościach szkoleniowych dla każdego zamiaru w modelu.

 Możesz zdefiniować własne jednostki lub użyć wstępnie utworzonych jednostek, aby zaoszczędzić czas dla typowych koncepcji, takich jak [datetimeV2](luis-reference-prebuilt-datetimev2.md), numer [porządkowy](luis-reference-prebuilt-ordinal.md), [adres e-mail](luis-reference-prebuilt-email.md)i [numery telefonów](luis-reference-prebuilt-phonenumber.md).

|Wypowiedź|Jednostka|Dane|
|--|--|--|
|Kup 3 bilety do Nowego Jorku|Wstępnie zbudowany numer<br>Element docelowy|3<br>Nowy Jork|


### <a name="entities-are-optional-but-recommended"></a>Jednostki są opcjonalne, ale zalecane

Podczas [gdy](luis-concept-intent.md) wymagane są wymagania, jednostki są opcjonalne. Nie trzeba tworzyć jednostek dla każdej koncepcji w aplikacji, ale tylko dla tych, w których aplikacja kliencka wymaga danych lub jednostka działa jako Wskazówka lub sygnał do innej jednostki lub zamiaru.

Podczas opracowywania aplikacji i tworzenia nowego zapotrzebowania na dane można później dodać odpowiednie jednostki do modelu LUIS.

<a name="entity-compared-to-intent"></a>

## <a name="entity-represents-data-extraction"></a>Jednostka reprezentuje wyodrębnianie danych

Jednostka reprezentuje koncepcję danych _w wypowiedź_. Celem jest sklasyfikowanie _całego wypowiedź_.

Weź pod uwagę następujące cztery wyrażenia długości:

|Wypowiedź|Przewidywany cel|Wyodrębnione jednostki|Objaśnienie|
|--|--|--|--|
|Pomoc|Pomoc|-|Brak elementów do wyodrębnienia.|
|Wyślij coś|sendSomething|-|Brak elementów do wyodrębnienia. Model nie ma wymaganej funkcji do wyodrębnienia `something` w tym kontekście i nie podano odbiorcy.|
|Wyślij Roberta|sendSomething|`Bob`, `present`|Model wyodrębnia `Bob` przez dodanie wymaganej funkcji wstępnie skompilowanej jednostki `personName` . Jednostka uczenia maszynowego została użyta do wyodrębnienia `present` .|
|Wyślij Roberta do pudełka czekolady|sendSomething|`Bob`, `box of chocolates`|Dwie ważne fragmenty danych `Bob` i, które zostały `box of chocolates` wyodrębnione przez jednostki uczenia maszynowego.|

## <a name="label-entities-in-all-intents"></a>Oznacz jednostki we wszystkich intencjach

Jednostki wyodrębniają dane niezależnie od zamierzonego zamiaru. Upewnij się, że _wszystkie_ przykładowe wyrażenia długości we wszystkich intencjach zostały oznaczone. `None`Brak etykiety jednostki w zamierzeniu powoduje pomyłkę, nawet jeśli w przypadku innych intencji wyrażenia długości się znacznie więcej szkoleń.

## <a name="design-entities-for-decomposition"></a>Projektowanie jednostek do dekompozycji

jednostki uczenia maszynowego umożliwiają projektowanie schematu aplikacji na potrzeby dekompozycji, co pozwala na rozdzielenie dużej koncepcji na podjednostki.

Projektowanie pod kątem dekompozycji umożliwia LUISom zwrócenie głębokiego stopnia rozpoznawania jednostek do aplikacji klienckiej. Dzięki temu aplikacja kliencka może skupić się na regułach biznesowych i pozostawić dane do LUIS.

Jednostka uczenia maszynowego jest wyzwalana na podstawie kontekstu uzyskanego za pośrednictwem przykładu wyrażenia długości.

[**jednostki uczenia maszynowego**](tutorial-machine-learned-entity.md) to elementy wyodrębniające najwyższego poziomu. Podjednostki są jednostkami podrzędnymi jednostek uczenia maszynowego.

## <a name="effective-machine-learned-entities"></a>Efektywne jednostki zdobyte przez maszynę

Aby efektywnie kompilować informacje o jednostkach:

* Etykiety powinny być spójne dla zamiar. Obejmuje to nawet wyrażenia długości w zamiarze **Brak** , które zawierają tę jednostkę. W przeciwnym razie model nie będzie mógł ustalić sekwencji efektywnie.
* Jeśli masz zainstalowaną maszynę z podjednostkami, upewnij się, że różne zamówienia i warianty jednostki i podjednostek są prezentowane w etykiecie wyrażenia długości. Przykładowe etykiety wyrażenia długości powinny zawierać wszystkie prawidłowe formularze i zawierają jednostki, które są wyświetlane i są nieobecne, a także są zmieniane w ramach wypowiedź.
* Należy unikać dopasowywania jednostek do bardzo ustalonego zestawu. Nastąpi **zamontowanie** , gdy model nie jest dobrze generalize i jest typowym problemem w modelach uczenia maszynowego. Oznacza to, że aplikacja nie będzie odpowiednio działała w nowych danych. Z kolei należy zaróżnić przykład z etykietą wyrażenia długości, aby aplikacja mogła uogólnić poza ograniczonymi przykładami. Należy zaróżnić różne podjednostki o wystarczającej zmianie modelu, aby myśleć o większej koncepcji, a nie tylko w przykładach.

## <a name="effective-prebuilt-entities"></a>Obowiązujące wstępnie skompilowane jednostki

W celu utworzenia efektywnych jednostek, które wyodrębniają wspólne dane, takie jak te dostarczone przez [wstępnie zbudowane jednostki](luis-reference-prebuilt-entities.md), zalecamy wykonanie poniższego procesu.

Ulepszanie wyodrębniania danych przez umieszczenie własnych danych w jednostce jako funkcji. W ten sposób wszystkie dodatkowe etykiety z danych zapoznają się z kontekstem, gdzie nazwy osób istnieją w aplikacji.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>Typy jednostek

Podjednostka do elementu nadrzędnego powinna być jednostką uczenia maszynowego. Podjednostka może użyć jednostki niezwiązanej z uczeniem maszynowym jako [funkcji](luis-concept-feature.md).

Wybierz jednostkę na podstawie sposobu wyodrębniania danych i sposobu ich reprezentowania po wyodrębnieniu.

|Typ jednostki|Przeznaczenie|
|--|--|
|[**Komputer — informacje**](tutorial-machine-learned-entity.md)|Wyodrębnij zagnieżdżone, złożone dane uzyskane z przykładów z etykietami. |
|[**Staw**](reference-entity-list.md)|Lista elementów i ich synonimy wyodrębnione z **dokładnym dopasowaniem do tekstu**.|
|[**Wzorzec. any**](#patternany-entity)|Jednostka, gdzie znalezienie końca jednostki jest trudne do ustalenia, ponieważ jednostka jest bezpłatna. Dostępne tylko w [wzorcach](luis-concept-patterns.md).|
|[**Wstępnie utworzonych**](luis-reference-prebuilt-entities.md)|Jest już szkolony do wyodrębniania określonego rodzaju danych, takich jak adres URL lub wiadomość e-mail. Niektóre z tych wstępnie skompilowanych jednostek są zdefiniowane w projekcie typu "Open Source [" — tekst](https://github.com/Microsoft/Recognizers-Text) . Jeśli określona kultura lub jednostka nie jest obecnie obsługiwana, współtworzy projekt.|
|[**Wyrażenie regularne**](reference-entity-regular-expression.md)|Używa wyrażenia regularnego do **dokładnego dopasowania tekstu**.|


## <a name="extraction-versus-resolution"></a>Wyodrębnianie a rozpoznawanie

Jednostki wyodrębniają dane, ponieważ dane są wyświetlane w wypowiedź. Jednostki nie zmieniają ani nie rozwiązują danych. Jednostka nie będzie udostępniać żadnej rozdzielczości, jeśli tekst jest prawidłową wartością dla jednostki lub nie.

Istnieją sposoby zapewnienia rozwiązania do ekstrakcji, ale należy pamiętać, że ogranicza to zdolność aplikacji do odporności na różnice i błędy.

Jednostki list i wyrażenia regularne (dopasowanie tekstu) mogą być używane jako [funkcje wymagane](luis-concept-feature.md#required-features) dla podjednostki i które działają jako filtr do wyodrębniania. Należy to uważnie użyć, aby nie utrudniać możliwości aplikacji.

## <a name="extracting-contextually-related-data"></a>Wyodrębnianie danych związanych z kontekstem

Wypowiedź może zawierać dwa lub więcej wystąpień jednostki, gdzie znaczenie danych opiera się na kontekście w wypowiedź. Przykładem jest wypowiedź do zarezerwowania lotu, który ma dwie lokalizacje geograficzne, źródło i miejsce docelowe.

`Book a flight from Seattle to Cairo`

Dwie lokalizacje muszą zostać wyodrębnione w sposób, w jaki aplikacja klienta wie o typie każdej lokalizacji w celu ukończenia zakupu biletów.

Aby wyodrębnić lokalizację początkową i docelową, Utwórz dwa podjednostki w ramach jednostki Uczenie maszynowe w ramach kolejności biletów. Dla każdej z podjednostek utwórz wymaganą funkcję, która używa geographyV2.

<a name="using-component-constraints-to-help-define-entity"></a>
<a name="using-subentity-constraints-to-help-define-entity"></a>

### <a name="using-required-features-to-constrain-entities"></a>Korzystanie z funkcji wymaganych do ograniczenia jednostek

Dowiedz się więcej o [wymaganych funkcjach](luis-concept-feature.md)

## <a name="patternany-entity"></a>Jednostka Pattern.any

Wzorzec. any jest dostępny tylko we [wzorcu](luis-concept-patterns.md).

<a name="if-you-need-more-than-the-maximum-number-of-entities"></a>
## <a name="exceeding-app-limits-for-entities"></a>Przekraczanie limitów aplikacji dla jednostek

Jeśli potrzebujesz więcej niż [Limit](luis-limits.md#model-limits), skontaktuj się z pomocą techniczną. Aby to zrobić, Zbierz szczegółowe informacje o systemie, przejdź do witryny sieci Web [Luis](luis-reference-regions.md#luis-website) , a następnie wybierz pozycję **Pomoc techniczna**. Jeśli Twoja subskrypcja platformy Azure obejmuje usługi pomocy technicznej, skontaktuj się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/options/).

## <a name="entity-prediction-status-and-errors"></a>Stan i błędy przewidywania jednostek

Portal LUIS pokazuje, kiedy jednostka ma inne przewidywania jednostek niż jednostka wybrana dla przykładu wypowiedź. Ten różny wynik jest oparty na bieżącym przeszkolonym modelu. 

:::image type="content" source="./media/luis-concept-entities/portal-entity-prediction-error.png" alt-text="Portal LUIS pokazuje, kiedy jednostka ma inne przewidywania jednostek niż jednostka wybrana dla przykładu wypowiedź.":::

Tekst błędu zostanie wyróżniony w przykładzie wypowiedź, a w wierszu przykładu wypowiedź znajduje się wskaźnik błędu z prawej strony wyświetlany jako czerwony trójkąt. 

Te informacje służą do rozwiązywania błędów jednostek przy użyciu co najmniej jednej z następujących czynności:
* Wyróżniony tekst jest w nieodpowiedniej etykiecie. Aby naprawić, przejrzeć, poprawić i ponownie wyszkolić. 
* Utwórz [funkcję](luis-concept-feature.md) dla jednostki ułatwiającą identyfikację koncepcji jednostki
* Dodaj więcej [przykładowych wyrażenia długości](luis-concept-utterance.md) i etykiet z jednostką
* [Przejrzyj aktywne sugestie dotyczące uczenia](luis-concept-review-endpoint-utterances.md) wszystkich wyrażenia długości odebranych w punkcie końcowym przewidywania, które mogą pomóc w zidentyfikowaniu koncepcji jednostki.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z pojęciami dotyczącymi dobrego [wyrażenia długości](luis-concept-utterance.md).

Zobacz [Dodawanie jednostek](luis-how-to-add-entities.md) , aby dowiedzieć się więcej na temat dodawania jednostek do aplikacji Luis.

Zobacz [Samouczek: Wyodrębnij dane strukturalne z wypowiedź użytkownika przy użyciu jednostek uczenia maszynowego w programie Language Understanding (Luis)](tutorial-machine-learned-entity.md) , aby dowiedzieć się, jak wyodrębnić dane strukturalne z wypowiedź przy użyciu jednostki Uczenie maszynowe.

