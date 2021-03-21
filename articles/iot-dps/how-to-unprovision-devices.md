---
title: Cofanie aprowizacji urządzeń, które zostały udostępnione za pomocą usługi Azure IoT Hub Device Provisioning Service
description: Jak anulować obsługę administracyjną urządzeń, które zostały udostępnione za pomocą usługi Azure IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 05/11/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 7c91cd8cbb1a927a88f05fcea603c6a4ed6e1c14
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101719036"
---
# <a name="how-to-deprovision-devices-that-were-previously-auto-provisioned"></a>Jak anulować obsługę administracyjną urządzeń, które były wcześniej inicjowane samoobsługowo 

Może się okazać, że konieczne jest cofnięcie aprowizacji urządzeń, które wcześniej były obsługiwane przez usługę Device Provisioning. Na przykład urządzenie może być sprzedawane lub przenoszone do innego Centrum IoT lub mogło zostać utracone, skradzione lub naruszone w inny sposób. 

Ogólnie rzecz biorąc, cofanie aprowizacji urządzenia obejmuje dwie czynności:

1. Wyrejestruj urządzenie z usługi aprowizacji, aby zapobiec przyszłej aprowizacji. W zależności od tego, czy chcesz odwołać dostęp tymczasowo czy trwale, możesz wyłączyć lub usunąć wpis rejestracji. W przypadku urządzeń korzystających z zaświadczania X. 509 warto wyłączyć/usunąć wpis w hierarchii istniejących grup rejestracji.  
 
   - Aby dowiedzieć się, jak wyrejestrować urządzenie, zobacz [Jak wyrejestrować urządzenie z usługi Azure IoT Hub Device Provisioning Service](how-to-revoke-device-access-portal.md).
   - Aby dowiedzieć się, jak wyrejestrować urządzenie programowo przy użyciu jednego z zestawów SDK usługi aprowizacji, zobacz [Zarządzanie rejestracjami urządzeń za pomocą zestawów SDK usług](./quick-enroll-device-x509-java.md).

