---
title: Funkcje w zapytaniach Azure Monitor dziennika
description: W tym artykule opisano sposób używania funkcji do wywołania zapytania z innego zapytania dziennika w Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/19/2021
ms.openlocfilehash: fecede1d582232ebc75878a687a24818031f0d80
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752942"
---
# <a name="functions-in-azure-monitor-log-queries"></a>Funkcje w zapytaniach Azure Monitor dziennika
Funkcja to zapytanie dziennika w Azure Monitor, które może być używane w innych zapytaniach dziennika, jakby było poleceniem. Usługa Functions umożliwia deweloperom zapewnianie rozwiązań różnym klientom i ponowne używanie logiki zapytań we własnym środowisku. Ten artykuł zawiera szczegółowe informacje na temat używania funkcji i tworzenia własnych.

## <a name="types-of-functions"></a>Typy funkcji
Istnieją dwa typy funkcji w Azure Monitor:

- **Funkcja rozwiązania:** Wstępnie wbudowane funkcje dołączone do Azure Monitor. Są one dostępne we wszystkich obszarach roboczych usługi Log Analytics i nie można ich modyfikować.
- **Funkcje obszaru roboczego:** Funkcje zainstalowane w określonym obszarze roboczym usługi Log Analytics, które mogą być modyfikowane i kontrolowane przez użytkownika.

## <a name="viewing-functions"></a>Wyświetlanie funkcji
Funkcje rozwiązań i funkcje obszaru roboczego w bieżącym obszarze roboczym można wyświetlić na karcie **Funkcje** w lewym okienku obszaru roboczego usługi Log Analytics. Użyj **przycisku** Filtruj, aby filtrować funkcje uwzględnione na liście i **grupować według,** aby zmienić ich grupowanie. Wpisz ciąg w **polu Wyszukiwania,** aby zlokalizować określoną funkcję. Umieść kursor nad funkcją, aby wyświetlić szczegółowe informacje o tej funkcji, w tym opis i parametry.

:::image type="content" source="media/functions/view-functions.png" alt-text="View, funkcja" lightbox="media/functions/view-functions.png":::

## <a name="use-a-function"></a>Korzystanie z funkcji
Użyj funkcji w zapytaniu, wpisując jej nazwę z wartościami dla wszystkich parametrów, tak jak w poleceniu. Dane wyjściowe funkcji mogą być zwracane jako wyniki lub potokowane do innego polecenia.

Dodaj funkcję do bieżącego zapytania, klikając dwukrotnie jej nazwę lub umieszczając nad nim wskaźnik myszy i wybierając pozycję **Użyj w edytorze**. Funkcje w obszarze roboczym będą również uwzględniane w funkcji IntelliSense podczas wpisywania zapytania. 

Jeśli zapytanie wymaga parametrów, podaj je przy użyciu składni : `function_name(param1,param2,...)` .

:::image type="content" source="media/functions/function-use.png" alt-text="Korzystanie z funkcji" lightbox="media/functions/function-use.png":::

## <a name="create-a-function"></a>Tworzenie funkcji
Aby utworzyć funkcję na pomocą bieżącego zapytania w edytorze, wybierz pozycję **Zapisz,** a następnie **pozycję Zapisz jako funkcję**. 

:::image type="content" source="media/functions/function-save.png" alt-text="Tworzenie funkcji" lightbox="media/functions/function-save.png":::

Utwórz funkcję za pomocą usługi Log Analytics w Azure Portal, klikając pozycję **Zapisz,** a następnie podając informacje w poniższej tabeli.

