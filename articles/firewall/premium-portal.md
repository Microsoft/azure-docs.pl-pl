---
title: Zapora Azure Premium w wersji zapoznawczej w Azure Portal
description: Dowiedz się więcej na temat usługi Azure firewall Premium w wersji zapoznawczej w Azure Portal.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: 3d56fc73faeb0d48ba7e5b21449c61d6213afa40
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100549837"
---
# <a name="azure-firewall-premium-preview-in-the-azure-portal"></a>Zapora Azure Premium w wersji zapoznawczej w Azure Portal

> [!IMPORTANT]
> Usługa Azure firewall Premium jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 Zapora Azure Premium w wersji zapoznawczej to Zapora nowej generacji z możliwościami, które są wymagane w środowiskach wysoce wrażliwych i regulowanych. Obejmuje następujące funkcje:

- **Inspekcja TLS** — odszyfrowuje ruch wychodzący, przetwarza dane, a następnie szyfruje dane i wysyła je do miejsca docelowego.
- **Dostawców tożsamości** — system wykrywania i zapobiegania włamaniom w sieci (dostawców tożsamości) pozwala monitorować działania sieciowe w poszukiwaniu złośliwego działania, rejestrować informacje o tym działaniu, zgłaszać je i opcjonalnie próbować go zablokować.
- **Filtrowanie adresów URL** — rozszerza możliwości filtrowania nazw FQDN zapory platformy Azure w celu uwzględnienia całego adresu URL. Na przykład `www.contoso.com/a/c` zamiast `www.contoso.com` .
- **Kategorie sieci Web** — Administratorzy mogą zezwalać użytkownikom na dostęp do takich kategorii witryn sieci Web, jak witryny hazard, witryny sieci Web mediów społecznościowych i inne osoby, jak na nie.

Aby uzyskać więcej informacji, zobacz [funkcje zapory platformy Azure w warstwie Premium](premium-features.md).

## <a name="deploy-the-firewall"></a>Wdrażanie zapory

Wdrażanie usługi Azure firewall Premium w wersji zapoznawczej jest podobne do wdrożenia standardowej zapory platformy Azure:

:::image type="content" source="media/premium-portal/premium-portal.png" alt-text="wdrożenie portalu":::

W przypadku **warstwy zapory** wybierz opcję **Premium (wersja zapoznawcza)** i dla **zasad zapory** Wybierz istniejące zasady Premium lub Utwórz nowe.

## <a name="configure-the-premium-policy"></a>Konfigurowanie zasad Premium

Konfigurowanie zasad zapory Premium jest podobne do konfigurowania standardowych zasad zapory. Korzystając z zasad Premium, można skonfigurować funkcje premium:

:::image type="content" source="media/premium-portal/premium-policy.png" alt-text="Wdrażanie zasad w warstwie Premium":::

### <a name="rule-configuration"></a>Konfiguracja reguły

Konfigurując reguły aplikacji w zasadach Premium, można skonfigurować dodatkowe funkcje premium:

:::image type="content" source="media/premium-portal/premium-application-rule.png" alt-text="Reguła Premium":::

## <a name="next-steps"></a>Następne kroki

Aby wyświetlić funkcje usługi Azure firewall Premium Preview w działaniu, zobacz [wdrażanie i Konfigurowanie usługi Azure firewall Premium Preview](premium-deploy.md).