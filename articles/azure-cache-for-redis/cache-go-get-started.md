---
title: Korzystanie z usługi Azure cache for Redis za pomocą języka go
description: W tym przewodniku szybki start dowiesz się, jak utworzyć aplikację języka go korzystającą z usługi Azure cache for Redis.
author: abhirockzz
ms.author: abhishgu
ms.service: cache
ms.devlang: go
ms.topic: quickstart
ms.date: 01/08/2021
ms.openlocfilehash: de7bdc22b4bfdf9ef5865b1b601b32672eb868d1
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165131"
---
# <a name="quickstart-use-azure-cache-for-redis-with-go"></a>Szybki Start: korzystanie z usługi Azure cache for Redis za pomocą języka go

W tym artykule dowiesz się, jak utworzyć interfejs API REST w programie go, który będzie przechowywał i pobierał informacje o użytkowniku z użyciem struktury danych [skrótu](https://redis.io/topics/data-types-intro#redis-hashes) w [pamięci podręcznej platformy Azure dla Redis](./cache-overview.md). 

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
- [Przejdź](https://golang.org/doc/install) (najlepiej w wersji 1,13 lub nowszej)
- [Narzędzia](https://git-scm.com/downloads)
- Klient HTTP, który [zazwinięcie](https://curl.se/)

## <a name="create-an-azure-cache-for-redis-instance"></a>Tworzenie wystąpienia pamięci podręcznej Azure Cache for Redis
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="review-the-code-optional"></a>Przejrzyj kod (opcjonalnie)

Jeśli chcesz się dowiedzieć, jak działa kod, możesz przejrzeć poniższe fragmenty kodu. W przeciwnym razie możesz dowolnie przeskoczyć do [uruchomienia aplikacji](#run-the-application).

Biblioteka open source [go-Redis](https://github.com/go-redis/redis) służy do współdziałania z usługą Azure cache for Redis.

`main`Funkcja zostaje wyłączona, odczytując nazwę hosta i hasło (klucz dostępu) dla wystąpienia usługi Azure cache for Redis.

```go
func main() {
    redisHost := os.Getenv("REDIS_HOST")
    redisPassword := os.Getenv("REDIS_PASSWORD")
...
```

Następnie ustanawiamy połączenie z usługą Azure cache for Redis. Należy pamiętać, że [tls.Config](https://golang.org/pkg/crypto/tls/#Config) jest używany — usługa Azure cache for Redis akceptuje tylko bezpieczne połączenia z protokołem [TLS 1,2 jako minimalną wymaganą wersję](cache-remove-tls-10-11.md).

```go
...
op := &redis.Options{Addr: redisHost, Password: redisPassword, TLSConfig: &tls.Config{MinVersion: tls.VersionTLS12}}
client := redis.NewClient(op)

ctx := context.Background()
err := client.Ping(ctx).Err()
if err != nil {
    log.Fatalf("failed to connect with redis instance at %s - %v", redisHost, err)
}
...
```

W przypadku pomyślnego nawiązania połączenia usługi [obsługi http](https://golang.org/pkg/net/http/#HandleFunc) są skonfigurowane do obsługi `POST` i `GET` działania, a serwer HTTP jest uruchamiany. 

> [!NOTE] 
> [Biblioteka MUX Gorilla](https://github.com/gorilla/mux) jest używana na potrzeby routingu (chociaż nie jest to absolutnie konieczne, a firma Microsoft może odejść od użycia standardowej biblioteki dla tej przykładowej aplikacji).
>

```go
uh := userHandler{client: client}

router := mux.NewRouter()
router.HandleFunc("/users/", uh.createUser).Methods(http.MethodPost)
router.HandleFunc("/users/{userid}", uh.getUser).Methods(http.MethodGet)

log.Fatal(http.ListenAndServe(":8080", router))
```

`userHandler` Struktura hermetyzuje [Redis. Klient](https://pkg.go.dev/github.com/go-redis/redis/v8#Client), który jest używany przez `createUser` , `getUser` metody-kod dla tych metod nie został uwzględniony w zwięzłości. 

- `createUser`: akceptuje ładunek JSON (zawierający informacje o użytkowniku) i zapisuje go jako `HASH` w usłudze Azure cache for Redis.
- `getUser`: Pobiera informacje o użytkowniku `HASH` lub zwraca odpowiedź HTTP, `404` Jeśli nie zostanie znaleziona.

```go
type userHandler struct {
    client *redis.Client
}
...

func (uh userHandler) createUser(rw http.ResponseWriter, r *http.Request) {
    // details omitted
}
...

func (uh userHandler) getUser(rw http.ResponseWriter, r *http.Request) {
    // details omitted
}
```

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Zacznij od klonowania aplikacji z usługi GitHub.

1. Otwórz wiersz polecenia i Utwórz nowy folder o nazwie `git-samples` .

    ```bash
    md "C:\git-samples"
    ```

1. Otwórz okno terminalu usługi Git, na przykład git bash. Użyj `cd` polecenia, aby przejść do nowego folderu, w którym zostanie sklonowana Przykładowa aplikacja.

    ```bash
    cd "C:\git-samples"
    ```

1. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię aplikacji przykładowej na komputerze.

    ```bash
    git clone https://github.com/Azure-Samples/azure-redis-cache-go-quickstart.git
    ```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Aplikacja akceptuje połączenia i poświadczenia w postaci zmiennych środowiskowych. 

1. Pobierz **nazwę hosta** i **klucze dostępu** (dostępne za pośrednictwem kluczy dostępu) dla wystąpienia usługi Azure Cache for Redis w [Azure Portal](https://portal.azure.com/)

1. Ustaw je na odpowiednie zmienne środowiskowe:

    ```shell
    set REDIS_HOST=<Host name>:<port> (e.g. <name of cache>.redis.cache.windows.net:6380)
    set REDIS_PASSWORD=<Primary Access Key>
    ```

1. W oknie terminalu przejdź do odpowiedniego folderu. Na przykład:

    ```shell
    cd "C:\git-samples\azure-redis-cache-go-quickstart"
    ```

1. W terminalu uruchom następujące polecenie, aby uruchomić aplikację.

    ```shell
    go run main.go
    ```

Serwer HTTP zostanie uruchomiony na porcie `8080` .

## <a name="test-the-application"></a>Testowanie aplikacji

1. Utwórz kilka wpisów użytkownika. W poniższym przykładzie zastosowano zwinięcie:

    ```bash
    curl -i -X POST -d '{"id":"1","name":"foo1", "email":"foo1@baz.com"}' localhost:8080/users/
    curl -i -X POST -d '{"id":"2","name":"foo2", "email":"foo2@baz.com"}' localhost:8080/users/
    curl -i -X POST -d '{"id":"3","name":"foo3", "email":"foo3@baz.com"}' localhost:8080/users/
    ```

1. Pobierz istniejącego użytkownika z `id` :

    ```bash
    curl -i localhost:8080/users/1
    ```

    Należy otrzymać odpowiedź JSON w taki sposób:
    
    ```json
    {
        "email": "foo1@bar",
        "id": "1",
        "name": "foo1"
    }
    ```

1. Jeśli spróbujesz pobrać nieistniejący użytkownik, otrzymasz komunikat HTTP `404` . Na przykład:

    ```bash
    curl -i localhost:8080/users/100
    
    #response

    HTTP/1.1 404 Not Found
    Date: Fri, 08 Jan 2021 13:43:39 GMT
    Content-Length: 0
    ```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zakończysz pracę z grupą zasobów i zasobami platformy Azure utworzoną w tym przewodniku Szybki Start, możesz je usunąć, aby uniknąć naliczania opłat.

> [!IMPORTANT]
> Usuwanie grupy zasobów jest nieodwracalne, a grupa zasobów i wszystkie znajdujące się w niej zasoby są trwale usuwane. Jeśli utworzono wystąpienie usługi Azure cache for Redis w istniejącej grupie zasobów, która ma zostać zachowana, można usunąć tylko pamięć podręczną, wybierając pozycję **Usuń** na stronie **Przegląd** pamięci podręcznej. 

Aby usunąć grupę zasobów i jej Redis Cache dla wystąpienia platformy Azure:

1. W [Azure Portal](https://portal.azure.com)Wyszukaj i wybierz pozycję **grupy zasobów**.
1. W polu tekstowym **Filtruj według nazwy** wprowadź nazwę grupy zasobów zawierającej wystąpienie pamięci podręcznej, a następnie wybierz ją z wyników wyszukiwania. 
1. Na stronie grupy zasobów wybierz pozycję **Usuń grupę zasobów**.
1. Wpisz nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.
   
   ![Usuwanie grupy zasobów dla usługi Azure cache for Redis](./media/cache-python-get-started/delete-your-resource-group-for-azure-cache-for-redis.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start pokazano, jak rozpocząć korzystanie z usługi Microsoft Azure cache for Redis. Skonfigurowano i uruchomiono prostą aplikację opartą na interfejsie API REST w celu utworzenia i pobrania informacji o użytkowniku przez `HASH` strukturę danych Redis.

> [!div class="nextstepaction"]
> [Tworzenie prostej aplikacji internetowej platformy ASP.NET, która korzysta z usługi Azure Cache for Redis.](./cache-web-app-howto.md)
