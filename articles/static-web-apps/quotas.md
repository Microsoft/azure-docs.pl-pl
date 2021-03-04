---
title: Przydziały w usłudze Azure static Web Apps Preview
description: Informacje o przydziałach skojarzonych z usługą Azure static Web Apps Preview
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 1bf9a2cf7af8b18994ef2473c11176b8aea74f6e
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042729"
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
| Autoryzacja<br><br>Przy użyciu ról niestandardowych i reguł routingu | Maksymalnie 25 zaproszonych i przypisanych ról użytkowników końcowych |
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
