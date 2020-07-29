---
title: Konfigurowanie ochrony bot dla zapory aplikacji sieci Web za pomocą platformy Azure — drzwi (wersja zapoznawcza)
description: Poznaj zaporę aplikacji sieci Web (WAF).
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: f48b683044bc727cda461fb64a743c055188962a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "76934647"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Konfigurowanie ochrony bot dla zapory aplikacji sieci Web (wersja zapoznawcza)
W tym artykule opisano sposób konfigurowania reguły ochrony bot w zaporze aplikacji sieci Web platformy Azure (WAF) dla drzwi zewnętrznych przy użyciu Azure Portal. Regułę ochrony bot można także skonfigurować przy użyciu szablonu interfejsu wiersza polecenia, Azure PowerShell lub Azure Resource Manager.

> [!IMPORTANT]
> Zestaw reguł ochrony bot jest obecnie w publicznej wersji zapoznawczej i jest dostarczany z umową dotyczącą poziomu usług w wersji zapoznawczej. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.  Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Utwórz podstawowe zasady WAF dla drzwi przednich, postępując zgodnie z instrukcjami opisanymi w artykule [Tworzenie zasad WAF dla drzwi platformy Azure z użyciem Azure Portal](waf-front-door-create-portal.md).

## <a name="enable-bot-protection-rule-set"></a>Włącz zestaw reguł ochrony bot

Na stronie **reguły zarządzane** podczas tworzenia zasad zapory aplikacji sieci Web, najpierw Znajdź sekcję **zarządzany zestaw reguł** , zaznacz pole wyboru przed regułą **Microsoft_BotManager_1 0** z menu rozwijanego, a następnie wybierz polecenie **Przegląd + Utwórz**.

   ![Reguła ochrony bot](.././media/waf-front-door-configure-bot-protection/botmanager112019.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [monitorować WAF](waf-front-door-monitor.md).
