---
title: Utwórz obszar roboczy Azure Databricks we własnym Virtual Network Szybki Start
description: W tym artykule opisano sposób wdrażania Azure Databricks w sieci wirtualnej.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: 63dd1b4d9396d340dd17a7afb92ff9c38a2b38b3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80132679"
---
# <a name="quickstart-create-an-azure-databricks-workspace-in-your-own-virtual-network"></a>Szybki Start: Tworzenie obszaru roboczego Azure Databricks we własnym Virtual Network

Domyślne wdrożenie Azure Databricks tworzy nową sieć wirtualną zarządzaną przez datakostki. Ten przewodnik Szybki Start przedstawia sposób tworzenia obszaru roboczego Azure Databricks w ramach własnej sieci wirtualnej. Utworzysz również Klaster Apache Spark w tym obszarze roboczym. 

Aby uzyskać więcej informacji o tym, dlaczego można utworzyć obszar roboczy Azure Databricks w sieci wirtualnej, zobacz [wdrażanie Azure Databricks w Virtual Network platformy Azure (Wybieranie sieci wirtualnej)](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject).

Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/databricks/).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

> [!Note]
> Tego samouczka nie można przeprowadzić za pomocą **subskrypcji bezpłatnej wersji próbnej platformy Azure**.
> Jeśli masz bezpłatne konto, przejdź do swojego profilu i Zmień subskrypcję na **płatność zgodnie z rzeczywistym**użyciem. Aby uzyskać więcej informacji, zobacz [Bezpłatne konto platformy Azure](https://azure.microsoft.com/free/). Następnie [Usuń limit wydatków](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)i [Poproś o zwiększenie limitu przydziału](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) dla procesorów wirtualnych vCPU w Twoim regionie. Podczas tworzenia obszaru roboczego Azure Databricks możesz wybrać warstwę cenową **wersji próbnej (Premium-14-Days Free dBu)** , aby umożliwić dostęp do obszaru roboczego bezpłatnie Azure Databricks DBU przez 14 dni.

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

1. Z menu Azure Portal wybierz pozycję **Utwórz zasób**. Następnie wybierz pozycję **sieć > Sieć wirtualna**.

    ![Tworzenie sieci wirtualnej w Azure Portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network-portal.png)

2. W obszarze **Utwórz sieć wirtualną**Zastosuj następujące ustawienia: 

    |Ustawienie|Sugerowana wartość|Opis|
    |-------|---------------|-----------|
    |Subskrypcja|\<Twoja subskrypcja\>|Wybierz subskrypcję platformy Azure, której chcesz użyć.|
    |Grupa zasobów|datakostki — Szybki Start|Wybierz pozycję **Utwórz nową** , a następnie wprowadź nową nazwę grupy zasobów dla swojego konta.|
    |Nazwa|datakostki — Szybki Start|Wybierz nazwę sieci wirtualnej.|
    |Region|\<Wybierz region, który jest najbliżej Twoich użytkowników\>|Wybierz lokalizację geograficzną, w której będzie można hostować sieć wirtualną. Użyj lokalizacji znajdującej się najbliżej użytkowników.|

    ![Podstawy dla sieci wirtualnej w Azure Portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network.png)

3. Wybierz pozycję **Dalej: adresy IP >** i Zastosuj następujące ustawienia. Następnie wybierz pozycję **Przegląd + Utwórz**.
    
    |Ustawienie|Sugerowana wartość|Opis|
    |-------|---------------|-----------|
    |Przestrzeń adresów IPv4|10.2.0.0/16|Zakres adresów sieci wirtualnej w notacji CIDR. Zakres CIDR musi należeć do zakresu od/16 do/24|
    |Nazwa podsieci|default|Wybierz nazwę domyślnej podsieci w sieci wirtualnej.|
    |Zakres adresów podsieci|10.2.0.0/24|Zakres adresów podsieci w notacji CIDR. Musi ona być zawarta w przestrzeni adresowej sieci wirtualnej. Nie można edytować zakresu adresów podsieci, która jest używana.|

    ![Ustaw konfiguracje IP dla sieci wirtualnej w Azure Portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network-ip-config.png)

4. Na karcie **Recenzja i tworzenie** wybierz pozycję **Utwórz** , aby wdrożyć sieć wirtualną. Po zakończeniu wdrażania przejdź do sieci wirtualnej i wybierz pozycję **przestrzeń adresowa** w obszarze **Ustawienia**. W polu tekstowym *Dodaj dodatkowy zakres adresów*Wstaw `10.179.0.0/16` i wybierz pozycję **Zapisz**.

    ![Przestrzeń adresowa sieci wirtualnej platformy Azure](./media/quickstart-create-databricks-workspace-vnet-injection/add-address-space.png)

## <a name="create-an-azure-databricks-workspace"></a>Tworzenie obszaru roboczego usługi Azure Databricks

1. Z menu Azure Portal wybierz pozycję **Utwórz zasób**. Następnie wybierz pozycję **analiza > datakostki**.

    ![Utwórz obszar roboczy Azure Databricks na Azure Portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace-portal.png)

2. W obszarze **Azure Databricks Service**Zastosuj następujące ustawienia:

    |Ustawienie|Sugerowana wartość|Opis|
    |-------|---------------|-----------|
    |Nazwa obszaru roboczego|datakostki — Szybki Start|Wybierz nazwę obszaru roboczego Azure Databricks.|
    |Subskrypcja|\<Twoja subskrypcja\>|Wybierz subskrypcję platformy Azure, której chcesz użyć.|
    |Grupa zasobów|datakostki — Szybki Start|Wybierz tę samą grupę zasobów, która została użyta dla sieci wirtualnej.|
    |Lokalizacja|\<Wybierz region, który jest najbliżej Twoich użytkowników\>|Wybierz tę samą lokalizację, w której znajduje się Twoja sieć wirtualna.|
    |Warstwa cenowa|Wybierz warstwę Standardowa lub Premium.|Więcej informacji o warstwach cenowych znajduje się na [stronie cennika usługi datacegłs](https://azure.microsoft.com/pricing/details/databricks/).|

    ![Tworzenie Azure Databricksego obszaru roboczego](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace.png)

3. Po zakończeniu wprowadzania ustawień na stronie **podstawowe** wybierz pozycję **dalej: sieć >** i Zastosuj następujące ustawienia:

    |Ustawienie|Sugerowana wartość|Opis|
    |-------|---------------|-----------|
    |Wdróż Azure Databricks obszar roboczy w Virtual Network (VNet)|Tak|To ustawienie umożliwia wdrożenie obszaru roboczego Azure Databricks w sieci wirtualnej.|
    |Virtual Network|datakostki — Szybki Start|Wybierz sieć wirtualną utworzoną w poprzedniej sekcji.|
    |Nazwa podsieci publicznej|podsieć publiczna|Użyj domyślnej nazwy podsieci publicznej.|
    |Zakres CIDR podsieci publicznej|10.179.64.0/18|Użyj zakresu CIDR do, włącznie z/26.|
    |Nazwa podsieci prywatnej|podsieć prywatna|Użyj domyślnej nazwy podsieci prywatnej.|
    |Zakres CIDR podsieci prywatnej|10.179.0.0/18|Użyj zakresu CIDR do, włącznie z/26.|

    ![Dodaj informacje o sieci wirtualnej do obszaru roboczego Azure Databricks na Azure Portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace-vnet-config.png)

3. Po zakończeniu wdrażania przejdź do zasobu Azure Databricks. Zwróć uwagę, że Komunikacja równorzędna sieci wirtualnych jest wyłączona. Zwróć uwagę na grupę zasobów i grupę zasobów zarządzanych na stronie Przegląd. 

    ![Omówienie Azure Databricks w Azure Portal](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-overview-portal.png)

    Nie można modyfikować zarządzanej grupy zasobów i nie jest ona używana do tworzenia maszyn wirtualnych. Można tworzyć tylko maszyny wirtualne w zarządzanej grupie zasobów.

    ![Azure Databricks zarządzaną grupę zasobów](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group.png)

    W przypadku niepowodzenia wdrożenia obszaru roboczego jest on nadal tworzony w stanie niepowodzenia. Usuń niepowodzenie obszaru roboczego i Utwórz nowy obszar roboczy, który rozwiązuje błędy wdrożenia. Po usunięciu obszaru roboczego niepowodzenie zostanie również usunięta zarządzana Grupa zasobów i wszystkie pomyślnie wdrożone zasoby.

## <a name="create-a-cluster"></a>Tworzenie klastra

> [!NOTE]
> Aby użyć bezpłatnego konta do utworzenia klastra usługi Azure Databricks, przed utworzeniem klastra przejdź do swojego profilu i zmień swoją subskrypcję na **płatność zgodnie z rzeczywistym użyciem**. Aby uzyskać więcej informacji, zobacz [Bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

1. Wróć do usługi Azure Databricks i wybierz pozycję **Uruchom obszar roboczy** na stronie **Przegląd** .

2. Wybierz pozycję **klastry** > **+ Utwórz klaster**. Następnie Utwórz nazwę klastra, na przykład *datakosteks — szybki start-Cluster*i zaakceptuj pozostałe ustawienia domyślne. Wybierz pozycję **Utwórz klaster**.

    ![Utwórz klaster Azure Databricks](./media/quickstart-create-databricks-workspace-vnet-injection/create-cluster.png)

3. Po uruchomieniu klastra Wróć do zarządzanej grupy zasobów w Azure Portal. Zwróć uwagę na nowe maszyny wirtualne, dyski, adresy IP i interfejsy sieciowe. Interfejs sieciowy jest tworzony w każdej podsieci publicznej i prywatnej z adresami IP.  

    ![Azure Databricks zarządzaną grupę zasobów po utworzeniu klastra](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group2.png)

4. Wróć do obszaru roboczego Azure Databricks i wybierz utworzony klaster. Następnie przejdź do karty **wykonawcy** na stronie **interfejsu użytkownika platformy Spark** . Zwróć uwagę, że adresy dla sterownika i modułów wykonujących znajdują się w zakresie podsieci prywatnych. W tym przykładzie sterownik to 10.179.0.6, a wykonawcy to 10.179.0.4 i 10.179.0.5. Adresy IP mogą być różne.

    ![Azure Databricks modułów wykonujących interfejs użytkownika platformy Spark](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-sparkui-executors.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy skończysz korzystać z tego artykułu, możesz zakończyć działanie klastra. Aby to zrobić, w obszarze roboczym usługi Azure Databricks wybierz pozycję **Klastry** w lewym okienku. W obszarze klastra, którego działanie chcesz zakończyć, przesuń kursor na wielokropek w kolumnie **Akcje**, a następnie wybierz ikonę **Zakończ**. Spowoduje to zatrzymanie klastra.

Jeśli klaster nie zostanie ręcznie zakończony, zostanie on automatycznie zatrzymany, pod warunkiem, że podczas tworzenia klastra zaznaczono pole wyboru **Przerwij po \_ \_ minutach braku aktywności** . W takim przypadku nieaktywny klaster automatycznie zatrzymuje się po określonym czasie.

Jeśli nie chcesz ponownie używać klastra, możesz usunąć grupę zasobów utworzoną w Azure Portal.

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano tworzenie klastra Spark w Azure Databricks wdrożonym w sieci wirtualnej. Przejdź do następnego artykułu, aby dowiedzieć się, jak wykonać zapytanie dotyczące kontenera Docker SQL Server Linux w sieci wirtualnej przy użyciu programu JDBC z notesu Azure Databricks.  

> [!div class="nextstepaction"]
>[Wysyłanie zapytań do kontenera platformy Docker w systemie Linux z programem SQL Server w sieci wirtualnej z notesu usługi Azure Databricks](vnet-injection-sql-server.md)
