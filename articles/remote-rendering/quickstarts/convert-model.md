---
title: Konwertowanie modelu
description: Przewodnik Szybki Start pokazujący etapy konwersji dla modelu niestandardowego.
author: florianborn71
ms.author: flborn
ms.date: 01/23/2020
ms.topic: quickstart
ms.openlocfilehash: f3fd214fa62d95430bd8ca62e78fd3df30c77d19
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91652452"
---
# <a name="quickstart-convert-a-model-for-rendering"></a>Szybki Start: konwertowanie modelu do renderowania

W [szybkim samouczku: renderowanie modelu przy użyciu aparatu Unity](render-model.md). wiesz już, jak używać projektu przykładowego środowiska Unity do renderowania wbudowanego modelu. W tym przewodniku przedstawiono sposób konwersji własnych modeli.

Omawiane tematy:

> [!div class="checklist"]
>
> * Konfigurowanie konta usługi Azure Blob Storage na potrzeby danych wejściowych i wyjściowych
> * Przekazywanie i konwertowanie modelu 3D do użycia z funkcją zdalnego renderowania na platformie Azure
> * Uwzględnij przekonwertowany model 3W w aplikacji do renderowania

## <a name="prerequisites"></a>Wymagania wstępne

* Kończenie [szybkiego startu: renderowanie modelu przy użyciu aparatu Unity](render-model.md)
* Zainstaluj Azure PowerShell [(dokumentacja)](https://docs.microsoft.com/powershell/azure/)
  * Otwieranie programu PowerShell z uprawnieniami administratora
  * Wykonane `Install-Module -Name Az -AllowClobber`

## <a name="overview"></a>Omówienie

Moduł renderowania na serwerze nie może współpracować bezpośrednio z formatami modeli źródłowych, takimi jak FBX lub GLTF. Zamiast tego wymaga, aby model miał format binarny.
Usługa konwersji korzysta z modeli z usługi Azure Blob Storage i zapisuje przekonwertowane modele z powrotem do dostarczonego kontenera usługi Azure Blob Storage.

Potrzebne elementy:

* Subskrypcja platformy Azure
* Konto "StorageV2" w Twojej subskrypcji
* Kontener usługi BLOB Storage dla modelu wejściowego
* Kontener magazynu obiektów BLOB dla danych wyjściowych
* Model do przekonwertowania, zobacz [Przykładowe modele](../samples/sample-model.md)
  * Zapoznaj się z listą [obsługiwanych formatów źródła](../how-tos/conversion/model-conversion.md#supported-source-formats)
  * Aby użyć przykładowego skryptu konwersji, upewnij się, że przygotowasz folder wejściowy zawierający model i wszystkie zależności zewnętrzne (takie jak tekstury zewnętrzne lub geometria)

## <a name="azure-setup"></a>Konfiguracja platformy Azure

Jeśli nie masz jeszcze konta, przejdź do [https://azure.microsoft.com/get-started/](https://azure.microsoft.com/get-started/) , kliknij opcję bezpłatnego konta i postępuj zgodnie z instrukcjami.

Gdy masz konto platformy Azure, przejdź do [https://ms.portal.azure.com/#home](https://ms.portal.azure.com/#home) .

### <a name="storage-account-creation"></a>Tworzenie konta magazynu

Aby można było utworzyć magazyn obiektów blob, należy najpierw dysponować kontem magazynu.
Aby go utworzyć, kliknij przycisk "Utwórz zasób":

![Azure — Dodawanie zasobu](media/azure-add-a-resource.png)

Z nowego ekranu wybierz pozycję **Magazyn** po lewej stronie, a następnie pozycję **konto magazynu — obiekt BLOB, plik, tabela, kolejka** w następnej kolumnie:

![Azure — Dodawanie magazynu](media/azure-add-storage.png)

Kliknięcie tego przycisku spowoduje wyświetlenie następującego ekranu z właściwościami magazynu, aby wypełnić:

![Konfiguracja platformy Azure](media/azure-setup1.png)

Wypełnij formularz w następujący sposób:

* Utwórz nową grupę zasobów z linku poniżej pola listy rozwijanej i nadaj jej nazwę **ARR_Tutorial**
* W polu **nazwa konta magazynu**wprowadź unikatową nazwę. **Ta nazwa musi być globalnie unikatowa**, w przeciwnym razie zostanie wyświetlony monit z informacją o tym, że nazwa jest już zajęta. W zakresie tego przewodnika Szybki Start **arrtutorialstoragemy**IT. W związku z tym należy zamienić ją na nazwę dowolnego wystąpienia w tym przewodniku Szybki Start.
* Wybierz **lokalizację** bliską. Najlepiej używać tej samej lokalizacji, która jest używana do konfigurowania renderowania w innym przewodniku Szybki Start.
* **Wydajność** ustawiona na wartość "Standardowa"
* **Rodzaj konta** ustawiony na wartość "StorageV2 (ogólnego przeznaczenia w wersji 2)"
* **Replikacja** ustawiona na "magazyn Geograficznie nadmiarowy do odczytu (RA-GRS)"
* **Warstwa dostępu** ustawiona na "gorąca"

Żadna z właściwości na innych kartach nie musi zostać zmieniona, więc możesz kontynuować pracę z poleceniem **"Przejrzyj + Utwórz"** , a następnie wykonaj kroki, aby zakończyć instalację.

Witryna internetowa zawiera teraz informacje o postępie wdrożenia i raportach "wdrożenie zostało ukończone". Kliknij przycisk **"przejdź do zasobu"** , aby zapoznać się z kolejnymi krokami:

![Ukończono tworzenie usługi Azure Storage](./media/storage-creation-complete.png)

### <a name="blob-storage-creation"></a>Tworzenie magazynu obiektów BLOB

Następnie potrzebujemy dwóch kontenerów obiektów blob, jednego dla danych wejściowych i jednego dla danych wyjściowych.

Na powyższym przycisku **"przejdź do zasobu"** uzyskasz dostęp do strony z panelem po lewej stronie, który zawiera menu listy. Na tej liście w kategorii **"BLOB Service"** kliknij przycisk **"kontenery"** :

![Azure — Dodawanie kontenerów](./media/azure-add-containers.png)

Naciśnij przycisk **"+ kontener"** , aby utworzyć **wejściowy** kontener BLOB Storage.
Podczas tworzenia należy użyć następujących ustawień:
  
* Nazwa = arrinput
* Poziom dostępu publicznego = prywatny

Po utworzeniu kontenera kliknij pozycję **+ kontener** ponownie i powtórz te ustawienia dla kontenera **danych wyjściowych** :

* Nazwa = arroutput
* Poziom dostępu publicznego = prywatny

Teraz powinny istnieć dwa kontenery magazynu obiektów blob:

![Konfiguracja Blob Storage](./media/blob-setup.png)

## <a name="run-the-conversion"></a>Uruchamianie konwersji

Aby ułatwić Wywoływanie usługi konwersji zasobów, udostępniamy skrypt narzędziowy. Znajduje się w folderze *skryptów* i nosi nazwę **Conversion.ps1**.

W szczególności ten skrypt

1. przekazuje wszystkie pliki w danym katalogu z dysku lokalnego do wejściowego kontenera magazynu
1. wywołuje [interfejs API REST konwersji zasobów](../how-tos/conversion/conversion-rest-api.md) , który pobierze dane z wejściowego kontenera magazynu i rozpocznie konwersję, która zwróci identyfikator konwersji
1. sondowanie interfejsu API stanu konwersji z pobranym IDENTYFIKATORem konwersji do momentu zakończenia procesu konwersji z sukcesem lub niepowodzeniem
1. Pobiera link do przekonwertowanego elementu zawartości w magazynie danych wyjściowych

Skrypt odczytuje swoją konfigurację z pliku *Scripts\arrconfig.jsna*. Otwórz ten plik JSON w edytorze tekstu.

```json
{
    "accountSettings": {
        "arrAccountId": "8*******-****-****-****-*********d7e",
        "arrAccountKey": "R***************************************l04=",
        "region": "<your-region>"
    },
    "renderingSessionSettings": {
        "vmSize": "standard",
        "maxLeaseTime": "1:00:00"
    },
    "assetConversionSettings": {
        "localAssetDirectoryPath": "D:\\tmp\\robot",
        "resourceGroup": "ARR_Tutorial",
        "storageAccountName": "arrexamplestorage",
        "blobInputContainerName": "arrinput",
        "inputFolderPath": "robotConversion",
        "inputAssetPath": "robot.fbx",
        "blobOutputContainerName": "arroutput",
        "outputFolderPath":"converted/robot",
        "outputAssetFileName": "robot.arrAsset"
    }
}
```

Konfiguracja w ramach grupy **accountSettings** (Identyfikator konta i klucz) powinna zostać wypełniona analogicznie do poświadczeń w [modelu renderowania za pomocą aparatu Unity — szybki start](render-model.md).

Upewnij się, że w grupie **assetConversionSettings** należy zmienić grupę **zasobów**, **blobInputContainerName**i **blobOutputContainerName** , jak pokazano powyżej.
Należy pamiętać, że wartość **arrtutorialstorage** musi zostać zastąpiona unikatową nazwą, którą pobrano podczas tworzenia konta magazynu.

Zmień **localAssetDirectoryPath** w taki sposób, aby wskazywał katalog na dysku zawierający model, który ma zostać przekształcony. Należy zachować ostrożność w prawidłowym wykorzystaniu ukośników odwrotnych (" \\ ") w ścieżce przy użyciu podwójnych ukośników odwrotnych (" \\ \\ ").

Wszystkie dane ze ścieżki podanej w **localAssetDirectoryPath** zostaną przekazane do kontenera obiektów BLOB **blobInputContainerName** w ramach ścieżki podrzędnej podanej przez **inputFolderPath**. Dlatego w przykładowej konfiguracji powyżej zawartości katalogu "D: \\ tmp \\ Robot" zostanie przekazany do kontenera obiektów BLOB "arrinput" konta magazynu "arrtutorialstorage" pod ścieżką "robotConversion". Już istniejące pliki zostaną nadpisywane.

Zmień **inputAssetPath** na ścieżkę modelu do przekonwertowania — ścieżka jest względna do localAssetDirectoryPath. Użyj znaku "/" zamiast znaku " \\ " jako separatora ścieżki. Tak więc dla pliku "Robot. FBX", który znajduje się bezpośrednio w "D: \\ tmp \\ Robot", użyj "Robot. FBX".

Przekonwertowany model zostanie zapisany z powrotem do kontenera magazynu podanego przez **blobOutputContainerName**. Ścieżka podrzędna może być określona przez podanie opcjonalnej **outputFolderPath**. W powyższym przykładzie "Robot. arrAsset" zostanie skopiowany do wyjściowego kontenera obiektów BLOB w obszarze "skonwertowane/robotny".

Ustawienie konfiguracji **outputAssetFileName** określa nazwę przekonwertowanego elementu zawartości — parametr jest opcjonalny, a wartość pliku wyjściowego zostanie wyliczona z nazwy pliku wejściowego w przeciwnym razie.

Otwórz program PowerShell i upewnij się, że zainstalowano *Azure PowerShell* jak wspomniano w [wymaganiach wstępnych](#prerequisites). Następnie zaloguj się do subskrypcji przy użyciu następującego polecenia i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie:

```PowerShell
Connect-AzAccount
```

> [!NOTE]
> W przypadku, gdy organizacja ma więcej niż jedną subskrypcję, może być konieczne określenie argumentów subskrypcji i dzierżawy. Szczegółowe informacje znajdują się w [dokumentacji Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount).

Przejdź do `azure-remote-rendering\Scripts` katalogu i uruchom skrypt konwersji:

```PowerShell
.\Conversion.ps1 -UseContainerSas
```

Powinieneś wyglądać następująco: ![Conversion.ps1](./media/successful-conversion.png)

## <a name="insert-new-model-into-quickstart-sample-app"></a>Wstaw nowy model do przykładowej aplikacji szybkiego startu

Skrypt konwersji generuje identyfikator URI *sygnatury dostępu współdzielonego (SAS)* dla przekonwertowanego modelu. Teraz można skopiować ten identyfikator URI jako **nazwę modelu** do przykładowej aplikacji szybkiego startu (zobacz [Szybki Start: renderowanie modelu przy użyciu aparatu Unity](render-model.md)).

![Zastąp model w aparacie Unity](./media/replace-model-in-unity.png)

 Przykład powinien teraz ładować i renderować niestandardowy model.

## <a name="optional-re-creating-a-sas-uri"></a>Opcjonalnie: ponowne tworzenie identyfikatora URI sygnatury dostępu współdzielonego

Identyfikator URI sygnatury dostępu współdzielonego utworzony przez skrypt konwersji będzie prawidłowy tylko przez 24 godziny. Jednak po jego wygaśnięciu nie trzeba ponownie konwertować modelu. Zamiast tego można utworzyć nowe sygnatury dostępu współdzielonego w portalu, zgodnie z opisem w następnych krokach:

1. Przejdź do witryny [Azure Portal](https://www.portal.azure.com).
1. Kliknij zasób **konta magazynu** : ![ zrzut ekranu, który wyróżnia wybrany zasób konta magazynu.](./media/portal-storage-accounts.png)
1. Na poniższym ekranie kliknij pozycję **Eksplorator magazynu** na lewym panelu i Znajdź model wyjściowy (plik *. arrAsset* ) w kontenerze magazynu obiektów BLOB *arroutput* . Kliknij prawym przyciskiem myszy plik i wybierz polecenie **Pobierz sygnaturę dostępu współdzielonego** z menu kontekstowego: ![ dostęp do podpisu](./media/portal-storage-explorer.png)
1. Zostanie otwarty nowy ekran, w którym można wybrać datę wygaśnięcia. Naciśnij pozycję **Utwórz**i skopiuj identyfikator URI, który jest wyświetlany w następnym oknie dialogowym. Ten nowy identyfikator URI zastępuje tymczasowy identyfikator URI utworzony przez skrypt.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już podstawy, zapoznaj się z naszymi samouczkami, aby uzyskać bardziej szczegółowe informacje.

Jeśli chcesz poznać szczegóły konwersji modelu, zapoznaj [się z interfejsem API REST konwersji modelu](../how-tos/conversion/conversion-rest-api.md).

> [!div class="nextstepaction"]
> [Samouczek: Wyświetlanie modeli renderowanych zdalnie](../tutorials/unity/view-remote-models/view-remote-models.md)
