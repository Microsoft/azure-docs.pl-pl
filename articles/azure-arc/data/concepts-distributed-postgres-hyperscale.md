---
title: Pojęcia dotyczące dystrybuowania danych i skalowania za pomocą grupy serwerów PostgreSQL z funkcją Arc
titleSuffix: Azure Arc enabled data services
description: Pojęcia dotyczące dystrybuowania danych za pomocą PostgreSQL
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: c01da4aed9e27296ea7b570420bb190b16749848
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90939782"
---
# <a name="concepts-for-distributing-data-with-arc-enabled-postgresql-hyperscale-server-group"></a>Pojęcia dotyczące dystrybuowania danych za pomocą PostgreSQL

W tym artykule wyjaśniono kluczowe pojęcia, które są ważne, aby korzystać z funkcji wieloskalowania usługi Azure Arc PostgreSQL.
Artykuły połączone poniżej wskazują koncepcje wyjaśnione dla Azure Database for PostgreSQL Citus. Jest to ta sama technologia co usługa Azure ARC z włączonym skalowaniem PostgreSQL, dzięki czemu te same koncepcje i perspektywy mają zastosowanie.

**Jaka jest różnica między nimi?**
- _Hiperskala usługi Azure Database for PostgreSQL (Citus)_

Jest to współczynnik formularza ze skalowaniem dla aparatu bazy danych Postgres, który jest dostępny jako usługa na platformie Azure (PaaS). Jest on obsługiwany przez rozszerzenie Citus, które umożliwia korzystanie z funkcji skalowania w górę. W tym formularzu usługa jest uruchamiana w centrach danych firmy Microsoft i jest obsługiwana przez firmę Microsoft.

- _Usługa Azure ARC z włączonym skalowaniem PostgreSQL_

Jest to współczynnik formularza ze skalowaniem dla aparatu bazy danych Postgres, który jest dostępny z usługą Azure ARC z obsługą danych. W tym celu nasz klienci udostępniają infrastrukturę, która hostuje systemy i obsługują je.

Kluczowe koncepcje dotyczące usługi Azure ARC z włączonym skalowaniem PostgreSQL są zestawione poniżej:

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="nodes-and-tables"></a>Węzły i tabele
Ważne jest, aby poznać następujące koncepcje, które najlepiej wykorzystać zalety platformy Azure z włączonym skalowaniem Postgres:
- Wyspecjalizowane węzły Postgres w usłudze Azure ARC z włączonym skalowaniem PostgreSQL: Koordynator i procesy robocze
- Typy tabel: tabele rozproszone, tabele referencyjne i tabele lokalne
- Fragmentów

Więcej informacji znajduje [się w węzłach i tabelach w Azure Database for PostgreSQL — funkcja Citus)](../../postgresql/concepts-hyperscale-nodes.md). 

## <a name="determine-the-application-type"></a>Określanie typu aplikacji
Jasno identyfikujący typ kompilowanej aplikacji jest istotny. Dlaczego? Ponieważ uruchamianie wydajnych zapytań na platformie Azure z włączoną obsługą PostgreSQL na serwerze wieloskalowania wymaga, aby tabele były prawidłowo dystrybuowane na różnych serwerach. Zalecana dystrybucja jest różna w zależności od typu aplikacji i jej wzorców zapytań. Istnieją rozległie dwa rodzaje aplikacji, które dobrze działają na platformie Azure z włączonym skalowaniem Postgres:
- Aplikacje z wieloma dzierżawcami
- Aplikacje działające w czasie rzeczywistym

Pierwszym krokiem modelowania danych jest zidentyfikowanie, który z nich jest bardziej zbliżony do Twojej aplikacji.

Zobacz szczegóły podczas [określania typu aplikacji](../../postgresql/concepts-hyperscale-app-type.md).


## <a name="choose-a-distribution-column"></a>Wybieranie kolumny dystrybucji
Dlaczego warto wybrać rozproszoną kolumnę?

Jest to jedna z najważniejszych decyzji modelowania, które należy podjąć. Usługa Azure ARC z włączonym funkcją PostgreSQL umożliwia przechowywanie wierszy w fragmentów na podstawie wartości kolumny dystrybucji wierszy. Poprawne grupy wyboru są powiązane z danymi w tych samych węzłach fizycznych, co sprawia, że zapytania są szybko dodawane i obsługują wszystkie funkcje SQL. Niepoprawny wybór sprawia, że system działa wolno i nie obsługuje wszystkich funkcji SQL w różnych węzłach. Ten artykuł zawiera wskazówki dotyczące kolumn dystrybucji dla dwóch najpopularniejszych scenariuszy.

Zobacz szczegóły w obszarze [Wybieranie kolumn dystrybucji](../../postgresql/concepts-hyperscale-choose-distribution-column.md).


## <a name="table-colocation"></a>Kolokacja tabeli

Wspólna lokalizacja polega na przechowywaniu powiązanych informacji w tych samych węzłach. Zapytania mogą szybko przechodzić, gdy wszystkie niezbędne dane są dostępne bez żadnego ruchu sieciowego. Współlokalizowanie powiązanych danych w różnych węzłach pozwala wydajnie uruchamiać zapytania w każdym węźle.

Zobacz szczegóły w obszarze wspólnej [lokalizacji tabeli](../../postgresql/concepts-hyperscale-colocation.md).


## <a name="next-steps"></a>Następne kroki
- [Przeczytaj o tworzeniu PostgreSQL](create-postgresql-hyperscale-server-group.md)
- [Przeczytaj informacje na temat skalowania grup serwerów PostgreSQL na platformie Azure z włączonym łukiem](scale-out-postgresql-hyperscale-server-group.md)
- [Przeczytaj informacje o usłudze Azure ARC z włączonym Data Services](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
- [Przeczytaj informacje o usłudze Azure Arc](https://aka.ms/azurearc)

