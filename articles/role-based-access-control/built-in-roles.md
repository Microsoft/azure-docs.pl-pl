---
title: Role wbudowane platformy Azure — RBAC Azure
description: W tym artykule opisano wbudowane role platformy Azure na potrzeby kontroli dostępu opartej na rolach (RBAC) na platformie Azure. Zawiera listę akcji, nienaruszonych, akcji dataactions i NotDataActions.
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
ms.date: 05/04/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: b6587ca113319c266d2335c9cbb154e0ecc4afa1
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82838743"
---
# <a name="azure-built-in-roles"></a>Wbudowane role platformy Azure

[Kontrola dostępu oparta na rolach na platformie Azure (RBAC)](overview.md) ma kilka wbudowanych ról platformy Azure, które można przypisać do użytkowników, grup, podmiotów usługi i tożsamości zarządzanych. Przypisania ról są sposobem kontroli dostępu do zasobów platformy Azure. Jeśli wbudowane role nie są zgodne z konkretnymi potrzebami organizacji, możesz utworzyć własne [role niestandardowe platformy Azure](custom-roles.md).

Ten artykuł zawiera listę wbudowanych ról platformy Azure, które są zawsze rozwijane. Aby uzyskać najnowsze role, użyj funkcji [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) lub [AZ role Definition list](/cli/azure/role/definition#az-role-definition-list). Jeśli szukasz ról administratorów dla Azure Active Directory (Azure AD), zobacz [uprawnienia roli administratora w programie Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="all"></a>Wszyscy

W poniższej tabeli przedstawiono krótki opis i unikatowy identyfikator każdej wbudowanej roli. `Actions`Wybierz nazwę roli, aby wyświetlić listę `NotActions`,, `DataActions`i `NotDataActions` dla każdej roli. Aby uzyskać informacje o tym, co oznaczają te działania oraz o sposobie ich zastosowania do płaszczyzny zarządzania i danych, zobacz [Opis ról roli platformy Azure](role-definitions.md).


> [!div class="mx-tableFixed"]
> | Wbudowana rola | Opis | ID |
> | --- | --- | --- |
> | **Ogólne** |  |  |
> | [Współautor](#contributor) | Umożliwia zarządzanie wszystko z wyjątkiem udzielania dostępu do zasobów. | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | [Właściciel](#owner) | Umożliwia zarządzanie wszystko, w tym dostęp do zasobów. | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | [Czytelnik](#reader) | Umożliwia wyświetlenie wszystkiego, ale nie wprowadza żadnych zmian. | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | [Administrator dostępu użytkowników](#user-access-administrator) | Umożliwia zarządzanie dostępem użytkowników do zasobów platformy Azure. | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Wystąpienia obliczeniowe** |  |  |
> | [Współautor klasycznej maszyny wirtualnej](#classic-virtual-machine-contributor) | Umożliwia zarządzanie klasycznymi maszynami wirtualnymi, ale nie umożliwia uzyskiwania do nich dostępu, a nie do sieci wirtualnej ani konta magazynu, z którymi są połączone. | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | [Logowanie administratora maszyny wirtualnej](#virtual-machine-administrator-login) | Wyświetl Virtual Machines w portalu i zaloguj się jako administrator | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | [Współautor maszyny wirtualnej](#virtual-machine-contributor) | Umożliwia zarządzanie maszynami wirtualnymi, ale nie dostęp do nich, a nie do sieci wirtualnej ani konta magazynu, z którymi są połączone. | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | [Logowanie użytkownika maszyny wirtualnej](#virtual-machine-user-login) | Wyświetl Virtual Machines w portalu i zaloguj się jako zwykły użytkownik. | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Networking** |  |  |
> | [Współautor punktu końcowego usługi CDN](#cdn-endpoint-contributor) | Może zarządzać punktami końcowymi usługi CDN, ale nie może udzielić dostępu innym użytkownikom. | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | [Czytnik punktu końcowego usługi CDN](#cdn-endpoint-reader) | Może wyświetlać punkty końcowe usługi CDN, ale nie może wprowadzać zmian. | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | [Współautor profilu CDN](#cdn-profile-contributor) | Może zarządzać profilami sieci CDN i ich punktami końcowymi, ale nie może udzielać dostępu innym użytkownikom. | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | [Czytnik profilu CDN](#cdn-profile-reader) | Może wyświetlać profile usługi CDN i ich punkty końcowe, ale nie może wprowadzać zmian. | 8f96442b-4075-438f-813d-ad51ab4019af |
> | [Współautor klasycznej sieci](#classic-network-contributor) | Umożliwia zarządzanie sieciami klasycznymi, ale nie umożliwia uzyskiwania do nich dostępu. | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | [Współautor strefy DNS](#dns-zone-contributor) | Umożliwia zarządzanie strefami i zestawami rekordów DNS w Azure DNS, ale nie pozwala na kontrolowanie dostępu do nich. | befefa01-2a29-4197-83a8-272ff33ce314 |
> | [Współautor sieci](#network-contributor) | Umożliwia zarządzanie sieciami, ale nie umożliwia uzyskiwania do nich dostępu. | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | [Współautor Traffic Manager](#traffic-manager-contributor) | Umożliwia zarządzanie profilami Traffic Manager, ale nie pozwala na kontrolowanie użytkowników, którzy mają do nich dostęp. | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Storage** |  |  |
> | [Współautor avere](#avere-contributor) | Można utworzyć klaster avere vFXT i zarządzać nim. | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | [Operator avere](#avere-operator) | Używane przez klaster avere vFXT do zarządzania klastrem | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | [Współautor kopii zapasowej](#backup-contributor) | Umożliwia zarządzanie usługą kopii zapasowych, ale nie może tworzyć magazynów i zapewniać dostępu innym osobom | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | [Operator kopii zapasowych](#backup-operator) | Umożliwia zarządzanie usługami kopii zapasowych, z wyjątkiem usuwania kopii zapasowych, tworzenia magazynu i udzielania dostępu innym osobom | 00c29273-979b-4161-815c-10b084fb9324 |
> | [Czytnik kopii zapasowych](#backup-reader) | Może wyświetlać usługi kopii zapasowej, ale nie może wprowadzać zmian | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | [Współautor klasycznego konta magazynu](#classic-storage-account-contributor) | Umożliwia zarządzanie klasycznymi kontami magazynu, ale nie umożliwia uzyskiwania do nich dostępu. | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | [Rola usługi operatora kluczy klasycznego konta magazynu](#classic-storage-account-key-operator-service-role) | Operatorzy kluczy klasycznego konta magazynu mogą wyświetlać i ponownie generować klucze na klasycznych kontach magazynu | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | [Współautor urządzenie Data Box](#data-box-contributor) | Umożliwia zarządzanie wszystko w obszarze usługi urządzenie Data Box z wyjątkiem udzielenia dostępu innym osobom. | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | [urządzenie Data Box czytelnik](#data-box-reader) | Umożliwia zarządzanie usługą urządzenie Data Box, z wyjątkiem tworzenia kolejności lub edytowania szczegółów kolejności i udzielania dostępu innym osobom. | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | [Data Lake Analytics deweloper](#data-lake-analytics-developer) | Umożliwia przesyłanie własnych zadań, monitorowanie ich i zarządzanie nimi, ale nie tworzenie ani usuwanie kont Data Lake Analytics. | 47b7735b-770e-4598-A7DA-8b91488b4c88 |
> | [Czytnik i dostęp do danych](#reader-and-data-access) | Umożliwia wyświetlenie wszystkiego, ale nie pozwala na usunięcie ani utworzenie konta magazynu ani zawartego zasobu. Zezwoli również na dostęp do odczytu i zapisu do wszystkich danych znajdujących się na koncie magazynu za pośrednictwem dostępu do kluczy konta magazynu. | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | [Współautor konta magazynu](#storage-account-contributor) | Umożliwia zarządzanie kontami magazynu. Zapewnia dostęp do klucza konta, który może służyć do uzyskiwania dostępu do danych za pośrednictwem autoryzacji klucza współużytkowanego. | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | [Rola usługi operatora kluczy konta magazynu](#storage-account-key-operator-service-role) | Zezwala na wyświetlanie i ponowne generowanie kluczy dostępu do konta magazynu. | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | [Współautor danych obiektu blob magazynu](#storage-blob-data-contributor) | Odczytuj, zapisuj i usuwaj kontenery i obiekty blob usługi Azure Storage. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | [Właściciel danych obiektów blob magazynu](#storage-blob-data-owner) | Zapewnia pełen dostęp do kontenerów obiektów blob i danych usługi Azure Storage, w tym do przypisywania kontroli dostępu POSIX. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | [Czytnik danych obiektów blob magazynu](#storage-blob-data-reader) | Odczytuj i wyświetlaj kontenery usługi Azure Storage oraz obiekty blob. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | [Delegat obiektów blob magazynu](#storage-blob-delegator) | Pobierz klucz delegowania użytkownika, którego można następnie użyć do utworzenia sygnatury dostępu współdzielonego dla kontenera lub obiektu BLOB podpisanego przy użyciu poświadczeń usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Tworzenie skojarzeń zabezpieczeń dla delegowania użytkowników](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas). | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | [Współautor udziałów SMB danych plików magazynu](#storage-file-data-smb-share-contributor) | Zezwala na dostęp do odczytu, zapisu i usuwania w plikach/katalogach w udziałach plików platformy Azure. Ta rola nie ma wbudowanego odpowiedniku na serwerach plików systemu Windows. | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | [Współautor współautora udziału SMB danych plików magazynu](#storage-file-data-smb-share-elevated-contributor) | Zezwala na odczyt, zapis, usuwanie i modyfikowanie list ACL dla plików/katalogów w udziałach plików platformy Azure. Ta rola jest równoważna z listą ACL udziałów plików na serwerach plików systemu Windows. | a7264617-510b-434b-a828-9731dc254ea7 |
> | [Czytnik udziałów SMB danych plików magazynu](#storage-file-data-smb-share-reader) | Zezwala na dostęp do odczytu dla plików/katalogów w udziałach plików platformy Azure. Ta rola jest równoważna z listą ACL udziału plików odczytywaną na serwerach plików systemu Windows. | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | [Współautor danych kolejki magazynu](#storage-queue-data-contributor) | Odczytuj, zapisuj i usuwaj kolejki usługi Azure Storage oraz wiadomości w kolejce. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | [Procesor komunikatów danych kolejki magazynu](#storage-queue-data-message-processor) | Wgląd, pobieranie i usuwanie wiadomości z kolejki usługi Azure Storage. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | [Nadawca komunikatu o danych kolejki magazynu](#storage-queue-data-message-sender) | Dodawanie komunikatów do kolejki usługi Azure Storage. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | [Czytnik danych kolejki magazynu](#storage-queue-data-reader) | Odczytuj i wyświetlaj kolejki usługi Azure Storage oraz wiadomości w kolejce. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Sieć Web** |  |  |
> | [Azure Maps czytnika danych](#azure-maps-data-reader) | Przyznaje dostęp do odczytu danych związanych z mapowaniem z konta usługi Azure Maps. | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | [Współautor Search Service](#search-service-contributor) | Umożliwia zarządzanie usługami wyszukiwania, ale nie umożliwia uzyskiwania do nich dostępu. | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | [Współautor planu sieci Web](#web-plan-contributor) | Umożliwia zarządzanie planami sieci Web dla witryn internetowych, ale nie umożliwia uzyskiwania do nich dostępu. | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | [Współautor witryny sieci Web](#website-contributor) | Umożliwia zarządzanie witrynami sieci Web (nie planami internetowymi), ale nie umożliwia uzyskiwania do nich dostępu. | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Containers** |  |  |
> | [AcrDelete](#acrdelete) | ACR Usuń | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | [AcrImageSigner](#acrimagesigner) | ACR podpisującego obraz | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | [AcrPull](#acrpull) | ACR ściągaj | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | [AcrPush](#acrpush) | ACR wypychania | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | [AcrQuarantineReader](#acrquarantinereader) | czytnik danych kwarantanny ACR | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | [AcrQuarantineWriter](#acrquarantinewriter) | składnik zapisywania danych kwarantanny ACR | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | [Rola administratora klastra usługi Kubernetes platformy Azure](#azure-kubernetes-service-cluster-admin-role) | Wyświetl listę akcji poświadczeń administratora klastra. | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | [Rola użytkownika klastra usługi Azure Kubernetes Service](#azure-kubernetes-service-cluster-user-role) | Wyświetl listę akcji poświadczeń użytkownika klastra. | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | **Bazy danych** |  |  |
> | [Rola czytnika konta Cosmos DB](#cosmos-db-account-reader-role) | Może odczytywać Azure Cosmos DB dane konta. Aby zarządzać kontami Azure Cosmos DB, zobacz [współautor konta usługi DocumentDB](#documentdb-account-contributor) . | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | [Operator Cosmos DB](#cosmos-db-operator) | Umożliwia zarządzanie kontami Azure Cosmos DB, ale nie dostęp do danych w nich. Uniemożliwia dostęp do kluczy konta i parametrów połączenia. | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | [CosmosBackupOperator](#cosmosbackupoperator) | Może przesłać żądanie przywracania dla bazy danych Cosmos DB lub kontenera dla konta | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | [Współautor konta DocumentDB](#documentdb-account-contributor) | Może zarządzać kontami Azure Cosmos DB. Azure Cosmos DB jest dawniej znany jako DocumentDB. | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | [Współautor Redis Cache](#redis-cache-contributor) | Umożliwia zarządzanie pamięciami podręcznymi Redis, ale nie umożliwia uzyskiwania do nich dostępu. | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | [Współautor bazy danych SQL](#sql-db-contributor) | Umożliwia zarządzanie bazami danych SQL, ale nie umożliwia uzyskiwania do nich dostępu. Nie można również zarządzać zasadami związanymi z zabezpieczeniami ani ich nadrzędnymi serwerami SQL. | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | [Współautor wystąpienia zarządzanego SQL](#sql-managed-instance-contributor) | Umożliwia zarządzanie wystąpieniami zarządzanymi SQL i wymaganą konfiguracją sieci, ale nie zapewnia dostępu innym osobom. | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | [Menedżer zabezpieczeń SQL](#sql-security-manager) | Umożliwia zarządzanie zasadami związanymi z zabezpieczeniami serwerów SQL i baz danych, ale nie umożliwia uzyskiwania do nich dostępu. | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | [Współautor SQL Server](#sql-server-contributor) | Umożliwia zarządzanie serwerami i bazami danych SQL, ale nie umożliwia uzyskiwania do nich dostępu ani zasad związanych z zabezpieczeniami. | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Analiza** |  |  |
> | [Właściciel danych Event Hubs platformy Azure](#azure-event-hubs-data-owner) | Umożliwia pełny dostęp do zasobów usługi Azure Event Hubs. | f526a384-b230-433a-b45c-95f59c4a2dec |
> | [Usługa Azure Event Hubs Data Receiver](#azure-event-hubs-data-receiver) | Zezwala na odbieranie dostępu do zasobów usługi Azure Event Hubs. | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | [Nadawca danych Event Hubs platformy Azure](#azure-event-hubs-data-sender) | Zezwala na wysyłanie dostępu do zasobów Event Hubs platformy Azure. | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | [Współautor Data Factory](#data-factory-contributor) | Twórz fabryki danych i zarządzaj nimi, a także zasobami podrzędnymi. | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | [Przeczyszczanie danych](#data-purger) | Można przeczyścić dane analityczne | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | [Operator klastra usługi HDInsight](#hdinsight-cluster-operator) | Umożliwia odczytywanie i modyfikowanie konfiguracji klastrów usługi HDInsight. | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | [Współautor usług domenowych w usłudze HDInsight](#hdinsight-domain-services-contributor) | Może odczytywać, tworzyć, modyfikować i usuwać operacje związane z usługami domenowymi, które są potrzebne pakiet Enterprise Security usługi HDInsight | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | [Współautor usługi Log Analytics](#log-analytics-contributor) | Współautor Log Analytics może odczytywać wszystkie dane monitorowania i edytować ustawienia monitorowania. Edytowanie ustawień monitorowania obejmuje dodanie rozszerzenia maszyny wirtualnej do maszyn wirtualnych; Odczytywanie kluczy konta magazynu w celu skonfigurowania kolekcji dzienników z usługi Azure Storage; Tworzenie i Konfigurowanie kont usługi Automation; Dodawanie rozwiązań; i Konfigurowanie diagnostyki platformy Azure dla wszystkich zasobów platformy Azure. | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | [Czytelnik usługi Log Analytics](#log-analytics-reader) | Log Analytics Reader może wyświetlać i przeszukiwać wszystkie dane monitorowania, a także wyświetlać ustawienia monitorowania, w tym Wyświetlanie konfiguracji diagnostyki platformy Azure na wszystkich zasobach platformy Azure. | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **Łańcuch bloków** |  |  |
> | [Dostęp do węzła elementu członkowskiego łańcucha bloków (wersja zapoznawcza)](#blockchain-member-node-access-preview) | Zezwala na dostęp do węzłów składowych łańcucha bloków | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **AI i uczenie maszynowe** |  |  |
> | [Współautor Cognitive Services](#cognitive-services-contributor) | Umożliwia tworzenie, odczytywanie, aktualizowanie, usuwanie i zarządzanie kluczami Cognitive Services. | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | [Czytnik danych Cognitive Services (wersja zapoznawcza)](#cognitive-services-data-reader-preview) | Umożliwia odczytywanie danych Cognitive Services. | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | [Cognitive Services użytkownika](#cognitive-services-user) | Umożliwia odczytywanie i wyświetlanie listy kluczy Cognitive Services. | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Rzeczywistość mieszana** |  |  |
> | [Współautor konta kotwic przestrzennych](#spatial-anchors-account-contributor) | Umożliwia zarządzanie zakotwiczeniami przestrzennymi na Twoim koncie, ale nie ich usuwanie | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | [Właściciel konta zakotwiczeń przestrzennych](#spatial-anchors-account-owner) | Umożliwia zarządzanie zakotwiczeniami przestrzennymi na Twoim koncie, w tym ich usuwanie | 70bbe301-9835-447d-afdd-19eb3167307c |
> | [Czytnik konta zakotwiczeń przestrzennych](#spatial-anchors-account-reader) | Umożliwia lokalizowanie i odczytywanie właściwości kotwic przestrzennych na Twoim koncie | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Integracja** |  |  |
> | [Współautor usługi API Management](#api-management-service-contributor) | Może zarządzać usługą i interfejsami API | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | [Rola operatora usługi API Management](#api-management-service-operator-role) | Może zarządzać usługą, ale nie z interfejsów API | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | [Rola czytnika usługi API Management](#api-management-service-reader-role) | Dostęp tylko do odczytu do usługi i interfejsów API | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | [Właściciel danych konfiguracji aplikacji](#app-configuration-data-owner) | Umożliwia pełny dostęp do danych konfiguracji aplikacji. | 5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b |
> | [Czytnik danych konfiguracji aplikacji](#app-configuration-data-reader) | Zezwala na dostęp do odczytu do danych konfiguracji aplikacji. | 516239f1-63e1-4d78-a4de-a74fb236a071 |
> | [Właściciel danych Azure Service Bus](#azure-service-bus-data-owner) | Umożliwia pełny dostęp do zasobów Azure Service Bus. | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | [Azure Service Bus odbiorca danych](#azure-service-bus-data-receiver) | Umożliwia uzyskanie dostępu do zasobów Azure Service Bus. | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | [Nadawca danych Azure Service Bus](#azure-service-bus-data-sender) | Zezwala na dostęp do Azure Service Bus zasobów. | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | [Właściciel rejestracji Azure Stack](#azure-stack-registration-owner) | Umożliwia zarządzanie rejestracjami Azure Stack. | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | [Współautor EventGrid EventSubscription](#eventgrid-eventsubscription-contributor) | Umożliwia zarządzanie operacjami subskrypcji zdarzeń EventGrid. | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | [EventGrid EventSubscription](#eventgrid-eventsubscription-reader) | Umożliwia odczytanie subskrypcji zdarzeń EventGrid. | 2414bbcf-6497-4faf-8c65-045460748405 |
> | [Współautor konta systemów inteligentnych](#intelligent-systems-account-contributor) | Umożliwia zarządzanie kontami inteligentnych systemów, ale nie umożliwia uzyskiwania do nich dostępu. | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | [Współautor aplikacji logiki](#logic-app-contributor) | Umożliwia zarządzanie aplikacjami logiki, ale nie umożliwia zmiany dostępu do nich. | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | [Operator aplikacji logiki](#logic-app-operator) | Umożliwia odczytywanie, Włączanie i wyłączanie aplikacji logiki, ale nie ich edytowanie ani aktualizowanie. | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Tożsamość** |  |  |
> | [Współautor tożsamości zarządzanej](#managed-identity-contributor) | Tworzenie, odczytywanie, aktualizowanie i usuwanie tożsamości przypisanej przez użytkownika | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | [Operator tożsamości zarządzanej](#managed-identity-operator) | Odczytaj i przypisz tożsamość przypisaną przez użytkownika | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Bezpieczeństwo** |  |  |
> | [Współautor wskaźnikowego platformy Azure](#azure-sentinel-contributor) | Współautor wskaźnikowego platformy Azure | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | [Czytnik wskaźnikowy platformy Azure](#azure-sentinel-reader) | Czytnik wskaźnikowy platformy Azure | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | [Obiekt odpowiadający wskaźnikowi platformy Azure](#azure-sentinel-responder) | Obiekt odpowiadający wskaźnikowi platformy Azure | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | [Współautor Key Vault](#key-vault-contributor) | Umożliwia Zarządzanie magazynami kluczy, ale nie umożliwia uzyskiwania do nich dostępu. | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | [Administrator zabezpieczeń](#security-admin) | Wyświetl i zaktualizuj uprawnienia dla Security Center. Te same uprawnienia, jak rola czytelnik zabezpieczeń, mogą także aktualizować zasady zabezpieczeń i odrzucać alerty i zalecenia. | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | [Współautor oceny zabezpieczeń](#security-assessment-contributor) | Umożliwia wypychanie ocen do Security Center | 612c2aa1-cb24-443b-ac28-3ab7272de6f5 |
> | [Security Manager (starsza wersja)](#security-manager-legacy) | Jest to Starsza rola. Zamiast tego należy użyć administratora zabezpieczeń. | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | [Czytelnik zabezpieczeń](#security-reader) | Wyświetl uprawnienia dla Security Center. Może wyświetlać zalecenia, alerty, zasady zabezpieczeń i Stany zabezpieczeń, ale nie może wprowadzać zmian. | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **DevOps** |  |  |
> | [Użytkownik DevTest Labs](#devtest-labs-user) | Umożliwia łączenie, uruchamianie, ponowne uruchamianie i zamykanie maszyn wirtualnych w Azure DevTest Labs. | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | [Twórca laboratorium](#lab-creator) | Umożliwia tworzenie i usuwanie zarządzanych laboratoriów z kont laboratorium platformy Azure oraz zarządzanie nimi. | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Monitor** |  |  |
> | [Współautor składnika Application Insights](#application-insights-component-contributor) | Może zarządzać składnikami Application Insights | ae349356-3a1b-4a5e-921d-050484c6347e |
> | [Application Insights Snapshot Debugger](#application-insights-snapshot-debugger) | Daje użytkownikowi uprawnienia do wyświetlania i pobierania migawek debugowania zebranych za pomocą Snapshot Debugger Application Insights. Należy zauważyć, że te uprawnienia nie są uwzględnione w rolach [właściciel](#owner) lub [współautor](#contributor) . | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | [Współautor monitorowania](#monitoring-contributor) | Może odczytywać wszystkie dane monitorowania i edytować ustawienia monitorowania. Zobacz też Rozpoczynanie [pracy z rolami, uprawnieniami i zabezpieczeniami przy użyciu Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles). | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | [Wydawca metryk monitorowania](#monitoring-metrics-publisher) | Włącza publikowanie metryk dla zasobów platformy Azure | 3913510d-42f4-4e42-8a64-420c390055eb |
> | [Czytnik monitorowania](#monitoring-reader) | Może odczytywać wszystkie dane monitorowania (metryki, dzienniki itp.). Zobacz też Rozpoczynanie [pracy z rolami, uprawnieniami i zabezpieczeniami przy użyciu Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles). | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | [Współautor skoroszytu](#workbook-contributor) | Może zapisywać udostępnione skoroszyty. | e8ddcd69-c73f-4f9f-9844-4100522f16ad |
> | [Czytelnik skoroszytu](#workbook-reader) | Może odczytywać skoroszyty. | b279062a-9be3-42a0-92ae-8b3cf002ec4d |
> | **Zarządzanie i nadzór** |  |  |
> | [Operator zadania automatyzacji](#automation-job-operator) | Twórz zadania i zarządzaj nimi za pomocą elementów Runbook usługi Automation. | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | [Operator usługi](#automation-operator) | Operatory automatyzacji mogą uruchamiać, zatrzymywać, wstrzymywać i wznawiać zadania | d3881f73-407a-4167-8283-e981cbba0404 |
> | [Operator elementu Runbook usługi Automation](#automation-runbook-operator) | Odczytywanie właściwości elementu Runbook — aby można było tworzyć zadania elementu Runbook. | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | [Dołączanie maszyny połączonej z platformą Azure](#azure-connected-machine-onboarding) | Można dołączyć maszyny połączone z platformą Azure. | b64e21ea-ac4e-4cdf-9dc9-5b892992bee7 |
> | [Administrator zasobów maszyny połączonej z platformą Azure](#azure-connected-machine-resource-administrator) | Może odczytywać, zapisywać, usuwać i ponownie dołączać maszyny wirtualne platformy Azure. | cd570a14-e51a-42ad-bac8-bafd67325302 |
> | [Czytnik rozliczeń](#billing-reader) | Zezwala na dostęp do odczytu do danych rozliczeniowych | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | [Współautor planu](#blueprint-contributor) | Może zarządzać definicjami planu, ale nie należy ich przypisywać. | 41077137-e803-4205-871c-5a86e6a753b4 |
> | [Operator planu](#blueprint-operator) | Może przypisywać istniejące opublikowane plany, ale nie może tworzyć nowych planów. Należy zauważyć, że to działanie działa tylko wtedy, gdy przypisanie zostało wykonane przy użyciu tożsamości zarządzanej przypisanej przez użytkownika. | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | [Współautor Cost Management](#cost-management-contributor) | Umożliwia wyświetlanie kosztów i zarządzanie konfiguracją kosztów (np. budżetów, eksportów) | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | [Cost Management czytelnik](#cost-management-reader) | Może wyświetlać dane i konfigurację kosztów (np. budżetów, eksportów) | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | [Rola współautora aplikacji zarządzanej](#managed-application-contributor-role) | Umożliwia tworzenie zasobów aplikacji zarządzanej. | 641177b8-a67a-45b9-a033-47bc880bb21e |
> | [Rola operatora aplikacji zarządzanej](#managed-application-operator-role) | Umożliwia odczytywanie i wykonywanie akcji dotyczących zasobów aplikacji zarządzanej | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | [Czytnik aplikacji zarządzanych](#managed-applications-reader) | Umożliwia odczytanie zasobów w zarządzanej aplikacji i zażądanie dostępu JIT. | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | [Rola usuwania przypisania rejestracji usług zarządzanych](#managed-services-registration-assignment-delete-role) | Rola usuwania przypisania rejestracji usług zarządzanych umożliwia użytkownikom zarządzającym dzierżawę usuwanie przypisania rejestracji przypisanego do swojej dzierżawy. | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | [Współautor grupy zarządzania](#management-group-contributor) | Rola współautora grupy zarządzania | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | [Czytelnik grupy zarządzania](#management-group-reader) | Rola czytelnika grupy zarządzania | ac63b705-f282-497d-ac71-919bf39d939d |
> | [Nowe współautor konta APM Relic](#new-relic-apm-account-contributor) | Umożliwia zarządzanie kontami i aplikacjami New Relic Application Performance Management, ale nie umożliwia uzyskiwania do nich dostępu. | 5d28c62d-5b37-4476-8438-e587778df237 |
> | [Moduł zapisywania danych usługi Policy Insights (wersja zapoznawcza)](#policy-insights-data-writer-preview) | Zezwala na dostęp do odczytu do zasad zasobów i dostęp do zapisu dla zdarzeń zasad składnika zasobów. | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | [Współautor zasad zasobów](#resource-policy-contributor) | Użytkownicy z prawami do tworzenia/modyfikowania zasad dotyczących zasobów, tworzenia biletów pomocy technicznej i odczytywania zasobów/hierarchii. | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | [Współautor Site Recovery](#site-recovery-contributor) | Umożliwia zarządzanie usługą Site Recovery z wyjątkiem tworzenia magazynu i przypisywania ról | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | [Operator Site Recovery](#site-recovery-operator) | Umożliwia przełączenie w tryb failover i powrót po awarii, ale nie wykonywanie innych operacji zarządzania Site Recovery | 494ae006-db33-4328-bf46-533a6560a3ca |
> | [Site Recovery czytelnik](#site-recovery-reader) | Umożliwia wyświetlanie stanu Site Recovery, ale nie wykonywanie innych operacji zarządzania | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | [Współautor żądania pomocy technicznej](#support-request-contributor) | Pozwala tworzyć żądania pomocy technicznej i zarządzać nimi | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | [Współautor tagów](#tag-contributor) | Umożliwia zarządzanie tagami w jednostkach bez zapewniania dostępu do samych jednostek. | 4a9ae827-6dc8-4573-8ac7-8239d42aa03f |
> | **Inne** |  |  |
> | [Współautor BizTalk](#biztalk-contributor) | Umożliwia zarządzanie usługami BizTalk Services, ale nie umożliwia uzyskiwania do nich dostępu. | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | [Współautor kolekcji zadań usługi Scheduler](#scheduler-job-collections-contributor) | Umożliwia zarządzanie kolekcjami zadań harmonogramu, ale nie umożliwia uzyskiwania do nich dostępu. | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |


## <a name="general"></a>Ogólne


### <a name="contributor"></a>Współautor

Umożliwia zarządzanie wszystko z wyjątkiem udzielania dostępu do zasobów.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | * | Twórz zasoby wszystkich typów i zarządzaj nimi |
> | **NotActions** |  |
> | Microsoft. Authorization/*/Delete | Usuwanie ról, przypisań zasad, definicji zasad i definicji zestawów zasad |
> | Microsoft. Authorization/*/Write | Tworzenie ról, przypisań ról, przypisań zasad, definicji zasad i definicji zestawów zasad |
> | Microsoft. Authorization/elevateAccess/akcja | Przyznaje osobie wywołującej uprawnienia administratora dostępu użytkowników w zakresie dzierżawy |
> | Microsoft. plan/blueprintAssignments/zapis | Utwórz lub zaktualizuj wszystkie przypisania strategii |
> | Microsoft. plan/blueprintAssignments/usuwanie | Usuń wszystkie przypisania strategii |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

Umożliwia zarządzanie wszystko, w tym dostęp do zasobów.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | * | Twórz zasoby wszystkich typów i zarządzaj nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

Umożliwia wyświetlenie wszystkiego, ale nie wprowadza żadnych zmian.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | */read | Odczytuj zasoby wszystkich typów, z wyjątkiem kluczy tajnych. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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
> | */read | Odczytuj zasoby wszystkich typów, z wyjątkiem kluczy tajnych. |
> | Microsoft. Authorization/* | Zarządzanie autoryzacją |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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


### <a name="classic-virtual-machine-contributor"></a>Współautor klasycznej maszyny wirtualnej

Umożliwia zarządzanie klasycznymi maszynami wirtualnymi, ale nie umożliwia uzyskiwania do nich dostępu, a nie do sieci wirtualnej ani konta magazynu, z którymi są połączone.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. ClassicCompute/domainNames/* | Tworzenie klasycznych nazw domen obliczeniowych i zarządzanie nimi |
> | Microsoft. ClassicCompute/virtualMachines/* | Tworzenie maszyn wirtualnych i zarządzanie nimi |
> | Microsoft. ClassicNetwork/networkSecurityGroups/Join/Action |  |
> | Microsoft. ClassicNetwork/reservedIps/link/akcja | Połącz zastrzeżony adres IP |
> | Microsoft. ClassicNetwork/reservedIps/odczyt | Pobiera zastrzeżone adresy IP |
> | Microsoft. ClassicNetwork/virtualNetworks/Join/Action | Przyłącza do sieci wirtualnej. |
> | Microsoft. ClassicNetwork/virtualNetworks/odczyt | Pobierz sieć wirtualną. |
> | Microsoft. ClassicStorage/storageAccounts/disks/Read | Zwraca dysk konta magazynu. |
> | Microsoft. ClassicStorage/storageAccounts/images/Read | Zwraca obraz konta magazynu. Przestarzałe. Użyj "Microsoft. ClassicStorage/storageAccounts/vmImages") |
> | Microsoft. ClassicStorage/storageAccounts/listKeys/akcja | Wyświetla listę kluczy dostępu dla kont magazynu. |
> | Microsoft. ClassicStorage/storageAccounts/odczyt | Zwróć konto magazynu z danym kontem. |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. ResourceHealth/availabilityStatuses/odczyt | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

Wyświetl Virtual Machines w portalu i zaloguj się jako administrator

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Network/publicIPAddresses/read | Pobiera definicję publicznego adresu IP. |
> | Microsoft. Network/virtualNetworks/odczyt | Pobierz definicję sieci wirtualnej |
> | Microsoft. Network/loadBalancers/odczyt | Pobiera definicję modułu równoważenia obciążenia |
> | Microsoft. Network/networkInterfaces/odczyt | Pobiera definicję interfejsu sieciowego.  |
> | Microsoft. COMPUTE/virtualMachines/*/Read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | Microsoft. COMPUTE/virtualMachines/login/akcja | Zaloguj się do maszyny wirtualnej jako zwykły użytkownik |
> | Microsoft. COMPUTE/virtualMachines/loginAsAdmin/akcja | Logowanie się do maszyny wirtualnej przy użyciu uprawnień administratora systemu Windows lub użytkownika root w systemie Linux |
> | **NotDataActions** |  |
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

Umożliwia zarządzanie maszynami wirtualnymi, ale nie dostęp do nich, a nie do sieci wirtualnej ani konta magazynu, z którymi są połączone.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. COMPUTE/availabilitySets/* | Tworzenie zestawów dostępności obliczeń i zarządzanie nimi |
> | Microsoft. COMPUTE/Locations/* | Tworzenie lokalizacji obliczeniowych i zarządzanie nimi |
> | Microsoft. COMPUTE/virtualMachines/* | Tworzenie maszyn wirtualnych i zarządzanie nimi |
> | Microsoft. COMPUTE/virtualMachineScaleSets/* | Tworzenie zestawów skalowania maszyn wirtualnych i zarządzanie nimi |
> | Microsoft.Compute/disks/write | Tworzy nowy dysk lub aktualizuje istniejący |
> | Microsoft.Compute/disks/read | Pobieranie właściwości dysku |
> | Microsoft. COMPUTE/disks/Delete | Usuwa dysk |
> | Microsoft. wspólny/Schedules/* |  |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. Network/applicationGateways/backendAddressPools/Join/Action | Sprzęga pulę adresów zaplecza bramy aplikacji. Brak alertów. |
> | Microsoft. Network/loadBalancers/backendAddressPools/Join/Action | Sprzęga pulę adresów zaplecza modułu równoważenia obciążenia. Brak alertów. |
> | Microsoft. Network/loadBalancers/inboundNatPools/Join/Action | Przyłącza do puli NAT dla ruchu przychodzącego modułu równoważenia obciążenia. Brak alertów. |
> | Microsoft. Network/loadBalancers/inboundNatRules/Join/Action | Przyłącza do przychodzącej reguły NAT modułu równoważenia obciążenia. Brak alertów. |
> | Microsoft. Network/loadBalancers/sondy/akcja | Umożliwia korzystanie z sond modułu równoważenia obciążenia. Na przykład za pomocą tej właściwości uprawnienia healthProbe zestawu skalowania maszyn wirtualnych można odwoływać się do sondy. Brak alertów. |
> | Microsoft. Network/loadBalancers/odczyt | Pobiera definicję modułu równoważenia obciążenia |
> | Microsoft. Network/lokalizacje/* | Tworzenie lokalizacji sieciowych i zarządzanie nimi |
> | Microsoft. Network/networkInterfaces/* | Tworzenie interfejsów sieciowych i zarządzanie nimi |
> | Microsoft. Network/networkSecurityGroups/Join/Action | Przyłącza do sieciowej grupy zabezpieczeń. Brak alertów. |
> | Microsoft. Network/networkSecurityGroups/odczyt | Pobiera definicję sieciowej grupy zabezpieczeń |
> | Microsoft.Network/publicIPAddresses/join/action | Przyłącza do publicznego adresu IP. Brak alertów. |
> | Microsoft.Network/publicIPAddresses/read | Pobiera definicję publicznego adresu IP. |
> | Microsoft. Network/virtualNetworks/odczyt | Pobierz definicję sieci wirtualnej |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Przyłącza do sieci wirtualnej. Brak alertów. |
> | Microsoft. RecoveryServices/Locations/* |  |
> | Microsoft. RecoveryServices/magazyny/backupFabrics/backupProtectionIntent/zapis | Tworzenie opcji ochrony kopii zapasowej |
> | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/*/Read |  |
> | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/Read | Zwraca szczegóły obiektu chronionego elementu |
> | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/Write | Utwórz chroniony element kopii zapasowej |
> | Microsoft. RecoveryServices/magazyny/backupPolicies/odczyt | Zwraca wszystkie zasady ochrony |
> | Microsoft. RecoveryServices/magazyny/backupPolicies/zapis | Tworzy zasady ochrony |
> | Microsoft. RecoveryServices/magazyny/odczyt | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "magazyn" |
> | Microsoft. RecoveryServices/magazyny/użycia/odczyt | Zwraca szczegóły użycia magazynu Recovery Servicesowego. |
> | Microsoft. RecoveryServices/magazyny/zapis | Operacja Utwórz magazyn tworzy zasób platformy Azure typu "magazyn" |
> | Microsoft. ResourceHealth/availabilityStatuses/odczyt | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. SqlVirtualMachine/* |  |
> | Microsoft. Storage/storageAccounts/listKeys/akcja | Zwraca klucze dostępu dla określonego konta magazynu. |
> | Microsoft. Storage/storageAccounts/Read | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="virtual-machine-user-login"></a>Logowanie użytkownika maszyny wirtualnej

Wyświetl Virtual Machines w portalu i zaloguj się jako zwykły użytkownik.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft.Network/publicIPAddresses/read | Pobiera definicję publicznego adresu IP. |
> | Microsoft. Network/virtualNetworks/odczyt | Pobierz definicję sieci wirtualnej |
> | Microsoft. Network/loadBalancers/odczyt | Pobiera definicję modułu równoważenia obciążenia |
> | Microsoft. Network/networkInterfaces/odczyt | Pobiera definicję interfejsu sieciowego.  |
> | Microsoft. COMPUTE/virtualMachines/*/Read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | Microsoft. COMPUTE/virtualMachines/login/akcja | Zaloguj się do maszyny wirtualnej jako zwykły użytkownik |
> | **NotDataActions** |  |
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


### <a name="cdn-endpoint-contributor"></a>Współautor punktu końcowego usługi CDN

Może zarządzać punktami końcowymi usługi CDN, ale nie może udzielić dostępu innym użytkownikom.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. CDN/edgenodes/odczyt |  |
> | Microsoft. CDN/operationresults/* |  |
> | Microsoft. CDN/profile/punkty końcowe/* |  |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="cdn-endpoint-reader"></a>Czytnik punktu końcowego usługi CDN

Może wyświetlać punkty końcowe usługi CDN, ale nie może wprowadzać zmian.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. CDN/edgenodes/odczyt |  |
> | Microsoft. CDN/operationresults/* |  |
> | Microsoft. CDN/profile/punkty końcowe/*/Read |  |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="cdn-profile-contributor"></a>Współautor profilu CDN

Może zarządzać profilami sieci CDN i ich punktami końcowymi, ale nie może udzielać dostępu innym użytkownikom.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. CDN/edgenodes/odczyt |  |
> | Microsoft. CDN/operationresults/* |  |
> | Microsoft. CDN/profile/* |  |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="cdn-profile-reader"></a>Czytnik profilu CDN

Może wyświetlać profile usługi CDN i ich punkty końcowe, ale nie może wprowadzać zmian.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. CDN/edgenodes/odczyt |  |
> | Microsoft. CDN/operationresults/* |  |
> | Microsoft. CDN/profile/*/Read |  |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

Umożliwia zarządzanie sieciami klasycznymi, ale nie umożliwia uzyskiwania do nich dostępu.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. ClassicNetwork/* | Tworzenie klasycznych sieci i zarządzanie nimi |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. ResourceHealth/availabilityStatuses/odczyt | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

Umożliwia zarządzanie strefami i zestawami rekordów DNS w Azure DNS, ale nie pozwala na kontrolowanie dostępu do nich.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. Network/dnsZones/* | Tworzenie stref i rekordów DNS oraz zarządzanie nimi |
> | Microsoft. ResourceHealth/availabilityStatuses/odczyt | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

Umożliwia zarządzanie sieciami, ale nie umożliwia uzyskiwania do nich dostępu.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. Network/* | Tworzenie sieci i zarządzanie nimi |
> | Microsoft. ResourceHealth/availabilityStatuses/odczyt | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="traffic-manager-contributor"></a>Współautor Traffic Manager

Umożliwia zarządzanie profilami Traffic Manager, ale nie pozwala na kontrolowanie użytkowników, którzy mają do nich dostęp.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. Network/trafficManagerProfiles/* |  |
> | Microsoft. ResourceHealth/availabilityStatuses/odczyt | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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


### <a name="avere-contributor"></a>Współautor avere

Można utworzyć klaster avere vFXT i zarządzać nim.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. COMPUTE/*/Read |  |
> | Microsoft. COMPUTE/availabilitySets/* |  |
> | Microsoft. COMPUTE/virtualMachines/* |  |
> | Microsoft. COMPUTE/disks/* |  |
> | Microsoft. Network/*/Read |  |
> | Microsoft. Network/networkInterfaces/* |  |
> | Microsoft. Network/virtualNetworks/odczyt | Pobierz definicję sieci wirtualnej |
> | Microsoft.Network/virtualNetworks/subnets/read | Pobiera definicję podsieci sieci wirtualnej |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Przyłącza do sieci wirtualnej. Brak alertów. |
> | Microsoft. Network/virtualNetworks/Subnets/joinViaServiceEndpoint/Action | Dołącza do podsieci zasoby, takie jak konto magazynu lub baza danych SQL. Brak alertów. |
> | Microsoft. Network/networkSecurityGroups/Join/Action | Przyłącza do sieciowej grupy zabezpieczeń. Brak alertów. |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Storage/*/Read |  |
> | Microsoft. Storage/storageAccounts/* | Tworzenie kont magazynu i zarządzanie nimi |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | Microsoft. resources/subscriptions/resourceGroups/Resources/Read | Pobiera zasoby dla grupy zasobów. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/usuwanie | Zwraca wynik usunięcia obiektu BLOB |
> | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/odczyt | Zwraca obiekt BLOB lub listę obiektów BLOB |
> | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/zapis | Zwraca wynik zapisania obiektu BLOB |
> | **NotDataActions** |  |
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

### <a name="avere-operator"></a>Operator avere

Używane przez klaster avere vFXT do zarządzania klastrem

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. COMPUTE/virtualMachines/Read | Pobierz właściwości maszyny wirtualnej |
> | Microsoft. Network/networkInterfaces/odczyt | Pobiera definicję interfejsu sieciowego.  |
> | Microsoft. Network/networkInterfaces/Write | Tworzy interfejs sieciowy lub aktualizuje istniejący interfejs sieciowy.  |
> | Microsoft. Network/virtualNetworks/odczyt | Pobierz definicję sieci wirtualnej |
> | Microsoft.Network/virtualNetworks/subnets/read | Pobiera definicję podsieci sieci wirtualnej |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Przyłącza do sieci wirtualnej. Brak alertów. |
> | Microsoft. Network/networkSecurityGroups/Join/Action | Przyłącza do sieciowej grupy zabezpieczeń. Brak alertów. |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Storage/storageAccounts/blobServices/kontenery/usuwanie | Zwraca wynik usunięcia kontenera |
> | Microsoft. Storage/storageAccounts/blobServices/kontenery/odczyt | Zwraca listę kontenerów |
> | Microsoft. Storage/storageAccounts/blobServices/kontenery/zapis | Zwraca wynik umieszczenia kontenera obiektów BLOB. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/usuwanie | Zwraca wynik usunięcia obiektu BLOB |
> | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/odczyt | Zwraca obiekt BLOB lub listę obiektów BLOB |
> | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/zapis | Zwraca wynik zapisania obiektu BLOB |
> | **NotDataActions** |  |
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

Umożliwia zarządzanie usługą kopii zapasowych, ale nie może tworzyć magazynów i zapewniać dostępu innym osobom

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. Network/virtualNetworks/odczyt | Pobierz definicję sieci wirtualnej |
> | Microsoft. RecoveryServices/Locations/* |  |
> | Microsoft. RecoveryServices/magazyny/backupFabrics/operationResults/* | Zarządzanie wynikami operacji zarządzania kopiami zapasowymi |
> | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/* | Tworzenie kontenerów kopii zapasowych w ramach sieci szkieletowej magazynu Recovery Services i zarządzanie nimi |
> | Microsoft. RecoveryServices/magazyny/backupFabrics/refreshContainers/akcja | Odświeża listę kontenerów |
> | Microsoft. RecoveryServices/magazyny/backupJobs/* | Tworzenie zadań tworzenia kopii zapasowych i zarządzanie nimi |
> | Microsoft. RecoveryServices/magazyny/backupJobsExport/akcja | Eksportuj zadania |
> | Microsoft. RecoveryServices/magazyny/backupOperationResults/* | Twórz wyniki operacji zarządzania kopiami zapasowymi i zarządzaj nimi |
> | Microsoft. RecoveryServices/magazyny/backupPolicies/* | Tworzenie zasad tworzenia kopii zapasowych i zarządzanie nimi |
> | Microsoft. RecoveryServices/magazyny/backupProtectableItems/* | Utwórz elementy, których kopię zapasową można utworzyć, i zarządzaj nimi |
> | Microsoft. RecoveryServices/magazyny/backupProtectedItems/* | Tworzenie elementów kopii zapasowych i zarządzanie nimi |
> | Microsoft. RecoveryServices/magazyny/backupProtectionContainers/* | Tworzenie kontenerów z elementami kopii zapasowych i zarządzanie nimi |
> | Microsoft. RecoveryServices/magazyny/backupSecurityPIN/* |  |
> | Microsoft. RecoveryServices/magazyny/backupUsageSummaries/odczyt | Zwraca podsumowania elementów chronionych i serwerów chronionych dla Recovery Services. |
> | Microsoft. RecoveryServices/magazyny/certyfikaty/* | Tworzenie i zarządzanie certyfikatami związanymi z tworzeniem kopii zapasowych w magazynie Recovery Services |
> | Microsoft. RecoveryServices/magazyny/extendedInformation/* | Utwórz rozszerzone informacje powiązane z magazynem i zarządzaj nimi |
> | Microsoft. RecoveryServices/magazyny/monitoringAlerts/odczyt | Pobiera alerty dla magazynu usługi Recovery Services. |
> | Microsoft. RecoveryServices/magazyny/monitoringConfigurations/* |  |
> | Microsoft. RecoveryServices/magazyny/odczyt | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "magazyn" |
> | Microsoft. RecoveryServices/magazyny/registeredIdentities/* | Tworzenie zarejestrowanych tożsamości i zarządzanie nimi |
> | Microsoft. RecoveryServices/magazyny/użycia/* | Tworzenie i zarządzanie użyciem magazynu Recovery Services |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Storage/storageAccounts/Read | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | Microsoft. RecoveryServices/magazyny/backupstorageconfig/* |  |
> | Microsoft. RecoveryServices/magazyny/backupconfig/* |  |
> | Microsoft. RecoveryServices/magazyny/backupValidateOperation/akcja | Sprawdź poprawność operacji dla chronionego elementu |
> | Microsoft. RecoveryServices/magazyny/zapis | Operacja Utwórz magazyn tworzy zasób platformy Azure typu "magazyn" |
> | Microsoft. RecoveryServices/magazyny/backupOperations/odczyt | Zwraca stan operacji kopii zapasowej dla magazynu Recovery Services. |
> | Microsoft. RecoveryServices/magazyny/backupEngines/odczyt | Zwraca wszystkie serwery zarządzania kopiami zapasowymi zarejestrowane w magazynie. |
> | Microsoft. RecoveryServices/magazyny/backupFabrics/backupProtectionIntent/* |  |
> | Microsoft. RecoveryServices/magazyny/backupFabrics/protectableContainers/odczyt | Pobierz wszystkie kontenery z ochroną |
> | Microsoft. RecoveryServices/Locations/backupStatus/Action | Sprawdź stan kopii zapasowej Recovery Services magazynów |
> | Microsoft. RecoveryServices/Locations/backupPreValidateProtection/Action |  |
> | Microsoft. RecoveryServices/Locations/backupValidateFeatures/Action | Weryfikuj funkcje |
> | Microsoft. RecoveryServices/magazyny/monitoringAlerts/zapis | Rozwiązuje alert. |
> | Microsoft. RecoveryServices/Operations/Read | Operacja zwraca listę operacji dla dostawcy zasobów |
> | Microsoft. RecoveryServices/Locations/operationStatus/Read | Pobiera stan operacji dla danej operacji |
> | Microsoft. RecoveryServices/magazyny/backupProtectionIntents/odczyt | Wyświetl listę wszystkich intencji ochrony kopii zapasowej |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="backup-operator"></a>Operator kopii zapasowych

Umożliwia zarządzanie usługami kopii zapasowych, z wyjątkiem usuwania kopii zapasowych, tworzenia magazynu i udzielania dostępu innym osobom

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. Network/virtualNetworks/odczyt | Pobierz definicję sieci wirtualnej |
> | Microsoft. RecoveryServices/magazyny/backupFabrics/operationResults/odczyt | Zwraca stan operacji |
> | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/operationResults/Read | Pobiera wynik operacji wykonanej na kontenerze ochrony. |
> | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/kopia zapasowa/akcja | Wykonuje kopię zapasową dla chronionego elementu. |
> | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/operationResults/odczyt | Pobiera wynik operacji wykonanej na elementach chronionych. |
> | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/operationsStatus/odczyt | Zwraca stan operacji wykonanej na elementach chronionych. |
> | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/Read | Zwraca szczegóły obiektu chronionego elementu |
> | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/Action | Zapewnij natychmiastowe odzyskiwanie elementu dla chronionego elementu |
> | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/recoveryPoints/odczyt | Pobierz punkty odzyskiwania dla elementów chronionych. |
> | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/recoveryPoints/przywracanie/akcja | Przywracanie punktów odzyskiwania dla elementów chronionych. |
> | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/Action | Odwołaj natychmiastowe odzyskiwanie elementu dla chronionego elementu |
> | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/Write | Utwórz chroniony element kopii zapasowej |
> | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/odczyt | Zwraca wszystkie zarejestrowane kontenery |
> | Microsoft. RecoveryServices/magazyny/backupFabrics/refreshContainers/akcja | Odświeża listę kontenerów |
> | Microsoft. RecoveryServices/magazyny/backupJobs/* | Tworzenie zadań tworzenia kopii zapasowych i zarządzanie nimi |
> | Microsoft. RecoveryServices/magazyny/backupJobsExport/akcja | Eksportuj zadania |
> | Microsoft. RecoveryServices/magazyny/backupOperationResults/* | Twórz wyniki operacji zarządzania kopiami zapasowymi i zarządzaj nimi |
> | Microsoft. RecoveryServices/magazyny/backupPolicies/operationResults/odczyt | Pobierz wyniki operacji zasad. |
> | Microsoft. RecoveryServices/magazyny/backupPolicies/odczyt | Zwraca wszystkie zasady ochrony |
> | Microsoft. RecoveryServices/magazyny/backupProtectableItems/* | Utwórz elementy, których kopię zapasową można utworzyć, i zarządzaj nimi |
> | Microsoft. RecoveryServices/magazyny/backupProtectedItems/odczyt | Zwraca listę wszystkich elementów chronionych. |
> | Microsoft. RecoveryServices/magazyny/backupProtectionContainers/odczyt | Zwraca wszystkie kontenery należące do subskrypcji |
> | Microsoft. RecoveryServices/magazyny/backupUsageSummaries/odczyt | Zwraca podsumowania elementów chronionych i serwerów chronionych dla Recovery Services. |
> | Microsoft. RecoveryServices/magazyny/certyfikaty/zapis | Operacja Aktualizuj certyfikat zasobu aktualizuje certyfikat poświadczeń zasobu/magazynu. |
> | Microsoft. RecoveryServices/magazyny/extendedInformation/odczyt | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu? |
> | Microsoft. RecoveryServices/magazyny/extendedInformation/zapis | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu? |
> | Microsoft. RecoveryServices/magazyny/monitoringAlerts/odczyt | Pobiera alerty dla magazynu usługi Recovery Services. |
> | Microsoft. RecoveryServices/magazyny/monitoringConfigurations/* |  |
> | Microsoft. RecoveryServices/magazyny/odczyt | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "magazyn" |
> | Microsoft. RecoveryServices/magazyny/registeredIdentities/operationResults/odczyt | Operacja Pobierz wyniki operacji umożliwia pobieranie stanu operacji i wyniku operacji przesłanej asynchronicznie |
> | Microsoft. RecoveryServices/magazyny/registeredIdentities/odczyt | Operacja Pobierz kontenery umożliwia pobieranie kontenerów zarejestrowanych dla zasobu. |
> | Microsoft. RecoveryServices/magazyny/registeredIdentities/zapis | Operacji rejestrowania kontenera usługi można użyć do zarejestrowania kontenera z usługą odzyskiwania. |
> | Microsoft. RecoveryServices/magazyny/użycia/odczyt | Zwraca szczegóły użycia magazynu Recovery Servicesowego. |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Storage/storageAccounts/Read | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | Microsoft. RecoveryServices/magazyny/backupstorageconfig/* |  |
> | Microsoft. RecoveryServices/magazyny/backupValidateOperation/akcja | Sprawdź poprawność operacji dla chronionego elementu |
> | Microsoft. RecoveryServices/magazyny/backupOperations/odczyt | Zwraca stan operacji kopii zapasowej dla magazynu Recovery Services. |
> | Microsoft. RecoveryServices/magazyny/backupPolicies/operacje/odczyt | Pobierz stan operacji zasad. |
> | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/zapis | Tworzy zarejestrowany kontener |
> | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/zapytania/akcja | Wykonaj zapytanie dotyczące obciążeń w kontenerze |
> | Microsoft. RecoveryServices/magazyny/backupEngines/odczyt | Zwraca wszystkie serwery zarządzania kopiami zapasowymi zarejestrowane w magazynie. |
> | Microsoft. RecoveryServices/magazyny/backupFabrics/backupProtectionIntent/zapis | Tworzenie opcji ochrony kopii zapasowej |
> | Microsoft. RecoveryServices/magazyny/backupFabrics/backupProtectionIntent/odczyt | Pobierz cel ochrony kopii zapasowej |
> | Microsoft. RecoveryServices/magazyny/backupFabrics/protectableContainers/odczyt | Pobierz wszystkie kontenery z ochroną |
> | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/Items/Read | Pobierz wszystkie elementy w kontenerze |
> | Microsoft. RecoveryServices/Locations/backupStatus/Action | Sprawdź stan kopii zapasowej Recovery Services magazynów |
> | Microsoft. RecoveryServices/Locations/backupPreValidateProtection/Action |  |
> | Microsoft. RecoveryServices/Locations/backupValidateFeatures/Action | Weryfikuj funkcje |
> | Microsoft. RecoveryServices/magazyny/monitoringAlerts/zapis | Rozwiązuje alert. |
> | Microsoft. RecoveryServices/Operations/Read | Operacja zwraca listę operacji dla dostawcy zasobów |
> | Microsoft. RecoveryServices/Locations/operationStatus/Read | Pobiera stan operacji dla danej operacji |
> | Microsoft. RecoveryServices/magazyny/backupProtectionIntents/odczyt | Wyświetl listę wszystkich intencji ochrony kopii zapasowej |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

Może wyświetlać usługi kopii zapasowej, ale nie może wprowadzać zmian

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. RecoveryServices/Locations/allocatedStamp/Read | Operacja getallocatedstamp jest operacją wewnętrzną używaną przez usługę |
> | Microsoft. RecoveryServices/magazyny/backupFabrics/operationResults/odczyt | Zwraca stan operacji |
> | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/operationResults/Read | Pobiera wynik operacji wykonanej na kontenerze ochrony. |
> | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/operationResults/odczyt | Pobiera wynik operacji wykonanej na elementach chronionych. |
> | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/operationsStatus/odczyt | Zwraca stan operacji wykonanej na elementach chronionych. |
> | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/Read | Zwraca szczegóły obiektu chronionego elementu |
> | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/protectedItems/recoveryPoints/odczyt | Pobierz punkty odzyskiwania dla elementów chronionych. |
> | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/odczyt | Zwraca wszystkie zarejestrowane kontenery |
> | Microsoft. RecoveryServices/magazyny/backupJobs/operationResults/odczyt | Zwraca wynik operacji zadania. |
> | Microsoft. RecoveryServices/magazyny/backupJobs/odczyt | Zwraca wszystkie obiekty zadań |
> | Microsoft. RecoveryServices/magazyny/backupJobsExport/akcja | Eksportuj zadania |
> | Microsoft. RecoveryServices/magazyny/backupOperationResults/odczyt | Zwraca wynik operacji tworzenia kopii zapasowej dla magazynu Recovery Services. |
> | Microsoft. RecoveryServices/magazyny/backupPolicies/operationResults/odczyt | Pobierz wyniki operacji zasad. |
> | Microsoft. RecoveryServices/magazyny/backupPolicies/odczyt | Zwraca wszystkie zasady ochrony |
> | Microsoft. RecoveryServices/magazyny/backupProtectedItems/odczyt | Zwraca listę wszystkich elementów chronionych. |
> | Microsoft. RecoveryServices/magazyny/backupProtectionContainers/odczyt | Zwraca wszystkie kontenery należące do subskrypcji |
> | Microsoft. RecoveryServices/magazyny/backupUsageSummaries/odczyt | Zwraca podsumowania elementów chronionych i serwerów chronionych dla Recovery Services. |
> | Microsoft. RecoveryServices/magazyny/extendedInformation/odczyt | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu? |
> | Microsoft. RecoveryServices/magazyny/monitoringAlerts/odczyt | Pobiera alerty dla magazynu usługi Recovery Services. |
> | Microsoft. RecoveryServices/magazyny/odczyt | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "magazyn" |
> | Microsoft. RecoveryServices/magazyny/registeredIdentities/operationResults/odczyt | Operacja Pobierz wyniki operacji umożliwia pobieranie stanu operacji i wyniku operacji przesłanej asynchronicznie |
> | Microsoft. RecoveryServices/magazyny/registeredIdentities/odczyt | Operacja Pobierz kontenery umożliwia pobieranie kontenerów zarejestrowanych dla zasobu. |
> | Microsoft. RecoveryServices/magazyny/backupstorageconfig/odczyt | Zwraca konfigurację magazynu dla magazynu Recovery Services. |
> | Microsoft. RecoveryServices/magazyny/backupconfig/odczyt | Zwraca konfigurację magazynu Recovery Services. |
> | Microsoft. RecoveryServices/magazyny/backupOperations/odczyt | Zwraca stan operacji kopii zapasowej dla magazynu Recovery Services. |
> | Microsoft. RecoveryServices/magazyny/backupPolicies/operacje/odczyt | Pobierz stan operacji zasad. |
> | Microsoft. RecoveryServices/magazyny/backupEngines/odczyt | Zwraca wszystkie serwery zarządzania kopiami zapasowymi zarejestrowane w magazynie. |
> | Microsoft. RecoveryServices/magazyny/backupFabrics/backupProtectionIntent/odczyt | Pobierz cel ochrony kopii zapasowej |
> | Microsoft. RecoveryServices/magazyny/backupFabrics/protectionContainers/Items/Read | Pobierz wszystkie elementy w kontenerze |
> | Microsoft. RecoveryServices/Locations/backupStatus/Action | Sprawdź stan kopii zapasowej Recovery Services magazynów |
> | Microsoft. RecoveryServices/magazyny/monitoringConfigurations/* |  |
> | Microsoft. RecoveryServices/magazyny/monitoringAlerts/zapis | Rozwiązuje alert. |
> | Microsoft. RecoveryServices/Operations/Read | Operacja zwraca listę operacji dla dostawcy zasobów |
> | Microsoft. RecoveryServices/Locations/operationStatus/Read | Pobiera stan operacji dla danej operacji |
> | Microsoft. RecoveryServices/magazyny/backupProtectionIntents/odczyt | Wyświetl listę wszystkich intencji ochrony kopii zapasowej |
> | Microsoft. RecoveryServices/magazyny/użycia/odczyt | Zwraca szczegóły użycia magazynu Recovery Servicesowego. |
> | Microsoft. RecoveryServices/Locations/backupValidateFeatures/Action | Weryfikuj funkcje |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

Umożliwia zarządzanie klasycznymi kontami magazynu, ale nie umożliwia uzyskiwania do nich dostępu.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. ClassicStorage/storageAccounts/* | Tworzenie kont magazynu i zarządzanie nimi |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. ResourceHealth/availabilityStatuses/odczyt | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="classic-storage-account-key-operator-service-role"></a>Rola usługi operatora kluczy klasycznego konta magazynu

Operatorzy kluczy klasycznego konta magazynu mogą wyświetlać i ponownie generować klucze na klasycznych kontach magazynu

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. ClassicStorage/storageAccounts/ListKeys/akcja | Wyświetla listę kluczy dostępu dla kont magazynu. |
> | Microsoft. ClassicStorage/storageAccounts/regeneratekey/akcja | Generuje ponownie istniejące klucze dostępu dla konta magazynu. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="data-box-contributor"></a>Współautor urządzenie Data Box

Umożliwia zarządzanie wszystko w obszarze usługi urządzenie Data Box z wyjątkiem udzielenia dostępu innym osobom.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. ResourceHealth/availabilityStatuses/odczyt | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | Microsoft. DATAbox/* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="data-box-reader"></a>urządzenie Data Box czytelnik

Umożliwia zarządzanie usługą urządzenie Data Box, z wyjątkiem tworzenia kolejności lub edytowania szczegółów kolejności i udzielania dostępu innym osobom.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. DATAbox/*/Read |  |
> | Microsoft. DATAbox/Jobs/listsecrets/akcja |  |
> | Microsoft. DATAbox/Jobs/listcredentials/akcja | Wyświetla listę nieszyfrowanych poświadczeń związanych z kolejnością. |
> | Microsoft. DATAbox/Locations/availableSkus/Action | Ta metoda zwraca listę dostępnych jednostek SKU. |
> | Microsoft. DATAbox/Locations/validateInputs/Action | Ta metoda wykonuje wszystkie typy walidacji. |
> | Microsoft. DATAbox/Locations/regionConfiguration/Action | Ta metoda zwraca konfiguracje dla regionu. |
> | Microsoft. DATAbox/Locations/validateAddress/Action | Sprawdza poprawność adresu wysyłkowego i udostępnia alternatywne adresy, jeśli istnieją. |
> | Microsoft. ResourceHealth/availabilityStatuses/odczyt | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="data-lake-analytics-developer"></a>Data Lake Analytics deweloper

Umożliwia przesyłanie własnych zadań, monitorowanie ich i zarządzanie nimi, ale nie tworzenie ani usuwanie kont Data Lake Analytics.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. BigAnalytics/accounts/* |  |
> | Microsoft. DataLakeAnalytics/accounts/* |  |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. ResourceHealth/availabilityStatuses/odczyt | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | Microsoft. BigAnalytics/accounts/Delete |  |
> | Microsoft. BigAnalytics/accounts/TakeOwnership/Action |  |
> | Microsoft. BigAnalytics/accounts/Write |  |
> | Microsoft. DataLakeAnalytics/accounts/Delete | Usuń konto DataLakeAnalytics. |
> | Microsoft. DataLakeAnalytics/accounts/TakeOwnership/Action | Udziel uprawnień do anulowania zadań przesłanych przez innych użytkowników. |
> | Microsoft. DataLakeAnalytics/accounts/Write | Utwórz lub zaktualizuj konto DataLakeAnalytics. |
> | Microsoft. DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Utwórz lub zaktualizuj połączone konto kontach datalakestore konta DataLakeAnalytics. |
> | Microsoft. DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Odłączanie konta kontach datalakestore z konta DataLakeAnalytics. |
> | Microsoft. DataLakeAnalytics/accounts/storageAccounts/Write | Utwórz lub zaktualizuj połączone konto magazynu dla konta DataLakeAnalytics. |
> | Microsoft. DataLakeAnalytics/accounts/storageAccounts/Delete | Odłączanie konta magazynu od konta DataLakeAnalytics. |
> | Microsoft. DataLakeAnalytics/accounts/firewallRules/Write | Utwórz lub zaktualizuj regułę zapory. |
> | Microsoft. DataLakeAnalytics/accounts/firewallRules/Delete | Usuń regułę zapory. |
> | Microsoft. DataLakeAnalytics/accounts/computePolicies/Write | Utwórz lub zaktualizuj zasady obliczeniowe. |
> | Microsoft. DataLakeAnalytics/accounts/computePolicies/Delete | Usuń zasady obliczeń. |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

Umożliwia wyświetlenie wszystkiego, ale nie pozwala na usunięcie ani utworzenie konta magazynu ani zawartego zasobu. Zezwoli również na dostęp do odczytu i zapisu do wszystkich danych znajdujących się na koncie magazynu za pośrednictwem dostępu do kluczy konta magazynu.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Storage/storageAccounts/listKeys/akcja | Zwraca klucze dostępu dla określonego konta magazynu. |
> | Microsoft. Storage/storageAccounts/ListAccountSas/akcja | Zwraca token sygnatury dostępu współdzielonego konta dla określonego konta magazynu. |
> | Microsoft. Storage/storageAccounts/Read | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

Umożliwia zarządzanie kontami magazynu. Zapewnia dostęp do klucza konta, który może służyć do uzyskiwania dostępu do danych za pośrednictwem autoryzacji klucza współużytkowanego.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. Insights/diagnosticSettings/* | Tworzy, aktualizuje lub odczytuje ustawienia diagnostyczne dla Analysis Server |
> | Microsoft. Network/virtualNetworks/Subnets/joinViaServiceEndpoint/Action | Dołącza do podsieci zasoby, takie jak konto magazynu lub baza danych SQL. Brak alertów. |
> | Microsoft. ResourceHealth/availabilityStatuses/odczyt | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Storage/storageAccounts/* | Tworzenie kont magazynu i zarządzanie nimi |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="storage-account-key-operator-service-role"></a>Rola usługi operatora kluczy konta magazynu

Zezwala na wyświetlanie i ponowne generowanie kluczy dostępu do konta magazynu.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Storage/storageAccounts/ListKeys/akcja | Zwraca klucze dostępu dla określonego konta magazynu. |
> | Microsoft. Storage/storageAccounts/regeneratekey/akcja | Generuje ponownie klucze dostępu dla określonego konta magazynu. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="storage-blob-data-contributor"></a>Współautor danych obiektu blob magazynu

Odczytuj, zapisuj i usuwaj kontenery i obiekty blob usługi Azure Storage. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Storage/storageAccounts/blobServices/kontenery/usuwanie | Usuń kontener. |
> | Microsoft. Storage/storageAccounts/blobServices/kontenery/odczyt | Zwraca kontener lub listę kontenerów. |
> | Microsoft. Storage/storageAccounts/blobServices/kontenery/zapis | Modyfikowanie metadanych lub właściwości kontenera. |
> | Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey/Action | Zwraca klucz delegowania użytkownika dla Blob service. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/usuwanie | Usuń obiekt BLOB. |
> | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/odczyt | Zwraca obiekt BLOB lub listę obiektów BLOB. |
> | Microsoft. Storage/storageAccounts/blobServices/Containers/Blobs/Move/Action | Przenosi obiekt BLOB z jednej ścieżki do innej |
> | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/zapis | Zapisz w obiekcie blob. |
> | **NotDataActions** |  |
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

### <a name="storage-blob-data-owner"></a>Właściciel danych obiektów blob magazynu

Zapewnia pełen dostęp do kontenerów obiektów blob i danych usługi Azure Storage, w tym do przypisywania kontroli dostępu POSIX. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Storage/storageAccounts/blobServices/kontenery/* | Pełne uprawnienia do kontenerów. |
> | Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey/Action | Zwraca klucz delegowania użytkownika dla Blob service. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/* | Pełne uprawnienia do obiektów BLOB. |
> | **NotDataActions** |  |
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

Odczytuj i wyświetlaj kontenery usługi Azure Storage oraz obiekty blob. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Storage/storageAccounts/blobServices/kontenery/odczyt | Zwraca kontener lub listę kontenerów. |
> | Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey/Action | Zwraca klucz delegowania użytkownika dla Blob service. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/odczyt | Zwraca obiekt BLOB lub listę obiektów BLOB. |
> | **NotDataActions** |  |
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

Pobierz klucz delegowania użytkownika, którego można następnie użyć do utworzenia sygnatury dostępu współdzielonego dla kontenera lub obiektu BLOB podpisanego przy użyciu poświadczeń usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Tworzenie skojarzeń zabezpieczeń dla delegowania użytkowników](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey/Action | Zwraca klucz delegowania użytkownika dla Blob service. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="storage-file-data-smb-share-contributor"></a>Współautor udziałów SMB danych plików magazynu

Zezwala na dostęp do odczytu, zapisu i usuwania w plikach/katalogach w udziałach plików platformy Azure. Ta rola nie ma wbudowanego odpowiedniku na serwerach plików systemu Windows.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | Microsoft. Storage/storageAccounts/fileServices/udziałów plików/pliki/odczyt | Zwraca plik/folder lub listę plików/folderów. |
> | Microsoft. Storage/storageAccounts/fileServices/udziałów plików/pliki/zapis | Zwraca wynik zapisania pliku lub utworzenia folderu. |
> | Microsoft. Storage/storageAccounts/fileServices/udziałów plików/pliki/usuwanie | Zwraca wynik usunięcia pliku/folderu. |
> | **NotDataActions** |  |
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

### <a name="storage-file-data-smb-share-elevated-contributor"></a>Współautor współautora udziału SMB danych plików magazynu

Zezwala na odczyt, zapis, usuwanie i modyfikowanie list ACL dla plików/katalogów w udziałach plików platformy Azure. Ta rola jest równoważna z listą ACL udziałów plików na serwerach plików systemu Windows.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | Microsoft. Storage/storageAccounts/fileServices/udziałów plików/pliki/odczyt | Zwraca plik/folder lub listę plików/folderów. |
> | Microsoft. Storage/storageAccounts/fileServices/udziałów plików/pliki/zapis | Zwraca wynik zapisania pliku lub utworzenia folderu. |
> | Microsoft. Storage/storageAccounts/fileServices/udziałów plików/pliki/usuwanie | Zwraca wynik usunięcia pliku/folderu. |
> | Microsoft. Storage/storageAccounts/fileServices/udziałów plików/Files/modifypermissions/Action | Zwraca wynik modyfikacji uprawnienia do pliku/folderu. |
> | **NotDataActions** |  |
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

### <a name="storage-file-data-smb-share-reader"></a>Czytnik udziałów SMB danych plików magazynu

Zezwala na dostęp do odczytu dla plików/katalogów w udziałach plików platformy Azure. Ta rola jest równoważna z listą ACL udziału plików odczytywaną na serwerach plików systemu Windows.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | Microsoft. Storage/storageAccounts/fileServices/udziałów plików/pliki/odczyt | Zwraca plik/folder lub listę plików/folderów. |
> | **NotDataActions** |  |
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

Odczytuj, zapisuj i usuwaj kolejki usługi Azure Storage oraz wiadomości w kolejce. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/Delete | Usuń kolejkę. |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/Read | Zwróć kolejkę lub listę kolejek. |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/Write | Modyfikuj metadane lub właściwości kolejki. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/Delete | Usuń co najmniej jeden komunikat z kolejki. |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/Read | Wgląd lub pobranie co najmniej jednego komunikatu z kolejki. |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/Write | Dodaj komunikat do kolejki. |
> | **NotDataActions** |  |
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

### <a name="storage-queue-data-message-processor"></a>Procesor komunikatów danych kolejki magazynu

Wgląd, pobieranie i usuwanie wiadomości z kolejki usługi Azure Storage. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/Read | Wgląd do wiadomości. |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/Process/Action | Pobierz i Usuń komunikat. |
> | **NotDataActions** |  |
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

### <a name="storage-queue-data-message-sender"></a>Nadawca komunikatu o danych kolejki magazynu

Dodawanie komunikatów do kolejki usługi Azure Storage. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/Add/Action | Dodaj komunikat do kolejki. |
> | **NotDataActions** |  |
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

Odczytuj i wyświetlaj kolejki usługi Azure Storage oraz wiadomości w kolejce. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/Read | Zwraca kolejkę lub listę kolejek. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/Read | Wgląd lub pobranie co najmniej jednego komunikatu z kolejki. |
> | **NotDataActions** |  |
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

## <a name="web"></a>Sieć Web


### <a name="azure-maps-data-reader"></a>Azure Maps czytnika danych

Przyznaje dostęp do odczytu danych związanych z mapowaniem z konta usługi Azure Maps.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | Microsoft. Maps/accounts/*/Read |  |
> | **NotDataActions** |  |
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
        "Microsoft.Maps/accounts/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Maps Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="search-service-contributor"></a>Współautor Search Service

Umożliwia zarządzanie usługami wyszukiwania, ale nie umożliwia uzyskiwania do nich dostępu.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. ResourceHealth/availabilityStatuses/odczyt | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Search/searchServices/* | Tworzenie usług wyszukiwania i zarządzanie nimi |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

Umożliwia zarządzanie planami sieci Web dla witryn internetowych, ale nie umożliwia uzyskiwania do nich dostępu.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. ResourceHealth/availabilityStatuses/odczyt | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | Microsoft. Web/dopuszczalna/* | Tworzenie farm serwerów i zarządzanie nimi |
> | Microsoft. Web/hostingEnvironments/Join/Action | Sprzęga App Service Environment |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="website-contributor"></a>Współautor witryny sieci Web

Umożliwia zarządzanie witrynami sieci Web (nie planami internetowymi), ale nie umożliwia uzyskiwania do nich dostępu.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. Insights/Components/* | Tworzenie składników usługi Insights i zarządzanie nimi |
> | Microsoft. ResourceHealth/availabilityStatuses/odczyt | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | Microsoft. Web/Certificates/* | Tworzenie i zarządzanie certyfikatami witryny sieci Web |
> | Microsoft. Web/listSitesAssignedToHostName/odczyt | Pobieranie nazw lokacji przypisanych do nazwy hosta. |
> | Microsoft. Web/dopuszczalna/Join/Action |  |
> | Microsoft. Web/dopuszczalna/odczyt | Pobierz właściwości planu App Service |
> | Microsoft. Web/Sites/* | Tworzenie witryn sieci Web i zarządzanie nimi (Tworzenie witryn wymaga również uprawnień do zapisu w skojarzonym planie App Service) |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

## <a name="containers"></a>Containers


### <a name="acrdelete"></a>AcrDelete

ACR Usuń

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. ContainerRegistry/rejestry/artefakty/usuwanie | Usuń artefakt w rejestrze kontenerów. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="acrimagesigner"></a>AcrImageSigner

ACR podpisującego obraz

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. ContainerRegistry/rejestry/rejestrowanie/zapisywanie | Wypychanie/Ściąganie metadanych zaufania zawartości dla rejestru kontenerów. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="acrpull"></a>AcrPull

ACR ściągaj

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. ContainerRegistry/rejestry/ściąganie/odczyt | Ściąganie lub pobieranie obrazów z rejestru kontenerów. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="acrpush"></a>AcrPush

ACR wypychania

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. ContainerRegistry/rejestry/ściąganie/odczyt | Ściąganie lub pobieranie obrazów z rejestru kontenerów. |
> | Microsoft. ContainerRegistry/rejestry/wypychanie/zapis | Wypchnij lub Zapisz obrazy do rejestru kontenerów. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="acrquarantinereader"></a>AcrQuarantineReader

czytnik danych kwarantanny ACR

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. ContainerRegistry/kancelarie/Kwarantanna/odczyt | Ściąganie lub pobieranie obrazów z kwarantanny z rejestru kontenerów |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

składnik zapisywania danych kwarantanny ACR

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. ContainerRegistry/kancelarie/Kwarantanna/odczyt | Ściąganie lub pobieranie obrazów z kwarantanny z rejestru kontenerów |
> | Microsoft. ContainerRegistry/kancelarie/Kwarantanna/zapis | Zapisywanie/modyfikowanie stanu kwarantanny obrazów z kwarantanny |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="azure-kubernetes-service-cluster-admin-role"></a>Rola administratora klastra usługi Kubernetes platformy Azure

Wyświetl listę akcji poświadczeń administratora klastra.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. ContainerService/managedClusters/listClusterAdminCredential/akcja | Wyświetlanie clusterAdmin poświadczenia zarządzanego klastra |
> | Microsoft. ContainerService/managedClusters/accessProfiles/listCredential/Action | Uzyskiwanie zarządzanego profilu dostępu do klastra według nazwy roli przy użyciu poświadczeń listy |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="azure-kubernetes-service-cluster-user-role"></a>Rola użytkownika klastra usługi Azure Kubernetes Service

Wyświetl listę akcji poświadczeń użytkownika klastra.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. ContainerService/managedClusters/listClusterUserCredential/akcja | Wyświetlanie clusterUser poświadczenia zarządzanego klastra |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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


### <a name="cosmos-db-account-reader-role"></a>Rola czytnika konta Cosmos DB

Może odczytywać Azure Cosmos DB dane konta. Aby zarządzać kontami Azure Cosmos DB, zobacz [współautor konta usługi DocumentDB](#documentdb-account-contributor) .

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. DocumentDB/*/Read | Odczytaj dowolną kolekcję |
> | Microsoft. DocumentDB/databaseAccounts/readonlykeys/akcja | Odczytuje klucze tylko do odczytu konta bazy danych. |
> | Microsoft. Insights/MetricDefinitions/Read | Odczytaj definicje metryk |
> | Microsoft. Insights/Metrics/Read | Odczytaj metryki |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="cosmos-db-operator"></a>Operator Cosmos DB

Umożliwia zarządzanie kontami Azure Cosmos DB, ale nie dostęp do danych w nich. Uniemożliwia dostęp do kluczy konta i parametrów połączenia.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. DocumentDb/databaseAccounts/* |  |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. ResourceHealth/availabilityStatuses/odczyt | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | Microsoft. Network/virtualNetworks/Subnets/joinViaServiceEndpoint/Action | Dołącza do podsieci zasoby, takie jak konto magazynu lub baza danych SQL. Brak alertów. |
> | **NotActions** |  |
> | Microsoft. DocumentDB/databaseAccounts/readonlyKeys/* |  |
> | Microsoft. DocumentDB/databaseAccounts/regenerateKey/* |  |
> | Microsoft. DocumentDB/databaseAccounts/listKeys/* |  |
> | Microsoft. DocumentDB/databaseAccounts/listConnectionStrings/* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="cosmosbackupoperator"></a>CosmosBackupOperator

Może przesłać żądanie przywracania dla bazy danych Cosmos DB lub kontenera dla konta

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. DocumentDB/databaseAccounts/kopia zapasowa/akcja | Prześlij żądanie skonfigurowania kopii zapasowej |
> | Microsoft. DocumentDB/databaseAccounts/Restore/Action | Prześlij żądanie przywrócenia |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

Może zarządzać kontami Azure Cosmos DB. Azure Cosmos DB jest dawniej znany jako DocumentDB.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. DocumentDb/databaseAccounts/* | Tworzenie kont Azure Cosmos DB i zarządzanie nimi |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. ResourceHealth/availabilityStatuses/odczyt | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | Microsoft. Network/virtualNetworks/Subnets/joinViaServiceEndpoint/Action | Dołącza do podsieci zasoby, takie jak konto magazynu lub baza danych SQL. Brak alertów. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="redis-cache-contributor"></a>Współautor Redis Cache

Umożliwia zarządzanie pamięciami podręcznymi Redis, ale nie umożliwia uzyskiwania do nich dostępu.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. cache/Redis/* | Tworzenie pamięci podręcznych Redis i zarządzanie nimi |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. ResourceHealth/availabilityStatuses/odczyt | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

Umożliwia zarządzanie bazami danych SQL, ale nie umożliwia uzyskiwania do nich dostępu. Nie można również zarządzać zasadami związanymi z zabezpieczeniami ani ich nadrzędnymi serwerami SQL.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. ResourceHealth/availabilityStatuses/odczyt | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. SQL/Locations/*/Read |  |
> | Microsoft. SQL/serwery/bazy danych/* | Tworzenie baz danych SQL i zarządzanie nimi |
> | Microsoft. SQL/serwery/odczyt | Zwróć listę serwerów lub pobiera właściwości dla określonego serwera. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | Microsoft. Insights/Metrics/Read | Odczytaj metryki |
> | Microsoft. Insights/metricDefinitions/Read | Odczytaj definicje metryk |
> | **NotActions** |  |
> | Microsoft. SQL/managedInstances/Databases/currentSensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/Databases/recommendedSensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/bazy danych/schematy/tabele/kolumny/sensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/Databases/securityAlertPolicies/* |  |
> | Microsoft. SQL/managedInstances/Databases/sensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/Databases/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/managedInstances/securityAlertPolicies/* |  |
> | Microsoft. SQL/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/serwery/bazy danych/auditingPolicies/* | Edytowanie zasad inspekcji |
> | Microsoft. SQL/serwery/bazy danych/auditingSettings/* | Edytuj ustawienia inspekcji |
> | Microsoft. SQL/serwery/bazy danych/auditRecords/odczyt | Pobieranie rekordów inspekcji obiektów BLOB bazy danych |
> | Microsoft. SQL/serwery/bazy danych/connectionPolicies/* | Edytowanie zasad połączenia |
> | Microsoft. SQL/serwery/bazy danych/currentSensitivityLabels/* |  |
> | Microsoft. SQL/serwery/bazy danych/dataMaskingPolicies/* | Edytowanie zasad maskowania danych |
> | Microsoft. SQL/serwery/bazy danych/extendedAuditingSettings/* |  |
> | Microsoft. SQL/serwery/bazy danych/recommendedSensitivityLabels/* |  |
> | Microsoft. SQL/serwery/bazy danych/schematy/tabele/kolumny/sensitivityLabels/* |  |
> | Microsoft. SQL/serwery/bazy danych/securityAlertPolicies/* | Edytuj zasady alertów zabezpieczeń |
> | Microsoft. SQL/serwery/bazy danych/securityMetrics/* | Edytuj metryki zabezpieczeń |
> | Microsoft. SQL/serwery/bazy danych/sensitivityLabels/* |  |
> | Microsoft. SQL/serwery/bazy danych/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/serwery/bazy danych/vulnerabilityAssessmentScans/* |  |
> | Microsoft. SQL/serwery/bazy danych/vulnerabilityAssessmentSettings/* |  |
> | Microsoft. SQL/serwery/vulnerabilityAssessments/* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

Umożliwia zarządzanie wystąpieniami zarządzanymi SQL i wymaganą konfiguracją sieci, ale nie zapewnia dostępu innym osobom.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. ResourceHealth/availabilityStatuses/odczyt | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Network/networkSecurityGroups/* |  |
> | Microsoft. Network/routeTables/* |  |
> | Microsoft. SQL/Locations/*/Read |  |
> | Microsoft. SQL/managedInstances/* |  |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | Microsoft. Network/virtualNetworks/podsieci/* |  |
> | Microsoft. Network/virtualNetworks/* |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. Insights/Metrics/Read | Odczytaj metryki |
> | Microsoft. Insights/metricDefinitions/Read | Odczytaj definicje metryk |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

Umożliwia zarządzanie zasadami związanymi z zabezpieczeniami serwerów SQL i baz danych, ale nie umożliwia uzyskiwania do nich dostępu.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. Network/virtualNetworks/Subnets/joinViaServiceEndpoint/Action | Dołącza do podsieci zasoby, takie jak konto magazynu lub baza danych SQL. Brak alertów. |
> | Microsoft. ResourceHealth/availabilityStatuses/odczyt | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. SQL/managedInstances/Databases/currentSensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/Databases/recommendedSensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/bazy danych/schematy/tabele/kolumny/sensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/Databases/securityAlertPolicies/* |  |
> | Microsoft. SQL/managedInstances/Databases/sensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/Databases/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/managedInstances/securityAlertPolicies/* |  |
> | Microsoft. SQL/managedInstances/Databases/transparentDataEncryption/* |  |
> | Microsoft. SQL/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/serwery/auditingPolicies/* | Tworzenie zasad inspekcji programu SQL Server i zarządzanie nimi |
> | Microsoft. SQL/serwery/auditingSettings/* | Tworzenie ustawień inspekcji programu SQL Server i zarządzanie nimi |
> | Microsoft. SQL/serwery/extendedAuditingSettings/odczyt | Pobierz szczegóły zasad inspekcji rozszerzonego serwera obiektów BLOB skonfigurowanych na danym serwerze |
> | Microsoft. SQL/serwery/bazy danych/auditingPolicies/* | Tworzenie zasad inspekcji bazy danych programu SQL Server i zarządzanie nimi |
> | Microsoft. SQL/serwery/bazy danych/auditingSettings/* | Tworzenie ustawień inspekcji bazy danych programu SQL Server i zarządzanie nimi |
> | Microsoft. SQL/serwery/bazy danych/auditRecords/odczyt | Pobieranie rekordów inspekcji obiektów BLOB bazy danych |
> | Microsoft. SQL/serwery/bazy danych/connectionPolicies/* | Tworzenie zasad połączenia bazy danych programu SQL Server i zarządzanie nimi |
> | Microsoft. SQL/serwery/bazy danych/currentSensitivityLabels/* |  |
> | Microsoft. SQL/serwery/bazy danych/dataMaskingPolicies/* | Tworzenie zasad maskowania danych bazy danych programu SQL Server i zarządzanie nimi |
> | Microsoft. SQL/serwery/bazy danych/extendedAuditingSettings/odczyt | Pobierz szczegóły zasad rozszerzonej inspekcji obiektów BLOB skonfigurowanych dla danej bazy danych |
> | Microsoft. SQL/serwery/bazy danych/Odczyt | Zwróć listę baz danych lub pobiera właściwości dla określonej bazy danych. |
> | Microsoft. SQL/serwery/bazy danych/recommendedSensitivityLabels/* |  |
> | Microsoft. SQL/serwery/bazy danych/schematy/odczyt | Pobierz schemat bazy danych. |
> | Microsoft. SQL/serwery/bazy danych/schematy/tabele/kolumny/odczyt | Pobierz kolumnę bazy danych. |
> | Microsoft. SQL/serwery/bazy danych/schematy/tabele/kolumny/sensitivityLabels/* |  |
> | Microsoft. SQL/serwery/bazy danych/schematy/tabele/odczyt | Pobierz tabelę bazy danych. |
> | Microsoft. SQL/serwery/bazy danych/securityAlertPolicies/* | Tworzenie zasad alertów zabezpieczeń bazy danych programu SQL Server i zarządzanie nimi |
> | Microsoft. SQL/serwery/bazy danych/securityMetrics/* | Tworzenie metryk zabezpieczeń bazy danych programu SQL Server i zarządzanie nimi |
> | Microsoft. SQL/serwery/bazy danych/sensitivityLabels/* |  |
> | Microsoft. SQL/serwery/bazy danych/transparentDataEncryption/* |  |
> | Microsoft. SQL/serwery/bazy danych/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/serwery/bazy danych/vulnerabilityAssessmentScans/* |  |
> | Microsoft. SQL/serwery/bazy danych/vulnerabilityAssessmentSettings/* |  |
> | Microsoft. SQL/serwery/firewallRules/* |  |
> | Microsoft. SQL/serwery/odczyt | Zwróć listę serwerów lub pobiera właściwości dla określonego serwera. |
> | Microsoft. SQL/serwery/securityAlertPolicies/* | Tworzenie zasad alertów zabezpieczeń programu SQL Server i zarządzanie nimi |
> | Microsoft. SQL/serwery/vulnerabilityAssessments/* |  |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="sql-server-contributor"></a>Współautor SQL Server

Umożliwia zarządzanie serwerami i bazami danych SQL, ale nie umożliwia uzyskiwania do nich dostępu ani zasad związanych z zabezpieczeniami.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. ResourceHealth/availabilityStatuses/odczyt | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. SQL/Locations/*/Read |  |
> | Microsoft. SQL/serwery/* | Tworzenie serwerów SQL i zarządzanie nimi |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | Microsoft. Insights/Metrics/Read | Odczytaj metryki |
> | Microsoft. Insights/metricDefinitions/Read | Odczytaj definicje metryk |
> | **NotActions** |  |
> | Microsoft. SQL/managedInstances/Databases/currentSensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/Databases/recommendedSensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/bazy danych/schematy/tabele/kolumny/sensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/Databases/securityAlertPolicies/* |  |
> | Microsoft. SQL/managedInstances/Databases/sensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/Databases/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/managedInstances/securityAlertPolicies/* |  |
> | Microsoft. SQL/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/serwery/auditingPolicies/* | Edytowanie zasad inspekcji programu SQL Server |
> | Microsoft. SQL/serwery/auditingSettings/* | Edytowanie ustawień inspekcji programu SQL Server |
> | Microsoft. SQL/serwery/bazy danych/auditingPolicies/* | Edytowanie zasad inspekcji bazy danych programu SQL Server |
> | Microsoft. SQL/serwery/bazy danych/auditingSettings/* | Edytowanie ustawień inspekcji bazy danych programu SQL Server |
> | Microsoft. SQL/serwery/bazy danych/auditRecords/odczyt | Pobieranie rekordów inspekcji obiektów BLOB bazy danych |
> | Microsoft. SQL/serwery/bazy danych/connectionPolicies/* | Edytowanie zasad połączenia bazy danych programu SQL Server |
> | Microsoft. SQL/serwery/bazy danych/currentSensitivityLabels/* |  |
> | Microsoft. SQL/serwery/bazy danych/dataMaskingPolicies/* | Edytowanie zasad maskowania danych w bazie danych programu SQL Server |
> | Microsoft. SQL/serwery/bazy danych/extendedAuditingSettings/* |  |
> | Microsoft. SQL/serwery/bazy danych/recommendedSensitivityLabels/* |  |
> | Microsoft. SQL/serwery/bazy danych/schematy/tabele/kolumny/sensitivityLabels/* |  |
> | Microsoft. SQL/serwery/bazy danych/securityAlertPolicies/* | Edytowanie zasad alertów zabezpieczeń bazy danych programu SQL Server |
> | Microsoft. SQL/serwery/bazy danych/securityMetrics/* | Edytuj metryki zabezpieczeń bazy danych programu SQL Server |
> | Microsoft. SQL/serwery/bazy danych/sensitivityLabels/* |  |
> | Microsoft. SQL/serwery/bazy danych/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/serwery/bazy danych/vulnerabilityAssessmentScans/* |  |
> | Microsoft. SQL/serwery/bazy danych/vulnerabilityAssessmentSettings/* |  |
> | Microsoft. SQL/serwery/extendedAuditingSettings/* |  |
> | Microsoft. SQL/serwery/securityAlertPolicies/* | Edytuj zasady alertów zabezpieczeń programu SQL Server |
> | Microsoft. SQL/serwery/vulnerabilityAssessments/* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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


### <a name="azure-event-hubs-data-owner"></a>Właściciel danych Event Hubs platformy Azure

Umożliwia pełny dostęp do zasobów usługi Azure Event Hubs.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. EventHub/* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | Microsoft. EventHub/* |  |
> | **NotDataActions** |  |
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

### <a name="azure-event-hubs-data-receiver"></a>Usługa Azure Event Hubs Data Receiver

Zezwala na odbieranie dostępu do zasobów usługi Azure Event Hubs.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. EventHub/*/eventhubs/consumergroups/Read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | Microsoft. EventHub/*/Receive/Action |  |
> | **NotDataActions** |  |
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

### <a name="azure-event-hubs-data-sender"></a>Nadawca danych Event Hubs platformy Azure

Zezwala na wysyłanie dostępu do zasobów Event Hubs platformy Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. EventHub/*/eventhubs/Read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | Microsoft. EventHub/*/Send/Action |  |
> | **NotDataActions** |  |
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

### <a name="data-factory-contributor"></a>Współautor Data Factory

Twórz fabryki danych i zarządzaj nimi, a także zasobami podrzędnymi.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. DataFactory/datafactors/* | Twórz fabryki danych i zarządzaj nimi. |
> | Microsoft. datafabryka/fabryki/* | Twórz fabryki danych i zarządzaj nimi. |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. ResourceHealth/availabilityStatuses/odczyt | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | Microsoft. EventGrid/eventSubscriptions/Write | Utwórz lub zaktualizuj eventSubscription |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="data-purger"></a>Przeczyszczanie danych

Można przeczyścić dane analityczne

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Insights/Components/*/Read |  |
> | Microsoft. Insights/Components/Przeczyść/akcja | Przeczyszczanie danych z Application Insights |
> | Microsoft. OperationalInsights/obszary robocze/*/Read | Wyświetlanie danych usługi log Analytics |
> | Microsoft. OperationalInsights/obszary robocze/przeczyszczanie/akcja | Usuń określone dane z obszaru roboczego |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="hdinsight-cluster-operator"></a>Operator klastra usługi HDInsight

Umożliwia odczytywanie i modyfikowanie konfiguracji klastrów usługi HDInsight.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. HDInsight/*/Read |  |
> | Microsoft. HDInsight/Klastry/getGatewaySettings/akcja | Pobierz ustawienia bramy dla klastra usługi HDInsight |
> | Microsoft. HDInsight/Klastry/updateGatewaySettings/akcja | Aktualizowanie ustawień bramy dla klastra usługi HDInsight |
> | Microsoft. HDInsight/Klastry/konfiguracje/* |  |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. resources/Deployments/Operations/Read | Pobiera lub wyświetla listę operacji wdrażania. |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="hdinsight-domain-services-contributor"></a>Współautor usług domenowych w usłudze HDInsight

Może odczytywać, tworzyć, modyfikować i usuwać operacje związane z usługami domenowymi, które są potrzebne pakiet Enterprise Security usługi HDInsight

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. AAD/*/Read |  |
> | Microsoft. AAD/domainServices/*/Read |  |
> | Microsoft. AAD/domainServices/oucontainer/* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

Współautor Log Analytics może odczytywać wszystkie dane monitorowania i edytować ustawienia monitorowania. Edytowanie ustawień monitorowania obejmuje dodanie rozszerzenia maszyny wirtualnej do maszyn wirtualnych; Odczytywanie kluczy konta magazynu w celu skonfigurowania kolekcji dzienników z usługi Azure Storage; Tworzenie i Konfigurowanie kont usługi Automation; Dodawanie rozwiązań; i Konfigurowanie diagnostyki platformy Azure dla wszystkich zasobów platformy Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | */read | Odczytuj zasoby wszystkich typów, z wyjątkiem kluczy tajnych. |
> | Microsoft. Automation/automationAccounts/* |  |
> | Microsoft. ClassicCompute/virtualMachines/Extensions/* |  |
> | Microsoft. ClassicStorage/storageAccounts/listKeys/akcja | Wyświetla listę kluczy dostępu dla kont magazynu. |
> | Microsoft. COMPUTE/virtualMachines/Extensions/* |  |
> | Microsoft. HybridCompute/komputery/rozszerzenia/zapis | Instaluje lub aktualizuje rozszerzenia usługi Azure Arc |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. Insights/diagnosticSettings/* | Tworzy, aktualizuje lub odczytuje ustawienia diagnostyczne dla Analysis Server |
> | Microsoft. OperationalInsights/* |  |
> | Microsoft. OperationsManagement/* |  |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/ResourceGroups/Deployments/* |  |
> | Microsoft. Storage/storageAccounts/listKeys/akcja | Zwraca klucze dostępu dla określonego konta magazynu. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

Log Analytics Reader może wyświetlać i przeszukiwać wszystkie dane monitorowania, a także wyświetlać ustawienia monitorowania, w tym Wyświetlanie konfiguracji diagnostyki platformy Azure na wszystkich zasobach platformy Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | */read | Odczytuj zasoby wszystkich typów, z wyjątkiem kluczy tajnych. |
> | Microsoft. OperationalInsights/obszary robocze/analiza/kwerenda/akcja | Wyszukaj przy użyciu nowego aparatu. |
> | Microsoft. OperationalInsights/obszary robocze/wyszukiwanie/akcja | Wykonuje zapytanie wyszukiwania |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | Microsoft. OperationalInsights/Workspaces/sharedKeys/Read | Pobiera klucze wspólne dla obszaru roboczego. Klucze te służą do łączenia agentów programu Microsoft Operational Insights z obszarem roboczym. |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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


### <a name="blockchain-member-node-access-preview"></a>Dostęp do węzła elementu członkowskiego łańcucha bloków (wersja zapoznawcza)

Zezwala na dostęp do węzłów składowych łańcucha bloków

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. łańcucha bloków/blockchainMembers/transactionNodes/Read | Pobiera lub Wyświetla istniejące węzły transakcji składowych łańcucha bloków. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | Microsoft. łańcucha bloków/blockchainMembers/transactionNodes/Connect/Action | Nawiązuje połączenie z węzłem transakcji elementu członkowskiego łańcucha bloków. |
> | **NotDataActions** |  |
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

## <a name="ai--machine-learning"></a>AI i uczenie maszynowe


### <a name="cognitive-services-contributor"></a>Współautor Cognitive Services

Umożliwia tworzenie, odczytywanie, aktualizowanie, usuwanie i zarządzanie kluczami Cognitive Services.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. CognitiveServices/* |  |
> | Microsoft. Features/Features/Read | Pobiera funkcje subskrypcji. |
> | Microsoft. Features/Providers/Features/Read | Pobiera funkcję subskrypcji dla danego dostawcy zasobów. |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. Insights/diagnosticSettings/* | Tworzy, aktualizuje lub odczytuje ustawienia diagnostyczne dla Analysis Server |
> | Microsoft. Insights/logDefinitions/Read | Odczytaj definicje dzienników |
> | Microsoft. Insights/metricdefinitions/Read | Odczytaj definicje metryk |
> | Microsoft. Insights/Metrics/Read | Odczytaj metryki |
> | Microsoft. ResourceHealth/availabilityStatuses/odczyt | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/Deployments/Operations/Read | Pobiera lub wyświetla listę operacji wdrażania. |
> | Microsoft. resources/subscriptions/operationresults/Read | Pobierz wyniki operacji subskrypcji. |
> | Microsoft. resources/subscriptions/Read | Pobiera listę subskrypcji. |
> | Microsoft. resources/subscriptions/ResourceGroups/Deployments/* |  |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="cognitive-services-data-reader-preview"></a>Czytnik danych Cognitive Services (wersja zapoznawcza)

Umożliwia odczytywanie danych Cognitive Services.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | Microsoft. CognitiveServices/*/Read |  |
> | **NotDataActions** |  |
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

### <a name="cognitive-services-user"></a>Cognitive Services użytkownika

Umożliwia odczytywanie i wyświetlanie listy kluczy Cognitive Services.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. CognitiveServices/*/Read |  |
> | Microsoft. CognitiveServices/accounts/ListKeys/Action | Klucze list |
> | Microsoft. Insights/alertRules/Read | Przeczytaj klasyczny alert dotyczący metryki |
> | Microsoft. Insights/diagnosticSettings/Read | Odczytaj ustawienie diagnostyczne zasobu |
> | Microsoft. Insights/logDefinitions/Read | Odczytaj definicje dzienników |
> | Microsoft. Insights/metricdefinitions/Read | Odczytaj definicje metryk |
> | Microsoft. Insights/Metrics/Read | Odczytaj metryki |
> | Microsoft. ResourceHealth/availabilityStatuses/odczyt | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/Operations/Read | Pobiera lub wyświetla listę operacji wdrażania. |
> | Microsoft. resources/subscriptions/operationresults/Read | Pobierz wyniki operacji subskrypcji. |
> | Microsoft. resources/subscriptions/Read | Pobiera listę subskrypcji. |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | Microsoft. CognitiveServices/* |  |
> | **NotDataActions** |  |
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


### <a name="spatial-anchors-account-contributor"></a>Współautor konta kotwic przestrzennych

Umożliwia zarządzanie zakotwiczeniami przestrzennymi na Twoim koncie, ale nie ich usuwanie

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Create/Action | Utwórz kotwice przestrzenne |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/odnajdywanie/odczyt | Odkryj kotwice w pobliżu |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/właściwości/odczyt | Pobierz właściwości kotwic przestrzennych |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Query/Read | Lokalizowanie kotwic przestrzennych |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/submitdiag/Read | Przesyłanie danych diagnostycznych w celu poprawy jakości usługi zakotwiczeń przestrzennych platformy Azure |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Write | Aktualizuj właściwości kotwic przestrzennych |
> | **NotDataActions** |  |
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

### <a name="spatial-anchors-account-owner"></a>Właściciel konta zakotwiczeń przestrzennych

Umożliwia zarządzanie zakotwiczeniami przestrzennymi na Twoim koncie, w tym ich usuwanie

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Create/Action | Utwórz kotwice przestrzenne |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Delete | Usuń kotwice przestrzenne |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/odnajdywanie/odczyt | Odkryj kotwice w pobliżu |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/właściwości/odczyt | Pobierz właściwości kotwic przestrzennych |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Query/Read | Lokalizowanie kotwic przestrzennych |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/submitdiag/Read | Przesyłanie danych diagnostycznych w celu poprawy jakości usługi zakotwiczeń przestrzennych platformy Azure |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Write | Aktualizuj właściwości kotwic przestrzennych |
> | **NotDataActions** |  |
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

### <a name="spatial-anchors-account-reader"></a>Czytnik konta zakotwiczeń przestrzennych

Umożliwia lokalizowanie i odczytywanie właściwości kotwic przestrzennych na Twoim koncie

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/odnajdywanie/odczyt | Odkryj kotwice w pobliżu |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/właściwości/odczyt | Pobierz właściwości kotwic przestrzennych |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Query/Read | Lokalizowanie kotwic przestrzennych |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/submitdiag/Read | Przesyłanie danych diagnostycznych w celu poprawy jakości usługi zakotwiczeń przestrzennych platformy Azure |
> | **NotDataActions** |  |
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


### <a name="api-management-service-contributor"></a>Współautor usługi API Management

Może zarządzać usługą i interfejsami API

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. ApiManagement/Service/* | Utwórz usługę API Management i zarządzaj nią |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. ResourceHealth/availabilityStatuses/odczyt | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="api-management-service-operator-role"></a>Rola operatora usługi API Management

Może zarządzać usługą, ale nie z interfejsów API

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. ApiManagement/Service/*/Read | Odczytaj API Management wystąpienia usługi |
> | Microsoft. ApiManagement/Service/kopia zapasowa/akcja | Usługa tworzenia kopii zapasowych API Management do określonego kontenera na koncie magazynu podanego przez użytkownika |
> | Microsoft. ApiManagement/Service/Delete | Usuń wystąpienie usługi API Management |
> | Microsoft. ApiManagement/Service/managedeployments/Action | Zmień jednostkę SKU/jednostki, Dodaj/Usuń wdrożenia regionalne usługi API Management |
> | Microsoft. ApiManagement/Service/Read | Odczytaj metadane dla wystąpienia usługi API Management |
> | Microsoft. ApiManagement/Service/Restore/Action | Przywracanie usługi API Management z określonego kontenera na koncie magazynu podanego przez użytkownika |
> | Microsoft. ApiManagement/Service/updatecertificate/Action | Przekazywanie certyfikatu TLS/SSL dla usługi API Management |
> | Microsoft. ApiManagement/Service/updatehostname/Action | Instalowanie, aktualizowanie lub usuwanie niestandardowych nazw domen dla usługi API Management |
> | Microsoft. ApiManagement/Service/Write | Utwórz lub zaktualizuj wystąpienie usługi API Management |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. ResourceHealth/availabilityStatuses/odczyt | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | Microsoft. ApiManagement/Service/users/klucze/odczyt | Pobierz klucze skojarzone z użytkownikiem |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="api-management-service-reader-role"></a>Rola czytnika usługi API Management

Dostęp tylko do odczytu do usługi i interfejsów API

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. ApiManagement/Service/*/Read | Odczytaj API Management wystąpienia usługi |
> | Microsoft. ApiManagement/Service/Read | Odczytaj metadane dla wystąpienia usługi API Management |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. ResourceHealth/availabilityStatuses/odczyt | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | Microsoft. ApiManagement/Service/users/klucze/odczyt | Pobierz klucze skojarzone z użytkownikiem |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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
> | **Akcje dataactions** |  |
> | Microsoft. AppConfiguration/configurationStores/*/Read |  |
> | Microsoft. AppConfiguration/configurationStores/*/Write |  |
> | Microsoft. AppConfiguration/configurationStores/*/Delete |  |
> | **NotDataActions** |  |
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

Zezwala na dostęp do odczytu do danych konfiguracji aplikacji.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | Microsoft. AppConfiguration/configurationStores/*/Read |  |
> | **NotDataActions** |  |
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

### <a name="azure-service-bus-data-owner"></a>Właściciel danych Azure Service Bus

Umożliwia pełny dostęp do zasobów Azure Service Bus.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. ServiceBus/* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | Microsoft. ServiceBus/* |  |
> | **NotDataActions** |  |
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

### <a name="azure-service-bus-data-receiver"></a>Azure Service Bus odbiorca danych

Umożliwia uzyskanie dostępu do zasobów Azure Service Bus.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. ServiceBus/*/Queues/Read |  |
> | Microsoft. ServiceBus/*/topics/Read |  |
> | Microsoft. ServiceBus/*/topics/subscriptions/Read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | Microsoft. ServiceBus/*/Receive/Action |  |
> | **NotDataActions** |  |
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

### <a name="azure-service-bus-data-sender"></a>Nadawca danych Azure Service Bus

Zezwala na dostęp do Azure Service Bus zasobów.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. ServiceBus/*/Queues/Read |  |
> | Microsoft. ServiceBus/*/topics/Read |  |
> | Microsoft. ServiceBus/*/topics/subscriptions/Read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | Microsoft. ServiceBus/*/Send/Action |  |
> | **NotDataActions** |  |
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

### <a name="azure-stack-registration-owner"></a>Właściciel rejestracji Azure Stack

Umożliwia zarządzanie rejestracjami Azure Stack.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. AzureStack/registrations/Products/*/Action |  |
> | Microsoft. AzureStack/rejestracje/produkty/odczyt | Pobiera właściwości produktu Azure Stack Marketplace |
> | Microsoft. AzureStack/rejestracje/odczyt | Pobiera właściwości rejestracji Azure Stack |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="eventgrid-eventsubscription-contributor"></a>Współautor EventGrid EventSubscription

Umożliwia zarządzanie operacjami subskrypcji zdarzeń EventGrid.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. EventGrid/eventSubscriptions/* |  |
> | Microsoft. EventGrid/topicTypes/eventSubscriptions/Read | Wyświetl listę globalnych subskrypcji zdarzeń według typu tematu |
> | Microsoft. EventGrid/Locations/eventSubscriptions/Read | Utwórz listę regionalnych subskrypcji zdarzeń |
> | Microsoft. EventGrid/Locations/topicTypes/eventSubscriptions/Read | Utwórz listę regionalnych subskrypcji zdarzeń według |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="eventgrid-eventsubscription-reader"></a>EventGrid EventSubscription

Umożliwia odczytanie subskrypcji zdarzeń EventGrid.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. EventGrid/eventSubscriptions/odczyt | Odczytaj eventSubscription |
> | Microsoft. EventGrid/topicTypes/eventSubscriptions/Read | Wyświetl listę globalnych subskrypcji zdarzeń według typu tematu |
> | Microsoft. EventGrid/Locations/eventSubscriptions/Read | Utwórz listę regionalnych subskrypcji zdarzeń |
> | Microsoft. EventGrid/Locations/topicTypes/eventSubscriptions/Read | Utwórz listę regionalnych subskrypcji zdarzeń według |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="intelligent-systems-account-contributor"></a>Współautor konta systemów inteligentnych

Umożliwia zarządzanie kontami inteligentnych systemów, ale nie umożliwia uzyskiwania do nich dostępu.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. IntelligentSystems/accounts/* | Tworzenie kont inteligentnych systemów i zarządzanie nimi |
> | Microsoft. ResourceHealth/availabilityStatuses/odczyt | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

Umożliwia zarządzanie aplikacjami logiki, ale nie umożliwia zmiany dostępu do nich.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. ClassicStorage/storageAccounts/listKeys/akcja | Wyświetla listę kluczy dostępu dla kont magazynu. |
> | Microsoft. ClassicStorage/storageAccounts/odczyt | Zwróć konto magazynu z danym kontem. |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. Insights/metricAlerts/* |  |
> | Microsoft. Insights/diagnosticSettings/* | Tworzy, aktualizuje lub odczytuje ustawienia diagnostyczne dla Analysis Server |
> | Microsoft. Insights/logdefinitions/* | To uprawnienie jest niezbędne dla użytkowników, którzy potrzebują dostępu do dzienników aktywności za pośrednictwem portalu. Wyświetl listę kategorii dziennika w dzienniku aktywności. |
> | Microsoft. Insights/metricDefinitions/* | Odczytaj definicje metryk (lista dostępnych typów metryk dla zasobu). |
> | Microsoft. Logic/* | Zarządza zasobami Logic Apps. |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/operationresults/Read | Pobierz wyniki operacji subskrypcji. |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Storage/storageAccounts/ListKeys/akcja | Zwraca klucze dostępu dla określonego konta magazynu. |
> | Microsoft. Storage/storageAccounts/Read | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | Microsoft. Web/connectionGateways/* | Utwórz bramę połączenia i zarządzaj nią. |
> | Microsoft. Web/Connections/* | Utwórz połączenie i Zarządzaj nim. |
> | Microsoft. Web/customApis/* | Tworzy niestandardowy interfejs API i zarządza nim. |
> | Microsoft. Web/dopuszczalna/Join/Action |  |
> | Microsoft. Web/dopuszczalna/odczyt | Pobierz właściwości planu App Service |
> | Microsoft. Web/Sites/Functions/listSecrets/Action | Utwórz listę wpisów tajnych funkcji. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

Umożliwia odczytywanie, Włączanie i wyłączanie aplikacji logiki, ale nie ich edytowanie ani aktualizowanie.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. Insights/alertRules/*/Read | Czytaj reguły alertów szczegółowych informacji |
> | Microsoft. Insights/metricAlerts/*/Read |  |
> | Microsoft. Insights/diagnosticSettings/*/Read | Pobiera ustawienia diagnostyczne dla Logic Apps |
> | Microsoft. Insights/metricDefinitions/*/Read | Pobiera dostępne metryki dla Logic Apps. |
> | Microsoft. Logic/*/Read | Odczytuje Logic Apps zasoby. |
> | Microsoft. Logic/Workflows/Disable/Action | Wyłącza przepływ pracy. |
> | Microsoft. Logic/Workflows/Enable/Action | Włącza przepływ pracy. |
> | Microsoft. Logic/Workflows/Validate/Action | Sprawdza poprawność przepływu pracy. |
> | Microsoft. resources/Deployments/Operations/Read | Pobiera lub wyświetla listę operacji wdrażania. |
> | Microsoft. resources/subscriptions/operationresults/Read | Pobierz wyniki operacji subskrypcji. |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | Microsoft. Web/connectionGateways/*/Read | Odczytaj bramy połączeń. |
> | Microsoft. Web/Connections/*/Read | Odczyt połączeń. |
> | Microsoft. Web/customApis/*/Read | Odczytaj niestandardowy interfejs API. |
> | Microsoft. Web/dopuszczalna/odczyt | Pobierz właściwości planu App Service |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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
> | Microsoft. ManagedIdentity/Resourceidentity/odczyt | Pobiera istniejącą tożsamość przypisanego użytkownika |
> | Microsoft. ManagedIdentity/Resourceidentity/Write | Tworzy nową tożsamość przypisanego użytkownika lub aktualizuje Tagi skojarzone z istniejącą tożsamością przypisanego użytkownika |
> | Microsoft. ManagedIdentity/Resourceidentity/Delete | Usuwa istniejącą tożsamość przypisanego użytkownika |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

Odczytaj i przypisz tożsamość przypisaną przez użytkownika

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. ManagedIdentity/Resourceidentity/*/Read |  |
> | Microsoft. ManagedIdentity/Resourceidentity/*/Assign/Action |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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


### <a name="azure-sentinel-contributor"></a>Współautor wskaźnikowego platformy Azure

Współautor wskaźnikowego platformy Azure

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. SecurityInsights/* |  |
> | Microsoft. OperationalInsights/obszary robocze/analiza/kwerenda/akcja | Wyszukaj przy użyciu nowego aparatu. |
> | Microsoft. OperationalInsights/obszary robocze/*/Read | Wyświetlanie danych usługi log Analytics |
> | Microsoft. OperationalInsights/Workspaces/savedSearches/* |  |
> | Microsoft. OperationsManagement/Solutions/Read | Pobierz wyjście z rozwiązania OMS |
> | Microsoft. OperationalInsights/obszary robocze/zapytanie/odczyt | Uruchamianie zapytań dotyczących danych w obszarze roboczym |
> | Microsoft. OperationalInsights/Workspaces/Query/*/Read |  |
> | Microsoft. OperationalInsights/obszary robocze/DataSources/odczyt | Pobierz źródła danych w obszarze roboczym. |
> | Microsoft. Insights/skoroszyty/* |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="azure-sentinel-reader"></a>Czytnik wskaźnikowy platformy Azure

Czytnik wskaźnikowy platformy Azure

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. SecurityInsights/*/Read |  |
> | Microsoft. SecurityInsights/dataConnectorsCheckRequirements/Action | Sprawdź autoryzację i licencję użytkownika |
> | Microsoft. OperationalInsights/obszary robocze/analiza/kwerenda/akcja | Wyszukaj przy użyciu nowego aparatu. |
> | Microsoft. OperationalInsights/obszary robocze/*/Read | Wyświetlanie danych usługi log Analytics |
> | Microsoft. OperationalInsights/Workspaces/LinkedServices/Read | Pobierz połączone usługi w podanym obszarze roboczym. |
> | Microsoft. OperationalInsights/Workspaces/savedSearches/Read | Pobiera zapisane zapytanie wyszukiwania |
> | Microsoft. OperationsManagement/Solutions/Read | Pobierz wyjście z rozwiązania OMS |
> | Microsoft. OperationalInsights/obszary robocze/zapytanie/odczyt | Uruchamianie zapytań dotyczących danych w obszarze roboczym |
> | Microsoft. OperationalInsights/Workspaces/Query/*/Read |  |
> | Microsoft. OperationalInsights/obszary robocze/DataSources/odczyt | Pobierz źródła danych w obszarze roboczym. |
> | Microsoft. Insights/skoroszyty/przeczytane | Odczytaj skoroszyt |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="azure-sentinel-responder"></a>Obiekt odpowiadający wskaźnikowi platformy Azure

Obiekt odpowiadający wskaźnikowi platformy Azure

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. SecurityInsights/*/Read |  |
> | Microsoft. SecurityInsights/dataConnectorsCheckRequirements/Action | Sprawdź autoryzację i licencję użytkownika |
> | Microsoft. SecurityInsights/przypadki/* |  |
> | Microsoft. SecurityInsights/incydenty/* |  |
> | Microsoft. OperationalInsights/obszary robocze/analiza/kwerenda/akcja | Wyszukaj przy użyciu nowego aparatu. |
> | Microsoft. OperationalInsights/obszary robocze/*/Read | Wyświetlanie danych usługi log Analytics |
> | Microsoft. OperationalInsights/obszary robocze/DataSources/odczyt | Pobierz źródła danych w obszarze roboczym. |
> | Microsoft. OperationalInsights/Workspaces/savedSearches/Read | Pobiera zapisane zapytanie wyszukiwania |
> | Microsoft. OperationsManagement/Solutions/Read | Pobierz wyjście z rozwiązania OMS |
> | Microsoft. OperationalInsights/obszary robocze/zapytanie/odczyt | Uruchamianie zapytań dotyczących danych w obszarze roboczym |
> | Microsoft. OperationalInsights/Workspaces/Query/*/Read |  |
> | Microsoft. OperationalInsights/obszary robocze/DataSources/odczyt | Pobierz źródła danych w obszarze roboczym. |
> | Microsoft. Insights/skoroszyty/przeczytane | Odczytaj skoroszyt |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="key-vault-contributor"></a>Współautor Key Vault

Umożliwia Zarządzanie magazynami kluczy, ale nie umożliwia uzyskiwania do nich dostępu.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft./Magazyn kluczy/* |  |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | Microsoft./Magazyn kluczy/lokalizacji/deletedVaults/przeczyszczanie/akcja | Przeczyść usunięty nietrwale Magazyn kluczy |
> | Microsoft./Magazyn kluczy/hsmPools/* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

Wyświetl i zaktualizuj uprawnienia dla Security Center. Te same uprawnienia, jak rola czytelnik zabezpieczeń, mogą także aktualizować zasady zabezpieczeń i odrzucać alerty i zalecenia.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. Authorization/policyAssignments/* | Tworzenie przypisań zasad i zarządzanie nimi |
> | Microsoft. Authorization/policyDefinitions/* | Tworzenie definicji zasad i zarządzanie nimi |
> | Microsoft. Authorization/policySetDefinitions/* | Tworzenie zestawów zasad i zarządzanie nimi |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. Management/managementGroups/Read | Wyświetl listę grup zarządzania dla uwierzytelnionego użytkownika. |
> | Microsoft. operationalInsights/obszary robocze/*/Read | Wyświetlanie danych usługi log Analytics |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Security/* | Tworzenie i zarządzanie składnikami i zasadami zabezpieczeń |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="security-assessment-contributor"></a>Współautor oceny zabezpieczeń

Umożliwia wypychanie ocen do Security Center

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Security/Assessments/Write | Utwórz lub zaktualizuj oceny zabezpieczeń w ramach subskrypcji |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="security-manager-legacy"></a>Security Manager (starsza wersja)

Jest to Starsza rola. Zamiast tego należy użyć administratora zabezpieczeń.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. ClassicCompute/*/Read | Odczytaj informacje o konfiguracji klasyczne maszyny wirtualne |
> | Microsoft. ClassicCompute/virtualMachines/*/Write | Zapisz konfigurację dla klasycznych maszyn wirtualnych |
> | Microsoft. ClassicNetwork/*/Read | Odczytaj informacje o konfiguracji dotyczące sieci klasycznej |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. ResourceHealth/availabilityStatuses/odczyt | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Security/* | Tworzenie i zarządzanie składnikami i zasadami zabezpieczeń |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

Wyświetl uprawnienia dla Security Center. Może wyświetlać zalecenia, alerty, zasady zabezpieczeń i Stany zabezpieczeń, ale nie może wprowadzać zmian.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. operationalInsights/obszary robocze/*/Read | Wyświetlanie danych usługi log Analytics |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Security/*/Read | Odczytaj składniki i zasady zabezpieczeń |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | Microsoft. Management/managementGroups/Read | Wyświetl listę grup zarządzania dla uwierzytelnionego użytkownika. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

Umożliwia łączenie, uruchamianie, ponowne uruchamianie i zamykanie maszyn wirtualnych w Azure DevTest Labs.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. COMPUTE/availabilitySets/Read | Pobierz właściwości zestawu dostępności |
> | Microsoft. COMPUTE/virtualMachines/*/Read | Odczytywanie właściwości maszyny wirtualnej (rozmiary maszyn wirtualnych, stan środowiska uruchomieniowego, rozszerzenia maszyn wirtualnych itp.) |
> | Microsoft. COMPUTE/virtualMachines/deallocate/Action | Wyłącza maszynę wirtualną i zwalnia zasoby obliczeniowe |
> | Microsoft. COMPUTE/virtualMachines/Read | Pobierz właściwości maszyny wirtualnej |
> | Microsoft. COMPUTE/virtualMachines/restart/akcja | Uruchamia ponownie maszynę wirtualną |
> | Microsoft. COMPUTE/virtualMachines/Start/akcja | Uruchamia maszynę wirtualną |
> | Microsoft. wspólny/*/Read | Odczytywanie właściwości laboratorium |
> | Microsoft. wspólny/Labs/claimAnyVm/akcja | Zarezerwuj losową maszynę wirtualną z dodaną odpowiedzialnością w laboratorium. |
> | Microsoft. wspólny/Labs/środowisko/akcja | Twórz maszyny wirtualne w laboratorium. |
> | Microsoft. wspólny/Labs/ensureCurrentUserProfile/akcja | Upewnij się, że bieżący użytkownik ma prawidłowy profil w laboratorium. |
> | Microsoft. wspólny/Labs/formuły/usuwanie | Usuń formuły. |
> | Microsoft. wspólny/Labs/formuły/odczyt | Odczytaj formuły. |
> | Microsoft. wspólny/Labs/formuły/zapis | Dodaj lub zmodyfikuj formuły. |
> | Microsoft. wspólny/Labs/policySets/evaluatePolicies/Action | Szacuje zasady laboratorium. |
> | Microsoft. wspólny/Labs/virtualMachines/roszczeń/akcja | Przejmowanie na własność istniejącej maszyny wirtualnej |
> | Microsoft. wspólny/Labs/virtualmachines/listApplicableSchedules/Action | Wyświetla listę odpowiednich harmonogramów uruchamiania/zatrzymywania (jeśli istnieją). |
> | Microsoft. wspólny/Labs/virtualMachines/getRdpFileContents/Action | Pobiera ciąg, który reprezentuje zawartość pliku RDP dla maszyny wirtualnej |
> | Microsoft. Network/loadBalancers/backendAddressPools/Join/Action | Sprzęga pulę adresów zaplecza modułu równoważenia obciążenia. Brak alertów. |
> | Microsoft. Network/loadBalancers/inboundNatRules/Join/Action | Przyłącza do przychodzącej reguły NAT modułu równoważenia obciążenia. Brak alertów. |
> | Microsoft. Network/networkInterfaces/*/Read | Odczytywanie właściwości interfejsu sieciowego (na przykład wszystkich modułów równoważenia obciążenia, które są częścią interfejsu sieciowego) |
> | Microsoft. Network/networkInterfaces/Join/Action | Przyłączanie maszyny wirtualnej do interfejsu sieciowego. Brak alertów. |
> | Microsoft. Network/networkInterfaces/odczyt | Pobiera definicję interfejsu sieciowego.  |
> | Microsoft. Network/networkInterfaces/Write | Tworzy interfejs sieciowy lub aktualizuje istniejący interfejs sieciowy.  |
> | Microsoft. Network/adresów publicipaddress/*/Read | Odczytywanie właściwości publicznego adresu IP |
> | Microsoft.Network/publicIPAddresses/join/action | Przyłącza do publicznego adresu IP. Brak alertów. |
> | Microsoft.Network/publicIPAddresses/read | Pobiera definicję publicznego adresu IP. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Przyłącza do sieci wirtualnej. Brak alertów. |
> | Microsoft. resources/Deployments/Operations/Read | Pobiera lub wyświetla listę operacji wdrażania. |
> | Microsoft. resources/Deployments/Read | Pobiera lub wyświetla listę wdrożeń. |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Storage/storageAccounts/listKeys/akcja | Zwraca klucze dostępu dla określonego konta magazynu. |
> | **NotActions** |  |
> | Microsoft. COMPUTE/virtualMachines/vmSizes/odczyt | Wyświetla dostępne rozmiary, do których można zaktualizować maszynę wirtualną |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

Umożliwia tworzenie i usuwanie zarządzanych laboratoriów z kont laboratorium platformy Azure oraz zarządzanie nimi.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. LabServices/labAccounts/*/Read |  |
> | Microsoft. LabServices/labAccounts/createLab/akcja | Utwórz laboratorium na koncie laboratorium. |
> | Microsoft. LabServices/labAccounts/Sizes/getRegionalAvailability/Action |  |
> | Microsoft. LabServices/labAccounts/getRegionalAvailability/akcja | Uzyskaj informacje o dostępności regionalnej dla każdej kategorii rozmiaru skonfigurowanej w ramach konta laboratorium |
> | Microsoft. LabServices/labAccounts/getPricingAndAvailability/akcja | Skorzystaj z cen i dostępności kombinacji rozmiarów, lokalizacje geograficzne i systemów operacyjnych dla konta laboratorium. |
> | Microsoft. LabServices/labAccounts/getRestrictionsAndUsage/akcja | Pobierz podstawowe ograniczenia i użycie dla tej subskrypcji |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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


### <a name="application-insights-component-contributor"></a>Współautor składnika Application Insights

Może zarządzać składnikami Application Insights

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. Insights/alertRules/* | Tworzenie klasycznych reguł alertów i zarządzanie nimi |
> | Microsoft. Insights/metricAlerts/* | Tworzenie nowych reguł alertów i zarządzanie nimi |
> | Microsoft. Insights/Components/* | Tworzenie składników usługi Insights i zarządzanie nimi |
> | Microsoft. Insights/webtests/* | Tworzenie testów sieci Web usługi Insights i zarządzanie nimi |
> | Microsoft. ResourceHealth/availabilityStatuses/odczyt | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="application-insights-snapshot-debugger"></a>Application Insights Snapshot Debugger

Daje użytkownikowi uprawnienia do wyświetlania i pobierania migawek debugowania zebranych za pomocą Snapshot Debugger Application Insights. Należy zauważyć, że te uprawnienia nie są uwzględnione w rolach [właściciel](#owner) lub [współautor](#contributor) .

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. Insights/Components/*/Read |  |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

Może odczytywać wszystkie dane monitorowania i edytować ustawienia monitorowania. Zobacz też Rozpoczynanie [pracy z rolami, uprawnieniami i zabezpieczeniami przy użyciu Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | */read | Odczytuj zasoby wszystkich typów, z wyjątkiem kluczy tajnych. |
> | Microsoft. AlertsManagement/alerty/* |  |
> | Microsoft. AlertsManagement/alertsSummary/* |  |
> | Microsoft. Insights/actiongroups/* |  |
> | Microsoft. Insights/activityLogAlerts/* |  |
> | Microsoft. Insights/AlertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. Insights/Components/* | Tworzenie składników usługi Insights i zarządzanie nimi |
> | Microsoft. Insights/DiagnosticSettings/* | Tworzy, aktualizuje lub odczytuje ustawienia diagnostyczne dla Analysis Server |
> | Microsoft. Insights/EventType | Wyświetl listę zdarzeń dziennika aktywności (zdarzenia zarządzania) w subskrypcji. To uprawnienie dotyczy zarówno dostępu programowego, jak i portalu do dziennika aktywności. |
> | Microsoft. Insights/LogDefinitions/* | To uprawnienie jest niezbędne dla użytkowników, którzy potrzebują dostępu do dzienników aktywności za pośrednictwem portalu. Wyświetl listę kategorii dziennika w dzienniku aktywności. |
> | Microsoft. Insights/metricalerts/* |  |
> | Microsoft. Insights/MetricDefinitions/* | Odczytaj definicje metryk (lista dostępnych typów metryk dla zasobu). |
> | Microsoft. Insights/Metrics/* | Odczytaj metryki dla zasobu. |
> | Microsoft. Insights/Register/Action | Zarejestruj dostawcę usługi Microsoft Insights |
> | Microsoft. Insights/scheduledqueryrules/* |  |
> | Microsoft. Insights/webtests/* | Tworzenie testów sieci Web usługi Insights i zarządzanie nimi |
> | Microsoft. Insights/skoroszyty/* |  |
> | Microsoft. Insights/privateLinkScopes/* |  |
> | Microsoft. Insights/privateLinkScopeOperationStatuses/* |  |
> | Microsoft. OperationalInsights/obszary robocze/zapis | Tworzy nowy obszar roboczy lub łączy z istniejącym obszarem roboczym, podając identyfikator klienta z istniejącego obszaru roboczego. |
> | Microsoft. OperationalInsights/Workspaces/intelligencepacks/* | Odczytaj/Zapisz/Usuń pakiety rozwiązań usługi log Analytics. |
> | Microsoft. OperationalInsights/Workspaces/savedSearches/* | Odczyt/zapis/usuwanie zapisanych wyszukiwań usługi log Analytics. |
> | Microsoft. OperationalInsights/obszary robocze/wyszukiwanie/akcja | Wykonuje zapytanie wyszukiwania |
> | Microsoft. OperationalInsights/obszary robocze/sharedKeys/akcja | Pobiera klucze wspólne dla obszaru roboczego. Klucze te służą do łączenia agentów programu Microsoft Operational Insights z obszarem roboczym. |
> | Microsoft. OperationalInsights/Workspaces/storageinsightconfigs/* | Odczyt/zapis/Usuwanie konfiguracji usługi Magazyn analizy dzienników. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | Microsoft. Monitor obciążenia został/Monitors/* |  |
> | Microsoft. Monitor obciążenia został/notificationSettings/* |  |
> | Microsoft. AlertsManagement/smartDetectorAlertRules/* |  |
> | Microsoft. AlertsManagement/actionRules/* |  |
> | Microsoft. AlertsManagement/smartGroups/* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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
        "Microsoft.Insights/privateLinkScopes/*",
        "Microsoft.Insights/privateLinkScopeOperationStatuses/*",
        "Microsoft.OperationalInsights/workspaces/write",
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

### <a name="monitoring-metrics-publisher"></a>Wydawca metryk monitorowania

Włącza publikowanie metryk dla zasobów platformy Azure

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Insights/Register/Action | Zarejestruj dostawcę usługi Microsoft Insights |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | Microsoft. Insights/Metrics/Write | Metryki zapisu |
> | **NotDataActions** |  |
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

### <a name="monitoring-reader"></a>Czytnik monitorowania

Może odczytywać wszystkie dane monitorowania (metryki, dzienniki itp.). Zobacz też Rozpoczynanie [pracy z rolami, uprawnieniami i zabezpieczeniami przy użyciu Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | */read | Odczytuj zasoby wszystkich typów, z wyjątkiem kluczy tajnych. |
> | Microsoft. OperationalInsights/obszary robocze/wyszukiwanie/akcja | Wykonuje zapytanie wyszukiwania |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

Może zapisywać udostępnione skoroszyty.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Insights/skoroszyty/zapis | Utwórz lub zaktualizuj skoroszyt |
> | Microsoft. Insights/skoroszyty/usuwanie | Usuwanie skoroszytu |
> | Microsoft. Insights/skoroszyty/przeczytane | Odczytaj skoroszyt |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="workbook-reader"></a>Czytelnik skoroszytu

Może odczytywać skoroszyty.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Insights/skoroszyty/przeczytane | Odczytaj skoroszyt |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

## <a name="management--governance"></a>Zarządzanie i nadzór


### <a name="automation-job-operator"></a>Operator zadania automatyzacji

Twórz zadania i zarządzaj nimi za pomocą elementów Runbook usługi Automation.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. Automation/automationAccounts/hybridRunbookWorkerGroups/odczyt | Odczytuje zasoby hybrydowego procesu roboczego elementu Runbook |
> | Microsoft. Automation/automationAccounts/Jobs/Read | Pobiera zadanie Azure Automation |
> | Microsoft. Automation/automationAccounts/Jobs/Resume/Action | Wznawia zadanie Azure Automation |
> | Microsoft. Automation/automationAccounts/Jobs/akcja | Kończy zadanie Azure Automation |
> | Microsoft. Automation/automationAccounts/Jobs/Streams/Read | Pobiera strumień zadania Azure Automation |
> | Microsoft. Automation/automationAccounts/Jobs/Suspend/Action | Wstrzymuje zadanie Azure Automation |
> | Microsoft. Automation/automationAccounts/Jobs/Write | Tworzy zadanie Azure Automation |
> | Microsoft. Automation/automationAccounts/Jobs/Output/Read | Pobiera dane wyjściowe zadania |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

Operatory automatyzacji mogą uruchamiać, zatrzymywać, wstrzymywać i wznawiać zadania

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. Automation/automationAccounts/hybridRunbookWorkerGroups/odczyt | Odczytuje zasoby hybrydowego procesu roboczego elementu Runbook |
> | Microsoft. Automation/automationAccounts/Jobs/Read | Pobiera zadanie Azure Automation |
> | Microsoft. Automation/automationAccounts/Jobs/Resume/Action | Wznawia zadanie Azure Automation |
> | Microsoft. Automation/automationAccounts/Jobs/akcja | Kończy zadanie Azure Automation |
> | Microsoft. Automation/automationAccounts/Jobs/Streams/Read | Pobiera strumień zadania Azure Automation |
> | Microsoft. Automation/automationAccounts/Jobs/Suspend/Action | Wstrzymuje zadanie Azure Automation |
> | Microsoft. Automation/automationAccounts/Jobs/Write | Tworzy zadanie Azure Automation |
> | Microsoft. Automation/automationAccounts/jobSchedules/odczyt | Pobiera harmonogram zadań Azure Automation |
> | Microsoft. Automation/automationAccounts/jobSchedules/Write | Tworzy harmonogram zadań Azure Automation |
> | Microsoft. Automation/automationAccounts/linkedWorkspace/odczyt | Pobiera obszar roboczy połączony z kontem usługi Automation |
> | Microsoft. Automation/automationAccounts/Read | Pobiera konto Azure Automation |
> | Microsoft. Automation/automationAccounts/Runbooks/Read | Pobiera element Runbook Azure Automation |
> | Microsoft. Automation/automationAccounts/Schedules/Read | Pobiera zasób harmonogramu Azure Automation |
> | Microsoft. Automation/automationAccounts/Schedules/Write | Tworzy lub aktualizuje zasób harmonogramu Azure Automation |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. ResourceHealth/availabilityStatuses/odczyt | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. Automation/automationAccounts/Jobs/Output/Read | Pobiera dane wyjściowe zadania |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="automation-runbook-operator"></a>Operator elementu Runbook usługi Automation

Odczytywanie właściwości elementu Runbook — aby można było tworzyć zadania elementu Runbook.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. Automation/automationAccounts/Runbooks/Read | Pobiera element Runbook Azure Automation |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="azure-connected-machine-onboarding"></a>Dołączanie maszyny połączonej z platformą Azure

Można dołączyć maszyny połączone z platformą Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. HybridCompute/Machines/Read | Odczytaj wszystkie maszyny usługi Azure Arc |
> | Microsoft. HybridCompute/Machines/Write | Zapisuje maszyny usługi Azure Arc |
> | Microsoft. GuestConfiguration/guestConfigurationAssignments/odczyt | Pobierz przypisanie konfiguracji gościa. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="azure-connected-machine-resource-administrator"></a>Administrator zasobów maszyny połączonej z platformą Azure

Może odczytywać, zapisywać, usuwać i ponownie dołączać maszyny wirtualne platformy Azure.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. HybridCompute/Machines/Read | Odczytaj wszystkie maszyny usługi Azure Arc |
> | Microsoft. HybridCompute/Machines/Write | Zapisuje maszyny usługi Azure Arc |
> | Microsoft. HybridCompute/Machines/Delete | Usuwa maszyny usługi Azure Arc |
> | Microsoft. HybridCompute/Machines/reconnect/Action | Ponownie nawiązuje połączenie z maszynami usługi Azure Arc |
> | Microsoft. HybridCompute/komputery/rozszerzenia/zapis | Instaluje lub aktualizuje rozszerzenia usługi Azure Arc |
> | Microsoft. HybridCompute/*/Read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

Zezwala na dostęp do odczytu do danych rozliczeniowych

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. rozliczenia/*/Read | Odczytaj informacje o rozliczeniach |
> | Microsoft. Commerce/*/Read |  |
> | Microsoft. zużycie/*/Read |  |
> | Microsoft. Management/managementGroups/Read | Wyświetl listę grup zarządzania dla uwierzytelnionego użytkownika. |
> | Microsoft. CostManagement/*/Read |  |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

Może zarządzać definicjami planu, ale nie należy ich przypisywać.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. plan/plany/* | Twórz definicje planów lub artefakty planów i zarządzaj nimi. |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

Może przypisywać istniejące opublikowane plany, ale nie może tworzyć nowych planów. Należy zauważyć, że to działanie działa tylko wtedy, gdy przypisanie zostało wykonane przy użyciu tożsamości zarządzanej przypisanej przez użytkownika.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. plan/blueprintAssignments/* | Utwórz przypisania planu i zarządzaj nimi. |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="cost-management-contributor"></a>Współautor Cost Management

Umożliwia wyświetlanie kosztów i zarządzanie konfiguracją kosztów (np. budżetów, eksportów)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. zużycie/* |  |
> | Microsoft. CostManagement/* |  |
> | Microsoft. rozliczenia/billingPeriods/odczyt |  |
> | Microsoft. resources/subscriptions/Read | Pobiera listę subskrypcji. |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | Microsoft. Advisor/konfiguracje/odczyt | Pobierz konfiguracje |
> | Microsoft. Advisor/zalecenia/odczyt | Odczytuje zalecenia |
> | Microsoft. Management/managementGroups/Read | Wyświetl listę grup zarządzania dla uwierzytelnionego użytkownika. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="cost-management-reader"></a>Cost Management czytelnik

Może wyświetlać dane i konfigurację kosztów (np. budżetów, eksportów)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. zużycie/*/Read |  |
> | Microsoft. CostManagement/*/Read |  |
> | Microsoft. rozliczenia/billingPeriods/odczyt |  |
> | Microsoft. resources/subscriptions/Read | Pobiera listę subskrypcji. |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | Microsoft. Advisor/konfiguracje/odczyt | Pobierz konfiguracje |
> | Microsoft. Advisor/zalecenia/odczyt | Odczytuje zalecenia |
> | Microsoft. Management/managementGroups/Read | Wyświetl listę grup zarządzania dla uwierzytelnionego użytkownika. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

Umożliwia tworzenie zasobów aplikacji zarządzanej.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | */read | Odczytuj zasoby wszystkich typów, z wyjątkiem kluczy tajnych. |
> | Microsoft. Solutions/Applications/* |  |
> | Microsoft. Solutions/Register/Action | Zarejestruj się w rozwiązaniach. |
> | Microsoft. resources/subscriptions/resourceGroups/* |  |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

Umożliwia odczytywanie i wykonywanie akcji dotyczących zasobów aplikacji zarządzanej

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | */read | Odczytuj zasoby wszystkich typów, z wyjątkiem kluczy tajnych. |
> | Microsoft. Solutions/Applications/Read | Pobiera listę aplikacji. |
> | Microsoft. Solutions/*/Action |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

Umożliwia odczytanie zasobów w zarządzanej aplikacji i zażądanie dostępu JIT.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | */read | Odczytuj zasoby wszystkich typów, z wyjątkiem kluczy tajnych. |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. Solutions/jitRequests/* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

Rola usuwania przypisania rejestracji usług zarządzanych umożliwia użytkownikom zarządzającym dzierżawę usuwanie przypisania rejestracji przypisanego do swojej dzierżawy.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. ManagedServices/registrationAssignments/odczyt | Pobiera listę przypisań rejestracji usług zarządzanych. |
> | Microsoft. ManagedServices/registrationAssignments/Delete | Usuwa przypisanie rejestracji usług zarządzanych. |
> | Microsoft. ManagedServices/operationStatuses/odczyt | Odczytuje stan operacji dla zasobu. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

Rola współautora grupy zarządzania

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Management/managementGroups/Delete | Usuń grupę zarządzania. |
> | Microsoft. Management/managementGroups/Read | Wyświetl listę grup zarządzania dla uwierzytelnionego użytkownika. |
> | Microsoft. Management/managementGroups/subskrypcje/usuwanie | Anuluj skojarzenie subskrypcji z grupą zarządzania. |
> | Microsoft. Management/managementGroups/subskrypcje/zapis | Kojarzy istniejącą subskrypcję z grupą zarządzania. |
> | Microsoft. Management/managementGroups/Write | Utwórz lub Zaktualizuj grupę zarządzania. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="management-group-reader"></a>Czytelnik grupy zarządzania

Rola czytelnika grupy zarządzania

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Management/managementGroups/Read | Wyświetl listę grup zarządzania dla uwierzytelnionego użytkownika. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="new-relic-apm-account-contributor"></a>Nowe współautor konta APM Relic

Umożliwia zarządzanie kontami i aplikacjami New Relic Application Performance Management, ale nie umożliwia uzyskiwania do nich dostępu.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. ResourceHealth/availabilityStatuses/odczyt | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | NewRelic. APM/accounts/* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="policy-insights-data-writer-preview"></a>Moduł zapisywania danych usługi Policy Insights (wersja zapoznawcza)

Zezwala na dostęp do odczytu do zasad zasobów i dostęp do zapisu dla zdarzeń zasad składnika zasobów.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/policyassignments/Read | Pobierz informacje o przypisaniu zasad. |
> | Microsoft. Authorization/PolicyDefinitions/Read | Pobierz informacje o definicji zasad. |
> | Microsoft. Authorization/policysetdefinitions/Read | Pobierz informacje o definicji zestawu zasad. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | Microsoft. PolicyInsights/checkDataPolicyCompliance/Action | Sprawdź stan zgodności danego składnika względem zasad dotyczących danych. |
> | Microsoft. PolicyInsights/policyEvents/logDataEvents/akcja | Rejestruj zdarzenia zasad składnika zasobów. |
> | **NotDataActions** |  |
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

Użytkownicy z prawami do tworzenia/modyfikowania zasad dotyczących zasobów, tworzenia biletów pomocy technicznej i odczytywania zasobów/hierarchii.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | */read | Odczytuj zasoby wszystkich typów, z wyjątkiem kluczy tajnych. |
> | Microsoft. Authorization/policyassignments/* | Tworzenie przypisań zasad i zarządzanie nimi |
> | Microsoft. Authorization/PolicyDefinitions/* | Tworzenie definicji zasad i zarządzanie nimi |
> | Microsoft. Authorization/policysetdefinitions/* | Tworzenie zestawów zasad i zarządzanie nimi |
> | Microsoft. PolicyInsights/* |  |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="site-recovery-contributor"></a>Współautor Site Recovery

Umożliwia zarządzanie usługą Site Recovery z wyjątkiem tworzenia magazynu i przypisywania ról

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. Network/virtualNetworks/odczyt | Pobierz definicję sieci wirtualnej |
> | Microsoft. RecoveryServices/Locations/allocatedStamp/Read | Operacja getallocatedstamp jest operacją wewnętrzną używaną przez usługę |
> | Microsoft. RecoveryServices/Locations/allocateStamp/Action | AllocateStamp jest operacją wewnętrzną używaną przez usługę |
> | Microsoft. RecoveryServices/magazyny/certyfikaty/zapis | Operacja Aktualizuj certyfikat zasobu aktualizuje certyfikat poświadczeń zasobu/magazynu. |
> | Microsoft. RecoveryServices/magazyny/extendedInformation/* | Utwórz rozszerzone informacje powiązane z magazynem i zarządzaj nimi |
> | Microsoft. RecoveryServices/magazyny/odczyt | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "magazyn" |
> | Microsoft. RecoveryServices/magazyny/refreshContainers/odczyt |  |
> | Microsoft. RecoveryServices/magazyny/registeredIdentities/* | Tworzenie zarejestrowanych tożsamości i zarządzanie nimi |
> | Microsoft. RecoveryServices/magazyny/replicationAlertSettings/* | Utwórz lub zaktualizuj ustawienia alertu replikacji |
> | Microsoft. RecoveryServices/magazyny/replicationEvents/odczyt | Odczytaj wszystkie zdarzenia |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/* | Tworzenie sieci szkieletowej replikacji i zarządzanie nimi |
> | Microsoft. RecoveryServices/magazyny/replicationJobs/* | Tworzenie zadań replikacji i zarządzanie nimi |
> | Microsoft. RecoveryServices/magazyny/replicationPolicies/* | Tworzenie zasad replikacji i zarządzanie nimi |
> | Microsoft. RecoveryServices/magazyny/replicationRecoveryPlans/* | Tworzenie planów odzyskiwania i zarządzanie nimi |
> | Microsoft. RecoveryServices/magazyny/storageConfig/* | Tworzenie i zarządzanie konfiguracją magazynu Recovery Services magazynu |
> | Microsoft. RecoveryServices/magazyny/tokenInfo/odczyt |  |
> | Microsoft. RecoveryServices/magazyny/użycia/odczyt | Zwraca szczegóły użycia magazynu Recovery Servicesowego. |
> | Microsoft. RecoveryServices/magazyny/vaultTokens/odczyt | Operacja token magazynu umożliwia pobieranie tokenu magazynu dla operacji zaplecza na poziomie magazynu. |
> | Microsoft. RecoveryServices/magazyny/monitoringAlerts/* | Odczytaj alerty dla magazynu usługi Recovery Services |
> | Microsoft. RecoveryServices/magazyny/monitoringConfigurations/notificationConfiguration/odczyt |  |
> | Microsoft. ResourceHealth/availabilityStatuses/odczyt | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Storage/storageAccounts/Read | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | Microsoft. RecoveryServices/magazyny/replicationOperationStatus/odczyt | Odczytaj wszystkie Stany operacji replikacji magazynu |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="site-recovery-operator"></a>Operator Site Recovery

Umożliwia przełączenie w tryb failover i powrót po awarii, ale nie wykonywanie innych operacji zarządzania Site Recovery

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. Network/virtualNetworks/odczyt | Pobierz definicję sieci wirtualnej |
> | Microsoft. RecoveryServices/Locations/allocatedStamp/Read | Operacja getallocatedstamp jest operacją wewnętrzną używaną przez usługę |
> | Microsoft. RecoveryServices/Locations/allocateStamp/Action | AllocateStamp jest operacją wewnętrzną używaną przez usługę |
> | Microsoft. RecoveryServices/magazyny/extendedInformation/odczyt | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu? |
> | Microsoft. RecoveryServices/magazyny/odczyt | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "magazyn" |
> | Microsoft. RecoveryServices/magazyny/refreshContainers/odczyt |  |
> | Microsoft. RecoveryServices/magazyny/registeredIdentities/operationResults/odczyt | Operacja Pobierz wyniki operacji umożliwia pobieranie stanu operacji i wyniku operacji przesłanej asynchronicznie |
> | Microsoft. RecoveryServices/magazyny/registeredIdentities/odczyt | Operacja Pobierz kontenery umożliwia pobieranie kontenerów zarejestrowanych dla zasobu. |
> | Microsoft. RecoveryServices/magazyny/replicationAlertSettings/odczyt | Odczytaj wszystkie ustawienia alertów |
> | Microsoft. RecoveryServices/magazyny/replicationEvents/odczyt | Odczytaj wszystkie zdarzenia |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/checkConsistency/akcja | Sprawdza spójność sieci szkieletowej |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/odczyt | Odczytaj wszystkie sieci szkieletowe |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/reassociateGateway/akcja | Skojarz ponownie bramę |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/renewcertificate/akcja | Odnów certyfikat sieci szkieletowej |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationNetworks/odczyt | Odczytaj wszystkie sieci |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationNetworks/replicationNetworkMappings/Read | Odczytaj wszystkie mapowania sieci |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/odczyt | Odczytaj wszystkie kontenery ochrony |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/Read | Odczytaj dowolne elementy podlegające ochronie |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/akcja | Zastosuj punkt odzyskiwania |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/akcja | Zatwierdzanie trybu failover |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/akcja | Planowane przełączanie do trybu failover |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/Read | Odczytaj wszystkie chronione elementy |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/odczyt | Odczytaj wszystkie punkty odzyskiwania replikacji |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/akcja | Napraw replikację |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/Włącz ochronę/akcję | Włącz ponownie ochronę chronionego elementu |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/switchprotection/Action | Przełącz kontener ochrony |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/akcja | Test pracy w trybie failover |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/akcja | Czyszczenie testu pracy w trybie failover |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/akcja | Tryb failover |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/akcja | Aktualizowanie usługi mobilności |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/Read | Odczytaj wszelkie mapowania kontenerów ochrony |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationRecoveryServicesProviders/odczyt | Odczytaj wszystkich dostawców Recovery Services |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/Action | Odśwież dostawcę |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationStorageClassifications/odczyt | Odczytaj wszystkie klasyfikacje magazynu |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/Read | Odczytaj dowolne mapowania klasyfikacji magazynu |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationvCenters/odczyt | Odczytaj dowolne vCenter |
> | Microsoft. RecoveryServices/magazyny/replicationJobs/* | Tworzenie zadań replikacji i zarządzanie nimi |
> | Microsoft. RecoveryServices/magazyny/replicationPolicies/odczyt | Odczytaj wszystkie zasady |
> | Microsoft. RecoveryServices/magazyny/replicationRecoveryPlans/failoverCommit/akcja | Plan odzyskiwania zatwierdzania w trybie failover |
> | Microsoft. RecoveryServices/magazyny/replicationRecoveryPlans/plannedFailover/akcja | Plan odzyskiwania planowanej pracy w trybie failover |
> | Microsoft. RecoveryServices/magazyny/replicationRecoveryPlans/odczyt | Zapoznaj się z planami odzyskiwania |
> | Microsoft. RecoveryServices/magazyny/replicationRecoveryPlans/ponowne Włączanie/działanie | Ponowne włączanie ochrony planu odzyskiwania |
> | Microsoft. RecoveryServices/magazyny/replicationRecoveryPlans/testFailover/akcja | Plan odzyskiwania testowego trybu failover |
> | Microsoft. RecoveryServices/magazyny/replicationRecoveryPlans/testFailoverCleanup/akcja | Plan odzyskiwania oczyszczania testów w trybie failover |
> | Microsoft. RecoveryServices/magazyny/replicationRecoveryPlans/unplannedFailover/akcja | Plan odzyskiwania w trybie failover |
> | Microsoft. RecoveryServices/magazyny/monitoringAlerts/* | Odczytaj alerty dla magazynu usługi Recovery Services |
> | Microsoft. RecoveryServices/magazyny/monitoringConfigurations/notificationConfiguration/odczyt |  |
> | Microsoft. RecoveryServices/magazyny/storageConfig/odczyt |  |
> | Microsoft. RecoveryServices/magazyny/tokenInfo/odczyt |  |
> | Microsoft. RecoveryServices/magazyny/użycia/odczyt | Zwraca szczegóły użycia magazynu Recovery Servicesowego. |
> | Microsoft. RecoveryServices/magazyny/vaultTokens/odczyt | Operacja token magazynu umożliwia pobieranie tokenu magazynu dla operacji zaplecza na poziomie magazynu. |
> | Microsoft. ResourceHealth/availabilityStatuses/odczyt | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Storage/storageAccounts/Read | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="site-recovery-reader"></a>Site Recovery czytelnik

Umożliwia wyświetlanie stanu Site Recovery, ale nie wykonywanie innych operacji zarządzania

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. RecoveryServices/Locations/allocatedStamp/Read | Operacja getallocatedstamp jest operacją wewnętrzną używaną przez usługę |
> | Microsoft. RecoveryServices/magazyny/extendedInformation/odczyt | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu? |
> | Microsoft. RecoveryServices/magazyny/monitoringAlerts/odczyt | Pobiera alerty dla magazynu usługi Recovery Services. |
> | Microsoft. RecoveryServices/magazyny/monitoringConfigurations/notificationConfiguration/odczyt |  |
> | Microsoft. RecoveryServices/magazyny/odczyt | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "magazyn" |
> | Microsoft. RecoveryServices/magazyny/refreshContainers/odczyt |  |
> | Microsoft. RecoveryServices/magazyny/registeredIdentities/operationResults/odczyt | Operacja Pobierz wyniki operacji umożliwia pobieranie stanu operacji i wyniku operacji przesłanej asynchronicznie |
> | Microsoft. RecoveryServices/magazyny/registeredIdentities/odczyt | Operacja Pobierz kontenery umożliwia pobieranie kontenerów zarejestrowanych dla zasobu. |
> | Microsoft. RecoveryServices/magazyny/replicationAlertSettings/odczyt | Odczytaj wszystkie ustawienia alertów |
> | Microsoft. RecoveryServices/magazyny/replicationEvents/odczyt | Odczytaj wszystkie zdarzenia |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/odczyt | Odczytaj wszystkie sieci szkieletowe |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationNetworks/odczyt | Odczytaj wszystkie sieci |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationNetworks/replicationNetworkMappings/Read | Odczytaj wszystkie mapowania sieci |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/odczyt | Odczytaj wszystkie kontenery ochrony |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/Read | Odczytaj dowolne elementy podlegające ochronie |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/Read | Odczytaj wszystkie chronione elementy |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/odczyt | Odczytaj wszystkie punkty odzyskiwania replikacji |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/Read | Odczytaj wszelkie mapowania kontenerów ochrony |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationRecoveryServicesProviders/odczyt | Odczytaj wszystkich dostawców Recovery Services |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationStorageClassifications/odczyt | Odczytaj wszystkie klasyfikacje magazynu |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/Read | Odczytaj dowolne mapowania klasyfikacji magazynu |
> | Microsoft. RecoveryServices/magazyny/replicationFabrics/replicationvCenters/odczyt | Odczytaj dowolne vCenter |
> | Microsoft. RecoveryServices/magazyny/replicationJobs/odczyt | Odczytaj wszystkie zadania |
> | Microsoft. RecoveryServices/magazyny/replicationPolicies/odczyt | Odczytaj wszystkie zasady |
> | Microsoft. RecoveryServices/magazyny/replicationRecoveryPlans/odczyt | Zapoznaj się z planami odzyskiwania |
> | Microsoft. RecoveryServices/magazyny/storageConfig/odczyt |  |
> | Microsoft. RecoveryServices/magazyny/tokenInfo/odczyt |  |
> | Microsoft. RecoveryServices/magazyny/użycia/odczyt | Zwraca szczegóły użycia magazynu Recovery Servicesowego. |
> | Microsoft. RecoveryServices/magazyny/vaultTokens/odczyt | Operacja token magazynu umożliwia pobieranie tokenu magazynu dla operacji zaplecza na poziomie magazynu. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

Pozwala tworzyć żądania pomocy technicznej i zarządzać nimi

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="tag-contributor"></a>Współautor tagów

Umożliwia zarządzanie tagami w jednostkach bez zapewniania dostępu do samych jednostek.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. resources/subscriptions/resourceGroups/Resources/Read | Pobiera zasoby dla grupy zasobów. |
> | Microsoft. resources/subscriptions/sources/Read | Pobiera zasoby subskrypcji. |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | Microsoft. resources/Tags/* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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


### <a name="biztalk-contributor"></a>Współautor BizTalk

Umożliwia zarządzanie usługami BizTalk Services, ale nie umożliwia uzyskiwania do nich dostępu.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. BizTalkServices/BizTalk/* | Tworzenie usługi BizTalk Services i zarządzanie nimi |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. ResourceHealth/availabilityStatuses/odczyt | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

### <a name="scheduler-job-collections-contributor"></a>Współautor kolekcji zadań usługi Scheduler

Umożliwia zarządzanie kolekcjami zadań harmonogramu, ale nie umożliwia uzyskiwania do nich dostępu.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Akcje** |  |
> | Microsoft. Authorization/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. Insights/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. ResourceHealth/availabilityStatuses/odczyt | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | Microsoft. resources/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Scheduler/jobcollections/* | Tworzenie kolekcji zadań i zarządzanie nimi |
> | Microsoft. Support/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
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

- [Dopasuj dostawcę zasobów do usługi](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Role niestandardowe platformy Azure](custom-roles.md)
- [Uprawnienia w usłudze Azure Security Center](../security-center/security-center-permissions.md)
