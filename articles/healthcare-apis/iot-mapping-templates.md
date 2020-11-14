---
title: 'Koncepcje: mapowanie szablonów w usłudze Azure IoT Connector for FHIR (wersja zapoznawcza) funkcja interfejsu API platformy Azure dla FHIR'
description: Dowiedz się, jak utworzyć dwa typy szablonów mapowania w usłudze Azure IoT Connector for FHIR (wersja zapoznawcza). Szablon mapowania urządzeń przekształca dane urządzenia w znormalizowany schemat. Szablon mapowania FHIR przekształca znormalizowany komunikat do ego zasobu na podstawie FHIR.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: punagpal
ms.openlocfilehash: 63484361a6d5a331fd9dc646c53627918ce8b246
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630553"
---
# <a name="azure-iot-connector-for-fhir-preview-mapping-templates"></a>Szablony mapowania łącznika usługi Azure IoT dla platformy FHIR (wersja zapoznawcza)
W tym artykule szczegółowo opisano sposób konfigurowania łącznika usługi Azure IoT na potrzeby szybkich zasobów współdziałania w usłudze opieki zdrowotnej (FHIR&#174;) * przy użyciu szablonów

Łącznik usługi Azure IoT dla FHIR wymaga dwóch typów szablonów mapowania opartych na notacji JSON. Pierwszy typ, **Mapowanie urządzenia** , jest odpowiedzialny za mapowanie ładunków urządzeń wysyłanych do `devicedata` punktu końcowego centrum zdarzeń platformy Azure. Wyodrębnia typy, identyfikatory urządzeń, Data i czas pomiaru oraz wartości miary. Drugim typem, **Mapowanie FHIR** , steruje mapowaniem zasobu FHIR. Umożliwia ona konfigurację długości okresu obserwacji, FHIR typ danych służący do przechowywania wartości oraz kodów terminologii. 

Szablony mapowania składają się na dokument JSON w oparciu o ich typ. Te dokumenty JSON są następnie dodawane do łącznika usługi Azure IoT dla FHIR za pomocą Azure Portal. Dokument mapowania urządzeń zostanie dodany za pomocą strony **Konfigurowanie mapowania urządzenia** i dokumentu mapowania FHIR na stronie **Konfigurowanie mapowania FHIR** .

> [!NOTE]
> Szablony mapowania są przechowywane w źródłowym magazynie obiektów blob i ładowane z obiektów BLOB na wykonanie obliczeń. Po zaktualizowaniu powinny one obowiązywać od razu. 

## <a name="device-mapping"></a>Mapowanie urządzeń
Mapowanie urządzeń udostępnia funkcje mapowania umożliwiające Wyodrębnienie zawartości urządzenia do wspólnego formatu w celu dalszej oceny. Każdy odebrany komunikat jest oceniany dla wszystkich szablonów. Takie podejście umożliwia projekcję pojedynczego komunikatu przychodzącego w wielu komunikatach wychodzących, które są później mapowane na różne obserwacje w FHIR. Wynikiem jest znormalizowany obiekt danych reprezentujący wartość lub wartości analizowane przez szablony. Znormalizowany model danych ma kilka wymaganych właściwości, które muszą zostać odnalezione i wyodrębnione:

| Właściwość | Opis |
| - | - |
|**Typ**|Nazwa/Typ do klasyfikowania miary. Ta wartość jest używana do powiązania z wymaganym szablonem mapowania FHIR.  Wiele szablonów można wyprowadzić do tego samego typu, co pozwala na mapowanie różnych reprezentacji na wiele urządzeń do jednego wspólnego danych wyjściowych.|
|**OccurenceTimeUtc**|Czas wystąpienia pomiaru.|
|**Identyfikator**|Identyfikator urządzenia. Ta wartość powinna być zgodna z identyfikatorem zasobu urządzenia, który istnieje na serwerze docelowym FHIR.|
 |**Właściwości**|Wyodrębnij co najmniej jedną właściwość, aby można było zapisać wartość w tworzonym zasobie.  Właściwości są kolekcją par wartości klucza wyodrębnionych podczas normalizacji.|

Poniżej znajduje się przykład koncepcji, co dzieje się podczas normalizacji.

![Przykład normalizacji](media/concepts-iot-mapping-templates/normalization-example.png)

Sam ładunek zawartości jest komunikatem centrum zdarzeń platformy Azure, który składa się z trzech części: Body, Properties i SystemProperties. `Body`Jest tablicą bajtów reprezentującą ciąg zakodowany w formacie UTF-8. Podczas obliczania szablonu tablica bajtów jest automatycznie konwertowana na wartość ciągu. `Properties` jest kolekcją wartości klucza do użycia przez twórcę wiadomości. `SystemProperties` jest również kolekcją wartości klucza zarezerwowaną przez platformę centrum zdarzeń platformy Azure z wpisami, które są automatycznie wypełniane przez program.

```json
{
    "Body": {
        "content": "value"
    },
    "Properties": {
        "key1": "value1",
        "key2": "value2"
    },
    "SystemProperties": {
        "x-opt-sequence-number": 1,
        "x-opt-enqueued-time": "2019-02-01T22:46:01.8750000Z",
        "x-opt-offset": 1,
        "x-opt-partition-key": "1"
    }
}
```

### <a name="mapping-with-json-path"></a>Mapowanie przy użyciu ścieżki JSON
Dwa typy szablonów zawartości urządzeń obsługiwane dzisiaj polegają na ścieżce JSON do obu elementów zgodnych z wymaganym szablonem i wyodrębnionymi wartościami. Więcej informacji na temat ścieżki JSON można znaleźć [tutaj](https://goessner.net/articles/JsonPath/). Oba typy szablonów używają implementacji w formacie [JSON platformy .NET](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm) do rozpoznawania wyrażeń ścieżki JSON.

#### <a name="jsonpathcontenttemplate"></a>JsonPathContentTemplate
JsonPathContentTemplate umożliwia dopasowanie i wyodrębnianie wartości z komunikatu centrum zdarzeń przy użyciu ścieżki JSON.

| Właściwość | Opis |<div style="width:150px">Przykład</div>
| --- | --- | --- 
|**Nazwa**|Typ do skojarzenia z pomiarami, które pasują do szablonu.|`heartrate`
|**TypeMatchExpression**|Wyrażenie ścieżki JSON, które jest oceniane względem ładunku centrum zdarzeń. Jeśli zostanie znaleziony pasujący JToken, szablon jest uznawany za dopasowanie. Wszystkie kolejne wyrażenia są oceniane względem wyodrębnionego JToken dopasowanego tutaj.|`$..[?(@heartRate)]`
|**TimestampExpression**|Wyrażenie ścieżki JSON wyodrębniające wartość sygnatury czasowej OccurenceTimeUtc pomiaru.|`$.endDate`
|**DeviceIdExpression**|Wyrażenie ścieżki JSON umożliwiające wyodrębnienie identyfikatora urządzenia.|`$.deviceId`
|**PatientIdExpression**|*Opcjonalnie* : wyrażenie ścieżki JSON do wyodrębnienia identyfikatora pacjenta.|`$.patientId`
|**EncounterIdExpression**|*Opcjonalnie* : wyrażenie ścieżki JSON do wyodrębnienia napotkanego identyfikatora.|`$.encounterId`
|**Wartości []. Pełna**|Nazwa, która ma zostać skojarzona z wartością wyodrębnioną przez kolejne wyrażenie. Służy do powiązania wymaganej wartości/składnika w szablonie mapowania FHIR. |`hr`
|**Wartości []. ValueExpression**|Wyrażenie ścieżki JSON umożliwiające wyodrębnienie wymaganej wartości.|`$.heartRate`
|**Wartości []. Wymagane**|Wartość musi być obecna w ładunku.  Jeśli nie zostanie znaleziona, pomiar nie zostanie wygenerowany i zostanie wygenerowany InvalidOperationException.|`true`

##### <a name="examples"></a>Przykłady
---
**Częstotliwość serca**

*Wiadomość*
```json
{
    "Body": {
        "heartRate": "78",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*Szablon*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
---
**Nacisk na krew**

*Wiadomość*
```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*Szablon*
```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@systolic && @diastolic)]",
    "deviceIdExpression": "$.deviceid",
    "timestampExpression": "$.endDate",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```
---

**Przeprojektowanie wielu pomiarów z pojedynczego komunikatu**

*Wiadomość*
```json
{
    "Body": {
        "heartRate": "78",
        "steps": "2",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*Szablon 1*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
*Szablon 2*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "stepcount",
        "typeMatchExpression": "$..[?(@steps)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.steps",
                "valueName": "steps"
            }
        ]
    }
}
```
---

**Zaprojektuj wiele pomiarów z tablicy w komunikacie**

*Wiadomość*
```json
{
    "Body": [
        {
            "heartRate": "78",
            "endDate": "2019-02-01T22:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "81",
            "endDate": "2019-02-01T23:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "72",
            "endDate": "2019-02-01T24:46:01.8750000Z",
            "deviceId": "device123"
        }
    ],
    "Properties": {},
    "SystemProperties": {}
}
```
*Szablon*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
#### <a name="iotjsonpathcontenttemplate"></a>IotJsonPathContentTemplate
IotJsonPathContentTemplate jest podobna do JsonPathContentTemplate, z wyjątkiem DeviceIdExpression i TimestampExpression nie są wymagane.

Założenie, że podczas korzystania z tego szablonu komunikaty są oceniane, są wysyłane przy użyciu [zestawów SDK urządzeń IoT Hub platformy Azure](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks). W przypadku korzystania z tych zestawów SDK tożsamość urządzenia (przy założeniu, że identyfikator urządzenia z usługi Azure IoT Hub/Centrala) jest zarejestrowana jako identyfikator dla zasobu urządzenia na serwerze docelowym FHIR, a sygnatura czasowa komunikatu jest znana. Jeśli używasz zestawów SDK urządzeń z platformą Azure IoT Hub, ale używamy właściwości niestandardowych w treści komunikatu dla tożsamości urządzenia lub sygnatury czasowej, możesz nadal korzystać z JsonPathContentTemplate.

*Uwaga: w przypadku korzystania z IotJsonPathContentTemplate, TypeMatchExpression powinien rozwiązać cały komunikat jako JToken. Zapoznaj się z poniższymi przykładami.* 
##### <a name="examples"></a>Przykłady
---
**Częstotliwość serca**

*Wiadomość*
```json
{
    "Body": {
        "heartRate": "78"        
    },
    "Properties": {
        "iothub-creation-time-utc" : "2019-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```
*Szablon*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.Body.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
---
**Nacisk na krew**

*Wiadomość*
```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87"
    },
    "Properties": {
        "iothub-creation-time-utc" : "2019-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```
*Szablon*
```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@Body.systolic && @Body.diastolic)]",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.Body.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.Body.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```

## <a name="fhir-mapping"></a>Mapowanie FHIR
Po wyodrębnieniu zawartości urządzenia do znormalizowanego modelu dane są zbierane i grupowane według identyfikatora urządzenia, typu pomiaru i przedziału czasu. Dane wyjściowe tego grupowania są wysyłane do konwersji na zasób FHIR (obecnie[obserwacja](https://www.hl7.org/fhir/observation.html) ). W tym miejscu szablon mapowania FHIR kontroluje, jak dane są mapowane na obserwację FHIR. Czy należy utworzyć obserwację dla punktu w czasie lub w ciągu godziny? Jakie kody należy dodać do obserwacji? Czy wartość powinna być reprezentowana jako [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) czy [ilość](https://www.hl7.org/fhir/datatypes.html#Quantity)? Te typy danych to wszystkie opcje FHIR mapowania konfiguracji.

### <a name="codevaluefhirtemplate"></a>CodeValueFhirTemplate
CodeValueFhirTemplate jest obecnie jedynym szablonem obsługiwanym w mapowaniu FHIR.  Umożliwia definiowanie kodów, obowiązujących okresów i wartości obserwacji. Obsługiwane są wiele typów wartości: [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData), [CodeableConcept](https://www.hl7.org/fhir/datatypes.html#CodeableConcept)i [ilooć](https://www.hl7.org/fhir/datatypes.html#Quantity). Wraz z tymi konfigurowalnymi wartościami identyfikator dla obserwacyjnego zasobu oraz połączenie z odpowiednim urządzeniem i zasobami pacjenta są obsługiwane automatycznie.

| Właściwość | Opis 
| --- | ---
|**Nazwa**| Typ miary, z którą ten szablon powinien powiązać. Powinien istnieć co najmniej jeden szablon mapowania urządzeń, który wyprowadza ten typ.
|**PeriodInterval**|Okres, jaki powinien być reprezentowany przez utworzone obserwacje. Obsługiwane wartości to 0 (wystąpienie), 60 (godzina), 1440 (dzień).
|**Kategoria**|Dowolna liczba [CodeableConcepts](http://hl7.org/fhir/datatypes-definitions.html#codeableconcept) do klasyfikowania typu tworzonego obserwacji.
|**Kody**|Co najmniej jeden [kod](http://hl7.org/fhir/datatypes-definitions.html#coding) , który ma zostać zastosowany do tworzonego obserwacji.
|**Kody []. Kodu**|Kod [kodowania](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Kody []. Systemami**|System do [kodowania](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Kody []. Wyświetlany**|Sposób wyświetlania [kodowania](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Wartość**|Wartość do wyodrębnienia i reprezentowania w obserwacji. Aby uzyskać więcej informacji, zobacz [Szablony typów wartości](#valuetypes).
|**Składniki**|*Opcjonalne:* Co najmniej jeden składnik do utworzenia na obserwacje.
|**Składniki []. Kody**|Co najmniej jeden [kod](http://hl7.org/fhir/datatypes-definitions.html#coding) , który ma zostać zastosowany do składnika.
|**Składniki []. Wartościami**|Wartość do wyodrębnienia i reprezentowania w składniku. Aby uzyskać więcej informacji, zobacz [Szablony typów wartości](#valuetypes).

### <a name="value-type-templates"></a>Szablony typów wartości <a name="valuetypes"></a>
Poniżej znajdują się obecnie obsługiwane szablony typów wartości. W przyszłości można dodać kolejne szablony.
#### <a name="sampleddata"></a>SampledData
Reprezentuje typ danych [SampledData](http://hl7.org/fhir/datatypes.html#SampledData) FHIR. Pomiary obserwacyjne są zapisywane w strumieniu wartości, rozpoczynając od punktu w czasie i zwiększają się w przód przy użyciu zdefiniowanego okresu. Jeśli wartość nie jest obecna, `E` nastąpi zapis do strumienia danych. Jeśli okres jest taki, że dwie więcej wartości zajmują tę samą pozycję w strumieniu danych, używana jest najnowsza wartość. Ta sama logika jest stosowana podczas aktualizowania obserwacji przy użyciu SampledData.

| Właściwość | Opis 
| --- | ---
|**DefaultPeriod**|Domyślny okres w milisekundach do użycia. 
|**Jednostka**|Jednostka, która ma zostać ustawiona w pochodzeniu SampledData. 

#### <a name="quantity"></a>Ilość
Przedstawia typ danych FHIR [ilość](http://hl7.org/fhir/datatypes.html#Quantity) . Jeśli w grupowaniu występuje więcej niż jedna wartość, zostanie użyta tylko pierwsza wartość. Po nadejściu nowej wartości, która mapuje na tę samą obserwację, spowoduje zastąpienie starej wartości.

| Właściwość | Opis 
| --- | --- 
|**Jednostka**| Reprezentacja jednostki.
|**Kod**| Zakodowana forma jednostki.
|**System**| System, który definiuje zakodowany formularz jednostki.

### <a name="codeableconcept"></a>CodeableConcept
Reprezentuje typ danych [CodeableConcept](http://hl7.org/fhir/datatypes.html#CodeableConcept) FHIR. Rzeczywista wartość nie jest używana.

| Właściwość | Opis 
| --- | --- 
|**Tekst**|Reprezentacja zwykłego tekstu. 
|**Kody**|Co najmniej jeden [kod](http://hl7.org/fhir/datatypes-definitions.html#coding) , który ma zostać zastosowany do tworzonego obserwacji.
|**Kody []. Kodu**|Kod [kodowania](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Kody []. Systemami**|System do [kodowania](http://hl7.org/fhir/datatypes-definitions.html#coding).
|**Kody []. Wyświetlany**|Sposób wyświetlania [kodowania](http://hl7.org/fhir/datatypes-definitions.html#coding).

### <a name="examples"></a>Przykłady
**Częstotliwość kierów — SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "8867-4",
                "system": "http://loinc.org",
                "display": "Heart rate"
            }
        ],
        "periodInterval": 60,
        "typeName": "heartrate",
        "value": {
            "defaultPeriod": 5000,
            "unit": "count/min",
            "valueName": "hr",
            "valueType": "SampledData"
        }
    }
}
```
---
**Kroki — SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "55423-8",
                "system": "http://loinc.org",
                "display": "Number of steps"
            }
        ],        
        "periodInterval": 60,
        "typeName": "stepsCount",
        "value": {
            "defaultPeriod": 5000,
            "unit": "",
            "valueName": "steps",
            "valueType": "SampledData"
        }
    }
}
```
---
**Nacisk na krew — SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 60,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "SampledData"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "SampledData"
                }
            }
        ]
    }
}
```
---
**Nacisk na krewną ilość**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 0,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "Quantity"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "Quantity"
                }
            }
        ]
    }
}
```
---
**Usunięto urządzenie — CodeableConcept**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "deviceEvent",
                "system": "https://www.mydevice.com/v1",
                "display": "Device Event"
            }
        ],
        "periodInterval": 0,
        "typeName": "deviceRemoved",
        "value": {
            "text": "Device Removed",
            "codes": [
                {
                    "code": "deviceRemoved",
                    "system": "https://www.mydevice.com/v1",
                    "display": "Device Removed"
                }
            ],
            "valueName": "deviceRemoved",
            "valueType": "CodeableConcept"
        }
    }
}
```
---

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z często zadawanymi pytaniami dotyczącymi usługi Azure IoT Connector FHIR (wersja zapoznawcza).

>[!div class="nextstepaction"]
>[Łącznik usługi Azure IoT dla FHIR — często zadawane pytania](fhir-faq.md)

* W Azure Portal łącznik usługi Azure IoT dla FHIR jest określany jako łącznik IoT (wersja zapoznawcza). FHIR jest zastrzeżonym znakiem towarowym HL7 i jest używany z uprawnieniem HL7.