---
title: Trusona i Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Dowiedz się, jak dodać Trusona jako dostawcę tożsamości w Azure AD B2C, aby umożliwić uwierzytelnianie bezhaseł.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a0d5b369e1c143b3df4157329bcf7d3a3f7142d7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87489473"
---
# <a name="integrating-trusona-with-azure-active-directory-b2c"></a>Integrowanie Trusona z Azure Active Directory B2C

Trusona to dostawca niezależnego dostawcy oprogramowania, który pomaga zabezpieczyć Logowanie przez włączenie uwierzytelniania bez hasła, uwierzytelniania wieloskładnikowego i skanowania licencji cyfrowych. W tym artykule dowiesz się, jak dodać Trusona jako dostawcę tożsamości w Azure AD B2C, aby umożliwić uwierzytelnianie bezhaseł.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, musisz:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* [Dzierżawa Azure AD B2C](tutorial-create-tenant.md) , która jest połączona z subskrypcją platformy Azure.
* [Konto próbne](https://www.trusona.com/aadb2c) o godzinie Trusona

## <a name="scenario-description"></a>Opis scenariusza

W tym scenariuszu Trusona działa jako dostawca tożsamości dla Azure AD B2C w celu włączenia uwierzytelniania bezhaseł. Następujące składniki tworzą rozwiązanie:

* Azure AD B2C połączone zasady logowania i rejestrowania
* Trusona dodany do Azure AD B2C jako dostawca tożsamości
* Aplikacja Trusona do pobrania

![Diagram architektury Trusona](media/partner-trusona/trusona-architecture-diagram.png)

| Krok | Opis |
|------|------|
|1     | Użytkownik próbuje zalogować się do aplikacji lub zarejestrować się w niej. Użytkownik jest uwierzytelniany za pośrednictwem Azure AD B2C zasad rejestracji i logowania. Podczas rejestracji zostanie użyty wcześniej zweryfikowany adres e-mail użytkownika z aplikacji Trusona.     |
|2     | Usługa Azure B2C przekierowuje użytkownika do dostawcy tożsamości usługi Trusona OpenID Connect Connect (OIDC) przy użyciu niejawnego przepływu.     |
|3     | W przypadku logowania za pomocą komputera stacjonarnego Trusona wyświetla unikatowy, bezstanowy, animowany i dynamiczny kod QR do skanowania przy użyciu aplikacji Trusona. W przypadku logowań opartych na urządzeniach przenośnych Trusona może otworzyć aplikację Trusona przy użyciu linku głębokiego. Te dwie metody są używane w przypadku urządzeń i ostatecznie odnajdowania użytkowników.     |
|4     | Użytkownik skanuje wyświetlany kod QR za pomocą aplikacji Trusona.     |
|5     | Konto użytkownika znajduje się w usłudze w chmurze Trusona i trwa przygotowywanie uwierzytelniania.     |
|6     | Usługa w chmurze Trusona wystawia użytkownikowi wyzwanie uwierzytelniania za pośrednictwem powiadomienia wypychanego wysłanego do aplikacji Trusona:<br>a. Użytkownik jest monitowany o wyzwanie uwierzytelniania. <br> b. Użytkownik zdecyduje się na zaakceptowanie lub odrzucenie wyzwania. <br> c. Użytkownik jest proszony o użycie zabezpieczeń systemu operacyjnego (na przykład biometryczny, kod dostępu, kod PIN lub wzorzec) w celu potwierdzenia i podpisania wyzwania przy użyciu klucza prywatnego w bezpiecznym środowisku wykonywania enklawy/zaufania. <br> d. Aplikacja Trusona generuje dynamiczny ładunek do ponownego uruchomienia na podstawie parametrów uwierzytelniania w czasie rzeczywistym. <br> e. Cała odpowiedź jest podpisana (przez drugi czas) przez klucz prywatny w bezpiecznym środowisku wykonywania enklawy/zaufanego i zwracana do usługi Trusona w chmurze w celu weryfikacji.      |
|7     |  Usługa w chmurze Trusona przekierowuje użytkownika z powrotem do aplikacji inicjującej za pomocą id_token. Azure AD B2C weryfikuje id_token przy użyciu konfiguracji OpenID Connect opublikowanej przez program Trusona zgodnie z konfiguracją podczas instalacji dostawcy tożsamości.    |
|  |  |

## <a name="onboard-with-trusona"></a>Dołączanie do Trusona

1. Wypełnij [formularz](https://www.trusona.com/aadb2c) , aby utworzyć konto Trusona i rozpocząć pracę.

2. Pobierz aplikację mobilną Trusona ze sklepu App Store. Zainstaluj aplikację i Zarejestruj swój adres e-mail.

3. Zweryfikuj swój adres e-mail za pośrednictwem bezpiecznego linku Magic wysyłanego przez oprogramowanie.  

4. Przejdź do [pulpitu nawigacyjnego dewelopera Trusona](https://dashboard.trusona.com) w celu samoobsługi.

5. Wybierz pozycję **jestem gotowy** i Uwierzytelnij się przy użyciu aplikacji Trusona.

6. W panelu nawigacyjnym po lewej stronie wybierz opcję **integracje OIDC**.

7. Wybierz pozycję **Utwórz integrację z usługą OpenID Connect Connect**.

8. Podaj wybraną **nazwę** i Użyj dostarczonych wcześniej informacji o domenie (na przykład contoso) w **polu hosta przekierowania klienta**.  

   > [!NOTE]
   > Początkowa nazwa domeny Azure Active Directory jest używana jako host przekierowania klienta.

9. Postępuj zgodnie z instrukcjami w [przewodniku integracji Trusona](https://docs.trusona.com/integrations/aad-b2c-integration/). Po wyświetleniu monitu użyj początkowej nazwy domeny (np. contoso), która została określona w poprzednim kroku.  

## <a name="integrate-with-azure-ad-b2c"></a>Integracja z usługą Azure AD B2C

### <a name="add-a-new-identity-provider"></a>Dodawanie nowego dostawcy tożsamości

> [!NOTE]
> Jeśli jeszcze tego nie zrobiono, [Utwórz dzierżawę Azure AD B2C](tutorial-create-tenant.md) , która jest połączona z subskrypcją platformy Azure.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.

2. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.

3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.

4. Przejdź do **pulpitu nawigacyjnego**  >  **Azure Active Directory B2C**  >  **dostawcy tożsamości**.

3. Wybierz pozycję **dostawcy tożsamości**.

4. Wybierz pozycję **Dodaj**.

### <a name="configure-an-identity-provider"></a>Konfigurowanie dostawcy tożsamości  

1. Wybierz pozycję **dostawca tożsamości typ**  >  **OpenID Connect Connect (wersja zapoznawcza)**.

2. Wypełnij formularz, aby skonfigurować dostawcę tożsamości:  

   | Właściwość | Wartość  |
   | :--- | :--- |
   | Adres URL metadanych | `https://gateway.trusona.net/oidc/.well-known/openid-configuration`|
   | Identyfikator klienta | Zostanie wysłana wiadomość e-mail od Trusona |
   | Zakres | Adres e-mail profilu OpenID Connect |
   | Typ odpowiedzi | Id_token |
   | Tryb odpowiedzi  | Form_post |

3. Kliknij przycisk **OK**.  

4. Wybierz pozycję **Mapuj oświadczenia tego dostawcy tożsamości**.  

5. Wypełnij formularz, aby zamapować dostawcę tożsamości:

   | Właściwość | Wartość  |
   | :--- | :--- |
   | UserID | Sub  |
   | Nazwa wyświetlana | pseudonim |
   | Imię | given_name |
   | Nazwisko | Family_name |
   | Tryb odpowiedzi | poczta e-mail |

6. Wybierz **przycisk OK** , aby zakończyć instalację nowego dostawcy tożsamości OIDC.

### <a name="create-a-user-flow-policy"></a>Tworzenie zasad przepływu użytkownika

Teraz powinien być widoczny Trusona jako **Nowy dostawca tożsamości programu OpenID Connect Connect** wymieniony w ramach dostawców tożsamości B2C.

1. W dzierżawie Azure AD B2C w obszarze **zasady**wybierz pozycję **przepływy użytkownika**.

1. Wybierz pozycję **Nowy przepływ użytkownika**.

1. Wybierz pozycję **zarejestruj się i zaloguj**, wybierz wersję, a następnie wybierz pozycję **Utwórz**.

1. Podaj **nazwę** zasad.

1. W sekcji **dostawcy tożsamości** wybierz nowo utworzonego **dostawcę tożsamości Trusona**.

   > [!NOTE]
   > Ponieważ Trusona jest z własnym czynnikiem wieloskładnikowym, najlepszym rozwiązaniem jest pozostawienie wyłączonego uwierzytelniania wieloskładnikowego.

1. Wybierz przycisk **Utwórz**.

1. W obszarze **atrybuty użytkownika i oświadczenia**wybierz pozycję **Pokaż więcej**. W formularzu wybierz co najmniej jeden atrybut, który został określony podczas instalacji dostawcy tożsamości w poprzedniej sekcji.

1. Kliknij przycisk **OK**.  

### <a name="test-the-policy"></a>Testowanie zasad

1. Wybierz nowo utworzone zasady.

2. Wybierz pozycję **Uruchom przepływ użytkownika**.

3. W formularzu wprowadź adres URL odpowiadający.

4. Wybierz pozycję **Uruchom przepływ użytkownika**. Należy przekierować do bramy Trusona OIDC. Na bramie Trusona Skanuj wyświetlany kod Secure QR za pomocą aplikacji Trusona lub z aplikacją niestandardową przy użyciu zestawu SDK Trusona Mobile.

5. Po przeskanowaniu bezpiecznego kodu QR należy przekierować do adresu URL odpowiedzi zdefiniowanego w kroku 3.

## <a name="next-steps"></a>Następne kroki  

Aby uzyskać dodatkowe informacje, zapoznaj się z następującymi artykułami:

- [Zasady niestandardowe w usłudze Azure AD B2C](custom-policy-overview.md)

- [Wprowadzenie do zasad niestandardowych w AAD B2C](custom-policy-get-started.md?tabs=applications)
