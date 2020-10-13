---
title: Konfigurowanie klastrowania Redis — pamięć podręczna Premium platformy Azure dla Redis
description: Dowiedz się, jak tworzyć i zarządzać klastrowaniem Redis dla usługi Azure cache w warstwie Premium dla wystąpień Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: 9545dd1480b9d16285d936787cf37fc087e882e1
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "92000042"
---
# <a name="how-to-configure-redis-clustering-for-a-premium-azure-cache-for-redis"></a>Jak skonfigurować klastrowanie Redis dla pamięci podręcznej systemu Azure w warstwie Premium dla Redis
Usługa Azure cache for Redis ma różne oferty pamięci podręcznej, które zapewniają elastyczność w wyborze rozmiaru i funkcji pamięci podręcznej, w tym funkcji warstwy Premium, takich jak klastrowanie, trwałość i obsługa sieci wirtualnej. W tym artykule opisano sposób konfigurowania klastrowania w pamięci podręcznej systemu Azure w warstwie Premium dla wystąpienia Redis.

## <a name="what-is-redis-cluster"></a>Co to jest klaster Redis?
Usługa Azure cache for Redis oferuje klaster Redis jako [zaimplementowany w Redis](https://redis.io/topics/cluster-tutorial). W przypadku klastra Redis uzyskasz następujące korzyści: 

* Możliwość automatycznego podziału zestawu danych między wieloma węzłami. 
* Możliwość kontynuowania operacji, gdy podzbiór węzłów ma błędy lub nie mogą komunikować się z resztą klastra. 
* Większa przepływność: przepływność wzrasta liniowo w miarę zwiększania liczby fragmentów. 
* Więcej pamięci: zwiększa się liniowo w miarę zwiększania liczby fragmentów.  

Klastrowanie nie zwiększa liczby połączeń dostępnych dla klastrowanej pamięci podręcznej. Aby uzyskać więcej informacji o rozmiarze, przepływności i przepustowości z pamięcią podręczną Premium, zobacz [Wybieranie odpowiedniej warstwy](cache-overview.md#choosing-the-right-tier)

Na platformie Azure klaster Redis jest oferowany jako model podstawowy/repliki, gdzie każdy fragmentu ma parę podstawowa/repliki z replikacją, w której replikacja jest zarządzana przez usługę Azure cache for Redis. 

## <a name="clustering"></a>Klastrowanie
Klastrowanie jest włączane w **nowym bloku Azure cache for Redis** podczas tworzenia pamięci podręcznej. 

1. Aby utworzyć pamięć podręczną Premium, zaloguj się do [Azure Portal](https://portal.azure.com) i wybierz pozycję **Utwórz zasób**. Oprócz tworzenia pamięci podręcznych w witrynie Azure Portal, możesz również utworzyć je przy użyciu programu PowerShell, interfejsu wiersza polecenia platformy Azure oraz szablonów usługi Resource Manager. Aby uzyskać więcej informacji na temat tworzenia pamięci podręcznej platformy Azure dla usługi Redis, zobacz [Tworzenie pamięci podręcznej](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Utwórz zasób.":::
   
2. Na stronie **Nowy** wybierz pozycję **bazy danych** , a następnie wybierz pozycję **Azure cache for Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Utwórz zasób.":::

3. Na stronie **nowy Redis Cache** Skonfiguruj ustawienia nowej pamięci podręcznej Premium.
   
   | Ustawienie      | Sugerowana wartość  | Opis |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nazwa DNS** | Podaj globalnie unikatową nazwę. | Nazwa pamięci podręcznej musi być ciągiem z przedziału od 1 do 63 znaków, który zawiera tylko cyfry, litery lub łączniki. Nazwa musi zaczynać się i kończyć cyfrą lub literą i nie może zawierać następujących po sobie łączników. *Nazwa hosta* wystąpienia pamięci podręcznej to * \<DNS name> . Redis.cache.Windows.NET*. | 
   | **Subskrypcja** | I wybierz swoją subskrypcję. | Subskrypcja, w ramach której ma zostać utworzone nowe wystąpienie usługi Azure cache for Redis. | 
   | **Grupa zasobów** | Wybierz grupę zasobów lub wybierz pozycję **Utwórz nową** , a następnie wprowadź nową nazwę grupy zasobów. | Nazwa grupy zasobów, w której ma zostać utworzona pamięć podręczna i inne zasoby. Umieszczenie wszystkich zasobów aplikacji w jednej grupie zasobów pozwala łatwo zarządzać nimi i usuwać je razem. | 
   | **Lokalizacja** | I wybierz lokalizację. | Wybierz [region](https://azure.microsoft.com/regions/) blisko innych usług, które będą korzystać z pamięci podręcznej. |
   | **Typ pamięci podręcznej** | Wybierz pozycję Pamięć podręczna Premium, aby skonfigurować funkcje Premium. Aby uzyskać szczegółowe informacje, zobacz [Azure cache for Redis — Cennik](https://azure.microsoft.com/pricing/details/cache/). |  Warstwa cenowa decyduje o rozmiarze, wydajności i funkcjach dostępnych dla pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [Omówienie pamięci podręcznej Azure Cache for Redis](cache-overview.md). |

4. Wybierz kartę **Sieć** lub kliknij przycisk **sieci** w dolnej części strony.

5. Na karcie **Sieć** wybierz metodę łączności. W przypadku wystąpień pamięci podręcznej Premium można łączyć się publicznie za pośrednictwem publicznych adresów IP lub punktów końcowych usług albo prywatnie przy użyciu prywatnego punktu końcowego.

6. Wybierz kartę **Dalej: Zaawansowane** lub kliknij przycisk **Dalej: Zaawansowane** w dolnej części strony.

7. Na karcie **Zaawansowane** wystąpienia pamięci podręcznej Premium Skonfiguruj ustawienia dla portu niezwiązanego z protokołem TLS, klastrowania i trwałości danych. Aby włączyć klastrowanie, kliknij pozycję **Włącz**.

    :::image type="content" source="media/cache-how-to-premium-clustering/redis-cache-clustering.png" alt-text="Utwórz zasób.":::

    W klastrze może znajdować się maksymalnie 10 fragmentów. Po kliknięciu przycisku **Włącz**przesuń suwak lub wpisz liczbę z zakresu od 1 do 10 dla **fragmentu liczba** i kliknij przycisk **OK**.

    Każdy fragmentu jest parę pamięci podręcznej podstawowej/repliki zarządzaną przez platformę Azure, a łączny rozmiar pamięci podręcznej jest obliczany przez pomnożenie liczby fragmentów przez rozmiar pamięci podręcznej wybrany w warstwie cenowej.

    :::image type="content" source="media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png" alt-text="Utwórz zasób.":::

    Po utworzeniu pamięci podręcznej można nawiązać z nią połączenie i używać jej w taki sam sposób, jak nieklastrowana pamięć podręczna, a następnie Redis dystrybuuje dane w całej pamięci podręcznej fragmentów. Jeśli Diagnostyka jest [włączona](cache-how-to-monitor.md#enable-cache-diagnostics), metryki są przechwytywane osobno dla każdego fragmentuu i można je [wyświetlić](cache-how-to-monitor.md) w bloku pamięci podręcznej platformy Azure dla Redis. 

8. Wybierz kartę **następne: Tagi** lub kliknij przycisk **Dalej: Tagi** w dolnej części strony.

9. Opcjonalnie na karcie **Tagi** wprowadź nazwę i wartość, jeśli chcesz przydzielić zasób. 

10. Wybierz pozycję **Recenzja + Utwórz**. Nastąpi przekierowanie do karty Recenzja + tworzenie, w której platforma Azure weryfikuje konfigurację.

11. Po wyświetleniu komunikatu o pomyślnym sprawdzeniu poprawności, wybierz pozycję **Utwórz**.

Tworzenie pamięci podręcznej zajmuje trochę czasu. Postęp można monitorować na stronie **Przegląd**usługi Azure cache for Redis   . Gdy **stan**   jest wyświetlany jako **uruchomiony**, pamięć podręczna jest gotowa do użycia. 

> [!NOTE]
> 
> W przypadku skonfigurowania klastrowania w aplikacji klienckiej istnieją pewne niewielkie różnice. Aby uzyskać więcej informacji, zobacz [Czy muszę wprowadzić zmiany w aplikacji klienckiej w celu korzystania z klastrowania?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 

Przykładowy kod dotyczący pracy z klastrem przy użyciu klienta StackExchange. Redis można znaleźć w części [Clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) przykładowej [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) .

<a name="cluster-size"></a>

## <a name="change-the-cluster-size-on-a-running-premium-cache"></a>Zmiana rozmiaru klastra na działającej pamięci podręcznej Premium
Aby zmienić rozmiar klastra w uruchomionej pamięci podręcznej Premium z włączoną obsługą klastrowania, kliknij pozycję **rozmiar klastra** w **menu zasób**.

![Rozmiar klastra Redis][redis-cache-redis-cluster-size]

Aby zmienić rozmiar klastra, użyj suwaka lub wpisz liczbę z zakresu od 1 do 10 w polu tekstowym **Liczba fragmentu** , a następnie kliknij przycisk **OK** , aby zapisać.

Zwiększenie rozmiaru klastra zwiększa maksymalną przepływność i rozmiar pamięci podręcznej. Zwiększenie rozmiaru klastra nie powoduje zwiększenia maksymalnej liczby połączeń dostępnych dla klientów.

> [!NOTE]
> Skalowanie klastra uruchamia polecenie [migracji](https://redis.io/commands/migrate) , które jest kosztownym poleceniem, dlatego w celu zapewnienia minimalnego wpływu należy rozważyć uruchomienie tej operacji w godzinach poza godzinami szczytu. Podczas migracji zostanie wyświetlony skok w polu obciążenie serwera. Skalowanie klastra to długotrwały proces i czas trwania zależy od liczby kluczy i rozmiaru wartości skojarzonych z tymi kluczami.
> 
> 

## <a name="clustering-faq"></a>Często zadawane pytania dotyczące klastrów
Poniższa lista zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure cache for Redis Clustering.

* [Czy muszę wprowadzić zmiany w aplikacji klienckiej w celu korzystania z klastrowania?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
* [W jaki sposób klucze są dystrybuowane w klastrze?](#how-are-keys-distributed-in-a-cluster)
* [Jaki jest największy rozmiar pamięci podręcznej, którą można utworzyć?](#what-is-the-largest-cache-size-i-can-create)
* [Czy wszyscy klienci Redis obsługują klastrowanie?](#do-all-redis-clients-support-clustering)
* [Jak mogę połączyć się z moją pamięcią podręczną po włączeniu klastrowania?](#how-do-i-connect-to-my-cache-when-clustering-is-enabled)
* [Czy mogę połączyć się bezpośrednio z konkretną fragmentówą pamięci podręcznej?](#can-i-directly-connect-to-the-individual-shards-of-my-cache)
* [Czy można skonfigurować klastrowanie dla wcześniej utworzonej pamięci podręcznej?](#can-i-configure-clustering-for-a-previously-created-cache)
* [Czy można skonfigurować klastrowanie dla podstawowej lub standardowej pamięci podręcznej?](#can-i-configure-clustering-for-a-basic-or-standard-cache)
* [Czy można używać klastrowania z Redis ASP.NET i dostawcy buforowania danych wyjściowych?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)
* [Co mam zrobić w przypadku korzystania z StackExchange. Redis i klastrowania?](#i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do)

### <a name="do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering"></a>Czy muszę wprowadzić zmiany w aplikacji klienckiej w celu korzystania z klastrowania?
* Gdy klastrowanie jest włączone, dostępna jest tylko baza danych 0. Jeśli aplikacja kliencka używa wielu baz danych i próbuje odczytać lub zapisać w bazie danych innej niż 0, zgłaszany jest następujący wyjątek. `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->` `StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
  
  Aby uzyskać więcej informacji, zobacz temat [Redis Cluster Specification-zaimplementowany podzestaw](https://redis.io/topics/cluster-spec#implemented-subset).
* Jeśli używasz [stackexchange. Redis](https://www.nuget.org/packages/StackExchange.Redis/), musisz użyć 1.0.481 lub nowszego. Łączysz się z pamięcią podręczną przy użyciu tych samych [punktów końcowych, portów i kluczy](cache-configure.md#properties) , które są używane podczas nawiązywania połączenia z pamięcią podręczną, w której nie włączono obsługi klastrowania. Jedyną różnicą jest to, że wszystkie operacje odczytu i zapisu należy wykonać w bazie danych 0.
  
  Inni klienci mogą mieć inne wymagania. Zobacz [czy wszyscy klienci Redis obsługują klastrowanie?](#do-all-redis-clients-support-clustering)
* Jeśli aplikacja używa wielu operacji wsadowych w pojedynczym poleceniu, wszystkie klucze muszą znajdować się w tym samym fragmentu. Aby zlokalizować klucze w tym samym fragmentu, zobacz [jak klucze są dystrybuowane w klastrze?](#how-are-keys-distributed-in-a-cluster)
* Jeśli używasz dostawcy stanu sesji Redis ASP.NET, musisz użyć wersji 2.0.1 lub nowszej. Zobacz [, czy można używać klastrowania z Redisem sesji ASP.NET i dostawcy buforowania danych wyjściowych?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)

### <a name="how-are-keys-distributed-in-a-cluster"></a>W jaki sposób klucze są dystrybuowane w klastrze?
Dokumentacja [modelu dystrybucji kluczy](https://redis.io/topics/cluster-spec#keys-distribution-model) Redis: przestrzeń klucza jest dzielona na 16384 gniazd. Każdy klucz jest przypisywany do jednego z tych miejsc, które są rozproszone w węzłach klastra. Można skonfigurować, która część klucza ma być używana do mieszania, aby mieć pewność, że wiele kluczy znajduje się w tym samym fragmentu za pomocą tagów skrótu.

* Klucze ze znacznikiem skrótu — Jeśli jakakolwiek część klucza jest zawarta w `{` i `}` , tylko ta część klucza ma wartość hash dla celów określania miejsca skrótu klucza. Na przykład następujące trzy klucze mogą znajdować się w tym samym fragmentu: `{key}1` , `{key}2` , i, `{key}3` ponieważ tylko `key` część nazwy jest skrótem. Aby zapoznać się z pełną listą parametrów skrótu kluczy, zobacz [Tagi skrótów kluczy](https://redis.io/topics/cluster-spec#keys-hash-tags).
* Klucze bez znacznika skrótu — cała nazwa klucza jest używana do tworzenia skrótów. Wynikiem tego jest statystycznie równomierny rozkład w fragmentów pamięci podręcznej.

W celu uzyskania najlepszej wydajności i przepływności zalecamy równomierne dystrybuowanie kluczy. Jeśli używasz kluczy ze znacznikiem skrótu, jest on odpowiedzialny za zapewnienie, że klucze są dystrybuowane równomiernie.

Aby uzyskać więcej informacji, zobacz temat [klucze model dystrybucji](https://redis.io/topics/cluster-spec#keys-distribution-model), [Redis klastra danych fragmentowania](https://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding)i [klucze skrótów kluczy](https://redis.io/topics/cluster-spec#keys-hash-tags).

Przykładowy kod dotyczący pracy z klastrowaniem i lokalizowanie kluczy w tym samym fragmentu za pomocą klienta StackExchange. Redis można znaleźć w części [Clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) przykładowej [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) .

### <a name="what-is-the-largest-cache-size-i-can-create"></a>Jaki jest największy rozmiar pamięci podręcznej, którą można utworzyć?
Największy rozmiar pamięci podręcznej Premium to 120 GB. Można utworzyć maksymalnie 10 fragmentów, co zapewnia maksymalny rozmiar 1,2 TB GB. Jeśli potrzebujesz większego rozmiaru, możesz [poprosić o więcej](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Aby uzyskać więcej informacji, zobacz [Azure cache for Redis — Cennik](https://azure.microsoft.com/pricing/details/cache/).

### <a name="do-all-redis-clients-support-clustering"></a>Czy wszyscy klienci Redis obsługują klastrowanie?
Nie wszyscy klienci obsługują klastrowanie Redis. Zapoznaj się z dokumentacją używanej biblioteki, aby sprawdzić, czy korzystasz z biblioteki i wersji, która obsługuje klaster. StackExchange. Redis to jedna biblioteka, która obsługuje klastrowanie w nowszych wersjach. Aby uzyskać więcej informacji na temat innych klientów, zobacz sekcję [granie z klastrem](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) w [samouczku klastra Redis](https://redis.io/topics/cluster-tutorial). 

Protokół klastrowania Redis wymaga, aby każdy klient łączył się z każdym fragmentu bezpośrednio w trybie klastrowania, a także definiuje nowe odpowiedzi na błędy, takie jak "przeniesione" na "CROSSSLOTS". Próba użycia klienta, który nie obsługuje klastrowania z pamięcią podręczną trybu klastra, może skutkować dużą ilością [przenoszonych wyjątków przekierowań](https://redis.io/topics/cluster-spec#moved-redirection)lub po prostu przerwać swoją aplikację, jeśli wykonujesz wielowymiarowe żądania wielokluczowe.

> [!NOTE]
> Jeśli używasz StackExchange. Redis jako klienta, upewnij się, że używasz najnowszej wersji [stackexchange. Redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 lub nowszej, aby klaster działał poprawnie. Jeśli masz problemy z wyjątkiem przenoszenia wyjątków, zobacz sekcję [przenoszenie wyjątków](#move-exceptions) , aby uzyskać więcej informacji.
>

### <a name="how-do-i-connect-to-my-cache-when-clustering-is-enabled"></a>Jak mogę połączyć się z moją pamięcią podręczną po włączeniu klastrowania?
Można nawiązać połączenie z pamięcią podręczną przy użyciu tych samych [punktów końcowych](cache-configure.md#properties), [portów](cache-configure.md#properties)i [kluczy](cache-configure.md#access-keys) , które są używane podczas nawiązywania połączenia z pamięcią podręczną, w której nie włączono obsługi klastrowania. Redis zarządza klastrowaniem w zapleczu, aby nie trzeba było zarządzać nim z poziomu klienta.

### <a name="can-i-directly-connect-to-the-individual-shards-of-my-cache"></a>Czy mogę połączyć się bezpośrednio z konkretną fragmentówą pamięci podręcznej?
Protokół klastrowania wymaga, aby klient przełączył poprawne połączenia usługi fragmentu. Dlatego klient powinien prawidłowo wykonać tę czynność. Z tego powodu każdy fragmentu składa się z pary pamięci podręcznej podstawowej/repliki, zwanej wspólnie wystąpieniem pamięci podręcznej. Można nawiązać połączenie z tymi wystąpieniami pamięci podręcznej za pomocą narzędzia Redis-CLI w [niestabilnej](https://redis.io/download) gałęzi repozytorium Redis w witrynie GitHub. Ta wersja implementuje podstawową pomoc techniczną po uruchomieniu `-c` przełącznika. Aby uzyskać więcej informacji, zobacz temat [granie z klastrem](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) w [https://redis.io](https://redis.io) [klastrze Redis](https://redis.io/topics/cluster-tutorial).

W przypadku niezwiązanych z protokołem TLS użyj następujących poleceń.

```bash
Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
...
Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)
```

W przypadku protokołu TLS Zastąp ciąg `1300N` opcją `1500N` .

### <a name="can-i-configure-clustering-for-a-previously-created-cache"></a>Czy można skonfigurować klastrowanie dla wcześniej utworzonej pamięci podręcznej?
Tak. Najpierw upewnij się, że pamięć podręczna jest Premium, przez skalowanie Jeśli nie jest. Następnie powinno być możliwe wyświetlenie opcji konfiguracji klastra, w tym opcji włączania klastrów. Rozmiar klastra można zmienić po utworzeniu pamięci podręcznej lub po pierwszym włączeniu klastra.

   >[!IMPORTANT]
   >Nie można cofnąć włączania klastrowania. I pamięć podręczna z włączoną obsługą klastrowania i tylko jeden fragmentu działa *inaczej* niż w przypadku *braku* klastrowania.

### <a name="can-i-configure-clustering-for-a-basic-or-standard-cache"></a>Czy można skonfigurować klastrowanie dla podstawowej lub standardowej pamięci podręcznej?
Klastrowanie jest dostępne tylko dla pamięci podręcznych w warstwie Premium.

### <a name="can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers"></a>Czy można używać klastrowania z Redis ASP.NET i dostawcy buforowania danych wyjściowych?
* **Dostawca wyjściowej pamięci podręcznej Redis** — nie są wymagane żadne zmiany.
* **Dostawca stanu sesji Redis** — aby użyć klastrowania, należy użyć [pakietu redissessionstateprovider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 lub nowszej lub wyjątek jest zgłaszany. Jest to istotna zmiana; Aby uzyskać więcej informacji, zobacz artykuł [v 2.0.0 — szczegóły dotyczące zmiany](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details).

<a name="move-exceptions"></a>

### <a name="i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do"></a>Co mam zrobić w przypadku korzystania z StackExchange. Redis i klastrowania?
Jeśli używasz StackExchange. Redis i odbierasz `MOVE` wyjątki podczas korzystania z klastrowania, upewnij się, że używasz [stackexchange. Redis 1.1.603](https://www.nuget.org/packages/StackExchange.Redis/) lub nowszego. Aby uzyskać instrukcje dotyczące konfigurowania aplikacji .NET do korzystania z StackExchange. Redis, zobacz [Konfigurowanie klientów pamięci podręcznej](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o funkcjach usługi Azure cache for Redis.

* [Pamięć podręczna systemu Azure dla warstw usługi Redis Premium](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png
