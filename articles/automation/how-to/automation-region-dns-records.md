---
title: Rekordy DNS centrum danych platformy Azure używane przez Azure Automation | Microsoft Docs
description: Ten artykuł zawiera rekordy DNS wymagane przez Azure Automation funkcje w przypadku ograniczenia komunikacji z określonym regionem świadczenia usługi Azure, w którym znajduje się to konto usługi Automation.
services: automation
ms.subservice: process-automation
ms.date: 11/25/2020
ms.topic: conceptual
ms.openlocfilehash: d41d825c7bc33e05815c7528b436c85873859928
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168930"
---
# <a name="dns-records-for-azure-regions-used-by-azure-automation"></a>Rekordy DNS dla regionów platformy Azure używanych przez Azure Automation

Usługa [Azure Automation](../automation-intro.md) używa wielu rekordów DNS w celu nawiązania połączenia z usługą. Jeśli masz konto usługi Automation zdefiniowane dla określonego regionu, możesz ograniczyć komunikację z tym regionalnym centrum danych. Może być konieczne poznanie tych rekordów, aby umożliwić działanie następujących funkcji automatyzacji, gdy są one hostowane za zaporą:

* Hybrydowy proces roboczy elementu Runbook
* Konfiguracja stanu
* Elementy webhook

>[!NOTE]
>Rejestracja hybrydowego procesu roboczego elementu Runbook systemu Linux kończy się niepowodzeniem z nowymi rekordami, chyba że jest w wersji 1.6.10.2 lub nowszej. Musisz uaktualnić program do nowszej wersji [agenta log Analytics dla systemu Linux](../../azure-monitor/agents/agent-linux.md) , aby komputer otrzymał zaktualizowaną wersję roli proces roboczy i korzystał z tych nowych rekordów. Istniejące maszyny będą nadal działać bez żadnych problemów.  

## <a name="dns-records-per-region"></a>Rekordy DNS na region

Poniższa tabela zawiera rekord DNS dla każdego regionu.

