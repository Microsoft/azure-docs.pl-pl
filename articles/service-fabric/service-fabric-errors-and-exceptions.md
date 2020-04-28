---
title: Zgłoszono typowe wyjątki FabricClient
description: Opisuje typowe wyjątki i błędy, które mogą być zgłaszane przez interfejsy API FabricClient podczas wykonywania operacji zarządzania aplikacjami i klastrami.
author: oanapl
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: oanapl
ms.openlocfilehash: 9ad3097a490d4728e05ea90652c17c24b79cac2c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75457933"
---
# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>Typowe wyjątki i błędy podczas pracy z interfejsami API FabricClient
Interfejsy API [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) umożliwiają administratorom klastrów i aplikacji wykonywanie zadań administracyjnych na Service Fabric aplikacji, usługi lub klastra. Na przykład wdrażanie, uaktualnianie i usuwanie aplikacji, sprawdzanie kondycji klastra lub Testowanie usługi. Deweloperzy aplikacji i Administratorzy klastrów mogą używać interfejsów API FabricClient, aby opracowywać narzędzia do zarządzania klastrem Service Fabric i aplikacjami.

Istnieje wiele różnych typów operacji, które można wykonać za pomocą FabricClient.  Każda metoda może generować wyjątki dla błędów z powodu nieprawidłowych danych wejściowych, błędów środowiska uruchomieniowego lub przejściowych problemów z infrastrukturą.  Zapoznaj się z dokumentacją interfejsu API, aby sprawdzić, które wyjątki są zgłaszane przez określoną metodę. Istnieją jednak pewne wyjątki, które mogą być zgłaszane przez wiele różnych interfejsów API [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) . Poniższa tabela zawiera listę wyjątków wspólnych dla interfejsów API FabricClient.

| Wyjątek | Zgłaszane, gdy |
| --- |:--- |
| [System. Fabric. FabricObjectClosedException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricobjectclosedexception) |Obiekt [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) jest w stanie zamkniętym. Usuń obiekt [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) , którego używasz, i Utwórz wystąpienie nowego obiektu [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) . |
| [System. TimeoutException](https://docs.microsoft.com/dotnet/core/api/system.timeoutexception) |Przekroczono limit czasu operacji. [OperationTimedOut](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) jest zwracany, gdy operacja zajmuje więcej niż MaxOperationTimeout. |
| [System. UnauthorizedAccessException](https://docs.microsoft.com/dotnet/core/api/system.unauthorizedaccessexception) |Sprawdzanie dostępu dla operacji nie powiodło się. E_ACCESSDENIED jest zwracana. |
| [System. Fabric. Fabricexception](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) |Wystąpił błąd środowiska uruchomieniowego podczas wykonywania operacji. Dowolna metoda FabricClient może potencjalnie zgłosić wyjątek [sieci szkieletowej](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception), a właściwość [ErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception.ErrorCode) wskazuje dokładną przyczynę wyjątku. Kody błędów są zdefiniowane w wyliczeniu [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) . |
| [System. Fabric. FabricTransientException](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception) |Operacja nie powiodła się z powodu warunku błędu przejściowego pewnego rodzaju. Na przykład operacja może zakończyć się niepowodzeniem, ponieważ kworum replik jest tymczasowo nieosiągalne. Przejściowe wyjątki odpowiadają operacji zakończonych niepowodzeniem, które można ponowić. |

Niektóre typowe błędy [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) , które mogą zostać zwrócone w sieci [szkieletowej](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception):

| Error | Warunek |
| --- |:--- |
| CommunicationError |Błąd komunikacji spowodował niepowodzenie operacji, spróbuj ponownie wykonać operację. |
| InvalidCredentialType |Typ poświadczeń jest nieprawidłowy. |
| InvalidX509FindType |X509FindType jest nieprawidłowy. |
| InvalidX509StoreLocation |Lokalizacja magazynu x509 jest nieprawidłowa. |
| InvalidX509StoreName |Nazwa magazynu x509 jest nieprawidłowa. |
| InvalidX509Thumbprint |Ciąg odcisku palca certyfikatu x509 jest nieprawidłowy. |
| InvalidProtectionLevel |Poziom ochrony jest nieprawidłowy. |
| InvalidX509Store |Nie można otworzyć magazynu certyfikatów x509. |
| InvalidSubjectName |Nazwa podmiotu jest nieprawidłowa. |
| InvalidAllowedCommonNameList |Format ciągu listy typowych nazw jest nieprawidłowy. Powinna to być lista rozdzielona przecinkami. |

