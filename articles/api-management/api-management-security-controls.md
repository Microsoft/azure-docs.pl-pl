---
title: Kontrolki zabezpieczeń dla usługi Azure API Management
description: Zapoznaj się z listą kontrolną kontroli zabezpieczeń dotyczącą oceniania API Management. Obejmują one kontrolę sieci, tożsamości i ochrony danych.
services: api-management
author: vladvino
ms.service: api-management
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: vlvinogr
ms.openlocfilehash: a147179f7b55e43379b3c3fa3a7a0767cc97b198
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87902579"
---
# <a name="security-controls-for-api-management"></a>Kontrolki zabezpieczeń dla API Management

W tym artykule opisano kontrolki zabezpieczeń wbudowane w API Management.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Sieć

| Kontrola zabezpieczeń | Tak/Nie | Uwagi | Dokumentacja |
|---|---|--|--|
| Obsługa punktów końcowych usługi| Nie | |  |
| Obsługa iniekcji sieci wirtualnej| Tak | |  |
| Izolacja sieci i obsługa zapór| Tak | Odpowiednio przy użyciu grup zabezpieczeń sieci (sieciowej grupy zabezpieczeń) i platformy Application Gateway Azure (lub innego oprogramowania). |  |
| Obsługa tunelowania wymuszonego| Tak | Sieć Azure udostępnia tunelowanie wymuszone. |  |

## <a name="monitoring--logging"></a>Monitorowanie rejestrowania &

| Kontrola zabezpieczeń | Tak/Nie | Uwagi| Dokumentacja |
|---|---|--|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Tak | | |
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Tak | [Azure Monitor dzienników aktywności](../azure-monitor/platform/platform-logs-overview.md) | |
| Rejestrowanie i inspekcja płaszczyzny danych| Tak | [Azure monitor dzienniki diagnostyczne](../azure-monitor/platform/platform-logs-overview.md) i (opcjonalnie) [Application Insights platformy Azure](../azure-monitor/app/app-insights-overview.md).  | |


## <a name="identity"></a>Tożsamość

| Kontrola zabezpieczeń | Tak/Nie | Uwagi| Dokumentacja |
|---|---|--|--|
| Uwierzytelnianie| Tak | |  |
| Autoryzacja| Tak | |  |

## <a name="data-protection"></a>Ochrona danych

| Kontrola zabezpieczeń | Tak/Nie | Uwagi | Dokumentacja |
|---|---|--|--|
| Szyfrowanie po stronie serwera w czasie spoczynku: klucze zarządzane przez firmę Microsoft | Tak | Dane poufne, takie jak certyfikaty, klucze i wartości nazwane tajne, są szyfrowane przy użyciu zarządzanych przez usługę usług według kluczy wystąpienia usługi. |  |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Nie | Wszystkie klucze szyfrowania są dla każdego wystąpienia usługi i są zarządzane przez usługę. |  |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| Nie dotyczy | |  |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie VNet-VNet)| Tak | Usługa [Express Route](../expressroute/index.yml) i szyfrowanie sieci wirtualnej są udostępniane przez [Sieć Azure](../virtual-network/index.yml). |  |
| Wywołania interfejsu API są szyfrowane| Tak | Wywołania płaszczyzny zarządzania są nawiązywane za pośrednictwem [Azure Resource Manager](../azure-resource-manager/index.yml) za pośrednictwem protokołu TLS. Wymagany jest prawidłowy token sieci Web JSON (JWT).  Wywołania płaszczyzny danych mogą być zabezpieczone przy użyciu protokołu TLS i jednego z obsługiwanych mechanizmów uwierzytelniania (na przykład certyfikatu klienta lub tokenu JWT). |   |
 |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola zabezpieczeń | Tak/Nie | Uwagi| Dokumentacja |
|---|---|--|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Tak | Korzystanie z [zestawu Resource Kit usługi Azure API Management DevOps](https://aka.ms/apimdevops) |  |

## <a name="vulnerability-scans-false-positives"></a>Luka w zabezpieczeniach skanowania fałszywych pozytywów

W tej części udokumentowano typowe luki w zabezpieczeniach, które nie wpływają na API Management platformy Azure.

| Problemy               | Opis                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed jest luką w zabezpieczeniach w implementacji rozszerzenia SessionTicket protokołu TLS znalezionego w niektórych F5. Pozwala na wycieki ("wykrwawinie") do 31 bajtów danych z niezainicjowanej pamięci. Jest to spowodowane tym, że stos TLS uzupełnia identyfikator sesji, który został przesłany przez klienta z danymi, aby zapewnić 32 bitów. |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych kontrolach zabezpieczeń w ramach usług platformy Azure](../security/fundamentals/security-controls.md).