---
title: Omówienie zasad Menedżera zapory platformy Azure
description: Informacje o zasadach Menedżera zapory platformy Azure
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: victorh
ms.openlocfilehash: 73a07af0fa98adf66d6104f1ab545d31a0cfd6d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95802023"
---
# <a name="azure-firewall-manager-policy-overview"></a>Omówienie zasad Menedżera zapory platformy Azure

Zasady zapory to zasób platformy Azure, który zawiera kolekcje reguł translatora adresów sieciowych, sieci i aplikacji oraz ustawienia analizy zagrożeń. Jest to zasób globalny, który może być używany w wielu wystąpieniach zapory platformy Azure w zabezpieczonych wirtualnych centrach i koncentratorach wirtualnych. Zasady działają między regionami i subskrypcjami.

![Zasady Menedżera zapory platformy Azure](media/policy-overview/policy-overview.png)

## <a name="policy-creation-and-association"></a>Tworzenie i kojarzenie zasad

Zasady można tworzyć i zarządzać nimi na wiele sposobów, w tym Azure Portal, interfejsu API REST, szablonów, Azure PowerShell i interfejsu wiersza polecenia.

Możesz również migrować istniejące reguły z zapory platformy Azure przy użyciu portalu lub Azure PowerShell, aby utworzyć zasady. Aby uzyskać więcej informacji, zobacz [jak migrować konfiguracje zapory platformy Azure do zasad zapory platformy Azure](migrate-to-policy.md). 

Zasady mogą być skojarzone z co najmniej jednym koncentratorem wirtualnym lub sieci wirtualnych. Zapora może należeć do dowolnej subskrypcji skojarzonej z Twoim kontem i w dowolnym regionie.

## <a name="hierarchical-policies"></a>Zasady hierarchiczne

Nowe zasady mogą być tworzone od podstaw lub dziedziczone z istniejących zasad. Dziedziczenie umożliwia DevOps do tworzenia lokalnych zasad zapory na podstawie zasad obowiązujących w organizacji.

Zasady utworzone przy użyciu zasad nadrzędnych, które nie są puste, dziedziczą wszystkie kolekcje reguł z zasad nadrzędnych. Kolekcje reguł sieciowych dziedziczone z zasad nadrzędnych są zawsze priorytetowe względem kolekcji reguł sieci zdefiniowanych w ramach nowych zasad. Ta sama logika dotyczy również kolekcji reguł aplikacji. Jednak kolekcje reguł sieci są zawsze przetwarzane przed kolekcjami reguł aplikacji niezależnie od dziedziczenia.

Tryb analizy zagrożeń jest również Dziedziczony z zasad nadrzędnych. Można ustawić tryb analizy zagrożeń na inną wartość, aby przesłonić to zachowanie, ale nie można go wyłączyć. Można przesłonić tylko wartość bardziej rygorystyczną. Na przykład jeśli zasady nadrzędne są ustawione tylko na **alerty**, można skonfigurować te zasady lokalne w taki sposób, aby były **wyzwalane i odrzucane**.

Podobnie jak w przypadku trybu analizy zagrożeń lista dozwolonych analiz zagrożeń jest dziedziczona z zasad nadrzędnych. Zasady podrzędne mogą dodawać dodatkowe adresy IP do listy dozwolonych.

Kolekcje reguł translatora adresów sieciowych nie są dziedziczone, ponieważ są specyficzne dla danej zapory.

Dzięki dziedziczeniu wszelkie zmiany zasad nadrzędnych są automatycznie stosowane do skojarzonych zasad podrzędnych zapory.

## <a name="traditional-rules-and-policies"></a>Tradycyjne reguły i zasady

Zapora systemu Azure obsługuje zarówno tradycyjne reguły, jak i zasady. Poniższa tabela zawiera porównanie zasad i zasad:


| Temat | Zasady  | Reguły |
| ------- | ------- | ----- |
|Contains     |Translator adresów sieciowych, Sieć, reguły aplikacji, niestandardowe ustawienia DNS i serwera proxy DNS, grupy adresów IP i ustawienia analizy zagrożeń (w tym lista dozwolonych)|Reguły translatora adresów sieciowych, sieci i aplikacji, niestandardowe ustawienia DNS i DNS serwera proxy, grupy adresów IP i ustawienia analizy zagrożeń (w tym lista dozwolonych)|
|Chroni     |Wirtualne centra i sieci wirtualne|Tylko sieci wirtualne|
|Środowisko portalu     |Centralne zarządzanie przy użyciu Menedżera zapory|Środowisko autonomicznej zapory|
|Obsługa wielu zapór     |Zasady zapory to oddzielne zasoby, które mogą być używane przez zapory|Ręcznie Eksportuj i Importuj reguły lub przy użyciu rozwiązań do zarządzania innych firm |
|Ceny     |Opłaty są naliczane na podstawie skojarzenia zapory. Zobacz [Cennik](#pricing).|Bezpłatna|
|Obsługiwane mechanizmy wdrażania     |Portal, interfejs API REST, szablony, Azure PowerShell i interfejs wiersza polecenia|Portal, interfejs API REST, szablony, PowerShell i interfejs wiersza polecenia. |

## <a name="pricing"></a>Ceny

Zasady są rozliczane na podstawie skojarzeń zapory. Zasady z nierównym lub jednym skojarzeniem zapory są bezpłatne. Zasady z wieloma skojarzeniami zapory są rozliczane według ustalonej stawki. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure firewall Manager](https://azure.microsoft.com/pricing/details/firewall-manager/).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak wdrożyć zaporę platformy Azure, zobacz [Samouczek: Zabezpieczanie sieci w chmurze za pomocą Menedżera zapory platformy Azure przy użyciu Azure Portal](secure-cloud-network.md).
