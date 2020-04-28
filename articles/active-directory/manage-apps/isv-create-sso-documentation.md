---
title: Utwórz & publikowanie dokumentacji logowania jednokrotnego dla aplikacji
description: Wskazówki dla niezależnych dostawców oprogramowania w ramach integracji z usługą Azure Active Directory
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb223ec8ab7b5c053136c78d3b4ca30ad4da4e18
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "74232281"
---
# <a name="create-and-publish-single-sign-on-documentation-for-your-application"></a>Tworzenie i publikowanie dokumentacji logowania jednokrotnego dla aplikacji   

## <a name="documentation-on-your-site"></a>Dokumentacja witryny

Łatwość wdrażania jest istotnym czynnikiem w podejmowaniu decyzji dotyczących oprogramowania w przedsiębiorstwie. Łatwa w obsłudze dokumentacja pozwala klientom na ich wdrażanie i zmniejsza koszty pomocy technicznej. Pracując z tysiącami dostawców oprogramowania, firma Microsoft widziała, co działa.

Zalecamy, aby dokumentacja w witrynie zawierała co najmniej następujące elementy.

* Wprowadzenie do funkcji logowania jednokrotnego

  * Obsługiwane protokoły

  * Wersja i jednostka SKU

  * Lista obsługiwanych dostawców tożsamości z linkami do dokumentacji

* Informacje o licencjonowaniu dla aplikacji

* Kontrola dostępu oparta na rolach na potrzeby konfigurowania logowania jednokrotnego

* Kroki konfiguracji logowania jednokrotnego

  * Elementy konfiguracji interfejsu użytkownika dla SAML z oczekiwanymi wartościami od dostawcy

  * Informacje o dostawcy usług, które mają zostać przesłane do dostawców tożsamości

* Jeśli OIDC/OAuth

  * Lista uprawnień wymaganych do wyrażania zgody z uzasadnieniem biznesowym

* Kroki testowania dla użytkowników pilotażowych

* Informacje dotyczące rozwiązywania problemów, w tym kody błędów i komunikaty

* Mechanizmy obsługi dla klientów

## <a name="documentation-on-the-microsoft-site"></a>Dokumentacja w witrynie firmy Microsoft

Po wyświetleniu listy aplikacji za pomocą galerii aplikacji Azure Active Directory, która również publikuje aplikację w portalu Azure Marketplace, firma Microsoft będzie generować dokumentację dla naszych klientów, którzy objaśniają proces krok po kroku. Przykład można zobaczyć [tutaj](https://aka.ms/appstutorial). Ta dokumentacja jest tworzona na podstawie przesłanej do galerii i można ją łatwo zaktualizować w przypadku wprowadzania zmian w aplikacji przy użyciu konta usługi GitHub.

## <a name="next-steps"></a>Następne kroki

[Wyświetlanie listy aplikacji w galerii aplikacji usługi Azure AD](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx)