---
title: Porównanie warstw API Management platformy Azure w oparciu o funkcję | Microsoft Docs
description: W tym artykule porównano API Management warstw na podstawie oferowanych przez nie funkcji.
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/15/2020
ms.author: apimpm
ms.openlocfilehash: ef16339d9a584eb12c611a25d636a9287265953a
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87284340"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Porównanie warstw API Management platformy Azure oparte na funkcjach

Każda [warstwa cenowa](https://aka.ms/apimpricing) API Management oferuje odrębny zestaw funkcji i poszczególnych [pojemności](api-management-capacity.md)jednostek. Poniższa tabela zawiera podsumowanie najważniejszych funkcji dostępnych w poszczególnych warstwach. Niektóre funkcje mogą współpracować inaczej lub mieć różne możliwości w zależności od warstwy. W takich przypadkach różnice są określane w artykułach dokumentacji opisującej te poszczególne funkcje.

> [!IMPORTANT]
> Zwróć uwagę na to, że warstwa dewelopera dotyczy nieprodukcyjnych przypadków użycia i ocen. Nie oferuje umowy SLA.

| Cechy                                                                                      | Zużycie | Deweloper | Podstawowa | Standardowa (Standard) | Premium |
| -------------------------------------------------------------------------------------------- | ----------- | --------- | ----- | -------- | ------- |
| Integracja z usługą Azure AD<sup>1</sup>                                                             | Nie          | Yes       | Nie    | Tak      | Tak     |
| Obsługa Virtual Network (VNet)                                                               | Nie          | Yes       | Nie    | Nie       | Yes     |
| Wdrażanie w wielu regionach                                                                      | Nie          | Nie        | Nie    | Nie       | Yes     |
| Wiele niestandardowych nazw domen                                                                 | Nie          | Yes        | Nie    | Nie       | Yes     |
| Portal dla deweloperów<sup>2</sup>                                                                 | Nie          | Tak       | Tak   | Tak      | Tak     |
| Wbudowana pamięć podręczna                                                                               | Nie          | Tak       | Tak   | Tak      | Tak     |
| Wbudowana analiza                                                                           | Nie          | Tak       | Tak   | Tak      | Tak     |
| [Samodzielna Brama](self-hosted-gateway-overview.md)<sup>3</sup>                           | Nie          | Yes       | Nie    | Nie       | Yes     |
| [Ustawienia protokołu TLS](api-management-howto-manage-protocols-ciphers.md)                             | Tak         | Tak       | Tak   | Tak      | Tak     |
| [Zewnętrzna pamięć podręczna](https://aka.ms/apimbyoc)                                                    | Tak         | Tak       | Tak   | Tak      | Tak     |
| [Uwierzytelnianie certyfikatu klienta](api-management-howto-mutual-certificates-for-clients.md) | Tak         | Tak       | Tak   | Tak      | Tak     |
| [Tworzenie kopii zapasowej i przywracanie](api-management-howto-disaster-recovery-backup-restore.md)               | Nie          | Tak       | Tak   | Tak      | Tak     |
| [Zarządzanie za pośrednictwem usługi git](api-management-configuration-repository-git.md)                        | Nie          | Tak       | Tak   | Tak      | Tak     |
| Interfejs API zarządzania bezpośredniego                                                                        | Nie          | Tak       | Tak   | Tak      | Tak     |
| Azure Monitor dzienników i metryk                                                               | Tak         | Tak       | Tak   | Tak      | Tak     |
| Statyczny adres IP                                                                                    | Nie          | Yes       | Tak   | Tak      | Tak     |

<sup>1</sup> umożliwia korzystanie z usługi Azure AD (i Azure AD B2C) jako dostawcy tożsamości na potrzeby logowania użytkownika w portalu dla deweloperów.<br/>
<sup>2</sup> w tym powiązane funkcje, takie jak użytkownicy, grupy, problemy, aplikacje i szablony wiadomości e-mail i powiadomienia.<br/>
<sup>3</sup> w bramach samoobsługowych w warstwie dewelopera są ograniczone do jednego węzła bramy.<br/>
