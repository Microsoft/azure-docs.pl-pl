---
title: Dołączanie komputerów z systemem Windows do usługi Azure Security Center
description: W tym przewodniku szybki start pokazano, jak udostępnić Agent Log Analytics na komputerze z systemem Windows.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/3/2018
ms.author: memildin
ms.openlocfilehash: 2840b6636c2b511ab57a8bae8adf411f4d8a27d2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80435921"
---
# <a name="quickstart-onboard-windows-computers-to-azure-security-center"></a>Szybki start: dołączanie komputerów z systemem Windows do usługi Azure Security Center
Po dodaniu subskrypcji platformy Azure można włączyć Security Center dla zasobów działających poza platformą Azure, na przykład lokalnie lub w innych chmurach, udostępniając agenta Log Analytics.

W tym przewodniku szybki start przedstawiono sposób instalowania agenta Log Analytics na komputerze z systemem Windows.

## <a name="prerequisites"></a>Wymagania wstępne
Do rozpoczęcia korzystania z usługi Security Center wymagana jest subskrypcja usługi Microsoft Azure. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnego konta](https://azure.microsoft.com/pricing/free-trial/).

Musisz korzystać z warstwy cenowej Standardowa usługi Security Center przed rozpoczęciem tego przewodnika Szybki start. Zobacz [Dołączanie subskrypcji platformy Azure do usługi Security Center w warstwie Standardowa](security-center-get-started.md), aby uzyskać instrukcje dotyczące uaktualnienia. Warstwę Standardowa usługi Security Center możesz wypróbować bezpłatnie. Aby dowiedzieć się więcej, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="add-new-windows-computer"></a>Dodawanie nowego komputera z systemem Windows

1. Zaloguj się do [Azure Portal](https://azure.microsoft.com/features/azure-portal/).
2. W menu **Microsoft Azure** wybierz pozycję **Security Center**. Zostanie otwarte okno **Security Center — Przegląd**.

   ![Security Center — Przegląd][2]

3. W menu głównym usługi Security Center wybierz pozycję **Wprowadzenie**.
4. Wybierz kartę **Rozpoczęcie pracy**.

   ![Wprowadzenie][3]

5. Kliknij przycisk **Konfiguruj** w obszarze **Dodaj nowe komputery spoza platformy Azure**. Zostanie wyświetlona lista obszarów roboczych usługi Log Analytics. Jeśli ma to zastosowanie, lista zawiera domyślny obszar roboczy utworzony przez usługę Security Center po włączeniu automatycznej aprowizacji. Wybierz ten obszar roboczy lub inny obszar roboczy, którego chcesz użyć.

    ![Dodawanie komputera spoza platformy Azure](./media/quick-onboard-windows-computer/non-azure.png)

   Zostanie otwarty blok **Agent bezpośredni** z linkiem umożliwiającym pobieranie agenta systemu Windows i kluczami identyfikatora obszaru roboczego do użycia podczas konfigurowania agenta.

6. Wybierz link **Pobierz agenta systemu Windows** odpowiadający typowi procesora komputera, aby pobrać plik instalacji.

7. Po prawej stronie **identyfikatora obszaru roboczego** wybierz ikonę kopiowania i wklej identyfikator do Notatnika.

8. Po prawej stronie **klucza podstawowego** wybierz ikonę kopiowania i wklej klucz do Notatnika.

## <a name="install-the-agent"></a>Instalowanie agenta
Teraz musisz zainstalować pobrany plik na komputerze docelowym.

1. Skopiuj plik na komputerze docelowym i uruchom Instalatora.
2. Na **stronie powitalnej** wybierz pozycję **Dalej**.
3. Na stronie **Postanowienia licencyjne** zapoznaj się z postanowieniami licencyjnymi, a następnie wybierz pozycję **Zgadzam się**.
4. Na stronie **Folder docelowy** zmień lub pozostaw domyślny folder instalacji, a następnie wybierz pozycję **Dalej**.
5. Na stronie **Opcje instalacji agenta** wybierz połączenie agenta z usługą Azure Log Analytics, a następnie wybierz pozycję **Dalej**.
6. Na stronie **Azure Log Analytics** wklej **identyfikator obszaru roboczego** i **klucz obszaru roboczego (klucz podstawowy)** skopiowany do Notatnika w ramach poprzedniej procedury.
7. Jeśli komputer powinien wysyłać raporty do obszaru roboczego usługi Log Analytics w chmurze Azure dla instytucji rządowych, wybierz pozycję **Azure — instytucje rządowe USA** z listy rozwijanej **Azure Cloud**. Jeśli komputer musi komunikować się z usługą Log Analytics za pośrednictwem serwera proxy, wybierz pozycję **Zaawansowane** i podaj adres URL i numer portu serwera proxy.
8. Po zdefiniowaniu niezbędnych ustawień konfiguracji wybierz pozycję **Dalej**.

   ![Instalowanie agenta][5]

9. Na stronie **Gotowe do zainstalowania** przejrzyj wybrane opcje, a następnie wybierz pozycję **Zainstaluj**.
10. Na stronie **Konfiguracja została zakończona pomyślnie** wybierz pozycję **Zakończ**.

Po zakończeniu **log Analytics Agent** zostanie wyświetlony w **Panelu sterowania**. Możesz tam przejrzeć konfigurację i sprawdzić, czy agent został połączony.

Aby uzyskać więcej informacji na temat instalowania i konfigurowania agenta, zobacz [Connect Windows computers (Łączenie komputerów z systemem Windows)](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard).

Teraz możesz monitorować maszyny wirtualne platformy Azure oraz komputery spoza platformy Azure w jednym miejscu. W obszarze **Obliczanie** zawarto przegląd wszystkich maszyn wirtualnych i komputerów wraz z zaleceniami. Każda kolumna reprezentuje jeden zestaw zaleceń. Kolor reprezentuje bieżący stan zabezpieczeń maszyny wirtualnej lub komputera dla tego zalecenia. Ponadto usługa Security Center przedstawia wszelkie wykrycia dotyczące tych komputerów w alertach zabezpieczeń.

  ![Blok Obliczanie][6]

Istnieją dwa typy ikon przedstawianych w bloku **Obliczanie**:

![icon1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) Komputer spoza platformy Azure

![icon2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) Maszyna wirtualna platformy Azure

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy agent nie jest już potrzebny, można usunąć go z komputera z systemem Windows.

Aby usunąć agenta:

1. Otwórz **Panel sterowania**.
2. Otwórz aplet **Programy i funkcje**.
3. W obszarze **programy i funkcje**wybierz opcję **Agent log Analytics** i kliknij przycisk **Odinstaluj**.

## <a name="next-steps"></a>Następne kroki
W tym przewodniku szybki start został zainicjowany Agent Log Analytics na komputerze z systemem Windows. Aby dowiedzieć się więcej na temat sposobu korzystania z usługi Security Center, przejdź do samouczka konfigurowania zasad zabezpieczeń i oceniania zabezpieczeń Twoich zasobów.

> [!div class="nextstepaction"]
> [Samouczek: Definiowanie i ocenianie zasad zabezpieczeń](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/get-started.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
