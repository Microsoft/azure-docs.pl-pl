---
title: Włączanie rozszerzenia maszyny wirtualnej z Azure Portal
description: W tym artykule opisano sposób wdrażania rozszerzeń maszyn wirtualnych na Azure Arc serwerów działających w środowiskach chmury hybrydowej z Azure Portal.
ms.date: 04/13/2021
ms.topic: conceptual
ms.openlocfilehash: b5b4ff79d68ec9ff0cc61b9dbb7d3c5d7fe93598
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388282"
---
# <a name="enable-azure-vm-extensions-from-the-azure-portal"></a>Włączanie rozszerzeń maszyn wirtualnych platformy Azure z Azure Portal

W tym artykule przedstawiono sposób wdrażania i odinstalowywania rozszerzeń maszyn wirtualnych platformy Azure obsługiwanych przez serwery z obsługą usługi Azure Arc na maszynie hybrydowej z systemem Linux lub Windows za pośrednictwem Azure Portal.

> [!NOTE]
> Rozszerzenie Key Vault maszyny wirtualnej (wersja zapoznawcza) nie obsługuje wdrażania z usługi Azure Portal, tylko przy użyciu interfejsu wiersza polecenia platformy Azure, interfejsu Azure PowerShell lub szablonu Azure Resource Manager szablonu.

> [!NOTE]
> Azure Arc obsługuje wdrażania rozszerzeń maszyn wirtualnych i zarządzania nimi na maszynach wirtualnych platformy Azure. W przypadku maszyn wirtualnych platformy Azure zobacz następujący artykuł [z omówieniem rozszerzenia maszyny wirtualnej.](../../virtual-machines/extensions/overview.md)

## <a name="enable-extensions-from-the-portal"></a>Włączanie rozszerzeń z portalu

Rozszerzenia maszyny wirtualnej można zastosować do usługi Arc dla maszyny zarządzanej przez serwer za pośrednictwem Azure Portal.

1. W przeglądarce przejdź do strony [Azure Portal](https://portal.azure.com).

2. W portalu przejdź do opcji **Serwery — Azure Arc** wybierz maszynę hybrydową z listy.

3. Wybierz **pozycję Rozszerzenia,** a następnie wybierz **pozycję Dodaj.** Wybierz rozszerzenie z listy dostępnych rozszerzeń i postępuj zgodnie z instrukcjami w kreatorze. W tym przykładzie wdrożymy rozszerzenie maszyny wirtualnej usługi Log Analytics.

    ![Wybieranie rozszerzenia maszyny wirtualnej dla wybranej maszyny](./media/manage-vm-extensions/add-vm-extensions.png)

    W poniższym przykładzie pokazano instalację rozszerzenia maszyny wirtualnej usługi Log Analytics z Azure Portal:

    ![Instalowanie rozszerzenia maszyny wirtualnej usługi Log Analytics](./media/manage-vm-extensions/mma-extension-config.png)

    Aby ukończyć instalację, musisz podać identyfikator obszaru roboczego i klucz podstawowy. Jeśli nie wiesz, jak znaleźć te informacje, zobacz Uzyskiwanie identyfikatora i klucza obszaru [roboczego](../../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

4. Po potwierdzeniu podanych wymaganych informacji wybierz pozycję **Utwórz**. Zostanie wyświetlone podsumowanie wdrożenia. Możesz sprawdzić jego stan.

>[!NOTE]
>Chociaż wiele rozszerzeń można tworzyć wsadowo i przetwarzać, są one instalowane szeregowo. Po zakończeniu pierwszej instalacji rozszerzenia podejmowana jest próba zainstalowania następnego rozszerzenia.

## <a name="list-extensions-installed"></a>Lista zainstalowanych rozszerzeń

Listę rozszerzeń maszyn wirtualnych na serwerze z usługą Arc można uzyskać z Azure Portal. Wykonaj poniższe kroki, aby je wyświetlić.

1. W przeglądarce przejdź do strony [Azure Portal](https://portal.azure.com).

2. W portalu przejdź do opcji **Serwery — Azure Arc** wybierz maszynę hybrydową z listy.

3. Wybierz **pozycję Rozszerzenia**. Zostanie zwrócona lista zainstalowanych rozszerzeń.

    ![Lista rozszerzeń maszyn wirtualnych wdrożonych na wybranej maszynie](./media/manage-vm-extensions/list-vm-extensions.png)

## <a name="uninstall-extension"></a>Odinstalowywanie rozszerzenia

Z serwera z usługą Arc można usunąć co najmniej jedno rozszerzenie z Azure Portal. Wykonaj poniższe kroki, aby usunąć rozszerzenie.

1. W przeglądarce przejdź do strony [Azure Portal](https://portal.azure.com).

2. W portalu przejdź do opcji **Serwery — Azure Arc** wybierz maszynę hybrydową z listy.

3. Wybierz **pozycję Rozszerzenia,** a następnie wybierz rozszerzenie z listy zainstalowanych rozszerzeń.

4. Wybierz **pozycję Odinstaluj.** Po wyświetleniu monitu o zweryfikowanie wybierz **pozycję Tak,** aby kontynuować.

## <a name="next-steps"></a>Następne kroki

- Rozszerzenia maszyn wirtualnych można wdrażać i usuwać oraz zarządzać nimi przy użyciu interfejsu wiersza polecenia platformy [Azure,](manage-vm-extensions-cli.md)programu [PowerShell](manage-vm-extensions-powershell.md) [lub Azure Resource Manager szablonów.](manage-vm-extensions-template.md)

- Informacje dotyczące rozwiązywania problemów można znaleźć w [przewodniku Rozwiązywanie problemów z rozszerzeniami maszyn wirtualnych.](troubleshoot-vm-extensions.md)