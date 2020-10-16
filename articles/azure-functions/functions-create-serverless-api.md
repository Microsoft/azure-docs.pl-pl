---
title: Dostosowywanie punktu końcowego HTTP w Azure Functions
description: Dowiedz się, jak dostosować punkt końcowy wyzwalacza HTTP w Azure Functions
author: mattchenderson
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: mahender
ms.custom: mvc
ms.openlocfilehash: d683ef92c4e8d11e9defbed5454e5849211bf8f7
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92104754"
---
# <a name="customize-an-http-endpoint-in-azure-functions"></a>Dostosowywanie punktu końcowego HTTP w Azure Functions

W tym artykule dowiesz się, jak Azure Functions pozwala tworzyć wysoce skalowalne interfejsy API. Azure Functions zawiera kolekcję wbudowanych wyzwalaczy i powiązań HTTP, które ułatwiają tworzenie punktów końcowych w różnych językach, w tym Node.js, C# i innych. W tym artykule opisano Dostosowywanie wyzwalacza protokołu HTTP w celu obsługi określonych akcji w projekcie interfejsu API. Przygotowuje się również do wzrostu interfejsu API, integrując go z serwery proxy usługi Azure Functions i konfigurując interfejsy API do tworzenia obrazu. Te zadania są wykonywane w oparciu o środowisko obliczeniowe bez serwera funkcji, więc nie trzeba martwić się o skalowanie zasobów — możesz po prostu skupić się na logice interfejsu API.

## <a name="prerequisites"></a>Wymagania wstępne 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

