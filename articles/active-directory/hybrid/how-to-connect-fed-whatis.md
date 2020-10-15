---
title: Azure AD Connect i Federacja | Microsoft Docs
description: Ta strona jest centralną lokalizacją dla całej dokumentacji dotyczącej AD FS operacji korzystających z Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: f9107cf5-0131-499a-9edf-616bf3afef4d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/09/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a97142e0c512f4f95235ad08c94c852906d3efd8
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92095860"
---
# <a name="azure-ad-connect-and-federation"></a>Program Azure AD Connect a federacja
Program Azure Active Directory (Azure AD) Connect umożliwia skonfigurowanie Federacji przy użyciu lokalnych Active Directory Federation Services (AD FS) i usługi Azure AD. Za pomocą logowania federacyjnego można umożliwić użytkownikom logowanie się do usług opartych na usłudze Azure AD przy użyciu haseł lokalnych — i, w sieci firmowej, bez konieczności ponownego wprowadzania haseł. Za pomocą opcji federacyjnej z AD FS można wdrożyć nową instalację AD FS lub można określić istniejącą instalację w farmie systemu Windows Server 2012 R2.

Ten temat zawiera informacje dotyczące funkcji związanych z federacyjnym Azure AD Connect. Zawiera listę linków do wszystkich powiązanych tematów. Aby uzyskać linki do Azure AD Connect, zobacz [integrowanie tożsamości lokalnych z Azure Active Directory](whatis-hybrid-identity.md).

## <a name="azure-ad-connect-federation-topics"></a>Azure AD Connect: Tematy dotyczące Federacji
| Temat | Co to obejmuje i kiedy go odczytać |
|:--- |:--- |
| **Azure AD Connect opcje logowania użytkownika** | |
| [Informacje o opcjach logowania użytkownika](plan-connect-user-signin.md) |Dowiedz się więcej o różnych opcjach logowania użytkowników i sposobach ich wpływu na środowisko użytkownika logowania do platformy Azure. |
| **Instalowanie AD FS przy użyciu Azure AD Connect** | |
| [Wymagania wstępne](how-to-connect-install-custom.md#ad-fs-configuration-prerequisites) |Zapoznaj się z wymaganiami wstępnymi dla pomyślnej instalacji AD FS za pośrednictwem Azure AD Connect. |
| [Konfigurowanie farmy AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs) |Zainstaluj nową farmę AD FS przy użyciu Azure AD Connect. |
| [Sfederować z usługą Azure AD przy użyciu alternatywnego identyfikatora logowania](how-to-connect-fed-management.md#alternateid) | Konfigurowanie Federacji przy użyciu alternatywnego identyfikatora logowania  |
| **Modyfikowanie konfiguracji AD FS** | |
| [Napraw zaufanie](how-to-connect-fed-management.md#repairthetrust) |Napraw bieżące zaufanie między lokalnym AD FS i Microsoft 365/Azure. |
| [Dodaj nowy serwer AD FS](how-to-connect-fed-management.md#addadfsserver) |Po instalacji początkowej rozwiń AD FS farmę z dodatkowym serwerem AD FS. |
| [Dodawanie nowego AD FS serwera WAP](how-to-connect-fed-management.md#addwapserver) |Po instalacji początkowej rozwiń AD FS farmę z dodatkowym serwerem proxy aplikacji sieci Web (WAP). |
| [Dodawanie nowej domeny federacyjnej](how-to-connect-fed-management.md#addfeddomain) |Dodaj kolejną domenę, która ma być federacyjnym z usługą Azure AD. |
| [Aktualizowanie certyfikatu TLS/SSL](how-to-connect-fed-ssl-update.md)| Zaktualizuj certyfikat TLS/SSL dla farmy AD FS. |
| [Odnawianie certyfikatów Federacji dla Microsoft 365 i usługi Azure AD](how-to-connect-fed-o365-certs.md)|Odnów certyfikat usługi O365 w usłudze Azure AD.|
| **Inna konfiguracja federacji** | |
| [Federowanie wielu wystąpień usługi Azure AD przy użyciu jednego wystąpienia usługi AD FS](how-to-connect-fed-single-adfs-multitenant-federation.md) | Sfederować wiele usług Azure AD za pomocą pojedynczej farmy AD FS| 
| [Dodaj niestandardowe logo/ilustrację firmy](how-to-connect-fed-management.md#customlogo) |Zmodyfikuj środowisko logowania, określając niestandardowe logo, które jest wyświetlane na stronie logowania AD FS. |
| [Dodawanie opisu logowania](how-to-connect-fed-management.md#addsignindescription) |Zmień opis logowania na stronie logowania AD FS. |
| [Modyfikuj reguły AD FSch roszczeń](how-to-connect-fed-management.md#modclaims) |Modyfikuj lub Dodaj reguły dotyczące roszczeń w AD FS, które odpowiadają konfiguracji synchronizacji Azure AD Connect. |


## <a name="additional-resources"></a>Dodatkowe zasoby
* [Federowanie dwie usługi Azure AD z pojedynczym AD FS](how-to-connect-fed-single-adfs-multitenant-federation.md)
* [Wdrożenie usług AD FS na platformie Azure](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs)
* [Wielogeograficzne wdrożenie AD FS na platformie Azure z platformą Azure Traffic Manager](/windows-server/identity/ad-fs/deployment/active-directory-adfs-in-azure-with-azure-traffic-manager)