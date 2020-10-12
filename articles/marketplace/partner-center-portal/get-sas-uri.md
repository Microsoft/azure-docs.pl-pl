---
title: Pobieranie identyfikatora URI sygnatury dostępu współdzielonego dla obrazu maszyny wirtualnej — Azure Marketplace
description: Wygeneruj identyfikator URI sygnatury dostępu współdzielonego (SAS) dla wirtualnych dysków twardych (VHD) w witrynie Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 08/14/2020
ms.openlocfilehash: a84f287c6303e093d68dd462ccc5cecc34b463cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89144530"
---
# <a name="get-a-sas-uri-for-your-vm-image"></a>Pobierz identyfikator URI sygnatury dostępu współdzielonego dla obrazu maszyny wirtualnej

Podczas procesu publikowania należy podać identyfikator URI sygnatury dostępu współdzielonego (Shared Access Signature) dla każdego wirtualnego dysku twardego skojarzonego z planami (wcześniej nazywanymi jednostkami SKU). Firma Microsoft potrzebuje dostępu do tych wirtualnych dysków twardych podczas procesu certyfikacji. Ten identyfikator URI zostanie wprowadzony na karcie **plany** w centrum partnerskim.

Generowanie identyfikatorów URI sygnatury dostępu współdzielonego dla wirtualnych dysków twardych ma następujące wymagania:

- Obsługują one tylko niezarządzane wirtualne dyski twarde.
- Wymagane są tylko uprawnienia do wyświetlania i odczytu. Nie zapewniaj dostępu do zapisu ani usuwania.
- Czas trwania dostępu (Data wygaśnięcia) powinien mieć co najmniej trzy tygodnie od momentu utworzenia identyfikatora URI SAS.
- Aby chronić przed zmianami czasu UTC, ustaw datę rozpoczęcia na jeden dzień przed bieżącą datą. Na przykład, jeśli bieżąca data to 16 czerwca 2020, wybierz pozycję 6/15/2020.

## <a name="generate-the-sas-address"></a>Generuj adres SAS

Istnieją dwa popularne narzędzia służące do tworzenia adresu SAS (URL):

1. **Microsoft Eksplorator usługi Storage** — narzędzie graficzne dostępne dla systemów Windows, MacOS i Linux.
2. **Interfejs wiersza polecenia Microsoft Azure** — zalecane dla systemów operacyjnych innych niż Windows oraz zautomatyzowanych lub ciągłych środowisk integracji.

### <a name="using-tool-1-microsoft-storage-explorer"></a>Korzystanie z narzędzia 1: Microsoft Eksplorator usługi Storage

