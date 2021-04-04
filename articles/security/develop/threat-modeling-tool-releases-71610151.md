---
title: Microsoft Threat Modeling Tool wersja 10/16/2019 — Azure
description: Dokumentowanie informacji o wersji narzędzia do modelowania zagrożeń 7.1.61015.1.
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 10/16/2019
ms.openlocfilehash: 761ad45496e799a555b60480ff575b9d8f30e984
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94516989"
---
# <a name="threat-modeling-tool-update-release-71610151---10162019"></a>Threat Modeling Tool, wersja aktualizacyjna 7.1.61015.1 — 2019-10-16

Wersja 7.1.61015.1 Microsoft Threat Modeling Tool (TMT) została wydana w październiku 16 2019 i zawiera następujące zmiany:

- Usprawnienia w zakresie ułatwień dostępu
- Poprawki błędów
- Nowe wzorniki dla Azure Logic Apps i platformy Azure Eksplorator danych

## <a name="notable-bug-fixes"></a>Istotne poprawki błędów

### <a name="improved-backward-compatibility-with-files-created-in-threat-modeling-tool-2016"></a>Zwiększona zgodność z poprzednimi wersjami z plikami utworzonymi w "Threat Modeling Tool 2016"

Rozwiązano kilka usterek związanych z otwieraniem lub wyświetlaniem plików modelu zagrożeń utworzonych w "Threat Modeling Tool 2016".

## <a name="feature-enhancements"></a>Udoskonalenia funkcji

### <a name="new-stencils-for-azure-logic-apps-and-azure-data-explorer"></a>Nowe wzorniki dla Azure Logic Apps i platformy Azure Eksplorator danych

Do wzornika platformy Azure dodano nowe wzorniki dla Azure Logic Apps i Eksplorator danych platformy Azure, a także powiązane z nimi zagrożenia i środki zaradcze.

![Azure Logic Apps i wzorniki Eksplorator danych platformy Azure](./media/threat-modeling-tool-releases-71610151/tmt-logic-apps.png)

## <a name="known-issues"></a>Znane problemy

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>Błędy związane z wartościami priorytetu spoza oczekiwanych zakresów

Niektórzy klienci zgłosili następujący komunikat o błędzie podczas otwierania plików utworzonych w szablonach "Threat Modeling Tool 2016" lub szablonów niestandardowych:

```output
System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()

System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)
```

Ten problem jest objęty badaniem

## <a name="system-requirements"></a>Wymagania systemowe

- Obsługiwane systemy operacyjne
  - [Rocznicowa Aktualizacja systemu Microsoft Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) lub nowsza wersja
- Wymagana wersja platformy .NET
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) lub nowszy
- Wymagania dodatkowe
  - Do otrzymywania aktualizacji narzędzia oraz szablonów są wymagane połączenia z Internetem.

## <a name="documentation-and-feedback"></a>Dokumentacja i opinie

- Dokumentacja Threat Modeling Tool znajduje się w witrynie [docs.Microsoft.com](./threat-modeling-tool.md)i zawiera informacje na [temat korzystania z narzędzia](./threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Następne kroki

Pobierz najnowszą wersję [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).