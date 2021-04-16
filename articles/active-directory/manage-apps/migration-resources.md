---
title: Zasoby dotyczące migrowania aplikacji do Azure Active Directory | Microsoft Docs
description: Zasoby, które ułatwiają migrację dostępu do aplikacji i uwierzytelniania do Azure Active Directory (Azure AD).
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 02/29/2020
ms.author: iangithinji
ms.reviewer: baselden
ms.openlocfilehash: 2d01c174bbfa522700773b87737b1e3da2de422e
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376651"
---
# <a name="resources-for-migrating-applications-to-azure-active-directory"></a>Zasoby pomocne przy migrowaniu aplikacji do usługi Azure Active Directory

Zasoby, które ułatwiają migrację dostępu do aplikacji i uwierzytelniania do Azure Active Directory (Azure AD).

| Zasób  | Opis  |
|:-----------|:-------------|
|[Migrowanie aplikacji do usługi Azure AD](https://aka.ms/migrateapps/whitepaper) | Ten oficjalny dokument przedstawia korzyści wynikające z migracji i opisuje sposób planowania migracji w czterech wyraźnie przedstawionych fazach: odnajdywanie, klasyfikacja, migracja i bieżące zarządzanie. Dowiesz się, jak myśleć o procesie i tworzyć łatwe w użyciu elementy projektu. W całym dokumencie znajdują się linki do ważnych zasobów, które pomogą Ci w drodze. |
|[Przewodnik po rozwiązaniu: Migrowanie aplikacji z Active Directory Federation Services (AD FS) do usługi Azure AD](./migrate-adfs-apps-to-azure.md) | W tym przewodniku po rozwiązaniu opisano te same cztery fazy planowania i wykonywania projektu migracji aplikacji opisane na wyższym poziomie w oficjalnych planach migracji. W tym przewodniku dowiesz się, jak zastosować te fazy do określonego celu, jakim jest przeniesienie aplikacji z usług Azure Directory Federated Services (AD FS) do usługi Azure AD.|
|[Samouczek dla deweloperów: AD FS migracji aplikacji usługi Azure AD dla deweloperów](https://aka.ms/adfsplaybook) | Ten zestaw przykładów kodu ASP.NET i towarzyszących samouczków pomoże Ci dowiedzieć się, jak bezpiecznie i bezpiecznie migrować aplikacje zintegrowane z usługą Active Directory Federation Services (AD FS) do usługi Azure Active Directory (Azure AD). Ten samouczek jest ukierunkowany na deweloperów, którzy nie tylko muszą nauczyć się konfigurować aplikacje zarówno w usłudze AD FS, jak i w usłudze Azure AD, ale także stają się świadomymi i pewnymi zmianami, których wymaga baza kodu w tym procesie.|
| [Narzędzie: Active Directory Federation Services gotowości do migracji](https://aka.ms/migrateapps/adfstools) | Jest to skrypt, który można uruchomić na serwerze lokalna usługa Active Directory Federation Services (AD FS), aby określić gotowość aplikacji do migracji do usługi Azure AD.|
| [Plan wdrożenia: migracja z usługi AD FS do synchronizacji skrótów haseł](https://aka.ms/ADFSTOPHSDPDownload) | W przypadku synchronizacji skrótów haseł skróty haseł użytkowników są synchronizowane lokalna usługa Active Directory z usługą Azure AD. Dzięki temu usługa Azure AD może uwierzytelniać użytkowników bez interakcji z lokalna usługa Active Directory.| 
| [Plan wdrożenia: Migracja z AD FS do uwierzytelniania pass-through](https://aka.ms/ADFSTOPTADPDownload)|Uwierzytelnianie pass-through usługi Azure AD ułatwia użytkownikom logowanie się do aplikacji lokalnych i opartych na chmurze przy użyciu tego samego hasła. Ta funkcja zapewnia użytkownikom lepsze środowisko, ponieważ mają o jedno hasło do zapamiętania mniej. Zmniejsza to również koszty pomocy technicznej IT, ponieważ istnieje mniejsze prawdopodobieństwo, że użytkownicy zapomnią, jak się zalogować, gdy muszą zapamiętać tylko jedno hasło. Gdy użytkownicy logują się za pomocą usługi Azure AD, ta funkcja weryfikuje ich hasła bezpośrednio w lokalnej usłudze Active Directory.|
| [Plan wdrożenia: włączanie logowania pojedynczego do aplikacji SaaS przy użyciu usługi Azure AD](https://aka.ms/SSODPDownload) | Logowanie jednokrotne ułatwia dostęp do wszystkich aplikacji i zasobów potrzebnych do prowadzenia działalności, a logowanie jednokrotne przy użyciu jednego konta użytkownika. Na przykład po zalogowaniu się użytkownika użytkownik może przejść z usługi Microsoft Office do usługi SalesForce do usługi Box bez uwierzytelniania (na przykład wpisywania hasła) po raz drugi. 
| [Plan wdrożenia: rozszerzanie aplikacji na usługę Azure AD przy użyciu serwer proxy aplikacji](https://aka.ms/AppProxyDPDownload)| Zapewnienie dostępu z laptopów pracowników i innych urządzeń do aplikacji lokalnych tradycyjnie dotyczyło wirtualnych sieci prywatnych (VPN) lub stref zdemilitaryzowanych (DMZ). Te rozwiązania nie tylko są skomplikowane i trudne do zabezpieczenia, ale też mają wysokie koszty konfigurowania i zarządzania. Usługa Azure AD serwer proxy aplikacji ułatwia dostęp do aplikacji lokalnych. |
| [Plany wdrażania](../fundamentals/active-directory-deployment-plans.md) | Znajdź więcej planów wdrażania funkcji, takich jak uwierzytelnianie wieloskładnikowe, dostęp warunkowy, aprowizowanie użytkowników, bezproblemowe logowanie jednokrotne, samoobsługowe resetowanie haseł i nie tylko. |