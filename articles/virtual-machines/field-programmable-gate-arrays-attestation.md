---
title: Azure FPGFA Attestation Service
description: Usługa zaświadczenia dla maszyn wirtualnych z serii NP.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: vikancha
ms.openlocfilehash: a3408d30a9caa24355cf3976235c3a9b8061b95f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531224"
---
# <a name="fpga-attestation-for-azure-np-series-vms-preview"></a>Zaświadczenia FPGA dla maszyn wirtualnych usługi Azure NP-Series (wersja zapoznawcza)

Usługa zaewidencjonowania FPGA przeprowadza serię weryfikacji pliku punktu kontrolnego projektu (nazywanego "listą sieciową") wygenerowanego przez zestaw narzędzi Xilinx i tworzy plik zawierający zweryfikowany obraz (nazywany "bitstreamem"), który można załadować na kartę Układu FpGA Systemu Xilinx U250 na maszynie wirtualnej serii NP.  

## <a name="prerequisites"></a>Wymagania wstępne  

Potrzebna będzie subskrypcja platformy Azure i konto usługi Azure Storage. Subskrypcja zapewnia dostęp do platformy Azure, a konto magazynu jest używane do przechowywania plików netlist i wyjściowych usługi atestowania.  

Zapewniamy skrypty programu PowerShell i powłoki Bash do przesyłania żądań zaświadczenia.   Skrypty używają interfejsu wiersza polecenia platformy Azure, który można uruchamiać w systemach Windows i Linux. Program PowerShell można uruchomić w systemach Windows, Linux i macOS.  

Pobieranie interfejsu wiersza polecenia platformy Azure (wymagane):  

https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest  

Pobieranie programu PowerShell dla systemów Windows, Linux i macOS (tylko dla skryptów programu PowerShell):  

https://docs.microsoft.com/powershell/scripting/install/installing-powershell?view=powershell-7  

Aby przesłać dane do usługi zaświadczenia, musisz mieć autoryzację dzierżawy i identyfikatora subskrypcji. Odwiedź https://aka.ms/AzureFPGAAttestationPreview stronę , aby poprosić o dostęp. 

## <a name="building-your-design-for-attestation"></a>Tworzenie projektu dla zaświadczenia  

Preferowanym zestawem narzędzi Firmy Xilinx do projektów budynku jest V 2020.2. Można używać plików Netlist utworzonych przy użyciu starszej wersji zestawu narzędzi, które są nadal zgodne z wersją 2020.2. Upewnij się, że załadowano poprawną powłokę do kompilacji. Obecnie obsługiwana wersja jest xilinx_u250_gen3x16_xdma_2_1_202010_1. Pliki pomocy technicznej można pobrać ze strony firmy Xilinx Alveo. 

Aby utworzyć plik xclbin zawierający listę netlist zamiast strumienia bitowego, należy dołączyć następujący argument do V kolejce (wiersz polecenia w języku v++).   

```--advanced.param compiler.acceleratorBinaryContent=dcp  ```

## <a name="logging-into-azure"></a>Logowanie do platformy Azure  

Przed wykonaniem jakichkolwiek operacji na platformie Azure musisz zalogować się na platformie Azure i ustawić subskrypcję, która jest autoryzowana do wywołania usługi. W tym ```az login``` celu użyj poleceń i ```az account set –s <Sub ID or Name>``` . Więcej informacji na temat tego procesu można znaleźć tutaj:  

https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest. Użyj opcji "Zaloguj się interaktywnie" lub "Zaloguj się przy użyciu poświadczeń" w wierszu polecenia.  

## <a name="creating-a-storage-account-and-blob-container"></a>Tworzenie konta magazynu i kontenera obiektów blob  

Plik netlist musi zostać przekazany do kontenera obiektów blob usługi Azure Storage w celu uzyskania dostępu przez usługę zaświadczenia.  

