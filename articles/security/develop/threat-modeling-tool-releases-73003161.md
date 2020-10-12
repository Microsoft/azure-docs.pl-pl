---
title: Microsoft Threat Modeling Tool wersja 03/22/2020 — Azure
description: Dokumentowanie informacji o wersji narzędzia do modelowania zagrożeń 7.3.00316.1.
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 03/22/2020
ms.openlocfilehash: 39963e9dadca0ee0be7bb8ad0ebf824c287bfee0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317909"
---
# <a name="threat-modeling-tool-update-release-73003161---03222020"></a>Threat Modeling Tool Update Release 7.3.00316.1 — 03/22/2020

Wersja 7.3.00316.1 Microsoft Threat Modeling Tool (TMT) została wydana w marcu 22 2020 i zawiera następujące zmiany:

- Usprawnienia w zakresie ułatwień dostępu
- Poprawki błędów
- Nowa funkcja DiagramReader

## <a name="notable-bug-fixes"></a>Istotne poprawki błędów

### <a name="exporting-the-threat-list-to-csv"></a>Eksportowanie listy zagrożeń do pliku CSV

Funkcja eksportowania do pliku CSV była niespójna, co umożliwia wyeksportowanie pól z listy zagrożeń. Teraz wszystkie pola z listy zagrożeń zostaną wyeksportowane do pliku CSV. 

### <a name="ux-bugs"></a>Usterki środowiska użytkownika

- Menu Pomoc w głównym przepływie pracy (Utwórz/Otwórz/Analizuj) i interfejs edytora szablonów mają teraz spójne opcje menu.
- Pasek wyszukiwania w okienku wzorników ma teraz standardowy kursor i dodano odpowiednie etykiety.

## <a name="new-features"></a>Nowe funkcje

### <a name="diagramreader-feature-has-been-added"></a>Dodano funkcję DiagramReader

W menu głównym dodano nową funkcję DiagramReader, podczas gdy model jest otwarty. Ta funkcja spowoduje przekonwertowanie graficznej reprezentacji modelu na tekstową narrację. 

## <a name="system-requirements"></a>Wymagania systemowe

- Obsługiwane systemu operacyjne:
  - [Rocznicowa Aktualizacja systemu Microsoft Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) lub nowsza wersja
- Wymagana wersja platformy .NET:
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) lub nowszy
- Wymagania dodatkowe:
  - Połączenie internetowe do otrzymywania aktualizacji narzędzia oraz szablonów

## <a name="documentation-and-feedback"></a>Dokumentacja i opinie

- Dokumentacja Threat Modeling Tool znajduje się w witrynie [docs.Microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)i zawiera informacje na [temat korzystania z narzędzia](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Następne kroki

Pobierz najnowszą wersję [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
