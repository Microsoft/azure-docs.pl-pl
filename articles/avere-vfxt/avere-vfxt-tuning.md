---
title: Dostrajanie klastra avere vFXT — platforma Azure
description: Dowiedz się więcej na temat niektórych niestandardowych dostrojenia klastrów vFXT w avere vFXT dla platformy Azure, które można wykonać, pracując z przedstawicielem pomocy technicznej.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 5d9f81c9438cb992f81bd3e6319532d67db75552
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88272386"
---
# <a name="cluster-tuning"></a>Dostosowywanie klastra

Większość klastrów vFXT może korzystać z niestandardowych ustawień wydajności. Te ustawienia pomagają, aby klaster działał najlepiej z określonym przepływem pracy, zestawem danych i narzędziami.

Takie dostosowanie powinno odbywać się z pomocą techniczną, ponieważ może to dotyczyć konfigurowania funkcji, które nie są dostępne w panelu sterowania avere.

W tej sekcji opisano niektóre niestandardowe dostrajania, które można wykonać.

## <a name="general-optimizations"></a>Optymalizacje ogólne

Te zmiany mogą być zalecane na podstawie jakości zestawu danych lub stylu przepływu pracy.

* Jeśli obciążenie ma duże możliwości zapisu, należy zwiększyć rozmiar pamięci podręcznej zapisu z wartości domyślnej 20%.
* Jeśli zestaw danych zawiera wiele małych plików, zwiększ limit liczby plików pamięci podręcznej klastra.
* Jeśli prace obejmują kopiowanie lub przeniesienie danych między dwoma repozytoriami, Dostosuj liczbę wątków używanych do przeniesienia danych:
  * Aby zwiększyć szybkość, można zwiększyć liczbę używanych wątków równoległych.
  * Jeśli wolumin magazynu zaplecza zostanie przeciążony, może być konieczne zmniejszenie liczby użytych wątków równoległych.
* Jeśli klaster buforuje dane dla podstawowego pliku, który używa list ACL NFSv4, Włącz buforowanie trybu dostępu, aby usprawnić autoryzację plików dla określonych klientów.

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>Optymalizacje NAS lub Cloud Gateway w chmurze

W scenariuszu serwera NAS lub bramy w chmurze klaster vFXT zapewnia dostęp w stylu NAS do kontenera chmury. Aby wykorzystać wyższe szybkości danych między klastrem vFXT i magazynem w chmurze, przedstawiciel może zalecić zmianę ustawień w celu bardziej agresywnego wypychania danych do woluminu magazynu z pamięci podręcznej. Na przykład:

* Zwiększ liczbę połączeń TCP między klastrem a kontenerem magazynu

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>Przenoszenie w chmurze lub hybrydowe optymalizacje sieci WAN

W scenariuszu obejmującym scalanie w chmurze lub hybrydowe scenariusze optymalizacji sieci WAN klaster vFXT zapewnia integrację między lokalnym magazynem sprzętowym a chmurowym. Te zmiany mogą być przydatne:

* Zwiększ liczbę połączeń TCP dozwolonych między klastrem a podstawowym plikiem
* Włącz ustawienie optymalizacji sieci WAN dla zdalnego pliku podstawowego. to ustawienie może być używane w przypadku zdalnego lokalnego pliku lub pliku w chmurze w innym regionie świadczenia usługi Azure.
* Zwiększ rozmiar buforu gniazda TCP<sup>*</sup>
* Włącz ustawienie "Zawsze przesyłaj dalej", aby zmniejszyć ilość plików buforowanych w pamięci podręcznej<sup>*</sup>

<sup>*</sup>Te zmiany mogą nie dotyczyć wszystkich systemów, w zależności od potrzeb obciążenia i wydajności.

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>Pomóż zoptymalizować swój avere vFXT dla platformy Azure

Aby skontaktować się z personelem pomocy technicznej na temat tych optymalizacji, użyj procedury opisanej w artykule [Uzyskiwanie pomocy](avere-vfxt-open-ticket.md)dotyczącej systemu.
