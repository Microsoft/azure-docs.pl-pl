---
title: Błędy konsoli szeregowej platformy Azure | Microsoft Docs
description: Typowe błędy w konsoli szeregowej platformy Azure
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: cad12a55332a6c7898f9709776c58d7dba8dd81a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86526439"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Typowe błędy w konsoli szeregowej platformy Azure
Istnieje zestaw znanych błędów w konsoli szeregowej platformy Azure. Jest to lista błędów i czynności zaradczych.

## <a name="common-errors"></a>Typowe błędy

Błąd                             |   Ograniczanie ryzyka
:---------------------------------|:--------------------------------------------|
"Konsola szeregowa Azure wymaga włączenia diagnostyki rozruchu. Kliknij tutaj, aby skonfigurować diagnostykę rozruchu dla maszyny wirtualnej ". | Upewnij się, że maszyna wirtualna lub zestaw skalowania maszyn wirtualnych ma włączoną [diagnostykę rozruchu](boot-diagnostics.md) . Jeśli używasz konsoli szeregowej w wystąpieniu zestawu skalowania maszyn wirtualnych, upewnij się, że wystąpienie ma najnowszy model.
"Konsola szeregowa platformy Azure wymaga, aby maszyna wirtualna była uruchomiona. Użyj przycisku Start powyżej, aby uruchomić maszynę wirtualną ".  | Wystąpienie maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych musi znajdować się w stanie uruchomienia, aby można było uzyskać dostęp do konsoli szeregowej (nie można zatrzymać ani cofnąć przydziału maszyny wirtualnej). Upewnij się, że maszyna wirtualna lub wystąpienie zestawu skalowania maszyn wirtualnych jest uruchomione, i spróbuj ponownie.
"Usługa Azure serial Console nie jest włączona dla tej subskrypcji, skontaktuj się z administratorem subskrypcji, aby ją włączyć". | Konsolę szeregową platformy Azure można wyłączyć na poziomie subskrypcji. Jeśli jesteś administratorem subskrypcji, możesz [włączyć i wyłączyć konsolę seryjną platformy Azure](./serial-console-enable-disable.md). Jeśli nie jesteś administratorem subskrypcji, musisz skontaktować się z administratorem subskrypcji w celu uzyskania dalszych kroków.
Napotkano odpowiedź "zabronione" podczas uzyskiwania dostępu do konta magazynu diagnostyki rozruchu maszyny wirtualnej. | Upewnij się, że Diagnostyka rozruchu nie ma zapory konta. Aby konsola szeregowa mogła działać, wymagane jest konto magazynu diagnostyki rozruchu. Konsola szeregowa według projektu nie może współpracować z zaporami konta magazynu włączonymi na koncie magazynu diagnostyki rozruchu.
Nie masz wymaganych uprawnień do korzystania z tej maszyny wirtualnej za pomocą konsoli szeregowej. Upewnij się, że masz co najmniej uprawnienia roli współautor maszyny wirtualnej.| Dostęp do konsoli szeregowej wymaga, aby na maszynie wirtualnej lub w zestawie skalowania maszyn wirtualnych miał dostęp na poziomie współautora lub wyższy. Aby uzyskać więcej informacji, zobacz [stronę przegląd](serial-console-overview.md).
Nie można odnaleźć konta magazynu "" używanego na potrzeby diagnostyki rozruchu na tej maszynie wirtualnej. Sprawdź, czy dla tej maszyny wirtualnej włączono diagnostykę rozruchu, to konto magazynu nie zostało usunięte i masz dostęp do tego konta magazynu. | Sprawdź, czy nie usunięto konta magazynu diagnostyki rozruchu dla maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych
Połączenie konsoli szeregowej z maszyną wirtualną napotkało błąd: "złe żądanie" (400) | Taka sytuacja może wystąpić, jeśli identyfikator URI diagnostyki rozruchu jest nieprawidłowy. Na przykład zamiast "https://" użyto elementu "http://". Identyfikator URI diagnostyki rozruchu można naprawić za pomocą tego polecenia: `az vm boot-diagnostics enable --name vmName --resource-group rgName --storage https://<storageAccountUri>.blob.core.windows.net/`
Nie masz wymaganych uprawnień do zapisu na koncie magazynu diagnostyki rozruchu dla tej maszyny wirtualnej. Upewnij się, że masz co najmniej uprawnienia współautora maszyny wirtualnej | Konsola szeregowa dostęp wymaga dostępu na poziomie współautor na koncie magazynu diagnostyki rozruchu. Aby uzyskać więcej informacji, zobacz [stronę przegląd](serial-console-overview.md).
Nie można określić grupy zasobów dla konta magazynu diagnostyki rozruchu * &lt; STORAGEACCOUNTNAME &gt; *. Sprawdź, czy Diagnostyka rozruchu jest włączona dla tej maszyny wirtualnej i czy masz dostęp do tego konta magazynu. | Konsola szeregowa dostęp wymaga dostępu na poziomie współautor na koncie magazynu diagnostyki rozruchu. Aby uzyskać więcej informacji, zobacz [stronę przegląd](serial-console-overview.md).
Inicjowanie obsługi dla tej maszyny wirtualnej nie powiodło się. Upewnij się, że maszyna wirtualna jest w pełni wdrożona, i ponów próbę połączenia z konsolą szeregową. | Obsługa maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych nadal może być niedostępna. Poczekaj chwilę i spróbuj ponownie.
Gniazdo sieci Web jest zamknięte lub nie można go otworzyć. | Może być konieczne dodanie dostępu do zapory `*.console.azure.com` . Bardziej szczegółowym, ale dłuższym podejściem jest umożliwienie dostępu zapory do [zakresów adresów IP centrum danych Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653), które zmieniają się dość często.
Konsola szeregowa nie działa z kontem magazynu przy użyciu Azure Data Lake Storage Gen2 z hierarchicznymi przestrzeniami nazw. | Jest to znany problem z hierarchicznymi przestrzeniami nazw. Aby rozwiązać problem, upewnij się, że konto magazynu diagnostyki rozruchu maszyny wirtualnej nie zostało utworzone przy użyciu Azure Data Lake Storage Gen2. Tę opcję można ustawić tylko podczas tworzenia konta magazynu. Może być konieczne utworzenie oddzielnego konta magazynu diagnostyki rozruchu bez Azure Data Lake Storage Gen2 włączenia tego problemu.
Wystąpił błąd połączenia z konsolą szeregową z maszyną wirtualną: "zabronione" (SubscriptionNotEnabled) — niezdefiniowana nazwa subskrypcji, identyfikator \<subscription id> jest w nieobsługiwanym stanie | Ten problem może wystąpić, jeśli subskrypcja, w ramach której utworzono konto magazynu Cloud Shell, została wyłączona. Aby rozwiązać problem, uruchom Cloud Shell i [wykonaj kroki niezbędne](../../cloud-shell/persisting-shell-storage.md#unmount-clouddrive-1) do ponownego udostępnienia zapasowego konta magazynu dla Cloud Shell w bieżącej subskrypcji.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [usłudze Azure serial Console dla maszyn wirtualnych z systemem Linux](./serial-console-linux.md)
* Dowiedz się więcej o [usłudze Azure serial Console dla maszyn wirtualnych z systemem Windows](./serial-console-windows.md)
