---
title: Często zadawane pytania dotyczące szablonu ARM
description: Często zadawane pytania dotyczące szablonów Azure Resource Manager (szablony ARM).
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: e64253aa79cfaeb2655bb091d038dc7c98b198bd
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2021
ms.locfileid: "103419409"
---
# <a name="frequently-asked-questions-about-arm-templates"></a>Często zadawane pytania dotyczące szablonów ARM

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące szablonów Azure Resource Manager (szablony ARM).

## <a name="getting-started"></a>Wprowadzenie

* **Co to są szablony ARM i dlaczego warto z nich korzystać?**

  Szablony ARM to pliki JSON, w których definiujesz, co chcesz wdrożyć na platformie Azure. Szablony ułatwiają Implementowanie rozwiązania infrastruktury jako kodu dla platformy Azure. Organizacja może wielokrotnie i niezawodnie wdrażać wymaganą infrastrukturę w różnych środowiskach.

  Aby dowiedzieć się więcej o tym, jak szablony usługi ARM pomagają zarządzać infrastrukturą platformy Azure, zobacz [co to są szablony ARM?](overview.md)

* **Jak mogę zacząć korzystać z szablonów?**

  Aby uprościć tworzenie szablonów ARM, potrzebne są odpowiednie narzędzia. Zalecamy zainstalowanie [Visual Studio Code](https://code.visualstudio.com/) i [rozszerzenia narzędzi Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools). Aby zapoznać się z szybkim wprowadzeniem do tych narzędzi, zobacz [Szybki Start: Tworzenie szablonów ARM przy użyciu Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).

  Aby dowiedzieć się więcej na temat tworzenia szablonów ARM, Zacznij od [początku serii samouczków na temat szablonów ARM](template-tutorial-create-first-template.md). Te samouczki przeprowadzimy krok po kroku w procesie konstruowania szablonu ARM. Dowiesz się więcej o różnych sekcjach szablonu i sposobach ich współdziałania. Ta zawartość jest również dostępna jako [moduł Microsoft Learn](/learn/modules/authoring-arm-templates/).

* **Czy należy używać szablonów ARM lub Terraform do wdrażania na platformie Azure?**

  Użyj opcji, która jest Najlepsza. Obie usługi pomagają w automatyzowaniu wdrożeń na platformie Azure.

  Uważamy, że istnieją korzyści wynikające z używania szablonów usługi ARM w porównaniu z innymi usługami infrastruktury jako kodu. Aby dowiedzieć się więcej na temat tych korzyści, zobacz [Dlaczego warto wybrać Szablony ARM?](overview.md#why-choose-arm-templates)

## <a name="build-2020"></a>Kompilacja 2020

* **Twoja prezentacja została pominięta w firmie Microsoft Build 2020. Czy prezentacja jest dostępna do wyświetlania?**

  Tak, [Obejrzyj ją w dowolnym momencie](https://mybuild.microsoft.com/sessions/82984db4-37a4-4ed3-bf8b-13298841ed18?source=sessions).

* **Gdzie można uzyskać więcej informacji na temat nowych funkcji ogłoszonych podczas kompilacji?**

  Aby uzyskać ogólne informacje o funkcjach, które pracujemy, Dołącz do [grupy Yammer wdrożenia doradców platformy Azure](https://aka.ms/ARMMeet).

  Aby dowiedzieć się więcej o nowym języku szablonów, [zarejestruj się w celu otrzymywania powiadomień](https://aka.ms/armLangUpdates).

  Aby dowiedzieć się więcej na temat specyfikacji szablonu, zobacz [Azure Resource Manager specyfikacji szablonu (wersja zapoznawcza)](template-specs.md).

## <a name="creating-and-testing-templates"></a>Tworzenie i testowanie szablonów

* **Gdzie mogę dowiedzieć się więcej o najlepszych rozwiązaniach dotyczących szablonów usługi ARM?**

  Aby zapoznać się z zaleceniami dotyczącymi sposobu implementacji szablonów, zobacz [najlepsze rozwiązania dotyczące szablonów ARM](template-best-practices.md). Po utworzeniu szablonu Uruchom [zestaw narzędzi ARM test Toolkit](https://github.com/azure/arm-ttk). Sprawdza, czy szablon spełnia zalecane rozwiązania.

* **Mam konfigurację mojego środowiska za pomocą portalu. Czy istnieje jakiś sposób pobrania szablonu z istniejącej grupy zasobów?**

  Tak, możesz [wyeksportować szablon](export-template-portal.md) z grupy zasobów. Wyeksportowany szablon to dobry punkt początkowy do uczenia się na temat szablonów, ale prawdopodobnie zechcesz ją poprawić przed użyciem jej w środowisku produkcyjnym.

  Podczas eksportowania szablonu można wybrać zasoby, które mają zostać uwzględnione w szablonie.

* **Czy można utworzyć grupę zasobów w szablonie ARM i wdrożyć do niej zasoby?**

  Tak, po wdrożeniu szablonu na poziomie subskrypcji platformy Azure można utworzyć grupę zasobów w szablonie. Przykład tworzenia grupy zasobów i wdrażania zasobów można znaleźć w temacie [zasoby i Grupa zasobów](deploy-to-subscription.md#resource-groups).

* **Czy mogę utworzyć subskrypcję w szablonie ARM?**

  Tak. Aby uzyskać więcej informacji, zobacz programowe [Tworzenie subskrypcji platformy Azure przy użyciu najnowszych interfejsów API](../../cost-management-billing/manage/programmatically-create-subscription-enterprise-agreement.md).

* **Jak przetestować szablon przed jego wdrożeniem?**

  Przed wdrożeniem programu zalecamy uruchomienie [zestawu narzędzi ARM test Toolkit](https://github.com/azure/arm-ttk) oraz [operacji wykonywania czynności wykonywanych](template-deploy-what-if.md) w Twoich szablonach. Zestaw narzędzi test Toolkit sprawdza, czy szablon używa najlepszych rozwiązań. Zawiera on ostrzeżenia, gdy identyfikuje zmiany, które mogą poprawić sposób implementacji szablonu.

  Operacja działania warunkowego pokazuje zmiany wprowadzone przez szablon w Twoim środowisku. Przed wdrożeniem można zobaczyć niezamierzone zmiany. Co-jeśli również zwraca wszystkie błędy, które może wykryć podczas weryfikacji stanu wstępnego. Na przykład jeśli szablon zawiera błąd syntaktyczny, zwracany jest ten błąd. Zwraca również wszelkie błędy, które mogą ustalić stan końcowy wdrożonych zasobów. Na przykład jeśli szablon wdraża konto magazynu o nazwie, która jest już używana, co oznacza, że funkcja zwraca ten błąd.

* **Gdzie można znaleźć informacje o właściwościach, które są dostępne dla każdego typu zasobu?**

  VS Code udostępnia funkcję IntelliSense do pracy z właściwościami zasobów. Można również wyświetlić informacje o [szablonie](/azure/templates/) dotyczące właściwości i opisów.

* **Muszę utworzyć wiele wystąpień typu zasobu. Jak mogę utworzyć iterator w moim szablonie?**

  Użyj elementu Copy, aby określić więcej niż jedno wystąpienie. Można używać kopiowania [zasobów](copy-resources.md), [Właściwości](copy-properties.md), [zmiennych](copy-variables.md)i danych [wyjściowych](copy-outputs.md).

## <a name="template-language"></a>Język szablonu

* **Słyszę, że pracujesz nad nowym szablonem szablonu. Gdzie mogę dowiedzieć się więcej na ten temat?**

  Aby dowiedzieć się więcej o nowym języku, zobacz [co to jest Bicep (wersja zapoznawcza)?](bicep-overview.md).

* **Czy istnieje plan obsługi tworzenia szablonów w programie YAML?**

  Obecnie nie ma planu obsługi YAML. Sądzimy, że nowy język szablonów będzie oferować rozwiązanie, które jest łatwiejsze w użyciu niż YAML lub JSON.

* **Czy nadal mogę pisać szablony w formacie JSON po wydaniu nowego języka szablonu?**

  Tak, możesz nadal używać szablonów JSON.

* **Czy oferujesz narzędzie do konwersji szablonów JSON do nowego języka szablonów?**

  Tak. Zobacz [konwertowanie szablonów ARM między elementami JSON i Bicep](bicep-decompile.md).

## <a name="template-specs"></a>Specyfikacje szablonu

* **Jak rozpocząć pracę z wersjami zapoznawczymi specyfikacji szablonów?**

  Zainstaluj najnowszą wersję programu PowerShell lub interfejsu wiersza polecenia platformy Azure. W przypadku Azure PowerShell Użyj [wersji 5.0.0 lub nowszej](/powershell/azure/install-az-ps). W przypadku interfejsu wiersza polecenia platformy Azure Użyj [wersji 2.14.2 lub nowszej](/cli/azure/install-azure-cli).

* **Jak są powiązane specyfikacje szablonu i plany platformy Azure?**

  Plany platformy Azure będą używać specyfikacji szablonu w swojej implementacji przez zastąpienie `blueprint definition` zasobu `template spec` zasobem. Udostępnimy ścieżkę migracji w celu przekonwertowania definicji planu na specyfikację szablonu, ale interfejsy API definicji strategii nadal będą obsługiwane. Brak zmian w `blueprint assignment` zasobie. Plany nie będą działać przez użytkownika w celu utworzenia środowiska regulowanego na platformie Azure.

* **Czy specyfikacje szablonu zastępują połączone szablony?**

  Nie, ale specyfikacje szablonu są zaprojektowane tak, aby dobrze działały z połączonymi szablonami. Nie musisz przenosić połączonego szablonu do publicznie dostępnego punktu końcowego przed wdrożeniem szablonu nadrzędnego. Zamiast tego należy spakować szablon nadrzędny wraz z jego artefaktami podczas tworzenia specyfikacji szablonu.

* **Czy specyfikacje szablonu mogą być współużytkowane przez subskrypcje?**

  Tak, mogą one być używane w różnych subskrypcjach, o ile użytkownik ma dostęp do odczytu do specyfikacji szablonu. Specyfikacje szablonu nie mogą być używane między dzierżawcami.

## <a name="scripts-in-templates"></a>Skrypty w szablonach

* **Czy mogę dołączyć skrypt w moim szablonie do zadań, które nie są możliwe w szablonie?**

  Tak, użyj [skryptów wdrażania](deployment-script-template.md). W szablonach można dołączać Azure PowerShell lub skrypty interfejsu wiersza polecenia platformy Azure. Ta funkcja jest dostępna w wersji zapoznawczej.

* **Czy nadal mogę używać niestandardowych rozszerzeń skryptów i konfiguracji żądanego stanu (DSC)?**

  Te opcje są nadal dostępne i nie zostały zmienione. Skrypty wdrażania są przeznaczone do wykonywania akcji, które nie są związane z gościem maszyny wirtualnej. Jeśli konieczne jest uruchomienie skryptu w systemie operacyjnym hosta na maszynie wirtualnej, lepszym wyborem może być rozszerzenie niestandardowego skryptu i/lub DSC. Jednak skrypty wdrażania mają zalety, takie jak ustawienie czasu trwania.

* **Czy skrypty wdrażania są obsługiwane w Azure Government?**

  Tak, możesz użyć skryptów wdrażania w US Gov Arizona i US Gov Wirginia.

## <a name="preview-changes-before-deployment"></a>Podgląd zmian przed wdrożeniem

* **Czy mogę wyświetlić podgląd zmian, które nastąpią przed wdrożeniem szablonu?**

  Tak, użyj [funkcji "co jeśli"](template-deploy-what-if.md). Oblicza bieżący stan środowiska i porównuje go ze stanem, który będzie istniał po wdrożeniu. Można sprawdzić podsumowania zmian, aby upewnić się, że szablon nie zawiera żadnych nieoczekiwanych wyników.

* **Czy mogę użyć co-jeśli z trybami przyrostowymi i pełnymi?**

  Tak, obsługiwane są oba [tryby wdrażania](deployment-modes.md) . Aby zapoznać się z przykładem użycia trybu przyrostowego, zobacz [Uruchamianie operacji działania co](template-deploy-what-if.md#run-what-if-operation). Aby zapoznać się z przykładem użycia trybu kompletnego, zobacz [potwierdzenie usunięcia](template-deploy-what-if.md#confirm-deletion).

* **Czy w przypadku pracy z szablonami połączonymi?**

  Tak, co w przypadku szacowania stanu szablonu nadrzędnego i jego połączonych szablonów.

* **Czy mogę użyć co zrobić w potoku platformy Azure?**

  Tak, możesz użyć opcji co, aby sprawdzić, czy potok powinien być kontynuowany.

* **Gdy używam co to zrobić, widzę zmiany właściwości, które nie znajdują się w szablonie. Czy jest oczekiwany "szum"?**

  Pracujemy nad zmniejszeniem szumów. Pomóż nam udoskonalić przez przesłanie problemów w naszym repozytorium GitHub tutaj: https://aka.ms/WhatIfIssues

## <a name="template-visualizer"></a>Wizualizator szablonu

* **Czy istnieje sposób wizualizacji szablonu ARM i jego zasobów?**

  Mamy [rozszerzenie vs Code](https://aka.ms/ARMVisualizer) współtworzone przez społeczność, które wykonuje doskonałe zadanie wizualizacji szablonu ARM. Pokazuje zasoby, które są wdrażane, oraz relacje między nimi.

* **Czy mogę użyć wizualizatora szablonu poza VS Code?**

  Wizualizator szablonu jest wyświetlany w portalu. Aby uzyskać więcej informacji, Obejrzyj [krótką sesję z kompilacji](https://mybuild.microsoft.com/sessions/0525094b-1fd2-4f69-bfd6-6d2fff6ffe5f?source=sessions).

## <a name="deployment-limits"></a>Limity wdrożenia

* **Jak wiele grup zasobów można wdrożyć w ramach jednej operacji wdrażania?**

  W przeszłości ten limit dotyczył pięciu grup zasobów. Został niedawno zwiększony do 800 grup zasobów. Aby uzyskać więcej informacji, zobacz [Tworzenie grup zasobów i zasobów na poziomie subskrypcji](deploy-to-subscription.md).

* **Wystąpił błąd dotyczący ograniczenia do 800 wdrożeń w historii wdrażania. Co mam zrobić?**

  Zmieniamy sposób utrzymywania historii wdrożenia dla grupy zasobów. W przeszłości należy ręcznie usunąć wdrożenia z tej historii, aby uniknąć tego błędu. Począwszy od czerwca 2020, firma Microsoft automatycznie usunie wdrożenia z historii, gdy zbliżasz się do limitu. Aby uzyskać więcej informacji, zobacz [Automatyczne usuwanie z historii wdrażania](deployment-history-deletions.md).

  Usunięcie wdrożenia z historii nie ma wpływu na wdrożone zasoby.

## <a name="templates-and-devops"></a>Szablony i DevOps

* **Czy mogę zintegrować szablony ARM z Azure Pipelines?**

  Tak. Aby dowiedzieć się, jak używać szablonów i potoków, zobacz [Samouczek: Ciągła integracja szablonów ARM z Azure Pipelines](deployment-tutorial-pipeline.md) i [integrowanie szablonów ARM z Azure Pipelines](add-template-to-azure-pipelines.md).

* **Czy mogę użyć akcji usługi GitHub do wdrożenia szablonu?**

  Tak, zobacz [wdrażanie szablonów ARM przy użyciu akcji usługi GitHub](deploy-github-actions.md).

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z wprowadzeniem do szablonów ARM, zobacz [co to są szablony ARM?](overview.md).
