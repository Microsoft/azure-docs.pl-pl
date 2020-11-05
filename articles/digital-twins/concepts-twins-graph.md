---
title: Bliźniacze reprezentacje cyfrowe i graf bliźniaczych reprezentacji
titleSuffix: Azure Digital Twins
description: Zrozumienie koncepcji wieloosiowej i sposobu tworzenia grafu przez ich relacje.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: a1fc5be93e2b9729838aa9fb3a777936003c5f45
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93356405"
---
# <a name="understand-digital-twins-and-their-twin-graph"></a>Informacje na temat bliźniaczych reprezentacji cyfrowych i ich bliźniaczych wykresów

W rozwiązaniu Digital bliźniaczych reprezentacji na platformie Azure jednostki w Twoim środowisku są reprezentowane przez usługę Azure **Digital bliźniaczych reprezentacji**. Cyfrowa dwuosiowa to wystąpienie jednego z [modeli](concepts-models.md)zdefiniowanych przez użytkownika. Może być połączony z innym cyfrowym bliźniaczych reprezentacji poprzez **relacje** , aby utworzyć **Wykres dwuosiowy** : ten wykres dwuosiowy jest reprezentacją całego środowiska.

> [!TIP]
> "Usługa Azure Digital bliźniaczych reprezentacji" odnosi się do tej usługi platformy Azure jako całości. "Pojedyncze sznury cyfrowe" lub "same sznury" odnoszą się do pojedynczych węzłów bliźniaczych wewnątrz Twojego wystąpienia usługi.

## <a name="digital-twins"></a>Digital Twins

Aby można było utworzyć dwuosiową cyfrę w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji, musisz mieć *model* przekazany do usługi. Model opisuje zestaw właściwości, wiadomości telemetrycznych i relacje, które mogą mieć poszczególne sznurki, między innymi. Aby uzyskać informacje o typach informacji, które są zdefiniowane w modelu, zobacz [*pojęcia: modele niestandardowe*](concepts-models.md).

Po utworzeniu i przekazaniu modelu aplikacja kliencka może utworzyć wystąpienie typu; to jest dwuosiowa cyfra. Na przykład po utworzeniu modelu *podłogi* można utworzyć jedną lub kilka bliźniaczych reprezentacji cyfrowych, które używają tego typu (takich jak sznury typu *podłogi* o nazwie *GroundFloor* , inne o nazwie *Floor2* itp.). 

## <a name="relationships-a-graph-of-digital-twins"></a>Relacje: wykres Digital bliźniaczych reprezentacji

Bliźniaczych reprezentacji są połączone z wykresem bliźniaczym przez ich relacje. Relacje, które mogą mieć sznurek, są zdefiniowane jako część jego modelu.  

Na przykład model *podłogi* może definiować relację *zawierania* , która jest przeznaczona dla bliźniaczych reprezentacji typu *pokoju*. W przypadku tej definicji usługa Azure Digital bliźniaczych reprezentacji umożliwia tworzenie relacji między *dowolnym* *podłogą piętra* a dowolnym przędzą za *Pokój* (w tym bliźniaczych reprezentacji, *Room* które są podtypem). 

Wynikiem tego procesu jest zestaw węzłów (Digital bliźniaczych reprezentacji) połączony za pośrednictwem krawędzi (ich relacji) w grafie.

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-with-the-apis"></a>Tworzenie za pomocą interfejsów API

W tej sekcji pokazano, jak wygląda sposób tworzenia cyfrowych bliźniaczych reprezentacji i relacji z aplikacji klienckiej. Zawiera przykłady kodu platformy .NET, które wykorzystują [interfejsy API DigitalTwins](/rest/api/digital-twins/dataplane/twins), aby zapewnić dodatkowy kontekst dotyczący tego, co się znajduje w ramach każdego z tych koncepcji.

### <a name="create-digital-twins"></a>Utwórz cyfrowy bliźniaczych reprezentacji

