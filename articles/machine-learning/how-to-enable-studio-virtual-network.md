---
title: Włączanie Azure Machine Learning Studio w sieci wirtualnej
titleSuffix: Azure Machine Learning
description: Użyj Azure Machine Learning Studio, aby uzyskać dostęp do danych przechowywanych w sieci wirtualnej.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 10/21/2020
ms.custom: contperfq4, tracking-python
ms.openlocfilehash: 781b37405bebc5ddc3d33cbbc089049b0c0f8ca4
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93325525"
---
# <a name="use-azure-machine-learning-studio-in-an-azure-virtual-network"></a>Korzystanie z programu Azure Machine Learning Studio w sieci wirtualnej platformy Azure

W tym artykule dowiesz się, jak używać programu Azure Machine Learning Studio w sieci wirtualnej. Omawiane kwestie:

> [!div class="checklist"]
> - Uzyskaj dostęp do programu Studio z zasobu w sieci wirtualnej.
> - Skonfiguruj prywatne punkty końcowe dla kont magazynu.
> - Przyznaj programowi Studio dostęp do danych przechowywanych w sieci wirtualnej.
> - Dowiedz się, w jaki sposób Studio ma wpływ na zabezpieczenia magazynu.

Ten artykuł jest częścią piątą serii składającej się z pięciu części, która przeprowadzi Cię przez proces zabezpieczania przepływów pracy Azure Machine Learning. Zdecydowanie zalecamy zapoznanie się z [częścią poniżej: Omówienie sieci wirtualnej](how-to-network-security-overview.md) , aby zrozumieć ogólną architekturę. 

Zapoznaj się z innymi artykułami w tej serii:

[1. Sieć wirtualna — Omówienie](how-to-network-security-overview.md)  >  [2. Zabezpiecz obszar roboczy](how-to-secure-workspace-vnet.md)  >  [3. Zabezpiecz środowisko szkoleniowe](how-to-secure-training-vnet.md)  >  [4. Zabezpiecz środowisko inferencing](how-to-secure-inferencing-vnet.md)  >  **5. Włącz funkcje programu Studio**


> [!IMPORTANT]
> Jeśli obszar roboczy znajduje się w __chmurze suwerennej__ , takiej jak Azure Government lub Azure Chiny 21Vianet, zintegrowane notesy _nie_ obsługują używania magazynu znajdującego się w sieci wirtualnej. Zamiast tego można użyć notesów Jupyter z wystąpienia obliczeniowego. Aby uzyskać więcej informacji, zobacz sekcję [dostęp do danych w notesie wystąpienia obliczeniowego](how-to-secure-training-vnet.md#access-data-in-a-compute-instance-notebook) .


## <a name="prerequisites"></a>Wymagania wstępne

+ Zapoznaj się z [omówieniem zabezpieczeń sieci](how-to-network-security-overview.md) , aby poznać typowe scenariusze sieci wirtualnej i ogólną architekturę sieci wirtualnej.

+ Istniejąca sieć wirtualna i podsieć do użycia.

+ Istniejący [obszar roboczy Azure Machine Learning z włączonym linkiem prywatnym](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint).

+ Istniejące [konto usługi Azure Storage zostało dodane do sieci wirtualnej](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints).

## <a name="access-the-studio-from-a-resource-inside-the-vnet"></a>Dostęp do programu Studio z zasobu w sieci wirtualnej

Jeśli uzyskujesz dostęp do programu Studio z zasobu w sieci wirtualnej (na przykład wystąpienie obliczeniowe lub maszyna wirtualna), musisz zezwolić na ruch wychodzący z sieci wirtualnej do programu Studio. 

Na przykład, jeśli używasz sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) w celu ograniczenia ruchu wychodzącego, Dodaj regułę do miejsca docelowego __tagu usługi__ __AzureFrontDoor. frontonu__.

## <a name="access-data-using-the-studio"></a>Uzyskiwanie dostępu do danych przy użyciu programu Studio

