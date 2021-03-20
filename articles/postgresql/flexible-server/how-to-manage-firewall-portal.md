---
title: Zarządzanie regułami zapory — Azure Portal-Azure Database for PostgreSQL — elastyczny serwer
description: Utwórz reguły zapory dla Azure Database for PostgreSQL-elastyczny serwer przy użyciu Azure Portal i zarządzaj nimi
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 8c107b88f964d8e657d6833fc3a2e8425d9053ce
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90936921"
---
# <a name="create-and-manage-firewall-rules-for-azure-database-for-postgresql---flexible-server-using-the-azure-portal"></a>Utwórz reguły zapory dla Azure Database for PostgreSQL-elastyczny serwer przy użyciu Azure Portal i zarządzaj nimi

> [!IMPORTANT]
> Serwer elastyczny Azure Database for PostgreSQL jest obecnie w publicznej wersji zapoznawczej.

Serwer elastyczny Azure Database for PostgreSQL obsługuje dwa typy wzajemnie wykluczających się metod łączności sieciowej w celu nawiązania połączenia z serwerem elastycznym. Te dwie opcje są następujące:

* Dostęp publiczny (dozwolone adresy IP)
* Dostęp prywatny (integracja z siecią wirtualną)

W tym artykule skupmy się na tworzeniu serwera PostgreSQL z **dostępem publicznym (dozwolone adresy IP)** przy użyciu Azure Portal i zapewnią przegląd zarządzania regułami zapory po utworzeniu elastycznego serwera. W przypadku *dostępu publicznego (dozwolone adresy IP)* połączenia z serwerem PostgreSQL są ograniczone tylko do dozwolonych adresów IP. Adresy IP klienta muszą być dozwolone w regułach zapory. Aby dowiedzieć się więcej na ten temat, zobacz [dostęp publiczny (dozwolone adresy IP)](./concepts-networking.md#public-access-allowed-ip-addresses). Reguły zapory można definiować w momencie tworzenia serwera (zalecane), ale można je również dodać później. W tym artykule omówiono sposób tworzenia reguł zapory i zarządzania nimi przy użyciu dostępu publicznego (dozwolone adresy IP).

## <a name="create-a-firewall-rule-when-creating-a-server"></a>Tworzenie reguły zapory podczas tworzenia serwera

1. Wybierz pozycję **Utwórz zasób** (+) w lewym górnym rogu portalu.
2. Wybierz pozycję **bazy danych**  >  **Azure Database for PostgreSQL**. Możesz również wprowadzić **PostgreSQL** w polu wyszukiwania, aby znaleźć usługę.
3. Wybierz pozycję **elastyczny serwer** jako opcję wdrożenia.
4. Wypełnij formularz **podstawy** .
5. Przejdź do karty **Sieć** , aby skonfigurować sposób nawiązywania połączenia z serwerem.
6. W polu **Metoda łączności** wybierz pozycję *dostęp publiczny (dozwolone adresy IP)*. Aby utworzyć **reguły zapory**, określ nazwę reguły zapory oraz pojedynczy adres IP lub zakres adresów. Jeśli chcesz ograniczyć regułę do pojedynczego adresu IP, wpisz ten sam adres w polu początkowy adres IP i końcowy adres IP. Otwarcie zapory umożliwia administratorom, użytkownikom i aplikacjom dostęp do dowolnych baz danych na serwerze PostgreSQL, do których mają prawidłowe poświadczenia.
   > [!Note]
   > Azure Database for PostgreSQL — serwer elastyczny tworzy zaporę na poziomie serwera. Uniemożliwia ona zewnętrznym aplikacjom i narzędziom łączenie się z serwerem i wszelkimi bazami danych na tym serwerze, chyba że utworzysz regułę otwierającą zaporę dla konkretnych adresów IP.
7. Wybierz pozycję **Przegląd + Utwórz** , aby przejrzeć konfigurację elastycznego serwera.
8.  Wybierz pozycję **Utwórz**, aby aprowizować serwer. Inicjowanie obsługi może potrwać kilka minut.

## <a name="create-a-firewall-rule-after-server-is-created"></a>Utwórz regułę zapory po utworzeniu serwera

1. W [Azure Portal](https://portal.azure.com/)wybierz serwer elastyczny Azure Database for PostgreSQL, na którym chcesz dodać reguły zapory.
2. Na stronie serwer elastyczny w polu Nagłówek **Ustawienia** kliknij pozycję **Sieć** , aby otworzyć stronę sieci dla elastycznego serwera.

   <!--![Azure portal - click Connection Security](./media/howto-manage-firewall-portal/1-connection-security.png)-->

3. Kliknij pozycję **Dodaj bieżący adres IP klienta** w regułach zapory. Spowoduje to automatyczne utworzenie reguły zapory z publicznym adresem IP komputera, tak jak to zostało postrzegane przez system Azure.

   <!--![Azure portal - click Add My IP](./media/howto-manage-firewall-portal/2-add-my-ip.png)-->

4. Sprawdź swój adres IP przed zapisaniem konfiguracji. W niektórych sytuacjach adres IP zaobserwowany przez Azure Portal różni się od adresu IP używanego podczas uzyskiwania dostępu do Internetu i serwerów platformy Azure. W związku z tym może zajść potrzeba zmiany początkowego adresu IP i końcowego adresu IP, aby zapewnić działanie reguły zgodnie z oczekiwaniami.

   Aby sprawdzić własny adres IP, możesz użyć wyszukiwarki lub innego narzędzia online. Na przykład wyszukaj ciąg "co to jest mój adres IP".

   <!--![Bing search for What is my IP](./media/howto-manage-firewall-portal/3-what-is-my-ip.png)-->

5. Dodaj dodatkowe zakresy adresów. W regułach zapory dla serwera o Azure Database for PostgreSQL elastycznym można określić pojedynczy adres IP lub zakres adresów. Jeśli chcesz ograniczyć regułę do pojedynczego adresu IP, wpisz ten sam adres w polu początkowy adres IP i końcowy adres IP. Otwarcie zapory umożliwia administratorom, użytkownikom i aplikacjom dostęp do dowolnych baz danych na serwerze PostgreSQL, do których mają prawidłowe poświadczenia.

   <!--![Azure portal - firewall rules](./media/howto-manage-firewall-portal/4-specify-addresses.png)-->

6. Kliknij przycisk **Zapisz** na pasku narzędzi, aby zapisać tę regułę zapory. Poczekaj na potwierdzenie, że aktualizacja reguł zapory zakończyła się pomyślnie.

   <!--![Azure portal - click Save](./media/howto-manage-firewall-portal/5-save-firewall-rule.png)-->

## <a name="connecting-from-azure"></a>Łączenie z platformy Azure

Możesz włączyć zasoby lub aplikacje wdrożone na platformie Azure, aby nawiązać połączenie z serwerem elastycznym. Obejmuje to aplikacje sieci Web hostowane w Azure App Service, uruchomione na maszynie wirtualnej platformy Azure, Azure Data Factory bramy zarządzania danymi i wiele innych. 

Gdy aplikacja w ramach platformy Azure próbuje nawiązać połączenie z serwerem, Zapora sprawdzi, czy połączenia platformy Azure są dozwolone. To ustawienie można włączyć, wybierając opcję **Zezwalaj na dostęp publiczny z usług i zasobów platformy Azure na platformie Azure do tego serwera** w portalu z karty **Sieć** , a następnie przycisk **Zapisz**.

Zasoby nie muszą znajdować się w tej samej sieci wirtualnej lub grupie zasobów dla reguły zapory w celu włączenia tych połączeń. Jeśli próba połączenia nie jest dozwolona, żądanie nie dociera do Azure Database for PostgreSQL-elastyczny serwer.

> [!IMPORTANT]
> Ta opcja konfiguruje zaporę w celu zezwalania na wszystkie połączenia z platformy Azure, w tym połączenia z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.
>
> Zalecamy wybranie **prywatnego dostępu (Integracja sieci wirtualnej)** , aby bezpiecznie uzyskać dostęp do elastycznego serwera.
>
## <a name="manage-existing-firewall-rules-through-the-azure-portal"></a>Zarządzanie istniejącymi regułami zapory za pomocą Azure Portal

Wykonaj następujące kroki, aby zarządzać regułami zapory.

- Aby dodać bieżący komputer, kliknij przycisk + **Dodaj bieżący adres IP klienta** w regułach zapory. Kliknij przycisk **Zapisz**, aby zapisać zmiany.
- Aby dodać kolejne adresy IP, wpisz nazwę reguły, początkowy adres IP i końcowy adres IP. Kliknij przycisk **Zapisz**, aby zapisać zmiany.
- Aby zmodyfikować istniejącą regułę, kliknij dowolne pole w regule i wprowadź zmiany. Kliknij przycisk **Zapisz**, aby zapisać zmiany.
- Aby usunąć istniejącą regułę, kliknij przycisk wielokropka [...], a następnie kliknij przycisk **Usuń** , aby usunąć regułę. Kliknij przycisk **Zapisz**, aby zapisać zmiany.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [sieci w Azure Database for PostgreSQL-elastycznym serwerze](./concepts-networking.md)
- Dowiedz się więcej o [regułach zapory serwera elastycznych Azure Database for PostgreSQL](./concepts-networking.md#public-access-allowed-ip-addresses)
- [Utwórz reguły zapory Azure Database for PostgreSQL i zarządzaj nimi za pomocą interfejsu wiersza polecenia platformy Azure](./how-to-manage-firewall-cli.md).