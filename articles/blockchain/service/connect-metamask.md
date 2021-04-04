---
title: Podłącz maskę do sieci usługi Azure łańcucha bloków Service
description: Nawiązywanie połączenia z siecią usługi Azure łańcucha bloków przy użyciu funkcji dbmask i wdrażanie kontraktu inteligentnego.
ms.date: 09/12/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: 4a45e02a861ff20a4dc774668a4e008f9b42aeea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90530441"
---
# <a name="quickstart-use-metamask-to-connect-and-deploy-a-smart-contract"></a>Szybki Start: używanie maskowania do łączenia i wdrażania kontraktu inteligentnego

W tym przewodniku szybki start nawiążesz połączenie z siecią usługi Azure łańcucha bloków i używasz Remix do wdrożenia inteligentnego kontraktu. Dbmask to rozszerzenie przeglądarki umożliwiające zarządzanie portfelem eteru i wykonywanie akcji inteligentnych kontraktu.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Kończenie [szybkiego startu: Tworzenie elementu członkowskiego łańcucha bloków przy użyciu Azure Portal](create-member.md) lub [szybkiego startu: Tworzenie elementu członkowskiego łańcucha bloków usługi Azure łańcucha bloków przy użyciu interfejsu wiersza polecenia platformy Azure](create-member-cli.md)
* Zainstaluj [rozszerzenie przeglądarki Xmlmask](https://metamask.io)
* Generowanie [portfela](https://metamask.zendesk.com/hc/en-us/articles/360015488971-New-to-MetaMask-Learn-How-to-Setup-MetaMask-the-First-Time) z maską

## <a name="get-endpoint-address"></a>Pobierz adres punktu końcowego

Do nawiązania połączenia z siecią łańcucha bloków potrzebny jest adres punktu końcowego usługi Azure łańcucha bloków. Adresy punktu końcowego i klucze dostępu znajdują się w Azure Portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do elementu członkowskiego usługi Azure łańcucha bloków.
1. Wybierz pozycję **węzły transakcji** i domyślne łącze węzła transakcji.

    ![Wybierz domyślny węzeł transakcji](./media/connect-metamask/transaction-nodes.png)

1. Wybierz **Parametry połączenia > klucze dostępu**.
1. Skopiuj adres punktu końcowego z **protokołu HTTPS (dostęp do klucza 1)**.

    ![Parametry połączenia](./media/connect-metamask/connection-string.png)

## <a name="connect-metamask"></a>Łączenie z maską

1. Otwórz rozszerzenie przeglądarki dbmask i zaloguj się.
1. Z listy rozwijanej sieć wybierz pozycję  **niestandardowe wywołanie RPC**.

    ![Niestandardowe wywołanie RPC](./media/connect-metamask/custom-rpc.png)

1. W obszarze **Nowa sieć > nowy adres URL usługi RPC** wklej skopiowany powyżej adres punktu końcowego.
1. Wybierz pozycję **Zapisz**.

    W przypadku pomyślnego nawiązania połączenia sieć prywatna jest wyświetlana na liście rozwijanej sieć.

    ![Nowa sieć](./media/connect-metamask/new-network.png)

## <a name="deploy-smart-contract"></a>Wdrażanie kontraktu inteligentnego

Remix to środowisko programistyczne oparte na przeglądarce. Korzystając z funkcji dbmasking i remix, możesz wdrażać i podejmować działania dotyczące inteligentnych umów.

1. W przeglądarce przejdź do `https://remix.ethereum.org`.
1. Wybierz pozycję **nowy plik** na karcie **Narzędzia główne** w obszarze **plik**.

    Nazwij nowy plik `simple.sol` .

    ![Utwórz plik](./media/connect-metamask/create-file.png)

    Wybierz przycisk **OK**.
1. W edytorze Remix wklej następujący **prosty kod kontraktu inteligentnego** .

    ```solidity
    pragma solidity ^0.5.0;
             
    contract simple {
        uint balance;
                 
        constructor() public{
            balance = 0;
        }
                 
        function add(uint _num) public {
            balance += _num;
        }
                 
        function get() public view returns (uint){
            return balance;
        }
    }
    ```

    **Prosta umowa** deklaruje zmienną stanu o nazwie **Saldo**. Zdefiniowano dwie funkcje. Funkcja **Dodaj** dodaje liczbę do **zrównoważenia**. Funkcja **Get** zwraca wartość **Saldo**.
1. Aby skompilować kontrakt, najpierw wybierz okienko kompilatora o pełnej trwałość, a następnie wybierz opcję  **Kompiluj Simple. peruwiański**.

    ![Przechwytywanie ekranu pokazuje kompilowany kontrakt.](./media/connect-metamask/compile.png)

1. Wybierz okienko **wdróż & przebiegu** , a następnie ustaw **środowisko** na **Web3** , aby nawiązać połączenie za pomocą elementu sqlmasking z członkiem łańcucha bloków.

    ![Karta przebieg](./media/connect-metamask/injected-web3.png)

1. Wybierz **prosty** kontrakt, a następnie **Wdróż**.

    ![Przechwytywanie ekranu pokazuje wdrażanie i uruchamianie transakcji z wybranym kontraktem i wdrażanie wybranych.](./media/connect-metamask/deploy.png)


1. Powiadomienie o niewystarczającej masce pozwala wykonać transakcję.

    W przypadku sieci publicznej łańcucha bloków należy ponieść Eter, aby płacić za koszt transakcji. Ponieważ jest to sieć prywatna w konsorcjum, można ustawić cenę gazu na zero.

1.  Wybierz pozycję **opłata za gaz > edytuj > zaawansowane**, ustaw **cenę gazu** na 0.

    ![Cena gazu](./media/connect-metamask/gas-price.png)

    Wybierz pozycję **Zapisz**.

1. Wybierz pozycję **Potwierdź** , aby wdrożyć kontrakt inteligentny do łańcucha bloków.
1. W sekcji **wdrożone kontrakty** rozwiń pozycję **prosty** kontrakt.

    ![Wdrożony kontrakt](./media/connect-metamask/deployed-contract.png)

    Dwie akcje, **Dodawanie** i **pobieranie**, mapowanie do funkcji zdefiniowanych w kontrakcie.

1. Aby wykonać operację **Dodaj** transakcję na łańcucha bloków, wprowadź liczbę, która ma zostać dodana, a następnie wybierz pozycję **Dodaj**. Może zostać wyświetlony komunikat o niepowodzeniu oceny gazu z remix: "wysyłasz transakcję do prywatnego łańcucha bloków, która nie wymaga gazu". Wybierz pozycję **Wyślij transakcję** , aby wymusić transakcję.
1. Podobnie jak w przypadku wdrożenia kontraktu, powiadomienie o niewystarczającej masce zostanie wykonane z powodu niewystarczających środków do wykonania transakcji.

    Ponieważ jest to sieć prywatna w ramach konsorcjum, można ustawić cenę gazu na zero.

1. Wybierz pozycję **opłata za gaz > edytuj > zaawansowane**, ustaw **cenę gazu** na 0, a następnie wybierz pozycję **Zapisz**.
1. Wybierz pozycję **Potwierdź** , aby wysłać transakcję do łańcucha bloków.
1. Wybierz pozycję **Pobierz** akcję. Jest to wywołanie do zapytania o dane węzła. Transakcja nie jest wymagana.

Okienko debugowanie Remix zawiera szczegółowe informacje o transakcjach w łańcucha bloków:

![Historia debugowania](./media/connect-metamask/debug.png)

Możesz zobaczyć **proste** tworzenie kontraktu, transakcję dla **prostego. Dodaj** i Wywołaj metodę **Simple. Get**.

Aby wyświetlić historię transakcji w ramach klasy dbmasking, Otwórz rozszerzenie przeglądarki z maską i zapoznaj się z sekcją **historia** dziennika wdrożonego kontraktu i transakcji.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start użyto rozszerzenia Browser masking do nawiązania połączenia z węzłem transakcji usługi Azure łańcucha bloków, wdrożenia kontraktu inteligentnego i wysłania transakcji do łańcucha bloków. Skorzystaj z następnego samouczka, aby użyć zestawu Azure łańcucha bloków Development Kit dla Ethereum i Truffle do tworzenia, kompilowania, wdrażania i wykonywania funkcji kontraktu inteligentnego za pośrednictwem transakcji.

> [!div class="nextstepaction"]
> [Twórz, Kompiluj i wdrażaj inteligentne kontrakty w usłudze Azure łańcucha bloków Service](send-transaction.md)