Zapoznaj się z tą stroną, aby uzyskać więcej informacji na temat tworzenia konta, kontenera i przekazywania listy sieci jako obiektu blob do tego kontenera: [https://docs.microsoft.com/en-us/azure/storage/blobs/storage-quickstart-blobs-cli](/azure/storage/blobs/storage-quickstart-blobs-cli) .  

Możesz również użyć Azure Portal w tym celu.  

## <a name="upload-your-netlist-file-to-azure-blob-storage"></a>Przekazywanie pliku netlist do usługi Azure Blob Storage  

Istnieje kilka sposobów kopiowania pliku; Poniżej przedstawiono przykład użycia polecenia cmdlet az storage upload. Polecenia az są uruchamiane w systemach Linux i Windows. Możesz wybrać dowolną nazwę dla nazwy "blob", ale pamiętaj o zachowaniu rozszerzenia xclbin. 

```az storage blob upload --account-name <storage account to receive netlist> --container-name <blob container name> --name <blob filename> --file <local file with netlist>  ```

## <a name="download-the-attestation-scripts"></a>Pobieranie skryptów zaświadczenia  

Skrypty weryfikacji można pobrać z następującego kontenera obiektów blob usługi Azure Storage:  

https://fpgaattestation.blob.core.windows.net/validationscripts/validate.zip  

Plik zip zawiera dwa skrypty programu PowerShell— jeden do przesyłania i drugi do monitorowania, a trzeci to skrypt powłoki bash, który wykonuje obie funkcje.  

## <a name="running-the-attestation-scripts"></a>Uruchamianie skryptów zaświadczenia  

Aby uruchomić skrypty, należy podać nazwę konta magazynu, nazwę kontenera obiektów blob, w którym jest przechowywany plik netlist, oraz nazwę pliku netlist. Należy również utworzyć sygnaturę dostępu współdzielonego (SAS, Service Shared Access Signature), która udziela dostępu do odczytu/zapisu do kontenera (a nie listy netlist). Ta sygnatura dostępu współdzielonego jest używana przez usługę zaświadczenia do tworzenia lokalnej kopii pliku netlist i do zapisu z powrotem wynikowych plików wyjściowych procesu walidacji do kontenera.  

Omówienie sygnatur dostępu współdzielonego jest dostępne tutaj. Szczegółowe informacje na temat sygnatury dostępu współdzielonego usługi są dostępne tutaj. Na stronie Sygnatura dostępu współdzielonego usługi znajduje się ważna uwaga na temat ochrony wygenerowanej sygnatury dostępu współdzielonego.  Należy zachować ostrożność, aby zrozumieć potrzebę ochrony sygnatury dostępu współdzielonego przed złośliwym lub niezamierzonym użyciem.  

Sygnaturę dostępu współdzielonego dla kontenera można wygenerować przy użyciu polecenia cmdlet az storage container generate-sas. Określ czas wygaśnięcia w formacie UTC, który upłynie co najmniej kilka godzin po chwili przesłania; około 6 godzin powinno być więcej niż wystarczające.  

Jeśli chcesz użyć katalogów wirtualnych, musisz dołączyć hierarchię katalogów jako część argumentu kontenera. Jeśli na przykład masz kontener o nazwie "netlists" i katalog wirtualny o nazwie "image1", który zawiera obiekt blob netlist, jako nazwę kontenera należy określić "netlists/image1". Dołącz wszelkie dodatkowe nazwy katalogów, aby określić głębszą hierarchię. 

### <a name="powershell"></a>PowerShell   

```$sas=$(az storage container generate-sas --account-name <storage acct name> --name <blob container name> --https-only --permissions rwc --expiry <e.g., 2021-01-07T17:00Z> --output tsv)  ```

```.\Validate-FPGAImage.ps1 -StorageAccountName <storage acct name> -Container <blob container name> -BlobContainerSAS $sas -NetlistName <netlist blob filename>  ```

### <a name="bash"></a>Bash  

``` sas=az storage container generate-sas --account-name <storage acct name> --name <blob container name> --https-only --permissions rwc --expiry <2021-01-07T17:00Z> --output tsv  ```

```validate-fpgaimage.sh --storage-account <storage acct name> --container <blob container name> --netlist-name <netlist blob filename> --blob-container-sas $sas ``` 

## <a name="checking-on-the-status-of-your-submission"></a>Sprawdzanie stanu przesyłania  

Usługa atestacji zwróci identyfikator aranżacji przesyłania. Skrypty przesyłania automatycznie zaczynają monitorować przesyłanie, sondując o ukończenie. Identyfikator aranżacji jest podstawowym sposobem przeglądania, co się stało z przesłaniem, dlatego należy zachować je w przypadku problemu. Jako punkty odniesienia, zaświadczenia dla małego pliku netlist (o rozmiarze 300 MB) trwa około 30 minut. Plik o rozmiarze 1,6 GB trwał godzinę. 

Możesz wywołać skrypt Monitor-Validation.ps1 w dowolnym momencie, aby uzyskać stan i wyniki zaświadczenia, podając identyfikator aranżacji jako argument:  

```.\Monitor-Validation.ps1 -OrchestrationId < Orchestration ID>  ```

Alternatywnie możesz przesłać żądanie POST PROTOKOŁU HTTP do punktu końcowego usługi za pomocą zaświadczenia:  

https://fpga-attestation.azurewebsites.net/api/ComputeFPGA_HttpGetStatus  

Treść żądania powinna zawierać identyfikator subskrypcji, identyfikator dzierżawy i identyfikator aranżacji żądania zaświadczenia:  

```
{  

  "OrchestrationId": ”< orchestration ID>”,  

  "ClientSubscriptionId": “<your subscription ID>”,  

  "ClientTenantId": “<your tenant ID>”  

}
```

## <a name="post-validation-steps"></a>Kroki po weryfikacji

Usługa zapisze dane wyjściowe z powrotem w kontenerze. Jeśli walidacja zakończy się powodzeniem, kontener będzie miał oryginalny plik netlist (abc.xclbin), plik z bitstreamem (abc.bit.xclbin), plik, który identyfikuje prywatną lokalizację przechowywanego strumienia bitowego (abc.azure.xclbin) i cztery pliki dziennika: jeden dla procesu uruchamiania (abc-log.txt) i jeden dla trzech równoległych faz, które wykonują walidację. Są one nazywane *logPhaseX.txt gdzie X jest liczbą dla fazy. Plik azure.xclbin jest używany na maszynie wirtualnej do sygnalizowania przekazania zweryfikowanego obrazu do urządzenia U250. 

Jeśli walidacja nie powiodła się, zapisywany jest plik error-*.txt wskazujący, który krok zakończył się niepowodzeniem. Sprawdź również pliki dziennika, jeśli dziennik błędów wskazuje, że zaświadczenia nie powiodło się. Kontaktując się z nami w celu pomocy technicznej, dołącz wszystkie te pliki w ramach żądania pomocy technicznej wraz z identyfikatorem aranżacji.  

Możesz użyć tej Azure Portal, aby utworzyć kontener, a także przekazać swoją listę sieciową oraz pobrać pliki strumienia bitowego i dziennika. Przesyłanie żądania zaświadczenia i monitorowanie jego postępu za pośrednictwem portalu nie jest obecnie obsługiwane i należy je wykonać za pomocą skryptów zgodnie z powyższym opisem. 

