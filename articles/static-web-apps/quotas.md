---
title: Przydziały w usłudze Azure static Web Apps Preview
description: Informacje o przydziałach skojarzonych z usługą Azure static Web Apps Preview
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 6fa70eba5875c32d41fa1cfc8daf9b1cdf8f19df
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "83599991"
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
| Rozmiar aplikacji                    | 100 MB           |
| Środowiska przedprodukcyjne | 1                |
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
