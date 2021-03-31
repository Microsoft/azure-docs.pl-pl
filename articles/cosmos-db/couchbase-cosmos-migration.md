---
title: Migrowanie z CouchBase Azure Cosmos DB do interfejsu API SQL
description: Wskazówki krok po kroku dotyczące migrowania z programu CouchBase Azure Cosmos DB do interfejsu API SQL
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 02/11/2020
ms.author: mansha
author: manishmsfte
ms.custom: devx-track-java
ms.openlocfilehash: a15c6b5919f428b28daab86fea9c3b6473d19162
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97606202"
---
# <a name="migrate-from-couchbase-to-azure-cosmos-db-sql-api"></a>Migrowanie z CouchBase Azure Cosmos DB do interfejsu API SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB to skalowalna, globalnie dystrybuowana, w pełni zarządzana baza danych. Zapewnia ona gwarantowany dostęp do danych o małym opóźnieniu. Aby dowiedzieć się więcej na temat Azure Cosmos DB, zobacz artykuł z [omówieniem](introduction.md) . Ten artykuł zawiera instrukcje dotyczące migracji aplikacji Java, które są połączone z Couchbase do konta interfejsu API SQL w Azure Cosmos DB.

## <a name="differences-in-nomenclature"></a>Różnice w nomenklaturze

Poniżej przedstawiono kluczowe funkcje, które działają inaczej w Azure Cosmos DB w porównaniu z Couchbase:

| Couchbase | Azure Cosmos DB |
|--|--|
| Serwer Couchbase | Konto |
| Porcj | baza danych |
| Porcj | Kontener/kolekcja |
| Dokument JSON | Element/dokument |

## <a name="key-differences"></a>Podstawowe różnice

* Azure Cosmos DB ma pole "ID" w dokumencie, a Couchbase ma identyfikator jako część przedziału. Pole "ID" jest unikatowe w ramach partycji.

* Azure Cosmos DB skale przy użyciu metody partycjonowania lub fragmentowania. Oznacza to, że dzieli dane na wiele fragmentów/partycje. Te partycje/fragmentów są tworzone na podstawie właściwości klucza partycji, która jest dostarczana. Możesz wybrać klucz partycji, aby zoptymalizować odczyt jako operacje zapisu, lub tylko do odczytu/zapisu. Aby dowiedzieć się więcej, zobacz artykuł [partycjonowania](./partitioning-overview.md) .

* W Azure Cosmos DB nie jest wymagane, aby hierarchia najwyższego poziomu była oznaczona jako kolekcja, ponieważ nazwa kolekcji już istnieje. Ta funkcja sprawia, że struktura JSON znacznie prostsze. Poniżej przedstawiono przykład pokazujący różnice w modelu danych między Couchbase i Azure Cosmos DB:

   **Couchbase**: Document ID = "99FF4444"

    ```json
    {
      "TravelDocument":
      {
       "Country":"India",
      "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    }
   ```

   **Azure Cosmos DB**: odwołaj się do "ID" w dokumencie, jak pokazano poniżej

    ```json
    {
      "id" : "99FF4444",
    
      "Country":"India",
       "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    
    ```
         
## <a name="java-sdk-support"></a>Obsługa zestawu SDK języka Java

Azure Cosmos DB ma następujące zestawy SDK do obsługi różnych platform Java:

* Asynchroniczny zestaw SDK
* Zestaw SDK rozruchu sprężynowego

W poniższych sekcjach opisano, kiedy należy używać każdego z tych zestawów SDK. Rozważmy przykład, w którym istnieją trzy typy obciążeń:

## <a name="couchbase-as-document-repository--spring-data-based-custom-queries"></a>Couchbase jako repozytorium dokumentów & sprężynowe zapytania niestandardowe

Jeśli migrowane obciążenie jest oparte na zestawie SDK opartym na rozruchu sprężynowym, można wykonać następujące czynności:

1. Dodaj element nadrzędny do pliku POM.xml:

   ```java
   <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>2.1.5.RELEASE</version>
      <relativePath/>
   </parent>
   ```

1. Dodaj właściwości do pliku POM.xml:

   ```java
   <azure.version>2.1.6</azure.version>
   ```

1. Dodaj zależności do pliku POM.xml:

   ```java
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
       <version>2.1.6</version>
   </dependency>
   ```

