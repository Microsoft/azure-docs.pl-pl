---
title: Wyrejestruj urządzenie z usługi Azure IoT Hub Device Provisioning Service
description: Jak wyrejestrować urządzenie, aby zapobiec aprowizacji za pomocą usługi Azure IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: c75fcd1fd20e41df5018fcaa07fe83051d7e5f1a
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/23/2020
ms.locfileid: "97740372"
---
# <a name="how-to-disenroll-a-device-from-azure-iot-hub-device-provisioning-service"></a>Jak wyrejestrować urządzenie z usługi Azure IoT Hub Device Provisioning Service

Prawidłowe zarządzanie poświadczeniami urządzeń jest kluczowe w przypadku systemów z wysokim profilem, takich jak rozwiązania IoT. Najlepszym rozwiązaniem dla takich systemów jest jasne zaplanowanie sposobu odwołania dostępu do urządzeń w przypadku ich poświadczeń, niezależnie od tego, czy token sygnatur dostępu współdzielonego (SAS) czy certyfikat X. 509 może zostać naruszony. 

Rejestracja w usłudze Device Provisioning umożliwia [zainicjowanie obsługi](about-iot-dps.md#provisioning-process)urządzenia. Zainicjowane urządzenie jest takie, które zostało zarejestrowane w IoT Hub, co umożliwia mu otrzymanie jego początkowego stanu [dwuosiowego urządzenia](~/articles/iot-hub/iot-hub-devguide-device-twins.md) i rozpoczęcie raportowania danych telemetrycznych. W tym artykule opisano, jak wyrejestrować urządzenie z wystąpienia usługi aprowizacji, uniemożliwiając ponowne zainicjowanie obsługi administracyjnej w przyszłości.

> [!NOTE] 
> Należy pamiętać o zasadach ponawiania prób dla urządzeń, do których odwołuje się dostęp. Na przykład urządzenie, które ma nieograniczone zasady ponawiania prób, może stale próbować zarejestrować się w usłudze aprowizacji. Ta sytuacja zużywa zasoby usługi i prawdopodobnie ma wpływ na wydajność.

## <a name="disallow-devices-by-using-an-individual-enrollment-entry"></a>Nie Zezwalaj na urządzenia przy użyciu indywidualnego wpisu rejestracji

Rejestracje indywidualne dotyczą jednego urządzenia i mogą używać certyfikatów X. 509, kluczy poręczenia modułu TPM (w rzeczywistym lub wirtualnym module TPM) lub tokenów SAS jako mechanizmu zaświadczania. Aby nie zezwalać na urządzenie z rejestracją indywidualną, można wyłączyć lub usunąć jego wpis rejestracyjny. 

Aby tymczasowo nie zezwalać na urządzenie przez wyłączenie jego wpisu rejestracji: 

1. Zaloguj się do Azure Portal i wybierz pozycję **wszystkie zasoby** w menu po lewej stronie.
2. Z listy zasobów wybierz usługę aprowizacji, z której chcesz uniemożliwić urządzenie.
3. W usłudze aprowizacji wybierz pozycję **Zarządzaj rejestracjami**, a następnie wybierz kartę **rejestracje indywidualne** .
4. Wybierz wpis rejestracji dla urządzenia, którego nie chcesz zezwolić. 

    ![Wybierz swoją rejestrację indywidualną](./media/how-to-revoke-device-access-portal/select-individual-enrollment.png)

5. Na stronie Rejestracja przewiń w dół i wybierz pozycję **Wyłącz** dla przełącznika **Włącz wpis** , a następnie wybierz pozycję **Zapisz**.  

   ![Wyłącz indywidualny wpis rejestracji w portalu](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)

Aby trwale uniemożliwić urządzenie, usuwając jego wpis rejestracyjny:

1. Zaloguj się do Azure Portal i wybierz pozycję **wszystkie zasoby** w menu po lewej stronie.
2. Z listy zasobów wybierz usługę aprowizacji, z której chcesz uniemożliwić urządzenie.
3. W usłudze aprowizacji wybierz pozycję **Zarządzaj rejestracjami**, a następnie wybierz kartę **rejestracje indywidualne** .
4. Zaznacz pole wyboru obok wpisu rejestracji urządzenia, którego chcesz nie zezwolić. 
5. Wybierz pozycję **Usuń** w górnej części okna, a następnie wybierz pozycję **tak** , aby potwierdzić, że chcesz usunąć rejestrację. 

   ![Usuwanie wpisu rejestracji indywidualnej w portalu](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)


Po zakończeniu procedury należy zobaczyć, że wpis został usunięty z listy rejestracji indywidualnych.  

## <a name="disallow-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group"></a>Nie Zezwalaj na certyfikat pośredniego lub głównego urzędu certyfikacji X. 509 przy użyciu grupy rejestracji

Certyfikaty X. 509 są zwykle uporządkowane w łańcuchu certyfikatów zaufania. Jeśli certyfikat na dowolnym etapie łańcucha zostanie naruszony, zaufanie jest zerwane. Certyfikat musi być niedozwolony, aby nie zezwalać usłudze Device Provisioning na dostęp do urządzeń, które znajdują się w dowolnym łańcuchu zawierającym ten certyfikat. Aby dowiedzieć się więcej o certyfikatach X. 509 i sposobach ich użycia w usłudze aprowizacji, zobacz [certyfikaty x. 509](./concepts-x509-attestation.md#x509-certificates). 

Grupa rejestracji to wpis dla urządzeń, które korzystają ze wspólnego mechanizmu zaświadczania certyfikatów X. 509 podpisanych przez ten sam pośredni lub główny urząd certyfikacji. Wpis grupy rejestracji jest konfigurowany przy użyciu certyfikatu X. 509 skojarzonego z pośrednim lub głównym urzędem certyfikacji. Wpis jest również skonfigurowany z dowolnymi wartościami konfiguracji, takimi jak stan dwuosiowy i połączenie usługi IoT Hub, które są współużytkowane przez urządzenia z tym certyfikatem w łańcuchu certyfikatów. Aby nie zezwalać na certyfikat, można wyłączyć lub usunąć jego grupę rejestracji.

Aby tymczasowo nie zezwalać na certyfikat poprzez wyłączenie jego grupy rejestracji: 

1. Zaloguj się do Azure Portal i wybierz pozycję **wszystkie zasoby** w menu po lewej stronie.
2. Z listy zasobów wybierz usługę aprowizacji, z której chcesz nie zezwalać na certyfikat podpisywania.
3. W usłudze aprowizacji wybierz pozycję **Zarządzaj rejestracjami**, a następnie wybierz kartę **grupy rejestracji** .
4. Wybierz grupę rejestracji przy użyciu certyfikatu, którego chcesz uniemożliwić.
5. Wybierz pozycję **Wyłącz** na przełączniku **Włącz wpis** , a następnie wybierz pozycję **Zapisz**.  

   ![Wyłączanie wpisu grupy rejestracji w portalu](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
Aby trwale nie zezwalać na certyfikat przez usunięcie jego grupy rejestracji:

1. Zaloguj się do Azure Portal i wybierz pozycję **wszystkie zasoby** w menu po lewej stronie.
2. Z listy zasobów wybierz usługę aprowizacji, z której chcesz uniemożliwić urządzenie.
3. W usłudze aprowizacji wybierz pozycję **Zarządzaj rejestracjami**, a następnie wybierz kartę **grupy rejestracji** .
4. Zaznacz pole wyboru obok grupy rejestracji dla certyfikatu, którego chcesz uniemożliwić. 
5. Wybierz pozycję **Usuń** w górnej części okna, a następnie wybierz pozycję **tak** , aby potwierdzić, że chcesz usunąć grupę rejestracji. 

   ![Usuwanie wpisu grupy rejestracji w portalu](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

Po zakończeniu procedury powinien zostać wyświetlony wpis usunięty z listy grup rejestracji.  

> [!NOTE]
> W przypadku usunięcia grupy rejestracji dla certyfikatu urządzenia, które mają certyfikat w łańcuchu certyfikatów, nadal mogą być rejestrowane, jeśli istnieje włączona Grupa rejestracji dla certyfikatu głównego lub inny certyfikat pośredni wyższy w swoim łańcuchu certyfikatów.

## <a name="disallow-specific-devices-in-an-enrollment-group"></a>Nie Zezwalaj na określone urządzenia w grupie rejestracji

Urządzenia implementujące mechanizm zaświadczania X. 509 używają łańcucha certyfikatów i klucza prywatnego urządzenia na potrzeby uwierzytelniania. Gdy urządzenie łączy się i uwierzytelnia za pomocą usługi Device Provisioning, usługa najpierw szuka rejestracji indywidualnej zgodnej z poświadczeniami urządzenia. Następnie usługa przeszukuje grupy rejestracji, aby ustalić, czy urządzenie może być obsługiwane. Jeśli usługa odnajdzie wyłączoną rejestrację indywidualną dla urządzenia, uniemożliwia nawiązanie połączenia z urządzeniem. Usługa uniemożliwia połączenie nawet wtedy, gdy istnieje włączona Grupa rejestracji dla pośredniego lub głównego urzędu certyfikacji w łańcuchu certyfikatów urządzenia. 

Aby nie zezwalać na pojedyncze urządzenie w grupie rejestracji, wykonaj następujące czynności:

1. Zaloguj się do Azure Portal i wybierz pozycję **wszystkie zasoby** w menu po lewej stronie.
2. Z listy zasobów wybierz usługę aprowizacji, która zawiera grupę rejestracji dla urządzenia, którego nie chcesz zezwolić.
3. W usłudze aprowizacji wybierz pozycję **Zarządzaj rejestracjami**, a następnie wybierz kartę **rejestracje indywidualne** .
4. Na górze wybierz przycisk **Dodaj rejestrację indywidualną** . 
5. Na stronie **Dodawanie rejestracji** wybierz pozycję **X. 509** jako **mechanizm** zaświadczania dla urządzenia.

    Przekaż certyfikat urządzenia i wprowadź identyfikator urządzenia, które ma być niedozwolone. W przypadku certyfikatu Użyj podpisanego certyfikatu jednostki końcowej zainstalowanego na urządzeniu. Urządzenie używa podpisanego certyfikatu jednostki końcowej do uwierzytelniania.

    ![Ustaw właściwości urządzenia dla niedozwolonego urządzenia](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group-1.png)

6. Przewiń w dół do strony **Dodaj rejestrację** i wybierz pozycję **Wyłącz** na przełączniku **Włącz wpis** , a następnie wybierz pozycję **Zapisz**. 

    [![Użyj wyłączonego wpisu rejestracji indywidualnej w celu wyłączenia urządzenia z rejestracji grupowej w portalu](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png)](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png#lightbox)

Po pomyślnym utworzeniu rejestracji na karcie **indywidualne rejestracje** powinna zostać wyświetlona wyłączona rejestracja urządzeń. 

## <a name="next-steps"></a>Następne kroki

Wyrejestrowanie jest również częścią większego procesu anulowania obsługi administracyjnej. Anulowanie aprowizacji urządzenia obejmuje zarówno Wyrejestrowanie z usługi Provisioning, jak i Wyrejestrowanie z Centrum IoT Hub. Aby dowiedzieć się więcej na temat pełnego procesu, zobacz [Jak anulować obsługę administracyjną urządzeń, które wcześniej były obsługiwane](how-to-unprovision-devices.md) .
