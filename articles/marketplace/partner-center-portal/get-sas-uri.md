---
title: Uzyskiwanie identyfikatora URI podpisu dostępu współdzielonego dla obrazu maszyny Wirtualnej | Azure Marketplace
description: W tym artykule wyjaśniono, jak uzyskać identyfikator URI podpisu dostępu współdzielonego (SAS) dla każdego wirtualnego dysku twardego (VHD).
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mingshen
ms.openlocfilehash: c226d35647e4a5a2b1d583dd6328bfb73dae2a1c
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732636"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>Uzyskiwanie identyfikatora URI podpisu dostępu współdzielonego dla obrazu maszyny Wirtualnej

> [!IMPORTANT]
> Przenosimy zarządzanie ofertami twojej maszyny wirtualnej platformy Azure z portalu cloud partner do centrum partnerów. Dopóki oferty nie zostaną zmigrowane, postępuj zgodnie z instrukcjami w [pobierz identyfikator URI podpisu dostępu współdzielonego dla obrazu maszyny Wirtualnej](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-get-sas-uri) dla portalu Cloud Partner Portal, aby zarządzać ofertami.

W tym artykule opisano sposób generowania jednolitego identyfikatora zasobów (URI) podpisu dostępu współdzielonego (Sygnatura dostępu współdzielonego) dla każdego wirtualnego dysku twardego (VHD).

Podczas procesu publikowania należy podać identyfikator URI dla każdego dysku VHD, który jest skojarzony z planami. Plany te były wcześniej określane jako jednostki SKU lub jednostki przechowujące zapasy. Firma Microsoft potrzebuje dostępu do tych identyfikatorów VHD podczas procesu certyfikacji. Wprowadź ten identyfikator URI na karcie **Plany** w Centrum partnerów.

Podczas generowania identyfikatorów URI sygnatury dostępu Współdzielonego dla vhdów należy spełnić następujące wymagania:

* Obsługiwane są tylko niezarządzane wirtualne identyfikatory wirtualne.
* Wymagane `List` `Read` są tylko uprawnienia. Nie podawaj dostępu do zapisu ani usuwania.
* Czas trwania dostępu (data wygaśnięcia) powinien wynosić co najmniej trzy tygodnie od momentu utworzenia identyfikatora URI sygnatury dostępu Współdzielonego.
* Aby chronić przed zmianami czasu UTC, należy ustawić datę rozpoczęcia na jeden dzień przed bieżącą datą. Jeśli na przykład bieżącą datą jest 6 października 2019 r., wybierz 5.05.2019.

## <a name="generate-the-sas-address"></a>Generowanie adresu Sygnatury dostępu Współdzielonego

Istnieją dwa typowe narzędzia używane do tworzenia adresu Sygnatury dostępu Współdzielonego (URL):

* **Microsoft Storage Explorer** — narzędzie graficzne dostępne dla systemów Windows, macOS i Linux.
* **Interfejs wiersza polecenia platformy Microsoft Azure** — zalecane dla systemów operacyjnych innych niż Windows oraz zautomatyzowanych lub ciągłych środowisk integracji.

### <a name="use-microsoft-storage-explorer"></a>Korzystanie z Eksploratora magazynu firmy Microsoft