Poniżej znajduje się fragment kodu klienta, który używa [interfejsów API DigitalTwins](/rest/api/digital-twins/dataplane/twins) do tworzenia wycinków typu *pokoju*.

Możesz inicjować właściwości sznurka podczas jego tworzenia lub ustawiać je później. Aby utworzyć sznurek z zainicjowanymi właściwościami, Utwórz dokument JSON, który zawiera wymagane wartości inicjalizacji.

[!INCLUDE [Azure Digital Twins code: create twin](../../includes/digital-twins-code-create-twin.md)]

Można również użyć klasy pomocnika `BasicDigitalTwin` do przechowywania pól właściwości w obiekcie "bliźniaczy" więcej bezpośrednio, jako alternatywy dla użycia słownika. Aby uzyskać więcej informacji na temat klasy pomocnika i przykłady ich użycia, zobacz sekcję [*Tworzenie cyfrowej sieci*](how-to-manage-twin.md#create-a-digital-twin) z informacjami o tym, *jak: Manage Digital bliźniaczych reprezentacji*.

>[!NOTE]
>Gdy właściwości dwuosiowe są traktowane jako opcjonalne i nie trzeba ich inicjować, wszelkie [składniki](concepts-models.md#elements-of-a-model) **na przędze** muszą być ustawione podczas tworzenia sznurka. Mogą być pustymi obiektami, ale same składniki muszą istnieć.

### <a name="create-relationships"></a>Utwórz relacje

Poniżej przedstawiono przykładowy kod klienta korzystający z [interfejsów API DigitalTwins](/rest/api/digital-twins/dataplane/twins) do tworzenia relacji między cyframi cyfrowymi typu *podłogi* o nazwie *GroundFloor* i sznurem cyfrowym typu *Room* o nazwie *Cafe*.

```csharp
// Create Twins, using functions similar to the previous sample
await CreateRoom("Cafe", 70, 66);
await CreateFloor("GroundFloor", averageTemperature=70);
// Create relationships
var relationship = new BasicRelationship
{
    TargetId = "Cafe",
    Name = "contains"
};
try
{
    string relId = $"GroundFloor-contains-Cafe";
    await client.CreateOrReplaceRelationshipAsync<BasicRelationship>("GroundFloor", relId, relationship);
} catch(ErrorResponseException e)
{
    Console.WriteLine($"*** Error creating relationship: {e.Response.StatusCode}");
}
```

## <a name="json-representations-of-graph-elements"></a>Reprezentacje JSON elementów grafu

Cyfrowe dane dwuosiowe i dane relacji są przechowywane w formacie JSON. Oznacza to, że po wykonaniu [zapytania dotyczącego grafu bliźniaczyego](how-to-query-graph.md) w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji, wynik będzie reprezentacją JSON bliźniaczych reprezentacji i utworzonych relacji.

### <a name="digital-twin-json-format"></a>Format JSON cyfrowego przędzy

Gdy jest reprezentowany jako obiekt JSON, dwuosiowy symbol cyfrowy będzie wyświetlał następujące pola:

| Nazwa pola | Opis |
| --- | --- |
| `$dtId` | Ciąg dostarczony przez użytkownika reprezentujący identyfikator dwuosiowego |
| `$etag` | Standardowe pole HTTP przypisane przez serwer sieci Web |
| `$conformance` | Wyliczenie zawierające stan zgodności tej dwucyfrowej sznurka *(niezgodne,* *niezgodne* , *nieznany* ) |
| `{propertyName}` | Wartość właściwości w formacie JSON ( `string` , typ numeru lub obiekt) |
| `$relationships` | Adres URL ścieżki do kolekcji Relationships. To pole jest nieobecne, jeśli cyfrowa dwuosiowa nie ma żadnych wychodzących krawędzi relacji. |
| `$metadata.$model` | Obowiązkowe Identyfikator interfejsu modelu, który charakteryzuje ten dwucyfrowego przędzy |
| `$metadata.{propertyName}.desiredValue` | [Tylko do zapisu właściwości] Wymagana wartość określonej właściwości |
| `$metadata.{propertyName}.desiredVersion` | [Tylko do zapisu właściwości] Wersja żądanej wartości |
| `$metadata.{propertyName}.ackVersion` | Wersja potwierdzona przez aplikację urządzenia implementującą dwuosiową cyfrę |
| `$metadata.{propertyName}.ackCode` | [Tylko do zapisu właściwości] `ack` Kod zwrócony przez aplikację urządzenia implementującą dwuosiową cyfrę |
| `$metadata.{propertyName}.ackDescription` | [Tylko do zapisu właściwości] `ack` Opis zwrócony przez aplikację urządzenia implementującą dwuosiową cyfrę |
| `{componentName}` | Obiekt JSON zawierający wartości właściwości i metadanych składnika, podobnie jak w przypadku obiektu głównego. Ten obiekt istnieje, nawet jeśli składnik nie ma właściwości. |
| `{componentName}.{propertyName}` | Wartość właściwości składnika w formacie JSON ( `string` , typ numeru lub obiekt) |
| `{componentName}.$metadata` | Informacje o metadanych składnika, podobne do poziomu głównego `$metadata` |

Poniżej znajduje się przykład cyfrowego sznurka sformatowanego jako obiekt JSON:

```json
{
  "$dtId": "Cafe",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "Temperature": 72,
  "Location": {
    "x": 101,
    "y": 33
  },
  "component": {
    "TableOccupancy": 1,
    "$metadata": {
      "TableOccupancy": {
        "desiredValue": 1,
        "desiredVersion": 3,
        "ackVersion": 2,
        "ackCode": 200,
        "ackDescription": "OK"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:contoso:Room;1",
    "Temperature": {
      "desiredValue": 72,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "Location": {
      "desiredValue": {
        "x": 101,
        "y": 33,
      },
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

### <a name="relationship-json-format"></a>Format JSON relacji

Gdy jest reprezentowany jako obiekt JSON, relacja z dwucyfrowego sznurka będzie wyświetlać następujące pola:

| Nazwa pola | Opis |
| --- | --- |
| `$relationshipId` | Ciąg dostarczony przez użytkownika reprezentujący identyfikator tej relacji. Ten ciąg jest unikatowy w kontekście źródłowej cyfrowej przędzy, co oznacza, że `sourceId`  +  `relationshipId` jest unikatowy w kontekście wystąpienia usługi Azure Digital bliźniaczych reprezentacji. |
| `$etag` | Standardowe pole HTTP przypisane przez serwer sieci Web |
| `$sourceId` | Identyfikator źródłowej dwuosiowej |
| `$targetId` | Identyfikator docelowej przędzy cyfrowej |
| `$relationshipName` | Nazwa relacji |
| `{propertyName}` | Obowiązkowe Wartość właściwości tej relacji, w formacie JSON ( `string` , typ liczby lub obiekt) |

Oto przykład relacji sformatowanej jako obiekt JSON:

```json
{
  "$relationshipId": "relationship-01",
  "$etag": "W/\"506e8391-2b21-4ac9-bca3-53e6620f6a90\"",
  "$sourceId": "GroundFloor",
  "$targetId": "Cafe",
  "$relationshipName": "contains",
  "startDate": "2020-02-04"
}
```

## <a name="next-steps"></a>Następne kroki

Zobacz, jak zarządzać elementami grafu za pomocą interfejsów API cyfrowych przędzy na platformie Azure:
* [*Instrukcje: Zarządzanie bliźniaczych reprezentacji cyfrowym*](how-to-manage-twin.md)
* [*Instrukcje: Zarządzanie wykresem bliźniaczym z relacjami*](how-to-manage-graph.md)

Lub Dowiedz się więcej na temat wykonywania zapytań dotyczących grafu bliźniaczych reprezentacjiów cyfrowych platformy Azure w celu uzyskania informacji:
* [*Koncepcje: język zapytań*](concepts-query-language.md)