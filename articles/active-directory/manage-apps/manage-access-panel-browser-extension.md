---
title: Rozwiązywanie problemów z rozszerzeniem panelu dostępu platformy Azure dla programu IE | Microsoft Docs
description: Jak wdrożyć dodatek programu Internet Explorer dla portalu My Apps przy użyciu zasad grupy.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0269c87572e2a9242a54491103ae0fcc3637518
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "67723908"
---
# <a name="troubleshoot-the-access-panel-extension-for-internet-explorer"></a>Rozwiązywanie problemów z rozszerzeniem panelu dostępu dla programu Internet Explorer

Ten artykuł pomaga w rozwiązywaniu następujących problemów:

* Nie możesz uzyskać dostępu do aplikacji za pośrednictwem portalu My Apps podczas korzystania z programu Internet Explorer.
* Zobaczysz komunikat "Zainstaluj oprogramowanie", mimo że zostało już zainstalowane oprogramowanie.

Jeśli jesteś administratorem, zapoznaj [się z tematem jak wdrożyć rozszerzenie panelu dostępu dla programu Internet Explorer przy użyciu zasady grupy](deploy-access-panel-browser-extension.md).

## <a name="run-the-diagnostic-tool"></a>Uruchom narzędzie diagnostyczne

Problemy z instalacją można zdiagnozować za pomocą rozszerzenia panelu dostępu, pobierając i uruchamiając narzędzie diagnostyczne panelu dostępu. 

Aby pobrać i zainstalować narzędzie diagnostyczne:

1. [Wybierz ten link, aby pobrać narzędzie diagnostyczne.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
1. Otwórz plik i Wyodrębnij zawartość na komputerze.
1. Aby uruchomić narzędzie, kliknij prawym przyciskiem myszy plik o nazwie *AccessPanelExtensionDiagnosticTool. js* i wybierz polecenie **Otwórz za pomocą** > **hosta skryptów systemu Microsoft Windows**.

    ![Otwórz za pomocą > hosta skryptów systemu Microsoft Windows](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

1. Przejrzyj wyświetlone wyniki diagnostyki i wybierz pozycję **tak** , aby rozwiązać problemy. Zostanie wyświetlone okno dialogowe **sprawdzanie wyników** z informacjami o tym, co należy zrobić, jeśli rozszerzenie nie działa.  
1. Przeczytaj komunikat i wybierz **przycisk OK**.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Sprawdź, czy rozszerzenie panelu dostępu jest włączone

Aby sprawdzić, czy rozszerzenie panelu dostępu zostało włączone w programie Internet Explorer:

1. W programie Internet Explorer wybierz **ikonę koła zębatego** w prawym górnym rogu okna i wybierz pozycję **Opcje internetowe**.
1. Przejdź do karty **programy** i wybierz pozycję **Zarządzaj**dodatkami.
1. Wybierz pozycję **rozszerzenie panelu dostępu** w sekcji **Microsoft Corporation** i wybierz pozycję **Włącz**.
1. Aby zapisać zmiany, zamknij wszystkie otwarte okna przeglądarki Internet Explorer. Zmiana zacznie obowiązywać przy następnym otwarciu programu Internet Explorer.

## <a name="enable-extensions-for-inprivate-browsing"></a>Włącz rozszerzenia do przeglądania InPrivate

Aby włączyć rozszerzenia do przeglądania InPrivate:

1. W programie Internet Explorer wybierz **ikonę koła zębatego** w prawym górnym rogu okna i wybierz pozycję **Opcje internetowe**.
1. Przejdź do karty **prywatność** i sprawdź, czy pole wyboru **Wyłącz paski narzędzi i rozszerzenia po rozpoczęciu przeglądania InPrivate** jest puste.
1. Aby zapisać zmiany, zamknij wszystkie otwarte okna przeglądarki Internet Explorer. Zmiana zacznie obowiązywać przy następnym otwarciu programu Internet Explorer.

## <a name="uninstall-the-access-panel-extension"></a>Odinstaluj rozszerzenie panelu dostępu

Aby odinstalować rozszerzenie panelu dostępu z komputera:

1. W panelu sterowania Wyszukaj pozycję *Odinstaluj*.
1. W wynikach wyszukiwania wybierz pozycję **Odinstaluj program**.

    ![Wybierz opcję Odinstaluj program z panelu sterowania](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

1. Z listy wybierz pozycję **rozszerzenie panelu dostępu** i wybierz pozycję **Odinstaluj**.

    ![Odinstaluj rozszerzenie panelu dostępu](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)

1. Następnie możesz spróbować ponownie zainstalować rozszerzenie, aby sprawdzić, czy problem został rozwiązany.

Jeśli wystąpią problemy z odinstalowaniem rozszerzenia, można je również usunąć za pomocą narzędzia [Microsoft Fix it](https://go.microsoft.com/?linkid=9779673) .

## <a name="related-articles"></a>Pokrewne artykuły:

* [Dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory](what-is-single-sign-on.md)
* [Jak wdrożyć rozszerzenie panelu dostępu dla programu Internet Explorer przy użyciu zasady grupy](deploy-access-panel-browser-extension.md)
