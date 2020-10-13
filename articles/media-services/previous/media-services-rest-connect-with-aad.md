---
title: Korzystanie z uwierzytelniania usługi Azure AD w celu uzyskiwania dostępu do Azure Media Services API przy użyciu usługi REST | Microsoft Docs
description: Dowiedz się, jak uzyskać dostęp do Azure Media Services API z uwierzytelnianiem za pomocą usługi Azure Active Directory przy użyciu protokołu REST.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.reviewer: willzhan; johndeu
ms.openlocfilehash: 958bfa605e0195b5f4fde2c0ff53a8ce567f50a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89257147"
---
# <a name="use-azure-ad-authentication-to-access-the-media-services-api-with-rest"></a>Używanie uwierzytelniania usługi Azure AD do uzyskiwania dostępu do interfejsu API usługi Media Services za pomocą architektury REST

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją [Media Services wersja 3](../latest/index.yml). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-from-v2-to-v3.md)

W przypadku korzystania z uwierzytelniania usługi Azure AD za pomocą Azure Media Services można uwierzytelnić się na jeden z dwóch sposobów:

- **Uwierzytelnianie użytkownika** służy do uwierzytelniania osoby, która używa aplikacji do współdziałania z zasobami Azure Media Services. Aplikacja interaktywna powinna najpierw monitować użytkownika o poświadczenia. Przykładem jest aplikacja konsoli zarządzania, która jest używana przez autoryzowanych użytkowników do monitorowania zadań kodowania lub przesyłania strumieniowego na żywo. 
- **Uwierzytelnianie jednostki usługi** uwierzytelnia usługę. Aplikacje, które często używają tej metody uwierzytelniania, to aplikacje, które uruchamiają usługi demonów, usługi warstwy środkowej lub zaplanowane zadania, takie jak aplikacje sieci Web, aplikacje funkcji, Aplikacje logiki, interfejsy API lub mikrousługi.

    W tym samouczku pokazano, jak za pomocą uwierzytelniania **podstawowego usługi** Azure AD uzyskać dostęp do interfejsu API AMS przy użyciu protokołu REST. 

    > [!NOTE]
    > Nazwa **główna usługi** jest zalecanym najlepszym rozwiązaniem dla większości aplikacji łączących się z Azure Media Services. 

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Pobierz informacje o uwierzytelnianiu z Azure Portal
> * Uzyskiwanie tokenu dostępu przy użyciu programu Poster
> * Testowanie interfejsu API **zasobów** przy użyciu tokenu dostępu


