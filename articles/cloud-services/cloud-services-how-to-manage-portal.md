---
title: Typowe zadania zarządzania usługą w chmurze | Microsoft Docs
description: Dowiedz się, jak zarządzać Cloud Services w Azure Portal. W poniższych przykładach użyto Azure Portal.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: tagore
ms.openlocfilehash: 84e450a55f9482c3a713943adfcc4d7940ab873f
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92076712"
---
# <a name="manage-cloud-services-in-the-azure-portal"></a>Zarządzanie Cloud Services w Azure Portal
W obszarze **Cloud Services** Azure Portal można wykonać następujące działania:

* Zaktualizuj rolę usługi lub wdrożenie.
* Podnieś poziom wdrożenia etapowego do środowiska produkcyjnego.
* Połącz zasoby z usługą w chmurze, aby można było zobaczyć zależności zasobów i wspólnie skalować zasoby.
* Usuń usługę w chmurze lub wdrożenie.

Aby uzyskać więcej informacji na temat skalowania usługi w chmurze, zobacz [Konfigurowanie automatycznego skalowania dla usługi w chmurze w portalu](cloud-services-how-to-scale-portal.md).

## <a name="update-a-cloud-service-role-or-deployment"></a>Aktualizowanie roli usługi w chmurze lub wdrożenia
Jeśli musisz zaktualizować kod aplikacji dla usługi w chmurze, użyj **aktualizacji** w bloku usługi w chmurze. Można zaktualizować pojedynczą rolę lub wszystkie role. Aby zaktualizować, można przekazać nowy pakiet usługi lub plik konfiguracji usługi.

1. W [Azure Portal][Azure portal]wybierz usługę w chmurze, którą chcesz zaktualizować. Ten krok powoduje otwarcie bloku wystąpienia usługi w chmurze.

