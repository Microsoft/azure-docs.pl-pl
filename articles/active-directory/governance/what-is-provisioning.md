---
title: Co to jest inicjowanie obsługi przy użyciu Azure Active Directory? | Microsoft Docs
description: Opisuje Omówienie aprowizacji tożsamości i scenariuszy ILM.
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
ms.openlocfilehash: 9ef6f6068bce7a676e55eca10ae9198b2238a143
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93135427"
---
# <a name="what-is-provisioning"></a>Co to jest aprowizacja?

Inicjowanie obsługi administracyjnej i cofanie aprowizacji to procesy, które zapewniają spójność tożsamości cyfrowych w wielu systemach.  Procesy te są zwykle wykorzystywane jako część [zarządzania cyklem życia tożsamości](what-is-identity-lifecycle-management.md).

**Inicjowanie obsługi** jest procesem tworzenia tożsamości w systemie docelowym na podstawie określonych warunków.  **Cofnięcie aprowizacji** polega na usunięciu tożsamości z systemu docelowego, gdy warunki nie są już spełnione. **Synchronizacja** to proces utrzymywania zainicjowanego obiektu, który jest aktualny, dzięki czemu obiekt źródłowy i obiekt docelowy są podobne.

Na przykład po dołączeniu nowego pracownika do organizacji ten pracownik jest wprowadzany do systemu kadr.  W tym momencie Inicjowanie obsługi **z** usługi HR **do** Azure Active Directory (Azure AD) może utworzyć odpowiednie konto użytkownika w usłudze Azure AD. Aplikacje, które wysyłają zapytania do usługi Azure AD, mogą zobaczyć konto dla tego nowego pracownika.  Jeśli istnieją aplikacje, które nie korzystają z usługi Azure AD, Inicjowanie obsługi administracyjnej **z** usługi Azure AD **do** baz danych aplikacji zapewnia użytkownikom dostęp do wszystkich aplikacji, do których użytkownik będzie potrzebował dostępu.  Ten proces umożliwia użytkownikowi uruchomienie pracy i uzyskanie dostępu do aplikacji i systemów, których potrzebują.  Podobnie, gdy ich właściwości, takie jak stan działu lub zatrudnienia, zmieniają się w systemie kadr, synchronizacja tych aktualizacji z systemu kadr do usługi Azure AD, a także do innych aplikacji i docelowych baz danych, zapewnia spójność.

Usługa Azure AD obecnie udostępnia trzy obszary automatycznej aprowizacji.  Oto one:  

