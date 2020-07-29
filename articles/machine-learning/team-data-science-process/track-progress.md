---
title: Śledzenie postępu projektów procesów naukowych dotyczących danych zespołu
description: Jak Menedżerowie grupy analizy danych, liderzy zespołu i potencjalni klienci projektu mogą śledzić postęp projektu analizy danych.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 8cf1e5a4d97b882e7a8d0c81041bbcde709760d1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75864200"
---
# <a name="track-the-progress-of-data-science-projects"></a>Śledzenie postępu projektów analizy danych

Menedżerowie grupy analizy danych, liderzy zespołu i potencjalni klienci projektu mogą śledzić postęp swoich projektów.  Menedżerowie chcą wiedzieć, jakie prace zostały wykonane, kto pracował i ile pracy pozostało.   Zarządzanie oczekiwaniami jest ważnym elementem sukcesu.

## <a name="azure-devops-dashboards"></a>Pulpity nawigacyjne usługi Azure DevOps

Jeśli używasz usługi Azure DevOps, możesz tworzyć pulpity nawigacyjne w celu śledzenia działań i elementów roboczych skojarzonych z danym projektem Agile. Aby uzyskać więcej informacji na temat pulpitów nawigacyjnych, zobacz [pulpity nawigacyjne, raporty i widżety](/azure/devops/report/dashboards/).

Aby uzyskać instrukcje dotyczące sposobu tworzenia i dostosowywania pulpitów nawigacyjnych i widżetów w usłudze Azure DevOps, zobacz następujące przewodniki szybki start:

- [Dodawanie pulpitów nawigacyjnych i zarządzanie nimi](/azure/devops/report/dashboards/dashboards)
- [Dodawanie widżetów do pulpitu nawigacyjnego](/azure/devops/report/dashboards/add-widget-to-dashboard)

## <a name="example-dashboard"></a>Przykładowy pulpit nawigacyjny

Oto prosty przykładowy pulpit nawigacyjny, który śledzi działania przebiegu projektu analizy danych Agile, łącznie z liczbą zatwierdzeń skojarzonych z repozytoriami. 

- Kafelek **odliczanie** pokazuje liczbę dni, które pozostaną w bieżącym przebiegu. 

- Dwa **kafelki kodu** pokazują liczbę zatwierdzeń w dwóch repozytoriach projektu dla ostatnich siedmiu dni. 

- **Elementy robocze dla projektu klienta przetwarzania TDSP** przedstawia wyniki zapytania dla wszystkich elementów roboczych i ich stanu. 

- **Skumulowany Diagram przepływu** (CFD) pokazuje liczbę zamkniętych i aktywnych elementów roboczych.

- **Wykres postępu** pokazuje pracę w dalszym ciągu z pozostałą godziną w przebiegu.

- **Wykres burnup** pokazuje ukończoną pracę w porównaniu do łącznej ilości pracy w przebiegu.

![Pulpit nawigacyjny](./media/track-progress/dashboard.png)

## <a name="next-steps"></a>Następne kroki

[Instruktaże wykonujące analizy danych zespołowych](walkthroughs.md) — instruktaże pokazujące wszystkie etapy procesu. Połączone scenariusze ilustrują sposób zarządzania zasobami chmurowymi i lokalnymi w aplikacjach inteligentnych. 
