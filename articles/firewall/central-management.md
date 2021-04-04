---
title: Centralne zarządzanie zaporą platformy Azure
description: Dowiedz się więcej o zarządzaniu centralnym Menedżera zapory platformy Azure
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: victorh
ms.openlocfilehash: 23a7682d8a64de57db4ff9ae785ada90d4a06944
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87084674"
---
# <a name="azure-firewall-central-management"></a>Centralne zarządzanie zaporą platformy Azure

Jeśli zarządzasz wieloma zaporami, wiesz, że stale zmieniające się reguły zapory utrudniają ich synchronizację. Centralne zespoły IT muszą mieć możliwość zdefiniowania podstawowych zasad zapory i wymuszania ich w wielu jednostkach roboczych. W tym samym czasie zespoły DevOps chcą utworzyć własne lokalne, pochodne zasady zapory, aby zapewnić lepszą elastyczność.

Menedżer zapory platformy Azure może pomóc w rozwiązaniu tych problemów.


## <a name="azure-firewall-manager"></a>Azure Firewall Manager

Menedżer zapory platformy Azure to usługa zarządzania zabezpieczeniami sieci, która zapewnia centralne zasady zabezpieczeń i Zarządzanie trasami dla obwodów zabezpieczeń opartych na chmurze. Ułatwia ona zespołom IT korporacyjnym centralne definiowanie reguł na poziomie sieci i aplikacji na potrzeby filtrowania ruchu w wielu wystąpieniach zapory platformy Azure. W przypadku zarządzania ruchem i ochrony w architekturze gwiazdy można rozróżnić różne regiony i subskrypcje platformy Azure. Zapewnia również lepszą elastyczność dzięki pochodnym zasadom zabezpieczeń zapory lokalnej, które są implementowane w różnych organizacjach.

### <a name="firewall-policy"></a>Zasady zapory

Zasady zapory to zasób platformy Azure, który zawiera kolekcje reguł translatora adresów sieciowych, sieci i aplikacji oraz ustawienia analizy zagrożeń. Jest to zasób globalny, który może być używany w wielu wystąpieniach zapory platformy Azure w *zabezpieczonych wirtualnych centrach* i *koncentratorach* wirtualnych. Nowe zasady mogą być tworzone od podstaw lub dziedziczone z istniejących zasad. Dziedziczenie umożliwia DevOps do tworzenia lokalnych zasad zapory na podstawie zasad obowiązujących w organizacji. Zasady działają między regionami i subskrypcjami.
 
Zasady zapory i skojarzenia można utworzyć za pomocą Menedżera zapory platformy Azure. Można jednak tworzyć zasady i zarządzać nimi za pomocą interfejsu API REST, szablonów, Azure PowerShell i interfejsu wiersza polecenia. Po utworzeniu zasad można je skojarzyć z zaporą w wirtualnym koncentratorze sieci WAN, aby *zabezpieczyć ją koncentratorem wirtualnym* i/lub zaporą w sieci wirtualnej tworzącą *Virtual Network centrum* IT.

### <a name="pricing"></a>Ceny

Zasady są rozliczane na podstawie skojarzeń zapory. Zasady z nierównym lub jednym skojarzeniem zapory są bezpłatne. Zasady z wieloma skojarzeniami zapory są rozliczane według ustalonej stawki. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure firewall Manager](https://azure.microsoft.com/pricing/details/firewall-manager/).

## <a name="azure-firewall-management-partners"></a>Partnerzy zarządzania zaporą platformy Azure

Poniższe wiodące rozwiązania innych firm obsługują usługę Azure firewall Central Management przy użyciu standardowych interfejsów API REST platformy Azure. Każdy z tych rozwiązań ma własne, unikatowe cechy i funkcje:

- [AlgoSec CloudFlow](https://www.algosec.com/azure/) 
- [Opiekun zabezpieczeń w chmurze Barracuda](https://www.barracuda.com/products/cloudsecurityguardian/for_azure)
- [Tufin Orca](https://www.tufin.com/products/tufin-orca)


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Menedżera zapory platformy Azure, zobacz [co to jest Menedżer zapory platformy Azure?](../firewall-manager/overview.md)