1. Dodaj właściwości aplikacji w obszarze zasoby i określ poniższe elementy. Pamiętaj, aby zastąpić parametry adresu URL, klucza i nazwy bazy danych:

   ```java
      azure.cosmosdb.uri=<your-cosmosDB-URL>
      azure.cosmosdb.key=<your-cosmosDB-key>
      azure.cosmosdb.database=<your-cosmosDB-dbName>
   ```

1. Zdefiniuj nazwę kolekcji w modelu. Możesz również określić dalsze adnotacje. Na przykład: identyfikator, klucz partycji, aby dokładnie zauważyć

   ```java
   @Document(collection = "mycollection")
       public class User {
           @id
           private String id;
           private String firstName;
           @PartitionKey
           private String lastName;
       }
   ```

Poniżej przedstawiono fragmenty kodu dla operacji CRUD:

### <a name="insert-and-update-operations"></a>Operacje INSERT i Update

Gdzie *_repo* jest obiektem repozytorium, a *doc* jest obiektem klasy Pojo. Można użyć `.save` do wstawienia lub upsert (Jeśli zostanie znaleziony dokument z określonym identyfikatorem). Poniższy fragment kodu pokazuje, jak wstawić lub zaktualizować obiekt doc:

```_repo.save(doc);```

### <a name="delete-operation"></a>Operacja usuwania

Rozważmy następujący fragment kodu, gdzie obiekt doc ma obowiązkowe identyfikatory i klucze partycji, aby zlokalizować i usunąć obiekt:

```_repo.delete(doc);```

### <a name="read-operation"></a>Operacja odczytu

Możesz odczytać dokument z lub bez określenia klucza partycji. Jeśli nie określisz klucza partycji, będzie on traktowany jako zapytanie między partycjami. Rozważmy następujące przykłady kodu, a pierwszy wykona operację przy użyciu pola Identyfikator i klucz partycji. W drugim przykładzie jest stosowane zwykłe pole & bez określania pola klucza partycji.

* ```_repo.findByIdAndName(objDoc.getId(),objDoc.getName());```
* ```_repo.findAllByStatus(objDoc.getStatus());```

Możesz teraz używać aplikacji z Azure Cosmos DB. Pełny przykładowy kod dla przykładu opisanego w tym dokumencie jest dostępny w repozytorium GitHub [CouchbaseToCosmosDB-SpringCosmos](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/main/SpringCosmos) .

## <a name="couchbase-as-a-document-repository--using-n1ql-queries"></a>Couchbase jako repozytorium dokumentów & przy użyciu zapytań N1QL

Zapytania N1QL są sposobem definiowania zapytań w Couchbase.

