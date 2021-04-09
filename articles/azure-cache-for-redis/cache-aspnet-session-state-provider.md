---
title: Dostawca stanu sesji ASP.NET pamięci podręcznej
description: Dowiedz się, jak przechowywać stan sesji ASP.NET w pamięci za pomocą usługi Azure cache for Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 05/01/2017
ms.openlocfilehash: ce77f5074d707da5cfb251a103653b96e4644b5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92544532"
---
# <a name="aspnet-session-state-provider-for-azure-cache-for-redis"></a>Dostawca stanu sesji ASP.NET dla usługi Azure Cache for Redis

Pamięć podręczna systemu Azure dla usługi Redis zawiera dostawcę stanu sesji, którego można użyć do przechowywania stanu sesji w pamięci za pomocą usługi Azure cache dla Redis zamiast bazy danych SQL Server. Aby użyć dostawcy stanu sesji buforowania, najpierw Skonfiguruj pamięć podręczną, a następnie skonfiguruj aplikację ASP.NET dla pamięci podręcznej przy użyciu usługi Azure cache dla pakietu NuGet stanu sesji Redis. W przypadku aplikacji ASP.NET Core, Odczytaj [Zarządzanie sesjami i Stanami w ASP.NET Core](/aspnet/core/fundamentals/app-state).

