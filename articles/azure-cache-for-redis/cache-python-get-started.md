---
title: 'Szybki start: używanie Azure Cache for Redis w języku Python'
description: Z tego przewodnika Szybki start dowiesz się, jak utworzyć aplikację w języku Python, która używa Azure Cache for Redis.
author: yegu-ms
ms.author: yegu
ms.date: 11/05/2019
ms.topic: quickstart
ms.service: cache
ms.devlang: python
ms.custom:
- mvc
- seo-python-october2019
- devx-track-python
- mode-api
ms.openlocfilehash: 40990dfb651817cf52cd5b5a039566e3209d6ac7
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532050"
---
# <a name="quickstart-use-azure-cache-for-redis-in-python"></a>Szybki start: używanie Azure Cache for Redis w języku Python

W tym artykule dołączasz aplikacje Azure Cache for Redis python, aby mieć dostęp do bezpiecznej, dedykowanej pamięci podręcznej, która jest dostępna z dowolnej aplikacji na platformie Azure.

## <a name="skip-to-the-code-on-github"></a>Przejdź do kodu w witrynie GitHub

Jeśli chcesz przejść bezpośrednio do kodu, zobacz Przewodnik Szybki start języka [Python w](https://github.com/Azure-Samples/azure-cache-redis-samples/tree/main/quickstart/python) witrynie GitHub.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure [— utwórz subskrypcję bezpłatnie](https://azure.microsoft.com/free/)
- [Python 2 lub 3](https://www.python.org/downloads/)

## <a name="create-an-azure-cache-for-redis-instance"></a>Tworzenie wystąpienia pamięci podręcznej Azure Cache for Redis
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="install-redis-py"></a>Instalowanie klienta redis-py

[Redis-py](https://github.com/andymccurdy/redis-py) to interfejs języka Python dla usługi Azure Cache for Redis. Użyj narzędzia pakietów języka Python *pip,* aby zainstalować pakiet *redis-py* z wiersza polecenia. 

W poniższym przykładzie *za pomocą polecenia pip3* dla języka Python 3 Windows 10 *redis-py* z wiersza polecenia administratora.

![Zainstaluj interfejs języka Python redis-py, aby Azure Cache for Redis](./media/cache-python-get-started/cache-python-install-redis-py.png)

## <a name="read-and-write-to-the-cache"></a>Odczytywanie i zapisywanie w pamięci podręcznej

Uruchom język Python z wiersza polecenia i przetestuj pamięć podręczną przy użyciu następującego kodu. Zastąp `<Your Host Name>` `<Your Access Key>` wartości i wartościami z Azure Cache for Redis wystąpienia. Nazwa hosta ma postać *\<DNS name> .redis.cache.windows.net*.

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
> W Azure Cache for Redis wersji 3.0 lub wyższej wymuszane jest sprawdzanie certyfikatu TLS/SSL. ssl_ca_certs należy jawnie ustawić podczas nawiązywania połączenia z Azure Cache for Redis. W przypadku systemu RedHat Linux ssl_ca_certs w *module certyfikatu /etc/pki/tls/certs/ca-bundle.crt.*

## <a name="create-a-python-sample-app"></a>Tworzenie przykładowej aplikacji w języku Python

Utwórz nowy plik tekstowy, dodaj następujący skrypt i zapisz plik jako PythonApplication1.py *.* Zastąp `<Your Host Name>` `<Your Access Key>` wartości i wartościami z Azure Cache for Redis wystąpienia. Nazwa hosta ma postać *\<DNS name> .redis.cache.windows.net*.

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

Uruchom *PythonApplication1.py* przy użyciu języka Python. Powinny zostać wyświetlony wyniki podobne do następujących:

![Uruchamianie skryptu języka Python w celu przetestowania dostępu do pamięci podręcznej](./media/cache-python-get-started/cache-python-completed.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu pracy z grupą zasobów platformy Azure i zasobami utworzonymi w tym przewodniku Szybki start możesz je usunąć, aby uniknąć nalicznych opłat.

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne, a grupa zasobów i wszystkie jej zasoby zostaną trwale usunięte. Jeśli utworzono wystąpienie Azure Cache for Redis w istniejącej grupie zasobów, którą chcesz zachować, możesz  usunąć tylko pamięć podręczną, wybierając pozycję Usuń na stronie **Przegląd pamięci podręcznej.** 

Aby usunąć grupę zasobów i jej Redis Cache wystąpienia platformy Azure:

1. Na stronie [Azure Portal](https://portal.azure.com)wyszukaj i wybierz **pozycję Grupy zasobów.**
1. W polu **tekstowym Filtruj** według nazwy wprowadź nazwę grupy zasobów zawierającej wystąpienie pamięci podręcznej, a następnie wybierz ją z wyników wyszukiwania. 
1. Na stronie grupy zasobów wybierz pozycję **Usuń grupę zasobów**.
1. Wpisz nazwę grupy zasobów, a następnie wybierz pozycję **Usuń.**
   
   ![Usuń grupę zasobów dla Azure Cache for Redis](./media/cache-python-get-started/delete-your-resource-group-for-azure-cache-for-redis.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie prostej aplikacji internetowej platformy ASP.NET, która korzysta z usługi Azure Cache for Redis.](./cache-web-app-howto.md)
