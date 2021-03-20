---
title: Samouczek — nowe przypisanie zasad z Azure Portal
description: W tym samouczku użyto Azure Portal do utworzenia przypisania Azure Policy w celu zidentyfikowania niezgodnych zasobów.
ms.topic: tutorial
ms.date: 10/07/2020
ms.openlocfilehash: 9a07e490525ce532f8f843b30b3b83715e65ce3c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91826596"
---
# <a name="tutorial-create-a-policy-assignment-to-identify-non-compliant-resources"></a>Samouczek: Tworzenie przypisania zasad w celu zidentyfikowania niezgodnych zasobów

Pierwszym krokiem do zrozumienia pojęcia zgodności na platformie Azure jest określenie obecnej sytuacji dotyczącej Twoich zasobów. Azure Policy obsługuje inspekcję stanu serwera z włączonym łukiem przy użyciu zasad konfiguracji gościa. Zasady konfiguracji gościa nie dotyczą konfiguracji, ale tylko ustawienia inspekcji w obrębie maszyny. Ten samouczek przeprowadzi Cię przez proces tworzenia i przypisywania zasad, co oznacza, że na serwerach z obsługą łuku nie zainstalowano agenta Log Analytics.

Po zakończeniu tego procesu pomyślnie zidentyfikujesz maszyny, na których nie zainstalowano agenta Log Analytics dla systemu Windows lub Linux. Są one _niezgodne_ z przypisaniem zasad.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="create-a-policy-assignment"></a>Tworzenie przypisania zasad

W tym samouczku utworzysz przypisanie zasad i przypiszesz _\[ Podgląd]: Agent log Analytics powinien być zainstalowany w definicji zasad komputera z systemem Linux Azure_ .

1. Uruchom usługę Azure Policy w witrynie Azure Portal, klikając opcję **Wszystkie usługi** i następnie wyszukując i wybierając opcję **Zasada**.

   :::image type="content" source="./media/tutorial-assign-policy-portal/search-policy.png" alt-text="Wyszukaj zasady w obszarze wszystkie usługi" border="false":::

1. Wybierz pozycję **Przypisania** w lewej części strony usługi Azure Policy. Przypisanie to zasady, które zostały przypisane do określonego zakresu.

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-assignment.png" alt-text="Strona wybierania przydziałów na stronie Przegląd zasad" border="false":::

1. Wybierz pozycję **Przypisz zasady** w górnej części strony **Zasady — Przypisania**.

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-assign-policy.png" alt-text="Przypisywanie definicji zasad ze strony przypisań" border="false":::

1. Na stronie **Przypisywanie zasad** wybierz **Zakres**, klikając wielokropek i wybierając grupę zarządzania lub subskrypcję. Opcjonalnie możesz wybrać grupę zasobów. Zakres określa, jakie zasoby lub grupy zasobów są wymuszane w ramach przypisania zasad. Następnie kliknij przycisk **Wybierz** w dolnej części strony **Zakres**.

   W tym przykładzie jest stosowana subskrypcja programu **Parnell Aerospace** . Twoja subskrypcja będzie inna.

1. Zasoby można wykluczyć na podstawie opcji **Zakres**. **Wykluczenia** są uruchamiane o jeden poziom niżej od poziomu opcji **Zakres**. Pole **Wykluczenia** jest opcjonalne, więc na razie można je pozostawić puste.

1. W polu **Definicja zasad** wybierz wielokropek, aby otworzyć listę dostępnych definicji. Usługa Azure Policy zawiera wbudowane definicje zasad, których możesz używać. Dostępnych jest wiele wbudowanych definicji, na przykład:

   - Enforce tag and its value (Wymuś tag i jego wartość)
   - Apply tag and its value (Zastosuj tag i jego wartość)
   - Dziedzicz tag z grupy zasobów, jeśli brakuje

   Aby zapoznać się z częściową listą dostępnych wbudowanych zasad, zobacz [Azure Policy Samples](../../../governance/policy/samples/index.md).

1. Przeszukaj listę definicji zasad, aby znaleźć wersję _\[ zapoznawczą]: na komputerze z systemem Windows należy zainstalować agenta log Analytics w definicji komputerów z systemem Windows Azure_ . W przypadku komputera z systemem Linux Znajdź odpowiednią wersję _\[ zapoznawczą]: Agent log Analytics powinien być zainstalowany w definicji zasad komputera z systemem Linux Azure_ . Kliknij te zasady, a następnie kliknij pozycję **Wybierz**.

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-available-definition.png" alt-text="Wyszukiwanie prawidłowej definicji zasad" border="false":::

