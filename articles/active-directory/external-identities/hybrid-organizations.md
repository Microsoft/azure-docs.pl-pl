---
title: Współpraca B2B dla organizacji hybrydowych — Azure AD
description: Zapewnianie partnerom dostępu do zasobów lokalnych i w chmurze przy użyciu funkcji współpracy B2B usługi Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 427f7ad4d6a1b9839b1197ef9f7ca15400ea0f59
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87909398"
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>Azure Active Directory współpracy B2B z organizacjami hybrydowymi

Azure Active Directory (Azure AD) Współpraca B2B ułatwia udostępnianie partnerom zewnętrznym dostępu do aplikacji i zasobów w organizacji. Jest to prawdziwe nawet w konfiguracji hybrydowej, w której znajdują się zasoby lokalne i oparte na chmurze. Nie ma znaczenia, czy obecnie zarządzasz kontami zewnętrznych partnerów lokalnie w lokalnym systemie tożsamości, czy zarządzasz kontami zewnętrznymi w chmurze jako użytkownikami B2B usługi Azure AD. Teraz można przyznać tym użytkownikom dostęp do zasobów w dowolnej lokalizacji przy użyciu tych samych poświadczeń logowania w obu środowiskach.

## <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-apps"></a>Przyznaj użytkownikom B2B dostęp do aplikacji lokalnych w usłudze Azure AD

Jeśli Twoja organizacja korzysta z funkcji współpracy B2B usługi Azure AD, aby zapraszać użytkowników-Gości od organizacji partnerskich do usługi Azure AD, możesz teraz udostępnić tym użytkownikom B2B dostęp do aplikacji lokalnych.

W przypadku aplikacji korzystających z uwierzytelniania opartego na protokole SAML można udostępnić te aplikacje użytkownikom B2B za pośrednictwem Azure Portal przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD do uwierzytelniania.

W przypadku aplikacji korzystających ze zintegrowanego uwierzytelniania systemu Windows (IWA) przy użyciu ograniczonego delegowania protokołu Kerberos (KCD) można również użyć serwera proxy usługi Azure AD. Jednak aby autoryzacja działała, wymagany jest obiekt użytkownika w lokalnym systemie Windows Server Active Directory. Istnieją dwie metody, których można użyć do tworzenia obiektów użytkowników lokalnych, które reprezentują użytkowników gościa B2B.

- Do Microsoft Graph można użyć Microsoft Identity Manager (MIM) 2016 SP1 i agenta zarządzania programu MIM.
- Możesz użyć skryptu programu PowerShell. (To rozwiązanie nie wymaga programu MIM).

Aby uzyskać szczegółowe informacje na temat sposobu implementowania tych rozwiązań, zobacz [udzielanie użytkownikom B2B w usłudze Azure AD dostępu do aplikacji lokalnych](hybrid-cloud-to-on-premises.md).

## <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources"></a>Przyznaj lokalnym zarządzanym kontom partnerskim dostęp do zasobów w chmurze

Przed rozpoczęciem pracy z usługą Azure AD organizacje z lokalnymi systemami tożsamości mają tradycyjnie zarządzane konta partnerów w katalogu lokalnym. Jeśli jesteś organizacją, upewnij się, że partnerzy nadal mają dostęp podczas przenoszenia aplikacji i innych zasobów do chmury. Najlepiej, aby Ci użytkownicy mogli korzystać z tego samego zestawu poświadczeń w celu uzyskania dostępu do zasobów w chmurze i lokalnych. 

Teraz oferujemy metody, w których można używać Azure AD Connect do synchronizowania tych kont lokalnych z chmurą jako "Gość", gdzie konta działają podobnie jak użytkownicy B2B usługi Azure AD.

Aby chronić dane firmy, możesz kontrolować dostęp do tylko odpowiednich zasobów i konfigurować zasady autoryzacji, które traktują tych użytkowników-Gości inaczej od pracowników.

Aby uzyskać szczegółowe informacje na temat implementacji, zobacz [udzielanie dostępu do zasobów w chmurze przy użyciu usługi Azure AD B2B](hybrid-on-premises-to-cloud.md).
 
## <a name="next-steps"></a>Następne kroki

- [Przyznaj użytkownikom B2B dostęp do aplikacji lokalnych w usłudze Azure AD](hybrid-cloud-to-on-premises.md)
- [Przyznaj lokalnym zarządzanym kontom partnerskim dostęp do zasobów w chmurze przy użyciu funkcji współpracy B2B usługi Azure AD](hybrid-on-premises-to-cloud.md)


