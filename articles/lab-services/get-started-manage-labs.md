---
title: Wprowadzenie do Azure Lab Services
description: W tym artykule opisano, jak rozpocząć pracę z Azure Lab Services.
ms.topic: article
ms.date: 11/18/2020
ms.openlocfilehash: d260ace7d7819c3ca2db96d6c4984bf834e170da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98165046"
---
# <a name="get-started-with-lab-services"></a>Wprowadzenie do usług laboratoryjnych 

Azure Lab Services oferuje uczniów i nauczycieli z dostępem do laboratoriów komputera wirtualnego bezpośrednio z własnych komputerów.

Nauczyciele muszą dowiedzieć się, jak nauczyć uczniów/rodziców, aby mogli korzystać z usług laboratoryjnych w ramach swoich instrukcji za poorednictwem sprzętu wystawionego przez jednego ucznia. W związku z tym uczniowie mogą uzyskać dostęp do oprogramowania standardowego w branży wymaganego przez programy badawcze za pomocą Virtual Machines (VM). 

Maszyna wirtualna to środowisko wirtualne, które działa jako komputer wirtualny. Maszyny wirtualne mają własny procesor, pamięć i magazyn. Maszyny wirtualne stanowią podstawę dla prawdziwej maszyny i mogą zapewnić użytkownikom dostęp do systemów operacyjnych i oprogramowania bez konieczności posiadania ich na własnym urządzeniu. Azure Lab Services zapewnia studentom dostęp do maszyn wirtualnych i nawigowanie po nich oraz do zarządzania nimi. 

Ten artykuł zawiera informacje dotyczące personelu nauczania w zakresie uzyskiwania dostępu do Azure Lab Services oraz zarządzania nimi i nauczania z nich.

## <a name="key-concepts"></a>Kluczowe pojęcia

### <a name="quota-hours"></a>Godziny przydziału

Studenci mogą uzyskać dostęp do swoich maszyn wirtualnych w dowolnym momencie podczas zaplanowanej klasy czasu bez wpływu na ich godziny przydziału. Godziny przydziału są ustawiane dla całego semestru i określają liczbę godzin, przez studenta mogą korzystać z maszyny wirtualnej poza regularnie zaplanowanym czasem klasy.

8 godzin na tydzień, resetuje od niedzieli — nie skumulowany.

