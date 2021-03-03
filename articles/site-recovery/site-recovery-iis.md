---
title: Konfigurowanie odzyskiwania po awarii dla aplikacji sieci Web usług IIS przy użyciu Azure Site Recovery
description: Dowiedz się, jak replikować maszyny wirtualne farmy sieci Web usług IIS przy użyciu Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 56ac58e47bffc73c7079af043ad567a77e8f3323
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101735509"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-iis-based-web-application"></a>Konfigurowanie odzyskiwania po awarii dla wielowarstwowej aplikacji internetowej opartej na usługach IIS

Oprogramowanie aplikacji jest aparatem produktywności biznesowej w organizacji. Różne aplikacje sieci Web mogą działać w różnych celach w organizacji. Niektóre aplikacje, takie jak aplikacje używane do przetwarzania listy płac, aplikacje finansowe i witryny sieci Web dostępne dla klientów, mogą mieć kluczowe znaczenie dla organizacji. Aby zapobiec utracie produktywności, ważne jest, aby organizacja mogła stale pracować z tymi aplikacjami. Ponadto, jeśli te aplikacje są stale dostępne, może to zapobiec uszkodzeniu marki lub wizerunku organizacji.

Krytyczne aplikacje sieci Web są zwykle konfigurowane jako aplikacje wielowarstwowe: sieć Web, baza danych i aplikacja są na różnych warstwach. Oprócz rozmieszczenia w różnych warstwach aplikacje mogą również używać wielu serwerów w każdej warstwie do równoważenia obciążenia ruchu. Ponadto mapowania między różnymi warstwami i na serwerze sieci Web mogą opierać się na statycznych adresach IP. W przypadku przejścia w tryb failover niektóre z tych mapowań należy zaktualizować, zwłaszcza jeśli na serwerze sieci Web skonfigurowano wiele witryn internetowych. Jeśli aplikacje sieci Web używają protokołu TLS, należy zaktualizować powiązania certyfikatów.

Tradycyjne metody odzyskiwania, które nie są oparte na replikacji, obejmują tworzenie kopii zapasowych różnych plików konfiguracji, ustawień rejestru, powiązań, niestandardowych składników (COM lub .NET), zawartości i certyfikatów. Pliki są odzyskiwane przez zestaw kroków ręcznych. Tradycyjne metody odzyskiwania kopii zapasowych i ręcznie przywracające pliki są bardzo skomplikowane, podatne na błędy i nie są skalowalne. Na przykład można łatwo zapomnieć, aby utworzyć kopię zapasową certyfikatów. Po przejściu w tryb failover pozostanie bez wyboru, ale do kupowania nowych certyfikatów dla serwera.

Dobre rozwiązanie do odzyskiwania po awarii obsługuje plany odzyskiwania dotyczące modelowania dla złożonych architektur aplikacji. Należy również dodać dostosowane kroki do planu odzyskiwania, aby obsługiwać mapowania aplikacji między warstwami. Jeśli wystąpi awaria, mapowania aplikacji udostępniają jednokrotne rozwiązanie, które może prowadzić do obniżenia RTO.

W tym artykule opisano sposób ochrony aplikacji sieci Web opartej na Internet Information Services (IIS) przy użyciu [Azure Site Recovery](site-recovery-overview.md). W tym artykule opisano najlepsze rozwiązania dotyczące replikacji trójwarstwowej aplikacji sieci Web opartej na usługach IIS do platformy Azure, sposób wykonywania czynności związanych z odzyskiwaniem po awarii oraz sposób przechodzenia w tryb failover aplikacji na platformę Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że wiesz, jak wykonać następujące zadania:

* [Replikowanie maszyny wirtualnej na platformę Azure](vmware-azure-tutorial.md)
* [Projektowanie sieci odzyskiwania](./concepts-on-premises-to-azure-networking.md)
* [Wykonaj test pracy w trybie failover na platformie Azure](site-recovery-test-failover-to-azure.md)
* [Przełączenie w tryb failover na platformie Azure](site-recovery-failover.md)
* [Replikowanie kontrolera domeny](site-recovery-active-directory.md)
* [Replikowanie programu SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Wzorce wdrożenia
Aplikacja sieci Web oparta na usługach IIS jest zwykle zgodna z jednym z następujących wzorców wdrożenia:

**Wzorzec wdrożenia 1**

Farma sieci Web opartej na usługach IIS z routingiem żądań aplikacji (ARR), serwerem usług IIS i SQL Server.

![Diagram farmy sieci Web opartej na usługach IIS, która ma trzy warstwy](./media/site-recovery-iis/deployment-pattern1.png)

**Wzorzec wdrożenia 2**

Farma sieci Web opartej na usługach IIS z ARR, serwerem usług IIS, serwerem aplikacji i SQL Server.

![Diagram farmy sieci Web opartej na usługach IIS, która ma cztery warstwy](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Obsługa usługi Site Recovery

Aby zapoznać się z przykładami w tym artykule, używamy maszyn wirtualnych VMware z usługami IIS 7,5 w systemie Windows Server 2012 R2 Enterprise. Ponieważ replikacja Site Recovery nie jest specyficzna dla aplikacji, zalecenia w tym artykule powinny być stosowane w scenariuszach wymienionych w poniższej tabeli oraz dla różnych wersji usług IIS.

### <a name="source-and-target"></a>Źródło i cel

Scenariusz | Do lokacji dodatkowej | Na platformę Azure
--- | --- | ---
Hyper-V | Tak | Tak
VMware | Tak | Tak
Serwer fizyczny | Nie | Tak
Azure|Nie dotyczy|Yes

## <a name="replicate-virtual-machines"></a>Replikowanie maszyn wirtualnych

Aby rozpocząć replikację wszystkich maszyn wirtualnych farmy sieci Web usług IIS do platformy Azure, postępuj zgodnie ze wskazówkami w temacie [Testowanie pracy w trybie failover na platformie Azure w Site Recovery](site-recovery-test-failover-to-azure.md).

Jeśli używasz statycznego adresu IP, możesz określić adres IP, który ma być pobierany przez maszynę wirtualną. Aby ustawić adres IP, przejdź do pozycji **obliczenia i ustawienia sieci**  >  **docelowy adres IP**.

![Zrzut ekranu, który pokazuje, jak ustawić docelowy adres IP w okienku Site Recovery obliczenia i sieć](./media/site-recovery-active-directory/dns-target-ip.png)

## <a name="create-a-recovery-plan"></a>Tworzenie planu odzyskiwania
Plan odzyskiwania obsługuje sekwencjonowanie różnych warstw w aplikacji wielowarstwowej podczas pracy w trybie failover. Sekwencjonowanie pomaga zachować spójność aplikacji. Po utworzeniu planu odzyskiwania dla wielowarstwowej aplikacji sieci Web wykonaj kroki opisane w temacie [Tworzenie planu odzyskiwania przy użyciu Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="add-virtual-machines-to-failover-groups"></a>Dodawanie maszyn wirtualnych do grup trybu failover
Typowa wielowarstwowa aplikacja sieci Web usług IIS składa się z następujących składników:
* Warstwa bazy danych z maszynami wirtualnymi SQL.
* Warstwa sieci Web, która składa się z serwera IIS i warstwy aplikacji. 

Dodaj maszyny wirtualne do różnych grup w oparciu o warstwę:

1. Utwórz plan odzyskiwania. Dodaj maszyny wirtualne warstwy bazy danych w grupie 1. Dzięki temu maszyny wirtualne warstwy bazy danych są zamykane jako ostatnie i najpierw wprowadzane.
1. Dodaj maszyny wirtualne warstwy aplikacji w grupie 2. Gwarantuje to, że maszyny wirtualne warstwy aplikacji zostaną przywrócone po przejściu warstwy bazy danych.
1. Dodaj maszyny wirtualne warstwy sieci Web w grupie 3. Gwarantuje to, że maszyny wirtualne warstwy sieci Web zostaną przywrócone po przełączeniu warstwy aplikacji.
1. Dodaj maszyny wirtualne z równoważeniem obciążenia w grupie 4. Gwarantuje to, że maszyny wirtualne równoważenia obciążenia zostaną przywrócone po przejściu warstwy sieci Web.

Aby uzyskać więcej informacji, zobacz [Dostosowywanie planu odzyskiwania](site-recovery-runbook-automation.md#customize-the-recovery-plan).


### <a name="add-a-script-to-the-recovery-plan"></a>Dodawanie skryptu do planu odzyskiwania
Aby Farma sieci Web usług IIS działała prawidłowo, może być konieczne wykonanie pewnych operacji na maszynach wirtualnych platformy Azure po zakończeniu pracy w trybie failover lub w trakcie testu pracy w trybie failover. Można zautomatyzować niektóre operacje wykonywane po przejściu w tryb failover. Można na przykład zaktualizować wpis DNS, zmienić powiązanie witryny lub zmienić parametry połączenia przez dodanie odpowiednich skryptów do planu odzyskiwania. [Dodawanie skryptu programu VMM do planu odzyskiwania](./hyper-v-vmm-recovery-script.md) zawiera opis sposobu konfigurowania zautomatyzowanych zadań przy użyciu skryptu.

#### <a name="dns-update"></a>Aktualizacja systemu DNS
Jeśli serwer DNS jest skonfigurowany do dynamicznej aktualizacji DNS, maszyny wirtualne zazwyczaj zaktualizują system DNS przy użyciu nowego adresu IP podczas uruchamiania. Jeśli chcesz dodać jawny krok w celu zaktualizowania systemu DNS przy użyciu nowych adresów IP maszyn wirtualnych, Dodaj skrypt, [Aby zaktualizować adres IP w systemie DNS](https://aka.ms/asr-dns-update) jako akcję po przejściu w tryb failover w grupach planu odzyskiwania.  

#### <a name="connection-string-in-an-applications-webconfig"></a>Parametry połączenia w web.config aplikacji
Parametry połączenia określają bazę danych, z którą witryna sieci Web komunikuje się. Jeśli parametry połączenia mają nazwę maszyny wirtualnej bazy danych, nie są wymagane żadne dalsze kroki po zakończeniu pracy w trybie failover. Aplikacja może automatycznie komunikować się z bazą danych. Ponadto, jeśli adres IP maszyny wirtualnej bazy danych jest zachowywany, nie trzeba aktualizować parametrów połączenia. 

Jeśli parametry połączenia odwołują się do maszyny wirtualnej bazy danych przy użyciu adresu IP, należy zaktualizować po zakończeniu pracy w trybie failover. Na przykład następujące parametry połączenia wskazują bazę danych z adresem IP 127.0.1.2:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
<connectionStrings>
<add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" />
</connectionStrings>
</configuration>
```

Aby zaktualizować parametry połączenia w warstwie sieci Web, Dodaj [skrypt aktualizacji połączenia usług IIS](https://gallery.technet.microsoft.com/Update-IIS-connection-2579aadc) po grupie 3 w planie odzyskiwania.

#### <a name="site-bindings-for-the-application"></a>Powiązania witryny dla aplikacji
Każda lokacja zawiera informacje o powiązaniu. Informacje o powiązaniu obejmują typ powiązania, adres IP, pod którym serwer IIS nasłuchuje żądań lokacji, numer portu i nazwy hostów lokacji. Podczas pracy w trybie failover może być konieczne zaktualizowanie tych powiązań, jeśli istnieje zmiana w skojarzonym z nimi adresie IP.

> [!NOTE]
>
> Jeśli ustawisz powiązanie witryny ze **wszystkimi nieprzypisanymi**, nie musisz aktualizować tego powiązania po przejściu w tryb failover. Ponadto, jeśli adres IP skojarzony z witryną nie został zmieniony po przejściu w tryb failover, nie trzeba aktualizować powiązania witryny. (Przechowywanie adresu IP zależy od architektury sieci i podsieci przypisanych do lokacji podstawowej i odzyskiwania. Aktualizowanie ich może być niemożliwe dla Twojej organizacji.

![Zrzut ekranu pokazujący ustawienie powiązania TLS/SSL](./media/site-recovery-iis/sslbinding.png)

Jeśli adres IP jest skojarzony z lokacją, należy zaktualizować wszystkie powiązania lokacji przy użyciu nowego adresu IP. Aby zmienić powiązania witryny, Dodaj [skrypt aktualizacji warstwy sieci Web usług IIS](/samples/browse/?redirectedfrom=TechNet-Gallery) po grupie 3 w planie odzyskiwania.

#### <a name="update-the-load-balancer-ip-address"></a>Aktualizowanie adresu IP modułu równoważenia obciążenia
Jeśli masz maszynę wirtualną o genotypie ARR, zaktualizuj adres IP przy użyciu [skryptu trybu failover usług IIS](/samples/browse/?redirectedfrom=TechNet-Gallery) z grupy 4.

#### <a name="tlsssl-certificate-binding-for-an-https-connection"></a>Powiązanie certyfikatu TLS/SSL dla połączenia HTTPS
Witryna sieci Web może mieć skojarzony certyfikat TLS/SSL, który pomaga zapewnić bezpieczną komunikację między serwerem sieci Web a przeglądarką użytkownika. Jeśli witryna sieci Web ma połączenie HTTPS, a także ma skojarzone powiązanie z adresem IP serwera usług IIS z powiązaniem certyfikatu TLS/SSL, należy dodać nowe powiązanie witryny dla certyfikatu przy użyciu adresu IP maszyny wirtualnej usług IIS po zakończeniu pracy w trybie failover.

Dla tych składników można wystawić certyfikat TLS/SSL:

* W pełni kwalifikowana nazwa domeny witryny sieci Web.
* Nazwa serwera.
* Certyfikat wieloznaczny dla nazwy domeny.  
* Adres IP. Jeśli certyfikat TLS/SSL zostanie wystawiony na podstawie adresu IP serwera IIS, inny certyfikat TLS/SSL musi być wystawiony względem adresu IP serwera usług IIS w witrynie platformy Azure. Należy utworzyć dodatkowe powiązanie protokołu TLS dla tego certyfikatu. W związku z tym nie zaleca się używania certyfikatu TLS/SSL dla adresu IP. Ta opcja jest mniej szeroko używana i wkrótce będzie przestarzała zgodnie z nowymi zmianami urzędu certyfikacji/forum przeglądarki.

#### <a name="update-the-dependency-between-the-web-tier-and-the-application-tier"></a>Aktualizowanie zależności między warstwą sieci Web i warstwą aplikacji
Jeśli masz zależność specyficzną dla aplikacji, która jest oparta na adresie IP maszyn wirtualnych, musisz zaktualizować tę zależność po zakończeniu pracy w trybie failover.

## <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

1. W Azure Portal wybierz swój magazyn Recovery Services.
2. Wybierz plan odzyskiwania, który został utworzony dla kolektywu serwerów sieci Web usług IIS.
3. Wybierz pozycję **Test pracy w trybie failover**.
4. Aby rozpocząć proces testowego przełączania do trybu failover, wybierz punkt odzyskiwania i sieć wirtualną platformy Azure.
5. Gdy środowisko pomocnicze jest w stanie up, można wykonać walidację.
6. Po zakończeniu walidacji w celu oczyszczenia środowiska testowego trybu failover wybierz pozycję **walidacje ukończone**.

Aby uzyskać więcej informacji, zobacz [test pracy w trybie failover na platformie Azure w Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Uruchamianie trybu failover

1. W Azure Portal wybierz swój magazyn Recovery Services.
1. Wybierz plan odzyskiwania, który został utworzony dla kolektywu serwerów sieci Web usług IIS.
1. Wybierz pozycję **Tryb failover**.
1. Aby rozpocząć proces trybu failover, wybierz punkt odzyskiwania.

Aby uzyskać więcej informacji, zobacz [tryb failover w Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej na temat [replikowania innych aplikacji](site-recovery-workload.md) przy użyciu Site Recovery.