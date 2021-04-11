---
title: Przydziały w usłudze Azure static Web Apps Preview
description: Informacje o przydziałach skojarzonych z usługą Azure static Web Apps Preview
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: e3538e90a6dea69c703f56871fde86a18557a022
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106095173"
---
# <a name="quotas-in-azure-static-web-apps-preview"></a>Przydziały w usłudze Azure static Web Apps Preview

Dla usługi Azure static Web Apps w wersji zapoznawczej istnieją następujące przydziały.

> [!IMPORTANT]
> Usługa Azure static Web Apps jest w publicznej wersji zapoznawczej i nie jest przeznaczona do użycia w środowisku produkcyjnym.

| Cecha                     | Bezpłatny plan        |
|-----------------------------|------------------|
| Uwzględniona przepustowość          | 100 GB miesięcznie |
| Przepustowość nadwyżkowa           | Niedostępny      |
| Aplikacje na subskrypcję platformy Azure | 10               |
| Rozmiar aplikacji                    | 250 MB           |
| Środowiska przedprodukcyjne | 3                |
| Niestandardowe domeny              | 1                |
| Autoryzacja (z rolami niestandardowymi i regułami routingu) | Maksymalnie 25 użytkowników końcowych, którzy mogą należeć do ról niestandardowych |
| Azure Functions             | Dostępne        |
| Umowa SLA                         | Brak             |

## <a name="github-storage"></a>Magazyn GitHub

Akcje i pakiety usługi GitHub korzystają z magazynu usługi GitHub, który ma swój własny zestaw przydziałów. Po utworzeniu witryny usługi Azure static Web Apps w serwisie GitHub są przechowywane artefakty witryny nawet po wdrożeniu.

Więcej szczegółów można znaleźć w następujących zasobach:

- [Zarządzanie miejscem do magazynowania akcji](https://github.community/t5/GitHub-Actions/Managing-Actions-storage-space/td-p/38944)
- [Informacje o rozliczeniach dla akcji usługi GitHub](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/about-billing-for-github-actions#about-billing-for-github-actions)
- [Zarządzanie limitem wydatków dla akcji usługi GitHub](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/managing-your-spending-limit-for-github-actions)

## <a name="next-steps"></a>Następne kroki

- [Omówienie](overview.md)
