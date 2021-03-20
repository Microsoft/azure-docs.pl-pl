---
title: Połącz maszynę hybrydową z serwerami z obsługą usługi Azure Arc
description: Dowiedz się, jak nawiązać połączenie i zarejestrować maszynę hybrydową przy użyciu serwerów z obsługą usługi Azure Arc.
ms.topic: quickstart
ms.date: 12/15/2020
ms.openlocfilehash: c52b8d1f7098a7a2a88a9770a3b768b7fea31775
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101646830"
---
# <a name="quickstart-connect-hybrid-machines-with-azure-arc-enabled-servers"></a>Szybki Start: łączenie maszyn hybrydowych przy użyciu serwerów z obsługą usługi Azure Arc

[Serwery z obsługą usługi Azure Arc](../overview.md) umożliwiają zarządzanie maszynami z systemami Windows i Linux hostowanymi w środowiskach lokalnych, w granicach i w chmurze. W tym przewodniku Szybki Start zostanie wdrożony i skonfigurowany Agent połączonej maszyny na komputerze z systemem Windows lub Linux hostowanym poza platformą Azure na potrzeby zarządzania przez serwery z włączonym łukiem.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Wdrożenie serwerów z włączonym Łukem hybrydowego agenta maszyny połączonej wymaga uprawnień administratora na komputerze, aby zainstalować i skonfigurować agenta. W systemie Linux przy użyciu konta głównego i w systemie Windows z kontem, które jest członkiem lokalnej grupy administratorów.

