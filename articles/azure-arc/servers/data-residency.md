---
title: Rezydencja danych
description: Zamieszkania danych i informacje o serwerach z obsługą usługi Azure Arc.
ms.topic: reference
ms.date: 02/16/2021
ms.custom: references_regions
ms.openlocfilehash: d4764772473bbf2e5aafe2607a9462c9a6a15203
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100559501"
---
# <a name="azure-arc-enabled-servers-data-residency"></a>Serwery z obsługą usługi Azure ARC: dane w miejscu

W tym artykule wyjaśniono koncepcję zamieszkania danych i sposób jej zastosowania w przypadku serwerów z obsługą usługi Azure Arc.

Serwery z obsługą usługi Azure Arc są **[dostępne](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** w **Stany Zjednoczone, Europie, Zjednoczonym Królestwie, Australii i Azja i Pacyfik**.

## <a name="data-residency"></a>Rezydencja danych

Serwery z obsługą usługi Azure Arc przechowują ustawienia konfiguracji [rozszerzenia maszyny wirtualnej platformy Azure](manage-vm-extensions.md) (czyli wartości właściwości) rozszerzenie wymaga określenia przed próbą włączenia na podłączonej maszynie. Na przykład po włączeniu rozszerzenia maszyny wirtualnej Log Analytics zostanie wyświetlony monit o podanie **identyfikatora obszaru roboczego** log Analytics i **klucza podstawowego**.

Zbierane są również informacje o metadanych połączonej maszyny. W szczególności:

* Nazwa, typ i wersja systemu operacyjnego
* Nazwa komputera
* W pełni kwalifikowana nazwa domeny komputera (FQDN)
* Wersja agenta połączonej maszyny
* Active Directory i w pełni kwalifikowana nazwa domeny DNS (FQDN)
* UUID (IDENTYFIKATOR SYSTEMU BIOS)
* Puls agenta połączonej maszyny
* Wersja agenta połączonej maszyny
* Klucz publiczny dla tożsamości zarządzanej
* Szczegóły i stan zgodności zasad (jeśli jest używany Azure Policy zasad konfiguracji gościa)

Serwery z włączonymi łukiemmi umożliwiają określenie regionu, w którym będą przechowywane dane. Firma Microsoft może przeprowadzić replikację do innych regionów w celu odporności danych, ale firma Microsoft nie replikuje ani nie przenosi danych poza lokalizację geograficzną. Te dane są przechowywane w regionie, w którym jest skonfigurowany zasób maszyny usługi Azure Arc. Na przykład jeśli maszyna jest zarejestrowana przy użyciu łuku w regionie Wschodnie stany USA, te dane są przechowywane w regionie US.

Aby uzyskać więcej informacji o naszej regionalnej odporności i obsłudze zgodności, zobacz [Geografia Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o projektowaniu [odporności na platformę Azure](/azure/architecture/reliability/architect).
