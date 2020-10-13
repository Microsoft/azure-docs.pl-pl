---
title: Zapytania wyszukiwania w dziennikach Azure Monitor | Microsoft Docs
description: Ten artykuł zawiera Samouczek przedstawiający wprowadzenie do korzystania z wyszukiwania w dzienniku Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/06/2018
ms.openlocfilehash: e13f4abc37e348759e7d0b8a2f7d890c82fe0d15
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77660244"
---
# <a name="search-queries-in-azure-monitor-logs"></a>Wyszukiwanie zapytań w dziennikach Azure Monitor
Zapytania dziennika Azure Monitor mogą rozpoczynać się od nazwy tabeli lub polecenia wyszukiwania. Ten samouczek obejmuje zapytania oparte na wyszukiwaniach. Istnieją zalety każdej z tych metod.

Zapytania oparte na tabelach rozpoczynają się od określania zakresu zapytania i dlatego są bardziej wydajne niż zapytania wyszukiwania. Zapytania wyszukiwania są mniej strukturalne, co sprawia, że są one lepszym wyborem podczas wyszukiwania określonej wartości w kolumnach lub tabelach. **Wyszukiwanie** może skanować wszystkie kolumny w danej tabeli lub we wszystkich tabelach dla określonej wartości. Ilość przetwarzanych danych może być olbrzymia, co oznacza, że te zapytania mogą trwać dłużej i mogą zwracać bardzo duże zestawy wyników.

## <a name="search-a-term"></a>Wyszukaj termin
Polecenie **wyszukiwania** jest zwykle używane do wyszukiwania określonego terminu. W poniższym przykładzie wszystkie kolumny we wszystkich tabelach są skanowane pod kątem terminu "błąd":

```Kusto
search "error"
| take 100
```

Mimo że są one łatwe w użyciu, zapytania nieobjęte zakresem, takie jak pokazane powyżej, nie są efektywne i prawdopodobnie zwracają wiele nieistotnych wyników. Lepszym rozwiązaniem jest wyszukanie w odpowiedniej tabeli, a nawet konkretnej kolumny.

### <a name="table-scoping"></a>Określanie zakresu tabeli
Aby wyszukać termin w określonej tabeli, Dodaj `in (table-name)` bezpośrednio po operatorze **wyszukiwania** :

```Kusto
search in (Event) "error"
| take 100
```

lub w wielu tabelach:
```Kusto
search in (Event, SecurityEvent) "error"
| take 100
```

### <a name="table-and-column-scoping"></a>Określanie zakresu tabeli i kolumny
Domyślnie **Wyszukiwanie** będzie szacować wszystkie kolumny w zestawie danych. Aby wyszukać tylko określoną kolumnę (nazwane *Źródło* w poniższym przykładzie), użyj następującej składni:

```Kusto
search in (Event) Source:"error"
| take 100
```

> [!TIP]
> Jeśli używasz `==` zamiast programu `:` , wyniki mogą zawierać rekordy, w których kolumna *źródłowa* ma dokładną wartość "Error" i w tym konkretnym przypadku. Użycie ":" spowoduje uwzględnienie rekordów, w których *Źródło* ma wartości takie jak "kod błędu 404" lub "błąd".

## <a name="case-sensitivity"></a>Uwzględnianie wielkości liter
Domyślnie wyszukiwanie warunkowe nie uwzględnia wielkości liter, dlatego wyszukiwanie "DNS" może dać wyniki, takie jak "DNS", "DNS" lub "DNS". Aby wyszukać wielkość liter, użyj `kind` opcji:

```Kusto
search kind=case_sensitive in (Event) "DNS"
| take 100
```

## <a name="use-wild-cards"></a>Korzystanie z symboli wieloznacznych
Polecenie **Search** obsługuje symbole wieloznaczne, na początku, na końcu lub w środku okresu.

Aby wyszukać terminy, które zaczynają się od "win":
```Kusto
search in (Event) "win*"
| take 100
```

Aby wyszukać Terminy kończące się na ". com":
```Kusto
search in (Event) "*.com"
| take 100
```

Aby wyszukać terminy zawierające ciąg "www":
```Kusto
search in (Event) "*www*"
| take 100
```

Aby wyszukać terminy zaczynające się od "Corp" i kończące się na ". com", takie jak "corp.mydomain.com" "

```Kusto
search in (Event) "corp*.com"
| take 100
```

Możesz również uzyskać wszystko w tabeli, używając tylko symbolu wieloznacznego: `search in (Event) *` , ale tak samo jak w przypadku pisania `Event` .

> [!TIP]
> `search *`W celu uzyskania każdej kolumny z każdej tabeli zaleca się, aby zawsze określać zakres zapytań do określonych tabel. Wykonywanie zapytań w zakresie nienależących do zakresu może potrwać trochę czasu i może zwracać zbyt wiele wyników.

## <a name="add-and--or-to-search-queries"></a>Dodawanie *i* / *or* wyszukiwanie zapytań
Użyj programu **i** , aby wyszukać rekordy zawierające wiele warunków:

```Kusto
search in (Event) "error" and "register"
| take 100
```

Użyj **lub** , aby pobrać rekordy zawierające co najmniej jeden z warunków:

```Kusto
search in (Event) "error" or "register"
| take 100
```

Jeśli masz wiele warunków wyszukiwania, możesz połączyć je do tego samego zapytania przy użyciu nawiasów:

```Kusto
search in (Event) "error" and ("register" or "marshal*")
| take 100
```

Wyniki tego przykładu byłyby rekordy zawierające termin "Error", a także zawierają "Register" lub coś, który rozpoczyna się od "Marshal".

## <a name="pipe-search-queries"></a>Zapytania wyszukiwania potokowego
Podobnie jak każde inne polecenie, **Wyszukiwanie** może być potokiem, dzięki czemu wyniki wyszukiwania mogą być filtrowane, sortowane i agregowane. Na przykład, aby uzyskać liczbę rekordów *zdarzeń* zawierających "win":

```Kusto
search in (Event) "win"
| count
```




## <a name="next-steps"></a>Następne kroki

- Zobacz więcej samouczków w [witrynie języka zapytań Kusto](/azure/kusto/query/).
