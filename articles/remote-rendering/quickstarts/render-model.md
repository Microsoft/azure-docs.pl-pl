---
title: Renderowanie modelu przy użyciu środowiska Unity
description: Przewodnik Szybki Start, który prowadzi użytkownika przez kroki w celu renderowania modelu
author: florianborn71
ms.author: flborn
ms.date: 01/23/2020
ms.topic: quickstart
ms.openlocfilehash: 525872ca3ad2558c327b7b856254319d3db2dc7f
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593997"
---
# <a name="quickstart-render-a-model-with-unity"></a>Szybki Start: renderowanie modelu przy użyciu aparatu Unity

W tym przewodniku szybki start opisano, jak uruchomić próbkę Unity, która zdalnie renderuje wbudowany model przy użyciu usługi renderowania zdalnego (ARR) platformy Azure.

Nie będziemy szczegółowo podawać samego interfejsu API ARR ani jak skonfigurować nowy projekt środowiska Unity. Te tematy zostały omówione w [samouczku: wyświetlanie zdalnie renderowane modele](../tutorials/unity/view-remote-models/view-remote-models.md).

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
>
>* Konfigurowanie lokalnego środowiska programistycznego
>* Pobierz i skompiluj przykładową aplikację z przewodnikiem Szybki Start dla aparatu Unity
>* Renderowanie modelu w przykładowej aplikacji z przewodnikiem Szybki Start

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać dostęp do usługi zdalnego renderowania na platformie Azure, musisz najpierw [utworzyć konto](../how-tos/create-an-account.md).

Należy zainstalować następujące oprogramowanie:

