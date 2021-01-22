---
title: Samouczek konfigurowania Azure Active Directory B2C przy użyciu programu Nevis
titleSuffix: Azure AD B2C
description: Dowiedz się, jak zintegrować uwierzytelnianie Azure AD B2C przy użyciu usługi Nevis do uwierzytelniania bez hasła
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/23/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 282ec6a25dc381dc51f28534d272bae57d2e792e
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98674997"
---
# <a name="tutorial-to-configure-nevis-with-azure-active-directory-b2c-for-passwordless-authentication"></a>Samouczek konfigurowania programu Nevis z Azure Active Directory B2C do uwierzytelniania bezhaseł

W tym przykładowym samouczku dowiesz się, jak rozciągnąć Azure AD B2C za pomocą programu  [Nevis](https://www.nevis.net/solution/authentication-cloud) , aby umożliwić uwierzytelnianie bez haseł. Usługa Nevis zapewnia mobilny, w pełni oznakowany interfejs użytkownika końcowego z dostępem do aplikacji firmy Nevis, aby zapewnić silne uwierzytelnianie klienta i zgodność z wymaganiami transakcji dyrektywy 2 (PSD2).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, musisz:

- [Konto wersji próbnej](https://www.nevis-security.com/aadb2c/) usługi Nevis

- Subskrypcja usługi Azure AD. Jeśli go nie masz, uzyskaj [bezpłatne konto](https://azure.microsoft.com/free/).

- [Dzierżawa Azure AD B2C](./tutorial-create-tenant.md) , która jest połączona z subskrypcją platformy Azure.

- Skonfigurowanie środowiska Azure AD B2C na potrzeby używania [zasad niestandardowych](./custom-policy-get-started.md), jeśli chcesz zintegrować go z przepływem zasad rejestracji.

## <a name="scenario-description"></a>Opis scenariusza

W tym scenariuszu Dodaj w pełni oznakowaną aplikację dostępu do aplikacji zaplecza w celu uwierzytelniania bezhaseł. Następujące składniki tworzą rozwiązanie:

- Dzierżawa Azure AD B2C z połączonymi zasadami logowania i rejestracji na zapleczu
- Nevis wystąpienie i interfejs API REST, aby ulepszyć Azure AD B2C
- Twoja aplikacja dostępu markowego

Na diagramie przedstawiono implementację.

![Przepływ logowania przy użyciu hasła wysokiego poziomu z Azure AD B2C i Nevis](./media/partner-nevis/nevis-architecture-diagram.png)

|Krok | Opis |
|:-----| :-----------|
| 1. | Użytkownik próbuje zalogować się do aplikacji lub zarejestrować się w niej za pośrednictwem Azure AD B2C zasad logowania i rejestrowania.
| 2. | Podczas rejestracji aplikacja z dostępem do usługi Nevis jest zarejestrowana na urządzeniu użytkownika przy użyciu kodu QR. Klucz prywatny jest generowany na urządzeniu użytkownika i służy do podpisywania żądań użytkownika.
| 3. |  Azure AD B2C używa profilu technicznego RESTful, aby rozpocząć Logowanie przy użyciu rozwiązania Nevis.
| 4. | Żądanie logowania jest wysyłane do aplikacji dostępu jako wiadomość wypychana, kod QR lub jako głębokie łącze.
| 5. | Użytkownik zatwierdza próbę logowania przy użyciu biometrii. Następnie zostanie zwrócony komunikat do programu Nevis, który weryfikuje Logowanie przy użyciu zapisanego klucza publicznego.
| 6. | Azure AD B2C wysyła od ostatniego żądania do programu Nevis, aby upewnić się, że logowanie zostało pomyślnie zakończone.
| 7. |W oparciu o komunikat o powodzeniu/niepowodzeniu z Azure AD B2C użytkownik otrzymuje/odmówił dostępu do aplikacji.

## <a name="integrate-your-azure-ad-b2c-tenant"></a>Integracja dzierżawy Azure AD B2C

### <a name="onboard-to-nevis"></a>Dołączanie do programu Nevis 

[Utwórz konto w usłudze Nevis](https://www.nevis-security.com/aadb2c/).
Otrzymasz dwie wiadomości e-mail:

1. Powiadomienie o koncie zarządzania

2. Zaproszenie aplikacji mobilnej.

### <a name="add-your-azure-ad-b2c-tenant-to-your-nevis-account"></a>Dodawanie dzierżawy Azure AD B2C do konta programu Nevis

1. W wiadomości e-mail z wersją próbną konta zarządzania usługi Nevis skopiuj swój klucz zarządzania do Schowka.

2. Otwórz https://console.nevis.cloud/ w przeglądarce.

3. Zaloguj się do konsoli zarządzania przy użyciu klucza.

4. Wybierz pozycję **Dodaj wystąpienie**

5. Wybierz nowo utworzone wystąpienie, aby je otworzyć.

6. Na pasku nawigacyjnym po stronie wybierz opcję **integracji niestandardowe**

7. Wybierz pozycję **Dodaj integrację niestandardową**.

8. W obszarze Nazwa integracji wprowadź nazwę dzierżawy Azure AD B2C.

9. W obszarze adres URL/Domena wprowadź `https://yourtenant.onmicrosoft.com`

10. Wybierz pozycję **Dalej**

>[!NOTE]
>Token dostępu do Nevis będzie potrzebny później.

11. Kliknij **Gotowe**.

### <a name="install-the-nevis-access-app-on-your-phone"></a>Zainstaluj aplikację "Nevis Access" na telefonie

1. Na stronie wiadomości e-mail firmy Nevis Mobile App Trial otwórz zaproszenie **aplikacji testowej** .

2. Zainstaluj aplikację.

3. Postępuj zgodnie z instrukcjami podanymi w celu zainstalowania aplikacji Nevis Access.

### <a name="integrate-azure-ad-b2c-with-nevis"></a>Integracja Azure AD B2C z usługą Nevis

1. Otwórz witrynę [Azure Portal](https://portal.azure.com/).

2. Przełącz się do dzierżawy Azure AD B2C. Upewnij się, że wybrano właściwą dzierżawę, ponieważ dzierżawa Azure AD B2C zwykle znajduje się w osobnej dzierżawie.

3. Z menu wybierz pozycję **Identity Experience Framework (IEF)**

4. Wybierz **klucze zasad**

5. Wybierz pozycję **Dodaj** i Utwórz nowy klucz z następującymi ustawieniami:

      a. Wybieranie opcji **ręcznych** w opcjach

      b. Ustaw nazwę na **AuthCloudAccessToken**

      c. Wklej wcześniej zapisany **token dostępu "Nevis** " w polu tajny

      d. Dla opcji użycie klucza wybierz **szyfrowanie**

      e. Wybierz pozycję **Utwórz**

### <a name="configure-and-upload-the-nevishtml-to-azure-blob-storage"></a>Konfigurowanie i przekazywanie nevis.html do usługi Azure Blob Storage

1. W środowisku programu Identity (IDE) przejdź do folderu [**Policy**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) .

2. Otwórz plik  [**nevis.html**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/nevis.html) .

3. Zastąp  **authentication_cloud_url** adresem URL konsoli administracyjnej programu Nevis — `https://<instance_id>.mauth.nevis.cloud` .

4. **Zapisz** zmiany w pliku.

5. Postępuj zgodnie z [instrukcjami](./customize-ui-with-html.md#2-create-an-azure-blob-storage-account) i przekaż plik **nevis.html** do magazynu obiektów blob platformy Azure.

6. Postępuj zgodnie z [instrukcjami](./customize-ui-with-html.md#3-configure-cors) i Włącz współużytkowanie zasobów między źródłami (CORS) dla tego pliku.

7. Po zakończeniu przekazywania i włączeniu funkcji CORS zaznacz plik **nevis.html** na liście.

8. Na karcie **Przegląd** obok **adresu URL** wybierz ikonę **Kopiuj link** .

9. Otwórz link na nowej karcie przeglądarki, aby upewnić się, że jest wyświetlany szary prostokąt.

>[!NOTE]
>Łącze obiektu BLOB będzie potrzebne później.

### <a name="customize-your-trustframeworkbasexml"></a>Dostosowywanie TrustFrameworkBase.xml

1. W środowisku IDE przejdź do folderu [**Policy**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) .

2. Otwórz plik [**TrustFrameworkBase.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkBase.xml) .

3. Zastąp **yourtenant** nazwą konta dzierżawy platformy Azure w **TenantId**.

4. Zastąp **yourtenant** nazwą konta dzierżawy platformy Azure w **PublicPolicyURI**.

5. Zamień wszystkie wystąpienia **authentication_cloud_url** na adres URL konsoli administracyjnej programu Nevis

6. **Zapisz** zmiany w pliku.

### <a name="customize-your-trustframeworkextensionsxml"></a>Dostosowywanie TrustFrameworkExtensions.xml

1. W środowisku IDE przejdź do folderu [**Policy**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) .

2. Otwórz plik [**TrustFrameworkExtensions.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkExtensions.xml) .

3. Zastąp **yourtenant** nazwą konta dzierżawy platformy Azure w **TenantId**.

4. Zastąp **yourtenant** nazwą konta dzierżawy platformy Azure w **PublicPolicyURI**.

5. W obszarze **BasePolicy**, w **TenantId**, należy również zastąpić _Yourtenant_ nazwą konta dzierżawy platformy Azure.

6. W obszarze **BuildingBlocks** Zastąp wartość **LoadUri** adresem URL linku obiektu BLOB _nevis.html_ na koncie magazynu obiektów BLOB.

7. **Zapisz** plik.

### <a name="customize-your-signuporsigninxml"></a>Dostosowywanie SignUpOrSignin.xml

1. W środowisku IDE przejdź do folderu [**Policy**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) .

2. Otwórz plik [**SignUpOrSignin.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/SignUpOrSignin.xml) .

3. Zastąp **yourtenant** nazwą konta dzierżawy platformy Azure w **TenantId**.

4. Zastąp **yourtenant** nazwą konta dzierżawy platformy Azure w **PublicPolicyUri**.

5. W obszarze **BasePolicy**, w **TenantId**, należy również zastąpić **Yourtenant** nazwą konta dzierżawy platformy Azure.

6. **Zapisz** plik.

### <a name="upload-your-custom-policies-to-azure-ad-b2c"></a>Przekaż zasady niestandardowe do Azure AD B2C

1. Otwórz stronę główną [dzierżawy Azure AD B2C](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview) .

2. Wybierz pozycję **platforma obsługi tożsamości**.

3. Wybierz pozycję **Przekaż zasady niestandardowe**.

4. Wybierz zmodyfikowany plik **TrustFrameworkBase.xml** .

5. Zaznacz pole wyboru **Zastąp zasady niestandardowe, jeśli już istnieje** .
6. Wybierz pozycję **Przekaż**.

7. Powtórz kroki 5 i 6 dla **TrustFrameworkExtensions.xml**.

8. Powtórz kroki 5 i 6 dla **SignUpOrSignin.xml**.

## <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

### <a name="test-account-creation-and-nevis-access-app-setup"></a>Tworzenie konta testowego i Nevis konfiguracja aplikacji dostępu

1. Otwórz stronę główną [dzierżawy Azure AD B2C](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview) .

2. Wybierz pozycję **platforma obsługi tożsamości**.

3. Przewiń w dół do zasad niestandardowych i wybierz pozycję **B2C_1A_signup_signin**.

4. Wybierz pozycję **Uruchom teraz**.

5. W oknie podręcznym wybierz pozycję **zarejestruj się teraz**.

6. Dodaj swój adres e-mail.

7. Wybierz pozycję **Wyślij kod weryfikacyjny**.

8. Skopiuj kod weryfikacyjny z wiadomości e-mail.

9. Wybierz pozycję **Weryfikuj**.

10. Wypełnij formularz nowymi hasłami i nazwą wyświetlaną.

11. Wybierz przycisk **Utwórz**.

12. Nastąpi przekierowanie do strony skanowania kodu QR.

13. Na telefonie Otwórz **aplikację Nevis Access**.

14. Wybierz pozycję **Identyfikator kroju**.

15. Gdy ekran informuje o **pomyślnym zarejestrowaniu uwierzytelnienia**, wybierz pozycję **Kontynuuj**.

16. Na telefonie ponownie Uwierzytelnij się przy użyciu Twojej platformy.

17. Nastąpi przekierowanie do strony docelowej [JWT.MS](https://jwt.ms) , która wyświetla dekodowane szczegóły tokenu.

### <a name="test-the-pure-passwordless-sign-in"></a>Przetestuj czyste logowanie bezhasła

1. W obszarze **Struktura środowiska tożsamości** wybierz **B2C_1A_signup_signin**.

2. Wybierz pozycję **Uruchom teraz**.

3. W oknie podręcznym wybierz pozycję **uwierzytelnianie bezhasła**.

4. Wprowadź adres e-mail.

5. Wybierz opcję **Kontynuuj**.

6. Na telefonie w obszarze powiadomienia wybierz pozycję **Nevis dostęp do aplikacji powiadomienia**.

7. Uwierzytelnianie za pomocą Twojej platformy.

8. Nastąpi automatyczne przełączenie na stronę docelową [JWT.MS](https://jwt.ms) , która wyświetla Twoje tokeny.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowe informacje, zapoznaj się z następującymi artykułami

- [Zasady niestandardowe w usłudze Azure AD B2C](./custom-policy-overview.md)

- [Wprowadzenie do zasad niestandardowych w Azure AD B2C](./custom-policy-get-started.md?tabs=applications)