---
title: 'Szybki Start: Tworzenie grupy serwerów warstwy podstawowej — Citus) — Azure Database for PostgreSQL'
description: Zacznij korzystać z warstwy Podstawowa Azure Database for PostgreSQL Citus.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 04/07/2021
ms.openlocfilehash: 07ace217e5299662a1c3145a225abc25f4f1f337
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107024079"
---
# <a name="create-a-hyperscale-citus-basic-tier-server-group-in-the-azure-portal"></a>Utwórz grupę serwerów warstwy Podstawowa (Citus) w Azure Portal

Azure Database for PostgreSQL-Citus) to zarządzana usługa, która służy do uruchamiania i skalowania baz danych PostgreSQL o wysokiej dostępności w chmurze oraz zarządzania nimi. Jej [warstwa podstawowa](concepts-hyperscale-tiers.md) jest wygodną opcją wdrażania na potrzeby wstępnego tworzenia i testowania.

W tym przewodniku szybki start przedstawiono, jak utworzyć grupę serwerów warstwy Podstawowa (Citus) przy użyciu Azure Portal. Zostanie zainicjowana Grupa serwerów i zweryfikowanie, że można się z nią łączyć, aby uruchamiać zapytania.

> [!IMPORTANT]
> Warstwa Podstawowa Citus (deskale) jest obecnie dostępna w wersji zapoznawczej.  Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
>
> Pełną listę innych nowych funkcji można znaleźć w artykule [dotyczącym funkcji Citus (wersja zapoznawcza)](hyperscale-preview-features.md).

[!INCLUDE [azure-postgresql-hyperscale-create-basic-tier](../../includes/azure-postgresql-hyperscale-create-basic-tier.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start zawarto informacje na temat aprowizacji grupy serwerów z Citus. Nawiązano połączenie z usługą PSQL, utworzono schemat i dane rozproszone.

- Postępuj zgodnie z samouczkiem, aby [utworzyć skalowalne aplikacje z wieloma dzierżawcami](./tutorial-design-database-hyperscale-multi-tenant.md)
- Określ najlepszy [początkowy rozmiar](howto-hyperscale-scale-initial.md) grupy serwerów
