---
title: Używanie Azure Automation elementów Runbook i modułów w programie Galeria programu PowerShell
description: W tym artykule opisano, jak używać elementów Runbook i modułów z repozytoriów usługi GitHub firmy Microsoft i Galeria programu PowerShell.
services: automation
ms.subservice: process-automation
ms.date: 04/07/2021
ms.topic: conceptual
ms.openlocfilehash: 2df019888d293cd8a25a34e6f0f4e7dd215c6a41
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107030637"
---
# <a name="use-existing-runbooks-and-modules"></a>Korzystanie z istniejących elementów Runbook i modułów

Zamiast tworzyć własne elementy Runbook i moduły w Azure Automation, możesz uzyskać dostęp do scenariuszy, które zostały już utworzone przez firmę Microsoft i społeczność. Możesz uzyskać informacje dotyczące platformy Azure oraz elementów Runbook programu PowerShell i języka Python z galerii elementów Runbook w Azure Portal oraz [modułów](#modules-in-the-powershell-gallery) i [elementów Runbook](#runbooks-in-the-powershell-gallery) (które mogą lub nie mogą być specyficzne dla platformy Azure) z Galeria programu PowerShell. Możesz również współtworzyć społeczność, udostępniając [scenariusze, które opracowujesz](#contribute-to-the-community).

> [!NOTE]
> Trwa wycofywanie centrum skryptów TechNet. Wszystkie elementy Runbook z centrum skryptów w galerii elementów Runbook zostały przeniesione do naszej [organizacji usługi GitHub](https://github.com/azureautomation) w usłudze UserVoice, aby uzyskać więcej informacji, zobacz [Azure Automation elementów Runbook przenoszonych do serwisu GitHub](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337).

## <a name="import-runbooks-from-github-with-the-azure-portal"></a>Importuj elementy Runbook z usługi GitHub za pomocą Azure Portal

1. W witrynie Azure Portal otwórz konto usługi Automation.
2. Wybierz pozycję **Galeria elementów Runbook** w obszarze **Automatyzacja procesów**.
3. Wybierz **Źródło: GitHub**.
4. Możesz użyć filtrów znajdujących się nad listą, aby zawęzić wyświetlanie według wydawcy, pisania i sortowania. Znajdź żądany element galerii i wybierz go, aby wyświetlić jego szczegóły.

   :::image type="content" source="./media/automation-runbook-gallery/browse-gallery-github.png" alt-text="Przeglądanie galerii elementów Runbook." lightbox="./media/automation-runbook-gallery/browse-gallery-github-expanded.png":::

5. Aby zaimportować element, kliknij przycisk **Importuj** na stronie szczegółów.

   :::image type="content" source="./media/automation-runbook-gallery/gallery-item-import.png" alt-text="Import elementu galerii.":::

6. Opcjonalnie Zmień nazwę elementu Runbook w bloku importu, a następnie kliknij przycisk **OK** , aby zaimportować element Runbook.

   :::image type="content" source="./media/automation-runbook-gallery/gallery-item-import-blade.png" alt-text="Blok importu elementu galerii.":::

7. Element Runbook jest wyświetlany na karcie **elementy Runbook** dla konta usługi Automation.
 
## <a name="runbooks-in-the-powershell-gallery"></a>Elementy Runbook w Galeria programu PowerShell

> [!IMPORTANT]
> Należy sprawdzić poprawność zawartości wszystkich elementów Runbook uzyskanych z Galeria programu PowerShell. Należy zachować szczególną ostrożność podczas instalowania i uruchamiania ich w środowisku produkcyjnym.

[Galeria programu PowerShell](https://www.powershellgallery.com/packages) udostępnia różne elementy Runbook firmy Microsoft i społeczność, które można zaimportować do Azure Automation. Aby użyć jednego z nich, pobierz element Runbook z galerii lub bezpośrednio Importuj elementy Runbook z galerii lub z konta usługi Automation w Azure Portal.

> [!NOTE]
> Graficzne elementy Runbook nie są obsługiwane w Galeria programu PowerShell.

Można importować tylko bezpośrednio z Galeria programu PowerShell przy użyciu Azure Portal. Nie można wykonać tej funkcji przy użyciu programu PowerShell. Procedura jest taka sama jak pokazana w obszarze [Importuj elementy Runbook z usługi GitHub z Azure Portal](#import-runbooks-from-github-with-the-azure-portal), z tą różnicą, że **Źródło** zostanie **Galeria programu PowerShell**.

:::image type="content" source="./media/automation-runbook-gallery/source-runbook-gallery-small.png" alt-text="Wyświetlanie wyboru źródła galerii elementów Runbook." lightbox="./media/automation-runbook-gallery/source-runbook-gallery-large.png":::

## <a name="modules-in-the-powershell-gallery"></a>Moduły w Galeria programu PowerShell

Moduły programu PowerShell zawierają polecenia cmdlet, których można używać w elementach Runbook. Istniejące moduły, które można zainstalować w Azure Automation są dostępne w [Galeria programu PowerShell](https://www.powershellgallery.com). Możesz uruchomić tę galerię z Azure Portal i zainstalować moduły bezpośrednio w Azure Automation lub ręcznie je pobrać i zainstalować.

Możesz również znaleźć moduły do zaimportowania w Azure Portal. Są one wyświetlane dla konta usługi Automation w **galerii modułów** w obszarze **udostępnione zasoby**.

## <a name="common-scenarios-available-in-the-powershell-gallery"></a>Typowe scenariusze dostępne w Galeria programu PowerShell

Poniższa lista zawiera kilka elementów Runbook, które obsługują typowe scenariusze. Aby uzyskać pełną listę elementów Runbook utworzonych przez zespół Azure Automation, zobacz [profil AzureAutomationTeam](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

   * [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) — importuje najnowszą wersję wszystkich modułów na koncie usługi Automation z Galeria programu PowerShell.
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) — konfiguruje Diagnostyka Azure i log Analytics do odbierania dzienników Azure Automation zawierających stan zadania i strumienie zadań.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) — kopiuje plik zdalny z maszyny wirtualnej platformy Microsoft Azure.
   * [Copy-ItemToAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) — kopiuje plik lokalny na maszynę wirtualną platformy Azure.

## <a name="contribute-to-the-community"></a>Współtworzenie do społeczności

Zdecydowanie zachęcamy do współtworzenia i zwiększania Azure Automation społeczność. Udostępniaj wspaniałe elementy Runbook skompilowane ze społecznością. Twoje wkłady będą doceniane!

### <a name="add-a-runbook-to-the-github-runbook-gallery"></a>Dodawanie elementu Runbook do galerii elementów Runbook usługi GitHub

Nowe elementy Runbook programu PowerShell lub Python można dodać do galerii elementów Runbook za pomocą tego przepływu pracy usługi GitHub.

1. Utwórz repozytorium publiczne w serwisie GitHub i Dodaj element Runbook oraz wszystkie inne niezbędne pliki (takie jak readme.md, Description itd.).
1. Dodaj temat `azureautomationrunbookgallery` , aby upewnić się, że repozytorium jest odnajdywane przez naszą usługę, więc można je wyświetlić w galerii elementów Runbook usługi Automation.
1. Jeśli utworzony element Runbook to przepływ pracy programu PowerShell, Dodaj temat `PowerShellWorkflow` . Jeśli jest to element Runbook języka Python 3, Dodaj `Python3` . Dla elementów Runbook platformy Azure nie są wymagane żadne inne tematy, ale zachęcamy do dodawania innych tematów, których można użyć do kategoryzacji i wyszukiwania w galerii elementów Runbook.

   >[!NOTE]
   >Zapoznaj się z istniejącymi elementami Runbook w galerii, takimi jak formatowanie, nagłówki i istniejące Tagi, które mogą być używane (na przykład `Azure Automation` lub `Linux Azure Virtual Machines` ).

Aby zasugerować zmiany w istniejącym elemencie Runbook, należy wysłać do niego żądanie ściągnięcia. 

Jeśli zdecydujesz się na klonowanie i Edytowanie istniejącego elementu Runbook, najlepszym rozwiązaniem jest nadanie mu innej nazwy. Jeśli będziesz używać starej nazwy, zostanie ona dwukrotnie wyświetlona na liście elementów Runbook galerii.

>[!NOTE]
>Zaczekaj co najmniej 12 godzin na synchronizację między usługą GitHub a galerią elementów Runbook usługi Automation dla zaktualizowanych i nowych elementów Runbook.

### <a name="add-a-powershell-runbook-to-the-powershell-gallery"></a>Dodawanie elementu Runbook programu PowerShell do galerii programu PowerShell

Firma Microsoft zachęca do dodawania elementów Runbook do Galeria programu PowerShell, które prawdopodobnie będą przydatne dla innych klientów. Galeria programu PowerShell akceptuje modułów programu PowerShell i skryptów programu PowerShell. Element Runbook można dodać, [przekazując go do Galeria programu PowerShell](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

## <a name="import-a-module-from-the-modules-gallery-in-the-azure-portal"></a>Zaimportuj moduł z galerii modułów w Azure Portal

1. W witrynie Azure Portal otwórz konto usługi Automation.
1. W obszarze **udostępnione zasoby** wybierz pozycję **Galeria modułów** , aby otworzyć listę modułów.

      :::image type="content" source="media/automation-runbook-gallery/modules-blade-sm.png" alt-text="Widok galerii modułów." lightbox="media/automation-runbook-gallery/modules-blade-lg.png":::

1. Na stronie Przeglądaj Galerię można wyszukać następujące pola:

   * Nazwa modułu
   * Tagi
   * Autor
   * Nazwa zasobu polecenia cmdlet/konfiguracji DSC

1. Znajdź interesujący Cię moduł i wybierz go, aby wyświetlić jego szczegóły.

   Podczas przechodzenia do szczegółów konkretnego modułu można wyświetlić więcej informacji. Te informacje obejmują łącze z powrotem do Galeria programu PowerShell, wszystkie wymagane zależności oraz wszystkie polecenia cmdlet lub zasoby DSC, które zawiera moduł.

   :::image type="content" source="media/automation-runbook-gallery/gallery-item-details-blade-sm.png" alt-text="Szczegółowy widok modułu z galerii." lightbox="media/automation-runbook-gallery/gallery-item-details-blade-lg.png":::

1. Aby zainstalować moduł bezpośrednio w Azure Automation, kliknij przycisk **Importuj**.
1. W okienku importowania można zobaczyć nazwę modułu do zaimportowania. Jeśli wszystkie zależności są zainstalowane, przycisk **OK** jest aktywowany. W przypadku braku zależności należy zaimportować te zależności przed zaimportowaniem tego modułu.
1. W okienku importowania kliknij przycisk **OK** , aby zaimportować moduł. Podczas Azure Automation importuje moduł do konta, wyodrębnia on metadane dotyczące modułu i poleceń cmdlet. Ta akcja może potrwać kilka minut, ponieważ należy wyodrębnić każde działanie.
1. Otrzymujesz początkowe powiadomienie, że moduł jest wdrażany, i inne powiadomienie po jego zakończeniu.
1. Po zaimportowaniu modułu można zobaczyć dostępne działania. Zasobów modułu można używać w elementach Runbook i zasobach DSC.

> [!NOTE]
> Moduły obsługujące tylko rdzeń programu PowerShell nie są obsługiwane w programie Azure Automation i nie można ich zaimportować w Azure Portal ani wdrożyć bezpośrednio w Galeria programu PowerShell.

## <a name="request-a-runbook-or-module"></a>Żądaj elementu Runbook lub modułu

Możesz wysyłać żądania do [głosu użytkownika](https://feedback.azure.com/forums/246290-azure-automation/).  Jeśli potrzebujesz pomocy przy pisaniu elementu Runbook lub masz pytania dotyczące programu PowerShell, Opublikuj pytanie na naszej stronie pytań i odpowiedzi na [pytania&firmy Microsoft](/answers/topics/azure-automation.html).

## <a name="next-steps"></a>Następne kroki

* Aby rozpocząć pracę z elementami Runbook programu PowerShell, zobacz [Samouczek: Tworzenie elementu Runbook programu PowerShell](learn/automation-tutorial-runbook-textual-powershell.md).
* Aby korzystać z elementów Runbook, zobacz [Zarządzanie elementami Runbook w Azure Automation](manage-runbooks.md).
* Aby uzyskać więcej informacji na temat skryptów programu PowerShell, zobacz dokumentację [programu PowerShell](/powershell/scripting/overview).
* Aby uzyskać informacje dotyczące poleceń cmdlet programu PowerShell, zobacz [AZ. Automation](/powershell/module/az.automation).
