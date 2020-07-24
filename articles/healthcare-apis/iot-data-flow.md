---
title: 'Pojęcia: przepływ danych w usłudze IoT Connector (wersja zapoznawcza) interfejsu API platformy Azure dla usługi FHIR'
description: Opis przepływu danych łącznika IoT. Usługa IoT Connector pozyskuje, normalizuje, grupuje, przekształca i utrzymuje dane IoMT w interfejsie API platformy Azure dla FHIR.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: punagpal
ms.openlocfilehash: c2d150fcd35bc51478a1d7f4a0407abce1446c06
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87099159"
---
# <a name="iot-connector-preview-data-flow"></a>Przepływ danych łącznika IoT (wersja zapoznawcza)

Ten artykuł zawiera omówienie przepływu danych w łączniku IoT. Dowiesz się [więcej na temat](https://www.hl7.org/fhir/observation.html) różnych etapów przetwarzania danych w ramach łącznika IoT, które przekształcają dane urządzeń w FHIRe zasoby.

![Przepływ danych łącznika IoT](media/concepts-iot-data-flow/iot-connector-data-flow.png)

Na powyższym diagramie przedstawiono różne etapy przepływu danych w ramach łącznika IoT. 

## <a name="ingest"></a>Pozyskiwanie
Pozyskiwanie to pierwszy etap, w którym dane urządzenia są odbierane do łącznika IoT. Punkt końcowy pozyskiwania danych urządzenia jest hostowany w [centrum zdarzeń platformy Azure](https://docs.microsoft.com/azure/event-hubs/). Platforma Azure Event Hub obsługuje wysoką skalowalność i przepływność z możliwością odbierania i przetwarzania milionów komunikatów na sekundę. Umożliwia ona również łącznikowi IoT używanie komunikatów asynchronicznie, co eliminuje konieczność, aby urządzenia czekały na przetwarzanie danych na urządzeniu.

> [!NOTE]
> W tym momencie jedynym obsługiwanym formatem jest format JSON dla danych urządzeń.

## <a name="normalize"></a>Normalizuj
Normalizacja to kolejny etap, w którym dane urządzenia są pobierane z powyższego centrum zdarzeń platformy Azure i przetwarzane przy użyciu szablonów mapowania urządzeń. Ten proces mapowania powoduje transformowanie danych urządzenia w znormalizowanym schemacie. 

Proces normalizacji nie tylko upraszcza przetwarzanie danych na późniejszym etapie, ale również zapewnia możliwość tworzenia projektu jednego komunikatu wejściowego w wielu znormalizowanych wiadomościach. Na przykład urządzenie może wysyłać wiele nieistotnych oznak dla temperatury ciała, współczynnika pulsu, naciskania krwi oraz stawki za ponowne oddychanie w pojedynczym komunikacie. Ten komunikat wejściowy utworzy cztery osobne zasoby FHIR. Każdy zasób reprezentuje inny klucz istotny, a komunikat wejściowy jest rzutowany na cztery różne znormalizowane komunikaty.

## <a name="group"></a>Grupa
Grupa jest następnym etapem, w którym znormalizowane wiadomości dostępne z poprzedniego etapu są grupowane przy użyciu trzech różnych parametrów: tożsamości urządzenia, typu pomiaru i okresu.

Grupowanie tożsamości urządzeń i typów pomiarów umożliwia użycie typu pomiaru [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) . Ten typ zapewnia zwięzły sposób przedstawiania serii pomiarów z urządzenia w FHIR. I okres określa czas, w którym obserwacje zasobów generowanych przez łącznik IoT Connector są zapisywane w interfejsie API platformy Azure dla FHIR.

> [!NOTE]
> Wartość przedziału czasu jest domyślnie równa 15 minut i nie można jej skonfigurować do korzystania z wersji zapoznawczej.

## <a name="transform"></a>Przekształcanie
Na etapie transformacji pogrupowane komunikaty są przetwarzane za pomocą szablonów mapowania FHIR. Komunikaty pasujące do typu szablonu są przekształcane na FHIR, które są określone za pośrednictwem mapowania.

W tym momencie zasób [urządzenia](https://www.hl7.org/fhir/device.html) wraz z jego skojarzonym zasobem [pacjenta](https://www.hl7.org/fhir/patient.html) jest również pobierany z serwera FHIR przy użyciu identyfikatora urządzenia znajdującego się w komunikacie. Te zasoby są dodawane jako odniesienia do tworzonego zasobu.

> [!NOTE]
> Wszystkie wyszukiwane tożsamości są buforowane po rozwiązaniu, aby zmniejszyć obciążenie serwera FHIR. Jeśli planujesz ponowne użycie urządzeń z wieloma pacjentami, zaleca się utworzenie zasobu urządzenia wirtualnego, który jest specyficzny dla pacjenta i wysłania identyfikatora urządzenia wirtualnego w ładunku wiadomości. Urządzenie wirtualne można połączyć z rzeczywistym zasobem urządzenia jako nadrzędnym.

Jeśli żaden zasób urządzenia dla danego identyfikatora urządzenia nie istnieje na serwerze FHIR, wynik zależy od wartości `Resolution Type` ustawionej w momencie tworzenia. Po ustawieniu na wartość `Lookup` określona wiadomość zostanie zignorowana, a potok będzie kontynuował przetwarzanie innych komunikatów przychodzących. Jeśli ta wartość `Create` jest ustawiona na, łącznik IoT utworzy urządzenie bez systemu operacyjnego i zasoby pacjenta na serwerze FHIR.  

## <a name="persist"></a>Persist
Po wygenerowaniu FHIR obserwacji w fazie przekształcania zasób jest zapisywany w interfejsie API platformy Azure dla FHIR. Jeśli zasób FHIR jest nowy, zostanie on utworzony na serwerze. Jeśli zasób FHIR już istnieje, zostanie zaktualizowany.

## <a name="next-steps"></a>Następne kroki

Kliknij poniżej następny krok, aby dowiedzieć się, jak utworzyć szablony mapowania urządzeń i FHIR.

>[!div class="nextstepaction"]
>[Szablony mapowania łącznika IoT](iot-mapping-templates.md)


FHIR to zastrzeżony znak towarowy firmy HL7 i jest używany za jej pozwoleniem.
