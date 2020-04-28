---
title: Dostosowywanie punktu końcowego HTTP w Azure Functions
description: Dowiedz się, jak dostosować punkt końcowy wyzwalacza HTTP w Azure Functions
author: mattchenderson
ms.topic: conceptual
ms.date: 05/04/2017
ms.author: mahender
ms.custom: mvc
ms.openlocfilehash: 61b930eec1385b8c4054f9c202547a82e61e55e7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75769272"
---
# <a name="customize-an-http-endpoint-in-azure-functions"></a>Dostosowywanie punktu końcowego HTTP w Azure Functions

W tym artykule dowiesz się, jak Azure Functions pozwala tworzyć wysoce skalowalne interfejsy API. Azure Functions zawiera kolekcję wbudowanych wyzwalaczy i powiązań HTTP, które ułatwiają tworzenie punktów końcowych w różnych językach, w tym Node. js, C# i innych. W tym artykule opisano dostosowanie wyzwalacza protokołu HTTP w celu obsługi określonych akcji w projekcie interfejsu API. Przygotujesz się również na rozwój Twojego interfejsu API, integrując go z serwerami proxy usługi Azure Functions i konfigurując makiety interfejsów API. Wszystko to dzieje się w górnej warstwie bezserwerowego środowiska obliczeniowego usługi Functions, dzięki czemu nie musisz martwić się o skalowanie zasobów — możesz po prostu skupić się na logice interfejsu API.

## <a name="prerequisites"></a>Wymagania wstępne 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

