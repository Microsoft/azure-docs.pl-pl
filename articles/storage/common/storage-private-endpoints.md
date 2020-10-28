---
title: Używanie prywatnych punktów końcowych
titleSuffix: Azure Storage
description: Przegląd prywatnych punktów końcowych w celu bezpiecznego dostępu do kont magazynu z sieci wirtualnych.
services: storage
author: santoshc
ms.service: storage
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: santoshc
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 73fa295c0c0d30cb0797820baaf2a4b03a1b7c99
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92783457"
---
# <a name="use-private-endpoints-for-azure-storage"></a>Używanie prywatnych punktów końcowych usługi Azure Storage

Możesz użyć [prywatnych punktów końcowych](../../private-link/private-endpoint-overview.md) dla kont usługi Azure Storage, aby umożliwić klientom w sieci wirtualnej (VNET) bezpieczne uzyskiwanie dostępu do danych za pośrednictwem [prywatnego linku](../../private-link/private-link-overview.md). Prywatny punkt końcowy używa adresu IP z przestrzeni adresowej sieci wirtualnej dla usługi konta magazynu. Ruch sieciowy między klientami w sieci wirtualnej a kontem magazynu przechodzącym przez sieć wirtualną i prywatnym łączem w usłudze Microsoft szkielet Network, eliminując ekspozycję z publicznego Internetu.

Używanie prywatnych punktów końcowych dla konta magazynu pozwala:

- Zabezpiecz swoje konto magazynu, konfigurując zaporę magazynu do blokowania wszystkich połączeń w publicznym punkcie końcowym usługi Storage.
- Zwiększ bezpieczeństwo sieci wirtualnej (VNet), umożliwiając zablokowanie eksfiltracji danych w wirtualnej.
- Bezpiecznie łącz się z kontami magazynu z sieci lokalnych, które łączą się z siecią wirtualną przy użyciu [sieci VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) lub [Usługa expressroutes](../../expressroute/expressroute-locations.md) z prywatną komunikację równorzędną.

## <a name="conceptual-overview"></a>Omówienie pojęć

