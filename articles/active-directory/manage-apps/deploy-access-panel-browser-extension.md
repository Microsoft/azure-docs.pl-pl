---
title: Wdróż rozszerzenie panelu dostępu platformy Azure dla programu IE przy użyciu obiektu zasad grupy | Microsoft Docs
description: Jak wdrożyć dodatek programu Internet Explorer dla portalu My Apps przy użyciu zasad grupy.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/08/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94c434a2892060acfdd56c496a31e41597c21357
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2020
ms.locfileid: "84763435"
---
# <a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>Instrukcje: Wdrażanie rozszerzenia panelu dostępu dla programu Internet Explorer przy użyciu zasad grupy

W tym samouczku pokazano, jak za pomocą zasad grupy zdalnie zainstalować rozszerzenie panelu dostępu dla programu Internet Explorer na komputerach użytkowników. To rozszerzenie jest wymagane dla użytkowników programu Internet Explorer, którzy muszą zalogować się do aplikacji skonfigurowanych przy użyciu [logowania jednokrotnego opartego na hasłach](what-is-single-sign-on.md#password-based-sso).

Zaleca się, aby administratorzy automatyzują wdrażanie tego rozszerzenia. W przeciwnym razie użytkownicy będą musieli pobrać i zainstalować rozszerzenie, które jest podatne na błąd użytkownika i wymaga uprawnień administratora. Ten samouczek obejmuje jedną z metod automatyzowania wdrożeń oprogramowania przy użyciu zasad grupy. [Dowiedz się więcej o zasadach grupy.](https://technet.microsoft.com/windowsserver/bb310732.aspx)

Rozszerzenie panelu dostępu jest również dostępne dla [przeglądarki Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) i [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998), a żadne z nich nie wymaga uprawnień administratora do instalacji.

## <a name="prerequisites"></a>Wymagania wstępne

* Skonfigurowano [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)i przyłączono komputery użytkowników do domeny.
* Aby edytować obiekt zasady grupy (GPO), musisz mieć uprawnienie "Edytuj ustawienia". Domyślnie członkowie następujących grup zabezpieczeń mają to uprawnienie: Administratorzy domeny, Administratorzy przedsiębiorstwa i właściciele zasady grupy Twórcy. [Dowiedz się więcej.](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

## <a name="step-1-create-the-distribution-point"></a>Krok 1. Tworzenie punktu dystrybucji

Najpierw należy umieścić pakiet Instalatora w lokalizacji sieciowej, do której mają dostęp komputery, na których chcesz zdalnie zainstalować rozszerzenie. W tym celu wykonaj następujące kroki:

1. Zaloguj się na serwerze jako administrator.
1. W oknie **Menedżer serwera** przejdź do pozycji **pliki i usługi magazynu**.

    ![Otwórz plik i usługi magazynu](./media/deploy-access-panel-browser-extension/files-services.png)

1. Przejdź do karty **udziały** . Następnie kliknij kolejno pozycje **zadania**  >  **Nowy udział...**

    ![Zrzut ekranu przedstawiający miejsce znalezienia nowego udziału na ekranie zadania](./media/deploy-access-panel-browser-extension/shares.png)

1. Ukończ pracę **Kreatora nowego udziału** i Ustaw uprawnienia, aby upewnić się, że można uzyskać do niego dostęp z maszyn użytkowników. [Dowiedz się więcej o udziałach.](https://technet.microsoft.com/library/cc753175.aspx)
1. Pobierz następujący pakiet Microsoft Instalator Windows (plik msi): [panel dostępu Extension.msi](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)
1. Skopiuj pakiet Instalatora do żądanej lokalizacji w udziale.

    ![Kopiuj plik msi do udziału](./media/deploy-access-panel-browser-extension/copy-package.png)

1. Sprawdź, czy komputery klienckie mogą uzyskać dostęp do pakietu Instalatora z udziału.

## <a name="step-2-create-the-group-policy-object"></a>Krok 2. Tworzenie obiektu zasad grupy

1. Zaloguj się na serwerze, który obsługuje instalację Active Directory Domain Services (AD DS).
1. W Menedżer serwera przejdź do pozycji **Narzędzia**  >  **zasady grupy zarządzanie**.

    ![Przejdź do narzędzi > zasady grupy Management](./media/deploy-access-panel-browser-extension/tools-gpm.png)

1. W lewym okienku okna **zarządzania zasady grupy** Wyświetl hierarchię jednostki organizacyjnej (OU) i określ zakres, w którym chcesz zastosować zasady grupy. Można na przykład wybrać małą jednostkę organizacyjną do wdrożenia na kilku użytkowników do testowania lub wybrać jednostkę organizacyjną najwyższego poziomu do wdrożenia w całej organizacji.

   > [!NOTE]
   > Jeśli chcesz utworzyć lub edytować jednostki organizacyjne (OU), przełącz się z powrotem do Menedżer serwera i przejdź do pozycji **Narzędzia**  >  **Active Directory Użytkownicy i komputery**.

1. Po wybraniu jednostki organizacyjnej kliknij ją prawym przyciskiem myszy, a następnie wybierz pozycję **Utwórz obiekt zasad grupy w tej domenie i umieść tu łącze...**

    ![Zrzut ekranu przedstawia opcję Utwórz nowy obiekt zasad grupy](./media/deploy-access-panel-browser-extension/create-gpo.png)

1. W oknie **nowy obiekt zasad grupy** wpisz nazwę nowego obiektu zasady grupy.
1. Kliknij prawym przyciskiem myszy obiekt zasady grupy, który został utworzony, a następnie wybierz polecenie **Edytuj**.

## <a name="step-3-assign-the-installation-package"></a>Krok 3. przypisanie pakietu instalacyjnego

1. Ustal, czy chcesz wdrożyć rozszerzenie na podstawie **konfiguracji komputera** lub **konfiguracji użytkownika**. W przypadku korzystania z [konfiguracji komputera](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx)rozszerzenie jest instalowane na komputerze niezależnie od tego, którzy użytkownicy logują się do niego. W przypadku [konfiguracji użytkownika](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx)użytkownicy mają zainstalowane rozszerzenie, niezależnie od tego, na których komputerach logują się.
1. W lewym okienku okna **Edytor zarządzania zasadami grupy** przejdź do jednej z następujących ścieżek folderów, w zależności od wybranego typu konfiguracji:

   * `Computer Configuration/Policies/Software Settings/`
   * `User Configuration/Policies/Software Settings/`

1. Kliknij prawym przyciskiem myszy pozycję **Instalacja oprogramowania**, a następnie wybierz pozycję **Nowy**  >  **pakiet.**
1. Przejdź do folderu udostępnionego zawierającego pakiet Instalatora z [kroku 1: Utwórz punkt dystrybucji](#step-1-create-the-distribution-point), wybierz plik msi, a następnie kliknij przycisk **Otwórz**.

   > [!IMPORTANT]
   > Jeśli udział znajduje się na tym samym serwerze, upewnij się, że uzyskujesz dostęp do pliku msi za pomocą ścieżki plików sieciowych zamiast ze ścieżki do pliku lokalnego.

    ![Wybierz pakiet instalacyjny z folderu udostępnionego](./media/deploy-access-panel-browser-extension/select-package.png)

1. W wierszu polecenia **Wdróż oprogramowanie** wybierz pozycję **przypisana** do metody wdrożenia. Następnie kliknij przycisk **OK**.

Rozszerzenie jest teraz wdrożone w wybranej jednostce organizacyjnej. [Dowiedz się więcej o instalacja oprogramowania zasad grupy.](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

## <a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>Krok 4. włączenie autowłączania rozszerzenia dla programu Internet Explorer

Oprócz uruchamiania Instalatora, każde rozszerzenie programu Internet Explorer musi być jawnie włączone, zanim będzie można go użyć. Wykonaj poniższe kroki, aby włączyć rozszerzenie panelu dostępu przy użyciu zasad grupy:

1. W oknie **Edytor zarządzania zasadami grupy** przejdź do jednej z następujących ścieżek, w zależności od typu konfiguracji, która została wybrana w [kroku 3: Przypisz pakiet instalacyjny](#step-3-assign-the-installation-package):

   * `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`

1. Kliknij prawym przyciskiem myszy **listę dodatków**i wybierz polecenie **Edytuj**.

    ![Kliknij prawym przyciskiem myszy pozycję "Lista dodatków" i wybierz pozycję "Edytuj".](./media/deploy-access-panel-browser-extension/edit-add-on-list.png)

1. W oknie **Lista dodatków** wybierz opcję **włączone**. Następnie w sekcji **Opcje** kliknij pozycję **Pokaż...**.

    ![Kliknij pozycję Włącz, a następnie kliknij pozycję Pokaż...](./media/deploy-access-panel-browser-extension/edit-add-on-list-window.png)

1. W oknie **Pokaż zawartość** wykonaj następujące czynności:

   1. Dla pierwszej kolumny (pole **Nazwa wartości** ) Skopiuj i wklej następujący identyfikator klasy:`{030E9A3F-7B18-4122-9A60-B87235E4F59E}`
   1. Dla drugiej kolumny (pole **wartość** ) wpisz następującą wartość:`1`
   1. Kliknij przycisk **OK** , aby zamknąć okno **Pokaż zawartość** .

      ![Wypełnij wartości określone w poprzednim kroku](./media/deploy-access-panel-browser-extension/show-contents.png)

1. Kliknij przycisk **OK** , aby zastosować zmiany i zamknąć okno **Lista dodatków** .

Rozszerzenie powinno być teraz włączone dla maszyn w wybranej jednostce organizacyjnej. [Dowiedz się więcej o używaniu zasad grupy do włączania lub wyłączania dodatków programu Internet Explorer.](https://technet.microsoft.com/library/dn454941.aspx)

## <a name="step-5-optional-disable-remember-password-prompt"></a>Krok 5 (opcjonalny): Wyłącz monit "Zapamiętaj hasło"

Gdy użytkownicy logują się do witryn sieci Web przy użyciu rozszerzenia panelu dostępu, program Internet Explorer może wyświetlić następujący monit z pytaniem o to, czy chcesz przechowywać hasło? ".

![Pokazuje "czy chcesz przechowywać swoje hasło..." pytać](./media/deploy-access-panel-browser-extension/remember-password-prompt.png)

Aby zapobiec wyświetlaniu tego monitu przez użytkowników, wykonaj poniższe czynności, aby zapobiec autouzupełnianiu haseł w następujący sposób:

1. W oknie **Edytor zarządzania zasadami grupy** przejdź do ścieżki wymienionej poniżej. To ustawienie konfiguracji jest dostępne tylko w obszarze **Konfiguracja użytkownika**.

   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`
1. Znajdź ustawienie o nazwie **Włącz funkcję autouzupełniania dla nazw użytkowników i haseł w formularzach**.

   > [!NOTE]
   > Poprzednie wersje Active Directory mogą wyświetlić to ustawienie z nazwą, **która nie zezwala na używanie autouzupełniania w celu zapisywania haseł**. Konfiguracja tego ustawienia różni się od ustawienia opisanego w tym samouczku.

    ![Pamiętaj, aby wyszukać to w obszarze Ustawienia użytkownika](./media/deploy-access-panel-browser-extension/disable-auto-complete.png)

1. Kliknij powyższe ustawienie prawym przyciskiem myszy, a następnie wybierz pozycję **Edytuj**.
1. W oknie zatytułowanym **Włącz funkcję autouzupełniania dla nazw użytkowników i haseł w formularzach**wybierz pozycję **wyłączone**.

    ![Wybierz opcję "wyłączone" dla funkcji Włącz funkcję autouzupełniania](./media/deploy-access-panel-browser-extension/disable-passwords.png)

1. Kliknij przycisk **OK** , aby zastosować te zmiany i zamknąć okno.

Użytkownicy nie będą już mogli przechowywać swoich poświadczeń ani używać autouzupełniania, aby uzyskać dostęp do wcześniej przechowywanych poświadczeń. Jednak te zasady umożliwiają użytkownikom kontynuowanie korzystania z Autouzupełniania dla innych typów pól formularza, takich jak pola wyszukiwania.

> [!WARNING]
> Jeśli te zasady są włączone, gdy użytkownicy wybrali niektóre poświadczenia, te zasady *nie* będą czyścić poświadczeń, które zostały już zapisane.

## <a name="step-6-testing-the-deployment"></a>Krok 6. Testowanie wdrożenia

Wykonaj poniższe kroki, aby sprawdzić, czy wdrożenie rozszerzenia zakończyło się pomyślnie:

1. Jeśli wdrożono przy użyciu **konfiguracji komputera**, zaloguj się do komputera klienckiego należącego do jednostki organizacyjnej, która została wybrana w [kroku 2: Tworzenie obiektu zasady grupy](#step-2-create-the-group-policy-object). Jeśli wdrożono program przy użyciu **konfiguracji użytkownika**, należy się upewnić, że zalogowanie się jako użytkownik, który należy do tej jednostki organizacyjnej.
1. Może to potrwać kilka logowań, aby zmiany zasad grupy zostały w pełni zaktualizowane z tą maszyną. Aby wymusić aktualizację, Otwórz okno **wiersza polecenia** i uruchom następujące polecenie:`gpupdate /force`
1. Aby instalacja została przeprowadzona, należy ponownie uruchomić maszynę. Rozruchu może trwać znacznie więcej czasu niż zwykle, gdy rozszerzenie zostanie zainstalowane.
1. Po ponownym uruchomieniu programu Otwórz program **Internet Explorer**. W prawym górnym rogu okna kliknij pozycję **Narzędzia** (ikona koła zębatego), a następnie wybierz pozycję **Zarządzaj dodatkami**.
1. W oknie **Zarządzanie dodatkami** Sprawdź, czy **rozszerzenie panelu dostępu** zostało zainstalowane i czy jego **stan** jest ustawiony na **włączone**.

   ![Sprawdź, czy rozszerzenie panelu dostępu jest zainstalowane i włączone](./media/deploy-access-panel-browser-extension/verify-install.png)

## <a name="learn-more"></a>Dowiedz się więcej

* [Dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory](what-is-single-sign-on.md)
* [Rozwiązywanie problemów z rozszerzeniem panelu dostępu dla programu Internet Explorer](manage-access-panel-browser-extension.md)
