---
title: Samouczek dotyczący konfigurowania Azure Active Directory B2C za pomocą aplikacji Nevis
titleSuffix: Azure AD B2C
description: Dowiedz się, jak zintegrować uwierzytelnianie Azure AD B2C z nevis w celu uwierzytelniania bez hasła
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/23/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 42e244249ecb0539637918ae2439bdb4f5da4b38
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588483"
---
# <a name="tutorial-to-configure-nevis-with-azure-active-directory-b2c-for-passwordless-authentication"></a>Samouczek dotyczący konfigurowania aplikacji Nevis przy użyciu Azure Active Directory B2C do uwierzytelniania bez hasła

Z tego przykładowego samouczka dowiesz się, jak rozszerzyć usługę Azure AD B2C  [nevis](https://www.nevis.net/en/solution/authentication-cloud) w celu włączenia uwierzytelniania bez hasła. Firma Nevis oferuje w pełni markowe środowisko użytkownika końcowego oparte na urządzeniach przenośnych z aplikacją Nevis Access, które zapewnia silne uwierzytelnianie klienta i spełnia wymagania dotyczące transakcji z dyrektywy 2 (PSD2) dotyczące usług payment services.

## <a name="prerequisites"></a>Wymagania wstępne

Do rozpoczęcia pracy potrzebne są:

- Konto wersji [próbnej](https://www.nevis-security.com/aadb2c/) Nevis

- Subskrypcja usługi Azure AD. Jeśli go nie masz, uzyskaj [bezpłatne konto](https://azure.microsoft.com/free/).

- Dzierżawa [Azure AD B2C połączona](./tutorial-create-tenant.md) z subskrypcją platformy Azure.

- Skonfigurowano Azure AD B2C do korzystania z [zasad niestandardowych,](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)jeśli chcesz zintegrować aplikację Nevis z przepływem zasad rejestracji.

## <a name="scenario-description"></a>Opis scenariusza

W tym scenariuszu dodasz w pełni markową aplikację dostępu do aplikacji back-end w celu uwierzytelniania bez hasła. Następujące składniki to rozwiązanie:

- Dzierżawa Azure AD B2C z połączonymi zasadami logowania i rejestracji w załocie
- Wystąpienie Nevis i jego interfejs API REST w celu Azure AD B2C
- Twoja aplikacja dostępu z marką

Diagram przedstawia implementację.

![Przepływ logowania za pomocą hasła wysokiego poziomu za pomocą Azure AD B2C i Nevis](./media/partner-nevis/nevis-architecture-diagram.png)

|Krok | Opis |
|:-----| :-----------|
| 1. | Użytkownik próbuje zalogować się lub zarejestrować w aplikacji za pośrednictwem Azure AD B2C logowania i rejestracji.
| 2. | Podczas rejestracji aplikacja Nevis Access jest rejestrowana na urządzeniu użytkownika przy użyciu kodu QR. Klucz prywatny jest generowany na urządzeniu użytkownika i używany do podpisywania żądań użytkownika.
| 3. |  Azure AD B2C korzysta z profilu technicznego RESTful, aby rozpocząć logowanie przy użyciu rozwiązania Nevis.
| 4. | Żądanie logowania jest wysyłane do aplikacji dostępu jako wiadomość wypychana, kod QR lub link bezpośredni.
| 5. | Użytkownik zatwierdza próbę logowania przy użyciu danych biometrycznych. Następnie do użytkownika Nevis jest zwracany komunikat, który weryfikuje logowanie przy użyciu przechowywanego klucza publicznego.
| 6. | Azure AD B2C ostatnie żądanie do użytkownika Nevis w celu potwierdzenia pomyślnego ukończenia logowania.
| 7. |Na podstawie komunikatu o sukcesie/niepowodzeniu Azure AD B2C udzielono/odmówiono dostępu do aplikacji.

## <a name="integrate-your-azure-ad-b2c-tenant"></a>Integrowanie dzierżawy Azure AD B2C dzierżawy

### <a name="onboard-to-nevis"></a>Dołączanie do nevis 

[Zarejestruj się, aby uzyskać konto Nevis.](https://www.nevis-security.com/aadb2c/)
Otrzymasz dwie wiadomości e-mail:

1. Powiadomienie o koncie zarządzania

2. Zaproszenie do aplikacji mobilnej.

### <a name="add-your-azure-ad-b2c-tenant-to-your-nevis-account"></a>Dodawanie dzierżawy Azure AD B2C dzierżawy do konta nevis

1. Z próbnego adresu e-mail konta zarządzania Nevis skopiuj klucz zarządzania do schowka.

2. Otwórz https://console.nevis.cloud/ w przeglądarce.

3. Zaloguj się do konsoli zarządzania przy użyciu swojego klucza.

4. Wybierz **pozycję Dodaj wystąpienie**

5. Wybierz nowo utworzone wystąpienie, aby je otworzyć.

6. Na bocznym pasku nawigacyjnym wybierz pozycję **Integracje niestandardowe**

7. Wybierz **pozycję Dodaj integrację niestandardową.**

8. W polach Nazwa integracji wprowadź Azure AD B2C dzierżawy.

9. W polu Adres URL/domena wprowadź `https://yourtenant.onmicrosoft.com`

10. Wybierz pozycję **Dalej**

>[!NOTE]
>Później będziesz potrzebować tokenu dostępu Nevis.

11. Kliknij **Gotowe**.

### <a name="install-the-nevis-access-app-on-your-phone"></a>Instalowanie aplikacji Nevis Access na telefonie

1. W wiadomości e-mail dotyczącej wersji próbnej aplikacji mobilnej Nevis otwórz zaproszenie **do aplikacji Test Flight.**

2. Zainstaluj aplikację.

3. Postępuj zgodnie z instrukcjami, aby zainstalować aplikację Nevis Access.

### <a name="integrate-azure-ad-b2c-with-nevis"></a>Integracja Azure AD B2C z nevis

1. Otwórz witrynę [Azure Portal](https://portal.azure.com/).

2. Przejdź do swojej Azure AD B2C dzierżawy. Upewnij się, że wybrano odpowiednią dzierżawę, ponieważ dzierżawa Azure AD B2C zazwyczaj znajduje się w oddzielnej dzierżawie.

3. W menu wybierz pozycję **Identity Experience Framework (IEF)**

4. Wybieranie **kluczy zasad**

5. Wybierz **pozycję** Dodaj i utwórz nowy klucz z następującymi ustawieniami:

      a. Wybieranie **opcji Ręcznie w** opcjach

      b. Ustaw wartość pola Nazwa **na AuthCloudAccessToken**

      c. Wklej wcześniej zapisany **token dostępu Nevis** w polu Klucz tajny

      d. W przypadku opcji Użycie klucza wybierz **pozycję Szyfrowanie**

      e. Wybierz pozycję **Utwórz**

### <a name="configure-and-upload-the-nevishtml-to-azure-blob-storage"></a>Konfigurowanie i przekazywanie nevis.html do usługi Azure Blob Storage

1. W środowisku tożsamości (IDE) przejdź do folderu [**zasad.**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy)

2. Otwórz [**pliknevis.html.**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/nevis.html)

3. Zastąp  **authentication_cloud_url** adresem URL konsoli administracyjnej nevis — `https://<instance_id>.mauth.nevis.cloud` .

4. **Zapisz** zmiany w pliku.

5. Postępuj zgodnie [z instrukcjami](./customize-ui-with-html.md#2-create-an-azure-blob-storage-account) i **przekażnevis.html** do usługi Azure Blob Storage.

6. Postępuj zgodnie z [instrukcjami](./customize-ui-with-html.md#3-configure-cors) i włącz udostępnianie zasobów między źródłami (CORS) dla tego pliku.

7. Po zakończeniu przekazywania i włączeniu funkcji CORS wybierz **nevis.html** pliku na liście.

8. Na karcie **Przegląd** obok adresu **URL** wybierz **ikonę kopiowania linku.**

9. Otwórz link na nowej karcie przeglądarki, aby upewnić się, że jest w nim wyświetlane szare pole.

>[!NOTE]
>Link do obiektu blob będzie potrzebny później.

### <a name="customize-your-trustframeworkbasexml"></a>Dostosowywanie TrustFrameworkBase.xml

1. W swoim idee przejdź do folderu [**zasad.**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy)

2. Otwórz [**TrustFrameworkBase.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkBase.xml) plik.

3. Zastąp **swoją dzierżawę** nazwą konta dzierżawy platformy Azure w **tenantId**.

4. Zastąp **swoją dzierżawę nazwą** konta dzierżawy platformy Azure w **publicPolicyURI**.

5. Zastąp **wszystkie authentication_cloud_url** adresem URL konsoli administracyjnej nevis

6. **Zapisz** zmiany w pliku.

### <a name="customize-your-trustframeworkextensionsxml"></a>Dostosowywanie TrustFrameworkExtensions.xml

1. W swoim idee przejdź do [**folderu zasad.**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy)

2. Otwórz [**TrustFrameworkExtensions.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkExtensions.xml) plik.

3. Zastąp **swoją dzierżawę** nazwą konta dzierżawy platformy Azure w **tenantId**.

4. Zastąp **swoją dzierżawę nazwą** konta dzierżawy platformy Azure w **publicPolicyURI**.

5. W **obszarze BasePolicy** w obszarze **TenantId** zastąp również _wartość yourtenant_ nazwą konta dzierżawy platformy Azure.

6. W **obszarze BuildingBlocks** zastąp **tekst LoadUri** adresem URL linku obiektu blob _nevis.html_ na koncie magazynu obiektów blob.

7. **Zapisz** plik.

### <a name="customize-your-signuporsigninxml"></a>Dostosowywanie SignUpOrSignin.xml

1. W swoim idee przejdź [**do**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) folderu zasad.

2. Otwórz [**SignUpOrSignin.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/SignUpOrSignin.xml) plik.

3. Zastąp **swoją dzierżawę** nazwą konta dzierżawy platformy Azure w **tenantId**.

4. Zastąp **swoją dzierżawę nazwą** konta dzierżawy platformy Azure w **publicPolicyUri.**

5. W **obszarze BasePolicy** w obszarze **TenantId** zastąp również **wartość yourtenant** nazwą konta dzierżawy platformy Azure.

6. **Zapisz** plik.

### <a name="upload-your-custom-policies-to-azure-ad-b2c"></a>Przekazywanie zasad niestandardowych do Azure AD B2C

1. Otwórz swoją [Azure AD B2C dzierżawy.](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview)

2. Wybierz **Identity Experience Framework**.

3. Wybierz **pozycję Przekaż zasady niestandardowe.**

4. Wybierz **TrustFrameworkBase.xml** pliku, który został zmodyfikowany.

5. Zaznacz pole **wyboru Zastąp zasady niestandardowe, jeśli już istnieją.**
6. Wybierz pozycję **Przekaż**.

7. Powtórz kroki 5 i 6 dla **TrustFrameworkExtensions.xml**.

8. Powtórz kroki 5 i 6 dla **SignUpOrSignin.xml**.

## <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

### <a name="test-account-creation-and-nevis-access-app-setup"></a>Testowanie tworzenia konta i konfiguracji aplikacji Nevis Access

1. Otwórz swoją [Azure AD B2C dzierżawy.](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview)

2. Wybierz **Identity Experience Framework**.

3. Przewiń w dół do opcji Zasady niestandardowe i **wybierz pozycję B2C_1A_signup_signin**.

4. Wybierz **pozycję Uruchom teraz.**

5. W oknie podręcznym wybierz pozycję **Zarejestruj się teraz.**

6. Dodaj swój adres e-mail.

7. Wybierz **pozycję Wyślij kod weryfikacyjny**.

8. Skopiuj kod weryfikacyjny z wiadomości e-mail.

9. Wybierz pozycję **Weryfikuj**.

10. Wypełnij formularz przy użyciu nowego hasła i nazwy wyświetlanej.

11. Wybierz przycisk **Utwórz**.

12. Zostaniesz wyświetlony na stronie skanowania kodu QR.

13. Na telefonie otwórz aplikację **Nevis Access**.

14. Wybierz **pozycję Face ID.**

15. Gdy na ekranie będzie się **dzieje, że rejestracja aplikacji Authenticator powiodła się,** wybierz pozycję **Kontynuuj.**

16. Na telefonie ponownie uwierzytelnij się twarzą.

17. Zostanie wyświetlona strona docelowa [jwt.ms,](https://jwt.ms) która wyświetla szczegóły zdekodowanych tokenów.

### <a name="test-the-pure-passwordless-sign-in"></a>Testowanie logowania bez hasła

1. W **Identity Experience Framework** wybierz pozycję **B2C_1A_signup_signin**.

2. Wybierz **pozycję Uruchom teraz.**

3. W oknie podręcznym wybierz pozycję **Uwierzytelnianie bez hasła.**

4. Wprowadź adres e-mail.

5. Wybierz opcję **Kontynuuj**.

6. Na telefonie w powiadomieniach wybierz powiadomienie **aplikacji Nevis Access**.

7. Uwierzytelnianie za pomocą twarzy.

8. Zostaniesz automatycznie przekierowyny do [jwt.ms](https://jwt.ms) docelowej, która wyświetla Twoje tokeny.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowe informacje, zapoznaj się z następującymi artykułami

- [Zasady niestandardowe w usłudze Azure AD B2C](./custom-policy-overview.md)

- [Wprowadzenie do zasad niestandardowych w Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
