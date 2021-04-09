---
title: Kompilowanie rozwiązań dla platformy Azure IoT Central | Microsoft Docs
description: Azure IoT Central to platforma aplikacji IoT, która upraszcza tworzenie rozwiązań IoT. Ten artykuł zawiera omówienie tworzenia zintegrowanych rozwiązań z IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 72aa8e5e3284e0ee7fbe63e0fb617b9eba03292e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100417805"
---
# <a name="iot-central-solution-builder-guide"></a>Przewodnik po konstruktorze rozwiązań IoT Central

*Ten artykuł ma zastosowanie do konstruktorów rozwiązań.*

Aplikacja IoT Central umożliwia monitorowanie milionów urządzeń i zarządzanie nimi w całym cyklu życia. Ten przewodnik jest przeznaczony dla konstruktorów rozwiązań, którzy używają IoT Central do kompilowania zintegrowanych rozwiązań. Aplikacja IoT Central umożliwia zarządzanie urządzeniami, analizowanie danych telemetrycznych urządzeń i integrację z innymi usługami zaplecza.

Konstruktor rozwiązań:

- Konfiguruje pulpity nawigacyjne i widoki w IoT Central interfejsie użytkownika sieci Web.
- Program używa wbudowanych reguł i narzędzi analitycznych do uzyskiwania szczegółowych informacji o firmie z połączonych urządzeń.
- Program korzysta z funkcji eksportu i reguł dotyczących danych, aby zintegrować IoT Central z innymi usługami zaplecza.

## <a name="configure-dashboards-and-views"></a>Konfigurowanie pulpitów nawigacyjnych i widoków

Aplikacja IoT Central może mieć co najmniej jeden pulpit nawigacyjny używany przez operatorów do wyświetlania aplikacji i korzystania z niej. Jako Konstruktor rozwiązań możesz dostosować domyślnego pulpitu nawigacyjnego i utworzyć wyspecjalizowane pulpity nawigacyjne:

- Aby wyświetlić niektóre przykłady dostosowanych pulpitów nawigacyjnych, zobacz [branżowe szablony](concepts-app-templates.md#industry-focused-templates).
- Aby dowiedzieć się więcej na temat pulpitów nawigacyjnych, zobacz [Tworzenie wielu pulpitów nawigacyjnych i zarządzanie nimi](howto-create-personal-dashboards.md) oraz [Konfigurowanie pulpitu nawigacyjnego aplikacji](howto-add-tiles-to-your-dashboard.md).

Gdy urządzenie łączy się z IoT Central, urządzenie jest skojarzone z szablonem urządzenia dla typu urządzenia. Szablon urządzenia zawiera dostosowywalne widoki używane przez operatora do zarządzania indywidualnymi urządzeniami. Jako deweloper rozwiązania można tworzyć i dostosowywać dostępne widoki dla typu urządzenia. Aby dowiedzieć się więcej, zobacz [Dodawanie widoków](howto-set-up-template.md#add-views).

## <a name="use-built-in-rules-and-analytics"></a>Korzystanie z wbudowanych reguł i analiz

Deweloper rozwiązania może dodawać reguły do aplikacji IoT Central, która uruchamia dostosowywalne akcje. Reguły obliczają warunki na podstawie danych pochodzących z urządzenia, aby określić, kiedy należy uruchomić akcję. Aby dowiedzieć się więcej na temat reguł, zobacz:

- [Samouczek: Tworzenie reguły i konfigurowanie powiadomień w aplikacji usługi Azure IoT Central](tutorial-create-telemetry-rules.md)
- [Tworzenie akcji elementów webhook dla reguł w usłudze Azure IoT Central](howto-create-webhooks.md)
- [Grupuj wiele akcji do uruchomienia z jednej lub kilku reguł](howto-use-action-groups.md)

IoT Central ma wbudowane funkcje analityczne, których operator może użyć do analizowania danych przepływów z połączonych urządzeń. Aby dowiedzieć się więcej, zobacz [jak używać analiz do analizowania danych urządzenia](howto-create-analytics.md).

## <a name="integrate-with-other-services"></a>Integracja z innymi usługami

Jako Konstruktor rozwiązań możesz użyć możliwości eksportu i reguł danych w IoT Central, aby zintegrować z inną usługą. Aby dowiedzieć się więcej, zobacz:

- [Eksportowanie danych IoT do miejsc docelowych w chmurze przy użyciu eksportu danych](howto-export-data.md)
- [Korzystanie z przepływów pracy do integrowania aplikacji IoT Central platformy Azure z innymi usługami w chmurze](howto-configure-rules-advanced.md)
- [Rozszerzanie usługi Azure IoT Central o niestandardowe reguły przy użyciu usług Stream Analytics, Azure Functions i SendGrid](howto-create-custom-rules.md)
- [Rozszerzając IoT Central platformy Azure z analizą niestandardową przy użyciu Azure Databricks](howto-create-custom-analytics.md)
- [Wizualizowanie i analizowanie danych IoT Central platformy Azure na pulpicie nawigacyjnym Power BI](howto-connect-powerbi.md)

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz dowiedzieć się więcej o korzystaniu z IoT Central, sugerowane następne kroki to wypróbowanie przewodnika Szybki Start, rozpoczynając od [tworzenia aplikacji IoT Central platformy Azure](./quick-deploy-iot-central.md).
