---
title: Wbudowane rola platformy Azure — Azure RBAC
description: W tym artykule opisano wbudowane role platformy Azure dla kontroli dostępu na podstawie ról (RBAC) platformy Azure. Zawiera on listę akcji, notakcji, akcji danych i notDataActions.
services: active-directory
ms.service: role-based-access-control
ms.topic: reference
ms.workload: identity
author: rolyon
ms.author: rolyon
ms.date: 04/09/2021
ms.custom: generated
ms.openlocfilehash: c89e6ed98e0a71f530cefda4cc1f42a27996d805
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518511"
---
# <a name="azure-built-in-roles"></a>Wbudowane role platformy Azure

Kontrola dostępu oparta na rolach [(RBAC)](overview.md) platformy Azure ma kilka wbudowanych ról platformy Azure, które można przypisać do użytkowników, grup, podmiotów zabezpieczeń usługi i tożsamości zarządzanych. Przypisania ról to sposób kontrolowania dostępu do zasobów platformy Azure. Jeśli role wbudowane nie spełniają określonych potrzeb organizacji, możesz utworzyć własne role [niestandardowe platformy Azure.](custom-roles.md) Aby uzyskać informacje na temat sposobu przypisywania ról, zobacz [Kroki przypisywania roli platformy Azure.](role-assignments-steps.md)

W tym artykule wymieniono wbudowane role platformy Azure. Jeśli szukasz ról administratora dla usługi Azure Active Directory (Azure AD), zobacz Wbudowane role [usługi Azure AD.](../active-directory/roles/permissions-reference.md)

W poniższej tabeli przedstawiono krótki opis każdej wbudowanej roli. Kliknij nazwę roli, aby wyświetlić listę `Actions` , , i dla każdej `NotActions` `DataActions` `NotDataActions` roli. Aby uzyskać informacje o tym, co oznaczają te akcje i jak mają zastosowanie do płaszczyzn zarządzania i danych, zobacz Understand Azure role definitions (Informacje o [definicjach ról platformy Azure).](role-definitions.md)

## <a name="all"></a>Wszystko

