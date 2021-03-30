---
title: Koncepcje projekcji
titleSuffix: Azure Cognitive Search
description: Zapisz i umieść wzbogacone dane z potoku indeksowania wzbogacenia AI do magazynu wiedzy, który ma być używany w scenariuszach innych niż wyszukiwanie pełnotekstowe.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 22db4f95bacd926208ac7edf3306cd136d81b00e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85565186"
---
# <a name="knowledge-store-projections-in-azure-cognitive-search"></a>Projekcje w sklepie z bazami danych na platformie Azure Wyszukiwanie poznawcze

Usługa Azure Wyszukiwanie poznawcze umożliwia wzbogacanie zawartości dzięki wbudowanym umiejętnościom poznawczym i umiejętnościom niestandardowym w ramach indeksowania. Wzbogacania tworzą nowe informacje, w przypadku których nie istniały wcześniej: wyodrębnianie informacji z obrazów, wykrywanie tonacji, fraz kluczowych i jednostek z tekstu, aby utworzyć kilka nazw. Wzbogacania również dodają strukturę do tekstu nierozróżnianego. Wszystkie te procesy powodują, że wyniki wyszukiwania pełnotekstowego są bardziej efektywne. W wielu przypadkach wzbogacone dokumenty są przydatne w scenariuszach innych niż wyszukiwanie, na przykład w przypadku wyszukiwania w bazie wiedzy.

Projekcje, składnik [magazynu wiedzy](knowledge-store-concept-intro.md), są widokami wzbogaconych dokumentów, które można zapisać w magazynie fizycznym na potrzeby wyszukiwania w bazie wiedzy. Projekcja pozwala na "projekt" dane do kształtu, który jest zgodny z potrzebami, dzięki czemu można zachować relacje, tak aby narzędzia takie jak Power BI mogły odczytywać dane bez dodatkowych nakładów pracy.

Projekcje mogą być tabelaryczne, z danymi przechowywanymi w wierszach i kolumnach w usłudze Azure Table Storage lub w obiektach JSON przechowywanych w usłudze Azure Blob Storage. Można zdefiniować wiele projekcji danych w miarę ich wzbogacania. Wiele projekcji jest przydatne, gdy chcesz, aby te same dane różniły się inaczej w przypadku poszczególnych przypadków użycia.

Magazyn wiedzy obsługuje trzy typy projekcji:

+ **Tabele**: w przypadku danych, które są najlepiej reprezentowane jako wiersze i kolumny, projekcje tabeli umożliwiają definiowanie kształtu schematized lub projekcji w magazynie tabel. Tylko prawidłowe obiekty JSON mogą być rzutowane jako tabele, wzbogacony dokument może zawierać węzły, które nie są obiektami JSON i podczas projekcji tych obiektów, tworzą prawidłowy obiekt JSON ze umiejętnością kształtu lub kształtem wbudowane.

+ **Obiekty**: gdy potrzebna jest reprezentacja w formacie JSON danych i wzbogacania, projekcje obiektów są zapisywane jako obiekty blob. Tylko prawidłowe obiekty JSON mogą być rzutowane jako obiekty, wzbogacony dokument może zawierać węzły, które nie są obiektami JSON i podczas projekcji tych obiektów, tworzą prawidłowy obiekt JSON ze umiejętnością kształtu lub kształtem wbudowane.

+ **Pliki**: Jeśli zachodzi potrzeba zapisania obrazów wyodrębnionych z dokumentów, projekcje plików umożliwia zapisanie znormalizowanych obrazów w usłudze BLOB Storage.

Aby wyświetlić projekcje zdefiniowane w kontekście, krok po kroku [Tworzenie sklepu z bazami danych w spoczynku](knowledge-store-create-rest.md).

## <a name="projection-groups"></a>Grupy projekcji

W niektórych przypadkach należy zaprojektować wzbogacone dane w różnych kształtach, aby zaspokoić różne cele. Magazyn wiedzy pozwala definiować wiele grup projekcji. Grupy projekcji mają następujące kluczowe cechy wzajemnej wyłączności i pokrewności.

### <a name="mutual-exclusivity"></a>Wzajemne wyłączności

Cała zawartość zadana w jednej grupie jest niezależna od danych rzutowanych na inne grupy projekcji.
Niezależność oznacza, że te same dane mogą wyglądać inaczej, ale powtórzone w każdej grupie projekcji.

