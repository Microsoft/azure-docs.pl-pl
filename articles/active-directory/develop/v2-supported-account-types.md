---
title: Obsługiwane typy kont — platforma tożsamości firmy Microsoft | Azure
description: Dokumentacja koncepcyjna dotycząca odbiorców i obsługiwane typy kont w aplikacjach
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: b3b0114bb5d545755fe59c49605d6def341d2275
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81535778"
---
# <a name="supported-account-types"></a>Obsługiwane typy konta

W tym artykule wyjaśniono, jakie typy kont (czasami nazwanych odbiorców) są obsługiwane w aplikacjach.

<!-- This section can be in an include for many of the scenarios (SPA, web app signing-in users, protecting a web API, Desktop (depending on the flows), Mobile -->

## <a name="supported-accounts-types-in-microsoft-identity-platform-applications"></a>Obsługiwane typy kont w aplikacjach platformy tożsamości firmy Microsoft

W Microsoft Azure chmurze publicznej większość typów aplikacji może logować użytkowników z dowolnych grup odbiorców:

- Jeśli piszesz aplikację biznesową, możesz zalogować użytkowników w swojej organizacji. Taka aplikacja jest czasami nazywana **pojedynczym dzierżawcą**.
- Jeśli jesteś niezależnym dostawcą oprogramowania, możesz napisać aplikację, która loguje użytkowników:

  - W każdej organizacji. Taka aplikacja jest nazywana **wielodostępną** aplikacją sieci Web. Czasami odczytasz użytkowników, którzy logują się przy użyciu swoich kont służbowych.
  - Wraz z konto Microsoftami służbowymi lub osobistymi.
  - Tylko konto Microsoft osobiste.
    > [!NOTE]
    > Obecnie platforma tożsamości firmy Microsoft obsługuje osobiste konta Microsoft tylko przez zarejestrowanie aplikacji dla **kont służbowych lub służbowych firmy Microsoft**, a następnie ograniczenie logowania w kodzie aplikacji przez określenie urzędu usługi Azure AD podczas kompilowania aplikacji, na przykład `https://login.microsoftonline.com/consumers`.

- Jeśli piszesz aplikację biznesową dla odbiorców, możesz także zalogować użytkowników przy użyciu ich tożsamości społecznościowych, korzystając z Azure AD B2C.

## <a name="certain-authentication-flows-dont-support-all-the-account-types"></a>Niektóre przepływy uwierzytelniania nie obsługują wszystkich typów kont.

Niektórych typów kont nie można używać w przypadku niektórych przepływów uwierzytelniania. Na przykład w aplikacjach Desktop, platformy UWP i demonach:

- Aplikacji demonów można używać tylko z organizacjami Azure Active Directory. Nie ma sensu próby użycia aplikacji demona do manipulowania kontami osobistymi firmy Microsoft (zgoda administratora nigdy nie zostanie udzielona).
- Zintegrowanego przepływu uwierzytelniania systemu Windows można używać tylko z kontami służbowymi (w organizacji lub w dowolnej organizacji). W rzeczywistości zintegrowane uwierzytelnianie systemu Windows działa z kontami domeny i wymaga przyłączenia komputerów do domeny lub przyłączenia do usługi Azure AD. Ten przepływ nie ma sensu dla osobistych kont Microsoft.
- Nie można używać [hasła właściciela zasobu](./v2-oauth-ropc.md) (username/Password) z osobistymi kontami Microsoft. W rzeczywistości osobiste konta Microsoft wymagają, aby użytkownik wyraził zgodę na dostęp do zasobów osobowych przy każdej sesji logowania. Dlatego to zachowanie nie jest zgodne z nieinteraktywnymi przepływami.
- Przepływ kodu urządzenia nie działa jeszcze z osobistymi kontami Microsoft.

## <a name="supported-account-types-in-national-clouds"></a>Obsługiwane typy kont w chmurach narodowych

 Aplikacje mogą również logować użytkowników w [chmurach narodowych](authentication-national-cloud.md). Konta osobiste firmy Microsoft nie są jednak obsługiwane w tych chmurach (z definicji tych chmur). Dlatego obsługiwane typy kont są ograniczone, w przypadku tych chmur, do organizacji (pojedynczej dzierżawy) lub wszelkich organizacji (aplikacje wielodostępne).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [dzierżawie w Azure Active Directory](./single-and-multi-tenant-apps.md)
- Dowiedz się więcej o [chmurach narodowych](./authentication-national-cloud.md)
