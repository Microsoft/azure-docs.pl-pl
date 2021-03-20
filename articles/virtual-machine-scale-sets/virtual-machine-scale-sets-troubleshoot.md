---
title: Rozwiązywanie problemów z automatycznym skalowaniem za pomocą Virtual Machine Scale Sets
description: Rozwiązywanie problemów z automatycznym skalowaniem za pomocą Virtual Machine Scale Sets. Poznaj typowe problemy napotkane i sposoby ich rozwiązywania.
author: avirishuv
ms.author: avverma
ms.topic: troubleshooting
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 06/25/2020
ms.reviwer: jushiman
ms.custom: avverma
ms.openlocfilehash: 11302c301bee466f678d544d0c4838c39cec9c8e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91818535"
---
# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>Troubleshooting autoscale with Virtual Machine Scale Sets (Rozwiązywanie problemów ze skalowaniem automatycznym przy użyciu zestawów skalowania maszyn wirtualnych)
**Problem** — utworzono infrastrukturę skalowania automatycznego w Azure Resource Manager przy użyciu zestawów skalowania maszyn wirtualnych — na przykład przez wdrożenie szablonu takiego jak ten: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale  — masz zdefiniowane reguły skalowania i działają doskonale, bez względu na to, ile obciążeń zostało umieszczonych na maszynach wirtualnych, nie jest to automatyczne skalowanie.

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów
Oto kilka rzeczy, które należy wziąć pod uwagę:

