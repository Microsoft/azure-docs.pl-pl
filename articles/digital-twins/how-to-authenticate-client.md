---
title: Pisanie kodu uwierzytelniania aplikacji
titleSuffix: Azure Digital Twins
description: Zobacz jak pisać kod uwierzytelniania w aplikacji klienckiej
author: baanders
ms.author: baanders
ms.date: 10/7/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 84cb7e7e98e81e242ec1cac554fe073370e45645
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92495788"
---
# <a name="write-client-app-authentication-code"></a>Napisz kod uwierzytelniania aplikacji klienckiej

Po [skonfigurowaniu wystąpienia i uwierzytelniania usługi Azure Digital bliźniaczych reprezentacji](how-to-set-up-instance-portal.md)można utworzyć aplikację kliencką, która będzie używana do współpracy z wystąpieniem. Po skonfigurowaniu początkowego projektu klienta należy **napisać kod w aplikacji klienckiej w celu uwierzytelnienia go** w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji.

Usługa Azure Digital bliźniaczych reprezentacji wykonuje uwierzytelnianie przy użyciu [tokenów zabezpieczeń usługi Azure AD opartych na protokole OAUTH 2,0](../active-directory/develop/security-tokens.md#json-web-tokens-jwts-and-claims). Aby uwierzytelnić zestaw SDK, musisz uzyskać token okaziciela z właściwymi uprawnieniami do usługi Azure Digital bliźniaczych reprezentacji i przekazać go wraz z wywołaniami interfejsu API. 

W tym artykule opisano sposób uzyskiwania poświadczeń przy użyciu `Azure.Identity` biblioteki klienckiej. Chociaż w tym artykule przedstawiono przykłady kodu w języku C#, takie jak zapis dla [zestawu SDK platformy .NET (c#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet-preview&preserve-view=true), możesz użyć wersji `Azure.Identity` niezależnie od używanego zestawu SDK (Aby uzyskać więcej informacji na temat zestawów SDK dostępnych dla usługi Azure Digital bliźniaczych reprezentacji, zobacz How to [*: Use the Azure Digital bliźniaczych reprezentacji API and SDK*](how-to-use-apis-sdks.md).

## <a name="prerequisites"></a>Wymagania wstępne

Najpierw wykonaj kroki instalacji opisane w temacie [*How to: Set a instance and Authentication*](how-to-set-up-instance-portal.md). Zapewni to, że masz wystąpienie usługi Azure Digital bliźniaczych reprezentacji, użytkownik ma uprawnienia dostępu i ustawił uprawnienia dla aplikacji klienckich. Po skonfigurowaniu tej konfiguracji możesz przystąpić do pisania kodu aplikacji klienta.

Aby można było wykonać operację, potrzebny będzie projekt aplikacji klienckiej, w którym napisano kod. Jeśli nie masz jeszcze skonfigurowanego projektu aplikacji klienckiej, Utwórz podstawowy projekt w wybranym języku, który ma być używany z tym samouczkiem.

## <a name="common-authentication-methods-with-azureidentity"></a>Typowe metody uwierzytelniania przy użyciu platformy Azure. Identity

`Azure.Identity` jest biblioteką klienta udostępniającą kilka metod uzyskiwania poświadczeń, których można użyć w celu uzyskania tokenu okaziciela i uwierzytelnienia w zestawie SDK. Mimo że ten artykuł zawiera przykłady w języku C#, można wyświetlić `Azure.Identity` w kilku językach, w tym...
* [.NET (C#)](/dotnet/api/azure.identity?preserve-view=true&view=azure-dotnet)
* [Java](/java/api/overview/azure/identity-readme?preserve-view=true&view=azure-java-stable)
* [JavaScript](/javascript/api/overview/azure/identity-readme?preserve-view=true&view=azure-node-latest)
* [Python](/python/api/overview/azure/identity-readme?preserve-view=true&view=azure-python)

Trzy popularne metody uzyskiwania poświadczeń w programie `Azure.Identity` to:
* [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) zapewnia domyślny `TokenCredential` przepływ uwierzytelniania dla aplikacji, które zostaną wdrożone na platformie Azure, i jest **zalecanym wyborem dla lokalnego tworzenia**. Można go również włączyć, aby wypróbować inne dwie metody zalecane w tym artykule. Zawija `ManagedIdentityCredential` i ma dostęp do `InteractiveBrowserCredential` zmiennej konfiguracyjnej.
* [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential?preserve-view=true&view=azure-dotnet) działa doskonale w przypadkach, gdy potrzebne są [tożsamości zarządzane (msi)](../active-directory/managed-identities-azure-resources/overview.md), i jest dobrym kandydatem do pracy z Azure Functions i wdrażaniem w usługach platformy Azure.
* [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?preserve-view=true&view=azure-dotnet) jest przeznaczony dla aplikacji interaktywnych i może służyć do tworzenia uwierzytelnionego klienta SDK

Poniższy przykład pokazuje, jak używać każdego z nich z zestawem SDK .NET (C#).

## <a name="authentication-examples-net-c-sdk"></a>Przykłady uwierzytelniania: .NET (C#) SDK

W tej sekcji przedstawiono przykład w języku C# służący do pisania kodu uwierzytelniania przy użyciu dostarczonego zestawu .NET SDK.

Najpierw Dołącz pakiet SDK `Azure.DigitalTwins.Core` i `Azure.Identity` pakiet do projektu. W zależności od wybranych narzędzi można dołączyć pakiety przy użyciu Menedżera pakietów programu Visual Studio lub `dotnet` narzędzia wiersza polecenia. 

Należy również dodać następujące instrukcje using do kodu projektu:

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
```

Następnie Dodaj kod, aby uzyskać poświadczenia przy użyciu jednej z metod w `Azure.Identity` .

### <a name="defaultazurecredential-method"></a>DefaultAzureCredential, Metoda

[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) zapewnia domyślny `TokenCredential` przepływ uwierzytelniania dla aplikacji, które zostaną wdrożone na platformie Azure, i jest **zalecanym wyborem dla lokalnego tworzenia**.

Aby użyć domyślnych poświadczeń platformy Azure, musisz mieć adres URL wystąpienia usługi Azure Digital bliźniaczych reprezentacji ([instrukcje do znalezienia](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).

Oto przykładowy kod umożliwiający dodanie elementu `DefaultAzureCredential` do projektu:

```csharp
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-URL>";

//...

DigitalTwinsClient client;
try
{
    var credential = new DefaultAzureCredential();
    client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
} catch(Exception e)
{
    Console.WriteLine($"Authentication or client creation error: {e.Message}");
    Environment.Exit(0);
}
```

#### <a name="set-up-local-azure-credentials"></a>Konfigurowanie lokalnych poświadczeń platformy Azure

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](../../includes/digital-twins-local-credentials-inner.md)]

### <a name="managedidentitycredential-method"></a>ManagedIdentityCredential, Metoda

Metoda [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential?preserve-view=true&view=azure-dotnet) działa dobrze w przypadkach, gdy potrzebne są [tożsamości zarządzane (msi)](../active-directory/managed-identities-azure-resources/overview.md)— na przykład podczas pracy z Azure Functions.

Oznacza to, że można użyć `ManagedIdentityCredential` w tym samym projekcie co `DefaultAzureCredential` lub `InteractiveBrowserCredential` , aby uwierzytelnić inną część projektu.

Aby użyć domyślnych poświadczeń platformy Azure, musisz mieć adres URL wystąpienia usługi Azure Digital bliźniaczych reprezentacji ([instrukcje do znalezienia](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).

W funkcji platformy Azure można użyć poświadczeń tożsamości zarządzanej, takich jak:

```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
DigitalTwinsClientOptions opts = 
    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
```

### <a name="interactivebrowsercredential-method"></a>InteractiveBrowserCredential, Metoda

Metoda [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?preserve-view=true&view=azure-dotnet) jest przeznaczona dla aplikacji interaktywnych i umożliwia wyświetlenie przeglądarki sieci Web w celu uwierzytelnienia. Można jej użyć zamiast `DefaultAzureCredential` w przypadku, gdy wymagane jest uwierzytelnianie interaktywne.

Aby można było używać poświadczeń interaktywnej przeglądarki, wymagana jest **Rejestracja aplikacji** z uprawnieniami do interfejsów API Digital bliźniaczych reprezentacji platformy Azure. Aby uzyskać instrukcje dotyczące konfigurowania rejestracji aplikacji, zobacz [*How to: Create a App Registration*](how-to-create-app-registration.md). Po skonfigurowaniu rejestracji aplikacji będziesz potrzebować...
* *Identyfikator aplikacji (klienta)* rejestracji aplikacji ([instrukcje dotyczące znajdowania](how-to-create-app-registration.md#collect-client-id-and-tenant-id))
* Identyfikator katalogu rejestracji aplikacji ( *dzierżawy)* ([instrukcje do znalezienia](how-to-create-app-registration.md#collect-client-id-and-tenant-id))
* adres URL wystąpienia usługi Azure Digital bliźniaczych reprezentacji ([instrukcje dotyczące znajdowania](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))

Oto przykładowy kod umożliwiający utworzenie uwierzytelnionego klienta SDK przy użyciu programu `InteractiveBrowserCredential` .

```csharp
// Your client / app registration ID
private static string clientId = "<your-client-ID>"; 
// Your tenant / directory ID
private static string tenantId = "<your-tenant-ID>";
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-URL>";

//...

DigitalTwinsClient client;
try
{
    var credential = new InteractiveBrowserCredential(tenantId, clientId);
    client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
} catch(Exception e)
{
    Console.WriteLine($"Authentication or client creation error: {e.Message}");
    Environment.Exit(0);
}
```

>[!NOTE]
> Identyfikator klienta, identyfikator dzierżawy i adres URL wystąpienia można umieścić bezpośrednio w kodzie jak pokazano powyżej. dobrym pomysłem jest, że kod pobiera te wartości z pliku konfiguracji lub zmiennej środowiskowej.

#### <a name="other-notes-about-authenticating-azure-functions"></a>Inne uwagi dotyczące uwierzytelniania Azure Functions

Zapoznaj się z tematem [*jak to zrobić: Konfigurowanie funkcji platformy Azure do przetwarzania danych*](how-to-create-azure-function.md) w celu przeprowadzenia pełniejszego przykładu, w którym objaśniono niektóre ważne opcje konfiguracji w kontekście funkcji.

Ponadto, aby użyć uwierzytelniania w funkcji, pamiętaj, aby:
* [Włączanie tożsamości zarządzanej](../app-service/overview-managed-identity.md?tabs=dotnet)
* Użyj [zmiennych środowiskowych](/sandbox/functions-recipes/environment-variables?tabs=csharp) stosownie do potrzeb
* Przypisz uprawnienia do aplikacji funkcji, która umożliwia jej dostęp do interfejsów API Digital bliźniaczych reprezentacji. Aby uzyskać więcej informacji na temat Azure Functions procesów, zobacz [*How to: set up a Azure Function for processing Data*](how-to-create-azure-function.md).

## <a name="other-credential-methods"></a>Inne metody poświadczeń

Jeśli wyróżnione scenariusze uwierzytelniania powyżej nie obejmują potrzeb aplikacji, można poznać inne typy uwierzytelniania oferowane na [**platformie tożsamości firmy Microsoft**](../active-directory/develop/v2-overview.md#getting-started). Dokumentacja tej platformy obejmuje dodatkowe scenariusze uwierzytelniania uporządkowane według typu aplikacji.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak działa zabezpieczenia w usłudze Azure Digital bliźniaczych reprezentacji:
* [*Koncepcje: zabezpieczenia rozwiązań Digital bliźniaczych reprezentacji na platformie Azure*](concepts-security.md)

Lub teraz, gdy uwierzytelnianie jest skonfigurowane, przejdź do w celu utworzenia modeli w wystąpieniu:
* [*Instrukcje: Zarządzanie modelami niestandardowymi*](how-to-manage-model.md)