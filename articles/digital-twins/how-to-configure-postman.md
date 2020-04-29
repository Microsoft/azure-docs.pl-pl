---
title: Jak skonfigurować usługę Poster — bliźniaczych reprezentacji Digital | Microsoft Docs
description: Informacje o konfigurowaniu i używaniu programu Poster do testowania interfejsów API Digital bliźniaczych reprezentacji platformy Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: ffcfb4f6ec5f6c654d0b243af85034ab575e0d88
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80297170"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>Jak skonfigurować usługę Poster dla usługi bliźniaczych reprezentacji Digital

W tym artykule opisano sposób konfigurowania klienta REST programu post na potrzeby współdziałania z interfejsami API zarządzania usługą Azure Digital bliźniaczych reprezentacji oraz ich testowania. W szczegółach opisano:

* Jak skonfigurować aplikację Azure Active Directory tak, aby korzystała z niejawnego przepływu uwierzytelniania OAuth 2,0.
* Jak używać klienta REST programu post do tworzenia żądań HTTP z tokenami do interfejsów API zarządzania.
* Jak za pomocą programu Ogłoś utworzyć wieloczęściowe żądania POST do interfejsów API zarządzania.

## <a name="postman-summary"></a>Podsumowanie ogłoszeń

Zacznij korzystać z usługi Azure Digital bliźniaczych reprezentacji, używając narzędzia klienta REST, takiego jak program do [publikowania](https://www.getpostman.com/) w celu przygotowania lokalnego środowiska testowania. Klient programu Poster pomaga szybko utworzyć złożone żądania HTTP. Pobierz wersję Desktop klienta programu Poster, przechodząc do [www.getpostman.com/apps](https://www.getpostman.com/apps).

[Poster](https://www.getpostman.com/) to narzędzie do testowania REST, które lokalizuje kluczowe funkcje żądania HTTP w UŻYTECZNym interfejsie użytkownika opartym na komputerach stacjonarnych i wtyczkach.

Za pośrednictwem klienta programu Poster deweloperzy rozwiązań mogą określić rodzaj żądania HTTP (*post*, *Get*, *Update*, *patch*i *delete*), punkt końcowy interfejsu API do wywołania i użycie protokołu TLS. Program pocztowy obsługuje także Dodawanie nagłówków żądań HTTP, parametrów, formularzy i danych oraz treści.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>Konfigurowanie Azure Active Directory do użycia niejawnego przepływu uwierzytelniania OAuth 2,0

1. Wykonaj kroki opisane w [przewodniku szybki start](quickstart-view-occupancy-dotnet.md#set-permissions-for-your-app) , aby utworzyć i skonfigurować aplikację Azure Active Directory. Alternatywnie możesz ponownie użyć istniejącej rejestracji aplikacji.

    [![Skonfiguruj nowy identyfikator URI przekierowania wpisu](media/how-to-configure-postman/authentication-redirect-uri.png)](media/how-to-configure-postman/authentication-redirect-uri.png#lightbox)

1. Teraz Dodaj **Identyfikator URI przekierowania** do `https://www.getpostman.com/oauth2/callback`elementu.

1. Zaznacz pole wyboru **niejawnie udzielaj** > **tokenom dostępu** , aby zezwolić na użycie niejawnego przepływu uwierzytelniania OAuth 2,0. Wybierz pozycję **Konfiguruj**, a następnie **Zapisz**.

1. Skopiuj **Identyfikator klienta** aplikacji Azure Active Directory.

## <a name="obtain-an-oauth-20-token"></a>Uzyskaj token OAuth 2,0

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]

Skonfiguruj i skonfiguruj aplikację do ogłaszania w celu uzyskania tokenu Azure Active Directory. Następnie należy wykonać uwierzytelnione żądanie HTTP na platformie Azure Digital bliźniaczych reprezentacji przy użyciu uzyskanego tokenu:

1. Sprawdź, czy **adres URL autoryzacji** jest poprawny. Powinien on mieć format:

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | Nazwa  | Zamień na | Przykład |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | Nazwa dzierżawy lub organizacji. Użyj przyjaznej nazwy, a nie alfanumerycznego **identyfikatora dzierżawy** Azure Active Directory rejestracji aplikacji. | `microsoft` |

1. Przejdź do [www.getpostman.com](https://www.getpostman.com/) , aby pobrać aplikację.

1. Chcemy wprowadzić żądanie GET. Wybierz kartę **autoryzacja** , wybierz pozycję OAuth 2,0, a następnie wybierz pozycję **Pobierz nowy token dostępu**.

    | Pole  | Wartość |
    |---------|---------|
    | Typ udzielenia | `Implicit` |
    | Adres URL wywołania zwrotnego | `https://www.getpostman.com/oauth2/callback` |
    | Adres URL uwierzytelniania | Użyj **adresu URL autoryzacji** z **kroku 1** |
    | Identyfikator klienta | Użyj **identyfikatora aplikacji** dla aplikacji Azure Active Directory, która została utworzona lub ponownie użyta w poprzedniej sekcji |
    | Zakres | Pozostaw puste |
    | Stan | Pozostaw puste |
    | Uwierzytelnianie klienta | `Send as Basic Auth header` |

1. Klient powinien teraz wyglądać następująco:

    [![Przykład tokenu klienta w programie Poster](media/how-to-configure-postman/configure-postman-oauth-token.png)](media/how-to-configure-postman/configure-postman-oauth-token.png#lightbox)

1. Wybierz pozycję **Request Token** (Zażądaj tokenu).
  
1. Przewiń w dół i wybierz pozycję **Użyj tokenu**.

## <a name="make-a-multipart-post-request"></a>Utwórz wieloczęściowe żądanie POST

Po wykonaniu powyższych kroków Skonfiguruj program do ogłaszania w celu wypróbowania uwierzytelnionego żądania protokołu HTTP wieloczęściowego:

1. Na karcie **nagłówki** Dodaj **Typ zawartości** klucz nagłówka żądania HTTP z wartością `multipart/mixed`.

   [![Określ typ zawartości wieloczęściowy/mieszany](media/how-to-configure-postman/configure-postman-content-type.png)](media/how-to-configure-postman/configure-postman-content-type.png#lightbox)

1. Serializacja danych nietekstowych w plikach. Dane JSON zostałyby zapisane jako plik JSON.
1. Na karcie **treść** wybierz opcję `form-data`. 
1. Dodaj każdy plik, przypisując nazwę **klucza** , `File`wybierając opcję.
1. Następnie zaznacz każdy plik za pomocą przycisku **Wybierz plik** .

   [![Przykład treści formularza klienta po wprowadzeniu tekstu](media/how-to-configure-postman/configure-postman-form-body.png)](media/how-to-configure-postman/configure-postman-form-body.png#lightbox)

   >[!NOTE]
   > * Klient programu Poster nie wymaga, aby fragmenty wieloczęściowe miały ręcznie przypisane **elementy Content-Type** i **Content-Dyspozycja**.
   > * Nie trzeba określać tych nagłówków dla każdej części.
   > * Musisz wybrać `multipart/mixed` lub inną odpowiednią **zawartość** dla całego żądania.

1. Na koniec wybierz pozycję **Wyślij** , aby przesłać wieloczęściowe żądanie HTTP POST. Kod stanu `200` lub `201` wskazuje pomyślne żądanie. Odpowiedni komunikat odpowiedzi pojawi się w interfejsie klienta.

1. Sprawdź poprawność danych żądania POST protokołu HTTP, wywołując punkt końcowy interfejsu API: 

   ```URL
   YOUR_MANAGEMENT_API_URL/spaces/blobs?includes=description
   ```

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o interfejsach API usługi Digital bliźniaczych reprezentacji Management i sposobach ich użycia, przeczytaj artykuł [jak korzystać z interfejsów API zarządzania usługą Azure Digital bliźniaczych reprezentacji](how-to-navigate-apis.md).

- Użyj wieloczęściowych żądań, aby [dodać obiekty blob do jednostek usługi Azure Digital bliźniaczych reprezentacji](./how-to-add-blobs.md).

- Aby dowiedzieć się więcej o uwierzytelnianiu za pomocą interfejsów API zarządzania, przeczytaj artykuł [uwierzytelnianie za pomocą interfejsów API](./security-authenticating-apis.md).
