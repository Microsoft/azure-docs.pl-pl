---
title: Rozwiązanie VMware firmy Azure według CloudSimple — tabele zapory
description: Dowiedz się więcej na temat tabel i reguł zapory chmury prywatnej CloudSimple, w tym reguł domyślnych tworzonych w każdej tabeli zapory.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8c44c39f66a0a0161eea8a7e9656bbe0e3d1015c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88140874"
---
# <a name="firewall-tables-overview"></a>Tabele zapory — Omówienie

W tabeli zapory są wyświetlane reguły filtrowania ruchu sieciowego do i z zasobów w chmurze prywatnej. Tabele zapory można stosować do sieci VLAN/podsieci. Reguły kontrolują ruch sieciowy między siecią źródłową lub adresem IP a siecią docelową lub adresem IP.

## <a name="firewall-rules"></a>Reguły zapory

W poniższej tabeli opisano parametry w regule zapory.

| Właściwość | Szczegóły |
| ---------| --------|
| **Nazwa** | Nazwa, która jednoznacznie identyfikuje regułę zapory i jej przeznaczenie. |
| **Priority** | Liczba z zakresu od 100 do 4096, z 100 jest najwyższy priorytet. Reguły są przetwarzane w kolejności priorytetów. Gdy ruch napotyka zgodność z regułą, przetwarzanie reguł zostanie zatrzymane. W efekcie reguły o niższych priorytetach, które mają te same atrybuty co reguły o wyższych priorytetach nie są przetwarzane.  Należy zachować ostrożność, aby uniknąć reguł powodujących konflikt. |
| **Śledzenie stanu** | Śledzenie może być bezstanowe (Chmura prywatna, Internet lub sieć VPN) lub stanowa (Public IP).  |
| **Protokół** | Dostępne opcje to TCP lub UDP. Jeśli jest wymagany protokół ICMP, użyj dowolnego z nich. |
| **Kierunek** | Określa, czy reguła ma zastosowanie do ruchu przychodzącego, czy wychodzącego. |
| **Akcja** | Zezwalaj lub Odmów dla typu ruchu zdefiniowanego w regule. |
| **Element źródłowy** | Adres IP, bezklasowy blok routingu międzydomenowego (CIDR) (na przykład 10.0.0.0/24) lub dowolny.  Określenie zakresu, tagu usługi lub grupy zabezpieczeń aplikacji umożliwia utworzenie mniejszej liczby reguł zabezpieczeń. |
| **Port źródłowy** | Port, z którego pochodzi ruch sieciowy.  Można określić pojedynczy port lub zakres portów, taki jak 443 lub 8000-8080. Określenie zakresów umożliwia utworzenie mniejszej liczby reguł zabezpieczeń. |
| **Element docelowy** | Adres IP, bezklasowy blok routingu międzydomenowego (CIDR) (na przykład 10.0.0.0/24) lub dowolny.  Określenie zakresu, tagu usługi lub grupy zabezpieczeń aplikacji umożliwia utworzenie mniejszej liczby reguł zabezpieczeń.  |
| **Port docelowy** | Port, do którego przepływy ruchu sieciowego.  Można określić pojedynczy port lub zakres portów, taki jak 443 lub 8000-8080. Określenie zakresów umożliwia utworzenie mniejszej liczby reguł zabezpieczeń.|

### <a name="stateless"></a>Bezstanowe

Reguła bezstanowa sprawdza tylko pojedyncze pakiety i filtruje je na podstawie reguły.  
Do przepływu ruchu w odwrotnym kierunku mogą być wymagane dodatkowe reguły.  Użyj reguł bezstanowych dla ruchu między następującymi punktami:

* Podsieci chmur prywatnych
* Podsieć lokalna i podsieć chmury prywatnej
* Ruch internetowy z chmur prywatnych

### <a name="stateful"></a>Stanowe

 Reguła stanowa ma świadomość połączeń, które przechodzą przez nią. Rekord przepływu tworzony jest dla istniejących połączeń. Komunikacja jest dozwolona lub zablokowana na podstawie stanu połączenia z rekordu przepływu.  Użyj tego typu reguły dla publicznych adresów IP, aby filtrować ruch z Internetu.

### <a name="default-rules"></a>Reguły domyślne

W każdej tabeli zapory są tworzone następujące reguły domyślne.

|Priorytet|Nazwa|Śledzenie stanu|Kierunek|Typ ruchu|Protokół|Element źródłowy|Port źródłowy|Element docelowy|Port docelowy|Akcja|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65000|Zezwalaj na wszystkie-internetowe|Stanowe|Wychodzący|Publiczny adres IP lub ruch internetowy|Wszyscy|Dowolne|Dowolne|Dowolne|Dowolne|Zezwalaj|
|65001|Odmów — wszystko-z Internetu|Stanowe|Przychodzący|Publiczny adres IP lub ruch internetowy|Wszyscy|Dowolne|Dowolne|Dowolne|Dowolne|Zablokuj|
|65002|Zezwól na dostęp do sieci intranet|Bezstanowe|Wychodzący|Ruch wewnętrzny lub sieci VPN w chmurze prywatnej|Wszyscy|Dowolne|Dowolne|Dowolne|Dowolne|Zezwalaj|
|65003|Zezwalaj — wszystko-z-intranet|Bezstanowe|Przychodzący|Ruch wewnętrzny lub sieci VPN w chmurze prywatnej|Wszyscy|Dowolne|Dowolne|Dowolne|Dowolne|Zezwalaj|

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie tabel i reguł zapory](firewall.md)
