---
title: Wdrażanie maszyny wirtualnej Amazon Web Services przy użyciu Azure Automation elementu Runbook
description: W tym artykule opisano, jak zautomatyzować tworzenie maszyny wirtualnej Amazon Web Services.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 456a7e4f07b2416e1d2037205574f2e7149e70e2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86185946"
---
# <a name="deploy-an-amazon-web-services-vm-with-a-runbook"></a>Wdrażanie maszyny wirtualnej Amazon Web Services za pomocą elementu Runbook

W tym artykule dowiesz się, jak skorzystać z Azure Automation, aby zainicjować obsługę administracyjną maszyny wirtualnej w ramach subskrypcji usługi Amazon Web Service (AWS) i nadać maszynie wirtualnej konkretną nazwę, która AWS odnosi się do "tagowania".

## <a name="prerequisites"></a>Wymagania wstępne

Musisz mieć konto Azure Automation i subskrypcję Amazon Web Services (AWS). Aby uzyskać więcej informacji na temat konfigurowania konta Azure Automation i konfigurowania go przy użyciu poświadczeń subskrypcji usługi AWS, przejrzyj temat [Konfigurowanie uwierzytelniania przy użyciu Amazon Web Services](automation-config-aws-account.md). To konto powinno zostać utworzone lub zaktualizowane przy użyciu poświadczeń subskrypcji usługi AWS przed kontynuowaniem, ponieważ odwołuje się do tego konta w poniższych sekcjach.

## <a name="deploy-amazon-web-services-powershell-module"></a>Wdróż moduł Amazon Web Services PowerShell

Element Runbook aprowizacji maszyn wirtualnych używa modułu AWS PowerShell do wykonywania swoich zadań. Wykonaj następujące kroki, aby dodać moduł do konta usługi Automation, które jest skonfigurowane z poświadczeniami subskrypcji AWS.  

