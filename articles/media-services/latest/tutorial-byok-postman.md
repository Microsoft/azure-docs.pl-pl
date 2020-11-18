---
title: Korzystanie z kluczy zarządzanych przez klienta lub interfejsu API REST usługi BYOK
description: W tym samouczku Użyj kluczy zarządzanych przez klienta lub Przenieś własny klucz (BYOK) przy użyciu konta magazynu Azure Media Services.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: 93bbd84c25f02ac1653a46ebb4a70c1dfa90c744
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94687244"
---
# <a name="tutorial-use-customer-managed-keys-or-byok-with-media-services-rest-api"></a>Samouczek: Używanie kluczy zarządzanych przez klienta lub BYOK za pomocą interfejsu API REST Media Services

Za pomocą interfejsu API 2020-05-01 można użyć klucza RSA zarządzanego przez klienta przy użyciu konta Azure Media Services, które ma tożsamość zarządzaną przez system. Ten samouczek obejmuje kolekcję i środowisko programu post do wysyłania żądań REST do usług platformy Azure. Używane usługi to:

- Rejestracja aplikacji Azure Active Directory (Azure AD) dla programu Poster
- Microsoft Graph API
- Azure Storage
- W usłudze Azure Key Vault
- Azure Media Services

W ramach tego samouczka nauczysz się używać programu Poster do:

> [!div class="checklist"]
> - Uzyskaj tokeny do użycia z usługami platformy Azure.
> - Utwórz grupę zasobów i konto magazynu.
> - Utwórz konto Media Services z tożsamością zarządzaną przez system.
> - Utwórz magazyn kluczy do przechowywania klucza RSA zarządzanego przez klienta.
> - Zaktualizuj konto Media Services, aby użyć klucza RSA z kontem magazynu.
> - Używanie zmiennych w programie Poster.

Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