>[!NOTE]
>Mimo że lista rekordów DNS usługi Automation w tym miejscu została wycofana, nadal pozostają funkcjonalne, aby umożliwić przeprowadzenie migracji do nowych rekordów wymienionych w obszarze [Obsługa prywatnego linku](#support-for-private-link) i zapobieganie błędom procesów automatyzacji.

| **Region** | **Rekord DNS** |
| --- | --- |
| Australia Środkowa |ac-jobruntimedata-prod-su1.azure-automation.net</br>ac-agentservice-prod-1.azure-automation.net |
| Australia Wschodnia |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| Australia Południowo-Wschodnia |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Kanada Środkowa |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Indie Środkowe |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Wschodnie stany USA 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Japonia Wschodnia |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Europa Północna |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| South Central US |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Azja Południowo-Wschodnia |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Południowe Zjednoczone Królestwo | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov Wirginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |
| Zachodnio-środkowe stany USA | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| West Europe |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Zachodnie stany USA 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |

### <a name="support-for-private-link"></a>Obsługa linku prywatnego

Aby zapewnić obsługę [prywatnego linku](../../private-link/private-link-overview.md) w Azure Automation, rekordy DNS dla każdego obsługiwanego centrum danych zostały zaktualizowane. Zamiast adresów URL specyficznych dla regionu adresy URL są specyficzne dla konta usługi Automation.

| **Region** | **Rekord DNS** |
| --- | --- |
| Zachodnio-środkowe stany USA |`https://<accountId>.webhook.wcus.azure-automation.net`<br>`https://<accountId>.agentsvc.wcus.azure-automation.net`<br>`https://<accountId>.jrds.wcus.azure-automation.net` |
| Zachodnie stany USA |`https://<accountId>.webhook.wus.azure-automation.net`<br>`https://<accountId>.agentsvc.wus.azure-automation.net`<br>`https://<accountId>.jrds.wus.azure-automation.net` |
| Zachodnie stany USA 2 |`https://<accountId>.webhook.wus2.azure-automation.net`<br>`https://<accountId>.agentsvc.wus2.azure-automation.net`<br>`https://<accountId>.jrds.wus2.azure-automation.net` |
| Central US |`https://<accountId>.webhook.cus.azure-automation.net`<br>`https://<accountId>.agentsvc.cus.azure-automation.net`<br>`https://<accountId>.jrds.cus.azure-automation.net` |
| South Central US |`https://<accountId>.webhook.scus.azure-automation.net`<br>`https://<accountId>.agentsvc.scus.azure-automation.net`<br>`https://<accountId>.jrds.scus.azure-automation.net` |
| Północno-środkowe stany USA |`https://<accountId>.webhook.ncus.azure-automation.net`<br>`https://<accountId>.agentsvc.ncus.azure-automation.net`<br>`https://<accountId>.jrds.ncus.azure-automation.net` |
| East US |`https://<accountId>.webhook.eus.azure-automation.net`<br>`https://<accountId>.agentsvc.eus.azure-automation.net`<br>`https://<accountId>.jrds.eus.azure-automation.net` |
| Wschodnie stany USA 2 |`https://<accountId>.webhook.eus2.azure-automation.net`<br>`https://<accountId>.agentsvc.eus2.azure-automation.net`<br>`https://<accountId>.jrds.eus2.azure-automation.net` |
| Kanada Środkowa |`https://<accountId>.webhook.cc.azure-automation.net`<br>`https://<accountId>.agentsvc.cc.azure-automation.net`<br>`https://<accountId>.jrds.cc.azure-automation.net` |
| West Europe |`https://<accountId>.webhook.we.azure-automation.net`<br>`https://<accountId>.agentsvc.we.azure-automation.net`<br>`https://<accountId>.jrds.we.azure-automation.net` |
| Europa Północna |`https://<accountId>.webhook.ne.azure-automation.net`<br>`https://<accountId>.agentsvc.ne.azure-automation.net`<br>`https://<accountId>.jrds.ne.azure-automation.net` |
| Azja Południowo-Wschodnia |`https://<accountId>.webhook.sea.azure-automation.net`<br>`https://<accountId>.agentsvc.sea.azure-automation.net`<br>`https://<accountId>.jrds.sea.azure-automation.net` |
| Azja Wschodnia |`https://<accountId>.webhook.ea.azure-automation.net`<br>`https://<accountId>.agentsvc.ea.azure-automation.net`<br>`https://<accountId>.jrds.ea.azure-automation.net` |
| Indie Środkowe |`https://<accountId>.webhook.cid.azure-automation.net`<br>`https://<accountId>.agentsvc.cid.azure-automation.net`<br>`https://<accountId>.jrds.cid.azure-automation.net` |
| Japonia Wschodnia |`https://<accountId>.webhook.jpe.azure-automation.net`<br>`https://<accountId>.agentsvc.jpe.azure-automation.net`<br>`https://<accountId>.jrds.jpe.azure-automation.net` |
| Korea Środkowa |`https://<accountId>.webhook.kc.azure-automation.net`<br>`https://<accountId>.agentsvc.kc.azure-automation.net`<br>`https://<accountId>.jrds.kc.azure-automation.net` |
| Australia Południowo-Wschodnia |`https://<accountId>.webhook.ase.azure-automation.net`<br>`https://<accountId>.agentsvc.ase.azure-automation.net`<br>`https://<accountId>.jrds.ase.azure-automation.net` |
| Australia Wschodnia |`https://<accountId>.webhook.ae.azure-automation.net`<br>`https://<accountId>.agentsvc.ae.azure-automation.net`<br>`https://<accountId>.jrds.ae.azure-automation.net` |
| Australia Środkowa |`https://<accountId>.webhook.ac.azure-automation.net`<br>`https://<accountId>.agentsvc.ac.azure-automation.net`<br>`https://<accountId>.jrds.ac.azure-automation.net` |
| Południowe Zjednoczone Królestwo |`https://<accountId>.webhook.uks.azure-automation.net`<br>`https://<accountId>.agentsvc.uks.azure-automation.net`<br>`https://<accountId>.jrds.uks.azure-automation.net` |
| Francja Środkowa |`https://<accountId>.webhook.fc.azure-automation.net`<br>`https://<accountId>.agentsvc.fc.azure-automation.net`<br>`https://<accountId>.jrds.fc.azure-automation.net` |
| Północna Republika Południowej Afryki |`https://<accountId>.webhook.san.azure-automation.net`<br>`https://<accountId>.agentsvc.san.azure-automation.net`<br>`https://<accountId>.jrds.san.azure-automation.net` |
| Brazylia Południowa |`https://<accountId>.webhook.brs.azure-automation.net`<br>`https://<accountId>.agentsvc.brs.azure-automation.net`<br>`https://<accountId>.jrds.brs.azure-automation.net` |
| Chiny Północne |`https://<accountId>.webhook.bjb.azure-automation.cn`<br>`https://<accountId>.agentsvc.bjb.azure-automation.cn`<br>`https://<accountId>.jrds.bjb.azure-automation.cn` |
| Chiny Północne 2 |`https://<accountId>.webhook.bjs2.azure-automation.cn`<br>`https://<accountId>.agentsvc.bjs2.azure-automation.cn`<br>`https://<accountId>.jrds.bjs2.azure-automation.cn` |
| Chiny Wschodnie 2 |`https://<accountId>.webhook.sha2.azure-automation.cn`<br>`https://<accountId>.agentsvc.sha2.azure-automation.cn`<br>`https://<accountId>.jrds.sha2.azure-automation.cn` |
| US Gov Wirginia |`https://<accountId>.webhook.usge.azure-automation.us`<br>`https://<accountId>.agentsvc.usge.azure-automation.us`<br>`https://<accountId>.jrds.usge.azure-automation.us` |
| US Gov Teksas |`https://<accountId>.webhook.ussc.azure-automation.us`<br>`https://<accountId>.agentsvc.ussc.azure-automation.us`<br>`https://<accountId>.jrds.ussc.azure-automation.us` |
| US Gov Arizona |`https://<accountId>.webhook.phx.azure-automation.us`<br>`https://<accountId>.agentsvc.phx.azure-automation.us`<br>`https://<accountId>.jrds.phx.azure-automation.us` |

Zamień `<accountId>` rekord DNS na identyfikator GUID reprezentujący identyfikator konta usługi Automation na podstawie **adresu URL** wartości. Możesz uzyskać identyfikator wymagany z **kluczy** w obszarze **ustawienia konta** w Azure Portal.

![Strona klucza podstawowego konta usługi Automation](./media/automation-region-dns-records/automation-account-keys.png)

Skopiuj wartość po *kont/* z pola **adresu URL**`https://<GUID>.agentsvc.<region>.azure-automation.net/accounts/<GUID>`

> [!NOTE]
> Wszystkie rekordy usługi webhook i agentservice DNS zostały zaktualizowane do nowych rekordów DNS w celu obsługi linku prywatnego. W przypadku rekordów DNS JRDS obsługiwane są zarówno stare, jak i nowe style DNS. Jeśli nie używasz linku prywatnego, będziesz widzieć stare rekordy DNS, podczas gdy przy użyciu linku prywatnego zobaczysz nowy styl rekordów DNS.

Zalecamy korzystanie z adresów wymienionych podczas definiowania [wyjątków](../automation-runbook-execution.md#exceptions). Aby uzyskać listę adresów IP regionów zamiast nazw regionów, Pobierz plik JSON z centrum pobierania Microsoft dla następujących środowisk w chmurze:

* [Zakresy adresów IP i Tagi usług platformy Azure — publiczne platformy Azure](https://www.microsoft.com/download/details.aspx?id=56519)
* [Zakresy adresów IP platformy Azure i Tagi usług — Azure Government](https://www.microsoft.com/download/details.aspx?id=57063)
* [Zakresy adresów IP platformy Azure i Tagi usług — platforma Azure (Niemcy)](https://www.microsoft.com/download/details.aspx?id=57064)
* [Zakresy adresów IP i Tagi usług platformy Azure — Chiny Vianet 21](https://www.microsoft.com/download/details.aspx?id=57062)

Plik adresu IP zawiera listę zakresów adresów IP, które są używane w centrach danych Microsoft Azure. Obejmuje ona zakresy obliczeń, SQL i magazynu oraz odzwierciedla aktualnie wdrożone zakresy i wszelkie nadchodzące zmiany w zakresach adresów IP. Nowe zakresy, które pojawiają się w pliku nie są używane w centrach danych przez co najmniej jeden tydzień.

Dobrym pomysłem jest pobranie nowego pliku adresów IP co tydzień. Następnie zaktualizuj swoją witrynę, aby prawidłowo identyfikować usługi działające na platformie Azure.

> [!NOTE]
> Jeśli używasz usługi Azure ExpressRoute, pamiętaj, że plik adresów IP służy do aktualizowania anonsu Border Gateway Protocol (BGP) usługi Azure Space w pierwszym tygodniu każdego miesiąca.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak rozwiązywać problemy dotyczące hybrydowych procesów roboczych elementów Runbook, zobacz [Rozwiązywanie problemów z hybrydowym procesem roboczym](../troubleshoot/hybrid-runbook-worker.md#general)

* Aby dowiedzieć się, jak rozwiązywać problemy z konfiguracją stanu, zobacz [Rozwiązywanie problemów z konfiguracją stanu](../troubleshoot/desired-state-configuration.md).