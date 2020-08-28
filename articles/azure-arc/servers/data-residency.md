---
title: Rezydencja danych
description: Dane zamieszkania i informacje o serwerach z obsługą usługi Azure ARC (wersja zapoznawcza).
ms.topic: reference
ms.date: 08/25/2020
ms.custom: references_regions
ms.openlocfilehash: 8f207f5889c1764eebcc6081960ff70c0d5bca3a
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89048860"
---
# <a name="azure-arc-enabled-servers-preview-data-residency"></a>Serwery z obsługą usługi Azure ARC (wersja zapoznawcza): zamieszkania danych

W tym artykule wyjaśniono koncepcję zamieszkania i sposobu zastosowania do serwerów z obsługą usługi Azure ARC (wersja zapoznawcza).

Serwery z obsługą usługi Azure ARC (wersja zapoznawcza) są **[dostępne w wersji zapoznawczej](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** w **Stany Zjednoczone, Europie lub Azja i Pacyfik**.

## <a name="data-residency"></a>Rezydencja danych

Serwery z obsługą usługi Azure ARC (wersja zapoznawcza) przechowują ustawienia konfiguracji [rozszerzenia maszyny wirtualnej platformy Azure](manage-vm-extensions.md) (czyli wartości właściwości) rozszerzenie wymaga określenia przed próbą włączenia na podłączonej maszynie. Na przykład po włączeniu rozszerzenia maszyny wirtualnej Log Analytics zostanie wyświetlony monit o podanie **identyfikatora obszaru roboczego** log Analytics i **klucza podstawowego**.

Zbierane są również informacje o metadanych połączonej maszyny. W szczególności:

* Nazwa i wersja systemu operacyjnego
* Nazwa komputera
* W pełni kwalifikowana nazwa domeny komputera (FQDN)
* Wersja agenta połączonej maszyny

Serwery z włączonymi łukiem (wersja zapoznawcza) umożliwiają określenie regionu, w którym będą przechowywane dane. Firma Microsoft może przeprowadzić replikację do innych regionów w celu odporności danych, ale firma Microsoft nie replikuje ani nie przenosi danych poza lokalizację geograficzną. Te dane są przechowywane w regionie, w którym jest skonfigurowany zasób maszyny usługi Azure Arc. Na przykład jeśli maszyna jest zarejestrowana przy użyciu łuku w regionie Wschodnie stany USA, te dane są przechowywane w regionie US.

Aby uzyskać więcej informacji o naszej regionalnej odporności i obsłudze zgodności, zobacz [Geografia Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o projektowaniu [odporności na platformę Azure](/azure/architecture/reliability/architect).