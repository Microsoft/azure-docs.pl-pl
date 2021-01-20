---
title: Co to jest inicjowanie obsługi tożsamości w usłudze Azure AD? | Microsoft Docs
description: Opisuje Omówienie aprowizacji tożsamości.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1c85f2a6d58a5dbeae93b951cea812d6aa91326
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98614829"
---
# <a name="what-is-identity-provisioning"></a>Co to jest aprowizacja tożsamości?

Obecnie małe i duże firmy w coraz większym stopniu korzystają zarówno z aplikacji lokalnych, jak i aplikacji w chmurze.  Użytkownicy potrzebują dostępu do aplikacji zarówno lokalnie, jak i w chmurze. Konieczne jest posiadanie jednej tożsamości między tymi różnymi aplikacjami (lokalnie i w chmurze).

Inicjowanie obsługi jest procesem tworzenia obiektu na podstawie określonych warunków, utrzymywania aktualnego obiektu i usuwania obiektu, gdy warunki nie są już spełnione. Na przykład gdy nowy użytkownik zostanie przyłączony do organizacji, ten użytkownik jest wprowadzany do systemu kadr.  W tym momencie Inicjowanie obsługi może utworzyć odpowiednie konto użytkownika w chmurze, w Active Directory i różne aplikacje, do których użytkownik będzie potrzebował dostępu.  Dzięki temu użytkownik może uruchamiać pracę i mieć dostęp do aplikacji i systemów, których potrzebują, w ciągu jednego dnia. 

![Diagram przedstawiający Inicjowanie obsługi chmury z Azure Active Directory.](media/what-is-provisioning/cloud-1.png)

W odniesieniu do Azure Active Directory Inicjowanie obsługi można podzielić na następujące kluczowe scenariusze.  

- **[Obsługa administracyjna oparta na HR](#hr-driven-provisioning)**  
- **[Inicjowanie obsługi aplikacji](#app-provisioning)**  
- **[Inicjowanie obsługi administracyjnej katalogów](#directory-provisioning)** 

## <a name="hr-driven-provisioning"></a>Obsługa administracyjna oparta na HR

![Diagram przedstawiający Inicjowanie obsługi przy użyciu usługi HR w chmurze, lokalne HR i Azure Active Directory.](media/what-is-provisioning/cloud-2.png)

Inicjowanie obsługi z poziomu usługi HR do chmury obejmuje tworzenie obiektów (użytkowników, ról, grup itp.) w oparciu o informacje znajdujące się w systemie kadr.  

Najbardziej typowym scenariuszem jest to, że w przypadku dołączenia nowego pracownika do firmy są one wprowadzane do systemu kadr.  Gdy to nastąpi, są one obsługiwane w chmurze.  W takim przypadku usługa Azure AD.  Inicjowanie obsługi administracyjnej z poziomu usługi kadr może obejmować następujące scenariusze. 

- **Zatrudnianie nowych pracowników** — po dodaniu nowego pracownika do usługi Cloud HR konto użytkownika jest automatycznie tworzone w Active Directory, Azure Active Directory i opcjonalnie Microsoft 365 i innych aplikacjach SaaS obsługiwanych przez usługę Azure AD, z możliwością zapisu z tyłu adresu e-mail w usłudze Cloud HR.
- **Aktualizacje atrybutu pracownika i profilu** — gdy rekord pracownika zostanie zaktualizowany w usłudze Cloud HR (takie jak nazwa, tytuł lub Menedżer), jego konto użytkownika zostanie automatycznie zaktualizowane w Active Directory, Azure Active Directory i opcjonalnie Microsoft 365 i innych aplikacjach SaaS obsługiwanych przez usługę Azure AD.
- **Zakończenie zatrudnienia pracownika** — po zakończeniu pracy pracownika w chmurze HR konto użytkownika jest automatycznie wyłączone w Active Directory, Azure Active Directory i opcjonalnie pakiet Office 365 i inne aplikacje SaaS obsługiwane przez usługę Azure AD.
- **Pracownicy** przeniesieni przez pracowników — w przypadku, gdy pracownik jest ponownie zatrudniany w usłudze Cloud HR, jego stare konto można automatycznie ponownie aktywować lub zainicjowanie (zależnie od preferencji) do Active Directory, Azure Active Directory i opcjonalnie Microsoft 365 i innych aplikacji SaaS obsługiwanych przez usługę Azure AD.


## <a name="app-provisioning"></a>Inicjowanie obsługi aplikacji

![Diagram przedstawiający Inicjowanie obsługi aplikacji za pomocą aplikacji lokalnych, aplikacji w chmurze innych niż firmy Microsoft i Azure Active Directory.](media/what-is-provisioning/cloud-3.png)

W Azure Active Directory (Azure AD) termin **[aprowizacji aplikacji](../app-provisioning/user-provisioning.md)** dotyczy automatycznego tworzenia tożsamości i ról użytkowników w aplikacjach w chmurze, do których użytkownicy potrzebują dostępu. Oprócz tworzenia tożsamości użytkowników automatyczne Inicjowanie obsługi obejmuje konserwację i usuwanie tożsamości użytkowników jako zmiany stanu lub ról. Typowe scenariusze obejmują Inicjowanie obsługi użytkownika usługi Azure AD w aplikacjach takich jak [Dropbox](../saas-apps/dropboxforbusiness-provisioning-tutorial.md), [Salesforce](../saas-apps/salesforce-provisioning-tutorial.md), [usługi ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md)i inne.

## <a name="directory-provisioning"></a>Inicjowanie obsługi administracyjnej katalogów

![Inicjowanie obsługi chmury](media/what-is-provisioning/cloud-4.png)

Lokalna obsługa administracyjna obejmuje Inicjowanie obsługi administracyjnej ze źródeł lokalnych (takich jak Active Directory) w usłudze Azure AD.  

Najbardziej typowym scenariuszem jest to, kiedy użytkownik w Active Directory (AD) jest zainicjowany do usługi Azure AD.

Jest to realizowane przez Azure AD Connect synchronizację, Azure AD Connect aprowizacji i Microsoft Identity Manager w chmurze. 
 
## <a name="next-steps"></a>Następne kroki 

- [Co to jest Azure AD Connect Sync Cloud?](what-is-cloud-sync.md)
- [Zainstaluj aprowizacji w chmurze](how-to-install.md)