---
title: Serializacja pamięci podręcznej tokenów (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Informacje o serializacji i serializacji klienta pamięci podręcznej tokenów przy użyciu biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/16/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 7e80123f21efded92ab6d59d550965ca72427b1c
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98064661"
---
# <a name="token-cache-serialization-in-msalnet"></a>Serializacja pamięci podręcznej tokenów w MSAL.NET
Po [uzyskaniu tokenu](msal-acquire-cache-tokens.md)zostanie on zbuforowany przez bibliotekę uwierzytelniania firmy Microsoft (MSAL).  Kod aplikacji powinien próbować uzyskać token z pamięci podręcznej przed uzyskaniem tokenu przez inną metodę.  W tym artykule omówiono domyślną i niestandardową serializację pamięci podręcznej tokenów w MSAL.NET.

Ten artykuł dotyczy MSAL.NET 3. x. Jeśli interesuje Cię MSAL.NET 2. x, zobacz [Serializacja pamięci podręcznej tokenów w MSAL.NET 2. x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Token-cache-serialization-2x).

## <a name="default-serialization-for-mobile-platforms"></a>Domyślna Serializacja dla platform mobilnych

W MSAL.NET jest udostępniana pamięć podręczna tokenów w pamięci. Serializacja jest udostępniana domyślnie dla platform, w których bezpieczny magazyn jest dostępny dla użytkownika jako część platformy. W tym przypadku platforma uniwersalna systemu Windows (platformy UWP), Xamarin. iOS i Xamarin. Android.

> [!Note]
> Podczas migrowania projektu Xamarin. Android z MSAL.NET 1. x do MSAL.NET 3. x, możesz chcieć dodać `android:allowBackup="false"` do projektu, aby uniknąć powrotu starych tokenów w pamięci podręcznej, gdy wdrożenia programu Visual Studio wyzwalają przywracanie magazynu lokalnego. Zobacz [#659 problemu](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/659#issuecomment-436181938).

## <a name="custom-serialization-for-windows-desktop-apps-and-web-appsweb-apis"></a>Serializacja niestandardowa dla aplikacji klasycznych systemu Windows i aplikacji sieci Web/interfejsów API sieci Web

Należy pamiętać, że Serializacja niestandardowa nie jest dostępna na platformach mobilnych (platformy UWP, Xamarin. iOS i Xamarin. Android). MSAL już definiuje bezpieczny i wydajny mechanizm serializacji dla tych platform. Aplikacje klasyczne i .NET Core programu .NET mają jednak różne architektury i MSAL nie mogą implementować mechanizmu serializacji ogólnego przeznaczenia. Na przykład witryny sieci Web mogą zawierać tokeny w pamięci podręcznej Redis, a aplikacje pulpitu przechowują tokeny w zaszyfrowanym pliku. Dlatego Serializacja nie jest dostarczana jako wbudowana. Aby mieć trwałą aplikację pamięci podręcznej tokenów w programie .NET Desktop lub .NET Core, należy dostosować serializację.

Następujące klasy i interfejsy są używane w serializacji pamięci podręcznej tokenu:

- `ITokenCache`, który definiuje zdarzenia subskrybowania żądań serializacji pamięci podręcznej tokenów, a także metody serializacji lub deserializacji pamięci podręcznej w różnych formatach (ADAL v 3.0, MSAL 2. x i MSAL 3. x = ADAL v 5.0).
- `TokenCacheCallback` to wywołanie zwrotne przesłane do zdarzeń, aby można było obsłużyć serializacji. Zostaną one wywołane z argumentami typu `TokenCacheNotificationArgs` .
- `TokenCacheNotificationArgs` udostępnia tylko `ClientId` aplikację i odwołanie do użytkownika, dla którego token jest dostępny.

  ![Diagram klas](media/msal-net-token-cache-serialization/class-diagram.png)

> [!IMPORTANT]
> MSAL.NET tworzy pamięć podręczną tokenów i udostępnia `IToken` pamięć podręczną, gdy wywołasz aplikację `UserTokenCache` i `AppTokenCache` właściwości. Nie zaleca się zaimplementowania interfejsu samodzielnie. Podczas implementowania serializacji pamięci podręcznej tokenów niestandardowych należy wykonać następujące zadanie:
> - Reagowanie na `BeforeAccess` i `AfterAccess` "zdarzenia" (lub ich ich typy asynchroniczne). `BeforeAccess`Delegat jest odpowiedzialny za deserializacja pamięci podręcznej, podczas gdy `AfterAccess` jest on odpowiedzialny za Serializowanie pamięci podręcznej.
> - Część tych zdarzeń przechowuje lub ładuje obiekty blob, które są przesyłane przez argument zdarzenia do dowolnego potrzebnego miejsca.

Strategie są różne w zależności od tego, czy piszesz serializacji pamięci podręcznej tokenów dla [publicznej aplikacji klienckiej](msal-client-applications.md) (klasycznej), czy [poufnej aplikacji klienckiej](msal-client-applications.md)) (aplikacja sieci Web/internetowy interfejs API, aplikacja demona).

### <a name="token-cache-for-a-public-client"></a>Pamięć podręczna tokenów dla klienta publicznego

Ponieważ MSAL.NET V2. x istnieje kilka opcji serializacji pamięci podręcznej tokenów klienta publicznego. Pamięć podręczną można serializować tylko do formatu MSAL.NET (pamięć podręczna Unified format jest wspólna dla MSAL i platform).  Można także obsłużyć serializacji [starszej](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) pamięci podręcznej tokenów biblioteki ADAL v3.

Dostosowanie serializacji pamięci podręcznej tokenów w celu udostępnienia stanu logowania jednokrotnego między ADAL.NET 3. x, ADAL.NET 5. x i MSAL.NET jest wyjaśnione w części poniższego przykładu: [Active-Directory-dotnet-V1-do-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

> [!Note]
> Format pamięci podręcznej tokenów MSAL.NET 1.1.4 (wersja zapoznawcza) nie jest już obsługiwany w MSAL 2. x. Jeśli masz aplikacje korzystające z MSAL.NET 1. x, użytkownicy będą musieli ponowić logowanie. Alternatywnie, obsługiwana jest migracja z biblioteki ADAL 4. x (i 3. x).

#### <a name="simple-token-cache-serialization-msal-only"></a>Serializacja prostej pamięci podręcznej tokenów (tylko MSAL)

Poniżej znajduje się przykład implementacji algorytmie niestandardowej serializacji pamięci podręcznej tokenów dla aplikacji klasycznych. W tym miejscu pamięć podręczna tokenów użytkownika to plik w tym samym folderze, w którym znajduje się aplikacja.

Po skompilowaniu aplikacji należy włączyć serializację, wywołując `TokenCacheHelper.EnableSerialization()` metodę i przekazując aplikację `UserTokenCache` .

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

`TokenCacheHelper`Klasa pomocnika jest definiowana jako:

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache. Note that this could be something different for instance for MSIX applications:
  /// private static readonly string CacheFilePath =
  /// $"{Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData)}\{AppName}\msalcache.bin";
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

  private static readonly object FileLock = new object();


  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

Serializator oparty na plikach pamięci podręcznej tokenu jakości produktu dla publicznych aplikacji klienckich (dla aplikacji klasycznych działających w systemach Windows, Mac i Linux) jest dostępny w bibliotece [Microsoft. Identity. Client. Extensions. Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) typu open source. Można go uwzględnić w aplikacjach z następującego pakietu NuGet: [Microsoft. Identity. Client. Extensions. Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

#### <a name="dual-token-cache-serialization-msal-unified-cache-and-adal-v3"></a>Serializacja podwójnego tokenu pamięci podręcznej (MSAL Unified cache i ADAL v3)

Jeśli chcesz zaimplementować serializacji pamięci podręcznej tokenu w formacie ujednoliconej pamięci podręcznej (wspólne dla ADAL.NET 4. x, MSAL.NET 2. x i inne MSALs tej samej generacji lub starszej, na tej samej platformie), zapoznaj się z następującym kodem:

```csharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

Tym razem Klasa pomocnika zdefiniowana jako:

```csharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Enables the serialization of the token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache tokenCache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

### <a name="token-cache-for-a-web-app-confidential-client-application"></a>Pamięć podręczna tokenów dla aplikacji sieci Web (poufne aplikacje klienckie)

W przypadku aplikacji sieci Web lub interfejsów API sieci Web pamięć podręczna może korzystać z sesji, pamięci podręcznej Redis lub bazy danych. Należy zachować jedną pamięć podręczną tokenów dla każdego konta w aplikacjach sieci Web lub API sieci Web. 

W przypadku usługi Web Apps bufor tokenów powinien być poprzedzony IDENTYFIKATORem konta.

W przypadku interfejsów API sieci Web konto powinno być poprzedzone przez skrót tokenu używanego do wywoływania interfejsu API.

MSAL.NET zapewnia serializowaną niestandardową pamięć podręczną tokenów w ramach podplatform .NET Framework i .NET Core. Zdarzenia są wywoływane podczas uzyskiwania dostępu do pamięci podręcznej, aplikacje mogą wybrać, czy należy serializować czy zdeserializować pamięci podręcznej. Poufne aplikacje klienckie, które obsługują użytkowników (aplikacje sieci Web, które logują użytkowników i wywołują interfejsy API sieci Web, oraz interfejsy API sieci Web wywołujące podrzędne interfejsy API sieci Web), mogą być wielu użytkowników i są przetwarzane równolegle. Ze względu na bezpieczeństwo i wydajność nasze zalecenia polegają na serializacji jednej pamięci podręcznej na użytkownika. Zdarzenia serializacji obliczają klucz pamięci podręcznej na podstawie tożsamości przetworzonego użytkownika i serializacji/deserializacji pamięci podręcznej tokenów dla tego użytkownika.

Biblioteka [Microsoft. Identity. Web](https://github.com/AzureAD/microsoft-identity-web) oferuje wersję zapoznawczą pakietu NuGet [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) zawierający serializacji pamięci podręcznej tokenu:

| Metoda rozszerzenia | Podrzędna przestrzeń nazw Microsoft. Identity. Web | Opis  |
| ---------------- | --------- | ------------ |
| `AddInMemoryTokenCaches` | `TokenCacheProviders.InMemory` | W serializacji pamięci podręcznej tokenu pamięci. Ta implementacja doskonale nadaje się do przykładów. Jest to również dobre w aplikacjach produkcyjnych, pod warunkiem, że pamięć podręczna tokenu zostanie utracona po ponownym uruchomieniu aplikacji sieci Web. `AddInMemoryTokenCaches` przyjmuje opcjonalny parametr typu `MsalMemoryTokenCacheOptions` , który umożliwia określenie czasu, po którym wpis pamięci podręcznej zostanie wygaśnie, chyba że jest używany.
| `AddSessionTokenCaches` | `TokenCacheProviders.Session` | Pamięć podręczna tokenów jest powiązana z sesją użytkownika. Ta opcja nie jest idealnym rozwiązaniem, jeśli token identyfikatora zawiera wiele oświadczeń, ponieważ plik cookie staje się zbyt duży.
| `AddDistributedTokenCaches` | `TokenCacheProviders.Distributed` | Pamięć podręczna tokenów jest kartą z `IDistributedCache` implementacją ASP.NET Core, co umożliwia wybranie między pamięcią podręczną pamięci rozproszonej, pamięci podręcznej Redis, rozproszonego NCache lub pamięci podręcznej SQL Server. Aby uzyskać szczegółowe informacje na temat `IDistributedCache` implementacji, zobacz https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache .

Oto przykład użycia pamięci podręcznej w pamięci w metodzie [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) klasy [startowej](/aspnet/core/fundamentals/startup) w aplikacji ASP.NET Core:

```C#
// or use a distributed Token Cache by adding
    services.AddSignIn(Configuration);
    services.AddWebAppCallsProtectedWebApi(Configuration, new string[] { scopesToRequest })
            .AddInMemoryTokenCaches();
```

Przykłady możliwych rozproszonej pamięci podręcznej:

```C#
// or use a distributed Token Cache by adding
    services.AddSignIn(Configuration);
    services.AddWebAppCallsProtectedWebApi(Configuration, new string[] { scopesToRequest })
            .AddDistributedTokenCaches();

// and then choose your implementation

// For instance the distributed in memory cache (not cleared when you stop the app)
services.AddDistributedMemoryCache()

// Or a Redis cache
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

Ich użycie jest proponowane w [samouczku ASP.NET Core Web App](/aspnet/core/tutorials/first-mvc-app/) w [Pamięci Podręcznej tokenów 2-2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache).

## <a name="next-steps"></a>Następne kroki

W poniższych przykładach przedstawiono serializacji pamięci podręcznej tokenów.

| Przykład | Platforma | Opis|
| ------ | -------- | ----------- |
|[Active-Directory-dotnet-Desktop-MSGraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | Pulpit (WPF) | Aplikacja Windows Desktop .NET (WPF) wywołuje interfejs API Microsoft Graph. ![Diagram przedstawia topologię z aplikacją klasyczną W P F TodoListClient przepływa do platformy Azure A D przez uzyskanie tokenu interaktywnie i Microsoft Graph.](media/msal-net-token-cache-serialization/topology.png)|
|[Active-Directory-dotnet-V1-do-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2) | Pulpit (konsola) | Zestaw rozwiązań Visual Studio ilustrujących migrację aplikacji usługi Azure AD v 1.0 (przy użyciu programu ADAL.NET) do aplikacji platformy tożsamości firmy Microsoft (przy użyciu MSAL.NET). W szczególności zobacz [Migrowanie pamięci podręcznej tokenu](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/blob/master/TokenCacheMigration/README.md)|
