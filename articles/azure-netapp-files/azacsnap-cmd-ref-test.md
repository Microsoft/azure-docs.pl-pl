---
title: Testowanie narzędzia migawek spójnej na poziomie aplikacji platformy Azure dla Azure NetApp Files | Microsoft Docs
description: Wyjaśnia, jak uruchomić polecenie testowe narzędzia migawek spójnej na platformie Azure, których można użyć z Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 03ffa93a71ded40e033f2068ea23fc6b994a5bbb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97632819"
---
# <a name="test-azure-application-consistent-snapshot-tool-preview"></a>Testowanie narzędzia do tworzenia migawek spójnych aplikacji platformy Azure (wersja zapoznawcza)

W tym artykule wyjaśniono, jak uruchomić polecenie testowe narzędzia do tworzenia migawek spójnych w aplikacji Azure, których można użyć z Azure NetApp Files.

## <a name="introduction"></a>Wprowadzenie

Test konfiguracji jest wykonywany przy użyciu `azacsnap -c test` polecenia.

## <a name="command-options"></a>Opcje polecenia

`-c test`Polecenie ma następujące opcje:

- `--test hana`  testuje połączenie z wystąpieniem SAP HANA.

- `--test storage` testuje komunikację z podstawowym interfejsem magazynu, tworząc tymczasową migawkę magazynu na wszystkich skonfigurowanych `data` woluminach, a następnie usuwając je. 

- `--test all` wykonuje zarówno `hana` testy, jak i `storage` sekwencję.

- `[--configfile <config filename>]` jest opcjonalnym parametrem umożliwiającym stosowanie niestandardowych nazw plików konfiguracji.

## <a name="check-connectivity-with-sap-hana-azacsnap--c-test---test-hana"></a>Sprawdź łączność z SAP HANA `azacsnap -c test --test hana`

To polecenie sprawdza łączność platformy HANA dla wszystkich wystąpień HANA w pliku konfiguracji. Używa HDBuserstore do nawiązywania połączenia z SYSTEMDB i pobiera informacje o identyfikatorze SID.

W przypadku protokołu SSL to polecenie może przyjmować następujący opcjonalny argument:

- `--ssl=` wymusza zaszyfrowane połączenie z bazą danych i definiuje metodę szyfrowania używaną do komunikowania się z SAP HANA, albo `openssl` lub `commoncrypto` . Jeśli ta wartość jest zdefiniowana, to polecenie oczekuje na znalezienie dwóch plików w tym samym katalogu. te pliki muszą być nazwane po odpowiednim identyfikatorze SID. Zapoznaj się z artykułem [Używanie protokołu SSL w celu komunikacji z usługą SAP HANA](azacsnap-installation.md#using-ssl-for-communication-with-sap-hana).

### <a name="output-of-the-azacsnap--c-test---test-hana-command"></a>Dane wyjściowe `azacsnap -c test --test hana` polecenia

```bash
azacsnap -c test --test hana
```

```output
BEGIN : Test process started for 'hana'
BEGIN : SAP HANA tests
PASSED: Successful connectivity to HANA version 2.00.032.00.1533114046
END   : Test process complete for 'hana'
```

## <a name="check-connectivity-with-storage-azacsnap--c-test---test-storage"></a>Sprawdź łączność z magazynem `azacsnap -c test --test storage`

`azacsnap`Polecenie utworzy migawkę dla wszystkich woluminów danych skonfigurowanych do sprawdzenia, czy ma ona prawidłowy dostęp do woluminów dla każdego wystąpienia SAP HANA. Tworzona jest tymczasowa migawka, a następnie usunięta dla każdego woluminu danych, aby sprawdzić dostęp do migawek dla każdego systemu plików.

### <a name="output-of-the-azacsnap--c-test---test-storage-command"></a>Dane wyjściowe `azacsnap -c test --test storage` polecenia

```bash
azacsnap -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 2 task(s) to Test Snapshots for Storage Volume Type 'data'
PASSED: Task#2/2 Storage test successful for Volume
PASSED: Task#1/2 Storage test successful for Volume
END   : Storage tests complete
END   : Test process complete for 'storage'
```

> [!NOTE]
> W przypadku usługi Azure Large instance `azacsnap -c test --test storage` polecenie ekstrapolacje generacji magazynu i jednostki SKU.  Na podstawie tych informacji w tym artykule przedstawiono wskazówki dotyczące konfigurowania migawek "Boot" (Zobacz wiersz zaczynający się od `Action:` danych wyjściowych).

```output
SID1   : Generation 4
Storage: ams07-a700s-saphan-1-01v250-client25-nprod
HLI SKU: S96
Action : Configure the 'boot' snapshots on ALL the servers.
```

## <a name="next-steps"></a>Następne kroki

- [Tworzenie kopii zapasowej za pomocą narzędzia migawek spójnej na poziomie aplikacji platformy Azure](azacsnap-cmd-ref-backup.md)
