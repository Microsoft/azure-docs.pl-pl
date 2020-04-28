---
title: Mechanizmy zabezpieczeń
description: Dowiedz się więcej o kontrolkach zabezpieczeń używanych w usłudze Azure Backup. Te kontrolki ułatwiają zapobieganie wykrywaniu luk w zabezpieczeniach, wykrywanie ich i reagowanie na nie.
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 0e3f5ce942ea8aef9bf5eb98883ae1e72a7ab239
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74172136"
---
# <a name="security-controls-for-azure-backup"></a>Kontrolki zabezpieczeń dla Azure Backup

W tym artykule opisano kontrolki zabezpieczeń wbudowane w Azure Backup.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Sieć

| Kontrola zabezpieczeń | Tak/Nie | Uwagi | Dokumentacja
|---|---|--|
| Obsługa punktów końcowych usługi| Nie |  |  |
| Obsługa iniekcji sieci wirtualnej| Nie |  |  |
| Izolacja sieci i obsługa zapór| Tak | Wymuszone tunelowanie jest obsługiwane w przypadku kopii zapasowej maszyny wirtualnej. Wymuszone tunelowanie nie jest obsługiwane w przypadku obciążeń uruchamianych wewnątrz maszyn wirtualnych. |  |
| Obsługa tunelowania wymuszonego| Nie |  |  |

## <a name="monitoring--logging"></a>Monitorowanie rejestrowania &

| Kontrola zabezpieczeń | Tak/Nie | Uwagi| | Dokumentacja
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Tak | Log Analytics jest obsługiwana za pośrednictwem dzienników zasobów. Aby uzyskać więcej informacji, zobacz [monitorowanie Azure Backup chronionych obciążeń przy użyciu log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/). |  |
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Tak | Wszystkie akcje wyzwalane przez klienta z Azure Portal są rejestrowane w dziennikach aktywności. |  |
| Rejestrowanie i inspekcja płaszczyzny danych| Nie | Nie można bezpośrednio połączyć Azure Backup płaszczyzny danych.  |  |

## <a name="identity"></a>Tożsamość

| Kontrola zabezpieczeń | Tak/Nie | Uwagi| | Dokumentacja
|---|---|--|
| Uwierzytelnianie| Tak | Uwierzytelnianie odbywa się za Azure Active Directory. |  |
| Autoryzacja| Tak | Są używane wbudowane role RBAC klienta. Aby uzyskać więcej informacji, zobacz [używanie Access Control opartej na rolach w celu zarządzania Azure Backup punktów odzyskiwania](/azure/backup/backup-rbac-rs-vault). |  |

## <a name="data-protection"></a>Ochrona danych

| Kontrola zabezpieczeń | Tak/Nie | Uwagi | | Dokumentacja
|---|---|--|
| Szyfrowanie po stronie serwera w czasie spoczynku: klucze zarządzane przez firmę Microsoft | Tak | Korzystanie z szyfrowania usługi Storage dla kont magazynu. |  |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Nie |  |  |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| Nie |  |  |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej)| Nie | Przy użyciu protokołu HTTPS. |  |
| Wywołania interfejsu API są szyfrowane| Tak |  |  |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola zabezpieczeń | Tak/Nie | Uwagi| | Dokumentacja
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Tak|  |  |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych kontrolach zabezpieczeń w ramach usług platformy Azure](../security/fundamentals/security-controls.md).