* Przed rozpoczęciem należy zapoznać się z [wymaganiami wstępnymi](../agent-overview.md#prerequisites) dotyczącymi agenta i sprawdzić następujące kwestie:

    * Na komputerze docelowym działa obsługiwany [system operacyjny](../agent-overview.md#supported-operating-systems).

    * Twoje konto otrzymuje przypisanie do [wymaganych ról platformy Azure](../agent-overview.md#required-permissions).

    * Jeśli komputer nawiązuje połączenie za pośrednictwem zapory lub serwera proxy w celu komunikacji przez Internet, upewnij się, że [wymienione](../agent-overview.md#networking-configuration) adresy URL nie są blokowane.

    * Serwery z obsługą usługi Azure Arc obsługują tylko regiony określone w [tym miejscu](../overview.md#supported-regions).

> [!WARNING]
> Nazwa hosta lub komputer z systemem Linux nie może użyć słowa zastrzeżonego lub znaków towarowych w nazwie. w przeciwnym razie próba zarejestrowania podłączonej maszyny na platformie Azure zakończy się niepowodzeniem. Zobacz [Rozwiązywanie błędów zarezerwowanych nazw zasobów](../../../azure-resource-manager/templates/error-reserved-resource-name.md) , aby uzyskać listę wyrazów zarezerwowanych.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

## <a name="register-azure-resource-providers"></a>Rejestrowanie dostawców zasobów platformy Azure

Serwery z obsługą usługi Azure Arc są zależne od następujących dostawców zasobów platformy Azure w ramach subskrypcji, aby można było korzystać z tej usługi:

* Microsoft. HybridCompute
* Microsoft. GuestConfiguration

Zarejestruj je przy użyciu następujących poleceń:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

## <a name="generate-installation-script"></a>Generuj skrypt instalacyjny

Skrypt służący do automatyzowania pobierania, instalacji i nawiązywania połączenia z usługą Azure Arc jest dostępny w Azure Portal. Aby ukończyć ten proces, wykonaj następujące czynności:

1. Uruchom usługę Azure Arc w Azure Portal, klikając pozycję **wszystkie usługi**, a następnie wyszukując i wybierając **serwery — Azure Arc**.

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="Wyszukaj serwery z obsługą łuku we wszystkich usługach" border="false":::

1. Na stronie **serwery — usługa Azure Arc** wybierz pozycję **Dodaj** w lewym górnym rogu.

1. Na stronie **Wybierz metodę** wybierz kafelek **Dodawanie serwerów przy użyciu interakcyjnego skryptu** , a następnie wybierz pozycję **Generuj skrypt**.

1. Na stronie **Generowanie skryptu** wybierz subskrypcję i grupę zasobów, w której maszyna ma być zarządzana na platformie Azure. Wybierz lokalizację platformy Azure, w której będą przechowywane metadane maszyny. Ta lokalizacja może być taka sama lub inna, jak lokalizacja grupy zasobów.

1. Na stronie **wymagania wstępne** Przejrzyj informacje, a następnie wybierz pozycję **Dalej: szczegóły zasobu**.

1. Na stronie **szczegóły zasobu** podaj następujące informacje:

    1. Z listy rozwijanej **Grupa zasobów** wybierz grupę zasobów, z której będzie zarządzana maszyna.
    1. Z listy rozwijanej **region** wybierz region platformy Azure, w którym mają być przechowywane metadane serwerów.
    1. Z listy rozwijanej **system operacyjny** wybierz system operacyjny, na którym ma być uruchamiany skrypt.
    1. Jeśli komputer komunikuje się za pomocą serwera proxy w celu nawiązania połączenia z Internetem, określ adres IP serwera proxy lub nazwę i numer portu, który będzie używany przez maszynę do komunikacji z serwerem proxy. Wprowadź wartość w formacie `http://<proxyURL>:<proxyport>` .
    1. Wybierz pozycję **Dalej: Tagi**.

1. Na stronie **Tagi** Przejrzyj domyślne sugerowane **znaczniki lokalizacji fizycznej** i wprowadź wartość lub Określ jeden lub więcej **tagów niestandardowych** do obsługi Twoich standardów.

1. Wybierz pozycję **Dalej: Pobierz i uruchom skrypt**.

1. Na stronie **Pobierz i uruchom skrypt** Przejrzyj informacje podsumowujące, a następnie wybierz pozycję **Pobierz**. Jeśli nadal musisz wprowadzić zmiany, wybierz pozycję **Poprzednia**.

## <a name="install-the-agent-using-the-script"></a>Instalowanie agenta za pomocą skryptu

### <a name="windows-agent"></a>Agent systemu Windows

1. Zaloguj się na serwerze.

1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień 64-bitowy programu PowerShell.

1. Przejdź do folderu lub udziału, do którego skopiowano skrypt, i wykonaj go na serwerze, uruchamiając `./OnboardingScript.ps1` skrypt.

### <a name="linux-agent"></a>Agent systemu Linux

1. Aby zainstalować agenta systemu Linux na maszynie docelowej, która może bezpośrednio komunikować się z platformą Azure, uruchom następujące polecenie:

    ```bash
    bash ~/Install_linux_azcmagent.sh
    ```

    * Jeśli komputer docelowy komunikuje się za pomocą serwera proxy, uruchom następujące polecenie:

        ```bash
        bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
        ```

## <a name="verify-the-connection-with-azure-arc"></a>Weryfikowanie połączenia z usługą Azure Arc

Po zainstalowaniu agenta programu i skonfigurowaniu go w celu nawiązania połączenia z serwerami z obsługą usługi Azure Arc przejdź do Azure Portal, aby sprawdzić, czy serwer pomyślnie nawiązał połączenie. Zapoznaj się z maszyną w [Azure Portal](https://aka.ms/hybridmachineportal).

:::image type="content" source="./media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="Pomyślne połączenie z maszyną" border="false":::

## <a name="next-steps"></a>Następne kroki

Teraz, gdy włączono maszynę hybrydową z systemem Linux lub Windows i pomyślnie nawiązano połączenie z usługą, możesz włączyć Azure Policy, aby zrozumieć zgodność na platformie Azure.

Aby dowiedzieć się, jak identyfikować maszynę z włączonymi serwerami z obsługą usługi Azure ARC, na których nie zainstalowano agenta Log Analytics, przejdź do samouczka:

> [!div class="nextstepaction"]
> [Tworzenie przypisania zasad w celu zidentyfikowania niezgodnych zasobów](tutorial-assign-policy-portal.md)
