---
title: dołączanie pliku
description: dołączanie pliku
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3417bf0bd4ae1e0aa670f9fbfcc1fbbfeb372972
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77471553"
---
Jeśli chcesz, aby użytkownicy otrzymali monit o drugi czynnik uwierzytelniania przed udzieleniem dostępu, możesz skonfigurować usługę Azure Multi-Factor Authentication (MFA). Uwierzytelnianie wieloskładnikowe można skonfigurować dla poszczególnych użytkowników lub użyć usługi MFA za pośrednictwem [dostępu warunkowego](../articles/active-directory/conditional-access/overview.md).

* Usługę MFA na użytkownika można włączyć bez dodatkowych kosztów. Po włączeniu usługi MFA na użytkownika zostanie wyświetlony monit o uwierzytelnienie drugiego czynnika dla wszystkich aplikacji powiązanych z dzierżawą usługi Azure AD. Zobacz [opcję 1](#peruser) , aby uzyskać instrukcje.
* Dostęp warunkowy umożliwia dokładniejszą kontrolę nad sposobem promowania drugiego czynnika. Może zezwalać na przypisanie uwierzytelniania MFA tylko do sieci VPN i wykluczać inne aplikacje powiązane z dzierżawą usługi Azure AD. Aby uzyskać instrukcje, zobacz [opcję 2](#conditional) .