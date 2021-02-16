---
title: Wdróż natywny samouczek języka C++ w usłudze HoloLens
description: Przewodnik Szybki Start przedstawiający sposób uruchamiania natywnego samouczka języka C++ na serwerze HoloLens
author: florianborn71
ms.author: flborn
ms.date: 06/08/2020
ms.topic: quickstart
ms.openlocfilehash: b340a180927b3df9ad51295383b09b03dbbb2d98
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530590"
---
# <a name="quickstart-deploy-native-c-sample-to-hololens"></a>Szybki Start: wdrażanie natywnego przykładu C++ w programie HoloLens

W tym przewodniku szybki start opisano sposób wdrażania i uruchamiania natywnej aplikacji samouczka C++ w systemie HoloLens 2.

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
>* Tworzenie aplikacji samouczka dla urządzeń HoloLens.
>* Zmień poświadczenia ARR w kodzie źródłowym.
>* Wdróż i uruchom przykład na urządzeniu.

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać dostęp do usługi zdalnego renderowania na platformie Azure, musisz najpierw [utworzyć konto](../../../how-tos/create-an-account.md).

Należy zainstalować następujące oprogramowanie:

* Windows SDK 10.0.18362.0 [(pobieranie)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* Najnowsza wersja programu Visual Studio 2019 [(pobieranie)](https://visualstudio.microsoft.com/vs/older-downloads/)
* [Visual Studio Tools dla rzeczywistości mieszanej](/windows/mixed-reality/install-the-tools). W szczególnych przypadkach następujące instalacje *obciążenia* są obowiązkowe:
  * **Programowanie aplikacji klasycznych w języku C++**
  * **Programowanie platforma uniwersalna systemu Windows (platformy UWP)**
* GIT [(pobieranie)](https://git-scm.com/downloads)

## <a name="clone-the-arr-samples-repository"></a>Klonowanie repozytorium przykładów ARR

Pierwszym krokiem jest sklonowanie repozytorium git, które przechowuje globalne przykłady renderowania zdalnego platformy Azure. Otwórz wiersz polecenia (wpisz `cmd` w menu Start systemu Windows) i przejdź do katalogu, w którym chcesz przechowywać przykładowy projekt arr.

Uruchom następujące polecenia:

```cmd
mkdir ARR
cd ARR
git clone https://github.com/Azure/azure-remote-rendering
```

Ostatnie polecenie tworzy podkatalog w katalogu ARR zawierający różne przykładowe projekty dla zdalnego renderowania na platformie Azure.

Samouczek środowiska HoloLens języka C++ znajduje się w podkatalogu *NativeCpp/HoloLens*.

## <a name="build-the-project"></a>Kompilowanie projektu

Otwórz plik rozwiązania *HolographicApp. sln* znajdujący się w podkatalogu *NativeCpp/HoloLens* z programem Visual Studio 2019.

Zmień konfigurację kompilacji, aby *debugować* (lub *Release*) i *arm64*. Upewnij się również, że tryb debugera jest ustawiony na *urządzenie* jako przeciwieństwo do *maszyny zdalnej*:

![Konfiguracja programu Visual Studio](media/vs-config-native-cpp-tutorial.png)

Ponieważ poświadczenia konta są stałe w kodzie źródłowym samouczka, należy zmienić je na prawidłowe poświadczenia. W tym celu Otwórz plik `HolographicAppMain.cpp` w programie Visual Studio i Zmień część, w której utworzono klienta w konstruktorze klasy `HolographicAppMain` :

```cpp
// 2. Create Client
{
    // Users need to fill out the following with their account data and model
    RR::SessionConfiguration init;
    init.AccountId = "00000000-0000-0000-0000-000000000000";
    init.AccountKey = "<account key>";
    init.RemoteRenderingDomain = "westus2.mixedreality.azure.com"; // <change to the region that the rendering session should be created in>
    init.AccountDomain = "westus2.mixedreality.azure.com"; // <change to the region the account was created in>
    m_modelURI = "builtin://Engine";
    m_sessionOverride = ""; // If there is a valid session ID to re-use, put it here. Otherwise a new one is created
    m_client = RR::ApiHandle(RR::RemoteRenderingClient(init));
}
```

W celu zmiany należy zmienić następujące wartości:
* `init.AccountId`, `init.AccountKey` i `init.AccountDomain` do korzystania z danych konta. Zobacz akapit dotyczący sposobu [pobierania informacji o koncie](../../../how-tos/create-an-account.md#retrieve-the-account-information).
* Określ, gdzie utworzyć sesję renderowania zdalnego, modyfikując część regionu `init.RemoteRenderingDomain` ciągu dla innych regionów niż `westus2` na przykład `"westeurope.mixedreality.azure.com"` .
* Ponadto, `m_sessionOverride` można zmienić na istniejący identyfikator sesji. Sesje można utworzyć poza tym przykładem, na przykład za pomocą [skryptu programu PowerShell](../../../samples/powershell-example-scripts.md#script-renderingsessionps1) lub bezpośrednio przy użyciu [interfejsu API REST sesji](../../../how-tos/session-rest-api.md) .
Tworzenie sesji poza próbką jest zalecane, gdy próbka powinna być uruchamiana wiele razy. Jeśli sesja nie zostanie przeniesiona, podczas każdego uruchomienia przykładu zostanie utworzona nowa sesja, co może potrwać kilka minut.

Teraz można skompilować aplikację.

## <a name="launch-the-application"></a>Uruchamianie aplikacji

1. Podłącz urządzenie HoloLens do komputera przy użyciu kabla USB.
1. Włącz urządzenie HoloLens i poczekaj na wyświetlenie menu Start.
1. Uruchom debuger w programie Visual Studio (F5). Aplikacja zostanie automatycznie wdrożona na urządzeniu.

Przykładowa aplikacja powinna zostać uruchomiona, a powinien pojawić się panel tekst, który informuje o bieżącym stanie aplikacji. Stan w czasie uruchamiania to rozpoczęcie nowej sesji lub połączenie z istniejącą sesją. Po zakończeniu ładowania modelu wbudowany model aparatu pojawia się bezpośrednio na pozycji głównej. Zamknięcia model aparatu współdziała prawidłowo z wirującym modułem, który jest renderowany lokalnie.

 Jeśli chcesz uruchomić próbkę później, możesz ją również znaleźć z menu Start HoloLens, ale Zapamiętaj, że może on mieć identyfikator sesji wygasły.

## <a name="next-steps"></a>Następne kroki

Ten przewodnik Szybki Start jest oparty na wynikach samouczka, w którym wyjaśniono, jak zintegrować wszystkie elementy związane z renderowaniem zdalnym w *aplikacji Holographic*. Aby dowiedzieć się, które kroki są niezbędne, postępuj zgodnie z tym samouczkiem:

> [!div class="nextstepaction"]
> [Samouczek: Integrowanie zdalnego renderowania w aplikacji Holographic HoloLens](../../../tutorials/native-cpp/hololens/integrate-remote-rendering-into-holographic-app.md)