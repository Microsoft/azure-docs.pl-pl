---
title: Użyj Azure Data Studio, aby zarządzać wystąpieniem PostgreSQL
description: Użyj Azure Data Studio, aby zarządzać wystąpieniem PostgreSQL
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 7dcc0f916a15598060e034dcf62536ee13e2672e
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92320235"
---
# <a name="use-azure-data-studio-to-manage-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Użyj Azure Data Studio, aby zarządzać grupą serwerów PostgreSQL z funkcją Azure Arc


W tym artykule opisano, jak:
- Zarządzanie wystąpieniami PostgreSQL za pomocą widoków pulpitu nawigacyjnego, takich jak przegląd, parametry połączenia, właściwości, Resource Health...
- Pracuj z danymi i schematem

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- [Instalowanie azdata, Azure Data Studio i interfejsu wiersza polecenia platformy Azure](install-client-tools.md)
- Zainstaluj program w Azure Data Studio **[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]** rozszerzeniami Microsoft i **Azure Arc** oraz **PostgreSQL**
- Utwórz [kontroler danych usługi Azure Arc](create-data-controller-using-azdata.md)
- Azure Data Studio uruchamiania

## <a name="connect-to-the-azure-arc-data-controller"></a>Nawiązywanie połączenia z kontrolerem danych usługi Azure Arc

W Azure Data Studio rozwiń węzeł **kontrolery usługi Azure Arc** i wybierz przycisk **Połącz kontroler** :

Wprowadź informacje o połączeniu z kontrolerem danych platformy Azure:

- **Adres URL kontrolera:**

    Adres URL do połączenia z kontrolerem w Kubernetes. Wprowadzone w postaci `https://<IP_address_of_the_controller>:<Kubernetes_port.` na przykład:

    ```console
    https://12.345.67.890:30080
    ```
- **Nazwa użytkownika:**

    Nazwa konta użytkownika używanego do nawiązywania połączenia z kontrolerem. Użyj nazwy zwykle używanej podczas uruchamiania programu `azdata login` . Nie jest to nazwa użytkownika PostgreSQL, który jest używany do nawiązywania połączenia z aparatem bazy danych PostgreSQL zazwyczaj z PSQL.
- **Hasło:** Hasło konta użytkownika używanego do nawiązywania połączenia z kontrolerem


Usługa Azure Data Studio Wyświetla kontroler danych Arc. Rozwiń go i wyświetla listę wystąpień PostgreSQL, którymi zarządza.

## <a name="manage-your-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Zarządzaj grupami serwerów PostgreSQL z funkcją Azure Arc

Kliknij prawym przyciskiem myszy wystąpienie PostgreSQL, którym chcesz zarządzać, a następnie wybierz pozycję [Zarządzaj]

Widok pulpitu nawigacyjnego PostgreSQL:

Ta funkcja zawiera kilka pulpitów nawigacyjnych, które znajdują się po lewej stronie tego okienka:

- **Przegląd:** Wyświetla informacje podsumowujące dotyczące wystąpienia, takie jak nazwa, nazwa użytkownika administratora PostgreSQL, Identyfikator subskrypcji platformy Azure, konfiguracja, wersja aparatu bazy danych, punkty końcowe dla Grafana i Kibana...
- **Parametry połączenia:** Wyświetla różne parametry połączeń, które mogą być potrzebne do nawiązania połączenia z wystąpieniem usługi PostgreSQL, na przykład PSQL, Node.js, PHP, Ruby...
- **Diagnozowanie i rozwiązywanie problemów:** To strona docelowa, w której znajdziesz różne zasoby, które ułatwią Rozwiązywanie problemów z wystąpieniem w miarę rozszerzania notesów rozwiązywania problemów
- **Nowe żądanie obsługi:** To strona docelowa, z której będzie można zażądać pomocy z naszych usług pomocy technicznej, aby uzyskać ogłoszenie o zadaniu w publicznej wersji zapoznawczej.

## <a name="work-with-your-data-and-schema"></a>Pracuj z danymi i schematem

Po lewej stronie okna Azure Data Studio rozwiń węzeł **serwery**:

I wybierz pozycję [Dodaj połączenie] i wypełnij szczegóły połączenia z wystąpieniem usługi PostgreSQL:
- **Typ połączenia:** PostgreSQL
- **Nazwa serwera:** wprowadź nazwę wystąpienia PostgreSQL. Na przykład: postgres01
- **Typ uwierzytelniania:** Hasło
- **Nazwa użytkownika:** można na przykład użyć standardowej/domyślnej nazwy użytkownika administratora PostgreSQL. Należy pamiętać, że w tym polu jest rozróżniana wielkość liter.
- **Hasło:** w danych wyjściowych polecenia można znaleźć hasło nazwy użytkownika PostgreSQL w ciągu połączenia PSQL. `azdata postgres server endpoint -n postgres01`
- **Nazwa bazy danych:** Określ nazwę bazy danych, z którą chcesz nawiązać połączenie. Można zezwolić na ustawienie __domyślne__
- **Grupa serwerów:** można zezwolić na ustawienie __domyślne__
- **Nazwa (opcjonalnie):** można pozostawić to pole puste
- **Doświadczonych**
    - **Adres IP hosta:** to publiczny adres IP klastra Kubernetes
    - **Port:** to port, na którym nasłuchuje wystąpienie PostgreSQL. Ten port można znaleźć na końcu ciągu połączenia PSQL w danych wyjściowych `azdata postgres server endpoint -n postgres01` polecenia. Nie jest to port 30080, na którym Kubernetes nasłuchuje i został wprowadzony podczas łączenia się z kontrolerem danych platformy Azure w Azure Data Studio.
    - **Inne parametry:** Powinny być one samodzielne, można na bieżąco z wartościami domyślnymi/pustymi, które są wyświetlane w.

Aby nawiązać połączenie z serwerem, wybierz pozycję **[OK] i [Połącz]** .

Po nawiązaniu połączenia dostępne są kilka środowisk:
- **Nowe zapytanie**
- **Nowy Notes**
- **Rozwiń widok serwera i Przeglądaj/Pracuj z obiektami w bazie danych**
- **...**

## <a name="next-step"></a>Następny krok
[Monitorowanie grupy serwerów](monitor-grafana-kibana.md)