2. W bloku wybierz pozycję **Aktualizuj**.

    ![Przycisk Aktualizuj](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Zaktualizuj wdrożenie przy użyciu nowego pliku pakietu usługi (. cspkg) i pliku konfiguracji usługi (cscfg).

    ![UpdateDeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. Opcjonalnie możesz zaktualizować konto magazynu i etykietę wdrożenia.

5. Jeśli wszystkie role mają tylko jedno wystąpienie roli, zaznacz pole wyboru **Wdróż, nawet jeśli co najmniej jedna rola zawiera pojedyncze wystąpienie** , aby umożliwić kontynuowanie uaktualniania.

    Platforma Azure może zagwarantować dostępność usługi do 99,95% podczas aktualizacji usługi w chmurze, jeśli każda rola ma co najmniej dwa wystąpienia roli (maszyny wirtualne). W przypadku dwóch wystąpień roli jedna maszyna wirtualna przetwarza żądania klientów, gdy druga zostanie zaktualizowana.

6. Zaznacz pole wyboru **Rozpocznij wdrażanie** , aby zastosować aktualizację po zakończeniu przekazywania pakietu.

7. Wybierz **przycisk OK** , aby rozpocząć aktualizowanie usługi.

## <a name="swap-deployments-to-promote-a-staged-deployment-to-production"></a>Wymiana wdrożeń w celu promowania wdrożenia przemieszczanego w środowisku produkcyjnym
W przypadku podjęcia decyzji o wdrożeniu nowej wersji usługi w chmurze Przeprowadź etap i przetestuj nową wersję w środowisku przejściowym usługi w chmurze. Użyj opcji **Zamień** , aby przełączyć adresy URL, na podstawie których są rozkierowane dwa wdrożenia, i Podnieś poziom nowej wersji do produkcji.

Możesz zamienić wdrożenia ze strony **Cloud Services** lub pulpitu nawigacyjnego.

1. W [Azure Portal][Azure portal]wybierz usługę w chmurze, którą chcesz zaktualizować. Ten krok powoduje otwarcie bloku wystąpienia usługi w chmurze.

2. W bloku wybierz pozycję **Zamień**.

    ![Przycisk zamiany Cloud Services](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. Zostanie otwarty następujący monit dotyczący potwierdzenia:

    ![Cloud Services wymiany](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. Po sprawdzeniu informacji o wdrożeniu wybierz pozycję **OK** , aby wymienić wdrożenia.

    Wymiana wdrożenia odbywa się szybko, ponieważ tylko te zmiany są wirtualnymi adresami IP (VIP) dla wdrożeń.

    Aby zaoszczędzić koszty obliczeń, można usunąć wdrożenie przejściowe po sprawdzeniu, czy wdrożenie produkcyjne działa zgodnie z oczekiwaniami.

### <a name="common-questions-about-swapping-deployments"></a>Często zadawane pytania dotyczące wymiany wdrożeń

**Jakie są wymagania wstępne dotyczące wymiany wdrożeń?**

Istnieją dwa kluczowe wymagania wstępne dotyczące przeprowadzenia zamiany wdrożenia:

- Jeśli chcesz użyć statycznego adresu IP dla miejsca produkcyjnego, musisz zarezerwować jeden dla miejsca przejściowego. W przeciwnym razie swap nie powiedzie się.

- Aby można było przeprowadzić zamianę, wszystkie wystąpienia ról muszą być uruchomione. Stan wystąpień można sprawdzić w bloku **przegląd** Azure Portal. Alternatywnie możesz użyć polecenia [Get-AzureRole](/powershell/module/servicemanagement/azure.service/get-azurerole?view=azuresmps-3.7.0) w programie Windows PowerShell.

Należy pamiętać, że aktualizacje systemu operacyjnego gościa i operacje naprawy usług mogą również spowodować niepowodzenie zamiany wdrożenia. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z wdrażaniem usługi w chmurze](cloud-services-troubleshoot-deployment-problems.md).

**Czy Zamiana wiąże się z czasem przestoju aplikacji? Jak należy ją obsłużyć?**

Zgodnie z opisem w poprzedniej sekcji wymiana wdrożenia jest zwykle szybka, ponieważ jest to tylko zmiana konfiguracji w module równoważenia obciążenia platformy Azure. W niektórych przypadkach może upłynąć 10 lub więcej sekund i spowodować błędy połączeń przejściowych. Aby ograniczyć wpływ klientów, należy rozważyć implementację [logiki ponawiania klienta](/azure/architecture/best-practices/transient-faults).

## <a name="delete-deployments-and-a-cloud-service"></a>Usuwanie wdrożeń i usługi w chmurze
Aby można było usunąć usługę w chmurze, należy usunąć wszystkie istniejące wdrożenia.

Aby zaoszczędzić koszty obliczeń, można usunąć wdrożenie przejściowe po sprawdzeniu, czy wdrożenie produkcyjne działa zgodnie z oczekiwaniami. Opłaty są naliczane za koszty obliczeń wdrożonych wystąpień roli, które są zatrzymane.

Aby usunąć wdrożenie lub usługę w chmurze, wykonaj czynności opisane w poniższej procedurze.

1. W [Azure Portal][Azure portal]wybierz usługę w chmurze, którą chcesz usunąć. Ten krok powoduje otwarcie bloku wystąpienia usługi w chmurze.

2. W bloku wybierz pozycję **Usuń**.

    ![Przycisk Usuń Cloud Services](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. Aby usunąć całą usługę w chmurze, zaznacz pole wyboru **Usługa w chmurze i jej wdrożenia** . Można też wybrać opcję **wdrożenie produkcyjne** lub **wdrożenie przejściowe** .

    ![Cloud Services usunąć](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. Wybierz pozycję **Usuń** u dołu.

5. Aby usunąć usługę w chmurze, wybierz pozycję **Usuń usługę w chmurze**. Następnie w monicie o potwierdzenie wybierz pozycję **tak**.

> [!NOTE]
> W przypadku usunięcia usługi w chmurze i skonfigurowania pełnego monitorowania należy usunąć dane ręcznie z konta magazynu. Aby uzyskać informacje o tym, gdzie znaleźć tabele metryk, zobacz [wprowadzenie do monitorowania usługi w chmurze](cloud-services-how-to-monitor.md).


## <a name="find-more-information-about-failed-deployments"></a>Znajdź więcej informacji na temat wdrożeń zakończonych niepowodzeniem
Blok **przeglądu** zawiera pasek stanu u góry. Po wybraniu paska zostanie otwarty nowy blok i zostaną wyświetlone wszystkie informacje o błędzie. Jeśli wdrożenie nie zawiera żadnych błędów, blok informacji jest pusty.

![Przegląd Cloud Services](./media/cloud-services-how-to-manage-portal/status-info.png)



[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>Następne kroki
* [Ogólna konfiguracja usługi w chmurze](cloud-services-how-to-configure-portal.md).
* Dowiedz się, jak [wdrożyć usługę w chmurze](cloud-services-how-to-create-deploy-portal.md).
* Skonfiguruj [niestandardową nazwę domeny](cloud-services-custom-domain-name-portal.md).
* Skonfiguruj [Certyfikaty TLS/SSL](cloud-services-configure-ssl-certificate-portal.md).