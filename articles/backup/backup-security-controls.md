---
title: Mechanizmy zabezpieczeń
description: Dowiedz się więcej o kontrolkach zabezpieczeń używanych w usłudze Azure Backup. Te kontrolki ułatwiają zapobieganie wykrywaniu luk w zabezpieczeniach, wykrywanie ich i reagowanie na nie.
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 40bd35bdf83d336aebd37cdda0a0b59164ac9f7a
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92055889"
---
# <a name="security-controls-for-azure-backup"></a>Kontrolki zabezpieczeń dla Azure Backup

W tym artykule opisano kontrolki zabezpieczeń wbudowane w Azure Backup.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Sieć

| Kontrola zabezpieczeń | Tak/Nie | Uwagi | Dokumentacja
|---|---|--|--|
| Obsługa punktów końcowych usługi| Nie |  |  |
| Obsługa iniekcji sieci wirtualnej| Nie |  |  |
| Izolacja sieci i obsługa zapór| Tak | |  |
| Obsługa tunelowania wymuszonego dla maszyn wirtualnych platformy Azure | Tak  |  |  |
| Wymuszone tunelowanie obsługujące aplikacje działające na maszynach wirtualnych platformy Azure| Nie  |  |  |

## <a name="monitoring--logging"></a>Monitorowanie rejestrowania &

| Kontrola zabezpieczeń | Tak/Nie | Uwagi| Dokumentacja
|---|---|--|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (np. log Analytics, App Insights)| Tak | Log Analytics jest obsługiwana za pośrednictwem dzienników zasobów. Aby uzyskać więcej informacji, zobacz [monitorowanie Azure Backup chronionych obciążeń przy użyciu log Analytics](backup-azure-diagnostics-mode-data-model.md). |  |
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Tak | Wszystkie akcje wyzwalane przez klienta z Azure Portal są rejestrowane w dziennikach aktywności. |  |
| Rejestrowanie i inspekcja płaszczyzny danych| Nie | Nie można bezpośrednio połączyć Azure Backup płaszczyzny danych.  |  |

## <a name="identity"></a>Tożsamość

| Kontrola zabezpieczeń | Tak/Nie | Uwagi| Dokumentacja
|---|---|--|--|
| Uwierzytelnianie| Tak | Uwierzytelnianie odbywa się za Azure Active Directory. |  |
| Autoryzacja| Tak | Są używane role wbudowane klienta i platformy Azure. Aby uzyskać więcej informacji, zobacz [używanie Role-Based Access Control do zarządzania punktami odzyskiwania Azure Backup](./backup-rbac-rs-vault.md). |  |

## <a name="data-protection"></a>Ochrona danych

| Kontrola zabezpieczeń | Tak/Nie | Uwagi | Dokumentacja
|---|---|--|--|
| Szyfrowanie po stronie serwera w czasie spoczynku: klucze zarządzane przez firmę Microsoft | Tak | Korzystanie z szyfrowania usługi Storage dla kont magazynu. |  |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Nie |  |  |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| Nie |  |  |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie VNet-VNet)| Nie | Przy użyciu protokołu HTTPS. |  |
| Wywołania interfejsu API są szyfrowane| Tak |  |  |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola zabezpieczeń | Tak/Nie | Uwagi| Dokumentacja
|---|---|--|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itd.)| Tak|  |  |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych kontrolach zabezpieczeń w ramach usług platformy Azure](../security/fundamentals/security-controls.md).
