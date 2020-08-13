---
title: Połącz maszynę hybrydową z serwerami z obsługą usługi Azure Arc
description: Dowiedz się, jak nawiązać połączenie i zarejestrować maszynę hybrydową za pomocą usługi Azure ARC dla serwerów.
ms.topic: quickstart
ms.date: 08/12/2020
ms.openlocfilehash: 2feb9da3600ef3664b0a75bf2d985d698572ff6f
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2020
ms.locfileid: "88173140"
---
# <a name="quickstart-connect-hybrid-machine-with-azure-arc-enabled-servers"></a>Szybki Start: łączenie maszyny hybrydowej z serwerami z obsługą usługi Azure Arc

[Usługa Azure ARC dla serwerów](../overview.md) (wersja zapoznawcza) umożliwia zarządzanie maszynami z systemami Windows i Linux, które znajdują się w środowiskach lokalnych, brzegowych i wielochmurowych, i zarządza nimi. W tym przewodniku szybki start wdrożono i skonfigurujesz agenta podłączonego maszyny na komputerze z systemem Windows lub Linux hostowanym poza platformą Azure na potrzeby zarządzania za pomocą usługi ARC dla serwerów (wersja zapoznawcza).

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Wdrożenie łuku dla serwerów (wersja zapoznawcza) Agent maszyny połączonej hybrydowo musi mieć uprawnienia administratora na tym komputerze, aby zainstalować i skonfigurować agenta. W systemie Linux przy użyciu konta głównego i w systemie Windows z kontem, które jest członkiem lokalnej grupy administratorów.

* Przed rozpoczęciem należy zapoznać się z [wymaganiami wstępnymi](../agent-overview.md#prerequisites) dotyczącymi agenta i sprawdzić następujące kwestie:

    * Na komputerze docelowym działa obsługiwany [system operacyjny](../agent-overview.md#supported-operating-systems).

    * Twoje konto otrzymuje przypisanie do [wymaganych ról platformy Azure](../agent-overview.md#required-permissions).

    * Jeśli komputer nawiązuje połączenie za pośrednictwem zapory lub serwera proxy w celu komunikacji przez Internet, upewnij się, że [wymienione](../agent-overview.md#networking-configuration) adresy URL nie są blokowane.

    * Usługa Azure ARC dla serwerów (wersja zapoznawcza) obsługuje tylko regiony określone w [tym miejscu](../overview.md#supported-regions).

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

## <a name="register-azure-resource-providers"></a>Rejestrowanie dostawców zasobów platformy Azure

Usługa Azure ARC dla serwerów (wersja zapoznawcza) jest zależna od następujących dostawców zasobów platformy Azure w ramach subskrypcji, aby można było korzystać z tej usługi:

* Microsoft. HybridCompute
* Microsoft. GuestConfiguration

Zarejestruj je przy użyciu następujących poleceń:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

## <a name="generate-installation-script"></a>Generuj skrypt instalacyjny

Skrypt służący do automatyzowania pobierania, instalacji i nawiązywania połączenia z usługą Azure Arc jest dostępny w Azure Portal. Aby ukończyć ten proces, wykonaj następujące czynności:

1. Uruchom usługę Azure Arc w Azure Portal, klikając pozycję **wszystkie usługi**, a następnie wyszukując i wybierając pozycję **maszyny — Azure Arc**.

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="Wyszukaj łuk dla serwerów we wszystkich usługach" border="false":::

1. Na stronie **automaty na platformie Azure** wybierz pozycję **Dodaj**, w lewym górnym rogu lub opcję **Utwórz Machine-Azure Arc** w dolnej części środkowego okienka.

1. Na stronie **Wybierz metodę** wybierz kafelek **Dodawanie maszyn przy użyciu interakcyjnego skryptu** , a następnie wybierz pozycję **Generuj skrypt**.

1. Na stronie **Generowanie skryptu** wybierz subskrypcję i grupę zasobów, w której maszyna ma być zarządzana na platformie Azure. Wybierz lokalizację platformy Azure, w której będą przechowywane metadane maszyny.

1. Na stronie **Generuj skrypt** na liście rozwijanej **system operacyjny** wybierz system operacyjny, na którym będzie uruchamiany skrypt.

1. Jeśli komputer komunikuje się za pomocą serwera proxy w celu nawiązania połączenia z Internetem, wybierz pozycję **Dalej: serwer proxy**.

1. Na karcie **serwer proxy** Określ adres IP serwera proxy lub nazwę i numer portu, który będzie używany przez maszynę do komunikacji z serwerem proxy. Wprowadź wartość w formacie `http://<proxyURL>:<proxyport>` .

1. Wybierz pozycję **Recenzja + generowanie**.

1. Na karcie **Recenzja + generowanie** Przejrzyj informacje podsumowujące, a następnie wybierz pozycję **Pobierz**. Jeśli nadal musisz wprowadzić zmiany, wybierz pozycję **Poprzednia**.

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

Po zainstalowaniu agenta programu i skonfigurowaniu go w celu nawiązania połączenia z usługą Azure ARC dla serwerów (wersja zapoznawcza) przejdź do Azure Portal, aby sprawdzić, czy serwer pomyślnie nawiązał połączenie. Zapoznaj się z maszyną w [Azure Portal](https://aka.ms/hybridmachineportal).

:::image type="content" source="./media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="Pomyślne połączenie z maszyną" border="false":::

## <a name="next-steps"></a>Następne kroki

Teraz, gdy włączono maszynę hybrydową z systemem Linux lub Windows i pomyślnie nawiązano połączenie z usługą, możesz włączyć Azure Policy, aby zrozumieć zgodność na platformie Azure.

Aby dowiedzieć się, jak zidentyfikować komputer z włączoną obsługą usługi Azure ARC dla serwerów (wersja zapoznawcza), na którym nie zainstalowano agenta Log Analytics, przejdź do samouczka:

> [!div class="nextstepaction"]
> [Tworzenie przypisania zasad w celu zidentyfikowania niezgodnych zasobów](tutorial-assign-policy-portal.md)
