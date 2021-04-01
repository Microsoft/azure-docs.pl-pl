---
title: 'Szybki Start: nowe przypisanie zasad przy użyciu portalu'
description: W tym przewodniku szybki start użyjesz Azure Portal, aby utworzyć przypisanie Azure Policy w celu zidentyfikowania niezgodnych zasobów.
ms.date: 01/29/2021
ms.topic: quickstart
ms.openlocfilehash: e5cbf31e897b5be404327efa254eb90ead990f5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99220891"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources"></a>Szybki Start: Tworzenie przypisania zasad w celu zidentyfikowania niezgodnych zasobów

Pierwszym krokiem do zrozumienia pojęcia zgodności na platformie Azure jest określenie obecnej sytuacji dotyczącej Twoich zasobów.
Ten przewodnik Szybki start przeprowadzi Cię przez proces tworzenia przypisania zasad w celu zidentyfikowania maszyn wirtualnych, które nie korzystają z dysków zarządzanych.

Po zakończeniu tego procesu pomyślnie zidentyfikujesz maszyny wirtualne, które nie korzystają z dysków zarządzanych. Są one _niezgodne_ z przypisaniem zasad.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="create-a-policy-assignment"></a>Tworzenie przypisania zasad

W tym przewodniku Szybki start utworzysz przypisanie zasad i przypiszesz definicję zasad _Audit VMs that do not use managed disks_ (Przeprowadź inspekcję maszyn wirtualnych, które nie korzystają z dysków zarządzanych).

1. Uruchom usługę Azure Policy w Azure Portal, wybierając pozycję **wszystkie usługi**, a następnie wyszukując i wybierając pozycję **zasady**.

   :::image type="content" source="./media/assign-policy-portal/search-policy.png" alt-text="Zrzut ekranu przedstawiający wyszukiwanie zasad w wszystkich usługach." border="false":::

1. Wybierz pozycję **Przypisania** w lewej części strony usługi Azure Policy. Przypisanie to zasady, które zostały przypisane do określonego zakresu.

   :::image type="content" source="./media/assign-policy-portal/select-assignments.png" alt-text="Zrzut ekranu przedstawiający Wybieranie strony przydziałów na stronie Przegląd zasad." border="false":::

1. Wybierz pozycję **Przypisz zasady** w górnej części strony **Zasady — Przypisania**.

   :::image type="content" source="./media/assign-policy-portal/select-assign-policy.png" alt-text="Zrzut ekranu przedstawiający Wybieranie pozycji &quot;Przypisz zasady&quot; ze strony przydziałów." border="false":::

1. Na stronie **przypisywanie zasad** Ustaw **zakres** , wybierając przycisk wielokropka, a następnie wybierając grupę lub subskrypcję zarządzania. Opcjonalnie możesz wybrać grupę zasobów. Zakres określa, jakie zasoby lub grupy zasobów są wymuszane w ramach przypisania zasad. Następnie użyj przycisku **Wybierz** w dolnej części strony **zakres** .

   W tym przykładzie użyto subskrypcji **Contoso**. Twoja subskrypcja będzie inna.

1. Zasoby można wykluczyć na podstawie opcji **Zakres**. **Wykluczenia** są uruchamiane o jeden poziom niżej od poziomu opcji **Zakres**. Pole **Wykluczenia** jest opcjonalne, więc na razie można je pozostawić puste.

1. W polu **Definicja zasad** wybierz wielokropek, aby otworzyć listę dostępnych definicji. Usługa Azure Policy zawiera wbudowane definicje zasad, których możesz używać. Dostępnych jest wiele wbudowanych definicji, na przykład:

   - Enforce tag and its value (Wymuś tag i jego wartość)
   - Apply tag and its value (Zastosuj tag i jego wartość)
   - Dziedzicz tag z grupy zasobów, jeśli brakuje

   Aby zapoznać się z częściową listą dostępnych wbudowanych zasad, zobacz [Azure Policy Samples](./samples/index.md).

1. Przeszukaj listę definicji zasad, aby znaleźć definicję _Audit VMs that do not use managed disks_ (Przeprowadź inspekcję maszyn wirtualnych, które nie używają dysków zarządzanych). Wybierz te zasady, a następnie użyj przycisku **Wybierz** .

   :::image type="content" source="./media/assign-policy-portal/select-available-definition.png" alt-text="Zrzut ekranu przedstawiający filtrowanie dostępnych definicji." border="false":::

1. W polu **Nazwa przypisania** jest automatycznie wpisywana nazwa wybranej zasady, ale można ją zmienić. W tym przykładzie pozostaw nazwę _Audit VMs that do not use managed disks_ (Przeprowadź inspekcję maszyn wirtualnych, które nie używają dysków zarządzanych). Można również dodać opcjonalny **Opis**. Opis zawiera szczegóły dotyczące danego przypisania zasad.
   Pole **Przypisane przez** jest wypełniane automatycznie w zależności od tego, kto jest zalogowany. To pole jest opcjonalne, dzięki czemu można wprowadzić wartości niestandardowe.

