---
title: dołączanie pliku
description: dołączanie pliku
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 06/15/2020
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 52569f3cec26432970606b31fe831bb6459839d6
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2020
ms.locfileid: "88011010"
---
Jeśli napotkasz problemy podczas wykonywania jakichkolwiek operacji na galeriach obrazów udostępnionych, definicjach obrazów i wersjach obrazów, uruchom polecenie powodujące niepowodzenie ponownie w trybie debugowania. Tryb debugowania jest aktywowany przez przekazanie `--debug` przełącznika przy użyciu interfejsu wiersza polecenia i `-Debug` przełącznika przy użyciu programu PowerShell. Po zlokalizowaniu błędu postępuj zgodnie z tym dokumentem, aby rozwiązać problemy.


## <a name="unable-to-create-a-shared-image-gallery"></a>Nie można utworzyć galerii obrazów udostępnionych

Możliwe przyczyny:

*Nazwa galerii jest nieprawidłowa.*

Dozwolone znaki w nazwie galerii to wielkie lub małe litery, cyfry, kropki i kropki. Nazwa galerii nie może zawierać kresek. Zmień nazwę galerii i spróbuj ponownie. 

*Nazwa galerii nie jest unikatowa w ramach subskrypcji.*

Wybierz inną nazwę galerii i spróbuj ponownie.


## <a name="unable-to-create-an-image-definition"></a>Nie można utworzyć definicji obrazu 

Możliwe przyczyny:

*Nazwa definicji obrazu jest nieprawidłowa.*

Dozwolone znaki dla definicji obrazu to wielkie lub małe litery, cyfry, kropki, łączniki i kropki. Zmień nazwę definicji obrazu i spróbuj ponownie.

*Właściwości obowiązkowe do tworzenia definicji obrazu nie są wypełnione.*

Właściwości, takie jak nazwa, Wydawca, oferta, jednostka SKU i typ systemu operacyjnego, są obowiązkowe. Sprawdź, czy wszystkie właściwości są przesyłane.

Upewnij się, że definicja obrazu **OSType**, Linux lub Windows jest taka sama jak źródło używane do tworzenia wersji obrazu. 


## <a name="unable-to-create-an-image-version"></a>Nie można utworzyć wersji obrazu 

Możliwe przyczyny:

*Nazwa wersji obrazu jest nieprawidłowa.*

Dozwolone znaki wersji obrazu to liczby i kropki. Liczba musi należeć do zakresu 32-bitowej liczby całkowitej. Format: *MajorVersion. MinorVersion. patch*. Zmień nazwę wersji obrazu i spróbuj ponownie.

*Nie znaleziono źródłowego obrazu zarządzanego, z którego jest tworzona wersja obrazu.* 

Sprawdź, czy obraz źródłowy istnieje i znajduje się w tym samym regionie co wersja obrazu.

*Nie zainicjowano obsługi obrazu zarządzanego.*

Upewnij się, że stan aprowizacji obrazu zarządzanego jest **zakończony pomyślnie**.

*Lista regionów docelowych nie obejmuje regionu źródłowego.*

Lista regionów docelowych musi zawierać region źródłowy wersji obrazu. Upewnij się, że na liście regionów docelowych została uwzględniona lokalizacja źródłowa, w której ma zostać zreplikowana wersja obrazu.

*Replikacja do wszystkich regionów docelowych nie została ukończona.*

Użyj flagi **--expand ReplicationStatus** , aby sprawdzić, czy replikacja do wszystkich określonych regionów docelowych została ukończona. W przeciwnym razie poczekaj do 6 godzin, aż zadanie zostanie ukończone. Jeśli to się nie powiedzie, ponownie uruchom polecenie, aby utworzyć i replikować wersję obrazu. Jeśli istnieje wiele regionów docelowych, do których jest replikowana wersja obrazu, należy wziąć pod uwagę replikację w fazach.

## <a name="unable-to-create-a-vm-or-a-scale-set"></a>Nie można utworzyć maszyny wirtualnej lub zestawu skalowania 

Możliwe przyczyny:

*Użytkownik próbujący utworzyć maszynę wirtualną lub zestaw skalowania maszyn wirtualnych nie ma dostępu do odczytu do wersji obrazu.*

Skontaktuj się z właścicielem subskrypcji i poproś o przyznanie dostępu do odczytu do wersji obrazu lub zasobów nadrzędnych (takich jak Galeria obrazów udostępnionych lub definicja obrazu) za pośrednictwem [kontroli dostępu opartej na rolach (Azure RBAC)](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles). 

*Nie znaleziono wersji obrazu.*

Sprawdź, czy region, w którym próbujesz utworzyć maszynę wirtualną lub skalowanie maszyn wirtualnych, znajduje się na liście regionów docelowych wersji obrazu. Jeśli region znajduje się już na liście regionów docelowych, sprawdź, czy zadanie replikacji zostało ukończone. Możesz użyć flagi **-ReplicationStatus** , aby sprawdzić, czy replikacja do wszystkich określonych regionów docelowych została ukończona. 

*Tworzenie zestawu skalowania maszyn wirtualnych lub maszyny wirtualnej zajmuje dużo czasu.*

Sprawdź, czy **OSType** wersji obrazu, z której próbujesz utworzyć maszynę wirtualną lub zestaw skalowania maszyn wirtualnych, ma ten sam **OSType** źródła, którego użyto do utworzenia wersji obrazu. 

## <a name="unable-to-share-resources"></a>Nie można udostępnić zasobów

Funkcja [kontroli dostępu opartej na rolach (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)na platformie Azure umożliwia udostępnianie galerii obrazów udostępnionych, definicji obrazu oraz zasobów wersji obrazu w różnych subskrypcjach. 

## <a name="replication-is-slow"></a>Replikacja jest powolna

Użyj flagi **--expand ReplicationStatus** , aby sprawdzić, czy replikacja do wszystkich określonych regionów docelowych została ukończona. Jeśli nie, poczekaj na ukończenie zadania do 6 godzin. Jeśli to się nie powiedzie, wyzwól polecenie ponownie, aby utworzyć i replikować wersję obrazu. Jeśli istnieje wiele regionów docelowych, do których jest replikowana wersja obrazu, należy wziąć pod uwagę replikację w fazach.

## <a name="azure-limits-and-quotas"></a>Limity przydziału i ograniczenia platformy Azure 

[Ograniczenia i limity platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) mają zastosowanie do całej galerii obrazów udostępnionych, definicji obrazu i zasobów wersji obrazu. Upewnij się, że jesteś w ramach limitów subskrypcji. 
