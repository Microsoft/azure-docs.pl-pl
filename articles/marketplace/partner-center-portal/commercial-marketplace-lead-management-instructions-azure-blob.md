---
title: Azure Table w komercyjnym programie Marketplace | Portal Azure Marketplace
description: Konfigurowanie zarządzania liderem dla obiektów blob platformy Azure
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 7/30/2019
ms.author: dsindona
ms.openlocfilehash: 062252b007e22fcd2644c8b647fc0ecc2f5938cc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80285252"
---
# <a name="lead-management-instructions-for-azure-blob"></a>Instrukcje dotyczące zarządzania potencjalnymi klientami dla obiektów blob platformy Azure

>[!Caution]
>Opcja obiektów blob platformy Azure do przetwarzania potencjalnych klientów z oferty Marketplace jest przestarzała. Jeśli obecnie masz opublikowaną ofertę z konfiguracją zarządzania potencjalnym klientem dla obiektu blob platformy Azure, nie będziesz już otrzymywać potencjalnych klientów. Zaktualizuj konfigurację zarządzania potencjalnymi klientami do dowolnej z pozostałych opcji zarządzania potencjalnymi klientami. Zapoznaj się z innymi opcjami na [stronie docelowej zarządzania potencjalnymi klientami](./commercial-marketplace-get-customer-leads.md).

Jeśli system zarządzania relacjami z klientami (CRM) nie jest jawnie obsługiwany w centrum partnerskim na potrzeby otrzymywania potencjalnych klientów w witrynie Azure Marketplace i AppSource, można użyć obiektu blob platformy Azure, aby obsłużyć te potencjalni klienci. Następnie można wyeksportować dane i zaimportować je do systemu CRM. Instrukcje zawarte w tym artykule pomogą w procesie tworzenia konta usługi Azure Storage i obiektu blob platformy Azure w ramach tego konta. Ponadto możesz utworzyć nowy przepływ przy użyciu Microsoft Flow, aby wysłać powiadomienie e-mail, gdy oferta odbierze potencjalny klient.


## <a name="how-to-configure-azure-blob"></a>Jak skonfigurować obiekt blob platformy Azure

1. Jeśli nie masz konta platformy Azure, możesz [utworzyć bezpłatne konto wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
1. Po uaktywnieniu konta platformy Azure Zaloguj się do [Azure Portal](https://portal.azure.com).
1. W Azure Portal Utwórz konto magazynu, wykonując poniższą procedurę.  
    1. Wybierz pozycję **+ Utwórz zasób** na pasku menu po lewej stronie.  **Nowe** okienko (blok) zostanie wyświetlone po prawej stronie.
    2. Wybierz pozycję **Magazyn** w **nowym** okienku.  Lista **proponowanych** zostanie wyświetlona po prawej stronie.
    3. Wybierz **konto magazynu** , aby rozpocząć tworzenie konta.  Postępuj zgodnie z instrukcjami w artykule [Tworzenie konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

    ![Procedura tworzenia konta usługi Azure Storage](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    Aby uzyskać więcej informacji na temat kont magazynu, wybierz pozycję [samouczek szybkiego startu](https://docs.microsoft.com/azure/storage/).  Aby uzyskać więcej informacji na temat cennika usługi Storage, zobacz [Cennik usługi Storage](https://azure.microsoft.com/pricing/details/storage/).

4. Zaczekaj na zainicjowanie obsługi konta magazynu, proces, który zwykle trwa kilka minut.  Następnie uzyskaj dostęp do konta magazynu ze strony **głównej** Azure Portal, wybierając pozycję **Pokaż wszystkie zasoby** lub wybierając pozycję **wszystkie zasoby** na lewym pasku nawigacyjnym Azure Portal.

    ![Dostęp do konta usługi Azure Storage](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. W okienku konto magazynu wybierz pozycję **klucze dostępu** i skopiuj wartość *Parametry połączenia* dla klucza. Zapisz tę wartość, ponieważ jest to wartość *parametrów połączenia konta magazynu* , która będzie potrzebna w portalu wydawców w celu otrzymywania potencjalnych klientów na potrzeby oferty z witryny Marketplace.

     Przykładem połączenia Sting jest:

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![Klucz usługi Azure Storage](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. Na stronie konto magazynu wybierz pozycję **obiekty blob**.

   ![Klucz usługi Azure Storage](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. Na stronie obiekty blob wybierz przycisk **+ kontener** .

8. Wpisz **nazwę** nowego kontenera. Nazwa kontenera musi być zapisana małymi literami, zaczynać się literą lub cyfrą i może zawierać tylko litery, cyfry i znak kreski (-). Aby uzyskać dodatkowe informacje o regułach nazewnictwa kontenerów i obiektów blob, zobacz [Nazewnictwo i odwołania do kontenerów, obiektów blob i metadanych](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

    Zapisz tę wartość, ponieważ jest to wartość *nazwy kontenera* , którą należy podać w portalu wydawców, aby otrzymywać potencjalnych klientów na potrzeby oferty portalu Marketplace.

9. Ustaw poziom publicznego dostępu do kontenera jako **prywatny (bez dostępu anonimowego)**.

10. Wybierz przycisk **OK**, aby utworzyć kontener.

    ![Nowy kontener](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-blob"></a>Konfigurowanie oferty do wysyłania potencjalnych klientów do obiektu blob platformy Azure

Gdy wszystko jest gotowe do skonfigurowania informacji dotyczących zarządzania potencjalnym liderem oferty w portalu wydawców, wykonaj następujące czynności:

1. Przejdź do strony **Konfiguracja oferty** dla swojej oferty.
2. Wybierz pozycję **Połącz** w sekcji Zarządzanie potencjalnymi klientami.

    ![Połącz ofertę](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-offer.png)

3. W oknie podręcznym szczegóły połączenia wybierz pozycję **obiekt blob platformy Azure** dla miejsca docelowego potencjalnego klienta.

    ![Szczegóły połączenia](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. Podaj **nazwę kontenera** i **Parametry połączenia konta magazynu** , które zostały uzyskane z poniższych instrukcji.

    * Przykład nazwy kontenera:`marketplaceleadcontainer`
    * Przykład parametrów połączenia konta magazynu: `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net` ![szczegóły połączenia](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. Wybierz pozycję **Zapisz**.

    > [!NOTE]
    > Musisz zakończyć konfigurowanie reszty oferty i opublikować ją przed odebraniem potencjalnych klientów do oferty.