1. Otwórz przeglądarkę internetową i przejdź do [Galeria programu PowerShell](https://www.powershellgallery.com/packages/AWSPowerShell/) i kliknij **przycisk Wdróż do Azure Automation**.<br><br> ![Import modułu AWS PS](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Nastąpi przekierowanie do strony logowania platformy Azure, a po uwierzytelnieniu zostanie nakierowany do Azure Portal i przedstawiony na następującej stronie:<br><br> ![Strona importowania modułu](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Wybierz konto usługi Automation, którego chcesz użyć, a następnie kliknij przycisk **OK** , aby rozpocząć wdrażanie.

   > [!NOTE]
   > Gdy Azure Automation importuje moduł programu PowerShell, wyodrębnia poleceń cmdlet. Działania nie są wyświetlane, dopóki Automatyzacja nie zakończy się całkowicie Importowanie modułu i wyodrębnienie poleceń cmdlet. Ten proces może potrwać kilka minut.  
   > <br>

1. W witrynie Azure Portal otwórz konto usługi Automation.
2. Kliknij kafelek **zasoby** , a następnie w okienku zasoby wybierz pozycję **moduły**.
3. Na stronie moduły na liście zostanie wyświetlony moduł **AWSPowerShell** .

## <a name="create-aws-deploy-vm-runbook"></a>Utwórz element Runbook AWS wdrożenia maszyny wirtualnej

Po wdrożeniu modułu AWS PowerShell możesz teraz utworzyć element Runbook, aby zautomatyzować Inicjowanie obsługi maszyny wirtualnej w programie AWS przy użyciu skryptu programu PowerShell. W poniższych krokach pokazano, jak używać macierzystego skryptu programu PowerShell w Azure Automation.  

> [!NOTE]
> Aby uzyskać więcej opcji i informacji dotyczących tego skryptu, odwiedź [Galeria programu PowerShell](https://www.powershellgallery.com/packages/New-AwsVM/).
> 

1. Pobierz New-AwsVM skryptu programu PowerShell z Galeria programu PowerShell, otwierając sesję programu PowerShell i wpisując następujące polecenie:<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. W Azure Portal Otwórz konto usługi Automation i wybierz pozycję **elementy Runbook** w obszarze **Automatyzacja procesów**.  
3. Na stronie elementy Runbook wybierz pozycję **Dodaj element Runbook**.
4. W okienku Dodawanie elementu Runbook wybierz pozycję **szybkie tworzenie** , aby utworzyć nowy element Runbook.
5. W okienku właściwości elementu Runbook wpisz nazwę elementu Runbook.
6. Z listy rozwijanej **Typ elementu Runbook** wybierz opcję **PowerShell**, a następnie kliknij przycisk **Utwórz**.<br><br> ![Utwórz okienko elementu Runbook](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
7. Gdy zostanie wyświetlona strona Edytowanie elementu Runbook programu PowerShell, skopiuj i wklej skrypt programu PowerShell do kanwy tworzenia elementów Runbook.<br><br> ![Skrypt programu PowerShell dla elementu Runbook](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    Podczas pracy z przykładowym skryptem programu PowerShell należy pamiętać o następujących kwestiach:

    * Element Runbook zawiera szereg domyślnych wartości parametrów. Oceń wszystkie wartości domyślne i zaktualizuj je w razie potrzeby.
    * Jeśli poświadczenia AWS są przechowywane jako zasób poświadczeń o nazwie inaczej niż w `AWScred` , należy zaktualizować skrypt w wierszu 57, aby dopasować go odpowiednio.  
    * W przypadku korzystania z poleceń interfejsu wiersza polecenia AWS w programie PowerShell, szczególnie w przypadku tego przykładowego elementu Runbook, należy określić region AWS. W przeciwnym razie polecenia cmdlet zakończą się niepowodzeniem. Aby uzyskać więcej informacji, zobacz temat AWS w temacie [Określanie regionu AWS](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) w dokumencie narzędzia AWS Tools for PowerShell.  

8. Aby pobrać listę nazw obrazów z subskrypcji programu AWS, uruchom program PowerShell ISE i zaimportuj moduł AWS PowerShell. Uwierzytelnianie względem AWS przez zastąpienie `Get-AutomationPSCredential` w środowisku ISE za pomocą `AWScred = Get-Credential` . W tej instrukcji zostanie wyświetlony komunikat z prośbą o podanie poświadczeń i podanie identyfikatora klucza dostępu dla nazwy użytkownika i klucza dostępu tajnego dla hasła. 

      ```powershell
        #Sample to get the AWS VM available images
        #Please provide the path where you have downloaded the AWS PowerShell module
        Import-Module AWSPowerShell
        $AwsRegion = "us-west-2"
        $AwsCred = Get-Credential
        $AwsAccessKeyId = $AwsCred.UserName
        $AwsSecretKey = $AwsCred.GetNetworkCredential().Password
   
        # Set up the environment to access AWS
        Set-AwsCredentials -AccessKey $AwsAccessKeyId -SecretKey $AwsSecretKey -StoreAs AWSProfile
        Set-DefaultAWSRegion -Region $AwsRegion
   
        Get-EC2ImageByName -ProfileName AWSProfile
      ```
        
    Zwracane są następujące dane wyjściowe:<br><br>
   ![Pobierz obrazy AWS](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
9. Skopiuj i wklej jedną z nazw obrazu w zmiennej automatyzacji jako odwołanie w elemencie Runbook jako `$InstanceType` . Ponieważ w tym przykładzie korzystasz z bezpłatnej AWSej subskrypcji warstwy, użyj **T2. Micro** dla przykładu elementu Runbook.  
10. Zapisz element Runbook, a następnie kliknij pozycję **Opublikuj** , aby opublikować element Runbook, a następnie przycisk **tak** po wyświetleniu monitu.

### <a name="test-the-aws-vm-runbook"></a>Testowanie elementu Runbook maszyny wirtualnej AWS

1. Sprawdź, czy element Runbook utworzy element zawartości o nazwie `AWScred` do uwierzytelniania w usłudze AWS, lub zaktualizuj skrypt, aby odwołać się do nazwy zasobu poświadczeń.    
2. Sprawdź nowy element Runbook i upewnij się, że wszystkie wartości parametrów zostały zaktualizowane.
Upewnij się, że moduł AWS PowerShell został zaimportowany do Azure Automation.  
3. W Azure Automation Skonfiguruj **rekordy pełne dziennika** i opcjonalnie **Rejestruj rekordy postępu** w obszarze **Rejestrowanie i śledzenie** operacji elementu **Runbook.**<br><br> ![Rejestrowanie i śledzenie elementów Runbook ](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png) .  
4. Kliknij przycisk **Start** , aby uruchomić element Runbook, a następnie kliknij przycisk **OK** po otwarciu okienka uruchamiania elementu Runbook.
5. W okienku Uruchamianie elementu Runbook Podaj nazwę maszyny wirtualnej. Zaakceptuj wartości domyślne dla innych parametrów, które zostały wstępnie skonfigurowane w skrypcie. Kliknij przycisk **OK** , aby uruchomić zadanie elementu Runbook.<br><br> ![Rozpocznij New-AwsVM elementu Runbook](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
6. Zostanie otwarte okienko zadań dla utworzonego zadania elementu Runbook. Zamknij to okienko.
7. Możesz wyświetlić postęp zadania i wyświetlić strumienie wyjściowe, wybierając pozycję **wszystkie dzienniki** w okienku zadania elementu Runbook.<br><br> ![Dane wyjściowe strumienia](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
8. Aby upewnić się, że maszyna wirtualna jest obsługiwana, zaloguj się do konsoli zarządzania AWS, jeśli obecnie nie jest zalogowany.<br><br> ![Wdrożona maszyna wirtualna w konsoli AWS](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Następne kroki
 
* Aby dowiedzieć się, jakie elementy Runbook są obsługiwane, zobacz[Azure Automation typów elementów Runbook](automation-runbook-types.md).
* Aby korzystać z elementów Runbook, zobacz [Zarządzanie elementami Runbook w Azure Automation](manage-runbooks.md).
* Aby uzyskać szczegółowe informacje na temat programu PowerShell, zobacz dokumentację [programu PowerShell](/powershell/scripting/overview).
* Aby uzyskać pomoc techniczną dotyczącą skryptów, zobacz [macierzysta obsługa skryptów programu PowerShell w Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
* Aby uzyskać informacje dotyczące poleceń cmdlet programu PowerShell, zobacz [AZ. Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation).
