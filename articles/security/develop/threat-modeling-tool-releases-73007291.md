---
title: Microsoft Threat Modeling Tool wersja 07/29/2020 — Azure
description: Dokumentowanie informacji o wersji narzędzia do modelowania zagrożeń 7.3.00729.1.
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 07/29/2020
ms.openlocfilehash: 3e6fcd52ad9cb6c127c14bac2f33223fb921519e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94516377"
---
# <a name="threat-modeling-tool-update-release-73007291---07292020"></a>Threat Modeling Tool Update Release 7.3.00729.1 — 07/29/2020

Wersja 7.3.00729.1 Microsoft Threat Modeling Tool (TMT) została wydana w lipcu 29 2020 i zawiera następujące zmiany:

- Poprawki błędów
 
## <a name="known-issues"></a>Znane problemy

### <a name="errors-related-to-tmt7application-file-deserialization"></a>Błędy związane z deserializacji pliku TMT7. Application

#### <a name="issue"></a>Problem

Niektórzy klienci zgłosili następujący komunikat o błędzie podczas pobierania Threat Modeling Tool:

```
The threat model file '$PATH\TMT7.application' could not be deserialized. File is not an actual threat model or the threat model may be corrupted.
```

Ten błąd występuje, ponieważ niektóre przeglądarki nie obsługują natywnie instalacji ClickOnce. W tych przypadkach plik aplikacji ClickOnce jest pobierany na dysk twardy użytkownika.

#### <a name="workaround"></a>Obejście

Ten błąd będzie nadal wyświetlany, jeśli Threat Modeling Tool zostanie uruchomiony przez dwukrotne kliknięcie pliku TMT7. Application. Jednak po pominięciu błędu narzędzie będzie działać normalnie. Zamiast uruchamiania Threat Modeling Tool przez dwukrotne kliknięcie pliku TMT7. Application, użytkownicy powinni używać skrótów utworzonych w menu systemu Windows podczas instalacji, aby uruchomić Threat Modeling Tool.

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