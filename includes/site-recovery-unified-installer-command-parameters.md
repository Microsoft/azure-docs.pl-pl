---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 8076a6af2382ccec1ac832cd83c3946d8c7c6f57
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91401096"
---
|Nazwa parametru| Type | Opis| Możliwe wartości|
|-|-|-|-|
| /ServerMode|Obowiązkowy|Określa, czy mają zostać zainstalowane oba serwery, konfiguracji i przetwarzania, czy tylko serwer przetwarzania|CS<br>PS|
|/InstallLocation|Obowiązkowy|Folder, w którym są instalowane składniki| Dowolny folder na komputerze|
|/MySQLCredsFilePath|Obowiązkowy|Ścieżka pliku, w której są przechowywane poświadczenia serwera MySQL|Plik powinien mieć format określony poniżej|
|/VaultCredsFilePath|Obowiązkowy|Ścieżka pliku poświadczeń magazynu|Prawidłowa ścieżka pliku|
|/EnvType|Obowiązkowy|Typ środowiska, który ma być chroniony |VMware<br>NonVMware|
|/PSIP|Obowiązkowy|Adres IP karty sieciowej do użytku podczas przesyłania danych replikacji| Dowolny prawidłowy adres IP|
|/CSIP|Obowiązkowy|Adres IP karty sieciowej, na której nasłuchuje serwer konfiguracji| Dowolny prawidłowy adres IP|
|/PassphraseFilePath|Obowiązkowy|Pełna ścieżka do lokalizacji pliku hasła|Prawidłowa ścieżka pliku|
|/BypassProxy|Opcjonalne|Określa, że serwer konfiguracji łączy się z platformą Azure bez serwera proxy||
|/ProxySettingsFilePath|Opcjonalne|Ustawienia serwera proxy (domyślny serwer proxy wymaga uwierzytelniania lub niestandardowy serwer proxy)|Plik powinien mieć format określony poniżej|
|DataTransferSecurePort|Opcjonalne|Numer portu dla protokołu PSIP do użytku z danymi replikacji| Prawidłowy numer portu (wartość domyślna to 9433)|
|/SkipSpaceCheck|Opcjonalne|Pomiń sprawdzanie miejsca dla dysku pamięci podręcznej| |
|/AcceptThirdpartyEULA|Obowiązkowy|Flaga implikuje akceptację umowy licencyjnej innego producenta| |
|/ShowThirdpartyEULA|Opcjonalne|Wyświetla umowę licencyjną innej firmy. Jeśli zostanie podany w danych wejściowych, wszystkie inne parametry są ignorowane| |
