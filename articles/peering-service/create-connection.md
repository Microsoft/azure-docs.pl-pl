---
title: 'Usługa komunikacji równorzędnej Azure: Tworzenie '
description: W tym samouczku dowiesz się, jak zarejestrować usługę komunikacji równorzędnej platformy Azure i prefiks.
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/2/2020
ms.author: derekol
Customer intent: With Azure Peering service enhancing the customer connectivity to Microsoft cloud services .
ms.openlocfilehash: f8f0ee71a8dd00498d868bb6dc11b3fa083df766
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91530222"
---
# <a name="tutorial-create-a-peering-service-connection"></a>Samouczek: Tworzenie połączenia usługi Komunikacja równorzędna

W tym samouczku pokazano, jak utworzyć zasób usługi komunikacji równorzędnej i jak skonfigurować połączenie usługi komunikacji równorzędnej. 

1. Aby zarejestrować połączenie z usługą komunikacji równorzędnej, wybierz pozycję **Utwórz zasób**  >  **równorzędny usługi**.

 
    ![Rejestrowanie usługi Peering Service](./media/peering-service-portal/peering-servicecreate.png)

2. Na karcie **podstawowe** na stronie **Tworzenie połączenia usługi Komunikacja równorzędna** wprowadź następujące szczegóły.
 
3. Wybierz subskrypcję i grupę zasobów skojarzoną z subskrypcją.

    ![Zarejestruj kartę podstawową usługi komunikacji równorzędnej](./media/peering-service-portal/peering-servicebasics.png)

4. Wprowadź **nazwę** , w której ma zostać zarejestrowane wystąpienie usługi komunikacji równorzędnej.

5. Teraz wybierz przycisk **Dalej: Konfiguracja** w dolnej części strony. Zostanie wyświetlona strona **Konfiguracja** .
## <a name="configure-the-peering-service-connection"></a>Konfigurowanie połączenia usługi Komunikacja równorzędna

1. Na stronie **Konfiguracja** wybierz lokalizację, w której należy włączyć usługę komunikacji równorzędnej, wybierając ją z listy rozwijanej **Lokalizacja usługi komunikacji równorzędnej** .

1. Wybierz dostawcę usług, z którego należy uzyskać usługę komunikacji równorzędnej, wybierając nazwę dostawcy z listy rozwijanej **Dostawca usługi komunikacji równorzędnej** .
 
1. Wybierz pozycję **Utwórz nowy prefiks** u dołu sekcji **prefiksy** , a następnie Wyświetl pola tekstowe. Teraz wprowadź nazwę zasobu prefiksu i prefiksy, które są skojarzone z dostawcą usług.

1. Wybierz pozycję **klucz prefiksu** i Dodaj klucz prefiksu, który został udzielony przez dostawcę (ISP lub IXP). Ten klucz umożliwia firmie Microsoft sprawdzenie poprawności prefiksu i dostawcy, którym przydzielono prefiks IP.

    ![Zrzut ekranu przedstawia kartę Konfiguracja strony Tworzenie połączenia z usługą komunikacji równorzędnej, w której można wprowadzić klucz prefiksu.](./media/peering-service-portal/peering-serviceconfiguration.png)

1. Wybierz przycisk **Recenzja + Utwórz** w lewym dolnym rogu strony. Zostanie wyświetlona strona **Recenzja + tworzenie** , a platforma Azure weryfikuje konfigurację.

 1. Gdy zostanie wyświetlony komunikat **zweryfikowano** , jak pokazano, wybierz pozycję **Utwórz**.

> ![Zrzut ekranu przedstawia kartę Przegląd + tworzenie na stronie Tworzenie połączenia z usługą komunikacji równorzędnej.](./media/peering-service-portal/peering-service-prefix.png)

1. Po zarejestrowaniu połączenia z usługą komunikacji równorzędnej zostanie przeprowadzone dodatkowe sprawdzanie poprawności zawartych prefiksów. Stan sprawdzania poprawności można sprawdzić w sekcji **prefiksy** nazwy zasobu. Jeśli sprawdzanie poprawności nie powiedzie się, zostanie wyświetlony jeden z następujących komunikatów o błędach:

   - Nieprawidłowy prefiks usługi komunikacji równorzędnej, prefiks powinien być prawidłowym formatem, obsługiwane są tylko prefiksy IPv4.
   - Nie odebrano prefiksu od dostawcy usługi komunikacji równorzędnej.
   - Anons prefiksu nie ma prawidłowej społeczności protokołu BGP, skontaktuj się z dostawcą usług komunikacji równorzędnej.
   - Nie znaleziono trasy kopii zapasowej, skontaktuj się z dostawcą usług komunikacji równorzędnej.
   - Prefiks został odebrany z dłuższą ścieżką, skontaktuj się z dostawcą usług komunikacji równorzędnej.
   - Prefiks został odebrany jako prywatny jako ścieżka, skontaktuj się z dostawcą usług komunikacji równorzędnej.

### <a name="add-or-remove-a-prefix"></a>Dodawanie lub usuwanie prefiksu

Wybierz pozycję **Dodaj prefiksy** na stronie **prefiksy** , aby dodać prefiksy.

Wybierz wielokropek (...) obok wymienionego prefiksu, a następnie wybierz opcję **Usuń** .

### <a name="delete-a-peering-service-connection"></a>Usuwanie połączenia usługi Komunikacja równorzędna

Na stronie **wszystkie zasoby** zaznacz pole wyboru w usłudze Komunikacja równorzędna i wybierz opcję **Usuń** w górnej części strony.
## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o połączeniu z usługą komunikacji równorzędnej, zobacz [nawiązywanie połączenia z usługą komunikacji równorzędnej](connection.md).
- Aby dowiedzieć się więcej o telemetrii połączenia z usługą komunikacji równorzędnej, zobacz [Komunikacja równorzędna połączenia z usługą](connection-telemetry.md).
- Aby mierzyć dane telemetryczne, zobacz [mierzenie danych telemetrycznych połączenia](measure-connection-telemetry.md).
- Aby zarejestrować połączenie przy użyciu Azure PowerShell, zobacz [Rejestrowanie połączenia usługi Komunikacja równorzędna — Azure PowerShell](powershell.md).
- Aby zarejestrować połączenie przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz temat [Rejestrowanie połączenia z usługą komunikacji równorzędnej — interfejs wiersza polecenia platformy Azure](cli.md).