> [!div class="mx-tableFixed"]
> | Rola wbudowana | Opis | ID (Identyfikator) |
> | --- | --- | --- |
> | **Ogólne** |  |  |
> | [Współautor](#contributor) | Daje pełny dostęp do zarządzania wszystkimi zasobami, ale nie pozwala na przypisywanie ról w kontroli dostępu na rolach platformy Azure, zarządzanie przypisaniami w Azure Blueprints i udostępnianie galerii obrazów. | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | [Właściciel](#owner) | Daje pełny dostęp do zarządzania wszystkimi zasobami, w tym możliwość przypisywania ról w funkcji RBAC platformy Azure. | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | [Czytelnik](#reader) | Wyświetl wszystko zasobów, ale nie pozwala na wprowadzanie żadnych zmian. | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | [Administrator dostępu użytkowników](#user-access-administrator) | Umożliwia zarządzanie dostępem użytkowników do zasobów platformy Azure. | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Środowisko obliczeniowe** |  |  |
> | [Współautor klasycznej maszyny wirtualnej](#classic-virtual-machine-contributor) | Umożliwia zarządzanie klasycznymi maszynami wirtualnymi, ale nie dostępem do nich, a nie siecią wirtualną lub kontem magazynu, z które są połączone. | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | [Logowanie administratora maszyny wirtualnej](#virtual-machine-administrator-login) | Wyświetlanie Virtual Machines portalu i logowanie się jako administrator | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | [Współautor maszyny wirtualnej](#virtual-machine-contributor) | Umożliwia zarządzanie maszynami wirtualnymi, ale nie dostęp do nich, a nie sieci wirtualnej ani konta magazynu, z które są połączone. | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | [Logowanie użytkownika maszyny wirtualnej](#virtual-machine-user-login) | Wyświetl Virtual Machines w portalu i zaloguj się jako zwykły użytkownik. | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Sieć** |  |  |
> | [Współautor punktu końcowego usługi CDN](#cdn-endpoint-contributor) | Może zarządzać punktami końcowymi usługi CDN, ale nie może udzielać dostępu innym użytkownikom. | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | [Czytnik punktów końcowych usługi CDN](#cdn-endpoint-reader) | Może wyświetlać punkty końcowe usługi CDN, ale nie może wprowadzać zmian. | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | [Współautor profilu usługi CDN](#cdn-profile-contributor) | Może zarządzać profilami cdn i ich punktami końcowymi, ale nie może udzielać dostępu innym użytkownikom. | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | [Czytnik profilów usługi CDN](#cdn-profile-reader) | Może wyświetlać profile cdn i ich punkty końcowe, ale nie może wprowadzać zmian. | 8f96442b-4075-438f-813d-ad51ab4019af |
> | [Współautor klasycznej sieci](#classic-network-contributor) | Umożliwia zarządzanie sieciami klasycznymi, ale nie umożliwia dostępu do nich. | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | [Współautor strefy DNS](#dns-zone-contributor) | Umożliwia zarządzanie strefami DNS i zestawami rekordów w Azure DNS, ale nie pozwala na kontrolowanie, kto ma do nich dostęp. | befefa01-2a29-4197-83a8-272ff33ce314 |
> | [Współautor sieci](#network-contributor) | Umożliwia zarządzanie sieciami, ale nie zapewnia do nich dostępu. | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | [Prywatna strefa DNS współautor strefy](#private-dns-zone-contributor) | Umożliwia zarządzanie zasobami prywatnej strefy DNS, ale nie sieciami wirtualnymi, z których są połączone. | b12aa53e-6015-4669-85d0-8515ebb3ae7f |
> | [Traffic Manager współautora](#traffic-manager-contributor) | Umożliwia zarządzanie profilami Traffic Manager, ale nie pozwala na kontrolowanie, kto ma do nich dostęp. | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Storage** |  |  |
> | [Współautor Avere](#avere-contributor) | Może tworzyć klastry Avere vFXT zarządzać nimi. | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | [Avere, operator](#avere-operator) | Używany przez klaster Avere vFXT do zarządzania klastrem | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | [Współautor kopii zapasowej](#backup-contributor) | Umożliwia zarządzanie usługą tworzenia kopii zapasowych, ale nie może tworzyć magazynów i udzielać dostępu innym osobom | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | [Operator kopii zapasowych](#backup-operator) | Umożliwia zarządzanie usługami tworzenia kopii zapasowych, z wyjątkiem usuwania kopii zapasowej, tworzenia magazynu i dawania dostępu innym osobom | 00c29273-979b-4161-815c-10b084fb9324 |
> | [Czytelnik kopii zapasowych](#backup-reader) | Może wyświetlać usługi tworzenia kopii zapasowych, ale nie może wprowadzać zmian | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | [Współautor klasycznego konta magazynu](#classic-storage-account-contributor) | Umożliwia zarządzanie klasycznymi kontami magazynu, ale nie umożliwia dostępu do nich. | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | [Rola klasycznego operatora klucza konta magazynu](#classic-storage-account-key-operator-service-role) | Operatorzy klasycznych kluczy konta magazynu mogą wyświetlić listę i ponownie wygenerować klucze na klasycznych kontach magazynu | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | [urządzenie Data Box współautora](#data-box-contributor) | Umożliwia zarządzanie wszystkim w usłudze urządzenie Data Box z wyjątkiem dawania dostępu innym osobom. | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | [urządzenie Data Box czytelnika](#data-box-reader) | Umożliwia zarządzanie usługą urządzenie Data Box z wyjątkiem tworzenia zamówienia lub edytowania szczegółów zamówienia i nadawania dostępu innym osobom. | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | [Data Lake Analytics Deweloper](#data-lake-analytics-developer) | Umożliwia przesyłanie i monitorowanie własnych zadań oraz zarządzanie nimi, ale nie pozwala na tworzenie ani Data Lake Analytics kont. | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | [Czytelnik i dostęp do danych](#reader-and-data-access) | Umożliwia wyświetlenie wszystkiego, ale nie pozwala na usunięcie ani utworzenie konta magazynu ani zawartego zasobu. Umożliwi to również dostęp do odczytu/zapisu do wszystkich danych zawartych na koncie magazynu za pośrednictwem dostępu do kluczy konta magazynu. | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | [Współautor konta magazynu](#storage-account-contributor) | Umożliwia zarządzanie kontami magazynu. Zapewnia dostęp do klucza konta, który może służyć do uzyskiwania dostępu do danych za pośrednictwem autoryzacji klucza wspólnego. | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | [Rola operatora klucza konta magazynu](#storage-account-key-operator-service-role) | Umożliwia wyświetlanie listy i ponowne generowanie kluczy dostępu do konta magazynu. | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | [Współautor danych obiektu blob usługi Storage](#storage-blob-data-contributor) | Odczytywanie, zapis i usuwanie kontenerów i obiektów blob usługi Azure Storage. Aby dowiedzieć się, które akcje są wymagane dla danej operacji na danych, zobacz Permissions for calling blob and queue data operations (Uprawnienia do wywoływania operacji na danych obiektów [blob i kolejek).](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations) | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | [Właściciel danych obiektu blob usługi Storage](#storage-blob-data-owner) | Zapewnia pełny dostęp do kontenerów i danych obiektów blob usługi Azure Storage, w tym przypisywanie kontroli dostępu POSIX. Aby dowiedzieć się, które akcje są wymagane dla danej operacji na danych, zobacz Permissions for calling blob and queue data operations (Uprawnienia do wywoływania operacji na danych obiektów [blob i kolejek).](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations) | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | [Czytelnik danych obiektu blob usługi Storage](#storage-blob-data-reader) | Odczyt i lista kontenerów i obiektów blob usługi Azure Storage. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz Uprawnienia do wywoływania operacji na danych obiektów [blob i kolejek](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | [Delegat usługi Storage Blob](#storage-blob-delegator) | Pobierz klucz delegowania użytkownika, którego następnie można użyć do utworzenia sygnatury dostępu współdzielonych dla kontenera lub obiektu blob podpisanego przy użyciu poświadczeń usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Tworzenie sygnatury dostępu współdzielonego delegowania użytkownika.](/rest/api/storageservices/create-user-delegation-sas) | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | [Współautor udziału SMB danych w pliku magazynu](#storage-file-data-smb-share-contributor) | Umożliwia odczyt, zapis i usuwanie dostępu do plików/katalogów w udziałach plików platformy Azure. Ta rola nie ma wbudowanego odpowiednika na serwerach plików z systemem Windows. | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | [Współautor udziału SMB danych w pliku magazynu z podwyższonym poziomem uprawnień](#storage-file-data-smb-share-elevated-contributor) | Umożliwia odczytywanie, zapis, usuwanie i modyfikowanie list ACL dla plików/katalogów w udziałach plików platformy Azure. Ta rola jest odpowiednikiem listy ACL zmian udziału plików na serwerach plików z systemem Windows. | a7264617-510b-434b-a828-9731dc254ea7 |
> | [Czytelnik udziału SMB danych w pliku magazynu](#storage-file-data-smb-share-reader) | Umożliwia dostęp do odczytu plików/katalogów w udziałach plików platformy Azure. Ta rola jest odpowiednikiem listy ACL udziałów plików odczytu na serwerach plików z systemem Windows. | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | [Współautor danych kolejki usługi Storage](#storage-queue-data-contributor) | Odczytywanie, zapis i usuwanie kolejek i komunikatów kolejek usługi Azure Storage. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz Uprawnienia do wywoływania operacji na danych obiektów [blob i kolejek](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | [Procesor komunikatów o danych kolejki usługi Storage](#storage-queue-data-message-processor) | Podgląd, pobieranie i usuwanie komunikatu z kolejki usługi Azure Storage. Aby dowiedzieć się, które akcje są wymagane dla danej operacji na danych, zobacz Permissions for calling blob and queue data operations (Uprawnienia do wywoływania operacji na danych obiektów [blob i kolejek).](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations) | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | [Nadawca komunikatów o danych kolejki usługi Storage](#storage-queue-data-message-sender) | Dodawanie komunikatów do kolejki usługi Azure Storage. Aby dowiedzieć się, które akcje są wymagane dla danej operacji na danych, zobacz Uprawnienia do wywoływania operacji na danych [obiektów blob i kolejek.](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations) | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | [Czytnik danych kolejki usługi Storage](#storage-queue-data-reader) | Odczytywanie i odczytywanie kolejek usługi Azure Storage oraz komunikatów kolejek. Aby dowiedzieć się, które akcje są wymagane dla danej operacji na danych, zobacz Uprawnienia do wywoływania operacji na danych [obiektów blob i kolejek.](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations) | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Sieć Web** |  |  |
> | [Azure Maps współautor danych](#azure-maps-data-contributor) | Przyznaje dostęp do odczytu, zapisu i usuwania danych powiązanych z mapami z konta usługi Azure Maps. | 8f5e0ce6-4f7b-4dcf-bddf-e6f48634a204 |
> | [Azure Maps czytnik danych](#azure-maps-data-reader) | Udziela dostępu do odczytu danych związanych z mapą z konta usługi Azure Maps. | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | [Azure Spring Cloud czytnik danych](#azure-spring-cloud-data-reader) | Zezwalaj na dostęp do odczytu Azure Spring Cloud Danych | b5537268-8956-4941-a8f0-646150406f0c |
> | [Współautor usługi wyszukiwania](#search-service-contributor) | Umożliwia zarządzanie usługami wyszukiwania, ale nie umożliwia dostępu do nich. | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | [SignalR AccessKey Reader](#signalr-accesskey-reader) | Odczytywanie SignalR Service dostępu | 04165923-9d83-45d5-8227-78b77b0a687e |
> | [SignalR App Server (wersja zapoznawcza)](#signalr-app-server-preview) | Umożliwia serwerowi aplikacji dostęp SignalR Service z opcjami uwierzytelniania usługi AAD. | 420fcaa2-552c-430f-98ca-3264be4806c7 |
> | [Współautor signalr](#signalr-contributor) | Tworzenie, odczytywanie, aktualizowanie i usuwanie zasobów usługi SignalR Service | 8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761 |
> | [Współautor bez serwera usługi SignalR (wersja zapoznawcza)](#signalr-serverless-contributor-preview) | Umożliwia usłudze dostępu do aplikacji w trybie bez serwera przy użyciu opcji uwierzytelniania usługi AAD. | fd53cd77-2268-407a-8f46-7e7863d0f521 |
> | [SignalR Service właścicielem (wersja zapoznawcza)](#signalr-service-owner-preview) | Pełny dostęp do Azure SignalR Service API REST | 7e4f1700-ea5a-4f59-8f37-079cfe29dce3 |
> | [SignalR Service Reader (wersja zapoznawcza)](#signalr-service-reader-preview) | Dostęp tylko do odczytu do Azure SignalR Service API REST | ddde6b66-c0df-4114-a159-3618637b3035 |
> | [Współautor planu sieci Web](#web-plan-contributor) | Umożliwia zarządzanie planami sieci Web witryn internetowych, ale nie umożliwia dostępu do nich. | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | [Współautor witryny internetowej](#website-contributor) | Umożliwia zarządzanie witrynami sieci Web (nie planami sieci Web), ale nie umożliwia dostępu do nich. | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Containers** |  |  |
> | [AcrDelete](#acrdelete) | acr delete | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | [AcrImageSigner](#acrimagesigner) | acr image signer | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | [AcrPull](#acrpull) | ściąganie acr | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | [AcrPush](#acrpush) | acr push | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | [AcrQuarantineReader](#acrquarantinereader) | Czytnik danych kwarantanny acr | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | [AcrQuarantineWriter](#acrquarantinewriter) | ACR kwarantanny zapisujący danych | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | [Azure Kubernetes Service roli administratora klastra](#azure-kubernetes-service-cluster-admin-role) | Lista akcji poświadczeń administratora klastra. | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | [Azure Kubernetes Service roli użytkownika klastra](#azure-kubernetes-service-cluster-user-role) | Lista akcji poświadczeń użytkownika klastra. | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | [Azure Kubernetes Service współautora](#azure-kubernetes-service-contributor-role) | Przyznaje dostęp do odczytu i zapisu Azure Kubernetes Service klastrach | ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8 |
> | [Azure Kubernetes Service RBAC Admin](#azure-kubernetes-service-rbac-admin) | Umożliwia zarządzanie wszystkimi zasobami w obszarze klastra/przestrzeni nazw, z wyjątkiem aktualizowania lub usuwania przydziałów zasobów i przestrzeni nazw. | 3498e952-d568-435e-9b2c-8d77e338d7f7 |
> | [Azure Kubernetes Service administratorem klastra kontroli RBAC](#azure-kubernetes-service-rbac-cluster-admin) | Umożliwia zarządzanie wszystkimi zasobami w klastrze. | b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b |
> | [Azure Kubernetes Service RBAC Reader](#azure-kubernetes-service-rbac-reader) | Zezwala na dostęp tylko do odczytu, aby wyświetlić większość obiektów w przestrzeni nazw. Nie zezwala na wyświetlanie ról ani powiązań ról. Ta rola nie zezwala na wyświetlanie wpisów tajnych, ponieważ odczytywanie zawartości wpisów tajnych umożliwia dostęp do poświadczeń konta usługi w przestrzeni nazw, co umożliwia dostęp do interfejsu API jako dowolnemu użytkownikowi ServiceAccount w przestrzeni nazw (w formie eskalacji uprawnień). Zastosowanie tej roli w zakresie klastra zapewni dostęp we wszystkich przestrzeniach nazw. | 7f6c6a51-bcf8-42ba-9220-52d62157d7db |
> | [Azure Kubernetes Service RBAC Writer](#azure-kubernetes-service-rbac-writer) | Umożliwia dostęp do odczytu/zapisu do większości obiektów w przestrzeni nazw. Ta rola nie zezwala na wyświetlanie ani modyfikowanie ról ani powiązań ról. Jednak ta rola umożliwia uzyskiwanie dostępu do wpisów tajnych i uruchamianie zasobników jako dowolnego konta usługi w przestrzeni nazw, więc może służyć do uzyskiwania poziomów dostępu do interfejsu API dowolnego konta usługi w przestrzeni nazw. Zastosowanie tej roli w zakresie klastra zapewni dostęp we wszystkich przestrzeniach nazw. | a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb |
> | **Bazy danych** |  |  |
> | [Cosmos DB roli czytelnika konta](#cosmos-db-account-reader-role) | Może odczytywać Azure Cosmos DB konta. Aby [uzyskać informacje na temat zarządzania kontami,](#documentdb-account-contributor) zobacz Współautor konta Azure Cosmos DB DocumentDB. | fbdf93bf-df7d-467e-a4d2-9458aaa1360c8 |
> | [Cosmos DB Operator](#cosmos-db-operator) | Umożliwia zarządzanie kontami Azure Cosmos DB, ale nie umożliwia uzyskiwania dostępu do danych w tych kontach. Uniemożliwia dostęp do kluczy konta i parametry połączenia. | 230815da-be43-4aae-9cb4-875f7bd000aaa |
> | [CosmosBackupOperator](#cosmosbackupoperator) | Może przesłać żądanie przywrócenia dla Cosmos DB bazy danych lub kontenera dla konta | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | [CosmosRestoreOperator](#cosmosrestoreoperator) | Może wykonać akcję przywracania dla Cosmos DB bazy danych z trybem ciągłej kopii zapasowej | 5432c526-bc82-444a-b7ba-57c5b0b5b34f |
> | [Współautor konta bazy danych DocumentDB](#documentdb-account-contributor) | Może zarządzać Azure Cosmos DB kontami. Azure Cosmos DB jest wcześniej znana jako DocumentDB. | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | [Redis Cache współautora](#redis-cache-contributor) | Umożliwia zarządzanie pamięciami podręcznmi Redis Cache, ale nie umożliwia dostępu do nich. | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | [Współautor bazy danych SQL](#sql-db-contributor) | Umożliwia zarządzanie bazami danych SQL, ale nie umożliwia dostępu do nich. Ponadto nie można zarządzać zasadami związanymi z zabezpieczeniami ani ich nadrzędnymi serwerami SQL. | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | [SQL Managed Instance współautora](#sql-managed-instance-contributor) | Umożliwia zarządzanie wystąpieniami zarządzanymi SQL i wymaganą konfiguracją sieci, ale nie umożliwia dawać dostępu innym osobom. | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | [Menedżer zabezpieczeń SQL](#sql-security-manager) | Umożliwia zarządzanie zasadami dotyczącymi zabezpieczeń serwerów SQL i baz danych, ale nie ma do nich dostępu. | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | [SQL Server współautora](#sql-server-contributor) | Umożliwia zarządzanie serwerami SQL i bazami danych, ale nie dostępem do nich, a nie ich zasadami związanymi z zabezpieczeniami. | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Analiza** |  |  |
> | [Azure Event Hubs właściciela danych](#azure-event-hubs-data-owner) | Umożliwia pełny dostęp do Azure Event Hubs zasobów. | f526a384-b230-433a-b45c-95f59c4a2dec |
> | [Azure Event Hubs Data Receiver](#azure-event-hubs-data-receiver) | Umożliwia odbieranie dostępu do Azure Event Hubs zasobów. | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | [Azure Event Hubs nadawcy danych](#azure-event-hubs-data-sender) | Umożliwia wysyłanie dostępu do Azure Event Hubs zasobów. | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | [Data Factory współautora](#data-factory-contributor) | Tworzenie fabryk danych, a także zasobów podrzędnych w tych fabrykach i zarządzanie nimi. | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | [Przeczyszczania danych](#data-purger) | Usuwanie danych prywatnych z obszaru roboczego usługi Log Analytics. | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | [Operator klastra usługi HDInsight](#hdinsight-cluster-operator) | Umożliwia odczytywanie i modyfikowanie konfiguracji klastra usługi HDInsight. | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | [Współautor usług HDInsight Domain Services](#hdinsight-domain-services-contributor) | Może odczytywać, tworzyć, modyfikować i usuwać operacje związane z usługami Domain Services wymagane przez usługę HDInsight pakiet Enterprise Security | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | [Współautor usługi Log Analytics](#log-analytics-contributor) | Współautor usługi Log Analytics może odczytywać wszystkie dane monitorowania i edytować ustawienia monitorowania. Edytowanie ustawień monitorowania obejmuje dodawanie rozszerzenia maszyny wirtualnej do maszyn wirtualnych; odczytywanie kluczy konta magazynu w celu skonfigurowania zbierania dzienników z usługi Azure Storage; tworzenie i konfigurowanie kont usługi Automation; dodawanie rozwiązań; i konfigurowanie diagnostyki platformy Azure dla wszystkich zasobów platformy Azure. | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | [Czytelnik usługi Log Analytics](#log-analytics-reader) | Czytelnik usługi Log Analytics może wyświetlać i wyszukiwać wszystkie dane monitorowania, a także wyświetlać ustawienia monitorowania, w tym wyświetlać konfigurację diagnostyki platformy Azure dla wszystkich zasobów platformy Azure. | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | [Purview Data Curator](#purview-data-curator) | Curator danych Microsoft.Purview może tworzyć, odczytywać, modyfikować i usuwać obiekty danych wykazu oraz ustanawiać relacje między obiektami. Ta rola jest w wersji zapoznawczej i może ulec zmianie. | 8a3c2885-9b38-4fd2-9d99-91af537c1347 |
> | [Purview Data Reader](#purview-data-reader) | Czytnik danych Microsoft.Purview może odczytywać obiekty danych wykazu. Ta rola jest w wersji zapoznawczej i może ulec zmianie. | ff100721-1b9d-43d8-af52-42b69c1272db |
> | [Administrator źródła danych programu Purview](#purview-data-source-administrator) | Administrator źródła danych Microsoft.Purview może zarządzać źródłami danych i skanowaniami danych. Ta rola jest w wersji zapoznawczej i może ulec zmianie. | 200bba9e-f0c8-430f-892b-6f0794863803 |
> | [Współautor rejestru schematów (wersja zapoznawcza)](#schema-registry-contributor-preview) | Odczyt, zapis i usuwanie grup i schematów rejestru schematów. | 5dffeca3-4936-4216-b2bc-10343a5abb25 |
> | [Czytelnik rejestru schematów (wersja zapoznawcza)](#schema-registry-reader-preview) | Odczytywanie i odczytywanie grup i schematów rejestru schematów. | 2c56ea50-c6b3-40a6-83c0-9d98858bc7d2 |
> | **Łańcuch bloków** |  |  |
> | [Dostęp do węzła członkowskiego łańcucha bloków (wersja zapoznawcza)](#blockchain-member-node-access-preview) | Umożliwia dostęp do węzłów członkowskich łańcucha bloków | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **AI i uczenie maszynowe** |  |  |
> | [Cognitive Services współautora](#cognitive-services-contributor) | Umożliwia tworzenie, odczytywanie, aktualizowanie i usuwanie kluczy usługi Cognitive Services. | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | [Cognitive Services Custom Vision współautora](#cognitive-services-custom-vision-contributor) | Pełny dostęp do projektu, w tym możliwość wyświetlania, tworzenia, edytowania i usuwania projektów. | c1ff6cc2-c111-46fe-8896-e0ef812ad9f3 |
> | [Cognitive Services Custom Vision wdrożenia](#cognitive-services-custom-vision-deployment) | Publikowanie, cofanie publikacji lub eksportowanie modeli. Wdrożenie może wyświetlić projekt, ale nie może go zaktualizować. | 5c4089e1-6d96-4d2f-b296-c1bc7137275f |
> | [Cognitive Services Custom Vision Labeler](#cognitive-services-custom-vision-labeler) | Wyświetlanie, edytowanie obrazów szkoleniowych oraz tworzenie, dodawanie, usuwanie lub usuwanie tagów obrazów. Etykiety mogą wyświetlać projekt, ale nie mogą aktualizować żadnych danych innych niż obrazy szkoleniowe i tagi. | 88424f51-ebe7-446f-bc41-7fa16989e96c |
> | [Cognitive Services Custom Vision czytelnika](#cognitive-services-custom-vision-reader) | Akcje tylko do odczytu w projekcie. Czytelnicy nie mogą tworzyć ani aktualizować projektu. | 93586559-c37d-4a6b-ba08-b9f0940c2d73 |
> | [Cognitive Services Custom Vision Instruktor](#cognitive-services-custom-vision-trainer) | Wyświetlaj, edytuj projekty i trenuj modele, w tym możliwość publikowania, cofania publikowania i eksportowania modeli. Instruktorzy nie mogą tworzyć ani usuwać projektu. | 0a5ae4ab-0d65-4eeb-be61-29fc9b54394b |
> | [Cognitive Services Czytnik danych (wersja zapoznawcza)](#cognitive-services-data-reader-preview) | Umożliwia odczytywanie Cognitive Services danych. | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | [Cognitive Services Doradca w zakresie metryk administratora](#cognitive-services-metrics-advisor-administrator) | Pełny dostęp do projektu, w tym do konfiguracji na poziomie systemu. | cb43c632-a144-4ec5-977c-e80c4affc34a |
> | [Cognitive Services QnA Maker edytora](#cognitive-services-qna-maker-editor) | Umożliwia tworzenie, edytowanie, importowanie i eksportowanie bazy wiedzy. Nie można opublikować ani usunąć bazy wiedzy. | f4cc2bf9-21be-47a1-bdf1-5c5804381025 |
> | [Cognitive Services QnA Maker Reader](#cognitive-services-qna-maker-reader) | Umożliwia odczytywanie i testowanie tylko bazy wiedzy. | 466ccd10-b268-4a11-b098-b4849f024126 |
> | [Cognitive Services użytkownika](#cognitive-services-user) | Umożliwia odczytywanie i wyświetlanie listy kluczy Cognitive Services. | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Internet rzeczy** |  |  |
> | [Administrator aktualizacji urządzenia](#device-update-administrator) | Zapewnia pełny dostęp do operacji zarządzania i zawartości | 02ca0879-e8e4-47a5-a61e-5c618b76e64a |
> | [Administrator zawartości aktualizacji urządzenia](#device-update-content-administrator) | Zapewnia pełny dostęp do operacji na zawartości | 0378884a-3af5-44ab-8323-f5b22f9f3c98 |
> | [Czytnik zawartości aktualizacji urządzenia](#device-update-content-reader) | Zapewnia dostęp do odczytu do operacji na zawartości, ale nie zezwala na wprowadzenie zmian | d1ee9a80-8b14-47f0-bdc2-f4a351625a7b |
> | [Administrator wdrożeń aktualizacji urządzeń](#device-update-deployments-administrator) | Zapewnia pełny dostęp do operacji zarządzania | e4237640-0e3d-4a46-8fda-70bc94856432 |
> | [Czytelnik wdrożeń aktualizacji urządzeń](#device-update-deployments-reader) | Zapewnia dostęp do odczytu do operacji zarządzania, ale nie zezwala na wprowadzenie zmian | 49e2f5d2-7741-4835-8efa-19e1fe35e47f |
> | [Czytnik aktualizacji urządzeń](#device-update-reader) | Zapewnia dostęp do odczytu do operacji zarządzania i zawartości, ale nie zezwala na wprowadzenie zmian | e9dba6fb-3d52-4cf0-bce3-f06ce71b9e0f |
> | **Rzeczywistość mieszana** |  |  |
> | [Remote Rendering administratora](#remote-rendering-administrator) | Zapewnia użytkownikowi możliwości konwersji, zarządzania sesjami, renderowania i diagnostyki dla Azure Remote Rendering | 3df8b902-2a6f-47c7-8cc5-360e9b272a7e |
> | [Remote Rendering klienta](#remote-rendering-client) | Zapewnia użytkownikom możliwości zarządzania sesją, renderowaniem i diagnostyką Azure Remote Rendering. | d39065c4-c120-43c9-ab0a-63eed9795f0a |
> | [Spatial Anchors współautora konta](#spatial-anchors-account-contributor) | Umożliwia zarządzanie zakotwiczeniami przestrzenni na koncie, ale nie ich usuwanie | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | [Spatial Anchors właściciela konta](#spatial-anchors-account-owner) | Umożliwia zarządzanie zakotwiczeniami przestrzenni na koncie, w tym ich usuwanie | 70bbe301-9835-447d-afdd-19eb3167307c |
> | [Spatial Anchors konta](#spatial-anchors-account-reader) | Umożliwia znajdowanie i odczytywanie właściwości kotwic przestrzennych na koncie | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Integracja** |  |  |
> | [API Management współautora usługi](#api-management-service-contributor) | Może zarządzać usługą i interfejsami API | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | [API Management operator usługi](#api-management-service-operator-role) | Może zarządzać usługą, ale nie interfejsami API | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | [API Management roli czytelnika usługi](#api-management-service-reader-role) | Dostęp tylko do odczytu do usługi i interfejsów API | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | [App Configuration właściciela danych](#app-configuration-data-owner) | Umożliwia pełny dostęp do App Configuration danych. | 5ae67d6-50cb-40e7-96ff-dc2bfa4b606b |
> | [App Configuration czytnik danych](#app-configuration-data-reader) | Umożliwia dostęp do odczytu App Configuration danych. | 516239f1-63e1-4d78-a4de-a74fb236a071 |
> | [Azure Service Bus właściciel danych](#azure-service-bus-data-owner) | Umożliwia pełny dostęp do Azure Service Bus zasobów. | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | [Azure Service Bus Data Receiver](#azure-service-bus-data-receiver) | Umożliwia odbieranie dostępu do Azure Service Bus zasobów. | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | [Azure Service Bus nadawcy danych](#azure-service-bus-data-sender) | Umożliwia wysyłanie dostępu do Azure Service Bus zasobów. | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | [Azure Stack właściciela rejestracji](#azure-stack-registration-owner) | Umożliwia zarządzanie Azure Stack rejestracji. | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | [Współautor eventGrid](#eventgrid-contributor) | Umożliwia zarządzanie operacjami eventGrid. | 1e241071-0855-49ea-94dc-649edcd759de |
> | [EventGrid EventSubscription Contributor](#eventgrid-eventsubscription-contributor) | Umożliwia zarządzanie operacjami subskrypcji zdarzeń usługi EventGrid. | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | [EventGrid EventSubscription Reader](#eventgrid-eventsubscription-reader) | Umożliwia odczytywanie subskrypcji zdarzeń usługi EventGrid. | 2414bbcf-6497-4faf-8c65-045460748405 |
> | [Współautor danych FHIR](#fhir-data-contributor) | Rola umożliwia użytkownikowi lub podmiotowi zabezpieczeń pełny dostęp do danych FHIR | 5a1fc7df-4bf1-4951-a576-89034ee01acd |
> | [Eksporter danych FHIR](#fhir-data-exporter) | Rola umożliwia użytkownikowi lub podmiotowi zabezpieczeń odczytywanie i eksportowanie danych FHIR | 3db33094-8700-4567-8da5-1501d4e7e843 |
> | [Czytnik danych FHIR](#fhir-data-reader) | Rola umożliwia użytkownikowi lub podmiotowi zabezpieczeń odczytywanie danych FHIR | 4c8d0bbc-75d3-4935-991f-5f3c56d81508 |
> | [FHIR Data Writer](#fhir-data-writer) | Rola umożliwia użytkownikowi lub podmiotowi zabezpieczeń odczytywanie i zapis danych FHIR | 3f88fce4-5892-4214-ae73-ba5294559913 |
> | [środowisko usługi integracji współautora](#integration-service-environment-contributor) | Umożliwia zarządzanie środowiskami usługi integracji, ale nie zapewnia dostępu do nich. | a41e2c5b-bd99-4a07-88f4-9bf657a760b8 |
> | [środowisko usługi integracji dewelopera](#integration-service-environment-developer) | Umożliwia deweloperom tworzenie i aktualizowanie przepływów pracy, kont integracji i połączeń interfejsu API w środowiskach usługi integracji. | c7aa55d3-1abb-444a-a5ca-5e51e485d6ec |
> | [Współautor konta systemów inteligentnych](#intelligent-systems-account-contributor) | Umożliwia zarządzanie kontami systemów inteligentnych, ale nie umożliwia dostępu do nich. | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | [Współautor aplikacji logiki](#logic-app-contributor) | Umożliwia zarządzanie aplikacjami logiki, ale nie zmienia dostępu do nich. | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | [Operator aplikacji logiki](#logic-app-operator) | Umożliwia odczytywanie, włączanie i wyłączanie aplikacji logiki, ale nie pozwala ich edytować ani aktualizować. | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Tożsamość** |  |  |
> | [Współautor tożsamości zarządzanej](#managed-identity-contributor) | Tworzenie, odczytywanie, aktualizowanie i usuwanie tożsamości przypisanej przez użytkownika | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | [Operator tożsamości zarządzanej](#managed-identity-operator) | Odczytywanie i przypisywanie tożsamości przypisanej przez użytkownika | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Bezpieczeństwo** |  |  |
> | [Współautor zaświadczenia](#attestation-contributor) | Może odczytywać lub usuwać wystąpienie dostawcy zaświadczenia | bbf86eb8-f7b4-4cce-96e4-18cddf81d86e |
> | [Czytnik zaświadczenia](#attestation-reader) | Może odczytywać właściwości dostawcy zaświadczenia | fd1bd22b-8476-40bc-a0bc-69b95687b9f3 |
> | [Azure Sentinel współautora automatyzacji](#azure-sentinel-automation-contributor) | Azure Sentinel współautor usługi Automation | f4c81013-99ee-4d62-a7ee-b3f1f648599a |
> | [Współautor usługi Azure Sentinel](#azure-sentinel-contributor) | Współautor usługi Azure Sentinel | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | [Czytelnik usługi Azure Sentinel](#azure-sentinel-reader) | Czytelnik usługi Azure Sentinel | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | [Obiekt odpowiadający usługi Azure Sentinel](#azure-sentinel-responder) | Obiekt odpowiadający usługi Azure Sentinel | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | [Key Vault administratora](#key-vault-administrator) | Wykonaj wszystkie operacje płaszczyzny danych w magazynie kluczy i wszystkich znajdujących się w nim obiektach, w tym certyfikatach, kluczach i wpisach tajnych. Nie można zarządzać zasobami magazynu kluczy ani zarządzać przypisaniami ról. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "Kontrola dostępu na podstawie ról" platformy Azure. | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
> | [Key Vault certyfikatów](#key-vault-certificates-officer) | Wykonaj dowolną akcję na certyfikatach magazynu kluczy, z wyjątkiem zarządzania uprawnieniami. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "Kontrola dostępu na podstawie ról" platformy Azure. | a4417e6f-fecd-4de8-b567-7b0420556985 |
> | [Key Vault współautora](#key-vault-contributor) | Zarządzanie magazynami kluczy, ale nie pozwala na przypisywanie ról w ramach kontroli dostępu na rolach platformy Azure i nie pozwala na dostęp do wpisów tajnych, kluczy ani certyfikatów. | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | [Key Vault Crypto Officer](#key-vault-crypto-officer) | Wykonaj dowolną akcję na kluczach magazynu kluczy, z wyjątkiem zarządzania uprawnieniami. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "Kontrola dostępu na podstawie ról" platformy Azure. | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
> | [Key Vault szyfrowania usługi kryptograficznych](#key-vault-crypto-service-encryption-user) | Odczytywanie metadanych kluczy i wykonywanie operacji opakowuj/odpakowuj. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "Kontrola dostępu na podstawie ról" platformy Azure. | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
> | [Key Vault użytkownik kryptograficzny](#key-vault-crypto-user) | Wykonywanie operacji kryptograficznych przy użyciu kluczy. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "Kontrola dostępu na podstawie ról" platformy Azure. | 12338af0-0e69-4776-bea7-57ae8d297424 |
> | [Key Vault czytelnika](#key-vault-reader) | Odczytywanie metadanych magazynów kluczy i ich certyfikatów, kluczy i wpisów tajnych. Nie można odczytać poufnych wartości, takich jak zawartość klucza tajnego lub materiał klucza. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "Kontrola dostępu na podstawie ról" platformy Azure. | 21090545-7ca7-4776-b22c-e363652d74d2 |
> | [Key Vault Secrets Officer](#key-vault-secrets-officer) | Wykonaj dowolną akcję na wpisach tajnych magazynu kluczy, z wyjątkiem zarządzania uprawnieniami. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "Kontrola dostępu na podstawie ról" platformy Azure. | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
> | [Key Vault wpisów tajnych](#key-vault-secrets-user) | Odczytywanie zawartości tajnej. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "Kontrola dostępu na podstawie ról" platformy Azure. | 4633458b-17de-408a-b874-0445c86b69e6 |
> | [Współautor zarządzanego modułu HSM](#managed-hsm-contributor) | Umożliwia zarządzanie zarządzanymi pulami modułów HSM, ale nie umożliwia dostępu do nich. | 18500a29-7fe2-46b2-a342-b16a415e101d |
> | [Administrator zabezpieczeń](#security-admin) | Wyświetlanie i aktualizowanie uprawnień dla Security Center. Takie same uprawnienia jak rola Czytelnik zabezpieczeń, a także może aktualizować zasady zabezpieczeń i odrzucać alerty i zalecenia. | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | [Współautor oceny zabezpieczeń](#security-assessment-contributor) | Umożliwia wypychanie ocen do Security Center | 612c2aa1-cb24-443b-ac28-3ab7272de6f5 |
> | [Menedżer zabezpieczeń (starsza wersja)](#security-manager-legacy) | Jest to starsza rola. Zamiast tego użyj konta Administrator zabezpieczeń. | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | [Czytelnik zabezpieczeń](#security-reader) | Wyświetlanie uprawnień dla Security Center. Może wyświetlać zalecenia, alerty, zasady zabezpieczeń i stany zabezpieczeń, ale nie może wprowadzać zmian. | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **DevOps** |  |  |
> | [Użytkownik laboratorium DevTest Labs](#devtest-labs-user) | Umożliwia łączenie, uruchamianie, ponowne uruchamianie i zamykanie maszyn wirtualnych w Azure DevTest Labs. | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | [Twórca laboratorium](#lab-creator) | Umożliwia tworzenie nowych laboratoriów w ramach kont laboratorium platformy Azure. | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Monitorowanie** |  |  |
> | [Application Insights współautora składnika](#application-insights-component-contributor) | Może zarządzać Application Insights składników | ae349356-3a1b-4a5e-921d-050484c6347e |
> | [Application Insights Snapshot Debugger](#application-insights-snapshot-debugger) | Daje użytkownikowi uprawnienia do wyświetlania i pobierania migawek debugowania zebranych za pomocą Application Insights Snapshot Debugger. Należy pamiętać, że te uprawnienia nie są uwzględnione w [rolach Właściciel](#owner) lub [Współautor.](#contributor) Podczas nadawania użytkownikom Application Insights Snapshot Debugger roli należy przyznać tę rolę bezpośrednio użytkownikowi. Rola nie jest rozpoznawana po dodaniu jej do roli niestandardowej. | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | [Współautor monitorowania](#monitoring-contributor) | Może odczytywać wszystkie dane monitorowania i edytować ustawienia monitorowania. Zobacz też [wprowadzenie do ról, uprawnień i zabezpieczeń za pomocą usługi Azure Monitor.](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles) | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | [Wydawca metryk monitorowania](#monitoring-metrics-publisher) | Umożliwia publikowanie metryk dotyczących zasobów platformy Azure | 3913510d-42f4-4e42-8a64-420c390055eb |
> | [Czytelnik monitorowania](#monitoring-reader) | Może odczytywać wszystkie dane monitorowania (metryki, dzienniki itp.). Zobacz też [wprowadzenie do ról, uprawnień](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles)i zabezpieczeń za pomocą Azure Monitor . | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | [Współautor skoroszytu](#workbook-contributor) | Może zapisywać udostępnione skoroszyty. | e8ddcd69-c73f-4f9f-9844-4100522f16ad |
> | [Czytnik skoroszytów](#workbook-reader) | Może odczytywać skoroszyty. | b279062a-9be3-42a0-92ae-8b3cf002ec4d |
> | **Zarządzanie i ład** |  |  |
> | [Operator zadania usługi Automation](#automation-job-operator) | Tworzenie zadań i zarządzanie nimi przy użyciu automatyzacji runbook. | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | [Operator usługi](#automation-operator) | Operatorzy automatyzacji mogą uruchamiać, zatrzymywać, wstrzymywać i wznawiać zadania | d3881f73-407a-4167-8283-e981cbba0404 |
> | [Automation Runbook Operator](#automation-runbook-operator) | Odczytywanie właściwości runbook — aby móc tworzyć zadania tego typu. | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | [Azure Arc użytkownika klastra Kubernetes z włączoną obsługą](#azure-arc-enabled-kubernetes-cluster-user-role) | Lista akcji poświadczeń użytkownika klastra. | 00493d72-78f6-4148-b6c5-d3ce8e4799dd |
> | [Azure Arc Kubernetes Admin](#azure-arc-kubernetes-admin) | Umożliwia zarządzanie wszystkimi zasobami w obszarze klastra/przestrzeni nazw, z wyjątkiem aktualizowania lub usuwania przydziałów zasobów i przestrzeni nazw. | dffb1e0c-446f-4dde-a09f-99eb5cc68b96 |
> | [Azure Arc administratorem klastra Kubernetes](#azure-arc-kubernetes-cluster-admin) | Umożliwia zarządzanie wszystkimi zasobami w klastrze. | 8393591c-06b9-48a2-a542-1bd6b377f6a2 |
> | [Azure Arc podglądu kubernetes](#azure-arc-kubernetes-viewer) | Umożliwia wyświetlanie wszystkich zasobów w klastrze/przestrzeni nazw, z wyjątkiem wpisów tajnych. | 63f0a09d-1495-4db4-a681-037d84835eb4 |
> | [Azure Arc Kubernetes Writer](#azure-arc-kubernetes-writer) | Umożliwia aktualizowanie wszystkiego w klastrze/przestrzeni nazw, z wyjątkiem ról (klastra) i powiązań ról (klastra). | 5b999177-9696-4545-85c7-50de3797e5a1 |
> | [Azure Connected Machine dołączania](#azure-connected-machine-onboarding) | Może dołączać połączone maszyny platformy Azure. | b64e21ea-ac4e-4cdf-9dc9-5b892992bee7 |
> | [Azure Connected Machine zasobów](#azure-connected-machine-resource-administrator) | Może odczytywać, zapisywać, usuwać i ponownie dołączać połączone maszyny platformy Azure. | cd570a14-e51a-42ad-bac8-bafd67325302 |
> | [Czytelnik rozliczeń](#billing-reader) | Zezwala na dostęp do odczytu danych rozliczeń | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | [Współautor strategii](#blueprint-contributor) | Może zarządzać definicjami strategii, ale nie może ich przypisywać. | 41077137-e803-4205-871c-5a86e6a753b4 |
> | [Operator strategii](#blueprint-operator) | Może przypisywać istniejące opublikowane strategie, ale nie może tworzyć nowych strategii. Należy pamiętać, że działa to tylko wtedy, gdy przypisanie jest wykonywane przy użyciu tożsamości zarządzanej przypisanej przez użytkownika. | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | [Współautor zarządzania kosztami](#cost-management-contributor) | Może wyświetlać koszty i zarządzać konfiguracją kosztów (np. budżetami, eksportami) | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | [Cost Management czytelnika](#cost-management-reader) | Może wyświetlać dane dotyczące kosztów i konfigurację (np. budżety, eksporty) | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | [Administrator ustawień hierarchii](#hierarchy-settings-administrator) | Umożliwia użytkownikom edytowanie i usuwanie ustawień hierarchii | 350f8d15-c687-4448-8ae1-157740a3936d |
> | [Klaster Kubernetes — Azure Arc dołączanie](#kubernetes-cluster---azure-arc-onboarding) | Definicja roli w celu autoryzowania dowolnego użytkownika/usługi do tworzenia zasobu connectedClusters | 34e09817-6cbe-4d01-b1a2-e0eac5743d41 |
> | [Rola współautora aplikacji zarządzanej](#managed-application-contributor-role) | Umożliwia tworzenie zasobów aplikacji zarządzanych. | 641177b8-a67a-45b9-a033-47bc880bb21e |
> | [Rola operatora aplikacji zarządzanej](#managed-application-operator-role) | Umożliwia odczytywanie i wykonywanie akcji na zasobach aplikacji zarządzanej | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | [Czytelnik aplikacji zarządzanych](#managed-applications-reader) | Umożliwia odczytywanie zasobów w aplikacji zarządzanej i żądanie dostępu JIT. | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | [Przypisanie rejestracji usług zarządzanych Usuń rolę](#managed-services-registration-assignment-delete-role) | Rola usuwania przypisania rejestracji usług zarządzanych umożliwia użytkownikom zarządzającym dzierżawą usunięcie przypisania rejestracji przypisanego do ich dzierżawy. | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | [Współautor grupy zarządzania](#management-group-contributor) | Rola współautora grupy zarządzania | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | [Czytelnik grupy zarządzania](#management-group-reader) | Rola czytelnika grupy zarządzania | ac63b705-f282-497d-ac71-919bf39d939d |
> | [New Relic współautora konta APM](#new-relic-apm-account-contributor) | Umożliwia zarządzanie kontami New Relic Application Performance Management i aplikacjami, ale nie ma do nich dostępu. | 5d28c62d-5b37-4476-8438-e587778df237 |
> | [Funkcja zapisu danych szczegółowych informacji o zasadach (wersja zapoznawcza)](#policy-insights-data-writer-preview) | Umożliwia dostęp do odczytu do zasad zasobów i dostęp do zapisu dla zdarzeń zasad składnika zasobów. | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | [Operator żądania limitu przydziału](#quota-request-operator) | Odczytywanie i tworzenie żądań przydziałów, uzyskiwanie stanu żądania limitu przydziału i tworzenie biletów pomocy technicznej. | 0e5f05e5-9ab9-446b-b98d-1e2157c94125 |
> | [Nabywca rezerwacji](#reservation-purchaser) | Umożliwia zakup rezerwacji | f7b75c60-3036-4b75-91c3-6b41c27c1689 |
> | [Współautor zasad zasobów](#resource-policy-contributor) | Użytkownicy z uprawnieniami do tworzenia/modyfikowania zasad zasobów, tworzenia biletu pomocy technicznej i odczytywania zasobów/hierarchii. | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | [Współautor usługi Site Recovery](#site-recovery-contributor) | Umożliwia zarządzanie usługą Site Recovery z wyjątkiem tworzenia magazynu i przypisywania ról | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | [Operator usługi Site Recovery](#site-recovery-operator) | Umożliwia trybu failover i powrotu po awarii, ale nie wykonuje innych Site Recovery zarządzania | 494ae006-db33-4328-bf46-533a6560a3ca |
> | [Czytelnik usługi Site Recovery](#site-recovery-reader) | Umożliwia wyświetlanie stanu Site Recovery, ale nie wykonywanie innych operacji zarządzania | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | [Współautor żądania pomocy technicznej](#support-request-contributor) | Umożliwia tworzenie żądań pomocy technicznej i zarządzanie nimi | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | [Współautor tagów](#tag-contributor) | Umożliwia zarządzanie tagami jednostek bez zapewniania dostępu do samych jednostek. | 4a9ae827-6dc8-4573-8ac7-8239d42aa03f |
> | **Inne** |  |  |
> | [Azure Digital Twins właściciela danych](#azure-digital-twins-data-owner) | Rola pełnego dostępu dla Digital Twins płaszczyzny danych | bcd981a7-7f74-457b-83e1-cceb9e632ffe |
> | [Azure Digital Twins czytnik danych](#azure-digital-twins-data-reader) | Rola tylko do odczytu Digital Twins właściwości płaszczyzny danych | d57506d4-4c8d-48b1-8587-93c323f6a5a3 |
> | [Współautor bizTalk](#biztalk-contributor) | Umożliwia zarządzanie usługami BizTalk, ale nie umożliwia dostępu do nich. | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | [Współautor grupy aplikacji wirtualizacji pulpitu](#desktop-virtualization-application-group-contributor) | Współautor grupy aplikacji wirtualizacji pulpitu. | 86240b0e-9422-4c43-887b-b61143f32ba8 |
> | [Czytelnik grupy aplikacji wirtualizacji pulpitu](#desktop-virtualization-application-group-reader) | Czytelnik grupy aplikacji wirtualizacji pulpitu. | aebf23d0-b568-4e86-b8f9-fe83a2c6ab55 |
> | [Współautor wirtualizacji pulpitu](#desktop-virtualization-contributor) | Współautor wirtualizacji pulpitu. | 082f0a83-3be5-4ba1-904c-961cca79b387 |
> | [Współautor puli hostów wirtualizacji pulpitu](#desktop-virtualization-host-pool-contributor) | Współautor puli hostów wirtualizacji pulpitu. | e307426c-f9b6-4e81-87de-d99efb3c32bc |
> | [Czytnik puli hostów wirtualizacji pulpitu](#desktop-virtualization-host-pool-reader) | Czytelnik puli hostów wirtualizacji pulpitu. | ceadfde2-b300-400a-ab7b-6143895aa822 |
> | [Czytnik wirtualizacji pulpitu](#desktop-virtualization-reader) | Czytelnik wirtualizacji pulpitu. | 49a72310-ab8d-41df-bbb0-79b649203868 |
> | [Operator hosta sesji wirtualizacji pulpitu](#desktop-virtualization-session-host-operator) | Operator hosta sesji wirtualizacji pulpitu. | 2ad6aaab-ead9-4eaa-8ac5-da422f562408 |
> | [Użytkownik wirtualizacji pulpitu](#desktop-virtualization-user) | Umożliwia użytkownikowi korzystanie z aplikacji w grupie aplikacji. | 1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63 |
> | [Operator sesji użytkownika wirtualizacji pulpitu](#desktop-virtualization-user-session-operator) | Operator sesji uesr wirtualizacji pulpitu. | ea4bfff8-7fb4-485a-aadd-d4129a0ffaa6 |
> | [Współautor obszaru roboczego wirtualizacji pulpitu](#desktop-virtualization-workspace-contributor) | Współautor obszaru roboczego wirtualizacji pulpitu. | 21efdde3-836f-432b-bf3d-3e8e734d4b2b |
> | [Czytelnik obszaru roboczego wirtualizacji pulpitu](#desktop-virtualization-workspace-reader) | Czytelnik obszaru roboczego wirtualizacji pulpitu. | 0fa44ee9-7a7d-466b-9bb2-2bf446b1204d |
> | [Czytnik kopii zapasowych dysków](#disk-backup-reader) | Zapewnia uprawnienia do magazynu kopii zapasowych w celu wykonania kopii zapasowej dysku. | 3e5e47e6-65f7-47ef-90b5-e5dd4d455f24 |
> | [Operator przywracania dysku](#disk-restore-operator) | Zapewnia uprawnienia do magazynu kopii zapasowych w celu wykonania przywracania dysku. | b50d9833-a0cb-478e-945f-707fcc997c13 |
> | [Współautor migawki dysku](#disk-snapshot-contributor) | Zapewnia uprawnienia do magazynu kopii zapasowych do zarządzania migawkami dysków. | 7efff54f-a5b4-42b5-a1c5-5411624893ce |
> | [Współautor kolekcji zadań harmonogramu](#scheduler-job-collections-contributor) | Umożliwia zarządzanie kolekcjami zadań harmonogramu, ale nie umożliwia dostępu do nich. | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | [Operator centrum usług](#services-hub-operator) | Operator centrum usług umożliwia wykonywanie wszystkich operacji odczytu, zapisu i usuwania związanych z łącznikami centrum usług. | 82200a5b-e217-47a5-b665-6d8765eee745b |


## <a name="general"></a>Ogólne


### <a name="contributor"></a>Współautor

Daje pełny dostęp do zarządzania wszystkimi zasobami, ale nie pozwala na przypisywanie ról w kontroli dostępu na rolach platformy Azure, zarządzanie przypisaniami w Azure Blueprints lub udostępnianie galerii obrazów. [Dowiedz się więcej](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | * | Tworzenie wszystkich typów zasobów i zarządzanie nimi |
> | **NotActions** |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/Delete | Usuwanie ról, przypisań zasad, definicji zasad i definicji zestawu zasad |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/Write | Tworzenie ról, przypisań ról, przypisań zasad, definicji zasad i definicji zestawu zasad |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/elevateAccess/Action | Przyznaje osobie wywołującej uprawnienia administratora dostępu użytkowników w zakresie dzierżawy |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/write | Tworzenie lub aktualizowanie przypisań strategii |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/delete | Usuwanie wszystkich przypisań strategii |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/galleries/share/action | Udostępnia galerię w różnych zakresach |
> | **DataActions (Działania danych)** |  |
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

Daje pełny dostęp do zarządzania wszystkimi zasobami, w tym możliwość przypisywania ról w funkcji RBAC platformy Azure. [Dowiedz się więcej](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | * | Tworzenie wszystkich typów zasobów i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

Wyświetl wszystko zasobów, ale nie pozwala na wprowadzanie żadnych zmian. [Dowiedz się więcej](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | */odczyt | Odczytywanie zasobów wszystkich typów, z wyjątkiem wpisów tajnych. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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
> | */odczyt | Odczytywanie zasobów wszystkich typów, z wyjątkiem wpisów tajnych. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/* | Zarządzanie autoryzacją |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

Umożliwia zarządzanie klasycznymi maszynami wirtualnymi, ale nie dostępem do nich, a nie siecią wirtualną lub kontem magazynu, z które są połączone.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/domainNames/* | Tworzenie klasycznych nazw domen obliczeniowych i zarządzanie nimi |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/* | Tworzenie maszyn wirtualnych i zarządzanie nimi |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/networkSecurityGroups/join/action |  |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/link/action | Łączenie zastrzeżonego adresu IP |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/read | Pobiera zastrzeżone adresy IP |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/join/action | Dołącza do sieci wirtualnej. |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/read | Pobierz sieć wirtualną. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/disks/read | Zwraca dysk konta magazynu. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/images/read | Zwraca obraz konta magazynu. (Przestarzałe. Użyj "Microsoft.ClassicStorage/storageAccounts/vmImages") |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | Wyświetla listę kluczy dostępu dla kont magazynu. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/read | Zwróć konto magazynu z danym kontem. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

Wyświetlanie Virtual Machines portalu i logowanie jako administrator [Dowiedz się więcej](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | Pobiera definicję publicznego adresu IP. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Uzyskiwanie definicji sieci wirtualnej |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | Pobiera definicję równoważenia obciążenia |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | Pobiera definicję interfejsu sieciowego.  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/login/action | Zaloguj się do maszyny wirtualnej jako zwykły użytkownik |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/loginAsAdmin/action | Zaloguj się do maszyny wirtualnej przy użyciu uprawnień administratora systemu Windows lub użytkownika głównego systemu Linux |
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

Umożliwia zarządzanie maszynami wirtualnymi, ale nie dostęp do nich, a nie sieci wirtualnej lub konta magazynu, z które są połączone. [Dowiedz się więcej](../virtual-machines/linux/tutorial-govern-resources.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/* | Tworzenie obliczeniowych zestawów dostępności i zarządzanie nimi |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/locations/* | Tworzenie lokalizacji obliczeniowych i zarządzanie nimi |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/* | Wykonaj wszystkie akcje maszyny wirtualnej, w tym tworzenie, aktualizowanie, usuwanie, uruchamianie, ponowne uruchamianie i wyłączanie maszyn wirtualnych. Wykonywanie wstępnie zdefiniowanych skryptów na maszynach wirtualnych. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachineScaleSets/* | Tworzenie zestawów skalowania maszyn wirtualnych i zarządzanie nimi |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/write | Tworzy nowy dysk lub aktualizuje istniejący dysk |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/read | Uzyskiwanie właściwości dysku |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/delete | Usuwa dysk |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/schedules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/applicationGateways/backendAddressPools/join/action | Dołącza pulę adresów zaplecza bramy aplikacji. Nie ma alertu. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/backendAddressPools/join/action | Dołącza pulę adresów zaplecza równoważenia obciążenia. Nie ma alertu. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatPools/join/action | Dołącza pulę nat dla ruchu przychodzącego do równoważenia obciążenia. Nie ma alertu. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatRules/join/action | Dołącza regułę nat dla ruchu przychodzącego do równoważenia obciążenia. Nie ma alertu. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/probes/join/action | Umożliwia korzystanie z sond usługi równoważenia obciążenia. Na przykład za pomocą tej właściwości healthProbe uprawnienia zestawu skalowania maszyn wirtualnych może odwoływać się do sondy. Nie ma alertów. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | Pobiera definicję równoważenia obciążenia |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/locations/* | Tworzenie lokalizacji sieciowych i zarządzanie nimi |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/* | Tworzenie interfejsów sieciowych i zarządzanie nimi |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | Dołącza do sieciowej grupy zabezpieczeń. Nie ma alertu. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/read | Pobiera definicję sieciowej grupy zabezpieczeń |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/join/action | Dołącza publiczny adres IP. Nie ma alertu. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | Pobiera definicję publicznego adresu IP. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Uzyskiwanie definicji sieci wirtualnej |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | Dołącza do sieci wirtualnej. Nie ma alertu. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/write | Tworzenie intencji ochrony kopii zapasowej |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | Zwraca szczegóły obiektu chronionego elementu |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/write | Tworzenie chronionego elementu kopii zapasowej |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | Zwraca wszystkie zasady ochrony |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/write | Tworzy zasady ochrony |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | Operacja Get Vault pobiera obiekt reprezentujący zasób platformy Azure typu "vault" |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Zwraca szczegóły użycia magazynu usługi Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/write | Operacja tworzenia magazynu tworzy zasób platformy Azure typu "vault" |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.SqlVirtualMachine](resource-provider-operations.md#microsoftsqlvirtualmachine)/* |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | Zwraca klucze dostępu dla określonego konta magazynu. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | Pobiera definicję publicznego adresu IP. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Uzyskiwanie definicji sieci wirtualnej |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | Pobiera definicję równoważenia obciążenia |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | Pobiera definicję interfejsu sieciowego.  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/login/action | Zaloguj się do maszyny wirtualnej jako zwykły użytkownik |
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

Może zarządzać punktami końcowymi usługi CDN, ale nie może udzielać dostępu innym użytkownikom.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/endpoints/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie tym alertem |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
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

### <a name="cdn-endpoint-reader"></a>Czytnik punktów końcowych usługi CDN

Może wyświetlać punkty końcowe usługi CDN, ale nie może wprowadzać zmian.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/endpoints/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie tym alertem |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

### <a name="cdn-profile-contributor"></a>Współautor profilu usługi CDN

Może zarządzać profilami cdn i ich punktami końcowymi, ale nie może udzielać dostępu innym użytkownikom. [Dowiedz się więcej](../cdn/cdn-app-dev-net.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
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

### <a name="cdn-profile-reader"></a>Czytnik profilów cdn

Może wyświetlać profile cdn i ich punkty końcowe, ale nie może wprowadzać zmian.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie tym alertem |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

Umożliwia zarządzanie sieciami klasycznymi, ale nie umożliwia dostępu do nich. [Dowiedz się więcej](../virtual-network/virtual-network-manage-peering.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/* | Tworzenie sieci klasycznych i zarządzanie nimi |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

Umożliwia zarządzanie strefami DNS i zestawami rekordów w Azure DNS, ale nie pozwala na kontrolowanie, kto ma do nich dostęp. [Dowiedz się więcej](../dns/dns-protect-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie tym alertem |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/dnsZones/* | Tworzenie stref i rekordów DNS i zarządzanie nimi |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
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

Umożliwia zarządzanie sieciami, ale nie zapewnia do nich dostępu.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/* | Tworzenie sieci i zarządzanie nimi |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

### <a name="private-dns-zone-contributor"></a>Prywatna strefa DNS współautor strefy

Umożliwia zarządzanie zasobami prywatnej strefy DNS, ale nie sieciami wirtualnymi, z których są połączone. [Dowiedz się więcej](../dns/dns-protect-private-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/privateDnsZones/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationResults/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationStatuses/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Uzyskiwanie definicji sieci wirtualnej |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/join/action | Dołącza do sieci wirtualnej. Nie ma alertu. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

### <a name="traffic-manager-contributor"></a>Traffic Manager współautora

Umożliwia zarządzanie profilami Traffic Manager, ale nie pozwala na kontrolowanie, kto ma do nich dostęp.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/trafficManagerProfiles/* |  |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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


### <a name="avere-contributor"></a>Współautor Avere

Może tworzyć klastry Avere vFXT i zarządzać nimi. [Dowiedz się więcej](../avere-vfxt/avere-vfxt-deploy-plan.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/*/read |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/* |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/proximityPlacementGroups/* |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/* |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/*/read |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Uzyskiwanie definicji sieci wirtualnej |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/read | Pobiera definicję podsieci sieci wirtualnej |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | Dołącza do sieci wirtualnej. Nie ma alertu. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Dołącza zasoby, takie jak konto magazynu lub baza danych SQL, do podsieci. Nie ma alertów. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | Dołącza do sieciowej grupy zabezpieczeń. Nie ma alertu. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie tym alertem |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/*/read |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/* | Tworzenie kont magazynu i zarządzanie nimi |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/resources/read | Pobiera zasoby dla grupy zasobów. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/delete | Zwraca wynik usunięcia obiektu blob |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | Zwraca obiekt blob lub listę obiektów blob |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | Zwraca wynik zapisu obiektu blob |
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

### <a name="avere-operator"></a>Avere Operator

Używany przez klaster Avere vFXT do zarządzania klastrem [Dowiedz się więcej](../avere-vfxt/avere-vfxt-manage-cluster.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/read | Uzyskiwanie właściwości maszyny wirtualnej |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | Pobiera definicję interfejsu sieciowego.  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/write | Tworzy interfejs sieciowy lub aktualizuje istniejący interfejs sieciowy.  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Uzyskiwanie definicji sieci wirtualnej |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/read | Pobiera definicję podsieci sieci wirtualnej |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | Dołącza do sieci wirtualnej. Nie ma alertu. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | Dołącza do sieciowej grupy zabezpieczeń. Nie ma alertu. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/delete | Zwraca wynik usunięcia kontenera |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/read | Zwraca listę kontenerów |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/write | Zwraca wynik kontenera obiektów blob put |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/delete | Zwraca wynik usunięcia obiektu blob |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | Zwraca obiekt blob lub listę obiektów blob |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | Zwraca wynik zapisu obiektu blob |
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

Umożliwia zarządzanie usługą tworzenia kopii zapasowych, ale nie może tworzyć magazynów i udzielać dostępu innym [Osobom Dowiedz się więcej](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Uzyskiwanie definicji sieci wirtualnej |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/* | Zarządzanie wynikami operacji zarządzania kopiami zapasowymi |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/* | Tworzenie kontenerów kopii zapasowych i zarządzanie nimi w sieciach szkieletowych kopii zapasowych magazynu usługi Recovery Services |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/refreshContainers/action | Odświeża listę kontenerów |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/* | Tworzenie zadań tworzenia kopii zapasowych i zarządzanie nimi |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | Eksportowanie zadań |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/* | Tworzenie wyników operacji zarządzania kopiami zapasowymi i zarządzanie nimi |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/* | Tworzenie zasad tworzenia kopii zapasowych i zarządzanie nimi |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectableItems/* | Tworzenie elementów, których kopię zapasową można utworzyć, i zarządzanie nimi |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/* | Tworzenie elementów kopii zapasowej i zarządzanie nimi |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/* | Tworzenie kontenerów z elementami kopii zapasowej i zarządzanie nimi |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupSecurityPIN/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | Zwraca podsumowania dotyczące elementów chronionych i serwerów chronionych dla usługi Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/* | Tworzenie certyfikatów związanych z tworzeniem kopii zapasowych w magazynie usługi Recovery Services i zarządzanie nimi |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/* | Tworzenie informacji rozszerzonych związanych z magazynem i zarządzanie nimi |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Pobiera alerty dla magazynu usługi Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | Operacja Get Vault pobiera obiekt reprezentujący zasób platformy Azure typu "vault" |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/* | Tworzenie zarejestrowanych tożsamości i zarządzanie nimi |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/* | Tworzenie magazynu usługi Recovery Services i zarządzanie jego użyciem |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupconfig/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupValidateOperation/action | Operacja walidacji dla chronionego elementu |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/write | Operacja tworzenia magazynu tworzy zasób platformy Azure typu "vault" |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | Zwraca stan operacji tworzenia kopii zapasowej dla magazynu usługi Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/read | Zwraca wszystkie serwery zarządzania kopiami zapasowymi zarejestrowane w magazynie. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectableContainers/read | Pobierz wszystkie kontenery, które można chronić |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | Sprawdzanie stanu kopii zapasowej magazynów usługi Recovery Services |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupPreValidateProtection/action |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | Weryfikowanie funkcji |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | Rozwiązuje alert. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | Operacja zwraca listę operacji dla dostawcy zasobów |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | Pobiera stan operacji dla danej operacji |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | Lista wszystkich intencji ochrony kopii zapasowych |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

Umożliwia zarządzanie usługami tworzenia kopii zapasowych z wyjątkiem usuwania kopii zapasowej, tworzenia magazynu i dawania dostępu innym [osobom Dowiedz się więcej](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Uzyskiwanie definicji sieci wirtualnej |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/read | Zwraca stan operacji |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/operationResults/read | Pobiera wynik operacji wykonywanej na kontenerze ochrony. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Wykonuje kopię zapasową chronionego elementu. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Pobiera wynik operacji wykonywanej na chronionych elementach. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Zwraca stan Operacja wykonana na chronionych elementach. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | Zwraca szczegóły obiektu chronionego elementu |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Aprowizuj natychmiastowe odzyskiwanie elementów dla chronionego elementu |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/accessToken/action | Pobierz dane AccessToken dla przywracania między regionami. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Uzyskaj punkty odzyskiwania dla chronionych elementów. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Przywróć punkty odzyskiwania dla chronionych elementów. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Odwoływanie natychmiastowego odzyskiwania elementów dla chronionego elementu |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/write | Tworzenie chronionego elementu kopii zapasowej |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/read | Zwraca wszystkie zarejestrowane kontenery |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/refreshContainers/action | Odświeża listę kontenerów |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/* | Tworzenie zadań tworzenia kopii zapasowych i zarządzanie nimi |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | Eksportowanie zadań |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/* | Tworzenie wyników operacji zarządzania kopiami zapasowymi i zarządzanie nimi |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operationResults/read | Pobierz wyniki operacji zasad. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | Zwraca wszystkie zasady ochrony |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectableItems/* | Tworzenie elementów, których kopię zapasową można utworzyć, i zarządzanie nimi |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/read | Zwraca listę wszystkich chronionych elementów. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/read | Zwraca wszystkie kontenery należące do subskrypcji |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | Zwraca podsumowania dotyczące elementów chronionych i serwerów chronionych dla usługi Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/write | Operacja aktualizacji certyfikatu zasobu aktualizuje certyfikat poświadczeń zasobu/magazynu. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | Operacja Pobierz informacje rozszerzone pobiera informacje rozszerzone obiektu reprezentujące zasób platformy Azure typu ?vault? |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/write | Operacja Pobierz informacje rozszerzone pobiera informacje rozszerzone obiektu reprezentujące zasób platformy Azure typu ?vault? |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Pobiera alerty dla magazynu usługi Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | Operacja Get Vault pobiera obiekt reprezentujący zasób platformy Azure typu "vault" |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | Operacji Get Operation Results można użyć do uzyskania stanu operacji i wyniku operacji przesłanej asynchronicznie |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | Można użyć operacji Pobierz kontenery, aby zarejestrować kontenery dla zasobu. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/write | Operacja rejestrowania kontenera usługi może służyć do rejestrowania kontenera w usłudze odzyskiwania. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Zwraca szczegóły użycia magazynu usługi Recovery Services. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupValidateOperation/action | Operacja walidacji dla chronionego elementu |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | Zwraca stan operacji tworzenia kopii zapasowej dla magazynu usługi Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operations/read | Pobierz stan operacji zasad. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/write | Tworzy zarejestrowany kontener |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/inquire/action | Zapytanie dotyczące obciążeń w kontenerze |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/read | Zwraca wszystkie serwery zarządzania kopiami zapasowymi zarejestrowane w magazynie. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/write | Tworzenie intencji ochrony kopii zapasowej |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/read | Uzyskiwanie intencji ochrony kopii zapasowej |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectableContainers/read | Pobierz wszystkie kontenery, które można chronić |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/items/read | Pobierz wszystkie elementy w kontenerze |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | Sprawdzanie stanu kopii zapasowej magazynów usługi Recovery Services |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupPreValidateProtection/action |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | Weryfikowanie funkcji |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupAadProperties/read | Pobierz właściwości usługi AAD do uwierzytelniania w trzecim regionie dla przywracania między regionami. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrJobs/action | Lista zadań przywracania między regionami w regionie pomocniczym dla magazynu usługi Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrJob/action | Pobierz szczegóły zadania przywracania między regionami w regionie pomocniczym dla magazynu usługi Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrossRegionRestore/action | Wyzwalanie przywracania między regionami. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrOperationResults/read | Zwraca wynik operacji CRR dla magazynu usługi Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrOperationsStatus/read | Zwraca stan operacji CRR dla magazynu usługi Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | Rozwiązuje alert. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | Operacja zwraca listę operacji dla dostawcy zasobów |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | Pobiera stan operacji dla danej operacji |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | Lista wszystkich intencji ochrony kopii zapasowych |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

### <a name="backup-reader"></a>Czytelnik kopii zapasowych

Może wyświetlać usługi tworzenia kopii zapasowych, ale nie może wprowadzać zmian [Dowiedz się więcej](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp to wewnętrzna operacja używana przez usługę |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/read | Zwraca stan operacji |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/operationResults/read | Pobiera wynik operacji wykonanej na kontenerze ochrony. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Pobiera wynik operacji wykonywanej na chronionych elementach. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Zwraca stan Operacji wykonanej na chronionych elementach. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | Zwraca szczegóły obiektu chronionego elementu |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Uzyskaj punkty odzyskiwania dla chronionych elementów. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/read | Zwraca wszystkie zarejestrowane kontenery |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/operationResults/read | Zwraca wynik operacji zadania. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/read | Zwraca wszystkie obiekty zadania |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | Eksportowanie zadań |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/read | Zwraca wynik operacji tworzenia kopii zapasowej dla magazynu usługi Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operationResults/read | Pobierz wyniki operacji zasad. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | Zwraca wszystkie zasady ochrony |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/read | Zwraca listę wszystkich chronionych elementów. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/read | Zwraca wszystkie kontenery należące do subskrypcji |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | Zwraca podsumowania dotyczące elementów chronionych i serwerów chronionych dla usługi Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | Operacja Pobierz informacje rozszerzone pobiera informacje rozszerzone obiektu reprezentujące zasób platformy Azure typu ?vault? |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Pobiera alerty dla magazynu usługi Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | Operacja Get Vault pobiera obiekt reprezentujący zasób platformy Azure typu "vault" |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | Operacji Get Operation Results można użyć do uzyskania stanu operacji i wyniku operacji przesłanej asynchronicznie |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | Można użyć operacji Pobierz kontenery, aby zarejestrować kontenery dla zasobu. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/read | Zwraca konfigurację magazynu dla magazynu usługi Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupconfig/read | Zwraca konfigurację magazynu usługi Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | Zwraca stan operacji tworzenia kopii zapasowej dla magazynu usługi Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operations/read | Pobierz stan operacji zasad. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/read | Zwraca wszystkie serwery zarządzania kopiami zapasowymi zarejestrowane w magazynie. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/read | Uzyskiwanie intencji ochrony kopii zapasowej |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/items/read | Pobierz wszystkie elementy w kontenerze |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | Sprawdzanie stanu kopii zapasowej magazynów usługi Recovery Services |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | Rozwiązuje alert. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | Operacja zwraca listę operacji dla dostawcy zasobów |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | Pobiera stan operacji dla danej operacji |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | Lista wszystkich intencji ochrony kopii zapasowych |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Zwraca szczegóły użycia magazynu usługi Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | Weryfikowanie funkcji |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

Umożliwia zarządzanie klasycznymi kontami magazynu, ale nie umożliwia dostępu do nich.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/* | Tworzenie kont magazynu i zarządzanie nimi |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie tym alertem |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
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

### <a name="classic-storage-account-key-operator-service-role"></a>Rola klasycznego operatora klucza konta magazynu

Operatorzy klasycznych kluczy konta magazynu mogą wyświetlić listę i ponownie wygenerować klucze na klasycznych kontach magazynu [Dowiedz się więcej](../key-vault/secrets/overview-storage-keys.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listkeys/action | Wyświetla listę kluczy dostępu dla kont magazynu. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/regeneratekey/action | Ponownie generuje istniejące klucze dostępu dla konta magazynu. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

### <a name="data-box-contributor"></a>urządzenie Data Box współautora

Umożliwia zarządzanie wszystkim w usłudze urządzenie Data Box z wyjątkiem dawania dostępu innym osobom. [Dowiedz się więcej](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

### <a name="data-box-reader"></a>urządzenie Data Box czytelnika

Umożliwia zarządzanie usługą urządzenie Data Box z wyjątkiem tworzenia zamówienia lub edytowania szczegółów zamówienia i nadawania dostępu innym osobom. [Dowiedz się więcej](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/*/read |  |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/jobs/listsecrets/action |  |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/jobs/listcredentials/action | Wyświetla niezaszyfrowane poświadczenia powiązane z zamówieniem. |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/availableSkus/action | Ta metoda zwraca listę dostępnych jednostki SKU. |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/validateInputs/action | Ta metoda obejmuje wszystkie typy weryfikacji. |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/regionConfiguration/action | Ta metoda zwraca konfiguracje dla regionu. |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/validateAddress/action | Weryfikuje adres wysyłkowy i udostępnia alternatywne adresy, jeśli są. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
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

### <a name="data-lake-analytics-developer"></a>Data Lake Analytics Deweloper

Umożliwia przesyłanie i monitorowanie własnych zadań oraz zarządzanie nimi, ale nie pozwala na tworzenie ani Data Lake Analytics kont. [Dowiedz się więcej](../data-lake-analytics/data-lake-analytics-manage-use-portal.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.BigAnalytics/accounts/* |  |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/Delete | Usuń konto DataLakeAnalytics. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/TakeOwnership/action | Przyznaj uprawnienia do anulowania zadań przesłanych przez innych użytkowników. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/Write | Utwórz lub zaktualizuj konto usługi DataLakeAnalytics. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/dataLakeStoreAccounts/Write | Utwórz lub zaktualizuj połączone konto Usługi DataLakeStore dla konta usługi DataLakeAnalytics. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/dataLakeStoreAccounts/Delete | Odłącz konto DataLakeStore od konta DataLakeAnalytics. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/storageAccounts/Write | Utwórz lub zaktualizuj połączone konto usługi Storage dla konta usługi DataLakeAnalytics. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/storageAccounts/Delete | Odłącz konto usługi Storage od konta usługi DataLakeAnalytics. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/firewallRules/Write | Utwórz lub zaktualizuj regułę zapory. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/firewallRules/Delete | Usuń regułę zapory. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/computePolicies/Write | Tworzenie lub aktualizowanie zasad obliczeniowych. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/computePolicies/Delete | Usuwanie zasad obliczeniowych. |
> | **DataActions** |  |
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

### <a name="reader-and-data-access"></a>Czytelnik i dostęp do danych

Umożliwia wyświetlenie wszystkiego, ale nie pozwala na usunięcie ani utworzenie konta magazynu ani zawartego zasobu. Umożliwi to również dostęp do odczytu/zapisu do wszystkich danych zawartych na koncie magazynu za pośrednictwem dostępu do kluczy konta magazynu.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | Zwraca klucze dostępu dla określonego konta magazynu. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/ListAccountSas/action | Zwraca token SAS konta dla określonego konta magazynu. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

Umożliwia zarządzanie kontami magazynu. Zapewnia dostęp do klucza konta, który może służyć do uzyskiwania dostępu do danych za pośrednictwem autoryzacji klucza wspólnego. [Dowiedz się więcej](../storage/common/storage-auth-aad.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Tworzy, aktualizuje lub odczytuje ustawienie diagnostyczne dla Analysis Server |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Dołącza zasoby, takie jak konto magazynu lub baza danych SQL, do podsieci. Nie ma alertów. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/* | Tworzenie kont magazynu i zarządzanie nimi |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
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

### <a name="storage-account-key-operator-service-role"></a>Rola operatora klucza konta magazynu

Umożliwia wyświetlanie listy i ponowne generowanie kluczy dostępu do konta magazynu. [Dowiedz się więcej](../storage/common/storage-account-keys-manage.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/action | Zwraca klucze dostępu dla określonego konta magazynu. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/regeneratekey/action | Ponownie generuje klucze dostępu dla określonego konta magazynu. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

Odczytywanie, zapis i usuwanie kontenerów i obiektów blob usługi Azure Storage. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz Uprawnienia do wywoływania operacji na danych obiektów [blob i kolejek](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Dowiedz się więcej](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/delete | Usuwanie kontenera. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/read | Zwróć kontener lub listę kontenerów. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/write | Modyfikowanie metadanych lub właściwości kontenera. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Zwraca klucz delegowania użytkownika dla Blob service. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/delete | Usuwanie obiektu blob. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | Zwracanie obiektu blob lub listy obiektów blob. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | Zapis w obiekcie blob. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/move/action | Przenosi obiekt blob z jednej ścieżki do innej |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/add/action | Zwraca wynik dodawania zawartości obiektu blob |
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

Zapewnia pełny dostęp do kontenerów i danych obiektów blob usługi Azure Storage, w tym do przypisywania kontroli dostępu POSIX. Aby dowiedzieć się, które akcje są wymagane dla danej operacji na danych, zobacz Uprawnienia do wywoływania operacji na danych [obiektów blob i kolejek.](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations) [Dowiedz się więcej](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/* | Pełne uprawnienia do kontenerów. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Zwraca klucz delegowania użytkownika dla Blob service. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/* | Pełne uprawnienia do obiektów blob. |
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

Odczytywanie i odczytywanie kontenerów i obiektów blob usługi Azure Storage. Aby dowiedzieć się, które akcje są wymagane dla danej operacji na danych, zobacz Uprawnienia do wywoływania operacji na danych [obiektów blob i kolejek.](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations) [Dowiedz się więcej](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/read | Zwracanie kontenera lub listy kontenerów. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Zwraca klucz delegowania użytkownika dla Blob service. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | Zwracanie obiektu blob lub listy obiektów blob. |
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

### <a name="storage-blob-delegator"></a>Delegat usługi Storage Blob

Pobierz klucz delegowania użytkownika, którego następnie można użyć do utworzenia sygnatury dostępu współdzielonych dla kontenera lub obiektu blob podpisanego przy użyciu poświadczeń usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Tworzenie sygnatury dostępu współdzielonego delegowania użytkownika.](/rest/api/storageservices/create-user-delegation-sas) [Dowiedz się więcej](/rest/api/storageservices/get-user-delegation-key)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Zwraca klucz delegowania użytkownika dla Blob service. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

Umożliwia odczyt, zapis i usuwanie dostępu do plików/katalogów w udziałach plików platformy Azure. Ta rola nie ma wbudowanego odpowiednika na serwerach plików z systemem Windows. [Dowiedz się więcej](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/read | Zwraca plik/folder lub listę plików/folderów. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/write | Zwraca wynik zapisu pliku lub utworzenia folderu. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/delete | Zwraca wynik usunięcia pliku/folderu. |
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

Umożliwia odczytywanie, zapis, usuwanie i modyfikowanie list ACL dla plików/katalogów w udziałach plików platformy Azure. Ta rola jest odpowiednikiem listy ACL zmian udziału plików na serwerach plików z systemem Windows. [Dowiedz się więcej](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/read | Zwraca plik/folder lub listę plików/folderów. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/write | Zwraca wynik zapisu pliku lub utworzenia folderu. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/delete | Zwraca wynik usunięcia pliku/folderu. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/modifypermissions/action | Zwraca wynik modyfikowania uprawnień do pliku/folderu. |
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

Umożliwia dostęp do odczytu plików/katalogów w udziałach plików platformy Azure. Ta rola jest odpowiednikiem listy ACL udziałów plików odczytu na serwerach plików z systemem Windows. [Dowiedz się więcej](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/read | Zwraca plik/folder lub listę plików/folderów. |
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

### <a name="storage-queue-data-contributor"></a>Współautor danych kolejki usługi Storage

Odczytywanie, zapis i usuwanie kolejek i komunikatów w kolejce usługi Azure Storage. Aby dowiedzieć się, które akcje są wymagane dla danej operacji na danych, zobacz Permissions for calling blob and queue data operations (Uprawnienia do wywoływania operacji na danych obiektów [blob i kolejek).](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations) [Dowiedz się więcej](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/delete | Usuwanie kolejki. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/read | Zwraca kolejkę lub listę kolejek. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/write | Modyfikowanie właściwości lub metadanych kolejki. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/delete | Usuń co najmniej jeden komunikat z kolejki. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/read | Podejrzanie lub pobierz co najmniej jeden komunikat z kolejki. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/write | Dodaj komunikat do kolejki. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/process/action | Zwraca wynik przetwarzania komunikatu |
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

### <a name="storage-queue-data-message-processor"></a>Procesor komunikatów danych kolejki usługi Storage

Podgląd, pobieranie i usuwanie komunikatu z kolejki usługi Azure Storage. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz Uprawnienia do wywoływania operacji na danych obiektów [blob i kolejek](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [Dowiedz się więcej](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/read | Podejrzanie komunikat. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/process/action | Pobieranie i usuwanie komunikatu. |
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

### <a name="storage-queue-data-message-sender"></a>Nadawca komunikatów o danych kolejki usługi Storage

Dodawanie komunikatów do kolejki usługi Azure Storage. Aby dowiedzieć się, które akcje są wymagane dla danej operacji na danych, zobacz Uprawnienia do wywoływania operacji na danych [obiektów blob i kolejek.](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations) [Dowiedz się więcej](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/add/action | Dodaj komunikat do kolejki. |
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

### <a name="storage-queue-data-reader"></a>Czytnik danych kolejki usługi Storage

Odczytywanie i odczytywanie kolejek usługi Azure Storage oraz komunikatów kolejek. Aby dowiedzieć się, które akcje są wymagane dla danej operacji na danych, zobacz Uprawnienia do wywoływania operacji na danych [obiektów blob i kolejek.](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations) [Dowiedz się więcej](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/read | Zwraca kolejkę lub listę kolejek. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/read | Podgląd lub pobieranie jednego lub większej liczby komunikatów z kolejki. |
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


### <a name="azure-maps-data-contributor"></a>Azure Maps współautor danych

Udziela dostępu do odczytu, zapisu i usuwania danych powiązanych z mapą z konta usługi Azure Maps. [Dowiedz się więcej](../azure-maps/azure-maps-authentication.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/read |  |
> | [Microsoft.Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/write |  |
> | [Microsoft.Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/delete |  |
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

### <a name="azure-maps-data-reader"></a>Azure Maps czytnik danych

Udziela dostępu do odczytu danych związanych z mapą z konta usługi Azure Maps. [Dowiedz się więcej](../azure-maps/azure-maps-authentication.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/read |  |
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

### <a name="azure-spring-cloud-data-reader"></a>Azure Spring Cloud czytnik danych

Zezwalaj na dostęp do odczytu Azure Spring Cloud danych [Dowiedz się więcej](../spring-cloud/how-to-access-data-plane-azure-ad-rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.AppPlatform](resource-provider-operations.md#microsoftappplatform)/Spring/*/read |  |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allow read access to Azure Spring Cloud Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b5537268-8956-4941-a8f0-646150406f0c",
  "name": "b5537268-8956-4941-a8f0-646150406f0c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppPlatform/Spring/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Spring Cloud Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="search-service-contributor"></a>Współautor usługi wyszukiwania

Umożliwia zarządzanie usługami wyszukiwania, ale nie umożliwia dostępu do nich. [Dowiedz się więcej](../search/search-security-rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie tym alertem |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Search](resource-provider-operations.md#microsoftsearch)/searchServices/* | Tworzenie usług wyszukiwania i zarządzanie nimi |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
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

### <a name="signalr-accesskey-reader"></a>SignalR AccessKey Reader

Odczytywanie SignalR Service dostępu

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/*/read |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/listkeys/action | Wyświetlanie wartości kluczy dostępu usługi SignalR w portalu zarządzania lub za pośrednictwem interfejsu API |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

### <a name="signalr-app-server-preview"></a>SignalR App Server (wersja zapoznawcza)

Umożliwia serwerowi aplikacji dostęp SignalR Service z opcjami uwierzytelniania usługi AAD.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/accessKey/action | Wygeneruj tymczasowy klucz AccessKey do podpisywania clientTokens. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/serverConnection/write | Uruchom połączenie z serwerem. |
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

### <a name="signalr-contributor"></a>Współautor signalr

Tworzenie, odczytywanie, aktualizowanie i usuwanie zasobów usługi SignalR Service

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/* |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie tym alertem |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
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

### <a name="signalr-serverless-contributor-preview"></a>Współautor bez serwera usługi SignalR (wersja zapoznawcza)

Umożliwia aplikacji dostęp do usługi w trybie bez serwera przy użyciu opcji uwierzytelniania usługi AAD.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/clientToken/action | Wygeneruj dane ClientToken w celu rozpoczęcia połączenia klienta. |
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

### <a name="signalr-service-owner-preview"></a>SignalR Service właściciela (wersja zapoznawcza)

Pełny dostęp do Azure SignalR Service API REST

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/accessKey/action | Wygeneruj tymczasowy klucz AccessKey do podpisywania clientTokens. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/clientToken/action | Wygeneruj clientToken do rozpoczęcia połączenia klienta. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/hub/send/action | Rozgłasza komunikaty do wszystkich połączeń klienta w centrum. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/send/action | Rozgłasza komunikat do grupy. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/read | Sprawdź istnienie grupy lub istnienie użytkownika w grupie. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/write | Dołącz/odejdzie z grupy. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/send/action | Wysyłaj komunikaty bezpośrednio do połączenia klienta. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/read | Sprawdź istnienie połączenia klienta. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/write | Zamknij połączenie klienta. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/send/action | Wysyłaj komunikaty do użytkownika, który może składać się z wielu połączeń klienta. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/read | Sprawdź istnienie użytkownika. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/write | Modyfikowanie użytkownika. |
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
        "Microsoft.SignalRService/SignalR/auth/accessKey/action",
        "Microsoft.SignalRService/SignalR/auth/clientToken/action",
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

### <a name="signalr-service-reader-preview"></a>SignalR Service czytelnika (wersja zapoznawcza)

Dostęp tylko do odczytu do Azure SignalR Service API REST

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/read | Sprawdź istnienie grupy lub istnienie użytkownika w grupie. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/read | Sprawdź istnienie połączenia klienta. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/read | Sprawdź istnienie użytkownika. |
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

Umożliwia zarządzanie planami sieci Web witryn internetowych, ale nie umożliwia dostępu do nich.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/* | Tworzenie farm serwerów i zarządzanie nimi |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/hostingEnvironments/Join/Action | Dołącza App Service Environment |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

### <a name="website-contributor"></a>Współautor witryny internetowej

Umożliwia zarządzanie witrynami internetowymi (nie planami sieci Web), ale nie umożliwia dostępu do nich.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie tym alertem |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/* | Tworzenie składników usługi Insights i zarządzanie nimi |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/certificates/* | Tworzenie certyfikatów witryn internetowych i zarządzanie nimi |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/listSitesAssignedToHostName/read | Pobierz nazwy witryn przypisanych do nazwy hosta. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/join/action | Dołącza App Service planu |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/read | Uzyskiwanie właściwości w planie App Service aplikacji |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/sites/* | Tworzenie witryn internetowych i zarządzanie nimi (tworzenie lokacji wymaga również uprawnień do zapisu w skojarzonym planie App Service sieci Web) |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

acr delete [Dowiedz się więcej](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/artifacts/delete | Usuń artefakt w rejestrze kontenerów. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

acr image signer [Dowiedz się więcej](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/sign/write | Wypychanie/ściąganie metadanych zaufania zawartości dla rejestru kontenerów. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

acr pull [Dowiedz się więcej](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/pull/read | Ściąganie lub uzyskiwanie obrazów z rejestru kontenerów. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
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

acr push [Dowiedz się więcej](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/pull/read | Ściąganie lub uzyskiwanie obrazów z rejestru kontenerów. |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/push/write | Wypychanie lub pisanie obrazów do rejestru kontenerów. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
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

Czytnik danych kwarantanny acr

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantine/read | Ściąganie lub uzyskiwanie obrazów poddanych kwarantannie z rejestru kontenerów |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
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

ACR kwarantanny zapisujący danych

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantine/read | Ściąganie lub uzyskiwanie obrazów poddanych kwarantannie z rejestru kontenerów |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantine/write | Zapis/modyfikowanie stanu kwarantanny obrazów poddanych kwarantannie |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

### <a name="azure-kubernetes-service-cluster-admin-role"></a>Azure Kubernetes Service roli administratora klastra

Lista akcji poświadczeń administratora klastra. [Dowiedz się więcej](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterAdminCredential/action | Lista poświadczeń clusterAdmin klastra zarządzanego |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/accessProfiles/listCredential/action | Uzyskiwanie profilu dostępu klastra zarządzanego według nazwy roli przy użyciu poświadczeń listy |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/read | Uzyskiwanie klastra zarządzanego |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

### <a name="azure-kubernetes-service-cluster-user-role"></a>Azure Kubernetes Service roli użytkownika klastra

Lista akcji poświadczeń użytkownika klastra. [Dowiedz się więcej](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | Lista poświadczeń clusterUser klastra zarządzanego |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/read | Uzyskiwanie klastra zarządzanego |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

### <a name="azure-kubernetes-service-contributor-role"></a>Azure Kubernetes Service współautora

Przyznaje dostęp do odczytu i zapisu w Azure Kubernetes Service klastrach [Dowiedz się więcej](../aks/concepts-identity.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/read | Uzyskiwanie klastra zarządzanego |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/write | Tworzy nowy klaster zarządzany lub aktualizuje istniejący |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
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

### <a name="azure-kubernetes-service-rbac-admin"></a>Azure Kubernetes Service RBAC Admin

Umożliwia zarządzanie wszystkimi zasobami w obszarze klastra/przestrzeni nazw, z wyjątkiem aktualizowania lub usuwania przydziałów zasobów i przestrzeni nazw. [Dowiedz się więcej](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie tym alertem |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Tworzy lub aktualizuje wdrożenie. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Pobierz wyniki operacji subskrypcji. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Pobiera listę subskrypcji. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | Lista poświadczeń clusterUser klastra zarządzanego |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
> | **NotDataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/write | Zapisuje resourcequotas |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/delete | Usuwa resourcequotas |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/write | Zapisuje przestrzenie nazw |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/delete | Usuwa przestrzenie nazw |

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

### <a name="azure-kubernetes-service-rbac-cluster-admin"></a>Azure Kubernetes Service RBAC Cluster Admin

Umożliwia zarządzanie wszystkimi zasobami w klastrze. [Dowiedz się więcej](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie tym alertem |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Tworzy lub aktualizuje wdrożenie. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Pobierz wyniki operacji subskrypcji. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Pobiera listę subskrypcji. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | Lista poświadczeń clusterUser klastra zarządzanego |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
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

### <a name="azure-kubernetes-service-rbac-reader"></a>Azure Kubernetes Service RBAC Reader

Zezwala na dostęp tylko do odczytu, aby wyświetlić większość obiektów w przestrzeni nazw. Nie zezwala na wyświetlanie ról ani powiązań ról. Ta rola nie zezwala na wyświetlanie wpisów tajnych, ponieważ odczytywanie zawartości wpisów tajnych umożliwia dostęp do poświadczeń konta usługi w przestrzeni nazw, co umożliwia dostęp do interfejsu API jako dowolnemu użytkownikowi ServiceAccount w przestrzeni nazw (w formie eskalacji uprawnień). Zastosowanie tej roli w zakresie klastra zapewni dostęp we wszystkich przestrzeniach nazw. [Dowiedz się więcej](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Tworzy lub aktualizuje wdrożenie. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Pobierz wyniki operacji subskrypcji. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Pobiera listę subskrypcji. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/controllerrevisions/read | Kontroler odczyturevisions |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/daemonsets/read | Odczytuje demony |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/deployments/read | Odczytuje wdrożenia |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/replicasets/read | Odczytuje zestawy replik |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/statefulsets/read | Odczytuje zestawy stanowe |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/autoscaling/horizontalpodautoscalers/read | Odczyty horizontalpodautoscalers |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/cronjobs/read | Odczytuje cronjobs |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/jobs/read | Odczytuje zadania |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/configmaps/read | Odczytuje mapy konfiguracji |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/endpoints/read | Odczytuje punkty końcowe |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events.k8s.io/events/read | Odczytuje zdarzenia |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events/read | Odczytuje zdarzenia |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/daemonsets/read | Odczytuje demony |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/deployments/read | Odczytuje wdrożenia |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/ingresses/read | Odczyty danych przychodzących |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/networkpolicies/read | Odczytuje networkpolicies |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/replicasets/read | Odczytuje zestawy replik |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/limitranges/read | Limity odczytów |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/read | Odczytuje przestrzenie nazw |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/ingresses/read | Odczytuje ruch przychodzący |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/networkpolicies/read | Odczytuje networkpolicies |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/persistentvolumeclaims/read | Odczytuje persistentvolumeclaims |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/pods/read | Odczytuje zasobniki |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/policy/poddisruptionbudgets/read | Odczytuje zasobniki poddisruptionbudgets |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/read | Odczytuje kontrolery replikacji |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/read | Odczytuje kontrolery replikacji |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/read | Odczytuje resourcequotas |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/serviceaccounts/read | Odczytuje konta usług |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/services/read | Odczytuje usługi |
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

### <a name="azure-kubernetes-service-rbac-writer"></a>Azure Kubernetes Service RBAC Writer

Umożliwia dostęp do odczytu/zapisu do większości obiektów w przestrzeni nazw. Ta rola nie zezwala na wyświetlanie ani modyfikowanie ról ani powiązań ról. Jednak ta rola umożliwia dostęp do wpisów tajnych i uruchamianie zasobników jako dowolnego konta usługi w przestrzeni nazw, więc może służyć do uzyskiwania poziomów dostępu do interfejsu API dowolnego konta usługi ServiceAccount w przestrzeni nazw. Zastosowanie tej roli w zakresie klastra zapewni dostęp we wszystkich przestrzeniach nazw. [Dowiedz się więcej](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Tworzy lub aktualizuje wdrożenie. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Pobierz wyniki operacji subskrypcji. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Pobiera listę subskrypcji. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/controllerrevisions/read | Kontroler odczyturevisions |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/daemonsets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/deployments/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/replicasets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/statefulsets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/autoscaling/horizontalpodautoscalers/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/cronjobs/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/jobs/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/configmaps/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/endpoints/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events.k8s.io/events/read | Odczytuje zdarzenia |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events/read | Odczytuje zdarzenia |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/daemonsets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/deployments/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/ingresses/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/networkpolicies/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/replicasets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/limitranges/read | Limity odczytów |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/read | Odczytuje przestrzenie nazw |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/ingresses/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/networkpolicies/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/persistentvolumeclaims/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/pods/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/policy/poddisruptionbudgets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/read | Odczytuje resourcequotas |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/secrets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/serviceaccounts/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/services/* |  |
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


### <a name="cosmos-db-account-reader-role"></a>Cosmos DB roli czytelnika konta

Może odczytywać Azure Cosmos DB konta. Aby [zarządzać kontami,](#documentdb-account-contributor) zobacz Współautor konta Azure Cosmos DB DocumentDB. [Dowiedz się więcej](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/*/read | Odczytywanie dowolnej kolekcji |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlykeys/action | Odczytuje tylko klucze do odczytu konta bazy danych. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/read | Odczytywanie definicji metryk |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Metrics/read | Odczytywanie metryk |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
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

### <a name="cosmos-db-operator"></a>Cosmos DB Operator

Umożliwia zarządzanie kontami Azure Cosmos DB, ale nie umożliwia uzyskiwania dostępu do danych w tych kontach. Uniemożliwia dostęp do kluczy konta i parametry połączenia. [Dowiedz się więcej](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Dołącza zasoby, takie jak konto magazynu lub baza danych SQL, do podsieci. Nie ma alertu. |
> | **NotActions** |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlyKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/regenerateKey/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listConnectionStrings/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleDefinitions/write | Tworzenie lub aktualizowanie definicji roli SQL |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleDefinitions/delete | Usuwanie definicji roli SQL |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleAssignments/write | Tworzenie lub aktualizowanie przypisania roli SQL |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleAssignments/delete | Usuwanie przypisania roli SQL |
> | **DataActions (Działania danych)** |  |
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

Może przesyłać żądanie przywrócenia dla bazy Cosmos DB lub kontenera dla konta [Dowiedz się więcej](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/backup/action | Przesyłanie żądania w celu skonfigurowania kopii zapasowej |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/restore/action | Przesyłanie żądania przywracania |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
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

Może wykonać akcję przywracania dla Cosmos DB bazy danych z trybem ciągłej kopii zapasowej

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/locations/restoreableDatabaseAccounts/restore/action | Przesyłanie żądania przywrócenia |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/locations/restorableDatabaseAccounts/*/read |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/locations/restorableDatabaseAccounts/read | Odczytywanie przywracanego konta bazy danych lub lista wszystkich kont baz danych, które można przywrócić |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

### <a name="documentdb-account-contributor"></a>Współautor konta bazy danych DocumentDB

Może zarządzać Azure Cosmos DB kontami. Azure Cosmos DB jest wcześniej znana jako DocumentDB. [Dowiedz się więcej](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/* | Tworzenie kont Azure Cosmos DB zarządzania nimi |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Dołącza zasoby, takie jak konto magazynu lub baza danych SQL, do podsieci. Nie ma alertów. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
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

### <a name="redis-cache-contributor"></a>Redis Cache współautora

Umożliwia zarządzanie pamięciami podręcznmi Redis Cache, ale nie umożliwia dostępu do nich.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Cache](resource-provider-operations.md#microsoftcache)/register/action | Rejestruje dostawcę zasobów "Microsoft.Cache" w subskrypcji |
> | [Microsoft.Cache](resource-provider-operations.md#microsoftcache)/redis/* | Tworzenie pamięci podręcznych Redis Cache i zarządzanie nimi |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie tym alertem |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

Umożliwia zarządzanie bazami danych SQL, ale nie umożliwia dostępu do nich. Ponadto nie można zarządzać zasadami związanymi z zabezpieczeniami ani ich nadrzędnymi serwerami SQL. [Dowiedz się więcej](../data-share/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/*/read |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/* | Tworzenie baz danych SQL i zarządzanie nimi |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/read | Zwraca listę serwerów lub pobiera właściwości dla określonego serwera. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Odczytywanie metryk |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | Odczytywanie definicji metryk |
> | **NotActions** |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingSettings/* | Edytowanie ustawień inspekcji |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/read | Pobieranie rekordów inspekcji obiektów blob bazy danych |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/dataMaskingPolicies/* | Edytowanie zasad maskowania danych |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/extendedAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityAlertPolicies/* | Edytowanie zasad alertów zabezpieczeń |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityMetrics/* | Edytowanie metryk zabezpieczeń |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
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

### <a name="sql-managed-instance-contributor"></a>SQL Managed Instance współautora

Umożliwia zarządzanie wystąpieniami zarządzanymi SQL i wymaganą konfiguracją sieci, ale nie umożliwia dawać dostępu innym osobom.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/routeTables/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/*/read |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/instanceFailoverGroups/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/* |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/* |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Odczytywanie metryk |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | Odczytywanie definicji metryk |
> | **NotActions** |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/delete | Usuwa określony serwer zarządzany Azure Active Directory tylko obiekt uwierzytelniania |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/write | Dodaje lub aktualizuje określony serwer zarządzany Azure Active Directory tylko obiekt uwierzytelniania |
> | **DataActions** |  |
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

Umożliwia zarządzanie zasadami dotyczącymi zabezpieczeń serwerów SQL i baz danych, ale nie ma do nich dostępu. [Dowiedz się więcej](../azure-sql/database/azure-defender-for-sql.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Dołącza zasoby, takie jak konto magazynu lub baza danych SQL, do podsieci. Nie ma alertu. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/administratorAzureAsyncOperation/read | Pobiera wynik operacji administratora async wystąpienia zarządzanego platformy Azure. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/transparentDataEncryption/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/auditingSettings/* | Tworzenie ustawienia inspekcji programu SQL Server i zarządzanie nimi |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/extendedAuditingSettings/read | Pobieranie szczegółów zasad inspekcji rozszerzonych obiektów blob serwera skonfigurowanych na danym serwerze |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingSettings/* | Tworzenie ustawień inspekcji bazy danych programu SQL Server i zarządzanie nimi |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/read | Pobieranie rekordów inspekcji obiektów blob bazy danych |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/dataMaskingPolicies/* | Tworzenie zasad maskowania danych bazy danych programu SQL Server i zarządzanie nimi |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/extendedAuditingSettings/read | Pobieranie szczegółów rozszerzonych zasad inspekcji obiektów blob skonfigurowanych dla danej bazy danych |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/read | Zwróć listę baz danych lub pobiera właściwości dla określonej bazy danych. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/read | Pobierz schemat bazy danych. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/read | Pobierz kolumnę bazy danych. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/read | Pobierz tabelę bazy danych. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityAlertPolicies/* | Tworzenie zasad alertów zabezpieczeń bazy danych programu SQL Server i zarządzanie nimi |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityMetrics/* | Tworzenie metryk zabezpieczeń bazy danych programu SQL Server i zarządzanie nimi |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/transparentDataEncryption/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/devOpsAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/firewallRules/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/read | Zwraca listę serwerów lub pobiera właściwości dla określonego serwera. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/securityAlertPolicies/* | Tworzenie zasad alertów zabezpieczeń programu SQL Server i zarządzanie nimi |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/vulnerabilityAssessments/* |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/azureADOnlyAuthentications/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/read | Zwraca listę wystąpień zarządzanych lub pobiera właściwości dla określonego wystąpienia zarządzanego. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/* |  |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/sqlVulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/administrators/read | Pobiera listę administratorów wystąpienia zarządzanego. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/administrators/read | Pobiera określony obiekt Azure Active Directory administratora |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

### <a name="sql-server-contributor"></a>SQL Server współautora

Umożliwia zarządzanie serwerami SQL i bazami danych, ale nie dostępem do nich, a nie ich zasadami dotyczącymi zabezpieczeń. [Dowiedz się więcej](../azure-sql/database/authentication-aad-configure.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/*/read |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/* | Tworzenie serwerów SQL i zarządzanie nimi |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Odczytywanie metryk |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | Odczytywanie definicji metryk |
> | **NotActions** |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/auditingSettings/* | Edytowanie ustawień inspekcji programu SQL Server |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingSettings/* | Edytowanie ustawień inspekcji bazy danych programu SQL Server |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/read | Pobieranie rekordów inspekcji obiektów blob bazy danych |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/dataMaskingPolicies/* | Edytowanie zasad maskowania danych bazy danych programu SQL Server |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/extendedAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityAlertPolicies/* | Edytowanie zasad alertów zabezpieczeń bazy danych programu SQL Server |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityMetrics/* | Edytowanie metryk zabezpieczeń bazy danych programu SQL Server |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/devOpsAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/extendedAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/securityAlertPolicies/* | Edytowanie zasad alertów zabezpieczeń programu SQL Server |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/azureADOnlyAuthentications/delete | Usuwa określony serwer tylko Azure Active Directory obiektu uwierzytelniania |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/azureADOnlyAuthentications/write | Dodaje lub aktualizuje określony serwer Azure Active Directory tylko obiekt uwierzytelniania |
> | **DataActions (Działania danych)** |  |
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


### <a name="azure-event-hubs-data-owner"></a>Azure Event Hubs właściciela danych

Umożliwia pełny dostęp do Azure Event Hubs zasobów. [Dowiedz się więcej](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
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

### <a name="azure-event-hubs-data-receiver"></a>Azure Event Hubs Data Receiver

Umożliwia odbieranie dostępu do Azure Event Hubs zasobów. [Dowiedz się więcej](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/eventhubs/consumergroups/read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/receive/action |  |
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

### <a name="azure-event-hubs-data-sender"></a>Azure Event Hubs nadawcy danych

Umożliwia wysyłanie dostępu do Azure Event Hubs zasobów. [Dowiedz się więcej](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/eventhubs/read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/send/action |  |
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

### <a name="data-factory-contributor"></a>Data Factory współautora

Tworzenie fabryk danych i zarządzanie nimi, a także zasoby podrzędne w tych fabrykach. [Dowiedz się więcej](../data-factory/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.DataFactory](resource-provider-operations.md#microsoftdatafactory)/dataFactories/* | Tworzenie fabryk danych i zasobów podrzędnych oraz zarządzanie nimi. |
> | [Microsoft.DataFactory](resource-provider-operations.md#microsoftdatafactory)/fabryki/* | Tworzenie fabryk danych i zasobów podrzędnych oraz zarządzanie nimi. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/write | Tworzenie lub aktualizowanie subskrypcji zdarzeń |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
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

### <a name="data-purger"></a>Przeczyszczania danych

Usuwanie danych prywatnych z obszaru roboczego usługi Log Analytics. [Dowiedz się więcej](../azure-monitor/logs/personal-data-mgmt.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/purge/action | Przeczyszczanie danych z Application Insights |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Wyświetlanie danych analizy dzienników |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/purge/action | Usuwanie określonych danych z obszaru roboczego |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

Umożliwia odczytywanie i modyfikowanie konfiguracji klastra usługi HDInsight. [Dowiedz się więcej](../hdinsight/hdinsight-migrate-granular-access-cluster-configurations.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/*/read |  |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/getGatewaySettings/action | Uzyskiwanie ustawień bramy dla klastra usługi HDInsight |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/updateGatewaySettings/action | Aktualizowanie ustawień bramy dla klastra usługi HDInsight |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/configurations/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Pobiera lub wyświetla listę operacji wdrażania. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

### <a name="hdinsight-domain-services-contributor"></a>Współautor usług HDInsight Domain Services

Can Read, Create, Modify and Delete Domain Services related operations needed for HDInsight pakiet Enterprise Security [Dowiedz się więcej](../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.AAD](resource-provider-operations.md#microsoftaad)/*/read |  |
> | [Microsoft.AAD](resource-provider-operations.md#microsoftaad)/domainServices/*/read |  |
> | [Microsoft.AAD](resource-provider-operations.md#microsoftaad)/domainServices/oucontainer/* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
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

Współautor usługi Log Analytics może odczytywać wszystkie dane monitorowania i edytować ustawienia monitorowania. Edytowanie ustawień monitorowania obejmuje dodawanie rozszerzenia maszyny wirtualnej do maszyn wirtualnych; odczytywanie kluczy konta magazynu w celu skonfigurowania zbierania dzienników z usługi Azure Storage; tworzenie i konfigurowanie kont usługi Automation; dodawanie rozwiązań; i konfigurowanie diagnostyki platformy Azure dla wszystkich zasobów platformy Azure. [Dowiedz się więcej](../azure-monitor/logs/manage-access.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | */read | Odczytywanie zasobów wszystkich typów, z wyjątkiem wpisów tajnych. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/* |  |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/extensions/* |  |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | Wyświetla listę kluczy dostępu dla kont magazynu. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/extensions/* |  |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/extensions/write | Instaluje lub aktualizuje Azure Arc rozszerzenia |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie tym alertem |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Tworzy, aktualizuje lub odczytuje ustawienie diagnostyczne dla Analysis Server |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/* |  |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourcegroups/deployments/* |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | Zwraca klucze dostępu dla określonego konta magazynu. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

Czytelnik usługi Log Analytics może wyświetlać i wyszukiwać wszystkie dane monitorowania, a także wyświetlać ustawienia monitorowania, w tym wyświetlać konfigurację diagnostyki platformy Azure dla wszystkich zasobów platformy Azure. [Dowiedz się więcej](../azure-monitor/logs/manage-access.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | */odczyt | Odczytywanie zasobów wszystkich typów, z wyjątkiem wpisów tajnych. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | Wyszukiwanie przy użyciu nowego aparatu. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | Wykonuje zapytanie wyszukiwania |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/sharedKeys/read | Pobiera klucze udostępnione dla obszaru roboczego. Te klucze są używane do łączenia agentów Operational Insights Microsoft z obszarem roboczym. |
> | **DataActions** |  |
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

### <a name="purview-data-curator"></a>Purview Data Curator

Curator danych Microsoft.Purview może tworzyć, odczytywać, modyfikować i usuwać obiekty danych wykazu oraz ustanawiać relacje między obiektami. Ta rola jest w wersji zapoznawczej i może ulec zmianie.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/read | Odczytaj zasób konta dla dostawcy Microsoft Purview. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/data/read | Odczytywanie obiektów danych. |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/data/write | Tworzenie, aktualizowanie i usuwanie obiektów danych. |
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

### <a name="purview-data-reader"></a>Purview Data Reader

Czytnik danych Microsoft.Purview może odczytywać obiekty danych wykazu. Ta rola jest w wersji zapoznawczej i może ulec zmianie.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/read | Odczytaj zasób konta dla dostawcy usługi Microsoft Purview. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/data/read | Odczytywanie obiektów danych. |
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

### <a name="purview-data-source-administrator"></a>Administrator źródła danych programu Purview

Administrator źródła danych Microsoft.Purview może zarządzać źródłami danych i skanowaniami danych. Ta rola jest w wersji zapoznawczej i może ulec zmianie.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/read | Odczytaj zasób konta dla dostawcy usługi Microsoft Purview. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/scan/read | Odczytywanie źródeł danych i skanów. |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/scan/write | Tworzenie, aktualizowanie i usuwanie źródeł danych oraz zarządzanie skanowaniami. |
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

### <a name="schema-registry-contributor-preview"></a>Współautor rejestru schematów (wersja zapoznawcza)

Odczyt, zapis i usuwanie schematów i grup rejestru schematów.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemagroups/* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemas/* |  |
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

### <a name="schema-registry-reader-preview"></a>Czytelnik rejestru schematów (wersja zapoznawcza)

Odczytywanie i odczytywanie grup i schematów rejestru schematów.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemagroups/read | Pobierz listę opisów zasobów schemagroup |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemas/read | Pobieranie schematów |
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


### <a name="blockchain-member-node-access-preview"></a>Dostęp do węzła członkowskiego łańcucha bloków (wersja zapoznawcza)

Zezwala na dostęp do węzłów członkowskich łańcucha bloków [Dowiedz się więcej](../blockchain/service/configure-aad.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Blockchain](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/read | Pobiera lub wyświetla istniejące węzła transakcji członkowskiej łańcucha bloków. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
> | [Microsoft.Blockchain](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/connect/action | Nawiązuje połączenie z węzłem transakcji członka łańcucha bloków. |
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


### <a name="cognitive-services-contributor"></a>Cognitive Services współautora

Umożliwia tworzenie, odczytywanie, aktualizowanie i usuwanie kluczy Cognitive Services. [Dowiedz się więcej](../cognitive-services/cognitive-services-virtual-networks.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
> | [Microsoft.Features](resource-provider-operations.md#microsoftfeatures)/features/read | Pobiera funkcje subskrypcji. |
> | [Microsoft.Features](resource-provider-operations.md#microsoftfeatures)/providers/features/read | Pobiera funkcję subskrypcji u danego dostawcy zasobów. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Tworzy, aktualizuje lub odczytuje ustawienie diagnostyczne dla Analysis Server |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/logDefinitions/read | Odczytywanie definicji dzienników |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricdefinitions/read | Odczytywanie definicji metryk |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Odczytywanie metryk |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Pobiera lub wyświetla listę operacji wdrażania. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Pobierz wyniki operacji subskrypcji. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Pobiera listę subskrypcji. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourcegroups/deployments/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
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

### <a name="cognitive-services-custom-vision-contributor"></a>Cognitive Services Custom Vision współautora

Pełny dostęp do projektu, w tym możliwość wyświetlania, tworzenia, edytowania i usuwania projektów. [Dowiedz się więcej](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/* |  |
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

### <a name="cognitive-services-custom-vision-deployment"></a>Cognitive Services Custom Vision wdrożenia

Publikowanie, cofanie publikowania lub eksportowanie modeli. Wdrożenie może wyświetlić projekt, ale nie może go zaktualizować. [Dowiedz się więcej](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/*/read |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/predictions/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/iterations/publish/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/iterations/export/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/quicktest/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/classify/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/detect/* |  |
> | **NotDataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/export/read | Eksportuje projekt. |

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

Wyświetlanie, edytowanie obrazów szkoleniowych oraz tworzenie, dodawanie, usuwanie lub usuwanie tagów obrazów. Etykiety mogą wyświetlać projekt, ale nie mogą aktualizować żadnych danych innych niż obrazy szkoleniowe i tagi. [Dowiedz się więcej](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/*/read |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/predictions/query/action | Pobierz obrazy, które zostały wysłane do punktu końcowego przewidywania. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/images/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/tags/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/images/suggested/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/tagsandregions/suggestions/action | Ten interfejs API pobierze sugerowane tagi i regiony dla tablicy/partii nieotagowanych obrazów wraz z ufnościami tagów. Jeśli tagi nie zostaną znalezione, zwraca pustą tablicę. |
> | **NotDataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/export/read | Eksportuje projekt. |

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

### <a name="cognitive-services-custom-vision-reader"></a>Cognitive Services Custom Vision czytelnika

Akcje tylko do odczytu w projekcie. Czytelnicy nie mogą tworzyć ani aktualizować projektu. [Dowiedz się więcej](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/*/read |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/predictions/query/action | Pobierz obrazy, które zostały wysłane do punktu końcowego przewidywania. |
> | **NotDataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/export/read | Eksportuje projekt. |

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

### <a name="cognitive-services-custom-vision-trainer"></a>Cognitive Services Custom Vision Instruktor

Wyświetlaj, edytuj projekty i trenuj modele, w tym możliwość publikowania, cofania publikowania i eksportowania modeli. Instruktorzy nie mogą tworzyć ani usuwać projektu. [Dowiedz się więcej](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/* |  |
> | **NotDataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/action | Utwórz projekt. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/delete | Usuń określony projekt. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/import/action | Importuje projekt. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/export/read | Eksportuje projekt. |

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

### <a name="cognitive-services-data-reader-preview"></a>Cognitive Services Czytnik danych (wersja zapoznawcza)

Umożliwia odczytywanie Cognitive Services danych.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
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

### <a name="cognitive-services-metrics-advisor-administrator"></a>Cognitive Services Doradca w zakresie metryk administratora

Pełny dostęp do projektu, w tym do konfiguracji na poziomie systemu. [Dowiedz się więcej](../cognitive-services/metrics-advisor/how-tos/alerts.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/Metrics / * |  |
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

### <a name="cognitive-services-qna-maker-editor"></a>Cognitive Services QnA Maker edytora kodu

Umożliwia tworzenie, edytowanie, importowanie i eksportowanie bazy wiedzy. Nie można opublikować ani usunąć bazy wiedzy. [Dowiedz się więcej](../cognitive-services/qnamaker/reference-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleAssignments/read | Uzyskaj informacje o przypisaniu roli. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleDefinitions/read | Uzyskaj informacje o definicji roli. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/read | Pobiera listę baz wiedzy lub szczegóły określonego bazy wiedzy. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/download/read | Pobierz bazę wiedzy. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/create/write | Operacja asynchroniczna tworzenia nowej bazy wiedzy. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/write | Asynchroniczna operacja modyfikowania bazy wiedzy lub zastępowania zawartości bazy wiedzy. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/generateanswer/action | Wywołanie GenerateAnswer w celu wykonywania zapytań w bazie wiedzy. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/train/action | Trenuj wywołanie, aby dodać sugestie do bazy wiedzy. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/zmiany/odczyt | Pobieranie zmian ze środowiska uruchomieniowego. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/zmiany/zapis | Zastąp dane zmian. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointkeys/read | Pobiera klucze punktu końcowego dla punktu końcowego |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointkeys/refreshkeys/action | Ponownie generuje klucz punktu końcowego. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointsettings/read | Pobiera ustawienia punktu końcowego dla punktu końcowego |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointsettings/write | Aktualizowanie punktów końcowych dla punktu końcowego. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/operations/read | Pobiera szczegóły konkretnej długotrwałej operacji. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/read | Pobiera listę baz wiedzy lub szczegóły określonego bazy wiedzy. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/download/read | Pobierz bazę wiedzy. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/create/write | Asynchroniczna operacja tworzenia nowej bazy wiedzy. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/write | Asynchroniczna operacja modyfikowania bazy wiedzy lub zastępowania zawartości bazy wiedzy. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/generateanswer/action | Wywołanie GenerateAnswer w celu wykonywania zapytań w bazie wiedzy. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/train/action | Trenuj wywołanie, aby dodać sugestie do bazy wiedzy. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/alterations/read | Pobieranie zmian ze środowiska uruchomieniowego. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/alterations/write | Zastąp dane zmian. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointkeys/read | Pobiera klucze punktu końcowego dla punktu końcowego |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointkeys/refreshkeys/action | Ponownie generuje klucz punktu końcowego. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointsettings/read | Pobiera ustawienia punktu końcowego dla punktu końcowego |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointsettings/write | Aktualizowanie przesłonień punktu końcowego. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/operations/read | Pobiera szczegóły konkretnej długotrwałej operacji. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/read | Pobiera listę baz wiedzy lub szczegóły określonego bazy wiedzy. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/download/read | Pobierz bazę wiedzy. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/create/write | Operacja asynchroniczna tworzenia nowej bazy wiedzy. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/write | Asynchroniczna operacja modyfikowania bazy wiedzy lub zastępowania zawartości bazy wiedzy. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/generateanswer/action | Wywołanie GenerateAnswer w celu wykonywania zapytań w bazie wiedzy. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/train/action | Trenuj wywołanie, aby dodać sugestie do bazy wiedzy. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/zmianami/odczytem | Pobieranie zmian ze środowiska uruchomieniowego. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/zmianami/zapisem | Zastępowanie danych zmian. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointkeys/read | Pobiera klucze punktu końcowego dla punktu końcowego |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointkeys/refreshkeys/action | Ponownie generuje klucz punktu końcowego. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointsettings/read | Pobiera ustawienia punktu końcowego dla punktu końcowego |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointsettings/write | Aktualizowanie punktów końcowych dla punktu końcowego. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/operations/read | Pobiera szczegóły konkretnej długotrwałej operacji. |
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

Umożliwia odczytywanie i testowanie tylko bazy wiedzy. [Dowiedz się więcej](../cognitive-services/qnamaker/reference-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleAssignments/read | Uzyskaj informacje o przypisaniu roli. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleDefinitions/read | Uzyskaj informacje o definicji roli. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/read | Pobiera listę baz wiedzy lub szczegóły określonego bazy wiedzy. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/download/read | Pobierz bazę wiedzy. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/generateanswer/action | Wywołanie GenerateAnswer w celu wykonywania zapytań w bazie wiedzy. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/zmiany/odczyt | Pobieranie zmian ze środowiska uruchomieniowego. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointkeys/read | Pobiera klucze punktu końcowego dla punktu końcowego |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointsettings/read | Pobiera ustawienia punktu końcowego dla punktu końcowego |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/read | Pobiera listę baz wiedzy lub szczegóły określonego bazy wiedzy. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/download/read | Pobierz bazę wiedzy. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/generateanswer/action | Wywołanie GenerateAnswer w celu wykonywania zapytań w bazie wiedzy. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/alterations/read | Pobieranie zmian ze środowiska uruchomieniowego. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointkeys/read | Pobiera klucze punktu końcowego dla punktu końcowego |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointsettings/read | Pobiera ustawienia punktu końcowego dla punktu końcowego |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/read | Pobiera listę baz wiedzy lub szczegóły określonego bazy wiedzy. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/download/read | Pobierz bazę wiedzy. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/generateanswer/action | Wywołanie GenerateAnswer w celu wykonywania zapytań w bazie wiedzy. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/zmianami/odczytem | Pobieranie zmian ze środowiska uruchomieniowego. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointkeys/read | Pobiera klucze punktu końcowego dla punktu końcowego |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointsettings/read | Pobiera ustawienia punktu końcowego dla punktu końcowego |
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
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/listkeys/action | Lista kluczy |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Odczytywanie alertu metryki klasycznej |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/read | Odczytywanie ustawienia diagnostycznego zasobu |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/logDefinitions/read | Odczytywanie definicji dzienników |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricdefinitions/read | Odczytywanie definicji metryk |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Odczytywanie metryk |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Pobiera lub wyświetla listę operacji wdrażania. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Pobierz wyniki operacji subskrypcji. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Pobiera listę subskrypcji. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
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

Zapewnia pełny dostęp do operacji zarządzania i zawartości [Dowiedz się więcej](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/read | Wykonuje operację odczytu związaną z aktualizacjami |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/write | Wykonuje operację zapisu związaną z aktualizacjami |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/delete | Wykonuje operację usuwania powiązaną z aktualizacjami |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/read | Wykonuje operację odczytu związaną z zarządzaniem |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/write | Wykonuje operację zapisu związaną z zarządzaniem |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/delete | Wykonuje operację usuwania związaną z zarządzaniem |
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

### <a name="device-update-content-administrator"></a>Administrator zawartości aktualizacji urządzenia

Zapewnia pełny dostęp do operacji na zawartości [Dowiedz się więcej](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie tym alertem |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/read | Wykonuje operację odczytu związaną z aktualizacjami |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/write | Wykonuje operację zapisu związaną z aktualizacjami |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/delete | Wykonuje operację usuwania związaną z aktualizacjami |
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

### <a name="device-update-content-reader"></a>Czytnik zawartości aktualizacji urządzenia

Zapewnia dostęp do odczytu do operacji na zawartości, ale nie zezwala na wprowadzenie zmian [Dowiedz się więcej](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/read | Wykonuje operację odczytu związaną z aktualizacjami |
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

Zapewnia pełny dostęp do operacji zarządzania Dowiedz [się więcej](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie tym alertem |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/read | Wykonuje operację odczytu związaną z zarządzaniem |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/write | Wykonuje operację zapisu związaną z zarządzaniem |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/delete | Wykonuje operację usuwania związaną z zarządzaniem |
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

### <a name="device-update-deployments-reader"></a>Czytelnik wdrożeń aktualizacji urządzeń

Zapewnia dostęp do odczytu operacji zarządzania, ale nie zezwala na wprowadzenie zmian [Dowiedz się więcej](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/read | Wykonuje operację odczytu związaną z zarządzaniem |
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

### <a name="device-update-reader"></a>Czytnik aktualizacji urządzeń

Zapewnia dostęp do odczytu do operacji zarządzania i zawartości, ale nie zezwala na wprowadzenie zmian [Dowiedz się więcej](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie tym alertem |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/read | Wykonuje operację odczytu związaną z aktualizacjami |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/read | Wykonuje operację odczytu związaną z zarządzaniem |
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


### <a name="remote-rendering-administrator"></a>Remote Rendering administratora

Zapewnia użytkownikowi funkcje konwersji, zarządzania sesją, renderowania i diagnostyki dla Azure Remote Rendering [Dowiedz się więcej](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/action | Rozpoczynanie konwersji zasobów |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/read | Uzyskiwanie właściwości konwersji zasobów |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/delete | Zatrzymywanie konwersji zasobów |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/read | Uzyskiwanie właściwości sesji |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/action | Rozpoczynanie sesji |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/delete | Zatrzymywanie sesji |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/render/read | Nawiązywanie połączenia z sesją |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/diagnostic/read | Nawiązywanie połączenia z Remote Rendering inspekcji |
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

### <a name="remote-rendering-client"></a>Remote Rendering klienta

Zapewnia użytkownikom możliwości zarządzania sesją, renderowaniem i diagnostyką Azure Remote Rendering. [Dowiedz się więcej](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/read | Uzyskiwanie właściwości sesji |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/action | Rozpoczynanie sesji |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/delete | Zatrzymywanie sesji |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/render/read | Nawiązywanie połączenia z sesją |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/diagnostic/read | Nawiązywanie połączenia z Remote Rendering danych |
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

### <a name="spatial-anchors-account-contributor"></a>Spatial Anchors współautor konta

Umożliwia zarządzanie zakotwiczeniami przestrzennmi na koncie, ale nie usuwanie ich [Dowiedz się więcej](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/create/action | Tworzenie kotwic przestrzennych |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | Odnajdywanie pobliskich kotwic przestrzennych |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | Uzyskiwanie właściwości kotwic przestrzennych |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | Lokalizowanie kotwic przestrzennych |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | Przesyłanie danych diagnostycznych w celu poprawy jakości usługi Azure Spatial Anchors Service |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/write | Aktualizowanie właściwości zakotwiczeń przestrzennych |
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

### <a name="spatial-anchors-account-owner"></a>Spatial Anchors właściciela konta

Umożliwia zarządzanie zakotwiczeniami przestrzenni na koncie, w tym ich usuwanie [Dowiedz się więcej](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/create/action | Tworzenie kotwic przestrzennych |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/delete | Usuwanie kotwic przestrzennych |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | Odnajdywanie pobliskich kotwic przestrzennych |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | Uzyskiwanie właściwości kotwic przestrzennych |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | Lokalizowanie kotwic przestrzennych |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | Przesyłanie danych diagnostycznych w celu poprawy jakości usługi Azure Spatial Anchors Service |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/write | Aktualizowanie właściwości kotwic przestrzennych |
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

### <a name="spatial-anchors-account-reader"></a>Spatial Anchors konta

Umożliwia lokalizowanie i odczytywanie właściwości kotwic przestrzennych na koncie Dowiedz [się więcej](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | Odnajdywanie pobliskich kotwic przestrzennych |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | Uzyskiwanie właściwości kotwic przestrzennych |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | Lokalizowanie kotwic przestrzennych |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | Przesyłanie danych diagnostycznych w celu poprawy jakości usługi Azure Spatial Anchors Service |
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


### <a name="api-management-service-contributor"></a>API Management współautora usługi

Może zarządzać usługą i interfejsami API [Dowiedz się więcej](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/* | Tworzenie usługi API Management zarządzanie |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie tym alertem |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

### <a name="api-management-service-operator-role"></a>API Management roli operatora usługi

Może zarządzać usługą, ale nie interfejsami API [Dowiedz się więcej](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/*/read | Odczytywanie API Management usługi |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/backup/action | Tworzenie API Management kopii zapasowej w określonym kontenerze na koncie magazynu dostarczonym przez użytkownika |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/delete | Usuwanie API Management usługi |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/managedeployments/action | Zmiana jednostki/jednostki SKU, dodawanie/usuwanie regionalnych wdrożeń usługi API Management Service |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/read | Odczytywanie metadanych dla wystąpienia API Management Service |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/restore/action | Przywracanie API Management z określonego kontenera na koncie magazynu podanym przez użytkownika |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/updatecertificate/action | Przekazywanie certyfikatu TLS/SSL dla usługi API Management Service |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/updatehostname/action | Konfigurowanie, aktualizowanie lub usuwanie niestandardowych nazw domen dla usługi API Management Service |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/write | Tworzenie lub aktualizowanie API Management service |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie tym alertem |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/users/keys/read | Uzyskiwanie kluczy skojarzonych z użytkownikiem |
> | **DataActions** |  |
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

### <a name="api-management-service-reader-role"></a>API Management roli czytelnika usługi

Dostęp tylko do odczytu do usługi i interfejsów API [Dowiedz się więcej](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/*/read | Odczytywanie API Management usługi |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/read | Odczytywanie metadanych dla wystąpienia API Management Service |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/users/keys/read | Uzyskiwanie kluczy skojarzonych z użytkownikiem |
> | **DataActions** |  |
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

### <a name="app-configuration-data-owner"></a>App Configuration właściciela danych

Umożliwia pełny dostęp do App Configuration danych. [Dowiedz się więcej](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/read |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/write |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/delete |  |
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

### <a name="app-configuration-data-reader"></a>App Configuration czytnik danych

Umożliwia dostęp do odczytu App Configuration danych. [Dowiedz się więcej](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/read |  |
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

### <a name="azure-service-bus-data-owner"></a>Azure Service Bus właściciel danych

Umożliwia pełny dostęp do Azure Service Bus zasobów. [Dowiedz się więcej](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/* |  |
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

### <a name="azure-service-bus-data-receiver"></a>Azure Service Bus Data Receiver

Umożliwia odbieranie dostępu do Azure Service Bus zasobów. [Dowiedz się więcej](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/queues/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/subscriptions/read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/receive/action |  |
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

### <a name="azure-service-bus-data-sender"></a>Azure Service Bus nadawcy danych

Umożliwia wysyłanie dostępu do Azure Service Bus zasobów. [Dowiedz się więcej](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/queues/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/subscriptions/read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/send/action |  |
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

### <a name="azure-stack-registration-owner"></a>Azure Stack właściciela rejestracji

Umożliwia zarządzanie Azure Stack rejestracji.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/edgeSubscriptions/read | Uzyskiwanie właściwości subskrypcji Azure Stack Edge subskrypcji |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/products/*/action |  |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/products/read | Pobiera właściwości produktu Azure Stack Marketplace |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/read | Pobiera właściwości Azure Stack rejestracji |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
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

### <a name="eventgrid-contributor"></a>Współautor eventGrid

Umożliwia zarządzanie operacjami eventGrid.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/* | Tworzenie zasobów Event Grid zarządzanie nimi |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie tym alertem |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

### <a name="eventgrid-eventsubscription-contributor"></a>EventGrid EventSubscription Contributor

Umożliwia zarządzanie operacjami subskrypcji zdarzeń usługi EventGrid. [Dowiedz się więcej](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/* | Tworzenie regionalnych subskrypcji zdarzeń i zarządzanie nimi |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/read | Lista subskrypcji zdarzeń globalnych według typu tematu |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/eventSubscriptions/read | Lista regionalnych subskrypcji zdarzeń |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/topicTypes/eventSubscriptions/read | Lista regionalnych subskrypcji zdarzeń według typu tematu |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
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

### <a name="eventgrid-eventsubscription-reader"></a>EventGrid EventSubscription Reader

Umożliwia odczytywanie subskrypcji zdarzeń usługi EventGrid. [Dowiedz się więcej](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/read | Odczytywanie subskrypcji zdarzenia |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/read | Lista subskrypcji zdarzeń globalnych według typu tematu |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/eventSubscriptions/read | Lista regionalnych subskrypcji zdarzeń |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/topicTypes/eventSubscriptions/read | Lista regionalnych subskrypcji zdarzeń według typu tematu |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

### <a name="fhir-data-contributor"></a>FHIR Data Contributor

Rola umożliwia użytkownikowi lub podmiotowi zabezpieczeń pełny dostęp do danych FHIR [Dowiedz się więcej](../healthcare-apis/fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/* |  |
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

### <a name="fhir-data-exporter"></a>Eksporter danych FHIR

Rola umożliwia użytkownikowi lub podmiotowi zabezpieczeń odczytywanie i eksportowanie danych FHIR [Dowiedz się więcej](../healthcare-apis/fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/read | Przeczytaj zasoby systemu FHIR (w tym historię wyszukiwania i wersji).  |
> | Microsoft.HealthcareApis/services/fhir/resources/export/action | Operacja eksportowania ($export). |
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
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/read | Przeczytaj zasoby FHIR (obejmuje wyszukiwanie i historię wersjonacyjną).  |
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

### <a name="fhir-data-writer"></a>FHIR Data Writer

Rola umożliwia użytkownikowi lub podmiotowi zabezpieczeń odczytywanie i zapis danych FHIR [Dowiedz się więcej](../healthcare-apis/fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/* |  |
> | **NotDataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/hardDelete/action | Usuwanie twarde (w tym historia wersji). |

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

### <a name="integration-service-environment-contributor"></a>środowisko usługi integracji współautora

Umożliwia zarządzanie środowiskami usługi integracji, ale nie zapewnia dostępu do nich. [Dowiedz się więcej](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

### <a name="integration-service-environment-developer"></a>środowisko usługi integracji dewelopera

Umożliwia deweloperom tworzenie i aktualizowanie przepływów pracy, kont integracji i połączeń interfejsu API w środowiskach usługi integracji. [Dowiedz się więcej](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/read | Odczytuje środowisko usługi integracji. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/*/join/action |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

Umożliwia zarządzanie kontami systemów inteligentnych, ale nie umożliwia dostępu do nich.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | Microsoft.IntelligentSystems/accounts/* | Tworzenie kont systemów inteligentnych i zarządzanie nimi |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
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

Umożliwia zarządzanie aplikacjami logiki, ale nie zmienia dostępu do nich. [Dowiedz się więcej](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | Wyświetla listę kluczy dostępu dla kont magazynu. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/read | Zwróć konto magazynu z danym kontem. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie tym alertem |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Tworzy, aktualizuje lub odczytuje ustawienie diagnostyczne dla Analysis Server |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/logdefinitions/* | To uprawnienie jest niezbędne dla użytkowników, którzy potrzebują dostępu do dzienników aktywności za pośrednictwem portalu. Lista kategorii dzienników w dzienniku aktywności. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/* | Odczytywanie definicji metryk (lista dostępnych typów metryk dla zasobu). |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/* | Zarządza Logic Apps zasobów. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Pobierz wyniki operacji subskrypcji. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/action | Zwraca klucze dostępu dla określonego konta magazynu. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connectionGateways/* | Tworzenie bramy połączeń i zarządzanie nimi. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connections/* | Tworzenie połączenia i zarządzanie nimi. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/customApis/* | Tworzy niestandardowy interfejs API i zarządza nimi. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/join/action | Dołącza App Service planu |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/read | Uzyskiwanie właściwości w planie App Service aplikacji |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/sites/functions/listSecrets/action | Lista wpisów tajnych funkcji. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
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

Umożliwia odczytywanie, włączanie i wyłączanie aplikacji logiki, ale nie ich edytowanie ani aktualizowanie. [Dowiedz się więcej](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/*/read | Odczytywanie reguł alertów szczegółowych informacji |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/*/read | Pobiera ustawienia diagnostyczne dla Logic Apps |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/*/read | Pobiera dostępne metryki dla Logic Apps. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/*/read | Odczytuje Logic Apps zasobów. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/disable/action | Wyłącza przepływ pracy. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/enable/action | Włącza przepływ pracy. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/validate/action | Weryfikuje przepływ pracy. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Pobiera lub wyświetla listę operacji wdrażania. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Pobierz wyniki operacji subskrypcji. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connectionGateways/*/read | Odczyt bramy połączeń. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connections/*/read | Odczyt połączeń. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/customApis/*/read | Przeczytaj temat Custom API (Niestandardowy interfejs API). |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/read | Uzyskiwanie właściwości w planie App Service aplikacji |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
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
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/read | Pobiera istniejącą tożsamość przypisaną przez użytkownika |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/write | Tworzy nową tożsamość przypisaną przez użytkownika lub aktualizuje tagi skojarzone z istniejącą tożsamością przypisaną przez użytkownika |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/delete | Usuwa istniejącą tożsamość przypisaną przez użytkownika |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

Przeczytaj i przypisz tożsamość przypisaną przez użytkownika [Dowiedz się więcej](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/read |  |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/assign/action |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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


### <a name="attestation-contributor"></a>Współautor zaświadczenia

Może odczytywać i usuwać wystąpienie dostawcy zaświadczenia Dowiedz [się więcej](../attestation/quickstart-powershell.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | Microsoft.Attestation/attestationProviders/attestation/read |  |
> | Microsoft.Attestation/attestationProviders/attestation/write |  |
> | Microsoft.Attestation/attestationProviders/attestation/delete |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
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

### <a name="attestation-reader"></a>Czytnik zaswiadczenia

Może odczytywać właściwości dostawcy zaświadczenia Dowiedz [się więcej](../attestation/troubleshoot-guide.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | Microsoft.Attestation/attestationProviders/attestation/read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

### <a name="azure-sentinel-automation-contributor"></a>Azure Sentinel współautor usługi Automation

Azure Sentinel Automation Dowiedz [się więcej](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/triggers/read | Odczytuje wyzwalacz. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/triggers/listCallbackUrl/action | Pobiera adres URL wywołania zwrotnego dla wyzwalacza. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/runs/read | Odczytuje przebieg przepływu pracy. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Automation Contributor",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f4c81013-99ee-4d62-a7ee-b3f1f648599a",
  "name": "f4c81013-99ee-4d62-a7ee-b3f1f648599a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Logic/workflows/triggers/read",
        "Microsoft.Logic/workflows/triggers/listCallbackUrl/action",
        "Microsoft.Logic/workflows/runs/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Automation Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-contributor"></a>Współautor usługi Azure Sentinel

Azure Sentinel Współautor [Dowiedz się więcej](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/* |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | Wyszukiwanie przy użyciu nowego aparatu. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Wyświetlanie danych analizy dzienników |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/* |  |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | Uzyskiwanie wychodzącego rozwiązania OMS |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/read | Uruchamianie zapytań dotyczących danych w obszarze roboczym |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/*/read |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | Uzyskiwanie źródeł danych w obszarze roboczym. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/read | Odczytywanie prywatnego skoroszytu |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie tym alertem |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

Azure Sentinel Reader [Dowiedz się więcej](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/read |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/action | Sprawdzanie autoryzacji użytkownika i licencji |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/query/action | Wykonywanie zapytań o wskaźniki analizy zagrożeń |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/queryIndicators/action | Wykonywanie zapytań o wskaźniki analizy zagrożeń |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | Wyszukiwanie przy użyciu nowego aparatu. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Wyświetlanie danych analizy dzienników |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/LinkedServices/read | Pobierz połączone usługi w ramach danego obszaru roboczego. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/read | Pobiera zapisane zapytanie wyszukiwania |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | Uzyskiwanie wychodzącego rozwiązania OMS |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/read | Uruchamianie zapytań dotyczących danych w obszarze roboczym |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/*/read |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | Uzyskiwanie źródeł danych w obszarze roboczym. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | Odczytywanie skoroszytu |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/read | Odczytywanie prywatnego skoroszytu |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie tym alertem |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

Azure Sentinel responder [Dowiedz się więcej](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/read |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/action | Sprawdzanie autoryzacji użytkownika i licencji |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/automationRules/* |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/cases/* |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/incidents/* |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/appendTags/action | Dołączanie tagów do wskaźnika analizy zagrożeń |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/query/action | Wykonywanie zapytań o wskaźniki analizy zagrożeń |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/bulkTag/action | Analizy zagrożeń tagów zbiorczych |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/appendTags/action | Dołączanie tagów do wskaźnika analizy zagrożeń |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/replaceTags/action | Zastępowanie tagów wskaźnika analizy zagrożeń |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/queryIndicators/action | Wykonywanie zapytań o wskaźniki analizy zagrożeń |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | Wyszukiwanie przy użyciu nowego aparatu. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Wyświetlanie danych analizy dzienników |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | Uzyskiwanie źródeł danych w obszarze roboczym. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/read | Pobiera zapisane zapytanie wyszukiwania |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | Zakończenie pracy z rozwiązaniem OMS |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/read | Uruchamianie zapytań dotyczących danych w obszarze roboczym |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/*/read |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | Uzyskiwanie źródeł danych w obszarze roboczym. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | Odczytywanie skoroszytu |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/read | Odczytywanie prywatnego skoroszytu |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/cases/*/Delete |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/incidents/*/Delete |  |
> | **DataActions** |  |
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

### <a name="key-vault-administrator"></a>Key Vault administratora

Wykonywanie wszystkich operacji płaszczyzny danych w magazynie kluczy i wszystkich znajdujących się w nim obiektów, w tym certyfikatów, kluczy i wpisów tajnych. Nie można zarządzać zasobami magazynu kluczy ani zarządzać przypisaniami ról. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "Kontrola dostępu na podstawie ról" platformy Azure.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Sprawdza, czy nazwa magazynu kluczy jest prawidłowa i nie jest w użyciu |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | Wyświetlanie właściwości magazynów kluczy usuniętych nieskojalnie |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Wyświetla listę operacji dostępnych w dostawcy zasobów Microsoft.KeyVault |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/* |  |
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

### <a name="key-vault-certificates-officer"></a>Key Vault certyfikatów

Wykonaj dowolną akcję na certyfikatach magazynu kluczy, z wyjątkiem zarządzania uprawnieniami. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "Kontrola dostępu na podstawie ról" platformy Azure.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Sprawdza, czy nazwa magazynu kluczy jest prawidłowa i nie jest w użyciu |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | Wyświetlanie właściwości magazynów kluczy z usuniętymi nieukońcami |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Wyświetla listę operacji dostępnych w dostawcy zasobów Microsoft.KeyVault |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/certificatecas/* |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/certificates/* |  |
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

### <a name="key-vault-contributor"></a>Key Vault współautora

Zarządzanie magazynami kluczy, ale nie pozwala na przypisywanie ról w ramach kontroli dostępu na rolach platformy Azure i nie pozwala na dostęp do wpisów tajnych, kluczy ani certyfikatów. [Dowiedz się więcej](../key-vault/general/secure-your-key-vault.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie tym alertem |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/deletedVaults/purge/action | Przeczyszczanie nieukońcowego magazynu kluczy |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/hsmPools/* |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/managedHsms/* |  |
> | **DataActions** |  |
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

### <a name="key-vault-crypto-officer"></a>Key Vault Crypto Officer

Wykonaj dowolną akcję na kluczach magazynu kluczy, z wyjątkiem zarządzania uprawnieniami. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "Kontrola dostępu na podstawie ról" platformy Azure.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Sprawdza, czy nazwa magazynu kluczy jest prawidłowa i nie jest w użyciu |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | Wyświetlanie właściwości magazynów kluczy z usuniętymi nieukońcami |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Wyświetla listę operacji dostępnych w dostawcy zasobów Microsoft.KeyVault |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/* |  |
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

### <a name="key-vault-crypto-service-encryption-user"></a>Key Vault szyfrowania usługi kryptograficznych

Odczytywanie metadanych kluczy i wykonywanie operacji opakowuj/odpakowuj. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "Kontrola dostępu na podstawie ról" platformy Azure.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/write | Tworzenie lub aktualizowanie subskrypcji zdarzeń |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/read | Odczytywanie subskrypcji zdarzenia |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/delete | Usuwanie subskrypcji zdarzeń |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/read | Wyświetlić listę kluczy w określonym magazynie lub odczytać właściwości i materiał publiczny klucza. W przypadku kluczy asymetrycznych ta operacja ujawnia klucz publiczny i umożliwia wykonywanie algorytmów kluczy publicznych, takich jak szyfrowanie i weryfikowanie podpisu. Klucze prywatne i klucze symetryczne nigdy nie są udostępniane. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/wrap/action | Opakowywuje klucz symetryczny za pomocą Key Vault klucza. Należy pamiętać, że jeśli Key Vault jest asymetryczny, ta operacja może zostać wykonana przez podmioty zabezpieczeń z dostępem do odczytu. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/unwrap/action | Odpakuje klucz symetryczny za pomocą Key Vault klucza. |
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

### <a name="key-vault-crypto-user"></a>Key Vault użytkownik kryptograficzny

Wykonywanie operacji kryptograficznych przy użyciu kluczy. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "Kontrola dostępu na podstawie ról" platformy Azure.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/read | Wyświetlić listę kluczy w określonym magazynie lub odczytać właściwości i materiał publiczny klucza. W przypadku kluczy asymetrycznych ta operacja uwidacznia klucz publiczny i umożliwia wykonywanie algorytmów kluczy publicznych, takich jak szyfrowanie i weryfikowanie podpisu. Klucze prywatne i klucze symetryczne nigdy nie są udostępniane. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/update/action | Aktualizuje określone atrybuty skojarzone z danym kluczem. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/backup/action | Tworzy plik kopii zapasowej klucza. Plik może służyć do przywracania klucza w Key Vault tej samej subskrypcji. Mogą obowiązywać ograniczenia. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/encrypt/action | Szyfruje zwykły tekst kluczem. Należy pamiętać, że jeśli klucz jest asymetryczny, ta operacja może zostać wykonana przez podmioty zabezpieczeń z dostępem do odczytu. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/decrypt/action | Odszyfrowuje szyfrowany tekst za pomocą klucza. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/wrap/action | Opakowywuje klucz symetryczny za pomocą Key Vault klucza. Pamiętaj, że jeśli klucz Key Vault jest asymetryczny, ta operacja może zostać wykonana przez podmioty zabezpieczeń z dostępem do odczytu. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/unwrap/action | Odpakowywa klucz symetryczny za pomocą Key Vault klucza. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/sign/action | Podpisuje skrót wiadomości kluczem. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/verify/action | Weryfikuje podpis skrótu wiadomości za pomocą klucza. Należy pamiętać, że jeśli klucz jest asymetryczny, ta operacja może zostać wykonana przez podmioty zabezpieczeń z dostępem do odczytu. |
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

### <a name="key-vault-reader"></a>Key Vault czytelnika

Odczytywanie metadanych magazynów kluczy i ich certyfikatów, kluczy i wpisów tajnych. Nie można odczytać poufnych wartości, takich jak zawartość klucza tajnego lub materiał klucza. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "Kontrola dostępu na podstawie ról" platformy Azure.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Sprawdza, czy nazwa magazynu kluczy jest prawidłowa i nie jest w użyciu |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | Wyświetlanie właściwości magazynów kluczy z usuniętymi nieukońcami |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Wyświetla listę operacji dostępnych w dostawcy zasobów Microsoft.KeyVault |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/readMetadata/action | Wyświetl listę lub właściwości tajnego, ale nie jego wartość. |
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

### <a name="key-vault-secrets-officer"></a>Key Vault Secrets Officer

Wykonaj dowolną akcję dla wpisów tajnych magazynu kluczy, z wyjątkiem zarządzania uprawnieniami. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "Kontrola dostępu na podstawie ról" platformy Azure.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie tym alertem |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Sprawdza, czy nazwa magazynu kluczy jest prawidłowa i nie jest w użyciu |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | Wyświetlanie właściwości magazynów kluczy usuniętych nie soft |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Wyświetla listę operacji dostępnych w dostawcy zasobów Microsoft.KeyVault |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/* |  |
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

### <a name="key-vault-secrets-user"></a>Key Vault wpisów tajnych

Odczytywanie zawartości tajnej. Działa tylko w przypadku magazynów kluczy, które korzystają z modelu uprawnień "Kontrola dostępu na podstawie ról" platformy Azure.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/getSecret/action | Pobiera wartość tajnego. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/readMetadata/action | Wyświetl listę lub właściwości tajnego, ale nie jego wartość. |
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

Umożliwia zarządzanie zarządzanymi pulami modułów HSM, ale nie umożliwia dostępu do nich. [Dowiedz się więcej](../key-vault/managed-hsm/secure-your-managed-hsm.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/managedHSMs/* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

Wyświetlanie i aktualizowanie uprawnień dla Security Center. Takie same uprawnienia jak rola Czytelnik zabezpieczeń, a także może aktualizować zasady zabezpieczeń i odrzucać alerty i zalecenia. [Dowiedz się więcej](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyAssignments/* | Tworzenie przypisań zasad i zarządzanie nimi |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyDefinitions/* | Tworzenie definicji zasad i zarządzanie nimi |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyExemptions/* | Tworzenie wyjątków zasad i zarządzanie nimi |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policySetDefinitions/* | Tworzenie zestawów zasad i zarządzanie nimi |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie tym alertem |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Lista grup zarządzania dla uwierzytelnionego użytkownika. |
> | [Microsoft.operationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Wyświetlanie danych analizy dzienników |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/* | Tworzenie składników zabezpieczeń i zasad oraz zarządzanie nimi |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/assessments/write | Tworzenie lub aktualizowanie ocen zabezpieczeń w ramach subskrypcji |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

### <a name="security-manager-legacy"></a>Menedżer zabezpieczeń (starsza wersja)

Jest to starsza rola. Zamiast tego użyj administratora zabezpieczeń.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/*/read | Odczytywanie informacji o konfiguracji klasycznych maszyn wirtualnych |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/*/write | Konfiguracja zapisu dla klasycznych maszyn wirtualnych |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/*/read | Przeczytaj informacje o konfiguracji dotyczące sieci klasycznej |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie tym alertem |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/* | Tworzenie składników zabezpieczeń i zasad oraz zarządzanie nimi |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

Wyświetlanie uprawnień dla Security Center. Może wyświetlać zalecenia, alerty, zasady zabezpieczeń i stany zabezpieczeń, ale nie może wprowadzać zmian. [Dowiedz się więcej](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Odczytywanie alertu metryki klasycznej |
> | [Microsoft.operationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Wyświetlanie danych analizy dzienników |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/*/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/*/read | Odczytywanie składników i zasad zabezpieczeń |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/*/read |  |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/iotDefenderSettings/packageDownloads/action | Pobiera informacje o pakietach usługi IoT Defender do pobrania |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/iotDefenderSettings/downloadManagerActivation/action | Pobieranie pliku aktywacji menedżera z danymi limitu przydziału subskrypcji |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/iotSensors/downloadResetPassword/action | Pobieranie pliku resetowania hasła dla czujników IoT |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Lista grup zarządzania dla uwierzytelnionego użytkownika. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
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


### <a name="devtest-labs-user"></a>Użytkownik laboratorium DevTest Labs

Umożliwia łączenie, uruchamianie, ponowne uruchamianie i zamykanie maszyn wirtualnych w Azure DevTest Labs. [Dowiedz się więcej](../devtest-labs/devtest-lab-add-devtest-user.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/read | Uzyskiwanie właściwości zestawu dostępności |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/read | Odczytywanie właściwości maszyny wirtualnej (rozmiary maszyn wirtualnych, stan środowiska uruchomieniowego, rozszerzenia maszyn wirtualnych itp.) |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/deallocate/action | Wyłącza maszynę wirtualną i zwalnia zasoby obliczeniowe |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/read | Uzyskiwanie właściwości maszyny wirtualnej |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/restart/action | Ponowne uruchamianie maszyny wirtualnej |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/start/action | Uruchamia maszynę wirtualną |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/*/read | Odczytywanie właściwości laboratorium |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/claimAnyVm/action | Przejmij losową wymierną maszynę wirtualną w laboratorium. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/createEnvironment/action | Tworzenie maszyn wirtualnych w laboratorium. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/ensureCurrentUserProfile/action | Upewnij się, że bieżący użytkownik ma prawidłowy profil w laboratorium. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/delete | Usuwanie formuł. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/read | Odczytywanie formuł. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/write | Dodawanie lub modyfikowanie formuł. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/policySets/evaluatePolicies/action | Ocenia zasady laboratorium. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualMachines/claim/action | Przejmij na własność istniejącą maszynę wirtualną |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualmachines/listApplicableSchedules/action | Wyświetla listę odpowiednich harmonogramów uruchamiania/zatrzymania, jeśli takie są. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualMachines/getRdpFileContents/action | Pobiera ciąg reprezentujący zawartość pliku RDP dla maszyny wirtualnej |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/backendAddressPools/join/action | Dołącza pulę adresów zaplecza równoważenia obciążenia. Nie ma alertu. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatRules/join/action | Dołącza regułę nat dla ruchu przychodzącego do równoważenia obciążenia. Nie ma alertu. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/*/read | Odczytywanie właściwości interfejsu sieciowego (na przykład wszystkich usług równoważenia obciążenia, do których należy interfejs sieciowy) |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/join/action | Dołącza maszynę wirtualną do interfejsu sieciowego. Nie ma alertu. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | Pobiera definicję interfejsu sieciowego.  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/write | Tworzy interfejs sieciowy lub aktualizuje istniejący interfejs sieciowy.  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/*/read | Odczytywanie właściwości publicznego adresu IP |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/join/action | Dołącza publiczny adres IP. Nie ma alertu. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | Pobiera definicję publicznego adresu IP. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | Dołącza do sieci wirtualnej. Nie ma alertu. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Pobiera lub wyświetla listę operacji wdrażania. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | Pobiera lub wyświetla listę wdrożeń. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | Zwraca klucze dostępu dla określonego konta magazynu. |
> | **NotActions** |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/vmSizes/read | Wyświetla dostępne rozmiary, do których można zaktualizować maszynę wirtualną |
> | **DataActions** |  |
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

Umożliwia tworzenie nowych laboratoriów w ramach kont laboratorium platformy Azure. [Dowiedz się więcej](../lab-services/add-lab-creator.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/*/read |  |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/createLab/action | Tworzenie laboratorium na koncie laboratorium. |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getPricingAndAvailability/action | Uzyskaj ceny i dostępność kombinacji rozmiarów, lokalizacji geograficznych i systemów operacyjnych dla konta laboratorium. |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getRestrictionsAndUsage/action | Uzyskiwanie podstawowych ograniczeń i użycia dla tej subskrypcji |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
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


### <a name="application-insights-component-contributor"></a>Application Insights współautor składników

Może zarządzać składnikami Application Insights Dowiedz [się więcej](../azure-monitor/app/resources-roles-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie reguł alertów klasycznych i zarządzanie nimi |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/generateLiveToken/read | Uzyskiwanie tokenu metryk na żywo |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/* | Tworzenie nowych reguł alertów i zarządzanie nimi |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/* | Tworzenie składników usługi Insights i zarządzanie nimi |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/scheduledqueryrules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/topology/read | Topologia odczytu |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/transactions/read | Transakcje odczytu |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/webtests/* | Tworzenie testów sieci Web usługi Insights i zarządzanie nimi |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

Daje użytkownikowi uprawnienia do wyświetlania i pobierania migawek debugowania zebranych za pomocą Application Insights Snapshot Debugger. Należy pamiętać, że te uprawnienia nie są uwzględnione w [rolach Właściciel](#owner) [ani Współautor.](#contributor) Podczas nadawania użytkownikom Application Insights Snapshot Debugger roli musisz przyznać tę rolę bezpośrednio użytkownikowi. Rola nie jest rozpoznawana, gdy zostanie dodana do roli niestandardowej. [Dowiedz się więcej](../azure-monitor/app/snapshot-debugger.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie tym alertem |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/*/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

Może odczytywać wszystkie dane monitorowania i edytować ustawienia monitorowania. Zobacz też [wprowadzenie do ról, uprawnień i zabezpieczeń za pomocą usługi Azure Monitor.](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles) [Dowiedz się więcej](../azure-monitor/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | */odczyt | Odczytywanie zasobów wszystkich typów, z wyjątkiem wpisów tajnych. |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/alerts/* |  |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/alertsSummary/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/actiongroups/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/activityLogAlerts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/AlertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/* | Tworzenie składników usługi Insights i zarządzanie nimi |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/dataCollectionRules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/dataCollectionRuleAssociations/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/DiagnosticSettings/* | Tworzy, aktualizuje lub odczytuje ustawienie diagnostyczne dla Analysis Server |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/eventtypes/* | Lista zdarzeń dziennika aktywności (zdarzeń zarządzania) w subskrypcji. To uprawnienie ma zastosowanie zarówno do dostępu programowego, jak i do portalu do dziennika aktywności. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/LogDefinitions/* | To uprawnienie jest niezbędne dla użytkowników, którzy potrzebują dostępu do dzienników aktywności za pośrednictwem portalu. Lista kategorii dzienników w dzienniku aktywności. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricalerts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/* | Odczytywanie definicji metryk (lista dostępnych typów metryk dla zasobu). |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Metrics/* | Odczytywanie metryk dla zasobu. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Register/Action | Rejestrowanie dostawcy usługi Microsoft Insights |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/scheduledqueryrules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/webtests/* | Tworzenie testów sieci Web usługi Insights i zarządzanie nimi |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/privateLinkScopes/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/privateLinkScopeOperationStatuses/* |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/write | Tworzy nowy obszar roboczy lub tworzy linki do istniejącego obszaru roboczego, podając identyfikator klienta z istniejącego obszaru roboczego. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/intelligencepacks/* | Odczytywanie/zapis/usuwanie pakietów rozwiązań usługi Log Analytics. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/* | Odczyt/zapis/usuwanie zapisanych wyszukiwań usługi Log Analytics. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | Wykonuje zapytanie wyszukiwania |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/sharedKeys/action | Pobiera klucze udostępnione dla obszaru roboczego. Te klucze są używane do łączenia agentów Operational Insights Microsoft z obszarem roboczym. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/storageinsightconfigs/* | Odczyt/zapis/usuwanie konfiguracji szczegółowych informacji magazynu usługi Log Analytics. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft.WorkloadMonitor](resource-provider-operations.md#microsoftworkloadmonitor)/monitors/* | Uzyskaj informacje o monitorach kondycji maszyny wirtualnej gościa. |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/smartDetectorAlertRules/* |  |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/actionRules/* |  |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/smartGroups/* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

Umożliwia publikowanie metryk dotyczących zasobów platformy Azure [Dowiedz się więcej](../azure-monitor/insights/container-insights-update-metrics.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Register/Action | Rejestrowanie dostawcy usługi Microsoft Insights |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Write | Pisanie metryk |
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

### <a name="monitoring-reader"></a>Czytelnik monitorowania

Może odczytywać wszystkie dane monitorowania (metryki, dzienniki itp.). Zobacz też [wprowadzenie do ról, uprawnień](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles)i zabezpieczeń za pomocą Azure Monitor . [Dowiedz się więcej](../azure-monitor/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | */read | Odczytywanie zasobów wszystkich typów, z wyjątkiem wpisów tajnych. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | Wykonuje zapytanie wyszukiwania |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/write | Tworzenie lub aktualizowanie skoroszytu |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/delete | Usuwanie skoroszytu |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | Odczytywanie skoroszytu |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

### <a name="workbook-reader"></a>Czytelnik skoroszytów

Może odczytywać skoroszyty. [Dowiedz się więcej](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [microsoft.insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | Odczytywanie skoroszytu |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

## <a name="management--governance"></a>Zarządzanie i ład


### <a name="automation-job-operator"></a>Operator zadania usługi Automation

Tworzenie zadań i zarządzanie nimi przy użyciu automatyzacji runbook. [Dowiedz się więcej](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/hybridRunbookWorkerGroups/read | Odczytuje zasoby hybrydowego procesu roboczego runbook |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/read | Pobiera zadanie Azure Automation zadań |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/resume/action | Wznawia zadanie Azure Automation zadań |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/stop/action | Zatrzymuje zadanie Azure Automation zadań |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/streams/read | Pobiera strumień Azure Automation zadania |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/suspend/action | Wstrzymuje Azure Automation zadań |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/write | Tworzy zadanie Azure Automation zadań |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/output/read | Pobiera dane wyjściowe zadania |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie tym alertem |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

Operatorzy automatyzacji mogą uruchamiać, zatrzymywać, wstrzymywać i wznawiać zadania [Dowiedz się więcej](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/hybridRunbookWorkerGroups/read | Odczytuje zasoby hybrydowego procesu roboczego runbook |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/read | Pobiera zadanie Azure Automation zadań |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/resume/action | Wznawia zadanie Azure Automation zadań |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/stop/action | Zatrzymuje zadanie Azure Automation zadań |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/streams/read | Pobiera strumień Azure Automation zadania |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/suspend/action | Wstrzymuje Azure Automation zadań |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/write | Tworzy zadanie Azure Automation zadań |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobSchedules/read | Pobiera harmonogram Azure Automation zadań |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobSchedules/write | Tworzy harmonogram Azure Automation zadań |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/linkedWorkspace/read | Pobiera obszar roboczy połączony z kontem usługi Automation |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/read | Pobiera Azure Automation konto |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/runbooks/read | Pobiera Azure Automation Runbook |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/schedules/read | Pobiera zasób Azure Automation harmonogramu |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/schedules/write | Tworzy lub aktualizuje zasób Azure Automation harmonogramu |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie tym alertem |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/output/read | Pobiera dane wyjściowe zadania |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

### <a name="automation-runbook-operator"></a>Automation Runbook Operator

Odczytywanie właściwości runbook — aby móc tworzyć zadania tego typu. [Dowiedz się więcej](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/runbooks/read | Pobiera Azure Automation Runbook |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

### <a name="azure-arc-enabled-kubernetes-cluster-user-role"></a>Azure Arc użytkownika klastra Kubernetes z włączoną obsługą

Lista akcji poświadczeń użytkownika klastra.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Tworzy lub aktualizuje wdrożenie. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Pobierz wyniki operacji subskrypcji. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Pobiera listę subskrypcji. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/listClusterUserCredentials/action | Lista poświadczeń użytkownika klastra |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | *brak* |  |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster user credentials action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00493d72-78f6-4148-b6c5-d3ce8e4799dd",
  "name": "00493d72-78f6-4148-b6c5-d3ce8e4799dd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Kubernetes/connectedClusters/listClusterUserCredentials/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Enabled Kubernetes Cluster User Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-arc-kubernetes-admin"></a>Azure Arc Kubernetes Admin

Umożliwia zarządzanie wszystkimi zasobami w obszarze klastra/przestrzeni nazw, z wyjątkiem aktualizowania lub usuwania przydziałów zasobów i przestrzeni nazw. [Dowiedz się więcej](../azure-arc/kubernetes/azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Tworzy lub aktualizuje wdrożenie. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Pobierz wyniki operacji subskrypcji. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Pobiera listę subskrypcji. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/controllerrevisions/read | Kontroler odczyturevisions |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/daemonsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/deployments/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/replicasets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/statefulsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/authorization.k8s.io/localsubjectaccessreviews/write | Zapisuje localsubjectaccessreviews |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/autoscaling/horizontalpodautoscalers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/cronjobs/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/jobs/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/configmaps/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/endpoints/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events.k8s.io/events/read | Odczytuje zdarzenia |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events/read | Odczytuje zdarzenia |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/daemonsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/deployments/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/ingresses/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/networkpolicies/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/replicasets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/limitranges/read | Limity odczytów |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/namespaces/read | Odczytuje przestrzenie nazw |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/ingresses/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/networkpolicies/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/persistentvolumeclaims/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/pods/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/policy/poddisruptionbudgets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/rbac.authorization.k8s.io/rolebindings/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/rbac.authorization.k8s.io/roles/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/resourcequotas/read | Odczytuje resourcequotas |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/secrets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/serviceaccounts/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/services/* |  |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources under cluster/namespace, except update or delete resource quotas and namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/dffb1e0c-446f-4dde-a09f-99eb5cc68b96",
  "name": "dffb1e0c-446f-4dde-a09f-99eb5cc68b96",
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
        "Microsoft.Kubernetes/connectedClusters/apps/controllerrevisions/read",
        "Microsoft.Kubernetes/connectedClusters/apps/daemonsets/*",
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/*",
        "Microsoft.Kubernetes/connectedClusters/apps/replicasets/*",
        "Microsoft.Kubernetes/connectedClusters/apps/statefulsets/*",
        "Microsoft.Kubernetes/connectedClusters/authorization.k8s.io/localsubjectaccessreviews/write",
        "Microsoft.Kubernetes/connectedClusters/autoscaling/horizontalpodautoscalers/*",
        "Microsoft.Kubernetes/connectedClusters/batch/cronjobs/*",
        "Microsoft.Kubernetes/connectedClusters/batch/jobs/*",
        "Microsoft.Kubernetes/connectedClusters/configmaps/*",
        "Microsoft.Kubernetes/connectedClusters/endpoints/*",
        "Microsoft.Kubernetes/connectedClusters/events.k8s.io/events/read",
        "Microsoft.Kubernetes/connectedClusters/events/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/daemonsets/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/deployments/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/ingresses/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/networkpolicies/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/replicasets/*",
        "Microsoft.Kubernetes/connectedClusters/limitranges/read",
        "Microsoft.Kubernetes/connectedClusters/namespaces/read",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/ingresses/*",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/networkpolicies/*",
        "Microsoft.Kubernetes/connectedClusters/persistentvolumeclaims/*",
        "Microsoft.Kubernetes/connectedClusters/pods/*",
        "Microsoft.Kubernetes/connectedClusters/policy/poddisruptionbudgets/*",
        "Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/rolebindings/*",
        "Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/roles/*",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/*",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/*",
        "Microsoft.Kubernetes/connectedClusters/resourcequotas/read",
        "Microsoft.Kubernetes/connectedClusters/secrets/*",
        "Microsoft.Kubernetes/connectedClusters/serviceaccounts/*",
        "Microsoft.Kubernetes/connectedClusters/services/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Kubernetes Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-arc-kubernetes-cluster-admin"></a>Azure Arc administratorem klastra Kubernetes

Umożliwia zarządzanie wszystkimi zasobami w klastrze. [Dowiedz się więcej](../azure-arc/kubernetes/azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie tym alertem |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Tworzy lub aktualizuje wdrożenie. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Pobierz wyniki operacji subskrypcji. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Pobiera listę subskrypcji. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/* |  |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources in the cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8393591c-06b9-48a2-a542-1bd6b377f6a2",
  "name": "8393591c-06b9-48a2-a542-1bd6b377f6a2",
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
        "Microsoft.Kubernetes/connectedClusters/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Kubernetes Cluster Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-arc-kubernetes-viewer"></a>Azure Arc podglądu kubernetes

Umożliwia wyświetlanie wszystkich zasobów w klastrze/przestrzeni nazw, z wyjątkiem wpisów tajnych. [Dowiedz się więcej](../azure-arc/kubernetes/azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Tworzy lub aktualizuje wdrożenie. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Pobierz wyniki operacji subskrypcji. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Pobiera listę subskrypcji. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/controllerrevisions/read | Kontroler odczyturevisions |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/daemonsets/read | Odczytuje demony |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/deployments/read | Odczytuje wdrożenia |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/replicasets/read | Odczytuje zestawy replik |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/statefulsets/read | Odczytuje zestawy stanowe |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/autoscaling/horizontalpodautoscalers/read | Odczyty horizontalpodautoscalers |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/cronjobs/read | Odczytuje cronjobs |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/jobs/read | Odczytuje zadania |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/configmaps/read | Odczytuje mapy konfiguracji |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/endpoints/read | Odczytuje punkty końcowe |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events.k8s.io/events/read | Odczytuje zdarzenia |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events/read | Odczytuje zdarzenia |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/daemonsets/read | Odczytuje demony |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/deployments/read | Odczytuje wdrożenia |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/ingresses/read | Odczyty danych przychodzących |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/networkpolicies/read | Odczytuje networkpolicies |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/replicasets/read | Odczytuje zestawy replik |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/limitranges/read | Limity odczytów |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/namespaces/read | Odczytuje przestrzenie nazw |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/ingresses/read | Odczytuje ruch przychodzący |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/networkpolicies/read | Odczytuje networkpolicies |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/persistentvolumeclaims/read | Odczytuje persistentvolumeclaims |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/pods/read | Odczytuje zasobniki |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/policy/poddisruptionbudgets/read | Odczytuje zasobniki poddisruptionbudgets |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/read | Odczytuje kontrolery replikacji |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/read | Odczytuje kontrolery replikacji |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/resourcequotas/read | Odczytuje resourcequotas |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/serviceaccounts/read | Odczytuje konta usług |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/services/read | Odczytuje usługi |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view all resources in cluster/namespace, except secrets.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/63f0a09d-1495-4db4-a681-037d84835eb4",
  "name": "63f0a09d-1495-4db4-a681-037d84835eb4",
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
        "Microsoft.Kubernetes/connectedClusters/apps/controllerrevisions/read",
        "Microsoft.Kubernetes/connectedClusters/apps/daemonsets/read",
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/read",
        "Microsoft.Kubernetes/connectedClusters/apps/replicasets/read",
        "Microsoft.Kubernetes/connectedClusters/apps/statefulsets/read",
        "Microsoft.Kubernetes/connectedClusters/autoscaling/horizontalpodautoscalers/read",
        "Microsoft.Kubernetes/connectedClusters/batch/cronjobs/read",
        "Microsoft.Kubernetes/connectedClusters/batch/jobs/read",
        "Microsoft.Kubernetes/connectedClusters/configmaps/read",
        "Microsoft.Kubernetes/connectedClusters/endpoints/read",
        "Microsoft.Kubernetes/connectedClusters/events.k8s.io/events/read",
        "Microsoft.Kubernetes/connectedClusters/events/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/daemonsets/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/deployments/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/ingresses/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/networkpolicies/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/replicasets/read",
        "Microsoft.Kubernetes/connectedClusters/limitranges/read",
        "Microsoft.Kubernetes/connectedClusters/namespaces/read",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/ingresses/read",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/networkpolicies/read",
        "Microsoft.Kubernetes/connectedClusters/persistentvolumeclaims/read",
        "Microsoft.Kubernetes/connectedClusters/pods/read",
        "Microsoft.Kubernetes/connectedClusters/policy/poddisruptionbudgets/read",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/read",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/read",
        "Microsoft.Kubernetes/connectedClusters/resourcequotas/read",
        "Microsoft.Kubernetes/connectedClusters/serviceaccounts/read",
        "Microsoft.Kubernetes/connectedClusters/services/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Kubernetes Viewer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-arc-kubernetes-writer"></a>Azure Arc Kubernetes Writer

Umożliwia aktualizowanie wszystkiego w klastrze/przestrzeni nazw, z wyjątkiem ról (klastra) i powiązań ról (klastra). [Dowiedz się więcej](../azure-arc/kubernetes/azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Tworzy lub aktualizuje wdrożenie. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Pobierz wyniki operacji subskrypcji. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Pobiera listę subskrypcji. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/controllerrevisions/read | Odczytuje kontrolerrevisions |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/daemonsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/deployments/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/replicasets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/statefulsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/autoscaling/horizontalpodautoscalers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/cronjobs/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/jobs/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/configmaps/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/endpoints/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events.k8s.io/events/read | Odczytuje zdarzenia |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events/read | Odczytuje zdarzenia |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/daemonsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/deployments/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/ingresses/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/networkpolicies/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/replicasets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/limitranges/read | Limity odczytów |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/namespaces/read | Odczytuje przestrzenie nazw |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/ingresses/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/networkpolicies/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/persistentvolumeclaims/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/pods/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/policy/poddisruptionbudgets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/resourcequotas/read | Odczytuje resourcequotas |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/secrets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/serviceaccounts/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/services/* |  |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you update everything in cluster/namespace, except (cluster)roles and (cluster)role bindings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5b999177-9696-4545-85c7-50de3797e5a1",
  "name": "5b999177-9696-4545-85c7-50de3797e5a1",
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
        "Microsoft.Kubernetes/connectedClusters/apps/controllerrevisions/read",
        "Microsoft.Kubernetes/connectedClusters/apps/daemonsets/*",
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/*",
        "Microsoft.Kubernetes/connectedClusters/apps/replicasets/*",
        "Microsoft.Kubernetes/connectedClusters/apps/statefulsets/*",
        "Microsoft.Kubernetes/connectedClusters/autoscaling/horizontalpodautoscalers/*",
        "Microsoft.Kubernetes/connectedClusters/batch/cronjobs/*",
        "Microsoft.Kubernetes/connectedClusters/batch/jobs/*",
        "Microsoft.Kubernetes/connectedClusters/configmaps/*",
        "Microsoft.Kubernetes/connectedClusters/endpoints/*",
        "Microsoft.Kubernetes/connectedClusters/events.k8s.io/events/read",
        "Microsoft.Kubernetes/connectedClusters/events/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/daemonsets/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/deployments/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/ingresses/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/networkpolicies/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/replicasets/*",
        "Microsoft.Kubernetes/connectedClusters/limitranges/read",
        "Microsoft.Kubernetes/connectedClusters/namespaces/read",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/ingresses/*",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/networkpolicies/*",
        "Microsoft.Kubernetes/connectedClusters/persistentvolumeclaims/*",
        "Microsoft.Kubernetes/connectedClusters/pods/*",
        "Microsoft.Kubernetes/connectedClusters/policy/poddisruptionbudgets/*",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/*",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/*",
        "Microsoft.Kubernetes/connectedClusters/resourcequotas/read",
        "Microsoft.Kubernetes/connectedClusters/secrets/*",
        "Microsoft.Kubernetes/connectedClusters/serviceaccounts/*",
        "Microsoft.Kubernetes/connectedClusters/services/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Kubernetes Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-onboarding"></a>Azure Connected Machine dołączania

Może dołączać połączone maszyny platformy Azure. [Dowiedz się więcej](../azure-arc/servers/onboard-service-principal.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/read | Odczytywanie Azure Arc maszyn wirtualnych |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/write | Zapisuje Azure Arc maszyn wirtualnych |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/privateLinkScopes/read | Odczytywanie wszystkich Azure Arc privateLinkScopes |
> | [Microsoft.GuestConfiguration](resource-provider-operations.md#microsoftguestconfiguration)/guestConfigurationAssignments/read | Pobierz przypisanie konfiguracji gościa. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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
        "Microsoft.HybridCompute/privateLinkScopes/read",
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

### <a name="azure-connected-machine-resource-administrator"></a>Azure Connected Machine zasobów

Może odczytywać, zapisywać, usuwać i ponownie dołączać połączone maszyny platformy Azure.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/read | Odczytywanie wszystkich Azure Arc maszyn wirtualnych |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/write | Zapisuje Azure Arc maszyn wirtualnych |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/delete | Usuwa maszynę Azure Arc maszyn wirtualnych |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/extensions/write | Instaluje lub aktualizuje Azure Arc rozszerzenia |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/privateLinkScopes/* |  |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/*/read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.HybridCompute/privateLinkScopes/*",
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

Zezwala na dostęp do odczytu danych rozliczeń [Dowiedz się więcej](../cost-management-billing/manage/manage-billing-access.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/*/read | Odczytywanie informacji rozliczeniowych |
> | [Microsoft.Commerce](resource-provider-operations.md#microsoftcommerce)/*/read |  |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/*/read |  |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Lista grup zarządzania dla uwierzytelnionego użytkownika. |
> | [Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/read |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

### <a name="blueprint-contributor"></a>Współautor strategii

Może zarządzać definicjami strategii, ale nie może ich przypisywać. [Dowiedz się więcej](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprints/* | Tworzenie definicji strategii lub artefaktów strategii i zarządzanie nimi. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
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

### <a name="blueprint-operator"></a>Operator strategii

Może przypisywać istniejące opublikowane strategie, ale nie może tworzyć nowych strategii. Należy pamiętać, że działa to tylko wtedy, gdy przypisanie jest wykonywane przy użyciu tożsamości zarządzanej przypisanej przez użytkownika. [Dowiedz się więcej](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/* | Tworzenie przypisań strategii i zarządzanie nimi. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

### <a name="cost-management-contributor"></a>Współautor zarządzania kosztami

Może wyświetlać koszty i zarządzać konfiguracją kosztów (np. budżetami, eksportami) [Dowiedz się więcej](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/* |  |
> | [Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement)/* |  |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/billingPeriods/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Pobiera listę subskrypcji. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/configurations/read | Uzyskiwanie konfiguracji |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/recommendations/read | Odczytuje zalecenia |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Lista grup zarządzania dla uwierzytelnionego użytkownika. |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/billingProperty/read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

### <a name="cost-management-reader"></a>Cost Management czytelnika

Może wyświetlać dane kosztów i konfigurację (np. budżety, eksporty) [Dowiedz się więcej](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/*/read |  |
> | [Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/read |  |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/billingPeriods/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Pobiera listę subskrypcji. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/configurations/read | Uzyskiwanie konfiguracji |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/recommendations/read | Odczytuje zalecenia |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Lista grup zarządzania dla uwierzytelnionego użytkownika. |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/billingProperty/read |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/settings/write | Tworzy lub aktualizuje ustawienia hierarchii grupy zarządzania. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/settings/delete | Usuwa ustawienia hierarchii grupy zarządzania. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

### <a name="kubernetes-cluster---azure-arc-onboarding"></a>Klaster Kubernetes — Azure Arc dołączanie

Definicja roli w celu autoryzowania dowolnego użytkownika/usługi do tworzenia zasobu connectedClusters [Dowiedz się więcej](../azure-arc/kubernetes/connect-cluster.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Tworzy lub aktualizuje wdrożenie. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Pobierz wyniki operacji subskrypcji. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Pobiera listę subskrypcji. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Write | Zapisuje connectedClusters |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/read | Odczytywanie połączonychznaki |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
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
> | */read | Odczytywanie zasobów wszystkich typów, z wyjątkiem wpisów tajnych. |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/applications/* |  |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/register/action | Zarejestruj się w rozwiązaniu. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

Umożliwia odczytywanie i wykonywanie akcji na zasobach aplikacji zarządzanej

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | */odczyt | Odczytywanie zasobów wszystkich typów, z wyjątkiem wpisów tajnych. |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/applications/read | Pobiera listę aplikacji. |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/*/action |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

### <a name="managed-applications-reader"></a>Czytelnik aplikacji zarządzanych

Umożliwia odczytywanie zasobów w aplikacji zarządzanej i żądanie dostępu JIT.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | */odczyt | Odczytywanie zasobów wszystkich typów, z wyjątkiem wpisów tajnych. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/jitRequests/* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

### <a name="managed-services-registration-assignment-delete-role"></a>Przypisanie rejestracji usług zarządzanych Usuń rolę

Rola usuwania przypisania rejestracji usług zarządzanych umożliwia użytkownikom zarządzającym dzierżawą usunięcie przypisania rejestracji przypisanego do ich dzierżawy. [Dowiedz się więcej](../lighthouse/how-to/remove-delegation.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/read | Pobiera listę przypisań rejestracji usług zarządzanych. |
> | [Microsoft.ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/delete | Usuwa przypisanie rejestracji usług zarządzanych. |
> | [Microsoft.ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/operationStatuses/read | Odczytuje stan operacji dla zasobu. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
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
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/delete | Usuń grupę zarządzania. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Lista grup zarządzania dla uwierzytelnionego użytkownika. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/delete | Anulowano skojarzenie subskrypcji z grupy zarządzania. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/write | Kojarzy istniejącą subskrypcję z grupą zarządzania. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/write | Utwórz lub zaktualizuj grupę zarządzania. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/read | Wyświetla subskrypcję w ramach danej grupy zarządzania. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Lista grup zarządzania dla uwierzytelnionego użytkownika. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/read | Wyświetla subskrypcję w ramach danej grupy zarządzania. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

### <a name="new-relic-apm-account-contributor"></a>New Relic współautora konta APM

Umożliwia zarządzanie kontami New Relic Application Performance Management i aplikacjami, ale nie ma do nich dostępu.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie tym alertem |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | NewRelic.APM/accounts/* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
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

### <a name="policy-insights-data-writer-preview"></a>Funkcja zapisu danych szczegółowych informacji o zasadach (wersja zapoznawcza)

Umożliwia dostęp do odczytu do zasad zasobów i dostęp do zapisu do zdarzeń zasad składnika zasobów. [Dowiedz się więcej](../governance/policy/concepts/policy-for-kubernetes.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyassignments/read | Uzyskaj informacje o przypisaniu zasad. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policydefinitions/read | Uzyskaj informacje o definicji zasad. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyexemptions/read | Uzyskaj informacje o wyłączeniu zasad. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/read | Uzyskaj informacje o definicji zestawu zasad. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/checkDataPolicyCompliance/action | Sprawdź stan zgodności danego składnika z zasadami danych. |
> | [Microsoft.PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/policyEvents/logDataEvents/action | Rejestrowanie zdarzeń zasad składnika zasobów. |
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

### <a name="quota-request-operator"></a>Operator żądania limitu przydziału

Odczytywanie i tworzenie żądań przydziałów, uzyskiwanie stanu żądania limitu przydziału i tworzenie biletów pomocy technicznej. [Dowiedz się więcej](/rest/api/reserved-vm-instances/quotaapi)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/resourceProviders/locations/serviceLimits/read | Uzyskiwanie bieżącego limitu usługi lub limitu przydziału określonego zasobu i lokalizacji |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/resourceProviders/locations/serviceLimits/write | Tworzenie limitu usługi lub limitu przydziału dla określonego zasobu i lokalizacji |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/resourceProviders/locations/serviceLimitsRequests/read | Pobierz dowolne żądanie limitu usług dla określonego zasobu i lokalizacji |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/register/action | Rejestruje dostawcę zasobów pojemności i umożliwia tworzenie zasobów pojemności. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie tym alertem |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
> | *brak* |  |
> | **NotDataActions** |  |
> | *brak* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and create quota requests, get quota request status, and create support tickets.",
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
  "roleName": "Quota Request Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reservation-purchaser"></a>Nabywca rezerwacji

Umożliwia zakup rezerwacji Dowiedz [się więcej](../cost-management-billing/reservations/prepare-buy-reservation.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Pobiera listę subskrypcji. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/register/action | Rejestruje dostawcę zasobów pojemności i umożliwia tworzenie zasobów pojemności. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/register/action | Rejestruje subskrypcję u dostawcy zasobów Microsoft.Compute |
> | [Microsoft.SQL](resource-provider-operations.md#microsoftsql)/register/action | Rejestruje subskrypcję dostawcy zasobów microsoft SQL Database i umożliwia tworzenie baz danych Microsoft SQL. |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/register/action | Rejestrowanie w rpy zużycia |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/catalogs/read | Odczytywanie katalogu rezerwacji |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleAssignments/read | Uzyskaj informacje o przypisaniu roli. |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/reservationRecommendations/read | Lista zaleceń dotyczących pojedynczych lub udostępnionych wystąpień zarezerwowanych dla subskrypcji. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/supporttickets/write | Umożliwia tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

Użytkownicy z uprawnieniami do tworzenia/modyfikowania zasad zasobów, tworzenia biletu pomocy technicznej i odczytywania zasobów/hierarchii. [Dowiedz się więcej](../governance/policy/overview.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | */odczyt | Odczytywanie zasobów wszystkich typów, z wyjątkiem wpisów tajnych. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyassignments/* | Tworzenie przypisań zasad i zarządzanie nimi |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policydefinitions/* | Tworzenie definicji zasad i zarządzanie nimi |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyexemptions/* | Tworzenie wyjątków zasad i zarządzanie nimi |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/* | Tworzenie zestawów zasad i zarządzanie nimi |
> | [Microsoft.PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/* |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
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
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie tym alertem |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Uzyskiwanie definicji sieci wirtualnej |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp to wewnętrzna operacja używana przez usługę |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocateStamp/action | AllocateStamp to wewnętrzna operacja używana przez usługę |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/write | Operacja Aktualizacji certyfikatu zasobu aktualizuje certyfikat poświadczeń zasobu/magazynu. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/* | Tworzenie informacji rozszerzonych związanych z magazynem i zarządzanie nimi |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | Operacja Get Vault pobiera obiekt reprezentujący zasób platformy Azure typu "vault" |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/* | Tworzenie zarejestrowanych tożsamości i zarządzanie nimi |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/* | Tworzenie lub aktualizowanie ustawień alertu replikacji |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/read | Odczytywanie dowolnych zdarzeń |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/* | Tworzenie sieci szkieletowych replikacji i zarządzanie nimi |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/* | Tworzenie zadań replikacji i zarządzanie nimi |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/* | Tworzenie zasad replikacji i zarządzanie nimi |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/* | Tworzenie planów odzyskiwania i zarządzanie nimi |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/* | Tworzenie konfiguracji magazynu magazynu usługi Recovery Services i zarządzanie nimi |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Zwraca szczegóły użycia magazynu usługi Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | Operacja tokenu magazynu może służyć do uzyskania tokenu magazynu dla operacji zaplecza na poziomie magazynu. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/* | Odczytywanie alertów dotyczących magazynu usługi Recovery Services |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationOperationStatus/read | Odczytywanie stanu operacji replikacji magazynu |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

Umożliwia trybu failover i powrotu po awarii, ale nie wykonuje innych operacji zarządzania Site Recovery [Dowiedz się więcej](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Uzyskiwanie definicji sieci wirtualnej |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp to wewnętrzna operacja używana przez usługę |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocateStamp/action | AllocateStamp to wewnętrzna operacja używana przez usługę |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | Operacja Pobierz informacje rozszerzone pobiera informacje rozszerzone obiektu reprezentujące zasób platformy Azure typu ?vault? |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | Operacja Get Vault pobiera obiekt reprezentujący zasób platformy Azure typu "vault" |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | Operacji Get Operation Results można użyć do uzyskania stanu operacji i wyniku operacji przesłanej asynchronicznie |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | Można użyć operacji Pobierz kontenery, aby zarejestrować kontenery dla zasobu. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/read | Odczytywanie wszystkich ustawień alertów |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/read | Odczytywanie dowolnych zdarzeń |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/checkConsistency/action | Sprawdza spójność sieci szkieletowej |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/read | Odczytywanie dowolnych szkieletów |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/reassociateGateway/action | Ponowne kojarzenie bramy |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/renewcertificate/action | Odnawianie certyfikatu dla sieci szkieletowej |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/read | Odczytywanie dowolnych sieci |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Odczytywanie mapowań sieci |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/read | Odczytywanie dowolnych kontenerów ochrony |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Odczytywanie wszystkich elementów, które można chronić |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Stosowanie punktu odzyskiwania |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Zatwierdzenie trybu failover |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Planowane przełączanie do trybu failover |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Odczytywanie wszystkich chronionych elementów |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Odczytywanie wszystkich punktów odzyskiwania replikacji |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Naprawianie replikacji |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Ponowne włączania ochrony chronionego elementu |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Przełącz kontener ochrony |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Test pracy w trybie failover |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Testowanie oczyszczania trybu failover |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Tryb failover |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Aktualizowanie usługi mobilności |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Odczytywanie mapowań kontenerów ochrony |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Odczytywanie wszystkich dostawców usług Recovery Services |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Odświeżanie dostawcy |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/read | Odczytywanie klasyfikacji magazynu |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Odczytywanie wszystkich mapowań klasyfikacji magazynu |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationvCenters/read | Odczytywanie wszystkich serwerów vCenter |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/* | Tworzenie zadań replikacji i zarządzanie nimi |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/read | Odczytywanie dowolnych zasad |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/failoverCommit/action | Plan odzyskiwania zatwierdzania trybu failover |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/plannedFailover/action | Plan odzyskiwania planowanego trybu failover |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/read | Odczytywanie planów odzyskiwania |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/reProtect/action | Ponowne włączania ochrony planu odzyskiwania |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/testFailover/action | Testowanie planu odzyskiwania w trybie failover |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Testowanie planu odzyskiwania oczyszczania trybu failover |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/unplannedFailover/action | Plan odzyskiwania trybu failover |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/* | Odczytywanie alertów dotyczących magazynu usługi Recovery Services |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Zwraca szczegóły użycia magazynu usługi Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | Operacja tokenu magazynu może służyć do uzyskania tokenu magazynu dla operacji zaplecza na poziomie magazynu. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

Umożliwia wyświetlanie stanu Site Recovery ale nie wykonywanie innych operacji zarządzania [Dowiedz się więcej](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp to wewnętrzna operacja używana przez usługę |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | Operacja Pobierz informacje rozszerzone pobiera informacje rozszerzone obiektu reprezentujące zasób platformy Azure typu ?vault? |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Pobiera alerty dla magazynu usługi Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | Operacja Get Vault pobiera obiekt reprezentujący zasób platformy Azure typu "vault" |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | Można użyć operacji Pobierz wyniki operacji, aby uzyskać stan operacji i wynik asynchronicznie przesłanej operacji |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | Można użyć operacji Pobierz kontenery, aby zarejestrować kontenery dla zasobu. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/read | Odczytywanie ustawień alertów |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/read | Odczytywanie dowolnych zdarzeń |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/read | Odczytywanie dowolnych szkieletów |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/read | Odczytywanie dowolnych sieci |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Odczytywanie mapowań sieci |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/read | Odczytywanie dowolnych kontenerów ochrony |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Odczytywanie wszystkich elementów, które można chronić |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Odczytywanie wszystkich chronionych elementów |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Odczytywanie wszystkich punktów odzyskiwania replikacji |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Odczytywanie mapowań kontenerów ochrony |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Odczytywanie wszystkich dostawców usług Recovery Services |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/read | Odczytywanie klasyfikacji magazynu |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Odczytywanie mapowań klasyfikacji magazynu |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationvCenters/read | Odczytywanie wszystkich serwerów vCenter |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/read | Odczytywanie dowolnych zadań |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/read | Odczytywanie dowolnych zasad |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/read | Odczytywanie planów odzyskiwania |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Zwraca szczegóły użycia magazynu usługi Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | Operacja tokenu magazynu może służyć do uzyskania tokenu magazynu dla operacji zaplecza na poziomie magazynu. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

Umożliwia tworzenie żądań pomocy technicznej i zarządzanie nimi [Dowiedz się więcej](../azure-portal/supportability/how-to-create-azure-support-request.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
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

Umożliwia zarządzanie tagami jednostek bez zapewniania dostępu do samych jednostek. [Dowiedz się więcej](../azure-resource-manager/management/tag-resources.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/resources/read | Pobiera zasoby dla grupy zasobów. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resources/read | Pobiera zasoby subskrypcji. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/tags/* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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


### <a name="azure-digital-twins-data-owner"></a>Azure Digital Twins właściciela danych

Rola pełnego dostępu dla Digital Twins danych [Dowiedz się więcej](../digital-twins/concepts-security.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/eventroutes/* | Odczytywanie, usuwanie, tworzenie i aktualizowanie dowolnej trasy zdarzeń |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/* | Odczytywanie, tworzenie, aktualizowanie lub usuwanie dowolnej bliźniaczej reprezentacji usługi Digital Twin |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/commands/* | Wywoływanie dowolnego polecenia w bliźniaczej reprezentacji usługi Digital Twin |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/relationships/* | Odczytywanie, tworzenie, aktualizowanie lub usuwanie dowolnej relacji bliźniaczej reprezentacji cyfrowej |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/models/* | Odczytywanie, tworzenie, aktualizowanie lub usuwanie dowolnego modelu |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/query/* | Wykonywanie zapytań Digital Twins grafie |
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

### <a name="azure-digital-twins-data-reader"></a>Azure Digital Twins danych

Rola tylko do odczytu dla Digital Twins płaszczyzny danych [Dowiedz się więcej](../digital-twins/concepts-security.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/read | Odczytywanie dowolnej bliźniaczej reprezentacji usługi Digital Twin |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/relationships/read | Odczytywanie dowolnej relacji bliźniaczej reprezentacji cyfrowej |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/eventroutes/read | Odczytywanie dowolnej trasy zdarzeń |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/models/read | Odczytywanie dowolnego modelu |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/query/action | Wykonywanie zapytań dla dowolnego Digital Twins Graph |
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

### <a name="biztalk-contributor"></a>Współautor bizTalk

Umożliwia zarządzanie usługami BizTalk, ale nie umożliwia dostępu do nich.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | Microsoft.BizTalkServices/BizTalk/* | Tworzenie usług BizTalk i zarządzanie nimi |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/* |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | Odczytywanie puli hostów |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/read | Odczytywanie puli hostów/hostów sesji |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie tym alertem |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
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
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/*/read |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/read | Odczytywanie grup aplikacji |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | Odczytywanie puli hostów |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/read | Odczytywanie puli hostów/hostów sesji |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | Pobiera lub wyświetla listę wdrożeń. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Odczytywanie alertu metryki klasycznej |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie tym alertem |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
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

### <a name="desktop-virtualization-host-pool-contributor"></a>Współautor puli hostów wirtualizacji pulpitu

Współautor puli hostów wirtualizacji pulpitu. [Dowiedz się więcej](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

### <a name="desktop-virtualization-host-pool-reader"></a>Czytnik puli hostów wirtualizacji pulpitu

Czytelnik puli hostów wirtualizacji pulpitu. [Dowiedz się więcej](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/*/read |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | Odczytywanie puli hostów |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | Pobiera lub wyświetla listę wdrożeń. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Odczytywanie alertu metryki klasycznej |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/*/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | Pobiera lub wyświetla listę wdrożeń. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Odczytywanie alertu metryki klasycznej |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | Odczytywanie puli hostów |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

Umożliwia użytkownikowi korzystanie z aplikacji w grupie aplikacji. [Dowiedz się więcej](../virtual-desktop/delegated-access-virtual-desktop.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | *brak* |  |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationGroups/useApplications/action | Korzystanie z grupy aplikacji |
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

Operator sesji uesr wirtualizacji pulpitu. [Dowiedz się więcej](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | Odczytywanie puli hostów |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/read | Odczytywanie puli hostów/hostów sesji |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/usersessions/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie tym alertem |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/workspaces/* |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/read | Odczytywanie grup aplikacji |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

### <a name="desktop-virtualization-workspace-reader"></a>Czytelnik obszaru roboczego wirtualizacji pulpitu

Czytelnik obszaru roboczego wirtualizacji pulpitu. [Dowiedz się więcej](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/workspaces/read | Odczytywanie obszarów roboczych |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/read | Odczytywanie grup aplikacji |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | Pobiera lub wyświetla listę wdrożeń. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Odczytywanie alertu metryki klasycznej |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions (Działania danych)** |  |
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

### <a name="disk-backup-reader"></a>Czytnik kopii zapasowych dysków

Umożliwia wykonywanie kopii zapasowych dysków w magazynie kopii zapasowych. [Dowiedz się więcej](../backup/disk-backup-faq.yml)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/read | Uzyskiwanie właściwości dysku |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/beginGetAccess/action | Uzyskiwanie URI sygnatury dostępu współdzielonego dysku w celu uzyskania dostępu do obiektów blob |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

Zapewnia uprawnienia do magazynu kopii zapasowych w celu wykonania przywracania dysku. [Dowiedz się więcej](../backup/restore-managed-disks.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/write | Tworzy nowy dysk lub aktualizuje istniejący dysk |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/read | Uzyskiwanie właściwości dysku |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

### <a name="disk-snapshot-contributor"></a>Współautor migawki dysku

Umożliwia zarządzanie migawkami dysków w magazynie kopii zapasowych. [Dowiedz się więcej](../backup/backup-managed-disks.md)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/delete | Usuwanie migawki |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/write | Tworzenie nowej migawki lub aktualizowanie istniejącej migawki |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/read | Uzyskiwanie właściwości migawki |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/beginGetAccess/action | Uzyskiwanie URI sygnatury dostępu współdzielonego migawki w celu uzyskania dostępu do obiektu blob |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/endGetAccess/action | Odwoływanie URI sygnatury dostępu współdzielonego migawki |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/beginGetAccess/action | Uzyskiwanie URI sygnatury dostępu współdzielonego dysku w celu uzyskania dostępu do obiektów blob |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/action | Zwraca klucze dostępu dla określonego konta magazynu. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/write | Tworzy konto magazynu z określonymi parametrami, aktualizuje właściwości lub tagi albo dodaje domenę niestandardową dla określonego konta magazynu. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/delete | Usuwa istniejące konto magazynu. |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

### <a name="scheduler-job-collections-contributor"></a>Współautor kolekcji zadań harmonogramu

Umożliwia zarządzanie kolekcjami zadań harmonogramu, ale nie umożliwia dostępu do nich.

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Tworzenie alertu metryki klasycznej i zarządzanie nimi |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Pobiera stany dostępności dla wszystkich zasobów w określonym zakresie |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Scheduler](resource-provider-operations.md#microsoftscheduler)/jobcollections/* | Tworzenie kolekcji zadań i zarządzanie nimi |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Tworzenie i aktualizowanie biletu pomocy technicznej |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

Operator centrum usług umożliwia wykonywanie wszystkich operacji odczytu, zapisu i usuwania związanych z łącznikami centrum usług. [Dowiedz się więcej](/services-hub/health/sh-connector-roles)

> [!div class="mx-tableFixed"]
> | Akcje | Opis |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Odczytywanie ról i przypisań ról |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Tworzenie wdrożenia i zarządzanie nimi |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/connectors/write | Tworzenie lub aktualizowanie łącznika centrum usług |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/connectors/read | Wyświetlanie lub wyświetlanie listy łączników centrum usług |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/connectors/delete | Usuwanie łączników centrum usług |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/connectors/checkAssessmentEntitlement/action | Wyświetla listę uprawnień oceny dla danego obszaru roboczego centrum usług |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/supportOfferingEntitlement/read | Wyświetlanie uprawnień do oferty pomocy technicznej dla danego obszaru roboczego centrum usług |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/workspaces/read | Lista obszarów roboczych centrum usług dla danego użytkownika |
> | **NotActions** |  |
> | *brak* |  |
> | **DataActions** |  |
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

- [Przypisywanie ról platformy Azure przy użyciu Azure Portal](role-assignments-portal.md)
- [Role niestandardowe platformy Azure](custom-roles.md)
- [Uprawnienia w usłudze Azure Security Center](../security-center/security-center-permissions.md)
