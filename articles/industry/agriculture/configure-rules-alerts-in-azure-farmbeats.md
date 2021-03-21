---
title: Konfigurowanie reguł i zarządzanie alertami
description: Opisuje sposób konfigurowania reguł i zarządzania alertami w programie FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: a04f973cbfa3a68016065f50e9e2ff4f7566da94
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182931"
---
# <a name="configure-rules-and-manage-alerts"></a>Konfigurowanie reguł i zarządzanie alertami

Usługa Azure FarmBeats umożliwia tworzenie reguł opartych na logice biznesowej, a także danych czujników, które są przepływów z czujników i urządzeń wdrożonych w farmie. Reguły wyzwalają alerty w systemie zawsze, gdy wartości z czujnika przekraczają wartość progową. Wyświetlając i analizując alerty utworzone po wartości progowych, można szybko podejmować problemy i uzyskać wymagane rozwiązania.

## <a name="create-rule"></a>Tworzenie reguły

1. Na stronie głównej przejdź do pozycji **reguły**.
2. Wybierz pozycję **Nowa reguła**. Zostanie wyświetlone okno Nowa reguła.

    ![Zrzut ekranu, który podświetla przycisk Nowa reguła i sekcję Nowa reguła.](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-1.png)

3. Wprowadź **nazwę reguły** i **Opis reguły** , a następnie wybierz farmę z menu rozwijanego **Wybieranie farmy** .
4. Wpisz nazwę farmy, aby wybrać sekcję farmy i **warunki** w tym samym oknie.  

    ![Zrzut ekranu, który podświetla sekcję warunki.](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-condition-1.png)

5. W obszarze **warunki** wprowadź wartości dla **miary**, **operatora** i **wartości**.
6. W menu rozwijanym **pomiar** wpisz nazwę miary.
7. Wybierz pozycję **+ Dodaj warunek** , aby dodać więcej warunków do reguły.
8. Wybierz **poziom ważności**.
9. W obszarze **Akcja** przejdź do przycisku przełączania z **włączoną obsługą poczty** e-mail, aby włączyć alerty e-mail.

    ![Zrzut ekranu pokazujący opcję z włączoną obsługą poczty E-mail.](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-email-1.png)

10. Wprowadź **adresy e-mail** , na które chcesz wysłać Alert e-mail, wraz z **tematem wiadomości e-mail** i **dodatkowymi notatkami**.  
11. Aby włączyć lub wyłączyć regułę, w obszarze **stan reguły** przejdź do opcji **włączony** przełącznik.
    Można wyświetlić liczbę urządzeń, na które wpłynie reguła.
12. Wybierz pozycję **Zastosuj** , aby utworzyć regułę.

## <a name="view-rule"></a>Wyświetl regułę

Na stronie **Farma** zostanie wyświetlona lista dostępnych reguł. Wybierz **nazwę reguły**. W oknie są wyświetlane następujące szczegóły dotyczące wybranej reguły:
 - Nazwa reguły
 - Połącz z farmą, z którą skojarzona jest reguła
 - Data utworzenia
 - Data ostatniej aktualizacji
 - Poziom ważności
 - Stan reguły
 - Lista warunków  
 - Liczba urządzeń, na które ma wpływ reguła

    ![Zrzut ekranu przedstawiający ekran Szczegóły reguły.](./media/configure-rules-and-alerts-in-azure-farmbeats/view-rule-1.png)

## <a name="edit-rule"></a>Edytowanie reguły

Aby edytować regułę, wykonaj następujące kroki:

1. Na stronie głównej wybierz pozycję **reguły** w menu nawigacji po lewej stronie.
   Zostanie wyświetlone okno reguły.
2. Wybierz regułę, dla której chcesz edytować.

    ![Zrzut ekranu przedstawiający wybraną regułę.](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-action-bar-1.png)

3. Wybierz pozycję **Edytuj** na pasku akcji, aby wyświetlić okno **Edytowanie reguły** .

    ![Zrzut ekranu przedstawiający ekran Edytowanie reguły.](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-one-1.png)

4. Zmień **nazwę reguły** i **Opis reguły** , a następnie wybierz farmę z menu rozwijanego **Wybieranie farmy** .
5. Wpisz nazwę farmy, aby wybrać farmę i **warunki** pojawiają się w tym samym oknie.  
6. W obszarze **warunki**, Edycja **miary**, **operator** i **wartość**.
7. W menu rozwijanym **pomiar** wpisz nazwę miary.
8. Wybierz pozycję **+ Dodaj warunek** , aby dodać/edytować warunki do reguł.

    ![Zrzut ekranu, który podświetla przycisk Dodaj warunek.](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-two-1.png)

9.  Wybierz **poziom ważności**.  
10. W obszarze **Akcja** przejdź do przycisku przełączania z **włączoną obsługą poczty** e-mail, aby włączyć alerty e-mail.
11. Edytuj **adresy e-mail** , na które chcesz wysłać Alert e-mail, a także **temat wiadomości E-mail** i **dodatkowe uwagi**.  
12. Aby włączyć lub wyłączyć regułę, w obszarze **stan reguły** przejdź do opcji **włączony** przełącznik.
Można wyświetlić liczbę urządzeń, na które wpłynie ta reguła.
13. Wybierz pozycję **Zastosuj** , aby edytować regułę.

## <a name="change-rule-status"></a>Zmień stan reguły

Aby zmienić stan reguły, wykonaj następujące kroki:

1. Na stronie głównej wybierz pozycję **reguły** w menu nawigacji po lewej stronie. Zostanie wyświetlone okno reguły.
2. Wybierz regułę, dla której chcesz zmienić stan.

    ![Zrzut ekranu pokazujący przycisk zmiany stanu.](./media/configure-rules-and-alerts-in-azure-farmbeats/change-status-rule-action-bar-1.png)

3. Wybierz pozycję **Zmień stan** na pasku akcji. Zostanie wyświetlone okno **zmiany stanu** .

    ![Zrzut ekranu przedstawiający ekran zmiana stanu.](./media/configure-rules-and-alerts-in-azure-farmbeats/rule-change-status-1.png)

3. Zmień stan reguły przy użyciu przycisku **Zmień stan** .
   Można wyświetlić liczbę urządzeń, na które wpłynie reguła.
4. Wybierz pozycję **Zastosuj** , aby zmienić stan reguły.

## <a name="delete-rule"></a>Usuwanie reguły

Aby usunąć regułę, wykonaj następujące kroki:

1. Na stronie głównej wybierz pozycję **reguły** w menu nawigacji po lewej stronie. Zostanie wyświetlone okno reguły.
2. Wybierz regułę, dla której chcesz usunąć.

    ![Zrzut ekranu, który podświetla przycisk Usuń.](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-action-bar-1.png)

3. Na pasku akcji wybierz pozycję **Usuń** .

    ![Farmy projektów](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-1.png)

4. Zostanie wyświetlone okno dialogowe **Usuwanie reguły** . Wybierz pozycję **Usuń**.
