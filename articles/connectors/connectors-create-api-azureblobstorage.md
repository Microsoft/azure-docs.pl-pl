---
title: Nawiązywanie połączenia z usługą Azure Blob Storage
description: Tworzenie obiektów blob i zarządzanie nimi na kontach usługi Azure Storage przy użyciu Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: cd23ff0f5ad9912440d38903a344011b069aaf16
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92677722"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-by-using-azure-logic-apps"></a>Tworzenie obiektów blob i zarządzanie nimi w usłudze Azure Blob Storage przy użyciu Azure Logic Apps

W tym artykule pokazano, jak można uzyskać dostęp do plików przechowywanych jako obiekty blob w ramach konta usługi Azure Storage i zarządzać nimi z poziomu aplikacji logiki za pomocą łącznika usługi Azure Blob Storage. Dzięki temu można tworzyć aplikacje logiki, które automatyzują zadania i przepływy pracy związane z zarządzaniem plikami. Na przykład możesz tworzyć aplikacje logiki, które tworzą, pobierają, aktualizują i usuwają pliki na koncie magazynu.

Załóżmy, że masz narzędzie, które jest aktualizowane w witrynie sieci Web systemu Azure. który działa jako wyzwalacz aplikacji logiki. Po wystąpieniu tego zdarzenia aplikacja logiki może aktualizować jakiś plik w kontenerze magazynu obiektów blob, który jest akcją w aplikacji logiki.

