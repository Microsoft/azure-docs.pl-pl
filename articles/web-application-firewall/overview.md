---
title: Wprowadzenie do zapory aplikacji sieci Web platformy Azure
description: Ten artykuł zawiera omówienie zapory aplikacji sieci Web platformy Azure (WAF)
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 03/18/2020
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 49024c86d09f5cdd9e8b04d5a49f60021660b0c4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "79475791"
---
# <a name="what-is-azure-web-application-firewall"></a>Co to jest usługa Azure Web Application Firewall?

Zapora aplikacji internetowej zapewnia centralną ochronę aplikacji internetowych przez typowymi atakami wykorzystującymi luki i lukami w zabezpieczeniach. Aplikacje sieci Web są coraz bardziej przeznaczone dla złośliwych ataków wykorzystujących często znane luki w zabezpieczeniach. Iniekcja kodu SQL i skryptów między lokacjami są między Najczęściej spotykanymi atakami.

![WAF — Omówienie](media/overview/wafoverview.png)

Zapobieganie takim atakom w kodzie aplikacji jest trudne. Może to wymagać rygorystycznej konserwacji, poprawek i monitorowania na wielu warstwach topologii aplikacji. Scentralizowana Zapora aplikacji sieci Web pomaga uprościć zarządzanie zabezpieczeniami. WAF zapewnia także administratorom aplikacji lepszą ochronę przed zagrożeniami i włamaniami.

Rozwiązanie WAF może szybko reagować na zagrożenia bezpieczeństwa dzięki scentralizowanej poprawkom znanej luki w zabezpieczeniach, zamiast zabezpieczaniu każdej indywidualnej aplikacji sieci Web.

## <a name="supported-service"></a>Obsługiwana usługa

WAF można wdrażać za pomocą platformy Azure Application Gateway, platformy Azure z przodu i usługi Azure Content Delivery Network (CDN) firmy Microsoft. WAF na Azure CDN jest obecnie w publicznej wersji zapoznawczej.  WAF zawiera funkcje, które są dostosowane do poszczególnych usług. Aby uzyskać więcej informacji na temat funkcji WAF dla każdej usługi, zobacz Omówienie każdej usługi.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat zapory aplikacji sieci Web na Application Gateway, zobacz [Zapora aplikacji sieci Web na platformie Azure Application Gateway](./ag/ag-overview.md).
- Aby uzyskać więcej informacji na temat zapory aplikacji sieci Web w usłudze Azure Front-drzwiczk, zobacz [Zapora aplikacji sieci Web w usłudze Azure front-drzwi](./afds/afds-overview.md).
- Aby uzyskać więcej informacji na temat zapory aplikacji sieci Web w usłudze Azure CDN, zobacz [Zapora aplikacji sieci Web w usłudze Azure CDN](./cdn/cdn-overview.md)