1. Zarejestruj jednostkę usługi z odpowiednimi uprawnieniami.
1. Zainstaluj program [Poster](https://www.postman.com).
1. Pobierz kolekcję programu Poster dla tego samouczka na [platformie Azure przykłady: Media-Services-Customer-Keys-BYOK](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok).

### <a name="register-a-service-principal-with-the-needed-permissions"></a>Rejestrowanie jednostki usługi z wymaganymi uprawnieniami

1. [Utwórz nazwę główną usługi](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).
1. Przejdź do [opcji 2: Utwórz nowy klucz tajny aplikacji](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#authentication-two-options) , aby uzyskać klucz tajny jednostki usługi.

   > [!IMPORTANT]
   >Skopiuj i Zapisz klucz tajny do późniejszego użycia. Nie możesz uzyskać dostępu do wpisu tajnego po opuszczeniu strony klucza tajnego w portalu.

1. Przypisz uprawnienia do nazwy głównej usługi, jak pokazano na poniższym zrzucie ekranu:

   :::image type="complex" source="./media/tutorial-byok/service-principal-permissions-1.png" alt-text="Zrzut ekranu przedstawiający uprawnienia wymagające nazwy głównej usługi.":::
   Uprawnienia są wyświetlane według usługi, nazwy uprawnienia, typu, a następnie opis. Azure Key Vault: personifikacja użytkownika, delegowany, pełny dostęp do Azure Key Vault. Zarządzanie usługami platformy Azure: personifikacja użytkownika, delegowane, dostęp do usługi Azure Service Management jako użytkownik organizacji. Azure Storage: personifikacja użytkownika, delegowane, dostęp do usługi Azure Storage. Media Services: personifikacja użytkownika, delegowane, dostęp do usługi Media Services. Microsoft Graph: User. Read, delegowany, loguj się i Odczytuj profil użytkownika.
   :::image-end:::

### <a name="install-postman"></a>Zainstaluj program Poster

Jeśli program nie został jeszcze zainstalowany na platformie Azure, możesz go uzyskać pod adresem [Postman.com](https://www.postman.com/).

### <a name="download-and-import-the-collection"></a>Pobieranie i importowanie kolekcji

Pobierz kolekcję programu Poster dla tego samouczka na [platformie Azure przykłady: Media-Services-Customer-Keys-BYOK](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok).

## <a name="install-the-postman-collection-and-environment"></a>Instalowanie kolekcji i środowiska programu Poster

1. Uruchom notkę.
1. Wybierz pozycję **Importuj**.
1. Wybierz pozycję **Przekaż pliki**.
1. Przejdź do lokalizacji, w której zapisano pliki kolekcji i środowiska.
1. Wybierz pliki kolekcji i środowiska.
1. Wybierz pozycję **Otwórz**. Zostanie wyświetlone ostrzeżenie z informacją, że pliki nie zostaną zaimportowane jako interfejs API, ale jako kolekcje. To ostrzeżenie jest dokładne. To wszystko, czego potrzebujesz.

Kolekcja jest teraz wyświetlana w kolekcjach jako BYOK. Ponadto zmienne środowiskowe są wyświetlane w Twoich środowiskach.

### <a name="understand-the-rest-api-requests-in-the-collection"></a>Zrozumienie żądań interfejsu API REST w kolekcji

Kolekcja zawiera następujące żądania interfejsu API REST.

> [!NOTE]
>
>- Żądania muszą być wysyłane w podanej kolejności.
>- Większość żądań ma Skrypty testowe, które dynamicznie tworzą zmienne globalne dla następnego żądania w sekwencji.
>- Nie musisz ręcznie tworzyć zmiennych globalnych.

W programie Poster zobaczysz te zmienne zawarte w nawiasach. Na przykład `{{bearerToken}}`.

1. Pobieranie tokenu usługi Azure AD: Test ustawia zmienną globalną **bearerToken**.
2. Pobierz token Microsoft Graph: Test ustawia zmienną globalną **graphToken**.
3. Pobierz szczegóły jednostki usługi: Test ustawia zmienną globalną **servicePrincipalObjectId**.
4. Tworzenie konta magazynu: Test ustawia zmienną globalną **storageAccountId**.
5. Utwórz konto Media Services z tożsamością zarządzaną przez system: Test ustawia zmienną globalną **principalId**.
6. Utwórz magazyn kluczy, aby udzielić dostępu do jednostki usługi: Test ustawia zmienną globalną **keyVaultId**.
7. Pobierz token Key Vault: Test ustawia zmienną globalną **keyVaultToken**.
8. Utwórz klucz RSA w magazynie kluczy: Test ustawia zmienną globalną **keyId**.
9. Zaktualizuj konto Media Services, aby użyć klucza z kontem magazynu: nie ma skryptu testowego dla tego żądania.

## <a name="define-environment-variables"></a>Definiuj zmienne środowiskowe

1. Wybierz listę rozwijaną środowiska, aby przełączyć się do pobranego środowiska.
1. Ustanów zmienne środowiskowe w programie Poster. Są one również używane jako zmienne zawarte w nawiasach. Na przykład `{{tenantId}}`.

    - **tenantId**: Identyfikator dzierżawy.
    - **servicePrincipalId**: identyfikator jednostki usługi nawiązanej przy użyciu ulubionej metody, takiej jak portal lub interfejs wiersza polecenia.
    - **servicePrincipalSecret**: wpis tajny został utworzony dla jednostki usługi.
    - **subskrypcja**: Identyfikator subskrypcji.
    - **storagename**: nazwa, którą chcesz przypisać do magazynu.
    - **AccountName**: nazwa konta Media Services, którego chcesz użyć.
    - Identyfikator **magazynu** kluczy: nazwa magazynu klucza, którego chcesz użyć.
    - **resourceLocation**: Lokalizacja **Centralna** lub miejsce, w którym chcesz umieścić swoje zasoby. Ta kolekcja została przetestowana tylko z **centralną**.
    - Grupa **zasobów: Nazwa** grupy zasobu.

    Poniżej przedstawiono zmienne standardowe do pracy z zasobami platformy Azure. Dlatego nie trzeba go zmieniać.

    - **armResource**: `https://management.core.windows.net`
    - **graphResource**: `https://graph.windows.net/`
    - **keyVaultResource**: `https://vault.azure.net`
    - **armEndpoint**: `management.azure.com`
    - **graphEndpoint**: `graph.windows.net`
    - **aadEndpoint**: `login.microsoftonline.com`
    - **keyVaultDomainSuffix**: `vault.azure.net`

## <a name="send-the-requests"></a>Wyślij żądania

Po zdefiniowaniu zmiennych środowiskowych można uruchamiać żądania pojedynczo w poprzedniej sekwencji. Można też użyć modułu uruchamiającego programu post, aby uruchomić kolekcję.

## <a name="change-the-key"></a>Zmień klucz

Media Services automatycznie wykrywa, kiedy klucz zostanie zmieniony. Utwórz kolejną wersję klucza dla tego samego klucza w celu przetestowania tego procesu. Media Services powinien wykryć klucz w czasie krótszym niż 15 minut.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z utworzonych zasobów, nie *musisz już otrzymywać opłat*, usuń je.

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak:
> [!div class="nextstepaction"]
> [Koduj plik zdalny na podstawie adresu URL i strumieniowego wideo przy użyciu interfejsu REST](stream-files-tutorial-with-rest.md)
