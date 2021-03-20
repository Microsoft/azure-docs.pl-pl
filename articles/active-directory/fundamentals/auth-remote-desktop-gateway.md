---
title: Pulpit zdalny usług bramy z Azure Active Directory
description: Wskazówki dotyczące architektury dotyczące uzyskiwania Pulpit zdalny usług bramy z Azure Active Directory.
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
ms.openlocfilehash: 4baaf2de6fbe4a56f64d449644b8594217dc432c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96172741"
---
# <a name="remote-desktop-gateway-services"></a>Pulpit zdalny usług bramy

Standardowe wdrożenie Usługi pulpitu zdalnego (RDS) obejmuje różne [usługi ról pulpit zdalny](/windows-server/remote/remote-desktop-services/Desktop-hosting-logical-architecture) działających w systemie Windows Server. Wdrożenie usług pulpitu zdalnego z serwerem proxy aplikacji Azure Active Directory (Azure AD) ma trwałe połączenie wychodzące z serwera, na którym działa usługa łącznika. Inne wdrożenia opuszczają otwarte połączenia przychodzące za pomocą modułu równoważenia obciążenia. Ten wzorzec uwierzytelniania umożliwia oferowanie więcej typów aplikacji przez publikowanie aplikacji lokalnych za pomocą Usługi pulpitu zdalnego. Zmniejsza również powierzchnię ataku na ich wdrożenie przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD.

## <a name="use-when"></a>Zastosowania

Musisz zapewnić dostęp zdalny i chronić wdrożenie Usługi pulpitu zdalnego przy użyciu uwierzytelniania wstępnego.

![Diagram architektoniczny](./media/authentication-patterns/rdp-auth.png)

## <a name="components-of-system"></a>Składniki systemu

* **Użytkownik**: uzyskuje dostęp do usług RDS obsłużonych przez serwer proxy aplikacji.

* **Przeglądarka sieci Web**: składnik, z którym pracuje użytkownik, aby uzyskać dostęp do zewnętrznego adresu URL aplikacji.

* **Azure AD**: uwierzytelnia użytkownika. 

* **Usługa serwera proxy aplikacji**: działa jako zwrotny serwer proxy do przesyłania dalej żądania od użytkownika do usług pulpitu zdalnego. Serwer proxy aplikacji może również wymusić wszelkie zasady dostępu warunkowego. 

* **Usługi pulpitu zdalnego**: działa jako platforma dla poszczególnych zwirtualizowanych aplikacji, zapewniając bezpieczny dostęp do pulpitu mobilnego i zdalnego oraz zapewnia użytkownikom końcowym możliwość uruchamiania aplikacji i pulpitów z chmury. 

## <a name="implement-remote-desktop-gateway-services-with-azure-ad"></a>Implementowanie usług Pulpit zdalny Gateway za pomocą usługi Azure AD

* [Publikowanie pulpitu zdalnego za pomocą usługi Azure serwer proxy aplikacji usługi Azure AD](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md) 

* [Dodawanie aplikacji lokalnej dla dostępu zdalnego za pomocą serwera proxy aplikacji w usłudze Azure AD](../manage-apps/application-proxy-add-on-premises-application.md)

