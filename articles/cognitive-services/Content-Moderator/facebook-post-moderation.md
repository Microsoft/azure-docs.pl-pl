---
title: 'Samouczek: umiarkowana zawartość w serwisie Facebook — Content Moderator'
titleSuffix: Azure Cognitive Services
description: W tym samouczku dowiesz się, jak użyć usługi Content Moderator opartej na uczeniu maszynowym do ułatwienia moderowania wpisów i komentarzy w serwisie Facebook.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 10/05/2020
ms.author: pafarley
ms.openlocfilehash: e930e5d125a8f1ee90448e293e2e0ca2c5c28465
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913674"
---
# <a name="tutorial-moderate-facebook-posts-and-commands-with-azure-content-moderator"></a>Samouczek: umiarkowane wpisy i polecenia w usłudze Facebook przy użyciu usługi Azure Content Moderator

W ramach tego samouczka nauczysz się, jak używać usługi Azure Content Moderator, aby ułatwić umiarkowane wpisy i komentarze na stronie w serwisie Facebook. Usługa Facebook wyśle zawartość opublikowaną przez odwiedzających do usługi Content Moderator. Następnie przepływy pracy Content Moderator będą publikować zawartość lub tworzyć przeglądy w ramach narzędzia do przeglądu, w zależności od wyników i progów zawartości. Zobacz [film wideo z pokazem 2017](https://channel9.msdn.com/Events/Build/2017/T6033) , aby zapoznać się z przykładowym scenariuszem.

> [!IMPORTANT]
> W 2018 w serwisie Facebook zaimplementowano bardziej rygorystyczne zasady przed sprawdzeniemymi dla aplikacji w serwisie Facebook. Nie będzie można wykonać kroków tego samouczka, jeśli Twoja aplikacja nie została przejrzana i zatwierdzona przez zespół ds. przeglądania w serwisie Facebook.

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zespołu usługi Content Moderator.
> * Tworzenie usługi Azure Functions, która nasłuchuje zdarzeń HTTP z usługi Content Moderator i serwisu Facebook.
> * Połącz stronę w serwisie Facebook, aby Content Moderator przy użyciu aplikacji w serwisie Facebook.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/cognitive-services/).

Ten diagram ilustruje każdy składnik tego scenariusza:

![Diagram Content Moderator otrzymywanie informacji z usługi Facebook przez "FBListener" i wysyłanie informacji za pośrednictwem "CMListener"](images/tutorial-facebook-moderation.png)

## <a name="prerequisites"></a>Wymagania wstępne

