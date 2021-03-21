---
title: Role wbudowane platformy Azure — RBAC Azure
description: W tym artykule opisano wbudowane role platformy Azure na potrzeby kontroli dostępu opartej na rolach (RBAC) na platformie Azure. Zawiera listę akcji, nienaruszonych, akcji dataactions i NotDataActions.
services: active-directory
ms.service: role-based-access-control
ms.topic: reference
ms.workload: identity
author: rolyon
ms.author: rolyon
ms.date: 03/15/2021
ms.custom: generated
ms.openlocfilehash: ba5340fdc12bf3141c5e3f24b6b9d178a5db600d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104602523"
---
# <a name="azure-built-in-roles"></a>Wbudowane role platformy Azure

[Kontrola dostępu oparta na rolach na platformie Azure (RBAC)](overview.md) ma kilka wbudowanych ról platformy Azure, które można przypisać do użytkowników, grup, podmiotów usługi i tożsamości zarządzanych. Przypisania ról są sposobem kontroli dostępu do zasobów platformy Azure. Jeśli wbudowane role nie są zgodne z konkretnymi potrzebami organizacji, możesz utworzyć własne [role niestandardowe platformy Azure](custom-roles.md).

Ten artykuł zawiera listę wbudowanych ról platformy Azure, które są zawsze rozwijane. Aby uzyskać najnowsze role, użyj funkcji [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) lub [AZ role Definition list](/cli/azure/role/definition#az-role-definition-list). Jeśli szukasz ról administratorów dla Azure Active Directory (Azure AD), zobacz [uprawnienia roli administratora w programie Azure Active Directory](../active-directory/roles/permissions-reference.md).

W poniższej tabeli przedstawiono krótki opis i unikatowy identyfikator każdej wbudowanej roli. Kliknij nazwę roli, aby wyświetlić listę `Actions` , `NotActions` , `DataActions` i `NotDataActions` dla każdej roli. Aby uzyskać informacje o tym, co oznaczają te działania oraz o sposobie ich zastosowania do płaszczyzny zarządzania i danych, zobacz [Opis ról roli platformy Azure](role-definitions.md).

## <a name="all"></a>Wszystko

> [!div class="mx-tableFixed"]
> | Wbudowana rola | Opis | ID (Identyfikator) |
> | --- | --- | --- |
> | **Ogólne** |  |  |
> | [Współautor](#contributor) | Przyznaje pełen dostęp do zarządzania wszystkimi zasobami, ale nie umożliwia przypisywania ról w usłudze Azure RBAC, zarządzania przypisaniami w planach platformy Azure ani udostępniania galerii obrazów. | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | [Właściciel](#owner) | Przyznaje pełen dostęp do zarządzania wszystkimi zasobami, w tym możliwość przypisywania ról w usłudze Azure RBAC. | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | [Czytelnik](#reader) | Wyświetlanie wszystkich zasobów, ale nie pozwala na wprowadzanie żadnych zmian. | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | [Administrator dostępu użytkowników](#user-access-administrator) | Umożliwia zarządzanie dostępem użytkowników do zasobów platformy Azure. | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Środowisko obliczeniowe** |  |  |
> | [Współautor klasycznej maszyny wirtualnej](#classic-virtual-machine-contributor) | Umożliwia zarządzanie klasycznymi maszynami wirtualnymi, ale nie umożliwia uzyskiwania do nich dostępu, a nie do sieci wirtualnej ani konta magazynu, z którymi są połączone. | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | [Logowanie administratora maszyny wirtualnej](#virtual-machine-administrator-login) | Wyświetl Virtual Machines w portalu i zaloguj się jako administrator | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | [Współautor maszyny wirtualnej](#virtual-machine-contributor) | Umożliwia zarządzanie maszynami wirtualnymi, ale nie dostęp do nich, a nie do sieci wirtualnej ani konta magazynu, z którymi są połączone. | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | [Logowanie użytkownika maszyny wirtualnej](#virtual-machine-user-login) | Wyświetl Virtual Machines w portalu i zaloguj się jako zwykły użytkownik. | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Sieć** |  |  |
> | [Współautor punktu końcowego usługi CDN](#cdn-endpoint-contributor) | Może zarządzać punktami końcowymi usługi CDN, ale nie może udzielić dostępu innym użytkownikom. | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | [Czytnik punktu końcowego usługi CDN](#cdn-endpoint-reader) | Może wyświetlać punkty końcowe usługi CDN, ale nie może wprowadzać zmian. | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | [Współautor profilu CDN](#cdn-profile-contributor) | Może zarządzać profilami sieci CDN i ich punktami końcowymi, ale nie może udzielać dostępu innym użytkownikom. | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | [Czytnik profilu CDN](#cdn-profile-reader) | Może wyświetlać profile usługi CDN i ich punkty końcowe, ale nie może wprowadzać zmian. | 8f96442b-4075-438f-813d-ad51ab4019af |
> | [Współautor klasycznej sieci](#classic-network-contributor) | Umożliwia zarządzanie sieciami klasycznymi, ale nie umożliwia uzyskiwania do nich dostępu. | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | [Współautor strefy DNS](#dns-zone-contributor) | Umożliwia zarządzanie strefami i zestawami rekordów DNS w Azure DNS, ale nie pozwala na kontrolowanie dostępu do nich. | befefa01-2a29-4197-83a8-272ff33ce314 |
> | [Współautor sieci](#network-contributor) | Umożliwia zarządzanie sieciami, ale nie umożliwia uzyskiwania do nich dostępu. | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | [Współautor strefy Prywatna strefa DNS](#private-dns-zone-contributor) | Umożliwia zarządzanie prywatnymi zasobami strefy DNS, ale nie z sieciami wirtualnymi, z którymi są one połączone. | b12aa53e-6015-4669-85d0-8515ebb3ae7f |
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
> | [Współautor danych obiektu blob usługi Storage](#storage-blob-data-contributor) | Odczytuj, zapisuj i usuwaj kontenery i obiekty blob usługi Azure Storage. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | [Właściciel danych obiektu blob usługi Storage](#storage-blob-data-owner) | Zapewnia pełen dostęp do kontenerów obiektów blob i danych usługi Azure Storage, w tym do przypisywania kontroli dostępu POSIX. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | [Czytelnik danych obiektu blob usługi Storage](#storage-blob-data-reader) | Odczytuj i wyświetlaj kontenery usługi Azure Storage oraz obiekty blob. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | [Delegat obiektów blob magazynu](#storage-blob-delegator) | Pobierz klucz delegowania użytkownika, którego można następnie użyć do utworzenia sygnatury dostępu współdzielonego dla kontenera lub obiektu BLOB podpisanego przy użyciu poświadczeń usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Tworzenie skojarzeń zabezpieczeń dla delegowania użytkowników](/rest/api/storageservices/create-user-delegation-sas). | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | [Współautor udziału SMB danych w pliku magazynu](#storage-file-data-smb-share-contributor) | Zezwala na dostęp do odczytu, zapisu i usuwania w plikach/katalogach w udziałach plików platformy Azure. Ta rola nie ma wbudowanego odpowiedniku na serwerach plików systemu Windows. | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | [Współautor udziału SMB danych w pliku magazynu z podwyższonym poziomem uprawnień](#storage-file-data-smb-share-elevated-contributor) | Zezwala na odczyt, zapis, usuwanie i modyfikowanie list ACL dla plików/katalogów w udziałach plików platformy Azure. Ta rola jest równoważna z listą ACL udziałów plików na serwerach plików systemu Windows. | a7264617-510b-434b-a828-9731dc254ea7 |
> | [Czytelnik udziału SMB danych w pliku magazynu](#storage-file-data-smb-share-reader) | Zezwala na dostęp do odczytu dla plików/katalogów w udziałach plików platformy Azure. Ta rola jest równoważna z listą ACL udziału plików odczytywaną na serwerach plików systemu Windows. | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | [Współautor danych kolejki magazynu](#storage-queue-data-contributor) | Odczytuj, zapisuj i usuwaj kolejki usługi Azure Storage oraz wiadomości w kolejce. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | [Procesor komunikatów danych kolejki magazynu](#storage-queue-data-message-processor) | Wgląd, pobieranie i usuwanie wiadomości z kolejki usługi Azure Storage. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | [Nadawca komunikatu o danych kolejki magazynu](#storage-queue-data-message-sender) | Dodawanie komunikatów do kolejki usługi Azure Storage. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | [Czytnik danych kolejki magazynu](#storage-queue-data-reader) | Odczytuj i wyświetlaj kolejki usługi Azure Storage oraz wiadomości w kolejce. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Sieć Web** |  |  |
> | [Współautor danych Azure Maps](#azure-maps-data-contributor) | Przyznaje dostęp do odczytu, zapisu i usuwania, aby mapować powiązane dane z konta usługi Azure Maps. | 8f5e0ce6-4f7b-4dcf-bddf-e6f48634a204 |
> | [Azure Maps czytnika danych](#azure-maps-data-reader) | Przyznaje dostęp do odczytu danych związanych z mapowaniem z konta usługi Azure Maps. | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | [Współautor Search Service](#search-service-contributor) | Umożliwia zarządzanie usługami wyszukiwania, ale nie umożliwia uzyskiwania do nich dostępu. | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | [Czytnik AccessKeyer sygnalizujący](#signalr-accesskey-reader) | Odczytaj klucze dostępu do usługi sygnalizującego | 04165923-9d83-45d5-8227-78b77b0a687e |
> | [Serwer aplikacji sygnalizujący (wersja zapoznawcza)](#signalr-app-server-preview) | Umożliwia usłudze Sygnalizującemu dostęp do serwera aplikacji z opcjami uwierzytelniania usługi AAD. | 420fcaa2-552c-430f-98ca-3264be4806c7 |
> | [Współautor sygnalizującego](#signalr-contributor) | Tworzenie, odczytywanie, aktualizowanie i usuwanie zasobów usługi sygnalizującego | 8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761 |
> | [Współautor bezserwerowy sygnalizujący (wersja zapoznawcza)](#signalr-serverless-contributor-preview) | Umożliwia usłudze dostęp do aplikacji w trybie bezserwerowym z opcjami uwierzytelniania usługi AAD. | fd53cd77-2268-407a-8f46-7e7863d0f521 |
> | [Właściciel usługi sygnalizującej (wersja zapoznawcza)](#signalr-service-owner-preview) | Pełny dostęp do interfejsów API REST usługi Azure Signal Service | 7e4f1700-ea5a-4f59-8f37-079cfe29dce3 |
> | [Czytnik usługi sygnalizującej (wersja zapoznawcza)](#signalr-service-reader-preview) | Dostęp tylko do odczytu do interfejsów API REST usługi Azure Signal | ddde6b66-c0df-4114-a159-3618637b3035 |
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
> | [Rola współautora usługi Azure Kubernetes](#azure-kubernetes-service-contributor-role) | Przyznaje dostęp do odczytu i zapisu klastrów usługi Azure Kubernetes | ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8 |
> | [Administrator RBAC usługi Azure Kubernetes Service](#azure-kubernetes-service-rbac-admin) | Umożliwia zarządzanie wszystkimi zasobami w obszarze klaster/przestrzeń nazw, z wyjątkiem aktualizacji lub usuwania przydziałów zasobów i przestrzeni nazw. | 3498e952-d568-435e-9b2c-8d77e338d7f7 |
> | [Administrator klastra RBAC usługi Azure Kubernetes Service](#azure-kubernetes-service-rbac-cluster-admin) | Umożliwia zarządzanie wszystkimi zasobami w klastrze. | b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b |
> | [Czytnik RBAC usługi Azure Kubernetes Service](#azure-kubernetes-service-rbac-reader) | Zezwala na dostęp tylko do odczytu do wyświetlania większości obiektów w przestrzeni nazw. Nie zezwala na wyświetlanie ról ani powiązań ról. Ta rola nie zezwala na wyświetlanie wpisów tajnych, ponieważ odczytywanie zawartości wpisów tajnych umożliwia dostęp do poświadczeń usługi ServiceAccount w przestrzeni nazw, co umożliwia dostęp do interfejsu API jako dowolnego elementu ServiceAccount w przestrzeni nazw (w formie eskalacji uprawnień). Zastosowanie tej roli w zakresie klastra zapewnia dostęp do wszystkich przestrzeni nazw. | 7f6c6a51-bcf8-42ba-9220-52d62157d7db |
> | [Składnik zapisywania RBAC usługi Azure Kubernetes Service](#azure-kubernetes-service-rbac-writer) | Zezwala na dostęp do odczytu/zapisu do większości obiektów w przestrzeni nazw. Ta rola nie zezwala na przeglądanie ani modyfikowanie ról ani powiązań ról. Jednak ta rola umożliwia dostęp do wpisów tajnych i uruchamianie zasobników jako dowolnego konta ServiceAccount w przestrzeni nazw, dlatego może służyć do uzyskania poziomów dostępu interfejsu API dowolnego konta ServiceAccount w przestrzeni nazw. Zastosowanie tej roli w zakresie klastra zapewnia dostęp do wszystkich przestrzeni nazw. | a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb |
> | **Bazy danych** |  |  |
> | [Rola czytnika konta Cosmos DB](#cosmos-db-account-reader-role) | Może odczytywać Azure Cosmos DB dane konta. Aby zarządzać kontami Azure Cosmos DB, zobacz [współautor konta usługi DocumentDB](#documentdb-account-contributor) . | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | [Operator Cosmos DB](#cosmos-db-operator) | Umożliwia zarządzanie kontami Azure Cosmos DB, ale nie dostęp do danych w nich. Uniemożliwia dostęp do kluczy konta i parametrów połączenia. | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | [CosmosBackupOperator](#cosmosbackupoperator) | Może przesłać żądanie przywracania dla bazy danych Cosmos DB lub kontenera dla konta | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | [CosmosRestoreOperator](#cosmosrestoreoperator) | Może wykonać akcję przywracania dla konta bazy danych Cosmos DB z trybem ciągłej kopii zapasowej | 5432c526-bc82-444a-b7ba-57c5b0b5b34f |
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
> | [Przeczyszczanie danych](#data-purger) | Usuń dane prywatne z obszaru roboczego Log Analytics. | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | [Operator klastra usługi HDInsight](#hdinsight-cluster-operator) | Umożliwia odczytywanie i modyfikowanie konfiguracji klastrów usługi HDInsight. | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | [Współautor usług domenowych w usłudze HDInsight](#hdinsight-domain-services-contributor) | Może odczytywać, tworzyć, modyfikować i usuwać operacje związane z usługami domenowymi, które są potrzebne pakiet Enterprise Security usługi HDInsight | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | [Współautor usługi Log Analytics](#log-analytics-contributor) | Współautor Log Analytics może odczytywać wszystkie dane monitorowania i edytować ustawienia monitorowania. Edytowanie ustawień monitorowania obejmuje dodanie rozszerzenia maszyny wirtualnej do maszyn wirtualnych; Odczytywanie kluczy konta magazynu w celu skonfigurowania kolekcji dzienników z usługi Azure Storage; Tworzenie i Konfigurowanie kont usługi Automation; Dodawanie rozwiązań; i Konfigurowanie diagnostyki platformy Azure dla wszystkich zasobów platformy Azure. | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | [Czytelnik usługi Log Analytics](#log-analytics-reader) | Log Analytics Reader może wyświetlać i przeszukiwać wszystkie dane monitorowania, a także wyświetlać ustawienia monitorowania, w tym Wyświetlanie konfiguracji diagnostyki platformy Azure na wszystkich zasobach platformy Azure. | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | [Kontrolą Curator danych](#purview-data-curator) | Dane Microsoft. kontrolą Data Curator mogą tworzyć, odczytywać, modyfikować i usuwać obiekty danych wykazu oraz ustanawiać relacje między obiektami. Ta rola jest w wersji zapoznawczej i może ulec zmianie. | 8a3c2885-9b38-4fd2-9d99-91af537c1347 |
> | [Czytnik danych kontrolą](#purview-data-reader) | Czytnik danych Microsoft. kontrolą może odczytywać obiekty danych wykazu. Ta rola jest w wersji zapoznawczej i może ulec zmianie. | ff100721-1b9d-43d8-af52-42b69c1272db |
> | [Administrator źródła danych kontrolą](#purview-data-source-administrator) | Administrator źródła danych Microsoft. kontrolą może zarządzać źródłami danych i skanami danych. Ta rola jest w wersji zapoznawczej i może ulec zmianie. | 200bba9e-f0c8-430f-892b-6f0794863803 |
> | [Współautor rejestru schematu (wersja zapoznawcza)](#schema-registry-contributor-preview) | Odczytuj, zapisuj i usuwaj grupy i schematy rejestru schematu. | 5dffeca3-4936-4216-b2bc-10343a5abb25 |
> | [Czytnik rejestru schematu (wersja zapoznawcza)](#schema-registry-reader-preview) | Odczytuj i wyświetlaj listę grup i schematów rejestru schematu. | 2c56ea50-c6b3-40a6-83c0-9d98858bc7d2 |
> | **Łańcuch bloków** |  |  |
> | [Dostęp do węzła elementu członkowskiego łańcucha bloków (wersja zapoznawcza)](#blockchain-member-node-access-preview) | Zezwala na dostęp do węzłów składowych łańcucha bloków | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **AI i uczenie maszynowe** |  |  |
> | [Współautor Cognitive Services](#cognitive-services-contributor) | Umożliwia tworzenie, odczytywanie, aktualizowanie, usuwanie i zarządzanie kluczami Cognitive Services. | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | [Współautor Custom Vision Cognitive Services](#cognitive-services-custom-vision-contributor) | Pełny dostęp do projektu, w tym możliwość wyświetlania, tworzenia, edytowania lub usuwania projektów. | c1ff6cc2-C111-46fe-8896-e0ef812ad9f3 |
> | [Wdrożenie Custom Vision Cognitive Services](#cognitive-services-custom-vision-deployment) | Publikowanie, cofanie publikowania i eksportowanie modeli. Wdrożenie może wyświetlić projekt, ale nie można go zaktualizować. | 5c4089e1-6d96-4d2f-b296-c1bc7137275f |
> | [Cognitive Services Custom Vision Labeler](#cognitive-services-custom-vision-labeler) | Wyświetlanie, edytowanie obrazów szkoleniowych i tworzenie, Dodawanie, usuwanie lub usuwanie tagów obrazu. Etykiety mogą wyświetlać projekt, ale nie mogą zaktualizować niczego innego niż obrazy szkoleniowe i Tagi. | 88424f51-ebe7-446f-bc41-7fa16989e96c |
> | [Cognitive Services Custom Vision Reader](#cognitive-services-custom-vision-reader) | Akcje tylko do odczytu w projekcie. Czytelnicy nie mogą utworzyć ani zaktualizować projektu. | 93586559-c37d-4a6b-ba08-b9f0940c2d73 |
> | [Cognitive Services Custom Vision Trainer](#cognitive-services-custom-vision-trainer) | Wyświetlaj, Edytuj projekty i uczenie modeli, w tym możliwość publikowania, cofania publikacji i eksportowania modeli. Instruktorzy nie mogą tworzyć ani usuwać projektu. | 0a5ae4ab-0d65-4eeb-be61-29fc9b54394b |
> | [Czytnik danych Cognitive Services (wersja zapoznawcza)](#cognitive-services-data-reader-preview) | Umożliwia odczytywanie danych Cognitive Services. | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | [Cognitive Services administrator doradcy metryk](#cognitive-services-metrics-advisor-administrator) | Pełny dostęp do projektu, w tym konfiguracja poziomu systemu. | cb43c632-a144-4ec5-977c-e80c4affc34a |
> | [Edytor QnA Maker Cognitive Services](#cognitive-services-qna-maker-editor) | Umożliwia tworzenie, edytowanie, importowanie i eksportowanie KB. Nie można opublikować ani usunąć bazy wiedzy. | f4cc2bf9-21be-47a1-bdf1-5c5804381025 |
> | [Cognitive Services QnA Maker Reader](#cognitive-services-qna-maker-reader) | Umożliwia odczytanie i przetestowanie tylko KB. | 466ccd10-b268-4a11-b098-b4849f024126 |
> | [Cognitive Services użytkownika](#cognitive-services-user) | Umożliwia odczytywanie i wyświetlanie listy kluczy Cognitive Services. | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Internet rzeczy** |  |  |
> | [Administrator aktualizacji urządzenia](#device-update-administrator) | Zapewnia pełny dostęp do operacji zarządzania i zawartości | 02ca0879-e8e4-47a5-a61e-5c618b76e64a |
> | [Administrator zawartości urządzenia z aktualizacją](#device-update-content-administrator) | Zapewnia pełny dostęp do operacji dotyczących zawartości | 0378884a-3af5-44ab-8323-f5b22f9f3c98 |
> | [Czytnik zawartości urządzenia aktualizacji](#device-update-content-reader) | Umożliwia odczyt operacji na potrzeby zawartości, ale nie pozwala na wprowadzanie zmian | d1ee9a80-8b14-47f0-bdc2-f4a351625a7b |
> | [Administrator wdrożeń aktualizacji urządzeń](#device-update-deployments-administrator) | Zapewnia pełny dostęp do operacji zarządzania | e4237640-0e3d-4a46-8fda-70bc94856432 |
> | [Czytnik wdrożeń aktualizacji urządzeń](#device-update-deployments-reader) | Zapewnia dostęp do odczytu do operacji zarządzania, ale nie pozwala na wprowadzanie zmian | 49e2f5d2-7741-4835-8efa-19e1fe35e47f |
> | [Czytnik aktualizacji urządzenia](#device-update-reader) | Zapewnia dostęp do odczytu do operacji zarządzania i zawartości, ale nie pozwala na wprowadzanie zmian | e9dba6fb-3d52-4cf0-bce3-f06ce71b9e0f |
> | **Rzeczywistość mieszana** |  |  |
> | [Administrator zdalnego renderowania](#remote-rendering-administrator) | Zapewnia użytkownikowi konwersję, możliwość zarządzania sesją, renderowania i diagnostyki na potrzeby renderowania zdalnego na platformie Azure | 3df8b902-2a6f-47c7-8cc5-360e9b272a7e |
> | [Klient renderowania zdalnego](#remote-rendering-client) | Umożliwia użytkownikowi zarządzanie sesjami, renderowaniem i diagnostyką w przypadku renderowania zdalnego na platformie Azure. | d39065c4-c120-43c9-ab0a-63eed9795f0a |
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
> | [Współautor EventGrid](#eventgrid-contributor) | Umożliwia zarządzanie operacjami EventGrid. | 1e241071-0855-49ea-94dc-649edcd759de |
> | [Współautor EventGrid EventSubscription](#eventgrid-eventsubscription-contributor) | Umożliwia zarządzanie operacjami subskrypcji zdarzeń EventGrid. | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | [EventGrid EventSubscription](#eventgrid-eventsubscription-reader) | Umożliwia odczytanie subskrypcji zdarzeń EventGrid. | 2414bbcf-6497-4faf-8c65-045460748405 |
> | [Współautor danych FHIR](#fhir-data-contributor) | Rola umożliwia użytkownikowi lub głównemu pełen dostęp do danych FHIR | 5a1fc7df-4bf1-4951-a576-89034ee01acd |
> | [FHIR — eksport danych](#fhir-data-exporter) | Rola umożliwia użytkownikowi lub głównemu odczytywanie i eksportowanie danych FHIR | 3db33094-8700-4567-8da5-1501d4e7e843 |
> | [Czytnik danych FHIR](#fhir-data-reader) | Rola umożliwia użytkownikowi lub podmiotowi zabezpieczeń odczytywanie danych FHIR | 4c8d0bbc-75d3-4935-991f-5f3c56d81508 |
> | [Moduł zapisywania danych FHIR](#fhir-data-writer) | Rola umożliwia użytkownikowi lub głównemu odczytywanie i zapisywanie danych FHIR | 3f88fce4-5892-4214-ae73-ba5294559913 |
> | [Współautor środowisko usługi integracji](#integration-service-environment-contributor) | Umożliwia zarządzanie środowiskami usługi integracji, ale nie umożliwia uzyskiwania do nich dostępu. | a41e2c5b-bd99-4a07-88f4-9bf657a760b8 |
> | [środowisko usługi integracji deweloper](#integration-service-environment-developer) | Umożliwia deweloperom tworzenie i aktualizowanie przepływów pracy, kont integracji i połączeń interfejsów API w środowiskach usługi integracji. | c7aa55d3-1abb-444a-a5ca-5e51e485d6ec |
> | [Współautor konta systemów inteligentnych](#intelligent-systems-account-contributor) | Umożliwia zarządzanie kontami inteligentnych systemów, ale nie umożliwia uzyskiwania do nich dostępu. | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | [Współautor aplikacji logiki](#logic-app-contributor) | Umożliwia zarządzanie aplikacjami logiki, ale nie umożliwia zmiany dostępu do nich. | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | [Operator aplikacji logiki](#logic-app-operator) | Umożliwia odczytywanie, Włączanie i wyłączanie aplikacji logiki, ale nie ich edytowanie ani aktualizowanie. | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Tożsamość** |  |  |
> | [Współautor tożsamości zarządzanej](#managed-identity-contributor) | Tworzenie, odczytywanie, aktualizowanie i usuwanie tożsamości przypisanej przez użytkownika | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | [Operator tożsamości zarządzanej](#managed-identity-operator) | Odczytaj i przypisz tożsamość przypisaną przez użytkownika | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Bezpieczeństwo** |  |  |
> | [Współautor zaświadczania](#attestation-contributor) | Może odczytać lub usunąć wystąpienie dostawcy zaświadczania | bbf86eb8-f7b4-4cce-96e4-18cddf81d86e |
> | [Czytelnik zaświadczania](#attestation-reader) | Może odczytać właściwości dostawcy zaświadczania | fd1bd22b-8476-40bc-a0bc-69b95687b9f3 |
> | [Współautor usługi Azure Sentinel](#azure-sentinel-contributor) | Współautor usługi Azure Sentinel | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | [Czytelnik usługi Azure Sentinel](#azure-sentinel-reader) | Czytelnik usługi Azure Sentinel | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | [Obiekt odpowiadający usługi Azure Sentinel](#azure-sentinel-responder) | Obiekt odpowiadający usługi Azure Sentinel | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | [Key Vault administrator](#key-vault-administrator) | Wykonaj wszystkie operacje płaszczyzny danych w magazynie kluczy i wszystkie obiekty w nim, w tym certyfikaty, klucze i wpisy tajne. Nie można zarządzać zasobami magazynu kluczy ani zarządzać przypisaniami ról. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "kontrola dostępu oparta na rolach" platformy Azure. | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
> | [Oficer certyfikatów Key Vault](#key-vault-certificates-officer) | Wykonaj dowolną akcję dotyczącą certyfikatów magazynu kluczy, z wyjątkiem uprawnień do zarządzania. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "kontrola dostępu oparta na rolach" platformy Azure. | a4417e6f-fecd-4de8-b567-7b0420556985 |
> | [Współautor Key Vault](#key-vault-contributor) | Zarządzanie magazynami kluczy, ale nie umożliwia przypisywania ról w usłudze Azure RBAC i nie pozwala na dostęp do wpisów tajnych, kluczy lub certyfikatów. | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | [Key Vault oficer kryptograficzny](#key-vault-crypto-officer) | Wykonaj dowolną akcję dotyczącą kluczy magazynu kluczy, z wyjątkiem uprawnień do zarządzania. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "kontrola dostępu oparta na rolach" platformy Azure. | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
> | [Key Vault użytkownika szyfrowania usługi kryptograficznej](#key-vault-crypto-service-encryption-user) | Odczytywanie metadanych kluczy i wykonywanie operacji zawijania/odpakowania. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "kontrola dostępu oparta na rolach" platformy Azure. | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
> | [Key Vault użytkownika kryptograficznego](#key-vault-crypto-user) | Wykonywanie operacji kryptograficznych przy użyciu kluczy. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "kontrola dostępu oparta na rolach" platformy Azure. | 12338af0-0e69-4776-bea7-57ae8d297424 |
> | [Key Vault czytelnik](#key-vault-reader) | Odczytywanie metadanych magazynów kluczy oraz jego certyfikatów, kluczy i wpisów tajnych. Nie można odczytać wartości poufnych, takich jak zawartość wpisu tajnego lub materiał klucza. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "kontrola dostępu oparta na rolach" platformy Azure. | 21090545-7ca7-4776-b22c-e363652d74d2 |
> | [Key Vault oficerów tajnych](#key-vault-secrets-officer) | Wykonaj wszelkie działania dotyczące wpisów tajnych magazynu kluczy, z wyjątkiem uprawnień do zarządzania. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "kontrola dostępu oparta na rolach" platformy Azure. | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
> | [Użytkownik Key Vault Secret](#key-vault-secrets-user) | Odczytaj zawartość wpisu tajnego. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "kontrola dostępu oparta na rolach" platformy Azure. | 4633458b-17de-408a-b874-0445c86b69e6 |
> | [Współautor zarządzanego modułu HSM](#managed-hsm-contributor) | Umożliwia zarządzanie zarządzanymi pulami modułów HSM, ale nie umożliwia uzyskiwania do nich dostępu. | 18500a29-7fe2-46b2-a342-b16a415e101d |
> | [Administrator zabezpieczeń](#security-admin) | Wyświetl i zaktualizuj uprawnienia dla Security Center. Te same uprawnienia, jak rola czytelnik zabezpieczeń, mogą także aktualizować zasady zabezpieczeń i odrzucać alerty i zalecenia. | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | [Współautor oceny zabezpieczeń](#security-assessment-contributor) | Umożliwia wypychanie ocen do Security Center | 612c2aa1-cb24-443b-ac28-3ab7272de6f5 |
> | [Security Manager (starsza wersja)](#security-manager-legacy) | Jest to Starsza rola. Zamiast tego należy użyć administratora zabezpieczeń. | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | [Czytelnik zabezpieczeń](#security-reader) | Wyświetl uprawnienia dla Security Center. Może wyświetlać zalecenia, alerty, zasady zabezpieczeń i Stany zabezpieczeń, ale nie może wprowadzać zmian. | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **DevOps** |  |  |
> | [Użytkownik DevTest Labs](#devtest-labs-user) | Umożliwia łączenie, uruchamianie, ponowne uruchamianie i zamykanie maszyn wirtualnych w Azure DevTest Labs. | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | [Twórca laboratorium](#lab-creator) | Umożliwia tworzenie nowych laboratoriów na kontach laboratorium platformy Azure. | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Monitorowanie** |  |  |
> | [Współautor składnika Application Insights](#application-insights-component-contributor) | Może zarządzać składnikami Application Insights | ae349356-3a1b-4a5e-921d-050484c6347e |
> | [Application Insights Snapshot Debugger](#application-insights-snapshot-debugger) | Daje użytkownikowi uprawnienia do wyświetlania i pobierania migawek debugowania zebranych za pomocą Snapshot Debugger Application Insights. Należy zauważyć, że te uprawnienia nie są uwzględnione w rolach [właściciel](#owner) lub [współautor](#contributor) . Podczas nadawania użytkownikom roli Snapshot Debugger Application Insights należy przyznać rolę bezpośrednio użytkownikowi. Rola nie jest rozpoznawana, gdy zostanie dodana do roli niestandardowej. | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | [Współautor monitorowania](#monitoring-contributor) | Może odczytywać wszystkie dane monitorowania i edytować ustawienia monitorowania. Zobacz też Rozpoczynanie [pracy z rolami, uprawnieniami i zabezpieczeniami przy użyciu Azure monitor](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles). | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | [Wydawca metryk monitorowania](#monitoring-metrics-publisher) | Włącza publikowanie metryk dla zasobów platformy Azure | 3913510d-42f4-4e42-8a64-420c390055eb |
> | [Czytnik monitorowania](#monitoring-reader) | Może odczytywać wszystkie dane monitorowania (metryki, dzienniki itp.). Zobacz też Rozpoczynanie [pracy z rolami, uprawnieniami i zabezpieczeniami przy użyciu Azure monitor](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles). | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
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
> | [Administrator ustawień hierarchii](#hierarchy-settings-administrator) | Umożliwia użytkownikom edytowanie i usuwanie ustawień hierarchii | 350f8d15-c687-4448-8ae1-157740a3936d |
> | [Klaster Kubernetes — dołączanie do usługi Azure Arc](#kubernetes-cluster---azure-arc-onboarding) | Definicja roli do autoryzacji dowolnego użytkownika/usługi do tworzenia zasobów connectedClusters | 34e09817-6cbe-4d01-b1a2-e0eac5743d41 |
> | [Rola współautora aplikacji zarządzanej](#managed-application-contributor-role) | Umożliwia tworzenie zasobów aplikacji zarządzanej. | 641177b8-a67a-45b9-a033-47bc880bb21e |
> | [Rola operatora aplikacji zarządzanej](#managed-application-operator-role) | Umożliwia odczytywanie i wykonywanie akcji dotyczących zasobów aplikacji zarządzanej | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | [Czytnik aplikacji zarządzanych](#managed-applications-reader) | Umożliwia odczytanie zasobów w zarządzanej aplikacji i zażądanie dostępu JIT. | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | [Rola usuwania przypisania rejestracji usług zarządzanych](#managed-services-registration-assignment-delete-role) | Rola usuwania przypisania rejestracji usług zarządzanych umożliwia użytkownikom zarządzającym dzierżawę usuwanie przypisania rejestracji przypisanego do swojej dzierżawy. | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | [Współautor grupy zarządzania](#management-group-contributor) | Rola współautora grupy zarządzania | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | [Czytelnik grupy zarządzania](#management-group-reader) | Rola czytelnika grupy zarządzania | ac63b705-f282-497d-ac71-919bf39d939d |
> | [Nowe współautor konta APM Relic](#new-relic-apm-account-contributor) | Umożliwia zarządzanie kontami i aplikacjami New Relic Application Performance Management, ale nie umożliwia uzyskiwania do nich dostępu. | 5d28c62d-5b37-4476-8438-e587778df237 |
> | [Moduł zapisywania danych usługi Policy Insights (wersja zapoznawcza)](#policy-insights-data-writer-preview) | Zezwala na dostęp do odczytu do zasad zasobów i dostęp do zapisu dla zdarzeń zasad składnika zasobów. | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | [Rola operatora żądania limitu przydziału](#quota-request-operator-role) | Odczytywanie i tworzenie żądań limitu przydziału, pobieranie stanu żądania przydziału i tworzenie biletów pomocy technicznej. | 0e5f05e5-9ab9-446b-b98d-1e2157c94125 |
> | [Zakup rezerwacji](#reservation-purchaser) | Umożliwia zakup rezerwacji | f7b75c60-3036-4b75-91c3-6b41c27c1689 |
> | [Współautor zasad zasobów](#resource-policy-contributor) | Użytkownicy z prawami do tworzenia/modyfikowania zasad dotyczących zasobów, tworzenia biletów pomocy technicznej i odczytywania zasobów/hierarchii. | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | [Współautor usługi Site Recovery](#site-recovery-contributor) | Umożliwia zarządzanie usługą Site Recovery z wyjątkiem tworzenia magazynu i przypisywania ról | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | [Operator usługi Site Recovery](#site-recovery-operator) | Umożliwia przełączenie w tryb failover i powrót po awarii, ale nie wykonywanie innych operacji zarządzania Site Recovery | 494ae006-db33-4328-bf46-533a6560a3ca |
> | [Czytelnik usługi Site Recovery](#site-recovery-reader) | Umożliwia wyświetlanie stanu Site Recovery, ale nie wykonywanie innych operacji zarządzania | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | [Współautor żądania pomocy technicznej](#support-request-contributor) | Pozwala tworzyć żądania pomocy technicznej i zarządzać nimi | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | [Współautor tagów](#tag-contributor) | Umożliwia zarządzanie tagami w jednostkach bez zapewniania dostępu do samych jednostek. | 4a9ae827-6dc8-4573-8ac7-8239d42aa03f |
> | **Inne** |  |  |
> | [Właściciel danych Digital bliźniaczych reprezentacji systemu Azure](#azure-digital-twins-data-owner) | Pełna rola dostępu do danych Digital bliźniaczych reprezentacji — płaszczyzna | bcd981a7-7f74-457b-83e1-cceb9e632ffe |
> | [Czytnik danych Digital bliźniaczych reprezentacji systemu Azure](#azure-digital-twins-data-reader) | Rola tylko do odczytu dla właściwości płaszczyzny danych cyfrowych bliźniaczych reprezentacji | d57506d4-4c8d-48b1-8587-93c323f6a5a3 |
> | [Współautor BizTalk](#biztalk-contributor) | Umożliwia zarządzanie usługami BizTalk Services, ale nie umożliwia uzyskiwania do nich dostępu. | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | [Współautor grupy aplikacji wirtualizacji pulpitu](#desktop-virtualization-application-group-contributor) | Współautor grupy aplikacji wirtualizacji pulpitu. | 86240b0e-9422-4c43-887b-b61143f32ba8 |
> | [Czytelnik grupy aplikacji wirtualizacji pulpitu](#desktop-virtualization-application-group-reader) | Czytelnik grupy aplikacji wirtualizacji pulpitu. | aebf23d0-b568-4e86-b8f9-fe83a2c6ab55 |
> | [Współautor wirtualizacji pulpitu](#desktop-virtualization-contributor) | Współautor wirtualizacji pulpitu. | 082f0a83-3be5-4ba1-904c-961cca79b387 |
> | [Współautor puli hostów hosta wirtualizacji pulpitu](#desktop-virtualization-host-pool-contributor) | Współautor puli hostów wirtualizacji pulpitu. | e307426c-f9b6-4e81-87de-d99efb3c32bc |
> | [Czytnik puli hosta wirtualizacji pulpitu](#desktop-virtualization-host-pool-reader) | Czytnik puli hostów wirtualizacji pulpitu. | ceadfde2-b300-400a-ab7b-6143895aa822 |
> | [Czytnik wirtualizacji pulpitu](#desktop-virtualization-reader) | Czytelnik wirtualizacji pulpitu. | 49a72310-ab8d-41df-bbb0-79b649203868 |
> | [Operator hosta sesji wirtualizacji pulpitu](#desktop-virtualization-session-host-operator) | Operator hosta sesji wirtualizacji pulpitu. | 2ad6aaab-ead9-4eaa-8ac5-da422f562408 |
> | [Użytkownik wirtualizacji pulpitu](#desktop-virtualization-user) | Umożliwia użytkownikom korzystanie z aplikacji w grupie aplikacji. | 1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63 |
> | [Operator sesji użytkownika wirtualizacji pulpitu](#desktop-virtualization-user-session-operator) | Operator sesji Uesr wirtualizacji pulpitu. | ea4bfff8-7fb4-485a-aadd-d4129a0ffaa6 |
> | [Współautor obszaru roboczego wirtualizacji pulpitu](#desktop-virtualization-workspace-contributor) | Współautor obszaru roboczego wirtualizacji pulpitu. | 21efdde3-836f-432b-bf3d-3e8e734d4b2b |
> | [Czytnik pulpitu obszaru roboczego](#desktop-virtualization-workspace-reader) | Czytnik obszaru roboczego wirtualizacji pulpitu. | 0fa44ee9-7a7d-466b-9bb2-2bf446b1204d |
> | [Czytnik kopii zapasowej dysku](#disk-backup-reader) | Zapewnia uprawnienie do magazynu kopii zapasowych w celu wykonania kopii zapasowej na dysku. | 3e5e47e6-65f7-47ef-90b5-e5dd4d455f24 |
> | [Operator przywracania dysku](#disk-restore-operator) | Zapewnia uprawnienie do magazynu kopii zapasowych w celu przeprowadzenia przywracania dysku. | b50d9833-a0cb-478e-945f-707fcc997c13 |
> | [Współautor migawek dysków](#disk-snapshot-contributor) | Zapewnia uprawnienie do magazynu kopii zapasowych w celu zarządzania migawkami dysków. | 7efff54f-a5b4-42b5-a1c5-5411624893ce |
> | [Współautor kolekcji zadań usługi Scheduler](#scheduler-job-collections-contributor) | Umożliwia zarządzanie kolekcjami zadań harmonogramu, ale nie umożliwia uzyskiwania do nich dostępu. | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | [Operator centrum usług](#services-hub-operator) | Operator centrum usług pozwala wykonywać wszystkie operacje odczytu, zapisu i usuwania powiązane z łącznikami centrum usług. | 82200a5b-e217-47a5-b665-6d8765ee745b |


## <a name="general"></a>Ogólne


### <a name="contributor"></a>Współautor

Przyznaje pełen dostęp do zarządzania wszystkimi zasobami, ale nie umożliwia przypisywania ról w usłudze Azure RBAC, zarządzania przypisaniami w planach platformy Azure ani udostępniania galerii obrazów. [Dowiedz się więcej](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | * | Twórz zasoby wszystkich typów i zarządzaj nimi |
> | **NotActions** |  |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Delete | Usuwanie ról, przypisań zasad, definicji zasad i definicji zestawów zasad |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Write | Tworzenie ról, przypisań ról, przypisań zasad, definicji zasad i definicji zestawów zasad |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/elevateAccess/Action | Przyznaje osobie wywołującej uprawnienia administratora dostępu użytkowników w zakresie dzierżawy |
> | [Microsoft. plan](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/Write | Utwórz lub zaktualizuj wszystkie przypisania strategii |
> | [Microsoft. plan](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/Delete | Usuń wszystkie przypisania strategii |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/Galleries/Share/Action | Udostępnia galerię do różnych zakresów |
> | **Akcje dataactions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants full access to manage all resources, but does not allow you to assign roles in Azure RBAC, manage assignments in Azure Blueprints, or share image galleries.",
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
        "Microsoft.Blueprint/blueprintAssignments/delete",
        "Microsoft.Compute/galleries/share/action"
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

Przyznaje pełen dostęp do zarządzania wszystkimi zasobami, w tym możliwość przypisywania ról w usłudze Azure RBAC. [Dowiedz się więcej](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
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
  "description": "Grants full access to manage all resources, including the ability to assign roles in Azure RBAC.",
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

Wyświetlanie wszystkich zasobów, ale nie pozwala na wprowadzanie żadnych zmian. [Dowiedz się więcej](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
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
  "description": "View all resources, but does not allow you to make any changes.",
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

Umożliwia zarządzanie dostępem użytkowników do zasobów platformy Azure. [Dowiedz się więcej](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | */read | Odczytuj zasoby wszystkich typów, z wyjątkiem kluczy tajnych. |
> | [Microsoft. autoryzacja](resource-provider-operations.md#microsoftauthorization)/* | Zarządzanie autoryzacją |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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

## <a name="compute"></a>Compute


### <a name="classic-virtual-machine-contributor"></a>Współautor klasycznej maszyny wirtualnej

Umożliwia zarządzanie klasycznymi maszynami wirtualnymi, ale nie umożliwia uzyskiwania do nich dostępu, a nie do sieci wirtualnej ani konta magazynu, z którymi są połączone.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/domainNames/* | Tworzenie klasycznych nazw domen obliczeniowych i zarządzanie nimi |
> | [Microsoft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/* | Tworzenie maszyn wirtualnych i zarządzanie nimi |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/networkSecurityGroups/Join/Action |  |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/link/Action | Połącz zastrzeżony adres IP |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/Read | Pobiera zastrzeżone adresy IP |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/Join/Action | Przyłącza do sieci wirtualnej. |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/Read | Pobierz sieć wirtualną. |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/disks/Read | Zwraca dysk konta magazynu. |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/images/Read | Zwraca obraz konta magazynu. Przestarzałe. Użyj "Microsoft. ClassicStorage/storageAccounts/vmImages") |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/Action | Wyświetla listę kluczy dostępu dla kont magazynu. |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/Read | Zwróć konto magazynu z danym kontem. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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

Wyświetl Virtual Machines w portalu i zaloguj się jako administrator [Dowiedz się więcej](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/Read | Pobiera definicję publicznego adresu IP. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | Pobierz definicję sieci wirtualnej |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/Read | Pobiera definicję modułu równoważenia obciążenia |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/Read | Pobiera definicję interfejsu sieciowego.  |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/Read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/virtualMachines/login/Action | Zaloguj się do maszyny wirtualnej jako zwykły użytkownik |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/virtualMachines/loginAsAdmin/Action | Logowanie się do maszyny wirtualnej przy użyciu uprawnień administratora systemu Windows lub użytkownika root w systemie Linux |
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

Umożliwia zarządzanie maszynami wirtualnymi, ale nie dostęp do nich, a nie do sieci wirtualnej ani konta magazynu, z którymi są połączone. [Dowiedz się więcej](../virtual-machines/linux/tutorial-govern-resources.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/availabilitySets/* | Tworzenie zestawów dostępności obliczeń i zarządzanie nimi |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/Locations/* | Tworzenie lokalizacji obliczeniowych i zarządzanie nimi |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/virtualMachines/* | Wykonaj wszystkie działania maszyn wirtualnych, w tym tworzenie, aktualizowanie, usuwanie, uruchamianie, ponowne uruchamianie i wyłączanie maszyn wirtualnych. Wykonaj wstępnie zdefiniowane skrypty na maszynach wirtualnych. |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/virtualMachineScaleSets/* | Tworzenie zestawów skalowania maszyn wirtualnych i zarządzanie nimi |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/disks/Write | Tworzy nowy dysk lub aktualizuje istniejący |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/disks/Read | Pobieranie właściwości dysku |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/disks/Delete | Usuwa dysk |
> | [Microsoft. wspólny](resource-provider-operations.md#microsoftdevtestlab)/Schedules/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/applicationGateways/backendAddressPools/Join/Action | Sprzęga pulę adresów zaplecza bramy aplikacji. Brak alertów. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/backendAddressPools/Join/Action | Sprzęga pulę adresów zaplecza modułu równoważenia obciążenia. Brak alertów. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatPools/Join/Action | Przyłącza do puli NAT dla ruchu przychodzącego modułu równoważenia obciążenia. Brak alertów. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatRules/Join/Action | Przyłącza do przychodzącej reguły NAT modułu równoważenia obciążenia. Brak alertów. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/probes/Join/Action | Umożliwia korzystanie z sond modułu równoważenia obciążenia. Na przykład za pomocą tej właściwości uprawnienia healthProbe zestawu skalowania maszyn wirtualnych można odwoływać się do sondy. Brak alertów. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/Read | Pobiera definicję modułu równoważenia obciążenia |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Locations/* | Tworzenie lokalizacji sieciowych i zarządzanie nimi |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/* | Tworzenie interfejsów sieciowych i zarządzanie nimi |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/Join/Action | Przyłącza do sieciowej grupy zabezpieczeń. Brak alertów. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/Read | Pobiera definicję sieciowej grupy zabezpieczeń |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/Join/Action | Przyłącza do publicznego adresu IP. Brak alertów. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/Read | Pobiera definicję publicznego adresu IP. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | Pobierz definicję sieci wirtualnej |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Subnets/Join/Action | Przyłącza do sieci wirtualnej. Brak alertów. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/Write | Tworzenie opcji ochrony kopii zapasowej |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/*/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/Read | Zwraca szczegóły obiektu chronionego elementu |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/Write | Utwórz chroniony element kopii zapasowej |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/Read | Zwraca wszystkie zasady ochrony |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/Write | Tworzy zasady ochrony |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "magazyn" |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Usages/Read | Zwraca szczegóły użycia magazynu Recovery Servicesowego. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Write | Operacja Utwórz magazyn tworzy zasób platformy Azure typu "magazyn" |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. SqlVirtualMachine](resource-provider-operations.md#microsoftsqlvirtualmachine)/* |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/Action | Zwraca klucze dostępu dla określonego konta magazynu. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/Read | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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

Wyświetl Virtual Machines w portalu i zaloguj się jako zwykły użytkownik. [Dowiedz się więcej](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/Read | Pobiera definicję publicznego adresu IP. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | Pobierz definicję sieci wirtualnej |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/Read | Pobiera definicję modułu równoważenia obciążenia |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/Read | Pobiera definicję interfejsu sieciowego.  |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/Read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/virtualMachines/login/Action | Zaloguj się do maszyny wirtualnej jako zwykły użytkownik |
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

## <a name="networking"></a>Sieć


### <a name="cdn-endpoint-contributor"></a>Współautor punktu końcowego usługi CDN

Może zarządzać punktami końcowymi usługi CDN, ale nie może udzielić dostępu innym użytkownikom.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/edgenodes/Read |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/Profiles/Endpoints/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/edgenodes/Read |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/Profiles/Endpoints/*/Read |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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

Może zarządzać profilami sieci CDN i ich punktami końcowymi, ale nie może udzielać dostępu innym użytkownikom. [Dowiedz się więcej](../cdn/cdn-app-dev-net.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/edgenodes/Read |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/Profiles/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/edgenodes/Read |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/Profiles/*/Read |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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

Umożliwia zarządzanie sieciami klasycznymi, ale nie umożliwia uzyskiwania do nich dostępu. [Dowiedz się więcej](../virtual-network/virtual-network-manage-peering.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/* | Tworzenie klasycznych sieci i zarządzanie nimi |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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

Umożliwia zarządzanie strefami i zestawami rekordów DNS w Azure DNS, ale nie pozwala na kontrolowanie dostępu do nich. [Dowiedz się więcej](../dns/dns-protect-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/dnsZones/* | Tworzenie stref i rekordów DNS oraz zarządzanie nimi |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/* | Tworzenie sieci i zarządzanie nimi |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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

### <a name="private-dns-zone-contributor"></a>Współautor strefy Prywatna strefa DNS

Umożliwia zarządzanie prywatnymi zasobami strefy DNS, ale nie z sieciami wirtualnymi, z którymi są one połączone. [Dowiedz się więcej](../dns/dns-protect-private-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/privateDnsZones/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationResults/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationStatuses/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | Pobierz definicję sieci wirtualnej |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Join/Action | Przyłącza do sieci wirtualnej. Brak alertów. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
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
  "description": "Lets you manage private DNS zone resources, but not the virtual networks they are linked to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b12aa53e-6015-4669-85d0-8515ebb3ae7f",
  "name": "b12aa53e-6015-4669-85d0-8515ebb3ae7f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/privateDnsZones/*",
        "Microsoft.Network/privateDnsOperationResults/*",
        "Microsoft.Network/privateDnsOperationStatuses/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/join/action",
        "Microsoft.Authorization/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Private DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="traffic-manager-contributor"></a>Współautor Traffic Manager

Umożliwia zarządzanie profilami Traffic Manager, ale nie pozwala na kontrolowanie użytkowników, którzy mają do nich dostęp.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/trafficManagerProfiles/* |  |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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

## <a name="storage"></a>Storage


### <a name="avere-contributor"></a>Współautor avere

Można utworzyć klaster avere vFXT i zarządzać nim. [Dowiedz się więcej](../avere-vfxt/avere-vfxt-deploy-plan.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/*/Read |  |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/availabilitySets/* |  |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/proximityPlacementGroups/* |  |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/virtualMachines/* |  |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/disks/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/*/Read |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | Pobierz definicję sieci wirtualnej |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Subnets/Read | Pobiera definicję podsieci sieci wirtualnej |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Subnets/Join/Action | Przyłącza do sieci wirtualnej. Brak alertów. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Subnets/joinViaServiceEndpoint/Action | Dołącza do podsieci zasoby, takie jak konto magazynu lub baza danych SQL. Brak alertów. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/Join/Action | Przyłącza do sieciowej grupy zabezpieczeń. Brak alertów. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/*/Read |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/* | Tworzenie kont magazynu i zarządzanie nimi |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Resources/Read | Pobiera zasoby dla grupy zasobów. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Blobs/Delete | Zwraca wynik usunięcia obiektu BLOB |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Blobs/Read | Zwraca obiekt BLOB lub listę obiektów BLOB |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Blobs/Write | Zwraca wynik zapisania obiektu BLOB |
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
        "Microsoft.Compute/proximityPlacementGroups/*",
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

Używane przez klaster avere vFXT do zarządzania klastrem [Dowiedz się więcej](../avere-vfxt/avere-vfxt-manage-cluster.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/virtualMachines/Read | Pobierz właściwości maszyny wirtualnej |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/Read | Pobiera definicję interfejsu sieciowego.  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/Write | Tworzy interfejs sieciowy lub aktualizuje istniejący interfejs sieciowy.  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | Pobierz definicję sieci wirtualnej |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Subnets/Read | Pobiera definicję podsieci sieci wirtualnej |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Subnets/Join/Action | Przyłącza do sieci wirtualnej. Brak alertów. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/Join/Action | Przyłącza do sieciowej grupy zabezpieczeń. Brak alertów. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Delete | Zwraca wynik usunięcia kontenera |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Read | Zwraca listę kontenerów |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Write | Zwraca wynik umieszczenia kontenera obiektów BLOB. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Blobs/Delete | Zwraca wynik usunięcia obiektu BLOB |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Blobs/Read | Zwraca obiekt BLOB lub listę obiektów BLOB |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Blobs/Write | Zwraca wynik zapisania obiektu BLOB |
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

Umożliwia zarządzanie usługą kopii zapasowych, ale nie może tworzyć magazynów i zapewniać innym użytkownikom [więcej informacji](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | Pobierz definicję sieci wirtualnej |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/* | Zarządzanie wynikami operacji zarządzania kopiami zapasowymi |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/* | Tworzenie kontenerów kopii zapasowych w ramach sieci szkieletowej magazynu Recovery Services i zarządzanie nimi |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/refreshContainers/Action | Odświeża listę kontenerów |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/* | Tworzenie zadań tworzenia kopii zapasowych i zarządzanie nimi |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/Action | Eksportuj zadania |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/* | Twórz wyniki operacji zarządzania kopiami zapasowymi i zarządzaj nimi |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/* | Tworzenie zasad tworzenia kopii zapasowych i zarządzanie nimi |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectableItems/* | Utwórz elementy, których kopię zapasową można utworzyć, i zarządzaj nimi |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/* | Tworzenie elementów kopii zapasowych i zarządzanie nimi |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/* | Tworzenie kontenerów z elementami kopii zapasowych i zarządzanie nimi |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupSecurityPIN/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/Read | Zwraca podsumowania elementów chronionych i serwerów chronionych dla Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Certificates/* | Tworzenie i zarządzanie certyfikatami związanymi z tworzeniem kopii zapasowych w magazynie Recovery Services |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/* | Utwórz rozszerzone informacje powiązane z magazynem i zarządzaj nimi |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/Read | Pobiera alerty dla magazynu usługi Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "magazyn" |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/* | Tworzenie zarejestrowanych tożsamości i zarządzanie nimi |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Usages/* | Tworzenie i zarządzanie użyciem magazynu Recovery Services |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/Read | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupconfig/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupValidateOperation/Action | Sprawdź poprawność operacji dla chronionego elementu |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Write | Operacja Utwórz magazyn tworzy zasób platformy Azure typu "magazyn" |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/Read | Zwraca stan operacji kopii zapasowej dla magazynu Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/Read | Zwraca wszystkie serwery zarządzania kopiami zapasowymi zarejestrowane w magazynie. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectableContainers/Read | Pobierz wszystkie kontenery z ochroną |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupStatus/Action | Sprawdź stan kopii zapasowej Recovery Services magazynów |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupPreValidateProtection/Action |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupValidateFeatures/Action | Weryfikuj funkcje |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/Write | Rozwiązuje alert. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Operations/Read | Operacja zwraca listę operacji dla dostawcy zasobów |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/operationStatus/Read | Pobiera stan operacji dla danej operacji |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/Read | Wyświetl listę wszystkich intencji ochrony kopii zapasowej |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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

Umożliwia zarządzanie usługami kopii zapasowych, z wyjątkiem usuwania kopii zapasowych, tworzenia magazynu i zapewniania [dostępu innym](../backup/backup-rbac-rs-vault.md) osobom.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | Pobierz definicję sieci wirtualnej |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/Read | Zwraca stan operacji |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/operationResults/Read | Pobiera wynik operacji wykonanej na kontenerze ochrony. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/Backup/Action | Wykonuje kopię zapasową dla chronionego elementu. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/Read | Pobiera wynik operacji wykonanej na elementach chronionych. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/Read | Zwraca stan operacji wykonanej na elementach chronionych. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/Read | Zwraca szczegóły obiektu chronionego elementu |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/Action | Zapewnij natychmiastowe odzyskiwanie elementu dla chronionego elementu |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/accessToken/Action | Pobierz AccessToken do przywracania między regionami. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/Read | Pobierz punkty odzyskiwania dla elementów chronionych. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/Restore/Action | Przywracanie punktów odzyskiwania dla elementów chronionych. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/Action | Odwołaj natychmiastowe odzyskiwanie elementu dla chronionego elementu |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/Write | Utwórz chroniony element kopii zapasowej |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/Read | Zwraca wszystkie zarejestrowane kontenery |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/refreshContainers/Action | Odświeża listę kontenerów |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/* | Tworzenie zadań tworzenia kopii zapasowych i zarządzanie nimi |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/Action | Eksportuj zadania |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/* | Twórz wyniki operacji zarządzania kopiami zapasowymi i zarządzaj nimi |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operationResults/Read | Pobierz wyniki operacji zasad. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/Read | Zwraca wszystkie zasady ochrony |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectableItems/* | Utwórz elementy, których kopię zapasową można utworzyć, i zarządzaj nimi |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/Read | Zwraca listę wszystkich elementów chronionych. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/Read | Zwraca wszystkie kontenery należące do subskrypcji |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/Read | Zwraca podsumowania elementów chronionych i serwerów chronionych dla Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Certificates/Write | Operacja Aktualizuj certyfikat zasobu aktualizuje certyfikat poświadczeń zasobu/magazynu. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/Read | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu? |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/Write | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu? |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/Read | Pobiera alerty dla magazynu usługi Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "magazyn" |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/Read | Operacja Pobierz wyniki operacji umożliwia pobieranie stanu operacji i wyniku operacji przesłanej asynchronicznie |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/Read | Operacja Pobierz kontenery umożliwia pobieranie kontenerów zarejestrowanych dla zasobu. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/Write | Operacji rejestrowania kontenera usługi można użyć do zarejestrowania kontenera z usługą odzyskiwania. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Usages/Read | Zwraca szczegóły użycia magazynu Recovery Servicesowego. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/Read | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupValidateOperation/Action | Sprawdź poprawność operacji dla chronionego elementu |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/Read | Zwraca stan operacji kopii zapasowej dla magazynu Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/Operations/Read | Pobierz stan operacji zasad. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/Write | Tworzy zarejestrowany kontener |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/Inquire/Action | Wykonaj zapytanie dotyczące obciążeń w kontenerze |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/Read | Zwraca wszystkie serwery zarządzania kopiami zapasowymi zarejestrowane w magazynie. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/Write | Tworzenie opcji ochrony kopii zapasowej |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/Read | Pobierz cel ochrony kopii zapasowej |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectableContainers/Read | Pobierz wszystkie kontenery z ochroną |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/Items/Read | Pobierz wszystkie elementy w kontenerze |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupStatus/Action | Sprawdź stan kopii zapasowej Recovery Services magazynów |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupPreValidateProtection/Action |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupValidateFeatures/Action | Weryfikuj funkcje |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupAadProperties/Read | Uzyskaj właściwości usługi AAD do uwierzytelniania w trzecim regionie do przywracania między regionami. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupCrrJobs/Action | Wyświetl listę zadań przywracania między regionami w regionie pomocniczym dla magazynu Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupCrrJob/Action | Pobierz szczegóły zadania przywracania między regionami w regionie pomocniczym dla magazynu Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupCrossRegionRestore/Action | Wyzwalanie przywracania między regionami. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupCrrOperationResults/Read | Zwraca wynik operacji CRR dla magazynu Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupCrrOperationsStatus/Read | Zwraca stan operacji CRR dla magazynu Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/Write | Rozwiązuje alert. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Operations/Read | Operacja zwraca listę operacji dla dostawcy zasobów |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/operationStatus/Read | Pobiera stan operacji dla danej operacji |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/Read | Wyświetl listę wszystkich intencji ochrony kopii zapasowej |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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
        "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/accessToken/action",
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
        "Microsoft.RecoveryServices/locations/backupAadProperties/read",
        "Microsoft.RecoveryServices/locations/backupCrrJobs/action",
        "Microsoft.RecoveryServices/locations/backupCrrJob/action",
        "Microsoft.RecoveryServices/locations/backupCrossRegionRestore/action",
        "Microsoft.RecoveryServices/locations/backupCrrOperationResults/read",
        "Microsoft.RecoveryServices/locations/backupCrrOperationsStatus/read",
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

Może wyświetlać usługi kopii zapasowych, ale nie może [wprowadzać zmian](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/allocatedStamp/Read | Operacja getallocatedstamp jest operacją wewnętrzną używaną przez usługę |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/Read | Zwraca stan operacji |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/operationResults/Read | Pobiera wynik operacji wykonanej na kontenerze ochrony. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/Read | Pobiera wynik operacji wykonanej na elementach chronionych. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/Read | Zwraca stan operacji wykonanej na elementach chronionych. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/Read | Zwraca szczegóły obiektu chronionego elementu |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/Read | Pobierz punkty odzyskiwania dla elementów chronionych. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/Read | Zwraca wszystkie zarejestrowane kontenery |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/operationResults/Read | Zwraca wynik operacji zadania. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/Read | Zwraca wszystkie obiekty zadań |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/Action | Eksportuj zadania |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/Read | Zwraca wynik operacji tworzenia kopii zapasowej dla magazynu Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operationResults/Read | Pobierz wyniki operacji zasad. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/Read | Zwraca wszystkie zasady ochrony |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/Read | Zwraca listę wszystkich elementów chronionych. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/Read | Zwraca wszystkie kontenery należące do subskrypcji |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/Read | Zwraca podsumowania elementów chronionych i serwerów chronionych dla Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/Read | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu? |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/Read | Pobiera alerty dla magazynu usługi Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "magazyn" |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/Read | Operacja Pobierz wyniki operacji umożliwia pobieranie stanu operacji i wyniku operacji przesłanej asynchronicznie |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/Read | Operacja Pobierz kontenery umożliwia pobieranie kontenerów zarejestrowanych dla zasobu. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/Read | Zwraca konfigurację magazynu dla magazynu Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupconfig/Read | Zwraca konfigurację magazynu Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/Read | Zwraca stan operacji kopii zapasowej dla magazynu Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/Operations/Read | Pobierz stan operacji zasad. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/Read | Zwraca wszystkie serwery zarządzania kopiami zapasowymi zarejestrowane w magazynie. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/Read | Pobierz cel ochrony kopii zapasowej |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/Items/Read | Pobierz wszystkie elementy w kontenerze |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupStatus/Action | Sprawdź stan kopii zapasowej Recovery Services magazynów |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/Write | Rozwiązuje alert. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Operations/Read | Operacja zwraca listę operacji dla dostawcy zasobów |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/operationStatus/Read | Pobiera stan operacji dla danej operacji |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/Read | Wyświetl listę wszystkich intencji ochrony kopii zapasowej |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Usages/Read | Zwraca szczegóły użycia magazynu Recovery Servicesowego. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupValidateFeatures/Action | Weryfikuj funkcje |
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
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/* | Tworzenie kont magazynu i zarządzanie nimi |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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

Operatorzy kluczy klasycznego konta magazynu mogą wyświetlać i generować ponownie klucze na klasycznych kontach magazynu [dowiedzieć się więcej](../key-vault/secrets/overview-storage-keys.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/ListKeys/Action | Wyświetla listę kluczy dostępu dla kont magazynu. |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/regeneratekey/Action | Generuje ponownie istniejące klucze dostępu dla konta magazynu. |
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

Umożliwia zarządzanie wszystko w obszarze usługi urządzenie Data Box z wyjątkiem udzielenia dostępu innym osobom. [Dowiedz się więcej](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft. DATAbox](resource-provider-operations.md#microsoftdatabox)/* |  |
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

Umożliwia zarządzanie usługą urządzenie Data Box, z wyjątkiem tworzenia kolejności lub edytowania szczegółów kolejności i udzielania dostępu innym osobom. [Dowiedz się więcej](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. DATAbox](resource-provider-operations.md#microsoftdatabox)/*/Read |  |
> | [Microsoft. DATAbox](resource-provider-operations.md#microsoftdatabox)/Jobs/listsecrets/Action |  |
> | [Microsoft. DATAbox](resource-provider-operations.md#microsoftdatabox)/Jobs/listcredentials/Action | Wyświetla listę nieszyfrowanych poświadczeń związanych z kolejnością. |
> | [Microsoft. DATAbox](resource-provider-operations.md#microsoftdatabox)/Locations/availableSkus/Action | Ta metoda zwraca listę dostępnych jednostek SKU. |
> | [Microsoft. DATAbox](resource-provider-operations.md#microsoftdatabox)/Locations/validateInputs/Action | Ta metoda wykonuje wszystkie typy walidacji. |
> | [Microsoft. DATAbox](resource-provider-operations.md#microsoftdatabox)/Locations/regionConfiguration/Action | Ta metoda zwraca konfiguracje dla regionu. |
> | [Microsoft. DATAbox](resource-provider-operations.md#microsoftdatabox)/Locations/validateAddress/Action | Sprawdza poprawność adresu wysyłkowego i udostępnia alternatywne adresy, jeśli istnieją. |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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

Umożliwia przesyłanie własnych zadań, monitorowanie ich i zarządzanie nimi, ale nie tworzenie ani usuwanie kont Data Lake Analytics. [Dowiedz się więcej](../data-lake-analytics/data-lake-analytics-manage-use-portal.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. BigAnalytics/accounts/* |  |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | Microsoft. BigAnalytics/accounts/Delete |  |
> | Microsoft. BigAnalytics/accounts/TakeOwnership/Action |  |
> | Microsoft. BigAnalytics/accounts/Write |  |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/Delete | Usuń konto DataLakeAnalytics. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/TakeOwnership/Action | Udziel uprawnień do anulowania zadań przesłanych przez innych użytkowników. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/Write | Utwórz lub zaktualizuj konto DataLakeAnalytics. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/dataLakeStoreAccounts/Write | Utwórz lub zaktualizuj połączone konto kontach datalakestore konta DataLakeAnalytics. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/dataLakeStoreAccounts/Delete | Odłączanie konta kontach datalakestore z konta DataLakeAnalytics. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/storageAccounts/Write | Utwórz lub zaktualizuj połączone konto magazynu dla konta DataLakeAnalytics. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/storageAccounts/Delete | Odłączanie konta magazynu od konta DataLakeAnalytics. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/firewallRules/Write | Utwórz lub zaktualizuj regułę zapory. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/firewallRules/Delete | Usuń regułę zapory. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/computePolicies/Write | Utwórz lub zaktualizuj zasady obliczeniowe. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/computePolicies/Delete | Usuń zasady obliczeń. |
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
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/Action | Zwraca klucze dostępu dla określonego konta magazynu. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/ListAccountSas/Action | Zwraca token sygnatury dostępu współdzielonego konta dla określonego konta magazynu. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/Read | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
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

Umożliwia zarządzanie kontami magazynu. Zapewnia dostęp do klucza konta, który może służyć do uzyskiwania dostępu do danych za pośrednictwem autoryzacji klucza współużytkowanego. [Dowiedz się więcej](../storage/common/storage-auth-aad.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Tworzy, aktualizuje lub odczytuje ustawienia diagnostyczne dla Analysis Server |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Subnets/joinViaServiceEndpoint/Action | Dołącza do podsieci zasoby, takie jak konto magazynu lub baza danych SQL. Brak alertów. |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/* | Tworzenie kont magazynu i zarządzanie nimi |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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

Zezwala na wyświetlanie i ponowne generowanie kluczy dostępu do konta magazynu. [Dowiedz się więcej](../storage/common/storage-account-keys-manage.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/ListKeys/Action | Zwraca klucze dostępu dla określonego konta magazynu. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/regeneratekey/Action | Generuje ponownie klucze dostępu dla określonego konta magazynu. |
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

### <a name="storage-blob-data-contributor"></a>Współautor danych obiektu blob usługi Storage

Odczytuj, zapisuj i usuwaj kontenery i obiekty blob usługi Azure Storage. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Dowiedz się więcej](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Delete | Usuń kontener. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Read | Zwraca kontener lub listę kontenerów. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Write | Modyfikowanie metadanych lub właściwości kontenera. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/Action | Zwraca klucz delegowania użytkownika dla Blob service. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Blobs/Delete | Usuń obiekt BLOB. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Blobs/Read | Zwraca obiekt BLOB lub listę obiektów BLOB. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Blobs/Write | Zapisz w obiekcie blob. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Blobs/Move/Action | Przenosi obiekt BLOB z jednej ścieżki do innej |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Blobs/Add/Action | Zwraca wynik dodania zawartości obiektu BLOB |
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
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-owner"></a>Właściciel danych obiektu blob usługi Storage

Zapewnia pełen dostęp do kontenerów obiektów blob i danych usługi Azure Storage, w tym do przypisywania kontroli dostępu POSIX. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Dowiedz się więcej](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/* | Pełne uprawnienia do kontenerów. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/Action | Zwraca klucz delegowania użytkownika dla Blob service. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Blobs/* | Pełne uprawnienia do obiektów BLOB. |
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

### <a name="storage-blob-data-reader"></a>Czytelnik danych obiektu blob usługi Storage

Odczytuj i wyświetlaj kontenery usługi Azure Storage oraz obiekty blob. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Dowiedz się więcej](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Read | Zwraca kontener lub listę kontenerów. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/Action | Zwraca klucz delegowania użytkownika dla Blob service. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/Containers/Blobs/Read | Zwraca obiekt BLOB lub listę obiektów BLOB. |
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

Pobierz klucz delegowania użytkownika, którego można następnie użyć do utworzenia sygnatury dostępu współdzielonego dla kontenera lub obiektu BLOB podpisanego przy użyciu poświadczeń usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Tworzenie skojarzeń zabezpieczeń dla delegowania użytkowników](/rest/api/storageservices/create-user-delegation-sas). [Dowiedz się więcej](/rest/api/storageservices/get-user-delegation-key)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/Action | Zwraca klucz delegowania użytkownika dla Blob service. |
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

### <a name="storage-file-data-smb-share-contributor"></a>Współautor udziału SMB danych w pliku magazynu

Zezwala na dostęp do odczytu, zapisu i usuwania w plikach/katalogach w udziałach plików platformy Azure. Ta rola nie ma wbudowanego odpowiedniku na serwerach plików systemu Windows. [Dowiedz się więcej](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/Files/Read | Zwraca plik/folder lub listę plików/folderów. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/Files/Write | Zwraca wynik zapisania pliku lub utworzenia folderu. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/Files/Delete | Zwraca wynik usunięcia pliku/folderu. |
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

### <a name="storage-file-data-smb-share-elevated-contributor"></a>Współautor udziału SMB danych w pliku magazynu z podwyższonym poziomem uprawnień

Zezwala na odczyt, zapis, usuwanie i modyfikowanie list ACL dla plików/katalogów w udziałach plików platformy Azure. Ta rola jest równoważna z listą ACL udziałów plików na serwerach plików systemu Windows. [Dowiedz się więcej](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/Files/Read | Zwraca plik/folder lub listę plików/folderów. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/Files/Write | Zwraca wynik zapisania pliku lub utworzenia folderu. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/Files/Delete | Zwraca wynik usunięcia pliku/folderu. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/Files/modifypermissions/Action | Zwraca wynik modyfikacji uprawnienia do pliku/folderu. |
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

### <a name="storage-file-data-smb-share-reader"></a>Czytelnik udziału SMB danych w pliku magazynu

Zezwala na dostęp do odczytu dla plików/katalogów w udziałach plików platformy Azure. Ta rola jest równoważna z listą ACL udziału plików odczytywaną na serwerach plików systemu Windows. [Dowiedz się więcej](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/Files/Read | Zwraca plik/folder lub listę plików/folderów. |
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

Odczytuj, zapisuj i usuwaj kolejki usługi Azure Storage oraz wiadomości w kolejce. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Dowiedz się więcej](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/Delete | Usuń kolejkę. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/Read | Zwróć kolejkę lub listę kolejek. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/Write | Modyfikuj metadane lub właściwości kolejki. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/messages/Delete | Usuń co najmniej jeden komunikat z kolejki. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/messages/Read | Wgląd lub pobranie co najmniej jednego komunikatu z kolejki. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/messages/Write | Dodaj komunikat do kolejki. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/messages/Process/Action | Zwraca wynik przetwarzania komunikatu |
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
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/write",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action"
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

Wgląd, pobieranie i usuwanie wiadomości z kolejki usługi Azure Storage. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Dowiedz się więcej](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/messages/Read | Wgląd do wiadomości. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/messages/Process/Action | Pobierz i Usuń komunikat. |
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

Dodawanie komunikatów do kolejki usługi Azure Storage. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Dowiedz się więcej](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/messages/Add/Action | Dodaj komunikat do kolejki. |
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

Odczytuj i wyświetlaj kolejki usługi Azure Storage oraz wiadomości w kolejce. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Dowiedz się więcej](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/Read | Zwraca kolejkę lub listę kolejek. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/messages/Read | Wgląd lub pobranie co najmniej jednego komunikatu z kolejki. |
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

## <a name="web"></a>Internet


### <a name="azure-maps-data-contributor"></a>Współautor danych Azure Maps

Przyznaje dostęp do odczytu, zapisu i usuwania, aby mapować powiązane dane z konta usługi Azure Maps. [Dowiedz się więcej](../azure-maps/azure-maps-authentication.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/Read |  |
> | [Microsoft. Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/Write |  |
> | [Microsoft. Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/Delete |  |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read, write, and delete access to map related data from an Azure maps account.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8f5e0ce6-4f7b-4dcf-bddf-e6f48634a204",
  "name": "8f5e0ce6-4f7b-4dcf-bddf-e6f48634a204",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Maps/accounts/*/read",
        "Microsoft.Maps/accounts/*/write",
        "Microsoft.Maps/accounts/*/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Maps Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-maps-data-reader"></a>Azure Maps czytnika danych

Przyznaje dostęp do odczytu danych związanych z mapowaniem z konta usługi Azure Maps. [Dowiedz się więcej](../azure-maps/azure-maps-authentication.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/Read |  |
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

Umożliwia zarządzanie usługami wyszukiwania, ale nie umożliwia uzyskiwania do nich dostępu. [Dowiedz się więcej](../search/search-security-rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Search](resource-provider-operations.md#microsoftsearch)/searchServices/* | Tworzenie usług wyszukiwania i zarządzanie nimi |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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

### <a name="signalr-accesskey-reader"></a>Czytnik AccessKeyer sygnalizujący

Odczytaj klucze dostępu do usługi sygnalizującego

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/*/Read |  |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/ListKeys/Action | Wyświetlanie wartości kluczy dostępu sygnalizującego w portalu zarządzania lub za pomocą interfejsu API |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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
  "description": "Read SignalR Service Access Keys",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/04165923-9d83-45d5-8227-78b77b0a687e",
  "name": "04165923-9d83-45d5-8227-78b77b0a687e",
  "permissions": [
    {
      "actions": [
        "Microsoft.SignalRService/*/read",
        "Microsoft.SignalRService/SignalR/listkeys/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR AccessKey Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-app-server-preview"></a>Serwer aplikacji sygnalizujący (wersja zapoznawcza)

Umożliwia usłudze Sygnalizującemu dostęp do serwera aplikacji z opcjami uwierzytelniania usługi AAD.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/AccessKey/Action | Generuj tymczasowy AccessKey do podpisywania ClientTokens. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/ServerConnection/Write | Rozpocznij połączenie z serwerem. |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets your app server access SignalR Service with AAD auth options.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/420fcaa2-552c-430f-98ca-3264be4806c7",
  "name": "420fcaa2-552c-430f-98ca-3264be4806c7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/auth/accessKey/action",
        "Microsoft.SignalRService/SignalR/serverConnection/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR App Server (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-contributor"></a>Współautor sygnalizującego

Tworzenie, odczytywanie, aktualizowanie i usuwanie zasobów usługi sygnalizującego

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/* |  |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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
  "description": "Create, Read, Update, and Delete SignalR service resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761",
  "name": "8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761",
  "permissions": [
    {
      "actions": [
        "Microsoft.SignalRService/*",
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
  "roleName": "SignalR Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-serverless-contributor-preview"></a>Współautor bezserwerowy sygnalizujący (wersja zapoznawcza)

Umożliwia usłudze dostęp do aplikacji w trybie bezserwerowym z opcjami uwierzytelniania usługi AAD.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/clientToken/Action | Generowanie ClientToken do uruchamiania połączenia z klientem. |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets your app access service in serverless mode with AAD auth options.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fd53cd77-2268-407a-8f46-7e7863d0f521",
  "name": "fd53cd77-2268-407a-8f46-7e7863d0f521",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/auth/clientToken/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Serverless Contributor (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-service-owner-preview"></a>Właściciel usługi sygnalizującej (wersja zapoznawcza)

Pełny dostęp do interfejsów API REST usługi Azure Signal Service

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/Hub/Send/Action | Emituj komunikaty do wszystkich połączeń klientów w centrum. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/Group/Send/Action | Emituj komunikat do grupy. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/Group/Read | Sprawdź, czy istnieje grupa lub czy użytkownik istnieje w grupie. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/Group/Write | Dołącz/Opuść grupę. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/Send/Action | Wysyłaj komunikaty bezpośrednio do połączenia klienta. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/Read | Sprawdź istnienie połączenia z klientem. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/Write | Zamknij połączenie z klientem. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/User/Send/Action | Wysyłanie komunikatów do użytkownika, którzy mogą składać się z wielu połączeń klienta. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/User/Read | Sprawdź istnienie użytkownika. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/User/Write |  |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access to Azure SignalR Service REST APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7e4f1700-ea5a-4f59-8f37-079cfe29dce3",
  "name": "7e4f1700-ea5a-4f59-8f37-079cfe29dce3",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/hub/send/action",
        "Microsoft.SignalRService/SignalR/group/send/action",
        "Microsoft.SignalRService/SignalR/group/read",
        "Microsoft.SignalRService/SignalR/group/write",
        "Microsoft.SignalRService/SignalR/clientConnection/send/action",
        "Microsoft.SignalRService/SignalR/clientConnection/read",
        "Microsoft.SignalRService/SignalR/clientConnection/write",
        "Microsoft.SignalRService/SignalR/user/send/action",
        "Microsoft.SignalRService/SignalR/user/read",
        "Microsoft.SignalRService/SignalR/user/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Service Owner (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-service-reader-preview"></a>Czytnik usługi sygnalizującej (wersja zapoznawcza)

Dostęp tylko do odczytu do interfejsów API REST usługi Azure Signal

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/Group/Read | Sprawdź, czy istnieje grupa lub czy użytkownik istnieje w grupie. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/Read | Sprawdź istnienie połączenia z klientem. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/User/Read | Sprawdź istnienie użytkownika. |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only access to Azure SignalR Service REST APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ddde6b66-c0df-4114-a159-3618637b3035",
  "name": "ddde6b66-c0df-4114-a159-3618637b3035",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/group/read",
        "Microsoft.SignalRService/SignalR/clientConnection/read",
        "Microsoft.SignalRService/SignalR/user/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Service Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="web-plan-contributor"></a>Współautor planu sieci Web

Umożliwia zarządzanie planami sieci Web dla witryn internetowych, ale nie umożliwia uzyskiwania do nich dostępu.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/serverFarms/* | Tworzenie farm serwerów i zarządzanie nimi |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/hostingEnvironments/Join/Action | Sprzęga App Service Environment |
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
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Components/* | Tworzenie składników usługi Insights i zarządzanie nimi |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/Certificates/* | Tworzenie i zarządzanie certyfikatami witryny sieci Web |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/listSitesAssignedToHostName/Read | Pobieranie nazw lokacji przypisanych do nazwy hosta. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/serverFarms/Join/Action | Sprzęga plan App Service |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/serverFarms/Read | Pobierz właściwości planu App Service |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/sites/* | Tworzenie witryn sieci Web i zarządzanie nimi (Tworzenie witryn wymaga również uprawnień do zapisu w skojarzonym planie App Service) |
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

## <a name="containers"></a>Kontenery


### <a name="acrdelete"></a>AcrDelete

ACR Usuń [więcej informacji](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/Registries/Artifacts/Delete | Usuń artefakt w rejestrze kontenerów. |
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

osoba podpisująca obraz ACR [Dowiedz się więcej](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/Registries/Sign/Write | Wypychanie/Ściąganie metadanych zaufania zawartości dla rejestru kontenerów. |
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

ACR Ściągnij [więcej informacji](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/Registries/pull/Read | Ściąganie lub pobieranie obrazów z rejestru kontenerów. |
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

ACR wypychanie [Dowiedz się więcej](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/Registries/pull/Read | Ściąganie lub pobieranie obrazów z rejestru kontenerów. |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/Registries/push/Write | Wypchnij lub Zapisz obrazy do rejestru kontenerów. |
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
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/Registries/Quarantine/Read | Ściąganie lub pobieranie obrazów z kwarantanny z rejestru kontenerów |
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
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/Registries/Quarantine/Read | Ściąganie lub pobieranie obrazów z kwarantanny z rejestru kontenerów |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/Registries/Quarantine/Write | Zapisywanie/modyfikowanie stanu kwarantanny obrazów z kwarantanny |
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

Wyświetl listę akcji poświadczeń administratora klastra. [Dowiedz się więcej](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterAdminCredential/Action | Wyświetlanie clusterAdmin poświadczenia zarządzanego klastra |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/accessProfiles/listCredential/Action | Uzyskiwanie zarządzanego profilu dostępu do klastra według nazwy roli przy użyciu poświadczeń listy |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Read | Pobierz zarządzany klaster |
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
        "Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action",
        "Microsoft.ContainerService/managedClusters/read"
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

Wyświetl listę akcji poświadczeń użytkownika klastra. [Dowiedz się więcej](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/Action | Wyświetlanie clusterUser poświadczenia zarządzanego klastra |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Read | Pobierz zarządzany klaster |
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
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action",
        "Microsoft.ContainerService/managedClusters/read"
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

### <a name="azure-kubernetes-service-contributor-role"></a>Rola współautora usługi Azure Kubernetes

Przyznaje dostęp do odczytu i zapisu klastrów usługi Azure Kubernetes, [Dowiedz się więcej](../aks/concepts-identity.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Read | Pobierz zarządzany klaster |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Write | Tworzy nowy zarządzany klaster lub aktualizuje istniejący. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
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
  "description": "Grants access to read and write Azure Kubernetes Service clusters",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8",
  "name": "ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/read",
        "Microsoft.ContainerService/managedClusters/write",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-admin"></a>Administrator RBAC usługi Azure Kubernetes Service

Umożliwia zarządzanie wszystkimi zasobami w obszarze klaster/przestrzeń nazw, z wyjątkiem aktualizacji lub usuwania przydziałów zasobów i przestrzeni nazw. [Dowiedz się więcej](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Write | Tworzy lub aktualizuje wdrożenie. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/Read | Pobierz wyniki operacji subskrypcji. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/Read | Pobiera listę subskrypcji. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/Action | Wyświetlanie clusterUser poświadczenia zarządzanego klastra |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
> | **NotDataActions** |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/Write | Zapisuje resourcequotas |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/Delete | Usuwa resourcequotas |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Namespaces/Write | Zapisuje przestrzenie nazw |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Namespaces/Delete | Usuwa przestrzenie nazw |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources under cluster/namespace, except update or delete resource quotas and namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3498e952-d568-435e-9b2c-8d77e338d7f7",
  "name": "3498e952-d568-435e-9b2c-8d77e338d7f7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*"
      ],
      "notDataActions": [
        "Microsoft.ContainerService/managedClusters/resourcequotas/write",
        "Microsoft.ContainerService/managedClusters/resourcequotas/delete",
        "Microsoft.ContainerService/managedClusters/namespaces/write",
        "Microsoft.ContainerService/managedClusters/namespaces/delete"
      ]
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-cluster-admin"></a>Administrator klastra RBAC usługi Azure Kubernetes Service

Umożliwia zarządzanie wszystkimi zasobami w klastrze. [Dowiedz się więcej](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Write | Tworzy lub aktualizuje wdrożenie. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/Read | Pobierz wyniki operacji subskrypcji. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/Read | Pobiera listę subskrypcji. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/Action | Wyświetlanie clusterUser poświadczenia zarządzanego klastra |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources in the cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b",
  "name": "b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Cluster Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-reader"></a>Czytnik RBAC usługi Azure Kubernetes Service

Zezwala na dostęp tylko do odczytu do wyświetlania większości obiektów w przestrzeni nazw. Nie zezwala na wyświetlanie ról ani powiązań ról. Ta rola nie zezwala na wyświetlanie wpisów tajnych, ponieważ odczytywanie zawartości wpisów tajnych umożliwia dostęp do poświadczeń usługi ServiceAccount w przestrzeni nazw, co umożliwia dostęp do interfejsu API jako dowolnego elementu ServiceAccount w przestrzeni nazw (w formie eskalacji uprawnień). Zastosowanie tej roli w zakresie klastra zapewnia dostęp do wszystkich przestrzeni nazw. [Dowiedz się więcej](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Write | Tworzy lub aktualizuje wdrożenie. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/Read | Pobierz wyniki operacji subskrypcji. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/Read | Pobiera listę subskrypcji. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Apps/controllerrevisions/Read | Odczytuje controllerrevisions |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Apps/daemonsets/Read | Odczytuje daemonsets |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Apps/Deployments/Read | Odczytuje wdrożenia |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Apps/replicasets/Read | Odczytuje replicasets |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Apps/statefulsets/Read | Odczytuje statefulsets |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/autoscaling/horizontalpodautoscalers/Read | Odczytuje horizontalpodautoscalers |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Batch/cronjobs/Read | Odczytuje cronjobs |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Batch/Jobs/Read | Odczytuje zadania |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/configmaps/Read | Odczytuje configmaps |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Endpoints/Read | Odczytuje punkty końcowe |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Events.k8s.IO/Events/Read | Odczytuje zdarzenia |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Events/Read | Odczytuje zdarzenia |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/daemonsets/Read | Odczytuje daemonsets |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/Deployments/Read | Odczytuje wdrożenia |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/ingresses/Read | Odczytuje ingresses |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/networkpolicies/Read | Odczytuje networkpolicies |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/replicasets/Read | Odczytuje replicasets |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/limitranges/Read | Odczytuje limitranges |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Namespaces/Read | Odczytuje przestrzenie nazw |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Networking.k8s.IO/ingresses/Read | Odczytuje ingresses |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Networking.k8s.IO/networkpolicies/Read | Odczytuje networkpolicies |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/persistentvolumeclaims/Read | Odczytuje persistentvolumeclaims |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/pods/Read | Odczytuje/zasobniki |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Policy/poddisruptionbudgets/Read | Odczytuje poddisruptionbudgets |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/Read | Odczytuje replicationcontrollers |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/Read | Odczytuje replicationcontrollers |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/Read | Odczytuje resourcequotas |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/serviceaccounts/Read | Odczytuje serviceaccounts |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Services/Read | Odczytuje usługi |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read-only access to see most objects in a namespace. It does not allow viewing roles or role bindings. This role does not allow viewing Secrets, since reading the contents of Secrets enables access to ServiceAccount credentials in the namespace, which would allow API access as any ServiceAccount in the namespace (a form of privilege escalation). Applying this role at cluster scope will give access across all namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f6c6a51-bcf8-42ba-9220-52d62157d7db",
  "name": "7f6c6a51-bcf8-42ba-9220-52d62157d7db",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/apps/controllerrevisions/read",
        "Microsoft.ContainerService/managedClusters/apps/daemonsets/read",
        "Microsoft.ContainerService/managedClusters/apps/deployments/read",
        "Microsoft.ContainerService/managedClusters/apps/replicasets/read",
        "Microsoft.ContainerService/managedClusters/apps/statefulsets/read",
        "Microsoft.ContainerService/managedClusters/autoscaling/horizontalpodautoscalers/read",
        "Microsoft.ContainerService/managedClusters/batch/cronjobs/read",
        "Microsoft.ContainerService/managedClusters/batch/jobs/read",
        "Microsoft.ContainerService/managedClusters/configmaps/read",
        "Microsoft.ContainerService/managedClusters/endpoints/read",
        "Microsoft.ContainerService/managedClusters/events.k8s.io/events/read",
        "Microsoft.ContainerService/managedClusters/events/read",
        "Microsoft.ContainerService/managedClusters/extensions/daemonsets/read",
        "Microsoft.ContainerService/managedClusters/extensions/deployments/read",
        "Microsoft.ContainerService/managedClusters/extensions/ingresses/read",
        "Microsoft.ContainerService/managedClusters/extensions/networkpolicies/read",
        "Microsoft.ContainerService/managedClusters/extensions/replicasets/read",
        "Microsoft.ContainerService/managedClusters/limitranges/read",
        "Microsoft.ContainerService/managedClusters/namespaces/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/ingresses/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/networkpolicies/read",
        "Microsoft.ContainerService/managedClusters/persistentvolumeclaims/read",
        "Microsoft.ContainerService/managedClusters/pods/read",
        "Microsoft.ContainerService/managedClusters/policy/poddisruptionbudgets/read",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/read",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/read",
        "Microsoft.ContainerService/managedClusters/resourcequotas/read",
        "Microsoft.ContainerService/managedClusters/serviceaccounts/read",
        "Microsoft.ContainerService/managedClusters/services/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-writer"></a>Składnik zapisywania RBAC usługi Azure Kubernetes Service

Zezwala na dostęp do odczytu/zapisu do większości obiektów w przestrzeni nazw. Ta rola nie zezwala na przeglądanie ani modyfikowanie ról ani powiązań ról. Jednak ta rola umożliwia dostęp do wpisów tajnych i uruchamianie zasobników jako dowolnego konta ServiceAccount w przestrzeni nazw, dlatego może służyć do uzyskania poziomów dostępu interfejsu API dowolnego konta ServiceAccount w przestrzeni nazw. Zastosowanie tej roli w zakresie klastra zapewnia dostęp do wszystkich przestrzeni nazw. [Dowiedz się więcej](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Write | Tworzy lub aktualizuje wdrożenie. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/Read | Pobierz wyniki operacji subskrypcji. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/Read | Pobiera listę subskrypcji. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Apps/controllerrevisions/Read | Odczytuje controllerrevisions |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Apps/daemonsets/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Apps/Deployments/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Apps/replicasets/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Apps/statefulsets/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/autoscaling/horizontalpodautoscalers/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Batch/cronjobs/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Batch/Jobs/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/configmaps/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Endpoints/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Events.k8s.IO/Events/Read | Odczytuje zdarzenia |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Events/Read | Odczytuje zdarzenia |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/daemonsets/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/Deployments/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/ingresses/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/networkpolicies/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/replicasets/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/limitranges/Read | Odczytuje limitranges |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Namespaces/Read | Odczytuje przestrzenie nazw |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Networking.k8s.IO/ingresses/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Networking.k8s.IO/networkpolicies/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/persistentvolumeclaims/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/pods/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Policy/poddisruptionbudgets/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/Read | Odczytuje resourcequotas |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Secrets/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/serviceaccounts/* |  |
> | [Microsoft. ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Services/* |  |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read/write access to most objects in a namespace.This role does not allow viewing or modifying roles or role bindings. However, this role allows accessing Secrets and running Pods as any ServiceAccount in the namespace, so it can be used to gain the API access levels of any ServiceAccount in the namespace. Applying this role at cluster scope will give access across all namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb",
  "name": "a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/apps/controllerrevisions/read",
        "Microsoft.ContainerService/managedClusters/apps/daemonsets/*",
        "Microsoft.ContainerService/managedClusters/apps/deployments/*",
        "Microsoft.ContainerService/managedClusters/apps/replicasets/*",
        "Microsoft.ContainerService/managedClusters/apps/statefulsets/*",
        "Microsoft.ContainerService/managedClusters/autoscaling/horizontalpodautoscalers/*",
        "Microsoft.ContainerService/managedClusters/batch/cronjobs/*",
        "Microsoft.ContainerService/managedClusters/batch/jobs/*",
        "Microsoft.ContainerService/managedClusters/configmaps/*",
        "Microsoft.ContainerService/managedClusters/endpoints/*",
        "Microsoft.ContainerService/managedClusters/events.k8s.io/events/read",
        "Microsoft.ContainerService/managedClusters/events/read",
        "Microsoft.ContainerService/managedClusters/extensions/daemonsets/*",
        "Microsoft.ContainerService/managedClusters/extensions/deployments/*",
        "Microsoft.ContainerService/managedClusters/extensions/ingresses/*",
        "Microsoft.ContainerService/managedClusters/extensions/networkpolicies/*",
        "Microsoft.ContainerService/managedClusters/extensions/replicasets/*",
        "Microsoft.ContainerService/managedClusters/limitranges/read",
        "Microsoft.ContainerService/managedClusters/namespaces/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/ingresses/*",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/networkpolicies/*",
        "Microsoft.ContainerService/managedClusters/persistentvolumeclaims/*",
        "Microsoft.ContainerService/managedClusters/pods/*",
        "Microsoft.ContainerService/managedClusters/policy/poddisruptionbudgets/*",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/*",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/*",
        "Microsoft.ContainerService/managedClusters/resourcequotas/read",
        "Microsoft.ContainerService/managedClusters/secrets/*",
        "Microsoft.ContainerService/managedClusters/serviceaccounts/*",
        "Microsoft.ContainerService/managedClusters/services/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="databases"></a>Bazy danych


### <a name="cosmos-db-account-reader-role"></a>Rola czytnika konta Cosmos DB

Może odczytywać Azure Cosmos DB dane konta. Aby zarządzać kontami Azure Cosmos DB, zobacz [współautor konta usługi DocumentDB](#documentdb-account-contributor) . [Dowiedz się więcej](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/*/Read | Odczytaj dowolną kolekcję |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlykeys/Action | Odczytuje klucze tylko do odczytu konta bazy danych. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/Read | Odczytaj definicje metryk |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Read | Odczytaj metryki |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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

Umożliwia zarządzanie kontami Azure Cosmos DB, ale nie dostęp do danych w nich. Uniemożliwia dostęp do kluczy konta i parametrów połączenia. [Dowiedz się więcej](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Subnets/joinViaServiceEndpoint/Action | Dołącza do podsieci zasoby, takie jak konto magazynu lub baza danych SQL. Brak alertów. |
> | **NotActions** |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlyKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/regenerateKey/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listConnectionStrings/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleDefinitions/Write | Utwórz lub zaktualizuj definicję roli SQL |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleDefinitions/Delete | Usuwanie definicji roli SQL |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleAssignments/Write | Utwórz lub zaktualizuj przypisanie roli SQL |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleAssignments/Delete | Usuwanie przypisania roli SQL |
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
        "Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/*",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions/write",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions/delete",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleAssignments/write",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleAssignments/delete"
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

Może przesłać żądanie przywracania dla bazy danych Cosmos DB lub kontenera dla konta [Dowiedz się więcej](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/Backup/Action | Prześlij żądanie skonfigurowania kopii zapasowej |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/Restore/Action | Prześlij żądanie przywrócenia |
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

### <a name="cosmosrestoreoperator"></a>CosmosRestoreOperator

Może wykonać akcję przywracania dla konta bazy danych Cosmos DB z trybem ciągłej kopii zapasowej

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/Locations/restorableDatabaseAccounts/Restore/Action | Prześlij żądanie przywrócenia |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/Locations/restorableDatabaseAccounts/*/Read |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/Locations/restorableDatabaseAccounts/Read | Odczytaj konto bazy danych dostępnych lub Wyświetl listę wszystkich kont bazy danych dostępnych |
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
  "description": "Can perform restore action for Cosmos DB database account with continuous backup mode",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5432c526-bc82-444a-b7ba-57c5b0b5b34f",
  "name": "5432c526-bc82-444a-b7ba-57c5b0b5b34f",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CosmosRestoreOperator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="documentdb-account-contributor"></a>Współautor konta DocumentDB

Może zarządzać kontami Azure Cosmos DB. Azure Cosmos DB jest dawniej znany jako DocumentDB. [Dowiedz się więcej](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/* | Tworzenie kont Azure Cosmos DB i zarządzanie nimi |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Subnets/joinViaServiceEndpoint/Action | Dołącza do podsieci zasoby, takie jak konto magazynu lub baza danych SQL. Brak alertów. |
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
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. cache](resource-provider-operations.md#microsoftcache)/Register/Action | Rejestruje dostawcę zasobów "Microsoft. cache" z subskrypcją |
> | [Microsoft. cache](resource-provider-operations.md#microsoftcache)/Redis/* | Tworzenie pamięci podręcznych Redis i zarządzanie nimi |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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
        "Microsoft.Cache/register/action",
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

Umożliwia zarządzanie bazami danych SQL, ale nie umożliwia uzyskiwania do nich dostępu. Nie można również zarządzać zasadami związanymi z zabezpieczeniami ani ich nadrzędnymi serwerami SQL. [Dowiedz się więcej](../data-share/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Locations/*/Read |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/* | Tworzenie baz danych SQL i zarządzanie nimi |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Read | Zwróć listę serwerów lub pobiera właściwości dla określonego serwera. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Read | Odczytaj metryki |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/Read | Odczytaj definicje metryk |
> | **NotActions** |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/schemas/Tables/Columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/auditingSettings/* | Edytuj ustawienia inspekcji |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/auditRecords/Read | Pobieranie rekordów inspekcji obiektów BLOB bazy danych |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/dataMaskingPolicies/* | Edytowanie zasad maskowania danych |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/extendedAuditingSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/schemas/Tables/Columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/securityAlertPolicies/* | Edytuj zasady alertów zabezpieczeń |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/securityMetrics/* | Edytuj metryki zabezpieczeń |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/vulnerabilityAssessments/* |  |
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
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
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
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/routeTables/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Locations/*/Read |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Locations/instanceFailoverGroups/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/* |  |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Subnets/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/* |  |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Read | Odczytaj metryki |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/Read | Odczytaj definicje metryk |
> | **NotActions** |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/Delete | Usuwa określony serwer zarządzany Azure Active Directory tylko obiekt uwierzytelniania |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/Write | Dodaje lub aktualizuje określony serwer zarządzany Azure Active Directory tylko obiekt uwierzytelniania |
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
        "Microsoft.Sql/locations/instanceFailoverGroups/*",
        "Microsoft.Sql/managedInstances/*",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/*",
        "Microsoft.Network/virtualNetworks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/delete",
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/write"
      ],
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

Umożliwia zarządzanie zasadami związanymi z zabezpieczeniami serwerów SQL i baz danych, ale nie umożliwia uzyskiwania do nich dostępu. [Dowiedz się więcej](../azure-sql/database/azure-defender-for-sql.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Subnets/joinViaServiceEndpoint/Action | Dołącza do podsieci zasoby, takie jak konto magazynu lub baza danych SQL. Brak alertów. |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Locations/administratorAzureAsyncOperation/Read | Pobiera wynik operacji dla administratorów asynchronicznych platformy Azure. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/schemas/Tables/Columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/transparentDataEncryption/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/auditingSettings/* | Tworzenie ustawień inspekcji programu SQL Server i zarządzanie nimi |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/extendedAuditingSettings/Read | Pobierz szczegóły zasad inspekcji rozszerzonego serwera obiektów BLOB skonfigurowanych na danym serwerze |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/auditingSettings/* | Tworzenie ustawień inspekcji bazy danych programu SQL Server i zarządzanie nimi |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/auditRecords/Read | Pobieranie rekordów inspekcji obiektów BLOB bazy danych |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/dataMaskingPolicies/* | Tworzenie zasad maskowania danych bazy danych programu SQL Server i zarządzanie nimi |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/extendedAuditingSettings/Read | Pobierz szczegóły zasad rozszerzonej inspekcji obiektów BLOB skonfigurowanych dla danej bazy danych |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/Read | Zwróć listę baz danych lub pobiera właściwości dla określonej bazy danych. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/schemas/Read | Pobierz schemat bazy danych. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/schemas/Tables/Columns/Read | Pobierz kolumnę bazy danych. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/schemas/Tables/Columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/schemas/Tables/Read | Pobierz tabelę bazy danych. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/securityAlertPolicies/* | Tworzenie zasad alertów zabezpieczeń bazy danych programu SQL Server i zarządzanie nimi |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/securityMetrics/* | Tworzenie metryk zabezpieczeń bazy danych programu SQL Server i zarządzanie nimi |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/transparentDataEncryption/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/devOpsAuditingSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/firewallRules/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Read | Zwróć listę serwerów lub pobiera właściwości dla określonego serwera. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/securityAlertPolicies/* | Tworzenie zasad alertów zabezpieczeń programu SQL Server i zarządzanie nimi |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/vulnerabilityAssessments/* |  |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/azureADOnlyAuthentications/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Read | Zwróć listę wystąpień zarządzanych lub pobiera właściwości dla określonego wystąpienia zarządzanego. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/* |  |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/sqlVulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Administrators/Read | Pobiera listę administratorów wystąpienia zarządzanego. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Administrators/Read | Pobiera określony obiekt administratora Azure Active Directory |
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
        "Microsoft.Sql/locations/administratorAzureAsyncOperation/read",
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/transparentDataEncryption/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
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
        "Microsoft.Sql/servers/devOpsAuditingSettings/*",
        "Microsoft.Sql/servers/firewallRules/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Support/*",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/*",
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/*",
        "Microsoft.Security/sqlVulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/administrators/read",
        "Microsoft.Sql/servers/administrators/read"
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

Umożliwia zarządzanie serwerami i bazami danych SQL, ale nie umożliwia uzyskiwania do nich dostępu ani zasad związanych z zabezpieczeniami. [Dowiedz się więcej](../azure-sql/database/authentication-aad-configure.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Locations/*/Read |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/* | Tworzenie serwerów SQL i zarządzanie nimi |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Read | Odczytaj metryki |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/Read | Odczytaj definicje metryk |
> | **NotActions** |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/schemas/Tables/Columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/auditingSettings/* | Edytowanie ustawień inspekcji programu SQL Server |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/auditingSettings/* | Edytowanie ustawień inspekcji bazy danych programu SQL Server |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/auditRecords/Read | Pobieranie rekordów inspekcji obiektów BLOB bazy danych |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/dataMaskingPolicies/* | Edytowanie zasad maskowania danych w bazie danych programu SQL Server |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/extendedAuditingSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/schemas/Tables/Columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/securityAlertPolicies/* | Edytowanie zasad alertów zabezpieczeń bazy danych programu SQL Server |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/securityMetrics/* | Edytuj metryki zabezpieczeń bazy danych programu SQL Server |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/devOpsAuditingSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/extendedAuditingSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/securityAlertPolicies/* | Edytuj zasady alertów zabezpieczeń programu SQL Server |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/azureADOnlyAuthentications/Delete | Usuwa określony serwer Azure Active Directory tylko obiekt uwierzytelniania |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/azureADOnlyAuthentications/Write | Dodaje lub aktualizuje określony serwer Azure Active Directory tylko obiekt uwierzytelniania |
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
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
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
        "Microsoft.Sql/servers/devOpsAuditingSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/delete",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/write"
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

Umożliwia pełny dostęp do zasobów usługi Azure Event Hubs. [Dowiedz się więcej](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
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

Zezwala na odbieranie dostępu do zasobów usługi Azure Event Hubs. [Dowiedz się więcej](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/*/eventhubs/consumergroups/Read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/*/Receive/Action |  |
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

Zezwala na wysyłanie dostępu do zasobów Event Hubs platformy Azure. [Dowiedz się więcej](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/*/eventhubs/Read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/*/Send/Action |  |
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

Twórz fabryki danych i zarządzaj nimi, a także zasobami podrzędnymi. [Dowiedz się więcej](../data-factory/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. DataFactory](resource-provider-operations.md#microsoftdatafactory)/dataFactories/* | Twórz fabryki danych i zarządzaj nimi. |
> | [Microsoft. DataFactory](resource-provider-operations.md#microsoftdatafactory)/Factories/* | Twórz fabryki danych i zarządzaj nimi. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/Write | Utwórz lub zaktualizuj eventSubscription |
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

Usuń dane prywatne z obszaru roboczego Log Analytics. [Dowiedz się więcej](../azure-monitor/logs/personal-data-mgmt.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Components/*/Read |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Components/PURGE/Action | Przeczyszczanie danych z Application Insights |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/*/Read | Wyświetlanie danych usługi log Analytics |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/PURGE/Action | Usuń określone dane z obszaru roboczego |
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

Umożliwia odczytywanie i modyfikowanie konfiguracji klastrów usługi HDInsight. [Dowiedz się więcej](../hdinsight/hdinsight-migrate-granular-access-cluster-configurations.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. HDInsight](resource-provider-operations.md#microsofthdinsight)/*/Read |  |
> | [Microsoft. HDInsight](resource-provider-operations.md#microsofthdinsight)/Clusters/getGatewaySettings/Action | Pobierz ustawienia bramy dla klastra usługi HDInsight |
> | [Microsoft. HDInsight](resource-provider-operations.md#microsofthdinsight)/Clusters/updateGatewaySettings/Action | Aktualizowanie ustawień bramy dla klastra usługi HDInsight |
> | [Microsoft. HDInsight](resource-provider-operations.md#microsofthdinsight)/Clusters/Configurations/* |  |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Operations/Read | Pobiera lub wyświetla listę operacji wdrażania. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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

Może odczytywać, tworzyć, modyfikować i usuwać powiązane z usługami domenowymi operacje dotyczące usługi HDInsight pakiet Enterprise Security [dowiedzieć się więcej](../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. AAD](resource-provider-operations.md#microsoftaad)/*/Read |  |
> | [Microsoft. AAD](resource-provider-operations.md#microsoftaad)/domainServices/*/Read |  |
> | [Microsoft. AAD](resource-provider-operations.md#microsoftaad)/domainServices/oucontainer/* |  |
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

Współautor Log Analytics może odczytywać wszystkie dane monitorowania i edytować ustawienia monitorowania. Edytowanie ustawień monitorowania obejmuje dodanie rozszerzenia maszyny wirtualnej do maszyn wirtualnych; Odczytywanie kluczy konta magazynu w celu skonfigurowania kolekcji dzienników z usługi Azure Storage; Tworzenie i Konfigurowanie kont usługi Automation; Dodawanie rozwiązań; i Konfigurowanie diagnostyki platformy Azure dla wszystkich zasobów platformy Azure. [Dowiedz się więcej](../azure-monitor/logs/manage-access.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | */read | Odczytuj zasoby wszystkich typów, z wyjątkiem kluczy tajnych. |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/* |  |
> | [Microsoft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/Extensions/* |  |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/Action | Wyświetla listę kluczy dostępu dla kont magazynu. |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/virtualMachines/Extensions/* |  |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/Extensions/Write | Instaluje lub aktualizuje rozszerzenia usługi Azure Arc |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Tworzy, aktualizuje lub odczytuje ustawienia diagnostyczne dla Analysis Server |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/* |  |
> | [Microsoft. OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/* |  |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/ResourceGroups/Deployments/* |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/Action | Zwraca klucze dostępu dla określonego konta magazynu. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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

Log Analytics Reader może wyświetlać i przeszukiwać wszystkie dane monitorowania, a także wyświetlać ustawienia monitorowania, w tym Wyświetlanie konfiguracji diagnostyki platformy Azure na wszystkich zasobach platformy Azure. [Dowiedz się więcej](../azure-monitor/logs/manage-access.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | */read | Odczytuj zasoby wszystkich typów, z wyjątkiem kluczy tajnych. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Analytics/Query/Action | Wyszukaj przy użyciu nowego aparatu. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Search/Action | Wykonuje zapytanie wyszukiwania |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/sharedKeys/Read | Pobiera klucze wspólne dla obszaru roboczego. Klucze te służą do łączenia agentów programu Microsoft Operational Insights z obszarem roboczym. |
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

### <a name="purview-data-curator"></a>Kontrolą Curator danych

Dane Microsoft. kontrolą Data Curator mogą tworzyć, odczytywać, modyfikować i usuwać obiekty danych wykazu oraz ustanawiać relacje między obiektami. Ta rola jest w wersji zapoznawczej i może ulec zmianie.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. kontrolą](resource-provider-operations.md#microsoftpurview)/accounts/Read | Odczytaj zasób konta dla dostawcy Microsoft kontrolą. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. kontrolą](resource-provider-operations.md#microsoftpurview)/accounts/Data/Read | Odczytywanie obiektów danych. |
> | [Microsoft. kontrolą](resource-provider-operations.md#microsoftpurview)/accounts/Data/Write | Tworzenie, aktualizowanie i usuwanie obiektów danych. |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "The Microsoft.Purview data curator can create, read, modify and delete catalog data objects and establish relationships between objects. This role is in preview and subject to change.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8a3c2885-9b38-4fd2-9d99-91af537c1347",
  "name": "8a3c2885-9b38-4fd2-9d99-91af537c1347",
  "permissions": [
    {
      "actions": [
        "Microsoft.Purview/accounts/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Purview/accounts/data/read",
        "Microsoft.Purview/accounts/data/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Purview Data Curator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="purview-data-reader"></a>Czytnik danych kontrolą

Czytnik danych Microsoft. kontrolą może odczytywać obiekty danych wykazu. Ta rola jest w wersji zapoznawczej i może ulec zmianie.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. kontrolą](resource-provider-operations.md#microsoftpurview)/accounts/Read | Odczytaj zasób konta dla dostawcy Microsoft kontrolą. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. kontrolą](resource-provider-operations.md#microsoftpurview)/accounts/Data/Read | Odczytywanie obiektów danych. |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "The Microsoft.Purview data reader can read catalog data objects. This role is in preview and subject to change.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ff100721-1b9d-43d8-af52-42b69c1272db",
  "name": "ff100721-1b9d-43d8-af52-42b69c1272db",
  "permissions": [
    {
      "actions": [
        "Microsoft.Purview/accounts/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Purview/accounts/data/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Purview Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="purview-data-source-administrator"></a>Administrator źródła danych kontrolą

Administrator źródła danych Microsoft. kontrolą może zarządzać źródłami danych i skanami danych. Ta rola jest w wersji zapoznawczej i może ulec zmianie.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. kontrolą](resource-provider-operations.md#microsoftpurview)/accounts/Read | Odczytaj zasób konta dla dostawcy Microsoft kontrolą. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. kontrolą](resource-provider-operations.md#microsoftpurview)/accounts/Scan/Read | Odczytuj źródła danych i skanowania. |
> | [Microsoft. kontrolą](resource-provider-operations.md#microsoftpurview)/accounts/Scan/Write | Tworzenie, aktualizowanie i usuwanie źródeł danych oraz Zarządzanie skanowaniem. |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "The Microsoft.Purview data source administrator can manage data sources and data scans. This role is in preview and subject to change.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/200bba9e-f0c8-430f-892b-6f0794863803",
  "name": "200bba9e-f0c8-430f-892b-6f0794863803",
  "permissions": [
    {
      "actions": [
        "Microsoft.Purview/accounts/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Purview/accounts/scan/read",
        "Microsoft.Purview/accounts/scan/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Purview Data Source Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="schema-registry-contributor-preview"></a>Współautor rejestru schematu (wersja zapoznawcza)

Odczytuj, zapisuj i usuwaj grupy i schematy rejestru schematu.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/Namespaces/schemagroups/* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/Namespaces/schemas/* |  |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read, write, and delete Schema Registry groups and schemas.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5dffeca3-4936-4216-b2bc-10343a5abb25",
  "name": "5dffeca3-4936-4216-b2bc-10343a5abb25",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/namespaces/schemagroups/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/namespaces/schemas/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Schema Registry Contributor (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="schema-registry-reader-preview"></a>Czytnik rejestru schematu (wersja zapoznawcza)

Odczytuj i wyświetlaj listę grup i schematów rejestru schematu.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/Namespaces/schemagroups/Read | Pobierz listę opisów zasobów ze schematu |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/Namespaces/schemas/Read | Pobierz schematy |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and list Schema Registry groups and schemas.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2c56ea50-c6b3-40a6-83c0-9d98858bc7d2",
  "name": "2c56ea50-c6b3-40a6-83c0-9d98858bc7d2",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/namespaces/schemagroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/namespaces/schemas/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Schema Registry Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="blockchain"></a>Łańcuch bloków


### <a name="blockchain-member-node-access-preview"></a>Dostęp do węzła elementu członkowskiego łańcucha bloków (wersja zapoznawcza)

Zezwala na dostęp do łańcucha bloków węzłów członkowskich [Dowiedz się więcej](../blockchain/service/configure-aad.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. łańcucha bloków](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/Read | Pobiera lub Wyświetla istniejące węzły transakcji składowych łańcucha bloków. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. łańcucha bloków](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/Connect/Action | Nawiązuje połączenie z węzłem transakcji elementu członkowskiego łańcucha bloków. |
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

## <a name="ai--machine-learning"></a>Sztuczna inteligencja i uczenie maszynowe


### <a name="cognitive-services-contributor"></a>Współautor Cognitive Services

Umożliwia tworzenie, odczytywanie, aktualizowanie, usuwanie i zarządzanie kluczami Cognitive Services. [Dowiedz się więcej](../cognitive-services/cognitive-services-virtual-networks.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
> | [Microsoft. Features](resource-provider-operations.md#microsoftfeatures)—/Features/Read | Pobiera funkcje subskrypcji. |
> | [Microsoft. Features](resource-provider-operations.md#microsoftfeatures)—/Providers/Features/Read | Pobiera funkcję subskrypcji dla danego dostawcy zasobów. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Tworzy, aktualizuje lub odczytuje ustawienia diagnostyczne dla Analysis Server |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/logDefinitions/Read | Odczytaj definicje dzienników |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/metricdefinitions/Read | Odczytaj definicje metryk |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Read | Odczytaj metryki |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Operations/Read | Pobiera lub wyświetla listę operacji wdrażania. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/Read | Pobierz wyniki operacji subskrypcji. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/Read | Pobiera listę subskrypcji. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/ResourceGroups/Deployments/* |  |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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

### <a name="cognitive-services-custom-vision-contributor"></a>Współautor Custom Vision Cognitive Services

Pełny dostęp do projektu, w tym możliwość wyświetlania, tworzenia, edytowania lub usuwania projektów. [Dowiedz się więcej](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/* |  |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access to the project, including the ability to view, create, edit, or delete projects.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c1ff6cc2-c111-46fe-8896-e0ef812ad9f3",
  "name": "c1ff6cc2-c111-46fe-8896-e0ef812ad9f3",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Custom Vision Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-deployment"></a>Wdrożenie Custom Vision Cognitive Services

Publikowanie, cofanie publikowania i eksportowanie modeli. Wdrożenie może wyświetlić projekt, ale nie można go zaktualizować. [Dowiedz się więcej](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/*/Read |  |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/Predictions/* |  |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/iterations/Publish/* |  |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/iterations/Export/* |  |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/Quicktest/* |  |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/classify/* |  |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/Detect/* |  |
> | **NotDataActions** |  |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/Export/Read | Eksportuje projekt. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Publish, unpublish or export models. Deployment can view the project but can't update.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5c4089e1-6d96-4d2f-b296-c1bc7137275f",
  "name": "5c4089e1-6d96-4d2f-b296-c1bc7137275f",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*/read",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/predictions/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/publish/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/export/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/quicktest/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/classify/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/detect/*"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Deployment",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-labeler"></a>Cognitive Services Custom Vision Labeler

Wyświetlanie, edytowanie obrazów szkoleniowych i tworzenie, Dodawanie, usuwanie lub usuwanie tagów obrazu. Etykiety mogą wyświetlać projekt, ale nie mogą zaktualizować niczego innego niż obrazy szkoleniowe i Tagi. [Dowiedz się więcej](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/*/Read |  |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/Predictions/Query/Action | Pobierz obrazy, które zostały wysłane do punktu końcowego przewidywania. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/images/* |  |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/Tags/* |  |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/images/Suggested/* |  |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/tagsandregions/Suggestions/Action | Ten interfejs API będzie pobierać sugerowane Tagi i regiony dla tablicy/partii nieoznakowanych obrazów, a także pewności dla tagów. Zwraca pustą tablicę, jeśli nie znaleziono żadnych tagów. |
> | **NotDataActions** |  |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/Export/Read | Eksportuje projekt. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View, edit training images and create, add, remove, or delete the image tags. Labelers can view the project but can't update anything other than training images and tags.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/88424f51-ebe7-446f-bc41-7fa16989e96c",
  "name": "88424f51-ebe7-446f-bc41-7fa16989e96c",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*/read",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/predictions/query/action",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/images/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/tags/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/images/suggested/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/tagsandregions/suggestions/action"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Labeler",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-reader"></a>Cognitive Services Custom Vision Reader

Akcje tylko do odczytu w projekcie. Czytelnicy nie mogą utworzyć ani zaktualizować projektu. [Dowiedz się więcej](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/*/Read |  |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/Predictions/Query/Action | Pobierz obrazy, które zostały wysłane do punktu końcowego przewidywania. |
> | **NotDataActions** |  |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/Export/Read | Eksportuje projekt. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only actions in the project. Readers can't create or update the project.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/93586559-c37d-4a6b-ba08-b9f0940c2d73",
  "name": "93586559-c37d-4a6b-ba08-b9f0940c2d73",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*/read",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/predictions/query/action"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-trainer"></a>Cognitive Services Custom Vision Trainer

Wyświetlaj, Edytuj projekty i uczenie modeli, w tym możliwość publikowania, cofania publikacji i eksportowania modeli. Instruktorzy nie mogą tworzyć ani usuwać projektu. [Dowiedz się więcej](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/* |  |
> | **NotDataActions** |  |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/Action | Utwórz projekt. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/Delete | Usuń określony projekt. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/import/Action | Importuje projekt. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/Export/Read | Eksportuje projekt. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View, edit projects and train the models, including the ability to publish, unpublish, export the models. Trainers can't create or delete the project.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0a5ae4ab-0d65-4eeb-be61-29fc9b54394b",
  "name": "0a5ae4ab-0d65-4eeb-be61-29fc9b54394b",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/action",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/delete",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/import/action",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Trainer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-data-reader-preview"></a>Czytnik danych Cognitive Services (wersja zapoznawcza)

Umożliwia odczytywanie danych Cognitive Services.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
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

### <a name="cognitive-services-metrics-advisor-administrator"></a>Cognitive Services administrator doradcy metryk

Pełny dostęp do projektu, w tym konfiguracja poziomu systemu. [Dowiedz się więcej](../cognitive-services/metrics-advisor/how-tos/alerts.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/MetricsAdvisor/* |  |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access to the project, including the system level configuration.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cb43c632-a144-4ec5-977c-e80c4affc34a",
  "name": "cb43c632-a144-4ec5-977c-e80c4affc34a",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/MetricsAdvisor/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Metrics Advisor Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-qna-maker-editor"></a>Edytor QnA Maker Cognitive Services

Umożliwia tworzenie, edytowanie, importowanie i eksportowanie KB. Nie można opublikować ani usunąć bazy wiedzy. [Dowiedz się więcej](../cognitive-services/qnamaker/reference-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/roleAssignments/Read | Pobierz informacje o przypisaniu roli. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/roleDefinitions/Read | Pobierz informacje o definicji roli. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/Read | Pobiera listę bazy wiedzy lub szczegóły konkretnej bazy wiedzy. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/Download/Read | Pobierz baza wiedzy. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/Create/Write | Operacja asynchroniczna do utworzenia nowej bazy wiedzy. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/Write | Operacja asynchroniczna do modyfikowania bazy wiedzy lub zastępowania zawartości bazy wiedzy. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/generateanswer/Action | GenerateAnswer wywołanie zapytania do bazy wiedzy. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/Train/Action | Połącz połączenie, aby dodać sugestie do bazy wiedzy. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/Alterations/Read | Pobierz zmiany z środowiska uruchomieniowego. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/Alterations/Write | Zastąp dane zmian. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointkeys/Read | Pobiera klucze punktów końcowych dla punktu końcowego |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointkeys/refreshkeys/Action | Ponowne generowanie klucza punktu końcowego. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointsettings/Read | Pobiera ustawienia punktu końcowego dla punktu końcowego |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointsettings/Write | Aktualizowanie punktu końcowego seettings dla punktu końcowego. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/Operations/Read | Pobiera szczegóły określonej długotrwałej operacji. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/Read | Pobiera listę bazy wiedzy lub szczegóły konkretnej bazy wiedzy. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/Download/Read | Pobierz baza wiedzy. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/Create/Write | Operacja asynchroniczna do utworzenia nowej bazy wiedzy. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/Write | Operacja asynchroniczna do modyfikowania bazy wiedzy lub zastępowania zawartości bazy wiedzy. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/generateanswer/Action | GenerateAnswer wywołanie zapytania do bazy wiedzy. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/Train/Action | Połącz połączenie, aby dodać sugestie do bazy wiedzy. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/Alterations/Read | Pobierz zmiany z środowiska uruchomieniowego. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/Alterations/Write | Zastąp dane zmian. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointkeys/Read | Pobiera klucze punktów końcowych dla punktu końcowego |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointkeys/refreshkeys/Action | Ponowne generowanie klucza punktu końcowego. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointsettings/Read | Pobiera ustawienia punktu końcowego dla punktu końcowego |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointsettings/Write | Aktualizowanie punktu końcowego seettings dla punktu końcowego. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/Operations/Read | Pobiera szczegóły określonej długotrwałej operacji. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/Read | Pobiera listę bazy wiedzy lub szczegóły konkretnej bazy wiedzy. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/Download/Read | Pobierz baza wiedzy. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/Create/Write | Operacja asynchroniczna do utworzenia nowej bazy wiedzy. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/Write | Operacja asynchroniczna do modyfikowania bazy wiedzy lub zastępowania zawartości bazy wiedzy. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/generateanswer/Action | GenerateAnswer wywołanie zapytania do bazy wiedzy. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/Train/Action | Połącz połączenie, aby dodać sugestie do bazy wiedzy. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/Alterations/Read | Pobierz zmiany z środowiska uruchomieniowego. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/Alterations/Write | Zastąp dane zmian. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointkeys/Read | Pobiera klucze punktów końcowych dla punktu końcowego |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointkeys/refreshkeys/Action | Ponowne generowanie klucza punktu końcowego. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointsettings/Read | Pobiera ustawienia punktu końcowego dla punktu końcowego |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointsettings/Write | Aktualizowanie punktu końcowego seettings dla punktu końcowego. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/Operations/Read | Pobiera szczegóły określonej długotrwałej operacji. |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Let's you create, edit, import and export a KB. You cannot publish or delete a KB.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f4cc2bf9-21be-47a1-bdf1-5c5804381025",
  "name": "f4cc2bf9-21be-47a1-bdf1-5c5804381025",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleDefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/create/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/train/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/alterations/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointkeys/refreshkeys/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointsettings/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/operations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/create/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/train/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/alterations/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointkeys/refreshkeys/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointsettings/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/operations/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/create/write",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/write",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/train/action",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/alterations/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/alterations/write",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointkeys/refreshkeys/action",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointsettings/write",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/operations/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services QnA Maker Editor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-qna-maker-reader"></a>Cognitive Services QnA Maker Reader

Umożliwia odczytanie i przetestowanie tylko KB. [Dowiedz się więcej](../cognitive-services/qnamaker/reference-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/roleAssignments/Read | Pobierz informacje o przypisaniu roli. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/roleDefinitions/Read | Pobierz informacje o definicji roli. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/Read | Pobiera listę bazy wiedzy lub szczegóły konkretnej bazy wiedzy. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/Download/Read | Pobierz baza wiedzy. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/generateanswer/Action | GenerateAnswer wywołanie zapytania do bazy wiedzy. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/Alterations/Read | Pobierz zmiany z środowiska uruchomieniowego. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointkeys/Read | Pobiera klucze punktów końcowych dla punktu końcowego |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointsettings/Read | Pobiera ustawienia punktu końcowego dla punktu końcowego |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/Read | Pobiera listę bazy wiedzy lub szczegóły konkretnej bazy wiedzy. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/Download/Read | Pobierz baza wiedzy. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/generateanswer/Action | GenerateAnswer wywołanie zapytania do bazy wiedzy. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/Alterations/Read | Pobierz zmiany z środowiska uruchomieniowego. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointkeys/Read | Pobiera klucze punktów końcowych dla punktu końcowego |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointsettings/Read | Pobiera ustawienia punktu końcowego dla punktu końcowego |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/Read | Pobiera listę bazy wiedzy lub szczegóły konkretnej bazy wiedzy. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/Download/Read | Pobierz baza wiedzy. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/generateanswer/Action | GenerateAnswer wywołanie zapytania do bazy wiedzy. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/Alterations/Read | Pobierz zmiany z środowiska uruchomieniowego. |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointkeys/Read | Pobiera klucze punktów końcowych dla punktu końcowego |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointsettings/Read | Pobiera ustawienia punktu końcowego dla punktu końcowego |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Let's you read and test a KB only.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/466ccd10-b268-4a11-b098-b4849f024126",
  "name": "466ccd10-b268-4a11-b098-b4849f024126",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleDefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/alterations/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointsettings/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services QnA Maker Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-user"></a>Cognitive Services użytkownika

Umożliwia odczytywanie i wyświetlanie listy kluczy Cognitive Services. [Dowiedz się więcej](../cognitive-services/authentication.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/Read |  |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/ListKeys/Action | Klucze list |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/Read | Przeczytaj klasyczny alert dotyczący metryki |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/Read | Odczytaj ustawienie diagnostyczne zasobu |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/logDefinitions/Read | Odczytaj definicje dzienników |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/metricdefinitions/Read | Odczytaj definicje metryk |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Read | Odczytaj metryki |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Operations/Read | Pobiera lub wyświetla listę operacji wdrażania. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/Read | Pobierz wyniki operacji subskrypcji. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/Read | Pobiera listę subskrypcji. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
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

## <a name="internet-of-things"></a>Internet rzeczy


### <a name="device-update-administrator"></a>Administrator aktualizacji urządzenia

Zapewnia pełen dostęp do operacji zarządzania i zawartości, [Dowiedz się więcej](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/Instances/Updates/Read | Wykonuje operację odczytu związaną z aktualizacjami |
> | [Microsoft. DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/Instances/Updates/Write | Wykonuje operację zapisu powiązaną z aktualizacjami |
> | [Microsoft. DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/Instances/Updates/Delete | Wykonuje operację usuwania powiązaną z aktualizacjami |
> | [Microsoft. DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/Instances/Management/Read | Wykonuje operację odczytu związaną z zarządzaniem |
> | [Microsoft. DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/Instances/Management/Write | Wykonuje operację zapisu powiązaną z zarządzaniem |
> | [Microsoft. DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/Instances/Management/Delete | Wykonuje operację usuwania powiązaną z zarządzaniem |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you full access to management and content operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/02ca0879-e8e4-47a5-a61e-5c618b76e64a",
  "name": "02ca0879-e8e4-47a5-a61e-5c618b76e64a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/updates/read",
        "Microsoft.DeviceUpdate/accounts/instances/updates/write",
        "Microsoft.DeviceUpdate/accounts/instances/updates/delete",
        "Microsoft.DeviceUpdate/accounts/instances/management/read",
        "Microsoft.DeviceUpdate/accounts/instances/management/write",
        "Microsoft.DeviceUpdate/accounts/instances/management/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-content-administrator"></a>Administrator zawartości urządzenia z aktualizacją

Zapewnia pełny dostęp do operacji dotyczących zawartości [Dowiedz się więcej](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/Instances/Updates/Read | Wykonuje operację odczytu związaną z aktualizacjami |
> | [Microsoft. DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/Instances/Updates/Write | Wykonuje operację zapisu powiązaną z aktualizacjami |
> | [Microsoft. DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/Instances/Updates/Delete | Wykonuje operację usuwania powiązaną z aktualizacjami |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you full access to content operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0378884a-3af5-44ab-8323-f5b22f9f3c98",
  "name": "0378884a-3af5-44ab-8323-f5b22f9f3c98",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/updates/read",
        "Microsoft.DeviceUpdate/accounts/instances/updates/write",
        "Microsoft.DeviceUpdate/accounts/instances/updates/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Content Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-content-reader"></a>Czytnik zawartości urządzenia aktualizacji

Zapewnia dostęp do operacji związanych z zawartością, ale nie pozwala na [wprowadzanie zmian](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/Instances/Updates/Read | Wykonuje operację odczytu związaną z aktualizacjami |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you read access to content operations, but does not allow making changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d1ee9a80-8b14-47f0-bdc2-f4a351625a7b",
  "name": "d1ee9a80-8b14-47f0-bdc2-f4a351625a7b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/updates/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Content Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-deployments-administrator"></a>Administrator wdrożeń aktualizacji urządzeń

Zapewnia pełen dostęp do operacji zarządzania, [Dowiedz się więcej](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/Instances/Management/Read | Wykonuje operację odczytu związaną z zarządzaniem |
> | [Microsoft. DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/Instances/Management/Write | Wykonuje operację zapisu powiązaną z zarządzaniem |
> | [Microsoft. DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/Instances/Management/Delete | Wykonuje operację usuwania powiązaną z zarządzaniem |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you full access to management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e4237640-0e3d-4a46-8fda-70bc94856432",
  "name": "e4237640-0e3d-4a46-8fda-70bc94856432",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/management/read",
        "Microsoft.DeviceUpdate/accounts/instances/management/write",
        "Microsoft.DeviceUpdate/accounts/instances/management/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Deployments Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-deployments-reader"></a>Czytnik wdrożeń aktualizacji urządzeń

Zapewnia dostęp do odczytu do operacji zarządzania, ale nie pozwala na [wprowadzanie zmian](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/Instances/Management/Read | Wykonuje operację odczytu związaną z zarządzaniem |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you read access to management operations, but does not allow making changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/49e2f5d2-7741-4835-8efa-19e1fe35e47f",
  "name": "49e2f5d2-7741-4835-8efa-19e1fe35e47f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/management/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Deployments Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-reader"></a>Czytnik aktualizacji urządzenia

Umożliwia dostęp do odczytu do operacji zarządzania i zawartości, ale nie pozwala na [wprowadzanie zmian.](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/Instances/Updates/Read | Wykonuje operację odczytu związaną z aktualizacjami |
> | [Microsoft. DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/Instances/Management/Read | Wykonuje operację odczytu związaną z zarządzaniem |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you read access to management and content operations, but does not allow making changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e9dba6fb-3d52-4cf0-bce3-f06ce71b9e0f",
  "name": "e9dba6fb-3d52-4cf0-bce3-f06ce71b9e0f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/updates/read",
        "Microsoft.DeviceUpdate/accounts/instances/management/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="mixed-reality"></a>Rzeczywistość mieszana


### <a name="remote-rendering-administrator"></a>Administrator zdalnego renderowania

Zapewnia użytkownikowi konwersję, możliwość zarządzania sesją, renderowania i diagnostyki na potrzeby renderowania zdalnego na platformie Azure [więcej informacji](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Convert/Action | Rozpocznij konwersję zasobów |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Convert/Read | Pobierz właściwości konwersji zasobów |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Convert/Delete | Zatrzymaj konwersję zasobów |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/Read | Pobierz właściwości sesji |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/Action | Rozpocznij sesje |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/Delete | Zatrzymaj sesje |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Render/Read | Nawiązywanie połączenia z sesją |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Diagnostic/Read | Połącz z inspektorem renderowania zdalnego |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides user with conversion, manage session, rendering and diagnostics capabilities for Azure Remote Rendering",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3df8b902-2a6f-47c7-8cc5-360e9b272a7e",
  "name": "3df8b902-2a6f-47c7-8cc5-360e9b272a7e",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/render/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/diagnostic/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Remote Rendering Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="remote-rendering-client"></a>Klient renderowania zdalnego

Umożliwia użytkownikowi zarządzanie sesjami, renderowaniem i diagnostyką w przypadku renderowania zdalnego na platformie Azure. [Dowiedz się więcej](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/Read | Pobierz właściwości sesji |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/Action | Rozpocznij sesje |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/Delete | Zatrzymaj sesje |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Render/Read | Nawiązywanie połączenia z sesją |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Diagnostic/Read | Połącz z inspektorem renderowania zdalnego |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides user with manage session, rendering and diagnostics capabilities for Azure Remote Rendering.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d39065c4-c120-43c9-ab0a-63eed9795f0a",
  "name": "d39065c4-c120-43c9-ab0a-63eed9795f0a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/render/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/diagnostic/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Remote Rendering Client",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-contributor"></a>Współautor konta kotwic przestrzennych

Pozwala zarządzać zakotwiczeniami przestrzennymi na Twoim koncie, ale nie usuwać ich [więcej](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Create/Action | Utwórz kotwice przestrzenne |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Discovery/Read | Odkryj kotwice w pobliżu |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Properties/Read | Pobierz właściwości kotwic przestrzennych |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Query/Read | Lokalizowanie kotwic przestrzennych |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/Read | Przesyłanie danych diagnostycznych w celu poprawy jakości usługi zakotwiczeń przestrzennych platformy Azure |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Write | Aktualizuj właściwości kotwic przestrzennych |
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

Pozwala zarządzać zakotwiczeniami przestrzennymi na Twoim koncie, w tym ich usuwanie [dowiedzieć się więcej](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Create/Action | Utwórz kotwice przestrzenne |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Delete | Usuń kotwice przestrzenne |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Discovery/Read | Odkryj kotwice w pobliżu |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Properties/Read | Pobierz właściwości kotwic przestrzennych |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Query/Read | Lokalizowanie kotwic przestrzennych |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/Read | Przesyłanie danych diagnostycznych w celu poprawy jakości usługi zakotwiczeń przestrzennych platformy Azure |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Write | Aktualizuj właściwości kotwic przestrzennych |
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

Pozwala lokalizować i odczytywać właściwości kotwic przestrzennych na koncie [dowiedzieć się więcej](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Discovery/Read | Odkryj kotwice w pobliżu |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Properties/Read | Pobierz właściwości kotwic przestrzennych |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Query/Read | Lokalizowanie kotwic przestrzennych |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/Read | Przesyłanie danych diagnostycznych w celu poprawy jakości usługi zakotwiczeń przestrzennych platformy Azure |
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

Może zarządzać usługą i interfejsami API [dowiedzieć się więcej](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/* | Utwórz usługę API Management i zarządzaj nią |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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

Może zarządzać usługą, ale nie [dowiedzieć się więcej](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/*/Read | Odczytaj API Management wystąpienia usługi |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/Backup/Action | Usługa tworzenia kopii zapasowych API Management do określonego kontenera na koncie magazynu podanego przez użytkownika |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/Delete | Usuń wystąpienie usługi API Management |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/managedeployments/Action | Zmień jednostkę SKU/jednostki, Dodaj/Usuń wdrożenia regionalne usługi API Management |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/Read | Odczytaj metadane dla wystąpienia usługi API Management |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/Restore/Action | Przywracanie usługi API Management z określonego kontenera na koncie magazynu podanego przez użytkownika |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/updatecertificate/Action | Przekazywanie certyfikatu TLS/SSL dla usługi API Management |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/updatehostname/Action | Instalowanie, aktualizowanie lub usuwanie niestandardowych nazw domen dla usługi API Management |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/Write | Utwórz lub zaktualizuj wystąpienie usługi API Management |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/users/Keys/Read | Pobierz klucze skojarzone z użytkownikiem |
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

Dostęp tylko do odczytu do usługi i interfejsów API [Dowiedz się więcej](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/*/Read | Odczytaj API Management wystąpienia usługi |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/Read | Odczytaj metadane dla wystąpienia usługi API Management |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/users/Keys/Read | Pobierz klucze skojarzone z użytkownikiem |
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

Umożliwia pełny dostęp do danych konfiguracji aplikacji. [Dowiedz się więcej](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/Read |  |
> | [Microsoft. AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/Write |  |
> | [Microsoft. AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/Delete |  |
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

Zezwala na dostęp do odczytu do danych konfiguracji aplikacji. [Dowiedz się więcej](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/Read |  |
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

Umożliwia pełny dostęp do zasobów Azure Service Bus. [Dowiedz się więcej](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/* |  |
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

Umożliwia uzyskanie dostępu do zasobów Azure Service Bus. [Dowiedz się więcej](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/Queues/Read |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/Read |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/subscriptions/Read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/Receive/Action |  |
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

Zezwala na dostęp do Azure Service Bus zasobów. [Dowiedz się więcej](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/Queues/Read |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/Read |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/subscriptions/Read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/Send/Action |  |
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
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. AzureStack](resource-provider-operations.md#microsoftazurestack)/edgeSubscriptions/Read | Pobierz właściwości subskrypcji Azure Stack Edge |
> | [Microsoft. AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/Products/*/Action |  |
> | [Microsoft. AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/Products/Read | Pobiera właściwości produktu Azure Stack Marketplace |
> | [Microsoft. AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/Read | Pobiera właściwości rejestracji Azure Stack |
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
        "Microsoft.AzureStack/edgeSubscriptions/read",
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

### <a name="eventgrid-contributor"></a>Współautor EventGrid

Umożliwia zarządzanie operacjami EventGrid.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/* | Tworzenie zasobów Event Grid i zarządzanie nimi |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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
  "description": "Lets you manage EventGrid operations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1e241071-0855-49ea-94dc-649edcd759de",
  "name": "1e241071-0855-49ea-94dc-649edcd759de",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/*",
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
  "roleName": "EventGrid Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-contributor"></a>Współautor EventGrid EventSubscription

Umożliwia zarządzanie operacjami subskrypcji zdarzeń EventGrid. [Dowiedz się więcej](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/* | Tworzenie regionalnych subskrypcji zdarzeń i zarządzanie nimi |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/Read | Wyświetl listę globalnych subskrypcji zdarzeń według typu tematu |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/Locations/eventSubscriptions/Read | Utwórz listę regionalnych subskrypcji zdarzeń |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/Locations/topicTypes/eventSubscriptions/Read | Utwórz listę regionalnych subskrypcji zdarzeń według |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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

Umożliwia odczytanie subskrypcji zdarzeń EventGrid. [Dowiedz się więcej](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/Read | Odczytaj eventSubscription |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/Read | Wyświetl listę globalnych subskrypcji zdarzeń według typu tematu |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/Locations/eventSubscriptions/Read | Utwórz listę regionalnych subskrypcji zdarzeń |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/Locations/topicTypes/eventSubscriptions/Read | Utwórz listę regionalnych subskrypcji zdarzeń według |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
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

### <a name="fhir-data-contributor"></a>Współautor danych FHIR

Rola pozwala użytkownikowi lub głównemu pełny dostęp do danych FHIR [dowiedzieć się więcej](../healthcare-apis/fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | Microsoft. HealthcareApis/Services/fhir/Resources/* |  |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal full access to FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5a1fc7df-4bf1-4951-a576-89034ee01acd",
  "name": "5a1fc7df-4bf1-4951-a576-89034ee01acd",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-exporter"></a>FHIR — eksport danych

Rola umożliwia użytkownikowi lub podmiotowi zabezpieczeń odczytywanie i eksportowanie danych FHIR [Dowiedz się więcej](../healthcare-apis/fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | Microsoft. HealthcareApis/Services/fhir/Resources/Read | Odczytuj zasoby FHIR (w tym wyszukiwania i historię wersji).  |
> | Microsoft. HealthcareApis/Services/fhir//zasoby/eksport/akcja | Operacja eksportowania ($export). |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read and export FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3db33094-8700-4567-8da5-1501d4e7e843",
  "name": "3db33094-8700-4567-8da5-1501d4e7e843",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/read",
        "Microsoft.HealthcareApis/services/fhir/resources/export/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Exporter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-reader"></a>Czytnik danych FHIR

Rola umożliwia użytkownikowi lub podmiotowi zabezpieczeń odczytywanie danych FHIR [Dowiedz się więcej](../healthcare-apis/fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | Microsoft. HealthcareApis/Services/fhir/Resources/Read | Odczytuj zasoby FHIR (w tym wyszukiwania i historię wersji).  |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4c8d0bbc-75d3-4935-991f-5f3c56d81508",
  "name": "4c8d0bbc-75d3-4935-991f-5f3c56d81508",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-writer"></a>Moduł zapisywania danych FHIR

Rola umożliwia użytkownikowi lub podmiotowi zabezpieczeń odczytywanie i zapisywanie danych FHIR [Dowiedz się więcej](../healthcare-apis/fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | Microsoft. HealthcareApis/Services/fhir/Resources/* |  |
> | **NotDataActions** |  |
> | Microsoft. HealthcareApis/Services/fhir/Resources/hardDelete/Action | Twarde usuwanie (w tym historia wersji). |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read and write FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3f88fce4-5892-4214-ae73-ba5294559913",
  "name": "3f88fce4-5892-4214-ae73-ba5294559913",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/*"
      ],
      "notDataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/hardDelete/action"
      ]
    }
  ],
  "roleName": "FHIR Data Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="integration-service-environment-contributor"></a>Współautor środowisko usługi integracji

Umożliwia zarządzanie środowiskami usługi integracji, ale nie umożliwia uzyskiwania do nich dostępu. [Dowiedz się więcej](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/* |  |
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
  "description": "Lets you manage integration service environments, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a41e2c5b-bd99-4a07-88f4-9bf657a760b8",
  "name": "a41e2c5b-bd99-4a07-88f4-9bf657a760b8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*",
        "Microsoft.Logic/integrationServiceEnvironments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Integration Service Environment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="integration-service-environment-developer"></a>środowisko usługi integracji deweloper

Umożliwia deweloperom tworzenie i aktualizowanie przepływów pracy, kont integracji i połączeń interfejsów API w środowiskach usługi integracji. [Dowiedz się więcej](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/Read | Odczytuje środowisko usługi integracji. |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/*/Join/Action |  |
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
  "description": "Allows developers to create and update workflows, integration accounts and API connections in integration service environments.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7aa55d3-1abb-444a-a5ca-5e51e485d6ec",
  "name": "c7aa55d3-1abb-444a-a5ca-5e51e485d6ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*",
        "Microsoft.Logic/integrationServiceEnvironments/read",
        "Microsoft.Logic/integrationServiceEnvironments/*/join/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Integration Service Environment Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="intelligent-systems-account-contributor"></a>Współautor konta systemów inteligentnych

Umożliwia zarządzanie kontami inteligentnych systemów, ale nie umożliwia uzyskiwania do nich dostępu.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | Microsoft. IntelligentSystems/accounts/* | Tworzenie kont inteligentnych systemów i zarządzanie nimi |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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

Umożliwia zarządzanie aplikacjami logiki, ale nie umożliwia zmiany dostępu do nich. [Dowiedz się więcej](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/Action | Wyświetla listę kluczy dostępu dla kont magazynu. |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/Read | Zwróć konto magazynu z danym kontem. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Tworzy, aktualizuje lub odczytuje ustawienia diagnostyczne dla Analysis Server |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/logdefinitions/* | To uprawnienie jest niezbędne dla użytkowników, którzy potrzebują dostępu do dzienników aktywności za pośrednictwem portalu. Wyświetl listę kategorii dziennika w dzienniku aktywności. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/* | Odczytaj definicje metryk (lista dostępnych typów metryk dla zasobu). |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/* | Zarządza zasobami Logic Apps. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/Read | Pobierz wyniki operacji subskrypcji. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/ListKeys/Action | Zwraca klucze dostępu dla określonego konta magazynu. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/Read | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/connectionGateways/* | Utwórz bramę połączenia i zarządzaj nią. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/Connections/* | Utwórz połączenie i Zarządzaj nim. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/customApis/* | Tworzy niestandardowy interfejs API i zarządza nim. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/serverFarms/Join/Action | Sprzęga plan App Service |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/serverFarms/Read | Pobierz właściwości planu App Service |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/sites/Functions/listSecrets/Action | Utwórz listę wpisów tajnych funkcji. |
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

Umożliwia odczytywanie, Włączanie i wyłączanie aplikacji logiki, ale nie ich edytowanie ani aktualizowanie. [Dowiedz się więcej](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/*/Read | Czytaj reguły alertów szczegółowych informacji |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/*/Read |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/*/Read | Pobiera ustawienia diagnostyczne dla Logic Apps |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/*/Read | Pobiera dostępne metryki dla Logic Apps. |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/*/Read | Odczytuje Logic Apps zasoby. |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/Workflows/Disable/Action | Wyłącza przepływ pracy. |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/Workflows/Enable/Action | Włącza przepływ pracy. |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/Workflows/Validate/Action | Sprawdza poprawność przepływu pracy. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Operations/Read | Pobiera lub wyświetla listę operacji wdrażania. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/Read | Pobierz wyniki operacji subskrypcji. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/connectionGateways/*/Read | Odczytaj bramy połączeń. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/Connections/*/Read | Odczyt połączeń. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/customApis/*/Read | Odczytaj niestandardowy interfejs API. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/serverFarms/Read | Pobierz właściwości planu App Service |
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

Tworzenie, odczytywanie, aktualizowanie i usuwanie tożsamości przypisanej przez użytkownika [Dowiedz się więcej](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/Read | Pobiera istniejącą tożsamość przypisanego użytkownika |
> | [Microsoft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/Write | Tworzy nową tożsamość przypisanego użytkownika lub aktualizuje Tagi skojarzone z istniejącą tożsamością przypisanego użytkownika |
> | [Microsoft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/Delete | Usuwa istniejącą tożsamość przypisanego użytkownika |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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

Odczytaj i przypisz tożsamość przypisanych do użytkownika [Dowiedz się więcej](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/Read |  |
> | [Microsoft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/Assign/Action |  |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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


### <a name="attestation-contributor"></a>Współautor zaświadczania

Może odczytać lub usunąć wystąpienie dostawcy zaświadczania [więcej informacji](../attestation/quickstart-powershell.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | Microsoft. zaświadczanie/attestationProviders/zaświadczanie/odczytanie |  |
> | Microsoft. zaświadczanie/attestationProviders/zaświadczanie/zapis |  |
> | Microsoft. zaświadczanie/attestationProviders/zaświadczanie/usunięcie |  |
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
  "description": "Can read write or delete the attestation provider instance",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/bbf86eb8-f7b4-4cce-96e4-18cddf81d86e",
  "name": "bbf86eb8-f7b4-4cce-96e4-18cddf81d86e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Attestation/attestationProviders/attestation/read",
        "Microsoft.Attestation/attestationProviders/attestation/write",
        "Microsoft.Attestation/attestationProviders/attestation/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Attestation Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="attestation-reader"></a>Czytelnik zaświadczania

Może odczytać właściwości dostawcy zaświadczania [więcej informacji](../attestation/troubleshoot-guide.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | Microsoft. zaświadczanie/attestationProviders/zaświadczanie/odczytanie |  |
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
  "description": "Can read the attestation provider properties",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fd1bd22b-8476-40bc-a0bc-69b95687b9f3",
  "name": "fd1bd22b-8476-40bc-a0bc-69b95687b9f3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Attestation/attestationProviders/attestation/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Attestation Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-contributor"></a>Współautor usługi Azure Sentinel

Współautor Azure wskaźnikowego [Dowiedz się więcej](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/* |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Analytics/Query/Action | Wyszukaj przy użyciu nowego aparatu. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/*/Read | Wyświetlanie danych usługi log Analytics |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/savedSearches/* |  |
> | [Microsoft. OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/Solutions/Read | Pobierz wyjście z rozwiązania OMS |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Query/Read | Uruchamianie zapytań dotyczących danych w obszarze roboczym |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Query/*/Read |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/DataSources/Read | Pobierz źródła danych w obszarze roboczym. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/Read | Odczytaj prywatny skoroszyt |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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
        "Microsoft.Insights/myworkbooks/read",
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

### <a name="azure-sentinel-reader"></a>Czytelnik usługi Azure Sentinel

Czytnik wskaźnikowy platformy Azure [Dowiedz się więcej](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/Read |  |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/Action | Sprawdź autoryzację i licencję użytkownika |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/Indicators/Query/Action | Zbadaj wskaźniki analizy zagrożeń |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/queryIndicators/Action | Zbadaj wskaźniki analizy zagrożeń |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Analytics/Query/Action | Wyszukaj przy użyciu nowego aparatu. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/*/Read | Wyświetlanie danych usługi log Analytics |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/LinkedServices/Read | Pobierz połączone usługi w podanym obszarze roboczym. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/savedSearches/Read | Pobiera zapisane zapytanie wyszukiwania |
> | [Microsoft. OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/Solutions/Read | Pobierz wyjście z rozwiązania OMS |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Query/Read | Uruchamianie zapytań dotyczących danych w obszarze roboczym |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Query/*/Read |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/DataSources/Read | Pobierz źródła danych w obszarze roboczym. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/Read | Odczytaj skoroszyt |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/Read | Odczytaj prywatny skoroszyt |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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
        "Microsoft.SecurityInsights/threatIntelligence/indicators/query/action",
        "Microsoft.SecurityInsights/threatIntelligence/queryIndicators/action",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/LinkedServices/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Insights/myworkbooks/read",
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

Obiekt odpowiadający usługi Azure wskaźnikowej [Dowiedz się więcej](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/Read |  |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/Action | Sprawdź autoryzację i licencję użytkownika |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/automationRules/* |  |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/Cases/* |  |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/incidents/* |  |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/Indicators/appendTags/Action | Dołącz Tagi do wskaźnika analizy zagrożeń |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/Indicators/Query/Action | Zbadaj wskaźniki analizy zagrożeń |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/bulkTag/Action | Analiza zagrożeń tagów zbiorczych |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/Indicators/appendTags/Action | Dołącz Tagi do wskaźnika analizy zagrożeń |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/Indicators/replaceTags/Action | Zamień Tagi wskaźnika analizy zagrożeń |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/queryIndicators/Action | Zbadaj wskaźniki analizy zagrożeń |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Analytics/Query/Action | Wyszukaj przy użyciu nowego aparatu. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/*/Read | Wyświetlanie danych usługi log Analytics |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/DataSources/Read | Pobierz źródła danych w obszarze roboczym. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/savedSearches/Read | Pobiera zapisane zapytanie wyszukiwania |
> | [Microsoft. OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/Solutions/Read | Pobierz wyjście z rozwiązania OMS |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Query/Read | Uruchamianie zapytań dotyczących danych w obszarze roboczym |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Query/*/Read |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/DataSources/Read | Pobierz źródła danych w obszarze roboczym. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/Read | Odczytaj skoroszyt |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/Read | Odczytaj prywatny skoroszyt |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/Cases/*/Delete |  |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/incidents/*/Delete |  |
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
        "Microsoft.SecurityInsights/automationRules/*",
        "Microsoft.SecurityInsights/cases/*",
        "Microsoft.SecurityInsights/incidents/*",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/appendTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/query/action",
        "Microsoft.SecurityInsights/threatIntelligence/bulkTag/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/appendTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/replaceTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/queryIndicators/action",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Insights/myworkbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.SecurityInsights/cases/*/Delete",
        "Microsoft.SecurityInsights/incidents/*/Delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Responder",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-administrator"></a>Key Vault administrator

Wykonaj wszystkie operacje płaszczyzny danych w magazynie kluczy i wszystkie obiekty w nim, w tym certyfikaty, klucze i wpisy tajne. Nie można zarządzać zasobami magazynu kluczy ani zarządzać przypisaniami ról. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "kontrola dostępu oparta na rolach" platformy Azure.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | /CheckNameAvailability/Read [magazynu kluczy firmy Microsoft](resource-provider-operations.md#microsoftkeyvault) | Sprawdza, czy nazwa magazynu kluczy jest prawidłowa i czy nie jest używana |
> | /DeletedVaults/Read [magazynu kluczy firmy Microsoft](resource-provider-operations.md#microsoftkeyvault) | Wyświetl właściwości usuniętych nietrwałych magazynów kluczy |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)/Locations/— Magazyn kluczy */Read |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)/Vaults/— Magazyn kluczy */Read |  |
> | /Operations/Read [magazynu kluczy firmy Microsoft](resource-provider-operations.md#microsoftkeyvault) | Wyświetla listę operacji dostępnych w ramach dostawcy zasobów Microsoft. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)/Vaults/— Magazyn kluczy * |  |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform all data plane operations on a key vault and all objects in it, including certificates, keys, and secrets. Cannot manage key vault resources or manage role assignments. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00482a5a-887f-4fb3-b363-3b7fe8e74483",
  "name": "00482a5a-887f-4fb3-b363-3b7fe8e74483",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-certificates-officer"></a>Oficer certyfikatów Key Vault

Wykonaj dowolną akcję dotyczącą certyfikatów magazynu kluczy, z wyjątkiem uprawnień do zarządzania. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "kontrola dostępu oparta na rolach" platformy Azure.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | /CheckNameAvailability/Read [magazynu kluczy firmy Microsoft](resource-provider-operations.md#microsoftkeyvault) | Sprawdza, czy nazwa magazynu kluczy jest prawidłowa i czy nie jest używana |
> | /DeletedVaults/Read [magazynu kluczy firmy Microsoft](resource-provider-operations.md#microsoftkeyvault) | Wyświetl właściwości usuniętych nietrwałych magazynów kluczy |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)/Locations/— Magazyn kluczy */Read |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)/Vaults/— Magazyn kluczy */Read |  |
> | /Operations/Read [magazynu kluczy firmy Microsoft](resource-provider-operations.md#microsoftkeyvault) | Wyświetla listę operacji dostępnych w ramach dostawcy zasobów Microsoft. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)/Vaults/certificatecas/— Magazyn kluczy * |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)/Vaults/Certificates/— Magazyn kluczy * |  |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the certificates of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4417e6f-fecd-4de8-b567-7b0420556985",
  "name": "a4417e6f-fecd-4de8-b567-7b0420556985",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/certificatecas/*",
        "Microsoft.KeyVault/vaults/certificates/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Certificates Officer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-contributor"></a>Współautor Key Vault

Zarządzanie magazynami kluczy, ale nie umożliwia przypisywania ról w usłudze Azure RBAC i nie pozwala na dostęp do wpisów tajnych, kluczy lub certyfikatów. [Dowiedz się więcej](../key-vault/general/secure-your-key-vault.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. kluczy — magazyn](resource-provider-operations.md#microsoftkeyvault)/* |  |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | /Locations/deletedVaults/PURGE/Action [magazynu kluczy firmy Microsoft](resource-provider-operations.md#microsoftkeyvault) | Przeczyść usunięty nietrwale Magazyn kluczy |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)/HsmPools/— Magazyn kluczy * |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)/ManagedHsms/— Magazyn kluczy * |  |
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
        "Microsoft.KeyVault/hsmPools/*",
        "Microsoft.KeyVault/managedHsms/*"
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

### <a name="key-vault-crypto-officer"></a>Key Vault oficer kryptograficzny

Wykonaj dowolną akcję dotyczącą kluczy magazynu kluczy, z wyjątkiem uprawnień do zarządzania. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "kontrola dostępu oparta na rolach" platformy Azure.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | /CheckNameAvailability/Read [magazynu kluczy firmy Microsoft](resource-provider-operations.md#microsoftkeyvault) | Sprawdza, czy nazwa magazynu kluczy jest prawidłowa i czy nie jest używana |
> | /DeletedVaults/Read [magazynu kluczy firmy Microsoft](resource-provider-operations.md#microsoftkeyvault) | Wyświetl właściwości usuniętych nietrwałych magazynów kluczy |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)/Locations/— Magazyn kluczy */Read |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)/Vaults/— Magazyn kluczy */Read |  |
> | /Operations/Read [magazynu kluczy firmy Microsoft](resource-provider-operations.md#microsoftkeyvault) | Wyświetla listę operacji dostępnych w ramach dostawcy zasobów Microsoft. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)/Vaults/Keys/— Magazyn kluczy * |  |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the keys of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/14b46e9e-c2b7-41b4-b07b-48a6ebf60603",
  "name": "14b46e9e-c2b7-41b4-b07b-48a6ebf60603",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto Officer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-service-encryption-user"></a>Key Vault użytkownika szyfrowania usługi kryptograficznej

Odczytywanie metadanych kluczy i wykonywanie operacji zawijania/odpakowania. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "kontrola dostępu oparta na rolach" platformy Azure.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/Write | Utwórz lub zaktualizuj eventSubscription |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/Read | Odczytaj eventSubscription |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/Delete | Usuń eventSubscription |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | /Vaults/Keys/Read [magazynu kluczy firmy Microsoft](resource-provider-operations.md#microsoftkeyvault) | Wyświetl listę kluczy w określonym magazynie lub Odczytaj właściwości i materiał publiczny klucza. W przypadku kluczy asymetrycznych ta operacja ujawnia klucz publiczny i obejmuje możliwość wykonywania algorytmów kluczy publicznych, takich jak szyfrowanie i Weryfikowanie podpisu. Klucze prywatne i klucze symetryczne nigdy nie są ujawniane. |
> | /Vaults/Keys/Wrap/Action [magazynu kluczy firmy Microsoft](resource-provider-operations.md#microsoftkeyvault) | Zawija klucz symetryczny z kluczem Key Vault. Należy pamiętać, że jeśli klucz Key Vault jest asymetryczny, ta operacja może być wykonywana przez podmioty zabezpieczeń z dostępem do odczytu. |
> | /Vaults/Keys/Unwrap/Action [magazynu kluczy firmy Microsoft](resource-provider-operations.md#microsoftkeyvault) | Odpakuje klucz symetryczny z kluczem Key Vault. |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read metadata of keys and perform wrap/unwrap operations. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e147488a-f6f5-4113-8e2d-b22465e65bf6",
  "name": "e147488a-f6f5-4113-8e2d-b22465e65bf6",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventGrid/eventSubscriptions/write",
        "Microsoft.EventGrid/eventSubscriptions/read",
        "Microsoft.EventGrid/eventSubscriptions/delete"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/read",
        "Microsoft.KeyVault/vaults/keys/wrap/action",
        "Microsoft.KeyVault/vaults/keys/unwrap/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto Service Encryption User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-user"></a>Key Vault użytkownika kryptograficznego

Wykonywanie operacji kryptograficznych przy użyciu kluczy. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "kontrola dostępu oparta na rolach" platformy Azure.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | /Vaults/Keys/Read [magazynu kluczy firmy Microsoft](resource-provider-operations.md#microsoftkeyvault) | Wyświetl listę kluczy w określonym magazynie lub Odczytaj właściwości i materiał publiczny klucza. W przypadku kluczy asymetrycznych ta operacja ujawnia klucz publiczny i obejmuje możliwość wykonywania algorytmów kluczy publicznych, takich jak szyfrowanie i Weryfikowanie podpisu. Klucze prywatne i klucze symetryczne nigdy nie są ujawniane. |
> | /Vaults/Keys/Update/Action [magazynu kluczy firmy Microsoft](resource-provider-operations.md#microsoftkeyvault) | Aktualizuje określone atrybuty skojarzone z danym kluczem. |
> | /Vaults/Keys/Backup/Action [magazynu kluczy firmy Microsoft](resource-provider-operations.md#microsoftkeyvault) | Tworzy plik kopii zapasowej klucza. Plik może zostać użyty do przywrócenia klucza w Key Vault tej samej subskrypcji. Mogą być stosowane ograniczenia. |
> | /Vaults/Keys/Encrypt/Action [magazynu kluczy firmy Microsoft](resource-provider-operations.md#microsoftkeyvault) | Szyfruje zwykły tekst przy użyciu klucza. Należy pamiętać, że jeśli klucz jest asymetryczny, ta operacja może być wykonywana przez podmioty zabezpieczeń z dostępem do odczytu. |
> | /Vaults/Keys/Decrypt/Action [magazynu kluczy firmy Microsoft](resource-provider-operations.md#microsoftkeyvault) | Odszyfrowuje tekst szyfrowany przy użyciu klucza. |
> | /Vaults/Keys/Wrap/Action [magazynu kluczy firmy Microsoft](resource-provider-operations.md#microsoftkeyvault) | Zawija klucz symetryczny z kluczem Key Vault. Należy pamiętać, że jeśli klucz Key Vault jest asymetryczny, ta operacja może być wykonywana przez podmioty zabezpieczeń z dostępem do odczytu. |
> | /Vaults/Keys/Unwrap/Action [magazynu kluczy firmy Microsoft](resource-provider-operations.md#microsoftkeyvault) | Odpakuje klucz symetryczny z kluczem Key Vault. |
> | /Vaults/Keys/Sign/Action [magazynu kluczy firmy Microsoft](resource-provider-operations.md#microsoftkeyvault) | Podpisuje skrót wiadomości (hash) za pomocą klucza. |
> | /Vaults/Keys/verify/Action [magazynu kluczy firmy Microsoft](resource-provider-operations.md#microsoftkeyvault) | Weryfikuje sygnaturę skrótu wiadomości (hash) przy użyciu klucza. Należy pamiętać, że jeśli klucz jest asymetryczny, ta operacja może być wykonywana przez podmioty zabezpieczeń z dostępem do odczytu. |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform cryptographic operations using keys. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/12338af0-0e69-4776-bea7-57ae8d297424",
  "name": "12338af0-0e69-4776-bea7-57ae8d297424",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/read",
        "Microsoft.KeyVault/vaults/keys/update/action",
        "Microsoft.KeyVault/vaults/keys/backup/action",
        "Microsoft.KeyVault/vaults/keys/encrypt/action",
        "Microsoft.KeyVault/vaults/keys/decrypt/action",
        "Microsoft.KeyVault/vaults/keys/wrap/action",
        "Microsoft.KeyVault/vaults/keys/unwrap/action",
        "Microsoft.KeyVault/vaults/keys/sign/action",
        "Microsoft.KeyVault/vaults/keys/verify/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-reader"></a>Key Vault czytelnik

Odczytywanie metadanych magazynów kluczy oraz jego certyfikatów, kluczy i wpisów tajnych. Nie można odczytać wartości poufnych, takich jak zawartość wpisu tajnego lub materiał klucza. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "kontrola dostępu oparta na rolach" platformy Azure.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | /CheckNameAvailability/Read [magazynu kluczy firmy Microsoft](resource-provider-operations.md#microsoftkeyvault) | Sprawdza, czy nazwa magazynu kluczy jest prawidłowa i czy nie jest używana |
> | /DeletedVaults/Read [magazynu kluczy firmy Microsoft](resource-provider-operations.md#microsoftkeyvault) | Wyświetl właściwości usuniętych nietrwałych magazynów kluczy |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)/Locations/— Magazyn kluczy */Read |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)/Vaults/— Magazyn kluczy */Read |  |
> | /Operations/Read [magazynu kluczy firmy Microsoft](resource-provider-operations.md#microsoftkeyvault) | Wyświetla listę operacji dostępnych w ramach dostawcy zasobów Microsoft. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)/Vaults/— Magazyn kluczy */Read |  |
> | /Vaults/Secrets/readMetadata/Action [magazynu kluczy firmy Microsoft](resource-provider-operations.md#microsoftkeyvault) | Wyświetlaj listę lub Wyświetl właściwości wpisu tajnego, ale nie jego wartość. |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read metadata of key vaults and its certificates, keys, and secrets. Cannot read sensitive values such as secret contents or key material. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/21090545-7ca7-4776-b22c-e363652d74d2",
  "name": "21090545-7ca7-4776-b22c-e363652d74d2",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/vaults/secrets/readMetadata/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-secrets-officer"></a>Key Vault oficerów tajnych

Wykonaj wszelkie działania dotyczące wpisów tajnych magazynu kluczy, z wyjątkiem uprawnień do zarządzania. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "kontrola dostępu oparta na rolach" platformy Azure.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | /CheckNameAvailability/Read [magazynu kluczy firmy Microsoft](resource-provider-operations.md#microsoftkeyvault) | Sprawdza, czy nazwa magazynu kluczy jest prawidłowa i czy nie jest używana |
> | /DeletedVaults/Read [magazynu kluczy firmy Microsoft](resource-provider-operations.md#microsoftkeyvault) | Wyświetl właściwości usuniętych nietrwałych magazynów kluczy |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)/Locations/— Magazyn kluczy */Read |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)/Vaults/— Magazyn kluczy */Read |  |
> | /Operations/Read [magazynu kluczy firmy Microsoft](resource-provider-operations.md#microsoftkeyvault) | Wyświetla listę operacji dostępnych w ramach dostawcy zasobów Microsoft. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)/Vaults/Secrets/— Magazyn kluczy * |  |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the secrets of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b86a8fe4-44ce-4948-aee5-eccb2c155cd7",
  "name": "b86a8fe4-44ce-4948-aee5-eccb2c155cd7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/secrets/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Secrets Officer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-secrets-user"></a>Użytkownik Key Vault Secret

Odczytaj zawartość wpisu tajnego. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "kontrola dostępu oparta na rolach" platformy Azure.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | /Vaults/Secrets/getSecret/Action [magazynu kluczy firmy Microsoft](resource-provider-operations.md#microsoftkeyvault) | Pobiera wartość wpisu tajnego. |
> | /Vaults/Secrets/readMetadata/Action [magazynu kluczy firmy Microsoft](resource-provider-operations.md#microsoftkeyvault) | Wyświetlaj listę lub Wyświetl właściwości wpisu tajnego, ale nie jego wartość. |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read secret contents. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4633458b-17de-408a-b874-0445c86b69e6",
  "name": "4633458b-17de-408a-b874-0445c86b69e6",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/secrets/getSecret/action",
        "Microsoft.KeyVault/vaults/secrets/readMetadata/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Secrets User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-hsm-contributor"></a>Współautor zarządzanego modułu HSM

Umożliwia zarządzanie zarządzanymi pulami modułów HSM, ale nie umożliwia uzyskiwania do nich dostępu. [Dowiedz się więcej](../key-vault/managed-hsm/secure-your-managed-hsm.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)/ManagedHSMs/— Magazyn kluczy * |  |
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
  "description": "Lets you manage managed HSM pools, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/18500a29-7fe2-46b2-a342-b16a415e101d",
  "name": "18500a29-7fe2-46b2-a342-b16a415e101d",
  "permissions": [
    {
      "actions": [
        "Microsoft.KeyVault/managedHSMs/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed HSM contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-admin"></a>Administrator zabezpieczeń

Wyświetl i zaktualizuj uprawnienia dla Security Center. Te same uprawnienia, jak rola czytelnik zabezpieczeń, mogą także aktualizować zasady zabezpieczeń i odrzucać alerty i zalecenia. [Dowiedz się więcej](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyAssignments/* | Tworzenie przypisań zasad i zarządzanie nimi |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyDefinitions/* | Tworzenie definicji zasad i zarządzanie nimi |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyExemptions/* | Tworzenie wykluczeń zasad i zarządzanie nimi |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policySetDefinitions/* | Tworzenie zestawów zasad i zarządzanie nimi |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Read | Wyświetl listę grup zarządzania dla uwierzytelnionego użytkownika. |
> | [Microsoft. operationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/*/Read | Wyświetlanie danych usługi log Analytics |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/* | Tworzenie i zarządzanie składnikami i zasadami zabezpieczeń |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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
        "Microsoft.Authorization/policyExemptions/*",
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
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/Assessments/Write | Utwórz lub zaktualizuj oceny zabezpieczeń w ramach subskrypcji |
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
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/*/Read | Odczytaj informacje o konfiguracji klasyczne maszyny wirtualne |
> | [Microsoft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/*/Write | Zapisz konfigurację dla klasycznych maszyn wirtualnych |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/*/Read | Odczytaj informacje o konfiguracji dotyczące sieci klasycznej |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/* | Tworzenie i zarządzanie składnikami i zasadami zabezpieczeń |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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

Wyświetl uprawnienia dla Security Center. Może wyświetlać zalecenia, alerty, zasady zabezpieczeń i Stany zabezpieczeń, ale nie może wprowadzać zmian. [Dowiedz się więcej](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/Read | Przeczytaj klasyczny alert dotyczący metryki |
> | [Microsoft. operationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/*/Read | Wyświetlanie danych usługi log Analytics |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/*/Read |  |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/*/Read | Odczytaj składniki i zasady zabezpieczeń |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/*/Read |  |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/iotDefenderSettings/packageDownloads/Action | Pobiera informacje o pakietach IoT Defender do pobrania |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/iotDefenderSettings/downloadManagerActivation/Action | Pobierz plik aktywacji Menedżera z danymi przydziału subskrypcji |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/iotSensors/downloadResetPassword/Action | Pobiera plik hasła resetowania dla czujników IoT |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Read | Wyświetl listę grup zarządzania dla uwierzytelnionego użytkownika. |
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
        "Microsoft.Insights/alertRules/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*/read",
        "Microsoft.Support/*/read",
        "Microsoft.Security/iotDefenderSettings/packageDownloads/action",
        "Microsoft.Security/iotDefenderSettings/downloadManagerActivation/action",
        "Microsoft.Security/iotSensors/downloadResetPassword/action",
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

Umożliwia łączenie, uruchamianie, ponowne uruchamianie i zamykanie maszyn wirtualnych w Azure DevTest Labs. [Dowiedz się więcej](../devtest-labs/devtest-lab-add-devtest-user.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/availabilitySets/Read | Pobierz właściwości zestawu dostępności |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/Read | Odczytywanie właściwości maszyny wirtualnej (rozmiary maszyn wirtualnych, stan środowiska uruchomieniowego, rozszerzenia maszyn wirtualnych itp.) |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/virtualMachines/deallocate/Action | Wyłącza maszynę wirtualną i zwalnia zasoby obliczeniowe |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/virtualMachines/Read | Pobierz właściwości maszyny wirtualnej |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/virtualMachines/restart/Action | Uruchamia ponownie maszynę wirtualną |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/virtualMachines/Start/Action | Uruchamia maszynę wirtualną |
> | [Microsoft. wspólny](resource-provider-operations.md#microsoftdevtestlab)/*/Read | Odczytywanie właściwości laboratorium |
> | [Microsoft. wspólny](resource-provider-operations.md#microsoftdevtestlab)/Labs/claimAnyVm/Action | Zarezerwuj losową maszynę wirtualną z dodaną odpowiedzialnością w laboratorium. |
> | [Microsoft. wspólny](resource-provider-operations.md#microsoftdevtestlab)/Labs/createEnvironment/Action | Twórz maszyny wirtualne w laboratorium. |
> | [Microsoft. wspólny](resource-provider-operations.md#microsoftdevtestlab)/Labs/ensureCurrentUserProfile/Action | Upewnij się, że bieżący użytkownik ma prawidłowy profil w laboratorium. |
> | [Microsoft. wspólny](resource-provider-operations.md#microsoftdevtestlab)/Labs/Formulas/Delete | Usuń formuły. |
> | [Microsoft. wspólny](resource-provider-operations.md#microsoftdevtestlab)/Labs/Formulas/Read | Odczytaj formuły. |
> | [Microsoft. wspólny](resource-provider-operations.md#microsoftdevtestlab)/Labs/Formulas/Write | Dodaj lub zmodyfikuj formuły. |
> | [Microsoft. wspólny](resource-provider-operations.md#microsoftdevtestlab)/Labs/policySets/evaluatePolicies/Action | Szacuje zasady laboratorium. |
> | [Microsoft. wspólny](resource-provider-operations.md#microsoftdevtestlab)/Labs/virtualMachines/Claim/Action | Przejmowanie na własność istniejącej maszyny wirtualnej |
> | [Microsoft. wspólny](resource-provider-operations.md#microsoftdevtestlab)/Labs/virtualmachines/listApplicableSchedules/Action | Wyświetla listę odpowiednich harmonogramów uruchamiania/zatrzymywania (jeśli istnieją). |
> | [Microsoft. wspólny](resource-provider-operations.md#microsoftdevtestlab)/Labs/virtualMachines/getRdpFileContents/Action | Pobiera ciąg, który reprezentuje zawartość pliku RDP dla maszyny wirtualnej |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/backendAddressPools/Join/Action | Sprzęga pulę adresów zaplecza modułu równoważenia obciążenia. Brak alertów. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatRules/Join/Action | Przyłącza do przychodzącej reguły NAT modułu równoważenia obciążenia. Brak alertów. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/*/Read | Odczytywanie właściwości interfejsu sieciowego (na przykład wszystkich modułów równoważenia obciążenia, które są częścią interfejsu sieciowego) |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/Join/Action | Przyłączanie maszyny wirtualnej do interfejsu sieciowego. Brak alertów. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/Read | Pobiera definicję interfejsu sieciowego.  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/Write | Tworzy interfejs sieciowy lub aktualizuje istniejący interfejs sieciowy.  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/*/Read | Odczytywanie właściwości publicznego adresu IP |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/Join/Action | Przyłącza do publicznego adresu IP. Brak alertów. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/Read | Pobiera definicję publicznego adresu IP. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Subnets/Join/Action | Przyłącza do sieci wirtualnej. Brak alertów. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Operations/Read | Pobiera lub wyświetla listę operacji wdrażania. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Read | Pobiera lub wyświetla listę wdrożeń. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/Action | Zwraca klucze dostępu dla określonego konta magazynu. |
> | **NotActions** |  |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/virtualMachines/vmSizes/Read | Wyświetla dostępne rozmiary, do których można zaktualizować maszynę wirtualną |
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

Umożliwia tworzenie nowych laboratoriów na kontach laboratorium platformy Azure. [Dowiedz się więcej](../lab-services/add-lab-creator.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/*/Read |  |
> | [Microsoft. LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/createLab/Action | Utwórz laboratorium na koncie laboratorium. |
> | [Microsoft. LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getPricingAndAvailability/Action | Skorzystaj z cen i dostępności kombinacji rozmiarów, lokalizacje geograficzne i systemów operacyjnych dla konta laboratorium. |
> | [Microsoft. LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getRestrictionsAndUsage/Action | Pobierz podstawowe ograniczenia i użycie dla tej subskrypcji |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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
  "description": "Lets you create new labs under your Azure Lab Accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "name": "b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.LabServices/labAccounts/*/read",
        "Microsoft.LabServices/labAccounts/createLab/action",
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

## <a name="monitor"></a>Monitor


### <a name="application-insights-component-contributor"></a>Współautor składnika Application Insights

Może zarządzać składnikami Application Insights [dowiedzieć się więcej](../azure-monitor/app/resources-roles-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie klasycznych reguł alertów i zarządzanie nimi |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/generateLiveToken/Read | Token pobierania metryk na żywo |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/* | Tworzenie nowych reguł alertów i zarządzanie nimi |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Components/* | Tworzenie składników usługi Insights i zarządzanie nimi |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/scheduledqueryrules/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Topology/Read | Odczytaj topologię |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Transactions/Read | Odczytaj transakcje |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/webtests/* | Tworzenie testów sieci Web usługi Insights i zarządzanie nimi |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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
        "Microsoft.Insights/generateLiveToken/read",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/scheduledqueryrules/*",
        "Microsoft.Insights/topology/read",
        "Microsoft.Insights/transactions/read",
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

Daje użytkownikowi uprawnienia do wyświetlania i pobierania migawek debugowania zebranych za pomocą Snapshot Debugger Application Insights. Należy zauważyć, że te uprawnienia nie są uwzględnione w rolach [właściciel](#owner) lub [współautor](#contributor) . Podczas nadawania użytkownikom roli Snapshot Debugger Application Insights należy przyznać rolę bezpośrednio użytkownikowi. Rola nie jest rozpoznawana, gdy zostanie dodana do roli niestandardowej. [Dowiedz się więcej](../azure-monitor/app/snapshot-debugger.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Components/*/Read |  |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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

Może odczytywać wszystkie dane monitorowania i edytować ustawienia monitorowania. Zobacz też Rozpoczynanie [pracy z rolami, uprawnieniami i zabezpieczeniami przy użyciu Azure monitor](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles). [Dowiedz się więcej](../azure-monitor/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | */read | Odczytuj zasoby wszystkich typów, z wyjątkiem kluczy tajnych. |
> | [Microsoft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/Alerts/* |  |
> | [Microsoft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/alertsSummary/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/actiongroups/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/activityLogAlerts/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/AlertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Components/* | Tworzenie składników usługi Insights i zarządzanie nimi |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/dataCollectionRules/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/dataCollectionRuleAssociations/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/DiagnosticSettings/* | Tworzy, aktualizuje lub odczytuje ustawienia diagnostyczne dla Analysis Server |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/eventtypes/* | Wyświetl listę zdarzeń dziennika aktywności (zdarzenia zarządzania) w subskrypcji. To uprawnienie dotyczy zarówno dostępu programowego, jak i portalu do dziennika aktywności. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/LogDefinitions/* | To uprawnienie jest niezbędne dla użytkowników, którzy potrzebują dostępu do dzienników aktywności za pośrednictwem portalu. Wyświetl listę kategorii dziennika w dzienniku aktywności. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/metricalerts/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/* | Odczytaj definicje metryk (lista dostępnych typów metryk dla zasobu). |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/* | Odczytaj metryki dla zasobu. |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Register/Action | Zarejestruj dostawcę usługi Microsoft Insights |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/scheduledqueryrules/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/webtests/* | Tworzenie testów sieci Web usługi Insights i zarządzanie nimi |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/privateLinkScopes/* |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/privateLinkScopeOperationStatuses/* |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Write | Tworzy nowy obszar roboczy lub łączy z istniejącym obszarem roboczym, podając identyfikator klienta z istniejącego obszaru roboczego. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/intelligencepacks/* | Odczytaj/Zapisz/Usuń pakiety rozwiązań usługi log Analytics. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/savedSearches/* | Odczyt/zapis/usuwanie zapisanych wyszukiwań usługi log Analytics. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Search/Action | Wykonuje zapytanie wyszukiwania |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/sharedKeys/Action | Pobiera klucze wspólne dla obszaru roboczego. Klucze te służą do łączenia agentów programu Microsoft Operational Insights z obszarem roboczym. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/storageinsightconfigs/* | Odczyt/zapis/Usuwanie konfiguracji usługi Magazyn analizy dzienników. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft. Monitor obciążenia został](resource-provider-operations.md#microsoftworkloadmonitor)/Monitors/* | Uzyskaj informacje na temat monitorów kondycji maszyn wirtualnych gościa. |
> | [Microsoft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/smartDetectorAlertRules/* |  |
> | [Microsoft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/actionRules/* |  |
> | [Microsoft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/smartGroups/* |  |
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
        "Microsoft.Insights/dataCollectionRules/*",
        "Microsoft.Insights/dataCollectionRuleAssociations/*",
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

Włącza publikowanie metryk dla zasobów platformy Azure [Dowiedz się więcej](../azure-monitor/insights/container-insights-update-metrics.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Register/Action | Zarejestruj dostawcę usługi Microsoft Insights |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Write | Metryki zapisu |
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

Może odczytywać wszystkie dane monitorowania (metryki, dzienniki itp.). Zobacz też Rozpoczynanie [pracy z rolami, uprawnieniami i zabezpieczeniami przy użyciu Azure monitor](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles). [Dowiedz się więcej](../azure-monitor/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | */read | Odczytuj zasoby wszystkich typów, z wyjątkiem kluczy tajnych. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/Workspaces/Search/Action | Wykonuje zapytanie wyszukiwania |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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

Może zapisywać udostępnione skoroszyty. [Dowiedz się więcej](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/Write | Utwórz lub zaktualizuj skoroszyt |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/Delete | Usuwanie skoroszytu |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/Read | Odczytaj skoroszyt |
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

Może odczytywać skoroszyty. [Dowiedz się więcej](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/Workbooks/Read | Odczytaj skoroszyt |
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

Twórz zadania i zarządzaj nimi za pomocą elementów Runbook usługi Automation. [Dowiedz się więcej](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/hybridRunbookWorkerGroups/Read | Odczytuje zasoby hybrydowego procesu roboczego elementu Runbook |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Read | Pobiera zadanie Azure Automation |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Resume/Action | Wznawia zadanie Azure Automation |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Stop/Action | Kończy zadanie Azure Automation |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Streams/Read | Pobiera strumień zadania Azure Automation |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Suspend/Action | Wstrzymuje zadanie Azure Automation |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Write | Tworzy zadanie Azure Automation |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Output/Read | Pobiera dane wyjściowe zadania |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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

Operatory automatyzacji mogą uruchamiać, zatrzymywać, wstrzymywać i wznawiać zadania [dowiedzieć się więcej](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/hybridRunbookWorkerGroups/Read | Odczytuje zasoby hybrydowego procesu roboczego elementu Runbook |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Read | Pobiera zadanie Azure Automation |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Resume/Action | Wznawia zadanie Azure Automation |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Stop/Action | Kończy zadanie Azure Automation |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Streams/Read | Pobiera strumień zadania Azure Automation |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Suspend/Action | Wstrzymuje zadanie Azure Automation |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Write | Tworzy zadanie Azure Automation |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobSchedules/Read | Pobiera harmonogram zadań Azure Automation |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobSchedules/Write | Tworzy harmonogram zadań Azure Automation |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/linkedWorkspace/Read | Pobiera obszar roboczy połączony z kontem usługi Automation |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Read | Pobiera konto Azure Automation |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Runbooks/Read | Pobiera element Runbook Azure Automation |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Schedules/Read | Pobiera zasób harmonogramu Azure Automation |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Schedules/Write | Tworzy lub aktualizuje zasób harmonogramu Azure Automation |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Output/Read | Pobiera dane wyjściowe zadania |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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

Odczytywanie właściwości elementu Runbook — aby można było tworzyć zadania elementu Runbook. [Dowiedz się więcej](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Runbooks/Read | Pobiera element Runbook Azure Automation |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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

Można dołączyć maszyny połączone z platformą Azure. [Dowiedz się więcej](../azure-arc/servers/onboard-service-principal.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/Read | Odczytaj wszystkie maszyny usługi Azure Arc |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/Write | Zapisuje maszyny usługi Azure Arc |
> | [Microsoft. GuestConfiguration](resource-provider-operations.md#microsoftguestconfiguration)/guestConfigurationAssignments/Read | Pobierz przypisanie konfiguracji gościa. |
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
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/Read | Odczytaj wszystkie maszyny usługi Azure Arc |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/Write | Zapisuje maszyny usługi Azure Arc |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/Delete | Usuwa maszyny usługi Azure Arc |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/reconnect/Action |  |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/Extensions/Write | Instaluje lub aktualizuje rozszerzenia usługi Azure Arc |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/*/Read |  |
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

Zezwala na dostęp do odczytu do danych rozliczeniowych [Dowiedz się więcej](../cost-management-billing/manage/manage-billing-access.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. rozliczenia](resource-provider-operations.md#microsoftbilling)/*/Read | Odczytaj informacje o rozliczeniach |
> | [Microsoft. Commerce](resource-provider-operations.md#microsoftcommerce)/*/Read |  |
> | [Microsoft. zużycie](resource-provider-operations.md#microsoftconsumption)/*/Read |  |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Read | Wyświetl listę grup zarządzania dla uwierzytelnionego użytkownika. |
> | [Microsoft. CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/Read |  |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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

Może zarządzać definicjami planu, ale nie należy ich przypisywać. [Dowiedz się więcej](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. plan](resource-provider-operations.md#microsoftblueprint)/Blueprints/* | Twórz definicje planów lub artefakty planów i zarządzaj nimi. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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

Może przypisywać istniejące opublikowane plany, ale nie może tworzyć nowych planów. Należy zauważyć, że to działanie działa tylko wtedy, gdy przypisanie zostało wykonane przy użyciu tożsamości zarządzanej przypisanej przez użytkownika. [Dowiedz się więcej](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. plan](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/* | Utwórz przypisania planu i zarządzaj nimi. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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

Umożliwia wyświetlanie kosztów i zarządzanie konfiguracją kosztów (np. budżetów, eksportów) [więcej informacji](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. zużycie](resource-provider-operations.md#microsoftconsumption)/* |  |
> | [Microsoft. CostManagement](resource-provider-operations.md#microsoftcostmanagement)/* |  |
> | [Microsoft. rozliczenia](resource-provider-operations.md#microsoftbilling)/billingPeriods/Read |  |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/Read | Pobiera listę subskrypcji. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft. Advisor](resource-provider-operations.md#microsoftadvisor)—/Configurations/Read | Pobierz konfiguracje |
> | [Microsoft. Advisor](resource-provider-operations.md#microsoftadvisor)—/Recommendations/Read | Odczytuje zalecenia |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Read | Wyświetl listę grup zarządzania dla uwierzytelnionego użytkownika. |
> | [Microsoft. rozliczenia](resource-provider-operations.md#microsoftbilling)/billingProperty/Read |  |
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
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Billing/billingProperty/read"
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

Może wyświetlać dane i konfigurację kosztów (np. budżetów, eksportów) [więcej informacji](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. zużycie](resource-provider-operations.md#microsoftconsumption)/*/Read |  |
> | [Microsoft. CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/Read |  |
> | [Microsoft. rozliczenia](resource-provider-operations.md#microsoftbilling)/billingPeriods/Read |  |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/Read | Pobiera listę subskrypcji. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft. Advisor](resource-provider-operations.md#microsoftadvisor)—/Configurations/Read | Pobierz konfiguracje |
> | [Microsoft. Advisor](resource-provider-operations.md#microsoftadvisor)—/Recommendations/Read | Odczytuje zalecenia |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Read | Wyświetl listę grup zarządzania dla uwierzytelnionego użytkownika. |
> | [Microsoft. rozliczenia](resource-provider-operations.md#microsoftbilling)/billingProperty/Read |  |
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
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Billing/billingProperty/read"
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

### <a name="hierarchy-settings-administrator"></a>Administrator ustawień hierarchii

Umożliwia użytkownikom edytowanie i usuwanie ustawień hierarchii

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Settings/Write | Tworzy lub aktualizuje ustawienia hierarchii grup zarządzania. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Settings/Delete | Usuwa ustawienia hierarchii grupy zarządzania. |
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
  "description": "Allows users to edit and delete Hierarchy Settings",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/350f8d15-c687-4448-8ae1-157740a3936d",
  "name": "350f8d15-c687-4448-8ae1-157740a3936d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/settings/write",
        "Microsoft.Management/managementGroups/settings/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Hierarchy Settings Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="kubernetes-cluster---azure-arc-onboarding"></a>Klaster Kubernetes — dołączanie do usługi Azure Arc

Definicja roli do autoryzacji dowolnego użytkownika/usługi do tworzenia zasobów connectedClusters [Dowiedz się więcej](../azure-arc/kubernetes/connect-cluster.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Write | Tworzy lub aktualizuje wdrożenie. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/Read | Pobierz wyniki operacji subskrypcji. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/Read | Pobiera listę subskrypcji. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Write | Zapisuje connectedClusters |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Read | Odczytaj connectedClusters |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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
  "description": "Role definition to authorize any user/service to create connectedClusters resource",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "name": "34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Kubernetes/connectedClusters/Write",
        "Microsoft.Kubernetes/connectedClusters/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Kubernetes Cluster - Azure Arc Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-contributor-role"></a>Rola współautora aplikacji zarządzanej

Umożliwia tworzenie zasobów aplikacji zarządzanej.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | */read | Odczytuj zasoby wszystkich typów, z wyjątkiem kluczy tajnych. |
> | [Microsoft. Solutions](resource-provider-operations.md#microsoftsolutions)/Applications/* |  |
> | [Microsoft. Solutions](resource-provider-operations.md#microsoftsolutions)/Register/Action | Zarejestruj się w rozwiązaniach. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/* |  |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
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
> | Akcje | Opis |
> | --- | --- |
> | */read | Odczytuj zasoby wszystkich typów, z wyjątkiem kluczy tajnych. |
> | [Microsoft. Solutions](resource-provider-operations.md#microsoftsolutions)/Applications/Read | Pobiera listę aplikacji. |
> | [Microsoft. Solutions](resource-provider-operations.md#microsoftsolutions)/*/Action |  |
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
> | Akcje | Opis |
> | --- | --- |
> | */read | Odczytuj zasoby wszystkich typów, z wyjątkiem kluczy tajnych. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. Solutions](resource-provider-operations.md#microsoftsolutions)/jitRequests/* |  |
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

Rola usuwania przypisania rejestracji usług zarządzanych umożliwia użytkownikom zarządzającym dzierżawę usuwanie przypisania rejestracji przypisanego do swojej dzierżawy. [Dowiedz się więcej](../lighthouse/how-to/remove-delegation.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/Read | Pobiera listę przypisań rejestracji usług zarządzanych. |
> | [Microsoft. ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/Delete | Usuwa przypisanie rejestracji usług zarządzanych. |
> | [Microsoft. ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/operationStatuses/Read | Odczytuje stan operacji dla zasobu. |
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

Rola współautora grupy zarządzania [Dowiedz się więcej](../governance/management-groups/overview.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Delete | Usuń grupę zarządzania. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Read | Wyświetl listę grup zarządzania dla uwierzytelnionego użytkownika. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/Delete | Anuluj skojarzenie subskrypcji z grupą zarządzania. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/Write | Kojarzy istniejącą subskrypcję z grupą zarządzania. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Write | Utwórz lub Zaktualizuj grupę zarządzania. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/Read | Wyświetla listę subskrypcji w ramach danej grupy zarządzania. |
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
        "Microsoft.Management/managementGroups/write",
        "Microsoft.Management/managementGroups/subscriptions/read"
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
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Read | Wyświetl listę grup zarządzania dla uwierzytelnionego użytkownika. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/Read | Wyświetla listę subskrypcji w ramach danej grupy zarządzania. |
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
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/read"
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
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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

Zezwala na dostęp do odczytu do zasad zasobów i dostęp do zapisu dla zdarzeń zasad składnika zasobów. [Dowiedz się więcej](../governance/policy/concepts/policy-for-kubernetes.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyassignments/Read | Pobierz informacje o przypisaniu zasad. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/PolicyDefinitions/Read | Pobierz informacje o definicji zasad. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyexemptions/Read | Uzyskaj informacje na temat wykluczania zasad. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/Read | Pobierz informacje o definicji zestawu zasad. |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/checkDataPolicyCompliance/Action | Sprawdź stan zgodności danego składnika względem zasad dotyczących danych. |
> | [Microsoft. PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/policyEvents/logDataEvents/Action | Rejestruj zdarzenia zasad składnika zasobów. |
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
        "Microsoft.Authorization/policyexemptions/read",
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

### <a name="quota-request-operator-role"></a>Rola operatora żądania limitu przydziału

Odczytywanie i tworzenie żądań limitu przydziału, pobieranie stanu żądania przydziału i tworzenie biletów pomocy technicznej. [Dowiedz się więcej](/rest/api/reserved-vm-instances/quotaapi)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Pojemność](resource-provider-operations.md#microsoftcapacity)—/resourceProviders/Locations/serviceLimits/Read | Pobieranie bieżącego limitu usługi lub przydziału określonego zasobu i lokalizacji |
> | [Microsoft. Pojemność](resource-provider-operations.md#microsoftcapacity)—/resourceProviders/Locations/serviceLimits/Write | Utwórz limit usług lub przydział dla określonego zasobu i lokalizacji |
> | [Microsoft. Pojemność](resource-provider-operations.md#microsoftcapacity)—/resourceProviders/Locations/serviceLimitsRequests/Read | Uzyskaj wszelkie żądania limitu usług dla określonego zasobu i lokalizacji |
> | [Microsoft. Pojemność](resource-provider-operations.md#microsoftcapacity)—/Register/Action | Rejestruje dostawcę zasobów pojemności i umożliwia tworzenie zasobów pojemności. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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
  "description": "Role to read and create Quota Requests and get Quota Request Status.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0e5f05e5-9ab9-446b-b98d-1e2157c94125",
  "name": "0e5f05e5-9ab9-446b-b98d-1e2157c94125",
  "permissions": [
    {
      "actions": [
        "Microsoft.Capacity/resourceProviders/locations/serviceLimits/read",
        "Microsoft.Capacity/resourceProviders/locations/serviceLimits/write",
        "Microsoft.Capacity/resourceProviders/locations/serviceLimitsRequests/read",
        "Microsoft.Capacity/register/action",
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
  "roleName": "Quota Request Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reservation-purchaser"></a>Zakup rezerwacji

Umożliwia zakup rezerwacji [więcej informacji](../cost-management-billing/reservations/prepare-buy-reservation.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/Read | Pobiera listę subskrypcji. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Pojemność](resource-provider-operations.md#microsoftcapacity)—/Register/Action | Rejestruje dostawcę zasobów pojemności i umożliwia tworzenie zasobów pojemności. |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/Register/Action | Rejestruje subskrypcję za pomocą dostawcy zasobów Microsoft. COMPUTE |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Register/Action | Rejestruje subskrypcję dostawcy zasobów programu Microsoft SQL Database i umożliwia tworzenie baz danych Microsoft SQL. |
> | [Microsoft./Register/Action użycia](resource-provider-operations.md#microsoftconsumption) | Zarejestruj się w celu użycia RP |
> | [Microsoft. Pojemność](resource-provider-operations.md#microsoftcapacity)—/Catalogs/Read | Odczytaj wykaz rezerwacji |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/roleAssignments/Read | Pobierz informacje o przypisaniu roli. |
> | [Microsoft./ReservationRecommendations/Read użycia](resource-provider-operations.md#microsoftconsumption) | Wyświetl listę pojedynczych lub udostępnionych zaleceń dla wystąpień zarezerwowanych dla subskrypcji. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/supporttickets/Write | Umożliwia tworzenie i aktualizowanie biletu pomocy technicznej |
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
  "description": "Lets you purchase reservations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f7b75c60-3036-4b75-91c3-6b41c27c1689",
  "name": "f7b75c60-3036-4b75-91c3-6b41c27c1689",
  "permissions": [
    {
      "actions": [
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Capacity/register/action",
        "Microsoft.Compute/register/action",
        "Microsoft.SQL/register/action",
        "Microsoft.Consumption/register/action",
        "Microsoft.Capacity/catalogs/read",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Consumption/reservationRecommendations/read",
        "Microsoft.Support/supporttickets/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reservation Purchaser",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="resource-policy-contributor"></a>Współautor zasad zasobów

Użytkownicy z prawami do tworzenia/modyfikowania zasad dotyczących zasobów, tworzenia biletów pomocy technicznej i odczytywania zasobów/hierarchii. [Dowiedz się więcej](../governance/policy/overview.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | */read | Odczytuj zasoby wszystkich typów, z wyjątkiem kluczy tajnych. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyassignments/* | Tworzenie przypisań zasad i zarządzanie nimi |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/PolicyDefinitions/* | Tworzenie definicji zasad i zarządzanie nimi |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyexemptions/* | Tworzenie wykluczeń zasad i zarządzanie nimi |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/* | Tworzenie zestawów zasad i zarządzanie nimi |
> | [Microsoft. PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/* |  |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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
        "Microsoft.Authorization/policyexemptions/*",
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

### <a name="site-recovery-contributor"></a>Współautor usługi Site Recovery

Umożliwia zarządzanie usługą Site Recovery z wyjątkiem tworzenia magazynu i przypisywania ról [Dowiedz się więcej](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | Pobierz definicję sieci wirtualnej |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/allocatedStamp/Read | Operacja getallocatedstamp jest operacją wewnętrzną używaną przez usługę |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/allocateStamp/Action | AllocateStamp jest operacją wewnętrzną używaną przez usługę |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Certificates/Write | Operacja Aktualizuj certyfikat zasobu aktualizuje certyfikat poświadczeń zasobu/magazynu. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/* | Utwórz rozszerzone informacje powiązane z magazynem i zarządzaj nimi |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "magazyn" |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/* | Tworzenie zarejestrowanych tożsamości i zarządzanie nimi |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationAlertSettings/* | Utwórz lub zaktualizuj ustawienia alertu replikacji |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationEvents/Read | Odczytaj wszystkie zdarzenia |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/* | Tworzenie sieci szkieletowej replikacji i zarządzanie nimi |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationJobs/* | Tworzenie zadań replikacji i zarządzanie nimi |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationPolicies/* | Tworzenie zasad replikacji i zarządzanie nimi |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/* | Tworzenie planów odzyskiwania i zarządzanie nimi |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/* | Tworzenie i zarządzanie konfiguracją magazynu Recovery Services magazynu |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Usages/Read | Zwraca szczegóły użycia magazynu Recovery Servicesowego. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/Read | Operacja token magazynu umożliwia pobieranie tokenu magazynu dla operacji zaplecza na poziomie magazynu. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/* | Odczytaj alerty dla magazynu usługi Recovery Services |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/Read |  |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/Read | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationOperationStatus/Read | Odczytaj wszystkie Stany operacji replikacji magazynu |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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

### <a name="site-recovery-operator"></a>Operator usługi Site Recovery

Umożliwia przełączenie w tryb failover i powrót po awarii, ale nie wykonywanie innych operacji zarządzania Site Recovery [więcej informacji](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Read | Pobierz definicję sieci wirtualnej |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/allocatedStamp/Read | Operacja getallocatedstamp jest operacją wewnętrzną używaną przez usługę |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/allocateStamp/Action | AllocateStamp jest operacją wewnętrzną używaną przez usługę |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/Read | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu? |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "magazyn" |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/Read | Operacja Pobierz wyniki operacji umożliwia pobieranie stanu operacji i wyniku operacji przesłanej asynchronicznie |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/Read | Operacja Pobierz kontenery umożliwia pobieranie kontenerów zarejestrowanych dla zasobu. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationAlertSettings/Read | Odczytaj wszystkie ustawienia alertów |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationEvents/Read | Odczytaj wszystkie zdarzenia |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/checkConsistency/Action | Sprawdza spójność sieci szkieletowej |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/Read | Odczytaj wszystkie sieci szkieletowe |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/reassociateGateway/Action | Skojarz ponownie bramę |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/renewcertificate/Action | Odnów certyfikat sieci szkieletowej |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationNetworks/Read | Odczytaj wszystkie sieci |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/Read | Odczytaj wszystkie mapowania sieci |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/Read | Odczytaj wszystkie kontenery ochrony |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/Read | Odczytaj dowolne elementy podlegające ochronie |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/Action | Zastosuj punkt odzyskiwania |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/Action | Zatwierdzanie trybu failover |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/Action | Planowane przełączanie do trybu failover |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/Read | Odczytaj wszystkie chronione elementy |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/Read | Odczytaj wszystkie punkty odzyskiwania replikacji |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/Action | Napraw replikację |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/Action | Włącz ponownie ochronę chronionego elementu |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/switchprotection/Action | Przełącz kontener ochrony |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/Action | Test pracy w trybie failover |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/Action | Czyszczenie testu pracy w trybie failover |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/Action | Tryb failover |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/Action | Aktualizowanie usługi mobilności |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/Read | Odczytaj wszelkie mapowania kontenerów ochrony |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationRecoveryServicesProviders/Read | Odczytaj wszystkich dostawców Recovery Services |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/Action | Odśwież dostawcę |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationStorageClassifications/Read | Odczytaj wszystkie klasyfikacje magazynu |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/Read | Odczytaj dowolne mapowania klasyfikacji magazynu |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationvCenters/Read | Odczytaj dowolne vCenter |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationJobs/* | Tworzenie zadań replikacji i zarządzanie nimi |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationPolicies/Read | Odczytaj wszystkie zasady |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/failoverCommit/Action | Plan odzyskiwania zatwierdzania w trybie failover |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/plannedFailover/Action | Plan odzyskiwania planowanej pracy w trybie failover |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/Read | Zapoznaj się z planami odzyskiwania |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/reProtect/Action | Ponowne włączanie ochrony planu odzyskiwania |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/testFailover/Action | Plan odzyskiwania testowego trybu failover |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/testFailoverCleanup/Action | Plan odzyskiwania oczyszczania testów w trybie failover |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/unplannedFailover/Action | Plan odzyskiwania w trybie failover |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/* | Odczytaj alerty dla magazynu usługi Recovery Services |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Usages/Read | Zwraca szczegóły użycia magazynu Recovery Servicesowego. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/Read | Operacja token magazynu umożliwia pobieranie tokenu magazynu dla operacji zaplecza na poziomie magazynu. |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/Read | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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

### <a name="site-recovery-reader"></a>Czytelnik usługi Site Recovery

Umożliwia wyświetlanie stanu Site Recovery, ale nie wykonywanie innych operacji zarządzania więcej [informacji](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Locations/allocatedStamp/Read | Operacja getallocatedstamp jest operacją wewnętrzną używaną przez usługę |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/Read | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu? |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/Read | Pobiera alerty dla magazynu usługi Recovery Services. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Read | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "magazyn" |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/Read | Operacja Pobierz wyniki operacji umożliwia pobieranie stanu operacji i wyniku operacji przesłanej asynchronicznie |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/Read | Operacja Pobierz kontenery umożliwia pobieranie kontenerów zarejestrowanych dla zasobu. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationAlertSettings/Read | Odczytaj wszystkie ustawienia alertów |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationEvents/Read | Odczytaj wszystkie zdarzenia |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/Read | Odczytaj wszystkie sieci szkieletowe |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationNetworks/Read | Odczytaj wszystkie sieci |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/Read | Odczytaj wszystkie mapowania sieci |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/Read | Odczytaj wszystkie kontenery ochrony |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/Read | Odczytaj dowolne elementy podlegające ochronie |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/Read | Odczytaj wszystkie chronione elementy |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/Read | Odczytaj wszystkie punkty odzyskiwania replikacji |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/Read | Odczytaj wszelkie mapowania kontenerów ochrony |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationRecoveryServicesProviders/Read | Odczytaj wszystkich dostawców Recovery Services |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationStorageClassifications/Read | Odczytaj wszystkie klasyfikacje magazynu |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/Read | Odczytaj dowolne mapowania klasyfikacji magazynu |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationvCenters/Read | Odczytaj dowolne vCenter |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationJobs/Read | Odczytaj wszystkie zadania |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationPolicies/Read | Odczytaj wszystkie zasady |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/Read | Zapoznaj się z planami odzyskiwania |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/Read |  |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Usages/Read | Zwraca szczegóły użycia magazynu Recovery Servicesowego. |
> | [Microsoft. RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/Read | Operacja token magazynu umożliwia pobieranie tokenu magazynu dla operacji zaplecza na poziomie magazynu. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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

Pozwala tworzyć żądania pomocy technicznej i zarządzać nimi [dowiedzieć się więcej](../azure-portal/supportability/how-to-create-azure-support-request.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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

Umożliwia zarządzanie tagami w jednostkach bez zapewniania dostępu do samych jednostek. [Dowiedz się więcej](../azure-resource-manager/management/tag-resources.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Resources/Read | Pobiera zasoby dla grupy zasobów. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/Resources/Read | Pobiera zasoby subskrypcji. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Tags/* |  |
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


### <a name="azure-digital-twins-data-owner"></a>Właściciel danych Digital bliźniaczych reprezentacji systemu Azure

Pełna rola dostępu do danych cyfrowych bliźniaczych reprezentacji — [Dowiedz się więcej](../digital-twins/concepts-security.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/eventroutes/* | Odczytywanie, usuwanie, tworzenie lub aktualizowanie dowolnej trasy zdarzeń |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/* | Odczytaj, Utwórz, zaktualizuj lub Usuń dowolne cyfrowe sznurki |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/Commands/* | Wywoływanie dowolnego polecenia na dwucyfrowej Bliźniaczyej |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/Relationships/* | Odczytaj, Utwórz, zaktualizuj lub Usuń dowolne cyfrowe relacje dwuosiowe |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/models/* | Odczytaj, Utwórz, zaktualizuj lub Usuń dowolny model |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/Query/* | Wykonywanie zapytania dotyczącego cyfrowego wykresu bliźniaczych reprezentacji |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access role for Digital Twins data-plane",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/bcd981a7-7f74-457b-83e1-cceb9e632ffe",
  "name": "bcd981a7-7f74-457b-83e1-cceb9e632ffe",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DigitalTwins/eventroutes/*",
        "Microsoft.DigitalTwins/digitaltwins/*",
        "Microsoft.DigitalTwins/digitaltwins/commands/*",
        "Microsoft.DigitalTwins/digitaltwins/relationships/*",
        "Microsoft.DigitalTwins/models/*",
        "Microsoft.DigitalTwins/query/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Digital Twins Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-digital-twins-data-reader"></a>Czytnik danych Digital bliźniaczych reprezentacji systemu Azure

Rola tylko do odczytu dla właściwości płaszczyzny danych cyfrowych bliźniaczych reprezentacji [Dowiedz się więcej](../digital-twins/concepts-security.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/Read | Odczytaj dowolne cyfrowe sznurki |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/Relationships/Read | Odczytaj wszystkie cyfrowe relacje jednoosiowe |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/eventroutes/Read | Odczytaj wszystkie trasy zdarzeń |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/models/Read | Odczytaj dowolny model |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/Query/Action | Wykonywanie zapytania dotyczącego cyfrowego wykresu bliźniaczych reprezentacji |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only role for Digital Twins data-plane properties",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d57506d4-4c8d-48b1-8587-93c323f6a5a3",
  "name": "d57506d4-4c8d-48b1-8587-93c323f6a5a3",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DigitalTwins/digitaltwins/read",
        "Microsoft.DigitalTwins/digitaltwins/relationships/read",
        "Microsoft.DigitalTwins/eventroutes/read",
        "Microsoft.DigitalTwins/models/read",
        "Microsoft.DigitalTwins/query/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Digital Twins Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="biztalk-contributor"></a>Współautor BizTalk

Umożliwia zarządzanie usługami BizTalk Services, ale nie umożliwia uzyskiwania do nich dostępu.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | Microsoft. BizTalkServices/BizTalk/* | Tworzenie usługi BizTalk Services i zarządzanie nimi |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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

### <a name="desktop-virtualization-application-group-contributor"></a>Współautor grupy aplikacji wirtualizacji pulpitu

Współautor grupy aplikacji wirtualizacji pulpitu. [Dowiedz się więcej](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/* |  |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/Read | Odczytaj hostpools |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/Read | Odczytaj hostpools/sessionhosts |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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
  "description": "Contributor of the Desktop Virtualization Application Group.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/86240b0e-9422-4c43-887b-b61143f32ba8",
  "name": "86240b0e-9422-4c43-887b-b61143f32ba8",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/applicationgroups/*",
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Application Group Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-application-group-reader"></a>Czytelnik grupy aplikacji wirtualizacji pulpitu

Czytelnik grupy aplikacji wirtualizacji pulpitu. [Dowiedz się więcej](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/*/Read |  |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/Read | Odczytaj applicationgroups |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/Read | Odczytaj hostpools |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/Read | Odczytaj hostpools/sessionhosts |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Read | Pobiera lub wyświetla listę wdrożeń. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/Read | Przeczytaj klasyczny alert dotyczący metryki |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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
  "description": "Reader of the Desktop Virtualization Application Group.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/aebf23d0-b568-4e86-b8f9-fe83a2c6ab55",
  "name": "aebf23d0-b568-4e86-b8f9-fe83a2c6ab55",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/applicationgroups/*/read",
        "Microsoft.DesktopVirtualization/applicationgroups/read",
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Application Group Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-contributor"></a>Współautor wirtualizacji pulpitu

Współautor wirtualizacji pulpitu. [Dowiedz się więcej](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/* |  |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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
  "description": "Contributor of Desktop Virtualization.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/082f0a83-3be5-4ba1-904c-961cca79b387",
  "name": "082f0a83-3be5-4ba1-904c-961cca79b387",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-host-pool-contributor"></a>Współautor puli hostów hosta wirtualizacji pulpitu

Współautor puli hostów wirtualizacji pulpitu. [Dowiedz się więcej](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/* |  |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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
  "description": "Contributor of the Desktop Virtualization Host Pool.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e307426c-f9b6-4e81-87de-d99efb3c32bc",
  "name": "e307426c-f9b6-4e81-87de-d99efb3c32bc",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Host Pool Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-host-pool-reader"></a>Czytnik puli hosta wirtualizacji pulpitu

Czytnik puli hostów wirtualizacji pulpitu. [Dowiedz się więcej](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/*/Read |  |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/Read | Odczytaj hostpools |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Read | Pobiera lub wyświetla listę wdrożeń. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/Read | Przeczytaj klasyczny alert dotyczący metryki |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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
  "description": "Reader of the Desktop Virtualization Host Pool.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ceadfde2-b300-400a-ab7b-6143895aa822",
  "name": "ceadfde2-b300-400a-ab7b-6143895aa822",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/*/read",
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Host Pool Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-reader"></a>Czytnik wirtualizacji pulpitu

Czytelnik wirtualizacji pulpitu. [Dowiedz się więcej](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/*/Read |  |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Read | Pobiera lub wyświetla listę wdrożeń. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/Read | Przeczytaj klasyczny alert dotyczący metryki |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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
  "description": "Reader of Desktop Virtualization.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/49a72310-ab8d-41df-bbb0-79b649203868",
  "name": "49a72310-ab8d-41df-bbb0-79b649203868",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-session-host-operator"></a>Operator hosta sesji wirtualizacji pulpitu

Operator hosta sesji wirtualizacji pulpitu. [Dowiedz się więcej](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/Read | Odczytaj hostpools |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/* |  |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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
  "description": "Operator of the Desktop Virtualization Session Host.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2ad6aaab-ead9-4eaa-8ac5-da422f562408",
  "name": "2ad6aaab-ead9-4eaa-8ac5-da422f562408",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Session Host Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-user"></a>Użytkownik wirtualizacji pulpitu

Umożliwia użytkownikom korzystanie z aplikacji w grupie aplikacji. [Dowiedz się więcej](../virtual-desktop/delegated-access-virtual-desktop.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **Akcje dataactions** |  |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationGroups/useApplications/Action | Korzystanie z aplikacji |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows user to use the applications in an application group.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63",
  "name": "1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DesktopVirtualization/applicationGroups/useApplications/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-user-session-operator"></a>Operator sesji użytkownika wirtualizacji pulpitu

Operator sesji Uesr wirtualizacji pulpitu. [Dowiedz się więcej](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/Read | Odczytaj hostpools |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/Read | Odczytaj hostpools/sessionhosts |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/usersessions/* |  |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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
  "description": "Operator of the Desktop Virtualization Uesr Session.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ea4bfff8-7fb4-485a-aadd-d4129a0ffaa6",
  "name": "ea4bfff8-7fb4-485a-aadd-d4129a0ffaa6",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization User Session Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-workspace-contributor"></a>Współautor obszaru roboczego wirtualizacji pulpitu

Współautor obszaru roboczego wirtualizacji pulpitu. [Dowiedz się więcej](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/Workspaces/* |  |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/Read | Odczytaj applicationgroups |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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
  "description": "Contributor of the Desktop Virtualization Workspace.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/21efdde3-836f-432b-bf3d-3e8e734d4b2b",
  "name": "21efdde3-836f-432b-bf3d-3e8e734d4b2b",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/workspaces/*",
        "Microsoft.DesktopVirtualization/applicationgroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Workspace Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-workspace-reader"></a>Czytnik pulpitu obszaru roboczego

Czytnik obszaru roboczego wirtualizacji pulpitu. [Dowiedz się więcej](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/Workspaces/Read | Odczytuj obszary robocze |
> | [Microsoft. DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/Read | Odczytaj applicationgroups |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/Read | Pobiera lub wyświetla listę wdrożeń. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/Read | Przeczytaj klasyczny alert dotyczący metryki |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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
  "description": "Reader of the Desktop Virtualization Workspace.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0fa44ee9-7a7d-466b-9bb2-2bf446b1204d",
  "name": "0fa44ee9-7a7d-466b-9bb2-2bf446b1204d",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/workspaces/read",
        "Microsoft.DesktopVirtualization/applicationgroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Workspace Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="disk-backup-reader"></a>Czytnik kopii zapasowej dysku

Zapewnia uprawnienie do magazynu kopii zapasowych w celu wykonania kopii zapasowej na dysku. [Dowiedz się więcej](../backup/disk-backup-faq.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/disks/Read | Pobieranie właściwości dysku |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/disks/beginGetAccess/Action | Pobierz identyfikator URI sygnatury dostępu współdzielonego dysku na potrzeby usługi dostęp do obiektów BLOB |
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
  "description": "Provides permission to backup vault to perform disk backup.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3e5e47e6-65f7-47ef-90b5-e5dd4d455f24",
  "name": "3e5e47e6-65f7-47ef-90b5-e5dd4d455f24",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/disks/read",
        "Microsoft.Compute/disks/beginGetAccess/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Disk Backup Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="disk-restore-operator"></a>Operator przywracania dysku

Zapewnia uprawnienie do magazynu kopii zapasowych w celu przeprowadzenia przywracania dysku. [Dowiedz się więcej](../backup/restore-managed-disks.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/disks/Write | Tworzy nowy dysk lub aktualizuje istniejący |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/disks/Read | Pobieranie właściwości dysku |
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
  "description": "Provides permission to backup vault to perform disk restore.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b50d9833-a0cb-478e-945f-707fcc997c13",
  "name": "b50d9833-a0cb-478e-945f-707fcc997c13",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Compute/disks/write",
        "Microsoft.Compute/disks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Disk Restore Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="disk-snapshot-contributor"></a>Współautor migawek dysków

Zapewnia uprawnienie do magazynu kopii zapasowych w celu zarządzania migawkami dysków. [Dowiedz się więcej](../backup/backup-managed-disks.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/snapshots/Delete | Usuń migawkę |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/snapshots/Write | Utwórz nową migawkę lub zaktualizuj istniejącą |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/snapshots/Read | Pobierz właściwości migawki |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/snapshots/beginGetAccess/Action | Pobierz identyfikator URI sygnatury dostępu współdzielonego dla migawki do obiektu BLOB |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/snapshots/endGetAccess/Action | Odwołaj identyfikator URI sygnatury dostępu współdzielonego dla migawki |
> | [Microsoft. COMPUTE](resource-provider-operations.md#microsoftcompute)/disks/beginGetAccess/Action | Pobierz identyfikator URI sygnatury dostępu współdzielonego dysku na potrzeby usługi dostęp do obiektów BLOB |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/ListKeys/Action | Zwraca klucze dostępu dla określonego konta magazynu. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/Write | Tworzy konto magazynu z określonymi parametrami lub aktualizuje właściwości lub Tagi albo dodaje niestandardową domenę dla określonego konta magazynu. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/Read | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/Delete | Usuwa istniejące konto magazynu. |
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
  "description": "Provides permission to backup vault to manage disk snapshots.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7efff54f-a5b4-42b5-a1c5-5411624893ce",
  "name": "7efff54f-a5b4-42b5-a1c5-5411624893ce",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Compute/snapshots/delete",
        "Microsoft.Compute/snapshots/write",
        "Microsoft.Compute/snapshots/read",
        "Microsoft.Compute/snapshots/beginGetAccess/action",
        "Microsoft.Compute/snapshots/endGetAccess/action",
        "Microsoft.Compute/disks/beginGetAccess/action",
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/write",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Disk Snapshot Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="scheduler-job-collections-contributor"></a>Współautor kolekcji zadań usługi Scheduler

Umożliwia zarządzanie kolekcjami zadań harmonogramu, ale nie umożliwia uzyskiwania do nich dostępu.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie i zarządzanie alertem dotyczącego klasycznej metryki |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/Read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. Scheduler](resource-provider-operations.md#microsoftscheduler)/jobcollections/* | Tworzenie kolekcji zadań i zarządzanie nimi |
> | [Microsoft. Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
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

### <a name="services-hub-operator"></a>Operator centrum usług

Operator centrum usług pozwala wykonywać wszystkie operacje odczytu, zapisu i usuwania powiązane z łącznikami centrum usług. [Dowiedz się więcej](/services-hub/health/sh-connector-roles)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Read | Odczytuj role i przypisania ról |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/Read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft. resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Tworzenie wdrożenia i zarządzanie nim |
> | [Microsoft. ServicesHub](resource-provider-operations.md#microsoftserviceshub)/Connectors/Write | Utwórz lub zaktualizuj łącznik centrum usług |
> | [Microsoft. ServicesHub](resource-provider-operations.md#microsoftserviceshub)/Connectors/Read | Wyświetlanie łączników centrum usług |
> | [Microsoft. ServicesHub](resource-provider-operations.md#microsoftserviceshub)/Connectors/Delete | Usuń łączniki centrum usług |
> | [Microsoft. ServicesHub](resource-provider-operations.md#microsoftserviceshub)/Connectors/checkAssessmentEntitlement/Action | Wyświetla listę uprawnień do oceny dla danego obszaru roboczego centrum usług |
> | [Microsoft. ServicesHub](resource-provider-operations.md#microsoftserviceshub)/supportOfferingEntitlement/Read | Wyświetlanie uprawnień pomocy technicznej dla danego obszaru roboczego centrum usług |
> | [Microsoft. ServicesHub](resource-provider-operations.md#microsoftserviceshub)/Workspaces/Read | Wyświetl listę obszarów roboczych centrum usług dla danego użytkownika |
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
  "description": "Services Hub Operator allows you to perform all read, write, and deletion operations related to Services Hub Connectors.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/82200a5b-e217-47a5-b665-6d8765ee745b",
  "name": "82200a5b-e217-47a5-b665-6d8765ee745b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.ServicesHub/connectors/write",
        "Microsoft.ServicesHub/connectors/read",
        "Microsoft.ServicesHub/connectors/delete",
        "Microsoft.ServicesHub/connectors/checkAssessmentEntitlement/action",
        "Microsoft.ServicesHub/supportOfferingEntitlement/read",
        "Microsoft.ServicesHub/workspaces/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Services Hub Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="next-steps"></a>Następne kroki

- [Dopasuj dostawcę zasobów do usługi](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Role niestandardowe platformy Azure](custom-roles.md)
- [Uprawnienia w usłudze Azure Security Center](../security-center/security-center-permissions.md)
