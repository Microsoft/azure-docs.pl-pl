---
title: Co to jest inicjowanie obsługi administracyjnej między katalogami Azure Active Directory? | Microsoft Docs
description: Opisuje Omówienie aprowizacji tożsamości między katalogami.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cbca2d00d10b6595b8a1d13251dd88c65667541
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93135350"
---
# <a name="what-is-inter-directory-provisioning"></a>Co to jest inicjowanie obsługi administracyjnej między katalogami?

Katalog to udostępniona infrastruktura informacyjna, która jest używana do lokalizowania, zarządzania, administrowania i organizowania elementów oraz zasobów sieciowych.  Przykładami aplikacji, które korzystają z usług katalogowych, są Microsoft Active Directory i Azure AD.  Tożsamości ułatwiają określenie, takich jak kto ma dostęp do tego, co i kto może korzystać z określonych zasobów.

Inicjowanie obsługi administracyjnej między katalogami zapewnia tożsamość między dwoma różnymi systemami usług katalogowych.   Najbardziej typowym scenariuszem aprowizacji między katalogami jest to, że użytkownik, który jest już w Active Directory jest zainicjowany do usługi Azure AD. Ta obsługa administracyjna może być realizowana przez agentów takich jak Azure AD Connect Sync lub Azure AD Connect aprowizacji w chmurze.

Inicjowanie obsługi administracyjnej między katalogami pozwala nam tworzyć [hybrydowe środowiska tożsamości](../hybrid/whatis-hybrid-identity.md) .


## <a name="what-types-of-inter-directory-provisioning-does-azure-ad-support"></a>Jakie typy aprowizacji między katalogami obsługuje usługa Azure AD

Usługa Azure AD obecnie obsługuje trzy metody inicjowania obsługi administracyjnej między katalogami. Są to następujące metody:

- [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) — narzędzie firmy Microsoft zaprojektowane do zaspokajania i wykonywania tożsamości hybrydowej, w tym do inicjowania obsługi administracyjnej między usługą Active Directory do usługi Azure AD.

- [Azure AD Connect aprowizacji w chmurze](../cloud-provisioning/what-is-cloud-provisioning.md) — nowy Agent firmy Microsoft, który zaprojektowano w celu sprostania i realizacji celów tożsamości hybrydowej.  Zapewnia ona uproszczone środowisko udostępniania między katalogami między Active Directory i Azure AD.

- [Microsoft Identity Manager](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016) — lokalne rozwiązanie do zarządzania tożsamościami i dostępem w firmie Microsoft, które ułatwia zarządzanie użytkownikami, poświadczeniami, zasadami i dostępem w organizacji. Ponadto program MIM udostępnia zaawansowane Inicjowanie obsługi administracyjnej między katalogami w celu osiągnięcia hybrydowych środowisk tożsamości dla Active Directory, usługi Azure AD i innych katalogów.

### <a name="key-benefits"></a>Najważniejsze korzyści

Ta funkcja aprowizacji między katalogami oferuje następujące korzyści biznesowe:

- [Synchronizacja skrótów haseł](../hybrid/whatis-phs.md) — metoda logowania, która umożliwia synchronizowanie skrótów haseł lokalnych użytkowników usługi AD w usłudze Azure AD.
- [Uwierzytelnianie przekazywane](../hybrid/how-to-connect-pta.md) — metoda logowania, która pozwala użytkownikom używać tego samego hasła w infrastrukturze lokalnej i w chmurze, ale nie wymaga dodatkowej infrastruktury w środowisku federacyjnym.
- [Integracja Federacji](../hybrid/how-to-connect-fed-whatis.md) — może służyć do konfigurowania środowiska hybrydowego przy użyciu lokalnej infrastruktury AD FS. Składnik ten udostępnia również funkcje zarządzania usługami AD FS, takie jak odnawianie certyfikatów i dodatkowe wdrożenia serwera usług AD FS.
- [Synchronizacja](../hybrid/how-to-connect-sync-whatis.md) — odpowiada za tworzenie użytkowników, grup i innych obiektów.  Odpowiada też za zapewnienie zgodności informacji o tożsamości lokalnych użytkowników i grup z informacjami w chmurze.  Synchronizacja dotyczy również skrótów haseł.
- [Monitorowanie kondycji](../hybrid/whatis-hybrid-identity-health.md) — może zapewnić niezawodne monitorowanie i zapewnić centralną lokalizację w Azure Portal, aby wyświetlić to działanie. 


## <a name="next-steps"></a>Następne kroki 
- [Co to jest zarządzanie cyklem życia tożsamości](what-is-identity-lifecycle-management.md)
- [Co to jest aprowizacja?](what-is-provisioning.md)
- [Co to jest obsługa administracyjna oparta na HR?](what-is-hr-driven-provisioning.md)
- [Co to jest inicjowanie obsługi aplikacji?](what-is-app-provisioning.md)