- Klucz subskrypcji pakietu Content Moderator. Aby zasubskrybować usługę Content Moderator i uzyskać klucz, postępuj zgodnie z instrukcjami z tematu [Create a Cognitive Services account (Tworzenie konta usług Cognitive Services)](../cognitive-services-apis-create-account.md).
- [Konto w serwisie Facebook](https://www.facebook.com/).

## <a name="create-a-review-team"></a>Tworzenie zespołu do przeprowadzania przeglądu

Zapoznaj się z instrukcją [Try Content moderator w sieci Web](quick-start.md) przewodnik szybkiego startu, aby uzyskać instrukcje dotyczące rejestrowania się w celu uzyskania [narzędzia do przeglądu Content moderator](https://contentmoderator.cognitive.microsoft.com/) i tworzenia zespołu przeglądu. Zwróć uwagę na wartość **Identyfikator zespołu** na stronie **Poświadczenia** .

## <a name="configure-image-moderation-workflow"></a>Konfigurowanie przepływu pracy moderowania obrazu

Zapoznaj się z podręcznikiem [Definiowanie, testowanie i używanie przepływów pracy,](review-tool-user-guide/workflows.md) aby utworzyć niestandardowy przepływ pracy obrazu. Content Moderator będzie używać tego przepływu pracy do automatycznego sprawdzania obrazów w serwisie Facebook i wysyłania niektórych do narzędzia do przeglądu. Zanotuj **nazwę** przepływu pracy.

## <a name="configure-text-moderation-workflow"></a>Konfigurowanie przepływu pracy moderowania tekstu

Ponownie zapoznaj się z podręcznikiem [Definiowanie, testowanie i używanie przepływów pracy](review-tool-user-guide/workflows.md) ; tym razem Utwórz niestandardowy przepływ pracy tekstu. Content Moderator będzie używać tego przepływu pracy do automatycznego sprawdzania zawartości tekstowej. Zanotuj **nazwę** przepływu pracy.

![Konfigurowanie przepływu pracy tekstu](images/text-workflow-configure.PNG)

Przetestuj przepływ pracy za pomocą przycisku **Wykonaj przepływ pracy** .

![Testowanie przepływu pracy tekstu](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Tworzenie usługi Azure Functions

Zaloguj się do [Azure Portal](https://portal.azure.com/) i wykonaj następujące kroki:

1. Utwórz aplikację usługi Azure Functions, jak pokazano na stronie usługi [Azure Functions](../../azure-functions/functions-create-function-app-portal.md).
1. Przejdź do nowo utworzonego aplikacja funkcji.
1. W aplikacji przejdź do karty **funkcje platformy** i wybierz pozycję **Konfiguracja** . W sekcji **Ustawienia aplikacji** na następnej stronie wybierz pozycję **nowe ustawienie aplikacji** , aby dodać następujące pary klucz/wartość:
    
    | Nazwa ustawienia aplikacji | wartość   | 
    | -------------------- |-------------|
    | `cm:TeamId`   | Identyfikator zespołu usługi Content Moderator  | 
    | `cm:SubscriptionKey` | Klucz subskrypcji usługi Content Moderator — zobacz [Poświadczenia](./review-tool-user-guide/configure.md#credentials) |
    | `cm:Region` | Nazwa regionu usługi Content Moderator, bez spacji. Tę nazwę można znaleźć w polu **Lokalizacja** na karcie **Przegląd** zasobu platformy Azure.|
    | `cm:ImageWorkflow` | Nazwa przepływu pracy do uruchomienia na obrazach |
    | `cm:TextWorkflow` | Nazwa przepływu pracy do uruchomienia na tekście |
    | `cm:CallbackEndpoint` | Adres URL CMListener aplikacja funkcji, który zostanie utworzony w dalszej części tego przewodnika |
    | `fb:VerificationToken` | Utworzony przez Ciebie token tajny, używany do subskrybowania zdarzeń kanału informacyjnego serwisu Facebook |
    | `fb:PageAccessToken` | Token dostępu do interfejsu API Graph serwisu Facebook nie wygasa i umożliwia funkcji ukrywanie i usuwanie wpisów w Twoim imieniu. Ten token zostanie wyświetlony w późniejszym kroku. |

    Kliknij przycisk **Zapisz** znajdujący się u góry strony.

1. Wróć do karty **funkcje platformy** . Użyj **+** przycisku w okienku po lewej stronie, aby wyświetlić **nowe okienko funkcji** . Funkcja, którą zamierzasz utworzyć, będzie odbierać zdarzenia z serwisu Facebook.

    ![Azure Functions okienku z wyróżnionym przyciskiem Dodaj funkcję.](images/new-function.png)

    1. Kliknij kafelek z **wyzwalaczem http** .
    1. Wprowadź nazwę **FBListener** . W polu **Authorization Level** (Poziom autoryzacji) powinna być ustawiona wartość **Function** (Funkcja).
    1. Kliknij pozycję **Utwórz** .
    1. Zastąp zawartość elementu **Run. CSX** zawartością z **FbListener/Run. CSX**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/FbListener/run.csx?range=1-154)]

1. Utwórz nową funkcję **wyzwalacza http** o nazwie **CMListener** . Ta funkcja odbiera zdarzenia z usługi Content Moderator. Zastąp zawartość elementu **Run. CSX** zawartością z **CMListener/Run. CSX**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/CmListener/run.csx?range=1-110)]

---

## <a name="configure-the-facebook-page-and-app"></a>Konfigurowanie aplikacji i strony w serwisie Facebook

1. Utwórz aplikację serwisu Facebook.

    ![Strona dewelopera usługi Facebook](images/facebook-developer-app.png)

    1. Przejdź do [witryny dewelopera serwisu Facebook](https://developers.facebook.com/)
    1. Kliknij pozycję **Moje aplikacje** .
    1. Dodaj nową aplikację.
    1. Nadaj mu nazwę
    1. Wybieranie elementów **webhook — konfiguracja >**
    1. Wybierz pozycję **Strona** w menu rozwijanym i wybierz pozycję **Subskrybuj ten obiekt** .
    1. Podaj **adres URL funkcji FBListener** jako adres URL wywołania zwrotnego i **zweryfikuj token** skonfigurowany w obszarze **ustawień aplikacji funkcji**
    1. Po zasubskrybowaniu przewiń w dół do kanału informacyjnego, a następnie wybierz pozycję **subskrybuj** .
    1. Kliknij przycisk **Testuj** w wierszu **kanału informacyjnego** , aby wysłać wiadomość testową do funkcji FBListener platformy Azure, a następnie naciśnij przycisk **Wyślij do mojego serwera** . Powinno zostać wyświetlone żądanie odbierane w FBListener.

1. Utwórz stronę serwisu Facebook.

    > [!IMPORTANT]
    > W 2018 w serwisie Facebook zaimplementowano bardziej rygorystyczne przed sprawdzeniem aplikacji w serwisie Facebook. Nie będzie można wykonywać sekcji 2, 3 i 4, jeśli Twoja aplikacja nie została przejrzana i zatwierdzona przez zespół ds. przeglądania w serwisie Facebook.

    1. Przejdź do serwisu [Facebook](https://www.facebook.com/bookmarks/pages) i utwórz **nową stronę w serwisie Facebook** .
    1. Zezwól aplikacji serwisu Facebook na dostęp do tej strony, wykonując następujące czynności:
        1. Przejdź do [eksploratora interfejsu API Graph](https://developers.facebook.com/tools/explorer/).
        1. Wybierz pozycję **aplikacja** .
        1. Wybierz pozycję **Token dostępu do strony** i wyślij żądanie **Get** .
        1. Kliknij pozycję **identyfikatora strony** w odpowiedzi.
        1. Teraz dołącz ciąg **/subscribed_apps** w adresie URL i wyślij żądanie **Get** (pusta odpowiedź).
        1. Prześlij żądanie **Post** . Uzyskasz odpowiedź w formie **success: true** .

3. Utwórz niewygasający token dostępu do interfejsu API Graph.

    1. Przejdź do [eksploratora interfejsu API Graph](https://developers.facebook.com/tools/explorer/).
    2. Wybierz opcję **Aplikacja** .
    3. Wybierz opcję **Uzyskaj token dostępu użytkownika** .
    4. W obszarze **Wybierz uprawnienia** wybierz opcję **manage_pages** i **publish_pages** .
    5. Użyjemy **tokenu dostępu** (token krótkotrwały) w następnym kroku.

4. W kilku następnych krokach będziemy używać narzędzia Postman.

    1. Otwórz narzędzie **Postman** (lub pobierz je [tutaj](https://www.getpostman.com/)).
    2. Zaimportuj te dwa pliki:
        1. [Postman Collection](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/Facebook%20Permanant%20Page%20Access%20Token.postman_collection.json)
        2. [Postman Environment](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FB%20Page%20Access%20Token%20Environment.postman_environment.json)       
    3. Zaktualizuj te zmienne środowiskowe:
    
        | Klucz | Wartość   | 
        | -------------------- |-------------|
        | appId   | Tutaj wstaw identyfikator aplikacji serwisu Facebook  | 
        | appSecret | Tutaj wstaw wpis tajny aplikacji serwisu Facebook | 
        | short_lived_token | Wstaw krótkotrwały token dostępu użytkownika, który został wygenerowany w poprzednim kroku |
    4. Teraz uruchom 3 interfejsy API wymienione w kolekcji: 
        1. Wybierz pozycję **Generate Long-Lived Access Token** (Wygeneruj długotrwały token dostępu) i kliknij przycisk **Send** (Wyślij).
        2. Wybierz pozycję **Get User ID** (Uzyskaj identyfikator użytkownika) i kliknij przycisk **Send** (Wyślij).
        3. Wybierz pozycję **Get Permanent Page Access Token** (Uzyskaj trwały token dostępu do strony) i kliknij przycisk **Send** (Wyślij).
    5. Skopiuj wartość **access_token** z odpowiedzi i przypisz ją do ustawienia aplikacji **fb:PageAccessToken** .

Rozwiązanie wysyła wszystkie obrazy i tekst opublikowane na stronie serwisu Facebook do usługi Content Moderator. Następnie są wywoływane przepływy pracy, które zostały wcześniej skonfigurowane. Zawartość, która nie przekazuje kryteriów zdefiniowanych w przepływach pracy, zostaje przekazana do przeglądu w ramach narzędzia do przeglądu. Pozostała część zawartości zostanie opublikowana automatycznie.

## <a name="next-steps"></a>Następne kroki

Podczas pracy z tym samouczkiem skonfigurowano program do analizowania obrazów produktów w celu oznaczenia ich tagami określającymi typ produktu i ułatwienia zespołowi przeprowadzającemu przegląd podejmowanie świadomych decyzji dotyczących moderacji treści. Teraz możesz dowiedzieć się więcej o szczegółach moderowania obrazów.

> [!div class="nextstepaction"]
> [Moderowanie obrazów](./image-moderation-api.md)