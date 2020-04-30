---
title: Uzyskiwanie identyfikatora URI sygnatury dostępu współdzielonego dla obrazu maszyny wirtualnej opartego na Microsoft Azure | Portal Azure Marketplace
description: Wyjaśnia, jak uzyskać identyfikator URI sygnatury dostępu współdzielonego (SAS) dla obrazu maszyny wirtualnej.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: 46b3764fe2a1e09875f2b92a461591b6ff08540f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82147904"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>Uzyskiwanie identyfikatora URI sygnatury dostępu współdzielonego dla obrazu maszyny wirtualnej

> [!IMPORTANT]
> Od 13 kwietnia 2020 rozpocznie się zarządzanie ofertami usługi Azure Virtual Machine w centrum partnerskim. Po przeprowadzeniu migracji utworzysz oferty w centrum partnerskim i zarządzaj nimi. Postępuj zgodnie z instrukcjami w temacie [Uzyskiwanie identyfikatora URI sygnatury dostępu współdzielonego dla obrazu maszyny wirtualnej](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-sas-uri-issues) , aby zarządzać migrowanymi ofertami.

Podczas procesu publikowania należy podać jednolity identyfikator zasobów (URI) dla każdego wirtualnego dysku twardego (VHD) skojarzonego z jednostkami SKU. Firma Microsoft potrzebuje dostępu do tych wirtualnych dysków twardych podczas procesu certyfikacji. W tym artykule wyjaśniono, jak wygenerować identyfikator URI sygnatury dostępu współdzielonego (SAS) dla każdego wirtualnego dysku twardego. Ten identyfikator URI zostanie wprowadzony na karcie **jednostki SKU** w Portal Cloud partner.

Podczas generowania identyfikatorów URI sygnatury dostępu współdzielonego dla wirtualnych dysków twardych należy przestrzegać następujących wymagań:

- Obsługiwane są tylko niezarządzane wirtualne dyski twarde.
- `List`i `Read` uprawnienia są wystarczające. *Nie* udostępniaj `Write` ani `Delete` nie uzyskuj dostępu.
- Czas trwania dostępu (*Data wygaśnięcia*) powinien mieć co najmniej trzy tygodnie od momentu utworzenia identyfikatora URI SAS.
- Aby zabezpieczyć przed odchyleniami czasu UTC, ustaw datę rozpoczęcia na jeden dzień przed bieżącą datą. Na przykład, jeśli bieżąca data to 6 października 2014, wybierz pozycję 10/5/2014.

## <a name="generate-the-sas-url"></a>Generowanie adresu URL sygnatury dostępu współdzielonego

Adres URL sygnatury dostępu współdzielonego można wygenerować na dwa typowe sposoby przy użyciu następujących narzędzi:

- Microsoft Eksplorator usługi Storage — narzędzie graficzne dostępne dla systemów Windows, macOS i Linux
- Microsoft Azure interfejsu wiersza polecenia zalecane dla środowisk nieopartych na systemie Windows i zautomatyzowanych lub ciągłych integracji

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Wykonaj poniższe kroki, aby wygenerować identyfikator URI sygnatury dostępu współdzielonego za pomocą interfejsu wiersza polecenia platformy Azure.