- Inicjowanie obsługi administracyjnej niekatalogowego autorytatywnego systemu rekordów w usłudze Azure AD przy użyciu funkcji udostępniania na podstawie **[kadr](#hr-driven-provisioning)**  
- Inicjowanie obsługi administracyjnej z usługi Azure AD do aplikacji przy użyciu funkcji **[aprowizacji aplikacji](#app-provisioning)**  
- Inicjowanie obsługi administracyjnej między usługą Azure AD i Active Directory usług domenowych przy użyciu **[udostępniania między katalogami](#inter-directory-provisioning)** 

![Zarządzanie cyklem życia tożsamości](media/what-is-provisioning/provisioning.png)

## <a name="hr-driven-provisioning"></a>Obsługa administracyjna oparta na HR

![Inicjowanie obsługi kadr](media/what-is-provisioning/cloud-2a.png)

Inicjowanie obsługi z usługi HR w usłudze Azure AD obejmuje tworzenie obiektów, zazwyczaj tożsamości użytkowników reprezentujących każdego pracownika, ale w niektórych przypadkach inne obiekty reprezentujące działy lub inne struktury, na podstawie informacji znajdujących się w systemie kadr.  

Najbardziej typowym scenariuszem jest to, że w przypadku dołączenia nowego pracownika do firmy są one wprowadzane do systemu kadr.  Gdy to nastąpi, są one automatycznie inicjowane jako nowy użytkownik w usłudze Azure AD, bez konieczności zaangażowania administracyjnego dla każdego nowego zatrudniania.  Ogólnie rzecz biorąc, Inicjowanie obsługi z działu kadr może obejmować następujące scenariusze.

- **Zatrudnianie nowych pracowników** — po dodaniu nowego pracownika do systemu kadr, konto użytkownika jest automatycznie tworzone w Active Directory, Azure AD i opcjonalnie w katalogach dla innych aplikacji obsługiwanych przez usługę Azure AD z zwrotem adresu e-mail w systemie kadr.
- **Aktualizacje atrybutu pracownika i profilu** — po zaktualizowaniu rekordu pracownika w tym systemie kadry (na przykład nazwy, tytułu lub Menedżera) jego konto użytkownika zostanie automatycznie zaktualizowane w Active Directory, Azure AD i opcjonalnie inne aplikacje obsługiwane przez usługę Azure AD.
- **Zakończenie zatrudnienia pracownika** — gdy pracownik zostanie zakończony w HR, jego konto użytkownika jest automatycznie blokowane przed zalogowaniem się lub usunięciem w Active Directory, Azure AD i innych aplikacjach.
- **Pracownicy** przeniesieni przez pracowników — w przypadku, gdy pracownik jest ponownie zatrudniany w usłudze Cloud HR, jego stare konto można automatycznie ponownie uaktywnić lub zainicjować (w zależności od preferencji).

Istnieją trzy opcje wdrażania dla aprowizacji opartej na usłudze kadr w usłudze Azure AD:

1. W przypadku organizacji z pojedynczą subskrypcją produktu Workday lub SuccessFactors nie należy używać Active Directory
1. W przypadku organizacji z pojedynczą subskrypcją w dniach Workday lub SuccessFactors, które mają zarówno Active Directory, jak i usługę Azure AD
1. W przypadku organizacji z wieloma systemami kadr lub lokalnego systemu kadr, takiego jak SAP, Oracle eBusiness lub PeopleSoft

Aby uzyskać więcej informacji, zobacz [co to jest funkcja aprowizacji oparta na usłudze HR?](what-is-hr-driven-provisioning.md)

## <a name="app-provisioning"></a>Inicjowanie obsługi aplikacji

![Inicjowanie obsługi aplikacji](media/what-is-provisioning/cloud-3b.png)

W usłudze Azure AD termin **[aprowizacji aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)** dotyczy automatycznego tworzenia kopii tożsamości użytkowników w aplikacjach, do których użytkownicy potrzebują dostępu, w przypadku aplikacji, które mają własny magazyn danych, odrębnie od usługi Azure AD lub Active Directory. Oprócz tworzenia tożsamości użytkowników Inicjowanie obsługi aplikacji obejmuje konserwację i usuwanie tożsamości użytkowników z tych aplikacji, ponieważ zmienia się stan lub role użytkownika. Typowe scenariusze obejmują Inicjowanie obsługi użytkownika usługi Azure AD w aplikacjach, takich jak [Dropbox](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), [Salesforce](https://docs.microsoft.com/azure/active-directory/saas-apps/salesforce-provisioning-tutorial)i [usługi ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial), ponieważ każda z tych aplikacji ma własne repozytorium użytkowników, które różnią się od usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [co to jest inicjowanie obsługi aplikacji?](what-is-app-provisioning.md)

## <a name="inter-directory-provisioning"></a>Inicjowanie obsługi administracyjnej między katalogami

![Inicjowanie obsługi administracyjnej między katalogami](media/what-is-provisioning/cloud-4a.png)

Wiele organizacji korzysta z zarówno Active Directory, jak i usługi Azure AD i mogą mieć połączone aplikacje Active Directory, takie jak lokalne serwery plików.

Ponieważ wiele organizacji historycznie wdrożyło obsługę administracyjną w środowisku lokalnym, może mieć już tożsamość użytkowników dla wszystkich pracowników w Active Directory.   Najbardziej typowym scenariuszem aprowizacji między katalogami jest to, że użytkownik, który jest już w Active Directory jest zainicjowany do usługi Azure AD.  To Inicjowanie obsługi jest zwykle realizowane przez Azure AD Connect synchronizowanie lub Azure AD Connect aprowizacji w chmurze. 

Ponadto organizacje mogą chcieć również udostępnić systemy lokalne z usługi Azure AD.  Na przykład organizacja mogła wprowadzić Gości do katalogu usługi Azure AD, ale ci Goście muszą mieć dostęp do lokalnych aplikacji sieci Web opartych na zintegrowanym uwierzytelnianiu systemu Windows (WIA) za pośrednictwem serwera proxy aplikacji.  Wymaga to aprowizacji lokalnych kont usługi AD dla tych użytkowników w usłudze Azure AD.

Aby uzyskać więcej informacji, zobacz [co to jest inicjowanie obsługi administracyjnej między katalogami?](what-is-inter-directory-provisioning.md)

 
## <a name="next-steps"></a>Następne kroki 
- [Co to jest zarządzanie cyklem życia tożsamości?](what-is-identity-lifecycle-management.md)
- [Co to jest obsługa administracyjna oparta na HR?](what-is-hr-driven-provisioning.md)
- [Co to jest inicjowanie obsługi aplikacji?](what-is-app-provisioning.md)
- [Co to jest inicjowanie obsługi administracyjnej między katalogami?](what-is-inter-directory-provisioning.md)
