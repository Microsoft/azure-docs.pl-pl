---
title: Przechowywanie danych tożsamości dla klientów z Australii i Nowej Zelandii — Azure AD
description: Dowiedz się, Azure Active Directory przechowuje dane dotyczące tożsamości dla swoich klientów w Australii i Nowej Zelandii.
services: active-directory
author: ajburnle
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bb095e93a3728835e26cbe283f79569c91b7487
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479070"
---
# <a name="identity-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Magazyn danych tożsamości dla klientów z Australii i Nowej Zelandii w Azure Active Directory

Dane tożsamości są przechowywane przez usługę Azure AD w lokalizacji geograficznej na podstawie adresu podanego przez organizację podczas subskrybowania usługi Online firmy Microsoft, takiej jak Microsoft 365 i Azure. Aby uzyskać informacje na temat miejsca przechowywania danych klienta tożsamości, możesz skorzystać z sekcji Gdzie znajdują się Twoje [dane?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) w Centrum zaufania Firmy Microsoft.

> [!NOTE]
> Usługi i aplikacje zintegrowane z usługą Azure AD mają dostęp do danych klienta tożsamości. Oceń każdą usługę i aplikację, których używasz, aby określić, w jaki sposób dane klienta tożsamości są przetwarzane przez określoną usługę i aplikację oraz czy spełniają one wymagania dotyczące magazynu danych firmy. Aby uzyskać więcej informacji na temat przechowywania danych usług firmy Microsoft, zobacz sekcję Gdzie przechowywane są Twoje dane? w Centrum zaufania Microsoft.

W przypadku klientów, którzy podali adres w Australii lub Nowej Zelandii, usługa Azure AD przechowuje dane tożsamości dla tych usług w australijskich centrach danych: 
- Zarządzanie katalogami w usłudze Azure AD 
- Authentication

Wszystkie inne usługi Azure AD przechowują dane klientów w globalnych centrach danych. Aby zlokalizować centrum danych dla usługi, [zobacz Azure Active Directory — Gdzie znajdują się Twoje dane?](https://aka.ms/AADDataMap)

## <a name="microsoft-azure-ad-multi-factor-authentication-mfa"></a>Microsoft Azure AD Multi-Factor Authentication (MFA)

Uwierzytelniania wieloskładnikowego przechowuje dane klienta tożsamości w globalnych centrach danych. Aby dowiedzieć się więcej na temat informacji o użytkownikach zbieranych i przechowywanych przez usługę Azure AD MFA opartą na chmurze i usługę Azure MFA Server, zobacz Zbieranie danych użytkowników za pomocą usługi [Azure Multi-Factor Authentication.](../authentication/concept-mfa-data-residency.md)

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji o funkcjach i funkcjach opisanych powyżej, zobacz następujące artykuły:
- [Co to jest uwierzytelnianie wieloskładnikowe?](../authentication/concept-mfa-howitworks.md)
