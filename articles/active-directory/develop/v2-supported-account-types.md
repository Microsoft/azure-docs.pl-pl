---
title: Obsługiwane typy kont — platforma tożsamości firmy Microsoft | Azure
description: Dokumentacja koncepcyjna dotycząca odbiorców i obsługiwanych typów kont w aplikacjach
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
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535778"
---
# <a name="supported-account-types"></a>Obsługiwane typy konta

Z tego artykułu dowiesz się, jakie typy kont (czasami nazywane grupy odbiorców) są obsługiwane w aplikacjach.

<!-- This section can be in an include for many of the scenarios (SPA, web app signing-in users, protecting a web API, Desktop (depending on the flows), Mobile -->

## <a name="supported-accounts-types-in-microsoft-identity-platform-applications"></a>Obsługiwane typy kont w aplikacjach platformy Microsoft Identity

W chmurze publicznej platformy Microsoft Azure większość typów aplikacji może logować się do użytkowników z dowolną grupą odbiorców:

- Jeśli piszesz aplikację business (LOB), możesz zalogować użytkowników we własnej organizacji. Taka aplikacja jest czasami nazywana **pojedynczą dzierżawą**.
- Jeśli jesteś niezależną instytucją żytą, możesz napisać aplikację, która loguje się do użytkowników:

  - W każdej organizacji. Taka aplikacja nosi nazwę aplikacji sieci web **z wieloma dzierżawami.** Czasami czytasz, że loguje się do użytkowników za pomocą swoich kont służbowych lub szkolnych.
  - Z ich pracy lub szkoły lub osobistego konta Microsoft.
  - Tylko z osobistym kontem Microsoft.
    > [!NOTE]
    > Obecnie platforma tożsamości firmy Microsoft obsługuje osobiste konta Microsoft tylko przez zarejestrowanie aplikacji dla **kont osobistych służbowych lub osobistych firmy Microsoft,** a następnie ograniczenie `https://login.microsoftonline.com/consumers`logowania kodu aplikacji przez określenie urzędu usługi Azure AD podczas tworzenia aplikacji, takiej jak .

- Jeśli piszesz firmę do aplikacji konsumentów, można również zalogować użytkowników przy użyciu ich tożsamości społecznościowych przy użyciu usługi Azure AD B2C.

## <a name="certain-authentication-flows-dont-support-all-the-account-types"></a>Niektóre przepływy uwierzytelniania nie obsługują wszystkich typów kont

Niektórych typów kont nie można używać z niektórymi przepływami uwierzytelniania. Na przykład w aplikacjach komputerowych, platformach uniwersalnych systemu Windows lub demonach:

- Aplikacje demonów mogą być używane tylko w organizacjach usługi Azure Active Directory. Nie ma sensu próbować używać aplikacji demonów do manipulowania kontami osobistymi firmy Microsoft (nigdy nie zostanie udzielona zgoda administratora).
- Przepływ zintegrowanego uwierzytelniania systemu Windows można używać tylko z kontami służbowymi lub szkolnymi (w organizacji lub dowolnej organizacji). W rzeczywistości zintegrowane uwierzytelnianie systemu Windows działa z kontami domeny i wymaga, aby maszyny były przyłączone do domeny lub przyłączona do usługi Azure AD. Ten przepływ nie ma sensu w przypadku osobistych kont Microsoft.
- [Grant hasła właściciela zasobu](./v2-oauth-ropc.md) (nazwa użytkownika/hasło) nie może być używany z osobistymi kontami Microsoft. W rzeczywistości osobiste konta Microsoft wymagają, aby użytkownik wyraził zgodę na dostęp do zasobów osobistych podczas każdej sesji logowania. Dlatego to zachowanie nie jest zgodne z przepływami nieinterakcyjnymi.
- Przepływ kodu urządzenia nie działa jeszcze z osobistymi kontami Microsoft.

## <a name="supported-account-types-in-national-clouds"></a>Obsługiwane typy kont w chmurach krajowych

 Aplikacje mogą również logować się do użytkowników w [chmurach krajowych](authentication-national-cloud.md). Jednak konta osobiste firmy Microsoft nie są obsługiwane w tych chmurach (z definicji tych chmur). Dlatego obsługiwane typy kont są zredukowane dla tych chmur do organizacji (pojedyncza dzierżawa) lub dowolnych organizacji (aplikacje z wieloma dzierżawami).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [najem w usłudze Azure Active Directory](./single-and-multi-tenant-apps.md)
- Dowiedz się więcej o [Chmurach Narodowych](./authentication-national-cloud.md)