### <a name="relatedness"></a>Pokrewieństwo

Grupy projekcji teraz umożliwiają projekcję dokumentów w typach projekcji przy zachowaniu relacji między typami projekcji. Cała zawartość zadana w ramach jednej grupy projekcji zachowuje relacje między danymi w typach projekcji. W tabelach relacje są oparte na wygenerowanym kluczu, a każdy węzeł podrzędny zachowuje odwołanie do węzła nadrzędnego. W przypadku typów (tabel, obiektów i plików) relacje są zachowywane, gdy pojedynczy węzeł jest rzutowany na różne typy. Rozważmy na przykład scenariusz, w którym dokument zawiera obrazy i tekst. Można projektować tekst do tabel lub obiektów oraz obrazów do plików, w których tabele lub obiekty mają kolumnę/Właściwość zawierającą adres URL pliku.

## <a name="input-shaping"></a>Kształtowanie danych wejściowych

Pobieranie danych w odpowiednim kształcie lub strukturze jest kluczem do efektywnego użycia, być tabelami lub obiektami. Możliwość kształtowania lub struktury danych w zależności od sposobu, w jaki planujesz uzyskać dostęp do niej, jest kluczową funkcją udostępnianą jako umiejętność **kształtu** w ramach zestawu umiejętności.  

Projekcje są łatwiejsze do zdefiniowania, gdy masz obiekt w drzewie wzbogacania, który jest zgodny ze schematem projekcji. Zaktualizowana [umiejętność kształtu](cognitive-search-skill-shaper.md) umożliwia redagowanie obiektów z różnych węzłów drzewa wzbogacania i ich nadrzędne używanie w nowym węźle. Umiejętność **kształtu** pozwala definiować typy złożone z zagnieżdżonymi obiektami.

Jeśli masz zdefiniowany nowy kształt, który zawiera wszystkie elementy potrzebne do wykreślania, możesz teraz użyć tego kształtu jako źródła dla projekcji lub jako dane wejściowe do innej umiejętności.

## <a name="projection-slicing"></a>Dzielenie wycinków

Podczas definiowania grupy projekcji pojedynczy węzeł drzewa wzbogacania można podzielić na wiele powiązanych tabel lub obiektów. Dodanie projekcji z ścieżką źródłową, która jest elementem podrzędnym istniejącej projekcji, spowoduje, że węzeł podrzędny zostanie podzielony na węzeł nadrzędny, a następnie nastąpi umieszczenie nowej, powiązanej tabeli lub obiektu. Ta technika pozwala zdefiniować pojedynczy węzeł w umiejętności kształtu, który może być źródłem dla wszystkich projekcji.

## <a name="table-projections"></a>Projekcje tabeli

Ponieważ ułatwia to importowanie, zalecamy projekcje tabeli na potrzeby eksploracji danych za pomocą Power BI. Ponadto projekcje tabeli umożliwiają zmianę kardynalności między relacjami między tabelami. 

Można projektować pojedynczy dokument w indeksie w wielu tabelach, zachowując relacje. Podczas projekcji w wielu tabelach, cały kształt zostanie rzutowany na każdą tabelę, chyba że węzeł podrzędny jest źródłem innej tabeli w tej samej grupie.

### <a name="defining-a-table-projection"></a>Definiowanie projekcji tabeli

Podczas definiowania projekcji tabeli w ramach `knowledgeStore` elementu zestawu umiejętności, Zacznij od mapowania węzła w drzewie wzbogacania do źródła tabeli. Zazwyczaj ten węzeł jest wynikiem umiejętności **kształtu** , który został dodany do listy umiejętności w celu utworzenia określonego kształtu, który jest potrzebny do projektu w tabelach. Wybrany dla projektu węzeł można podzielić na projekt na wiele tabel. Definicja tabel jest listą tabel, które mają być przeznaczone do projektu.

Każda tabela wymaga trzech właściwości:

+ TableName: Nazwa tabeli w usłudze Azure Storage.

+ generatedKeyName: Nazwa kolumny klucza, który jednoznacznie identyfikuje ten wiersz.

+ Źródło: węzeł z drzewa wzbogacania, z którego pochodzą Twoje wzbogacenia. Ten węzeł jest zwykle wynikiem kształtu, ale może być wyjściem z dowolnych umiejętności.

