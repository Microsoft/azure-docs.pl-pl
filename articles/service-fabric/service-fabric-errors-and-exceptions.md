---
title: Zgłoszono typowe wyjątki FabricClient
description: Opisuje typowe wyjątki i błędy, które mogą być zgłaszane przez interfejsy API FabricClient podczas wykonywania operacji zarządzania aplikacjami i klastrami.
author: georgewallace
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: gwallace
ms.openlocfilehash: 7f3c3e072a3a2e4f7723f84b2c70ba0d0ddb9d03
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86258839"
---
# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>Typowe wyjątki i błędy podczas pracy z interfejsami API FabricClient
Interfejsy API [FabricClient](/dotnet/api/system.fabric.fabricclient) umożliwiają administratorom klastrów i aplikacji wykonywanie zadań administracyjnych na Service Fabric aplikacji, usługi lub klastra. Na przykład wdrażanie, uaktualnianie i usuwanie aplikacji, sprawdzanie kondycji klastra lub Testowanie usługi. Deweloperzy aplikacji i Administratorzy klastrów mogą używać interfejsów API FabricClient, aby opracowywać narzędzia do zarządzania klastrem Service Fabric i aplikacjami.

Istnieje wiele różnych typów operacji, które można wykonać za pomocą FabricClient.  Każda metoda może generować wyjątki dla błędów z powodu nieprawidłowych danych wejściowych, błędów środowiska uruchomieniowego lub przejściowych problemów z infrastrukturą.  Zapoznaj się z dokumentacją interfejsu API, aby sprawdzić, które wyjątki są zgłaszane przez określoną metodę. Istnieją jednak pewne wyjątki, które mogą być zgłaszane przez wiele różnych interfejsów API [FabricClient](/dotnet/api/system.fabric.fabricclient) . Poniższa tabela zawiera listę wyjątków wspólnych dla interfejsów API FabricClient.

| Wyjątek | Zgłaszane, gdy |
| --- |:--- |
| [System. Fabric. FabricObjectClosedException](/dotnet/api/system.fabric.fabricobjectclosedexception) |Obiekt [FabricClient](/dotnet/api/system.fabric.fabricclient) jest w stanie zamkniętym. Usuń obiekt [FabricClient](/dotnet/api/system.fabric.fabricclient) , którego używasz, i Utwórz wystąpienie nowego obiektu [FabricClient](/dotnet/api/system.fabric.fabricclient) . |
| [System. TimeoutException](/dotnet/core/api/system.timeoutexception) |Przekroczono limit czasu operacji. [OperationTimedOut](/dotnet/api/system.fabric.fabricerrorcode) jest zwracany, gdy operacja zajmuje więcej niż MaxOperationTimeout. |
| [System. UnauthorizedAccessException](/dotnet/core/api/system.unauthorizedaccessexception) |Sprawdzanie dostępu dla operacji nie powiodło się. E_ACCESSDENIED jest zwracana. |
| [System. Fabric. Fabricexception](/dotnet/api/system.fabric.fabricexception) |Wystąpił błąd środowiska uruchomieniowego podczas wykonywania operacji. Dowolna metoda FabricClient może potencjalnie zgłosić wyjątek [sieci szkieletowej](/dotnet/api/system.fabric.fabricexception), a właściwość [ErrorCode](/dotnet/api/system.fabric.fabricexception.errorcode) wskazuje dokładną przyczynę wyjątku. Kody błędów są zdefiniowane w wyliczeniu [FabricErrorCode](/dotnet/api/system.fabric.fabricerrorcode) . |
| [System. Fabric. FabricTransientException](/dotnet/api/system.fabric.fabrictransientexception) |Operacja nie powiodła się z powodu warunku błędu przejściowego pewnego rodzaju. Na przykład operacja może zakończyć się niepowodzeniem, ponieważ kworum replik jest tymczasowo nieosiągalne. Przejściowe wyjątki odpowiadają operacji zakończonych niepowodzeniem, które można ponowić. |

Niektóre typowe błędy [FabricErrorCode](/dotnet/api/system.fabric.fabricerrorcode) , które mogą zostać zwrócone w sieci [szkieletowej](/dotnet/api/system.fabric.fabricexception):

| Błąd | Warunek |
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