Aby uzyskać więcej informacji, zobacz [Ustawianie limitu przydziału](how-to-configure-student-usage.md#set-quotas-for-users).

### <a name="automatic-shut-down"></a>Automatyczne zamykanie

Aby pomóc w obniżeniu kosztów i zaoszczędzeniu godzin przydziału dla uczniów, automatyczne wyłączenia są włączane dla laboratoriów. Automatyczne zamykanie spowoduje wyłączenie maszyn wirtualnych po okresie braku aktywności (bez danych wejściowych myszy lub klawiatury). Automatyczne zamykanie działa w dwóch etapach, a pierwszy student zostanie odłączony od maszyny wirtualnej po okresie braku aktywności. W tym momencie maszyna wirtualna nadal **działa** i uczniowie mogą nawiązywać połączenia. Po rozłączeniu z innym okresem braku aktywności maszyna wirtualna zostanie zamknięta.

Automatyczne zamykanie jest ważnym narzędziem do oszczędzania kosztów, ale w związku z tym w przypadku zapisywania pracy i renderowania dużych plików projektu są one dostępne jako wyzwanie dla studentów. Jeśli uczniowie są często rozłączane lub maszyny wirtualne są zbyt szybko wyłączone, skontaktuj się z administratorem CTE. 

Aby uzyskać więcej informacji, zobacz [Konfigurowanie automatycznego zamykania maszyn wirtualnych dla konta laboratorium](how-to-configure-lab-accounts.md).

### <a name="managing-virtual-machines"></a>Zarządzanie maszynami wirtualnymi

Zarządzanie laboratorium umożliwia nauczycielom Sterowanie elementami, takimi jak pojemność laboratoryjna (liczba maszyn wirtualnych dostępnych dla studentów) oraz ręczne uruchamianie, zatrzymywanie lub resetowanie maszyn wirtualnych. Nauczyciele mogą również łączyć się z maszynami wirtualnymi, aby korzystać z interfejsu ucznia, uzyskiwać dostęp do plików i rozwiązywać problemy z oprogramowaniem lub maszyną wirtualną.

Najważniejszym znaczeniem do zapamiętania, gdy zarządzanie maszynami wirtualnymi jest **uruchomienie** komputera, są ponoszone koszty nawet wtedy, gdy żaden z nich nie jest podłączony do maszyny wirtualnej.

## <a name="lab-dashboards"></a>Pulpity nawigacyjne laboratorium

### <a name="overview"></a>Omówienie

Pulpity nawigacyjne dla laboratoriów w Azure Lab Services zawierają migawkę różnych aspektów konkretnego laboratorium, w tym informacje o maszynie wirtualnej, liczbę przypisanych i nieprzypisanych maszyn wirtualnych, liczbę zarejestrowanych i niezarejestrowanego użytkownika oraz informacje o harmonogramach laboratorium. 

> [!NOTE]
> Chociaż większość aspektów administracyjnych pulpitu nawigacyjnego i [witryny sieci web Azure Lab Services](https://labs.azure.com/) będzie widoczna dla nauczycieli, uprawnienia specyficzne dla Twojej roli mogą mieć wpływ na możliwość modyfikowania określonych kryteriów na pulpicie nawigacyjnym. Jeśli wystąpi problem z konkretną konfigurację laboratorium, skontaktuj się z administratorem CTE.

:::image type="content" source="./media/use-dashboard/dashboard.png" alt-text="Portal Azure Lab Services":::

### <a name="examine-a-dashboard"></a>Sprawdzanie pulpitu nawigacyjnego

1. Przejdź do [witryny sieci Web usługi Azure Lab Services](https://labs.azure.com/)i zaloguj się.
1. Wybierz laboratorium.
1. Po lewej stronie okna zobaczysz **pulpit nawigacyjny** . Kliknij pozycję **pulpit nawigacyjny** , aby zobaczyć kilka kafelków na pulpicie nawigacyjnym.
1. Poniżej znajdują się kafelki z **Rozliczeniami &** , istnieją również kafelki dotyczące szablonów, pul maszyn wirtualnych, użytkowników i harmonogramów, które pozwalają modyfikować aspekty i wyświetlać więcej szczegółowych informacji na temat laboratorium zajęć.

    * Szablon — opisuje datę utworzenia szablonu i ostatnią publikację. 
    * Pula maszyn wirtualnych — liczba przypisanych i nieprzypisanych maszyn wirtualnych.
    * Użytkownicy — liczba zarejestrowanych użytkowników i użytkowników, którzy zostali dodani do laboratorium, ale nie są zarejestrowani.
    * Harmonogramy — wyświetla nadchodzące zaplanowane zdarzenia dla laboratorium oraz link, aby wyświetlić więcej zdarzeń.

Aby uzyskać więcej informacji, zobacz [Korzystanie z pulpitu nawigacyjnego](use-dashboard.md).

### <a name="manually-starting-vms"></a>Ręczne uruchamianie maszyn wirtualnych

1. Na stronie **Pula maszyn wirtualnych** możesz uruchomić wszystkie maszyny wirtualne w laboratorium, klikając przycisk **Rozpocznij wszystko** w górnej części strony.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/start-all-vms-button.png" alt-text="Uruchamianie maszyn wirtualnych":::
1. Poszczególne maszyny wirtualne można uruchomić, klikając przełącznik stanu. 

    Przełącznik zostanie odczytany, **rozpoczynając** od uruchomienia maszyny wirtualnej, a następnie **uruchomiona** po uruchomieniu maszyny wirtualnej.
1. Możesz również wybrać liczbę maszyn wirtualnych przy użyciu czeków po lewej stronie **nazwy** kolumny. 

    Po wybraniu żądanych maszyn wirtualnych kliknij przycisk **Start** w górnej części ekranu.
1. Po uruchomieniu można kliknąć przycisk **Zatrzymaj wszystko** , aby zatrzymać wszystkie maszyny wirtualne.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png" alt-text="Zatrzymywanie maszyn wirtualnych":::

### <a name="stopping-and-resetting-vms"></a>Zatrzymywanie i resetowanie maszyn wirtualnych

* Można zatrzymać poszczególne maszyny wirtualne, klikając przełącznik stanu.
* Możesz również zatrzymać wiele maszyn wirtualnych, używając testów i klikając przycisk "Zatrzymaj" w górnej części ekranu.

Jeśli student ma problemy z nawiązywaniem połączenia z maszyną wirtualną lub maszyna wirtualna musi zostać zresetowana z dowolnego powodu, można użyć funkcji Reset.
1. Aby zresetować co najmniej jedną maszynę wirtualną, wybierz ją przy użyciu sprawdzenia, a następnie kliknij przycisk **Resetuj** w górnej części strony.
1. W oknie podręcznym kliknij pozycję **Zresetuj**.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png" alt-text="Zresetuj maszynę wirtualną":::

    > [!NOTE]
    > Włączenie maszyny wirtualnej ucznia nie wpłynie na przydział dla ucznia. Limity przydziału dla użytkowników określają liczbę godzin Lab dostępnych dla użytkownika poza zaplanowanym czasem klasy.

### <a name="connect-to-vms"></a>Nawiązywanie połączenia z maszynami wirtualnymi

Nauczyciele mogą łączyć się z maszyną wirtualną ucznia, o ile jest ona włączona, a student nie jest połączony z maszyną wirtualną. Łącząc się z maszyną wirtualną, będziesz mieć dostęp do plików lokalnych na maszynie wirtualnej i pomóc uczniom w rozwiązywaniu problemów.

1. Aby nawiązać połączenie z maszyną wirtualną ucznia, umieść wskaźnik myszy na maszynie wirtualnej na liście, a następnie kliknij przycisk **Połącz** . 
1. Następnie postępuj zgodnie z przewodnikiem wprowadzającym dla uczniów dla Chromebooks, Mac lub komputerów

:::image type="content" source="./media/how-to-set-virtual-machine-passwords/connect-student-vm.png" alt-text="Przycisk połączenia z maszyną wirtualną ucznia":::

## <a name="manage-users-in-a-lab"></a>Zarządzanie użytkownikami w laboratorium

Nauczyciele mogą dodawać użytkowników uczniów do laboratorium i monitorować ich limity godzinowe. Aby uzyskać szczegółowe informacje na temat dodawania użytkowników przy użyciu adresu e-mail lub listy arkusza kalkulacyjnego oraz rejestrowania użytkowników, zobacz [Dodawanie użytkowników laboratorium i zarządzanie nimi](how-to-configure-student-usage.md).

Po zaproszeniu użytkowników lub udostępnieniu linku będzie można monitorować użytkowników, którzy zostali pomyślnie zarejestrowani na stronie **Użytkownicy** w kolumnie **stan** . 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal używać zasobów utworzonych w tym przewodniku Szybki Start, Usuń zasoby.

## <a name="next-steps"></a>Następne kroki

[Konfigurowanie konta laboratorium](tutorial-setup-lab-account.md)
