---
author: yegu-ms
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: yegu
ms.openlocfilehash: 61e93e3700b9a396d2ac4fdcbb51fc5c874cf9cb
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/26/2020
ms.locfileid: "68286303"
---
Aplikacje platformy .NET mogą używać klienta pamięci podręcznej **StackExchange.Redis**, którego można skonfigurować w programie Visual Studio przy użyciu pakietu NuGet upraszczającego konfigurację aplikacji klienta pamięci podręcznej. 

> [!NOTE]
> Aby uzyskać więcej informacji, zobacz stronę usługi GitHub [stackexchange. Redis](https://github.com/StackExchange/StackExchange.Redis) i [stackexchange. Azure cache for Redis Client](https://github.com/StackExchange/StackExchange.Redis#documentation).
>
>

Aby skonfigurować aplikację klienta w programie Visual Studio przy użyciu pakietu NuGet StackExchange.Redis, kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań** i wybierz pozycję **Zarządzaj pakietami NuGet**. 

![Zarządzanie pakietami NuGet](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-manage-nuget-menu.png)

Wpisz **StackExchange.Redis** lub **StackExchange.Redis.StrongName** w polu tekstowym wyszukiwania, wybierz z wyników żądaną wersję i kliknij przycisk **Zainstaluj**.

> [!NOTE]
> Jeśli wolisz użyć wersji biblioteki klienckiej **StackExchange.Redis** o silnej nazwie, wybierz pozycję **StackExchange.Redis.StrongName**; w innym wypadku wybierz pozycję **StackExchange.Redis**.
>
>

![Pakiet NuGet StackExchange.Redis](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-stackexchange-redis.png)

Pakiet NuGet pobiera i dodaje wymagane odwołania do zestawu umożliwiające aplikacji klienta uzyskanie dostępu do usługi Azure Cache for Redis przy użyciu klienta usługi Azure Cache for Redis StackExchange.

> [!NOTE]
> Jeśli wcześniej skonfigurowano projekt pod kątem pakietu StackExchange.Redis, można sprawdzić dostępność aktualizacji do pakietu z poziomu **Menedżera pakietów NuGet**. Aby sprawdzić i zainstalować zaktualizowane wersje pakietu NuGet StackExchange. Redis, kliknij pozycję **aktualizacje** w oknie **Menedżera pakietów NuGet** . Jeśli dostępna jest aktualizacja pakietu NuGet StackExchange.Redis, można zaktualizować projekt tak, aby korzystał ze zaktualizowanej wersji.
>
>

Można także zainstalować pakiet NuGet StackExchange.Redis, klikając pozycję **Menedżer pakietów NuGet**, **Konsola menedżera pakietów** w menu **Narzędzia**, a następnie uruchamiając następujące polecenie w oknie **Konsola menedżera pakietów**.

```
Install-Package StackExchange.Redis
```
