---
title: Model udostępnionych metadanych usługi Azure Synapse Analytics
description: Usługa Azure Synapse Analytics umożliwia różnym aparatom obliczeniowym obszaru roboczego udostępnianie baz danych i tabel między pulami platformy Spark (wersja zapoznawcza), aparatem SQL na żądanie (wersja zapoznawcza) i pule SQL.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: 3b26d516080961a482a3ba67f314e98ece4c9f24
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424118"
---
# <a name="azure-synapse-analytics-shared-metadata"></a>Udostępnione metadane usługi Azure Synapse Analytics

Usługa Azure Synapse Analytics umożliwia różnym aparatom obliczeniowym obszaru roboczego udostępnianie baz danych i tabel między pulami platformy Spark (wersja zapoznawcza), aparatem SQL na żądanie (wersja zapoznawcza) i pule SQL.

[!INCLUDE [preview](../includes/note-preview.md)]



Udostępnianie obsługuje tak zwany nowoczesny wzorzec magazynu danych i daje aparatom SQL obszar roboczy dostęp do baz danych i tabel utworzonych za pomocą platformy Spark. Umożliwia również aparaty SQL do tworzenia własnych obiektów, które nie są udostępniane innym aparatom.

## <a name="support-the-modern-data-warehouse"></a>Obsługa nowoczesnego magazynu danych

Model metadanych udostępnionych obsługuje nowoczesny wzorzec magazynu danych w następujący sposób:

1. Dane z usługi data lake są przygotowywane i skutecznie uporządkowane za pomocą platformy Spark, przechowując przygotowane dane w (prawdopodobnie podzielonych na partycje) tabelach opartych na parkietach zawartych w prawdopodobnie kilku bazach danych.

2. Spark utworzone bazy danych i wszystkie ich tabele stają się widoczne w dowolnym z obszaru roboczego Platformy Azure Synapse Spark wystąpień puli i mogą być używane z dowolnego zadania platformy Spark. Ta funkcja podlega [uprawnieniom,](#security-model-at-a-glance) ponieważ wszystkie pule platformy Spark w obszarze roboczym współużytkują ten sam podstawowy magazyn meta katalogu.

3. Spark utworzone bazy danych i ich tabele oparte na parkietach stają się widoczne w obszarze roboczym SQL na żądanie aparatu. [Bazy danych](database.md) są tworzone automatycznie w metadanych SQL na żądanie, a zarówno [tabele zewnętrzne,](table.md) jak i zarządzane utworzone przez `dbo` zadanie Platformy Spark są udostępniane jako tabele zewnętrzne w metadanych SQL na żądanie w schemacie odpowiedniej bazy danych. <!--For more details, see [ADD LINK].-->

4. Jeśli w obszarze roboczym występują wystąpienia puli SQL, w których jest włączona synchronizacja metadanych <!--[ADD LINK]--> lub jeśli zostanie utworzone nowe wystąpienie puli SQL z włączoną synchronizacją metadanych, bazy danych utworzone przez platformę Spark i ich tabele z bazą danych z bazą danych opartą na parkietach zostaną automatycznie mapowane do bazy danych puli SQL, zgodnie z opisem w [udostępnionej bazie danych usługi Azure Synapse Analytics.](database.md)

<!--[INSERT PICTURE]-->

<!--__Figure 1 -__ Supporting the Modern Data Warehouse Pattern with shared metadata-->

Synchronizacja obiektów odbywa się asynchronicznie. Obiekty będą miały niewielkie opóźnienie o kilka sekund, dopóki nie pojawią się w kontekście SQL. Po ich pojawieniu się, mogą być wyszukiwane, ale nie aktualizowane ani zmieniane przez aparaty SQL, które mają do nich dostęp.

## <a name="which-metadata-objects-are-shared"></a>Które obiekty metadanych są współużytkowane

Platforma Spark umożliwia tworzenie baz danych, tabel zewnętrznych, tabel zarządzanych i widoków. Ponieważ widoki platformy Spark wymagają aparatu platformy Spark do przetwarzania definiującej instrukcji Spark SQL i nie mogą być przetwarzane przez aparat SQL, tylko bazy danych i ich zawarte tabele zewnętrzne i zarządzane, które używają formatu magazynu parkietu są współużytkowane z aparatami SQL obszaru roboczego. Widoki platformy Spark są współużytkowane tylko przez wystąpienia puli platformy Spark.

## <a name="security-model-at-a-glance"></a>Model zabezpieczeń w skrócie

Spark baz danych i tabel, wraz z ich zsynchronizowane reprezentacje w aparatach SQL, są zabezpieczone na poziomie magazynu podstawowej. Gdy tabela jest wyszukiwana przez dowolny aparat, który ma prawo do użycia, podmiot zabezpieczeń przesyłający kwerendę jest przekazywany do plików źródłowych. Uprawnienia są sprawdzane na poziomie systemu plików.

Aby uzyskać więcej informacji, zobacz [Udostępniona baza danych usługi Azure Synapse Analytics](database.md).

## <a name="change-maintenance"></a>Zmiana konserwacji

Jeśli obiekt metadanych zostanie usunięty lub zmieniony za pomocą platformy Spark, zmiany są pobierane i propagowane do aparatu SQL na żądanie i pul SQL, które mają zsynchronizowane obiekty. Synchronizacja jest asynchronicznie i zmiany są odzwierciedlane w aparatach SQL po krótkim opóźnieniu.

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o udostępnionych bazach danych metadanych usługi Azure Synapse Analytics](database.md)
- [Dowiedz się więcej o udostępnionych tabelach metadanych usługi Azure Synapse Analytics](table.md)

