---
title: Konfigurowanie bramy do przesyłania żądań
description: Dowiedz się, jak skonfigurować bramę obsługującą ruch przychodzący dla aplikacji działających na Service Fabric siatki.
author: georgewallace
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: aa3ac9d8835cd17387346bb29b3e7c30f286cd1f
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91839728"
---
# <a name="configure-a-gateway-resource-to-route-requests"></a>Konfigurowanie zasobu bramy do przesyłania żądań

Zasób bramy służy do kierowania ruchem przychodzącym do sieci, w której znajduje się Twoja aplikacja. Skonfiguruj, aby określić reguły, za pomocą których żądania są kierowane do określonych usług lub punktów końcowych w oparciu o strukturę żądania. Aby uzyskać więcej informacji o sieciach i bramach w sieci, zobacz [wprowadzenie do sieci w Service Fabric siatki](service-fabric-mesh-networks-and-gateways.md) . 

Zasoby bramy muszą być zadeklarowane jako część szablonu wdrożenia (JSON lub YAML) i są zależne od zasobu sieciowego. Ten dokument zawiera opis różnych właściwości, które można ustawić dla bramy, i obejmuje przykładową konfigurację bramy.

## <a name="options-for-configuring-your-gateway-resource"></a>Opcje konfigurowania zasobu bramy

Ponieważ zasób bramy służy jako Most między siecią aplikacji a siecią źródłową infrastruktury ( `open` Sieć). Należy tylko skonfigurować jeden (w podglądzie siatki, istnieje limit jednej bramy dla każdej aplikacji). Deklaracja dla zasobu składa się z dwóch głównych części: metadanych zasobów i właściwości. 

### <a name="gateway-resource-metadata"></a>Metadane zasobów bramy

Brama jest zadeklarowana przy użyciu następujących metadanych:
* `apiVersion` -musi być ustawiony na wartość "2018-09-01-Preview" (lub nowszą, w przyszłości)
* `name` -Nazwa ciągu dla tej bramy
* `type` -"Microsoft. ServiceFabricMesh/Gateways"
* `location` -powinna być ustawiona na lokalizację aplikacji/sieci; zwykle będzie to odwołanie do parametru lokalizacji we wdrożeniu
* `dependsOn` — Sieć, dla której ta brama będzie działać jako punkt transferu dla

Oto jak wygląda w szablonie wdrożenia Azure Resource Manager (JSON): 

```json
{
  "apiVersion": "2018-09-01-preview",
  "name": "myGateway",
  "type": "Microsoft.ServiceFabricMesh/gateways",
  "location": "[parameters('location')]",
  "dependsOn": [
    "Microsoft.ServiceFabricMesh/networks/myNetwork"
  ],
  "properties": {
    [...]
  }
}
```

### <a name="gateway-properties"></a>Właściwości bramy

Sekcja właściwości służy do definiowania sieci, między którymi jest brama, oraz reguł żądań routingu. 

#### <a name="source-and-destination-network"></a>Sieć źródłowa i docelowa 

Każda Brama wymaga programu `sourceNetwork` i `destinationNetwork` . Sieć źródłowa jest definiowana jako sieć, z której aplikacja będzie odbierać żądania przychodzące. Właściwość Name powinna zawsze być ustawiona na wartość "Open". Sieć docelowa to sieć, do której są kierowane żądania. Wartość nazwy dla tej opcji powinna być ustawiona na nazwę zasobu sieci lokalnej aplikacji (powinna zawierać pełne odwołanie do zasobu). Poniżej znajduje się Przykładowa konfiguracja tego, jak wygląda w przypadku wdrożenia w sieci o nazwie "Moja sieć".

```json 
"properties": {
  "description": "Service Fabric Mesh Gateway",
  "sourceNetwork": {
    "name": "Open"
  },
  "destinationNetwork": {
    "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'myNetwork')]"
  },
  [...]
}
```

#### <a name="rules"></a>Reguły 

Brama może mieć wiele reguł routingu, które określają sposób obsługi ruchu przychodzącego. Reguła routingu definiuje relację między portem nasłuchiwania i docelowym punktem końcowym dla danej aplikacji. W przypadku reguł routingu TCP istnieje mapowanie 1:1 między portem: punkt końcowy. W przypadku reguł routingu HTTP można ustawić bardziej złożone reguły routingu, które badają ścieżkę żądania oraz opcjonalnie nagłówki, aby określić, jak będzie kierowane żądanie. 

Reguły routingu są określane dla poszczególnych portów. Każdy port transferu danych przychodzących ma własną tablicę reguł w sekcji Właściwości konfiguracji bramy. 

#### <a name="tcp-routing-rules"></a>Reguły routingu TCP 

Reguła routingu TCP składa się z następujących właściwości: 
* `name` -odwołanie do reguły, która może być dowolnym dowolnym ciągiem 
* `port` -Port, na którym nasłuchuje żądań przychodzących 
* `destination` -Specyfikacja punktu końcowego obejmująca `applicationName` , `serviceName` i `endpointName` , w przypadku których żądania muszą być kierowane do

Oto przykładowa reguła routingu TCP:

```json
"properties": {
  [...]
  "tcp": [
    {
      "name": "web",
      "port": 80,
      "destination": {
        "applicationName": "myApp",
        "serviceName": "myService",
        "endpointName": "myListener"
      }
    }
  ]
}
```


