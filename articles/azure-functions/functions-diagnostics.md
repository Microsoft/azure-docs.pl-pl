---
title: Omówienie diagnostyki Azure Functions
description: Dowiedz się, jak rozwiązywać problemy z aplikacją funkcji za pomocą diagnostyki Azure Functions.
author: yunjchoi
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
ms.openlocfilehash: 41acc5703f6b6e5cdeedf2afd0dc61b23bc579c6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75834048"
---
# <a name="azure-functions-diagnostics-overview"></a>Omówienie diagnostyki Azure Functions

Gdy uruchamiasz aplikację funkcji, chcesz przygotować się do wszelkich problemów, które mogą wystąpić, z 4xx błędów, aby wyzwolić błędy. Azure Functions Diagnostics to inteligentne i interaktywne środowisko ułatwiające rozwiązywanie problemów z aplikacją funkcji bez konieczności konfigurowania ani ponoszenia dodatkowych kosztów. Gdy uruchamiasz problemy z aplikacją funkcji, Azure Functions Diagnostics wskazuje, co jest niewłaściwe, aby przeprowadzić Cię do właściwych informacji w celu łatwiejszego i szybkiego rozwiązywania problemów i rozwiązania problemu. W tym artykule przedstawiono podstawowe informacje dotyczące korzystania z narzędzia Azure Functions Diagnostics do szybszego diagnozowania i rozwiązywania typowych problemów z aplikacjami funkcji.

## <a name="start-azure-functions-diagnostics"></a>Uruchom diagnostykę Azure Functions

Aby uzyskać dostęp do diagnostyki Azure Functions:

1. Przejdź do aplikacji funkcji w [Azure Portal](https://portal.azure.com).
2. Wybierz kartę **funkcje platformy** .
3. Wybierz opcję **Diagnozuj i rozwiąż problemy** w obszarze **Zarządzanie zasobami**, co spowoduje otwarcie Azure Functions diagnostyki.
4. Wybierz kategorię, która najlepiej opisuje problem z aplikacją funkcji przy użyciu słów kluczowych na kafelku strony głównej. Możesz również wpisać słowo kluczowe, które najlepiej opisuje Twój problem na pasku wyszukiwania. Można na przykład wpisać `execution` listę raportów diagnostycznych związanych z wykonywaniem aplikacji funkcji i otworzyć je bezpośrednio z poziomu strony głównej.

![Strona główna](./media/functions-diagnostics/homepage.png)

## <a name="use-the-interactive-interface"></a>Korzystanie z interfejsu interakcyjnego

Po wybraniu kategorii głównej najlepiej dopasowanej do problemu z aplikacją funkcji Azure Functions Diagnostyka "Interactive Interface Genie" może przeprowadzić Cię przez diagnozowanie i rozwiązywanie problemów z aplikacją. Aby wyświetlić pełny raport diagnostyczny dotyczącej interesującej kategorii problemu, możesz skorzystać z skrótów dostępnych w genie. Skróty kafelków zapewniają bezpośredni sposób uzyskiwania dostępu do metryk diagnostycznych.

![Genie](./media/functions-diagnostics/genie.png)

Po wybraniu kafelka zobaczysz listę tematów związanych z problemem opisanym w kafelku. Te tematy zawierają fragmenty istotnych informacji z pełnego raportu. Możesz wybrać dowolny z tych tematów, aby dokładniej zbadać problemy. Ponadto możesz wybrać opcję **Wyświetl pełny raport** , aby poznać wszystkie tematy na jednej stronie.

![Podgląd raportu diagnostycznego](./media/functions-diagnostics/preview-of-diagnostic-report.png)

## <a name="view-a-diagnostic-report"></a>Wyświetl raport diagnostyczny

Po wybraniu tematu można wyświetlić raport diagnostyczny specyficzny dla aplikacji funkcji. Raporty diagnostyczne używają ikon stanu, aby wskazać, czy występują pewne problemy z aplikacją. Zobaczysz szczegółowy opis problemu, zalecane akcje, powiązane z nią metryki i przydatne dokumenty. Niestandardowe raporty diagnostyczne są generowane na podstawie serii kontroli wykonywanych w aplikacji funkcji. Raporty diagnostyczne mogą być użytecznym narzędziem do lokalizowania problemów w aplikacji funkcji, a także do rozwiązywania problemu.

## <a name="find-the-problem-code"></a>Znajdź kod problemu

W przypadku funkcji opartych na skryptach można użyć **funkcji wykonywania i błędów** w obszarze **aplikacja funkcji lub raportowania błędów** , aby zawęzić wiersz kodu powodujący wyjątki lub błędy. Ta funkcja może być przydatnym narzędziem do uzyskiwania dostępu do głównej przyczyny i rozwiązywania problemów z określonym wierszem kodu. Ta opcja jest niedostępna dla wstępnie skompilowanych funkcji języka C# i języka Java.

![Raport diagnostyczny dotyczący błędów wykonania funkcji](./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png)

![Wyjątek funkcji](./media/functions-diagnostics/function-exception.png)

## <a name="next-steps"></a>Następne kroki

Możesz zadawać pytania lub przekazać opinię na temat Azure Functions diagnostyki w usłudze [UserVoice](https://feedback.azure.com/forums/355860-azure-functions). Dołącz `[Diag]` do tytułu swojej opinii.

> [!div class="nextstepaction"]
> [Monitorowanie aplikacji funkcji](functions-monitoring.md)
