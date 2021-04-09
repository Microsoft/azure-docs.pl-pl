---
title: Uaktualnij narzędzie DirSync i Azure AD Sync | Microsoft Docs
description: Opisuje sposób uaktualniania narzędzi DirSync i Azure AD Sync do Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: bd68fb88-110b-4d76-978a-233e15590803
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53d9e2ea10aefc96c2d4d72ad0020704ba48709b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99539420"
---
# <a name="upgrade-windows-azure-active-directory-sync-and-azure-active-directory-sync"></a>Upgrade Windows Azure Active Directory Sync and Azure Active Directory Sync (Uaktualnianie programów Windows Azure Active Directory Sync i Azure Active Directory Sync)
Azure AD Connect to najlepszy sposób na połączenie katalogu lokalnego z usługą Azure AD i Microsoft 365. Jest to doskonały moment na uaktualnienie do Azure AD Connect z usługi Windows Azure Active Directory Sync (DirSync) lub Azure AD Sync (AADSync), ponieważ te narzędzia są obecnie przestarzałe i nie są już obsługiwane od 13 kwietnia 2017.

Dwa narzędzia do synchronizacji tożsamości, które zostały wycofane, zostały zaoferowane dla klientów z pojedynczym lasem (DirSync) i dla różnych klientów zaawansowanych (Azure AD Sync). Te starsze narzędzia zostały zastąpione jednym rozwiązaniem, które jest dostępne we wszystkich scenariuszach: Azure AD Connect. Oferuje ona nowe funkcje, udoskonalenia funkcji i obsługę nowych scenariuszy. Aby można było kontynuować synchronizowanie lokalnych danych tożsamości z usługą Azure AD i Microsoft 365, zdecydowanie zalecamy uaktualnienie do Azure AD Connect. Firma Microsoft nie gwarantuje, że te starsze wersje nie działają po 31 grudnia 2017.

Ostatnia wersja narzędzia DirSync została wydana w lipcu 2014, a Ostatnia wersja Azure AD Sync została wydana w maju 2015.

## <a name="what-is-azure-ad-connect"></a>Co to jest program Azure AD Connect
Azure AD Connect jest następnikiem narzędzia DirSync i Azure AD Sync. Łączy wszystkie scenariusze obsługiwane przez te dwa. Więcej informacji na ten temat można znaleźć w temacie [integrowanie tożsamości lokalnych z Azure Active Directory](whatis-hybrid-identity.md).

## <a name="deprecation-schedule"></a>Harmonogram wycofania
| Date (Data) | Komentarz |
| --- | --- |
| 13 kwietnia 2016 |Synchronizacja Azure Active Directory systemu Windows ("DirSync") i synchronizacja Microsoft Azure Active Directory ("Azure AD Sync") są anonsowane jako przestarzałe. |
| 13 kwietnia 2017 |Obsługa zakończona. Klienci nie będą już mogli otworzyć zgłoszenia do pomocy technicznej bez wcześniejszego uaktualnienia do Azure AD Connect. |
|31 grudnia 2017|Usługa Azure AD nie może już akceptować komunikacji z Azure Active Directory synchronizacji systemu Windows ("DirSync") i Microsoft Azure Active Directory synchronizacji ("Azure AD Sync").
|1 kwietnia, 2021| Synchronizacja Azure Active Directory systemu Windows ("DirSync") i synchronizacja Microsoft Azure Active Directory ("Azure AD Sync") nie będą już działać |

## <a name="how-to-transition-to-azure-ad-connect"></a>Jak przejść do Azure AD Connect
W przypadku korzystania z narzędzia DirSync istnieją dwa sposoby uaktualnienia: uaktualnienie w miejscu i wdrożenie równoległe. W przypadku większości klientów zaleca się uaktualnienie w miejscu, a w przypadku korzystania z najnowszego systemu operacyjnego i mniej niż 50 000 obiektów. W innych przypadkach zaleca się wdrożenie równoległe, w którym konfiguracja narzędzia DirSync jest przenoszona na nowy serwer z systemem Azure AD Connect.

| Rozwiązanie | Scenariusz |
| --- | --- |
| [Uaktualnianie z narzędzia DirSync](how-to-dirsync-upgrade-get-started.md) |<li>Jeśli masz już uruchomiony istniejący serwer DirSync.</li> |
| [Uaktualnij z Azure AD Sync](how-to-upgrade-previous-version.md) |<li>W przypadku przechodzenia z Azure AD Sync.</li> |

Jeśli chcesz dowiedzieć się, jak wykonać uaktualnienie w miejscu z narzędzia DirSync do Azure AD Connect, zobacz ten film wideo Channel 9:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-in-place-upgrade-from-legacy-tools/player]
>
>

## <a name="faq"></a>Często zadawane pytania
**Pytanie: otrzymałem powiadomienie e-mail od zespołu platformy Azure i/lub komunikat z centrum wiadomości Microsoft 365, ale używam funkcji Connect.**  
Powiadomienie zostało również wysłane do klientów przy użyciu Azure AD Connect z numerem kompilacji 1,0. \* .. 0 (użycie wersji pre-1,1). Firma Microsoft zaleca klientom bieżące korzystanie z wersji Azure AD Connect. Funkcja [uaktualniania automatycznego](how-to-connect-install-automatic-upgrade.md) wprowadzona w programie 1,1 ułatwia zawsze zainstalowanie najnowszej wersji Azure AD Connect.

**P: czy narzędzie DirSync/Azure AD Sync przestanie działać od 13 kwietnia 2017?**  
Narzędzie DirSync/Azure AD Sync będzie nadal działało w dniu 13 kwietnia 2017.  Jednak usługa Azure AD nie może już akceptować komunikacji z narzędzia DirSync/Azure AD Sync po 31 grudnia 2017. Narzędzia DirSync i Azure AD Sync nie będą już działać po 1 kwietnia, 2021

**P: które wersje narzędzia DirSync mogę uaktualnić?**  
Jest ona obsługiwana do uaktualnienia z dowolnego aktualnie używanego wydania narzędzia DirSync. 

**P: co to jest łącznik usługi Azure AD dla programu FIM/MIM?**  
Łącznik usługi Azure AD dla programu FIM/MIM **nie** został ogłoszony jako przestarzały. Jest ona **zawieszania funkcji**; nie dodano żadnych nowych funkcji i nie otrzymuje poprawek błędów. Firma Microsoft zaleca klientom korzystanie z tej usługi w celu zaplanowania przejścia od niego do Azure AD Connect. Zdecydowanie zaleca się, aby nie uruchamiać żadnych nowych wdrożeń, z których korzysta. Ten łącznik zostanie ogłoszony jako przestarzały w przyszłości.

## <a name="additional-resources"></a>Dodatkowe zasoby
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
