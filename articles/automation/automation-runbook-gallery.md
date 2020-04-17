---
title: Galerie elementów Runbook i modułów dla usługi Azure Automation
description: Elementy runbook i moduły firmy Microsoft i społeczności są dostępne do zainstalowania i użycia w środowisku usługi Azure Automation.  W tym artykule opisano, jak uzyskać dostęp do tych zasobów i przyczynić się do śmiwojenia uruchomieniu do galerii.
services: automation
ms.subservice: process-automation
ms.date: 03/20/2019
ms.topic: conceptual
ms.openlocfilehash: 90b475e275598363314c8f131911fe12650cd3df
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535557"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Galerie elementów Runbook i modułów dla usługi Azure Automation

Zamiast tworzyć własne elementy runbook i moduły w usłudze Azure Automation, można uzyskać dostęp do scenariuszy, które zostały już utworzone przez firmę Microsoft i społeczność. Podręczniki i [moduły](#modules-in-powershell-gallery) programu PowerShell można uzyskać z programów PowerShell Gallery i [python runbooks](#use-python-runbooks) z Galerii Centrum skryptów. Możesz również przyczynić się do społeczności, udostępniając [scenariusze, które opracujesz](#add-a-powershell-runbook-to-the-gallery). 

## <a name="runbooks-in-powershell-gallery"></a>Podręczniki runbook w galerii programu PowerShell

[Galeria programu PowerShell](https://www.powershellgallery.com/packages) udostępnia wiele śmięty śmięty firmy Microsoft i społeczności, które można zaimportować do usługi Azure Automation. Aby go użyć, pobierz podręcznik runbook z galerii lub możesz bezpośrednio importować elementy runbook z galerii lub z konta automatyzacji w witrynie Azure portal.

> [!NOTE]
> Graficzne programy runbook nie są obsługiwane w programie PowerShell Gallery.

Można importować tylko bezpośrednio z galerii programu PowerShell przy użyciu witryny Azure portal. Tej funkcji nie można wykonać przy użyciu programu PowerShell.

> [!NOTE]
> Należy sprawdzić poprawność zawartości wszystkich życiówek, które można uzyskać z galerii programu PowerShell i zachować szczególną ostrożność podczas instalowania i uruchamiania ich w środowisku produkcyjnym.

## <a name="modules-in-powershell-gallery"></a>Moduły w galerii programu PowerShell

Moduły programu PowerShell zawierają polecenia cmdlet, których można używać w podręcznikach runbook, a istniejące moduły, które można zainstalować w usłudze Azure Automation, są dostępne w [Galerii programu PowerShell.](https://www.powershellgallery.com) Tę galerię można uruchomić w witrynie Azure portal i zainstalować ją bezpośrednio w usłudze Azure Automation. Można je również pobrać i zainstalować ręcznie.

## <a name="common-solutions-available-in-powershell-gallery"></a>Typowe rozwiązania dostępne w programie PowerShell Gallery

Poniższa lista zawiera kilka kreśleń eks-owych, które zapewniają rozwiązania typowych scenariuszy. Aby uzyskać pełną listę uruchomieniu książek utworzonych przez zespół usługi Azure Automation, zobacz [Profil AzureAutomationTeam](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

   * [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) — importuje najnowszą wersję wszystkich modułów na koncie automatyzacji z galerii programu PowerShell.
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) — konfiguruje diagnostykę i usługę Azure Log Analytics w celu odbierania dzienników usługi Azure Automation zawierających stan zadania i strumienie zadań.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) — kopiuje plik zdalny z maszyny wirtualnej systemu Windows Azure.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) — kopiuje plik lokalny na maszynie wirtualnej platformy Azure.

## <a name="import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Importowanie uruchomieniu programu PowerShell z galerii uruchomieniu za pomocą witryny Azure Portal

1. W witrynie Azure Portal otwórz konto usługi Automation.
2. Wybierz **galerię twórców** w obszarze **Automatyzacja procesów**.
3. Wybierz **źródło: Galeria programu PowerShell**.
4. Znajdź odpowiedni element galerii i wybierz go, aby wyświetlić jego szczegóły. Po lewej stronie można wprowadzić dodatkowe parametry wyszukiwania dla wydawcy i typu.

   ![Galeria przeglądania](media/automation-runbook-gallery/browse-gallery.png)

5. Kliknij **pozycję Wyświetl projekt źródłowy,** aby wyświetlić element w [Centrum skryptów TechNet](https://gallery.technet.microsoft.com/).
6. Aby zaimportować element, kliknij go, aby wyświetlić jego szczegóły, a następnie kliknij przycisk **Importuj**.

   ![Przycisk Importuj](media/automation-runbook-gallery/gallery-item-detail.png)

7. Opcjonalnie zmień nazwę likwidu, a następnie kliknij przycisk **OK,** aby zaimportować projekt runbook.
8. Zestaw runbook pojawi się na karcie **Elementy runbook** dla konta Automatyzacja.

## <a name="add-a-powershell-runbook-to-the-gallery"></a>Dodawanie umocowania programu PowerShell do galerii

Firma Microsoft zachęca do dodawania życiówek do galerii programu PowerShell, które uważasz za przydatne dla innych klientów. Galeria programu PowerShell akceptuje moduły programu PowerShell i skrypty programu PowerShell. Podręcznik można dodać, [przesyłając go do galerii programu PowerShell](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

## <a name="import-a-module-from-the-module-gallery-with-the-azure-portal"></a>Importowanie modułu z galerii modułów za pomocą portalu Azure

1. W witrynie Azure Portal otwórz konto usługi Automation.
2. Wybierz **moduły** w obszarze **Zasoby udostępnione,** aby otworzyć listę modułów.
3. Kliknij **pozycję Przeglądaj galerię** u góry strony.

   ![Galeria modułów](media/automation-runbook-gallery/modules-blade.png)

4. Na stronie Przeglądaj galerię możesz wyszukiwać według następujących pól:

   * Nazwa modułu
   * Tagi
   * Autor
   * Nazwa zasobu polecenia cmdlet/DSC

5. Znajdź interesujący Cię moduł i wybierz go, aby wyświetlić jego szczegóły.

   Podczas przechodzenia do szczegółów w określonym module można wyświetlić więcej informacji. Te informacje obejmują łącze z powrotem do galerii programu PowerShell, wszelkie wymagane zależności i wszystkie polecenia cmdlet lub zasobów DSC, które zawiera moduł.

   ![Szczegóły modułu programu PowerShell](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. Aby zainstalować moduł bezpośrednio w usłudze Azure Automation, kliknij przycisk **Importuj**.
7. W okienku Importuj można wyświetlić nazwę modułu do zaimportowania. Jeśli wszystkie zależności są zainstalowane, przycisk **OK** jest aktywowany. Jeśli brakuje zależności, należy zaimportować te zależności, zanim będzie można zaimportować ten moduł.
8. W okienku Importuj kliknij przycisk **OK,** aby zaimportować moduł. Usługa Azure Automation importuje moduł do konta, wyodrębnia metadane dotyczące modułu i poleceń cmdlet. Ta akcja może potrwać kilka minut, ponieważ każde działanie musi zostać wyodrębnione.
9. Otrzymasz początkowe powiadomienie, że moduł jest wdrażany i kolejne powiadomienie po jego zakończeniu.
10. Po zaimportowaniu modułu można zobaczyć dostępne działania. Można użyć zasobów modułu w yśgielnikach i zasobach DSC.

> [!NOTE]
> Moduły, które obsługują tylko rdzeń programu PowerShell, nie są obsługiwane w usłudze Azure Automation i nie można ich zaimportować w witrynie Azure portal ani wdrożyć bezpośrednio z galerii programu PowerShell.

## <a name="use-python-runbooks"></a>Używanie żyła życowych obrażeń pythonowych

Podręczniki pythona są dostępne w [galerii Centrum skryptów](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=). Możesz przekazać elementy runbook python do galerii Centrum skryptów, klikając **pozycję Prześlij wkład**. Gdy to zrobisz, upewnij się, że dodasz tag `Python` podczas przesyłania wkładu.

> [!NOTE]
> Aby przesłać zawartość do [Centrum skryptów,](https://gallery.technet.microsoft.com/scriptcenter)potrzebujesz co najmniej 100 punktów.

## <a name="request-a-runbook-or-module"></a>Żądanie eksty lub modułu

Możesz wysyłać żądania do [User Voice](https://feedback.azure.com/forums/246290-azure-automation/).  Jeśli potrzebujesz pomocy przy pisaniu runbooka lub masz pytanie dotyczące programu PowerShell, opublikuj pytanie na naszym [forum.](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)

## <a name="next-steps"></a>Następne kroki

* Aby rozpocząć pracę z systemami runbook, zobacz [Zarządzanie systemem runbook w usłudze Azure Automation](manage-runbooks.md).
* Aby zrozumieć różnice między programem PowerShell a przepływem pracy programu PowerShell z licznikami ekwałc, zobacz [Uczenie przepływu pracy programu PowerShell](automation-powershell-workflow.md).
* Aby uzyskać więcej informacji na temat programu PowerShell, w tym odwoływania się do języka i modułów szkoleniowych, zobacz [Dokumenty programu PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
