---
title: Używanie prywatnych punktów końcowych w celu bezpiecznego dostępu do usługi kontrolą
description: W tym artykule opisano sposób konfigurowania prywatnego punktu końcowego dla konta usługi kontrolą
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 03/02/2021
ms.openlocfilehash: 09fa10e7f7751321601c5c4871b2cf36ccf6f01f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "104720894"
---
# <a name="use-private-endpoints-for-your-purview-account"></a>Używanie prywatnych punktów końcowych dla konta usługi kontrolą

Możesz użyć prywatnych punktów końcowych dla kont usługi kontrolą, aby umożliwić klientom i użytkownikom w sieci wirtualnej (VNet) bezpieczne uzyskiwanie dostępu do wykazu za pośrednictwem prywatnego linku. Prywatny punkt końcowy używa adresu IP z przestrzeni adresowej sieci wirtualnej dla konta kontrolą. Ruch sieciowy między klientami w sieci wirtualnej a kontem usługi kontrolą przechodzi przez sieć wirtualną oraz łącze prywatne przez sieć szkieletową firmy Microsoft, eliminując ekspozycję z publicznego Internetu.

## <a name="create-purview-account-with-a-private-endpoint"></a>Utwórz konto kontrolą za pomocą prywatnego punktu końcowego

1. Przejdź do [Azure Portal](https://portal.azure.com) , a następnie do konta kontrolą.

1. Wypełnij podstawowe informacje i ustaw metodę połączenia na prywatny punkt końcowy na karcie **Sieć** . Skonfiguruj prywatne punkty końcowe pozyskiwania, podając szczegóły dotyczące **subskrypcji, sieci wirtualnej i podsieci** , które chcesz sparować z prywatnym punktem końcowym.

    > [!NOTE]
    > Utwórz prywatny punkt końcowy pozyskiwania tylko wtedy, gdy zamierzasz włączyć izolację sieci na potrzeby kompleksowych scenariuszy skanowania zarówno dla źródła platformy Azure, jak i źródeł lokalnych. Obecnie nie obsługujemy prywatnych punktów końcowych pozyskiwania pracy z źródłami AWS.

    :::image type="content" source="media/catalog-private-link/create-pe-azure-portal.png" alt-text="Tworzenie prywatnego punktu końcowego w Azure Portal":::

1. Opcjonalnie możesz również skonfigurować **strefę prywatna strefa DNS** dla każdego prywatnego punktu końcowego pozyskiwania.

1. Kliknij przycisk Dodaj, aby dodać prywatny punkt końcowy dla konta usługi kontrolą.

1. Na stronie Tworzenie prywatnego punktu końcowego Ustaw kontrolą zasobów podrzędnych na **konto**, wybierz sieć wirtualną i podsieć, a następnie wybierz strefę prywatna strefa DNS, w której zostanie zarejestrowany system DNS (można również użyć własnych serwerów DNS lub utworzyć rekordy DNS przy użyciu plików hosta na maszynach wirtualnych).

    :::image type="content" source="media/catalog-private-link/create-pe-account.png" alt-text="Opcje tworzenia prywatnych punktów końcowych":::

1. Wybierz przycisk **OK**.

1. Wybierz pozycję **Przejrzyj i utwórz**. Nastąpi przekierowanie do strony Przeglądanie i tworzenie, na której platforma Azure zweryfikuje konfigurację.

1. Po wyświetleniu komunikatu Sprawdzanie poprawności zakończone powodzeniem kliknij przycisk **Utwórz**.

    :::image type="content" source="media/catalog-private-link/validation-passed.png" alt-text="Weryfikacja została przeniesiona w celu utworzenia konta":::

## <a name="create-a-private-endpoint-for-the-purview-web-portal"></a>Tworzenie prywatnego punktu końcowego dla portalu sieci Web kontrolą

1. Przejdź do właśnie utworzonego konta kontrolą, a następnie wybierz pozycję połączenia prywatnego punktu końcowego w sekcji Ustawienia.

1. Kliknij pozycję + prywatny punkt końcowy, aby utworzyć nowy prywatny punkt końcowy.

    :::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="Utwórz prywatny punkt końcowy portalu":::

1. Wypełnij podstawowe informacje.

1. Na karcie zasób wybierz typ zasobu, który ma być typem **Microsoft. kontrolą/accounts**.

1. Wybierz zasób, który ma zostać nowo utworzonym kontem kontrolą i wybierz docelowy zasób podrzędny do **portalu**.
    :::image type="content" source="media/catalog-private-link/pe-portal-details.png" alt-text="Szczegóły dotyczące prywatnego punktu końcowego portalu":::

1. Wybierz strefę sieci wirtualnej i Prywatna strefa DNS na karcie Konfiguracja. Przejdź do strony Podsumowanie, a następnie kliknij przycisk **Utwórz** , aby utworzyć prywatny punkt końcowy portalu.

## <a name="enabling-access-to-azure-active-directory"></a>Włączanie dostępu do Azure Active Directory

> [!NOTE]
> Jeśli maszyna wirtualna, Brama sieci VPN lub Brama wirtualnej sieci równorzędnej ma publiczny dostęp do Internetu, może uzyskać dostęp do portalu kontrolą i konta kontrolą z prywatnymi punktami końcowymi, a nie musisz postępować zgodnie z pozostałymi instrukcjami poniżej. Jeśli sieć prywatna ma reguły sieciowej grupy zabezpieczeń ustawione tak, aby odmówić całego publicznego ruchu internetowego, należy dodać reguły umożliwiające dostęp do usługi AAD. Postępuj zgodnie z poniższymi instrukcjami.

Poniższe instrukcje dotyczą bezpiecznego uzyskiwania dostępu do kontrolą z maszyny wirtualnej platformy Azure. Należy postępować podobnie, jeśli korzystasz z sieci VPN lub innych bram wirtualnych sieci równorzędnych.

1. Przejdź do maszyny wirtualnej w Azure Portal, wybierz kartę Sieć w sekcji Ustawienia. Następnie wybierz pozycję reguły portów wychodzących i kliknij pozycję Dodaj regułę portu wychodzącego.

   :::image type="content" source="media/catalog-private-link/outbound-rule-add.png" alt-text="Dodawanie reguły ruchu wychodzącego":::

2. W bloku Dodaj regułę portu wychodzącego wybierz pozycję *Lokalizacja docelowa* , która ma być tagiem usługi, docelowa wersja znacznika usługi to **usługi azureactivedirectory**, docelowy zakres portów do *, Akcja do zezwolenia, **priorytet powinien być wyższy niż reguła, która odrzuciła cały ruch internetowy**. Utwórz regułę.

   :::image type="content" source="media/catalog-private-link/outbound-rule-details.png" alt-text="Dodawanie szczegółów reguły ruchu wychodzącego":::

3. Wykonaj te same czynności, aby utworzyć kolejną regułę zezwalającą na tag usługi "**AzureResourceManager**". Jeśli potrzebujesz dostępu do Azure Portal, możesz również dodać regułę dla tagu usługi "*AzurePortal*".

4. Połącz się z maszyną wirtualną, Otwórz przeglądarkę, przejdź do konsoli przeglądarki (Ctrl + Shift + J) i przejdź do karty sieć, aby monitorować żądania sieciowe. Wprowadź web.purview.azure.com w polu adres URL i spróbuj zalogować się przy użyciu poświadczeń usługi AAD. Prawdopodobnie logowanie może zakończyć się niepowodzeniem i na karcie Sieć w konsoli zobaczysz, że w usłudze AAD zostanie wyświetlona próba uzyskania dostępu do aadcdn.msauth.net, ale jest to możliwe.

   :::image type="content" source="media/catalog-private-link/login-fail.png" alt-text="Szczegóły niepowodzenia logowania":::

5. W takim przypadku Otwórz wiersz polecenia na maszynie wirtualnej i Wyślij polecenie ping do tego adresu URL (aadcdn.msauth.net), uzyskaj jego adres IP, a następnie Dodaj regułę portu wychodzącego dla adresu IP w regułach zabezpieczeń sieci maszyny wirtualnej. Ustaw wartość miejsce docelowe na adres IP i ustaw docelowe adresy IP na aadcdn IP. Ze względu na moduł równoważenia obciążenia i usługi Traffic Manager adres IP sieci CDN usługi AAD może być dynamiczny. Po otrzymaniu adresu IP lepiej jest dodać go do pliku hosta maszyny wirtualnej w celu wymuszenia odwiedzenia przez przeglądarkę tego adresu IP w celu uzyskania usługi AAD CDN.

   :::image type="content" source="media/catalog-private-link/ping.png" alt-text="Testuj polecenie ping":::

   :::image type="content" source="media/catalog-private-link/aadcdn-rule.png" alt-text="Reguła usługi CDN w usłudze AAD":::

6. Po utworzeniu nowej reguły Wróć do maszyny wirtualnej i spróbuj ponownie wykonać logowanie przy użyciu poświadczeń usługi AAD. Jeśli logowanie powiedzie się, Portal kontrolą jest gotowy do użycia. Jednak w niektórych przypadkach usługi AAD przekierują się do innych domen w celu zalogowania się na podstawie typu konta klienta. Na przykład w przypadku konta live.com usługa AAD przekierowuje do usługi live.com w celu zalogowania, a następnie te żądania byłyby blokowane ponownie. W przypadku kont pracowników firmy Microsoft usługi AAD będą uzyskiwać dostęp do usługi msft.sts.microsoft.com na potrzeby informacji logowania. Sprawdź pozycję żądania sieci na karcie Sieć przeglądarki, aby sprawdzić, które żądania są blokowane, Powtórz poprzedni krok, aby uzyskać adres IP i dodać reguły portów wychodzących w sieciowej grupie zabezpieczeń, aby zezwolić na żądania dla tego adresu IP (jeśli to możliwe, Dodaj adres URL i IP do pliku hosta maszyny wirtualnej w celu rozwiązania rozpoznawania nazw DNS). Jeśli znasz dokładnie zakresy adresów IP domeny logowania, możesz je również bezpośrednio dodać do reguł sieci.

7. Teraz Zaloguj się do usługi AAD. Portal kontrolą zostanie załadowany pomyślnie, ale zostanie wyświetlona lista wszystkich kont kontrolą, ponieważ będzie ona mogła uzyskać dostęp tylko do określonego konta kontrolą. Wprowadź wartość *Web. kontrolą. Azure. com/Resource/{PurviewAccountName}* , aby bezpośrednio odwiedzać konto kontrolą, dla którego pomyślnie skonfigurowano prywatny punkt końcowy.
 
## <a name="ingestion-private-endpoints-and-scanning-sources-in-private-networks-vnets-and-behind-private-endpoints"></a>Prywatne punkty końcowe pozyskiwania i skanowania źródeł w sieciach prywatnych, sieci wirtualnych i za prywatne punkty końcowe

Aby zapewnić izolację sieci dla metadanych przepływających ze źródła, które jest skanowane do kontrolą DataMap, należy wykonać następujące czynności:
1. Włącz **prywatny punkt końcowy** pozyskiwania, wykonując kroki opisane w [tej](#creating-an-ingestion-private-endpoint) sekcji
1. Skanuj Źródło przy użyciu samodzielnego środowiska **IR**.
 
    1. Wszystkie lokalne typy źródeł, takie jak SQL Server, Oracle, SAP i inne, są obecnie obsługiwane tylko za pośrednictwem samodzielnych skanów opartych na środowisku IR. Własne środowisko IR musi działać w sieci prywatnej, a następnie być połączone za pomocą komunikacji równorzędnej z siecią wirtualną na platformie Azure. Sieć wirtualna platformy Azure musi być następnie włączona w prywatnym punkcie końcowym pozyskiwania, [wykonując następujące czynności:](#creating-an-ingestion-private-endpoint) 
    1. W przypadku wszystkich typów źródeł **platformy Azure** , takich jak Azure Blob storage, Azure SQL Database i innych, należy jawnie wybrać Uruchamianie skanowania przy użyciu samodzielnego środowiska IR, aby zapewnić izolację sieci. Wykonaj kroki opisane [tutaj](manage-integration-runtimes.md) , aby skonfigurować samoobsługowe środowisko IR. Następnie należy skonfigurować skanowanie w źródle platformy Azure, wybierając środowisko IR w obszarze **Połącz za pośrednictwem środowiska Integration Runtime** , aby zapewnić izolację sieci. 
    
    :::image type="content" source="media/catalog-private-link/shir-for-azure.png" alt-text="Uruchamianie skanowania platformy Azure przy użyciu samodzielnego środowiska IR":::

> [!NOTE]
> Obecnie nie obsługujemy metody poświadczeń MSI podczas skanowania źródeł platformy Azure przy użyciu samodzielnego środowiska IR. Musisz użyć jednej z innych obsługiwanych metod Credential dla tego źródła platformy Azure.

## <a name="enable-private-endpoint-on-existing-purview-accounts"></a>Włącz prywatny punkt końcowy na istniejących kontach kontrolą

Istnieją dwa sposoby dodawania prywatnych punktów końcowych kontrolą po utworzeniu konta kontrolą:

- Korzystanie z Azure Portal (konto kontrolą)
- Korzystanie z prywatnego centrum linków

### <a name="using-the-azure-portal-purview-account"></a>Korzystanie z Azure Portal (konto kontrolą)

1. Przejdź do konta kontrolą z Azure Portal, wybierz połączenia prywatnego punktu końcowego w sekcji **Sieć** **ustawień**.

    :::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="Utwórz prywatny punkt końcowy konta":::

1. Kliknij pozycję + prywatny punkt końcowy, aby utworzyć nowy prywatny punkt końcowy.

1. Wypełnij podstawowe informacje.

1. Na karcie zasób wybierz typ zasobu, który ma być typem **Microsoft. kontrolą/accounts**.

1. Wybierz zasób, który ma być kontem kontrolą, a następnie wybierz pozycję docelowy zasób podrzędny, aby **uwzględnić konto**.

1. Wybierz **strefę** **sieci wirtualnej** i prywatna strefa DNS na karcie Konfiguracja. Przejdź do strony Podsumowanie, a następnie kliknij przycisk **Utwórz** , aby utworzyć prywatny punkt końcowy portalu.

> [!NOTE]
> Należy wykonać te same czynności jak powyżej dla docelowego zasobu podrzędnego wybranego jako **Portal** .

#### <a name="creating-an-ingestion-private-endpoint"></a>Tworzenie prywatnego punktu końcowego pozyskiwania

1. Przejdź do konta kontrolą z Azure Portal, wybierz połączenia prywatnego punktu końcowego w sekcji **Sieć** **ustawień**.
1. Przejdź do karty **połączenia prywatnego punktu końcowego** pozyskiwania, a następnie kliknij pozycję **+ Nowy** , aby utworzyć nowy prywatny punkt końcowy pozyskiwania.

1. Wypełnij podstawowe informacje i szczegóły sieci wirtualnej.
 
    :::image type="content" source="media/catalog-private-link/ingestion-pe-fill-details.png" alt-text="Wypełnij szczegóły prywatnego punktu końcowego":::

1. Kliknij przycisk **Utwórz** , aby zakończyć konfigurację.

> [!NOTE]
> Prywatne punkty końcowe pozyskiwania można tworzyć tylko za pomocą opisanego powyżej środowiska kontrolą Azure Portal. Nie można go utworzyć z poziomu prywatnego centrum linków.

### <a name="using-the-private-link-center"></a>Korzystanie z prywatnego centrum linków

1. Przejdź do [Azure Portal](https://portal.azure.com).

2. Na pasku wyszukiwania w górnej części strony Wyszukaj wartość "link prywatny" i przejdź do bloku link prywatny, klikając pierwszą opcję.

3. Kliknij pozycję "+ Dodaj" i Wypełnij podstawowe informacje.

   :::image type="content" source="media/catalog-private-link/private-link-center.png" alt-text="Tworzenie środowiska PE z poziomu prywatnego centrum linków":::

4. Wybierz zasób, który ma być już utworzonym kontem kontrolą i wybierz docelowy zasób podrzędny, który ma być **kontem**.

5. Wybierz strefę sieci wirtualnej i Prywatna strefa DNS na karcie Konfiguracja. Przejdź do strony Podsumowanie, a następnie kliknij przycisk **Utwórz** , aby utworzyć prywatny punkt końcowy konta.

> [!NOTE]
> Należy wykonać te same czynności jak powyżej dla docelowego zasobu podrzędnego wybranego jako **Portal** .

## <a name="firewalls-to-restrict-public-access"></a>Zapory ograniczające dostęp publiczny

Aby całkowicie wyciąć dostęp do konta kontrolą z publicznej sieci Internet, wykonaj poniższe czynności. To ustawienie będzie stosowane do prywatnych punktów końcowych i prywatnych połączeń punktów końcowych.

1. Przejdź do konta kontrolą z Azure Portal, wybierz połączenia prywatnego punktu końcowego w sekcji **Sieć** **ustawień**.
1. Przejdź do karty Zapora i upewnij się, że przełącznik jest ustawiony na **Odmów**.

    :::image type="content" source="media/catalog-private-link/private-endpoint-firewall.png" alt-text="Ustawienia zapory prywatnego punktu końcowego":::

## <a name="next-steps"></a>Następne kroki

- [Przeglądanie Data Catalog platformy Azure kontrolą](how-to-browse-catalog.md)

- [Przeszukaj Data Catalog Azure kontrolą](how-to-search-catalog.md)