2. Wyrejestruj urządzenie z IoT Hub, aby uniemożliwić komunikację w przyszłości i transfer danych. Ponownie można tymczasowo wyłączyć lub trwale usunąć wpis urządzenia w rejestrze tożsamości dla IoT Hub, w którym Zainicjowano obsługę administracyjną. Aby dowiedzieć się więcej o wyłączaniu, zobacz temat [wyłączanie urządzeń](../iot-hub/iot-hub-devguide-identity-registry.md#disable-devices) . Zapoznaj się z tematem "Zarządzanie urządzeniami/urządzenia IoT" dla zasobu IoT Hub w [Azure Portal](https://portal.azure.com).

Dokładne kroki wykonywane w celu anulowania aprowizacji urządzenia zależą od jego mechanizmu zaświadczania i odpowiedniego wpisu rejestracji w usłudze aprowizacji. Poniższe sekcje zawierają omówienie procesu na podstawie typu rejestracji i zaświadczania.

## <a name="individual-enrollments"></a>Rejestracje indywidualne
Urządzenia korzystające z zaświadczania modułu TPM lub zaświadczania X. 509 z certyfikatem liścia są inicjowane za pomocą indywidualnego wpisu rejestracji. 

Aby anulować obsługę administracyjną urządzenia, które ma rejestrację indywidualną: 

1. Wyrejestruj urządzenie z usługi aprowizacji:

   - W przypadku urządzeń korzystających z zaświadczania modułu TPM Usuń wpis rejestracji indywidualnej, aby trwale odwołać dostęp urządzenia do usługi aprowizacji, lub Wyłącz ten wpis, aby tymczasowo odwołać dostęp do niego. 
   - W przypadku urządzeń korzystających z zaświadczania X. 509 można usunąć lub wyłączyć tę pozycję. Należy pamiętać, że jeśli usuniesz rejestrację indywidualną dla urządzenia używającego X. 509, a dla certyfikatu podpisywania w łańcuchu certyfikatów tego urządzenia istnieje już włączona Grupa rejestracji, urządzenie może je ponownie zarejestrować. W przypadku takich urządzeń może być bezpieczniejsze wyłączenie wpisu rejestracji. Uniemożliwi to ponowne zarejestrowanie urządzenia, niezależnie od tego, czy dla jednego z jego certyfikatów podpisywania istnieje włączona Grupa rejestracji.

2. Wyłączenie lub usunięcie urządzenia w rejestrze tożsamości Centrum IoT Hub, do którego została zainicjowana obsługa administracyjna. 


## <a name="enrollment-groups"></a>Grupy rejestracji
Za pomocą zaświadczania X. 509 urządzenia mogą być również obsługiwane za pomocą grupy rejestracji. Grupy rejestracji są konfigurowane przy użyciu certyfikatu podpisywania, certyfikatu pośredniego lub głównego urzędu certyfikacji i kontroli dostępu do usługi aprowizacji dla urządzeń z tym certyfikatem w łańcuchu certyfikatów. Aby dowiedzieć się więcej o grupach rejestracji i certyfikatach X. 509 z usługą aprowizacji, zobacz [zaświadczenie certyfikatu x. 509](concepts-x509-attestation.md). 

Aby wyświetlić listę urządzeń, które zostały zainicjowane za pomocą grupy rejestracji, można wyświetlić szczegóły grupy rejestracji. Jest to prosty sposób na zrozumienie, które Centrum IoT Hub zostało zainicjowane dla każdego urządzenia. Aby wyświetlić listę urządzeń: 

1. Zaloguj się do Azure Portal i kliknij pozycję **wszystkie zasoby** w menu po lewej stronie.
2. Kliknij usługę aprowizacji na liście zasobów.
3. W usłudze aprowizacji kliknij pozycję **Zarządzaj rejestracjami**, a następnie wybierz kartę **grupy rejestracji** .
4. Kliknij grupę rejestracji, aby ją otworzyć.

   ![Wyświetlanie wpisu grupy rejestracji w portalu](./media/how-to-unprovision-devices/view-enrollment-group.png)

W przypadku grup rejestracji istnieją dwa scenariusze, które należy wziąć pod uwagę:

- Aby anulować obsługę administracyjną wszystkich urządzeń, które zostały zainicjowane za pomocą grupy rejestracji:
  1. Wyłącz grupę rejestracji, aby nie zezwalać na certyfikat podpisywania. 
  2. Aby wyłączyć lub usunąć każde urządzenie z rejestru tożsamości odpowiedniego centrum IoT Hub, należy użyć listy urządzeń z zainicjowaną obsługą tej grupy rejestracji. 
  3. Po wyłączeniu lub usunięciu wszystkich urządzeń z odpowiednich centrów IoT można opcjonalnie usunąć grupę rejestracji. Należy jednak pamiętać, że w przypadku usunięcia grupy rejestracji, gdy istnieje grupa rejestracji z włączoną obsługą certyfikatu podpisywania w łańcuchu certyfikatów co najmniej jednego z tych urządzeń, te urządzenia mogą być ponownie rejestrowane. 

- Aby anulować obsługę administracyjną pojedynczego urządzenia z grupy rejestracji:
  1. Utwórz wyłączoną rejestrację indywidualną dla swojego certyfikatu liścia (urządzenia). Spowoduje to odwołanie dostępu do usługi aprowizacji dla tego urządzenia, pozostawiając jednocześnie dostęp do innych urządzeń, które mają certyfikat podpisywania grupy rejestracji w łańcuchu. Nie usuwaj wyłączonej rejestracji indywidualnej dla urządzenia. Pozwoli to na ponowne zarejestrowanie urządzenia za pomocą grupy rejestracji. 
  2. Aby znaleźć centrum IoT, do którego zainicjowano urządzenie i wyłączyć lub usunąć je z rejestru tożsamości centrum, należy użyć listy urządzeń, które zostały zainicjowane dla tej grupy rejestracji.