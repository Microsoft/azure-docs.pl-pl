---
title: 'Samouczek: Konfigurowanie sieci w klastrze usługi Azure FXT Edge'
description: Jak dostosować ustawienia sieci po utworzeniu klastra usługi Azure FXT Edge
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 4ce7ffc66e0b6164b2e4ca9725b3f26403292a4a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92220775"
---
# <a name="tutorial-configure-the-clusters-network-settings"></a>Samouczek: Konfigurowanie ustawień sieciowych klastra

Przed użyciem nowo utworzonego klastra usługi Azure FXT Edge, należy sprawdzić i dostosować kilka ustawień sieciowych dla przepływu pracy.

W tym samouczku wyjaśniono ustawienia sieci, które mogą być konieczne do dostosowania nowego klastra.

Dowiesz się:

> [!div class="checklist"]
>
> * Jakie ustawienia sieciowe mogą wymagać aktualizacji po utworzeniu klastra
> * Które przypadki użycia usługi Azure FXT Edge wymagają serwera AD lub serwera DNS
> * Jak skonfigurować serwer DNS z działaniem okrężnym (RRDNS) w celu automatycznego równoważenia obciążenia żądań klientów do klastra FXT

Czas potrzebny na wykonanie tych czynności zależy od liczby zmian konfiguracji wymaganych w systemie:

* Jeśli musisz tylko przeczytać ten samouczek i sprawdzić kilka ustawień, powinien upłynąć od 10 do 15 minut.
* Jeśli musisz skonfigurować system DNS działający w trybie okrężnym, zadanie może trwać godzinę lub dłużej.

## <a name="adjust-network-settings"></a>Dostosuj ustawienia sieci

Kilka zadań związanych z siecią jest częścią konfiguracji nowego klastra usługi Azure FXT Edge. Sprawdź tę listę i zdecyduj, które z nich mają zastosowanie do systemu.

Aby dowiedzieć się więcej na temat ustawień sieci dla klastra, przeczytaj temat [Konfigurowanie usług sieciowych](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html) w przewodniku konfigurowania klastra.

