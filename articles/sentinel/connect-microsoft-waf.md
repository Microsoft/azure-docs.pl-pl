---
title: Łączenie danych zapory aplikacji sieci Web z platformą Azure — wskaźnikiem
description: Dowiedz się, jak połączyć dane zapory aplikacji sieci Web firmy Microsoft z platformą Azure.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: a5cef16694fa2cfae036152d22cfa4473956fc72
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77588182"
---
# <a name="connect-data-from-microsoft-web-application-firewall"></a>Łączenie danych z zapory aplikacji sieci Web firmy Microsoft



Dzienniki można przesyłać strumieniowo z zapory aplikacji sieci Web firmy Microsoft (WAF) platformy Application Gateway Azure. Ta WAF chroni aplikacje przed typowymi lukami w zabezpieczeniach sieci Web, takimi jak iniekcja kodu SQL i skrypty między lokacjami, oraz pozwala na dostosowanie reguł w celu ograniczenia fałszywych wartości dodatnich. Postępuj zgodnie z tymi instrukcjami w celu przesyłania strumieniowego dzienników zapory aplikacji sieci Web firmy Microsoft do usługi Azure wskaźnikowej.


## <a name="prerequisites"></a>Wymagania wstępne

- Istniejący zasób bramy aplikacji

## <a name="connect-to-microsoft-web-application-firewall"></a>Nawiązywanie połączenia z zaporą aplikacji sieci Web firmy Microsoft

Jeśli masz już zaporę aplikacji sieci Web firmy Microsoft, upewnij się, że masz istniejący zasób bramy.
Po wdrożeniu i pobieraniu danych przez zaporę aplikacji sieci Web firmy Microsoft dane alertów mogą być łatwo przesyłane strumieniowo do usługi Azure wskaźnikowej.
    
1. W portalu wskaźnikowym platformy Azure wybierz pozycję **Łączniki danych**.
1. Na stronie łączniki danych wybierz kafelek **WAF** .
1. Przejdź do [zasobu Application Gateway](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAllResourcesBlade/resourceType/Microsoft.Network%2FapplicationGateways)   i wybierz pozycję WAF.
    1. Wybierz pozycję **Ustawienia diagnostyczne**.
    1. Wybierz pozycję **+ Dodaj ustawienie diagnostyczne** w tabeli.
    1. Na stronie **Ustawienia diagnostyczne** wpisz **nazwę** i wybierz pozycję **Wyślij do log Analytics**.
    1. W obszarze **obszar roboczy log Analytics** wybierz obszar roboczy wskaźnik platformy Azure.
    1. Wybierz typy dzienników, które chcesz analizować. Zalecamy: ApplicationGatewayAccessLog i ApplicationGatewayFirewallLog.
1. Aby użyć odpowiedniego schematu w Log Analytics dla alertów zapory aplikacji sieci Web firmy Microsoft, Wyszukaj pozycję **AzureDiagnostics**.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie pokazano, jak połączyć zaporę aplikacji sieci Web firmy Microsoft z platformą Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
