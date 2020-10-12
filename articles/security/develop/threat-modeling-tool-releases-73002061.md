---
title: Microsoft Threat Modeling Tool wersja 02/11/2020 — Azure
description: Dokumentowanie informacji o wersji narzędzia do modelowania zagrożeń 7.3.00206.1.
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: fa89ffcfd37629f95fbf281a6a703a173acd5d48
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317926"
---
# <a name="threat-modeling-tool-update-release-73002061---02112020"></a>Threat Modeling Tool Update Release 7.3.00206.1 — 02/11/2020

Wersja 7.3.00206.1 Microsoft Threat Modeling Tool (TMT) została wydana w lutym 11 2020 i zawiera następujące zmiany:

- Poprawki błędów

## <a name="notable-bug-fixes"></a>Istotne poprawki błędów

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>Błędy związane z wartościami priorytetu spoza oczekiwanych zakresów

Niektórzy klienci zgłosili następujący komunikat o błędzie podczas otwierania plików utworzonych w szablonach "Threat Modeling Tool 2016" lub szablonów niestandardowych:

```output
System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()

System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)
```

Ten problem został rozwiązany w tej wersji.

## <a name="system-requirements"></a>Wymagania systemowe

- Obsługiwane systemy operacyjne
  - [Rocznicowa Aktualizacja systemu Microsoft Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) lub nowsza wersja
- Wymagana wersja platformy .NET
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) lub nowszy
- Wymagania dodatkowe
  - Do otrzymywania aktualizacji narzędzia oraz szablonów są wymagane połączenia z Internetem.

## <a name="documentation-and-feedback"></a>Dokumentacja i opinie

- Dokumentacja Threat Modeling Tool znajduje się w witrynie [docs.Microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)i zawiera informacje na [temat korzystania z narzędzia](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Następne kroki

Pobierz najnowszą wersję [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
