---
title: Uwierzytelnianie użytkowników końcowych — Data Lake Storage Gen1 z usługą Azure AD
description: Dowiedz się, jak uzyskać uwierzytelnianie użytkowników końcowych za pomocą Azure Data Lake Storage Gen1 przy użyciu Azure Active Directory
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: has-adal-ref
ms.openlocfilehash: 0dd5d1da41c5fcc596e4c70f797e75c30997ce0b
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578550"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Uwierzytelnianie użytkowników końcowych za pomocą Azure Data Lake Storage Gen1 przy użyciu Azure Active Directory
> [!div class="op_single_selector"]
> * [Uwierzytelnianie użytkowników końcowych](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Uwierzytelnianie między usługami](data-lake-store-service-to-service-authenticate-using-active-directory.md)
>
>

Azure Data Lake Storage Gen1 używa Azure Active Directory do uwierzytelniania. Przed utworzeniem aplikacji, która współpracuje z Data Lake Storage Gen1 lub Azure Data Lake Analytics, należy zdecydować, jak uwierzytelnić aplikację przy użyciu Azure Active Directory (Azure AD). Dostępne są dwie główne opcje:

* Uwierzytelnianie użytkowników końcowych (ten artykuł)
* Uwierzytelnianie między usługami (Wybierz tę opcję z listy rozwijanej powyżej)

Obie te opcje powodują, że aplikacja jest dostarczana z tokenem OAuth 2,0, który jest dołączany do każdego żądania wysyłanego do Data Lake Storage Gen1 lub Azure Data Lake Analytics.