|N1QL zapytanie | Zapytanie CosmosDB platformy Azure|
|-------------------|-------------------|
|Wybierz META ( `TravelDocument` ). ID as ID, `TravelDocument` . * z `TravelDocument` Where `_type` = "com. XX. XX. XX. xxx. xxx. xxxx" i Country = "Indie" oraz wszystkie m w wizach spełnia wartość m. Type = = "wiele-entry" i m. Country w ["Indie", BHUTAN "] order by ` Validity` | Wybierz pozycję c. ID, c z pozycji c JOIN m w c. Country = "Indie", gdzie c._type = "com. XX. XX. XX. xxx. xxx. xxxx" i c. Country = "Indie" i m. Type = "wiele wpisów" i m. Country w ("Indie", "Bhutan") ORDER BY c |

W zapytaniach N1QL można zauważyć następujące zmiany:

* Nie musisz używać słowa kluczowego META lub zapoznaj się z dokumentem pierwszego poziomu. Zamiast tego możesz utworzyć własne odwołanie do kontenera. W tym przykładzie uważamy go za "c" (może to być wszystko). To odwołanie jest używane jako prefiks dla wszystkich pól pierwszego poziomu. Przykład fr, c.id, c. Country itd.

* Zamiast "dowolny" można teraz wykonać sprzężenie w dokumencie podrzędnym i odwołać się do niego przy użyciu dedykowanego aliasu, takiego jak "m". Po utworzeniu aliasu dla dokumentu podrzędnego należy użyć aliasu. Na przykład m. Country.

* Sekwencja przesunięcia jest inna w Azure Cosmos DB zapytania, najpierw należy określić przesunięcie, a następnie ograniczyć. Zaleca się, aby nie używać zestawu SDK danych sprężynowych, jeśli używasz maksymalnych niestandardowych zapytań, ponieważ mogą one mieć niepotrzebne obciążenie po stronie klienta podczas przekazywania zapytania do Azure Cosmos DB. Zamiast tego mamy bezpośredni asynchroniczny zestaw SDK Java, który może być w tym przypadku wydajnie wykorzystywany.

### <a name="read-operation"></a>Operacja odczytu

Użyj asynchronicznego zestawu Java SDK, wykonując następujące czynności:

1. Skonfiguruj następujący zależność do pliku POM.xml:

   ```java
   <!-- https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb -->
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmos</artifactId>
       <version>3.0.0</version>
   </dependency>
   ```

1. Utwórz obiekt połączenia dla Azure Cosmos DB przy użyciu `ConnectionBuilder` metody, jak pokazano w poniższym przykładzie. Upewnij się, że ta deklaracja została umieszczona w ziarnach, tak że następujący kod powinien zostać wykonany tylko raz:

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
    
   if(client==null)
      client= CosmosClient.builder()
         .endpoint(Host)//(Host, PrimaryKey, dbName, collName).Builder()
          .connectionPolicy(cp)
          .key(PrimaryKey)
          .consistencyLevel(ConsistencyLevel.EVENTUAL)
          .build();
   
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

1. Aby wykonać zapytanie, należy uruchomić następujący fragment kodu:

   ```java
   Flux<FeedResponse<CosmosItemProperties>> objFlux= container.queryItems(query, fo);
   ```

Teraz za pomocą powyższej metody można przekazać wiele zapytań i wykonać bez kłopotów. W przypadku konieczności wykonania jednego dużego zapytania, które można podzielić na wiele zapytań, spróbuj użyć poniższego fragmentu kodu zamiast poprzedniego:

```java
for(SqlQuerySpec query:queries)
{
   objFlux= container.queryItems(query, fo);
   objFlux .publishOn(Schedulers.elastic())
         .subscribe(feedResponse->
            {
               if(feedResponse.results().size()>0)
               {
                  _docs.addAll(feedResponse.results());
               }
            
            },
            Throwable::printStackTrace,latch::countDown);
   lstFlux.add(objFlux);
}
                  
      Flux.merge(lstFlux);
      latch.await();
}
```

Za pomocą powyższego kodu można uruchamiać zapytania równolegle i zwiększać liczbę dystrybuowanych wykonań w celu optymalizacji. Dalsze operacje wstawiania i aktualizacji można wykonywać również w programie:

### <a name="insert-operation"></a>Wstaw operację

Aby wstawić dokument, uruchom następujący kod:

```java
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

Następnie Zasubskrybuj program mono jako:

```java
CountDownLatch latch=new CountDownLatch(1);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
           if(resourceResponse.statusCode()!=successStatus)
              {
                 throw new RuntimeException(resourceResponse.toString());
              }
           },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="upsert-operation"></a>Upsert, operacja

Operacja upsert wymaga określenia dokumentu, który należy zaktualizować. Aby pobrać kompletny dokument, można użyć fragmentu wymienionego w obszarze Operacja odczytu nagłówka i zmodyfikować wymagane pola. Poniższy fragment kodu upserts dokument:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Następnie Zasubskrybuj polecenie mono. Zapoznaj się z fragmentem subskrypcji mono w operacji INSERT.

### <a name="delete-operation"></a>Operacja usuwania

Poniższy fragment kodu wykona operację usuwania:

```java
CosmosItem objItem= container.getItem(doc.Id, doc.Tenant);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Następnie Zasubskrybuj narzędzie mono, odwołaj się do fragmentu subskrypcji mono w operacji INSERT. Pełny przykładowy kod jest dostępny w repozytorium GitHub [CouchbaseToCosmosDB-AsyncInSpring](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/main/AsyncInSpring) .

## <a name="couchbase-as-a-keyvalue-pair"></a>Couchbase jako para klucz/wartość

Jest to prosty typ obciążenia, w którym można wykonywać wyszukiwania zamiast zapytań. W przypadku par klucz/wartość wykonaj następujące kroki:

1. Rozważ użycie "/ID" jako klucza podstawowego, co zapewni, że można wykonać operację wyszukiwania bezpośrednio w konkretnej partycji. Utwórz kolekcję i określ wartość "/ID" jako klucz partycji.

1. Całkowicie Wyłącz indeksowanie. Ponieważ wykonujesz operacje wyszukiwania, nie ma żadnego punktu, aby przeciążać indeksowanie. Aby wyłączyć indeksowanie, zaloguj się do Azure Portal, przejdź do Azure Cosmos DB konta. Otwórz **Eksplorator danych**, wybierz swoją **bazę danych** i **kontener**. Otwórz kartę **ustawienia & skalowanie** i wybierz  **zasady indeksowania**. Obecnie zasady indeksowania wyglądają następująco:
    
   ```json
   {
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/\"_etag\"/?"
        }
    ]
    }
   ````

   Zastąp powyższe zasady indeksowania następującymi zasadami:

   ```json
   {
    "indexingMode": "none",
    "automatic": false,
    "includedPaths": [],
    "excludedPaths": []
    }
   ```

