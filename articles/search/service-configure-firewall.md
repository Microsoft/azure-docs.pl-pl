---
title: Konfigurowanie zapory IP dla usługi Azure Wyszukiwanie poznawcze
titleSuffix: Azure Cognitive Search
description: Skonfiguruj zasady kontroli adresów IP, aby ograniczyć dostęp do usługi Azure Wyszukiwanie poznawcze.
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 67fa53e565de70d3da0b90e36a968b68d54d667f
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99573249"
---
# <a name="configure-ip-firewall-for-azure-cognitive-search"></a>Konfigurowanie zapory IP dla usługi Azure Wyszukiwanie poznawcze

Usługa Azure Wyszukiwanie poznawcze obsługuje reguły adresów IP na potrzeby obsługi zapory przychodzącej. Ten model zapewnia dodatkową warstwę zabezpieczeń usługi wyszukiwania podobną do reguł adresów IP, które znajdują się w grupie zabezpieczeń sieci wirtualnej platformy Azure. Za pomocą tych reguł IP można skonfigurować usługę wyszukiwania jako dostępną tylko z zatwierdzonego zestawu maszyn i/lub usług w chmurze. Dostęp do danych przechowywanych w usłudze wyszukiwania z tych zatwierdzonych zestawów maszyn i usług nadal będzie wymagał, aby obiekt wywołujący mógł przedstawić prawidłowy token autoryzacji.

> [!Important]
> Reguły adresów IP w usłudze Azure Wyszukiwanie poznawcze można skonfigurować przy użyciu Azure Portal lub [interfejsu API REST zarządzania w wersji 2020-03-13](/rest/api/searchmanagement/).

## <a name="configure-an-ip-firewall-using-the-azure-portal"></a><a id="configure-ip-policy"></a> Konfigurowanie zapory IP przy użyciu Azure Portal

Aby ustawić zasady kontroli dostępu IP w Azure Portal, przejdź do strony usługi Wyszukiwanie poznawcze platformy Azure i wybierz pozycję **Sieć** w menu nawigacji. Łączność sieciową punktu końcowego musi być **publiczna**. Jeśli łączność jest ustawiona na **prywatną**, dostęp do usługi wyszukiwania można uzyskać tylko za pośrednictwem prywatnego punktu końcowego.

![Zrzut ekranu przedstawiający sposób konfigurowania zapory IP w Azure Portal](./media/service-configure-firewall/azure-portal-firewall.png)

Azure Portal zapewnia możliwość określania adresów IP i zakresów adresów IP w formacie CIDR. Przykładem notacji CIDR jest 8.8.8.0/24, która reprezentuje adresy IP z zakresu od 8.8.8.0 do 8.8.8.255.

> [!NOTE]
> Po włączeniu zasad kontroli dostępu IP dla usługi Azure Wyszukiwanie poznawcze wszystkie żądania do płaszczyzny danych z maszyn spoza listy dozwolonych zakresów adresów IP zostaną odrzucone. Po skonfigurowaniu reguł IP niektóre funkcje Azure Portal są wyłączone. Będziesz mieć możliwość wyświetlania informacji o poziomie usług i zarządzania nimi, ale dostęp portalu do danych indeksu i różne składniki usługi, takie jak indeks, indeksator i definicje zestawu umiejętności, są ograniczone ze względów bezpieczeństwa. Jako alternatywę dla portalu można użyć [rozszerzenia vs Code](https://aka.ms/vscode-search) , aby korzystać z różnych składników usługi.

### <a name="requests-from-your-current-ip"></a>Żądania z bieżącego adresu IP

Aby uprościć programowanie, Azure Portal ułatwia identyfikowanie i Dodawanie adresu IP komputera klienckiego do listy dozwolonych. Aplikacje uruchomione na maszynie mogą następnie uzyskiwać dostęp do usługi Azure Wyszukiwanie poznawcze.

Portal automatycznie wykrywa adres IP klienta. Może to być adres IP klienta komputera lub bramy sieci. Pamiętaj o usunięciu tego adresu IP przed rozpoczęciem pracy w środowisku produkcyjnym.

Aby dodać bieżący adres IP do listy adresów IP, zaznacz pole wyboru **Dodaj swój klient**. Następnie wybierz pozycję **Zapisz**.

![Zrzut ekranu przedstawiający sposób konfigurowania ustawień zapory IP w celu zezwolenia na bieżący adres IP](./media/service-configure-firewall/enable-current-ip.png)

## <a name="troubleshoot-issues-with-an-ip-access-control-policy"></a><a id="troubleshoot-ip-firewall"></a>Rozwiązywanie problemów z zasadami kontroli dostępu IP

Problemy z zasadami kontroli dostępu IP można rozwiązać, korzystając z następujących opcji:

### <a name="azure-portal"></a>Azure Portal

Włączenie zasad kontroli dostępu IP dla usługi Azure Wyszukiwanie poznawcze blokuje wszystkie żądania z maszyn spoza listy dozwolonych zakresów adresów IP, w tym Azure Portal.  Będziesz mieć możliwość wyświetlania informacji o poziomie usług i zarządzania nimi, ale dostęp portalu do danych indeksu i różne składniki usługi, takie jak indeks, indeksator i definicje zestawu umiejętności, są ograniczone ze względów bezpieczeństwa. 

### <a name="sdks"></a>Zestawy SDK

Gdy uzyskujesz dostęp do usługi Azure Wyszukiwanie poznawcze przy użyciu zestawu SDK z maszyn, które nie znajdują się na liście dozwolonych, zostanie zwrócona ogólna odpowiedź z **zabronionym 403** , która nie zawiera żadnych dodatkowych informacji. Sprawdź listę dozwolonych adresów IP dla Twojego konta i upewnij się, że Zaktualizowano poprawną konfigurację dla usługi wyszukiwania.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do usługi wyszukiwania za pośrednictwem prywatnego linku, zobacz następujący artykuł:

* [Utwórz prywatny punkt końcowy dla bezpiecznego połączenia z platformą Azure Wyszukiwanie poznawcze](service-create-private-endpoint.md)