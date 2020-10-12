---
title: Jak używać nazwanych wartości w zasadach usługi Azure API Management
description: Dowiedz się, jak używać nazwanych wartości w zasadach usługi Azure API Management. Nazwane wartości mogą zawierać ciągi literałów i wyrażenia zasad.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/08/2020
ms.author: apimpm
ms.openlocfilehash: 3f317276ae92e6121d519553b7883677dab89705
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87852195"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Jak używać nazwanych wartości w zasadach usługi Azure API Management

Zasady API Management są zaawansowaną możliwością systemu, która umożliwia Azure Portal zmianę zachowania interfejsu API za pomocą konfiguracji. Zasady to zbiór instrukcji, które są wykonywane sekwencyjnie podczas żądania lub odpowiedzi interfejsu API. Instrukcje zasad można utworzyć przy użyciu literałów wartości tekstowych, wyrażeń zasad i nazwanych wartości.

Każde wystąpienie usługi API Management ma kolekcję par klucz/wartość, które są nazywane wartościami nazwanymi, które są globalne dla wystąpienia usługi. Nie ma żadnego narzuconego limitu liczby elementów w kolekcji. Nazwane wartości mogą służyć do zarządzania stałymi wartościami ciągu w ramach wszystkich konfiguracji i zasad interfejsu API. Każda nazwana wartość może mieć następujące atrybuty:

| Atrybut      | Typ            | Opis                                                                                                                            |
| -------------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `Display name` | ciąg          | Służy do odwoływania się do nazwanej wartości w zasadach. Ciąg od jednego do 256 znaków. Dozwolone są tylko litery, cyfry, kropki i kreski. |
| `Value`        | ciąg          | Wartość rzeczywista. Nie może być pusty ani zawierać tylko odstępów. Maksymalnie 4096 znaków.                                        |
| `Secret`       | boolean         | Określa, czy wartość jest kluczem tajnym i powinna być szyfrowana, czy nie.                                                               |
| `Tags`         | tablica ciągów | Służy do filtrowania listy nazwanych wartości. Do 32 tagów.                                                                                    |

![Nazwane wartości](./media/api-management-howto-properties/named-values.png)

Nazwane wartości mogą zawierać ciągi literałów i [wyrażenia zasad](./api-management-policy-expressions.md). Na przykład wartość `Expression` jest wyrażenie zasad, które zwraca ciąg zawierający bieżącą datę i godzinę. Nazwana wartość `Credential` jest oznaczona jako wpis tajny, więc jej wartość nie jest domyślnie wyświetlana.

| Nazwa       | Wartość                      | Wpis tajny | Tagi          |
| ---------- | -------------------------- | ------ | ------------- |
| Wartość      | 42                         | Fałsz  | Najważniejsze wartości |
| Poświadczenie | ••••••••••••••••••••••     | Prawda   | security      |
| Wyrażenie | @ (DateTime. Now. ToString ()) | Fałsz  |               |

> [!NOTE]
> Zamiast nazwanych wartości przechowywanych w ramach usługi API Management, można użyć wartości przechowywanych w usłudze [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) , jak pokazano w tym [przykładzie](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Look%20up%20Key%20Vault%20secret%20using%20Managed%20Service%20Identity.policy.xml).

## <a name="to-add-and-edit-a-named-value"></a>Aby dodać i edytować nazwaną wartość

![Dodaj nazwaną wartość](./media/api-management-howto-properties/add-property.png)

1. Wybierz pozycję **Interfejsy API** w obszarze **API MANAGEMENT**.
2. Wybierz **nazwane wartości**.
3. Naciśnij klawisze **+ Dodaj**.

    Wartości Name i value są wymagane. Jeśli wartość jest kluczem tajnym, zaznacz pole wyboru _to jest wpis tajny_ . Wprowadź jeden lub więcej tagów opcjonalnych, aby pomóc w organizowaniu nazwanych wartości, a następnie kliknij przycisk Zapisz.

4. Kliknij przycisk **Utwórz**.

Po utworzeniu nazwanej wartości można ją edytować, klikając ją. W przypadku zmiany nazwy nazwanej wartości wszystkie zasady odwołujące się do tej nazwanej wartości są automatycznie aktualizowane w celu użycia nowej nazwy.

