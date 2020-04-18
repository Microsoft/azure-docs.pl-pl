---
title: Automatyzacja wdrażania maszyny wirtualnej w usługach Amazon Web Services
description: W tym artykule pokazano, jak za pomocą usługi Azure Automation zautomatyzować tworzenie maszyny Wirtualnej usługi sieci Web Amazon
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 52a887d2d934aa2f7e13f0c2fdb4332066aee0e7
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604825"
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Scenariusz usługi Azure Automation — aprowizuj maszynę wirtualną usługi AWS
W tym artykule dowiesz się, jak można wykorzystać usługę Azure Automation do aprowizowania maszyny wirtualnej w subskrypcji usługi Amazon Web Service (AWS) i nadać tej maszynie wirtualnej określoną nazwę — która usługa AWS określa jako "tagowanie" maszyny wirtualnej.

## <a name="prerequisites"></a>Wymagania wstępne
Musisz mieć konto usługi Azure Automation i subskrypcję usług Amazon Web Services (AWS). Aby uzyskać więcej informacji na temat konfigurowania konta usługi Azure Automation i konfigurowania go przy użyciu poświadczeń subskrypcji AWS, zapoznaj [się z omówieniem konfigurowania uwierzytelniania za pomocą usług Amazon Web Services](automation-config-aws-account.md). To konto należy utworzyć lub zaktualizować za pomocą poświadczeń subskrypcji AWS przed kontynuowaniem, jak odwołanie się do tego konta w sekcjach poniżej.

## <a name="deploy-amazon-web-services-powershell-module"></a>Wdrażanie modułu programu PowerShell usług Amazon Web Services
Moduł runbook inicjowania obsługi administracyjnej maszyny Wirtualnej używa modułu AWS PowerShell do wykonywania swojej pracy. Poniższe kroki można wykonać, aby dodać moduł do konta automatyzacji skonfigurowanego przy użyciu poświadczeń subskrypcji usługi AWS.  

