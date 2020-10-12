---
title: Często zadawane pytania dotyczące baz danych ClearDB MySql przy użyciu Azure App Service
description: Odpowiedzi na często zadawane pytania dotyczące korzystania z baz danych MySQL ClearDB z Azure App Service.
documentationcenter: php
services: mysql
author: sunbuild
manager: yochayk
tags: mysql
ms.service: multiple
ms.topic: article
ms.date: 10/27/2016
ms.author: sumuth
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: references_regions
ms.openlocfilehash: ced4e920936ad94c3d4140d51d5e7d4e867ee848
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84196072"
---
# <a name="faq-for-cleardb-mysql-databases-with-azure-app-service"></a>Często zadawane pytania dotyczące baz danych ClearDB MySql przy użyciu Azure App Service
Często zadawane pytania dotyczą często zadawanych pytań dotyczących używania i kupowania baz danych MySQL ClearDB dla platformy Web Apps Azure.

> [!IMPORTANT]
> Od 13 czerwca 2018 ClearDB przeniesieli klientów opartych na platformie Azure, którzy są rozliczani przez firmę Microsoft do bezpośredniego modelu rozliczeń z ClearDB. Informacje zawarte w tym artykule są obecnie nieaktualne. Nie będzie już można tworzyć ani aktualizować bazy danych ClearDB, która została utworzona na platformie Azure.
>
> Aby uzyskać więcej informacji i następnych kroków, zobacz [zmiany w planach usług ClearDB](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/).

