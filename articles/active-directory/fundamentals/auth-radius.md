---
title: Uwierzytelnianie usługi RADIUS za pomocą Azure Active Directory
description: Wskazówki dotyczące architektury dotyczące uzyskiwania uwierzytelniania usługi RADIUS za pomocą Azure Active Directory.
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
ms.openlocfilehash: 155b359c109de948ab9b9d6862ef7507ee76f619
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94576816"
---
# <a name="radius-authentication-with-azure-active-directory"></a>Uwierzytelnianie usługi RADIUS za pomocą Azure Active Directory

Usługa telefonujących użytkowników zdalnego uwierzytelniania (RADIUS) to protokół sieciowy, który zabezpiecza sieć przez umożliwienie scentralizowanego uwierzytelniania i autoryzacji użytkowników w telefonach. Wiele aplikacji nadal korzysta z protokołu RADIUS w celu uwierzytelniania użytkowników.

System Microsoft Windows Server ma rolę o nazwie serwer zasad sieciowych (NPS), który może działać jako serwer RADIUS i obsługiwał uwierzytelnianie usługi RADIUS.

Azure Active Directory (Azure AD) umożliwia uwierzytelnianie wieloskładnikowe z systemami opartymi na usłudze RADIUS. Jeśli klient chce zastosować Multi-Factor Authentication platformy Azure do któregokolwiek z wymienionych wcześniej obciążeń usługi RADIUS, może zainstalować rozszerzenie serwera zasad sieciowych platformy Azure Multi-Factor Authentication na swoim serwerze zasad sieciowych systemu Windows. 

Serwer NPS systemu Windows uwierzytelnia poświadczenia użytkownika w odniesieniu do Active Directory, a następnie wysyła żądanie Multi-Factor Authentication do platformy Azure. Następnie użytkownik otrzymuje wyzwanie dotyczące ich uwierzytelniającego urządzenia przenośnego. Po pomyślnym zakończeniu aplikacja kliencka będzie mogła nawiązać połączenie z usługą. 

## <a name="use-when"></a>Zastosowania: 

Musisz dodać Multi-Factor Authentication do aplikacji, takich jak
* Wirtualna sieć prywatna (VPN)
* Dostęp do sieci Wi-Fi
* Pulpit zdalny Gateway (RDG)
* Infrastruktura pulpitów wirtualnych (VDI)
* Wszystkie inne osoby, które zależą od protokołu RADIUS do uwierzytelniania użytkowników w usłudze. 

> [!NOTE]
> Zamiast polegać na usłudze RADIUS i rozszerzeniu zasad sieciowych Multi-Factor Authentication platformy Azure, aby zastosować usługę Azure Multi-Factor Authentication do obciążeń sieci VPN, zalecamy uaktualnienie sieci VPN do protokołu SAML i bezpośrednie sfederować sieci VPN za pomocą usługi Azure AD. Zapewnia to sieci VPN pełną szerokość ochrony usługi Azure AD, w tym dostęp warunkowy, Multi-Factor Authentication, zgodność urządzeń i ochronę tożsamości.

![Diagram architektoniczny](./media/authentication-patterns/radius-auth.png)


## <a name="components-of-the-system"></a>Składniki systemu 

* **Aplikacja kliencka (klient sieci VPN)** : wysyła żądanie uwierzytelnienia do klienta usługi RADIUS.

* **Klient RADIUS** : konwertuje żądania z aplikacji klienckiej i wysyła je na serwer RADIUS z zainstalowanym rozszerzeniem serwera NPS.

* **Serwer RADIUS** : nawiązuje połączenie z Active Directory, aby wykonać podstawowe uwierzytelnianie dla żądania usługi RADIUS. Po pomyślnym przekazanie żądania do usługi Azure Multi-Factor Authentication rozszerzenia serwera NPS.

* **Rozszerzenie serwera NPS** : wyzwala żądanie do usługi Azure Multi-Factor Authentication na potrzeby uwierzytelniania pomocniczego. Jeśli to się powiedzie, rozszerzenie serwera zasad sieciowych kończy żądanie uwierzytelnienia, dostarczając serwer RADIUS tokeny zabezpieczające, które zawierają Multi-Factor Authenticationego żądania wystawionego przez usługę tokenu zabezpieczającego platformy Azure.

* **Azure Multi-Factor Authentication** : komunikuje się z usługą Azure AD w celu pobrania szczegółów użytkownika i przeprowadzania dodatkowego uwierzytelniania przy użyciu metody weryfikacji skonfigurowanej przez użytkownika.

## <a name="implement-radius-with-azure-ad"></a>Implementowanie usługi RADIUS przy użyciu usługi Azure AD 

* [Udostępnianie możliwości Multi-Factor Authentication platformy Azure przy użyciu serwera zasad sieciowych](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-nps-extension) 

* [Konfigurowanie rozszerzenia serwera NPS usługi Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-nps-extension-advanced) 

* [Sieć VPN z usługą Azure Multi-Factor Authentication przy użyciu rozszerzenia serwera NPS](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-nps-extension-vpn) 

  
‎ 

 