W tym artykule omówiono sposób tworzenia **aplikacji natywnej usługi Azure AD na potrzeby uwierzytelniania użytkowników końcowych**. Aby uzyskać instrukcje dotyczące konfiguracji aplikacji usługi Azure AD na potrzeby uwierzytelniania między usługami, zobacz Uwierzytelnianie między usługami i [Data Lake Storage Gen1 przy użyciu Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Wymagania wstępne
* Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* Identyfikator subskrypcji. Można go pobrać z Azure Portal. Na przykład jest dostępny w bloku konta Data Lake Storage Gen1.

    ![Pobierz identyfikator subskrypcji](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)

* Nazwa domeny usługi Azure AD. Możesz ją pobrać, aktywując wskaźnik myszy w prawym górnym rogu Azure Portal. Na poniższym zrzucie ekranu nazwa domeny to **contoso.onmicrosoft.com**, a identyfikator GUID w nawiasach.

    ![Pobierz domenę usługi AAD](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

* Identyfikator dzierżawy platformy Azure. Aby uzyskać instrukcje dotyczące pobierania identyfikatora dzierżawy, zobacz [Pobieranie identyfikatora dzierżawy](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).

## <a name="end-user-authentication"></a>Uwierzytelnianie użytkowników końcowych
Mechanizm uwierzytelniania jest zalecanym rozwiązaniem, jeśli użytkownik końcowy ma zalogować się do aplikacji za pośrednictwem usługi Azure AD. Aplikacja będzie w stanie uzyskać dostęp do zasobów platformy Azure z takim samym poziomem dostępu jak zalogowany użytkownik końcowy. Użytkownik końcowy musi okresowo podawać poświadczenia, aby aplikacja mogła zachować dostęp.

Wynikiem posiadania logowania użytkownika końcowego jest to, że aplikacja otrzymuje token dostępu i token odświeżenia. Token dostępu jest dołączany do każdego żądania wykonanego do Data Lake Storage Gen1 lub Data Lake Analytics i jest domyślnie ważny przez jedną godzinę. Token odświeżania może służyć do uzyskania nowego tokenu dostępu i jest domyślnie ważny przez maksymalnie dwa tygodnie. Możesz użyć dwóch różnych metod logowania użytkownika końcowego.

### <a name="using-the-oauth-20-pop-up"></a>Korzystanie z okienka wyskakującego OAuth 2,0
Aplikacja może wyzwolić wyskakujące okienko autoryzacji OAuth 2,0, w którym użytkownik końcowy może wprowadzić swoje poświadczenia. To okno podręczne działa również w procesie uwierzytelniania dwuskładnikowego (funkcji 2FA) usługi Azure AD, jeśli to konieczne.

> [!NOTE]
> Ta metoda nie jest jeszcze obsługiwana w bibliotece Azure AD Authentication Library (ADAL) dla języka Python lub Java.
>
>

### <a name="directly-passing-in-user-credentials"></a>Bezpośrednie przekazywanie poświadczeń użytkownika
Aplikacja może bezpośrednio podać poświadczenia użytkownika w usłudze Azure AD. Ta metoda działa tylko z kontami użytkowników o IDENTYFIKATORze organizacji; nie jest on zgodny z kontami użytkowników osobisty/"Live ID", w tym kontami kończącymi się na @outlook.com lub @live.com . Ponadto ta metoda nie jest zgodna z kontami użytkowników, które wymagają uwierzytelniania dwuskładnikowego usługi Azure AD (funkcji 2FA).

### <a name="what-do-i-need-for-this-approach"></a>Co jest potrzebne do tego podejścia?
* Nazwa domeny usługi Azure AD. To wymaganie jest już wymienione w wymaganiach wstępnych dotyczących tego artykułu.
* Identyfikator dzierżawy usługi Azure AD. To wymaganie jest już wymienione w wymaganiach wstępnych dotyczących tego artykułu.
* **Aplikacja natywna** usługi Azure AD
* Identyfikator aplikacji dla aplikacji natywnej usługi Azure AD
* Identyfikator URI przekierowania dla aplikacji natywnej usługi Azure AD
* Ustaw uprawnienia delegowane.


## <a name="step-1-create-an-active-directory-native-application"></a>Krok 1. Tworzenie aplikacji Active Directory Native

Utwórz i skonfiguruj natywną aplikację usługi Azure AD na potrzeby uwierzytelniania użytkowników końcowych za pomocą Data Lake Storage Gen1 przy użyciu Azure Active Directory. Aby uzyskać instrukcje, zobacz [Tworzenie aplikacji usługi Azure AD](../active-directory/develop/howto-create-service-principal-portal.md).

Po zakończeniu wykonywania instrukcji w linku upewnij się, że wybrano opcję **natywny** dla typu aplikacji, jak pokazano na poniższym zrzucie ekranu:

![Tworzenie aplikacji internetowej](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-create-native-app.png "Tworzenie aplikacji natywnej")

## <a name="step-2-get-application-id-and-redirect-uri"></a>Krok 2. Pobieranie identyfikatora aplikacji i identyfikatora URI przekierowania

Zobacz [Pobieranie identyfikatora aplikacji](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) , aby pobrać identyfikator aplikacji.

Aby pobrać identyfikator URI przekierowania, wykonaj następujące czynności.

1. W Azure Portal wybierz pozycję **Azure Active Directory**, kliknij pozycję **rejestracje aplikacji**, a następnie Znajdź i kliknij utworzoną aplikację Azure AD Native.

2. W bloku **Ustawienia** aplikacji kliknij pozycję **Przekieruj identyfikatory URI**.

    ![Pobierz identyfikator URI przekierowania](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-redirect-uri.png)

3. Skopiuj wyświetlaną wartość.


## <a name="step-3-set-permissions"></a>Krok 3. Ustawianie uprawnień

1. W Azure Portal wybierz pozycję **Azure Active Directory**, kliknij pozycję **rejestracje aplikacji**, a następnie Znajdź i kliknij utworzoną aplikację Azure AD Native.

2. W bloku **Ustawienia** aplikacji kliknij pozycję **wymagane uprawnienia**, a następnie kliknij przycisk **Dodaj**.

    ![Zrzut ekranu przedstawiający blok ustawień z opcją redirect p R I o nazwie wywoływanej i przekierowania U R I z rzeczywistą p R I wywołałą.](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)

3. W bloku **Dodaj dostęp do interfejsu API** kliknij pozycję **Wybierz interfejs API**, kliknij pozycję **Azure Data Lake**, a następnie kliknij pozycję **Wybierz**.

    ![Zrzut ekranu przedstawiający blok Dodaj dostęp do interfejsu API z opcją wybierz interfejs API o nazwie out i blokiem wybierz interfejs API z opcją Azure Data Lake i opcją SELECT.](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)

4.  W bloku **Dodaj dostęp do interfejsu API** kliknij pozycję **Wybierz uprawnienia**, zaznacz pole wyboru, aby zapewnić **pełny dostęp do Data Lake Store**, a następnie kliknij przycisk **Wybierz**.

    ![Zrzut ekranu przedstawiający blok Dodawanie dostępu do interfejsu API z opcją wyboru uprawnień o nazwie "out" i bloku Włącz dostęp z opcją ma pełny dostęp do usługi Azure Data Lake, a opcja Select została wywołana.](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-3.png)

    Kliknij pozycję **Gotowe**.

5. Powtórz ostatnie dwa kroki, aby również udzielić uprawnień dla **systemu Windows Azure interfejs API zarządzania usługami** .

## <a name="next-steps"></a>Następne kroki
W tym artykule utworzono aplikację natywną usługi Azure AD i zebrano potrzebne informacje w aplikacjach klienckich, które tworzysz przy użyciu zestawu .NET SDK, zestawu Java SDK, interfejsu API REST itd. Teraz możesz przechodzić do poniższych artykułów, które zapoznają się z używaniem aplikacji sieci Web usługi Azure AD do pierwszego uwierzytelniania przy użyciu Data Lake Storage Gen1 a następnie wykonywania innych operacji w sklepie.

* [Uwierzytelnianie użytkownika końcowego za pomocą Data Lake Storage Gen1 przy użyciu zestawu Java SDK](data-lake-store-end-user-authenticate-java-sdk.md)
* [Uwierzytelnianie użytkowników końcowych za pomocą Data Lake Storage Gen1 przy użyciu zestawu .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
* [Uwierzytelnianie użytkowników końcowych za pomocą Data Lake Storage Gen1 przy użyciu języka Python](data-lake-store-end-user-authenticate-python.md)
* [Uwierzytelnianie użytkowników końcowych za pomocą Data Lake Storage Gen1 korzystania z interfejsu API REST](data-lake-store-end-user-authenticate-rest-api.md)
