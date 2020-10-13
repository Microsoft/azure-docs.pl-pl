---
title: Dostosowanie języka w Azure Active Directory B2C
description: Dowiedz się więcej o dostosowywaniu środowiska w przepływach użytkownika.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/13/2019
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6b3bb3648fff6bc893ea07204fad79d2830a1b47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91355097"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Dostosowanie języka w Azure Active Directory B2C

Dostosowanie języka w programie Azure Active Directory B2C (Azure AD B2C) umożliwia przepływom użytkownika dostosowywać się do potrzeb klientów. Firma Microsoft udostępnia tłumaczenia dla [języków 36](#supported-languages), ale można również udostępniać własne tłumaczenia w dowolnym języku. Nawet jeśli środowisko użytkownika jest dostarczane tylko dla jednego języka, można dostosować dowolny tekst na stronach.

## <a name="how-language-customization-works"></a>Jak działa dostosowanie języka

Za pomocą dostosowywania języka można wybrać Języki, w których przepływ użytkownika jest dostępny. Po włączeniu funkcji można podać parametr ciągu zapytania, `ui_locales` z poziomu aplikacji. Gdy wywołasz się do Azure AD B2C, Strona zostanie przetłumaczona na wskazane ustawienia regionalne. Ten typ konfiguracji zapewnia pełną kontrolę nad językami w przepływie użytkownika i ignoruje ustawienia języka w przeglądarce klienta.

Użytkownik może nie potrzebować tego poziomu kontroli nad językami, które widzi klient. Jeśli nie podano `ui_locales` parametru, środowisko klienta jest podyktowane ustawieniami przeglądarki. Można nadal kontrolować Języki, do których przepływ użytkownika jest tłumaczony przez dodanie go jako obsługiwanego języka. Jeśli w przeglądarce klienta ustawiono język, który nie ma być obsługiwany, w zamian zostanie wyświetlony język wybrany jako domyślny w obsługiwanych kulturach.

* **interfejs użytkownika — określony język**: po włączeniu dostosowywania języka przepływ użytkownika zostanie przetłumaczony na język, który jest określony w tym miejscu.
* **Język żądany w przeglądarce**: Jeśli żaden `ui_locales` parametr nie został określony, przepływ użytkownika zostanie przetłumaczony na język żądany w przeglądarce, *Jeśli jest obsługiwany*.
* **Język domyślny dla zasad**: Jeśli w przeglądarce nie określono języka lub określono taki, który nie jest obsługiwany, przepływ użytkownika zostanie przetłumaczony na domyślny język przepływu użytkownika.

> [!NOTE]
> Jeśli używasz niestandardowych atrybutów użytkownika, musisz podać własne tłumaczenia. Aby uzyskać więcej informacji, zobacz [Dostosowywanie ciągów](#customize-your-strings).

## <a name="support-requested-languages-for-ui_locales"></a>Obsługa żądanych języków dla ui_locales

Zasady, które zostały utworzone przed ogólnym udostępnieniem dostosowania języka, muszą najpierw włączyć tę funkcję. Zasady i przepływy użytkowników utworzone po włączeniu dostosowywania języka są domyślnie włączone.

Po włączeniu dostosowywania języka w przepływie użytkownika można kontrolować język przepływu użytkownika, dodając `ui_locales` parametr.

1. W dzierżawie Azure AD B2C wybierz pozycję **przepływy użytkownika**.
1. Kliknij przepływ użytkownika, który chcesz włączyć dla tłumaczeń.
1. Wybierz **Języki**.
1. Wybierz pozycję **Włącz Dostosowywanie języka**.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>Wybierz Języki, w których przepływ użytkownika jest włączony

Zezwól na tłumaczenie zestawu języków dla przepływu użytkownika na żądanie w przeglądarce bez `ui_locales` parametru.

1. Upewnij się, że przepływ użytkownika ma włączone dostosowanie języka z poprzednich instrukcji.
1. Na stronie **Języki** dla przepływu użytkownika wybierz język, który ma być obsługiwany.
1. W okienku właściwości Zmień wartość **włączone** na **tak**.
1. Wybierz pozycję **Zapisz** w górnej części okienka właściwości.

>[!NOTE]
>Jeśli `ui_locales` parametr nie zostanie podany, Strona zostanie przetłumaczona na język przeglądarki klienta tylko wtedy, gdy jest włączona.
>

## <a name="customize-your-strings"></a>Dostosowywanie ciągów

Dostosowanie języka umożliwia dostosowanie dowolnego ciągu w przepływie użytkownika.

1. Upewnij się, że przepływ użytkownika ma włączone dostosowanie języka z poprzednich instrukcji.
1. Na stronie **Języki** dla przepływu użytkownika wybierz język, który chcesz dostosować.
1. W obszarze **pliki zasobów na poziomie strony**wybierz stronę, którą chcesz edytować.
1. Wybierz pozycję **Pobierz domyślne** (lub **Pobierz zastąpienia** , jeśli wcześniej edytowano ten język).

Te kroki zapewniają plik JSON, którego można użyć do rozpoczęcia edytowania ciągów.

### <a name="change-any-string-on-the-page"></a>Zmień dowolny ciąg na stronie

1. Otwórz plik JSON pobrany z poprzednich instrukcji w edytorze JSON.
1. Znajdź element, który chcesz zmienić. Możesz znaleźć `StringId` ciąg, którego szukasz, lub poszukać `Value` atrybutu, który ma zostać zmieniony.
1. Zaktualizuj `Value` atrybut o dane, które mają być wyświetlane.
1. Dla każdego ciągu, który chcesz zmienić, przejdź `Override` do `true` .
1. Zapisz plik i przekaż zmiany. (Formant przekazywania można znaleźć w tym samym miejscu, w którym został pobrany plik JSON).

> [!IMPORTANT]
> Jeśli musisz przesłonić ciąg, upewnij się, że wartość jest ustawiona `Override` na `true` . Jeśli wartość nie zostanie zmieniona, wpis zostanie zignorowany.

### <a name="change-extension-attributes"></a>Zmień atrybuty rozszerzenia

Jeśli chcesz zmienić ciąg dla niestandardowego atrybutu użytkownika lub chcesz dodać go do pliku JSON, jest w następującym formacie:

```json
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

Zamień `<ExtensionAttribute>` na nazwę niestandardowego atrybutu użytkownika.

Zamień na `<ExtensionAttributeValue>` Nowy ciąg, który ma być wyświetlany.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Podaj listę wartości za pomocą LocalizedCollections

Jeśli chcesz podać listę wartości odpowiedzi, musisz utworzyć `LocalizedCollections` atrybut. `LocalizedCollections` jest tablicą `Name` par i `Value` . Kolejność dla elementów będzie kolejność, w jakiej są wyświetlane. Aby dodać `LocalizedCollections` , użyj następującego formatu:

```json
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId` jest atrybutem użytkownika, do którego ten `LocalizedCollections` atrybut jest odpowiedzią.
* `Name` jest wartością pokazywaną użytkownikowi.
* `Value` to co jest zwracane w ramach żądania, gdy ta opcja jest zaznaczona.

### <a name="upload-your-changes"></a>Przekaż zmiany

1. Po wprowadzeniu zmian w pliku JSON Wróć do swojej dzierżawy B2C.
1. Wybierz pozycję **przepływy użytkownika** i kliknij przepływ użytkownika, który chcesz włączyć dla tłumaczeń.
1. Wybierz **Języki**.
1. Wybierz język, w którym chcesz przeprowadzić tłumaczenie.
1. Wybierz stronę, na której chcesz przekazać tłumaczenia.
1. Wybierz ikonę folderu i wybierz plik JSON do przekazania.

Zmiany są zapisywane automatycznie w przepływie użytkownika.

## <a name="customize-the-page-ui-by-using-language-customization"></a>Dostosowywanie interfejsu użytkownika strony przy użyciu dostosowywania języka

Istnieją dwa sposoby lokalizowania zawartości HTML. Jednym ze sposobów jest włączenie [dostosowywania języka](user-flow-language-customization.md). Włączenie tej funkcji pozwala Azure AD B2C do przesyłania dalej parametru Connect OpenID Connect `ui-locales` do punktu końcowego. Na serwerze zawartości można użyć tego parametru, aby zapewnić dostosowane strony HTML, które są specyficzne dla języka.

Alternatywnie można ściągnąć zawartość z różnych miejsc na podstawie ustawień regionalnych, które są używane. W punkcie końcowym z obsługą mechanizmu CORS można skonfigurować strukturę folderów, aby hostować zawartość dla określonych języków. Jeśli używasz wartości wieloznacznej, nastąpi wywołanie odpowiedniej metody `{Culture:RFC5646}` . Załóżmy na przykład, że jest to identyfikator URI strony niestandardowej:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```

Możesz załadować stronę w `fr` . Gdy Strona pobiera zawartość HTML i CSS, pobiera z:

```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Dodawanie języków niestandardowych

Możesz również dodać Języki, dla których firma Microsoft obecnie nie udostępnia tłumaczeń. Musisz podać tłumaczenia dla wszystkich ciągów w przepływie użytkownika. Kody języka i ustawień regionalnych są ograniczone do tych w standardzie ISO 639-1.

1. W dzierżawie Azure AD B2C wybierz pozycję **przepływy użytkownika**.
2. Kliknij przepływ użytkownika, w którym chcesz dodać Języki niestandardowe, a następnie kliknij pozycję **Języki**.
3. Wybierz pozycję **Dodaj język niestandardowy** w górnej części strony.
4. W otwartym okienku kontekstu Zidentyfikuj język, w którym są udostępniane tłumaczenia, wprowadzając prawidłowy kod ustawień regionalnych.
5. Dla każdej strony można pobrać zestaw zastąpień dla języka angielskiego i rozpocząć korzystanie z tłumaczeń.
6. Po zakończeniu pracy z plikami JSON można przekazać je dla każdej strony.
7. Wybierz pozycję **Włącz**, a przepływ użytkownika może teraz pokazać ten język dla użytkowników.
8. Zapisz język.

>[!IMPORTANT]
>Przed zapisaniem należy włączyć Języki niestandardowe lub przekazywać do nich zastąpienia.

## <a name="additional-information"></a>Dodatkowe informacje

### <a name="page-ui-customization-labels-as-overrides"></a>Etykiety dostosowywania interfejsu użytkownika strony jako zastąpienia

Po włączeniu dostosowywania języka poprzednie zmiany w etykietach korzystających z dostosowywania interfejsu użytkownika są utrwalane w pliku JSON dla języka angielskiego (pl). Można nadal zmieniać etykiety i inne ciągi przez przekazywanie zasobów języka w celu dostosowania języka.

### <a name="up-to-date-translations"></a>Aktualne tłumaczenia

Firma Microsoft dokłada starań, aby zapewnić najbardziej aktualne tłumaczenia do użycia. Firma Microsoft stale ulepsza tłumaczenia i zapewnia ich zgodność. Firma Microsoft zidentyfikuje usterki i zmiany w globalnej terminologii oraz wprowadzi aktualizacje, które bezproblemowo pracują w przepływie użytkownika.

### <a name="support-for-right-to-left-languages"></a>Obsługa języków pisanych od prawej do lewej

Firma Microsoft obecnie nie obsługuje języków pisanych od prawej do lewej. Można to zrobić za pomocą niestandardowych ustawień regionalnych i CSS, aby zmienić sposób wyświetlania ciągów. Jeśli potrzebujesz tej funkcji, zagłosuj na opinię na temat [platformy Azure](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).

### <a name="social-identity-provider-translations"></a>Tłumaczenia dostawcy tożsamości społecznościowej

Firma Microsoft udostępnia `ui_locales` parametr OIDC na potrzeby logowania do sieci społecznościowych. Jednak niektórzy dostawcy tożsamości społecznościowych, w tym Facebook i Google, nie honorli.

### <a name="browser-behavior"></a>Zachowanie przeglądarki

Programy Chrome i Firefox żądają swojego języka zestawu. Jeśli jest to obsługiwany język, jest on wyświetlany przed ustawieniem domyślnym. Przeglądarka Microsoft Edge obecnie nie żąda języka i przechodzi bezpośrednio do języka domyślnego.

## <a name="supported-languages"></a>Obsługiwane języki

Azure AD B2C obejmuje obsługę następujących języków. Języki przepływu użytkownika są udostępniane przez Azure AD B2C. Języki powiadomień usługi uwierzytelniania wieloskładnikowego (MFA) są udostępniane przez [usługę Azure MFA](../active-directory/authentication/concept-mfa-howitworks.md).

| Język              | Kod języka | Przepływy użytkowników         | Powiadomienia usługi MFA  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Arabski                | ty            | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Bułgarski             | BG            | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Języku                | mld USD            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) |
| Kataloński               | urzędu certyfikacji            | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Czeski                 | Rejestr            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Duński                | da            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Niemiecki                | de            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Grecki                 | Colon            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Angielski               | en            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Hiszpański               | es            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Estoński              | staw            | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Baskijski                | Europejska            | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Fiński               | fi            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Francuski                | fr            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Galicyjski              | GL            | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Gudżarati              | gu            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) |
| Hebrajski                | Przewodniczący            | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Hindi                 | Cześć            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Chorwacki              | godz.            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Węgierski             | Węgry            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Indonezyjski            | identyfikator            | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Włoski               | it            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| japoński              | ja            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Kazachski                | kk            | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Kannada               | kN            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) |
| Koreański                | Ko            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Litewski            | lt            | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Łotewski               | LV            | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Malayalam             | ml            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) |
| Marathi               | Pan            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) |
| Malajski                 | Arial            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Norweski (Bokmal)      | sesja            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) |
| Niderlandzki                 | nl            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Norweski             | nie            | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Pendżabski               | biur            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) |
| Polski                | zysków            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Portugalski (Brazylia)   | pt-br         | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Portugalski (Portugalia) | pt-pt         | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Rumuński              | ro            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Rosyjski               | ru            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Słowacki                | SK            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Słoweński             | SL            | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Serbski – cyrylica    | Wirtualizacja SR-cryl-CS    | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Serbski – łaciński       | Wirtualizacja sr-latn-cs    | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Szwedzki               | sv            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Tamilski                 | Ta            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) |
| Telugu                | Usuń            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) |
| Tajlandzki                  | th            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Turecki               | zdawczy            | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Ukraiński             | Południowe Zjednoczone Królestwo            | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Wietnamski            | VI            | ![Znak X wskazujący nie.](./media/user-flow-language-customization/no.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Chiński – uproszczony  | zh-Hans       | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
| Chiński – tradycyjny | zh-Hant       | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) | ![Zielony znacznik wyboru.](./media/user-flow-language-customization/yes.png) |
