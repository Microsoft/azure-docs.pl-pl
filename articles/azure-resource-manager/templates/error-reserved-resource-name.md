---
title: Błędy nazw zarezerwowanych zasobów
description: Opisuje sposób rozwiązywania błędów podczas podawania nazwy zasobu zawierającej słowo zastrzeżone.
ms.topic: troubleshooting
ms.date: 11/08/2017
ms.openlocfilehash: e76f4bf9bfee7de6e7523d69acf1388d2dd80e93
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75477631"
---
# <a name="resolve-reserved-resource-name-errors"></a>Rozwiązywanie błędów zarezerwowanych nazw zasobów

W tym artykule opisano błąd występujący podczas wdrażania zasobu zawierającego słowo zastrzeżone w nazwie.

## <a name="symptom"></a>Objaw

Podczas wdrażania zasobu, który jest dostępny za pomocą publicznego punktu końcowego, może zostać wyświetlony następujący błąd:

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>Przyczyna

Zasoby, które mają publiczny punkt końcowy nie mogą używać słów zarezerwowanych ani znaków towarowych w nazwie.

Następujące słowa są zastrzeżone:

* DOSTĘP
* AZURE
* PRZEGLĄDARCE
* BIZSPARK
* DOKUMENT
* CORTANA
* DIRECTX
* DOTNET
* DYNAMICS
* EXCEL
* ZAMIAN
* FOREFRONT
* GROOVE
* HOLOLENS
* HYPERV
* URZĄDZENIA Kinect
* LYNC
* MSDN
* O365
* Pakiet
* Office
* USŁUDZE
* #B0
* PROGRAMIE
* POWERPOINT
* SHAREPOINT
* ROZMOWY
* ?
* VISUALSTUDIO

Następujących słów nie można używać jako całego wyrazu lub podciągu w nazwie:

* IDENTYFIKATORÓW
* PROGRAMU
* Systemy
* RAMACH

## <a name="solution"></a>Rozwiązanie

Podaj nazwę, która nie używa jednego z wyrazów zastrzeżonych.