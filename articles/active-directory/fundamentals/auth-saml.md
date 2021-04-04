---
title: Uwierzytelnianie SAML za pomocą Azure Active Directory
description: Wskazówki dotyczące architektury dotyczące uzyskiwania uwierzytelniania SAML przy użyciu Azure Active Directory
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cd2aa5e9ff8cbaeead69f11d2e3de7f760b53ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96168648"
---
# <a name="saml-authentication-with-azure-active-directory"></a>Uwierzytelnianie SAML za pomocą Azure Active Directory

SAML (SAML) to otwarty standard wymiany danych uwierzytelniania i autoryzacji między dostawcą tożsamości a dostawcą usług. SAML jest językiem znaczników opartym na języku XML dla zatwierdzeń zabezpieczeń, które są instrukcjami używanymi przez dostawców usług w celu podejmowania decyzji dotyczących kontroli dostępu. 

Specyfikacja SAML definiuje trzy role:

* Podmiot zabezpieczeń, zazwyczaj użytkownik
* Dostawca tożsamości (dostawcy tożsamości)
* Dostawca usług (SP)


## <a name="use-when"></a>Zastosowania

Istnieje konieczność zapewnienia logowania jednokrotnego (SSO) dla aplikacji SAML dla przedsiębiorstwa.

Chociaż jednym z najważniejszych przypadków użycia adresów SAML jest logowanie jednokrotne, szczególnie przez rozszerzenie logowania jednokrotnego w domenach zabezpieczeń, istnieją również inne przypadki użycia (nazywane profilami). 

![Diagram architektoniczny dla języka SAML](./media/authentication-patterns/saml-auth.png)

## <a name="components-of-system"></a>Składniki systemu

* **Użytkownik**: żąda usługi z aplikacji.

* **Przeglądarka sieci Web**: składnik, z którym korzysta użytkownik.

* **Aplikacja internetowa**: aplikacja dla przedsiębiorstw, która obsługuje protokół SAML i używa usługi Azure AD jako dostawcy tożsamości.

* **Token**: potwierdzenie SAML (nazywane również tokenami SAML), które przenosi zestawy oświadczeń dostawcy tożsamości na temat zasady (użytkownika). Zawiera informacje dotyczące uwierzytelniania, atrybuty i decyzje autoryzacji.

* **Azure AD**: dostawcy tożsamości Cloud w chmurze, który zapewnia uwierzytelnianie logowania jednokrotnego i uwierzytelniania wieloskładnikowego dla aplikacji SAML. Synchronizuje, utrzymuje i zarządza informacjami o tożsamości dla użytkowników, a jednocześnie świadczy usługi uwierzytelniania na potrzeby aplikacji. 

## <a name="implement-saml-authentication-with-azure-ad"></a>Implementowanie uwierzytelniania SAML za pomocą usługi Azure AD

* [Samouczki dotyczące integrowania aplikacji SaaS przy użyciu Azure Active Directory](../saas-apps/tutorial-list.md) 

* [Konfigurowanie logowania jednokrotnego opartego na protokole SAML dla aplikacji spoza galerii](../manage-apps/add-application-portal.md) 

* [Jak usługa Azure AD używa protokołu SAML](../develop/active-directory-saml-protocol-reference.md)