Po dodaniu konta usługi Azure Storage do sieci wirtualnej za pomocą [punktu końcowego usługi](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints) lub [prywatnego punktu końcowego](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-private-endpoints)należy skonfigurować konto magazynu tak, aby korzystało z [tożsamości zarządzanej](../active-directory/managed-identities-azure-resources/overview.md) w celu udzielenia dostępu do danych.

Jeśli nie włączysz tożsamości zarządzanej, zostanie wyświetlony następujący błąd, co `Error: Unable to profile this dataset. This might be because your data is stored behind a virtual network or your data does not support profile.` spowoduje wyłączenie następujących operacji:

* Podgląd danych w Studio.
* Wizualizowanie danych w projektancie.
* Prześlij eksperyment AutoML.
* Rozpocznij projekt etykietowania.

Program Virtual Machines obsługuje odczytywanie danych z następujących typów magazynów w sieci wirtualnej:

* Obiekt bob Azure
* Usługa Azure Data Lake Storage 1. generacji
* Usługa Azure Data Lake Storage 2. generacji
* Azure SQL Database

### <a name="grant-workspace-managed-identity-__reader__-access-to-storage-private-link"></a>Przyznaj dostęp do __czytnika__ tożsamości zarządzanego obszaru roboczego do prywatnego linku do magazynu

