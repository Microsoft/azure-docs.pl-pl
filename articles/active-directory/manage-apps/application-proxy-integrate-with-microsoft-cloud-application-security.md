---
title: Integrowanie aplikacji lokalnych z usługą Cloud App Security — Azure AD
description: Skonfiguruj aplikację lokalną w Azure Active Directory do pracy z usługą Microsoft Cloud App Security (MCAS). Użyj Kontrola dostępu warunkowego aplikacji MCAS do monitorowania i kontrolowania sesji w czasie rzeczywistym na podstawie zasad dostępu warunkowego. Te zasady można stosować do aplikacji lokalnych, które używają serwera proxy aplikacji w Azure Active Directory (Azure AD).
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 12/19/2018
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 889168782acabaafa4200a8ebd097e431a68ede3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84764574"
---
# <a name="configure-real-time-application-access-monitoring-with-microsoft-cloud-app-security-and-azure-active-directory"></a>Konfigurowanie monitorowania dostępu do aplikacji w czasie rzeczywistym przy użyciu Microsoft Cloud App Security i Azure Active Directory
Skonfiguruj aplikację lokalną w Azure Active Directory (Azure AD), aby używać Microsoft Cloud App Security (MCAS) do monitorowania w czasie rzeczywistym. MCAS używa Kontrola dostępu warunkowego aplikacji do monitorowania i kontrolowania sesji w czasie rzeczywistym na podstawie zasad dostępu warunkowego. Te zasady można stosować do aplikacji lokalnych, które używają serwera proxy aplikacji w Azure Active Directory (Azure AD).

Poniżej przedstawiono kilka przykładów typów zasad, które można utworzyć za pomocą MCAS:

- Blokuj lub Chroń pobieranie poufnych dokumentów na urządzeniach niezarządzanych.
- Monitoruj, gdy użytkownicy o wysokim ryzyku logują się do aplikacji, a następnie rejestrują swoje działania w ramach sesji. Korzystając z tych informacji, można analizować zachowanie użytkowników w celu określenia sposobu stosowania zasad sesji.
- Używanie certyfikatów klienta lub zgodności urządzeń w celu blokowania dostępu do określonych aplikacji z urządzeń niezarządzanych.
- Ogranicz sesje użytkowników z sieci innych niż firmowe. Można udzielić ograniczonego dostępu użytkownikom, którzy uzyskują dostęp do aplikacji spoza sieci firmowej. Na przykład ten ograniczony dostęp może uniemożliwić użytkownikowi pobieranie poufnych dokumentów.

Aby uzyskać więcej informacji, zobacz [Ochrona aplikacji za pomocą Microsoft Cloud App Security kontrola dostępu warunkowego aplikacji](/cloud-app-security/proxy-intro-aad).

## <a name="requirements"></a>Wymagania

Licencjonowan

- Licencja EMS E5 lub 
- Azure Active Directory — wersja Premium w wersjach P1 i MCAS.

Aplikacja lokalna:

- Aplikacja lokalna musi korzystać z ograniczonego delegowania protokołu Kerberos (KCD)

Skonfiguruj serwer proxy aplikacji:

- Skonfiguruj usługę Azure AD do korzystania z serwera proxy aplikacji, w tym przygotowując środowisko i instalując łącznik serwera proxy aplikacji. Aby zapoznać się z samouczkiem, zobacz [Dodawanie aplikacji lokalnych dla dostępu zdalnego za pomocą serwera proxy aplikacji w usłudze Azure AD](application-proxy-add-on-premises-application.md). 

## <a name="add-on-premises-application-to-azure-ad"></a>Dodawanie aplikacji lokalnej do usługi Azure AD

Dodaj aplikację lokalną do usługi Azure AD. Aby zapoznać się z przewodnikiem Szybki Start, zobacz [Dodawanie aplikacji lokalnej do usługi Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). Podczas dodawania aplikacji Pamiętaj, aby ustawić następujące dwa ustawienia w bloku **Dodaj aplikację lokalną** :

- **Wstępne uwierzytelnianie**: wprowadź **Azure Active Directory**.
- **Tłumaczenie adresów URL w treści aplikacji**: wybierz opcję **tak**.

Te dwa ustawienia są wymagane, aby aplikacja działała z MCAS.

## <a name="test-the-on-premises-application"></a>Testowanie aplikacji lokalnej

Po dodaniu aplikacji do usługi Azure AD wykonaj kroki opisane w sekcji [testowanie aplikacji](application-proxy-add-on-premises-application.md#test-the-application) , aby dodać użytkownika do testowania i przetestuj logowanie. 

## <a name="deploy-conditional-access-app-control"></a>Wdróż Kontrola dostępu warunkowego aplikacji

Aby skonfigurować aplikację przy użyciu kontroli dostępu warunkowego aplikacji, postępuj zgodnie z instrukcjami w temacie [Wdrażanie dostępu warunkowego aplikacji kontrola dla aplikacji usługi Azure AD](/cloud-app-security/proxy-deployment-aad).


## <a name="test-conditional-access-app-control"></a>Kontrola dostępu warunkowego aplikacji testowe

Aby przetestować wdrażanie aplikacji usługi Azure AD przy użyciu kontroli dostępu warunkowego aplikacji, postępuj zgodnie z instrukcjami w temacie [testowanie wdrażania aplikacji usługi Azure AD](/cloud-app-security/proxy-deployment-aad).





