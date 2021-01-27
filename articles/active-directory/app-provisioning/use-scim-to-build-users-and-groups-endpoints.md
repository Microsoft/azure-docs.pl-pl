---
title: Tworzenie punktu końcowego Standard scim na potrzeby aprowizacji użytkowników w aplikacjach z usługi Azure AD
description: System do zarządzania tożsamościami między domenami (standard scim) zapewnia standaryzację automatycznej aprowizacji użytkowników. Dowiedz się, jak opracowywać punkt końcowy Standard scim, zintegrować interfejs API Standard scim z usługą Azure Active Directory i rozpocząć automatyzację użytkowników i grup aprowizacji w aplikacjach w chmurze.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/07/2020
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ae36af981b113d44ac1b8fd45a1d084760b0294
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900244"
---
# <a name="tutorial-develop-a-sample-scim-endpoint"></a>Samouczek: opracowywanie przykładowego punktu końcowego Standard scim

Nikt nie chce kompilować nowego punktu końcowego od podstaw, dlatego utworzyliśmy [kod referencyjny](https://aka.ms/scimreferencecode) , aby rozpocząć pracę z usługą [Standard scim](https://aka.ms/scimoverview). W tym samouczku opisano, jak wdrożyć kod referencyjny Standard scim na platformie Azure i przetestować go przy użyciu programu Poster lub przez integrację z klientem usługi Azure AD Standard scim. Punkt końcowy Standard scim można uzyskać bez kodu w zaledwie 5 minut. Ten samouczek jest przeznaczony dla deweloperów, którzy chcą rozpocząć pracę z standard SCIMą lub innymi osobami zainteresowanymi testowaniem punktu końcowego SICM. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Pobierz kod referencyjny
> * Wdrażanie punktu końcowego Standard scim na platformie Azure
> * Testowanie punktu końcowego Standard scim

Dostępne są następujące możliwości punktu końcowego:

|Punkt końcowy|Opis|
|---|---|
|`/User`|Wykonywanie operacji CRUD na zasobie użytkownika: **Create**, **Update**, **delete**, **Get**, **list**, **Filter**|
|`/Group`|Wykonywanie operacji CRUD na zasobach grupy: **Create**, **Update**, **delete**, **Get**, **list**, **Filter**|
|`/Schemas`|Pobierz co najmniej jeden obsługiwany schemat.<br/><br/>Zestaw atrybutów zasobu obsługiwane przez poszczególnych dostawców usług może się różnić, np. dostawca usług A obsługuje "name", "title" i "emails", podczas gdy dostawca usług B obsługuje "name", "title" i "ServiceName" dla użytkowników.|
|`/ResourceTypes`|Pobierz obsługiwane typy zasobów.<br/><br/>Liczba i typy zasobów obsługiwanych przez poszczególnych dostawców usług mogą się różnić, np. dostawca usług A obsługuje użytkowników, podczas gdy dostawca usług B obsługuje użytkowników i grupy.|
|`/ServiceProviderConfig`|Pobierz konfigurację Standard scim dostawcy usług<br/><br/>Funkcje Standard scim obsługiwane przez poszczególnych dostawców usług mogą się różnić, np. dostawca usług A obsługuje operacje na poprawkach, podczas gdy dostawca usług B obsługuje operacje poprawek i odnajdywania schematów.|

## <a name="download-the-reference-code"></a>Pobierz kod referencyjny

[Kod referencyjny](https://github.com/AzureAD/SCIMReferenceCode) do pobrania obejmuje następujące projekty:

- **Microsoft.SystemForCrossDomainIdentityManagement**, interfejs API sieci Web platformy .NET Core do kompilowania i inicjowania obsługi interfejsu API Standard scim
- **Microsoft. Standard scim. WebHostSample**, działający przykład punktu końcowego Standard scim

Projekty zawierają następujące foldery i pliki:

|Plik/folder|Opis|
|-|-|
|Folder **schematów**| Modele zasobów **użytkowników** i **grup** wraz z niektórymi klasami abstrakcyjnymi, takimi jak schematized.<br/><br/> Folder **atrybutów** , który zawiera definicje klas dla złożonych atrybutów **użytkowników** i **grup** , takich jak adresy.|
|Folder **usługi** | Zawiera logikę dla akcji odnoszących się do sposobu, w jaki zasoby są badane i aktualizowane.<br/><br/> Kod odwołania zawiera usługi, które zwracają użytkowników i grupy.<br/><br/>Folder **controllers** zawiera różne punkty końcowe Standard scim. Kontrolery zasobów obejmują zlecenia HTTP do wykonywania operacji CRUD na zasobach (**Get**, **post**, **Put**, **patch** i **delete**). Kontrolery wykorzystują usługi do wykonywania akcji.|
|Folder **protokołu**|Zawiera logikę dla akcji odnoszących się do sposobu, w jaki zasoby są zwracane zgodnie z standard scim RFC, takie jak:<br/><ul><li>Zwracanie wielu zasobów jako listy.</li><li>Zwracanie tylko określonych zasobów na podstawie filtru.</li><li>Przekształcanie zapytania w listę połączonych list pojedynczych filtrów.</li><li>Przekształcanie żądania PATCH w operację z atrybutami odnoszącymi się do ścieżki wartości.</li><li>Definiowanie typu operacji, która może być używana do stosowania zmian w obiektach zasobów.</li></ul>|
|`Microsoft.SystemForCrossDomainIdentityManagement`| Przykładowy kod źródłowy.|
|`Microsoft.SCIM.WebHostSample`| Przykładowa implementacja biblioteki Standard scim.|
|*. gitignore*|Zdefiniuj, co ma być ignorowane w czasie zatwierdzania.|
|*CHANGELOG.md*|Lista zmian w próbce.|
|*CONTRIBUTING.md*|Wskazówki dotyczące współtworzenia przykładu.|
|*README.md*|Ten plik **README** .|
|*LICENCJONOWAN*|Licencja dla przykładu.|

> [!NOTE]
> Ten kod ma pomóc w rozpoczęciu tworzenia punktu końcowego Standard scim i jest dostępny **jako**. Dołączone odwołania nie zapewniają żadnej gwarancji dotyczącej aktywnego maintainence lub pomocy technicznej.
>
> W tym projekcie przyjęto [Kodeks postępowania oprogramowania Open Source firmy Microsoft](https://opensource.microsoft.com/codeofconduct/). Ponieważ takie [wkłady](https://github.com/AzureAD/SCIMReferenceCode/wiki/Contributing-Overview) ze społeczności są zachęcane do kompilowania i konserwowania repozytorium, jak w przypadku innych wkładów typu "open source", akceptujesz umowę licencyjną współautora (CLA). Niniejsza Umowa oświadcza, że masz i przyznasz prawa do korzystania z Twojego wkładu, aby uzyskać szczegółowe informacje, zobacz [Microsoft Open Source](https://cla.opensource.microsoft.com).
>
> Aby uzyskać więcej informacji, zobacz artykuł [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) (Często zadawane pytania dotyczące kodeksu postępowania). Jeśli będziesz mieć jeszcze jakieś pytania lub komentarze, wyślij wiadomość e-mail na adres [opencode@microsoft.com](mailto:opencode@microsoft.com).

###  <a name="use-multiple-environments"></a>Używanie wielu środowisk

Dołączony kod Standard scim używa środowiska ASP.NET Core, aby kontrolować jego autoryzację do użycia podczas tworzenia i po wdrożeniu, zobacz [Używanie wielu środowisk w ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/environments?view=aspnetcore-3.1).

```csharp
private readonly IWebHostEnvironment _env;
...

public void ConfigureServices(IServiceCollection services)
{
    if (_env.IsDevelopment())
    {
        ...
    }
    else
    {
        ...
    }
```

## <a name="deploy-your-scim-endpoint-in-azure"></a>Wdrażanie punktu końcowego Standard scim na platformie Azure

Kroki podane tutaj wdrażają punkt końcowy Standard scim w usłudze przy użyciu [programu Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) i [platformy Azure App Services](https://docs.microsoft.com/azure/app-service/). Kod odwołania Standard scim może być również uruchamiany lokalnie, hostowany przez serwer lokalny lub wdrożony w innej usłudze zewnętrznej. 

### <a name="open-solution-and-deploy-to-azure-app-service"></a>Otwórz rozwiązanie i wdróż je w Azure App Service

1. Przejdź do [kodu referencyjnego](https://github.com/AzureAD/SCIMReferenceCode) z usługi GitHub i wybierz opcję **Klonuj lub Pobierz**.

1. Wybierz pozycję **Otwórz w programie Desktop** lub skopiuj link, Otwórz **program Visual Studio** i wybierz polecenie **Klonuj lub** Wyewidencjonuj kod, aby wprowadzić skopiowane łącze i utworzyć kopię lokalną.

1. W programie **Visual Studio** Pamiętaj, aby zalogować się do konta, które ma dostęp do zasobów hostingowych.

1. W **Eksplorator rozwiązań** Otwórz plik *Microsoft. Standard scim. sln* i kliknij prawym przyciskiem myszy pliku *Microsoft. Standard scim. WebHostSample* . Kliknij pozycję **Opublikuj**.

    ![Publikowanie w chmurze](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish.png)

    > [!NOTE]
    > Aby uruchomić to rozwiązanie lokalnie, kliknij dwukrotnie projekt i wybierz **IIS Express** , aby uruchomić projekt jako stronę sieci Web z adresem URL hosta lokalnego.

1. Wybierz pozycję **Utwórz profil** i upewnij się, że jest wybrana opcja **App Service** i **Utwórz nowe** .

    ![Publikowanie w chmurze 2](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-2.png)

1. Przejdź do opcji okna dialogowego i Zmień nazwę aplikacji na wybraną nazwę. Ta nazwa jest używana zarówno w aplikacji, jak i w adresie URL punktu końcowego Standard scim.

    ![Publikowanie w chmurze 3](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-3.png)

1. Wybierz grupę zasobów, która ma zostać użyta, a następnie wybierz pozycję **Publikuj**.

1. Przejdź do aplikacji w obszarze **Konfiguracja usługi Azure App Services**  >   i wybierz pozycję **nowe ustawienie aplikacji** , aby dodać ustawienie *Token__TokenIssuer* z wartością `https://sts.windows.net/<tenant_id>/` . Zamień `<tenant_id>` na Tenant_id usługi Azure AD i jeśli chcesz przetestować punkt końcowy Standard scim przy użyciu programu [Poster](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint), dodaj również ustawienie *ASPNETCORE_ENVIRONMENT* z wartością `Development` . 

   ![Ustawienia appService](media/use-scim-to-build-users-and-groups-endpoints/app-service-settings.png)

   Podczas testowania punktu końcowego za pomocą aplikacji przedsiębiorstwa w Azure Portal wybierz, aby zachować środowisko jako `Development` i udostępnić token wygenerowany z `/scim/token` punktu końcowego w celu przetestowania lub zmiany środowiska na `Production` i pozostawić pole token puste w aplikacji przedsiębiorstwa w [Azure Portal](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client). 

Gotowe. Punkt końcowy Standard scim jest teraz opublikowany i umożliwia użycie adresu URL Azure App Service do testowania punktu końcowego Standard scim.

## <a name="test-your-scim-endpoint"></a>Testowanie punktu końcowego Standard scim

Żądania do punktu końcowego Standard scim wymagają autoryzacji, a standard Standard SCIMu pozostawia wiele opcji uwierzytelniania i autoryzacji, takich jak pliki cookie, uwierzytelnianie podstawowe, uwierzytelnianie klienta protokołu TLS lub dowolne metody wymienione w [dokumencie RFC 7644](https://tools.ietf.org/html/rfc7644#section-2).

Pamiętaj, aby uniknąć niezabezpieczonych metod, takich jak nazwa użytkownika i hasło, na rzecz bezpieczniejszej metody, takiej jak OAuth. Usługa Azure AD obsługuje tokeny okaziciela o długim czasie trwania (w przypadku aplikacji z galerii i innych galerii) oraz przyznawanie autoryzacji OAuth (w przypadku aplikacji opublikowanych w galerii aplikacji).

> [!NOTE]
> Metody autoryzacji dostępne w repozytorium służą wyłącznie do celów testowych. W przypadku integracji z usługą Azure AD można przejrzeć wskazówki dotyczące autoryzacji, zobacz [Planowanie aprowizacji dla punktu końcowego Standard scim](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#authorization-for-provisioning-connectors-in-the-application-gallery). 

Środowisko programistyczne umożliwia korzystanie z funkcji niebezpiecznych dla środowiska produkcyjnego, takich jak kod referencyjny, aby kontrolować zachowanie weryfikacji tokenu zabezpieczającego. Kod sprawdzania poprawności tokenu jest skonfigurowany tak, aby używał tokenu zabezpieczeń z podpisem własnym, a klucz podpisywania jest przechowywany w pliku konfiguracji, zobacz **token: IssuerSigningKey** w pliku *appsettings.Development.js* .

```json
"Token": {
    "TokenAudience": "Microsoft.Security.Bearer",
    "TokenIssuer": "Microsoft.Security.Bearer",
    "IssuerSigningKey": "A1B2C3D4E5F6A1B2C3D4E5F6",
    "TokenLifetimeInMins": "120"
}
```

> [!NOTE]
> Po wysłaniu żądania **Get** do `/scim/token` punktu końcowego token jest wystawiany przy użyciu skonfigurowanego klucza i może być używany jako token okaziciela do dalszej autoryzacji.

Domyślny kod sprawdzania poprawności tokenu jest skonfigurowany do używania tokenu wystawionego przez Azure Active Directory i wymaga skonfigurowania dzierżawy wystawiającej przy użyciu parametru **token: TokenIssuer** w *appsettings.js* pliku.

``` json
"Token": {
    "TokenAudience": "8adf8e6e-67b2-4cf2-a259-e3dc5476c621",
    "TokenIssuer": "https://sts.windows.net/<tenant_id>/"
}
```

### <a name="use-postman-to-test-endpoints"></a>Użyj programu Poster do testowania punktów końcowych

Po wdrożeniu punktu końcowego Standard scim można testować, aby upewnić się, że jest standard scim zgodne ze standardem RFC. Ten przykład zawiera zestaw testów w programie **Poster** do sprawdzania poprawności operacji CRUD dla użytkowników i grup, filtrowania, aktualizacji członkostwa w grupach i wyłączania użytkowników.

Punkty końcowe znajdują się w `{host}/scim/` katalogu i można z nimi korzystać w standardowych żądaniach HTTP. Aby zmodyfikować `/scim/` trasę, zobacz *ControllerConstant.cs* in **AzureADProvisioningSCIMreference**  >  **ScimReferenceApi**  >  **controllers**.

> [!NOTE]
> Punktów końcowych protokołu HTTP można używać tylko dla testów lokalnych, ponieważ usługa Azure AD Provisioning wymaga obsługi protokołu HTTPS przez punkt końcowy.

#### <a name="open-postman-and-run-tests"></a>Otwieranie programu Poster i uruchamianie testów

1. Pobierz aplikację do [publikowania](https://www.getpostman.com/downloads/) i uruchamiania.
1. Skopiuj link i wklej go do programu [https://aka.ms/ProvisioningPostman](https://aka.ms/ProvisioningPostman) Poster, aby zaimportować kolekcję testową.

    ![Kolekcja ogłoszeń](media/use-scim-to-build-users-and-groups-endpoints/postman-collection.png)

1. Utwórz środowisko testowe z poniższymi zmiennymi:

   |Środowisko|Zmienna|Wartość|
   |-|-|-|
   |Uruchamianie projektu lokalnie przy użyciu IIS Express|||
   ||**Server** (Serwer)|`localhost`|
   ||**Port**|`:44359`*(nie zapomnij **:**)*|
   ||**Interfejsu API**|`scim`|
   |Uruchamianie projektu lokalnie przy użyciu Kestrel|||
   ||**Server** (Serwer)|`localhost`|
   ||**Port**|`:5001`*(nie zapomnij **:**)*|
   ||**Interfejsu API**|`scim`|
   |Hostowanie punktu końcowego na platformie Azure|||
   ||**Server** (Serwer)|*(wprowadź adres URL Standard scim)*|
   ||**Port**|*(pozostaw puste)*|
   ||**Interfejsu API**|`scim`|

1. Użyj **Get Key** z kolekcji Poster, aby wysłać żądanie **Get** do punktu końcowego tokenu i pobrać token zabezpieczający, który ma być przechowywany w zmiennej **tokenu** dla kolejnych żądań. 

   ![Pobierz klucz](media/use-scim-to-build-users-and-groups-endpoints/postman-get-key.png)

   > [!NOTE]
   > Aby zapewnić bezpieczeństwo punktów końcowych Standard scim, musisz mieć token zabezpieczający przed połączeniem, a samouczek używa `{host}/scim/token` punktu końcowego do wygenerowania tokenu z podpisem własnym.

Gotowe. Teraz można uruchomić kolekcję programu **Poster** w celu przetestowania funkcji punktu końcowego Standard scim.

## <a name="next-steps"></a>Następne kroki

Aby opracować Standard scim zgodny z użytkownikiem i grupowym punktem końcowym z współdziałaniem dla klienta, zobacz [implementację klienta standard scim](http://www.simplecloud.info/#Implementations2).

> [!div class="nextstepaction"]
> [Samouczek: opracowywanie i planowanie aprowizacji dla punktu końcowego Standard scim](use-scim-to-provision-users-and-groups.md) 
>  [Samouczek: Konfigurowanie aprowizacji dla aplikacji galerii](configure-automatic-user-provisioning-portal.md)