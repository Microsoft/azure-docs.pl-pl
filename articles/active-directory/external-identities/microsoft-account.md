---
title: Dostawca tożsamości konta Microsoft (MSA) w usłudze Azure AD
description: Użyj usługi Azure AD, aby umożliwić użytkownikom zewnętrznym logowanie się do aplikacji usługi Azure AD przy użyciu ich konto Microsoft (MSA).
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25d3380277d0e653fd5cb03069b7d91cb363dd95
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101693145"
---
# <a name="microsoft-account-msa-identity-provider-for-external-identities-preview"></a>Dostawca tożsamości konta Microsoft (MSA) dla tożsamości zewnętrznych (wersja zapoznawcza)

> [!NOTE]
> Dostawca tożsamości konta Microsoft jest publiczną funkcją w wersji zapoznawczej Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure.

Użytkownicy gościa B2B mogą korzystać z własnych osobistych kont Microsoft na potrzeby współpracy B2B bez dalszej konfiguracji. Użytkownicy-Goście mogą wykorzystać zaproszenia do współpracy B2B lub dokończyć przepływy użytkowników do rejestracji przy użyciu ich osobistego konto Microsoft.

Konta Microsoft są konfigurowane przez użytkownika w celu uzyskania dostępu do firmowych produktów firmy Microsoft i usług w chmurze, takich jak Outlook, OneDrive, Xbox LIVE lub Microsoft 365. Konto jest tworzone i przechowywane w systemie kont tożsamości konsumenta firmy Microsoft, który jest uruchamiany przez firmę Microsoft.

## <a name="guest-sign-in-using-microsoft-accounts"></a>Logowanie gościa przy użyciu kont Microsoft

Konto Microsoft jest dostępne na liście tożsamości dostawców tożsamości zewnętrznych domyślnie. Żadna dodatkowa konfiguracja nie jest wymagana, aby umożliwić użytkownikom-Gościom logowanie się przy użyciu ich konto Microsoft za pomocą przepływu zaproszenia lub samoobsługowego przepływu użytkownika.

![Konto Microsoft na liście dostawców tożsamości](media/microsoft-account/microsoft-account-identity-provider.png)

### <a name="microsoft-account-in-the-invitation-flow"></a>Konto Microsoft w przepływie zaproszenia

Gdy [zapraszasz użytkownika-gościa](add-users-administrator.md) do współpracy B2B, możesz określić ich konto Microsoft jako adres e-mail, który będzie używany do logowania.

![Zaproś przy użyciu konto Microsoft](media/microsoft-account/microsoft-account-invite.png)

### <a name="microsoft-account-in-self-service-sign-up-user-flows"></a>Konto Microsoft w przepływach użytkowników samoobsługowego rejestrowania

Konto Microsoft to opcja dostawcy tożsamości dla przepływów użytkownika samoobsługowego rejestrowania. Użytkownicy mogą zarejestrować się w aplikacjach przy użyciu własnych kont Microsoft. Najpierw musisz włączyć samoobsługowe [Rejestrowanie](self-service-sign-up-user-flow.md) dla dzierżawy. Następnie możesz skonfigurować przepływ użytkownika dla aplikacji i wybrać opcję konto Microsoft jako jedną z opcji logowania.

![konto Microsoft w przepływie użytkownika samoobsługowego rejestrowania](media/microsoft-account/microsoft-account-user-flow.png)

## <a name="next-steps"></a>Następne kroki

- [Dodawanie Azure Active Directory użytkowników współpracy B2B](add-users-administrator.md)
- [Dodawanie funkcji samoobsługowego rejestrowania do aplikacji](self-service-sign-up-user-flow.md)