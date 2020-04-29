---
title: 'Uruchamianie wielu usług zależnych: .NET Core & Visual Studio Code'
services: azure-dev-spaces
ms.date: 11/21/2018
ms.topic: tutorial
description: W tym samouczku pokazano, jak używać Azure Dev Spaces i Visual Studio Code do debugowania aplikacji platformy .NET Core z obsługą wielousług w usłudze Azure Kubernetes Service
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, Service siatk, Service siatk Routing, polecenia kubectl, k8s
ms.openlocfilehash: 0bbb1aefe517c45207160b83b89f7207e8909666
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75438313"
---
# <a name="running-multiple-dependent-services-net-core-and-visual-studio-code-with-azure-dev-spaces"></a>Uruchamianie wielu usług zależnych: .NET Core i Visual Studio Code z Azure Dev Spaces

Z tego samouczka dowiesz się, jak programować aplikacje wielousługowe za pomocą usługi Azure Dev Spaces oraz poznasz niektóre dodatkowe korzyści zapewniane przez usługę Dev Spaces.

## <a name="call-a-service-running-in-a-separate-container"></a>Wywoływanie usługi uruchomionej w oddzielnym kontenerze

W tej sekcji utworzysz drugą usługę, `mywebapi`, a usługa `webfrontend` ją wywoła. Każda usługa będzie działać w osobnych kontenerach. Następnie przeprowadzisz debugowanie w obu kontenerach.

![Wiele kontenerów](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Pobieranie przykładowego kodu aplikacji *mywebapi*
Aby nie tracić czasu, pobierzmy przykładowy kod z repozytorium GitHub. Przejdź do witryny https://github.com/Azure/dev-spaces i wybierz pozycję **Clone or Download** (Sklonuj lub pobierz), aby pobrać repozytorium GitHub. Kod dla tej sekcji znajduje się w folderze `samples/dotnetcore/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Uruchamianie aplikacji *mywebapi*
1. Otwórz aplikację `mywebapi` w *osobnym oknie programu VS Code*.
1. Otwórz okno **Paleta poleceń** (za pomocą menu **Widok | Paleta poleceń**) i przy użyciu autouzupełniania wpisz i wybierz to polecenie: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. Nie należy mylić tego polecenia z poleceniem `azds prep`, które umożliwia skonfigurowanie projektu na potrzeby wdrożenia.
1. Naciśnij klawisz F5 i zaczekaj na skompilowanie i wdrożenie usługi. Zobaczysz, że będzie ona gotowa podczas *uruchamiania aplikacji. Naciśnij klawisze CTRL + C, aby zamknąć.* w konsoli debugowania zostanie wyświetlony komunikat.
1. Adres URL punktu końcowego będą wyglądał mniej więcej tak: `http://localhost:<portnumber>`. **Porada: VS Code pasek stanu spowoduje włączenie elementu pomarańczowego i wyświetlenie adresu URL, który zostanie kliknięty.** Może się wydawać, że kontener działa lokalnie, ale faktycznie jest on uruchamiany w naszym obszarze deweloperskim na platformie Azure. Adres hosta lokalnego jest tworzony, ponieważ w aplikacji `mywebapi` nie zdefiniowano żadnych publicznych punktów końcowych i dostęp do niej można uzyskać wyłącznie z poziomu wystąpienia w środowisku Kubernetes. Dla Twojej wygody i ułatwienia interakcji z usługą prywatną z komputera lokalnego usługa Azure Dev Spaces tworzy tymczasowy tunel SSH do kontenera uruchomionego na platformie Azure.
1. Gdy aplikacja `mywebapi` jest gotowa, otwórz w przeglądarce adres hosta lokalnego. Dołącz do adresu URL część `/api/values`, aby wywołać domyślny interfejs API GET dla kontrolera `ValuesController`.
1. Jeśli wszystkie kroki zostały wykonane pomyślnie, powinno być możliwe wyświetlenie odpowiedzi z usługi `mywebapi`.

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Tworzenie żądania z aplikacji *webfrontend* do aplikacji *mywebapi*
Napiszmy w aplikacji `webfrontend` kod, który będzie wysyłał żądanie do aplikacji `mywebapi`.
1. Przełącz się na okno programu VS Code, aby uzyskać dostęp do aplikacji `webfrontend`.
1. *Zastąp* kod metody About w `HomeController.cs`:

    ```csharp
    public async Task<IActionResult> About()
    {
        ViewData["Message"] = "Hello from webfrontend";
        
        using (var client = new System.Net.Http.HttpClient())
            {
                // Call *mywebapi*, and display its response in the page
                var request = new System.Net.Http.HttpRequestMessage();
                request.RequestUri = new Uri("http://mywebapi/api/values/1");
                if (this.Request.Headers.ContainsKey("azds-route-as"))
                {
                    // Propagate the dev space routing header
                    request.Headers.Add("azds-route-as", this.Request.Headers["azds-route-as"] as IEnumerable<string>);
                }
                var response = await client.SendAsync(request);
                ViewData["Message"] += " and " + await response.Content.ReadAsStringAsync();
            }

        return View();
    }
    ```

W poprzednim przykładzie kodu nagłówek `azds-route-as` jest przekazywany z żądania przychodzącego do żądania wychodzącego. Później zobaczysz, jak ułatwia to zespołom [programowanie zespołowe](team-development-netcore.md).

### <a name="debug-across-multiple-services"></a>Debugowanie w wielu usługach
1. W tym momencie aplikacja `mywebapi` powinna być nadal uruchomiona z dołączonym debugerem. Jeśli nie jest, naciśnij klawisz F5 w projekcie `mywebapi`.
1. Ustaw punkt przerwania wewnątrz `Get(int id)` metody, która `api/values/{id}` obsługuje żądania GET. Jest to około [Wiersz 23 w pliku *controllers/ValuesController. cs* ](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/mywebapi/Controllers/ValuesController.cs#L23).
1. W projekcie `webfrontend` ustaw punkt przerwania tuż przed wysłaniem żądania GET do aplikacji `mywebapi/api/values`. Jest to około wiersz 32 w pliku [ *controllers/HomeController. cs* ](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/webfrontend/Controllers/HomeController.cs) zmodyfikowanym w poprzedniej sekcji.
1. W projekcie `webfrontend` naciśnij klawisz F5.
1. Wywołaj aplikację internetową i wykonaj kod w obu usługach.
1. W aplikacji internetowej na stronie Informacje zostanie wyświetlony połączony komunikat z dwóch usług: „Hello from webfrontend and Hello from mywebapi”.

### <a name="well-done"></a>Gotowe!
Teraz masz aplikację z wieloma kontenerami, z których każdy może być tworzony i wdrażany oddzielnie.


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat programowania zespołowego w usłudze Dev Spaces](team-development-netcore.md)
