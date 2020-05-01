---
title: Architektura monitorowania ciągłego pacjenta na platformie Azure IoT Central | Microsoft Docs
description: Poznaj architekturę rozwiązania ciągłego monitorowania pacjenta.
author: philmea
ms.author: philmea
ms.date: 10/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 92eb4157abb55b7056952d1fb064c7c7d7500335
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77021700"
---
# <a name="continuous-patient-monitoring-architecture"></a>Architektura ciągłego monitorowania pacjenta



Rozwiązania do monitorowania ciągłego pacjenta można skompilować przy użyciu dostarczonego szablonu aplikacji i korzystając z architektury wymienionej poniżej jako wskazówki.

>[!div class="mx-imgBorder"] 
>![Architektura CPM](media/cpm-architecture.png)

1. Urządzenia medyczne komunikują się za pomocą technologii Bluetooth Low Energy (beli)
1. Brama telefonu komórkowego otrzymuje dane z beli i wysyła do IoT Central
1. Ciągły eksport danych o kondycji pacjenta do interfejsu API platformy Azure dla usługi FHIR&reg;
1. Uczenie maszynowe na podstawie danych międzyoperacyjnych
1. Pulpit nawigacyjny zespołu opiekowego oparty na danych FHIR

## <a name="details"></a>Szczegóły
W tej sekcji opisano szczegółowo każdą część diagramu architektury.

### <a name="ble-medical-devices"></a>Urządzenia medyczne dotyczące przebeli
Wiele noszenia medycznych używanych w obszarze IoT opieki zdrowotnej to urządzenia o niskiej energii Bluetooth. Nie są one w stanie mówić bezpośrednio do chmury i będą musiały zostać przekazane przez bramę. Ta architektura sugeruje użycie aplikacji dla telefonów komórkowych jako tej bramy.

### <a name="mobile-phone-gateway"></a>Brama telefonu komórkowego
Główną funkcją aplikacji telefonii mobilnej jest pozyskiwanie danych z urządzeń medycznych i przekazywanie ich do IoT Central platformy Azure. Ponadto aplikacja może pomóc w zapełnieniu pacjentów za pomocą konfiguracji urządzenia i przepływu aprowizacji, a także ułatwić im wyświetlanie danych osobistych dotyczących kondycji. Inne rozwiązania mogą korzystać z bramy typu tablet lub bramy statycznej, jeśli znajduje się w pokoju szpitalnym do osiągnięcia tego samego przepływu komunikacji.

### <a name="export-to-azure-api-for-fhirreg"></a>Eksportuj do interfejsu API platformy Azure dla usługi FHIR&reg;
Usługa Azure IoT Central jest zgodna z certyfikatem&reg; HIPAA i HiTRUST, ale możesz również wysyłać dane dotyczące kondycji pacjenta do interfejsu API platformy Azure dla FHIR. [Usługa Azure API for FHIR](../../healthcare-apis/overview.md) to w pełni zarządzany, oparty na standardach interfejs API służący do klinicznych danych dotyczących kondycji, które umożliwiają tworzenie nowych systemów zaangażowania przy użyciu danych dotyczących kondycji. Umożliwia szybką wymianę danych za pomocą interfejsów API FHIR, które są obsługiwane przez zarządzaną ofertę platformy jako usługi (PaaS) w chmurze. Korzystając z funkcji ciągłego eksportowania danych IoT Central, można wysyłać dane do interfejsu API platformy Azure dla FHIR.

### <a name="machine-learning"></a>Uczenie maszynowe
Po agregowaniu danych i przeprowadzeniu ich translacji do formatu FHIR można kompilować modele uczenia maszynowego, które mogą wzbogacać szczegółowe informacje i umożliwiają inteligentniejsze podejmowanie decyzji dla Twojego zespołu opieki. Istnieją różne rodzaje usług, których można użyć do kompilowania, uczenia i wdrażania modeli uczenia maszynowego. Więcej informacji o sposobach korzystania z ofert uczenia maszynowego na platformie Azure znajduje się w naszej [dokumentacji usługi Machine Learning](../../machine-learning/index.yml).

### <a name="provider-dashboard"></a>Pulpit nawigacyjny dostawcy
Dane znajdujące się w interfejsie API platformy Azure dla FHIR mogą służyć do tworzenia pulpitu nawigacyjnego usługi pacjenta Insights lub mogą być bezpośrednio zintegrowane z EMR, aby pomóc zespołom w pomyślnym wizualizowaniu stanu pacjenta. Zespoły opieki mogą korzystać z tego pulpitu nawigacyjnego, aby zadbać o to, aby uzyskać pomoc, oraz szybko ostrzegać o pogorszeniu. Aby dowiedzieć się, jak utworzyć pulpit nawigacyjny dostawcy Power BI w czasie rzeczywistym, postępuj zgodnie z naszym [przewodnikiem](howto-health-data-triage.md).

## <a name="next-steps"></a>Następne kroki
* [Dowiedz się, jak wdrożyć szablon aplikacji do monitorowania ciągłego pacjenta](tutorial-continuous-patient-monitoring.md)