Ten krok jest wymagany tylko wtedy, gdy dodano konto usługi Azure Storage do sieci wirtualnej przy użyciu [prywatnego punktu końcowego](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-private-endpoints). Aby uzyskać więcej informacji, zobacz wbudowana rola [czytnika](../role-based-access-control/built-in-roles.md#reader) .

### <a name="configure-datastores-to-use-workspace-managed-identity"></a>Konfigurowanie magazynów danych do korzystania z tożsamości zarządzanej obszaru roboczego

Azure Machine Learning używa [magazynów](concept-data.md#datastores) danych do łączenia się z kontami magazynu. Wykonaj następujące kroki, aby skonfigurować magazyny danych do korzystania z tożsamości zarządzanej. 

1. W programie Studio wybierz pozycję __magazyny__ danych.

1. Aby utworzyć nowy magazyn danych, wybierz pozycję __+ nowy magazyn__ danych.

    Aby zaktualizować istniejący magazyn danych, wybierz magazyn danych i wybierz pozycję __Aktualizuj poświadczenia__.

1. W ustawieniach magazynu danych wybierz opcję __tak__ , aby  __umożliwić usłudze Azure Machine Learning dostęp do magazynu przy użyciu tożsamości zarządzanej przez obszar roboczy__.


Te kroki umożliwiają dodanie tożsamości zarządzanej przez obszar roboczy jako __czytnika__ do usługi magazynu przy użyciu funkcji kontroli dostępu opartej na zasobach platformy Azure (Azure RBAC). Dostęp __czytnika__ umożliwia pobranie ustawień zapory przez obszar roboczy i upewnienie się, że dane nie opuszczają sieci wirtualnej.

> [!NOTE]
> Wprowadzenie zmian może potrwać do 10 minut.

## <a name="technical-notes-for-managed-identity"></a>Uwagi techniczne dotyczące tożsamości zarządzanej

Korzystanie z tożsamości zarządzanej w celu uzyskiwania dostępu do usług magazynu wpływa na niektóre zagadnienia dotyczące zabezpieczeń. Te zagadnienia są unikatowe dla typu konta magazynu, do którego uzyskujesz dostęp. W tej sekcji opisano zmiany dotyczące poszczególnych typów kont magazynu.

### <a name="azure-blob-storage"></a>Azure Blob Storage

W przypadku __usługi Azure Blob Storage__ tożsamość zarządzana przez obszar roboczy jest również dodawana jako [czytnik danych obiektów BLOB](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) , dzięki czemu może odczytywać dane z magazynu obiektów BLOB.

### <a name="azure-data-lake-storage-gen2-access-control"></a>Azure Data Lake Storage Gen2 kontroli dostępu

Aby kontrolować dostęp do danych wewnątrz sieci wirtualnej, można użyć list kontroli dostępu (ACL) na platformie Azure oraz do sterowania dostępem do nich.

Aby użyć kontroli RBAC platformy Azure, Dodaj tożsamość zarządzaną przez obszar roboczy do roli [czytnika danych obiektów BLOB](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) . Aby uzyskać więcej informacji, zobacz [Kontrola dostępu oparta na rolach na platformie Azure](../storage/blobs/data-lake-storage-access-control-model.md#role-based-access-control).

Aby można było używać list kontroli dostępu, tożsamość zarządzana przez obszar roboczy może zostać przypisana podobnie jak jakakolwiek inna zasada zabezpieczeń. Aby uzyskać więcej informacji, zobacz [listy kontroli dostępu do plików i katalogów](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="azure-data-lake-storage-gen1-access-control"></a>Azure Data Lake Storage Gen1 kontroli dostępu

Azure Data Lake Storage Gen1 obsługuje tylko listy kontroli dostępu w stylu POSIX. Możesz przypisać dostęp do tożsamości zarządzanej przez obszar roboczy do zasobów tak samo jak w przypadku każdej innej zasady zabezpieczeń. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu w Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

### <a name="azure-sql-database-contained-user"></a>Azure SQL Database zawarty użytkownik

Aby uzyskać dostęp do danych przechowywanych w Azure SQL Database przy użyciu tożsamości zarządzanej, należy utworzyć użytkownika programu SQL Server, który jest mapowany na tożsamość zarządzaną. Aby uzyskać więcej informacji na temat tworzenia użytkownika od dostawcy zewnętrznego, zobacz [Tworzenie zawartych użytkowników mapowanych na tożsamości usługi Azure AD](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).

Po utworzeniu użytkownika zawartego w programie SQL Udziel uprawnień do niego przy użyciu [polecenia Udziel T-SQL](/sql/t-sql/statements/grant-object-permissions-transact-sql).

### <a name="azure-machine-learning-designer-default-datastore"></a>Domyślny magazyn danych projektanta Azure Machine Learning

Projektant używa konta magazynu dołączonego do obszaru roboczego do przechowywania danych wyjściowych domyślnie. Można jednak określić, aby przechowywać dane wyjściowe do dowolnego magazynu danych, do którego masz dostęp. Jeśli środowisko korzysta z sieci wirtualnych, można użyć tych kontrolek, aby upewnić się, że dane pozostają bezpieczne i dostępne.

Aby ustawić nowy magazyn domyślny dla potoku:

1. W wersji roboczej potoku wybierz **ikonę koła zębatego** obok tytułu potoku.
1. Wybierz **pozycję Wybierz domyślny magazyn** danych.
1. Określ nowy magazyn danych.

Możesz również zastąpić domyślny magazyn danych dla każdego modułu. Zapewnia to kontrolę nad lokalizacją przechowywania poszczególnych modułów.

1. Wybierz moduł, którego dane wyjściowe chcesz określić.
1. Rozwiń sekcję **Ustawienia wyjściowe** .
1. Wybierz opcję **Zastąp domyślne ustawienia wyjściowe**.
1. Wybierz pozycję **Ustaw ustawienia wyjściowe**.
1. Określ nowy magazyn danych.

## <a name="next-steps"></a>Następne kroki

Ten artykuł jest opcjonalną częścią serii sieci wirtualnych z czterema częścią. Zapoznaj się z pozostałymi artykułami, aby dowiedzieć się, jak zabezpieczyć sieć wirtualną:

* [Część 1: Omówienie usługi Virtual Network](how-to-network-security-overview.md)
* [Część 2: Zabezpieczanie zasobów obszaru roboczego](how-to-secure-workspace-vnet.md)
* [Część 3: Zabezpieczanie środowiska szkoleniowego](how-to-secure-training-vnet.md)
* [Część 4: Zabezpieczanie środowiska inferencing](how-to-secure-inferencing-vnet.md)