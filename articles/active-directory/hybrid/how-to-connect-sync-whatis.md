---
title: 'Azure AD Connect Sync: omówienie i dostosowanie synchronizacji | Microsoft Docs'
description: Wyjaśnia, jak działa synchronizacja Azure AD Connect i jak je dostosować.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ee4bf802-045b-4da0-986e-90aba2de58d6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/08/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cea26cb119f64679807bc6c5eaadb41b341e5d5a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89662380"
---
# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Synchronizacja programu Azure AD Connect: omówienie i dostosowywanie synchronizacji
Usługi synchronizacji Azure Active Directory Connect (Azure AD Connect Sync) są głównym składnikiem Azure AD Connect. Zajmuje ona wszystkie operacje związane z synchronizacją danych tożsamości między środowiskiem lokalnym i usługą Azure AD. Azure AD Connect Sync to następca narzędzi DirSync, Azure AD Sync i Forefront Identity Manager z skonfigurowanym łącznikiem Azure Active Directory.

Ten temat jest domem dla **Azure AD Connect synchronizacji** (nazywanego także **aparatem synchronizacji**) i zawiera linki do wszystkich innych tematów związanych z nim. Aby uzyskać linki do Azure AD Connect, zobacz [integrowanie tożsamości lokalnych z Azure Active Directory](whatis-hybrid-identity.md).

Usługa synchronizacji składa się z dwóch składników, składnika **synchronizacji Azure AD Connect** lokalnego i usługi w usłudze Azure AD o nazwie **usługa synchronizacji Azure AD Connect**.

