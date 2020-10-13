---
title: Włącz Autozarządzanie maszynami wirtualnymi za Azure Policy
description: Dowiedz się, jak włączyć usługę Azure automanage dla maszyn wirtualnych za pomocą wbudowanej Azure Policy w Azure Portal.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: 8f679626b69bd855e86b94cdde51955edd068e8f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91714896"
---
# <a name="enable-automanage-for-virtual-machines-through-azure-policy"></a>Włącz Autozarządzanie maszynami wirtualnymi za Azure Policy

Jeśli chcesz włączyć Autozarządzanie dla wielu maszyn wirtualnych, możesz to zrobić przy użyciu wbudowanej [Azure Policy](..\governance\azure-management.md). W tym artykule opisano sposób znajdowania odpowiednich zasad i sposobu ich przypisywania w celu włączenia autozarządzania w Azure Portal.


## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).

> [!NOTE]
> Konta bezpłatnych wersji próbnych nie mają dostępu do maszyn wirtualnych używanych w tym samouczku. Przeprowadź uaktualnienie do subskrypcji z opcją płatność zgodnie z rzeczywistym użyciem.

> [!IMPORTANT]
> Następujące uprawnienie kontroli RBAC platformy Azure jest konieczne, aby włączyć Autozarządzanie: rola **właściciela** lub **współautor** wraz z rolami **administratora dostępu użytkowników** .


## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).


## <a name="locate-and-assign-the-policy"></a>Lokalizowanie i przypisywanie zasad

1. Przejdź do **zasad** w Azure Portal
1. Przejdź do okienka **definicje**
1. Kliknij listę rozwijaną **Kategorie** , aby wyświetlić dostępne opcje.
1. Wybierz opcję **Włącz Autozarządzanie — najlepsze rozwiązania dotyczące usługi Azure Virtual Machines**
1. Teraz lista zostanie zaktualizowana, aby wyświetlić zasady wbudowane o nazwie rozpoczynającej się od *enable automanage...*
1. Kliknij pozycję *Włącz Autozarządzanie —* Nazwa zasad wbudowanych najlepszych rozwiązań dla usługi Azure Virtual Machine
1. Po kliknięciu zasad możesz teraz wyświetlić kartę **Definicja**

    > [!NOTE]
    > Definicja Azure Policy służy do ustawiania parametrów automanage, takich jak profil konfiguracji lub konto. Ustawia również filtry, które gwarantują, że zasady dotyczą tylko prawidłowych maszyn wirtualnych.

1. Kliknij przycisk **Przypisz** , aby utworzyć przypisanie
1. Na karcie **podstawy** Wypełnij **zakres** , ustawiając *subskrypcję* i *grupę zasobów* .

    > [!NOTE]
    > Zakres umożliwia zdefiniowanie maszyn wirtualnych, których dotyczą te zasady. Aplikację można ustawić na poziomie subskrypcji lub grupy zasobów. W przypadku skonfigurowania grupy zasobów wszystkie maszyny wirtualne, które znajdują się obecnie w tej grupie zasobów lub wszystkie przyszłe maszyny wirtualne, które dodamy do niej, będą automatycznie włączone. 

1. Kliknij kartę **Parametry** , a następnie ustaw **konto autozarządzaj** i żądany **profil konfiguracji** . 
1. Na karcie **Recenzja + tworzenie** przejrzyj ustawienia.
1. Zastosuj przypisanie, klikając pozycję **Utwórz** .
1. Wyświetlanie przypisań na karcie **przypisania** obok **definicji**

> [!NOTE]
> Ta zasada zajmie trochę czasu, zanim zaczną obowiązywać w przypadku maszyn wirtualnych znajdujących się obecnie w grupie zasobów lub subskrypcji.


## <a name="next-steps"></a>Następne kroki 

Zapoznaj się z innymi sposobami włączania usługi Azure automanage dla maszyn wirtualnych za pomocą Azure Portal. 

> [!div class="nextstepaction"]
> [Włącz Autozarządzanie maszynami wirtualnymi w Azure Portal](quick-create-virtual-machines-portal.md)