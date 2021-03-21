---
title: Dodawanie i wywoływanie funkcji z Azure Logic Apps
description: Wywoływanie i uruchamianie niestandardowego kodu w funkcjach wykonanych na platformie Azure z zautomatyzowanych zadań i przepływów pracy w Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/01/2019
ms.custom: devx-track-js
ms.openlocfilehash: 7df9f7d072af7c5f6523fd1be0432ce51954fa10
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791883"
---
# <a name="call-functions-from-azure-logic-apps"></a>Wywoływanie funkcji z Azure Logic Apps

Gdy chcesz uruchomić kod, który wykonuje określone zadanie w usłudze Logic Apps, możesz utworzyć własną funkcję przy użyciu [Azure Functions](../azure-functions/functions-overview.md). Ułatwia to utworzenie funkcji w środowiskach Node.js, C# i F# bez konieczności tworzenia pełnej aplikacji lub infrastruktury w celu uruchomienia kodu. Możesz również [wywoływać aplikacje logiki z funkcji wewnątrz](#call-logic-app). Azure Functions zapewnia obsługę obliczeń bezserwerowych w chmurze i jest przydatna do wykonywania zadań, takich jak następujące przykłady:

* Zwiększ zachowanie aplikacji logiki przy użyciu funkcji w Node.js lub C#.
* Wykonaj obliczenia w przepływie pracy aplikacji logiki.
* Stosuj Zaawansowane formatowanie lub pola obliczeniowe w usłudze Logic Apps.

Aby uruchomić fragmenty kodu bez używania Azure Functions, Dowiedz się, jak [dodać i uruchomić kod wbudowany](../logic-apps/logic-apps-add-run-inline-code.md).