1. Użyj poniższego fragmentu kodu, aby utworzyć obiekt połączenia. Obiekt połączenia (umieszczony w @Bean lub uczynić go statycznym):

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
   
   if(client==null)
      client= CosmosClient.builder()
         .endpoint(Host)//(Host, PrimaryKey, dbName, collName).Builder()
          .connectionPolicy(cp)
          .key(PrimaryKey)
          .consistencyLevel(ConsistencyLevel.EVENTUAL)
          .build();
    
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

Teraz można wykonywać operacje CRUD w następujący sposób:

### <a name="read-operation"></a>Operacja odczytu

Aby odczytać element, użyj następującego fragmentu kodu:

```java        
CosmosItemRequestOptions ro=new CosmosItemRequestOptions();
ro.partitionKey(new PartitionKey(documentId));
CountDownLatch latch=new CountDownLatch(1);
      
var objCosmosItem= container.getItem(documentId, documentId);
Mono<CosmosItemResponse> objMono = objCosmosItem.read(ro);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
           if(resourceResponse.item()!=null)
           {
              doc= resourceResponse.properties().toObject(UserModel.class);
           }
        },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="insert-operation"></a>Wstaw operację

Aby wstawić element, można wykonać następujący kod:

```java
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

Następnie Zasubskrybuj program mono jako:

```java
CountDownLatch latch=new CountDownLatch(1);
objMono.subscribeOn(Schedulers.elastic())
      .subscribe(resourceResponse->
      {
         if(resourceResponse.statusCode()!=successStatus)
            {
               throw new RuntimeException(resourceResponse.toString());
            }
         },
      Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="upsert-operation"></a>Upsert, operacja

Aby zaktualizować wartość elementu, zapoznaj się z poniższym fragmentem kodu:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Następnie Zasubskrybuj narzędzie mono, odwołaj się do fragmentu subskrypcji mono w operacji INSERT.

### <a name="delete-operation"></a>Operacja usuwania

Aby wykonać operację usuwania, użyj następującego fragmentu kodu:

```java
CosmosItem objItem= container.getItem(id, id);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Następnie Zasubskrybuj narzędzie mono, odwołaj się do fragmentu subskrypcji mono w operacji INSERT. Pełny przykładowy kod jest dostępny w repozytorium GitHub [CouchbaseToCosmosDB-AsyncKeyValue](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/main/AsyncKeyValue) .

## <a name="data-migration"></a>Migrowanie danych

Istnieją dwa sposoby migrowania danych.

* **Użyj Azure Data Factory:** Jest to najbardziej zalecana metoda migrowania danych. Aby [Data Factory Cosmos DB](../data-factory/connector-azure-cosmos-db.md) zapoznać się ze szczegółowymi krokami, należy skonfigurować źródło jako Couchbase i ujścia jako Azure Cosmos DB interfejsu API SQL.

* **Użyj narzędzia do importowania danych Azure Cosmos DB:** Ta opcja jest zalecana do migracji przy użyciu maszyn wirtualnych z mniejszą ilością danych. Aby uzyskać szczegółowe instrukcje, zobacz artykuł dotyczący [importera danych](./import-data.md) .

## <a name="next-steps"></a>Następne kroki

* Aby przeprowadzić testy wydajnościowe, zobacz [testowanie wydajności i skalowania przy użyciu Azure Cosmos DB](./performance-testing.md) artykułu.
* Aby zoptymalizować kod, zobacz [porady dotyczące wydajności Azure Cosmos DB](./performance-tips-async-java.md) artykułu.
* Poznaj zestaw SDK dla języka Java Async v3, repozytorium usługi GitHub [dotyczące zestawu SDK](https://github.com/Azure/azure-cosmosdb-java/tree/v3) .
