---
title: Porównanie warstw usługi Azure API Management oparte na funkcjach | Microsoft Docs
description: Porównaj API Management w zależności od funkcji, które oferują. Zobacz tabelę, która zawiera podsumowanie kluczowych funkcji dostępnych w poszczególnych warstwach cenowych.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 04/13/2021
ms.author: apimpm
ms.openlocfilehash: f111729d7d7707ed4f40ce8f89ce76975fb47400
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536452"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Porównanie warstw usługi Azure API Management na podstawie funkcji

Każda API Management [cenowa](https://aka.ms/apimpricing) oferuje odrębny zestaw funkcji i pojemność [jednostkową.](api-management-capacity.md) W poniższej tabeli przedstawiono podsumowanie kluczowych funkcji dostępnych w każdej z warstw. Niektóre funkcje mogą działać inaczej lub mieć różne możliwości w zależności od warstwy. W takich przypadkach różnice są wywoływane w artykułach dokumentacji opisujących poszczególne funkcje.

> [!IMPORTANT]
> Należy pamiętać, że warstwa Deweloper jest dla przypadków użycia i ocen nieprodukcyjnych. Nie oferuje ona umowy SLA.

| Cecha                                                                                      | Zużycie | Deweloper | Podstawowa | Standardowa (Standard) | Premium |
| -------------------------------------------------------------------------------------------- | ----------- | --------- | ----- | -------- | ------- |
| Integracja z usługą Azure AD<sup>1</sup>                                                             | Nie          | Yes       | Nie    | Yes      | Yes     |
| Virtual Network (VNet)                                                               | Nie          | Yes       | Nie    | Nie       | Yes     |
| Wdrażanie w wielu regionach                                                                      | Nie          | Nie        | Nie    | Nie       | Yes     |
| Strefy dostępności                                                                           | Nie          | Nie        | Nie    | Nie       | Yes     |
| Wiele niestandardowych nazw domen                                                                 | Nie          | Yes        | Nie    | Nie       | Yes     |
| Portal deweloperów<sup>2</sup>                                                                 | Nie          | Yes       | Yes   | Yes      | Yes     |
| Wbudowana pamięć podręczna                                                                               | Nie          | Yes       | Yes   | Yes      | Yes     |
| Wbudowana analiza                                                                           | Nie          | Yes       | Yes   | Yes      | Yes     |
| [Brama 3 hostowana](self-hosted-gateway-overview.md)<sup>samodzielnie</sup>                           | Nie          | Yes       | Nie    | Nie       | Yes     |
| [Ustawienia TLS](api-management-howto-manage-protocols-ciphers.md)                             | Yes         | Yes       | Yes   | Yes      | Yes     |
| [Zewnętrzna pamięć podręczna](./api-management-howto-cache-external.md)                                                    | Yes         | Yes       | Yes   | Yes      | Yes     |
| [Uwierzytelnianie certyfikatu klienta](api-management-howto-mutual-certificates-for-clients.md) | Yes         | Yes       | Yes   | Yes      | Yes     |
| [Tworzenie kopii zapasowej i przywracanie](api-management-howto-disaster-recovery-backup-restore.md)               | Nie          | Yes       | Yes   | Yes      | Yes     |
| [Zarządzanie za pośrednictwem usługi Git](api-management-configuration-repository-git.md)                        | Nie          | Yes       | Yes   | Yes      | Yes     |
| Interfejs API zarządzania bezpośredniego                                                                        | Nie          | Yes       | Yes   | Yes      | Yes     |
| Azure Monitor i metryki                                                               | Nie          | Yes       | Yes   | Yes      | Yes     |
| Statyczny adres IP                                                                                    | Nie          | Yes       | Yes   | Yes      | Yes     |

<sup>1</sup> Umożliwia korzystanie z usługi Azure AD (i Azure AD B2C) jako dostawcy tożsamości do logowania użytkownika w portalu dla deweloperów.<br/>
<sup>2 Obejmuje</sup> powiązane funkcje, takie jak użytkownicy, grupy, problemy, aplikacje oraz szablony wiadomości e-mail i powiadomienia.<br/>
<sup>3</sup> W warstwie Deweloper bramy hostowane samodzielnie są ograniczone do pojedynczego węzła bramy.<br/>