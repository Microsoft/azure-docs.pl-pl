---
title: Przekształć XML w mapy XSLT
description: Dodaj mapy XSLT, aby przekształcić XML w Azure Logic Apps z Pakiet integracyjny dla przedsiębiorstw
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/06/2019
ms.openlocfilehash: 62c3d4533dd04dbb5a2ce0c73afa52b81d433913
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91570781"
---
# <a name="transform-xml-with-maps-in-azure-logic-apps-with-enterprise-integration-pack"></a>Przekształć kod XML przy użyciu map w usłudze Azure Logic Apps z Pakietem integracyjnym dla przedsiębiorstw

Aby przesłać dane XML między formatami dla scenariuszy integracji przedsiębiorstw w usłudze Azure Logic Apps, aplikacja logiki musi używać map, a właściwie map XSLT (Extensible Style sheet Language Transformations). Mapa to dokument XML, który opisuje sposób konwersji danych z dokumentu XML na inny format. 

Załóżmy na przykład, że regularnie otrzymujesz zamówienia B2B lub faktury od klienta, który używa formatu daty YYYMMDD. Organizacja używa jednak formatu daty MMDDYYY. Można zdefiniować i użyć mapy, która przekształca format daty YYYMMDD w format MMDDYYY przed zapisaniem szczegółów zamówienia lub faktury w bazie danych działań klienta.