## <a name="to-delete-a-named-value"></a>Aby usunąć nazwaną wartość

Aby usunąć nazwaną wartość, kliknij przycisk **Usuń** obok nazwanej wartości do usunięcia.

> [!IMPORTANT]
> Jeśli do nazwanej wartości odwołują się żadne zasady, nie będzie można jej pomyślnie usunąć do momentu usunięcia nazwanej wartości ze wszystkich zasad, które go używają.

## <a name="to-search-and-filter-named-values"></a>Aby wyszukać i filtrować nazwane wartości

Karta **nazwane wartości** zawiera funkcje wyszukiwania i filtrowania, które ułatwiają zarządzanie nazwanymi wartościami. Aby filtrować listę nazwanych wartości według nazwy, wprowadź wyszukiwany termin w polu tekstowym **Właściwości wyszukiwania** . Aby wyświetlić wszystkie nazwane wartości, usuń zaznaczenie pola tekstowego **Właściwości wyszukiwania** i naciśnij klawisz ENTER.

Aby odfiltrować listę według tagu, wprowadź jeden lub więcej tagów do pola tekstowego **Filtruj według tagów** . Aby wyświetlić wszystkie nazwane wartości, usuń zaznaczenie pola tekstowego **Filtruj według tagów** i naciśnij klawisz ENTER.

## <a name="to-use-a-named-value"></a>Aby użyć nazwanej wartości

Aby użyć nazwanej wartości w zasadach, umieść ją w podwójnej parze nawiasów klamrowych `{{ContosoHeader}}` , jak pokazano w następującym przykładzie:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

W tym przykładzie `ContosoHeader` jest używany jako nazwa nagłówka w `set-header` zasadach i `ContosoHeaderValue` jest używany jako wartość tego nagłówka. Kiedy te zasady są oceniane podczas żądania lub odpowiedzi bramy API Management `{{ContosoHeader}}` i `{{ContosoHeaderValue}}` są zastępowane odpowiednimi wartościami.

Nazwane wartości mogą być używane jako kompletne wartości atrybutów lub elementów, jak pokazano w poprzednim przykładzie, ale mogą być również wstawiane do lub połączone z częścią wyrażenia tekstu literału, jak pokazano w następującym przykładzie: `<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Nazwane wartości mogą również zawierać wyrażenia zasad. W poniższym przykładzie `ExpressionProperty` jest używany.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Jeśli te zasady zostaną ocenione, `{{ExpressionProperty}}` zostaną zastąpione jej wartością: `@(DateTime.Now.ToString())` . Ponieważ wartość jest wyrażeniem zasad, wyrażenie jest oceniane i zasady są wykonywane w ramach jego wykonania.

Możesz to przetestować w portalu dla deweloperów, wywołując operację, która ma zasady z nazwanymi wartościami w zakresie. W poniższym przykładzie operacja jest wywoływana z dwiema poprzednimi przykładowymi `set-header` zasadami z nazwanymi wartościami. Należy zauważyć, że odpowiedź zawiera dwa niestandardowe nagłówki, które zostały skonfigurowane przy użyciu zasad z nazwanymi wartościami.

![Portal deweloperów][api-management-send-results]

Jeśli przeszukiwany jest [ślad inspektora interfejsu API](api-management-howto-api-inspector.md) dla wywołania, które obejmuje dwie poprzednie przykładowe zasady z nazwanymi wartościami, można zobaczyć dwie `set-header` zasady z nazwanymi wartościami, a także oszacować wyrażenie zasad dla nazwanej wartości, która zawierała wyrażenie zasad.

![Ślad Inspektora interfejsów API][api-management-api-inspector-trace]

Gdy nazwane wartości mogą zawierać wyrażenia zasad, nie mogą zawierać innych nazwanych wartości. Jeśli tekst zawierający odwołanie nazwanej wartości jest używany dla wartości, na przykład `Text: {{MyProperty}}` , to odwołanie nie zostanie rozwiązane i zastąpione.

## <a name="next-steps"></a>Następne kroki

-   Dowiedz się więcej na temat pracy z zasadami
    -   [Zasady w API Management](api-management-howto-policies.md)
    -   [Dokumentacja zasad](./api-management-policies.md)
    -   [Wyrażenia zasad](./api-management-policy-expressions.md)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png
