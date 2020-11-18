---
title: 'Serwer proxy aplikacji usługi Azure AD platformy Azure: historia wersji'
description: W tym artykule wymieniono wszystkie wersje platformy Azure serwer proxy aplikacji usługi Azure AD i opisano nowe funkcje oraz naprawione problemy
services: active-directory
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 07/22/2020
ms.subservice: app-mgmt
ms.author: kenwith
ms.openlocfilehash: 8000d07824c27f60dd5d8d6a457521691fe2b7d7
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658404"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Serwer proxy aplikacji usługi Azure AD platformy Azure: historia wersji
W tym artykule wymieniono wersje i funkcje serwera proxy aplikacji Azure Active Directory (Azure AD), który został wystawiony. Zespół usługi Azure AD regularnie aktualizuje serwer proxy aplikacji przy użyciu nowych funkcji i funkcjonalności. Łączniki serwera proxy aplikacji są aktualizowane automatycznie po wydaniu nowej wersji. 

Zalecamy, aby upewnić się, że funkcja autoaktualizacje jest włączona dla łączników, aby upewnić się, że masz najnowsze funkcje i poprawki błędów. Firma Microsoft zapewnia bezpośrednią pomoc techniczną dotyczącą najnowszej wersji łącznika i jednej wersji przed.

Poniżej znajduje się lista powiązanych zasobów:

