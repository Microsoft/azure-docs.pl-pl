---
title: Omówienie tworzenia usług łańcucha bloków platformy Azure
description: Wprowadzenie do tworzenia rozwiązań w usłudze Azure łańcucha bloków Service.
ms.date: 03/26/2020
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 4a98e5ea025894303bc5c77bba0c6154a08315f2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "80348377"
---
# <a name="azure-blockchain-service-development-overview"></a>Omówienie tworzenia usług łańcucha bloków platformy Azure

Za pomocą usługi Azure łańcucha bloków można tworzyć sieci łańcucha bloków konsorcjum, aby umożliwić korzystanie z scenariuszy korporacyjnych, takich jak śledzenie zasobów, token cyfrowy, lojalność, wynagrodzenie i pochodzenie łańcucha dostaw. Poniższe sekcje zawierają wprowadzenie do programowania usługi Azure łańcucha bloków w celu wdrożenia rozwiązań Enterprise łańcucha bloków.

## <a name="connecting-to-azure-blockchain-service"></a>Nawiązywanie połączenia z usługą Azure łańcucha bloków

Istnieją różne typy klientów dla sieci łańcucha bloków, w tym pełne węzły, węzły lekkie i klienci zdalni. Usługa Azure łańcucha bloków tworzy sieć łańcucha bloków, która zawiera węzły. Możesz użyć różnych klientów jako bramy do usługi Azure łańcucha bloków Service do tworzenia łańcucha bloków. Usługa Azure łańcucha bloków oferuje podstawowe uwierzytelnianie lub klucz dostępu jako punkt końcowy projektowania. Poniżej znajdują się popularne Klienci, których można użyć do nawiązania połączenia.

### <a name="visual-studio-code"></a>Visual Studio Code

Można nawiązać połączenie z członkami konsorcjum przy użyciu rozszerzenia Azure łańcucha bloków Development Visual Studio Code Kit. Po nawiązaniu połączenia z konsorcjum można skompilować, skompilować i wdrożyć inteligentne kontrakty w składowej konsorcjum usługi Azure łańcucha bloków.

Aby opracowywać zaawansowane rozwiązania dla przedsiębiorstw łańcucha bloków, platforma programistyczna jest niezbędna do łączenia się z różnymi sieciami łańcucha bloków i zarządzania cyklami życia kontraktu inteligentnego. Większość projektów współdziała z co najmniej dwoma węzłami łańcucha bloków. Deweloperzy używają łańcucha bloków lokalnego podczas opracowywania. Gdy aplikacja jest gotowa do testowania lub wydania, deweloper wdraża ją w sieci łańcucha bloków. Na przykład główna publiczna sieć Ethereum lub usługa Azure łańcucha bloków. Rozszerzenie Azure łańcucha bloków Development Kit for Ethereum w Visual Studio Code używa Truffle. Truffle to popularna platforma programistyczna łańcucha bloków do pisania, kompilowania, wdrażania i testowania zdecentralizowanych aplikacji na Ethereum blockchains. Możesz również traktować Truffle jako platformę, która próbuje bezproblemowo zintegrować inteligentne projektowanie kontraktu i tradycyjne programowanie w sieci Web.

Aby uzyskać więcej informacji, zobacz [Szybki Start: użyj Visual Studio Code, aby nawiązać połączenie z siecią konsorcjum usługi Azure łańcucha bloków](connect-vscode.md).

### <a name="metamask"></a>Maska

Funkcja maskowania to oparty na przeglądarce portfel (Klient zdalny), klient RPC i podstawowy Eksplorator umów. W przeciwieństwie do innych portfeli przeglądarki, można wstrzyknąć wystąpienie web3 do kontekstu JavaScript przeglądarki, działającego jako klient RPC, który łączy się z wieloma Ethereum blockchains (*mainnet*, *Ropsten testnet*, *KOVAN testnet*, lokalny węzeł RPC itp.). Możesz łatwo skonfigurować niestandardowe zdalne wywoływanie procedur, aby nawiązać połączenie z usługą Azure łańcucha bloków i rozpocząć programowanie łańcucha bloków przy użyciu Remix.

Aby uzyskać więcej informacji, zobacz [Szybki Start: korzystanie z usługi dbmask do łączenia i wdrażania kontraktu inteligentnego](connect-metamask.md)