#### <a name="http-routing-rules"></a>Reguły routingu HTTP 

Reguła routingu HTTP składa się z następujących właściwości: 
* `name` -odwołanie do reguły, która może być dowolnym dowolnym ciągiem 
* `port` -Port, na którym nasłuchuje żądań przychodzących 
* `hosts` -tablicę zasad, które mają zastosowanie do żądań skierowanych do różnych "hostów" na porcie określonym powyżej. Hosty to zbiór aplikacji i usług, które mogą być uruchomione w sieci i mogą obsługiwać żądania przychodzące, np. aplikację sieci Web. Zasady hosta są interpretowane w podanej kolejności, dlatego należy utworzyć następujące elementy na malejących poziomach
    * `name` -Nazwa DNS hosta, dla którego określono następujące reguły routingu. Użycie znaku "*" spowoduje utworzenie reguł routingu dla wszystkich hostów.
    * `routes` -tablicę zasad dla tego konkretnego hosta
        * `match` -Specyfikacja przychodzącej struktury żądań dla tej reguły na podstawie `path`
            * `path` -zawiera `value` (przychodzący identyfikator URI), `rewrite` (w jaki sposób żądanie ma zostać przesłane dalej) i `type` (obecnie tylko "prefiks")
            * `header` -to opcjonalna Tablica wartości nagłówków do dopasowania w nagłówku żądania, gdy żądanie jest zgodne ze specyfikacją ścieżki (powyżej).
              * Każdy wpis zawiera `name` (Nazwa ciągu nagłówka do dopasowania), `value` (wartość ciągu nagłówka w żądaniu) i `type` (teraz może być tylko "dokładne")
        * `destination` — Jeśli żądanie jest zgodne, zostanie przekazane do tego miejsca docelowego, które jest określone przy użyciu `applicationName` , `serviceName` i `endpointName`

Poniżej znajduje się przykład reguły routingu HTTP, która powinna być stosowana do żądań przychodzących na porcie 80, do wszystkich hostów obsługiwanych przez aplikacje w tej sieci. Jeśli adres URL żądania ma strukturę zgodną ze specyfikacją ścieżki, tj., `<IPAddress>:80/pickme/<requestContent>` zostanie ona skierowana do `myListener` punktu końcowego.  

```json
"properties": {
  [...]
  "http": [
    {
      "name": "web",
      "port": 80,
      "hosts": [
        {
          "name": "*",
          "routes": [
            {
              "match": {
                "path": {
                  "value": "/pickme",
                  "rewrite": "/",
                  "type": "Prefix"
                }
              },
              "destination": {
                "applicationName": "meshApp",
                "serviceName": "myService",
                "endpointName": "myListener"
              }
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="sample-config-for-a-gateway-resource"></a>Przykładowa konfiguracja dla zasobu bramy 

Oto jak wygląda pełna Konfiguracja zasobów bramy (jest to dostosowana z przykładu danych przychodzących dostępnych w [repozytorium próbek siatki](https://github.com/Azure-Samples/service-fabric-mesh/blob/2018-09-01-preview/templates/ingress/meshingress.linux.json)):

```json
{
  "apiVersion": "2018-09-01-preview",
  "name": "ingressGatewayLinux",
  "type": "Microsoft.ServiceFabricMesh/gateways",
  "location": "[parameters('location')]",
  "dependsOn": [
    "Microsoft.ServiceFabricMesh/networks/meshNetworkLinux"
  ],
  "properties": {
    "description": "Service Fabric Mesh Gateway for Linux mesh samples.",
    "sourceNetwork": {
      "name": "Open"
    },
    "destinationNetwork": {
      "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'meshNetworkLinux')]"
    },
    "http": [
      {
        "name": "web",
        "port": 80,
        "hosts": [
          {
            "name": "*",
            "routes": [
              {
                "match": {
                  "path": {
                    "value": "/hello",
                    "rewrite": "/",
                    "type": "Prefix"
                  }
                },
                "destination": {
                  "applicationName": "meshAppLinux",
                  "serviceName": "helloWorldService",
                  "endpointName": "helloWorldListener"
                }
              },
              {
                "match": {
                  "path": {
                    "value": "/counter",
                    "rewrite": "/",
                    "type": "Prefix"
                  }
                },
                "destination": {
                  "applicationName": "meshAppLinux",
                  "serviceName": "counterService",
                  "endpointName": "counterServiceListener"
                }
              }
            ]
          }
        ]
      }
    ]
  }
}
```

Ta brama jest skonfigurowana dla aplikacji systemu Linux "meshAppLinux", która składa się z co najmniej dwóch usług, "helloWorldService" i "counterService", które nasłuchuje na porcie 80. W zależności od struktury adresu URL żądania przychodzącego kieruje ono żądanie do jednej z tych usług. 
* " \<IPAddress> : 80/helloWorld/ \<request\> " spowoduje przekierowanie żądania do "helloWorldListener" w helloWorldService. 
* " \<IPAddress> : 80/Counter/ \<request\> " spowoduje przekierowanie żądania do "counterListener" w CounterService. 

## <a name="next-steps"></a>Następne kroki
* Wdróż [przykład](https://github.com/Azure-Samples/service-fabric-mesh/tree/2018-09-01-preview/templates/ingress) transferu danych przychodzących, aby zobaczyć bramy w działaniu
