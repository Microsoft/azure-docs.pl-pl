---
title: Synchronizowanie lokalnych kont partnerskich z chmurą jako użytkownikami B2B — Azure AD
description: Przyznaj lokalnie zarządzanym partnerom zewnętrznym dostęp do zasobów lokalnych i w chmurze przy użyciu tych samych poświadczeń z funkcją współpracy B2B usługi Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/24/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76b17391008160cfea9cbf029932d7081466cf3d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87909403"
---
# <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources-using-azure-ad-b2b-collaboration"></a>Przyznaj lokalnym zarządzanym kontom partnerskim dostęp do zasobów w chmurze przy użyciu funkcji współpracy B2B usługi Azure AD

Przed Azure Active Directory (Azure AD) organizacje z lokalnymi systemami tożsamości mają tradycyjnie zarządzane konta partnerów w katalogu lokalnym. W takiej organizacji po rozpoczęciu przenoszenia aplikacji do usługi Azure AD chcesz upewnić się, że partnerzy mogą uzyskać dostęp do potrzebnych zasobów. Bez względu na to, czy zasoby są lokalne, czy w chmurze. Ponadto chcesz, aby partnerzy mogli korzystać z tych samych poświadczeń logowania zarówno dla zasobów lokalnych, jak i usługi Azure AD. 

Jeśli utworzysz konta partnerów zewnętrznych w katalogu lokalnym (na przykład utworzysz konto z nazwą logowania "wmoran" dla zewnętrznego użytkownika o nazwie Wendy została w domenie partners.contoso.com), możesz teraz synchronizować te konta z chmurą. W tym celu można użyć Azure AD Connect do synchronizowania kont partnerskich z chmurą jako użytkownicy B2B usługi Azure AD (czyli użytkowników z atrybutem UserType = gość). Dzięki temu użytkownicy będą mogli uzyskiwać dostęp do zasobów w chmurze przy użyciu tych samych poświadczeń, które są kontami lokalnymi, bez udzielania im dodatkowych informacji niż potrzebują. 

## <a name="identify-unique-attributes-for-usertype"></a>Zidentyfikuj unikatowe atrybuty dla elementu UserType

Przed włączeniem synchronizacji atrybutu UserType należy najpierw zdecydować, jak utworzyć atrybut UserType z poziomu Active Directory lokalnego. Innymi słowy, jakie parametry w środowisku lokalnym są unikatowe dla zewnętrznych współpracowników? Określ parametr odróżniający tych zewnętrznych współpracowników od członków organizacji.

Dwa typowe podejścia do tego są następujące:

- Wyznacz nieużywany lokalny atrybut Active Directory (na przykład extensionAttribute1), który ma być używany jako atrybut source. 
- Alternatywnie można utworzyć wartość atrybutu UserType z innych właściwości. Na przykład chcesz zsynchronizować wszystkich użytkowników jako gościa, jeśli ich lokalny Active Directory atrybut UserPrincipalName kończy się * \@ Partners.contoso.com*domeny.
 
Aby uzyskać szczegółowe wymagania dotyczące atrybutów, zobacz [Włączanie synchronizacji elementu UserType](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype). 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Konfigurowanie Azure AD Connect synchronizacji użytkowników z chmurą

Po zidentyfikowaniu unikatowego atrybutu można skonfigurować Azure AD Connect do synchronizowania tych użytkowników z chmurą jako użytkownicy B2B usługi Azure AD (to jest użytkownicy z atrybutem UserType = gość). Z punktu widzenia autoryzacji tych użytkowników nie można odróżnić od użytkowników B2B utworzonych za pośrednictwem procesu zaproszenia do współpracy B2B usługi Azure AD.

Aby uzyskać instrukcje dotyczące implementacji, zobacz [Włączanie synchronizacji elementu UserType](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype).

## <a name="next-steps"></a>Następne kroki

- [Azure Active Directory współpracy B2B z organizacjami hybrydowymi](hybrid-organizations.md)
- [Przyznaj użytkownikom B2B dostęp do aplikacji lokalnych w usłudze Azure AD](hybrid-cloud-to-on-premises.md)
- Aby zapoznać się z omówieniem Azure AD Connect, zobacz [integrowanie katalogów lokalnych z Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

