---
title: Tworzenie konfiguracji DSC
description: W tym artykule opisano sposób redagowania konfiguracji przy użyciu zasobów złożonych w konfiguracji stanu Azure Automation.
keywords: PowerShell DSC, Konfiguracja żądanego stanu, środowisko PowerShell DSC Azure, zasoby złożone
services: automation
ms.subservice: dsc
ms.date: 08/21/2018
ms.topic: conceptual
ms.openlocfilehash: 1b1bbb12412deec6ecac8cf1ffd47a00f778862e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98894732"
---
# <a name="compose-dsc-configurations"></a>Tworzenie konfiguracji DSC

Jeśli potrzebujesz zarządzać zasobem z więcej niż jedną konfiguracją żądanego stanu (DSC), najlepszą ścieżką jest użycie [zasobów złożonych](/powershell/scripting/dsc/resources/authoringresourcecomposite). Zasób złożony jest zagnieżdżoną i sparametryzowana konfiguracją używaną jako zasób DSC w innej konfiguracji. Użycie zasobów złożonych umożliwia tworzenie złożonych konfiguracji, a jednocześnie umożliwia zarządzanie i kompilowanie podstawowych zasobów złożonych.

Azure Automation umożliwia [Importowanie i kompilowanie zasobów złożonych](automation-dsc-compile.md). Po zaimportowaniu zasobów złożonych na konto usługi Automation możesz użyć konfiguracji stanu Azure Automation za pomocą funkcji **Konfiguracja stanu (DSC)** w Azure Portal.

## <a name="compose-a-configuration"></a>Redaguj konfigurację

Aby można było przypisać konfigurację z zasobów złożonych w Azure Portal, należy utworzyć konfigurację. Kompozycja używa **konfiguracji redagowania** na stronie Konfiguracja stanu (DSC) na karcie **konfiguracje** lub **skompilowane konfiguracje** .

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie kliknij pozycję **wszystkie zasoby** , a następnie nazwę konta usługi Automation.
1. Na stronie konto usługi Automation wybierz pozycję **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**.
1. Na stronie Konfiguracja stanu (DSC) kliknij kartę **konfiguracje** lub **skompilowane konfiguracje** , a następnie kliknij pozycję **Utwórz konfigurację** w menu w górnej części strony.
1. Na etapie **podstawowe** Podaj nową nazwę konfiguracji (wymagana) i kliknij w dowolnym miejscu w wierszu każdego zasobu złożonego, który ma zostać uwzględniony w nowej konfiguracji, a następnie kliknij przycisk **dalej** lub kliknij krok **kod źródłowy** . W przypadku następujących kroków zostały wybrane `PSExecutionPolicy` i `RenameAndDomainJoin` złożone zasoby.
   ![Zrzut ekranu przedstawiający podstawowe kroki strony Konfiguracja redagowania](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. Krok **kod źródłowy** pokazuje, jak wygląda pozłożona konfiguracja wybranych zasobów złożonych. Można zobaczyć scalanie wszystkich parametrów i sposób przekazywania ich do zasobu złożonego. Po zakończeniu przeglądania nowego kodu źródłowego kliknij przycisk **dalej** lub kliknij etap **parametrów** .
   ![Zrzut ekranu przedstawiający krok kod źródłowy na stronie redagowanie konfiguracji](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. W kroku **Parametry** , parametr dla każdego zasobu złożonego jest uwidoczniony, aby można było podać wartości. Jeśli parametr ma opis, zostanie wyświetlony obok pola parametru. Jeśli parametr jest `PSCredential` typu, lista rozwijana zawiera listę obiektów **poświadczeń** na bieżącym koncie usługi Automation. Dostępna jest również opcja **+ Add a Credential** . Po podaniu wszystkich wymaganych parametrów kliknij przycisk **Zapisz i skompiluj**.
   ![Zrzut ekranu przedstawiający krok parametrów na stronie Konfiguracja redagowania](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

## <a name="submit-the-configuration-for-compilation"></a>Prześlij konfigurację dla kompilacji

Po zapisaniu nowej konfiguracji zostanie ona przesłana do kompilacji. Możesz wyświetlić stan zadania kompilacji, tak jak w przypadku dowolnej zaimportowanej konfiguracji. Aby uzyskać więcej informacji, zobacz [Wyświetlanie zadania kompilacji](automation-dsc-getting-started.md#view-a-compilation-job).

Po pomyślnym zakończeniu kompilacji Nowa konfiguracja zostanie wyświetlona na karcie **skompilowane konfiguracje** . Następnie można przypisać konfigurację do węzła zarządzanego przy użyciu kroków w sekcji [ponowne przypisywanie węzła do konfiguracji innej węzła](automation-dsc-getting-started.md#reassign-a-node-to-a-different-node-configuration).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak włączyć węzły, zobacz [Włączanie konfiguracji stanu Azure Automation](automation-dsc-onboarding.md).
- Aby dowiedzieć się więcej na temat kompilowania konfiguracji DSC, aby można było przypisać je do węzłów docelowych, zobacz [Kompilowanie konfiguracji DSC w konfiguracji stanu Azure Automation](automation-dsc-compile.md).
- Aby zapoznać się z przykładem użycia konfiguracji stanu Azure Automation w potoku ciągłego wdrażania, zobacz [Konfigurowanie ciągłego wdrażania z czekoladą](automation-dsc-cd-chocolatey.md).
- Aby uzyskać informacje o cenach, zobacz [Cennik konfiguracji stanu Azure Automation](https://azure.microsoft.com/pricing/details/automation/).
- Aby uzyskać informacje dotyczące poleceń cmdlet programu PowerShell, zobacz [AZ. Automation](/powershell/module/az.automation).
