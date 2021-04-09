---
title: Tworzenie i Integracja interfejsu API usługi Azure ExpressRoute CrossConnnections
description: Ten artykuł zawiera szczegółowe omówienie partnerów ExpressRoute dotyczących typu zasobu expressRouteCrossConnections.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: duau
ms.openlocfilehash: b80392231dba26a10141dcd8247b092e8171894c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011986"
---
# <a name="expressroute-crossconnnections-api-development-and-integration"></a>Programowanie i Integracja interfejsu API ExpressRoute CrossConnnections

Interfejs API Menedżer zasobów partnera ExpressRoute umożliwia partnerom ExpressRoute zarządzanie konfiguracją warstwy 2 i warstwą klienta usługi ExpressRoute. Interfejs API Menedżer zasobów partnera ExpressRoute wprowadza nowy typ zasobu **expressRouteCrossConnections**. Partnerzy używają tego zasobu do zarządzania obwodymi usługi ExpressRoute klienta.

## <a name="workflow"></a>Przepływ pracy

Zasób expressRouteCrossConnections jest zasobem w tle do obwodu ExpressRoute. Gdy klient platformy Azure tworzy obwód usługi ExpressRoute i wybiera określonego partnera ExpressRoute, firma Microsoft tworzy zasób expressRouteCrossConnections w subskrypcji zarządzania usługi Azure ExpressRoute partnera. W takim przypadku firma Microsoft definiuje grupę zasobów, aby utworzyć w niej zasób expressRouteCrossConnections. Standard nazewnictwa dla grupy zasobów to **CrossConnection-* PeeringLocation * * *; gdzie PeeringLocation = ExpressRoute. Na przykład jeśli klient tworzy obwód usługi ExpressRoute w Denver, CrossConnection zostanie utworzona w subskrypcji platformy Azure partnera w następującej grupie zasobów: **CrossConnnection-Denver**.

ExpressRoute partnerzy zarządzają konfiguracją warstwy 2 i warstwy 3, wydając operacje REST względem zasobu expressRouteCrossConnections.

## <a name="benefits"></a>Korzyści

Zalety przejścia do zasobu expressRouteCrossConnections:

* Wszelkie przyszłe ulepszenia dla partnerów ExpressRoute zostaną udostępnione w ramach zasobu ExpressRouteCrossConnection.

* Partnerzy mogą zastosować [kontrolę dostępu opartą na rolach (Azure RBAC)](../role-based-access-control/overview.md) do zasobu expressRouteCrossConnection. Te kontrolki mogą definiować uprawnienia, dla których konta użytkowników mogą modyfikować zasób expressRouteCrossConnection oraz dodawać/aktualizować i usuwać konfiguracje komunikacji równorzędnej.

* Zasób expressRouteCrossConnection udostępnia interfejsy API, które mogą być pomocne podczas rozwiązywania problemów z połączeniami ExpressRoute. Obejmuje to tabelę ARP, podsumowanie tabeli tras BGP i szczegóły tabeli tras BGP. Ta funkcja nie jest obsługiwana przez klasyczne interfejsy API wdrażania.

* Partnerzy mogą również wyszukiwać ogłoszone społeczności w komunikacji równorzędnej firmy Microsoft przy użyciu zasobu *RouteFilter* .

## <a name="api-development-and-integration-steps"></a>Kroki projektowania i integracji interfejsu API

Aby można było rozwijać interfejs API partnera, partnerzy ExpressRoute korzystają z testowej konfiguracji klienta i partnera testowego. Testowa konfiguracja klienta zostanie użyta do utworzenia obwodów usługi ExpressRoute w testowaniu lokalizacji komunikacji równorzędnej, które mapują do fikcyjnych urządzeń i portów. Konfiguracja partnera testowego służy do zarządzania obwodymi usługi ExpressRoute utworzonymi w lokalizacji testowania komunikacji równorzędnej.

### <a name="1-enlist-subscriptions"></a>1. Rejestracja subskrypcji