* Ile procesorów wirtualnych vCPU każdej maszyny wirtualnej, i czy ładujesz każdy vCPU?
  Poprzedni przykładowy szablon szybkiego startu platformy Azure zawiera skrypt do_work. php, który ładuje pojedyncze vCPU. Jeśli używasz maszyny wirtualnej o rozmiarze większym niż vCPU maszyn wirtualnych, takiej jak Standard_A1 lub D1, musisz uruchomić to obciążenie wiele razy. Sprawdź, ile procesorów wirtualnych vCPU dla maszyn wirtualnych, przeglądając [rozmiary maszyn wirtualnych z systemem Windows na platformie Azure](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Ile maszyn wirtualnych w zestawie skalowania maszyn wirtualnych jest wykonywanych na każdym z nich?
  
    Zdarzenie skalowania w poziomie odbywa się tylko wtedy, gdy średni czas CPU we **wszystkich** maszynach wirtualnych w zestawie skalowania przekracza wartość progową, w odniesieniu do czasu wewnętrznego zdefiniowanego w regułach skalowania automatycznego.
* Czy pominięto jakieś zdarzenia skalowania?
  
    Sprawdź dzienniki inspekcji w Azure Portal dla zdarzeń skalowania. Może to być skalowanie w górę i w dół, które nie zostało pominięte. Można filtrować według "skali".
  
    ![Dzienniki inspekcji][audit]
* Czy progi skalowania i skalowania w poziomie są wystarczająco różne?
  
    Załóżmy, że ustawisz skalowanie w poziomie, gdy średni procesor CPU jest większy niż 50% przez pięć minut i do skalowania, gdy średni procesor CPU jest mniejszy niż 50%. To ustawienie może spowodować wystąpienie problemu "niestabilny", gdy użycie procesora zostanie zbliżone do progu, a akcje skalowania ciągle rosną i zmniejszają rozmiar zestawu. Z powodu tego ustawienia Usługa automatycznego skalowania podejmuje próbę uniemożliwienia "niestabilny", który może nie skalować. W związku z tym upewnij się, że skalowanie w poziomie i progi skalowania są wystarczająco różne, aby umożliwić pewne miejsce między skalowaniem.
* Czy piszesz własny szablon JSON?
  
    Jest to łatwe w obsłudze błędów, więc Zacznij od szablonu, takiego jak ten, który jest sprawdzony do pracy, i wprowadź małe zmiany przyrostowe. 
* Czy możesz ręcznie skalować w poziomie lub na zewnątrz?
  
    Spróbuj ponownie wdrożyć zasób zestawu skalowania maszyn wirtualnych przy użyciu innego ustawienia "pojemność", aby ręcznie zmienić liczbę maszyn wirtualnych. Przykładowy szablon jest tutaj: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing — może być konieczne edytowanie szablonu, aby upewnić się, że ma ten sam rozmiar maszyny, jak używany przez zestaw skalowania. Jeśli można pomyślnie zmienić liczbę maszyn wirtualnych ręcznie, wiadomo, że problem jest izolowany do automatycznego skalowania.
* Sprawdź zasoby Microsoft. COMPUTE/element virtualmachinescaleset i Microsoft. Insights w [Azure Resource Explorer](https://resources.azure.com/)
  
    Azure Resource Explorer to narzędzie do rozwiązywania problemów, które pokazuje stan zasobów Azure Resource Manager. Kliknij swoją subskrypcję i przejrzyj grupę zasobów, którą chcesz rozwiązać. W obszarze dostawca zasobów obliczeniowych zapoznaj się z utworzonym zestawem skalowania maszyn wirtualnych i sprawdź Widok wystąpienia, który pokazuje stan wdrożenia. Sprawdź również widok wystąpienia maszyn wirtualnych w zestawie skalowania maszyn wirtualnych. Następnie przejdź do dostawcy zasobów Microsoft. Insights i sprawdź, czy reguły skalowania automatycznego wyglądają w prawo.
* Czy rozszerzenie diagnostyczne działa i emituje dane wydajności?
  
    **Aktualizacja:** Skalowanie automatyczne platformy Azure zostało udoskonalone tak, aby korzystało z potoku metryk opartych na hoście, który nie wymaga już zainstalowania rozszerzenia diagnostyki. W następnych akapitach nie ma już zastosowania w przypadku tworzenia aplikacji skalowania automatycznego przy użyciu nowego potoku. Przykład szablonów platformy Azure, które zostały przekonwertowane do korzystania z potoku hosta, jest dostępny tutaj: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale . 
  
    Korzystanie z metryk opartych na hoście na potrzeby automatycznego skalowania jest lepsze z następujących powodów:
  
  * Mniejsza część ruchoma, ponieważ nie trzeba instalować rozszerzeń diagnostyki.
  * Prostsze szablony. Po prostu Dodaj reguły automatycznego skalowania usługi Insights do istniejącego szablonu zestawu skalowania.
  * Bardziej niezawodne raportowanie i szybsze uruchamianie nowych maszyn wirtualnych.
    
    Jedyną przyczyną, którą można chcieć zachować przy użyciu rozszerzenia diagnostycznego, jest to, że wymagane jest raportowanie/skalowanie diagnostyki pamięci. Metryki oparte na hoście nie zgłaszają pamięci.
    
    Z tego względu, jeśli używasz rozszerzeń diagnostycznych do skalowania automatycznego, postępuj zgodnie z resztą tego artykułu.
    
    Skalowanie automatyczne w Azure Resource Manager może działać (ale nie musi już) za pomocą rozszerzenia maszyny wirtualnej o nazwie rozszerzenie diagnostyki. Emituje dane wydajności do konta magazynu zdefiniowanego w szablonie. Te dane są następnie agregowane przez usługę Azure Monitor.
    
    Jeśli usługa Insights nie może odczytać danych z maszyn wirtualnych, należy wysłać do Ciebie wiadomość e-mail. Na przykład otrzymujesz wiadomość e-mail, jeśli maszyny wirtualne nie działają. Pamiętaj o sprawdzeniu poczty e-mail pod adresem e-mail określonym podczas tworzenia konta platformy Azure.
    
    Możesz również sprawdzić dane. Zapoznaj się z kontem usługi Azure Storage za pomocą Eksploratora chmury. Na przykład za pomocą [programu Visual Studio Cloud Explorer](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2)Zaloguj się i wybierz subskrypcję platformy Azure, której używasz. Następnie Sprawdź nazwę konta magazynu diagnostyki, do którego istnieje odwołanie w definicji rozszerzenia diagnostyki w szablonie wdrożenia.
    
    ![Cloud Explorer][explorer]
    
    Zobaczysz wiele tabel, w których są przechowywane dane z każdej maszyny wirtualnej. Na przykład przy użyciu systemu Linux i metryki procesora CPU należy zapoznać się z najnowszymi wierszami. Program Visual Studio Cloud Explorer obsługuje język zapytań, aby można było uruchomić zapytanie. Na przykład można uruchomić zapytanie dla "timestamp gt DateTime" 2016-02-02T21:20:00Z "", aby upewnić się, że otrzymujesz najnowsze zdarzenia. Strefa czasowa odnosi się do czasu UTC. Czy dane widoczne w tym miejscu odpowiadają skonfigurowanym regułom skalowania? W poniższym przykładzie procesor CPU dla maszyny 20 został zwiększony do 100% w ciągu ostatnich pięciu minut.
    
    ![Tabele magazynu][tables]
    
    Jeśli dane nie znajdują się tam, oznacza to, że problem dotyczy rozszerzenia diagnostycznego działającego na maszynach wirtualnych. Jeśli tam znajdują się dane, oznacza to, że występuje problem z regułami skalowania lub usługą Insights. Sprawdź [Stan platformy Azure](https://azure.microsoft.com/status/).
    
    Jeśli po wykonaniu tych kroków nadal występują problemy ze skalowaniem automatycznego, możesz wypróbować następujące zasoby: 
    * Zapoznaj się z forów na stronie pytań i odpowiedzi [firmy Microsoft&pytań](/answers/topics/azure-virtual-machines.html)i [przepełnienia stosu](https://stackoverflow.com/questions/tagged/azure) 
    * Rejestrowanie zgłoszenia do pomocy technicznej. Przygotuj się, aby udostępnić szablon i widok danych dotyczących wydajności.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png
