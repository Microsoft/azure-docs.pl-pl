---
title: Używanie Azure Automation elementów Runbook i modułów w programie Galeria programu PowerShell
description: W tym artykule opisano sposób używania elementów Runbook i modułów z firmy Microsoft i społeczności w Galeria programu PowerShell.
services: automation
ms.subservice: process-automation
ms.date: 01/08/2021
ms.topic: conceptual
ms.openlocfilehash: 590220782a7f43e785cc7885e68eefa99afb7d1d
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98049120"
---
# <a name="use-runbooks-and-modules-in-powershell-gallery"></a>Używanie elementów runbook i modułów w Galerii programu PowerShell

Zamiast tworzyć własne elementy Runbook i moduły w Azure Automation, możesz uzyskać dostęp do scenariuszy, które zostały już utworzone przez firmę Microsoft i społeczność. Elementy Runbook i [moduły](#modules-in-powershell-gallery) programu PowerShell można pobrać z Galeria programu PowerShell i [elementów Runbook języka Python](#use-python-runbooks) z Azure Automation organizacji usługi GitHub. Możesz również współtworzyć społeczność, udostępniając [scenariusze, które opracowujesz](#add-a-powershell-runbook-to-the-gallery).

> [!NOTE]
> Trwa wycofywanie centrum skryptów TechNet. Wszystkie elementy Runbook z centrum skryptów w galerii elementów Runbook zostały przeniesione do naszej [organizacji usługi GitHub](https://github.com/azureautomation) w usłudze UserVoice, aby uzyskać więcej informacji, zobacz [tutaj](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337).

## <a name="runbooks-in-powershell-gallery"></a>Elementy Runbook w Galeria programu PowerShell

[Galeria programu PowerShell](https://www.powershellgallery.com/packages) oferuje różne elementy Runbook firmy Microsoft i społeczność, które można zaimportować do Azure Automation. Aby użyć jednego z nich, pobierz element Runbook z galerii lub bezpośrednio Importuj elementy Runbook z galerii lub z konta usługi Automation w Azure Portal.

> [!NOTE]
> Graficzne elementy Runbook nie są obsługiwane w Galeria programu PowerShell.

Można importować tylko bezpośrednio z Galeria programu PowerShell przy użyciu Azure Portal. Nie można wykonać tej funkcji przy użyciu programu PowerShell.

> [!NOTE]
> Należy sprawdzić poprawność zawartości wszystkich elementów Runbook uzyskanych z Galeria programu PowerShell, a także zachować wyjątkową ostrożność instalowania i uruchamiania ich w środowisku produkcyjnym.

## <a name="modules-in-powershell-gallery"></a>Moduły w Galeria programu PowerShell

Moduły programu PowerShell zawierają polecenia cmdlet, których można używać w elementach Runbook, oraz istniejące moduły, które można zainstalować w Azure Automation są dostępne w [Galeria programu PowerShell](https://www.powershellgallery.com). Możesz uruchomić tę galerię z Azure Portal i zainstalować ją bezpośrednio w Azure Automation. Można je również pobrać i zainstalować ręcznie.

## <a name="common-scenarios-available-in-powershell-gallery"></a>Typowe scenariusze dostępne w Galeria programu PowerShell

Poniższa lista zawiera kilka elementów Runbook, które obsługują typowe scenariusze. Aby uzyskać pełną listę elementów Runbook utworzonych przez zespół Azure Automation, zobacz [profil AzureAutomationTeam](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

   * [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) — importuje najnowszą wersję wszystkich modułów na koncie usługi Automation z Galeria programu PowerShell.
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) — konfiguruje Diagnostyka Azure i log Analytics do odbierania dzienników Azure Automation zawierających stan zadania i strumienie zadań.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) — kopiuje plik zdalny z maszyny wirtualnej platformy Microsoft Azure.
   * [Copy-ItemToAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) — kopiuje plik lokalny na maszynę wirtualną platformy Azure.

## <a name="import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Importowanie elementu Runbook programu PowerShell z galerii elementów Runbook za pomocą Azure Portal

1. W witrynie Azure Portal otwórz konto usługi Automation.
2. Wybierz pozycję **Galeria elementów Runbook** w obszarze **Automatyzacja procesów**.
3. Wybierz **Źródło: Galeria programu PowerShell**.
4. Znajdź żądany element galerii i wybierz go, aby wyświetlić jego szczegóły. Po lewej stronie możesz wprowadzić dodatkowe parametry wyszukiwania dla wydawcy i typu.

   ![Galeria przeglądania](media/automation-runbook-gallery/browse-gallery.png)

5. Kliknij pozycję **Wyświetl projekt źródłowy** , aby wyświetlić element w [organizacji usługi GitHub Azure Automation](https://github.com/azureautomation).
6. Aby zaimportować element, kliknij go, aby wyświetlić jego szczegóły, a następnie kliknij przycisk **Importuj**.

   ![Przycisk Importuj](media/automation-runbook-gallery/gallery-item-detail.png)

7. Opcjonalnie Zmień nazwę elementu Runbook, a następnie kliknij przycisk **OK** , aby zaimportować element Runbook.
8. Element Runbook jest wyświetlany na karcie **elementy Runbook** dla konta usługi Automation.

## <a name="add-a-powershell-runbook-to-the-gallery"></a>Dodawanie elementu Runbook programu PowerShell do galerii

Firma Microsoft zachęca do dodawania elementów Runbook do Galeria programu PowerShell, które prawdopodobnie będą przydatne dla innych klientów. Galeria programu PowerShell akceptuje modułów programu PowerShell i skryptów programu PowerShell. Element Runbook można dodać, [przekazując go do Galeria programu PowerShell](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

## <a name="import-a-module-from-the-module-gallery-with-the-azure-portal"></a>Zaimportuj moduł z galerii modułów z Azure Portal

1. W witrynie Azure Portal otwórz konto usługi Automation.
2. Wybierz pozycję **moduły** w obszarze **udostępnione zasoby** , aby otworzyć listę modułów.
3. W górnej części strony kliknij pozycję **Przeglądaj Galerię** .

   ![Galeria modułów](media/automation-runbook-gallery/modules-blade.png)

4. Na stronie Przeglądaj Galerię można wyszukać następujące pola:

   * Nazwa modułu
   * Tagi
   * Autor
   * Nazwa zasobu polecenia cmdlet/konfiguracji DSC

5. Znajdź interesujący Cię moduł i wybierz go, aby wyświetlić jego szczegóły.

   Podczas przechodzenia do szczegółów konkretnego modułu można wyświetlić więcej informacji. Te informacje obejmują łącze z powrotem do Galeria programu PowerShell, wszystkie wymagane zależności oraz wszystkie polecenia cmdlet lub zasoby DSC, które zawiera moduł.

   ![Szczegóły modułu programu PowerShell](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. Aby zainstalować moduł bezpośrednio w Azure Automation, kliknij przycisk **Importuj**.
7. W okienku importowania można zobaczyć nazwę modułu do zaimportowania. Jeśli wszystkie zależności są zainstalowane, przycisk **OK** jest aktywowany. W przypadku braku zależności należy zaimportować te zależności przed zaimportowaniem tego modułu.
8. W okienku importowania kliknij przycisk **OK** , aby zaimportować moduł. Podczas Azure Automation importuje moduł do konta, wyodrębnia on metadane dotyczące modułu i poleceń cmdlet. Ta akcja może potrwać kilka minut, ponieważ należy wyodrębnić każde działanie.
9. Otrzymujesz początkowe powiadomienie, że moduł jest wdrażany, i inne powiadomienie po jego zakończeniu.
10. Po zaimportowaniu modułu można zobaczyć dostępne działania. Zasobów modułu można używać w elementach Runbook i zasobach DSC.

> [!NOTE]
> Moduły obsługujące tylko rdzeń programu PowerShell nie są obsługiwane w programie Azure Automation i nie można ich zaimportować w Azure Portal ani wdrożyć bezpośrednio w Galeria programu PowerShell.

## <a name="use-python-runbooks"></a>Korzystanie z elementów Runbook języka Python

Elementy Runbook języka Python są dostępne w [organizacji usługi GitHub Azure Automation](https://github.com/azureautomation). Podczas współtworzenia repozytorium GitHub Dodaj tag **(temat GitHub): python3** podczas przekazywania Twojego wkładu.

## <a name="request-a-runbook-or-module"></a>Żądaj elementu Runbook lub modułu

Możesz wysyłać żądania do [głosu użytkownika](https://feedback.azure.com/forums/246290-azure-automation/).  Jeśli potrzebujesz pomocy przy pisaniu elementu Runbook lub masz pytania dotyczące programu PowerShell, Opublikuj pytanie na naszej stronie pytań i odpowiedzi na [pytania&firmy Microsoft](/answers/topics/azure-automation.html).

## <a name="next-steps"></a>Następne kroki

* Aby rozpocząć pracę z elementem Runbook programu PowerShell, zobacz [Samouczek: Tworzenie elementu Runbook programu PowerShell](learn/automation-tutorial-runbook-textual-powershell.md).
* Aby korzystać z elementów Runbook, zobacz [Zarządzanie elementami Runbook w Azure Automation](manage-runbooks.md).
* Aby uzyskać szczegółowe informacje na temat programu PowerShell, zobacz dokumentację [programu PowerShell](/powershell/scripting/overview).
* Aby uzyskać informacje dotyczące poleceń cmdlet programu PowerShell, zobacz [AZ. Automation](/powershell/module/az.automation).
