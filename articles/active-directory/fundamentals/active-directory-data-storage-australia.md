---
title: Magazyn danych tożsamości dla klientów Australii i Nowej Zelandii — Azure AD
description: Dowiedz się, gdzie Azure Active Directory przechowuje dane dotyczące tożsamości dla swoich klientów Australii i Nowej Zelandii.
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
ms.openlocfilehash: 498b75a6541da97e57e23465ae6eb23a6c854727
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94836958"
---
# <a name="identity-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Magazyn danych tożsamości dla klientów Australii i Nowej Zelandii w Azure Active Directory

Dane tożsamości są przechowywane w usłudze Azure AD w lokalizacji geograficznej na podstawie adresu dostarczonego przez organizację podczas subskrybowania usługi online firmy Microsoft, takiej jak Microsoft 365 i Azure. Aby uzyskać informacje o tym, gdzie są przechowywane dane klienta tożsamości, możesz użyć sekcji [gdzie znajdują się Twoje dane?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) Centrum zaufania Microsoft.

> [!NOTE]
> Usługi i aplikacje, które integrują się z usługą Azure AD, mają dostęp do tożsamości klienta danych. Oceń poszczególne usługi i aplikacje, których używasz, aby określić, jak dane klienta tożsamości są przetwarzane przez daną usługę i aplikację oraz czy spełniają wymagania dotyczące magazynu danych w firmie. Aby uzyskać więcej informacji na temat przechowywania danych usług firmy Microsoft, zobacz sekcję Gdzie przechowywane są Twoje dane? w Centrum zaufania Microsoft.

W przypadku klientów, którzy podały adres w Australii lub Nowej Zelandii, usługa Azure AD przechowuje dane tożsamości dla tych usług w australijskich centrach danych: 
- Zarządzanie katalogami usługi Azure AD 
- Authentication

Wszystkie inne usługi Azure AD Services przechowują dane klienta w globalnych centrach danych. Aby zlokalizować centrum danych dla usługi, zobacz [Azure Active Directory — gdzie znajdują się Twoje dane?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

## <a name="microsoft-azure-ad-multi-factor-authentication-mfa"></a>Multi-Factor Authentication Microsoft Azure AD (MFA)

Usługi MFA przechowują dane klienta tożsamości w globalnych centrach danych. Aby dowiedzieć się więcej na temat informacji o użytkowniku zebranych i przechowywanych przez usługę Azure AD MFA i serwer usługi Azure MFA, zobacz [zbieranie danych użytkownika w usłudze azure Multi-Factor Authentication](../authentication/concept-mfa-data-residency.md).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat funkcji i funkcji opisanych powyżej, zobacz następujące artykuły:
- [Co to jest uwierzytelnianie wieloskładnikowe?](../authentication/concept-mfa-howitworks.md)