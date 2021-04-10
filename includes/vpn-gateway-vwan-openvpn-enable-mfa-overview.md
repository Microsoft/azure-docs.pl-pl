---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8fba8aa577bcb3b5ef44d57c388a1f1de7494782
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95995623"
---
Jeśli chcesz, aby użytkownicy otrzymali monit o drugi czynnik uwierzytelniania przed udzieleniem dostępu, możesz skonfigurować usługę Azure AD Multi-Factor Authentication (MFA). Uwierzytelnianie wieloskładnikowe można skonfigurować dla poszczególnych użytkowników lub użyć usługi MFA za pośrednictwem [dostępu warunkowego](../articles/active-directory/conditional-access/overview.md).

* Usługę MFA na użytkownika można włączyć bez dodatkowych kosztów. Po włączeniu usługi MFA na użytkownika zostanie wyświetlony monit o uwierzytelnienie drugiego czynnika dla wszystkich aplikacji powiązanych z dzierżawą usługi Azure AD. Zobacz [opcję 1](#peruser) , aby uzyskać instrukcje.
* Dostęp warunkowy umożliwia dokładniejszą kontrolę nad sposobem promowania drugiego czynnika. Może zezwalać na przypisanie uwierzytelniania MFA tylko do sieci VPN i wykluczać inne aplikacje powiązane z dzierżawą usługi Azure AD. Aby uzyskać instrukcje, zobacz [opcję 2](#conditional) .