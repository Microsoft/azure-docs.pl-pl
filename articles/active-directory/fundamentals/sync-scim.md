---
title: Standard scim synchronizacji z Azure Active Directory
description: Wskazówki dotyczące architektury w celu osiągnięcia synchronizacji Standard scim z Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f957070ec94fc4c61089f31fe91261a2f52c4ee4
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578862"
---
# <a name="scim-synchronization-with-azure-active-directory"></a>Standard scim synchronizacji z Azure Active Directory

System do zarządzania tożsamościami w wielu domenach (standard scim) to otwarty protokół standardowy służący do automatyzowania wymiany informacji o tożsamości użytkowników między domenami tożsamości i systemami IT. Standard scim gwarantuje, że pracownicy dodani do systemu zarządzania stolicą Kadr (HCM) automatycznie mają konta utworzone w Azure Active Directory (Azure AD) lub Windows Server Active Directory. Atrybuty i profile użytkownika są synchronizowane między tymi dwoma systemami, co umożliwia aktualizowanie usuwania użytkowników na podstawie stanu użytkownika lub zmiany roli.

Standard scim jest standardową definicją dwóch punktów końcowych:/Users "punkt końcowy i/Groups punkt końcowy. Używa wspólnych czasowników REST do tworzenia, aktualizowania i usuwania obiektów. Używa również wstępnie zdefiniowanego schematu dla wspólnych atrybutów, takich jak nazwa grupy, nazwa użytkownika, imię, nazwisko i adres e-mail. Aplikacje oferujące interfejs API REST w systemie Standard scim 2,0 mogą zmniejszyć lub wyeliminować możliwości pracy z własnościowymi interfejsami API zarządzania użytkownikami lub produktami. Na przykład dowolny klient zgodny z standard scim może dokonać wpisu HTTP obiektu JSON do punktu końcowego/users, aby utworzyć nowy wpis użytkownika. Zamiast niepotrzebnego nieco innego interfejsu API dla tych samych podstawowych akcji, aplikacje zgodne z standardem Standard scim mogą natychmiast korzystać z istniejących klientów, narzędzi i kodu. 

## <a name="use-when"></a>Zastosowania: 

Chcesz automatycznie udostępnić informacje o użytkowniku z systemu HCM w usłudze Azure AD i systemie Windows Server Active Directory, a następnie w razie potrzeby w systemach docelowych. 

![Diagram architektoniczny](./media/authentication-patterns/scim-auth.png)


## <a name="components-of-system"></a>Składniki systemu 

* **System HCM** : aplikacje i technologie, które umożliwiają proces zarządzania stolicą przez człowieka oraz praktyki, które obsługują i automatyzują procesy kadr w całym cyklu życia pracownika. 

* **Usługa Azure AD Provisioning** : używa protokołu Standard scim 2,0 do automatycznej aprowizacji. Usługa nawiązuje połączenie z punktem końcowym usługi Standard scim dla aplikacji, a następnie używa schematu obiektu użytkownika Standard scim i interfejsów API REST w celu zautomatyzowania aprowizacji i anulowania aprowizacji użytkowników i grup.  

* **Azure AD** : repozytorium użytkowników używane do zarządzania cyklem życia tożsamości i ich uprawnień. 

* **System docelowy** : aplikacja lub system z punktem końcowym Standard scim i współpracuje z obsługą administracyjną usługi Azure AD w celu włączenia automatycznej aprowizacji użytkowników i grup.  

## <a name="implement-scim-with-azure-ad"></a>Implementowanie Standard scim przy użyciu usługi Azure AD 

* [Jak działa Inicjowanie obsługi w usłudze Azure AD ](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works)

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw w Azure Portal ](https://docs.microsoft.com/azure/active-directory/app-provisioning/configure-automatic-user-provisioning-portal)

* [Tworzenie punktu końcowego Standard scim i Konfigurowanie aprowizacji użytkowników przy użyciu usługi Azure AD  ](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups)

* [Zgodność protokołów Standard scim 2,0 z usługą Azure AD Provisioning](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-scim-compatibility)

