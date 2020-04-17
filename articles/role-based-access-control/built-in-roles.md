---
title: Wbudowane role platformy Azure dla usługi Azure RBAC
description: W tym artykule opisano wbudowane role platformy Azure dla kontroli dostępu opartej na rolach platformy Azure (RBAC). Wyświetla listę akcji, notActions, DataActions i NotDataActions.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 04/15/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: 14ff86bc47ab6de55d840c4b986c99caf2d4e99c
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81482024"
---
# <a name="azure-built-in-roles"></a>Wbudowane role platformy Azure

[Kontrola dostępu oparta na rolach platformy Azure (RBAC)](overview.md) ma kilka wbudowanych ról platformy Azure, które można przypisać do użytkowników, grup, podmiotów usługi i tożsamości zarządzanych. Przypisania ról to sposób kontrolowania dostępu do zasobów platformy Azure. Jeśli wbudowane role nie spełniają określonych potrzeb organizacji, możesz utworzyć własne [role niestandardowe platformy Azure.](custom-roles.md)

W tym artykule wymieniono wbudowane role zasobów platformy Azure, które zawsze ewoluują. Aby uzyskać najnowsze role, użyj [get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) lub [az role definition list](/cli/azure/role/definition#az-role-definition-list). Jeśli szukasz ról administratora usługi Azure Active Directory (Azure AD), zobacz [Uprawnienia roli administratora w usłudze Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="all"></a>Wszystkie

Poniższa tabela zawiera krótki opis i unikatowy identyfikator każdej wbudowanej roli. Wybierz `Actions`nazwę roli, aby wyświetlić listę , `NotActions` `DataActions`, i `NotDataActions` dla każdej roli. Aby uzyskać informacje o tym, co oznaczają te akcje i jak mają zastosowanie do płaszczyzn zarządzania i danych, zobacz [Opis definicji ról dla zasobów platformy Azure.](role-definitions.md)


> [!div class="mx-tableFixed"]
> | Wbudowana rola | Opis | ID |
> | --- | --- | --- |
> | **Ogólne** |  |  |
> | [Współautor](#contributor) | Umożliwia zarządzanie wszystkim, z wyjątkiem udzielania dostępu do zasobów. | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | [Właściciel](#owner) | Umożliwia zarządzanie wszystkim, w tym dostępem do zasobów. | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | [Czytelnik](#reader) | Umożliwia wyświetlanie wszystkiego, ale nie wprowadzać żadnych zmian. | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | [Administrator dostępu użytkowników](#user-access-administrator) | Umożliwia zarządzanie dostępem użytkowników do zasobów platformy Azure. | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Wystąpienia obliczeniowe** |  |  |
> | [Klasyczny współautor maszyny wirtualnej](#classic-virtual-machine-contributor) | Umożliwia zarządzanie klasycznymi maszynami wirtualnymi, ale nie ma do nich dostępu, a nie z kontem sieci wirtualnej lub magazynu, z którymi są połączone. | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | [Logowanie administratora maszyny wirtualnej](#virtual-machine-administrator-login) | Wyświetlanie maszyn wirtualnych w portalu i logowanie się jako administrator | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | [Współautor maszyny wirtualnej](#virtual-machine-contributor) | Umożliwia zarządzanie maszynami wirtualnymi, ale nie ma do nich dostępu, a nie z kontem sieci wirtualnej lub magazynu, z którymi są połączone. | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | [Login użytkownika maszyny wirtualnej](#virtual-machine-user-login) | Wyświetl maszyny wirtualne w portalu i zaloguj się jako zwykły użytkownik. | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Networking** |  |  |
> | [Współautor punktu końcowego sieci CDN](#cdn-endpoint-contributor) | Może zarządzać punktami końcowymi sieci CDN, ale nie może udzielić dostępu innym użytkownikom. | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | [Czytnik punktów końcowych CDN](#cdn-endpoint-reader) | Można wyświetlić punkty końcowe sieci CDN, ale nie można wprowadzać zmian. | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | [Współautor profilu sieci CDN](#cdn-profile-contributor) | Może zarządzać profilami sieci CDN i ich punktami końcowymi, ale nie może udzielić dostępu innym użytkownikom. | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | [Czytnik profili CDN](#cdn-profile-reader) | Można wyświetlić profile sieci CDN i ich punktów końcowych, ale nie można wprowadzać zmian. | 8f96442b-4075-438f-813d-ad51ab4019af |
> | [Współautor klasycznej sieci](#classic-network-contributor) | Umożliwia zarządzanie klasycznymi sieciami, ale nie ma do nich dostępu. | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | [Współautor strefy DNS](#dns-zone-contributor) | Umożliwia zarządzanie strefami DNS i zestawami rekordów w usłudze Azure DNS, ale nie pozwala kontrolować, kto ma do nich dostęp. | befefa01-2a29-4197-83a8-272ff33ce314 |
> | [Współautor sieci](#network-contributor) | Umożliwia zarządzanie sieciami, ale nie ma do nich dostępu. | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | [Współautor menedżera ruchu](#traffic-manager-contributor) | Umożliwia zarządzanie profilami usługi Traffic Manager, ale nie pozwala kontrolować, kto ma do nich dostęp. | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Storage** |  |  |
> | [Współpracownik Avere](#avere-contributor) | Może tworzyć klaster vFXT avere i zarządzać nim. | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | [Avere Operator](#avere-operator) | Używany przez klaster VFXT Avere do zarządzania klastrem | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | [Współautor kopii zapasowej](#backup-contributor) | Umożliwia zarządzanie usługą tworzenia kopii zapasowych, ale nie można tworzyć magazynów i udzielać dostępu innym osobom | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | [Operator kopii zapasowej](#backup-operator) | Umożliwia zarządzanie usługami tworzenia kopii zapasowych, z wyjątkiem usuwania kopii zapasowych, tworzenia magazynu i udzielania dostępu innym osobom | 00c29273-979b-4161-815c-10b084fb9324 |
> | [Czytnik kopii zapasowych](#backup-reader) | Może wyświetlać usługi tworzenia kopii zapasowych, ale nie można wprowadzać zmian | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | [Współautor klasycznego konta magazynu](#classic-storage-account-contributor) | Umożliwia zarządzanie klasycznymi kontami magazynu, ale nie ma do nich dostępu. | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | [Rola usługi operatora klucza klasycznego magazynu](#classic-storage-account-key-operator-service-role) | Operatorzy kluczy konta klasycznego magazynu mogą wystawiać i regenerować klucze na klasycznych kontach magazynu | 985d6b00-f706-48f5-a6fe-d0ca12fb68d |
> | [Współautor pola danych](#data-box-contributor) | Umożliwia zarządzanie wszystkim w ramach usługi Data Box Service, z wyjątkiem udzielania dostępu innym osobom. | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | [Czytnik pól danych](#data-box-reader) | Umożliwia zarządzanie usługą Data Box Service z wyjątkiem tworzenia szczegółów zamówienia lub edycji i udzielania dostępu innym osobom. | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | [Deweloper data lake analytics](#data-lake-analytics-developer) | Umożliwia przesyłanie, monitorowanie i zarządzanie własnymi zadaniami, ale nie tworzenie ani usuwanie kont Usługi Data Lake Analytics. | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | [Czytnik i dostęp do danych](#reader-and-data-access) | Umożliwia wyświetlanie wszystkiego, ale nie pozwala usunąć lub utworzyć konto magazynu lub zawarte zasób. Umożliwi również dostęp do odczytu/zapisu do wszystkich danych zawartych na koncie pamięci masowej poprzez dostęp do kluczy konta magazynu. | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | [Współautor konta magazynu](#storage-account-contributor) | Zezwala na zarządzanie kontami magazynowania. Zapewnia dostęp do klucza konta, który może służyć do uzyskiwania dostępu do danych za pośrednictwem autoryzacji klucza udostępnionego. | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | [Rola usługi operatora klucza konta magazynu](#storage-account-key-operator-service-role) | Zezwala na wyświetlanie i regenerację kluczy dostępu do konta magazynu. | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | [Współautor danych obiektów blob magazynu](#storage-blob-data-contributor) | Odczytu, zapisu i usuwania kontenerów i obiektów blob usługi Azure Storage. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [Uprawnienia do wywoływania operacji danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | [Właściciel danych obiektu blob magazynu](#storage-blob-data-owner) | Zapewnia pełny dostęp do kontenerów obiektów blob usługi Azure Storage i danych, w tym przypisywanie kontroli dostępu POSIX. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [Uprawnienia do wywoływania operacji danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | [Czytnik danych obiektów blob magazynu](#storage-blob-data-reader) | Odczytywanie i wyświetlanie listy kontenerów i obiektów blob usługi Azure Storage. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [Uprawnienia do wywoływania operacji danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | [Delegat obiektów blob magazynu](#storage-blob-delegator) | Pobierz klucz delegowania użytkownika, który następnie może służyć do tworzenia podpisu dostępu udostępnionego dla kontenera lub obiektu blob, który jest podpisany przy użyciu poświadczeń usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Tworzenie sygnatury dostępu Współdzielonego delegowania użytkowników](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas). | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | [Współautor udziału w plikach danych magazynu](#storage-file-data-smb-share-contributor) | Umożliwia odczyt, zapis i usuwanie dostępu do plików/katalogów w udziałach plików platformy Azure. Ta rola nie ma wbudowanego odpowiednika na serwerach plików systemu Windows. | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | [Współtwórca z podwyższonym poziomem uprawnień do udostępniania danych magazynu danych SMB](#storage-file-data-smb-share-elevated-contributor) | Umożliwia odczytywanie, zapisywanie, usuwanie i modyfikowanie list ACL w plikach/katalogach w udziałach plików platformy Azure. Ta rola jest odpowiednikiem listy ACL udziału plików zmian na serwerach plików systemu Windows. | a7264617-510b-434b-a828-9731dc254ea7 |
> | [Czytnik udostępniania danych pliku magazynu](#storage-file-data-smb-share-reader) | Umożliwia dostęp do odczytu plików/katalogów w udziałach plików platformy Azure. Ta rola jest odpowiednikiem listy ACL udziału plików odczytu na serwerach plików systemu Windows. | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | [Współautor danych kolejki magazynu](#storage-queue-data-contributor) | Odczytu, zapisu i usuwania kolejek usługi Azure Storage i komunikatów kolejki. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [Uprawnienia do wywoływania operacji danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | [Procesor komunikatów o danych kolejki magazynu](#storage-queue-data-message-processor) | Wgląd, pobieranie i usuwanie wiadomości z kolejki usługi Azure Storage. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [Uprawnienia do wywoływania operacji danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | [Nadawca wiadomości z danymi kolejki magazynu](#storage-queue-data-message-sender) | Dodawanie wiadomości do kolejki usługi Azure Storage. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [Uprawnienia do wywoływania operacji danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | [Czytnik danych kolejki magazynu](#storage-queue-data-reader) | Odczytywanie i wyświetlanie listy kolejek usługi Azure Storage i wiadomości kolejek. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [Uprawnienia do wywoływania operacji danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Sieć Web** |  |  |
> | [Czytnik danych usługi Azure Maps (wersja zapoznawcza)](#azure-maps-data-reader-preview) | Udziela dostępu do odczytu danych związanych z mapami z konta map platformy Azure. | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | [Współautor usługi wyszukiwania](#search-service-contributor) | Umożliwia zarządzanie usługami wyszukiwania, ale nie ma do nich dostępu. | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | [Współautor planu sieci Web](#web-plan-contributor) | Umożliwia zarządzanie planami sieci Web dla witryn sieci Web, ale nie ma do nich dostępu. | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | [Współautor strony internetowej](#website-contributor) | Umożliwia zarządzanie witrynami sieci Web (nie planami internetowymi), ale nie ma do nich dostępu. | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Kontenery** |  |  |
> | [AcrDelete (AcrDelete)](#acrdelete) | acr usunąć | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | [AcrImageSigner (Znak AcrImage)](#acrimagesigner) | acr sygnatariusz obrazu | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | [AcrPull ( AcrPull )](#acrpull) | acr ciągnąć | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | [Acrpush ( Acrpush )](#acrpush) | acr push | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | [Czytnik AcrQuarantine](#acrquarantinereader) | czytnik danych kwarantanny acr | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | [AcrQuarantineWriter](#acrquarantinewriter) | acr moduł zapisujący dane kwarantanny | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | [Rola administratora klastra usługi Azure Kubernetes](#azure-kubernetes-service-cluster-admin-role) | Wyświetl akcję poświadczeń administratora klastra. | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | [Rola użytkownika klastra usług Azure Kubernetes](#azure-kubernetes-service-cluster-user-role) | Wyświetl listę akcji poświadczeń użytkownika klastra. | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | **Bazy danych** |  |  |
> | [Rola czytnika kont usługi Cosmos DB](#cosmos-db-account-reader-role) | Może odczytywać dane konta usługi Azure Cosmos DB. Zobacz [Współautor konta documentdb](#documentdb-account-contributor) do zarządzania kontami usługi Azure Cosmos DB. | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | [Operator bazy danych Cosmos](#cosmos-db-operator) | Umożliwia zarządzanie kontami usługi Azure Cosmos DB, ale nie uzyskiwać w nich dostępu do danych. Uniemożliwia dostęp do kluczy kont i ciągów połączeń. | 230815da-be43-4aae-9cb4-875f7bd000aaa |
> | [Kosmopracownik](#cosmosbackupoperator) | Można przesłać żądanie przywracania bazy danych usługi Cosmos DB lub kontenera dla konta | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | [Współautor konta DocumentDB](#documentdb-account-contributor) | Może zarządzać kontami usługi Azure Cosmos DB. Usługa Azure Cosmos DB jest wcześniej znany jako DocumentDB. | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | [Współautor pamięci podręcznej Redis](#redis-cache-contributor) | Umożliwia zarządzanie pamięciami podręcznymi Redis, ale nie ma do nich dostępu. | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | [Współautor bazy danych SQL](#sql-db-contributor) | Umożliwia zarządzanie bazami danych SQL, ale nie ma do nich dostępu. Ponadto nie można zarządzać ich zasad związanych z zabezpieczeniami lub ich nadrzędnych serwerów SQL. | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | [Współautor wystąpienia zarządzanego SQL](#sql-managed-instance-contributor) | Umożliwia zarządzanie wystąpieniami zarządzanymi sql i wymaganą konfiguracją sieci, ale nie można udzielić dostępu innym osobom. | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | [Menedżer zabezpieczeń SQL](#sql-security-manager) | Umożliwia zarządzanie zasadami zabezpieczeń serwerów i baz danych SQL, ale nie ma do nich dostępu. | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | [Współautor programu SQL Server](#sql-server-contributor) | Umożliwia zarządzanie serwerami SQL i bazami danych, ale nie ma do nich dostępu, a nie ich zasadami związanymi z zabezpieczeniami. | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Analiza** |  |  |
> | [Właściciel danych usługi Azure Event Hubs](#azure-event-hubs-data-owner) | Umożliwia pełny dostęp do zasobów usługi Azure Event Hubs. | f526a384-b230-433a-b45c-95f59c4a2dec |
> | [Odbiorca danych usługi Azure Event Hubs](#azure-event-hubs-data-receiver) | Umożliwia odbierania dostępu do zasobów usługi Azure Event Hubs. | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | [Nadawca danych usługi Azure Event Hubs](#azure-event-hubs-data-sender) | Umożliwia wysyłanie dostępu do zasobów usługi Azure Event Hubs. | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | [Współautor fabryki danych](#data-factory-contributor) | Tworzenie fabryk danych i zarządzanie nimi, a także zasoby podrzędne w nich. | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | [Purger danych](#data-purger) | Może przeczyścić dane analityczne | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | [Operator klastra HDInsight](#hdinsight-cluster-operator) | Umożliwia odczytywanie i modyfikowanie konfiguracji klastra HDInsight. | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | [Współautor usług domenowych HDInsight](#hdinsight-domain-services-contributor) | Może odczytywać, tworzyć, modyfikować i usuwać operacje związane z usługami domenowymi wymagane dla pakietu zabezpieczeń hdinsight enterprise | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | [Współautor usługi Log Analytics](#log-analytics-contributor) | Współautor usługi Log Analytics może odczytywać wszystkie dane monitorowania i edytować ustawienia monitorowania. Ustawienia monitorowania edycji obejmują dodawanie rozszerzenia maszyny Wirtualnej do maszyn wirtualnych; odczytywanie kluczy konta magazynu, aby móc skonfigurować zbieranie dzienników z usługi Azure Storage; tworzenie i konfigurowanie kont automatyzacji; dodawanie rozwiązań; i konfigurowanie diagnostyki platformy Azure na wszystkich zasobach platformy Azure. | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | [Czytelnik usługi Log Analytics](#log-analytics-reader) | Program Log Analytics Reader może wyświetlać i przeszukiwać wszystkie dane monitorowania, a także wyświetlać ustawienia monitorowania, w tym wyświetlać konfigurację diagnostyki platformy Azure na wszystkich zasobach platformy Azure. | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **Łańcuch bloków** |  |  |
> | [Dostęp do węzła członkowskiego łańcucha bloków (wersja zapoznawcza)](#blockchain-member-node-access-preview) | Umożliwia dostęp do węzłów członka łańcucha bloków | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **AI + uczenie maszynowe** |  |  |
> | [Współautor usług kognitywnych](#cognitive-services-contributor) | Umożliwia tworzenie, odczytywanie, aktualizowanie, usuwanie i zarządzanie kluczami usług Cognitive Services. | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | [Czytnik danych usług Cognitive Services (wersja zapoznawcza)](#cognitive-services-data-reader-preview) | Umożliwia odczytywanie danych usług Cognitive Services. | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | [Użytkownik usług Kognitywnych](#cognitive-services-user) | Umożliwia odczytywanie i wyświetlanie listy kluczy usług Cognitive Services. | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Rzeczywistość mieszana** |  |  |
> | [Współautor konta zakotwiczenia przestrzennego](#spatial-anchors-account-contributor) | Umożliwia zarządzanie kotwicami przestrzennymi na koncie, ale nie ich usuwanie | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | [Właściciel konta zakotwiczenia przestrzennego](#spatial-anchors-account-owner) | Umożliwia zarządzanie kotwicami przestrzennymi na koncie, w tym ich usuwanie | 70bbe301-9835-447d-afdd-19eb3167307c |
> | [Czytnik kont zakotwiczenia przestrzennego](#spatial-anchors-account-reader) | Umożliwia lokalizowanie i odczytywanie właściwości zakotwiczeń przestrzennych na koncie | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Integracja** |  |  |
> | [Współautor usługi zarządzania interfejsami API](#api-management-service-contributor) | Może zarządzać usługami i interfejsami API | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | [Rola operatora usługi zarządzania interfejsami API](#api-management-service-operator-role) | Może zarządzać usługą, ale nie interfejsami API | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | [Rola czytnika usług zarządzania interfejsami API](#api-management-service-reader-role) | Dostęp tylko do odczytu do usług i interfejsów API | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | [Właściciel danych konfiguracji aplikacji](#app-configuration-data-owner) | Umożliwia pełny dostęp do danych konfiguracji aplikacji. | 5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b |
> | [Czytnik danych konfiguracji aplikacji](#app-configuration-data-reader) | Umożliwia dostęp do odczytu danych konfiguracji aplikacji. | 516239f1-63e1-4d78-a4de-a74fb236a071 |
> | [Właściciel danych usługi Azure Service Bus](#azure-service-bus-data-owner) | Umożliwia pełny dostęp do zasobów usługi Azure Service Bus. | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | [Odbiornik danych usługi Azure Service Bus](#azure-service-bus-data-receiver) | Umożliwia odbieranie dostępu do zasobów usługi Azure Service Bus. | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | [Nadawca danych usługi Azure Service Bus](#azure-service-bus-data-sender) | Umożliwia wysyłanie dostępu do zasobów usługi Azure Service Bus. | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | [Właściciel rejestracji stosu azure](#azure-stack-registration-owner) | Umożliwia zarządzanie rejestracjami usługi Azure Stack. | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | [Współautor eventgrid eventsubscription](#eventgrid-eventsubscription-contributor) | Umożliwia zarządzanie operacjami subskrypcji zdarzeń EventGrid. | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | [EventGrid EventSubscription Reader](#eventgrid-eventsubscription-reader) | Umożliwia odczytywanie subskrypcji zdarzeń EventGrid. | 2414bbcf-6497-4faf-8c65-045460748405 |
> | [Współautor konta inteligentnych systemów](#intelligent-systems-account-contributor) | Umożliwia zarządzanie kontami inteligentnych systemów, ale nie ma do nich dostępu. | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | [Współautor aplikacji logiki](#logic-app-contributor) | Umożliwia zarządzanie aplikacjami logicznymi, ale nie zmienia ich dostępu. | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | [Operator aplikacji logiki](#logic-app-operator) | Umożliwia odczytywanie, włączanie i wyłączanie aplikacji logiki, ale nie jest ich edytowanie ani aktualizowanie. | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Tożsamość** |  |  |
> | [Współautor tożsamości zarządzanej](#managed-identity-contributor) | Tworzenie, odczytywanie, aktualizowanie i usuwanie tożsamości przypisanej przez użytkownika | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | [Operator tożsamości zarządzanej](#managed-identity-operator) | Odczyt i przypisanie tożsamości przypisanej użytkownikowi | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Zabezpieczenia** |  |  |
> | [Współautor wartownika platformy Azure](#azure-sentinel-contributor) | Współautor wartownika platformy Azure | ab8e14d6-4a74-4a29-9ba8-549422addad |
> | [Czytnik wartownika platformy Azure](#azure-sentinel-reader) | Czytnik wartownika platformy Azure | 8d289c81-5878-46d4-8554-54e1e3d8b5cbb |
> | [Obiekt odpowiadający usługi Azure Sentinel](#azure-sentinel-responder) | Obiekt odpowiadający usługi Azure Sentinel | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | [Współautor magazynu kluczy](#key-vault-contributor) | Umożliwia zarządzanie magazynami kluczy, ale nie ma do nich dostępu. | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | [Administrator zabezpieczeń](#security-admin) | Może wyświetlać zasady zabezpieczeń, wyświetlać stany zabezpieczeń, edytować zasady zabezpieczeń, wyświetlać alerty i zalecenia, odrzucać alerty i zalecenia. | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | [Współautor oceny bezpieczeństwa](#security-assessment-contributor) | Umożliwia wypychanie ocen do centrum zabezpieczeń | 612c2aa1-cb24-443b-ac28-3ab7272de6f5 |
> | [Menedżer zabezpieczeń (starsza wersja)](#security-manager-legacy) | Jest to starsza rola. Zamiast tego użyj administratora zabezpieczeń. | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | [Czytnik zabezpieczeń](#security-reader) | Może wyświetlać zalecenia i alerty, wyświetlać zasady zabezpieczeń, wyświetlać stany zabezpieczeń, ale nie może wprowadzać zmian. | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **DevOps** |  |  |
> | [Użytkownik DevTest Labs](#devtest-labs-user) | Umożliwia łączenie, uruchamianie, ponowne uruchamianie i zamykanie maszyn wirtualnych w laboratoriach devtest platformy Azure. | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | [Twórca laboratorium](#lab-creator) | Umożliwia tworzenie, zarządzanie, usuwanie zarządzanych laboratoriów w ramach kont laboratorium Azure. | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Monitor** |  |  |
> | [Współautor składnika usługi Application Insights](#application-insights-component-contributor) | Może zarządzać składnikami usługi Application Insights | ae349356-3a1b-4a5e-921d-050484c6347e |
> | [Debuger migawek usługi Application Insights](#application-insights-snapshot-debugger) | Daje użytkownikowi uprawnienia do wyświetlania i pobierania migawek debugowania zebranych za pomocą debugera migawek usługi Application Insights. Należy zauważyć, że te uprawnienia nie są uwzględniane w [roli właściciela](#owner) lub [współautora.](#contributor) | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | [Współautor monitorowania](#monitoring-contributor) | Może odczytywać wszystkie dane monitorowania i edytować ustawienia monitorowania. Zobacz też [Wprowadzenie do ról, uprawnień i zabezpieczeń w usłudze Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles). | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | [Monitorowanie metryki Wydawca](#monitoring-metrics-publisher) | Włącza publikowanie metryk w zasobach platformy Azure | 3913510d-42f4-4e42-8a64-420c390055eb |
> | [Czytnik monitoringu](#monitoring-reader) | Może odczytywać wszystkie dane z monitoringu (metryki, dzienniki itp.). Zobacz też [Wprowadzenie do ról, uprawnień i zabezpieczeń w usłudze Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles). | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | [Współautor skoroszytu](#workbook-contributor) | Można zapisywać skoroszyty udostępnione. | e8ddcd69-c73f-4f9f-9844-4100522f16ad |
> | [Czytnik skoroszytu](#workbook-reader) | Może czytać skoroszyty. | b279062a-9be3-42a0-92ae-8b3cf002ec4d |
> | **Zarządzanie + zarządzanie** |  |  |
> | [Operator zadań automatyzacji](#automation-job-operator) | Tworzenie zadań i zarządzanie nimi przy użyciu śmięty automatyzacji. | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | [Operator usługi](#automation-operator) | Operatorzy automatyzacji mogą uruchamiać, zatrzymywać, zawieszać i wznawiać zadania | d3881f73-407a-4167-8283-e981cbba0404 |
> | [Operator systemu runbook automatyzacji](#automation-runbook-operator) | Przeczytaj właściwości elementu runbook — aby móc tworzyć zadania elementu runbook. | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | [Dołączanie do połączonego komputera platformy Azure](#azure-connected-machine-onboarding) | Może dołączać połączone maszyny platformy Azure. | b64e21ea-ac4e-4cdf-9dc9-5b892992bee7 |
> | [Administrator zasobów podłączonego komputera platformy Azure](#azure-connected-machine-resource-administrator) | Może odczytywać, zapisywać, usuwać i ponownie dołączać połączone maszyny platformy Azure. | cd570a14-e51a-42ad-bac8-bafd67325302 |
> | [Czytnik rozliczeń](#billing-reader) | Umożliwia dostęp do odczytu danych rozliczeniowych | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | [Współautor planu](#blueprint-contributor) | Może zarządzać definicjami planu, ale nie przypisywać ich. | 41077137-e803-4205-871c-5a86e6a753b4 |
> | [Operator planu](#blueprint-operator) | Można przypisać istniejące opublikowane plany, ale nie można utworzyć nowe plany. Należy zauważyć, że to działa tylko wtedy, gdy przypisanie odbywa się z tożsamością zarządzaną przypisaną przez użytkownika. | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | [Współautor zarządzania kosztami](#cost-management-contributor) | Może wyświetlać koszty i zarządzać konfiguracją kosztów (np. budżety, eksport) | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | [Czytnik zarządzania kosztami](#cost-management-reader) | Może wyświetlać dane o kosztach i konfigurację (np. budżety, eksport) | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | [Rola współautora aplikacji zarządzanej](#managed-application-contributor-role) | Umożliwia tworzenie zasobów aplikacji zarządzanych. | 641177b8-a67a-45b9-a033-47bc880bb21e |
> | [Rola operatora aplikacji zarządzanej](#managed-application-operator-role) | Umożliwia odczytywanie i wykonywanie akcji w zasobach aplikacji zarządzanych | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | [Czytnik aplikacji zarządzanych](#managed-applications-reader) | Umożliwia odczytywanie zasobów w zarządzanej aplikacji i żądanie dostępu JIT. | b9331d3-8a36-4f8c-b097-4f54124fdb44 |
> | [Rola usuwania przypisania rejestracji usług zarządzanych](#managed-services-registration-assignment-delete-role) | Rola usuwania przypisania rejestracji usług zarządzanych umożliwia użytkownikom dzierżawy zarządzającej usunięcie przypisania rejestracji przypisanego do ich dzierżawy. | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | [Współautor grupy zarządzania](#management-group-contributor) | Rola współautora grupy zarządzającej | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | [Czytnik grup zarządzania](#management-group-reader) | Rola czytnika grupy zarządzania | ac63b705-f282-497d-ac71-919bf39d939d |
> | [Nowy współautor konta APM reliktów](#new-relic-apm-account-contributor) | Umożliwia zarządzanie kontami i aplikacjami zarządzania wydajnością nowych aplikacji, ale nie ma do nich dostępu. | 5d28c62d-5b37-4476-8438-e587778df237 |
> | [Moduł zapisujący dane wglądu w zasady (wersja zapoznawcza)](#policy-insights-data-writer-preview) | Umożliwia dostęp do odczytu do zasad zasobów i dostęp do zapisu do zdarzeń zasad składnika zasobów. | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | [Współautor zasad zasobów](#resource-policy-contributor) | Użytkownicy z uprawnieniami do tworzenia/modyfikowania zasad zasobów, tworzenia biletów pomocy technicznej i odczytywania zasobów/hierarchii. | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | [Współautor odzyskiwania witryny](#site-recovery-contributor) | Umożliwia zarządzanie usługą site recovery z wyjątkiem tworzenia przechowalni i przypisywania ról | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | [Operator odzyskiwania witryny](#site-recovery-operator) | Umożliwia pracę w pracy awaryjnej i powiększanie po awarii, ale nie wykonywanie innych operacji zarządzania odzyskiwaniem witryny | 494ae006-db33-4328-bf46-533a6560a3ca |
> | [Czytnik odzyskiwania witryny](#site-recovery-reader) | Umożliwia wyświetlanie stanu odzyskiwania witryny, ale nie wykonywanie innych operacji zarządzania | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | [Współautor żądania pomocy technicznej](#support-request-contributor) | Umożliwia tworzenie żądań pomocy technicznej i zarządzanie nimi | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | [Współautor tagu](#tag-contributor) | Umożliwia zarządzanie tagami w jednostkach bez zapewniania dostępu do samych encji. | 4a9ae827-6dc8-4573-8ac7-8239d42aa03f |
> | **Inne** |  |  |
> | [Współautor systemu BizTalk](#biztalk-contributor) | Umożliwia zarządzanie usługami BizTalk, ale nie ma do nich dostępu. | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | [Współautor kolekcji zadań harmonogramu](#scheduler-job-collections-contributor) | Umożliwia zarządzanie kolekcjami zadań harmonogramu, ale nie ma do nich dostępu. | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |


## <a name="general"></a>Ogólne


### <a name="contributor"></a>Współautor

Umożliwia zarządzanie wszystkim, z wyjątkiem udzielania dostępu do zasobów.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | * | Tworzenie zasobów wszystkich typów i zarządzanie nimi |
> | **NotActions** |  |
> | Microsoft.Authorization/*/Delete | Usuwanie ról, przypisań zasad, definicji zasad i definicji zestawów zasad |
> | Microsoft.Authorization/*/Write | Tworzenie ról, przypisań ról, przypisań zasad, definicji zasad i definicji zestawów zasad |
> | Microsoft.Authorization/elevateAccess/Action | Przyznaje osobie wywołującej uprawnienia administratora dostępu użytkowników w zakresie dzierżawy |
> | Microsoft.Blueprint/blueprintAssignments/write | Tworzenie lub aktualizowanie wszystkich przypisań planu |
> | Microsoft.Blueprint/blueprintAssignments/delete Microsoft.Blueprint/blueprintAssignments/delete Microsoft.Blueprint/blueprintAssignments/delete Microsoft. | Usuwanie wszystkich przypisań planu |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything except access to resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
  "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="owner"></a>Właściciel

Umożliwia zarządzanie wszystkim, w tym dostępem do zasobów.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | * | Tworzenie zasobów wszystkich typów i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything, including access to resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "name": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader"></a>Czytelnik

Umożliwia wyświetlanie wszystkiego, ale nie wprowadzać żadnych zmian.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | */odczyt | Odczytywanie zasobów wszystkich typów, z wyjątkiem wpisów tajnych. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view everything, but not make any changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "permissions": [
    {
      "actions": [
        "*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="user-access-administrator"></a>Administrator dostępu użytkowników

Umożliwia zarządzanie dostępem użytkowników do zasobów platformy Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | */odczyt | Odczytywanie zasobów wszystkich typów, z wyjątkiem wpisów tajnych. |
> | Microsoft.Authorization/* | Zarządzanie autoryzacją |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage user access to Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "User Access Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="compute"></a>Wystąpienia obliczeniowe


### <a name="classic-virtual-machine-contributor"></a>Klasyczny współautor maszyny wirtualnej

Umożliwia zarządzanie klasycznymi maszynami wirtualnymi, ale nie ma do nich dostępu, a nie z kontem sieci wirtualnej lub magazynu, z którymi są połączone.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.ClassicCompute/domainNames/* | Tworzenie klasycznych nazw domen obliczeniowych i zarządzanie nimi |
> | Microsoft.ClassicCompute/virtualMachines/* | Tworzenie maszyn wirtualnych i zarządzanie nimi |
> | Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
> | Microsoft.ClassicNetwork/reservedIps/link/akcja | Łącze zastrzeżonego adresu IP |
> | Microsoft.ClassicNetwork/reservedIps/read | Pobiera zastrzeżone identyfikatory |
> | Microsoft.ClassicNetwork/virtualNetworks/join/action | Dołącza do sieci wirtualnej. |
> | Microsoft.ClassicNetwork/virtualNetworks/read | Pobierz sieć wirtualną. |
> | Microsoft.ClassicStorage/storageKonta/dyski/odczyt | Zwraca dysk konta magazynu. |
> | Microsoft.ClassicStorage/storageKonta/obrazy/odczyt | Zwraca obraz konta magazynu. (Przestarzałe. Użyj funkcji "Microsoft.ClassicStorage/storageAccounts/vmImages") |
> | Microsoft.ClassicStorage/storageKonta/listKeys/akcja | Wyświetla listę kluczy dostępu dla kont magazynu. |
> | Microsoft.ClassicStorage/storageKonta/odczyt | Zwróć konto magazynu przy podanym koncie. |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "name": "d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/domainNames/*",
        "Microsoft.ClassicCompute/virtualMachines/*",
        "Microsoft.ClassicNetwork/networkSecurityGroups/join/action",
        "Microsoft.ClassicNetwork/reservedIps/link/action",
        "Microsoft.ClassicNetwork/reservedIps/read",
        "Microsoft.ClassicNetwork/virtualNetworks/join/action",
        "Microsoft.ClassicNetwork/virtualNetworks/read",
        "Microsoft.ClassicStorage/storageAccounts/disks/read",
        "Microsoft.ClassicStorage/storageAccounts/images/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-administrator-login"></a>Logowanie administratora maszyny wirtualnej

Wyświetlanie maszyn wirtualnych w portalu i logowanie się jako administrator

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Network/publicIPAddresses/read | Pobiera definicję adresu IP publicznych. |
> | Microsoft.Network/virtualNetworks/read | Uzyskaj definicję sieci wirtualnej |
> | Microsoft.Network/loadBalancers/read | Pobiera definicję modułu równoważenia obciążenia |
> | Microsoft.Network/networkInterfaces/read | Pobiera definicję interfejsu sieciowego.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | Microsoft.Compute/virtualMachines/login/action | Zaloguj się do maszyny wirtualnej jako zwykły użytkownik |
> | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Zaloguj się do maszyny wirtualnej za pomocą uprawnień administratora systemu Windows lub użytkownika głównego systemu Linux |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as administrator",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "name": "1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action",
        "Microsoft.Compute/virtualMachines/loginAsAdmin/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Administrator Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-contributor"></a>Współautor maszyny wirtualnej

Umożliwia zarządzanie maszynami wirtualnymi, ale nie ma do nich dostępu, a nie z kontem sieci wirtualnej lub magazynu, z którymi są połączone.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Compute/availabilitySets/* | Tworzenie zestawów dostępności obliczeń i zarządzanie nimi |
> | Microsoft.Compute/lokalizacje/* | Tworzenie lokalizacji obliczeniowych i zarządzanie nimi |
> | Microsoft.Compute/virtualMachines/* | Tworzenie maszyn wirtualnych i zarządzanie nimi |
> | Zestawy Microsoft.Compute/virtualMachineScaleSets/* | Tworzenie zestawów skalowania maszyn wirtualnych i zarządzanie nimi |
> | Microsoft.Compute/disks/write | Tworzy nowy dysk lub aktualizuje istniejący |
> | Microsoft.Compute/disks/read | Pobierz właściwości dysku |
> | Microsoft.Compute/disks/delete Microsoft.Compute/disks/delete Microsoft.Compute/disks/delete Microsoft. | Usuwa dysk |
> | Microsoft.DevTestLab/harmonogramy/* |  |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Dołącza do puli adresów wewnętrznej bazy danych bramy aplikacji. Nie czujny. |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Łączy pulę adresów wewnętrznej bazy danych modułu równoważenia obciążenia. Nie czujny. |
> | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Łączy przychodzącą pulę nat modułu równoważenia obciążenia. Nie czujny. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Łączy przychodzących reguły nat modułu równoważenia obciążenia. Nie czujny. |
> | Microsoft.Network/loadBalancers/probes/join/action | Umożliwia użycie sond modułu równoważenia obciążenia. Na przykład z tym healthprobe właściwości zestawu skalowania maszyny Wirtualnej może odwoływać się do sondy. Nie czujny. |
> | Microsoft.Network/loadBalancers/read | Pobiera definicję modułu równoważenia obciążenia |
> | Sieć Microsoft.Network/lokalizacje/* | Tworzenie lokalizacji sieciowych i zarządzanie nimi |
> | Microsoft.Network/networkInterfaces/* | Tworzenie interfejsów sieciowych i zarządzanie nimi |
> | Microsoft.Network/networkSecurityGroups/join/action | Dołącza do sieciowej grupy zabezpieczeń. Nie czujny. |
> | Microsoft.Network/networkSecurityGroups/read | Pobiera definicję sieciowej grupy zabezpieczeń |
> | Microsoft.Network/publicIPAddresses/join/action | Dołącza do publicznego adresu IP. Nie czujny. |
> | Microsoft.Network/publicIPAddresses/read | Pobiera definicję adresu IP publicznych. |
> | Microsoft.Network/virtualNetworks/read | Uzyskaj definicję sieci wirtualnej |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Dołącza do sieci wirtualnej. Nie czujny. |
> | Microsoft.RecoverySługs/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Tworzenie intencji ochrony kopii zapasowej |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Zwraca szczegóły obiektu chronionego elementu |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Tworzenie kopii zapasowej chronionego elementu |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Zwraca wszystkie zasady ochrony |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Tworzy zasady ochrony |
> | Microsoft.RecoveryServices/Vaults/read | Operacja Pobierz przechowalnię pobiera obiekt reprezentujący zasób platformy Azure typu "vault" |
> | Microsoft.RecoveryServices/Vaults/usages/read | Zwraca szczegóły użycia magazynu usług odzyskiwania. |
> | Microsoft.RecoveryServices/Przechowalnia/zapis | Utwórz operację Vault tworzy zasób platformy Azure typu "vault" |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.SqlVirtualMachine/* |  |
> | Magazyn/magazyn microsoftKonserwuje/listKeys/akcja | Zwraca klucze dostępu dla określonego konta magazynu. |
> | Magazyn/magazyn microsoftKonsowania/odczytu | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/locations/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/virtualMachineScaleSets/*",
        "Microsoft.Compute/disks/write",
        "Microsoft.Compute/disks/read",
        "Microsoft.Compute/disks/delete",
        "Microsoft.DevTestLab/schedules/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/loadBalancers/probes/join/action",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/locations/*",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Network/networkSecurityGroups/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/write",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.SqlVirtualMachine/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-user-login"></a>Login użytkownika maszyny wirtualnej

Wyświetl maszyny wirtualne w portalu i zaloguj się jako zwykły użytkownik.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Network/publicIPAddresses/read | Pobiera definicję adresu IP publicznych. |
> | Microsoft.Network/virtualNetworks/read | Uzyskaj definicję sieci wirtualnej |
> | Microsoft.Network/loadBalancers/read | Pobiera definicję modułu równoważenia obciążenia |
> | Microsoft.Network/networkInterfaces/read | Pobiera definicję interfejsu sieciowego.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | Microsoft.Compute/virtualMachines/login/action | Zaloguj się do maszyny wirtualnej jako zwykły użytkownik |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as a regular user.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb879df8-f326-4884-b1cf-06f3ad86be52",
  "name": "fb879df8-f326-4884-b1cf-06f3ad86be52",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine User Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="networking"></a>Networking


### <a name="cdn-endpoint-contributor"></a>Współautor punktu końcowego sieci CDN

Może zarządzać punktami końcowymi sieci CDN, ale nie może udzielić dostępu innym użytkownikom.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Cdn/edgenodes/odczyt |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profile/punkty końcowe/* |  |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "name": "426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-endpoint-reader"></a>Czytnik punktów końcowych CDN

Można wyświetlić punkty końcowe sieci CDN, ale nie można wprowadzać zmian.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Cdn/edgenodes/odczyt |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profile/endpoints/*/read |  |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "name": "871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-contributor"></a>Współautor profilu sieci CDN

Może zarządzać profilami sieci CDN i ich punktami końcowymi, ale nie może udzielić dostępu innym użytkownikom.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Cdn/edgenodes/odczyt |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/* |  |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN profiles and their endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "name": "ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-reader"></a>Czytnik profili CDN

Można wyświetlić profile sieci CDN i ich punktów końcowych, ale nie można wprowadzać zmian.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Cdn/edgenodes/odczyt |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/*/read |  |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN profiles and their endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8f96442b-4075-438f-813d-ad51ab4019af",
  "name": "8f96442b-4075-438f-813d-ad51ab4019af",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-network-contributor"></a>Współautor klasycznej sieci

Umożliwia zarządzanie klasycznymi sieciami, ale nie ma do nich dostępu.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.ClassicNetwork/* | Tworzenie sieci klasycznych i zarządzanie nimi |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "name": "b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicNetwork/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="dns-zone-contributor"></a>Współautor strefy DNS

Umożliwia zarządzanie strefami DNS i zestawami rekordów w usłudze Azure DNS, ale nie pozwala kontrolować, kto ma do nich dostęp.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.Network/dnsZones/* | Tworzenie stref i rekordów DNS oraz zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DNS zones and record sets in Azure DNS, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/befefa01-2a29-4197-83a8-272ff33ce314",
  "name": "befefa01-2a29-4197-83a8-272ff33ce314",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/dnsZones/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="network-contributor"></a>Współautor sieci

Umożliwia zarządzanie sieciami, ale nie ma do nich dostępu.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Sieć Microsoft.Network/* | Tworzenie sieci i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4d97b98b-1d4f-4787-a291-c67834d212e7",
  "name": "4d97b98b-1d4f-4787-a291-c67834d212e7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="traffic-manager-contributor"></a>Współautor menedżera ruchu

Umożliwia zarządzanie profilami usługi Traffic Manager, ale nie pozwala kontrolować, kto ma do nich dostęp.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.Network/trafficManagerProfiles/* |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Traffic Manager profiles, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "name": "a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/trafficManagerProfiles/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Traffic Manager Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="storage"></a>Magazyn


### <a name="avere-contributor"></a>Współpracownik Avere

Może tworzyć klaster vFXT avere i zarządzać nim.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Compute/*/odczyt |  |
> | Microsoft.Compute/availabilitySets/* |  |
> | Microsoft.Compute/virtualMachines/* |  |
> | Microsoft.Compute/dyski/* |  |
> | Sieć Microsoft.Network/*/odczyt |  |
> | Microsoft.Network/networkInterfaces/* |  |
> | Microsoft.Network/virtualNetworks/read | Uzyskaj definicję sieci wirtualnej |
> | Microsoft.Network/virtualNetworks/subnets/read | Pobiera definicję podsieci sieci wirtualnej |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Dołącza do sieci wirtualnej. Nie czujny. |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Łączy zasób, taki jak konto magazynu lub baza danych SQL, do podsieci. Nie czujny. |
> | Microsoft.Network/networkSecurityGroups/join/action | Dołącza do sieciowej grupy zabezpieczeń. Nie czujny. |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Magazyn Microsoft.Storage/*/odczyt |  |
> | Microsoft.Storage/storageKondyje/* | Tworzenie kont magazynu i zarządzanie nimi |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/zasoby/odczyt | Pobiera zasoby dla grupy zasobów. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | Magazynowanie/magazynowanie MicrosoftKonserwacje/blobSługi/kontenery/obiekty blob/delete | Zwraca wynik usunięcia obiektu blob |
> | Magazynowanie/magazynowanie MicrosoftKonserwacje/blobSługi/kontenery/obiekty blob/odczyt | Zwraca obiekt blob lub listę obiektów blob |
> | Magazynowanie/magazynowanie MicrosoftKonserwacje/blobSługi/kontenery/obiekty blob/zapis | Zwraca wynik zapisu obiektu blob |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can create and manage an Avere vFXT cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "name": "4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/disks/*",
        "Microsoft.Network/*/read",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/*/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="avere-operator"></a>Avere Operator

Używany przez klaster VFXT Avere do zarządzania klastrem

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Compute/virtualMachines/read | Pobierz właściwości maszyny wirtualnej |
> | Microsoft.Network/networkInterfaces/read | Pobiera definicję interfejsu sieciowego.  |
> | Microsoft.Network/networkInterfaces/write | Tworzy interfejs sieciowy lub aktualizuje istniejący interfejs sieciowy.  |
> | Microsoft.Network/virtualNetworks/read | Uzyskaj definicję sieci wirtualnej |
> | Microsoft.Network/virtualNetworks/subnets/read | Pobiera definicję podsieci sieci wirtualnej |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Dołącza do sieci wirtualnej. Nie czujny. |
> | Microsoft.Network/networkSecurityGroups/join/action | Dołącza do sieciowej grupy zabezpieczeń. Nie czujny. |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Magazyn/magazynowanie firmy MicrosoftKonta/blobSługi/kontenery/usuwanie | Zwraca wynik usunięcia kontenera |
> | Magazyn/magazynowanie firmy MicrosoftKonta/blobSługi/kontenery/odczyt | Zwraca listę kontenerów |
> | Magazyn/magazynowanie firmy MicrosoftKonta/kont/obiektów blobUsługi/kontenery/zapis | Zwraca wynik kontenera obiektu blob put |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | Magazynowanie/magazynowanie MicrosoftKonserwacje/blobSługi/kontenery/obiekty blob/delete | Zwraca wynik usunięcia obiektu blob |
> | Magazynowanie/magazynowanie MicrosoftKonserwacje/blobSługi/kontenery/obiekty blob/odczyt | Zwraca obiekt blob lub listę obiektów blob |
> | Magazynowanie/magazynowanie MicrosoftKonserwacje/blobSługi/kontenery/obiekty blob/zapis | Zwraca wynik zapisu obiektu blob |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Used by the Avere vFXT cluster to manage the cluster",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "name": "c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-contributor"></a>Współautor kopii zapasowej

Umożliwia zarządzanie usługą tworzenia kopii zapasowych, ale nie można tworzyć magazynów i udzielać dostępu innym osobom

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Network/virtualNetworks/read | Uzyskaj definicję sieci wirtualnej |
> | Microsoft.RecoverySługs/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | Zarządzanie wynikami operacji podczas zarządzania kopiami zapasowymi |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Tworzenie kontenerów kopii zapasowych i zarządzanie nimi wewnątrz sieci szkieletowej magazynów usług odzyskiwania |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Odświeża listę kontenerów |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Tworzenie zadań tworzenia kopii zapasowych i zarządzanie nimi |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Zadania eksportu |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Tworzenie wyników operacji zarządzania kopiami zapasowymi i zarządzanie nimi |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/* | Tworzenie zasad tworzenia kopii zapasowych i zarządzanie nimi |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Tworzenie elementów, których kopię zapasową można tworzyć i zarządzać nimi |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Tworzenie elementów kopii zapasowej i zarządzanie nimi |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Tworzenie kontenerów przechowujących elementy kopii zapasowej i zarządzanie nimi |
> | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Zwraca podsumowania dla elementów chronionych i serwerów chronionych dla usług odzyskiwania . |
> | Microsoft.RecoveryServices/Vaults/certificates/* | Tworzenie certyfikatów związanych z tworzeniem kopii zapasowych w magazynie usług odzyskiwania i zarządzanie nimi |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Tworzenie informacji rozszerzonych związanych z magazynem i zarządzanie nimi |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Pobiera alerty dla magazynu usług odzyskiwania. |
> | Microsoft.RecoveryServices/Vaults/monitoringKonfiguracje/* |  |
> | Microsoft.RecoveryServices/Vaults/read | Operacja Pobierz przechowalnię pobiera obiekt reprezentujący zasób platformy Azure typu "vault" |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Tworzenie zarejestrowanych tożsamości i zarządzanie nimi |
> | Microsoft.RecoveryServices/Vaults/usages/* | Tworzenie magazynu usług odzyskiwania i zarządzanie nim |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Magazyn/magazyn microsoftKonsowania/odczytu | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Sprawdzanie poprawności działania na chronionym elemencie |
> | Microsoft.RecoveryServices/Przechowalnia/zapis | Utwórz operację Vault tworzy zasób platformy Azure typu "vault" |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Zwraca stan operacji kopii zapasowej dla magazynu usług odzyskiwania. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Zwraca wszystkie serwery zarządzania kopiami zapasowymi zarejestrowane w przechowalni. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Zdobądź wszystkie kontenery chronione |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Sprawdź stan kopii zapasowej w przechowalniach usług odzyskiwania |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action Microsoft.RecoveryServices/locations/backupValidateFeatures/action Microsoft.Recovery | Sprawdzanie poprawności funkcji |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Rozwiązuje alert. |
> | Microsoft.RecoverySługs/operations/read | Operacja zwraca listę operacji dla dostawcy zasobów |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Pobiera stan operacji dla danej operacji |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Wyświetl listę wszystkich intencji ochrony kopii zapasowej |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup service,but can't create vaults and give access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e467623-bb1f-42f4-a55d-6e525e11384b",
  "name": "5e467623-bb1f-42f4-a55d-6e525e11384b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupSecurityPIN/*",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/*",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/Vaults/usages/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-operator"></a>Operator kopii zapasowej

Umożliwia zarządzanie usługami tworzenia kopii zapasowych, z wyjątkiem usuwania kopii zapasowych, tworzenia magazynu i udzielania dostępu innym osobom

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Network/virtualNetworks/read | Uzyskaj definicję sieci wirtualnej |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Zwraca stan operacji |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Pobiera wynik operacji wykonywanej w kontenerze ochrony. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Wykonuje kopię zapasową dla chronionego elementu. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Pobiera wynik operacji wykonywanej na chronionych elementów. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Zwraca stan operacji wykonywanej na chronionych elementach. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Zwraca szczegóły obiektu chronionego elementu |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Aprowizuj natychmiastowy zwrot elementów dla chronionego przedmiotu |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Zdobądź punkty odzyskiwania dla przedmiotów chronionych. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Przywróć punkty odzyskiwania dla elementów chronionych. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Odwołaj natychmiastowe odzyskiwanie przedmiotów dla chronionego przedmiotu |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Tworzenie kopii zapasowej chronionego elementu |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Zwraca wszystkie zarejestrowane kontenery |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Odświeża listę kontenerów |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Tworzenie zadań tworzenia kopii zapasowych i zarządzanie nimi |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Zadania eksportu |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Tworzenie wyników operacji zarządzania kopiami zapasowymi i zarządzanie nimi |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Uzyskaj wyniki operacji zasad. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Zwraca wszystkie zasady ochrony |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Tworzenie elementów, których kopię zapasową można tworzyć i zarządzać nimi |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Zwraca listę wszystkich elementów chronionych. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Zwraca wszystkie kontenery należące do subskrypcji |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Zwraca podsumowania dla elementów chronionych i serwerów chronionych dla usług odzyskiwania . |
> | Microsoft.RecoveryServices/Vaults/certificates/write | Operacja Aktualizuj certyfikat zasobu aktualizuje certyfikat poświadczeń zasobu/magazynu. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operacja Pobierz informacje rozszerzone pobiera rozszerzone informacje obiektu reprezentujące zasób platformy Azure typu ?vault? |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | Operacja Pobierz informacje rozszerzone pobiera rozszerzone informacje obiektu reprezentujące zasób platformy Azure typu ?vault? |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Pobiera alerty dla magazynu usług odzyskiwania. |
> | Microsoft.RecoveryServices/Vaults/monitoringKonfiguracje/* |  |
> | Microsoft.RecoveryServices/Vaults/read | Operacja Pobierz przechowalnię pobiera obiekt reprezentujący zasób platformy Azure typu "vault" |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Operacja Pobierz wyniki operacji może być używana, aby uzyskać stan operacji i wynik dla operacji przesłanej asynchronicznie |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Operacja Pobierz kontenery może służyć uzyskać kontenery zarejestrowane dla zasobu. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Operacja Zarejestruj kontener usługi może służyć do rejestrowania kontenera w usłudze odzyskiwania. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Zwraca szczegóły użycia magazynu usług odzyskiwania. |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Magazyn/magazyn microsoftKonsowania/odczytu | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Sprawdzanie poprawności działania na chronionym elemencie |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Zwraca stan operacji kopii zapasowej dla magazynu usług odzyskiwania. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Uzyskaj stan operacji zasad. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Tworzy zarejestrowany kontener |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Czy zapytanie dotyczące obciążeń w kontenerze |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Zwraca wszystkie serwery zarządzania kopiami zapasowymi zarejestrowane w przechowalni. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Tworzenie intencji ochrony kopii zapasowej |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Uzyskaj zamiar ochrony kopii zapasowej |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Zdobądź wszystkie kontenery chronione |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Pobierz wszystkie elementy w kontenerze |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Sprawdź stan kopii zapasowej w przechowalniach usług odzyskiwania |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action Microsoft.RecoveryServices/locations/backupValidateFeatures/action Microsoft.Recovery | Sprawdzanie poprawności funkcji |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Rozwiązuje alert. |
> | Microsoft.RecoverySługs/operations/read | Operacja zwraca listę operacji dla dostawcy zasobów |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Pobiera stan operacji dla danej operacji |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Wyświetl listę wszystkich intencji ochrony kopii zapasowej |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup services, except removal of backup, vault creation and giving access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00c29273-979b-4161-815c-10b084fb9324",
  "name": "00c29273-979b-4161-815c-10b084fb9324",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/write",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/write",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-reader"></a>Czytnik kopii zapasowych

Może wyświetlać usługi tworzenia kopii zapasowych, ale nie można wprowadzać zmian

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp jest operacją wewnętrzną używaną przez usługę |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Zwraca stan operacji |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Pobiera wynik operacji wykonywanej w kontenerze ochrony. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Pobiera wynik operacji wykonywanej na chronionych elementów. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Zwraca stan operacji wykonywanej na chronionych elementach. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Zwraca szczegóły obiektu chronionego elementu |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Zdobądź punkty odzyskiwania dla przedmiotów chronionych. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Zwraca wszystkie zarejestrowane kontenery |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Zwraca wynik operacji zlecenia. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | Zwraca wszystkie obiekty zadania |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Zadania eksportu |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Zwraca wynik operacji kopii zapasowej dla magazynu usług odzyskiwania. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Uzyskaj wyniki operacji zasad. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Zwraca wszystkie zasady ochrony |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Zwraca listę wszystkich elementów chronionych. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Zwraca wszystkie kontenery należące do subskrypcji |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Zwraca podsumowania dla elementów chronionych i serwerów chronionych dla usług odzyskiwania . |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operacja Pobierz informacje rozszerzone pobiera rozszerzone informacje obiektu reprezentujące zasób platformy Azure typu ?vault? |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Pobiera alerty dla magazynu usług odzyskiwania. |
> | Microsoft.RecoveryServices/Vaults/read | Operacja Pobierz przechowalnię pobiera obiekt reprezentujący zasób platformy Azure typu "vault" |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Operacja Pobierz wyniki operacji może być używana, aby uzyskać stan operacji i wynik dla operacji przesłanej asynchronicznie |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Operacja Pobierz kontenery może służyć uzyskać kontenery zarejestrowane dla zasobu. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Zwraca konfigurację magazynu dla magazynu usług odzyskiwania. |
> | Microsoft.RecoveryServices/Vaults/backupconfig/read | Zwraca konfigurację magazynu usług odzyskiwania. |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Zwraca stan operacji kopii zapasowej dla magazynu usług odzyskiwania. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Uzyskaj stan operacji zasad. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Zwraca wszystkie serwery zarządzania kopiami zapasowymi zarejestrowane w przechowalni. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Uzyskaj zamiar ochrony kopii zapasowej |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Pobierz wszystkie elementy w kontenerze |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Sprawdź stan kopii zapasowej w przechowalniach usług odzyskiwania |
> | Microsoft.RecoveryServices/Vaults/monitoringKonfiguracje/* |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Rozwiązuje alert. |
> | Microsoft.RecoverySługs/operations/read | Operacja zwraca listę operacji dla dostawcy zasobów |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Pobiera stan operacji dla danej operacji |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Wyświetl listę wszystkich intencji ochrony kopii zapasowej |
> | Microsoft.RecoveryServices/Vaults/usages/read | Zwraca szczegóły użycia magazynu usług odzyskiwania. |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action Microsoft.RecoveryServices/locations/backupValidateFeatures/action Microsoft.Recovery | Sprawdzanie poprawności funkcji |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view backup services, but can't make changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "name": "a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/read",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-contributor"></a>Współautor klasycznego konta magazynu

Umożliwia zarządzanie klasycznymi kontami magazynu, ale nie ma do nich dostępu.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.ClassicStorage/storageKonta/* | Tworzenie kont magazynu i zarządzanie nimi |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic storage accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "name": "86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-key-operator-service-role"></a>Rola usługi operatora klucza klasycznego magazynu

Operatorzy kluczy konta klasycznego magazynu mogą wystawiać i regenerować klucze na klasycznych kontach magazynu

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.ClassicStorage/storageKonta/klucze listy/akcja | Wyświetla listę kluczy dostępu dla kont magazynu. |
> | Microsoft.ClassicStorage/storageKonaje/regenerować klucz/akcję | Ponownie generuje istniejące klucze dostępu dla konta magazynu. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Classic Storage Account Key Operators are allowed to list and regenerate keys on Classic Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "name": "985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ClassicStorage/storageAccounts/listkeys/action",
        "Microsoft.ClassicStorage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-contributor"></a>Współautor pola danych

Umożliwia zarządzanie wszystkim w ramach usługi Data Box Service, z wyjątkiem udzielania dostępu innym osobom.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | Microsoft.Databox/* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything under Data Box Service except giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/add466c9-e687-43fc-8d98-dfcf8d720be5",
  "name": "add466c9-e687-43fc-8d98-dfcf8d720be5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Databox/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-reader"></a>Czytnik pól danych

Umożliwia zarządzanie usługą Data Box Service z wyjątkiem tworzenia szczegółów zamówienia lub edycji i udzielania dostępu innym osobom.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Databox/*/odczyt |  |
> | Microsoft.Databox/zadania/listsecrets/akcja |  |
> | Microsoft.Databox/jobs/listcredentials/action | Wyświetla listę niezaszyfrowanych poświadczeń związanych z zamówieniem. |
> | Microsoft.Databox/locations/availableSkus/action | Ta metoda zwraca listę dostępnych skus. |
> | Microsoft.Databox/locations/validateInputs/action | Ta metoda wykonuje wszystkie typy sprawdzania poprawności. |
> | Microsoft.Databox/locations/regionConfiguration/action | Ta metoda zwraca konfiguracje dla regionu. |
> | Microsoft.Databox/locations/validateAddress/action | Sprawdza poprawność adresu wysyłki i podaje alternatywne adresy, jeśli takie istnieją. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Data Box Service except creating order or editing order details and giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "name": "028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Databox/*/read",
        "Microsoft.Databox/jobs/listsecrets/action",
        "Microsoft.Databox/jobs/listcredentials/action",
        "Microsoft.Databox/locations/availableSkus/action",
        "Microsoft.Databox/locations/validateInputs/action",
        "Microsoft.Databox/locations/regionConfiguration/action",
        "Microsoft.Databox/locations/validateAddress/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-lake-analytics-developer"></a>Deweloper data lake analytics

Umożliwia przesyłanie, monitorowanie i zarządzanie własnymi zadaniami, ale nie tworzenie ani usuwanie kont Usługi Data Lake Analytics.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.BigAnalytics/accounts/* |  |
> | Microsoft.DataLakeAnalytics/accounts/* |  |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete Microsoft.BigAnalytics/accounts/Delete Microsoft.BigAnalytics/accounts/Delete Microsoft. |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | Microsoft.DataLakeAnalytics/accounts/Delete | Usuń konto DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Udziel uprawnień do anulowania zadań przesłanych przez innych użytkowników. |
> | Microsoft.DataLakeAnalytics/accounts/Write | Utwórz lub zaktualizuj konto DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Utwórz lub zaktualizuj połączone konto DataLakeStore konta DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Odłącz konto DataLakeStore od konta DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | Utwórz lub zaktualizuj połączone konto magazynu konta DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/storageKonta/Usuń | Odłącz konto magazynu z konta DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | Tworzenie lub aktualizowanie reguły zapory. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | Usuwanie reguły zapory. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | Tworzenie lub aktualizowanie zasad obliczeniowych. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | Usuwanie zasad obliczeniowych. |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you submit, monitor, and manage your own jobs but not create or delete Data Lake Analytics accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/47b7735b-770e-4598-a7da-8b91488b4c88",
  "name": "47b7735b-770e-4598-a7da-8b91488b4c88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BigAnalytics/accounts/*",
        "Microsoft.DataLakeAnalytics/accounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.BigAnalytics/accounts/Delete",
        "Microsoft.BigAnalytics/accounts/TakeOwnership/action",
        "Microsoft.BigAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action",
        "Microsoft.DataLakeAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Write",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Write",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Lake Analytics Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader-and-data-access"></a>Czytnik i dostęp do danych

Umożliwia wyświetlanie wszystkiego, ale nie pozwala usunąć lub utworzyć konto magazynu lub zawarte zasób. Umożliwi również dostęp do odczytu/zapisu do wszystkich danych zawartych na koncie pamięci masowej poprzez dostęp do kluczy konta magazynu.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Magazyn/magazyn microsoftKonserwuje/listKeys/akcja | Zwraca klucze dostępu dla określonego konta magazynu. |
> | Magazyn/magazyn microsoftKonta/Konto listyKonsyce/akcja | Zwraca token Sygnatury dostępu Współdzielonego dla określonego konta magazynu. |
> | Magazyn/magazyn microsoftKonsowania/odczytu | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view everything but will not let you delete or create a storage account or contained resource. It will also allow read/write access to all data contained in a storage account via access to storage account keys.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c12c1c16-33a1-487b-954d-41c89c60f349",
  "name": "c12c1c16-33a1-487b-954d-41c89c60f349",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/ListAccountSas/action",
        "Microsoft.Storage/storageAccounts/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader and Data Access",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-contributor"></a>Współautor konta magazynu

Zezwala na zarządzanie kontami magazynowania. Zapewnia dostęp do klucza konta, który może służyć do uzyskiwania dostępu do danych za pośrednictwem autoryzacji klucza udostępnionego.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.Insights/diagnosticSettings/* | Tworzy, aktualizuje lub odczytuje ustawienie diagnostyczne dla analysis server |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Łączy zasób, taki jak konto magazynu lub baza danych SQL, do podsieci. Nie czujny. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Storage/storageKondyje/* | Tworzenie kont magazynu i zarządzanie nimi |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage storage accounts, including accessing storage account keys which provide full access to storage account data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "name": "17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-key-operator-service-role"></a>Rola usługi operatora klucza konta magazynu

Zezwala na wyświetlanie i regenerację kluczy dostępu do konta magazynu.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Magazyn/magazyn microsoftKonserwuje/klucze listy/akcja | Zwraca klucze dostępu dla określonego konta magazynu. |
> | Magazyn/magazynowanie MicrosoftKonta/regeneruj klucz/akcję | Ponownie generuje klucze dostępu dla określonego konta magazynu. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Storage Account Key Operators are allowed to list and regenerate keys on Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/81a9662b-bebf-436f-a333-f67b29880f12",
  "name": "81a9662b-bebf-436f-a333-f67b29880f12",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-contributor"></a>Współautor danych obiektów blob magazynu

Odczytu, zapisu i usuwania kontenerów i obiektów blob usługi Azure Storage. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [Uprawnienia do wywoływania operacji danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Magazyn/magazynowanie firmy MicrosoftKonta/blobSługi/kontenery/usuwanie | Usuwanie kontenera. |
> | Magazyn/magazynowanie firmy MicrosoftKonta/blobSługi/kontenery/odczyt | Zwraca kontener lub listę kontenerów. |
> | Magazyn/magazynowanie firmy MicrosoftKonta/kont/obiektów blobUsługi/kontenery/zapis | Modyfikowanie metadanych lub właściwości kontenera. |
> | Magazyn/magazynowanie firmy MicrosoftKonserwacje/blobUsługi/generowanieUserDelegationKey/action | Zwraca klucz delegowania użytkowników dla usługi obiektów Blob. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | Magazynowanie/magazynowanie MicrosoftKonserwacje/blobSługi/kontenery/obiekty blob/delete | Usuwanie obiektu blob. |
> | Magazynowanie/magazynowanie MicrosoftKonserwacje/blobSługi/kontenery/obiekty blob/odczyt | Zwraca obiekt blob lub listę obiektów blob. |
> | Magazynowanie/magazynowanie Kont/blobUsługi/kontenery/obiekty blobs/move/action | Przenosi obiekt blob z jednej ścieżki do drugiej |
> | Magazynowanie/magazynowanie MicrosoftKonserwacje/blobSługi/kontenery/obiekty blob/zapis | Zapis do obiektu blob. |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write and delete access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "name": "ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-owner"></a>Właściciel danych obiektu blob magazynu

Zapewnia pełny dostęp do kontenerów obiektów blob usługi Azure Storage i danych, w tym przypisywanie kontroli dostępu POSIX. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [Uprawnienia do wywoływania operacji danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Magazyn/magazynowanie firmy MicrosoftKonta/blobSługi/kontenery/* | Pełne uprawnienia do kontenerów. |
> | Magazyn/magazynowanie firmy MicrosoftKonserwacje/blobUsługi/generowanieUserDelegationKey/action | Zwraca klucz delegowania użytkowników dla usługi obiektów Blob. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | Magazyn/magazynowanie firmy MicrosoftKonta/kont/obiektów blobUsługi/kontenery/obiekty blob/* | Pełne uprawnienia do obiektów blob. |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "name": "b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/*",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-reader"></a>Czytnik danych obiektów blob magazynu

Odczytywanie i wyświetlanie listy kontenerów i obiektów blob usługi Azure Storage. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [Uprawnienia do wywoływania operacji danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Magazyn/magazynowanie firmy MicrosoftKonta/blobSługi/kontenery/odczyt | Zwraca kontener lub listę kontenerów. |
> | Magazyn/magazynowanie firmy MicrosoftKonserwacje/blobUsługi/generowanieUserDelegationKey/action | Zwraca klucz delegowania użytkowników dla usługi obiektów Blob. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | Magazynowanie/magazynowanie MicrosoftKonserwacje/blobSługi/kontenery/obiekty blob/odczyt | Zwraca obiekt blob lub listę obiektów blob. |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-delegator"></a>Delegat obiektów blob magazynu

Pobierz klucz delegowania użytkownika, który następnie może służyć do tworzenia podpisu dostępu udostępnionego dla kontenera lub obiektu blob, który jest podpisany przy użyciu poświadczeń usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Tworzenie sygnatury dostępu Współdzielonego delegowania użytkowników](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Magazyn/magazynowanie firmy MicrosoftKonserwacje/blobUsługi/generowanieUserDelegationKey/action | Zwraca klucz delegowania użytkowników dla usługi obiektów Blob. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for generation of a user delegation key which can be used to sign SAS tokens",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "name": "db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Delegator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-contributor"></a>Współautor udziału w plikach danych magazynu

Umożliwia odczyt, zapis i usuwanie dostępu do plików/katalogów w udziałach plików platformy Azure. Ta rola nie ma wbudowanego odpowiednika na serwerach plików systemu Windows.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | Magazyn Microsoft.Storage/storageKonta/fileServices/fileshares/files/read | Zwraca plik/folder lub listę plików/folderów. |
> | Magazyn Microsoft.Storage/storageKonta/fileServices/fileshares/files/write | Zwraca wynik zapisu pliku lub utworzenia folderu. |
> | Magazyn Microsoft.Storage/storageKonta/fileServices/fileshares/files/delete | Zwraca wynik usunięcia pliku/folderu. |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "name": "0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-elevated-contributor"></a>Współtwórca z podwyższonym poziomem uprawnień do udostępniania danych magazynu danych SMB

Umożliwia odczytywanie, zapisywanie, usuwanie i modyfikowanie list ACL w plikach/katalogach w udziałach plików platformy Azure. Ta rola jest odpowiednikiem listy ACL udziału plików zmian na serwerach plików systemu Windows.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | Magazyn Microsoft.Storage/storageKonta/fileServices/fileshares/files/read | Zwraca plik/folder lub listę plików/folderów. |
> | Magazyn Microsoft.Storage/storageKonta/fileServices/fileshares/files/write | Zwraca wynik zapisu pliku lub utworzenia folderu. |
> | Magazyn Microsoft.Storage/storageKonta/fileServices/fileshares/files/delete | Zwraca wynik usunięcia pliku/folderu. |
> | Magazyn Microsoft.Storage/storageKonta/fileServices/fileshares/files/modifypermissions/action | Zwraca wynik modyfikowania uprawnień do pliku/folderu. |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, delete and modify NTFS permission access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7264617-510b-434b-a828-9731dc254ea7",
  "name": "a7264617-510b-434b-a828-9731dc254ea7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Elevated Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-reader"></a>Czytnik udostępniania danych pliku magazynu

Umożliwia dostęp do odczytu plików/katalogów w udziałach plików platformy Azure. Ta rola jest odpowiednikiem listy ACL udziału plików odczytu na serwerach plików systemu Windows.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | Magazyn Microsoft.Storage/storageKonta/fileServices/fileshares/files/read | Zwraca plik/folder lub listę plików/folderów. |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure File Share over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/aba4ae5f-2193-4029-9191-0cb91df5e314",
  "name": "aba4ae5f-2193-4029-9191-0cb91df5e314",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-contributor"></a>Współautor danych kolejki magazynu

Odczytu, zapisu i usuwania kolejek usługi Azure Storage i komunikatów kolejki. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [Uprawnienia do wywoływania operacji danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Magazyn/magazynowanie Firmy MicrosoftKonserwowanie/kolejkaSługi/kolejki/usuwanie | Usuwanie kolejki. |
> | Magazyn/magazynowanie Firmy MicrosoftKonserwowanie/kolejkaUsługi/kolejki/odczyt | Zwracanie kolejki lub listy kolejek. |
> | Magazyn microsoft.storage/storageKonta/kolejkaSługi/kolejki/zapis | Modyfikowanie metadanych lub właściwości kolejki. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | Magazyn Microsoft.Storage/storageKonta/kolejkaSługi/kolejki/wiadomości/usuń | Usuń jedną lub więcej wiadomości z kolejki. |
> | Magazyn Microsoft.Storage/storageKonta/kolejkaSługi/kolejki/wiadomości/odczyt | Wgląd lub pobieranie jednej lub więcej wiadomości z kolejki. |
> | Magazyn firmy Microsoft/magazynOwaniekonta/kolejkaSługi/kolejki/wiadomości/zapis | Dodawanie wiadomości do kolejki. |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "name": "974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-processor"></a>Procesor komunikatów o danych kolejki magazynu

Wgląd, pobieranie i usuwanie wiadomości z kolejki usługi Azure Storage. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [Uprawnienia do wywoływania operacji danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | Magazyn Microsoft.Storage/storageKonta/kolejkaSługi/kolejki/wiadomości/odczyt | Zajrzyj do wiadomości. |
> | Magazyn firmy Microsoft/magazynAckonta/kolejkaSługi/kolejki/wiadomości/proces/akcja | Pobieranie i usuwanie wiadomości. |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for peek, receive, and delete access to Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "name": "8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Processor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-sender"></a>Nadawca wiadomości z danymi kolejki magazynu

Dodawanie wiadomości do kolejki usługi Azure Storage. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [Uprawnienia do wywoływania operacji danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | Magazyn firmy Microsoft/magazynAckonta/kolejkaSługi/kolejki/wiadomości/dodawanie/akcja | Dodawanie wiadomości do kolejki. |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for sending of Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "name": "c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-reader"></a>Czytnik danych kolejki magazynu

Odczytywanie i wyświetlanie listy kolejek usługi Azure Storage i wiadomości kolejek. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [Uprawnienia do wywoływania operacji danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Magazyn/magazynowanie Firmy MicrosoftKonserwowanie/kolejkaUsługi/kolejki/odczyt | Zwraca kolejkę lub listę kolejek. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | Magazyn Microsoft.Storage/storageKonta/kolejkaSługi/kolejki/wiadomości/odczyt | Wgląd lub pobieranie jednej lub więcej wiadomości z kolejki. |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/19e7f393-937e-4f77-808e-94535e297925",
  "name": "19e7f393-937e-4f77-808e-94535e297925",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="web"></a>sieć Web


### <a name="azure-maps-data-reader-preview"></a>Czytnik danych usługi Azure Maps (wersja zapoznawcza)

Udziela dostępu do odczytu danych związanych z mapami z konta map platformy Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | Microsoft.Maps/accounts/data/read | Udziela dostępu do odczytu danych do konta map. |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read map related data from an Azure maps account.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "name": "423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Maps/accounts/data/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Maps Data Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="search-service-contributor"></a>Współautor usługi wyszukiwania

Umożliwia zarządzanie usługami wyszukiwania, ale nie ma do nich dostępu.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Search/searchSługs/* | Tworzenie usług wyszukiwania i zarządzanie nimi |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Search services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "name": "7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Search/searchServices/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Search Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="web-plan-contributor"></a>Współautor planu sieci Web

Umożliwia zarządzanie planami sieci Web dla witryn sieci Web, ale nie ma do nich dostępu.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | Microsoft.Web/serverFarms/* | Tworzenie farm serwerów i zarządzanie nimi |
> | Microsoft.Web/hostingŚrodowienia/Dołączanie/Działanie | Dołącza do środowiska usługi aplikacji |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the web plans for websites, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "name": "2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/serverFarms/*",
        "Microsoft.Web/hostingEnvironments/Join/Action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Web Plan Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="website-contributor"></a>Współautor strony internetowej

Umożliwia zarządzanie witrynami sieci Web (nie planami internetowymi), ale nie ma do nich dostępu.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.Insights/składniki/* | Tworzenie składników aplikacji Insights i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | Microsoft.Web/certyfikaty/* | Tworzenie certyfikatów witryn sieci Web i zarządzanie nimi |
> | Microsoft.Web/listSitesAssignedToHostName/read | Pobierz nazwy witryn przypisanych do nazwy hosta. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Pobierz właściwości w planie usługi aplikacji |
> | Microsoft.Web/sites/* | Tworzenie witryn sieci Web i zarządzanie nimi (tworzenie witryn wymaga również uprawnień do zapisu w skojarzonym planie usługi app service) |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage websites (not web plans), but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/de139f84-1756-47ae-9be6-808fbbe84772",
  "name": "de139f84-1756-47ae-9be6-808fbbe84772",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/certificates/*",
        "Microsoft.Web/listSitesAssignedToHostName/read",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Website Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="containers"></a>Kontenery


### <a name="acrdelete"></a>AcrDelete (AcrDelete)

acr usunąć

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.ContainerRegistry/registries/artifacts/delete Microsoft.ContainerRegistry/registries/artifacts/delete Microsoft.ContainerRegistry/registries/artifacts/delete Microsoft. | Usuwanie artefaktu w rejestrze kontenerów. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr delete",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "name": "c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/artifacts/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrDelete",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrimagesigner"></a>AcrImageSigner (Znak AcrImage)

acr sygnatariusz obrazu

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.ContainerRegistry/registries/sign/write | Push/Pull zawartość zaufania metadanych dla rejestru kontenerów. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr image signer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6cef56e8-d556-48e5-a04f-b8e64114680f",
  "name": "6cef56e8-d556-48e5-a04f-b8e64114680f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/sign/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrImageSigner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpull"></a>AcrPull ( AcrPull )

acr ciągnąć

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Pobierz lub pobierz obrazy z rejestru kontenerów. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr pull",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "name": "7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPull",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpush"></a>Acrpush ( Acrpush )

acr push

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Pobierz lub pobierz obrazy z rejestru kontenerów. |
> | Microsoft.ContainerRegistry/registries/push/write | Wypychanie lub zapisywanie obrazów w rejestrze kontenerów. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr push",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8311e382-0749-4cb8-b61a-304f252e45ec",
  "name": "8311e382-0749-4cb8-b61a-304f252e45ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read",
        "Microsoft.ContainerRegistry/registries/push/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPush",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinereader"></a>Czytnik AcrQuarantine

czytnik danych kwarantanny acr

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.ContainerRegistry/registries/quarantine/read | Ściąganie lub pobieranie obrazów poddanych kwarantannie z rejestru kontenerów |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cdda3590-29a3-44f6-95f2-9f980659eb04",
  "name": "cdda3590-29a3-44f6-95f2-9f980659eb04",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineReader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinewriter"></a>AcrQuarantineWriter

acr moduł zapisujący dane kwarantanny

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.ContainerRegistry/registries/quarantine/read | Ściąganie lub pobieranie obrazów poddanych kwarantannie z rejestru kontenerów |
> | Microsoft.ContainerRegistry/rejestry/kwarantanna/zapis | Zapisz/zmodyfikuj stan kwarantanny obrazów poddanych kwarantannie |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data writer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "name": "c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read",
        "Microsoft.ContainerRegistry/registries/quarantine/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineWriter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-admin-role"></a>Rola administratora klastra usługi Azure Kubernetes

Wyświetl akcję poświadczeń administratora klastra.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Wyświetlanie listy poświadczeń clusterAdmin klastra zarządzanego klastra |
> | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Uzyskiwanie zarządzanego profilu dostępu do klastra według nazwy roli przy użyciu poświadczeń listy |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster admin credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "name": "0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action",
        "Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster Admin Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-user-role"></a>Rola użytkownika klastra usług Azure Kubernetes

Wyświetl listę akcji poświadczeń użytkownika klastra.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Wyświetlanie listy poświadczeń użytkownika klastra zarządzanego klastra |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster user credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "name": "4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster User Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="databases"></a>Bazy danych


### <a name="cosmos-db-account-reader-role"></a>Rola czytnika kont usługi Cosmos DB

Może odczytywać dane konta usługi Azure Cosmos DB. Zobacz [Współautor konta documentdb](#documentdb-account-contributor) do zarządzania kontami usługi Azure Cosmos DB.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.DocumentDB/*/odczyt | Przeczytaj dowolną kolekcję |
> | Microsoft.DocumentDB/databaseKonta/readonlykeys/action | Odczytuje konto bazy danych tylko klucze odczytu. |
> | Microsoft.Insights/MetricDefinitions/read | Odczyt definicji metryk |
> | Microsoft.Insights/Metryki/odczyt | Odczytywanie danych |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read Azure Cosmos DB Accounts data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "name": "fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDB/*/read",
        "Microsoft.DocumentDB/databaseAccounts/readonlykeys/action",
        "Microsoft.Insights/MetricDefinitions/read",
        "Microsoft.Insights/Metrics/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Account Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmos-db-operator"></a>Operator bazy danych Cosmos

Umożliwia zarządzanie kontami usługi Azure Cosmos DB, ale nie uzyskiwać w nich dostępu do danych. Uniemożliwia dostęp do kluczy kont i ciągów połączeń.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.DocumentDb/databaseKonta/* |  |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Łączy zasób, taki jak konto magazynu lub baza danych SQL, do podsieci. Nie czujny. |
> | **NotActions** |  |
> | Microsoft.DocumentDB/databaseKonta/readonlyKeys/* |  |
> | Microsoft.DocumentDB/databaseKonta/regenerateKey/* |  |
> | Microsoft.DocumentDB/databaseKonta/listKeys/* |  |
> | Microsoft.DocumentDB/databaseKonta/listConnectionStrings/* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Cosmos DB accounts, but not access data in them. Prevents access to account keys and connection strings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/230815da-be43-4aae-9cb4-875f7bd000aa",
  "name": "230815da-be43-4aae-9cb4-875f7bd000aa",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [
        "Microsoft.DocumentDB/databaseAccounts/readonlyKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/regenerateKey/*",
        "Microsoft.DocumentDB/databaseAccounts/listKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmosbackupoperator"></a>Kosmopracownik

Można przesłać żądanie przywracania bazy danych usługi Cosmos DB lub kontenera dla konta

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.DocumentDB/databaseKonta/kopia zapasowa/akcja | Prześlij żądanie skonfigurowania kopii zapasowej |
> | Microsoft.DocumentDB/databaseKonta/przywracanie/akcja | Prześlij żądanie przywrócenia |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can submit restore request for a Cosmos DB database or a container for an account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "name": "db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDB/databaseAccounts/backup/action",
        "Microsoft.DocumentDB/databaseAccounts/restore/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CosmosBackupOperator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="documentdb-account-contributor"></a>Współautor konta DocumentDB

Może zarządzać kontami usługi Azure Cosmos DB. Usługa Azure Cosmos DB jest wcześniej znany jako DocumentDB.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.DocumentDb/databaseKonta/* | Tworzenie kont usługi Azure Cosmos DB i zarządzanie nimi |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Łączy zasób, taki jak konto magazynu lub baza danych SQL, do podsieci. Nie czujny. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DocumentDB accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5bd9cd88-fe45-4216-938b-f97437e15450",
  "name": "5bd9cd88-fe45-4216-938b-f97437e15450",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DocumentDB Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="redis-cache-contributor"></a>Współautor pamięci podręcznej Redis

Umożliwia zarządzanie pamięciami podręcznymi Redis, ale nie ma do nich dostępu.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Pamięć podręczna/redis firmy Microsoft | Tworzenie pamięci podręcznych Redis i zarządzanie nimi |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Redis caches, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e0f68234-74aa-48ed-b826-c38b57376e17",
  "name": "e0f68234-74aa-48ed-b826-c38b57376e17",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cache/redis/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Redis Cache Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-db-contributor"></a>Współautor bazy danych SQL

Umożliwia zarządzanie bazami danych SQL, ale nie ma do nich dostępu. Ponadto nie można zarządzać ich zasad związanych z zabezpieczeniami lub ich nadrzędnych serwerów SQL.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/serwery/bazy danych/* | Tworzenie baz danych SQL i zarządzanie nimi |
> | Microsoft.Sql/servers/read | Zwraca listę serwerów lub pobiera właściwości dla określonego serwera. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | Microsoft.Insights/metryki/odczyt | Odczytywanie danych |
> | Microsoft.Insights/metricDefinitions/read | Odczyt definicji metryk |
> | **NotActions** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Edytowanie zasad inspekcji |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Edytowanie ustawień inspekcji |
> | Microsoft.Sql/servers/databases/auditRecords/read | Pobieranie rekordów inspekcji obiektów blob bazy danych |
> | Microsoft.Sql/serwery/bazy danych/connectionPolicies/* | Edytowanie zasad połączeń |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Edytowanie zasad maskowania danych |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Edytowanie zasad alertów zabezpieczeń |
> | Microsoft.Sql/serwery/bazy danych/securityMetrics/* | Edytowanie wskaźników zabezpieczeń |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL databases, but not access to them. Also, you can't manage their security-related policies or their parent SQL servers.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "name": "9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/databases/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL DB Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-managed-instance-contributor"></a>Współautor wystąpienia zarządzanego SQL

Umożliwia zarządzanie wystąpieniami zarządzanymi sql i wymaganą konfiguracją sieci, ale nie można udzielić dostępu innym osobom.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Network/networkSecurityGroups/* |  |
> | Microsoft.Network/routeTables/* |  |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/managedInstances/* |  |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | Microsoft.Network/virtualNetworks/podsieci/* |  |
> | Microsoft.Network/virtualNetworks/* |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.Insights/metryki/odczyt | Odczytywanie danych |
> | Microsoft.Insights/metricDefinitions/read | Odczyt definicji metryk |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL Managed Instances and required network configuration, but can't give access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "name": "4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Network/networkSecurityGroups/*",
        "Microsoft.Network/routeTables/*",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/managedInstances/*",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/*",
        "Microsoft.Network/virtualNetworks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Managed Instance Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-security-manager"></a>Menedżer zabezpieczeń SQL

Umożliwia zarządzanie zasadami zabezpieczeń serwerów i baz danych SQL, ale nie ma do nich dostępu.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Łączy zasób, taki jak konto magazynu lub baza danych SQL, do podsieci. Nie czujny. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Tworzenie zasad inspekcji serwera SQL i zarządzanie nimi |
> | Microsoft.Sql/servers/auditingSettings/* | Tworzenie ustawień inspekcji serwera SQL i zarządzanie nim |
> | Microsoft.Sql/servers/extendedAuditingSettings/read | Pobieranie szczegółów rozszerzonej zasady inspekcji obiektów blob serwera skonfigurowanej na danym serwerze |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Tworzenie zasad inspekcji baz danych serwera SQL i zarządzanie nimi |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Tworzenie ustawień inspekcji baz danych serwera SQL i zarządzanie nimi |
> | Microsoft.Sql/servers/databases/auditRecords/read | Pobieranie rekordów inspekcji obiektów blob bazy danych |
> | Microsoft.Sql/serwery/bazy danych/connectionPolicies/* | Tworzenie zasad połączeń z bazą danych serwera SQL i zarządzanie nimi |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Tworzenie zasad maskowania danych serwera SQL Server i zarządzanie nimi |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Pobieranie szczegółów rozszerzonej zasady inspekcji obiektów blob skonfigurowanej w danej bazie danych |
> | Microsoft.Sql/serwery/bazy danych/odczyt | Zwraca listę baz danych lub pobiera właściwości dla określonej bazy danych. |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/read | Pobierz schemat bazy danych. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Pobierz kolumnę bazy danych. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/read | Pobierz tabelę bazy danych. |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Tworzenie zasad alertów zabezpieczeń bazy danych serwera SQL i zarządzanie nimi |
> | Microsoft.Sql/serwery/bazy danych/securityMetrics/* | Tworzenie metryk zabezpieczeń bazy danych serwera SQL i zarządzanie nimi |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/transparentDataEncryption/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/firewallRules/* |  |
> | Microsoft.Sql/servers/read | Zwraca listę serwerów lub pobiera właściwości dla określonego serwera. |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Tworzenie zasad alertów zabezpieczeń serwera SQL i zarządzanie nimi |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the security-related policies of SQL servers and databases, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "name": "056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/transparentDataEncryption/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingPolicies/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/read",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/read",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/transparentDataEncryption/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/firewallRules/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Security Manager",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-server-contributor"></a>Współautor programu SQL Server

Umożliwia zarządzanie serwerami SQL i bazami danych, ale nie ma do nich dostępu, a nie ich zasadami związanymi z zabezpieczeniami.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/serwery/* | Tworzenie serwerów SQL i zarządzanie nimi |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | Microsoft.Insights/metryki/odczyt | Odczytywanie danych |
> | Microsoft.Insights/metricDefinitions/read | Odczyt definicji metryk |
> | **NotActions** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Edytowanie zasad inspekcji serwera SQL |
> | Microsoft.Sql/servers/auditingSettings/* | Edytowanie ustawień inspekcji serwera SQL |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Edytowanie zasad inspekcji bazy danych serwera SQL |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Edytowanie ustawień inspekcji bazy danych serwera SQL |
> | Microsoft.Sql/servers/databases/auditRecords/read | Pobieranie rekordów inspekcji obiektów blob bazy danych |
> | Microsoft.Sql/serwery/bazy danych/connectionPolicies/* | Edytowanie zasad połączeń z bazą danych serwera SQL |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Edytowanie zasad maskowania danych serwera SQL Server |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Edytowanie zasad alertów zabezpieczeń bazy danych serwera SQL |
> | Microsoft.Sql/serwery/bazy danych/securityMetrics/* | Edytowanie metryk zabezpieczeń bazy danych serwera SQL |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Edytowanie zasad alertów zabezpieczeń serwera SQL |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL servers and databases, but not access to them, and not their security -related policies.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "name": "6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/*",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingPolicies/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Server Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="analytics"></a>Analiza


### <a name="azure-event-hubs-data-owner"></a>Właściciel danych usługi Azure Event Hubs

Umożliwia pełny dostęp do zasobów usługi Azure Event Hubs.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.EventHub/* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | Microsoft.EventHub/* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f526a384-b230-433a-b45c-95f59c4a2dec",
  "name": "f526a384-b230-433a-b45c-95f59c4a2dec",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-receiver"></a>Odbiorca danych usługi Azure Event Hubs

Umożliwia odbierania dostępu do zasobów usługi Azure Event Hubs.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.EventHub/*/eventhubs/consumergroups/read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | Microsoft.EventHub/*/odbieranie/akcja |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows receive access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "name": "a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/consumergroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-sender"></a>Nadawca danych usługi Azure Event Hubs

Umożliwia wysyłanie dostępu do zasobów usługi Azure Event Hubs.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.EventHub/*/eventhubs/read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | Microsoft.EventHub/*/wyślij/akcja |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows send access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2b629674-e913-4c01-ae53-ef4638d8f975",
  "name": "2b629674-e913-4c01-ae53-ef4638d8f975",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-factory-contributor"></a>Współautor fabryki danych

Tworzenie fabryk danych i zarządzanie nimi, a także zasoby podrzędne w nich.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.DataFactory/dataFactories/* | Tworzenie fabryk danych i zarządzania nimi oraz zasoby podrzędne w nich. |
> | Microsoft.DataFactory/fabryki/* | Tworzenie fabryk danych i zarządzania nimi oraz zasoby podrzędne w nich. |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | Microsoft.EventGrid/eventSubscriptions/write | Tworzenie lub aktualizowanie zdarzeniaSubskrypcja |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and manage data factories, as well as child resources within them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/673868aa-7521-48a0-acc6-0f60742d39f5",
  "name": "673868aa-7521-48a0-acc6-0f60742d39f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DataFactory/dataFactories/*",
        "Microsoft.DataFactory/factories/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.EventGrid/eventSubscriptions/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Factory Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-purger"></a>Purger danych

Może przeczyścić dane analityczne

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Insights/składniki/przeczyszczanie/akcja | Czyszczenie danych z usługi Application Insights |
> | Microsoft.OperationalInsights/workspaces/*/read | Wyświetlanie danych analizy dzienników |
> | Microsoft.OperationalInsights/workspaces/purge/action | Usuwanie określonych danych z obszaru roboczego |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can purge analytics data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "name": "150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/components/*/read",
        "Microsoft.Insights/components/purge/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/purge/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Purger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-cluster-operator"></a>Operator klastra HDInsight

Umożliwia odczytywanie i modyfikowanie konfiguracji klastra HDInsight.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.HDInsight/*/odczyt |  |
> | Microsoft.HDInsight/clusters/getGatewaySettings/action | Pobierz ustawienia bramy dla klastra HDInsight |
> | Microsoft.HDInsight/clusters/updateGatewaySettings/action | Aktualizowanie ustawień bramy dla klastra usługi HDInsight |
> | Microsoft.HDInsight/klastry/konfiguracje/* |  |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Zasoby firmy Microsoft/wdrożenia/operacje/odczyt | Pobiera lub wyświetla listę operacji wdrażania. |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and modify HDInsight cluster configurations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/61ed4efc-fab3-44fd-b111-e24485cc132a",
  "name": "61ed4efc-fab3-44fd-b111-e24485cc132a",
  "permissions": [
    {
      "actions": [
        "Microsoft.HDInsight/*/read",
        "Microsoft.HDInsight/clusters/getGatewaySettings/action",
        "Microsoft.HDInsight/clusters/updateGatewaySettings/action",
        "Microsoft.HDInsight/clusters/configurations/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Cluster Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-domain-services-contributor"></a>Współautor usług domenowych HDInsight

Może odczytywać, tworzyć, modyfikować i usuwać operacje związane z usługami domenowymi wymagane dla pakietu zabezpieczeń hdinsight enterprise

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.AAD/*/odczyt |  |
> | Microsoft.AAD/domainSługa/*/odczyt |  |
> | Microsoft.AAD/domainSługa/oucontainer/* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can Read, Create, Modify and Delete Domain Services related operations needed for HDInsight Enterprise Security Package",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "name": "8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "permissions": [
    {
      "actions": [
        "Microsoft.AAD/*/read",
        "Microsoft.AAD/domainServices/*/read",
        "Microsoft.AAD/domainServices/oucontainer/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Domain Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-contributor"></a>Współautor usługi Log Analytics

Współautor usługi Log Analytics może odczytywać wszystkie dane monitorowania i edytować ustawienia monitorowania. Ustawienia monitorowania edycji obejmują dodawanie rozszerzenia maszyny Wirtualnej do maszyn wirtualnych; odczytywanie kluczy konta magazynu, aby móc skonfigurować zbieranie dzienników z usługi Azure Storage; tworzenie i konfigurowanie kont automatyzacji; dodawanie rozwiązań; i konfigurowanie diagnostyki platformy Azure na wszystkich zasobach platformy Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | */odczyt | Odczytywanie zasobów wszystkich typów, z wyjątkiem wpisów tajnych. |
> | Microsoft.Automation/automationKonta/* |  |
> | Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
> | Microsoft.ClassicStorage/storageKonta/listKeys/akcja | Wyświetla listę kluczy dostępu dla kont magazynu. |
> | Microsoft.Compute/virtualMachines/extensions/* |  |
> | Microsoft.HybridCompute/maszyny/rozszerzenia/zapis | Instalowanie lub aktualizowanie rozszerzeń łuku platformy Azure |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.Insights/diagnosticSettings/* | Tworzy, aktualizuje lub odczytuje ustawienie diagnostyczne dla analysis server |
> | Microsoft.OperationalInsights/* |  |
> | Microsoft.OperationsManagement/* |  |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/grupy/wdrożenia zasobów/* |  |
> | Magazyn/magazyn microsoftKonserwuje/listKeys/akcja | Zwraca klucze dostępu dla określonego konta magazynu. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Contributor can read all monitoring data and edit monitoring settings. Editing monitoring settings includes adding the VM extension to VMs; reading storage account keys to be able to configure collection of logs from Azure Storage; creating and configuring Automation accounts; adding solutions; and configuring Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "name": "92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Automation/automationAccounts/*",
        "Microsoft.ClassicCompute/virtualMachines/extensions/*",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.Compute/virtualMachines/extensions/*",
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.OperationalInsights/*",
        "Microsoft.OperationsManagement/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-reader"></a>Czytelnik usługi Log Analytics

Program Log Analytics Reader może wyświetlać i przeszukiwać wszystkie dane monitorowania, a także wyświetlać ustawienia monitorowania, w tym wyświetlać konfigurację diagnostyki platformy Azure na wszystkich zasobach platformy Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | */odczyt | Odczytywanie zasobów wszystkich typów, z wyjątkiem wpisów tajnych. |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Szukaj za pomocą nowego silnika. |
> | Microsoft.OperationalInsights/workspaces/search/action | Wykonuje kwerendę wyszukiwania |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Pobiera klucze udostępnione dla obszaru roboczego. Te klucze są używane do łączenia agentów usługi Microsoft Operational Insights z obszarem roboczym. |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Reader can view and search all monitoring data as well as and view monitoring settings, including viewing the configuration of Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/73c42c96-874c-492b-b04d-ab87d138a893",
  "name": "73c42c96-874c-492b-b04d-ab87d138a893",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.OperationalInsights/workspaces/sharedKeys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="blockchain"></a>Łańcuch bloków


### <a name="blockchain-member-node-access-preview"></a>Dostęp do węzła członkowskiego łańcucha bloków (wersja zapoznawcza)

Umożliwia dostęp do węzłów członka łańcucha bloków

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Blockchain/blockchainCzłonek/transactionNodes/read | Pobiera lub wyświetla istniejące węzły transakcji członkowskich łańcucha bloków. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | Microsoft.Blockchain/blockchainCzłonek/transactionNodes/connect/action | Łączy się z węzłem transakcji członka łańcucha bloków. |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for access to Blockchain Member nodes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "name": "31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "permissions": [
    {
      "actions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Blockchain Member Node Access (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="ai--machine-learning"></a>AI + uczenie maszynowe


### <a name="cognitive-services-contributor"></a>Współautor usług kognitywnych

Umożliwia tworzenie, odczytywanie, aktualizowanie, usuwanie i zarządzanie kluczami usług Cognitive Services.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.CognitiveServices/* |  |
> | Microsoft.Funkcje/funkcje/odczyt | Pobiera funkcje subskrypcji. |
> | Microsoft.Funkcje/dostawcy/funkcje/odczyt | Pobiera funkcję subskrypcji w danym dostawcy zasobów. |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.Insights/diagnosticSettings/* | Tworzy, aktualizuje lub odczytuje ustawienie diagnostyczne dla analysis server |
> | Microsoft.Insights/logDefinitions/read | Odczytywanie definicji dzienników |
> | Microsoft.Insights/metricdefinitions/read | Odczyt definicji metryk |
> | Microsoft.Insights/metryki/odczyt | Odczytywanie danych |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/wdrożenia/operacje/odczyt | Pobiera lub wyświetla listę operacji wdrażania. |
> | Zasoby Firmy Microsoft/subskrypcje/operacjawyniki/odczyt | Pobierz wyniki operacji subskrypcji. |
> | Zasoby Firmy Microsoft/subskrypcje/odczyt | Pobiera listę subskrypcji. |
> | Zasoby firmy Microsoft/subskrypcje/grupy/wdrożenia zasobów/* |  |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create, read, update, delete and manage keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "name": "25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.CognitiveServices/*",
        "Microsoft.Features/features/read",
        "Microsoft.Features/providers/features/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-data-reader-preview"></a>Czytnik danych usług Cognitive Services (wersja zapoznawcza)

Umożliwia odczytywanie danych usług Cognitive Services.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | Microsoft.CognitiveServices/*/odczyt |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read Cognitive Services data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "name": "b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Data Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-user"></a>Użytkownik usług Kognitywnych

Umożliwia odczytywanie i wyświetlanie listy kluczy usług Cognitive Services.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.CognitiveServices/*/odczyt |  |
> | Microsoft.CognitiveServices/accounts/listkeys/action | Klawisze listy |
> | Microsoft.Insights/alertRules/read | Odczytywanie klasycznego alertu metrycznego |
> | Microsoft.Insights/diagnosticSettings/read | Odczytanie ustawienia diagnostycznego zasobu |
> | Microsoft.Insights/logDefinitions/read | Odczytywanie definicji dzienników |
> | Microsoft.Insights/metricdefinitions/read | Odczyt definicji metryk |
> | Microsoft.Insights/metryki/odczyt | Odczytywanie danych |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | Zasoby firmy Microsoft/wdrożenia/operacje/odczyt | Pobiera lub wyświetla listę operacji wdrażania. |
> | Zasoby Firmy Microsoft/subskrypcje/operacjawyniki/odczyt | Pobierz wyniki operacji subskrypcji. |
> | Zasoby Firmy Microsoft/subskrypcje/odczyt | Pobiera listę subskrypcji. |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | Microsoft.CognitiveServices/* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and list keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a97b65f3-24c7-4388-baec-2e87135dc908",
  "name": "a97b65f3-24c7-4388-baec-2e87135dc908",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.CognitiveServices/accounts/listkeys/action",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Insights/diagnosticSettings/read",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="mixed-reality"></a>Rzeczywistość mieszana


### <a name="spatial-anchors-account-contributor"></a>Współautor konta zakotwiczenia przestrzennego

Umożliwia zarządzanie kotwicami przestrzennymi na koncie, ale nie ich usuwanie

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Tworzenie zakotwiczeń przestrzennych |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Odkryj pobliskie kotwice przestrzenne |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Uzyskaj właściwości zakotwiczeń przestrzennych |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Lokalizowanie zakotwiczeń przestrzennych |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Przesyłanie danych diagnostycznych w celu poprawy jakości usługi Azure Spatial Anchors |
> | Microsoft.MixedReality/SpatialAnchorsAkt/zapis | Aktualizowanie właściwości zakotwiczeń przestrzennych |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, but not delete them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "name": "8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-owner"></a>Właściciel konta zakotwiczenia przestrzennego

Umożliwia zarządzanie kotwicami przestrzennymi na koncie, w tym ich usuwanie

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Tworzenie zakotwiczeń przestrzennych |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/delete Microsoft.MixedReality/SpatialAnchorsAccounts/delete Microsoft.MixedReality/SpatialAnchorsAccounts/delete Microsoft. | Usuwanie zakotwiczeń przestrzennych |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Odkryj pobliskie kotwice przestrzenne |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Uzyskaj właściwości zakotwiczeń przestrzennych |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Lokalizowanie zakotwiczeń przestrzennych |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Przesyłanie danych diagnostycznych w celu poprawy jakości usługi Azure Spatial Anchors |
> | Microsoft.MixedReality/SpatialAnchorsAkt/zapis | Aktualizowanie właściwości zakotwiczeń przestrzennych |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, including deleting them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/70bbe301-9835-447d-afdd-19eb3167307c",
  "name": "70bbe301-9835-447d-afdd-19eb3167307c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/delete",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-reader"></a>Czytnik kont zakotwiczenia przestrzennego

Umożliwia lokalizowanie i odczytywanie właściwości zakotwiczeń przestrzennych na koncie

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Odkryj pobliskie kotwice przestrzenne |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Uzyskaj właściwości zakotwiczeń przestrzennych |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Lokalizowanie zakotwiczeń przestrzennych |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Przesyłanie danych diagnostycznych w celu poprawy jakości usługi Azure Spatial Anchors |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you locate and read properties of spatial anchors in your account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "name": "5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="integration"></a>Integracja


### <a name="api-management-service-contributor"></a>Współautor usługi zarządzania interfejsami API

Może zarządzać usługami i interfejsami API

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.ApiManagement/usługa/* | Tworzenie usługi api Management i zarządzanie nią |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service and the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c",
  "name": "312a565d-c81f-4fd8-895a-4e21e48d571c",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-operator-role"></a>Rola operatora usługi zarządzania interfejsami API

Może zarządzać usługą, ale nie interfejsami API

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.ApiManagement/service/*/read | Odczytywanie wystąpień usługi zarządzania interfejsami API |
> | Microsoft.ApiManagement/service/backup/action | Tworzenie kopii zapasowych usługi zarządzania interfejsami API do określonego kontenera na koncie magazynu podanym przez użytkownika |
> | Microsoft.ApiManagement/service/delete Microsoft.ApiManagement/service/delete Microsoft.ApiManagement/service/delete Microsoft. | Usuń wystąpienie usługi zarządzania interfejsami API |
> | Microsoft.ApiManagement/service/managedeployments/action Microsoft.ApiManagement/service/managedeployments/action Microsoft.ApiManagement/service/managedeployments/action Microsoft. | Zmienianie jednostki SKU/jednostek, dodawanie/usuwanie regionalnych wdrożeń usługi api Management Service |
> | Microsoft.ApiManagement/service/read | Odczytywanie metadanych wystąpienia usługi zarządzania interfejsami API |
> | Microsoft.ApiManagement/service/restore/action | Przywracanie usługi zarządzania interfejsami API z określonego kontenera na koncie magazynu dostarczonym przez użytkownika |
> | Microsoft.ApiManagement/service/updatecertificate/action | Przekazywanie certyfikatu TLS/SSL dla usługi zarządzania interfejsem API |
> | Microsoft.ApiManagement/service/updatehostname/action | Konfigurowanie, aktualizowanie lub usuwanie niestandardowych nazw domen usługi zarządzania interfejsami API |
> | Microsoft.ApiManagement/service/write | Tworzenie lub aktualizowanie wystąpienia usługi zarządzania interfejsami API |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Uzyskaj klucze skojarzone z użytkownikiem |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service but not the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "name": "e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/backup/action",
        "Microsoft.ApiManagement/service/delete",
        "Microsoft.ApiManagement/service/managedeployments/action",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.ApiManagement/service/restore/action",
        "Microsoft.ApiManagement/service/updatecertificate/action",
        "Microsoft.ApiManagement/service/updatehostname/action",
        "Microsoft.ApiManagement/service/write",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-reader-role"></a>Rola czytnika usług zarządzania interfejsami API

Dostęp tylko do odczytu do usług i interfejsów API

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.ApiManagement/service/*/read | Odczytywanie wystąpień usługi zarządzania interfejsami API |
> | Microsoft.ApiManagement/service/read | Odczytywanie metadanych wystąpienia usługi zarządzania interfejsami API |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Uzyskaj klucze skojarzone z użytkownikiem |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only access to service and APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/71522526-b88f-4d52-b57f-d31fc3546d0d",
  "name": "71522526-b88f-4d52-b57f-d31fc3546d0d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-owner"></a>Właściciel danych konfiguracji aplikacji

Umożliwia pełny dostęp do danych konfiguracji aplikacji.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | Microsoft.AppConfiguration/configurationStores/*/read |  |
> | Microsoft.AppConfiguration/configurationStores/*/write |  |
> | Microsoft.AppConfiguration/configurationStores/*/delete Microsoft.AppConfiguration/configurationStores/*/delete Microsoft.AppConfiguration/configurationStores/*/delete Microsoft. |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows full access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "name": "5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read",
        "Microsoft.AppConfiguration/configurationStores/*/write",
        "Microsoft.AppConfiguration/configurationStores/*/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-reader"></a>Czytnik danych konfiguracji aplikacji

Umożliwia dostęp do odczytu danych konfiguracji aplikacji.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | Microsoft.AppConfiguration/configurationStores/*/read |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/516239f1-63e1-4d78-a4de-a74fb236a071",
  "name": "516239f1-63e1-4d78-a4de-a74fb236a071",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-owner"></a>Właściciel danych usługi Azure Service Bus

Umożliwia pełny dostęp do zasobów usługi Azure Service Bus.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.ServiceBus/* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | Microsoft.ServiceBus/* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/090c5cfd-751d-490a-894a-3ce6f1109419",
  "name": "090c5cfd-751d-490a-894a-3ce6f1109419",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-receiver"></a>Odbiornik danych usługi Azure Service Bus

Umożliwia odbieranie dostępu do zasobów usługi Azure Service Bus.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.ServiceBus/*/kolejki/odczyt |  |
> | Microsoft.ServiceBus/*/topics/read |  |
> | Microsoft.ServiceBus/*/topics/subscriptions/read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | Microsoft.ServiceBus/*/odbieranie/akcja |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for receive access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "name": "4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-sender"></a>Nadawca danych usługi Azure Service Bus

Umożliwia wysyłanie dostępu do zasobów usługi Azure Service Bus.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.ServiceBus/*/kolejki/odczyt |  |
> | Microsoft.ServiceBus/*/topics/read |  |
> | Microsoft.ServiceBus/*/topics/subscriptions/read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | Microsoft.ServiceBus/*/wyślij/akcję |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for send access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "name": "69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-stack-registration-owner"></a>Właściciel rejestracji stosu azure

Umożliwia zarządzanie rejestracjami usługi Azure Stack.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.AzureStack/registrations/products/*/action Microsoft.AzureStack/registrations/products/*/action Microsoft.AzureStack/registrations/products/*/action Microsoft. |  |
> | Microsoft.AzureStack/registrations/products/read Microsoft.AzureStack/registrations/products/read Microsoft.AzureStack/registrations/products/read Microsoft. | Pobiera właściwości produktu portalu Azure Stack Marketplace |
> | Microsoft.AzureStack/registrations/read | Pobiera właściwości rejestracji usługi Azure Stack |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Stack registrations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "name": "6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "permissions": [
    {
      "actions": [
        "Microsoft.AzureStack/registrations/products/*/action",
        "Microsoft.AzureStack/registrations/products/read",
        "Microsoft.AzureStack/registrations/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Stack Registration Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-contributor"></a>Współautor eventgrid eventsubscription

Umożliwia zarządzanie operacjami subskrypcji zdarzeń EventGrid.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.EventGrid/eventSubscriptions/* |  |
> | Microsoft.EventGrid/topicTypy/eventSubscriptions/read | Wyświetlanie subskrypcji zdarzeń globalnych według typu tematu |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Wyświetlanie subskrypcji zdarzeń regionalnych |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Wyświetlanie subskrypcji zdarzeń regionalnych według typu tematu |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage EventGrid event subscription operations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "name": "428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/*",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-reader"></a>EventGrid EventSubscription Reader

Umożliwia odczytywanie subskrypcji zdarzeń EventGrid.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.EventGrid/eventSubscriptions/read | Czytanie zdarzeniaSubscription |
> | Microsoft.EventGrid/topicTypy/eventSubscriptions/read | Wyświetlanie subskrypcji zdarzeń globalnych według typu tematu |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Wyświetlanie subskrypcji zdarzeń regionalnych |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Wyświetlanie subskrypcji zdarzeń regionalnych według typu tematu |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read EventGrid event subscriptions.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2414bbcf-6497-4faf-8c65-045460748405",
  "name": "2414bbcf-6497-4faf-8c65-045460748405",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/read",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="intelligent-systems-account-contributor"></a>Współautor konta inteligentnych systemów

Umożliwia zarządzanie kontami inteligentnych systemów, ale nie ma do nich dostępu.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.IntelligentSystems/konta/* | Tworzenie kont inteligentnych systemów i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Intelligent Systems accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/03a6d094-3444-4b3d-88af-7477090a9e5e",
  "name": "03a6d094-3444-4b3d-88af-7477090a9e5e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.IntelligentSystems/accounts/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Intelligent Systems Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-contributor"></a>Współautor aplikacji logiki

Umożliwia zarządzanie aplikacjami logicznymi, ale nie zmienia ich dostępu.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.ClassicStorage/storageKonta/listKeys/akcja | Wyświetla listę kluczy dostępu dla kont magazynu. |
> | Microsoft.ClassicStorage/storageKonta/odczyt | Zwróć konto magazynu przy podanym koncie. |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.Insights/metricAlerts/* |  |
> | Microsoft.Insights/diagnosticSettings/* | Tworzy, aktualizuje lub odczytuje ustawienie diagnostyczne dla analysis server |
> | Microsoft.Insights/logdefinitions/* | To uprawnienie jest niezbędne dla użytkowników, którzy potrzebują dostępu do dzienników aktywności za pośrednictwem portalu. Lista kategorii dziennika w dzienniku aktywności. |
> | Microsoft.Insights/metryDefinitions/* | Odczyt definicji metryk (lista dostępnych typów metryk dla zasobu). |
> | Microsoft.Logic/* | Zarządza zasobami aplikacji logiki. |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby Firmy Microsoft/subskrypcje/operacjawyniki/odczyt | Pobierz wyniki operacji subskrypcji. |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Magazyn/magazyn microsoftKonserwuje/klucze listy/akcja | Zwraca klucze dostępu dla określonego konta magazynu. |
> | Magazyn/magazyn microsoftKonsowania/odczytu | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | Microsoft.Web/connectionGateways/* | Tworzenie bramy połączenia i zarządzanie nią. |
> | Microsoft.Web/połączenia/* | Tworzenie połączenia i zarządzanie nim. |
> | Microsoft.Web/customApis/* | Tworzy niestandardowy interfejs API i zarządza nim. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Pobierz właściwości w planie usługi aplikacji |
> | Microsoft.Web/sites/functions/listSecrets/action Microsoft.Web/sites/functions/listSecrets/action | Wpisy tajne funkcji listy. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage logic app, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "name": "87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logdefinitions/*",
        "Microsoft.Insights/metricDefinitions/*",
        "Microsoft.Logic/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*",
        "Microsoft.Web/connections/*",
        "Microsoft.Web/customApis/*",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/functions/listSecrets/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-operator"></a>Operator aplikacji logiki

Umożliwia odczytywanie, włączanie i wyłączanie aplikacji logiki, ale nie jest ich edytowanie ani aktualizowanie.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Insights/alertRules/*/read | Przeczytaj reguły alertów aplikacji Insights |
> | Microsoft.Insights/metricAlerts/*/read |  |
> | Microsoft.Insights/diagnosticSettings/*/read | Pobiera ustawienia diagnostyczne dla aplikacji logiki |
> | Microsoft.Insights/metricDefinitions/*/read | Pobiera dostępne metryki dla aplikacji logiki. |
> | Microsoft.Logic/*/odczyt | Odczytuje zasoby aplikacji logiki. |
> | Microsoft.Logic/workflows/disable/action | Wyłącza przepływ pracy. |
> | Microsoft.Logic/workflows/enable/action | Włącza przepływ pracy. |
> | Microsoft.Logic/workflows/validate/action | Sprawdza poprawność przepływu pracy. |
> | Zasoby firmy Microsoft/wdrożenia/operacje/odczyt | Pobiera lub wyświetla listę operacji wdrażania. |
> | Zasoby Firmy Microsoft/subskrypcje/operacjawyniki/odczyt | Pobierz wyniki operacji subskrypcji. |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | Microsoft.Web/connectionGateways/*/read | Odczyt bram połączeń. |
> | Microsoft.Web/connections/*/read | Odczyt połączeń. |
> | Microsoft.Web/customApis/*/read | Przeczytaj niestandardowy interfejs API. |
> | Microsoft.Web/serverFarms/read | Pobierz właściwości w planie usługi aplikacji |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read, enable and disable logic app.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "name": "515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*/read",
        "Microsoft.Insights/metricAlerts/*/read",
        "Microsoft.Insights/diagnosticSettings/*/read",
        "Microsoft.Insights/metricDefinitions/*/read",
        "Microsoft.Logic/*/read",
        "Microsoft.Logic/workflows/disable/action",
        "Microsoft.Logic/workflows/enable/action",
        "Microsoft.Logic/workflows/validate/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*/read",
        "Microsoft.Web/connections/*/read",
        "Microsoft.Web/customApis/*/read",
        "Microsoft.Web/serverFarms/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="identity"></a>Tożsamość


### <a name="managed-identity-contributor"></a>Współautor tożsamości zarządzanej

Tworzenie, odczytywanie, aktualizowanie i usuwanie tożsamości przypisanej przez użytkownika

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/read | Pobiera istniejącą tożsamość przypisaną użytkownikowi |
> | Microsoft.ManagedIdentity/userAssignedIdentities/write | Tworzy nową tożsamość przypisaną użytkownikowi lub aktualizuje znaczniki skojarzone z istniejącą tożsamością przypisaną przez użytkownika |
> | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Usuwa istniejącą tożsamość przypisaną użytkownikowi |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, Read, Update, and Delete User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "name": "e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/write",
        "Microsoft.ManagedIdentity/userAssignedIdentities/delete",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-identity-operator"></a>Operator tożsamości zarządzanej

Odczyt i przypisanie tożsamości przypisanej użytkownikowi

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and Assign User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f1a07417-d97a-45cb-824c-7a7467783830",
  "name": "f1a07417-d97a-45cb-824c-7a7467783830",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="security"></a>Zabezpieczenia


### <a name="azure-sentinel-contributor"></a>Współautor wartownika platformy Azure

Współautor wartownika platformy Azure

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.SecurityInsights/* |  |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Szukaj za pomocą nowego silnika. |
> | Microsoft.OperationalInsights/workspaces/*/read | Wyświetlanie danych analizy dzienników |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* |  |
> | Microsoft.OperationsManagement/solutions/read | Uzyskaj wyjście z rozwiązania OMS |
> | Microsoft.OperationalInsights/workspaces/query/read | Uruchamianie kwerend nad danymi w obszarze roboczym |
> | Microsoft.OperationalInsights/workspaces/query/*/read |  |
> | Microsoft.OperationalInsights/workspaces/dataSources/read | Pobierz źródła danych w obszarze roboczym. |
> | Microsoft.Insights/skoroszyty/* |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Contributor",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ab8e14d6-4a74-4a29-9ba8-549422addade",
  "name": "ab8e14d6-4a74-4a29-9ba8-549422addade",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-reader"></a>Czytnik wartownika platformy Azure

Czytnik wartownika platformy Azure

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.SecurityInsights/*/odczyt |  |
> | Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action | Sprawdzanie autoryzacji i licencji użytkownika |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Szukaj za pomocą nowego silnika. |
> | Microsoft.OperationalInsights/workspaces/*/read | Wyświetlanie danych analizy dzienników |
> | Microsoft.OperationalInsights/workspaces/LinkedServices/read | Uzyskaj połączone usługi w danym obszarze roboczym. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/read | Pobiera zapisane zapytanie wyszukiwania |
> | Microsoft.OperationsManagement/solutions/read | Uzyskaj wyjście z rozwiązania OMS |
> | Microsoft.OperationalInsights/workspaces/query/read | Uruchamianie kwerend nad danymi w obszarze roboczym |
> | Microsoft.OperationalInsights/workspaces/query/*/read |  |
> | Microsoft.OperationalInsights/workspaces/dataSources/read | Pobierz źródła danych w obszarze roboczym. |
> | Microsoft.Insights/skoroszyty/odczyt | Czytanie skoroszytu |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "name": "8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/LinkedServices/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-responder"></a>Obiekt odpowiadający usługi Azure Sentinel

Obiekt odpowiadający usługi Azure Sentinel

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.SecurityInsights/*/odczyt |  |
> | Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action | Sprawdzanie autoryzacji i licencji użytkownika |
> | Microsoft.SecurityInsights/cases/* |  |
> | Microsoft.SecurityInsights/incidents/* |  |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Szukaj za pomocą nowego silnika. |
> | Microsoft.OperationalInsights/workspaces/*/read | Wyświetlanie danych analizy dzienników |
> | Microsoft.OperationalInsights/workspaces/dataSources/read | Pobierz źródła danych w obszarze roboczym. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/read | Pobiera zapisane zapytanie wyszukiwania |
> | Microsoft.OperationsManagement/solutions/read | Uzyskaj wyjście z rozwiązania OMS |
> | Microsoft.OperationalInsights/workspaces/query/read | Uruchamianie kwerend nad danymi w obszarze roboczym |
> | Microsoft.OperationalInsights/workspaces/query/*/read |  |
> | Microsoft.OperationalInsights/workspaces/dataSources/read | Pobierz źródła danych w obszarze roboczym. |
> | Microsoft.Insights/skoroszyty/odczyt | Czytanie skoroszytu |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Responder",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "name": "3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
        "Microsoft.SecurityInsights/cases/*",
        "Microsoft.SecurityInsights/incidents/*",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Responder",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-contributor"></a>Współautor magazynu kluczy

Umożliwia zarządzanie magazynami kluczy, ale nie ma do nich dostępu.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.KeyVault/* |  |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | Microsoft.KeyVault/locations/deletedVaults/purge/action | Czyszczenie nietrwale usuniętego magazynu kluczy |
> | Microsoft.KeyVault/hsmPools/* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage key vaults, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f25e0fa2-a7c8-4377-a976-54943a77a395",
  "name": "f25e0fa2-a7c8-4377-a976-54943a77a395",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.KeyVault/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.KeyVault/locations/deletedVaults/purge/action",
        "Microsoft.KeyVault/hsmPools/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-admin"></a>Administrator zabezpieczeń

Może wyświetlać zasady zabezpieczeń, wyświetlać stany zabezpieczeń, edytować zasady zabezpieczeń, wyświetlać alerty i zalecenia, odrzucać alerty i zalecenia.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Authorization/policyAssignments/* | Tworzenie przypisań zasad i zarządzanie nimi |
> | Microsoft.Authorization/policyDefinitions/* | Tworzenie definicji zasad i zarządzanie nimi |
> | Microsoft.Authorization/policySetDefinitions/* | Tworzenie zestawów zasad i zarządzanie nimi |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.Management/managementGrupy/odczyt | Listy grup zarządzania dla uwierzytelnionego użytkownika. |
> | Microsoft.operationalInsights/workspaces/*/read | Wyświetlanie danych analizy dzienników |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Security/* | Tworzenie składników i zasad zabezpieczeń oraz zarządzanie nimi |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Admin Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "name": "fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Authorization/policyAssignments/*",
        "Microsoft.Authorization/policyDefinitions/*",
        "Microsoft.Authorization/policySetDefinitions/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-assessment-contributor"></a>Współautor oceny bezpieczeństwa

Umożliwia wypychanie ocen do centrum zabezpieczeń

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Security/assessments/write | Tworzenie lub aktualizowanie ocen zabezpieczeń w ramach subskrypcji |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you push assessments to Security Center",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "name": "612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Security/assessments/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Assessment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-manager-legacy"></a>Menedżer zabezpieczeń (starsza wersja)

Jest to starsza rola. Zamiast tego użyj administratora zabezpieczeń.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.ClassicCompute/*/odczyt | Odczytywanie informacji o konfiguracji klasycznych maszyn wirtualnych |
> | Microsoft.ClassicCompute/virtualMachines/*/write | Konfiguracja zapisu dla klasycznych maszyn wirtualnych |
> | Microsoft.ClassicNetwork/*/odczyt | Odczytywanie informacji o konfiguracji sieci klasycznej |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Security/* | Tworzenie składników i zasad zabezpieczeń oraz zarządzanie nimi |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "This is a legacy role. Please use Security Administrator instead",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "name": "e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/*/read",
        "Microsoft.ClassicCompute/virtualMachines/*/write",
        "Microsoft.ClassicNetwork/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Manager (Legacy)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-reader"></a>Czytelnik zabezpieczeń

Może wyświetlać zalecenia i alerty, wyświetlać zasady zabezpieczeń, wyświetlać stany zabezpieczeń, ale nie może wprowadzać zmian.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.operationalInsights/workspaces/*/read | Wyświetlanie danych analizy dzienników |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Security/*/read | Odczytywanie składników i zasad zabezpieczeń |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | Microsoft.Management/managementGrupy/odczyt | Listy grup zarządzania dla uwierzytelnionego użytkownika. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "name": "39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*/read",
        "Microsoft.Support/*",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="devops"></a>DevOps


### <a name="devtest-labs-user"></a>Użytkownik DevTest Labs

Umożliwia łączenie, uruchamianie, ponowne uruchamianie i zamykanie maszyn wirtualnych w laboratoriach devtest platformy Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Compute/availabilitySets/read | Pobierz właściwości zestawu dostępności |
> | Microsoft.Compute/virtualMachines/*/read | Odczytywanie właściwości maszyny wirtualnej (rozmiary maszyn wirtualnych, stan środowiska uruchomieniowego, rozszerzenia maszyn wirtualnych itp.) |
> | Microsoft.Compute/virtualMachines/deallocate/action | Wyłącza maszynę wirtualną i zwalnia zasoby obliczeniowe |
> | Microsoft.Compute/virtualMachines/read | Pobierz właściwości maszyny wirtualnej |
> | Microsoft.Compute/virtualMachines/restart/action | Uruchamia ponownie maszynę wirtualną |
> | Microsoft.Compute/virtualMachines/start/action | Uruchamia maszynę wirtualną |
> | Microsoft.DevTestLab/*/odczyt | Odczytywanie właściwości laboratorium |
> | Microsoft.DevTestLab/labs/claimAnyVm/action | Odbierz losową maszynę wirtualną z roszczeniem w laboratorium. |
> | Microsoft.DevTestLab/labs/createEnvironment/action | Tworzenie maszyn wirtualnych w laboratorium. |
> | Microsoft.DevTestLab/labs/ensureObywającProfil/akcja | Upewnij się, że bieżący użytkownik ma prawidłowy profil w laboratorium. |
> | Microsoft.DevTestLab/labs/formulas/delete | Usuwanie formuł. |
> | Microsoft.DevTestLab/labs/formulas/read | Odczytywanie formuł. |
> | Microsoft.DevTestLab/labs/formulas/write | Dodawanie lub modyfikowanie formuł. |
> | Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Ocenia zasady laboratorium. |
> | Microsoft.DevTestLab/labs/virtualMachines/claim/action | Przejęcie na własność istniejącej maszyny wirtualnej |
> | Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action | Wyświetla odpowiednie harmonogramy start/stop, jeśli takie istnieją. |
> | Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action | Pobiera ciąg reprezentujący zawartość pliku RDP dla maszyny wirtualnej |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Łączy pulę adresów wewnętrznej bazy danych modułu równoważenia obciążenia. Nie czujny. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Łączy przychodzących reguły nat modułu równoważenia obciążenia. Nie czujny. |
> | Microsoft.Network/networkInterfaces/*/read | Odczytanie właściwości interfejsu sieciowego (na przykład wszystkich modułów równoważenia obciążenia, których częścią jest interfejs sieciowy) |
> | Sieć Microsoft.Network/networkInterfaces/join/action | Łączy maszynę wirtualną z interfejsem sieciowym. Nie czujny. |
> | Microsoft.Network/networkInterfaces/read | Pobiera definicję interfejsu sieciowego.  |
> | Microsoft.Network/networkInterfaces/write | Tworzy interfejs sieciowy lub aktualizuje istniejący interfejs sieciowy.  |
> | Microsoft.Network/publicIPAddresses/*/read | Odczytywanie właściwości publicznego adresu IP |
> | Microsoft.Network/publicIPAddresses/join/action | Dołącza do publicznego adresu IP. Nie czujny. |
> | Microsoft.Network/publicIPAddresses/read | Pobiera definicję adresu IP publicznych. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Dołącza do sieci wirtualnej. Nie czujny. |
> | Zasoby firmy Microsoft/wdrożenia/operacje/odczyt | Pobiera lub wyświetla listę operacji wdrażania. |
> | Zasoby firmy Microsoft/wdrożenia/odczyt | Pobiera lub wyświetla listy wdrożeń. |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Magazyn/magazyn microsoftKonserwuje/listKeys/akcja | Zwraca klucze dostępu dla określonego konta magazynu. |
> | **NotActions** |  |
> | Microsoft.Compute/virtualMachines/vmSizes/read | Listy dostępnych rozmiarów maszyny wirtualnej można zaktualizować do |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you connect, start, restart, and shutdown your virtual machines in your Azure DevTest Labs.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/76283e04-6283-4c54-8f91-bcf1374a3c64",
  "name": "76283e04-6283-4c54-8f91-bcf1374a3c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/read",
        "Microsoft.Compute/virtualMachines/*/read",
        "Microsoft.Compute/virtualMachines/deallocate/action",
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Compute/virtualMachines/restart/action",
        "Microsoft.Compute/virtualMachines/start/action",
        "Microsoft.DevTestLab/*/read",
        "Microsoft.DevTestLab/labs/claimAnyVm/action",
        "Microsoft.DevTestLab/labs/createEnvironment/action",
        "Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action",
        "Microsoft.DevTestLab/labs/formulas/delete",
        "Microsoft.DevTestLab/labs/formulas/read",
        "Microsoft.DevTestLab/labs/formulas/write",
        "Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action",
        "Microsoft.DevTestLab/labs/virtualMachines/claim/action",
        "Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action",
        "Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/networkInterfaces/*/read",
        "Microsoft.Network/networkInterfaces/join/action",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/publicIPAddresses/*/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listKeys/action"
      ],
      "notActions": [
        "Microsoft.Compute/virtualMachines/vmSizes/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DevTest Labs User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="lab-creator"></a>Twórca laboratorium

Umożliwia tworzenie, zarządzanie, usuwanie zarządzanych laboratoriów w ramach kont laboratorium Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.LabServices/labKonta/*/odczyt |  |
> | Microsoft.LabServices/labKonta/createLab/akcja | Utwórz laboratorium na koncie laboratorium. |
> | Microsoft.LabServices/labKonta/rozmiary/getRegionalDostępność/akcja |  |
> | Microsoft.LabServices/labKonta/getRegionalDostępność/działanie | Uzyskaj informacje o dostępności regionalnej dla każdej kategorii rozmiarów skonfigurowanych na koncie laboratorium |
> | Microsoft.LabServices/labKonta/getPricingAndDostępność/akcja | Uzyskaj ceny i dostępność kombinacji rozmiarów, lokalizacji geograficznych i systemów operacyjnych dla konta laboratorium. |
> | Microsoft.LabServices/labKonta/getRestrictionsAndUsage/action | Uzyskaj podstawowe ograniczenia i użycie dla tej subskrypcji |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create, manage, delete your managed labs under your Azure Lab Accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "name": "b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.LabServices/labAccounts/*/read",
        "Microsoft.LabServices/labAccounts/createLab/action",
        "Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action",
        "Microsoft.LabServices/labAccounts/getRegionalAvailability/action",
        "Microsoft.LabServices/labAccounts/getPricingAndAvailability/action",
        "Microsoft.LabServices/labAccounts/getRestrictionsAndUsage/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Lab Creator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="monitor"></a>Monitorowanie


### <a name="application-insights-component-contributor"></a>Współautor składnika usługi Application Insights

Może zarządzać składnikami usługi Application Insights

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie klasycznych reguł alertów i zarządzanie nimi |
> | Microsoft.Insights/metricAlerts/* | Tworzenie nowych reguł alertów i zarządzanie nimi |
> | Microsoft.Insights/składniki/* | Tworzenie składników aplikacji Insights i zarządzanie nimi |
> | Microsoft.Insights/webtests/* | Tworzenie testów sieci Web aplikacji Insights i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage Application Insights components",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e",
  "name": "ae349356-3a1b-4a5e-921d-050484c6347e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/webtests/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Component Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="application-insights-snapshot-debugger"></a>Debuger migawek usługi Application Insights

Daje użytkownikowi uprawnienia do wyświetlania i pobierania migawek debugowania zebranych za pomocą debugera migawek usługi Application Insights. Należy zauważyć, że te uprawnienia nie są uwzględniane w [roli właściciela](#owner) lub [współautora.](#contributor)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.Insights/components/*/read |  |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives user permission to use Application Insights Snapshot Debugger features",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "name": "08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Snapshot Debugger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-contributor"></a>Współautor monitorowania

Może odczytywać wszystkie dane monitorowania i edytować ustawienia monitorowania. Zobacz też [Wprowadzenie do ról, uprawnień i zabezpieczeń w usłudze Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | */odczyt | Odczytywanie zasobów wszystkich typów, z wyjątkiem wpisów tajnych. |
> | Microsoft.AlertsManiuł/Alerty/* |  |
> | Microsoft.AlertsManagement/alertsSummary/* |  |
> | Microsoft.Insights/actiongroups/* |  |
> | Microsoft.Insights/activityLogAlerts/* |  |
> | Microsoft.Insights/AlertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.Insights/składniki/* | Tworzenie składników aplikacji Insights i zarządzanie nimi |
> | Microsoft.Insights/DiagnostykaStawienia/* | Tworzy, aktualizuje lub odczytuje ustawienie diagnostyczne dla analysis server |
> | Microsoft.Insights/eventtypes/* | Lista zdarzeń dziennika aktywności (zdarzenia zarządzania) w subskrypcji. To uprawnienie ma zastosowanie zarówno do programu, jak i dostępu do dziennika aktywności w portalu. |
> | Microsoft.Insights/LogDefinitions/* | To uprawnienie jest niezbędne dla użytkowników, którzy potrzebują dostępu do dzienników aktywności za pośrednictwem portalu. Lista kategorii dziennika w dzienniku aktywności. |
> | Microsoft.Insights/metricalerts/* |  |
> | Microsoft.Insights/MetricDefinitions/* | Odczyt definicji metryk (lista dostępnych typów metryk dla zasobu). |
> | Microsoft.Insights/Metryki/* | Odczyt metryki zasobu. |
> | Microsoft.Insights/Zarejestruj się/Akcja | Rejestrowanie dostawcy usługi Microsoft Insights |
> | Microsoft.Insights/scheduledqueryrules/* |  |
> | Microsoft.Insights/webtests/* | Tworzenie testów sieci Web aplikacji Insights i zarządzanie nimi |
> | Microsoft.Insights/skoroszyty/* |  |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Pakiety rozwiązań analizy do odczytu/zapisu/usuwania dzienników. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* | Odczyt/zapis/usuwanie wyszukiwań z analizą dzienników. |
> | Microsoft.OperationalInsights/workspaces/search/action | Wykonuje kwerendę wyszukiwania |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Pobiera klucze udostępnione dla obszaru roboczego. Te klucze są używane do łączenia agentów usługi Microsoft Operational Insights z obszarem roboczym. |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Odczyt/zapis/usuwanie konfiguracji wglądu w dane magazynu usługi analizy dzienników. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | Microsoft.WorkloadMonitor/monitory/* |  |
> | Microsoft.WorkloadMonitor/notificationSettings/* |  |
> | Microsoft.AlertsManagement/smartDetectorAlertRules/* |  |
> | Microsoft.AlertsManagement/actionRules/* |  |
> | Microsoft.AlertsManagement/smartGroups/* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data and update monitoring settings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "name": "749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.AlertsManagement/alerts/*",
        "Microsoft.AlertsManagement/alertsSummary/*",
        "Microsoft.Insights/actiongroups/*",
        "Microsoft.Insights/activityLogAlerts/*",
        "Microsoft.Insights/AlertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/DiagnosticSettings/*",
        "Microsoft.Insights/eventtypes/*",
        "Microsoft.Insights/LogDefinitions/*",
        "Microsoft.Insights/metricalerts/*",
        "Microsoft.Insights/MetricDefinitions/*",
        "Microsoft.Insights/Metrics/*",
        "Microsoft.Insights/Register/Action",
        "Microsoft.Insights/scheduledqueryrules/*",
        "Microsoft.Insights/webtests/*",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.OperationalInsights/workspaces/intelligencepacks/*",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.OperationalInsights/workspaces/sharedKeys/action",
        "Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*",
        "Microsoft.Support/*",
        "Microsoft.WorkloadMonitor/monitors/*",
        "Microsoft.WorkloadMonitor/notificationSettings/*",
        "Microsoft.AlertsManagement/smartDetectorAlertRules/*",
        "Microsoft.AlertsManagement/actionRules/*",
        "Microsoft.AlertsManagement/smartGroups/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-metrics-publisher"></a>Monitorowanie metryki Wydawca

Włącza publikowanie metryk w zasobach platformy Azure

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Insights/Zarejestruj się/Akcja | Rejestrowanie dostawcy usługi Microsoft Insights |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | Microsoft.Insights/Metryki/Zapis | Zapis metryki |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Enables publishing metrics against Azure resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c390055eb",
  "name": "3913510d-42f4-4e42-8a64-420c390055eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/Register/Action",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Insights/Metrics/Write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Metrics Publisher",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-reader"></a>Czytnik monitoringu

Może odczytywać wszystkie dane z monitoringu (metryki, dzienniki itp.). Zobacz też [Wprowadzenie do ról, uprawnień i zabezpieczeń w usłudze Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | */odczyt | Odczytywanie zasobów wszystkich typów, z wyjątkiem wpisów tajnych. |
> | Microsoft.OperationalInsights/workspaces/search/action | Wykonuje kwerendę wyszukiwania |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "name": "43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-contributor"></a>Współautor skoroszytu

Można zapisywać skoroszyty udostępnione.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Insights/skoroszyty/zapis | Tworzenie lub aktualizowanie skoroszytu |
> | Microsoft.Insights/skoroszyty/delete | Usuwanie skoroszytu |
> | Microsoft.Insights/skoroszyty/odczyt | Czytanie skoroszytu |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can save shared workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "name": "e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/workbooks/write",
        "Microsoft.Insights/workbooks/delete",
        "Microsoft.Insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-reader"></a>Czytnik skoroszytu

Może czytać skoroszyty.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | microsoft.insights/zeszyty/odczyt | Czytanie skoroszytu |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "name": "b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "permissions": [
    {
      "actions": [
        "microsoft.insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="management--governance"></a>Zarządzanie + zarządzanie


### <a name="automation-job-operator"></a>Operator zadań automatyzacji

Tworzenie zadań i zarządzanie nimi przy użyciu śmięty automatyzacji.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Automatyzacja/automatyzacja Microsoft.Automation/automationKonta/hybrydaRunbookWorkerGroups/read | Odczytuje zasoby procesu roboczego egoisty hybrydowego |
> | Automatyzacja/automatyzacja Microsoft.AutomationAccounts/jobs/read | Pobiera zadanie automatyzacji platformy Azure |
> | Automatyzacja firmy Microsoft/automatyzacjaKonta/zadania/wznowienie/akcja | Wznawia zadanie usługi Azure Automation |
> | Automatyzacja firmy Microsoft/automatyzacjaKonta/zadania/zatrzymanie/akcja | Zatrzymuje zadanie usługi Azure Automation |
> | Automatyzacja firmy Microsoft/automationKonta/zadania/strumienie/odczyt | Pobiera strumień zadań usługi Azure Automation |
> | Automatyzacja firmy Microsoft.Automation/automationKonta/zadania/wstrzymanie/działanie | Zawiesza zadanie usługi Azure Automation |
> | Automatyzacja/automatyzacja Microsoft.AutomationAccounts/jobs/write | Tworzy zadanie usługi Azure Automation |
> | Automatyzacja firmy Microsoft/automatyzacjaKonta/zadania/dane wyjściowe/odczyt | Pobiera dane wyjściowe zadania |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and Manage Jobs using Automation Runbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "name": "4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Job Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-operator"></a>Operator usługi

Operatorzy automatyzacji mogą uruchamiać, zatrzymywać, zawieszać i wznawiać zadania

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Automatyzacja/automatyzacja Microsoft.Automation/automationKonta/hybrydaRunbookWorkerGroups/read | Odczytuje zasoby procesu roboczego egoisty hybrydowego |
> | Automatyzacja/automatyzacja Microsoft.AutomationAccounts/jobs/read | Pobiera zadanie automatyzacji platformy Azure |
> | Automatyzacja firmy Microsoft/automatyzacjaKonta/zadania/wznowienie/akcja | Wznawia zadanie usługi Azure Automation |
> | Automatyzacja firmy Microsoft/automatyzacjaKonta/zadania/zatrzymanie/akcja | Zatrzymuje zadanie usługi Azure Automation |
> | Automatyzacja firmy Microsoft/automationKonta/zadania/strumienie/odczyt | Pobiera strumień zadań usługi Azure Automation |
> | Automatyzacja firmy Microsoft.Automation/automationKonta/zadania/wstrzymanie/działanie | Zawiesza zadanie usługi Azure Automation |
> | Automatyzacja/automatyzacja Microsoft.AutomationAccounts/jobs/write | Tworzy zadanie usługi Azure Automation |
> | Automatyzacja Microsoft.Automation/automationKonta/zadaniaSześci/odczyt | Pobiera harmonogram zadań usługi Azure Automation |
> | Automatyzacja/automatyzacja Microsoft.Automation/automationKonta/zadaniaSześci/zapis | Tworzy harmonogram zadań usługi Azure Automation |
> | Automatyzacja/automatyzacja Microsoft.Automation/automationKonta/linkedWorkspace/read | Pobiera obszar roboczy połączony z kontem automatyzacji |
> | Automatyzacja/automatyzacja Microsoft.AutomationKonta/odczyt | Pobiera konto usługi Azure Automation |
> | Automatyzacja/automatyzacja Microsoft.AutomationAccounts/runbooks/read | Pobiera element runbook usługi Azure Automation |
> | Automatyzacja/automatyzacja Microsoft.AutomationAccounts/schedules/read | Pobiera zasób harmonogramu usługi Azure Automation |
> | Automatyzacja/automatyzacja Microsoft.AutomationAccounts/schedules/write | Tworzy lub aktualizuje zasób harmonogramu usługi Azure Automation |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Automatyzacja firmy Microsoft/automatyzacjaKonta/zadania/dane wyjściowe/odczyt | Pobiera dane wyjściowe zadania |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Automation Operators are able to start, stop, suspend, and resume jobs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d3881f73-407a-4167-8283-e981cbba0404",
  "name": "d3881f73-407a-4167-8283-e981cbba0404",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobSchedules/read",
        "Microsoft.Automation/automationAccounts/jobSchedules/write",
        "Microsoft.Automation/automationAccounts/linkedWorkspace/read",
        "Microsoft.Automation/automationAccounts/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Automation/automationAccounts/schedules/read",
        "Microsoft.Automation/automationAccounts/schedules/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-runbook-operator"></a>Operator systemu runbook automatyzacji

Przeczytaj właściwości elementu runbook — aby móc tworzyć zadania elementu runbook.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Automatyzacja/automatyzacja Microsoft.AutomationAccounts/runbooks/read | Pobiera element runbook usługi Azure Automation |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read Runbook properties - to be able to create Jobs of the runbook.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "name": "5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Runbook Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-onboarding"></a>Dołączanie do połączonego komputera platformy Azure

Może dołączać połączone maszyny platformy Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.HybridCompute/maszyny/odczyt | Odczytywanie dowolnych maszyn z systemem Azure Arc |
> | Microsoft.HybridCompute/maszyny/zapis | Zapisuje maszyny łukowe platformy Azure |
> | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | Pobierz przypisanie konfiguracji gościa. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "name": "b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.GuestConfiguration/guestConfigurationAssignments/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-resource-administrator"></a>Administrator zasobów podłączonego komputera platformy Azure

Może odczytywać, zapisywać, usuwać i ponownie dołączać połączone maszyny platformy Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.HybridCompute/maszyny/odczyt | Odczytywanie dowolnych maszyn z systemem Azure Arc |
> | Microsoft.HybridCompute/maszyny/zapis | Zapisuje maszyny łukowe platformy Azure |
> | Microsoft.HybridCompute/maszyny/delete | Usuwa maszyny łukowe platformy Azure |
> | Microsoft.HybridCompute/maszyny/ponowne połączenie/działanie | Ponowne łączenie maszyn z systemem Azure Arc |
> | Microsoft.HybridCompute/maszyny/rozszerzenia/zapis | Instalowanie lub aktualizowanie rozszerzeń łuku platformy Azure |
> | Microsoft.HybridCompute/*/odczyt |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read, write, delete and re-onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cd570a14-e51a-42ad-bac8-bafd67325302",
  "name": "cd570a14-e51a-42ad-bac8-bafd67325302",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.HybridCompute/machines/delete",
        "Microsoft.HybridCompute/machines/reconnect/action",
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.HybridCompute/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Resource Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="billing-reader"></a>Czytelnik rozliczeń

Umożliwia dostęp do odczytu danych rozliczeniowych

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Billing/*/odczyt | Przeczytaj informacje rozliczeniowe |
> | Microsoft.Commerce/*/odczyt |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.Management/managementGrupy/odczyt | Listy grup zarządzania dla uwierzytelnionego użytkownika. |
> | Zarządzanie kosztami firmy Microsoft/*/odczyt |  |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to billing data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "name": "fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Billing/*/read",
        "Microsoft.Commerce/*/read",
        "Microsoft.Consumption/*/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Billing Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-contributor"></a>Współautor planu

Może zarządzać definicjami planu, ale nie przypisywać ich.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Blueprint/plany/* | Tworzenie definicji planu lub artefaktów planu i zarządzanie nimi. |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage blueprint definitions, but not assign them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/41077137-e803-4205-871c-5a86e6a753b4",
  "name": "41077137-e803-4205-871c-5a86e6a753b4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprints/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-operator"></a>Operator planu

Można przypisać istniejące opublikowane plany, ale nie można utworzyć nowe plany. Należy zauważyć, że to działa tylko wtedy, gdy przypisanie odbywa się z tożsamością zarządzaną przypisaną przez użytkownika.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Blueprint/blueprintAssignments/* | Tworzenie przypisań planu i zarządzanie nimi. |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can assign existing published blueprints, but cannot create new blueprints. NOTE: this only works if the assignment is done with a user-assigned managed identity.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/437d2ced-4a38-4302-8479-ed2bcb43d090",
  "name": "437d2ced-4a38-4302-8479-ed2bcb43d090",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprintAssignments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-contributor"></a>Współautor zarządzania kosztami

Może wyświetlać koszty i zarządzać konfiguracją kosztów (np. budżety, eksport)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Consumption/* |  |
> | Zarządzanie kosztami firmy Microsoft/* |  |
> | Microsoft.Billing/billingWygody/odczyt |  |
> | Zasoby Firmy Microsoft/subskrypcje/odczyt | Pobiera listę subskrypcji. |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | Microsoft.Advisor/configurations/read | Pobierz konfiguracje |
> | Microsoft.Advisor/recommendations/read | Odczytuje zalecenia |
> | Microsoft.Management/managementGrupy/odczyt | Listy grup zarządzania dla uwierzytelnionego użytkownika. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view costs and manage cost configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/434105ed-43f6-45c7-a02f-909b2ba83430",
  "name": "434105ed-43f6-45c7-a02f-909b2ba83430",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*",
        "Microsoft.CostManagement/*",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-reader"></a>Czytnik zarządzania kosztami

Może wyświetlać dane o kosztach i konfigurację (np. budżety, eksport)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Consumption/*/read |  |
> | Zarządzanie kosztami firmy Microsoft/*/odczyt |  |
> | Microsoft.Billing/billingWygody/odczyt |  |
> | Zasoby Firmy Microsoft/subskrypcje/odczyt | Pobiera listę subskrypcji. |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | Microsoft.Advisor/configurations/read | Pobierz konfiguracje |
> | Microsoft.Advisor/recommendations/read | Odczytuje zalecenia |
> | Microsoft.Management/managementGrupy/odczyt | Listy grup zarządzania dla uwierzytelnionego użytkownika. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view cost data and configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/72fafb9e-0641-4937-9268-a91bfd8191a3",
  "name": "72fafb9e-0641-4937-9268-a91bfd8191a3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-contributor-role"></a>Rola współautora aplikacji zarządzanej

Umożliwia tworzenie zasobów aplikacji zarządzanych.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | */odczyt | Odczytywanie zasobów wszystkich typów, z wyjątkiem wpisów tajnych. |
> | Microsoft.Solutions/applications/* |  |
> | Microsoft.Solutions/register/action | Zarejestruj się w rozwiązaniach. |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/* |  |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for creating managed application resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/641177b8-a67a-45b9-a033-47bc880bb21e",
  "name": "641177b8-a67a-45b9-a033-47bc880bb21e",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/*",
        "Microsoft.Solutions/register/action",
        "Microsoft.Resources/subscriptions/resourceGroups/*",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-operator-role"></a>Rola operatora aplikacji zarządzanej

Umożliwia odczytywanie i wykonywanie akcji w zasobach aplikacji zarządzanych

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | */odczyt | Odczytywanie zasobów wszystkich typów, z wyjątkiem wpisów tajnych. |
> | Microsoft.Solutions/applications/read | Pobiera listę aplikacji. |
> | Microsoft.Solutions/*/akcja |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and perform actions on Managed Application resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "name": "c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/read",
        "Microsoft.Solutions/*/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-applications-reader"></a>Czytnik aplikacji zarządzanych

Umożliwia odczytywanie zasobów w zarządzanej aplikacji i żądanie dostępu JIT.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | */odczyt | Odczytywanie zasobów wszystkich typów, z wyjątkiem wpisów tajnych. |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Solutions/jitRequests/* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read resources in a managed app and request JIT access.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "name": "b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Solutions/jitRequests/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Applications Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-services-registration-assignment-delete-role"></a>Rola usuwania przypisania rejestracji usług zarządzanych

Rola usuwania przypisania rejestracji usług zarządzanych umożliwia użytkownikom dzierżawy zarządzającej usunięcie przypisania rejestracji przypisanego do ich dzierżawy.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.ManagedServices/registrationAssignments/read Microsoft.ManagedServices/registrationAssignments/read Microsoft.ManagedServices/registrationAssignments/read Microsoft. | Pobiera listę przypisań rejestracji usług zarządzanych. |
> | Microsoft.ManagedServices/registrationAssignments/delete Microsoft.ManagedServices/registrationAssignments/delete Microsoft.ManagedServices/registrationAssignments/delete Microsoft. | Usuwa przypisanie rejestracji usług zarządzanych. |
> | Microsoft.ManagedServices/operationStatuss/read | Odczytuje stan operacji zasobu. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Managed Services Registration Assignment Delete Role allows the managing tenant users to delete the registration assignment assigned to their tenant.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/91c1777a-f3dc-4fae-b103-61d183457e46",
  "name": "91c1777a-f3dc-4fae-b103-61d183457e46",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedServices/registrationAssignments/read",
        "Microsoft.ManagedServices/registrationAssignments/delete",
        "Microsoft.ManagedServices/operationStatuses/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Services Registration assignment Delete Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-contributor"></a>Współautor grupy zarządzania

Rola współautora grupy zarządzającej

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Management/managementGrupy/usuń | Usuń grupę zarządzania. |
> | Microsoft.Management/managementGrupy/odczyt | Listy grup zarządzania dla uwierzytelnionego użytkownika. |
> | Microsoft.Management/managementGrupy/subskrypcje/usuń | De-associates subskrypcji z grupy zarządzania. |
> | Microsoft.Management/managementGrupy/subskrypcje/zapis | Kojarzy istniejącą subskrypcję z grupą zarządzania. |
> | Microsoft.Management/managementGrupy/zapis | Tworzenie lub aktualizowanie grupy zarządzania. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Contributor Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "name": "5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/delete",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/delete",
        "Microsoft.Management/managementGroups/subscriptions/write",
        "Microsoft.Management/managementGroups/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-reader"></a>Czytnik grup zarządzania

Rola czytnika grupy zarządzania

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Management/managementGrupy/odczyt | Listy grup zarządzania dla uwierzytelnionego użytkownika. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ac63b705-f282-497d-ac71-919bf39d939d",
  "name": "ac63b705-f282-497d-ac71-919bf39d939d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="new-relic-apm-account-contributor"></a>Nowy współautor konta APM reliktów

Umożliwia zarządzanie kontami i aplikacjami zarządzania wydajnością nowych aplikacji, ale nie ma do nich dostępu.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | NewRelic.APM/konta/* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage New Relic Application Performance Management accounts and applications, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d28c62d-5b37-4476-8438-e587778df237",
  "name": "5d28c62d-5b37-4476-8438-e587778df237",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "NewRelic.APM/accounts/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "New Relic APM Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="policy-insights-data-writer-preview"></a>Moduł zapisujący dane wglądu w zasady (wersja zapoznawcza)

Umożliwia dostęp do odczytu do zasad zasobów i dostęp do zapisu do zdarzeń zasad składnika zasobów.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/policyassignments/read | Uzyskaj informacje o przypisaniu zasad. |
> | Microsoft.Authorization/policydefinitions/read | Uzyskaj informacje o definicji zasad. |
> | Microsoft.Authorization/policysetdefinitions/read | Uzyskaj informacje o definicji zestawu zasad. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | Microsoft.PolicyInsights/checkDataPolitycompliance/action | Sprawdź stan zgodności danego składnika względem zasad danych. |
> | Microsoft.PolicyInsights/policyEvents/logDataEvents/action | Rejestrowanie zdarzeń zasad składnika zasobów. |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to resource policies and write access to resource component policy events.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "name": "66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/policyassignments/read",
        "Microsoft.Authorization/policydefinitions/read",
        "Microsoft.Authorization/policysetdefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.PolicyInsights/checkDataPolicyCompliance/action",
        "Microsoft.PolicyInsights/policyEvents/logDataEvents/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Policy Insights Data Writer (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="resource-policy-contributor"></a>Współautor zasad zasobów

Użytkownicy z uprawnieniami do tworzenia/modyfikowania zasad zasobów, tworzenia biletów pomocy technicznej i odczytywania zasobów/hierarchii.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | */odczyt | Odczytywanie zasobów wszystkich typów, z wyjątkiem wpisów tajnych. |
> | Microsoft.Authorization/policyassignments/* | Tworzenie przypisań zasad i zarządzanie nimi |
> | Microsoft.Autoryzacja/zasadydefinicje/* | Tworzenie definicji zasad i zarządzanie nimi |
> | Microsoft.Authorization/policysetdefinitions/* | Tworzenie zestawów zasad i zarządzanie nimi |
> | Microsoft.PolicyInsights/* |  |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Users with rights to create/modify resource policy, create support ticket and read resources/hierarchy.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/36243c78-bf99-498c-9df9-86d9f8d28608",
  "name": "36243c78-bf99-498c-9df9-86d9f8d28608",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/policyassignments/*",
        "Microsoft.Authorization/policydefinitions/*",
        "Microsoft.Authorization/policysetdefinitions/*",
        "Microsoft.PolicyInsights/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Resource Policy Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-contributor"></a>Współautor odzyskiwania witryny

Umożliwia zarządzanie usługą site recovery z wyjątkiem tworzenia przechowalni i przypisywania ról

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.Network/virtualNetworks/read | Uzyskaj definicję sieci wirtualnej |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp jest operacją wewnętrzną używaną przez usługę |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp jest operacją wewnętrzną używaną przez usługę |
> | Microsoft.RecoveryServices/Vaults/certificates/write | Operacja Aktualizuj certyfikat zasobu aktualizuje certyfikat poświadczeń zasobu/magazynu. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Tworzenie informacji rozszerzonych związanych z magazynem i zarządzanie nimi |
> | Microsoft.RecoveryServices/Vaults/read | Operacja Pobierz przechowalnię pobiera obiekt reprezentujący zasób platformy Azure typu "vault" |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Tworzenie zarejestrowanych tożsamości i zarządzanie nimi |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | Tworzenie lub aktualizowanie ustawień alertu replikacji |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Czytaj dowolne wydarzenia |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/* | Tworzenie sieci szkieletowych replikacji i zarządzanie nimi |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Tworzenie zadań replikacji i zarządzanie nimi |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/* | Tworzenie zasad replikacji i zarządzanie nimi |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | Tworzenie planów odzyskiwania i zarządzanie nimi |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* | Tworzenie konfiguracji magazynu usług Recovery i zarządzanie nim |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Zwraca szczegóły użycia magazynu usług odzyskiwania. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Operacja tokenu przechowalni może służyć do uzyskania tokenu przechowalni dla operacji wewnętrznej bazy danych na poziomie magazynu. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Odczytywanie alertów dotyczących magazynu usług odzyskiwania |
> | Microsoft.RecoveryServices/Vaults/monitoringKonfiguracje/powiadomienieKonfiguracja/odczyt |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Magazyn/magazyn microsoftKonsowania/odczytu | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | Microsoft.RecoveryServices/vaults/replicationOperationStatus/read | Odczytywanie dowolnego stanu operacji replikacji przechowalni |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Site Recovery service except vault creation and role assignment",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "name": "6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/*",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/*",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/*",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/*",
        "Microsoft.RecoveryServices/Vaults/storageConfig/*",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/vaults/replicationOperationStatus/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-operator"></a>Operator odzyskiwania witryny

Umożliwia pracę w pracy awaryjnej i powiększanie po awarii, ale nie wykonywanie innych operacji zarządzania odzyskiwaniem witryny

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.Network/virtualNetworks/read | Uzyskaj definicję sieci wirtualnej |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp jest operacją wewnętrzną używaną przez usługę |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp jest operacją wewnętrzną używaną przez usługę |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operacja Pobierz informacje rozszerzone pobiera rozszerzone informacje obiektu reprezentujące zasób platformy Azure typu ?vault? |
> | Microsoft.RecoveryServices/Vaults/read | Operacja Pobierz przechowalnię pobiera obiekt reprezentujący zasób platformy Azure typu "vault" |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Operacja Pobierz wyniki operacji może być używana, aby uzyskać stan operacji i wynik dla operacji przesłanej asynchronicznie |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Operacja Pobierz kontenery może służyć uzyskać kontenery zarejestrowane dla zasobu. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Przeczytaj dowolne ustawienia alertów |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Czytaj dowolne wydarzenia |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Sprawdza spójność tkaniny |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Przeczytaj dowolne tkaniny |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Ponowne przyłączanie bramy |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Odnów certyfikat dla sieci szkieletowej |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Czytaj dowolne sieci |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Odczytywanie mapowań sieci |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Przeczytaj dowolne kontenery ochronne |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Przeczytaj dowolne elementy podlegające ochronie |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Zastosuj punkt odzyskiwania |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Zatwierdzenie trybu failover |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Planowane przełączanie do trybu failover |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Przeczytaj wszystkie chronione elementy |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Odczytywanie dowolnych punktów odzyskiwania replikacji |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Naprawa replikacji |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | ReProtect chroniony element |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Kontener ochrony przełączników |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Test pracy w trybie failover |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Testowanie oczyszczania trybu failover |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Tryb failover |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Aktualizowanie usługi mobilności |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Odczytywanie mapowań kontenerów ochrony |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Przeczytaj dostawców usług odzyskiwania |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServiceSProviders/refreshProvider/action | Dostawca odświeżania |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Odczytywanie dowolnych klasyfikacji magazynu |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Odczytywanie mapowań klasyfikacji magazynu |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Przeczytaj dowolne vCenters |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Tworzenie zadań replikacji i zarządzanie nimi |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Przeczytaj dowolne zasady |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Plan odzyskiwania zatwierdzania trybu failover |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Planowany plan odzyskiwania trybu failover |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Przeczytaj dowolne plany odzyskiwania |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Planu odzyskiwania ReProtect |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Testowy plan odzyskiwania trybu failover |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Testowy plan odzyskiwania oczyszczania pracy awaryjnej |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Plan odzyskiwania trybu failover |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Odczytywanie alertów dotyczących magazynu usług odzyskiwania |
> | Microsoft.RecoveryServices/Vaults/monitoringKonfiguracje/powiadomienieKonfiguracja/odczyt |  |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Zwraca szczegóły użycia magazynu usług odzyskiwania. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Operacja tokenu przechowalni może służyć do uzyskania tokenu przechowalni dla operacji wewnętrznej bazy danych na poziomie magazynu. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Magazyn/magazyn microsoftKonsowania/odczytu | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you failover and failback but not perform other Site Recovery management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/494ae006-db33-4328-bf46-533a6560a3ca",
  "name": "494ae006-db33-4328-bf46-533a6560a3ca",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-reader"></a>Czytnik odzyskiwania witryny

Umożliwia wyświetlanie stanu odzyskiwania witryny, ale nie wykonywanie innych operacji zarządzania

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp jest operacją wewnętrzną używaną przez usługę |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operacja Pobierz informacje rozszerzone pobiera rozszerzone informacje obiektu reprezentujące zasób platformy Azure typu ?vault? |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Pobiera alerty dla magazynu usług odzyskiwania. |
> | Microsoft.RecoveryServices/Vaults/monitoringKonfiguracje/powiadomienieKonfiguracja/odczyt |  |
> | Microsoft.RecoveryServices/Vaults/read | Operacja Pobierz przechowalnię pobiera obiekt reprezentujący zasób platformy Azure typu "vault" |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Operacja Pobierz wyniki operacji może być używana, aby uzyskać stan operacji i wynik dla operacji przesłanej asynchronicznie |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Operacja Pobierz kontenery może służyć uzyskać kontenery zarejestrowane dla zasobu. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Przeczytaj dowolne ustawienia alertów |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Czytaj dowolne wydarzenia |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Przeczytaj dowolne tkaniny |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Czytaj dowolne sieci |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Odczytywanie mapowań sieci |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Przeczytaj dowolne kontenery ochronne |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Przeczytaj dowolne elementy podlegające ochronie |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Przeczytaj wszystkie chronione elementy |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Odczytywanie dowolnych punktów odzyskiwania replikacji |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Odczytywanie mapowań kontenerów ochrony |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Przeczytaj dostawców usług odzyskiwania |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Odczytywanie dowolnych klasyfikacji magazynu |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Odczytywanie mapowań klasyfikacji magazynu |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Przeczytaj dowolne vCenters |
> | Microsoft.RecoveryServices/vaults/replicationJobs/read | Przeczytaj dowolne oferty pracy |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Przeczytaj dowolne zasady |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Przeczytaj dowolne plany odzyskiwania |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Zwraca szczegóły użycia magazynu usług odzyskiwania. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Operacja tokenu przechowalni może służyć do uzyskania tokenu przechowalni dla operacji wewnętrznej bazy danych na poziomie magazynu. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view Site Recovery status but not perform other management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "name": "dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/read",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="support-request-contributor"></a>Współautor żądania pomocy technicznej

Umożliwia tworzenie żądań pomocy technicznej i zarządzanie nimi

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create and manage Support requests",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "name": "cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Support Request Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="tag-contributor"></a>Współautor tagu

Umożliwia zarządzanie tagami w jednostkach bez zapewniania dostępu do samych encji.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/zasoby/odczyt | Pobiera zasoby dla grupy zasobów. |
> | Zasoby Firmy Microsoft/subskrypcje/zasoby/odczyt | Pobiera zasoby subskrypcji. |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | Zasoby/tagi firmy Microsoft/* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage tags on entities, without providing access to the entities themselves.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "name": "4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
        "Microsoft.Resources/subscriptions/resources/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/tags/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Tag Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="other"></a>Inne


### <a name="biztalk-contributor"></a>Współautor systemu BizTalk

Umożliwia zarządzanie usługami BizTalk, ale nie ma do nich dostępu.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Usługi Microsoft.BizTalkServices/BizTalk/* | Tworzenie usług systemu BizTalk i zarządzanie nimi |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage BizTalk services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "name": "5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BizTalkServices/BizTalk/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "BizTalk Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="scheduler-job-collections-contributor"></a>Współautor kolekcji zadań harmonogramu

Umożliwia zarządzanie kolekcjami zadań harmonogramu, ale nie ma do nich dostępu.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów aplikacji Insights i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | Zasoby/wdrożenia firmy Microsoft/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Scheduler/jobcollections/* | Tworzenie kolekcji zadań i zarządzanie nimi |
> | Pomoc techniczna firmy Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataAkcje** |  |
> | *brak* |  |
> | **NotDataACtions (NotDataActions)** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Scheduler job collections, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "name": "188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Scheduler/jobcollections/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Scheduler Job Collections Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="next-steps"></a>Następne kroki

- [Dopasowywać dostawcę zasobów do usługi](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Role niestandardowe platformy Azure](custom-roles.md)
- [Uprawnienia w usłudze Azure Security Center](../security-center/security-center-permissions.md)
