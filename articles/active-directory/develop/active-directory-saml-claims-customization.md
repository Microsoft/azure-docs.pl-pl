---
title: Dostosowywanie oświadczeń tokenu SAML aplikacji
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak dostosować oświadczenia wystawione przez platformę tożsamości firmy Microsoft w tokenie SAML dla aplikacji dla przedsiębiorstw.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 12/09/2020
ms.author: kenwith
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.openlocfilehash: 25e737afb524cb8c6f45ac8e99f46a8064ae7855
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598843"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>How to: customize claims issued in the SAML token for enterprise applications (Jak dostosować oświadczenia wystawione w tokenie SAML dla aplikacji dla przedsiębiorstw)

Obecnie platforma tożsamości firmy Microsoft obsługuje logowanie jednokrotne (SSO) w przypadku większości aplikacji dla przedsiębiorstw, w tym aplikacji wstępnie zintegrowanych w galerii aplikacji usługi Azure AD, a także aplikacji niestandardowych. Gdy użytkownik uwierzytelnia się w aplikacji za pośrednictwem platformy tożsamości firmy Microsoft przy użyciu protokołu SAML 2.0, platforma tożsamości firmy Microsoft wysyła token do aplikacji (za pośrednictwem żądania POST protokołu HTTP). Następnie aplikacja weryfikuje token i używa go do logowania użytkownika zamiast monitowania o nazwę użytkownika i hasło. Te tokeny SAML zawierają informacje o użytkowniku znane jako *oświadczenia*.

