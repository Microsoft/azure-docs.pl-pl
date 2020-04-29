---
title: Samouczek — symulacja błędu podczas odczytywania danych z regionu podstawowego
titleSuffix: Azure Storage
description: Symulacja błędu podczas odczytywania danych z regionu podstawowego, gdy jest włączony magazyn Geograficznie nadmiarowy do odczytu (RA-GRS) dla konta magazynu.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: artek
ms.openlocfilehash: 3693ae8c9c425c35ab1a4ebf88b6cd3dfb58f92c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80061291"
---
# <a name="tutorial-simulate-a-failure-in-reading-data-from-the-primary-region"></a>Samouczek: symulowanie błędu podczas odczytywania danych z regionu podstawowego

Ten samouczek jest drugą częścią serii. W tym artykule omówiono zalety [magazynu geograficznie nadmiarowego dostępnego do odczytu](../common/storage-redundancy.md) (RA-GRS), symulując awarię.

W celu symulowania awarii można użyć [statycznego routingu](#simulate-a-failure-with-an-invalid-static-route) lub [programu Fiddler](#simulate-a-failure-with-fiddler). Obie metody umożliwią symulowanie niepowodzeń żądań do podstawowego punktu końcowego konta magazynu [geograficznie nadmiarowego do odczytu](../common/storage-redundancy.md) (RA-GRS), powodując zamianę aplikacji z pomocniczego punktu końcowego.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) .

Część druga serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Uruchamianie i zatrzymywanie aplikacji
> * Symuluj niepowodzenie przy użyciu [nieprawidłowej trasy statycznej](#simulate-a-failure-with-an-invalid-static-route) lub [programu Fiddler](#simulate-a-failure-with-fiddler)
> * Symulowanie przywracania podstawowego punktu końcowego

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy z tym samouczkiem wykonaj poprzedni samouczek: [Udostępnij dane aplikacji za pomocą usługi Azure Storage][previous-tutorial].

Aby symulować awarię routingu statycznego, należy użyć wiersza polecenia z podwyższonym poziomem uprawnień.

Aby symulować awarię przy użyciu programu Fiddler, Pobierz i [Zainstaluj programu Fiddler](https://www.telerik.com/download/fiddler)

## <a name="simulate-a-failure-with-an-invalid-static-route"></a>Symulowanie błędu przy użyciu nieprawidłowej trasy statycznej

Możesz utworzyć nieprawidłową trasę statyczną dla wszystkich żądań kierowanych do podstawowego punktu końcowego konta magazynu [geograficznie nadmiarowego do odczytu](../common/storage-redundancy.md) (RA-GRS). W tym samouczku host lokalny jest używany jako brama na potrzeby kierowania żądań do konta magazynu. Dzięki użyciu hosta lokalnego brama powoduje, że wszystkie żądania kierowane do podstawowego punktu końcowego konta magazynu wywołują sprzężenie zwrotne wewnątrz hosta, co następnie prowadzi do wystąpienia błędu. Wykonaj poniższe kroki, aby symulować błąd i przywracanie podstawowego punktu końcowego przy użyciu nieprawidłowej trasy statycznej.

### <a name="start-and-pause-the-application"></a>Uruchamianie i zatrzymywanie aplikacji

Skorzystaj z instrukcji w [poprzednim samouczku][previous-tutorial] , aby uruchomić przykład i pobrać plik testowy, upewniając się, że pochodzi on z magazynu podstawowego. W zależności od platformy docelowej można następnie ręcznie wstrzymać próbkę lub zaczekać na monit.

### <a name="simulate-failure"></a>Symulowanie błędu

Gdy aplikacja jest wstrzymana, Otwórz wiersz polecenia w systemie Windows jako administrator lub uruchom terminal jako element główny w systemie Linux.

Pobierz informacje o podstawowej domenie punktu końcowego konta magazynu, wprowadzając następujące polecenie w wierszu polecenia lub terminalu, zastępując `STORAGEACCOUNTNAME` nazwę konta magazynu.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
```

Skopiuj adres IP konta magazynu do edytora tekstów w celu późniejszego użycia.

Aby uzyskać adres IP hosta lokalnego, wpisz `ipconfig` w wierszu polecenia systemu Windows lub `ifconfig` w terminalu systemu Linux.

Aby dodać trasę statyczną dla hosta docelowego, wpisz następujące polecenie w wierszu polecenia systemu Windows lub terminalu z systemem Linux, zastępując `<destination_ip>` adres IP konta magazynu i `<gateway_ip>` adresem IP hosta lokalnego.

#### <a name="linux"></a>Linux

```
route add <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route add <destination_ip> <gateway_ip>
```

W oknie z uruchomionym przykładem Wznów działanie aplikacji lub naciśnij odpowiedni klucz, aby pobrać przykładowy plik i potwierdzić, że pochodzi z magazynu pomocniczego. Następnie możesz ponownie wstrzymać próbkę lub zaczekaj na monit.

### <a name="simulate-primary-endpoint-restoration"></a>Symulowanie przywracania podstawowego punktu końcowego

Aby symulować podstawowy punkt końcowy ponownie działał, Usuń nieprawidłową trasę statyczną z tabeli routingu. Dzięki temu wszystkie żądania do podstawowego punktu końcowego będą kierowane za pośrednictwem bramy domyślnej. Wpisz następujące polecenie w wierszu polecenia systemu Windows lub terminalu z systemem Linux.

#### <a name="linux"></a>Linux

```
route del <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route delete <destination_ip>
```

Następnie można wznowić działanie aplikacji lub nacisnąć odpowiedni klucz, aby ponownie pobrać przykładowy plik, tym razem potwierdzając, że po raz pierwszy pochodzi z magazynu podstawowego.

## <a name="simulate-a-failure-with-fiddler"></a>Symulowanie błędu przy użyciu programu Fiddler

Aby symulować błąd przy użyciu programu Fiddler, należy dodać odpowiedź oznaczającą błąd dla żądań kierowanych do podstawowego punktu końcowego konta magazynu RA-GRS.

Poniższe sekcje pokazują, jak zasymulować błąd i przywrócić podstawowy punkt końcowy przy użyciu programu Fiddler.

### <a name="launch-fiddler"></a>Uruchamianie programu Fiddler

Otwórz program Fiddler i wybierz kolejno pozycje **Reguły** i **Dostosuj reguły**.

![Dostosowywanie zasad programu Fiddler](media/storage-simulate-failure-ragrs-account-app/figure1.png)

Zostanie uruchomiony edytor ScriptEditor programu Fiddler i wyświetli plik **SampleRules.js**. Ten plik jest używany do dostosowywania programu Fiddler.

Wklej następujący przykładowy kod w `OnBeforeResponse` funkcji, zastępując `STORAGEACCOUNTNAME` ją nazwą konta magazynu. W zależności od przykładu może być również konieczne zastąpienie `HelloWorld` nazwy pliku testowego (lub prefiksu, takiego jak `sampleFile`). Nowy kod jest oznaczony jako komentarz, aby upewnić się, że nie działa natychmiast.

Po zakończeniu wybierz pozycję **Plik** i **Zapisz**, aby zapisać swoje zmiany. Pozostaw otwarte okno ScriptEditor do użycia w poniższych krokach.

```javascript
    /*
        // Simulate data center failure
        // After it is successfully downloading the blob, pause the code in the sample,
        // uncomment these lines of script, and save the script.
        // It will intercept the (probably successful) responses and send back a 503 error.
        // When you're ready to stop sending back errors, comment these lines of script out again
        //     and save the changes.

        if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
            && (oSession.PathAndQuery.Contains("HelloWorld"))) {
            oSession.responseCode = 503;
        }
    */
```

![Wklejanie dostosowanej reguły](media/storage-simulate-failure-ragrs-account-app/figure2.png)

### <a name="start-and-pause-the-application"></a>Uruchamianie i zatrzymywanie aplikacji

Skorzystaj z instrukcji w [poprzednim samouczku][previous-tutorial] , aby uruchomić przykład i pobrać plik testowy, upewniając się, że pochodzi on z magazynu podstawowego. W zależności od platformy docelowej można następnie ręcznie wstrzymać próbkę lub zaczekać na monit.

### <a name="simulate-failure"></a>Symulowanie błędu

Gdy aplikacja jest wstrzymana, przełącz się z powrotem do programu Fiddler i usuń znaczniki komentarza z reguły niestandardowej zapisanej w `OnBeforeResponse` funkcji. Upewnij się, że wybrano opcję **plik** i **Zapisz** , aby zapisać zmiany, aby reguła zaczęła obowiązywać. Ten kod szuka żądań do konta magazynu RA-GRS i, jeśli ścieżka zawiera nazwę pliku przykładowego, zwraca kod odpowiedzi `503 - Service Unavailable`.

W oknie z uruchomionym przykładem Wznów działanie aplikacji lub naciśnij odpowiedni klucz, aby pobrać przykładowy plik i potwierdzić, że pochodzi z magazynu pomocniczego. Następnie możesz ponownie wstrzymać próbkę lub zaczekaj na monit.

### <a name="simulate-primary-endpoint-restoration"></a>Symulowanie przywracania podstawowego punktu końcowego

W programu Fiddler Usuń lub Dodaj komentarz do reguły niestandardowej. Wybierz pozycję **plik** i **Zapisz** , aby upewnić się, że reguła nie będzie już obowiązywać.

W oknie z uruchomionym przykładem Wznów działanie aplikacji lub naciśnij odpowiedni klucz, aby pobrać przykładowy plik i potwierdzić, że pochodzi on z magazynu podstawowego ponownie. Następnie można wyjść z przykładu.

## <a name="next-steps"></a>Następne kroki

W drugiej części serii omówiono czynności dotyczące symulowania błędu w celu przetestowania magazynu geograficznie nadmiarowego do odczytu.

Aby dowiedzieć się więcej na temat sposobu działania magazynu RA-GRS i powiązanych z nim czynników ryzyka, przeczytaj następujący artykuł:

> [!div class="nextstepaction"]
> [Projektowanie aplikacji HA przy użyciu magazynu RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md
