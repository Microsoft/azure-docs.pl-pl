---
title: Samouczek konfigurowania Azure Active Directory B2C przy użyciu HYPR
titleSuffix: Azure AD B2C
description: Samouczek dotyczący konfigurowania Azure Active Directory B2C przy użyciu Hypr na potrzeby silnego uwierzytelniania klienta bez hasła
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/27/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 4f6b09061a4aa98824e176af55efcedfab3df48c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89051920"
---
# <a name="tutorial-for-configuring-hypr-with-azure-active-directory-b2c"></a>Samouczek dotyczący konfigurowania HYPR z Azure Active Directory B2C

W tym przykładowym samouczku przedstawiono wskazówki dotyczące konfigurowania Azure AD B2C za pomocą [HYPR](https://get.hypr.com). Korzystając z Azure AD B2C jako dostawcy tożsamości, możesz zintegrować HYPR z dowolnymi aplikacjami klienta, aby zapewnić użytkownikom uwierzytelnianie za pomocą hasła. HYPR zastępuje hasła przy użyciu szyfrowania klucza publicznego eliminujące oszustwo, phishing i ponowne użycie poświadczeń.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, musisz:

- Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).

- [Dzierżawa Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant). Dzierżawca jest połączony z subskrypcją platformy Azure.

- Dzierżawa w chmurze HYPR, uzyskaj bezpłatne [Konto próbne](https://get.hypr.com/free-trial).

- Urządzenie przenośne użytkownika zarejestrowane przy użyciu interfejsów API REST usługi HYPR lub HYPR Menedżer urządzeń w dzierżawie HYPR. Na przykład możesz użyć [zestawu HYPR Java SDK](https://docs.hypr.com/integratinghypr/docs/hypr-java-web-sdk) , aby wykonać to zadanie.

## <a name="scenario-description"></a>Opis scenariusza

Integracja HYRP obejmuje następujące składniki:

- Azure AD B2C — serwer autoryzacji odpowiedzialny za Weryfikowanie poświadczeń użytkownika, znanego również jako dostawca tożsamości

- Aplikacje internetowe i mobilne — aplikacje mobilne lub sieci Web, które można chronić za pomocą HYPR i Azure AD B2C. HYPR udostępnia niezawodny zestaw Mobile SDK również aplikację mobilną, której można używać na platformach z systemami iOS i Android w celu przeprowadzania rzeczywistego uwierzytelniania bez hasła.

- Aplikacja mobilna HYPR — aplikacja mobilna HYPR może służyć do wykonywania tego przykładu, jeśli woli nie używać zestawów SDK dla urządzeń przenośnych we własnych aplikacjach mobilnych.

- Interfejsy API REST usługi HYPR — można używać interfejsów API HYPR do rejestrowania i uwierzytelniania urządzenia użytkownika. Te interfejsy API można znaleźć [tutaj](https://apidocs.hypr.com).

Na poniższym diagramie architektury przedstawiono implementację.

![Zrzut ekranu przedstawiający diagram hypr-Architecture](media/partner-hypr/hypr-architecture-diagram.png)

|Krok | Opis |
|:-----| :-----------|
| 1. | Użytkownik dotarł do strony logowania. Użytkownicy wybierają pozycję Zaloguj się/Zaloguj się, a następnie wprowadź nazwę użytkownika na stronie.
| 2. | Aplikacja wysyła atrybuty użytkownika do Azure AD B2C, aby zidentyfikować weryfikację.
| 3. | Azure AD B2C gromadzi atrybuty użytkownika i wysyła atrybuty do HYPR w celu uwierzytelnienia użytkownika za pomocą aplikacji mobilnej HYPR.
| 4. | HYPR wysyła powiadomienie push do urządzenia przenośnego zarejestrowanego użytkownika w celu uwierzytelniania certyfikowanego w trybie online (FIDO). Może to być wydruk odcisku palca, biometryczny lub zdecentralizowany kod PIN.  
| 5. | Po potwierdzeniu powiadomienia wypychanego użytkownik ma przyznane lub odmówiono dostępu do aplikacji klienta na podstawie wyników weryfikacji.

## <a name="configure-the-azure-ad-b2c-policy"></a>Konfigurowanie zasad Azure AD B2C

1. Przejdź do [Azure AD B2C zasad HYPR](https://github.com/HYPR-Corp-Public/Azure-AD-B2C-HYPR-Sample/tree/master/policy) w folderze Policy.

2. Postępuj zgodnie z tym [dokumentem](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications#custom-policy-starter-pack) , aby pobrać [LocalAccounts Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)

3. Skonfiguruj zasady dla dzierżawy Azure AD B2C.

>[!NOTE]
>Aktualizowanie podanych zasad w celu odłożenia względem określonej dzierżawy.

## <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Otwórz dzierżawcę Azure AD B2C i w obszarze zasady wybierz pozycję **platforma obsługi tożsamości**.

2. Wybierz wcześniej utworzone **SignUpSignIn**.

3. Wybierz pozycję **Uruchom przepływ użytkownika** i wybierz ustawienia:

   a. **Aplikacja**: wybór zarejestrowanej aplikacji (przykład: JWT)

   b. **Adres URL odpowiedzi**: Wybierz **adres URL przekierowania**

   c. Wybierz pozycję **Uruchom przepływ użytkownika**.

4. Przejdź przez przepływ rejestracji i Utwórz konto

5. HYPR zostanie wywołana w przepływie, po utworzeniu atrybutu użytkownika. Jeśli przepływ jest niekompletny, sprawdź, czy użytkownik nie jest zapisany w katalogu.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowe informacje, zapoznaj się z następującymi artykułami:

- [Zasady niestandardowe w usłudze Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Wprowadzenie do zasad niestandardowych w Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
