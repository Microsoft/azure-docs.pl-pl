---
title: Włączanie automatycznegomanage dla maszyn wirtualnych za pośrednictwem Azure Policy
description: Dowiedz się, jak Azure Automanage maszyn wirtualnych za pomocą wbudowanej Azure Policy w Azure Portal.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: 8846efa3619cec383809cdbd6efe70e3622fa007
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365199"
---
# <a name="enable-automanage-for-virtual-machines-through-azure-policy"></a>Włączanie automatycznegomanage dla maszyn wirtualnych za pośrednictwem Azure Policy

Jeśli chcesz włączyć automatycznemanage dla wielu maszyn wirtualnych, możesz to zrobić przy użyciu wbudowanego [Azure Policy](..\governance\azure-management.md). W tym artykule opisano sposób znajdowania odpowiednich zasad i przypisywania ich w celu włączenia funkcji Automatycznego Azure Portal.


## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).

> [!NOTE]
> Konta bezpłatnej wersji próbnej nie mają dostępu do maszyn wirtualnych używanych w tym samouczku. Przechodz do subskrypcji z płatnością zgodnie z platformą.

> [!IMPORTANT]
> Do włączenia funkcji automatycznego zarządzania potrzebne jest następujące uprawnienie RBAC platformy Azure: **Rola** właściciela lub **Współautor** wraz z **rolami administratora dostępu** użytkowników.

## <a name="direct-link-to-policy"></a>Bezpośredni link do zasad
Definicję zasad Automanage można znaleźć w Azure Portal pod nazwą Konfigurowanie maszyn wirtualnych do do dołączania do [Azure Automanage](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F270610db-8c04-438a-a739-e8e6745b22d3). Jeśli klikniesz ten link, przejdź bezpośrednio do kroku 8 w te stronie [Zlokalizuj i przypisz poniższe](#locate-and-assign-the-policy) zasady.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).


## <a name="locate-and-assign-the-policy"></a>Lokalizowanie i przypisywanie zasad

1. Przejdź do **zasad** w Azure Portal
1. Przejdź do **okienka Definicje**
1. Kliknij menu **rozwijane Kategorie,** aby wyświetlić dostępne opcje
1. Wybierz opcję **Enable Automanage – Azure virtual machine best practices (Włączanie** automatycznego zarządzania — najlepsze rozwiązania dla maszyn wirtualnych platformy Azure)
1. Teraz lista zostanie zaktualizowana w celu pokazania wbudowanych zasad o nazwie, która rozpoczyna się od opcji *Włącz automatycznemanage...*
1. Kliknij *wbudowaną nazwę zasad Enable Automanage - Azure virtual machine best practices (Włączanie* automatycznego zarządzania — najlepsze rozwiązania dotyczące maszyn wirtualnych platformy Azure)
1. Po kliknięciu zasad można teraz wyświetlić **kartę Definicja**

    > [!NOTE]
    > Definicja Azure Policy służy do konfigurowania parametrów automatycznego zarządzania, takich jak profil konfiguracji lub konto. Ustawia również filtry, które zapewniają, że zasady mają zastosowanie tylko do prawidłowych maszyn wirtualnych.

1. Kliknij przycisk **Przypisz,** aby utworzyć przypisanie
1. Na karcie **Podstawowe wypełnij** pole **Zakres,** ustawiając *subskrypcję i* *grupę zasobów*

    > [!NOTE]
    > Zakres umożliwia zdefiniowanie maszyn wirtualnych, których dotyczą te zasady. Aplikację można ustawić na poziomie subskrypcji lub grupy zasobów. Jeśli ustawisz grupę zasobów, wszystkie maszyny wirtualne, które obecnie znajdują się w tej grupie zasobów, lub dowolne przyszłe maszyny wirtualne, które do nich dodamy, będą automatycznie włączane automatyczne zarządzanie.

1. Kliknij **kartę Parametry** i ustaw konto **automatycznego zarządzania** i żądany **profil konfiguracji**
1. Na karcie **Przeglądanie + tworzenie** przejrzyj ustawienia
1. Zastosuj przypisanie, klikając pozycję **Utwórz**
1. Wyświetlanie przypisań na karcie **Przypisania** obok ustawienia **Definicja**

> [!NOTE]
> Te zasady zaczną obowiązywać w przypadku maszyn wirtualnych, które są aktualnie w grupie zasobów lub subskrypcji, dopiero po pewnym czasie.


## <a name="next-steps"></a>Następne kroki

Poznaj inny sposób włączania Azure Automanage maszyn wirtualnych za pośrednictwem Azure Portal.

> [!div class="nextstepaction"]
> [Włącz automatycznemanage dla maszyn wirtualnych w Azure Portal](quick-create-virtual-machines-portal.md)