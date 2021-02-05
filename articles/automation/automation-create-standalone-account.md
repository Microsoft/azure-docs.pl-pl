---
title: Tworzenie autonomicznego konta usługi Azure Automation
description: W tym artykule opisano sposób tworzenia autonomicznego konta Azure Automation oraz klasycznego konta Uruchom jako.
services: automation
ms.subservice: process-automation
ms.date: 01/07/2021
ms.topic: conceptual
ms.openlocfilehash: e0088fb129e9c6558de7539ba754a45e067dc3d8
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576841"
---
# <a name="create-a-standalone-azure-automation-account"></a>Tworzenie autonomicznego konta usługi Azure Automation

W tym artykule opisano sposób tworzenia konta Azure Automation w Azure Portal. Możesz użyć konta usługi Automation w portalu, aby oszacować i poznać automatyzację bez używania dodatkowych funkcji zarządzania ani integrowania z dziennikami Azure Monitor. Możesz dodać funkcje zarządzania lub zintegrować je z dziennikami Azure Monitor w celu zaawansowanego monitorowania zadań elementu Runbook w dowolnym momencie w przyszłości.

Za pomocą konta usługi Automation można uwierzytelniać elementy Runbook przez zarządzanie zasobami w ramach jednego Azure Resource Manager lub klasycznego modelu wdrażania. Jedno konto usługi Automation może zarządzać zasobami we wszystkich regionach i subskrypcjach dla danej dzierżawy.

Po utworzeniu konta usługi Automation w Azure Portal konto **Uruchom jako** jest tworzone automatycznie. To konto wykonuje następujące zadania:

* Tworzy nazwę główną usługi w Azure Active Directory (Azure AD).
* Tworzy certyfikat.
* Przypisuje rolę współautor, która zarządza zasobami Azure Resource Manager za pomocą elementów Runbook.

Po utworzeniu tego konta możesz szybko rozpocząć tworzenie i wdrażanie elementów Runbook, aby obsługiwać potrzeby automatyzacji.

## <a name="permissions-required-to-create-an-automation-account"></a>Uprawnienia wymagane do utworzenia konta usługi Automation

Aby utworzyć lub zaktualizować konto usługi Automation oraz wykonać zadania opisane w tym artykule, musisz mieć następujące uprawnienia i uprawnienia:

