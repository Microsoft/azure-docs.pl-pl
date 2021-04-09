---
title: Raport dotyczący użycia i szczegółowych informacji | Microsoft Docs
description: Wprowadzenie do raportu dotyczącego użycia i szczegółowych informacji w portalu Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 3fba300d-18fc-4355-9924-d8662f563a1f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 54bce5e839786862a6dac9aeb685dd364547a09a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98685036"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Raport dotyczący użycia i szczegółowych informacji w portalu Azure Active Directory

Korzystając z raportu Użycie i szczegółowe informacje, można uzyskać widok danych logowania skoncentrowany na aplikacjach. Odpowiedzi można znaleźć na następujących pytaniach:

*   Jakie są najczęściej używane aplikacje w mojej organizacji?
*   Jakie aplikacje mają najwięcej nieudanych logowań? 
*   Jakie są najważniejsze błędy logowania dla każdej aplikacji?

## <a name="prerequisites"></a>Wymagania wstępne 

Aby uzyskać dostęp do danych z raportu Użycie i szczegółowe informacje, potrzebne są:

* Dzierżawa usługi Azure AD
* Licencja usługi Azure AD Premium (P1/P2) do wyświetlania danych logowania
* Użytkownik w rolach Administrator globalny, administrator zabezpieczeń, czytelnik zabezpieczeń lub czytelnik raportów. Ponadto wszyscy użytkownicy (inni niż administratorzy) mogą uzyskać dostęp do swoich logowań. 

## <a name="access-the-usage-and-insights-report"></a>Dostęp do raportu dotyczącego użycia i szczegółowych informacji

1. Przejdź do [Azure Portal](https://portal.azure.com).
2. Wybierz odpowiedni katalog, a następnie wybierz pozycję **Azure Active Directory** i wybierz pozycję **aplikacje dla przedsiębiorstw**.
3. W sekcji **działanie** wybierz pozycję **użycie & szczegółowych** informacji, aby otworzyć raport. 

![Zrzut ekranu przedstawia użycie & szczegółowych informacji w sekcji działanie.](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>Korzystanie z raportu

Raport użycie i wgląd w szczegółowe dane przedstawia listę aplikacji z co najmniej jedną próbą logowania, a także umożliwia posortowanie według liczby pomyślnych logowań, niepowodzeń logowania i częstotliwości sukcesów.

Kliknięcie przycisku **Załaduj więcej** u dołu listy umożliwia wyświetlenie dodatkowych aplikacji na stronie. Możesz wybrać zakres dat, aby wyświetlić wszystkie aplikacje, które zostały użyte w zakresie.

![Zrzut ekranu przedstawia użycie & szczegółowych informacji na temat działania aplikacji, w którym można wybrać zakres i wyświetlić działania związane z logowaniem dla różnych aplikacji.](./media/concept-usage-insights-report/usage-and-insights-report.png)

Możesz również ustawić fokus dla określonej aplikacji. Wybierz pozycję **Wyświetl działanie związane z logowaniem** , aby zobaczyć aktywność logowania w czasie dla aplikacji, a także najważniejszych błędów.  

Po wybraniu dnia na wykresie użycia aplikacji zostanie wykorzystana Szczegółowa lista działań związanych z logowaniem dla aplikacji.  

:::image type="content" source="./media/concept-usage-insights-report/usage-and-insights-application-report.png" alt-text="Zrzut ekranu przedstawia użycie & szczegółowych informacji dotyczących konkretnej aplikacji, w której można zobaczyć Graf działania związanego z logowaniem.":::

## <a name="next-steps"></a>Następne kroki

* [Raport dotyczący logowań](concept-sign-ins.md)
