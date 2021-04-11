---
title: Usługa zaświadczania usługi Azure FPGFA
description: Usługa zaświadczania dla maszyn wirtualnych z serii NP.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: vikancha
ms.openlocfilehash: 563155bb6559f8443f1453a65fa0b1574af106f7
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556246"
---
# <a name="fpga-attestation-for-azure-np-series-vms-preview"></a>Zaświadczanie FPGA dla maszyn wirtualnych platformy Azure NP-Series (wersja zapoznawcza)

Usługa zaświadczania FPGA wykonuje szereg prawidłowych plików punktów kontrolnych projektu (nazywanych "") generowanych przez zestaw narzędzi Xilinx i tworzy plik zawierający zweryfikowany obraz (nazywany "Bitstream"), który można załadować do karty Xilinx U250 FPGA na maszynie wirtualnej z serii NP.  

## <a name="prerequisites"></a>Wymagania wstępne  

Potrzebna jest subskrypcja platformy Azure i konto usługi Azure Storage. Subskrypcja zapewnia dostęp do platformy Azure, a konto magazynu jest używane do przechowywania plików list i danych wyjściowych usługi zaświadczania.  

Udostępniamy skrypty programu PowerShell i bash do przesyłania żądań zaświadczania.   Skrypty używają interfejsu wiersza polecenia platformy Azure, który można uruchomić w systemach Windows i Linux. Program PowerShell może działać w systemach Windows, Linux i macOS.  

Pobieranie interfejsu wiersza polecenia platformy Azure (wymagane):  

https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest  

Program PowerShell dla systemu Windows, Linux i macOS Download (tylko dla skryptów PowerShell):  

https://docs.microsoft.com/powershell/scripting/install/installing-powershell?view=powershell-7  

Aby przesłać do usługi zaświadczania, musisz mieć autoryzację dzierżawy i Identyfikator subskrypcji. Odwiedź stronę https://aka.ms/AzureFPGAAttestationPreview , aby zażądać dostępu. 

## <a name="building-your-design-for-attestation"></a>Kompilowanie projektu na potrzeby zaświadczania  

Preferowany zestaw narzędzi Xilinx do kompilowania projektów to Vitis 2020,2. Można używać plików listow, które zostały utworzone przy użyciu wcześniejszej wersji zestawu narzędzi i nadal są zgodne z 2020,2. Upewnij się, że załadowano poprawną powłokę do kompilacji. Obecnie obsługiwana wersja jest xilinx_u250_gen3x16_xdma_2_1_202010_1. Pliki obsługi można pobrać z Xilinx ALVEO Lounge. 

Musisz dołączyć następujący argument do Vitis (wiersz cmd + +), aby skompilować plik xclbin, który zawiera listę sieciową, a nie Bitstream.   

```--advanced.param compiler.acceleratorBinaryContent=dcp  ```

## <a name="logging-into-azure"></a>Logowanie do platformy Azure  

Przed wykonaniem jakichkolwiek operacji na platformie Azure należy zalogować się do platformy Azure i ustawić subskrypcję autoryzowaną do wywoływania usługi. W ```az login``` ```az account set –s <Sub ID or Name>``` tym celu Użyj poleceń i. Więcej informacji na temat tego procesu opisano tutaj:  

https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest. Użyj opcji "Zaloguj się interaktywnie" lub "Zaloguj się przy użyciu poświadczeń" w wierszu polecenia.  

## <a name="creating-a-storage-account-and-blob-container"></a>Tworzenie konta magazynu i kontenera obiektów BLOB  

Plik listy sieci musi zostać przekazany do kontenera obiektów BLOB usługi Azure Storage, aby można było uzyskać dostęp do usługi zaświadczania.  

Zapoznaj się z tą stroną, aby uzyskać więcej informacji na temat tworzenia konta, kontenera i przekazywania listy sieci jako obiektu BLOB do tego kontenera: https://docs.microsoft.com/azure/storage/blobs/storage-quickstartblobs-cli .  

Można także użyć również Azure Portal.  

## <a name="upload-your-netlist-file-to-azure-blob-storage"></a>Przekazywanie pliku listy sieci do usługi Azure Blob Storage  

Istnieje kilka sposobów kopiowania pliku; Poniżej przedstawiono przykład użycia polecenia AZ Storage upload cmdlet. Polecenie AZ polecenia działa w systemach Linux i Windows. Możesz wybrać dowolną nazwę dla nazwy "BLOB", ale pamiętaj, aby zachować rozszerzenie xclbin. 

```az storage blob upload --account-name <storage account to receive netlist> container-name <blob container name> --name <blob filename> --file <local file with netlist>  ```

## <a name="download-the-attestation-scripts"></a>Pobierz skrypty zaświadczania  

Skrypty sprawdzania poprawności można pobrać z następującego kontenera obiektów BLOB usługi Azure Storage:  

https://fpgaattestation.blob.core.windows.net/validationscripts/validate.zip  

Plik zip ma dwa skrypty programu PowerShell, jeden do przesłania i drugi do monitorowania, podczas gdy trzeci plik jest skrypt bash, który wykonuje obie funkcje.  

## <a name="running-the-attestation-scripts"></a>Uruchamianie skryptów zaświadczania  

