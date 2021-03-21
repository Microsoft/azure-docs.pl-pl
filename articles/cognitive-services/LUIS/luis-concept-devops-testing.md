---
title: Testowanie dla DevOps aplikacji LUIS
description: Testowanie aplikacji Language Understanding (LUIS) w środowisku DevOpsu.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/3/2020
ms.openlocfilehash: cd2fd8dc8c10864089b198db1ca1089f994a3ffb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98788455"
---
# <a name="testing-for-luis-devops"></a>Testowanie pod kątem LUIS DevOps

Inżynierowie oprogramowania, którzy opracowują aplikację Language Understanding (LUIS), mogą stosować praktyki DevOps dotyczące [kontroli źródła](luis-concept-devops-sourcecontrol.md), [zautomatyzowanych kompilacji](luis-concept-devops-automation.md), [testów](luis-concept-devops-testing.md)i [zarządzania wersjami](luis-concept-devops-automation.md#release-management) , postępując zgodnie z tymi wskazówkami.

W metodologii opracowywania oprogramowania Agile testowanie odgrywa rolę integralną w tworzeniu oprogramowania jakościowego. Każda znacząca zmiana w aplikacji LUIS powinna towarzyszyć testom, które umożliwiają przetestowanie nowych funkcji kompilowanych przez dewelopera w aplikacji. Testy te są sprawdzane w repozytorium kodu źródłowego wraz ze `.lu` źródłem aplikacji Luis. Implementacja zmiany zostanie zakończona, gdy aplikacja spełni testy.

Testy są krytyczną częścią [przepływów pracy](luis-concept-devops-automation.md)ciągłej integracji/ciągłego wdrażania. Gdy zmiany w aplikacji LUIS są proponowane w żądaniu ściągnięcia (PR) lub po scaleniu zmian z gałęzią główną, przepływy pracy CI powinny uruchamiać testy, aby sprawdzić, czy aktualizacje nie powodowały żadnych regresji.

## <a name="how-to-do-unit-testing-and-batch-testing"></a>Jak przeprowadzić testy jednostkowe i testy wsadowe

Istnieją dwa różne rodzaje testów dla aplikacji LUIS, które należy wykonać w przepływach pracy ciągłej integracji:

- **Testy jednostkowe** — stosunkowo proste testy, które weryfikują najważniejsze funkcje aplikacji Luis. Test jednostkowy kończy się, gdy oczekiwane przeznaczenie i oczekiwane jednostki są zwracane dla danego wypowiedź testu. Wszystkie testy jednostkowe muszą zostać przekazane w celu pomyślnego ukończenia przebiegu testowego.  
Ten rodzaj testów jest podobny do [interaktywnego testowania](./luis-concept-test.md) , który można wykonać w [portalu Luis](https://www.luis.ai/).

- **Testy wsadowe** — testowanie wsadowe to kompleksowy test w bieżącym przeszkolonym modelu, który umożliwia mierzenie jego wydajności. W przeciwieństwie do testów jednostkowych, testowanie wsadowe nie przebiega pomyślnie | testowanie nie powiodło się. Oczekiwanie przy testowaniu wsadowym nie jest, że każdy test zwróci oczekiwany cel i oczekiwane jednostki. Zamiast tego test wsadowy ułatwia przeglądanie dokładności poszczególnych założeń i jednostek w aplikacji, a także pomaga w porównaniu z upływem czasu wprowadzania ulepszeń.  
Tego rodzaju testowanie jest takie samo jak [testy wsadowe](./luis-how-to-batch-test.md) , które można wykonać interaktywnie w portalu Luis.

Testy jednostkowe można zastosować od początku projektu. Testowanie wsadowe ma naprawdę wartość tylko wtedy, gdy opracowano schemat aplikacji LUIS i pracujesz nad zwiększeniem jego dokładności.

W przypadku testów jednostkowych i testów wsadowych upewnij się, że testy wyrażenia długości są oddzielone od wyrażenia długości szkoleniowych. Jeśli testujesz te same dane, na których nauczysz się nauczyć, uzyskasz fałszywe wrażenie, że aplikacja działa prawidłowo, gdy tylko przejdzie do danych testowych. Testy nie muszą być widoczne dla modelu w celu przetestowania prawidłowego przebiegu.

### <a name="writing-tests"></a>Pisanie testów

Podczas pisania zestawu testów dla każdego testu należy zdefiniować:

* Test wypowiedź
* Oczekiwany cel
* Oczekiwane jednostki.

Użyj [składni pliku wsadowego](./luis-how-to-batch-test.md#batch-syntax-template-for-intents-with-entities) Luis, aby zdefiniować grupę testów w pliku w formacie JSON. Na przykład:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities":
    [
        {
            "entity": "entity name 1 goes here",
            "startPos": 14,
            "endPos": 23
        },
        {
            "entity": "entity name 2 goes here",
            "startPos": 14,
            "endPos": 23
        }
    ]
  }
]
```

Niektóre narzędzia testowe, takie jak [NLU. DevOps](https://github.com/microsoft/NLU.DevOps) również obsługują pliki testowe sformatowane w LUDown.

#### <a name="designing-unit-tests"></a>Projektowanie testów jednostkowych

Testy jednostkowe powinny być przeznaczone do testowania podstawowych funkcji aplikacji LUIS. W każdej iteracji lub przebiegu opracowywania aplikacji należy napisać wystarczającą liczbę testów, aby sprawdzić, czy najważniejsze funkcje implementowane w tej iteracji działają poprawnie.

W każdym teście jednostkowym dla danego wypowiedź testowego można:

* Sprawdź, czy zwracany jest poprawny cel
* Sprawdź, czy jednostki "Key" — te, które mają krytyczne znaczenie dla rozwiązania, są zwracane.
* Sprawdź, czy [wynik przewidywania](./luis-concept-prediction-score.md) dla zamiaru i jednostek przekracza zdefiniowany próg. Na przykład można zdecydować, że tylko test przeszedł, jeśli wynik przewidywania dla zamiaru i dla jednostek klucza przekracza 0,75.

W testach jednostkowych dobrym pomysłem jest przetestowanie, czy jednostki kluczy zostały zwrócone w odpowiedzi predykcyjnej, ale w celu zignorowania wszelkich fałszywych wartości dodatnich. *Fałszywie dodatnie* to jednostki, które znajdują się w odpowiedzi predykcyjnej, ale które nie są zdefiniowane w oczekiwanych wynikach testu. Ignorowanie fałszywych wartości dodatnich powoduje, że mniej uciążliwe do tworzenia testów jednostkowych, a jednocześnie pozwala skupić się na testowaniu, że dane, które są kluczem do rozwiązania, są zwracane w odpowiedzi predykcyjnej.

> [!TIP]
> [NLU. Narzędzie DevOps](https://github.com/microsoft/NLU.DevOps) obsługuje wszystkie wymagania dotyczące testowania Luis. `compare`Polecenie użyte w [trybie testu jednostkowego](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#unit-test-mode) spowoduje potwierdzenie, że wszystkie testy zakończą się pomyślnie i zignoruje fałszywe pozytywne wyniki dla jednostek, które nie są oznaczone jako oczekiwane.

#### <a name="designing-batch-tests"></a>Projektowanie testów wsadowych

Zestawy testów wsadowych powinny zawierać dużą liczbę przypadków testowych, które są przeznaczone do testowania między wszystkimi intencjami i wszystkimi jednostkami w aplikacji LUIS. Aby uzyskać informacje na temat definiowania zestawu testów wsadowych [, zobacz test wsadowy w portalu Luis](./luis-how-to-batch-test.md) .

### <a name="running-tests"></a>Uruchamianie testów

Portal LUIS oferuje funkcje ułatwiające testowanie interaktywne:

* [**Testowanie interaktywne**](./luis-concept-test.md) pozwala przesłać przykładową wypowiedźę i uzyskać odpowiedź na LUISe i jednostki. Sprawdzasz sukces testu przez kontrolę wzrokową.

* [**Testy wsadowe**](./luis-how-to-batch-test.md) wykorzystują plik testu wsadowego jako dane wejściowe, aby zweryfikować aktywną przeszkolonej wersji, aby zmierzyć jej dokładność przewidywania. Test wsadowy ułatwia przeglądanie dokładności poszczególnych zamierzeń i jednostek w aktywnej wersji, wyświetlając wyniki z wykresem.

#### <a name="running-tests-in-an-automated-build-workflow"></a>Uruchamianie testów w zautomatyzowanym przepływie pracy kompilacji

Interaktywne funkcje testowania w portalu LUIS są przydatne, ale w przypadku DevOps, automatyczne testowanie wykonywane w przepływie pracy ciągłej integracji/ciągłego wdrażania zapewnia pewne wymagania:

* Narzędzia testowe muszą zostać uruchomione w kroku przepływu pracy na serwerze kompilacji. Oznacza to, że narzędzia muszą być dostępne w wierszu polecenia.
* Narzędzia testowe muszą być w stanie wykonać grupę testów względem punktu końcowego i automatycznie sprawdzić oczekiwane wyniki względem rzeczywistych wyników.
* Jeśli testy zakończą się niepowodzeniem, narzędzia testowe muszą zwrócić kod stanu, aby zatrzymać przepływ pracy i "Niepowodzenie kompilacji".

LUIS nie oferuje narzędzia wiersza polecenia lub interfejsu API wysokiego poziomu, który oferuje te funkcje. Zalecamy użycie [NLU. Narzędzie DevOps](https://github.com/microsoft/NLU.DevOps) do uruchamiania testów i sprawdzania wyników, zarówno w wierszu polecenia, jak i podczas zautomatyzowanego testowania w ramach przepływu pracy ciągłej integracji/ciągłego wdrażania.

Możliwości testowania, które są dostępne w portalu LUIS, nie wymagają opublikowanego punktu końcowego i są częścią możliwości tworzenia LUIS. Gdy wdrażasz testy w zautomatyzowanym przepływie pracy kompilacji, musisz opublikować wersję aplikacji LUIS do przetestowania do punktu końcowego, aby narzędzia testowe, takie jak NLU. DevOps może wysyłać żądania prognozowania w ramach testowania.

> [!TIP]
> * Jeśli wdrażasz własne rozwiązanie do testowania i piszesz kod w celu wysłania testu wyrażenia długości do punktu końcowego, pamiętaj, że jeśli używasz klucza tworzenia LUIS, dozwolona stawka transakcji jest ograniczona do 5TPS. Ogranicz szybkość wysyłania lub zamiast tego użyj klucza przewidywania.
> * Podczas wysyłania zapytań testowych do punktu końcowego należy pamiętać, aby użyć `log=false` ciągu zapytania do żądania prognozowania. Gwarantuje to, że test wyrażenia długości nie zostanie zarejestrowany przez LUIS i kończy się na liście przeglądów wyrażenia długości punktu końcowego prezentowanych przez funkcję [Active Learning](./luis-concept-review-endpoint-utterances.md) Luis, a w rezultacie przypadkowo dodaliśmy do szkolenia wyrażenia długości aplikacji.

#### <a name="running-unit-tests-at-the-command-line-and-in-cicd-workflows"></a>Uruchamianie testów jednostkowych w wierszu polecenia i w przepływach pracy ciągłej integracji/ciągłego dostarczania

Możesz użyć [NLU. Pakiet DevOps](https://github.com/microsoft/NLU.DevOps) do uruchamiania testów w wierszu polecenia:

* Użyj NLU. DevOps [polecenie testowe](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Test.md) , aby przesłać testy z pliku testowego do punktu końcowego i przechwycić rzeczywiste wyniki przewidywania do pliku.
* Użyj NLU. DevOps [PORÓWNAJ polecenie](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md) , aby porównać rzeczywiste wyniki z oczekiwanymi wynikami zdefiniowanymi w wejściowym pliku testowym. `compare`Polecenie generuje dane wyjściowe testu nunit i gdy jest używany w [trybie testowania jednostkowego](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#unit-test-mode) za pomocą `--unit-test` flagi, zostanie potwierdzona, że wszystkie testy zakończą się pomyślnie.

### <a name="running-batch-tests-at-the-command-line-and-in-cicd-workflows"></a>Uruchamianie testów wsadowych w wierszu polecenia i w przepływach pracy ciągłej integracji/ciągłego dostarczania

Możesz również użyć NLU. Pakiet DevOps do uruchamiania testów wsadowych w wierszu polecenia.

* Użyj NLU. DevOps [polecenie testowe](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Test.md) , aby przesłać testy z pliku testowego do punktu końcowego i przechwycić rzeczywiste wyniki przewidywania do pliku, tak samo jak w przypadku testów jednostkowych.
* Użyj NLU. DevOps [PORÓWNAJ polecenie](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md) w [trybie testowania wydajności](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#performance-test-mode) , aby zmierzyć wydajność aplikacji, można także porównać wydajność aplikacji z wzorcem wydajności linii bazowej, na przykład wyniki od najnowszych zatwierdzeń do wersji głównej lub bieżącej. W trybie testowania wydajności `compare` polecenie generuje dane wyjściowe testu nunit oraz [wyniki testów wsadowych](./luis-glossary.md#batch-test) w formacie JSON.

## <a name="luis-non-deterministic-training-and-the-effect-on-testing"></a>LUIS niedeterministyczne szkolenie i wpływ na testowanie

Gdy LUIS jest szkoleniowym modelem, na przykład w celu zamiaru, potrzebuje zarówno danych pozytywnych, jak i szkoleń, które zostały podane w celu uczenia aplikacji dla danych model-i danych negatywnych, które *nie* są prawidłowymi przykładami użycia tego modelu. W trakcie szkolenia LUIS kompiluje dane ujemne jednego modelu ze wszystkich danych pozytywnych dostarczonych dla innych modeli, ale w niektórych przypadkach może to spowodować Niezrównoważenie danych. Aby uniknąć tego nierównowagi, LUIS próbuje podzbiór danych negatywnych w sposób niedeterministyczny, aby zoptymalizować go pod kątem lepszego, zrównoważonego zestawu szkoleniowego, ulepszonej wydajności modelu i szybszego uczenia się.

Wynikiem tego niedeterministycznego szkolenia jest to, że może wystąpić nieco [inna odpowiedź przewidywania między różnymi sesjami szkoleniowymi](./luis-concept-prediction-score.md), zwykle dla intencji i/lub jednostek, w których [wynik przewidywania](./luis-concept-prediction-score.md) nie jest wysoki.

Jeśli chcesz wyłączyć niedeterministyczne szkolenie dla tych wersji aplikacji LUIS, które tworzysz na potrzeby testowania, użyj [interfejsu API ustawień wersji](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) z `UseAllTrainingData` ustawieniem ustawionym na `true` .

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [implementowaniu przepływów pracy Ci/CD](luis-concept-devops-automation.md)
* Dowiedz się, jak [zaimplementować DevOps dla Luis przy użyciu usługi GitHub](luis-how-to-devops-with-github.md)