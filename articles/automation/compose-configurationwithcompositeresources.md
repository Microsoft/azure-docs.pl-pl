---
title: Tworzenie konfiguracji DSC w konfiguracji stanu automatyzacji platformy Azure przy użyciu zasobów złożonych
description: Dowiedz się, jak redagować konfiguracje przy użyciu zasobów złożonych w konfiguracji stanu automatyzacji platformy Azure.
keywords: powershell dsc, konfiguracja żądanego stanu, powershell dsc azure, zasoby złożone
services: automation
ms.subservice: dsc
ms.date: 08/21/2018
ms.topic: conceptual
ms.openlocfilehash: 7e7625ae95d5355ae1122a16ea4828eed5f78c73
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682926"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-using-composite-resources"></a>Tworzenie konfiguracji DSC w konfiguracji stanu automatyzacji platformy Azure przy użyciu zasobów złożonych

Jeśli trzeba zarządzać zasobem z więcej niż jedną żądaną konfiguracją stanu (DSC), najlepszą ścieżką jest użycie [zasobów złożonych](/powershell/scripting/dsc/resources/authoringresourcecomposite). Zasób złożony jest zagnieżdżoną i sparametryzowaną konfiguracją używaną jako zasób DSC w ramach innej konfiguracji. Użycie zasobów złożonych umożliwia tworzenie złożonych konfiguracji, umożliwiając jednocześnie indywidualne zarządzanie i tworzenie podstawowych zasobów złożonych.

Usługa Azure Automation umożliwia [importowanie i kompilowanie zasobów złożonych.](automation-dsc-compile.md) Po zaimportowaniu zasobów złożonych do konta automatyzacji można użyć konfiguracji stanu automatyzacji platformy Azure za pośrednictwem **konfiguracji stanu (funkcji DSC** w witrynie Azure portal.

## <a name="composing-a-configuration-from-composite-resources"></a>Tworzenie konfiguracji z zasobów złożonych

Aby można było przypisać konfigurację wykonaną z zasobów złożonych w witrynie Azure portal, należy skomponować konfigurację. Kompozycja używa **konfiguracji redagowania** na stronie Konfiguracja stanu (DSC), podczas gdy na **karcie Konfiguracje** lub **Skompilowane konfiguracje.**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie kliknij pozycję **Wszystkie zasoby,** a następnie nazwę konta automatyzacji.
1. Na stronie Konto automatyzacji wybierz pozycję **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**.
1. Na stronie Konfiguracja stanu (DSC) kliknij kartę **Konfiguracje** lub **Skompilowane konfiguracje,** a następnie kliknij polecenie **Redagowanie konfiguracji** w menu u góry strony.
1. W kroku **Podstawy** podaj nową nazwę konfiguracji (wymagane) i kliknij dowolne miejsce w wierszu każdego zasobu złożonego, który chcesz uwzględnić w nowej konfiguracji, a następnie kliknij przycisk **Dalej** lub kliknij krok **Kod źródłowy.** W następujących krokach `PSExecutionPolicy` wybraliśmy i `RenameAndDomainJoin` zasoby złożone.
   ![Zrzut ekranu przedstawiający krok podstawowy strony konfiguracji redagowania](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. Krok **Kod źródłowy** pokazuje, jak wygląda skomponowana konfiguracja wybranych zasobów złożonych. Można zobaczyć scalanie wszystkich parametrów i jak są one przekazywane do zasobu złożonego. Po zakończeniu przeglądania nowego kodu źródłowego kliknij przycisk **Dalej** lub kliknij krok **Parametry.**
   ![Zrzut ekranu przedstawiający krok kodu źródłowego strony konfiguracji redagowania](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. W kroku **Parametry** parametr dla każdego zasobu złożonego jest narażony, dzięki czemu można podać wartości. Jeśli parametr ma opis, jest on wyświetlany obok pola parametru. Jeśli parametr jest `PSCredential` typu, lista rozwijana zawiera listę **poświadczeń** obiektów na bieżącym koncie automatyzacji. Dostępna jest również opcja **+ Dodaj poświadczenia.** Po podaniu wszystkich wymaganych parametrów kliknij przycisk **Zapisz i skompiluj**.
   ![Zrzut ekranu przedstawiający krok parametrów strony konfiguracji redagowania](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

Po zapisaniu nowej konfiguracji jest przesyłana do kompilacji. Stan zadania kompilacji można wyświetlać jak każdą zaimportowaną konfigurację. Aby uzyskać więcej informacji, zobacz [Wyświetlanie zadania kompilacji](automation-dsc-getting-started.md#viewing-a-compilation-job).

Po pomyślnym zakończeniu kompilacji nowa konfiguracja pojawi się na karcie **Skompilowane konfiguracje.** Następnie można przypisać konfigurację do węzła zarządzanego, wykonując czynności opisane w [yznaczanie węzła do innej konfiguracji węzła.](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration)

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje o tym, zobacz [Wprowadzenie do konfiguracji stanu automatyzacji platformy Azure](automation-dsc-getting-started.md).
- Aby dowiedzieć się, jak dołączać węzły, zobacz [Maszyny dołączające do zarządzania przez konfigurację stanu automatyzacji platformy Azure](automation-dsc-onboarding.md).
- Aby dowiedzieć się więcej o kompilowaniu konfiguracji DSC, aby można je było przypisać do węzłów docelowych, zobacz [Kompilowanie konfiguracji w konfiguracji stanu automatyzacji platformy Azure](automation-dsc-compile.md).
- Aby uzyskać informacje o poleceniach cmdlet programu PowerShell, zobacz [polecenia cmdlet konfiguracji stanu automatyzacji platformy Azure](/powershell/module/azurerm.automation/#automation).
- Aby uzyskać informacje o cenach, zobacz [Cennik konfiguracji stanu usługi Azure Automation .](https://azure.microsoft.com/pricing/details/automation/)
- Aby zobaczyć przykład użycia konfiguracji stanu automatyzacji platformy Azure w potoku ciągłego wdrażania, zobacz [Ciągłe wdrażanie przy użyciu konfiguracji stanu automatyzacji platformy Azure i czekoladowe](automation-dsc-cd-chocolatey.md).
