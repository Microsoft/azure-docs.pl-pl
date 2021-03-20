---
title: Sprawdzanie poprawności kodu XML ze schematami
description: Dodaj schematy do walidacji dokumentów XML w Azure Logic Apps z Pakiet integracyjny dla przedsiębiorstw
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/06/2019
ms.openlocfilehash: ade29318fdd1510d03ddd208534258a30b256e98
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "75979378"
---
# <a name="validate-xml-with-schemas-in-azure-logic-apps-with-enterprise-integration-pack"></a>Waliduj kod XML przy użyciu schematów w usłudze Azure Logic Apps z Pakietem integracyjnym dla przedsiębiorstw

Aby sprawdzić, czy dokumenty używają prawidłowego kodu XML i mają oczekiwane dane we wstępnie zdefiniowanym formacie scenariuszy integracji przedsiębiorstwa w Azure Logic Apps, aplikacja logiki może używać schematów. Schemat może również sprawdzać poprawność komunikatów, które są przeznaczone dla aplikacji Logic Apps Exchange w scenariuszach biznes-to-Business (B2B).

Aby uzyskać ograniczenia dotyczące kont integracji i artefaktów, takich jak schematy, zobacz [Informacje o limitach i konfiguracji Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>.

* [Konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , w którym przechowywane są schematy i inne artefakty dla rozwiązań Enterprise Integration i Business-to-Business (B2B). 

  Jeśli schemat ma wartość [2 MB lub mniejszą](#smaller-schema), możesz dodać schemat do konta integracji bezpośrednio z Azure Portal. Jeśli jednak schemat jest większy niż 2 MB, ale nie większy niż [limit rozmiaru schematu](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits), można przekazać schemat do konta usługi Azure Storage. 
  Aby dodać ten schemat do konta integracji, możesz połączyć się z kontem magazynu z konta integracji. 
  Oto elementy, które są potrzebne w przypadku tego zadania: 

  * [Konto usługi Azure Storage](../storage/common/storage-account-overview.md) , na którym tworzysz kontener obiektów BLOB dla schematu. Dowiedz się, jak [utworzyć konto magazynu](../storage/common/storage-account-create.md). 

  * Kontener obiektów BLOB do przechowywania schematu. Dowiedz się, jak [utworzyć kontener obiektów BLOB](../storage/blobs/storage-quickstart-blobs-portal.md). 
  Identyfikator URI zawartości kontenera jest potrzebny później, gdy dodasz schemat do konta integracji.

  * [Eksplorator usługi Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md), za pomocą którego można zarządzać kontami magazynu i kontenerami obiektów BLOB. 
  Aby użyć Eksplorator usługi Storage, wybierz jedną z opcji:
  
    * W Azure Portal Znajdź i wybierz konto magazynu. 
    W menu konto magazynu wybierz pozycję **Eksplorator usługi Storage**.

    * W przypadku wersji klasycznej [Pobierz i zainstaluj Eksplorator usługi Azure Storage](https://www.storageexplorer.com/). 
    Następnie połącz Eksplorator usługi Storage z kontem magazynu, wykonując kroki opisane w temacie [wprowadzenie do Eksplorator usługi Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md). 
    Aby dowiedzieć się więcej, zobacz [Szybki Start: Tworzenie obiektu BLOB w magazynie obiektów przy użyciu Eksplorator usługi Azure Storage](../storage/blobs/storage-quickstart-blobs-storage-explorer.md).

Aplikacja logiki nie jest potrzebna podczas tworzenia i dodawania schematów. Jednak aby użyć schematu, aplikacja logiki wymaga połączenia z kontem integracji, w którym jest przechowywany ten schemat. Dowiedz się [, jak połączyć Aplikacje logiki z kontami integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account). Jeśli nie masz jeszcze aplikacji logiki, Dowiedz się, [jak tworzyć aplikacje logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-schemas"></a>Dodawanie schematów

1. Zaloguj się do <a href="https://portal.azure.com" target="_blank">witryny Azure Portal</a> przy użyciu poświadczeń konta Azure.

1. Aby znaleźć i otworzyć konto integracji, w głównym menu platformy Azure wybierz pozycję **wszystkie usługi**. W polu wyszukiwania wprowadź ciąg "konto integracji". Wybierz pozycję **konta integracji**.

   ![Znajdź konto integracji](./media/logic-apps-enterprise-integration-schemas/find-integration-account.png)

1. Wybierz konto integracji, do którego chcesz dodać schemat, na przykład:

   ![Wybierz konto integracji](./media/logic-apps-enterprise-integration-schemas/select-integration-account.png)

1. Na stronie **Przegląd** konta integracji w obszarze **składniki** wybierz kafelek **schematy** .

   ![Wybierz "schematy"](./media/logic-apps-enterprise-integration-schemas/select-schemas.png)

1. Po otwarciu strony **schematy** wybierz pozycję **Dodaj**.

   ![Wybierz pozycję "Dodaj"](./media/logic-apps-enterprise-integration-schemas/add-schema.png)

Na podstawie rozmiaru pliku schematu (XSD) postępuj zgodnie z instrukcjami dotyczącymi przekazywania schematu, który jest [do 2 MB](#smaller-schema) lub [więcej niż 2 MB, do 8 MB](#larger-schema).

<a name="smaller-schema"></a>

### <a name="add-schemas-up-to-2-mb"></a>Dodaj schematy do 2 MB

1. W obszarze **Dodaj schemat** wprowadź nazwę schematu. 
   Pozostaw wybrany **mały plik** . Obok pola **schemat** wybierz ikonę folderu. Znajdź i wybierz przeszukiwany schemat, na przykład:

   ![Przekaż mniejszy schemat](./media/logic-apps-enterprise-integration-schemas/upload-smaller-schema-file.png)

1. Gdy wszystko będzie gotowe, wybierz **przycisk OK**.

   Po zakończeniu przekazywania schematu na liście **schematy** zostanie wyświetlony schemat.

<a name="larger-schema"></a>

### <a name="add-schemas-more-than-2-mb"></a>Dodaj schematy więcej niż 2 MB

Aby dodać większy schemat, możesz przekazać schemat do kontenera obiektów blob platformy Azure na koncie usługi Azure Storage. Kroki związane z dodawaniem schematów różnią się w zależności od tego, czy kontener obiektów BLOB ma publiczny dostęp do odczytu. Najpierw sprawdź, czy kontener obiektów BLOB ma publiczny dostęp do odczytu, wykonując następujące czynności: [Ustaw poziom dostępu publicznego dla kontenera obiektów BLOB](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Sprawdź poziom dostępu do kontenera

1. Otwórz Eksplorator usługi Azure Storage. W oknie Eksploratora Rozwiń swoją subskrypcję platformy Azure, jeśli nie została jeszcze rozszerzona.

1. Rozwiń węzeł **konta magazynu** > {*Twoje konto magazynu*} > **kontenery obiektów BLOB**. Wybierz kontener obiektów BLOB.

1. Z menu skrótów kontenera obiektów BLOB wybierz pozycję **Ustaw poziom dostępu publicznego**.

   * Jeśli kontener obiektów BLOB ma co najmniej dostęp publiczny, wybierz pozycję **Anuluj**, a następnie wykonaj następujące kroki w dalszej części tej strony: [Przekaż do kontenerów z dostępem publicznym](#public-access)

     ![Dostęp publiczny](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Jeśli kontener obiektów BLOB nie ma dostępu publicznego, wybierz pozycję **Anuluj**, a następnie wykonaj następujące kroki w dalszej części tej strony: [Przekaż do kontenerów bez dostępu publicznego](#public-access)

     ![Brak publicznego dostępu](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access"></a>

#### <a name="upload-to-containers-with-public-access"></a>Przekaż do kontenerów z dostępem publicznym

1. Przekaż schemat do konta magazynu. 
   W oknie po prawej stronie wybierz pozycję **Przekaż**.

1. Po zakończeniu przekazywania wybierz przekazany schemat. Na pasku narzędzi wybierz opcję **Kopiuj adres URL** , aby skopiować adres URL schematu.

1. Wróć do Azure Portal, w którym jest otwarte okienko **Dodaj schemat** . 
   Wprowadź nazwę zestawu. 
   Wybierz **duży plik (większy niż 2 MB)**. 

   Teraz pojawi się pole **Content URI** , a nie pole **schematu** .

1. W polu **Identyfikator URI zawartości** Wklej adres URL schematu. 
   Zakończ dodawanie schematu.

Po zakończeniu przekazywania schematu na liście **schematy** zostanie wyświetlony schemat. Na stronie **Przegląd** konta integracji w obszarze **składniki** na kafelku **schematy** jest teraz wyświetlana liczba przekazanych schematów.

<a name="no-public-access"></a>

#### <a name="upload-to-containers-without-public-access"></a>Przekaż do kontenerów bez dostępu publicznego

1. Przekaż schemat do konta magazynu. 
   W oknie po prawej stronie wybierz pozycję **Przekaż**.

1. Po zakończeniu przekazywania Wygeneruj sygnaturę dostępu współdzielonego (SAS) dla schematu. 
   Z menu podręcznego schematu wybierz pozycję **Pobierz sygnaturę dostępu współdzielonego**.

1. W okienku **sygnatura dostępu współdzielonego** wybierz pozycję **Generuj identyfikator URI sygnatury dostępu współdzielonego na poziomie kontenera**  >  **Utwórz**. 
   Po wygenerowaniu adresu URL sygnatury dostępu współdzielonego obok pola **adres URL** wybierz **Kopiuj**.

1. Wróć do Azure Portal, w którym jest otwarte okienko **Dodaj schemat** . Wybierz **duży plik**.

   Teraz pojawi się pole **Content URI** , a nie pole **schematu** .

1. W polu **Identyfikator URI zawartości** wklej wcześniej wygenerowany identyfikator URI SAS. Zakończ dodawanie schematu.

Po zakończeniu przekazywania schematu na liście **schematy** zostanie wyświetlony schemat. Na stronie **Przegląd** konta integracji w obszarze **składniki** na kafelku **schematy** jest teraz wyświetlana liczba przekazanych schematów.

## <a name="edit-schemas"></a>Edytuj schematy

Aby zaktualizować istniejący schemat, należy przekazać nowy plik schematu z żądanymi zmianami. Można jednak najpierw pobrać istniejący schemat do edycji.

1. W <a href="https://portal.azure.com" target="_blank">Azure Portal</a>Znajdź i Otwórz konto integracji, jeśli nie jest jeszcze otwarte.

1. W głównym menu platformy Azure wybierz pozycję **wszystkie usługi**. 
   W polu wyszukiwania wprowadź ciąg "konto integracji". 
   Wybierz pozycję **konta integracji**.

1. Wybierz konto integracji, do którego chcesz zaktualizować schemat.

1. Na stronie **Przegląd** konta integracji w obszarze **składniki** wybierz kafelek **schematy** .

1. Po otwarciu strony **schematy** wybierz schemat. 
   Aby najpierw pobrać i edytować schemat, wybierz pozycję **Pobierz** i Zapisz schemat.

1. Gdy wszystko będzie gotowe do przekazania zaktualizowanego schematu, na stronie **schematy** wybierz schemat, który chcesz zaktualizować, a następnie wybierz pozycję **Aktualizuj**.

1. Znajdź i wybierz zaktualizowany schemat, który chcesz przekazać. 
   Po zakończeniu przekazywania pliku schematu na liście **schematy** zostanie wyświetlony zaktualizowany schemat.

## <a name="delete-schemas"></a>Usuń schematy

1. W <a href="https://portal.azure.com" target="_blank">Azure Portal</a>Znajdź i Otwórz konto integracji, jeśli nie jest jeszcze otwarte.

1. W głównym menu platformy Azure wybierz pozycję **wszystkie usługi**. 
   W polu wyszukiwania wprowadź ciąg "konto integracji". 
   Wybierz pozycję **konta integracji**.

1. Wybierz konto integracji, do którego chcesz usunąć schemat.

1. Na stronie **Przegląd** konta integracji w obszarze **składniki** wybierz kafelek **schematy** .

1. Po otwarciu strony **schematy** wybierz schemat i wybierz polecenie **Usuń**.

1. Aby potwierdzić, że chcesz usunąć schemat, wybierz pozycję **tak**.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o Pakiet integracyjny dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md)
* [Dowiedz się więcej o mapach](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [Dowiedz się więcej o transformacjech](../logic-apps/logic-apps-enterprise-integration-transform.md)
