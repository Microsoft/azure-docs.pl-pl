---
title: Samouczek — symulacja błędu podczas odczytywania danych z regionu podstawowego
titleSuffix: Azure Storage
description: Symulacja błędu podczas odczytywania danych z regionu podstawowego, gdy konto magazynu jest skonfigurowane pod kątem strefy geograficznej do odczytu nadmiarowego (RA-GZRS).
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 04/16/2020
ms.author: tamram
ms.reviewer: artek
ms.custom: devx-track-js
ms.openlocfilehash: 5a0bef4fbfd4ee9aa720dab430a33bbfcd0b918b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91280355"
---
# <a name="tutorial-simulate-a-failure-in-reading-data-from-the-primary-region"></a>Samouczek: symulowanie błędu podczas odczytywania danych z regionu podstawowego

Ten samouczek jest drugą częścią serii. W tym artykule dowiesz się, jak uzyskać [dostęp do magazynu geograficznie nadmiarowego do odczytu](../common/storage-redundancy.md) (Ra-GZRS), symulując awarię.

W celu symulowania awarii można użyć [statycznego routingu](#simulate-a-failure-with-an-invalid-static-route) lub [programu Fiddler](#simulate-a-failure-with-fiddler). Obie metody umożliwiają symulowanie niepowodzeń żądań do podstawowego punktu końcowego konta magazynu [geograficznie nadmiarowego do odczytu](../common/storage-redundancy.md) (Ra-GZRS), co spowoduje, że aplikacja zostanie odczytana z pomocniczego punktu końcowego.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

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

Można utworzyć nieprawidłową trasę statyczną dla wszystkich żądań do podstawowego punktu końcowego konta magazynu [geograficznie nadmiarowego do odczytu](../common/storage-redundancy.md) (Ra-GZRS). W tym samouczku host lokalny jest używany jako brama na potrzeby kierowania żądań do konta magazynu. Dzięki użyciu hosta lokalnego brama powoduje, że wszystkie żądania kierowane do podstawowego punktu końcowego konta magazynu wywołują sprzężenie zwrotne wewnątrz hosta, co następnie prowadzi do wystąpienia błędu. Wykonaj poniższe kroki, aby symulować błąd i przywracanie podstawowego punktu końcowego przy użyciu nieprawidłowej trasy statycznej.

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

```bash
route del <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```console
route delete <destination_ip>
```

Następnie można wznowić działanie aplikacji lub nacisnąć odpowiedni klucz, aby ponownie pobrać przykładowy plik, tym razem potwierdzając, że po raz pierwszy pochodzi z magazynu podstawowego.

## <a name="simulate-a-failure-with-fiddler"></a>Symulowanie błędu przy użyciu programu Fiddler

Aby symulować błąd w usłudze programu Fiddler, należy wstrzyknąć niepowodzenie odpowiedzi dla żądań do podstawowego punktu końcowego konta magazynu RA-GZRS.

Poniższe sekcje pokazują, jak zasymulować błąd i przywrócić podstawowy punkt końcowy przy użyciu programu Fiddler.

### <a name="launch-fiddler"></a>Uruchamianie programu Fiddler

Otwórz program Fiddler i wybierz kolejno pozycje **Reguły** i **Dostosuj reguły**.

![Dostosowywanie zasad programu Fiddler](media/simulate-primary-region-failure/figure1.png)

Zostanie uruchomiony edytor ScriptEditor programu Fiddler i wyświetli plik **SampleRules.js**. Ten plik jest używany do dostosowywania programu Fiddler.

Wklej następujący przykładowy kod w `OnBeforeResponse` funkcji, zastępując ją `STORAGEACCOUNTNAME` nazwą konta magazynu. W zależności od przykładu może być również konieczne zastąpienie `HelloWorld` nazwy pliku testowego (lub prefiksu, takiego jak `sampleFile` ). Nowy kod jest oznaczony jako komentarz, aby upewnić się, że nie działa natychmiast.

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

![Wklejanie dostosowanej reguły](media/simulate-primary-region-failure/figure2.png)

### <a name="start-and-pause-the-application"></a>Uruchamianie i zatrzymywanie aplikacji

Skorzystaj z instrukcji w [poprzednim samouczku][previous-tutorial] , aby uruchomić przykład i pobrać plik testowy, upewniając się, że pochodzi on z magazynu podstawowego. W zależności od platformy docelowej można następnie ręcznie wstrzymać próbkę lub zaczekać na monit.

### <a name="simulate-failure"></a>Symulowanie błędu

Gdy aplikacja jest wstrzymana, przełącz się z powrotem do programu Fiddler i usuń znaczniki komentarza z reguły niestandardowej zapisanej w `OnBeforeResponse` funkcji. Upewnij się, że wybrano opcję **plik** i **Zapisz** , aby zapisać zmiany, aby reguła zaczęła obowiązywać. Ten kod szuka żądań do konta magazynu RA-GZRS i, jeśli ścieżka zawiera nazwę pliku przykładowego, zwraca kod odpowiedzi `503 - Service Unavailable` .

W oknie z uruchomionym przykładem Wznów działanie aplikacji lub naciśnij odpowiedni klucz, aby pobrać przykładowy plik i potwierdzić, że pochodzi z magazynu pomocniczego. Następnie możesz ponownie wstrzymać próbkę lub zaczekaj na monit.

### <a name="simulate-primary-endpoint-restoration"></a>Symulowanie przywracania podstawowego punktu końcowego

W programu Fiddler Usuń lub Dodaj komentarz do reguły niestandardowej. Wybierz pozycję **plik** i **Zapisz** , aby upewnić się, że reguła nie będzie już obowiązywać.

W oknie z uruchomionym przykładem Wznów działanie aplikacji lub naciśnij odpowiedni klucz, aby pobrać przykładowy plik i potwierdzić, że pochodzi on z magazynu podstawowego ponownie. Następnie można wyjść z przykładu.

## <a name="next-steps"></a>Następne kroki

W drugiej części serii omówiono czynności dotyczące symulowania błędu w celu przetestowania magazynu geograficznie nadmiarowego do odczytu.

Aby dowiedzieć się więcej o tym, jak działa magazyn RA-GZRS, a także powiązane z nim zagrożenia, przeczytaj następujący artykuł:

> [!div class="nextstepaction"]
> [Projektowanie aplikacji HA przy użyciu RA-GZRS](../common/geo-redundant-design.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md
