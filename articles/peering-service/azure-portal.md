---
title: Rejestrowanie usługi Komunikacja równorzędna Azure — Azure Portal
description: Dowiedz się, jak zarejestrować usługę komunikacji równorzędnej platformy Azure przy użyciu Azure Portal
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 62210105ba4ba84cff4b246c5b7cf8cc794fb5e7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91534951"
---
# <a name="register-peering-service-by-using-the-azure-portal"></a>Zarejestruj usługę komunikacji równorzędnej przy użyciu Azure Portal

Usługa komunikacji równorzędnej Azure to usługa sieciowa, która zwiększa łączność klienta z usługami w chmurze firmy Microsoft, takimi jak Microsoft 365, Dynamics 365, oprogramowanie jako usługa (SaaS), Azure lub wszelkie usługi firmy Microsoft dostępne za pośrednictwem publicznej sieci Internet.

W tym artykule dowiesz się, jak zarejestrować połączenie z usługą komunikacji równorzędnej za pomocą Azure Portal.

Jeśli nie masz subskrypcji platformy Azure, Utwórz [konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) teraz.

> 

## <a name="prerequisites"></a>Wymagania wstępne

Wymagane są następujące elementy:

### <a name="azure-account"></a>Konto platformy Azure

Musisz mieć prawidłowe i aktywne konto Microsoft Azure. To konto jest wymagane do skonfigurowania połączenia z usługą komunikacji równorzędnej. Usługa komunikacji równorzędnej jest zasobem w ramach subskrypcji platformy Azure. 

### <a name="connectivity-provider"></a>Dostawca połączenia

Aby uzyskać usługę komunikacji równorzędnej w celu połączenia sieci z siecią firmy Microsoft, możesz współpracować z usługodawcą internetowym lub z internetowym partnerem programu Exchange.

Upewnij się, że [dostawcy łączności](location-partners.md) są partnerem z firmą Microsoft.



## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

W przeglądarce przejdź do Azure Portal i zaloguj się przy użyciu konta platformy Azure.

## <a name="register-a-peering-service-connection"></a>Rejestrowanie połączenia usługi Peering Service

1. Aby zarejestrować połączenie z usługą komunikacji równorzędnej, wybierz pozycję **Utwórz zasób**  >  **równorzędny usługi**.

    ![Rejestrowanie usługi Peering Service](./media/peering-service-portal/peering-servicecreate.png)
1. Na karcie **podstawowe** na stronie **Tworzenie połączenia usługi Komunikacja równorzędna** wprowadź następujące szczegóły.

 
1. Wybierz subskrypcję i grupę zasobów skojarzoną z subskrypcją.

   ![Rejestrowanie komunikacji równorzędnej — Karta podstawowa](./media/peering-service-portal/peering-servicebasics.png)

1. Wprowadź **nazwę** , w której ma zostać zarejestrowane wystąpienie usługi komunikacji równorzędnej.
 
1. Teraz wybierz przycisk **Dalej: Konfiguracja** w dolnej części strony. Zostanie wyświetlona strona **Konfiguracja** .

## <a name="configure-the-peering-service-connection"></a>Konfigurowanie połączenia usługi Komunikacja równorzędna

1. Na stronie **Konfiguracja** wybierz lokalizację, w której należy włączyć usługę komunikacji równorzędnej, wybierając ją z listy rozwijanej **Lokalizacja usługi komunikacji równorzędnej** .

1. Wybierz dostawcę usług, z którego należy uzyskać usługę komunikacji równorzędnej, wybierając nazwę dostawcy z listy rozwijanej **Dostawca usługi komunikacji równorzędnej** .
 
1. Wybierz pozycję **Utwórz nowy prefiks** u dołu sekcji **prefiksy** , a następnie Wyświetl pola tekstowe. Teraz wprowadź nazwę zasobu prefiksu i prefiksy, które są skojarzone z dostawcą usług.

