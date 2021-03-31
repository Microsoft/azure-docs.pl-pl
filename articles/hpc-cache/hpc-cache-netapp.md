---
title: Użyj pamięci podręcznej platformy Azure HPC i Azure NetApp Files
description: Jak korzystać z pamięci podręcznej platformy Azure HPC w celu ulepszania dostępu do danych przechowywanych przy użyciu Azure NetApp Files
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: e955ddc14bb2b0a7abc0dc815c6955247568876b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86497016"
---
# <a name="use-azure-hpc-cache-with-azure-netapp-files"></a>Używanie pamięci podręcznej platformy Azure HPC z Azure NetApp Files

[Azure NetApp Files](https://azure.microsoft.com/services/netapp/) jako miejsce docelowe magazynu dla pamięci podręcznej platformy Azure HPC. W tym artykule wyjaśniono, jak te dwie usługi mogą współdziałać i podano porady dotyczące ich konfigurowania.

Azure NetApp Files łączy swój system operacyjny ONTAP z skalowalnością i szybkością Microsoft Azure. Ta kombinacja umożliwia użytkownikom przesunięcia określonych przepływów pracy do chmury bez konieczności ponownego pisania kodu.

Dodanie składnika pamięci podręcznej platformy Azure HPC może poprawić dostęp do pliku przez zaprezentowanie wielu Azure NetApp Files woluminów w jednej zagregowanej przestrzeni nazw. Może zapewnić buforowanie krawędzi dla woluminów znajdujących się w innym regionie usługi. Może również zwiększyć wydajność na żądanie dla woluminów, które zostały utworzone na poziomach usługi niższej warstwy w celu oszczędności kosztów.

## <a name="overview"></a>Omówienie

Aby użyć systemu Azure NetApp Files jako magazynu zaplecza z pamięcią podręczną Azure HPC, wykonaj ten proces.

1. Utwórz system Azure NetApp Files i woluminy zgodnie ze wskazówkami w temacie [Planowanie systemu, poniżej](#plan-your-azure-netapp-files-system).
1. Utwórz pamięć podręczną platformy Azure HPC w regionie, w którym potrzebujesz dostępu do pliku. (Skorzystaj z instrukcji przedstawionych w temacie [Tworzenie pamięci podręcznej platformy Azure HPC](hpc-cache-create.md)).
1. [Zdefiniuj cele magazynu](#create-storage-targets-in-the-cache) w pamięci podręcznej, które wskazują woluminy Azure NetApp Files. Utwórz jeden obiekt docelowy magazynu pamięci podręcznej dla każdego unikatowego adresu IP używanego do uzyskiwania dostępu do woluminów.
1. Klienci powinni [instalować pamięć podręczną platformy Azure HPC](#mount-storage-targets) zamiast bezpośrednio instalować Azure NetApp Files woluminów.

## <a name="plan-your-azure-netapp-files-system"></a>Zaplanuj system Azure NetApp Files

W przypadku planowania systemu Azure NetApp Files należy zwrócić uwagę na elementy w tej sekcji, aby upewnić się, że można się z nią bezproblemowo zintegrować z pamięcią podręczną Azure HPC.

Zapoznaj się również z [dokumentacją Azure NetApp Files](../azure-netapp-files/index.yml) przed utworzeniem woluminów do użycia z pamięcią podręczną platformy Azure HPC.

### <a name="nfs-client-access-only"></a>Tylko dostęp klienta NFS

Pamięć podręczna Azure HPC obecnie obsługuje tylko dostęp do systemu plików NFS. Nie można jej używać z woluminami listy ACL protokołu SMB ani bitowym trybem POSIX.

### <a name="exclusive-subnet-for-azure-netapp-files"></a>Wyłączna podsieć dla Azure NetApp Files

Azure NetApp Files używa pojedynczej podsieci delegowanej dla woluminów. Żadne inne zasoby nie mogą korzystać z tej podsieci. Ponadto do Azure NetApp Files można używać tylko jednej podsieci w sieci wirtualnej. Dowiedz się więcej w temacie [wskazówki dotyczące planowania sieci Azure NetApp Files](../azure-netapp-files/azure-netapp-files-network-topologies.md).

### <a name="delegated-subnet-size"></a>Rozmiar delegowanej podsieci

Podczas tworzenia systemu Azure NetApp Files do użycia z pamięcią podręczną platformy Azure HPC należy użyć minimalnego rozmiaru podsieci delegowanej.

Minimalny rozmiar, który jest określony za pomocą maski sieci/28, zapewnia 16 adresów IP. W przypadku Azure NetApp Files używa tylko trzech z tych dostępnych adresów IP na potrzeby dostępu do woluminu. Oznacza to, że należy utworzyć tylko trzy cele magazynu w pamięci podręcznej platformy Azure HPC, aby uwzględnić wszystkie woluminy.

Jeśli delegowana podsieć jest zbyt duża, możliwe, aby Azure NetApp Files woluminów używały większej liczby adresów IP niż może obsłużyć pojedyncze wystąpienie pamięci podręcznej platformy Azure HPC. Pojedyncza pamięć podręczna może mieć co najwyżej 10 obiektów docelowych magazynu.

Przykład szybkiego startu w dokumentacji Azure NetApp Files używa 10.7.0.0/16 dla delegowanej podsieci, która zapewnia zbyt dużą podsieć.

### <a name="capacity-pool-service-level"></a>Poziom usługi puli pojemności

Podczas wybierania poziomu usług dla puli pojemności należy wziąć pod uwagę przepływ pracy. Jeśli często zapisujesz dane z powrotem na woluminie Azure NetApp Files, wydajność pamięci podręcznej może być ograniczona w przypadku powolnego czasu zapisywania zwrotnego. Wybierz wysoki poziom usług dla woluminów, które będą często zapisywane.

Woluminy z niskimi poziomami usług mogą również wyświetlać pewne opóźnienia na początku zadania podczas wstępnego wypełniania zawartości pamięci podręcznej. Gdy pamięć podręczna zostanie uruchomiona z dobrym zestawem plików, opóźnienie powinno być niezauważalne.

Ważne jest, aby zaplanować poziom usługi puli pojemności przed czasem, ponieważ nie można jej zmienić po utworzeniu. Nowy wolumin musi zostać utworzony w innej puli pojemności, a dane skopiowane przez program.

Należy pamiętać, że można zmienić przydział magazynu woluminu i rozmiar puli pojemności bez zakłócania dostępu.

## <a name="create-storage-targets-in-the-cache"></a>Tworzenie obiektów docelowych magazynu w pamięci podręcznej

Po skonfigurowaniu systemu Azure NetApp Files i utworzeniu pamięci podręcznej platformy Azure HPC Zdefiniuj cele magazynu w pamięci podręcznej wskazujące woluminy systemu plików.

Utwórz jeden docelowy magazyn dla każdego adresu IP używanego przez woluminy Azure NetApp Files. Adres IP jest wymieniony na stronie z instrukcjami instalacji woluminu.

Jeśli wiele woluminów współużytkuje ten sam adres IP, można użyć jednego miejsca docelowego magazynu dla wszystkich z nich.  

Postępuj zgodnie z [instrukcjami instalacji zawartymi w dokumentacji Azure NetApp Files](../azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) , aby znaleźć adresy IP do użycia.

Adresy IP można również znaleźć w interfejsie wiersza polecenia platformy Azure:

```azurecli
az netappfiles volume list -g ${RESOURCE_GROUP} --account-name ${ANF_ACCOUNT} --pool-name ${POOL} --query "[].mountTargets[].ipAddress" | grep -Ee '[0-9]+[.][0-9]+[.][0-9]+[.][0-9]+' | tr -d '"' | tr -d , | sort | uniq
```

Eksport nazw w systemie Azure NetApp Files ma jeden składnik ścieżki. Nie należy próbować utworzyć docelowego magazynu dla eksportu głównego ``/`` w Azure NetApp Files, ponieważ ten eksport nie zapewnia dostępu do pliku.

Nie ma specjalnych ograniczeń ścieżek wirtualnych przestrzeni nazw dla tych obiektów docelowych magazynu.

## <a name="mount-storage-targets"></a>Zainstaluj cele magazynu

Komputery klienckie powinny zainstalować pamięć podręczną zamiast bezpośrednio instalować woluminy Azure NetApp Files. Postępuj zgodnie z instrukcjami w temacie [Instalowanie pamięci podręcznej platformy Azure HPC](hpc-cache-mount.md).

## <a name="next-steps"></a>Następne kroki

* Przeczytaj więcej na temat konfigurowania i używania [Azure NetApp Files](../azure-netapp-files/index.yml)
* Aby uzyskać pomoc w planowaniu i konfigurowaniu systemu pamięci podręcznej platformy Azure HPC do używania Azure NetApp Files, [skontaktuj się z pomocą techniczną](hpc-cache-support-ticket.md).