Oto przykład projekcji tabeli.

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [
            { "tableName": "MainTable", "generatedKeyName": "SomeId", "source": "/document/EnrichedShape" },
            { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/EnrichedShape/*/KeyPhrases/*" },
            { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/EnrichedShape/*/Entities/*" }
          ]
        },
        {
          "objects": [ ]
        },
        {
            "files": [ ]
        }
      ]
    }
}
```

Jak pokazano w tym przykładzie, kluczowe frazy i jednostki są modelowane w różnych tabelach i zawierają odwołanie z powrotem do elementu nadrzędnego (Mainname) dla każdego wiersza.

## <a name="object-projections"></a>Projekcje obiektów

Projekcje obiektów to reprezentacje JSON drzewa wzbogacania, które mogą być źródłem z dowolnego węzła. W wielu przypadkach ta sama umiejętność **kształtu** , która tworzy projekcję tabeli, może służyć do generowania projekcji obiektu. 

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [ ]
        },
        {
          "objects": [
            {
              "storageContainer": "hotelreviews", 
              "source": "/document/hotel"
            }
          ]
        },
        {
            "files": [ ]
        }
      ]
    }
}
```

Generowanie projekcji obiektu wymaga kilku atrybutów specyficznych dla obiektu:

+ storageContainer: kontener obiektów blob, w którym zostaną zapisane obiekty
+ Źródło: ścieżka do węzła drzewa wzbogacania, który jest elementem głównym projekcji

## <a name="file-projection"></a>Projekcja pliku

Projekcje plików są podobne do projekcji obiektów i działają tylko w `normalized_images` kolekcji. Podobnie jak w projekcjach obiektów, projekcje plików są zapisywane w kontenerze obiektów blob z prefiksem folderu w wartości zakodowanej Base64 identyfikatora dokumentu. Projekcje plików nie mogą współużytkować tego samego kontenera co projekcje obiektów i muszą być rzutowane do innego kontenera.

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [ ]
        },
        {
          "objects": [ ]
        },
        {
            "files": [
                 {
                  "storageContainer": "ReviewImages",
                  "source": "/document/normalized_images/*"
                }
            ]
        }
      ]
    }
}
```

## <a name="projection-lifecycle"></a>Cykl życia projekcji

Projekcje mają cykl życia, który jest powiązany z danymi źródłowymi w źródle danych. Gdy dane są aktualizowane i ponownie indeksowane, projekcje są aktualizowane przy użyciu wyników wzbogacania, dzięki czemu projekcje są ostatecznie spójne z danymi w źródle danych. Projekcje dziedziczą zasady usuwania skonfigurowane dla Twojego indeksu. Projekcje nie są usuwane, gdy zostanie usunięty indeksator lub usługa wyszukiwania.

## <a name="using-projections"></a>Korzystanie z projekcji

Po uruchomieniu indeksatora można odczytać dane z projektu w kontenerach lub tabelach określonych za pomocą projekcji.

W przypadku analiz poszukiwanie w Power BI jest proste, jak w przypadku ustawienia usługi Azure Table Storage jako źródła danych. Można łatwo utworzyć zestaw wizualizacji na danych przy użyciu relacji w ramach programu.

Alternatywnie, jeśli konieczne jest użycie wzbogaconych danych w potoku analizy danych, można [załadować dane z obiektów BLOB do Pandas Dataframe](../machine-learning/team-data-science-process/explore-data-blob.md).

Na koniec Jeśli trzeba wyeksportować dane ze sklepu z bazami danych, Azure Data Factory ma łączniki do eksportowania danych i wystawić je w wybranej bazie danych. 

## <a name="next-steps"></a>Następne kroki

Następnym krokiem jest utworzenie pierwszego sklepu z wiedzą przy użyciu przykładowych danych i instrukcji.

> [!div class="nextstepaction"]
> [Utwórz magazyn wiedzy w usłudze REST](knowledge-store-create-rest.md).

Samouczek obejmujący zaawansowane koncepcje dotyczące projekcji, takich jak dzielenie, kształtowanie i relacje w tekście, rozpoczyna się od [definiowania projekcji w sklepie z wiedzą](knowledge-store-projections-examples.md)

> [!div class="nextstepaction"]
> [Definiowanie prognoz w sklepie z bazami danych](knowledge-store-projections-examples.md)
