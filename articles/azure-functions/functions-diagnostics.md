---
title: Omówienie diagnostyki Azure Functions
description: Dowiedz się, jak rozwiązywać problemy z aplikacją funkcji za pomocą diagnostyki Azure Functions.
author: yunjchoi
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
ms.openlocfilehash: fb329273d442c023233fee2e7e01aabe5f5bff8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "83122373"
---
# <a name="azure-functions-diagnostics-overview"></a>Omówienie diagnostyki usługi Azure Functions

Gdy uruchamiasz aplikację funkcji, chcesz przygotować się do wszelkich problemów, które mogą wystąpić, z 4xx błędów, aby wyzwolić błędy. Azure Functions Diagnostics to inteligentne i interaktywne środowisko ułatwiające rozwiązywanie problemów z aplikacją funkcji bez konieczności konfigurowania ani ponoszenia dodatkowych kosztów. Gdy uruchamiasz problemy z aplikacją funkcji, Azure Functions Diagnostics wskazuje, co jest nieprawidłowe. Przeprowadzimy Cię do właściwych informacji, aby łatwiej i szybko rozwiązywać problemy oraz rozwiązać problem. W tym artykule przedstawiono podstawowe informacje dotyczące korzystania z narzędzia Azure Functions Diagnostics do szybszego diagnozowania i rozwiązywania typowych problemów z aplikacjami funkcji.

## <a name="start-azure-functions-diagnostics"></a>Uruchom diagnostykę Azure Functions

Aby rozpocząć diagnostykę Azure Functions:

1. Przejdź do aplikacji funkcji w [Azure Portal](https://portal.azure.com).
1. Wybierz pozycję **Diagnozuj i rozwiąż problemy** , aby otworzyć Azure Functions diagnostykę.
1. Wybierz kategorię, która najlepiej opisuje problem z aplikacją funkcji przy użyciu słów kluczowych na kafelku strony głównej. Możesz również wpisać słowo kluczowe, które najlepiej opisuje Twój problem na pasku wyszukiwania. Można na przykład wpisać `execution` listę raportów diagnostycznych związanych z wykonywaniem aplikacji funkcji i otworzyć je bezpośrednio z poziomu strony głównej.

   :::image type="content" source="./media/functions-diagnostics/functions-app-search-azure-functions-diagnostics.png" alt-text="Wyszukaj Azure Functions diagnostykę." border="true":::

## <a name="use-the-interactive-interface"></a>Korzystanie z interfejsu interakcyjnego

Po wybraniu kategorii głównej najlepiej dopasowanej do problemu z aplikacją funkcji, Azure Functions interfejs interaktywny diagnostyki o nazwie Genie, może przeprowadzić Cię przez diagnozowanie i rozwiązywanie problemów z aplikacją. Aby wyświetlić pełny raport diagnostyczny dotyczącej interesującej kategorii problemu, możesz skorzystać z skrótów dostępnych w genie. Skróty kafelków zapewniają bezpośredni sposób uzyskiwania dostępu do metryk diagnostycznych.

:::image type="content" source="./media/functions-diagnostics/genie.png" alt-text="Genie jest interfejsem diagnostycznym &quot;Azure Functions&quot;." border="false":::

Po wybraniu kafelka zobaczysz listę tematów związanych z problemem opisanym w kafelku. Te tematy zawierają fragmenty istotnych informacji z pełnego raportu. Wybierz dowolny z tych tematów, aby dokładniej zbadać problemy. Ponadto możesz wybrać opcję **Wyświetl pełny raport** , aby poznać wszystkie tematy na jednej stronie.

:::image type="content" source="./media/functions-diagnostics/preview-of-diagnostic-report.png" alt-text="Podgląd raportu diagnostycznego" border="false":::

## <a name="view-a-diagnostic-report"></a>Wyświetl raport diagnostyczny

Po wybraniu tematu można wyświetlić raport diagnostyczny specyficzny dla aplikacji funkcji. Raporty diagnostyczne używają ikon stanu, aby wskazać, czy występują pewne problemy z aplikacją. Zobaczysz szczegółowy opis problemu, zalecane akcje, powiązane z nią metryki i przydatne dokumenty. Niestandardowe raporty diagnostyczne są generowane na podstawie serii kontroli wykonywanych w aplikacji funkcji. Raporty diagnostyczne mogą być użytecznym narzędziem do lokalizowania problemów w aplikacji funkcji, a także do rozwiązywania problemu.

## <a name="find-the-problem-code"></a>Znajdź kod problemu

W przypadku funkcji opartych na skryptach można użyć **funkcji wykonywania i błędów** w obszarze **aplikacja funkcji lub raportowania błędów** , aby zawęzić wiersz kodu powodujący wyjątki lub błędy. Możesz użyć tego narzędzia do uzyskiwania dostępu do głównej przyczyny i rozwiązywania problemów z określonego wiersza kodu. Ta opcja jest niedostępna dla wstępnie skompilowanych funkcji języka C# i języka Java.

:::image type="content" source="./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png" alt-text="Raport diagnostyczny dotyczący błędów wykonania funkcji" border="false":::

:::image type="content" source="./media/functions-diagnostics/function-exception.png" alt-text="Widok szczegółów wyjątku." border="false":::

## <a name="next-steps"></a>Następne kroki

Możesz zadawać pytania lub przekazać opinię na temat Azure Functions diagnostyki w usłudze [UserVoice](https://feedback.azure.com/forums/355860-azure-functions). Dołącz `[Diag]` do tytułu swojej opinii.

> [!div class="nextstepaction"]
> [Monitorowanie aplikacji funkcji](functions-monitoring.md)
