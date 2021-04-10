---
title: Konfigurowanie programu Poster dla interfejsu API REST Azure Media Services v3
description: W tym artykule opisano sposób konfigurowania programu Poster, aby można było go użyć do wywołania interfejsów API REST Azure Media Services (AMS).
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: b0a521ddeee6d93ce809e5e6e85eefa1f01a3f12
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98953975"
---
# <a name="configure-postman-for-media-services-v3-rest-api-calls"></a>Konfigurowanie programu Poster dla wywołań interfejsu API REST z Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

W tym artykule opisano sposób konfigurowania programu **Poster** , aby można było go użyć do wywołania interfejsów api REST Azure Media Services (AMS). W tym artykule przedstawiono sposób importowania plików środowiska i kolekcji do programu **Poster**. Kolekcja zawiera zgrupowane definicje żądań HTTP, które wywołują Azure Media Services (AMS) interfejsy API REST. Plik środowiska zawiera zmienne, które są używane przez kolekcję.

Przed rozpoczęciem opracowywania programu zapoznaj [się z tematem Programowanie przy użyciu interfejsów api Media Services v3](media-services-apis-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

- [Utwórz konto Media Services](./create-account-howto.md). Pamiętaj, aby zapamiętać nazwę grupy zasobów i nazwę konta Media Services. 
- Uzyskaj informacje konieczne do [uzyskania dostępu do interfejsów API](./access-api-howto.md)
- Zainstaluj klienta REST programu [Postman](https://www.getpostman.com/), aby uruchomić interfejsy API REST przedstawione w niektórych samouczkach dotyczących AMS REST. 

    W przykładzie użyto programu **Postman**, ale odpowiednie będzie każde narzędzie REST. Inne alternatywy to: **Visual Studio Code** z wtyczką REST lub **Telerik programu Fiddler**. 

> [!IMPORTANT]
> Zapoznaj się z [konwencjami nazewnictwa](media-services-apis-overview.md#naming-conventions).

## <a name="download-postman-files"></a>Pobieranie plików Postman

Sklonuj repozytorium GitHub zawierające kolekcję programu Postman oraz pliki środowiska.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

## <a name="configure-postman"></a>Konfigurowanie programu Postman

### <a name="configure-the-environment"></a>Konfigurowanie środowiska 

1. Otwórz aplikację **Poster** .
2. Po prawej stronie ekranu wybierz opcję **Zarządzaj środowiskiem**.

    ![Zarządzanie środowiskiem](./media/develop-with-postman/postman-import-env.png)
4. W oknie dialogowym **Zarządzaj środowiskiem** kliknij opcję **Importuj**.
2. Przejdź do pliku `Azure Media Service v3 Environment.postman_environment.json`, który został pobrany podczas klonowania `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`.
6. Zostanie dodane środowisko **Azure Media Service v3 Environment**.

    > [!Note]
    > Zaktualizuj zmienne dostępu przy użyciu wartości z sekcji **Uzyskiwanie dostępu do interfejsu API usługi Media Services** powyżej.

7. Kliknij dwukrotnie wybrany plik, a następnie wprowadź wartości uzyskane podczas wykonywania kroków uzyskiwania dostępu do interfejsu API.
8. Zamknij okno dialogowe.
9. Z listy rozwijanej wybierz środowisko **Azure Media Service v3 Environment**.

    ![Wybór środowiska](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>Konfigurowanie kolekcji

1. Kliknij przycisk **Importuj**, aby zaimportować plik kolekcji.
1. Przejdź do pliku `Media Services v3.postman_collection.json`, który został pobrany podczas klonowania `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`
3. Wybierz plik **Media Services v3.postman_collection.json**.

    ![Importowanie pliku](./media/develop-with-postman/postman-import-collection.png)

## <a name="get-azure-ad-token"></a>Pobieranie tokenu usługi Azure AD 

Przed rozpoczęciem manipulowania zasobami usługi AMS v3 musisz uzyskać i ustawić token usługi Azure AD na potrzeby uwierzytelniania w ramach jednostki usług.

1. W lewym oknie aplikacji Poster wybierz pozycję "krok 1: pobieranie tokenu uwierzytelniania usługi AAD".
2. Następnie wybierz pozycję „Pobieranie tokenu usługi Azure AD do uwierzytelniania jednostki usługi”.
3. Kliknij pozycję **Wyślij**.

    Zostanie wysłana następująca operacja **POST**.

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. Odpowiedź wróci z tokenem i ustawi zmienną środowiskową „AccessToken” na wartość tokenu.  

    ![Pobieranie tokenu usługi AAD](./media/develop-with-postman/postman-get-aad-auth-token.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów 

* Jeśli aplikacja nie powiedzie się z "HTTP 504: limit czasu bramy", upewnij się, że zmienna lokalizacji nie została jawnie ustawiona na wartość inną niż oczekiwana Lokalizacja konta Media Services. 
* Jeśli zostanie wyświetlony komunikat o błędzie "nie znaleziono konta", upewnij się również, że właściwość Location w treści pliku JSON jest ustawiona na lokalizację, w której znajduje się konto Media Services. 

## <a name="see-also"></a>Zobacz też

- [Tworzenie filtrów za pomocą usługi Media Services — REST](filters-dynamic-manifest-rest-howto.md)
- [Interfejs API REST bazujący na usłudze Azure Resource Manager](https://github.com/Azure-Samples/media-services-v3-arm-templates)

## <a name="next-steps"></a>Następne kroki

- [Przesyłaj strumieniowo pliki przy użyciu protokołu REST](stream-files-tutorial-with-rest.md).  
- [Samouczek: Kodowanie pliku zdalnego na podstawie adresu URL i przesyłanie strumieniowe wideo — REST](stream-files-tutorial-with-rest.md)