## <a name="what-options-do-i-have-for-mysql-on-azure"></a>Jakie opcje są dostępne dla bazy danych MySQL na platformie Azure?
Aby uzyskać najnowsze informacje na temat tej usługi, zobacz [ClearDB](https://w2.cleardb.net/) . ClearDB to usługa hostingu MySQL i zarządza infrastrukturą MySQL. 

Masz kilka innych opcji hostingu MySQL na platformie Azure:
* [Azure Database for MySQL](https://azure.microsoft.com/services/mysql/)
* [Klaster MySQL uruchomiony na maszynie wirtualnej platformy Azure](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)
* [Pojedyncze wystąpienie MySQL uruchomione na maszynie wirtualnej platformy Azure](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="do-i-need-a-credit-card-for-the-web-app--mysql-template-in-the-azure-marketplace"></a>Czy potrzebuję karty kredytowej dla szablonu aplikacja sieci Web i baza danych MySQL w witrynie Azure Marketplace?
Jest to zależne od typu używanej subskrypcji. Poniżej przedstawiono niektóre powszechnie używane typy subskrypcji:

* [Płatność zgodnie z rzeczywistym](https://azure.microsoft.com/offers/ms-azr-0003p/)użyciem: wymaga karty kredytowej, a w przypadku zakupu płatnej bazy danych MySQL opłata za kartę kredytową jest naliczana.
* [Bezpłatna wersja próbna](https://azure.microsoft.com/pricing/free-trial/): obejmuje kredyty do użytku z usługami Microsoft Azure Services, ale nie zezwala na zakup zasobów innych firm. Aby kupić usługi innych firm lub płatną bazę danych MySQL, należy skorzystać z subskrypcji z obsługą kart kredytowych. Aby uzyskać Web Apps, możesz utworzyć bezpłatną bazę danych MySQL ClearDB.
* [Subskrypcje MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/) i **MSDN Dev Test — płatność zgodnie z rzeczywistym**użyciem: podobnie jak w przypadku bezpłatnej wersji próbnej subskrypcja MSDN wymaga posiadania karty kredytowej do zakupu płatnego rozwiązania MySQL od ClearDB.
* [Umowa Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/): klienci z umowami EA są rozliczani według umowy EA każdego kwartału dla wszystkich zakupów w portalu Azure Marketplace na oddzielnej, skonsolidowanej fakturze. Opłaty są naliczane poza zobowiązaniem pieniężnym za jakiekolwiek zakupy w portalu Marketplace. Należy pamiętać, że w tym momencie sklep systemu Azure nie jest dostępny dla klientów zarejestrowanych w Azerbejdżanu, Chorwacji, Norwegii i Portoryko. 

## <a name="why-was-i-charged-350-for-a-web-app--mysql-from-the-azure-marketplace"></a>Dlaczego opłata była naliczana $3,50 dla aplikacji internetowej i bazy danych MySQL z portalu Azure Marketplace?
Domyślna opcja bazy danych to Titan, co to jest $3,50. Nie pokazujemy kosztów podczas tworzenia bazy danych i można wypróbować wykupienie nieplanowanej bazy danych. Próbujemy znaleźć sposób ulepszania środowiska, ale do momentu, gdy po kliknięciu przycisku **Utwórz** i rozpocznie się wdrażanie zasobów, musisz sprawdzić wszystkie wybrane warstwy cenowe dla aplikacji sieci Web i bazy danych.

## <a name="i-am-running-mysql-on-my-own-azure-virtual-machine-can-i-connect-my-azure-web-app-to-my-database"></a>Korzystam z programu MySQL na mojej maszynie wirtualnej platformy Azure. Czy mogę połączyć aplikację sieci Web platformy Azure z moją bazą danych?
Tak. Możesz połączyć swoją aplikację sieci Web z bazą danych, o ile maszyna wirtualna platformy Azure ma dostęp zdalny do aplikacji sieci Web. Aby uzyskać więcej informacji, zobacz [Instalowanie programu MySQL na maszynie wirtualnej](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="in-which-countriesregions-are-cleardb-premium-mysql-clusters-supported"></a>W jakich krajach/regionach są obsługiwane klastry ClearDB Premium MySQL?
Klastry MySQL ClearDB Premium są dostępne we wszystkich regionach świadczenia usługi Azure na całym świecie z wyjątkiem Indii, Australii, Brazylii Południowej i Chin.

## <a name="can-i-create-a-new-cluster-prior-to-creating-a-database-with-cleardb-premium-cluster-solution"></a>Czy można utworzyć nowy klaster przed utworzeniem bazy danych z rozwiązaniem klastra ClearDB Premium?
Nie, tworzenie pustych klastrów ClearDB nie jest obsługiwane. Azure Portal umożliwia tworzenie baz danych w klastrze, co może jednocześnie utworzyć nowy klaster.

## <a name="will-i-be-warned-if-i-try-to-delete-a-cleardb-mysql-database-that-is-in-use-by-one-of-my-applications"></a>Czy zostanie wyświetlone ostrzeżenie w przypadku próby usunięcia bazy danych MySQL ClearDB, która jest używana przez jedną z aplikacji?
Nie, platforma Azure nie wyświetli ostrzeżenia w przypadku usunięcia zakupu w portalu Marketplace, od którego zależy Twoja aplikacja.

## <a name="which-regions-can-i-create-cleardb-databases-in"></a>W jakich regionach można tworzyć bazy danych ClearDB?
Portal Azure Marketplace nie jest dostępny dla klientów zarejestrowanych w Azerbejdżanu, Chorwacji, Norwegii lub Portoryko. ClearDB nie jest dostępna w tych regionach.

## <a name="what-pricing-tier-should-i-choose-for-a-production-web-app-and-database"></a>Jaka warstwa cenowa powinna zostać wybrana dla produkcyjnej aplikacji sieci Web i bazy danych?
Użyj warstwy cenowej podstawowa lub wyższa dla Web Apps. W przypadku ClearDB zalecamy użycie planu Saturn lub Jupiter. Przejrzyj funkcje & ograniczenia każdej warstwy cenowej dla [bazy danych MySQL](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/) [Web Apps](https://azure.microsoft.com/pricing/details/app-service/) i ClearDB, aby wybrać ten, który odpowiada Twoim potrzebom.

## <a name="how-do-i-upgrade-my-cleardb-database-from-one-plan-to-another"></a>Jak mogę uaktualnić bazę danych ClearDB z jednego planu do innego?
W [Azure Portal](https://portal.azure.com)można skalować w górę ClearDB udostępnioną bazę danych hostingu. Przeczytaj ten [artykuł](https://blogs.msdn.microsoft.com/appserviceteam/2016/10/06/upgrade-your-cleardb-mysql-database-in-azure-portal/) , aby dowiedzieć się więcej. Obecnie nie obsługujemy uaktualniania do klastrów ClearDB Premium w Azure Portal.

## <a name="i-cant-see-my-cleardb-database-in-azure-portal"></a>Nie mogę zobaczyć mojej bazy danych ClearDB w Azure Portal?
Jeśli baza danych ClearDB została utworzona w trybie klasycznym, nie będzie można zobaczyć bazy danych w [Azure Portal](https://portal.azure.com). W tym scenariuszu nie ma żadnego obejścia.

## <a name="who-do-i-contact-for-support-when-my-database-is-down"></a>Z kim mam się kontaktować, aby uzyskać pomoc techniczną, gdy moja baza danych nie działa?
Skontaktuj się z [pomocą techniczną ClearDB](https://www.cleardb.com/developers/help/support) w przypadku problemów związanych z bazą danych. Przygotuj się, aby zapewnić im informacje o subskrypcji platformy Azure.

## <a name="can-i-create-additional-users-for-my-cleardb-mysql-database-cluster-solution"></a>Czy mogę utworzyć dodatkowych użytkowników rozwiązania do tworzenia klastrów baz danych ClearDB MySQL?
Nie. Nie można utworzyć dodatkowych użytkowników, ale można utworzyć dodatkowe bazy danych w klastrze bazy danych ClearDB.  

## <a name="can-basicpro-series-databases-be-upgraded-in-place-similar-to-planetary-plans-today-on-cleardb-portal"></a>Czy bazy danych z serii Basic/Pro można uaktualniać w miejscu podobnym do planów Planetary w portalu ClearDB?
Tak, bazy danych serii Basic można uaktualnić w miejscu (podstawowa 60 do wersji Basic 500). Serie Pro można uaktualnić w miejscu (Pro 125 do Pro 1000), z wyjątkiem Pro 60. Obecnie nie obsługujemy uaktualniania bazy danych Pro 60. 

## <a name="when-i-migrate-my-resources-from-one-subscription-to-another-does-my-cleardb-mysql-database-get-migrated-as-well"></a>Czy po przeprowadzeniu migracji zasobów z jednej subskrypcji do innej baza danych ClearDB MySQL jest również migrowana?
W przypadku przeprowadzania migracji zasobów między subskrypcjami obowiązują pewne [ograniczenia](azure-resource-manager/management/move-support-resources.md) . Baza danych programu ClearDB MySQL jest usługą innej firmy, dlatego nie jest migrowana podczas migracji subskrypcji platformy Azure. Jeśli nie zarządzasz migracją bazy danych MySQL przed migracją zasobów platformy Azure, można wyłączyć bazy danych MySQL ClearDB. Ręcznie Migruj bazy danych, a następnie Przeprowadź migrację subskrypcji platformy Azure dla aplikacji sieci Web. 

## <a name="i-hit-the-spending-limit-on-my-subscription-i-removed-the-limit-and-my-app-service-is-online-however-the-database-is-not-accessible-how-do-i-re-enable-the-cleardb-database"></a>Osiągnięto limit wydatków w ramach subskrypcji. Po usunięciu limitu, gdy App Service jest w trybie online, jednak baza danych jest niedostępna. Jak mogę ponownie włączyć bazę danych ClearDB?
Skontaktuj się z [pomocą techniczną ClearDB](https://www.cleardb.com/developers/help/support) , aby ponownie włączyć bazę danych. Podaj je za pomocą informacji o subskrypcji platformy Azure i nazwy bazy danych.

## <a name="can-i-transfer-a-cleardb-database-from-a-credit-card-subscription-to-an-ea-subscription"></a>Czy mogę przenieść bazę danych ClearDB z subskrypcji karty kredytowej do subskrypcji umowy EA?
Istniejące bazy danych ClearDB używają karty kredytowej skojarzonej z istniejącymi subskrypcjami. Aby skorzystać z subskrypcji EA, należy przeprowadzić migrację danych do nowej bazy danych:

* Kup nową bazę danych ClearDB z subskrypcją EA.
* Migruj dane do nowej bazy danych.
* Zaktualizuj swoją aplikację, aby używała nowej bazy danych.
* Usuń starą bazę danych ClearDB.

W przypadku tworzenia nowej aplikacji sieci Web przy użyciu programu MySQL (ClearDB) lub tworzenia bazy danych MySQL (ClearDB) wybrana subskrypcja określa, w jaki sposób płacisz za usługę. W przypadku subskrypcji z umową EA nie będziemy blokować zakupów usług innych firm, takich jak ClearDB, w Azure Portal. Subskrypcje z umową EA są rozliczane poza zobowiązaniem pieniężnym i są rozliczane kwartalnie i w zaległościach. Klient z umową EA musiał skonfigurować metodę płatności, taką jak karta kredytowa, aby zapłacić za jakiekolwiek usługi rynkowe innych firm.

## <a name="where-can-i-see-the-charges-for-cleardb-resources-in-an-ea-subscription"></a>Gdzie mogę zobaczyć opłaty za zasoby ClearDB w ramach subskrypcji umowy EA?
W przypadku klientów z bezpośrednią umową EA opłaty za usługę Azure Marketplace są widoczne na Enterprise Portal. Należy pamiętać, że wszystkie zakupy i zużycie w portalu Marketplace są rozliczane poza zobowiązaniem pieniężnym i są rozliczane kwartalnie i w zaległościach. Klienci z umowami EA muszą zapłacić bezpośrednio dla dostawców usług innych firm i można to zrobić, włączając metodę płatności, taką jak karta kredytowa, przy użyciu konta umowy EA.

Pośrednim klientom z umową EA mogą znaleźć subskrypcje portalu Azure Marketplace na stronie **Zarządzanie subskrypcjami** Enterprise Portal, ale Cennik jest ukryty. Klienci powinni skontaktować się ze swoim dostawcą LSP, aby uzyskać informacje o opłatach za platformę handlową.

Dostęp do portalu Azure Marketplace dla usług innych firm może być zarządzany przez administratorów rejestracji systemu Azure w ramach umowy EA. Mogą oni wyłączyć lub ponownie włączyć dostęp do zakupów innych firm ze sklepu w obszarze Zarządzanie kontami i subskrypcjami w sekcji konta w Enterprise Portal.

## <a name="who-do-i-contact-for-questions-about-my-bill-for-cleardb-services-in-my-ea-subscription"></a>Z kim mam się kontaktować, aby uzyskać odpowiedzi na pytania dotyczące moich rachunków za usługi ClearDB Services w ramach subskrypcji umowy EA?
Skontaktuj się z działem [pomocy technicznej przedsiębiorstwa](https://aka.ms/AzureEntSupport) w zakresie rozliczeń w ramach ich rejestracji w ramach umowy EA. Zespół pomocy technicznej portalu EA udzieli odpowiedzi na Twoje pytanie lub pomoże Ci rozwiązać problem.

## <a name="more-information"></a>Więcej informacji
[Witryna Azure Marketplace — często zadawane pytania](https://azure.microsoft.com/marketplace/faq/)

