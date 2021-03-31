---
title: EditableGrid — element interfejsu użytkownika
description: Opisuje element interfejsu użytkownika Microsoft. Common. EditableGrid dla Azure Portal. Umożliwia użytkownikom zbieranie danych wejściowych tabelarycznych.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: tomfitz
ms.openlocfilehash: 04f86883a75110985d1cbe050fe3fd3e0582986a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88893770"
---
# <a name="microsoftcommoneditablegrid-ui-element"></a>Microsoft. Common. EditableGrid — element interfejsu użytkownika

Kontrolka służąca do zbierania danych wejściowych tabelarycznych. Wszystkie pola w siatce są edytowalne i liczba wierszy może się różnić.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

:::image type="content" source="./media/managed-application-elements/microsoft-common-editablegrid.png" alt-text="Microsoft. Common. EditableGrid":::

## <a name="schema"></a>Schemat

```json
{
  "name": "people",
  "type": "Microsoft.Common.EditableGrid",
  "ariaLabel": "Enter information per person",
  "label": "People",
  "constraints": {
    "width": "Full",
    "rows": {
      "count": {
        "min": 1,
        "max": 10
      }
    },
    "columns": [
      {
        "id": "colName",
        "header": "Name",
        "width": "1fr",
        "element": {
          "type": "Microsoft.Common.TextBox",
          "placeholder": "Full name",
          "constraints": {
            "required": true,
            "validations": [
              {
                "isValid": "[startsWith(last(take(steps('grid').people, $rowIndex)).colName, 'contoso')]",
                "message": "Must start with 'contoso'."
              },
              {
                "regex": "^[a-z0-9A-Z]{1,30}$",
                "message": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
              }
            ]
          }
        }
      },
      {
        "id": "colGender",
        "header": "Gender",
        "width": "1fr",
        "element": {
          "name": "dropDown1",
          "type": "Microsoft.Common.DropDown",
          "placeholder": "Select a gender...",
          "constraints": {
            "allowedValues": [
              {
                "label": "Female",
                "value": "female"
              },
              {
                "label": "Male",
                "value": "male"
              },
              {
                "label": "Other",
                "value": "other"
              }
            ],
            "required": true
          }
        }
      },
      {
        "id": "colContactPreference",
        "header": "Contact preference",
        "width": "1fr",
        "element": {
          "type": "Microsoft.Common.OptionsGroup",
          "constraints": {
            "allowedValues": [
              {
                "label": "Email",
                "value": "email"
              },
              {
                "label": "Text",
                "value": "text"
              }
            ],
            "required": true
          }
        }
      }
    ]
  }
}
```

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
{
  "colName": "contoso",
  "colGender": "female",
  "colContactPreference": "email"
}
```

## <a name="remarks"></a>Uwagi

- Jedyne prawidłowe kontrolki w tablicy kolumn to [pola tekstowe](microsoft-common-textbox.md), [Opcje](microsoft-common-optionsgroup.md)i [Lista rozwijana](microsoft-common-dropdown.md).
- `$rowIndex`Zmienna jest prawidłowa tylko w wyrażeniach zawartych w elemencie podrzędnym kolumn siatki. Jest to liczba całkowita, która reprezentuje indeks wierszy względem elementu, a liczba rozpoczyna się o jeden i zwiększa się o jeden. Jak pokazano w `"columns":` sekcji schematu, `$rowIndex` jest używany do walidacji.
- Gdy walidacji są wykonywane przy użyciu `$rowIndex` zmiennej, możliwe jest uzyskanie wartości bieżącego wiersza przez połączenie `last()` `take()` poleceń i.

  Na przykład:

  `last(take(<reference_to_grid>, $rowIndex))`

- `label`Właściwość nie jest wyświetlana jako część kontrolki, ale jest wyświetlana na końcu podsumowania karty.
- `ariaLabel`Właściwość jest etykietą dostępności dla siatki. Określ przydatny tekst dla użytkowników korzystających z czytników zawartości ekranu.
- `constraints.width`Właściwość służy do ustawiania szerokości całkowitej siatki. Opcje są _pełne_, _średnie_ i _małe_. Wartość domyślna to _pełna_.
- `width`Właściwość elementów podrzędnych kolumn Określa szerokość kolumny. Szerokości są określane przy użyciu jednostek ułamkowych, takich jak _3fr_, z łącznym miejscem przydzielonym do kolumn proporcjonalnie do ich jednostek. Jeśli szerokość kolumny nie zostanie określona, wartością domyślną jest _1fr_.

## <a name="next-steps"></a>Następne kroki

- Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
- Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