Aby uzyskać ograniczenia dotyczące kont integracji i artefaktów, takich jak mapy, zobacz [Informacje o limitach i konfiguracji Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* [Konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , w którym przechowywane są mapy i inne artefakty dla rozwiązań Enterprise Integration i Business-to-Business (B2B).

* Jeśli mapa odwołuje się do zestawu zewnętrznego, należy przekazać *zestaw i mapę* do konta integracji. Upewnij się, że [*najpierw przekażesz zestaw*](#add-assembly), a następnie Przekaż mapę, która odwołuje się do zestawu.

  Jeśli zestaw ma 2 MB lub mniejszy, możesz dodać swój zestaw do konta integracji *bezpośrednio* z Azure Portal. Jeśli jednak zestaw lub mapa jest większy niż 2 MB, ale nie większy niż [limit rozmiaru dla zestawów lub map](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits), dostępne są następujące opcje:

  * W przypadku zestawów potrzebny jest kontener obiektów blob platformy Azure, w którym można przekazać zestaw i lokalizację tego kontenera. Dzięki temu można podać tę lokalizację później, gdy dodasz zestaw do konta integracji. 
  W przypadku tego zadania potrzebne są następujące elementy:

    | Element | Opis |
    |------|-------------|
    | [Konto usługi Azure Storage](../storage/common/storage-account-overview.md) | Na tym koncie Utwórz kontener obiektów blob platformy Azure dla Twojego zestawu. Dowiedz się, [jak utworzyć konto magazynu](../storage/common/storage-account-create.md). |
    | Kontener obiektów blob | W tym kontenerze można przekazać zestaw. Ta lokalizacja kontenera jest również potrzebna po dodaniu zestawu do konta integracji. Dowiedz się, jak [utworzyć kontener obiektów BLOB](../storage/blobs/storage-quickstart-blobs-portal.md). |
    | [Eksplorator usługi Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) | To narzędzie pomaga łatwiej zarządzać kontami magazynu i kontenerami obiektów BLOB. Aby użyć Eksplorator usługi Storage, [Pobierz i zainstaluj Eksplorator usługi Azure Storage](https://www.storageexplorer.com/). Następnie połącz Eksplorator usługi Storage z kontem magazynu, wykonując kroki opisane w temacie [wprowadzenie do Eksplorator usługi Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md). Aby dowiedzieć się więcej, zobacz [Szybki Start: Tworzenie obiektu BLOB w magazynie obiektów przy użyciu Eksplorator usługi Azure Storage](../storage/blobs/storage-quickstart-blobs-storage-explorer.md). <p>Lub w Azure Portal Znajdź i wybierz konto magazynu. W menu konto magazynu wybierz pozycję **Eksplorator usługi Storage**. |
    |||

  * W przypadku map można obecnie dodawać większe mapy przy użyciu [Azure Logic Apps API REST — Maps](/rest/api/logic/maps/createorupdate).

Nie potrzebujesz aplikacji logiki podczas tworzenia i dodawania map. Jednak aby użyć mapy, aplikacja logiki wymaga połączenia z kontem integracji, w którym jest przechowywana ta mapa. Dowiedz się [, jak połączyć Aplikacje logiki z kontami integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account). Jeśli nie masz jeszcze aplikacji logiki, Dowiedz się, [jak tworzyć aplikacje logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-assembly"></a>

## <a name="add-referenced-assemblies"></a>Dodaj zestawy, do których istnieją odwołania

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu poświadczeń konta Azure.

1. Aby znaleźć i otworzyć konto integracji, w głównym menu platformy Azure wybierz pozycję **wszystkie usługi**. 
   W polu wyszukiwania wprowadź ciąg "konto integracji". 
   Wybierz pozycję **konta integracji**.

   ![Znajdź konto integracji](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Wybierz konto integracji, do którego chcesz dodać zestaw, na przykład:

   ![Wybierz konto integracji](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. Na stronie **Przegląd** konta integracji w obszarze **składniki**wybierz kafelek **zespoły** .

   ![Wybierz pozycję "zestawy"](./media/logic-apps-enterprise-integration-maps/select-assemblies.png)

1. Po otwarciu strony **zestawy** wybierz pozycję **Dodaj**.

   ![Zrzut ekranu, który podświetla przycisk Dodaj na stronie zestawy.](./media/logic-apps-enterprise-integration-maps/add-assembly.png)

Na podstawie rozmiaru pliku zestawu wykonaj kroki przekazywania zestawu, który jest [do 2 MB](#smaller-assembly) lub [więcej niż 2 MB, ale tylko do 8 MB](#larger-assembly).
Aby uzyskać limity dotyczące liczby zestawów na kontach integracji, zobacz [limity i konfiguracja dla Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits).

> [!NOTE]
> W przypadku zmiany zestawu należy również zaktualizować mapę niezależnie od tego, czy mapa zawiera zmiany.

<a name="smaller-assembly"></a>

### <a name="add-assemblies-up-to-2-mb"></a>Dodaj zestawy do 2 MB

1. W obszarze **Dodawanie zestawu**wpisz nazwę zestawu. Pozostaw wybrany **mały plik** . Obok pola **zestaw** wybierz ikonę folderu. Znajdź i wybierz zestaw, który jest przekazywany, na przykład:

   ![Przekaż mniejszy zestaw](./media/logic-apps-enterprise-integration-maps/upload-assembly-file.png)

   We właściwości **Nazwa zestawu** nazwa pliku zestawu jest wyświetlana automatycznie po wybraniu zestawu.

1. Gdy wszystko będzie gotowe, wybierz **przycisk OK**.

   Po zakończeniu przekazywania pliku zestawu zostanie on wyświetlony na liście **zestawy** .

   ![Lista przekazanych zestawów](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies-list.png)

   Na stronie **Przegląd** konta integracji w obszarze **składniki**kafelek **zestawy** są teraz wyświetlane Liczba przekazanych zestawów, na przykład:

   ![Przekazane zestawy](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies.png)

<a name="larger-assembly"></a>

### <a name="add-assemblies-more-than-2-mb"></a>Dodaj zestawy więcej niż 2 MB

Aby dodać większe zestawy, można przekazać zestaw do kontenera obiektów blob platformy Azure na koncie usługi Azure Storage. Kroki związane z dodawaniem zestawów różnią się w zależności od tego, czy kontener obiektów BLOB ma publiczny dostęp do odczytu. Najpierw sprawdź, czy kontener obiektów BLOB ma publiczny dostęp do odczytu, wykonując następujące czynności: [Ustaw poziom dostępu publicznego dla kontenera obiektów BLOB](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Sprawdź poziom dostępu do kontenera

1. Otwórz Eksplorator usługi Azure Storage. W oknie Eksploratora Rozwiń swoją subskrypcję platformy Azure, jeśli nie została jeszcze rozszerzona.

1. Rozwiń węzeł **konta magazynu** > {*Twoje konto magazynu*} > **kontenery obiektów BLOB**. Wybierz kontener obiektów BLOB.

1. Z menu skrótów kontenera obiektów BLOB wybierz pozycję **Ustaw poziom dostępu publicznego**.

   * Jeśli kontener obiektów BLOB ma co najmniej dostęp publiczny, wybierz pozycję **Anuluj**, a następnie wykonaj następujące kroki w dalszej części tej strony: [Przekaż do kontenerów z dostępem publicznym](#public-access-assemblies)

     ![Dostęp publiczny](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Jeśli kontener obiektów BLOB nie ma dostępu publicznego, wybierz pozycję **Anuluj**, a następnie wykonaj następujące kroki w dalszej części tej strony: [Przekaż do kontenerów bez dostępu publicznego](#no-public-access-assemblies)

     ![Brak publicznego dostępu](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-assemblies"></a>

#### <a name="upload-to-containers-with-public-access"></a>Przekaż do kontenerów z dostępem publicznym

1. Przekaż zestaw do konta magazynu. 
   W oknie po prawej stronie wybierz pozycję **Przekaż**.

1. Po zakończeniu przekazywania wybierz przekazany zestaw. Na pasku narzędzi wybierz opcję **Kopiuj adres URL** , aby skopiować adres URL zestawu.

1. Wróć do Azure Portal, w którym jest otwarte okienko **Dodawanie zestawu** . 
   Wprowadź nazwę zestawu. 
   Wybierz **duży plik (większy niż 2 MB)**.

   Teraz pojawi się pole **Content URI** , a nie pole **zestawu** .

1. W polu **Identyfikator URI zawartości** Wklej adres URL zestawu. 
   Zakończ Dodawanie zestawu.

Po zakończeniu przekazywania zestawu schemat pojawia się na liście **zestawy** .
Na stronie **Przegląd** konta integracji w obszarze **składniki**kafelek **zestawy** są teraz wyświetlane Liczba przekazanych zestawów.

<a name="no-public-access-assemblies"></a>

#### <a name="upload-to-containers-without-public-access"></a>Przekaż do kontenerów bez dostępu publicznego

1. Przekaż zestaw do konta magazynu. 
   W oknie po prawej stronie wybierz pozycję **Przekaż**.

1. Po zakończeniu przekazywania Wygeneruj sygnaturę dostępu współdzielonego dla Twojego zestawu. 
   Z menu skrótów zestawu wybierz polecenie **Pobierz sygnaturę dostępu współdzielonego**.

1. W okienku **sygnatura dostępu współdzielonego** wybierz pozycję **Generuj identyfikator URI sygnatury dostępu współdzielonego na poziomie kontenera**  >  **Utwórz**. 
   Po wygenerowaniu adresu URL sygnatury dostępu współdzielonego obok pola **adres URL** wybierz **Kopiuj**.

1. Wróć do Azure Portal, w którym jest otwarte okienko **Dodawanie zestawu** . 
   Wprowadź nazwę zestawu. 
   Wybierz **duży plik (większy niż 2 MB)**.

   Teraz pojawi się pole **Content URI** , a nie pole **zestawu** .

1. W polu **Identyfikator URI zawartości** wklej wcześniej wygenerowany identyfikator URI SAS. Zakończ Dodawanie zestawu.

Po zakończeniu przekazywania zestawu zostanie on wyświetlony na liście **schematy** . Na stronie **Przegląd** konta integracji w obszarze **składniki**kafelek **zestawy** są teraz wyświetlane Liczba przekazanych zestawów.

## <a name="create-maps"></a>Tworzenie map

Aby utworzyć dokument XSLT, którego można użyć jako mapy, można użyć programu Visual Studio 2015 do tworzenia projektu integracji BizTalk przy użyciu [pakiet integracyjny dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md). W tym projekcie można utworzyć plik mapy integracji, który umożliwia wizualne Mapowanie elementów między dwoma plikami schematu XML. Po skompilowaniu tego projektu uzyskasz dokument XSLT.
Aby uzyskać ograniczenia dotyczące ilości mapy na kontach integracji, zobacz [limity i konfiguracja dla Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits). 

## <a name="add-maps"></a>Dodawanie map

Po przekazaniu zestawów, do których odwołuje się mapowanie, można teraz przekazać mapę.

1. Jeśli jeszcze nie zalogowano się, zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu poświadczeń konta platformy Azure. 

1. Jeśli Twoje konto integracji nie jest jeszcze otwarte, w głównym menu platformy Azure wybierz pozycję **wszystkie usługi**. 
   W polu wyszukiwania wprowadź ciąg "konto integracji". 
   Wybierz pozycję **konta integracji**.

   ![Znajdź konto integracji](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Wybierz konto integracji, do którego chcesz dodać mapę, na przykład:

   ![Wybierz konto integracji](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. Na stronie **Przegląd** konta integracji w obszarze **składniki**wybierz kafelek **mapy** .

   ![Wybierz pozycję "Maps"](./media/logic-apps-enterprise-integration-maps/select-maps.png)

1. Po otwarciu strony **Maps** wybierz pozycję **Dodaj**.

   ![Wybierz pozycję "Dodaj"](./media/logic-apps-enterprise-integration-maps/add-map.png)  

<a name="smaller-map"></a>

### <a name="add-maps-up-to-2-mb"></a>Dodaj mapy do 2 MB

1. W obszarze **Dodaj mapę**wpisz nazwę mapy. 

1. W obszarze **typ mapy**wybierz typ, na przykład: **Liquid**, **XSLT**, **XSLT 2,0**lub **XSLT 3,0**.

1. Pozostaw wybrany **mały plik** . Obok pola **Mapa** wybierz ikonę folderu. Znajdź i wybierz przekazaną mapę, na przykład:

   ![Przekaż mapę](./media/logic-apps-enterprise-integration-maps/upload-map-file.png)

   Po podaniu pustej właściwości **name** nazwa pliku mapy zostanie automatycznie wyświetlona po wybraniu pliku mapy. 
   Można jednak użyć dowolnej unikatowej nazwy.

1. Gdy wszystko będzie gotowe, wybierz **przycisk OK**. 
   Po zakończeniu przekazywania pliku mapy mapa zostanie wyświetlona na liście **Maps** .

   ![Lista przekazanych map](./media/logic-apps-enterprise-integration-maps/uploaded-maps-list.png)

   Na stronie **Przegląd** konta integracji w obszarze **składniki**kafelek **mapy** zawiera teraz liczbę przekazanych map, na przykład:

   ![Przekazane mapy](./media/logic-apps-enterprise-integration-maps/uploaded-maps.png)

<a name="larger-map"></a>

### <a name="add-maps-more-than-2-mb"></a>Dodaj mapy więcej niż 2 MB

Obecnie aby dodać większe mapy, użyj [Azure Logic Apps interfejsu API REST — Maps](/rest/api/logic/maps/createorupdate).

<!--

To add larger maps, you can upload your map to 
an Azure blob container in your Azure storage account. 
Your steps for adding maps differ based whether your 
blob container has public read access. So first, check 
whether or not your blob container has public read 
access by following these steps: 
[Set public access level for blob container](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### Check container access level

1. Open Azure Storage Explorer. In the Explorer window, 
   expand your Azure subscription if not already expanded.

1. Expand **Storage Accounts** > {*your-storage-account*} > 
   **Blob Containers**. Select your blob container.

1. From your blob container's shortcut menu, 
   select **Set Public Access Level**.

   * If your blob container has at least public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers with public access](#public-access)

     ![Public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * If your blob container doesn't have public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers without public access](#public-access)

     ![No public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-maps"></a>

### Add maps to containers with public access

1. Upload the map to your storage account. 
   In the right-hand window, choose **Upload**. 

1. After you finish uploading, select your 
   uploaded map. On the toolbar, choose **Copy URL** 
   so that you copy the map's URL.

1. Return to the Azure portal where the 
   **Add Map** pane is open. Choose **Large file**. 

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste your map's URL. 
   Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

<a name="no-public-access-maps"></a>

### Add maps to containers with no public access

1. Upload the map to your storage account. 
   In the right-hand window, choose **Upload**.

1. After you finish uploading, generate a 
   shared access signature (SAS) for your schema. 
   From your map's shortcut menu, 
   select **Get Shared Access Signature**.

1. In the **Shared Access Signature** pane, select 
   **Generate container-level shared access signature URI** > **Create**. 
   After the SAS URL gets generated, next to the **URL** box, choose **Copy**.

1. Return to the Azure portal where the 
   **Add Maps** pane is open. Choose **Large file**.

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste the SAS URI 
   you previously generated. Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

-->

## <a name="edit-maps"></a>Edytowanie map

Aby zaktualizować istniejącą mapę, należy przekazać nowy plik mapy, który ma odpowiednie zmiany. Można jednak najpierw pobrać istniejącą mapę do edycji.

1. W [Azure Portal](https://portal.azure.com)Znajdź i Otwórz konto integracji, jeśli nie jest jeszcze otwarte.

1. W głównym menu platformy Azure wybierz pozycję **wszystkie usługi**. W polu wyszukiwania wprowadź ciąg "konto integracji". Wybierz pozycję **konta integracji**.

1. Wybierz konto integracji, do którego chcesz zaktualizować mapę.

1. Na stronie **Przegląd** konta integracji w obszarze **składniki**wybierz kafelek **mapy** .

1. Po otwarciu strony **Maps** wybierz swoją mapę. 
   Aby najpierw pobrać i zmodyfikować mapę, wybierz pozycję **Pobierz**i Zapisz mapę.

1. Gdy wszystko będzie gotowe do przekazania zaktualizowanej mapy, na stronie **mapy** Wybierz mapę, którą chcesz zaktualizować, a następnie wybierz pozycję **Aktualizuj**.

1. Znajdź i wybierz zaktualizowaną mapę, która ma zostać przekazana. 
   Po zakończeniu przekazywania pliku mapy na liście **Maps** zostanie wyświetlona zaktualizowana mapa.

## <a name="delete-maps"></a>Usuwanie map

1. W [Azure Portal](https://portal.azure.com)Znajdź i Otwórz konto integracji, jeśli nie jest jeszcze otwarte.

1. W głównym menu platformy Azure wybierz pozycję **wszystkie usługi**. 
   W polu wyszukiwania wprowadź ciąg "konto integracji". 
   Wybierz pozycję **konta integracji**.

1. Wybierz konto integracji, dla którego chcesz usunąć mapę.

1. Na stronie **Przegląd** konta integracji w obszarze **składniki**wybierz kafelek **mapy** .

1. Po otwarciu strony **Maps** Wybierz mapę, a następnie wybierz pozycję **Usuń**.

1. Aby potwierdzić, że chcesz usunąć mapę, wybierz pozycję **tak**.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o Pakiet integracyjny dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md)  
* [Dowiedz się więcej o schematach](../logic-apps/logic-apps-enterprise-integration-schemas.md)
* [Dowiedz się więcej o transformacjech](../logic-apps/logic-apps-enterprise-integration-transform.md)
