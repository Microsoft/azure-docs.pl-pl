---
title: Co to jest Azure AD Connect Sync Cloud. | Microsoft Docs
description: Opisuje Azure AD Connect synchronizacji w chmurze.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/11/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: af4eaa5912cdf7463c81f501d71b69e934f8febb
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306011"
---
# <a name="what-is-azure-ad-connect-cloud-sync"></a>Co to jest Azure AD Connect Sync Cloud?
Azure AD Connect Sync Cloud to nowa oferta od firmy Microsoft, która została zaprojektowana w celu sprostania i realizacji celów tożsamości hybrydowej na potrzeby synchronizacji użytkowników, grup i kontaktów z usługą Azure AD.  Jest to realizowane za pomocą agenta aprowizacji w chmurze usługi Azure AD zamiast aplikacji Azure AD Connect.  Można go jednak używać razem z Azure AD Connect synchronizacją i zapewnia następujące korzyści:
    
- Obsługa synchronizowania z dzierżawą usługi Azure AD z wielolasach odłączonego Active Directory środowiska lasu: typowe scenariusze obejmują & pozyskiwania (w przypadku których lasy usługi AD przejętej firmy są odizolowane od lasów usługi AD w firmie nadrzędnej) oraz firmy, które w przeszłości korzystają z wielu lasów usługi AD.
- Uproszczona instalacja z użyciem lekkich agentów aprowizacji: agenci działają jako most z usługi AD do usługi Azure AD, ze wszystkimi konfiguracją synchronizacji zarządzaną w chmurze. 
- Wielu agentów aprowizacji można użyć do uproszczenia wdrożeń o wysokiej dostępności, szczególnie krytycznych dla organizacji korzystających z synchronizacji skrótów haseł z usługi AD do usługi Azure AD.
- Obsługa dużych grup z maksymalnie 50 000 członkami. Zalecane jest używanie tylko filtru określania zakresu jednostki organizacyjnej podczas synchronizacji dużych grup.


![Co to jest program Azure AD Connect](media/what-is-cloud-sync/architecture-1.png)

## <a name="how-is-azure-ad-connect-cloud-sync-different-from-azure-ad-connect-sync"></a>Jak Azure AD Connect synchronizacja w chmurze różni się od synchronizacji Azure AD Connect?
Dzięki synchronizacji z usługą Azure AD Connect Cloud Inicjowanie obsługi administracyjnej z usługi AD do usługi Azure AD jest zorganizowane w usługach online firmy Microsoft. Organizacja musi tylko wdrożyć, w środowisku lokalnym lub IaaSm, lekki Agent, który działa jako Most między usługą Azure AD i usługą AD. Konfiguracja aprowizacji jest przechowywana w usłudze Azure AD i zarządzana jako część usługi.

## <a name="azure-ad-connect-cloud-sync-video"></a>Film wideo dotyczący synchronizacji z chmurą Azure AD Connect
Poniższy krótki film zawiera doskonałe Omówienie Azure AD Connect synchronizacji w chmurze:

> [!VIDEO https://youtube.com/embed/mOT3ID02_YQ]


## <a name="comparison-between-azure-ad-connect-and-cloud-sync"></a>Porównanie między Azure AD Connect a synchronizacją w chmurze

Poniższa tabela zawiera porównanie między Azure AD Connect i Azure AD Connect synchronizacji z chmurą:

| Cecha | Synchronizacja Azure Active Directory Connect| Azure Active Directory Connect synchronizacji w chmurze |
|:--- |:---:|:---:|
|Połączenie z pojedynczym lokalnym lasem usługi AD|● |● |
| Połączenie z wieloma lokalnymi lasami usługi AD |● |● |
| Nawiązywanie połączenia z wieloma odłączonymi lokalnymi lasami usługi AD | |● |
| Model instalacji uproszczonego agenta | |● |
| Wielu aktywnych agentów w celu zapewnienia wysokiej dostępności | |● |
| Łączenie z katalogami LDAP|●| | 
| Obsługa obiektów użytkownika |● |● |
| Obsługa obiektów grupy |● |● |
| Obsługa obiektów kontaktów |● |● |
| Obsługa obiektów urządzeń |● | |
| Zezwalaj na podstawowe dostosowywanie przepływów atrybutów |● |● |
| Synchronizuj atrybuty usługi Exchange Online |● |● |
| Synchronizuj atrybuty rozszerzenia 1-15 |● |● |
| Synchronizowanie atrybutów usługi AD zdefiniowanych przez klienta (rozszerzenia katalogów) |● | |
| Obsługa synchronizacji skrótów haseł |●|●|
| Obsługa uwierzytelniania Pass-Through |●||
| Obsługa federacji |●|●|
| Bezproblemowe logowanie jednokrotne|● |●|
| Obsługuje instalację na kontrolerze domeny |● |● |
| Obsługa systemu Windows Server 2016|● |● |
| Filtrowanie według domen/jednostek organizacyjnych/grup |● |● |
| Filtrowanie wartości atrybutów obiektów |● | |
| Umożliwia synchronizowanie minimalnego zestawu atrybutów (MinSync) |● |● |
| Umożliwia usuwanie atrybutów z przepływu z usługi AD do usługi Azure AD |● |● |
| Umożliwia zaawansowane dostosowywanie przepływów atrybutów |● | |
| Obsługa zapisywania zwrotnego (hasła, urządzenia, grupy) |● | |
| Obsługa Azure AD Domain Services|● | |
| [Stornowanie hybrydowe programu Exchange](../hybrid/reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) |● | |
| Obsługa do 150 000 obiektów na domenę usługi AD |● |● |
| Obsługa dużych grup — grupy z maksymalnie 50 000 elementami członkowskimi |● |● |
| Odwołania między domenami|● | |
| Aprowizacja na żądanie| |● |

## <a name="next-steps"></a>Następne kroki 

- [Co to jest aprowizacja?](what-is-provisioning.md)
- [Zainstaluj synchronizację z chmurą](how-to-install.md)
