---
title: Tworzenie tokenu sygnatury dostępu współdzielonego (SAS) dla kontenerów i obiektów BLOB za pomocą programu Microsoft Eksplorator usługi Storage
description: Jak utworzyć token dostępu współdzielonego (SAS) dla kontenerów i obiektów BLOB za pomocą programu Microsoft Eksplorator usługi Storage i Azure Portal
ms.topic: how-to
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 02/11/2021
ms.openlocfilehash: 104329928c507c288537704fbbef4e35b65cb445
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101739440"
---
# <a name="create-sas-tokens-for-document-translation"></a>Utwórz tokeny sygnatury dostępu współdzielonego na potrzeby tłumaczenia dokumentu

W tym artykule dowiesz się, jak tworzyć tokeny sygnatury dostępu współdzielonego (SAS) przy użyciu Eksplorator usługi Azure Storage lub Azure Portal. Token sygnatury dostępu współdzielonego zapewnia bezpieczny, delegowany dostęp do zasobów na koncie usługi Azure Storage.

## <a name="create-sas-tokens-with-azure-storage-explorer"></a>Tworzenie tokenów SAS przy użyciu Eksplorator usługi Azure Storage

### <a name="prerequisites"></a>Wymagania wstępne

* Wymagana jest aplikacja [**Eksplorator usługi Azure Storage**](../../../vs-azure-tools-storage-manage-with-storage-explorer.md) zainstalowana w środowisku deweloperskim systemu Windows, MacOS lub Linux. Eksplorator usługi Azure Storage to bezpłatne narzędzie, które pozwala łatwo zarządzać zasobami magazynu w chmurze platformy Azure.
* Po zainstalowaniu aplikacji Eksplorator usługi Azure Storage [Połącz ją z kontem magazynu](../../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#connect-to-a-storage-account-or-service) używanym do tłumaczenia dokumentów.

### <a name="create-your-tokens"></a>Tworzenie tokenów

### <a name="sas-tokens-for-containers"></a>[Tokeny SAS dla kontenerów](#tab/Containers)

1. Otwórz aplikację Eksplorator usługi Azure Storage na komputerze lokalnym i przejdź do podłączonych **kont magazynu**.
1. Rozwiń węzeł konta magazynu i wybierz pozycję **kontenery obiektów BLOB**.
1. Rozwiń węzeł kontenery obiektów blob, a następnie kliknij prawym przyciskiem myszy węzeł **kontenera** magazynu lub Wyświetl menu Opcje.
1. Wybierz pozycję **Pobierz sygnaturę dostępu współdzielonego...** z menu Opcje.
1. W oknie **sygnatura dostępu współdzielonego** wybierz następujące opcje:
    * Wybierz **zasady dostępu** (wartość domyślna to None).
    * Określ datę **początkową** i godzinę **wygaśnięcia** klucza. Krótki cykl życia jest zalecany, ponieważ po wygenerowaniu nie można odwołać sygnatury dostępu współdzielonego.
    * Wybierz **strefę czasową** dla daty i godziny rozpoczęcia i wygaśnięcia (Domyślnie wartość lokalna).
    * Zdefiniuj **uprawnienia** kontenera, sprawdzając i/lub czyszcząc odpowiednie pole wyboru.
    * Przejrzyj i wybierz pozycję **Utwórz**.

1. Zostanie wyświetlone nowe okno z nazwą **kontenera** , **identyfikatorem URI** i **ciągiem zapytania** dla kontenera.  
1. **Skopiuj i Wklej wartości kontenera, identyfikatora URI i ciągu zapytania w bezpiecznej lokalizacji. Będą one wyświetlane tylko raz i nie można ich pobrać po zamknięciu okna.**
1. Aby utworzyć adres URL sygnatury dostępu współdzielonego, Dołącz token sygnatury dostępu współdzielonego (URI) do adresu URL usługi magazynu.

### <a name="sas-tokens-for-blobs"></a>[Tokeny SAS dla obiektów BLOB](#tab/blobs)

1. Otwórz aplikację Eksplorator usługi Azure Storage na komputerze lokalnym i przejdź do podłączonych **kont magazynu**.
1. Rozwiń węzeł magazynu i wybierz pozycję **kontenery obiektów BLOB**.
1. Rozwiń węzeł kontenery obiektów blob i wybierz węzeł **kontenera** , aby wyświetlić zawartość okna głównego.
1. Wybierz obiekt BLOB, do którego chcesz delegować dostęp do sygnatury dostępu współdzielonego, a następnie kliknij prawym przyciskiem myszy, aby wyświetlić menu Opcje.
1. Wybierz pozycję **Pobierz sygnaturę dostępu współdzielonego...** z menu Opcje.
1. W oknie **sygnatura dostępu współdzielonego** wybierz następujące opcje:
    * Wybierz **zasady dostępu** (wartość domyślna to None).
    * Określ datę **początkową** i godzinę **wygaśnięcia** klucza. Krótki cykl życia jest zalecany, ponieważ po wygenerowaniu nie można odwołać sygnatury dostępu współdzielonego.
    * Wybierz **strefę czasową** dla daty i godziny rozpoczęcia i wygaśnięcia (Domyślnie wartość lokalna).
    * Zdefiniuj **uprawnienia** kontenera, sprawdzając i/lub czyszcząc odpowiednie pole wyboru.
    * Przejrzyj i wybierz pozycję **Utwórz**.
1. Zostanie wyświetlone nowe okno z nazwą **obiektu BLOB** , **identyfikatorem URI** i **ciągiem zapytania** dla obiektu BLOB.  
1. **Skopiuj i Wklej wartości BLOB, URI i ciągu zapytania w bezpiecznej lokalizacji. Zostaną one wyświetlone tylko raz i nie będzie można go pobrać po zamknięciu okna.**
1. Aby utworzyć adres URL sygnatury dostępu współdzielonego, Dołącz token sygnatury dostępu współdzielonego (URI) do adresu URL usługi magazynu.

---

## <a name="create-sas-tokens-for-blobs-in-the-azure-portal"></a>Utwórz tokeny sygnatury dostępu współdzielonego dla obiektów BLOB w Azure Portal

> [!NOTE]
> Tworzenie tokenów sygnatury dostępu współdzielonego dla kontenerów bezpośrednio w Azure Portal nie jest obecnie obsługiwane. Można jednak utworzyć token sygnatury dostępu współdzielonego z [**Eksplorator usługi Azure Storage**](#create-sas-tokens-with-azure-storage-explorer) lub wykonać zadanie [programowo](../../../storage/blobs/sas-service-create.md).

<!-- markdownlint-disable MD024 -->
### <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, musisz:

* Aktywne [**konto platformy Azure**](https://azure.microsoft.com/free/cognitive-services/).  Jeśli go nie masz, możesz [**utworzyć bezpłatne konto**](https://azure.microsoft.com/free/).
* Zasób usługi [**translatora**](https://ms.portal.azure.com/#create/Microsoft) (**nie** Cognitive Services zasobu wieloskładnikowego.  *Zobacz* [Tworzenie nowego zasobu platformy Azure](../../cognitive-services-apis-create-account.md#create-a-new-azure-cognitive-services-resource).  
* [**Konto usługi Azure Blob Storage**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Cały dostęp do usługi Azure Storage odbywa się za pośrednictwem konta magazynu.

### <a name="create-your-tokens"></a>Tworzenie tokenów

Przejdź do [Azure Portal](https://ms.portal.azure.com/#home) i przejdź w następujący sposób:  

 **Twoje konto magazynu** → **kontenery** → **kontener** → **obiektu BLOB**

1. Wybierz pozycję **Generuj sygnaturę dostępu współdzielonego** w menu w górnej części strony.

1. Wybierz **metodę podpisywania** → **klucz delegowania użytkownika**.

1. Zdefiniuj **uprawnienia** , sprawdzając i/lub czyszcząc odpowiednie pole wyboru.

1. Określ godziny **rozpoczęcia** i **wygaśnięcia** klucza.

1. Pole **dozwolone adresy IP** jest opcjonalne i określa adres IP lub zakres adresów IP, z których należy akceptować żądania. Jeśli adres IP żądania nie jest zgodny z adresem IP lub zakresem adresów określonym w tokenie sygnatury dostępu współdzielonego, nie będzie autoryzowany.

1. Pole **dozwolone protokoły** jest opcjonalne i określa protokół dozwolony dla żądania wysłanego za pomocą sygnatury dostępu współdzielonego. Wartość domyślna to HTTPS.

1. Przejrzyj następnie wybierz pozycję **Generuj token SAS i adres URL**.

1. Ciąg zapytania **tokenu sygnatury dostępu współdzielonego obiektu BLOB** i **adres URL sygnatury dostępu współdzielonego obiektu** BLOB będą wyświetlane w dolnym obszarze okna.  

1. **Skopiuj i Wklej wartości token i adres URL sygnatury dostępu współdzielonego obiektu BLOB w bezpiecznej lokalizacji. Będą one wyświetlane tylko raz i nie będzie można ich pobrać po zamknięciu okna.**

1. Aby utworzyć adres URL sygnatury dostępu współdzielonego, Dołącz token sygnatury dostępu współdzielonego (URI) do adresu URL usługi magazynu.

## <a name="learn-more"></a>Więcej tutaj

* [Programistyczne tworzenie tokenów SAS dla obiektów blob lub kontenerów](../../../storage/blobs/sas-service-create.md)
* [Uprawnienia do katalogu, kontenera lub obiektu BLOB](/rest/api/storageservices/create-service-sas#permissions-for-a-directory-container-or-blob)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do tłumaczenia dokumentu](get-started-with-document-translation.md)
>
>