1. Wybierz pozycję **klucz prefiksu** i Dodaj klucz prefiksu, który został udzielony przez dostawcę (ISP lub IXP). Ten klucz umożliwia firmie Microsoft sprawdzenie poprawności prefiksu i dostawcy, którym przydzielono prefiks IP.
   > ![Zrzut ekranu przedstawia kartę Konfiguracja strony Tworzenie połączenia z usługą komunikacji równorzędnej, w której można wprowadzić klucz prefiksu.](./media/peering-service-portal/peering-serviceconfiguration.png)

1. Wybierz przycisk **Recenzja + Utwórz** w lewym dolnym rogu strony. Zostanie wyświetlona strona **Recenzja + tworzenie** , a platforma Azure weryfikuje konfigurację.
    

1. Gdy zostanie wyświetlony komunikat **zweryfikowano** , jak pokazano, wybierz pozycję **Utwórz**.

   > ![Zrzut ekranu przedstawia kartę Przegląd + tworzenie na stronie Tworzenie połączenia z usługą komunikacji równorzędnej.](./media/peering-service-portal/peering-service-prefix.png)


1. Po zarejestrowaniu połączenia z usługą komunikacji równorzędnej zostanie przeprowadzone dodatkowe sprawdzanie poprawności zawartych prefiksów. Stan sprawdzania poprawności można sprawdzić w sekcji **prefiksy** nazwy zasobu. Jeśli sprawdzanie poprawności nie powiedzie się, zostanie wyświetlony jeden z następujących komunikatów o błędach:

   - Nieprawidłowy prefiks usługi komunikacji równorzędnej, prefiks powinien być prawidłowym formatem, obsługiwane są tylko prefiksy IPv4.
   - Nie odebrano prefiksu od dostawcy usługi komunikacji równorzędnej.
   - Anons dotyczący prefiksu nie ma prawidłowej społeczności protokołu BGP, kontaktuje się z dostawcą usług komunikacji równorzędnej.
   - Nie znaleziono trasy kopii zapasowej, skontaktuj się z dostawcą usług komunikacji równorzędnej.
   - Odebrano prefiks z więcej niż ścieżką, skontaktuj się z dostawcą usług komunikacji równorzędnej.
   - Prefiks został odebrany jako prywatny jako ścieżka, skontaktuj się z dostawcą usług komunikacji równorzędnej.

### <a name="add-or-remove-a-prefix"></a>Dodawanie lub usuwanie prefiksu

Wybierz pozycję **Dodaj prefiksy** na stronie **prefiksy** , aby dodać prefiksy.

Wybierz wielokropek (...) obok wymienionego prefiksu, a następnie wybierz opcję **Usuń** .

### <a name="delete-a-peering-service-connection"></a>Usuwanie połączenia usługi Komunikacja równorzędna

Na stronie **wszystkie zasoby** zaznacz pole wyboru w usłudze Komunikacja równorzędna i wybierz opcję **Usuń** w górnej części strony.

> [!NOTE]
> Nie można zmodyfikować istniejącego prefiksu.
>

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o połączeniu z usługą komunikacji równorzędnej, zobacz [nawiązywanie połączenia z usługą komunikacji równorzędnej](connection.md).
- Aby dowiedzieć się więcej o telemetrii połączenia z usługą komunikacji równorzędnej, zobacz [Komunikacja równorzędna połączenia z usługą](connection-telemetry.md).
- Aby mierzyć dane telemetryczne, zobacz [mierzenie danych telemetrycznych połączenia](measure-connection-telemetry.md).
- Aby zarejestrować połączenie przy użyciu Azure PowerShell, zobacz [Rejestrowanie połączenia usługi Komunikacja równorzędna — Azure PowerShell](powershell.md).
- Aby zarejestrować połączenie przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz temat [Rejestrowanie połączenia z usługą komunikacji równorzędnej — interfejs wiersza polecenia platformy Azure](cli.md).
