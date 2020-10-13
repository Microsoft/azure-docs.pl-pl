---
title: Zarządzanie potencjalnymi klientami za pomocą usługi Azure Blob Storage — Microsoft Commercial Marketplace
description: Dowiedz się, jak używać usługi Azure BLOB do konfigurowania potencjalnych klientów dla Microsoft AppSource i witryny Azure Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 05/01/2020
ms.openlocfilehash: e6ad75178bdc8ce7db18555508e2b819f08352f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86121915"
---
# <a name="use-azure-blob-storage-to-manage-commercial-marketplace-leads"></a>Korzystanie z usługi Azure Blob Storage do zarządzania komercyjnymi klientami portalu Marketplace

>[!Caution]
>Komercyjna pomoc techniczna portalu Azure Blob Storage jest przestarzała i nie jest już rozwiązaniem do przetwarzania potencjalnych klientów z oferty. Jeśli obecnie masz komercyjną ofertę rynkową, która jest skonfigurowana dla usługi Azure Blob Management, nie będziesz już otrzymywać potencjalnych klientów. Zaktualizuj konfigurację zarządzania potencjalnymi klientami do dowolnej z pozostałych opcji zarządzania potencjalnymi klientami. Zapoznaj się z innymi opcjami na [stronie docelowej zarządzania potencjalnymi klientami](./commercial-marketplace-get-customer-leads.md).

 Jeśli system zarządzania relacjami z klientami (CRM) nie jest jawnie obsługiwany w centrum partnerskim na potrzeby otrzymywania Microsoft AppSource i potencjalnych klientów portalu Azure Marketplace, możesz użyć usługi Azure Blob Storage. Następnie można wyeksportować dane i zaimportować je do systemu CRM. Instrukcje zawarte w tym artykule pomogą w procesie tworzenia konta usługi Azure Storage i obiektu BLOB w ramach tego konta. Ponadto możesz utworzyć nowy przepływ przy użyciu narzędzia do automatyzacji, aby wysłać powiadomienie e-mail, gdy oferta odbierze potencjalny klient.

>[!NOTE]
>Łącznik automatyzacji, używany w tych instrukcjach, wymaga płatnej subskrypcji do automatyzacji. Przed wykonaniem instrukcji opisanych w tym artykule upewnij się, że to konto jest odpowiednie.

## <a name="configure-azure-blob-storage"></a>Konfigurowanie usługi Azure Blob Storage

1. Jeśli nie masz konta platformy Azure, możesz [utworzyć bezpłatne konto wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

2. Po uaktywnieniu konta platformy Azure Zaloguj się do [Azure Portal](https://portal.azure.com).

3. W Azure Portal Utwórz konto magazynu, wykonując poniższą procedurę.  
    1. Wybierz pozycję **+ Utwórz zasób** na pasku menu po lewej stronie.  **Nowe** okienko (blok) zostanie wyświetlone po prawej stronie.
    2. Wybierz pozycję **Magazyn** w **nowym** okienku.  Lista **proponowanych** zostanie wyświetlona po prawej stronie.
    3. Wybierz **konto magazynu** , aby rozpocząć tworzenie konta.  Postępuj zgodnie z instrukcjami w artykule [Tworzenie konta magazynu](../../storage/common/storage-quickstart-create-account.md?tabs=azure-portal).

    ![Procedura tworzenia konta usługi Azure Storage](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    Aby uzyskać więcej informacji na temat kont magazynu, zobacz ten [Przewodnik Szybki Start](../../storage/blobs/storage-quickstart-blobs-portal.md).  Aby uzyskać więcej informacji na temat cennika usługi Storage, zobacz [Cennik usługi Storage](https://azure.microsoft.com/pricing/details/storage/).

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

## <a name="configure-your-offer-to-send-leads-to-azure-blob-storage"></a>Konfigurowanie oferty do wysyłania potencjalnych klientów do usługi Azure Blob Storage

Gdy wszystko jest gotowe do skonfigurowania informacji dotyczących zarządzania potencjalnym liderem oferty w portalu wydawców, wykonaj następujące czynności:

1. Przejdź do strony **Konfiguracja oferty** dla swojej oferty.
2. W sekcji **potencjalni klienci** wybierz pozycję **Połącz**.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-azure-blob/customer-leads.png" alt-text="Potencjalni klienci":::

3. W oknie podręcznym szczegóły połączenia wybierz pozycję **obiekt blob platformy Azure** dla miejsca docelowego potencjalnego klienta.

    ![Szczegóły połączenia](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. Podaj **nazwę kontenera** i **Parametry połączenia konta magazynu** , które zostały uzyskane z poniższych instrukcji.

    * Przykład nazwy kontenera: `marketplaceleadcontainer`
    * Przykład parametrów połączenia konta magazynu: `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net` ![ szczegóły połączenia](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. Wybierz pozycję **Zapisz**.

    > [!NOTE]
    > Musisz zakończyć konfigurowanie reszty oferty i opublikować ją przed odebraniem potencjalnych klientów do oferty.


