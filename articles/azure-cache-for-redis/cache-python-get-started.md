---
title: 'Szybki Start: korzystanie z usługi Azure cache for Redis w języku Python'
description: W tym przewodniku szybki start dowiesz się, jak utworzyć aplikację w języku Python korzystającą z usługi Azure cache for Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: python
ms.topic: quickstart
ms.custom:
- mvc
- seo-python-october2019
- devx-track-python
ms.date: 11/05/2019
ms.openlocfilehash: 048c31bedb56bdb66dc3b80d12c3014652e34625
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121154"
---
# <a name="quickstart-use-azure-cache-for-redis-in-python"></a>Szybki Start: korzystanie z usługi Azure cache for Redis w języku Python

W tym artykule opisano obsługę usługi Azure cache for Redis w aplikacji w języku Python w celu uzyskania dostępu do bezpiecznej, dedykowanej pamięci podręcznej dostępnej z dowolnej aplikacji na platformie Azure.

## <a name="skip-to-the-code-on-github"></a>Przejdź do kodu w usłudze GitHub

Jeśli chcesz pominąć prosty kod, zobacz [Przewodnik Szybki Start](https://github.com/Azure-Samples/azure-cache-redis-samples/tree/main/quickstart/python) w języku Python w witrynie GitHub.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
- [Python 2 lub 3](https://www.python.org/downloads/)

## <a name="create-an-azure-cache-for-redis-instance"></a>Tworzenie wystąpienia pamięci podręcznej Azure Cache for Redis
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="install-redis-py"></a>Instalowanie klienta redis-py

[Redis-py](https://github.com/andymccurdy/redis-py) to interfejs języka Python dla usługi Azure Cache for Redis. Użyj narzędzia pakiety Python, *PIP*, aby zainstalować pakiet *Redis-PR* z wiersza polecenia. 

W poniższym przykładzie użyto *PIP3* for Python 3 do zainstalowania *Redis-PR* w systemie Windows 10 z poziomu wiersza polecenia administratora.

![Zainstaluj interfejs Python Redis-PR w usłudze Azure cache dla Redis](./media/cache-python-get-started/cache-python-install-redis-py.png)

## <a name="read-and-write-to-the-cache"></a>Odczytywanie i zapisywanie w pamięci podręcznej

Uruchom Język Python z wiersza polecenia i przetestuj pamięć podręczną przy użyciu następującego kodu. Zamień `<Your Host Name>` i `<Your Access Key>` na wartości z wystąpienia usługi Azure cache for Redis. Nazwa hosta ma postać *\<DNS name> . Redis.cache.Windows.NET*.

```python
>>> import redis
>>> r = redis.StrictRedis(host='<Your Host Name>',
        port=6380, db=0, password='<Your Access Key>', ssl=True)
>>> r.set('foo', 'bar')
True
>>> r.get('foo')
b'bar'
```

> [!IMPORTANT]
> W przypadku usługi Azure cache for Redis w wersji 3,0 lub nowszej wymuszone jest sprawdzenie certyfikatu TLS/SSL. ssl_ca_certs musi być jawnie ustawiona podczas nawiązywania połączenia z usługą Azure cache for Redis. W przypadku systemu RedHat Linux ssl_ca_certs znajdują się w module certyfikatu */etc/pki/tls/certs/CA-Bundle.CRT* .

## <a name="create-a-python-sample-app"></a>Tworzenie przykładowej aplikacji w języku Python

Utwórz nowy plik tekstowy, Dodaj następujący skrypt i Zapisz plik jako *PythonApplication1.py*. Zamień `<Your Host Name>` i `<Your Access Key>` na wartości z wystąpienia usługi Azure cache for Redis. Nazwa hosta ma postać *\<DNS name> . Redis.cache.Windows.NET*.

```python
import redis

myHostname = "<Your Host Name>"
myPassword = "<Your Access Key>"

r = redis.StrictRedis(host=myHostname, port=6380,
                      password=myPassword, ssl=True)

result = r.ping()
print("Ping returned : " + str(result))

result = r.set("Message", "Hello!, The cache is working with Python!")
print("SET Message returned : " + str(result))

result = r.get("Message")
print("GET Message returned : " + result.decode("utf-8"))

result = r.client_list()
print("CLIENT LIST returned : ")
for c in result:
    print("id : " + c['id'] + ", addr : " + c['addr'])
```

Uruchom *PythonApplication1.py* za pomocą języka Python. Powinny być widoczne wyniki podobne do następującego przykładu:

![Uruchom skrypt języka Python, aby przetestować dostęp do pamięci podręcznej](./media/cache-python-get-started/cache-python-completed.png)

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

> [!div class="nextstepaction"]
> [Tworzenie prostej aplikacji internetowej platformy ASP.NET, która korzysta z usługi Azure Cache for Redis.](./cache-web-app-howto.md)

