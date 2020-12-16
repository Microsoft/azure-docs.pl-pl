---
title: Architektura monitorowania ciągłego pacjenta na platformie Azure IoT Central | Microsoft Docs
description: Samouczek — informacje o architekturze rozwiązania ciągłego monitorowania pacjenta.
author: philmea
ms.author: philmea
ms.date: 12/11/2020
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: dad6327acd24fe7642bf1033b1527ef3e40b772e
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590107"
---
# <a name="continuous-patient-monitoring-architecture"></a>Architektura ciągłego monitorowania pacjenta

W tym artykule opisano architekturę rozwiązania skompilowanego na podstawie szablonu **ciągłej kontroli nad pacjentem** :

Rozwiązania do monitorowania ciągłego pacjenta można skompilować przy użyciu dostarczonego szablonu aplikacji i korzystając z architektury przedstawionej poniżej jako wskazówki.

:::image type="content" source="media/cpm-architecture.png" alt-text="Architektura ciągłego monitorowania pacjenta":::

## <a name="details"></a>Szczegóły

W tej sekcji opisano szczegółowo poszczególne części diagramu architektury:

### <a name="bluetooth-low-energy-ble-medical-devices"></a>Urządzenia medyczne o niskim poziomie energii Bluetooth

Wiele noszenia medycznych używanych w ramach rozwiązań do ochrony zdrowia IoT to urządzenia z beli. Te urządzenia nie mogą komunikować się bezpośrednio z chmurą i muszą używać bramy do wymiany danych z rozwiązaniem w chmurze. Ta architektura używa aplikacji dla telefonów komórkowych jako bramy.

### <a name="mobile-phone-gateway"></a>Brama telefonu komórkowego

Podstawowa funkcja aplikacji telefonu komórkowego polega na zbieraniu danych z urządzeń medycznych i przekazywaniu ich do IoT Central. Aplikacja poprowadzi także przez proces konfiguracji urządzeń i pozwala na wyświetlanie ich osobistych danych o kondycji. Inne rozwiązania mogą korzystać z bramy typu tablet lub bramy statycznej w pokoju szpitala. Przykładowa aplikacja mobilna typu "open source" jest dostępna dla systemów Android i iOS do użycia jako punkt wyjścia do tworzenia aplikacji. Aby dowiedzieć się więcej, zobacz [IoT Central ciągłe monitorowanie aplikacji mobilnej](/samples/iot-for-all/iotc-cpm-sample/iotc-cpm-sample/).

### <a name="export-to-azure-api-for-fhirreg"></a>Eksportuj do interfejsu API platformy Azure dla usługi FHIR&reg;

Usługa Azure IoT Central jest zgodna z certyfikatami HIPAA i HITRUST &reg; . Dane kondycji pacjenta można także wysyłać do innych usług przy użyciu [interfejsu API platformy Azure dla usługi FHIR](../../healthcare-apis/overview.md). Azure API for FHIR to oparty na standardach interfejs API służący do danych klinicznych kondycji. [Łącznik usługi Azure IoT dla programu FHIR](../../healthcare-apis/iot-fhir-portal-quickstart.md) umożliwia korzystanie z interfejsu API platformy Azure dla FHIR jako stałego miejsca docelowego eksportu danych z IoT Central.

### <a name="machine-learning"></a>Uczenie maszynowe

Używaj modeli uczenia maszynowego z danymi FHIR, aby generować szczegółowe informacje i wspierać decyzje podejmowane przez zespół opieki. Aby dowiedzieć się więcej, zobacz [dokumentację usługi Azure Machine Learning](../../machine-learning/index.yml).

### <a name="provider-dashboard"></a>Pulpit nawigacyjny dostawcy

Użyj interfejsu API platformy Azure do FHIR danych, aby utworzyć pulpit nawigacyjny usługi pacjenta Insights lub zintegrować go bezpośrednio z elektronicznym rejestrem medycyny używanym przez zespoły opieki. Zespoły opieki mogą korzystać z pulpitu nawigacyjnego, aby pomóc pacjentom i identyfikować wczesne ostrzeżenia o pogorszeniu. Aby dowiedzieć się więcej, zobacz Samouczek dotyczący [tworzenia pulpitu nawigacyjnego dostawcy Power BI](howto-health-data-triage.md) .

## <a name="next-steps"></a>Następne kroki

Sugerowany następny krok to [Dowiedz się, jak wdrożyć szablon aplikacji do monitorowania ciągłego pacjenta](tutorial-continuous-patient-monitoring.md).