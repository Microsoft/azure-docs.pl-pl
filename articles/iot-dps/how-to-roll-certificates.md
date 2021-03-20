---
title: Wycofaj certyfikaty X. 509 na platformie Azure IoT Hub Device Provisioning Service
description: Jak wycofać certyfikaty X. 509 za pomocą wystąpienia usługi Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: bf8b1e04e11dee4e636826430838a467fe034e3f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94951132"
---
# <a name="how-to-roll-x509-device-certificates"></a>Jak wycofać certyfikaty urządzeń X. 509

W trakcie cyklu życia rozwiązania IoT konieczne będzie wycofanie certyfikatów. Dwa główne przyczyny certyfikatów kroczących byłyby naruszenia zabezpieczeń i wygaśnięcia certyfikatów. 

Certyfikaty kroczące to najlepsze rozwiązanie w zakresie zabezpieczeń, które pomaga w zabezpieczeniu systemu w przypadku naruszenia. W ramach zamierzonej [metodologii](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf), firma Microsoft zaleca, aby procesy dotyczące ponownego aktywnej ochrony były stosowane wraz ze środkami zapobiegawczymi. Należy uwzględnić w tych procesach zabezpieczeń następujące urządzenia. Częstotliwość wycofywania certyfikatów zależy od potrzeb związanych z bezpieczeństwem rozwiązania. Klienci z rozwiązaniami obejmującymi wysoce poufne dane mogą codziennie wycofać certyfikat, podczas gdy inne przydzielą swoje certyfikaty co dwa lata.

