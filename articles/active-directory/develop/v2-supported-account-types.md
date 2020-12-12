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
ms.date: 07/14/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: f0895cfc14b0237db8f2f762602fee949354e6cb
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97347459"
---
# <a name="supported-account-types"></a>Obsługiwane typy kont

W tym artykule wyjaśniono, jakie typy kont (czasami nazywane *odbiorcami*) są obsługiwane w aplikacjach platformy tożsamości firmy Microsoft.

<!-- This section can be in an include for many of the scenarios (SPA, web app signing-in users, protecting a web API, Desktop (depending on the flows), Mobile -->

## <a name="account-types-in-the-public-cloud"></a>Typy kont w chmurze publicznej

W Microsoft Azure chmurze publicznej większość typów aplikacji może logować użytkowników z dowolnych grup odbiorców:

- Jeśli piszesz aplikację biznesową (LOB), możesz zalogować użytkowników w swojej organizacji. Taka aplikacja jest czasami nazywana *pojedynczą dzierżawą*.
- Jeśli jesteś niezależnym dostawcą oprogramowania, możesz napisać aplikację, która loguje się do użytkowników:

  - W każdej organizacji. Taka aplikacja jest nazywana *wielodostępną* aplikacją sieci Web. Czasami odczytasz, że loguje się do użytkowników przy użyciu kont służbowych.
  - Z kontami służbowymi lub osobistymi firmy Microsoft.
  - Tylko z osobistymi kontami Microsoft.
    
- Jeśli piszesz aplikację biznesową, możesz także zalogować użytkowników przy użyciu tożsamości społecznościowych, korzystając z Azure Active Directory B2C (Azure AD B2C).

## <a name="account-type-support-in-authentication-flows"></a>Obsługa typu konta w przepływach uwierzytelniania

Niektórych typów kont nie można używać w przypadku niektórych przepływów uwierzytelniania. Na przykład w aplikacjach Desktop, platformy UWP lub demonach:

- Aplikacje demonów mogą być używane tylko z organizacjami usługi Azure AD. Nie ma sensu, aby próbować używać aplikacji demona do manipulowania kontami osobistymi firmy Microsoft. Zgoda administratora nigdy nie zostanie przyznana.
- Ze zintegrowanego przepływu uwierzytelniania systemu Windows można korzystać tylko z kontami służbowymi (w organizacji lub w dowolnej organizacji). Zintegrowane uwierzytelnianie systemu Windows działa z kontami domeny i wymaga przyłączenia komputerów do domeny lub przyłączenia do usługi Azure AD. Ten przepływ nie ma sensu dla osobistych kont Microsoft.
- Nie można używać [poświadczeń hasła właściciela zasobu](./v2-oauth-ropc.md) (username/Password) z osobistymi kontami Microsoft. Osobiste konta Microsoft wymagają, aby użytkownik wyraził zgodę na dostęp do zasobów osobowych przy każdej sesji logowania. Dlatego to zachowanie nie jest zgodne z przepływami nieinteraktywnymi.

## <a name="account-types-in-national-clouds"></a>Typy kont w chmurach narodowych

Aplikacje mogą również logować użytkowników w [chmurach narodowych](authentication-national-cloud.md). Konta osobiste firmy Microsoft nie są jednak obsługiwane w tych chmurach. Dlatego obsługiwane typy kont są ograniczone, w przypadku tych chmur, do organizacji (pojedynczej dzierżawy) lub wszelkich organizacji (aplikacje wielodostępne).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [dzierżawie w Azure Active Directory](./single-and-multi-tenant-apps.md).
- Dowiedz się więcej o [chmurach narodowych](./authentication-national-cloud.md).
