---
title: Używanie uwierzytelniania usługi Azure AD w celu uzyskiwania dostępu do interfejsu API Azure Media Services przy użyciu platformy .NET | Microsoft Docs
description: W tym temacie przedstawiono sposób użycia uwierzytelniania usługi Azure Active Directory (Azure AD) w celu uzyskania dostępu do interfejsu API Azure Media Services (AMS) przy użyciu platformy .NET.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: has-adal-ref, devx-track-csharp
ms.openlocfilehash: fd19bce05d18eb8f9b8fd3806082caeebe98bdad
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013996"
---
# <a name="use-azure-ad-authentication-to-access-azure-media-services-api-with-net"></a>Używanie uwierzytelniania usługi Azure AD w celu uzyskiwania dostępu do interfejsu API Azure Media Services przy użyciu platformy .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją [Media Services wersja 3](../latest/index.yml). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-v-2-v-3-migration-introduction.md)

Począwszy od windowsazure. MediaServices 4.0.0.4, Azure Media Services obsługuje uwierzytelnianie oparte na Azure Active Directory (Azure AD). W tym temacie pokazano, jak za pomocą uwierzytelniania usługi Azure AD uzyskać dostęp do interfejsu API Azure Media Services przy użyciu Microsoft .NET.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
- Konto usługi Media Services. Aby uzyskać więcej informacji, zobacz [Tworzenie konta Azure Media Services przy użyciu Azure Portal](media-services-portal-create-account.md).
- Najnowszy pakiet [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) .
- Znajomość tematu [uzyskiwania dostępu do interfejsu API Azure Media Services przy użyciu uwierzytelniania usługi Azure AD — Omówienie](media-services-use-aad-auth-to-access-ams-api.md).

W przypadku korzystania z uwierzytelniania usługi Azure AD za pomocą Azure Media Services można uwierzytelnić się na jeden z dwóch sposobów:

- **Uwierzytelnianie użytkownika** służy do uwierzytelniania osoby, która używa aplikacji do współdziałania z zasobami Azure Media Services. Aplikacja interaktywna powinna najpierw monitować użytkownika o poświadczenia. Przykładem jest aplikacja konsoli zarządzania, która jest używana przez autoryzowanych użytkowników do monitorowania zadań kodowania lub przesyłania strumieniowego na żywo.
- **Uwierzytelnianie jednostki usługi** uwierzytelnia usługę. Aplikacje, które często używają tej metody uwierzytelniania, to aplikacje, które uruchamiają usługi demonów, usługi warstwy środkowej lub zaplanowane zadania, takie jak aplikacje sieci Web, aplikacje funkcji, Aplikacje logiki, interfejsy API lub mikrousługi.

>[!IMPORTANT]
>Usługa Azure Media Service obsługuje obecnie model uwierzytelniania usługi Azure Access Control Service. Jednak autoryzacja Access Control będzie przestarzała w dniu 22 czerwca 2018. Zalecamy przeprowadzenie migracji do modelu uwierzytelniania Azure Active Directory najszybciej, jak to możliwe.

## <a name="get-an-azure-ad-access-token"></a>Uzyskiwanie tokenu dostępu usługi Azure AD

Aby nawiązać połączenie z interfejsem API Azure Media Services przy użyciu uwierzytelniania usługi Azure AD, aplikacja kliencka musi zażądać tokenu dostępu usługi Azure AD. W przypadku korzystania z zestawu SDK klienta programu Media Services .NET wiele szczegółów dotyczących uzyskiwania tokenu dostępu usługi Azure AD jest opakowane i uproszczone dla Ciebie w klasach [AzureAdTokenProvider](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenProvider.cs) i [AzureAdTokenCredentials](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenCredentials.cs) .

Nie trzeba na przykład podawać informacji o urzędzie usługi Azure AD, identyfikatorze URI zasobu Media Services ani natywnych szczegółach aplikacji usługi Azure AD. Są to dobrze znane wartości, które są już skonfigurowane przez klasę dostawcy tokenów dostępu usługi Azure AD.