Zasób |  Szczegóły
--------- | --------- |
Jak włączyć serwer proxy aplikacji | Wymagania wstępne dotyczące włączania serwera proxy aplikacji oraz instalowania i rejestrowania łącznika są opisane w tym [samouczku](application-proxy-add-on-premises-application.md).
Omówienie łączników serwer proxy aplikacji usługi Azure AD platformy Azure | Dowiedz się więcej o [zarządzaniu łącznikami](application-proxy-connectors.md) i sposobach [uaktualniania](application-proxy-connectors.md#automatic-updates)łączników.
Pobieranie łącznika usługi Azure serwer proxy aplikacji usługi Azure AD |  [Pobierz najnowszy łącznik](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download).

## <a name="1519750"></a>1.5.1975.0

### <a name="release-status"></a>Stan wydania

22 lipca 2020: wydane do pobrania ta wersja jest dostępna tylko do instalacji za pośrednictwem strony pobierania. Wydanie z autouaktualnianiem tej wersji zostanie zwolnione w późniejszym czasie.

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
-   Ulepszona obsługa Azure Government środowiska chmury. Aby uzyskać instrukcje dotyczące prawidłowej instalacji łącznika dla programu Azure Government Cloud, zapoznaj się z [wymaganiami wstępnymi](../hybrid/reference-connect-government-cloud.md#allow-access-to-urls) i [procedurą instalacji](../hybrid/reference-connect-government-cloud.md#install-the-agent-for-the-azure-government-cloud).
- Obsługa korzystania z klienta sieci Web Usługi pulpitu zdalnego z serwerem proxy aplikacji. Aby uzyskać więcej informacji, zobacz temat [publikowanie pulpit zdalny przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD](application-proxy-integrate-with-remote-desktop-services.md) .
- Ulepszone negocjowanie rozszerzeń protokołu WebSocket. 

### <a name="fixed-issues"></a>Naprawione problemy
- Rozwiązano problem z użyciem protokołu WebSocket z wymuszonymi małymi literami.
- Rozwiązano problem, który spowodował, że łączniki czasami nie odpowiadają.

## <a name="1516260"></a>1.5.1626.0

### <a name="release-status"></a>Stan wydania

17 lipca 2020: wydano do pobrania. Ta wersja jest dostępna tylko do instalacji za pośrednictwem strony pobierania. Wydanie z autouaktualnianiem tej wersji zostanie zwolnione w późniejszym czasie.

### <a name="fixed-issues"></a>Naprawione problemy
- Rozwiązano problem przecieku pamięci występujący w poprzedniej wersji
- Ogólne ulepszenia obsługi protokołu WebSocket

## <a name="1515260"></a>1.5.1526.0

### <a name="release-status"></a>Stan wydania

07 kwietnia 2020: wydano do pobrania

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
-   Łączniki używają tylko protokołu TLS 1,2 dla wszystkich połączeń. Aby uzyskać więcej informacji, zobacz [wymagania wstępne łącznika](application-proxy-add-on-premises-application.md#prerequisites) .
- Udoskonalone sygnalizowanie między łącznikiem i usługami platformy Azure. Obejmuje to obsługę niezawodnych sesji dla komunikacji WCF między łącznikiem a usługami platformy Azure i ulepszeniami buforowania DNS na potrzeby komunikacji z użyciem protokołu WebSocket.
- Obsługa konfigurowania serwera proxy między łącznikiem a aplikacją zaplecza. Aby uzyskać więcej informacji, zobacz temat [współpraca z istniejącymi lokalnymi serwerami proxy](application-proxy-configure-connectors-with-proxy-servers.md).

### <a name="fixed-issues"></a>Naprawione problemy
- Usunięte z powrotem do portu 8080 na potrzeby komunikacji z łącznika z usługami platformy Azure.
- Dodano ślady debugowania dla komunikacji z użyciem protokołu WebSocket. 
- Rozwiązano zachowanie atrybutu SameSite w przypadku ustawienia plików cookie aplikacji zaplecza.

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>Stan wydania

20 września 2018: wydano do pobrania

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

- Dodano obsługę protokołu WebSocket dla aplikacji QlikSense. Aby dowiedzieć się więcej o tym, jak zintegrować QlikSense z serwerem proxy aplikacji, zobacz ten [Przewodnik](application-proxy-qlik.md). 
- Ulepszono Kreatora instalacji, aby ułatwić konfigurowanie wychodzącego serwera proxy. 
- Dla łączników należy ustawić protokół TLS 1,2. 
- Dodano nową umowę licencyjną End-User (EULA).  

### <a name="fixed-issues"></a>Naprawione problemy

- Naprawiono usterkę, która spowodowała wystąpienie przecieków pamięci w łączniku.
- Zaktualizowano wersję Azure Service Bus, która obejmuje rozwiązywanie problemów z przekroczeniem limitu czasu łącznika.

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>Stan wydania

19 stycznia 2018: wydano do pobrania

### <a name="fixed-issues"></a>Naprawione problemy

- Dodano obsługę domen niestandardowych, które wymagają tłumaczenia domeny w pliku cookie.

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>Stan wydania 

25 maja 2017: wydano do pobrania 

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia 

Ulepszona kontrola nad limitami połączeń wychodzących łączników. 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>Stan wydania

15 kwietnia 2017: wydano do pobrania

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

- Uproszczone dołączanie i zarządzanie przy użyciu mniejszej liczby wymaganych portów. Serwer proxy aplikacji wymaga teraz otwarcia tylko dwóch standardowych portów wychodzących: 443 i 80. Serwer proxy aplikacji nadal używa tylko połączeń wychodzących, więc nadal nie są potrzebne żadne składniki w strefie DMZ. Aby uzyskać szczegółowe informacje, zobacz [dokumentację dotyczącą konfiguracji](application-proxy-add-on-premises-application.md).  
- Jeśli jest to obsługiwane przez zewnętrzny serwer proxy lub zaporę, możesz teraz otworzyć sieć przy użyciu systemu DNS, a nie z zakresu adresów IP. Usługi serwera proxy aplikacji wymagają połączeń tylko z *. msappproxy.net i *. servicebus.windows.net.


## <a name="earlier-versions"></a>Wcześniejsze wersje

Jeśli używasz wersji łącznika serwera proxy aplikacji starszej niż 1.5.36.0, zaktualizuj ją do najnowszej wersji, aby upewnić się, że masz najnowsze w pełni obsługiwane funkcje.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej na temat [dostępu zdalnego do aplikacji lokalnych za pomocą usługi Azure serwer proxy aplikacji usługi Azure AD](application-proxy.md).
- Aby rozpocząć korzystanie z serwera proxy aplikacji, zobacz [Samouczek: Dodawanie aplikacji lokalnej dla dostępu zdalnego za pośrednictwem serwera proxy aplikacji](application-proxy-add-on-premises-application.md).