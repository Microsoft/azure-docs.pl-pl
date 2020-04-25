---
title: Certyfikowanie obrazu maszyny wirtualnej w witrynie Azure Marketplace
description: Wyjaśnia, jak testować i przesyłać obraz maszyny wirtualnej na potrzeby certyfikacji w portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/26/2018
ms.author: dsindona
ms.openlocfilehash: 81dec159a8a84adce97750666baba455ae3fdd93
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82147077"
---
# <a name="certify-your-vm-image"></a>Certyfikowanie obrazu maszyny wirtualnej

> [!IMPORTANT]
> Od 13 kwietnia 2020 rozpocznie się zarządzanie ofertami usługi Azure Virtual Machine w centrum partnerskim. Po przeprowadzeniu migracji utworzysz oferty w centrum partnerskim i zarządzaj nimi. Postępuj zgodnie z instrukcjami w temacie [certyfikat obrazu maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-image-certification) , aby zarządzać migrowanymi ofertami.

Po utworzeniu i wdrożeniu maszyny wirtualnej należy przetestować i przesłać obraz maszyny wirtualnej na potrzeby certyfikacji w portalu Azure Marketplace. W tym artykule wyjaśniono, w jaki sposób pobrać *Narzędzie do testowania certyfikacji dla certyfikatu platformy Azure*, jak używać tego narzędzia do certyfikowania obrazu maszyny wirtualnej i przekazywania wyników weryfikacji do kontenera platformy Azure, w którym znajdują się wirtualne dyski twarde. 


## <a name="download-and-run-the-certification-test-tool"></a>Pobieranie i uruchamianie narzędzia testowego certyfikacji

Narzędzie Test certyfikacji dla certyfikatu platformy Azure jest uruchamiane na lokalnym komputerze z systemem Windows, ale testuje maszynę wirtualną z systemem Windows lub Linux opartą na platformie Azure.  Sprawdza, czy obraz maszyny wirtualnej użytkownika jest zgodny z Microsoft Azure — że zostały spełnione wskazówki i wymagania dotyczące przygotowywania wirtualnego dysku twardego. Dane wyjściowe narzędzia to raport zgodności, który zostanie przekazany do [Portal Cloud partner](https://cloudpartner.azure.com) , aby zażądać certyfikacji maszyny wirtualnej.

1. Pobierz i zainstaluj najnowsze [Narzędzie testowania certyfikacji dla certyfikatu platformy Azure](https://www.microsoft.com/download/details.aspx?id=44299). 
2. Otwórz narzędzie certyfikacji, a następnie kliknij przycisk **Rozpocznij nowy test**.
3. Na ekranie **Informacje o testach** wprowadź **nazwę testu** dla przebiegu testu.
4. Wybierz **platformę** dla maszyny wirtualnej ( `Windows Server` lub `Linux`). Wybór platformy ma wpływ na pozostałe opcje.
5. Jeśli maszyna wirtualna korzysta z tej usługi bazy danych, zaznacz pole wyboru **Testuj dla Azure SQL Database** .

   ![Strona początkowa narzędzia do testowania certyfikatów](./media/publishvm_025.png)


## <a name="connect-the-certification-tool-to-a-vm-image"></a>Łączenie Narzędzia certyfikacji z obrazem maszyny wirtualnej

  Narzędzie nawiązuje połączenie z maszynami wirtualnymi z systemem Windows [przy użyciu](https://www.ssh.com/ssh/protocol/) [programu PowerShell](https://docs.microsoft.com/powershell/) i nawiązuje połączenie z maszynami wirtualnymi z systemem Linux

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>Łączenie Narzędzia certyfikacji z obrazem maszyny wirtualnej z systemem Linux

1. Wybierz tryb **uwierzytelniania SSH** : `Password Authentication` lub `key File Authentication`.
2. W przypadku korzystania z uwierzytelniania opartego na hasłach wprowadź wartości w polu Nazwa DNS, **Nazwa użytkownika**i **hasło** **maszyny wirtualnej**.  Opcjonalnie można zmienić domyślny numer **portu SSH** .

     ![Uwierzytelnianie hasła maszyny wirtualnej z systemem Linux](./media/publishvm_026.png)

3. W przypadku użycia uwierzytelniania opartego na plikach klucza wpisz wartości w polu **nazwa DNS maszyny wirtualnej**, **Nazwa użytkownika**i lokalizacja **klucza prywatnego** .  Opcjonalnie możesz podać **hasło** lub zmienić domyślny numer **portu SSH** .

     ![Uwierzytelnianie plików na obrazie maszyny wirtualnej z systemem Linux](./media/publishvm_027.png)

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Łączenie Narzędzia certyfikacji z obrazem maszyny wirtualnej z systemem Windows**
1. Wprowadź w pełni kwalifikowaną **nazwę DNS maszyny wirtualnej** (na przykład `MyVMName.Cloudapp.net`).
2. Wprowadź wartości w polu **Nazwa użytkownika** i **hasło**.

   ![Uwierzytelnianie hasła w obrazie maszyny wirtualnej systemu Windows](./media/publishvm_028.png)


## <a name="run-a-certification-test"></a>Uruchamianie testu certyfikacji

Po podaniu wartości parametrów dla obrazu maszyny wirtualnej w narzędziu certyfikacji wybierz pozycję **Testuj połączenie** , aby upewnić się, że nastąpiło prawidłowe połączenie z maszyną wirtualną. Po zweryfikowaniu połączenia wybierz pozycję **dalej** , aby rozpocząć test.  Po zakończeniu testu zostanie wyświetlona tabela z wynikami testu (przebieg/niepowodzenie/ostrzeżenie).  Poniższy przykład pokazuje wyniki testu dla testu maszyny wirtualnej z systemem Linux. 

![Wyniki testu certyfikacji dla obrazu maszyny wirtualnej z systemem Linux](./media/publishvm_029.png)

Jeśli którykolwiek z testów zakończy się niepowodzeniem, obraz *nie* zostanie certyfikowany. W takim przypadku należy przejrzeć wymagania i komunikaty o błędach, wprowadzić wskazane zmiany i ponownie uruchomić test. 

Po automatycznym teście wymagane jest podanie dodatkowych informacji o obrazie maszyny wirtualnej na ekranie **kwestionariusza** .  Zawiera dwie karty, które należy wykonać.  Karta **Ocena ogólna** zawiera pytania **prawdziwe/fałszywe** , podczas gdy **dostosowanie jądra** zawiera wiele pytań dotyczących wyboru i dowolnego kształtu.  Wykonaj pytania na obu kartach, a następnie wybierz przycisk **dalej**.

![Kwestionariusz Narzędzia certyfikacji](./media/publishvm_030.png)

Ostatni ekran umożliwia podanie dodatkowych informacji, takich jak informacje o dostępie SSH dla obrazu maszyny wirtualnej z systemem Linux, oraz wyjaśnienie ewentualnych ocen zakończonych niepowodzeniem w przypadku wyszukiwania wyjątków. 

Na koniec kliknij pozycję **Generuj raport** , aby pobrać wyniki testów i pliki dziennika dla wykonanych przypadków testowych, a także do odpowiedzi na kwestionariusz. Zapisz wyniki w tym samym kontenerze co wirtualne dyski twarde.

![Zapisz wyniki testu certyfikacji](./media/publishvm_031.png)


## <a name="next-steps"></a>Następne kroki

Następnie [dla każdego wirtualnego dysku twardego](./cpp-get-sas-uri.md) , który zostanie przesłany do portalu Marketplace, zostaną wygenerowane identyfikatory URI. 