Jeśli dopiero zaczynasz tworzyć aplikacje logiki, zapoznaj [się z tematem Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [Szybki Start: Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Informacje techniczne dotyczące konkretnego łącznika można znaleźć w [dokumentacji łącznika usługi Azure Blob Storage](/connectors/azureblobconnector/).

> [!IMPORTANT]
> Aplikacje logiki nie mogą bezpośrednio uzyskiwać dostępu do kont magazynu, które znajdują się za zaporami, jeśli znajdują się one w tym samym regionie. Jako obejście tego problemu możesz mieć Aplikacje logiki i konto magazynu w różnych regionach. Więcej informacji na temat włączania dostępu Azure Logic Apps do kont magazynu związanych z zaporami znajduje się w sekcji [dostęp do kont magazynu związanych z zaporami](#storage-firewalls) w dalszej części tego tematu.

<a name="blob-storage-limits"></a>

## <a name="limits"></a>Limity

* Domyślnie akcje Blob Storage platformy Azure mogą odczytywać lub zapisywać pliki o *rozmiarze 50 MB lub mniejszym* . Do obsługi plików o rozmiarze większym niż 50 MB, ale nawet do 1024 MB, działania Blob Storage platformy Azure obsługują [fragmenty komunikatów](../logic-apps/logic-apps-handle-large-messages.md). Akcja **Pobierz zawartość obiektu BLOB** niejawnie używa podziału.

* Wyzwalacze usługi Azure Blob Storage nie obsługują rozdzielenie. Podczas żądania zawartości pliku wyzwalane są tylko pliki o rozmiarze 50 MB lub mniejszej. Aby uzyskać pliki o rozmiarze większym niż 50 MB, wykonaj następujące czynności:

  * Użyj wyzwalacza Blob Storage platformy Azure, który zwraca właściwości pliku, na przykład **gdy obiekt BLOB jest dodawany lub modyfikowany (tylko właściwości)** .

  * Obserwuj wyzwalacz z akcją **Pobierz zawartość obiektu Blob** BLOB Storage platformy Azure, która odczytuje kompletny plik i niejawnie używa podziału.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* [Kontener konta i magazynu platformy Azure](../storage/blobs/storage-quickstart-blobs-portal.md)

* Aplikacja logiki, do której potrzebny jest dostęp do konta usługi Azure Blob Storage. Aby uruchomić aplikację logiki przy użyciu wyzwalacza usługi Azure Blob Storage, musisz mieć [pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Dodaj wyzwalacz magazynu obiektów BLOB

W Azure Logic Apps każda aplikacja logiki musi rozpoczynać się od [wyzwalacza](../logic-apps/logic-apps-overview.md#logic-app-concepts), który jest uruchamiany w przypadku wystąpienia konkretnego zdarzenia lub spełnienia określonego warunku. Za każdym razem, gdy wyzwala wyzwalacz, aparat Logic Apps tworzy wystąpienie aplikacji logiki i uruchamia przepływ pracy aplikacji.

Ten przykład pokazuje, jak uruchomić przepływ pracy aplikacji logiki przy użyciu, **kiedy obiekt BLOB jest dodawany lub modyfikowany (tylko właściwości)** wyzwalacz, gdy właściwości obiektu BLOB zostaną dodane lub zaktualizowane w kontenerze magazynu.

1. W [Azure Portal](https://portal.azure.com) lub Visual Studio Utwórz pustą aplikację logiki, która otwiera projektanta aplikacji logiki. Ten przykład używa Azure Portal.

2. W polu wyszukiwania wprowadź ciąg "Azure Blob" jako filtr. Z listy Wyzwalacze wybierz wyzwalacz, który chcesz.

   W tym przykładzie używa tego wyzwalacza: **gdy obiekt BLOB jest dodawany lub modyfikowany (tylko właściwości)**

   ![Wybierz wyzwalacz usługi Azure Blob Storage](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-trigger.png)

3. Jeśli zostanie wyświetlony monit o podanie szczegółów połączenia, [Utwórz teraz połączenie usługi BLOB Storage](#create-connection). Lub, jeśli połączenie już istnieje, podaj niezbędne informacje dla wyzwalacza.

   Na potrzeby tego przykładu wybierz kontener i folder, który chcesz monitorować.

   1. W polu **kontener** wybierz ikonę folderu.

   2. Na liście folder wybierz nawias ostry ( **>** ), a następnie Przeglądaj do momentu znalezienia i wybrania żądanego folderu.

      ![Wybierz folder magazynu do użycia z wyzwalaczem](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Wybierz interwał i częstotliwość sprawdzania przez wyzwalacza zmian w folderze.

4. Gdy skończysz, na pasku narzędzi projektanta wybierz pozycję **Zapisz** .

5. Teraz Kontynuuj dodawanie co najmniej jednej akcji do aplikacji logiki w celu wykonywania zadań, które chcesz wykonać z wynikami wyzwalacza.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Dodaj akcję magazynu obiektów BLOB

W Azure Logic Apps [Akcja](../logic-apps/logic-apps-overview.md#logic-app-concepts) to krok w przepływie pracy, który następuje po wyzwalaczu lub innej akcji. W tym przykładzie aplikacja logiki rozpoczyna się od [wyzwalacza cyklu](../connectors/connectors-native-recurrence.md).

1. W [Azure Portal](https://portal.azure.com) lub Visual Studio Otwórz aplikację logiki w Projektancie aplikacji logiki. Ten przykład używa Azure Portal.

2. W Projektancie aplikacji logiki w obszarze wyzwalacza lub akcji wybierz pozycję **nowy krok** .

   ![Dodawanie nowego kroku do przepływu pracy aplikacji logiki](./media/connectors-create-api-azureblobstorage/add-new-step-logic-app-workflow.png) 

   Aby dodać akcję między istniejącymi krokami, przesuń wskaźnik myszy na strzałkę łączącą. Wybierz wyświetlony znak plus ( **+** ), a następnie wybierz pozycję **Dodaj akcję** .

3. W polu wyszukiwania wprowadź ciąg "Azure Blob" jako filtr. Z listy Akcje wybierz żądaną akcję.

   W tym przykładzie używa tej akcji: **Pobierz zawartość obiektu BLOB**

   ![Wybierz akcję Blob Storage platformy Azure](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-action.png)

4. Jeśli zostanie wyświetlony monit o podanie szczegółów połączenia, [Utwórz teraz połączenie BLOB Storage platformy Azure](#create-connection).
Lub, jeśli połączenie już istnieje, podaj niezbędne informacje dla tej akcji.

   Na potrzeby tego przykładu wybierz odpowiedni plik.

   1. W polu **obiekt BLOB** wybierz ikonę folderu.
  
      ![Wybierz folder magazynu do użycia z akcją](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Znajdź i wybierz odpowiedni plik na podstawie numeru **identyfikacyjnego** obiektu BLOB. Ten numer **identyfikacyjny** można znaleźć w metadanych obiektu BLOB, które są zwracane przez poprzednio opisany wyzwalacz magazynu obiektów BLOB.

5. Gdy skończysz, na pasku narzędzi projektanta wybierz pozycję **Zapisz** .
Aby przetestować aplikację logiki, upewnij się, że wybrany folder zawiera obiekt BLOB.

W tym przykładzie pobierana jest tylko zawartość obiektu BLOB. Aby wyświetlić zawartość, Dodaj kolejną akcję, która tworzy plik z obiektem BLOB przy użyciu innego łącznika. Na przykład Dodaj akcję OneDrive, która tworzy plik na podstawie zawartości obiektu BLOB.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>Połącz z kontem magazynu

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Po wyświetleniu monitu o utworzenie połączenia podaj następujące informacje:

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Nazwa połączenia** | Tak | <*Nazwa połączenia*> | Nazwa do utworzenia dla połączenia |
   | **Konto magazynu** | Tak | <*Magazyn — konto*> | Wybierz konto magazynu z listy. |
   ||||

   Na przykład:

   ![Tworzenie połączenia z kontem usługi Azure Blob Storage](./media/connectors-create-api-azureblobstorage/create-storage-account-connection.png) 

1. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz** .

1. Po utworzeniu połączenia Kontynuuj [Dodawanie wyzwalacza usługi BLOB Storage](#add-trigger) lub [Dodawanie akcji magazynu obiektów BLOB](#add-action).

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać więcej szczegółowych informacji technicznych dotyczących tego łącznika, takich jak wyzwalacze, akcje i limity, zgodnie z opisem w pliku Swagger łącznika, zobacz [stronę odwołania łącznika](/connectors/azureblobconnector/).

> [!NOTE]
> W przypadku aplikacji logiki w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), wersja tego łącznika z oznaczeniem ISE w zamian używa [limitów komunikatów ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) .

<a name="storage-firewalls"></a>

## <a name="access-storage-accounts-behind-firewalls"></a>Dostęp do kont magazynu za zaporą

Możesz dodać zabezpieczenia sieciowe do konta usługi Azure Storage, ograniczając dostęp z użyciem [zapory i reguł zapory](../storage/common/storage-network-security.md). Jednak ta konfiguracja powoduje utworzenie wyzwania dla platformy Azure i innych usług firmy Microsoft, które wymagają dostępu do konta magazynu. Komunikacja lokalna w centrum danych dzieli wewnętrzne adresy IP, dlatego nie można skonfigurować reguł zapory z ograniczeniami adresów IP. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](../storage/common/storage-network-security.md).

Poniżej przedstawiono różne opcje uzyskiwania dostępu do kont magazynu za zaporą z Azure Logic Apps przy użyciu łącznika Blob Storage platformy Azure lub innych rozwiązań:

* Łącznik usługi Azure Storage Blob

  * [Dostęp do kont magazynu w innych regionach](#access-other-regions)
  * [Dostęp do kont magazynu za pomocą zaufanej sieci wirtualnej](#access-trusted-virtual-network)

* Inne rozwiązania

  * [Dostęp do kont magazynu jako usługi zaufanej z tożsamościami zarządzanymi](#access-trusted-service)
  * [Dostęp do kont magazynu za pomocą usługi Azure API Management](#access-api-management)

<a name="access-other-regions"></a>

### <a name="problems-accessing-storage-accounts-in-the-same-region"></a>Problemy z uzyskiwaniem dostępu do kont magazynu w tym samym regionie

Aplikacje logiki nie mogą bezpośrednio uzyskiwać dostępu do kont magazynu za zaporami, gdy znajdują się one w tym samym regionie. Aby obejść ten element, należy umieścić Aplikacje logiki w regionie, który różni się od konta magazynu, i zapewnić dostęp do [wychodzących adresów IP dla łączników zarządzanych w Twoim regionie](../logic-apps/logic-apps-limits-and-config.md#outbound).

> [!NOTE]
> To rozwiązanie nie dotyczy łącznika usługi Azure Table Storage i łącznika usługi Azure Queue Storage. Zamiast tego, aby uzyskać dostęp do Table Storage lub Queue Storage, użyj wbudowanego wyzwalacza HTTP i akcji.

<a name="access-trusted-virtual-network"></a>

### <a name="access-storage-accounts-through-a-trusted-virtual-network"></a>Dostęp do kont magazynu za pomocą zaufanej sieci wirtualnej

Konto magazynu można umieścić w zarządzanej sieci wirtualnej platformy Azure, a następnie dodać tę sieć wirtualną do listy zaufanych sieci wirtualnych. Aby aplikacja logiki mogła uzyskać dostęp do konta magazynu za pomocą [zaufanej sieci wirtualnej](../virtual-network/virtual-networks-overview.md), należy wdrożyć tę aplikację logiki w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), które może łączyć się z zasobami w sieci wirtualnej. Następnie można dodać podsieci w tym ISE do listy zaufanych. Łączniki usługi Azure Storage, takie jak łącznik Blob Storage, mogą bezpośrednio uzyskiwać dostęp do kontenera magazynu. Ta konfiguracja jest taka sama jak w przypadku korzystania z punktów końcowych usługi z ISE.

<a name="access-trusted-service"></a>

### <a name="access-storage-accounts-as-a-trusted-service-with-managed-identities"></a>Dostęp do kont magazynu jako usługi zaufanej z tożsamościami zarządzanymi

Aby przyznać zaufanym usługom firmy Microsoft dostęp do konta magazynu za pomocą zapory, można skonfigurować wyjątek dla tego konta magazynu dla tych usług. To rozwiązanie umożliwia usługom platformy Azure, które obsługują [tożsamości zarządzane do uwierzytelniania](../active-directory/managed-identities-azure-resources/overview.md) w celu uzyskania dostępu do kont magazynu za zaporą jako zaufanych usług. W przypadku aplikacji logiki na globalnie wielodostępnej platformie Azure, aby uzyskać dostęp do tych kont magazynu, należy najpierw [włączyć obsługę tożsamości zarządzanych](../logic-apps/create-managed-service-identity.md) w aplikacji logiki. Następnie użyj akcji HTTP lub wyzwalacza w aplikacji logiki i [Ustaw ich typ uwierzytelniania, aby używał tożsamości zarządzanej aplikacji logiki](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity). W tym scenariuszu można użyć *tylko* akcji lub wyzwalacza http.

Aby skonfigurować wyjątek i obsługę tożsamości zarządzanej, wykonaj następujące ogólne czynności:

1. Na koncie magazynu w obszarze **Ustawienia** wybierz pozycję **zapory i sieci wirtualne** . W obszarze **Zezwalaj na dostęp z** , zaznacz opcję **wybrane sieci** , aby wyświetlić ustawienia pokrewne.

1. W obszarze **wyjątki** wybierz opcję **Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu** , a następnie wybierz pozycję **Zapisz** .

   ![Wybierz wyjątek, który umożliwia korzystanie z zaufanych usług firmy Microsoft](./media/connectors-create-api-azureblobstorage/allow-trusted-services-firewall.png)

1. W ustawieniach aplikacji logiki [Włącz obsługę zarządzanej tożsamości](../logic-apps/create-managed-service-identity.md).

1. W przepływie pracy aplikacji logiki Dodaj i skonfiguruj akcję lub wyzwalacz HTTP w celu uzyskania dostępu do konta magazynu lub jednostki.

   > [!IMPORTANT]
   > W przypadku wychodzących akcji HTTP lub wyzwalania wywołań do kont usługi Azure Storage upewnij się, że nagłówek żądania zawiera `x-ms-version` Właściwość i wersję interfejsu API dla operacji, która ma być uruchamiana na koncie magazynu. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie dostępu z zarządzaną tożsamością](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) i [przechowywaniem wersji dla usług Azure Storage](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests).

1. Na tej akcji [Wybierz zarządzaną tożsamość](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) , która ma być używana do uwierzytelniania.

<a name="access-api-management"></a>

### <a name="access-storage-accounts-through-azure-api-management"></a>Dostęp do kont magazynu za pomocą usługi Azure API Management

W przypadku korzystania z warstwy dedykowanej dla [API Management](../api-management/api-management-key-concepts.md)można skorzystać z interfejsu API usługi Storage, korzystając z API Management i zezwalając na te adresy IP w ramach zapory. W zasadzie należy dodać sieć wirtualną platformy Azure używaną przez API Management do ustawienia zapory dla konta magazynu. Następnie można użyć akcji API Management lub akcji HTTP do wywołania interfejsów API usługi Azure Storage. Jednak w przypadku wybrania tej opcji musisz samodzielnie obsłużyć proces uwierzytelniania. Aby uzyskać więcej informacji, zobacz [prosta architektura integracji przedsiębiorstwa](/azure/architecture/reference-architectures/enterprise-integration/basic-enterprise-integration).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)