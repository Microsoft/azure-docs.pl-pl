---
title: Wykonywanie operacji eksportowania przez wywoływanie $export polecenia w interfejsie API platformy Azure dla FHIR
description: W tym artykule opisano sposób eksportowania danych FHIR przy użyciu $export
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 1/21/2021
ms.author: cavoeg
ms.openlocfilehash: 8ad5ee78a525b3798bbf613168ff74a9e21fe99b
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920261"
---
# <a name="how-to-export-fhir-data"></a>Jak wyeksportować dane FHIR


Funkcja eksportu zbiorczego umożliwia eksportowanie danych z serwera FHIR na [specyfikację FHIR](https://hl7.org/fhir/uv/bulkdata/export/index.html). 

Przed rozpoczęciem korzystania z $export upewnij się, że jest skonfigurowany do korzystania z interfejsu API platformy Azure dla usługi FHIR. Aby skonfigurować ustawienia eksportu i utworzyć konto usługi Azure Storage, zapoznaj się z [stroną Konfigurowanie eksportu danych](configure-export-data.md).

## <a name="using-export-command"></a>Używanie $export polecenia

Po skonfigurowaniu interfejsu API platformy Azure dla usługi FHIR na potrzeby eksportu można użyć polecenia $export, aby wyeksportować dane z usługi. Dane będą przechowywane na koncie magazynu określonym podczas konfigurowania eksportu. Aby dowiedzieć się, jak wywołać $export polecenie na serwerze FHIR, Przeczytaj dokumentację dotyczącą [specyfikacji HL7 FHIR $Export](https://hl7.org/Fhir/uv/bulkdata/export/index.html). 

Interfejs API platformy Azure dla usługi FHIR obsługuje $export na następujących poziomach:
* [System](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---system-level-export): `GET https://<<FHIR service base URL>>/$export>>`
* [Pacjent](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---all-patients): `GET https://<<FHIR service base URL>>/Patient/$export>>`
* [Grupa pacjentów *](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---group-of-patients) — interfejs API platformy Azure dla FHIR eksportuje wszystkie powiązane zasoby, ale nie eksportuje cech grupy: `GET https://<<FHIR service base URL>>/Group/[ID]/$export>>`



> [!Note] 
> `Patient/$export` i `Group/[ID]/$export` mogą eksportować duplikaty zasobów, jeśli zasób znajduje się w przedziale więcej niż jednego zasobu lub znajduje się w wielu grupach.

Ponadto sprawdzanie stanu eksportu za pośrednictwem adresu URL zwróconego przez nagłówek lokalizacji podczas kolejkowania jest obsługiwane wraz z anulowaniem rzeczywistego zadania eksportu.

## <a name="settings-and-parameters"></a>Ustawienia i parametry

### <a name="headers"></a>Nagłówki
Istnieją dwa wymagane parametry nagłówka, które muszą zostać ustawione dla $export zadań. Wartości są definiowane przez bieżącą [specyfikację $Export](https://hl7.org/Fhir/uv/bulkdata/export/index.html#headers).
* **Accept** -Application/fhir + JSON
* **Preferuj** — odpowiedź — Async

### <a name="query-parameters"></a>Parametry zapytania
Interfejs API platformy Azure dla usługi FHIR obsługuje następujące parametry zapytania. Wszystkie te parametry są opcjonalne:

|Parametr zapytania        | Zdefiniowano w specyfikacji FHIR?    |  Opis|
|------------------------|---|------------|
| \_outputFormat | Tak | Obecnie program obsługuje trzy wartości do dopasowania do specyfikacji FHIR: Application/FHIR + ndjson, Application/ndjson lub tylko ndjson. Wszystkie zadania eksportu zostaną zwrócone `ndjson` i przeniesiona wartość nie ma wpływu na zachowanie kodu. |
| \_Fire | Tak | Umożliwia eksportowanie tylko zasobów zmodyfikowanych od czasu podanego |
| \_Wprowadź | Tak | Pozwala określić, które typy zasobów zostaną uwzględnione. Na przykład \_ wpisz = pacjent zwróci tylko zasoby pacjenta|
| \_typefilter | Tak | Aby zażądać bardziej szczegółowego filtrowania, można użyć \_ TypeFilter wraz z \_ parametrem typu. Wartość parametru _typeFilter jest rozdzielaną przecinkami listą zapytań FHIR, które bardziej ograniczają wyniki |
| \_wbudowane | Nie |  Określa kontener w skonfigurowanym koncie magazynu, w którym mają zostać wyeksportowane dane. Jeśli kontener jest określony, dane zostaną wyeksportowane do tego kontenera w nowym folderze o nazwie. Jeśli kontener nie zostanie określony, zostanie on wyeksportowany do nowego kontenera przy użyciu sygnatury czasowej i identyfikatora zadania. |


## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób eksportowania FHIR zasobów przy użyciu polecenia $export. Następnie Dowiedz się, jak eksportować dane z wyznaczonymi danymi:
 
>[!div class="nextstepaction"]
>[Eksportowanie wyznaczonych danych](de-identified-export.md)
