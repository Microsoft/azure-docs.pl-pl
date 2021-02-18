---
title: Tworzenie punktu końcowego Standard scim na potrzeby aprowizacji użytkowników w aplikacjach z Azure Active Directory
description: Dowiedz się, jak opracowywać punkt końcowy Standard scim, zintegrować interfejs API Standard scim z usługą Azure AD, a następnie automatycznie zainicjować obsługę użytkowników i grup w aplikacjach w chmurze przy użyciu Azure Active Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 9fac8db6747d1e081fa884ed794e55b98f44577f
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652041"
---
# <a name="tutorial-develop-a-sample-scim-endpoint"></a>Samouczek: opracowywanie przykładowego punktu końcowego Standard scim

Nikt nie chce kompilować nowego punktu końcowego od podstaw, dlatego utworzyliśmy [kod referencyjny](https://aka.ms/scimreferencecode) , aby rozpocząć pracę z [systemem na potrzeby zarządzania tożsamościami między domenami (standard scim)](https://aka.ms/scimoverview). Możesz uzyskać punkt końcowy Standard scim i pracować bez kodu w zaledwie pięć minut.

W tym samouczku opisano sposób wdrażania kodu odwołania Standard scim na platformie Azure i testowania go przy użyciu programu Poster lub przez integrację z klientem Standard scim usługi Azure Active Directory (Azure AD). Ten samouczek jest przeznaczony dla deweloperów, którzy chcą zacząć korzystać z usługi Standard scim, lub osoby zainteresowane testowaniem punktu końcowego Standard scim.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
>
> * Wdróż punkt końcowy Standard scim na platformie Azure.
> * Przetestuj punkt końcowy Standard scim.

## <a name="deploy-your-scim-endpoint-in-azure"></a>Wdrażanie punktu końcowego Standard scim na platformie Azure

Kroki opisane w tym miejscu umożliwiają wdrożenie punktu końcowego Standard scim w usłudze przy użyciu [programu Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) i [Azure App Service](https://docs.microsoft.com/azure/app-service/). Kod odwołania Standard scim może być również uruchamiany lokalnie, hostowany przez serwer lokalny lub wdrożony w innej usłudze zewnętrznej.

1. Przejdź do [kodu referencyjnego](https://github.com/AzureAD/SCIMReferenceCode) z usługi GitHub i wybierz opcję **Klonuj lub Pobierz**.

1. Wybierz pozycję **Otwórz w programie Desktop** lub skopiuj link, Otwórz program Visual Studio, a następnie wybierz pozycję **Klonuj lub Wyewidencjonuj kod** , aby wprowadzić skopiowane łącze i utworzyć kopię lokalną.

1. W programie Visual Studio upewnij się, że logujesz się do konta, które ma dostęp do zasobów hostingowych.

1. W Eksplorator rozwiązań otwórz plik *Microsoft. Standard scim. sln* i kliknij prawym przyciskiem myszy pliku *Microsoft. Standard scim. WebHostSample* . Kliknij pozycję **Opublikuj**.

    ![Zrzut ekranu pokazujący przykładowy plik.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish.png)

    > [!NOTE]
    > Aby uruchomić to rozwiązanie lokalnie, kliknij dwukrotnie projekt i wybierz **IIS Express** , aby uruchomić projekt jako stronę sieci Web z adresem URL hosta lokalnego.

1. Wybierz pozycję **Utwórz profil** i upewnij się, że wybrano opcję **App Service** i **Utwórz nowy** .

    ![Zrzut ekranu przedstawiający okno publikowanie.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-2.png)

1. Przejdź do opcji okna dialogowego i Zmień nazwę aplikacji na wybraną nazwę. Ta nazwa jest używana zarówno w aplikacji, jak i w adresie URL punktu końcowego Standard scim.

    ![Zrzut ekranu przedstawiający tworzenie nowej usługi App Service.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-3.png)

1. Wybierz grupę zasobów, która ma zostać użyta, a następnie wybierz pozycję **Publikuj**.

1. Przejdź do aplikacji w obszarze   >  **Konfiguracja** Azure App Service a następnie wybierz pozycję **nowe ustawienie aplikacji** , aby dodać ustawienie *Token__TokenIssuer* z wartością `https://sts.windows.net/<tenant_id>/` . Zastąp ciąg `<tenant_id>` identyfikatorem dzierżawy usługi Azure AD. Jeśli chcesz przetestować punkt końcowy Standard scim za pomocą programu [Poster](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint), dodaj ustawienie *ASPNETCORE_ENVIRONMENT* z wartością `Development` .

   ![Zrzut ekranu przedstawiający okno Ustawienia aplikacji.](media/use-scim-to-build-users-and-groups-endpoints/app-service-settings.png)

   Po przetestowaniu punktu końcowego z aplikacją przedsiębiorstwa w [Azure Portal](use-scim-to-provision-users-and-groups.md#integrate-your-scim-endpoint-with-the-aad-scim-client)dostępne są dwie opcje. Środowisko można zachować `Development` i udostępnić token testowy z `/scim/token` punktu końcowego lub można zmienić środowisko na `Production` i pozostawić puste pole tokenu.

Gotowe. Punkt końcowy Standard scim jest teraz publikowany i możesz użyć adresu URL Azure App Service do przetestowania punktu końcowego Standard scim.

## <a name="test-your-scim-endpoint"></a>Testowanie punktu końcowego Standard scim

Żądania do punktu końcowego Standard scim wymagają autoryzacji. Standard Standard scim ma wiele opcji uwierzytelniania i autoryzacji, w tym plików cookie, uwierzytelniania podstawowego, uwierzytelniania klientów protokołu TLS lub dowolnej metody wymienionej w [dokumencie RFC 7644](https://tools.ietf.org/html/rfc7644#section-2).

Pamiętaj, aby uniknąć metod, które nie są bezpieczne, takich jak nazwa użytkownika i hasło, na rzecz bezpieczniejszej metody, takiej jak OAuth. Usługa Azure AD obsługuje tokeny okaziciela o długim okresie ważności (w przypadku aplikacji z galerii i innych galerii) oraz przyznawanie autoryzacji OAuth (dla aplikacji galerii).

> [!NOTE]
> Metody autoryzacji dostępne w repozytorium służą wyłącznie do celów testowych. W przypadku integracji z usługą Azure AD można przejrzeć wskazówki dotyczące autoryzacji. Zobacz [Planowanie aprowizacji dla punktu końcowego Standard scim](use-scim-to-provision-users-and-groups.md).

Środowisko programistyczne umożliwia korzystanie z funkcji, które są niebezpieczne dla środowiska produkcyjnego, takich jak kod referencyjny do sterowania zachowaniem walidacji tokenu zabezpieczającego. Kod sprawdzania poprawności tokenu używa tokenu zabezpieczającego z podpisem własnym, a klucz podpisywania jest przechowywany w pliku konfiguracji. Zobacz parametr **token: IssuerSigningKey** w pliku *appsettings.Development.json* .

```json
"Token": {
    "TokenAudience": "Microsoft.Security.Bearer",
    "TokenIssuer": "Microsoft.Security.Bearer",
    "IssuerSigningKey": "A1B2C3D4E5F6A1B2C3D4E5F6",
    "TokenLifetimeInMins": "120"
}
```

> [!NOTE]
> Po wysłaniu żądania **Get** do `/scim/token` punktu końcowego token jest wystawiany przy użyciu skonfigurowanego klucza. Ten token może służyć jako token okaziciela do dalszej autoryzacji.

Domyślny kod sprawdzania poprawności tokenu jest skonfigurowany do korzystania z tokenu usługi Azure AD i wymaga skonfigurowania dzierżawy wystawiającej za pomocą parametru **token: TokenIssuer** w *appsettings.js* pliku.

``` json
"Token": {
    "TokenAudience": "8adf8e6e-67b2-4cf2-a259-e3dc5476c621",
    "TokenIssuer": "https://sts.windows.net/<tenant_id>/"
}
```

### <a name="use-postman-to-test-endpoints"></a>Użyj programu Poster do testowania punktów końcowych

Po wdrożeniu punktu końcowego Standard scim można przeprowadzić test, aby upewnić się, że jest zgodny z standard scim RFC. Ten przykład zawiera zestaw testów w programie Poster, które weryfikują operacje CRUD (tworzenie, odczytywanie, aktualizowanie i usuwanie) dla użytkowników i grup, filtrowanie, aktualizacje członkostwa w grupie i wyłączanie użytkowników.

Punkty końcowe znajdują się w `{host}/scim/` katalogu i można z nich korzystać w standardowych żądaniach HTTP. Aby zmodyfikować `/scim/` trasę, zobacz *ControllerConstant.cs* in **AzureADProvisioningSCIMreference**  >  **ScimReferenceApi**  >  **controllers**.

> [!NOTE]
> Punktów końcowych protokołu HTTP można używać tylko dla testów lokalnych. Usługa Azure AD Provisioning wymaga, aby punkt końcowy obsługiwał protokół HTTPS.

1. Pobierz aplikację [pocztową](https://www.getpostman.com/downloads/) i uruchom ją.
1. Skopiuj i wklej ten link do programu Poster, aby zaimportować kolekcję testową: `https://aka.ms/ProvisioningPostman` .

    ![Zrzut ekranu pokazujący Importowanie kolekcji testowej w programie Poster.](media/use-scim-to-build-users-and-groups-endpoints/postman-collection.png)

1. Utwórz środowisko testowe, które ma następujące zmienne:

   |Środowisko|Zmienna|Wartość|
   |-|-|-|
   |Uruchamianie projektu lokalnie przy użyciu IIS Express|||
   ||**Server** (Serwer)|`localhost`|
   ||**Port**|`:44359`*(nie zapomnij **`:`** )*|
   ||**Interfejsu API**|`scim`|
   |Uruchamianie projektu lokalnie przy użyciu Kestrel|||
   ||**Server** (Serwer)|`localhost`|
   ||**Port**|`:5001`*(nie zapomnij **`:`** )*|
   ||**Interfejsu API**|`scim`|
   |Hostowanie punktu końcowego na platformie Azure|||
   ||**Server** (Serwer)|*(wprowadź adres URL Standard scim)*|
   ||**Port**|*(pozostaw puste)*|
   ||**Interfejsu API**|`scim`|

1. Użyj **Get Key** z kolekcji Poster, aby wysłać żądanie **Get** do punktu końcowego tokenu i pobrać token zabezpieczający, który ma być przechowywany w zmiennej **tokenu** dla kolejnych żądań.

   ![Zrzut ekranu przedstawiający folder Pobieranie klucza programu post.](media/use-scim-to-build-users-and-groups-endpoints/postman-get-key.png)

   > [!NOTE]
   > Aby zapewnić bezpieczeństwo punktu końcowego Standard scim, musisz mieć token zabezpieczający przed połączeniem. Ten samouczek używa `{host}/scim/token` punktu końcowego do wygenerowania tokenu z podpisem własnym.

Gotowe. Teraz można uruchomić kolekcję programu **Poster** w celu przetestowania funkcji punktu końcowego Standard scim.

## <a name="next-steps"></a>Następne kroki

Aby opracować punkt końcowy użytkownika i grupy zgodnej z standard SCIMą z współdziałaniem klienta, zobacz [Standard scim implementation Client](http://www.simplecloud.info/#Implementations2).

> [!div class="nextstepaction"]
> [Samouczek: opracowywanie i planowanie aprowizacji dla punktu końcowego Standard scim](use-scim-to-provision-users-and-groups.md) 
>  [Samouczek: Konfigurowanie aprowizacji dla aplikacji galerii](configure-automatic-user-provisioning-portal.md)