> [!NOTE]
> Integracja między Logic Apps i Azure Functions obecnie nie działa z włączonymi gniazdami.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Aplikacja funkcji, która jest kontenerem dla funkcji utworzonej w Azure Functions, wraz z utworzoną funkcją. Jeśli nie masz aplikacji funkcji, [najpierw Utwórz aplikację funkcji](../azure-functions/functions-get-started.md). Następnie możesz utworzyć funkcję spoza aplikacji logiki w Azure Portal lub [z poziomu aplikacji logiki](#create-function-designer) w Projektancie aplikacji logiki.

* Podczas pracy z usługą Logic Apps te same wymagania dotyczą aplikacji funkcji i funkcji, niezależnie od tego, czy są one istniejące, czy nowe:

  * Aplikacja funkcji i aplikacja logiki muszą używać tej samej subskrypcji platformy Azure.

  * Nowe aplikacje funkcji muszą używać platformy .NET lub JavaScript jako stosu środowiska uruchomieniowego. Po dodaniu nowej funkcji do istniejących aplikacji funkcji można wybrać opcję C# lub JavaScript.

  * Funkcja używa szablonu **wyzwalacza http** .

    Szablon wyzwalacza HTTP może akceptować zawartość, która ma `application/json` Typ z aplikacji logiki. Gdy dodasz funkcję do aplikacji logiki, projektant aplikacji logiki wyświetli niestandardowe funkcje, które są tworzone na podstawie tego szablonu w ramach subskrypcji platformy Azure.

  * Funkcja nie korzysta z tras niestandardowych, chyba że zdefiniowano [definicję openapi](../azure-functions/functions-openapi-definition.md) (wcześniej znaną jako [plik Swagger](https://swagger.io/)).

  * Jeśli masz definicję OpenAPI dla swojej funkcji, Projektant Logic Apps oferuje bogatsze środowisko pracy z parametrami funkcji. Zanim aplikacja logiki będzie mogła znajdować i uzyskiwać dostęp do funkcji, które mają definicje OpenAPI, [Skonfiguruj aplikację funkcji, wykonując następujące kroki](#function-swagger).

* Aplikacja logiki, do której chcesz dodać funkcję, w tym [wyzwalacz](../logic-apps/logic-apps-overview.md#logic-app-concepts) jako pierwszy krok w aplikacji logiki

  Aby można było dodawać akcje, które uruchamiają funkcje, aplikacja logiki musi rozpoczynać się od wyzwalacza. Jeśli dopiero zaczynasz tworzyć aplikacje logiki, zapoznaj [się z tematem Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [Szybki Start: Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="function-swagger"></a>

## <a name="find-functions-that-have-openapi-descriptions"></a>Znajdź funkcje, które mają opisy OpenAPI

Aby uzyskać bardziej zaawansowane środowisko pracy z parametrami funkcji w projektancie Logic Apps, [Wygeneruj definicję openapi](../azure-functions/functions-openapi-definition.md), wcześniej znaną jako [plik struktury Swagger](https://swagger.io/), dla funkcji. Aby skonfigurować aplikację funkcji, tak aby aplikacja logiki mogła znaleźć i używać funkcji, które mają opis struktury Swagger, wykonaj następujące kroki:

1. Upewnij się, że aplikacja funkcji jest aktywnie uruchomiona.

1. W aplikacji funkcji Skonfiguruj [współużytkowanie zasobów między źródłami (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) , tak aby wszystkie źródła były dozwolone, wykonując następujące czynności:

   1. Z listy **aplikacje funkcji** wybierz aplikację funkcji. W okienku po prawej stronie wybierz pozycję **funkcje platformy**  >  **CORS**.

      ![Wybierz aplikację funkcji > "funkcje platformy" > "CORS"](./media/logic-apps-azure-functions/function-platform-features-cors.png)

   1. W obszarze **CORS** Dodaj **`*`** symbol wieloznaczny gwiazdki (), ale Usuń wszystkie inne źródła z listy i wybierz pozycję **Zapisz**.

      ![Ustaw wartość "CORS * na symbol wieloznaczny" * "](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

## <a name="access-property-values-inside-http-requests"></a>Wartości właściwości dostępu w żądaniach HTTP

Funkcje elementu webhook mogą akceptować żądania HTTP jako dane wejściowe i przekazywać te żądania do innych funkcji. Na przykład, chociaż Logic Apps ma [funkcje, które konwertują wartości DateTime](../logic-apps/workflow-definition-language-functions-reference.md), ta podstawowa Przykładowa funkcja JavaScript pokazuje, w jaki sposób można uzyskać dostęp do właściwości wewnątrz obiektu żądania, który jest przesyłany do funkcji i wykonywać operacje na tej wartości właściwości. Aby uzyskać dostęp do właściwości wewnątrz obiektów, w tym przykładzie jest stosowany [operator kropki (.)](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors):

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

Oto co się dzieje w tej funkcji:

1. Funkcja tworzy `data` zmienną i przypisuje `body` obiekt wewnątrz `request` obiektu do tej zmiennej. Funkcja używa operatora kropki (.) do odwoływania się do `body` obiektu wewnątrz `request` obiektu:

   ```javascript
   var data = request.body;
   ```

1. Funkcja może teraz uzyskiwać dostęp do `date` właściwości za pośrednictwem `data` zmiennej i przekonwertować tę wartość właściwości z typu DateTime na typ DateString przez wywołanie `ToDateString()` funkcji. Funkcja zwraca również wynik przez `body` Właściwość w odpowiedzi funkcji:

   ```javascript
   body: data.date.ToDateString();
   ```

Teraz, po utworzeniu funkcji na platformie Azure, wykonaj kroki, aby [dodać funkcje do usługi Logic Apps](#add-function-logic-app).

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>Tworzenie funkcji w usłudze Logic Apps

Możesz tworzyć funkcje bezpośrednio z przepływu pracy aplikacji logiki przy użyciu wbudowanej akcji Azure Functions w Projektancie aplikacji logiki, ale tej metody można używać tylko w przypadku funkcji pisanych w języku JavaScript. W przypadku innych języków można tworzyć funkcje za pomocą środowiska Azure Functions w Azure Portal. Aby uzyskać więcej informacji, zobacz [Tworzenie pierwszej funkcji w Azure Portal](../azure-functions/functions-get-started.md).

Jednak zanim będzie można utworzyć funkcję na platformie Azure, musisz mieć już aplikację funkcji, która jest kontenerem dla funkcji. Jeśli nie masz aplikacji funkcji, najpierw Utwórz tę aplikację funkcji. Zobacz [Tworzenie pierwszej funkcji w Azure Portal](../azure-functions/functions-get-started.md).

1. W [Azure Portal](https://portal.azure.com)Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. Aby utworzyć i dodać funkcję, wykonaj czynności opisane w tym scenariuszu:

   * W ostatnim kroku przepływu pracy aplikacji logiki wybierz pozycję **nowy krok**.

   * Między istniejącymi krokami przepływu pracy aplikacji logiki Przenieś wskaźnik myszy nad strzałkę, wybierz znak plus (+), a następnie wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania wprowadź ciąg "Azure Functions" jako filtr. Z listy Akcje wybierz akcję **Wybierz funkcję platformy Azure** , na przykład:

   ![Znajdź funkcje w Azure Portal.](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. Z listy Aplikacje funkcji wybierz aplikację funkcji. Po otwarciu listy akcji wybierz tę akcję: **Utwórz nową funkcję**.

   ![Wybierz aplikację funkcji](./media/logic-apps-azure-functions/select-function-app-create-function.png)

1. W edytorze definicji funkcji Zdefiniuj funkcję:

   1. W polu **nazwa funkcji** Podaj nazwę funkcji.

   1. W polu **kod** Dodaj kod do szablonu funkcji, łącznie z odpowiedzią i ładunkiem, która ma zostać zwrócona do aplikacji logiki po zakończeniu działania funkcji. Gdy wszystko będzie gotowe, wybierz przycisk **Utwórz**.

   Na przykład:

   ![Zdefiniuj funkcję](./media/logic-apps-azure-functions/add-code-function-definition.png)

   W kodzie szablonu *`context` obiekt* odwołuje się do wiadomości wysyłanej przez aplikację logiki za pomocą pola **treści żądania** w późniejszym kroku. Aby uzyskać dostęp do `context` właściwości obiektu z wewnątrz funkcji, użyj następującej składni:

   `context.body.<property-name>`

   Na przykład, aby odwołać się do `content` Właściwości wewnątrz `context` obiektu, użyj następującej składni:

   `context.body.content`

   Kod szablonu zawiera również `input` zmienną, która przechowuje wartość z `data` parametru, aby funkcja mogła wykonywać operacje na tej wartości. Wewnątrz funkcji języka JavaScript `data` zmienna jest również skrótem dla `context.body` .

   > [!NOTE]
   > `body`Właściwość w tym miejscu dotyczy `context` obiektu i nie jest taka sama jak token **treści** z danych wyjściowych akcji, które można również przekazać do funkcji.

1. W polu **treść żądania** podaj dane wejściowe funkcji, które muszą być sformatowane jako obiekt JavaScript Object Notation (JSON).

   To dane wejściowe to *obiekt kontekstu* lub komunikat, który aplikacja logiki wysyła do funkcji. Po kliknięciu w polu **treść żądania** zostanie wyświetlona lista zawartości dynamicznej, aby można było wybrać tokeny dla danych wyjściowych z poprzednich kroków. Ten przykład określa, że ładunek kontekstu zawiera właściwość o nazwie `content` , która ma wartość **z z** wyzwalacza poczty e-mail.

   ![Przykład "treść żądania" — ładunek obiektu kontekstu](./media/logic-apps-azure-functions/function-request-body-example.png)

   W tym miejscu obiekt kontekstu nie jest rzutowany jako ciąg, więc zawartość obiektu jest dodawana bezpośrednio do ładunku JSON. Jeśli jednak obiekt kontekstu nie jest tokenem JSON, który przekazuje ciąg, obiekt JSON lub tablicę JSON, pojawia się błąd. Tak więc, jeśli w tym przykładzie użyto tokenu **otrzymanego czasu** zamiast tego, można rzutować obiekt kontekstu jako ciąg, dodając znaki podwójnego cudzysłowu.

   ![Rzutowanie obiektu jako ciągu](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Aby określić inne szczegóły, takie jak Metoda używania, nagłówki żądań lub parametry zapytania lub uwierzytelnianie, Otwórz listę **Dodaj nowe parametry** i wybierz odpowiednie opcje. W przypadku uwierzytelniania Opcje różnią się w zależności od wybranej funkcji. Zobacz [Włączanie uwierzytelniania dla funkcji](#enable-authentication-functions).

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>Dodawanie istniejących funkcji do aplikacji logiki

Aby wywołać istniejące funkcje z usługi Logic Apps, można dodać funkcje takie jak wszystkie inne akcje w Projektancie aplikacji logiki.

1. W [Azure Portal](https://portal.azure.com)Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W kroku, w którym chcesz dodać funkcję, wybierz pozycję **nowy krok**.

1. W obszarze **Wybierz akcję** w polu wyszukiwania wprowadź ciąg "Azure Functions" jako filtr. Z listy Akcje wybierz akcję **Wybierz funkcję platformy Azure** .

   ![Znajdź funkcję na platformie Azure.](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. Z listy Aplikacje funkcji wybierz aplikację funkcji. Gdy zostanie wyświetlona lista funkcji, wybierz funkcję.

   ![Wybierz aplikację funkcji i funkcję](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   W przypadku funkcji, które mają definicje interfejsu API (opis struktury Swagger) i są [skonfigurowane tak, aby aplikacja logiki mogła znaleźć te funkcje i uzyskać do nich dostęp](#function-swagger), można wybrać **Akcje struktury Swagger**.

   ![Wybierz aplikację funkcji, "akcje programu Swagger" i swoją funkcję](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

1. W polu **treść żądania** podaj dane wejściowe funkcji, które muszą być sformatowane jako obiekt JavaScript Object Notation (JSON).

   To dane wejściowe to *obiekt kontekstu* lub komunikat, który aplikacja logiki wysyła do funkcji. Po kliknięciu w polu **treść żądania** zostanie wyświetlona lista zawartości dynamicznej, dzięki czemu można wybrać tokeny dla danych wyjściowych z poprzednich kroków. Ten przykład określa, że ładunek kontekstu zawiera właściwość o nazwie `content` , która ma wartość **z z** wyzwalacza poczty e-mail.

   ![Przykład "treść żądania" — ładunek obiektu kontekstu](./media/logic-apps-azure-functions/function-request-body-example.png)

   W tym miejscu obiekt kontekstu nie jest rzutowany jako ciąg, więc zawartość obiektu jest dodawana bezpośrednio do ładunku JSON. Jeśli jednak obiekt kontekstu nie jest tokenem JSON, który przekazuje ciąg, obiekt JSON lub tablicę JSON, pojawia się błąd. Tak więc, jeśli w tym przykładzie użyto tokenu **otrzymany czas** zamiast tego, można rzutować obiekt kontekstu jako ciąg, dodając cudzysłów:

   ![Rzutowanie obiektu jako ciągu](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Aby określić inne szczegóły, takie jak metoda do użycia, nagłówki żądania, parametry zapytania lub uwierzytelnianie, Otwórz listę **Dodaj nowy parametr** i wybierz odpowiednie opcje. W przypadku uwierzytelniania Opcje różnią się w zależności od wybranej funkcji. Zobacz [Włączanie uwierzytelniania w funkcjach](#enable-authentication-functions).

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-functions"></a>Wywoływanie aplikacji logiki z funkcji

Gdy chcesz wyzwolić aplikację logiki z wewnątrz funkcji, aplikacja logiki musi rozpoczynać się od wyzwalacza, który udostępnia możliwy do przekroczenia punkt końcowy. Na przykład możesz uruchomić aplikację logiki za pomocą wyzwalacza **http**, **żądania**, **Azure Queues** lub **Event Grid** . Wewnątrz funkcji Wyślij żądanie HTTP POST do adresu URL wyzwalacza i Dołącz ładunek, który ma być przetwarzany przez aplikację logiki. Aby uzyskać więcej informacji, zobacz [wywoływanie, wyzwalanie lub zagnieżdżanie aplikacji logiki](../logic-apps/logic-apps-http-endpoint.md).

<a name="enable-authentication-functions"></a>

## <a name="enable-authentication-for-functions"></a>Włącz uwierzytelnianie dla funkcji

Aby w prosty sposób uwierzytelniać dostęp do innych zasobów chronionych przez usługę Azure Active Directory (Azure AD) bez konieczności logowania się i dostarczania poświadczeń lub wpisów tajnych, aplikacja logiki może korzystać z [tożsamości zarządzanej](../active-directory/managed-identities-azure-resources/overview.md) (znanej wcześniej jako tożsamość usługi ZARZĄDZANEJ lub MSI). Platforma Azure zarządza tą tożsamością i pomaga zabezpieczyć poświadczenia, ponieważ dzięki temu nie musisz dostarczać ani rotować wpisów tajnych. Dowiedz się więcej [na temat usług platformy Azure, które obsługują tożsamości zarządzane na potrzeby uwierzytelniania w usłudze Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

W przypadku skonfigurowania aplikacji logiki do korzystania z tożsamości przypisanej do systemu lub ręcznie utworzonej tożsamości przypisanej przez użytkownika funkcja w aplikacji logiki może także używać tej samej tożsamości do uwierzytelniania. Aby uzyskać więcej informacji na temat obsługi uwierzytelniania dla funkcji usługi Logic Apps, zobacz [Dodawanie uwierzytelniania do połączeń wychodzących](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

Aby skonfigurować tożsamość zarządzaną i korzystać z niej, wykonaj następujące kroki:

1. Włącz zarządzaną tożsamość w aplikacji logiki i Skonfiguruj dostęp tej tożsamości do zasobu docelowego. Zobacz temat [uwierzytelnianie dostępu do zasobów platformy Azure przy użyciu tożsamości zarządzanych w Azure Logic Apps](../logic-apps/create-managed-service-identity.md).

1. Włącz uwierzytelnianie w aplikacji funkcji i funkcji, wykonując następujące czynności:

   * [Konfigurowanie uwierzytelniania anonimowego w funkcji](#set-authentication-function-app)
   * [Konfigurowanie uwierzytelniania usługi Azure AD w aplikacji funkcji](#set-azure-ad-authentication)

<a name="set-authentication-function-app"></a>

### <a name="set-up-anonymous-authentication-in-your-function"></a>Konfigurowanie uwierzytelniania anonimowego w funkcji

Aby użyć zarządzanej tożsamości aplikacji logiki w funkcji, należy ustawić poziom uwierzytelniania funkcji na anonimowy. W przeciwnym razie aplikacja logiki zgłosi błąd "nieprawidłowego żądania".

1. W [Azure Portal](https://portal.azure.com)Znajdź i wybierz swoją aplikację funkcji. W tych krokach użyto "FabrikamFunctionApp" jako przykładowej aplikacji funkcji.

1. W okienku aplikacja funkcji wybierz pozycję **funkcje platformy**. W obszarze **Narzędzia programistyczne** wybierz pozycję **Narzędzia zaawansowane (kudu)**.

   ![Otwórz zaawansowane narzędzia dla kudu](./media/logic-apps-azure-functions/open-advanced-tools-kudu.png)

1. Na pasku tytułu witryny sieci Web kudu w menu **konsoli debugowania** wybierz polecenie **cmd**.

   ![Z menu konsoli debugowania wybierz opcję "CMD"](./media/logic-apps-azure-functions/open-debug-console-kudu.png)

1. Gdy zostanie wyświetlona Następna strona, na liście folder wybierz pozycję **Witryna**  >  **wwwroot**  >  *Funkcja*. W tych krokach użyto "FabrikamAzureFunction" jako funkcji przykładowej.

   ![Wybierz pozycję "Witryna" > "wwwroot" > funkcji](./media/logic-apps-azure-functions/select-site-wwwroot-function-folder.png)

1. Otwórz `function.json` plik do edycji.

   ![Kliknij pozycję Edytuj dla pliku "function.json"](./media/logic-apps-azure-functions/edit-function-json-file.png)

1. `bindings`Sprawdź, czy właściwość istnieje w obiekcie `authLevel` . Jeśli właściwość istnieje, ustaw wartość właściwości na `anonymous` . W przeciwnym razie Dodaj tę właściwość i ustaw wartość.

   ![Dodaj właściwość "authLevel" i ustaw wartość "Anonymous"](./media/logic-apps-azure-functions/set-authentication-level-function-app.png)

1. Gdy skończysz, Zapisz ustawienia, a następnie przejdź do następnej sekcji.

<a name="set-azure-ad-authentication"></a>

### <a name="set-up-azure-ad-authentication-for-your-function-app"></a>Konfigurowanie uwierzytelniania usługi Azure AD dla aplikacji funkcji

Przed rozpoczęciem tego zadania Znajdź i Umieść te wartości w celu późniejszego użycia:

* Identyfikator obiektu, który jest generowany dla tożsamości przypisanej do systemu reprezentującej aplikację logiki

  * Aby wygenerować ten identyfikator obiektu, [Włącz tożsamość przypisaną przez system do aplikacji logiki](../logic-apps/create-managed-service-identity.md#azure-portal-system-logic-app).

  * W przeciwnym razie aby znaleźć ten identyfikator obiektu, Otwórz aplikację logiki w Projektancie aplikacji logiki. W menu aplikacji logiki w obszarze **Ustawienia** wybierz pozycję   >  **przypisany system** tożsamości.

* Identyfikator katalogu dla dzierżawy w Azure Active Directory (Azure AD)

  Aby uzyskać identyfikator katalogu dzierżawy, możesz uruchomić [`Get-AzureAccount`](/powershell/module/servicemanagement/azure.service/get-azureaccount) polecenie programu PowerShell. Lub w Azure Portal wykonaj następujące kroki:

  1. W [Azure Portal](https://portal.azure.com)Znajdź i wybierz swoją aplikację funkcji.

  1. Znajdź i wybierz dzierżawę usługi Azure AD. W tych krokach użyto "Fabrikam" jako przykładowej dzierżawy.

  1. W menu dzierżawca w obszarze **Zarządzaj** wybierz pozycję **Właściwości**.

  1. Skopiuj identyfikator katalogu dzierżawy, na przykład i Zapisz ten identyfikator do późniejszego użycia.

     ![Znajdź i skopiuj identyfikator katalogu dzierżawy usługi Azure AD](./media/logic-apps-azure-functions/azure-active-directory-tenant-id.png)

* Identyfikator zasobu dla zasobu docelowego, do którego chcesz uzyskać dostęp

  * Aby znaleźć te identyfikatory zasobów, przejrzyj [usługi platformy Azure, które obsługują usługę Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

  > [!IMPORTANT]
  > Ten identyfikator zasobu musi być dokładnie dopasowany do wartości oczekiwanej przez usługę Azure AD, w tym wszystkich wymaganych końcowych ukośników.

  Ten identyfikator zasobu jest również tą samą wartością, która później jest używana we właściwości **odbiorców** podczas [konfigurowania akcji funkcji do korzystania z tożsamości przypisanej do systemu](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

Teraz możesz przystąpić do konfigurowania uwierzytelniania usługi Azure AD dla aplikacji funkcji.

1. W [Azure Portal](https://portal.azure.com)Znajdź i wybierz swoją aplikację funkcji.

1. W okienku aplikacja funkcji wybierz pozycję **funkcje platformy**. W obszarze **Sieć** wybierz pozycję **uwierzytelnianie/autoryzacja**.

   ![Wyświetlanie ustawień uwierzytelniania i autoryzacji](./media/logic-apps-azure-functions/view-authentication-authorization-settings.png)

1. Zmień ustawienie **App Service Authentication** na **włączone**. Z listy **Akcja do wykonania w przypadku nieuwierzytelnionego żądania** wybierz pozycję **Zaloguj się przy użyciu Azure Active Directory**. W obszarze **dostawcy uwierzytelniania** wybierz pozycję **Azure Active Directory**.

   ![Włączanie uwierzytelniania za pomocą usługi Azure AD](./media/logic-apps-azure-functions/turn-on-authentication-azure-active-directory.png)

1. W okienku **ustawienia Azure Active Directory** wykonaj następujące kroki:

   1. Ustaw **tryb zarządzania** na **Zaawansowane**.

   1. We właściwości **Identyfikator klienta** wprowadź identyfikator obiektu dla tożsamości przypisanej do systemu aplikacji logiki.

   1. W polu właściwości **adres URL wystawcy** wprowadź `https://sts.windows.net/` adres URL i Dołącz identyfikator katalogu dzierżawy usługi Azure AD.

      `https://sts.windows.net/<Azure-AD-tenant-directory-ID>`

   1. We właściwości **dozwolonych odbiorców tokenu** wprowadź identyfikator zasobu dla zasobu docelowego, do którego chcesz uzyskać dostęp.

      Ten identyfikator zasobu jest tą samą wartością, którą można później użyć we właściwości **odbiorców** podczas [konfigurowania akcji funkcji do korzystania z tożsamości przypisanej do systemu](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

   W tym momencie wersja wygląda podobnie do tego przykładu:

   ![Ustawienia uwierzytelniania Azure Active Directory](./media/logic-apps-azure-functions/azure-active-directory-authentication-settings.png)

1. Gdy skończysz, wybierz opcję **OK**.

1. Wróć do projektanta aplikacji logiki i postępuj zgodnie z instrukcjami w [celu uwierzytelnienia dostępu przy użyciu tożsamości zarządzanej](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

## <a name="next-steps"></a>Następne kroki

* Więcej informacji na temat [łączników Logic Apps](../connectors/apis-list.md)