Często nie jest to praktyczne w aplikacji w chmurze w rzeczywistości, aby uniknąć przechowywania niektórych form stanu dla sesji użytkownika, ale niektóre podejścia mają wpływ na wydajność i skalowalność więcej niż inne. Jeśli musisz przechowywać stan, najlepszym rozwiązaniem jest zachowanie małego stanu i zapisanie go w plikach cookie. Jeśli to nie jest możliwe, następnym najlepszym rozwiązaniem jest użycie ASP.NET stanu sesji z dostawcą dla rozproszonej pamięci podręcznej w pamięci. Najgorszym rozwiązaniem z punktu widzenia wydajności i skalowalności jest użycie dostawcy stanu sesji bazy danych. Ten temat zawiera wskazówki dotyczące używania dostawcy stanu sesji ASP.NET dla usługi Azure cache for Redis. Aby uzyskać informacje o innych opcjach stanu sesji, zobacz [ASP.NET Session State Options](#aspnet-session-state-options).

## <a name="store-aspnet-session-state-in-the-cache"></a>Przechowywanie stanu sesji programu ASP.NET w pamięci podręcznej

Aby skonfigurować aplikację kliencką w programie Visual Studio przy użyciu pakietu NuGet usługi Azure cache for Redis, kliknij pozycję **Menedżer pakietów NuGet**, **konsola Menedżera pakietów** z menu **Narzędzia** .

W oknie `Package Manager Console` uruchom następujące polecenie.
    

```powershell
Install-Package Microsoft.Web.RedisSessionStateProvider
```

> [!IMPORTANT]
> Jeśli używasz funkcji klastrowania z warstwy Premium, musisz użyć [pakietu redissessionstateprovider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 lub nowszej lub wyjątek jest zgłaszany. Przechodzenie do wersji 2.0.1 lub wyższej jest istotną zmianą; Aby uzyskać więcej informacji, zobacz artykuł [v 2.0.0 — szczegóły dotyczące zmiany](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details). W momencie aktualizacji tego artykułu bieżąca wersja tego pakietu jest 2.2.3.
> 
> 

Pakiet NuGet dostawcy stanu sesji Redis ma zależność od pakietu StackExchange. Redis. StrongName. Jeśli pakiet StackExchange. Redis. StrongName nie występuje w projekcie, jest on zainstalowany.

>[!NOTE]
>Oprócz silnej nazwy pakietu StackExchange. Redis. StrongName istnieje również StackExchange. Redis, która nie ma silnej nazwy. Jeśli projekt korzysta z niesilnej nazwy StackExchange. Redis, należy ją odinstalować. w przeciwnym razie w projekcie pojawiają się konflikty nazw. Aby uzyskać więcej informacji o tych pakietach, zobacz [Konfigurowanie klientów pamięci podręcznej platformy .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

Pakiet NuGet pobiera i dodaje wymagane odwołania do zestawu i dodaje do pliku web.config następującą sekcję. Ta sekcja zawiera konfigurację wymaganą przez aplikację ASP.NET do korzystania z pamięci podręcznej platformy Azure dla dostawcy stanu sesji Redis.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
  <providers>
    <!-- Either use 'connectionString' OR 'settingsClassName' and 'settingsMethodName' OR use 'host','port','accessKey','ssl','connectionTimeoutInMilliseconds' and 'operationTimeoutInMilliseconds'. -->
    <!-- 'throwOnError','retryTimeoutInMilliseconds','databaseId' and 'applicationName' can be used with both options. -->
    <!--
      <add name="MySessionStateStore" 
        host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
        throwOnError = "true" [true|false]
        retryTimeoutInMilliseconds = "5000" [number]
        databaseId = "0" [number]
        applicationName = "" [String]
        connectionTimeoutInMilliseconds = "5000" [number]
        operationTimeoutInMilliseconds = "1000" [number]
        connectionString = "<Valid StackExchange.Redis connection string>" [String]
        settingsClassName = "<Assembly qualified class name that contains settings method specified below. Which basically return 'connectionString' value>" [String]
        settingsMethodName = "<Settings method should be defined in settingsClass. It should be public, static, does not take any parameters and should have a return type of 'String', which is basically 'connectionString' value.>" [String]
        loggingClassName = "<Assembly qualified class name that contains logging method specified below>" [String]
        loggingMethodName = "<Logging method should be defined in loggingClass. It should be public, static, does not take any parameters and should have a return type of System.IO.TextWriter.>" [String]
        redisSerializerType = "<Assembly qualified class name that implements Microsoft.Web.Redis.ISerializer>" [String]
      />
    -->
    <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider"
         host=""
         accessKey=""
         ssl="true" />
  </providers>
</sessionState>
```

Sekcja z komentarzem zawiera przykład atrybutów i ustawień przykładowych dla każdego atrybutu.

Skonfiguruj atrybuty przy użyciu wartości z bloku pamięci podręcznej w Microsoft Azure Portal i skonfiguruj inne wartości zgodnie z potrzebami. Aby uzyskać instrukcje dotyczące uzyskiwania dostępu do właściwości pamięci podręcznej, zobacz [Konfigurowanie usługi Azure cache for Redis Settings](cache-configure.md#configure-azure-cache-for-redis-settings).

* **host** — Określ punkt końcowy pamięci podręcznej.
* **port** — użyj portu bez protokołu TLS/SSL lub portu TLS/SSL, w zależności od ustawień protokołu TLS.
* **AccessKey** — Użyj klucza podstawowego lub pomocniczego dla pamięci podręcznej.
* **SSL** — prawda, jeśli chcesz zabezpieczyć pamięć podręczną/komunikację z klientem przy użyciu protokołu TLS. w przeciwnym razie false. Upewnij się, że określono prawidłowy port.
  * Port inny niż TLS jest domyślnie wyłączony w przypadku nowych pamięci podręcznych. Określ wartość true dla tego ustawienia, aby używać portu TLS. Aby uzyskać więcej informacji na temat włączania portu bez protokołu TLS, zobacz sekcję [porty dostępu](cache-configure.md#access-ports) w temacie [Konfigurowanie pamięci podręcznej](cache-configure.md) .
* **parametr throwOnError** — wartość true, jeśli chcesz, aby wyjątek był zgłaszany w przypadku niepowodzenia, lub wartość false, jeśli chcesz, aby operacja została zakończona niepowodzeniem. Aby wyszukać błąd, należy sprawdzić Właściwość static Microsoft. Web. Redis. pakietu redissessionstateprovider. LastException. Wartość domyślna to true.
* **retryTimeoutInMilliseconds** — operacje, które nie powiodły się, są ponawiane w tym interwale, określone w milisekundach. Pierwsza ponowna próba nastąpi po 20 milisekundach, a następnie ponowne próby są wykonywane co sekundę do czasu wygaśnięcia interwału retryTimeoutInMilliseconds. Natychmiast po tym czasie operacja zostanie ponowiona po raz ostatni. Jeśli operacja nadal kończy się niepowodzeniem, wyjątek jest generowany z powrotem do obiektu wywołującego, w zależności od ustawienia parametr throwOnError. Wartość domyślna to 0, co oznacza brak ponownych prób.
* **DatabaseID** — określa, która baza danych ma być używana do danych wyjściowych pamięci podręcznej. Jeśli nie zostanie określony, zostanie użyta wartość domyślna 0.
* **ApplicationName** — klucze są przechowywane w Redis jako `{<Application Name>_<Session ID>}_Data` . Ten schemat nazewnictwa umożliwia wielu aplikacjom współużytkowanie tego samego wystąpienia Redis. Ten parametr jest opcjonalny i jeśli nie zostanie on podano, zostanie użyta wartość domyślna.
* **connectionTimeoutInMilliseconds** — to ustawienie umożliwia zastąpienie ustawienia connectTimeout w kliencie stackexchange. Redis. Jeśli nie zostanie określony, zostanie użyte domyślne ustawienie connectTimeout 5000. Aby uzyskać więcej informacji, zobacz [stackexchange. Redis Configuration model](https://go.microsoft.com/fwlink/?LinkId=398705).
* **operationTimeoutInMilliseconds** — to ustawienie umożliwia zastąpienie ustawienia syncTimeout w kliencie stackexchange. Redis. Jeśli nie zostanie określony, zostanie użyte domyślne ustawienie syncTimeout 1000. Aby uzyskać więcej informacji, zobacz [stackexchange. Redis Configuration model](https://go.microsoft.com/fwlink/?LinkId=398705).
* **redisSerializerType** — to ustawienie pozwala określić niestandardową serializację zawartości sesji, która jest wysyłana do Redis. Określony typ musi implementować `Microsoft.Web.Redis.ISerializer` i musi deklarować publiczny Konstruktor bez parametrów. Domyślnie  `System.Runtime.Serialization.Formatters.Binary.BinaryFormatter` jest używana.

Aby uzyskać więcej informacji o tych właściwościach, zapoznaj się z ogłoszeniem oryginalnego wpisu w blogu, w którym jest [ogłaszany dostawca stanu sesji ASP.NET dla usługi Redis](https://devblogs.microsoft.com/aspnet/announcing-asp-net-session-state-provider-for-redis-preview-release/).

Nie zapomnij dodać komentarza do sekcji dostawca stanu sesji InProc w web.config.

```xml
<!-- <sessionState mode="InProc"
     customProvider="DefaultSessionProvider">
     <providers>
        <add name="DefaultSessionProvider"
              type="System.Web.Providers.DefaultSessionStateProvider,
                    System.Web.Providers, Version=1.0.0.0, Culture=neutral,
                    PublicKeyToken=31bf3856ad364e35"
              connectionStringName="DefaultConnection" />
      </providers>
</sessionState> -->
```

Po wykonaniu tych kroków aplikacja jest skonfigurowana do korzystania z usługi Azure cache dla dostawcy stanu sesji Redis. Gdy używasz stanu sesji w aplikacji, jest on przechowywany w pamięci podręcznej platformy Azure dla wystąpienia Redis.

> [!IMPORTANT]
> Dane przechowywane w pamięci podręcznej muszą być możliwe do serializacji, w przeciwieństwie do danych, które mogą być przechowywane w domyślnym dostawcy stanu sesji ASP.NET w pamięci. Gdy jest używany dostawca stanu sesji dla Redis, należy się upewnić, że typy danych przechowywane w stanie sesji są możliwe do serializacji.
> 
> 

## <a name="aspnet-session-state-options"></a>Opcje stanu sesji ASP.NET

* Dostawca stanu sesji w pamięci — ten dostawca przechowuje stan sesji w pamięci. Korzyści wynikające z używania tego dostawcy są proste i szybkie. Nie można jednak skalować Web Apps, jeśli używasz w dostawcy pamięci, ponieważ nie jest on dystrybuowany.
* Dostawca stanu sesji programu SQL Server — ten dostawca przechowuje stan sesji w programie SQL Server. Użyj tego dostawcy, jeśli chcesz przechowywać stan sesji w magazynie trwałym. Możesz skalować aplikację sieci Web, ale korzystanie z programu SQL Server dla sesji ma wpływ na wydajność aplikacji sieci Web. Tego dostawcy można także użyć z [konfiguracją OLTP w pamięci](/archive/blogs/sqlserverstorageengine/asp-net-session-state-with-sql-server-in-memory-oltp) , aby zwiększyć wydajność.
* Dostawca stanu sesji rozproszonej w pamięci, taki jak usługa Azure cache dla dostawcy stanu sesji Redis — ten dostawca zapewnia najlepsze rozwiązanie obu rozwiązań. Aplikacja sieci Web może mieć prosty, szybki i skalowalny dostawca stanu sesji. Ponieważ ten dostawca przechowuje stan sesji w pamięci podręcznej, należy wziąć pod uwagę wszystkie cechy skojarzone podczas rozmowy z pamięcią podręczną rozproszonej pamięci, na przykład przejściowe awarie sieci. Aby zapoznać się z najlepszymi rozwiązaniami dotyczącymi używania pamięci podręcznej, zobacz [wskazówki dotyczące buforowania](/azure/architecture/best-practices/caching) ze wzorców firmy Microsoft & wskazówki [dotyczące projektowania i wdrażania aplikacji w chmurze Azure](https://github.com/mspnp/azure-guidance)

Aby uzyskać więcej informacji o stanie sesji i innych najlepszych rozwiązaniach, zobacz [najlepsze rozwiązania dotyczące programowania w sieci Web (kompilowanie Real-World aplikacji w chmurze przy użyciu platformy Azure)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices).

## <a name="third-party-session-state-providers"></a>Dostawcy stanu sesji innych firm

* [NCache](https://www.alachisoft.com/ncache/session-index.html)
* [Apache zapłon](https://apacheignite-net.readme.io/docs/aspnet-session-state-caching)

## <a name="next-steps"></a>Następne kroki

Sprawdź [dostawcę wyjściowej pamięci Podręcznej ASP.NET dla usługi Azure cache for Redis](cache-aspnet-output-cache-provider.md).