| Ustawienie | Opis |
|:---|:---|
| Nazwa funkcji  | Nazwa funkcji. Może to nie zawierać spacji ani znaków specjalnych. Może również nie zaczynać się od podkreślenia (_), ponieważ ten znak jest zarezerwowany dla funkcji rozwiązania. |
| Starsza kategoria | Kategoria zdefiniowana przez użytkownika ułatwiający filtrowanie i grupowanie funkcji.   |
| Zapisz jako grupę komputerów | Zapisz zapytanie jako [grupę komputerów.](computer-groups.md)  |
| Parametry | Dodaj parametr dla każdej zmiennej w funkcji, która wymaga wartości, gdy jest używana. Aby [uzyskać szczegółowe informacje,](#function-parameters) zobacz Parametry funkcji. |

:::image type="content" source="media/functions/function-details.png" alt-text="Szczegóły funkcji" lightbox="media/functions/function-details.png":::

## <a name="function-parameters"></a>Parametry funkcji 
Do funkcji można dodać parametry, aby można było podać wartości dla niektórych zmiennych podczas jej wywoływania. Dzięki temu ta sama funkcja może być używana w różnych zapytaniach, z których każde udostępnia różne wartości parametrów. Parametry są definiowane przez następujące właściwości.

| Ustawienie | Opis |
|:---|:---|
| Typ  | Typ danych dla wartości. |
| Nazwa  | Nazwa parametru. Jest to nazwa, która musi być używana w zapytaniu, aby zastąpić wartość parametru.  |
| Wartość domyślna | Wartość, która ma być używana dla parametru, jeśli nie podano wartości. |

Parametry są uporządkowane, ponieważ są tworzone przy użyciu dowolnych parametrów, które nie mają wartości domyślnej, umieszczonych przed tymi, które mają wartość domyślną.

## <a name="working-with-function-code"></a>Praca z kodem funkcji
Możesz wyświetlić kod funkcji, aby uzyskać wgląd w sposób jej działania lub zmodyfikować kod funkcji obszaru roboczego. Wybierz **pozycję Załaduj kod** funkcji, aby dodać kod funkcji do bieżącego zapytania w edytorze. Dodanie go do pustego zapytania lub pierwszego wiersza istniejącego zapytania spowoduje dodanie nazwy funkcji do karty. Jeśli jest to funkcja obszaru roboczego, umożliwia to edytowanie szczegółów funkcji.

:::image type="content" source="media/functions/function-code.png" alt-text="Ładowanie kodu funkcji" lightbox="media/functions/function-code.png":::

## <a name="edit-a-function"></a>Edytowanie funkcji
Edytuj właściwości lub kod funkcji, tworząc nowe zapytanie, a następnie umieść kursor nad nazwą funkcji i wybierz pozycję Załaduj **kod funkcji**. Dokonaj modyfikacji kodu, a następnie wybierz pozycję **Zapisz,** a następnie pozycję **Edytuj szczegóły funkcji.** Przed kliknięciem przycisku Zapisz należy wprowadzić zmiany we właściwościach i parametrach **funkcji.**

:::image type="content" source="media/functions/function-edit.png" alt-text="Edit, funkcja" lightbox="media/functions/function-edit.png":::
## <a name="example"></a>Przykład
Następująca przykładowa funkcja zwraca wszystkie zdarzenia w dzienniku aktywności platformy Azure od określonej daty, które pasują do określonej kategorii. 

Rozpocznij od następującego zapytania, używając wartości na dysku. Pozwala to sprawdzić, czy zapytanie działa zgodnie z oczekiwaniami.

```Kusto
AzureActivity
| where CategoryValue == "Administrative"
| where TimeGenerated > todatetime("2021/04/05 5:40:01.032 PM")
```

:::image type="content" source="media/functions/example-query.png" alt-text="Przykładowa funkcja — zapytanie początkowe" lightbox="media/functions/example-query.png":::

Następnie zastąp wartości na dysku nazwami parametrów, a następnie zapisz funkcję, wybierając pozycję **Zapisz,** a następnie **pozycję Zapisz jako funkcję**.

```Kusto
AzureActivity
| where CategoryValue == CategoryParam
| where TimeGenerated > DateParam
```

:::image type="content" source="media/functions/example-save-function.png" alt-text="Przykładowa funkcja — zapisywanie funkcji" lightbox="media/functions/example-save-function.png":::

 Podaj następujące wartości dla właściwości funkcji.

| Właściwość | Wartość |
|:---|:---|
| Nazwa funkcji | AzureActivityByCategory |
| Starsza kategoria | Funkcje demonstracyjne |

Przed zapisaniem funkcji zdefiniuj następujące parametry.

| Typ | Nazwa | Wartość domyślna |
|:---|:---|:---|
| ciąg   | CategoryParam | "Administracyjne" |
| datetime | DateParam     | |

:::image type="content" source="media/functions/example-function-properties.png" alt-text="Przykładowa funkcja — właściwości funkcji" lightbox="media/functions/example-function-properties.png":::

Utwórz nowe zapytanie i wyświetl nową funkcję, umieszczając na nim wskaźnik myszy. Zwróć uwagę na kolejność parametrów, ponieważ jest to kolejność, w których muszą być określone podczas korzystania z funkcji.

:::image type="content" source="media/functions/example-view-details.png" alt-text="Przykładowa funkcja — wyświetlanie szczegółów" lightbox="media/functions/example-view-details.png":::

Wybierz **pozycję Użyj w edytorze,** aby dodać nową funkcję do zapytania, a następnie dodaj wartości parametrów. Pamiętaj, że nie musisz określać wartości dla właściwości CategoryParam, ponieważ ma ona wartość domyślną.

:::image type="content" source="media/functions/example-use-function.png" alt-text="Przykładowa funkcja — używanie funkcji" lightbox="media/functions/example-use-function.png":::



## <a name="next-steps"></a>Następne kroki
Zobacz inne lekcje dotyczące pisania Azure Monitor zapytań dziennika:

- [Operacje dotyczące ciągów](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#string-operations)