1. W polu **Nazwa przypisania** jest automatycznie wpisywana nazwa wybranej zasady, ale można ją zmienić. Na potrzeby tego przykładu pozostaw wersję _\[ zapoznawczą]: Agent log Analytics powinien być zainstalowany na komputerze z systemem Windows Azure ARC lub w_ _\[ wersji zapoznawczej]: Agent log Analytics należy zainstalować na komputerach z systemem Linux Azure Arc_ , w zależności od tego, który został wybrany. Można również dodać opcjonalny **Opis**. Opis zawiera szczegóły dotyczące danego przypisania zasad.
   Pole **Przypisane przez** jest wypełniane automatycznie w zależności od tego, kto jest zalogowany. To pole jest opcjonalne, dzięki czemu można wprowadzić wartości niestandardowe.

1. Pozostaw pole **Utwórz tożsamość zarządzaną** niezaznaczone. To pole _należy_ zaznaczyć, gdy zasada lub inicjatywa obejmuje zasadę z efektem [deployIfNotExists](../../../governance/policy/concepts/effects.md#deployifnotexists). Ponieważ w przypadku zasad stosowanych na potrzeby tego przewodnika Szybki start tak nie jest, pozostaw to pole puste. Aby uzyskać więcej informacji, zobacz tematy dotyczące [tożsamości zarządzanych](../../../active-directory/managed-identities-azure-resources/overview.md) i [sposobu działania zabezpieczeń w zakresie korygowania](../../../governance/policy/how-to/remediate-resources.md#how-remediation-security-works).

1. Kliknij przycisk **Przypisz**.

Teraz możesz zidentyfikować niezgodne zasoby, aby zrozumieć stan zgodności środowiska.

## <a name="identify-non-compliant-resources"></a>Identyfikowanie niezgodnych zasobów

Wybierz pozycję **Zgodność** w lewej części strony. Następnie zlokalizuj **\[ Podgląd]: Agent log Analytics powinien być zainstalowany na komputerze z systemem Windows Azure ARC lub w** **\[ wersji zapoznawczej]: Agent log Analytics powinien być zainstalowany w** utworzonym przypisaniu zasad komputera z systemem Linux Azure.

:::image type="content" source="./media/tutorial-assign-policy-portal/policy-compliance.png" alt-text="Szczegóły zgodności na stronie zgodność zasad" border="false":::

Jeśli istnieją jakiekolwiek zasoby niezgodne z nowym przypisaniem, zostaną one wyświetlone w obszarze **Niezgodne zasoby**.

Jeśli warunek zostanie oceniony dla istniejących zasobów i okaże się prawdziwy, zasoby te zostaną oznaczone jako niezgodne z zasadami. W poniższej tabeli przedstawiono sposób, w jaki różne akcje dotyczące zasad wpływają na ocenę warunku na potrzeby wynikowego stanu zgodności. Chociaż nie widzisz logiki oceny w Azure Portal, zostaną wyświetlone wyniki stanu zgodności. Wynik stanu zgodności może być zgodny lub niezgodny.

| **Stan zasobu** | **Efekt** | **Ocena zasad** | **Stan zgodności** |
| --- | --- | --- | --- |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | Prawda | Niezgodne |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | Fałsz | Zgodny |
| Nowy | Audit, AuditIfNotExist\* | Prawda | Niezgodne |
| Nowy | Audit, AuditIfNotExist\* | Fałsz | Zgodny |

\* Efekty Append, DeployIfNotExist i AuditIfNotExist wymagają instrukcji IF z wartością TRUE.
Ponadto efekty wymagają, aby warunek istnienia miał wartość FALSE, aby być niezgodnymi. W przypadku wartości TRUE warunek IF wyzwala ocenę warunku istnienia dla powiązanych zasobów.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć utworzone przypisanie, wykonaj następujące kroki:

1. Wybierz pozycję **zgodność** (lub **przypisania**) w lewej części strony Azure Policy i znajdź **\[ Podgląd]: log Analytics Agent powinien być zainstalowany na komputerze z systemem Windows Azure ARC lub w** **\[ wersji zapoznawczej]: Agent log Analytics powinien być zainstalowany w** utworzonym przypisaniu zasad komputera z systemem Linux Azure.

1. Kliknij prawym przyciskiem myszy przypisanie zasad i wybierz polecenie **Usuń przypisanie**.

   :::image type="content" source="./media/tutorial-assign-policy-portal/delete-assignment.png" alt-text="Usuwanie przypisania ze strony zgodność" border="false":::

## <a name="next-steps"></a>Następne kroki

W tym samouczku przypisano definicję zasad do zakresu i oceniono raport zgodności. Definicja zasady zawiera sprawdzenie, czy wszystkie zasoby w ramach zakresu są zgodne, oraz określenie niezgodnych zasobów. Teraz możesz przystąpić do monitorowania maszyny z włączonymi serwerami usługi Azure ARC przy użyciu Azure Monitor dla maszyn wirtualnych.

Aby dowiedzieć się, jak monitorować i wyświetlać wydajność, uruchomiony proces i ich zależności z komputera, przejdź do samouczka:

> [!div class="nextstepaction"]
> [Włącz Azure Monitor dla maszyn wirtualnych](tutorial-enable-vm-insights.md)