Aby zażądać od partnera testowego i przetestowania konfiguracji klienta, należy zarejestrować dwie subskrypcje platformy Azure z opcją płatność zgodnie z rzeczywistym użyciem na kontakt inżynieryjny ExpressRoute:
* **ExpressRoute_API_Dev_Provider_Sub:** Ta subskrypcja będzie służyć do zarządzania obwodami usługi ExpressRoute utworzonymi w testowaniu lokalizacji komunikacji równorzędnej na urządzeniach fikcyjnych i portach.

* **ExpressRoute_API_Dev_Customer_Sub:** Ta subskrypcja będzie używana do tworzenia obwodów usługi ExpressRoute w testowaniu lokalizacji komunikacji równorzędnej, które są mapowane na fikcyjne urządzenia i porty.

Testowanie lokalizacji komunikacji równorzędnej: urządzenia fikcyjne i porty nie są domyślnie udostępniane klientom produkcyjnym. Aby można było utworzyć obwodów usługi ExpressRoute, które są mapowane na konfigurację testu, należy włączyć flagę funkcji subskrypcji.

### <a name="2-register-the-dev_provider-subscription-to-access-the-expressroutecrossconnections-api"></a>2. Zarejestruj subskrypcję Dev_Provider, aby uzyskać dostęp do interfejsu API expressRouteCrossConnections

Aby można było uzyskać dostęp do interfejsu API expressRouteCrossConnections, subskrypcja partnera musi zostać zarejestrowana w **dostawcy zasobów Microsoft. Network**. Wykonaj kroki opisane w artykule [dostawcy zasobów platformy Azure i typy](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) artykułów, aby ukończyć proces rejestracji.

### <a name="3-set-up-authentication-for-azure-resource-manager-rest-api-calls"></a>3. Skonfiguruj uwierzytelnianie dla wywołań interfejsu API REST Azure Resource Manager

Większość usług platformy Azure wymaga od kodu klienta uwierzytelnienia za pomocą Menedżer zasobów przy użyciu prawidłowych poświadczeń przed wywołaniem interfejsów API usługi. Uwierzytelnianie jest koordynowane między różnymi aktorami przez usługę Azure AD i zapewnia klientowi token dostępu jako potwierdzenie uwierzytelniania.

Proces uwierzytelniania obejmuje dwa główne kroki:

1. [Zarejestruj klienta](/rest/api/azure/#register-your-client-application-with-azure-ad).
2. [Utwórz żądanie dostępu](/rest/api/azure/#create-the-request).

### <a name="4-provide-network-contributor-permission-to-the-client-application"></a>4. Podaj uprawnienia współautora sieci dla aplikacji klienckiej

Po pomyślnym skonfigurowaniu uwierzytelniania należy udzielić aplikacji klienckiej dostępu współautora sieci w Dev_Provider_Sub. Aby udzielić uprawnień, zaloguj się do [Azure Portal](https://ms.portal.azure.com/#home) i wykonaj następujące czynności:

1. Przejdź do subskrypcji i wybierz Dev_Provider_Sub
2. Przejdź do Access Control (IAM)
3. Dodaj przypisanie roli
4. Wybierz rolę współautor sieci
5. Przypisywanie dostępu do użytkownika, grupy lub nazwy głównej usługi Azure AD
6. Wybierz aplikację kliencką
7. Zapisz zmiany

### <a name="5-develop"></a>5. programowanie

Programowanie przy użyciu [interfejsu API expressRouteCrossConnections](/rest/api/expressroute/expressroutecrossconnections).

## <a name="rest-api"></a>Interfejs API REST

Zobacz [interfejs API REST usługi ExpressRoute CrossConnections](/rest/api/expressroute/expressroutecrossconnections) dla dokumentacji interfejsu API REST.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat wszystkich ExpressRoute interfejsów API REST, zobacz [ExpressRoute API REST](/rest/api/expressroute/).