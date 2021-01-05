---
title: Generowanie identyfikatora URI sygnatury dostępu współdzielonego dla obrazu maszyny wirtualnej — Azure Marketplace
description: Generuj identyfikator URI sygnatury dostępu współdzielonego (SAS) dla wirtualnych dysków twardych (VHD) w witrynie Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: krsh
ms.date: 10/19/2020
ms.openlocfilehash: e28942a77a1d695a17f3231901f337695e602c64
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97825552"
---
# <a name="how-to-generate-a-sas-uri-for-a-vm-image"></a>Jak wygenerować identyfikator URI sygnatury dostępu współdzielonego dla obrazu maszyny wirtualnej

Podczas procesu publikowania należy podać identyfikator URI sygnatury dostępu współdzielonego (Shared Access Signature) dla każdego wirtualnego dysku twardego skojarzonego z planami (wcześniej nazywanymi jednostkami SKU). Firma Microsoft potrzebuje dostępu do tych wirtualnych dysków twardych podczas procesu certyfikacji. Ten identyfikator URI zostanie wprowadzony na karcie **plany** w centrum partnerskim.

Generowanie identyfikatorów URI sygnatury dostępu współdzielonego dla wirtualnych dysków twardych ma następujące wymagania:

- Obsługują one tylko niezarządzane wirtualne dyski twarde.
- Wymagane są tylko uprawnienia do wyświetlania i odczytu. Nie zapewniaj dostępu do zapisu ani usuwania.
- Czas trwania dostępu (Data wygaśnięcia) powinien mieć co najmniej trzy tygodnie od momentu utworzenia identyfikatora URI SAS.
- Aby chronić przed zmianami czasu UTC, ustaw datę rozpoczęcia na jeden dzień przed bieżącą datą. Na przykład, jeśli bieżąca data to 16 czerwca 2020, wybierz pozycję 6/15/2020.

## <a name="generate-the-sas-address"></a>Generuj adres SAS

Istnieją dwa popularne narzędzia służące do tworzenia adresu SAS (URL):

1. **Eksplorator usługi Azure Storage** — dostępne na Azure Portal.
2. **Interfejs wiersza polecenia platformy Azure** — zalecany dla systemów operacyjnych innych niż Windows oraz zautomatyzowanych lub ciągłych środowisk integracji.

### <a name="using-tool-1-azure-storage-explorer"></a>Korzystanie z narzędzia 1: Eksplorator usługi Azure Storage

1. Przejdź do **konta magazynu**.
1. Otwórz **Eksplorator usługi Storage**.

    :::image type="content" source="media/create-vm/storge-account-explorer.png" alt-text="Okno konta magazynu.":::

3. W **kontenerze** kliknij prawym przyciskiem myszy plik VHD i wybierz pozycję **Pobierz sygnaturę dostępu do udziału**.
4. W oknie dialogowym **sygnatura dostępu współdzielonego** wypełnij następujące pola:

    1. Czas rozpoczęcia — data rozpoczęcia uprawnienia dostępu do dysku VHD. Podaj datę, która jest dniem poprzedzającym bieżącą datę.
    2. Czas wygaśnięcia — Data wygaśnięcia uprawnień dla dostępu do dysku VHD. Podaj datę z co najmniej trzech tygodni poza bieżącą datą.
    3. Uprawnienia — wybierz uprawnienia do odczytu i listy.
    4. Na poziomie kontenera — sprawdź pole wyboru Generuj identyfikator URI sygnatury dostępu współdzielonego na poziomie kontenera.

    ![Okno dialogowe sygnatura dostępu współdzielonego.](media/vm/create-sas-uri-storage-explorer.png)

5. Aby utworzyć skojarzony identyfikator URI sygnatury dostępu współdzielonego dla tego wirtualnego dysku twardego, wybierz pozycję **Utwórz**.
6. Skopiuj identyfikator URI i Zapisz go w pliku tekstowym w bezpiecznej lokalizacji. Ten wygenerowany identyfikator URI SAS dotyczy dostępu na poziomie kontenera. Aby wprowadzić właściwe informacje, edytuj plik tekstowy, aby dodać nazwę dysku VHD.
7. Wstaw nazwę wirtualnego dysku twardego po ciągu VHD w identyfikatorze URI sygnatury dostępu współdzielonego (w tym ukośniku). Końcowy identyfikator URI sygnatury dostępu współdzielonego powinien wyglądać następująco:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

8. Powtórz te kroki dla każdego wirtualnego dysku twardego w planach, które będą publikowane.

### <a name="using-tool-2-azure-cli"></a>Korzystanie z narzędzia 2: interfejs wiersza polecenia platformy Azure

