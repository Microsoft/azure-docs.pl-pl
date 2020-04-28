---
title: Rozwiązania wykorzystujące globalnie dystrybuowaną analizę w Azure Cosmos DB.
description: Zapoznaj się z rozwiązaniami, które można skompilować za pomocą globalnie dystrybuowanej analizy w Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 09/26/2019
ms.reviewer: sngun
ms.openlocfilehash: d0b5042c1401a95cc75f4c01ace843659b6babfe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "73681716"
---
# <a name="solutions-using-globally-distributed-analytics-in-azure-cosmos-db"></a>Rozwiązania wykorzystujące globalnie dystrybuowaną analizę w Azure Cosmos DB

W tym artykule opisano rozwiązania, które można skompilować przy użyciu ogólnie dystrybuowanej analizy w Azure Cosmos DB.

## <a name="retail-and-consumer-goods"></a>Sprzedaż detaliczna i towary konsumenckie

Możesz użyć obsługi platformy Spark w Azure Cosmos DB, aby dostarczyć rekomendacje i oferty w czasie rzeczywistym. Możesz pomóc klientom w znalezieniu elementów, których potrzebują, z personalizacją w czasie rzeczywistym i zaleceniami dotyczącymi produktów.

* Możesz użyć wbudowanej obsługi Machine Learning dostarczonej przez środowisko uruchomieniowe Apache Spark, aby wygenerować zalecenia w czasie rzeczywistym w ramach katalogów produktów.

* Możesz kliknąć pozycję dane przesyłane strumieniowo, kupić dane i dane klientów, aby zapewnić zamierzone zalecenia dotyczące wartości okresu istnienia.

* Korzystając z funkcji globalnej dystrybucji Azure Cosmos DB, duże ilości danych produktu, które są rozmieszczone w różnych regionach, można analizować w milisekundach.

* Możesz szybko uzyskać wgląd w szczegółowe informacje dla użytkowników i danych rozproszonych geograficznie. Możesz poprawić szybkość konwersji dla promocji, udostępniając odpowiednią reklamę odpowiednim użytkownikom w odpowiednim czasie.

* Możesz wykorzystać wbudowaną funkcję przetwarzania strumieniowego Spark do wzbogacania danych na żywo, łącząc ją ze statycznymi danymi klienta. Dzięki temu możesz dostarczać bardziej spersonalizowanych i strategicznych reklam w czasie rzeczywistym i w kontekście działania wykonywanego przez klientów.

Na poniższej ilustracji przedstawiono sposób, w jaki Azure Cosmos DB obsługa platformy Spark służy do optymalizowania cen i promocji:

![Azure Cosmos DB obsługa platformy Spark w celu zoptymalizowania cen i promocji](./media/analytics-solution-architectures/optimize-pricing-and-promotions.png)


Na poniższej ilustracji przedstawiono, jak Azure Cosmos DB obsługa platformy Spark jest używana w aparacie rekomendacji w czasie rzeczywistym:

![Azure Cosmos DB obsługa platformy Spark w aparacie rekomendacji w czasie rzeczywistym](./media/analytics-solution-architectures/real-time-recommendation-engine.png)

## <a name="manufacturing-and-iot"></a>Produkcja i IoT

Wbudowana platforma analityczna Azure Cosmos DB umożliwia włączenie analizy danych IoT w czasie rzeczywistym z milionów urządzeń na skalę globalną. Możesz tworzyć nowoczesne innowacje, takie jak przewidywanie wzorców pogodowych, analizy predykcyjnej i optymalizacji zużycia energii.

* Za pomocą Azure Cosmos DB można pogodzić dane, takie jak metryki zasobów w czasie rzeczywistym i czynniki pogodowe, a następnie zastosować inteligentne analizy siatki w celu zoptymalizowania wydajności podłączonych urządzeń w polu. Inteligentna analiza siatki jest kluczem do kontrolowania kosztów operacyjnych, zwiększania niezawodności sieci i dostarczania spersonalizowanych usług energetycznych do klientów.

Na poniższej ilustracji przedstawiono, w jaki sposób obsługa platformy Spark w Azure Cosmos DB jest używana do odczytywania metryk z urządzeń IoT i stosowania inteligentnej analizy siatki:

![Azure Cosmos DB obsługa platformy Spark w celu odczytywania metryk z urządzeń IoT](./media/analytics-solution-architectures/read-metrics-from-iot-devices.png)

## <a name="predictive-maintenance"></a>Konserwacja zapobiegawcza

* Utrzymywanie zasobów takich jak kompresory, które są używane w małych zbiorach wiertniczych na platformach głębokiej wody, jest złożonym Endeavor. Te zasoby znajdują się na całym świecie i generują petabajtów danych. Za pomocą Azure Cosmos DB można utworzyć kompleksowy Potok danych predykcyjnych, który korzysta z przesyłania strumieniowego Spark, aby przetwarzać duże ilości danych telemetrycznych czujnika, części zasobów magazynu i mapowania czujników.

* Możesz tworzyć i wdrażać modele uczenia maszynowego w celu przewidywania niepowodzeń zasobów przed ich wystąpieniem i wystawiać zlecenia służbowe do konserwacji przed wystąpieniem awarii.

Na poniższej ilustracji przedstawiono, w jaki sposób obsługa platformy Spark Azure Cosmos DB jest używana do tworzenia systemu konserwacji predykcyjnej:

![Azure Cosmos DB obsługi platformy Spark w celu utworzenia systemu konserwacji predykcyjnej](./media/analytics-solution-architectures/predictive-maintenance-system.png)

Na poniższej ilustracji przedstawiono, w jaki sposób obsługa platformy Spark Azure Cosmos DB jest używana do kompilowania systemu diagnostycznego w czasie rzeczywistym:

![Azure Cosmos DB obsługi platformy Spark w celu utworzenia systemu diagnostycznego w czasie rzeczywistym](./media/analytics-solution-architectures/real-time-vehicle-diagnostic-system.png)

## <a name="gaming"></a>Gry

* Dzięki wbudowanej obsłudze platformy Spark Azure Cosmos DB umożliwia łatwe tworzenie, skalowanie i wdrażanie zaawansowanych modeli analizy i uczenia maszynowego w ciągu kilku minut w celu utworzenia najlepszej możliwego środowiska gier.

* Możesz analizować dane dotyczące odtwarzacza, zakupu i zachowania, aby utworzyć odpowiednie spersonalizowane oferty, aby osiągnąć wysoką szybkość konwersji.

* Za pomocą usługi Spark Machine Learning można analizować i uzyskiwać wgląd w dane telemetryczne gier. Można diagnozować i zapobiegać spowolnieniom czasów ładowania i problemów w grach.

Na poniższej ilustracji przedstawiono, w jaki sposób obsługa platformy Spark w Azure Cosmos DB jest używana w analizie gier:

![Obsługa platformy Spark Azure Cosmos DB w analizie gier](./media/analytics-solution-architectures/gaming-analytics.png)

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o zaletach Azure Cosmos DB, zobacz artykuł z [omówieniem](introduction.md) .
* [Wprowadzenie do interfejsu API Azure Cosmos DB MongoDB](mongodb-introduction.md)
* [Rozpocznij pracę z Azure Cosmos DB interfejs API Cassandra](cassandra-introduction.md)
* [Wprowadzenie do interfejsu API Azure Cosmos DB Gremlin](graph-introduction.md)
* [Rozpocznij pracę z Azure Cosmos DB interfejs API tabel](table-introduction.md)
