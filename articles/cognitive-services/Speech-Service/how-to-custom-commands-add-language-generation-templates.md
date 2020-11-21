---
title: 'Instrukcje: korzystanie z szablonów generacji języka dla odpowiedzi mowy — usługa mowy'
titleSuffix: Azure Cognitive Services
description: W tym artykule dowiesz się, jak używać szablonów generowania języka z poleceniami niestandardowymi. Szablony generowania języka umożliwiają dostosowanie odpowiedzi wysyłanych do klienta i wprowadzenie wariancji w odpowiedziach.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 40c5e3474d3992108ef61d34e745bc63c1f7a713
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020950"
---
# <a name="add-language-generation-templates-for-speech-responses"></a>Dodawanie szablonów generowania języka na potrzeby odpowiedzi mowy

W tym artykule dowiesz się, jak używać szablonów generowania języka z poleceniami niestandardowymi. Szablony generowania języka umożliwiają dostosowanie odpowiedzi wysyłanych do klienta i wprowadzenie wariancji w odpowiedziach. Dostosowanie generowania języka można osiągnąć przez:

- Korzystanie z szablonów generacji języka
- Użycie wyrażeń adaptacyjnych

## <a name="prerequisites"></a>Wymagania wstępne

Należy wykonać czynności opisane w następujących artykułach:

> [!div class="checklist"]
> * [Instrukcje: Tworzenie aplikacji przy użyciu prostych poleceń](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [Instrukcje: Dodawanie parametrów do poleceń](./how-to-custom-commands-add-parameters-to-commands.md)

## <a name="language-generation-templates-overview"></a>Omówienie szablonów generowania języka

Szablony poleceń niestandardowych są oparte na [szablonach LG](/azure/bot-service/file-format/bot-builder-lg-file-format#templates)BotFramework. Ponieważ polecenia niestandardowe tworzą nowy szablon LG w razie potrzeby (czyli w przypadku odpowiedzi na mowę w parametrach lub akcjach), nie trzeba określać nazwy szablonu LG. Dlatego zamiast definiować szablon jako:

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

Wystarczy zdefiniować treść szablonu bez nazwy w następujący sposób.

> [!div class="mx-imgBorder"]
> ![przykład edytora szablonów](./media/custom-commands/template-editor-example.png)


Ta zmiana wprowadza zmiany do odpowiedzi na mowę wysyłanych do klienta. Tak więc w przypadku tego samego wypowiedź, odpowiednia odpowiedź mowy byłaby losowo wybierana z dostępnych opcji.

Korzystanie z szablonów LG pozwala także definiować złożone odpowiedzi na mowę dla poleceń przy użyciu wyrażeń adaptacyjnych. Aby uzyskać więcej informacji, można zapoznać się z [formatem szablonów LG](/azure/bot-service/file-format/bot-builder-lg-file-format#templates) . Polecenia niestandardowe domyślnie obsługują wszystkie funkcje z następującymi niewielkimi różnicami:

* W jednostkach szablonów LG są reprezentowane jako $ {EntityName}. W poleceniach niestandardowych nie używamy jednostek, ale parametry mogą być używane jako zmienne z jedną z tych reprezentacji $ {ParameterName} lub {ParameterName}
* Tworzenie i rozszerzanie szablonu nie jest obsługiwane w poleceniach niestandardowych. Dzieje się tak dlatego, że nigdy nie edytujesz `.lg` pliku bezpośrednio, ale tylko odpowiedzi automatycznie utworzonych szablonów.
* Funkcje niestandardowe wstrzykiwane przez element LG nie są obsługiwane w poleceniach niestandardowych. Wstępnie zdefiniowane funkcje są nadal obsługiwane.
* Opcje (Strict, replaceNull & lineBreakStyle) nie są obsługiwane w poleceniach niestandardowych.

## <a name="add-template-responses-to-turnonoff-command"></a>Dodawanie odpowiedzi szablonu do polecenia TurnOnOff

Zmodyfikuj polecenie **TurnOnOff** , aby dodać nowy parametr z następującą konfiguracją:

| Ustawienie            | Sugerowana wartość       | 
| ------------------ | --------------------- | 
| Nazwa               | `SubjectContext`         | 
| Jest globalny          | unchecked             | 
| Wymagane           | unchecked               | 
| Typ               | Ciąg                |
| Wartość domyślna      | `all` |
| Konfiguracja      | Akceptowanie wstępnie zdefiniowanych wartości wejściowych z wykazu wewnętrznego | 
| Wstępnie zdefiniowane wartości wejściowe | `room`, `bathroom`, `all`|

### <a name="modify-completion-rule"></a>Modyfikuj regułę uzupełniania

Edytuj sekcję **Actions** istniejącej reguły ukończenia **ConfirmationResponse**. W oknie podręcznym **Edytowanie akcji** przejdź do **edytora szablonów** i Zastąp tekst następującym przykładem.

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

**Przeszkol** i **Przetestuj** swoją aplikację w następujący sposób. Zwróć uwagę na odmianę odpowiedzi z powodu użycia wielu alternatyw wartości szablonu, a także wykorzystano wyrażenia adaptacyjne.

* Wejście: Włączanie telewizora
* Wynik: OK, Włączanie telewizora
* Wejście: Włączanie telewizora
* Wynik: gotowe, włączona telewizja
* Dane wejściowe: Wyłącz światła
* Wynik: OK, wyłączanie wszystkich lamp
* Dane wejściowe: Wyłącz światła pokojowe
* Wynik: OK, wyłączanie sygnalizatorów pokoju

## <a name="use-custom-voice"></a>Używanie głosu niestandardowego

Innym sposobem dostosowania odpowiedzi na polecenia niestandardowe jest wybranie niestandardowego głosu wyjściowego. Wykonaj następujące kroki, aby przełączyć domyślny głos na niestandardowy.

1. W aplikacji polecenia niestandardowe wybierz pozycję **Ustawienia** w okienku po lewej stronie.
1. Wybierz opcję **niestandardowy głos** w środkowym okienku.
1. Wybierz żądany niestandardowy lub publiczny głos z tabeli.
1. Wybierz pozycję **Zapisz**.

> [!div class="mx-imgBorder"]
> ![Przykładowe zdania z parametrami](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> - Dla **publicznych głosów** **typy neuronowych** są dostępne tylko dla określonych regionów. Aby sprawdzić dostępność, zobacz [głosy standardowe i neuronowych według regionu/punktu końcowego](./regions.md#standard-and-neural-voices).
> - W przypadku **niestandardowych głosów** można je utworzyć na stronie niestandardowego projektu głosu. Zobacz Rozpoczynanie [pracy z niestandardowym głosem](./how-to-custom-voice.md).

Teraz aplikacja będzie odpowiadać w wybranym głosowaniu zamiast domyślnego głosu.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Integrowanie poleceń niestandardowych przy użyciu zestawu Speech SDK](./how-to-custom-commands-setup-speech-sdk.md).