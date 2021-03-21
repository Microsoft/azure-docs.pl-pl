---
title: Włączanie Azure Machine Learning Studio w sieci wirtualnej
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak skonfigurować Azure Machine Learning Studio, aby uzyskać dostęp do danych przechowywanych w sieci wirtualnej.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 10/21/2020
ms.custom: contperf-fy20q4, tracking-python
ms.openlocfilehash: da8007a651b62430055f263f082fabf2aa4bf610
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103574292"
---
# <a name="use-azure-machine-learning-studio-in-an-azure-virtual-network"></a>Korzystanie z programu Azure Machine Learning Studio w sieci wirtualnej platformy Azure

W tym artykule dowiesz się, jak używać programu Azure Machine Learning Studio w sieci wirtualnej. Program Studio zawiera funkcje, takie jak AutoML, Projektant i etykiety danych. Aby można było korzystać z tych funkcji w sieci wirtualnej, należy wykonać kroki opisane w tym artykule.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> - Przyznaj programowi Studio dostęp do danych przechowywanych w sieci wirtualnej.
> - Uzyskaj dostęp do programu Studio z zasobu w sieci wirtualnej.
> - Dowiedz się, w jaki sposób Studio ma wpływ na zabezpieczenia magazynu.

Ten artykuł jest częścią piątą serii składającej się z pięciu części, która przeprowadzi Cię przez proces zabezpieczania przepływów pracy Azure Machine Learning. Zdecydowanie zalecamy zapoznanie się z poprzednimi częściami w celu skonfigurowania środowiska sieci wirtualnej.

Zapoznaj się z innymi artykułami w tej serii:

[1. Sieć wirtualna — Omówienie](how-to-network-security-overview.md)  >  [2. Zabezpiecz obszar roboczy](how-to-secure-workspace-vnet.md)  >  [3. Zabezpiecz środowisko szkoleniowe](how-to-secure-training-vnet.md)  >  [4. Zabezpiecz środowisko inferencing](how-to-secure-inferencing-vnet.md)  >  **5. Włącz funkcje programu Studio**


