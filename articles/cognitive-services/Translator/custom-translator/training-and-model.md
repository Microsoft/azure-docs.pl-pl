---
title: Co to jest szkolenia i model? — Translator niestandardowy
titleSuffix: Azure Cognitive Services
description: Modelem jest system, który zapewnia tłumaczenie dla określonej pary językowej. Wynikiem pomyślnego szkolenia jest model. Podczas uczenia modelu są wymagane trzy wzajemnie wykluczające się zestawy danych szkoleniowych, zestaw kontrolek danych i testowanie zestawu danych.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: lajanuar
ms.openlocfilehash: 62a444b66d50579c601077751b0700aa954b34c0
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897938"
---
# <a name="what-are-trainings-and-models"></a>Co to są szkolenia i modele?

Modelem jest system, który zapewnia tłumaczenie dla określonej pary językowej.
Wynikiem pomyślnego szkolenia jest model. Podczas uczenia modelu wymagane są trzy wzajemnie wykluczające się typy dokumentów: uczenie, dostrajanie i testowanie. Można również podać typ dokumentu słownika. Zapoznaj się z [wyrównaniem zdania](./sentence-alignment.md#suggested-minimum-number-of-sentences).

Jeśli podczas kolejkowania szkolenia są udostępniane tylko dane szkoleniowe, usługa Custom translator automatycznie gromadzi dane dostrajania i testowania. Spowoduje to użycie losowego podzestawu zdań z dokumentów szkoleniowych i wykluczenie tych zdań z samych danych szkoleniowych.

## <a name="training-document-type-for-custom-translator"></a>Typ dokumentu szkoleniowego dla translatora niestandardowego

Dokumenty zawarte w zestawie szkoleniowym są używane przez translatora niestandardowego jako podstawę tworzenia modelu. W trakcie wykonywania szkoleniowego, zdania, które znajdują się w tych dokumentach są wyrównane (lub sparowane). Możesz wykonać Liberties w redagowaniu zestawu dokumentów szkoleniowych. Można dołączać dokumenty, które są uważane za odnoszące się do styczności w jednym modelu. Należy ponownie wykluczyć je w innej, aby zobaczyć wpływ w [Bleu (analiza dwujęzyczna podkreślenie)](what-is-bleu-score.md). Tak długo, jak zachowujesz zestaw strojenia i stały zestaw testów, możesz poeksperymentować z kompozycją zestawu szkoleniowego. To podejście jest skutecznym sposobem modyfikacji jakości systemu tłumaczenia.

Można uruchomić wiele szkoleń w ramach projektu i porównać [wyniki Bleu](what-is-bleu-score.md) na wszystkich przebiegach szkoleniowych. W przypadku korzystania z wielu szkoleń w celu porównania upewnij się, że dane dotyczące dostrajania/testowania są określane za każdym razem. Upewnij się również, że wyniki są również sprawdzane ręcznie na karcie ["testowanie"](how-to-view-system-test-results.md) .

## <a name="tuning-document-type-for-custom-translator"></a>Dostrajanie typu dokumentu dla translatora niestandardowego

W przypadku dokumentów równoległych zawartych w tym zestawie są używane przez translator niestandardowy w celu dostosowania systemu tłumaczenia w celu uzyskania optymalnych wyników.

Dane dostrajania są używane podczas szkoleń, aby dostosować wszystkie parametry i wagi systemu tłumaczenia do optymalne wartości. Starannie wybieraj dane dostrajania: dane dostrajania powinny być reprezentatywne dla zawartości dokumentów, które mają być tłumaczone w przyszłości. Dane dostrajania mają istotny wpływ na jakość tworzonych tłumaczeń. Dostrajanie umożliwia systemowi translacji dostarczanie tłumaczeń, które znajdują się najbliżej próbek dostarczanych w danych dostrajania. Dane dostrajania nie wymagają więcej niż 2500 zdań. Aby zapewnić optymalną jakość tłumaczenia, zaleca się ręczne wybranie zestawu dostrajania, wybierając najbardziej reprezentatywny wybór zdań.

Podczas tworzenia zestawu strojenia wybierz zdania, które są zrozumiałą i reprezentatywną długością przyszłych zdań, które zamierzasz przetłumaczyć. Należy również wybrać zdania, które mają słowa i frazy, które mają być przetłumaczone w przybliżonej dystrybucji oczekiwanej w przyszłych tłumaczeniach. W przypadku zdania o długości od 7 do 10 wyrazów da najlepsze wyniki, ponieważ te zdania zawierają wystarczająco dużo kontekstu, aby pokazać przegięcia i zapewnić znaczącą długość frazy, bez nadmiernej złożoności.

Dobrym opisem typu zdań do użycia w zestawie strojenia jest Prose: rzeczywiste zdania Fluent. Nie są to komórki tabeli, nie Poems, niezawierające list rzeczy, nie tylko znaki interpunkcyjne ani cyfry w języku regularnym.

Po ręcznym wybraniu danych dostrajania nie powinno ono zawierać żadnych z tych samych zdań co dane szkoleniowe i testowe. Dane dostrajania mają znaczny wpływ na jakość tłumaczeń — należy uważnie wybierać zdania.

Jeśli nie masz pewności, co należy wybrać dla danych dostrajania, po prostu wybierz dane szkoleniowe i zezwól usłudze Custom translator wybierz dla Ciebie dane dostrajania. Gdy zezwolisz na automatyczne wybieranie danych dostrajania przez translatora niestandardowego, będzie on używał losowego podzestawu zdań z dokumentów szkoleniowych dwujęzycznych i wyklucza te zdania z samego materiału szkoleniowego.

## <a name="testing-dataset-for-custom-translator"></a>Testowanie zestawu danych dla translatora niestandardowego

Równoległe dokumenty zawarte w zestawie testów są używane do obliczania wyniku BLEU (analiza dwujęzyczna). Ten wynik wskazuje jakość systemu tłumaczenia. Ten wynik polega na tym, jak ściśle tłumaczenia wykonywane przez system tłumaczenia wynikający z tego szkolenia pasują do zdań odniesienia w zestawie danych testowych.

Wynik BLEU jest pomiarem różnicy między automatycznym translacją i translacją odwołań. Wartość z zakresu od 0 do 100. Wynik 0 wskazuje, że w tłumaczeniu nie występuje pojedynczy wyraz odwołania. Wynik 100 wskazuje, że automatyczne tłumaczenie dokładnie pasuje do odwołania: ten sam wyraz znajduje się w dokładnie tym samym położeniu. Otrzymany wynik to średnia ocena BLEU dla wszystkich zdań danych testowych.

Dane testowe powinny zawierać dokumenty równoległe, w których zdania języka docelowego są najbardziej pożądane tłumaczenia odpowiednich zdań języka źródłowego w parze Source-Target. Możesz chcieć użyć tych samych kryteriów, które zostały użyte do redagowania danych dostrajania. Jednak dane dotyczące testowania nie mają wpływu na jakość systemu tłumaczenia. Służy on wyłącznie do generowania wyniku BLEU.

Dane testowe nie wymagają więcej niż 2 500 zdań. Gdy zezwolisz systemowi na automatyczne wybieranie zestawu testów, będzie on używał losowego podzestawu zdań z dokumentów szkoleniowych dwujęzycznych i wyklucza te zdania z samego materiału szkoleniowego.

Można wyświetlić niestandardowe tłumaczenia zestawu testów i porównać je z tłumaczeniami dostarczonymi w zestawie testów, przechodząc do karty test w modelu.