1. Otwórz przeglądarkę internetową i przejdź do [Galerii programu PowerShell](https://www.powershellgallery.com/packages/AWSPowerShell/) i kliknij **przycisk Wdrażanie w usłudze Azure Automation**.<br><br> ![Import modułu PS AWS](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Nastąpi przekierowanie do strony logowania platformy Azure i po uwierzytelnieniu zostaniesz przekierowany do witryny Azure portal i przedstawione z następującą stroną:<br><br> ![Strona Moduł importu](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Wybierz konto automatyzacji, którego chcesz użyć, i kliknij przycisk **OK,** aby rozpocząć wdrażanie.

   > [!NOTE]
   > Gdy usługa Azure Automation importuje moduł programu PowerShell, wyodrębnia polecenia cmdlet. Działania nie są wyświetlane, dopóki automatyzacja nie zakończy całkowicie importowanie modułu i wyodrębnianie poleceń cmdlet. Ten proces może potrwać kilka minut.  
   > <br>

1. W witrynie Azure Portal otwórz konto usługi Automation.
2. Kliknij **kafelek Zasoby,** a w okienku Zasoby wybierz pozycję **Moduły**.
3. Na stronie Moduły na liście znajduje się moduł **AWSPowerShell.**

## <a name="create-aws-deploy-vm-runbook"></a>Tworzenie aws wdrożyć runbook maszyn wirtualnych
Po wdrożeniu modułu programu AWS PowerShell można teraz zasilić podręcznik runbook, aby zautomatyzować inicjowanie obsługi administracyjnej maszyny wirtualnej w udręki AWS przy użyciu skryptu programu PowerShell. Poniższe kroki pokazują, jak używać natywnego skryptu programu PowerShell w usłudze Azure Automation.  

> [!NOTE]
> Aby uzyskać więcej opcji i informacji dotyczących tego skryptu, odwiedź [galerię programu PowerShell](https://www.powershellgallery.com/packages/New-AwsVM/).
> 

1. Pobierz skrypt programu PowerShell New-AwsVM z galerii programu PowerShell, otwierając sesję programu PowerShell i wpisując następujące polecenie:<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. W witrynie Azure portal otwórz konto automatyzacji i wybierz **pozycję Elementy runbook w** obszarze **Automatyzacja procesów**.  
3. Na stronie Runbooks wybierz pozycję **Dodaj program runbook**.
4. W okienku Dodawanie eksmisji wybierz pozycję **Szybkie tworzenie,** aby utworzyć nowy projekt runbook.
5. W okienku Właściwości elementu runbook wpisz nazwę elementu runbook.
6. Z listy rozwijanej **Typ ekscesu** wybierz pozycję **PowerShell**, a następnie kliknij przycisk **Utwórz**.<br><br> ![Tworzenie okienka eks- łańszka](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. Po wyświetleniu strony Edytuj podręcznik runbook programu PowerShell skopiuj i wklej skrypt programu PowerShell do obszaru roboczego tworzenia życiówek.<br><br> ![Skrypt programu Runbook PowerShell](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > Podczas pracy z przykładem programu PowerShell należy zwrócić uwagę na następujące kwestie:
    > 
    > * Element runbook zawiera liczbę wartości parametrów domyślnych. Oceń wszystkie wartości domyślne i zaktualizuj w razie potrzeby.
    > * Jeśli poświadczenia AWS zostały zapisane jako zasób `AWScred`poświadczeń o nazwie inaczej niż , należy zaktualizować skrypt w wierszu 57, aby odpowiednio dopasować.  
    > * Podczas pracy z poleceniami interfejsu wiersza polecenia AWS w programie PowerShell, szczególnie w tym przykładowym elemście runbooka, należy określić region AWS. W przeciwnym razie polecenia cmdlet nie powiedzie się. Zobacz temat AWS [Określ region AWS](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) w dokumencie Narzędzia AWS dla programu PowerShell, aby uzyskać więcej informacji.  
    >

7. Aby pobrać listę nazw obrazów z subskrypcji usługi AWS, uruchom program PowerShell ISE i zaimportuj moduł AWS PowerShell. Uwierzytelnij się przed `Get-AutomationPSCredential` AWS, zastępując `AWScred = Get-Credential`w środowisku ISE . Ta instrukcja monituje o poświadczenia i można podać identyfikator klucza dostępu dla nazwy użytkownika i klucza dostępu tajnego dla hasła. 

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
   ![Uzyskaj obrazy AWS](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
8. Skopiuj i wklej jedną z nazw obrazów w zmiennej automatyzacji, o których mowa w bieś runbook jako `$InstanceType`. Ponieważ w tym przykładzie używasz bezpłatnej subskrypcji warstwowej usługi AWS, w przykładzie podręcznika runbook należy użyć **t2.micro.**  
9. Zapisz projekt runbook, a następnie kliknij przycisk **Publikuj,** aby opublikować projekt runbook, a następnie **przycisk Tak** po wyświetleniu monitu.

### <a name="testing-the-aws-vm-runbook"></a>Testowanie runbooka maszyn wirtualnych AWS
Przed przystąpieniem do testowania śmigięcie należy zweryfikować kilka czynności:

* Utworzono `AWScred` zasób wywoływany do uwierzytelniania za pomocą usługi AWS lub zaktualizowano skrypt w celu odwołania się do nazwy zasobu poświadczeń.    
* Moduł AWS PowerShell został zaimportowany w usłudze Azure Automation.  
* Utworzono nowy element runbook i w razie potrzeby zweryfikowano i zaktualizowano wartości parametrów.  
* **Rejestrowanie pełnych rekordów** i opcjonalnie **Rejestrowanie rekordów postępu** w ramach operacji runbook **Rejestrowanie i śledzenie** zostało ustawione na **Włączone**.<br><br> ![Rejestrowanie i śledzenie](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)uruchomieniu wiązka .  

1. Kliknij **przycisk Start,** aby uruchomić program runbook, a następnie kliknij przycisk **OK** po otwarciu okienka Rozpocznij bieganie.
2. W okienku Uruchom program przekaż nazwę maszyny Wirtualnej. Zaakceptuj wartości domyślne dla innych parametrów wstępnie skonfigurowanych w skrypcie. Kliknij **przycisk OK,** aby uruchomić zadanie runbook.<br><br> ![Uruchamianie nowego runbooka AwsVM](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. Okienko Zadania jest otwierane dla utworzonego zadania ego. Zamknij to okienko.
4. Można wyświetlić postęp zadania i wyświetlić strumienie wyjściowe, wybierając **wszystkie dzienniki** z okienka zadania uruchomieniu.<br><br> ![Wyjście strumienia](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. Aby potwierdzić, że maszyna wirtualna jest aprowizowana, zaloguj się do Konsoli zarządzania AWS, jeśli nie jesteś aktualnie zalogowany.<br><br> ![Konsola AWS wdrożona maszyna wirtualna](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Następne kroki
* Aby rozpocząć korzystanie z graficznych śmięty, zobacz [Mój pierwszy graficzny element runbook](automation-first-runbook-graphical.md).
* Aby rozpocząć pracę z uruchomieniu przepływem pracy programu PowerShell, zobacz [Mój pierwszy program PowerShell .](automation-first-runbook-textual.md)
* Aby dowiedzieć się więcej o typach elementów runbook oraz ich zaletach i ograniczeniach, zobacz [Typy elementów runbook usługi Azure Automation](automation-runbook-types.md).
* Aby uzyskać więcej informacji na temat funkcji obsługi skryptów programu PowerShell, zobacz [Natywna obsługa skryptów programu PowerShell w usłudze Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).