Funkcja wyników zostanie użyta w pozostałej części tego artykułu.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w [witrynie Azure Portal](https://portal.azure.com) przy użyciu danych konta Azure.

## <a name="customize-your-http-function"></a>Dostosowywanie funkcji HTTP

Domyślnie funkcja wyzwalacza HTTP jest skonfigurowana do akceptowania dowolnej metody HTTP. Możesz również użyć domyślnego adresu URL `http://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>` . W tej sekcji zmodyfikujesz funkcję tak, aby odpowiadała tylko na żądania GET `/api/hello` . 

1. Przejdź do swojej funkcji w witrynie Azure Portal. W menu po lewej stronie wybierz pozycję **integracja** , a następnie w obszarze **wyzwalacz**wybierz pozycję **http (REQ)** .

    :::image type="content" source="./media/functions-create-serverless-api/customizing-http.png" alt-text="Dostosowywanie funkcji HTTP":::

1. Użyj ustawień wyzwalacza HTTP, jak określono w poniższej tabeli.

    | Pole | Wartość przykładowa | Opis |
    |---|---|---|
    | Szablon trasy | /hello | Określa trasę używaną do wywoływania tej funkcji |
    | Poziom autoryzacji | Anonimowe | Opcjonalnie: sprawia, że funkcja jest dostępna bez klucza interfejsu API |
    | Wybrane metody HTTP | GET | Umożliwia wywoływanie tej funkcji tylko przy użyciu wybranych metod HTTP |

    Prefiks ścieżki podstawowej nie został uwzględniony `/api` w szablonie trasy, ponieważ jest obsługiwany przez ustawienie globalne.

1. Wybierz pozycję **Zapisz**.

Aby uzyskać więcej informacji na temat dostosowywania funkcji HTTP, zobacz [Azure Functions powiązania HTTP](./functions-bindings-http-webhook.md).

### <a name="test-your-api"></a>Testowanie interfejsu API

Następnie przetestuj funkcję, aby zobaczyć, jak działa z nową powierzchnią interfejsu API:
1. Na stronie funkcja wybierz pozycję **Code + test** w menu po lewej stronie.

1. Wybierz pozycję **Pobierz adres URL funkcji** z górnego menu i skopiuj adres URL. Potwierdź, że teraz używa tej `/api/hello` ścieżki.
 
1. Skopiuj adres URL do nowej karty przeglądarki lub preferowanego klienta REST. 

   Przeglądarki używają domyślnie GET.
 
1. Dodaj parametry do ciągu zapytania w adresie URL. 

   Na przykład `/api/hello/?name=John`.
 
1. Naciśnij klawisz ENTER, aby potwierdzić, że działa. Powinna zostać wyświetlona odpowiedź "*Hello Jan*".

1. Możesz również spróbować wywołać punkt końcowy z inną metodą HTTP, aby potwierdzić, że funkcja nie jest wykonywana. Aby to zrobić, użyj klienta REST, takiego jak zwinięcie, Poster lub programu Fiddler.

## <a name="proxies-overview"></a>Omówienie serwerów proxy

W następnej sekcji zostanie wyświetlony interfejs API za pomocą serwera proxy. Serwery proxy usługi Azure Functions umożliwiają przekazywanie żądań do innych zasobów. Należy zdefiniować punkt końcowy HTTP, podobnie jak w przypadku wyzwalacza HTTP. Jednak zamiast pisania kodu do wykonania, gdy ten punkt końcowy jest wywoływany, należy podać adres URL zdalnej implementacji. Dzięki temu można tworzyć wiele źródeł interfejsów API w jednej powierzchni interfejsu API, co jest łatwe do użycia przez klientów, co jest przydatne, jeśli chcesz skompilować interfejs API jako mikrousługi.

Serwer proxy może wskazywać dowolny zasób HTTP, na przykład:
- Azure Functions 
- Aplikacje interfejsów API w usłudze [Azure App Service](../app-service/overview.md)
- Kontenery platformy Docker w usłudze [App Service w systemie Linux](../app-service/overview.md#app-service-on-linux)
- Jakikolwiek inny hostowany interfejs API

Aby dowiedzieć się więcej na temat serwerów proxy, zobacz [Praca z serwerami proxy usługi Azure Functions].

## <a name="create-your-first-proxy"></a>Tworzenie pierwszego serwera proxy

W tej sekcji utworzysz nowy serwer proxy, który służy jako fronton dla ogólnego interfejsu API. 

### <a name="setting-up-the-frontend-environment"></a>Konfigurowanie środowiska frontonu

Wykonaj ponownie procedurę [Tworzenie aplikacji funkcji](./functions-create-first-azure-function.md#create-a-function-app), aby utworzyć nową aplikację funkcji, w której utworzysz serwer proxy. Ten adres URL nowej aplikacji służy jako fronton dla naszego interfejsu API, a aplikacja funkcji, która była wcześniej edytowana, służy jako zaplecze.

1. Przejdź do nowej aplikacji funkcji frontonu w portalu.
1. Wybierz pozycję **Funkcje platformy**, a następnie pozycję **Ustawienia aplikacji**.
1. Przewiń w dół do **ustawień aplikacji**, gdzie są przechowywane pary klucz/wartość, a następnie utwórz nowe ustawienie przy użyciu klucza `HELLO_HOST` . Ustaw dla niego wartość hosta aplikacji funkcji zaplecza, na przykład `<YourBackendApp>.azurewebsites.net`. Ta wartość jest częścią adresu URL, który został skopiowany wcześniej podczas testowania funkcji HTTP. Później będziesz odwoływać się do tego ustawienia w konfiguracji.

    > [!NOTE] 
    > Ustawienia aplikacji są zalecane w przypadku konfiguracji hosta, aby uniknąć zakodowanej zależności środowiska dla serwera proxy. Korzystanie z ustawień aplikacji oznacza możliwość przenoszenia konfiguracji serwera proxy między środowiskami i zastosowanie ustawień aplikacji specyficznych dla środowiska.

1. Wybierz pozycję **Zapisz**.

### <a name="creating-a-proxy-on-the-frontend"></a>Tworzenie serwera proxy we frontonie

1. Przejdź z powrotem do aplikacji funkcji frontonu w portalu.

1. W menu po lewej stronie wybierz pozycję **proxy**, a następnie wybierz pozycję **Dodaj**. 

1. Na stronie **nowy serwer proxy** Użyj ustawień z poniższej tabeli, a następnie wybierz pozycję **Utwórz**.

    | Pole | Wartość przykładowa | Opis |
    |---|---|---|
    | Nazwa | HelloProxy | Przyjazna nazwa używana tylko do zarządzania |
    | Szablon trasy | /api/remotehello | Określa trasę używaną do wywoływania tego serwera proxy |
    | Adres URL zaplecza | https://%HELLO_HOST%/api/hello | Określa punkt końcowy, do którego powinno być przekazywane żądanie |

    
    :::image type="content" source="./media/functions-create-serverless-api/creating-proxy.png" alt-text="Dostosowywanie funkcji HTTP":::

    Serwery proxy usługi Azure Functions nie udostępnia `/api` prefiksu ścieżki podstawowej, który musi być uwzględniony w szablonie trasy. `%HELLO_HOST%`Składnia odwołuje się do utworzonego wcześniej ustawienia aplikacji. Rozpoznany adres URL będzie wskazywał oryginalną funkcję.

1. Wypróbuj nowy serwer proxy przez skopiowanie adresu URL serwera proxy i przetestowanie go w przeglądarce lub z ulubionym klientem HTTP:
    - Użycie funkcji anonimowej:   `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?name="Proxies"` .
    - Dla funkcji z użyciem autoryzacji:   `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?code=YOURCODE&name="Proxies"` .

## <a name="create-a-mock-api"></a>Tworzenie makiety interfejsu API

Następnie użyjesz serwera proxy, aby utworzyć interfejs API służący do rozwiązania. Ten serwer proxy pozwala na postęp opracowywania klienta bez konieczności pełnego zaimplementowania wewnętrznej bazy danych. W dalszej części opracowywania można utworzyć nową aplikację funkcji, która obsługuje tę logikę i przekierowuje do niej serwer proxy.

Aby utworzyć ten interfejs API, utworzymy nowy serwer proxy, tym razem używając [Edytor usługi App Service](https://github.com/projectkudu/kudu/wiki/App-Service-Editor). Aby rozpocząć, przejdź do aplikacji funkcji w portalu. Wybierz pozycję **funkcje platformy**i w obszarze **Narzędzia programistyczne** Znajdź **Edytor usługi App Service**. Edytor usługi App Service zostanie otwarta na nowej karcie.

Wybierz pozycję `proxies.json` na lewym pasku nawigacyjnym. Ten plik przechowuje konfigurację dla wszystkich serwerów proxy. Jeśli używasz jednej z [metod wdrażania funkcji](./functions-continuous-deployment.md), ten plik jest zachowywany w kontroli źródła. Aby dowiedzieć się więcej na temat tego pliku, zobacz [Konfiguracja zaawansowana serwerów proxy](./functions-proxies.md#advanced-configuration).

Po wykonaniu tej czynności proxies.jspowinien wyglądać następująco:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/remotehello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        }
    }
}
```

Następnie dodasz interfejs API służący do zapamiętania. Zastąp proxies.jsw pliku następującym kodem:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/remotehello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        },
        "GetUserByName" : {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/users/{username}"
            },
            "responseOverrides": {
                "response.statusCode": "200",
                "response.headers.Content-Type" : "application/json",
                "response.body": {
                    "name": "{username}",
                    "description": "Awesome developer and master of serverless APIs",
                    "skills": [
                        "Serverless",
                        "APIs",
                        "Azure",
                        "Cloud"
                    ]
                }
            }
        }
    }
}
```

Ten kod dodaje nowy serwer proxy, `GetUserByName` bez `backendUri` właściwości. Zamiast wywoływania innego zasobu, modyfikuje on odpowiedź domyślną z serwerów proxy przy użyciu funkcji przesłonięcia odpowiedzi. Przesłonięć żądań i odpowiedzi można także używać w połączeniu z adresem URL zaplecza. Ta technika jest szczególnie przydatna w przypadku proxy do starszego systemu, w którym może być konieczne zmodyfikowanie nagłówków, parametrów zapytania itd. Aby dowiedzieć się więcej na temat przesłonięć żądań i odpowiedzi, zobacz [Modyfikowanie żądań i odpowiedzi w serwerach proxy](./functions-proxies.md).

Przetestuj makietę interfejsu API, wywołując metodę `<YourProxyApp>.azurewebsites.net/api/users/{username}` za pomocą przeglądarki lub ulubionego klienta REST. Koniecznie zastąp parametr _{username}_ wartością ciągu reprezentującą nazwę użytkownika.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób tworzenia i dostosowywania interfejsu API na Azure Functions. Przedstawiono również sposób łączenia wielu interfejsów API, w tym makiet, w celu uzyskania ujednoliconego środowiska interfejsu API. Za pomocą tych technik możesz tworzyć interfejsy API o dowolnej złożoności, pracując na bezserwerowym modelu obliczeniowym udostępnianym przez usługę Azure Functions.

Podczas dalszego programowania interfejsu API może być przydatna następująca dokumentacja:

- [Powiązania HTTP usługi Azure Functions](./functions-bindings-http-webhook.md)
- [Praca z serwerami proxy usługi Azure Functions]
- [Dokumentowanie interfejsu API usługi Azure Functions (wersja zapoznawcza)](./functions-openapi-definition.md)


[Create your first function]: ./functions-create-first-azure-function.md
[Praca z serwerami proxy usługi Azure Functions]: ./functions-proxies.md