1. Pobierz i zainstaluj [interfejs wiersza polecenia Microsoft Azure](https://azure.microsoft.com/documentation/articles/xplat-cli-install/). Wersje są dostępne dla systemów Windows, macOS i różnych dystrybucje systemu Linux.
2. Utwórz plik programu PowerShell (`.ps1` rozszerzenie pliku), skopiuj go do poniższego kodu, a następnie zapisz go lokalnie.

   ```azurecli-interactive
   az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
   ```

3. Edytuj plik, aby podać poniższe wartości parametrów.  Daty powinny być podane w formacie daty/godziny UTC, `2016-10-25T00:00:00Z`na przykład.
   - `<account-name>`— Nazwa konta usługi Azure Storage
   - `<account-key>`— Klucz konta usługi Azure Storage
   - `<vhd-name>`— Nazwa wirtualnego dysku twardego
   - `<start-date>`— Data rozpoczęcia uprawnienia dostępu do dysku VHD. Podaj datę o jeden dzień przed bieżącą datą.
   - `<expiry-date>`— Data wygaśnięcia uprawnień dla dostępu do dysku VHD.  Podaj datę z co najmniej trzech tygodni poza bieżącą datą.

   W poniższym przykładzie przedstawiono poprawne wartości parametrów (w czasie tego zapisu).

   ```azurecli-interactive
   az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2017-11-06T00:00:00Z' --expiry '2018-08-20T00:00:00Z'
   ```

4. Zapisz zmiany w tym skrypcie programu PowerShell.
5. Uruchom ten skrypt przy użyciu uprawnień administracyjnych, aby wygenerować *Parametry połączenia sygnatury dostępu współdzielonego* na poziomie kontenera.  Można użyć dwóch podstawowych metod:
   - Uruchom skrypt z konsoli programu.  Na przykład, w systemie Windows, kliknij skrypt, a następnie wybierz opcję **Uruchom jako administrator**.
   - Uruchom skrypt z edytora skryptów programu PowerShell, takiego jak [Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise), przy użyciu uprawnień administracyjnych.
     Poniżej pokazano, jak są generowane parametry połączenia sygnatury dostępu współdzielonego w ramach tego edytora.

     ![Generowanie identyfikatora URI sygnatury dostępu współdzielonego w programie PowerShell ISE](./media/publishvm_032.png)

6. Skopiuj otrzymane parametry połączenia SAS i Zapisz je w pliku tekstowym w bezpiecznej lokalizacji.  Edytujesz ten ciąg, aby dodać do niego skojarzone informacje o lokalizacji wirtualnego dysku twardego w celu utworzenia końcowego identyfikatora URI sygnatury dostępu współdzielonego.
7. W Azure Portal przejdź do magazynu obiektów BLOB zawierającego dysk VHD skojarzony z nowo wygenerowanym identyfikatorem URI.
8. Skopiuj wartość adres URL **punktu końcowego BLOB Service**, jak pokazano poniżej.

    ![Blob service punkt końcowy w Azure Portal](./media/publishvm_033.png)

9. Edytuj plik tekstowy przy użyciu parametrów połączenia sygnatury dostępu współdzielonego z kroku 6.  Pełny identyfikator URI sygnatury dostępu współdzielonego zostanie skonstruowany przy użyciu następującego formatu:

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Na przykład, jeśli nazwa przypadku to `TestRGVM2.vhd`, otrzymany identyfikator URI sygnatury dostępu współdzielonego będzie:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Powtórz te kroki dla każdego wirtualnego dysku twardego w jednostkach SKU, które planujesz opublikować.

### <a name="microsoft-storage-explorer"></a>Eksplorator usługi Storage firmy Microsoft

Wykonaj następujące kroki, aby wygenerować identyfikator URI sygnatury dostępu współdzielonego przy użyciu Eksplorator usługi Microsoft Azure Storage.

1. Pobrać i zainstalować program [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
2. Otwórz Eksploratora i na lewym pasku menu kliknij ikonę **Dodaj konto** .  Zostanie wyświetlone okno dialogowe **łączenie z usługą Azure Storage** .
3. Wybierz pozycję **Dodaj konto platformy Azure**, a następnie kliknij pozycję **Zaloguj się...**.  Kontynuuj wymagane kroki, aby zalogować się do konta platformy Azure.
4. W okienku **Eksploratora** po lewej stronie przejdź do **kont magazynu** i rozwiń ten węzeł.
5. Kliknij prawym przyciskiem myszy dysk VHD i wybierz polecenie **Pobierz sygnaturę dostępu do udziału** z menu kontekstowego.

    ![Pobieranie elementu SAS w Eksploratorze Azure](./media/publishvm_034.png)

6. Zostanie wyświetlone okno dialogowe **sygnatura dostępu współdzielonego** . Wprowadź wartości dla następujących pól:
   - **Godzina rozpoczęcia** — uprawnienie do daty rozpoczęcia dostępu do dysku VHD. Podaj datę, która jest dniem poprzedzającym bieżącą datę.
   - **Czas wygaśnięcia** — Data wygaśnięcia uprawnień dla dostępu do dysku VHD.  Podaj datę z co najmniej trzech tygodni poza bieżącą datą.
   - **Uprawnienia** — wybierz uprawnienia `Read` i `List` .

     ![Okno dialogowe SAS w Eksploratorze Azure](./media/publishvm_035.png)

7. Kliknij przycisk **Utwórz** , aby utworzyć skojarzony identyfikator URI sygnatury dostępu współdzielonego dla tego wirtualnego dysku twardego.  W oknie dialogowym zostaną wyświetlone szczegółowe informacje o tej operacji.
8. Skopiuj wartość **adresu URL** i Zapisz ją w pliku tekstowym w bezpiecznej lokalizacji.

    ![Tworzenie identyfikatora URI sygnatury dostępu współdzielonego w Eksploratorze Azure](./media/publishvm_036.png)

    Ten wygenerowany adres URL SAS dotyczy dostępu na poziomie kontenera.  Aby wprowadzić właściwe informacje, należy dodać do niej skojarzoną nazwę dysku VHD.

9. Edytuj plik tekstowy. Wstaw nazwę wirtualnego dysku twardego po `vhds` ciągu w adresie URL sygnatury dostępu współdzielonego (Dołącz początkowy ukośnik do przodu).  Końcowy identyfikator URI sygnatury dostępu współdzielonego powinien mieć format:

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Na przykład, jeśli nazwa przypadku to `TestRGVM2.vhd`, otrzymany identyfikator URI sygnatury dostępu współdzielonego będzie:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Powtórz te kroki dla każdego wirtualnego dysku twardego w jednostkach SKU, które planujesz opublikować.

## <a name="verify-the-sas-uri"></a>Weryfikowanie identyfikatora URI sygnatury dostępu współdzielonego

Przejrzyj i sprawdź każdy wygenerowany identyfikator URI SYGNATURy dostępu współdzielonego, korzystając z poniższej listy kontrolnej.  Sprawdź, czy:

- Identyfikator URI ma postać: `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` +`<sas-connection-string>`
- Identyfikator URI zawiera nazwę pliku obrazu VHD, w tym rozszerzenie nazwy pliku ". VHD".
- W kierunku środka identyfikatora URI `sp=rl` pojawia się. Ten ciąg wskazuje, `Read` że `List` i jest określony dostęp.
- Po tym momencie pojawia `sr=c` się również. Ten ciąg wskazuje, że jest określony dostęp na poziomie kontenera.
- Skopiuj i wklej identyfikator URI do przeglądarki, aby rozpocząć pobieranie skojarzonego obiektu BLOB.  (Można anulować operację przed ukończeniem pobierania).

## <a name="next-steps"></a>Następne kroki

Jeśli masz problemy z generowaniem identyfikatora URI sygnatury dostępu współdzielonego, zobacz [typowe problemy dotyczące adresów URL SAS](./cpp-common-sas-url-issues.md).  W przeciwnym razie Zapisz identyfikatory URI sygnatury dostępu współdzielonego w bezpiecznej lokalizacji do późniejszego użycia. Będzie wymagane [opublikowanie oferty maszyny wirtualnej](./cpp-publish-offer.md) w Portal Cloud partner.
