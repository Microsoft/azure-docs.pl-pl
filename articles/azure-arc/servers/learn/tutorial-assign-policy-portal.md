---
title: Samouczek — nowe przypisanie zasad za pomocą Azure Portal
description: W tym samouczku użyjemy Azure Portal przypisania Azure Policy do identyfikowania niezgodnych zasobów.
ms.topic: tutorial
ms.date: 04/21/2021
ms.openlocfilehash: 20cb8125a157a3ce97c88278c99f90936b449b65
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831658"
---
# <a name="tutorial-create-a-policy-assignment-to-identify-non-compliant-resources"></a>Samouczek: tworzenie przypisania zasad w celu zidentyfikowania niezgodnych zasobów

Pierwszym krokiem do zrozumienia pojęcia zgodności na platformie Azure jest określenie obecnej sytuacji dotyczącej Twoich zasobów. Azure Policy obsługuje inspekcję stanu serwera z obsługą usługi Arc przy użyciu zasad konfiguracji gościa. Zasady konfiguracji gościa nie stosują konfiguracji, tylko przejmują ustawienia wewnątrz maszyny. Ten samouczek zawiera instrukcje tworzenia i przypisywania zasad, które z serwerów z usługą Arc nie mają zainstalowanego agenta usługi Log Analytics.

Po zakończeniu tego procesu pomyślnie zidentyfikujesz maszyny, które nie mają zainstalowanego agenta usługi Log Analytics dla systemu Windows lub Linux. Są one _niezgodne_ z przypisaniem zasad.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="create-a-policy-assignment"></a>Tworzenie przypisania zasad

W tym samouczku utworzysz przypisanie zasad i przypiszesz _\[ podgląd]: Agent_ usługi Log Analytics powinien być zainstalowany w definicji zasad Azure Arc Linux.

1. Uruchom usługę Azure Policy w witrynie Azure Portal, klikając opcję **Wszystkie usługi** i następnie wyszukując i wybierając opcję **Zasada**.

   :::image type="content" source="./media/tutorial-assign-policy-portal/search-policy.png" alt-text="Wyszukiwanie zasad we wszystkich usługach" border="false":::

1. Wybierz pozycję **Przypisania** w lewej części strony usługi Azure Policy. Przypisanie to zasady, które zostały przypisane do określonego zakresu.

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-assignment.png" alt-text="Wybieranie strony Przypisania na stronie Przegląd zasad" border="false":::

1. Wybierz pozycję **Przypisz zasady** w górnej części strony **Zasady — Przypisania**.

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-assign-policy.png" alt-text="Przypisywanie definicji zasad ze strony Przypisania" border="false":::

1. Na stronie **Przypisywanie zasad** wybierz **Zakres**, klikając wielokropek i wybierając grupę zarządzania lub subskrypcję. Opcjonalnie możesz wybrać grupę zasobów. Zakres określa, jakie zasoby lub grupy zasobów są wymuszane w ramach przypisania zasad. Następnie kliknij przycisk **Wybierz** w dolnej części strony **Zakres**.

   W tym przykładzie jest używana **subskrypcja Par nie tylko.** Twoja subskrypcja będzie inna.

1. Zasoby można wykluczyć na podstawie opcji **Zakres**. **Wykluczenia** są uruchamiane o jeden poziom niżej od poziomu opcji **Zakres**. Pole **Wykluczenia** jest opcjonalne, więc na razie można je pozostawić puste.

1. W polu **Definicja zasad** wybierz wielokropek, aby otworzyć listę dostępnych definicji. Usługa Azure Policy zawiera wbudowane definicje zasad, których możesz używać. Dostępnych jest wiele wbudowanych definicji, na przykład:

   - Enforce tag and its value (Wymuś tag i jego wartość)
   - Apply tag and its value (Zastosuj tag i jego wartość)
   - Dziedziczenie tagu z grupy zasobów, jeśli go nie ma

   Aby uzyskać częściową listę dostępnych wbudowanych zasad, zobacz [Azure Policy przykłady.](../../../governance/policy/samples/index.md)

1. Przeszukaj listę definicji zasad, aby znaleźć _\[ podgląd]:_ Agent usługi Log Analytics powinien być zainstalowany w definicji maszyn z systemem Windows Azure Arc, jeśli agent serwerów z obsługą usługi Arc został włączony na maszynie z systemem Windows. W przypadku maszyny z systemem Linux znajdź odpowiedni _\[ podgląd]:_ Agent usługi Log Analytics powinien być zainstalowany w definicji zasad Azure Arc Linux. Kliknij te zasady, a następnie kliknij pozycję **Wybierz**.

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-available-definition.png" alt-text="Wyszukiwanie prawidłowej definicji zasad" border="false":::