## <a name="azure-ad-connect-sync-topics"></a>Tematy dotyczące synchronizacji Azure AD Connect
| Temat | Co obejmuje i kiedy należy przeczytać |
| --- | --- |
| **Podstawy synchronizacji Azure AD Connect** | |
| [Opis architektury](concept-azure-ad-connect-sync-architecture.md) |Dla osób, które są nowe dla aparatu synchronizacji i chcą poznać architekturę i używane terminy. |
| [Koncepcje techniczne](how-to-connect-sync-technical-concepts.md) |Krótka wersja tematu architektura i krótkie objaśnienie użytych terminów. |
| [Topologie obsługiwane w programie Azure AD Connect](plan-connect-topologies.md) |Opisuje różne topologie i scenariusze obsługiwane przez aparat synchronizacji. |
| **Konfiguracja niestandardowa** | |
| [Ponowne uruchamianie Kreatora instalacji](how-to-connect-installation-wizard.md) |Wyjaśnia, jakie opcje są dostępne po ponownym uruchomieniu Kreatora instalacji Azure AD Connect. |
| [Omówienie aprowizacji deklaratywnej](concept-azure-ad-connect-sync-declarative-provisioning.md) |Opisuje model konfiguracji o nazwie deklaracyjne Inicjowanie obsługi. |
| [Opis wyrażeń związanych z aprowizacją deklaratywną](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) |Opisuje składnię języka wyrażeń używanego w aprowizacji deklaracyjnej. |
| [Opis konfiguracji domyślnej](concept-azure-ad-connect-sync-default-configuration.md) |Zawiera opis reguł out-of-Box i konfiguracji domyślnej. Opisuje również sposób, w jaki reguły współpracują ze sobą, aby można było korzystać ze swoich scenariuszy. |
| [Informacje o użytkownikach i kontaktach](concept-azure-ad-connect-sync-user-and-contacts.md) |Kontynuuje działanie w poprzednim temacie i opisuje, w jaki sposób Konfiguracja użytkowników i kontaktów działa razem, w szczególności w środowisku z obsługą kilku lasów. |
| [Jak wprowadzić zmianę konfiguracji domyślnej](how-to-connect-sync-change-the-configuration.md) |Przeprowadzi Cię przez proces wykonywania typowej zmiany konfiguracji przepływów atrybutów. |
| [Najlepsze rozwiązania dotyczące zmieniania konfiguracji domyślnej](how-to-connect-sync-best-practices-changing-default-configuration.md) |Obsługa ograniczeń i wprowadzanie zmian w konfiguracji wstępnej. |
| [Konfigurowanie filtrowania](how-to-connect-sync-configure-filtering.md) |Opisuje różne opcje dotyczące sposobu ograniczania, które obiekty są synchronizowane z usługą Azure AD, oraz krok po kroku, jak skonfigurować te opcje. |
| **Funkcje i scenariusze** | |
| [Zapobieganie przypadkowemu usuwaniu](how-to-connect-sync-feature-prevent-accidental-deletes.md) |Opisuje funkcję *zapobiegania przypadkowemu usuwaniu* i konfigurowania jej. |
| [Scheduler](how-to-connect-sync-feature-scheduler.md) |Opisuje wbudowany harmonogram, który importuje, synchronizuje i eksportuje dane. |
| [Implementowanie synchronizacji skrótów haseł](how-to-connect-password-hash-synchronization.md) |Opisuje sposób działania synchronizacji haseł, sposobu wdrażania i sposobu działania oraz rozwiązywania problemów. |
| [Zapisywanie zwrotne urządzeń](how-to-connect-device-writeback.md) |Opisuje sposób działania funkcji zapisywania zwrotnego urządzeń w Azure AD Connect. |
| [Rozszerzenia katalogów](how-to-connect-sync-feature-directory-extensions.md) |Opisuje, jak zwiększyć schemat usługi Azure AD przy użyciu własnych atrybutów niestandardowych. |
| [Microsoft 365 PreferredDataLocation](how-to-connect-sync-feature-preferreddatalocation.md) |Opisuje sposób umieszczania zasobów Microsoft 365 użytkownika w tym samym regionie, w którym znajduje się użytkownik. |
| **Usługa synchronizacji** | |
| [Funkcje usługi synchronizacji programu Azure AD Connect](how-to-connect-syncservice-features.md) |Zawiera opis strony usługi synchronizacji oraz sposobu zmiany ustawień synchronizacji w usłudze Azure AD. |
| [Odporność na zduplikowane atrybuty](how-to-connect-syncservice-duplicate-attribute-resiliency.md) |Opisuje sposób włączania i używania wartości właściwości **userPrincipalName** i **proxyAddresses** duplikatów atrybutów. |
| **Operacje i interfejs użytkownika** | |
| [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md) |Opisuje interfejs użytkownika Synchronization Service Manager, w tym [operacje](how-to-connect-sync-service-manager-ui-operations.md), [Łączniki](how-to-connect-sync-service-manager-ui-connectors.md), [projektanta Metaverse](how-to-connect-sync-service-manager-ui-mvdesigner.md)i karty [wyszukiwania Metaverse](how-to-connect-sync-service-manager-ui-mvsearch.md) . |
| [Zadania operacyjne i zagadnienia](./how-to-connect-sync-staging-server.md) |Opisuje problemy operacyjne, takie jak odzyskiwanie po awarii. |
| **Jak...** | |
| [Zresetuj konto usługi Azure AD](how-to-connect-azureadaccount.md) |Jak zresetować poświadczenia konta usługi używanego do łączenia się z Azure AD Connect synchronizacji z usługą Azure AD. |
| **Więcej informacji i odwołań** | |
| [Porty](reference-connect-ports.md) |Wyświetla listę portów, które należy otworzyć między aparatem synchronizacji i lokalnymi katalogami i usługą Azure AD. |
| [Atrybuty synchronizowane z usługą Azure Active Directory](reference-connect-sync-attributes-synchronized.md) |Wyświetla wszystkie atrybuty synchronizowane między lokalną usługą AD i usługą Azure AD. |
| [Informacje o funkcjach](reference-connect-sync-functions-reference.md) |Wyświetla listę wszystkich funkcji dostępnych w ramach aprowizacji deklaracyjnej. |

## <a name="additional-resources"></a>Dodatkowe zasoby
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)