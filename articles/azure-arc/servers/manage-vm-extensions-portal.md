---
title: Włącz rozszerzenie maszyny wirtualnej na podstawie Azure Portal
description: W tym artykule opisano sposób wdrażania rozszerzeń maszyn wirtualnych na serwerach z obsługą usługi Azure ARC z systemem w środowiskach chmury hybrydowej z poziomu Azure Portal.
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: dcab7cb441c329a60b2c6fa3256aeedb2bb5b33d
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92462939"
---
# <a name="enable-azure-vm-extensions-from-the-azure-portal"></a>Włącz rozszerzenia maszyny wirtualnej platformy Azure z poziomu Azure Portal

W tym artykule opisano sposób wdrażania i odinstalowywania rozszerzeń maszyn wirtualnych platformy Azure, które są obsługiwane przez serwery z obsługą usługi Azure ARC, na maszynę hybrydową z systemem Linux lub Windows za pomocą Azure Portal.

## <a name="enable-extensions-from-the-portal"></a>Włącz rozszerzenia portalu

Rozszerzenia maszyny wirtualnej mogą być stosowane do Twojego łuku dla maszyny zarządzanej przez serwer za pomocą Azure Portal.

1. W przeglądarce przejdź do [Azure Portal](https://portal.azure.com).

2. W portalu przejdź do opcji **serwery — Azure Arc** i wybierz maszynę hybrydową z listy.

3. Wybierz pozycję **rozszerzenia**, a następnie wybierz pozycję **Dodaj**. Wybierz odpowiednie rozszerzenie z listy dostępnych rozszerzeń i postępuj zgodnie z instrukcjami wyświetlanymi w kreatorze. W tym przykładzie zostanie wdrożone rozszerzenie maszyny wirtualnej Log Analytics.

    ![Wybierz rozszerzenie maszyny wirtualnej dla wybranej maszyny](./media/manage-vm-extensions/add-vm-extensions.png)

    W poniższym przykładzie przedstawiono instalację rozszerzenia maszyny wirtualnej Log Analytics z Azure Portal:

    ![Zainstaluj rozszerzenie maszyny wirtualnej Log Analytics](./media/manage-vm-extensions/mma-extension-config.png)

    Aby ukończyć instalację, należy podać identyfikator obszaru roboczego i klucz podstawowy. Jeśli nie wiesz, jak znaleźć te informacje, zobacz temat [Uzyskiwanie identyfikatora i klucza obszaru roboczego](../../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key).

4. Po potwierdzeniu wymaganych informacji wybierz pozycję **Utwórz**. Zostanie wyświetlone podsumowanie wdrożenia i można sprawdzić stan wdrożenia.

>[!NOTE]
>Chociaż wiele rozszerzeń można wsadowo i przetwarzać, są one instalowane seryjnie. Po zakończeniu instalacji pierwszego rozszerzenia zostanie podjęta próba instalacji następnego rozszerzenia.

## <a name="uninstall-extension"></a>Odinstaluj rozszerzenie

Można usunąć jedno lub więcej rozszerzeń z serwera z obsługą łuku z Azure Portal. Wykonaj następujące kroki, aby usunąć rozszerzenie.

1. W przeglądarce przejdź do [Azure Portal](https://portal.azure.com).

2. W portalu przejdź do opcji **serwery — Azure Arc** i wybierz maszynę hybrydową z listy.

3. Wybierz **rozszerzenia**, a następnie wybierz rozszerzenie z listy zainstalowanych rozszerzeń.

4. Wybierz pozycję **Odinstaluj** , a po wyświetleniu monitu o potwierdzenie wybierz pozycję **tak** , aby wykonać operację.

## <a name="next-steps"></a>Następne kroki

- Można wdrażać i usuwać rozszerzenia maszyn wirtualnych oraz zarządzać nimi przy użyciu szablonów [interfejsu wiersza polecenia platformy Azure](manage-vm-extensions-cli.md), [programu PowerShell](manage-vm-extensions-powershell.md)lub [Azure Resource Manager](manage-vm-extensions-template.md).

- Informacje dotyczące rozwiązywania problemów można znaleźć w [podręczniku Rozwiązywanie problemów z maszynami](troubleshoot-vm-extensions.md)wirtualnymi.