1. Pozostaw _włączone_ wymuszanie zasad. Aby uzyskać więcej informacji, zobacz [Tryb wymuszania przypisywania zasad](./concepts/assignment-structure.md#enforcement-mode).

1. Wybierz pozycję **dalej** w dolnej części strony lub kartę **Parametry** w górnej części strony, aby przejść do następnego segmentu Kreatora przypisywania.

1. Jeśli definicja zasad wybrana na karcie **podstawowe** zawiera parametry, zostaną one skonfigurowane na tej karcie. Ponieważ _maszyny wirtualne inspekcji, które nie korzystają z dysków zarządzanych_ nie mają żadnych parametrów, wybierz pozycję **dalej** w dolnej części strony lub kartę **korygowanie** w górnej części strony, aby przejść do następnego segmentu kreatora przypisania.

1. Pozostaw pole **Utwórz tożsamość zarządzaną** niezaznaczone. To pole _należy_ zaznaczyć, gdy zasady lub inicjatywa obejmują zasady z efektem [deployIfNotExists](./concepts/effects.md#deployifnotexists) lub [Modify](./concepts/effects.md#modify) . Ponieważ w przypadku zasad stosowanych na potrzeby tego przewodnika Szybki start tak nie jest, pozostaw to pole puste. Aby uzyskać więcej informacji, zobacz tematy dotyczące [tożsamości zarządzanych](../../active-directory/managed-identities-azure-resources/overview.md) i [sposobu działania zabezpieczeń w zakresie korygowania](./how-to/remediate-resources.md#how-remediation-security-works).

1. Wybierz pozycję **dalej** w dolnej części strony lub kartę **komunikaty niezgodności** w górnej części strony, aby przejść do następnego segmentu Kreatora przypisywania.

1. Ustaw **komunikat o niezgodności** z _maszynami wirtualnymi powinien korzystać z dysku zarządzanego_. Ten niestandardowy komunikat jest wyświetlany, gdy zasób zostanie odrzucony lub dla niezgodnych zasobów podczas regularnej oceny.

1. Wybierz pozycję **dalej** w dolnej części strony lub kartę **Przegląd + tworzenie** w górnej części strony, aby przejść do następnego segmentu kreatora przypisania.

1. Przejrzyj wybrane opcje, a następnie wybierz pozycję **Utwórz** w dolnej części strony.

Teraz możesz zidentyfikować niezgodne zasoby, aby zrozumieć stan zgodności środowiska.

## <a name="identify-non-compliant-resources"></a>Identyfikowanie niezgodnych zasobów

Wybierz pozycję **Zgodność** w lewej części strony. Znajdź utworzone przypisanie zasad _Audit VMs that do not use managed disks_ (Przeprowadź inspekcję maszyn wirtualnych, które nie używają dysków zarządzanych).

:::image type="content" source="./media/assign-policy-portal/policy-compliance.png" alt-text="Zrzut ekranu przedstawiający szczegóły zgodności na stronie zgodność zasad." border="false":::

Jeśli istnieją jakiekolwiek zasoby niezgodne z nowym przypisaniem, zostaną one wyświetlone w obszarze **Niezgodne zasoby**.

Jeśli warunek zostanie oceniony dla istniejących zasobów i okaże się prawdziwy, zasoby te zostaną oznaczone jako niezgodne z zasadami. W poniższej tabeli przedstawiono sposób, w jaki różne akcje dotyczące zasad wpływają na ocenę warunku na potrzeby wynikowego stanu zgodności. Chociaż nie widzisz logiki oceny w Azure Portal, zostaną wyświetlone wyniki stanu zgodności. Wynik stanu zgodności może być zgodny lub niezgodny.

| Stan zasobu | Efekt | Ocena zasad | Stan zgodności |
| --- | --- | --- | --- |
| Nowa czy zaktualizowana? | Inspekcja, modyfikowanie, AuditIfNotExist | Prawda | Niezgodne |
| Nowa czy zaktualizowana? | Inspekcja, modyfikowanie, AuditIfNotExist | Fałsz | Zgodny |
| Exists | Deny, Audit, append, Modify, DeployIfNotExist, AuditIfNotExist | Prawda | Niezgodne |
| Exists | Deny, Audit, append, Modify, DeployIfNotExist, AuditIfNotExist | Fałsz | Zgodny |

> [!NOTE]
> Efekty DeployIfNotExist i AuditIfNotExist wymagają, aby instrukcja IF była prawdziwa, a warunek istnienia ma wartość FALSE, aby nie był zgodny. W przypadku wartości TRUE warunek IF wyzwala ocenę warunku istnienia dla powiązanych zasobów.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć utworzone przypisanie, wykonaj następujące kroki:

1. Wybierz pozycję **Zgodność** (lub **Przypisania**) w lewej części strony usługi Azure Policy i znajdź utworzone przypisanie zasad _Audit VMs that do not use managed disks_ (Przeprowadź inspekcję maszyn wirtualnych, które nie używają dysków zarządzanych).

1. Kliknij prawym przyciskiem myszy _maszyny wirtualne inspekcji, które nie używają przypisania zasad dysków zarządzanych_ , i wybierz pozycję **Usuń przypisanie**.

   :::image type="content" source="./media/assign-policy-portal/delete-assignment.png" alt-text="Zrzut ekranu przedstawiający korzystanie z menu kontekstowego, aby usunąć przypisanie ze strony zgodność." border="false":::

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start przypisano definicję zasad do zakresu i oceniono raport jego zgodności.
Definicja zasady zawiera sprawdzenie, czy wszystkie zasoby w ramach zakresu są zgodne, oraz określenie niezgodnych zasobów.

Aby dowiedzieć się więcej na temat przypisywania zasad w celu sprawdzenia zgodności nowych zasobów, przejdź do samouczka:

> [!div class="nextstepaction"]
> [Tworzenie zasad i zarządzanie nimi](./tutorials/create-and-manage.md)