* Aby można było utworzyć konto usługi Automation, konto użytkownika usługi Azure AD musi zostać dodane do roli z uprawnieniami odpowiadającymi roli właściciela dla `Microsoft.Automation` zasobów. Aby uzyskać więcej informacji, zobacz [Access Control oparte na rolach w Azure Automation](automation-role-based-access-control.md).
* W Azure Portal w obszarze **Azure Active Directory**  >  **Zarządzanie**  >  **ustawieniami użytkownika**, jeśli **rejestracje aplikacji** jest ustawiona na **tak**, użytkownicy inni niż administratorzy w dzierżawie usługi Azure AD mogą [rejestrować aplikacje Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#check-azure-subscription-permissions). Jeśli **rejestracje aplikacji** jest ustawiona na wartość **nie**, użytkownik wykonujący tę akcję musi mieć co najmniej rolę Deweloper aplikacji w usłudze Azure AD.

Jeśli nie jesteś członkiem wystąpienia Active Directory subskrypcji przed dodaniem do roli administratora globalnego/współadministratora subskrypcji, dodasz Cię do Active Directory jako gość. W tym scenariuszu ten komunikat zostanie wyświetlony w okienku Dodawanie konta usługi Automation: `You do not have permissions to create.`

Jeśli użytkownik zostanie najpierw dodany do roli administratora globalnego/współadministratora, można usunąć tego użytkownika z wystąpienia Active Directory subskrypcji. Możesz przeczytać użytkownika do roli użytkownika w Active Directory. Aby sprawdzić role użytkowników:

1. W Azure Portal przejdź do okienka Azure Active Directory.
1. Wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Wszyscy użytkownicy**.
1. Po wybraniu określonego użytkownika wybierz pozycję **profil**. Wartość atrybutu **typu użytkownika** w profilu użytkownika nie powinna być **gościem**.

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Utwórz nowe konto usługi Automation w Azure Portal

Aby utworzyć konto Azure Automation w Azure Portal, wykonaj następujące czynności:

1. Zaloguj się do Azure Portal przy użyciu konta, które jest członkiem roli Administratorzy subskrypcji i współadministratorem subskrypcji.
1. Wybierz pozycję **+ Utwórz zasób**.
1. Wyszukaj **automatyzację**. W wynikach wyszukiwania wybierz pozycję **Automatyzacja**.

   ![Wyszukaj i wybierz formant & Automation w portalu Azure Marketplace](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)

1. Na następnym ekranie wybierz pozycję **Utwórz nowy**.

   ![Dodawanie konta usługi Automation](media/automation-create-standalone-account/automation-create-automationacct-properties.png)

   > [!NOTE]
   > Jeśli w okienku Dodawanie konta usługi Automation zostanie wyświetlony następujący komunikat, Twoje konto nie jest członkiem roli Administratorzy subskrypcji i współadministratorem subskrypcji.
   >
   > :::image type="content" source="media/automation-create-standalone-account/create-account-without-perms.png" alt-text="Zrzut ekranu przedstawiający monit &quot;nie masz uprawnień do utworzenia konta Uruchom jako w usłudze Azure Active Directory&quot;.":::

1. W okienku Dodawanie konta usługi Automation wprowadź nazwę nowego konta usługi Automation w polu **Nazwa** . Tej nazwy nie można zmienić po jej wybraniu. 

    > [!NOTE]
    > Nazwy kont usługi Automation są unikatowe dla poszczególnych regionów i grup zasobów. Nazwy usuniętych kont usługi Automation mogą nie być od razu dostępne.

1. Jeśli masz więcej niż jedną subskrypcję, użyj pola **subskrypcja** , aby określić subskrypcję do użycia dla nowego konta.
1. W obszarze **Grupa zasobów** wprowadź lub wybierz nową lub istniejącą grupę zasobów.
1. W obszarze **Lokalizacja** wybierz lokalizację centrum danych platformy Azure.
1. Upewnij **się, że opcja** **Utwórz konto Uruchom jako platformy Azure** jest zaznaczona, a następnie kliknij przycisk **Utwórz**.

   > [!NOTE]
   > Jeśli zdecydujesz się nie tworzyć konta Uruchom jako, wybierając pozycję **nie** , aby **utworzyć konto Uruchom jako platformy Azure**, w okienku Dodawanie konta usługi Automation zostanie wyświetlony komunikat. Mimo że konto jest tworzone w Azure Portal, konto nie ma odpowiedniej tożsamości uwierzytelniania w ramach subskrypcji klasycznego modelu wdrażania lub subskrypcji Azure Resource Manager usługi katalogowej. W związku z tym konto usługi Automation nie ma dostępu do zasobów w Twojej subskrypcji. Dzięki temu wszystkie elementy Runbook odwołujące się do tego konta nie będą mogły uwierzytelniać i wykonywać zadań względem zasobów w tych modelach wdrożenia.
   >
   > :::image type="content" source="media/automation-create-standalone-account/create-account-decline-create-runas-msg.png" alt-text="Zrzut ekranu przedstawiający monit z komunikatem &quot;nie można utworzyć konta Uruchom jako&quot;.":::
   >
   > Gdy nie zostanie utworzona jednostka usługi, rola współautor nie jest przypisana.
   >

1. Aby śledzić postęp tworzenia konta usługi Automation, w menu wybierz pozycję **powiadomienia** .

Po pomyślnym utworzeniu konta usługi Automation automatycznie zostanie utworzonych kilka zasobów. Po utworzeniu te elementy Runbook można bezpiecznie usunąć, jeśli nie chcesz ich zachować. Konta Uruchom jako mogą służyć do uwierzytelniania na koncie w elemencie Runbook i powinny pozostać bez względu na to, czy nie zostanie utworzona inna lub nie jest wymagana. Poniższa tabela zawiera podsumowanie zasobów dla konta Uruchom jako.

| Zasób | Opis |
| --- | --- |
| AzureAutomationTutorial Runbook |Przykładowy graficzny element Runbook, który demonstruje sposób uwierzytelniania przy użyciu konta Uruchom jako. Element Runbook pobiera wszystkie zasoby Menedżer zasobów. |
| AzureAutomationTutorialScript Runbook |Przykładowy element Runbook programu PowerShell, który demonstruje sposób uwierzytelniania przy użyciu konta Uruchom jako. Element Runbook pobiera wszystkie zasoby Menedżer zasobów. |
| Element runbook AzureAutomationTutorialPython2 |Przykładowy element Runbook języka Python, który demonstruje sposób uwierzytelniania przy użyciu konta Uruchom jako. Element Runbook zawiera listę wszystkich grup zasobów obecnych w subskrypcji. |
| AzureRunAsCertificate |Zasób certyfikatu tworzony automatycznie podczas tworzenia konta usługi Automation lub przy użyciu skryptu programu PowerShell dla istniejącego konta. Certyfikat jest uwierzytelniany na platformie Azure, aby można było zarządzać zasobami Azure Resource Manager z elementów Runbook. Ten certyfikat ma roczny okres obowiązywania. |
| AzureRunAsConnection |Zasób połączenia, który jest tworzony automatycznie podczas tworzenia konta usługi Automation lub przy użyciu skryptu programu PowerShell dla istniejącego konta. |

## <a name="create-a-classic-run-as-account"></a>Tworzenie klasycznego konta Uruchom jako

Klasyczne konta Uruchom jako nie są tworzone domyślnie podczas tworzenia konta Azure Automation. Jeśli do zarządzania klasycznymi zasobami platformy Azure jest wymagane klasyczne konto Uruchom jako, wykonaj następujące czynności:

1. Z poziomu konta usługi Automation wybierz pozycję **konta Uruchom jako** w obszarze **Ustawienia konta**.
2. Wybierz **klasyczne konto Uruchom jako platformy Azure**.
3. Kliknij przycisk **Utwórz** , aby kontynuować tworzenie klasycznego konta Uruchom jako.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o tworzeniu graficznym, zobacz [Tworzenie graficznych elementów Runbook w programie Azure Automation](automation-graphical-authoring-intro.md).
* Aby rozpocząć pracę z elementami Runbook programu PowerShell, zobacz [Samouczek: Tworzenie elementu Runbook programu PowerShell](learn/automation-tutorial-runbook-textual-powershell.md).
* Aby rozpocząć pracę z elementami Runbook przepływu pracy programu PowerShell, zobacz [Samouczek: Tworzenie elementu Runbook przepływu pracy programu PowerShell](learn/automation-tutorial-runbook-textual.md).
* Aby rozpocząć pracę z elementami Runbook języka Python 3, zobacz [Samouczek: Tworzenie elementu Runbook języka Python 3](learn/automation-tutorial-runbook-textual-python-3.md).
* Aby uzyskać informacje dotyczące poleceń cmdlet programu PowerShell, zobacz [AZ. Automation](/powershell/module/az.automation).
