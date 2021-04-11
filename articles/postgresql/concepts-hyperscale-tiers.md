---
title: Warstwa podstawowa — wersja zapoznawcza — Citus — Azure Database for PostgreSQL
description: Warstwa Podstawowa pojedynczego węzła dla Azure Database for PostgreSQL-Citus
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: c8cad5eb0983715a7cc7c18249243e93a2c498d7
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107024045"
---
# <a name="basic-tier-preview"></a>Warstwa Podstawowa (wersja zapoznawcza)

> [!IMPORTANT]
> Warstwa Podstawowa Citus (deskale) jest obecnie dostępna w wersji zapoznawczej.  Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
>
> Pełną listę innych nowych funkcji można znaleźć w artykule [dotyczącym funkcji Citus (wersja zapoznawcza)](hyperscale-preview-features.md).

Warstwa Podstawowa w Azure Database for PostgreSQL-Citus) to prosta metoda tworzenia małych grup serwerów, które można skalować później. Mimo że grupy serwerów w warstwie Standardowa mają węzeł koordynatora i co najmniej dwa węzły procesu roboczego, warstwa podstawowa uruchamia wszystko w jednym węźle bazy danych.

W przypadku korzystania z mniejszej liczby węzłów warstwa podstawowa ma wszystkie funkcje warstwy Standardowa. Podobnie jak w przypadku warstwy Standardowa, obsługuje wysoką dostępność, odczyt replik i kolumnowy magazyn tabel kolumnowych, między innymi.

## <a name="choosing-basic-vs-standard-tier"></a>Wybieranie warstwy Podstawowa vs Standard

Warstwa Podstawowa może być oszczędną i wygodną opcją wdrażania na potrzeby wstępnego tworzenia, testowania i ciągłej integracji. Używa ona jednego węzła bazy danych i prezentuje ten sam interfejs API SQL, co w przypadku warstwy Standardowa. Aplikacje można testować przy użyciu warstwy Podstawowa i później z [warstwy Standardowa](howto-hyperscale-scale-grow.md#add-worker-nodes) , bez obaw, że interfejs pozostaje taki sam.

Warstwa Podstawowa jest również odpowiednia w przypadku mniejszych obciążeń w środowisku produkcyjnym (gdy okazuje się, że wersja zapoznawcza jest ogólnie dostępna). Istnieje możliwość skalowania w pionie *w* warstwie Podstawowa przez zwiększenie liczby rdzeni wirtualnych serwera.

Gdy wymagana jest większa skala, użyj warstwy Standardowa. Najmniejsza dozwolona Grupa serwerów ma jeden węzeł koordynatora i dwóch procesów roboczych. Możesz użyć większej liczby węzłów w zależności od użytego przypadku użycia, zgodnie z opisem w opisie [wstępnego określania](howto-hyperscale-scale-initial.md) metody.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak przeprowadzić [obsługę administracyjną warstwy Podstawowa](quickstart-create-hyperscale-basic-tier.md)
* Gdy wszystko będzie gotowe, zapoznaj się z tematem [Jak przeprowadzić stopniowe skalowanie](howto-hyperscale-scale-grow.md#add-worker-nodes) z warstwy Podstawowa do warstwy Standardowa.
* Opcja [magazynu kolumnowego](concepts-hyperscale-columnar.md) jest dostępna zarówno w warstwie Podstawowa, jak i Standardowa
