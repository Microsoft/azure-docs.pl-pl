---
title: Rozwiązywanie problemów z analizatorem internetowym platformy Azure | Microsoft Docs
description: Informacje dotyczące rozwiązywania problemów z usługą Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: a265278652c16b4682707470d183a02a55b9a0ec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77069221"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Rozwiązywanie problemów z analizatorem internetowym platformy Azure

Ten artykuł zawiera kroki rozwiązywania problemów dotyczących typowych problemów z analizatorem internetowym.

## <a name="things-to-keep-in-mind"></a>Rzeczy, o których należy pamiętać
- Skrypt klienta musi być osadzony w witrynie sieci Web **https** . Pomiary nie będą zbierane, jeśli skrypt zostanie uruchomiony w witrynie sieci Web (**http://**) lub lokalnie (**File://**).
- Dane pomiarowe będą zbierane tylko wtedy, gdy skrypt klienta profilu analizatora internetowego został osadzony w aplikacji, która odbiera rzeczywisty ruch użytkownika. Ruch syntetyczny (na przykład testy wydajności WebApp platformy Azure) nie zazwyczaj wykonuje osadzony kod JavaScript, więc żadne pomiary nie będą generowane przez ten typ ruchu.

## <a name="azure-portal"></a>Azure Portal
**"Karta wyników nie została wygenerowana dla wybranej kombinacji filtru" w sekcji kart wyników**
- Karty wyników są generowane codziennie (na koniec każdego dnia, czasu UTC).
- Karty wyników są generowane tylko wtedy, gdy zebrano ponad 100 pomiarów dla wybranej kombinacji filtru (test, okres, kraj itp.).

**"Całkowita liczba pomiarów" wynosi zero dla jednego lub obu punktów końcowych w teście**
- Liczba szeregów czasowych i pomiarowych jest obliczana raz na godzinę, więc musisz poczekać co najmniej przez czas na wyświetlenie nowych danych pomiarów.
- Analizator Internetu zlicza pomyślne pomiary (tj. odpowiedzi HTTP 200) na potrzeby analizy. Jeśli jeden lub oba punkty końcowe w teście są nieosiągalne lub zwracają kod HTTP inny niż 200, będą wyświetlane z zerowymi pomiarami.

## <a name="next-steps"></a>Następne kroki
Przeczytaj [często zadawane pytania dotyczące narzędzia Internet Analyzer](internet-analyzer-faq.md)