> [!IMPORTANT]
> Jeśli obszar roboczy znajduje się w __chmurze suwerennej__, takiej jak Azure Government lub Azure Chiny 21Vianet, zintegrowane notesy _nie_ obsługują używania magazynu znajdującego się w sieci wirtualnej. Zamiast tego można użyć notesów Jupyter z wystąpienia obliczeniowego. Aby uzyskać więcej informacji, zobacz sekcję [dostęp do danych w notesie wystąpienia obliczeniowego](how-to-secure-training-vnet.md#access-data-in-a-compute-instance-notebook) .

## <a name="prerequisites"></a>Wymagania wstępne

+ Zapoznaj się z artykułem [Omówienie zabezpieczeń sieci](how-to-network-security-overview.md) , aby zrozumieć typowe scenariusze i architekturę sieci wirtualnych.

+ Istniejąca sieć wirtualna i podsieć do użycia.

+ Istniejący [obszar roboczy Azure Machine Learning z włączonym linkiem prywatnym](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint).

+ Istniejące [konto usługi Azure Storage zostało dodane do sieci wirtualnej](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints).

## <a name="configure-data-access-in-the-studio"></a>Konfigurowanie dostępu do danych w programie Studio

Niektóre funkcje programu Studio są domyślnie wyłączone w sieci wirtualnej. Aby ponownie włączyć te funkcje, należy włączyć zarządzaną tożsamość dla kont magazynu, które mają być używane w programie Studio. 

Następujące operacje są domyślnie wyłączone w sieci wirtualnej:

* Podgląd danych w Studio.
* Wizualizowanie danych w projektancie.
* Wdróż model w projektancie ([domyślne konto magazynu](#enable-managed-identity-authentication-for-default-storage-accounts)).
* Prześlij eksperyment AutoML ([domyślne konto magazynu](#enable-managed-identity-authentication-for-default-storage-accounts)).
* Rozpocznij projekt etykietowania.

Program Virtual Machines obsługuje odczytywanie danych z następujących typów magazynów w sieci wirtualnej:

* Obiekt bob Azure
* Usługa Azure Data Lake Storage 1. generacji
* Usługa Azure Data Lake Storage 2. generacji
* Azure SQL Database

### <a name="configure-datastores-to-use-workspace-managed-identity"></a>Konfigurowanie magazynów danych do korzystania z tożsamości zarządzanej przez obszar roboczy

Po dodaniu konta usługi Azure Storage do sieci wirtualnej przy użyciu [punktu końcowego usługi](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints) lub [prywatnego punktu końcowego](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-private-endpoints)należy skonfigurować magazyn danych do korzystania z uwierzytelniania [tożsamości zarządzanej](../active-directory/managed-identities-azure-resources/overview.md) . Dzięki temu program Studio umożliwia dostęp do danych na koncie magazynu.

Azure Machine Learning używa [magazynów](concept-data.md#datastores) danych do łączenia się z kontami magazynu. Wykonaj następujące kroki, aby skonfigurować magazyn danych do korzystania z tożsamości zarządzanej:

1. W programie Studio wybierz pozycję __magazyny__ danych.

1. Aby zaktualizować istniejący magazyn danych, wybierz magazyn danych i wybierz pozycję __Aktualizuj poświadczenia__.

    Aby utworzyć nowy magazyn danych, wybierz pozycję __+ nowy magazyn__ danych.

1. W obszarze Ustawienia magazynu danych wybierz pozycję __tak__ dla opcji  __Użyj tożsamości zarządzanej obszaru roboczego, aby uzyskać podgląd i profilowanie danych w programie Azure Machine Learning Studio__.

    ![Zrzut ekranu przedstawiający sposób włączania tożsamości zarządzanego obszaru roboczego](./media/how-to-enable-studio-virtual-network/enable-managed-identity.png)

Te kroki umożliwiają dodanie tożsamości zarządzanej przez obszar roboczy jako __czytnika__ do usługi magazynu przy użyciu funkcji RBAC platformy Azure. Dostęp do __czytnika__ pozwala obszarowi roboczemu pobrać ustawienia zapory, aby upewnić się, że dane nie opuszczają sieci wirtualnej. Wprowadzenie zmian może potrwać do 10 minut.

### <a name="enable-managed-identity-authentication-for-default-storage-accounts"></a>Włącz uwierzytelnianie tożsamości zarządzanej dla domyślnych kont magazynu

Każdy obszar roboczy Azure Machine Learning ma dwa domyślne konta magazynu, domyślne konto magazynu obiektów blob i domyślne konto magazynu plików, które są definiowane podczas tworzenia obszaru roboczego. Nowe ustawienia domyślne można również ustawić na stronie zarządzania **magazynem** danych.

![Zrzut ekranu pokazujący, gdzie można znaleźć domyślne magazyny danych](./media/how-to-enable-studio-virtual-network/default-datastores.png)

W poniższej tabeli opisano, dlaczego należy włączyć uwierzytelnianie tożsamości zarządzanej dla domyślnych kont magazynu obszaru roboczego.

|Konto magazynu  | Uwagi  |
|---------|---------|
|Domyślny magazyn obiektów BLOB obszaru roboczego| Przechowuje zasoby modelu z projektanta. Aby wdrażać modele w projektancie, należy włączyć uwierzytelnianie tożsamości zarządzane na tym koncie magazynu. <br> <br> Możesz wizualizować i uruchamiać potok projektanta, jeśli używa on innego niż domyślny magazyn danych, który został skonfigurowany do korzystania z tożsamości zarządzanej. Jeśli jednak spróbujesz wdrożyć model szkolony bez włączonej tożsamości zarządzanej w domyślnym magazynie danych, wdrożenie zakończy się niepowodzeniem niezależnie od innych magazynów danych w użyciu.|
|Domyślny magazyn plików obszaru roboczego| Przechowuje zasoby eksperymentu AutoML. Musisz włączyć uwierzytelnianie tożsamości zarządzanej na tym koncie magazynu, aby przesłać eksperymenty AutoML. |

> [!WARNING]
> Istnieje znany problem polegający na tym, że domyślny magazyn plików nie tworzy `azureml-filestore` folderu, który jest wymagany do przesyłania eksperymentów AutoML. Dzieje się tak, gdy użytkownicy wprowadzą istniejące Filestore do ustawienia domyślnego Filestore podczas tworzenia obszaru roboczego.
> 
> Aby uniknąć tego problemu, dostępne są dwie opcje: 1) Użyj domyślnej Filestore, która jest tworzona automatycznie podczas tworzenia obszaru roboczego. 2) aby zapewnić własne Filestore, upewnij się, że Filestore jest poza siecią wirtualną podczas tworzenia obszaru roboczego. Po utworzeniu obszaru roboczego Dodaj konto magazynu do sieci wirtualnej.
>
> Aby rozwiązać ten problem, Usuń konto Filestore z sieci wirtualnej, a następnie dodaj je z powrotem do sieci wirtualnej.

### <a name="grant-workspace-managed-identity-__reader__-access-to-storage-private-link"></a>Przyznaj dostęp do __czytnika__ tożsamości zarządzanego obszaru roboczego do prywatnego linku do magazynu

Jeśli konto usługi Azure Storage korzysta z prywatnego punktu końcowego, należy przyznać zarządzanemu przez obszar roboczym **dostęp do** prywatnego linku. Aby uzyskać więcej informacji, zobacz wbudowana rola [czytnika](../role-based-access-control/built-in-roles.md#reader) . 

Jeśli konto magazynu używa punktu końcowego usługi, możesz pominąć ten krok.

## <a name="access-the-studio-from-a-resource-inside-the-vnet"></a>Dostęp do programu Studio z zasobu w sieci wirtualnej

Jeśli uzyskujesz dostęp do programu Studio z zasobu w sieci wirtualnej (na przykład wystąpienie obliczeniowe lub maszyna wirtualna), musisz zezwolić na ruch wychodzący z sieci wirtualnej do programu Studio. 

Na przykład, jeśli używasz sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) w celu ograniczenia ruchu wychodzącego, Dodaj regułę do miejsca docelowego __tagu usługi__ __AzureFrontDoor. frontonu__.

## <a name="technical-notes-for-managed-identity"></a>Uwagi techniczne dotyczące tożsamości zarządzanej

Korzystanie z tożsamości zarządzanej w celu uzyskania dostępu do usług magazynu wpływa na kwestie dotyczące zabezpieczeń. W tej sekcji opisano zmiany dotyczące poszczególnych typów kont magazynu. 

Te zagadnienia są unikatowe dla __typu konta magazynu__ , do którego uzyskujesz dostęp.

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

### <a name="azure-machine-learning-designer-intermediate-module-output"></a>Dane wyjściowe modułu pośredniego projektanta Azure Machine Learning

Możesz określić lokalizację wyjściową dla każdego modułu w projektancie. Służy do przechowywania pośrednich zestawów danych w osobnej lokalizacji na potrzeby zabezpieczeń, rejestrowania lub inspekcji. Aby określić dane wyjściowe:

1. Wybierz moduł, którego dane wyjściowe chcesz określić.
1. W okienku ustawienia modułu, które pojawia się po prawej stronie, wybierz pozycję **Ustawienia wyjściowe**.
1. Określ magazyn danych, który ma być używany dla każdego wychodzącego modułu.
 
Upewnij się, że masz dostęp do pośrednich kont magazynu w sieci wirtualnej. W przeciwnym razie potok zakończy się niepowodzeniem.

Należy również [włączyć uwierzytelnianie tożsamości zarządzanej](#configure-datastores-to-use-workspace-managed-identity) dla kont magazynu pośredniego w celu wizualizowania danych wyjściowych.

## <a name="next-steps"></a>Następne kroki

Ten artykuł jest częścią piątą serii sieci wirtualnych z pięcioma częściami. Zapoznaj się z pozostałymi artykułami, aby dowiedzieć się, jak zabezpieczyć sieć wirtualną:

* [Część 1: Omówienie usługi Virtual Network](how-to-network-security-overview.md)
* [Część 2: Zabezpieczanie zasobów obszaru roboczego](how-to-secure-workspace-vnet.md)
* [Część 3: Zabezpieczanie środowiska szkoleniowego](how-to-secure-training-vnet.md)
* [Część 4: Zabezpieczanie środowiska inferencing](how-to-secure-inferencing-vnet.md)

Zobacz również artykuł dotyczący używania [niestandardowego systemu DNS](how-to-custom-dns.md) do rozpoznawania nazw.