1. Pobierz i zainstaluj [Microsoft Azure CL](/cli/azure/install-azure-cli)i. Wersje są dostępne dla systemów Windows, macOS i różnych dystrybucje systemu Linux.
2. Utwórz plik programu PowerShell (rozszerzenie pliku ps1), Skopiuj poniższy kod, a następnie zapisz go lokalnie.

    ```azurecli-interactive
    az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net’ --name <vhd-name> --permissions rl --start ‘<start-date>’ --expiry ‘<expiry-date>’
    ```

3. Edytuj plik, aby użyć następujących wartości parametrów. Podaj daty w formacie daty/godziny UTC, takie jak 2020-04-01T00:00:00Z.

    - Nazwa konta — Nazwa konta usługi Azure Storage.
    - klucz konta — klucz konta usługi Azure Storage.
    - Data rozpoczęcia — Data początkowa uprawnienia dostępu do dysku VHD. Podaj datę o jeden dzień przed bieżącą datą.
    - Data wygaśnięcia — Data wygaśnięcia uprawnień dla dostępu do dysku VHD. Podaj datę z co najmniej trzech tygodni od bieżącej daty.

    Oto przykład prawidłowych wartości parametrów (w czasie pisania):

    ```azurecli-interactive
    az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ON c+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net’ --name vhds -- permissions rl --start ‘2020-04-01T00:00:00Z’ --expiry ‘2021-04-01T00:00:00Z’
    ```

1. Zapisz zmiany.
2. Korzystając z jednej z następujących metod, Uruchom ten skrypt z uprawnieniami administracyjnymi, aby utworzyć parametry połączenia sygnatury dostępu współdzielonego na poziomie kontenera:

    - Uruchom skrypt z konsoli programu. W systemie Windows kliknij prawym przyciskiem myszy skrypt, a następnie wybierz polecenie **Uruchom jako administrator**.
    - Uruchom skrypt z edytora skryptów programu PowerShell, takiego jak [Windows PowerShell ISE](/powershell/scripting/components/ise/introducing-the-windows-powershell-ise). Ten ekran przedstawia tworzenie parametrów połączenia sygnatury dostępu współdzielonego w ramach tego edytora:

    [![Tworzenie parametrów połączenia sygnatury dostępu współdzielonego w edytorze programu PowerShell](media/vm/create-sas-uri-power-shell-ise.png)](media/vm/create-sas-uri-power-shell-ise.png#lightbox)

6. Skopiuj parametry połączenia sygnatury dostępu współdzielonego i Zapisz je w pliku tekstowym w bezpiecznej lokalizacji. Edytuj ten ciąg, aby dodać informacje o lokalizacji wirtualnego dysku twardego w celu utworzenia końcowego identyfikatora URI sygnatury dostępu współdzielonego.
7. W Azure Portal przejdź do magazynu obiektów blob, który zawiera dysk VHD skojarzony z nowym identyfikatorem URI.
8. Skopiuj adres URL punktu końcowego usługi BLOB Service:

    ![Kopiowanie adresu URL punktu końcowego usługi BLOB Service.](media/vm/create-sas-uri-blob-endpoint.png)

9. Edytuj plik tekstowy przy użyciu parametrów połączenia sygnatury dostępu współdzielonego z kroku 6. Utwórz pełny identyfikator URI sygnatury dostępu współdzielonego przy użyciu tego formatu:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

## <a name="verify-the-sas-uri"></a>Weryfikowanie identyfikatora URI sygnatury dostępu współdzielonego

Sprawdź identyfikator URI sygnatury dostępu współdzielonego przed opublikowaniem w centrum partnerskim, aby uniknąć problemów związanych z przesłaniem żądania przez identyfikator URI sygnatury dostępu współdzielonego. Ten proces jest opcjonalny, ale zalecany.

- Identyfikator URI zawiera nazwę pliku obrazu VHD, łącznie z rozszerzeniem nazwy pliku `.vhd` .
- `Sp=rl` pojawia się blisko środka identyfikatora URI. Ten ciąg zawiera określony dostęp do odczytu i listy.
- Gdy jest `sr=c` wyświetlany, oznacza to, że jest określony dostęp na poziomie kontenera.
- Skopiuj i wklej identyfikator URI do przeglądarki w celu przetestowania i pobrania obiektu BLOB (możesz anulować operację przed ukończeniem pobierania).

## <a name="next-steps"></a>Następne kroki

- Jeśli występują problemy, zobacz komunikaty o [błędach SAS maszyny wirtualnej](azure-vm-sas-failure-messages.md).
- [Zaloguj się do Centrum partnerskiego](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)
- [Tworzenie oferty maszyny wirtualnej w witrynie Azure Marketplace](azure-vm-create.md)