* Konfigurowanie systemu DNS z działaniem okrężnym dla sieci przeznaczonej dla klientów (opcjonalnie)

  Równoważyć obciążenie ruchem klastra przez skonfigurowanie systemu DNS zgodnie z opisem w temacie [Konfigurowanie usługi DNS dla klastra FXT Edge](#configure-dns-for-load-balancing).

* Weryfikuj Ustawienia NTP

* Skonfiguruj pliki do pobrania dla Active Directory i nazwy użytkownika/grupy (w razie konieczności)

  Jeśli hosty sieci używają Active Directory lub innego rodzaju zewnętrznej usługi katalogowej, należy zmodyfikować konfigurację usług katalogowych klastra, aby skonfigurować sposób, w jaki klaster pobiera informacje o nazwie użytkownika i grupie.   >  Aby uzyskać szczegółowe informacje, przeczytaj temat **usługi katalog** klastra w przewodniku konfigurowania klastra.

  Jeśli chcesz obsługiwać protokół SMB, wymagany jest serwer usługi AD. Skonfiguruj usługi AD przed rozpoczęciem konfigurowania protokołu SMB.

* Definiuj sieci VLAN (opcjonalnie)
  
  Skonfiguruj wszelkie dodatkowe sieci VLAN potrzebne przed zdefiniowaniem vservers i globalnej przestrzeni nazw klastra. Aby dowiedzieć się więcej, zapoznaj się z tematem [Praca z sieciami VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) w przewodniku konfigurowania klastra.

* Skonfiguruj serwery proxy (w razie konieczności)

  Jeśli klaster używa serwera proxy w celu uzyskania dostępu do adresów zewnętrznych, wykonaj następujące kroki, aby go skonfigurować:

  1. Definiowanie serwera proxy na stronie ustawień **konfiguracji serwera proxy**
  1. Zastosuj konfigurację serwera proxy na   >  stronie **Ogólne ustawienia** klastra lub na stronie **szczegółów podstawowego pliku** .
  
  Aby uzyskać więcej informacji, zapoznaj się z tematem [Korzystanie z serwerów proxy sieci Web](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/proxy_overview.html) w podręczniku konfiguracji klastra.

* Przekaż [certyfikaty szyfrowania](#encryption-certificates) dla klastra do użycia (opcjonalnie)

### <a name="encryption-certificates"></a>Certyfikaty szyfrowania

Klaster programu FXT Edge używa certyfikatów X. 509 dla następujących funkcji:

* Aby zaszyfrować ruch administrowania klastrem

* Aby uwierzytelnić się w imieniu klienta na serwerach KMIP innych firm

* Weryfikowanie certyfikatów serwera dostawcy chmury

Jeśli konieczne jest przekazanie certyfikatów do klastra, użyj   >  strony ustawienia **certyfikatów** klastra. Szczegóły znajdują się na stronie [> certyfikatów](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_certificates.html) w przewodniku konfigurowania klastra.

Aby zaszyfrować komunikację z zarządzaniem klastrami, użyj   >  strony **Ogólne ustawienia konfiguracji** klastra, aby wybrać certyfikat do użycia na potrzeby administracyjnego protokołu TLS.

> [!Note]
> Klucze dostępu do usługi w chmurze są przechowywane przy użyciu strony Konfiguracja **poświadczeń w chmurze** . Powyżej przedstawiono przykładową sekcję [Dodawanie podstawowego pliku](fxt-add-storage.md#add-a-core-filer) Szczegółowe informacje znajdują się [w sekcji Przewodnik po konfiguracji](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_credentials.html) klastra.

## <a name="configure-dns-for-load-balancing"></a>Konfigurowanie usługi DNS na potrzeby równoważenia obciążenia

W tej części objaśniono podstawowe informacje dotyczące konfigurowania systemu DNS w trybie okrężnym do dystrybucji obciążenia klienta między wszystkimi adresami IP skierowanymi na klienta w klastrze usługi FXT Edge.

### <a name="decide-whether-or-not-to-use-dns"></a>Zdecyduj, czy należy używać systemu DNS

Równoważenie obciążenia jest zawsze zalecane, ale nie trzeba używać systemu DNS. Na przykład w przypadku niektórych typów przepływów pracy klient może być bardziej zrozumiały do przypisywania adresów IP klastra nawet między klientami podczas instalacji klastra. Niektóre metody są opisane w artykule [Instalowanie klastra](fxt-mount-clients.md).

Należy pamiętać o tym, czy należy używać serwera DNS:

* Jeśli system jest dostępny tylko dla klientów NFS, system DNS nie jest wymagany. Można określić wszystkie adresy sieciowe przy użyciu liczbowych adresów IP.

* Jeśli system obsługuje dostęp do protokołu SMB (CIFS), wymagany jest system DNS, ponieważ należy określić domenę DNS dla serwera Active Directory.

* System DNS jest wymagany, aby można było używać uwierzytelniania Kerberos.

### <a name="round-robin-dns-configuration-details"></a>Szczegóły konfiguracji DNS z działaniem okrężnym

Gdy klienci uzyskują dostęp do klastra, usługa RRDNS automatycznie równoważy swoje żądania między wszystkimi dostępnymi interfejsami.

W celu uzyskania optymalnej wydajności Skonfiguruj serwer DNS, aby obsługiwał adresy klastra dostępne dla klientów, jak pokazano na poniższym diagramie.

Vserver klastra jest pokazywany po lewej stronie, a adresy IP są wyświetlane w centrum i po prawej stronie. Skonfiguruj każdy punkt dostępu klienta zawierający rekordy i wskaźniki, jak pokazano.

![Diagram DNS działania okrężnego klastra — szczegółowe łącze do tekstu alternatywnego następuje po ](media/fxt-cluster-config/fxt-rrdns-diagram.png) 
 [szczegółowym opisie](https://azure.github.io/Avere/legacy/Azure-FXT-EdgeFilerDNSconfiguration-alt-text.html) obrazu

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

### <a name="enable-dns-in-the-cluster"></a>Włączanie usługi DNS w klastrze

Określ serwer DNS, którego klaster używa na   >  stronie ustawień **sieci administracyjnej** klastra. Ustawienia na tej stronie obejmują:

* Adres serwera DNS
* Nazwa domeny DNS
* Domeny wyszukiwania DNS

Aby uzyskać więcej informacji, zapoznaj się z [ustawieniami DNS](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) w podręczniku konfiguracji klastra.

## <a name="next-steps"></a>Następne kroki

Jest to ostatni podstawowy krok konfiguracji klastra usługi Azure FXT Edge.

* Zapoznaj się z diodami LED systemu i innymi wskaźnikami w temacie [monitorowanie stanu sprzętu](fxt-monitor.md).
* Dowiedz się więcej o tym, jak klienci powinni instalować klaster plików FXT Edge w [instalacji klastra](fxt-mount-clients.md).
* Aby uzyskać więcej informacji na temat obsługi klastra FXT Edge i zarządzania nim, zobacz [Przewodnik po konfiguracji klastra](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html).
