---
title: Wersje, poprawki, &, uaktualnienia w ramach usługi Azure łańcucha bloków
description: Omówienie obsługiwanych wersji księgi w usłudze Azure łańcucha bloków Service. Uwzględnienie zasad poprawek i uaktualnień systemów.
ms.date: 06/30/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: ea7c54e229178320329204a3199ab2b4c44058fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85807744"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Obsługiwane wersje finansów usługi Azure łańcucha bloków Service

Usługa Azure łańcucha bloków korzysta z opartej na Ethereum księgi [kworum](https://www.goquorum.com/developers) przeznaczonej do przetwarzania transakcji prywatnych w grupie znanych uczestników, identyfikowanej jako konsorcjum w usłudze Azure łańcucha bloków.

Obecnie usługa Azure łańcucha bloków obsługuje [kworum w wersji 2.6.0](https://github.com/jpmorganchase/quorum/releases/tag/v2.6.0) i [Tessera Transaction Manager](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Zarządzanie aktualizacjami i uaktualnieniami

Przechowywanie wersji w kworum odbywa się poprzez wersje główne, pomocnicze i poprawki. Na przykład jeśli wersja kworum to 2.0.1, typ wydania zostanie skategoryzowany w następujący sposób:

|Duży | Mały  | Patch  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Usługa Azure łańcucha bloków automatycznie aktualizuje wersje poprawki kworum do istniejących uruchomionych członków w ciągu 30 dni od udostępnienia kworum.

## <a name="availability-of-new-ledger-versions"></a>Dostępność nowych wersji księgi

Usługa Azure łańcucha bloków oferuje najnowsze i pomocnicze wersje księgi kworum w ciągu 60 dni od producenta kworum. Dla konsorcjów, które mogą wybierać z usług w przypadku aprowizacji nowych członków i konsorcjum, udostępniane są maksymalnie cztery pomocnicze wersje. Uaktualnianie z programu do wersji głównej lub pomocniczej nie jest obecnie obsługiwane. Na przykład jeśli korzystasz z wersji 2. x, uaktualnienie do wersji 3. x nie jest obecnie obsługiwane. Podobnie, jeśli korzystasz z wersji 2,2, uaktualnienie do wersji 2,3 nie jest obecnie obsługiwane.

## <a name="how-to-check-quorum-ledger-version"></a>Sprawdzanie wersji księgi kworum

Możesz sprawdzić wersję kworum w składowej usługi Azure łańcucha bloków, dołączając do węzła za pomocą geth lub wyświetlając dzienniki diagnostyczne.

### <a name="using-geth"></a>Korzystanie z geth

Dołącz do węzła usługi Azure łańcucha bloków za pomocą geth. Na przykład `geth attach https://myblockchainmember.blockchain.azure.com:3200/<Access key>`.

Po nawiązaniu połączenia z węzłem program geth zgłosi wersję kworum podobną do następującej:

``` text
instance: Geth/v1.9.7-stable-9339be03(quorum-v2.6.0)/linux-amd64/go1.13.12
```

Aby uzyskać więcej informacji na temat korzystania z programu geth, zobacz [Szybki Start: korzystanie z geth do dołączania do węzła transakcji usługi Azure łańcucha bloków](connect-geth.md).

### <a name="using-diagnostic-logs"></a>Korzystanie z dzienników diagnostycznych

W przypadku włączenia dzienników diagnostycznych wersja kworum jest raportowana dla węzłów transakcji. Na przykład poniższy węzeł zawiera informacje o wersji kworum.

``` text 
{"NodeName":"transaction-node","Message":"INFO [06-22|05:31:45.156] Starting peer-to-peer node instance=Geth/v1.9.7-stable-9339be03(quorum-v2.6.0)/linux-amd64/go1.13.12\n"}
{"NodeName":"transaction-node","Message":"[*] Starting Quorum node with QUORUM_VERSION=2.6.0, TESSERA_VERSION=0.10.5 and PRIVATE_CONFIG=/working-dir/c/tm.ipc\n"}
111
```

Aby uzyskać więcej informacji na temat dzienników diagnostycznych, zobacz [monitorowanie usługi Azure łańcucha bloków za pomocą Azure monitor](monitor-azure-blockchain-service.md#diagnostic-settings).

## <a name="how-to-check-genesis-file-content"></a>Jak sprawdzić zawartość pliku Genesis

Aby sprawdzić zawartość pliku Genesis w węźle łańcucha bloków, można użyć następującego interfejsu API języka JavaScript w języku Ethereum:

``` bash
admin.nodeInfo.protocols
```
Interfejs API można wywołać za pomocą konsoli geth lub biblioteki web3. Aby uzyskać więcej informacji na temat korzystania z programu geth, zobacz [Szybki Start: korzystanie z geth do dołączania do węzła transakcji usługi Azure łańcucha bloków](connect-geth.md).

## <a name="next-steps"></a>Następne kroki

[Limity w usłudze Azure łańcucha bloków Service](limits.md)