Oświadczenie *to* informacje, które dostawca tożsamości podaje o użytkowniku w tokenie wystawiany dla tego użytkownika. W [tokenie SAML](https://en.wikipedia.org/wiki/SAML_2.0)te dane są zwykle zawarte w instrukcji atrybutu SAML. Unikatowy identyfikator użytkownika jest zwykle reprezentowany w temacie SAML nazywanym również identyfikatorem nazwy.

Domyślnie platforma tożsamości firmy Microsoft wydaje aplikacji token SAML zawierający oświadczenie z wartością nazwy użytkownika (znaną także jako główna nazwa użytkownika) w usłudze Azure AD, które może jednoznacznie zidentyfikować `NameIdentifier` użytkownika. Token SAML zawiera również dodatkowe oświadczenia zawierające adres e-mail użytkownika, imię i nazwisko.

Aby wyświetlić lub edytować oświadczenia wystawione w tokenie SAML dla aplikacji, otwórz aplikację w Azure Portal. Następnie otwórz **sekcję Atrybuty użytkownika & oświadczenia.**

![Otwórz sekcję Atrybuty użytkownika & oświadczenia w Azure Portal](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

Istnieją dwie możliwe przyczyny, dla których może być konieczne edytowanie oświadczeń wystawionych w tokenie SAML:

* Aplikacja wymaga, aby oświadczenie lub NameID było czymś innym niż nazwa użytkownika (lub główna nazwa `NameIdentifier` użytkownika) przechowywana w usłudze Azure AD.
* Aplikacja została napisana tak, aby wymagała innego zestawu URI lub wartości oświadczenia.

## <a name="editing-nameid"></a>Edytowanie nameID

Aby edytować wartość NameID (identyfikator nazwy):

1. Otwórz stronę **Wartość identyfikatora** nazwy.
1. Wybierz atrybut lub przekształcenie, które chcesz zastosować do atrybutu. Opcjonalnie możesz określić format, który ma mieć oświadczenie NameID.

   ![Edytowanie wartości NameID (identyfikator nazwy)](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>Format NameID

Jeśli żądanie SAML zawiera element NameIDPolicy w określonym formacie, platforma tożsamości firmy Microsoft będzie honorować format w żądaniu.

Jeśli żądanie SAML nie zawiera elementu NameIDPolicy, platforma tożsamości firmy Microsoft wystawia wartość NameID w formacie, który określisz. Jeśli format nie zostanie określony, platforma tożsamości firmy Microsoft będzie używać domyślnego formatu źródła skojarzonego z wybranym źródłem oświadczenia.

Z listy **rozwijanej Wybierz format identyfikatora** nazwy możesz wybrać jedną z następujących opcji.

| Format NameID | Opis |
|---------------|-------------|
| **Domyślny** | Platforma tożsamości firmy Microsoft będzie używać domyślnego formatu źródła. |
| **Stale** | Platforma tożsamości firmy Microsoft będzie używać formatu Trwałe jako nameID. |
| **EmailAddress (Adres e-mail)** | Platforma tożsamości firmy Microsoft będzie używać adresu e-mail jako formatu NameID. |
| **Nie określono** | Platforma tożsamości firmy Microsoft będzie używać nieokreślonego jako formatu NameID. |

Przejściowy nameID jest również obsługiwany, ale nie jest dostępny na liście rozwijanej i nie można go skonfigurować po stronie platformy Azure. Aby dowiedzieć się więcej na temat atrybutu NameIDPolicy, zobacz Single Sign-On SAML protocol (Protokół SAML dla pojedynczego Sign-On [nameIDPolicy).](single-sign-on-saml-protocol.md)

### <a name="attributes"></a>Atrybuty

Wybierz żądane źródło dla `NameIdentifier` oświadczenia (lub NameID). Możesz wybrać jedną z następujących opcji.

| Nazwa | Opis |
|------|-------------|
| E-mail | Adres e-mail użytkownika |
| Userprincipalname | Główna nazwa użytkownika (UPN) użytkownika |
| onpremisessamaccountname | Nazwa konta SAM, które zostało zsynchronizowane z lokalnej usługi Azure AD |
| Objectid | Identyfikator objectid użytkownika w usłudze Azure AD |
| employeeid | Identyfikator pracownika użytkownika |
| Rozszerzenia katalogów | Rozszerzenia katalogów [synchronizowane z lokalna usługa Active Directory za pomocą Azure AD Connect Sync](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| Atrybuty rozszerzenia 1–15 | Atrybuty rozszerzenia lokalnego używane do rozszerzania schematu usługi Azure AD |

Aby uzyskać więcej informacji, zobacz [Tabela 3: Prawidłowe wartości identyfikatorów na źródło](reference-claims-mapping-policy-type.md#table-3-valid-id-values-per-source).

Możesz również przypisać dowolną stałą (statyczną) wartość do dowolnych oświadczeń, które definiujesz w usłudze Azure AD. Wykonaj poniższe kroki, aby przypisać wartość stałą:

1. W <a href="https://portal.azure.com/" target="_blank">oknie Azure Portal</a>w sekcji Atrybuty użytkownika **& oświadczenia** kliknij ikonę Edytuj, aby edytować oświadczenia. 
1. Kliknij wymagane oświadczenie, które chcesz zmodyfikować.
1. Wprowadź wartość stałą bez cudzysłowów w **atrybutze Source** zgodnie z twoją organizacją, a następnie kliknij przycisk **Zapisz**.

    ![Atrybuty organizacji & oświadczenia w Azure Portal](./media/active-directory-saml-claims-customization/organization-attribute.png)

1. Wartość stała będzie wyświetlana jak poniżej.

    ![Edytuj sekcję Atrybuty & oświadczenia w Azure Portal](./media/active-directory-saml-claims-customization/edit-attributes-claims.png)

### <a name="special-claims---transformations"></a>Oświadczenia specjalne — przekształcenia

Można również użyć funkcji przekształcania oświadczeń.

| Funkcja | Opis |
|----------|-------------|
| **ExtractMailPrefix()** | Usuwa sufiks domeny z adresu e-mail lub głównej nazwy użytkownika. Wyodrębnia tylko pierwszą część przekazywanej nazwy użytkownika (na przykład "joe_smith" zamiast joe_smith@contoso.com ). |
| **Join()** | Dołącza atrybut ze zweryfikowaną domeną. Jeśli wybrana wartość identyfikatora użytkownika ma domenę, wyodrębni nazwę użytkownika, aby dołączyć wybraną zweryfikowaną domenę. Jeśli na przykład wybierzesz adres e-mail ( ) jako wartość identyfikatora użytkownika i wybierzesz contoso.onmicrosoft.com jako zweryfikowaną domenę, spowoduje joe_smith@contoso.com to . joe_smith@contoso.onmicrosoft.com |
| **ToLower()** | Konwertuje znaki wybranego atrybutu na małe litery. |
| **ToUpper()** | Konwertuje znaki wybranego atrybutu na wielkie litery. |

## <a name="adding-application-specific-claims"></a>Dodawanie oświadczeń specyficznych dla aplikacji

Aby dodać oświadczenia specyficzne dla aplikacji:

1. Na **stronie Atrybuty & oświadczenia wybierz** pozycję Dodaj nowe **oświadczenie,** aby otworzyć stronę Zarządzanie **oświadczeniami** użytkownika.
1. Wprowadź **nazwę** oświadczeń. Wartość nie musi być ściśle przestrzegana wzorca URI zgodnie ze specyfikacją SAML. Jeśli potrzebujesz wzorca URI, możesz umieścić go w polu **Przestrzeń** nazw.
1. Wybierz **źródło,** w którym oświadczenie ma pobrać jego wartość. Możesz wybrać atrybut użytkownika z listy rozwijanej atrybutu źródłowego lub zastosować przekształcenie do atrybutu użytkownika przed emitem go jako oświadczenia.

### <a name="claim-transformations"></a>Przekształcanie oświadczenia

Aby zastosować przekształcenie do atrybutu użytkownika:

1. Na **stronie Zarządzanie oświadczeniem** wybierz *pozycję Przekształcenie* jako źródło oświadczenia, aby otworzyć stronę **Zarządzanie przekształceniem.**
2. Wybierz funkcję z listy rozwijanej przekształcenia. W zależności od wybranej funkcji należy podać parametry i stałą wartość do oceny w przekształceniu. Zapoznaj się z tabelą poniżej, aby uzyskać więcej informacji na temat dostępnych funkcji.
3. Aby zastosować wiele przekształceń, kliknij pozycję **Dodaj przekształcenie.** Do oświadczenia można zastosować maksymalnie dwa przekształcenia. Na przykład można najpierw wyodrębnić prefiks adresu e-mail `user.mail` elementu . Następnie nadaj ciągowi wielkie litery.

   ![Przekształcanie wielu oświadczeń](./media/active-directory-saml-claims-customization/sso-saml-multiple-claims-transformation.png)

Do przekształcania oświadczeń można użyć następujących funkcji.

| Funkcja | Opis |
|----------|-------------|
| **ExtractMailPrefix()** | Usuwa sufiks domeny z adresu e-mail lub głównej nazwy użytkownika. Wyodrębnia tylko pierwszą część przekazywanej nazwy użytkownika (na przykład "joe_smith" zamiast joe_smith@contoso.com ). |
| **Join()** | Tworzy nową wartość, łącząc dwa atrybuty. Opcjonalnie można użyć separatora między dwoma atrybutami. W przypadku przekształcania oświadczenia NameID sprzężenia jest ograniczone do zweryfikowanego domeny. Jeśli wybrana wartość identyfikatora użytkownika ma domenę, wyodrębni nazwę użytkownika, aby dołączyć wybraną zweryfikowaną domenę. Jeśli na przykład wybierzesz adres e-mail ( ) jako wartość identyfikatora użytkownika i wybierzesz contoso.onmicrosoft.com jako zweryfikowaną domenę, spowoduje joe_smith@contoso.com to . joe_smith@contoso.onmicrosoft.com |
| **ToLowercase()** | Konwertuje znaki wybranego atrybutu na małe litery. |
| **ToUppercase()** | Konwertuje znaki wybranego atrybutu na wielkie litery. |
| **Contains()** | Wyprowadza atrybut lub stałą, jeśli dane wejściowe są takie, jak określona wartość. W przeciwnym razie możesz określić inne dane wyjściowe, jeśli nie ma dopasowania.<br/>Jeśli na przykład chcesz emitować oświadczenie, w którym wartość jest adresem e-mail użytkownika, jeśli zawiera domenę " ", w przeciwnym razie chcesz uzyskać główną nazwę @contoso.com użytkownika. W tym celu należy skonfigurować następujące wartości:<br/>*Parametr 1 (input)*: user.email<br/>*Wartość*: " @contoso.com "<br/>Parametr 2 (dane wyjściowe): user.email<br/>Parametr 3 (dane wyjściowe, jeśli nie ma dopasowania): user.userprincipalname |
| **EndWith()** | Wyprowadza atrybut lub stałą, jeśli dane wejściowe kończą się określoną wartością. W przeciwnym razie możesz określić inne dane wyjściowe, jeśli nie ma dopasowania.<br/>Jeśli na przykład chcesz emitować oświadczenie, w którym wartość jest identyfikatorem pracownika użytkownika, jeśli identyfikator pracownika kończy się na "000", w przeciwnym razie chcesz wyprowadzić atrybut rozszerzenia. W tym celu należy skonfigurować następujące wartości:<br/>*Parametr 1 (input)*: user.employeeid<br/>*Wartość:*"000"<br/>Parametr 2 (dane wyjściowe): user.employeeid<br/>Parametr 3 (dane wyjściowe, jeśli nie ma dopasowania): user.extensionattribute1 |
| **StartWith()** | Wyprowadza atrybut lub stałą, jeśli dane wejściowe zaczynają się od określonej wartości. W przeciwnym razie możesz określić inne dane wyjściowe, jeśli nie ma dopasowania.<br/>Jeśli na przykład chcesz emitować oświadczenie, w którym wartość jest identyfikatorem pracownika użytkownika, jeśli kraj/region zaczyna się od "US", w przeciwnym razie chcesz wyprowadzić atrybut rozszerzenia. W tym celu należy skonfigurować następujące wartości:<br/>*Parametr 1 (input)*: user.country<br/>*Wartość:*"US"<br/>Parametr 2 (dane wyjściowe): user.employeeid<br/>Parametr 3 (dane wyjściowe, jeśli nie ma dopasowania): user.extensionattribute1 |
| **Extract() — po dopasowaniu** | Zwraca podciąg, gdy pasuje do określonej wartości.<br/>Jeśli na przykład wartość danych wejściowych to "Finance_BSimon", pasującą wartością jest "Finance_", dane wyjściowe oświadczenia to "BSimon". |
| **Extract() — przed dopasowaniem** | Zwraca podciąg, dopóki nie pasuje do określonej wartości.<br/>Jeśli na przykład wartość danych wejściowych to "BSimon_US", pasującą wartością jest "_US", dane wyjściowe oświadczenia to "BSimon". |
| **Extract() — między dopasowywaniem** | Zwraca podciąg, dopóki nie pasuje do określonej wartości.<br/>Jeśli na przykład wartość danych wejściowych to "Finance_BSimon_US", pierwszą pasującą wartością jest "Finanse", drugą pasującą wartością jest "US", a następnie dane wyjściowe oświadczenia to \_ \_ "BSimon". |
| **ExtractAlpha() — prefiks** | Zwraca prefiks alfabetycznej części ciągu.<br/>Jeśli na przykład wartość danych wejściowych to "BSimon_123", zwracana jest wartość "BSimon". |
| **ExtractAlpha() — sufiks** | Zwraca sufiks alfabetyczną część ciągu.<br/>Jeśli na przykład wartość danych wejściowych to "123_Simon", zwracana jest wartość "Simon". |
| **ExtractNumeric() — prefiks** | Zwraca część liczbową prefiksu ciągu.<br/>Jeśli na przykład wartość danych wejściowych to "123_BSimon", zwracana jest wartość "123". |
| **ExtractNumeric() — sufiks** | Zwraca część liczbową sufiksu ciągu.<br/>Jeśli na przykład wartość danych wejściowych to "BSimon_123", zwracana jest wartość "123". |
| **IfEmpty()** | Zwraca atrybut lub stałą, jeśli dane wejściowe mają wartość null lub są puste.<br/>Jeśli na przykład chcesz uzyskać dane wyjściowe atrybutu przechowywanego w extensionattribute, jeśli identyfikator pracownika dla danego użytkownika jest pusty. W tym celu należy skonfigurować następujące wartości:<br/>Parametr 1 (input): user.employeeid<br/>Parametr 2 (dane wyjściowe): user.extensionattribute1<br/>Parametr 3 (dane wyjściowe, jeśli nie ma dopasowania): user.employeeid |
| **IfNotEmpty()** | Zwraca atrybut lub stałą, jeśli dane wejściowe nie mają wartości null lub są puste.<br/>Jeśli na przykład chcesz uzyskać dane wyjściowe atrybutu przechowywanego w extensionattribute, jeśli identyfikator pracownika dla danego użytkownika nie jest pusty. W tym celu należy skonfigurować następujące wartości:<br/>Parametr 1(input): user.employeeid<br/>Parametr 2 (dane wyjściowe): user.extensionattribute1 |

Jeśli potrzebujesz dodatkowych przekształceń, prześlij swój pomysł na forum opinii w usłudze [Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) w kategorii *aplikacji SaaS.*

## <a name="emitting-claims-based-on-conditions"></a>Emitowanie oświadczeń na podstawie warunków

Można określić źródło oświadczenia na podstawie typu użytkownika i grupy, do której należy użytkownik. 

Typ użytkownika może być:
- **Dowolne:** wszyscy użytkownicy mogą uzyskać dostęp do aplikacji.
- **Członkowie:** natywny członek dzierżawy
- **Wszyscy goście:** użytkownik jest przejmyny z organizacji zewnętrznej z usługą Azure AD lub bez usługi Azure AD.
- **Goście usługi AAD:** użytkownik-gość należy do innej organizacji przy użyciu usługi Azure AD.
- **Goście zewnętrzni:** użytkownik-gość należy do organizacji zewnętrznej, która nie ma usługi Azure AD.


Jednym ze scenariuszy, w którym jest to przydatne, jest sytuacja, w której źródło oświadczenia jest inne dla gościa i pracownika, który uzyskuje dostęp do aplikacji. Możesz określić, że jeśli użytkownik jest pracownikiem, identyfikator NameID pochodzi z user.email, ale jeśli użytkownik jest gościem, identyfikator NameID pochodzi z user.extensionattribute1.

Aby dodać warunek oświadczenia:

1. W **Zarządzaj oświadczenie**, rozwiń oświadczenia warunki.
2. Wybierz typ użytkownika.
3. Wybierz grupy, do których powinien należeć użytkownik. Możesz wybrać maksymalnie 50 unikatowych grup dla wszystkich oświadczeń dla danej aplikacji. 
4. Wybierz **źródło,** w którym oświadczenie ma pobrać jego wartość. Możesz wybrać atrybut użytkownika z listy rozwijanej atrybutu źródłowego lub zastosować przekształcenie do atrybutu użytkownika przed emitem go jako oświadczenia.

Kolejność dodawania warunków jest ważna. Usługa Azure AD ocenia warunki od góry do dołu, aby zdecydować, którą wartość emitować w oświadczenie. Ostatnia wartość, która pasuje do wyrażenia, zostanie emitowana w oświadczenie.

Na przykład Britta Simon jest użytkownikiem-gościem w dzierżawie firmy Contoso. Należy do innej organizacji, która również używa usługi Azure AD. W przypadku poniższej konfiguracji aplikacji Fabrikam, gdy Britta spróbuje zalogować się do firmy Fabrikam, platforma tożsamości firmy Microsoft oceni warunki w następujący sposób.

Najpierw platforma tożsamości firmy Microsoft sprawdza, czy typ użytkownika Britta to `All guests` . Ponieważ jest to prawdziwe, platforma tożsamości firmy Microsoft przypisuje źródło oświadczenia do `user.extensionattribute1` . Po drugie platforma tożsamości firmy Microsoft sprawdza, czy typ użytkownika Britta to , ponieważ jest to również prawdziwe, platforma tożsamości firmy Microsoft przypisuje źródło oświadczenia `AAD guests` do usługi `user.mail` . Na koniec oświadczenie jest emitowane z wartością `user.mail` dla Britta.

![Konfiguracja warunkowa oświadczeń](./media/active-directory-saml-claims-customization/sso-saml-user-conditional-claims.png)

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie aplikacją w usłudze Azure AD](../manage-apps/what-is-application-management.md)
* [Konfigurowanie aplikacji logowania pojedynczego, które nie znajdują się w galerii aplikacji usługi Azure AD](../manage-apps/configure-saml-single-sign-on.md)
* [Rozwiązywanie problemów z logowaniem jednokrotnym opartym na języku SAML](../manage-apps/debug-saml-sso-issues.md)