1. Pobierz i zainstaluj [Eksplorator usługi Microsoft Azure Storage](https://azure.microsoft.com/features/storage-explorer/).
2. Otwórz Eksploratora i w menu po lewej stronie wybierz pozycję **Dodaj konto**.
3. W oknie dialogowym **łączenie z usługą Azure Storage** wybierz pozycję **Dodaj konto platformy Azure** i zaloguj się do konta platformy Azure.
4. W okienku po lewej stronie rozwiń węzeł **konta magazynu** .
5. Kliknij prawym przyciskiem myszy wirtualny dysk twardy i wybierz pozycję **Pobierz sygnaturę dostępu do udziału**.
6. W oknie dialogowym **sygnatura dostępu współdzielonego** wypełnij następujące pola:

    1. Czas rozpoczęcia — data rozpoczęcia uprawnienia dostępu do dysku VHD. Podaj datę, która jest dniem poprzedzającym bieżącą datę.
    2. Czas wygaśnięcia — Data wygaśnięcia uprawnień dla dostępu do dysku VHD. Podaj datę z co najmniej trzech tygodni poza bieżącą datą.
    3. Uprawnienia — wybierz uprawnienia do odczytu i listy.
    4. Na poziomie kontenera — sprawdź pole wyboru Generuj identyfikator URI sygnatury dostępu współdzielonego na poziomie kontenera.

    ![Okno dialogowe sygnatura dostępu współdzielonego.](media/vm/create-sas-uri-storage-explorer.png)

7. Aby utworzyć skojarzony identyfikator URI sygnatury dostępu współdzielonego dla tego wirtualnego dysku twardego, wybierz pozycję **Utwórz**. Okno dialogowe odświeża i pokazuje szczegółowe informacje o tej operacji.

8. Skopiuj identyfikator URI i Zapisz go w pliku tekstowym w bezpiecznej lokalizacji.

    ![Kopiowanie identyfikatora URI.](media/vm/create-sas-uri-shared-access-signature-details.png)

    Ten wygenerowany identyfikator URI SAS dotyczy dostępu na poziomie kontenera. Aby wprowadzić właściwe informacje, edytuj plik tekstowy, aby dodać nazwę dysku VHD.

9. Wstaw nazwę wirtualnego dysku twardego po ciągu VHD w identyfikatorze URI sygnatury dostępu współdzielonego (w tym ukośniku). Końcowy identyfikator URI sygnatury dostępu współdzielonego powinien wyglądać następująco:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

1. Powtórz te kroki dla każdego wirtualnego dysku twardego w planach, które będą publikowane.

### <a name="using-tool-2-azure-cli"></a>Korzystanie z narzędzia 2: interfejs wiersza polecenia platformy Azure

1. Pobierz i zainstaluj [Microsoft Azure CL](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)i. Wersje są dostępne dla systemów Windows, macOS i różnych dystrybucje systemu Linux.
2. Utwórz plik programu PowerShell (rozszerzenie pliku ps1), Skopiuj poniższy kod, a następnie zapisz go lokalnie.

    ```JSON
    az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net’ --name <vhd-name> --permissions rl --start ‘<start-date>’ --expiry ‘<expiry-date>’
    ```

3. Edytuj plik, aby użyć następujących wartości parametrów. Podaj daty w formacie daty/godziny UTC, takie jak 2020-04-01T00:00:00Z.

    - Nazwa konta — Nazwa konta usługi Azure Storage.
    - klucz konta — klucz konta usługi Azure Storage.
    - Nazwa wirtualnego dysku twardego — nazwa wirtualnego dysku twardego.
    - Data rozpoczęcia — Data początkowa uprawnienia dostępu do dysku VHD. Podaj datę o jeden dzień przed bieżącą datą.
    - Data wygaśnięcia — Data wygaśnięcia uprawnień dla dostępu do dysku VHD. Podaj datę z co najmniej trzech tygodni od bieżącej daty.

    Oto przykład prawidłowych wartości parametrów (w czasie pisania):

    `az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ON c+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net’ --name vhds -- permissions rl --start ‘2020-04-01T00:00:00Z’ --expiry ‘2021-04-01T00:00:00Z’`

1. Zapisz zmiany.
2. Korzystając z jednej z następujących metod, Uruchom ten skrypt z uprawnieniami administracyjnymi, aby utworzyć parametry połączenia sygnatury dostępu współdzielonego na poziomie kontenera:

    - Uruchom skrypt z konsoli programu. W systemie Windows kliknij prawym przyciskiem myszy skrypt, a następnie wybierz polecenie **Uruchom jako administrator**.
    - Uruchom skrypt z edytora skryptów programu PowerShell, takiego jak [Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise). Ten ekran przedstawia tworzenie parametrów połączenia sygnatury dostępu współdzielonego w ramach tego edytora:

    [![Tworzenie parametrów połączenia sygnatury dostępu współdzielonego w edytorze programu PowerShell](media/vm/create-sas-uri-power-shell-ise.png)](media/vm/create-sas-uri-power-shell-ise.png#lightbox)

6. Skopiuj parametry połączenia sygnatury dostępu współdzielonego i Zapisz je w pliku tekstowym w bezpiecznej lokalizacji. Edytuj ten ciąg, aby dodać informacje o lokalizacji wirtualnego dysku twardego w celu utworzenia końcowego identyfikatora URI sygnatury dostępu współdzielonego.
7. W Azure Portal przejdź do magazynu obiektów blob, który zawiera dysk VHD skojarzony z nowym identyfikatorem URI.
8. Skopiuj adres URL punktu końcowego usługi thebBlob:

    ![Kopiowanie adresu URL punktu końcowego usługi BLOB Service.](media/vm/create-sas-uri-blob-endpoint.png)

9. Edytuj plik tekstowy przy użyciu parametrów połączenia sygnatury dostępu współdzielonego z kroku 6. Utwórz pełny identyfikator URI sygnatury dostępu współdzielonego przy użyciu tego formatu:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

## <a name="verify-the-sas-uri"></a>Weryfikowanie identyfikatora URI sygnatury dostępu współdzielonego

Sprawdź identyfikator URI sygnatury dostępu współdzielonego przed opublikowaniem w centrum partnerskim, aby uniknąć problemów związanych z przesłaniem żądania przez identyfikator URI sygnatury dostępu współdzielonego. Ten proces jest opcjonalny, ale zalecany.

- Identyfikator URI zawiera nazwę pliku obrazu VHD, łącznie z rozszerzeniem nazwy pliku `.vhd` .
- `Sp=rl` pojawia się blisko środka identyfikatora URI. Ten ciąg zawiera określony dostęp do odczytu i listy.
- Gdy jest `sr=c` wyświetlany, oznacza to, że jest określony dostęp na poziomie kontenera.
- Skopiuj i wklej identyfikator URI do przeglądarki w celu przetestowania i pobrania obiektu BLOB (możesz anulować operację przed ukończeniem pobierania).

## <a name="next-step"></a>Następny krok

- Przeczytaj temat [Tworzenie oferty maszyny wirtualnej platformy Azure](azure-vm-create-offer.md).