Funkcja wyników zostanie użyta w pozostałej części tego artykułu.

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Otwórz witrynę Azure Portal. W tym celu zaloguj się do [https://portal.azure.com](https://portal.azure.com) programu przy użyciu konta platformy Azure.

## <a name="customize-your-http-function"></a>Dostosowywanie funkcji HTTP

Domyślnie funkcja wyzwalana przez protokół HTTP jest skonfigurowana tak, aby akceptowała dowolną metodę HTTP. Istnieje również domyślny adres URL formularza `http://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>`. Jeśli udało Ci się wykonać instrukcje z przewodnika Szybki start, parametr `<funcname>` prawdopodobnie wygląda jak „HttpTriggerJS1”. W tej sekcji zmodyfikujesz funkcję tak, aby odpowiadała tylko na żądania GET dotyczące trasy `/api/hello`. 

1. Przejdź do swojej funkcji w witrynie Azure Portal. Wybierz pozycję **Integracja** na lewym pasku nawigacyjnym.

    ![Dostosowywanie funkcji HTTP](./media/functions-create-serverless-api/customizing-http.png)

1. Użyj ustawień wyzwalacza HTTP określonych w tabeli.

    | Pole | Wartość przykładowa | Opis |
    |---|---|---|
    | Dozwolone metody HTTP | Wybrane metody | Określa, jakich metod HTTP można używać do wywoływania tej funkcji |
    | Wybrane metody HTTP | GET | Umożliwia wywoływanie tej funkcji tylko przy użyciu wybranych metod HTTP |
    | Szablon trasy | /hello | Określa trasę używaną do wywoływania tej funkcji |
    | Poziom autoryzacji | Anonimowe | Opcjonalnie: sprawia, że funkcja jest dostępna bez klucza interfejsu API |

    > [!NOTE] 
    > Zwróć uwagę, że w szablonie trasy nie został uwzględniony prefiks ścieżki podstawowej `/api`, ponieważ jest on obsługiwany przez ustawienie globalne.

1. Kliknij przycisk **Zapisz**.

Więcej informacji na temat dostosowywania funkcji HTTP możesz znaleźć w artykule [Powiązania HTTP usługi Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook).

### <a name="test-your-api"></a>Testowanie interfejsu API

Następnie przetestuj swoją funkcję, aby sprawdzić, czy działa z nowym środowiskiem interfejsu API.
1. Przejdź z powrotem do strony programowania, klikając nazwę funkcji na lewym pasku nawigacyjnym.
1. Kliknij pozycję **Pobierz adres URL funkcji** i skopiuj adres URL. Powinien on teraz zawierać trasę `/api/hello`.
1. Skopiuj adres URL do nowej karty przeglądarki lub preferowanego klienta REST. W przeglądarkach domyślnie jest używana metoda GET.
1. Dodaj parametry do ciągu zapytania w adresie URL, np. `/api/hello/?name=John`.
1. Naciśnij klawisz „Enter”, aby sprawdzić, czy adres działa. Powinna zostać wyświetlona odpowiedź „*Hello John*”.
1. Możesz również podjąć próbę wywołania punktu końcowego za pomocą innej metody HTTP, aby upewnić się, że funkcja nie zostanie wykonana. W tym celu musisz użyć klienta REST, np. programu cURL, Postman lub Fiddler.

## <a name="proxies-overview"></a>Omówienie serwerów proxy

W następnej sekcji udostępnisz interfejs API za pośrednictwem serwera proxy. Serwery proxy usługi Azure Functions umożliwiają przekazywanie żądań do innych zasobów. Ty definiujesz punkt końcowy HTTP, podobnie jak w przypadku wyzwalacza HTTP, ale zamiast pisać kod, który zostanie wykonany po wywołaniu punktu końcowego, podajesz adres URL do zdalnej implementacji. Dzięki temu możesz utworzyć wiele źródeł interfejsu API w jednym środowisku interfejsu API, które jest łatwe do użycia przez klientów. Jest to szczególnie przydatne, gdy chcesz utworzyć interfejs API w formie mikrousług.

Serwer proxy może wskazywać dowolny zasób HTTP, na przykład:
- Azure Functions 
- Aplikacje interfejsów API w usłudze [Azure App Service](https://docs.microsoft.com/azure/app-service/overview)
- Kontenery platformy Docker w usłudze [App Service w systemie Linux](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro)
- Jakikolwiek inny hostowany interfejs API

Aby dowiedzieć się więcej na temat serwerów proxy, zobacz [Praca z serwerami proxy usługi Azure Functions].

## <a name="create-your-first-proxy"></a>Tworzenie pierwszego serwera proxy

W tej sekcji utworzysz nowy serwer proxy, który będzie służył jako fronton dla ogólnego interfejsu API. 

### <a name="setting-up-the-frontend-environment"></a>Konfigurowanie środowiska frontonu

Wykonaj ponownie procedurę [Tworzenie aplikacji funkcji](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function#create-a-function-app), aby utworzyć nową aplikację funkcji, w której utworzysz serwer proxy. Adres URL tej nowej aplikacji będzie służył jako fronton dla naszego interfejsu API, a edytowana wcześniej aplikacja funkcji będzie pełniła rolę zaplecza.

1. Przejdź do nowej aplikacji funkcji frontonu w portalu.
1. Wybierz pozycję **Funkcje platformy**, a następnie pozycję **Ustawienia aplikacji**.
1. Przewiń w dół do **ustawień aplikacji**, w których są przechowywane pary klucz/wartość i utwórz nowe ustawienie z kluczem „HELLO_HOST”. Ustaw dla niego wartość hosta aplikacji funkcji zaplecza, na przykład `<YourBackendApp>.azurewebsites.net`. Jest to część adresu URL, który został skopiowany wcześniej podczas testowania funkcji HTTP. Później będziesz odwoływać się do tego ustawienia w konfiguracji.

    > [!NOTE] 
    > Ustawienia aplikacji są zalecane w przypadku konfiguracji hosta, aby uniknąć zakodowanej zależności środowiska dla serwera proxy. Korzystanie z ustawień aplikacji oznacza możliwość przenoszenia konfiguracji serwera proxy między środowiskami i zastosowanie ustawień aplikacji specyficznych dla środowiska.

1. Kliknij przycisk **Zapisz**.

### <a name="creating-a-proxy-on-the-frontend"></a>Tworzenie serwera proxy we frontonie

1. Przejdź z powrotem do aplikacji funkcji frontonu w portalu.
1. Na lewym pasku nawigacyjnym kliknij znak plus „+” obok pozycji „Serwer proxy”.
    ![Tworzenie serwera proxy](./media/functions-create-serverless-api/creating-proxy.png)
1. Użyj ustawień serwera proxy określonych w tabeli. 

    | Pole | Wartość przykładowa | Opis |
    |---|---|---|
    | Nazwa | HelloProxy | Przyjazna nazwa używana tylko do zarządzania |
    | Szablon trasy | /api/remotehello | Określa trasę używaną do wywoływania tego serwera proxy |
    | Adres URL zaplecza | https://%HELLO_HOST%/api/hello | Określa punkt końcowy, do którego powinno być przekazywane żądanie |
    
1. Zwróć uwagę, że serwery proxy nie zapewniają prefiksu ścieżki podstawowej `/api`, który musi znajdować się w szablonie trasy.
1. Składnia `%HELLO_HOST%` będzie odwoływać się do utworzonego wcześniej ustawienia aplikacji. Rozpoznany adres URL będzie wskazywał oryginalną funkcję.
1. Kliknij przycisk **Utwórz**.
1. Nowy serwer proxy możesz wypróbować, kopiując adres URL serwera proxy i testując go w przeglądarce lub używając ulubionego klienta HTTP.
    1. W przypadku funkcji anonimowej użyj adresu:
        1. `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?name="Proxies"`
    1. W przypadku funkcji z autoryzacją użyj adresu:
        1. `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?code=YOURCODE&name="Proxies"`

## <a name="create-a-mock-api"></a>Tworzenie makiety interfejsu API

Następnie użyjesz serwera proxy do utworzenia makiety interfejsu API dla Twojego rozwiązania. Umożliwi to postęp w programowaniu klienta bez konieczności pełnego zaimplementowania zaplecza. Na dalszym etapie programowania możesz utworzyć nową aplikację funkcji, która będzie obsługiwała tę logikę, i przekierować do niej serwer proxy.

W celu utworzenia tej makiety interfejsu API utworzymy nowy serwer proxy, tym razem używając [Edytora usługi App Service](https://github.com/projectkudu/kudu/wiki/App-Service-Editor). Aby rozpocząć, przejdź do aplikacji funkcji w portalu. Wybierz pozycję **Funkcje platformy** i w obszarze **Narzędzia programistyczne** znajdź pozycję **Edytor usługi App Service**. Kliknięcie tej pozycji spowoduje otwarcie Edytora usługi App Service w nowej karcie.

Wybierz pozycję `proxies.json` na lewym pasku nawigacyjnym. Jest to plik zawierający konfigurację wszystkich Twoich serwerów proxy. Jeśli używasz jednej z [metod wdrażania usługi Functions](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment), jest to plik, który będzie utrzymywany w kontroli źródła. Aby dowiedzieć się więcej na temat tego pliku, zobacz [Konfiguracja zaawansowana serwerów proxy](https://docs.microsoft.com/azure/azure-functions/functions-proxies#advanced-configuration).

Po wykonaniu wszystkich powyższych kroków plik proxies.json powinien wyglądać następująco:

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

Następnie dodasz makietę interfejsu API. Zastąp plik proxies.json następującym:

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

Spowoduje to dodanie nowego serwera proxy „GetUserByName” bez właściwości backendUri. Zamiast wywoływania innego zasobu, modyfikuje on odpowiedź domyślną z serwerów proxy przy użyciu funkcji przesłonięcia odpowiedzi. Przesłonięć żądań i odpowiedzi można także używać w połączeniu z adresem URL zaplecza. Jest to szczególnie przydatne w przypadku proxy w starszym systemie, w którym może zajść potrzeba zmodyfikowania nagłówków, parametrów zapytania itp. Aby dowiedzieć się więcej na temat przesłonięć żądań i odpowiedzi, zobacz [Modyfikowanie żądań i odpowiedzi w serwerach proxy](https://docs.microsoft.com/azure/azure-functions/functions-proxies).

Przetestuj makietę interfejsu API, wywołując metodę `<YourProxyApp>.azurewebsites.net/api/users/{username}` za pomocą przeglądarki lub ulubionego klienta REST. Koniecznie zastąp parametr _{username}_ wartością ciągu reprezentującą nazwę użytkownika.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób tworzenia i dostosowywania interfejsu API na Azure Functions. Przedstawiono również sposób łączenia wielu interfejsów API, w tym makiet, w celu uzyskania ujednoliconego środowiska interfejsu API. Za pomocą tych technik możesz tworzyć interfejsy API o dowolnej złożoności, pracując na bezserwerowym modelu obliczeniowym udostępnianym przez usługę Azure Functions.

Podczas dalszego programowania interfejsu API może być przydatna następująca dokumentacja:

- [Powiązania HTTP usługi Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook)
- [Praca z serwerami proxy usługi Azure Functions]
- [Dokumentowanie interfejsu API usługi Azure Functions (wersja zapoznawcza)](https://docs.microsoft.com/azure/azure-functions/functions-api-definition-getting-started)


[Create your first function]: https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function
[Praca z serwerami proxy usługi Azure Functions]: https://docs.microsoft.com/azure/azure-functions/functions-proxies
