---
title: Microsoft Threat Modeling Tool wersja 1/29/2019
titleSuffix: Azure
description: Zapoznaj się z informacjami o wersji Microsoft Threat Modeling Tool wydanych w dniu 1/29/2019. Uwagi obejmują zmiany funkcji i znane problemy.
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 01/25/2019
ms.openlocfilehash: 17147d412bd888cdd3cd270829ad6d6103867b34
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87539054"
---
# <a name="threat-modeling-tool-update-release-71601261---1292019"></a>Threat Modeling Tool, wersja aktualizacyjna 7.1.60126.1 — 2019-01-29

Wersja 7.1.60126.1 Microsoft Threat Modeling Tool została wydana w styczniu 29 2019 i zawiera następujące zmiany:

- Minimalna wymagana wersja platformy .NET została zwiększona do [programu .NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262).
- Minimalna wymagana wersja systemu Windows została zwiększona do [rocznicowej aktualizacji systemu Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) ze względu na zależność programu .NET.
- Funkcja przełączania walidacji modelu została dodana do menu Opcje narzędzia.
- Zaktualizowano kilka linków we właściwościach zagrożeń.
- Drobne zmiany środowiska użytkownika na ekranie głównym narzędzia.
- Threat Modeling Tool teraz dziedziczy ustawienia protokołu TLS systemu operacyjnego hosta i jest obsługiwane w środowiskach wymagających protokołu TLS 1,2 lub nowszego.

## <a name="feature-changes"></a>Zmiany funkcji

### <a name="model-validation-option"></a>Opcja walidacji modelu

Na podstawie opinii klientów dodano opcję do narzędzia w celu włączenia lub wyłączenia walidacji modelu. Wcześniej, jeśli szablon używał pojedynczego przepływu danych jednokierunkowe między dwoma obiektami, w ramce komunikatów może zostać wyświetlony komunikat o błędzie z informacją: obiekty Objectsname wymagają co najmniej jednego elementu "any". Wyłączenie walidacji modelu uniemożliwi wyświetlanie tych ostrzeżeń w widoku.

Opcja przełączania i wyłączania walidacji modelu znajduje się w menu >ustawienia >opcje. Wartość domyślna tego ustawienia jest wyłączona.

![Opcja walidacji modelu](./media/threat-modeling-tool-releases-71601261/tmt_model_validation_option.png)

## <a name="system-requirements"></a>Wymagania systemowe

- Obsługiwane systemy operacyjne
  - [Rocznicowa Aktualizacja systemu Microsoft Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) lub nowsza wersja
- Wymagana wersja platformy .NET
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) lub nowszy
- Wymagania dodatkowe
  - Do otrzymywania aktualizacji narzędzia oraz szablonów są wymagane połączenia z Internetem.

## <a name="known-issues"></a>Znane problemy

### <a name="unsupported-systems"></a>Nieobsługiwane systemy

#### <a name="issue"></a>Problem

Użytkownicy systemów Windows 10, którzy nie mogą zainstalować programu .NET 4.7.1 lub nowszego, na przykład Windows 10 Enterprise LTSB (wersja 1507), nie będą mogli otworzyć narzędzia po uaktualnieniu. Te starsze wersje systemu Windows nie są już obsługiwanymi platformami dla Threat Modeling Tool i nie powinny instalować najnowszych aktualizacji.

#### <a name="workaround"></a>Obejście

Użytkownicy systemu Windows 10 Enterprise LTSB (wersja 1507) z zainstalowaną najnowszą aktualizacją mogą przywrócić poprzednią wersję Threat Modeling Tool za pomocą okna dialogowego odinstalowywania w obszarze aplikacje & funkcje.

## <a name="documentation-and-feedback"></a>Dokumentacja i opinie

- Dokumentacja Threat Modeling Tool znajduje się w witrynie [docs.Microsoft.com](threat-modeling-tool.md)i zawiera informacje na [temat korzystania z narzędzia](threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Następne kroki

Pobierz najnowszą wersję [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
