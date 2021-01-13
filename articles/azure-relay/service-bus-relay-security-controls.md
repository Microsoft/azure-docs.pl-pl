---
title: Kontrolki zabezpieczeń dla Azure Relay
description: W tym artykule przedstawiono listę kontrolną wbudowanych mechanizmów zabezpieczeń do oceniania Azure Relay.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 5d55026bfb6e3d6fe955a540b7596a85707398d6
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133348"
---
# <a name="security-controls-for-azure-relay"></a>Kontrolki zabezpieczeń dla Azure Relay

W tym artykule opisano kontrolki zabezpieczeń wbudowane w Azure Relay.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Sieć

| Kontrola zabezpieczeń | Tak/Nie | Uwagi | Dokumentacja |
|---|---|--|--|
| Obsługa prywatnego punktu końcowego| Nie |  |   |
| Izolacja sieci i obsługa zapór| Nie |  |   |
| Obsługa tunelowania wymuszonego| Brak | Przekaźnik jest tunelem TLS  |   |

## <a name="monitoring--logging"></a>Monitorowanie rejestrowania &

| Kontrola zabezpieczeń | Tak/Nie | Uwagi| Dokumentacja |
|---|---|--|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Tak | |   |
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Tak | Za [Azure Resource Manager](../azure-resource-manager/index.yml). |   |
| Rejestrowanie i inspekcja płaszczyzny danych| Tak | Powodzenie/Niepowodzenie połączenia i błędy oraz zarejestrowane.  |   |

## <a name="identity"></a>Tożsamość

| Kontrola zabezpieczeń | Tak/Nie | Uwagi| Dokumentacja |
|---|---|--|--|
| Authentication| Tak | Za pośrednictwem SAS. | [Azure Relay uwierzytelnianie i autoryzacja](relay-authentication-and-authorization.md) |
| Autoryzacja|  Tak | Za pośrednictwem SAS. | [Azure Relay uwierzytelnianie i autoryzacja](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>Ochrona danych

| Kontrola zabezpieczeń | Tak/Nie | Uwagi | Dokumentacja |
|---|---|--|--|
| Szyfrowanie po stronie serwera w czasie spoczynku: klucze zarządzane przez firmę Microsoft |  Brak | Przekaźnik jest gniazdem internetowym i nie utrzymuje danych. |   |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Nie | Używa tylko certyfikatów Microsoft TLS.  |   |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| Brak | |   |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie VNet-VNet)| Tak | Usługa wymaga protokołu TLS. |   |
| Wywołania interfejsu API są szyfrowane| Tak | Schemat. |


## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola zabezpieczeń | Tak/Nie | Uwagi| Dokumentacja |
|---|---|--|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Tak | Za [Azure Resource Manager](../azure-resource-manager/index.yml).|   |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych kontrolach zabezpieczeń w ramach usług platformy Azure](../security/fundamentals/security-controls.md).