> [!IMPORTANT]
> Obecnie Media Services obsługuje model uwierzytelniania usług Azure Access Control Services. Jednak uwierzytelnianie Access Control będzie przestarzałe 1 czerwca 2018. Zalecamy jak najszybszą migrację do modelu uwierzytelniania za pomocą usługi Azure AD.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Utwórz konto Azure Media Services przy użyciu Azure Portal](media-services-portal-create-account.md).
- Zapoznaj się z artykułem [Omówienie uzyskiwania dostępu do interfejsu API Azure Media Services przy użyciu uwierzytelniania usługi Azure AD](media-services-use-aad-auth-to-access-ams-api.md) .
- Zainstaluj klienta REST programu [post](https://www.getpostman.com/) na potrzeby wykonywania interfejsów API REST przedstawionych w tym artykule. 

    W tym samouczku korzystamy z programu **Poster** , ale wszystkie narzędzia REST byłyby odpowiednie. Inne alternatywy to: **Visual Studio Code** z wtyczką REST lub **Telerik programu Fiddler**. 

## <a name="get-the-authentication-information-from-the-azure-portal"></a>Pobierz informacje o uwierzytelnianiu z Azure Portal

### <a name="overview"></a>Omówienie

Aby uzyskać dostęp do interfejsu API Media Services, należy zebrać następujące punkty danych.

|Ustawienie|Przykład|Opis|
|---|-------|-----|
|Azure Active Directory domeny dzierżawy|microsoft.onmicrosoft.com|Usługa Azure AD jako punkt końcowy usługi bezpiecznego tokenu (STS) jest tworzona przy użyciu następującego formatu: <https://login.microsoftonline.com/{your-ad-tenant-name.onmicrosoft.com}/oauth2/token> . Usługa Azure AD wystawia token JWT w celu uzyskania dostępu do zasobów (tokenu dostępu).|
|Punkt końcowy interfejsu API REST|<https://amshelloworld.restv2.westus.media.azure.net/api/>|Jest to punkt końcowy, w którym wykonywane są wszystkie wywołania interfejsu API REST w aplikacji Media Services.|
|Identyfikator klienta (Identyfikator aplikacji)|f7fbbb29-a02d-4d91-bbc6-59a2579259d2|Identyfikator aplikacji usługi Azure AD (klienta). Do uzyskania tokenu dostępu wymagany jest identyfikator klienta. |
|Klucz tajny klienta|+ mUERiNzVMoJGggD6aV1etzFGa1n6KeSlLjIq + Dbim0 =|Klucze aplikacji usługi Azure AD (klucz tajny klienta). Wpis tajny klienta jest wymagany do pobrania tokenu dostępu.|

### <a name="get-aad-auth-info-from-the-azure-portal"></a>Pobierz informacje uwierzytelniania usługi AAD z Azure Portal

Aby uzyskać informacje, wykonaj następujące kroki:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2. Przejdź do wystąpienia AMS.
3. Wybierz pozycję **dostęp do interfejsu API**.
4. Kliknij pozycję **Połącz z interfejsem API Azure Media Services przy użyciu nazwy głównej usługi**.

    ![Dostęp za pomocą interfejsu API](./media/connect-with-rest/connect-with-rest01.png)

5. Wybierz istniejącą **aplikację usługi Azure AD** lub Utwórz nową (pokazaną poniżej).

    > [!NOTE]
    > Aby żądanie REST usługi Azure Media zakończyło się pomyślnie, użytkownik wywołujący musi mieć rolę **współautora** lub **właściciela** dla konta Media Services, do którego próbuje uzyskać dostęp. Jeśli zostanie wyświetlony wyjątek "serwer zdalny zwrócił błąd: (401) nieautoryzowany," Zobacz [Kontrola dostępu](media-services-use-aad-auth-to-access-ams-api.md#access-control).

    Jeśli musisz utworzyć nową aplikację usługi AD, wykonaj następujące kroki:
    
   1. Naciśnij pozycję **Utwórz nowy**.
   2. Wprowadź nazwę.
   3. Naciśnij ponownie przycisk **Utwórz nowy** .
   4. Naciśnij pozycję **Zapisz**.

      ![Dostęp za pomocą interfejsu API](./media/connect-with-rest/new-app.png)

      Nowa aplikacja zostanie wyświetlona na stronie.

6. Pobierz **Identyfikator klienta** (Identyfikator aplikacji).
    
   1. Wybierz aplikację.
   2. Pobierz **Identyfikator klienta** z okna po prawej stronie. 

      ![Dostęp za pomocą interfejsu API](./media/connect-with-rest/existing-client-id.png)

7. Pobieranie **klucza** aplikacji (klucz tajny klienta). 

   1. Kliknij przycisk **Zarządzaj aplikacją** (Zwróć uwagę, że informacje o identyfikatorze klienta są w obszarze **Identyfikator aplikacji**). 
   2. Naciśnij **klawisze**.
    
       ![Dostęp za pomocą interfejsu API](./media/connect-with-rest/manage-app.png)
   3. Wygeneruj klucz aplikacji (klucz tajny klienta), wypełniając **Opis** i **wygaśnie** , a następnie naciskając klawisz **Save (Zapisz**).
    
       Po naciśnięciu przycisku **Zapisz** zostanie wyświetlona wartość klucza. Skopiuj wartość klucza przed opuszczeniem bloku.

   ![Dostęp za pomocą interfejsu API](./media/connect-with-rest/connect-with-rest03.png)

Możesz dodać wartości parametrów połączenia usługi AD do pliku web.config lub app.config do późniejszego użycia w kodzie.

> [!IMPORTANT]
> **Klucz klienta** jest ważnym kluczem tajnym i powinien być prawidłowo zabezpieczony w magazynie kluczy lub zaszyfrowany w środowisku produkcyjnym.

## <a name="get-the-access-token-using-postman"></a>Uzyskiwanie tokenu dostępu przy użyciu programu Poster

W tej sekcji pokazano, jak za pomocą programu **Poster** wykonać interfejs API REST, który zwraca token okaziciela JWT (token dostępu). Aby wywołać dowolny Media Services interfejs API REST, należy dodać nagłówek "Authorization" do wywołań i dodać wartość "Bearer *your_access_token*" do każdego wywołania (jak pokazano w następnej sekcji tego samouczka). 

1. Otwórz **notkę**.
2. Wybierz pozycję **POST**.
3. Wprowadź adres URL, który zawiera nazwę dzierżawy, w następującym formacie: Nazwa dzierżawy powinna kończyć się znakiem **. onmicrosoft.com** , a adres URL powinien kończyć się **OAuth2/tokenem**: 

    `https://login.microsoftonline.com/{your-aad-tenant-name.onmicrosoft.com}/oauth2/token`

4. Wybierz kartę **nagłówki** .
5. Wprowadź informacje o **nagłówkach** za pomocą siatki danych "klucz/wartość". 

    ![siatka danych](./media/connect-with-rest/headers-data-grid.png)

    Alternatywnie możesz kliknąć link **Edytuj zbiorczo** po prawej stronie okna programu Poster i wkleić poniższy kod.

    ```javascript
    Content-Type:application/x-www-form-urlencoded
    Keep-Alive:true
    ```

6. Naciśnij kartę **treść** .
7. Wprowadź informacje o treści przy użyciu siatki danych "klucz/wartość" (Zastąp wartości identyfikator klienta i klucz tajny). 

    ![siatka danych](./media/connect-with-rest/data-grid.png)

    Alternatywnie, kliknij przycisk **Edytuj zbiorczo** po prawej stronie okna programu Poster i wklej następujący tekst (Zastąp wartości identyfikator klienta i klucz tajny):

    ```javascript
    grant_type:client_credentials
    client_id:{Your Client ID that you got from your Azure AD Application}
    client_secret:{Your client secret that you got from your Azure AD Application's Keys}
    resource:https://rest.media.azure.net
    ```

8. Kliknij pozycję **Wyślij**.

    ![Pobierz token](./media/connect-with-rest/connect-with-rest04.png)

Zwrócona odpowiedź zawiera **token dostępu** , który ma być używany do uzyskiwania dostępu do dowolnych interfejsów API AMS.

## <a name="test-the-assets-api-using-the-access-token"></a>Testowanie interfejsu API **zasobów** przy użyciu tokenu dostępu

W tej sekcji pokazano, jak uzyskać dostęp do interfejsu API **zasobów** przy użyciu programu **Poster**.

1. Otwórz **notkę**.
2. Wybierz pozycję **GET**.
3. Wklej punkt końcowy interfejsu API REST (na przykład https://amshelloworld.restv2.westus.media.azure.net/api/Assets)
4. Wybierz kartę **autoryzacja** . 
5. Wybierz **token elementu nośnego**.
6. Wklej token, który został utworzony w poprzedniej sekcji.

    ![Pobierz token](./media/connect-with-rest/connect-with-rest05.png)

    > [!NOTE]
    > ŚRODOWISKO użytkownika programu Poster może być inne niż komputery Mac i komputery. Jeśli wersja dla systemu Mac nie ma opcji "token okaziciela" na liście rozwijanej sekcja **uwierzytelniania** , należy ręcznie dodać nagłówek **autoryzacji** do klienta na komputerze Mac.

   ![Nagłówek uwierzytelniania](./media/connect-with-rest/auth-header.png)

7. Wybierz pozycję **nagłówki**.
5. Kliknij link **Edytuj zbiorczo** w prawym okienku okna.
6. Wklej następujące nagłówki:

    ```javascript
    x-ms-version:2.19
    Accept:application/json
    Content-Type:application/json
    DataServiceVersion:3.0
    MaxDataServiceVersion:3.0
    ```

7. Kliknij pozycję **Wyślij**.

Zwrócona odpowiedź zawiera zasoby, które znajdują się na Twoim koncie.

## <a name="next-steps"></a>Następne kroki

* Wypróbuj ten przykładowy kod w [uwierzytelnianiu usługi Azure AD, aby uzyskać dostęp do Azure Media Services: zarówno za pośrednictwem interfejsu API REST](https://github.com/willzhan/WAMSRESTSoln)
* [Przekazywanie plików za pomocą platformy .NET](media-services-dotnet-upload-files.md)