Jeśli nie korzystasz z zestawu .NET SDK usługi Azure Media Service, zalecamy korzystanie z [biblioteki uwierzytelniania usługi Azure AD](../../active-directory/azuread-dev/active-directory-authentication-libraries.md). Aby uzyskać wartości parametrów, które mają być używane z biblioteką uwierzytelniania usługi Azure AD, zobacz [użyj Azure Portal, aby uzyskać dostęp do ustawień uwierzytelniania usługi Azure AD](media-services-portal-get-started-with-aad.md).

Istnieje również możliwość zastępowania domyślnej implementacji **AzureAdTokenProvider** przy użyciu własnej implementacji.

## <a name="install-and-configure-azure-media-services-net-sdk"></a>Instalowanie i Konfigurowanie Azure Media Services .NET SDK

>[!NOTE]
>Aby używać uwierzytelniania usługi Azure AD z zestawem SDK Media Services platformy .NET, należy dysponować najnowszym pakietem [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) . Należy również dodać odwołanie do zestawu **Microsoft. IdentityModel. clients. ActiveDirectory** . Jeśli używasz istniejącej aplikacji, Dołącz zestaw **Microsoft.WindowsAzure.MediaServices.Client.Common.Authentication.dll** .

1. Utwórz nową aplikację konsoli języka C# w programie Visual Studio.
2. Użyj pakietu NuGet [windowsazure. MediaServices](https://www.nuget.org/packages/windowsazure.mediaservices) , aby zainstalować **Azure Media Services .NET SDK**.

    Aby dodać odwołania przy użyciu NuGet, wykonaj następujące czynności: w **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy nazwę projektu, a następnie wybierz pozycję **Zarządzaj pakietami NuGet**. Następnie wyszukaj ciąg **windowsazure. MediaServices** i wybierz pozycję **Zainstaluj**.

    -lub-

    Uruchom następujące polecenie w **konsoli Menedżera pakietów** w programie Visual Studio.

    ```console
    Install-Package windowsazure.mediaservices -Version 4.0.0.4
    ```

3. Dodaj **użycie** do kodu źródłowego.

    ```csharp
    using Microsoft.WindowsAzure.MediaServices.Client;
    ```

## <a name="use-user-authentication"></a>Korzystanie z uwierzytelniania użytkowników

Aby nawiązać połączenie z interfejsem API usługi Azure Media Service przy użyciu opcji uwierzytelniania użytkownika, aplikacja kliencka musi zażądać tokenu usługi Azure AD przy użyciu następujących parametrów:

- Punkt końcowy dzierżawy usługi Azure AD. Informacje o dzierżawie można pobrać z Azure Portal. Umieść kursor nad zalogowanym użytkownikiem w prawym górnym rogu.
- Media Services identyfikator URI zasobu.
- Identyfikator klienta aplikacji Media Services (natywny).
- Media Services (natywny) identyfikator URI przekierowania aplikacji.

Wartości tych parametrów można znaleźć w **AzureEnvironments. AzureCloudEnvironment**. Stała **AzureEnvironments. AzureCloudEnvironment** jest pomocnikiem zestawu .NET SDK, aby uzyskać odpowiednie ustawienia zmiennych środowiskowych dla publicznego centrum danych platformy Azure.

Zawiera wstępnie zdefiniowane ustawienia środowiska umożliwiające dostęp do Media Services tylko w publicznych centrach danych. W przypadku regionów w chmurze suwerennych lub rządowych można odpowiednio użyć **AzureChinaCloudEnvironment**, **AzureUsGovernmentEnvironment** lub **AzureGermanCloudEnvironment** .

Poniższy przykład kodu tworzy token:

```csharp
var tokenCredentials = new AzureAdTokenCredentials("microsoft.onmicrosoft.com", AzureEnvironments.AzureCloudEnvironment);
var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
```

Aby rozpocząć programowanie względem Media Services, należy utworzyć wystąpienie **CloudMediaContext** reprezentujące kontekst serwera. **CloudMediaContext** zawiera odwołania do ważnych kolekcji, w tym zadań, zasobów, plików, zasad dostępu i lokalizatorów.

Należy również przekazać **Identyfikator URI zasobu dla usług REST usługi Media** do konstruktora **CloudMediaContext** . Aby uzyskać identyfikator URI zasobu dla usług Media REST, zaloguj się do Azure Portal, wybierz konto Azure Media Services, wybierz pozycję **dostęp do interfejsu API**, a następnie wybierz pozycję **Połącz z Azure Media Services z uwierzytelnianiem użytkowników**.

Poniższy przykład kodu tworzy wystąpienie **CloudMediaContext** :

```csharp
CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
```

Poniższy przykład pokazuje, jak utworzyć token usługi Azure AD i kontekst:

```csharp
namespace AzureADAuthSample
{
    class Program
    {
        static void Main(string[] args)
        {
            // Specify your Azure AD tenant domain, for example "microsoft.onmicrosoft.com".
            var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            // Specify your REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".
            CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);

            var assets = context.Assets;
            foreach (var a in assets)
            {
                Console.WriteLine(a.Name);
            }
        }
    }
}
```

>[!NOTE]
>Jeśli zostanie wyświetlony wyjątek "serwer zdalny zwrócił błąd: (401) bez autoryzacji," Zobacz sekcję [Kontrola dostępu](media-services-use-aad-auth-to-access-ams-api.md#access-control) , aby uzyskać dostęp do interfejsu API Azure Media Services przy użyciu uwierzytelniania usługi Azure AD.

## <a name="use-service-principal-authentication"></a>Użyj uwierzytelniania nazwy głównej usługi

Aby nawiązać połączenie z interfejsem API Azure Media Services przy użyciu opcji głównej usługi, aplikacja warstwy środkowej (Web API lub aplikacja sieci Web) musi zażądać tokenu usługi Azure AD z następującymi parametrami:

- Punkt końcowy dzierżawy usługi Azure AD. Informacje o dzierżawie można pobrać z Azure Portal. Umieść kursor nad zalogowanym użytkownikiem w prawym górnym rogu.
- Media Services identyfikator URI zasobu.
- Wartości aplikacji usługi Azure AD: **Identyfikator klienta** i **klucz tajny klienta**.

Wartości parametrów **Identyfikator klienta** i **klucz tajny klienta** można znaleźć w Azure Portal. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z uwierzytelnianiem w usłudze Azure AD przy użyciu Azure Portal](media-services-portal-get-started-with-aad.md).

Poniższy przykład kodu tworzy token przy użyciu konstruktora **AzureAdTokenCredentials** , który pobiera **AzureAdClientSymmetricKey** jako parametr:

```csharp
var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}",
                        new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"),
                        AzureEnvironments.AzureCloudEnvironment);

var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
```

Można również określić konstruktora **AzureAdTokenCredentials** , który pobiera **AzureAdClientCertificate** jako parametr.

Aby uzyskać instrukcje dotyczące sposobu tworzenia i konfigurowania certyfikatu w formularzu, który może być używany przez usługę Azure AD, zobacz temat uwierzytelnianie w usłudze [Azure AD w aplikacjach demonów z certyfikatami — kroki konfiguracji ręcznej](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2).

```csharp
var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}",
                        new AzureAdClientCertificate("{YOUR CLIENT ID HERE}", "{YOUR CLIENT CERTIFICATE THUMBPRINT}"),
                        AzureEnvironments.AzureCloudEnvironment);
```

Aby rozpocząć programowanie względem Media Services, należy utworzyć wystąpienie **CloudMediaContext** reprezentujące kontekst serwera. Należy również przekazać **Identyfikator URI zasobu dla usług REST usługi Media** do konstruktora **CloudMediaContext** . **Identyfikator URI zasobu dla usługi Media REST Services** można również uzyskać z Azure Portal.

Poniższy przykład kodu tworzy wystąpienie **CloudMediaContext** :

```csharp
CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
```

Poniższy przykład pokazuje, jak utworzyć token usługi Azure AD i kontekst:

```csharp
namespace AzureADAuthSample
{
    class Program
    {
        static void Main(string[] args)
        {
            var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}",
                                        new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"),
                                        AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            // Specify your REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".
            CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);

            var assets = context.Assets;
            foreach (var a in assets)
            {
                Console.WriteLine(a.Name);
            }

            Console.ReadLine();
        }
    }
}
```

## <a name="next-steps"></a>Następne kroki

Rozpocznij pracę z [przekazywaniem plików na konto](media-services-dotnet-upload-files.md).
