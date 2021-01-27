---
title: Tworzenie punktu końcowego Standard scim na potrzeby aprowizacji użytkowników w aplikacjach z Azure Active Directory
description: System do zarządzania tożsamościami między domenami (standard scim) zapewnia standaryzację automatycznej aprowizacji użytkowników. Dowiedz się, jak opracowywać punkt końcowy Standard scim, zintegrować interfejs API Standard scim z usługą Azure Active Directory i rozpocząć automatyzację użytkowników i grup aprowizacji w aplikacjach w chmurze przy użyciu Azure Active Directory.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 34fa76197c4e08cffd1d8c66d6877b3e427e9fd6
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98918148"
---
# <a name="tutorial-develop-a-sample-scim-endpoint"></a>Samouczek: opracowywanie przykładowego punktu końcowego Standard scim

Nikt nie chce kompilować nowego punktu końcowego od podstaw, dlatego utworzyliśmy [kod referencyjny](https://aka.ms/scimreferencecode) , aby rozpocząć pracę z usługą [Standard scim](https://aka.ms/scimoverview). W tym samouczku opisano, jak wdrożyć kod referencyjny Standard scim na platformie Azure i przetestować go przy użyciu programu Poster lub przez integrację z klientem usługi Azure AD Standard scim. Punkt końcowy Standard scim można uzyskać bez kodu w zaledwie 5 minut. Ten samouczek jest przeznaczony dla deweloperów, którzy chcą rozpocząć pracę z standard SCIMą lub innymi osobami zainteresowanymi testowaniem punktu końcowego SICM. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wdrażanie punktu końcowego Standard scim na platformie Azure
> * Testowanie punktu końcowego Standard scim

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

Środowisko programistyczne umożliwia korzystanie z funkcji niebezpiecznych dla środowiska produkcyjnego, takich jak kod referencyjny, aby kontrolować zachowanie weryfikacji tokenu zabezpieczającego. Kod sprawdzania poprawności tokenu jest skonfigurowany do korzystania z tokenu zabezpieczającego z podpisem własnym, a klucz podpisywania jest przechowywany w pliku konfiguracji, zobacz **token: IssuerSigningKey** w pliku *appsettings.Development.js* .

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

Aby opracować punkt końcowy Standard scim użytkownika i grupy z współdziałaniem dla klienta, zapoznaj się z [implementacją klienta standard scim](http://www.simplecloud.info/#Implementations2).

> [!div class="nextstepaction"]
> [Samouczek: opracowywanie i planowanie aprowizacji dla punktu końcowego Standard scim](use-scim-to-provision-users-and-groups.md) 
>  [Samouczek: Konfigurowanie aprowizacji dla aplikacji galerii](configure-automatic-user-provisioning-portal.md)