---
title: Dodawanie i wywoływanie funkcji platformy Azure z aplikacji logiki azure
description: Wywoływanie i uruchamianie kodu niestandardowego w usłudze Azure Functions z zautomatyzowanych zadań i przepływów pracy w usłudze Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/01/2019
ms.openlocfilehash: 29713622be90ea280bff3c002be746bf1615718f
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605903"
---
# <a name="call-azure-functions-from-azure-logic-apps"></a>Wywoływanie funkcji platformy Azure z aplikacji Azure Logic Apps

Jeśli chcesz uruchomić kod, który wykonuje określone zadanie w aplikacjach logiki, możesz utworzyć własną funkcję przy użyciu [usługi Azure Functions](../azure-functions/functions-overview.md). Ta usługa ułatwia tworzenie node.js, C#, i F# funkcje, dzięki czemu nie trzeba tworzyć pełną aplikację lub infrastrukturę do uruchamiania kodu. Można również [wywołać aplikacje logiki z wewnętrznych funkcji platformy Azure](#call-logic-app). Usługa Azure Functions zapewnia przetwarzanie bezserwerowe w chmurze i jest przydatna do wykonywania zadań, takich jak następujące przykłady:

* Rozszerz zachowanie aplikacji logiki za pomocą funkcji w node.js lub C#.
* Wykonywanie obliczeń w przepływie pracy aplikacji logiki.
* Stosowanie zaawansowanych pól formatowania lub obliczeń w aplikacjach logiki.

Aby uruchomić fragmenty kodu bez tworzenia funkcji platformy Azure, dowiedz się, jak [dodać i uruchomić kod wbudowany](../logic-apps/logic-apps-add-run-inline-code.md).

