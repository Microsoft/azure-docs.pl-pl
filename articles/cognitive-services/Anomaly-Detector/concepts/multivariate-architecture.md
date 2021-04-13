---
title: Architektura konserwacji predicative przy użyciu interfejsu API wieloczynnikowa wykrywania anomalii
titleSuffix: Azure Cognitive Services
description: Architektura referencyjna umożliwiająca korzystanie z interfejsów API wieloczynnikowa wykrywania anomalii w celu zastosowania wykrycia anomalii w danych szeregów czasowych w celu przeprowadzenia konserwacji predykcyjnej.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: wykrywanie anomalii, uczenie maszynowe, algorytmy
ms.openlocfilehash: 4d379cfe01dcbd88531b98a32b45e0b30f6adeef
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315595"
---
# <a name="predictive-maintenance-solution-with-anomaly-detector-multivariate"></a>Rozwiązanie do konserwacji predykcyjnej z wieloczynnikowa wykrywania anomalii

Wiele różnych branż potrzebuje rozwiązań do konserwacji predykcyjnej, aby zmniejszyć ryzyko i uzyskać wgląd w szczegółowe dane z ich sprzętu. Konserwacja predykcyjna służy do szacowania stanu sprzętu przez przeprowadzenie monitorowania w trybie online. Celem jest przeprowadzenie konserwacji przed obniżeniem lub przerwą w działaniu sprzętu.

Monitorowanie stanu kondycji sprzętu może być trudne, ponieważ każdy składnik wewnątrz sprzętu może generować dziesiątki sygnałów, na przykład drgania, orientację i obrót.  Może to być jeszcze bardziej skomplikowane, gdy sygnały te mają niejawną relację i muszą być monitorowane i analizowane wspólnie. Definiowanie różnych reguł dla tych sygnałów i skorelowanie ich ze sobą za pomocą każdego z nich może być kosztowne. Funkcja wieloczynnikowa wykrywania anomalii umożliwia:

* Wiele skorelowanych sygnałów, które mają być monitorowane ze sobą, a wzajemne korelacje między nimi są uwzględniane w modelu.
* We wszystkich przechwyconych anomaliach ranga udziału różnych sygnałów może pomóc w wyjaśnieniu anomalii i analizie głównej przyczyny zdarzenia.
* Model wykrywania anomalii w wieloczynnikowa jest zbudowany w sposób nienadzorowany. Modele mogą być przeszkolone dla różnych typów urządzeń.

Tutaj udostępniamy architekturę referencyjną rozwiązania do konserwacji predykcyjnej na podstawie wieloczynnikowa wykrywania anomalii.

## <a name="reference-architecture"></a>Architektura referencyjna

[![Diagram architektoniczny, który rozpoczyna się od danych czujników zbieranych na granicy z częściowym sprzętem branżowym i śledzi potok przetwarzania/analizy do końcowego wyjścia alertu zdarzenia generowanego po uruchomieniu detektora anomalii. ](../media/multivariate-architecture/multivariate-architecture.png)](../media/multivariate-architecture/multivariate-architecture.png#lightbox)

W powyższej architekturze zdarzenia przesyłania strumieniowego pochodzące z danych czujników będą przechowywane w Azure Data Lake, a następnie przetwarzane przez moduł przekształcania danych w formacie szeregów czasowych. W tym czasie zdarzenie przesyłania strumieniowego spowoduje wyzwolenie wykrywania w czasie rzeczywistym z przeszkolonym modelem. Ogólnie rzecz biorąc, będzie można zarządzać cyklem życia modelu wieloczynnikowa, takim jak *Usługa mostka* w tej architekturze.

**Uczenie modeli**: przed użyciem wykrywania anomalii wieloczynnikowa do wykrywania anomalii dla składnika lub sprzętu. Musimy przeszkolić model dla konkretnych sygnałów (szeregów czasowych) generowanych przez tę jednostkę. *Usługa mostka* pobierze dane historyczne i prześle zadanie szkolenia do wykrywania anomalii, a następnie zachowa Identyfikator modelu w *metadanych modelu* .

**Sprawdzanie poprawności modelu**: czas uczenia określonego modelu może być zróżnicowany w zależności od ilości danych szkoleniowych. *Usługa mostka* może regularnie badać stan modelu i informacje diagnostyczne. Sprawdzanie poprawności jakości modelu może być konieczne przed przełączeniem do trybu online. Jeśli w tym scenariuszu znajdują się etykiety, te etykiety mogą służyć do weryfikowania jakości modelu. W przeciwnym razie informacje diagnostyczne mogą służyć do oszacowania jakości modelu. można także przeprowadzić wykrywanie danych historycznych przy użyciu przeszkolonego modelu i oszacować wynik, aby testować poprawność modelu.

**Wnioskowanie modelu**: wykrywanie w trybie online zostanie wykonane z prawidłowym modelem, a identyfikator wyniku może być przechowywany w *tabeli wnioskowania*. Proces szkolenia i proces wnioskowania są wykonywane w sposób asynchroniczny. Ogólnie rzecz biorąc, zadanie wykrywania można wykonać w ciągu kilku sekund. Sygnały używane do wykrywania powinny być takie same, jak te, które zostały użyte do uczenia się. Na przykład, jeśli korzystamy z drgań, orientacji i rotacji do szkoleń, w przypadku wykrywania należy uwzględnić trzy sygnały jako dane wejściowe.

**Alerty zdarzeń** Wyniki wykrywania mogą być zapytania z identyfikatorami wyników. Każdy wynik zawiera ważność każdej anomalii oraz rangę udziału. Można użyć rangi udziału, aby zrozumieć, dlaczego wystąpił anomalia, i który sygnał spowodował to zdarzenie. Można ustawić różne progi o ważności, aby generować alerty i powiadomienia do wysłania do inżynierów pól w celu przeprowadzenia konserwacji.

## <a name="next-steps"></a>Następne kroki

- [Przewodniki Szybki Start](../quickstarts/client-libraries-multivariate.md).
- [Najlepsze rozwiązania](../concepts/best-practices-multivariate.md): w tym artykule zawarto informacje dotyczące zalecanych wzorców do użycia z interfejsami API wieloczynnikowa.