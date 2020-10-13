---
title: Multi-Factor Authentication w Azure Active Directory B2C | Microsoft Docs
description: Jak włączyć Multi-Factor Authentication w aplikacjach przeznaczonych dla konsumentów zabezpieczonych przez Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/30/2018
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 69096e5f650a131c5af7ec4da60b7cbca225a56f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87116612"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Włączanie uwierzytelniania wieloskładnikowego w Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) integruje się bezpośrednio z [platformą Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) w celu dodania drugiej warstwy zabezpieczeń do tworzenia kont i logowania w aplikacjach. Uwierzytelnianie wieloskładnikowe można włączyć bez konieczności pisania pojedynczego wiersza kodu. Jeśli utworzono już przepływy rejestracji i logowania użytkowników, nadal możesz włączyć uwierzytelnianie wieloskładnikowe.

Ta funkcja ułatwia aplikacjom obsługę scenariuszy, takich jak następujące:

- Nie musisz wymagać uwierzytelniania wieloskładnikowego, aby uzyskać dostęp do jednej aplikacji, ale musisz uzyskać do niej dostęp. Klient może na przykład zalogować się do aplikacji z branży ubezpieczeniowej przy użyciu konta społecznościowego lub lokalnego, ale musi zweryfikować numer telefonu przed uzyskaniem dostępu do aplikacji ubezpieczenia domowego zarejestrowanej w tym samym katalogu.
- Nie musisz wymagać uwierzytelniania wieloskładnikowego, aby uzyskać dostęp do aplikacji Ogólnie, ale wymagasz, aby uzyskać dostęp do poufnych części w tym miejscu. Klient może na przykład zalogować się do aplikacji bankowej przy użyciu konta społecznościowego lub lokalnego i sprawdzić saldo konta, ale przed podjęciem próby przeniesienia połączenia należy zweryfikować numer telefonu.

## <a name="set-multi-factor-authentication"></a>Ustawianie uwierzytelniania wieloskładnikowego

Podczas tworzenia przepływu użytkownika można włączyć opcję Uwierzytelnianie wieloskładnikowe.

![Ustawianie uwierzytelniania wieloskładnikowego](./media/custom-policy-multi-factor-authentication/add-policy.png)

Ustaw **uwierzytelnianie wieloskładnikowe** na **włączone**.

Aby sprawdzić środowisko, można użyć funkcji **Uruchom przepływ użytkownika** . Potwierdź następujący scenariusz:

Konto klienta jest tworzone w dzierżawie przed wykonaniem kroku usługi uwierzytelnianie wieloskładnikowe. W ramach tego kroku klient zostanie poproszony o podanie numeru telefonu i zweryfikowanie go. Jeśli weryfikacja zakończy się pomyślnie, numer telefonu jest dołączany do konta w celu późniejszego użycia. Nawet jeśli klient zostanie anulowany lub odrzuca, klient może zostać poproszony o ponowne zweryfikowanie numeru telefonu podczas kolejnego logowania z włączonym uwierzytelnianiem wieloskładnikowym.

## <a name="add-multi-factor-authentication"></a>Dodawanie uwierzytelniania wieloskładnikowego

Istnieje możliwość włączenia uwierzytelniania wieloskładnikowego w przepływie użytkownika, który został wcześniej utworzony.

Aby włączyć uwierzytelnianie wieloskładnikowe:

1. Otwórz przepływ użytkownika, a następnie wybierz pozycję **Właściwości**.
2. Obok pozycji **uwierzytelnianie wieloskładnikowe**wybierz pozycję **włączone**.
3. Kliknij przycisk **Zapisz** w górnej części strony.


