---
title: Co to są zarejestrowane urządzenia usługi Azure AD?
description: Dowiedz się, w jaki sposób zarejestrowane urządzenia usługi Azure AD zapewniają użytkownikom pomoc techniczną dla scenariuszy tworzenia własnych urządzeń (BYOD) lub urządzeń przenośnych.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72c40b7962090492f8b4b85e555a947130c3628d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91256442"
---
# <a name="azure-ad-registered-devices"></a>Urządzenia zarejestrowane w usłudze Azure AD

Celem zarejestrowanych urządzeń usługi Azure AD jest zapewnienie użytkownikom pomocy technicznej na potrzeby scenariuszy przynoszenia własnych urządzeń (BYOD) lub urządzeń przenośnych. W tych scenariuszach użytkownik może uzyskać dostęp do Azure Active Directory kontrolowanych zasobów organizacji przy użyciu urządzenia osobistego.

| Zarejestrowane usługi Azure AD | Opis |
| --- | --- |
| **Definicja** | Zarejestrowano w usłudze Azure AD bez konieczności logowania się na urządzeniu przy użyciu konta organizacyjnego |
| **Odbiorcy podstawowe** | Dotyczy wszystkich użytkowników z następującymi kryteriami: |
|   | „Przynieś własne urządzenie” (BYOD) |
|   | Urządzenia przenośne |
| **Własność urządzeń** | Użytkownik lub organizacja |
| **Systemy operacyjne** | Windows 10, iOS, Android i MacOS |
| **Inicjowanie obsługi** | Windows 10 — ustawienia |
|   | iOS/Android — aplikacja Portal firmy lub Microsoft Authenticator |
|   | MacOS — Portal firmy |
| **Opcje logowania urządzenia** | Poświadczenia lokalne użytkownika końcowego |
|   | Hasło |
|   | Windows Hello |
|   | Kod PIN |
|   | Biometria lub wzorzec dla innych urządzeń |
| **Zarządzanie urządzeniami** | Zarządzanie urządzeniami przenośnymi (przykład: Microsoft Intune) |
|   | zarządzanie aplikacjami mobilnymi |
| **Najważniejsze możliwości** | Logowanie jednokrotne do zasobów w chmurze |
|   | Dostęp warunkowy po zarejestrowaniu w usłudze Intune |
|   | Dostęp warunkowy za pomocą zasad ochrony aplikacji |
|   | Włącza logowanie za pomocą telefonu przy użyciu aplikacji Microsoft Authenticator |

![Urządzenia zarejestrowane w usłudze Azure AD](./media/concept-azure-ad-register/azure-ad-registered-device.png)

Zarejestrowane urządzenia usługi Azure AD są zalogowane do korzystania z konta lokalnego, takiego jak konto Microsoft na urządzeniu z systemem Windows 10, ale dodatkowo załączono konto usługi Azure AD w celu uzyskania dostępu do zasobów organizacji. Dostęp do zasobów w organizacji może być dodatkowo ograniczony w zależności od tego, czy konto usługi Azure AD i zasady dostępu warunkowego są stosowane do tożsamości urządzenia.

Administratorzy mogą bezpiecznie i bardziej kontrolować te zarejestrowane urządzenia usługi Azure AD przy użyciu narzędzi do zarządzania urządzeniami przenośnymi (MDM), takich jak Microsoft Intune. Zarządzanie urządzeniami przenośnymi umożliwia wymuszenie konfiguracji wymaganych przez organizację, takich jak wymaganie zaszyfrowania magazynu, złożoność hasła i aktualizowanie oprogramowania zabezpieczającego. 

Rejestrację usługi Azure AD można wykonać podczas pierwszego uzyskiwania dostępu do aplikacji służbowej lub ręcznie przy użyciu menu Ustawienia systemu Windows 10. 

## <a name="scenarios"></a>Scenariusze

Użytkownik w organizacji chce uzyskiwać dostęp do narzędzi do obsługi poczty e-mail, raportowania czasu wolnego i uzyskiwania korzyści z rejestracji z komputera domowego. Organizacja zawiera te narzędzia za zasadami dostępu warunkowego, które wymagają dostępu z urządzenia zgodnego z usługą Intune. Użytkownik dodaje konto organizacji i rejestruje swój komputer domowy w usłudze Azure AD, a wymagane zasady usługi Intune są wymuszane, dając użytkownikowi dostęp do swoich zasobów.

Inny użytkownik chce uzyskać dostęp do swojej organizacyjnej poczty e-mail na swoim osobistym telefonie z systemem Android, który został odblokowany. Firma wymaga zgodnego urządzenia i utworzyła zasady zgodności usługi Intune w celu zablokowania urządzeń z odblokowanym dostępem. Pracownik nie jest w stanie uzyskać dostępu do zasobów organizacji na tym urządzeniu.

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie tożsamościami urządzeń przy użyciu witryny Azure Portal](device-management-azure-portal.md)
- [Zarządzanie nieaktywnymi urządzeniami w usłudze Azure AD](manage-stale-devices.md)