* Windows SDK 10.0.18362.0 [(pobieranie)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* Najnowsza wersja programu Visual Studio 2019 [(pobieranie)](https://visualstudio.microsoft.com/vs/older-downloads/)
* [Visual Studio Tools dla rzeczywistości mieszanej](/windows/mixed-reality/install-the-tools). W szczególnych przypadkach następujące instalacje *obciążenia* są obowiązkowe:
  * **Programowanie aplikacji klasycznych w języku C++**
  * **Programowanie platforma uniwersalna systemu Windows (platformy UWP)**
* GIT [(pobieranie)](https://git-scm.com/downloads)
* 2019.3.1 Unity [(pobieranie)](https://unity3d.com/get-unity/download)
  * Zainstaluj te moduły w aparacie Unity:
    * **Platformy UWP** — obsługa kompilacji platforma uniwersalna systemu Windows
    * **IL2CPP** — obsługa kompilacji systemu Windows (IL2CPP)

## <a name="clone-the-sample-app"></a>Klonowanie przykładowej aplikacji

Otwórz wiersz polecenia (wpisz `cmd` w menu Start systemu Windows) i przejdź do katalogu, w którym chcesz przechowywać przykładowy projekt arr.

Uruchom następujące polecenia:

```cmd
mkdir ARR
cd ARR
git clone https://github.com/Azure/azure-remote-rendering
```

Ostatnie polecenie tworzy podkatalog w katalogu ARR zawierający różne przykładowe projekty dla zdalnego renderowania na platformie Azure.

Przykładowa aplikacja szybkiego startu dla aparatu Unity znajduje się w podkatalogu *Unity/szybki start*.

## <a name="rendering-a-model-with-the-unity-sample-project"></a>Renderowanie modelu z przykładowym projektem Unity

Otwórz Centrum Unity i Dodaj przykładowy projekt, który jest folderem *ARR\azure-Remote-rendering\Unity\Quickstart* .
Otwórz projekt. W razie potrzeby Zezwól platformie Unity na uaktualnienie projektu do zainstalowanej wersji.

Domyślny model, który jest renderowany, jest [wbudowanym przykładowym modelem](../samples/sample-model.md). Pokażemy sposób konwersji modelu niestandardowego przy użyciu usługi konwersji ARR w [następnym przewodniku szybki start](convert-model.md).

### <a name="enter-your-account-info"></a>Wprowadź informacje o koncie

1. W przeglądarce zasobów aparatu Unity przejdź do folderu *sceny* i Otwórz scenę **szybkiego startu** .
1. Z *hierarchii* wybierz obiekt gry **RemoteRendering** .
1. W *Inspektorze* wprowadź [poświadczenia konta](../how-tos/create-an-account.md). Jeśli nie masz jeszcze konta, [Utwórz je](../how-tos/create-an-account.md).

![Informacje o koncie ARR](./media/arr-sample-account-info.png)

> [!IMPORTANT]
> Ustaw **RemoteRenderingDomain** na `<region>.mixedreality.azure.com` , gdzie `<region>` jest [jednym z dostępnych regionów w sąsiedztwie](../reference/regions.md). \
> Ustaw **AccountDomain** na [domenę konta](../how-tos/create-an-account.md#retrieve-the-account-information) wyświetlaną w witrynie Azure Portal.

Później chcemy wdrożyć ten projekt w usłudze HoloLens i nawiązać połączenie z usługą renderowania zdalnego z tego urządzenia. Ze względu na to, że nie mamy łatwego sposobu wprowadzania poświadczeń na urządzeniu, próbka szybkiego startu **zapisze poświadczenia w scenie aparatu Unity**.

> [!WARNING]
> Upewnij się, że projekt z zapisanymi poświadczeniami nie jest sprawdzany w niektórych repozytorium, w którym byłyby przeciekowe informacje o logowaniu tajne.

### <a name="create-a-session-and-view-the-default-model"></a>Tworzenie sesji i przeglądanie domyślnego modelu

Naciśnij przycisk **odtwarzania** aparatu Unity, aby rozpocząć sesję. Na dole okienka ekranu w panelu *gry* powinna zostać wyświetlona nakładka z tekstem stanu. Sesja zostanie przejdzie do serii przejść stanu. W stanie **początkowym** serwer ma wartość Spuninst, co trwa kilka minut. Po sukcesie przechodzi do stanu **gotowości** . Teraz sesja przechodzi w stan **połączenia** , w którym próbuje dotrzeć do środowiska uruchomieniowego renderowania na tym serwerze. Po pomyślnym przeniesieniu przykładowego przejścia do stanu **połączonego** . W tym momencie rozpocznie się pobieranie modelu do renderowania. Ze względu na rozmiar modelu pobieranie może potrwać kilka minut. Następnie zostanie wyświetlony model zdalnie renderowany.

![Dane wyjściowe z przykładu](media/arr-sample-output.png)

Gratulacje! Teraz przeglądasz zdalnie renderowany model!

## <a name="inspecting-the-scene"></a>Sprawdzanie sceny

Po uruchomieniu połączenia renderowania zdalnego panel inspektora aktualizuje z dodatkowymi informacjami o stanie: ![ odtwarzanie przykładu aparatu Unity](./media/arr-sample-configure-session-running.png)

Teraz możesz eksplorować wykres sceny, wybierając nowy węzeł i klikając polecenie **Pokaż elementy podrzędne** w Inspektorze.

![Hierarchia aparatu Unity](./media/unity-hierarchy.png)

W scenie znajduje się obiekt [płaszczyzny wycinania](../overview/features/cut-planes.md) . Spróbuj włączyć ją we właściwościach i przenieść ją wokół:

![Zmiana płaszczyzny wycinania](media/arr-sample-unity-cutplane.png)

Aby zsynchronizować transformacje, kliknij pozycję **Synchronizuj teraz** lub zaznacz opcję **Synchronizuj każdą klatkę** . W przypadku właściwości składnika wystarczy zmienić ich rozmiar.

## <a name="next-steps"></a>Następne kroki

W następnym przewodniku szybki start wykonamy wdrożenie przykładu do urządzenia HoloLens w celu wyświetlenia zdalnie renderowanego modelu w oryginalnym rozmiarze.

> [!div class="nextstepaction"]
> [Szybki start: Wdrażanie przykładu środowiska Unity na urządzeniu HoloLens](deploy-to-hololens.md)

Alternatywnie można również wdrożyć ten przykład na komputerze stacjonarnym.

> [!div class="nextstepaction"]
> [Szybki Start: wdrażanie przykładu aparatu Unity na pulpicie](deploy-to-desktop.md)