> [!NOTE]
> Integracja między aplikacjami logiki i usługi Azure Functions obecnie nie działa z gniazda włączone.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Aplikacja funkcji platformy Azure, która jest kontenerem dla funkcji platformy Azure, wraz z funkcją platformy Azure. Jeśli nie masz aplikacji funkcyjnej, [najpierw utwórz aplikację funkcyjną](../azure-functions/functions-create-first-azure-function.md). Następnie można utworzyć funkcję poza aplikacją logiki w witrynie Azure portal lub [z wewnątrz aplikacji logiki](#create-function-designer) w logice Projektanta aplikacji.

* Podczas pracy z aplikacjami logiki te same wymagania dotyczą aplikacji i funkcji, niezależnie od tego, czy są one istniejące, czy nowe:

  * Aplikacja funkcji i aplikacja logiki musi używać tej samej subskrypcji platformy Azure.

  * Nowe aplikacje funkcji muszą używać .NET lub JavaScript jako stosu środowiska wykonawczego. Po dodaniu nowej funkcji do istniejących aplikacji funkcji można wybrać c# lub JavaScript.

  * Funkcja używa szablonu **wyzwalacza HTTP.**

    Szablon wyzwalacza HTTP może `application/json` akceptować zawartość, która ma typ z aplikacji logiki. Po dodaniu funkcji platformy Azure do aplikacji logiki Projektant aplikacji logiki pokazuje funkcje niestandardowe, które są tworzone na podstawie tego szablonu w ramach subskrypcji platformy Azure.

  * Funkcja nie używa tras niestandardowych, chyba że zdefiniowano [definicję OpenAPI](../azure-functions/functions-openapi-definition.md) (wcześniej znaną jako [plik Swaggera).](https://swagger.io/)

  * Jeśli masz definicję OpenAPI dla swojej funkcji, Projektant aplikacji logiki zapewnia bogatsze środowisko podczas pracy z parametrami funkcji. Zanim aplikacja logiki będzie mogła znajdować funkcje openapi i uzyskiwać do nich dostęp, [skonfiguruj aplikację funkcji, wykonując następujące kroki](#function-swagger).

* Aplikacja logiki, w której chcesz dodać funkcję, w tym [wyzwalacz](../logic-apps/logic-apps-overview.md#logic-app-concepts) jako pierwszy krok w aplikacji logiki

  Przed dodaniem akcji, które uruchamiają funkcje, aplikacja logiki musi rozpocząć się od wyzwalacza. Jeśli jesteś nowy w aplikacjach logiki, sprawdź [Co to jest Usługa Azure Logic Apps](../logic-apps/logic-apps-overview.md) i szybki [start: Utwórz pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="function-swagger"></a>

## <a name="find-functions-that-have-openapi-descriptions"></a>Znajdowanie funkcji z opisami OpenAPI

Aby uzyskać bogatsze środowisko podczas pracy z parametrami funkcji w Logic Apps Designer, [wygeneruj definicję OpenAPI](../azure-functions/functions-openapi-definition.md), wcześniej znaną jako [plik Swagger,](https://swagger.io/)dla swojej funkcji. Aby skonfigurować aplikację funkcji, aby aplikacja logiki mogła znajdować i używać funkcji, które mają opisy Swagger, wykonaj następujące kroki:

1. Upewnij się, że aplikacja funkcji jest aktywnie uruchomiona.

1. W aplikacji funkcji skonfiguruj [udostępnianie zasobów między źródłami (CORS),](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) aby wszystkie źródła były dozwolone, wykonując następujące kroki:

   1. Z listy **Aplikacje funkcyjne** wybierz aplikację funkcji. W okienku po prawej stronie wybierz **pozycję Platforma zawiera cors** > **CORS**.

      ![Wybierz aplikację funkcji > "Funkcje platformy" > "CORS"](./media/logic-apps-azure-functions/function-platform-features-cors.png)

   1. W obszarze **CORS**dodaj symbol**`*`** wieloznaczny gwiazdki ( ), ale usuń wszystkie inne źródła na liście i wybierz pozycję **Zapisz**.

      ![Ustaw znak "CORS* na symbol wieloznaczny "*"](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

## <a name="access-property-values-inside-http-requests"></a>Dostęp do wartości właściwości wewnątrz żądań HTTP

Funkcje webhook mogą akceptować żądania HTTP jako dane wejściowe i przekazywać te żądania do innych funkcji. Na przykład chociaż aplikacje logiki ma [funkcje, które konwertują wartości DateTime,](../logic-apps/workflow-definition-language-functions-reference.md)ta podstawowa funkcja javascript próbki pokazuje, jak można uzyskać dostęp do właściwości wewnątrz obiektu żądania, który jest przekazywany do funkcji i wykonywać operacje na tej wartości właściwości. Aby uzyskać dostęp do właściwości wewnątrz obiektów, w tym przykładzie użyto [operatora kropki (.):](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors)

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

Oto, co dzieje się wewnątrz tej funkcji:

1. Funkcja tworzy `data` zmienną i `body` przypisuje obiekt `request` wewnątrz obiektu do tej zmiennej. Funkcja używa operatora kropki (.) do `body` odwoływania `request` się do obiektu wewnątrz obiektu:

   ```javascript
   var data = request.body;
   ```

1. Funkcja może teraz `date` uzyskać dostęp `data` do właściwości za pośrednictwem zmiennej i przekonwertować `ToDateString()` tę wartość właściwości z typu DateTime na typ DateString, wywołując funkcję. Funkcja zwraca również wynik `body` za pośrednictwem właściwości w odpowiedzi funkcji:

   ```javascript
   body: data.date.ToDateString();
   ```

Po utworzeniu funkcji platformy Azure wykonaj czynności, jak [dodawać funkcje do aplikacji logiki.](#add-function-logic-app)

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>Tworzenie funkcji wewnątrz aplikacji logiki

Można tworzyć funkcje platformy Azure bezpośrednio z przepływu pracy aplikacji logiki przy użyciu wbudowanej akcji usługi Azure Functions w logice App Designer, ale można użyć tej metody tylko dla funkcji platformy Azure napisanych w języku JavaScript. W przypadku innych języków można tworzyć funkcje platformy Azure za pośrednictwem usługi Azure Functions w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [Tworzenie pierwszej funkcji w witrynie Azure portal](../azure-functions/functions-create-first-azure-function.md).

Jednak zanim będzie można utworzyć dowolną funkcję platformy Azure, musisz już mieć aplikację funkcji platformy Azure, która jest kontenerem dla funkcji. Jeśli nie masz aplikacji funkcji, najpierw utwórz tę aplikację funkcji. Zobacz [Tworzenie pierwszej funkcji w witrynie Azure portal](../azure-functions/functions-create-first-azure-function.md).

1. W [witrynie Azure portal](https://portal.azure.com)otwórz aplikację logiki w projektancie aplikacji logiki.

1. Aby utworzyć i dodać funkcję, wykonaj krok, który ma zastosowanie do scenariusza:

   * W obszarze ostatniego kroku w przepływie pracy aplikacji logiki wybierz pozycję **Nowy krok**.

   * Między istniejącymi krokami w przepływie pracy aplikacji logiki przesuń wskaźnik myszy na strzałkę, wybierz znak plus (+), a następnie wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania wprowadź "funkcje azure" jako filtr. Z listy akcji wybierz akcję **Wybierz funkcję platformy Azure,** na przykład:

   ![Znajdź "Funkcje platformy Azure"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. Z listy aplikacji funkcyjnych wybierz aplikację funkcyjną. Po otwarciu listy akcji wybierz tę akcję: **Utwórz nową funkcję**

   ![Wybierz aplikację funkcji](./media/logic-apps-azure-functions/select-function-app-create-function.png)

1. W edytorze definicji funkcji zdefiniuj funkcję:

   1. W polu **Nazwa funkcji** podaj nazwę funkcji.

   1. W polu **Kod** dodaj kod do szablonu funkcji, w tym odpowiedzi i ładunku, które mają powrócić do aplikacji logiki po zakończeniu działania funkcji. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

   Przykład:

   ![Zdefiniuj swoją funkcję](./media/logic-apps-azure-functions/add-code-function-definition.png)

   W kodzie szablonu * `context` obiekt* odwołuje się do komunikatu, który aplikacja logiki wysyła za pośrednictwem pola Treść **żądania** w późniejszym kroku. Aby uzyskać `context` dostęp do właściwości obiektu z wewnątrz funkcji, należy użyć tej składni:

   `context.body.<property-name>`

   Na przykład, aby `content` odwołać `context` się do właściwości wewnątrz obiektu, należy użyć tej składni:

   `context.body.content`

   Kod szablonu zawiera `input` również zmienną, która `data` przechowuje wartość z parametru, dzięki czemu funkcja może wykonywać operacje na tej wartości. Wewnątrz funkcji JavaScript `data` zmienna jest `context.body`również skrótem do .

   > [!NOTE]
   > Właściwość `body` w tym `context` miejscu stosuje się do obiektu i nie jest taka sama jak **Body** token z danych wyjściowych akcji, które mogą również przejść do funkcji.

1. W treści **treści żądania** podaj dane wejściowe funkcji, które muszą być sformatowane jako obiekt notacji obiektu JavaScript (JSON).

   To dane wejściowe jest *obiekt kontekstu* lub komunikat, który aplikacja logiki wysyła do funkcji. Po kliknięciu pola **Treść żądania** zostanie wyświetlona dynamiczna lista zawartości, dzięki czemu można wybrać tokeny dla wyjść z poprzednich kroków. W tym przykładzie określono, że ładunek kontekstu zawiera właściwość o nazwie, `content` która ma wartość **Od** tokenu z wyzwalacza wiadomości e-mail.

   ![Przykład "Treść żądania" — ładunek obiektu kontekstu](./media/logic-apps-azure-functions/function-request-body-example.png)

   W tym miejscu obiekt kontekstu nie jest rzutowy jako ciąg, więc zawartość obiektu zostanie dodana bezpośrednio do ładunku JSON. Jednak gdy obiekt kontekstu nie jest token JSON, który przekazuje ciąg, obiekt JSON lub tablicy JSON, pojawia się błąd. Tak, jeśli w tym przykładzie **użyto** odebranego czasu tokenu zamiast, można rzutować obiekt kontekstu jako ciąg, dodając znaki cudzysłowu.

   ![Rzut obiektu jako ciąg](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Aby określić inne szczegóły, takie jak metoda użycia, żądania nagłówków lub parametrów kwerendy lub uwierzytelnianie, otwórz listę **Dodaj nowy parametr** i wybierz odpowiednie opcje. W przypadku uwierzytelniania opcje różnią się w zależności od wybranej funkcji. Zobacz [Włącz uwierzytelnianie dla funkcji platformy Azure](#enable-authentication-functions).

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>Dodawanie istniejących funkcji do aplikacji logiki

Aby wywołać istniejące funkcje platformy Azure z aplikacji logiki, można dodać funkcje platformy Azure, jak każda inna akcja w Projektancie aplikacji logiki.

1. W [witrynie Azure portal](https://portal.azure.com)otwórz aplikację logiki w projektancie aplikacji logiki.

1. W obszarze kroku, w którym chcesz dodać funkcję, wybierz pozycję **Nowy krok**.

1. W obszarze **Wybierz akcję**w polu wyszukiwania wprowadź jako filtr "funkcje azure". Z listy akcji wybierz akcję **Wybierz funkcję platformy Azure.**

   ![Znajdź "Funkcje platformy Azure"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. Z listy aplikacji funkcyjnych wybierz aplikację funkcyjną. Po wyświetleniu listy funkcji wybierz funkcję.

   ![Wybierz aplikację funkcji i funkcję platformy Azure](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   Dla funkcji, które mają definicje interfejsu API (opisy Swagger) i są [skonfigurowane tak, aby aplikacja logiki można znaleźć i uzyskać dostęp do tych funkcji,](#function-swagger)można wybrać **akcje Swagger**.

   ![Wybierz aplikację funkcji, "Akcje swagger" i funkcję platformy Azure](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

1. W treści **treści żądania** podaj dane wejściowe funkcji, które muszą być sformatowane jako obiekt notacji obiektu JavaScript (JSON).

   To dane wejściowe jest *obiekt kontekstu* lub komunikat, który aplikacja logiki wysyła do funkcji. Po kliknięciu w polu **Treść żądania** zostanie wyświetlona dynamiczna lista zawartości, dzięki czemu można wybrać tokeny dla wyjść z poprzednich kroków. W tym przykładzie określono, że ładunek kontekstu zawiera właściwość o nazwie, `content` która ma wartość **Od** tokenu z wyzwalacza wiadomości e-mail.

   ![Przykład "Treść żądania" — ładunek obiektu kontekstu](./media/logic-apps-azure-functions/function-request-body-example.png)

   W tym miejscu obiekt kontekstu nie jest rzutowy jako ciąg, więc zawartość obiektu zostanie dodana bezpośrednio do ładunku JSON. Jednak gdy obiekt kontekstu nie jest token JSON, który przekazuje ciąg, obiekt JSON lub tablicy JSON, pojawia się błąd. Tak więc jeśli w tym przykładzie użyto tokenu **Odebrany czas,** można rzutować obiekt kontekstu jako ciąg, dodając znaki cudzysłowu:

   ![Rzut obiektu jako ciąg](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Aby określić inne szczegóły, takie jak metoda użycia, żądaj nagłówków, parametrów kwerendy lub uwierzytelniania, otwórz listę **Dodaj nowy parametr** i wybierz odpowiednie opcje. W przypadku uwierzytelniania opcje różnią się w zależności od wybranej funkcji. Zobacz [Włączanie uwierzytelniania w funkcjach platformy Azure](#enable-authentication-functions).

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-azure-functions"></a>Wywoływanie aplikacji logiki z funkcji platformy Azure

Jeśli chcesz wyzwolić aplikację logiki z wewnątrz funkcji platformy Azure, aplikacja logiki musi rozpocząć się od wyzwalacza, który zapewnia punkt końcowy, który można wywołać. Na przykład można uruchomić aplikację logiki za pomocą **wyzwalacza HTTP**, **Request**, **Azure Queues**lub **Event Grid.** Wewnątrz funkcji wyślij żądanie HTTP POST do adresu URL wyzwalacza i dołącz ładunek, który ma przetwarzać tę aplikację logiki. Aby uzyskać więcej informacji, zobacz [Wywoływanie, wyzwalanie lub zagnieżdżanie aplikacji logiki](../logic-apps/logic-apps-http-endpoint.md).

<a name="enable-authentication-functions"></a>

## <a name="enable-authentication-for-azure-functions"></a>Włącz uwierzytelnianie dla funkcji platformy Azure

Aby uwierzytelnić dostęp do zasobów w innych dzierżawach usługi Azure Active Directory (Azure AD) bez konieczności logowania się i podawania poświadczeń lub wpisów tajnych, aplikacja logiki może używać [tożsamości zarządzanej](../active-directory/managed-identities-azure-resources/overview.md) (wcześniej znanej jako tożsamość usługi zarządzanej lub MSI). Platforma Azure zarządza tą tożsamością i pomaga zabezpieczyć poświadczenia, ponieważ nie trzeba podawać ani obracać wpisów tajnych. Dowiedz się więcej o [usługach platformy Azure, które obsługują tożsamości zarządzane dla uwierzytelniania usługi Azure AD.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

Jeśli skonfigurujesz aplikację logiki do używania tożsamości przypisanej do systemu lub ręcznie utworzonej tożsamości przypisanej przez użytkownika, funkcje platformy Azure w aplikacji logiki mogą również używać tej samej tożsamości do uwierzytelniania. Aby uzyskać więcej informacji na temat obsługi uwierzytelniania dla funkcji platformy Azure w aplikacjach logiki, zobacz [Dodawanie uwierzytelniania do połączeń wychodzących](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

Aby skonfigurować i używać tożsamości zarządzanej za pomocą funkcji, wykonaj następujące kroki:

1. Włącz tożsamość zarządzana w aplikacji logiki i skonfiguruj dostęp tej tożsamości do zasobu docelowego. Zobacz [Uwierzytelnij dostęp do zasobów platformy Azure przy użyciu zarządzanych tożsamości w usłudze Azure Logic Apps.](../logic-apps/create-managed-service-identity.md)

1. Włącz uwierzytelnianie w aplikacji funkcji i funkcji platformy Azure, wykonując następujące kroki:

   * [Konfigurowanie uwierzytelniania anonimowego w funkcji](#set-authentication-function-app)
   * [Konfigurowanie uwierzytelniania usługi Azure AD w aplikacji funkcji](#set-azure-ad-authentication)

<a name="set-authentication-function-app"></a>

### <a name="set-up-anonymous-authentication-in-your-function"></a>Konfigurowanie uwierzytelniania anonimowego w funkcji

Aby użyć tożsamości zarządzanej aplikacji logiki w funkcji platformy Azure, ustawiono poziom uwierzytelniania funkcji na anonimowy. W przeciwnym razie aplikacja logiki zgłasza błąd "BadRequest".

1. W [witrynie Azure portal](https://portal.azure.com)znajdź i wybierz aplikację funkcji. W tych krokach użyj "FabrikamFunctionApp" jako przykładowej aplikacji funkcji.

1. W okienku aplikacji funkcji wybierz pozycję **Funkcje platformy**. W obszarze **Narzędzia programistyczne**wybierz pozycję **Zaawansowane narzędzia (Kudu)**.

   ![Otwórz zaawansowane narzędzia dla Kudu](./media/logic-apps-azure-functions/open-advanced-tools-kudu.png)

1. Na pasku tytułu witryny Kudu z menu **Konsola debugowania** wybierz polecenie **CMD**.

   ![Z menu konsoli debugowania wybierz opcję "CMD"](./media/logic-apps-azure-functions/open-debug-console-kudu.png)

1. Po wyświetleniu następnej strony z listy folderów > wybierz pozycję > **wwwroot***witryny funkcji*. **site** W tych krokach użyto funkcji "FabrikamAzureFunction".

   ![Wybierz "site" > "wwwroot" > swoją funkcję](./media/logic-apps-azure-functions/select-site-wwwroot-function-folder.png)

1. Otwórz `function.json` plik do edycji.

   ![Kliknij edytuj dla pliku "function.json"](./media/logic-apps-azure-functions/edit-function-json-file.png)

1. W `bindings` obiekcie sprawdź, `authLevel` czy właściwość istnieje. Jeśli właściwość istnieje, ustaw wartość `anonymous`właściwości na . W przeciwnym razie dodaj tę właściwość i ustaw wartość.

   ![Dodaj właściwość "authLevel" i ustaw wartość "anonimowy"](./media/logic-apps-azure-functions/set-authentication-level-function-app.png)

1. Po zakończeniu zapisz ustawienia, a następnie przejdź do następnej sekcji.

<a name="set-azure-ad-authentication"></a>

### <a name="set-up-azure-ad-authentication-for-your-function-app"></a>Konfigurowanie uwierzytelniania usługi Azure AD dla aplikacji funkcji

Przed rozpoczęciem tego zadania znajdź i odłóż te wartości na bok do późniejszego użycia:

* Identyfikator obiektu wygenerowany dla tożsamości przypisanej do systemu, która reprezentuje aplikację logiki

  * Aby wygenerować ten identyfikator obiektu, [włącz tożsamość przypisaną systemowi aplikacji logiki.](../logic-apps/create-managed-service-identity.md#azure-portal-system-logic-app)

  * W przeciwnym razie, aby znaleźć ten identyfikator obiektu, otwórz aplikację logiki w logice App Designer. W menu aplikacji logiki w obszarze **Ustawienia**wybierz pozycję System **tożsamości** > **przypisany**.

* Identyfikator katalogu dzierżawy w usłudze Azure Active Directory (Azure AD)

  Aby uzyskać identyfikator katalogu dzierżawy, można uruchomić [`Get-AzureAccount`](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureaccount) polecenie programu Powershell. Lub w witrynie Azure portal wykonaj następujące kroki:

  1. W [witrynie Azure portal](https://portal.azure.com)znajdź i wybierz aplikację funkcji.

  1. Znajdź i wybierz dzierżawę usługi Azure AD. W tych krokach użyto "Fabrikam" jako przykładowej dzierżawy.

  1. W menu dzierżawy w obszarze **Zarządzaj**wybierz pozycję **Właściwości**.

  1. Skopiuj identyfikator katalogu dzierżawy, na przykład i zapisz ten identyfikator do późniejszego użycia.

     ![Znajdowanie i kopiowanie identyfikatora katalogu dzierżawy usługi Azure AD](./media/logic-apps-azure-functions/azure-active-directory-tenant-id.png)

* Identyfikator zasobu docelowego, do którego chcesz uzyskać dostęp

  * Aby znaleźć te identyfikatory zasobów, przejrzyj [usługi platformy Azure obsługujące usługę Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

  > [!IMPORTANT]
  > Ten identyfikator zasobu musi dokładnie odpowiadać wartości oczekiwanej przez usługę Azure AD, w tym wszelkich wymaganych ukośników końcowych.

  Ten identyfikator zasobu jest również tą samą wartością, której później używasz we właściwości **Odbiorcy** podczas [konfigurowania akcji funkcji w celu użycia tożsamości przypisanej przez system](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

Teraz możesz skonfigurować uwierzytelnianie usługi Azure AD dla aplikacji funkcji.

1. W [witrynie Azure portal](https://portal.azure.com)znajdź i wybierz aplikację funkcji.

1. W okienku aplikacji funkcji wybierz pozycję **Funkcje platformy**. W obszarze **Sieć**wybierz **pozycję Uwierzytelnianie / Autoryzacja**.

   ![Wyświetlanie ustawień uwierzytelniania i autoryzacji](./media/logic-apps-azure-functions/view-authentication-authorization-settings.png)

1. Zmień ustawienie **Uwierzytelnianie usługi aplikacji** na **Włączone**. Z listy **Akcja do wykonania, gdy żądanie nie jest uwierzytelnione,** wybierz pozycję **Zaloguj się za pomocą usługi Azure Active Directory**. W obszarze **Dostawcy uwierzytelniania**wybierz pozycję **Azure Active Directory**.

   ![Włączanie uwierzytelniania za pomocą usługi Azure AD](./media/logic-apps-azure-functions/turn-on-authentication-azure-active-directory.png)

1. W okienku **Ustawienia usługi Azure Active Directory** wykonaj następujące kroki:

   1. Ustaw **tryb zarządzania** na **Zaawansowane**.

   1. We właściwości **Identyfikator klienta** wprowadź identyfikator obiektu dla tożsamości przypisanej systemem aplikacji logiki.

   1. We właściwości Adres url `https://sts.windows.net/` **wystawcy** wprowadź adres URL i dołącz identyfikator katalogu dzierżawy usługi Azure AD.

      `https://sts.windows.net/<Azure-AD-tenant-directory-ID>`

   1. We właściwości **Dozwolone grupy odbiorców tokenów** wprowadź identyfikator zasobu docelowego, do którego chcesz uzyskać dostęp.

      Ten identyfikator zasobu jest tą samą wartością, której później używasz we właściwości **Odbiorcy** podczas [konfigurowania akcji funkcji w celu użycia tożsamości przypisanej przez system](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

   W tym momencie wersja wygląda podobnie do tego przykładu:

   ![Ustawienia uwierzytelniania usługi Azure Active Directory](./media/logic-apps-azure-functions/azure-active-directory-authentication-settings.png)

1. Gdy skończysz, wybierz opcję **OK**.

1. Wróć do projektanta aplikacji logiki i wykonaj [kroki, aby uwierzytelnić dostęp za pomocą tożsamości zarządzanej](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [łącznikach aplikacji logiki](../connectors/apis-list.md)
