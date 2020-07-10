---
title: Co nowego w zaporze aplikacji sieci Web platformy Azure
description: Dowiedz się, co nowego w zaporze aplikacji sieci Web platformy Azure, takich jak Najnowsze informacje o wersji, znane problemy, poprawki błędów, przestarzałe funkcje i nadchodzące zmiany.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: overview
ms.date: 10/24/2019
ms.author: victorh
ms.openlocfilehash: 5033d19de2321e0dfd3b6d89d2da3306b1723bd0
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86146584"
---
# <a name="whats-new-in-azure-web-application-firewall"></a>Co nowego w zaporze aplikacji sieci Web platformy Azure?

Zapora aplikacji sieci Web platformy Azure jest regularnie aktualizowana. Aby zachować aktualność najnowszych zmian, ten artykuł zawiera informacje na temat:

- Najnowsze wersje
- Znane problemy
- Poprawki błędów
- Funkcje uznane za przestarzałe

## <a name="new-features"></a>Nowe funkcje

|Cecha  |Opis  |Data dodania  |
|---------|---------|---------|
|Zasady dotyczące poszczególnych lokacji| Zasady dotyczące poszczególnych lokacji są teraz ogólnie dostępne. | Lipiec 2020 r. |
|Zestaw reguł łagodzenia bot (wersja zapoznawcza)|Zestaw reguł łagodzenia bot można włączyć wraz z zestawem reguł programu KSR. | Listopad 2019 r. |
|Integracja z GeoDB (wersja zapoznawcza)|Teraz można tworzyć reguły niestandardowe ograniczające ruch według kraju/regionu pochodzenia. | Listopad 2019 r. |
|Zasady WAF dla poszczególnych witryn/identyfikatorów URI (wersja zapoznawcza)|WAF-v2 obsługuje teraz stosowanie zasad do odbiorników, a także reguł opartych na ścieżkach. Zobacz [Tworzenie zasad WAFymi](create-waf-policy-ag.md). | Listopad 2019 r. |
|Reguły niestandardowe zapory aplikacji internetowej |Application Gateway WAF_v2 obsługuje teraz Tworzenie reguł niestandardowych. Zobacz [Application Gateway reguł niestandardowych](custom-waf-rules-overview.md). |Czerwiec 2019 r. |
|WAF konfiguracja i lista wykluczeń     |Dodaliśmy więcej opcji, które ułatwiają konfigurowanie WAF i zmniejszanie fałszywych wyników pozytywnych. Aby uzyskać więcej informacji [, zobacz limity rozmiaru żądań zapory aplikacji sieci Web i listy wykluczeń](application-gateway-waf-configuration.md) .|Grudzień 2018 r.|

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zapory aplikacji sieci Web na Application Gateway, zobacz [Zapora aplikacji sieci Web platformy Azure w usłudze azure Application Gateway](ag-overview.md).
