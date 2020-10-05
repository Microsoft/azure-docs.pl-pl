---
title: Tworzenie elementu członkowskiego usługi Azure łańcucha bloków — interfejs wiersza polecenia platformy Azure
description: Utwórz członka usługi Azure łańcucha bloków dla konsorcjum łańcucha bloków przy użyciu interfejsu wiersza polecenia platformy Azure.
ms.date: 07/23/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 3442c3b6023edcde97aabcb13e91120ba6811027
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91323077"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>Szybki Start: Tworzenie członka usługi Azure łańcucha bloków Service łańcucha bloków przy użyciu interfejsu wiersza polecenia platformy Azure

W tym przewodniku szybki start wdrożono nowego członka łańcucha bloków i konsorcjum w usłudze Azure łańcucha bloków przy użyciu interfejsu wiersza polecenia platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Brak.

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie.

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Cloud Shell można również uruchomić na osobnej karcie przeglądarki, przechodząc do [https://shell.azure.com/bash](https://shell.azure.com/bash) . Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

Jeśli wolisz zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik Szybki Start będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.51 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne jest zainstalowanie lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="prepare-your-environment"></a>Przygotowywanie środowiska

1. Zaloguj się.

    Jeśli używasz lokalnej instalacji interfejsu wiersza polecenia, zaloguj się przy użyciu polecenia [az login](/cli/azure/reference-index#az-login).

    ```azurecli
    az login
    ```

    Wykonaj kroki wyświetlane w terminalu, aby ukończyć proces uwierzytelniania.

1. Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure.

    Aby pracować z odwołaniami do rozszerzenia dla interfejsu wiersza polecenia platformy Azure, należy najpierw zainstalować rozszerzenie.  Rozszerzenia interfejsu wiersza polecenia platformy Azure dają dostęp do poleceń eksperymentalnych i w wersji wstępnej, które nie zostały jeszcze dostarczone jako część podstawowego interfejsu wiersza polecenia.  Aby dowiedzieć się więcej na temat rozszerzeń, w tym aktualizacji i odinstalowywania, zobacz [Używanie rozszerzeń z interfejsem wiersza polecenia platformy Azure](/cli/azure/azure-cli-extensions-overview).

    Zainstaluj [rozszerzenie usługi Azure łańcucha bloków](/cli/azure/ext/blockchain/blockchain) , uruchamiając następujące polecenie:

    ```azurecli-interactive
    az extension add --name blockchain
    ```

1. Utwórz grupę zasobów.

    Usługa Azure łańcucha bloków, podobnie jak wszystkie zasoby platformy Azure, musi zostać wdrożona w grupie zasobów. Grupy zasobów umożliwiają organizowanie powiązanych zasobów platformy Azure i zarządzanie nimi.

    W tym przewodniku szybki start Utwórz grupę zasobów o nazwie Moja _zasobów_ w lokalizacji _Wschodnie_ przy użyciu następującego polecenia [AZ Group Create](/cli/azure/group#az-group-create) :

    ```azurecli-interactive
    az group create \
                     --name "myResourceGroup" \
                     --location "eastus"
    ```

## <a name="create-a-blockchain-member"></a>Tworzenie elementu członkowskiego łańcucha bloków

Członek usługi Azure łańcucha bloków to węzeł łańcucha bloków w sieci prywatnej łańcucha bloków. Podczas aprowizacji elementu członkowskiego można utworzyć sieć konsorcjum lub dołączyć do niej. Potrzebujesz co najmniej jednego elementu członkowskiego dla sieci konsorcjum. Liczba członków łańcucha bloków wymaganych przez uczestników zależy od danego scenariusza. Uczestnicy konsorcjum mogą mieć co najmniej jednego członka łańcucha bloków lub mogą współdzielić członków z innymi uczestnikami. Aby uzyskać więcej informacji na temat konsorcjów, zobacz [Azure łańcucha bloków Service Consortium](consortium.md).

Istnieje kilka parametrów i właściwości, które należy przekazać. Zastąp przykładowe parametry wartościami.

```azurecli-interactive
az blockchain member create \
                            --resource-group "MyResourceGroup" \
                            --name "myblockchainmember" \
                            --location "eastus" \
                            --password "strongMemberAccountPassword@1" \
                            --protocol "Quorum" \
                            --consortium "myconsortium" \
                            --consortium-management-account-password "strongConsortiumManagementPassword@1" \
                            --sku "Basic"
```

| Parametr | Opis |
|---------|-------------|
| **Grupa zasobów** | Nazwa grupy zasobów, w której są tworzone zasoby usługi Azure łańcucha bloków. Użyj grupy zasobów utworzonej w poprzedniej sekcji.
| **name** | Unikatowa nazwa identyfikująca członka usługi Azure łańcucha bloków Service łańcucha bloków. Nazwa jest używana dla publicznego adresu punktu końcowego. Na przykład `myblockchainmember.blockchain.azure.com`.
| **przeniesienie** | Region świadczenia usługi Azure, w którym jest tworzony element członkowski łańcucha bloków. Na przykład `westus2`. Wybierz lokalizację najbliżej użytkowników lub innych aplikacji Azure. Funkcje mogą być niedostępne w niektórych regionach. Usługa Azure łańcucha bloków Data Manager jest dostępna w następujących regionach świadczenia usługi Azure: Wschodnie stany USA i Europa Zachodnia.
| **hasło** | Hasło dla domyślnego węzła transakcji elementu członkowskiego. Użyj hasła uwierzytelniania podstawowego podczas nawiązywania połączenia z domyślnym punktem końcowym węzła transakcji elementu członkowskiego łańcucha bloków.
| **protokol** | Protokół łańcucha bloków. Obecnie jest obsługiwany protokół *kworum* .
| **ustanawiające** | Nazwa konsorcjum do przyłączenia lub utworzenia. Aby uzyskać więcej informacji na temat konsorcjów, zobacz [Azure łańcucha bloków Service Consortium](consortium.md).
| **konsorcjum — zarządzanie — hasło** | Hasło konta konsorcjum jest również znane jako hasło do konta elementu członkowskiego. Hasło konta elementu członkowskiego służy do szyfrowania klucza prywatnego dla konta Ethereum utworzonego dla elementu członkowskiego. Do zarządzania konsorcjum używasz konta elementu członkowskiego i hasła konta elementu członkowskiego.
| **Magazyn** | Typ warstwy. *Standardowa* lub *podstawowa*. Skorzystaj z warstwy *podstawowa* na potrzeby tworzenia, testowania i sprawdzania poprawności koncepcji. Użyj warstwy *standardowa* dla wdrożeń klasy produkcyjnej. W przypadku korzystania z łańcucha bloków Data Manager lub wysyłania dużej liczby transakcji prywatnych należy również użyć warstwy *standardowa* . Zmiana warstwy cenowej między podstawowa i Standardowa po utworzeniu elementu członkowskiego nie jest obsługiwana.

Utworzenie elementu członkowskiego łańcucha bloków i obsłudze zasobów trwa około 10 minut.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Możesz użyć elementu członkowskiego łańcucha bloków utworzonego dla następnego przewodnika Szybki start lub samouczka. Gdy zasoby nie będą już potrzebne, można je usunąć przez usunięcie `myResourceGroup` grupy zasobów utworzonej dla przewodnika Szybki Start.

Uruchom następujące polecenie, aby usunąć grupę zasobów i wszystkie powiązane zasoby.

```azurecli-interactive
az group delete \
                 --name myResourceGroup \
                 --yes
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wdrożono członka usługi Azure łańcucha bloków i nowego konsorcjum. Skorzystaj z następnego przewodnika Szybki Start, aby użyć usługi Azure łańcucha bloków Development Kit dla Ethereum w celu dołączenia do członka usług Azure łańcucha bloków.

> [!div class="nextstepaction"]
> [Użyj Visual Studio Code, aby nawiązać połączenie z usługą Azure łańcucha bloków](connect-vscode.md)
