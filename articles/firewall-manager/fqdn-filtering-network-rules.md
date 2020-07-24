---
title: Filtrowanie Menedżera zapory platformy Azure w regułach sieci (wersja zapoznawcza)
description: Jak używać filtrowania nazw FQDN w regułach sieci
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 599620c5fcc3ad1802527bd66e2dbead1b97d11d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87079034"
---
# <a name="fqdn-filtering-in-network-rules-preview"></a>Filtrowanie nazw FQDN w regułach sieci (wersja zapoznawcza)

> [!IMPORTANT]
> Filtrowanie nazw FQDN w regułach sieciowych jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W pełni kwalifikowana nazwa domeny (FQDN) reprezentuje nazwę domeny hosta. Nazwa domeny jest skojarzona z pojedynczym lub wieloma adresami IP. W regułach aplikacji można zezwalać na nazwy FQDN i Tagi FQDN oraz blokować je. Używając niestandardowych ustawień DNS i DNS serwera proxy, można również użyć filtrowania nazw FQDN w regułach sieci.

## <a name="how-it-works"></a>Jak to działa

Zapora platformy Azure tłumaczy nazwę FQDN na adresy IP przy użyciu ustawień DNS i przetwarza reguły na podstawie Azure DNS lub niestandardowej konfiguracji DNS.

Aby używać nazw FQDN w regułach sieci, należy włączyć serwer proxy DNS. Jeśli nie włączysz serwera proxy DNS, niezawodne przetwarzanie reguł jest zagrożone. Gdy ta funkcja jest włączona, ruch DNS jest kierowany do zapory platformy Azure, w której można skonfigurować niestandardowy serwer DNS. Następnie Zapora i klienci używają tego samego skonfigurowanego serwera DNS. Jeśli serwer proxy DNS nie jest włączony, Zapora platformy Azure może utworzyć inną odpowiedź, ponieważ klient i Zapora mogą używać różnych serwerów do rozpoznawania nazw. Filtrowanie nazw FQDN w regułach sieciowych może być wadliwe lub niespójne, jeśli klient i Zapora odbierają różne odpowiedzi DNS.

## <a name="next-steps"></a>Następne kroki

[Ustawienia usługi DNS zapory platformy Azure](dns-settings.md)
