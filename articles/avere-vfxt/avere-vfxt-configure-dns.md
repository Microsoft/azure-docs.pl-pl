---
title: Avere vFXT DNS — Azure
description: Konfigurowanie serwera DNS na potrzeby równoważenia obciążenia z działaniem okrężnym z avere vFXT dla platformy Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 81b53904f85e2ac936195b1e39d7586fd1d47524
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96009028"
---
# <a name="avere-cluster-dns-configuration"></a>Konfiguracja DNS klastra Avere

W tej sekcji objaśniono podstawowe informacje dotyczące konfigurowania systemu DNS na potrzeby równoważenia obciążenia klastra avere vFXT.

Ten dokument nie *zawiera* instrukcji dotyczących KONFIGUROWANIA serwera DNS i zarządzania nim w środowisku platformy Azure.

Zamiast korzystać z usługi DNS w trybie okrężnym do równoważenia obciążenia klastra vFXT na platformie Azure, należy rozważyć użycie metod ręcznych w celu przypisywania adresów IP nawet wśród klientów po ich zainstalowaniu. Kilka metod opisano w temacie [Instalowanie klastra avere](avere-vfxt-mount-clients.md).

Należy pamiętać o tym, czy należy używać serwera DNS:

* Jeśli system jest dostępny tylko dla klientów NFS, korzystanie z systemu DNS nie jest wymagane — można określić wszystkie adresy sieciowe przy użyciu liczbowych adresów IP.

* Jeśli system obsługuje dostęp do protokołu SMB (CIFS), wymagany jest system DNS, ponieważ należy określić domenę DNS dla serwera Active Directory.

* System DNS jest wymagany, aby można było używać uwierzytelniania Kerberos.

## <a name="load-balancing"></a>Równoważenie obciążenia

Aby rozpowszechnić ogólne obciążenie, należy skonfigurować domenę DNS tak, aby korzystała z dystrybucji obciążenia z działaniem okrężnym dla adresów IP skierowanych na klienta.

## <a name="configuration-details"></a>Szczegóły konfiguracji

Gdy klienci uzyskują dostęp do klastra, usługa RRDNS automatycznie równoważy swoje żądania między wszystkimi dostępnymi interfejsami.

W celu uzyskania optymalnej wydajności Skonfiguruj serwer DNS, aby obsługiwał adresy klastra dostępne dla klientów, jak pokazano na poniższym diagramie.

Vserver klastra jest pokazywany po lewej stronie, a adresy IP są wyświetlane w centrum i po prawej stronie. Skonfiguruj każdy punkt dostępu klienta zawierający rekordy i wskaźniki, jak pokazano.

![Diagram DNS działania okrężnego klastra avere](media/avere-vfxt-rrdns-diagram.png)
<!--- separate text description file provided  [diagram text description](avere-vfxt-rrdns-alt-text.md) -->

Każdy adres IP skierowany na klienta musi mieć unikatową nazwę do użytku wewnętrznego w klastrze. (Na tym diagramie adresy IP klientów mają nazwę VS1-Client-IP-* dla jasności, ale w środowisku produkcyjnym należy raczej używać czegoś bardziej zwięzłego, takiego jak klient *).

Klienci instalują klaster przy użyciu nazwy vserver jako argumentu serwera.

Zmodyfikuj plik serwera DNS ``named.conf`` w celu ustawienia kolejności cyklicznej dla zapytań do vserver. Ta opcja zapewnia, że wszystkie dostępne wartości są przetwarzane przez. Dodaj następującą instrukcję:

```
options {
    rrset-order {
        class IN A name "vserver1.example.com" order cyclic;
    };
};
```

Następujące ``nsupdate`` polecenia zapewniają przykład prawidłowego skonfigurowania usługi DNS:

```
update add vserver1.example.com. 86400 A 10.0.0.10
update add vserver1.example.com. 86400 A 10.0.0.11
update add vserver1.example.com. 86400 A 10.0.0.12
update add vs1-client-IP-10.example.com. 86400 A 10.0.0.10
update add vs1-client-IP-11.example.com. 86400 A 10.0.0.11
update add vs1-client-IP-12.example.com. 86400 A 10.0.0.12
update add 10.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-10.example.com
update add 11.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-11.example.com
update add 12.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-12.example.com
```

## <a name="cluster-dns-settings"></a>Ustawienia DNS klastra

Określ serwer DNS, którego używa klaster vFXT na   >  stronie ustawień **sieci administracyjnej** klastra. Ustawienia na tej stronie obejmują:

* Adres serwera DNS
* Nazwa domeny DNS
* Domeny wyszukiwania DNS

Aby uzyskać więcej informacji na temat korzystania z tej strony, Przeczytaj [Ustawienia DNS](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) w przewodniku konfigurowania klastra avere.
