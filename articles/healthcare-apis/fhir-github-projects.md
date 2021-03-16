---
title: Powiązane projekty GitHub dla interfejsu API platformy Azure dla usługi FHIR
description: Wyświetl listę wszystkich repozytoriów programu Open Source (GitHub) dla interfejsu API platformy Azure dla usługi FHIR.
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/01/2021
ms.author: ginle
ms.openlocfilehash: 9977765183bd567377592631d65f3e548bef4b34
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103548022"
---
# <a name="related-github-projects"></a>Powiązane projekty GitHub

W usłudze GitHub istnieje wiele projektów open source, które udostępniają kod źródłowy i instrukcje wdrażania usług do różnych celów. Zawsze zapraszamy do odwiedzenia naszych repozytoriów usługi GitHub, aby uczyć się i eksperymentować z naszymi funkcjami i produktami. 

## <a name="fhir-server"></a>Serwer FHIR
* [Microsoft/fhir-Server](https://github.com/microsoft/fhir-server/): serwer fhir Open Source, który stanowi podstawę dla interfejsu API platformy Azure dla usługi fhir
* Aby zapoznać się z najnowszymi wersjami, zapoznaj się z [informacjami o wersji](https://github.com/microsoft/fhir-server/releases) .
* [Microsoft/fhir-Server-Samples](https://github.com/microsoft/fhir-server-samples): Przykładowe środowisko

## <a name="data-conversion--anonymization"></a>Konwersja danych & zachowywanie anonimowości

#### <a name="fhir-converter"></a>Konwerter FHIR
* [Microsoft/FHIR-Converter](https://github.com/microsoft/FHIR-Converter): narzędzie konwersji służące do tłumaczenia starszych formatów danych na FHIR
* Integracja z interfejsem API platformy Azure dla programu FHIR oraz serwera FHIR dla platformy Azure w formie $convert operacji na danych
* Bieżące ulepszenia usługi OSS i ciągła integracja z serwerami FHIR
 
#### <a name="fhir-converter---vs-code-extension"></a>Konwerter FHIR — rozszerzenie VS Code
* [Microsoft/FHIR-Tools-for-zachowywanie anonimowości](https://github.com/microsoft/FHIR-Tools-for-Anonymization): zestaw narzędzi do pomocy z danymi (w formacie FHIR) zachowywanie anonimowości
* Integracja z interfejsem API platformy Azure dla usługi FHIR oraz serwera FHIR dla platformy Azure w postaci "de-zidentyfikowany Export"

#### <a name="fhir-tools-for-anonymization"></a>Narzędzia FHIR dla zachowywanie anonimowości
* [Microsoft/programu vscode-azurehealthcareapis-Tools](https://github.com/microsoft/vscode-azurehealthcareapis-tools): rozszerzenie vs Code zawierające kolekcję narzędzi do pracy z interfejsami API usługi Azure opieki zdrowotnej
* Wydane do Visual Studio Marketplace
* Służy do tworzenia szablonów płynnych, które mają być używane w konwerterze FHIR

## <a name="iot-connector"></a>Łącznik IoT

#### <a name="integration-with-iot-hub-and-iot-central"></a>Integracja z IoT Hub i IoT Central
* [Microsoft/iomt-fhir](https://github.com/microsoft/iomt-fhir): integracja z IoT Hub lub IoT Central do fhir przy użyciu normalizacji danych i konwersji fhir znormalizowanych danych
* Normalizacja: informacje o danych urządzenia są wyodrębniane do wspólnego formatu w celu dalszej obróbki
* Konwersja FHIR: znormalizowane i zgrupowane dane są mapowane na FHIR. Obserwacje są tworzone lub aktualizowane zgodnie ze skonfigurowanymi szablonami i połączone z urządzeniem i pacjentem.
* [Narzędzia pomagające w tworzeniu mapy konwersacji](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper): Wizualizuj konfigurację mapowania w celu normalizacji danych wejściowych urządzenia i Przekształć ją w zasoby FHIR. Za pomocą tego narzędzia deweloperzy mogą edytować i testować mapowania, mapowanie urządzeń i mapowanie FHIR oraz eksportować je w celu przekazywania do łącznika IoT w Azure Portal.

#### <a name="healthkit-and-fhir-integration"></a>Integracja z HealthKit i FHIR
* [Microsoft/HealthKit-on-fhir](https://github.com/microsoft/healthkit-on-fhir): Biblioteka SWIFT, która automatyzuje eksportowanie danych Apple HealthKit do serwera fhir

 