1. W polu **Nazwa przypisania** jest automatycznie wpisywana nazwa wybranej zasady, ale można ją zmienić. Na przykład pozostaw opcję Wersja _\[ zapoznawcza]:_ Agent usługi Log Analytics powinien być zainstalowany na maszynach z systemem Windows Azure Arc lub w wersji _\[ zapoznawczej]:_ Agent usługi Log Analytics powinien być zainstalowany na maszynach z systemem Linux Azure Arc w zależności od wybranego agenta. Można również dodać opcjonalny **Opis**. Opis zawiera szczegóły dotyczące danego przypisania zasad.
   Pole **Przypisane przez** jest wypełniane automatycznie w zależności od tego, kto jest zalogowany. To pole jest opcjonalne, dzięki czemu można wprowadzić wartości niestandardowe.

1. Pozostaw pole **Utwórz tożsamość zarządzaną** niezaznaczone. To pole _należy_ zaznaczyć, gdy zasada lub inicjatywa obejmuje zasadę z efektem [deployIfNotExists](../../../governance/policy/concepts/effects.md#deployifnotexists). Ponieważ w przypadku zasad stosowanych na potrzeby tego przewodnika Szybki start tak nie jest, pozostaw to pole puste. Aby uzyskać więcej informacji, zobacz tematy dotyczące [tożsamości zarządzanych](../../../active-directory/managed-identities-azure-resources/overview.md) i [sposobu działania zabezpieczeń w zakresie korygowania](../../../governance/policy/how-to/remediate-resources.md#how-remediation-security-works).

1. Kliknij przycisk **Przypisz**.

Teraz możesz zidentyfikować niezgodne zasoby, aby zrozumieć stan zgodności środowiska.

## <a name="identify-non-compliant-resources"></a>Identyfikowanie niezgodnych zasobów

Wybierz pozycję **Zgodność** w lewej części strony. Następnie znajdź agenta usługi Log Analytics w wersji **\[ zapoznawczej]:** Agent usługi Log Analytics powinien być zainstalowany na maszynach z systemem Windows Azure Arc lub w wersji **\[ zapoznawczej]:** Agent usługi Log Analytics powinien być zainstalowany w utworzonym przypisaniu zasad usługi Log Analytics Azure Arc Linux.

:::image type="content" source="./media/tutorial-assign-policy-portal/policy-compliance.png" alt-text="Szczegóły zgodności na stronie Zgodność zasad" border="false":::

Jeśli istnieją jakiekolwiek zasoby niezgodne z nowym przypisaniem, zostaną one wyświetlone w obszarze **Niezgodne zasoby**.

Jeśli warunek zostanie oceniony dla istniejących zasobów i okaże się prawdziwy, zasoby te zostaną oznaczone jako niezgodne z zasadami. W poniższej tabeli przedstawiono sposób, w jaki różne akcje dotyczące zasad wpływają na ocenę warunku na potrzeby wynikowego stanu zgodności. Mimo że logika oceny nie jest wyświetlana w Azure Portal, wyświetlane są wyniki stanu zgodności. Wynik stanu zgodności może być zgodny lub niezgodny.

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

1. Wybierz pozycję Zgodność **(lub** Przypisania **)** w lewej części strony usługi Azure Policy i znajdź pozycję Wersja zapoznawcza]: Agent usługi Log Analytics powinien być zainstalowany na maszynach z systemem Windows Azure Arc lub w wersji zapoznawczej]: Agent usługi Log Analytics powinien być zainstalowany w utworzonym przypisaniu zasad usługi **\[ Azure Arc Log Analytics.** **\[**

1. Kliknij prawym przyciskiem myszy przypisanie zasad i wybierz **polecenie Usuń przypisanie.**

   :::image type="content" source="./media/tutorial-assign-policy-portal/delete-assignment.png" alt-text="Usuwanie przypisania ze strony Zgodność" border="false":::

## <a name="next-steps"></a>Następne kroki

W tym samouczku przypisano definicję zasad do zakresu i oceniono jej raport zgodności. Definicja zasady zawiera sprawdzenie, czy wszystkie zasoby w ramach zakresu są zgodne, oraz określenie niezgodnych zasobów. Teraz możesz monitorować maszynę serwerów z włączoną obsługą Azure Arc, włączając [szczegółowe informacje o maszynie wirtualnej.](../../../azure-monitor/vm/vminsights-overview.md)

Aby dowiedzieć się, jak monitorować i wyświetlać wydajność, uruchomiony proces i ich zależności z komputera, przejdź do samouczka:

> [!div class="nextstepaction"]
> [Włączanie szczegółowych informacji o maszynach wirtualnych](tutorial-enable-vm-insights.md)