![Przegląd prywatnych punktów końcowych usługi Azure Storage](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

Prywatny punkt końcowy jest specjalnym interfejsem sieciowym dla usługi platformy Azure w [Virtual Network](../../virtual-network/virtual-networks-overview.md) (VNET). Utworzenie prywatnego punktu końcowego dla konta magazynu zapewnia bezpieczną łączność między klientami w sieci wirtualnej i magazynem. Do prywatnego punktu końcowego jest przypisany adres IP z zakresu adresów IP sieci wirtualnej. Połączenie między prywatnym punktem końcowym a usługą magazynu używa bezpiecznego linku prywatnego.

Aplikacje w sieci wirtualnej mogą bezproblemowo łączyć się z usługą magazynu za pośrednictwem prywatnego punktu końcowego, **używając tych samych parametrów połączenia i mechanizmów autoryzacji, które mogą być używane w inny sposób** . Prywatnych punktów końcowych można używać ze wszystkimi protokołami obsługiwanymi przez konto magazynu, w tym REST i SMB.

Prywatne punkty końcowe można utworzyć w podsieciach, które korzystają z [punktów końcowych usługi](../../virtual-network/virtual-network-service-endpoints-overview.md). Klienci w podsieci mogą w ten sposób łączyć się z jednym kontem magazynu przy użyciu prywatnego punktu końcowego, jednocześnie używając punktów końcowych usługi, aby uzyskiwać dostęp do innych osób.

Podczas tworzenia prywatnego punktu końcowego dla usługi magazynu w sieci wirtualnej, żądanie zgody jest wysyłane do zatwierdzenia przez właściciela konta magazynu. Jeśli użytkownik żądający utworzenia prywatnego punktu końcowego jest również właścicielem konta magazynu, to żądanie zgody jest automatycznie zatwierdzane.

Właściciele kont magazynu mogą zarządzać żądaniami zgody i prywatnymi punktami końcowymi za pomocą karty " *prywatne punkty końcowe* " dla konta magazynu w [Azure Portal](https://portal.azure.com).

> [!TIP]
> Aby ograniczyć dostęp do konta magazynu tylko za pomocą prywatnego punktu końcowego, należy skonfigurować zaporę magazynu w celu odmowy lub kontroli dostępu za pomocą publicznego punktu końcowego.

Możesz zabezpieczyć konto magazynu tak, aby akceptowało tylko połączenia z sieci wirtualnej, [konfigurując zaporę magazynu](storage-network-security.md#change-the-default-network-access-rule) tak, aby domyślnie nie zezwala na dostęp za pośrednictwem jego publicznego punktu końcowego. Nie musisz mieć reguły zapory, aby zezwalać na ruch z sieci wirtualnej, która ma prywatny punkt końcowy, ponieważ Zapora magazynu kontroluje dostęp tylko za pośrednictwem publicznego punktu końcowego. Prywatne punkty końcowe zamiast tego polegają na przepływie zgody na przyznanie podsieci dostępu do usługi magazynu.

### <a name="private-endpoints-for-azure-storage"></a>Prywatne punkty końcowe usługi Azure Storage

Podczas tworzenia prywatnego punktu końcowego należy określić konto magazynu i usługę magazynu, z którą nawiąże połączenie. Potrzebujesz osobnego prywatnego punktu końcowego dla każdej usługi magazynu na koncie magazynu, do której należy uzyskać dostęp, czyli [obiektów BLOB](../blobs/storage-blobs-overview.md), [Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md), [plików](../files/storage-files-introduction.md), [kolejek](../queues/storage-queues-introduction.md), [tabel](../tables/table-storage-overview.md)lub [statycznych witryn sieci Web](../blobs/storage-blob-static-website.md).

> [!TIP]
> Utwórz oddzielny prywatny punkt końcowy dla dodatkowego wystąpienia usługi Storage, aby uzyskać lepszą wydajność odczytu na kontach RA-GRS.

Aby uzyskać dostęp do odczytu do regionu pomocniczego z kontem magazynu skonfigurowanym dla magazynu geograficznie nadmiarowego, należy oddzielić prywatne punkty końcowe zarówno dla głównych, jak i dodatkowych wystąpień usługi. Nie musisz tworzyć prywatnego punktu końcowego dla wystąpienia dodatkowego do **pracy w trybie failover** . Prywatny punkt końcowy będzie automatycznie łączyć się z nowym wystąpieniem podstawowym po przejściu w tryb failover. Aby uzyskać więcej informacji na temat opcji nadmiarowości magazynu, zobacz [nadmiarowość usługi Azure Storage](storage-redundancy.md).

Aby uzyskać bardziej szczegółowe informacje na temat tworzenia prywatnego punktu końcowego dla konta magazynu, zapoznaj się z następującymi artykułami:

- [Połącz się prywatnie z kontem magazynu z poziomu środowiska konta magazynu w Azure Portal](../../private-link/tutorial-private-endpoint-storage-portal.md)
- [Utwórz prywatny punkt końcowy przy użyciu prywatnego centrum linków w Azure Portal](../../private-link/create-private-endpoint-portal.md)
- [Tworzenie prywatnego punktu końcowego przy użyciu interfejsu wiersza polecenia platformy Azure](../../private-link/create-private-endpoint-cli.md)
- [Tworzenie prywatnego punktu końcowego przy użyciu Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)

### <a name="connecting-to-private-endpoints"></a>Nawiązywanie połączenia z prywatnymi punktami końcowymi

Klienci w sieci wirtualnej korzystającej z prywatnego punktu końcowego powinni używać tych samych parametrów połączenia dla konta magazynu, ponieważ klienci nawiązują połączenie z publicznym punktem końcowym. Firma Microsoft korzysta z rozpoznawania nazw DNS, aby automatycznie kierować połączenia z sieci wirtualnej do konta magazynu za pośrednictwem prywatnego linku.

> [!IMPORTANT]
> Użyj tych samych parametrów połączenia, aby nawiązać połączenie z kontem magazynu za pomocą prywatnych punktów końcowych, jak w przeciwnym razie. Nie łącz się z kontem magazynu przy użyciu adresu URL poddomeny " *privatelink* ".

Utworzymy [prywatną strefę DNS](../../dns/private-dns-overview.md) dołączoną do sieci wirtualnej z domyślnymi aktualizacjami dla prywatnych punktów końcowych. Jeśli jednak używasz własnego serwera DNS, może być konieczne wprowadzenie dodatkowych zmian w konfiguracji DNS. W sekcji dotyczącej [zmian w systemie DNS](#dns-changes-for-private-endpoints) poniżej opisano aktualizacje wymagane dla prywatnych punktów końcowych.

## <a name="dns-changes-for-private-endpoints"></a>Zmiany w systemie DNS dla prywatnych punktów końcowych

Podczas tworzenia prywatnego punktu końcowego rekord zasobu CNAME DNS dla konta magazynu zostanie zaktualizowany do aliasu w poddomenie z prefiksem " *privatelink* ". Domyślnie tworzymy również [prywatną strefę DNS](../../dns/private-dns-overview.md), odpowiadającą poddomeną " *privatelink* ", z rekordem zasobów DNS dla prywatnych punktów końcowych.

Po rozwiązaniu adresu URL punktu końcowego magazynu spoza sieci wirtualnej z prywatnym punktem końcowym jest on rozpoznawany jako publiczny punkt końcowy usługi magazynu. Po rozwiązaniu problemu z siecią wirtualną, w której jest przechowywany prywatny punkt końcowy, adres URL punktu końcowego magazynu jest rozpoznawany jako adres IP prywatnego punktu końcowego.

W przykładzie przedstawionym powyżej, rekordy zasobów DNS dla konta magazynu "StorageAccountA", po rozwiązaniu spoza sieci wirtualnej obsługującej prywatny punkt końcowy, będą:

| Nazwa                                                  | Typ  | Wartość                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | \<storage service public endpoint\>                   |
| \<storage service public endpoint\>                   | A     | \<storage service public IP address\>                 |

Jak wspomniano wcześniej, można odmówić lub kontrolować dostęp dla klientów spoza sieci wirtualnej za pośrednictwem publicznego punktu końcowego przy użyciu zapory magazynu.

Rekordy zasobów DNS dla StorageAccountA, po rozwiązaniu przez klienta w sieci wirtualnej hostującym prywatnego punktu końcowego, będą:

| Nazwa                                                  | Typ  | Wartość                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | A     | klienta 10.1.1.5                                              |

Takie podejście umożliwia dostęp do konta magazynu **przy użyciu tych samych parametrów połączenia** dla klientów w sieci wirtualnej, które obsługują prywatne punkty końcowe, a także klientów spoza sieci wirtualnej.

Jeśli używasz niestandardowego serwera DNS w sieci, klienci muszą mieć możliwość rozpoznania nazwy FQDN dla punktu końcowego konta magazynu na prywatnym adresie IP punktu końcowego. Należy skonfigurować serwer DNS w celu delegowania poddomeny prywatnego linku do prywatnej strefy DNS dla sieci wirtualnej lub skonfigurować rekordy A dla " *StorageAccountA.privatelink.blob.Core.Windows.NET* " z prywatnym adresem IP punktu końcowego.

> [!TIP]
> W przypadku korzystania z niestandardowego lub lokalnego serwera DNS należy skonfigurować serwer DNS w celu rozpoznania nazwy konta magazynu w poddomenie "privatelink" do prywatnego adresu IP punktu końcowego. Można to zrobić przez delegowanie poddomeny "privatelink" do prywatnej strefy DNS sieci wirtualnej lub skonfigurowanie strefy DNS na serwerze DNS oraz dodanie rekordu A usługi DNS.

Zalecane nazwy stref DNS dla prywatnych punktów końcowych usług magazynu to:

| Usługa magazynu        | Nazwa strefy                            |
| :--------------------- | :----------------------------------- |
| Blob service           | `privatelink.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `privatelink.dfs.core.windows.net`   |
| Usługa plików           | `privatelink.file.core.windows.net`  |
| usługa kolejki          | `privatelink.queue.core.windows.net` |
| Table service          | `privatelink.table.core.windows.net` |
| Statyczne witryny sieci Web        | `privatelink.web.core.windows.net`   |

Aby uzyskać więcej informacji na temat konfigurowania własnego serwera DNS do obsługi prywatnych punktów końcowych, zapoznaj się z następującymi artykułami:

- [Rozpoznawanie nazw dla zasobów w sieciach wirtualnych platformy Azure](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [Konfiguracja DNS dla prywatnych punktów końcowych](../../private-link/private-endpoint-overview.md#dns-configuration)

## <a name="pricing"></a>Cennik

Aby uzyskać szczegółowe informacje o cenach, zobacz [Cennik usługi Azure Private link](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Znane problemy

Należy wziąć pod uwagę następujące znane problemy dotyczące prywatnych punktów końcowych usługi Azure Storage.

### <a name="copy-blob-support"></a>Kopiuj obsługę obiektów BLOB

Jeśli konto magazynu jest chronione przez zaporę, a konto jest dostępne za pomocą prywatnych punktów końcowych, to konto nie może stanowić źródła operacji [kopiowania obiektu BLOB](/rest/api/storageservices/copy-blob) .

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Ograniczenia dostępu do magazynu dla klientów w sieci wirtualnych z prywatnymi punktami końcowymi

Klienci w programie sieci wirtualnych z istniejącymi prywatnymi punktami końcowymi ograniczeniami podczas uzyskiwania dostępu do innych kont magazynu, które mają prywatne punkty końcowe. Na przykład załóżmy, że sieć wirtualna N1 ma prywatny punkt końcowy dla konta magazynu a1 dla magazynu obiektów BLOB. Jeśli konto magazynu a2 ma prywatny punkt końcowy w sieci wirtualnej N2 dla usługi BLOB Storage, klienci w sieci wirtualnej N1 muszą również uzyskać dostęp do usługi BLOB Storage na koncie a2 przy użyciu prywatnego punktu końcowego. Jeśli konto magazynu a2 nie ma żadnych prywatnych punktów końcowych usługi BLOB Storage, klienci w sieci wirtualnej N1 mogą uzyskać dostęp do magazynu obiektów BLOB na tym koncie bez prywatnego punktu końcowego.

To ograniczenie jest wynikiem zmian wprowadzonych w systemie DNS, gdy konto a2 tworzy prywatny punkt końcowy.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>Reguły sieciowej grupy zabezpieczeń dla podsieci z prywatnymi punktami końcowymi

Obecnie nie można skonfigurować zasad [sieciowych grup zabezpieczeń](../../virtual-network/network-security-groups-overview.md) (sieciowej grupy zabezpieczeń) i tras zdefiniowanych przez użytkownika dla prywatnych punktów końcowych. Reguły sieciowej grupy zabezpieczeń stosowane do podsieci obsługującej prywatny punkt końcowy są stosowane tylko do innych punktów końcowych (np. kart sieciowych) niż prywatny punkt końcowy. Ograniczone obejście tego problemu polega na implementacji reguł dostępu dla prywatnych punktów końcowych w podsieciach źródłowych, chociaż takie podejście może wymagać wyższego obciążenia zarządzania.

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](storage-network-security.md)
- [Zalecenia dotyczące zabezpieczeń usługi BLOB Storage](../blobs/security-recommendations.md)