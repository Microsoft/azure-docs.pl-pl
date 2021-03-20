---
title: Przewodnik Szybki start platformy Azure — Tworzenie konta usługi Azure Automation | Microsoft Docs
description: Ten artykuł ułatwia rozpoczęcie tworzenia konta Azure Automation i uruchamiania elementu Runbook.
services: automation
ms.date: 04/04/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: a07c6ac524aa213519ace1ae204ac2d76db802aa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "83836706"
---
# <a name="create-an-azure-automation-account"></a>Tworzenie konta usługi Azure Automation

Możesz utworzyć konto Azure Automation za pośrednictwem platformy Azure, korzystając z Azure Portal, interfejsu użytkownika opartego na przeglądarce, który umożliwia dostęp do wielu zasobów. Jedno konto usługi Automation może zarządzać zasobami między wszystkimi regionami i subskrypcjami dla danej dzierżawy. 

Ten przewodnik Szybki Start przedstawia sposób tworzenia konta usługi Automation i uruchamiania elementu Runbook w ramach konta. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

[Zaloguj się na platformie Azure](https://portal.azure.com).

## <a name="create-automation-account"></a>Tworzenie konta usługi Automation

1. Wybierz nazwę swojego konta platformy Azure. Nazwy kont usługi Automation są unikatowe dla poszczególnych regionów i grup zasobów. Nazwy kont usługi Automation, które zostały usunięte, mogą nie być od razu dostępne.

    > [!NOTE]
    > Nie można zmienić nazwy konta, gdy została wprowadzona w interfejsie użytkownika. 

2. Kliknij przycisk **Utwórz zasób** znajdujący się w lewym górnym rogu Azure Portal.

3. Wybierz **go & narzędzia do zarządzania**, a następnie wybierz **automatyzację**.

4. Wprowadź informacje o koncie, w tym nazwę wybranego konta. W obszarze **Utwórz konto Uruchom jako platformy Azure** wybierz pozycję **Tak**, aby artefakty upraszczające uwierzytelnianie zostały automatycznie włączone. Po zakończeniu tych informacji kliknij pozycję **Utwórz** , aby rozpocząć wdrażanie konta usługi Automation.

    ![Wprowadzanie na stronie informacji dotyczących konta usługi Automation](./media/automation-quickstart-create-account/create-automation-account-portal-blade.png)  

    > [!NOTE]
    > Zaktualizowaną listę lokalizacji, do których można wdrożyć konto usługi Automation, znajduje się w temacie [dostępne produkty według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=automation&regions=all).

5. Po zakończeniu wdrażania kliknij pozycję **wszystkie usługi**.

6. Wybierz pozycję **konta usługi Automation** , a następnie wybierz utworzone konto usługi Automation.

    ![Przegląd konta usługi Automation](./media/automation-quickstart-create-account/automation-account-overview.png)

## <a name="run-a-runbook"></a>Uruchamianie elementu runbook

Uruchom jeden z elementów runbook z samouczka.

1. Kliknij pozycję **elementy Runbook** w obszarze **Automatyzacja procesów**. Zostanie wyświetlona lista elementów runbook. Domyślnie na koncie jest włączonych kilka elementów Runbook samouczków.

    ![Lista elementów runbook konta usługi Automation](./media/automation-quickstart-create-account/automation-runbooks-overview.png)

1. Wybierz element runbook **AzureAutomationTutorialScript**. Ta akcja spowoduje otwarcie strony przeglądu elementu runbook.

    ![Przegląd elementu runbook](./media/automation-quickstart-create-account/automation-tutorial-script-runbook-overview.png)

1. Kliknij pozycję **Uruchom**, a na stronie Uruchamianie elementu runbook kliknij przycisk **OK**, aby uruchomić element runbook.

    ![Strona zadania elementu runbook](./media/automation-quickstart-create-account/automation-tutorial-script-job.png)

1. Po stanie zadania `Running` kliknij pozycję **dane wyjściowe** lub **wszystkie dzienniki** , aby wyświetlić dane wyjściowe zadania elementu Runbook. W przypadku tego elementu runbook z samouczka danymi wyjściowymi jest lista Twoich zasobów platformy Azure.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wdrożono konto usługi Automation, uruchomiono element runbook i wyświetlono wyniki zadania. Aby dowiedzieć się więcej na temat usługi Azure Automation, przejdź do przewodnika Szybki start dotyczącego tworzenia pierwszego elementu runbook.

> [!div class="nextstepaction"]
> [Przewodnik Szybki Start dotyczący automatyzacji — tworzenie Azure Automation elementu Runbook](./automation-quickstart-create-runbook.md)

