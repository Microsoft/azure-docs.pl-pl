---
title: CheckBox — element interfejsu użytkownika
description: Opisuje element interfejsu użytkownika Microsoft. Common. CheckBox dla Azure Portal. Umożliwia użytkownikom Zaznaczanie lub usuwanie zaznaczenia opcji.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tomfitz
ms.openlocfilehash: 9f40f223cca34df58d2af7373d8f60fd7f383162
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87098546"
---
# <a name="microsoftcommoncheckbox-ui-element"></a>Element interfejsu użytkownika Microsoft. Common. CheckBox

Formant CheckBox umożliwia użytkownikom sprawdzenie lub zaznaczenie opcji. Kontrolka zwraca **wartość PRAWDA** , gdy kontrolka jest zaznaczona lub **Fałsz** , gdy nie jest zaznaczone.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

:::image type="content" source="./media/managed-application-elements/microsoft-common-checkbox.png" alt-text="Microsoft. Common. CheckBox":::

## <a name="schema"></a>Schemat

```json
{
    "name": "legalAccept",
    "type": "Microsoft.Common.CheckBox",
    "label": "I agree to the terms and conditions.",
    "constraints": {
        "required": true,
        "validationMessage": "Please acknowledge the legal conditions."
    }
}
```

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
true
```

## <a name="remarks"></a>Uwagi

W przypadku wybrania opcji **wymagane** na **wartość true** użytkownik musi zaznaczyć pole wyboru. Jeśli użytkownik nie zaznaczy pola wyboru, zostanie wyświetlony komunikat weryfikacji.

## <a name="next-steps"></a>Następne kroki

* Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
* Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
