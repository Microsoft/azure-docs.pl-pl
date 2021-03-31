---
title: Mapowanie oświadczeń użytkowników współpracy B2B — Azure Active Directory
description: Dostosuj oświadczenia użytkowników, które są wystawiane w tokenie SAML dla użytkowników B2B Azure Active Directory (Azure AD).
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/06/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3508865d9f89501cf70e09087c6a609beb6662b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87909667"
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>Mapowanie oświadczeń użytkowników współpracy B2B w Azure Active Directory

Azure Active Directory (Azure AD) obsługuje Dostosowywanie oświadczeń wystawionych w tokenie SAML dla użytkowników współpracy B2B. Gdy użytkownik uwierzytelnia się w aplikacji, usługa Azure AD wystawia token języka SAML aplikacji zawierającej informacje (lub oświadczenia) o użytkowniku, który jednoznacznie identyfikuje je. Domyślnie obejmuje to nazwę użytkownika, adres e-mail, imię i nazwisko.

W [Azure Portal](https://portal.azure.com)można wyświetlić lub edytować oświadczenia, które są wysyłane w tokenie SAML do aplikacji. Aby uzyskać dostęp do tych ustawień, wybierz pozycję **Azure Active Directory**  >  **aplikacje dla przedsiębiorstw** > aplikację, która została skonfigurowana do logowania jednokrotnego > **rejestracji** jednokrotnej. Zobacz ustawienia tokenu SAML w sekcji **atrybuty użytkownika** .

![Pokazuje atrybuty tokenu SAML w interfejsie użytkownika](media/claims-mapping/view-claims-in-saml-token.png)

Istnieją dwie możliwe przyczyny, dla których może być konieczne edytowanie oświadczeń wystawionych w tokenie SAML:

1. Aplikacja wymaga innego zestawu identyfikatorów URI lub wartości zgłoszeń.

2. Aplikacja wymaga, aby oświadczenie NameIdentifier miało coś innego niż główna nazwa użytkownika (UPN), która jest przechowywana w usłudze Azure AD.

Aby uzyskać informacje na temat dodawania i edytowania oświadczeń, zobacz [Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw w Azure Active Directory](../develop/active-directory-saml-claims-customization.md).

W przypadku użytkowników współpracy B2B mapowanie wielu dzierżawców NameID i UPN jest blokowane ze względów bezpieczeństwa.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje o właściwościach użytkownika współpracy B2B, zobacz [właściwości Azure Active Directory użytkownika współpracy B2B](user-properties.md).
- Aby uzyskać informacje o tokenach użytkowników związanych z użytkownikami współpracy B2B, zobacz [Omówienie tokenów użytkowników w artykule współpraca B2B w usłudze Azure AD](user-token.md).