Wycofywane certyfikaty urządzeń będą wymagały zaktualizowania certyfikatu przechowywanego na urządzeniu i centrum IoT. Następnie urządzenie może ponownie zainicjować obsługę administracyjną w usłudze IoT Hub przy użyciu funkcji normalnej [aprowizacji](about-iot-dps.md#provisioning-process) w usłudze Device Provisioning Service (DPS).


## <a name="obtain-new-certificates"></a>Uzyskaj nowe certyfikaty

Istnieje wiele sposobów uzyskiwania nowych certyfikatów dla urządzeń IoT. Obejmują one Uzyskiwanie certyfikatów z fabryki urządzeń, generowanie własnych certyfikatów oraz zarządzanie tworzeniem certyfikatów przez inną firmę. 

Certyfikaty są podpisane przez siebie, aby utworzyć łańcuch zaufania z certyfikatu głównego urzędu certyfikacji do [certyfikatu liścia](concepts-x509-attestation.md#end-entity-leaf-certificate). Certyfikat podpisywania to certyfikat używany do podpisywania certyfikatu liścia na końcu łańcucha zaufania. Certyfikat podpisywania może być certyfikatem głównego urzędu certyfikacji lub certyfikatem pośrednim w łańcuchu zaufania. Aby uzyskać więcej informacji, zobacz [certyfikaty X. 509](concepts-x509-attestation.md#x509-certificates).
 
Istnieją dwa różne sposoby uzyskiwania certyfikatu podpisywania. Pierwszy sposób, który jest zalecany w przypadku systemów produkcyjnych, polega na zakupie certyfikatu podpisywania od głównego urzędu certyfikacji (CA). Dzięki temu łańcuch zabezpieczeń jest podłączany do zaufanego źródła. 

Drugi sposób polega na utworzeniu własnych certyfikatów X. 509 przy użyciu narzędzia, takiego jak OpenSSL. Takie podejście jest doskonałe do testowania certyfikatów X. 509, ale zapewnia pewne gwarancje związane z bezpieczeństwem. Zalecamy używanie tego podejścia wyłącznie do testowania, chyba że przygotowano do działania jako własny dostawca urzędu certyfikacji.
 

## <a name="roll-the-certificate-on-the-device"></a>Wycofaj certyfikat na urządzeniu

Certyfikaty na urządzeniu powinny być zawsze przechowywane w bezpiecznym miejscu, na przykład [sprzętowego modułu zabezpieczeń (HSM)](concepts-service.md#hardware-security-module). Sposób rzutowania certyfikatów urządzeń będzie zależeć od tego, jak zostały utworzone i zainstalowane na urządzeniach w pierwszej kolejności. 

Jeśli masz certyfikaty od innych firm, musisz przyjrzeć się sposobom ich wycofywania certyfikatów. Ten proces może być uwzględniony w rozmieszczeniu lub może być osobnym oferowaną przez niego usługą. 

Jeśli zarządzasz własnymi certyfikatami urządzeń, musisz utworzyć własny potok do aktualizowania certyfikatów. Upewnij się, że zarówno stare, jak i nowe certyfikaty liścia mają tę samą nazwę pospolitą (CN). Mając tę samą wartość CN, urządzenie może ponownie inicjować udostępnianie bez tworzenia duplikatu rekordu rejestracji. 


## <a name="roll-the-certificate-in-the-iot-hub"></a>Wycofaj certyfikat w centrum IoT Hub

Certyfikat urządzenia można dodać ręcznie do centrum IoT Hub. Certyfikat może być również zautomatyzowany przy użyciu wystąpienia usługi Device Provisioning. W tym artykule przyjęto założenie, że wystąpienie usługi Device Provisioning Service jest używane do obsługi autoaprowizacji.

Gdy urządzenie jest początkowo obsługiwane przez funkcję samoobsługowego inicjowania obsługi administracyjnej, wykonuje jego rozruch i kontaktuje się z usługą aprowizacji. Usługa aprowizacji odpowiada przed utworzeniem tożsamości urządzenia w usłudze IoT Hub przy użyciu certyfikatu liścia urządzenia jako poświadczenia. Usługa aprowizacji informuje następnie urządzenie, do którego jest przypisany Centrum IoT Hub, a następnie urządzenie używa certyfikatu liścia do uwierzytelniania i nawiązywania połączenia z usługą IoT Hub. 

Po przeprowadzeniu nowego certyfikatu liścia do urządzenia nie może on już nawiązać połączenia z usługą IoT Hub, ponieważ używa nowego certyfikatu do nawiązania połączenia. Centrum IoT rozpoznaje tylko urządzenie ze starym certyfikatem. Wynik próby połączenia urządzenia będzie nieautoryzowany. Aby rozwiązać ten problem, należy zaktualizować wpis rejestracji dla urządzenia w celu uwzględnienia nowego certyfikatu liścia urządzenia. Następnie usługa aprowizacji może zaktualizować informacje rejestru urządzenia IoT Hub w miarę potrzeb, gdy urządzenie zostanie ponownie zainicjowane. 

Jednym z możliwych wyjątków dla tego błędu połączenia jest scenariusz, w którym utworzono [grupę rejestracji](concepts-service.md#enrollment-group) dla urządzenia w usłudze aprowizacji. W takim przypadku, jeśli nie powrócisz certyfikatów głównych lub pośrednich w łańcuchu certyfikatów urządzenia zaufania, urządzenie zostanie rozpoznane, jeśli nowy certyfikat jest częścią łańcucha zaufania zdefiniowanego w grupie rejestracyjnej. Jeśli ten scenariusz występuje jako reakcja na naruszenie zabezpieczeń, należy co najmniej nie zezwalać określonym certyfikatom urządzenia w grupie, które są uważane za naruszone. Aby uzyskać więcej informacji, zobacz nie [Zezwalaj na określone urządzenia w grupie rejestracji](./how-to-revoke-device-access-portal.md#disallow-specific-devices-in-an-enrollment-group).

Aktualizowanie wpisów rejestracji dla wycofanych certyfikatów jest realizowane na stronie **Zarządzanie rejestracjami** . Aby uzyskać dostęp do tej strony, wykonaj następujące kroki:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do wystąpienia IoT Hub Device Provisioning Service, które ma wpis rejestracji dla Twojego urządzenia.

2. Kliknij pozycję **Zarządzanie rejestracjami**.

    ![Zarządzanie rejestracjami](./media/how-to-roll-certificates/manage-enrollments-portal.png)


Sposób obsługi aktualizacji wpisu rejestracji zależy od tego, czy są używane indywidualne rejestracje, czy też rejestracje grup. Ponadto zalecane procedury różnią się w zależności od tego, czy certyfikaty są wycofywane z powodu naruszenia zabezpieczeń, czy wygaśnięcia certyfikatu. W poniższych sekcjach opisano, jak obsługiwać te aktualizacje.


## <a name="individual-enrollments-and-security-breaches"></a>Indywidualne rejestracje i naruszenia zabezpieczeń

W przypadku wycofywania certyfikatów w odpowiedzi na naruszenie zabezpieczeń należy zastosować następujące podejście, które powoduje natychmiastowe usunięcie bieżącego certyfikatu:

1. Kliknij pozycję **rejestracje indywidualne** i kliknij wpis Identyfikator rejestracji na liście. 

2. Kliknij przycisk **Usuń bieżące certyfikaty** , a następnie kliknij ikonę folderu, aby wybrać nowy certyfikat do przekazania dla wpisu rejestracji. Po zakończeniu kliknij przycisk **Zapisz** .

    Te kroki należy wykonać dla certyfikatu podstawowego i pomocniczego, jeśli oba zabezpieczenia zostały naruszone.

    ![Zarządzanie rejestracjami indywidualnymi z naruszeniem zabezpieczeń](./media/how-to-roll-certificates/manage-individual-enrollments-portal.png)

3. Po usunięciu złamanego certyfikatu z usługi aprowizacji można nadal używać certyfikatu do nawiązywania połączeń urządzeń z Centrum IoT Hub, o ile istnieje tam jego Rejestracja. Można rozwiązać te dwa sposoby: 

    Pierwszym sposobem jest ręczne przechodzenie do centrum IoT Hub i natychmiastowe usunięcie rejestracji urządzenia skojarzonej z naruszonym certyfikatem. Po ponownym zarejestrowaniu urządzenia z zaktualizowanym certyfikatem zostanie utworzona nowa Rejestracja urządzenia.     

    ![Usuwanie rejestracji urządzeń w usłudze IoT Hub](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    Druga metoda umożliwia ponowne Inicjowanie obsługi administracyjnej urządzenia w tym samym centrum IoT Hub. Takie podejście może służyć do zastępowania certyfikatu rejestracji urządzenia w usłudze IoT Hub. Aby uzyskać więcej informacji, zobacz [jak ponownie zainicjować obsługę urządzeń](how-to-reprovision.md).

## <a name="individual-enrollments-and-certificate-expiration"></a>Rejestracje indywidualne i wygaśnięcie certyfikatów

Jeśli aktualizujesz certyfikaty, aby obsługiwać wygaśnięcia certyfikatów, należy użyć dodatkowej konfiguracji certyfikatu w następujący sposób, aby ograniczyć przestoje dla urządzeń, które próbują zainicjować obsługę administracyjną.

Później, gdy certyfikat pomocniczy zbliża się również do wygaśnięcia i musi zostać przeniesiona, można obrócić do korzystania z konfiguracji podstawowej. Obracanie między głównymi i dodatkowymi certyfikatami w ten sposób zmniejsza czas przestoju w przypadku urządzeń, które próbują zainicjować obsługę administracyjną.


1. Kliknij pozycję **rejestracje indywidualne** i kliknij wpis Identyfikator rejestracji na liście. 

2. Kliknij pozycję **certyfikat pomocniczy** , a następnie kliknij ikonę folderu, aby wybrać nowy certyfikat do przekazania dla wpisu rejestracji. Kliknij pozycję **Zapisz**.

    ![Zarządzanie rejestracjami indywidualnymi przy użyciu dodatkowego wygaśnięcia certyfikatu](./media/how-to-roll-certificates/manage-individual-enrollments-secondary-portal.png)

3. Później, po wygaśnięciu certyfikatu podstawowego, wróć i Usuń ten certyfikat podstawowy, klikając przycisk **Usuń bieżący certyfikat** .

## <a name="enrollment-groups-and-security-breaches"></a>Grupy rejestracji i naruszenia zabezpieczeń

Aby zaktualizować rejestrację grupy w odpowiedzi na naruszenie zabezpieczeń, należy zastosować jedną z następujących metod, która spowoduje natychmiastowe usunięcie bieżącego głównego urzędu certyfikacji lub certyfikatu pośredniego.

#### <a name="update-compromised-root-ca-certificates"></a>Aktualizuj naruszone certyfikaty głównego urzędu certyfikacji

1. Kliknij kartę **Certyfikaty** dla wystąpienia usługi Device Provisioning.

2. Kliknij na liście certyfikat z naruszeniem zabezpieczeń, a następnie kliknij przycisk **Usuń** . Potwierdź usunięcie, wprowadzając nazwę certyfikatu, a następnie kliknij przycisk **OK**. Powtórz ten proces dla wszystkich certyfikatów z naruszeniem zabezpieczeń.

    ![Usuń certyfikat głównego urzędu certyfikacji](./media/how-to-roll-certificates/delete-root-cert.png)

3. Wykonaj kroki opisane w sekcji [Konfigurowanie zweryfikowanych certyfikatów urzędu certyfikacji](how-to-verify-certificates.md) , aby dodać i zweryfikować nowe certyfikaty głównego urzędu certyfikacji.

4. Kliknij kartę **Zarządzanie rejestracjami** dla wystąpienia usługi Device Provisioning Service, a następnie kliknij listę **grupy rejestracji** . Kliknij nazwę grupy rejestracyjnej na liście.

5. Kliknij pozycję **certyfikat urzędu certyfikacji**, a następnie wybierz nowy certyfikat głównego urzędu certyfikacji. Następnie kliknij przycisk **Zapisz**. 

    ![Wybierz nowy certyfikat głównego urzędu certyfikacji dla certyfikatu z naruszeniem zabezpieczeń](./media/how-to-roll-certificates/select-new-root-cert.png)

6. Po usunięciu złamanego certyfikatu z usługi aprowizacji można nadal używać certyfikatu do nawiązywania połączeń urządzeń z Centrum IoT Hub, o ile istnieją tam zarejestrowane urządzenia. Można rozwiązać te dwa sposoby: 

    Pierwszym sposobem jest ręczne przechodzenie do centrum IoT Hub i natychmiastowe usunięcie rejestracji urządzenia skojarzonej z naruszonym certyfikatem. Po ponownym zainicjowaniu urządzeń z zaktualizowanymi certyfikatami dla każdej z nich zostanie utworzona nowa Rejestracja urządzenia.     

    ![Usuwanie rejestracji urządzeń w usłudze IoT Hub](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    Druga metoda umożliwia ponowne Inicjowanie obsługi administracyjnej urządzeń w tym samym centrum IoT Hub. Takie podejście może służyć do zastępowania certyfikatów dla rejestracji urządzeń w usłudze IoT Hub. Aby uzyskać więcej informacji, zobacz [jak ponownie zainicjować obsługę urządzeń](how-to-reprovision.md).



#### <a name="update-compromised-intermediate-certificates"></a>Aktualizowanie naruszonych certyfikatów pośrednich

1. Kliknij pozycję **grupy rejestracji**, a następnie kliknij nazwę grupy na liście. 

2. Kliknij pozycję **certyfikat pośredni** i **Usuń bieżący certyfikat**. Kliknij ikonę folderu, aby przejść do nowego certyfikatu pośredniego, który ma zostać przekazany do grupy rejestracji. Po zakończeniu kliknij przycisk **Zapisz** . Te kroki należy wykonać zarówno dla certyfikatu podstawowego, jak i pomocniczego, jeśli oba zabezpieczenia zostały naruszone.

    Ten nowy certyfikat pośredni powinien być podpisany przez zweryfikowany certyfikat głównego urzędu certyfikacji, który został już dodany do usługi aprowizacji. Aby uzyskać więcej informacji, zobacz [certyfikaty X. 509](concepts-x509-attestation.md#x509-certificates).

    ![Zarządzanie rejestracjami indywidualnymi dla pośredniego naruszenia zabezpieczeń](./media/how-to-roll-certificates/enrollment-group-delete-intermediate-cert.png)


3. Po usunięciu złamanego certyfikatu z usługi aprowizacji można nadal używać certyfikatu do nawiązywania połączeń urządzeń z Centrum IoT Hub, o ile istnieją tam zarejestrowane urządzenia. Można rozwiązać te dwa sposoby: 

    Pierwszym sposobem jest ręczne przechodzenie do centrum IoT Hub i natychmiastowe usunięcie rejestracji urządzenia skojarzonej z naruszonym certyfikatem. Po ponownym zainicjowaniu urządzeń z zaktualizowanymi certyfikatami dla każdej z nich zostanie utworzona nowa Rejestracja urządzenia.     

    ![Usuwanie rejestracji urządzeń w usłudze IoT Hub](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    Druga metoda umożliwia ponowne Inicjowanie obsługi administracyjnej urządzeń w tym samym centrum IoT Hub. Takie podejście może służyć do zastępowania certyfikatów dla rejestracji urządzeń w usłudze IoT Hub. Aby uzyskać więcej informacji, zobacz [jak ponownie zainicjować obsługę urządzeń](how-to-reprovision.md).


## <a name="enrollment-groups-and-certificate-expiration"></a>Grupy rejestracji i wygaśnięcie certyfikatu

W przypadku wycofywania certyfikatów do obsługi wygaśnięć certyfikatów należy użyć dodatkowej konfiguracji certyfikatu w następujący sposób, aby zapewnić Brak przestoju dla urządzeń, które próbują zainicjować obsługę administracyjną.

Później, gdy certyfikat pomocniczy zbliża się również do wygaśnięcia i musi zostać przeniesiona, można obrócić do korzystania z konfiguracji podstawowej. Obracanie między głównymi i dodatkowymi certyfikatami w ten sposób nie zapewnia przestoju dla urządzeń, które próbują zainicjować obsługę administracyjną. 

#### <a name="update-expiring-root-ca-certificates"></a>Aktualizowanie certyfikatów głównego urzędu certyfikacji

1. Wykonaj kroki opisane w sekcji [Konfigurowanie zweryfikowanych certyfikatów urzędu certyfikacji](how-to-verify-certificates.md) , aby dodać i zweryfikować nowe certyfikaty głównego urzędu certyfikacji.

2. Kliknij kartę **Zarządzanie rejestracjami** dla wystąpienia usługi Device Provisioning Service, a następnie kliknij listę **grupy rejestracji** . Kliknij nazwę grupy rejestracyjnej na liście.

3. Kliknij pozycję **certyfikat urzędu certyfikacji** i wybierz nowy certyfikat głównego urzędu certyfikacji w ramach konfiguracji **certyfikatu pomocniczego** . Następnie kliknij przycisk **Zapisz**. 

    ![Wybierz nowy certyfikat głównego urzędu certyfikacji do wygaśnięcia](./media/how-to-roll-certificates/select-new-root-secondary-cert.png)

4. Później po wygaśnięciu certyfikatu podstawowego kliknij kartę **Certyfikaty** dla wystąpienia usługi Device Provisioning. Kliknij na liście certyfikat wygasły, a następnie kliknij przycisk **Usuń** . Potwierdź usunięcie, wprowadzając nazwę certyfikatu, a następnie kliknij przycisk **OK**.

    ![Usuń certyfikat głównego urzędu certyfikacji](./media/how-to-roll-certificates/delete-root-cert.png)



#### <a name="update-expiring-intermediate-certificates"></a>Aktualizacja certyfikatów pośrednich


1. Kliknij pozycję **grupy rejestracji**, a następnie kliknij nazwę grupy na liście. 

2. Kliknij pozycję **certyfikat pomocniczy** , a następnie kliknij ikonę folderu, aby wybrać nowy certyfikat do przekazania dla wpisu rejestracji. Kliknij pozycję **Zapisz**.

    Ten nowy certyfikat pośredni powinien być podpisany przez zweryfikowany certyfikat głównego urzędu certyfikacji, który został już dodany do usługi aprowizacji. Aby uzyskać więcej informacji, zobacz [certyfikaty X. 509](concepts-x509-attestation.md#x509-certificates).

   ![Zarządzanie grupami rejestracji przy użyciu certyfikatu pomocniczego](./media/how-to-roll-certificates/manage-enrollment-group-secondary-portal.png)

3. Później, po wygaśnięciu certyfikatu podstawowego, wróć i Usuń ten certyfikat podstawowy, klikając przycisk **Usuń bieżący certyfikat** .


## <a name="reprovision-the-device"></a>Ponowne Inicjowanie obsługi urządzenia

Po przeprowadzeniu tego certyfikatu zarówno na urządzeniu, jak i w usłudze Device Provisioning urządzenie może zostać ponownie zainicjowane przez skontaktowanie się z usługą Device Provisioning. 

Jednym z łatwych w obsłudze urządzeń programistycznych jest zaprogramowanie urządzenia w celu skontaktowania się z usługą aprowizacji w celu przechodzenia przez proces aprowizacji w przypadku, gdy urządzenie otrzyma komunikat o błędzie "nieautoryzowane" podczas próby nawiązania połączenia z Centrum IoT Hub.

Innym sposobem, aby stare i nowe certyfikaty były prawidłowe dla krótkiego nakładania się, i użyć Centrum IoT do wysłania polecenia do urządzeń w celu ponownego zarejestrowania się za pośrednictwem usługi aprowizacji w celu zaktualizowania IoT Hub informacji o połączeniu. Ponieważ poszczególne urządzenia mogą przetwarzać polecenia inaczej, należy zaprogramować urządzenie, aby dowiedzieć się, co należy zrobić, gdy polecenie jest wywoływane. Istnieje kilka sposobów, aby można było wykonać polecenie na urządzeniu za pośrednictwem IoT Hub i zalecamy użycie [bezpośrednich metod](../iot-hub/iot-hub-devguide-direct-methods.md) lub [zadań](../iot-hub/iot-hub-devguide-jobs.md) do zainicjowania procesu.

Po zakończeniu ponownej aprowizacji urządzenia będą mogły nawiązywać połączenia z IoT Hub przy użyciu nowych certyfikatów.


## <a name="disallow-certificates"></a>Nie Zezwalaj na certyfikaty

W odpowiedzi na naruszenie zabezpieczeń może być konieczne niezezwalanie na certyfikat urządzenia. Aby nie zezwalać na certyfikat urządzenia, wyłącz wpis rejestracji dla urządzenia docelowego/certyfikatu. Aby uzyskać więcej informacji, zobacz temat niezezwalanie na urządzenia w artykule [Zarządzanie odrejestrowaniem](how-to-revoke-device-access-portal.md) .

Po dołączeniu certyfikatu jako części wyłączonego wpisu rejestracji wszystkie próby zarejestrowania się w usłudze IoT Hub przy użyciu tych certyfikatów będą kończyć się niepowodzeniem nawet wtedy, gdy zostanie on włączony w ramach innego wpisu rejestracji.
 



## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o certyfikatach X. 509 w usłudze Device Provisioning, zobacz [zaświadczanie o certyfikatach x. 509](concepts-x509-attestation.md) 
- Aby dowiedzieć się, jak zrobić dowód posiadania dla certyfikatów urzędu certyfikacji X. 509 za pomocą usługi Azure IoT Hub Device Provisioning Service, zobacz [jak weryfikować certyfikaty](how-to-verify-certificates.md) .
- Aby dowiedzieć się, jak utworzyć grupę rejestracji przy użyciu portalu, zobacz [Zarządzanie rejestracjami urządzeń za pomocą Azure Portal](how-to-manage-enrollments.md).