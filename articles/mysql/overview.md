---
title: Przegląd — Azure Database for MySQL
description: Dowiedz się więcej na temat usługi Azure Database for MySQL, usługi relacyjnej bazy danych w chmurze firmy Microsoft w oparciu o wersję MySQL Community Edition.
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 3/18/2020
ms.openlocfilehash: 51c194ca9b091bc685f293320750da55925ad49d
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91565619"
---
# <a name="what-is-azure-database-for-mysql"></a>Co to jest usługa Azure Database for MySQL?

Azure Database for MySQL jest usługą relacyjnej bazy danych w chmurze firmy Microsoft opartą na programie [MySQL Community Edition](https://www.mysql.com/products/community/) (dostępnym w ramach GPLv2 licencji), wersje 5,6, 5,7 i 8,0. Usługa Azure Database for MySQL zapewnia następujące funkcje:

- Wbudowana wysoka dostępność
- Ochrona danych przy użyciu automatycznych kopii zapasowych i przywracanie do punktu w czasie przez maksymalnie 35 dni.
- Automatyczna konserwacja podstawowego sprzętu, systemu operacyjnego i aparatu bazy danych, aby zapewnić bezpieczeństwo i aktualność usługi.
- Przewidywalna wydajność z zastosowaniem inkluzywnego cennika płatności zgodnie z rzeczywistym użyciem.
- Elastyczne skalowanie w ciągu kilku sekund.
- Kontrolki optymalizacji kosztów z możliwością zatrzymania/uruchomienia serwera. 
- Bezpieczeństwo klasy korporacyjnej i wiodąca w branży zgodność w celu ochrony poufnych danych w czasie spoczynku i ruchowo.
- Monitorowanie i Automatyzacja upraszczające zarządzanie i monitorowanie wdrożeń na dużą skalę.
- Wiodące w branży środowisko pomocy technicznej.

Powyższe możliwości prawie w ogóle nie wymagają administracji i są udostępniane bez żadnych dodatkowych kosztów. Dzięki nim możesz skoncentrować się na szybkim tworzeniu aplikacji i skracaniu czasu wejścia na rynek, a nie na poświęcaniu cennego czasu i cennych zasobów na zarządzanie maszynami wirtualnymi i infrastrukturą. Ponadto możesz kontynuować tworzenie aplikacji przy użyciu tych samych wybranych przez siebie narzędzi typu open source oraz platformy w celu dostarczania zawartości z wymaganą szybkością i skutecznością bez konieczności uczenia się nowych umiejętności.

:::image type="content" source="media/overview/1-azure-db-for-mysql-conceptual-diagram.png" alt-text="Diagram koncepcyjny Azure Database for MySQL":::

## <a name="deployment-models"></a>Modele wdrażania

Azure Database for MySQL obsługiwane przez program MySQL Community Edition jest dostępny w dwóch trybach wdrożenia:
- Pojedynczy serwer 
- Serwer elastyczny (wersja zapoznawcza)
  
### <a name="azure-database-for-mysql---single-server"></a>Azure Database for MySQL — pojedynczy serwer

Azure Database for MySQL pojedynczym serwerze to w pełni zarządzana usługa bazy danych z minimalnymi wymaganiami dotyczącymi dostosowywania bazy danych. Platforma jednoserwerowa została zaprojektowana tak, aby obsługiwała większość funkcji zarządzania bazami danych, takich jak stosowanie poprawek, kopii zapasowych, wysoka dostępność, zabezpieczenia z minimalną konfiguracją i kontrolą użytkownika. Architektura jest zoptymalizowana pod kątem wbudowanej wysokiej dostępności dzięki dostępności na 99,99% w ramach pojedynczej strefy dostępności. Obsługuje ona wersję społeczności MySQL 5,6, 5,7 i 8,0. Usługa jest ogólnie dostępna dzisiaj w wielu [regionach świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/services/).

Opcja wdrożenia pojedynczego serwera oferuje trzy warstwy cenowe: Podstawowa, Ogólnego przeznaczenia lub Zoptymalizowana pod kątem pamięci. Każda warstwa oferuje inne możliwości w zakresie zasobów, aby wspierać obciążenia bazy danych. Możesz utworzyć swoją pierwszą aplikację na podstawie małej bazy danych za jedynie kilka dolarów miesięcznie, a następnie dostosować skalowanie do potrzeb rozwiązania. Dynamiczna skalowalność umożliwia bazie danych przezroczyste odpowiadanie na gwałtownie zmieniające się wymagania dotyczące zasobów. Zapłacisz tylko za potrzebne zasoby i tylko wtedy, gdy będą używane. Aby uzyskać szczegółowe informacje, zobacz [Warstwy cenowe](concepts-pricing-tiers.md).

Pojedyncze serwery najlepiej nadają się do obsługi natywnych aplikacji w chmurze, które mają obsługiwać automatyczne stosowanie poprawek, bez konieczności przeprowadzania szczegółowej kontroli nad harmonogramem poprawek i niestandardowych ustawień konfiguracji programu MySQL. 

Aby zapoznać się z szczegółowym omówieniem trybu wdrażania na jednym serwerze, zobacz [Omówienie jednego serwera](single-server-overview.md).

### <a name="azure-database-for-mysql---flexible-server-preview"></a>Serwer elastyczny Azure Database for MySQL (wersja zapoznawcza)

Azure Database for MySQL elastyczny serwer to w pełni zarządzana usługa bazy danych, która umożliwia dokładniejszą kontrolę i elastyczność w zakresie funkcji zarządzania bazami danych i ustawień konfiguracji. Ogólnie rzecz biorąc, usługa zapewnia większą elastyczność i dostosowanie w zależności od wymagań użytkownika. Elastyczna architektura serwera umożliwia użytkownikom wybór wysokiej dostępności w ramach strefy pojedynczej dostępności i wielu stref dostępności. Elastyczne serwery zapewniają lepszą kontrolę kosztów dzięki możliwości zatrzymywania/uruchamiania serwera i możliwego do naliczania mocy obliczeniowej, idealnym rozwiązaniem w przypadku obciążeń, które nie wymagają ciągłej pojemności obliczeniowej. Usługa obecnie obsługuje wersje społeczności MySQL 5,7 z planami, aby wkrótce dodać nowsze wersje. Usługa jest obecnie w publicznej wersji zapoznawczej, która jest dostępna dzisiaj w wielu [regionach świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/services/).

Serwery elastyczne najlepiej nadają się do 
- Rozwój aplikacji wymagający lepszej kontroli i dostosowań.
- Strefa nadmiarowa wysokiej dostępności
- Zarządzane okna obsługi

Aby zapoznać się z szczegółowym omówieniem elastycznego trybu wdrażania serwera, zapoznaj się z tematem [elastyczne Omówienie serwera](flexible-server/overview.md).

## <a name="contacts"></a>Kontakty
Aby dowiedzieć się więcej na temat pytań lub sugestii dotyczących pracy z Azure Database for MySQL, Wyślij wiadomość e-mail do zespołu Azure Database for MySQL ([ @Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). Ten adres e-mail nie jest aliasem pomocy technicznej.

Ponadto, w zależności od potrzeb, należy wziąć pod uwagę następujące punkty kontaktowe:

- Aby skontaktować się z pomocą techniczną platformy Azure, [wyślij zgłoszenie z witryny Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Aby rozwiązać problem z Twoim kontem, wyślij [żądanie obsługi](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) w portalu Azure Portal.
- Aby przekazać opinię lub poprosić o nowe funkcje, utwórz wpis w platformie [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat dwóch trybów wdrażania dla Azure Database for MySQL i wybierz odpowiednie opcje w zależności od potrzeb.

- [Pojedynczy serwer](single-server/index.yml)
- [Elastyczny serwer](flexible-server/index.yml)
- [Wybierz odpowiednią opcję wdrożenia MySQL dla danego obciążenia](select-right-deployment-type.md)
