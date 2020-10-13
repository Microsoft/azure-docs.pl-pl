---
title: Połączenie maszyny wirtualnej Azure SQL do indeksowania wyszukiwania
titleSuffix: Azure Cognitive Search
description: Włącz połączenia szyfrowane i skonfiguruj zaporę tak, aby zezwalała na połączenia SQL Server na maszynie wirtualnej platformy Azure za pomocą indeksatora w usłudze Azure Wyszukiwanie poznawcze.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/12/2020
ms.openlocfilehash: a13f78b6aa4fc3cb6f6777c76bc762ec565624fc
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951319"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-server-on-an-azure-vm"></a>Konfigurowanie połączenia z indeksatora Wyszukiwanie poznawcze platformy Azure do SQL Server na maszynie wirtualnej platformy Azure

Jak zostało to opisane w sekcji [łączenie Azure SQL Database z platformą azure wyszukiwanie poznawcze przy użyciu indeksatorów](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq), tworzenie indeksatorów względem **SQL Server na maszynach wirtualnych platformy Azure** (lub na **maszynach wirtualnych z usługą SQL Azure** ) jest obsługiwane przez usługę Azure wyszukiwanie poznawcze, ale istnieje kilka wymagań wstępnych związanych z zabezpieczeniami, które należy wziąć pod uwagę w pierwszej kolejności. 

Połączenia z usługi Azure Wyszukiwanie poznawcze do SQL Server na maszynie wirtualnej są publicznym połączeniem internetowym. Wszystkie miary zabezpieczeń, które zwykle są spełnione w przypadku tych połączeń, mają również zastosowanie tutaj:

+ Uzyskaj certyfikat od [dostawcy urzędu certyfikacji](https://en.wikipedia.org/wiki/Certificate_authority#Providers) dla w pełni kwalifikowanej nazwy domeny wystąpienia SQL Server na maszynie wirtualnej platformy Azure.
+ Zainstaluj certyfikat na maszynie wirtualnej, a następnie Włącz i skonfiguruj połączenia szyfrowane na maszynie wirtualnej, korzystając z instrukcji przedstawionych w tym artykule.

## <a name="enable-encrypted-connections"></a>Włącz połączenia szyfrowane
Usługa Azure Wyszukiwanie poznawcze wymaga szyfrowanego kanału dla wszystkich żądań indeksatora za pośrednictwem publicznego połączenia internetowego. W tej sekcji przedstawiono kroki, które należy wykonać w celu wykonania tej czynności.

1. Sprawdź właściwości certyfikatu, aby sprawdzić, czy nazwa podmiotu jest w pełni kwalifikowaną nazwą domeny (FQDN) maszyny wirtualnej platformy Azure. Aby wyświetlić właściwości, można użyć narzędzia, takiego jak CertUtil lub przystawki Certyfikaty. Możesz pobrać nazwę FQDN z sekcji podstawowe bloku usługi maszyny wirtualnej, w polu **publiczny adres IP/etykieta nazwy DNS** w [Azure Portal](https://portal.azure.com/).
   
   * W przypadku maszyn wirtualnych utworzonych przy użyciu nowszego szablonu **Menedżer zasobów** nazwa FQDN jest formatowana jako `<your-VM-name>.<region>.cloudapp.azure.com`
   * W przypadku starszych maszyn wirtualnych utworzonych jako **klasyczna** maszyna wirtualna nazwa FQDN jest formatowana jako `<your-cloud-service-name.cloudapp.net>` .

2. Skonfiguruj SQL Server, aby używać certyfikatu przy użyciu Edytora rejestru (regedit). 
   
    Chociaż SQL Server Configuration Manager jest często używany dla tego zadania, nie można go używać w tym scenariuszu. Nie znaleziono zaimportowanego certyfikatu, ponieważ nazwa FQDN maszyny wirtualnej na platformie Azure nie jest zgodna z nazwą FQDN określoną przez maszynę wirtualną (identyfikuje domenę jako komputer lokalny lub domenę sieci, do której jest przyłączona). Gdy nazwy nie są zgodne, należy użyć regedit, aby określić certyfikat.
   
   * W programie regedit przejdź do tego klucza rejestru: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate` .
     
     `[MSSQL13.MSSQLSERVER]`Część różni się w zależności od wersji i nazwy wystąpienia. 
   * Ustaw wartość klucza **certyfikatu** na **odcisk palca** certyfikatu TLS/SSL zaimportowanego do maszyny wirtualnej.
     
     Istnieje kilka sposobów uzyskania odcisku palca, a nieco lepiej niż inne. Jeśli skopiujesz go z przystawki **Certyfikaty** w programie MMC, prawdopodobnie wybierzesz niewidoczny znak wiodący [zgodnie z opisem w tym artykule dotyczącym pomocy technicznej](https://support.microsoft.com/kb/2023869/), co spowoduje wystąpienie błędu podczas próby połączenia. Istnieją różne Obejścia dotyczące rozwiązywania tego problemu. Najłatwiej jest w tym miejscu, a następnie ponownie wpisać pierwszy znak odcisku palca, aby usunąć znak wiodący w polu wartość klucza w regedit. Alternatywnie można użyć innego narzędzia do kopiowania odcisku palca.

3. Udziel uprawnień do konta usługi. 
   
    Upewnij się, że konto usługi SQL Server ma przyznane odpowiednie uprawnienia w kluczu prywatnym certyfikatu TLS/SSL. Jeśli zapomina ten krok, SQL Server nie zostanie uruchomiona. Możesz użyć przystawki **Certyfikaty** lub narzędzia **certutil** dla tego zadania.
    
4. Uruchom ponownie usługę SQL Server.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>Konfigurowanie łączności SQL Server na maszynie wirtualnej
Po skonfigurowaniu połączenia szyfrowanego wymaganego przez usługę Azure Wyszukiwanie poznawcze w przypadku maszyn wirtualnych platformy Azure są dostępne dodatkowe SQL Server czynności konfiguracyjne. Jeśli jeszcze tego nie zrobiono, następnym krokiem jest zakończenie konfiguracji przy użyciu jednego z następujących artykułów:

* Aby uzyskać **Menedżer zasobów** maszyny wirtualnej, zobacz [nawiązywanie połączenia z maszyną wirtualną SQL Server na platformie Azure przy użyciu Menedżer zasobów](../azure-sql/virtual-machines/windows/ways-to-connect-to-sql.md). 
* W przypadku **klasycznej** maszyny wirtualnej zobacz [nawiązywanie połączenia z maszyną wirtualną SQL Server w klasycznym systemie Azure](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-connect).

W szczególności zapoznaj się z sekcją w każdym artykule dotyczącym łączenia przez Internet.

## <a name="configure-the-network-security-group-nsg"></a>Konfigurowanie sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń)
Konfiguracja sieciowej grupy zabezpieczeń i odpowiedniego punktu końcowego platformy Azure lub listy Access Control (ACL) nie jest nietypowa, aby umożliwić dostęp do maszyny wirtualnej platformy Azure innym stronom. W tym celu należy wcześniej wykonać te czynności, aby umożliwić własnej logice aplikacji łączenie się z maszyną wirtualną usługi SQL Azure. Nie jest on różny dla połączenia usługi Azure Wyszukiwanie poznawcze z maszyną wirtualną usługi SQL Azure. 

Poniższe linki zawierają instrukcje dotyczące konfiguracji sieciowej grupy zabezpieczeń dla wdrożeń maszyn wirtualnych. Te instrukcje służą do listy ACL punktu końcowego usługi Azure Wyszukiwanie poznawcze na podstawie jego adresu IP.

> [!NOTE]
> Aby uzyskać ogólne informacje, zobacz [co to jest sieciowa Grupa zabezpieczeń?](../virtual-network/network-security-groups-overview.md)
> 
> 

* Aby uzyskać **Menedżer zasobów** maszyny wirtualnej, zobacz [How to Create sieciowych grup zabezpieczeń for ARM Deployments](../virtual-network/tutorial-filter-network-traffic.md). 
* Aby zapoznać się z **klasyczną** maszyną wirtualną, zobacz [jak utworzyć sieciowych grup zabezpieczeń dla wdrożeń klasycznych](/previous-versions/azure/virtual-network/virtual-networks-create-nsg-classic-ps).

Adresowanie IP może stanowić kilka wyzwań, które można łatwo przezwyciężyć, jeśli masz świadomość problemu i potencjalnych obejść. Pozostałe sekcje zawierają zalecenia dotyczące obsługi problemów związanych z adresami IP na liście ACL.

#### <a name="restrict-access-to-the-azure-cognitive-search"></a>Ograniczanie dostępu do Wyszukiwanie poznawcze platformy Azure
Zdecydowanie zalecamy, aby ograniczyć dostęp do adresu IP usługi wyszukiwania oraz zakresu adresów IP usługi `AzureCognitiveSearch` [service tag](../virtual-network/service-tags-overview.md#available-service-tags) na liście ACL, a nie do otwierania maszyn wirtualnych usługi SQL Azure na wszystkich żądaniach połączeń.

Adres IP można sprawdzić, wysyłając polecenie ping do nazwy FQDN (na przykład `<your-search-service-name>.search.windows.net` ) usługi wyszukiwania.

Zakres adresów IP można sprawdzić `AzureCognitiveSearch` [service tag](../virtual-network/service-tags-overview.md#available-service-tags) za pomocą [plików JSON do pobrania](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) lub za pośrednictwem [interfejsu API odnajdywania tagów usługi](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview). Zakres adresów IP jest aktualizowany co tydzień.

#### <a name="managing-ip-address-fluctuations"></a>Zarządzanie fluktuacjami adresów IP
Jeśli usługa wyszukiwania ma tylko jedną jednostkę wyszukiwania (czyli jedną replikę i jedną partycję), adres IP ulegnie zmianie podczas rutynowych ponownych uruchomień usługi. unieważnienie istniejącej listy kontroli dostępu przy użyciu adresu IP usługi wyszukiwania.

Jednym ze sposobów uniknięcia kolejnego błędu łączności jest użycie więcej niż jednej repliki i jednej partycji na platformie Azure Wyszukiwanie poznawcze. Zwiększa to koszt, ale również rozwiązuje problem z adresem IP. W usłudze Azure Wyszukiwanie poznawcze adresy IP nie zmieniają się, jeśli masz więcej niż jedną jednostkę wyszukiwania.

Drugim podejściem jest umożliwienie niepowodzenia połączenia, a następnie ponowne skonfigurowanie list kontroli dostępu w sieciowej grupy zabezpieczeń. Średnio można oczekiwać, że adresy IP są zmieniane co kilka tygodni. W przypadku klientów, którzy przeprowadzają nierzadko kontrolowane indeksowanie, takie podejście może być w dobrej kondycji.

Trzecią żywotną (ale nieszczególnie bezpieczną) podejściem jest określenie zakresu adresów IP regionu platformy Azure, w którym Zainicjowano obsługę usługi wyszukiwania. Lista zakresów adresów IP, z których publiczne adresy IP są przypisywane do zasobów platformy Azure, jest publikowana w [zakresach adresów IP centrum danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653). 

#### <a name="include-the-azure-cognitive-search-portal-ip-addresses"></a>Uwzględnij adresy IP portalu usługi Azure Wyszukiwanie poznawcze
Jeśli używasz Azure Portal do tworzenia indeksatora, logika portalu usługi Azure Wyszukiwanie poznawcze wymaga również dostępu do maszyny wirtualnej usługi SQL Azure podczas jej tworzenia. Adresy IP portalu usługi Azure Wyszukiwanie poznawcze można znaleźć za pomocą polecenia ping `stamp2.search.ext.azure.com` .

## <a name="next-steps"></a>Następne kroki
Za pomocą konfiguracji możesz teraz określić SQL Server na maszynie wirtualnej platformy Azure jako źródło danych dla indeksatora Wyszukiwanie poznawcze platformy Azure. Aby uzyskać więcej informacji [, zobacz łączenie Azure SQL Database z usługą Azure wyszukiwanie poznawcze przy użyciu indeksatorów](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) .