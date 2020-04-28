---
title: Mechanizmy zabezpieczeń
description: Znajdź listę kontrolną kontroli zabezpieczeń na potrzeby oceny Azure App Service w organizacji.
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 2586821c4c48f809efb5408c3cdae5e42e3b3fcf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74671449"
---
# <a name="security-controls-for-azure-app-service"></a>Kontrolki zabezpieczeń dla Azure App Service

W tym artykule opisano kontrolki zabezpieczeń wbudowane w Azure App Service.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>Sieć

| Kontrola zabezpieczeń | Tak/Nie | Uwagi | Dokumentacja
|---|---|--|
| Obsługa punktów końcowych usługi| Tak | Dostępne dla App Service.| [Ograniczenia dostępu Azure App Service](app-service-ip-restrictions.md)
| Obsługa iniekcji sieci wirtualnej| Tak | Środowiska App Service są prywatnymi implementacjami App Service przeznaczonymi dla pojedynczego klienta, które zostały dodane do sieci wirtualnej klienta. | [Wprowadzenie do środowisk App Service Environment](environment/intro.md)
| Izolacja sieci i obsługa zapór| Tak | W przypadku publicznej App Service odmiany wielodostępnej wielu dzierżawców klienci mogą konfigurować listy ACL sieci (ograniczenia adresów IP), aby zablokować dozwolony ruch przychodzący.  Środowiska App Service są wdrażane bezpośrednio w sieciach wirtualnych, dlatego można je zabezpieczyć za pomocą sieciowych grup zabezpieczeń. | [Ograniczenia dostępu Azure App Service](app-service-ip-restrictions.md)
| Obsługa tunelowania wymuszonego| Tak | Środowiska App Service można wdrożyć w sieci wirtualnej klienta, w której jest skonfigurowany tunelowanie wymuszone. | [Konfigurowanie wymuszonego tunelowania środowiska App Service Environment](environment/forced-tunnel-support.md)

## <a name="monitoring--logging"></a>Monitorowanie rejestrowania &

| Kontrola zabezpieczeń | Tak/Nie | Uwagi | Dokumentacja
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Tak | App Service integruje się z Application Insights dla języków, które obsługują Application Insights (pełny .NET Framework, .NET Core, Java i Node. JS).  Zobacz [monitorowanie wydajności Azure App Service](../azure-monitor/app/azure-web-apps.md). App Service również wysyła metryki aplikacji do Azure Monitor. | [Monitorowanie aplikacji w Azure App Service](web-sites-monitor.md)
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Tak | Wszystkie operacje zarządzania wykonywane na App Service obiektów odbywają się za pośrednictwem [Azure Resource Manager](../azure-resource-manager/index.yml). Dzienniki historyczne tych operacji są dostępne zarówno w portalu, jak i za pośrednictwem interfejsu wiersza polecenia. | [Azure Resource Manager operacji dostawcy zasobów](../role-based-access-control/resource-provider-operations.md#microsoftweb), [AZ monitor Activity-Log](/cli/azure/monitor/activity-log)
| Rejestrowanie i inspekcja płaszczyzny danych | Nie | Płaszczyzna danych dla App Service jest zdalnym udziałem plików zawierającym zawartość witryny sieci Web wdrożonej przez klienta.  Nie istnieje Inspekcja zdalnego udziału plików. |

## <a name="identity"></a>Tożsamość

| Kontrola zabezpieczeń | Tak/Nie | Uwagi |  Dokumentacja
|---|---|--|
| Uwierzytelnianie| Tak | Klienci mogą tworzyć aplikacje na App Service, które automatycznie integrują się z usługą [Azure Active Directory (Azure AD)](../active-directory/index.yml) , a także z innymi dostawcami tożsamości zgodnymi z uwierzytelnianiem OAuth na potrzeby zarządzania dostępem do App Service zasobów, cały dostęp jest kontrolowany przez kombinację uwierzytelnionego podmiotu zabezpieczeń usługi Azure AD i Azure Resource Manager ról RBAC. | [Uwierzytelnianie i autoryzacja w usłudze Azure App Service](overview-authentication-authorization.md)
| Autoryzacja| Tak | W celu zarządzania dostępem do zasobów App Service, cały dostęp jest kontrolowany przez kombinację uwierzytelnionego podmiotu zabezpieczeń usługi Azure AD i Azure Resource Manager ról RBAC.  | [Uwierzytelnianie i autoryzacja w usłudze Azure App Service](overview-authentication-authorization.md)

## <a name="data-protection"></a>Ochrona danych

| Kontrola zabezpieczeń | Tak/Nie | Uwagi | Dokumentacja
|---|---|--|
| Szyfrowanie po stronie serwera w czasie spoczynku: klucze zarządzane przez firmę Microsoft | Tak | Zawartość pliku witryny sieci Web jest przechowywana w usłudze Azure Storage, która automatycznie szyfruje zawartość w stanie spoczynku. <br><br>Klucze tajne dostarczone przez klienta są szyfrowane w stanie spoczynku. Wpisy tajne są szyfrowane w stanie spoczynku, podczas gdy są przechowywane w App Service bazach danych konfiguracji.<br><br>Opcjonalnie dyski dołączone lokalnie mogą być używane jako magazyn tymczasowy przez witryny sieci Web (D:\Local i% TMP%). Dyski dołączone lokalnie nie są szyfrowane w stanie spoczynku. | [Szyfrowanie usługi Azure Storage dla danych magazynowanych](../storage/common/storage-service-encryption.md)
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Tak | Klienci mogą wybrać przechowywanie wpisów tajnych aplikacji w Key Vault i pobrać je w czasie wykonywania. | [Użyj Key Vault odwołań dla App Service i Azure Functions (wersja zapoznawcza)](app-service-key-vault-references.md)
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| Nie dotyczy | |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej)| Tak | Klienci mogą konfigurować witryny sieci Web tak, aby wymagały protokołu HTTPS i używać go do obsługi ruchu przychodzącego.  | [Jak utworzyć tylko Azure App Service https](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/) (wpis w blogu)
| Wywołania interfejsu API są szyfrowane| Tak | Wywołania zarządzania w celu skonfigurowania App Service występują za pośrednictwem [Azure Resource Manager](../azure-resource-manager/index.yml) wywołań za pośrednictwem protokołu HTTPS. |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola zabezpieczeń | Tak/Nie | Uwagi | Dokumentacja
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Tak | W przypadku operacji zarządzania stanem konfiguracji App Service można wyeksportować jako szablon Azure Resource Manager i wersji w czasie. W przypadku operacji środowiska uruchomieniowego klienci mogą obsługiwać wiele różnych wersji na żywo aplikacji przy użyciu funkcji App Service miejsc wdrożenia. | 

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych kontrolach zabezpieczeń w ramach usług platformy Azure](../security/fundamentals/security-controls.md).
