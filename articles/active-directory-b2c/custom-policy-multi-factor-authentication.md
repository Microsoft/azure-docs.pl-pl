---
title: Multi-Factor Authentication w Azure Active Directory B2C | Microsoft Docs
description: Jak włączyć Multi-Factor Authentication w aplikacjach przeznaczonych dla konsumentów zabezpieczonych przez Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8c150235dc6b867a69b539aba9c90d666122a210
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96170450"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Włączanie uwierzytelniania wieloskładnikowego w Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) integruje się bezpośrednio z [usługą Azure AD Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md) , co umożliwia dodanie drugiej warstwy zabezpieczeń do tworzenia kont i logowania w aplikacjach. Uwierzytelnianie wieloskładnikowe można włączyć bez konieczności pisania pojedynczego wiersza kodu. Jeśli utworzono już przepływy rejestracji i logowania użytkowników, nadal możesz włączyć uwierzytelnianie wieloskładnikowe.

Ta funkcja ułatwia aplikacjom obsługę scenariuszy, takich jak:

- Nie musisz wymagać uwierzytelniania wieloskładnikowego, aby uzyskać dostęp do jednej aplikacji, ale musisz uzyskać do niej dostęp. Klient może na przykład zalogować się do aplikacji z branży ubezpieczeniowej przy użyciu konta społecznościowego lub lokalnego, ale musi zweryfikować numer telefonu przed uzyskaniem dostępu do aplikacji ubezpieczenia domowego zarejestrowanej w tym samym katalogu.
- Nie musisz wymagać uwierzytelniania wieloskładnikowego, aby uzyskać dostęp do aplikacji Ogólnie, ale wymagasz, aby uzyskać dostęp do poufnych części w tym miejscu. Klient może na przykład zalogować się do aplikacji bankowej przy użyciu konta społecznościowego lub lokalnego i sprawdzić saldo konta, ale przed podjęciem próby przeniesienia połączenia należy zweryfikować numer telefonu.

## <a name="set-multi-factor-authentication"></a>Ustawianie uwierzytelniania wieloskładnikowego

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
1. Użyj filtru **katalogów i subskrypcji** w górnym menu, aby wybrać katalog, który zawiera dzierżawę Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Lub wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **przepływy użytkownika**.
1. Wybierz przepływ użytkownika, dla którego chcesz włączyć usługę MFA. Na przykład *B2C_1_signinsignup*.
1. Wybierz pozycję **Właściwości**.
1. W sekcji **uwierzytelnianie** wieloskładnikowe wybierz żądaną **metodę MFA**, a następnie w obszarze **wymuszania MFA** wybierz opcję **zawsze włączone**, lub **[warunkowo](conditional-access-user-flow.md) (zalecane)**. W przypadku warunku należy utworzyć zasady [dostępu warunkowego](conditional-access-identity-protection-setup.md) i określić aplikacje, do których mają być stosowane zasady. 
1. Wybierz pozycję Zapisz. Uwierzytelnianie wieloskładnikowe jest teraz włączone dla tego przepływu użytkownika.

Aby sprawdzić środowisko, można użyć funkcji **Uruchom przepływ użytkownika** . Potwierdź następujący scenariusz:

Konto klienta jest tworzone w dzierżawie przed wykonaniem kroku usługi uwierzytelnianie wieloskładnikowe. W ramach tego kroku klient zostanie poproszony o podanie numeru telefonu i zweryfikowanie go. Jeśli weryfikacja zakończy się pomyślnie, numer telefonu jest dołączany do konta w celu późniejszego użycia. Nawet jeśli klient zostanie anulowany lub odrzuca, klient może zostać poproszony o ponowne zweryfikowanie numeru telefonu podczas kolejnego logowania z włączonym uwierzytelnianiem wieloskładnikowym.