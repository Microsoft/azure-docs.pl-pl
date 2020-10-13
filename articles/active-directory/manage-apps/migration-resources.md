---
title: Zasoby do migrowania aplikacji do Azure Active Directory | Microsoft Docs
description: Zasoby ułatwiające Migrowanie dostępu do aplikacji i ich uwierzytelniania do Azure Active Directory (Azure AD).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 02/29/2020
ms.author: kenwith
ms.reviewer: baselden
ms.openlocfilehash: 327e59d3fee6f23d2635b846e9fb5ab422e0139e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87429029"
---
# <a name="resources-for-migrating-applications-to-azure-active-directory"></a>Zasoby do migrowania aplikacji do Azure Active Directory

Zasoby ułatwiające Migrowanie dostępu do aplikacji i ich uwierzytelniania do Azure Active Directory (Azure AD).

| Zasób  | Opis  |
|:-----------|:-------------|
|[Migrowanie aplikacji do usługi Azure AD](https://aka.ms/migrateapps/whitepaper) | Ten oficjalny dokument przedstawia zalety migracji i opisuje sposób planowania migracji w czterech jasno dostępnych fazach: odnajdywanie, klasyfikacja, migracja i bieżące zarządzanie. Zapoznaj się z informacjami o tym, jak myśleć o procesie i podzielić projekt na łatwe w użyciu elementy. W dokumencie znajdują się linki do ważnych zasobów, które pomogą Ci w tym czasie. |
|[Przewodnik po rozwiązaniu: Migrowanie aplikacji z Active Directory Federation Services (AD FS) do usługi Azure AD](https://aka.ms/migrateapps/adfssolutionguide) | Ten przewodnik po rozwiązaniu przeprowadzi Cię przez te same cztery etapy planowania i wykonywania projektu migracji aplikacji opisanego na wyższym poziomie w oficjalnym dokumencie dotyczącym migracji. W tym przewodniku dowiesz się, jak zastosować te fazy do określonego celu przeniesienia aplikacji z usług federacyjnych w usłudze Azure Directory (AD FS) do usługi Azure AD.|
| [Narzędzie: skrypt gotowości do Active Directory Federation Services migracji](https://aka.ms/migrateapps/adfstools) | Jest to skrypt, który można uruchomić na serwerze lokalnym Active Directory Federation Services (AD FS), aby określić gotowość aplikacji do migracji do usługi Azure AD.|
| [Plan wdrożenia: Migrowanie z AD FS do synchronizacji skrótów haseł](https://aka.ms/ADFSTOPHSDPDownload) | W przypadku synchronizacji skrótów haseł skróty użytkowników są synchronizowane z Active Directory lokalnych do usługi Azure AD. Dzięki temu usługa Azure AD może uwierzytelniać użytkowników bez konieczności korzystania z Active Directory lokalnych.| 
| [Plan wdrożenia: Migrowanie z AD FS do uwierzytelniania przekazywanego](https://aka.ms/ADFSTOPTADPDownload)|Uwierzytelnianie przekazywane przez usługę Azure AD ułatwia użytkownikom logowanie się do aplikacji lokalnych i opartych na chmurze przy użyciu tego samego hasła. Ta funkcja zapewnia użytkownikom lepszy komfort pracy, ponieważ mają one mniej hasła. Zmniejsza to również koszty pomocy technicznej działu IT, ponieważ użytkownicy nie mogą zapomnieć, jak zalogować się tylko po zapamiętaniu jednego hasła. Gdy użytkownicy logują się za pomocą usługi Azure AD, ta funkcja weryfikuje ich hasła bezpośrednio w lokalnej usłudze Active Directory.|
| [Plan wdrożenia: Włączanie logowania jednokrotnego do aplikacji SaaS przy użyciu usługi Azure AD](https://aka.ms/SSODPDownload) | Logowanie jednokrotne (SSO) pomaga uzyskać dostęp do wszystkich aplikacji i zasobów, które są potrzebne do prowadzenia działalności, podczas logowania tylko raz przy użyciu jednego konta użytkownika. Na przykład po zalogowaniu się użytkownika użytkownik może przejść z Microsoft Office, do usługi SalesForce, do usługi Box bez uwierzytelniania (na przykład wpisując hasło) po raz drugi. 
| [Plan wdrożenia: rozszerzanie aplikacji do usługi Azure AD za pomocą serwera proxy aplikacji](https://aka.ms/AppProxyDPDownload)| Zapewnianie dostępu od laptopów pracowników i innych urządzeń do aplikacji lokalnych ma tradycyjnie związane wirtualne sieci prywatne (VPN) lub strefy zdemilitaryzowana (stref DMZ). Te rozwiązania nie tylko są skomplikowane i trudne do zabezpieczenia, ale też mają wysokie koszty konfigurowania i zarządzania. Usługa Azure serwer proxy aplikacji usługi Azure AD ułatwia dostęp do aplikacji lokalnych. |
| [Plany wdrażania](../fundamentals/active-directory-deployment-plans.md) | Znajdź więcej planów wdrażania dotyczących wdrażania funkcji, takich jak uwierzytelnianie wieloskładnikowe, dostęp warunkowy, Inicjowanie obsługi użytkowników, bezproblemowe logowanie jednokrotne, Samoobsługowe resetowanie haseł i inne funkcje. |