Aby uruchomić skrypty, należy podać nazwę konta magazynu, nazwę kontenera obiektów blob, w którym jest przechowywany plik listy sieci, oraz nazwę pliku listy. Należy również utworzyć sygnaturę dostępu współdzielonego (SAS) usługi, która przyznaje dostęp do odczytu i zapisu do kontenera (nie do listy sieci). Sygnatura dostępu współdzielonego jest używana przez usługę zaświadczania do tworzenia lokalnej kopii pliku listy sieci i zapisywania wynikowych plików wyjściowych procesu weryfikacji do kontenera.  

Przegląd sygnatur dostępu współdzielonego jest dostępny tutaj z określonymi informacjami o tym, które są dostępne w tym miejscu. Na stronie SAS usługi znajduje się ważna Uwaga dotycząca ochrony wygenerowanego skojarzenia zabezpieczeń.  Zapoznaj się z ostrzeżeniem, aby zrozumieć konieczność zachowania sygnatury dostępu współdzielonego przed złośliwym lub niezamierzonym użyciem.  

Można wygenerować sygnaturę dostępu współdzielonego dla kontenera za pomocą polecenia AZ Storage Container Generate-SAS. Określ czas wygaśnięcia w formacie UTC, który jest co najmniej kilka godzin poza czasem przesłania; około 6 godzin powinna być większa niż odpowiednia.  

Jeśli chcesz używać katalogów wirtualnych, musisz dołączyć hierarchię katalogów jako część argumentu kontenera. Na przykład w przypadku kontenera o nazwie "weblists" i katalogu wirtualnego o nazwie "image1", który zawiera obiekt BLOB list, jako nazwę kontenera należy określić wartość "weblists/image1". Dołącz wszelkie dodatkowe nazwy katalogów, aby określić dokładniejszą hierarchię. 

### <a name="powershell"></a>PowerShell   

```$sas=$(az storage container generate-sas --account-name <storage acct name> -name <blob container name> --https-only --permissions rwc --expiry <e.g., 2021-01-07T17:00Z> --output tsv)  ```

```.\Validate-FPGAImage.ps1 -StorageAccountName <storage acct name> -Container <blob container name> -BlobContainerSAS $sas -NetlistName <netlist blob filename>  ```

### <a name="bash"></a>Bash  

``` sas=az storage container generate-sas --account-name <storage acct name> -name <blob container name> --https-only --permissions rwc --expiry <2021-01-07T17:00Z> --output tsv  ```

```validate-fpgaimage.sh --storage-account <storage acct name> --container <blob container name> --netlist-name <netlist blob filename> --blob-container-sas $sas ``` 

## <a name="checking-on-the-status-of-your-submission"></a>Sprawdzanie stanu zgłoszenia  

Usługa zaświadczania zwróci identyfikator aranżacji przesłanej. Skrypty przesłania automatycznie Rozpocznij monitorowanie przesłane przez sondowanie w celu ukończenia. Identyfikator aranżacji to podstawowy sposób, aby sprawdzić, co się stało w związku z przesyłaniem danych, więc pamiętaj, że w przypadku wystąpienia problemu. Jako punkty odniesienia zaświadczenie trwa około 30 minut, aby uzyskać mały plik listy sieci (300MB o rozmiarze); plik 1.6 GB trwał godzinę. 

Możesz w dowolnym momencie wywołać skrypt Monitor-Validation.ps1, aby uzyskać stan i wyniki zaświadczania, podając identyfikator aranżacji jako argument:  

```.\Monitor-Validation.ps1 -OrchestrationId < Orchestration ID>  ```

Alternatywnie możesz przesłać żądanie HTTP POST do punktu końcowego usługi zaświadczania:  

https://fpga-attestation.azurewebsites.net/api/ComputeFPGA_HttpGetStatus  

Treść żądania powinna zawierać identyfikator subskrypcji, identyfikator dzierżawy oraz identyfikator aranżacji żądania zaświadczania:  

```
{  

  "OrchestrationId": ”< orchestration ID>”,  

  "ClientSubscriptionId": “<your subscription ID>”,  

  "ClientTenantId": “<your tenant ID>”  

}
```

## <a name="post-validation-steps"></a>Kroki po walidacji

Usługa zapisze dane wyjściowe z powrotem w kontenerze. Jeśli sprawdzanie poprawności zakończy się pomyślnie, kontener będzie mieć oryginalny plik listy sieci (ABC. xclbin), plik z Bitstream (ABC. bit. xclbin), plik, który identyfikuje prywatną lokalizację przechowywanych Bitstream (ABC. Azure. xclbin) i cztery pliki dziennika: jeden dla procesu uruchamiania (abc-log.txt) i jeden dla trzech faz równoległych, które wykonują walidację. Są one nazwane * logPhaseX.txt gdzie X jest liczbą dla fazy. Na maszynie wirtualnej jest używana usługa Azure. xclbin, która sygnalizuje przekazywanie zweryfikowanego obrazu do U250. 

Jeśli walidacja nie powiodła się, zostanie zapisany plik Error-*. txt wskazujący, który krok zakończył się niepowodzeniem. Sprawdź również pliki dziennika, jeśli dziennik błędów wskazuje, że zaświadczanie nie powiodło się. Podczas kontaktowania się z nami w celu uzyskania pomocy technicznej Pamiętaj, aby uwzględnić wszystkie te pliki w ramach żądania pomocy technicznej wraz z IDENTYFIKATORem aranżacji.  

Za pomocą Azure Portal można utworzyć kontener, a także przekazać listę sieci i pobrać pliki Bitstream i log. Przesyłanie żądania zaświadczania i monitorowanie jego postępu w portalu nie jest w tej chwili obsługiwane i należy je wykonać za pomocą skryptów, jak opisano powyżej. 

