---
title: Multi-Factor Authentication w Azure Active Directory B2C | Microsoft Docs
description: Jak włączyć Multi-Factor Authentication w aplikacjach przeznaczonych dla konsumentów zabezpieczonych przez Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/01/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: b7b7f1c5fb0a7991707a26b4a7f54fb3ffaf7bab
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033524"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Włączanie uwierzytelniania wieloskładnikowego w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) integruje się bezpośrednio z [usługą Azure AD Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md) , co umożliwia dodanie drugiej warstwy zabezpieczeń do tworzenia kont i logowania w aplikacjach. Uwierzytelnianie wieloskładnikowe włącza się bez pisania nawet jednego wiersza kodu. Jeśli utworzono już przepływy rejestracji i logowania użytkowników, nadal możesz włączyć uwierzytelnianie wieloskładnikowe.

Ta funkcja ułatwia aplikacjom obsługę scenariuszy, takich jak:

- Nie musisz wymagać uwierzytelniania wieloskładnikowego, aby uzyskać dostęp do jednej aplikacji, ale musisz uzyskać do niej dostęp. Klient może na przykład zalogować się do aplikacji z branży ubezpieczeniowej przy użyciu konta społecznościowego lub lokalnego, ale musi zweryfikować numer telefonu przed uzyskaniem dostępu do aplikacji ubezpieczenia domowego zarejestrowanej w tym samym katalogu.
- Nie musisz wymagać uwierzytelniania wieloskładnikowego, aby uzyskać dostęp do aplikacji Ogólnie, ale wymagasz, aby uzyskać dostęp do poufnych części w tym miejscu. Klient może na przykład zalogować się do aplikacji bankowej przy użyciu konta społecznościowego lub lokalnego i sprawdzić saldo konta, ale przed podjęciem próby przeniesienia połączenia należy zweryfikować numer telefonu.

## <a name="set-multi-factor-authentication"></a>Ustawianie uwierzytelniania wieloskładnikowego

::: zone pivot="b2c-user-flow"

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
1. Użyj filtru **katalogów i subskrypcji** w górnym menu, aby wybrać katalog, który zawiera dzierżawę Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Lub wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **przepływy użytkownika**.
1. Wybierz przepływ użytkownika, dla którego chcesz włączyć usługę MFA. Na przykład *B2C_1_signinsignup*.
1. Wybierz pozycję **Właściwości**.
1. W sekcji **uwierzytelnianie** wieloskładnikowe wybierz żądaną **metodę MFA**, a następnie w obszarze **wymuszania MFA** wybierz opcję **zawsze włączone**, lub **warunkowo (zalecane)**.
   > [!NOTE]
   >
   > - W przypadku wybrania **warunkowego (zalecane)** należy również [dodać dostęp warunkowy do przepływów użytkowników](conditional-access-user-flow.md)i określić aplikacje, do których mają być stosowane zasady.
   > - Uwierzytelnianie wieloskładnikowe (MFA) jest domyślnie wyłączone dla przepływów użytkowników logujących się. Uwierzytelnianie wieloskładnikowe można włączyć w przepływach użytkownika przy użyciu rejestracji w telefonie, ale ponieważ numer telefonu jest używany jako identyfikator podstawowy, jedyną opcją dostępną dla drugiego czynnika uwierzytelniania jest adres e-mail.

1. Wybierz pozycję **Zapisz**. Uwierzytelnianie wieloskładnikowe jest teraz włączone dla tego przepływu użytkownika.

Aby sprawdzić środowisko, można użyć funkcji **Uruchom przepływ użytkownika** . Potwierdź następujący scenariusz:

Konto klienta jest tworzone w dzierżawie przed wykonaniem kroku usługi uwierzytelnianie wieloskładnikowe. W ramach tego kroku klient zostanie poproszony o podanie numeru telefonu i zweryfikowanie go. Jeśli weryfikacja zakończy się pomyślnie, numer telefonu jest dołączany do konta w celu późniejszego użycia. Nawet jeśli klient zostanie anulowany lub odrzuca, klient może zostać poproszony o ponowne zweryfikowanie numeru telefonu podczas kolejnego logowania z włączonym uwierzytelnianiem wieloskładnikowym.

::: zone-end

::: zone pivot="b2c-custom-policy"

Aby włączyć Multi-Factor Authentication Pobierz pakiety początkowe zasad z usługi GitHub, zaktualizuj pliki XML w pakiecie **SocialAndLocalAccountsWithMFA** Starter przy użyciu nazwy dzierżawy Azure AD B2C. **SocialAndLocalAccountsWithMFA** umożliwia dostęp do opcji uwierzytelniania społecznościowego, lokalnego i wieloskładnikowego. Aby uzyskać więcej informacji, zobacz Wprowadzenie [do zasad niestandardowych w Active Directory B2C](custom-policy-get-started.md). 

::: zone-end
