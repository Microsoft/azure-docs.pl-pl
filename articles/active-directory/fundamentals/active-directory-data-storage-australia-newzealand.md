---
title: Magazyn danych tożsamości dla klientów Australii — Azure AD
description: Informacje o tym, gdzie Azure Active Directory są przechowywane dane dotyczące tożsamości dla klientów Australii.
services: active-directory
author: msaburnley
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0dc659f3d580646c1e86e5e97940268311e5546f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "79460538"
---
# <a name="identity-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Magazyn danych tożsamości dla klientów Australii i Nowej Zelandii w Azure Active Directory

Dane tożsamości są przechowywane w usłudze Azure AD w lokalizacji geograficznej na podstawie adresu dostarczonego przez organizację w przypadku subskrybowania usługi online firmy Microsoft, takiej jak Office 365 i Azure. Aby uzyskać informacje o tym, gdzie są przechowywane dane klienta tożsamości, możesz użyć sekcji [gdzie znajdują się Twoje dane?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) Centrum zaufania Microsoft.

> [!NOTE]
> Usługi i aplikacje, które integrują się z usługą Azure AD, mają dostęp do tożsamości klienta danych. Oceń poszczególne usługi i aplikacje, których używasz, aby określić, jak dane klienta tożsamości są przetwarzane przez daną usługę i aplikację oraz czy spełniają wymagania dotyczące magazynu danych w firmie. Aby uzyskać więcej informacji na temat przechowywania danych usług firmy Microsoft, zobacz sekcję Gdzie przechowywane są Twoje dane? w Centrum zaufania Microsoft.

W przypadku klientów, którzy podały adres w Australii i Nowej Zelandii i korzystasz z usługi Azure AD w wersji bezpłatna, usługa Azure AD przechowuje dane OSOBowe w obrębie australijskich centrów danych. 

Wszystkie inne usługi Azure AD Premium przechowują dane klienta w globalnych centrach danych. Aby zlokalizować centrum danych dla usługi, zobacz [Azure Active Directory — gdzie znajdują się Twoje dane?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure uwierzytelnianie wieloskładnikowe (MFA)

Usługa MFA w usłudze Azure AD przechowuje dane klientów tożsamości w globalnych centrach danych. Aby dowiedzieć się więcej na temat informacji o użytkowniku zebranych i przechowywanych przez usługę Azure MFA opartą na chmurze i serwer usługi Azure MFA, zobacz [zbieranie danych użytkowników w usłudze azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-data-residency). Klienci korzystający z usługi MFA będą przechowywali dane poza centrami danych w Australii. 

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat funkcji i funkcji opisanych powyżej, zobacz następujące artykuły:
- [Co to jest uwierzytelnianie wieloskładnikowe?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)
