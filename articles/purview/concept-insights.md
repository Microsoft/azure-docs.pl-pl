---
title: Zrozumienie raportów szczegółowych informacji na platformie Azure kontrolą
description: W tym artykule wyjaśniono, co szczegółowe informacje znajdują się w usłudze Azure kontrolą.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: d841fa336b2702a02f3215f97a6403217986d7e0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96554760"
---
# <a name="understand-insights-in-azure-purview"></a>Opis analizy w usłudze Azure Purview

Ten artykuł zawiera omówienie funkcji Insights w usłudze Azure kontrolą.

Szczegółowe informacje są jednym z głównych filarów kontrolą. Ta funkcja udostępnia klientom, pojedyncze okienko szkła w wykazie, a także zapewnia szczegółowe informacje o administratorach źródła danych, użytkownikach, stewardsch danych, oficerach danych i administratorach zabezpieczeń. Obecnie kontrolą ma następujące raporty usługi Insights, które będą dostępne dla klientów w publicznej wersji zapoznawczej.

## <a name="asset-insights"></a>Szczegółowe informacje o zasobach

Ten raport zawiera widok oka z danymi, a także jego rozkład według typu źródła, według klasyfikacji i rozmiaru pliku jako niektórych wymiarów. Ten raport uwzględnia różne typy użytkowników, którzy mogą zarządzać kontem kontrolą i uruchamiać skanowania lub użytkowników firm, którzy mogą chcieć wiedzieć, ile zasobów istnieje w ramach określonej klasyfikacji w ramach danej organizacji. 

Raport zawiera szczegółowe informacje za pomocą wykresów i wskaźników KPI, a później głębokie szczegółowe w określonych anomaliach, takich jak pliki o nieprawidłowych miejscach. Raport obsługuje również kompleksową obsługę klienta, w której klient może wyświetlić liczbę zasobów z określoną klasyfikacją, może podzielić informacje według typów źródłowych i folderów głównych, a także wyświetlić listę zasobów do dalszej analizy.

## <a name="scan-insights"></a>Skanuj szczegółowe dane

Raport umożliwia administratorom zrozumienie ogólnej kondycji skanów — liczba zakończonych sukcesem, liczba zakończonych niepowodzeniem, liczba anulowanych operacji. Ten raport zawiera aktualizacje stanu dotyczące skanowania, które zostały wykonane na koncie kontrolą w okresie ostatnich siedmiu dni lub ostatnich 30 dni.

Raport umożliwia także administratorom głębokie szczegółowe i eksplorowanie, które skanowania nie powiodły się i dla konkretnych typów źródła. Aby umożliwić użytkownikom badanie, raport ułatwia przechodzenie do strony historia skanowania w ramach środowiska "źródła".

## <a name="glossary-insights"></a>Szczegółowe informacje na temat słownika

Ten raport daje użytkownikom biznesowym i dane Stewards raport o stanie na słowniku. Użytkownicy mogą wyświetlać ten raport, aby zrozumieć dystrybucję terminów słownika według stanu, dowiedzieć się, ile terminów słownika jest dołączonych do zasobów i ile nie jest jeszcze dołączonych do żadnego elementu zawartości. Użytkownicy biznesowi mogą również poznać kompletność swoich terminów słownika. 

Ten raport podsumowuje najważniejsze elementy, na które chce się skoncentrować użytkownik biznesowy lub Steward danych, aby utworzyć kompletny i przydatny słownik dla swojej organizacji. Użytkownicy mogą również przechodzić do środowiska "słownik" ze środowiska "słownik szczegółowych informacji", aby wprowadzić zmiany w określonym terminie słownika.

## <a name="classification-insights"></a>Szczegółowe informacje o klasyfikacji

Ten raport zawiera szczegółowe informacje o tym, gdzie znajdują się sklasyfikowane dane, klasyfikacje znalezione podczas skanowania i rozwijające się do samych sklasyfikowanych plików. Umożliwia ona administratorom zabezpieczeń zrozumienie typów informacji znajdujących się w danej organizacji. 

W usłudze Azure kontrolą klasyfikacje są podobne do tagów podmiotu i służą do oznaczania i identyfikowania zawartości określonego typu w obszarze danych.

Użyj raportu informacje o klasyfikacji, aby zidentyfikować zawartość z określonymi klasyfikacjami i zrozumieć wymagane akcje, takie jak dodanie dodatkowych zabezpieczeń do repozytoriów lub przeniesienie zawartości do bezpieczniejszej lokalizacji.

Aby uzyskać więcej informacji, zobacz temat [Klasyfikacja informacji o danych z usługi Azure kontrolą](classification-insights.md).

## <a name="sensitivity-labeling-insights"></a>Szczegółowe informacje o etykietowaniu

Ten raport zawiera szczegółowe informacje o etykietach poufności znalezionych podczas skanowania, a także o tym, jak również przechodzenie do plików z etykietą. Umożliwia ona administratorom zabezpieczeń zapewnienie bezpieczeństwa informacji znajdujących się w danych organizacji. 

W usłudze Azure kontrolą etykiety czułości są używane do identyfikowania kategorii typów klasyfikacji oraz zasad zabezpieczeń grup, które mają być stosowane do poszczególnych kategorii.

Raport dotyczący etykiet szczegółowych informacji umożliwia zidentyfikowanie etykiet czułości znalezionych w treści i zrozumienie wymaganych akcji, takich jak zarządzanie dostępem do określonych repozytoriów lub plików.

Aby uzyskać więcej informacji, zobacz [Informacje o czułości dotyczące danych w usłudze Azure kontrolą](sensitivity-insights.md).

## <a name="file-extension-insights"></a>Szczegółowe informacje o rozszerzeniu pliku

Ten raport zawiera szczegółowe informacje o rozszerzeniach plików lub typach plików, które znajdują się podczas skanowania, a także do szczegółów samych plików. 

Za pomocą raportu szczegółowe informacje o rozszerzeniu pliku można zrozumieć, ile plików każdego z nich ma miejsce, gdzie te pliki są, oraz czy są scannable do poufnych danych.

Aby uzyskać więcej informacji, zobacz temat [rozszerzenie pliku Insights na temat danych z usługi Azure kontrolą](file-extension-insights.md).

## <a name="next-steps"></a>Następne kroki

* [Szczegółowe informacje na temat słownika](glossary-insights.md)
* [Skanuj szczegółowe dane](scan-insights.md)
* [Szczegółowe informacje o klasyfikacji](./classification-insights.md)