### <a name="geth"></a>Geth

Geth jest interfejs wiersza polecenia do uruchamiania pełnego węzła Ethereum zaimplementowanego w programie go. Nie musisz uruchamiać pełnego węzła, ale można uruchomić jego interaktywną konsolę, która zapewnia środowisko uruchomieniowe języka JavaScript, które udostępnia interfejs API języka JavaScript do interakcji z usługą Azure łańcucha bloków.

Aby uzyskać więcej informacji, zobacz [Szybki Start: korzystanie z Geth w celu dołączenia do węzła transakcji usługi Azure łańcucha bloków](connect-geth.md).

## <a name="ethereum-quorum-private-transactions"></a>Ethereum transakcji prywatnych kworum

Kworum to oparty na systemie Ethereum protokół księgi rozproszonej z transakcją oraz prywatnością umowy i nowymi konsensusami. Najważniejsze ulepszenia dotyczące Go-Ethereum obejmują:

* **Prywatność** — kworum obsługuje transakcje prywatne i prywatne w ramach separacji Stanów publicznych i prywatnych oraz wykorzystuje wymiany komunikatów typu peer-to-peer do bezpośredniego transferu danych prywatnych do uczestników sieci.
* **Alternatywne metody z Konsensusem** — weryfikacja typu "nie jest wymagana w przypadku sieci z uprawnieniami". Kworum oferuje wiele mechanizmów o consensusu, które są przeznaczone dla łańcuchów konsorcjum, takich jak TRATWy i IBFT.  Usługa Azure łańcucha bloków używa mechanizmu IBFT o konsensusie.
* **Uprawnienie elementu równorzędnego** — uprawnienia węzła i elementu równorzędnego za pomocą inteligentnych kontraktów zapewniają, że tylko znane strony mogą dołączyć do sieci.
* **Wyższa wydajność** — kworum zapewnia wyższą wydajność niż publiczne Geth.

## <a name="block-explorers"></a>Eksplorator bloków

Eksploratory bloków są w trybie online łańcucha bloków przeglądarki, które wyświetlają poszczególne zawartość bloku, dane adresów transakcji i historię. Podstawowe informacje o bloku są dostępne za pomocą Azure Monitor w usłudze Azure łańcucha bloków Service. Jeśli jednak potrzebujesz bardziej szczegółowych informacji podczas programowania, Eksplorator bloków może być przydatny.  Następujące Eksploratory blokowania współpracują z usługą Azure łańcucha bloków:

* [Epirus Azure łańcucha bloków Service Explorer](https://azuremarketplace.microsoft.com/marketplace/apps/blk-technologies.azure-blockchain-explorer-template?tab=Overview) z programu Web3 Labs
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

Możesz również utworzyć własny Eksplorator bloków przy użyciu łańcucha bloków Data Manager i Azure Cosmos DB, zobacz [Samouczek: używanie łańcucha bloków Data Manager do wysyłania danych do Azure Cosmos DB](data-manager-cosmosdb.md).

## <a name="tps-measurement"></a>Pomiar TPS

Ponieważ łańcucha bloków jest używany w większej liczbie scenariuszy dla przedsiębiorstw, ważne jest, aby uniknąć wąskich gardeł i niewydajności systemu. Wysokie stawki transakcji mogą być trudne do utrzymania w zdecentralizowanej łańcucha bloków. Do dokładnej miary TPS mogą mieć wpływ różne czynniki, takie jak wątek serwera, rozmiar kolejki transakcji, opóźnienie sieci i zabezpieczenia. Jeśli potrzebujesz mierzyć szybkość TPS podczas opracowywania, popularne narzędzie typu Open Source to [ChainHammer](https://github.com/drandreaskrueger/chainhammer).

## <a name="next-steps"></a>Następne kroki

Skorzystaj z przewodnika Szybki Start przy użyciu zestawu Azure łańcucha bloków Development Kit, aby dołączyć do konsorcjum w usłudze Azure łańcucha bloków.

> [!div class="nextstepaction"]
> [Użyj Visual Studio Code, aby nawiązać połączenie z usługą Azure łańcucha bloków](connect-vscode.md)