1. Pobierz i zainstaluj [Eksploratora usługi Microsoft Azure Storage .](https://azure.microsoft.com/features/storage-explorer/)
2. Otwórz eksploratora i w menu po lewej stronie wybierz pozycję **Dodaj konto**. Zostanie wyświetlone okno dialogowe **Łączenie z usługą Azure Storage.**
3. Wybierz **pozycję Dodaj konto platformy Azure,** a następnie zaloguj **się**. Wykonaj kroki wymagane, aby zalogować się do konta platformy Azure.
4. W lewym okienku**Eksplorator** przejdź do **konta magazynu** i rozwiń ten węzeł.
5. Kliknij prawym przyciskiem myszy dysk VHD, a następnie wybierz pozycję **Pobierz podpis dostępu do udziału**.
6. Zostanie wyświetlone okno dialogowe **Podpis dostępu udostępnionego.** Wypełnij następujące pola:

    * **Godzina rozpoczęcia** — data rozpoczęcia uprawnień dla dostępu VHD. Podaj datę, która jest jeden dzień przed bieżącą datą.
    * **Czas wygaśnięcia** — data wygaśnięcia uprawnień dla dostępu VHD. Podaj datę, która jest co najmniej trzy tygodnie po bieżącej dacie.
    * **Uprawnienia** — wybierz uprawnienia Odczyt i Lista.
    * **Poziom kontenera** — zaznacz pole wyboru **Generowanie identyfikatora URI podpisu dostępu współdzielonego na poziomie kontenera.**

        :::image type="content" source="media/create-sas-uri-storage-explorer.png" alt-text="Ilustruje okno dialogowe Podpis dostępu udostępnionego":::

7. Aby utworzyć skojarzony identyfikator URI sygnatury dostępu Współdzielonego dla tej sieci VHD, wybierz pozycję **Utwórz**. Okno dialogowe odświeża i zawiera szczegółowe informacje o tej operacji.
8. Skopiuj **identyfikator URI** i zapisz go w pliku tekstowym w bezpiecznej lokalizacji.

    :::image type="content" source="media/create-sas-uri-shared-access-signature-details.png" alt-text="Ilustruje pole szczegółów podpisu dostępu udostępnionego":::

    Ten wygenerowany identyfikator URI sygnatury dostępu współdzielonego jest przeznaczony do dostępu na poziomie kontenera. Aby było specyficzne, edytuj plik tekstowy, aby dodać nazwę VHD (następny krok).

9. Wstaw nazwę VHD po ciągu vhds w identyfikatorze URI sygnatury dostępu Współdzielonego (dołącz ukośnik do przodu). Końcowy identyfikator URI sygnatury dostępu Współdzielonego powinien wyglądać następująco:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`Na przykład jeśli nazwa VDH `TestRGVM2.vhd`jest , a następnie wynikowy identyfikator URI sygnatury dostępu Współdzielonego będzie:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

10. Powtórz te kroki dla każdego dysku VHD w planach, które opublikujesz.

### <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

1. Pobierz i zainstaluj [narzędzie Microsoft Azure CLI](https://azure.microsoft.com/documentation/articles/xplat-cli-install/). Wersje są dostępne dla systemu Windows, macOS i różnych dystrybucji Linuksa.
2. Utwórz plik programu PowerShell (rozszerzenie pliku.ps1), skopiuj go w poniższym kodzie, a następnie zapisz go lokalnie.

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
    ```

3. Edytuj plik, aby użyć następujących wartości parametrów. Podaj daty w formacie `2020-04-01T00:00:00Z`datatime UTC, na przykład .

    * `<account-name>`– Nazwa konta magazynu platformy Azure
    * `<account-key>`– Klucz konta magazynu platformy Azure
    * `<vhd-name>`– Twoja nazwa VHD
    * `<start-date>`– Data rozpoczęcia uprawnień dla dostępu VHD. Podaj datę na jeden dzień przed bieżącą datą.
    * `<expiry-date>`– Data wygaśnięcia uprawnień dla dostępu VHD. Podaj datę co najmniej trzy tygodnie po bieżącej dacie.

    W tym przykładzie przedstawiono prawidłowe wartości parametrów (w momencie pisania tego zapisu):

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2020-04-01T00:00:00Z' --expiry '2021-04-01T00:00:00Z'
    ```

4. Zapisz zmiany.
5. Korzystając z jednej z następujących metod, uruchom ten skrypt z uprawnieniami administracyjnymi, aby utworzyć **ciąg połączenia sygnatury dostępu współdzielonego** dla dostępu na poziomie kontenera:

    * Uruchom skrypt z konsoli. W systemie Windows kliknij prawym przyciskiem myszy skrypt i wybierz polecenie **Uruchom jako administrator**.
    * Uruchom skrypt z edytora skryptów programu PowerShell, takiego jak [Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise). Na tym ekranie pokazano utworzenie ciągu połączenia sygnatury dostępu Współdzielonego w tym edytorze:

     :::image type="content" source="media/create-sas-uri-power-shell-ise.png" alt-text="Ilustruje utworzenie ciągu połączenia sygnatury dostępu Współdzielonego z programem Windows PowerShell ISE":::

6. Skopiuj ciąg połączenia Sygnatury dostępu Współdzielonego i zapisz go w pliku tekstowym w bezpiecznej lokalizacji. Edytuj ten ciąg, aby dodać informacje o lokalizacji VHD, aby utworzyć końcowy identyfikator URI Sygnatury dostępu Współdzielonego.
7. W witrynie Azure portal przejdź do magazynu obiektów blob, który zawiera dysk VHD skojarzony z nowym identyfikatorem URI.
8. Skopiuj adres URL **punktu końcowego usługi obiektów Blob,** jak pokazano na poniższym zrzucie ekranu

    :::image type="content" source="media/create-sas-uri-blob-endpoint.png" alt-text="Ilustruje punkt końcowy usługi obiektów blob":::

9. Edytuj plik tekstowy za pomocą ciągu połączenia sygnatury dostępu Współdzielonego z kroku 6. Utwórz pełny identyfikator URI sygnatury dostępu Współdzielonego przy użyciu tego formatu:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

    Na przykład, jeśli nazwa VHD `TestRGVM2.vhd`jest , identyfikator URI sygnatury dostępu Współdzielonego będzie:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Powtórz te kroki dla każdego dysku VHD w jednostkach SKU, które zamierzasz opublikować.

## <a name="verify-the-sas-uri"></a>Sprawdź identyfikator URI sygnatury dostępu Współdzielonego

Przejrzyj każdy utworzony identyfikator URI sygnatury dostępu Współdzielonego, korzystając z następującej listy kontrolnej, aby sprawdzić, czy:

* Identyfikator URI wygląda następująco:`<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
* Identyfikator URI zawiera nazwę pliku obrazu VHD, w tym rozszerzenie nazwy pliku ".vhd".
* `sp=rl`pojawia się w pobliżu środka identyfikatora URI. Ten ciąg `Read` pokazuje, że i `List` dostęp jest określony.
* Po `sr=c` wyświetleniu oznacza to, że dostęp na poziomie kontenera jest określony.
* Skopiuj i wklej identyfikator URI do przeglądarki, aby przetestować pobieranie obiektu blob (można anulować operację przed zakończeniem pobierania).

## <a name="next-step"></a>Następny krok

Jeśli masz trudności z utworzeniem identyfikatora URI sygnatury dostępu Współdzielonego, zobacz [Typowe problemy z adresem URL sygnatury dostępu Współdzielonego](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-sas-uri-issues). W przeciwnym razie zapisz identyfikatory URI sygnatury dostępu Współdzielonego w bezpiecznej lokalizacji do późniejszego użycia. Musisz go opublikować ofertę maszyny Wirtualnej w Centrum partnerów.

* [Tworzenie oferty maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer)
