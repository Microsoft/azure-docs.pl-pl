---
title: Uwierzytelnianie między usługami — Data Lake Storage Gen1 — Azure
description: Dowiedz się, jak uzyskać uwierzytelnianie między usługami Azure Data Lake Storage Gen1 przy użyciu Azure Active Directory.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 9dc195f98310e63cbde06885effe86ea3c239249
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91576102"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Uwierzytelnianie między usługami Azure Data Lake Storage Gen1 przy użyciu Azure Active Directory
> [!div class="op_single_selector"]
> * [Uwierzytelnianie użytkowników końcowych](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Uwierzytelnianie między usługami](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
>  

Azure Data Lake Storage Gen1 używa Azure Active Directory do uwierzytelniania. Przed utworzeniem aplikacji, która współpracuje z Data Lake Storage Gen1, należy zdecydować, jak uwierzytelnić aplikację przy użyciu Azure Active Directory (Azure AD). Dostępne są dwie główne opcje:

* Uwierzytelnianie użytkowników końcowych 
* Uwierzytelnianie między usługami (ten artykuł) 

Obie te opcje powodują, że aplikacja jest dostarczana z tokenem OAuth 2,0, który jest dołączany do każdego żądania wysyłanego do Data Lake Storage Gen1.

W tym artykule omówiono sposób tworzenia **aplikacji sieci Web usługi Azure AD na potrzeby uwierzytelniania między usługami**. Aby uzyskać instrukcje dotyczące konfiguracji aplikacji usługi Azure AD na potrzeby uwierzytelniania użytkowników końcowych, zobacz [uwierzytelnianie użytkowników końcowych w Data Lake Storage Gen1 przy użyciu Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Wymagania wstępne
* Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-create-an-active-directory-web-application"></a>Krok 1. Tworzenie aplikacji sieci Web Active Directory

Utwórz i skonfiguruj aplikację sieci Web usługi Azure AD na potrzeby uwierzytelniania między usługami i usługą Azure Data Lake Storage Gen1 przy użyciu Azure Active Directory. Aby uzyskać instrukcje, zobacz [Tworzenie aplikacji usługi Azure AD](../active-directory/develop/howto-create-service-principal-portal.md).

Podczas wykonywania instrukcji w powyższym łączu upewnij się, że wybrano pozycję **Web App/API** dla typu aplikacji, jak pokazano na poniższym zrzucie ekranu:

![Tworzenie aplikacji internetowej](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "Tworzenie aplikacji internetowej")

## <a name="step-2-get-application-id-authentication-key-and-tenant-id"></a>Krok 2. Uzyskiwanie identyfikatora aplikacji, klucza uwierzytelniania i identyfikatora dzierżawy
Podczas programowego rejestrowania w programie potrzebny jest identyfikator aplikacji. Jeśli aplikacja działa w ramach własnych poświadczeń, wymagany jest również klucz uwierzytelniania.

* Aby uzyskać instrukcje dotyczące pobierania identyfikatora aplikacji i klucza uwierzytelniania (nazywanego również wpisem tajnym klienta) dla aplikacji, zobacz [Pobieranie identyfikatora aplikacji i klucza uwierzytelniania](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).

* Aby uzyskać instrukcje dotyczące pobierania identyfikatora dzierżawy, zobacz [Pobieranie identyfikatora dzierżawy](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder"></a>Krok 3. przypisanie aplikacji usługi Azure AD do pliku lub folderu konta Azure Data Lake Storage Gen1


1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Otwórz konto Data Lake Storage Gen1, które chcesz skojarzyć z utworzoną wcześniej aplikacją Azure Active Directory.
2. W bloku konta Data Lake Storage Gen1 kliknij pozycję **Eksplorator danych**.
   
    ![Tworzenie katalogów na koncie Data Lake Storage Gen1](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "Tworzenie katalogów na koncie Data Lake")
3. W bloku **Eksplorator danych** kliknij plik lub folder, dla którego chcesz zapewnić dostęp do aplikacji usługi Azure AD, a następnie kliknij pozycję **dostęp**. Aby skonfigurować dostęp do pliku, należy kliknąć przycisk **dostęp** w bloku **Podgląd pliku** .
   
    ![Ustawianie list ACL w systemie plików Data Lake](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "Ustawianie list ACL w systemie plików Data Lake")
4. Blok **dostępu** zawiera listę dostępu standardowego i dostępu niestandardowego, które są już przypisane do katalogu głównego. Kliknij ikonę **Dodaj** , aby dodać listy ACL na poziomie niestandardowym.
   
    ![Wyświetlanie listy standardowych i niestandardowych](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "Wyświetlanie listy standardowych i niestandardowych")
5. Kliknij ikonę **Dodaj** , aby otworzyć blok **Dodawanie dostępu niestandardowego** . W tym bloku kliknij pozycję **Wybierz użytkownika lub grupę**, a następnie w bloku **Wybierz użytkownika lub grupę** wyszukaj utworzoną wcześniej aplikację Azure Active Directory. Jeśli masz wiele grup do przeszukiwania, użyj pola tekstowego u góry, aby odfiltrować nazwę grupy. Kliknij grupę, którą chcesz dodać, a następnie kliknij pozycję **Wybierz**.
   
    ![Dodawanie grupy](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "Dodawanie grupy")
6. Kliknij pozycję **Wybierz uprawnienia**, wybierz uprawnienia i określ, czy chcesz przypisać uprawnienia jako domyślną listę ACL, listę ACL dostępu czy obie. Kliknij przycisk **OK**.
   
    ![Zrzut ekranu przedstawiający blok Dodawanie dostępu niestandardowego z opcją wybierz uprawnienia o nazwie "out" i bloku Wybieranie uprawnień z opcją "OK".](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "Przypisywanie uprawnień do grupy")
   
    Aby uzyskać więcej informacji o uprawnieniach w Data Lake Storage Gen1 i domyślnych listach kontroli dostępu, zobacz [Access Control w Data Lake Storage Gen1](data-lake-store-access-control.md).
7. W bloku **Dodaj niestandardowy dostęp** kliknij przycisk **OK**. Nowo dodane grupy ze skojarzonymi uprawnieniami są wyświetlane w bloku **dostępu** .
   
    ![Zrzut ekranu przedstawiający blok dostęp z nowo dodaną grupą o nazwie z niestandardowym dostępem.](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "Przypisywanie uprawnień do grupy")

> [!NOTE]
> Jeśli planujesz ograniczenie Azure Active Directory aplikacji do określonego folderu, musisz również nadać temu samemu uprawnienia do **wykonywania** tej samej aplikacji usługi Azure Active Directory, aby umożliwić dostęp do tworzenia plików za pośrednictwem zestawu .NET SDK.

> [!NOTE]
> Jeśli chcesz użyć zestawów SDK do utworzenia konta Data Lake Storage Gen1, musisz przypisać aplikację sieci Web usługi Azure AD jako rolę do grupy zasobów, w której tworzysz konto Data Lake Storage Gen1.
> 
>

## <a name="step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications"></a>Krok 4. Pobieranie punktu końcowego tokenu OAuth 2,0 (tylko dla aplikacji opartych na języku Java)

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i kliknij przycisk Active Directory w lewym okienku.

2. W lewym okienku kliknij pozycję **rejestracje aplikacji**.

3. W górnej części bloku Rejestracje aplikacji kliknij pozycję **punkty końcowe**.

    ![Zrzut ekranu przedstawiający Active Directory z opcją Rejestracje aplikacji i punktami końcowymi o nazwie out.](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint.png "Punkt końcowy tokenu OAuth")

4. Z listy punktów końcowych Skopiuj punkt końcowy tokenu OAuth 2,0.

    ![Zrzut ekranu przedstawiający blok punkty końcowe z ikoną punktu końcowego tokenu uwierzytelniania O wartości 2 punktu O o nazwie "out".](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint-1.png "Punkt końcowy tokenu OAuth")   

## <a name="next-steps"></a>Następne kroki
W tym artykule opisano tworzenie aplikacji sieci Web usługi Azure AD i zebrano potrzebne informacje w aplikacjach klienckich, które tworzysz przy użyciu zestawu .NET SDK, Java, Python, interfejsu API REST itd. Teraz możesz przechodzić do poniższych artykułów, które zapoznają się z używaniem aplikacji natywnych usługi Azure AD do pierwszego uwierzytelniania przy użyciu Data Lake Storage Gen1 a następnie wykonywania innych operacji w sklepie.

* [Uwierzytelnianie między usługami Data Lake Storage Gen1 przy użyciu języka Java](data-lake-store-service-to-service-authenticate-java.md)
* [Uwierzytelnianie między usługami Data Lake Storage Gen1 przy użyciu zestawu .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
* [Uwierzytelnianie między usługami Data Lake Storage Gen1 przy użyciu języka Python](data-lake-store-service-to-service-authenticate-python.md)
* [Uwierzytelnianie między usługami Data Lake Storage Gen1 przy użyciu interfejsu API REST](data-lake-store-service-to-service-authenticate-rest-api.md)


