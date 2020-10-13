---
title: plik dołączany
description: plik dołączany
services: search
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
ms.date: 05/11/2020
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: fe5c8129434ddb4eec2dd25a3f123f28b4db221b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83682613"
---
Usługa wyszukiwania jest ograniczona przez miejsce na dysku lub przez stały limit maksymalnej liczby indeksów lub indeksatorów, w zależności od tego, co nastąpi wcześniej. W poniższej tabeli przedstawiono limity magazynu. Aby uzyskać maksymalne limity obiektów, zobacz [ograniczenia według zasobu](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Zasób | Bezpłatna | Podstawowa<sup>1</sup> | S1 | S2 | S3 | &nbsp;Wyjście S3 HD | L1 | L2 |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Umowa dotycząca poziomu usług (SLA)<sup>2</sup>  |Nie |Tak |Tak |Tak |Tak |Tak |Tak |Tak |
| Magazyn na partycję |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Partycje na usługę |Nie dotyczy |1 |12 |12 |12 |3 |12 |12 |
| Rozmiar partycji |Nie dotyczy |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Repliki |Nie dotyczy |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> podstawowa ma jedną stałą partycję. Dodatkowe jednostki wyszukiwania mogą służyć do dodawania replik dla większych woluminów zapytań.

<sup>2</sup> umowy dotyczące poziomu usług są stosowane w przypadku usług rozliczanych w dedykowanych zasobach. Bezpłatne usługi i funkcje wersji zapoznawczej nie mają umowy SLA. W przypadku usług rozliczanych umowy SLA zacznie obowiązywać po wprowadzeniu wystarczającej nadmiarowości dla usługi. Co najmniej dwie repliki są wymagane dla zapytania (Read) umowy SLA. Co najmniej trzy repliki są wymagane do wykonywania zapytań i indeksowania (do odczytu i zapisu) umowy SLA. Liczba partycji nie jest objęta umową SLA. 